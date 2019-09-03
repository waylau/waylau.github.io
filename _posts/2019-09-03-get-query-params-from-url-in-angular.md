---
layout: post
title: 从 Angular 中的 URL 获取查询参数
date: 2019-09-03 00:22
author: admin
comments: true
categories: [Angular]
tags: [Angular,URL]
---

本文介绍了如何从 Angular 中的 URL 获取查询参数。


<!-- more -->
通过注入ActivatedRoute的实例，可以订阅各种可观察对象，包括queryParams和params observable。以下是范例：

```ts
import { ActivatedRoute } from '@angular/router';  // 用于获取路由参数
import { Component, OnInit } from '@angular/core';
import { DomSanitizer } from '@angular/platform-browser'; // 用于HTML过滤
import { Location } from '@angular/common'; // 用于回退浏览记录

import { NewsDetailService } from '../news-detail.service';

@Component({
  selector: 'app-news-detail',
  templateUrl: './news-detail.component.html',
  styleUrls: ['./news-detail.component.css']
})
export class NewsDetailComponent implements OnInit {

  newsDetailData = null;
  newsUrl = null;

  constructor(private newsDetailService: NewsDetailService,
    private domSanitizer: DomSanitizer,
    private route: ActivatedRoute,
    private location: Location) { }

  ngOnInit() {
    this.showNewsDetailData();
  }

  // 展示新闻详情数据
  showNewsDetailData() {
    this.route.queryParams.subscribe(p => {
      this.newsUrl = p.newsUrl // 获取参数

      this.newsDetailService.getNewsData(this.newsUrl).subscribe(
        (newsApiData) => this.newsDetailData =
          this.domSanitizer.bypassSecurityTrustHtml(newsApiData.toString()) //HTML过滤
      );

    });
  }

  // 返回
  goback() {
    // 浏览器回退浏览记录
    this.location.back();
  }
}
```




## 参考引用

* 本文同步至: <https://waylau.com/get-query-params-from-url-in-angular/>
* 完整源码：<https://github.com/waylau/angular-enterprise-application-development-samples>
* 《[Angular企业级应用开发实战](https://search.jd.com/Search?keyword=柳伟卫%20Angular企业级应用开发实战&enc=utf-8&wq=柳伟卫%20Angular企业级应用开发实战&pvid=26dca7699970469ba0f6455f689bd4eb)》