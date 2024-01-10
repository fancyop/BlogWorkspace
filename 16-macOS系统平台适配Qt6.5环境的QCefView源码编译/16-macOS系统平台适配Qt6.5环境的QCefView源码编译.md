
## macOS系统平台适配Qt6.5环境的QCefView源码编译

编译测试环境：

QCefView Commit: [27e1c99](https://github.com/CefView/QCefView/tree/27e1c99cdc1faf14a94e95ec8bf71a4021247912)

Qt Version: Qt6.5.1

- ### 修改 `CMakeLists.txt`

    修改 C++ 标准为17 

    ````cmake
    set(CMAKE_CXX_STANDARD 17)
    ````

    修改 最低目标支持版本（`注意：此处版本号需要与后续CEF最低支持版本保持一致`）

    关于为什么修改为 10.15 ，主要因为 Qt6.5相关库使用C++ 17标准，使用了 std::filesystem 类，而mac上对于使用了std::filesystem的最低部署系统支持为 10.15

    ````cmake
    set(CMAKE_OSX_DEPLOYMENT_TARGET 10.15)
    ````

- ### 修改 `CefViewCore` 中 `cef_variables.cmake`

    测试commit为：27e1c99，使用的的cef版本为102.0，其对应的`cef_variables.cmake` 路径如下，路径以实际拉取源码配置的cef版本对应的为准

    ```
    CefViewCore/dep/cef_binary_102.0.10+gf249b2e+chromium-102.0.5005.115_macosx64/cmake/cef_variables.cmake
    ```

    主要修改配置CEF编译的配置修改器部署的最低支持系统版本，与上一步版本号保持一致

    ````cmake
    set(CEF_TARGET_SDK               "10.15")
    ````

- ### 修改脚本 `generate-mac-x86_64.sh` 实现一键编译
    
    设置Qt6的路径 (改为实际路径)

    ````shell
    export QTDIR=/Users/xxxxxx/Qt-6.5/6.5.1/macos
    ````

    打开一键编译开关

    ````shell
    BUILD_PROJECT=1
    ````

    配置编译模式和执行install

    Debug

    ````shell
    cmake --build "${BUILD_DIR}" --target install --config Debug
    ````

    Release

    ````shell
    cmake --build "${BUILD_DIR}" --target install --config Release
    ````

    完整的修改脚本如下，Debug、Release 按需分开编译

    ```shell 
    #!/bin/bash

    export QTDIR=/Users/xxxxxx/Qt-6.5/6.5.1/macos

    BUILD_PROJECT=1

    BUILD_DIR="$(pwd)/.build/macos.x86_64"

    while getopts bi flag
    do
        case "${flag}" in
            b) BUILD_PROJECT=1;;
        esac
    done

    echo ============== Config project ==============
    cmake -G "Xcode" -S . -B "${BUILD_DIR}" -DPROJECT_ARCH=x86_64 -DBUILD_DEMO=ON -DUSE_SANDBOX=ON -DCMAKE_INSTALL_PREFIX:PATH="$(pwd)/out/macos.x86_64"

    if [ ${BUILD_PROJECT} -eq 1 ] 
    then
        echo ============== Build project ==============
        cmake --build "${BUILD_DIR}" --target install --config Debug
        # cmake --build "${BUILD_DIR}" --target install --config Release
    fi
    ```

- ### 编译

    执行一键编译脚本

    ````shell
    ./generate-mac-x86_64.sh
    ````

    `out` 为编译完成后的输出目录
