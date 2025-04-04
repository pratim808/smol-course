# AI Chatbot for ExtraMarks

This repository contains the code for an AI-powered chatbot designed to assist students with Class 12 Physics queries. The chatbot leverages advanced RAG pipeline to generate answers to physics-related questions. It is built using Python, LangChain, LangGraph, Google Gemini API, and other relevant tools.

## Features

- **Question Answering:** The chatbot provides answers to Class 12 Physics questions by utilizing a knowledge base of Q&A documents.
- **OCR Integration:** Allows students to upload images containing physics questions, which are then processed using Optical Character Recognition (OCR) to extract text and generate answers.
- **Session Management:** Tracks user interactions and conversation history for improved contextual understanding.
- **Real-time Query Classification:** Classifies queries into either knowledge-based or discussion-based to route them accordingly.
- **Document Retrieval:** Uses an ensemble retriever to fetch relevant physics documents to answer knowledge-based queries.
- **Discussion on Solutions:** The chatbot can engage in discussions based on the provided solutions, allowing for deeper exploration of the topic or clarification of the answer.

## Installation

### Prerequisites

Ensure the following dependencies are installed:

- Python 3.11
- Google Generative AI API key
- Hugging Face API token

### Setup
Follow these steps to set up the application:

1. **Install dependencies:**
```bash
pip install -r requirements.txt
```
3. **Set up environment variables:**
Create a `.env` file in the root directory and add the following:
```bash
GOOGLE_API_KEY=your_google_api_key
HUGGINGFACEHUB_API_TOKEN=your_huggingface_api_token
```

**Note:** Replace `your_google_api_key` with your actual Google API key, and `your_huggingface_api_token` with your actual Hugging Face API token.

3. **Run the application:**
```bash
streamlit run app.py
```


## Components

- **`app.py`:** The main interface for the chatbot, built with Streamlit. It handles user inputs, file uploads (for OCR), and displays the conversation.
- **`chat_history_manager.py`:** Manages and stores the chat history for each session, allowing the bot to remember previous interactions.
- **`config.py`:** Configuration file containing the setup for language models, embeddings, and other application settings.
- **`data_ingestion_pipeline.py`:** Handles the loading and processing of Class 12 Physics Q&A data from CSV files.
- **`graph_assembly.py`:** Assembles the workflow for handling queries, routing them through classification, retrieval, and generation steps.
- **`main.py`:** Main entry point for running the Physics QA pipeline.
- **`node_classifier.py`:** Classifies queries into either 'knowledge' (retrieval) or 'discussion' (direct conversation).
- **`node_generation.py`:** Generates final answers to questions based on context or chat history.
- **`node_retrieval.py`:** Retrieves relevant documents from the knowledge base based on user queries.
- **`retriever_setup.py`:** Configures the ensemble retriever for document retrieval using FAISS and BM25.
- **`system_prompts`:** A folder containing three essential prompts used in the chatbot workflow:
  - `query_classification.txt:` A prompt used by the QueryClassifier to classify the user's query as either 'knowledge' or 'discussion'.
  - `knowledge.txt:` A prompt used by the Generation node to generate answers to knowledge-based queries, utilizing relevant physics documents.
  - `discussion.txt:` A prompt used by the Generation node to generate answers for discussion-based queries, utilizing chat history.

## Usage

- **Ask a Question:** Simply type your question in the input field and hit "Enter". The chatbot will respond with an answer based on the knowledge base.
- **Upload an Image:** If you have a physics question in an image format (PNG, JPG, JPEG), you can upload the image, and the chatbot will extract the text using OCR to generate an answer.
- **View Processing Logs:** The processing logs are available for debugging and tracking. You can access them in the "Processing Log" section.

## Workflow

1. **User Query:** A user submits a query through text input or image upload.
2. **OCR Processing (if applicable):** If an image is uploaded, the OCR system extracts the text.
3. **Query Classification:** The chatbot classifies the query into either 'knowledge' (requiring document retrieval) or 'discussion' (simple conversation).
4. **Document Retrieval:** For knowledge queries, relevant documents are retrieved from the physics Q&A knowledge base.
5. **Answer Generation:** The final answer is generated based on the retrieved documents or chat history.
6. **Response Display:** The answer is displayed to the user, and the conversation is logged.
