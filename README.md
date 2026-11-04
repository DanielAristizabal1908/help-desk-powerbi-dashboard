# 📊 IT Help Desk Analytics: Monitoreo de MTTR y SLA

## 📝 Resumen del Proyecto

Este proyecto simula el entorno de datos de un departamento de soporte técnico (Help Desk). El objetivo principal es proporcionar a los líderes operativos un panel de control interactivo para monitorear el rendimiento de los agentes, identificar cuellos de botella en la resolución de problemas y medir el cumplimiento de los Acuerdos de Nivel de Servicio (SLA).

El dashboard reveló de forma crítica que el equipo actual de soporte (Ana, Luis y Carlos) opera con un cumplimiento de SLA de entre 50% y 74%, muy por debajo del objetivo corporativo del 90%, lo que sugiere la necesidad de una auditoría de procesos en la asignación de tickets críticos.

## 🛠️ Stack Tecnológico

* **Base de Datos:** MySQL (Creación de esquema y generación de registros).
* **ETL & Limpieza:** Power Query.
* **Modelado y DAX:** Power BI.
* **Visualización:** Power BI (Diseño en Modo Oscuro UI/UX corporativo).

## ⚙️ Fases del Desarrollo

### 1. Extracción de Datos (MySQL)

Se diseñó una base de datos relacional simulando un sistema de ticketing. Se generaron **515 registros** históricos de soporte técnico, capturando métricas clave como categorías de fallas, agentes asignados, fechas de apertura/cierre y el cumplimiento de los tiempos de resolución esperados.

### 2. Transformación y Limpieza (Power Query)

Al conectar Power BI con MySQL vía DirectQuery/Importación, se detectaron inconsistencias en los tipos de datos lógicos.

* **Problema:** Los campos de cumplimiento (`sla_cumplido`, `fcr_cumplido`) fueron interpretados como texto booleano (Verdadero/Falso), impidiendo cálculos matemáticos en DAX.
* **Solución:** Mediante Power Query y la Vista de Tabla, se forzó la conversión de estas columnas a **Número Entero** (1 para cumplimiento, 0 para incumplimiento), habilitando la capacidad de promediar las tasas de éxito.

### 3. Modelado de Datos y Funciones DAX

Se crearon medidas dinámicas para calcular los KPIs operativos centrales del negocio, independientes del filtrado visual:

* **% SLA Cumplido:**
  ```dax
  % SLA Cumplido = AVERAGE('help_desk_analytics tickets'[sla_cumplido])
  ```
  *(Calcula la tasa de éxito de los tickets resueltos a tiempo basándose en el indicador booleano transformado).*

* **MTTR (Mean Time to Resolve):**
  ```dax
  MTTR(Horas) = AVERAGE('help_desk_analytics tickets'[Horas_Resolucion])
  ```
  *(Promedia el tiempo de resolución en horas para medir la eficiencia del equipo).*

### 4. Visualización de Datos (Power BI)

Se implementó un diseño analítico con enfoque corporativo (Dark Mode), optimizando la relación señal-ruido mediante la eliminación de fondos blancos y bordes innecesarios.

**Características Visuales Clave:**

* **KPI Cards:** Tarjetas de diseño transparente para MTTR general y % SLA.
* **Análisis Categórico:** Gráfico de anillo mostrando el volumen de los 515 tickets distribuidos por tipo de falla (Hardware, Software, Redes, etc.).
* **Formato Condicional Inteligente:** Gráfico de columnas de SLA por agente con una línea de meta constante al 90% (0.9). Las barras cambian dinámicamente a **Rojo** si el rendimiento cae por debajo de la meta, o a **Azul** si la superan, permitiendo una identificación inmediata de problemas de rendimiento.
* **Interactividad:** Segmentador de datos (Slicer) en estilo mosaico para filtrar todo el modelo por Nivel de Prioridad (Alta, Media, Baja, Crítica).

## 💡 Conclusiones y Hallazgos (Insights)

1. **Déficit de SLA:** Ninguno de los agentes alcanza la meta operativa del 90%. El rendimiento máximo registrado ronda el 74%.
2. **Oportunidad de Mejora:** Al segmentar por prioridad "Crítica", las métricas se recalculan evidenciando áreas específicas donde el flujo de trabajo se estanca, lo que requeriría una revisión en la categorización de los tickets o en la carga de trabajo asignada a cada analista de soporte.
