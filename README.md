# Finora — Aplicación de Finanzas Personales

Aplicación web full-stack de finanzas personales con autenticación por roles, panel de administración completo, gráficas interactivas y exportación de reportes.

**Demo de Página** https://finora-app-financuiera-personal.vercel.app/
---

## Log in
<img width="1915" height="900" alt="Captura de pantalla 2026-07-09 194932" src="https://github.com/user-attachments/assets/dfc5ef31-58ce-4950-8bbc-cb0855e05418" />

## Dashboard User
<img width="1897" height="883" alt="Captura de pantalla 2026-07-09 194235" src="https://github.com/user-attachments/assets/00359a9f-8850-4b03-b520-6bc60a43ffc0" />

## Panel Admin
<img width="1897" height="891" alt="Captura de pantalla 2026-07-09 194656" src="https://github.com/user-attachments/assets/5463edd6-fe08-46ea-bd3b-1f2678b8776d" />

---
## Usuario / Admin "prueba"

| Usuario | Admin |
|--------|-------------|
| elio@gmail.com | adminjose@gmail.com |
| Elio_0402 | Jose_0402 |

## Stack tecnológico

**Frontend**
- **Next.js 16** — App Router, Server & Client Components, Turbopack
- **React 19** · **TypeScript 5**
- **Tailwind CSS v4** — diseño oscuro 100% personalizado, sin librerías de componentes
- **Recharts v3** — 10+ gráficas interactivas (Line, Bar, Area, Pie)
- **Framer Motion v12** — animaciones y transiciones
- **Lucide React** — sistema de íconos

**Backend**
- **Next.js Route Handlers** — API REST propia con 30+ endpoints
- **Auth.js v5 (NextAuth)** — autenticación JWT con Google OAuth y Credentials
- **Prisma v5** — ORM con esquema tipado
- **bcryptjs** — hash de contraseñas
- **Middleware de Next.js** — protección de rutas por rol en el edge (sin acceder a la BD)

**Infraestructura**
- **PostgreSQL** via **Supabase** (cloud)
- **Vercel** — despliegue con CI/CD automático desde GitHub

---

## Funcionalidades

### App de usuario

- **Dashboard** — balance total, score financiero, evolución mensual, transacciones recientes
- **Transacciones** — CRUD completo con tipo (ingreso/gasto), categorías, búsqueda y filtros
- **Cuentas** — Efectivo, Yape, Plin, Bancaria, Crédito, Ahorros con balance individual
- **Presupuestos** — límites mensuales por categoría con barra de progreso y alertas
- **Metas de ahorro** — objetivos con monto, avance y fecha límite
- **Deudas** — registro de deudas propias y de terceros con pagos parciales
- **Reportes** — gráficas de evolución e ingresos vs gastos por período
- **Exportar** — PDF (jsPDF) y CSV (PapaParse) con filtros de fecha
- **Sugerencias** — formulario para enviar mejoras o reportar errores al equipo admin
- **Búsqueda global** — Ctrl+K para buscar en toda la aplicación
- **Historial de actividad** — log de acciones del usuario
- **Configuración** — moneda (PEN/USD/EUR), idioma (ES/EN), notificaciones

### Panel de administración (`/admin`)

- **Dashboard admin** — KPIs globales y 6 gráficas: usuarios nuevos diarios, transacciones diarias, ingresos vs gastos, top categorías, crecimiento acumulado de usuarios y usuarios activos por mes
- **Gestión de usuarios** — lista con búsqueda, ver detalle completo, suspender/reactivar cuenta, cambiar rol a admin, eliminar usuario
- **Detalle de usuario** — estadísticas individuales, últimas transacciones, cuentas y metas
- **Reportes globales** — volumen mensual, top usuarios activos; exportar a CSV y PDF
- **Categorías globales** — todas las categorías de la plataforma con volúmenes
- **Seguridad** — historial de accesos (éxito/fallo), detección de cuentas con actividad sospechosa (≥3 intentos fallidos en 1h), log de auditoría
- **Sugerencias** — gestión de estado: pendiente → revisado → implementado

---

## Decisiones técnicas

**RBAC con JWT + Middleware en el edge**
El rol (`user`/`admin`) y el estado `isActive` se almacenan en el JWT y se re-leen desde la BD en cada sign-in para garantizar consistencia. El middleware de Next.js protege todas las rutas en el edge sin ninguna llamada a la BD.

**Suspensión de cuentas con mensaje correcto**
Auth.js no diferencia "credenciales incorrectas" de "cuenta suspendida" — ambas retornan el mismo error genérico. Solución: endpoint `/api/auth/check` que se consulta antes del `signIn` para mostrar el mensaje adecuado al usuario.

**Google OAuth + redirección por rol**
Google OAuth requiere un redirect real (`redirect: false` devuelve una URL, no navega). Solución: `callbackUrl: "/auth/redirect"` apunta a una página cliente que lee el rol desde la sesión y redirige a `/admin` o `/dashboard`.

**Logs de acceso sin bloquear el flujo de auth**
El registro en `LoginHistory` usa `.catch(() => {})` (fire-and-forget) para que un fallo en el log nunca pueda interrumpir el proceso de login.

**Route groups para layouts independientes**
`(app)/` provee el layout con sidebar de usuario; `(admin)/` provee el sidebar de administración. Cada uno es completamente independiente sin duplicar código.

---

## Esquema de base de datos

| Modelo | Descripción |
|--------|-------------|
| `User` | Rol (`user`/`admin`) + estado `isActive` |
| `Transaction` | Ingresos y gastos con categoría |
| `Wallet` | Cuentas: Yape, Bancaria, Efectivo, etc. |
| `Budget` | Presupuestos mensuales por categoría |
| `Goal` | Metas de ahorro con monto y progreso |
| `Debt` | Deudas propias o de terceros |
| `Suggestion` | Sugerencias de usuarios con estado |
| `LoginHistory` | Registro de intentos de acceso |
| `ActivityLog` | Log de auditoría de la plataforma |
