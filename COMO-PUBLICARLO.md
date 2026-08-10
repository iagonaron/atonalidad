# Publicar el Taller de Atonalidad y quitar los anuncios de los vídeos

## Lo primero: sí, los modos 2 y 3 llevan anuncios

El reproductor incrustado de YouTube **sirve anuncios igual que la web de YouTube**. El dominio
`youtube-nocookie.com` que usa el portal solo evita cookies de seguimiento: **no quita la publicidad**.
Abrirlo en pestaña aparte tampoco. La única forma de tener cero anuncios es el **modo 1**: que el vídeo
sea un archivo tuyo servido desde tu propia web.

La buena noticia: si subes los archivos al repositorio, el modo 1 funciona **igual de bien en la pantalla
del aula que en casa de tus alumnos**. Nadie ve un anuncio, ni tú ni ellos.

---

## 1 · La estructura de la carpeta

```
ATONALIDAD/
├── index.html
├── manifest.webmanifest
├── favicon.ico   ·   favicon-32.png   ·   apple-touch-icon.png
├── icon-192.png  ·   icon-512.png     ·   icon-1024.png
├── icon-maskable-192.png · icon-maskable-512.png
└── videos/
    ├── catcerto.mp4
    ├── imaginary-landscape-4.mp4
    ├── sequenza-iii.mp4
    ├── pithoprakta.mp4
    └── metastasis.mp4
```

Los nombres tienen que ser **exactamente esos**. No hace falta ponerlos todos: cada botón busca solo el suyo.

---

## 2 · Preparar los vídeos (el paso importante)

GitHub tiene tres topes que conviene conocer:

| Límite | Valor | A qué se aplica |
|---|---|---|
| Subida arrastrando en la web | **25 MB** | **por archivo** · si te pasas, con GitHub Desktop |
| Aviso de Git | 50 MB | **por archivo** · te avisa pero deja |
| Tope duro | **100 MB** | **por archivo** · por encima GitHub lo rechaza |
| Tamaño del sitio publicado | **1 GB** | **por repositorio** |
| Tráfico | **100 GB al mes** (soft) | **por sitio** |

Todos son **por archivo o por repositorio**, nunca por cuenta: lo que ocupe este proyecto no le quita
espacio a ningún otro repositorio tuyo, ni te acerca a ningún tope global. Y ojo al segundo:
el tope de 100 MB es **de cada archivo suelto**, no de la suma. La suma de la carpeta puede llegar
hasta 1 GB. Con clips de un minuto tienes sitio para decenas de vídeos:
el límite real no va a ser GitHub, va a ser lo que tarde en cargar la página.

**El truco: no subas el vídeo entero.** En clase no pones los 12 minutos de *Metastasis*, pones el fragmento.
Con un minuto por obra, el peso deja de ser un problema y puedes ir a por calidad:

| Un minuto de vídeo | Pesa | Cabe arrastrando (25 MB) |
|---|---|---|
| 720p, CRF 22 | 8 – 14 MB | sí, holgado |
| **1080p, CRF 20** | **18 – 30 MB** | justo; a veces hay que usar GitHub Desktop |
| 1080p, CRF 23 | 12 – 20 MB | sí |

Con clips de 1080p a unos 25 MB caben **unos cuarenta vídeos** dentro del 1 GB del sitio. Margen de sobra.

**Regla de oro: nunca escalar hacia arriba.** Si el original que descargaste es 720p, exportarlo a 1080p
no añade un solo detalle: solo infla el archivo. Conviene mirar antes qué trae de verdad cada fichero
(`ffprobe original.mp4`) y quedarse en esa resolución o por debajo.

### Con ffmpeg (una línea por vídeo)

```bash
ffmpeg -ss 00:00:12 -to 00:01:12 -i "original.mp4" \
       -vf "scale=-2:'min(1080,ih)'" -c:v libx264 -crf 20 -preset slow \
       -c:a aac -b:a 192k -movflags +faststart \
       videos/catcerto.mp4
```

- `-ss` / `-to` → principio y final del fragmento (aquí, un minuto justo).
- `-crf 20` → calidad alta. Bajar a 18 casi no se nota y engorda bastante; subir a 23 aligera mucho.
- `scale=-2:'min(1080,ih)'` → limita a 1080p **pero no amplía** si el original es menor.
- `-b:a 192k` → el audio, que en esta unidad es lo que importa, con margen de sobra.
- **`-movflags +faststart` es imprescindible**: pone el índice del archivo al principio para que el vídeo
  empiece a verse enseguida en vez de descargarse entero. Sin esto, en el aula tendrás unos segundos
  incómodos de pantalla en negro.

Si no tienes ffmpeg: `brew install ffmpeg`, o usa **HandBrake** (gratis, con ventanas): preset
*Fast 720p30*, y en la pestaña *Video* marca *Web Optimized* (es el equivalente a faststart).
También sirve QuickTime para recortar y exportar a 720p, aunque comprime peor.

---

## 3 · Subirlo a GitHub

1. En github.com → **New repository**. Ponle por ejemplo `atonalidad`. Público.
2. **Add file → Upload files** y arrastra todo el contenido de la carpeta `ATONALIDAD`
   (el `index.html` tiene que quedar en la raíz, no dentro de otra carpeta).
   Los vídeos puedes arrastrarlos dentro de una carpeta `videos` en el mismo gesto.
3. **Settings → Pages → Source: Deploy from a branch → `main` / `/ (root)` → Save.**
4. En un minuto tendrás la dirección: `https://TUUSUARIO.github.io/atonalidad/`

Si algún vídeo pasa de 25 MB, súbelo con **GitHub Desktop** (arrastras la carpeta, *Commit*, *Push*)
en vez de por la web.

---

## 4 · El icono en la pantalla del aula

El portal ya es una PWA (por eso lleva `manifest.webmanifest` e iconos), así que se instala como una app
de verdad, sin barra de direcciones:

- **Chrome / Edge**: abre la dirección → icono de instalar en la barra, o menú ⋮ →
  *Enviar, guardar y compartir* → *Instalar página como aplicación*. Aparece en el Dock y en Launchpad
  con el icono violeta de los doce sonidos.
- **Safari 17 o más nuevo**: *Archivo → Añadir al Dock*.
- **Pantalla táctil con Windows**: Edge → ⋯ → *Aplicaciones* → *Instalar este sitio como una aplicación*,
  y luego botón derecho en el icono → *Anclar a Inicio*.

Una vez instalado, se abre a pantalla completa de un toque. Eso es exactamente el "icono directo" que querías.

---

## 5 · Activar el modo aula (una sola vez, en el ordenador de clase)

Abre cualquier vídeo del portal y pulsa el botón **🔓 Modo aula** de la ventana. Se queda en
**🔒 Modo aula: sin YouTube**.

A partir de ahí, en ese ordenador el portal **nunca incrusta YouTube**. Si algún día falta un archivo,
en lugar de colarte un anuncio te dice que ese vídeo no está subido. Es el cinturón de seguridad para
que no te lleves una sorpresa delante de treinta alumnos.

Los alumnos en su casa no tocan ese botón, así que a ellos, si algún vídeo no está en `videos/`,
les seguirá funcionando el respaldo de YouTube.

---

## 6 · Plan B para cuando falle el wifi

Guarda una copia de toda la carpeta (con sus vídeos) en el disco del ordenador del aula y crea un alias
en el escritorio apuntando al `index.html`. Doble clic y funciona **sin internet ninguno**: el portal
detecta que está en local y busca los vídeos en la carpeta de al lado. Es literalmente la misma carpeta
que subes a GitHub, así que no tienes que mantener dos versiones distintas.

---

## 7 · Dos avisos prácticos

**Derechos.** Si el repositorio es público estás republicando vídeos que no son tuyos. Lo prudente es subir
solo el **fragmento** que usas en clase —que además es lo que te conviene por peso— y dejar visible el
botón *Abrir en YouTube*, que enlaza al original. Si prefieres curarte en salud, otra opción perfectamente
válida es no subir la carpeta `videos/` a GitHub y tenerla solo en el ordenador del aula: tú sin anuncios,
tus alumnos con el YouTube incrustado.

**Cambiar un enlace de YouTube.** Abre `index.html` con un editor de texto, busca `const VIDEOS={` y cambia
el campo `yt:'...'`. Ahí va solo el código del vídeo, lo que aparece detrás de `watch?v=`.
Ejemplo: `https://www.youtube.com/watch?v=zeoT66v4EHg` → `yt:'zeoT66v4EHg'`.
Para algo rápido y de un día, pega el enlace en el recuadro de la propia ventana y pulsa Intro.
