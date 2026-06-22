# 🚒 Sistema de Gestión de Incendios Forestales - Comunitat Valenciana

Backend API completo para el panel de control de gestión de incendios forestales de la Comunidad Valenciana.

## 🎯 Características

- **Gestión de Incendios**: Crear, leer, actualizar y eliminar incendios
- **Gestión de Recursos**: Administrar bomberos, helicópteros, retenes
- **Asignación de Recursos**: Asignar recursos a incendios específicos
- **Estadísticas en Tiempo Real**: Dashboard con métricas actualizadas
- **API RESTful**: Endpoints completos con manejo de errores
- **Base de Datos**: SQLAlchemy con SQLite (configurable)
- **CORS Habilitado**: Para acceso desde frontend

## 📋 Requisitos

- Python 3.8+
- pip (gestor de paquetes)

## 🚀 Instalación

1. **Clonar el repositorio**
```bash
git clone https://github.com/dvivesboix-eng/vives.git
cd vives
```

2. **Crear entorno virtual**
```bash
python -m venv venv
source venv/bin/activate  # En Windows: venv\Scripts\activate
```

3. **Instalar dependencias**
```bash
pip install -r requirements.txt
```

4. **Configurar variables de entorno**
```bash
cp .env.example .env
# Editar .env con tu configuración
```

5. **Ejecutar la aplicación**
```bash
python app.py
```

La API estará disponible en: `http://localhost:5000`

## 📚 API Endpoints

### Incendios

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| GET | `/api/incendios` | Obtener todos los incendios |
| GET | `/api/incendios/<id>` | Obtener incendio específico |
| POST | `/api/incendios` | Crear nuevo incendio |
| PUT | `/api/incendios/<id>` | Actualizar incendio |
| DELETE | `/api/incendios/<id>` | Eliminar incendio |

**Ejemplo POST:**
```json
{
    "ubicacion": "Valencia",
    "nivel": 2,
    "lat": 39.4699,
    "lng": -0.3763,
    "estado": "Activo"
}
```

### Recursos

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| GET | `/api/recursos` | Obtener todos los recursos |
| GET | `/api/recursos/<id>` | Obtener recurso específico |
| POST | `/api/recursos` | Crear nuevo recurso |
| PUT | `/api/recursos/<id>` | Actualizar recurso |
| DELETE | `/api/recursos/<id>` | Eliminar recurso |

**Ejemplo POST:**
```json
{
    "nombre": "Parque de Bomberos Valencia",
    "tipo": "Bomberos",
    "lat": 39.4699,
    "lng": -0.3763,
    "estado": "Disponible",
    "capacidad": 25
}
```

### Asignaciones

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| GET | `/api/asignaciones` | Obtener todas las asignaciones |
| POST | `/api/asignaciones` | Asignar recurso a incendio |
| DELETE | `/api/asignaciones/<id>` | Eliminar asignación |

**Ejemplo POST:**
```json
{
    "incendio_id": 1,
    "recurso_id": 1
}
```

### Estadísticas

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| GET | `/api/estadisticas` | Obtener estadísticas del sistema |

**Respuesta:**
```json
{
    "incendios": {
        "total": 3,
        "activos": 2,
        "controlados": 1,
        "extinguidos": 0
    },
    "recursos": {
        "total": 4,
        "disponibles": 2,
        "en_mision": 2,
        "mantenimiento": 0
    }
}
```

### Health Check

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| GET | `/health` | Verificar estado del servidor |

## 🗄️ Modelos de Datos

### Incendio
```python
{
    "id": int,
    "ubicacion": str,
    "nivel": int (1-3),
    "estado": str ("Activo", "Controlado", "Extinguido"),
    "lat": float,
    "lng": float,
    "created_at": datetime,
    "updated_at": datetime
}
```

### Recurso
```python
{
    "id": int,
    "nombre": str,
    "tipo": str ("Bomberos", "Helicóptero", "Retén"),
    "estado": str ("Disponible", "En Misión", "Mantenimiento"),
    "lat": float,
    "lng": float,
    "capacidad": int,
    "created_at": datetime,
    "updated_at": datetime
}
```

### Asignación
```python
{
    "id": int,
    "incendio_id": int,
    "recurso_id": int,
    "created_at": datetime
}
```

## 🎨 Frontend

El frontend está ubicado en `frontend/index.html` y proporciona:

- 🗺️ Mapa interactivo con Leaflet
- 📊 Dashboard de estadísticas en tiempo real
- 📝 Formulario para reportar incendios
- 🔍 Filtros para mostrar/ocultar elementos
- ⚡ Actualización automática cada 10 segundos
- 📱 Diseño responsive con Bootstrap

**Características del Frontend:**
- Marcadores de colores diferentes (rojo para incendios, azul para recursos)
- Notificaciones de éxito/error
- Interfaz intuitiva y accesible
- Estadísticas en tiempo real

## 📊 Ciclo de Vida de un Incendio

1. **Notificación**: Se crea un incendio nuevo con estado "Activo"
2. **Asignación**: Se asignan recursos disponibles al incendio
3. **Gestión**: Los recursos cambian a estado "En Misión"
4. **Actualización**: El estado del incendio se actualiza (Activo → Controlado → Extinguido)
5. **Liberación**: Al eliminar la asignación, los recursos vuelven a "Disponible"

## 🔧 Configuración Avanzada

### Cambiar Base de Datos

En `.env`:
```env
# SQLite (default)
DATABASE_URL=sqlite:///forest_fires.db

# PostgreSQL
DATABASE_URL=postgresql://user:password@localhost:5432/forest_fires

# MySQL
DATABASE_URL=mysql+pymysql://user:password@localhost:3306/forest_fires
```

### Modo Producción

```bash
export FLASK_ENV=production
gunicorn -w 4 -b 0.0.0.0:5000 app:app
```

## 📝 Ejemplos de Uso

### Crear un incendio
```bash
curl -X POST http://localhost:5000/api/incendios \
  -H "Content-Type: application/json" \
  -d '{
    "ubicacion": "Castellón",
    "nivel": 3,
    "lat": 40.0104,
    "lng": -0.0527
  }'
```

### Obtener todos los incendios
```bash
curl http://localhost:5000/api/incendios
```

### Actualizar estado de incendio
```bash
curl -X PUT http://localhost:5000/api/incendios/1 \
  -H "Content-Type: application/json" \
  -d '{"estado": "Extinguido"}'
```

### Asignar recurso a incendio
```bash
curl -X POST http://localhost:5000/api/asignaciones \
  -H "Content-Type: application/json" \
  -d '{
    "incendio_id": 1,
    "recurso_id": 2
  }'
```

## 🐛 Troubleshooting

**Error: "Cannot connect to database"**
- Verificar que el archivo `.env` existe y está configurado correctamente
- Asegurar que el directorio tiene permisos de escritura para crear la BD

**Error: "Port 5000 already in use"**
```bash
# Usar puerto diferente
python app.py --port 5001
```

**CORS Error desde frontend**
- La API tiene CORS habilitado por defecto
- Verificar que la URL en el frontend coincide con la del backend

## 📦 Dependencias

- **Flask**: Framework web
- **Flask-CORS**: Soporte CORS
- **Flask-SQLAlchemy**: ORM para base de datos
- **python-dotenv**: Gestión de variables de entorno
- **gunicorn**: Servidor WSGI para producción

## 📄 Licencia

Este proyecto es de código abierto y está disponible bajo licencia MIT.

## 👤 Autor

Desarrollado por **dvivesboix-eng**

## 🤝 Contribuciones

Las contribuciones son bienvenidas. Por favor:
1. Fork el proyecto
2. Crear una rama para tu feature (`git checkout -b feature/AmazingFeature`)
3. Commit tus cambios (`git commit -m 'Add AmazingFeature'`)
4. Push a la rama (`git push origin feature/AmazingFeature`)
5. Abrir un Pull Request

## ⭐ Si te ha sido útil, por favor deja una estrella en GitHub!

---

**Última actualización**: 22 de Junio de 2026
