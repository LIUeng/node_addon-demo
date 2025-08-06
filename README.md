# Node.js C++ 插件开发指南

这是一个 Node.js C++ 插件示例项目，展示了如何使用 Node-API (N-API) 创建原生 C++ 扩展模块。

## 项目结构

```
addon-demo01/
├── src/
│   └── addon.cc          # C++ 源代码
├── __test__/
│   └── addon.js           # 测试文件
├── build/                 # 编译输出目录
├── binding.gyp            # 构建配置文件
├── package.json           # Node.js 项目配置
└── README.md              # 项目说明文档
```

## 功能说明

本插件提供了一个简单的加法函数 `add(a, b)`，用于演示 C++ 与 JavaScript 之间的数据交互。

## 环境要求

### 基础环境
- Node.js (版本 >= 14.0.0)
- npm 或 yarn
- Python (版本 2.7 或 3.x)

### 平台特定要求

#### Windows
- Visual Studio 2017 或更高版本（包含 C++ 构建工具）
- 或者 Visual Studio Build Tools
- Windows SDK

#### macOS
- Xcode Command Line Tools
```bash
xcode-select --install
```

#### Linux (Ubuntu/Debian)
```bash
sudo apt-get update
sudo apt-get install build-essential
sudo apt-get install python3-dev
```

#### Linux (CentOS/RHEL/Fedora)
```bash
# CentOS/RHEL
sudo yum groupinstall "Development Tools"
sudo yum install python3-devel

# Fedora
sudo dnf groupinstall "Development Tools"
sudo dnf install python3-devel
```

## 安装依赖

```bash
npm install
```

## 编译插件

### 方法一：使用 node-gyp（推荐）

首先全局安装 node-gyp：
```bash
npm install -g node-gyp
```

然后编译：
```bash
node-gyp configure
node-gyp build
```

### 方法二：使用 npm scripts

在 `package.json` 中添加构建脚本：
```json
{
  "scripts": {
    "build": "node-gyp rebuild",
    "clean": "node-gyp clean"
  }
}
```

然后运行：
```bash
npm run build
```

### 方法三：使用 cmake-js（替代方案）

如果遇到 node-gyp 问题，可以使用 cmake-js：
```bash
npm install -g cmake-js
cmake-js compile
```

## 跨平台编译注意事项

### Windows 特殊配置

如果使用 Visual Studio 2019 或更高版本：
```bash
npm config set msvs_version 2019
node-gyp configure --msvs_version=2019
node-gyp build
```

### 指定 Python 版本
```bash
# 全局配置
npm config set python /path/to/python

# 或者临时指定
node-gyp configure --python=/path/to/python
```

### 交叉编译

为不同架构编译：
```bash
# 为 ARM64 编译（在 x64 系统上）
node-gyp configure --target_arch=arm64
node-gyp build

# 为 x86 编译（在 x64 系统上）
node-gyp configure --target_arch=ia32
node-gyp build
```

## 使用示例

```javascript
const addon = require('./build/Release/addon.node');

// 调用 C++ 函数
const result = addon.add(5, 3);
console.log('5 + 3 =', result); // 输出: 5 + 3 = 8
```

## 测试

运行测试文件：
```bash
node __test__/addon.js
```

## 开发指南

### 1. 修改 C++ 代码

编辑 `src/addon.cc` 文件，添加新的函数或修改现有功能。

### 2. 更新 binding.gyp

如果添加了新的源文件，需要在 `binding.gyp` 中更新 `sources` 数组：
```json
{
  "targets": [
    {
      "target_name": "addon",
      "sources": [
        "src/addon.cc",
        "src/new_file.cc"  // 新增的源文件
      ]
    }
  ]
}
```

### 3. 重新编译

每次修改 C++ 代码后都需要重新编译：
```bash
node-gyp rebuild
```

### 4. 调试

#### Debug 模式编译
```bash
node-gyp configure --debug
node-gyp build
```

#### 使用 GDB (Linux/macOS)
```bash
gdb node
(gdb) run __test__/addon.js
```

#### 使用 LLDB (macOS)
```bash
lldb node
(lldb) run __test__/addon.js
```

## 常见问题

### 1. 编译失败

**问题**: `gyp ERR! stack Error: not found: make`

**解决**: 安装构建工具
- Windows: 安装 Visual Studio Build Tools
- macOS: `xcode-select --install`
- Linux: `sudo apt-get install build-essential`

### 2. Python 版本问题

**问题**: `gyp ERR! stack Error: Python executable "python" is v3.x.x`

**解决**: 指定 Python 2.7 路径或使用 Python 3.x 兼容的 node-gyp 版本
```bash
npm config set python /usr/bin/python2.7
```

### 3. Node.js 版本不兼容

**问题**: 编译的插件无法在不同 Node.js 版本间使用

**解决**: 为目标 Node.js 版本重新编译
```bash
node-gyp rebuild --target=16.14.0 --arch=x64
```

### 4. Windows 上的编码问题

**问题**: 中文路径或文件名导致编译失败

**解决**: 使用英文路径，或设置环境变量
```cmd
set PYTHONIOENCODING=utf-8
```

## 性能优化

### 1. 编译优化

在 `binding.gyp` 中添加优化选项：
```json
{
  "targets": [
    {
      "target_name": "addon",
      "cflags": ["-O3"],
      "cflags_cc": ["-O3"]
    }
  ]
}
```

### 2. 内存管理

使用 RAII 和智能指针避免内存泄漏：
```cpp
#include <memory>

std::unique_ptr<MyClass> ptr = std::make_unique<MyClass>();
```

## 部署

### 1. 预编译二进制文件

使用 `prebuild` 为多个平台预编译：
```bash
npm install -g prebuild
prebuild --all
```

### 2. 发布到 npm

```bash
npm publish
```

## 相关资源

- [Node-API 官方文档](https://nodejs.org/api/n-api.html)
- [node-addon-api GitHub](https://github.com/nodejs/node-addon-api)
- [node-gyp 文档](https://github.com/nodejs/node-gyp)
- [N-API 示例](https://github.com/nodejs/abi-stable-node-addon-examples)

## 许可证

ISC

## 贡献

欢迎提交 Issue 和 Pull Request！