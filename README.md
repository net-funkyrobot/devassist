# Devassist – Self hosted coding model service

Uses Ollama and Google Cloud Run to create a serverless coding model service that scales to zero when not being used. The service can be integrated into Cursor (or other tools that support custom OpenAI base URLs) and used in place of OpenAI.

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
gcloud run services proxy funkyrobot-devassist --port=[desired-port] --region {runtime-region}
```
