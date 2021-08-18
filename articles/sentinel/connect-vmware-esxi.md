---
title: Conexión de datos de VMware ESXi a Azure Sentinel | Microsoft Docs
description: Obtenga información sobre cómo usar el conector de datos de VMware ESXi para extraer registros de ESXi en Azure Sentinel. Vea los datos de ESXi en los libros, cree alertas y mejore la investigación.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2021
ms.author: yelevin
ms.openlocfilehash: 2f43607e47e059e6bba9a3ae02c07262ec7309a2
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252661"
---
# <a name="connect-your-vmware-esxi-to-azure-sentinel"></a>Conexión de la instancia de VMware ESXi a Azure Sentinel

> [!IMPORTANT]
> El conector de VMware ESXi está actualmente en **VERSIÓN PRELIMINAR**. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

En este artículo se explica cómo conectar el dispositivo de VMware ESXi con Azure Sentinel. El conector de datos de VMware ESXi le permite ingerir fácilmente los registros de VMware ESXi en Azure Sentinel; esto le proporciona más información sobre las actividades ESXi de su organización y le ayuda a mejorar la capacidad de las operaciones de seguridad. La integración entre VMware ESXi y Azure Sentinel permite el uso de un servidor de Syslog con el agente de Log Analytics instalado. También usa un analizador de registro personalizado basado en una función de Kusto.

> [!NOTE]
> Los datos se almacenarán en la ubicación geográfica del área de trabajo en la que Azure Sentinel se ejecute.

## <a name="prerequisites"></a>Requisitos previos

- Debe tener permiso de lectura y escritura en el área de trabajo de Azure Sentinel.

- La solución de VMware ESXi debe estar configurada para exportar registros a través de Syslog.

## <a name="send-vmware-esxi-logs-to-the-syslog-agent"></a>Envío de registros de VMware ESXi al agente de Syslog  

Configure VMware ESXi para reenviar mensajes de Syslog a su área de trabajo de Azure Sentinel a través del agente de Syslog.
3. Siga las instrucciones que aparecen en la página **VMware ESXi**.


1. En el menú de navegación de Azure Sentinel, seleccione **Conectores de datos**.

1. En la galería **Conectores de datos**, seleccione el conector **VMware ESXi (versión preliminar)** y, a continuación, **Open connector page** (abrir página de conector).

1. Siga las instrucciones que aparecen en la página del conector **VMware ESXi**:

    1. Instale e incorpore el agente para Linux.

        - Elija una VM Linux de Azure o una máquina Linux que no sea de Azure (física o virtual).

    1. Configure los registros que se van a recopilar.

        - Seleccione los recursos y los niveles de gravedad en la **configuración de los agentes del área de trabajo**.

    1. Configuración y conexión de VMware ESXi

        - Siga estas instrucciones para configurar VMware ESXi para reenviar Syslog:
            - [VMware ESXi 3.5 y 4.x](https://kb.vmware.com/s/article/1016621)
            - [VMware ESXi 5.0 y posteriores](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.monitoring.doc/GUID-9F67DB52-F469-451F-B6C8-DAE8D95976E7.html)

            En el caso del servidor remoto, use la dirección IP de la máquina Linux en la que instaló el agente de Linux.

## <a name="validate-connectivity-and-find-your-data"></a>Validación de la conectividad y búsqueda de los datos

Los registros pueden tardar hasta 20 minutos en empezar a aparecer en Azure Sentinel. 

Una vez establecida una conexión correcta, los datos aparecen en **Registros**, en la sección *Administración del registro*, en la tabla *Syslog*.

Este conector de datos depende de un analizador basado en una función de Kusto para funcionar según lo previsto. [Siga estos pasos](https://aka.ms/sentinel-vmwareesxi-parser) para crear el alias de la función **VMwareESXi** de Kusto. Después, puede escribir `VMwareESXi` en cualquier ventana de consulta de Azure Sentinel para consultar los datos.

Consulte la pestaña **Pasos siguientes** de la página de conectores para ver algunos ejemplos de consulta útiles.

## <a name="next-steps"></a>Pasos siguientes

En este documento, aprendió a conectar VMware ESXi con Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:

- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
- [Use libros](monitor-your-data.md) para supervisar los datos.