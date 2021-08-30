---
title: Certificación de SAP HANA en Azure (instancias grandes) | Microsoft Docs
description: Aprenda sobre la certificación de SAP HANA en Azure (instancias grandes).
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
ms.date: 07/14/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ade0e82131f65183ac9471bb77d0ab0cc92ae285
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114219646"
---
# <a name="certification"></a>Certificación

Además de la certificación de NetWeaver, SAP requiere una certificación especial para SAP HANA para que esta plataforma se admita en determinadas infraestructuras, como IaaS de Azure y BareMetal Infrastructure.

La nota fundamental de SAP sobre NetWeaver y, hasta cierto punto, sobre la certificación de SAP HANA, es [Nota de SAP 1928533: Aplicaciones de SAP en Azure: Supported products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533). (Nota de SAP 1928533: Aplicaciones de SAP en Azure: productos y tipos de máquina virtual de Azure admitidos)

Los registros de certificación de unidades de SAP HANA en Azure (instancias grandes) pueden encontrarse en el sitio de [plataformas IaaS certificadas para SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). 

Los tipos de SAP HANA en Azure (instancias grandes) mencionados en el sitio de plataformas IaaS certificadas para SAP HANA ofrecen a los clientes de Microsoft y SAP la posibilidad de implementar:

- Cargas de trabajo de gran tamaño de SAP Business Suite
- SAP BW
- S/4 HANA
- BW/4HANA
- Otras cargas de trabajo de SAP HANA en Azure. 

Esta solución se basa en la demarcación de hardware dedicado con certificación de SAP HANA ([Integración de centro de datos adaptado para SAP HANA o TDI](https://scn.sap.com/docs/DOC-63140)). Si ejecuta una solución configurada según SAP HANA TDI, todas las aplicaciones basadas en SAP HANA anteriores funcionan en la infraestructura de hardware.

En comparación con la ejecución de SAP HANA en máquinas virtuales, esta solución ofrece la ventaja de volúmenes de memoria mucho mayores. 

## <a name="key-concepts"></a>Conceptos clave

Para habilitar esta solución, debe entender los siguientes aspectos fundamentales:

- El nivel de aplicación de SAP y las aplicaciones que no son de SAP se ejecutan en máquinas virtuales que se hospedan en las demarcaciones de hardware de Azure habituales.
- La infraestructura local, los centros de datos y las implementaciones de aplicaciones del cliente se conectan a la plataforma en la nube mediante ExpressRoute (recomendado) o una red privada virtual (VPN). Active Directory y DNS también se extienden a Azure.
- La instancia de base de datos de SAP HANA para cargas de trabajo de HANA se ejecuta en SAP HANA en Azure (Instancias grandes). La demarcación de instancias grandes está conectada a las redes de Azure, por lo que el software que se ejecuta en las máquinas virtuales puede interactuar con la instancia de HANA que se ejecuta en HANA (instancias grandes).
- El hardware de SAP HANA en Azure (instancias grandes) es hardware dedicado que se proporciona como IaaS con SUSE Linux Enterprise Server o Red Hat Enterprise Linux preinstalado. Al igual que con las máquinas virtuales, es responsabilidad suya llevar a cabo las actualizaciones y el mantenimiento del sistema operativo.
- La instalación de HANA o de cualquier otro componente necesario para ejecutar SAP HANA en unidades de HANA (instancias grandes) es su responsabilidad. Todas las operaciones relacionadas correspondientes y la administración de SAP HANA en Azure también son responsabilidad suya.
- También puede instalar otros componentes en la suscripción de Azure que se conectan a SAP HANA en Azure (instancias grandes). Por ejemplo, componentes que permiten la comunicación con la base de datos de SAP HANA, como:        
    - Servidores de salto
    - Servidores RDP
    - SAP HANA Studio
    - SAP Data Services para escenarios de BI de SAP
    - Soluciones de supervisión de redes.
- Como en Azure, HANA (instancias grandes) ofrece funcionalidades auxiliares de alta disponibilidad y recuperación ante desastres.

## <a name="next-steps"></a>Pasos siguientes

Conozca las SKU disponibles para HANA (instancias grandes).

> [!div class="nextstepaction"]
> [SKU disponibles para HLI](hana-available-skus.md)
