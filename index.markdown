---
layout: home
title: From Generality to Mastery
description: Composer‑Style Symbolic Music Generation via Large‑Scale Pre‑training
---

# Overview  

**GnM (Generality → Mastery)** is a two‑stage Transformer framework for symbolic music generation that first **learns general musical knowledge** from a large multi‑genre corpus and then **specialises** in the idiom of four classical composers (Bach, Mozart, Beethoven, Chopin).  

<div align="center">
  <img src="figures/architecture.png" width="800" alt="">
  <figcaption><strong>Fig.&nbsp;1</strong> Pipeline of the proposed GnM model.</figcaption>
</div>

---

## Key Contributions  

1. **Two‑Stage Training Paradigm**  
   **Stage 1 – Generality:** pre‑train on 1.3 M bars from pop, folk, and classical scores to learn broad melodic, harmonic, and rhythmic patterns.  
   **Stage 2 – Mastery:** fine‑tune with lightweight adapter modules on < 1k verified scores from four target composers, conditioning generation on a composer token.

2. **Extended REMI Representation**  
   * New `[TS]` tokens cover 2/4, 3/4, 4/4, 3/8, 6/8 (other metres are mapped by simple rules).  
   * High‑resolution beat grids (up to 48 ticks per bar) preserve fine rhythmic nuance in every meter.

3. **Data Efficiency**  
   GnM reaches or surpasses state‑of‑the‑art quality with **46M parameters** after introducing adapters, much smaller than comparable ABC‑notation models.  
---

# Method  

### 1&nbsp;┇ Extended REMI  

| Aspect | Original REMI | **Extended REMI (ours)** |
| ------ | ------------- | ------------------------ |
| Time signature | Fixed 4/4 | Five common metres + mapping rules |
| Beat resolution | 16/bar | 18–48 grids/bar (meter‑dependent) |
| Global tokens | Tempo | Tempo **+ Composer** |

**Time‑Signature Events**
Each bar opens with `[TS:<meter>]`. Irregular metres (e.g., 5/4) are decomposed (2/4 + 3/4) to retain bar integrity.

**High‑Resolution Grids**
Quarter‑note metres use 12 ticks/beat.
Eighth‑note metres use 6. 
Example: 4/4 → 48 grids/bar.

### 2&nbsp;┇ Two‑Stage Training

| Stage | Corpus / Pieces | Objective | Conditioning |
|-------|-----------------|-----------|--------------|
| Generality | 64.8 M tokens (pop + folk + classical) | Next‑token | `[Tempo]` |
| Mastery | 891 unique pieces (4 composers) | Next‑token | `[ComposerName]` + `[Tempo]` |

**Style Adapter** (inserted every other decoder layer)

Concatenates composer embedding with hidden state → 2‑layer MLP (GELU) → projection back → residual add (Fig.&nbsp;2).

<div align="center">
  <img src="figures/adapter.jpeg" width="400" alt="">
  <figcaption><strong>Fig.&nbsp;2</strong> Style adapter for composer conditioning.</figcaption>
</div>

---

# Results  

<div align="center">
  <img src="figures/obj_music.jpeg" width="800" alt="">
  <figcaption><strong>Fig.&nbsp;3</strong> Objective musical‑quality metrics (higher is better except entropy).</figcaption>
</div>
From the table, we found that

* **Musicality** – GnM reaches top pitch‑class entropy and grooves while maintaining competitive long‑term structure, comparable with same-period [Notagen-finetuned](https://github.com/ElectricAlexis/NotaGen)
* **Ablations** – Removing the pre‑training stage would inflate repetition in generated music

<div align="center">
  <img src="figures/obj_composer.jpeg" width="950" alt="">
  <figcaption><strong>Fig.&nbsp;4</strong> Composer‑classifier accuracy (higher = closer to target style).</figcaption>
</div>

*GnM Mastery* exceeds NotaGen on Mozart & Beethoven, and matches on Bach & Chopin.

<div align="center">
  <img src="figures/sub_music.jpeg" width="600" alt="">
  <figcaption><strong>Fig.&nbsp;5</strong> Mean listener musicality ratings (1 – 5) with 95 % CI.</figcaption>
</div>

GnM (pre-training and mastery models) achieves the highest perceived musicality; listeners also distinguish its stylistic features better than the Notagen and our ablated GnM scratch (Fig.&nbsp;6).

<div align="center">
  <img src="figures/sub_composer.jpeg" width="950" alt="">
  <figcaption><strong>Fig.&nbsp;6</strong> Listener‑chosen composer identity (two‑option forced choice).</figcaption>
</div>

---

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
      <td><audio controls=""><source src="assets/demos/Mastery/Bach/1.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/Mastery/Beethoven/1.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/Mastery/Chopin/1.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/Mastery/Mozart/1.wav" type="audio/mpeg" /></audio></td>
    </tr>
    <tr>
      <td><audio controls=""><source src="assets/demos/Mastery/Bach/2.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/Mastery/Beethoven/2.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/Mastery/Chopin/2.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/Mastery/Mozart/2.wav" type="audio/mpeg" /></audio></td>
    </tr>
    <tr>
      <td><audio controls=""><source src="assets/demos/Mastery/Bach/3.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/Mastery/Beethoven/3.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/Mastery/Chopin/3.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/Mastery/Mozart/3.wav" type="audio/mpeg" /></audio></td>
    </tr>
    <tr>
      <td><audio controls=""><source src="assets/demos/Mastery/Bach/4.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/Mastery/Beethoven/4.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/Mastery/Chopin/4.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/Mastery/Mozart/4.wav" type="audio/mpeg" /></audio></td>
    </tr>
    <tr>
      <td><audio controls=""><source src="assets/demos/Mastery/Bach/5.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/Mastery/Beethoven/5.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/Mastery/Chopin/5.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/Mastery/Mozart/5.wav" type="audio/mpeg" /></audio></td>
    </tr>
    <tr>
      <td><audio controls=""><source src="assets/demos/Mastery/Bach/6.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/Mastery/Beethoven/6.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/Mastery/Chopin/6.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/Mastery/Mozart/6.wav" type="audio/mpeg" /></audio></td>
    </tr>
    <tr>
      <td><audio controls=""><source src="assets/demos/Mastery/Bach/7.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/Mastery/Beethoven/7.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/Mastery/Chopin/7.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/Mastery/Mozart/7.wav" type="audio/mpeg" /></audio></td>
    </tr>
    <tr>
      <td><audio controls=""><source src="assets/demos/Mastery/Bach/8.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/Mastery/Beethoven/8.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/Mastery/Chopin/8.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/Mastery/Mozart/8.wav" type="audio/mpeg" /></audio></td>
    </tr>
    <tr>
      <td><audio controls=""><source src="assets/demos/Mastery/Bach/9.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/Mastery/Beethoven/9.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/Mastery/Chopin/9.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/Mastery/Mozart/9.wav" type="audio/mpeg" /></audio></td>
    </tr>
    <tr>
      <td><audio controls=""><source src="assets/demos/Mastery/Bach/10.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/Mastery/Beethoven/10.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/Mastery/Chopin/10.wav" type="audio/mpeg" /></audio></td>
      <td><audio controls=""><source src="assets/demos/Mastery/Mozart/10.wav" type="audio/mpeg" /></audio></td>
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
