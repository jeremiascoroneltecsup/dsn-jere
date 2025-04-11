# Caso de Estudio: Implementando A/B Testing con Nginx y Docker

## Contexto del Caso

**Cliente**: FastShop, una startup de e-commerce en crecimiento que desea mejorar su experiencia de usuario.

**Problema**: El equipo de UX ha diseñado una nueva versión de la página de inicio que creen que aumentará las conversiones, pero el CEO quiere evidencia antes de implementarla completamente. Necesitan una forma de mostrar diferentes versiones de la página a los usuarios y medir el rendimiento.

**Objetivo**: Implementar un sistema de testing A/B que permita servir dos versiones diferentes de la aplicación web, asignar usuarios a cada versión mediante rutas específicas, y facilitar la recopilación de métricas de conversión.

**Restricciones**: 
- Presupuesto limitado, prefieren soluciones basadas en contenedores
- No pueden modificar sustancialmente el código de la aplicación existente
- Necesitan una solución que pueda implementarse rápidamente

## Solución Propuesta

En este caso de estudio, implementaremos un sistema de proxy basado en Nginx que permite servir diferentes versiones de una aplicación web mediante URLs específicas. Esta solución facilita las pruebas A/B y el acceso directo a cada versión para el equipo de desarrollo.

## Estructura del Proyecto

```
proxy-lab/
├── app/
│   ├── app_a.py
│   ├── app_b.py
│   ├── Dockerfile.a
│   └── Dockerfile.b
└── nginx/
    ├── nginx.conf
    └── Dockerfile
```

## Guía Paso a Paso

### Paso 1: Preparar el entorno de trabajo

Creamos la estructura base del proyecto:

```bash
# Crear el directorio principal del proyecto
mkdir proxy-lab

# Entrar al directorio del proyecto
cd proxy-lab

# Crear el directorio para las aplicaciones
mkdir app
```

### Paso 2: Crear las aplicaciones Flask (Simulando Versiones del Sitio Web)

Para simular las dos versiones diferentes de la página de inicio de FastShop, crearemos dos aplicaciones Flask simples:

```bash
# Entrar al directorio de las aplicaciones
cd app
```

#### Aplicación A (Versión Original)

```bash
# Crear el archivo de la aplicación A
vi app_a.py
```

Con el siguiente contenido:

```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def home():
    return """
    <!DOCTYPE html>
    <html>
    <head>
        <title>FastShop - Tu Tienda Online</title>
        <style>
            body { font-family: Arial, sans-serif; margin: 0; padding: 20px; background-color: #f4f4f4; }
            .container { max-width: 1200px; margin: 0 auto; background-color: white; padding: 20px; border-radius: 5px; }
            .header { color: #2c3e50; text-align: center; }
            .button { background-color: #3498db; color: white; padding: 10px 15px; border: none; border-radius: 4px; cursor: pointer; }
        </style>
    </head>
    <body>
        <div class="container">
            <h1 class="header">Bienvenido a FastShop - Versión A (Original)</h1>
            <p>Descubre nuestros productos de alta calidad a precios increíbles.</p>
            <button class="button">Ver Ofertas</button>
        </div>
    </body>
    </html>
    """

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=80)
```

Ahora creamos el Dockerfile para la aplicación A:

```bash
# Crear el Dockerfile para la aplicación A
vi Dockerfile.a
```

Con el siguiente contenido:

```dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY app_a.py .
RUN pip install flask
CMD ["python", "app_a.py"]
```

#### Aplicación B (Nueva Versión Propuesta por UX)

Creamos el archivo de la aplicación B, que representa el nuevo diseño propuesto por el equipo de UX:

```bash
# Crear el archivo de la aplicación B
vi app_b.py
```

Con el siguiente contenido:

```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def home():
    return """
    <!DOCTYPE html>
    <html>
    <head>
        <title>FastShop - Experiencia de Compra Renovada</title>
        <style>
            body { font-family: 'Roboto', sans-serif; margin: 0; padding: 0; background-color: #ecf0f1; }
            .hero { background-color: #2980b9; color: white; padding: 40px; text-align: center; }
            .container { max-width: 1200px; margin: 0 auto; padding: 20px; }
            .cta-button { background-color: #e74c3c; color: white; padding: 15px 30px; border: none; border-radius: 30px; 
                          font-size: 18px; margin-top: 20px; cursor: pointer; }
            .features { display: flex; justify-content: space-between; margin-top: 30px; text-align: center; }
            .feature { flex: 1; padding: 20px; }
        </style>
    </head>
    <body>
        <div class="hero">
            <h1>Descubre la Nueva Experiencia FastShop - Versión B</h1>
            <p>Compra más rápido, encuentra mejores ofertas, disfruta de una experiencia renovada</p>
            <button class="cta-button">EXPLORAR OFERTAS EXCLUSIVAS</button>
        </div>
        <div class="container">
            <div class="features">
                <div class="feature">
                    <h3>Envío Gratuito</h3>
                    <p>En todos tus pedidos superiores a 50€</p>
                </div>
                <div class="feature">
                    <h3>Descuentos Exclusivos</h3>
                    <p>Hasta 70% en productos seleccionados</p>
                </div>
                <div class="feature">
                    <h3>Atención 24/7</h3>
                    <p>Soporte disponible todos los días</p>
                </div>
            </div>
        </div>
    </body>
    </html>
    """

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=80)
```

Creamos el Dockerfile para la aplicación B:

```bash
# Crear el Dockerfile para la aplicación B
vi Dockerfile.b
```

Con el siguiente contenido:

```dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY app_b.py .
RUN pip install flask
CMD ["python", "app_b.py"]
```

### Paso 3: Construir las imágenes Docker para las aplicaciones

```bash
# Volver al directorio principal del proyecto
cd ..

# Construir la imagen de Docker para la versión A
# El punto al final indica que el contexto de construcción es el directorio actual
sudo docker build -f app/Dockerfile.a -t myapp:version-a app/

# Construir la imagen de Docker para la versión B
sudo docker build -f app/Dockerfile.b -t myapp:version-b app/
```

### Paso 4: Configurar Nginx como proxy para pruebas A/B

En este paso, configuraremos Nginx como un proxy inverso que dirigirá el tráfico hacia cada versión de la aplicación basándose en rutas específicas.

```bash
# Crear el directorio para los archivos de Nginx
mkdir nginx

# Entrar al directorio de Nginx
cd nginx
```

Creamos el archivo de configuración de Nginx:

```bash
# Crear el archivo de configuración de Nginx
vi nginx.conf
```

Con el siguiente contenido (configuración optimizada):

```nginx
events {
    worker_connections 1024;
}

http {
    # Configuración básica
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;

    # Definición de servidores
    server {
        listen       80;
        server_name  localhost;

        # Ruta para la versión A
        location /version-a/ {
            proxy_pass http://app_a/;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
        }

        # Ruta para la versión B
        location /version-b/ {
            proxy_pass http://app_b/;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
        }

        # Ruta principal que redirige a version-a por defecto
        location = / {
            return 302 /version-a/;
        }
    }
}
```

Creamos el Dockerfile para Nginx:

```bash
# Crear el Dockerfile para Nginx
vi Dockerfile
```

Con el siguiente contenido:

```dockerfile
FROM nginx:alpine
COPY nginx.conf /etc/nginx/nginx.conf
```

### Paso 5: Construir la imagen de Nginx

```bash
# Volver al directorio principal del proyecto
cd ..

# Construir la imagen de Docker para Nginx
# Esto crea una imagen a partir del Dockerfile en el directorio nginx/
docker build -t mynginx -f nginx/Dockerfile nginx
```

### Paso 6: Crear la red Docker y ejecutar los contenedores

```bash
# Crear una red Docker para la comunicación entre contenedores
# Esto permite que los contenedores se comuniquen usando nombres en lugar de IPs
docker network create my-network

# Ejecutar el contenedor de la aplicación A
# --name: asigna un nombre al contenedor
# --network: conecta el contenedor a la red especificada
# --network-alias: permite que otros contenedores en la misma red resuelvan este contenedor por nombre
docker run -d --name app_a --network my-network --network-alias app_a myapp:version-a

# Ejecutar el contenedor de la aplicación B
docker run -d --name app_b --network my-network --network-alias app_b myapp:version-b

# Ejecutar el contenedor de Nginx
# -p 80:80: mapea el puerto 80 del host al puerto 80 del contenedor
docker run -d --name nginx-proxy --network my-network -p 80:80 mynginx
```

### Paso 7: Verificar el funcionamiento

```bash
# Verificar que todos los contenedores estén en ejecución
docker ps

# Acceder a la versión A directamente
curl http://localhost/version-a/

# Acceder a la versión B directamente
curl http://localhost/version-b/

# Acceder a la raíz (deberá redirigir a version-a)
curl -L http://localhost/
```

También puedes probar accediendo desde tu navegador:
- http://localhost/version-a/ para ver la versión original
- http://localhost/version-b/ para ver la nueva versión
- http://localhost/ te redirigirá automáticamente a la versión A

### Paso 8: Solución de problemas comunes

Si encuentras algún problema, aquí hay algunos pasos para solucionarlo:

```bash
# Ver los logs de los contenedores
docker logs app_a
docker logs app_b
docker logs nginx-proxy

# Entrar al contenedor de Nginx para realizar pruebas
docker exec -it nginx-proxy /bin/sh

# Dentro del contenedor de Nginx, verifica la conectividad a las aplicaciones
ping app_a
ping app_b
curl http://app_a
curl http://app_b

# Si es necesario, reinicia todo el entorno
docker stop app_a app_b nginx-proxy
docker rm app_a app_b nginx-proxy
docker network rm my-network
docker network create my-network
docker run -d --name app_a --network my-network --network-alias app_a myapp:version-a
docker run -d --name app_b --network my-network --network-alias app_b myapp:version-b
docker run -d --name nginx-proxy --network my-network -p 80:80 mynginx
```

## Resultados y Análisis

La implementación de este sistema de proxy permitió a FastShop realizar pruebas A/B efectivas con las siguientes ventajas:

1. **Acceso directo a versiones**: Los usuarios y el equipo de desarrollo pueden acceder directamente a cada versión a través de URLs específicas.
2. **Transparencia para pruebas**: La redirección automática permite dirigir a usuarios nuevos a la versión A por defecto.
3. **Fácil implementación**: La configuración basada en rutas es simple de entender y mantener.
4. **Escalabilidad**: El diseño basado en contenedores permite escalar horizontalmente según la demanda.

## Preguntas Críticas para el Análisis

1. **Arquitectura y Diseño**:
   - ¿Qué ventajas ofrece la arquitectura de microservicios utilizada en este caso frente a una aplicación monolítica?
   - ¿Cómo adaptarías esta solución para implementar balanceo de carga además de las pruebas A/B?
   - ¿Qué consideraciones de seguridad deberían tenerse en cuenta al implementar esta solución en producción?

2. **Implementación Técnica**:
   - ¿Cómo modificarías la configuración para implementar un reparto aleatorio del tráfico (70/30) entre las versiones A y B?
   - ¿Qué estrategia implementarías para recopilar métricas de conversión de cada versión?
   - ¿Cómo adaptarías esta solución para manejar más de dos versiones simultáneas?

3. **Operaciones y Mantenimiento**:
   - ¿Qué estrategia de backup y recuperación implementarías para este sistema?
   - ¿Cómo realizarías actualizaciones de las aplicaciones sin tiempo de inactividad?
   - ¿Qué métricas de monitoreo serían críticas para asegurar el funcionamiento correcto del proxy?

4. **Consideraciones de Negocio**:
   - ¿Qué métricas específicas recomendarías a FastShop para evaluar el éxito de cada versión?
   - ¿Cómo integrarías este sistema con herramientas de análisis como Google Analytics?
   - ¿Qué consideraciones éticas deberían tenerse en cuenta al realizar pruebas A/B con usuarios reales?

## Actividad de Extensión

Modifica la solución propuesta para:
1. Implementar un sistema de distribución de tráfico basado en porcentajes (60% versión A, 40% versión B)
2. Agregar cabeceras HTTP personalizadas que permitan a sistemas de analítica identificar qué versión está viendo cada usuario
3. Diseñar un mecanismo para forzar la visualización de una versión específica para propósitos de prueba

---

## Notas Adicionales

### Alternativa: Implementación basada en cookies

Una alternativa a la solución basada en rutas sería implementar un sistema basado en cookies que asigne automáticamente a los usuarios a una versión y mantenga esa asignación en visitas posteriores. Esta implementación sería más adecuada para pruebas A/B auténticas donde la asignación debe ser transparente para el usuario.

### Ventajas de la solución implementada

La solución basada en rutas implementada en este caso de estudio tiene varias ventajas para un entorno educativo:
- Mayor simplicidad y facilidad de comprensión
- Depuración más sencilla
- Acceso directo a cada versión sin manipular cookies o cabeceras
- Facilidad para demostrar el concepto sin complejidades adicionales
