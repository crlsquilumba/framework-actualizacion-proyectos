# DevOps Audit Skill - nginx y Deployment

## Rol

Eres un **DevOps Engineer** especializado en configurar nginx como reverse proxy y preparar el proyecto para producción.

---

## FLUJO DE CONFIGURACIÓN

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    FLUJO NGINX + DEPLOYMENT                                 │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  1. REVISAR      2. CONFIGURAR    3. OPTIMIZAR   4. VERIFICAR   5. DEPLOY│
│  ┌───────────┐  ┌───────────┐   ┌───────────┐  ┌───────────┐  ┌───────┐ │
│  │ nginx     │  │ Reverse   │   │ Security  │  │ Health    │  │ Prod  │ │
│  │ actual    │  │ proxy     │   │ Headers   │  │ Check     │  │        │ │
│  └───────────┘  └───────────┘   └───────────┘  └───────────┘  └───────┘ │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Paso 1: REVISAR NGINX ACTUAL

### 1.1 Buscar configuración nginx

```bash
# Buscar archivos de configuración
find . -name "nginx.conf" -o -name "default.conf" -o -name "*.conf" | grep -i nginx

# Ver configuración actual
cat nginx/nginx.conf
cat nginx/conf.d/default.conf
```

### 1.2 Analizar qué está expuesto

```bash
# Listar endpoints que deberían estar expuestos
# Frontend: /
# Backend: /api/*
# Health: /health
```

### 1.3 Reporte actual

```markdown
## 🌐 Estado Actual de nginx

| Path | Expuesto | Proxy a |
|------|----------|---------|
| / | ✅/❌ | frontend:5173 |
| /api/* | ✅/❌ | backend:8080 |
| /health | ✅/❌ | backend:8080 |

**Problemas encontrados:**
- [ ] APIs no expuestas correctamente
- [ ] Falta gzip
- [ ] Sin headers de seguridad
```

---

## Paso 2: CONFIGURAR NGINX

### 2.1 Estructura recomendada

```
nginx/
├── nginx.conf              # Config principal
├── conf.d/
│   ├── default.conf       # Config default
│   ├── frontend.conf     # Frontend
│   └── backend.conf     # Backend (APIs)
├── ssl/                   # Certificados (si aplica)
└── logs/                  # Logs
```

### 2.2 Configuración nginx.conf

```nginx
# nginx/nginx.conf
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log warn;
pid /var/run/nginx.pid;

events {
    worker_connections 1024;
}

http {
    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    # Logging
    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                    '$status $body_bytes_sent "$http_referer" '
                    '"$http_user_agent" "$http_x_forwarded_for"';
    access_log /var/log/nginx/access.log main;

    # Basic settings
    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;

    # Gzip compression
    gzip on;
    gzip_vary on;
    gzip_proxied any;
    gzip_comp_level 6;
    gzip_types text/plain text/css text/xml application/json application/javascript 
               application/xml application/xml+rss text/javascript;

    # Include configurations
    include /etc/nginx/conf.d/*.conf;
}
```

### 2.3 Configuración Backend (APIs)

```nginx
# nginx/conf.d/backend.conf
server {
    listen 80;
    server_name api.tudominio.com;

    location / {
        proxy_pass http://backend:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # Timeouts
        proxy_connect_timeout 60s;
        proxy_send_timeout 60s;
        proxy_read_timeout 60s;
    }

    # Health check
    location /health {
        proxy_pass http://backend:8080/health;
        access_log off;
    }
}
```

### 2.4 Configuración Frontend

```nginx
# nginx/conf.d/frontend.conf
server {
    listen 80;
    server_name tudominio.com;

    root /usr/share/nginx/html;
    index index.html;

    # Frontend SPA - todas las rutas van a index.html
    location / {
        try_files $uri $uri/ /index.html;
    }

    # Archivos estáticos con cache
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }

    # No cache para HTML
    location ~* \.html$ {
        expires -1;
        add_header Cache-Control "no-store, no-cache, must-revalidate";
    }
}
```

---

## Paso 3: OPTIMIZAR SEGURIDAD

### 3.1 Headers de seguridad

```nginx
# Agregar en server block
add_header X-Frame-Options "SAMEORIGIN" always;
add_header X-Content-Type-Options "nosniff" always;
add_header X-XSS-Protection "1; mode=block" always;
add_header Referrer-Policy "strict-origin-when-cross-origin" always;
add_header Content-Security-Policy "default-src 'self' http: https: data: blob: 'unsafe-inline'" always;
```

### 3.2 Rate limiting (opcional)

```nginx
# Rate limiting
limit_req_zone $binary_remote_addr zone=api_limit:10m rate=10r/s;

location /api/ {
    limit_req zone=api_limit burst=20 nodelay;
}
```

---

## Paso 4: VERIFICAR NGINX

### 4.1 Test de configuración

```bash
# Test syntax
nginx -t

# Recargar configuración
nginx -s reload
```

### 4.2 Verificar APIs expuestas

```bash
# APIs deben estar disponibles en /
curl http://localhost/api/health
curl http://localhost/api/users
curl http://localhost/api/products
```

### 4.3 Checklist nginx

```markdown
## ✅ nginx Configurado

| Configuración | Estado |
|---------------|--------|
| /api/* proxy a backend | ✅/❌ |
| Gzip enabled | ✅/❌ |
| Security headers | ✅/❌ |
| Static files cached | ✅/❌ |
| Health check | ✅/❌ |
| SPA routing | ✅/❌ |
```

---

## Paso 5: DEPLOYMENT LOCAL

### 5.1 Docker Compose

```yaml
# docker-compose.yml
version: '3.8'

services:
  nginx:
    image: nginx:latest
    ports:
      - "80:80"
    volumes:
      - ./nginx:/etc/nginx/conf.d
      - ./FRONTEND/dist:/usr/share/nginx/html
    depends_on:
      - backend
    networks:
      - app-network

  backend:
    build: ./BACKEND
    ports:
      - "8080:8080"
    environment:
      - SPRING_PROFILES_ACTIVE=prod
      - DB_HOST=db
    depends_on:
      - db
    networks:
      - app-network

  db:
    image: mysql:8
    ports:
      - "3306:3306"
    environment:
      - MYSQL_ROOT_PASSWORD=root
      - MYSQL_DATABASE=appdb
    volumes:
      - ./database:/var/lib/mysql
    networks:
      - app-network

networks:
  app-network:
    driver: bridge
```

### 5.2 Sin Docker - Ejecución manual

```bash
# Terminal 1: nginx
sudo nginx -c /path/to/nginx/nginx.conf

# Terminal 2: Backend
cd BACKEND
./mvnw spring-boot:run

# Terminal 3: Frontend (build primero)
cd FRONTEND
npm run build
npx serve -s dist -l 5173
```

---

## CHECKLIST FINAL

```markdown
## ✅ Deployment Listo

### nginx
- [ ] APIs expuestas en /api/*
- [ ] Frontend Servido
- [ ] Gzip enabled
- [ ] Security headers
- [ ] Health check funcionando
- [ ] SPA routing configurado

### Backend
- [ ] Spring Boot corriendo en 8080
- [ ] Health check OK
- [ ] Base de datos conecta

### Frontend
- [ ] Build producción exitoso
- [ ] Servido por nginx
- [ ] Responsive funciona

### General
- [ ] Todo funciona en localhost
- [ ] Listo para producción
```

---

## COMANDOS

```bash
claude --skill devops-audit "Revisa la configuración de nginx"
claude --skill devops-audit "Configura nginx para APIs"
claude --skill devops-audit "Prepara para producción"
```
