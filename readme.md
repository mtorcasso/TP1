# Documentación TP1 Embebidos

## Uso del IDE

### Descarga de firmware_v2 de git:
Una vez descargados los programas necesarios se procede a importar el repositorio de trabajo.
Esto se hace en la consola con los siguientes comandos
```
	git clone  https://github.com/ciaa/firmware_v2.git
	cd firmware_v2
	git status -s
	git checkout master
```
Luego se realiza una copia del archivo project.mk.template:
```
	cp project.mk.template project.mk
```
Dentro de este documento se configura el proyecto, procesador y placa. 
El procesador a usar es el lpc4337_m4, la placa es la edu_ciaa_nxp y el proyecto será el TP1.

Se importa el firmware desde la IDE de MCUXpresso de la siguiente manera:

 - File -> New -> Other -> C/C++ -> Makefile Project with Existing code

En *Existing Code Location Browse* se busca la carpeta firmware_v2, se destilda la opción C++ y se selecciona NXP MCU Tools.
Luego se hace clic derecho en firmware_v2 y se selecciona Clean Proyect, y luego Build Proyect:

![clean_build](https://github.com/mtorcasso/TP1/blob/master/img/clean_build.png)

En la consola debería verse lo siguiente:

![consola_build](https://github.com/mtorcasso/TP1/blob/master/img/consola_build.png)



### Configuración del Debug:
Se hace clic derecho en firmware_v2, se selecciona Debug As -> Debug Configurations. Se selecciona GDB OpenOCD Debug y se configura acorde a las siguientes imágenes:

- Pestaña Main

![debug_main](https://github.com/mtorcasso/TP1/blob/master/img/debug_main.png)

- Pestaña debugger

![debug_debugger](https://github.com/mtorcasso/TP1/blob/master/img/debug_debugger.png)



###  Estructura de archivos
La carpeta gpio_02_blinky tiene dos subcarpetas y un archivo independiente. Este último es el Makefile que será el que conecta los subcarpetas para su compilación. Las subcarpetas se llaman src (que contiene los archivos .c a compilar) e inc (que posee los headers de los archivos en src). 
 
 
 
### Secuencia de comandos
Luego de debuggear se procede a depurar el código. Para esto hay varios comandos útiles:
* Suspend All Debug Sessions: Pausa la depuración
* Resume All Debug Sessions: Continua la depuración
* Terminate All Debug Sessions: Finaliza la depuración
* Step Into: Ingresa a la función que se encuentra en la linea presente
* Step Over: Ejecuta la línea (sin ingresar)
* Step Return: Sale de la función a la que se entró con Step Into
* Breakpoints: Se define un punto de parada en determinada línea del código, para esto se hace clic derecho sobre dicha línea y se selecciona Toggle Breakpoint, de esta manera se puede analizar los estados de las variables en el momento que se desee.

En la siguiente imagen se muestra la barra de herramientas de debug, y la opcion de Toggle Breakpoint.

![debug_tools](https://github.com/mtorcasso/TP1/blob/master/img/debug_tools.png)



### Migración del proyecto
Se debe copiar la carpeta firmware_v2/sapi_examples/edu-ciaa-nxp/bare_metal/gpio/gpio_02_blinky y pegarla en firmware_v2/projects y renombrarla como TP1. Además, se deben renombrar los archivos `.c` y `.h` con el nombre del proyecto. Luego se debe modificar el archivo project.mk y definir un nuevo proyecto a compilar, según:

```
	PROJECT = projects/TP1
```


### Uso de GitHub
En primera instancia se crea una cuenta de GitHub y un repositorio. Luego se configuran los colaboradores del repositorio para trabajar en grupo. Finalmente, mediante Git Bash se configura el repositorio local asociado al trabajo práctico y se lo sincroniza con el repositorio remoto.

    git init 
    git remote add origin [url del repositorio] 
    git status #muestra el estado actual del repositorio 
    git add - - all 
    git status #chequeo que se hayan agregado todos los archivos
    git commit –m ”commit inicial” 
    git push -u origin master


## Blinky - Parpadeo de LEDS
Este programa tiene la función de parpadear un LED indefinidamente. En el main, se hace uso de la función gpioWrite() para setear el estado del pin correspondiente al LED, y de un delay para determinar la frecuencia de parpadeo.

![blinky.c](https://github.com/mtorcasso/TP1/blob/master/img/blinky_c.PNG)


### gpioWrite()
Esta función se encarga de setear en un pin un '1' o  un '0'.  La función hace uso de un tipo gpioMap_t y un bool_t, que determinan que LED se desea setear, y con cual estado (ON OFF). Para esto hace uso de las funciones gpioOptainPinConfig() y Chip_GPIO_SetPinState().

![gpioWrite()](https://github.com/mtorcasso/TP1/blob/master/img/gpio_write.PNG)


### gpioOptainPinConfig()
Devuelve la configuración del pin que se le pasa como primer parámetro. Se ingresa con un tipo gpioMap_t, y devuelve el nombre y la dirección del puerto y pin asociados.

![gpioOptainPinConfig()](https://github.com/mtorcasso/TP1/blob/master/img/gpioObtainPinConfig.PNG)


### Chip_GPIO_SetPinState()
Función de bajo nivel que finalmente setea el valor que se desea. Utiliza los valores de dirección provistos por la funcion gpioOptainPinConfig().

![Chip_GPIO_SetPinState()](https://github.com/mtorcasso/TP1/blob/master/img/Chip_GPIO_SetPinState.PNG)



## Compilación condicional
En la imagen se muestra un ejemplo de como se debe realizar este tipo de compilación. Esencialmente se elige que parte del código se compila, mediante el uso de defines y condicionales en tiempo de compilación. Permite evaluar distintos programas sin tener que cambiar de proyecto.

![Compilación condicional](https://github.com/mtorcasso/TP1/blob/master/img/compilacion_condicional.PNG)


## Switches LEDS
Se encarga del sensado de 5 pines (4 conectados a los pulsadores incluidos en la EDU-CIAA) y el encendido del LED respectivo a cada uno, o el activado del quinto pin. En principio se inicializa la placa mediante:
- boardconfig() inicializa el hardware, el conteo de ticks con tickinit() y configura los pines de entrada y salida acorde a la placa de desarrollo.
- gpioConfig() recibe como parámetros qué pin desea configurar y en qué modo, en base a esto lo setea como input o output, ademas permite activar el pull-up interno entre otras cosas.

![Switches LEDS](https://github.com/mtorcasso/TP1/blob/master/img/switching_leds.PNG)


### gpioConfig()
gpioConfig() recibe los tipos gpioMap_t y gpioConfig_t, luego mediante la función gpioObtainPinConfig() explicada anteriormente obtiene la dirección del pin a configurar, y mediante Chip_SCU_PinMux() configura el pin como entrada o salida.

![gpioConfig()](https://github.com/mtorcasso/TP1/blob/master/img/gpioConfig1.PNG)
![gpioConfig()](https://github.com/mtorcasso/TP1/blob/master/img/gpioConfig2.PNG)


### gpioRead()
Se encarga de ser la interfaz que le pide a la función Chip_GPIO_ReadPortBit() que lea un determinado pin de un puerto. De forma análoga a lo explicado previamente para la función gpioWrite(), utiliza la función gpioObtainPinConfig() para obtener la dirección del pin a leer.

![gpioRead()](https://github.com/mtorcasso/TP1/blob/master/img/gpioRead.PNG)


### Chip_GPIO_ReadPortBit()
Lee y devuelve en un bool_t "ret_val" el estado del pin consultado.

![Chip_GPIO_ReadPortBit()](https://github.com/mtorcasso/TP1/blob/master/img/Chip_GPIO_ReadPortBit.PNG)


## TickHook
Este bloque se encarga de encender o apagar (según corresponda en su estado anterior) de a uno, todos los LEDS  cada un determinado intervalo de tiempo. Hace uso de interrupciones de timer, y de las funciones tickConfig(), tickCallbackSet() y myTickHook().

![TickHook](https://github.com/mtorcasso/TP1/blob/master/img/TickHook.PNG)


### tickConfig() - tickInit()
tickConfig es una macro que permite pasarle un parámetro a tickInit. tickInit setea cada cuando tiempo se activará una interrupción que será atendida mediante SysTick_Handler().

![tickInit()](https://github.com/mtorcasso/TP1/blob/master/img/tickInit.PNG)


### tickCallbackSet()
Esta función configura mediante un puntero a función del tipo callBackFuncPtr_t, cual será la función a ejecutarse cuando sucede una interrupción atendida por SysTick_Handler() y los siguientes son los parámetros que esta requiere.

![tickCallbackSet()](https://github.com/mtorcasso/TP1/blob/master/img/tickCallbackSet.PNG)

### myTickHook()
Es la funcion configurada mediante tickCallbackSet() para ejecutarse cuando ocurre la interrupción. Togglea el estado de un bool_t y lo escribe en el pin asociado al LED pasado por argumento, generando el parpadeo del mismo.

![myTickHook()](https://github.com/mtorcasso/TP1/blob/master/img/myTickHook.PNG)

## 4.0 TickHook Portable
Con una serie de definiciones se puede configurar el periodo de interrupción de Tick:

    #define TICKRATE_1MS (1)            /* 1000 ticks per second */
    #define TICKRATE_10MS   (10)        /* 100 ticks per second */
    #define TICKRATE_100MS  (100)       /* 10 ticks per second */
    #define TICKRATE_MS     (TICKRATE_1MS) /* ¿? ticks per second */
Luego, con otras definiciones, se define el periodo de parpadeo del LED, normalizado al valor de TICKRATE seleccionado:

    #define LED_TOGGLE_100MS	(100)
    #define LED_TOGGLE_500MS	(500)
    #define LED_TOGGLE_1000MS	(1000)
    #define LED_TOGGLE_MS		(LED_TOGGLE_1000MS / TICKRATE_MS)
Esto permite modificar el tiempo de desborde del timer y la frecuencia de parpadeo independientemente, sólo modificando dos definiciones. Otro cambio importante es en la funcion myTickHook(): en lugar de cambiar el estado del LED en la función, simplemente se setea un flag que determina que hubo una interrupcion de Tick. Luego en el main se realiza el toggle del estado del LED una vez que el contador LED_Toggle_Counter llega a cero (acorde al período fijado por LED_TOOGLE_MS).  Tener en cuenta que mediante una sentencia Switch...Case se realiza la secuencia con todos los LED.

![TickHook portable](https://github.com/mtorcasso/TP1/blob/master/img/TickHook%20portable.PNG)

### myTickHook()
Setea un flag cada vez que ocurre la interrupción. Esto suele ser una buena práctica de programación para evitar demorar mucho dentro de una interrupción.

![myTickHook()](https://github.com/mtorcasso/TP1/blob/master/img/myTIckHook_portable.PNG)

### gpioToggle()
Conmuta el estado de un pin según su estado anterior. Se pasan por parámetro los distintos LED para generar la secuencia mediante la sentencia Switch...Case.

![gpioToggle()](https://github.com/mtorcasso/TP1/blob/master/img/gpioToggle.PNG)

## Envío de mensajes de depuración por puerto serie

El código es, en esencia, igual al del punto anterior, con el agregado de las funciones debugPrintConfigUart(), debugPrintString() y el macro DEBUG_PRINT_ENABLE. Este ultimo declara una variable global del tipo print_t que luego apuntara al UART configurado para el debug mediante la función debugPrintConfigUart().

### debugPrintConfigUart()
Es un macro que llama a la función printConfigUart(). La misma apunta el puntero mencionado anteriormente (print_t) a la dirección del UART que se configura para debug, en este caso es el conectado al chip FTDI de la placa de desarrollo. Además llama a la funcion uartConfig() que configura el puerto mencionado con un baudrate determinado.
![config_uart](https://github.com/mtorcasso/TP1/blob/master/img/config_uart.png)

### debugPrintString()
Es un macro que llama a la funcion printString(), que a su vez llama a la funcion uartWriteString(), que a su vez llama a la función uartWriteByte() para escribir caracter a caracter el string pasado por parámetro, en el UART pasado por parámetro. 

![print_string](https://github.com/mtorcasso/TP1/blob/master/img/print_string.png)

En el main se incorporo este macro cada vez que el LED_Toggle_Counter llega a cero, indicando que hubo una conmutación del estado de un LED, según se observa en la siguiente imagen:

![buclecinco.png](https://github.com/mtorcasso/TP1/blob/master/img/buclecinco.png)

En la siguiente imagen se muestran los strings recibidos utilizando el software RealTerm:

![terminal.png](https://github.com/mtorcasso/TP1/blob/master/img/terminal.png)
