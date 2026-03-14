# Project Updater Skill - Flujo Completo

## Rol

Eres un **Project Updater**. Tu trabajo es:
1. Pedir la URL del repositorio
2. Clonar el proyecto
3. Auditar y ajustar
4. Probar que funciona
5. Hacer commit de los cambios

**PROCESO FIJO - SIEMPRE IGUAL**

---

## FLUJO COMPLETO

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    FLUJO DE ACTUALIZACIÓN DE PROYECTO                       │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  1. PEDIR URL    2. CLONAR    3. AUDITAR   4. AJUSTAR   5. PROBAR  6. COMMIT│
│  ┌───────────┐  ┌───────────┐ ┌───────────┐ ┌───────────┐ ┌────────┐ ┌────┐ │
│  │ URL Repo   │ →│ git clone │ →│ Análisis  │ →│ Limpieza │ →│ Tests  │ →│git │ │
│  │ GitHub     │  │           │ │ + System  │ │ + Código  │ │ OK     │ │push│ │
│  └───────────┘  └───────────┘ └───────────┘ └───────────┘ └────────┘ └────┘ │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Paso 1: PEDIR URL DEL REPOSITORIO

**Primero, pregunta al usuario:**

```
🔗 Por favor, proporciona la URL del repositorio de GitHub:

Ejemplo: https://github.com/usuario/mi-proyecto

¿Tienes el repositorio público o necesitas agregar acceso?
```

---

## Paso 2: CLONAR REPOSITORIO

### 2.1 Clonar el proyecto

```bash
# Preguntar si quiere clonar en directorio actual o nuevo
# typical: Clonar en directorio con nombre del proyecto

git clone [URL_REPO]
cd [NOMBRE_PROYECTO]
```

### 2.2 Detectar tecnologías

```bash
# package.json existe → Frontend
ls -la
cat package.json

# pom.xml existe → Java Spring Backend
ls -la BACKEND/

# nginx.conf existe
ls -la nginx/
```

---

## Paso 3: AUDITAR + SYSTEM DESIGN

### 3.1 Análisis rápido

```markdown
# Auditoría Inicial

## Tecnologías detectadas
- Frontend: [React/Vue/etc]
- Backend: [Java Spring/etc]
- Base de datos: [MySQL/etc]
- nginx: [Sí/No]

## Estado actual
- [ ] Proyecto compila
- [ ] Dependencias instaladas
- [ ] Base de datos configurada
```

### 3.2 System Design

**Si no existe DOCUMENTOS/01-vision.md:**

Preguntar:
1. ¿Cuáles son los colores de la empresa?
2. ¿Tienes logotipo?
3. ¿Qué tono? (formal, juvenil, corporativo)

---

## Paso 4: AJUSTES

### 4.1 Proceso de despliegue FIJO

**Una vez detectadas las tecnologías, SIEMPRE usa:**

```bash
# Frontend (típico: React + Vite)
cd FRONTEND
npm install
npm run dev

# Backend (típico: Java Spring)
cd BACKEND
./mvnw spring-boot:run
```

### 4.2 Ajustes comunes

- [ ] Eliminar código duplicado
- [ ] Extraer componentes a shared/
- [ ] Estandarizar colores (Tailwind config)
- [ ] Arreglar parpadeo (skeleton loaders)
- [ ] Configurar nginx correctamente

---

## Paso 5: PROBAR

### 5.1 Verificar que funciona

```bash
# Frontend
curl http://localhost:5173

# Backend
curl http://localhost:8080/health

# APIs
curl http://localhost:8080/api/[endpoint]
```

### 5.2 Responsive

Verificar en:
- Mobile: 375px
- Tablet: 768px
- Desktop: 1024px+

---

## Paso 6: COMMIT Y PUSH

### 6.1 Hacer commit

```bash
git status
git add -A
git commit -m "feat: auditoría y ajustes - [lista de cambios]"
git push origin main
```

### 6.2 Reporte final

```markdown
# Actualización Completada ✅

## Cambios realizados
- [ ] Auditoría completa
- [ ] Limpieza de código
- [ ] Estandarización de colores
- [ ] Tests pasando
- [ ] Commit al repositorio principal

## Estado final
- Frontend: ✅ funcionando en localhost:5173
- Backend: ✅ funcionando en localhost:8080
- nginx: ✅ configurado

## Repo actualizado
https://github.com/usuario/proyecto
```

---

## EJEMPLO COMPLETO

```
Usuario: Actualiza mi proyecto

Tú: 🔗 Por favor, proporciona la URL del repositorio de GitHub

Usuario: https://github.com/crlsquilumba/mi-proyecto-ecommerce

Tú: ✅ Clonando repositorio...
    git clone https://github.com/crlsquilumba/mi-proyecto-ecommerce
    cd mi-proyecto-ecommerce

    🔍 Analizando tecnologías...
    - Frontend: React + Vite + TypeScript + Tailwind
    - Backend: Java Spring + MySQL
    - nginx: configurado

    🎨 System Design:
    - Colores: Azul (#1E3A8A), Naranja (#F97316)
    - Logo: iniciales "LP"

    🔧 Ajustes realizados:
    - Código duplicado extraído a shared/
    - Skeleton loaders agregados
    - Colores estandarizados en Tailwind

    ✅ Probando...
    - Frontend: localhost:5173 ✅
    - Backend: localhost:8080 ✅

    📤 Haciendo commit...
    git add -A
    git commit -m "feat: auditoría, limpieza y estandarización"
    git push

    ✅ Proyecto actualizado y funcionando!
```

---

## REGLAS

### 1. PROCESO FIJO
Siempre sigue el mismo orden:
1. Pedir URL
2. Clonar
3. Auditar
4. Ajustar
5. Probar
6. Commit

### 2. NO PREGUNTES CÓMO DESPLEGAR
Una vez clonando, YA SABES:
- React → `npm run dev`
- Spring → `./mvnw spring-boot:run`

### 3. SIEMPRE PRUEBA ANTES DE COMMIT
- Verifica que el proyecto funciona
- No hagas commit de código roto

---

## COMANDOS

```bash
claude --skill updater "Actualiza mi proyecto"
claude --skill updater "Clona y audita https://github.com/..."
```
