# Análisis de competidores: experiencia agéntica

**Task:** ENG-2418 · **Fecha:** 26 oct 2026 · **Tienda revisada:** `brunchy-test` (crunchy)

## Qué revisé

| App | Estado | ¿Tiene agente? |
|---|---|---|
| **Lifetimely** (AMP) | Revisada a fondo | **Sí: "Profit Agent"** (marcado *New*, primer item del menú) + servidor MCP |
| **TrueProfit** | Revisada | No. Lo nuevo es profit neto + ad spend por orden |
| Polar Analytics | Cuenta demo | Alertas e insights automáticos; no se ve un agente conversacional |
| Triple Whale | No revisada | Pide login con credenciales |
| Conduit (AI Data Analyst) | No revisada | Sin configurar en la tienda (abre su flujo de setup) |

Otras apps instaladas en la tienda: By the Numbers, Setpilot, Juicy Profit, Bloom, BeProfit, Profit AI, Ablestar Email Reports, Madgicx, Kipify, DataDrew, Fivetran, Airbyte, Matrixify, Omnise AI Builder. Quedan pendientes si queremos ampliar el análisis.

---

## Lifetimely: Profit Agent (la referencia directa)

**1. El agente es la puerta de entrada.** "Profit Agent" es el primer item del nav, por encima de Dashboards. La home dice que el agente sirve para *"make sense of your analytics, spot issues, uncover opportunities, and take action"*.

**2. El agente es freemium.** El plan gratis trae un *"free preview of one insight"*. Pagando se pueden hacer follow-ups y llega un insight nuevo cada día. El agente ya es un argumento de venta del plan.

**3. Prompt library por categorías.** Tiene 8 categorías: Agent, LTV, Behavior, Retention, Profitability, Campaigns, Subscriptions y **Check-ins**.
- Casi todos los prompts terminan en *"And what do you suggest doing?"*, así que la respuesta siempre trae una recomendación y no solo el número.
- Ejemplo de Profitability: *"What is my net margin trend over the last 6 months, and which product type is contributing most to gross profit?"*

**4. Check-ins = digests creados desde el chat.** Es nuestro concepto de digest, pero se crea con lenguaje natural dentro del chat:
- *"Schedule a weekly check-in on my repeat rate, AOV, and refund percentage and flag anything that looks off compared to the prior 4 weeks."*
- *"…track my average units per order and let me know if it drops below my recent baseline."*
- Además hay una tarjeta "Create a scheduled check".

**5. Prompts de integridad de datos (categoría Agent).**
- *"Can you check my data integrity and tell me what is missing or incomplete before I start pulling reports?"*
- *"I want to set up profit tracking. Can you check what is connected, what is missing, and tell me the exact steps…"*

**6. Tarjetas de tareas con tiempo estimado: el agente actúa, no solo responde.**
- Make a Dashboard (5 min)
- See what you can do (primer recorrido)
- **Create an impactful audience for Klaviyo**: revisa la conexión y propone la audiencia que falta.
- **Try Upsells**: crea y optimiza ofertas post-compra en la tienda.
- **Connect Slack**: recibe insights y le preguntas al agente desde Slack.
- **Create a scheduled check**

**7. Packs de prompts por temporada.** Hay un banner que dice *"Build or check your BFCM strategy on real margin with these ready-to-run prompts"*, y estamos a un mes de BFCM.

**8. AI connections: servidor MCP por tienda.** Tiene una URL de conector por tienda y guías para Claude y ChatGPT. La **Tools Library** documenta las herramientas:
- `get-store-info`, `query-store-data` (SQL sobre orders, order_line_items, products, marketing, klaviyo, subscriptions…), `describe-all-tables`, `initialise-query-data`
- P&L statement, `get-metric-time-series`
- `get-products-table` (profitabilidad por producto)
- **`get-orders-table` (profit por orden) y `get-order-details` (desglose completo de una orden)**
- Cohorts (`get-cohorts-table`, `get-cohort-averages`, `get-cohort-breakdown`), repurchase rate y breakdown

**9. No eliminó los reportes.** Profit & loss, Lifetime value, Attribution, Customer behavior y Benchmarks siguen en el nav, debajo del agente. Pusieron el agente arriba en vez de reemplazar los reportes.

## TrueProfit

- **Recién lanzado:** *"Ad Spend and Net Profit for every order"* en el Orders Report, con un popup de novedad y una encuesta de feedback. El ad spend por orden aparece marcado como **"est."** (estimado).
- Columnas del Orders Report: Order ID, Date, Items, Revenue, Shipping Costs, Total COGS, Transaction Fees, Handling Fees, Gross Profit, Gross Profit Margin, Ad Spend (est.) y Net Profit.
- **No tiene agente de IA.** La única "conversación" es el chat de soporte. Su canal proactivo es la **app móvil** ("Get mobile app").
- Muestra **Recommended Apps** con descuentos (cross-promotion). Sirve de referencia para la idea de app adyacente que mencionó Anas.

## Polar Analytics (demo)

- Destaca **"Understanding Alerts & Insights"**: alertas automáticas configurables sobre cualquier métrica.
- El onboarding arranca con preguntas tipo *"What's my true CAC?"* o *"Why is conversion down this week?"*.

---

## Qué replicar en Cifra (priorizado)

| # | Qué | Por qué | Esfuerzo |
|---|---|---|---|
| 1 | **Servidor MCP + Tools Library** ("AI connections") | Es literal lo que pidió Anas: números listos para que los agentes los interpreten. Lifetimely ya lo tiene, y nos pone dentro de Claude y ChatGPT, no solo dentro del admin. | Alto (backend) |
| 2 | **Herramientas `orders-table` / `order-details`** en nuestro agente | Hoy el agente de Cifra no puede responder profit por orden. Lifetimely lo expone como tool y TrueProfit lo acaba de lanzar. | Medio |
| 3 | **Prompt library por categoría** con "¿qué me sugieres hacer?" | Reemplaza nuestras 4 sugerencias fijas por una biblioteca. Las respuestas con recomendación son más valiosas. | Bajo |
| 4 | **Crear digests y alertas desde el chat** (como los check-ins) | Ya está en el prototipo. Sumar el lenguaje "flag vs. prior 4 weeks / recent baseline" y una tarjeta "Create a scheduled check". | Bajo |
| 5 | **Tareas del agente con tiempo estimado** | "Crear audiencia en Klaviyo", "Revisar mi setup de costos", "Conectar Slack". El agente pasa de responder a actuar. | Medio |
| 6 | **Agente de integridad de datos** ("check my setup") | Ya tenemos el banner de costos faltantes. Conviene convertirlo en una tarea explícita del onboarding y del Home. | Bajo |
| 7 | **Pack de prompts BFCM** | Es estacional, el timing es ahora y sirve para marketing (foro, landing). | Bajo |
| 8 | **Etiqueta "est." en ad spend por orden** | TrueProfit la usa para ser honesto con la atribución. Encaja con nuestro principio de mostrar el cálculo. | Muy bajo |
| 9 | **Slack como canal para preguntar**, no solo para recibir | Lifetimely lo ofrece. Va en la fase de canales. | Medio |
| 10 | **Gating freemium del agente** (1 insight gratis, follow-ups pagos) | Para la discusión de planes (Starter / Advanced). | Decisión de negocio |

## Qué no copiar (o revisar)

- Lifetimely **mantuvo los reportes** en el nav. Nuestra propuesta los elimina; es la principal diferencia de posicionamiento y conviene validarla con Anas.
- Las tarjetas de Lifetimely tienen mucho texto morado de baja jerarquía. Con Polaris lo resolvemos con `s-section` + `s-badge` + un CTA claro.

## Diferenciación de Cifra que sigue vigente

- **Profit neto con todos los costos** y transparencia del cálculo: pasos visibles y "qué falta".
- **Alertas con baseline explícito y backtest** ("habría disparado 2 veces en 30 días"). Ni Lifetimely ni TrueProfit muestran esto.
- **Nativo en el admin** con Polaris y un Inbox unificado.
- **Integraciones**: TikTok y Omnisend.
