---
title: OSG踩坑记-模型共享&模型颜色修改
date: 2021-03-20 20:54:22
categories: 
- works
- GF
- OSG
tags: 
- cpp
- osg
---

###  简单概述

在很多场景中，我们需要将同一个模型绘制到不同的区域并且还需要对模型进行一系列的旋转缩放等操作，同时需要使用不同的颜色，这个时候我们只需要使用一些简单的设置便可以实现这个功能功能。

### 伪代码：

#### 1、读入模型

```c++
// 无论你是从外部读入模型 还是你自己构建的一个模型都是可以的。
// 使用osg自带读取模型的函数读取模型
// 推荐使用 osg 的智能指针
// 此处使用一个 内嵌的几何体 代替模型 

osg::ref_ptr<osg::Geometry> geom = new osg::Geometry; 
// osg 的智能指针采用引用计数的方式决定当前对象是否释放
```

#### 2、计算对模型的旋转、缩放、平移的操作矩阵

```C++
// osg 有专门用户模型操作的类  osg::MatrixTransform 
// 创建一个对象 模型操作类中抽象了矩阵的接口 可以当做矩阵操作 本质上是一个节点 
osg::ref_ptr<osg::MatrixTransform> trans = new osg::MatrixTransform;
// 对于模型的操作，有PVM矩阵的意思 

// 创建旋转矩阵 
osg::Matrix matrix_rotate;
// 构建旋转，从osg坐标系的沿着Z轴方向转到X轴（都是正方向）  
matrix_rotate.makeRotate(osg::vec3(0,0,1),osg::vec3(1,0,0));  // 参数分别是两个方向向量 第一个参数是当前模型的方向 第二个参数是你需要旋转的方向
// 你也可以直接选择使用
osg::Matrix::rotate(osg::Vec3(),osg::Vec3()); // 输入的参数也可以是四元数  返回值是一个表示旋转的矩阵

// ==============================================================================================
// 也可以直接 设置最终的变换矩阵 
trans->setMatrix(matrix_rotate * osg::Martix::scale(1.0,1.0,1.0) * osg::translate(osg::Vec3()));
// trans 已经存储了 我们对于模型的操作 
```

#### 3、对trans节点设置材料等属性 

```C++
// 创建材质对象
osg::Material* mat = new osg::Material();
mat->setColorMode(osg::Material::ColorMode::DIFFUSE);   // 设置绘制颜色的模式 
mat->setDiffuse(osg::Material::FRONT, osg::Vec4());      // 设置此种模式下的颜色 

trans->getOrCreateStateSet()->setAttribute(mat);        // 将材质设置给 节点 
```

#### 4、将对象作为节点添加

```C++
trans.addchild(geom.get());
```

#### 5、将操作节点添加到绘制的根节点或者是其他的叶节点

```C++
// ......
```

