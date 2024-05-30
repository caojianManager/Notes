# VSCode中OpenGL项目的相关配置

## 一 .vscode文件的相关配置
### settings.json配置文件

```json
{
    "editor.fontSize":15,
    "editor.fontFamily": "Menlo",
    "window.zoomLevel": 1
}
```
### tasks.json配置文件
```json
{
    "tasks": [
        {
            "type": "cppbuild",
            "label": "C/C++: g++.exe 生成活动文件",
            "command": "C:/Software/mingw64/bin/g++.exe",
            "args": [
                "-fdiagnostics-color=always",
                "-g",
                "${file}",
                "-o",
                "${workspaceFolder}/build/LearningOpenGL.exe",
            ],
            "options": {
                "cwd": "C:/Software/mingw64/bin"
            },
            "problemMatcher": [
                "$gcc"
            ],
            "group": {
                "kind": "build",
                "isDefault": true
            },
            "detail": "调试器生成的任务。"
        }
    ],
    "version": "2.0.0"
}
```

### launch.json文件

```json
{
    // 使用 IntelliSense 了解相关属性。 
    // 悬停以查看现有属性的描述。
    // 欲了解更多信息，请访问: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "(gdb) 启动",
            "type": "cppdbg",
            "request": "launch",
            "program": "${workspaceFolder}/build/LearningOpenGL.exe",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${fileDirname}",
            "environment": [],
            "externalConsole": false,
            "MIMode": "gdb",
            "miDebuggerPath": "C:/Software/mingw64/bin/gdb.exe",
            "setupCommands": [
                {
                    "description": "为 gdb 启用整齐打印",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                },
                {
                    "description": "将反汇编风格设置为 Intel",
                    "text": "-gdb-set disassembly-flavor intel",
                    "ignoreFailures": true
                }
            ]
        }

    ]
}
```

### CMakeLists.txt文件配置

```json
cmake_minimum_required(VERSION 3.25.2) #规定cmkae最低版本要求
project(LearningOpenGL) #项目名称，不一定和文件夹名称相同

set(CMAKE_CXX_STANDARD 17)

# --执行文件输出目录
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)

# --定义头文件和源文件目录
set(INCLUDE_DIR ./include)
set(SRC_DIR ${PROJECT_SOURCE_DIR}/src)

# --定义include文件目录
include_directories(${INCLUDE_DIR})
include_directories(${PROJECT_SOURCE_DIR}/src/Tools)

# 查找指定目录下的所有.cpp与.h文件 并存放到指定变量名SC_FILES中
file(GLOB_RECURSE SC_FILES "${SRC_DIR}/*.cpp" "${SRC_DIR}/*.h")

add_executable(${PROJECT_NAME} src/main.cpp include/glad/glad.c ${SC_FILES})

link_directories(${PROJECT_SOURCE_DIR}/lib)
target_link_libraries(${PROJECT_NAME} ${PROJECT_SOURCE_DIR}/lib/glfw3.dll)
```