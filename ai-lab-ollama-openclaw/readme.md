para resolver problema de: "origin not allowed (open the Control UI from the gateway host or allow it in gateway.controlUi.allowedOrigins)"
-> docker exec openclaw node dist/index.js config set --batch-json '[{"path":"gateway.controlUi.allowedOrigins","value":["http://localhost:18789","http://127.0.0.1:18789","http://macmini-lab.local:18789","http://192.168.0.200:18789"]}]'

Para entrar en openclaw con el token:
docker exec openclaw node dist/index.js config set gateway.auth.token "admin_token_2026"
docker restart openclaw


cuando salga:
device pairing required
ejecutar:
docker exec openclaw node dist/index.js devices approve [ID_DISPOSITIVO] --token admin_token_2026



Para bajar llama 3.2:1b
-> docker exec ollama ollama pull llama3.2:1b
-> docker exec openclaw node dist/index.js config set agents.defaults.model.primary "ollama/llama3.2:1b"
-> docker restart openclaw



error en openclaw de ollama:
Unknown model: ollama/llama3.2:1b. Ollama requires authentication to be registered as a provider. Set OLLAMA_API_KEY
-> docker exec openclaw sh -c "echo 'OLLAMA_API_KEY=macmini_dummy_key' >> /home/node/.openclaw/.env"
-> docker exec openclaw node dist/index.js config set providers.ollama.apiKey "macmini_dummy_key"
-> docker restart openclaw