## IMPORTANTE LA FECHA TOPE: 17/10/2025

# Ejercicios Docker con Python

## Índice de contenidos

**Instrucciones generales**

**Ejercicios básicos:**
- **Ejercicio 1:** Crear una imagen Docker básica
- **Ejercicio 2:** Ejecutar un script de Python en un contenedor
- **Ejercicio 3:** Copiar archivos al contenedor
- **Ejercicio 4:** Crear una API Flask y leer archivos de configuración
- **Ejercicio 5:** Usar variables de entorno definidas en el Dockerfile

**Ejercicios intermedios:**
- **Ejercicio 6:** Exponer puertos y mapearlos al host
- **Ejercicio 7:** Pasar variables de entorno en tiempo de ejecución
- **Ejercicio 8:** Usar volúmenes persistentes desde el host
- **Ejercicio 9:** Multi-stage build para optimizar imágenes
- **Ejercicio 10:** Docker Compose para múltiples servicios

**Ejercicios avanzados:**
- **Ejercicio 11:** Optimizar con .dockerignore
- **Ejercicio 12:** Diferencia entre ENTRYPOINT y CMD


## PARA QUE UN EJERCICIO SEA REVISADO ES OBLIGATORIO REALIZAR UN PULL-REQ
---

# Instrucciones para trabajar los repositorios

1. Clona el repositorio en tu máquina local.
2. Crea una rama y carpeta con tu nombre.apellido.
   - Ejemplo: `sergio.garcia`
3. Desarrolla en esa rama y bajo tu carpeta todos los ejercicios, teniendo cada ejercicio una carpeta propia ej:
```
/7-dokcer-basics/
  ├── sergio.garcia
        ├──ejercicio1
            ├── contenido_del_ejercicio
```

4. Cuando termines un ejercicio commitea y pushea, para solicitar revision es necesario abrir pull request
---

## Ejercicio 1: Crear una imagen Docker básica

1. Crea un `Dockerfile` que utilice como base la imagen oficial de **Python**.
2. El `Dockerfile` debe instalar **requests** dentro del contenedor.
3. Al iniciar el contenedor, debe ejecutar el comando `python --version` para verificar la instalación.

### Dockerfile:

```Dockerfile
FROM python:3.9-slim

RUN pip install requests

CMD ["python", "--version"]
```

### Comando para crear la imagen:

```bash
docker build -t ejercicio1-python-basico .
```

---

## Ejercicio 2: Crear una imagen que ejecute un script de Python

1. Crea un archivo `app.py` que imprima "Hola, Docker!".
2. Crea un `Dockerfile` que copie el archivo `app.py` al contenedor.
3. Configura el `Dockerfile` para que al iniciar el contenedor, ejecute el script `app.py`.

### Estructura del proyecto:

```
/ejercicioX/
  ├── Dockerfile
  └── app.py
```

### Dockerfile:

```Dockerfile
FROM python:3.9-slim

COPY app.py /app/app.py

CMD ["python", "/app/app.py"]
```

### app.py:

```python
print("Hola, Docker!")
```

### Comando para crear la imagen:

```bash
docker build -t ejercicio2-python-script .
```

---

## Ejercicio 3: Crear y montar un volumen dentro del proyecto

1. Crea un archivo `data.txt` con texto de ejemplo dentro de la carpeta `/data` de tu proyecto.
2. Modifica el `Dockerfile` para copiar el archivo `data.txt` al directorio `/data` del contenedor.
3. Incluye el volumen dentro del proyecto y haz que el contenedor acceda a él desde el `Dockerfile`.

### Estructura del proyecto:

```
/ejercicioX/
  ├── Dockerfile
  ├── app.py
  └── data/
      └── data.txt
```

### Dockerfile:

```Dockerfile
FROM python:3.9-slim

COPY data/data.txt /data/data.txt

CMD ["cat", "/data/data.txt"]
```

### Comando para crear la imagen:

```bash
docker build -t ejercicio3-volumen .
```

---

## Ejercicio 4: Crear una imagen que ejecute una API Flask y use un volumen para la configuración

1. Crea una pequeña aplicación Flask que devuelva "Hola, API!".
2. Crea un archivo de configuración `config.txt` en un directorio del proyecto.
3. Modifica la aplicación Flask para que lea el mensaje desde `config.txt`.
4. Asegúrate de que el archivo de configuración está incluido en el volumen dentro del `Dockerfile`.

### Estructura del proyecto:

```
/ejercicioX/
  ├── Dockerfile
  ├── app.py
  └── config/
      └── config.txt
```

### config.txt:

```
Hola desde config.txt
```

### Dockerfile:

```Dockerfile
FROM python:3.9-slim

WORKDIR /app
COPY app.py /app
COPY config/config.txt /app/config.txt

RUN pip install Flask

CMD ["python", "app.py"]
```

### app.py:

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
    with open('config.txt', 'r') as f:
        mensaje = f.read()
    return mensaje

if __name__ == '__main__':
    app.run(host='0.0.0.0')
```

### Comando para crear la imagen:

```bash
docker build -t ejercicio4-flask-api .
```

---

## Ejercicio 5: Crear un contenedor que use variables de entorno y volúmenes

1. Crea un archivo `mensaje.txt` dentro del proyecto.
2. Modifica el `Dockerfile` para incluir variables de entorno que personalicen el comportamiento del contenedor y copie el archivo `mensaje.txt` al contenedor.
3. Haz que el script lea tanto la variable de entorno como el archivo montado desde el volumen.

### Estructura del proyecto:

```
/ejercicioX/
  ├── Dockerfile
  ├── app.py
  └── config/
      └── mensaje.txt
```

### mensaje.txt:

```
Mensaje desde el archivo mensaje.txt
```

### Dockerfile:

```Dockerfile
FROM python:3.9-slim

WORKDIR /app
COPY app.py /app
COPY config/mensaje.txt /app/mensaje.txt

CMD ["python", "app.py"]
```

### app.py:

```python
import os

# Leer mensaje desde variable de entorno o usar valor por defecto
mensaje = os.getenv("MENSAJE", "Mensaje por defecto")

# Leer mensaje desde archivo montado en volumen
with open('mensaje.txt', 'r') as f:
    mensaje_archivo = f.read()

print(mensaje)
print(mensaje_archivo)
```

### Comando para crear la imagen:

```bash
docker build -t ejercicio5-entorno-volumen .
```

---

## Ejercicio 6: Exponer puertos y acceder desde el host

1. Crea una aplicación Flask simple que devuelva "Servidor corriendo en el puerto 5000".
2. Crea un `Dockerfile` que exponga el puerto 5000.
3. Ejecuta el contenedor mapeando el puerto para acceder desde tu navegador.

### Estructura del proyecto:

```
/ejercicioX/
  ├── Dockerfile
  └── app.py
```

### Dockerfile:

```Dockerfile
FROM python:3.9-slim

WORKDIR /app
COPY app.py /app

RUN pip install Flask

EXPOSE 5000

CMD ["python", "app.py"]
```

### app.py:

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
    return "Servidor corriendo en el puerto 5000"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

### Comando para crear la imagen:

```bash
docker build -t ejercicio6-puertos .
```

### Comando para ejecutar el contenedor:

```bash
docker run -d -p 8080:5000 --name mi-servidor ejercicio6-puertos
```

Accede desde tu navegador a `http://localhost:8080`

---

## Ejercicio 7: Usar variables de entorno en tiempo de ejecución

1. Crea un script que lea una variable de entorno `NOMBRE` y la muestre.
2. Crea un `Dockerfile` para ejecutar el script.
3. Ejecuta el contenedor pasando la variable de entorno desde la línea de comandos.

### Estructura del proyecto:

```
/ejercicioX/
  ├── Dockerfile
  └── app.py
```

### Dockerfile:

```Dockerfile
FROM python:3.9-slim

WORKDIR /app
COPY app.py /app

CMD ["python", "app.py"]
```

### app.py:

```python
import os

nombre = os.getenv("NOMBRE", "Usuario Anónimo")
edad = os.getenv("EDAD", "desconocida")

print(f"Hola {nombre}, tu edad es {edad}")
```

### Comando para crear la imagen:

```bash
docker build -t ejercicio7-env-runtime .
```

### Comando para ejecutar el contenedor:

```bash
docker run -e NOMBRE="Juan" -e EDAD="25" ejercicio7-env-runtime
```

---

## Ejercicio 8: Usar volúmenes persistentes reales

1. Crea un script que escriba datos en un archivo `log.txt`.
2. Monta un volumen desde tu máquina local al contenedor para que los datos persistan.
3. Verifica que el archivo se crea en tu máquina local.

### Estructura del proyecto:

```
/ejercicioX/
  ├── Dockerfile
  └── app.py
```

### Dockerfile:

```Dockerfile
FROM python:3.9-slim

WORKDIR /app
COPY app.py /app

CMD ["python", "app.py"]
```

### app.py:

```python
import datetime

with open('/data/log.txt', 'a') as f:
    timestamp = datetime.datetime.now()
    f.write(f"Ejecutado en: {timestamp}\n")
    
print("Log escrito correctamente")
```

### Comando para crear la imagen:

```bash
docker build -t ejercicio8-volumen-persistente .
```

### Comando para ejecutar el contenedor:

```bash
docker run -v $(pwd)/data:/data ejercicio8-volumen-persistente
```

Verifica que el archivo `log.txt` se ha creado en tu carpeta `data`.

---

## Ejercicio 9: Multi-stage build para optimizar la imagen

1. Crea una aplicación Flask simple.
2. Usa multi-stage build para crear una imagen más pequeña.
3. Compara el tamaño de las imágenes.

### Estructura del proyecto:

```
/ejercicioX/
  ├── Dockerfile
  ├── app.py
  └── requirements.txt
```

### requirements.txt:

```
Flask==2.3.0
```

### Dockerfile:

```Dockerfile
# Etapa 1: Builder
FROM python:3.9 as builder

WORKDIR /app
COPY requirements.txt .
RUN pip install --user -r requirements.txt

# Etapa 2: Runtime
FROM python:3.9-slim

WORKDIR /app
COPY --from=builder /root/.local /root/.local
COPY app.py .

ENV PATH=/root/.local/bin:$PATH

EXPOSE 5000

CMD ["python", "app.py"]
```

### app.py:

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
    return "Aplicación optimizada con multi-stage build"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

### Comando para crear la imagen:

```bash
docker build -t ejercicio9-multistage .
```

### Ver el tamaño de la imagen:

```bash
docker images ejercicio9-multistage
```

---

## Ejercicio 10: Docker Compose para múltiples servicios

1. Crea una aplicación Flask que se conecte a Redis.
2. Usa Docker Compose para levantar ambos servicios.
3. Verifica que la aplicación puede incrementar un contador en Redis.

### Estructura del proyecto:

```
/ejercicioX/
  ├── docker-compose.yml
  ├── Dockerfile
  ├── app.py
  └── requirements.txt
```

### requirements.txt:

```
Flask==2.3.0
redis==4.5.0
```

### Dockerfile:

```Dockerfile
FROM python:3.9-slim

WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt

COPY app.py .

EXPOSE 5000

CMD ["python", "app.py"]
```

### app.py:

```python
from flask import Flask
import redis

app = Flask(__name__)
cache = redis.Redis(host='redis', port=6379)

@app.route('/')
def hello():
    count = cache.incr('hits')
    return f'Has visitado esta página {count} veces\n'

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

### docker-compose.yml:

```yaml
version: '3.8'

services:
  web:
    build: .
    ports:
      - "8080:5000"
    depends_on:
      - redis
  
  redis:
    image: redis:alpine
```

### Comando para levantar los servicios:

```bash
docker-compose up -d
```

### Comando para ver los logs:

```bash
docker-compose logs -f
```

### Comando para detener los servicios:

```bash
docker-compose down
```

Accede desde tu navegador a `http://localhost:8080`

---

## Ejercicio 11: Crear un .dockerignore

1. Crea varios archivos en tu proyecto (algunos necesarios y otros no).
2. Crea un archivo `.dockerignore` para excluir archivos innecesarios.
3. Verifica que la imagen es más pequeña.

### Estructura del proyecto:

```
/ejercicioX/
  ├── Dockerfile
  ├── .dockerignore
  ├── app.py
  ├── README.md
  ├── tests/
  └── __pycache__/
```

### .dockerignore:

```
__pycache__
*.pyc
*.pyo
*.pyd
.Python
*.log
.git
.gitignore
README.md
tests/
```

### Dockerfile:

```Dockerfile
FROM python:3.9-slim

WORKDIR /app
COPY . /app

CMD ["python", "app.py"]
```

### app.py:

```python
print("Imagen optimizada con .dockerignore")
```

### Comando para crear la imagen:

```bash
docker build -t ejercicio11-dockerignore .
```

---

## Ejercicio 12: Usar ENTRYPOINT y CMD juntos

1. Crea un script que acepte argumentos.
2. Usa `ENTRYPOINT` para definir el comando base y `CMD` para argumentos por defecto.
3. Ejecuta el contenedor con y sin argumentos personalizados.

### Estructura del proyecto:

```
/ejercicioX/
  ├── Dockerfile
  └── app.py
```

### Dockerfile:

```Dockerfile
FROM python:3.9-slim

WORKDIR /app
COPY app.py /app

ENTRYPOINT ["python", "app.py"]
CMD ["Mundo"]
```

### app.py:

```python
import sys

nombre = sys.argv[1] if len(sys.argv) > 1 else "Usuario"
print(f"Hola, {nombre}!")
```

### Comando para crear la imagen:

```bash
docker build -t ejercicio12-entrypoint .
```

### Ejecutar con argumento por defecto:

```bash
docker run ejercicio12-entrypoint
```

### Ejecutar con argumento personalizado:

```bash
docker run ejercicio12-entrypoint "Juan"
```


