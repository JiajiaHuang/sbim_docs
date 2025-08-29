---
sidebar_position: 1
---

# 系统安装

本指南将帮助您完成SBIM系统的安装和初始配置。

## 系统要求

### 服务器要求
- **操作系统**: Ubuntu 20.04+ / CentOS 8+ / Windows Server 2019+
- **CPU**: 4核心以上
- **内存**: 8GB以上
- **存储**: 100GB以上可用空间
- **网络**: 稳定的互联网连接

### 数据库要求
- **MySQL**: 8.0+ 或 **PostgreSQL**: 12+
- **Redis**: 6.0+ (用于缓存和会话管理)

### 客户端要求
- **浏览器**: Chrome 90+, Firefox 88+, Safari 14+, Edge 90+
- **移动设备**: iOS 13+, Android 8+

## 安装步骤

### 1. 环境准备

#### 安装Docker (推荐)
```bash
# Ubuntu/Debian
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# 安装Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/download/v2.20.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

#### 安装Node.js (如需源码部署)
```bash
# 使用NodeSource仓库
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs

# 验证安装
node --version
npm --version
```

### 2. 下载SBIM

#### 方式一：Docker部署 (推荐)
```bash
# 下载docker-compose配置
wget https://releases.sbim.com/latest/docker-compose.yml

# 下载环境配置模板
wget https://releases.sbim.com/latest/.env.example
cp .env.example .env
```

#### 方式二：源码部署
```bash
# 克隆仓库
git clone https://github.com/sbim/sbim.git
cd sbim

# 安装依赖
npm install
```

### 3. 配置环境

编辑 `.env` 文件，配置以下关键参数：

```bash
# 应用配置
APP_NAME=SBIM
APP_ENV=production
APP_DEBUG=false
APP_URL=https://your-domain.com

# 数据库配置
DB_CONNECTION=mysql
DB_HOST=localhost
DB_PORT=3306
DB_DATABASE=sbim
DB_USERNAME=sbim_user
DB_PASSWORD=your_secure_password

# Redis配置
REDIS_HOST=localhost
REDIS_PASSWORD=your_redis_password
REDIS_PORT=6379

# 邮件配置
MAIL_MAILER=smtp
MAIL_HOST=smtp.gmail.com
MAIL_PORT=587
MAIL_USERNAME=your-email@gmail.com
MAIL_PASSWORD=your-app-password

# 文件存储配置
FILESYSTEM_DISK=local
# 或使用云存储
# FILESYSTEM_DISK=s3
# AWS_ACCESS_KEY_ID=your-access-key
# AWS_SECRET_ACCESS_KEY=your-secret-key
# AWS_DEFAULT_REGION=us-east-1
# AWS_BUCKET=your-bucket-name
```

### 4. 数据库初始化

#### 创建数据库
```sql
-- MySQL
CREATE DATABASE sbim CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'sbim_user'@'localhost' IDENTIFIED BY 'your_secure_password';
GRANT ALL PRIVILEGES ON sbim.* TO 'sbim_user'@'localhost';
FLUSH PRIVILEGES;
```

#### 运行数据库迁移
```bash
# Docker部署
docker-compose exec app php artisan migrate

# 源码部署
php artisan migrate
```

### 5. 启动服务

#### Docker部署
```bash
# 启动所有服务
docker-compose up -d

# 查看服务状态
docker-compose ps

# 查看日志
docker-compose logs -f
```

#### 源码部署
```bash
# 启动应用服务器
npm run start

# 或使用PM2管理进程
npm install -g pm2
pm2 start ecosystem.config.js
```

### 6. 初始化配置

访问 `http://your-domain.com/setup` 完成初始化配置：

1. **管理员账号设置**
   - 用户名：admin
   - 邮箱：admin@your-domain.com
   - 密码：设置强密码

2. **系统基础配置**
   - 系统名称和描述
   - 时区设置
   - 语言偏好

3. **许可证激活**
   - 输入许可证密钥
   - 验证激活状态

## 验证安装

### 1. 健康检查
```bash
# 检查应用状态
curl http://localhost:3000/health

# 预期响应
{
  "status": "ok",
  "timestamp": "2025-08-29T12:00:00Z",
  "version": "2.0.0"
}
```

### 2. 登录测试
1. 访问 `http://your-domain.com`
2. 使用管理员账号登录
3. 检查仪表板是否正常显示

### 3. API测试
```bash
# 获取访问令牌
curl -X POST http://localhost:3000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"admin@your-domain.com","password":"your-password"}'

# 测试API调用
curl -X GET http://localhost:3000/api/buildings \
  -H "Authorization: Bearer YOUR_TOKEN"
```

## 常见问题

### 数据库连接失败
```bash
# 检查数据库服务状态
sudo systemctl status mysql

# 检查网络连接
telnet localhost 3306

# 验证用户权限
mysql -u sbim_user -p -e "SHOW DATABASES;"
```

### 端口冲突
```bash
# 查看端口占用
sudo netstat -tlnp | grep :3000

# 修改配置文件中的端口
# .env 文件中的 APP_PORT=3001
```

### 权限问题
```bash
# 设置正确的文件权限
sudo chown -R www-data:www-data storage/
sudo chmod -R 755 storage/
```

## 下一步

安装完成后，建议您：

1. [配置SSL证书](./ssl-setup) 确保数据传输安全
2. [设置备份策略](./backup-setup) 保护重要数据
3. [阅读快速入门指南](./quick-start) 了解基本使用方法
4. [查看管理员指南](../guides/admin-guide) 学习系统管理

## 获取帮助

如果在安装过程中遇到问题：

- 📖 查看[故障排除指南](../tutorials/troubleshooting)
- 💬 访问[社区论坛](https://community.sbim.com)
- 📧 联系技术支持：[support@sbim.com](mailto:support@sbim.com)
