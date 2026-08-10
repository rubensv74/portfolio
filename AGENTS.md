# Instrucciones para agentes

## Calidad y entrega

- Validar localmente que el portfolio construye correctamente antes de considerar terminado un cambio que afecte al sitio.
- Los cambios de contenido deben mantener datos ficticios y no introducir información sensible o afirmaciones profesionales no verificables.

## GitHub Actions — Local First / Remote Gate

- La validación de build no debe ejecutarse de nuevo en cada `push` a `main` cuando el workflow de despliegue ya reconstruye el sitio para publicarlo.
- Reservar el workflow de validación a Pull Requests hacia `main` o ejecución manual.
- Mantener el despliegue de GitHub Pages en `push` a `main`, porque sí requiere infraestructura remota y produce una entrega real.
- Usar filtros por rutas y `concurrency` para evitar ejecuciones irrelevantes u obsoletas.
- No añadir workflows de calidad redundantes con el build de despliegue.

Principio obligatorio: **validar localmente primero; ejecutar GitHub Actions solo cuando aporte un gate remoto o una entrega necesaria.**
