# Data Agent - 文档数据处理代理

## 项目简介

Data Agent 是一个基于 FastAPI 的文档数据处理系统，专门用于处理对文档资料数据的收集、清理、结构化/非结构化入库。

## 主要功能

1. **数据接收** - 支持多种文档格式的上传和接收
2. **数据解析** - 结构化提取、表格识别、OCR 等
3. **数据清洗** - 去重、标准化、格式转换
4. **数据路由** - 智能数据分发和路由
5. **分类处理** - 财报数据、量化公式、合同条款等专业分类
6. **向量化嵌入** - 文档内容向量化处理
7. **存储管理** - 向量库、结构化库存储
8. **API 接口** - 完整的 RESTful API 服务

## 主要工作流程

```
数据接收 → 数据解析 → 数据清洗 → 数据路由 → 分类处理 → 向量化嵌入 → 存储
    1   →    2    →    3    →    4    →    5     →      6      →   7
```

## 技术栈

- **Web 框架**: FastAPI
- **文档处理**: PyPDF2, python-docx, openpyxl, pandas
- **OCR**: pytesseract, opencv-python
- **表格识别**: tabula-py
- **向量化**: sentence-transformers
- **向量存储**: ChromaDB, FAISS
- **数据库**: MongoDB
- **缓存**: Redis
- **异步处理**: Celery
- **中文处理**: jieba

## 项目结构

```
data-agent/
├── src/
│   └── data_agent/
│       ├── __init__.py
│       ├── main.py              # FastAPI 主应用
│       ├── api/
│       │   ├── __init__.py
│       │   └── routers.py       # API 路由定义
│       ├── core/
│       │   ├── __init__.py
│       │   └── config.py        # 配置管理
│       ├── processors/
│       │   ├── __init__.py
│       │   ├── ingestion.py     # 数据接收处理
│       │   └── parsing.py       # 文档解析处理
│       └── storage/
│           ├── __init__.py
│           └── vector_store.py  # 向量存储管理
├── frontend/                    # 前端实现
│   ├── __init__.py             # 前端包初始化
│   ├── ARCHITECTURE.md         # 前端架构设计文档
│   ├── streamlit/              # Streamlit 前端实现
│   │   ├── __init__.py         # Streamlit 包初始化
│   │   ├── components/         # 可复用 UI 组件
│   │   │   ├── __init__.py     # 组件包初始化
│   │   │   ├── upload.py       # 文件上传组件
│   │   │   ├── results.py      # 结果展示组件
│   │   │   └── config.py       # 配置管理组件
│   │   ├── pages/              # 页面管理
│   │   │   ├── __init__.py     # 页面包初始化
│   │   │   └── manager.py      # 页面管理器和路由
│   │   ├── utils/              # 工具函数
│   │   │   ├── __init__.py     # 工具包初始化
│   │   │   └── api_client.py   # API 客户端
│   │   └── styles/             # 样式主题
│   │       ├── __init__.py     # 样式包初始化
│   │       └── theme.py        # 主题配置和 CSS
│   └── web/                    # 未来 Web 前端扩展
├── tests/                       # 测试文件
├── docs/                        # 文档
├── config/                      # 配置文件
├── uploads/                     # 文件上传目录
├── logs/                        # 日志文件目录
├── chroma_db/                   # ChromaDB 向量存储
├── static/                      # 静态文件
├── templates/                   # HTML 模板
├── streamlit_app.py            # 原版 Streamlit 应用
├── streamlit_app_modular.py    # 模块化 Streamlit 应用
├── requirements.txt             # 依赖包
├── requirements-basic.txt       # 基础依赖包
├── pyproject.toml              # 项目配置
└── README.md                   # 项目说明
```

## 核心模块功能说明

### 1. 主应用模块 (`main.py`)
- **FastAPI 应用初始化**: 创建 FastAPI 实例，配置 CORS 中间件
- **路由注册**: 注册健康检查和数据处理 API 路由
- **启动/关闭事件**: 处理应用启动和关闭时的初始化和清理工作
- **服务器配置**: 支持开发模式和生产模式的服务器配置

### 2. 配置管理模块 (`core/config.py`)
- **环境配置**: 基于 Pydantic Settings 的配置管理
- **数据库配置**: MongoDB、Redis、ChromaDB 连接配置
- **文件处理配置**: 上传目录、文件大小限制、支持格式配置
- **OCR 配置**: Tesseract OCR 路径和语言配置
- **模型配置**: 嵌入模型和向量化相关配置
- **自动目录创建**: 自动创建必要的工作目录

### 3. API 路由模块 (`api/routers.py`)
- **健康检查端点**: 提供系统健康状态和服务状态检查
- **数据处理端点**: 定义完整的数据处理工作流 API
  - `/data/ingest` - 数据接收
  - `/data/parse` - 数据解析
  - `/data/clean` - 数据清洗
  - `/data/classify` - 数据分类
  - `/data/vectorize` - 数据向量化
  - `/data/store` - 数据存储

### 4. 数据接收处理器 (`processors/ingestion.py`)
- **文件验证**: 文件大小、格式、内容类型验证
- **安全上传**: 基于哈希的文件去重和安全存储
- **批量处理**: 支持多文件同时上传和处理
- **元数据提取**: 自动提取文件基本信息和元数据
- **异步处理**: 基于 aiofiles 的异步文件操作
- **错误处理**: 完整的错误捕获和用户友好的错误信息

### 5. 文档解析处理器 (`processors/parsing.py`)
- **多格式支持**: 
  - **PDF 解析**: 文本提取、表格识别、OCR 处理
  - **Word 解析**: 段落提取、表格数据处理
  - **Excel 解析**: 多工作表处理、数据统计分析
  - **CSV 解析**: 结构化数据处理和验证
  - **图像 OCR**: 中英文 OCR 识别、图像预处理
  - **文本解析**: 纯文本文件处理和统计
- **智能预处理**: 图像去噪、二值化、OCR 准确性优化
- **表格识别**: 基于 tabula-py 的 PDF 表格自动提取
- **内容结构化**: 将非结构化内容转换为结构化数据格式

### 6. 前端界面模块 (`frontend/`)
- **模块化架构**: 采用组件化设计，代码结构清晰易维护
- **Streamlit 实现**: 基于 Streamlit 的现代化 Web 界面
- **多页面管理**: 
  - **文档上传页面**: 支持拖拽上传、进度跟踪、批量处理
  - **处理结果页面**: 可视化展示文档处理结果和统计信息
  - **统计分析页面**: 数据图表分析、文件类型分布、处理状态统计
  - **系统配置页面**: 上传设置、存储配置、处理参数管理
- **可复用组件**: 
  - **上传组件**: 文件验证、进度显示、帮助说明
  - **结果组件**: 结果卡片、错误处理、数据可视化
  - **配置组件**: 参数设置、连接测试、配置保存
- **主题系统**: 统一的颜色配置、CSS 样式、响应式设计
- **API 集成**: 完整的后端 API 调用和状态管理
- **实时监控**: 系统状态监控、服务健康检查、处理进度跟踪

## 技术特性

### 异步处理架构
- **FastAPI 异步支持**: 全异步 API 设计，支持高并发请求
- **异步文件操作**: 使用 aiofiles 进行非阻塞文件读写
- **异步数据库操作**: 支持 MongoDB Motor 异步驱动

### 智能文档处理
- **多语言 OCR**: 支持中英文混合识别，基于 Tesseract 引擎
- **表格智能识别**: 自动检测和提取 PDF 中的表格数据
- **图像预处理**: 降噪、二值化等图像优化技术提升 OCR 准确率
- **内容分类**: 支持财报、合同、量化公式等专业文档分类

### 数据安全与验证
- **文件哈希验证**: MD5 哈希确保文件完整性和去重
- **格式验证**: 严格的文件格式和大小限制
- **安全存储**: 基于哈希的文件命名避免冲突

### 配置管理
- **环境变量支持**: 基于 .env 文件的配置管理
- **Pydantic 验证**: 配置参数类型验证和默认值设置
- **自动初始化**: 启动时自动创建必要的目录结构

### 现代化前端界面
- **响应式设计**: 支持桌面和移动端访问，自适应布局
- **模块化组件**: 可复用的 UI 组件，便于维护和扩展
- **实时交互**: 文件上传进度、处理状态实时更新
- **数据可视化**: 基于 Plotly 的交互式图表和统计分析
- **主题定制**: 统一的色彩系统和可定制的界面风格
- **多页面路由**: 侧边栏导航、页面状态管理、快速状态监控

## 详细工作流程

### 1. 数据接收流程 (Data Ingestion)
```
文件上传 → 格式验证 → 大小检查 → 哈希计算 → 去重处理 → 安全存储 → 元数据记录
```

### 2. 数据解析流程 (Data Parsing)
```
文件类型识别 → 选择解析器 → 内容提取 → 结构化处理 → 元数据生成 → 结果返回
```

### 3. 数据清洗流程 (Data Cleaning)
```
数据验证 → 格式标准化 → 重复数据处理 → 数据质量评估 → 清洗结果输出
```

### 4. 数据分类流程 (Data Classification)
```
内容特征提取 → 模式匹配 → 分类算法应用 → 置信度评估 → 分类结果输出
```

### 5. 向量化流程 (Vectorization)
```
文本预处理 → 分句分段 → 嵌入模型处理 → 向量生成 → 维度优化 → 向量存储
```

### 6. 存储流程 (Data Storage)
```
数据格式确认 → 存储策略选择 → 索引创建 → 数据写入 → 备份处理 → 存储确认
```

## 安装和运行

### 环境要求
- Python 3.9+
- 虚拟环境 (推荐)

### 1. 克隆项目并设置虚拟环境

```bash
# 创建虚拟环境
python -m venv .venv

# 激活虚拟环境 (Windows)
.venv\Scripts\Activate.ps1

# 激活虚拟环境 (Linux/Mac)
source .venv/bin/activate
```

### 2. 安装依赖

```bash
# 安装基本依赖 (推荐先安装)
pip install -r requirements-basic.txt

# 安装完整依赖 (包含数据处理库)
pip install -r requirements.txt

# 或使用 pip 从 pyproject.toml 安装
pip install -e .
```

### 3. 配置环境

复制环境配置文件并根据需要修改：

```bash
# 复制配置文件
cp .env.example .env
```

编辑 `.env` 文件：

```env
# 基本配置
DEBUG=true
HOST=0.0.0.0
PORT=8000

# 数据库配置
MONGODB_URL=mongodb://localhost:27017
DATABASE_NAME=data_agent

# Redis 配置
REDIS_URL=redis://localhost:6379

# 向量数据库配置
CHROMA_PERSIST_DIRECTORY=./chroma_db

# 文件上传配置
UPLOAD_DIRECTORY=./uploads
MAX_FILE_SIZE=52428800  # 50MB

# OCR 配置（Windows 示例）
TESSERACT_PATH=C:\Program Files\Tesseract-OCR\tesseract.exe

# 模型配置
EMBEDDING_MODEL=sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2

# 日志配置
LOG_LEVEL=INFO
LOG_FILE=./logs/data_agent.log
```

### 4. 启动服务

#### 后端服务 (FastAPI)

```bash
# 方式1: 直接启动 (开发模式)
python src/data_agent/main.py

# 方式2: 使用 uvicorn 启动
uvicorn src.data_agent.main:app --reload --host 0.0.0.0 --port 8000

# 方式3: 使用 VS Code 任务启动
# 在 VS Code 中按 Ctrl+Shift+P，选择 "Tasks: Run Task"，然后选择 "启动 Data Agent 服务器"
```

#### 前端界面 (Streamlit)

```bash
# 启动模块化 Streamlit 前端 (推荐)
streamlit run streamlit_app_modular.py --server.port 8501

# 启动原版 Streamlit 前端 (向后兼容)
streamlit run streamlit_app.py --server.port 8501
```

#### 完整启动流程

```bash
# 1. 激活虚拟环境
.venv\Scripts\Activate.ps1  # Windows
# source .venv/bin/activate  # Linux/Mac

# 2. 启动后端服务 (终端1)
uvicorn src.data_agent.main:app --reload --host 127.0.0.1 --port 8000

# 3. 启动前端界面 (终端2)
streamlit run streamlit_app_modular.py --server.port 8501
```

### 5. 访问应用

#### 后端 API 文档
- **Swagger UI**: http://localhost:8000/docs
- **ReDoc**: http://localhost:8000/redoc
- **健康检查**: http://localhost:8000/health/

#### 前端界面
- **Streamlit 应用**: http://localhost:8501
- **模块化界面**: 包含文档上传、结果展示、统计分析、系统配置四个主要功能模块
- **实时监控**: 系统状态和处理进度实时更新

## API 端点

### 健康检查

- `GET /health/` - 基本健康检查
- `GET /health/status` - 详细服务状态

### 数据处理

- `GET /api/v1/` - API 信息
- `POST /api/v1/data/ingest` - 数据接收
- `POST /api/v1/data/parse` - 数据解析
- `POST /api/v1/data/clean` - 数据清洗
- `POST /api/v1/data/classify` - 数据分类
- `POST /api/v1/data/vectorize` - 数据向量化
- `POST /api/v1/data/store` - 数据存储

## 支持的文件格式

- **PDF**: .pdf
- **Word**: .docx, .doc
- **Excel**: .xlsx, .xls
- **CSV**: .csv
- **文本**: .txt
- **图像**: .png, .jpg, .jpeg, .tiff, .bmp

## 开发指南

### 添加新的文档处理器

1. 在 `src/data_agent/processors/` 目录下创建新的处理器
2. 继承或实现相应的接口
3. 在路由中注册新的处理器

### 添加新的存储后端

1. 在 `src/data_agent/storage/` 目录下创建新的存储管理器
2. 实现标准的存储接口
3. 在配置中添加相应的配置项

## 测试

```bash
# 运行所有测试
pytest

# 运行特定测试
pytest tests/test_parsing.py

# 生成测试覆盖率报告
pytest --cov=src/data_agent
```

## 部署

### Docker 部署

```dockerfile
# 创建 Dockerfile（待补充）
FROM python:3.9-slim

WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt

COPY src/ ./src/
CMD ["uvicorn", "src.data_agent.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### 生产环境

推荐使用 gunicorn + uvicorn 部署：

```bash
gunicorn src.data_agent.main:app -w 4 -k uvicorn.workers.UvicornWorker --bind 0.0.0.0:8000
```

## 贡献指南

1. Fork 项目
2. 创建功能分支
3. 提交更改
4. 推送到分支
5. 创建 Pull Request

## 许可证

MIT License

## 联系方式

- 项目维护者: Bin
- 邮箱: michael.b.tian@gmail.com

## 项目当前状态

### ✅ 已完成功能
- **基础架构**: FastAPI 应用框架搭建完成
- **配置管理**: 完整的环境配置和设置管理
- **API 接口**: 健康检查和数据处理端点定义
- **文件上传**: 安全的文件接收和验证机制
- **文档解析**: 多格式文档解析器框架 (PDF、Word、Excel、图像 OCR)
- **开发环境**: VS Code 任务配置，支持一键启动
- **前端界面**: 完整的 Streamlit 模块化前端系统
  - **模块化架构**: 组件化设计，代码结构清晰
  - **四大功能模块**: 文档上传、结果展示、统计分析、系统配置
  - **专业 UI 设计**: 响应式布局、主题系统、数据可视化
  - **实时交互**: 文件上传进度、系统状态监控
  - **API 集成**: 完整的前后端通信和状态管理

### 🚧 开发中功能
- **数据清洗模块**: 去重、标准化、格式转换逻辑
- **分类处理器**: 财报、量化公式、合同条款专业分类
- **向量化引擎**: 文档内容向量化和嵌入处理
- **存储管理**: 向量库和结构化数据库集成

### 📋 待开发功能
- **智能路由**: 基于内容的数据路由分发
- **批量处理**: 大规模文档批量处理优化
- **缓存机制**: Redis 缓存优化性能
- **监控告警**: 系统监控和性能指标
- **单元测试**: 完整的测试覆盖

### 🔧 技术债务
- **依赖优化**: 某些依赖包需要 Python 3.13 兼容性优化
- **错误处理**: 增强错误处理和用户反馈
- **文档完善**: API 文档和使用示例补充
- **性能优化**: 大文件处理性能调优

## 下一步开发计划

### 短期目标 (1-2 周) - 核心功能闭环
1. **完善文档解析**: 解决依赖包兼容性问题，确保 OCR 和表格识别功能稳定可用
2. **数据清洗优先**: 实现数据去重、标准化处理逻辑，保证数据可靠性
3. **分类器开发**: 开发财报、合同等专业文档分类算法，确保数据质量
4. **集成向量化**: 添加 sentence-transformers 和向量存储功能
5. **最小可运行闭环**: 形成完整的用户上传 → 解析 → 清洗 → 分类 → 向量化 → 存储 → 查询 → 回答的数据流
6. **API 功能实现**: 将路由端点连接到实际处理逻辑

### 中期目标 (1 个月) - 性能和智能化
1. **Chunk 级别路由**: 实现文档内容块级别的智能路由，财报和合同能在同一文档内独立处理
2. **批量处理优化**: 实现高效的批量文档处理流水线，提升处理性能
3. **缓存机制**: 集成 Redis 缓存，优化重复查询和计算性能
4. **数据库集成**: 完成 MongoDB 和 ChromaDB 的完整集成和优化
5. **Web 界面**: 开发简单的文件上传和管理界面

### 长期目标 (3 个月) - 企业级架构
1. **智能路由 DAG/Graph**: 引入基于有向无环图的智能路由系统，支持多 Agent 调度和复杂工作流
2. **存储层三分化**: 增强存储架构，实现原始文件存储 + 结构化数据库 + 向量库的分层存储策略
3. **企业级功能**: 
   - 用户管理和权限控制系统
   - 完整的审计日志和操作追踪
   - 系统监控和性能指标告警
4. **模型优化**: 自定义嵌入模型和分类模型训练
5. **云端部署**: Docker 容器化和云端部署方案

## 核心技术架构设计

### 最小可运行闭环架构
```
用户上传文档 → 文件验证 → 格式解析 → 数据清洗 → 内容分类 → 文本向量化 → 向量存储 → 语义搜索 → 智能回答
     ↓              ↓           ↓           ↓           ↓            ↓            ↓           ↓           ↓
  文件校验     OCR/表格提取   去重标准化   财报/合同识别  Embedding   ChromaDB    相似度匹配   LLM生成   用户反馈
```

### Chunk 级别路由设计
```
文档输入 → 内容分段 → 段落分类 → 路由分发
                ↓
        ┌───────┼───────┐
        ↓       ↓       ↓
     财报处理  合同处理  通用处理
        ↓       ↓       ↓
     专用Pipeline 各自独立 → 汇总结果
```

### 三分化存储架构
```
1. 原始文件层 (File Storage)
   └── 文件系统 / 对象存储 (原始PDF、Word等)

2. 结构化数据层 (Structured DB)
   └── MongoDB (元数据、关系数据、业务逻辑)

3. 向量数据层 (Vector DB)
   └── ChromaDB / FAISS (文本嵌入、语义搜索)
```

### 智能路由 DAG/Graph 设计
```
输入节点 → 预处理节点 → 分类节点 → 专业处理节点 → 后处理节点 → 输出节点
    ↓          ↓           ↓          ↓              ↓            ↓
  数据接收    格式标准化    内容分类   Agent调度      结果合并     用户响应
    ↓          ↓           ↓          ↓              ↓            ↓
 Agent_A    Agent_B     Agent_C    Agent_D        Agent_E      Agent_F
```

## 更新日志

### v0.2.0 (2025-09-09) - 前端界面完成
- ✅ **模块化前端架构**: 完整的 frontend/ 目录结构设计
- ✅ **Streamlit 前端实现**: 基于组件化的现代界面系统
- ✅ **四大功能模块**: 
  - 📤 文档上传模块 - 支持拖拽上传、进度跟踪、批量处理
  - 📊 结果展示模块 - 处理结果可视化、错误处理、状态展示
  - 📈 统计分析模块 - 数据图表、文件分析、处理统计
  - ⚙️ 系统配置模块 - 参数设置、连接测试、配置管理
- ✅ **可复用组件系统**: upload.py, results.py, config.py 组件
- ✅ **统一主题系统**: 颜色配置、CSS 样式、响应式设计
- ✅ **页面管理器**: 路由管理、导航系统、状态监控
- ✅ **API 客户端**: 完整的后端通信和错误处理
- ✅ **双版本支持**: streamlit_app.py (原版) + streamlit_app_modular.py (模块化)
- ✅ **前端架构文档**: 详细的 ARCHITECTURE.md 设计文档

### v0.1.0 (2025-09-09) - 基础架构
- ✅ 初始项目结构搭建
- ✅ FastAPI 基础架构完成
- ✅ 配置管理系统实现
- ✅ 文档解析器框架创建
- ✅ 文件上传和验证功能
- ✅ API 接口定义和路由配置
- ✅ 开发环境和任务配置
- ✅ 基础依赖安装和测试
