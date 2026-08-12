---
title: "RecSys"
weight: 2
bookFlatSection: true
bookHidden: false
---

## Building a RecSys

---

## Phase 0: The Cold Start (Day 0 - Minimal Data)

**Scenario:** You just launched a new app (newsletter platform or niche e-commerce). You have users and items, but no historical click/purchase data.

At this stage, **collaborative filtering is not reliable**. Design for robustness with **content-based filtering**, rules, and careful logging.

### How to design Phase 0

1. Define candidate pools first: popular items, category-matched items, and new arrivals.
2. Add strict eligibility filters before ranking (stock, delivery zone, policy/safety).
3. Use onboarding answers to build a lightweight user preference profile.
4. Rank candidates with simple heuristics: content similarity + popularity + business constraints.
5. Reserve a small fixed slot budget for exploration of new items (for example, a few positions per page).
6. Log every impression with rank position and context so Phase 2 training data is clean.

### How do we know if the user liked a recommended product?

In practice, we use **implicit feedback** from logged events after an impression:

- Positive: click, add-to-cart, purchase, long dwell.
- Negative/weak: skip or hide.
- Important: items the user never saw are **unknown**, not negative.

This is enough to start collecting useful preference labels without explicit star ratings.

### Logistics shopping example (Phase 0)

- New user in Graz opens the app; ask 2-3 onboarding questions (household size, preferred categories, delivery window).
- Build the first feed from three pools: in-stock local bestsellers, category-matched products, and new nearby merchants.
- Enforce hard constraints early: serviceable address, inventory availability, and delivery SLA.
- Keep a few exploration slots for newly onboarded restaurants/products to gather signal.
- Log impression -> click -> cart -> purchase events to prepare nightly ALS/BPR in Phase 2.

---

## Phase 2: The MVP (Months 1-3 - Batch Processing)

**Scenario:** You now have hundreds of thousands of interactions (clicks, likes, purchases). Personalization becomes feasible.
Yes, in this phase it is often better to **pre-compute recommendations at night** instead of scoring on every request.

### Practical architecture in this phase

- Build an implicit **User-Item Matrix** from clicks/add-to-cart/purchases.
- Train a nightly **collaborative filtering** model (ALS or BPR).
- Precompute top-{{< katex >}}N{{< /katex >}} recommendations per active user and store them in cache/DB.
- Serve directly from precomputed lists with lightweight business filters.
- Optionally use **XGBoost** as a practical ranking baseline before moving to full deep ranking.

### Matrix factorization (ALS-style)

{{< katex display >}}
\min_{P,Q}\sum_{u,i} c_{ui}\left(r_{ui}-p_u^\top q_i\right)^2+\lambda\left(\|p_u\|^2+\|q_i\|^2\right)
{{< /katex >}}

This captures patterns like: *users who bought X also bought Y*.

### Pairwise alternative (BPR)

{{< katex display >}}
\mathcal{L}_{BPR}=-\sum_{(u,i,j)}\log \sigma\left(p_u^\top q_i-p_u^\top q_j\right)+\lambda\|\Theta\|^2
{{< /katex >}}

In practice, Phase 2 is often hybrid:

{{< katex display >}}
s_{mvp}(u,i)=\alpha\cdot p_u^\top q_i + (1-\alpha)\cdot s_{content}(u,i)
{{< /katex >}}

This keeps collaborative filtering as the main engine while content-based filtering handles new/sparse items.

Apply time decay in batch features so recent behavior matters more:

{{< katex display >}}
\tilde{r}_{ui}=\sum_{e\in \mathcal{E}_{ui}}\exp\left(-\lambda(T-t_e)\right)\cdot r_e
{{< /katex >}}

### Logistics shopping example (Phase 2)

- Build matrix from orders, carts, and repeat purchases.
- Nightly ALS/BPR precompute user lists such as: rice -> cooking oil -> detergent.
- Blend with content-based scores for newly onboarded SKUs with no purchase history.

---

## Phase 3: Scalable Architecture (The Industry Standard)

**Scenario:** You now have millions of users and millions of items. Nightly full precomputation is too slow and wasteful. You must generate recommendations on request.

### Practical solution: Multi-Stage Funnel Architecture

You cannot score 10M items with a heavy model in 100ms. Use a funnel:

**Retrieval -> Ranking -> Re-ranking**

1. **Retrieval (Two-Tower + ANN)**
   - Fetch top-{{< katex >}}K_r{{< /katex >}} candidates fast from a vector index.
   - Collaborative signal: interaction-trained user/item towers.
   - Content-based signal: metadata/content tower for cold or newly launched items.
2. **Ranking (DCN v2)**
   - Apply heavier personalized scoring on the reduced candidate set.
   - Two-Tower is optimized for fast recall, while DCN v2 captures richer feature interactions for precise final ordering.
3. **Re-ranking**
   - Enforce diversity, freshness, business rules, and safety constraints.

Typical online flow:

{{< katex display >}}
10^7\ \text{items} \rightarrow 10^3\ \text{candidates} \rightarrow 10^2\ \text{ranked} \rightarrow K\ \text{served}
{{< /katex >}}

### How are the deep networks trained? (and sampling)

For Two-Tower and DCN v2, training data comes from logged impressions and outcomes.

1. Build tuples from logs:
   - positives {{< katex >}}(u,i^+){{< /katex >}} from clicked/purchased impressions
   - negatives {{< katex >}}(u,i^-){{< /katex >}} from shown-but-not-clicked items
   - **unseen items are unknown, not negative**
   - train mostly on exposed set {{< katex >}}E_u{{< /katex >}}:
{{< katex display >}}
(u,i)\in E_u \quad \text{with sampled negatives}
{{< /katex >}}
2. Train in mini-batches with mixed negatives:
   - **hard negatives**: exposed but skipped
   - **in-batch negatives**: other positives in the same batch
   - **random negatives**: sampled catalog items for coverage

Two-Tower retrieval loss (sampled softmax):

{{< katex display >}}
\mathcal{L}_{retr}=-\log\frac{\exp(s(u,i^+)/\tau)}{\exp(s(u,i^+)/\tau)+\sum_{j\in N(u)}\exp(s(u,j)/\tau)}
{{< /katex >}}

DCN v2 ranking loss (pointwise):

{{< katex display >}}
\mathcal{L}_{rank}=-\sum_n\left[y_n\log\hat{y}_n+(1-y_n)\log(1-\hat{y}_n)\right]
{{< /katex >}}

Because positives are sparse, handle class imbalance with weighted loss:

{{< katex display >}}
\mathcal{L}_{wbce}=-\sum_n\left[w^+y_n\log\hat{y}_n+w^-(1-y_n)\log(1-\hat{y}_n)\right],\quad w^+>w^-
{{< /katex >}}

Optional focal variant for rare positives:

{{< katex display >}}
\mathcal{L}_{focal}=-\sum_n\left[y_n(1-\hat{y}_n)^\gamma\log\hat{y}_n+(1-y_n)\hat{y}_n^\gamma\log(1-\hat{y}_n)\right]
{{< /katex >}}

In the logistics case, sample negatives from **available items in the same delivery zone** so training matches real serving constraints.

### Freshness and new nearby items

When a new restaurant or new product appears nearby, inject it through content/geolocation retrieval and boost freshness in ranking/re-ranking.

{{< katex display >}}
b_{fresh}(i)=\exp(-\lambda\cdot \text{age}_i),\qquad s'_{rank}(u,i)=s_{rank}(u,i)+\eta b_{fresh}(i)
{{< /katex >}}

Use time decay both in features (Phase 2) and online re-ranking (Phase 3) to avoid stale recommendations.

### Evaluation and rollout in Phase 3

Use these metrics and release steps before full rollout:

- **Offline retrieval**: Recall
- **Offline ranking**: AUC, PR-AUC
- **Guardrails**: latency, complaint/cancel rate, fairness/diversity.


### Why this scales

- Retrieval remains sublinear with ANN search.
- Ranking compute is focused only where it matters.
- Re-ranking protects user experience and policy constraints.

### Logistics shopping example (Phase 3)

- Retrieval: Two-Tower ANN over millions of catalog items across warehouses.
- Ranking: DCN v2 with stock, ETA, shipping cost and margin features.
- Re-ranking: enforce serviceability, delivery SLA, basket diversity and policy constraints.

Example logistics-aware final score:

{{< katex display >}}
s_{final}(u,i)=s_{rank}(u,i)+\alpha\cdot \text{availability}_i-\beta\cdot \text{ETA}_i-\gamma\cdot \text{shipping\_cost}_i
{{< /katex >}}
