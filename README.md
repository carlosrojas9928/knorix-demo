# KNORIX — Plataforma SaaS de Cursos Online

> Conecta tutores verificados con estudiantes. Sin comisiones — el tutor fija el precio y se queda con todo.

![Status](https://img.shields.io/badge/estado-en%20desarrollo-F59E0B)
![Stack](https://img.shields.io/badge/stack-Next.js%20%7C%20NestJS%20%7C%20PostgreSQL-5B9BD5)
![TypeScript](https://img.shields.io/badge/TypeScript-5.x-3178C6)

---

## ¿Qué es KNORIX?

KNORIX es una plataforma SaaS de cursos online con modelo de suscripción mensual para tutores. A diferencia de plataformas como Udemy (que cobra 50-75% de comisión), en KNORIX el tutor paga una suscripción fija y se queda con el 100% de sus ingresos.

### Características principales

- **Tutores verificados** — revisión manual por admin, badge visible en perfil
- **Preview gratuito** — primeras lecciones accesibles sin pagar
- **Garantía 7 días** — devolución si se solicita dentro del plazo
- **Reseñas verificadas** — solo compradores activos pueden opinar
- **Certificados únicos** — código UUID verificable desde URL pública
- **Progreso automático** — el certificado se genera al completar el 100%

### Planes para tutores

| Plan | Precio/mes | Cursos | Estudiantes | Destacado |
|------|-----------|--------|-------------|-----------|
| Básico | $15.000 COP | 3 | 100 | — |
| Pro ⭐ | $35.000 COP | 10 | 500 | Más popular |
| Ilimitado | $70.000 COP | Ilimitado | Sin límite | — |
| Agencia 🏢 | $120.000 COP | Ilimitado | Sin límite | Multi-tutor |

> **Add-ons premium:** Certificados con marca del tutor · Dominio personalizado · Analíticas avanzadas · Soporte prioritario

---

## Stack Tecnológico

### Frontend
| Tecnología | Versión | Uso |
|-----------|---------|-----|
| Next.js | 14 (App Router) | Framework principal |
| TypeScript | 5.x | Lenguaje |
| Tailwind CSS | v4 | Estilos |
| ShadCN UI | latest | Componentes (Radix + Geist) |
| Zustand | 4.x | Estado global |
| React Hook Form + Zod | latest | Formularios y validación |

### Backend
| Tecnología | Versión | Uso |
|-----------|---------|-----|
| NestJS | 11.x | Framework API REST |
| TypeScript | 5.x | Lenguaje |
| Prisma | 5.x | ORM |
| PostgreSQL | 18.x | Base de datos |
| JWT + Refresh Tokens | — | Autenticación |
| class-validator | — | Validación de DTOs |

### Infraestructura (planificada)
| Tecnología | Uso |
|-----------|-----|
| AWS S3 + CloudFront | Videos y archivos |
| Stripe | Pagos y suscripciones |
| Railway | Deploy del backend |
| Vercel | Deploy del frontend |
| GitHub Actions | CI/CD |

---

## Arquitectura
```
knorix/
├── frontend/          # Next.js 14 — App Router
│   └── src/
│       ├── app/       # Páginas (landing, auth, dashboards, cursos)
│       ├── components/
│       └── lib/
│           └── api.ts # Cliente centralizado para el backend
└── backend/           # NestJS API REST
    └── src/
        ├── auth/      # JWT + Refresh Tokens
        ├── users/     # Perfiles y roles
        ├── courses/   # CRUD de cursos
        ├── lessons/   # Lecciones por curso
        ├── enrollments/ # Inscripciones, progreso y certificados
        └── prisma/    # Servicio de base de datos
```

### Roles del sistema

| Rol | Descripción |
|-----|-------------|
| **Estudiante** | Explora, se inscribe y accede a cursos. Obtiene certificados. |
| **Tutor** | Crea y publica cursos, gestiona lecciones, ve estadísticas. |
| **Admin** | Aprueba tutores, modera contenido, gestiona planes. |

---

## Base de Datos — Modelos Prisma

El schema cuenta con **12 modelos** relacionados:
```
User ─── TutorProfile
  │
  ├── Course ─── Category
  │     │
  │     └── Lesson ─── LessonProgress
  │
  ├── Enrollment ─── LessonProgress
  ├── Review
  ├── Certificate
  ├── Payment
  ├── Subscription
  └── ForumPost
```

---

## Endpoints de la API

### Auth
```
POST /auth/register     Registro de usuario
POST /auth/login        Login → retorna accessToken
POST /auth/refresh      Renovar token
POST /auth/logout       Cerrar sesión
```

### Users
```
GET  /users/me          Perfil del usuario autenticado
PATCH /users/me         Actualizar perfil
GET  /users/:id         Perfil público
```

### Courses
```
GET  /courses           Listar cursos (con filtros)
POST /courses           Crear curso (tutor)
GET  /courses/mine      Mis cursos (tutor autenticado)
GET  /courses/:slug     Detalle de curso
PATCH /courses/:id      Editar curso
DELETE /courses/:id     Eliminar curso
```

### Lessons
```
POST   /courses/:courseId/lessons        Crear lección
GET    /courses/:courseId/lessons        Listar lecciones
PATCH  /courses/:courseId/lessons/:id    Editar lección
DELETE /courses/:courseId/lessons/:id    Eliminar lección
```

### Enrollments
```
POST /enrollments/:courseId                       Inscribirse
GET  /enrollments/me                              Mis inscripciones
POST /enrollments/:courseId/lessons/:id/complete  Completar lección
GET  /enrollments/:courseId/check                 Verificar inscripción
```

> El progreso se calcula automáticamente tras cada lección completada.
> Al llegar al 100% se genera un certificado con código UUID verificable públicamente.

---

## Snippets incluidos

Este repositorio expone fragmentos representativos de cada capa del proyecto.

| Archivo | Qué demuestra |
|---------|---------------|
| `snippets/schema.prisma` | Diseño de BD relacional — 12 modelos, relaciones 1:N y N:M |
| `snippets/auth.service.ts` | Seguridad — JWT, bcrypt, access + refresh tokens |
| `snippets/api.ts` | Integración full stack — cliente centralizado con Bearer token automático |
| `snippets/enrollments.service.ts` | Lógica de negocio — progreso por lección, cálculo automático, certificado al 100% |

---

## Páginas del Frontend

| Ruta | Descripción | Estado |
|------|-------------|--------|
| `/` | Landing page responsive | ✅ |
| `/auth/login` | Login conectado con backend | ✅ |
| `/auth/registro` | Registro en 2 pasos | ✅ |
| `/cursos` | Explorador con filtros | ✅ |
| `/cursos/[slug]` | Detalle de curso | ✅ |
| `/dashboard/estudiante` | Mis cursos y progreso real | ✅ |
| `/dashboard/tutor` | Estadísticas y cursos reales | ✅ |
| `/dashboard/admin` | Métricas y aprobaciones reales | ✅ |
| `/checkout/[courseId]` | Flujo de pago Stripe | ⏳ Mes 7-8 |
| `/certificado/[id]` | Verificación pública de certificado | ⏳ Mes 6-7 |

---

## Estado del Proyecto
```
✅ Mes 1-2   Frontend completo (plantillas + auth + dashboards)
✅ Mes 3-4   Backend completo (Prisma + Auth + Users + Courses)
✅ Mes 4-5   Integración (Lessons + Enrollments + dashboards con datos reales)
⏳ Mes 5-6   Videos con AWS S3 + CloudFront
⏳ Mes 6-7   Reseñas + foro + certificados PDF
⏳ Mes 7-8   Pagos con Stripe
⏳ Mes 8-9   Deploy + CI/CD
⏳ Mes 9-12  Beta + lanzamiento
```

---

## Autor

**Carlos Esteban Rojas Ibarra**  
carlosrojas9928@gmail.com  
[LinkedIn](https://www.linkedin.com/in/carlos-esteban-rojas/) · [GitHub](https://github.com/carlosrojas9928)

---

> Este repositorio contiene fragmentos representativos del proyecto. El código fuente completo es privado.