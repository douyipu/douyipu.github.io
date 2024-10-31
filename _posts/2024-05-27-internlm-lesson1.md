---
layout: post
title: 书生·浦语大模型-实战营第二期-课程一
date: 2024-05-30 11:12:00-0400
description: 本文介绍了上海人工智能实验室推出的开源大模型 InternLM，以及配套的数据处理、微调、部署、评测等开放体系组件。
tags: 书生·浦语大模型 InternLM
giscus_comments: true
---

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0" style="text-align:center;">
        {% include figure.html path="assets/img/mark-twain.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

<br/>

## 摘要

本文介绍了上海人工智能实验室推出的开源大模型InternLM及其相关的开源开放体系。主要内容包括：
1. 上海人工智能实验室及上海人工智能创新中心的背景介绍。
2. InternLM 大模型的发展现状及“书生·浦语大模型-实战营”的介绍。
3. 开源开放体系的各个组件，包括数据平台 OpenDataLab、微调工具 XTuner、部署工具 LMDeploy、评测体系 OpenCompass 司南和智能体工具 AgentLego 等。
4. 每个组件的功能和特点简介。

<br/>

## 背景介绍

### 上海人工智能实验室

[上海人工智能实验室](https://www.shlab.org.cn/aboutus)是由**上海人工智能创新中心**（事业单位）设立的研发机构。

<br/>

### 上海人工智能创新中心

信息来源：[企查查](https://www.qcc.com/crun/gb763cde408bc79a86548e43bc1daa8b.html)，下载 CSV 格式的[数据](https://github.com/douyipu/douyipu.github.io/blob/master/assets/database/shanghai-ai-hire.csv)。

职位类型：
- 研究员/青年研究员（如内容生成、视觉多模态大模型、语言大模型等）
- 工程师（如大模型训练系统研发、深度学习芯片评测、深度学习编译研究等）
- 算法工程师/开发工程师（如OpenLMLab算法开发、深度学习训练框架算法等）
- 项目经理（如大模型安全项目经理）
- 高校合作主管
- 技术运营

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0" style="text-align:center;">
        {% include figure.html path="assets/img/shanghai-ai-position-distribution.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

月薪：
- 大部分职位的月薪范围在20-60K，并且大多数是15薪
- 最高月薪范围为30-60K·15薪，多见于博士学历的研究员职位
- 最低月薪范围为13-26K·15薪，为高校合作高级主管职位

学历要求：
- 博士学历（20%）：主要针对研究员/青年研究员等职位
- 硕士学历（35%）：主要针对工程师、算法工程师等职位
- 本科学历（45%）：主要针对算法开发、系统研发等工程师职位，也有部分研究员职位可接受优秀的本科生

工作经验：
- 经验不限：多数职位对工作经验没有硬性要求
- 1-3年：主要针对算法开发工程师、高校合作主管等职位
- 3-5年：主要针对大语言模型算法研究员、高校合作高级主管等职位

工作地点：
- 绝大多数（98%）职位的工作地点都在上海

<br/>

### 书生·浦语大模型 InternLM

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0" style="text-align:center;">
        {% include figure.html path="assets/img/shusheng.jpg" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

近年来，以 ChatGPT、GPT-4 为代表的大语言模型（LLM）快速发展，在自然语言处理领域取得突破性进展，引发业界对通用人工智能（AGI）的广泛关注和讨论。为推动 LLM 技术的开放共享，上海人工智能实验室于 2021 年推出了中文领域的开源大模型 InternLM。InternLM 在中文数据规模、模型效果等方面达到了国际先进水平，促进了中文 LLM 生态的建设。

<br/>

### 书生·浦语大模型-实战营第二期

为进一步助力开发者掌握大模型技术，上海人工智能实验室在 InternLM 基础上，推出“书生·浦语大模型-实战营”系列活动。实战营秉承“从零开始，手把手教学”的理念，通过系统的课程和实践，帮助学员复现大模型从数据处理、模型搭建、预训练、应用部署的全流程，快速习得 LLM 开发应用技能。

具体查看：[学员手册](https://aicarrier.feishu.cn/wiki/KamPwGy0SiArQbklScZcSpVNnTb)。

<br/>

## 课程一：书生·浦语大模型开源开放体系的介绍

### 讲解视频和 PPT

<iframe src="//player.bilibili.com/player.html?isOutside=true&aid=1752403076&bvid=BV1Vx421X72D&cid=1484131437&p=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"></iframe>

下载 [PPT](https://github.com/douyipu/douyipu.github.io/blob/master/assets/pdf/%5B1%5D%E4%B9%A6%E7%94%9F%C2%B7%E6%B5%A6%E8%AF%AD%E5%A4%A7%E6%A8%A1%E5%9E%8B%E5%BC%80%E6%BA%90%E5%BC%80%E6%94%BE%E4%BD%93%E7%B3%BB_compressed.pdf)

视频主要讲解了 InternLM 的发展现状和书生·浦语大模型开源开放体系。

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0" style="text-align:center;">
        {% include figure.html path="assets/img/chain-open-source-system.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

### 数据：书生·万卷 OpenDataLab

[OpenDataLab](https://opendatalab.org.cn/) 是上海人工智能实验室的大模型数据基座团队打造的数据开放平台，现已成为中国大模型语料数据联盟开源数据服务指定平台，为开发者提供全链条的 AI 数据支持，应对和解决数据处理中的风险与挑战，推动 AI 研究及应用。

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0" style="text-align:center;">
        {% include figure.html path="assets/img/OpenDataLab.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

下载榜单：

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0" style="text-align:center;">
        {% include figure.html path="assets/img/Download-leaderboard.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

数据库示例：

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0" style="text-align:center;">
        {% include figure.html path="assets/img/wanjuan-cc.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

OpenDataLab 有来自不同平台的数据集，大部分使用的许可协议（License）是 CC BY 4.0，不过也有其他的许可协议，具体可以看这篇 OpenDataLab 讲解许可协议的文章：[规范使用开源数据集，一定要看License](https://opendatalab.org.cn/news/details/article/17)。

<br/>

### 微调：XTuner

[XTuner](https://github.com/InternLM/xtuner) 是一个高效、灵活、全能的轻量化**大模型微调工具库**，能够提高模型微调的速率。

<br/>

### 部署：LMDeploy

[LMDeploy](https://github.com/InternLM/lmdeploy) 是用于部署大语言模型的工具箱，可以加速大语言模型的在运行时的推理速度。

<br/>

### 评测：OpenCompass 司南

[OpenCompass 司南](https://opencompass.org.cn/home)是一个开源、高效、全面的**大模型的评测体系**及**开放平台**。具备：
- [CompassKit](https://github.com/open-compass/OpenCompass/) 开源评测工具
- [CompassHub](https://hub.opencompass.org.cn/home) 数据集社区
- [CompassRank](https://rank.opencompass.org.cn/home) 评测榜单

<br/>

#### CompassHub 数据集社区

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0" style="text-align:center;">
        {% include figure.html path="assets/img/compasshub.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

<br/>

#### CompassRank 评测榜单

CompassRank 提供了由 OpenCompass 评测体系的排名结果，分为**大语言模型**和**多模态模型**。

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0" style="text-align:center;">
        {% include figure.html path="assets/img/compassrank.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

<br/>

### 应用：AgentLego

[AgentLego](https://github.com/InternLM/agentlego) 是一个开源的工具，用于在大语言模型上连接**智能体（Agent）**。

<br/>

## 总结

上海人工智能实验室推出的 InternLM 大模型及其配套的开源开放体系，为中文大模型生态建设做出了重要贡献。该体系涵盖了大模型开发的全流程，包括数据、微调、部署、评测和应用等环节，为开发者提供了完整的工具支持。通过开源的方式,促进了大模型技术的开放共享，有助于推动人工智能技术的发展和应用。同时，实战营活动则为开发者掌握大模型技能提供了良好的学习渠道。

<br/>

## 拓展阅读

- [InternLM2 Technical Report (English)](https://arxiv.org/pdf/2403.17297)
- [InternLM2 技术报告（中文）](https://arxivtools.blob.core.windows.net/xueshuxiangzipaperhtml/2024_3_27/2403.17297.pdf)
- [InternLM2 Technical Report / 技术报告（中英文对照）](https://yiyibooks.cn/arxiv/2403.17297v1/index.html)
