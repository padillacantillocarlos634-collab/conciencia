# 🚀 Guía de Instalación - ConCiencia

## Requisitos Previos

### Sistema
- Python 3.11+
- Node.js 18+
- PostgreSQL 13+
- Git

### Verificar instalación
```bash
python --version      # Python 3.11+
node --version        # Node 18+
npm --version         # npm 8+
psql --version        # PostgreSQL 13+
```

## 1. Clonar el Repositorio

```bash
git clone https://github.com/padillacantillocarlos634-collab/conciencia.git
cd conciencia
```

## 2. Configurar Backend

### Crear base de datos PostgreSQL

```bash
# Conectarse a PostgreSQL
psql -U postgres

# En psql:
CREATE DATABASE conciencia;
CREATE USER conciencia_user WITH PASSWORD 'tu_contraseña_segura';
ALTER ROLE conciencia_user SET client_encoding TO 'utf8';
ALTER ROLE conciencia_user SET default_transaction_isolation TO 'read committed';
ALTER ROLE conciencia_user SET default_transaction_deferrable TO on;
GRANT ALL PRIVILEGES ON DATABASE conciencia TO conciencia_user;
\q
```

### Configurar entorno Python

```bash
cd backend

# Crear entorno virtual
python -m venv venv

# Activar (Linux/Mac)
source venv/bin/activate

# Activar (Windows)
venv\Scripts\activate

# Instalar dependencias
pip install -r requirements.txt
```

### Configurar variables de entorno

```bash
# Copiar archivo de ejemplo
cp .env.example .env

# Editar .env
nano .env
```

Reemplazar en `.env`:
```
DATABASE_URL=postgresql://conciencia_user:tu_contraseña_segura@localhost:5432/conciencia
SECRET_KEY=tu_clave_secreta_muy_larga_y_aleatoria
```

### Ejecutar migraciones

```bash
# Crear tablas
alembic upgrade head
```

### Iniciar servidor

```bash
uvicorn app.main:app --reload

# Acceder a:
# - API: http://localhost:8000
# - Swagger: http://localhost:8000/docs
# - ReDoc: http://localhost:8000/redoc
```

## 3. Configurar Frontend

```bash
cd frontend

# Instalar dependencias
npm install

# Crear archivo de configuración
cp .env.example .env

# Editar si es necesario
nano .env

# Iniciar servidor de desarrollo
npm run dev

# Acceder a: http://localhost:5173
```

## 4. Configurar micro:bit (Opcional)

### Opción A: MakeCode (Visual)

1. Ir a https://makecode.microbit.org/
2. Crear nuevo proyecto
3. Copiar código de `firmware/` (si está disponible)
4. Descargar archivo .hex
5. Conectar micro:bit por USB
6. Copiar .hex a MICROBIT (como USB)

### Opción B: MicroPython (Texto)

```bash
cd firmware

# Instalar herramientas
pip install uflash

# Cargar código
uflash sensor_verification.py

# Ver serial output
screen /dev/ttyACM0 115200  # Linux/Mac
# o
COM3 (en Device Manager)    # Windows
```

## 5. Verificar que Todo Funcione

### Backend
```bash
curl http://localhost:8000/docs
# Debería abrir Swagger UI
```

### Frontend
```
Abrir http://localhost:5173 en navegador
```

### Database
```bash
psql -U conciencia_user -d conciencia -c "SELECT version();"
```

## 6. Crear Usuario de Prueba

```bash
# En el shell de Python del backend
python

from app.core.security import get_password_hash
from app.models.user import User
from app.database import SessionLocal

db = SessionLocal()
user = User(
    email="admin@conciencia.edu",
    username="admin",
    hashed_password=get_password_hash("password123"),
    role="admin"
)
db.add(user)
db.commit()
```

Acceder con:
- Email: `admin@conciencia.edu`
- Password: `password123`

## 7. Estructura de Carpetas (Verificación)

```
conciencia/
├── backend/
│   ├── app/
│   ├── venv/
│   ├── requirements.txt
│   └── .env
├── frontend/
│   ├── src/
│   ├── node_modules/
│   ├── package.json
│   └── .env
├── firmware/
│   ├── sensor_config.py
│   └── sensor_verification.py
├── docs/
└── README.md
```

## 8. Solución de Problemas

### PostgreSQL no conecta
```bash
# Verificar que corre
psql -U postgres -c "SELECT 1;"

# Reiniciar servicio (Linux)
sudo systemctl restart postgresql

# Puerto por defecto: 5432
```

### Puertos en uso
```bash
# Cambiar puerto backend (en .env)
BACKEND_PORT=8001

# Cambiar puerto frontend (en vite.config.js)
port: 5174
```

### Errores de dependencias
```bash
# Actualizar pip
pip install --upgrade pip

# Reinstalar todo
pip install -r requirements.txt --force-reinstall
```

## 9. Comandos Útiles

```bash
# Backend
cd backend
source venv/bin/activate
python -m uvicorn app.main:app --reload --host 0.0.0.0 --port 8000

# Frontend
cd frontend
npm run dev

# Tests Backend
pytest --cov

# Build Frontend
npm run build
```

## 10. Siguientes Pasos

- [ ] Crear modelos de datos en `backend/app/models/`
- [ ] Implementar rutas de API en `backend/app/routes/`
- [ ] Crear componentes en `frontend/src/components/`
- [ ] Configurar autenticación
- [ ] Conectar frontend con backend
- [ ] Pruebas iniciales

¡Listo! Ya tienes ConCiencia ejecutándose en tu máquina local.
