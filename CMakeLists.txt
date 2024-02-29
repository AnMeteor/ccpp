# 单行注释
#[[
    多行注释
    一、git 未提交到仓库的忽略操作
        1、新建一个文件.gitignore（注意文件前面的点）

        2、.gitignore 也可以在每一个子文件夹下创建

        # 忽略当前文件夹下的picture_1.txt
        /img.jpg
        
        # 忽略当前文件夹下的js文件夹
        /js
        
        # 忽略当前目录中所有名为ok.jpg的文件
        *.jpg
        
        # 忽略当前目录中所有扩展名为.mp4的文件
        *.mp4
        
    二、已提交仓库的忽略方法
        1、先按第一步设置忽略

        2. 对于已经加入到版本库的文件，可以在版本库中删除该文件

            git rm --cached .idea

        3. 然后在 .gitignore 中配置忽略

            .idea

        4. add  和 commit 提交即可

]]
# 配置cmake 需求的最低版本要求，低于此版本，需升级cmake
cmake_minimum_required(VERSION 3.22.1)

# 定于工程名称，工程版本，工程描述，web主页地址，支持的语言（默认情况下，支持所有语言），如果都不需要，只定义工程名即可
project(index)

# 指定使用c++标准， 有c++11、c++14、c++17、c++20
# 编译命令的时候可以指定：gcc *.c -std=c++17 -o app (可变化为: g++ *.cpp -std=c++17 -o app)
# 1.在CMakeLists.txt 中，通过 set命令指定
# 增加 -std=c++17
set(CMAKE_CXX_STANDARD 17)

# 定义变量
# 对多个源文件进行单独定义，单独定义多个源文件，无法根源解决多次引用，冗余问题
# set(SRC_LIST hello.c)

#${PROJECT_SOURCE_DIR} ：是执行cmake .. 命令下 携带的路径。(.. 就是这个路径下的文件)
#[[
    aux_source_directory(< dir > < variable >)
        dir：要搜索的目录
        variable：将从dir目录下搜索到的源文件列表存储到 该变量中 
]]
# aux_source_directory(${PROJECT_SOURCE_DIR} SRC_LIST1)

# 搜索多个源文件目录(引入源文件)，可如下写法
# aux_source_directory(${PROJECT_SOURCE_DIR}/src SRC_LIST1)
# set(SRC_LIST SRC_LIST1 SRC_LIST2)

#[[ 
    GLOB： 将指定目录下搜索到的满足条件的所有文件名生成一个列表，将其存储到变量中
    GLOB_RECURSE：递归搜索指定目录，将搜索到的满足条件的文件名生成一个列表，将其存储到变量中
    file(GLOB/GLOB_RESURSE 变量名 要搜索的文件路径和文件夹类型)
    {CMAKE_CURRENT_SOURCE_DI} 是CMakeLists.txt 文件当前的路径
]]
# 搜索所有指定格式的文件 并输出到变量中
#[[
    GLOB_RECURSE 
        需要注意的是，使用 file(GLOB_RECURSE) 命令来自动查找文件是方便的，但不是最佳实践。这是因为它会在每次生成时重新扫描文件系统，
        这可能会导致不必要的重新编译，特别是当新的源文件被添加或删除时。最好的做法是手动列出源文件，以确保只有真正需要的文件被编译。
]]
# file(GLOB SRC_LIST1 ${PROJECT_SOURCE_DIR}/*.c)
# file(GLOB SRC_LIST2 ${PROJECT_SOURCE_DIR}/src/*.c)
# file(GLOB SRC_LIST3 ${PROJECT_SOURCE_DIR}/include/*.c)
file(GLOB SRC_LIST1 ${PROJECT_SOURCE_DIR}/*.c)

# （生成库文件的时候，应明确哪些文件须生成库）根据指定文件路径 生成库文件
# file(GLOB SRC_LIST1 ${CMAKE_CURRENT_SOURCE_DIR}/src/*.c)
# file(GLOB SRC_LIST2 ${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp)

#[[ 链接库文件（动态/静态库）
    参数解释：
        1. 参数 1：指定出要链接的静态库的名字
                * 可以是全名 libxxx.a
                * 也可以是掐头(lib) 去尾(.a)之后的名字 xxx
        2. 参数 2-N：要链接的其他静态库的名字
        *** 如果该静态库不是系统提供的（自己制作或者使用第三方提供的静态库）可能出现静态库找不到的情况
            此时可以将静态库的路径也指定出来
]]
# 链接 静态库
# link_libraries(calc)
#[[
    链接 动态库
    target: 指定要加载动态库的文件名字
                * 该文件可能是个源文件
                * 该文件可能是个动态库文件
                * 该文件可能是个可执行文件
    PRIVATE /PUBLIC /INTERFACE : 【动态库的访问权限】，默认为PUBLIC
            * 如果各个动态库之间没有依赖关系，无需做任何设置，三者没有区别，一般无需指定，默认使用PUBLIC 即可
            * 动态库的链接具有传递性， 如果动态库A链接了动态库B/C,动态库D链接了动态库A,此时动态库D相当于
               也链接了动态库B/C，并且可以使用动态库B/C中定义的方法
            PUBLIC: 在public后面的库会被Link 到前面的target种，并且里面的符号也会被导出，提供给第三方使用
           PRIVATE: 在private 后面的库仅被Link到前面的target中，并且终结掉，第三方不能感知你调用了什么库
         INTERFACE: 在interface 后面引入的库不会被链接到前面的target中，只会导出符号
]]
# (链接动态库，需放在生成可执行文件后面)可以给单独的库设置权限， 请注意传递性
# target_link_libraries(calc)
# 非系统库，如果无法识别到第三方生成的库，应明确路径
link_directories(${CMAKE_CURRENT_SOURCE_DIR}/lib1)
# 包含头文件
include_directories(${PROJECT_SOURCE_DIR}/include)

#[[
    日志:
        (无) ：重要消息
        STATUS ：非重要消息
        WARNING：CMake 警告, 会继续执行
        AUTHOR_WARNING：CMake 警告 (dev), 会继续执行
        SEND_ERROR：CMake 错误, 继续执行，但是会跳过生成的步骤
        FATAL_ERROR：CMake 错误, 终止所有处理过程
]]
# message(WARNING "CMake 警告, 会继续执行")
#[[
    变量操作
]]
# set(变量名 )

# 使用list拼接
# list(APPEND ${TEST1})
# set(tep hello word)
# set(tep1 ${tep} ${SRC_LIST1})
# message(${tep})
# message(${tep1})

#list 字符串追加测试
# message(${SRC_LIST1})
# list(APPEND temp "AAAA1" "BBBB1" ${SRC_LIST1})
# message(${temp})
# 删除 
# list(REMOVE_ITEM temp "AAAA1")
# list(REMOVE_ITEM temp ${PROJECT_SOURCE_DIR}/src/test.c)
# message(${temp})

# 宏定义，为项目打印日志时，在cmake中可控（实际项目中，日志打印消耗性能）
add_definitions(-DDEBUG)

# 对多个子jiCMakeLists.txt管理
add_subdirectory()


# set(SRC_LIST ${SRC_LIST2} ${SRC_LIST1})
# 定义工程生成一个可执行程序， 后面是源文件名
# add_executable(app ${SRC_LIST1})

# 2.在执行cmake命令的时候指定出这个宏的值
# cmake CMakeLists.txt路径 -DCMAKE_CXX_STANDARD=17

# 指定输出的路径，可以是可执行程序，也可以是 动态库
# 指定输出的可执行程序存放位置, 建议使用绝对路径（路径如果不存在，cmake会自动创建）
# set(HOME /home/laihualin)
# set(EXECUTABLE_OUTPUT_PATH /home/laihualin/)

#[[
    制作动态库或者静态库(动态是有执行权限的)
    在Linux中，静态库名字分为三部分：lib+库名字+.a,此处只需要指定出库的名字就可以,另外俩部分在生成该文件的时候会自动生成
    动态库：在linux 就是 .so, 在windows 就是 .dll; 
    静态库：在linux 就是 .a, 在windows 就是  .lib
]]
# shared 动态库 也叫共享库
# add_library(calc SHARED ${SRC_LIST1})
# STATIC 静态库(最终会被打包到可执行程序中)
# add_library(calc STATIC ${SRC_LIST1})

# 指定库文件生成路径，使用宏； 动态与静态库都是使用该宏
# set(LIBRARY_OUTPUT_PATH  /home/laihualin/c_cpp/lib1)


# 定义工程生成一个可执行程序， 后面是源文件名
add_executable(app ${SRC_LIST1})

# 可以给单独的库设置权限， 请注意传递性
# 必须在生成 可执行程序后，生成动态库链接
target_link_libraries(app calc)

