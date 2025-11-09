## Start server with VRAM and GPU
Go to vast.ai or runpod.io and find a server which has at least:
- 24GB VRAM
Start it.  
Connect to it over ssh:  
`ssh -p 7709 root@163.5.212.63 -L 8080:localhost:8080`


## Download LLM and serve it:
```bash
curl -fsSL https://ollama.com/install.sh | sh
systemctl enable ollama
systemctl start ollama
ollama --version
ollama pull qwen2.5-coder:7b
ollama pull qwen2.5-coder:7b-instruct-q4_K_M
ollama run qwen2.5-coder:7b (successful interaction with model)
ps aux | grep ollama (could see that ollama was already serving on port 11434)
```

## Connect to the LLM via ssh
`ssh -i ~/.ssh/id_ed25519 -p 7709 root@163.5.212.63 -L 11434:localhost:11434` (one would think we could drop the -i here)  
Test the connection via curl call to:  
```
curl -X POST http://localhost:11434/v1/chat/completions   -H "Content-Type: application/json"   -d '{
        "model": "qwen2.5-coder:7b",
        "messages": [{"role":"user","content":"Hello, can you introduce yourself?"}]
      }'
```

## Spin up OpenDevin image
(Something not fully working here:)  
```
docker run --rm -d --name opendevin_test --network host   -e LLM_BASE_URL="http://localhost:11434"   -e LLM_MODEL="qwen2.5-coder:7b"   -e LLM_DEFAULT_MODEL="qwen2.5-coder:7b"   -e WORKSPACE_MOUNT_PATH="$HOME/opendevin_workspace"   -e SANDBOX_USER_ID="lemcact"   -v $HOME/opendevin_workspace:/opt/workspace_base   -v /var/run/docker.sock:/var/run/docker.sock   ghcr.io/opendevin/opendevin:main

```
