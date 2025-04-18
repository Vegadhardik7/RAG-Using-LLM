# Sun Tzu RAG: A Retrieval-Augmented Generation System

Welcome to **Sun Tzu RAG**, a powerful Retrieval-Augmented Generation (RAG) system that extracts insights from Sun Tzu's *"The Art of War"* using natural language processing. This project transforms the classic text into a searchable knowledge base, allowing users to query strategic wisdom with modern AI techniques. Built with Python, SentenceTransformers, FAISS, and FastAPI, it’s a showcase of scalable NLP and deployment-ready ML.

## Why This Project?

*"The Art of War"* is a timeless guide to strategy, but its dense prose can be hard to navigate. This project makes it accessible by:
- Extracting and cleaning text from the PDF.
- Creating sentence-level embeddings with `all-MiniLM-L6-v2`.
- Indexing embeddings with FAISS for fast retrieval.
- Serving queries via a FastAPI endpoint for real-time answers.

Whether you’re a developer exploring RAG, a student studying Sun Tzu, or an ML enthusiast, this repo offers a practical, end-to-end NLP pipeline with deployment in mind.

## Features

- **PDF Processing**: Extracts and cleans text from *"The Art of War"* PDF using PyPDF2.
- **Sentence Embeddings**: Generates semantic embeddings with SentenceTransformers for contextual understanding.
- **FAISS Indexing**: Enables lightning-fast similarity search for relevant passages.
- **FastAPI Endpoint**: Provides a REST API for querying the knowledge base.
- **Colab-Friendly**: Optimized for Google Colab with clear setup instructions.
- **Save & Load**: Persists the model, FAISS index, and dataset for easy reuse.

## Tech Stack

- **Python**: Core programming language.
- **PyPDF2**: PDF text extraction.
- **NLTK**: Sentence tokenization.
- **SentenceTransformers**: Embedding generation (`all-MiniLM-L6-v2`).
- **FAISS**: Efficient vector search.
- **Datasets**: Hugging Face library for data management.
- **FastAPI**: API for deployment.
- **Uvicorn**: ASGI server for FastAPI.
- **Google Colab**: Development environment.

## Getting Started

### Prerequisites

- Python 3.9+
- Google Colab (optional, for cloud-based setup)
- `The_Art_of_War-Sun_Tzu.pdf` (place in project root or `/content/` for Colab)

### Installation

1. **Clone the Repository**:
   ```bash
   git clone https://github.com/<your-username>/sun-tzu-rag.git
   cd sun-tzu-rag
   ```

2. **Install Dependencies**:
   ```bash
   pip install PyPDF2 nltk datasets sentence-transformers faiss-cpu fastapi uvicorn pydantic numpy
   ```

   In Google Colab:
   ```python
   !pip install PyPDF2 nltk datasets sentence-transformers faiss-cpu fastapi uvicorn pydantic numpy
   ```

3. **Download NLTK Data**:
   ```python
   import nltk
   nltk.download('punkt')
   ```

4. **Upload PDF (Colab)**:
   ```python
   from google.colab import files
   uploaded = files.upload()  # Upload The_Art_of_War-Sun_Tzu.pdf
   ```

### Usage

1. **Run the Script**:
   ```bash
   python rag.py
   ```

   This:
   - Processes the PDF.
   - Creates embeddings and a FAISS index.
   - Saves the model (`sentence_transformer_model/`), index (`faiss_index.bin`), and dataset (`rag_dataset/`).
   - Runs an example query: *"What are five ways of attack?"*

2. **Query the Model**:
   - The script outputs results for the example query.
   - To query via API, run the FastAPI server (see Deployment).

### Example Output

```plaintext
Query: What are five ways of attack?
Scores: [0.2345, 0.2678, 0.2981]
Contexts: ["The leader is the arbiter of the people's fate...", "When you engage in actual fighting...", "There are five ways of attacking with fire..."]
Answer: The leader is the arbiter of the people's fate... When you engage in actual fighting... There are five ways of attacking with fire...
```

## Deployment

### Local FastAPI Server

1. **Run the Server**:
   ```bash
   uvicorn rag:app --host 0.0.0.0 --port 8000
   ```

2. **Test the API**:
   ```bash
   curl -X POST "http://localhost:8000/query" -H "Content-Type: application/json" -d '{"query": "What are five ways of attack?", "k": 3}'
   ```

   Response:
   ```json
   {
     "query": "What are five ways of attack?",
     "scores": [0.2345, 0.2678, 0.2981],
     "contexts": ["The leader is the arbiter...", "..."],
     "answer": "The leader is the arbiter... ..."
   }
   ```

### Google Colab

Colab doesn’t support `uvicorn` directly. Use this workaround:
```python
from nest_asyncio import apply
import nest_asyncio
nest_asyncio.apply()
import uvicorn
uvicorn.run(app, host="0.0.0.0", port=8000)
```

### Docker

1. **Create `requirements.txt`**:
   ```
   PyPDF2
   nltk
   datasets
   sentence-transformers
   faiss-cpu
   fastapi
   uvicorn
   pydantic
   numpy
   ```

2. **Create `Dockerfile`**:
   ```dockerfile
   FROM python:3.9
   WORKDIR /app
   COPY . .
   RUN pip install -r requirements.txt
   CMD ["uvicorn", "rag:app", "--host", "0.0.0.0", "--port", "8000"]
   ```

3. **Build and Run**:
   ```bash
   docker build -t sun-tzu-rag .
   docker run -p 8000:8000 sun-tzu-rag
   ```

### Cloud Deployment

- **AWS ECS**: Push the Docker image to ECR, deploy with ECS, and store model files in S3.
- **Azure App Service**: Use Azure’s Docker support, with Blob Storage for model files.
- **Heroku**: Deploy with a `Procfile`:
  ```
  web: uvicorn rag:app --host=0.0.0.0 --port=$PORT
  ```

## Project Structure

```
sun-tzu-rag/
├── rag.py                 # Main script for RAG pipeline and API
├── The_Art_of_War-Sun_Tzu.pdf  # Input PDF
├── requirements.txt       # Dependencies
├── Dockerfile             # Docker configuration
├── faiss_index.bin        # Saved FAISS index
├── rag_dataset/           # Saved dataset
├── sentence_transformer_model/  # Saved SentenceTransformer model
```

## How It Works

1. **PDF Processing**: Extracts text from the PDF using PyPDF2, cleaning it with regex to normalize whitespace.
2. **Sentence Tokenization**: Uses NLTK to split text into sentences, filtering out short ones (<3 words).
3. **Embedding Generation**: Encodes sentences with `all-MiniLM-L6-v2` for semantic search.
4. **FAISS Indexing**: Builds a FAISS index for fast similarity search.
5. **Querying**: Retrieves top-k relevant passages for a query, combining them into a coherent answer.
6. **API**: Serves queries via FastAPI for real-time access.

## Potential Applications

- **Education**: Query strategic insights for academic study or personal growth.
- **Chatbots**: Integrate into a conversational AI for historical or strategic advice.
- **Research**: Extend to other texts (e.g., philosophy, literature) for NLP research.
- **APIs**: Build a public API for strategy enthusiasts or developers.

## Contributing

Contributions are welcome! To contribute:
1. Fork the repo.
2. Create a feature branch (`git checkout -b feature/awesome-feature`).
3. Commit changes (`git commit -m 'Add awesome feature'`).
4. Push to the branch (`git push origin feature/awesome-feature`).
5. Open a pull request.

Please include tests and update documentation as needed.

## Troubleshooting

- **PDF Not Found**: Ensure `The_Art_of_War-Sun_Tzu.pdf` is in `/content/` (Colab) or the project root.
- **No Sentences**: If the dataset is empty, adjust `min_words` in `prepare_dataset`.
- **API Errors**: Check FastAPI logs for issues with model loading or query processing.
- **Colab Uvicorn**: Use the `nest_asyncio` workaround for API testing.


## About the Author

I’m Hardik Vegad, a Software Engineer and AI enthusiast with 3+ years of experience in Python, ML, and backend development. Check out my [GitHub](https://github.com/Veoadhardik7), [LinkedIn](https://www.linkedin.com/in/vegadhardik7/) and [Portfolio](https://infinitycodex.github.io/Hardik_Vegad_Resume/) for more projects!
