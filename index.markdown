---
layout: home
title: From Generality to Mastery
description: Composer-Style Symbolic Music Generation via Large-Scale Pre-training
---

# Introduction

In this paper, we introduce **GnM (Generality & Mastery)**, a two-stage Transformer-based model for composer-style symbolic music generation, and explore its effectiveness in musicality, composer style similarity and characteristic on music theory. The first stage focuses on **generality**, learning rich and diverse genres of music progressions, while the second stage leads to the style capturing, achieving **mastery** in specific composers' style.

<div align="center">
  <img src="figures/architecture.png" width=800 alt="">
  <figcaption><strong>Fig.1</strong> Overview of the proposed model GnM (Generality and Mastery) for data-efficient
composer-style symbolic music generation</figcaption>
</div>

To better represent the composer’s style, we extend the original [REMI](https://github.com/YatingMusic/remi) representation to support all common time signatures and adjust its resolutions. Then we introduce adapter modules to embed composer conditions during the generation process.

The extension of REMI are mainly adding explicit **time-signature** tokens and by increasing the **grid (beat‑token)** resolution per bar, so that our model can receive complete information. Concretely:

- **Time‑Signature Events**  
  We support five common time signatures, 2/4, 3/4, 4/4, 3/8, 6/8 and convert all others into these (e.g. 5/4 → 2/4 + 3/4; 12/8 → two 6/8 bars). Each bar begins with a `[TS]` token indicating its current time signature.

- **High‑Resolution Beat Grids**  
  Within each bar, we emit a fixed number of `[G]` tokens (“grids”) based on the meter’s note‐value subdivisions:  
  - **4/4** → 12 grids per quarter note → **48** grids/bar  
  - **3/4** → 12 grids per quarter note → **36** grids/bar  
  - **2/4** → 12 grids per quarter note → **24** grids/bar  
  - **6/8** → 6 grids per eighth note → **36** grids/bar  
  - **3/8** → 6 grids per eighth note → **18** grids/bar  

By making both the time signature and an appropriately large number of rhythmic “ticks” explicit, our representation captures fine‐grained rhythmic structure in any time signature, unlike the original REMI’s fixed 16‐step 4/4 grid.

We follow typical [NLP](https://arxiv.org/abs/1902.00751) practice when designing adapter modules in the mastery stage. The module concatenates the composer embedding with the hidden state of the layer, feeds the result through a two-layer MLP (with a GELU nonlinearity) to learn a style bias, and project it back to the original hidden-state dimension. A residual connection around this MLP stabilizes training.

<div align="center">
  <img src="figures/adapter.jpeg" width=400 alt="">
  <figcaption><strong>Fig.2</strong> The architecture of the style adapter</figcaption>
</div>

Our experiment indicates the effectiveness of our pipeline and extended data representation on both improving musicality and capturing composer's style. Further, on some objective metrics on generated music samples, our model also reaches comparable performance compared with same-period [NotaGen](https://github.com/ElectricAlexis/NotaGen) but with much less data and model capacity.

Results on the objective metrics with our *GnM*, *GnM* (no pre-training),  *GnM* (no fine-tuning), REMI, [Emo-Disentanger](https://github.com/Yuer867/EMO-Disentanger), and Notagen-finetuned are presented below:

<div align="center">
  <img src="figures/obj_music.jpeg" width=800 alt="">
  <figcaption><strong>Fig.3</strong> The objective evaluation results among different music generation models and our ablations</figcaption>
</div>

The objective classification results on three composer-conditioned generation models, *GnM* (Mastery), *GnM* (no pre-training, or from scratch) and Notagen-finetuned are also presented. Other models are excluded because they cannot generate music with specific composer styles.

<div align="center">
  <img src="figures/obj_composer.jpeg" width=950 alt="">
  <figcaption><strong>Fig.4</strong> The composer classification accuracy on the GnM model without the fine-tuning stage (as Scratch), fine-tuned NotaGen, and the final GnM model (as Mastery).</figcaption>
</div>

Additionally, the subjective surveys on the overall music quality and composer style similarities are below:
<div align="center">
  <img src="figures/sub_music.jpeg" width=600 alt="">
  <figcaption><strong>Fig.5</strong> The musicality rating scores from the subjective listening test. We report the mean value, standard deviation (STD), standard error (SE), and 95% Confidence Interval (CI).</figcaption>
</div>
<br><br>
<div align="center">
  <img src="figures/sub_composer.jpeg" width=950 alt="">
  <figcaption><strong>Fig.6</strong> The composer style assessment from the subjective listening test. We only evaluate these three composer-conditionable models as the same in objective composer evaluation. </figcaption>
</div>

# Generation Samples
We show some generation samples from multiple models for comparison on the music generation quality and their style similarities compared with the actual conditions:

- **GnM (Mastery)**: Generality-to-mastery two-stage generation model with extended REMI representation
- **[Notagen-finetuned](https://huggingface.co/ElectricAlexis/NotaGen/blob/main/weights_notagen_pretrain-finetune_p_size_16_p_length_1024_p_layers_c_layers_6_20_h_size_1280_lr_1e-05_batch_1.pth)**: Same period ABC-notation-based generation model, baseline
- **GnM (from scratch)**: Generality-to-mastery one-stage generation model with extended REMI representation (Train with fine-tuned dataset from scratch)

It was found that our **GnM (Mastery)** model is able to produce music with high musicality and strong composer style similarity with given conditions.

## GnM (Mastery) Model by Composer

<table class="audio-table">
  <thead>
    <tr class="header">
    <th>Bach</th>
    <th>Beethoven</th>
    <th>Chopin</th>
    <th>Mozart</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><audio controls=""><source src="assets/demos/mastery/Bach/1.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/mastery/Beethoven/1.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/mastery/Chopin/1.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/mastery/Mozart/1.wav" type="audio/mpeg" /></audio></td>
    </tr>
    <tr>
      <td><audio controls=""><source src="assets/demos/mastery/Bach/2.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/mastery/Beethoven/2.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/mastery/Chopin/2.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/mastery/Mozart/2.wav" type="audio/mpeg" /></audio></td>
    </tr>
    <tr>
      <td><audio controls=""><source src="assets/demos/mastery/Bach/3.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/mastery/Beethoven/3.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/mastery/Chopin/3.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/mastery/Mozart/3.wav" type="audio/mpeg" /></audio></td>
    </tr>
    <tr>
      <td><audio controls=""><source src="assets/demos/mastery/Bach/4.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/mastery/Beethoven/4.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/mastery/Chopin/4.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/mastery/Mozart/4.wav" type="audio/mpeg" /></audio></td>
    </tr>
    <tr>
      <td><audio controls=""><source src="assets/demos/mastery/Bach/5.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/mastery/Beethoven/5.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/mastery/Chopin/5.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/mastery/Mozart/5.wav" type="audio/mpeg" /></audio></td>
    </tr>
    <tr>
      <td><audio controls=""><source src="assets/demos/mastery/Bach/6.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/mastery/Beethoven/6.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/mastery/Chopin/6.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/mastery/Mozart/6.wav" type="audio/mpeg" /></audio></td>
    </tr>
    <tr>
      <td><audio controls=""><source src="assets/demos/mastery/Bach/7.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/mastery/Beethoven/7.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/mastery/Chopin/7.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/mastery/Mozart/7.wav" type="audio/mpeg" /></audio></td>
    </tr>
    <tr>
      <td><audio controls=""><source src="assets/demos/mastery/Bach/8.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/mastery/Beethoven/8.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/mastery/Chopin/8.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/mastery/Mozart/8.wav" type="audio/mpeg" /></audio></td>
    </tr>
    <tr>
      <td><audio controls=""><source src="assets/demos/mastery/Bach/9.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/mastery/Beethoven/9.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/mastery/Chopin/9.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/mastery/Mozart/9.wav" type="audio/mpeg" /></audio></td>
    </tr>
    <tr>
      <td><audio controls=""><source src="assets/demos/mastery/Bach/10.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/mastery/Beethoven/10.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/mastery/Chopin/10.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/mastery/Mozart/10.wav" type="audio/mpeg" /></audio></td>
    </tr>
  </tbody>
</table>

<br><br>

## Notagen-finetuned Model by Composer

<table class="audio-table">
  <thead>
    <tr class="header">
    <th>Bach</th>
    <th>Beethoven</th>
    <th>Chopin</th>
    <th>Mozart</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><audio controls=""><source src="assets/demos/notagen/Bach/1.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Beethoven/1.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Chopin/1.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Mozart/1.wav" type="audio/mpeg" /></audio></td>
    </tr>
    <tr>
      <td><audio controls=""><source src="assets/demos/notagen/Bach/2.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Beethoven/2.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Chopin/2.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Mozart/2.wav" type="audio/mpeg" /></audio></td>
    </tr>
    <tr>
      <td><audio controls=""><source src="assets/demos/notagen/Bach/3.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Beethoven/3.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Chopin/3.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Mozart/3.wav" type="audio/mpeg" /></audio></td>
    </tr>
    <tr>
      <td><audio controls=""><source src="assets/demos/notagen/Bach/4.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Beethoven/4.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Chopin/4.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Mozart/4.wav" type="audio/mpeg" /></audio></td>
    </tr>
    <tr>
      <td><audio controls=""><source src="assets/demos/notagen/Bach/5.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Beethoven/5.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Chopin/5.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Mozart/5.wav" type="audio/mpeg" /></audio></td>
    </tr>
    <tr>
      <td><audio controls=""><source src="assets/demos/notagen/Bach/6.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Beethoven/6.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Chopin/6.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Mozart/6.wav" type="audio/mpeg" /></audio></td>
    </tr>
    <tr>
      <td><audio controls=""><source src="assets/demos/notagen/Bach/7.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Beethoven/7.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Chopin/7.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Mozart/7.wav" type="audio/mpeg" /></audio></td>
    </tr>
    <tr>
      <td><audio controls=""><source src="assets/demos/notagen/Bach/8.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Beethoven/8.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Chopin/8.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Mozart/8.wav" type="audio/mpeg" /></audio></td>
    </tr>
    <tr>
      <td><audio controls=""><source src="assets/demos/notagen/Bach/9.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Beethoven/9.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Chopin/9.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Mozart/9.wav" type="audio/mpeg" /></audio></td>
    </tr>
    <tr>
      <td><audio controls=""><source src="assets/demos/notagen/Bach/10.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Beethoven/10.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Chopin/10.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Mozart/10.wav" type="audio/mpeg" /></audio></td>
    </tr>
  </tbody>
</table>

<br><br>

## GnM Scratch Model (Ablation) by Composer

<table class="audio-table">
  <thead>
    <tr class="header">
    <th>Bach</th>
    <th>Beethoven</th>
    <th>Chopin</th>
    <th>Mozart</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><audio controls=""><source src="assets/demos/notagen/Bach/1.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Beethoven/1.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Chopin/1.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Mozart/1.wav" type="audio/mpeg" /></audio></td>
    </tr>
    <tr>
      <td><audio controls=""><source src="assets/demos/notagen/Bach/2.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Beethoven/2.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Chopin/2.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Mozart/2.wav" type="audio/mpeg" /></audio></td>
    </tr>
    <tr>
      <td><audio controls=""><source src="assets/demos/notagen/Bach/3.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Beethoven/3.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Chopin/3.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Mozart/3.wav" type="audio/mpeg" /></audio></td>
    </tr>
    <tr>
      <td><audio controls=""><source src="assets/demos/notagen/Bach/4.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Beethoven/4.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Chopin/4.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Mozart/4.wav" type="audio/mpeg" /></audio></td>
    </tr>
    <tr>
      <td><audio controls=""><source src="assets/demos/notagen/Bach/5.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Beethoven/5.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Chopin/5.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/notagen/Mozart/5.wav" type="audio/mpeg" /></audio></td>
    </tr>
  </tbody>
</table>

<br><br>

## GnM (Pre-training Only), No Composer
This section shows generated music by GnM with only pre-training. We found the diversity of music learned from multi-genre training data.

<table class="audio-table">
  <tbody>
    <tr>
      <td><audio controls=""><source src="assets/demos/pretrain/1.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/pretrain/2.wav" type="audio/mpeg" /></audio></td>
    </tr>
    <tr>
      <td><audio controls=""><source src="assets/demos/pretrain/3.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/pretrain/4.wav" type="audio/mpeg" /></audio></td>
    </tr>
    <tr>
      <td><audio controls=""><source src="assets/demos/pretrain/5.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/pretrain/6.wav" type="audio/mpeg" /></audio></td>
    </tr>
    <tr>
      <td><audio controls=""><source src="assets/demos/pretrain/7.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/pretrain/8.wav" type="audio/mpeg" /></audio></td>
    </tr>
    <tr>
      <td><audio controls=""><source src="assets/demos/pretrain/9.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/pretrain/10.wav" type="audio/mpeg" /></audio></td>
    </tr>
  </tbody>
</table>
