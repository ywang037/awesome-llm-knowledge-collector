Below is a comparative analysis of the most notable open‑weight LLM families and individual models featured in Sebastian Raschka’s *LLM Architecture Gallery*. The focus is on architectural innovations, efficiency strategies, and the unique “key detail” that makes each series stand out.

---

## 1. DeepSeek Series  
**Models:** V3 (671B / 37B active), R1 (same arch, reasoning‑tuned), V3.2 (671B / 37B active)  

**Defining Innovation**  
- **Multi‑head Latent Attention (MLA)** – Compresses the KV cache into a low‑rank latent space, drastically reducing memory footprint during inference.  
- **Shared Expert + Dense Prefix** – First few layers are dense, then MoE routing begins, with one expert always active (shared) to ensure all tokens have a stable baseline.  
- **DeepSeek Sparse Attention (V3.2)** – Introduced in V3.2 to cut long‑context costs while preserving MLA’s efficiency.  

**Why It Stands Out**  
DeepSeek V3 became the template for the current wave of ultra‑large open MoE models. Its MLA is now adopted by many successors (e.g., Kimi, GLM‑5, Mistral Large 3). The V3.2 update adds sparse attention without changing the base layout, focusing purely on inference efficiency.

---

## 2. Qwen Series  
**Models:** Qwen3 (235B‑A22B, 32B, 8B, 4B), Qwen3 Next (80B‑A3B), Qwen3 Coder Flash (30B‑A3B), Qwen3.5 (397B‑A17B)  

**Defining Innovation**  
- **No Shared Expert in MoE Variants** – Unlike DeepSeek, Qwen3 MoE removes the shared expert, relying on top‑k routing alone for a simpler, serving‑friendly design.  
- **QK‑Norm + GQA** – Applied consistently across dense and MoE variants, stabilises training and improves attention quality.  
- **Hybrid Attention (Next / 3.5)** – Qwen3 Next introduced a 3:1 ratio of **Gated DeltaNet** (linear attention) to standard Gated Attention, cutting long‑context cost. Qwen3.5 mainstreamed this hybrid, now with 512 experts and 17B active parameters.  

**Why It Stands Out**  
Qwen offers one of the most complete architecture families: from tiny dense models (4B) to massive sparse hybrids. Their shift to linear‑attention hybrids in Qwen3 Next and Qwen3.5 demonstrates a clear move toward long‑context efficiency without sacrificing the transformer backbone.

---

## 3. GLM Series  
**Models:** GLM‑4.5 (355B / 32B active), GLM‑4.7 (355B / 32B active), GLM‑5 (744B / 40B active)  

**Defining Innovation**  
- **Dense Prefix + Shared Expert** – Borrows DeepSeek’s pattern of starting with several dense layers before MoE routing, plus a shared expert for stability.  
- **Gradual Adoption of MLA** – GLM‑4.5/4.7 used GQA with QK‑Norm; GLM‑5 upgraded to MLA plus DeepSeek Sparse Attention, making it a “DeepSeek‑style” model with even more parameters and fewer layers.  

**Why It Stands Out**  
GLM illustrates how established families evolve by incorporating proven innovations from other leaders. GLM‑5 is currently one of the largest open MoE models, combining MLA, sparse attention, and a massive 744B total size.

---

## 4. Kimi Series (Moonshot AI)  
**Models:** Kimi K2 (1T / 32B active), Kimi Linear (48B‑A3B), Kimi K2.5 (1T / 32B active, multimodal)  

**Defining Innovation**  
- **Scaling the DeepSeek Recipe** – K2 uses MLA with even more experts (384) and fewer MLA heads, reaching 1 trillion total parameters.  
- **Linear‑Attention Hybrid (Kimi Linear)** – Replaces most full‑attention layers with **Kimi Delta Attention** (a linear variant), using a 3:1 ratio of Delta to MLA layers. NoPE (no positional encoding) is applied in the MLA layers for further efficiency.  
- **Multimodal Extension (K2.5)** – Keeps the 1T MoE backbone but adds vision capabilities and doubles native context to 256k tokens.  

**Why It Stands Out**  
Kimi demonstrates two parallel tracks: scaling the DeepSeek MoE/MLA design to 1T, and exploring linear‑attention hybrids for extreme long‑context efficiency (1M tokens). K2.5 shows how the same architecture can be extended to multimodal tasks.

---

## 5. Step 3.5 Flash (196B / 11B active)  

**Defining Innovation**  
- **Multi‑Token Prediction (MTP‑3)** – Uses MTP during both training and inference to achieve unusually high throughput.  
- **3:1 Sliding‑Window Attention** – Employs a sliding‑window/global attention ratio that balances local coherence with global context.  

**Why It Stands Out**  
Step 3.5 Flash is a throughput‑focused MoE model that leverages multi‑token prediction as a first‑class efficiency mechanism—unlike most models that treat MTP only as a training auxiliary loss. This makes it competitive with much larger systems.

---

## 6. MiniMax Series  
**Models:** MiniMax M2 (230B / 10B active), MiniMax‑M2.5 (230B / 10B active)  

**Defining Innovation**  
- **Sparse Routing + QK‑Norm** – M2 uses GQA with QK‑Norm and partial RoPE, but with much sparser routing (fewer active experts) than Qwen or DeepSeek.  
- **Deliberate Simplicity (M2.5)** – The coder‑oriented M2.5 variant explicitly avoids sliding‑window or linear hybrids, sticking to a classic sparse MoE design with QK‑Norm for stability.  

**Why It Stands Out**  
MiniMax shows that not every new model needs a hybrid or exotic attention. By optimising sparsity and keeping the architecture conservative, they deliver competitive performance, especially for coding tasks, with a small active parameter count (10B).

---

## 7. Xiaomi MiMo‑V2‑Flash (309B / 15B active)  

**Defining Innovation**  
- **Aggressive Sliding‑Window Attention** – Uses a 5:1 ratio of sliding‑window to global attention, with an unusually small local window of only 128 tokens.  
- **Multi‑Token Prediction** – Trained with MTP to improve token generation efficiency.  

**Why It Stands Out**  
MiMo‑V2‑Flash pushes sliding‑window attention to its extreme, relying heavily on local context to reduce compute while still maintaining a large total parameter count. It represents a strong “local‑first” approach among MoE models.

---

## Comparative Summary Table

| Model | Total / Active | Decoder Type | Attention | Key Efficiency Trick |
|-------|----------------|--------------|-----------|----------------------|
| **DeepSeek V3** | 671B / 37B | MoE | MLA | Shared expert, dense prefix |
| **DeepSeek V3.2** | 671B / 37B | MoE | MLA + Sparse Attention | Long‑context sparse attention |
| **Qwen3 235B** | 235B / 22B | MoE | GQA + QK‑Norm | No shared expert |
| **Qwen3.5** | 397B / 17B | Hybrid MoE | 3:1 DeltaNet / Gated Attn | Linear‑attention hybrid |
| **GLM‑5** | 744B / 40B | MoE | MLA + Sparse Attention | Largest MLA MoE with dense prefix |
| **Kimi K2** | 1T / 32B | MoE | MLA | Scaled MLA with 384 experts |
| **Kimi Linear** | 48B / 3B | Hybrid | 3:1 Delta Attn / MLA | NoPE, linear attention for 1M context |
| **Step 3.5 Flash** | 196B / 11B | MoE | GQA + 3:1 sliding‑window | Multi‑token prediction (MTP‑3) |
| **MiniMax M2** | 230B / 10B | MoE | GQA + QK‑Norm | Very sparse routing |
| **MiMo‑V2‑Flash** | 309B / 15B | MoE | 5:1 sliding‑window | 128‑token local window + MTP |

---

## Key Trends & Takeaways

1. **MLA is the new GQA** – Originally pioneered by DeepSeek, MLA is now adopted by Kimi, GLM‑5, and Mistral Large 3 as the preferred attention for large MoEs.  
2. **Hybrids are mainstreaming** – Qwen3.5 and Kimi Linear replace a fraction of attention layers with linear (DeltaNet) or state‑space (Mamba) alternatives to handle ultra‑long contexts (1M+ tokens).  
3. **Efficiency‑first designs** – Models like Step 3.5 Flash and MiMo‑V2‑Flash use multi‑token prediction and aggressive sliding‑window attention to boost throughput without sacrificing parameter count.  
4. **Specialised variants** – Within each family, we see sub‑variants targeting reasoning (DeepSeek R1), coding (Qwen Coder Flash, MiniMax‑M2.5), or multilingual needs (Sarvam, not in this list).  

All architectural diagrams and detailed fact sheets can be explored in the [LLM Architecture Gallery](https://sebastianraschka.com/llm-architecture-gallery/).
