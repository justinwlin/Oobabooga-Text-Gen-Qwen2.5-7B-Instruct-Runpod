# Oobabooga Text Generation Server (Runpod Ready)

[![RunPod](https://api.runpod.io/badge/justinwlin/Oobabooga-Text-Gen-Qwen2.5-7B-Instruct-Runpod)](https://www.runpod.io/console/hub/justinwlin/Oobabooga-Text-Gen-Qwen2.5-7B-Instruct-Runpod)

This project provides a Dockerized oobabooga text generation server, ready for deployment on Runpod with serverless support (or even Pod support). It exposes an OpenAI-compatible API endpoint on port 5000, making it easy to integrate with existing OpenAI API clients.

# Variants

This project is available in two main variants, each suited for different hardware and use cases:

| Variant                | Model Size | Best For                        | Link                                                                 |
|------------------------|-----------|---------------------------------|----------------------------------------------------------------------|
| Qwen 2.5 7B Instruct   | 7B        | Local dev, research, lightweight | [Qwen 2.5 7B Instruct Repo](https://github.com/justinwlin/Oobabooga-Text-Gen-Qwen2.5-7B-Instruct-Runpod) |
| Qwen 2.5 32B Instruct  | 32B       | maximum performance, complex tasks   | [Qwen 2.5 32B Instruct Repo](https://github.com/justinwlin/Oobabooga-Text-Gen-Qwen2.5-32B-Instruct-Runpod) |

**Qwen 2.5 7B Instruct**  
A smaller, resource-efficient variant.  
- Good for more straight forward questions and light weight chatbot.

**Qwen 2.5 32B Instruct**  
A larger, more powerful variant.  
- Best for complex and layered tasks.

> For a detailed comparison, see the [Qwen 2.5 7B vs 32B section](#qwen-25-7b-vs-32b-key-differences) below.

## Features
- Dockerfile for oobabooga/text-generation-webui
- Runpod serverless compatibility
- OpenAI-compatible API endpoint on port 5000
- Simple handler for forwarding requests

## Quick Start

> **Note:** When deploying to RunPod, set the following environment variables in your RunPod template or deployment environment:
> - `MODE_TO_RUN`: Set to `serverless` to enable the handler, or `pod` for standard pod mode

### Build and Push to RunPod

#### Build Docker
```bash
depot build . -t dockerusername/repo_name:1.0 --platform linux/amd64 --push
```
#### Push to RunPod
You can make a template with Runpod, and specify the Docker image.

### Github Integration
#### Deploy to RunPod using Github integration
You are able to point to this repository using RunPod github integration.

### Changing the Prebaked Model

To use a different model, update the following environment variables during build time:

- `MODEL_NAME`: Set this to the HuggingFace model repository you want to use (e.g., `TheBloke/Llama-2-7B-GGUF`)
- `MODEL_FILE`: Set this to the specific model file you want to load (e.g., `llama-2-7b.Q4_K_M.gguf`)

Make sure the model you specify is compatible with oobabooga/text-generation-webui and is supported by your hardware.

After changing these variables, redeploy your pod or serverless endpoint.

> **Tip:** If you're unsure which models are compatible, you can ask ChatGPT or Claude for help selecting a model that works with the oobabooga/text-generation-webui repository.

### Example Input (for handler.py or direct API call)
```json
{
  "model": "Qwen2.5-7B-Instruct-Q4_K_M.gguf",
  "prompt": "Write a short story about a robot learning to love.",
  "max_tokens": 100,
  "temperature": 0.7
}
```

- `model`: The model file name (should match the deployed model)
- `prompt`: The text prompt to generate from
- `max_tokens`: (Optional) Maximum number of tokens to generate
- `temperature`: (Optional) Sampling temperature

#### Note:
The input is passed along to the OpenAI compatible server that Text Gen Web UI spins up. So theoretically any parameters supported by OpenAI compatible API endpoint should work. You can modify the handler.py further for any additional functionality you need.

Read more on [text generation web UI wiki](https://github.com/oobabooga/text-generation-webui/wiki/12-%E2%80%90-OpenAI-API)

### Why Use a Prebaked Model?

Using a prebaked model (one that is downloaded and set up during the image build) offers significant advantages:

- **Faster Cold-Start:** The model is already present in the image, so loading it into memory at container startup is much faster. This is especially important for serverless or on-demand deployments where startup latency matters.
- **Predictable Startup:** You avoid delays from downloading large models at runtime, making deployments more reliable and consistent.

### Model and Image Size Recommendations

- **Keep It Under 20–25GB:** Try to keep your final Docker image (including the model) under 20–25GB. Images larger than this can result in very slow initial Docker loading and deployment times, especially on cloud or serverless platforms.
- **Why?** Large images take longer to pull, extract, and start, which can impact both developer experience and production reliability.

If your model is very large, consider using a smaller quantized version or optimizing your image to include only the necessary files.

## Requirements
- Docker
- Python 3.x
- See `requirements.txt` for Python dependencies

## Development
- Update `handler.py` to customize request forwarding or add pre/post-processing.
- Add new dependencies to `requirements.txt` and rebuild the Docker image.
