# ykb_copyer

医考帮爬虫，基于AutojsX获取app试题数据的自动化脚本

## 项目简介

本项目是一个用于从"医考帮"APP中自动抓取医学考试题目数据的工具集，包含自动化脚本、数据清理工具和格式转换工具。主要用于收集和整理医学考试题库，支持导出为JSON和Markdown格式。

### 主要功能

1. **自动化题目抓取**：使用AutojsX脚本自动从医考帮APP中提取试题数据
2. **数据清洗**：清理重复、空白和格式错误的题目数据
3. **格式转换**：将JSON格式的题目转换为Markdown格式，便于在Obsidian等笔记软件中使用
4. **题型标准化**：统一题目类型标记（A1、A2、A3、A4、B、X等）

### 项目能实现的效果

- ✅ 自动抓取医考帮APP中的试题（包括题目、选项、答案、解析、知识点等完整信息）
- ✅ 生成标准化的JSON格式题库数据
- ✅ 将题目转换为Markdown格式，支持在Obsidian中使用
- ✅ 自动去重，避免重复题目
- ✅ 数据清洗和质量控制
- ✅ 支持多种题型（单选、多选、A1-A4型题等）

## 试题仓库

- **路径**：`./tests_json_vault`
- **涵盖内容**：
    - 临床期末全科目试题
    - 考研真题（按章节科目分类）
- **数据格式**：JSON文件，每个文件包含一道题目的完整信息

### 题目数据结构

每个JSON文件包含以下字段：
```json
{
  "name": "2024-09-25-00-03-51-822",    // 题目唯一标识
  "cls": "生理学",                       // 科目
  "numb": "1",                          // 题号
  "unit": "第一章 绪论",                 // 章节
  "mode": "A1型题",                     // 题型
  "test": "题目内容...",                // 题目文本
  "option": ["A.选项1", "B.选项2"...],  // 选项列表
  "answer": "B",                        // 答案
  "point": "知识点出处...",             // 课本知识点
  "discuss": "详细解析..."              // 答案解析
}
```

## 使用指南

### 一、题目抓取

#### 环境配置

1. **模拟器**：MuMu模拟器（推荐）
2. **屏幕设置**：1440 × 2500，380 DPI
3. **脚本环境**：AutojsX
4. **VSCode插件**：Auto.js-Autox.js-VSCodeExt

#### 抓取步骤

1. 安装并配置MuMu模拟器，设置屏幕分辨率为1440×2500，DPI为380
2. 在模拟器中安装医考帮APP和AutojsX
3. 在VSCode中安装Auto.js插件，连接到模拟器
4. 在医考帮APP中打开需要抓取的试题界面
5. 在VSCode中打开 `ykb.js` 文件
6. 运行脚本开始自动抓取
7. 脚本会自动滚动页面并保存题目数据到JSON文件

#### 注意事项

- **入口文件**：`./ykb.js`
- **运行前提**：必须先进入具体的试题界面再启动脚本
- 脚本会自动识别题目类型、选项、答案等信息
- 抓取的数据会保存到 `tests` 目录（需要手动创建）

### 二、数据后处理

抓取完成后，使用以下Python脚本对数据进行清洗和转换：

#### 1. 删除空白文件 (`rm_empty.py`)

```bash
python rm_empty.py
```

**功能**：删除空的或无效的JSON文件

#### 2. 移除缺失字段的文件 (`rm_undefined.py`)

```bash
python rm_undefined.py
```

**功能**：将缺少 `mode` 字段的JSON文件移动到 `./undefined` 目录

#### 3. 清理JSON格式 (`cleanjson.py`)

```bash
python cleanjson.py
```

**功能**：清理JSON文件中的格式错误，只保留第一个有效的JSON对象

#### 4. 去重 (`dedupe.py`)

```bash
python dedupe.py
```

**功能**：根据题目内容（`test` 字段）去重，删除重复的题目文件

#### 5. 标准化题型 (`changemode.py`)

```bash
python changemode.py
```

**功能**：将题型字段统一转换为标准格式
- "A1型题" → "A1"
- "A2型题" → "A2"
- "单选题" → "A1"
- "多选题" → "X"
- 等等...

#### 6. 转换为Markdown (`tomd.py`)

```bash
python tomd.py
```

**功能**：将JSON格式的题目转换为Markdown格式，适合在Obsidian等笔记软件中使用

**输出格式示例**：
```markdown
---
class: 生理学
mode: A1
tags:
  - 医考帮
  - 习题
  - 第一章-绪论
---

# Q
题目内容...
A.选项1
B.选项2

# A
正确答案

# D
```ad-note
title:课本出处
collapse:false
知识点内容...
```

```ad-summary
title:解析
collapse:false
详细解析内容...
```
```

### 三、完整工作流程

推荐的完整使用流程：

```bash
# 1. 使用ykb.js抓取题目数据（在AutojsX中运行）

# 2. 删除空白文件
python rm_empty.py

# 3. 处理缺失字段的文件
python rm_undefined.py

# 4. 清理JSON格式
python cleanjson.py

# 5. 去重
python dedupe.py

# 6. 标准化题型
python changemode.py

# 7. 转换为Markdown（可选）
python tomd.py
```

## 高级功能

### Frida调试 (`tracer.py`)

如果需要深入分析医考帮APP的内部实现，可以使用Frida进行动态分析：

```bash
python tracer.py
```

**功能**：Hook医考帮APP中的TextView组件，追踪文本内容的显示

**前提条件**：
- 已安装Frida
- USB连接真实设备或模拟器
- 设备已Root

## 目录结构

```
ykb_copyer/
├── ykb.js              # AutojsX自动化抓取脚本
├── tests_json_vault/   # 题目数据仓库（JSON格式）
├── tests/              # 临时抓取目录
├── undefined/          # 缺失字段的题目文件
├── md/                 # Markdown格式输出目录
├── rm_empty.py         # 删除空白文件
├── rm_undefined.py     # 移除缺失字段文件
├── cleanjson.py        # 清理JSON格式
├── dedupe.py           # 去重工具
├── changemode.py       # 题型标准化
├── tomd.py             # JSON转Markdown
├── tracer.py           # Frida调试脚本
└── README.md           # 本文档
```

## 依赖环境

### AutojsX脚本环境
- MuMu模拟器或其他Android模拟器
- AutojsX
- VSCode + Auto.js插件

### Python环境
- Python 3.x
- 标准库（无需额外安装依赖）

### 可选依赖
- Frida（用于高级调试）

## 常见问题

**Q: 脚本运行后没有抓取到数据？**  
A: 请确保：
1. 已经打开医考帮APP并进入试题界面
2. 模拟器屏幕分辨率设置正确（1440×2500，380DPI）
3. AutojsX已获得必要的权限

**Q: JSON文件格式错误？**  
A: 运行 `cleanjson.py` 清理格式错误的文件

**Q: 有重复的题目？**  
A: 运行 `dedupe.py` 进行去重

**Q: 如何在Obsidian中使用？**  
A: 运行 `tomd.py` 将JSON转换为Markdown格式，然后将md文件夹内容复制到Obsidian vault中

## 免责声明

本项目仅供学习和研究使用，请勿用于商业用途。使用本工具抓取的数据仅供个人学习使用，请尊重原始数据的版权。

## 许可证

本项目仅供学习交流使用。
