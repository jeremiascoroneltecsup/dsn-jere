#!/bin/bash

# Este script INTENTA purgar la configuración de Docker en macOS.
# ¡EJECÚTALO CON EXTREMA PRECAUCIÓN Y BAJO TU PROPIA RESPONSABILIDAD!
# SE RECOMIENDA USAR "Reset to factory defaults" EN DOCKER DESKTOP.

set -e # Detener el script si un comando falla

echo "Deteniendo y eliminando todos los contenedores..."
docker stop $(docker ps -aq) || true
docker rm $(docker ps -aq) || true

echo "Eliminando todas las imágenes..."
docker rmi $(docker images -aq) || true

echo "Eliminando todos los volúmenes no utilizados..."
docker volume prune -f || true

echo "Eliminando todas las redes personalizadas..."
docker network prune -f || true

echo "Intentando detener la aplicación Docker Desktop..."
osascript -e 'quit app "Docker"' || true
sleep 5 # Esperar a que la aplicación se cierre

echo "Intentando eliminar directorios relacionados con Docker (requiere sudo)..."
sudo rm -rf ~/Library/Containers/com.docker.docker
sudo rm -rf ~/.docker
sudo rm -rf /Applications/Docker.app # Esto solo elimina la aplicación, descomenta si lo deseas

echo "Esperando unos segundos..."
sleep 10

echo "Puede que necesites reiniciar tu Mac para una limpieza más completa."
echo "Se recomienda verificar y eliminar cualquier archivo o directorio restante relacionado con Docker."
echo "La forma MÁS SEGURA y RECOMENDADA es usar 'Reset to factory defaults' en Docker Desktop."
