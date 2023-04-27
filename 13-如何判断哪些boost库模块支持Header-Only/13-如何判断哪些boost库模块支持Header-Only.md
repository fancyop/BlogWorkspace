### 问题描述
如何判断哪些boost库支持Header-Only？

### 解决方法
1. 下载boost库，官方下载地址：https://www.boost.org/users/history/

2. 在库根目录下执行命令即可查看需要编译才可使用的库模块，即不在此列表内的支持`Header-Only`

    - Windows
        ```` bash
        .\bootstrap.bat
        .\b2 --show-libraries
        ````
    - macOS & Linux
        ```` bash
        ./bootstrap.sh --show-libraries
        ````
    
    得到的结果如下，以Version 1.78.0为例测试：
    ```` bash
    The following libraries require building:
    - atomic
    - chrono
    - container
    - context
    - contract
    - coroutine
    - date_time
    - exception
    - fiber
    - filesystem
    - graph
    - graph_parallel
    - headers
    - iostreams
    - json
    - locale
    - log
    - math
    - mpi
    - nowide
    - program_options
    - python
    - random
    - regex
    - serialization
    - stacktrace
    - system
    - test
    - thread
    - timer
    - type_erasure
    - wave
    ````