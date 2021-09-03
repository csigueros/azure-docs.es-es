---
title: Script para habilitar kdump en SAP HANA (instancias grandes) | Microsoft Docs
description: Aprenda a habilitar el servicio kdump en Azure HANA (instancias grandes), Tipo I y Tipo II.
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/22/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 272ac309629c62ee9fc7d12236aac4b2c3106b8a
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/23/2021
ms.locfileid: "112540931"
---
# <a name="kdump-for-sap-hana-on-azure-large-instances"></a>kdump para SAP HANA en Azure (instancias grandes)

En este artículo, se explica cómo habilitar el servicio kdump en Instancias grandes de HANA (HLI) **Tipo I y Tipo II** de Azure.

Es necesario configurar y habilitar kdump para solucionar los problemas de bloqueos del sistema que no tienen una causa evidente. En ocasiones un bloqueo del sistema no se puede explicar con un problema de hardware o infraestructura. En esos casos, un sistema operativo o una aplicación pueden haber causado el problema. kdump permitirá a SUSE determinar el motivo del bloqueo del sistema.

## <a name="supported-skus"></a>SKU compatibles

|  Tipo de instancia grande de HANA   |  Proveedor del SO   |  Versión del paquete del SO   |  SKU |
|-----------------------------|--------------|-----------------------|-------------|
|   Tipo I                    |  SuSE        |   SLES 12 SP3         |  S224 m      |
|   Tipo I                    |  SuSE        |   SLES 12 SP4         |  S224 m      |
|   Tipo I                    |  SuSE        |   SLES 12 SP2         |  S72        |
|   Tipo I                    |  SuSE        |   SLES 12 SP2         |  S72m       |
|   Tipo I                    |  SuSE        |   SLES 12 SP3         |  S72m       |
|   Tipo I                    |  SuSE        |   SLES 12 SP2         |  S96        |
|   Tipo I                    |  SuSE        |   SLES 12 SP3         |  S96        |
|   Tipo I                    |  SuSE        |   SLES 12 SP2         |  S192       |
|   Tipo I                    |  SuSE        |   SLES 12 SP3         |  S192       |
|   Tipo I                    |  SuSE        |   SLES 12 SP4         |  S192       |
|   Tipo I                    |  SuSE        |   SLES 12 SP2         |  S192m      |
|   Tipo I                    |  SuSE        |   SLES 12 SP3         |  S192m      |
|   Tipo I                    |  SuSE        |   SLES 12 SP4         |  S192m      |
|   Tipo I                    |  SuSE        |   SLES 12 SP2         |  S144       |
|   Tipo I                    |  SuSE        |   SLES 12 SP3         |  S144       |
|   Tipo I                    |  SuSE        |   SLES 12 SP2         |  S144m      |
|   Tipo I                    |  SuSE        |   SLES 12 SP3         |  S144m      |
|   Tipo II                   |  SuSE        |   SLES 12 SP2         |  S384       |
|   Tipo II                   |  SuSE        |   SLES 12 SP3         |  S384       |
|   Tipo II                   |  SuSE        |   SLES 12 SP4         |  S384       |
|   Tipo II                   |  SuSE        |   SLES 12 SP2         |  S384xm     |
|   Tipo II                   |  SuSE        |   SLES 12 SP3         |  S384xm     |
|   Tipo II                   |  SuSE        |   SLES 12 SP4         |  S384xm     |
|   Tipo II                   |  SuSE        |   SLES 12 SP2         |  S576m      |
|   Tipo II                   |  SuSE        |   SLES 12 SP3         |  S576m      |
|   Tipo II                   |  SuSE        |   SLES 12 SP4         |  S576m      |

## <a name="prerequisites"></a>Requisitos previos

- El servicio kdump usa el directorio `/var/crash` para escribir volcados. Asegúrese de que la partición correspondiente a este directorio tenga espacio suficiente para alojar los volcados.

## <a name="setup-details"></a>Detalles de la configuración

- El script para habilitar kdump se puede encontrar en [Azure sap-hana-tools en GitHub](https://github.com/Azure/sap-hana-tools/blob/master/tools/enable-kdump.sh).

> [!NOTE]
> Este script se ha creado en función de la configuración del laboratorio. Deberá ponerse en contacto con el proveedor del sistema operativo para cualquier ajuste adicional.
> Se aprovisionará un número de unidad lógica (LUN) independiente para los servidores nuevos y existentes a fin de guardar los volcados. Un script se encargará de configurar el sistema de archivos fuera del LUN.
> Microsoft no será responsable del análisis del volcado de memoria. Tendrá que abrir una incidencia de soporte técnico para que el proveedor del sistema operativo la analice.

- Use el comando siguiente para ejecutar este en la instancia grande de HANA:

    > [!NOTE]
    > Se necesitan privilegios sudo para ejecutar este comando.

    ```bash
    sudo bash enable-kdump.sh
    ```

- Si la salida del comando muestra que kdump está habilitado correctamente, reinicie el sistema para aplicar los cambios.

- Si la salida del comando muestra un error de una operación, el servicio kdump no está habilitado. Consulte la sección [Problemas de compatibilidad](#support-issues), más adelante.

## <a name="test-kdump"></a>Prueba de kdump

> [!NOTE]
>  La operación siguiente desencadenará un bloqueo del kernel y el reinicio del sistema.

- Desencadenar un bloqueo del kernel

    ```bash
    echo c > /proc/sysrq-trigger
    ```

- Una vez que el sistema se reinicie correctamente, compruebe el directorio `/var/crash` para buscar los registros de bloqueo del kernel.

- Si `/var/crash` tiene un directorio con la fecha actual, kdump se ha habilitado correctamente.

## <a name="support-issues"></a>Problemas de compatibilidad

Si se produce un error en el script o kdump no está habilitado, envíe una solicitud de servicio al equipo de soporte técnico de Microsoft. Incluya los detalles siguientes:

* Identificador de la suscripción de HLI

* Nombre de servidor

* Proveedor del SO

* Versión del SO

* Versión del kernel

Para obtener más información, vea [Configuración de kdump](https://www.suse.com/support/kb/doc/?id=3374462).

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre las actualizaciones del sistema operativo en HANA (instancias grandes).

> [!div class="nextstepaction"]
> [Actualizaciones de sistema operativo ](os-upgrade-hana-large-instance.md)
