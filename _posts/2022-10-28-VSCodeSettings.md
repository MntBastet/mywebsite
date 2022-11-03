---
title: VS Code Settings
tag:
- VS Code
---
### VS Code的一些运行配置。

---

# C/C++

---

## 【基本配置】
### 插件
> - C/C++
> - Code Runner

### 配置文件
#### launch.json 
```json
{
    // One of the key features of Visual Studio Code is its great debugging support.
    // VS Code's built-in debugger helps accelerate your edit, compile and debug loop.
    // VS Code keeps debugging configuration information in a launch.json file
    // located in a .vscode folder in your workspace (project root folder).
    "version": "0.2.0",
    "configurations": [
      {
        /* ------ these three options are mandatory ------ */
        // The type of debugger to use for this launch configuration.
        "type": "cppdbg", // C++ debug
        // The request type of this launch configuration. Currently, launch and attach are supported.
        //   If you come from a server or desktop background, 
        //   it's quite normal to have your editor launch your process for you, 
        //   and your editor automatically attaches its debugger to the newly launched process.
        //   A launch configuration starts your app in debug mode before VS Code attaches to it.
        // 大概意思是说，如果你开始debug的时候你的项目已经启起来了，那就attach（把debug的工具附加上去）
        // 如果你开始debug的时机和你启动项目的时机是相同的，那就launch
        "request": "launch", // debug的类型，launch表示启动，attach表示附加
        // The reader-friendly name to appear in the Debug launch configuration drop-down.
        "name": "C++ Debug", // 在VSCode侧边栏Run那里看到的名字（可以随便起）
        /* ------ some optional attributes available to all launch configurations ------ */
        // To launch a task before the start of a debug session, set this attribute to the label of a task specified in tasks.json.
        "preLaunchTask": "g++ compile", //在调试之前要进行的工作 compile是在 tasks.json 的编译任务里面的label
        /* ------ Many debuggers support some of the following attributes: ------ */
        // executable or file to run when launching the debugger
        // ！！不要在程序和代码的路径及文件名中出现空格！！否则无法调试（我尝试解决这个问题，但真的无法解决）
        "program": "${fileDirname}/${fileBasenameNoExtension}.out", // debug的对象(-g编译出来的二进制文件)，需要和.vscode/tasks.json中生成的可执行文件一致
        // arguments passed to the program to debug
        "args": [], // 比如运行你的程序添加输入参数（argc/argv），需要在这里添加
        // Environment variables to add to the environment for the program
        "environment": [], // 放置环境变量
        // current working directory for finding dependencies and other files
        "cwd": "${workspaceFolder}",
        // break immediately when the program launches
        "stopAtEntry": false,
        // If true, a console is launched for the debuggee.
        // If false, on Linux and Windows, it will appear in the Integrated Console.
        "externalConsole": true,
        // 为true则会打开系统终端在其中进行交互
        // 如果为 true，则为调试对象启动控制台。如果为 false，它在 Linux 和 Windows 上会显示在集成控制台中
        // macOS不适用：https://code.visualstudio.com/docs/cpp/launch-json-reference#_externalconsole
        /* ------ Customizing GDB or LLDB ------ */
        // Indicates the debugger that VS Code will connect to. Must be set to gdb or lldb. 
        // 但是macOS只安装了llbd（有可能是安装命令行工具的时候安装的），那就用lldb吧
        "MIMode": "lldb",
      }
    ]
}
```
#### settings.json
```json
{
    // 添加希望被忽略的文件,这样一些文件虽然存在于当前工作目录下,但是不会被显示在左侧的文件浏览器里
    "files.exclude": {
      // dSYM文件具有调试信息,普通使用的话不看到它就可以了
      "**/*.dSYM": true,
      "**/*.out": true,
    },
    // --------------------------------------------------------------------------------------
    // Code Runner
    // To run code:
    //   use shortcut "Ctrl Opt N" *
    //   or press F1 and then select/type Run Code,
    //   or right click the Text Editor and then click Run Code in editor context menu
    //   or click Run Code button in editor title menu
    //   or click Run Code button in context menu of file explorer
    // To stop the running code:
    //   use shortcut "Ctrl Opt M" *
    //   or press F1 and then select/type Stop Code Run
    //   or right click the Output Channel and then click Stop Code Run in context menu
    "code-runner.executorMap": {
      // Introduction:
      //   Make sure the executor PATH of each language is set in the environment variable.
      //   You could also add entry into "code-runner.executorMap" to set the executor PATH.
      // Supported customized parameters:
      //   $workspaceRoot: The path of the folder opened in VS Code
      //   $dir: The directory of the code file being run
      //   $fullFileName: The full name of the code file being run
      //   $fileName: The base name of the code file being run, that is the file without the directory
      //   $fileNameWithoutExt: The base name of the code file being run without its extension
      /* ------ 编译、运行只有一个文件的cpp文件 ------ */
      // 注：路径中有空格不会出现问题
      "cpp": "g++ $fullFileName -o $dir\"$fileNameWithoutExt\"\".out\" -W -Wall -O2 -std=c++17 && $dir\"$fileNameWithoutExt\"\".out\"",
      // 其中 $fullFileName 是绝对路径，是主文件
      // 自己决定是否加入 && rm $dir\"$fileNameWithoutExt\"\".out\"（也可以添加"files.exclude"）
      /* ------ 编译、运行多个cpp文件 ------ */
      // "cpp": "g++ $fullFileName <file_to_link> -o $dir\"$fileNameWithoutExt\"\".out\" -W -Wall -O2 -std=c++17 && $dir\"$fileNameWithoutExt\"\".out\"",
      // <file_to_link>的写法：
      //   一般的，你也可以直接写绝对路径
      //     \"/path/xxxx.cpp\"
      //   如果你链接的cpp文件和主文件在一个目录下：
      //     $dir\"xxxx.cpp\"
      //   更一般的，如果你链接的cpp文件不和主文件在一个目录下，需要从当前VSCode的工作目录补充相对路径从而形成绝对路径：
      //     $workspaceRoot\"relative/path/xxxx.cpp\"
      /* ------ 编译c文件 ------ */
      "c": "gcc $fullFileName -o $dir\"$fileNameWithoutExt\"\".out\" -W -Wall -O2 -std=c17 && $dir\"$fileNameWithoutExt\"\".out\"",
      // "c": "gcc $fullFileName <file_to_link> -o $dir\"$fileNameWithoutExt\"\".out\" -W -Wall -O2 -std=c17 && $dir\"$fileNameWithoutExt\"\".out\"",
    },
    // Whether to clear previous output before each run (default is false):
    "code-runner.clearPreviousOutput": true,
    // Whether to save all files before running (default is false):
    "code-runner.saveAllFilesBeforeRun": false,
    // Whether to save the current file before running (default is false):
    "code-runner.saveFileBeforeRun": true,
    // Whether to show extra execution message like [Running] ... and [Done] ... (default is true):
    "code-runner.showExecutionMessage": true, // cannot see that message is you set "code-runner.runInTerminal" to true
    // Whether to run code in Integrated Terminal (only support to run whole file in Integrated Terminal, neither untitled file nor code snippet) (default is false):
    "code-runner.runInTerminal": true, // cannot input data when setting to false
    // Whether to preserve focus on code editor after code run is triggered (default is true, the code editor will keep focus; when it is false, Terminal or Output Channel will take focus):
    "code-runner.preserveFocus": false,
    // Whether to ignore selection to always run entire file. (Default is false)
    "code-runner.ignoreSelection": true,
    "files.associations": {
      "ostream": "cpp"
    },
    // --------------------------------------------------------------------------------------
}
```
#### tasks.json
```json
{
    // Tasks in VS Code can be configured to run scripts and start processes
    // so that many of these existing tools can be used from within VS Code 
    // without having to enter a command line or write new code.
    // Workspace or folder specific tasks are configured from the tasks.json file in the .vscode folder for a workspace.
    "version": "2.0.0",
    "tasks": [
      {
        // The task's label used in the user interface.
        // Terminal -> Run Task... 看到的名字
        "label": "g++ compile",
        // The task's type. For a custom task, this can either be shell or process.
        // If shell is specified, the command is interpreted as a shell command (for example: bash, cmd, or PowerShell).
        // If process is specified, the command is interpreted as a process to execute.
        "type": "shell",// shell: 输入命令
        // The actual command to execute.
        // 因为g++已经在环境变量中了，所以我们这里写命令就行不用写g++的绝对路径
        "command": "g++",
        "args": [
          "${file}", // 表示当前文件（绝对路径）
          // 在这里添加你还需要链接的.cpp文件
          "-o",
          "${fileDirname}/${fileBasenameNoExtension}.out",
          "-W",
          "-Wall",
          "-g",
          "-std=c++17",
        ],
        // Defines to which execution group this task belongs to.
        // It supports "build" to add it to the build group and "test" to add it to the test group.
        // Tasks that belong to the build/test group can be executed by running Run Build/Test Task from the Command Palette (sft cmd P).
        // Valid values:
        //   "build",
        //   {"kind":"build","isDefault":true}, 
        //   "test",
        //   {"kind":"test","isDefault":true}, 
        //   "none".
        "group": {
          "kind": "build",
          "isDefault": true, // Defines if this task is the default task in the group.
        },
        // Configures the panel that is used to present the task's output and reads its input.
        "presentation": {
          // Controls whether the executed command is echoed to the panel. Default is true.
          "echo": true, // 打开可以看到编译的命令，把命令本身输出一次
          // Controls whether the terminal running the task is revealed or not. Default is "always".
          //   always: Always reveals the terminal when this task is executed.
          //   silent: Only reveals the terminal if the task exits with an error or the problem matcher finds an error.(会显示错误，但不会显示警告)
          //   never: Never reveals the terminal when this task is executed.
          "reveal": "silent", // 控制在集成终端中是否显示。如果没问题那我不希望终端被切换、如果有问题我希望能看到编译过程哪里出错，所以选silent(可能always会好一些)
          // Controls whether the panel takes focus. Default is false.
          "focus": false, // 我的理解是：是否将鼠标移过去。因为这个是编译任务，我们不需要输入什么东西，所以选false
          // Controls if the panel is shared between tasks, dedicated to this task or a new one is created on every run.
          "panel": "shared", // shared:不同任务的输出使用同一个终端panel（为了少生成几个panel我们选shared）
          // Controls whether to show the `Terminal will be reused by tasks, press any key to close it` message.
          "showReuseMessage": true, // 就一句话，你想看就true，不想看就false
          // Controls whether the terminal is cleared before executing the task.
          "clear": false, // 还是保留之前的task输出信息比较好。所以不清理
        },
        // Other two choices: options & runOptions (cmd I to use IntelliSense)
        "options": {
          // The current working directory of the executed program or script. If omitted Code's current workspace root is used.
          "cwd": "${workspaceFolder}",// 默认就是这个，删掉也没问题
        },
        // problemMatcher: 用正则表达式提取g++的输出中的错误信息并将其显示到VS Code下方的Problems窗口
        // check: https://code.visualstudio.com/docs/editor/tasks#_defining-a-problem-matcher
        "problemMatcher": {
          "owner": "cpp",
          "fileLocation": "absolute",
          "pattern": {
            "regexp": "^(.*):(\\d+):(\\d+):\\s+(warning|error):\\s+(.*)$",
            "file": 1,
            "line": 2,
            "column": 3,
            "severity": 4,
            "message": 5,
          },
        },
        // 官网教程 https://code.visualstudio.com/docs/cpp/config-clang-mac#_build-helloworldcpp 
        // 提到了另一种problemMatcher，但试了之后好像不起作用，甚至还把我原本的电脑搞出了一些问题……
      },
      {
        "label": "Open Terminal.app",
        "type": "shell",
        "command": "osascript -e 'tell application \"Terminal\"\ndo script \"echo now VS Code is able to open Terminal.app\"\nend tell'",
        "problemMatcher": [],
        "group": "none",
      }
    ]
}
```
### 备注
- .c/.cpp运行时需保证与以上三个.json处于同一文件夹内   
- VS Code调试.c/.cpp时打开的文件夹中应包含以上.json
- 可将.json打包在一个文件夹.vscode中
- 插件和.json的配置先后没有要求

---

## 【进阶配置】
### 配置文件
#### 头文件 c_cpp_properties.json
```json
{
    "configurations": [
        {
            "name": "Mac",
            "includePath": [
                "${workspaceFolder}/**"
            ],
            "defines": [],
            "macFrameworkPath": [ //在此处添加头文件路径
                "/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/System/Library/Frameworks"
            ],
            "compilerPath": "/usr/bin/clang",
            "cStandard": "c17",
            "cppStandard": "c++17",
            "intelliSenseMode": "macos-clang-arm64"
        }
    ],
    "version": 4
}
```
##### or
```
 F1 C/C++:Edit Configurations(JSON)
```

---

# HTML/CSS/JavaScript
### 插件
> open in browser

---

# Markdown
### 插件
> Markdown All in One