# Prueba-3-Virtualizacion
Aqui se encuentran una breve explicación de los yaml que use:

### `mysql-deployment.yaml`

Este archivo define los recursos necesarios para desplegar la base de datos MySQL:

- **PersistentVolumeClaim (`mysql-pv-claim`)**: solicita 20Gi de almacenamiento persistente para asegurar que los datos no se pierdan aunque el pod se reinicie.
- **Service de tipo `ClusterIP`**: expone MySQL dentro del clúster, permitiendo que WordPress se conecte internamente por nombre DNS (`mysql`).
- **Deployment (`wordpress-mysql`)**: crea un contenedor basado en `mysql:5.6`, inicializado con una base de datos llamada `wordpress`, junto con un usuario y contraseña definidos por variables de entorno. Este contenedor monta el PVC para almacenar los datos en un volumen persistente.


### `wordpress-deployment.yaml`

Este archivo despliega WordPress y lo expone hacia la red local:

- **PersistentVolumeClaim (`wp-pv-claim`)**: reserva 20Gi de almacenamiento persistente para guardar archivos del sitio como configuraciones, imágenes o contenido subido.
- **Service de tipo `NodePort`**: expone WordPress a la red LAN mediante el puerto `30080`, permitiendo el acceso desde un navegador externo.
- **Deployment (`wordpress`)**: crea un contenedor usando la imagen `wordpress:4.8-apache`, configurado para conectarse a la base de datos MySQL usando las variables de entorno definidas. También monta el volumen persistente para guardar los datos del sitio.


### `manual-pv.yaml`

Este archivo define los volúmenes físicos necesarios para cumplir los `PersistentVolumeClaim` anteriores, ya que el clúster no tiene un `StorageClass` con provisionamiento dinámico.

- Se definen dos `PersistentVolume` con tipo `hostPath`, lo que significa que usan carpetas locales del nodo como almacenamiento.
  - Uno ubicado en `/mnt/data/mysql` para MySQL.
  - Otro en `/mnt/data/wordpress` para WordPress.

Gracias a estos volúmenes, los pods pudieron salir del estado `Pending` y funcionar correctamente con almacenamiento persistente.
