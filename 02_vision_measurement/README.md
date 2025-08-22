# 📐 基于单目视觉的目标物测量装置

## 项目概述

本项目实现了基于单目视觉的非接触式测量系统，能够准确测量A4纸目标物的距离和几何形状尺寸。该项目对应全国大学生电子设计竞赛C题要求。

## 技术特点

- **非接触式测量**: 基于计算机视觉技术
- **高精度测量**: 距离精度±2cm，尺寸精度±5%
- **实时处理**: 单次测量时间<5秒
- **自动识别**: 支持多种几何形状自动识别
- **畸变校正**: 自动相机畸变校正

## 核心算法

### 1. 相机标定
使用张正友标定法获取相机内参：
```python
camera_matrix = np.array([
    [fx,  0, cx],
    [ 0, fy, cy],
    [ 0,  0,  1]
])
```

### 2. 目标检测
- **颜色空间转换**: BGR → HSV
- **边缘检测**: 黑色边框识别
- **轮廓近似**: Douglas-Peucker算法
- **形状验证**: A4纸宽高比检验(~0.707)

### 3. 距离计算
基于相似三角形原理：
```python
D = (camera_height * fy) / dy_pixels
```

### 4. 尺寸测量
- **透视变换**: 获取正视图
- **形状识别**: 圆形、三角形、正方形
- **尺寸计算**: 像素到实际尺寸转换

## 文件说明

```
02_vision_measurement/
├── Distance.py           # 主要测量算法
├── Debug流程.png        # 调试流程图
├── Debug版本.txt        # 调试说明
├── 寄存器单击常测.txt    # 寄存器测试
├── 寄存器单机单测.txt    # 单机测试
├── 常用命令.txt         # 常用命令参考
└── 葵花宝典.png        # 使用指南
```

## 环境配置

### Python依赖
```bash
pip install opencv-python>=4.5.0
pip install numpy>=1.19.0
pip install scipy>=1.6.0
pip install imutils>=0.5.4
pip install pytesseract>=0.3.7
```

### 系统要求
- **操作系统**: Windows/Linux/macOS
- **Python版本**: 3.7+
- **内存**: 最低4GB RAM
- **摄像头**: USB摄像头或工业相机

## 使用方法

### 1. 基本测量
```python
from Distance import VisionMeasurementSystem

# 初始化系统
vms = VisionMeasurementSystem(camera_matrix, dist_coeffs, camera_height)

# 读取图像
image = cv2.imread("target_image.jpg")

# 执行测量
D, x, shapes = vms.measure(image, debug=True)

print(f"距离: {D:.1f}cm, 尺寸: {x:.1f}cm")
```

### 2. 相机标定
```python
# 使用标定板图像进行标定
ret, mtx, dist, rvecs, tvecs = cv2.calibrateCamera(
    objpoints, imgpoints, gray.shape[::-1], None, None)
```

### 3. 调试模式
```python
# 开启调试模式查看处理过程
debug_info = vms.debug_analysis(image)
```

## 测量流程

### 1. 图像预处理
```
原始图像 → 畸变校正 → HSV转换 → 边缘检测 → 形态学处理
```

### 2. 目标检测
```
轮廓提取 → 轮廓筛选 → 多边形近似 → A4纸验证 → 角点排序
```

### 3. 透视变换
```
四点对应 → 透视矩阵 → 图像变换 → 正视图获取
```

### 4. 形状识别
```
二值化 → 轮廓检测 → 形状分类 → 尺寸计算
```

## 精度指标

| 测量项目 | 精度指标 | 测试条件 |
|----------|----------|----------|
| 距离测量(D) | ±2cm | 距离12-18cm |
| 边长测量(x) | ±5% | 正方形边长2-10cm |
| 圆直径测量 | ±5% | 圆直径2-10cm |
| 三角形边长 | ±5% | 等边三角形 |

## 参数配置

### 相机参数
```python
# 相机内参矩阵
camera_matrix = np.array([
    [1.06086502e+03, 0.00000000e+00, 6.44918938e+02],
    [0.00000000e+00, 1.07097676e+03, 3.42131143e+02],
    [0.00000000e+00, 0.00000000e+00, 1.00000000e+00]
])

# 畸变系数
dist_coeffs = np.array([k1, k2, p1, p2, k3])

# 相机高度(cm)
camera_height = 50.0
```

### 图像处理参数
```python
# HSV阈值范围
lower_black = np.array([0, 0, 0])
upper_black = np.array([180, 255, 50])

# A4纸宽高比范围
aspect_ratio_range = (0.6, 0.8)

# 轮廓近似精度
epsilon_factors = [0.01, 0.02, 0.03, 0.04, 0.05]
```

## 调试功能

### 1. 可视化调试
```python
# 显示处理步骤
cv2.imshow("Original", image)
cv2.imshow("Preprocessed", undistorted)
cv2.imshow("Mask", mask)
cv2.imshow("Contours", debug_img)
```

### 2. 参数分析
```python
# 输出调试信息
print(f"检测到轮廓数量: {len(contours)}")
print(f"宽高比: {aspect_ratio:.3f}")
print(f"面积: {area:.0f}px²")
```

### 3. 性能监控
```python
import time
start_time = time.time()
# 测量代码
end_time = time.time()
print(f"处理时间: {end_time - start_time:.2f}秒")
```

## 常见问题

### 1. 无法检测到A4纸
**原因**: 光照不均、对比度不够
**解决**: 
- 调整照明条件
- 修改HSV阈值范围
- 检查相机焦距

### 2. 测量精度不足
**原因**: 相机标定不准确
**解决**:
- 重新进行相机标定
- 使用更多标定图像
- 检查标定板质量

### 3. 形状识别错误
**原因**: 轮廓近似参数不当
**解决**:
- 调整epsilon参数
- 改善图像质量
- 优化预处理算法

## 扩展功能

### 1. 多目标测量
```python
# 支持同时测量多个目标
for shape in shapes:
    size = measure_size(shape)
    print(f"{shape['type']}: {size}cm")
```

### 2. 实时测量
```python
# 摄像头实时测量
cap = cv2.VideoCapture(0)
while True:
    ret, frame = cap.read()
    D, x, shapes = vms.measure(frame)
    # 显示结果
```

### 3. 数据导出
```python
# 测量结果保存
results = {
    'timestamp': time.time(),
    'distance': D,
    'size': x,
    'shape_type': target_shape['shape']
}
with open('results.json', 'w') as f:
    json.dump(results, f)
```

## 参考资料

- [OpenCV官方文档](https://docs.opencv.org/)
- [相机标定理论](https://docs.opencv.org/master/dc/dbb/tutorial_py_calibration.html)
- [透视变换原理](https://docs.opencv.org/master/da/d6e/tutorial_py_geometric_transformations.html)

---
**注意**: 使用前请确保相机已正确标定，并根据实际使用环境调整参数。
