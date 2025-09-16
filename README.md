# Devassist – Self hosted coding model service

Uses [Ollama](https://ollama.com/) and [Google Cloud Run](https://cloud.google.com/run/docs/overview/what-is-cloud-run) to create a serverless coding model service that scales to zero when not being used. The service can be integrated into code assistant agents like [Continue.dev](https://www.continue.dev/) in VSCode and Jetbrains IDEs. This will also work for any other tools that support either local Ollama endpoints OR custom OpenAI base URLs.

Ollama pulls [Mistral AI](https://mistral.ai/)'s [Codestral](https://ollama.com/library/codestral) open-weights code generation model.

The model can complete coding functions, write tests, and complete any partial code using a fill-in-the-middle mechanism.

Using Continue.dev, you don't need to sign into an account or use their cloud features to use your own OpenAI/Google/Anthropic API keys OR use Ollama locally (via the GCloud Run proxy).

This means all of your codebase, data and any prompts you submit stay inside your own private cloud infrastructure. None of your data is used for other purposes, such as training future models.


## Requirements

- GCloud CLI installed locally and authenticated


## Create cloud infrastructure

1. Tweak config in `infrastructure-environment`

**Don't commit these values.**

2. Source the infrastructure-environment shell script to make your config available in your shell.

```
source infrastructure-environment
```

3. Copy and run the commands from `infrastructure-commands.txt` in order to setup the Google Cloud infrastructure and run first deployment.

Run commands one by one to avoid race-conditions. Ensure you read and acknowledge the output and that each command has completed successfully.


## Configure and run the GCloud Run proxy

This allows your machine to access the service through a tunnel, authenticated with your GCloud CLI credentials. This approach means only you can access your private service and negates the need to secure the service on the internet.

```
gcloud run services proxy funkyrobot-devassist --port=11434 --region [runtime-region]
```

## Inspect StackDriver logs to monitor pulling of model weights

The container image uses an entrypoint script that pulls the weights for a model if they haven't been downloaded already.

This process takes time. You can monitor the progress through StackDriver logging:

https://console.cloud.google.com/logs/query

If it any point the Cloud Run instance terminates, it's likley because Cloud Run failed to establish a connection to the container within it's own timeout while Ollama was busy pulling model weights.

If this happens just load the proxy URL in a browser to start the service again.

## Use OpenWebUI to verify the model has loaded

To play around with the model before configuring it in an editor, you can use the OpenWebUI Docker image:

```
docker run --rm -p 3000:8080 --add-host=host.docker.internal:host-gateway -v open-webui:/app/backend/data --name open-webui ghcr.io/open-webui/open-webui:main
```

This will connect up to Ollama locally on Ollama's default port of 11434, which is currently being proxied to the Cloud Run app.

