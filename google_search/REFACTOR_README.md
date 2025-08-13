# Google Search 模块重构说明

## 重构概述

原始的 `engine.py` 文件（1188行）已按功能拆分为多个更小、更易维护的模块。

## 模块结构

### 1. `fingerprint.py` (154行)
- **功能**: 浏览器指纹管理
- **包含**:
  - 设备配置定义
  - 宿主机配置获取
  - 设备配置选择
  - 随机延迟生成

### 2. `browser_manager.py` (263行)
- **功能**: 浏览器生命周期管理
- **包含**:
  - 浏览器启动
  - 上下文创建
  - 页面创建
  - 状态保存/加载
  - Google域名管理

### 3. `search_executor.py` (336行)
- **功能**: 搜索执行逻辑
- **包含**:
  - 搜索执行
  - 结果等待
  - 结果提取
  - 人机验证检测

### 4. `html_extractor.py` (211行)
- **功能**: HTML页面提取
- **包含**:
  - HTML内容获取
  - 内容清理
  - 文件保存
  - 截图功能

### 5. `engine.py` (339行)
- **功能**: 主引擎接口
- **包含**:
  - 主要API函数
  - 模块协调
  - 错误处理
  - 向后兼容性

## 重构优势

1. **可读性提升**: 每个模块职责单一，代码更易理解
2. **可维护性**: 修改特定功能时只需关注对应模块
3. **可测试性**: 各模块可以独立测试
4. **可扩展性**: 新增功能时可以创建新模块或扩展现有模块
5. **代码复用**: 通用功能可以在不同模块间共享

## 向后兼容性

- 所有公共API保持不变
- 外部调用方式完全一致
- 功能行为完全一致
- 只是内部实现被重构

## 模块依赖关系

```
engine.py (主引擎)
├── fingerprint.py (指纹管理)
├── browser_manager.py (浏览器管理)
├── search_executor.py (搜索执行)
└── html_extractor.py (HTML提取)
```

## 使用方式

重构后的使用方式与之前完全相同：

```python
from google_search.engine import google_search, get_google_search_page_html

# 执行搜索
results = await google_search("Python programming")

# 获取HTML
html_response = await get_google_search_page_html("Python programming")
```

## 注意事项

- 所有模块都保持了原有的错误处理逻辑
- 日志记录功能完全保留
- 配置选项和默认值保持不变
- 性能特性完全保留 