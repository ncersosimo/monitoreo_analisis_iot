# Ejemplos de clase

En esta práctica utilizaremos el logger de python para mejorar nuestro sistema.

Logearse desde VM y obtener cual es la dirección IP del dispositivo:
```sh
$ ifconfig
```

### 2 - Vistazo desde donde comenzamos
Desde el VSC abrir el script de "ejemplo_3_signals_logging.py", el cual viene con las librerías de logging de python importadas.
- Ya se encuentrá cargada la configuración del logger.
- El objetivo será quitar todos los "prints" del programa.


### 3 - Modificar mensajes de debugging
Comenzar por editar todos los prints utilizados dentro de la función "accel_analysis" por:
```python
logging.debug(...)
```

Estos mensajes solo son de interés cuando se está evaluando como funciona el sistema de análisis del acelerómetro, pero luego en producción no aportarán información. Es por eso que los dejaremos como DEBUG.


### 4 - Modificar mensajes de error
Editar todos los prints utilizados para informar que hubo un error, editar aquellos mensajes que informan que MQTT no pudo conectarse dentro de "on_connect":
```python
logging.error(...)
```

Estos mensajes aportarán mucha información en producción cuando el sistema falle.


### 5 - Modificar mensajes restantes
Los mensajes restantes (prints) que queden en el programa los modificaremos a mensaje de tipo INFO:
```python
logging.info(...)
```

### 6 - Verificar el funcionamiento
Lanzar el programa y repita el ensayo de encender la luz del celular, observe al finalizar el ensayo que los mensajes de logs se hayan generado correctamente.


### 7 - Visualización en un dashboard
Lanzar el dashboard y observe los logs:
- Primero coloque la consola dentro de la carpeta "log-viewer".
- Ejecuta el comando:
```sh
$ docker-compose up
```
- Esa terminal le quedará tomada hasta que cierre el dashboard.

Visualización:
- Abrir el explorador e ingresar a la siguiente URL:
```
http://<ip_VM>:8111
```

- Abrir el archivo de debug y filtrar unicamente por mensaje tipo "DEBUG". Deberá ver unicamente los mensajes relativos al análisis del acelerómetro.
- Puede ver que si su programa de python sigue en ejecución, el dashboard irá mostrando los mensajes a medida que llegan.
