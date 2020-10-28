---
title: curl上传文件$_FILES为空问题
date: 2020-04-09 21:05:29
categories: 
 - PHP
tags:
 - php
 - $_FILES
cover: https://s2.ax1x.com/2019/10/19/Kn0gZ4.png
---

## php使用curl上传文件

代码如下:

```
<?php

/* http://devtest.com/upload.php:
print_r($_POST);
print_r($_FILES);
*/

$ch = curl_init();

$data = array('name' => 'Foo', 'file' => '@/home/vagrant/test.png');

curl_setopt($ch, CURLOPT_URL, 'http://devtest.com/load_file.php');
curl_setopt($ch, CURLOPT_POST, 1);
curl_setopt($ch, CURLOPT_POSTFIELDS, $data);

curl_exec($ch);
?>
```

接收代码:

```
<?php
print_r($_POST);
print_r($_FILES);
```

运行结果:

```
php -f demo.php
Array
(
[name] => Foo
[file] => @/home/vagrant/test.png
)
Array
(
)
```

这时我们发现 通过 curl 发送的文件 在另一边 $_FILES 接受不到

### 解决方法1：

``` 
<?php

/* http://devtest.com/upload.php:
print_r($_POST);
print_r($_FILES);
*/

$ch = curl_init();

$data = array('name' => 'Foo', 'file' => '@/home/vagrant/test.png');

curl_setopt($ch, CURLOPT_URL, 'http://devtest.com/load_file.php');
curl_setopt($ch, CURLOPT_POST, 1);
curl_setopt($ch, CURLOPT_SAFE_UPLOAD, false);
curl_setopt($ch, CURLOPT_POSTFIELDS, $data);

curl_exec($ch);
?>
```

### 解决方法2:

> 5.6版本下

```
<?php

/* http://devtest.com/upload.php:
print_r($_POST);
print_r($_FILES);
*/

$ch = curl_init();

$data = array('name' => 'Foo', 'file' => new \CURLFile(realpath('/home/vagrant/test.png')));

curl_setopt($ch, CURLOPT_URL, 'http://devtest.com/load_file.php');
curl_setopt($ch, CURLOPT_POST, 1);
curl_setopt($ch, CURLOPT_SAFE_UPLOAD, false);
curl_setopt($ch, CURLOPT_POSTFIELDS, $data);

curl_exec($ch);
?>
```

### 最后附上完整代码

> 使用时建议配上域名单独使用

```
<?php
if ($_SERVER['REQUEST_METHOD'] == 'POST'){
//    var_dump(file_get_contents('php://input'));die;
    var_dump($_FILES);
    exit(1);
}elseif ($_SERVER['REQUEST_METHOD'] == 'GET') {
    $str = <<<EOT
<center><h1>curl 文件上传测试demo</h1></center>
EOT;
    echo $str;
    exit(1);
}

function ceshi()
{
    $ch = curl_init();

    $data = array('name' => 'Foo', 'file' => new \CURLFile('/Users/higanbana/Desktop/GM8A78.png'));


    curl_setopt($ch, CURLOPT_URL, 'http://devtest.com/fileTest.php');
    curl_setopt($ch, CURLOPT_POST, 1);
    curl_setopt($ch, CURLOPT_SAFE_UPLOAD, false);
    curl_setopt($ch, CURLOPT_POSTFIELDS, $data);

    return curl_exec($ch);
}

var_dump(ceshi()); // version php 7.3.11
```