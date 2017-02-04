---
title: Tensorflow 学习之路
date: 2017-02-04 10:47:11
tags:
    - 人工智能
---

## Tensorflow 示例

收集了一些Tensorflow可以做的一些有意思的事情：

1. 图像风格转换,生成各种有意思的图
 
    https://github.com/anishathalye/neural-style
    https://github.com/fzliu/style-transfer

2. 给素描黑白画，自动上色
https://github.com/pfnet/PaintsChainer

3. 生成古诗词：基于RNN生成古诗词
http://blog.topspeedsnail.com/archives/10542

4. 结合RL玩游戏的，愤怒的小鸟，超级马里奥
https://github.com/yenchenlin/DeepLearningFlappyBird

5. 用TensorFlow实现MarioKart游戏自动驾驶
https://github.com/kevinhughes27/TensorKart

6. Udacity自驾模拟项目Nanodegree
https://github.com/upul/behavioral_cloning

## Tensorflow 资料

1. Tensorflow从入门到精通
https://github.com/aymericdamien/TensorFlow-Examples

## Tensorflow 体验

### neural-style

#### 下载源代码

```bash
$ git clone https://github.com/anishathalye/neural-style.git
```

#### 下载训练集

```url
http://www.vlfeat.org/matconvnet/models/beta16/imagenet-vgg-verydeep-19.mat
```

#### 安装Python依赖

```
$ pip install 
```

#### 运行应用

```
$ python neural_style.py --content examples/1-content.jpg --styles examples/1-style.jpg --output output.jpg
```

#### 查看结果

![原图](/images/hhkb-keyboard.jpg)

![样式图](/images/1-style.jpg)

![结果图](/images/hhkb-keyboard.jpg)

## Tensorflow 安装

### 源代码安装

#### 获取源代码

```bash
$ git clone https://github.com/tensorflow/tensorflow
```

#### 准备安装环境

在Mac上安装bazel，建议使用[homebrew](http://brew.sh/)安装依赖

```bash
$ brew install bazel 
```

可以使用easy_install或pip安装python依赖，如果使用easy_install运行下面的命令:
```bash
$ sudo easy_install -U six
$ sudo easy_install -U numpy
$ sudo easy_install wheel
```

推荐使用[ipython](https://ipython.org/)，运行下面的命令安装：
```bash
$ sudo easy_install ipython
```

#### 配置安装

```bash
$ ./configure
Please specify the location of python. [Default is /usr/local/bin/python]:
Please specify optimization flags to use during compilation when bazel option "--config=opt" is specified [Default is -march=native]:
Do you wish to build TensorFlow with Google Cloud Platform support? [y/N]
No Google Cloud Platform support will be enabled for TensorFlow
Do you wish to build TensorFlow with Hadoop File System support? [y/N]
No Hadoop File System support will be enabled for TensorFlow
Do you wish to build TensorFlow with the XLA just-in-time compiler (experimental)? [y/N]
No XLA support will be enabled for TensorFlow
Found possible Python library paths:
  /usr/local/Cellar/python/2.7.12_2/Frameworks/Python.framework/Versions/2.7/lib/python2.7/site-packages
  /Library/Python/2.7/site-packages
Please input the desired Python library path to use.  Default is [/usr/local/Cellar/python/2.7.12_2/Frameworks/Python.framework/Versions/2.7/lib/python2.7/site-packages]

Using python library path: /usr/local/Cellar/python/2.7.12_2/Frameworks/Python.framework/Versions/2.7/lib/python2.7/site-packages
Do you wish to build TensorFlow with OpenCL support? [y/N]
No OpenCL support will be enabled for TensorFlow
Do you wish to build TensorFlow with CUDA support? [y/N]
No CUDA support will be enabled for TensorFlow
Configuration finished
INFO: Starting clean (this may take a while). Consider using --expunge_async if the clean takes more than several minutes.
..........
INFO: All external dependencies fetched successfully.
```

#### 编译安装

```bash
$ bazel build -c opt //tensorflow/tools/pip_package:build_pip_package

$ bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg

# The name of the .whl file will depend on your platform.
$ pip install /tmp/tensorflow_pkg/tensorflow-0.12.1-cp27-cp27m-macosx_10_11_x86_64.whl
```

#### 测试

```python
>>> import tensorflow as tf
>>> hello = tf.constant('Hello, TensorFlow!')
>>> sess = tf.Session()
>>> sess.run(hello)
Hello, TensorFlow!
>>> a = tf.constant(10)
>>> b = tf.constant(32)
>>> sess.run(a+b)
42
>>>
```

