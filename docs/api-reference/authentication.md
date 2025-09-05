---
id: authentication
sidebar_position: 1
---

# 认证授权

SuperDock CS 控制系统提供多种认证方式，确保API访问的安全性。本文档介绍如何进行API认证和授权。

## 认证方式

### 1. API Key 认证

最简单的认证方式，适用于服务器到服务器的通信。

#### 获取 API Key

1. 登录 SuperDock CS 管理界面
2. 进入 **"系统设置"** → **"API 管理"**
3. 点击 **"创建 API Key"**
4. 设置权限范围和过期时间
5. 保存并复制生成的 API Key

#### 使用方式

在请求头中添加 API Key：

```bash
curl -X GET "https://your-domain.com/api/v1/docks" \
  -H "X-API-Key: your-api-key-here"
```

或者作为查询参数：

```bash
curl -X GET "https://your-domain.com/api/v1/docks?api_key=your-api-key-here"
```

### 2. JWT Token 认证

基于 JSON Web Token 的认证方式，适用于用户会话管理。

#### 获取 Token

```bash
curl -X POST "https://your-domain.com/api/v1/auth/login" \
  -H "Content-Type: application/json" \
  -d '{
    "username": "your-username",
    "password": "your-password"
  }'
```

响应示例：

```json
{
  "success": true,
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "expires_in": 3600,
    "refresh_token": "refresh-token-here"
  }
}
```

#### 使用 Token

在请求头中添加 Bearer Token：

```bash
curl -X GET "https://your-domain.com/api/v1/docks" \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
```

#### 刷新 Token

```bash
curl -X POST "https://your-domain.com/api/v1/auth/refresh" \
  -H "Content-Type: application/json" \
  -d '{
    "refresh_token": "refresh-token-here"
  }'
```

### 3. OAuth 2.0

适用于第三方应用集成，支持标准的 OAuth 2.0 流程。

#### 注册应用

1. 在管理界面注册 OAuth 应用
2. 获取 `client_id` 和 `client_secret`
3. 配置回调 URL

#### 授权码流程

**步骤 1**: 重定向用户到授权页面

```
https://your-domain.com/oauth/authorize?
  response_type=code&
  client_id=your-client-id&
  redirect_uri=your-callback-url&
  scope=read:docks,write:missions&
  state=random-state-string
```

**步骤 2**: 用户授权后获取授权码

**步骤 3**: 使用授权码换取访问令牌

```bash
curl -X POST "https://your-domain.com/oauth/token" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=authorization_code&
      code=authorization-code&
      redirect_uri=your-callback-url&
      client_id=your-client-id&
      client_secret=your-client-secret"
```

## 权限范围

### 权限级别

| 权限级别 | 说明 | 适用场景 |
|---------|------|----------|
| `read` | 只读权限 | 数据查询、状态监控 |
| `write` | 读写权限 | 任务创建、参数配置 |
| `admin` | 管理员权限 | 系统配置、用户管理 |

### 资源权限

| 权限范围 | 说明 | 包含操作 |
|---------|------|----------|
| `read:docks` | 机场信息读取 | 查看机场状态、配置 |
| `write:docks` | 机场控制 | 机场开关、参数设置 |
| `read:drones` | 无人机信息读取 | 查看无人机状态、位置 |
| `write:drones` | 无人机控制 | 起飞、降落、任务执行 |
| `read:missions` | 任务信息读取 | 查看任务列表、状态 |
| `write:missions` | 任务管理 | 创建、修改、删除任务 |
| `read:media` | 媒体文件访问 | 下载照片、视频 |
| `write:media` | 媒体文件管理 | 上传、删除媒体文件 |
| `read:ai` | AI识别结果读取 | 查看识别结果、统计 |
| `write:ai` | AI模型管理 | 训练、部署AI模型 |

## DJI API 兼容性

SuperDock CS 完全兼容 DJI 上云 API，支持无缝迁移现有系统。

### 兼容的认证方式

```bash
# DJI API 风格的认证
curl -X POST "https://your-domain.com/api/dji/v1/token" \
  -H "Content-Type: application/json" \
  -d '{
    "app_id": "your-dji-app-id",
    "app_key": "your-dji-app-key"
  }'
```

### 兼容的请求格式

```bash
# 获取设备列表（DJI API 兼容）
curl -X GET "https://your-domain.com/api/dji/v1/devices" \
  -H "Authorization: Bearer dji-compatible-token"
```

## 错误处理

### 认证错误

| 错误码 | 说明 | 解决方案 |
|-------|------|----------|
| `401` | 未认证 | 检查 API Key 或 Token |
| `403` | 权限不足 | 检查权限范围设置 |
| `429` | 请求过于频繁 | 实施请求限流 |

### 错误响应格式

```json
{
  "success": false,
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Invalid API key",
    "details": "The provided API key is invalid or expired"
  }
}
```

## 安全最佳实践

### 1. API Key 管理

- ✅ 定期轮换 API Key
- ✅ 设置合适的过期时间
- ✅ 限制 API Key 的权限范围
- ❌ 不要在客户端代码中硬编码 API Key

### 2. Token 安全

- ✅ 使用 HTTPS 传输
- ✅ 安全存储 refresh_token
- ✅ 实施 Token 过期机制
- ❌ 不要在 URL 中传递敏感信息

### 3. 请求限流

系统默认实施以下限流策略：

| 认证方式 | 限制 | 时间窗口 |
|---------|------|----------|
| API Key | 1000 请求 | 每小时 |
| JWT Token | 500 请求 | 每小时 |
| OAuth | 2000 请求 | 每小时 |

### 4. IP 白名单

可以为 API Key 配置 IP 白名单：

```json
{
  "api_key": "your-api-key",
  "allowed_ips": [
    "192.168.1.100",
    "10.0.0.0/8"
  ]
}
```

## 示例代码

### Python 示例

```python
import requests
import json

class SuperDockAPI:
    def __init__(self, base_url, api_key):
        self.base_url = base_url
        self.api_key = api_key
        self.session = requests.Session()
        self.session.headers.update({
            'X-API-Key': api_key,
            'Content-Type': 'application/json'
        })
    
    def get_docks(self):
        response = self.session.get(f"{self.base_url}/api/v1/docks")
        return response.json()
    
    def create_mission(self, mission_data):
        response = self.session.post(
            f"{self.base_url}/api/v1/missions",
            data=json.dumps(mission_data)
        )
        return response.json()

# 使用示例
api = SuperDockAPI("https://your-domain.com", "your-api-key")
docks = api.get_docks()
print(docks)
```

### JavaScript 示例

```javascript
class SuperDockAPI {
  constructor(baseUrl, apiKey) {
    this.baseUrl = baseUrl;
    this.apiKey = apiKey;
  }

  async request(endpoint, options = {}) {
    const url = `${this.baseUrl}/api/v1${endpoint}`;
    const config = {
      headers: {
        'X-API-Key': this.apiKey,
        'Content-Type': 'application/json',
        ...options.headers
      },
      ...options
    };

    const response = await fetch(url, config);
    return response.json();
  }

  async getDocks() {
    return this.request('/docks');
  }

  async createMission(missionData) {
    return this.request('/missions', {
      method: 'POST',
      body: JSON.stringify(missionData)
    });
  }
}

// 使用示例
const api = new SuperDockAPI('https://your-domain.com', 'your-api-key');
api.getDocks().then(docks => console.log(docks));
```

## 下一步

了解认证机制后，您可以：

1. 📖 查看 [机场控制 API](./dock-api) 了解机场操作接口
2. 🚁 查看 [无人机 API](./drone-api) 了解无人机控制接口
3. 🤖 查看 [AI识别 API](./ai-api) 了解智能识别接口
4. 🔔 查看 [Webhook](./webhooks) 了解事件通知机制

如有疑问，请查看 [常见问题](../faq/) 或联系技术支持。
