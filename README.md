# VotaLive

VotaLive es una aplicación web para crear votaciones de aula y permitir que los alumnos consulten una votación mediante un código. Está construida con [Astro](https://astro.build/) y se ejecuta en [Cloudflare Workers](https://workers.cloudflare.com/) con una base de datos [Cloudflare D1](https://developers.cloudflare.com/d1/).

## Requisitos

- Node.js 22.12 o superior.
- Una cuenta de Cloudflare.
- Wrangler, incluido como dependencia del proyecto.
- Git, si vas a clonar o publicar el repositorio.

Comprueba las versiones instaladas:

```powershell
node --version
npm --version
```

## Instalación

Clona el repositorio y entra en la carpeta del proyecto:

```powershell
git clone https://github.com/eduardosalinasurquia-sketch/Developer-Votalive.git
cd Developer-Votalive
```

Instala las dependencias:

```powershell
npm install
```

## Configuración local

Copia `.dev.vars.example` como `.dev.vars` y define la clave que usará el panel docente:

```powershell
Copy-Item .dev.vars.example .dev.vars
```

Edita `.dev.vars` y asigna una clave segura:

```text
ADMIN_KEY=una-clave-secreta-local
```

Este archivo es local y está excluido de Git. Nunca publiques sus valores.

## Base de datos D1

La configuración de Wrangler ya apunta a la base de datos `votalive-db`. Para aplicar la migración inicial en la base de datos remota:

```powershell
npx wrangler login
npx wrangler d1 migrations apply votalive-db --remote
```

La migración crea las tablas de votaciones, opciones, participantes y votos. Si necesitas comprobar el estado:

```powershell
npx wrangler d1 migrations list votalive-db --remote
```

## Ejecución en desarrollo

Inicia el servidor:

```powershell
npm run dev
```

Abre `http://localhost:4321` en el navegador. Las páginas principales son `/`, `/docente`, `/alumno` y `/votar?code=XXXXXXXX`.

El adaptador de Cloudflare puede intentar conectarse al modo remoto durante el desarrollo. Si todavía no tienes un subdominio `workers.dev`, pulsa `l` en la terminal para usar el modo local, o créalo desde **Workers & Pages** en el panel de Cloudflare.

## Comprobaciones antes de publicar

```powershell
npm run generate-types
npm run build
npm run preview
```

`generate-types` actualiza los tipos de Cloudflare, `build` comprueba la compilación de producción y `preview` sirve esa compilación localmente.

## Despliegue en Cloudflare Workers

1. Inicia sesión:

   ```powershell
   npx wrangler login
   ```

2. Configura la clave de administración como secreto remoto:

   ```powershell
   npx wrangler secret put ADMIN_KEY
   ```

3. Aplica las migraciones en D1:

   ```powershell
   npx wrangler d1 migrations apply votalive-db --remote
   ```

4. Construye y despliega:

   ```powershell
   npm run deploy
   ```

El comando `npm run deploy` ejecuta `astro build` y después `wrangler deploy`. Al terminar, Wrangler mostrará la URL pública del Worker.

## Comandos disponibles

| Comando | Función |
| --- | --- |
| `npm run dev` | Inicia el servidor de desarrollo. |
| `npm run build` | Genera la compilación de producción en `dist/`. |
| `npm run preview` | Sirve localmente la compilación generada. |
| `npm run deploy` | Compila y publica en Cloudflare Workers. |
| `npm run generate-types` | Genera los tipos a partir de la configuración de Wrangler. |
| `npm run astro -- --help` | Muestra la ayuda de Astro. |

## Estructura principal

```text
src/
├── layouts/MainLayout.astro   # Diseño compartido
├── pages/                     # Páginas y endpoints
├── styles/global.css          # Estilos globales
migrations/                   # Migraciones de Cloudflare D1
wrangler.jsonc                # Configuración de Workers y D1
```

## Seguridad

No subas `.dev.vars`, claves de administración ni credenciales a GitHub. Para producción utiliza `npx wrangler secret put ADMIN_KEY`. La variable `ADMIN_KEY` solo debe estar disponible en el entorno del servidor.

## Repositorio

https://github.com/eduardosalinasurquia-sketch/Developer-Votalive
