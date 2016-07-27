---
layout: post
title: AsWing 应用之JTable的例子
date: 2013-07-26 02:12
author: admin
comments: true
categories: [ActionScript3,AsWing]
tags: [ActionScript3,AsWing,JTable]
---
	package
	{
		import flash.display.Sprite;
		import org.aswing.ASColor;
		import org.aswing.JScrollPane;
		import org.aswing.JTable;
		import org.aswing.VectorListModel;
		import org.aswing.event.SelectionEvent;
		import org.aswing.table.PropertyTableModel;
		import org.aswing.table.sorter.TableSorter;
		/**
		 * 一个AsWing JTable的例子
		 *@author waylau.com
		 *@data 2013-7-26
		 */
		public class JTableExample extends Sprite
		{
			private var data:Array = [];  //表格数据源
			private var columns:Array = [];  //列项
			private var table:JTable;
			/**
			 * PropertyTableModel是JTable的表格模型之一，常常用于把一个对象列表中的对象数据，以对象为行，
			 * 以对象属性为列来显示，它提供默认方式把属性值以直接字符串化显示，也提供转换器（PropertyTranslator 或Function）
			 * 把值转换成期望的字符串格式来显示
			 */
			private var tableModel:PropertyTableModel; 
			/**
			* VectorListModel是AsWing自带的ListModel 实现，它的行为类似一个Vector， 
			* 可以随意在任何位置插入/删除数据，并且在数据改变时，会自动触发对应的事件，使得JList 自动更新界面显示。
			*/
			private var modeList:VectorListModel; 
			/**
			 * TableSorter表格排序器。它是TableModel 接口的一个实现，通过包装一个现有的 TableModel 来实现排序。
			 * 实现原理为：以选择排序方式为基准，变换原始TableModel 类的行的值，以达到变换行顺序的目的。
			 * 排序所采用的值比较函数，可以通过给指定列设定列类型，然后给指定列类型设定比较函数来达到。
			 * 默认情况下，所有列的类型都是Object 类型，TableSorter 自带String 和Number 类型的比较函数，
			 * 对于没有指定比较函数的列，默认将采用String 类型的比较函数
			 */
			private var sorter:TableSorter;
	
			public function JTableExample()
			{
				super();
				init();
			}
	
			private function init():void
			{		
				this.stage.frameRate = 25;
				data = [{name:"iiley", sex:1, age:26, score:99},
					{name:"Comeny", sex:0, age:24, score:100},
					{name:"Tom", sex:1, age:30, score:98},
					{name:"Lita", sex:0, age:16, score:36}]
				;
				modeList = new VectorListModel(data);
				columns = ["Name", "Sex", "age", "Score"] ;
				tableModel = new PropertyTableModel(modeList,columns,["name","sex","age","score"],[null,sexTranslator,null,null,]);
				sorter = new TableSorter(tableModel);
	
				this.table = new JTable(sorter);
				this.table.setRowHeight(22);
				//水平间隔线
				table.setShowHorizontalLines(false);
				//竖直间隔线
				table.setShowVerticalLines(false);
				//设置字体颜色，前景色
				 table.setForeground(new ASColor(0xffffff));
				//设置选中行的时候的字体颜色
				table.setSelectionForeground(new ASColor(0x123456));
				//拉伸的模式，随意拉伸
				this.table.setAutoResizeMode ( JTable.AUTO_RESIZE_OFF );
				//只能选择一行
		 		this.table.setSelectionMode ( JTable.SINGLE_SELECTION );
				//设置表格底色
				this.table.setBackground(new ASColor(0x000000));
				//设置监听row选中
				this.table.addEventListener(SelectionEvent.ROW_SELECTION_CHANGED,__rowSelectHandler);
	
				var panel:JScrollPane = new JScrollPane();
				panel.setSizeWH(200, 100);
				panel.append(this.table);
				this.addChild(panel);
			 	panel.validate();
			}
			/**
			 * row选择执行事件
			 */
			private function __rowSelectHandler(e:SelectionEvent):void
			{
				var rowCount : int = this.table.getSelectedRow();
	
				if(rowCount&gt;0)
				{
					var row:Object = modeList.getElementAt(sorter.modelIndex(rowCount));
				}
				if(row!=null)
				{
					trace("Human"+" Name:"+row.name+"，Sex："+row.sex+"，Age："+row.age+"，Score："+row.score);
				}
			}
			/**
			 * 性别转换器
			 */
			private function sexTranslator(info:*, key:String):String
			{
				if(info[key]=="1")
				{
					return "男";
				}
				else
				{
					return "女";
				}
			}
		}
	} 
参考：

<http://www.aswing.org/?tag=jtable>

<http://wiki.9ria.com/index.php/Aswing>

<http://www.ebibi.com/i/study/2010/0913/1262.html>
