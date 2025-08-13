# Google 搜索工具

这是一个基于 Playwright 的 Python 工具，能够绕过搜索引擎的反爬虫机制，执行 Google 搜索并提取结果。它可作为命令行工具直接使用，或通过 Model Context Protocol (MCP) 服务器为 Claude 等 AI 助手提供实时搜索能力。


[English](README.md)

## 核心亮点

- **本地化 SERP API 替代方案**：无需依赖付费的搜索引擎结果 API 服务，完全在本地执行搜索操作
- **先进的反机器人检测绕过技术**：
  - 智能浏览器指纹管理，模拟真实用户行为
  - 自动保存和恢复浏览器状态，减少验证频率
  - 无头/有头模式智能切换，遇到验证时自动转为有头模式让用户完成验证
  - 多种设备和区域设置随机化，降低被检测风险
- **原始HTML获取**：能够获取搜索结果页面的原始HTML（已移除CSS和JavaScript），用于分析和调试Google页面结构变化时的提取策略
- **网页截图功能**：在保存HTML内容的同时，自动捕获并保存完整网页截图
- **MCP 服务器集成**：为 Claude 等 AI 助手提供实时搜索能力，无需额外 API 密钥
- **完全开源免费**：所有代码开源，无使用限制，可自由定制和扩展
- **Python原生**：使用Python构建，提供更好的性能和更容易的部署

## 技术特性

- **使用 Python 3.8+ 开发**，提供优秀的性能和广泛的兼容性
- 基于 **Playwright** 实现浏览器自动化，支持多种浏览器引擎
- 支持命令行参数输入搜索关键词
- **MCP 服务器支持**，为 Claude 等 AI 助手提供搜索能力
- 返回搜索结果的标题、链接和摘要
- 支持获取搜索结果页面的原始HTML用于分析
- 以 JSON 格式输出结果
- 支持无头模式和有头模式（调试用）
- 提供详细的日志输出
- 健壮的错误处理机制
- 支持保存和恢复浏览器状态，有效避免反机器人检测
- **多层反爬虫保护机制**

## 安装

```bash
# 从源码安装
git clone https://github.com/iwanghc/mcp_web_search.git
cd mcp_web_search

# 安装Python依赖
pip install -r requirements.txt

# 安装Playwright浏览器
playwright install chromium
```

## 使用方法

### 命令行工具

```bash
# 直接使用命令行
python cli.py "搜索关键词"

# 使用命令行选项
python cli.py --limit 5 --timeout 30000 "搜索关键词"

# 获取搜索结果页面的原始HTML
python cli.py --get-html "搜索关键词"

# 获取HTML并保存到文件
python cli.py --get-html --save-html "搜索关键词"
```

### MCP服务器

```bash
# 在dotenv.env配置模型API KEY等信息
# 使用MCP客户端
python -m mcp_integration.client
```

#### 命令行选项

- `-l, --limit <number>`: 结果数量限制（默认：10）
- `-t, --timeout <number>`: 超时时间（毫秒，默认：30000）
- `--no-headless`: 显示浏览器界面（调试用）
- `--remote-debugging-port <number>`: 启用远程调试端口（默认：9222）
- `--state-file <path>`: 浏览器状态文件路径（默认：./browser-state.json）
- `--no-save-state`: 不保存浏览器状态
- `--get-html`: 获取搜索结果页面的原始HTML而不是解析结果
- `--save-html`: 将HTML保存到文件（与--get-html一起使用）
- `--html-output <path>`: 指定HTML输出文件路径（与--get-html和--save-html一起使用）
- `-V, --version`: 显示版本号
- `-h, --help`: 显示帮助信息

#### 输出示例

```json
{
  "query": "deepseek",
  "results": [
    {
      "title": "DeepSeek",
      "link": "https://www.deepseek.com/",
      "snippet": "DeepSeek-R1 is now live and open source, rivaling OpenAI's Model o1. Available on web, app, and API. Click for details. Into ..."
    },
    {
      "title": "DeepSeek",
      "link": "https://www.deepseek.com/",
      "snippet": "DeepSeek-R1 is now live and open source, rivaling OpenAI's Model o1. Available on web, app, and API. Click for details. Into ..."
    },
    {
      "title": "deepseek-ai/DeepSeek-V3",
      "link": "https://github.com/deepseek-ai/DeepSeek-V3",
      "snippet": "We present DeepSeek-V3, a strong Mixture-of-Experts (MoE) language model with 671B total parameters with 37B activated for each token."
    }
    // 更多结果...
  ]
}
```

#### HTML输出示例

使用`--get-html`选项时，输出将包含HTML内容的相关信息：

```json
{
  "query": "playwright automation",
  "url": "https://www.google.com/",
  "originalHtmlLength": 1291733,
  "cleanedHtmlLength": 456789,
  "htmlPreview": "<!DOCTYPE html><html itemscope=\"\" itemtype=\"http://schema.org/SearchResultsPage\" lang=\"zh-CN\"><head><meta charset=\"UTF-8\"><meta content=\"dark light\" name=\"color-scheme\"><meta content=\"origin\" name=\"referrer\">..."
}
```

如果同时使用`--save-html`选项，输出中还将包含HTML保存的文件路径：

```json
{
  "query": "playwright automation",
  "url": "https://www.google.com/",
  "originalHtmlLength": 1292241,
  "cleanedHtmlLength": 458976,
  "savedPath": "./google-search-html/playwright_automation-2025-04-06T03-30-06-852Z.html",
  "screenshotPath": "./google-search-html/playwright_automation-2025-04-06T03-30-06-852Z.png",
  "htmlPreview": "<!DOCTYPE html><html itemscope=\"\" itemtype=\"http://schema.org/SearchResultsPage\" lang=\"zh-CN\">..."
}
```

### MCP 服务器

本项目提供 Model Context Protocol (MCP) 服务器功能，让 Claude 等 AI 助手直接使用 Google 搜索能力。MCP 是一个开放协议，使 AI 助手能安全访问外部工具和数据。

#### 与 Claude Desktop 集成

1. 编辑 Claude Desktop 配置文件
   - Mac: `~/Library/Application Support/Claude/claude_desktop_config.json`
   - Windows: `%APPDATA%\Claude\claude_desktop_config.json`
     - 通常位于 `C:\Users\用户名\AppData\Roaming\Claude\claude_desktop_config.json`
     - 可以在 Windows 资源管理器地址栏输入 `%APPDATA%\Claude` 直接访问

2. 添加服务器配置并重启 Claude

```json
{
  "mcpServers": {
    "google-search": {
      "command": "python",
      "args": ["mcp_server_simple.py"]
    }
  }
}
```

集成后，可在 Claude 中直接使用搜索功能，如"搜索最新的 AI 研究"。

## 项目结构

```
google-search/
├── 核心功能/
│   ├── google_search/
│   │   ├── engine.py              # 搜索引擎实现
│   │   ├── browser_manager.py     # 浏览器管理和自动化
│   │   ├── search_executor.py     # 搜索执行逻辑
│   │   ├── html_extractor.py      # HTML解析和提取
│   │   ├── fingerprint.py         # 浏览器指纹管理
│   │   ├── utils.py               # 工具函数
│   │   └── __init__.py            # 包初始化
│   └── cli.py                     # 命令行接口
├── MCP集成/
│   ├── mcp_integration/
│   │   ├── server.py              # MCP服务器实现
│   │   ├── client.py              # MCP客户端实现
│   │   └── __init__.py            # 包初始化
├── 通用工具/
│   ├── common/
│   │   ├── logger.py              # 日志系统
│   │   ├── types.py               # 通用数据类型
│   │   └── __init__.py            # 包初始化
├── 配置和运行时/
│   ├── requirements.txt            # Python依赖
│   ├── dotenv.env                  # 环境变量
│   ├── browser-state.json          # 浏览器状态持久化
│   ├── browser-state-fingerprint.json # 浏览器指纹数据
│   └── .gitignore                  # Git忽略规则
├── 文档/
│   ├── README.md                   # 英文文档
│   ├── README.zh-CN.md             # 中文文档
│   └── google_search/REFACTOR_README.md # 重构说明
├── 开发/
│   ├── .vscode/                    # VS Code配置
│   └── logs/                       # 应用日志
└── 其他/
    └── __pycache__/                # Python缓存文件
```

## 技术栈

- **Python 3.8+**: 开发语言，提供优秀的性能和兼容性
- **Playwright**: 用于浏览器自动化，支持多种浏览器
- **MCP SDK**: 用于实现MCP服务器的开发工具
- **asyncio**: Python标准库的异步I/O
- **aiofiles**: 异步文件操作

## 开发指南

所有命令都可以在项目根目录下运行：

```bash
# 安装依赖
pip install -r requirements.txt

# 安装Playwright浏览器
playwright install chromium

# 运行CLI工具
python cli.py "搜索关键词"

# 启动MCP服务器
python mcp_server_simple.py

# 测试MCP客户端
python mcp_client_enhanced.py
```

## 错误处理

工具内置了健壮的错误处理机制：

- 浏览器启动失败时提供友好的错误信息
- 网络连接问题时自动返回错误状态
- 搜索结果解析失败时提供详细日志
- 超时情况下优雅退出并返回有用信息

## 注意事项

### 通用注意事项

- 本工具仅用于学习和研究目的
- 请遵守 Google 的使用条款和政策
- 不要过于频繁地发送请求，以避免被 Google 封锁
- 某些地区可能需要使用代理才能访问 Google
- Playwright 需要安装浏览器，首次使用时会自动下载

### 状态文件

- 状态文件包含浏览器 cookies 和存储数据，请妥善保管
- 使用状态文件可以有效避免 Google 的反机器人检测，提高搜索成功率

### MCP 服务器

- 使用 MCP 服务器时，请确保 Claude Desktop 已更新到最新版本
- 配置 Claude Desktop 时，请使用绝对路径指向 MCP 服务器文件

### Windows 环境特别注意事项

- 在 Windows 环境下，首次运行可能需要管理员权限安装 Playwright 浏览器
- 如果遇到权限问题，可以尝试以管理员身份运行命令提示符或 PowerShell
- Windows 防火墙可能会阻止 Playwright 浏览器的网络连接，请在提示时允许访问
- 浏览器状态文件默认保存在用户主目录下的 `.google-search-browser-state.json`
- 日志文件保存在系统临时目录下的 `google-search-logs` 文件夹中

## 与商业 SERP API 的对比

与付费的搜索引擎结果 API 服务（如 SerpAPI）相比，本项目提供了以下优势：

- **完全免费**：无需支付 API 调用费用
- **本地执行**：所有搜索在本地执行，无需依赖第三方服务
- **隐私保护**：搜索查询不会被第三方记录
- **可定制性**：完全开源，可根据需要修改和扩展功能
- **无使用限制**：不受 API 调用次数或频率限制
- **MCP 集成**：原生支持与 Claude 等 AI 助手集成

## 反爬虫保护

本项目实现了多层反爬虫保护：

### 客户端层面保护
- 请求间隔控制，随机延迟
- 工具调用超时保护
- 智能错误处理

### 服务器层面保护
- 请求频率限制
- 请求间随机延迟
- 全局请求计数和管理

### 搜索层面保护
- 浏览器指纹随机化
- 设备和区域随机化
- 浏览器状态管理
- 自动人机验证检测和处理

## 性能指标

- **响应时间**：通常5-15秒
- **成功率**：95%+（使用状态文件）
- **并发支持**：支持异步操作
- **内存使用**：优化，每次调用后清理
- **稳定性**：健壮的错误恢复和超时处理
