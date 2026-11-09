# NOCTURNA — Especificación inicial para Codex
Fecha: 2026-11-08
Estado: planificación; decisiones pendientes marcadas como tales.

## Visión
E-commerce de productos para adultos con catálogo público y administración interna. Estética minimalista nocturna premium, negro/ciruela/vino/malva, responsive, accesible y discreta.

## Stack obligatorio
- React + TypeScript + Vite + Tailwind CSS.
- Java + Spring Boot + Spring Security; API REST.
- PostgreSQL + Flyway para migraciones.
- Docker y Docker Compose para desarrollo/despliegue.
- Git/GitHub, pruebas Vitest/React Testing Library y JUnit/Testcontainers.

## Arquitectura
Monolito modular Spring Boot; frontend React único con layouts público y administrativo, separados por rutas. La seguridad se aplica en el backend, no por ocultamiento de rutas. Contenedores separados para frontend, API y PostgreSQL. Configuración por variables de entorno, sin secretos versionados.

## Rutas previstas
Públicas: /, /catalogo, /categoria/:slug, /producto/:slug, /carrito, /checkout, /mi-cuenta.
Internas: /admin/login, /admin, /admin/productos, /admin/categorias, /admin/inventario, /admin/pedidos, /admin/clientes, /admin/finanzas, /admin/usuarios, /admin/auditoria, /admin/configuracion.

## Roles propuestos
- ADMIN: configuración, usuarios, auditoría, gestión total.
- CONTADOR: informes, costos, gastos, conciliación y finanzas.
- BODEGA: recepción, preparación, movimientos de inventario.
- PEDIDOS (propuesto): seguimiento y gestión comercial de pedidos.
Definir permisos granulares por recurso y acción (RBAC), mínimo privilegio y segregación de funciones. MFA TOTP obligatorio para todo usuario interno, códigos de recuperación de un uso, revocación de sesiones, limitación de intentos, auditoría de accesos. Las contraseñas se almacenan con hash robusto; proteger los secretos MFA cifrados.

## Tienda pública
Navbar, hero editorial, destacados, categorías en cards, novedades, promociones, beneficios de privacidad, footer con acceso administrativo discreto. Catálogo con búsqueda, filtros, ordenamiento, paginación; ficha de producto con imágenes, variantes, disponibilidad, precio; carrito y checkout; confirmaciones y seguimiento de pedidos. Diseño mobile-first.

## Panel interno
Dashboard de ventas, pedidos, productos, inventario y KPIs; CRUD de productos/categorías; imágenes y variantes; precios/costos; estados borrador/publicado/archivado; stock; pedidos; clientes; promociones; finanzas; informes; usuarios; auditoría; configuración.

## Entidades candidatas
User, Role, Permission, Customer, Address, Category, Product, ProductVariant, ProductImage, InventoryLocation, InventoryMovement, StockReservation, Cart, CartItem, Order, OrderItem, Payment, Refund, Shipment, Discount, Expense, AuditEvent.
Definir modelo lógico antes de migraciones. Precios monetarios con BigDecimal en Java y NUMERIC en PostgreSQL, nunca float.

## Reglas de negocio esenciales
- Publicar producto desde admin lo muestra en catálogo si cumple validaciones.
- No permitir sobreventa: reservar stock de forma transaccional y liberar al vencer/cancelar pedidos.
- Pedido, pago, despacho, devolución y reembolso son estados/eventos diferentes.
- Webhooks de pagos firmados y operaciones idempotentes; no confiar en redirección del navegador como prueba de pago.
- No borrar movimientos financieros o de inventario: reversar con trazabilidad.
- Ingresos, costos, margen bruto, gastos, devoluciones y utilidad neta son métricas distintas.
- Auditoría de cambios sensibles: precios, stock, roles, pagos, reembolsos.
- Datos sensibles del cliente con retención mínima, controles de acceso y comunicaciones discretas.

## Seguridad y cumplimiento
HTTPS, control de acceso backend, cookies seguras o estrategia de sesión explícita, CSRF cuando aplique, validación de entradas, consultas parametrizadas, límites de tasa, subida segura de imágenes, backups probados, logging sin secretos. Verificar restricciones de pasarela de pagos y proveedores logísticos para productos adultos; políticas de mayoría de edad, protección de datos (Colombia Ley 1581), consumidor (Ley 1480), facturación e impuestos según el negocio. No asumir que una simple pantalla de confirmación de edad resuelve obligaciones legales.

## Diseño
Colores iniciales: #101014, #211821, #542D49, #B68AA8, #EAE4E8. Tipografía moderna, espacios amplios, contrastes accesibles, animaciones discretas, componentes reutilizables, diseño responsive. Admin más denso y funcional que la tienda pública.

## Organización sugerida
frontend/src/{app,routes,features,components,layouts,styles,lib}; backend/src/main/java/.../{auth,catalog,inventory,orders,payments,finance,users,audit,config}; infra/; docs/; .github/workflows/.

## Roadmap
0. Descubrimiento, alcance, UX, ERD y contratos API.
1. Monorepo, Docker Compose, Spring Boot, React, PostgreSQL, migraciones, CI.
2. Auth/RBAC/MFA y auditoría mínima.
3. Catálogo público y CRUD administrativo de productos/categorías/imágenes.
4. Inventario y reservas de stock.
5. Carrito, checkout, pedidos y estados.
6. Pasarela de pagos aprobada, webhooks, reembolsos, envíos.
7. Finanzas, reportes y dashboards.
8. Pruebas de seguridad, rendimiento, accesibilidad, backups, despliegue.
Priorizar MVP vertical funcional antes de ampliar módulos.

## Decisiones por confirmar
- Inventario propio, dropshipping o mixto.
- Checkout online, WhatsApp o ambos; pasarela y medios de pago admitidos.
- Cobertura geográfica, entregas y logística discreta.
- Registro de clientes obligatorio u opcional; guest checkout.
- Política de verificación de mayoría de edad.
- Facturación, impuestos, devoluciones, garantías y políticas de privacidad.
- Hosting, dominio, almacenamiento de imágenes, correo transaccional y backups.
- Rol PEDIDOS y matriz exacta de permisos.

## Instrucciones iniciales para Codex
1. Leer este documento y proponer estructura, ADRs y plan incremental; no crear funcionalidades comerciales aún.
2. Implementar scaffolding mínimo del monorepo, Docker Compose, frontend React/TS/Vite/Tailwind, backend Spring Boot y PostgreSQL/Flyway.
3. Agregar README con comandos reproducibles, .env.example sin secretos, health checks, lint, tests y CI.
4. No introducir credenciales ni datos reales en Git. No afirmar que pruebas pasan sin ejecutarlas.
5. Mantener cambios pequeños, revisables y probados. Solicitar aprobación antes de decisiones irreversibles o servicios de pago.
