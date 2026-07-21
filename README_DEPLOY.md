# Open WebUI - Deploy EasyPanel

**Status**: ✅ MVP Deploy Concluído  
**Aplicação**: https://startups-openwebui.qfotry.easypanel.host  
**Repositório**: https://github.com/sxsevenxperts/openwebui

---

## 🚀 Quick Start

### Acesso
```bash
# URL pública
https://startups-openwebui.qfotry.easypanel.host

# Health check
curl https://startups-openwebui.qfotry.easypanel.host/health

# Status esperado quando pronto: HTTP 200
# {"status": true}
```

### Primeira Login
1. Abra a URL acima no browser
2. Clique em "Sign Up" para criar admin account
3. Email + Senha
4. Confirme

### Uso Básico
1. Settings → Models → Add model
2. Configure LLM (Ollama local ou OpenAI API)
3. New Chat → Selecione modelo → Start chatting

---

## 🐳 Docker Architecture

### Build
```dockerfile
# Imagem oficial pré-construída (Zero build overhead)
FROM ghcr.io/open-webui/open-webui:main

# Port 80 para Traefik routing
ENV PORT=80

# Volume persistente
VOLUME /app/backend/data
```

**Por que imagem oficial?**
- ✅ Zero complexidade Nixpacks
- ✅ Build 3x mais rápido
- ✅ Otimizado pelo time oficial

### Deployment

**Infraestrutura**:
- **Server**: EasyPanel + Traefik (Docker Compose)
- **IP**: 164.68.116.21:3000 (Dashboard)
- **Domain**: startups-openwebui.qfotry.easypanel.host
- **HTTPS**: Let's Encrypt automático

**Configuração no EasyPanel**:
```yaml
Service: openwebui
Type: App (Docker Image)

Source:
  Type: Image
  Image: ghcr.io/open-webui/open-webui:main

Environment:
  WEBUI_SECRET_KEY: <WEBUI_SECRET_KEY>
  PORT: 80
  SCARF_NO_ANALYTICS: true
  DO_NOT_TRACK: true
  ANONYMIZED_TELEMETRY: false

Storage:
  Volume: openwebui-data
  Mount Path: /app/backend/data

Domain:
  Host: startups-openwebui.qfotry.easypanel.host
  Port: 80
  HTTPS: Enabled
  Certificate Resolver: Let's Encrypt

Deploy:
  Replicas: 1
  Zero Downtime: true
```

---

## 📦 Dados Persistentes

**Volume**: `openwebui-data`  
**Path**: `/app/backend/data`

Dados que persistem entre redeploys:
- ✅ Chats e conversas
- ✅ Embedding models cache
- ✅ User accounts + settings
- ✅ Custom prompts

**Backup** (não configurado ainda):
```bash
# Manual backup (via Docker)
docker run --rm -v openwebui-data:/data -v $(pwd):/backup \
  alpine tar czf /backup/openwebui-backup.tar.gz -C /data .

# Restore
docker run --rm -v openwebui-data:/data -v $(pwd):/backup \
  alpine tar xzf /backup/openwebui-backup.tar.gz -C /data
```

---

## 🔄 Update & Redeploy

### Atualizar imagem para latest
```bash
# Via EasyPanel Dashboard:
1. Services → openwebui
2. Source → Update Image
3. Image: ghcr.io/open-webui/open-webui:latest
4. Deploy

# Ou via API:
curl -X POST http://164.68.116.21:3000/api/trpc/services.app.updateSourceImage \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "json": {
      "projectName": "startups",
      "serviceName": "openwebui",
      "image": "ghcr.io/open-webui/open-webui:latest"
    }
  }'
```

### Reiniciar container
```bash
# Via Dashboard: Services → openwebui → Restart
# Dados persistem (volume intacto)
```

---

## 🛠️ Troubleshooting

### Página em branco
```bash
# 1. Esperar 30-60s (primeira vez baixa modelos)
# 2. Limpar cache do browser (Ctrl+Shift+Del)
# 3. Verificar console (F12 → Console)
# 4. Check logs: Dashboard → Deployments → Logs
```

### HTTP 502/503
```bash
# 1. Container pode estar rebooting
# 2. Esperar 2-5 min
# 3. Se persistir, check logs ou reiniciar

curl https://startups-openwebui.qfotry.easypanel.host/health -v
```

### Volume não persiste
```bash
# Verificar mount
docker volume ls | grep openwebui-data

# Recriar
docker volume rm openwebui-data  # ⚠️ Perde dados!
# Redeploy via Dashboard
```

---

## 🔐 Segurança

### Credenciais (ROTACIONAR IMEDIATAMENTE)

**Críticas**:
- [ ] GitHub Token: `ghp_XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX`
- [ ] EasyPanel Password: `<EASYPANEL_PASSWORD>`
- [ ] EasyPanel API Token (rotacionar)

**Ações**:
1. Trocar senha do EasyPanel
2. Gerar novo GitHub personal access token
3. Atualizar source config no EasyPanel
4. Regenerar WEBUI_SECRET_KEY para produção

### Proteção
```bash
# Secrets NOT em git:
✅ Env vars via Dashboard (nunca .env commitado)
✅ Volume perms (755)
✅ HTTPS obrigatório
```

---

## 📊 Performance & Monitoring

### Recursos atuais
- **CPU**: ~0.5-1 core (ocioso)
- **RAM**: ~1-2 GB (boot + modelos)
- **Disk**: ~5-10 GB (volume + modelos)

### Health Check
```bash
curl -s https://startups-openwebui.qfotry.easypanel.host/health | jq
{
  "status": true
}
```

### Logs
```bash
# Via EasyPanel Dashboard
Deployments → openwebui → Logs

# Procurar por:
✓ "Uvicorn running on" → App started
✓ "Loading model" → Model initialization
✗ "Error" → Problemas
```

---

## 📚 Documentação

- **Roadmap**: [ROADMAP.md](ROADMAP.md)
- **Diário de Bordo**: [DIARIO_DE_BORDO.md](DIARIO_DE_BORDO.md)
- **Deploy Manual**: [DEPLOY_EASYPANEL.md](DEPLOY_EASYPANEL.md)
- **Official Docs**: https://docs.openwebui.com
- **GitHub**: https://github.com/open-webui/open-webui

---

## 🔗 Links Úteis

| Recurso | URL |
|---------|-----|
| **Aplicação** | https://startups-openwebui.qfotry.easypanel.host |
| **EasyPanel** | http://164.68.116.21:3000 |
| **GitHub Repo** | https://github.com/sxsevenxperts/openwebui |
| **Open WebUI Docs** | https://docs.openwebui.com |

---

**Última atualização**: 2026-07-21  
**Próxima**: Setup CI/CD + Backup automático
