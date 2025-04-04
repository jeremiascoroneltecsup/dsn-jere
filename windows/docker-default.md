## Script para Purga Completa de Docker (Linux)

**¡ADVERTENCIA!** Este script eliminará por completo todos tus contenedores, imágenes, volúmenes y la configuración principal de Docker en un sistema Linux. **Úsalo con extrema precaución y bajo tu propia responsabilidad. Asegúrate de haber realizado copias de seguridad importantes antes de ejecutarlo.**

```bash
#!/bin/bash

# Este script purga la configuración de Docker.
# ¡EJECÚTALO CON PRECAUCIÓN! PERDERÁS TODOS TUS CONTENEDORES, IMÁGENES Y VOLÚMENES.

set -e # Detener el script si un comando falla

echo "Deteniendo y eliminando todos los contenedores..."
docker stop $(docker ps -aq) || true # Ignorar error si no hay contenedores
docker rm $(docker ps -aq) || true   # Ignorar error si no hay contenedores

echo "Eliminando todas las imágenes..."
docker rmi $(docker images -aq) || true # Ignorar error si no hay imágenes

echo "Eliminando todos los volúmenes no utilizados..."
docker volume prune -f || true # Ignorar error si no hay volúmenes

echo "Eliminando todas las redes personalizadas..."
docker network prune -f || true # Ignorar error si no hay redes

echo "Deteniendo el servicio de Docker..."
sudo systemctl stop docker

echo "Eliminando el directorio de datos de Docker (requiere sudo)..."
sudo rm -rf /var/lib/docker

echo "Iniciando el servicio de Docker..."
sudo systemctl start docker

echo "¡Purgado de Docker completado!"
echo "Es posible que necesites reiniciar tu sistema para una limpieza completa."
