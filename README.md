# Cifra – Prototipos · Experiencia agéntica (ENG-2418)

Duplicado de [cifra-prototipos](https://github.com/jesusavendano-web/cifra-prototipos) para explorar la app "AI-first".
El repo original no se toca. Todo usa Polaris Web Components (App Home); lo custom va marcado con `data-custom`
(botón "Resaltar custom" en la barra de prototipos).

Local: `node serve.js` → http://localhost:4321 · PRD: [`docs/PRD-experiencia-agentica.md`](docs/PRD-experiencia-agentica.md)

## Qué cambió

| Pantalla | Cambio |
|---|---|
| `home.html` | AskBar + prompts sugeridos, briefing del agente (reemplaza Insights), "Running for you", acciones para responder mejor |
| `chat.html` | Pasos del agente, profit neto con todos los costos, banner de costos faltantes, costos totales (donut), profit por orden (barras), acciones: digest / alerta / pin |
| `orders-profit.html` | **Nueva.** Profit por orden con desglose de costos |
| `automations.html` | **Nueva.** Digests + alertas (reemplaza Reports y Schedules) |
| `automation-new.html` | **Nueva.** Lenguaje natural → "What Cifra understood" → preview (`#alert`) |
| `inbox.html` | **Nueva.** Digests entregados, alertas disparadas e insights (absorbe Insights) |
| `integrations.html` | TikTok Ads y Omnisend disponibles para conectar |
| Retiradas | `reports.html`, `report.html`, `report-new.html`, `schedule.html`, `insights.html` |
