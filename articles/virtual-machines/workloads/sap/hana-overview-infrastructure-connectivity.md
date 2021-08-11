---
title: Infraestructura y conectividad con SAP HANA en Azure (instancias grandes) | Microsoft Docs
description: Configure la infraestructura de conectividad necesaria para usar SAP HANA en Azure (instancias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/1/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f8fc161f83bcdb070ca4be1c1da3be84f1086c93
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2021
ms.locfileid: "111412554"
---
# <a name="sap-hana-large-instances-deployment"></a>Implementación de SAP HANA (instancias grandes) 

En este artículo, enumeraremos la información que necesitará para implementar SAP HANA (instancias grandes), también conocidas como instancias de BareMetal Infrastructure. En primer lugar, para obtener información general, consulte:
- [Términos comunes de HANA (instancias grandes)](hana-know-terms.md)
-  [SKU de HANA (instancias grandes)](hana-available-skus.md)

## <a name="required-information"></a>Información necesaria

Ha adquirido SAP HANA en Azure (instancias grandes) a Microsoft y quiere implementar. Microsoft necesitará la siguiente información de usted:

- Nombre del cliente.
- Información del contacto de la empresa (incluida la dirección de correo electrónico y el número de teléfono).
- Información del contacto técnico (incluida la dirección de correo electrónico y el número de teléfono).
- Información del contacto técnico de red (incluida la dirección de correo electrónico y el número de teléfono).
- Región de implementación de Azure (por ejemplo, Oeste de EE. UU., Este de Australia o Norte de Europa).
- SKU de SAP HANA en Azure (instancias grandes) (configuración).
- Para cada región de implementación de Azure:
    - Un intervalo de direcciones IP /29 para conexiones ER-P2P para conectar redes virtuales de Azure con HANA (instancias grandes).
    - Un bloque CIDR /24 usado para el grupo de direcciones IP de servidor de HANA (instancias grandes).
    - Opcional al usar [Global Reach de ExpressRoute](../../../expressroute/expressroute-global-reach.md); reserve otro intervalo de direcciones IP /29. El intervalo agregado permite el enrutamiento directo desde el entorno local a las unidades de HANA (instancias grandes). El intervalo agregado también permite el enrutamiento entre unidades de HANA (instancias grandes) en diferentes regiones de Azure. Este intervalo concreto no se puede superponer con los intervalos de direcciones IP definidos antes.
- Los valores del intervalo de direcciones IP usados en el atributo de espacio de direcciones de red virtual de cada red virtual de Azure que se conecta a HANA (instancias grandes).
- Datos para cada sistema de HANA (instancias grandes):
  - Nombre de host deseado, preferiblemente el nombre de dominio completo.
  - Dirección IP deseada para la unidad de HANA (instancias grandes) fuera del intervalo de direcciones del grupo de IP del servidor. (Las primeras 30 direcciones IP del intervalo de direcciones del grupo de IP del servidor están reservadas para el uso interno de HANA [instancias grandes]).
  - Nombre del SID de SAP HANA para la instancia de SAP HANA (necesario para crear los volúmenes de disco necesarios relacionados con SAP HANA). Microsoft necesita el SID de HANA para crear los permisos para sidadm en los volúmenes NFS. Estos volúmenes se conectan a la unidad de HANA (instancias grandes). El SID de HANA también se usa como uno de los componentes del nombre de los volúmenes de disco que se montan. Si quiere ejecutar más de una instancia de HANA en la unidad, debería enumerar varios SID de HANA. Cada uno obtiene un conjunto independiente de volúmenes asignados.
  - En el sistema operativo Linux, el usuario sidadm tiene un identificador de grupo. Este identificador es necesario para crear los volúmenes de disco necesarios relacionados con SAP HANA. La instalación de SAP HANA normalmente crea el grupo sapsys con un identificador de grupo de 1001. El usuario sidadm forma parte de ese grupo.
  - En el sistema operativo Linux, el usuario sidadm tiene un identificador de usuario. Este identificador es necesario para crear los volúmenes de disco necesarios relacionados con SAP HANA. Si está ejecutando varias instancias HANA en la unidad, se enumeran todos los usuarios sidadm. 
- Identificador de la suscripción a Azure a la que SAP HANA en Azure (instancias grandes) estará conectado directamente. Este identificador de suscripción hace referencia a la suscripción a Azure, que se va a cargar con la unidad o unidades de HANA (instancias grandes).

Después de proporcionar la información anterior, Microsoft aprovisiona SAP HANA en Azure (instancias grandes). Microsoft le envía información para vincular sus redes virtuales de Azure a HANA (instancias grandes). También puede acceder a las unidades de HANA (instancias grandes).

## <a name="next-steps"></a>Pasos siguientes

Consulte los artículos siguientes en secuencia para conectarse a HANA (instancias grandes) una vez que Microsoft las haya implementado:

1. [Conexión de las máquinas virtuales de Azure a HANA Instancias grandes](hana-connect-azure-vm-large-instances.md)
2. [Conexión de una red virtual a ExpressRoute de instancias grandes de HANA](hana-connect-vnet-express-route.md)
3. [Más requisitos de red (opcional)](hana-additional-network-requirements.md)
