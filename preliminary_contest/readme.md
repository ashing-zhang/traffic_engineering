# SUMO竞赛框架使用说明

## 概述

本框架为SUMO智能交通算法竞赛提供完整的开发、测试和评估环境。框架包含两个主要部分：

- **竞赛开发框架** (SUMOCompetitionFramework) - 供参赛者开发交通控制算法
- **评估系统** (TrafficMetricsEvaluatorStrict) - 用于最终性能评估和反作弊检测

## 文件结构

```
竞赛文件包/
├── mian.py                    # 主开发框架(选手使用)
├── routes.xml                 # 交通流配置文件
└── net.xml                    # 路网配置文件
└── baseline.sumocfg           # sumo仿真文件
└── README.md                  # 本文档
```

---

## 第一部分：开发框架使用指南

### 1. 环境配置

#### 系统要求
- Python 3.7+
- SUMO仿真软件
- 必需的Python包：pandas,  traci

#### 安装步骤

```bash
# 安装SUMO
# 从 https://sumo.dlr.de/docs/Downloads.php 下载并安装，建议安装sumo版本为1.2.4

# 安装Python包
pip install pandas numpy traci
```


### 2. 核心功能开发

#### 控制算法实现区域

在`apply_control_algorithm()`方法中实现你的算法：

```python
def apply_control_algorithm(self, step):
    """
    在此实现你的交通控制算法
    可用的TraCI函数示例：
    """
    # 示例1：车辆速度控制
    vehicle_ids = traci.vehicle.getIDList()
    for veh_id in vehicle_ids:
        current_speed = traci.vehicle.getSpeed(veh_id)
        # 你的控制逻辑...
        
    # 示例2：路径规划
    for veh_id in vehicle_ids:
        current_route = traci.vehicle.getRoute(veh_id)
        # 你的路径优化逻辑...

```

#### 可用TraCI函数示例

**示例1-车辆控制：**
```python
traci.vehicle.setSpeed(veh_id, speed)
traci.vehicle.setRoute(veh_id, edge_list)
traci.vehicle.getRoute(veh_id)
```

**示例2-交通流监测：**
```python
traci.lane.getLastStepVehicleNumber(lane_id)
traci.edge.getLastStepVehicleNumber(edge_id)
traci.simulation.getDepartedIDList()
```

### 4. 数据收集与分析

框架自动收集以下数据：

- 车辆级数据：速度、位置、完成度、OD信息等
- 时间步数据：车辆数量、到达/出发统计、信号灯状态等
- 仿真参数：流量率、仿真时长、总需求等

#### 数据输出

仿真结束后，数据自动保存到`competition_results`目录：

- `vehicle_data_时间戳.csv` - 详细车辆轨迹数据
- `step_data_时间戳.csv` - 时间步统计数据
- `simulation_params_时间戳.json` - 仿真参数

---

## 第二部分：评估系统说明

### 评估指标

最终评分基于三个核心指标：

#### 1. OD完成率 (OCR) 
- 衡量交通系统效率的核心指标
- 计算公式：OCR = (到达车辆数 + 在途车辆完成度之和) / 总车辆数

#### 2. 速度稳定性 
- 评估交通流畅度
- 基于车辆速度的标准差

#### 3. 加速度平顺性 
- 评估驾驶舒适度和安全性
- 基于平均绝对加速度


#### 严格验证项目

**OD一致性验证：**
- 对比提交数据中的OD信息与routes.xml配置
- 要求100%匹配，防止修改车辆路径作弊

**信号灯状态验证：**
- 对比信号灯状态与baseline数据
- 检测异常的信号灯控制模式

**数据完整性检查：**
- 验证数据结构和字段完整性
- 检查数据合理性边界

### 允许的优化方法

#### ✅ 允许的车辆控制：
- 速度引导策略
- 有限的路径重规划（需符合OD约束）

#### ❌ 禁止的行为：
- 不得对仿真环境配置信息做任何修改（包括但不限制于：CV车辆比例、OD配置、路径等）
- 信号灯相位信息不得进行修改
- csv文件保存程序不得进行二次修改
---

## 第三部分：开发建议

### 算法开发流程

#### 第一阶段：熟悉框架和交通网络
- 运行baseline仿真理解交通流特性
- 分析拥堵点和瓶颈区域


#### 第二阶段：算法效果优化
- 开发协调控制算法
- 实现多目标优化策略

### 调试技巧

```python
# 添加调试输出
def apply_control_algorithm(self, step):
    if step % 100 == 0:  # 每100步输出一次
        print(f"Step {step}: 活跃车辆数 {len(traci.vehicle.getIDList())}")
    
    # 你的算法代码...
```

---

## 第四部分：提交要求

### 提交内容

- 源代码：完整的算法实现
- 输出数据：运行框架生成的CSV文件


### 文件命名规范

- 源代码：`main.py`
- 数据文件：保持框架生成的原始命名


---

## 技术支持

### 常见问题

- **SUMO启动失败**：检查配置文件路径和SUMO安装
- **TraCI连接错误**：确保SUMO_GUI或SUMO正确安装
- **数据收集异常**：检查文件写入权限

### 获取帮助

- 查阅SUMO官方文档
- 参考框架中的示例代码
- 联系竞赛技术支持

---

**祝你在竞赛中取得优异成绩！** 🚦🚗🏆