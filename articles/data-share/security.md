---
title: Información general de seguridad para Azure Data Share
description: Información general de seguridad para Azure Data Share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 12/17/2020
ms.openlocfilehash: 770ee01d054aec084e9abd609af28e8125844e0f
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2021
ms.locfileid: "112895875"
---
# <a name="security-overview-for-azure-data-share"></a>Información general de seguridad para Azure Data Share

En este artículo se proporciona una información general de la seguridad del servicio de Azure Data Share.

## <a name="security-overview"></a>Introducción a la seguridad

Azure Data Share aprovecha la seguridad subyacente que ofrece Azure para proteger los datos tanto en reposo como en tránsito. Los datos se cifran en reposo, si el almacén de datos subyacente admite esta opción. Los datos también se cifran en tránsito mediante TLS 1.2. Los metadatos de un recurso compartido de datos también se cifran tanto en reposo como en tránsito. Azure Data Share no almacena el contenido de los datos del cliente que se esté compartiendo.

Azure Data Share aprovecha la identidad administrada (anteriormente conocida como MSI) para tener acceso a los almacenes de datos que sirven para el uso compartido de datos. No hay intercambio de credenciales entre un proveedor de datos y un consumidor de datos. Para obtener más información sobre las identidades administradas, consulte [Identidades administradas para recursos de Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md). Para obtener más información sobre los roles y los permisos necesarios para compartir datos, consulte [Roles y requisitos](concepts-roles-permissions.md).

## <a name="access-control"></a>Control de acceso

Se pueden establecer controles de acceso a Azure Data Share en el nivel de recursos para asegurarse de que solo se accede con autorización. El propietario y el colaborador de un recurso de Data Share pueden compartir datos, recibir recursos compartidos y realizar cambios en los recursos compartidos existentes. El lector de un recurso de Data Share puede ver recursos compartidos, pero no puede realizar cambios. 

Una vez que se crea o recibe un recurso compartido, los usuarios con el permiso adecuado para el recurso de Data Share pueden realizar cambios. Cuando el usuario que crea o recibe un recurso compartido deja la organización, no se finaliza el recurso compartido ni se detiene el flujo de datos. Otros usuarios con el permiso adecuado para el recurso de Data Share pueden seguir administrando el recurso compartido.

## <a name="share-data-from-or-to-data-stores-with-firewall-enabled"></a>Datos compartidos de almacenes de datos o con estos con el firewall habilitado
Para compartir datos con cuentas de almacenamiento o de estas con el firewall activado, debe habilitar **Permitir servicios de Microsoft de confianza** en la cuenta de almacenamiento. Consulte [Configuración de redes virtuales y firewalls de Azure Storage](../storage/common/storage-network-security.md#trusted-microsoft-services) para más información.


## <a name="next-steps"></a>Pasos siguientes

Para obtener información acerca de cómo empezar a compartir datos, vaya al tutorial que cubre cómo [compartir sus datos](share-your-data.md).
