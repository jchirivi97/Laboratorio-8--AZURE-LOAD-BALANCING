### Escuela Colombiana de Ingeniería
### Arquitecturas de Software - ARSW

## Integrantes:

- Juan Manuel Villate

- Jimmy Armando Chirivi

## Servidor
ip: 104.42.98.236/3000

## Escalamiento en Azure con Maquinas Virtuales, Sacale Sets y Service Plans

### Dependencias
* Cree una cuenta gratuita dentro de Azure. Para hacerlo puede guiarse de esta [documentación](https://azure.microsoft.com/en-us/free/search/?&ef_id=Cj0KCQiA2ITuBRDkARIsAMK9Q7MuvuTqIfK15LWfaM7bLL_QsBbC5XhJJezUbcfx-qAnfPjH568chTMaAkAsEALw_wcB:G:s&OCID=AID2000068_SEM_alOkB9ZE&MarinID=alOkB9ZE_368060503322_%2Bazure_b_c__79187603991_kwd-23159435208&lnkd=Google_Azure_Brand&dclid=CjgKEAiA2ITuBRDchty8lqPlzS4SJAC3x4k1mAxU7XNhWdOSESfffUnMNjLWcAIuikQnj3C4U8xRG_D_BwE). Al hacerlo usted contará con $200 USD para gastar durante 1 mes.

### Parte 0 - Entendiendo el escenario de calidad

Adjunto a este laboratorio usted podrá encontrar una aplicación totalmente desarrollada que tiene como objetivo calcular el enésimo valor de la secuencia de Fibonnaci.

**Escalabilidad**
Cuando un conjunto de usuarios consulta un enésimo número (superior a 1000000) de la secuencia de Fibonacci de forma concurrente y el sistema se encuentra bajo condiciones normales de operación, todas las peticiones deben ser respondidas y el consumo de CPU del sistema no puede superar el 70%.

### Parte 1 - Escalabilidad vertical

1. Diríjase a el [Portal de Azure](https://portal.azure.com/) y a continuación cree una maquina virtual con las características básicas descritas en la imágen 1 y que corresponden a las siguientes:
    * Resource Group = SCALABILITY_LAB
    * Virtual machine name = VERTICAL-SCALABILITY
    * Image = Ubuntu Server 
    * Size = Standard B1ls
    * Username = scalability_lab
    * SSH publi key = Su llave ssh publica

![Imágen 1](images/part1/part1-vm-basic-config.png)

#### EVIDENCIA

![evidencia caracteristicas basicas](https://github.com/jchirivi97/Laboratorio-8--AZURE-LOAD-BALANCING/blob/master/images/part1/evidencias/1-lab08-part1-1.png)

![evidencia detalles finales](https://github.com/jchirivi97/Laboratorio-8--AZURE-LOAD-BALANCING/blob/master/images/part1/evidencias/2-lab08-part1-1.2.png)

![evidencia creacion de VM](https://github.com/jchirivi97/Laboratorio-8--AZURE-LOAD-BALANCING/blob/master/images/part1/evidencias/3-lab08-part1-1.3.png)

![evidencia networking](https://github.com/jchirivi97/Laboratorio-8--AZURE-LOAD-BALANCING/blob/master/images/part1/evidencias/4-lab08-part1-1.4.png)



2. Para conectarse a la VM use el siguiente comando, donde las `x` las debe remplazar por la IP de su propia VM.

    `ssh scalability_lab@xxx.xxx.xxx.xxx`
    
#### EVIDENCIA
   
![evidencia conexion]( https://github.com/jchirivi97/Laboratorio-8--AZURE-LOAD-BALANCING/blob/master/images/part1/evidencias/5-lab08-part1-2.png)

3. Instale node, para ello siga la sección *Installing Node.js and npm using NVM* que encontrará en este [enlace](https://linuxize.com/post/how-to-install-node-js-on-ubuntu-18.04/).

#### EVIDENCIA

![evidencia intalacion ](https://github.com/jchirivi97/Laboratorio-8--AZURE-LOAD-BALANCING/blob/master/images/part1/evidencias/6-lab08-part1-3.png)

4. Para instalar la aplicación adjunta al Laboratorio, suba la carpeta `FibonacciApp` a un repositorio al cual tenga acceso y ejecute estos comandos dentro de la VM:

    `git clone <your_repo>`

    `cd <your_repo>/FibonacciApp`

    `npm install`

#### EVIDENCIA
![evidencia conexion](https://github.com/jchirivi97/Laboratorio-8--AZURE-LOAD-BALANCING/blob/master/images/part1/evidencias/7-lab08-part1-4.png)

5. Para ejecutar la aplicación puede usar el comando `npm FibinacciApp.js`, sin embargo una vez pierda la conexión ssh la aplicación dejará de funcionar. Para evitar ese compartamiento usaremos *forever*. Ejecute los siguientes comando dentro de la VM.

    `npm install forever -g`

    `forever start FibinacciApp.js`

#### EVIDENCIA
![evidencia conexion](https://github.com/jchirivi97/Laboratorio-8--AZURE-LOAD-BALANCING/blob/master/images/part1/evidencias/8-lab08-part1-5.png)

6. Antes de verificar si el endpoint funciona, en Azure vaya a la sección de *Networking* y cree una *Inbound port rule* tal como se muestra en la imágen. Para verificar que la aplicación funciona, use un browser y user el endpoint `http://xxx.xxx.xxx.xxx:3000/fibonacci/6`. La respuesta debe ser `The answer is 8`.

![](images/part1/part1-vm-3000InboudRule.png)

#### EVIDENCIA
![evidencia conexion](https://github.com/jchirivi97/Laboratorio-8--AZURE-LOAD-BALANCING/blob/master/images/part1/evidencias/9-lab08-part1-6.png)

![evidencia conexion](https://github.com/jchirivi97/Laboratorio-8--AZURE-LOAD-BALANCING/blob/master/images/part1/evidencias/10-lab08-part1-6.2.png)

7. La función que calcula en enésimo número de la secuencia de Fibonacci está muy mal construido y consume bastante CPU para obtener la respuesta. Usando la consola del Browser documente los tiempos de respuesta para dicho endpoint usando los siguintes valores:

#### EVIDENCIA

   * Por motivos de internet (conexion muy lenta) solo hicimos la prueba  de algunos casos.

    * 1000000
    
![evidencia A0](https://github.com/jchirivi97/Laboratorio-8--AZURE-LOAD-BALANCING/blob/master/images/part1/evidencias/punto7/1-1000000-A0.png)

    * 1010000
    
![evidencia A0](https://github.com/jchirivi97/Laboratorio-8--AZURE-LOAD-BALANCING/blob/master/images/part1/evidencias/punto7/2-1010000-A0.png)

    * 1030000
    
![evidencia A0](https://github.com/jchirivi97/Laboratorio-8--AZURE-LOAD-BALANCING/blob/master/images/part1/evidencias/punto7/3-1030000-A0.png)

    * 1050000
    
![evidencia A0](https://github.com/jchirivi97/Laboratorio-8--AZURE-LOAD-BALANCING/blob/master/images/part1/evidencias/punto7/4-1050000-A0.png)

    * 1070000
    
![evidencia A0](https://github.com/jchirivi97/Laboratorio-8--AZURE-LOAD-BALANCING/blob/master/images/part1/evidencias/punto7/5-1070000-A0.png)

    * 1090000
    
![evidencia A0](https://github.com/jchirivi97/Laboratorio-8--AZURE-LOAD-BALANCING/blob/master/images/part1/evidencias/punto7/6-1090000-A0.png)
    


8. Dírijase ahora a Azure y verifique el consumo de CPU para la VM. (Los resultados pueden tardar 5 minutos en aparecer).

![Imágen 2](images/part1/part1-vm-cpu.png)

#### EVIDENCIA
![evidencia conexion](https://github.com/jchirivi97/Laboratorio-8--AZURE-LOAD-BALANCING/blob/master/images/part1/evidencias/13-lab08-part1-8.png)

9. Ahora usaremos Postman para simular una carga concurrente a nuestro sistema. Siga estos pasos.
    * Instale newman con el comando `npm install newman -g`. Para conocer más de Newman consulte el siguiente [enlace](https://learning.getpostman.com/docs/postman/collection-runs/command-line-integration-with-newman/).
    * Diríjase hasta la ruta `FibonacciApp/postman` en una maquina diferente a la VM.
    * Para el archivo `[ARSW_LOAD-BALANCING_AZURE].postman_environment.json` cambie el valor del parámetro `VM1` para que coincida con la IP de su VM.
    * Ejecute el siguiente comando.

    ```
    newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10 &
    newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10
    ```

#### EVIDENCIA
![evidencia conexion](https://github.com/jchirivi97/Laboratorio-8--AZURE-LOAD-BALANCING/blob/master/images/part1/evidencias/14-lab08-part1-9.png)

![evidencia conexion](https://github.com/jchirivi97/Laboratorio-8--AZURE-LOAD-BALANCING/blob/master/images/part1/evidencias/15-lab08-part1-9.png)

![evidencia conexion](https://github.com/jchirivi97/Laboratorio-8--AZURE-LOAD-BALANCING/blob/master/images/part1/evidencias/16-lab08-part1-9.2.png)

10. La cantidad de CPU consumida es bastante grande y un conjunto considerable de peticiones concurrentes pueden hacer fallar nuestro servicio. Para solucionarlo usaremos una estrategia de Escalamiento Vertical. En Azure diríjase a la sección *size* y a continuación seleccione el tamaño `B2ms`.

![Imágen 3](images/part1/part1-vm-resize.png)

#### EVIDENCIA
   * Escogimos otro tamano diferente a B2ms ya que en localizacion seleccionamos West US por que no nos permitia escoger East US 2
   * 
   
![evidencia error cambio tamano](https://github.com/jchirivi97/Laboratorio-8--AZURE-LOAD-BALANCING/blob/master/images/part1/evidencias/errorCambioTamanoB2ms.png)

![evidencia cambio tamano](https://github.com/jchirivi97/Laboratorio-8--AZURE-LOAD-BALANCING/blob/master/images/part1/evidencias/19-lab08-part110.2.png)

11. Una vez el cambio se vea reflejado, repita el paso 7, 8 y 9.

#### EVIDENCIA

   * 1000000
    
![evidencia A3](https://github.com/jchirivi97/Laboratorio-8--AZURE-LOAD-BALANCING/blob/master/images/part1/evidencias/punto7/1-1000000-A3.png)

    * 1010000
    
![evidencia A3](https://github.com/jchirivi97/Laboratorio-8--AZURE-LOAD-BALANCING/blob/master/images/part1/evidencias/punto7/2-1010000-A3.png)

    * 1030000
    
![evidencia A3](https://github.com/jchirivi97/Laboratorio-8--AZURE-LOAD-BALANCING/blob/master/images/part1/evidencias/punto7/3-1030000-A3.png)

    * 1050000
    
![evidencia A3](https://github.com/jchirivi97/Laboratorio-8--AZURE-LOAD-BALANCING/blob/master/images/part1/evidencias/punto7/4-1050000-A3.png)

    * 1070000
    
![evidencia A3](https://github.com/jchirivi97/Laboratorio-8--AZURE-LOAD-BALANCING/blob/master/images/part1/evidencias/punto7/5-1070000-A3.png)

    * 1090000
    
![evidencia A3](https://github.com/jchirivi97/Laboratorio-8--AZURE-LOAD-BALANCING/blob/master/images/part1/evidencias/punto7/6-1090000-A3.png)

![evidencia repetir punto 8](https://github.com/jchirivi97/Laboratorio-8--AZURE-LOAD-BALANCING/blob/master/images/part1/evidencias/18-lab08-part1-11.punto8.png)


12. Evalue el escenario de calidad asociado al requerimiento no funcional de escalabilidad y concluya si usando este modelo de escalabilidad logramos cumplirlo.

#### Respuesta
   *  
13. Vuelva a dejar la VM en el tamaño inicial para evitar cobros adicionales.

**Preguntas**

1. ¿Cuántos y cuáles recursos crea Azure junto con la VM?

![evidencia repetir punto 8](https://github.com/jchirivi97/Laboratorio-8--AZURE-LOAD-BALANCING/blob/master/images/part1/evidencias/3-lab08-part1-1.3.png)

   * Crea 6 al crear la VM

2. ¿Brevemente describa para qué sirve cada recurso?
  
  * network Interfaces : se comunica con el controlador de dispositivo específico de red 
  
  * Public Ip Addresses: Asigna una IP para poder acceder a la VM
  
  * Virtual Networks: bloque de creación fundamental de una red privada
  
  * Network Security Groups: Contiene las reglas de seguridad que permiten o niegan el tráfico de red entrante a, o el tráfico de red saliente
  
  * Storage Accounts : Almacenamiento de datos
   
3. ¿Al cerrar la conexión ssh con la VM, por qué se cae la aplicación que ejecutamos con el comando `npm FibonacciApp.js`? ¿Por qué debemos crear un *Inbound port rule* antes de acceder al servicio?

   * El comando `npm fibonacciApp.js` no funciona, el comando correcto para ejecutar la app es `node FibonacciApp.js`
   
   * Creamos una nueva regla de puerto para poder escuchar o usar el puerto indicado (3000) ya que no existe al crear la VM
   
4. Adjunte tabla de tiempos e interprete por qué la función tarda tando tiempo.

![evidencia tabla](https://github.com/jchirivi97/Laboratorio-8--AZURE-LOAD-BALANCING/blob/master/images/part1/evidencias/tablaCalculo.png)

   * Se debe a la capacidad de procesamiento de la VM.


5. Adjunte imágen del consumo de CPU de la VM e interprete por qué la función consume esa cantidad de CPU.

![evidencia consumo CPU](https://github.com/jchirivi97/Laboratorio-8--AZURE-LOAD-BALANCING/blob/master/images/part1/evidencias/13-lab08-part1-8.png)

   * La funcion tiene un ciclo para calcular el resultado donde el consumo depende de la cantidad de operaciones, es decir, es una programa lineal. 
   
6. Adjunte la imagen del resumen de la ejecución de Postman. Interprete:
    * Tiempos de ejecución de cada petición.
    * Si hubo fallos documentelos y explique.
    
7. ¿Cuál es la diferencia entre los tamaños `B2ms` y `B1ls` (no solo busque especificaciones de infraestructura)?

   * Las diferencias entre cualquier tipo de tamano bien sea `B2ms` y `B1ls` o las que usamos `A3` y `A0` hablando de infraestructura es el numero de vCPUs, la RAM, el numero de data disk y el precio. pero existen otras como el rendimiento base de CPU, rendimiento maximo de la CPU, redimiento maximo de almacenamiento con o sin cache, entre otros.
   
   * B2ms:
![evidencia consumo CPU](https://github.com/jchirivi97/Laboratorio-8--AZURE-LOAD-BALANCING/blob/master/images/part1/evidencias/b2.png)
   
   * B1ls:

![evidencia consumo CPU](https://github.com/jchirivi97/Laboratorio-8--AZURE-LOAD-BALANCING/blob/master/images/part1/evidencias/b1.png)

8. ¿Aumentar el tamaño de la VM es una buena solución en este escenario?, ¿Qué pasa con la FibonacciApp cuando cambiamos el tamaño de la VM?

   * No es una buena solucion, ya que lo unico que hace es incrementar costos.
   
   * No pasa nada sigue comportandose de la misma manera.
   
9. ¿Qué pasa con la infraestructura cuando cambia el tamaño de la VM? ¿Qué efectos negativos implica?

   * Al cambiar el tamano de la VM, la infraestructura no cambia, solo se caen los servicios y toca reiniciar la maquina. Se puede generar otra IP publica pero si uno lo desea puede conservar la IP.
   
   * Lo negativo, es volver a correr los servicios o en este caso volver a ejecutar el programa
   
10. ¿Hubo mejora en el consumo de CPU o en los tiempos de respuesta? Si/No ¿Por qué?

   * SI, Hubo mejora en el consumo de CPU y en los tiempos de respuesta, pero en si el problema esta en la app.
   
11. Aumente la cantidad de ejecuciones paralelas del comando de postman a `4`. ¿El comportamiento del sistema es porcentualmente mejor?

   * No, es el mismo.

### Parte 2 - Escalabilidad horizontal

#### Crear el Balanceador de Carga

Antes de continuar puede eliminar el grupo de recursos anterior para evitar gastos adicionales y realizar la actividad en un grupo de recursos totalmente limpio.

1. El Balanceador de Carga es un recurso fundamental para habilitar la escalabilidad horizontal de nuestro sistema, por eso en este paso cree un balanceador de carga dentro de Azure tal cual como se muestra en la imágen adjunta.

![](images/part2/part2-lb-create.png)

2. A continuación cree un *Backend Pool*, guiese con la siguiente imágen.

![](images/part2/part2-lb-bp-create.png)

3. A continuación cree un *Health Probe*, guiese con la siguiente imágen.

![](images/part2/part2-lb-hp-create.png)

4. A continuación cree un *Load Balancing Rule*, guiese con la siguiente imágen.

![](images/part2/part2-lb-lbr-create.png)

5. Cree una *Virtual Network* dentro del grupo de recursos, guiese con la siguiente imágen.

![](images/part2/part2-vn-create.png)

#### Crear las maquinas virtuales (Nodos)

Ahora vamos a crear 3 VMs (VM1, VM2 y VM3) con direcciones IP públicas standar en 3 diferentes zonas de disponibilidad. Después las agregaremos al balanceador de carga.

1. En la configuración básica de la VM guíese por la siguiente imágen. Es importante que se fije en la "Avaiability Zone", donde la VM1 será 1, la VM2 será 2 y la VM3 será 3.

![](images/part2/part2-vm-create1.png)

2. En la configuración de networking, verifique que se ha seleccionado la *Virtual Network*  y la *Subnet* creadas anteriormente. Adicionalmente asigne una IP pública y no olvide habilitar la redundancia de zona.

![](images/part2/part2-vm-create2.png)

3. Para el Network Security Group seleccione "avanzado" y realice la siguiente configuración. No olvide crear un *Inbound Rule*, en el cual habilite el tráfico por el puerto 3000. Cuando cree la VM2 y la VM3, no necesita volver a crear el *Network Security Group*, sino que puede seleccionar el anteriormente creado.

![](images/part2/part2-vm-create3.png)

![punto3](https://user-images.githubusercontent.com/48265107/78923243-7fa09900-7a5d-11ea-908c-8f04e45da122.jpeg)

4. Ahora asignaremos esta VM a nuestro balanceador de carga, para ello siga la configuración de la siguiente imágen.

![](images/part2/part2-vm-create4.png)

![punto4](https://user-images.githubusercontent.com/48265107/78923170-67307e80-7a5d-11ea-92cd-f0a040034951.jpeg)

5. Finalmente debemos instalar la aplicación de Fibonacci en la VM. para ello puede ejecutar el conjunto de los siguientes comandos, cambiando el nombre de la VM por el correcto




```
git clone https://github.com/daprieto1/ARSW_LOAD-BALANCING_AZURE.git

curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash
source /home/vm1/.bashrc
nvm install node

cd ARSW_LOAD-BALANCING_AZURE/FibonacciApp
npm install

npm install forever -g
forever start FibonacciApp.js
```

Realice este proceso para las 3 VMs, por ahora lo haremos a mano una por una, sin embargo es importante que usted sepa que existen herramientas para aumatizar este proceso, entre ellas encontramos Azure Resource Manager, OsDisk Images, Terraform con Vagrant y Paker, Puppet, Ansible entre otras.


![punto5](https://user-images.githubusercontent.com/48265107/78923089-48ca8300-7a5d-11ea-8472-1ad4dcba9fd2.jpeg)



#### Probar el resultado final de nuestra infraestructura

1. Porsupuesto el endpoint de acceso a nuestro sistema será la IP pública del balanceador de carga, primero verifiquemos que los servicios básicos están funcionando, consuma los siguientes recursos:

```
http://52.155.223.248/
http://52.155.223.248/fibonacci/1
```

2. Realice las pruebas de carga con `newman` que se realizaron en la parte 1 y haga un informe comparativo donde contraste: tiempos de respuesta, cantidad de peticiones respondidas con éxito, costos de las 2 infraestrucruras, es decir, la que desarrollamos con balanceo de carga horizontal y la que se hizo con una maquina virtual escalada.

3. Agregue una 4 maquina virtual y realice las pruebas de newman, pero esta vez no lance 2 peticiones en paralelo, sino que incrementelo a 4. Haga un informe donde presente el comportamiento de la CPU de las 4 VM y explique porque la tasa de éxito de las peticiones aumento con este estilo de escalabilidad.

```
newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10 &
newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10 &
newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10 &
newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10
```

**Preguntas**

* ¿Cuáles son los tipos de balanceadores de carga en Azure y en qué se diferencian?, ¿Qué es SKU, qué tipos hay y en qué se diferencian?, ¿Por qué el balanceador de carga necesita una IP pública?
* ¿Cuál es el propósito del *Backend Pool*?
* ¿Cuál es el propósito del *Health Probe*?
* ¿Cuál es el propósito de la *Load Balancing Rule*? ¿Qué tipos de sesión persistente existen, por qué esto es importante y cómo puede afectar la escalabilidad del sistema?.
* ¿Qué es una *Virtual Network*? ¿Qué es una *Subnet*? ¿Para qué sirven los *address space* y *address range*?
* ¿Qué son las *Availability Zone* y por qué seleccionamos 3 diferentes zonas?. ¿Qué significa que una IP sea *zone-redundant*?
* ¿Cuál es el propósito del *Network Security Group*?
* Informe de newman 1 (Punto 2)
* Presente el Diagrama de Despliegue de la solución.




