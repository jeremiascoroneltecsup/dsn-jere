# Caso de Estudio: Implementación de Testing A/B para Startup de E-commerce con Nginx

## Contexto del Caso

**Cliente**: FastShop, una startup de e-commerce en crecimiento que desea mejorar su experiencia de usuario.

**Problema**: El equipo de UX ha diseñado una nueva versión de la página de inicio que creen que aumentará las conversiones, pero el CEO quiere evidencia antes de implementarla completamente. Necesitan una forma de mostrar diferentes versiones de la página a los usuarios y medir el rendimiento.

**Objetivo**: Implementar un sistema de testing A/B que permita servir dos versiones diferentes de la aplicación web, asignar usuarios a cada versión mediante cookies, y facilitar la recopilación de métricas de conversión.

**Restricciones**: 
- Presupuesto limitado, prefieren soluciones basadas en contenedores
- No pueden modificar sustancialmente el código de la aplicación existente
- Necesitan una solución que pueda implementarse rápidamente

## Solución Propuesta

En este caso de estudio, implementaremos un sistema de proxy basado en Nginx que permite servir diferentes versiones de una aplicación web basada en Flask. El proxy utilizará cookies para determinar qué versión de la aplicación debe servir al usuario, facilitando así las pruebas A/B.

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

## Paso 1: Preparar el entorno de trabajo

Creamos la estructura base del proyecto:

```bash
mkdir proxy-lab
cd proxy-lab
mkdir app
```

## Paso 2: Crear las aplicaciones Flask (Simulando Versiones del Sitio Web)

Para simular las dos versiones diferentes de la página de inicio de FastShop, crearemos dos aplicaciones Flask simples:

### Aplicación A (Versión Original)

```bash
cd app
```

Creamos el archivo de la aplicación A:

```bash
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

Creamos el Dockerfile para la aplicación A:

```bash
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

### Aplicación B (Nueva Versión Propuesta por UX)

Creamos el archivo de la aplicación B, que representa el nuevo diseño propuesto por el equipo de UX:

```bash
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

## Paso 3: Construir las imágenes Docker para las aplicaciones

Volvemos al directorio principal y construimos las imágenes:

```bash
cd ..
sudo docker build -f app/Dockerfile.a -t myapp:version-a app/
sudo docker build -f app/Dockerfile.b -t myapp:version-b app/
```

## Paso 4: Configurar Nginx como proxy para pruebas A/B

En este paso, configuraremos Nginx como un proxy inverso que gestionará la distribución del tráfico entre las dos versiones de la aplicación según las cookies del usuario. Esta es la parte central de nuestra solución para las pruebas A/B:

```bash
mkdir nginx
cd nginx
```

Creamos el archivo de configuración de Nginx:

```bash
vi nginx.conf
```

Con el siguiente contenido:

```nginx
worker_processes 1;

events {
    worker_connections 1024;
}

http {
    upstream app_a {
        server app_a:80;
    }
    
    upstream app_b {
        server app_b:80;
    }
    
    server {
        listen 80;
        
        location / {
            set $backend "";
            
            if ($http_cookie ~* "version=([a-z]+)") {
                set $backend $1;
            }
            
            if ($backend = "") {
                set $backend "app_a";  # backend por defecto.
            }
            
            if ($backend = "app_a") {
                proxy_pass http://app_a;
            }
            
            if ($backend = "app_b") {
                proxy_pass http://app_b;
            }
            
            add_header Set-Cookie "version=$backend; Path=/";
        }
    }
}
```

Creamos el Dockerfile para Nginx:

```bash
vi Dockerfile
```

Con el siguiente contenido:

```dockerfile
FROM nginx:alpine
COPY nginx.conf /etc/nginx/nginx.conf
```

## Paso 5: Construir la imagen de Nginx

```bash
cd ..
docker build -t mynginx -f nginx/Dockerfile nginx
```

## Paso 6: Crear la red Docker y ejecutar los contenedores

Creamos una red Docker para que los contenedores puedan comunicarse:

```bash
docker network create my-network
```

Ejecutamos los contenedores de las aplicaciones:

```bash
docker run -d --name app_a --network my-network --network-alias app_a myapp:version-a
docker run -d --name app_b --network my-network --network-alias app_b myapp:version-b
```

Ejecutamos el proxy Nginx:

```bash
docker run -d --name nginx-proxy --network my-network -p 80:80 mynginx
```

## Paso 7: Verificar el funcionamiento del sistema de pruebas A/B

Verificamos que todos los contenedores estén en ejecución:

```bash
docker ps
```

Accedemos al contenedor de Nginx para probar el acceso a las diferentes versiones:

```bash
docker exec -it nginx-proxy /bin/sh
```

Una vez dentro del contenedor, realizamos pruebas para verificar el acceso a ambas versiones:

```bash
curl http://app_a
curl http://app_b
```

Para probar el comportamiento de las cookies, podemos hacer solicitudes al proxy:

```bash
# Primera solicitud (debería asignar versión A por defecto)
curl -v http://localhost

# Solicitud con cookie de versión A
curl -v --cookie "version=app_a" http://localhost

# Solicitud con cookie de versión B
curl -v --cookie "version=app_b" http://localhost
```

## Funcionamiento del Sistema de Pruebas A/B

1. **Asignación inicial**: Cuando un usuario accede al sitio web por primera vez, se le asigna la versión A (original) por defecto.
2. **Configuración de cookie**: Nginx establece una cookie con el valor `version=app_a` en el navegador del usuario.
3. **Visitas posteriores**: En visitas posteriores, el proxy detecta la cookie y dirige al usuario a la misma versión que vio inicialmente.
4. **Consistencia de experiencia**: Esto garantiza que cada usuario tenga una experiencia consistente durante toda su interacción con el sitio.
5. **Cambio manual de versión**: Para propósitos de prueba, el equipo de desarrollo puede modificar manualmente la cookie para ver ambas versiones.

## Integración con Sistemas de Analítica

Para medir el rendimiento de cada versión, se recomienda integrar este sistema con herramientas de analítica como Google Analytics o Mixpanel. La cookie `version` puede ser leída por scripts de seguimiento para segmentar los datos según la versión visualizada.

---

## Resultados y Análisis

La implementación de este sistema de proxy permitió a FastShop realizar pruebas A/B efectivas con las siguientes ventajas:

1. **Distribución transparente**: Los usuarios no perciben que están participando en una prueba A/B.
2. **Consistencia de experiencia**: Una vez asignada una versión, el usuario mantiene esa versión en sus visitas posteriores.
3. **Fácil rollback**: En caso de problemas, se puede modificar la configuración para dirigir a todos los usuarios a la versión estable.
4. **Escalabilidad**: El diseño basado en contenedores permite escalar horizontalmente según la demanda.

## Preguntas Críticas para el Análisis

1. **Arquitectura y Diseño**:
   - ¿Qué ventajas ofrece la arquitectura de microservicios utilizada en este caso frente a una aplicación monolítica?
   - ¿Cómo podría mejorarse la configuración de Nginx para manejar balanceo de carga además de routing basado en cookies?
   - ¿Qué consideraciones de seguridad deberían tenerse en cuenta al implementar esta solución en producción?

2. **Implementación Técnica**:
   - ¿Cómo modificarías la configuración para distribuir el tráfico 70/30 entre las versiones A y B en lugar de basarte únicamente en cookies?
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
