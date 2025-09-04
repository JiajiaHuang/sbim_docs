---
sidebar_position: 1
---

# SuperDock 系统安装指南

本指南将帮助您完成草莓创新 SuperDock 自动机场系统的安装和初始配置。

## 硬件要求

### SuperDock Pro V4 自动机场

**基本规格**
- **尺寸**: 1000mm × 1200mm × 800mm
- **重量**: 约 150kg
- **电源**: AC 220V，功率 ≤ 3.5kW
- **防护等级**: IP54
- **工作温度**: -20°C ~ +55°C

**适配无人机**
- DJI M350 RTK（换电版）
- DJI M350 RTK / M30 / M30T（充电版）

### SuperDock Mini 2 自动机场

**基本规格**
- **尺寸**: 800mm × 650mm × 750mm（闭合状态）
- **重量**: 75kg
- **电源**: AC 220V，功率 ≤ 2kW
- **防护等级**: IP54
- **工作温度**: -20°C ~ +50°C

**适配无人机**
- DJI Mavic 3E
- DJI Mavic 3T
- DJI Mavic 3M

### SuperDock Pro 自动机场

**基本规格**
- **尺寸**: 1200mm × 1500mm × 1000mm
- **重量**: 约 200kg
- **电源**: AC 220V，功率 ≤ 4kW
- **防护等级**: IP54
- **工作温度**: -20°C ~ +55°C

**适配无人机**
- DJI M350 RTK（充电版）
- DJI M300 RTK（使用RC Plus遥控器）

## 软件要求

### SuperDock CS 控制系统

**服务器要求**
- **操作系统**: Ubuntu 20.04+ / CentOS 8+
- **CPU**: 4核心以上，推荐8核心
- **内存**: 8GB以上，推荐16GB
- **存储**: 500GB以上可用空间，推荐SSD
- **网络**: 4G/5G 或稳定的有线网络连接

**软件依赖**
- Docker 20.10+
- Docker Compose 2.0+
- PostgreSQL 12+ 或 MySQL 8.0+
- Redis 6.0+
- Node.js 16+

## 安装前准备

### 1. 选址要求

**地面条件**
- 平整、坚固的地面
- 水平误差 < ±5mm
- 承重能力满足机场重量要求

**电源要求**
- 稳定的AC 220V电源
- 电压波动范围: ±10%
- 建议配备UPS不间断电源

**网络要求**
- 4G/5G信号覆盖良好
- 或提供有线网络接入
- 网络带宽 ≥ 10Mbps

**空域要求**
- 确认飞行区域符合当地法规
- 获得必要的飞行许可
- 避开禁飞区域

### 2. 工具准备

**安装工具**
- 起重设备（Pro V4/Pro需要）
- 水平仪
- 电工工具
- 网络测试工具

**测试设备**
- 万用表
- 网络测试仪
- 4G信号测试仪

## 硬件安装步骤

### 1. 机场安装

#### SuperDock Pro V4 安装

```bash
# 1. 地面平整度检查
# 使用水平仪检查安装地面
# 确保水平误差 < ±5mm

# 2. 定位安装
# 使用起重设备将机场放置到指定位置
# 确保机场朝向正确（通常朝向主要飞行方向）

# 3. 固定机场
# 使用地脚螺栓固定机场（如需要）
# 确保机场稳固不晃动
```

#### SuperDock Mini 2 安装

```bash
# 1. 展开机场
# 按照操作手册展开机场结构
# 确保所有机械部件正常工作

# 2. 检查组件
# 验证TEC工业空调工作正常
# 检查集成式气象站
# 测试UPS备用电源
# 确认视频监控设备
```

### 2. 电源连接

```bash
# 1. 电源线路检查
# 确认电压稳定在 AC 220V ±10%
# 检查接地线连接

# 2. 电源连接
# 连接主电源线
# 连接UPS备用电源（如有）
# 测试电源开关

# 3. 电源测试
# 开启机场电源
# 检查各组件供电正常
# 验证电源指示灯状态
```

### 3. 网络配置

#### 4G网络配置

```bash
# 1. SIM卡安装
# 将4G SIM卡插入机场4G模块
# 确保SIM卡已激活并有足够流量

# 2. 网络参数配置
# 配置APN设置
# 设置网络优先级
# 测试网络连接

# 网络测试命令
ping 8.8.8.8
curl -I https://www.baidu.com
```

#### 有线网络配置

```bash
# 1. 网线连接
# 连接网线到机场网络接口
# 确保网线连接稳定

# 2. IP地址配置
# 配置静态IP地址（推荐）
# 或使用DHCP自动获取

# 示例静态IP配置
sudo nano /etc/netplan/01-netcfg.yaml

# 配置内容示例
network:
  version: 2
  ethernets:
    eth0:
      dhcp4: no
      addresses: [192.168.1.100/24]
      gateway4: 192.168.1.1
      nameservers:
        addresses: [8.8.8.8, 8.8.4.4]
```

### 4. 无人机配对

#### DJI M350 配对（Pro V4）

```bash
# 1. 无人机准备
# 确保无人机电池充满
# 检查无人机固件版本
# 确认无人机处于正常状态

# 2. 放置无人机
# 将无人机放置在机场平台上
# 确保无人机位置正确
# 检查充电/换电接口对齐

# 3. 执行配对
# 开启无人机和机场电源
# 通过控制系统执行配对流程
# 等待配对完成确认
```

#### DJI Mavic 3 配对（Mini 2）

```bash
# 1. 兼容性检查
# 确认无人机型号（3E/3T/3M）
# 检查固件版本兼容性

# 2. 自动配对
# 将无人机放置在机场内
# 关闭机场舱门
# 启动自动配对流程

# 3. 配对验证
# 检查通信连接状态
# 测试充电功能
# 验证起降功能
```

## 软件安装步骤

### 1. 下载控制系统

```bash
# 下载最新版本的 SuperDock CS
wget https://releases.sb.im/latest/superdock-cs.tar.gz

# 解压安装包
tar -xzf superdock-cs.tar.gz
cd superdock-cs

# 检查系统要求
./check-requirements.sh
```

### 2. 环境准备

```bash
# 安装 Docker
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# 安装 Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/download/v2.20.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# 验证安装
docker --version
docker-compose --version
```

### 3. 运行安装脚本

```bash
# 运行自动安装脚本
sudo ./install.sh

# 安装脚本将自动完成：
# - 系统要求检查
# - Docker 服务配置
# - 数据库初始化
# - 控制系统部署
# - DJI API 兼容接口配置
```

### 4. 配置控制系统

编辑配置文件 `/etc/superdock/config.yml`：

```yaml
# SuperDock CS 配置文件
system:
  name: "SuperDock Control System"
  timezone: "Asia/Shanghai"
  log_level: "INFO"

# 数据库配置
database:
  type: postgresql
  host: localhost
  port: 5432
  name: superdock
  username: superdock_user
  password: your_secure_password

# DJI API 兼容配置
dji_api:
  enabled: true
  endpoint: "https://your-domain.com/api/dji"
  app_id: "your_app_id"
  app_key: "your_app_key"
  webhook_url: "https://your-domain.com/webhook/dji"

# 机场连接配置
docks:
  - id: "dock001"
    name: "SuperDock Pro V4 #1"
    ip: "192.168.1.100"
    type: "pro_v4"
    location:
      latitude: 22.689214
      longitude: 114.236018
      altitude: 10
  - id: "dock002"
    name: "SuperDock Mini 2 #1"
    ip: "192.168.1.101"
    type: "mini_2"
    location:
      latitude: 22.689314
      longitude: 114.236118
      altitude: 12

# AI 识别配置
ai_recognition:
  enabled: true
  models:
    - name: "person_detection"
      type: "yolo"
      confidence: 0.7
    - name: "vehicle_detection"
      type: "yolo"
      confidence: 0.8
  custom_training: true

# 通知配置
notifications:
  email:
    enabled: true
    smtp_host: "smtp.gmail.com"
    smtp_port: 587
    username: "your-email@gmail.com"
    password: "your-app-password"
  wechat:
    enabled: true
    webhook_url: "your-wechat-webhook-url"
```

### 5. 启动服务

```bash
# 启动 SuperDock CS 服务
sudo systemctl start superdock-cs
sudo systemctl enable superdock-cs

# 检查服务状态
sudo systemctl status superdock-cs

# 查看服务日志
sudo journalctl -u superdock-cs -f
```

### 6. 验证安装

```bash
# 检查控制系统状态
curl -X GET http://localhost:8080/api/v1/system/status

# 检查机场连接状态
curl -X GET http://localhost:8080/api/v1/docks

# 检查无人机状态
curl -X GET http://localhost:8080/api/v1/drones

# 访问 Web 界面
# 打开浏览器访问: http://your-server-ip:8080
```

## 初始配置

### 1. 创建管理员账户

首次访问系统时，需要创建管理员账户：

1. 访问 `http://your-server-ip:8080`
2. 点击"创建管理员账户"
3. 填写管理员信息
4. 完成初始化设置

### 2. 机场注册

在控制系统中注册机场设备：

1. 登录管理界面
2. 进入"设备管理" → "机场管理"
3. 点击"添加机场"
4. 填写机场信息并保存

### 3. 无人机注册

注册无人机到系统：

1. 进入"设备管理" → "无人机管理"
2. 点击"添加无人机"
3. 扫描无人机序列号或手动输入
4. 绑定到对应机场

### 4. 飞行任务配置

创建第一个飞行任务：

1. 进入"任务管理" → "创建任务"
2. 选择机场和无人机
3. 设置飞行路径和参数
4. 配置AI识别规则
5. 保存并测试任务

## 故障排除

### 常见问题

#### 机场无法连接

```bash
# 检查网络连接
ping 机场IP地址

# 检查机场电源状态
# 确认电源指示灯正常

# 检查防火墙设置
sudo ufw status
sudo ufw allow 8080
```

#### 无人机配对失败

```bash
# 检查无人机电池电量
# 确保电池电量 > 50%

# 检查无人机固件版本
# 确保固件版本兼容

# 重启机场和无人机
# 重新执行配对流程
```

#### 控制系统启动失败

```bash
# 查看详细错误日志
sudo journalctl -u superdock-cs -n 50

# 检查配置文件语法
sudo superdock-cs config validate

# 检查端口占用
sudo netstat -tlnp | grep :8080
```

## 下一步

安装完成后，建议您：

1. 阅读 [快速入门指南](./quick-start)
2. 了解 [基本概念](./basic-concepts)
3. 查看 [用户手册](../guides/user-guide)
4. 学习 [API 文档](../api-reference/)

如需技术支持，请联系：
- 📧 技术支持：support@sb.im
- 📞 技术热线：400-xxx-xxxx
- 💬 在线客服：https://sb.im/support
