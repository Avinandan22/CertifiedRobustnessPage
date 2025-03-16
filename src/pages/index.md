---
layout: "../layouts/Layout.astro"
title: "Keeping up with dynamic attackers Certifying robustness to adaptive online data poisoning"
description: "Simple project page template for your research paper, built with Astro and Tailwind CSS"
favicon: "favicon.svg"
thumbnail: "screenshot-light.png"
---

import Header from "../components/Header.astro";
import Video from "../components/Video.astro";
import HighlightedSection from "../components/HighlightedSection.astro";
import SmallCaps from "../components/SmallCaps.astro";
import Figure from "../components/Figure.astro";
import Image from "../components/Image.astro";
import TwoColumns from "../components/TwoColumns.astro";
import YouTubeVideo from "../components/YouTubeVideo.astro";
import LaTeX from "../components/LaTeX.astro";

import {ImageComparison} from "../components/ImageComparison.tsx";

import outside from "../assets/outside.mp4";
import transformer from "../assets/transformer.webp";
import Splat from "../components/Splat.tsx"
import dogsDiffc from "../assets/dogs-diffc.png"
import Figure3 from "../assets/Figure3.avif"
import schematic from "../assets/schematic.png"
import table1 from "../assets/table1.webp"
import mean from "../assets/mean.avif"
import dogsTrue from "../assets/dogs-true.png"

import CodeBlock from "../components/CodeBlock.astro";
import Table from "../components/Table.astro";
export const components = {pre: CodeBlock, table: Table};

<Header
  title={frontmatter.title}
  authors={[
    {
      name: "Avinandan Bose",
      institution: "University of Washington",
      notes: [],
    },
    {
      name: "Laurent Lessard",
      institution: "Northeastern University",
      notes: [],
    },
    {
      name: "Maryam Fazel",
      institution: "University of Washington",
      notes: [],
    },
    {
      name: "Krishnamurthy Dj Dvijotham",
      institution: "ServiceNow Research",
      notes: [],
    },
  ]}
  conference="AISTATS 2025"
  links={[
    {
      name: "Paper",
      url: "https://arxiv.org/pdf/2502.16737",
      icon: "ri:file-pdf-2-line",
    },
    {
      name: "Code",
      url: "https://github.com/Avinandan22/Certified-Robustness",
      icon: "ri:github-line",
    },
    {
      name: "Blog",
      url: "https://www.servicenow.com/blogs/2025/robustness-against-dynamic-data-poisoning",
      icon: "ri:article-line", 
    },
  ]} 
/>

<HighlightedSection>

## Abstract

Machine learning (ML) and AI rely on massive, uncurated datasets, where verifying data quality is often impractical. As AI models increasingly incorporate input from untrusted users, they become more vulnerable to carefully orchestrated attacks that can compromise their reliability.

Adversarial data poisoning poses significant cybersecurity risks, ranging from life-threatening misdiagnoses to market disruptions. As illustrated in Table 1, these attacks can vary depending on the stage at which they occur, during training or deployment. 

<Figure>
  <Image slot="figure" source={table1} altText="Table 1: Cybersecurity risks of adversarial data poisoning" />
  <span slot="caption">Table 1: Cybersecurity risks of adversarial data poisoning.</span>
</Figure>

Currently, much research focuses on certifying model robustness against static adversaries that modify a portion of the offline dataset before the training algorithm is applied (as shown in Figure 1). For backdoor attack certification, studies typically involve adversaries poisoning offline datasets in a single step, making these attacks non-dynamic.

Dynamically optimized attack strategies enhance the effectiveness of adversarial poisoning. Online attackers can observe the trained algorithm in real time and adapt their poisoning tactics to the evolving behavior of the ML model.

This is particularly true for models that are continuously or periodically updated, such as those involved in fine-tuning or reinforcement learning from human feedback (RLHF).

We introduce a new way to protect ML systems from dynamic data poisoning attacks. We developed a framework that helps us measure how much an attack could affect the model, and we use these measurements to create learning algorithms that can better handle such attacks.

</HighlightedSection>


## The Problem Setup
Let’s break down the problem. We assume that our learning algorithm is trying to estimate a set of parameters θ (like the mean of some data). The algorithm updates its estimate of θ with every new data point it receives.

We study the setting of online learning, where the learning algorithm is continuously learning from new data. This is reflective of popular paradigms in frontier AI models, such as ChatGPT, Gemini, and Claude, that involve constantly adapting and improving the models given new data or feedback from users.

In an online learning setup, the algorithm learns from one data point at a time and adjusts the model’s parameters accordingly. The update rule for the parameters maps the parameters at time (t) to new parameters given an external noise and a data point.

In our setup, some of the data points may be poisoned by an adversary who wants to mislead the algorithm. A practical example where this is shown to be possible is demonstrated in a paper about poisoning web-scale training datasets.

Widely used models, such as Contrastive Language-Image Pre-training (CLIP), trained on data scraped from the web can be poisoned by an attacker who buys some of the domains harvested to create the training data and injects bad data.

At each step, the algorithm might receive poisoned data with a certain probability. This is controlled by a parameter, which determines what fraction of data points are poisoned.

Unlike standard data poisoning attacks, where the poisoning adversary must choose the poisoned data points before learning happens, our setting allows the adversary to observe the online learning process and adjust their attack as the algorithm learns. This creates a more powerful threat model that can achieve stronger poisoning.
<Figure>
  <Image slot="figure" source={schematic} altText="Schematic diagrams highlighting the differences between static and dynamic data poisoning" />
  <span slot="caption">Figure 1: Schematic diagrams highlighting the differences between static and dynamic data poisoning.</span>
</Figure>

<HighlightedSection>

## How the adversary affects the learning process

The learning process forms a chain, where the current estimate of the parameters depends on all previous estimates and the data received. The adversary tries to influence this process by poisoning the data at certain points. We need to understand how this affects the parameter estimates over time.

The adversary’s goal is to maximize some kind of loss function—for example, increasing the error rate of predictions the model makes. We can think of the adversary’s actions as decisions in a game, where each move tries to make the model less accurate.

</HighlightedSection>

## General Framework to Compute Certificate

Our main contribution is the robustness certificate. This is a tool that helps us understand how much damage an adversarial attack can do to a model. Using this certificate, we can design algorithms that are more resistant to attacks (see Figure 2). We calculate how different choices of poisoned data influence the parameter estimates. This gives us a "measure" of the model's robustness. The certificate allows us to predict and limit the damage done by these attacks.

Our approach is inspired by meta-learning, where we aim to design a learning algorithm that performs well across a range of distributions.

The first component focuses on the algorithm's ability to perform effectively without an adversary, by ensuring it converges to a stable set of parameters that results in low expected loss. The second component provides an upper bound on the potential worst-case loss the algorithm might face when an adversary is involved.

The key here is to find a defense that works well on average, even when faced with new, unseen data distributions.
<Figure>
  <Image slot="figure" source={Figure3} altText="Schematic of robustness certificate." />
  <span slot="caption">Figure 2: Schematic of robustness certificate.</span>
</Figure>

<HighlightedSection>
 
## Experiments

We tested our defense on a set of 50 Gaussian distributions (a common data type in statistics). The defense parameter was trained on 10 randomly chosen distributions, and we tested how well it performed when faced with poisoned data.

The results (see Figure 3) demonstrate that our defense significantly improves performance compared to training without any defense. We varied the learning rates and the fraction of samples corrupted by the dynamic adversary.

Based on our results, our algorithm performs much better at estimating the mean, even when a significant fraction of the data is poisoned.

<Figure>
  <Image slot="figure" source={mean} altText="Figure 4: Test performance (mean squared error between true and estimated means)" />
  <span slot="caption">Figure 3: Test performance (mean squared error between true and estimated means).</span>
</Figure>

</HighlightedSection>

## Conclusions

Although we illustrated our framework using mean estimations and binary classification, its applications extend far beyond this simple example. Our defense strategies are particularly valuable for real-world AI systems, from binary classifiers making critical yes/no decisions (such as fraud detection in financial transactions) to multiclass systems categorizing data into multiple categories (such as image recognition or document topic classification).

More significantly, the framework can protect reward models in reinforcement learning systems, where corrupted feedback could fundamentally alter AI behavior and learning trajectory.

Our robustness certificates provide AI practitioners with tools to quantify and mitigate vulnerabilities to dynamic poisoning attacks. As AI systems become increasingly integrated in critical applications, these defenses offer a mathematical foundation for building more reliable and trustworthy AI systems.

## BibTeX citation

```bibtex
@article{bose2025keeping,
  title={Keeping up with dynamic attackers: Certifying robustness to adaptive online data poisoning},
  author={Bose, Avinandan and Lessard, Laurent and Fazel, Maryam and Dvijotham, Krishnamurthy Dj},
  journal={arXiv preprint arXiv:2502.16737},
  year={2025}
}
```