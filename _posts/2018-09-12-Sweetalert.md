---
layout:     post
title:     Sweetalert 弹窗插件
subtitle:   优化你的弹窗
date:       2018-9-13
author:     wylb
header-img: img/post-bg-map.jpg
catalog: 	 true
tags:
    - js
    - 弹窗
---
# Sweet alert 使用教程

####  优化单调的 alert 页面展示

- js、css 文件的引用，上传当前文件夹目录到软件根目录

  ```
  <head>
  	<script src="../js/plugins/sweetalert/sweetalert.min.js"></script> 
  	<link rel="stylesheet" type="text/css" href="../css/plugins/sweetalert/sweetalert.css">
  </head>
  ```

### 使用教程

- 弹框输出一句话

  ```
  swal("这是一条信息！")
  ```

- 成功信息弹窗

  ```
  swal("干得漂亮！", "你点击了按钮！","success")
  ```

- 警告信息弹窗，'确认'按钮带有一个函数

  ```
  swal({ 
    title: "确定删除吗？", 
    text: "你将无法恢复该虚拟文件！", 
    type: "warning",
    showCancelButton: true, 
    confirmButtonColor: "#DD6B55",
    confirmButtonText: "确定删除！", 
    closeOnConfirm: false
  },
  function(){
    swal("删除！", "你的虚拟文件已经被删除。", "success"); 
  });
  ```

- 定时关闭信息弹窗

  ```
  swal({ 
    title: "自动关闭弹窗！", 
    text: "2秒后自动关闭。", 
    timer: 2000, 
    showConfirmButton: false 
  });
  ```

  ​

- 和 PHP 混写

  ```
  <!DOCTYPE html>
  <html>
  <head>
          <script src="../js/plugins/sweetalert/sweetalert.min.js"></script> 
          <link rel="stylesheet" type="text/css" href="../css/plugins/sweetalert/sweetalert.css">
  </head>

  <body class="gray-bg">
          <?php
          echo "<script> swal('删除文件成功！'); </script>";
          //echo "<script> swal('干得漂亮！', '你点击了按钮！','success'); </script>";  注意引号
          ?>
  </body>
  </html>
  ```

  ​

  ​
