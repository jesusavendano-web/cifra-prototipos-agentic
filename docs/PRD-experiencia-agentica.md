# PRD · Experiencia agéntica de Cifra

**Task:** ENG-2418 · **Autor:** Jesús Avendaño · **Fecha:** 26 oct 2026 · **Estado:** borrador para feedback del equipo
**Prototipos:** repo `cifra-prototipos-agentic`, branch `eng-2418-experiencia-agentica` (el repo original no se tocó)

---

## 1. Contexto: por qué ahora

En el weekly del 26/10 acordamos que Cifra debe pasar a ser una app "casi completamente agéntica". Esto fue lo que dijo Anas:

- Los reportes **van perdiendo relevancia**. El valor está en que la IA interprete los números y responda preguntas.
- En vez de construir reportes, el usuario define un prompt y Cifra se lo **envía a su inbox cada semana**.
- **Alertas en lenguaje natural**: *"alértame cuando las ventas bajen más de lo normal"*, y Cifra entiende qué es "normal".
- Hay que **ver mejor el profit por orden** y que la IA lo pueda representar.
- **No competir con lo que Shopify ya hace**. La diferencia está en los costos, el profit neto y más fuentes de datos (integraciones).
- El listing y la landing pasan de "Analytics & Reports" a **"AI & Analytics"**.

## 2. Qué hace hoy la app (prueba en `cifra-analytics-dev`, 26/10)

| Pregunta | Resultado | Problema |
|---|---|---|
| "Most profitable product in the last 90 days, including product costs? And its total costs?" | Tabla con **"Default Title"**: gross profit $3,020, COGS $1,712 | Muestra el nombre de la variante en vez del producto. El profit es **gross** (solo COGS); no considera envío, fees ni ads. Respondió solo la parte de COGS. |
| "Show me profit per order for my last 10 orders, with a chart" | *"I can't show profit per order… with the available dimensions"* | **No existe la dimensión orden** en el agente. Es justo el pedido de Anas. |
| "¿Puedes crear alertas o resúmenes por email?" | Solo resúmenes de un **reporte guardado** | **No hay alertas.** Los schedules siguen atados a reportes. |
| Insights | "Nothing needs your attention right now" | Insights es pasivo y no tiene cómo avisar al usuario. |

La conversación de prueba es `db6dfca0-2442-41e2-a6dd-db2621add6cd`. Sirve para que Julio Sansossio revise el caso de costos.

## 3. Objetivos y no-objetivos

**Objetivos**
1. Cualquier pregunta de profit se responde con **profit neto**, explicando qué costos incluye y cuáles faltan.
2. Cifra trabaja **sin que el usuario abra la app**: digests programados y alertas en lenguaje natural.
3. **Profit por orden** disponible para el agente y en una vista propia.
4. Una sola bandeja (Inbox) con todo lo que el agente entrega.

**No-objetivos (esta fase)**
- Cambios de pricing y planes (van en otra task).
- El bug de emails repetidos y el error 429 (lo ve Julio Flores). Este PRD solo pide deduplicar las entregas (sección 9).
- Metafields y la app adyacente para adquisición.
- Rediseñar Dashboards y Settings.

## 4. Diferenciación frente a Sidekick de Shopify

Sidekick ya responde preguntas sobre datos de Shopify, así que Cifra no puede ser solo "un chat sobre tus ventas".

| | Sidekick | Cifra |
|---|---|---|
| Datos | Solo Shopify | Shopify + **costos** (COGS, envío, gateways, custom) + Meta, Klaviyo, **TikTok, Omnisend** |
| Métrica central | Ventas | **Profit neto** por producto, orden, campaña y cliente |
| Modo | Reactivo (le preguntas) | **Proactivo**: digests, alertas, briefing diario |
| Entrega | Dentro del admin | Inbox de Cifra, email, Slack |

## 5. Capacidades

### 5.1 Ask: chat agéntico (`chat.html`)
- **Pasos visibles** ("4 steps completed", desplegable): rango de fechas, fuentes y cálculos. Es el mismo patrón de la app actual, ahora con detalle.
- **Profit neto por defecto**: net sales − COGS − envío − fees de pago − ad spend atribuido. Cada respuesta dice qué costos usó.
- **Costos faltantes**: si un producto no tiene COGS, el agente lo dice (`s-banner` warning) y ofrece cargarlo. Nunca trata $0 como un costo real sin avisar.
- **Contexto entre turnos**: "What were its total costs?" usa el producto de la pregunta anterior. Hoy la IA se traba justo ahí.
- **Visualización generada por el agente**: tabla, donut de costos, barras de profit por orden con negativos.
- **Acciones sobre la respuesta**: *Send this every Monday* (abre un `s-modal` prellenado), *Alert me on changes*, *Pin to dashboard*.

### 5.2 Digests programados (`automation-new.html`)
- El usuario escribe en sus palabras qué quiere recibir. El agente lo traduce a "What Cifra understood" (qué incluye, frecuencia, hora) y muestra una **vista previa del email**.
- Cada envío vuelve a correr la pregunta con datos frescos. No es un PDF fijo.
- Canales: Inbox, email, Slack. Respeta horario de silencio.
- Reemplaza `schedule.html`. Los schedules de reportes existentes se migran a digests.

### 5.3 Alertas en lenguaje natural (`automation-new.html#alert`)
- Entrada: *"Alert me when sales drop more than normal"*.
- El agente propone una **interpretación explícita**: métrica (net sales por hora), qué es "normal" (rango por día de semana y hora, últimas 8 semanas), cuándo dispara (2 horas seguidas por debajo del rango) y qué ignora (horas con menos de 3 órdenes esperadas).
- Se ve una gráfica de "últimos 7 días vs. normal" y un **backtest**: "habría disparado 2 veces en 30 días".
- Sensibilidad Low/Medium/High expresada como frecuencia esperada, no como sigma.
- **Nada se activa sin confirmación** del usuario.
- La alerta llega con diagnóstico ("checkout conversion bajó de 3.1% a 1.4%") y CTA "Ask why".

### 5.4 Profit por orden (`orders-profit.html`)
- KPIs: profit neto, profit promedio por orden y órdenes con pérdida.
- Barras por orden: positivas en azul, negativas en rojo.
- Tabla por orden: net sales, COGS, envío, fees, ad spend y profit, con un `s-badge` de margen.
- Aviso de atribución: Meta last-click 7d; TikTok entra al conectarlo.

### 5.5 Inbox (`inbox.html`)
- Reúne digests entregados, alertas disparadas e insights que encontró el agente. **Absorbe Insights.**
- Cada entrada tiene "Continue in chat" con el contexto ya cargado.

### 5.6 Home agent-first (`home.html`)
- Mantiene el AskBar actual y suma **prompts sugeridos** debajo.
- **Today's briefing**, escrito por el agente: profit de ayer y 3 cosas que mirar con `s-badge` por tipo.
- **Running for you**: digests y alertas activas, más "New automation".
- **Help Cifra answer better**: las acciones que mejoran las respuestas (costos faltantes, conectar TikTok, conectar Omnisend).

## 6. Principios del agente
1. **Mostrar el cálculo**: qué datos, qué rango, qué costos.
2. **Decir qué falta** en vez de inventar o asumir $0 en silencio.
3. **Confirmar antes de actuar**: digests y alertas pasan por "What Cifra understood".
4. **Una respuesta siempre puede volverse trabajo recurrente** (digest, alerta o widget).
5. **Nombrar las cosas como el usuario**: producto y no variante, "#1047" y no un ID interno.

## 7. Arquitectura de información

**Antes:** Home · Dashboards · Insights · Reports · Schedules · Integrations · Settings
**Después:** **Home** (Ask) · **Inbox** · **Automations** · **Profit per order** · Dashboards · Integrations · Settings

- **Se retira Reports** (`reports.html`, `report.html`, `report-new.html`). Los reportes guardados se migran a digests o a respuestas fijadas, y las plantillas pasan a ser prompts sugeridos.
- **Se retira Schedules** → Automations.
- **Se retira Insights** → Inbox.

## 8. Mapeo a Polaris Web Components (App Home)

| Pantalla | Nativo | Custom (`data-custom`) y por qué |
|---|---|---|
| Home | `s-section`, `s-badge`, `s-clickable-chip`, `s-button`, `s-link`, `s-grid`, `s-heading`, `s-divider` | AskBar y strip de métricas (ya existían) |
| Chat | `s-banner`, `s-modal`, `s-select`, `s-text-area`, `s-choice-list`, `s-clickable-chip`, `s-unordered-list`, `s-spinner`, `s-popover` | **AgentSteps** (no hay un disclosure nativo con este estilo), burbuja, composer, gráficas SVG (no existe `s-chart`) |
| Automations | `s-table`, `s-badge`, `s-switch`, `s-banner`, `s-empty-state`, `s-page` slots | Tabs (no existe `s-tabs`) |
| New automation | `s-text-area`, `s-select`, `s-choice-list`, `s-switch`, `s-banner`, `s-grid` | Segmented Digest/Alert (no existe `s-segmented-control`), MailPreview, gráfica de rango normal |
| Inbox | `s-section`, `s-badge`, `s-button`, `s-empty-state` | Tabs, punto de no leído |
| Profit per order | `s-table`, `s-badge`, `s-select`, `s-banner`, `s-grid` | KPI cards, BarChart con negativos |

**Gotcha encontrado:** en las pantallas nuevas, `s-select value="…"` no preseleccionó la opción y quedó la primera, aunque en `settings.html` sí funciona. Lo seguro es marcar `selected` en el `s-option`, como hace onboarding.

## 9. Requisitos de datos y backend (para estimar con dev)
1. **Dimensión "order"** en la capa que consulta el agente, con costos por orden: COGS de líneas, envío, fee del gateway, descuentos y ad spend atribuido.
2. **Asignación de costos** a producto y a orden (envío y fees prorrateados; ads por atribución).
3. **Cobertura de costos**: endpoint que diga qué productos no tienen COGS, para el banner.
4. **Nombre de producto** en vez de "Default Title" (usar `product.title` cuando la variante es la default).
5. **Baseline de anomalías** por métrica, día de semana y hora (8 semanas), con backtest.
6. **Scheduler y delivery** de digests y alertas: email, Slack e inbox, con **deduplicación y rate limit por usuario**. Se relaciona con el incidente de emails repetidos y el 429.
7. **Integraciones** TikTok Ads y Omnisend como fuentes del agente (prioridad de esta semana).

## 10. Referencias (Mobbin, 26/10)

| Patrón | Referencia | Qué tomamos |
|---|---|---|
| Home ask-first + inbox | Plane (Plane AI) | Saludo + "Ask Plane AI" + accesos rápidos + recientes. Inbox separado para lo que entrega el sistema |
| Tarea recurrente con IA | Plane (recurring work items) | Prompt en lenguaje natural ("Weekly Product Performance Review") + frecuencia |
| Crear alerta con anomaly detection | Snowflake | Plantilla "anomaly detection", métrica, alcance, canal de notificación |
| Programar tarea | Snowflake (schedule notebook task) | Frecuencia, hora y zona horaria explícitas |
| Alerta por email simple | Gumloop | Modal mínimo: email + "notify only on failure" |
| Pasos del agente / panel lateral | Base44 | Lista de acciones del agente ("Edited…", "Thinking…") y prompts sugeridos |
| Chat Ask/Create | Voicenotes | "Thinking…" y modos Ask/Create en el mismo composer |
| Onboarding de un agente | Quo (Sona) | "Ya está configurado para ayudarte" y opt-in claro |

## 11. Posicionamiento: listing de la App Store y landing

> Propuesta para revisar con Anas, que tiene asignado el listing. Hay que verificar los límites de caracteres de Shopify antes de publicar.

- **Nombre:** Cifra: AI & Profit Analytics *(sin "Reports")*
- **Subtítulo:** Ask your profit anything. Get alerts and weekly digests.
- **Intro:** Your AI profit analyst: true net profit per product and order, with alerts before it's too late.
- **Features:**
  - Ask in plain words and get net profit with every cost included
  - Weekly digests in your inbox, email or Slack, written by AI
  - Alerts in your words: "tell me when sales drop more than normal"
  - Profit per order: see exactly which orders lose money and why
  - Shopify, Meta, Klaviyo, TikTok and Omnisend in one profit view
- **Capturas:** Home con briefing, Chat con costos, Alerta "What Cifra understood", Profit per order, Inbox.
- **Landing hero:** mismo mensaje. *"Cifra is AI first: ask, get alerted, get your weekly profit in your inbox."*
- **Planes:** pendiente. La transcripción no deja claros los límites de Starter y Advanced.

## 12. Métricas de éxito
- % de usuarios activos con al menos 1 automation activa (meta inicial: 30% a 60 días).
- Preguntas por usuario por semana y tasa de respuesta sin "can't answer".
- % de respuestas de profit con cobertura de costos completa.
- Clicks en "Continue in chat" desde el Inbox (engagement proactivo).
- Retención a 30 días de usuarios con digest frente a sin digest.

## 13. Fases de implementación
1. **F1 · Fundamentos de datos (2–3 sem):** dimensión orden, profit neto por defecto, nombre de producto, cobertura de costos, TikTok + Omnisend.
2. **F2 · Chat agéntico (1–2 sem):** pasos, banner de costos, acciones sobre respuestas, gráficas generadas.
3. **F3 · Digests + Inbox (2 sem):** migrar Schedules, entrega deduplicada, Inbox absorbe Insights.
4. **F4 · Alertas en lenguaje natural (2–3 sem):** baseline, backtest, sensibilidad, diagnóstico.
5. **F5 · Retiro de Reports + Home + listing (1 sem):** migración de reportes guardados, nueva navegación, listing y landing.

## 14. Preguntas abiertas
1. ¿Qué modelo de atribución de ad spend por orden usamos por defecto (last-click 7d)? ¿Será configurable?
2. ¿Los digests y alertas cuentan para el límite del plan o consumen tokens aparte? Julio Sansossio mencionó controlar el consumo.
3. ¿Qué hacemos con los usuarios que tienen reportes guardados? ¿Migración automática o aviso?
4. ¿Slack entra en F3 o después?
5. ¿El Inbox necesita notificación en el admin de Shopify o basta con email?
