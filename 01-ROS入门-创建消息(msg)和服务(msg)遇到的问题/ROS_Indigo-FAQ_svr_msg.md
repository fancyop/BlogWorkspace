#### ROS测试版本：Indigo

 在按ROS入门教程（[传送门](http://www.ncnynl.com/archives/201608/508.html)）行进过程中到了执行 

```bash
rosmsg show beginner_tutorials/Num
```

 命令时，出现提示：

The manifest (with format version 2) must not contain the following tags: run_depend 

这个警告的主要解决方法是使用catkin方式时在package.xml文件中将教程中添加的两行语句

```xml
 <build_depend>message_generation</build_depend>
 <run_depend>message_runtime</run_depend>
```

 要改成 

```xml
  <build_depend> message_generation </build_depend>
  <exec_depend> message_runtime </exec_depend>
```

或者

```xml
<build_export_depend>message_generation</build_export_depend>
<exec_depend>message_runtime</exec_depend>
```

这样就得以解决问题