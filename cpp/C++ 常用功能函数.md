## 目录

- [读取文件内容](#读取文件内容)
  - [读取所有内容](#读取所有内容)
  - [一行一行读取](#一行一行读取)
- [写入文件内容](#写入文件内容)
  - [覆盖写入](#覆盖写入)
  - [尾部追加](#尾部追加)
  - [头部插入](#头部插入)
- [分割字符串](#分割字符串)
- [清空文件数据](#清空文件数据)
- [获取程序所在绝对路径](#获取程序所在绝对路径)
- [预处理器指令检查操作系统](#预处理器指令检查操作系统)
- [查看进程是否存在](#查看进程是否存在)

## 读取文件内容

### 读取所有内容

```c++
#include <fstream>
#include <sstream>
#include <string>

std::string readAllContent(const std::string &file)
{
    std::ifstream in;
    in.open(file);
    
    if (in.is_open())
    {
        std::stringstream stream;
        stream << in.rdbuf();
        return stream.str();
    }
    
    return std::string();
}
```

参考：<https://stackoverflow.com/questions/116038/what-is-the-best-way-to-read-an-entire-file-into-a-stdstring-in-c>

### 一行一行读取

```c++
#include <fstream>
#include <string>

void readLineByLine(const std::string &file)
{
    std::ifstream in;
    in.open(file);
    
    if (in.is_open())
    {
        std::string line;
        while (std::getline(in, line))
        {
            std::cout << line << std::endl;
        }
    }
}
```

参考：<https://stackoverflow.com/questions/7868936/read-file-line-by-line-using-ifstream-in-c>

## 写入文件内容

### 覆盖写入

```c++

```

### 尾部追加

```c++

```

### 头部插入

```c++

```

## 分割字符串

```c++
#include <string>
#include <vector>

/**
 * 字符串分割
 *
 * @param  str
 *         待分割的字符串
 *
 * @param  separator
 *         分隔符
 *
 * @param  skipEmpty
 *         保存时是否跳过空的字符串
 *
 * @return 分割好的字符串数组
 */
std::vector<std::string> SplitByChar(const std::string& str, char separator, bool skipEmpty = true)
{
    std::vector<std::string> strVector;

    std::istringstream stream(str);
    std::string temp;
    while (std::getline(stream, temp, separator))
    {
        if (skipEmpty && temp.empty())
            continue;

        strVector.push_back(temp);
    }

    return strVector;
}
```

参考：<https://stackoverflow.com/questions/14265581/parse-split-a-string-in-c-using-string-delimiter-standard-c>

## 清空文件数据

```c++
#include <fstream>

std::ofstream ofs;
ofs.open("test.txt", std::ofstream::out | std::ofstream::trunc);
```

参考：<https://stackoverflow.com/questions/17032970/clear-data-inside-text-file-in-c>

## 获取程序所在绝对路径

不管于何处调用，返回的都是该程序的绝对路径，而不是调用处的路径。

```c++
#include <unistd.h>

std::string getCurrentExePath()
{
    std::string currentPath;

    char path[1024] = {0};
    int count;
    count = readlink("/proc/self/exe", path, sizeof(path));
    if (count < 0 || count >= sizeof(path))
    {
        path[sizeof(path) - 1] = '\0';
        currentPath = path;
        return currentPath;
    }

    for (int i = count - 1; i >= 0; --i) // 只保留路径,去掉程序名
    {
        if (path[i] == '/')
        {
            path[i] = '\0';
            break;
        }
    }

    currentPath = path;

    return currentPath;
}
```

参考：

- <https://stackoverflow.com/questions/143174/how-do-i-get-the-directory-that-a-program-is-running-from>
- [C++ 17 std::filesystem::current_path](https://en.cppreference.com/w/cpp/filesystem/current_path)

## 预处理器指令检查操作系统

只适用 PC 类系统。

```c++
#if defined(_WIN32) || defined(__CYGWIN__)
    // Windows (x86 or x64)
    // ...
#elif defined(__linux__)
    // Linux
    // ...
#elif defined(__APPLE__) && defined(__MACH__)
    // Mac OS
    // ...
#elif defined(unix) || defined(__unix__) || defined(__unix)
    // Unix like OS
    // ...
#else
    #error Unknown environment!
#endif
```

参考：<https://stackoverflow.com/questions/142508/how-do-i-check-os-with-a-preprocessor-directive>

## 查看进程是否存在

```c++
#include <stdio.h>
#include <stdlib.h>

bool isProcessExisted(const std::string processName)
{
#if defined(__CYGWIN__)
    string cmd = "ps -W|grep " + processName + "|grep -v \"grep\"|wc -l";
#else
    string cmd = "ps |grep " + processName + "|grep -v \"grep\"|wc -l";
#endif

    FILE *fp = popen(cmd.c_str(), "r");
    if (fp)
    {
        char buf[16] = {0};
        fgets(buf, sizeof(buf), fp);
        pclose(fp);

        int ret = atoi(buf); // 存在的进程数
        if (ret >= 1)
            return true;
    }
    else
    {
    }

    return false;
}
```









