---
title: "树莓派各个版本参数明细"
date: 2021-07-11T13:08:00+08:00
slug: "raspberry-pi-model-specifications"
description: "系统整理 Raspberry Pi（树莓派）各主要型号的硬件参数与基础信息，包括发布时间、CPU、内存规格、接口能力等，作为长期可查阅的入门参考资料。"
summary: "系统整理 Raspberry Pi（树莓派）各主要型号的硬件参数与基础信息，包括发布时间、CPU、内存规格、接口能力等，作为长期可查阅的入门参考资料。"
categories:
  - 树莓派
tags:
  - Raspberry Pi
  - 参数对比
  - 硬件规格
  - 入门指南
aliases:
  - /raspberry-pi/210.html
---

> 本文最初整理于 **2021 年**，用于对 Raspberry Pi 各主要型号的硬件参数进行基础汇总。
> 随着后续新型号发布，本文在 **保留原有结构的基础上** 进行了补充更新，作为长期参考资料使用。

---

## 什么是树莓派？

### 树莓派简介

Raspberry Pi（中文名为「树莓派」）是一种信用卡大小的单板计算机，由英国 **Raspberry Pi 基金会** 推动和维护，主要用于教育、学习编程、嵌入式开发以及轻量服务器等场景。

树莓派可运行多种 Linux 系统，其官方推荐系统为 **Raspberry Pi OS（原名 Raspbian）**，部分型号也支持 Windows 10 IoT。

“Raspberry Pi” 这一名称来源于水果树莓（Raspberry）。在国外，用生活化事物为技术产品命名并不罕见，例如 MariaDB、Taylor 吉他等。

---

## 树莓派基础信息（通用）

- 发行与维护：Raspberry Pi 基金会
- 发源国家：英国
- 项目带头人：Eben Upton
- 初代产品发布时间：2012 年
- 官方网站：https://www.raspberrypi.org
- CPU 供应商：Broadcom（博通）
- 指令集架构：ARM
- 常见系统架构：armhf（32 位）、aarch64（64 位）
- 官方系统：Raspberry Pi OS
- 官方烧录工具：Raspberry Pi Imager

---

## 树莓派 Zero 系列

### Raspberry Pi Zero / Zero W

**定位：超低功耗 / 轻量级项目**

- 发布时间：2015（Zero）、2017（Zero W）
- CPU：1GHz 单核 ARM
- 内存：512MB
- 无线（Zero W）：802.11 b/g/n、蓝牙 4.1 / BLE
- 显示接口：Mini HDMI
- USB：Micro USB（电源 + OTG）
- GPIO：40 针（需焊接）

---

### Raspberry Pi Zero 2 W

> **更新补充型号（2021 年后）**

- 发布时间：2021 年
- CPU：四核 ARM Cortex-A53 @ 1GHz（BCM2710A1）
- 内存：512MB
- 无线：802.11 b/g/n、蓝牙 4.2 / BLE
- 说明：
  在体积与功耗几乎不变的前提下，性能相比 Zero W 有明显提升，是 Zero 系列中更推荐的选择。

---

## 树莓派 3 系列

### Raspberry Pi 3 Model B

- 发布时间：2016 年
- CPU：Broadcom BCM2837，四核 Cortex-A53 @ 1.2GHz
- 内存：1GB
- 无线：2.4GHz Wi-Fi、蓝牙 BLE
- 有线网卡：100Mbps
- HDMI：全尺寸 HDMI
- USB：4 × USB 2.0
- 电源：5V 2.5A Micro USB

---

### Raspberry Pi 3 Model B+

- 发布时间：2018 年
- CPU：BCM2837B0，四核 Cortex-A53 @ 1.4GHz
- 内存：1GB LPDDR2
- 无线：2.4G / 5G Wi-Fi、蓝牙 4.2 / BLE
- 有线网卡：千兆（实际吞吐约 300Mbps）
- USB：4 × USB 2.0
- 支持 PoE（需 PoE HAT）

---

## 树莓派 4 系列

### Raspberry Pi 4 Model B

- 发布时间：2019 年
- CPU：Broadcom BCM2711，四核 Cortex-A72 @ 1.5GHz
- 内存版本：2GB / 4GB / 8GB LPDDR4
- HDMI：2 × Micro-HDMI（支持双屏 4K）
- USB：2 × USB 3.0 + 2 × USB 2.0
- 网络：真千兆以太网
- 无线：2.4G / 5G Wi-Fi，蓝牙 5.0 / BLE
- 电源：USB-C（5V / 3A）
- 视频解码：H.265 4Kp60

> 维护声明：
> Raspberry Pi 4 Model B 计划长期生产，至少持续至 **2026 年以后**。

---

## Raspberry Pi 400

**定位：桌面学习 / 键盘一体式**

- 发布时间：2020 年
- CPU：BCM2711，四核 Cortex-A72 @ 1.8GHz
- 内存：4GB LPDDR4
- 网络：千兆以太网
- USB：USB 3.0 ×2，USB 2.0 ×1
- 特点：
  键盘一体化设计，更接近传统桌面电脑形态，适合教学与 Linux 入门。

---

## Raspberry Pi 5

> **重要代际升级（2023 年发布）**

- 发布时间：2023 年
- CPU：Broadcom BCM2712，四核 Cortex-A76 @ 2.4GHz
- 内存：4GB / 8GB LPDDR4X
- GPU：VideoCore VII
- USB：2 × USB 3.0 + 2 × USB 2.0
- PCIe：通过 FPC 提供 PCIe 2.0 通道
- 电源：USB-C（官方建议 5V / 5A）
- 说明：
  Raspberry Pi 首次在 CPU 架构与整体性能上出现明显跨代提升，应用场景进一步扩展。

---

## Compute Module 系列（简述）

- Compute Module 3 / 3+：基于 Raspberry Pi 3
- Compute Module 4 / 4+：基于 Raspberry Pi 4
- 特点：
  - 无标准接口
  - 需搭配载板使用
  - 主要面向工业与定制化场景

---

## 关于 Raspberry Pi Pico

Raspberry Pi Pico 基于 **RP2040 微控制器**，不运行 Linux，更接近 Arduino 类产品。

> 本文 **不将 Pico 系列纳入型号参数对比**，以避免与单板计算机混淆。

---

## 简要选型建议（补充）

- **入门 / 学习 Linux**：Raspberry Pi 4B / 5
- **轻量项目 / 嵌入式**：Zero 2 W
- **桌面使用 / 教学**：Raspberry Pi 400 / 5
- **工业 / 定制硬件**：Compute Module 系列

---

## 结语

本文定位为 **长期可查阅的树莓派型号与参数参考文档**，不追求实时跑分或性能评测。
后续如有新的重要型号发布，可在现有结构基础上继续补充更新。

