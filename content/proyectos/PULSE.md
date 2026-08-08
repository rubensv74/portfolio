# PULSE — Plataforma de gestión para proyectos industriales

> Borrador de contenido. Este documento todavía no está aprobado para publicación.

## Resumen

PULSE es una iniciativa orientada a mejorar la forma en que se consulta, revisa y gestiona información operativa de proyectos industriales.

El trabajo combina análisis funcional, diseño de experiencia, datos, automatización y construcción incremental de soluciones. Uno de sus focos principales ha sido transformar información dispersa y procesos de revisión pesados en espacios de trabajo más claros y útiles para la toma de decisiones.

Mi aportación se ha centrado en entender las necesidades funcionales, diseñar la experiencia, ordenar la información, definir el comportamiento esperado y evolucionar la solución de forma incremental apoyándome en herramientas de Microsoft Power Platform, SQL y técnicas de desarrollo asistido por IA.

## El problema

En entornos industriales complejos, una parte importante del trabajo de seguimiento depende de grandes volúmenes de información, revisiones periódicas y coordinación entre distintas disciplinas.

Cuando esa información se reparte entre tablas extensas, pantallas aisladas y procesos manuales, aparecen problemas frecuentes:

- cuesta obtener una visión rápida de la situación;
- las reuniones se consumen navegando por datos en lugar de tomando decisiones;
- es difícil pasar de un indicador general al detalle que lo explica;
- los usuarios necesitan demasiados pasos para revisar o localizar información;
- mantener una experiencia coherente se vuelve cada vez más difícil a medida que la solución crece.

## El contexto

PULSE se ha ido desarrollando como una plataforma modular alrededor de diferentes necesidades de gestión de proyectos.

Para el portfolio, el caso se presentará de forma anonimizada y con información recreada. No se publicarán datos reales, nombres de clientes, detalles internos ni capturas que puedan contener información sensible.

## Mi papel

Mi trabajo ha incluido, entre otras responsabilidades:

- análisis de necesidades funcionales;
- definición de flujos de usuario;
- diseño y revisión de pantallas;
- definición de componentes reutilizables;
- diseño de consultas y estructuras de datos junto con la capa funcional;
- definición de automatizaciones;
- validación incremental de funcionalidades;
- detección y resolución de problemas funcionales;
- documentación del método de trabajo y de las decisiones tomadas;
- utilización de IA como apoyo para acelerar análisis, documentación, prototipado y desarrollo.

## Una de las áreas de trabajo: revisión de incidencias

Uno de los problemas abordados ha sido mejorar la revisión de grandes conjuntos de incidencias durante sesiones de seguimiento.

El objetivo no era simplemente mostrar una tabla más bonita. La pantalla debía ayudar a responder preguntas como:

- ¿cuál es la situación general?
- ¿dónde se concentra el trabajo pendiente?
- ¿qué disciplinas o responsables requieren atención?
- ¿puedo pasar del resumen al detalle sin perder el contexto?
- ¿puedo revisar varias incidencias de forma ágil durante una reunión?

## Cómo se abordó

La solución se fue construyendo por partes pequeñas y verificables.

En lugar de intentar terminar una pantalla compleja de una sola vez, cada zona asumía una responsabilidad clara: indicadores, distribución de información, filtros, detalle y acciones.

Cada bloque se validaba antes de continuar. Cuando aparecía un problema, se resolvía antes de añadir nuevas capas de complejidad.

Este enfoque permitió separar problemas de diseño, comportamiento y datos, y facilitó corregir decisiones sin rehacer toda la solución.

## Decisiones importantes

### Separar visión general y detalle

La pantalla debía permitir comprender rápidamente el estado general y, al mismo tiempo, acceder al detalle operativo.

La solución combinó indicadores, gráficos y una tabla de detalle conectados por el mismo contexto de filtrado.

### Convertir los gráficos en herramientas de trabajo

Los gráficos no debían ser simples elementos decorativos.

Por ejemplo, seleccionar una disciplina debía servir también para filtrar la información que se estaba revisando. Esto convierte una visualización en una forma directa de navegar por el problema.

### Construir componentes reutilizables

A medida que crecía la solución se hizo necesario evitar que cada pantalla resolviera los mismos problemas de una manera diferente.

Se comenzó a trabajar con componentes reutilizables para indicadores, gráficos, tablas y acciones, buscando una experiencia más consistente.

### Documentar el método de construcción

La experiencia mostró que trabajar con IA era mucho más eficaz cuando existía un método explícito: dividir el trabajo, congelar decisiones importantes, validar cada paso y mantener la documentación junto al proyecto.

Esto terminó convirtiéndose en un protocolo de implementación incremental reutilizable en otros desarrollos.

## La solución

El resultado es una experiencia de trabajo que busca concentrar en una misma pantalla:

- información de situación;
- indicadores principales;
- distribución de incidencias;
- filtros relevantes;
- acceso al detalle;
- acciones de revisión;
- navegación entre niveles de información.

La intención es que la persona usuaria dedique menos esfuerzo a localizar información y más a interpretar qué está ocurriendo y decidir qué hacer.

## Tecnologías utilizadas

### Microsoft Power Apps

Para construir las experiencias de usuario y los espacios de trabajo.

### Microsoft Power Automate

Para automatizar procesos que conectan la aplicación con otras tareas y servicios.

### SQL Server

Para organizar, consultar y preparar información de forma eficiente.

### GitHub

Para mantener documentación, decisiones y evolución del trabajo de forma ordenada.

### Inteligencia artificial

Como apoyo al análisis, documentación, prototipado, revisión y desarrollo, manteniendo las decisiones funcionales y de diseño bajo control humano.

## Evolución

PULSE no se ha diseñado como una solución cerrada desde el primer día.

Las pantallas han pasado por varias iteraciones. Algunos componentes se descartaron, otros se simplificaron y determinadas decisiones se revisaron después de comprobar cómo funcionaban en la práctica.

Esa evolución forma parte del caso de estudio y es uno de los aspectos que interesa mostrar en el portfolio: no solo el resultado final, sino cómo una solución mejora cuando se prueba, se cuestiona y se corrige.

## Resultado

El trabajo realizado ha permitido avanzar hacia una experiencia más integrada y orientada a la revisión operativa, reduciendo la dependencia de vistas aisladas y acercando el resumen y el detalle dentro del mismo flujo de trabajo.

En la versión pública del portfolio solo se incluirán resultados que puedan explicarse de forma segura y verificable.

## Lo que he aprendido

Entre los principales aprendizajes del proyecto destacan:

- una pantalla con mucha información no es necesariamente una buena pantalla;
- los indicadores son más útiles cuando ayudan a actuar y no solo a observar;
- dividir el desarrollo en piezas pequeñas facilita detectar errores y tomar mejores decisiones;
- documentar las decisiones evita repetir discusiones y mejora la continuidad del trabajo;
- la IA acelera mucho el desarrollo cuando existe un método claro y límites de decisión bien definidos;
- el diseño funcional, los datos y la experiencia de usuario no deben tratarse como problemas independientes.

## Material que habrá que preparar para el portfolio

Antes de publicar este caso se deberán crear versiones seguras de:

- una imagen general de la solución;
- un diagrama sencillo del flujo de revisión;
- una pantalla recreada con datos ficticios;
- una comparación entre una primera versión y una versión evolucionada;
- un esquema de cómo se conectan resumen, filtros y detalle.

## Capacidades demostradas

- análisis funcional;
- diseño de soluciones;
- transformación digital;
- diseño de experiencia de usuario;
- Power Platform;
- SQL;
- automatización;
- documentación;
- trabajo incremental;
- IA aplicada al análisis y desarrollo.
