---
layout: post
title: flex4 ColumnChart外包裹NavigatorContent进行TabNavigator分页出错，柱子移位 
date: 2012-03-28 00:33
author: admin
comments: true
categories: [Flex]
tags: [Flex,ColumnChart]
---
之前单页面的ColumnChart显示图标正常。
后来用TabNavigator进行分2页，每个页面用NavigatorContent，
在NavigatorContent中 再添加ColumnChart 出现了柱子移位的现象。

在ColumnChart 外包裹一层BorderContainer ，问题解决。~

    <mx:TabNavigator id="tabnavigator1" width="100%" height="100%" >
    <s:NavigatorContent id="nc_Id_1" label="调峰报告"
    icon="@Embed('assets/icon/list/48.png')" width="100%" height="100%">
    <ElectroLoadShifting:Usefuldays width="100%" height="100%" id="usefuldays_id"/>
    </s:NavigatorContent>

    <s:NavigatorContent id="nc_Id_2" label="调峰执行"
    icon="@Embed('assets/icon/list/48.png')" width="100%" height="100%" >

    <s:HGroup width="100%" height="100%" paddingLeft="1" paddingRight="10"
    verticalAlign="middle" includeIn="loadingState">
    <s:BorderContainer width="100%" height="100%" borderVisible="false" >
    <s:HGroup width="100%" height="100%" >
    <s:Label id="unit_id" width="18" height="100%"
    text="{'用电量︵'+_model.loadingShitServerModel.unit+'︶'}"
    verticalAlign="middle"/>
    <s:VGroup width="100%" height="100%">
    <s:Label text="近30天用电量"/>
    <mx:ColumnChart id="column" height="100%" color="0x323232" width="100%"
    showDataTips="true" dataProvider="{_model.loadingShitServerModel.datas}"
    dataTipRenderer="common.skin.ColumnChart_DatatipSkin" creationComplete="init()"
    >
    <mx:horizontalAxis>
    <mx:CategoryAxis categoryField="time"/>
    </mx:horizontalAxis>

    <mx:series>
    <mx:ColumnSeries xField="time" yField="data" displayName="日用电量" id="columSeries_id" />
    </mx:series>
    </mx:ColumnChart>
    </s:VGroup>

    </s:HGroup>
    </s:BorderContainer>

    </s:HGroup>

    <s:HGroup width="100%" height="100%" includeIn="coarseTurningState">
    <ElectroLoadShifting:CoarseTurning id="coarseTurning_id" width="100%" height="100%"/>
    </s:HGroup>
    <s:HGroup width="100%" height="100%" includeIn="fineTurningState">
    <ElectroLoadShifting:FineTurning id="fineTurning_id" width="100%" height="100%"/>
    </s:HGroup>
    <s:HGroup width="100%" height="100%" includeIn="powerSchemeState">
    <ElectroLoadShifting:PowerScheme id="powerScheme_id" width="100%" height="100%"/>
    </s:HGroup>

    </s:NavigatorContent>

    </mx:TabNavigator>