# Plan visual — Caso Power BI Studio

## Objetivo

Power BI Studio debe demostrar una capacidad distinta a PULSE y CMMS 2.0: convertir conocimiento disperso sobre Power BI en un sistema común, reutilizable y gobernado.

No queremos presentar otro dashboard. Queremos mostrar cómo se pasa de tomar decisiones aisladas en cada informe a construir una base compartida para diseño, datos, indicadores, componentes y calidad.

## Qué está realmente demostrado en el repositorio

El caso puede apoyarse en activos ya definidos o implementados:

- catálogo de arquetipos de dashboard;
- sistema de plantillas;
- estándar de diseño premium;
- contrato de design tokens;
- estándar DAX;
- estándar de modelo semántico;
- contrato semántico de KPI;
- controles de calidad;
- arquitectura modular del repositorio;
- catálogo de custom visuals;
- primer visual de referencia: KpiCardPro;
- validación automática y releases controladas.

KpiCardPro está implementado y empaquetado automáticamente, pero su validación dentro de Power BI Desktop sigue pendiente. El portfolio debe reflejar exactamente ese estado.

## Historia visual

La pieza principal mostrará esta transformación:

Decisiones aisladas → estándares → componentes reutilizables → validación → activos versionados

## Ejemplo ficticio

Para explicar el sistema utilizaremos un informe completamente ficticio:

**Northstar Retail — Executive Performance**

Todos sus datos, nombres, métricas y cifras serán inventados exclusivamente para el portfolio.

El ejemplo no se presentará como un proyecto real ni como evidencia de resultados empresariales.

## Pieza principal

La composición se dividirá en dos niveles.

### Nivel 1 — El problema

Varios dashboards hipotéticos muestran pequeñas inconsistencias:

- KPI con formatos diferentes;
- colores y espaciados distintos;
- definiciones de indicadores no compartidas;
- decisiones repetidas;
- componentes recreados varias veces.

No hace falta representar un “mal dashboard”. El problema es la falta de sistema.

### Nivel 2 — La respuesta de PBI Studio

Cinco bloques conectados:

1. **Foundations** — reglas de diseño y tokens.
2. **Semantic** — contratos de KPI, DAX y modelo semántico.
3. **Archetypes & Templates** — patrones para empezar desde una estructura adecuada.
4. **Visuals** — componentes reutilizables como KpiCardPro.
5. **Quality & Release** — validar antes de convertir algo en referencia estable.

## KpiCardPro

La pieza mostrará un ejemplo visual simplificado de KpiCardPro con datos ficticios:

- Actual;
- Target;
- Previous;
- Variance;
- Status;
- Trend;
- Context label.

Debe indicarse que el componente está implementado y validado en build, mientras que la validación final en Power BI Desktop sigue pendiente.

## Qué debe entender un recruiter

Power BI Studio debe demostrar:

- pensamiento de sistema;
- capacidad para estandarizar;
- diseño de componentes reutilizables;
- gobierno de analítica;
- modelado y semántica;
- orientación a calidad;
- documentación y versionado;
- capacidad para transformar aprendizaje en activos que otros puedan reutilizar.

## Privacidad

Todos los ejemplos visibles utilizarán exclusivamente datos sintéticos o públicos. Para la V1 se usarán solo datos sintéticos creados para el portfolio.

## Estado

**Aprobado como dirección visual del caso.**
