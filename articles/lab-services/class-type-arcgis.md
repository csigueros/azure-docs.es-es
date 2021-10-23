---
title: Configuración de un laboratorio para ArcMap\ArcGIS Desktop con Azure Lab Services | Microsoft Docs
description: Aprenda a configurar un laboratorio para clases mediante ArcGIS.
author: nicolela
ms.topic: how-to
ms.date: 02/04/2021
ms.author: nicolela
ms.openlocfilehash: dad2beae5248d8f73ceb2dfa747d0c4f45333a80
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177201"
---
# <a name="set-up-a-lab-for-arcmaparcgis-desktop"></a>Configuración de un laboratorio para ArcMap\ArcGIS Desktop

[ArcGIS](https://www.esri.com/en-us/arcgis/products/arcgis-solutions/overview) es un tipo de sistema de información geográfica (SIG).  ArcGIS se usa para crear y analizar mapas y trabajar con datos geográficos proporcionados por [Environmental Systems Research Institute](https://www.esri.com/en-us/home) (ESRI).  Aunque ArcGIS Desktop incluye varias aplicaciones, en este artículo se muestra cómo configurar laboratorios para usar ArcMap.  [ArcMap](https://desktop.arcgis.com/en/arcmap/latest/map/main/what-is-arcmap-.htm) se usa para crear, editar y analizar mapas 2D.

## <a name="lab-configuration"></a>Configuración del laboratorio

Para empezar a configurar un laboratorio para usar ArcMap, necesita una suscripción a Azure y una cuenta de laboratorio.  Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

Una vez que obtenga una suscripción a Azure, puede crear una cuenta de laboratorio en Azure Lab Services.  Para obtener más información sobre la creación de una nueva cuenta de laboratorio, consulte [Configuración de una cuenta de laboratorio](tutorial-setup-lab-account.md).  También puede usar una cuenta de laboratorio existente.

### <a name="lab-account-settings"></a>Configuración de la cuenta de laboratorio

Habilite la configuración de la cuenta de laboratorio tal y como se describe en la tabla siguiente.  Para obtener más información sobre cómo habilitar imágenes de Azure Marketplace, consulte [Especificación de las imágenes de Azure Marketplace disponibles para los creadores de laboratorios](./specify-marketplace-images.md).

| Configuración de la cuenta de laboratorio | Instructions |
| ------------------- | ------------ |
|Imagen de Marketplace| Habilite la imagen de Windows 10 Pro o Windows 10 Pro N para su uso en la cuenta de laboratorio.|

### <a name="licensing-server"></a>Servidor de licencias

Un tipo de licencia que ofrece ArcGIS Desktop son las [licencias de uso simultáneo](https://desktop.arcgis.com/en/license-manager/latest/license-manager-basics.htm).  Esto requiere la instalación de ArcGIS License Manager en el servidor de licencias.  License Manager hace un seguimiento del número de copias de software que se pueden ejecutar al mismo tiempo.  Para obtener más información sobre cómo configurar License Manager en el servidor, consulte la [ Guía de License Manager](https://desktop.arcgis.com/en/license-manager/latest/welcome.htm).

Habitualmente, el servidor de licencias se encuentra en la red local u hospedado en una máquina virtual de Azure en una instancia de Azure Virtual Network.  Una vez configurado el servidor de licencias, tendrá que [emparejar la red virtual](./how-to-connect-peer-virtual-network.md) con la [cuenta de laboratorio](./tutorial-setup-lab-account.md).  Antes debe crear el laboratorio para que las máquinas virtuales de laboratorio puedan acceder al servidor de licencias y viceversa.

Para obtener más información, vea [Configuración de un servidor de licencias como recurso compartido](how-to-create-a-lab-with-shared-resource.md).

### <a name="lab-settings"></a>Configuración del laboratorio

El tamaño de la máquina virtual (VM) que se recomienda usar para ArcGIS Desktop depende de las aplicaciones, las extensiones y las versiones específicas que vayan a usar los alumnos.  El tamaño de la VM también depende de las cargas de trabajo que se espera que realicen los alumnos.  Consulte los [Requisitos del sistema de ArcGIS Desktop](https://desktop.arcgis.com/en/system-requirements/latest/arcgis-desktop-system-requirements.htm) para obtener ayuda para identificar el tamaño de la VM.  Una vez que haya identificado el posible tamaño de la VM, le recomendamos probar las cargas de trabajo de los alumnos para garantizar un rendimiento adecuado.

En este artículo, se recomienda usar el [tamaño de VM **Medio**](administrator-guide.md#vm-sizing) para la versión [10.7.1 de ArcMap](https://desktop.arcgis.com/en/system-requirements/10.7/arcgis-desktop-system-requirements.htm), suponiendo que no se use ninguna otra extensión de ArcGIS Desktop.  Sin embargo, en función de las necesidades de la clase, puede que necesite un tamaño de VM **Grande** o incluso **GPU pequeña\mediana (visualización)** .  Por ejemplo, la [extensión Spatial Analyst](https://desktop.arcgis.com/en/arcmap/latest/tools/spatial-analyst-toolbox/gpu-processing-with-spatial-analyst.htm) que se incluye con ArcGIS Desktop admite una GPU para rendimiento mejorado, pero no requiere el uso de una GPU.

| Configuración del laboratorio | Valor y descripción |
| ------------ | ------------------ |
|Tamaño de la máquina virtual| **Media**.  Es el más adecuado para bases de datos relacionales, análisis y almacenamiento en caché en memoria.|  

### <a name="template-machine"></a>Máquina de plantilla

Los pasos de esta sección muestran cómo configurar la VM de plantilla:

1.  Inicie la VM de plantilla y conéctese a la máquina con RDP.

2.  Descargue e instale los componentes de ArcGIS Desktop siguiendo las instrucciones de ESRI.  Estos pasos incluyen la asignación del administrador de licencias para la concesión de licencias de uso simultáneo: 
    - [Introducción a la instalación y configuración de ArcGIS Desktop](https://desktop.arcgis.com/en/arcmap/latest/get-started/installation-guide/introduction.htm)

3.  Configure el almacenamiento de copia de seguridad externo para los alumnos.  Los alumnos pueden guardar los archivos directamente en la VM asignada, ya que todos los cambios que hagan se guardan entre sesiones.  Sin embargo, se recomienda que los alumnos hagan copias de seguridad de su trabajo en un almacenamiento externo a su VM por varias razones:
    - Para permitir que los alumnos tengan acceso a su trabajo una vez finalizada la clase y el laboratorio.  
    - En caso de que se dañe la VM de un alumno y sea necesario [restablecer](how-to-set-virtual-machine-passwords.md#reset-vms) la imagen.

    Con ArcGIS, cada alumno debe hacer una copia de seguridad de los archivos siguientes al final de cada sesión de trabajo:

    - Archivo mxd, que almacena la información de diseño de un proyecto.
    - Bases de datos espaciales de archivos, que almacenan todos los datos generados por ArcGIS.
    - Cualquier otro dato que el alumno pueda estar usando, como archivos de tramas, archivos de forma, GeoTIFF, etc.

    Se recomienda usar OneDrive para el almacenamiento de copia de seguridad.  Para configurar OneDrive en la VM de plantilla, siga los pasos descritos en el artículo [Instalación y configuración de OneDrive](how-to-prepare-windows-template.md#install-and-configure-onedrive). 

4.  Por último, [publique](how-to-create-manage-template.md#publish-the-template-vm) la VM de plantilla para crear las VM de los alumnos.

### <a name="auto-shutdown-and-disconnect-settings"></a>Opciones de apagado y desconexión automáticos

Las [opciones de apagado y desconexión automáticos](cost-management-guide.md#automatic-shutdown-settings-for-cost-control) de un laboratorio ayudan a asegurarse de que la VM de un alumno esté apagada cuando no esté en uso.  Esta configuración debe establecerse en función de los tipos de cargas de trabajo que tendrán los alumnos para que su VM no se apague en medio del trabajo.  Por ejemplo, la opción **Desconectar a los usuarios cuando las máquinas virtuales estén inactivas** desconecta al alumno de su sesión de RDP después de que no se detecten entradas del mouse ni del teclado durante un período de tiempo especificado.  Esta opción debe permitir el tiempo suficiente para las cargas de trabajo en las que el alumno no use activamente el mouse ni el teclado, como para ejecutar consultas largas o esperar la representación.

En el caso de ArcGIS, se recomiendan los siguientes valores para estas opciones:
- Desconectar a los usuarios cuando las máquinas virtuales estén inactivas
    - 30 minutos después de detectarse el estado de inactividad
- Apagar las máquinas virtuales cuando los usuarios se desconecten
    - 15 minutos después de que el usuario se desconecte

## <a name="cost"></a>Coste

Vamos a cubrir una posible estimación de costos para esta clase. Esta estimación no incluye el costo de ejecutar el servidor de licencias. Usaremos una clase de 25 alumnos. Hay 20 horas de tiempo de clase programado. Además, cada alumno obtiene una cuota de 10 horas para deberes o tareas fuera del tiempo de clase programado. El tamaño de la máquina virtual que elegimos es **Mediana**, que tiene 42 unidades de laboratorio.

25 alumnos \* (20 horas programadas + 10 horas de cuota) \* 42 unidades de laboratorio * 0,01 USD por hora = 315,00 USD

>[!IMPORTANT]
> La estimación de costos solo se utiliza con fines de ejemplo.  Para conocer los detalles actuales sobre los precios, consulte [Precios de Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).  

## <a name="next-steps"></a>Pasos siguientes

Los pasos siguientes son comunes a la configuración de cualquier laboratorio.

- [Creación y administración de plantillas](how-to-create-manage-template.md)
- [Incorporación de usuarios](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Establecimiento de la cuota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Establecimiento de la programación](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Vínculos de registro por correo electrónico para los alumnos](how-to-configure-student-usage.md#send-invitations-to-users)