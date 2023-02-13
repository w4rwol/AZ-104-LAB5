# Laboratorio 05: Implementar la conectividad entre sitios

## Escenario de laboratorio
Contoso tiene sus centros de datos en las oficinas de Boston, Nueva York y Seattle conectados a través de enlaces de red de área amplia en malla, con conectividad total entre ellos. Debe implementar un entorno de laboratorio que refleje la topología de las redes locales de Contoso y verificar su funcionalidad.

**Nota:** Hay disponible una simulación de laboratorio interactiva que le permite hacer clic en este laboratorio a su propio ritmo. Es posible que encuentre ligeras diferencias entre la simulación interactiva y el laboratorio alojado, pero los conceptos e ideas centrales que se demuestran son los mismos.

## Objetivos
En este laboratorio, usted:

- Tarea 1: Aprovisionar el entorno de laboratorio
- Tarea 2: configurar el emparejamiento de redes virtuales locales y globales
- Tarea 3: probar la conectividad entre sitios

## Tiempo estimado: 30 minutos

## Diagrama de arquitectura

![Alt text](./img/0.PNG)

## Instrucciones
### Tarea 1: Aprovisionar el entorno de laboratorio
En esta tarea, implementará tres máquinas virtuales, cada una en una red virtual independiente, dos de ellas en la misma región de Azure y la tercera en otra región de Azure.

1. Inicie sesión en el portal de Azure .

2. En Azure Portal, abra Azure Cloud Shell haciendo clic en el icono en la parte superior derecha de Azure Portal.

3. Si se le solicita que seleccione Bash o PowerShell , seleccione PowerShell .

**Nota :** si es la primera vez que inicia Cloud Shell y aparece el mensaje No tiene almacenamiento montado , seleccione la suscripción que está usando en este laboratorio y haga clic en Crear almacenamiento .

4. En la barra de herramientas del panel de Cloud Shell, haga clic en el ícono Cargar/Descargar archivos , en el menú desplegable, haga clic en Cargar y cargue los archivos \Allfiles\Labs\05\az104-05-vnetvm-loop-template.json y \ Allfiles\Labs\05\az104-05-vnetvm-loop-parameters.json en el directorio principal de Cloud Shell.

5. Edite el archivo de parámetros que acaba de cargar y cambie la contraseña. Si necesita ayuda para editar el archivo en el Shell, solicite ayuda a su instructor. Como práctica recomendada, los secretos, como las contraseñas, deben almacenarse de forma más segura en Key Vault.

6. Desde el panel de Cloud Shell, ejecute lo siguiente para crear el grupo de recursos que hospedará el entorno de laboratorio. Las dos primeras redes virtuales y un par de máquinas virtuales se implementarán en [Azure_region_1]. La tercera red virtual y la tercera máquina virtual se implementarán en el mismo grupo de recursos pero en otro [Azure_region_2]. (reemplace el marcador de posición [Azure_region_1] y [Azure_region_2], incluidos los corchetes, con los nombres de dos regiones de Azure diferentes donde pretende implementar estas máquinas virtuales de Azure. Un ejemplo es $ubicación1 = 'eastus'. Puede usar Get- AzLocation para enumerar todas las ubicaciones):

```
$location1 = 'eastus'

$location2 = 'westus'

$rgName = 'az104-05-rg1'

New-AzResourceGroup -Name $rgName -Location $location1
```

**Nota :** Las regiones utilizadas anteriormente se probaron y se sabía que funcionaban cuando este laboratorio se revisó oficialmente por última vez. Si prefiere usar ubicaciones diferentes, o si ya no funcionan, deberá identificar dos regiones diferentes en las que se puedan implementar las máquinas virtuales D2Sv3 estándar.

Para identificar las regiones de Azure, desde una sesión de PowerShell en Cloud Shell, ejecute ```(Get-AzLocation).Location```

Una vez que haya identificado dos regiones que le gustaría usar, ejecute el siguiente comando en Cloud Shell para cada región para confirmar que puede implementar máquinas virtuales estándar D2Sv3.

```
az vm list-skus --location <Replace with your location> -o table --query "[? contains(name,'Standard_D2s')].name"
```

Si el comando no devuelve resultados, debe elegir otra región. Una vez que haya identificado dos regiones adecuadas, puede ajustar las regiones en el bloque de código anterior.

7. Desde el panel de Cloud Shell, ejecute lo siguiente para crear las tres redes virtuales e implementar máquinas virtuales en ellas usando la plantilla y los archivos de parámetros que cargó:

```
New-AzResourceGroupDeployment `
   -ResourceGroupName $rgName `
   -TemplateFile $HOME/az104-05-vnetvm-loop-template.json `
   -TemplateParameterFile $HOME/az104-05-vnetvm-loop-parameters.json `
   -location1 $location1 `
   -location2 $location2
```
**Nota :** Espere a que se complete la implementación antes de continuar con el siguiente paso. Esto debería tomar alrededor de 2 minutos.

8. Cierre el panel de Cloud Shell.