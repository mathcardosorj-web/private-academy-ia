# API IA Matheus - Private Academy

API customizada que atua como gerente virtual da Private Academy, atendendo clientes via WhatsApp através de integração com BotConversa + Claude Haiku 4.5 (Anthropic).

**Versão atual:** 7.4 (Claude Haiku 4.5 — áudios desativados)

---

## 🎯 O que faz

A IA simula um consultor humano chamado "Matheus", que:

- ✅ Conduz qualificação consultiva de leads
- ✅ Atende 2 produtos diferentes (Funil 1 + Funil 2)
- ✅ Detecta automaticamente qual produto o lead veio buscar
- ✅ Apresenta a Love Tradding como financeira oficial
- ✅ Transfere para equipe comercial de forma sutil
- ✅ Decide entre enviar 1 ou 2 mensagens conforme contexto

---

## 🚀 Stack Tecnológica

| Componente | Tecnologia |
|---|---|
| Runtime | Node.js 18+ |
| Framework | Express 4.21 |
| LLM SDK | @anthropic-ai/sdk 0.30 |
| Modelo IA | claude-haiku-4-5-20251001 |
| Hospedagem | Render.com |
| Frontend | BotConversa PRO (WhatsApp Business) |

---

## 📋 Os 2 Funis

### 🔵 Funil 1 — Método Recuperação de Banca
- **Trader:** Bruno (formado em Economia)
- **Público:** Quem perdeu capital e quer reconstruir
- **Foco:** Gestão de banca, controle emocional, métodos validados
- **Pilares:** 5 (gestão, risco, emocional, métodos, análise)
- **Gatilho `funil_origem`:** `recuperacao_banca`

### 🟠 Funil 2 — Compartilhamento de Receita / Alavancagem
- **Trader:** Igor
- **Público:** Quem já tem experiência e quer voltar com estratégia
- **Foco:** Operações guiadas ao vivo, gestão de risco
- **Pilares:** 4 (gestão, técnica, mentoria, emocional)
- **Gatilho `funil_origem`:** `alavancagem` ou `compartilhamento_receita`

---

## 🏢 Love Tradding (financeira oficial)

- **Link cadastro:** https://lovetradding.com/account/signup
- **Depósito mínimo:** R$ 100
- **Saque:** liberado em até 24h após solicitação
- **Suporte:** em português, em horário comercial

⚠️ **Importante:** a IA está programada para NUNCA mencionar percentuais de comissão ou "taxas baixíssimas".

---

## 🔌 Endpoints da API

### POST /chat
Endpoint principal — recebe mensagem e retorna resposta da IA.

**Request body:**
```json
{
  "mensagem": "Vim pelo Método Recuperação",
  "cliente_id": "+5521999999999",
  "nome_cliente": "João Silva",
  "funil_origem": "recuperacao_banca"
}
```

**Response:**
```json
{
  "resposta_1": "Show, fico feliz que veio direto.",
  "resposta_2": "Há quanto tempo você opera?",
  "resposta": "Show... Há quanto tempo você opera?",
  "transferir_humano": false,
  "tem_segunda_parte": true,
  "audio_enviar": "",
  "tem_audio": false
}
```

### POST /resetar
Reseta memória de um cliente específico.

```bash
curl -X POST https://botconversa-api-2uty.onrender.com/resetar \
  -H "Content-Type: application/json" \
  -d '{"cliente_id":"+5521999999999"}'
```

### GET /
Health check — retorna status e versão.

---

## ⚙️ Configurações Críticas

| Parâmetro | Valor |
|---|---|
| Modelo | claude-haiku-4-5-20251001 |
| Temperature | 0.8 |
| Max tokens | 600 |
| Histórico | 10 mensagens |
| Expiração memória | 30 minutos |
| Rate limit local | 30 msg/hora/cliente |
| Retry tentativas | 3 (backoff 2s/4s/8s) |

---

## 🔑 Variáveis de Ambiente (Render)

| Variável | Status |
|---|---|
| ANTHROPIC_API_KEY | ✅ ATIVA |
| GROQ_API_KEY | Backup |
| CEREBRAS_API_KEY | Backup |
| GEMINI_API_KEY | Backup |
| OPENROUTER_API_KEY | Backup |

---

## 🛠️ Como Subir Nova Versão

1. Editar index.js no GitHub
2. Commit changes
3. Render detecta automaticamente e faz deploy (~2-3 min)
4. Confirmar nova versão acessando a URL raiz

## 🧹 Como Resetar Memória de Todos

1. Render Dashboard → botconversa-api
2. **Manual Deploy** → **Clear build cache & deploy**
3. Aguardar redeploy (~3 min)
4. Memória em RAM zera automaticamente

---

## 📋 Campos Personalizados no BotConversa

| Campo | Tipo | Função |
|---|---|---|
| mensagem_atual | Texto | Última mensagem do cliente |
| ia_resposta_1 | Texto | 1ª parte da resposta da IA |
| ia_resposta_2 | Texto | 2ª parte da resposta da IA |
| ia_transferir | Texto | Flag de transferência (true/false) |
| ia_resposta | Texto | Resposta completa (compatibilidade) |
| funil_origem | Texto | Identifica funil de origem |
| audio_enviar | Texto | Identifica áudio a ser enviado |

## 🔗 Corpo do Webhook (Integração)

```json
{
  "mensagem": "{mensagem_atual}",
  "cliente_id": "{telefone}",
  "nome_cliente": "{primeiro-nome}",
  "funil_origem": "{funil_origem}",
  "audio_enviar": "{audio_enviar}"
}
```

⚠️ As variáveis entre { } precisam ser inseridas pelo botão de variáveis do BotConversa.

---

## 🛡️ Recursos de Segurança

- ✅ Anti-abuse (30 msg/hora por cliente)
- ✅ Retry automático em erros 429/5xx
- ✅ Cache de saudações (economiza tokens)
- ✅ Proteção contra fragmentos curtos
- ✅ Anti-vazamento de instruções internas
- ✅ Anti-repetição de frases

---

## 📈 Histórico de Versões

| Versão | Mudança Principal |
|---|---|
| 1.0 - 2.x | Iterações iniciais (Groq Llama 3.3 70B) |
| 3.0 - 3.5 | Cache, anti-abuse, 2 funis, humanização |
| 4.x | Tentativa Cerebras (descartada) |
| 5.x | Tentativa Gemini (descartada - rate limits) |
| 6.x | Tentativa OpenRouter (descartada - 8 req/min global) |
| 7.0 | Migração para Claude Haiku 4.5 (Anthropic) |
| 7.1 | Mensagens curtas estilo WhatsApp |
| 7.2 | Love Tradding como financeira oficial |
| 7.3 | Sistema de áudios pré-gravados |
| **7.4** | **Áudios desativados; ajustes de prompt (atual)** |

---

## 💰 Custos Estimados (Produção)

| Item | Custo Mensal |
|---|---|
| Render Starter | ~R$ 35 |
| Anthropic Claude Haiku 4.5 | ~R$ 30-80 |
| BotConversa PRO | Já existente |
| **TOTAL ESTIMADO** | **R$ 65-115** |

Estimativa por conversa de qualificação: ~$0.03 (R$ 0.15)

---

## 📦 Como Instalar Localmente

```bash
# Clonar o repositório
git clone https://github.com/mathcardosorj-web/private-academy-ia.git
cd private-academy-ia

# Instalar dependências
npm install

# Criar arquivo .env com as variáveis
echo "ANTHROPIC_API_KEY=sua_chave_aqui" > .env

# Rodar localmente
npm start
```

---

## ❓ Troubleshooting

| Problema | Solução |
|---|---|
| Variáveis chegando como {nome} literal | Usar botão de variáveis no BotConversa, não digitar |
| Erro 429 RateLimitError | Cota do provedor atingida, aguardar reset |
| IA respondendo fora do funil | Verificar bloco "Ação: funil_origem" no fluxo |
| API "dormindo" (spin down) | Render Free dorme após 15min, migrar pra Starter |

---

## 📞 Contato

**Cliente:** Private Academy
**Responsável técnico:** Matheus Cardoso
**Repositório:** github.com/mathcardosorj-web/private-academy-ia
