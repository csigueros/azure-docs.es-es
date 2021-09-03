---
title: 'Azure Lab Services: Enfoques recomendados para crear imágenes personalizadas para laboratorios'
description: En este artículo se describen los enfoques para crear imágenes personalizadas para laboratorios.
ms.date: 07/27/2021
ms.topic: article
ms.openlocfilehash: aa318ad47187fd93d331c7a6f33d4699dfe42af6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780637"
---
# <a name="recommended-approaches-for-creating-custom-images"></a>Enfoques recomendados para crear imágenes personalizadas
En este artículo se describen estos enfoques recomendados para crear una imagen personalizada:

-   Cree y guarde una imagen personalizada de una [máquina virtual (VM) de plantilla de un laboratorio](how-to-create-manage-template.md).
-   Traslado de una imagen personalizada desde fuera del contexto de un laboratorio mediante:
    - Una [máquina virtual de Azure](https://azure.microsoft.com/services/virtual-machines/).
    - Un VHD en el entorno de laboratorio físico.

## <a name="save-a-custom-image-from-a-labs-template-vm"></a>Guardado de una imagen personalizada a partir de una máquina virtual de un laboratorio 

El uso de una máquina virtual de plantilla de laboratorio para crear y guardar una imagen personalizada es la manera más sencilla de crear una imagen, ya que se admite mediante el portal de Lab Services.  Como resultado, tanto los departamentos de TI como los educadores pueden crear imágenes personalizadas mediante una máquina virtual de plantilla de laboratorio.

Por ejemplo, puede empezar con una de las imágenes de Marketplace y, a continuación, instalar aplicaciones de software adicionales, herramientas, etc., que son necesarias para una clase.  Cuando haya terminado de configurar la imagen, puede guardarla en la [galería de imágenes compartidas conectada](how-to-attach-detach-shared-image-gallery.md) para que usted y otros educadores puedan usar la imagen a fin de crear laboratorios nuevos.

Hay algunos puntos clave que debe tener en cuenta con este enfoque:
-   Azure Lab Services guarda automáticamente una imagen *especializada* cuando exporta la imagen de la VM de plantilla.  En la mayoría de los casos, las imágenes especializadas son adecuadas para crear laboratorios, ya que la imagen conserva información específica de la máquina y los perfiles de usuario.  El uso de una imagen especializada ayuda a garantizar que el software instalado se ejecutará igual cuando use la imagen para crear laboratorios.  Si necesita crear una imagen *generalizada*, debe usar uno de los otros enfoques recomendados en este artículo para crearla.

    Puede crear laboratorios basados en imágenes generalizadas y especializadas en Azure Lab Services.  Para más información sobre las diferencias, lea el artículo [Imágenes generalizadas y especializadas](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images).

-   Para escenarios más avanzados con la configuración de la imagen, puede resultar útil crear una imagen fuera de los laboratorios mediante una máquina virtual de Azure o un VHD desde el entorno de laboratorio físico.  Lea las secciones siguientes para más información.

### <a name="how-to-save-a-custom-image-from-a-labs-template-vm"></a>Procedimiento para guardar una imagen personalizada a partir de una máquina virtual de un laboratorio

Puede usar una máquina virtual de plantilla de laboratorio para crear imágenes personalizadas de Windows o Linux.  Para más información, lea el artículo sobre [cómo guardar la imagen en una galería de imágenes compartidas](how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery).

## <a name="bring-a-custom-image-from-an-azure-vm"></a>Traslado de una imagen personalizada desde una máquina virtual de Azure

Otro enfoque es usar una máquina virtual de Azure para configurar una imagen personalizada.  Cuando haya terminado de configurar la imagen, puede guardarla en una galería de imágenes compartidas para que usted y sus colegas puedan usar la imagen a fin de crear laboratorios nuevos.

El uso de una máquina virtual de Azure proporciona más flexibilidad:
-   Puede crear imágenes [generalizadas o especializadas](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images).  De lo contrario, si usa la máquina virtual de plantilla de un laboratorio para [exportar una imagen](how-to-use-shared-image-gallery.md), la imagen siempre es especializada.
-   Tiene acceso a características más avanzadas de una máquina virtual de Azure que pueden resultar útiles para configurar una imagen.  Por ejemplo, puede utilizar [extensiones](../virtual-machines/extensions/overview.md) para realizar la configuración y automatización posteriores a la implementación.  Además, puede acceder a los [diagnósticos de arranque](../virtual-machines/boot-diagnostics.md) y la [consola serie](/troubleshoot/azure/virtual-machines/serial-console-overview) de la máquina virtual.

Sin embargo, es más complejo configurar una imagen mediante una VM de Azure.  Como resultado, los departamentos de TI suelen ser responsables de crear imágenes personalizadas en máquinas virtuales de Azure.

### <a name="how-to-bring-a-custom-image-from-an-azure-vm"></a>Procedimiento para trasladar una imagen personalizada de una máquina virtual de Azure

Estos son los pasos de alto nivel para traer una imagen personalizada desde una máquina virtual de Azure:

1.  Cree una [máquina virtual de Azure](https://azure.microsoft.com/services/virtual-machines/) con una imagen de Marketplace de Windows o Linux.  
1.  Conéctese a la VM de Azure e instale software adicional.  También puede realizar otras personalizaciones necesarias para el laboratorio.  
1.  Cuando termine de configurar la imagen de la VM, [guárdela en una galería de imágenes compartidas](../virtual-machines/image-version-vm-powershell.md).  Como parte de esto, también tendrá que crear la definición y la versión de la imagen.
1.  Una vez que la imagen personalizada se guarda en la galería, se puede usar para crear laboratorios.   

Los pasos varían en función de si va a crear una imagen Windows o Linux.  Lea estos artículos para conocer los pasos detallados:

-   [Procedimiento para trasladar una imagen personalizada de Windows desde una máquina virtual de Azure](how-to-bring-custom-windows-image-azure-vm.md)
-   [Procedimiento para trasladar una imagen personalizada de Linux desde una máquina virtual de Azure](how-to-bring-custom-linux-image-azure-vm.md)

## <a name="bring-a-custom-image-from-a-vhd-in-your-physical-lab-environment"></a>Traslado de una imagen personalizada desde una VHD en su entorno de laboratorio físico

El tercer enfoque que se debe tener en cuenta es trasladar una imagen personalizada de un VHD en el entorno de laboratorio físico a una galería de imágenes compartidas.  Una vez que la imagen está en una galería de imágenes compartidas, usted y otros educadores pueden usar la imagen para crear laboratorios.

Estas son algunas de las razones por las que quizás desee usar este enfoque:

-   Puede crear imágenes [generalizadas o especializadas](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) para usarlas en los laboratorios.  Por el contrario, si usa una [VM de plantilla de laboratorio](how-to-use-shared-image-gallery.md) para exportar una imagen, la imagen siempre es especializada.
-   Puede acceder a los recursos que existen en el entorno local.  Por ejemplo, puede tener archivos de instalación de gran tamaño en el entorno local que tarden demasiado tiempo en copiarse en la máquina virtual de plantilla de un laboratorio.
-   Puede cargar imágenes creadas con otras herramientas, como [Microsoft Endpoint Configuration Manager](/mem/configmgr/core/understand/introduction), para que no tenga que configurar manualmente una imagen mediante la máquina virtual de plantilla de un laboratorio.

Trasladar una imagen personalizada desde un VHD es el enfoque más avanzado, ya que debe asegurarse de que la imagen esté configurada correctamente para que funcione dentro de Azure.  Como resultado, los departamentos de TI suelen ser responsables de crear imágenes personalizadas a partir de discos duros virtuales.

### <a name="how-to-bring-a-custom-image-from-a-vhd"></a>Procedimiento para trasladar una imagen personalizada desde un VHD

Estos son los pasos de alto nivel para trasladar una imagen personalizada desde un VHD:

1.  Use [Windows Hyper-V](/virtualization/hyper-v-on-windows/about/) en la máquina local para crear un VHD de Windows o Linux.
1.  Conéctese a la VM de Hyper-V e instale software adicional.  También puede realizar otras personalizaciones necesarias para el laboratorio.  
1.  Cuando termine de configurar la imagen, cargue el VHD para crear un [disco administrado](../virtual-machines/managed-disks-overview.md) en Azure.
1.  Desde el disco administrado, cree la [definición de la imagen](../virtual-machines/shared-image-galleries.md#image-definitions) y su versión en una galería de imágenes compartidas.
1.  Una vez que la imagen personalizada se guarda en la galería, se puede usar para crear laboratorios.   

Los pasos varían en función de si va a crear una imagen Windows o Linux.  Lea estos artículos para conocer los pasos detallados:

-   [Procedimiento para trasladar una imagen personalizada de Windows desde un VHD](upload-custom-image-shared-image-gallery.md)
-   [Procedimiento para trasladar una imagen personalizada de Linux desde un VHD](how-to-bring-custom-linux-image-vhd.md)

## <a name="next-steps"></a>Pasos siguientes

* [Información general de Shared Image Galleries](../virtual-machines/shared-image-galleries.md)
* [Asociación o desasociación de una galería de imágenes compartidas](how-to-attach-detach-shared-image-gallery.md)
* [Uso de Shared Image Gallery](how-to-use-shared-image-gallery.md)