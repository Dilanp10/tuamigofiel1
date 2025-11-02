🐾 TuAmigoFiel — Sistema de Gestión para Veterinarias

TuAmigoFiel es una aplicación Full Stack para la gestión integral de una veterinaria.
Permite administrar productos, servicios, ventas (POS), alertas de stock y vencimiento, agenda y reportes.
El proyecto está dividido en frontend (React) y backend (Node.js + Express + SQLite), con autenticación basada en JWT.

🚀 Visión general

TuAmigoFiel centraliza las operaciones diarias de una veterinaria o pet shop:

🧾 Gestión de productos: stock, precios, vencimientos, categorías.

💰 Ventas (POS): creación de tickets, carrito de compra, reducción automática de stock.

🧼 Servicios: registro y control de precios.

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
1. Instalar dependencias

Backend:

cd backend
npm install
npm install node-cron nodemailer


Frontend:

cd frontend
npm install

2. Variables de entorno

Crear un archivo .env dentro de /backend con el siguiente contenido:

PORT=4000
JWT_SECRET=tu_jwt_secret

ADMIN_USER=admin
ADMIN_PASS_HASH=$2a$...      # Hash generado con bcryptjs

ALERT_LOW_STOCK_THRESHOLD=5
ALERT_EXPIRY_DAYS=30
ALERT_CRON=0 8 * * *
ALERT_TIMEZONE=America/Argentina/Buenos_Aires

ADMIN_EMAIL=tuemail@ejemplo.com
SMTP_HOST=smtp.tu-servidor.com
SMTP_PORT=587
SMTP_USER=usuario_smtp
SMTP_PASS=contraseña_smtp


⚠️ No subas este archivo al repositorio público.

3. Ejecutar el proyecto

Backend (modo desarrollo):

npm run dev


Ejecutar job de alertas manualmente:

node run_alerts.js


Frontend (modo desarrollo):

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
cost	REAL	Costo (opcional)
categoria	TEXT	Categoría
imagen	TEXT	URL o ruta
stock	INTEGER	Cantidad disponible
vencimiento	TEXT	Fecha ISO (YYYY-MM-DD)
Tabla services

Registra servicios ofrecidos (baños, vacunas, etc.)

Tabla sales y sale_items

Control de ventas y detalle de productos/servicios vendidos.

Tabla alerts

Registra alertas automáticas generadas por stock bajo o vencimiento próximo.

Tabla users

Maneja usuarios del sistema y roles.

🌐 Endpoints principales
🔑 Autenticación

POST /api/login
Body: { "user": "admin", "pass": "..." }
Response: { "token": "JWT..." }

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
Método	Ruta	Descripción
POST	/api/sales	Crear venta y reducir stock
GET	/api/sales	Listar ventas
⚠️ Alertas
Método	Ruta	Descripción
GET	/api/alerts	Listar alertas
POST	/api/alerts/check	Forzar chequeo
PUT	/api/alerts/:id/resolve	Marcar resuelta

Autenticación: enviar header Authorization: Bearer <token>.

🧭 Frontend — Páginas principales

LoginPage.jsx: Inicio de sesión administrador.

AdminPage.jsx: Dashboard principal.

AlmacenPage.jsx: Listado de productos (cards + modal de edición).

VentasPage.jsx: Punto de venta con búsqueda y carrito.

AlertsPage.jsx: Visualización y resolución de alertas.

ReportsPage.jsx: (pendiente) Gráficos y métricas de ventas.

🧪 Cómo probar funciones clave
Alertas

Crear un producto con bajo stock o vencimiento próximo.

Ejecutar:

node run_alerts.js


Verificar resultados:

sqlite3 data/database.sqlite "SELECT * FROM alerts ORDER BY created_at DESC LIMIT 5;"

Ventas

Enviar POST /api/sales con:

{
  "cart": [
    { "id": 1, "qty": 2, "precio": 500, "type": "product" }
  ]
}


→ Se genera una venta y se descuenta el stock automáticamente.

🧩 Buenas prácticas

Mantener .env fuera del control de versiones.

Usar migraciones (Knex, Umzug) si se escala a PostgreSQL.

Realizar backups periódicos de la base SQLite.

Implementar roles y middlewares (admin, cashier, vet).

Agregar tests para endpoints críticos (sales, stock changes).

📋 Próximas mejoras

Módulo de reportes (ganancia mensual, top productos).

Agenda de turnos y recordatorios automáticos.

Dashboard con gráficos en tiempo real.

Multiusuario y roles diferenciados.

Notificaciones vía correo para alertas críticas.

👨‍💻 Autor

Desarrollador: Dilan Perea

📧 Contacto: dilanperea.dev@gmail.com

💼 Rol: Full Stack Developer — Node.js, React, SQLite

🪪 Licencia

Este proyecto está bajo la licencia MIT.
Podés usarlo, modificarlo y distribuirlo libremente, manteniendo el crédito al autor
