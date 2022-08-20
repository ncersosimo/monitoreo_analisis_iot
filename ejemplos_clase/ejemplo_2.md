# Ejemplos de clase

En esta práctica utilizaremos el logger de python y un dashboard para analizar logs.

Logearse desde VM y obtener cual es la dirección IP del dispositivo:
```sh
$ ifconfig
```

### 2 - Vistazo desde donde comenzamos
Desde el VSC abrir el script de "ejemplo_2_logging.py", el cual viene con las librerías de logging de python importadas.
- Se generará la carpeta "logs" en donde se almacenarán los logs del sistema.
- Comenzaremos por configurar como desearemos que funcione el logger, utilizando el archivo "logging.json" que contiene toda la información de nuestros logs.
- Realizaremos algunas pruebas y compararemos los resultados en cada archivo generado.

### 3 - Lanzar el simulador drone iot
Comenzaremos por agregar dentro del bloque principal la configuración del logger:
```python
# Configurar el logger
with open("logging.json", 'rt') as f:
    config = json.load(f)
    logging.config.dictConfig(config)
```

Realizar algunos ensayos de logging de error, warning, info y debug:
```python
# Realizar algunas pruebas
logging.info('¡Hola Mundo!')
logging.error('Mqtt Local connection faild')
logging.warning('No se pudo enviar el paquete MQTT')
logging.debug('THRESHOLD_FIN 15.9')
```

Lanzar el programa, observe que se creará la carpeta logs con los distintos archivos.

### 4 - Ensayar distintas configuraciones
- Observar el contenido de cada archivo, verá que en el archivo "errors" solo hay logs de errores o críticos.
- Observe que en el algo info hay más información, como por ejemplo los mensajes de INFO
- Observe que el archivo debug tiene el mismo contenido que el archivo INFO, no hay mensajes de DEBUG allí.

Cambiar el nivel de logging del archivo de configuración para mostrar mensajes de DEBUG:
- Abrir el archivo de configuración "logging.json".
- Modifique el nivel de logging que se encuentra entre las últimas líneas, dentro del diccionario "root" modificar "level: INFO" por "level: DEBUG".

Observar los resultados:
- Lanzar nuevamente el programa.
- Observe que ahora los mensajes de DEBUG aparecen en la consola.
- Observe que ahora los mensajes de DEBUG aparecen en el archivo DEBUG.

Conclusión:
```
El nivel de debug de "root" controla el nivel general de todo lo que se logea, luego cada archivo tiene su propio nivel de logging que aceptará.
```

### 5 - Visualización en un dashboard
Clonar el repositorio del visualizador de logs llamado "log-viewer":
```sh
$ git clone https://github.com/hernancontigiani/log-viewer.git
```

Ingresar a la carpeta "log-viewer" y abrir el archivo "docker-compose.yml":
- Buscar la línea del archivo "source: ./demo_python/logs".
- Debemos modificar esta ruta con la ruta completa a la carpeta de logs creada durante los ensayos.
- Para conocer la ruta completa a la carpeta de logs, ingrese con la consola a esa carpeta y ejecute el siguiente comando:
```sh
pwd
```
- Copie ese path dentro del archivo "docker-compose.yml" y salvelo

Lanzar el dashboard:
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

Una vez ingresado la dashboard, ingresar a la carpeta "logs":
- Abrir uno de los archivos de logs (por ejemplo, el de DEBUG).
- Podrá ver que los campos de fecha y tipo de log tienen un color especial.
- Puede filtrar los mensajes por fecha o por tipo.
- Puede usar el buscador para filtrar los logs por una palabra especial.

### 6 - Cerrar el dashboard
Para cerrar el dashboard y recuperar la consola, cierre el docker con "CTRL + C".