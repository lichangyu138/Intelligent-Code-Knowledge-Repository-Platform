# 智能代码知识沉淀平台

## 📋 目录

- [系统简介](#系统简介)
- [解决的核心问题](#解决的核心问题)
- [技术架构](#技术架构)
- [功能特性](#功能特性)
- [快速开始](#快速开始)
- [详细使用说明](#详细使用说明)
- [API接口文档](#api接口文档)
- [项目结构](#项目结构)
- [配置说明](#配置说明)
- [常见问题](#常见问题)

---

## 系统简介

**智能代码知识沉淀平台**是一个面向中小型研发团队（10-50人）的代码审查与知识管理系统。系统通过AI技术自动捕获代码审查过程中的知识，智能关联相关代码片段，并在后续开发中主动提供审查建议，帮助团队提升代码质量和开发效率。

### 核心价值

- ✅ **知识自动沉淀**：Code Review评论自动转化为结构化知识，沉淀率>80%
- ✅ **智能知识复用**：基于语义相似度匹配历史审查案例，复用率>60%
- ✅ **准实时响应**：15秒内返回智能审查建议
- ✅ **隐私保护**：完全本地化部署，代码数据不上传公网

---

## 解决的核心问题

### 痛点分析

在中小型研发团队的日常开发协作中，存在以下痛点：

1. **知识无法有效沉淀**
   - 现有Code Review工具（GitHub、GitLab）只保存评论，不做知识提取和关联
   - 审查经验散落在PR评论、文档、聊天记录中，难以检索和复用

2. **新人上手困难**
   - 新人无法快速了解"这段代码曾经被提过哪些问题"
   - 缺乏"团队对这类实现的最佳实践是什么"的上下文

3. **重复犯错**
   - 团队在同一个问题上重复犯错（如异步处理不当、SQL注入风险等）
   - 缺乏主动式的代码审查建议，依赖人工记忆

4. **数据隐私担忧**
   - 金融、医疗等敏感行业不能将代码上传到公网AI服务
   - 需要本地化或私有化部署方案

### 解决方案

本系统通过以下方式解决上述问题：

1. **自动知识提取**：AI自动将审查评论转化为结构化知识库条目
2. **智能关联匹配**：基于向量检索技术，自动匹配历史审查案例和最佳实践
3. **主动审查建议**：提交代码后自动获得基于团队历史经验的智能审查建议
4. **本地化部署**：使用Ollama本地模型，确保数据隐私安全

---

## 技术架构

### 整体架构

```
┌─────────────────────────────────────────────────────────────┐
│                        前端层 (Web UI)                        │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │  代码审查    │  │  知识库管理  │  │  数据看板    │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
                            │ HTTP/REST API
┌─────────────────────────────────────────────────────────────┐
│                        后端服务层 (FastAPI)                    │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │  代码解析    │  │  知识匹配    │  │  用户认证    │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │  AI审查引擎  │  │  知识图谱    │  │  数据统计    │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
                            │
┌─────────────────────────────────────────────────────────────┐
│                        AI服务层 (本地化)                       │
│  ┌──────────────┐  ┌──────────────┐                          │
│  │  Ollama服务  │  │  Embedding   │                          │
│  │  (gemma3:4b) │  │  (bge-m3)    │                          │
│  └──────────────┘  └──────────────┘                          │
└─────────────────────────────────────────────────────────────┘
                            │
┌─────────────────────────────────────────────────────────────┐
│                        数据存储层                              │
│  ┌──────────────┐  ┌──────────────┐                          │
│  │    MySQL     │  │   Milvus      │                          │
│  │  (关系数据)  │  │  (向量检索)  │                          │
│  └──────────────┘  └──────────────┘                          │
└─────────────────────────────────────────────────────────────┘
```

### 技术栈

#### 后端技术
- **框架**: FastAPI 0.104.1
- **服务器**: Uvicorn 0.24.0
- **ORM**: SQLAlchemy 2.0.23
- **数据库驱动**: PyMySQL 1.1.0

#### AI技术
- **LLM模型**: Ollama + gemma3:4b（代码审查）
- **Embedding模型**: Ollama + bge-m3:567m（向量生成）
- **向量数据库**: Milvus 2.3+（向量检索）

#### 数据库
- **关系数据库**: MySQL 8.0+（存储代码、知识、用户等关系数据）
- **向量数据库**: Milvus 2.3+（存储embedding向量，支持语义检索）

#### 前端技术
- **框架**: 原生HTML + JavaScript
- **UI**: 现代化响应式设计，支持移动端

#### 安全与认证
- **认证方式**: JWT Token
- **密码加密**: bcrypt
- **权限管理**: 基于角色的访问控制（RBAC）

---

## 功能特性

### 1. 代码审查功能

#### 1.1 AI智能审查
- **多语言支持**: Python、JavaScript、Java、Go、C++等
- **AST解析**: 自动解析代码结构，提取函数、类、导入等信息
- **多维度审查**: 
  - 安全性（SQL注入、XSS、权限控制等）
  - 性能（算法复杂度、资源泄漏等）
  - 代码规范（命名、结构、可读性等）
  - 最佳实践（设计模式、架构合理性等）
- **历史案例关联**: 自动匹配相似的历史审查案例，提供上下文参考

#### 1.2 代码上传方式
- **代码粘贴**: 直接粘贴代码进行审查
- **文件上传**: 支持上传代码文件（自动识别语言）

#### 1.3 审查历史查询
- 查看代码的历史问题和最佳实践
- 基于语义相似度匹配相关案例

### 2. 知识库管理

#### 2.1 知识条目管理
- **增删改查**: 完整的知识库CRUD操作
- **分类管理**: 支持安全性、性能、代码规范、最佳实践等分类
- **标签系统**: 支持多标签分类和检索
- **状态管理**: 草稿、待审核、已发布三种状态
- **分页显示**: 支持分页浏览，每页10条

#### 2.2 审核工作流
- **快速审核**: 审核员可一键批准或驳回待审核知识
- **审核备注**: 记录审核意见和驳回原因
- **权限控制**: 只有审核员（admin/reviewer/curator）可以审核

#### 2.3 知识自动提取
- **单条提取**: 将审查评论自动转化为知识库条目
- **批量提取**: 批量将历史审查评论转化为知识库
- **AI辅助**: 使用AI自动提取知识标题、内容、代码模式、最佳实践

#### 2.4 知识图谱可视化
- 可视化展示代码文件、审查评论、知识条目之间的关联关系
- 支持交互式浏览和探索

### 3. 数据统计分析

#### 3.1 数据看板
- **审查统计**: 总审查次数、发现问题数、知识库条目数、知识复用率
- **高频问题Top10**: 统计团队最常见的问题类型和严重程度
- **知识沉淀趋势**: 最近30天的知识增长趋势图表
- **严重度分布**: 高/中/低严重度问题的分布统计

#### 3.2 问题详情查询
- 点击高频问题可查看详细的问题列表
- 支持按问题类型和严重度筛选

### 4. 用户认证与权限

#### 4.1 用户角色
- **admin**: 管理员，拥有所有权限
- **reviewer**: 审核员，可以审核知识
- **curator**: 知识管理员，可以管理知识库
- **developer**: 开发者，可以提交代码审查和查看知识

#### 4.2 功能权限
- 不同角色看到不同的功能按钮
- 审核功能仅审核员可见
- 知识管理功能根据角色显示

### 5. 智能追问

- 基于审查建议进行AI对话
- 可以针对审查建议进行追问
- AI基于上下文提供详细回答

### 6. 操作日志

- 记录所有用户操作（审查、添加知识、审核等）
- 管理员可查看所有日志，普通用户只能查看自己的日志

---

## 快速开始

### 环境要求

- Python 3.10+
- MySQL 8.0+
- Milvus 2.3+
- Ollama（已安装gemma3:4b和bge-m3:567m模型）

### 1. 安装依赖

```bash
pip install -r requirements.txt
```

### 2. 配置环境变量

创建`.env`文件（或修改`config.py`中的默认值）：

```bash
# MySQL数据库配置
MYSQL_HOST=localhost
MYSQL_PORT=13306
MYSQL_USER=root
MYSQL_PASSWORD=SFBhdweREaKTm6XX
MYSQL_DATABASE=code_review_db

# Milvus向量数据库配置
MILVUS_HOST=localhost
MILVUS_PORT=19530

# Ollama配置
OLLAMA_HOST=localhost
OLLAMA_PORT=11434
OLLAMA_EMBEDDING_MODEL=bge-m3:567m
OLLAMA_LLM_MODEL=gemma3:4b

# 应用配置
APP_HOST=0.0.0.0
APP_PORT=8050
```

### 3. 初始化数据库

#### 方式一：使用Python脚本（推荐）

```bash
# 初始化MySQL表结构
python scripts/init_db.py

# 初始化Milvus集合（如果需要）
python scripts/init_milvus.py

# 数据库迁移（如果表结构有更新）
python scripts/migrate_db.py
```

#### 方式二：使用SQL脚本（手动导入）

如果需要在MySQL客户端中手动执行，可以使用SQL脚本：

```bash
# 完整初始化（推荐，包含数据库创建、表结构、默认数据）
mysql -u root -p < scripts/init_database.sql

# 或仅创建表结构（已有数据库时使用）
mysql -u root -p code_review_db < scripts/init_database_structure_only.sql

# 或仅插入默认数据（表结构已存在时使用）
mysql -u root -p code_review_db < scripts/init_default_data.sql
```

**SQL脚本说明：**
- `init_database.sql` - 完整初始化脚本（创建数据库+表结构+默认数据）
- `init_database_structure_only.sql` - 仅表结构脚本
- `init_default_data.sql` - 仅默认数据脚本

详细说明请参考：[scripts/README_SQL.md](scripts/README_SQL.md)

### 4. 启动应用

```bash
# 方式1: 直接运行
python main.py

# 方式2: 使用uvicorn（推荐）
uvicorn main:app --reload --host 0.0.0.0 --port 8050
```

### 5. 访问应用

- **前端页面**: http://localhost:8050
- **API文档**: http://localhost:8050/docs
- **健康检查**: http://localhost:8050/api/v1/health

### 6. 默认账号

- **用户名**: root
- **密码**: 123456
- **角色**: admin（拥有所有权限）

---

## 详细使用说明

### 用户A：资深开发者 - 代码审查与知识沉淀

#### 场景：在Code Review中发现问题，希望自动关联到类似代码模式

**操作步骤：**

1. **提交代码审查**
   - 登录系统（默认账号：root/123456）
   - 切换到"代码审查"标签页
   - 选择"粘贴代码"或"上传文件"
   - 输入代码，选择编程语言
   - 点击"开始审查"

2. **查看审查结果**
   - 系统自动分析代码，生成审查建议
   - 显示问题列表（类型、严重度、描述、修复建议）
   - 显示相关历史案例（自动匹配相似的历史审查）
   - 显示代码结构信息（AST解析结果）

3. **知识自动沉淀**
   - 系统自动将中高严重程度的问题保存为审查评论
   - 审查评论会自动生成embedding并存储到Milvus
   - 下次遇到类似代码时，会自动匹配到这些历史案例

4. **批量提取知识**（可选）
   - 切换到"知识库"标签页
   - 点击"批量提取知识"按钮（需要admin/reviewer/curator权限）
   - 系统自动将历史审查评论转化为知识库条目

### 用户B：新入职开发者 - 快速了解代码历史

#### 场景：接手老项目，想了解代码的历史问题和最佳实践

**操作步骤：**

1. **查询代码历史**
   - 登录系统
   - 切换到"历史查询"标签页
   - 粘贴或输入代码
   - 点击"查询历史"

2. **查看结果**
   - **历史问题**: 显示这段代码相关的历史审查评论
   - **最佳实践**: 显示相关的知识库条目
   - 每个结果都显示相似度评分

3. **查看详情**
   - 点击任意历史问题或最佳实践，查看详细内容
   - 了解问题的具体描述和修复建议

### 用户C：团队Leader - 知识沉淀与团队管理

#### 场景：将Code Review经验转化为团队知识库，提升整体代码质量

**操作步骤：**

1. **查看数据看板**
   - 登录系统（admin角色）
   - 切换到"数据看板"标签页
   - 查看：
     - 总审查次数、发现问题数、知识库条目数
     - 高频问题Top10
     - 知识沉淀趋势（最近30天）
     - 严重度问题分布

2. **批量提取知识**
   - 切换到"知识库"标签页
   - 点击"批量提取知识"按钮
   - 系统自动将历史审查评论转化为知识库条目
   - 查看提取结果（总计、成功、失败）

3. **审核知识**
   - 在知识列表中，找到"待审核"状态的知识
   - 点击"✓ 批准"按钮批准并发布
   - 或点击"✗ 驳回"按钮，输入驳回原因后驳回

4. **管理知识库**
   - 可以编辑、删除知识条目
   - 可以添加标签、分类
   - 可以查看知识图谱，了解知识之间的关联

### 日常使用流程

#### 1. 代码审查流程

```
开发者提交代码 
  → AI自动分析代码结构（AST解析）
  → 向量检索匹配历史案例
  → AI生成审查建议（基于历史案例）
  → 自动保存中高严重程度问题为审查评论
  → 15秒内返回结果
```

#### 2. 知识沉淀流程

```
审查评论生成
  → 自动生成embedding
  → 存储到Milvus向量数据库
  → 审核员批量提取知识
  → AI自动提取知识结构
  → 保存到知识库
  → 审核发布
```

#### 3. 知识复用流程

```
新代码提交
  → 生成代码embedding
  → 在Milvus中检索相似案例
  → 匹配历史审查评论和知识库条目
  → 作为上下文提供给AI
  → 生成更准确的审查建议
```

---

## API接口文档

### 认证相关

#### 用户登录
```http
POST /api/v1/auth/login
Content-Type: application/json

{
  "username": "root",
  "password": "123456"
}
```

**响应：**
```json
{
  "success": true,
  "access_token": "eyJ...",
  "token_type": "bearer",
  "user": {
    "id": 1,
    "username": "root",
    "role": "admin"
  }
}
```

#### 用户注册
```http
POST /api/v1/auth/register
Content-Type: application/json

{
  "username": "newuser",
  "password": "password123",
  "email": "user@example.com"
}
```

#### 获取当前用户信息
```http
GET /api/v1/auth/me
Authorization: Bearer {token}
```

### 代码审查

#### 提交代码审查
```http
POST /api/v1/code/review
Authorization: Bearer {token}
Content-Type: application/json

{
  "code": "def hello():\n    print('Hello')",
  "language": "python",
  "file_name": "hello.py"
}
```

**响应：**
```json
{
  "success": true,
  "data": {
    "review_id": 123,
    "file_id": 456,
    "issues": [
      {
        "type": "style",
        "severity": "low",
        "description": "建议添加函数文档字符串",
        "suggestion": "在函数定义后添加docstring",
        "code_snippet": "def hello():"
      }
    ],
    "related_cases": [...],
    "review_time_ms": 12000
  }
}
```

#### 上传文件审查
```http
POST /api/v1/code/upload
Authorization: Bearer {token}
Content-Type: multipart/form-data

file: [文件内容]
language: python (可选)
```

#### 查询代码历史
```http
POST /api/v1/code/history
Authorization: Bearer {token}
Content-Type: application/json

{
  "code": "代码内容",
  "top_k": 10
}
```

### 知识库管理

#### 获取知识列表（支持分页）
```http
GET /api/v1/knowledge?page=1&page_size=10&status=published&keyword=安全
Authorization: Bearer {token}
```

**响应：**
```json
{
  "success": true,
  "data": {
    "items": [...],
    "total": 100,
    "page": 1,
    "page_size": 10,
    "total_pages": 10
  }
}
```

#### 添加知识
```http
POST /api/v1/knowledge
Authorization: Bearer {token}
Content-Type: application/json

{
  "title": "SQL注入防护",
  "content": "使用参数化查询...",
  "category": "security",
  "code_pattern": "SELECT * FROM users WHERE id = {}",
  "best_practice": "使用参数化查询或ORM",
  "tags": ["SQL", "安全"]
}
```

#### 更新知识
```http
PUT /api/v1/knowledge/{knowledge_id}
Authorization: Bearer {token}
Content-Type: application/json

{
  "title": "更新后的标题",
  "status": "published"
}
```

#### 删除知识
```http
DELETE /api/v1/knowledge/{knowledge_id}
Authorization: Bearer {token}
```

#### 快速批准知识
```http
POST /api/v1/knowledge/{knowledge_id}/approve
Authorization: Bearer {token}
Content-Type: application/json

{
  "review_notes": "审核通过"
}
```

#### 快速驳回知识
```http
POST /api/v1/knowledge/{knowledge_id}/reject
Authorization: Bearer {token}
Content-Type: application/json

{
  "review_notes": "内容不够详细，需要补充示例"
}
```

#### 自动提取知识（单条）
```http
POST /api/v1/knowledge/auto-extract/{comment_id}
Authorization: Bearer {token}
```

#### 批量提取知识
```http
POST /api/v1/knowledge/batch-extract?min_severity=medium
Authorization: Bearer {token}
```

#### 获取知识图谱
```http
GET /api/v1/knowledge/graph?limit=30
Authorization: Bearer {token}
```

### 统计分析

#### 获取数据看板
```http
GET /api/v1/statistics/dashboard
Authorization: Bearer {token}
```

#### 获取高频问题TopN
```http
GET /api/v1/statistics/top-issues?limit=10
Authorization: Bearer {token}
```

#### 获取知识沉淀趋势
```http
GET /api/v1/statistics/knowledge-trend?days=30
Authorization: Bearer {token}
```

#### 获取审查统计
```http
GET /api/v1/statistics/review-stats
Authorization: Bearer {token}
```

#### 获取问题详情
```http
GET /api/v1/statistics/issue-details?issue_type=security&severity=high&limit=20
Authorization: Bearer {token}
```

### 智能追问

#### AI对话
```http
POST /api/v1/chat
Authorization: Bearer {token}
Content-Type: application/json

{
  "question": "为什么这个函数需要添加异常处理？",
  "context": "审查建议上下文",
  "review_id": 123
}
```

### 操作日志

#### 获取操作日志
```http
GET /api/v1/operation-logs?limit=50
Authorization: Bearer {token}
```

---

## 项目结构

```
.
├── main.py                      # FastAPI主应用，定义所有API路由
├── config.py                    # 配置文件，读取环境变量
├── database.py                  # 数据库模型定义和连接管理
├── auth.py                      # 用户认证和权限管理
├── services.py                  # 业务逻辑服务（代码审查、知识管理）
├── code_parser.py               # 代码解析模块（AST解析）
├── ollama_service.py           # Ollama服务封装（LLM和Embedding）
├── milvus_client.py             # Milvus向量数据库客户端
├── statistics_service.py        # 数据统计服务
├── operation_log.py             # 操作日志服务
├── index.html                   # 前端页面（单页应用）
├── requirements.txt             # Python依赖包
├── scripts/
│   ├── init_db.py                      # 初始化MySQL数据库表（Python脚本）
│   ├── init_milvus.py                  # 初始化Milvus集合
│   ├── create_database.py              # 创建数据库
│   ├── migrate_db.py                   # 数据库迁移脚本
│   ├── init_database.sql                # 完整数据库初始化SQL脚本
│   ├── init_database_structure_only.sql # 仅表结构SQL脚本
│   ├── init_default_data.sql            # 仅默认数据SQL脚本
│   └── README_SQL.md                    # SQL脚本使用说明
├── README.md                    # 项目说明文档（本文件）
├── [AI编程题]李长宇.md         # 原始需求文档
└── 技术方案实现文档.md         # 技术方案详细文档
```

### 核心模块说明

#### 1. main.py
- FastAPI应用入口
- 定义所有API路由
- 处理HTTP请求和响应
- 用户认证和权限检查

#### 2. services.py
- `CodeReviewService`: 代码审查服务
  - `review_code()`: 审查代码
  - `get_code_history()`: 获取代码历史
  - `save_review_comment()`: 保存审查评论
- `KnowledgeService`: 知识库服务
  - `add_knowledge()`: 添加知识
  - `get_all_knowledge()`: 获取知识列表（支持分页）
  - `update_knowledge()`: 更新知识
  - `auto_extract_knowledge_from_review()`: 自动提取知识
  - `batch_extract_knowledge()`: 批量提取知识

#### 3. code_parser.py
- 支持多语言AST解析（Python、JavaScript、Java、Go、C++）
- 提取代码结构（函数、类、导入等）
- 生成代码摘要

#### 4. ollama_service.py
- `get_embedding()`: 生成文本embedding向量
- `generate_code_review()`: 生成代码审查建议

#### 5. milvus_client.py
- 向量数据库连接管理
- 向量插入和检索
- 集合创建和管理

#### 6. database.py
- SQLAlchemy模型定义
- 数据库连接管理
- 表结构定义：
  - `users`: 用户表
  - `code_files`: 代码文件表
  - `review_comments`: 审查评论表
  - `knowledge_base`: 知识库表
  - `code_reviews`: 代码审查记录表
  - `operation_logs`: 操作日志表

---

## 配置说明

### 环境变量配置

创建`.env`文件或直接修改`config.py`：

```bash
# MySQL数据库配置
MYSQL_HOST=127.0.0.1          # MySQL服务器地址
MYSQL_PORT=13306              # MySQL端口
MYSQL_USER=root               # 数据库用户名
MYSQL_PASSWORD=SFBhdweREaKTm6XX  # 数据库密码
MYSQL_DATABASE=code_review_db # 数据库名

# Milvus向量数据库配置
MILVUS_HOST=127.0.0.1         # Milvus服务器地址
MILVUS_PORT=19530            # Milvus服务端口

# Ollama配置
OLLAMA_HOST=127.0.0.1     # Ollama服务器地址
OLLAMA_PORT=11434           # Ollama端口
OLLAMA_EMBEDDING_MODEL=bge-m3:567m  # Embedding模型
OLLAMA_LLM_MODEL=gemma3:4b   # LLM模型

# 应用配置
APP_HOST=0.0.0.0            # 应用监听地址
APP_PORT=8050               # 应用端口
DEBUG=True                  # 调试模式
```

### 数据库配置

#### MySQL配置
- 确保MySQL服务运行
- 创建数据库（或使用`scripts/create_database.py`自动创建）
- 运行`scripts/init_db.py`初始化表结构

#### Milvus配置
- 确保Milvus服务运行（默认端口19530）
- 运行`scripts/init_milvus.py`初始化集合（可选，系统会自动创建）

### Ollama配置

#### 安装Ollama
```bash
# 访问 https://ollama.ai 下载安装
# 或使用包管理器安装
```

#### 下载模型
```bash
# 下载Embedding模型
ollama pull bge-m3:567m

# 下载LLM模型
ollama pull gemma3:4b
```

#### 启动Ollama服务
```bash
ollama serve
```

---

## 常见问题

### 1. 启动报错：Ollama连接失败

**问题**: `TypeError: httpx.Client() got multiple values for keyword argument 'base_url'`

**解决**: 
- 检查Ollama服务是否运行：`curl http://localhost:11434/api/tags`
- 确认Ollama库版本，系统已自动适配不同版本的API

### 2. 数据库表缺失列

**问题**: `Unknown column 'knowledge_base.status' in 'field list'`

**解决**: 
```bash
python scripts/migrate_db.py
```

### 3. 知识列表加载失败

**问题**: 显示"加载失败，请稍后重试"

**解决**:
- 检查数据库连接
- 运行数据库迁移脚本
- 查看浏览器控制台和服务器日志

### 4. 批量提取知识失败

**问题**: 批量提取时出错

**解决**:
- 检查Ollama服务是否正常运行
- 查看服务器终端日志，了解具体错误
- 确保有足够的审查评论数据

### 5. 向量检索无结果

**问题**: 查询历史案例时没有匹配结果

**解决**:
- 确保Milvus服务正常运行
- 检查是否有历史审查评论数据
- 确认embedding模型已正确加载

### 6. 权限不足

**问题**: 某些功能按钮不显示或操作被拒绝

**解决**:
- 使用admin账号登录（root/123456）
- 或联系管理员分配相应角色权限

---

## 性能优化建议

### 1. 响应时间优化
- 使用量化模型减少推理时间
- 启用缓存机制（相同代码hash的审查结果）
- 批量处理多个代码片段

### 2. 数据库优化
- 为高频查询字段建立索引
- 使用连接池减少连接开销
- 定期清理历史数据

### 3. 向量检索优化
- 根据数据量调整Milvus索引参数
- 使用批量检索提升吞吐量
- 预加载常用集合到内存

---

## 安全建议

### 1. 生产环境配置
- 修改默认密码和JWT密钥
- 使用HTTPS加密传输
- 配置防火墙规则
- 定期备份数据库

### 2. 权限管理
- 合理分配用户角色
- 定期审查操作日志
- 限制敏感操作的权限

### 3. 数据保护
- 代码数据加密存储（可选）
- 访问控制审计
- 定期安全扫描

---

## 开发与扩展

### 添加新的编程语言支持

在`code_parser.py`中添加新的解析函数：

```python
def _parse_new_language(self, code: str) -> Dict[str, Any]:
    # 实现新语言的解析逻辑
    pass
```

### 自定义审查规则

修改`ollama_service.py`中的`_build_review_prompt()`方法，添加自定义审查规则。

### 扩展统计功能

在`statistics_service.py`中添加新的统计方法。

---

## 许可证

MIT License

---

## 联系方式

如有问题或建议，请提交Issue或联系开发团队。

---

**最后更新**: 2024年  
**版本**: v1.0.0


