# Memoripy

**Memoripy** is a Python library designed to manage and retrieve context-aware memory interactions using both short-term and long-term storage. It supports AI-driven applications requiring memory management, with compatibility for OpenAI and Ollama APIs. Features include contextual memory retrieval, memory decay and reinforcement, hierarchical clustering, and graph-based associations.

## Features

- **Short-term and Long-term Memory**: Manages memory as short-term or long-term based on usage and relevance.

- **Contextual Retrieval**: Retrieves memories based on embeddings, concepts, and past interactions.

- **Concept Extraction and Embeddings**: Uses OpenAI or Ollama models for concept extraction and embedding generation.

- **Graph-Based Associations**: Builds a concept graph and uses spreading activation for relevance-based retrieval.

- **Hierarchical Clustering**: Clusters similar memories into semantic groups to aid in contextually relevant retrieval.

- **Decay and Reinforcement**: Older memories decay over time, while frequently accessed memories are reinforced.

## Installation

You can install Memoripy with pip:

```bash
pip install memoripy
```

## Usage
The following example demonstrates how to set up and use Memoripy in a Python script.

### Example: `example.py`
This example script shows the primary functionality of Memoripy, including initialization, storing interactions, retrieving relevant memories, and generating responses.

```bash
from memoripy import MemoryManager, JSONStorage

def main():
    # Replace 'your-api-key' with your actual OpenAI API key
    api_key = "your-key"
    if not api_key:
        raise ValueError("Please set your OpenAI API key.")

    # Define chat and embedding models
    chat_model = "openai"           # Choose 'openai' or 'ollama' for chat
    chat_model_name = "gpt-4o-mini" # Specific chat model name
    embedding_model = "ollama"      # Choose 'openai' or 'ollama' for embeddings
    embedding_model_name = "mxbai-embed-large"  # Specific embedding model name

    # Choose your storage option
    storage_option = JSONStorage("interaction_history.json")
    # Or use in-memory storage:
    # from memoripy import InMemoryStorage
    # storage_option = InMemoryStorage()

    # Initialize the MemoryManager with the selected models and storage
    memory_manager = MemoryManager(
        api_key=api_key,
        chat_model=chat_model,
        chat_model_name=chat_model_name,
        embedding_model=embedding_model,
        embedding_model_name=embedding_model_name,
        storage=storage_option
    )

    # New user prompt
    new_prompt = "My name is Khazar"

    # Load the last 5 interactions from history (for context)
    short_term, _ = memory_manager.load_history()
    last_interactions = short_term[-5:] if len(short_term) >= 5 else short_term

    # Retrieve relevant past interactions, excluding the last 5
    relevant_interactions = memory_manager.retrieve_relevant_interactions(new_prompt, exclude_last_n=5)

    # Generate a response using the last interactions and retrieved interactions
    response = memory_manager.generate_response(new_prompt, last_interactions, relevant_interactions)

    # Display the response
    print(f"Generated response:\n{response}")

    # Extract concepts for the new interaction
    combined_text = f"{new_prompt} {response}"
    concepts = memory_manager.extract_concepts(combined_text)

    # Store this new interaction along with its embedding and concepts
    new_embedding = memory_manager.get_embedding(combined_text)
    memory_manager.add_interaction(new_prompt, response, new_embedding, concepts)

if __name__ == "__main__":
    main()
```
## Classes and Modules
- `MemoryManager`: Manages memory interactions, retrieves relevant information, and generates responses based on past interactions.

- `MemoryStore`: Stores and organizes interactions in short-term and long-term memory, with support for clustering and retrieval based on relevance.

- `InMemoryStorage` and `JSONStorage`: Store memory in either in-memory data structures or JSON files.

- `BaseStorage`: Abstract base class for defining storage methods.

##Core Functionalities
1. **Initialize Memory**: Load previous interactions from the chosen storage and initialize memory.

2. **Add Interaction**: Store a new interaction with its embedding, concepts, prompt, and output.

3. **Retrieve Relevant Interactions**: Search past interactions based on a query using cosine similarity, decay factors, and spreading activation.

4. **Generate Response**: Combine the current prompt and retrieved interactions to generate a contextually relevant response.

5. **Decay and Reinforcement**: Increase decay on unused memories and reinforce frequently accessed memories.

## Requirements
Memoripy relies on several dependencies, including:

- `openai`

- `faiss-cpu`

- `numpy`

- `networkx`

- `scikit-learn`

- `langchain`

- `ollama`

These dependencies will be installed automatically with pip install memoripy.

## License
Memoripy is licensed under the MIT License.

## Contributing
Contributions are welcome! Feel free to open issues or submit pull requests for improvements.