# how-to-create-cpp-project-in-linux
使用vscode远程连接linux创建C++ CMake项目

# Linux下创建vscode CMake C++项目





## 使用脚本自动化创建

### 项目对应文件目录结构如下


```
.
├── build
├── CMakeLists.txt
├── include
├── src
│   └── main.cpp
└── .vscode
    └── launch.json
```


* **`.vscode`**: 存放项目配置文件

* **`build`**：存放CMake构建产生的文件

* **`CMakeLists.txt`**: CMake项目的配置文件
* **`include`**: 存放头文件 .h .hpp
* **`src`**: 存放源文件 .cpp

### 创建脚本

```bash
#!/bin/bash

# 检查是否输入了项目名参数
if [ -z "$1" ]; then
    echo "请提供项目名作为参数."
    exit 1
fi

# 设置项目名变量
PROJECT_NAME=$1

# 创建项目文件夹
mkdir -p $PROJECT_NAME

# 创建.vscode文件夹及其内部的launch.json文件
mkdir -p $PROJECT_NAME/.vscode

cat <<EOL > $PROJECT_NAME/.vscode/launch.json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "type": "lldb",             // 可更换调试器
            "request": "launch",
            "name": "(lldb)Debug",
            "program": "\${command:cmake.launchTargetPath}",
            "args": [],
            "cwd": "\${workspaceFolder}"
        }
    ]
}
EOL

# 创建src, include, 和build文件夹
mkdir -p $PROJECT_NAME/src
mkdir -p $PROJECT_NAME/include
mkdir -p $PROJECT_NAME/build

# 创建CMakeLists.txt文件
cat <<EOL > $PROJECT_NAME/CMakeLists.txt
cmake_minimum_required(VERSION 3.10)

# 设置项目名
project($PROJECT_NAME)

# 设置C++标准
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED True)

# 包含include目录
include_directories(include)

# 查找src目录下的所有源文件
file(GLOB SOURCES "src/*.cpp")

# 添加可执行文件
add_executable(\${PROJECT_NAME} \${SOURCES})
EOL

# 创建src/main.cpp文件
cat <<EOL > $PROJECT_NAME/src/main.cpp
#include <iostream>

int main() {
    std::cout << "Hello, World!" << std::endl;
    return 0;
}
EOL

echo "项目 $PROJECT_NAME 创建成功."
```





### 添加脚本到环境变量中

1. **将脚本移动到`/usr/local/bin`**:

```bash
sudo mv ~/create_cpp_project.sh /usr/local/bin/create_cpp_project
```

1. **确保脚本具有可执行权限**:

```
sudo chmod +x /usr/local/bin/create_cpp_project
```

1. **更新`PATH`变量（如果必要）**:

- 打开您的shell配置文件，例如`.bashrc`或`.bash_profile`（如果使用的是Bash shell）。

```bash
vim ~/.bashrc
```

- 在文件末尾添加以下行：

```
export PATH=$PATH:/usr/local/bin
```

- 保存文件并退出编辑器，然后使更改生效：

```bash
source ~/.bashrc
```

### 测试脚本

现在可以在任何地方通过命令行调用您的脚本了：

```bash
create_cpp_project 项目名
```
