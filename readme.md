##Descarga de firmware_v2 de git:
Una vez descargados los programas necesarios se procede a importar el repositorio de trabajo.
Esto se hace en la consola con los siguientes comandos:
git clone  https://github.com/ciaa/firmware_v2.git
cd firmware_v2
git status -s
git checkout master
Luego se le cambia el nombre al archivo project.mk.template:
cp project.mk.template project.mk
Dentro de este documento se configura el proyecto, procesador y placa. 
El procesador a usar es el lpc4337_m4, la placa es la edu_ciaa_nxp y el proyecto será el TP1.
Se abre el firmware con el software descargado de la siguiente manera:
File -> New -> Other -> C/C++ -> Makefile Project with Existing code
En Existing Code Location Browse se busca la carpeta firmware_v2, se destilda la opción C++ y se selecciona NXP MCU Tools.
Luego se hace clic derecho en firmware_v2 y se selecciona Build Project, se debería ver lo siguiente en la consola:
![Descripcion](URL de la imagen en github)

## Configuración del Debug:
Se hace clic derecho en firmware_v2, se selecciona Debug As -> Debug Configurations. En las pestañas Main y Debugger se deben llenar los campos como en las sigueintes dos imágenes.
En Debugger, Executable se debe seleccionar Browse y buscar dentro de la carpeta donde se instaló el OpenOCD el archivo openocd.exe.
![Debug Main](URL de la imagen en github)
![Debug Debugger](URL de la imagen en github)
Para los futuros debuggeos se pueden repetir los pasos anteriores o tocar en el escarabajo verde de la barra de herramientas

## 1.6 Estructura de archivos
La carpeta gpio_02_blinky tiene dos subcarpetas y un archivo independiente. Este último es el Makefile que será el que conecta los subcarpetas para su compilación. Las subcarpetas se llaman src (que contiene los archivos .c a compilar) e inc (que posee los headers de los archivos en src). 
 
## 1.7 Secuencia de comandos
Luego de debuggear se procede a depurar el código. Para esto hay varios comandos útiles:
*Suspend All Debug Sessions: Pausa la depuración
*Resume All Debug Sessions: Continua la depuración
*Terminate All Debug Sessions: Finaliza la depuración
*Step Into: Ingresa a la función que se encuentra en la linea siguiente
*Step Over: Ejecuta la línea siguiente
*Step Return: Sale de la función a la que se entró con Step Into
*Breakpoints: Se define un punto de parada en determinada línea del código, para esto se hace clic derecho sobre dicha línea y se selecciona Toggle Breakpoint, de esta manera se puede analizar los estados de las variables en el momento que se desee.

## Migración del proyecto
Se debe copiar la carpeta firmware_v2/sapi_examples/edu-ciaa-nxp/bare_metal/gpio/gpio_02_blinky y pegarla en firmware_v2/projects y renombrarla como TP1. Luego se debe abrir el archivo project.mk y definir un nuevo proyecto como:
PROJECT = projects/TP1

## 1.8 Blinky - Parpadeo de LEDS
Este software tiene la función de parpadear un LED indefinidamente. Hace uso de la función gpioWrite() para llevarlo a cabo.
![blinky.c](https://github.com/mtorcasso/TP1/blob/master/img/blinky_c.PNG)

### gpioWrite()
Esta función se encarga de setear en un pin un '1' o  un '0'. Para esto hace uso de las funciones gpioOptainPinConfig() y Chip_GPIO_SetPinState().
![gpioWrite()](https://github.com/mtorcasso/TP1/blob/master/img/gpio_write.PNG)

#### gpioOptainPinConfig()
Devuelve la configuración del pin que se le pasa como primer parámetro.
![gpioOptainPinConfig()](https://github.com/mtorcasso/TP1/blob/master/img/gpioObtainPinConfig.PNG)

#### Chip_GPIO_SetPinState()
Función de bajo nivel que finalmente setea el valor que se desea.
![Chip_GPIO_SetPinState()](https://github.com/mtorcasso/TP1/blob/master/img/Chip_GPIO_SetPinState.PNG)

## 1.9 GitHub
Falopita

#Compilación condicional
En la imagen se muestra un ejemplo de como se debe realizar este tipo de compilación.
![Compilación condicional](https://github.com/mtorcasso/TP1/blob/master/img/compilacion_condicional.PNG)


## 2.0 Switches LEDS
Se encarga del sensado de 5 pines (4 conectados a pulsadores) y el encendido del LED respectivo a cada uno, o el activado del quinto pin. Hace uso de las funciones gpioConfig(), gpioRead() y gpioWrite().
![Switches LEDS](https://github.com/mtorcasso/TP1/blob/master/img/switching_leds.PNG)

### gpioConfig()
Recibe como parámetros qué pin desea configurar y en qué modo, en base a esto lo setea como input o output, ya sea con pull down o con pull up.
![gpioConfig()](https://github.com/mtorcasso/TP1/blob/master/img/gpioConfig1.PNG)
![gpioConfig()](https://github.com/mtorcasso/TP1/blob/master/img/gpioConfig2.PNG)

### gpioRead()
Se encarga de ser la interfaz que le pide a la función Chip_GPIO_ReadPortBit() que lea un determinado pin de un puerto. Para eso usa la función nombrada previamente y a gpioObtainPinConfig().
![gpioRead()](https://github.com/mtorcasso/TP1/blob/master/img/gpioRead.PNG)

#### Chip_GPIO_ReadPortBit()
Lee y devuelve si es verdadero o falso que en un determinado pin haya un '1'.
![Chip_GPIO_ReadPortBit()](https://github.com/mtorcasso/TP1/blob/master/img/Chip_GPIO_ReadPortBit.PNG)

## 3.0 TickHook
Este bloque se encarga de encender o apagar (según corresponda en su estado anterior) de a uno, todos los LEDS  cada un determinado intervalo de tiempo. Hace uso de la macro tickConfig(), tickCallbackSet() y myTickHook().
![TickHook](https://github.com/mtorcasso/TP1/blob/master/img/TickHook.PNG)


### tickConfig() - tickInit()
tickConfig es una macro que permite pasarle un parámetro a tickInit. tickInit setea cada cuando tiempo se activará una interrupción que hará que se encienda el LED que sigue.
![tickInit()](https://github.com/mtorcasso/TP1/blob/master/img/tickInit.PNG)


### tickCallbackSet()
Este es un puntero a función, se le pasa como primer parámetro qué función debe ejecutarse cuando sucede una interrupción y los siguientes son los parámetros que esta requiere.
![tickCallbackSet()](https://github.com/mtorcasso/TP1/blob/master/img/tickCallbackSet.PNG)

### myTickHook()
Se fija si el estado del LED es encendido y lo apaga o viceversa cada vez qye se ingresa.
![myTickHook()](https://github.com/mtorcasso/TP1/blob/master/img/myTickHook.PNG)

## 4.0 TickHook Portable
Es similar al item anterior con la diferencia de que la función myTickHook en vez de utilizar las estructuras que provee sAPI solo hace uso de una variable global para verificar si un LED esta encendido o no.
![TickHook portable](https://github.com/mtorcasso/TP1/blob/master/img/TickHook%20portable.PNG)

### myTickHook()
Setea como encendido cada vez que entra a la interrupción.
![myTickHook()](https://github.com/mtorcasso/TP1/blob/master/img/myTIckHook_portable.PNG)

### gpioToggle()
Conmuta el estado de un pin según su estado anterior.
![gpioToggle()](https://github.com/mtorcasso/TP1/blob/master/img/gpioToggle.PNG)
