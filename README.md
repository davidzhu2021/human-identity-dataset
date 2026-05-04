# Real & Synthetic Pedestrian Detection Dataset / 真假行人检测数据集

> 一个面向**行人检测模型训练**的标注数据集，包含真实采集与合成生成的人体图像，提供 Pascal VOC 和 YOLO 两种标注格式。
>
> A pedestrian detection dataset for **model training**, containing real and synthetically generated human body images, annotated in both Pascal VOC and YOLO formats.

---

## 📁 目录结构 / Directory Structure

```
dataset/
├── images/
│   ├── train/               # 训练集图片
│   ├── val/                 # 验证集图片
│   └── test/                # 测试集图片
│
├── annotations/
│   ├── voc/                 # Pascal VOC 格式标注（XML）
│   │   ├── train/
│   │   ├── val/
│   │   └── test/
│   └── yolo/                # YOLO 格式标注（TXT）
│       ├── train/
│       ├── val/
│       └── test/
│
├── labels.txt               # 类别列表
└── README.md
```

---


## 🏷️ 标注格式 / Annotation Formats

本数据集提供两种标注格式，可按需选择使用。

This dataset provides two annotation formats. Choose the one that fits your training framework.

### Pascal VOC（XML）

部分数据采用 Pascal VOC 格式，每张图片对应一个 `.xml` 文件：

```xml
<annotation>
  <filename>example.jpg</filename>
  <size>
    <width>480</width>
    <height>480</height>
    <depth>3</depth>
  </size>
  <object>
    <name>real_person</name>   <!-- real_person 或 fake_person -->
    <truncated>0</truncated>
    <difficult>0</difficult>
    <bndbox>
      <xmin>131</xmin>
      <ymin>205</ymin>
      <xmax>381</xmax>
      <ymax>439</ymax>
    </bndbox>
  </object>
</annotation>
```

### YOLO（TXT）

部分数据采用 YOLO 格式，每张图片对应一个 `.txt` 文件，每行代表一个目标：

```
<class_id> <x_center> <y_center> <width> <height>
```

- 所有坐标为**归一化值**（相对图片宽高，范围 0\~1）
- 类别映射 / Class mapping：

```
0 → real_person
1 → fake_person
```

---

## 🚀 快速开始 / Quick Start

### 读取 Pascal VOC 标注

```python
import xml.etree.ElementTree as ET

def parse_voc(xml_path):
    tree = ET.parse(xml_path)
    root = tree.getroot()
    results = []
    for obj in root.findall('object'):
        name = obj.find('name').text
        bbox = obj.find('bndbox')
        box = {
            'class': name,
            'xmin': int(bbox.find('xmin').text),
            'ymin': int(bbox.find('ymin').text),
            'xmax': int(bbox.find('xmax').text),
            'ymax': int(bbox.find('ymax').text),
        }
        results.append(box)
    return results
```

### 读取 YOLO 标注

```python
def parse_yolo(txt_path, img_w, img_h):
    class_map = {0: 'real_person', 1: 'fake_person'}
    results = []
    with open(txt_path, 'r') as f:
        for line in f:
            cid, cx, cy, w, h = line.strip().split()
            results.append({
                'class': class_map[int(cid)],
                'x_center': float(cx) * img_w,
                'y_center': float(cy) * img_h,
                'width':    float(w)  * img_w,
                'height':   float(h)  * img_h,
            })
    return results
```

### 推荐训练框架 / Recommended Frameworks

| 框架 | 支持格式 | 链接 |
|------|---------|------|
| YOLOv8 | YOLO | [ultralytics/ultralytics](https://github.com/ultralytics/ultralytics) |
| YOLOv5 | YOLO | [ultralytics/yolov5](https://github.com/ultralytics/yolov5) |
| MMDetection | VOC / COCO | [open-mmlab/mmdetection](https://github.com/open-mmlab/mmdetection) |
| Detectron2 | COCO | [facebookresearch/detectron2](https://github.com/facebookresearch/detectron2) |

> 💡 若使用 COCO 格式的框架，可通过 [Roboflow](https://roboflow.com/) 进行格式转换。

---

## ⚙️ 环境依赖 / Requirements

```bash
pip install opencv-python lxml
```

---

## 📜 许可证 / License

本数据集仅供**学术研究与模型训练**使用，禁止用于商业用途。

This dataset is intended for **academic research and model training only**. Commercial use is strictly prohibited.

© 2026 [David Zhu]
