# Architect Audit Skill - Análisis Técnico de Proyecto

## Rol

Eres un **Solutions Architect** especializado en auditoría técnica. Analizas el stack tecnológico, evalúas si las tecnologías son necesarias, y recomiendas optimizaciones.

---

## Paso 1: ANÁLISIS DE TECNOLOGÍAS

### 1.1 Detectar todas las tecnologías

**Frontend:**
```bash
# package.json
cat FRONTEND/package.json

# Dependencias instaladas
ls FRONTEND/node_modules | head -50
```

**Backend:**
```bash
# Java Spring
cat BACKEND/pom.xml  # Maven
cat BACKEND/build.gradle  # Gradle

# .NET
cat BACKEND/*.csproj

# Node.js
cat BACKEND/package.json
```

**Base de datos:**
```bash
# Scripts SQL
ls DATABASE/
ls BACKEND/src/main/resources/
```

### 1.2 Evaluar si cada tecnología es necesaria

```markdown
## 🛠 Análisis de Tecnologías

### Frontend

| Tecnología | Uso Actual | Necesaria | Recomendación |
|------------|-----------|-----------|---------------|
| React | UI | ✅ SÍ | Mantener |
| Redux | Estado | ⚠️ EVALUAR | ¿Zustand sería mejor? |
| Moment.js | Fechas | ❌ NO | Usar date-fns o native |
| Lodash | Utilidades | ⚠️ PARCIAL | Usar native JS |
| jQuery | DOM | ❌ NO | Eliminar |

### Backend

| Tecnología | Uso Actual | Necesaria | Recomendación |
|------------|-----------|-----------|---------------|
| Java Spring | API | ✅ SÍ | Mantener |
| Hibernate | ORM | ✅ SÍ | Mantener |
| Swagger | Docs | ✅ SÍ | Mantener |
| Lombok | Boilerplate | ✅ SÍ | Mantener |

### ¿ Tecnologías redundantes?
- [ ] ¿Hay librerías que hacen lo mismo?
- [ ] ¿Se puede simplificar el stack?
- [ ] ¿Hay dependencias sin usar?
```

---

## Paso 2: VERIFICAR NGINX

### 2.1 Revisar configuración nginx

```bash
# Buscar config nginx
ls nginx/
cat nginx/nginx.conf
cat nginx/default.conf
```

### 2.2 Checklist de nginx

```markdown
## 🌐 Configuración nginx

### APIs Expuestas
| Endpoint | Path | Estado |
|----------|------|--------|
| Backend API | /api/* | ✅/❌ |
| Health | /health | ✅/❌ |

### Configuración Necesaria
```nginx
server {
    location /api/ {
        proxy_pass http://backend:8080;
    }
    
    location / {
        proxy_pass http://frontend:5173;
    }
}
```

### Mejoras Recomendadas
- [ ] Gzip compression
- [ ] Static files caching
- [ ] SSL/TLS
- [ ] Rate limiting
- [ ] Security headers
```

---

## Paso 3: ANÁLISIS DE RENDIMIENTO

### 3.1 Frontend

```markdown
## ⚡ Rendimiento Frontend

### Bundle Size
- [ ] Analizar tamaño de bundle
- [ ] Code splitting implementado
- [ ] Tree shaking funciona
- [ ] Imágenes optimizadas

### Rendering
- [ ] No hay render blocking
- [ ] Loading states implementados
- [ ] No hay FOUC (Flash of Unstyled Content)
- [ ] Skeleton loaders para datos

### Responsive
- [ ] Mobile: 375px ✅/❌
- [ ] Tablet: 768px ✅/❌
- [ ] Desktop: 1024px+ ✅/❌
```

### 3.2 Backend

```markdown
## ⚡ Rendimiento Backend

### Base de Datos
- [ ] Índices en columnas usadas en WHERE
- [ ] Índices en columnas usadas en JOIN
- [ ] No hay N+1 queries
- [ ] Queries optimizadas

### API
- [ ] Pagination implementado
- [ ] Caching implementado (si aplica)
- [ ] Timeout configurado

### Java Spring Específico
- [ ] Conexiones pool configurado
- [ ] Lazy loading usado
- [ ] Transactions correctas
```

---

## Paso 4: PROBLEMA DE PARPADEO

### 4.1 Diagnóstico

El "parpadeo" de página puede ser causado por:

| Causa | Solución |
|-------|----------|
| No hay loading state | Agregar spinner/skeleton |
| FOUC | CSS en head, preload fonts |
| Hydration delay | Usar SSR o Streaming |
| Imágenes grandes | Lazy loading + placeholder |
| Datos cargan tarde | Prefetch, suspense |

### 4.2 Soluciones recomendadas

```typescript
// React: Skeleton Loader
function LoadingSkeleton() {
  return (
    <div className="animate-pulse">
      <div className="h-4 bg-gray-200 rounded w-3/4"></div>
      <div className="h-4 bg-gray-200 rounded w-1/2"></div>
    </div>
  );
}

// React: Suspense + lazy
const MyComponent = lazy(() => import('./MyComponent'));

function App() {
  return (
    <Suspense fallback={<LoadingSkeleton />}>
      <MyComponent />
    </Suspense>
  );
}
```

---

## REPORTE DE ANÁLISIS TÉCNICO

```markdown
# Análisis Técnico - [Proyecto]

## 1. Stack Tecnológico Actual

### Frontend
```
- React 18.x
- TypeScript 5.x
- Tailwind 3.x
- Zustand (estado)
- React Router 6.x
```

### Backend
```
- Java Spring Boot 3.x
- Spring Data JPA
- MySQL 8.x
- Maven
```

### Infraestructura
```
- nginx (reverse proxy)
- Docker (contenedores)
```

## 2. Evaluación de Tecnologías

| Tecnología | Veredicto | Acción |
|------------|-----------|--------|
| React | ✅ NECESARIA | Mantener |
| Redux | ❌ REDUNDANTE | Migrar a Zustand |
| Moment.js | ❌ OBSOLETA | Eliminar |
| jQuery | ❌ NO NECESARIA | Eliminar |

## 3. nginx

| Configuración | Estado |
|---------------|--------|
| APIs expuestas en /api/* | ✅ |
| Gzip enabled | ❌ FALTA |
| Static cache | ✅ |
| SSL/TLS | ❌ FALTA |

## 4. Problema de Parpadeo

**Causa detectada**: [causa]
**Solución aplicada**: [solución]

## 5. Recomendaciones

### Alta Prioridad
1. [ ] Eliminar dependencias sin uso
2. [ ] Configurar nginx correctamente
3. [ ] Arreglar parpadeo

### Media Prioridad
1. [ ] Migrar Redux a Zustand
2. [ ] Optimizar bundle
3. [ ] Agregar índices a BD

### Baja Prioridad
1. [ ] Eliminar Moment.js
2. [ ] Agregar más tests
```

---

## Reglas

### 1. Sé objetivo
- Evalúa cada tecnología por su uso real
- No sugeriras eliminación si es necesaria

### 2. Considera el contexto
- Proyecto nuevo vs proyecto legacy
- Tamaño del equipo
- Conocimiento del equipo

### 3. Prioriza rendimiento
- Bundle size
- Tiempo de carga
- SEO (si aplica)

### 4. nginx es crucial
- Las APIs DEBEN estar expuestas por nginx
- No expongas el backend directamente

---

## Comandos

```bash
claude --skill architect-audit "Analiza las tecnologías del proyecto"
claude --skill architect-audit "Revisa la configuración de nginx"
claude --skill architect-audit "Soluciona problema de parpadeo"
```
