---
layout: post
title: Flex 4.6 XML搜索、匹配示例
date: 2011-05-20 01:43
author: admin
comments: true
categories: [Flex]
---
效果见图

初始化界面

<img class="alignnone" alt="" src="http://a.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=512510f857fbb2fb302b58177f715199/3c6d55fbb2fb431665e962df22a4462308f7d3a4.jpg?referer=becb17663887e9501b00c75c1d7a&amp;x=.jpg" width="258" height="385" />

输入“设置”，进行搜索、匹配后界面

<img class="alignnone" alt="" src="http://a.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=512510f857fbb2fb302b58177f715199/3c6d55fbb2fb431665e962df22a4462308f7d3a4.jpg?referer=becb17663887e9501b00c75c1d7a&amp;x=.jpg" width="258" height="385" />

下面是代码
    <?xml version="1.0" encoding="utf-8"?>
    <s:Application xmlns:fx="http://ns.adobe.com/mxml/2009" 
			   xmlns:s="library://ns.adobe.com/flex/spark" 
			   xmlns:mx="library://ns.adobe.com/flex/mx" 
			   minWidth="955" minHeight="600" creationComplete="init(event)">
	<fx:Script>
		<![CDATA[
			import mx.events.FlexEvent;
			import mx.utils.StringUtil;
			import spark.events.TextOperationEvent;

			public var rawList:XML = 
				<apps name="应用程序" >
				  <item name="关于"
				  		icon="plugins/about_002/assets/icons/about_48.png" 
				  		iconSmall="plugins/about_002/assets/icons/about_24.png" 
				  		moduleUrl="plugins/about_002/Ahout_002.swf"
				  		version="1.0"  date="2013-5-13"  author="way" type="plugin"
				  		description="关于能源管理中心的一个说明" />

				 <item name="程序管理" 
				  		icon="plugins/appManager_001/assets/icons/apps_48.png" 
				  		iconSmall="plugins/appManager_001/assets/icons/apps_24.png"
				  		moduleUrl="plugins/appManager_001/AppManager_001.swf"
				  		version="1.0"  date="2013-3-5"  author="way" type="plugin"  resizable="false"
				  		description="对系统应用，及用户自定义应用进行管理"
				  		shortCutCreated="true"/>

				  <item name="导航设置" 
				  		icon="plugins/navigatorSetting_001/assets/icons/gears_48.png" 
				  		iconSmall="plugins/navigatorSetting_001/assets/icons/gears_24.png" 
				  		moduleUrl="plugins/navigatorSetting_001/NavigatorSetting_001.swf"
				  		version="1.0"  date="2013-3-13" author="way" type="plugin" resizable="false"
						description="对导航进行设置"/>
				  <item name="主题设置" 
				  		icon="plugins/themeSetting_001/assets/icons/icon_48.png" 
				  		iconSmall="plugins/themeSetting_001/assets/icons/icon_24.png" 
				  		moduleUrl="plugins/themeSetting_001/ThemeSetting_001.swf"
				  		version="1.0"  date="2013-3-5" author="way" type="plugin" resizable="false"
				  		description="对系统的主题、样式进行设置"/>
			    </apps>
				;     

			[Bindable]
			public var rawListShow:XML = null;  //搜索过滤后的数据

			protected function init(event:FlexEvent):void
			{
				getData();
			}

			protected function textinput1_changeHandler(event:TextOperationEvent):void
			{
				getData();
			}

			//初始化数据
			private function getData():void{
				if(StringUtil.trim(textInput.text) == ""){
					rawListShow = rawList ;

				}else{
					createNewXml(textInput.text,rawList);
				}

				/* 打开或关闭指定项目下的所有树项目。如果设置 dataProvider 之后立即调用 expandChildrenOf()，
				则您可能看不到正确的行为。您应该等待对组件进行验证或调用 validateNow() 方法 */
				tree1.validateNow();

				expandtree();
			}

			//搜索过滤后,生产新的xml
			private function createNewXml(searchString:String, xml:XML):void{
				rawListShow =<apps  name="应用程序" />;

				for(var i:int = 0; i<xml.children().length(); i++)
				{
					var itemXml:XML = xml.child("item")[i];
					if(isInStr(searchString,itemXml.@name.toString())){   
						rawListShow.appendChild(itemXml);
					}
				}
			}

			//判断search_str是否在str内 
			public function isInStr(search_str:String , str:String):Boolean{    
				var num:int= str.indexOf(search_str);
				if(num>-1){
					return true;
				}else{
					return false;
				}
			}

			//展开树
			private function expandtree():void {
				for each(var item:XML in this.tree1.dataProvider)
				this.tree1.expandChildrenOf(item,true);
			}

		]]>
	</fx:Script>
	<fx:Declarations>
		<!-- 该例子由waylau.com提供-->
	</fx:Declarations>
	<s:TextInput prompt="请输入要搜索的字段"  x="10" y="10" 
				 change="textinput1_changeHandler(event)" id="textInput"/>
	<mx:Tree id="tree1" dataProvider="{rawListShow}" 
			 labelField="@name" width="200" height="300" x="10" y="40">
	</mx:Tree>

	<s:Label text="更多例子 请关注  waylau.com" x="10" y="360"/>
    </s:Application>
 