# Ejemplos de clase

En esta práctica no es necesario utilizar ningún simulador, trabajaremos sobre el ejemplo anterior.

Logearse desde VM y obtener cual es la dirección IP del dispositivo:
```sh
$ ifconfig
```

### 1 - Observar keepalive request
El dashboard envía una vez por segundo un mensaje de keepalive request a todos los usuarios logeados, el objetivo será recibir y observar este mensaje:
- Debe tener configurado en el archivo .env el mismo usuario de campus que utiliza para logearse para que le llege este mensaje.
- Ingresar al dashboardiot y logearse en el con su usuario del campus.
```
dashboardiot/<usuario_campus>/keepalive/request
```

Utilizar el MQTTExplorar o Mosquitto Sub conectado al MQTT remoto para verificar que está llegando este mensaje.

Si hay más de una persona logeada en el dashboard lo podrá saber suscribiendose al siguiente tópico:
```
dashboardiot/+/keepalive/request
```

### 2 - Enviar el keepalive acknowledge (ack)
Utilizar el MQTTExplorar o Mosquitto Sub conectado al MQTT remoto para enviar la notificación de keepalive ack al dashboard con su usuario del campus:
- Tópico:
```
dashboardiot/<usuario_campus>/keepalive/ack
```
- Dato:
```
1
```

Si todo funciona correctamente, en la sección de usuarios del dashboard deberá ver como aparece su usuario y se actualiza la cantidad de paquetes recibidos cada vez que publica al tópico.

En caso de que otros alumnos estén haciendo el desafio al mismo tiempo también recibirá en el dashboard sus mensajes con sus usuarios del campus.


### 3 - Agregar funcionalidad de keepalive al bridge
Desde el VSC abrir el script de "ejemplo_5_keepalive.py", el cual viene con todo el sistema resuelto de bridge y logging.
- Dentro de la función "on_connect_remoto" agregue la suscripción al tópico de keepalive request:
```
client.subscribe(config["DASHBOARD_TOPICO_BASE"] + "keepalive/request")
```
- Dentro de la función "procesamiento_remoto" deberá analizar si recibe el tópico recibido en el mensaje a procesar es "keepalive/request". Si el tópico recibido es el esperado, enviar un mensaje al tópico keepalive/ack
```
# Analizar topico recibido
if topico == "keepalive/request":
    # Enviar respuesta de keepalive
    topico_remoto = config["DASHBOARD_TOPICO_BASE"] + "keepalive/ack"
    client_remoto.publish(topico_remoto, "1")
```

Si todo funciona correctamente, en la sección de usuarios del dashboard deberá ver como aparece su usuario y se actualiza la cantidad de paquetes recibidos una vez por segundo.

En caso de que otros alumnos estén haciendo el desafio al mismo tiempo también recibirá en el dashboard sus mensajes con sus usuarios del campus.