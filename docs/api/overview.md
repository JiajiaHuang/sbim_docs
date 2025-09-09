# API 概览

草莓创新无人机自动机场系统提供完整的 RESTful API，让您可以通过编程方式控制和管理无人机机场。

## 基础信息

- **API 基础 URL**: `https://api.sb.im/v1`
- **协议**: HTTPS
- **数据格式**: JSON
- **认证方式**: API Key + JWT Token

## 核心功能模块

### 🚁 无人机管理

- 无人机注册和配置
- 实时状态监控
- 飞行控制指令
- 电池和传感器数据

### 🏠 机场管理

- 机场状态监控
- 起降平台控制
- 充电系统管理
- 环境传感器数据

### 📋 任务管理

- 任务创建和调度
- 航点规划
- 任务执行监控
- 结果数据获取

### 📊 数据分析

- 飞行数据分析
- 性能统计
- 历史记录查询
- 报告生成

## 快速开始

### 1. 获取 API 密钥

首先需要在控制台获取您的 API 密钥：

```bash
curl -X POST https://api.sb.im/v1/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "email": "your@email.com",
    "password": "your_password"
  }'
```

### 2. 认证请求

```bash
curl -X POST https://api.sb.im/v1/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "api_key": "your_api_key",
    "secret": "your_secret"
  }'
```

### 3. 发起 API 调用

```bash
curl -X GET https://api.sb.im/v1/docks \
  -H "Authorization: Bearer your_jwt_token" \
  -H "Content-Type: application/json"
```

## 主要端点

### 机场管理

| 方法 | 端点 | 说明 |
|------|------|------|
| GET | `/docks` | 获取机场列表 |
| GET | `/docks/{id}` | 获取机场详情 |
| POST | `/docks` | 创建新机场 |
| PUT | `/docks/{id}` | 更新机场信息 |
| DELETE | `/docks/{id}` | 删除机场 |

### 无人机管理

| 方法 | 端点 | 说明 |
|------|------|------|
| GET | `/drones` | 获取无人机列表 |
| GET | `/drones/{id}` | 获取无人机详情 |
| POST | `/drones/{id}/takeoff` | 起飞指令 |
| POST | `/drones/{id}/land` | 降落指令 |
| POST | `/drones/{id}/return` | 返航指令 |

### 任务管理

| 方法 | 端点 | 说明 |
|------|------|------|
| GET | `/missions` | 获取任务列表 |
| POST | `/missions` | 创建新任务 |
| GET | `/missions/{id}` | 获取任务详情 |
| POST | `/missions/{id}/start` | 开始执行任务 |
| POST | `/missions/{id}/pause` | 暂停任务 |
| POST | `/missions/{id}/stop` | 停止任务 |

## 响应格式

### 成功响应

```json
{
  "success": true,
  "data": {
    "id": "dock_001",
    "name": "主机场",
    "status": "online",
    "location": {
      "latitude": 39.9042,
      "longitude": 116.4074
    }
  },
  "timestamp": "2024-01-01T12:00:00Z"
}
```

### 错误响应

```json
{
  "success": false,
  "error": {
    "code": "INVALID_REQUEST",
    "message": "请求参数无效",
    "details": "缺少必需的字段: api_key"
  },
  "timestamp": "2024-01-01T12:00:00Z"
}
```

## 状态码

| 状态码 | 说明 |
|--------|------|
| 200 | 请求成功 |
| 201 | 资源创建成功 |
| 400 | 请求参数错误 |
| 401 | 认证失败 |
| 403 | 权限不足 |
| 404 | 资源不存在 |
| 429 | 请求频率超限 |
| 500 | 服务器内部错误 |

## 限制和配额

- **请求频率**: 每分钟最多 1000 次请求
- **数据传输**: 每月最多 10GB
- **并发连接**: 最多 50 个并发连接
- **文件上传**: 单个文件最大 100MB

## SDK 支持

我们提供多种编程语言的 SDK：

- **Python**: `pip install sbim-sdk`
- **JavaScript**: `npm install @sbim/sdk`
- **Java**: Maven/Gradle 依赖
- **Go**: Go modules 支持

## 下一步

- 了解 [身份验证](./authentication) 详细信息
- 继续阅读其他文档章节
- 开始使用 API 进行开发
