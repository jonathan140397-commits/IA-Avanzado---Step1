# IA Avanzado - Proyecto Integrador (Step 1)

Checkpoint 1: **Agente base y motor de razonamiento** en n8n.

Este repositorio contiene el flujo `checkpoint1_jona_apellido.json`, la primera versión del proyecto integrador. El mismo flujo se va a ampliar módulo a módulo durante el curso.

## Qué hace el agente

Asistente de Triaje de Solicitudes CAPEX. Recibe pedidos de inversión por chat (equipos, obras, repuestos mayores, servicios de ingeniería), extrae los datos clave, asigna una prioridad (Alta / Media / Baja) y registra la solicitud en una planilla de Google Sheets. Al terminar, envía un mail de control con la consulta, la respuesta y el Execution Log.

## Arquitectura del flujo

```
[Chat Trigger] → [AI Agent (Tools Agent)] → [Gmail: Log de Observabilidad]
                    ├─ Chat Model: Google Gemini 2.5 Flash
                    └─ Tool: registrar_solicitud_capex (Google Sheets, Append)
```

| Componente | Detalle |
|---|---|
| Disparador | Chat Trigger |
| Agente | AI Agent en modo Tools Agent, máximo 8 iteraciones (guardrail contra bucles) |
| Modelo | Google Gemini 2.5 Flash, temperatura 0.2 |
| System Prompt | Modular: Rol, Ámbito, Objetivo, Reglas y Escalamiento, Restricciones, Estilo |
| Herramienta | `registrar_solicitud_capex`, conectada de forma lateral al agente, con descripción extensa de cuándo activarla y cuándo no |
| Observabilidad | Gmail con consulta, respuesta final y pasos intermedios (`intermediateSteps`) |

## Planilla de Google Sheets requerida

Hoja llamada `Solicitudes_CAPEX`, con estos encabezados en la fila 1:

`Fecha | Solicitante | Descripcion | Monto_Estimado | Categoria | Prioridad | Resumen`

## Cómo importarlo

1. En n8n: menú de los tres puntos → **Import from file** → seleccionar el `.json`.
2. Conectar las credenciales: Google Gemini (API key gratuita desde Google AI Studio), Google Sheets (OAuth2) y Gmail (OAuth2).
3. En el nodo `registrar_solicitud_capex`, elegir la planilla y la hoja `Solicitudes_CAPEX`.
4. En el nodo de Gmail, configurar el correo destinatario del log.

## Prueba sugerida

Prompt de ejemplo en el chat de n8n:

> Necesito comprar un compresor de aire nuevo para la línea 2, estimo USD 18.000, es urgente porque el actual falla seguido.

Resultado esperado: el agente activa la herramienta, agrega una fila a la planilla, responde con un resumen y llega el mail de observabilidad.

## Roadmap del proyecto integrador

| Módulo | Extensión |
|---|---|
| M1 | Agente base (este checkpoint) |
| M2 | Multi-agente: Manager y Workers como sub-workflows |
| M3 | Memoria por Session_ID |
| M4 | Integraciones (CRM, calendario, workspace vía OAuth2) |
| M5 | RAG con base documental |
| M6 | Voz (STT / TTS) |
| M11 | Proyecto Final Integrador |
