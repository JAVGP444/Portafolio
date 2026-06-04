# Raíz de México — Diseño de proyecto

[← Portfolio](../../README.md) · [CV](../../CV.md)

**Francisco Javier Granados Paz**  
Estudiante de Ciencias de Datos para Negocios · UNRC · Proyecto Prototípico · Equipo 301  

**Periodo:** 2025–2026  
**Tipo:** Proyecto académico — tercer semestre (Proyecto Prototípico)  
**Rol:** Diseño e implementación de la plataforma (datos, interfaz, despliegue)  
**Producción:** [app.raizdemex.com](https://app.raizdemex.com) · [api.raizdemex.com](https://api.raizdemex.com) *(demo finalizada; ver capturas)*  

---

## Qué es este proyecto

**Raíz de México** es una plataforma web que desarrollé para apoyar decisiones agrícolas basadas en datos: policultivos, comparativos por estado, riesgo, costos e ingresos defendibles. La pensé para dos usos al mismo tiempo: **presentar resultados en auditorio** (proyector, PDFs, narrativa ejecutiva) y **profundizar en consultoría** con visualizaciones interactivas.

No es un mockup: llegó a producción con frontend en Vercel, API en Railway y DNS en Cloudflare.

## El reto

Cuando empecé, la información estaba repartida: tablas, gráficas, informes y pantallas de consultoría no conversaban entre sí. Además, al embeber la Sábana 3D en un iframe, el scroll se cortaba o quedaba un bloque vacío debajo del radar territorial. Necesitaba **una sola aplicación** que sirviera para exponer, analizar y descargar evidencia.

## Qué construí

Organicé la app en cinco módulos principales:

| Módulo | Para qué sirve |
|--------|----------------|
| **Presentación** | Deck 4:3, modo auditorio, tablas expandibles, rotación de tres motores de decisión cada 6 segundos y descarga de *Informe Ejecutivo* y *Documento de Investigación* |
| **Consultoría** | Sábana 3D embebida, radar territorial por estado, sin barra lateral; altura del iframe sincronizada con el contenido real |
| **Sala de datos** | Grafo de dominios, artefactos y descargas orientadas a due diligence |
| **Soporte comercial** | Flujos de cotización y cobertura nacional |
| **Lean Canvas** | Modelo de negocio integrado en la misma SPA |

En el backend implementé API REST, WebSockets para telemetría y alertas, Prisma sobre PostgreSQL, panel AdminJS y un simulador MQTT para sensores (suelo, clima, agua). Los PDFs se integran en el build de producción para que las descargas funcionen aunque la API esté limitada.

## Stack que utilicé

```text
Frontend     React 19 · Vite · Tailwind · Framer Motion · ECharts · MapLibre · XYFlow
Backend      Node.js · Express · Socket.io · Prisma · AdminJS
Datos        PostgreSQL (Railway)
Deploy       Vercel · Railway · Cloudflare
Integración  iframe + postMessage · rewrites en vercel.json
```

## Arquitectura en producción

```mermaid
flowchart LR
  U[Usuario] --> CF[Cloudflare DNS]
  CF --> V[Vercel app.raizdemex.com]
  CF --> R[Railway api.raizdemex.com]
  V -->|rewrite /api| R
  V -->|iframe Sábana 3D| R
  R --> DB[(PostgreSQL)]
```

## Decisiones de las que estoy más orgulloso

1. **Iframe de consultoría** — Medí la altura del contenido visible en `.workspace` y la publiqué al padre con `postMessage`, permitiendo que el iframe crezca o encoja sin dejar espacio muerto.
2. **Presentación para proyector** — Unifiqué los tres motores (matemático, contable, probabilístico) en una sola diapositiva con transición suave y paleta clara para salas con luz.
3. **PDFs en el build** — Automatizé la copia de informes al `dist` antes de `vite build`, combinando descarga estática y endpoints de API.
4. **Monorepo desplegable** — Mismo repositorio, dos raíces de deploy (`frontend/` en Vercel, `backend/` en Railway).

## Capturas (30 pantallas de producción)

Así se veía la plataforma en uso real antes de dar de baja los servidores:

| | | |
|:---:|:---:|:---:|
| ![01](./screenshots/01-captura.jpg) | ![02](./screenshots/02-captura.jpg) | ![03](./screenshots/03-captura.jpg) |
| ![04](./screenshots/04-captura.jpg) | ![05](./screenshots/05-captura.jpg) | ![06](./screenshots/06-captura.jpg) |
| ![07](./screenshots/07-captura.jpg) | ![08](./screenshots/08-captura.jpg) | ![09](./screenshots/09-captura.jpg) |
| ![10](./screenshots/10-captura.jpg) | ![11](./screenshots/11-captura.jpg) | ![12](./screenshots/12-captura.jpg) |
| ![13](./screenshots/13-captura.jpg) | ![14](./screenshots/14-captura.jpg) | ![15](./screenshots/15-captura.jpg) |
| ![16](./screenshots/16-captura.jpg) | ![17](./screenshots/17-captura.jpg) | ![18](./screenshots/18-captura.jpg) |
| ![19](./screenshots/19-captura.jpg) | ![20](./screenshots/20-captura.jpg) | ![21](./screenshots/21-captura.jpg) |
| ![22](./screenshots/22-captura.jpg) | ![23](./screenshots/23-captura.jpg) | ![24](./screenshots/24-captura.jpg) |
| ![25](./screenshots/25-captura.jpg) | ![26](./screenshots/26-captura.jpg) | ![27](./screenshots/27-captura.jpg) |
| ![28](./screenshots/28-captura.jpg) | ![29](./screenshots/29-captura.jpg) | ![30](./screenshots/30-captura.jpg) |

[Todas las capturas en alta resolución](./screenshots/)

## Cómo levantarlo en local

El repositorio es un **monorepo** (carpetas `frontend/` y `backend/`). No hace falta MySQL para una prueba rápida: el backend puede correr en memoria.

### Opción A — Script automático (Windows)

```powershell
git clone https://github.com/JAVGP444/Javier-Granados-pp.git
cd Javier-Granados-pp
powershell -ExecutionPolicy Bypass -File scripts\LEVANTAR-RAIZ-LOCAL.ps1
```

Abre **http://localhost:5173** (app) y **http://localhost:4000** (API + Sábana embebida).

### Opción B — Manual

```powershell
git clone https://github.com/JAVGP444/Javier-Granados-pp.git
cd Javier-Granados-pp
copy backend\.env.local.example backend\.env
npm install --workspace backend --workspace frontend
npm run prisma:generate --workspace backend
```

Terminal 1:

```powershell
npm run dev:backend
```

Terminal 2:

```powershell
cd frontend
npm run dev
```

### Si ves muchos errores

| Error típico | Solución |
|--------------|----------|
| `Can't reach database` / Prisma | En `backend\.env` pon `ENABLE_MYSQL=false` |
| `AUTH_SECRET` en producción | Solo en deploy; en local usa `.env.local.example` |
| `npm run dev` desde raíz falla | Usa `npm run dev:backend` + `frontend\npm run dev` |
| Puerto ocupado | Cierra otras ventanas Node o cambia `PORT=4001` |

## Enlaces

[← Portfolio](../../README.md) · [CV](../../CV.md) · [LinkedIn](https://www.linkedin.com/in/francisco-javier-granados-paz-1a196b3a7)
