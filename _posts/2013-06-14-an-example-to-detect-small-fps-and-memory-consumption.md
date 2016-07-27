---
layout: post
title: 一个用于检测FPS和内存占用的小例子
date: 2013-06-14 01:51
author: admin
comments: true
categories: [ActionScript3]
tags: [ActionScript3,FPS]
---
用于实时检测FPS（实际上可以算是检测CPU）和内存占用情况。

使用方法，在application的Document Class（或者Application Class）上构建一个检测器实例（MonitorKit）并addChild即可，所含参数比较简单，不再一一赘述：

	 var monitor:MonitorKit = new MonitorKit(MonitorKit.MKMODE_T);
	addChild(monitor);
	package 
	{
	  import flash.display.Sprite;
	  import flash.display.Stage;
	  import flash.events.Event;
	  import flash.system.System;
	  import flash.text.TextField;
	  import flash.text.TextFieldAutoSize;
	  import flash.text.TextFormat;
	  import flash.utils.getTimer;
	
	  public class MonitorKit extends Sprite{
	    public static const MKMODE_T:String = "MKMODE_T";
	    public static const MKMODE_B:String = "MKMODE_B";
	    public static const MKMODE_L:String = "MKMODE_L";
	    public static const MKMODE_R:String = "MKMODE_R";
	    public static const MKMODE_TL:String = "MKMODE_TL";
	    public static const MKMODE_TR:String = "MKMODE_TR";
	    public static const MKMODE_BL:String = "MKMODE_BL";
	    public static const MKMODE_BR:String = "MKMODE_BR";
	
	    private static var stageInstance:Stage;
	
	    private var lastTime:uint = getTimer();
	    private var frames:Number = 0;
	    private var monitorKitTextField:TextField;
	    private var mode:String;
	    private var textColor:uint;
	    private var backgroundColor:uint;
	    private var transparent:Boolean;
	    public static var delay:Number = 0;
	    public function MonitorKit(_mode:String = MKMODE_T,
	        _transparent:Boolean = true,
	        _textColor:uint=0xffffff,
	        _backgroundColor:uint=0x000000) {
	      mode = _mode;
	      transparent = _transparent;
	      textColor = _textColor;
	      backgroundColor = _backgroundColor;
	      // Initialize it when rendered on the stage.
	      addEventListener(Event.ADDED_TO_STAGE, initMonitorHandler);
	    }
	
	    private function initMonitorHandler(event:Event):void {
	      // Unregister the event handler
	      removeEventListener(Event.ADDED_TO_STAGE, initMonitorHandler);
	      stageInstance = this.stage;
	      monitorKitTextField = new TextField();
	      monitorKitTextField.selectable = false;
	      monitorKitTextField.background = transparent;
	      monitorKitTextField.textColor = textColor;
	      monitorKitTextField.backgroundColor = backgroundColor;
	      monitorKitTextField.autoSize = TextFieldAutoSize.LEFT;
	      var format:TextFormat = new TextFormat();
	      format.font = "Courier New";
	      format.size = 15;
	      monitorKitTextField.setTextFormat(format);
	      monitorKitTextField.defaultTextFormat = format;
	      monitorKitTextField.text = "[ Loading... ]";
	      addChild(monitorKitTextField);
	      stageInstance.addEventListener(Event.ENTER_FRAME, enterFrameHandler);
	    }
	
	    public function enterFrameHandler(evt:Event):void {
	      frames++;
	      var currentTime:uint = getTimer();
	      var deltaTime:uint = currentTime - lastTime;
	      var fps:Number = frames / deltaTime * 1000;
	      monitorKitTextField.text = "FPS: " + fps.toFixed(1);
	      monitorKitTextField.appendText("\nMem: " + Number(System.totalMemory/1024/1024).toFixed(3)+"(M)");
	      frames = 0;
	      lastTime = currentTime;
	      // Replace the monitor object
	      switch (mode) {
	        case MKMODE_T:
	          monitorKitTextField.x = stageInstance.stageWidth / 2 - monitorKitTextField.width / 2;
	          monitorKitTextField.y = 0;
	          break;
	        case MKMODE_B:
	          monitorKitTextField.x = stageInstance.stageWidth / 2 - monitorKitTextField.width / 2;
	          monitorKitTextField.y = stageInstance.stageHeight - monitorKitTextField.height;
	          break;
	        case MKMODE_L:
	          monitorKitTextField.x = 0;
	          monitorKitTextField.y = stageInstance.stageHeight / 2 - monitorKitTextField.height / 2;
	          break;
	        case MKMODE_R:
	          monitorKitTextField.x = stageInstance.stageWidth - monitorKitTextField.width;
	          monitorKitTextField.y = stageInstance.stageHeight / 2 - monitorKitTextField.height / 2;
	          break;
	        case MKMODE_TL:
	          monitorKitTextField.x = 0;
	          monitorKitTextField.y = 0;
	          break;
	        case MKMODE_TR:
	          monitorKitTextField.x = stageInstance.stageWidth - monitorKitTextField.width;
	          monitorKitTextField.y = 0;
	          break;
	        case MKMODE_BL:
	          monitorKitTextField.x = 0;
	          monitorKitTextField.y = stageInstance.stageHeight - monitorKitTextField.height;
	          break;
	        case MKMODE_BR:
	          monitorKitTextField.x = stageInstance.stageWidth - monitorKitTextField.width;
	          monitorKitTextField.y = stageInstance.stageHeight - monitorKitTextField.height;
	          break;
	        default:
	        break;
	      }
	    }
	  }
	} 