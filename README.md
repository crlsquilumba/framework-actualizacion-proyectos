# Framework de Actualización de Proyectos

> Sistema de auditoría y actualización de proyectos existentes

---

## ¿Qué es este framework?

Un **sistema de auditoría integral** que analiza proyectos existentes, identifica qué funciona y qué no, detecta código duplicado, y prepara el proyecto para producción.

---

## Flujo de Auditoría

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    FASE 1: AUDITORÍA COMPLETA                               │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  1. ANALISIS      2. ESCANEO     3. EVALUACIÓN   4. LIMPIEZA    5. PRUEBAS│
│  ┌───────────┐   ┌───────────┐  ┌───────────┐   ┌───────────┐  ┌────────┐ │
│  │ Qué hace? │ → │ Código    │ → │ Qué       │ → │ Eliminar  │ → │ Tests  │ │
│  │ Tech      │   │ duplicado?│   │ funciona? │   │ redundancia│ │ Funcion│ │
│  └───────────┘   └───────────┘  └───────────┘   └───────────┘  └────────┘ │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Skills Disponibles

| Skill | Función |
|-------|---------|
| **Auditor** | Analiza el proyecto, detecta tecnologías, qué funciona/no funciona |
| **Architect Audit** | Evalúa stack tecnológico, nginx, rendimiento |
| **Dev Cleanup** | Limpia código duplicado, optimiza, arregla parpadeo |
| **QA Verify** | Pruebas de frontend, backend, datos, responsive |
| **DevOps Audit** | Configura nginx, prepara deployment |

---

## Quick Start

```bash
# 1. Auditoría inicial
claude --skill auditor "Audita mi proyecto existente"

# 2. Análisis técnico
claude --skill architect-audit "Analiza las tecnologías"

# 3. Limpieza de código
claude --skill dev-cleanup "Limpia código duplicado"

# 4. Verificación
claude --skill qa-verify "Ejecuta pruebas de funcionamiento"

# 5. nginx + deployment
claude --skill devops-audit "Configura nginx para producción"
```

---

## Pasos Detallados

### 1. Auditor (Análisis)
- Detecta estructura del proyecto
- Identifica tecnologías (Frontend, Backend, BD)
- Presenta resumen del proyecto
- Lista qué funciona y qué no

### 2. Architect Audit (Evaluación)
- Evalúa cada tecnología (¿necesaria o redundante?)
- Revisa nginx (APIs expuestas correctamente)
- Analiza rendimiento
- Diagnostica problema de parpadeo

### 3. Dev Cleanup (Limpieza)
- Elimina código muerto
- Extrae componentes repetidos a shared/
- Optimiza rendimiento
- Implementa skeleton loaders para evitar parpadeo

### 4. QA Verify (Pruebas)
- Smoke tests de frontend
- Health check de backend
- Verificación de queries
- Pruebas responsive (mobile/tablet/desktop)

### 5. DevOps Audit (Deployment)
- Configura nginx como reverse proxy
- Expone APIs correctamente
- Agrega seguridad (headers, gzip)
- Prepara para producción

---

## Problemas Comunes a Resolver

| Problema | Skill | Solución |
|----------|-------|----------|
| Página parpadea | Dev Cleanup | Skeleton loaders + Suspense |
| Código duplicado | Dev Cleanup | Extraer a shared/ |
| APIs no expuestas | DevOps Audit | Configurar nginx proxy_pass |
| No responsive | QA Verify | Media queries |
| Tecnologías redundantes | Architect Audit | Eliminar dependencias |
| Sin loading states | Dev Cleanup | Agregar skeleton |

---

## Tecnologías Soportadas

### Frontend
- React, Vue, Angular
- TypeScript, JavaScript
- Tailwind, CSS Modules
- Vite, Webpack

### Backend
- Java Spring
- .NET
- Node.js
- Python

### Base de Datos
- MySQL, PostgreSQL, SQL Server
- MongoDB

### Infraestructura
- nginx
- Docker (opcional)

---

## Estructura del Proyecto Auditado

```
proyecto-auditar/
├── FRONTEND/           # Código frontend
├── BACKEND/            # Código backend (Java Spring)
├── nginx/              # Configuración nginx
├── DATABASE/           # Scripts de BD
├── DOCUMENTOS/         # Documentación
└── INFRA/             # Infraestructura
```

---

## Ejemplo de Auditoría

```
> Usuario: Audita mi proyecto

> Auditor Skill:
Analizando proyecto...

📋 RESUMEN
- Proyecto: E-Commerce Librería
- Tecnologías: React + Java Spring + MySQL + nginx

✅ QUÉ FUNCIONA
- Catálogo de libros
- Carrito de compras
- Checkout

❌ QUÉ NO FUNCIONA
- Reseñas de productos
- Programa de puntos

🔍 CÓDIGO DUPLICADO
- FormatDate en 3 componentes

> Architect Audit Skill:
- Redux → REDUNDANTE, migrar a Zustand
- jQuery → NO NECESARIO, eliminar
- nginx → FALTA configurar /api/*

> Dev Cleanup Skill:
- [✓] Código duplicado extraído a shared/utils
- [✓] Parpadeo arreglado con skeleton loaders

> QA Verify Skill:
- Frontend: ✅
- Backend: ✅
- Responsive: ✅

> DevOps Audit Skill:
- nginx configurado correctamente
- APIs expuestas en /api/*
```

---

## Requisitos

- Node.js 18+
- Java 17+ (si el proyecto es Spring)
- nginx
- Git

---

*Framework creado para auditar y actualizar proyectos existentes*
