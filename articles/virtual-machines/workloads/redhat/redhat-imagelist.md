---
title: Imágenes de Red Hat Enterprise Linux disponibles en Azure
description: Obtenga información sobre las imágenes de Red Hat Enterprise Linux en Microsoft Azure.
author: asinn826
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.openlocfilehash: b38ced5ec6ed66f14648b165a9556bdc87aa2302
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696805"
---
# <a name="red-hat-enterprise-linux-rhel-images-available-in-azure"></a>Imágenes de Red Hat Enterprise Linux (RHEL) disponibles en Azure

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux 

Azure ofrece una variedad de imágenes de RHEL para diferentes casos de uso.

> [!NOTE]
> Todas las imágenes de RHEL están disponibles en las nubes públicas de Azure y de Azure Government. No están disponibles en las nubes de Azure China.

## <a name="list-of-rhel-images"></a>Lista de imágenes de RHEL
Esta es una lista de imágenes de RHEL disponibles en Azure. A menos que se indique lo contrario, todas las imágenes tienen particiones LVM y se acoplarán en los repositorios de RHEL convencionales (no EUS ni E4S). Las imágenes siguientes están disponibles actualmente para uso general:

> [!NOTE]
> Ya no se generan imágenes sin procesar en favor de las imágenes con particiones LVM. LVM ofrece varias ventajas en comparación con el esquema de partición sin formato (que no es LVM) anterior, incluidas las opciones de cambio de tamaño de partición significativamente más flexibles.

Oferta| SKU | Creación de particiones | Aprovisionamiento | Notas
:----|:----|:-------------|:-------------|:-----
RHEL          | 6.7      | RAW    | Agente Linux | Disponibilidad de soporte técnico ampliado del ciclo de vida a partir del 1 de diciembre. [Más detalles aquí.](redhat-extended-lifecycle-support.md)
|             | 6,8      | RAW    | Agente Linux | Disponibilidad de soporte técnico ampliado del ciclo de vida a partir del 1 de diciembre. [Más detalles aquí.](redhat-extended-lifecycle-support.md)
|             | 6.9      | RAW    | Agente Linux | Disponibilidad de soporte técnico ampliado del ciclo de vida a partir del 1 de diciembre. [Más detalles aquí.](redhat-extended-lifecycle-support.md)
|             | 6.10     | RAW    | Agente Linux | Disponibilidad de soporte técnico ampliado del ciclo de vida a partir del 1 de diciembre. [Más detalles aquí.](redhat-extended-lifecycle-support.md)
|             | 7-RAW    | RAW    | Agente Linux | Familia de imágenes de RHEL 7.x. <br> Se acopla a repositorios convencionales de forma predeterminada (no EUS).
|             | 7-LVM    | LVM    | Agente Linux | Familia de imágenes de RHEL 7.x. <br> Se acopla a repositorios convencionales de forma predeterminada (no EUS). Si está buscando una imagen de RHEL estándar para implementarla, utilice este conjunto de imágenes o su homólogo de segunda generación.
|             | 7lvm-gen2| LVM    | Agente Linux | Familia de imágenes de RHEL 7.x de segunda generación. <br> Se acopla a repositorios convencionales de forma predeterminada (no EUS). Si está buscando una imagen de RHEL estándar para implementarla, utilice este conjunto de imágenes o su homólogo de primera generación.
|             | 7-RAW-CI | RAW-CI | Cloud-init  | Familia de imágenes de RHEL 7.x. <br> Se acopla a repositorios convencionales de forma predeterminada (no EUS).
|             | 7.2      | RAW    | Agente Linux |
|             | 7.3      | RAW    | Agente Linux |
|             | 7.4      | RAW    | Agente Linux | Adjuntada a repositorios de EUS de forma predeterminada a partir de abril de 2019.
|             | 74-gen2  | RAW    | Agente Linux | Adjuntada a repositorios de EUS de forma predeterminada.
|             | 7.5      | RAW    | Agente Linux | Adjuntada a repositorios de EUS de forma predeterminada a partir de junio de 2019.
|             | 75-gen2  | RAW    | Agente Linux | Adjuntada a repositorios de EUS de forma predeterminada.
|             | 7.6      | RAW    | Agente Linux | Adjuntada a repositorios de EUS de forma predeterminada a partir de mayo de 2019.
|             | 76-gen2  | RAW    | Agente Linux | Adjuntada a repositorios de EUS de forma predeterminada.
|             | 7,7      | LVM    | Agente Linux | Adjuntada a repositorios de EUS de forma predeterminada.
|             | 77-gen2  | LVM    | Agente Linux | Adjuntada a repositorios de EUS de forma predeterminada.
|             | 7.8      | LVM    | Agente Linux | Conectada a repositorios normales (EUS no está disponible para RHEL 7.8)
|             | 78-gen2  | LVM    | Agente Linux | Conectada a repositorios normales (EUS no está disponible para RHEL 7,8)
|             | 7.9      | LVM    | Agente Linux | Conectada a repositorios normales (EUS no está disponible para RHEL 7.9)
|             | 79-gen2  | LVM    | Agente Linux | Conectada a repositorios normales (EUS no está disponible para RHEL 7.9)
|             | 8-LVM    | LVM    | Agente Linux | Familia de imágenes de RHEL 8.x. Se adjunta a repositorios convencionales.
|             | 8-lvm-gen2| LVM    | Agente Linux | Generación 2 de Hyper-V: familia de imágenes de RHEL 8.x. Se adjunta a repositorios convencionales.
|             | 8        | LVM    | Agente Linux | Imágenes de RHEL 8.0.
|             | 8-gen2   | LVM    | Agente Linux | Hyper-V Generación 2: imágenes de RHEL 8.0.
|             | 8.1      | LVM    | Agente Linux | Adjuntada a repositorios de EUS de forma predeterminada.
|             | 81gen2   | LVM    | Agente Linux | Hyper-V generación 2: se conecta a los repositorios EUS a partir de noviembre de 2020.
|             | 8.1-ci   | LVM    | Agente Linux | Se conecta a repositorios EUS a partir de noviembre de 2020.
|             | 81-ci-gen2| LVM    | Agente Linux | Hyper-V generación 2: se conecta a los repositorios EUS a partir de noviembre de 2020.
|             | 8,2      | LVM    | Agente Linux | Se conecta a repositorios EUS a partir de noviembre de 2020.
|             | 82gen2   | LVM    | Agente Linux | Hyper-V generación 2: se conecta a los repositorios EUS a partir de noviembre de 2020.
|             | 8.3   | LVM    | Agente Linux |  Se conecta a repositorios normales (EUS no está disponible para RHEL 8.3)
|             | 83-gen2   | LVM    | Agente Linux |Hyper-V generación 2: se conecta a repositorios normales (EUS no está disponible para RHEL 8.3)
RHEL-SAP      | 7.4      | LVM    | Agente Linux | RHEL 7.4 for SAP HANA y Business Apps. Se acopla a los repositorios de E4S, cobrará un recargo para SAP y RHEL, así como la tarifa de proceso básica.
|             | 74sap-gen2| LVM    | Agente Linux | RHEL 7.4 for SAP HANA y Business Apps. Imagen de segunda generación. Se acopla a los repositorios de E4S, cobrará un recargo para SAP y RHEL, así como la tarifa de proceso básica.
|             | 7.5       | LVM    | Agente Linux | RHEL 7.5 for SAP HANA y Business Apps. Se acopla a los repositorios de E4S, cobrará un recargo para SAP y RHEL, así como la tarifa de proceso básica.
|             | 75sap-gen2| LVM    | Agente Linux | RHEL 7.5 for SAP HANA y Business Apps. Imagen de segunda generación. Se acopla a los repositorios de E4S, cobrará un recargo para SAP y RHEL, así como la tarifa de proceso básica.
|             | 7.6       | LVM    | Agente Linux | RHEL 7.6 for SAP HANA y Business Apps. Se acopla a los repositorios de E4S, cobrará un recargo para SAP y RHEL, así como la tarifa de proceso básica.
|             | 76sap-gen2| LVM    | Agente Linux | RHEL 7.6 for SAP HANA y Business Apps. Imagen de segunda generación. Se acopla a los repositorios de E4S, cobrará un recargo para SAP y RHEL, así como la tarifa de proceso básica.
|             | 7,7       | LVM    | Agente Linux | RHEL 7.7 for SAP HANA y Business Apps. Se acopla a los repositorios de E4S, cobrará un recargo para SAP y RHEL, así como la tarifa de proceso básica.
RHEL-SAP-HANA (se quitará en noviembre de 2020) | 6.7       | RAW    | Agente Linux | RHEL 6.7 for SAP HANA. Obsoleto en favor de las imágenes de RHEL-SAP. Esta imagen se quitará en noviembre de 2020. Puede encontrar más información sobre las ofertas de nube de SAP de Red Hat en [Ofertas de SAP en proveedores de nube certificados](https://access.redhat.com/articles/3751271).
|             | 7.2       | LVM    | Agente Linux | RHEL 7.2 for SAP HANA. Obsoleto en favor de las imágenes de RHEL-SAP. Esta imagen se quitará en noviembre de 2020. Puede encontrar más información sobre las ofertas de nube de SAP de Red Hat en [Ofertas de SAP en proveedores de nube certificados](https://access.redhat.com/articles/3751271).
|             | 7.3       | LVM    | Agente Linux | RHEL 7.3 for SAP HANA. Obsoleto en favor de las imágenes de RHEL-SAP. Esta imagen se quitará en noviembre de 2020. Puede encontrar más información sobre las ofertas de nube de SAP de Red Hat en [Ofertas de SAP en proveedores de nube certificados](https://access.redhat.com/articles/3751271).
RHEL-SAP-APPS | 6,8       | RAW    | Agente Linux | RHEL 6.8 for SAP Business Applications. Obsoleto en favor de las imágenes de RHEL-SAP.
|             | 7.3       | LVM    | Agente Linux | RHEL 7.3 for SAP Business Applications. Obsoleto en favor de las imágenes de RHEL-SAP.
|             | 7.4       | LVM    | Agente Linux | RHEL 7.4 for SAP Business Applications.
|             | 7.6       | LVM    | Agente Linux | RHEL 7.6 for SAP Business Applications.
|             | 7,7       | LVM    | Agente Linux | RHEL 7.7 for SAP Business Applications.
|             | 77-gen2       | LVM    | Agente Linux | RHEL 7.7 for SAP Business Applications. Imagen de generación 2
|             | 8.1       | LVM    | Agente Linux | RHEL 8.1 for SAP Business Applications.
|             | 81-gen2      | LVM    | Agente Linux | RHEL 8.1 for SAP Business Applications. Imagen de segunda generación.
|             | 8,2       | LVM    | Agente Linux | RHEL 8.2 for SAP Business Applications.
|             | 82-gen2      | LVM    | Agente Linux | RHEL 8.2 for SAP Business Applications. Imagen de segunda generación.
RHEL-HA       | 7.4       | LVM    | Agente Linux | RHEL 7.4 con el complemento de alta disponibilidad. Cobrará un recargo por alta disponibilidad y RHEL además de la tarifa de proceso básica. Obsoleta en favor de las imágenes de RHEL-SAP-HA.
|             | 7.5       | LVM    | Agente Linux | RHEL 7.5 con el complemento de alta disponibilidad. Cobrará un recargo por alta disponibilidad y RHEL además de la tarifa de proceso básica. Obsoleta en favor de las imágenes de RHEL-SAP-HA.
|             | 7.6       | LVM    | Agente Linux | RHEL 7.6 con el complemento de alta disponibilidad. Cobrará un recargo por alta disponibilidad y RHEL además de la tarifa de proceso básica. Obsoleta en favor de las imágenes de RHEL-SAP-HA.
RHEL-SAP-HA   | 7.4          | LVM    | Agente Linux | RHEL 7.4 for SAP con alta disponibilidad y servicios de actualización. Se acopla a los repositorios de E4S. Cobrará un recargo para los repositorios de SAP y alta disponibilidad así como RHEL, además de las tarifas de proceso básicas.
|             | 74sapha-gen2 | LVM    | Agente Linux | RHEL 7.4 for SAP con alta disponibilidad y servicios de actualización. Imagen de segunda generación. Se acopla a los repositorios de E4S. Cobrará un recargo para los repositorios de SAP y alta disponibilidad así como RHEL, además de las tarifas de proceso básicas.
|             | 7.5          | LVM    | Agente Linux | RHEL 7.5 for SAP con alta disponibilidad y servicios de actualización. Se acopla a los repositorios de E4S. Cobrará un recargo para los repositorios de SAP y alta disponibilidad así como RHEL, además de las tarifas de proceso básicas.
|             | 7.6          | LVM    | Agente Linux | RHEL 7.6 for SAP con alta disponibilidad y servicios de actualización. Se acopla a los repositorios de E4S. Cobrará un recargo para los repositorios de SAP y alta disponibilidad así como RHEL, además de las tarifas de proceso básicas.
|             | 76sapha-gen2 | LVM    | Agente Linux | RHEL 7.6 for SAP con alta disponibilidad y servicios de actualización. Imagen de segunda generación. Se acopla a los repositorios de E4S. Cobrará un recargo para los repositorios de SAP y alta disponibilidad así como RHEL, además de las tarifas de proceso básicas.
|             | 7,7          | LVM    | Agente Linux | RHEL 7.7 for SAP con alta disponibilidad y servicios de actualización. Se acopla a los repositorios de E4S. Cobrará un recargo para los repositorios de SAP y alta disponibilidad así como RHEL, además de las tarifas de proceso básicas.
|             | 77sapha-gen2 | LVM    | Agente Linux | RHEL 7.7 for SAP con alta disponibilidad y servicios de actualización. Imagen de segunda generación. Se acopla a los repositorios de E4S. Cobrará un recargo para los repositorios de SAP y alta disponibilidad así como RHEL, además de las tarifas de proceso básicas.
|             | 8.1          | LVM    | Agente Linux | RHEL 8.1 for SAP con alta disponibilidad y servicios de actualización. Se acopla a los repositorios de E4S. Cobrará un recargo para los repositorios de SAP y alta disponibilidad así como RHEL, además de las tarifas de proceso básicas.
|             | 81sapha-gen2          | LVM    | Agente Linux | RHEL 8.1 for SAP con alta disponibilidad y servicios de actualización. Imágenes de generación 2 conectadas a repositorios E4S. Cobrará un recargo para los repositorios de SAP y alta disponibilidad así como RHEL, además de las tarifas de proceso básicas.
|             | 8,2          | LVM    | Agente Linux | RHEL 8.2 for SAP con alta disponibilidad y servicios de actualización. Cobrará un recargo para los repositorios de SAP y alta disponibilidad así como RHEL, además de las tarifas de proceso básicas.
|             | 82sapha-gen2          | LVM    | Agente Linux | RHEL 8.2 for SAP con alta disponibilidad y servicios de actualización. Imágenes de generación 2 conectadas a repositorios E4S. Cobrará un recargo para los repositorios de SAP y alta disponibilidad así como RHEL, además de las tarifas de proceso básicas.
rhel-byos     |rhel-lvm74| LVM    | Agente Linux | Las imágenes BYOS de RHEL 7.4, no acopladas a ningún origen de actualizaciones, no cobrarán un recargo de RHEL.
|             |rhel-lvm75| LVM    | Agente Linux | Las imágenes BYOS de RHEL 7.5, no acopladas a ningún origen de actualizaciones, no cobrarán un recargo de RHEL.
|             |rhel-lvm76| LVM    | Agente Linux | Las imágenes BYOS de RHEL 7.6, no acopladas a ningún origen de actualizaciones, no cobrarán un recargo de RHEL.
|             |rhel-lvm76-gen2| LVM    | Agente Linux | Las imágenes BYOS de RHEL 7.6 de segunda generación, no acopladas a ningún origen de actualizaciones, no cobrarán un recargo de RHEL.
|             |rhel-lvm77| LVM    | Agente Linux | Las imágenes BYOS de RHEL 7.7, no acopladas a ningún origen de actualizaciones, no cobrarán un recargo de RHEL.
|             |rhel-lvm77-gen2| LVM    | Agente Linux | Las imágenes BYOS de RHEL 7.7 de segunda generación, no acopladas a ningún origen de actualizaciones, no cobrarán un recargo de RHEL.
|             |rhel-lvm78| LVM    | Agente Linux | Las imágenes BYOS de RHEL 7.8, no acopladas a ningún origen de actualizaciones, no cobrarán un recargo de RHEL.
|             |rhel-lvm78-gen2| LVM    | Agente Linux | Las imágenes BYOS de RHEL 7.8 de segunda generación, no acopladas a ningún origen de actualizaciones, no cobrarán un recargo de RHEL.
|             |rhel-lvm8 | LVM    | Agente Linux | Las imágenes BYOS de RHEL 8.0, no acopladas a ningún origen de actualizaciones, no cobrarán un recargo de RHEL.
|             |rhel-lvm8-gen2 | LVM    | Agente Linux | Las imágenes BYOS de RHEL 8.0 de segunda generación, no acopladas a ningún origen de actualizaciones, no cobrarán un recargo de RHEL.
|             |rhel-lvm81 | LVM    | Agente Linux | Las imágenes BYOS de RHEL 8.1, no acopladas a ningún origen de actualizaciones, no cobrarán un recargo de RHEL.
|             |rhel-lvm81-gen2 | LVM    | Agente Linux | Las imágenes BYOS de RHEL 8.1 de segunda generación, no acopladas a ningún origen de actualizaciones, no cobrarán un recargo de RHEL.
|             |rhel-lvm82 | LVM    | Agente Linux | Las imágenes BYOS de RHEL 8.2, no acopladas a ningún origen de actualizaciones, no cobrarán un recargo de RHEL.
|             |rhel-lvm82-gen2 | LVM    | Agente Linux | Las imágenes BYOS de RHEL 8.2 de segunda generación, no acopladas a ningún origen de actualizaciones, no cobrarán un recargo de RHEL.

> [!NOTE]
> Red Hat considera fin de la vida útil la oferta de productos RHEL-SAP-HANA. Las implementaciones existentes seguirán funcionando con normalidad, pero Red Hat recomienda que los clientes migren de las imágenes RHEL-SAP-HANA a las imágenes RHEL-SAP-HANA que incluyen los repositorios SAP HANA y el complemento HA. Puede encontrar más información sobre las ofertas de nube de SAP de Red Hat en [Ofertas de SAP en proveedores de nube certificados](https://access.redhat.com/articles/3751271).

## <a name="next-steps"></a>Pasos siguientes
* Obtenga más información sobre las [imágenes de Red Hat en Azure](./redhat-images.md).
* Obtenga más información sobre la [infraestructura de actualización de Red Hat](./redhat-rhui.md).
* Obtenga más información sobre la [oferta BYOS de RHEL](./byos.md).
* Puede encontrar información sobre las directivas de soporte técnico de Red Hat para todas las versiones de RHEL en la página [Red Hat Enterprise Linux Life Cycle](https://access.redhat.com/support/policy/updates/errata) (Ciclo de vida de Red Hat Enterprise Linux).
