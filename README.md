# Agentic-AI-Script-generator
Fine-tuned multiple LLMs to generate new FRIENDS TV show scripts. Compared GPT-2, LLaMA, Mistral, DeepSeek, and Gemma using LoRA, prompt engineering, and perplexity evaluation. Achieved strong results with Gemma and Mistral in humor, dialogue, and voice fidelity.
# 🎥 Generating FRIENDS TV Scripts with Fine-Tuned LLMs

##  Overview

This project explores the fine-tuning of Large Language Models (LLMs) to generate new scripts for the TV show **FRIENDS**. We compared models such as **GPT2 (Base, Large, XL)**, **Meta's LLaMA-3**, **Mistral-7B**, **DeepSeek R1-Qwen**, and **Google's Gemma 3**, and applied **Low-Rank Adaptation (LoRA)** for parameter-efficient training.

Our experiments assessed each model's ability to preserve **dialogue coherence**, **character voice**, **sitcom-style humor**, and **overall readability**, using both **intrinsic metrics** (e.g., perplexity) and **extrinsic evaluations** (e.g., ratings from GPT-4o).

---

##  Dataset

* **Primary**: Kaggle's FRIENDS scripts dataset (228 `.txt` files covering all episodes)
* **Secondary**: WikiText-103-v1 (for general language ability evaluation)

###  Preprocessing Steps:

* Concatenated scripts into a single file and split into train/val/test (70/10/20)
* Removed metadata (e.g., episode numbers, scene tags)
* Retained informal language for realism ("How ya doin'")
* Removed web links and cleaned text
* Scenes were used as training units and padded to uniform token length

---

##  Models Used

| Model            | Params | Fine-Tuning Method | Notes                                |
| ---------------- | ------ | ------------------ | ------------------------------------ |
| GPT2-Base        | 124M   | Full               | Underperformed; weak style retention |
| GPT2-Large       | 774M   | Full + LoRA        | Balanced performance and coherence   |
| GPT2-XL          | 1.5B   | Full + LoRA        | Best among GPT2s; still limited      |
| DeepSeek R1-Qwen | 1.5B   | LoRA + 4-bit       | Weak coherence and voice accuracy    |
| LLaMA-3          | 8B     | LoRA + Unsloth     | Great scaling with chunk size        |
| Mistral-7B       | 7B     | LoRA               | Best perplexity and script quality   |
| Gemma-3          | 4B     | LoRA + Quant.      | Best extrinsic evaluation scores     |

---

## Training Configuration

* **Optimizers**: AdamW (weight decay = 0.01)
* **Schedulers**: Linear for GPT2; Cosine for R1, Gemma
* **Learning Rates**: 2e-5 to 1e-4 depending on model
* **Batch Sizes**: 1 (quantized) or 4 (standard)
* **Token Block Sizes**:

  * 1024 (GPT2s), 2048-6144 (others)
  * Best result: use token length = longest scene (6313)
* **Evaluation**: Performed every N steps; best model loaded by validation loss

---

## 🌐 Intrinsic Evaluation (Perplexity)

| Model           | FRIENDS  | WikiText (Pre) | WikiText (Post) |
| --------------- | -------- | -------------- | --------------- |
| GPT2-Base       | 13.46    | 33.78          | 68.72           |
| GPT2-Large      | 10.28    | 21.12          | 22.87           |
| GPT2-XL         | 9.68     | 19.30          | 20.49           |
| GPT2-XL LoRA    | 9.97     | 19.30          | 19.69           |
| R1-Qwen         | 15.80    | 91.97          | 61.97           |
| LLaMA-3 (6144c) | 7.69     | 7.54           | 6.36            |
| Mistral-7B      | **5.31** | **5.47**       | **5.53**        |
| Gemma-3         | 17.46    | 47.26          | 47.47           |

---

## 🔍 Script Generation Techniques

* **Windowed Generation** (used for small models to bypass memory limits)
* **Repetition Penalty**: 1.2 to reduce line duplication
* **Top-k and Top-p Sampling**: k=40, p=0.75
* **Bad Word Filtering**: Prevents early "END" tokens
* **Character Anchoring**: Prioritizes recent speakers for next turn
* **Strong Prompting**: Includes detailed setup, characters, context
* **Summary Prompting**: Appends a brief summary after each chunk to maintain story flow

---

## 📊 Extrinsic Evaluation (ChatGPT-4o)

| Model           | Coherence | Dialogue | Voice | Humor | Readability | Avg.    |
| --------------- | --------- | -------- | ----- | ----- | ----------- | ------- |
| GPT2-Base       | 4         | 4        | 4     | 4     | 4           | 4.0     |
| GPT2-Large      | 6         | 5        | 5     | 5     | 6           | 5.4     |
| GPT2-XL         | 6         | 5        | 5     | 5     | 6           | 5.4     |
| GPT2-XL (LoRA)  | 5         | 6        | 5     | 5     | 5           | 5.2     |
| R1-Qwen         | 4         | 3        | 2     | 2     | 5           | 3.2     |
| LLaMA-3 (6144c) | 7         | 7        | 7     | 7     | 7           | 7.0     |
| Mistral-7B      | 7         | 7        | 7     | 7     | 8           | 7.2     |
| Gemma-3-4B      | 7         | 8        | 8     | 8     | 8           | **7.8** |
| Grok 3 (0-shot) | 8         | 8        | 8     | 8     | 8           | 8.0     |
| Claude 3.7      | 9         | 9        | 9     | 9     | 9           | 9.0     |
| Human Episode   | 9         | 9        | 9     | 9     | 9           | 9.0     |

---

##  Prompt Engineering Insights

* **Base GPT2 Models** failed without prompt engineering
* **Character Anchoring** + **Summary Prompting** increased alignment
* **Best Prompt Style**: Rich, structured with character tags and setup

```txt
Title: The One With McMaster Party
Scene: Student Center
Characters: Rachel and Joey
Context: Rachel and Joey arrive at an event...
Rachel: I can’t believe you brought a microphone.
Joey: You said it was a gig!
```

---

##  Conclusions

* **Mistral-7B**: Best in perplexity and fluency
* **Gemma-3-4B**: Best in character voice, style, and humor
* **Chunk Size Matters**: Larger blocks (e.g., 6144) significantly improve contextual generation
* **Prompt Engineering** is critical for high-quality outputs
* **LoRA is sufficient**: Even low-rank (8-16) LoRA yielded competitive results

---

##  Authors

* Razieh Moradi, Saber Yu, Yingtao Zhang
 McMaster University


## References

1. Blesson Densil. FRIENDS TV Scripts Dataset, Kaggle (2022)
2. Hu et al., LoRA: Low-Rank Adaptation of LLMs, arXiv:2106.09685
3. Merity et al., WikiText-103, arXiv:1609.07843
4. Patil, Unsloth: Efficient LLM Fine-tuning, Hugging Face Blog (2024)
