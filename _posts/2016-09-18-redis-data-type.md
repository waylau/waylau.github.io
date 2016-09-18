---
layout: post
title: Redis 数据类型及抽象
date: 2016-09-18 01:41
author: admin
comments: true
categories: [Redis]
tags: [Redis]
---

Redis 不仅仅是简单的 key value 存储，实际上它还是一个 data strutures server（据数结构服务器)，用以支持不同的数值类型。在 key value 中，value 不仅仅局限于 string 类型，它可以是更复杂的数据结构：

<!-- more -->

* 二进制安全的 string；
* List：一个链表，链表中的元素按照插入顺序排列；
* Set：string 集合，集合中元素是唯一，没有排序
* Sorted set： 和 Set 类似，但是每一个 string 元素关联一个浮点数值。这个数值被称为 Score。元素总是通过他们的 Score 进行排序，所以不像 Set，它可以获取一段范围的元素（例如，获取前10个，或者后10个）。
* Hash：Hash 就是由关联值的字段构成的 Map。字段和值都是 string。这个与 Ruby 或 Python 的 hash 很类似。
* Bit array（或者简单称为 Bitmap）：像位数值一样通过特别的命令处理字符串：你可以设置和清除单独的 bit，统计所有 bit 集合中为1的数量，查找第一个设置或者没有设置的 bit 等等。
* HyperLogLogs：这是一个概率统计用的数据结构，可以被用来估计一个集合的基数。 

对于所有的例子，我们都使用 redis-cli 工具来演示。这是一个简单但是非常有用的命令行工具，可以用来发送命令给 Redis Server。


### 1. Redis key 

Redis key 是二进制安全的。这意味着你可以使用任何二进制序列作为key， 如从一个像"foo"的字符串到一个 JPEG 文件的内容。空字符串也是一个有效的 key。

关于 key 的一些其它规则：

- 不建议使用非常长的 Key。这不仅仅是考虑内存方面问题， 而且在数据集中查找 key 可能需要和多个 key 进行比较。如果当前的任务需要使用一个很大值，将它进行 hash 是一个不错的方案（例如，使用SHA1），尤其是从内存和带宽的角度考虑。
- 非常短的 key 往往也不是一个好主意。如果你可以将 key 写成“user:1000:followers”，就不要使用“u1000flw”。因为，首先前者更加具有可读性，其次增加的空间相比 key 对象本身和值对象占用的空间是很小的。当然，短 key 显然会消耗更少的内存，你需要找到一个适当的平衡。
- 努力坚持使用模式。例如像“user:1000”这样的“object-type:id”模式是一个好主意。点和连接线通常被用在多个单词的字段，例如“comment:1234:reply.to”或者“comment:1234:reply-to”。
- 允许 key 最大的大小是512MB。

### 2. Redis String

Redis String 类型是关联到 Redis key 最简单的值类型。它是Memcached 中唯一数据类型，所以对于 Redis 新手来说，使用它也是非常自然的。

因为 Redis key 是 String, 所以当我们使用 String 类型作为 value 时, 其实就是将一个 String 映射到另一个 String。String 数据类型对于大量的用例是非常有用的，如缓存 HTML 片段或者页面。

让我们一起使用 redis-cli 来操作下 String 类型：

```
> set mykey somevalue
OK
> get mykey
"somevalue"
```

使用 SET 和 GET 命令可以设置和获取 String 值。注意 SET 会替换已经存入到 key 中的任何值，即使这个 key 存在的不是 String 值。所以SET 执行一次分配。

值可以是任何类型的 String（包括二进制数据）， 例如你可以存一个jpeg 图片到一个 key 中。但值不能超过512MB大小。

SET 命令有一些有趣的选项，这些选项可以通过额外的参数来设置。例如，

* NX——只在 key 不存在的情况下执行
* XX——只在 key 存在的情况下执行


下面是操作示例：

```
> set mykey somevalue
OK
> get mykey
"somevalue"
```

String 是 Redis 的基础值，你可以对它们进行一些有意思的操作。例如，进行原子递增：

```
> set counter 100
OK
> incr counter
(integer) 101
> incr counter
(integer) 102
> incrby counter 50
(integer) 152
```

INCR 命令将 String 值解析为 Integer，然后将它递增1，最后将新值作为返回值。这里也有一些类似的命令，如 INCRBY、DECR 和 DCRBY。在内部它们是相同的命令，并且执行方式的差别非常微小。

INCR 命令是原子操作意味着什么？这表示即使多个客户端对同一个 key 发送 INCR 命令也不会导致 Race Condition（竞争条件）问题。例如， 当 client1 和 client2 同时给值加1时（旧值为10），它们不会同时读到10，最终值一定是12，因为 read-increment-set 起作用了。

操作 String 有很多的命令。例如 GETSET 命令将一个 key 设置为新值，并将旧值作为返回值。例如，你有一个系统。每次你的网站接收到新的访问者，则使用 INCR 命令递增一个 Redis key，这时你就可以使用 GETSET 命令来实现。你可能想每隔一个小时收集一次信息，并且需要不丢失每一次的递增。这样你就可以 GETSET 这个 key，将新值0赋给它，并将旧值读回。

MSET 和 MGET 命令用于在一条命令中设置或者获取多个 key 的值，这对减少网络延时是非常有用的。

下面是操作示例：

```
> mset a 10 b 20 c 30
OK
> mget a b c
1) "10"
2) "20"
3) "30"
```

当使用 MGET 时，Redis 返回的是一个值数组。


### 3. 修改和查询 key 空间

还有一些命令没有定义在具体的类型上，但在与 key 空间交互时非常有用。这些命令可以用于任何类型的 key。

例如，EXISTS 命令返回1或者0来标志一个给定的Key是否在数据库中存在。DEL 命令用来删除一个 key 和关联的值，而不管这个值是什么。

下面是操作示例：

```
> set mykey hello
OK
> exists mykey
(integer) 1
> del mykey
(integer) 1
> exists mykey
(integer) 0
```

通过这个例子，你也可以看到 DEL 命令根据 key 是否被删除返回1或者0。

这里有很多与 key 空间相关的命令，但是上面两个命令以及 TYPE 命令是非常关键的命令。TYPE 命令返回指定 key 中存放值的类型。

下面是操作示例：

```
> set mykey x
OK
> type mykey
string
> del mykey
(integer) 1
> type mykey
none
```
 

### 4. Redis 失效：具有有限生存时间的 key

Redis 失效是 Redis 的一个特性之一。这个特性可以用在任何一种值类型中。你可以给一个 key 设置一个超时时间，这个超时时间就是有限的生存时间。当这个生存时间过去，这个 key 会自动被销毁。

下面是一些关于 Redis 失效的描述：

- 在设置失效时间时，可以使用秒或者毫秒精度。
- 失效时间一般总是1ms。
- 失效信息会被复制，并持久化到磁盘中。当你的 Redis 服务器停止时(这意味着 Redis 将保存 key 的失效时间），这个时间在无形中度过。

设置失效时间是轻而易举的：

```
> set key some-value
OK
> expire key 5
(integer) 1
> get key (immediately)
"some-value"
> get key (after some time)
(nil)
```

这个 key 在两次 GET 调用之间消失了，因为第二次调用延时超过了5秒。在上面的例子中，我们使用 EXPIRE 命令来设置超时时间（它也可以用来给一个已经设置超时时间的 key 设置一个不同的值。PERSIST 可以用来删除失效时间，并将 key 永远持久化）。当然我们也可以使用其它的R edis 命令来创建带失效时间的 key。例如，使用 SET 选项：

```
> set key 100 ex 10
OK
> ttl key
(integer) 9
```

上面例子中设置 key 值为 String 100, 并带有10秒的超时时间。之后，使用 TTL 命令检测这个 key 的剩余生存时间。

如果你想知道如何以毫秒级设置和检测超时时间，查看 PEXPIRE 和 PTTL 命令，以及 SET 选项列表，可以参见 <http://redis.io/commands>。

 

### 5. Redis List

Redis List 是通过 Linked List 实现。这意味着即使成千上万的元素在一个列表中，在列表头和尾增加一个元素的操作是在一个常量时间完成。使用 LPUSH 命令增加一个新元素到一个具有10个元素的列表头的速度和增加一个元素到有千万元素的列表头是一样的。

这样做的负面影响是什么？在使用数组实现的列表中，使用 index 访问一个元素是非常快的（index 访问是常量时间），而在使用 Linked List 实现的 List 中不是那么快的（这个操作需要的工作量和被访问元素的 index 成正比）

Redis List 使用 Linked List 实现原因，因为对于数据库系统，能够快速增加一个元素到一个非常长的列表中是非常关键的。

你过一会儿将会看到，Redis List 的另一个重要优势是可以在常量时间内获取一个固定长度子 List。

如何实现当快速访问一个庞大元素集合的中间值？可以使用另一个数据结构，它称为 Sorted Set。 Sorted Set 将在后面讲到。

### 6. 使用 Redis List 的第一步

LPUSH 命令将一个新元素从左边加入到列表中，而 RPUSH 命令将一个新元素从右边加入到列表中。最后，LRANGE 命令获取列表范围内的元素：

```
> rpush mylist A
(integer) 1
> rpush mylist B
(integer) 2
> lpush mylist first
(integer) 3
> lrange mylist 0 -1
1) "first"
2) "A"
3) "B"
```

注意 LRANGE 带有两个 index， 分别是返回范围的开始和结束。这两个index 都可以是负数，告诉 Redis 从后边开始计数：-1表示最后一个元素，-2表示倒数第二个元素，以此类推。

正如你看到的, RPUSH 将元素附加到列表右边，而 LPUSH 将元素附加到左边。

两个命令都是 variadic commands（可变参数的命令）。这意味着你可以在一次调用中将多个元素插入到列表中。

下面是操作示例：

```
> rpush mylist 1 2 3 4 5 "foo bar"
(integer) 9
> lrange mylist 0 -1
1) "first"
2) "A"
3) "B"
4) "1"
5) "2"
6) "3"
7) "4"
8) "5"
9) "foo bar"
```


Redis List 的一个重要操作是 pop 元素的能力。pop 元素是指从列表中取出元素，并同时将它从列表中删除的操作。你可以从左边或者右边 pop 元素，这和你从列表的两侧 push 元素是类似的。

下面是操作示例：

```
> rpush mylist a b c
(integer) 3
> rpop mylist
"c"
> rpop mylist
"b"
> rpop mylist
"a"
```

我们加入了三个元素，并 pop 了三个元素，所以在这些命令执行完后，这个列表是空的，并且没有更多的元素可以 pop。如果我尝试再 pop 一个元素，下面就是我们获得的结果：

```
> rpop mylist
(nil)
```

Redis 返回 NULL 值来表示已经没有元素在列表中了。

 

### 7. List 常见的用例

List 对于某些特定的场景是非常有用的。两个非常典型用例如下：

- 记录用户 post 到社区网络的最新更新。
- 使用消费者-生产者模式进行进程间通信。生产者推送数据到 List 中，消费者消费这些数据并执行操作。Redis 有专门的 List 命令使这个用例更加可靠和高效。

例如，热门的 Ruby 库 [resque](https://github.com/resque/resque) 和 [sidekiq](https://github.com/mperham/sidekiq) 在底层就是使用 Redis 列表实现后台任务。

热门的 Twitter 社交网络将用户最新 post 的 tweet 放入到 Redis List 中。

为了一步步描述这个常见的用例，设想你的主页展示一个图片共享社交网站上发布的最新图片，并且你想提高访问速度。

- 每次一个用户 post 一张新的图片，我们就使用 LPUSH 将它的 ID 加入到一个 List 中。
- 当用户访问这个主页时, 我们使用 LRANGE 0 9 来获得最近上传的10个数据。

### 8. Capped list（限制列表）

在很多的用例中，我们仅需要使用 List 保存最近的元素，比如，社交网络的更新，日志，或者其他任何事。

Redis 允许我们使用 List 作为 capped 集合，使用 LTRIM 命令来仅记住最近 N 个元素，并丢失所有旧的数据。

LTRIM 命令和 LRANGE 类似，但它设置这个范围作为新的 List 值，而不是展示指定范围的元素。所有在给定范围之外的元素会被删除。

通过下面的例子，我们可以使它更加容易理解：

```
> rpush mylist 1 2 3 4 5
(integer) 5
> ltrim mylist 0 2
OK
> lrange mylist 0 -1
1) "1"
2) "2"
3) "3"
```

上面的 LTRIM 命令告诉 Redis 仅取从 index 0到2的列表元素，其它的会被丢弃。这允许一个非常简单但又很有用的模式:执行一个 List 推送操作 + 一个 List 截断操作，来实现增加一个新元素，并丢弃超过限制的元素。用法如下

```
LPUSH mylist <some element> 
LTRIM mylist 0 999
```


上面的组合增加一个新元素，并取1000个最新的元素放入这个 List。通过LRANGE 命令，你可以访问最前面的数据，而不需要记住非常旧的数据。

注意：LRANGE 是一个 O(N) 的命令，访问向列表头或者尾的小范围元素是一个常量时间操作。

 

### 9. List 上的阻塞操作

List 有一个特别的特性, 非常适合用来实现队列，并且它也是内部进程通信系统的一个基本构件：阻塞操作。

设想你想使用一个进程将数据推送到一个列表中，并且使用一个不同的进程在这些数据上做一些工作。这是一个普通的生产者-消费者模式，并且可以通过下面简单的方法来实现。

- 为了推送数据到这个 List 中，生产者调用 LPUSH 命令。
- 为了从列表中提取/处理数据，消费者调用 RPOP 命令。

然而，有时这个列表有可能是空的，没有什么需要处理，所以 RPOP 仅返回NULL。在这种情况下，消费者强制等待一些时间，并使用 RPOP 进行重试。这种方式称为 polling（拉），在这个场景下并不是很合适，因为它有下面几个缺点：

- 迫使 Redis 和客户端处理无用的命令（所有的请求在 List 为空的时候没有实际工作要做,他们仅是返回 NULL)
- 增加了数据处理的延时，因为在一个工作者接收到 NULL 之后，它等待了一段时间。为了使延时更小，我们可以在调用 RPOP 之间等待更短时间，但负面影响是放大了上面第一问题，因为这意味着产生了更多无用的 Redis  调用。

所以 Redis 实现了 BRPOP 和 BLPOP 命令。他们是 RPOP 和 LPOP 的另一个版本，可以在 List 为空的时候进行阻塞，仅当一个新的元素加入到列表中，或者用户指定的超时时间到达时，他们才会返回给调用者。

这是一个我们可以在工作者中调用 BRPOP 的例子：

```
> brpop tasks 5
1) "tasks"
2) "do_something"
```

它的意思是“等待在列表 tasks 中的元素, 但是如果5秒钟过后，还没有元素有效则返回”。

注意你可以使用0作为超时时间表示永久等待元素。并且你也可以指定多个 List 而不仅是一个。这样就可以在同一时间等待多个 List，并且当第一个 List 接收到元素时获得通知。

关于 BRPOP 的一些注意事项:

- 客户端按照顺序被服务：当某个客户端推送一个元素时，第一个阻塞等待 List 的客户端会被最先服务。
- BRPOP 的返回值是和 RPOP 不同的：它是两个元素的数组。数组中除了新加入的元素，还有 key 的名称，这是因为 BRPOP 和 BLPOP 能够等待多个队列中的元素。
- 如果超时时间到达，则返回NULL。

关于 List 和阻塞操作，你需要知道更多的事情。我们建议你阅读下面的更多信息。

- 可以使用 RPOPLPUSH 构建安全队列或者循环队列（网址为 <http://redis.io/commands/rpoplpush>）。
- 还有一个这个命令的阻塞版本，称为 BRPOPLPUSH（网址为 <http://redis.io/commands/brpoplpush>）。

 

### 10. key 的自动创建和删除

到现在，在我们的例子中，我们从没有在推送元素之前必须创建空列表，或者当他们不再有元素在里面的时候删除空列表。当 List 为空时，Redis 会负责删除这个 key；当 key 不存在时，而我们正在尝试增加一个新元素给它，Redis 会负责创建一个空列表。

这不是 List 特有的，它对所有包含多个元素的 Redis 数据类型都有效，包括 Set、Sorted Set 和 Hash。

可以用下面三个规则来总结这个行为：

- 当我们增加一个元素到一个聚合数据类型（aggregate data type），如果目标 key 不存在，那么在增加元素之前被自动创建一个空的聚合数据类型。
- 当我们从一个聚合数据类型中删除元素，如果值为空，那么这个 key 会自动销毁。
- 对于一个空 key，调用一个只读命令，如 LLEN（返回列表的长度），或者调用一个删除元素的写命令，总是和持有空聚合类型的 key 产生一样的结果。

第1个规则的例子：

```
> del mylist
(integer) 1
> lpush mylist 1 2 3
(integer) 3
```

然而，我们不能对 key 中存在的错误类型执行操作：

```
> set foo bar
OK
> lpush foo 1 2 3
(error) WRONGTYPE Operation against a key holding the wrong kind of value
> type foo
string
```

第2个规则的例子：

```
> lpush mylist 1 2 3 
(integer) 3 
> exists mylist 
(integer) 1 
> lpop mylist 
"3" 
> lpop mylist 
"2" 
> lpop mylist 
"1" 
> exists mylist 
(integer) 0
```


在所有的元素 pop 之后，这个 key 不再存在。

第3个规则的例子：

```
> del mylist 
(integer) 0 
> llen mylist 
(integer) 0 
> lpop mylist 
(nil)
```
 

### 11. Redis Hash 

```
Redis hash 是 field-value 对：

> hmset user:1000 username antirez birthyear 1977 verified 1 
OK 
> hget user:1000 username 
"antirez" 
> hget user:1000 birthyear 
"1977" 
> hgetall user:1000 
1) "username" 
2) "antirez" 
3) "birthyear" 
4) "1977" 
5) "verified" 
6) "1"
```


Hash 可以非常方便地表示对象，事实上你能放入到 Hash 中字段的数量是没有限制的(除了可用内存)，所以你可以在你的应用中以不同的方式使用 Hash。

命令 HMSET 可以用来设置 Hash 的多个字段，HGET 获取单个字段。HMGET  和 HGET 类似，但是返回一个值数组：

```
> hmget user:1000 username birthyear no-such-field 
1) "antirez" 
2) "1977" 
3) (nil)
```

这里也有命令能够在单个字段上执行操作，如 HINCRBY：

```
> hincrby user:1000 birthyear 10 
(integer) 1987 
> hincrby user:1000 birthyear 10 
(integer) 1997
```

你可以在文档（<http://redis.io/commands#hash>）中找到 Hash 命令的完整列表。

值得注意的是小 Hash（例如，一些带有小值的元素）在内存中使用特殊方式编码，这样使他们内存使用非常高效。

### 12. Redis Set 

Redis Set 是 String 的无序集合。SADD 命令增加新元素到一个 Set 中。还有一些针对 Set 的其它操作，如检查一个元素是否已经存在，可以在多个 Set 上执行交、并或者差运算等等。

```
> sadd myset 1 2 3 
(integer) 3 
> smembers myset 
1. 3 
2. 1 
3. 2
```


这里，我们加入三个元素到我的 Set 中，并且告诉 Redis 返回所有元素。正如你看到的，他们是无序的，在每次调用时，Redis 以任意顺序返回元素，没有和用户有任何关于元素顺序的约定。

Redis 有用来测试成员关系的命令。执行：

```
> sismember myset 3 
(integer) 1 
> sismember myset 30 
(integer) 0
```


“3”是 Set 的元素，而“30”不是。

Set 善于用来表达对象间关系。例如我们可以很容易用 Set 来实现打标签。

我们想打标签的每一个对象来构建一个 Set。这个 Set 包含关联这个对象的标签（tag） ID。

如果我们的新闻（news）ID 使用1、2、5和77来进行打标签，那么我们就可以使用一个 Set 关联我们的标签 ID 和新闻数据。

```
> sadd news:1000:tags 1 2 5 77 
(integer) 4
```

可是有时我可能也想有反向关系: 所有新闻列表都使用一个给定的 tag 来打标签。

```
> sadd tag:1:news 1000 
(integer) 1 
> sadd tag:2:news 1000 
(integer) 1 
> sadd tag:5:news 1000 
(integer) 1 
> sadd tag:77:news 1000 
(integer) 1
```


获得一个给定对象的所有 tag 是非常简单的:

```
> smembers news:1000:tags 
1. 5 
2. 1 
3. 77 
4. 2
```


注意：在这个例子中，我们是用一个 Redis Hash 来将 tag ID 映射到tag 名称上。

这里也有其它一些比较复杂的操作，仍可以很简单的使用恰当的 Redis 命令来实现。例如，我们可能想得到所有带有 tag 1, 2, 10和27的对象列表。我们可以使用 SINTER 命令来做到这一点，这个命令可以在不同 的Set 上做交集。我可以使用：

```
> sinter tag:1:news tag:2:news tag:10:news tag:27:news 
... results here ...
```

处理交集操作，你也可以执行并、差或提取一个随机元素等操作。

提取元素的命令是 SPOP，非常适合这种问题的建模。例如，为了实现一个Web 的扑克牌游戏，你可能想使用 Set 来表示。设想我们使用一个字母前缀表示4种花色的扑克牌 (C)lubs、(D)iamonds、(H)earts、(S)pades：

```
>  sadd deck C1 C2 C3 C4 C5 C6 C7 C8 C9 C10 CJ CQ CK 
  D1 D2 D3 D4 D5 D6 D7 D8 D9 D10 DJ DQ DK H1 H2 H3 
  H4 H5 H6 H7 H8 H9 H10 HJ HQ HK S1 S2 S3 S4 S5 S6 
  S7 S8 S9 S10 SJ SQ SK 
  (integer) 52
```

现在我们想提供给每一个玩家5张牌。SPOP 命令删除一个随机元素，并将它返回给客户端，所以在这种情况下，这是完美的操作。

然而如果我们对我们的牌直接调用它，在这个游戏的下一次玩的时候, 我们将需要再次获得一副牌。这可能就不是完美的。所以在开始的时候，我们可以拷贝 deck key 中的 Set 到 game:1:deck 的 key 中。

这可以使用 SUNIONSTORE 完成。这个命令是执行多个 Set 的并操作，并保存结果到另一个 Set 中。然而，一个单独 Set 的并操作是它自己，我们可以通过下面操作拷贝我们的 deck：

```
> sunionstore game:1:deck deck 
(integer) 52
```

现在，我们已经可以提供给第一个玩家5张牌:

```
> spop game:1:deck 
"C6" 
> spop game:1:deck 
"CQ" 
> spop game:1:deck 
"D1" 
> spop game:1:deck 
"CJ" 
> spop game:1:deck 
"SJ"
```

 
现在我们可以介绍获取 Set 内元素数量命令。这个通常被称为一个 Set 的基数，在 Set 理论上下文中，对应的 Redis 命令称为 SCARD。

```
> scard game:1:deck 
(integer) 47
```

数学计算: 52 - 5 = 47.

当你仅需要获取随机元素，而不需要从 Set 中删除它们，可以使用 SRANDMEMBER 命令。它也提供返回重复和非重复元素的能力。

 

### 13. Redis Sorted set 

Sorted Set 是和 Set 与 Hash 的混合体很类似的一种数据类型。和 Set一样, Sorted Set 由唯一、不重复的 String 元素组成。所以在某种意义上， Sorted Set 也是 Set。

然而，Set 中的元素是无序的，而 Sorted Set 中每一个元素关联一个称为分数（Score）的浮点值（这就是为什么这个类型也和 Hash 类似，因为每一个元素都映射到一个值）。

此外, 可以有序获取 Sorted Set 中元素（所以你不需要明确要求 Sorted Set 进行排序，排序是 Sorted Set 的特性)。他们根据下面的规则排序：

- 如果A和B是具有不同分数的元素，那么如果 A.score > B.score, 则A>B。
- 如果A和B确实有相同的分数, 那么如果 A 的 String 比 B 的字典顺序大，则A>B。A和B 的 String 是不可能相等的，因为 Sorted Set 不会包含重复元素。

让我们从一个简单的例子开始，将一些选定的黑客名称作为元素增加到 Sorted Set 中，他们的生日年份作为 Score。

```
> zadd hackers 1940 "Alan Kay"
(integer) 1
> zadd hackers 1957 "Sophie Wilson"
(integer) 1
> zadd hackers 1953 "Richard Stallman"
(integer) 1
> zadd hackers 1949 "Anita Borg"
(integer) 1
> zadd hackers 1965 "Yukihiro Matsumoto"
(integer) 1
> zadd hackers 1914 "Hedy Lamarr"
(integer) 1
> zadd hackers 1916 "Claude Shannon"
(integer) 1
> zadd hackers 1969 "Linus Torvalds"
(integer) 1
> zadd hackers 1912 "Alan Turing"
(integer) 1
```

正如你看到的，ZADD 和 SADD 类似，但是带有一个额外的参数（放置在被增加元素的前面），这就是 Score。ZADD 也是可变列表（Variadic），所以你可以随意指定多个 score-value 对，即使在上面的例子中没有使用。

返回以黑客生日年份排序的黑客列表是非常容易的，因为事实上他们已经作了排序。

实现注意事项:Sorted Set 通过一个 dual-ported 数据结构，包含一个 skip 列表和一个 hash 表，所以每次我们增加一个元素，Redis 执行一个O(log(N)) 的操作。这是非常好的实践，因为当我们请求排序的元素，Redis 完全不需要做任何工作，所有的都已经排序。

```
> zrange hackers 0 -1 
1) "Alan Turing" 
2) "Hedy Lamarr" 
3) "Claude Shannon" 
4) "Alan Kay" 
5) "Anita Borg" 
6) "Richard Stallman" 
7) "Sophie Wilson" 
8) "Yukihiro Matsumoto" 
9) "Linus Torvalds"
```

注意：0和-1表示元素 index 0到最后一个元素（-1在这里和 LRANGE 命令中的是一样的）。

如果我想反向排序他们， 使用 ZREVRAGE 代替 ZRANGE：

```
> zrevrange hackers 0 -1 
1) "Linus Torvalds" 
2) "Yukihiro Matsumoto" 
3) "Sophie Wilson" 
4) "Richard Stallman" 
5) "Anita Borg" 
6) "Alan Kay" 
7) "Claude Shannon" 
8) "Hedy Lamarr" 
9) "Alan Turing"
```

我们也可以返回 Score, 使用 WITHSCORES 参数：

```
> zrange hackers 0 -1 withscores 
1) "Alan Turing" 
2) "1912" 
3) "Hedy Lamarr" 
4) "1914" 
5) "Claude Shannon" 
6) "1916" 
7) "Alan Kay" 
8) "1940" 
9) "Anita Borg" 
10) "1949" 
11) "Richard Stallman" 
12) "1953" 
13) "Sophie Wilson" 
14) "1957" 
15) "Yukihiro Matsumoto" 
16) "1965" 
17) "Linus Torvalds" 
18) "1969"
```

### 14. 在范围上的操作

Sorted Set 比上面提到的更强大。他们可以对一个范围进行操作。让我们获得所有1950年和之前出生的人。我们可以使用 ZRANGEBYSCORE 命令做到这点：

```
> zrangebyscore hackers -inf 1950 
1) "Alan Turing" 
2) "Hedy Lamarr" 
3) "Claude Shannon" 
4) "Alan Kay" 
5) "Anita Borg"
```

我们要求 Redis 返回所有从负无穷到1950之间 Score 的所有元素(两个极端都包含)。

我们也可以删除一个区域的所有元素。让我们从 Sorted Set 中删除在1940到1960之间出生的所有黑客：

```
> zremrangebyscore hackers 1940 1960 
(integer) 4
```

ZREMRANGEBYSCORE 可能不是最好的命令名称，但是它非常有用。它的返回值是删除元素的数量。

Sorted Set 的另一个非常有用的命令是获取位置操作。可能用来查询一个元素在 Sorted Set 中的排序位置。

```
> zrank hackers "Anita Borg" 
(integer) 4
```

ZREVRANK 命令也是可以获得元素位置，但是元素反序排列的位置。

 

### 15. Lexicographical（字典顺序）scores 

在最近 Redis 2.8 版本中引入了一个新的特性，获取字典顺序（Lexicographical）范围。 可以设想这个特性就是在 Sorted Se t中所有元素插入时带有相同的 Score（元素使用C 语言的 memcmp 函数进行比较，所以保证没有冲突，并且每一个 Redis 实例回复相同的输出）。

字典顺序范围操作的主要命令有 ZRANGEBYLEX、ZREVRANGEBYLEX、ZREMRANGEBYLEX 和 ZLEXCOUNT。

例如， 让我们再次加入著名黑客列表，但是这次对所有元素使用 Score 0。

```
> zadd hackers 0 "Alan Kay" 0 "Sophie Wilson" 0 "Richard Stallman" 0 
  "Anita Borg" 0 "Yukihiro Matsumoto" 0 "Hedy Lamarr" 0 "Claude Shannon" 
  0 "Linus Torvalds" 0 "Alan Turing"
```

因为这个 Sorted Set 的排序规则，他们已经以字典顺序排序：

```
> zrange hackers 0 -1 
1) "Alan Kay" 
2) "Alan Turing" 
3) "Anita Borg" 
4) "Claude Shannon" 
5) "Hedy Lamarr" 
6) "Linus Torvalds" 
7) "Richard Stallman" 
8) "Sophie Wilson" 
9) "Yukihiro Matsumoto"
```

通过使用 ZRANGEBYLEX，我们可以得到字典顺序范围：

```
> zrangebylex hackers [B [P 
1) "Claude Shannon" 
2) "Hedy Lamarr" 
3) "Linus Torvalds"
```

范围可以设置为“包含”或“排除”规则（根据第一个字符)。string  infinite 和 minus infinite 分别使用+和-来指定。 

这个特性很重要，因为它允许我们将 Sorted set 用着通用的 index。例如，如果你想通过一个 128-bit 无符号整形参数来 index 一个元素，你可以将 Sorted Set 中元素设置成相同的 Score（例如0)，但在元素前面加上一个 8 byte 前缀。这8 byte由128 bit 数字以大端字节序（big endian）组成。 因为数字是大端字节序, 当按照字典顺序排序事实上也是按照数字排序。你可以获取在128bit空间中的任何范围，并且获得元素的值，然后抛弃这个前缀。

如果你想在一个更加正式的 demo 中查看这个特性，参考 Redis autocomplete demo （网址为 <http://autocomplete.redis.io/>）。

### 16. 更新 Score：积分榜

Sorted Set 的分数可以在任何时候都可以被修改。仅需要在 Sorted Set 中包含的元素上调用 ZADD 命令，就可以更新它的值（和位置）。其时间复杂度是O(log(N))。正是这样，Sorted Set 也适用在有大量更新的场景。

正因这个特性，一个常见的用例是积分榜。 Facebook 游戏就是一个经典的应用。你结合按最高分排序用户的能力，和获取位置的操作，来显示最前 N 位用户以及在积分榜上的用户位置。

### 17. Bitmap（位图）

Bitmap 不是一个真实数据类型，而是定义在 String 类型上面向 bit 操作的集合。因为 String 是二进制安全的，最大长度可以是512MB。他们适合设置2^32个不同的 bit。

bit 操作被分成两个组：

* 常量时间单 bit 操作，如设置一个 bit 为1或者0，又或者获得它的值；
* 在 bit 组上的操作，例如，在一个给定 bit 范围的 bit 的计数（例如，人口统计)。

Bitmap 最大优点之一是在存储信息时，他们经常能节约大量空间。例如在一个系统中，不同用户通过递增用户ID来表示。可以记录一个单个 bit 的信息(例如，仅需要512MB内存就可以记录40亿用户中每一个用户是否接受一个刊物)。

使用 SETBIT 和 GETBIT 命令设置或获取 bit：

```
> setbit key 10 1 
(integer) 1 
> getbit key 10 
(integer) 1 
> getbit key 11 
(integer) 0
```

SETBIT 命令将 bit 序号作为它的第一个参数，需要设置的值作为它的第二个参数。

GETBIG 仅返回在指定位置上 bit 值。在范围以外的 bit（定位一个在目标 key 保存的 string 的长度之外的 bit）总是被认为是0。

在 bit 组上的三个命令操作。

- BITOP 在不同的 string 按位操作。提供的操作有AND、OR、XOR 和 NOT。
- BITCOUNT 获取设置成1的 bit 数量。
- BITPOS 查找指定0或者1值的第一个 bit。

BITOPS 和 BITCOUNT 能够在 string 的 byte 范围上操作，而不是在整个 string 长度上运行。下面是一个 BITCOUNT 调用的简单例子。

```
> setbit key 0 1 
(integer) 0 
> setbit key 100 1 
(integer) 0 
> bitcount key 
(integer) 2
```


Bitmap 的常见用例：

- 所有类型的实时分析
- 高效的存储空间，同时具有高性能的关联对象ID的布尔信息

例如，你想知道你的网站用户日访问最长的 streak。从你的网站开放开始自0开始计天数，每次有用户访问网站就使用 SETBIT 设置一个 bit。使用当前 unix 时间作为 bit index, 减去初始偏移量，并除以3600*24。

对于每一个用户，你使用一个小 string 包含每天的访问信息。使用BITCOUNT 可以很容易得到一个用户访问网站的天数。使用一些 BITPOS 调用简单的获取和分析 bitmap 客户端，就可以很容易计算出最长的 streak。

Bitmap 可以很容易的分割成多个 key。因为需要将数据集合分片，以及避免使用巨大的 key ，我们将一个 Bitmap 分割成不同的 key，而不是将所有的 bit 设置到一个 key 中。实现这个的一个简单策略是每个 key 仅保存 M 个 bit，并使用 bit-number/M 获得 key 的名称，第 N 个 bit 通过 bit-number MOD M 来在 key 中定位。

 

### 18. HyperLogLogs

HyperLogLog 是带有统计功能的数据结构，用来统计唯一的事情（技术上来讲，这是估计 set 的基数）。一般来说，计算个数需要一定比例的内存，这个比例和计算元素个数相关。因为你要记住你之前计数过的元素，防止重复计数。有一系列算法以内存换精度：你可以以估计手段，限制一个标准误差，例如在 Redis 中，去查小于1%。这个算法的神奇在于你不用使用根据计算元素个数来设定使用的内存，而是可以使用常量的内存。最坏情况下，是 12k 的字节，如果 HyperLogLog 元素少，内存可以更少。

Redis 中，HLLs（HyperLogLog）是不同的数据结构，它被强制看作 Redis string，所以你可以用 GET 来序列化一个 HLL，使用 SET 来反序列化它到服务器上。 

概念上讲，HLL API 像使用 Set 来做相同的任务。你可以 SADD 每个观察元素到 set，使用 SCARD 来检查 set 中元素数量，它是唯一的，因为 SADD 不会重复添加存在的元素。

当你没有真正给 HLL 添加元素，因为数据结构仅仅包含状态，不包含实际元素。API 类似如下： 

* 每次你看到一个新元素，使用 PFADD 把它添加到计数器中。 
* 每次你想获取当前唯一元素的近似位置，请使用 PFADD，而后再使用 PFCOUNT。

```
> pfadd hll a b c d
(integer) 1
> pfcount hll
(integer) 4
```

使用这个数据结构的一个例子是统计用户每天唯一查询的的次数。

 
 
 
## 参考引用

* <http://redis.io/clients>
* <http://redis.io/topics/data-types-intro>