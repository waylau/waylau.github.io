---
layout: post
title:   Flex的一些总结
date:   2013-03-26 00:52
author: admin
comments: true
categories: [Flex]
tags: [Flex,总结]
---

##Loader与URLLoader,SWFLoader

（1） Loader Loader 类可用于加载 SWF 文件或图像（JPG、PNG 或 GIF）文件。 使用 load() 方法来启动加载。 被加载的显示对象将作为 Loader 对象的子级添加。 （2）URLLoader URLLoader 类以文本、二进制数据或 URL 编码变量的形式从 URL 下载数据。 在下载文本文件、XML 或其它用于动态数据驱动应用程序的信息时，它很有用。URLLoader 对象会先从 URL 中下载所有数据，然后才将数据用于 ActionScript。 它会发出有关下载进度的通知，通过 bytesLoaded 和bytesTotal 属性以及已调度的事件，可以监视下载进度。在加载非常大的视频文件（如 FLV 的视频文件）时，可能会出现内存不足错误。

区别：一个用来加载可显示数据，一个用来加载非显示数据因为loader是继承与容器类的，所以首先它是个容器，而URLLoader 是继承与EventDispatcher，它不是容器URLLoader 类以文本、二进制数据或 URL 编码变量的形式从 URL 下载数据，在加载非常大的视频文件（如 FLV 的视频文件）时，可能会出现内存不足错误，返回的数据在data属性里面而loader多加载图片，swf等可视化对象，加载后作为唯一的子对象显示在列表上。

应用范围 Loader: 多用于swf,图片(jpg,png,gif) URLLoader: 多用于文本文件(xml,php,jsp…) 使用方法

        loader.contentLoaderInfo.addEventListener(Event.COMPLETE, loadComplete); 
        private function loadComplete(event:Event)
        { 
            trace("done");
            addChild(loader);
        }

        //URLLoader:
        xmlLoader.dataFormat=URLLoaderDataFormat.TEXT; 
        xmlLoader.addEventListener(Event.COMPLETE,xmlLoaded); 
        private function xmlLoaded(event:Event) { 
            try {
                myXML = XML(event.target.data);area.text=myXML;
            }catch (e:TypeError) 
            {
                area.text="Load faild:\n"+e.message;
            } 
        } 
Loader 在使用Loader来加载数据时，添加侦听事件时，注意一定要给Loader的 contentLoaderInfo属性增加事件，而不是给Loader对象增加事件。
	 var loader:Loader = new Loader(); loader.contentLoaderInfo.addEventListener(Event.COMPLETE, completeHandler); loader.contentLoaderInfo.addEventListener(SecurityErrorEvent.SECURITYERROR, securityErrorHandler); loader.contentLoaderInfo.addEventListener(IOErrorEvent.IOERROR, ioErrorHandler);

（3）可以使用SWFLoader和Loader两个类来加载子应用程序，多数情况下我们使用SWFLoader。它是Loader的一个包装类，提供了很多附加功能，使加载子应用程序更简单。 SWFLoader有如下特征： 支持flex的样式和特效；而Loader类却不支持任何固有的样式和特效。 方便管理加载进度；如果使用Loader则首先要获取LoaderInfo引用。

是一个UIComponent组件。SWFLoader它自己管理了所有子显示对象，不需要添加多余的代码。 自动缩放显示内容的大小

可以加载Application以外的实现类，如果检测到不是一个Application，会自动进行处理。 可以处理不同的版本。Loader不支持加载不同的编译版本

当使用Loader加载不是受信任区域的swf时，必须提供一个遮罩来重新定位，否则它会显示在屏幕外面： 代码 
	import flash.display.*;
	import flash.net.URLRequest;
	var rect:Shape = new Shape();
	rect.graphics.beginFill(0xFFFFFF);
	rect.graphics.drawRect(0, 0, 100, 100);
	addChild(rect);
	var ldr:Loader = new Loader();
	ldr.mask = rect;
	var url:String = "http://www.unknown.example.com/content.swf";
	var urlReq:URLRequest = new URLRequest(url);
	ldr.load(urlReq);
	addChild(ldr);

SWFLoader加载外部swf是一个很好的方法，在进行Flex游戏开发的时候，我们通常会需要很多Flash提供的游戏角色，这些角色可以每个都是一个单独的swf，也可以是存在于一个swf文件中的各个元件。两种方法各有优劣： 1. 单独的swf，每个角色独立性很强，可以在需要的时候才加载。未来修改角色也不会影响到其它角色。但是会有很多的swf需要管理。 2. 每个角色是一个元件，存在于一个swf中，这种方法比较容易管理资源，比较干净。但是修改一个角色都需要重新编译swf，可能会误操作影响其它的元件。而且一次加载所有角色，可能会加载许多不必要的元件，浪费带宽。

两种方法怎么选择，根据项目读者根据以下几个问题考虑： 1. 你的所有元件相互之间是否又很多公用元件？如果是的话，可以考虑放到一个swf中，因为这样会降低所有元件的文件量。 2. 你的所有元件是否都继承同一些类，实现同一些接口，调用同一些类？如果是的话，可以考虑放到一个swf中，这样比较容易管理类包，而且降低swf的文件量，因为共有的类只编译一次。 3. 你的所有元件是否完全独立，没有任何关系？如果是的话，你可以考虑每个角色独立的swf。 具体实现代码如下：

```mxml
<?xml version="1.0" encoding="utf-8"?>
<s:Application xmlns:fx="http://ns.adobe.com/mxml/2009" 
               xmlns:s="library://ns.adobe.com/flex/spark" 
               xmlns:mx="library://ns.adobe.com/flex/mx" minWidth="955" minHeight="600"
               creationComplete="init()"
               >
    <fx:Declarations>
        <!-- Place non-visual elements (e.g., services, value objects) here -->
    </fx:Declarations>
    <fx:Script>
        <![CDATA[
            import mx.controls.SWFLoader;
            import mx.core.UIComponent;

            private var swfLoader:SWFLoader = new SWFLoader();
            private function init():void{
                swfLoader.addEventListener(Event.COMPLETE,swfLoadComplete);
                swfLoader.load("assets/fishmomo.swf");
            }
            private function swfLoadComplete(e:Event):void{
                /**
                 * 将整个swf都加载到flex舞台上
                 * 此方法将每个flex要用的资源做成单独的swf，需要时才加载进来
                 */ 
                addElement(swfLoader);
                var swf:MovieClip = swfLoader.content as MovieClip;
                //fish 是 swf主舞台上的一个鱼对象,属性命名为fish
                //调用舞台上fish元件的say方法
                swf.fish.say("Load a swf");

                /**
                 * 此方法可以把所有的flex要用的资源放到一个swf中
                 * 把需要在flex中使用的元件链接到一个类，并将此元件拖到flash舞台
                 * 通过类名，随意显示其中一个元件到flex舞台
                 */ 
                var FishSymbol:Object = swfLoader.content.loaderInfo.applicationDomain.getDefinition("Fish");
                var f:MovieClip = new FishSymbol() as MovieClip;
                //调用元件的say方法
                f.say("Load a symbol within a swf");
                var ui:UIComponent = new UIComponent();
                ui.addChild(f);
                addElement(ui);
            }
        ]]>
    </fx:Script>
</s:Application>
```

##导致内存泄露的一些情况：

事件监听： 对父级对象加了监听函数，会造成内存泄露，例： 

	override protected function mouseDownHandler(…):void { 
	systemManager.addEventListener(“mouseUp”, mouseUpHandler); …… 
	} 
解决： 在销毁对象的时候，remove掉这些监听，虽然弱引用也可以避免这些问题，但自己掌控感觉更好。 但以下几种情况不会造成内存泄露： 弱引用：

	someObject.addEventListener(MouseClick.CLICK, handlerFunction, false, 0, true); 
对自己的引用：

	this.addEventListener(MouseClick.CLICK, handlerFunction); 
子对象的引用： 

	private var childObject:UIComponent = new UIComponent; addChild(childObject); childObject.addEventListener(MouseEvent.CLICK, clickHandler); 
总之…有addEventListener，就removeEventListener一下吧，要为自己做的事负责~哈哈

清除引用 remove掉子对象后并不会删除该对象，他还保留在内存中，应该将引用设置为null removeChildren(obj); obj = null;

静态成员 Class （或MXML）中有： 
	public static var _eventService : MyService=new MyService(); 
解决：在dispose时，需要设置： 
	_eventService =null;

module (未解决) moduleLoader unloadModule后 ModuleInfo 并不会被GC. Garbage Collection in a MultiCore Modular Pipes Application 这篇文章介绍了一种GC策略，感觉对于ModuleInfo 的GC无效。 (未尝试、未遇到)

CSS样式 module 中如果使用了shell的CSS定义或是<mx:Style> 这样的定义，那么这个module将不能GC. 弹出的窗口应该是同样的结果. 解决方法，使用动态CSS文件 module init中 StyleManager.loadStyleDeclarations(“css/myStyle.swf”); module dispose中 StyleManager.unloadStyleDeclarations(“css/myStyle.swf”);

TextInput/Textarea(未解决) 如果module中有window使用了TextInput/Textarea控件，不点击没有问题，只要点上去，那么很遗憾了，module和所在窗体将不能被GC. 这个BUG非常严重，目前还没有解决方法。 memory leak when using TextInput and TextArea when click the keyboard 这里面附加的解决方法无效。 通过profiler分析，应该和Focusmanager有关，只有一点击就不会释放。

CursorManager.setCursor 使用了 cursorID = CursorManager.setCursor(iconClosed); dispose时要 CursorManager.removeCursor(cursorID);

Bitmap 如果使用Bitmap，结束时需要调用其dispose方法，否则内存消耗巨大。 另外，BitmapData是可以共享使用的，多个Bitmap可以使用同一BitmapData，节省不少内存。 var bmp:Bitmap =new Bitmap(); …….. if (bmp.bitmapData!=null) { bmp.bitmapData.dispose(); } Image 包含了Image对象时，在removeChildren时会造成不能释放（测试多次，结果不一，建议还是做如下处理）。 解决： img.source = null; this.removeChild(img); img = null;

Loader、SWFLoader、声音、视频、Effect等… 如果是加载SWF文件，先停止播放。 停止声音的播放 停止正在播放的影片剪辑(Movieclip) 关闭任何连接的网络对象，例如Loader正在加载，要先close。 取消对摄像头或者麦克风的引用 取消事件监听器 停止任何正在运行的定时器，clearInterval() 停止任何Timer对象，timer.stop() 停止正在播放的效果(Effect) 其他

binding也疑似有memory leak 问题。 引用以及内存泄露相关博文和资料： http://blogs.adobe.com/aharui/2007/03/garbagecollectionandmemory.html http://www.craftymind.com/2008/04/09/kick-starting-the-garbage-collector-in-actionscript-3-with-air/ http://www.cnblogs.com/janyou/archive/2008/11/25/1340753.html http://www.dreamingwell.com/articles/archives/2008/05/understandingm.php 总结：由于之前Flash一直是在网页上使用，一般网页都是看完就关掉的，估计Adobe在内存回收这块也没有下太大的功夫， 现在AIR的出现使得内存管理也相当重要了，并且，AIR本身对内存的消耗就相当大，一个没有任何内容的初始创建的AIR程序，就得占掉10-20M+的内存…AIR还需改善.

##Modules

为什么要模块化;模块化提供了分离应用程序代码到不同的swf文件上以便减少下载时间和文件大小。 使用Modules的好处是： 

1. 主应用程序开始时不需马上下载modules。应用程序会根据需求载入和卸载modules。

2. 由于每个modules独立于其他应用程序modules，当需要改变一个modules时，你只需要重编译这个modules而不是整个应用程序。模块化可创建模块化应用程序是提高Flex框架性能的重要方面，提供更多能力控制下载时间和文件大小，使用modules，你可以根据哪些可被独立载入，哪些可被其他程序使用而分离应用程序代码。

优点是合理分割了主Application，模块化开发。更小的SWF文件体积，配合RSL和延迟加载策略，大大减少用户等待时间。

缺点就是容易引起内存泄露。

##RSL

RSL（Runtime Shared Library），即运行时加载库。当前L主要有3个级别的RSL，一个是Standard RSL（即一个网站内共享），一个是Cross-domain RSL（跨域共享），最后一个也是最关键的是Framework RSL（Flex框架共享）。 功能说明 Flex Builder在默认情况下，编译的每个程序都是独立的。每个程序都包含了所需类的一个副本，例如：在多个程序中都用到了VBox这个控件，那么每个程序都要独立的拥有一个VBox类。由此造成了程序代码的重复，使得代码量增大很多。而RSL正是解决此问题的一种方法，它可以把共享代码提取出来，然后在相同域的程序之间进行共享。这些共享代码不会再编译进程序，而是放在单独的库中，供运行时加载。另外，RSL也可以在客户端进行缓存，因此不需要在每次使用程序时重复下载。

RSL(Runtime shared libraries)即动态链接库，在程序运行时由FlashPlayer动态加载。静态链接库是SWC文件，通过编译器的library-path和include-libraries 编译进应用程序。采用静态链接的应用程序SWF会产生比较大的文件以及更长的下载时间。使用RSL的应用程序载入时间短且文件比较小，而且提高了内存使用效率，只是 在开始需要花点时间下载RSL。RSL的强大体现在多个应用程序共享公共代码时，因为RSL只需要被下载一次，多个应用程序动态链接到相同的RSL，访问其中已经缓存在客 户端的资源。 使用RSL： (1).在项目文件夹中点右建,选择"properties"-"Flex Build Path"-"Library Path" (2).该选项卡上我们看到"FrameWork linkage",默认是"Merged into cdoe"(FLEX4默认是RSL) (3).点开下拉,选择"runtime shared library(RSL)" (4).针对自定义的SWC，修改其link type为RSL，选择None,同时勾上Automatically extract swf to deployment path(自动将SWF提取到部署路径)。如果想对不同域的RSL共享，则选择Digests（摘要），同时指定其Policy file url（策略文件）。具体可参考FLEX SDK中的SWC文件处理方式。 (5).点击OK

##SWC

SWC 文件是类似 zip 的文件。静态链接库是SWC文件，通过编译器的library-path和include-libraries编译进应用程序。新建Flex Library Object或使用compc命令可以制作SWC。

##E4X
E4X是在ECMA-357标准中定义的，并成为AS3的一部分。优点是提供更简明和可读性强的语法从XML中查找和提取数据。

##profile工具
可以很方便地观察对象的创建和销毁，帮助检查内存泄露问题。

##FlexUnit
FlexUnit - Flex单元测试框架

##其他
1、flex生成出来的文件都是很大,请问你用什么办法进行缩小呢? (1)RSL (2)Module (3)外部加载资源

2、做一个flex项目,你认为成功的要素在哪呢? (1)数据和需求等传统软件项目的成功要素 (2)模块化开发,MVC框架 (3)swf文件减肥，运行效率，内存泄露问题，前台优化 (4)开源组件的使用

3、flex 前端的性能优化 (1)、避免容器的多级嵌套，减少相对尺寸、相对定位的使用。 (2)、尽量使用轻量级的容器 (3)、避免使用大体积的组件，比如DataGrid、AdvancedDataGrid (4)、处理数据时多用分页的方式 (5)、少使用setStyle (6)、使用延迟实例化加载子组件

4.Embed绑定图片有什么缺点? 答：直接编译到swf文件中，造成其体积过大。而且由于嵌入代码中，维护不便。

5、flex里调用JS措施? 答：直接在AS中利用ExternalInterface.call()来调用JS措施。如： 
	import flash.external.ExternalInterface ; ExternalInterface.call("JSFunction"); ExternalInterface.call("JSFunctionWithParameters","myParameter"); var result:String=ExternalInterface.call("JSFunctionWithReturn"); 
6、用JavaScript调用ActionScript函数 答：利用ExternalInterface.addCallback在JavaScript里设置对Flex的回调措施并且在JavaScript里调用ActionScript措施。 AS: 
	ExternalInterface.addCallback("function1",callback1); privatefunctioncallback1():void { Alert.show("callback1executed"); } JS: container[swf].function1(); 
6、FileReference browse(typeFilter:Array = null):Boolean//揭示一个文件博览对话框，让用户抉择要上载的文件。 cancel():void//废止正在对该 FileReference 对象厉行的任何上载或下载垄断。 download(request:URLRequest, defaultFileName:String = null):void//敞开对话框，以批准用户从长途服务器下载文件. upload(request:URLRequest, uploadDataFieldName:String = "Filedata", testUpload:Boolean = false):void//开始将用户抉择的文件上载到长途服务器。 

7、flash与flex是如何调停开发的？ 答：这个问题能够这么来会意，万一是确乎必需用到FLEX SDK的利用，那我们就能够发生一个flex工程，翔实必需flash做UI的时候，输出成swc作为flex的skin，万一说并无须要flex sdk，只是为了编码得体而抉择flex的话，那我们凡是发生一个as工程，让flex作为flash的编码器。

8、请说下事件里的currentTarget 和 target的区别? 答：在事件流的过程中，目标阶段确定的目标对象由Event中的target属性来记录， 冒泡阶段移动的游标则由currentTarget来记录。事件对象冒泡过程中每往上移动一级，就会克隆出一个仅与前副本currentTarget不同的新副本。

9、warning: unable to bind to property ” on class ‘Object’ (class is not an IEventDispatcher) 在使用ItemRender时,经常会出现这种警告? 答：ArrayCollection的子元素是无法作为数据源绑定的。可以声明中间变量，在override set data时将ArrayCollection的子元素传入， 然后再将中间变量作为新的绑定源即可。

10、常用的几个切换数据的组件比如: TabNavigator等都有属性 creationPolicy 你知道这种策略的使用方式是什么嘛?以及优,缺点呢? 答：延迟实例化。优点是可以更快地加载组件，缺点是首次访问其他组件时，由于需要重新加载此组件，可能会有一定的延迟。

11、请试写一个自定义的验证组件 答：

	package myCompenent
        {
            import mx.validators.Validator;//引用Validator类
            import mx.validators.ValidationResult;//引用ValidationResult类
            public class myValidators extends Validator
            {
                public function myValidators()//构造函数
                {
                    super();
                }
                private var results:Array;//定义一个数组，用以存储错误
                //重写验证函数
                override protected function doValidation(value:Object):Array
                {
                    var s:String = value as String;
                    results = [];//清空数组
                    results = super.doValidation(value);//先用继承类中的doValida tion方法验证
                    if (results.length &gt; 0)//如果验证时有错，返回错误信息
                        return results;
                    if(s.length&gt;6)//自定义验证，字符长度不超过6
                    {
                        //记录出错信息
                        results.push(new ValidationResult(true,”text”,”StringTooLong”, “字符长度超过6了”));
                    }
                    return results;
                }
            }
        } 