# Tele Tranquila — resumen del proyecto

## Objetivo
Interfaz simple para ver una lista **curada** de videos de YouTube en una Smart TV Samsung, sin autoplay, sin sugerencias ni pantalla de "próximo video" al terminar. Pensada para que un hijo pueda elegir entre contenido pre-aprobado por el padre, navegando con el control remoto.

## Enfoque elegido
De tres alternativas evaluadas (descargar a pendrive / descargar + reproductor propio / reproducir directo desde YouTube), se eligió **reproducir directo desde YouTube** usando el **YouTube IFrame Player API**, embebido en una página web propia. Ventajas: no requiere descargar ni mantener archivos, es legal (usa la API oficial de embed), y da control total sobre la interfaz.

## Estado actual
Archivo único: `tele-tranquila.html` (HTML + CSS + JS vanilla, sin dependencias externas salvo la librería `iframe_api` de YouTube que se carga desde `https://www.youtube.com/iframe_api`).

### Funcionalidad implementada
- **Grilla de videos** (4 columnas) con miniatura (`img.youtube.com/vi/{id}/hqdefault.jpg`), título y navegación con flechas del control remoto + Enter/OK.
- **Reproductor embebido** con parámetros que minimizan la lógica de retención de YouTube: `rel=0` (sin relacionados de otros canales), `modestbranding=1`, `iv_load_policy=3` (sin tarjetas/anotaciones), sin autoplay al siguiente video.
- **Panel de fin de video**: al terminar (`YT.PlayerState.ENDED`), muestra un panel simple con botón "Volver a la lista" — no autoreproduce nada.
- **Botón "volver" del control remoto**: escucha `Backspace`, `Escape` y el keyCode 10009 (código estándar del botón Back en Tizen) para salir del reproductor.
- Diseño con paleta propia (navy `#14202b`, acento amber `#e8a33d`, tarjetas `#1f303f`), focus visible marcado para navegación por remoto.

### Configuración de contenido
La lista de videos vive en un array `VIDEOS` al principio del `<script>`:
```js
const VIDEOS = [
  { id: "VIDEO_ID_DE_YOUTUBE", title: "Título a mostrar" },
  // ...
];
```
El `id` es el valor que sigue a `?v=` en la URL de YouTube. Actualmente tiene 4 videos de **muestra/prueba** (Big Buck Bunny, Sintel, Tears of Steel, un video de paisaje) — hay que reemplazarlos por el contenido real elegido para el hijo.

## Bugs encontrados y resueltos
1. **`ReferenceError: YT is not defined`**: se intentaba crear el player antes de que cargara la librería asíncrona de YouTube. Solución: flag `apiReady` + callback global `window.onYouTubeIframeAPIReady`; si el usuario toca play antes de tiempo, el video queda en `pendingVideoId` y se reproduce automáticamente apenas la API está lista.
2. **YouTube Error 153 ("Error de configuración del reproductor")**: causado por usar IDs de video inventados/no reales en los ejemplos iniciales. Se reemplazaron por IDs reales y embebibles para poder validar el mecanismo.

## Pendiente / en curso
- **Deploy a hosting con HTTPS real**: se estaba guiando al usuario a través de GitHub Pages (Settings del repo → Pages → Source: Deploy from a branch → elegir rama `main` → Save). El usuario reportó no encontrar el paso de activar Pages en Settings — quedó sin confirmar si:
  - ya tiene un repositorio creado en GitHub,
  - el repo es público (requisito para Pages gratis),
  - ya hizo al menos un commit/subida de archivo (si no, no aparece la rama `main` en el desplegable).
  - Alternativa más rápida sin cuenta: **Netlify Drop** (app.netlify.com/drop), arrastrar el HTML y listo.
- **Reemplazar los 4 videos de muestra** por el contenido real curado para el hijo.
- **Probar en la Smart TV Samsung real** vía navegador Samsung Internet, apuntando a la URL pública (GitHub Pages o Netlify).
- **Opcional a futuro**: empaquetar como Tizen Web App (mismo HTML, vía Tizen Studio) para que aparezca como ícono en el Smart Hub en vez de abrirse por navegador. No es necesario para que funcione, solo mejora la experiencia de acceso.
- **Ideas sugeridas y no implementadas aún**: categorías/carpetas de videos, temporizador de apagado automático.

## Archivo de referencia
El HTML completo (249 líneas) está en los outputs de la conversación de Claude.ai como `tele-tranquila.html`. Se recomienda pegarlo en el proyecto local antes de seguir iterando en Claude Code.
