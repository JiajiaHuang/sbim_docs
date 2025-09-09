# 快速上手

完成安装后，让我们开始第一次使用草莓创新无人机自动机场系统。

## 第一步：启动系统

启动主控制程序：

```bash
python3 sbim_dock.py --start
```

系统启动后，您将看到控制台输出：

```
🚀 草莓创新无人机自动机场系统 v1.0.0
📡 正在连接到控制中心...
✅ 连接成功
🔧 系统初始化完成
```

## 第二步：连接无人机

### 自动检测

系统会自动检测附近的兼容无人机：

```bash
python3 sbim_dock.py --scan
```

### 手动连接

如果需要手动连接特定无人机：

```bash
python3 sbim_dock.py --connect --drone-id "DJI_MAVIC_001"
```

## 第三步：执行第一个任务

### 创建简单巡检任务

```python
from sbim_sdk import DockController, Mission

# 初始化控制器
dock = DockController(api_key="your_api_key")

# 创建巡检任务
mission = Mission()
mission.add_waypoint(lat=39.9042, lng=116.4074, altitude=50)
mission.add_waypoint(lat=39.9052, lng=116.4084, altitude=50)
mission.add_waypoint(lat=39.9042, lng=116.4074, altitude=50)  # 返回起点

# 执行任务
result = dock.execute_mission(mission)
print(f"任务状态: {result.status}")
```

### 使用命令行工具

```bash
# 创建任务文件
cat > mission.json << EOF
{
  "name": "第一次巡检",
  "waypoints": [
    {"lat": 39.9042, "lng": 116.4074, "altitude": 50},
    {"lat": 39.9052, "lng": 116.4084, "altitude": 50},
    {"lat": 39.9042, "lng": 116.4074, "altitude": 50}
  ]
}
EOF

# 执行任务
python3 sbim_dock.py --mission mission.json
```

## 第四步：监控任务状态

### 实时监控

```bash
python3 sbim_dock.py --monitor
```

### Web 界面

打开浏览器访问：http://localhost:8080

您将看到实时的：
- 无人机位置
- 电池状态
- 任务进度
- 传感器数据

## 常用命令

| 命令 | 说明 |
|------|------|
| `--start` | 启动系统 |
| `--stop` | 停止系统 |
| `--status` | 查看系统状态 |
| `--scan` | 扫描无人机 |
| `--connect` | 连接无人机 |
| `--mission` | 执行任务 |
| `--monitor` | 监控模式 |
| `--emergency-land` | 紧急降落 |

## 安全注意事项

⚠️ **重要提醒**

- 首次使用前请确保在空旷无人的区域测试
- 始终保持无人机在视线范围内
- 检查当地法规和飞行限制
- 确保电池电量充足
- 恶劣天气条件下请勿飞行

## 故障排除

### 常见问题

**Q: 无人机连接失败**
A: 检查无人机是否开机，确认蓝牙/WiFi连接正常

**Q: 任务执行中断**
A: 检查网络连接，确认GPS信号良好

**Q: 系统启动失败**
A: 检查配置文件是否正确，API密钥是否有效

## 下一步

- 了解更多 [API 接口](../api/overview)
- 学习 [身份验证](../api/authentication)
- 继续探索其他文档章节
