# Texas Center — Sales Dashboard

Dashboard de metas e indicadores comerciais da Texas Center.

## Stack

- **Frontend**: HTML5, Tailwind CSS (CDN), Chart.js 4.x
- **Fontes**: Google Fonts (DM Sans, Inter, Orbitron, Space Grotesk)
- **Ícones**: Font Awesome 6.5
- **PWA**: Service Worker (network-first) + manifest.json
- **Deploy**: Vercel (auto-deploy via push no `master`)
- **Dados**: Google Sheets (via pubhtml) + API WebSamos (fluxo de pessoas)

## Estrutura

```
index.html          # Aplicação completa (SPA single-file)
manifest.json       # PWA manifest (instalável no celular)
sw.js               # Service Worker (cache network-first)
ERP-INTEGRATION.md  # Guia de integração com Data System ERP
.vercel/            # Configuração Vercel
```

## Funcionalidades

### Seções do Dashboard

| Seção | Descrição |
|-------|-----------|
| Painel Geral | KPIs, meta diária com pulso de ritmo, receita por setor, heatmap de vendas |
| Fluxo da Loja | Fluxo mensal, diário com filtro de data, gráfico dia a dia, comparativo LY |
| Performance | Ranking individual com atingimento, projeção, pódio top 3 gamificado |
| Comissão | Comissionamento semanal por faixa de atingimento |
| Calculadora Frete | Consulta Melhor Envio (sandbox) com máscara de CEP |
| Mundo | Notícias Brasil + Notícias Agro + Cotações (câmbio e CEPEA) |
| Previsão do Tempo | Goiânia, GO via Open-Meteo (4 dias) |

### Recursos Adicionais

| Recurso | Descrição |
|---------|-----------|
| Dark Mode | Tema escuro completo, salvo no localStorage |
| Modo TV | Carrossel fullscreen automático (15s por página) |
| Busca Ctrl+K | Navegação rápida entre seções com teclado |
| Toast Notifications | Alertas de ritmo crítico e vendedores abaixo de 50% |
| Pulso de Ritmo | Card de meta diária pulsa vermelho/amarelo/verde conforme status |
| Pódio Gamificado | Top 3 vendedores com medalhas na Performance |
| Filtro de Data | Consultar fluxo de qualquer dia com comparativo LY automático |
| Print/Export | Layout otimizado para impressão via `@media print` |
| PWA | Instalável no celular com ícone na home screen |
| Easter Egg | 5 cliques rápidos no botão dark mode |

## Fontes de Dados

| # | Fonte | Uso |
|---|-------|-----|
| 1 | **Google Sheets** | Metas, realizado, ranking, comissão (via pubhtml) |
| 2 | **WebSamos API** | Fluxo de pessoas (contador câmera) |
| 3 | **AwesomeAPI** | Cotações de câmbio (USD, EUR, BTC) |
| 4 | **Google News RSS** | Notícias do Brasil + Agro |
| 5 | **Open-Meteo** | Previsão do tempo (Goiânia) |
| 6 | **Data System ERP** | (em preparação) Vendas em tempo real |

### Bug conhecido — API WebSamos 2026

Para datas de 2026, a API exige `data_fim = dia + 1` para retornar dados do dia correto. O dashboard aplica esse ajuste automaticamente via `adjustEndDate()`. Datas de 2025 funcionam normalmente.

### Comparativo LY — Fluxo

O comparativo de fluxo usa o **mesmo dia da semana** do ano anterior (segunda com segunda, terça com terça). Se o dia correspondente não tiver dados, tenta a semana anterior como fallback.

## Integração ERP (Data System)

Ver [ERP-INTEGRATION.md](./ERP-INTEGRATION.md) para o guia completo.

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

| Dados | Intervalo | Método |
|-------|-----------|--------|
| Fluxo de pessoas | 5 minutos | Automático |
| ERP (quando habilitado) | 5 minutos | Automático |
| Timestamp top bar | 1 minuto | Automático |
| Demais dados | Manual | Botão Sincronizar (com debounce) |

## Desenvolvimento

1. Clone o repositório
2. Abra `index.html` diretamente no navegador ou use um server local
3. Para deploy, faça push na branch `master` — Vercel detecta automaticamente

### Observações técnicas

- **Service Worker**: Estratégia network-first — sempre busca versão mais recente, cache serve como fallback offline
- **Debounce**: Botão Sincronizar protegido contra cliques duplos, com feedback visual (ícone girando)
- **CORS fallback**: Se chamada direta falhar, usa `allorigins.win` como proxy (Google Sheets e WebSamos)
- **Dark mode**: Preferência salva em `localStorage` (`tc_dark_mode`)
