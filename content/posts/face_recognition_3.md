---
title: "[Face_recognition中文文档] 第3节 用法"
date: "2019-01-11"
draft: false
toc: true
images:
tags: 
  - face_recognition
  - 中文文档
---

# Usage用法
在项目中使用面部识别：

```python
import face_recognition
```

有关如何使用每个函数，请参阅github上的 [/examples](https://github.com/ageitgey/face_recognition/tree/master/examples) 文件夹中的示例。
您还可以查阅`face_recognition`的[API文档](https://face-recognition.readthedocs.io/en/latest/face_recognition.html)，以查看每个功能的可能参数。
基本的想法是，首先你加载一个图像：

```python
import face_recognition

image = face_recognition.load_image_file("your_file.jpg")
```

将图像加载到numpy数组中。如果您已经有一个numpy数组中的图像，可以跳过此步骤。

然后，您可以对图像执行操作，如查找面部，识别面部特征或查找面部编码：

```python
# 查找图像中的所有脸部
face_locations = face_recognition.face_locations(image)

# 或者也可以找到图像中的面部特征
face_landmarks_list = face_recognition.face_landmarks(image)

# 或者您可以为图像中的每个面部获取面部编码：
list_of_face_encodings = face_recognition.face_encodings(image)
```

面部编码可以相互比较，看看脸部是否匹配。注意：查找面部的编码有点慢，所以如果需要稍后再参考一下，您可能希望将每个图像的结果保存在数据库或缓存中。

但是，一旦你有面孔的编码，你可以像这样比较：

```python
# results是一个True/False数组，表示未知的面部与known_faces数组中的任何人匹配
results = face_recognition.compare_faces(known_face_encodings, a_single_unknown_face_encoding)
```

这很简单！查看示例了解更多详情。