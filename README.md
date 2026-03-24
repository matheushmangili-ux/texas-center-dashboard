# Texas Center — Sales Dashboard

Dashboard de metas e indicadores comerciais da Texas Center.

## Stack

- **Frontend**: HTML5, Tailwind CSS (CDN), Chart.js 4.x
- **Fontes**: Google Fonts (DM Sans, Inter, Orbitron, Space Grotesk)
- **Ícones**: Font Awesome 6.5
- **Deploy**: Vercel (auto-deploy via push no `master`)
- **Dados**: Google Sheets (via pubhtml) + API WebSamos (fluxo de pessoas)

## Estrutura

```
index.html          # Aplicação completa (SPA single-file)
.vercel/            # Configuração Vercel
```

## Seções do Dashboard

| Seção | Descrição |
|-------|-----------|
| Painel Geral | KPIs, meta diária com pulso, receita por setor, heatmap |
| Fluxo da Loja | Fluxo mensal, último dia com dados, gráfico dia a dia |
| Performance | Ranking individual com atingimento e projeção |
| Comissão | Comissionamento semanal por faixa |
| Calculadora Frete | Consulta Melhor Envio (sandbox) |
| Notícias | Google News Brasil |
| Cotações | Câmbio (USD, EUR, BTC) + Agro |
| Previsão do Tempo | Goiânia via wttr.in |

## Fontes de Dados

1. **Google Sheets** — Metas, realizado, ranking, comissão
2. **WebSamos API** — Fluxo de pessoas (contador)
3. **AwesomeAPI** — Cotações de câmbio
4. **Google News RSS** — Notícias
5. **wttr.in** — Previsão do tempo
6. **Data System ERP** — (em preparação) Vendas em tempo real

## Integração ERP (Data System)

Ver [ERP-INTEGRATION.md](./ERP-INTEGRATION.md) para o guia completo de integração.

### Resumo rápido

No `index.html`, configure:

```javascript
ERP_CONFIG.baseUrl = 'https://api.datasystem.com.br/v1';
ERP_CONFIG.token = 'seu-token-aqui';
ERP_CONFIG.storeId = 'id-da-loja';
ERP_CONFIG.enabled = true;
```

O dashboard consumirá automaticamente os endpoints mapeados em `ERP_ENDPOINTS`.

## Auto-refresh

- Fluxo de pessoas: a cada 5 minutos
- ERP (quando habilitado): a cada 5 minutos
- Demais dados: manual via botão Sincronizar

## Desenvolvimento

1. Clone o repositório
2. Abra `index.html` diretamente no navegador ou use um server local
3. Para deploy, faça push na branch `master`
