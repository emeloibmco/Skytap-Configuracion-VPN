# Skytap-Configuracion-VPN
En esta guía aprenderá cómo crear una conexión VPN entre su cuenta de Skytap y una red externa, la cual para este caso será una VPN IPsec de IBM. El propósto de las VPNs es conectar las máquinas virtuales de Skytap a otra máquina de la red de conexión.

Indice:
1. [Crear y configurar VPN Skytap](#1-crear-y-configurar-vpn-skytap).
1.1. [Crear IP Pública estática](#1.1.-crear-ip-pública-estática).
1.2. [Crear una VPN](#1.2.-crear-una-vpn).
2. [Crear y configurar IPSec VPN](#2-crear-y-configurar-ipsec-vpn).
3. [Probar conexión VPN](#3-probar-conexión-entre-vpns).

## 1. Crear y configurar VPN Skytap
Acceda desde la consola de IBM al servicio de Skytap **Launch Skytap on IBM Cloud**, servicio que previamente debió adquirir.

<img width="960" alt="img1" src="https://user-images.githubusercontent.com/60628267/86811209-43bca600-c043-11ea-9c14-30a0b92662d7.PNG">

Desde la barra de navegación, haga clic en **Environments** y seleccione el ambiente en el cual creará la VPN.

### 1.1. Crear IP Pública estática 
Desde la barra de navegación, ingrese a **Manage -> Public IPs**. En este paso se creará una dirección IP pública estática la cual se utiliza como la dirección IP de Skytap para la conexión VPN. 

<img width="426" alt="img3" src="https://user-images.githubusercontent.com/60628267/86812051-2f2cdd80-c044-11ea-96d4-0666e708f537.PNG">

Haga clic en **Static Public IP address**, deberá elegir de la lista de regiones disponibles la que pertenece su ambiente de Skytap y luego haga clic en añadir (Add IP address). Como respuesta obtendrá una mensaje en pantalla: Added public IP address [Static Public IP address] in region [region].

<img width="674" alt="img4" src="https://user-images.githubusercontent.com/60628267/86812174-5388ba00-c044-11ea-9319-a436b4cc8187.PNG">

### 1.2. Crear una VPN 
Desde la barra de navegación, haga clic en **Manage -> WAN -> New VPN**. Se abrirá un recuadro como el siguiente en el cual deberá proporcionar los datos de configuración. Verifique que la casilla de **IBM peer IP** tenga la IP Pública estática que creó en el paso anterior.

<img width="263" alt="img5" src="https://user-images.githubusercontent.com/60628267/86812143-4966bb80-c044-11ea-9f7a-174a6ee62603.PNG">

Especificaciones de los parametros a configurar:
- IBM subnet: Subred a la que pertenece la máquina virtual del ambiente sobre el que se está trabajando.
- IBM Peer IP: IP Pública estática 
- Remote peer IP: IP Pública del lado remoto, es la misma **Hosted Peer Address** que se configura en IPSec VPN. (Se configurará en los próximos pasos)
- Preshared Key: Contraseña a disposición del usuario.
- NAT: OFF (no se tendrán varias máquinas conectadas al mismo ambiente por lo que no es necesario tenerlo habilitado).

En los parámetros de Fase 1 y 2, se modifican de la siguiente manera:
- Phase encryption algorithm: aes 256 (este tipo de encriptación de alta seguridad)
- Phase hash algorithm: sha256
- Group DH: modp1536

Para Fase 1:
- Key Life (in seconds): 28800

Para Fase 2:
- Key Life (in seconds): 3600

- Phase 2 perfect forward secrecy (PFS) : ON 
Las demás configuraciones se dejan por defecto como vienen.


Una vez realizada esta configuración se procede a la creación y configuración de IPSec VPN, en donde se obtendrán los parámetros necesarios para terminar esta configuración en Skytap, pero antes de eso, al guardar la configuración realizada le aparecerá en pantalla todos los detalles de la VPN. Al lado izquierdo encontrará un recuadro, tal como se muestra en la imagen a continuación.

<img width="294" alt="img9" src="https://user-images.githubusercontent.com/60628267/86848768-3966d000-c074-11ea-8f90-ae46753b7a6f.PNG">
 
Agregue la subred remota (**Hosted Private Subnets**, se le proporcionará en el próximo paso), también agregue la subred a la que pertenece la **Remote Peer IP** que acaba de configurar.

## 2. Crear y configurar IPSec VPN 

IPSec es un protocolo diseñado para autenticar y cifrar todo el tráfico de IP, mediante la conexión de VPN permite la gestión de tráfico a través de un tunel de cifrado el cual permite establecer una conexión segura. Para acceder a este servicio desde la consola de IBM Cloud acceda a **Infraestructura Clásica -> Network -> IPSec VPN**


<img width="384" alt="img6" src="https://user-images.githubusercontent.com/60628267/86841780-fdc70880-c069-11ea-923a-6312bc7e3e2b.PNG">

1. Cree el servicio haciendo clic en **Order IPSec VPN**.
2. Seleccione la ubicación donde quiere que sea provisto su servicio a crear. Se recomienda DAL13-Dallas. 
<img width="323" alt="img7" src="https://user-images.githubusercontent.com/60628267/86842310-a07f8700-c06a-11ea-86a7-bfdea5f1e433.PNG">
3. Una vez creado el nuevo IPSec se podrá visualizar la siguiente pantalla, en la cual se deberá configurar todos los parámetros de negociación.
<img width="479" alt="img8" src="https://user-images.githubusercontent.com/60628267/86842488-e5a3b900-c06a-11ea-9c96-d6e087ad9a62.PNG">

A continuación, encontrará a masa detalle que datos proporcionar para la configuración.

- Tunnel Name: Ingresar un nombre para el servicio (campo a disposición del usuario).
- Hosted Peer Address: Automáticamente al ingresar a esta pantalla se llena esta IP. Tenga presente esta IP, ya que es la misma que debe proporcionara en **Remote peer IP** en la VPN de Skytap.
- Remote Peer Address: Es la direccion de la **IP Pública estática** proporcionada en la configuración de la VPN de Skytap.
- Preshared Key: Contraseña a disposición del usuario (misma clave configurada en VPN Skytap).
- Diffie-Hellman Group: 5
- Customer Subnets: Aquí se deberan incluir las subredes a las que pertenece la VM de Skytap.
- Hosted Private Subnets: Haga clic en **+ Add Hosted Private Subnet** y seleccione la subred.

Las demás configuraciones se deberán dejar tal como se configuró la VPN de Skytap, con el fin de no tener problemas con las fases de encriptacion y autenticación.


## 3. Probar conexión entre VPNs.

Después de crear la conexión VPNs, pruébela con la herramienta de **Test WAN**.

<img width="406" alt="img10" src="https://user-images.githubusercontent.com/60628267/86849883-f7d72480-c075-11ea-9de5-9be4d5be12a2.PNG">

Ingrese una dirección IP y un número de puerto remoto desde una máquina en una de las subredes remotas incluidas. Idealmente, ingrese la dirección IP de una máquina que pueda responder a pings.

<img width="306" alt="img11" src="https://user-images.githubusercontent.com/60628267/86850111-543a4400-c076-11ea-85ff-88da9215e2c5.PNG">

Skytap realiza cuatro pruebas de conectividad y muestra los resultados, si aparecen tal como se muestra a continuación estas correctamente configuradas las dos VPN y se estan comunicando entre sí. Una vez obtenido este resultado, habilite la VPN, haciendo clic en **Enable**.






