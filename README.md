# Skytap-Configuracion-VPN
En esta guía aprenderá cómo crear una conexión VPN entre su cuenta de Skytap y una red externa, la cual para este caso será una VPN IPsec de IBM. El propósto de las VPNs es conectar las máquinas virtuales de Skytap a otra máquina de la red de conexión.

Indice:
1. Crear y configurar VPN Skytap
2. Crear y configurar IPSec VPN 
3. Probar conexión VPN

## Crear y configurar VPN Skytap
Acceda desde la consola de IBM al servicio de Skytap **Launch Skytap on IBM Cloud**, servicio que previamente debió adquirir.

<img width="960" alt="img1" src="https://user-images.githubusercontent.com/60628267/86811209-43bca600-c043-11ea-9c14-30a0b92662d7.PNG">

Desde la barra de navegación, haga clic en **Environments** y seleccione el ambiente en el cual creará la VPN.

### Crear IP Pública estática 
Desde la barra de navegación, ingrese a **Manage -> Public IPs**. En este paso se creará una dirección IP pública estática la cual se utiliza como la dirección IP de Skytap para la conexión VPN. 

<img width="426" alt="img3" src="https://user-images.githubusercontent.com/60628267/86812051-2f2cdd80-c044-11ea-96d4-0666e708f537.PNG">

Haga clic en **Static Public IP address**, deberá elegir de la lista de regiones disponibles la que pertenece su ambiente de Skytap y luego haga clic en añadir (Add IP address). Como respuesta obtendrá una mensaje en pantalla: Added public IP address [Static Public IP address] in region [region].

<img width="674" alt="img4" src="https://user-images.githubusercontent.com/60628267/86812174-5388ba00-c044-11ea-9319-a436b4cc8187.PNG">

### Crear una VPN 
Desde la barra de navegación, haga clic en **Manage -> WAN -> New VPN**. Se abrirá un recuadro como el siguiente en el cual deberá proporcionar los datos de configuración. Verifique que la casilla de **IBM peer IP** tenga la IP Pública estática que creó en el paso anterior.

<img width="263" alt="img5" src="https://user-images.githubusercontent.com/60628267/86812143-4966bb80-c044-11ea-9f7a-174a6ee62603.PNG">

Especificaciones de los parametros a configurar:
- IBM subnet: Subred a la que pertenece la máquina virtual del ambiente sobre el que se está trabajando.
- IBM Peer IP: IP Pública estática 
- Remote peer IP: IP Pública del lado remoto (Se configurará en los próximos pasos)
- NAT: OFF (no se tendrán varias máquinas conectadas al mismo ambiente por lo que no es necesario tenerlo habilitado).

En los parámetros de Fase 1 y 2, se modifican de la siguiente manera:
- Phase 1 encryption algorithm: aes 256 (este tipo de encriptación de alta seguridad)
- Phase 1 hash algorithm: sha256
Las demás configuraciones se dejan por defecto como vienen.
Phase 2 perfect forward secrecy (PFS) : ON 

Una vez realizada esta configuración se procede a la creación y configuración de IPSec VPN, en donde se obtendrán los parámetros necesarios para terminar esta configuración en Skytap.



## Crear y configurar IPSec VPN 


