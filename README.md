# Summary: Dmitry Kondrashkin's Talk on Zen's Recommendation System

## 1. Problem Background
- **Zen** = personalized feed (articles, videos, posts) in Yandex.
- **Scale**:
  - 10k requests/sec,
  - 10B daily events (likes, dislikes, views),
  - 100M users, 10M documents.
- **Challenges**:
  - Find relevant content in hundreds of milliseconds,
  - Incorporate very fresh user actions,
  - Filter outdated content.

## 2. Requirements
- **Speed**: response in ≤200 ms,
- **Freshness**: account for latest user activity,
- **Scalability**: manage 10M+ documents,
- **Flexibility**: support multiple content formats.

## 3. System Modeling
- **Vector-based recommendation**:
  - Users and documents are embedded into the same vector space,
  - **Cosine similarity** determines relevance.
- **Mathematical foundation**:
  - **Interaction matrix**: rows = users, columns = documents, values = +1/-1,
  - **SVD decomposition**: U (user vectors), D (document vectors),
  - **Optimization**: gradient descent minimizes prediction error.

## 4. Architecture Influence
- **Main components**:
  - Multiple recommenders (per content type),
  - **Blender**: merges recommendations considering user preferences,
  - **KNN index** (e.g., HNSW) for fast neighbor search.
- **Background processing**:
  - Every 2 weeks: full vector recalculation (SVD),
  - Every 30 minutes: document vector updates,
  - Real-time: user vector recalculation.

## 5. Engineering Approaches
- **Speed optimization**:
  - Document vectors stored in RAM (4GB for 10M vectors),
  - HNSW narrows search space to ~10k candidates in 10–20 ms.
- **Handling new data**:
  - New user vectors = real-time,
  - New document vectors = predicted from text (neural network).
- **Scalability**:
  - Kafka for event streaming,
  - Sharding by users.

## 6. Solution Optimality
- **Strengths**:
  - 25% faster responses,
  - Increased user vector diversity (10 → 100),
  - Fast deployment of new models.
- **Weaknesses**:
  - Cold start for new users/docs (needs thematic defaults),
  - Data noise and account sharing issues.

## 7. Conclusion
- **Zen's recommender** = ML models + engineering optimizations:
  - SVD + HNSW for search,
  - Real-time adaptation,
  - Regular background updates.

### Personal thoughts:
- Why not use **transformers** for embeddings (common in NLP)?
- **Cold start** remains a major challenge — could benefit from social graph features.
- Interested in how they handle **conflict of interests** (e.g., shared accounts).

### Open questions:
- How do they handle **account sharing conflicts** (e.g., child + parent)?
- More details desired on **anti-fraud/nakrutka** protection and **model calibration**.

### Key terms:
- **Embeddings**: vector representations,
- **HNSW**: efficient approximate nearest neighbor search,
- **SVD**: dimensionality reduction method.
