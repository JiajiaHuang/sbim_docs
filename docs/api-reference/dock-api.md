---
sidebar_position: 2
---

# 机场控制 API

本文档介绍 SuperDock 自动机场的控制接口，包括机场状态查询、控制操作和配置管理。

## 基础信息

### API 端点

```
Base URL: https://your-domain.com/api/v1
DJI 兼容: https://your-domain.com/api/dji/v1
```

### 认证方式

所有 API 请求都需要认证，详见 [认证授权](./authentication) 文档。

## 机场管理

### 获取机场列表

获取系统中所有机场的信息。

```http
GET /docks
```

**响应示例：**

```json
{
  "success": true,
  "data": [
    {
      "dock_id": "dock001",
      "name": "SuperDock Pro V4 #1",
      "type": "pro_v4",
      "status": "online",
      "location": {
        "latitude": 22.689214,
        "longitude": 114.236018,
        "altitude": 10,
        "address": "深圳市南山区科技园"
      },
      "capabilities": {
        "max_drones": 1,
        "charging": true,
        "battery_swap": true,
        "weather_station": true,
        "rtk_base": true
      },
      "current_drone": {
        "drone_sn": "1234567890",
        "model": "DJI M350 RTK",
        "status": "docked"
      },
      "last_seen": "2025-01-15T10:30:00Z"
    }
  ],
  "total": 1
}
```

### 获取机场详情

获取指定机场的详细信息。

```http
GET /docks/{dock_id}
```

**路径参数：**
- `dock_id` (string): 机场ID

**响应示例：**

```json
{
  "success": true,
  "data": {
    "dock_id": "dock001",
    "name": "SuperDock Pro V4 #1",
    "type": "pro_v4",
    "status": "online",
    "hardware": {
      "serial_number": "SPV4-2025-001",
      "firmware_version": "2.1.5",
      "hardware_version": "V4.2",
      "manufacture_date": "2025-01-01"
    },
    "power": {
      "status": "normal",
      "voltage": 220.5,
      "current": 12.3,
      "power": 2710.15,
      "ups_status": "standby",
      "ups_battery": 95
    },
    "environment": {
      "temperature": 25.6,
      "humidity": 65,
      "wind_speed": 3.2,
      "wind_direction": 180,
      "pressure": 1013.25,
      "visibility": 10000
    },
    "network": {
      "type": "4g",
      "signal_strength": -65,
      "ip_address": "192.168.1.100",
      "data_usage": {
        "upload": 1024000,
        "download": 2048000
      }
    },
    "storage": {
      "total_space": 1000000000,
      "used_space": 250000000,
      "available_space": 750000000
    }
  }
}
```

## 机场控制

### 开启机场

启动机场系统，准备接收无人机。

```http
POST /docks/{dock_id}/power-on
```

**请求体：**

```json
{
  "force": false,
  "check_weather": true,
  "check_airspace": true
}
```

**响应示例：**

```json
{
  "success": true,
  "data": {
    "dock_id": "dock001",
    "status": "powering_on",
    "estimated_time": 30,
    "message": "机场正在启动，预计30秒完成"
  }
}
```

### 关闭机场

安全关闭机场系统。

```http
POST /docks/{dock_id}/power-off
```

**请求体：**

```json
{
  "force": false,
  "wait_for_drone": true,
  "emergency": false
}
```

### 打开舱门

打开机场舱门，准备无人机起飞或降落。

```http
POST /docks/{dock_id}/open-cover
```

**响应示例：**

```json
{
  "success": true,
  "data": {
    "dock_id": "dock001",
    "cover_status": "opening",
    "estimated_time": 15,
    "message": "舱门正在打开"
  }
}
```

### 关闭舱门

关闭机场舱门，保护无人机。

```http
POST /docks/{dock_id}/close-cover
```

### 机场复位

将机场恢复到初始状态。

```http
POST /docks/{dock_id}/reset
```

**请求体：**

```json
{
  "reset_type": "soft",  // soft, hard, factory
  "clear_logs": false,
  "reset_network": false
}
```

## 充电和换电

### 开始充电

为停靠的无人机开始充电。

```http
POST /docks/{dock_id}/start-charging
```

**请求体：**

```json
{
  "target_percentage": 100,
  "fast_charge": true,
  "auto_stop": true
}
```

**响应示例：**

```json
{
  "success": true,
  "data": {
    "dock_id": "dock001",
    "charging_status": "charging",
    "current_percentage": 65,
    "target_percentage": 100,
    "estimated_time": 1800,
    "charging_power": 150
  }
}
```

### 停止充电

停止为无人机充电。

```http
POST /docks/{dock_id}/stop-charging
```

### 换电操作

执行电池更换操作（仅支持换电版机场）。

```http
POST /docks/{dock_id}/battery-swap
```

**请求体：**

```json
{
  "battery_slot": "auto",  // auto, slot1, slot2, etc.
  "target_percentage": 95,
  "return_old_battery": true
}
```

**响应示例：**

```json
{
  "success": true,
  "data": {
    "dock_id": "dock001",
    "swap_status": "swapping",
    "old_battery": {
      "serial": "BAT001",
      "percentage": 15,
      "cycles": 245
    },
    "new_battery": {
      "serial": "BAT002",
      "percentage": 98,
      "cycles": 12
    },
    "estimated_time": 100
  }
}
```

### 获取电池状态

查询机场中所有电池的状态。

```http
GET /docks/{dock_id}/batteries
```

**响应示例：**

```json
{
  "success": true,
  "data": {
    "dock_id": "dock001",
    "battery_slots": [
      {
        "slot_id": "slot1",
        "battery": {
          "serial": "BAT001",
          "percentage": 98,
          "voltage": 25.2,
          "temperature": 28.5,
          "cycles": 12,
          "health": 95,
          "status": "ready"
        }
      },
      {
        "slot_id": "slot2",
        "battery": {
          "serial": "BAT002",
          "percentage": 85,
          "voltage": 24.8,
          "temperature": 30.1,
          "cycles": 156,
          "health": 88,
          "status": "charging"
        }
      }
    ],
    "charging_status": {
      "active_slots": 1,
      "total_power": 150,
      "estimated_completion": "2025-01-15T12:30:00Z"
    }
  }
}
```

## 环境监控

### 获取环境数据

获取机场环境传感器数据。

```http
GET /docks/{dock_id}/environment
```

**查询参数：**
- `start_time` (string): 开始时间 (ISO 8601)
- `end_time` (string): 结束时间 (ISO 8601)
- `interval` (string): 数据间隔 (1m, 5m, 1h)

**响应示例：**

```json
{
  "success": true,
  "data": {
    "dock_id": "dock001",
    "current": {
      "timestamp": "2025-01-15T10:30:00Z",
      "temperature": 25.6,
      "humidity": 65,
      "wind_speed": 3.2,
      "wind_direction": 180,
      "pressure": 1013.25,
      "visibility": 10000,
      "weather_condition": "clear",
      "flight_conditions": {
        "suitable": true,
        "warnings": []
      }
    },
    "history": [
      {
        "timestamp": "2025-01-15T10:25:00Z",
        "temperature": 25.4,
        "humidity": 66,
        "wind_speed": 2.8,
        "wind_direction": 175
      }
    ]
  }
}
```

### 设置环境阈值

配置环境监控的报警阈值。

```http
PUT /docks/{dock_id}/environment/thresholds
```

**请求体：**

```json
{
  "temperature": {
    "min": -10,
    "max": 50,
    "warning_min": 0,
    "warning_max": 40
  },
  "wind_speed": {
    "max": 12,
    "warning_max": 8
  },
  "humidity": {
    "max": 90,
    "warning_max": 80
  },
  "visibility": {
    "min": 1000,
    "warning_min": 3000
  }
}
```

## 网络和通信

### 获取网络状态

查询机场网络连接状态。

```http
GET /docks/{dock_id}/network
```

**响应示例：**

```json
{
  "success": true,
  "data": {
    "dock_id": "dock001",
    "connections": [
      {
        "type": "4g",
        "status": "connected",
        "signal_strength": -65,
        "carrier": "China Mobile",
        "ip_address": "10.123.45.67",
        "data_usage": {
          "upload": 1024000,
          "download": 2048000,
          "reset_date": "2025-01-01T00:00:00Z"
        }
      },
      {
        "type": "ethernet",
        "status": "disconnected",
        "ip_address": null
      }
    ],
    "rtk": {
      "status": "active",
      "base_station": true,
      "accuracy": 0.02,
      "satellites": 18
    }
  }
}
```

### 配置网络

配置机场网络连接参数。

```http
PUT /docks/{dock_id}/network/config
```

**请求体：**

```json
{
  "ethernet": {
    "enabled": true,
    "dhcp": false,
    "ip_address": "192.168.1.100",
    "netmask": "255.255.255.0",
    "gateway": "192.168.1.1",
    "dns": ["8.8.8.8", "8.8.4.4"]
  },
  "4g": {
    "enabled": true,
    "apn": "cmnet",
    "username": "",
    "password": "",
    "priority": 2
  },
  "rtk": {
    "enabled": true,
    "mode": "base",
    "ntrip_server": "rtk.example.com",
    "ntrip_port": 2101,
    "mount_point": "RTCM3"
  }
}
```

## 日志和诊断

### 获取系统日志

获取机场系统日志。

```http
GET /docks/{dock_id}/logs
```

**查询参数：**
- `level` (string): 日志级别 (debug, info, warning, error)
- `start_time` (string): 开始时间
- `end_time` (string): 结束时间
- `limit` (integer): 返回条数限制

**响应示例：**

```json
{
  "success": true,
  "data": {
    "dock_id": "dock001",
    "logs": [
      {
        "timestamp": "2025-01-15T10:30:00Z",
        "level": "info",
        "module": "power",
        "message": "机场电源系统正常启动",
        "details": {
          "voltage": 220.5,
          "current": 12.3
        }
      },
      {
        "timestamp": "2025-01-15T10:29:45Z",
        "level": "warning",
        "module": "environment",
        "message": "风速接近警告阈值",
        "details": {
          "wind_speed": 7.8,
          "threshold": 8.0
        }
      }
    ],
    "total": 1250,
    "has_more": true
  }
}
```

### 系统诊断

执行机场系统诊断检查。

```http
POST /docks/{dock_id}/diagnostics
```

**请求体：**

```json
{
  "checks": [
    "hardware",
    "network",
    "power",
    "environment",
    "storage"
  ],
  "detailed": true
}
```

**响应示例：**

```json
{
  "success": true,
  "data": {
    "dock_id": "dock001",
    "overall_status": "healthy",
    "checks": {
      "hardware": {
        "status": "pass",
        "details": "所有硬件组件正常"
      },
      "network": {
        "status": "pass",
        "details": "网络连接正常，信号强度良好"
      },
      "power": {
        "status": "warning",
        "details": "UPS电池电量偏低",
        "recommendations": ["检查UPS电池状态"]
      }
    },
    "timestamp": "2025-01-15T10:30:00Z"
  }
}
```

## 错误处理

### 常见错误码

| 错误码 | 说明 | 解决方案 |
|-------|------|----------|
| `DOCK_NOT_FOUND` | 机场不存在 | 检查机场ID是否正确 |
| `DOCK_OFFLINE` | 机场离线 | 检查机场网络连接 |
| `DOCK_BUSY` | 机场忙碌 | 等待当前操作完成 |
| `POWER_FAILURE` | 电源故障 | 检查电源连接 |
| `COVER_STUCK` | 舱门卡住 | 手动检查舱门机械结构 |
| `BATTERY_ERROR` | 电池错误 | 检查电池状态和连接 |
| `WEATHER_UNSUITABLE` | 天气不适宜 | 等待天气条件改善 |

### 错误响应格式

```json
{
  "success": false,
  "error": {
    "code": "DOCK_OFFLINE",
    "message": "机场离线，无法执行操作",
    "details": "机场 dock001 已离线超过5分钟",
    "timestamp": "2025-01-15T10:30:00Z"
  }
}
```

## 下一步

了解机场控制 API 后，您可以：

1. 🚁 查看 [无人机 API](./drone-api) 了解无人机控制接口
2. 🤖 查看 [AI识别 API](./ai-api) 了解智能识别接口
3. 📋 查看 [任务管理 API](./mission-api) 了解任务调度接口
4. 🔔 查看 [Webhook](./webhooks) 了解事件通知机制

如有疑问，请查看 [常见问题](../faq/) 或联系技术支持。
