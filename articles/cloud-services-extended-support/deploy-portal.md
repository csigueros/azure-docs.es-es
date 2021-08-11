---
title: 'Implementación de una instancia de Azure Cloud Services (soporte extendido): Azure Portal'
description: Implementación de una instancia de Azure Cloud Services (soporte extendido) mediante Azure Portal
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: a147d4ca7b947c6a1cdfeec1587e9ae6b2d27fa0
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114448914"
---
# <a name="deploy-a-azure-cloud-services-extended-support-using-the-azure-portal"></a>Implementación de una instancia de Azure Cloud Services (soporte extendido) mediante Azure Portal
En este artículo se explica cómo usar Azure Portal para crear una implementación de una instancia de Cloud Services (soporte extendido). 

## <a name="before-you-begin"></a>Antes de comenzar

Consulte los [requisitos previos de implementación](deploy-prerequisite.md) de Cloud Services (soporte extendido) y cree los recursos asociados. 

## <a name="deploy-a-cloud-services-extended-support"></a>Implementación de una instancia de Cloud Services (soporte extendido) 
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com)

2.  Con la barra de búsqueda que se encuentra en la parte superior de Azure Portal, busque y seleccione **Cloud Services (soporte extendido)** .

    :::image type="content" source="media/deploy-portal-1.png" alt-text="Imagen que muestra la hoja Todos los recursos en Azure Portal":::
 
3.  En el panel Cloud Services (soporte extendido), seleccione **Crear**. 

    :::image type="content" source="media/deploy-portal-2.png" alt-text="Imagen que muestra la compra de un servicio en la nube desde el marketplace":::

4. La ventana de creación de Cloud Services (soporte extendido) se abrirá en la pestaña **Datos básicos**. 
    - Seleccione una suscripción.
    - Elija un grupo de recursos o cree uno.
    - Escriba el nombre que desee para la implementación de su instancia de Cloud Services (soporte extendido).
        - El nombre DNS del servicio en la nube es independiente y se especifica mediante la etiqueta de nombre DNS de la dirección IP pública y se puede modificar en la sección de IP pública de la pestaña de configuración.
    -  Seleccione la región en la que se va a realizar la implementación.

    :::image type="content" source="media/deploy-portal-3.png" alt-text="Imagen que muestra la hoja principal de Cloud Services (soporte extendido)":::

5. Agregue los archivos de configuración del servicio en la nube, paquetes y definiciones. Puede agregar archivos existentes desde el almacenamiento de blobs o cargarlos desde la máquina local. Si realiza la carga desde la máquina local, se almacenará en una cuenta de almacenamiento. 

    :::image type="content" source="media/deploy-portal-4.png" alt-text="Imagen que muestra la sección de carga de la pestaña Datos básicos durante la creación":::

6. Una vez completados todos los campos, desplácese hasta la pestaña **Configuración** y complétela. 
    - Seleccione una red virtual para asociarla al servicio en la nube o cree una nueva. 
        - Las implementaciones de la instancia de Cloud Services (soporte extendido) **deben** estar en una red virtual. También se **debe** hacer referencia a la red virtual en el archivo de configuración del servicio (.cscfg) en la sección `NetworkConfiguration`.
    - Seleccione una dirección IP pública existente para asociarla a la instancia de Cloud Services o cree una nueva.
        - Si tiene **puntos de conexión de entrada IP** definidos en el archivo de definición de servicio (.csdef), será necesario crear una dirección IP pública para la instancia de Cloud Services. 
        - Cloud Services (soporte extendido) solo admite la SKU de dirección IP básica.
        - Si la configuración del servicio (.cscfg) contiene una dirección IP reservada, el tipo de asignación de la dirección IP pública debe establecer en **Estática**. 
        - Opcionalmente, asigne un nombre DNS para el punto de conexión de servicio en la nube mediante la actualización de la propiedad de la etiqueta DNS de la dirección IP pública que está asociada al servicio en la nube.  
    - Si lo desea, inicie la instancia de Cloud Services. Elija iniciar o no iniciar el servicio inmediatamente después de la creación.
    - Seleccione una instancia de Key Vault. 
        - Key Vault es necesario al especificar uno o varios certificados en el archivo de configuración del servicio (.cscfg). Al seleccionar un almacén de claves, intentaremos buscar los certificados seleccionados del archivo de configuración del servicio (.cscfg) en función de sus huellas digitales. Si faltan certificados en el almacén de claves, puede cargarlos ahora y hacer clic en **Actualizar**.   

 :::image type="content" source="media/deploy-portal-5.png" alt-text="Imagen que muestra la hoja Configuración en Azure Portal al crear una instancia de Cloud Services (soporte extendido)":::

7. Una vez completados todos los campos, vaya a la pestaña **Revisar y crear** para validar la configuración de implementación y crear la instancia de Cloud Services (soporte extendido).

## <a name="next-steps"></a>Pasos siguientes 
- Vea las [preguntas más frecuentes](faq.yml) sobre Cloud Services (soporte extendido).
- Implemente una instancia de Cloud Services (soporte extendido) mediante [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), una [plantilla](deploy-template.md) o [Visual Studio](deploy-visual-studio.md).
- Visite el [repositorio de ejemplos de Cloud Services (soporte extendido)](https://github.com/Azure-Samples/cloud-services-extended-support)
