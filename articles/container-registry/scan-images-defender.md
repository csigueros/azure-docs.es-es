---
title: Examen de imágenes del registro con Microsoft Defender for Cloud
description: Más información sobre el uso de Microsoft Defender para registros de contenedor a fin de examinar imágenes en los registros de contenedor de Azure
ms.topic: article
ms.date: 05/19/2021
ms.openlocfilehash: 0170895ede2be40cc57d9ecfd82806e1ee3a617c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132323493"
---
# <a name="scan-registry-images-with-microsoft-defender-for-cloud"></a>Examen de imágenes del registro con Microsoft Defender for Cloud

Para examinar las imágenes de los registros de contenedor de Azure en busca de vulnerabilidades, puede integrar una de las soluciones de Azure Marketplace disponibles o, si quiere usar Microsoft Defender for Cloud, habilitar opcionalmente **Microsoft Defender para los registros de contenedor** en el nivel de suscripción. 

* Más información sobre [Microsoft Defender para registros de contenedor](../security-center/defender-for-container-registries-introduction.md)
* Más información sobre la [seguridad de los contenedores en Microsoft Defender for Cloud](../security-center/container-security.md)

## <a name="registry-operations-by-microsoft-defender-for-cloud"></a>Operaciones del registro de Microsoft Defender for Cloud

Microsoft Defender for Cloud examina todas las imágenes insertadas o importadas en un registro, así como las imágenes extraídas en los últimos 30 días. Si se detectan vulnerabilidades, en Microsoft Defender for Cloud aparecen las [correcciones recomendadas](../security-center/defender-for-container-registries-usage.md#view-and-remediate-findings).

 Después de realizar los pasos recomendados para corregir el problema de seguridad, reemplace la imagen en el Registro. Microsoft Defender for Cloud vuelve a examinar la imagen para confirmar que se han corregido las vulnerabilidades. 

Para obtener información, vea [Uso de Microsoft Defender para registros de contenedor](../security-center/defender-for-container-registries-usage.md).

> [!TIP]
> Microsoft Defender for Cloud realiza la autenticación con el registro a fin de extraer imágenes para el examen de vulnerabilidades. Si se recopilan [registros de recursos](monitor-service-reference.md#resource-logs) para el registro, verá eventos de inicio de sesión del registro y eventos de extracción de imágenes generados por Microsoft Defender for Cloud. Estos eventos están asociados a un identificador alfanumérico, como `b21cb118-5a59-4628-bab0-3c3f0e434cg6`.

## <a name="scanning-a-network-restricted-registry"></a>Examen de un registro restringido a la red

Microsoft Defender for Cloud puede examinar imágenes en un registro de contenedor accesible públicamente o en uno protegido mediante reglas de acceso a la red. Si se configuran reglas de red (es decir, se deshabilita el acceso al registro público, se configuran reglas de acceso IP o se crean puntos de conexión privados), asegúrese de habilitar la configuración de red para [**permitir que los servicios Microsoft de confianza**](allow-access-trusted-services.md) accedan al registro. De forma predeterminada, esta configuración está habilitada en los registros de contenedor nuevos.

## <a name="next-steps"></a>Pasos siguientes

* Obtenga más información sobre el acceso al Registro por parte de [servicios de confianza](allow-access-trusted-services.md).
* Para restringir el acceso a un registro mediante un punto de conexión privado de una red virtual, consulte [Configuración de Azure Private Link para un registro de contenedor de Azure](container-registry-private-link.md).
* Para configurar las reglas de firewall del registro, vea [Configuración de reglas de red de dirección IP pública](container-registry-access-selected-networks.md).
