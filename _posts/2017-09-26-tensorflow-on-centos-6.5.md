---
layout: post
title: "CentOS 6.5安装tensorflow"
category: ml 
tags: ["tensorflow", "centos", "glibc"]
---


由于工作需要，要在系统古老的开发机器上安装tensorflow。

毫无疑问，直接pip install的tensorflow肯定没法运行，不然就不会有这篇文字了。

前期的准备工作包括：
 
* 下载tensorflow
* 安装python 3
* 下载gcc-4.9.4
* 下载glibc-2.14

主要的步骤如下：

1. 安装新版本python，注意最好不要覆盖系统原有的python，否则会破坏yum，可手动编辑yum，将其python版本明确指定为python2.6

	```python
	#!/usr/bin/python2.6
	```
2. 升级gcc，可以直接下载源码编译安装，网上教程很多，不再啰嗦
3. 升级glibc，这里需要提醒一下，glibc升级有风险会破坏原有的软件系统，所以安全起见，我在/opt/新建了一个目录专门用来安装glibc-2.14，使用2.14的源码编译安装完成之后，需要手动配置一下环境变量：

	```
		
		[www@dev-hdp002 downloads]$ cat ~/.bashrc 

		export LD_LIBRARY_PATH=opt/glibc-2.14:$LD_LIBRARY_PATH
	```
4. 需要从源码编译tensorflow，生成whl包，然后再本地安装whl包即可，需要下载安装bazel，用bazel编译tensorflow，这部分参考了：[万能的SO](https://stackoverflow.com/questions/33655731/error-while-importing-tensorflow-in-python2-7-in-ubuntu-12-04-glibc-2-17-not-f/41003023#41003023)

主要的步骤：

```
 ./configure

    bazel build -c opt --config=cuda //tensorflow/tools/pip_package:build_pip_package

    bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg

    pip install --upgrade /tmp/tensorflow_pkg/tensorflow-${version}.whl
```

我使用的各个组件的版本：

* gcc 4.9.4
* glibc 2.14
* bazel 0.5.2
* python 3.6.2
* tensorflow 1.3.0

```python

	import tensorflow as tf

	sess = tf.Session()

	hello = tf.constant('Hello, TensorFlow!')

	print(sess.run(hello))

	>>>b'Hello, TensorFlow!'
	
	a = tf.constant(10)
	b = tf.constant(32)
	print(sess.run(a + b))

	>>>42
```