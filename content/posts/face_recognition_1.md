---
title: "[face_recognition中文文档] 第1节 人脸识别"
date: 2020-10-11T09:50:26+08:00
draft: false
toc: true
images:
tags: 
  - face_recognition
  - 中文文档
---

# Face Recognition人脸识别库

这是世界上最简单的人脸识别库了。你可以通过Python引用或者命令行的形式使用它，来管理和识别人脸。
该软件包使用dlib中最先进的人脸识别深度学习算法，使得识别准确率在《Labled Faces in the world》测试基准下达到了99.38%。
它同时提供了一个叫face_recognition的命令行工具，以便你可以用命令行对一个文件夹中的图片进行识别操作。

## 特征

### 在图片中识别人脸

找到图片中所有的人脸：

![示例图1](http://upload-images.jianshu.io/upload_images/2640591-feccdb7521c773cd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```python
import face_recognition
image=face_recognition.load_image_file("your_file.jpg")
face_locations=face_recognition.face_locations(image)
```

### 找到并操作图片中的脸部特征

获得图片中人类眼睛、鼻子、嘴、下巴的位置和轮廓：

![示例图2](http://upload-images.jianshu.io/upload_images/2640591-ba41d9baeb1bd4f1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```python
import face_recognition
image = face_recognition.load_image_file("your_file.jpg")
face_landmarks_list = face_recognition.face_landmarks(image)
```

找到脸部特征有很多超级有用的应用场景，当然你也可以把它用在最显而易见的功能上：美颜功能 [就像美图秀秀那样](https://github.com/ageitgey/face_recognition/blob/master/examples/digital_makeup.py)：

![示例图3](http://upload-images.jianshu.io/upload_images/2640591-9cebf7989905cdbf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 鉴定图片中的脸

识别图片中的人是谁

![示例图4](http://upload-images.jianshu.io/upload_images/2640591-a50a601146624c98.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```python
import face_recognition
known_image=face_recognition.load_image_file("biden.jpg")
unknown_image=face_recognition.load_image_file("unknown.jpg")
biden_encoding=face_recognition.face_encodings(known_image)[0
]unknown_encoding=face_recognition.face_encodings(unknown_image)[0]
results=face_recognition.compare_faces([biden_encoding],unknown_encoding)
```

你甚至可以用这个软件包做人脸的实时识别：

![示例图5](http://upload-images.jianshu.io/upload_images/2640591-988581b251acb5a7.gif?imageMogr2/auto-orient/strip)

这里有一个[实时识别的例子](https://github.com/ageitgey/face_recognition/blob/master/examples/facerec_from_webcam_faster.py)

## 安装

### 环境要求:

- Python3.3+或者Python2.7
- MacOS或者Linux（Windows不做支持，但是你可以试试，也许也能运行）

### 安装步骤

#### 在Mac或Linux上安装

首先，确保你安装了dlib，以及该软件的Python绑定接口。如果没有的话，看这篇安装说明：

- [如何从macOS或Ubuntu上安装dlib](https://gist.github.com/ageitgey/629d75c1baac34dfa5ca2a1928a7aeaf)

然后，使用`pip3`（Python 2的`pip2`）从pypi安装此模块：

```python
pip3 install face_recognition
```

如果你安装遇到问题，可以试试这个安装好了的[虚拟机](https://medium.com/@ageitgey/try-deep-learning-in-python-now-with-a-fully-pre-configured-vm-1d97d4c3e9b)

#### 在树莓派2+上安装

- [在树莓派2+上安装说明](https://gist.github.com/ageitgey/1ac8dbe8572f3f533df6269dab35df65)

#### 在Windows上安装

虽然Windows不是官方支持的，但是有热心网友写出了一个Windows上的使用指南，请看这里：

- [@ masoudr的Windows 10安装指南（dlib + face_recognition）](https://github.com/ageitgey/face_recognition/issues/175#issue-257710508)

#### 使用已经配置好的虚拟机（支持VMWare和VirtualBox）

- [下载预配置的虚拟机映像](https://medium.com/@ageitgey/try-deep-learning-in-python-now-with-a-fully-pre-configured-vm-1d97d4c3e9b)（适用于VMware Player或VirtualBox）。

## 使用方法

### 命令行界面

如果你已经安装了face_recognition，那么你的系统中已经有了一个名为face_recognition的命令，你可以使用它对图片进行识别，或者对一个文件夹中的所有图片进行识别。
首先你需要提供一个文件夹，里面是所有你希望系统认识的人的图片。其中每个人一张图片，图片以人的名字命名：

![示例图6](http://upload-images.jianshu.io/upload_images/2640591-7c754e41e224f0bf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

接下来，你需要准备另一个文件夹，里面是你要识别的图片：

![示例图7](http://upload-images.jianshu.io/upload_images/2640591-1553f10adc1fc607.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

然后你就可以运行face_recognition命令了，把刚刚准备的两个文件夹作为参数传入，命令就会返回需要识别的图片中都出现了谁：

```python
$ face_recognition ./pictures_of_people_i_know/ ./unknown_pictures/

/unknown_pictures/unknown.jpg,Barack Obama
/face_recognition_test/unknown_pictures/unknown.jpg,unknown_person
```

输出中，识别到的每张脸都单独占一行，输出格式为<图片名称>,<人名>。
unknown_person 是一个与你的文件夹的已知人图像不匹配的人物。

### 调整公差/灵敏度

如果你正在为同一个人获得多个比较，那可能就是这样您的照片中的人看起来非常相似，容差值较低需要使脸部比较更严格。
你可以用`--tolerance`参数来做到这一点。默认容差值为0.6，较低的数字使脸部比较更严格：

```python
$ face_recognition --tolerance 0.54 ./pictures_of_people_i_know/ ./unknown_pictures/

/unknown_pictures/unknown.jpg,Barack Obama
/face_recognition_test/unknown_pictures/unknown.jpg,unknown_person
```

如果要按顺序查看每次计算出的面距要调整公差设置，可以使用：`--show-distancetrue`

```python
$ face_recognition --show-distance true ./pictures_of_people_i_know/ ./unknown_pictures/

/unknown_pictures/unknown.jpg,Barack Obama,0.378542298956785
/face_recognition_test/unknown_pictures/unknown.jpg,unknown_person,None
```

### 更多例子

如果你只想知道每张照片中的人的名字，但不要关心文件名，你可以这样做：

```python
$ face_recognition ./pictures_of_people_i_know/ ./unknown_pictures/ | cut -d ',' -f2

Barack Obama
unknown_person
```

### 加快人脸识别

如果您有多个CPU内核的电脑，则可以并行完成脸部识别。例如，如果您的系统有4个CPU内核，您可以通过使用在相同的时间量内所有的CPU内核并行处理约4倍的图像。
如果您使用的是Python 3.4或更新版本，请传入参数：`--cpus <number_of_cpu_cores_to_use> parameter`:

```python
$ face_recognition --cpus 4 ./pictures_of_people_i_know/ ./unknown_pictures/
```

您还可以传入使用系统中的所有CPU内核。--cpus-1

### Python模块

你可以通过导入face_recognition模块来使用它，使用方式超级简单，文档在这里[API文件](https://face-recognition.readthedocs.io/en/latest/face_recognition.html)：

### 自动找到图片中所有的脸

```python
import face_recognition

image = face_recognition.load_image_file("my_picture.jpg")
face_locations = face_recognition.face_locations(image)

# face_locations is now an array listing the co-ordinates of each face!
```

看看[这个例子](https://github.com/ageitgey/face_recognition/blob/master/examples/find_faces_in_picture.py)自己实践一下，试试看。

你还可以自定义替换人类识别的深度学习模型。

注意：想获得比较好的性能的话，你可能需要GPU加速（使用英伟达的CUDA库）。所以编译的时候你也需要开启dlib的GPU加速选项。

```python
import face_recognition

image = face_recognition.load_image_file("my_picture.jpg")
face_locations = face_recognition.face_locations(image, model="cnn")

# face_locations is now an array listing the co-ordinates of each face!
```

你也可以通过[这个例子](https://github.com/ageitgey/face_recognition/blob/master/examples/find_faces_in_picture_cnn.py)实践一下，试试看。

如果你有很多图片和GPU，你也可以并行快速识别，看[这篇文章](https://github.com/ageitgey/face_recognition/blob/master/examples/find_faces_in_batches.py)。

### 自动识别人脸特征

```python
import face_recognition

image = face_recognition.load_image_file("my_picture.jpg")
face_landmarks_list = face_recognition.face_landmarks(image)

# face_landmarks_list is now an array with the locations of each facial feature in each face.
# face_landmarks_list[0]['left_eye'] would be the location and outline of the first person's left eye.
```

试试[这个例子](https://github.com/ageitgey/face_recognition/blob/master/examples/find_facial_features_in_picture.py)，试试看。

### 识别人脸鉴定是哪个人

```python
import face_recognition

picture_of_me = face_recognition.load_image_file("me.jpg")
my_face_encoding = face_recognition.face_encodings(picture_of_me)[0]

# my_face_encoding now contains a universal 'encoding' of my facial features that can be compared to any other picture of a face!

unknown_picture = face_recognition.load_image_file("unknown.jpg")
unknown_face_encoding = face_recognition.face_encodings(unknown_picture)[0]

# Now we can see the two face encodings are of the same person with `compare_faces`!

results = face_recognition.compare_faces([my_face_encoding], unknown_face_encoding)

if results[0] == True:
    print("It's a picture of me!")
else:
    print("It's not a picture of me!")
```

这里是一个[例子](https://github.com/ageitgey/face_recognition/blob/master/examples/recognize_faces_in_pictures.py)，试试看。

## Python代码示例

所有的例子都[在这里](https://github.com/ageitgey/face_recognition/tree/master/examples)。

### 人脸检测

- [在照片中找到面孔](https://github.com/ageitgey/face_recognition/blob/master/examples/find_faces_in_picture.py)
- [在照片中找到面孔（使用深度学习）](https://github.com/ageitgey/face_recognition/blob/master/examples/find_faces_in_picture_cnn.py)
- [在GPU（使用深度学习）的图像批量查找面孔](https://github.com/ageitgey/face_recognition/blob/master/examples/find_faces_in_batches.py)

### 面部特征

- [识别照片中的特定面部特征](https://github.com/ageitgey/face_recognition/blob/master/examples/find_facial_features_in_picture.py)
- [应用（可怕的丑陋）数字化妆](https://github.com/ageitgey/face_recognition/blob/master/examples/digital_makeup.py)

### 面部识别

- [根据已知人的照片，查找并识别照片中的未知脸部](https://github.com/ageitgey/face_recognition/blob/master/examples/recognize_faces_in_pictures.py)
- [通过数字表面距离比较面部，而不是仅True / False匹配](https://github.com/ageitgey/face_recognition/blob/master/examples/face_distance.py)
- [使用您的网络摄像头识别实况视频中的人脸 - 简单/较慢版本（需要安装OpenCV）](https://github.com/ageitgey/face_recognition/blob/master/examples/facerec_from_webcam.py)
- [使用您的网络摄像头识别实况视频中的人脸 - 更快的版本（需要安装OpenCV）](https://github.com/ageitgey/face_recognition/blob/master/examples/facerec_from_webcam_faster.py)
- [识别视频文件中的面孔并写出新的视频文件（需要安装OpenCV）](https://github.com/ageitgey/face_recognition/blob/master/examples/facerec_from_video_file.py)
- [识别Raspberry Pi w /相机的面孔](https://github.com/ageitgey/face_recognition/blob/master/examples/facerec_on_raspberry_pi.py)
- [运行Web服务通过HTTP识别面孔（需要安装Flask）](https://github.com/ageitgey/face_recognition/blob/master/examples/web_service_example.py)

### 人脸识别如何运作

如果你想了解脸部位置和识别如何工作，而不是取决于黑匣子库，请[阅读我的文章](https://medium.com/@ageitgey/machine-learning-is-fun-part-4-modern-face-recognition-with-deep-learning-c3cffc121d78)。

### 注意事项

- 面部识别模型是针对成年人进行培训的，对儿童的工作效果不佳。它倾向于使用默认比较阈值0.6来混合孩子很容易。

### 部署到云端主机（Heroku，AWS等）

由于`face_recognition`取决于使用`dlib`C ++编写的内容，将其用于云端托管服务商，如Heroku或AWS 部署应用程序可能很棘手。
为了使事情更容易，这个repo中有一个Dockerfile示例，显示如何在[Docker](https://www.docker.com/)容器中运行一个构建的应用程序`face_recognition`。因此，您应该可以部署支持Docker图像的任何服务。

### 常见问题

问题：使用face_recognition或运行示例时出现 `Illegal instruction(coredumped)`

解决方案：`dlib`使用SSE4或AVX进行编译，但您的CPU太旧，不支持。
您需要更改`dlib`代码后重新编译[这里概述代码更改](https://github.com/ageitgey/face_recognition/issues/11#issuecomment-287398611)。

问题：运行摄像头示例时出现`RuntimeError: Unsupported image type, must be 8bit gray or RGB image.`

解决方案：您的网络摄像机可能未正确使用OpenCV设置。[在这里寻找更多帮助](https://github.com/ageitgey/face_recognition/issues/21#issuecomment-287779524)。

问题：运行`pip2 install face_recognition`时出现`MemoryError`

解决方案：face_recognition_models文件对于可用的缓存内存来说太大了。尝试使用以下方法`pip2 --no-cache-dir install face_recognition`来尽量避免这个问题。

问题：`AttributeError: 'module' object has no attribute 'face_recognition_model_v1'`

解决方案：`dlib`您安装的版本太旧了。您需要19.7或更新版本。升级`dlib`。

问题：`AttributeError: 'Module' object has no attribute 'cnn_face_detection_model_v1'`

解决方案：`dlib`您安装的版本太旧了。您需要19.7或更新版本。升级`dlib`。

问题：`TypeError: imread() got an unexpected keyword argument 'mode'`

解决方案：`scipy`您安装的版本太旧了。您需要版本0.17或更新版本。升级`scipy`。

### 谢谢

- 非常感谢[戴维斯·金](https://github.com/davisking)（[@nulhom](https://twitter.com/nulhom)）创建dlib并提供训练有素的面部特征检测和此库中使用的脸部编码模型。有关ResNet上有关面部编码的更多信息，请查看他的[博文](http://blog.dlib.net/2017/02/high-quality-face-recognition-with-deep.html)。
- 感谢所有在Python数据科学图书馆工作的所有人，如data，scipy，scikit-image，pillow等，使得这种东西在Python中如此简单而有趣。
- 感谢[Cookiecutter](https://github.com/audreyr/cookiecutter)和[audreyr / cookiecutter-pypackage](https://github.com/audreyr/cookiecutter-pypackage)项目模板，使Python项目打包方式更有效率。