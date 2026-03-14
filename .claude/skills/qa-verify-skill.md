# QA Verification Skill - Pruebas de Funcionamiento

## Rol

Eres un **QA Engineer** especializado en verificar que todo funciona. Ejecutas pruebas de frontend, backend, datos y responsive.

---

## FLUJO DE PRUEBAS

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                       FLUJO DE VERIFICACIÓN                                  │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  1. FRONTEND    2. BACKEND      3. DATOS        4. RESPONSIVE   5. REPORTE│
│  ┌───────────┐  ┌───────────┐   ┌───────────┐  ┌───────────┐  ┌────────┐ │
│  │ Smoke     │  │ Health    │   │ Queries   │  │ Multi     │  │ Resume │ │
│  │ Tests     │  │ Check     │   │ Tests     │  │ Screens   │  │        │ │
│  └───────────┘  └───────────┘   └───────────┘  └───────────┘  └────────┘ │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Paso 1: PRUEBAS FRONTEND

### 1.1 Verificar que el frontend corre

```bash
# Iniciar frontend
cd FRONTEND
npm run dev

# Verificar que responde
curl http://localhost:5173
```

### 1.2 Smoke Tests

| Test | Comando | Esperado |
|------|---------|----------|
| Homepage carga | `curl http://localhost:5173` | HTML con React root |
| CSS carga | Revisar en DevTools | Sin errores 404 |
| JS carga | Revisar en DevTools | Sin errores |
| Navegación | Click en links | Cambia ruta |

### 1.3 Pruebas E2E con Playwright

```bash
# Instalar Playwright
npm install -D @playwright/test

# Configuración playwright.config.ts
export default {
  testDir: './tests',
  use: {
    baseURL: 'http://localhost:5173',
    headless: true,
  },
};
```

```typescript
// tests/smoke.spec.ts
import { test, expect } from '@playwright/test';

test('homepage loads', async ({ page }) => {
  await page.goto('http://localhost:5173');
  await expect(page).toHaveTitle(/.*/);
  await expect(page.locator('body')).toBeVisible();
});

test('navigation works', async ({ page }) => {
  await page.goto('http://localhost:5173');
  await page.click('text=Catálogo');
  await expect(page.url()).toContain('/catalog');
});

test('no console errors', async ({ page }) => {
  const errors: string[] = [];
  page.on('console', msg => {
    if (msg.type() === 'error') errors.push(msg.text());
  });
  await page.goto('http://localhost:5173');
  expect(errors).toHaveLength(0);
});
```

### 1.4 Checklist Frontend

```markdown
## ✅ Frontend

| Prueba | Resultado |
|--------|-----------|
| Homepage carga | ✅/❌ |
| CSS carga | ✅/❌ |
| JS carga sin errores | ✅/❌ |
| Navegación funciona | ✅/❌ |
| No hay console errors | ✅/❌ |
| No hay parpadeo | ✅/❌ |
```

---

## Paso 2: PRUEBAS BACKEND

### 2.1 Verificar que el backend corre

```bash
# Java Spring
cd BACKEND
./mvnw spring-boot:run

# Verificar health
curl http://localhost:8080/health
```

### 2.2 Health Check

```bash
# Backend health
curl http://localhost:8080/health
# Esperar: {"status":"UP"}

# nginx proxy health
curl http://localhost/api/health
# Esperar: {"status":"UP"}
```

### 2.3 Probar APIs

```bash
# GET endpoints
curl http://localhost:8080/api/users
curl http://localhost:8080/api/products

# POST endpoints
curl -X POST http://localhost:8080/api/users \
  -H "Content-Type: application/json" \
  -d '{"name":"Test","email":"test@test.com"}'

# Autenticación
curl -X POST http://localhost:8080/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"test@test.com","password":"123456"}'
```

### 2.4 Checklist Backend

```markdown
## ✅ Backend

| Prueba | Comando | Resultado |
|--------|---------|-----------|
| Health check | `curl localhost:8080/health` | ✅/❌ |
| GET /api/users | `curl localhost:8080/api/users` | ✅/❌ |
| POST /api/auth/login | curl login | ✅/❌ |
| Base de datos conecta | revisar health | ✅/❌ |
```

---

## Paso 3: PRUEBAS DE DATOS

### 3.1 Verificar conexión a BD

```bash
# MySQL
mysql -u user -p -e "SELECT 1"

# Verificar tablas
mysql -u user -p -e "SHOW TABLES" database_name
```

### 3.2 Verificar queries

```bash
# Probar queries principales
# - Login de usuario
# - Listar productos
# - Crear orden
```

### 3.3 Checklist Datos

```markdown
## ✅ Datos

| Prueba | Resultado |
|--------|-----------|
| Conexión a BD | ✅/❌ |
| Tablas existen | ✅/❌ |
| Queries funcionan | ✅/❌ |
| Datos seed existen | ✅/❌ |
```

---

## Paso 4: PRUEBAS RESPONSIVE

### 4.1 Breakpoints

| Tamaño | Ancho | Dispositivo |
|--------|-------|--------------|
| Mobile | 375px | iPhone SE |
| Tablet | 768px | iPad |
| Desktop | 1024px | Laptop |
| Large | 1440px | Desktop |

### 4.2 Playwright Responsive

```typescript
// tests/responsive.spec.ts
import { test, expect } from '@playwright/test';

const devices = [
  { name: 'Mobile', width: 375, height: 667 },
  { name: 'Tablet', width: 768, height: 1024 },
  { name: 'Desktop', width: 1024, height: 768 },
  { name: 'Large', width: 1440, height: 900 },
];

for (const device of devices) {
  test(`responsive - ${device.name}`, async ({ page }) => {
    await page.setViewportSize({ 
      width: device.width, 
      height: device.height 
    });
    
    await page.goto('http://localhost:5173');
    
    // Verificar que todo es visible
    await expect(page.locator('body')).toBeVisible();
    
    // Verificar que no hay overflow horizontal
    const body = await page.locator('body');
    const boundingBox = await body.boundingBox();
    expect(boundingBox.width).toBeLessThanOrEqual(device.width);
  });
}
```

### 4.3 Checklist Responsive

```markdown
## ✅ Responsive

| Tamaño | Ancho | Resultado |
|--------|-------|-----------|
| Mobile | 375px | ✅/❌ |
| Tablet | 768px | ✅/❌ |
| Desktop | 1024px | ✅/❌ |
| Large | 1440px | ✅/❌ |
| No horizontal scroll | - | ✅/❌ |
```

---

## REPORTE DE PRUEBAS

```markdown
# Reporte de Pruebas - [Fecha]

## 📊 Resumen

| Categoría | Pruebas | Pasaron | Fallaron |
|-----------|---------|---------|----------|
| Frontend | 5 | 5 | 0 |
| Backend | 4 | 4 | 0 |
| Datos | 4 | 4 | 0 |
| Responsive | 5 | 5 | 0 |
| **TOTAL** | **18** | **18** | **0** |

## ✅ Frontend
- [x] Homepage carga
- [x] Navegación funciona
- [x] No hay console errors
- [x] No hay parpadeo

## ✅ Backend
- [x] Health check OK
- [x] APIs responden
- [x] Autenticación funciona

## ✅ Datos
- [x] Conexión BD OK
- [x] Queries funcionan
- [x] Datos seed existen

## ✅ Responsive
- [x] Mobile (375px)
- [x] Tablet (768px)
- [x] Desktop (1024px)
- [x] Large (1440px)

## 🎯 Estado Final
**✅ PROYECTO APROBADO PARA PRODUCCIÓN**
```

---

## COMANDOS

```bash
# Ejecutar todas las pruebas
claude --skill qa-verify "Ejecuta pruebas de frontend"
claude --skill qa-verify "Ejecuta pruebas de backend"
claude --skill qa-verify "Verifica responsive"
claude --skill qa-verify "Genera reporte de pruebas"
```
