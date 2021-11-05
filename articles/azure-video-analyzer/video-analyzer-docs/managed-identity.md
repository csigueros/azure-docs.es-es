---
title: Identidades administradas con Azure Video Analyzer
description: En este tema se explica cómo usar identidades administradas con Azure Video Analyzer.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 46d3936c1822b4409dad8ad373352b062c6c9b77
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852776"
---
# <a name="managed-identity"></a>Identidad administrada

Un desafío común para los desarrolladores es la administración de secretos y credenciales para proteger la comunicación entre distintos servicios. En Azure, las identidades administradas eliminan la necesidad de que los desarrolladores administren credenciales, al proporcionar una identidad para el recurso de Azure en Azure Active Directory (Azure AD) y usarla para obtener tokens de Azure AD.

Al crear una cuenta de Azure Video Analyzer, tiene que asociar una cuenta de Azure Storage a ella. Si usa Video Analyzer para grabar vídeo en directo desde una cámara, los datos se almacenan como blobs en un contenedor de la cuenta de almacenamiento. Opcionalmente, puede asociar una instancia de IoT Hub a su cuenta de Video Analyzer; esto es necesario si usa el módulo perimetral de Video Analyzer como [puerta de enlace transparente](./cloud/use-remote-device-adapter.md). Debe usar una identidad administrada para conceder a la cuenta de Video Analyzer el acceso adecuado a la cuenta de almacenamiento y a IoT Hub (si es necesario para la solución), tal como se muestra a continuación.

## <a name="user-assigned-managed-identity-for-video-analyzer"></a>Identidad administrada asignada por el usuario para Video Analyzer

* Cree una [identidad administrada asignada por el usuario (UAMI)](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

> [!NOTE]
> Necesitará una suscripción de Azure en la que tenga acceso tanto al rol [Colaborador](../../role-based-access-control/built-in-roles.md#contributor) como al rol [Administrador de acceso de usuario](../../role-based-access-control/built-in-roles.md#user-access-administrator) al grupo de recursos en el que creará recursos (identidad administrada asignada por el usuario, cuenta de almacenamiento, cuenta de Video Analyzer). Si no tiene los permisos adecuados, pida al administrador de la cuenta que se los conceda. La cuenta de almacenamiento asociada debe estar en la misma región que la cuenta de Video Analyzer. Se recomienda usar una cuenta de almacenamiento [estándar de uso general v2](../../storage/common/storage-account-overview.md#types-of-storage-accounts).
También necesitará acceso al rol de colaborador de IoT Hub si decide asociar una instancia a su cuenta de Video Analyzer.

### <a name="enable-video-analyzer-account-to-access-storage-account"></a>Habilitación de la cuenta de Video Analyzer para acceder a la cuenta de almacenamiento

* Cree una cuenta de almacenamiento de Azure.

* Agregue los roles [Colaborador de datos de Storage Blob](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) y [Lector](../../role-based-access-control/built-in-roles.md#reader) de la cuenta de almacenamiento anterior a la identidad administrada.

* Cree la cuenta de Video Analyzer; para ello, proporcione la identidad administrada asignada por el usuario y la cuenta de almacenamiento como valores para las propiedades pertinentes.

De este modo, Video Analyzer puede acceder a la cuenta de almacenamiento en su nombre mediante la identidad administrada.

### <a name="enable-video-analyzer-account-to-access-iot-hub"></a>Habilitación de la cuenta de Video Analyzer para acceder a IoT Hub

En la sección anterior, ha creado una identidad administrada asignada por el usuario (UAMI) que permite a Video Analyzer acceder a la cuenta de almacenamiento. En este paso, la cuenta de Video Analyzer recibirá acceso a IoT Hub mediante la UAMI y, después, IoT Hub se vinculará a su cuenta de Video Analyzer. Para obtener más información sobre cómo funciona la identidad administrada con IoT Hub, consulte el artículo [Compatibilidad de IoT Hub con identidades administradas](../../iot-hub/iot-hub-managed-identity.md).

* Vaya a la hoja de administración de Video Analyzer en Azure Portal y seleccione **IoT Hub** en **Configuración** en el panel izquierdo.
* Después, seleccione **Asociar** en el panel "Adjuntar IoT Hub". En el panel de configuración **Adjuntar IoT Hub**, escriba los valores de campo necesarios:
    * Suscripción: seleccione el nombre de la suscripción de Azure donde se ha creado la instancia de IoT Hub.
    * IoT Hub: seleccione el centro de IoT existente que se debe asociar a la cuenta de Video Analyzer.
    * Identidad administrada: seleccione la identidad administrada asignada por el usuario (creada anteriormente en la sección anterior) que se usará para acceder a la instancia de IoT Hub
* Haga clic en **Guardar** para vincular IoT Hub a su cuenta de Video Analyzer.

Consulte [este](create-video-analyzer-account.md) artículo para obtener un ejemplo del uso de Azure Portal para realizar lo anterior.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre las funciones de las identidades administradas y su uso con las aplicaciones de Azure, consulte [Identidades administradas de Azure AD](../../active-directory/managed-identities-azure-resources/overview.md).
