# Guia de Integração — Data System ERP

## Visão Geral

O dashboard está preparado para consumir dados do ERP Data System via API REST.
A integração alimentará indicadores em tempo real como **Vendido Hoje**, **Ticket Médio**,
**Ranking de Vendedores** e outros.

## Configuração

### 1. Variáveis de Configuração

No `index.html`, localize o bloco `ERP_CONFIG` e preencha:

```javascript
const ERP_CONFIG = {
  baseUrl: 'https://api.datasystem.com.br/v1',  // URL base da API
  token: 'Bearer <seu-token>',                    // Token de autenticação
  storeId: 'LOJA_01',                             // ID da loja no ERP
  refreshInterval: 5 * 60 * 1000,                 // Intervalo de sync (ms)
  enabled: true                                    // Ativar integração
};
```

### 2. Endpoints Esperados

| Endpoint | Método | Descrição | Resposta Esperada |
|----------|--------|-----------|-------------------|
| `/vendas/dia` | GET | Vendas do dia atual | `{ total: number, vendedores: [{nome, valor}] }` |
| `/vendas/mes` | GET | Vendas do mês | `{ total: number }` |
| `/estoque/resumo` | GET | Resumo de estoque | `{ itens: number, valor: number }` |
| `/clientes/dia` | GET | Clientes atendidos hoje | `{ total: number }` |
| `/indicadores/ticket` | GET | Ticket médio | `{ valor: number }` |
| `/metas/diaria` | GET | Meta diária do ERP | `{ valor: number }` |
| `/vendedores/ranking` | GET | Ranking de vendedores | `[{nome, meta, realizado, pct}]` |
| `/produtos/top` | GET | Top produtos do dia | `[{nome, qtd, valor}]` |
| `/setores/vendas` | GET | Vendas por setor | `[{setor, valor}]` |

### 3. Parâmetros de Query

Todos os endpoints aceitam:

- `data` — Data no formato `YYYY-MM-DD` (padrão: hoje)
- `loja` — ID da loja (padrão: `ERP_CONFIG.storeId`)

### 4. Headers Enviados

```
Authorization: Bearer <token>
Content-Type: application/json
X-Store-Id: <storeId>
```

## Objeto de Dados

Após o sync, `window.ERP_DATA` será populado:

```javascript
window.ERP_DATA = {
  vendidoHoje: 15420.50,     // Total vendido hoje (alimenta card Meta Diária)
  vendidoMes: 245000.00,     // Total vendido no mês
  ticketMedio: 385.50,       // Ticket médio do dia
  clientesDia: 40,           // Clientes atendidos
  topProdutos: [...],        // Top produtos
  vendedoresDia: [...],      // Ranking do dia
  estoque: {...},            // Resumo estoque
  lastSync: '2026-03-24T...' // Timestamp do último sync
};
```

## Fluxo de Sync

1. Na inicialização, se `ERP_CONFIG.enabled === true`, `erpSync()` é chamada
2. A cada `refreshInterval` ms, `erpSync()` roda novamente
3. Após cada sync, `render()` é chamada para atualizar a UI
4. O botão **Sincronizar** no sidebar também dispara `erpSync()`

## Campos Impactados pela Integração

| Campo no Dashboard | Fonte Atual | Fonte após ERP |
|---------------------|-------------|----------------|
| Vendido Hoje | Zerado | `ERP_DATA.vendidoHoje` |
| % da Meta Diária | Zerado | Calculado automaticamente |

## Testes

Para testar sem o ERP real, popule `window.ERP_DATA` manualmente no console:

```javascript
window.ERP_DATA.vendidoHoje = 18500;
render(); // Atualiza o dashboard
```

## Próximos Passos (Pós-Integração)

- [ ] Adicionar gráfico de vendas por hora (dado do ERP)
- [ ] Card de estoque em tempo real
- [ ] Alertas de ruptura de estoque
- [ ] Ranking de vendedores em tempo real (não apenas diário)
- [ ] Comparativo vendido hoje vs meta com gauge
- [ ] Notificações push para metas atingidas

## Segurança

- O token da API deve ser mantido em variável de ambiente no Vercel (não no código)
- Para produção, migrar para um backend (Node.js/Next.js) que faça o proxy das chamadas
- Nunca expor credenciais do ERP no frontend em produção

## Contato

Para dúvidas sobre a API do Data System, consultar o suporte do ERP ou a documentação oficial.
