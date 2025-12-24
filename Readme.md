# 🎥 Video RAG (POC)
**Amazon S3 + Bedrock Titan Multimodal Embeddings + ChromaDB + Optional OpenAI Vision**

This repository contains a **proof-of-concept implementation of Video Retrieval-Augmented Generation (Video RAG)**.

The system enables **semantic search over videos** by:
- Downloading short videos from Amazon S3
- Extracting representative frames with OpenCV
- Generating **multimodal embeddings** using **Amazon Bedrock Titan**
- Storing vectors in **ChromaDB**
- Retrieving relevant frames (with timestamps) from natural language queries
- Optionally generating human-readable summaries using **OpenAI vision models**

This project is designed for **learning, experimentation, and portfolio demonstration**, while keeping **cost-awareness and production concepts** front and center.

---

## 🧠 High-Level Architecture

### Indexing (one-time per video)
```
S3 Video
   ↓
Download locally (POC)
   ↓
Frame extraction (OpenCV, sampled)
   ↓
Frame normalization (JPEG, resize)
   ↓
Titan Multimodal Embeddings
   ↓
ChromaDB (vector store)
```

### Retrieval (per query)
```
User text query
   ↓
Titan Text Embedding
   ↓
Chroma similarity search
   ↓
Top-K frames + timestamps
```

### Optional Generation
```
Top frames
   ↓
OpenAI Vision model
   ↓
Natural-language explanation
```

---

## ✨ Key Features

- ✅ Multimodal Video RAG (text → video frames)
- ✅ Shared embedding space (Titan text + image)
- ✅ Timestamp-aware retrieval
- ✅ Cost-aware frame sampling and caching
- ✅ Failure-tolerant ingestion
- ✅ Colab-friendly notebook design
- ✅ Clean path to production architectures

---

## 📁 Recommended Project Structure

```
.
├── notebooks/
│   └── video_rag_poc.ipynb
├── data/
│   ├── videos/
│   └── frames/
├── chroma_video_db/
├── docs/
│   └── video_rag_docs.html
├── README.md
└── requirements.txt
```

---

## 🔐 Prerequisites

### AWS
- S3 bucket containing short videos (< 15 minutes)
- IAM permissions:
  - `s3:GetObject`
  - `s3:ListBucket`
  - `bedrock:InvokeModel`
- Environment variables:
```bash
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
AWS_DEFAULT_REGION
```

### OpenAI (Optional)
```bash
OPENAI_API_KEY
```

---

## 🔁 Typical Usage Flow

```python
video_keys = list_video_keys(BUCKET, VIDEO_PREFIX, max_keys=3)
collection = get_chroma_collection()

for vk in video_keys:
    stats = index_video_to_chroma(collection, BUCKET, vk)

hits = query_video_frames(collection, "beach", k=8)
show_frame_hits(hits, n=6)

summary = summarize_frames_with_openai("beach", hits, n_images=4)
print(summary)
```

---

## 🧩 Core Components

### S3 Utilities
- `get_s3_client()`
- `list_video_keys()`
- `download_s3_video()`

### Video Processing
- `extract_frames_opencv()`
- `safe_mkdir()`

### Image Normalization
- `normalize_image_to_jpeg_bytes()`

### Bedrock Embeddings
- `titan_embed_image_safe()`
- `titan_embed_text()`

### Vector Store
- `get_chroma_collection()`
- `make_frame_id()`
- `already_indexed()`

### End-to-End Indexing
- `index_video_to_chroma()`

### Retrieval & Visualization
- `query_video_frames()`
- `show_frame_hits()`

### Optional Generation
- `image_to_b64()`
- `summarize_frames_with_openai()`

---

## 💸 Cost Awareness

Main cost drivers:
- Number of frames embedded
- OpenAI Vision calls (optional)

Built-in cost controls:
- `sample_every_sec`
- `max_frames_per_video`
- `max_frames_total_run`
- Frame caching

---

## 🚀 Extensions & Next Steps

- Scene detection
- Audio transcription
- Segment summaries
- Managed vector stores
- Full AWS-native processing

---

## 📄 License

MIT (or your preferred license)
