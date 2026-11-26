# gvera · app-tareas

API REST de un **gestor de tareas** construida con **FastAPI** y **SQLAlchemy 2.0 (asíncrono)**, con base de datos SQLite. Sirve como backend para la app móvil (Expo / React Native) del gestor de tareas.

## Tecnologías
- Python 3.10 o superior
- FastAPI + Uvicorn
- SQLAlchemy 2.0 (async) + aiosqlite
- Pydantic v2 (validaciones)
- SQLite (`test.db`, se crea automáticamente)

## Estructura
```text
main.py       # Aplicación FastAPI y endpoints (CRUD)
database.py   # Motor asíncrono, sesiones y Base declarativa
models.py     # Modelo ORM: TaskModel
schemas.py    # Esquemas Pydantic: TaskCreate, TaskUpdate, TaskResponse
test.db       # Base de datos SQLite (se genera al iniciar)
```

## Instalación
```bash
# 1. Crear y activar entorno virtual
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate

# 2. Instalar dependencias
pip install fastapi "uvicorn[standard]" sqlalchemy aiosqlite pydantic
```

## Ejecutar
```bash
uvicorn main:app --host 0.0.0.0 --port 8000 --reload
```
Las tablas se crean solas al iniciar. Si `test.db` ya existía sin la columna `priority`, se agrega automáticamente.

Documentación interactiva (Swagger): http://localhost:8000/docs

## Modelo de tarea
| Campo | Tipo | Detalle |
|---|---|---|
| `id` | int | Autogenerado |
| `title` | str | Obligatorio, no vacío, máx. 100 caracteres |
| `description` | str / null | Opcional, máx. 255 caracteres |
| `completed` | bool | Por defecto `false` |
| `priority` | str | `alta`, `media` (defecto) o `baja` |

## Endpoints
| Método | Ruta | Descripción |
|---|---|---|
| POST | `/tasks/` | Crear una tarea |
| GET | `/tasks/` | Listar tareas (`skip`, `limit`; más recientes primero) |
| GET | `/tasks/{task_id}` | Obtener una tarea por ID |
| PUT | `/tasks/{task_id}` | Actualizar una tarea completa |
| PATCH | `/tasks/{task_id}` | Actualizar parcialmente (ej. completar/descompletar) |
| DELETE | `/tasks/{task_id}` | Eliminar una tarea |

### Ejemplo
```bash
curl -X POST http://localhost:8000/tasks/ \
  -H "Content-Type: application/json" \
  -d '{"title": "Estudiar FastAPI", "priority": "alta"}'
```

## Conexión con el front
- CORS está habilitado para cualquier origen (útil para Expo web).
- Desde un emulador o dispositivo, usar la IP de la PC en lugar de `localhost` (por ejemplo `http://192.168.0.10:8000`). Android Emulator usa `http://10.0.2.2:8000`.

## Errores comunes
- **404 "Tarea no encontrada":** el ID no existe.
- **422:** título vacío, mayor a 100 caracteres o prioridad inválida.
