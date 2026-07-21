# Deploy Open WebUI no EasyPanel

## Problema resolvido
- ❌ **Erro anterior**: "No start command could be found" (Nixpacks procurava por npm start)
- ✅ **Solução**: Dockerfile customizado que faz build do frontend SvelteKit + executa backend Python

## Configuração no EasyPanel

### 1. Altere o método de build
Na dashboard do seu projeto no EasyPanel:

1. **Vá para Settings → Build Configuration**
2. Mude de **"Nixpacks"** para **"Dockerfile"**
3. Aponte para **`./Dockerfile`** (raiz do projeto)

### 2. Variáveis de Ambiente (obrigatórias)
Configure essas variáveis antes do deploy:

```env
# Segurança
WEBUI_SECRET_KEY=seu-secret-key-aqui-mude-isso
OPENAI_API_KEY=sk-xxx (ou deixe vazio se não usar)

# Database (se aplicável)
DATABASE_URL=postgresql://user:pass@host/db

# URLs
OPENAI_API_BASE_URL=https://api.openai.com/v1
OLLAMA_BASE_URL=http://ollama:11434
```

### 3. Recursos recomendados
- **CPU**: 2 cores (mínimo 1)
- **RAM**: 2-4GB (mínimo 2GB para embedding models)
- **Storage**: 5-10GB (para modelos de embedding e cache)

### 4. Portas
- **Porta interna**: 8080 (exposta automaticamente)
- **Protocolo**: HTTP/HTTPS (EasyPanel gerencia SSL)

### 5. Health Check (automático)
O Dockerfile inclui health check em `http://localhost:8080/health`
- Intervalo: 30s
- Timeout: 10s
- Retries: 3

## Build time esperado
- **Primeira build**: 15-30 min (download de dependencies + modelos)
- **Builds subsequentes**: 5-10 min (cache de layers)

## Após o deploy

### Acesso inicial
1. Acesse `https://seu-dominio.com`
2. Crie admin account na primeira inicialização
3. Configure modelos e integrações

### Dados persistentes
Todos os dados são armazenados em `/app/backend/data`:
- ✅ Chats e conversas
- ✅ Embedding models cache
- ✅ Configurações

**Importante**: Configure volume persistente no EasyPanel para `/app/backend/data`

## Logs
Verifique logs em tempo real no EasyPanel:
```
Deployments → Seu Projeto → Logs
```

Procure por:
- `INFO: Uvicorn running on` → Backend iniciado ✅
- Erros de model loading → Model download pode estar em progresso

## Troubleshooting

### Build falha com "No space left"
- Aumentar espaço em disco (modelos são 2-5GB)

### Container inicia mas página fica em branco
- Esperar 30-60s (primeiro startup baixa modelos)
- Checar logs do backend

### API retorna 502/503
- Health check pode estar falhando
- Verificar logs: `curl http://localhost:8080/health`

## Otimizações futuras
Para melhorar performance:

1. **CUDA/GPU** (se disponível): Requer build arg `USE_CUDA=true`
2. **Slim mode**: Para ambientes com pouca RAM
3. **Ollama integrado**: Para usar LLMs locais

## Support
Documentação oficial: https://docs.openwebui.com/deployment/docker
GitHub Issues: https://github.com/open-webui/open-webui/issues
