# Templates de Automação — Graventum

Templates n8n parametrizados por padrão técnico. Cada flow do catálogo (`fluxos_automacao.template_key`) aponta para um template aqui.

## Mapa de Templates

| template_key | Arquivo | Flows cobertos | API keys necessárias |
|---|---|---|---|
| `lembrete-whatsapp` | `lembrete-whatsapp.json` | 114, 126, 131, 185, 192 | `google_sheet_id` |
| `cobranca-asaas` | `cobranca-asaas.json` | 130, 141 | `asaas_api_key` |
| `qualificacao-leads-wa` | `qualificacao-leads-wa.json` | 6, 136, 193 | — |
| `followup-sequence` | `followup-sequence.json` | 8, 163, 167 | — |
| `agendamento-online` | *(pendente)* | 113 | `google_calendar_id` |
| `confirmacao-reserva` | *(pendente)* | 7, 162 | `google_sheet_id` |
| `nps-pesquisa` | *(pendente)* | 10 | `google_sheet_id` |
| `comunicacao-status` | *(pendente)* | 148, 176, 184 | — |
| `custom` | — | flows complexos | manual |

## Como funciona o deploy

1. Admin adiciona automação em `/admin/clients/[id]`
2. API route POST `/api/admin/clients/[id]` cria registro em `client_automations` e dispara `DEPLOY_01`
3. **DEPLOY_01** (`DEPLOY_01_workflow.json`):
   - Busca dados do cliente (api_keys, whatsapp) e do flow (template_key)
   - Se `template_key != custom` e todas as api_keys_required estão presentes → deploy automático
   - Carrega o JSON do template (do GitHub), substitui variáveis, importa no n8n via API, ativa
   - Marca automação como `ativo` no Supabase
   - Notifica Robson no Telegram (sucesso ou intervenção manual necessária)

## Variáveis substituídas em cada template

| Variável | Fonte |
|---|---|
| `{{CLIENT_AUTOMATION_ID}}` | `client_automations.id` |
| `{{CLIENT_WA_INSTANCE}}` | `provisioned_clients.evolution_api_key` ou instância padrão Graventum |
| `{{CLIENT_EVOLUTION_API_KEY}}` | `provisioned_clients.evolution_api_key` ou chave Graventum |

## Planilha padrão — Google Sheet

Para templates que usam `google_sheet_id`, o cliente preenche a ID da planilha durante o onboarding. 
O template espera uma aba com as colunas documentadas em cada `_meta.description`.

## Fluxo da planilha de lembretes (`lembrete-whatsapp`)

Aba `Lembretes`, colunas: `nome | telefone | data_hora | tipo | mensagem`

Exemplo:
```
João Silva | 41999990000 | 2026-04-10 09:00 | Consulta | Olá João, lembrete da sua consulta amanhã às 9h.
```
