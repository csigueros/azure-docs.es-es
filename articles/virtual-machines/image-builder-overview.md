---
title: Información sobre Azure Image Builder
description: Obtenga más información sobre Azure Image Builder para máquinas virtuales de Azure.
author: kof-f
ms.author: kofiforson
ms.date: 05/24/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: image-builder
ms.custom: references_regions
ms.reviewer: cynthn
ms.openlocfilehash: 3e27cbb2284f77abb9bd46bc6dcb7433b50a72e8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751637"
---
# <a name="azure-image-builder-overview"></a>Introducción a Azure Image Builder

Las imágenes estandarizadas de máquinas virtuales permiten a las organizaciones migrar a la nube y garantizar la coherencia de las implementaciones. Normalmente, las imágenes incluyen opciones de seguridad y de configuración predefinidas y el software necesario. La configuración de su propia canalización de creación de imágenes requiere tiempo, una infraestructura y el programa de instalación, pero con Azure VM Image Builder, basta con que proporcione una configuración que describa la imagen y la envíe al servicio para que se cree y se distribuya.
 
Azure VM Image Builder (Azure Image Builder) le permite empezar con una imagen de Azure Marketplace basada en Windows o Linux, imágenes personalizadas existentes y empezar a agregar sus propias personalizaciones. Dado que Image Builder se basa en [HashiCorp Packer](https://packer.io/), verá algunas similitudes, pero tiene la ventaja de un servicio administrado. También puede especificar dónde quiere que se hospeden sus imágenes, en la [Azure Shared Image Gallery](shared-image-galleries.md), como una imagen administrada o un VHD.


## <a name="features"></a>Características

Azure Image Builder admite las siguientes características:

- Creación de imágenes de base de referencia, que incluyen las configuraciones corporativas y de seguridad mínimas, y permiten que los departamentos puedan personalizarlas aún más.
- Integración de las aplicaciones principales, de modo que la VM puede asumir cargas de trabajo después de la creación o agregar configuraciones para admitir imágenes de Azure Virtual Desktop.
- Aplicación de revisiones de imágenes existentes. Image Builder permitirá revisar continuamente las imágenes personalizadas existentes.
- Conecte el generador de imágenes a las redes virtuales existentes para que pueda conectarse a los servidores de configuración existentes (DSC, Chef, Puppet, etc.), los recursos compartidos de archivos o cualquier otro servidor o servicio enrutable.
- Integración con Azure Shared Image Gallery, que permite distribuir, crear versiones y escalar imágenes globalmente y ofrece un sistema de administración de imágenes.
- Integración con las canalizaciones de compilación de imágenes existentes. Simplemente llame a Image Builder desde la canalización o use la tarea sencilla de Azure DevOps de Image Builder.
- Migración de una canalización de personalización de imagen existente a Azure. Use los scripts, comandos y procesos existentes para personalizar las imágenes.
- Crear imágenes en formato VHD para admitir Azure Stack.
 

## <a name="regions"></a>Regions

El servicio Azure Image Builder está disponible en [estas](./linux/image-builder-json.md#location) regiones. Las imágenes se pueden distribuir fuera de estas regiones.

## <a name="os-support"></a>SO compatible
AIB será compatible con imágenes del sistema operativo base de Azure Marketplace:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6, 7.7
- CentOS 7.6, 7.7
- SLES 12 SP4
- SLES 15, SLES 15 SP1
- Windows 10 RS5 Enterprise/Enterprise multisesión/Professional
- Windows 2016
- Windows 2019

Hemos enumerado los sistemas operativos que hemos probado y sabemos que funcionan con Azure Image Builder. Sin embargo, Azure Image Builder debería funcionar con cualquier imagen de Linux Windows en Marketplace.

## <a name="how-it-works"></a>Funcionamiento

Azure VM Image Builder es un servicio de Azure totalmente administrado al que se accede a través de un proveedor de recursos de Azure. Proporcione una configuración al servicio que especifique la imagen de origen, la personalización que se realizará y la ubicación a la que se distribuirá la nueva imagen. El diagrama siguiente muestra un flujo de trabajo de alto nivel:

![Dibujo conceptual del proceso de Azure Image Builder que muestra los orígenes (Windows/Linux), las personalizaciones (Shell, PowerShell, Reiniciar y actualizar de Windows, adición de archivos) y la distribución global con Azure Shared Image Gallery.](./media/image-builder-overview/image-builder-flow.png)

Las configuraciones de plantilla se pueden pasar mediante PowerShell, la CLI de Azure, las plantillas de ARM y, si usa la tarea DevOps de Azure VM Image Builder al realizar el envío al servicio, se creará un recurso de plantilla de imagen. Cuando se cree el recurso de plantilla de imagen, verá un grupo de recursos de almacenamiento provisional creado en la suscripción, con el formato: IT_\<DestinationResourceGroup> _\<TemplateName>_ \(GUID). El grupo de recursos de almacenamiento provisional contiene archivos y scripts a los que se hace referencia en la personalización del archivo, del shell o de PowerShell en la propiedad ScriptURI.

Para ejecutar la compilación, invocará `Run` en el recurso de plantilla de imagen. A continuación, el servicio implementará recursos adicionales para la compilación, como una VM, una red, un disco, un adaptador de red, etc. Si compila una imagen sin usar una instancia de Image Builder de red virtual, también se implementará una IP pública y un grupo NSG. El servicio se conectará a la VM de compilación mediante SSH o WinRM. Si selecciona una red virtual existente, el servicio se implementará con Azure Private Link y no se necesitará ninguna dirección IP pública. Para obtener más detalles sobre las redes de Image Builder, revise los [detalles](./linux/image-builder-networking.md).

Una vez finalizada la compilación, se eliminarán todos los recursos, salvo el grupo de recursos de almacenamiento provisional y la cuenta de almacenamiento. Para quitarlos, se eliminará el recurso de plantilla de imagen o se pueden dejar allí para volver a ejecutar la compilación.

Hay varios ejemplos y guías paso a paso en esta documentación, que hacen referencia a las plantillas de configuración y las soluciones del [repositorio de GitHub de Azure Image Builder](https://github.com/azure/azvmimagebuilder).

### <a name="move-support"></a>Compatibilidad con el movimiento
El recurso de la plantilla de imagen es inmutable y contiene vínculos a recursos y al grupo de recursos de almacenamiento provisional, por lo que el tipo de recurso no es compatible con el traslado. Si desea trasladar el recurso de plantilla de imagen, asegúrese de que tiene una copia de la plantilla de configuración (extraiga la configuración existente del recurso si no la tiene), cree un nuevo recurso de plantilla de imagen en el nuevo grupo de recursos con un nuevo nombre y elimine el recurso de plantilla de imagen anterior. 

## <a name="permissions"></a>Permisos
Al registrarse para el (AIB), se concede al servicio AIB permiso para crear, administrar y eliminar un grupo de recursos de almacenamiento provisional (IT_ *) y tener derechos para agregarle recursos, que son necesarios para la compilación de la imagen. Esto se realiza mediante un nombre de entidad de seguridad de servicio (SPN) AIB que se pone a disposición de la suscripción durante un registro correcto.

Para permitir que Azure VM Image Builder distribuya imágenes a las imágenes administradas o a Shared Image Gallery, debe crear una identidad asignada por el usuario de Azure que tenga permisos para leer y escribir imágenes. Si está accediendo a Azure Storage, necesitará permisos para leer contenedores privados y públicos.

Los permisos se explican con más detalle para [PowerShell](./linux/image-builder-permissions-powershell.md) y la [CLI de Azure](./linux/image-builder-permissions-cli.md).

## <a name="costs"></a>Costos
Se incurrirá en algunos costos de procesos, redes y almacenamiento al crear, compilar y almacenar las imágenes con Azure Image Builder. Estos costos son similares a los que conlleva la creación manual de imágenes personalizadas. En el caso de los recursos, se le cargarán las tarifas que tenga en Azure. 

Durante el proceso de creación de imagen, los archivos se descargan y se almacenan en el grupo de recursos de `IT_<DestinationResourceGroup>_<TemplateName>`, que incurrirá en costos menores de almacenamiento. Si no quiere conservarlos, elimine la **plantilla de imagen** después de la compilación de la imagen.
 
Image Builder crea una máquina virtual con un tamaño D1v2 y el almacenamiento y redes que necesita. Estos recursos estarán en vigor durante el proceso de compilación y se eliminarán una vez que Image Builder haya terminado de crear la imagen. 
 
Azure Image Builder distribuirá la imagen a las regiones elegidas, lo que podría suponer cargos de salida de red.

## <a name="hyper-v-generation"></a>Generación de Hyper-V
Actualmente, Image Builder solo admite de forma nativa la creación de imágenes de Hyper-V de la generación 1 (Gen1) en el servicio Shared Image Gallery (SIG) de Azure o en una imagen administrada. 
 
## <a name="next-steps"></a>Pasos siguientes 
 
Para probar Azure Image Builder, consulte los artículos para la compilación de imágenes de [Linux](./linux/image-builder.md) o [Windows](./windows/image-builder.md).
