# Publicar Valmira en Cloudflare

## Workers Builds (panel con «Deploy command»)

En **Settings → Build**, configura el Worker conectado a `main`:

| Ajuste | Valor |
|---|---|
| Root directory | `/` (raíz del repositorio, donde están `hugo.toml` y `wrangler.toml`) |
| Build command | `hugo --gc --minify` |
| Deploy command | `npx wrangler deploy` |
| Build variable | `HUGO_VERSION=0.170.1` |

Wrangler publica los archivos de `public/` según `wrangler.toml`. Comprueba que `name` en ese archivo coincide con el nombre del **Worker** en el panel. Si el token de build figura como eliminado, revocado o de otro miembro, usa **Create new token** en la configuración del build y guarda el token seleccionado allí.

Una vez subido `wrangler.toml` a `main`, vuelve a desplegar y comprueba `/es/` y `/en/` en la URL del Worker. Para servir `valmira.tech`, añádelo al Worker en **Settings → Domains & Routes → Add → Custom Domain**.

## Cloudflare Pages (proyecto con «Build output directory»)

Si tu proyecto es de Pages, sigue estos pasos:

## 1. Preparar y subir el sitio

Desde la raíz del repo, comprueba el mismo build que ejecutará Pages:

```sh
git submodule update --init --recursive
hugo version                    # 0.170.1
hugo --gc --minify --panicOnWarning
git status --short
```

Revisa ambas portadas y el selector de idioma en local ([comandos](README.md)). Para el primer despliegue, incluye la configuración, el contenido, la documentación y la referencia al tema:

```sh
git add .gitignore .gitmodules README.md PLAN.md instrucions.md hugo.toml archetypes content themes/PaperMod
git diff --cached --stat
git commit -m "Publica la base bilingüe de Valmira"
git push origin main
```

`public/` es el resultado del build: no se sube. `.gitmodules` y `themes/PaperMod` permiten a Pages descargar la revisión correcta del tema.

## 2. Conectar GitHub (una sola vez)

En **Cloudflare → Workers & Pages**, crea un proyecto de **Pages** con integración de Git. Selecciona `oliver1us/valmira.tech` y configura:

| Ajuste | Valor |
|---|---|
| Rama de producción | `main` |
| Directorio raíz | Dejar vacío (raíz del repo) |
| Comando de producción | `hugo --gc --minify` |
| Directorio de salida | `public` |
| Variable de entorno | `HUGO_VERSION=0.170.1` (producción y previews) |

Inicia el despliegue. Comprueba que termina correctamente y que las rutas `/es/` y `/en/` responden en el host `*.pages.dev` asignado. En producción, los enlaces absolutos y las canónicas apuntan a `https://valmira.tech/` por diseño; la prueba de navegación completa se hace después de conectar el dominio.

**Si usas previews de ramas** y quieres que sus enlaces se queden en la preview, usa este comando de build en vez del anterior:

```sh
if [ "$CF_PAGES_BRANCH" = "main" ]; then hugo --gc --minify; else hugo --gc --minify --baseURL "$CF_PAGES_URL/"; fi
```

## 3. Activar `valmira.tech` y comprobar el resultado

1. Para servir el dominio raíz, añade `valmira.tech` como zona en la misma cuenta de Cloudflare. Si el DNS aún está fuera, compara los registros importados con los actuales (incluido el correo) antes de cambiar los *nameservers* en tu registrador. El dominio se sigue renovando donde lo compraste.
2. Con la zona **Active**, abre el proyecto de Pages → **Custom domains** → añade `valmira.tech`. Confirma el registro DNS propuesto y espera a que el dominio y HTTPS estén activos.
3. Abre <https://valmira.tech/>: debe llevarte a `/es/`. Desde el primer artículo, cambia a **English** y vuelve a **Español**: debes llegar al mismo artículo traducido, siempre sobre `valmira.tech`.
4. Abre los feeds [en español](https://valmira.tech/es/posts/index.xml) y [en inglés](https://valmira.tech/en/posts/index.xml). Comprueba que cada uno enlaza a artículos de su idioma.

## Publicar el siguiente artículo

Crea ambas versiones como indica el [README](README.md), revisa los borradores con `hugo server -D` y quita `draft: true` (o usa `draft: false`) en las versiones listas. Comprueba el build sin `-D`, haz commit y lleva el cambio a `main`. Pages despliega automáticamente: confirma el resultado en **Deployments** y abre las URLs del artículo y su feed.
