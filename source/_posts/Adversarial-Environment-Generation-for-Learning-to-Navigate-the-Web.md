---
title: "[论文解读]Adversarial Environment Generation for Learning to Navigate the Web"
author: lornd
mathjax: true
date: 2023-08-20 14:38:00
url: aeg
tags:
---

论文地址：[Adversarial Environment Generation for Learning to Navigate the Web](https://arxiv.org/abs/2103.01991) 。

## 摘要

学习如何自动在网页中进行导航是一个困难的序列决策工作。状态空间和动作空间巨大，且具有组合特性，同时网站也是包含多个页面的动态环境。训练网页导航智能体的一个瓶颈是为训练环境提供一个可学习的课程，并且要求能够覆盖真实世界网站的多样性。因此，我们提出用对抗环境生成（Adversarial Environment Generation，AEG）方法生成具有挑战性的网页环境，以训练强化学习智能体。

- 我们提供了一个新的基准环境 gMiniWob ，使强化学习的对手能够使用组合原语学习生成任意复杂的网站。
- 为了训练这个对手，我们提出了一种新的技术，利用导航智能体获得的分数差来最大化遗憾。我们的实验结果表明：我们的方法在最小最大化遗憾 AEG 方面明显优于之前的方法。
- 遗憾目标训练对手设计一套对于导航智能体来说是“适中挑战”的课程。我们的实验结果表明：随着时间的推移，对手学会了生成逐渐变难的网页导航任务。
- 使用我们的技术训练的导航智能体学会了完成具有挑战性的，高维的网页导航任务，如表单填写，航班预定等。我们的实验结果表明：在一组具有挑战性的不可见测试环境中，通过我们提出的灵活的 b-PAIRED 技术训练的导航智能体的性能显著优于具有竞争力的自动化课程生成基线，包括了一个最先进的强化学习网页导航方法，并且在一些任务上达到了超过 $80\%$ 的准确率。

## 研究背景

本文的主要目的是训练一个强化学习智能体自动在网页中进行导航，将相关信息正确输入到未知的现实网站中。这种能力可以让用户发出如预定机票和发朋友圈等请求，并让强化学习智能体自动处理完成任务的细节。然而，现实网站的复杂性和多样性让这个任务非常困难。

为了让我们的智能体能够泛化到新的网站，我们让其直接在 DOM 树上进行操作，智能体必须正确选择并将信息填入到合适的 DOM 元素中。这让问题的状态-动作空间非常大。即使智能体能够在网页中导航到正确的表单，甚至选择了正确的元素，仍然会有很多的可以输入的值。为了解决这个问题，前人的研究利用了从专家演示进行动作克隆的方法。但是，这个方法十分脆弱，并且不能有效地泛化。为每个网站收集导航演示是不可能的，尤其在网页经常变化和更新的情况下。如果没有可用的演示数据，基于模仿学习的模型就不能够泛化到新的网站。

成功在大范围的现实网站中进行导航需要在大量可能的任务和环境中对智能体进行训练。问题在于如何创建这样一个任务分布，其不仅能够涵盖大多数现实任务，而且能够以可以让智能体进行学习课程的形式呈现。一种可能的做法是为网站人为设计一个提前定义好的课程。但是这种做法十分枯燥，耗时，易错，而且脆弱，设计者很容易忽略一些现实边缘情况。另外一种做法是使用域随机算法（Domain Randomization, DR）对网站的参数进行随机化，或者随着时间的推移自动增加一些参数以控制难度。然而，这些方法可能无法覆盖重要的测试案例，并且不能根据智能体当前的能力来调整参数配置的难度。

## 文章贡献

在本文中，我们利用最新的对抗环境生成（AEG）技术为富有挑战性的网页导航任务建立课程。具体地，我们训练一个对抗强化学习智能体学习在网站中创建新的页面，以利用当前正在学习网页导航的智能体的弱点。为了实验这个 AEG 网页设计技术，我们建立了一个新的框架 gMiniWoB ，以允许对抗智能体利用常见的设计基元构建网站，如导航栏、产品转盘、项目卡片、网络表单和购物车等。这个环境是开源的以促进后续的研究。

AEG 的目标是自动生成一个训练环境的课程，涵盖可能的网站空间，并由此实现到现实网站导航任务的泛化。然而，如果我们只是简单地应用一个最小最大对抗智能体，即对抗智能体试图让学习智能体的表现最糟糕，那么这种课程不太可能出现。这是因为对抗智能体的动机是创建一个尽可能难的网站，而不是根据学习智能体当前的技术水平调整网站难度。最近提出的一种 AEG 技术 PAIRED（Protagonist Antagonist Induced Regret Environment Design）训练对抗智能体去最大化遗憾值。我们通过两种新的算法改进了原始的 PAIRED 算法：

1. 我们提出了一种更加灵活的计算遗憾的方法，使得我们的算法梗不容易陷入局部最小值。
2. 我们引入了一个明确的预算机制，使得对抗智能体在学习智能体不能完成任务时，因为使得环境更复杂而受到惩罚；否则的话对抗智能体会因为使得环境变复杂而受到奖励。

本文总体有如下贡献：

- 一个新的基准环境 gMiniWoB ，通过允许利用组合设计基元来构建网站，增强 AEG 在网页导航中的应用。
- Flexible b-PAIRED 算法，可计算出一个更加稳定的对遗憾的估计，并直接鼓励对抗智能体根据学习智能体的表现调整环境的复杂程度。
- 实验结果表明：Flexible b-PAIRED 申城了一套包含越来越具有挑战性的网站的课程，并且训练出了能够在测试时成功泛化到复杂、未知网站的智能体。我们的方法明显超过了在最小最大遗憾 AEG 上的之前的工作，也包括了使用强化学习训练网页导航智能体的最先进的方法。

## 问题描述

### 网页导航问题

我们将网页导航问题定义为一个序列决策问题。我们训练一个智能体，其参数为一个网络 $\pi(a_t|s_t;\Theta_i)$ ，将输入的状态 $s_t$ 转换为输出的动作 $a_t$ 以最大化累计折损增益 $O = \sum\limits_{t=0}^T \gamma^tr_t$ ，其中 $r_t$ 是在 $t$ 时刻所获得的奖励，$\gamma$ 是折损因子，$T$ 是一个 episode 的长度。 **我们将网页和用户指令作为输入状态**。其中网页在每个时刻都会被动态更新，而用户指令在最开始就确定不变。我们使用 DOM 树表示网页，其中每个元素都用一个（属性，值）二元组的集合和一个特征数组进行表示。指令是字段的集合，其中字段都表示为（键，值）二元组。对于每个任务，键是固定的，但是值会基于用户的输入而动态变化。

**每个动作都被表示为（元素，字段）二元组，表示在元素上进行动作，并且将字段的值作为输入**，即将字段的值输入元素。智能体在每个 episode 的最后会收到一个**任务成功奖励** （$1.0$ 或 $-1.0$）；每当页面中的元素被更新，智能体会收到一个**基于潜力的奖励**；为了鼓励智能体进行高效地导航，每个时刻智能体都会收到一个**小惩罚**。

### PAIRED 算法

AEG 训练一个对抗策略 $\pi_E$ 以设计环境使得学习智能体策略 $\pi_P$ 的表现尽可能坏。假设 $R^P_i = \sum\limits_{t=1}^T \gamma^tr^P_t$ 表示学习策略在轨迹 $i$ 上所获得的总奖励值。在最小最大 AEG 中，对抗策略的目标很简单：$-R^P$ 。因此，极小极大对抗策略有动机创造过于困难或不可能的环境，这可能使得学习策略无法学习。然而，PAIRED 算法训练对抗策略最大化学习策略的遗憾值，定义为学习策略所获得回报与最有策略所获得回报的插值 $R^*-R^P$ 。当奖励函数包含对更高效地完成任务的激励时，对于那些在最优策略下，可以在几步之内完成，但是当前策略无法完成的简单任务，遗憾值会很高。因此，最大化遗憾的对抗策略会不断提出更加容易的任务，直到学习策略开始解决这些任务。这也让遗憾值成为 AEG 的一个理想目标。

为了估计遗憾值，PAIRED 算法引入了第三个智能体——反派智能体（策略为 $\pi_A$），它限制对抗智能体只能生成反派智能体能够完成的可行环境。当对抗智能体生成了一个环境 $E$ ，学习智能体和反派智能体都会在环境 $E$ 中收集 $M$ 条轨迹，其回报分别为 $R^P_1,\cdots,R^P_M,R^A_1,\cdots,R^A_M$，最后的遗憾值计算如下：

$${\rm Regret} = \max\limits_{i} R^A_i - \dfrac{1}{M}\sum\limits_{m=1}^MR^P_m$$

在这种情况下，如果对抗策略和反派策略协调一致并与学习策略达成纳什均衡，那么学习智能体将学会最小化遗憾。然而，在实践中，基于梯度的多智能体强化学习并没有收敛性保证，它们具有高度非稳态性，并且经常不会收敛。如果反派策略和对抗策略不能协调一致，那么 PAIRED 算法会通过优化反派策略以最小化遗憾值。在这种情况下，上式中的目标只会让学习策略学得像反派策略一样好。如果反派策略无法提升，或者达到一个局部最优值，那么对抗策略也就无法继续训练学习策略了。因此本文提出了一个改进的目标以解决这个问题。

## 解决方法

我们从一个空的网站开始，逐渐添加新的页面和页面之间的链接。由于我们通过 DOM 树来表示页面，我们重点关注 DOM 树的创建，并且假设页面之间的链接是根据绑定到某些元素的事件隐式定义的。

设计 DOM 树的最通用的方法是以自底向上的方法组合一组任意元素，但是这会生成大量的语义不一致的错误网站。如图 3 的第二个页面所示，文本框的顶部有一个标签“First Name”。现在，如果我们将这个标签插入到第一个页面中“Username”文本框的上面，那么这个网站的格式就会变得不正确，因为无法确定文本框是指“Username”还是“First Name”。

{% gallery %}
![网页生成示例](/images/aeg/fig3.png)
{% endgallery %}

因此，我们将网页设计定义为一系列原子 DOM 子树的组合，这些子树足够通用以构建复杂的网站，但是可以在树形结构中安全地组合。收i西安，我们创建了一组未指定的 DOM 树模板，将其中一些元素和属性替换成了变量。通过在模板中为属性赋值，就能够生成一个完全指定的原子 DOM 树，并且可以和其他原子元素组合创建一个新的网页。原子的组合顺序也决定了网页将会如何渲染。

{% gallery %}
![DOM 树模板示例](/images/aeg/fig2.png)
{% endgallery %}

图 2 是未指定 DOM 树模板及其在不同变量赋值情况下实例化的一个例子。在图 2b 中，我们创建了一个输入模板，包括一个变量标签和一个文本框，它们有一个共同的父节点；在图 2a 中，我们选择标签元素，并为它的文本属性赋值；在图 2c 中，我们为文本框的内部文本赋值，而忽略了标签元素。

### 网页设计组件

我们引入了一个名为 gMiniWoB 的新框架，用于自动生成网站，其实现了来自 11 个不同的未指定 DOM 模板中的 40 个不同的设计组件。这些组件在网页中被广泛使用，包括导航栏、轮播图、项目卡片、网页表单、购物车、下拉菜单等。每个组件都有至少一个可交互的元素，当智能体与其进行交互时，会改变 DOM 结构。每个组件根据其在奖励计算中不同的用处分成两类：（1）**主动组件（已使用）**；（2）**被动组件（未使用）**。40 个不同的组件中，26 个组件时主动组件，其他的是被动组件。当一个新的主动组件被添加到网页中时，指令会自动扩展以适应对应的字段。这使得主动组件相较于被动组件（大多数情况下被用作噪声）学起来更加复杂。然而，真实网站中往往包括很多分散注意力的元素（被动组件），因此智能体能够学会忽略它们非常重要。

### 对抗智能体架构

为了解决组合环境生成问题，我们提出了一个自回归的对抗策略，其目标时将一系列组件放到一系列位置上。我们使用策略 $\pi_E(a^A|o^A)$ 对对抗智能体进行参数化：

$$\pi_E(a^A|o^A) = \pi(k|K)\prod\limits_{i=0}^N\pi(a_i|a_{0\cdots i-1},b_{0\cdots i-1},k)\pi(b_i|a_{0\cdots i-1},b_{0\cdots i-1},k)$$

其中 $N$ 是输出数量的上限；$K$ 是位置数量的上限；$a_i$ 是设计组件；$b_i$ 是位置序号；$o^A$ 是初始状态。对抗策略首先从参数化的多项式分布 $Cat(0, K)$ 中采样位置的数量 $k$ 。在给定 $o^A$ 的条件下，它执行一个自回归的模型以生成一组基元及其对应的位置 $[0,\cdots, k]$ 。

类似于对抗生成网络（GAN），我们从标准正太分布中采样 $o^A$ 以让对抗策略多样化其设计分布。观测状态首先通过前馈网络编码为 $h_0=f_0(o^A)$ ，再通过另外一个网络 $f_K$ 输出关于空白页数量的分布。相同的隐藏状态 $h_0$ 被输入到一个 LSTM 网络中，作为其初始输入向量。LSTM 网络的输出被两个独立的网络 $f_P$ 和 $f_L$ 分别用于学习（1）设计组件的分布和（2）位置的分布。我们从这些分布中采样一个组件和一个位置，将它们通过另外一个网络 $f_I$ 编码成隐藏向量，作为 LSTM 下一个时刻的输入。$N$ 步之后，采样得到的设计动作就会发送给渲染模块以生成环境。

对于网页导航问题，$K$ 表示网站中页面的数量，位置 $b_i$ 表示页面，而组件 $a_i$ 表示 DOM 树组件。图 3 展示了对抗策略如何生成网页。我们还使用了一个特殊的**跳过操作**来增强组件设计操作，当渲染器执行该动作时，不进行任何操作。这允许对抗策略控制添加的组件的数量。

{% gallery %}
![网页生成示例](/images/aeg/fig3.png)
{% endgallery %}

### 灵活 PAIRED 算法

我们使用灵活的对手选择来提升前文公式所示的遗憾目标。首先初始化两个智能体 $A$ 和 $P$ 。在每次迭代的过程中，对抗智能体设计一个新的网站，每个智能体都通过在网页中进行导航收集到奖励 $R$ ，最后的遗憾值为：

$${\rm REGRET} = \max{R^A,R^P} - 0.5 \times (R^A+R^P)$$

这个目标不在学习智能体和反派智能体之间进行区分，而是将表现最好的智能体视为反派智能体。只要有一个智能体表现得比另外一个智能体更好，这个目标就会持续改进更弱的智能体。在此期间，另外一个智能体会持续学习，从而为我们衡量遗憾值提供一个更强大的最好表现依据。我们提出的灵活 PAIRED 算法如算法 1 所示：利用策略梯度更新法，我们训练学习智能体和反派智能体优化环境奖励，而训练对抗智能体最大化遗憾值。

{% gallery %}
![算法 1](/images/aeg/alg1.png)
{% endgallery %}

### 将预算引入对抗策略

考虑以下场景：智能体在一个购物网站的首页，首页上有很多元素，但是只有一个单独的按钮会进入到账户页面。在探索的过程中，智能体大多数只会因为采取错误的行动而收集负奖励，这个奖励的范围很小（因为只有一个最优行动）。在这种情况下，遗憾值就会很小，并且不提供任何信息，这阻碍了对抗智能体为学习智能体设计难度合适的环境的能力。这个问题在我们提出的灵活遗憾目标下依旧存在。

为了解决这个问题，在遗憾值（将对抗智能体设计的预算与最好的智能体的表现绑定）的基础上，我们使用预算对目标进行增强。我们将对抗智能体的有效预算近似为 $N$ 个时刻中非跳过行为的期望数量，并根据学习智能体是否正在学习来调整这个预算。具体地，我们将下面这个最小化预算的目标添加到 PAIRED 目标中对目标进行增强：

$$O_{\rm budget} = R^A \times \sum\limits_{i=1}^N \log \pi(a_i = {\rm SKIP} | a_{0,\cdots,i-1}, b_{0,\cdots,i-1})$$

其中 $R^A$ 是反派智能体（或者说表现最好的智能体）所获得的奖励。这个目标鼓励对抗智能体在学习智能体没有学习时，使用更少的预算（更多的跳过操作）；而在学习智能体从环境中获得了正奖励时，使用更多的预算（更少的跳过操作）。

## 实验验证

我们在 MiniWoB 框架实现的各种网页环境上测试了我们的模型。我们还利用相同的设计组件实现了几个不同难度等级的网站。这些环境包括了登录、输入地址、航班预定、付款、购物等网页，这些网页要求智能体在页面中导航的时候输入文本或者选择网页中的信息。通过逐渐向网页中增加组件，每个环境都有四个不同的难度等级。这些环境在训练时从未明确地向智能体展示，因此在这些环境上的表现可以看出智能体能够多好地泛化到没有见过的网站。

**智能体结构** 我们使用基于 LSTM 的 DOM 树编码器，以及一个编码指令字段的前馈网络。导航智能体通过计算元素编码和字段编码之间的两两相似性，输出一个元素和字段之间的联合分布。我们使用元素的边际分布作为元素编码的注意力权重，并将上下文向量传递给一个前馈神经网络来计算状态价值。网页导航智能体是通过演员-评论家算法进行训练的。而基于 LSTM 的对抗智能体是通过应用了灵活 PAIRED 算法和灵活 b-PAIRED 算法的策略梯度算法进行训练的。

**基线方法** 我们将 PAIRED 算法、灵活 PAIRED 算法和灵活 b-PAIRED 算法和两个基线算法进行对比：（1）域随机（Domain Randomization, DR）智能体，首先从均匀分布 $U[0, K]$ 中抽样空白页面的数量 $k$ ，接下来，在 $N$ 步中，每一步随机选择一个组件（包括跳过），以及均匀分布 $U[0, k]$ 中的一个页面。（2）课程学习（Curriculum Learning，CL）方法，以概率 $p$ 随机选择每个组件，其中 $p$ 一开始是一个很小的值，并在训练的过程中逐渐达到 $1.0$

### 实验结果

{% gallery %}
![灵活 PAIRED 算法实验结果](/images/aeg/fig4.png)
{% endgallery %}


我们首先将我们提出的灵活 PAIRED 算法与原始 PAIRED 算法进行比较。如图 4 所示：PAIRED 算法在实验环境中根本无法学习，灵活 PAIRED 算法提升非常明显。一个原因是当智能体是分开的，且有非常相似的奖励时，遗憾值会很小，这一现象在训练时尤其明显。这个没有信息量的信号让对抗智能体很难进行学习。另一方面，灵活 PAIRED 算法计算出的遗憾信号总是正的，这更清楚地向对抗智能体展示哪个环境更具有挑战性但是任务仍然可以被完成。后续的消融实验表明引入预算同时提升了原始 PAIRED 算法和灵活 PAIRED 算法的表现。

{% gallery %}
![不同环境上的实验结果](/images/aeg/fig5.png)
{% endgallery %}

**测试环境上的比较** 我们在不同难度等级的测试环境上，利用成功率指标，比较了我们提出的模型与基线模型的表现。如图 5 所示：灵活 b-PAIRED 算法比灵活 PAIRED 算法表现更好，说明预算目标显著提升了性能。另外，这两种算法都在所有任务上超越了基线模型，其中灵活 b-PAIRED 算法在难度 1 的任务上达到了超过 $80\%$ 的成功率。甚至随着环境的复杂度逐渐增加，灵活 b-PAIRED 智能体仍然能够表现得很好，性能下降很小。虽然 CL 方法在训练的早期表现得比灵活 PAIRED 算法更好，但是由于忽略智能体的技能水平，创造出让智能体很难完成的环境，CL 方法在后期的性能下降非常明显。我们还注意到：灵活 b-PAIRED 算法比灵活 PAIRED 算法在所有环境上都学习得更快，因为灵活 b-PAIRED 算法对智能体的表现反应更快。

**环境复杂度** 以生成的主动组件的百分比作为环境复杂性的衡量指标。相对而言，在一个具有更多被动组件的网页中学习更加简单。因为组件要么是添加智能体应当忽略的噪音，要么只有在智能体导航到另一个页面时才使用它们。另一方面，如果有更多的主动组件，不仅 DOM 树的大小会增加，命令字段的数量也会增加，这会增加元素和命令之间的匹配难度。如图 4f 所示：灵活 b-PAIRED 算法在最开始随机选择了大约 60% 的主动组件，并逐渐生成更多的主动组件。尽管灵活 b-PAIRED 算法使用了更多的主动组件，智能体依旧能够提升性能，这要归功于灵活 b-PAIRED 算法根据智能体的技能准确调整环境难度的能力。我们还观察到：在训练后期，组件的分布转变为更复杂和相关的组件。