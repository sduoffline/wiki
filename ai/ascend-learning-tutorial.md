---
title: 华为Ascend昇腾学习笔记
description: 
published: true
date: 2023-07-24T09:38:43.745Z
tags: 
editor: markdown
dateCreated: 2023-07-24T09:38:28.221Z
---

# 华为Ascend昇腾学习笔记

## 简述

[昇腾官网文档](https://www.hiascend.com/document)部分共提供了如下几个部分的文档：

- 异构计算架构：包括CANN社区版和商业版。
- 应用使能MindX：包括MindX DL、MindX Edge和MindX SDK。
- 全流程开发工具：MindStudio。
- Atlas系列硬件：Atlas 200I DK A2、Atlas 200 DK以及一系列加速模块、加速卡等。
- AI框架：MindSpore和TF、PyTorch的适配。

本文集中学习异构计算架构（即CANN社区版）和Atlas开发版的使用。

![华为昇腾计算生态 ](https://s2.loli.net/2023/07/24/SJhI4Dt5GRmpz7A.png)

## 异构计算

![Ascend 910 AI Processor架构](https://s2.loli.net/2023/07/24/emBn3o2Wgr7LqHh.png)

![Ai Core计算单元结构](https://s2.loli.net/2023/07/24/IKpvhtafY6LQBJ2.png)

### CANN逻辑架构

![CANN逻辑架构示意图](https://s2.loli.net/2023/07/24/rRtmaGINBhk1Hpn.png)

> 与TensorRT的功能和位置是相似的，但是涉及到各家芯片的具体优化。

根据多层级设计，包括：

- **统一APP编程语言**：提供一套标准的`AscendCL`编程接口，对应用程序开发者屏蔽底层多种芯片差异，提升用户APP编程易用性。
- **统一网络构图接口**：提供了标准的昇腾计算`AIR`，支持多框架，支持用户在昇腾芯片上快速部署神经网络业务。
- **高性能计算引擎以及算子库**：通过高性能编译引擎/ 执行引擎/调优引擎和预置高性能算子库，支撑客户快速部署神经网络业务、降低部署成本并最大程度发挥昇腾计算能力。
- **基础服务**：驱动、虛拟化、媒体、集合通信等能力。

![昇腾计算编译层示意图](https://s2.loli.net/2023/07/24/gEHxX4dsPmQF2ev.png)

![昇腾计算执行层](https://s2.loli.net/2023/07/24/PrSVYZzfjcBlUNK.png)

![昇腾计算基础层](https://s2.loli.net/2023/07/24/Dm1Q7PIaJubfEWp.png)

### CANN模型训练与调优

HCCL（Huawei Collective Communication Library）提供单机多卡、多机多卡的通信原语。

![HCCL示意图](https://s2.loli.net/2023/07/24/RGcXfTiqQkzHunw.png)

![算子编译运行流程](https://s2.loli.net/2023/07/24/6MnIfGurtzqPl8L.png)

### CANN算子

### CANN应用

## 模型开发

## 应用开发

## 算子开发

## 全流程工具链

