# 第一章 mysqli扩展（重点）
## 1.1 mysqli扩展的基本介绍
1. mysqli扩展和mysql扩展都可以完成对mysql数据库的操作
2. mysqli扩展可以看做是mysql扩展的升级版, i = improve
3. mysqli扩展的性能比mysql扩展好，因此我们在新项目开发中，就不再使用mysql扩展, 新项目对mysql数据库的操作使用(mysqli, pdo).
4. mysqli扩展支持面向对象开发, mysqli也支持面向过程
5. 使用mysqli扩展前，需要引入一个dll文件, 该文件是 php_mysqli.dll, 该文件是安装php时有，在 ~/php/ext/php_mysqli.dll
## 1.2 快速入门案例
```php
<?php
	header('content-type:text/html;charset=utf8');
	// 使用mysqli扩展进行扩展
	// 1.创建一张测试表
	/*
	create table `user3`(
		id int unsigned primary key auto_increment,
		name varchar(64) not null default '',
		pwd char(32) not null default '',
		email varchar(64) unique not null,
		birthday date not null
	
	)charset=utf8 engine=MyISAM;
	
	insert into `user3` values(null,'张三',md5('abc'),'zs@sohu.com',current_date());
	insert into `user3` values(null,'李四',md5('abc'),'ls@sohu.com','2012-12-12');
	*/
	
	// 2. 具体操作
	// (1). 创建一个对象
	/*
		功能得到一个连接到mysql数据库的连接，代表php和mysql数据库之间的一个连接
		@param string localhost 主机
		@param string root 用户名
		@param string '' 密码
    @param string 'phpStudy' 数据库
		@param int 3306 端口号
	*/
	$mySQLi = new MySQLi('localhost','root','','phpStudy','3306');
	
	// 判断是否连接成功	如果没有错误，返回0，如果有错误，返回对应的错误号
	if($mySQLi->connect_errno){
		// connect_error属性表示错误信息
		die('连接错误，错误信息是'.$mySQLi->connect_error);
	} else {
		echo '<br>连接成功';
	}
	// (2). 设置字符集
	$mySQLi -> set_charset('utf8');
	// (3). 准备sql语句
	$sql = 'select * from `user3`';
	// (4). 执行sql语句
	$res = $mySQLi -> query($sql);
	// (5). 显示数据
	while($row = $res->fetch_assoc()){
		var_dump($row);
	}
	// (6). 关闭相关资源
	// 先释放结果集
	$res -> free();
	// 关闭连接
	$mySQLi->close();
?>
```
## 1.3 mysqli使用的细节说明
+ 如果执行的是dml语句,则返回bool 举例
```php
<?php
	header('content-type:text/html;charset=utf8');
	// 1.连接sql	
	$mySQLi = new MySQLi('localhost','root','','phpStudy',3306);
	// 2. 设置字符集
	$mySQLi -> set_charset('utf8');
	// 3. 准备sql语句
	$sql1 = 'insert into `user3` values(null,"王五",md5("abc"),"ww@sohu.com","2011-11-11")';  // 插入数据
	$sql1 = 'update `user3` set email="abc@sohu.com" where id = 3';			// 更新数据
	$sql1 = "delete from `user3` where id = 3";			// 删除数据
	
	if($mySQLi->query($sql1)){
		echo '<br>执行成功';
	} else {
		echo '<br>执行失败sql语句是'.$sql1;
		echo '<br>失败的原因是'.$mySQLi->error;
		exit;
	}
?>
```
+ 从mysql_result 对象取出数据的方式有4种
```php
  // 第一种
  while($row = $res->fetch_assoc()){
		var_dump($row);
	}
  // 第二种
  // 将结果指向0,相当于放到结果集最前面
  // 将结果指向0
	$res -> data_seek(0);
	// 索引数组
	while($row = $res->fetch_row()){
		var_dump($row);
	}
	echo '<br>================================<br>';
	$res->data_seek(0);
    // 第三种
	// 索引数组，关联数组一起返回
	while($row = $res->fetch_array()){
		var_dump($row);
	}
	echo '<br>================================<br>';
	$res->data_seek(0);
    // 第四种
	while($row=$res->fetch_object()){
		var_dump($row);
		echo '<br>名字是'.$row -> name;
	}
```
+ 如何判断我们的dml语句是否真正影响了表
```php
  // 判断是否影响表
	if($mySQLi->affected_rows > 0){
		echo '<br>对表进行改变';
	} else {
		echo '<br>对表没有造成任何影响';
	}
```
+ 如何获取到刚刚添加的自增长id的值
```php
  echo '<br>id='.$mySQLi -> insert_id;
```
