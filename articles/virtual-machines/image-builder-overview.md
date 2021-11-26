---
title: Información sobre Azure Image Builder
description: Obtenga más información sobre Azure Image Builder para máquinas virtuales de Azure.
author: sumit-kalra
ms.author: sukalra
ms.date: 10/15/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: image-builder
ms.custom: references_regions
ms.reviewer: cynthn
ms.openlocfilehash: 702a4b624041a20c925bfc65d92542788f381800
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551319"
---
# <a name="azure-image-builder-overview"></a>Introducción a Azure Image Builder

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado flexibles :heavy_check_mark: Conjuntos de escalado uniformes

Las imágenes estandarizadas de máquina virtual permiten a las organizaciones migrar a la nube y garantizar la coherencia de las implementaciones. Normalmente, las imágenes incluyen opciones de seguridad y de configuración predefinidas y el software necesario. La configuración de su propia canalización de creación de imágenes requiere tiempo, infraestructura y configuración. Con Azure VM Image Builder (Image Builder), solo tiene que crear una configuración que describa la imagen y enviarla al servicio, donde se crea y luego se distribuye la imagen.

Con Image Builder, puede migrar la canalización de personalización de imágenes existente a Azure mientras sigue utilizando los scripts, comandos y procesos existentes para personalizar las imágenes. Con Image Builder, puede integrar las aplicaciones principales en una imagen de máquina virtual para que las máquinas virtuales puedan asumir cargas de trabajo a la vez después de la creación. Incluso puede agregar configuraciones para crear imágenes para Azure Virtual Desktop o como VHD para su uso en Azure Stack o para facilitar la exportación.

Image Builder le permite empezar con imágenes Windows o Linux, tanto de Azure Marketplace como imágenes personalizadas existentes, y agregar sus propias personalizaciones. También puede especificar dónde quiere que se hospeden las imágenes resultantes en [Azure Compute Gallery](shared-image-galleries.md) (antes conocido como Shared Image Gallery): como una imagen administrada o como un disco duro virtual.

## <a name="features"></a>Características

Aunque es posible crear imágenes de máquina virtual personalizadas a mano o mediante otras herramientas, el proceso puede ser complicado y poco confiable. Azure VM Image Builder, que se basa en [HashiCorp Packer](https://www.packer.io/), proporciona las ventajas de un servicio administrado.

### <a name="simplicity"></a>Simplicidad

- Elimina la necesidad de usar herramientas, procesos y pasos manuales complejos para crear una imagen de máquina virtual. Image Builder abstrae todos estos detalles y oculta los requisitos específicos de Azure, como la necesidad de generalizar la imagen (sysprep), al tiempo que proporciona a los usuarios más avanzados la capacidad de invalidarlos.
- Image Builder se puede integrar con las canalizaciones de creación de imágenes existentes para una experiencia de tipo hacer clic y listo. Puede simplemente llamar a Image Builder desde la canalización o usar la [Tarea de DevOps del servicio Azure Image Builder (versión preliminar)](./linux/image-builder-devops-task.md).
- Image Builder puede capturar datos de personalización de varios orígenes, lo que elimina la necesidad de recopilarlos todos juntos en un solo lugar para crear una imagen de máquina virtual.
- La integración de Image Builder con Azure Compute Gallery proporciona un sistema de administración de imágenes que le permite distribuir, replicar y escalar imágenes globalmente, así como crear versiones de ellas. Además, puede distribuir la misma imagen resultante como un VHD o como una o varias imágenes administradas sin volver a generarlas desde cero.

### <a name="infrastructure-as-code"></a>Infraestructura como código

- No es necesario administrar la infraestructura a largo plazo (*como las cuentas de almacenamiento que van a contener los datos de personalización*) ni la infraestructura transitoria (*como la máquina virtual temporal para crear la imagen*). 
- Image Builder almacena la especificación de la creación de la imagen de máquina virtual y los artefactos de personalización como recursos de Azure, lo que elimina la necesidad de mantener las definiciones sin conexión y el riesgo de que el entorno se desvíe debido a eliminaciones o actualizaciones accidentales.

### <a name="security"></a>Seguridad

- Image Builder permite la creación de imágenes de línea base (*que incluyen las configuraciones corporativas y de seguridad mínimas*) y permiten que los distintos departamentos puedan personalizarlas aún más. Estas imágenes se pueden mantener seguras y compatibles mediante el uso de Image Builder para volver a crear rápidamente una imagen maestra con la versión más reciente con revisiones de una imagen de origen. Image Builder también facilita la creación de imágenes que cumplen la línea base de Windows en Azure. Para más información, consulte [Image Builder: plantilla de línea base de Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/imagebuilder-windowsbaseline).
- No es necesario que los artefactos de personalización sean accesibles públicamente para que Image Builder pueda capturarlos. Image Builder puede usar la [identidad administrada de Azure](../active-directory/managed-identities-azure-resources/overview.md) para capturar estos recursos y puede restringir los privilegios de esta identidad tan estrictamente como sea necesario mediante RBAC de Azure. Esto no solo significa que puede mantener los artefactos en secreto, sino que tampoco los pueden alterar actores no autorizados.
- Las copias de los artefactos de personalización, los recursos de proceso y almacenamiento transitorios, y las imágenes resultantes se almacenan de forma segura en la suscripción con el acceso controlado por RBAC de Azure. Esto incluye la máquina virtual de creación que se usa para crear la imagen personalizada y asegurarse de que los scripts y archivos de personalización no se copien en una máquina virtual desconocida de una suscripción desconocida. Además, puede lograr un alto grado de aislamiento de las cargas de trabajo de otros clientes mediante [ofertas de máquinas virtuales aisladas](./isolation.md) para la máquina virtual de creación.
- Puede conectar Image Builder a las redes virtuales existentes para que pueda comunicarse con los servidores de configuración existentes (DSC, Chef, Puppet, etc.), los recursos compartidos de archivos o cualquier otro servidor o servicio enrutable.

## <a name="regions"></a>Regions

El servicio Azure Image Builder está disponible en las siguientes regiones. 

>[!NOTE]
> Las imágenes se pueden distribuir fuera de estas regiones.
> 
- Este de EE. UU.
- Este de EE. UU. 2
- Centro-Oeste de EE. UU.
- Oeste de EE. UU.
- Oeste de EE. UU. 2
- Centro-sur de EE. UU.
- Norte de Europa
- Oeste de Europa
- Sudeste de Asia
- Sudeste de Australia
- Este de Australia
- Sur de Reino Unido
- Oeste de Reino Unido

## <a name="os-support"></a>SO compatible
Azure Image Builder será compatible con las imágenes de base de sistemas operativos de Azure Marketplace:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6, 7.7
- CentOS 7.6, 7.7
- SLES 12 SP4
- SLES 15, SLES 15 SP1
- Windows 10 RS5 Enterprise/Enterprise multisesión/Professional
- Windows 2016
- Windows 2019

>[!IMPORTANT]
> Los sistemas operativos enumerados se han probado y ahora funcionan con Azure Image Builder. Sin embargo, Azure Image Builder debería funcionar con cualquier imagen de Linux Windows en Marketplace.

## <a name="how-it-works"></a>Funcionamiento

Azure VM Image Builder es un servicio de Azure totalmente administrado al que se accede a través de un proveedor de recursos de Azure. Proporcione una configuración al servicio que especifique la imagen de origen, la personalización que se realizará y la ubicación a la que se distribuirá la nueva imagen. El diagrama siguiente muestra un flujo de trabajo de alto nivel:

![Dibujo conceptual del proceso de Azure Image Builder que muestra los orígenes (Windows/Linux), las personalizaciones (Shell, PowerShell, Reiniciar y actualizar de Windows, adición de archivos) y la distribución global con Azure Compute Gallery.](./media/image-builder-overview/image-builder-flow.png)

Las configuraciones de plantilla se pueden pasar mediante PowerShell, la CLI de Azure, plantillas de Azure Resource Manager y con la tarea de DevOps de Azure VM Image Builder; al realizar el envío al servicio, se creará un recurso de plantilla de imagen. Cuando se cree el recurso de plantilla de imagen, verá que se ha creado en la suscripción un grupo de recursos de almacenamiento provisional, con el formato: `IT_\<DestinationResourceGroup>_\<TemplateName>_\(GUID)`. El grupo de recursos de almacenamiento provisional contiene archivos y scripts a los que se hace referencia en la personalización del archivo, del shell o de PowerShell en la propiedad ScriptURI.

Para ejecutar la compilación, invocará `Run` en el recurso de plantilla de imagen. A continuación, el servicio implementará recursos adicionales para la compilación, como una VM, una red, un disco, un adaptador de red, etc. Si compila una imagen sin usar una instancia de Image Builder de red virtual, también se implementará una IP pública y un grupo NSG. El servicio se conectará a la VM de compilación mediante SSH o WinRM. Si selecciona una red virtual existente, el servicio llevará a cabo la implementación mediante Azure Private Link y no se necesitará ninguna dirección IP pública. Para más detalles, consulte [Opciones de red del servicio Azure Image Builder](./linux/image-builder-networking.md).

Una vez finalizada la compilación, se eliminarán todos los recursos, salvo el grupo de recursos de almacenamiento provisional y la cuenta de almacenamiento. Para quitarlos, se eliminará el recurso de plantilla de imagen o se pueden dejar allí para volver a ejecutar la compilación.

Hay varios ejemplos y guías paso a paso en esta documentación, que hacen referencia a las plantillas de configuración y las soluciones del [repositorio de GitHub de Azure Image Builder](https://github.com/azure/azvmimagebuilder).

### <a name="move-support"></a>Compatibilidad con el movimiento
El recurso de la plantilla de imagen es inmutable y contiene vínculos a recursos y al grupo de recursos de almacenamiento provisional, por lo que el tipo de recurso no es compatible con el traslado. Si desea trasladar el recurso de plantilla de imagen, asegúrese de que tiene una copia de la plantilla de configuración (extraiga la configuración existente del recurso si no la tiene), cree un nuevo recurso de plantilla de imagen en el nuevo grupo de recursos con un nuevo nombre y elimine el recurso de plantilla de imagen anterior. 

## <a name="permissions"></a>Permisos
Al registrarse para (AIB), se concede al servicio AIB permiso para crear, administrar y eliminar un grupo de recursos de almacenamiento provisional `(IT_*)` y tener derechos para agregarle recursos, que son necesarios para la creación de la imagen. Esto se realiza mediante un nombre de entidad de seguridad de servicio (SPN) AIB que se pone a disposición de la suscripción durante un registro correcto.

Para permitir que Azure VM Image Builder distribuya imágenes a las imágenes administradas o a Azure Compute Gallery, debe crear una identidad asignada por el usuario de Azure que tenga permisos para leer y escribir imágenes. Si está accediendo a Azure Storage, necesitará permisos para leer contenedores privados y públicos.

Los permisos se explican con más detalle para [PowerShell](./linux/image-builder-permissions-powershell.md) y la [CLI de Azure](./linux/image-builder-permissions-cli.md).

## <a name="costs"></a>Costos
Se incurrirá en algunos costos de procesos, redes y almacenamiento al crear, compilar y almacenar las imágenes con Azure Image Builder. Estos costos son similares a los que conlleva la creación manual de imágenes personalizadas. En el caso de los recursos, se le cargarán las tarifas que tenga en Azure. 

Durante el proceso de creación de imagen, los archivos se descargan y se almacenan en el grupo de recursos de `IT_<DestinationResourceGroup>_<TemplateName>`, que incurrirá en costos menores de almacenamiento. Si no quiere conservarlos, elimine la **plantilla de imagen** después de la compilación de la imagen.
 
Image Builder crea una máquina virtual con el tamaño predeterminado de máquina virtual D1v2 para imágenes Gen1 y D2ds V4 para imágenes de Gen2, junto con el almacenamiento y las redes necesarias para la máquina virtual. Estos recursos estarán en vigor durante el proceso de compilación y se eliminarán una vez que Image Builder haya terminado de crear la imagen. 
 
Azure Image Builder distribuirá la imagen a las regiones elegidas, lo que podría suponer cargos de salida de red.

## <a name="hyper-v-generation"></a>Generación de Hyper-V
Image Builder actualmente solo admite de forma nativa la creación de imágenes de la primera y segunda generación de Hyper-V en Azure Compute Gallery. 
 
## <a name="next-steps"></a>Pasos siguientes 
 
Para probar Azure Image Builder, consulte los artículos para la compilación de imágenes de [Linux](./linux/image-builder.md) o [Windows](./windows/image-builder.md).
