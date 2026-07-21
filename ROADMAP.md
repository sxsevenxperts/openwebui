# 🗺️ Roadmap - Open WebUI EasyPanel

**Última atualização**: 2026-07-21  
**Status geral**: ✅ Deploy MVP concluído  
**Próxima milestone**: Produção estável + RBAC

---

## 🎯 Versão 1.0 - Deploy MVP (CONCLUÍDO)

### ✅ Implementado
- [x] Dockerização com imagem oficial `ghcr.io/open-webui/open-webui:main`
- [x] Deploy automático via EasyPanel tRPC API
- [x] Variáveis de ambiente configuradas (SECRET_KEY, PORT=80, telemetria OFF)
- [x] Volume persistente em `/app/backend/data` (dados sobrevivem redeploy)
- [x] Domínio HTTPS configurado: `startups-openwebui.qfotry.easypanel.host`
- [x] Repositório GitHub com documentação
- [x] .dockerignore otimizado

### ⏳ Em Progresso
- [ ] Boot container (primeira pull 3.7GB) - ~10-15 min esperado
- [ ] Health endpoint respondendo 200 OK
- [ ] Interface web acessível
- [ ] Primeiro login admin

### 🔲 Próximos (v1.1)
- [ ] **Autenticação** - RBAC, roles (admin/user/viewer)
- [ ] **Integração LLM** - Ollama local ou OpenAI API
- [ ] **Modelos de embedding** - RAG com sentence-transformers
- [ ] **Backup automático** - Volume snapshots diários
- [ ] **Monitoramento** - Healthchecks, alertas, logs centralizados
- [ ] **CI/CD** - GitHub Actions para test/lint/build automático

---

## 📋 Dependências e Riscos

### Dependências
- EasyPanel API responsiva (VPS 164.68.116.21:3000)
- GitHub access token válido (ghp_XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX)
- FQDN resolving (startups-openwebui.qfotry.easypanel.host)
- Traefik + Let's Encrypt para HTTPS

### Riscos Técnicos
- ⚠️ **Imagem oficial 3.7GB** - Pull pode levar 10-15 min em rede lenta
- ⚠️ **Embedding models download** - Primeira inicialização baixa ~2GB em modelos
- ⚠️ **Credenciais expostas** - Token GitHub, senha EasyPanel compartilhados (ROTACIONAR após deploy)
- ⚠️ **Domínio zerado** - API updateDomains/updateRedirects sem implementação clara, manual fix necessário

### Débitos Técnicos
1. **Procedure mapping incompleto** - Alguns endpoints tRPC da API EasyPanel não estão bem documentados
2. **Volume restore** - Não há backup do volume persistente configurado
3. **Auth secrets** - WEBUI_SECRET_KEY é static (regenerar em produção)
4. **Logging** - Não há central de logs, apenas stdout do container

---

## 🚀 Próximas Ações Imediatas

1. **Validar boot** (agora) - Esperar HTTP 200 no health endpoint
2. **Criar admin account** - Primeira login no https://startups-openwebui.qfotry.easypanel.host
3. **Testar LLM** - Configurar modelo e fazer chat de teste
4. **Rotacionar credenciais** - Gerar novo GitHub token e trocar senha EasyPanel
5. **Setup backup** - Configurar snapshot diário do volume persistente

---

## 📊 Marcos Completados

| Data | Evento | Responsável |
|------|--------|------------|
| 2026-07-21 | Dockerização completada | Claude Haiku 4.5 |
| 2026-07-21 | Deploy EasyPanel via API | Claude Haiku 4.5 |
| 2026-07-21 | Documentação inicial | Claude Haiku 4.5 |

---

## 📞 Documentação Relacionada

- [DIARIO_DE_BORDO.md](DIARIO_DE_BORDO.md) - Log cronológico de ações
- [DEPLOY_EASYPANEL.md](DEPLOY_EASYPANEL.md) - Guia manual de deploy
- [Dockerfile](Dockerfile) - Build image oficial + volume
- [.dockerignore](.dockerignore) - Otimizações de build
- [GitHub Repo](https://github.com/sxsevenxperts/openwebui) - Source
