# Valmira · valmira.tech

Blog técnico en español e inglés, generado con Hugo **0.170.1** y PaperMod.

[Estado y próximas fases](PLAN.md) · [Cómo publicar en Cloudflare](instrucions.md)

## Desarrollo local

Desde la raíz del repo, con Hugo 0.170.1 instalado:

```sh
git submodule update --init --recursive
hugo server -D
```

Abre <http://localhost:1313/es/> o <http://localhost:1313/en/>. La raíz `/` lleva a español.

### Probar desde otro equipo de la red

Detén el servidor anterior con `Ctrl+C`. Para escuchar en todas las interfaces:

```sh
hugo server -D --bind 0.0.0.0 --port 1313
```

Para **navegar desde otro equipo**, usa esta variante: sustituye la IP por la del servidor. Así los enlaces de Hugo y el selector de idioma no te devuelven a `localhost`.

```sh
IP=192.168.1.50
hugo server -D --bind 0.0.0.0 --port 1313 \
  --baseURL "http://${IP}:1313/" --appendPort=false
```

Abre `http://192.168.1.50:1313/en/` o `/es/` usando esa misma IP. `0.0.0.0` es la dirección de escucha, no la dirección del navegador. `-D` incluye borradores; esta configuración es para tu red de desarrollo. La URL pública de `hugo.toml` no cambia.

## Escribir en español e inglés

Guarda las dos versiones y sus imágenes en una misma carpeta:

```sh
hugo new content posts/mi-articulo/index.md
hugo new content posts/mi-articulo/index.en.md
```

`index.md` es español (idioma predeterminado); `index.en.md` es su traducción. Traduce título, descripción y cuerpo. Si añades `slug: "my-article"` al frontmatter inglés, las rutas serán `/es/posts/mi-articulo/` y `/en/posts/my-article/`. Hugo mantiene la relación entre ambos archivos. Para índices de sección se usa `_index.md` y `_index.en.md`.

En Markdown, usa `{{< relref "/posts" >}}` para enlazar al listado en el idioma actual. Los feeds de artículos son `/es/posts/index.xml` y `/en/posts/index.xml`.

Cada versión empieza con `draft: true`. Cuando esté lista, cambia su valor a `false` y ejecuta `hugo --gc --minify` para comprobar el build de producción.
