# SD-Admissions-Cloud

[![Java 17](https://img.shields.io/badge/Java-17-red?logo=openjdk)](https://openjdk.org/)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.1-green?logo=spring)](https://spring.io/)
[![Vue 3](https://img.shields.io/badge/Vue-3-brightgreen?logo=vue.js)](https://vuejs.org/)
[![MySQL 8](https://img.shields.io/badge/MySQL-8-blue?logo=mysql)](https://www.mysql.com/)

本项目为山东省高校招生计划数据管理平台（SD Admissions Cloud，简写“SDAC”，），提供招生计划录入、历史数据查询、院校专业分析等功能，支持多年度专业代码动态映射。


## 技术栈

### 后端

- **核心框架**: Spring Boot 3.1
- **数据库**: MySQL 8.0
- **ORM**: MyBatis-Plus
- **API文档**: Swagger 3.0
- **构建工具**: Maven
- **其他工具**: Lombok, Hutool

### 前端

- **核心框架**: Vue 3 + TypeScript
- **UI组件库**: Element Plus
- **状态管理**: Pinia
- **路由管理**: Vue Router
- **HTTP库**: Axios
- **构建工具**: Vite

### 基础设施

- **容器化**: Docker + Docker Compose
- **部署**: Nginx 反向代理

---

## 功能特性

### 核心模块

1. **招生计划管理**
   - 多年度计划数据批量导入（支持Excel）
   - 动态专业代码映射（解决年度代码变更问题）
   - 计划人数与学费统计分析

2. **历史数据查询**
   - 按院校/专业/年份多维度筛选
   - 录取分数与位次趋势可视化

3. **院校专业画像**
   - 院校基础信息管理（城市、隶属单位等）
   - 硕博点数据关联分析

### 技术亮点

- **动态专业ID映射**: 通过`major_code_mapping`表实现跨年度专业标识一致性
- **数据校验引擎**: 基于Spring Validation实现Excel导入数据自动校验
- **高性能查询**: 使用MyBatis-Plus多租户插件优化分页查询

---

## 快速部署

### 环境要求

- JDK 17+
- MySQL 8.0+
- Node.js 18+
- Docker（可选）

### 后端启动

```bash
# 克隆项目
git clone https://github.com/yourname/shandong-admission.git

# 初始化数据库
mysql -u root -p < database/schema.sql
mysql -u root -p < database/sample_data.sql

# 编译运行
cd backend
mvn clean package
java -jar target/admission-api.jar
```

### 前端启动

```bash
cd frontend
npm install
npm run dev
```

### Docker部署

```bash
docker-compose -f docker-compose.yml up --build
```

---

## 数据库设计

### 核心表结构

```sql
-- 专业主表（稳定ID）
CREATE TABLE majors (
    major_id INT AUTO_INCREMENT PRIMARY KEY,
    major_name VARCHAR(200) NOT NULL COMMENT '标准化专业名称',
    discipline_category VARCHAR(50) COMMENT '学科门类'
);

-- 年度代码映射表
CREATE TABLE major_code_mapping (
    year INT NOT NULL,
    admission_code CHAR(10) NOT NULL COMMENT '年度招生代码',
    major_id INT NOT NULL,
    FOREIGN KEY (major_id) REFERENCES majors(major_id),
    UNIQUE KEY (year, admission_code)
);

-- 招生计划表
CREATE TABLE admission_plans (
    plan_id INT AUTO_INCREMENT PRIMARY KEY,
    year INT NOT NULL,
    institution_code CHAR(10) NOT NULL,
    major_id INT NOT NULL,
    plan_num INT COMMENT '计划人数',
    FOREIGN KEY (major_id) REFERENCES majors(major_id)
);
```

### 数据关系图

```
┌─────────────┐       ┌───────────────────┐
│  majors     │◄───┐  │ major_code_mapping│
├─────────────┤    │  ├───────────────────┤
│ major_id    │────┘  │ year              │
│ major_name  │       │ admission_code    │
└─────────────┘       └───────────────────┘
       ▲                    ▲
       │                    │
       └───────────────┐    │
                       │    │
                 ┌─────────────┐
                 │ admission   │
                 │ _plans      │
                 ├─────────────┤
                 │ year        │
                 │ major_id    │
                 └─────────────┘
```

---

## 项目结构

### 后端 (`backend/src/main/java`)

```
com.shandong.admission
├── config            # Spring配置类
├── controller        # REST API接口
├── service           # 业务逻辑层
├── mapper            # MyBatis数据访问层
├── entity            # 数据库实体类
├── dto               # 数据传输对象
├── util              # 工具类库
└── exception         # 全局异常处理
```

### 前端 (`frontend/src`)

```
src/
├── api/              # Axios接口封装
├── assets/           # 静态资源
├── components/       # 通用组件
├── router/           # 路由配置
├── stores/           # Pinia状态管理
├── types/            # TypeScript类型定义
└── views/            # 页面组件
```

---

## API文档

访问运行后的后端服务：

```
http://localhost:8080/swagger-ui.html
```

示例接口：

```http
GET /api/plans?year=2025&institutionCode=10422
Response:
{
  "data": [
    {
      "planId": 1,
      "majorName": "计算机科学与技术",
      "admissionCode": "080901",
      "planNum": 120,
      "tuition": 6000.00
    }
  ]
}
```

---

## 贡献指南

1. Fork 项目仓库
2. 创建特性分支 (`git checkout -b feature/your-feature`)
3. 提交代码 (`git commit -m 'Add some feature'`)
4. 推送分支 (`git push origin feature/your-feature`)
5. 提交Pull Request

**代码规范**:

- 后端遵循《阿里巴巴Java开发手册》
- 前端使用ESLint + Prettier

---

## 许可证

[MIT License](LICENSE)

```

```