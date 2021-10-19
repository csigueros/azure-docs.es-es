---
title: Archivo de inclusión
description: Archivo de inclusión
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/04/2021
ms.author: tamram
ms.custom: include file, references_regions
ms.openlocfilehash: 8a50cc2f09d9094e8a7f327f0088988ca776a2c6
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2021
ms.locfileid: "129713894"
---
En la tabla siguiente se describen los límites predeterminados para las cuentas de almacenamiento de uso general v2 (GPv2) y v1 (GPv1) de Azure y de Blob Storage. El límite de *entrada* hace referencia a todos los datos que se envían a una cuenta de almacenamiento. El límite de *salida* hace referencia a todos los datos que se reciben de una cuenta de almacenamiento.

Microsoft recomienda usar una cuenta de almacenamiento GPv2 en la mayoría de los escenarios. Puede actualizar fácilmente una cuenta de almacenamiento GPv1 o de Blob Storage a una cuenta GPv2 sin tiempo de inactividad y sin la necesidad de copiar datos. Para más información, consulte [Actualización a una cuenta de almacenamiento GPv2](../articles/storage/common/storage-account-upgrade.md).

> [!NOTE]
> Puede solicitar unos límites de capacidad y de entrada mayores. Para solicitar un aumento, póngase en contacto con [Soporte técnico de Azure](https://azure.microsoft.com/support/faq/).

| Resource | Límite |
|--|--|
| Número de cuentas de almacenamiento por región y suscripción, incluidas las cuentas de almacenamiento Estándar y Premium. | 250 |
| Capacidad máxima predeterminada de la cuenta de almacenamiento. | 5 PiB <sup>1</sup> |
| Número máximo de contenedores de blobs, blobs, recursos compartidos de archivos, tablas, colas, entidades o mensajes por cuenta de almacenamiento. | Sin límite |
| Tasa de solicitud máxima predeterminada por cuenta de almacenamiento. | 20 000 solicitudes por segundo<sup>1</sup> |
| Entrada máxima predeterminada por cuenta de almacenamiento de uso general v2 y de Blob Storage en las siguientes regiones (LRS/GRS):<br /><ul><li>Este de Australia</li><li>Centro de EE. UU.</li><li>Este de Asia</li><li>Este de EE. UU. 2</li><li>Centro de Francia</li><li>Japón Oriental</li><li>Centro de Corea del Sur</li><li>Norte de Europa</li><li>Centro-sur de EE. UU.</li><li>Sudeste de Asia</li><li>Sur de Reino Unido</li><li>Oeste de Europa</li><li>Oeste de EE. UU.</li></ul> | 60 Gbps<sup>1</sup> |
| Entrada máxima predeterminada por cuenta de almacenamiento de uso general v2 y de Blob Storage en las siguientes regiones (ZRS):<br /><ul><li>Este de Australia</li><li>Centro de EE. UU.</li><li>Este de EE. UU.</li><li>Este de EE. UU. 2</li><li>Centro de Francia</li><li>Japón Oriental</li><li>Norte de Europa</li><li>Centro-sur de EE. UU.</li><li>Sudeste de Asia</li><li>Sur de Reino Unido</li><li>Oeste de Europa</li><li>Oeste de EE. UU. 2</li></ul> | 60 Gbps<sup>1</sup> |
| Entrada máxima predeterminada por cuenta de almacenamiento de uso general v2 y de Blob Storage en regiones que no aparecen en la lista anterior. | 25 Gbps<sup>1</sup> |
| Entrada máxima predeterminada para las cuentas de almacenamiento de uso general v1 (todas las regiones) | 10 Gbps<sup>1</sup> |
| Salida máxima predeterminada de las cuentas de almacenamiento de uso general v2 y de Blob Storage en las siguientes regiones (LRS/GRS):<br /><ul><li>Este de Australia</li><li>Centro de EE. UU.</li><li>Este de Asia</li><li>Este de EE. UU. 2</li><li>Centro de Francia</li><li>Japón Oriental</li><li>Centro de Corea del Sur</li><li>Norte de Europa</li><li>Centro-sur de EE. UU.</li><li>Sudeste de Asia</li><li>Sur de Reino Unido</li><li>Oeste de Europa</li><li>Oeste de EE. UU.</li></ul> | 120 Gbps<sup>1</sup> |
| Salida máxima predeterminada de las cuentas de almacenamiento de uso general v2 y de Blob Storage en las siguientes regiones (ZRS): <ul><li>Este de Australia</li><li>Centro de EE. UU.</li><li>Este de EE. UU.</li><li>Este de EE. UU. 2</li><li>Centro de Francia</li><li>Japón Oriental</li><li>Norte de Europa</li><li>Centro-sur de EE. UU.</li><li>Sudeste de Asia</li><li>Sur de Reino Unido</li><li>Oeste de Europa</li><li>Oeste de EE. UU. 2</li></ul> | 120 Gbps<sup>1</sup> |
| Salida máxima predeterminada de las cuentas de almacenamiento de uso general v2 y de Blob Storage en regiones que no aparecen en la lista anterior. | 50 Gbps<sup>1</sup> |
| Salida máxima para cuentas de almacenamiento de uso general v1 (regiones de EE. UU.) | 20 Gbps si RA-GRS o GRS están habilitado, 30 Gbps en el caso de LRS o ZRS<sup>2</sup> |
| Salida máxima para cuentas de almacenamiento de uso general v1 (regiones no de EE. UU.) | 10 Gbps si RA-GRS o GRS está habilitado, 15 Gbps en el caso de LRS o ZRS<sup>2</sup> |
| Número máximo de reglas de dirección IP por cuenta de almacenamiento | 200 |
| Número máximo de reglas de red virtual por cuenta de almacenamiento | 200 |
| Número máximo de reglas de instancias de recurso por cuenta de almacenamiento | 200 |
| Número máximo de puntos de conexión privados por cuenta de almacenamiento | 200 |

<sup>1</sup> Las cuentas estándar de Azure Storage admiten límites de capacidad y límites de entrada y salida por solicitud superiores. Para solicitar un aumento en los límites de cuenta, póngase en contacto con el [soporte técnico de Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup> Si la cuenta de almacenamiento tiene habilitado el acceso de lectura con almacenamiento con redundancia geográfica (RA-GRS) o el almacenamiento con redundancia de zona geográfica (RA-GZRS), los destinos de salida de la ubicación secundaria son idénticos a los destinos de salida de la ubicación principal. Para más información, consulte [Replicación de Azure Storage](../articles/storage/common/storage-redundancy.md).
