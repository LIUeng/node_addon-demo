# 构建说明

## GitHub Actions 自动构建

本项目配置了 GitHub Actions 工作流，可以自动在多个平台上构建 Node.js 原生扩展。

### 支持的平台

- **Windows** (windows-latest)
- **macOS** (macos-latest) 
- **Linux** (ubuntu-latest)

### 支持的 Node.js 版本

- Node.js 18
- Node.js 20
- Node.js 22

### 触发条件

工作流会在以下情况下自动运行：

1. 推送到 `main` 或 `master` 分支
2. 创建 Pull Request 到 `main` 或 `master` 分支
3. 创建 Release

### 构建产物

- 每个平台和 Node.js 版本组合都会生成对应的 `.node` 文件
- 构建产物会作为 GitHub Actions Artifacts 上传，保留 30 天
- 在创建 Release 时，所有构建产物会自动打包并附加到 Release 中

### 本地构建

```bash
# 安装依赖
npm install

# 构建原生扩展
npm run build

# 运行测试
npm test

# 清理构建文件
npm run clean
```

### 构建要求

- **Windows**: 自动安装 windows-build-tools
- **Linux**: 自动安装 build-essential
- **macOS**: 使用系统自带的 Xcode 命令行工具

所有平台都需要 Node.js 和 npm 环境。