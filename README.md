# 🏆 电子设计竞赛项目集合

## 📖 项目简介

本仓库是一个全国大学生电子设计竞赛（National Undergraduate Electronics Design Contest）的项目集合，包含了多个不同领域的电子设计项目和相关资料。项目涵盖了嵌入式系统、计算机视觉、自动控制等多个技术领域。

## 🗂️ 项目结构

```
restructured_project/
├── 01_electromagnetic_cannon/     # 电磁炮项目
├── 02_vision_measurement/         # 基于单目视觉的目标物测量装置
├── 03_camera_calibration/         # 相机标定项目
├── 04_gui_applications/           # GUI应用程序
├── 05_competition_docs/           # 竞赛文档和题目
└── 06_reference_materials/        # 参考资料和综合测评资料
```

## 🚀 主要项目

### 1. 电磁炮项目 (01_electromagnetic_cannon)
**技术栈**: STM32F103, C语言, 嵌入式系统

**项目特点**:
- 基于STM32F103微控制器的智能电磁炮系统
- 集成MPU6050陀螺仪进行姿态控制
- 支持OpenMV视觉识别和目标跟踪
- 包含PID控制算法和PWM调速
- 具备LCD显示和人机交互界面

**主要功能**:
- 自动目标识别与跟踪
- 精确姿态控制
- 实时数据显示
- 远程控制接口

### 2. 基于单目视觉的目标物测量装置 (02_vision_measurement)
**技术栈**: Python, OpenCV, 计算机视觉

**项目特点**:
- 实现基于单目视觉的非接触式测量
- 支持A4纸目标物的距离和尺寸测量
- 自动图像畸变校正
- 透视变换和几何形状识别

**主要功能**:
- 目标物距离测量 (D)
- 几何形状尺寸测量 (x)
- 自动形状识别（圆形、三角形、正方形）
- 实时图像处理和显示

### 3. 相机标定项目 (03_camera_calibration)
**技术栈**: Python, OpenCV, 张正友标定法

**项目特点**:
- 基于棋盘格的相机标定
- 输出完整的相机内参和畸变参数
- 支持批量图像处理

**输出参数**:
- 相机内参矩阵
- 畸变系数
- 重投影误差
- 旋转和平移向量

### 4. GUI应用程序 (04_gui_applications)
**技术栈**: Python, Tkinter

**项目特点**:
- 为测量装置设计的用户界面
- 实时数据显示和控制
- 一键启动测量功能
- 友好的交互设计

## 📋 竞赛文档

### 包含题目
- **L题**: 无线电能传输与智能充电系统
- **C题**: 基于单目视觉的目标物测量装置
- 完整的题目问答集合（883条问答）

### 参考资料
- 历年综合测评题目 (2011-2023)
- 电路设计参考资料
- 数字电路和模拟电路设计指南
- 运放电路设计文档

## 🛠️ 环境要求

### 电磁炮项目
- Keil MDK-ARM 开发环境
- STM32F103 开发板
- ALIENTEK 2.8/3.5/4.3/7寸TFT LCD模块
- MPU6050陀螺仪模块
- OpenMV摄像头模块

### 视觉测量项目
```bash
pip install opencv-python
pip install numpy
pip install scipy
pip install imutils
pip install pytesseract
```

### 相机标定项目
```bash
pip install opencv-python
pip install numpy
```

### GUI应用
```bash
pip install tkinter  # 通常Python自带
```

## 🏃‍♂️ 快速开始

### 1. 电磁炮项目
```bash
cd 01_electromagnetic_cannon/电磁炮v3.5
# 使用Keil MDK打开 USER/ 目录下的工程文件
# 编译并下载到STM32开发板
```

### 2. 视觉测量系统
```bash
cd 02_vision_measurement
python Distance.py
```

### 3. 相机标定
```bash
cd 03_camera_calibration
python main.py
```

### 4. GUI应用
```bash
cd 04_gui_applications/路径问题解决版本
python Test.py
```

## 🔧 项目配置

### 电磁炮硬件连接
- DS0 (LED): PB5
- 串口1: PA9/PA10 (调试用)
- LCD模块: 通过FSMC连接
- KEY0按键: PE4
- MPU6050: PB10(SCL), PB11(SDA), PA15(AD0)

### 视觉测量参数配置
```python
# 相机内参矩阵示例
camera_matrix = np.array([
    [1.06086502e+03, 0.00000000e+00, 6.44918938e+02],
    [0.00000000e+00, 1.07097676e+03, 3.42131143e+02],
    [0.00000000e+00, 0.00000000e+00, 1.00000000e+00]
])

# 相机高度设置
camera_height = 50.0  # 单位: cm
```

## 📊 测试结果

### 视觉测量精度
- 距离测量精度: ±2cm
- 尺寸测量精度: ±5%
- 处理速度: <5秒/次

### 电磁炮性能
- 姿态控制精度: ±2°
- 响应时间: <100ms
- 目标跟踪精度: 像素级别

## 🤝 贡献指南

1. Fork 本仓库
2. 创建你的特性分支 (`git checkout -b feature/AmazingFeature`)
3. 提交你的改动 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 打开一个 Pull Request

## 📄 许可证

本项目采用 MIT 许可证 - 查看 [LICENSE](LICENSE) 文件了解详情

## 📞 联系方式

如果你有任何问题或建议，欢迎通过以下方式联系:

- 创建 [Issue](../../issues)
- 发送邮件

## 🙏 致谢

- 感谢全国大学生电子设计竞赛组委会提供的竞赛平台
- 感谢开源社区提供的优秀库和工具
- 感谢团队成员的辛勤付出

## ⭐ 如果这个项目对你有帮助，请给个星星！

---

**注意**: 本项目仅用于学习和研究目的，请遵守相关竞赛规则和学术诚信原则。
