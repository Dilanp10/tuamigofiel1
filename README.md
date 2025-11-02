🐾 TuAmigoFiel — Sistema de Gestión para Veterinarias

TuAmigoFiel es una aplicación Full Stack para la gestión integral de una veterinaria / pet shop.
Permite administrar productos, servicios, ventas (POS), alertas de stock y vencimiento, agenda y reportes.
Frontend: React. Backend: Node.js + Express + SQLite. Autenticación por JWT.

🚀 Visión general

TuAmigoFiel centraliza las operaciones diarias de una veterinaria:

🧾 Gestión de productos: stock, precios, vencimientos, categorías.

💰 Ventas (POS): creación de tickets, carrito y reducción automática de stock.

🧼 Servicios: registro y control de precios (baños, vacunas, consultas, etc.).

⚠️ Alertas automáticas: por bajo stock o vencimiento próximo.

📅 Agenda y reportes: planificados para futuras versiones.

🔐 Autenticación JWT: acceso seguro al panel administrativo.

🗂️ Estructura del repositorio
tuamigofiel1/
│
├── backend/
│   ├── app.js / server.js        # Arranque del servidor y montaje de rutas
│   ├── package.json
│   ├── config/
│   │   └── db.js                 # Helpers para SQLite (all, get, run)
│   ├── controllers/              # Lógica de endpoints
│   │   ├── productosController.js
│   │   ├── servicesController.js
│   │   ├── salesController.js
│   │   └── alertsController.js
│   ├── routes/
│   │   ├── productos.js
│   │   ├── services.js
│   │   ├── sales.js
│   │   └── alerts.js
│   ├── services/                 # Lógica de negocio
│   ├── jobs/
│   │   └── alertsJob.js          # Cron que genera alertas automáticas
│   ├── data/
│   │   └── database.sqlite       # Base de datos SQLite
│   └── run_alerts.js             # Script manual de prueba de alertas
│
├── frontend/
│   └── src/
│       ├── pages/
│       │   ├── LoginPage.jsx
│       │   ├── AdminPage.jsx
│       │   ├── AlmacenPage.jsx
│       │   ├── VentasPage.jsx
│       │   ├── AlertsPage.jsx
│       │   └── ReportsPage.jsx
│       ├── component/
│       │   ├── NavbarAdmin.jsx
│       │   └── ProductModal.jsx
│       └── lib/
│           └── axios.js           # Instancia con baseURL y token
│
└── txt/                          # Documentos y notas técnicas

⚙️ Configuración y ejecución
1) Instalar dependencias

Backend

cd backend
npm install
npm install node-cron nodemailer


Frontend

cd frontend
npm install

2) Variables de entorno

Crear un archivo .env dentro de /backend (no subir al repo). Ejemplo:

PORT=4000
JWT_SECRET=tu_jwt_secret

ADMIN_USER=admin
ADMIN_PASS_HASH=$2a$...      # Hash generado con bcryptjs

ALERT_LOW_STOCK_THRESHOLD=5
ALERT_EXPIRY_DAYS=30
ALERT_CRON="0 8 * * *"
ALERT_TIMEZONE=America/Argentina/Buenos_Aires

ADMIN_EMAIL=tuemail@ejemplo.com
SMTP_HOST=smtp.tu-servidor.com
SMTP_PORT=587
SMTP_USER=usuario_smtp
SMTP_PASS=contraseña_smtp


⚠️ No subas este archivo al repositorio público.

3) Ejecutar el proyecto

Backend (dev)

cd backend
npm run dev


Ejecutar job de alertas manualmente:

node run_alerts.js


Frontend (dev)

cd frontend
npm run dev

🧱 Esquema de Base de Datos (SQLite)
Tabla products
Campo	Tipo	Descripción
id	INTEGER	PK
nombre	TEXT	Nombre del producto
marca	TEXT	Marca
descripcion	TEXT	Descripción
precio	REAL	Precio de venta
cost	REAL	Costo (opcional, para ganancias)
categoria	TEXT	Categoría
imagen	TEXT	URL o ruta de imagen
stock	INTEGER	Cantidad disponible
vencimiento	TEXT	Fecha ISO (YYYY-MM-DD)

Otras tablas:

services — servicios ofrecidos (baños, vacunas, consultas, etc.).

sales y sale_items — control de ventas y detalle (qty, unit_price).

alerts — alertas automáticas (stock bajo / vencimiento próximo).

users — usuarios, passwords hasheados y roles (admin, cashier, vet, ...).

🌐 Endpoints principales

En endpoints protegidos enviar header:

Authorization: Bearer <token>

🔑 Autenticación

POST /api/login
Body:

{ "user": "admin", "pass": "..." }


Response:

{ "token": "JWT..." }

📦 Productos
Método	Ruta	Descripción
GET	/api/products	Listar productos
GET	/api/products/search?q=	Buscar productos
POST	/api/products	Crear producto (auth)
PUT	/api/products/:id	Actualizar producto (auth)
🧼 Servicios
Método	Ruta	Descripción
GET	/api/services	Listar servicios
POST	/api/services	Crear servicio (auth)
💰 Ventas

POST /api/sales — Crear venta y reducir stock (auth)
GET /api/sales — Listar ventas (filtros opcionales)

Ejemplo body para /api/sales:

{
  "cart": [
    { "id": 1, "qty": 2, "precio": 500, "type": "product" }
  ]
}

⚠️ Alertas
Método	Ruta	Descripción
GET	/api/alerts	Listar alertas (auth)
POST	/api/alerts/check	Forzar chequeo (auth, opcional)
PUT	/api/alerts/:id/resolve	Marcar alerta como resuelta
🧪 Cómo probar funciones clave

Alertas

Crear un producto con bajo stock o vencimiento próximo.

Ejecutar manualmente:

node run_alerts.js


Verificar en SQLite:

sqlite3 backend/data/database.sqlite "SELECT * FROM alerts ORDER BY created_at DESC LIMIT 5;"


Ventas
Enviar POST /api/sales con el body de ejemplo → se genera la venta y se descuenta stock automáticamente.

🧩 Buenas prácticas

Mantener .env fuera del control de versiones (.gitignore).

Usar migraciones (Knex, Umzug) si migrás a PostgreSQL.

Realizar backups periódicos del archivo SQLite.

Implementar roles y middlewares (admin, cashier, vet).

Agregar tests para endpoints críticos (sales, stock changes).

📋 Roadmap (próximas mejoras)

Módulo de reportes (ganancia mensual, top productos).

Agenda de turnos y recordatorios automáticos.

Dashboard con gráficos en tiempo real.

Multiusuario y roles diferenciados.

Notificaciones por correo para alertas críticas.

👨‍💻 Autor

Dilan Perea — Full Stack Developer (Node.js, React, SQLite)
📧 dilanperea.dev@gmail.com

GitHub: https://github.com/Dilanp10

🪪 Licencia

Este proyecto está bajo la licencia MIT. Podés usarlo, modificarlo y distribuirlo libremente, manteniendo el crédito al autor.
