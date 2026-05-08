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



error en openclaw de ollama (no me funciono los comandos):
Unknown model: ollama/llama3.2:1b. Ollama requires authentication to be registered as a provider. Set OLLAMA_API_KEY
-> docker exec openclaw sh -c "echo 'OLLAMA_API_KEY=macmini_dummy_key' >> /home/node/.openclaw/.env"
-> docker exec openclaw node dist/index.js config set providers.ollama.apiKey "macmini_dummy_key"
-> docker restart openclaw
-> docker exec -it openclaw node dist/index.js configure
El base url debe ser: http://ollama:11434


Para probar ollama:
docker exec -it ollama ollama run llama3.2:1b "Hola, ¿estás funcionando correctamente?"


Para automentar timeout de ollama/openclaw:
docker exec openclaw node dist/index.js config set "agents.defaults.timeouts.idle" "300s"
docker exec openclaw node dist/index.js config set "agents.defaults.timeouts.total" "600s"


Para un warning que da en los logs:
docker exec openclaw node dist/index.js config set "gateway.trustedProxies" '["172.20.0.0/16"]'

Para desactivar el streaming:
docker exec openclaw node -e '
const fs = require("fs");
const path = "/home/node/.openclaw/openclaw.json";
let config = JSON.parse(fs.readFileSync(path, "utf8"));

// Aseguramos la estructura del ACP
if (!config.acp) config.acp = {};
if (!config.acp.stream) config.acp.stream = {};

// Apagamos el stream incremental y forzamos el buffer completo
config.acp.stream.deliveryMode = "final_only";

fs.writeFileSync(path, JSON.stringify(config, null, 2));
console.log("Streaming desactivado con éxito. Modo configurado a: final_only");
'



Para desactivar el thinking (NO FUNCIONO ESTO, en la config de openclaw se ve desactivado el thinking):
docker exec openclaw node -e '
const fs = require("fs");
const path = "/home/node/.openclaw/openclaw.json";
let config = JSON.parse(fs.readFileSync(path, "utf8"));

if (!config.agents) config.agents = {};
if (!config.agents.defaults) config.agents.defaults = {};

// Apagamos el motor de razonamiento
config.agents.defaults.thinking = "off";

fs.writeFileSync(path, JSON.stringify(config, null, 2));
console.log("Thinking mode inyectado como OFF directamente en el JSON.");
'


Para Incrementar el timeout (NO FUNCIONA):
docker exec openclaw node -e '
const fs = require("fs");
const path = "/home/node/.openclaw/openclaw.json";
let config = JSON.parse(fs.readFileSync(path, "utf8"));

if (!config.agents) config.agents = {};
if (!config.agents.defaults) config.agents.defaults = {};
if (!config.agents.defaults.llm) config.agents.defaults.llm = {};

// Inyectamos el timeout del foro (600 segundos = 10 minutos)
config.agents.defaults.llm.idleTimeoutSeconds = 600;

fs.writeFileSync(path, JSON.stringify(config, null, 2));
console.log("Éxito: idleTimeoutSeconds configurado a 600 dentro de agents.defaults.llm");
'
-> docker restart openclaw


Para revisar la config:
-> sudo nano /etc/komodo/stacks/ollama-openclaw/ai-lab-ollama-openclaw/data/openclaw-config/openclaw.json


Para Incrementar el timeout:
docker exec openclaw node -e '
const fs = require("fs");
const path = "/home/node/.openclaw/openclaw.json";
let config = JSON.parse(fs.readFileSync(path, "utf8"));

// Construimos la ruta oficial de la versión 2026
if (!config.models) config.models = {};
if (!config.models.providers) config.models.providers = {};
if (!config.models.providers.ollama) config.models.providers.ollama = {};

// Asignamos 600 segundos (10 minutos)
config.models.providers.ollama.timeoutSeconds = 600;

fs.writeFileSync(path, JSON.stringify(config, null, 2));
console.log("¡Éxito! timeoutSeconds configurado a 600 en models.providers.ollama");
'
-> docker restart openclaw
-> docker logs -f openclaw