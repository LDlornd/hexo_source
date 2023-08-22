---
title: "[论文解读]FLIN: A Flexible Natural Language Interface for Web Navigation"
author: lornd
mathjax: true
date: 2023-08-22 16:08:29
url: flin
tags:
---

论文地址：[FLIN: A Flexible Natural Language Interface for Web Navigation](https://arxiv.org/abs/2010.12844) 。

## 摘要

AI 助手现在可以通过直接与网页 UI 交互为用户完成任务。但是如果没有持续的再训练，现有的语义解析技术和槽填充技术无法灵活地适应各种不同的网站。

我们提出了 FLIN，一个用于网页导航的自然语言接口，可以将用户命令映射为概念层次的动作（而不是低层次的 UI 动作），进而可以灵活地适应不同的网站，并且处理网站的瞬时性。

我们将这个问题建模成排序问题：给定用户命令和一个网页，FLIN 学习对最相关的导航指令（包括动作和参数值）进行评分。为了训练和测试 FLIN，我们从三个领域的九个热门网站中收集了数据集。我们的结果表明：在给定的领域内，FLIN 能够适应新的网站。

## 研究背景

AI 个人助手现在可以通过直接与网页 UI 交互完成人类的任务。用户命令 AI ，AI 通过输入、选择项目、点击按钮，以及在不同的网页中进行导航等动作执行人类的命令。这样的解决方案十分吸引人，因为它降低了对第三方 API 的依赖，并且扩展了 AI 助手的能力。本文关注这些系统中的一个关键部件：一个能够将用户命令转换为网页浏览器能够执行的导航命令的自然语言接口。

一个实现这种自然语言接口的方法是直接将自然语言映射为低层次的 UI 动作。在网页中，UI 元素通过串联其 DOM 属性进行嵌入表示。然后，学习一个评分函数或者神经策略以判断哪个 UI 元素能够最好地匹配给定的命令。在 UI 元素的层次进行学习十分高效，但是只在受控制（UI 元素并不随时间变化）或者受限制（单个应用）的环境中有效。这和真实网页的情况是冲突的：（1）网站总是在更新；（2）一个用户可能会让助手在不同网页上执行相同的任务。为了处理网页的瞬时性和多样性，我们需要一个在不经过持续在训练的情况下，能够灵活地适应有着变量和未知行为的环境的自然语言接口。

为了达到这个目标，我们采用两个步骤。首先，我们提出一种为网页导航设计自然语言接口的新方法。我们不将自然语言命令映射为低层次的 UI 操作，而是将它们映射为具有丰富信息的概念层次的动作。概念层次的动作用于表达当用户在看网页 UI 时，他们看到了什么。如图 1 所示：OpenTable 的首页有一个概念层次的动作“Let's go”（一个搜索按钮），这表示了一个搜索的概念，并且可以通过各种参数进行具体化。直观上来说，同一个领域内的网站会有着语义相似的概念层次动作，并且人类任务的语义不会随着时间变化。因此，在概念层次学习动作，可以得到一个更加灵活的自然语言接口。

{% gallery %}
![自然语言驱动的网页任务示例](/images/flin/fig1.png)
{% endgallery %}

然而，虽然概念层次的动作比原始 UI 元素种类更少，在不同的网站上，它们还是会有不同的表现形式和不同的参数。例如：在 opentable.com 上搜索餐馆，对应的是“Let's go”，并且支持多达四个参数；而在 yelp.com 上，对应的是“Search”按钮，并且只支持两个参数。并且同一个领域的网站，也会有不同种类的动作（如搜索餐馆和点外卖）。

我们解决问题的第二步是采用了一种新的语义解析方法。传统的语义解析方法是用于处理有着固定且已知的动作的环境，不能直接使用。因此，我们提出了 FLIN，一种新的语义解析方法，其中我们不是学习如何将自然语言命令映射成可执行的逻辑形式，而是利用逻辑形式中符号的语义信息，来学习如何与给定的命令进行匹配。具体地，我们将语义解析任务建模成一个排序问题：给定自然语言命令 $c$ 和在网页上可执行的动作集合 $A$ ，FLIN 根据 $c$ 为每一个动作打分。同时，对于一个动作的每个参数 $p$ ，从 $c$ 中提取一个可以表达 $p$ 值的解析 $m$ ，然后根据 $m$ 对 $p$ 值进行评分，以得到 $p$ 的最佳评分。每个动作及其相关的参数值列表都表示一个待排序的候选导航指令。FLIN 基于对应动作和参数值分数学习每个指令的网络得分，并且输出得分最高的指令作为预测的导航指令。

为了收集能够训练和测试 FLIN 的数据集，我们构建了一个简单的基于规则的动作提取工具以从网页中提取概念层次的动作及其参数（包括名称和参数值）。如图 1 所示：在一个完整的系统中，我们设想动作提取器提取当前网页中的概念层次动作，并将其传递给 FLIN，FLIN 计算出一个由动作执行器执行的候选导航指令。

## 文章贡献

总体来看，本文的贡献有三点：

1. 基于概念层次的动作，提出了一个为网页导航设计自然语言接口的新方法。
2. 我们建立了一个基于匹配的语义解析器，以将自然语言命令映射为导航命令。
3. 我们基于九个网站（包括餐饮、旅馆和购物领域）收集了一个新的数据集，并且提供了实证结果，以证明我们方法的泛化性。

代码和数据集开源于：https://github.com/microsoft/flin-nl2web 。

## 问题描述

## 解决方法

## 实验验证