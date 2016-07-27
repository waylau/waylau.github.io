---
layout: post
title: SVG实例之中国地图
date: 2014-05-27 07:08
author: admin
comments: true
categories: [SVG]
tags: [SVG,geo,map]
---
 
SVG 做地图具有可以任意比例放大缩小不失真的优点。本例基于d3.js的svg制作。

地图数据用GeoJSON存储。GeoJSON 是基于JSON 的、为Web 应用而编码地理数据的一个标准。实际上，GeoJSON 并不是另一种格式，而只是JSON 非常特定的一种使用方法。

网上有很多免费的GeoJSON下载，本例子中国地图GeoJSon 可以从[本例源码](https://github.com/waylau/svg-china-map) 下载

###1.获取d3
在项目中引入即可，
  `<script src="http://d3js.org/d3.v3.min.js"></script>`

###2.创建svg
    var width = 960, height = 500;
    var svg = d3.select("body")
            .append("svg")
            .attr("width", width)
            .attr("height", height);
###3.创建投影(projection)
    var projection = d3.geo.mercator().translate([width / 2, height / 2]).center([105, 38]).scale(550);

###4.创建path
    var path = d3.geo.path().projection(projection);

###5. 解析json
    d3.json("china.geo.json", function(json) {

        svg.selectAll("path")
                .data(json.features)
                .enter()
                .append("path")
                .attr("d", path)
                .on('mouseover', function(data) {
                    d3.select(this).attr('fill', 'rgba(2,2,139,0.61)');

                    //创建显示tooltip用的矩形
                    svg.append("rect")
                            .attr("id", "tooltip1")
                            .attr("x", 50)
                            .attr("y",50)
                            .attr("width",140)
                            .attr("height",130)
                            .attr("stroke","black")
                            .attr("fill","none")
                    ;

                    //创建显示tooltip文本
                    svg.append("text")
                            .attr("id", "tooltip2")
                            .attr("x", 100)
                            .attr("y", 100)
                            .attr("text-anchor", "middle")
                            .attr("font-family", "sans-serif")
                            .attr("font-size", "11px")
                            .attr("font-weight", "bold")
                            .attr("fill", "black")
                            .text(data.properties.name);
                })
                .on('mouseout', function(data) {
                    d3.select(this).attr('fill', 'rgba(128,124,139,0.61)');
                    //Remove the tooltip
                    d3.select("#tooltip1").remove();
                    d3.select("#tooltip2").remove();
                })
                .attr('fill', 'rgba(128,124,139,0.61)')
                .attr('stroke', 'rgba(255,255,255,1)')
                .attr('stroke-width', 1)
        ;
    });

演示效果：
<iframe style="width: 100%; height: 600px" src="../assets/demos/china-map/china_map.html" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

[完整源码下载](https://github.com/waylau/svg-china-map)

[中国地图GeoJSON](../assets/demos/china-map/china.geo.json)

[查看例子](../assets/demos/china-map/china_map.html)
