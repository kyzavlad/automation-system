# Automation System

This repository contains a set of microservices running in Docker containers.
They implement a simple pipeline for downloading videos, generating clips,
editing them and finally publishing to social networks or YouTube.

## Services

1. **service1** – download a video using `yt-dlp` with cookies and transcribe it
   with Whisper. Endpoint: `POST /download-transcribe`.
2. **service2** – cut viral clips from a video. Endpoint: `POST /clip-video`.
3. **service3** – edit short clips (captions, music, fades). Endpoint: `POST /edit-shorts`.
4. **service4** – publish shorts to multiple platforms. Endpoint: `POST /publish-shorts`.
5. **service5** – publish long videos. Endpoint: `POST /publish-long`.

All services return JSON responses so they can be easily consumed from tools
like **n8n**.

## Usage

Build and run the stack:

```bash
docker-compose build
docker-compose up
```

## Example n8n workflow

1. **HTTP Request** – call `service1` (`/download-transcribe`) with `{ "videoUrl": "..." }`. Save `inputPath` and `segments`.
2. **HTTP Request** – call `service2` (`/clip-video`) with `{ "inputPath": "...", "segments": [...] }`. Save returned clip paths.
3. **HTTP Request** – call `service3` (`/edit-shorts`) with `{ "clips": [...] }`.
4. **HTTP Request** – call `service4` (`/publish-shorts`) or `service5` (`/publish-long`) to publish the result.

The output of each step can be used as input for the next one inside n8n.

## Environment collection

Run `./collect_env.sh` to gather information about your Docker and Python
setup. The script will create `env_report.tar.gz` which can be attached to
support requests.

## Configuration

Four sample configuration files are included in the repository root:

- `workflow-config.txt` – instructions for n8n HTTP request nodes.
- `ai-agent-prompt.txt` – system prompt for the AI agent.
- `accounts-config.json` – account details for each social platform.
- `api-keys.env` – API keys and tokens used by the services.

Fill the `ЗАПОЛНИТЬ` placeholders with real credentials before running the system.
