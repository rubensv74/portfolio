# Auditoría de privacidad del portfolio — 2026-08-09

## Objetivo

Revisar el portfolio completo con una regla más estricta de confidencialidad: no basta con sustituir nombres o cifras. También deben protegerse hitos, códigos, documentos, indicadores, terminología interna y estructuras que puedan permitir reconstruir cómo funciona un entorno profesional real.

## Alcance revisado

Se ha revisado:

- Home pública del portfolio;
- caso PULSE;
- caso AuditFlow;
- caso CMMS 2.0;
- caso Power BI Studio;
- borradores de contenido de proyectos;
- documentación de privacidad y contenido;
- plantilla de casos de estudio;
- estructura del repositorio y material visual documentado.

## Criterios aplicados

Un contenido público se considera seguro cuando:

1. no utiliza nombres reales de proyectos, clientes, proveedores, personas o ubicaciones profesionales;
2. no utiliza datos, fechas, cifras o registros operativos reales;
3. no reutiliza códigos, hitos, documentos, controles o indicadores de procesos corporativos reales;
4. no reproduce fielmente una pantalla profesional cuando esa fidelidad no es necesaria;
5. no expone nombres internos de servidores, bases de datos, tablas, procedimientos, rutas, endpoints o servicios;
6. utiliza terminología sectorial únicamente cuando es genérica y necesaria para explicar la capacidad profesional;
7. no permite identificar un proyecto real mediante la combinación de varios elementos aparentemente inocuos;
8. no inventa métricas de impacto para mejorar el relato.

## Resultado por caso

### PULSE — APTO CON REGLAS REFORZADAS

Estado: apto para publicación con recreaciones.

Observaciones:

- los nombres de proyecto, empresas, identificadores y cifras de la recreación pública son ficticios;
- la interfaz pública es una recreación, no una captura de producción;
- el lenguaje sectorial utilizado es genérico y sirve para explicar el problema funcional;
- se ha reforzado el borrador de contenido para exigir también hitos, códigos, documentos, etiquetas y terminología ficticia o generalizada cuando proceda de un entorno profesional;
- cualquier futura evidencia visual deberá reconstruirse desde cero.

### AuditFlow — CORREGIDO

Estado: apto después de correcciones.

Riesgos detectados durante la auditoría:

- se habían conservado nombres reales de hitos del proceso dentro de una recreación ficticia;
- algunos códigos e indicadores mantenían una forma demasiado próxima a referencias reales;
- parte de la terminología describía de forma demasiado directa estructuras del entorno profesional.

Correcciones realizadas:

- sustitución de hitos por nombres completamente ficticios;
- sustitución de códigos por identificadores `DEMO-*`;
- sustitución de indicadores por controles sintéticos no derivados de preguntas reales;
- sustitución de nombres de informes por archivos ficticios;
- generalización de referencias a áreas y sistemas internos;
- refuerzo del aviso de confidencialidad visible en el caso.

### CMMS 2.0 — APTO

Estado: apto.

Observaciones:

- el caso utiliza un activo, instalación y datos ficticios;
- AMEF y RCM son términos metodológicos/sectoriales, no nomenclaturas corporativas privadas;
- no se han detectado nombres de clientes, proyectos, ubicaciones profesionales ni datos operativos reales;
- el caso explica decisiones y metodología sin revelar arquitectura corporativa.

### Power BI Studio — APTO

Estado: apto.

Observaciones:

- el ejemplo `Northstar Retail` y sus cifras son sintéticos;
- el caso está basado en activos propios y estándares de trabajo, no en datos de una empresa real;
- no se han detectado identificadores, nombres de clientes, proyectos internos o datos operativos reales;
- las métricas se utilizan como datos de demostración y no como resultados atribuidos a un proyecto real.

## Cambios de gobierno aplicados

Se han actualizado:

- `docs/PRIVACY_GUIDELINES.md`;
- `docs/CONTENT_GUIDELINES.md`;
- `docs/CASE_STUDY_TEMPLATE.md`;
- `content/proyectos/PULSE.md`;
- `src/pages/proyectos/auditflow.astro`.

Las nuevas reglas establecen que una recreación profesional debe ficticiar o generalizar, según corresponda:

- nombres;
- empresas y personas;
- ubicaciones;
- hitos;
- códigos;
- fechas;
- cifras;
- documentos;
- indicadores;
- reglas de validación;
- etiquetas y estados internos;
- nombres técnicos internos;
- estructuras visuales o de arquitectura que revelen demasiado contexto.

## Regla operativa desde ahora

**Las capturas reales de aplicaciones profesionales nunca se publicarán en el portfolio.**

Podrán utilizarse únicamente como referencia privada para comprender una función. La versión pública se reconstruirá desde cero y deberá superar el gate de privacidad antes de publicarse.

## Gate previo a cada nuevo caso

Antes de publicar una nueva pieza se comprobará:

- [ ] datos ficticios;
- [ ] nombres ficticios;
- [ ] proyectos ficticios;
- [ ] hitos ficticios cuando procedan de un proceso profesional;
- [ ] códigos e identificadores ficticios;
- [ ] documentos y archivos ficticios;
- [ ] indicadores y controles ficticios;
- [ ] terminología interna generalizada;
- [ ] arquitectura y estructuras simplificadas;
- [ ] ausencia de métricas inventadas de impacto;
- [ ] imposibilidad razonable de identificar el proyecto real por combinación de pistas.

## Conclusión

Tras las correcciones realizadas, no se han detectado en las páginas públicas actuales nombres de clientes, proyectos reales, personas, cifras operativas o documentos reales. AuditFlow era el principal punto débil y ha sido corregido.

El riesgo residual más importante no está en los casos actuales, sino en futuras ampliaciones del portfolio. Por ese motivo las reglas de privacidad pasan a formar parte obligatoria de la plantilla y del proceso de publicación.
