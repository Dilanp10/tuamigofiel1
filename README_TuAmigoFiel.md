# 🐾 TuAmigoFiel — Sistema de Gestión para Veterinarias

**TuAmigoFiel** es una aplicación Full Stack para la gestión integral de una veterinaria / pet-shop.  
Permite administrar productos, servicios, ventas (POS), alertas de stock y vencimiento, agenda y reportes.  
Frontend: **React + Vite**. Backend: **Node.js + Express + SQLite**. Autenticación con **JWT**.

---

## 📋 Tabla de contenidos
- [Descripción](#-descripción)  
- [Características](#-características)  
- [Tecnologías](#-tecnologías)  
- [Instalación](#-instalación)  
- [Configuración (env)](#-configuración-env)  
- [Ejecución](#-ejecución)  
- [Uso rápido](#-uso-rápido)  
- [Esquema de la base de datos (resumen)](#-esquema-de-la-base-de-datos-resumen)  
- [Endpoints principales](#-endpoints-principales)  
- [Pruebas y comandos útiles](#-pruebas-y-comandos-útiles)  
- [Roadmap](#-roadmap)  
- [Autor y licencia](#-autor-y-licencia)

---

## 🐶 Descripción
TuAmigoFiel centraliza las operaciones diarias de una veterinaria: inventario, ventas (POS), servicios, alertas automáticas por stock o vencimiento y un panel administrativo seguro mediante JWT. Diseñada para ser práctica, ligera y fácil de desplegar con SQLite para entornos locales u offline.

---

## 🎯 Características
- **Gestión de inventario:** productos con stock, precios, fechas de vencimiento y categorías.  
- **Ingreso / edición de productos:** formularios con validaciones (client side).  
- **Punto de venta (POS):** búsqueda de productos, carrito, creación de tickets y reducción automática de stock.  
- **Servicios:** registro de servicios (vacunas, baños, consultas).  
- **Alertas automáticas:** cron/job que genera alertas por stock bajo o vencimiento próximo.  
- **Agenda y reportes:** páginas preparadas para futuras mejoras.  
- **Autenticación:** login con JWT y roles básicos (admin, cashier, vet).  
- **Frontend moderno:** componentes reutilizables, toasts y UX accesible.

---

## 🛠 Tecnologías
**Frontend**
- React 18 + Vite  
- Tailwind CSS  
- React Router DOM v6  
- React Hook Form (+ Zod opcional)  
- HeadlessUI, Heroicons  
- react-hot-toast  
- Context API / Zustand (opcional)

**Backend**
- Node.js + Express  
- SQLite (better-sqlite3 o sqlite3)  
- Arquitectura modular: routes, controllers, services, repositories  
- node-cron (job de alertas)  
- nodemailer (envío de alertas por email, opcional)

**Herramientas**
- npm / npx  
- Postman (para pruebas API)

---

## ⚙️ Instalación

```bash
# clonar repo
git clone https://github.com/Dilanp10/tuamigofiel.git
cd tuamigofiel
```

### Backend
```bash
cd backend
npm install
# (opcional)
npm install node-cron nodemailer
```

### Frontend
```bash
cd ../frontend
npm install
```

---

## 🔐 Configuración (archivo .env ejemplo)
Crear `.env` en `/backend` (¡no subirlo al repo!):
```
PORT=4000
JWT_SECRET=tu_jwt_secret

ADMIN_USER=admin
ADMIN_PASS_HASH=$2a$...      # hash bcrypt del password admin

ALERT_LOW_STOCK_THRESHOLD=5
ALERT_EXPIRY_DAYS=30
ALERT_CRON="0 8 * * *"
ALERT_TIMEZONE=America/Argentina/Buenos_Aires

ADMIN_EMAIL=tuemail@ejemplo.com
SMTP_HOST=smtp.ejemplo.com
SMTP_PORT=587
SMTP_USER=smtp_user
SMTP_PASS=smtp_pass
```

---

## ▶️ Ejecución (desarrollo)

**Backend (dev)**
```bash
cd backend
npm run dev
# o
node server.js
```

**Frontend (dev)**
```bash
cd frontend
npm run dev
# abrir http://localhost:5173 (o el puerto que indique Vite)
```

**Ejecutar job de alertas manualmente**
```bash
cd backend
node run_alerts.js
```

---

## ⚡ Uso rápido
- Iniciar sesión en la interfaz admin.  
- Ir a **Almacén** para crear/editar/eliminar productos (stock, vencimiento, imagen).  
- Ir a **Ventas** para crear tickets, usar el carrito y confirmar venta (se reduce stock automáticamente).  
- Ir a **Alertas** para ver alertas generadas por el cron o forzar un chequeo.  
- **Reports** y **Agenda** disponibles como secciones (pendientes de completar).

---

## 🧱 Esquema de la base de datos (resumen)

**products**
- `id` INTEGER PK  
- `nombre` TEXT  
- `marca` TEXT  
- `descripcion` TEXT  
- `precio` REAL  
- `cost` REAL  
- `categoria` TEXT  
- `imagen` TEXT  
- `stock` INTEGER  
- `vencimiento` TEXT (YYYY-MM-DD)

**services** — id, nombre, precio, descripcion  
**sales** — venta_id, total, cliente_id (opcional), created_at  
**sale_items** — sale_id, product_id/service_id, qty, unit_price  
**alerts** — id, tipo (stock/vencimiento), message, created_at, resolved  
**users** — id, username, pass_hash, role

---

## 🌐 Endpoints principales (resumen)

> En rutas protegidas enviar header: `Authorization: Bearer <token>`

**Auth**
- `POST /api/login` — login → devuelve `{ token }`

**Products**
- `GET /api/products` — listar  
- `GET /api/products/search?q=` — buscar  
- `POST /api/products` — crear (auth)  
- `PUT /api/products/:id` — actualizar (auth)  

**Services**
- `GET /api/services`  
- `POST /api/services` (auth)

**Sales**
- `POST /api/sales` — crear venta (body: cart)  
- `GET /api/sales` — listar ventas

**Alerts**
- `GET /api/alerts`  
- `POST /api/alerts/check` — forzar chequeo (auth)  
- `PUT /api/alerts/:id/resolve` — marcar resuelta (auth)

---

## 🧪 Pruebas y comandos útiles
- Ejecutar chequeo manual de alertas:
```bash
node backend/run_alerts.js
```
- Consultar últimas alertas con sqlite3:
```bash
sqlite3 backend/data/database.sqlite "SELECT * FROM alerts ORDER BY created_at DESC LIMIT 10;"
```
- Simular venta (POST `/api/sales`) con body:
```json
{
  "cart": [
    { "id": 1, "qty": 2, "precio": 500, "type": "product" }
  ]
}
```

---

## 🛣 Roadmap
- Reportes (ganancia mensual, top productos)  
- Agenda con recordatorios por email/WhatsApp  
- Dashboard en tiempo real (WebSockets)  
- Roles más granulares y permisos por ruta  
- Migración opcional a PostgreSQL con migraciones (Knex/Umzug)

---

## 👨‍💻 Autor
**Dilan Perea** — Full Stack Developer (Node.js, React, SQLite)  
📧 dilanperea.dev@gmail.com  
GitHub: https://github.com/Dilanp10

---

## 🪪 Licencia
Proyecto bajo **MIT License** — podés usarlo, modificarlo y distribuirlo libremente manteniendo el crédito al autor.
