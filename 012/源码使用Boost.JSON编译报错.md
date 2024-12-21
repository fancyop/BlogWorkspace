### 问题描述
在使用Boost.Json库时，代码编译提示：

    无法打开文件`libboost_json-*.lib` `libboost_container-*.lib`

### 原因分析
提示上述错误意味着编译器找不到依赖的lib库，所以可以引出两种解决方法
1. ~~编译boost源代码，编译得到所需的lib库文件（**而我不想编库。。弃之**）~~
2. 告诉编译器这些个缺失的lib库可不可以不用它哇！嘿，当然可以！！

### 解决方法

1. 添加hpp头文件，[1.78版本官方文档](https://www.boost.org/doc/libs/1_78_0/libs/json/doc/html/json/overview.html)提示了直接使用源码即[Header-Only](https://www.boost.org/doc/libs/1_78_0/libs/json/doc/html/json/overview.html#json.overview.requirements.header_only)需要添加下面头文件
    ```cpp
    #include <boost/json/src.hpp>
    ```
2. 需要添加下面两项全局宏！**全局**！！，禁用相关模块的自动链接，不然它就会自己去找lib，然而源码没有编译是没有的，所以提示报错。解决参考链接：[Some boost libraries didn't compile](https://github.com/s9w/cpp-lit/issues/3)

    `BOOST_CONTAINER_NO_LIB` 

    `BOOST_JSON_NO_LIB`

    - Visual Studio工程添加全局宏

        在 `配置属性`>>`C/C++`>>`预处理器`>>`预处理器定义`里面添加：`BOOST_CONTAINER_NO_LIB;BOOST_JSON_NO_LIB;`

    - Qt工程添加全局宏

        在pro文件内添加：`QMAKE_DEFINES += BOOST_CONTAINER_NO_LIB BOOST_JSON_NO_LIB`

    - CMake工程添加全局宏

        在CMakeLists.txt内添加：`add_definitions(-DBOOST_CONTAINER_NO_LIB -DBOOST_JSON_NO_LIB)`

### 扩展内容
问：如何判断哪些boost库支持Header-Only？

答：方法见 [13-如何判断哪些boost库模块支持Header-Only](../13-如何判断哪些boost库模块支持Header-Only/13-如何判断哪些boost库模块支持Header-Only.md)
