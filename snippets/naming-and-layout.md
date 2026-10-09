# C++ 命名与代码排版规范

> 目标：统一命名、减少歧义、提高代码可读性。
> 原则：**语义优先，一致性优先，格式交给工具。**

## 1. 命名总表

| 对象        | 规则                 | 示例                       |
| ----------- | -------------------- | -------------------------- |
| 类 / 结构体 | `PascalCase`         | `ChatServer`、`ThreadPool` |
| 枚举类型    | `PascalCase`         | `MsgType`                  |
| 枚举值      | `PascalCase`         | `MsgType::Login`           |
| 函数 / 方法 | `camelCase`          | `handleClient()`           |
| 普通变量    | `snake_case`         | `client_fd`                |
| 参数        | `snake_case`         | `buffer_size`              |
| 成员变量    | `snake_case_`        | `listen_fd_`               |
| 静态成员    | `s_` + `snake_case_` | `s_count_`                 |
| 全局变量    | `g_` + `snake_case`  | `g_running`                |
| 常量        | `kPascalCase`        | `kMaxSize`                 |
| 宏          | `UPPER_SNAKE_CASE`   | `CHAT_PORT`                |
| 类型别名    | `PascalCase`         | `Task`、`Callback`         |
| 命名空间    | 小写                 | `net`、`chat`              |
| 文件名      | `snake_case`         | `thread_pool.cpp`          |

## 2. 常用语义后缀

| 含义       | 推荐                 | 示例             |
| ---------- | -------------------- | ---------------- |
| 文件描述符 | `_fd`                | `client_fd`      |
| 长度       | `_len`               | `recv_len`       |
| 数量       | `_count`             | `client_count`   |
| 大小       | `_size`              | `buffer_size`    |
| 缓冲区     | `_buf` / `_buffer`   | `recv_buffer`    |
| 地址       | `_addr`              | `server_addr`    |
| 端口       | `_port`              | `server_port`    |
| 下标       | `_idx` / `_index`    | `client_idx`     |
| 偏移       | `_offset`            | `read_offset`    |
| 路径       | `_path`              | `config_path`    |
| 互斥锁     | `_mutex` / `_mtx`    | `clients_mutex_` |
| 条件变量   | `_cv`                | `task_cv_`       |
| 时间戳     | `_ts` / `_timestamp` | `start_ts`       |

### 布尔变量

优先：

```
is_
has_
can_
should_
```

示例：

```
is_running
has_data
can_retry
should_stop
```

## 3. 命名原则

### 推荐

```
client
connection
recv_buffer
task_queue
server_addr
```

### 避免

```
data
tmp
obj
a
x
```

### 容器

优先表达业务含义：

```
std::vector<Client> clients;
```

不强制：

```
clients_vec
```

### 指针

默认不把指针类型编码进名称：

```
std::shared_ptr<Connection> connection;
std::unique_ptr<Buffer> buffer;
Client* client;
```

不强制：

```
sp_connection
up_buffer
p_client
```

## 4. 类型别名

| 场景         | 规则                   | 示例           |
| ------------ | ---------------------- | -------------- |
| 普通别名     | `PascalCase`           | `Task`         |
| 回调         | `Callback` / `Handler` | `RecvCallback` |
| 智能指针别名 | `Ptr`                  | `ClientPtr`    |
| 容器别名     | 语义名                 | `ClientList`   |
| 返回类型     | 语义名                 | `Result`       |

```
using Task = std::function<void()>;
using RecvCallback = std::function<void(const Message&)>;
using ClientPtr = std::shared_ptr<Client>;
```

## 5. 枚举

优先：

```
enum class MsgType {
    Login,
    Logout,
    Message
};
```

使用：

```
MsgType::Login
```

避免：

```
MSG_LOGIN
```

除非是遗留代码或项目已有统一规范。

## 6. Include 顺序

```
1. 当前 .cpp 对应头文件
2. C 标准库
3. C++ 标准库
4. 第三方库
5. 项目内部头文件
```

示例：

```
#include "chat_server.h"

#include <cstdio>

#include <string>
#include <vector>

#include <some_library/api.h>

#include "common/logger.h"
```

不同分组之间空一行。

## 7. 头文件结构

```
头文件保护
    ↓
include
    ↓
常量 / 类型
    ↓
类 / 接口
```

示例：

```
#ifndef CHAT_SERVER_H
#define CHAT_SERVER_H

#include <string>

constexpr int kDefaultPort = 8080;

class ChatServer {
public:
    void start();

private:
    int listen_fd_;
};

#endif
```

项目也可以统一使用：

```
#pragma once
```

## 8. 源文件结构

```
自己的头文件
    ↓
其他 include
    ↓
文件内部实现
    ↓
对外实现
```

推荐：

```
#include "chat_server.h"

namespace {

void helperFunction() {
}

}

void ChatServer::start() {
}
```

文件内部辅助函数优先使用匿名命名空间。

## 9. 类内部结构

推荐顺序：

```
public
    构造 / 析构
    公共接口

private
    内部方法
    成员变量
```

示例：

```
class ChatServer {
public:
    ChatServer();
    ~ChatServer();

    void start();
    void stop();

private:
    void handleClient(int client_fd);

    int listen_fd_;
    bool is_running_;
};
```

> 先看“能做什么”，再看“怎么实现”。

## 10. 避坑

| 避免                          | 原因                     |
| ----------------------------- | ------------------------ |
| `__name`                      | 保留标识符               |
| `_Name`                       | 保留标识符               |
| `_name`                       | 避免进入复杂保留规则     |
| `xxx_t`                       | POSIX 环境中不建议自定义 |
| `tmp` / `data` / `obj`        | 语义过弱                 |
| 拼音缩写                      | 可读性差                 |
| `clientFd` / `client_fd` 混用 | 风格不统一               |
| 名称携带过多类型信息          | 实现变化时容易被迫改名   |

## 11. 格式化职责

| 内容     | 负责人         |
| -------- | -------------- |
| 命名     | 人             |
| 接口设计 | 人             |
| 代码组织 | 人             |
| 缩进     | `clang-format` |
| 空格     | `clang-format` |
| 换行     | `clang-format` |
| 括号布局 | `clang-format` |

## 12. 速查

```
ClassName           类 / 类型
functionName()      函数
local_variable      普通变量
member_variable_    成员变量
s_static_member_    静态成员
g_global_variable   全局变量
kConstantName       常量
MACRO_NAME          宏
namespace_name      命名空间
file_name.cpp       文件
头文件：
保护 → include → 常量 / 类型 → 接口

源文件：
自己的头 → 其他依赖 → 内部实现 → 对外实现

类：
public → private
```

> **名称表达语义，格式交给工具，项目内部保持一致。**