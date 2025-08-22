# 📷 相机标定项目

## 项目概述

本项目实现了基于张正友标定法的相机标定系统，使用棋盘格标定板获取相机的内参、外参和畸变系数。这是计算机视觉应用的基础步骤。

## 标定原理

### 张正友标定法
张正友标定法是一种基于平面棋盘格的相机标定方法，具有以下优点：
- 只需要平面标定板
- 标定精度高
- 算法稳定可靠
- 易于实现

### 数学模型
```
相机成像模型:
s * [u]   [fx  0  cx]   [R t] [X]
    [v] = [ 0 fy  cy] * [0 1] [Y]
    [1]   [ 0  0   1]         [Z]
                              [1]
```

## 文件结构

```
03_camera_calibration/
├── main.py              # 主标定程序
├── README.md           # 项目说明文档
├── 标定参数.txt         # 标定结果输出
├── 棋盘格.jpg          # 标定板参考图
├── p1/                 # 原始标定图像
│   ├── 5.jpg ~ 25.jpg  # 标定图像序列
├── p2/                 # (预留：调整大小后图像)
└── p3/                 # 标定结果图像
    ├── calibresult.jpg     # 标定结果展示
    └── print_corners*.jpg # 角点检测结果
```

## 环境要求

### Python依赖
```bash
pip install opencv-python>=4.5.0
pip install numpy>=1.19.0
```

### 硬件要求
- 摄像头或相机
- 棋盘格标定板
- 良好的照明环境

## 标定板规格

### 棋盘格参数
```python
# 棋盘格内角点数量
w = 9  # 宽度方向角点数
h = 6  # 高度方向角点数

# 实际棋盘格大小(可根据实际情况调整)
square_size = 25.0  # 单位：mm
```

### 标定板要求
- **平整度**: 必须保证标定板平整，避免弯曲
- **对比度**: 黑白格子对比鲜明
- **打印质量**: 高分辨率打印，边缘清晰
- **尺寸精度**: 确保方格尺寸准确

## 使用说明

### 1. 准备标定图像

#### 图像采集要求
- **数量**: 至少15-20张不同角度图像
- **角度**: 覆盖相机视野的不同区域
- **姿态**: 包含倾斜、旋转等不同姿态
- **质量**: 图像清晰，无运动模糊

#### 采集建议
```python
# 推荐的采集角度分布
angles = [
    "正面", "左倾斜", "右倾斜", 
    "上倾斜", "下倾斜", "左上", 
    "右上", "左下", "右下"
]
```

### 2. 执行标定

```bash
cd 03_camera_calibration
python main.py
```

### 3. 查看结果

标定完成后会生成：
- `标定参数.txt`: 包含所有标定参数
- `p3/calibresult.jpg`: 畸变校正效果对比
- `p3/print_corners*.jpg`: 角点检测结果图

## 输出参数

### 相机内参矩阵 (mtx)
```
mtx = [fx  0  cx]
      [ 0 fy  cy]  
      [ 0  0   1]

fx, fy: 焦距 (像素)
cx, cy: 主点坐标 (像素)
```

### 畸变系数 (dist)
```
dist = [k1, k2, p1, p2, k3]

k1, k2, k3: 径向畸变系数
p1, p2:     切向畸变系数
```

### 其他参数
- **ret**: 重投影误差
- **rvecs**: 旋转向量 (外参)
- **tvecs**: 平移向量 (外参)

## 标定质量评估

### 1. 重投影误差
```python
# 理想误差范围
ret < 0.5  # 优秀
ret < 1.0  # 良好  
ret < 2.0  # 可接受
ret > 2.0  # 需要重新标定
```

### 2. 参数合理性检查
```python
# 焦距比例检查
fx_fy_ratio = fx / fy
assert 0.95 < fx_fy_ratio < 1.05  # 应接近1

# 主点位置检查
assert 0.3 * width < cx < 0.7 * width
assert 0.3 * height < cy < 0.7 * height
```

## 代码示例

### 主要标定流程
```python
import cv2
import numpy as np
import glob

# 设置棋盘格参数
criteria = (cv2.TERM_CRITERIA_EPS + cv2.TERM_CRITERIA_MAX_ITER, 30, 0.001)
w, h = 9, 6

# 准备标定点
objp = np.zeros((w * h, 3), np.float32)
objp[:, :2] = np.mgrid[0:w, 0:h].T.reshape(-1, 2)

# 存储标定点
objpoints = []  # 世界坐标系中的点
imgpoints = []  # 图像坐标系中的点

# 读取标定图像
images = glob.glob('p1/*.jpg')

for fname in images:
    img = cv2.imread(fname)
    gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
    
    # 查找棋盘格角点
    ret, corners = cv2.findChessboardCorners(gray, (w, h), None)
    
    if ret:
        objpoints.append(objp)
        
        # 亚像素角点检测
        corners2 = cv2.cornerSubPix(gray, corners, (11, 11), (-1, -1), criteria)
        imgpoints.append(corners2)

# 执行标定
ret, mtx, dist, rvecs, tvecs = cv2.calibrateCamera(
    objpoints, imgpoints, gray.shape[::-1], None, None)

# 优化相机内参
h, w = gray.shape[:2]
newcameramtx, roi = cv2.getOptimalNewCameraMatrix(mtx, dist, (w, h), 1, (w, h))
```

### 畸变校正示例
```python
# 校正图像畸变
def undistort_image(img, mtx, dist):
    h, w = img.shape[:2]
    newcameramtx, roi = cv2.getOptimalNewCameraMatrix(mtx, dist, (w, h), 1, (w, h))
    
    # 畸变校正
    undistorted = cv2.undistort(img, mtx, dist, None, newcameramtx)
    
    # 裁剪图像
    x, y, w, h = roi
    undistorted = undistorted[y:y+h, x:x+w]
    
    return undistorted
```

## 常见问题

### 1. 角点检测失败
**现象**: findChessboardCorners返回False
**原因**: 
- 标定板不够平整
- 光照不均匀
- 图像模糊或过曝

**解决方案**:
```python
# 改善检测参数
flags = cv2.CALIB_CB_ADAPTIVE_THRESH + cv2.CALIB_CB_NORMALIZE_IMAGE
ret, corners = cv2.findChessboardCorners(gray, (w, h), flags)
```

### 2. 标定精度不高
**现象**: 重投影误差大于1.0
**原因**:
- 标定图像数量不足
- 图像角度分布不均匀
- 标定板尺寸测量不准确

**解决方案**:
- 增加标定图像数量
- 确保角度分布均匀
- 精确测量标定板尺寸

### 3. 参数不合理
**现象**: fx/fy比值偏离1太多
**原因**:
- 相机传感器不是正方形像素
- 标定过程存在误差

**解决方案**:
```python
# 固定fx=fy进行标定
flags = cv2.CALIB_FIX_ASPECT_RATIO
ret, mtx, dist, rvecs, tvecs = cv2.calibrateCamera(
    objpoints, imgpoints, gray.shape[::-1], None, None, flags=flags)
```

## 标定技巧

### 1. 图像采集技巧
- 保持标定板充满视野的2/3
- 确保标定板边缘清晰可见
- 避免反光和阴影
- 多个距离、多个角度

### 2. 标定参数优化
```python
# 使用更严格的终止条件
criteria = (cv2.TERM_CRITERIA_EPS + cv2.TERM_CRITERIA_MAX_ITER, 100, 0.0001)

# 启用有用的标定标志
flags = (cv2.CALIB_RATIONAL_MODEL + 
         cv2.CALIB_THIN_PRISM_MODEL + 
         cv2.CALIB_TILTED_MODEL)
```

### 3. 结果验证
```python
# 计算重投影误差分布
errors = []
for i in range(len(objpoints)):
    imgpoints2, _ = cv2.projectPoints(objpoints[i], rvecs[i], tvecs[i], mtx, dist)
    error = cv2.norm(imgpoints[i], imgpoints2, cv2.NORM_L2) / len(imgpoints2)
    errors.append(error)

print(f"平均误差: {np.mean(errors):.6f}")
print(f"误差标准差: {np.std(errors):.6f}")
```

## 应用示例

标定完成后，可将参数应用于：
- 立体视觉
- 目标测量
- 增强现实
- 机器人视觉

```python
# 在其他项目中使用标定结果
camera_matrix = np.array([
    [fx,  0, cx],
    [ 0, fy, cy],
    [ 0,  0,  1]
])

dist_coeffs = np.array([k1, k2, p1, p2, k3])

# 畸变校正
undistorted = cv2.undistort(image, camera_matrix, dist_coeffs)
```

---
**注意**: 相机标定的质量直接影响后续视觉应用的精度，建议多次标定取最优结果。