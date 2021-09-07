---
title: 'Cifrado doble de infraestructura: Azure Database for MySQL'
description: Más información sobre el uso del cifrado doble de infraestructura para agregar una segunda capa de cifrado con claves administradas por el servicio.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 6/30/2020
ms.openlocfilehash: 9b6d87c3bfabf3884d9a90966994eea002a45dd8
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2021
ms.locfileid: "114674441"
---
# <a name="azure-database-for-mysql-infrastructure-double-encryption"></a>Cifrado doble de infraestructura de Azure Database for MySQL

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Azure Database for MySQL usa el [cifrado de datos en reposo](concepts-security.md#at-rest) del almacenamiento mediante claves administradas de Microsoft. Los datos, incluidas las copias de seguridad, se cifran en el disco y este cifrado está siempre activo y no se puede deshabilitar. El cifrado usa el módulo criptográfico validado FIPS 140-2 y un cifrado AES de 256 bits para el cifrado del almacenamiento de Azure.

El cifrado doble de infraestructura agrega una segunda capa de cifrado con claves administradas por el servicio. Usa el módulo criptográfico validado FIPS 140-2, pero con un algoritmo de cifrado diferente. Esto proporciona una capa adicional de protección para los datos en reposo. La clave que se usa en el cifrado doble de infraestructura también la administra el servicio Azure Database for MySQL. El cifrado doble de infraestructura no está habilitado de forma predeterminada ya que el nivel adicional de cifrado puede afectar al rendimiento.

> [!NOTE]
> Al igual que el cifrado de datos en reposo, esta característica solo se admite en el almacenamiento "Almacenamiento de uso general v2 (admite hasta 16 TB)" disponible en los planes de tarifa De uso general y optimizados para memoria. Consulte [Conceptos de almacenamiento](concepts-pricing-tiers.md#storage) para más información. Para otras limitaciones, consulte la sección [Limitaciones](concepts-infrastructure-double-encryption.md#limitations).

El cifrado del nivel de infraestructura tiene la ventaja de que se implementa en la capa más cercana al dispositivo de almacenamiento o a los cables de red. Azure Database for MySQL implementa las dos capas de cifrado mediante claves administradas por el servicio. Aunque técnicamente siga en la capa de servicio, está muy cerca del hardware que almacena los datos en reposo. También puede habilitar opcionalmente el cifrado de datos en reposo mediante el uso de una [clave administrada por el cliente](concepts-data-encryption-mysql.md) para el servidor MySQL aprovisionado. 

La implementación en los niveles de infraestructura también admite una diversidad de claves. La infraestructura debe tener en cuenta los diferentes clústeres de máquinas y redes. Por ello, se usan diferentes claves para minimizar el radio de impacto de los ataques a la infraestructura y de diversos errores de hardware y de red. 

> [!NOTE]
> El uso del cifrado doble de infraestructura tendrá un impacto del 5 al 10 % en el rendimiento del servidor de Azure Database for MySQL debido al proceso de cifrado adicional.

## <a name="benefits"></a>Ventajas

El cifrado doble de infraestructura de Azure Database for MySQL proporciona las siguientes ventajas:

1. **Diversidad de implementación de cifrado adicional**: La intención planeada de cambiar al cifrado basado en hardware diversificará aún más las implementaciones proporcionando una implementación basada en hardware además de otra basada en software.
2. **Errores de implementación**: dos capas de cifrado en el nivel de infraestructura protegen frente a los errores en el almacenamiento en caché o la administración de memoria en niveles superiores que exponen datos de texto no cifrado. Además, las dos capas también protegen frente a los errores de implementación del cifrado en general.

La combinación de estas proporciona una protección sólida frente a las amenazas comunes y los puntos débiles utilizados para atacar la criptografía.

## <a name="supported-scenarios-with-infrastructure-double-encryption"></a>Escenarios admitidos en el cifrado doble de infraestructura

Las funcionalidades de cifrado que proporciona Azure Database for MySQL se pueden usar juntas. A continuación se muestra un resumen de los distintos escenarios que puede usar:

|  ##   | Cifrado predeterminado | Cifrado doble de infraestructura | Cifrado de datos con claves administradas por el cliente  |
|:------|:------------------:|:--------------------------------:|:--------------------------------------------:|
| 1     | *Sí*              | *No*                             | *No*                                         |
| 2     | *Sí*              | *Sí*                            | *No*                                         |
| 3     | *Sí*              | *No*                             | *Sí*                                        |
| 4     | *Sí*              | *Sí*                            | *Sí*                                        |
|       |                    |                                  |                                              |

> [!Important]
> - Los escenarios 2 y 4 pueden introducir una caída del 5-10 por ciento en el rendimiento según el tipo de carga de trabajo para el servidor de Azure Database for MySQL debido a la capa adicional de cifrado de la infraestructura.
> - La configuración del cifrado doble de infraestructura para Azure Database for MySQL solo se permite durante la creación del servidor. Una vez que se ha aprovisionado el servidor, no se puede cambiar el cifrado del almacenamiento. No obstante, podrá habilitar el cifrado de datos mediante claves administradas por el cliente para el servidor creado con o sin cifrado doble de infraestructura.

## <a name="limitations"></a>Limitaciones

Para Azure Database for MySQL, la compatibilidad con el cifrado doble de infraestructura tiene unas limitaciones:

* La compatibilidad con esta funcionalidad se limita a **De uso general** y los planes de tarifa **Optimizados para memoria**.
* Esta característica solo se admite en regiones y servidores compatibles con el almacenamiento de uso general v2 (hasta 16 TB). Para ver la lista de regiones de Azure que admiten almacenamiento de hasta 16 TB, consulte [aquí](concepts-pricing-tiers.md#storage) la sección de almacenamiento de la documentación.

    > [!NOTE]
    > - Para todos los nuevos servidores MySQL creados en las [regiones de Azure](concepts-pricing-tiers.md#storage) compatibles con el almacenamiento de uso general v2, está **disponible** soporte del cifrado con claves de administrador de clientes. El servidor Point In Time Restored (PITR) o la réplica de lectura no calificarán, aunque en teoría son "nuevos".
    > - Para validar el almacenamiento de uso general v2 del servidor aprovisionado, puede ir a la hoja del plan de tarifa en el portal y ver el tamaño de almacenamiento máximo que admite el servidor aprovisionado. Si puede subir el control deslizante hasta 4 TB, el servidor está en el almacenamiento de uso general v1 y no admitirá el cifrado con claves administradas por el cliente. Pero los datos se cifran en todo momento con claves administradas por el servicio. Póngase en contacto con AskAzureDBforMySQL@service.microsoft.com si tiene alguna pregunta.



## <a name="next-steps"></a>Pasos siguientes

Aprenda a [configurar el cifrado doble de infraestructura para Azure Database for MySQL](howto-double-encryption.md).
