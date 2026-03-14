# Dev Cleanup Skill - Limpieza y Optimización

## Rol

Eres un **Developer** especializado en limpieza de código. Tomas el código existente, eliminas redundancias, optimizas y dejas listo para producción.

---

## FLUJO DE LIMPIEZA

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         FLUJO DE LIMPIEZA                                    │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  1. ELIMINAR     2. EXTRAER      3. OPTIMIZAR   4. ARREGLAR       5. TEST │
│  ┌───────────┐   ┌───────────┐   ┌───────────┐  ┌───────────┐  ┌───────┐ │
│  │ Código    │ → │ Componentes│ → │ Rendimien-│ → │ Problemas │ → │ Verify│ │
│  │ muerto    │   │ repetidos  │   │ to        │   │ (parpadeo)│  │       │ │
│  └───────────┘   └───────────┘   └───────────┘  └───────────┘  └───────┘ │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Paso 1: ELIMINAR CÓDIGO MUERTO

### 1.1 Frontend

```bash
# Encontrar imports sin usar
cd FRONTEND
npx eslint --no-eslintrc --parser @typescript-eslint/parser --plugin @typescript-eslint --rule '{"@typescript-eslint/no-unused-vars": "error"}' src/

# Encontrar código sin usar
grep -r "export " src/ --include="*.ts" --include="*.tsx" | grep -v "import"
```

### 1.2 Backend (Java Spring)

```bash
# Encontrar métodos sin usar
cd BACKEND
grep -r "public\|private\|protected" src/main/java --include="*.java" | grep -v "test"

# Dependencias sin uso en pom.xml
mvn dependency:analyze
```

### 1.3 Checklist de eliminación

- [ ] Imports sin usar eliminados
- [ ] Funciones sin usar eliminadas
- [ ] Componentes obsoletos eliminados
- [ ] Estilos CSS sin usar eliminados
- [ ] Imágenes/assets sin usar eliminados
- [ ] Dependencias sin usar eliminadas

---

## Paso 2: EXTRAER COMPONENTES REPETIDOS

### 2.1 Identificar duplicados

```bash
# Frontend: buscar código similar
find FRONTEND/src -name "*.tsx" -exec wc -l {} \; | sort -rn | head -20

# Buscar funciones duplicadas
grep -r "function\|const.*=.*(" FRONTEND/src --include="*.ts" | sort | uniq -d
```

### 2.2 Estructura de componentes compartidos

```
FRONTEND/src/
├── shared/
│   ├── components/
│   │   ├── UI/           # Button, Input, Modal, etc.
│   │   ├── Layout/       # Header, Footer, etc.
│   │   └── Common/       # Cards, Lists, etc.
│   ├── hooks/            # useAuth, useCart, etc.
│   ├── utils/           # Funciones helper
│   └── types/           # Tipos compartidos
```

### 2.3 Extraer a shared/

```typescript
// ANTES: código duplicado en múltiples componentes
// ComponentA.tsx
const formatDate = (date) => {
  return new Date(date).toLocaleDateString('es-ES');
}

// ComponentB.tsx
const formatDate = (date) => {
  return new Date(date).toLocaleDateString('es-ES');
}

// DESPUÉS: extraer a shared/utils/dateUtils.ts
// shared/utils/dateUtils.ts
export const formatDate = (date: Date): string => {
  return new Date(date).toLocaleDateString('es-ES');
}

// Usar en ambos componentes
import { formatDate } from '@/shared/utils/dateUtils';
```

---

## Paso 3: OPTIMIZAR RENDIMIENTO

### 3.1 Frontend

**Bundle size:**
```bash
# Analizar bundle
cd FRONTEND
npm run build
npm run build -- --analyze
```

**Optimizaciones:**
- [ ] Code splitting por rutas
- [ ] Lazy loading de componentes
- [ ] Imágenes optimizadas (WebP, lazy load)
- [ ] CSS purgado (Tailwind)
- [ ] Imports tree-shakeables

**React específico:**
```typescript
// Lazy loading
const LazyComponent = lazy(() => import('./HeavyComponent'));

// Memoización
const MemoizedComponent = React.memo(({ data }) => (
  <div>{data.name}</div>
));

// useMemo y useCallback
const expensiveValue = useMemo(() => computeExpensive(a, b), [a, b]);
const handleClick = useCallback(() => doSomething(id), [id]);
```

### 3.2 Backend (Java Spring)

**Base de datos:**
```java
// ANTES: N+1 query
List<User> users = userRepository.findAll();
for (User user : users) {
    System.out.println(user.getOrders().size()); // Nueva query por cada usuario
}

// DESPUÉS: Eager fetch con join
@Query("SELECT u FROM User u LEFT JOIN FETCH u.orders")
List<User> findAllWithOrders();
```

**Optimizaciones:**
- [ ] Índices en columnas frecuentes
- [ ] Pagination en listas grandes
- [ ] Cache con @Cacheable
- [ ] Conexiones pool configuradas

---

## Paso 4: ARREGLAR PARPADEO

### 4.1 Solución: Loading States

```typescript
// Skeleton Loader
function BookCardSkeleton() {
  return (
    <div className="animate-pulse">
      <div className="bg-gray-200 h-48 w-full rounded"></div>
      <div className="mt-4 h-4 bg-gray-200 rounded w-3/4"></div>
      <div className="mt-2 h-4 bg-gray-200 rounded w-1/2"></div>
    </div>
  );
}
```

### 4.2 Solución: Suspense

```typescript
import { Suspense, lazy } from 'react';

// Carga lazy
const BookDetail = lazy(() => import('./pages/BookDetail'));

function App() {
  return (
    <Suspense fallback={<BookCardSkeleton />}>
      <Routes>
        <Route path="/book/:id" element={<BookDetail />} />
      </Routes>
    </Suspense>
  );
}
```

### 4.3 Solución: Preloading

```typescript
// Preload datos antes de navegar
import { useNavigate } from 'react-router-dom';

function BookCard({ book }) {
  const navigate = useNavigate();
  
  const handleClick = () => {
    // Fetch datos antes de navegar
    prefetchBookDetails(book.id);
    navigate(`/catalog/${book.id}`);
  };
  
  return <div onClick={handleClick}>{book.title}</div>;
}
```

---

## Paso 5: VERIFICAR

### 5.1 Tests

```bash
# Frontend
cd FRONTEND
npm run test          # Unit tests
npm run test:e2e      # E2E tests

# Backend
cd BACKEND
./mvnw test           # Java tests
```

### 5.2 Build producción

```bash
# Frontend
cd FRONTEND
npm run build         # Build producción

# Backend
cd BACKEND
./mvnw clean package -Dprod
```

### 5.3 Verificar que todo funciona

```bash
# Frontend
curl http://localhost:5173

# Backend  
curl http://localhost:8080/health

# nginx
curl http://localhost/api/health
```

---

## CHECKLIST DE LIMPIEZA

```markdown
## ✅ Limpieza Completada

### Código
- [ ] Código duplicado extraído a shared/
- [ ] Código muerto eliminado
- [ ] Imports no utilizados eliminados
- [ ] Dependencias sin uso eliminadas

### Rendimiento
- [ ] Code splitting implementado
- [ ] Lazy loading implementado
- [ ] Imágenes optimizadas
- [ ] Bundle size reducido

### Parpadeo
- [ ] Skeleton loaders agregados
- [ ] Suspense implementado
- [ ] Preloading de datos

### Responsive
- [ ] Mobile: 375px funciona
- [ ] Tablet: 768px funciona
- [ ] Desktop: 1024px+ funciona

### Tests
- [ ] Tests pasando
- [ ] Build producción exitoso
- [ ] Funciona localmente
```

---

## COMANDOS

```bash
claude --skill dev-cleanup "Limpia el código duplicado"
claude --skill dev-cleanup "Optimiza el bundle"
claude --skill dev-cleanup "Arregla el problema de parpadeo"
```
