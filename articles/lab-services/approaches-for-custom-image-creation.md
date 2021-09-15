---
title: 'Azure Lab Services: Enfoques recomendados para crear imágenes personalizadas para laboratorios'
description: En este artículo se describen los enfoques para crear imágenes personalizadas para laboratorios.
ms.date: 07/27/2021
ms.topic: article
ms.openlocfilehash: 94a86ab6668300d736d17e52aaeaa0e615adf637
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829195"
---
# <a name="recommended-approaches-for-creating-custom-images"></a>Enfoques recomendados para crear imágenes personalizadas
En este artículo se describen estos enfoques recomendados para crear una imagen personalizada:

-   Cree y guarde una imagen personalizada de una [máquina virtual de plantilla de un laboratorio](how-to-create-manage-template.md).
-   Traslado de una imagen personalizada desde fuera del contexto de un laboratorio mediante:
    - Una [máquina virtual de Azure](https://azure.microsoft.com/services/virtual-machines/).
    - Un VHD en el entorno de laboratorio físico.

## <a name="save-a-custom-image-from-a-labs-template-vm"></a>Guardado de una imagen personalizada a partir de una máquina virtual de un laboratorio

El uso de una máquina virtual de plantilla de laboratorio para crear y guardar una imagen personalizada es la manera más sencilla de crear una imagen, ya que se admite mediante el portal de Azure Lab Services. Como resultado, tanto los departamentos de TI como los educadores pueden crear imágenes personalizadas mediante una máquina virtual de plantilla de laboratorio.

Por ejemplo, puede empezar con una de las imágenes de Azure Marketplace y, a continuación, instalar las aplicaciones de software y las herramientas necesarias para una clase. Cuando haya terminado de configurar la imagen, puede guardarla en la [galería de imágenes compartidas conectada](how-to-attach-detach-shared-image-gallery.md) para que usted y otros educadores puedan usarla para crear laboratorios nuevos.

Hay algunos puntos clave que debe tener en cuenta con este enfoque:

- Lab Services guarda automáticamente una imagen *especializada* cuando exporta la imagen de la máquina virtual de la plantilla. En la mayoría de los casos, las imágenes especializadas son adecuadas para crear laboratorios, ya que la imagen conserva información específica de la máquina y los perfiles de usuario. El uso de una imagen especializada ayuda a garantizar que el software instalado se ejecutará igual cuando use la imagen para crear laboratorios. Si necesita crear una imagen *generalizada*, debe usar uno de los otros enfoques recomendados en este artículo para crearla.

    Puede crear laboratorios basados en imágenes generalizadas y especializadas en Azure Lab Services. Para más información sobre las diferencias, consulte [Imágenes generalizadas y especializadas](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images).

- Para escenarios más avanzados de configuración de la imagen, puede resultar útil crear una imagen fuera de los laboratorios mediante una máquina virtual de Azure o un disco duro virtual desde el entorno del laboratorio físico. Lea las secciones siguientes para más información.

### <a name="use-a-labs-template-vm-to-save-a-custom-image"></a>Uso de una máquina virtual de plantilla de laboratorio para guardar una imagen personalizada 

Puede usar una máquina virtual de plantilla de laboratorio para crear imágenes personalizadas de Windows o Linux. Para más información, consulte [Guardar una imagen en la galería de imágenes compartidas](how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery).

## <a name="bring-a-custom-image-from-an-azure-vm"></a>Obtención de una imagen personalizada de una máquina virtual de Azure

Otro enfoque es usar una máquina virtual de Azure para configurar una imagen personalizada. Cuando haya terminado de configurar la imagen, puede guardarla en una galería de imágenes compartidas para que usted y sus colegas puedan usarla para crear laboratorios nuevos.

El uso de una máquina virtual de Azure proporciona más flexibilidad:
- Puede crear imágenes [generalizadas o especializadas](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images). Por el contrario, si usa una máquina virtual de plantilla de laboratorio para [exportar una imagen](how-to-use-shared-image-gallery.md), esta siempre será especializada.
- Tiene acceso a características más avanzadas de una máquina virtual de Azure que pueden resultar útiles para configurar una imagen. Por ejemplo, puede utilizar [extensiones](../virtual-machines/extensions/overview.md) para realizar la configuración y automatización posteriores a la implementación. Además, puede acceder a los [diagnósticos de arranque](../virtual-machines/boot-diagnostics.md) y la [consola serie](/troubleshoot/azure/virtual-machines/serial-console-overview) de la máquina virtual.

La configuración de una imagen mediante una máquina virtual de Azure es más compleja. Es por esto que la creación de imágenes personalizadas en máquinas virtuales de Azure suele ser responsabilidad del departamento de TI.

### <a name="use-an-azure-vm-to-set-up-a-custom-image"></a>Uso de una máquina virtual de Azure para configurar una imagen personalizada

Estos son los pasos de alto nivel para traer una imagen personalizada desde una máquina virtual de Azure:

1. Cree una [máquina virtual de Azure](https://azure.microsoft.com/services/virtual-machines/) con una imagen de Marketplace de Windows o Linux.
1. Conéctese a la máquina virtual de Azure e instale más software. También puede realizar otras personalizaciones necesarias para el laboratorio.
1. Cuando termine de configurar la imagen de la máquina virtual, [guárdela en una galería de imágenes compartidas](../virtual-machines/image-version-vm-powershell.md). Como parte de este paso, también deberá crear la definición y la versión de la imagen.
1. Una vez guardada la imagen personalizada en la galería, podrá usarla para crear laboratorios. 

Los pasos varían en función de si va a crear una imagen personalizada en Windows o Linux. Lea estos artículos para conocer los pasos detallados:

-   [Transporte de una imagen personalizada de Windows desde una máquina virtual de Azure](how-to-bring-custom-windows-image-azure-vm.md)
-   [Transporte de una imagen personalizada de Linux desde una máquina virtual de Azure](how-to-bring-custom-linux-image-azure-vm.md)

## <a name="bring-a-custom-image-from-a-vhd-in-your-physical-lab-environment"></a>Traslado de una imagen personalizada desde una VHD en su entorno de laboratorio físico

El tercer enfoque que se debe tener en cuenta es trasladar una imagen personalizada de un disco duro virtual del entorno del laboratorio físico a una galería de imágenes compartidas. Una vez que la imagen está en una galería de imágenes compartidas, usted y otros formadores podrán usarla para crear laboratorios.

Estas son algunas de las razones por las que querría usar este enfoque:

- Puede crear imágenes [generalizadas o especializadas](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) para usarlas en los laboratorios. Por el contrario, si usa una [máquina virtual de plantilla de laboratorio](how-to-use-shared-image-gallery.md) para exportar una imagen, esta siempre será especializada.
- Puede acceder a los recursos que existen en el entorno local. Por ejemplo, puede tener archivos de instalación de gran tamaño en el entorno local que tarden demasiado tiempo en copiarse en una máquina virtual de plantilla de un laboratorio.
- Puede cargar imágenes creadas con otras herramientas, como [Microsoft Endpoint Configuration Manager](/mem/configmgr/core/understand/introduction), para no tener que configurar manualmente una imagen mediante la máquina virtual de plantilla del laboratorio.

Trasladar una imagen personalizada desde un VHD es el enfoque más avanzado, ya que debe asegurarse de que la imagen esté configurada correctamente para que funcione dentro de Azure. Como resultado, los departamentos de TI suelen ser responsables de crear imágenes personalizadas a partir de discos duros virtuales.

### <a name="bring-a-custom-image-from-a-vhd"></a>Traslado de una imagen personalizada desde un disco duro virtual

Estos son los pasos de alto nivel para trasladar una imagen personalizada desde un VHD:

1. Use [Windows Hyper-V](/virtualization/hyper-v-on-windows/about/) en la máquina local para crear un VHD de Windows o Linux.
1. Conéctese a la máquina virtual de Hyper-V e instale más software. También puede realizar otras personalizaciones necesarias para el laboratorio.
1. Cuando termine de configurar la imagen, cargue el disco duro virtual para crear un [disco administrado](../virtual-machines/managed-disks-overview.md) en Azure.
1. Desde el disco administrado, cree la [definición de la imagen](../virtual-machines/shared-image-galleries.md#image-definitions) y su versión en una galería de imágenes compartidas.
1. Una vez guardada la imagen personalizada en la galería, podrá usarla para crear laboratorios. 

Los pasos varían en función de si va a crear una imagen personalizada en Windows o Linux. Lea estos artículos para conocer los pasos detallados:

-   [Traslado de una imagen personalizada de Windows desde un disco duro virtual](upload-custom-image-shared-image-gallery.md)
-   [Traslado de una imagen personalizada de Linux desde un disco duro virtual](how-to-bring-custom-linux-image-vhd.md)

## <a name="next-steps"></a>Pasos siguientes

* [Información general de Shared Image Galleries](../virtual-machines/shared-image-galleries.md)
* [Asociación o desasociación de una galería de imágenes compartidas](how-to-attach-detach-shared-image-gallery.md)
* [Uso de una galería de imágenes compartidas](how-to-use-shared-image-gallery.md)
