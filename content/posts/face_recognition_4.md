---
title: "[Face_recognition中文文档] 第4节 Face Recognition API"
date: "2019-01-11"
draft: false
toc: true
images:
tags: 
  - face_recognition
  - 中文文档
---

# Face Recognition API

- [face_recognition包](https://face-recognition.readthedocs.io/en/latest/face_recognition.html)
- [模块内容](https://face-recognition.readthedocs.io/en/latest/face_recognition.html#module-face_recognition.api)

---

```python
face_recognition.api.batch_face_locations(images, number_of_times_to_upsample=1, batch_size=128)
```

[源码](https://face-recognition.readthedocs.io/en/latest/_modules/face_recognition/api.html#batch_face_locations)

使用cnn面部检测器返回图像中二维人脸的边界框数组，如果您正在使用GPU，这可以更快的给您结果，因为GPU可以一次处理批次的图像。如果您不使用GPU，则不需要此功能。

##### 参数

- images - 图像列表（每个作为numpy数组）
- number_of_times_to_upsample - 用于对图像进行采样的次数。较高的数字找到较小的脸。
- batch_size - 每个GPU处理批次中包含的图像数量。

##### 返回

一个可以在css（上，右，下，左）顺序中找到的人脸位置的元组列表

---

```python
face_recognition.api.compare_faces(known_face_encodings, face_encoding_to_check, tolerance=0.6)
```

[源码](https://face-recognition.readthedocs.io/en/latest/_modules/face_recognition/api.html#compare_faces)

将候选编码的面部编码列表进行比较，以查看它们是否匹配。

##### 参数

- known_face_encodings - 已知面部编码的列表
- face_encoding_to_check - 与已知面部编码的列表进行比较的单面编码
- tolerance - 面孔之间的距离要考虑多少。越小越严格， 0.6是典型的最佳性能。

##### 返回

一个True / False值的列表，指出哪个known_face_encodings匹配要检查的面部编码

---

```python
face_recognition.api.face_distance(face_encodings, face_to_compare)
```

[源码](https://face-recognition.readthedocs.io/en/latest/_modules/face_recognition/api.html#face_distance)

给出面部编码列表，将其与已知的面部编码进行比较，并为每个比较的人脸获得欧几里得距离。距离告诉你面孔是如何相似的。

##### 参数

- face_encodings - 要比较的面部编码列表
- face_to_compare - 要比较的面部编码

##### 返回

一个numpy ndarray，每个面的距离与“faces”数组的顺序相同

---

```python
face_recognition.api.face_encodings(face_image, known_face_locations=None, num_jitters=1)
```

[源码](https://face-recognition.readthedocs.io/en/latest/_modules/face_recognition/api.html#face_encodings)

给定图像，返回图像中每个面部的128维面部编码。

##### 参数

- face_image - 包含一个或多个面的图像
- known_face_locations - 可选 - 如果您已经知道它们，每个面的边框。
- num_jitters - 计算编码时重新采样多少次。更高更准确，但更慢（即100是100倍慢）

##### 返回

128个面部编码的列表（图像中的每个脸部一个）

---

```python
face_recognition.api.face_landmarks(face_image, face_locations=None)
```

[源码](https://face-recognition.readthedocs.io/en/latest/_modules/face_recognition/api.html#face_landmarks)

给定图像，返回图像中每个脸部的脸部特征位置（眼睛，鼻子等）的指令

##### 参数

- face_image - 要搜索的图像
- face_locations - 可选地提供要检查的面部位置的列表。

##### 返回

面部特征位置（眼睛，鼻子等）的列表

---

```python
face_recognition.api.face_locations(img, number_of_times_to_upsample=1, model='hog')
```

[源码](https://face-recognition.readthedocs.io/en/latest/_modules/face_recognition/api.html#face_locations)

返回图像中人脸的边框数组

##### 参数

- img - 一个图像（作为一个numpy数组）
- number_of_times_to_upsample - 用于对图像进行上采样的次数多少次。较高的数字找到较小的脸。
- model - 要使用的面部检测模型。“hog”在CPU上不太准确，但速度更快。“cnn”是一个更准确的深入学习模式，GPU / CUDA加速（如果可用）。默认为“hog”。

##### 返回

一个可以在css（上，右，下，左）顺序中找到的表面位置的元组列表

---

```python
face_recognition.api.load_image_file(file, mode='RGB')
```

[源码](https://face-recognition.readthedocs.io/en/latest/_modules/face_recognition/api.html#load_image_file)

将图像文件（.jpg，.png等）加载到numpy数组中

##### 参数

- file - 要加载的图像文件名或文件对象
- mode - 将图像转换为格式。只支持“RGB”（8位RGB，3声道）和“L”（黑白）。

##### 返回

图像内容为numpy数组
