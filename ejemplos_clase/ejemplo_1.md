# Ejemplos de clase

### 1 - Preparar el entorno de trabajo

En esta práctica utilizaremos el simulador "drone_iot" con de celular para realizar análisis de señales sobre los valores reales informados por el celular.

En caso que usted no desee user o no pueda usar el simulador "drone_iot" junto a su celular, puede utilizar el simulador "drone_mock_iot".

Logearse desde VM y obtener cual es la dirección IP del dispositivo:
```sh
$ ifconfig
```

Conectarse por ssh desde una terminal del host:
```
$ ssh inove@<ip_dispositivo>
```

Crear la carpeta "clase_5" para trabajar sobre los ejemplos de esta clase:
```sh
$ mkdir clase_5
```

Ingresar a la carpeta creada y clonar la carpeta del repositorio de esta clase:
```sh
$ cd clase_5
$ git https://github.com/InoveAlumnos/monitoreo_analisis_iot
$ cd monitoreo_analisis_iot
```

### 2 - Preparar el simulador

Abrir el Visual Studio Code y conectarse de forma remota al dispositivo. Trabajaremos sobre la carpeta recientemente clonada de este repositorio.

Clonar el repositorio del simulador de sensores:
```sh
$ git clone https://github.com/InoveAlumnos/drone_iot
```

Topicos que soporta que utilizaremos de este mock:
|             |             | datos ejemplo
| ----------  | --------    | -----
|  sensores   | gps         | {"latitude": -34.55, "longitude": -58.496}
|  sensores   | inericiales | {"heading": 160, accel: 4.5}


Desde ssh conectado a la VM, ingresar a la carpeta clonada del "drone_iot" y lanzar la aplicación:
```sh
$ python3 app.py
```

Ingresar a su explorador web e ingresar a al aplicación del drone. Recuerde ingresar con la URL en https y aceptar el ingreso "inseguro" a la app.
```
https://<ip_VM>:5010
```

### 3 - Ensayar que el simulador funcione
Utilizar el MQTTExplorar y verificar de esta manera el correcto funcionamiento de cada sensor disponible del celular.


### 4 - Vistazo desde donde comenzamos
Desde el VSC abrir el script de "ejemplo_1_signals.py", el cual viene con la conectividad a MQTT resuelta de la clase anterior (MQTT modo bridge - coneción local & dashboard). El objetivo será analizar los datos del acelerómetro, por lo que observe lo siguiente:
- Observe que al comienzo del archivo se ha creado la función "accel_analysis", la cual es invocada en el procesamiento del cliente local cuando ingresan datos del acelerómetor.
- Ya se encuentran definidos los threshold de flanco (de inicio y fin).
- Estas variables y constantes ya se encuentran definidas al comienzo del archivo


### 5 - Detección por flanco o pulso
Tome el script "ejemplo_1_signals.py" el cual viene con la conectividad a MQTT resuelta de la clase anterior (MQTT modo bridge - coneción local & dashboard).

Dentro de la función "accel_analysis" identificar cuando llega un nuevo mensaje del acelerómetro y capturar ese valor (flotante - float) para analizarlo.

Deberá generar una máquina de estados que cumpla con el siguiente comportamiento.

Comportamiento del estado "ESTADO_INICIO":
```python
if estado_sistema == ESTADO_INICIO:
    pass
    # completar el código del estado ESTADO_INICIO
```
-  Si el valor del acelerómetro supera el threshold de inicio flanco, colocar la variable en estado 1. Con esto indicaremos que estamos en presencia de un posible flanco (aún no confirmado). 
```python
if accel > THRESHOLD_INICIO:
    # Se ha detectado una aceleración
    # mayor al threshold de inicio, se pasa
    # al estado "en presencia de posible flanco"
    estado_sistema = ESTADO_PRESENCIA_FLANCO
    print(f"THRESHOLD_INICIO superado {accel}")
```

Comportamiento del estado "ESTADO_PRESENCIA_FLANCO":
```python
elif estado_sistema == ESTADO_PRESENCIA_FLANCO:
    pass
    # completar el código del estado ESTADO_PRESENCIA_FLANCO
```
- Si el valor del acelerómetro cae por debajo threshold de inicio, diremos que se cancela el flanco volviendo al estado inicial 0:
```python
if accel < THRESHOLD_INICIO:
    # La aceleración a disminuido,
    # se pasa al estado de inicio
    estado_sistema = ESTADO_INICIO
    print(f"RESET ESTADO_INICIO {accel}")
```
- Si el valor del acelerómetro continua siendo mayor al threshold inicial, nos mantendremos en el estado 1 (en presencia de un posible flanco), hasta que el valor del acelerómetro supere al threshold de fin de flanco, pasando entonces al estado 2 indicando que estamos en presencia de un flanco confirmado.
- Enviar el mensaje de enceder o apagar luz con un "publish" al MQTT remoto y pasamos el estado a valor 3, indicando que nos encontramos a la espera de que el flanco desaparezca:
```python
elif accel > THRESHOLD_FIN:
    # Se ha detectado una aceleración
    # mayor al threshold de fin de flanco, se pasa
    # al estado "flanco confirmado" y se prende la luz
    estado_sistema = ESTADO_FLANCO_CONFIRMADO

    # Invertir el estado de la luz
    estado_luz = 1 if estado_luz == 0 else 0
    
    topico = "actuadores/luces/1"            
    client.publish(topico, estado_luz)
    print(f"THRESHOLD_FIN superado {accel}")
```

Comportamiento del estado "ESTADO_PRESENCIA_FLANCO":
```python
elif estado_sistema == ESTADO_FLANCO_CONFIRMADO:
    pass
    # completar el código del estado ESTADO_FLANCO_CONFIRMADO
```
- Si el valor del acelerómetro vuele a quedar por debajo del threshold de inicio de flanco, el estado vuelve a su valor inicial 0 permitiendo que vuelva a comenzar el proceso de detección de flanco:
```python
# Se espera a que la aceleración disminuya
# y se termine el flanco ascendente
if accel < THRESHOLD_INICIO:
    # La aceleración a disminuido,
    # se pasa al estado de inicio
    estado_sistema = ESTADO_INICIO
    print(f"RESET ESTADO_INICIO {accel}")
```

__NOTA__: Recoemndamos agitar tres veces el celular para que se encienda la luz