# Portfolio — Checklist Release Candidate 1

## Objetivo

Dejar una primera versión suficientemente sólida como para revisarla en navegador y decidir si puede convertirse en la versión que se enlazará desde LinkedIn.

## Estado actual

### Contenido principal

- [x] Home con posicionamiento profesional claro.
- [x] Tres casos de estudio diferenciados.
- [x] PULSE con evidencia visual ficticia y fiel a funcionalidades reales.
- [x] CMMS 2.0 con recorrido ficticio basado en el proceso real trabajado.
- [x] Power BI Studio con foco en estándares y reutilización.
- [x] Lenguaje de “web en construcción” eliminado de la Home.
- [x] Casos de estudio situados antes de “Sobre mí” para priorizar evidencia.
- [x] Continuidad de lectura entre los tres casos.

### Privacidad

- [x] Regla global: todos los datos visibles deben ser ficticios.
- [x] Regla global: los nombres de proyectos usados dentro de ejemplos deben ser ficticios.
- [x] No publicar capturas reales por defecto.
- [x] No inventar métricas de impacto.
- [x] No inventar funcionalidades para mejorar una recreación.
- [x] Primera revisión de privacidad completada antes de hacer público el repositorio.
- [ ] Segunda revisión manual final antes de enlazar el portfolio desde LinkedIn.

### Navegación y accesibilidad

- [x] Navegación principal consistente.
- [x] Navegación ordenada para priorizar los proyectos.
- [x] Etiquetas de navegación coherentes con el contenido real de las secciones.
- [x] Enlaces de retorno desde casos de estudio.
- [x] Navegación continua PULSE → CMMS 2.0 → Power BI Studio.
- [x] Enlace para saltar directamente al contenido.
- [x] Foco visible para navegación por teclado.
- [x] Preferencia de movimiento reducido respetada.
- [x] Primera revisión real de la Home en escritorio.
- [x] Ajuste de jerarquía y densidad de la portada tras verla publicada.
- [x] Revisión estructural y responsive de los tres casos.
- [ ] Revisión visual real en un navegador móvil o emulación móvil.

### Compartir desde LinkedIn

- [x] Título y descripción específicos por página.
- [x] Dirección canónica preparada para GitHub Pages.
- [x] Metadatos básicos para compartir enlaces.
- [ ] Imagen social propia integrada físicamente en el repositorio.
- [ ] Comprobar la vista previa real del enlace antes de incorporarlo a LinkedIn.

### Calidad técnica

- [x] Validación automática del proyecto configurada.
- [x] GitHub Pages activado con GitHub Actions.
- [x] Despliegue público operativo.
- [x] Build y deploy de GitHub Pages completados correctamente.
- [x] Ruta base de GitHub Pages corregida.
- [x] Hoja de estilos cargando correctamente en producción.
- [x] Artefacto publicado inspeccionado.
- [x] Rutas publicadas verificadas: Home, PULSE, CMMS 2.0 y Power BI Studio.
- [x] Recursos CSS y enlaces internos verificados en el artefacto publicado.
- [ ] Comprobar que no existen errores de consola en un navegador real.

## Elementos que no bloquean RC1

No son necesarios para la primera revisión pública controlada:

- dominio propio;
- versión en inglés;
- blog;
- formulario de contacto;
- CV descargable;
- animaciones avanzadas;
- nuevos casos de estudio.

## Gate para publicar el enlace en LinkedIn

Antes de colocar la URL en LinkedIn deben cumplirse estas condiciones:

1. revisión visual final en móvil;
2. revisión de privacidad final sin hallazgos;
3. ninguna afirmación profesional que no podamos defender;
4. ninguna funcionalidad inventada en las recreaciones;
5. ningún dato real visible;
6. navegación y recursos sin errores;
7. vista previa del enlace revisada.

La imagen social propia es recomendable antes del lanzamiento definitivo, pero no debe forzarnos a publicar un recurso no validado o a fingir que ya está integrado.

El sitio puede permanecer público mientras realizamos esta revisión, pero no se considerará versión final hasta superar este gate.
