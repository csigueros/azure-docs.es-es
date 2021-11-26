---
title: Acerca del marco de automatización de implementaciones de SAP
description: Información general sobre el marco y las herramientas del marco de automatización de la implementación de SAP en Azure.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.service: virtual-machines-sap
ms.topic: conceptual
ms.openlocfilehash: 4b3db0835c1710317672aca717a0faa59626e19f
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725350"
---
# <a name="sap-deployment-automation-framework-on-azure"></a>Marco de automatización de implementaciones de SAP

El [marco de automatización de la implementación de SAP en Azure](https://github.com/Azure/sap-hana) es una herramienta de orquestación de código abierto para implementar, instalar y mantener entornos de SAP. Puede crear una infraestructura para entornos de SAP basada en SAP HANA y NetWeaver con AnyDB en cualquiera de las versiones de sistema operativo compatibles con SAP e implementarlas en cualquier región de Azure.

El [marco de automatización](https://github.com/Azure/sap-hana) tiene dos componentes principales:
-   La infraestructura de implementación (plano de control) 
-   La infraestructura de SAP (carga de trabajo de SAP)

La dependencia entre el plano de control y el plano de aplicación se muestra en el diagrama siguiente.

:::image type="content" source="./media/automation-deployment-framework/control-plane-sap-infrastructure.png" alt-text="Diagrama que muestra la dependencia del marco de automatización de la implementación de SAP entre el plano de control y el plano de aplicación.":::

En el diagrama siguiente se muestra los componentes clave del plano de control y la zona de cargas de trabajo.

:::image type="content" source="./media/automation-deployment-framework/automation-diagram-full.png" alt-text="Diagrama que muestra el entorno del marco de automatización de implementaciones de SAP.":::

El marco usa [Terraform](https://www.terraform.io/) para la implementación de la infraestructura, y [Ansible](https://www.ansible.com/) para la configuración del sistema operativo y de la aplicación.

> [!NOTE]
> Este marco de automatización se basa en los procedimientos recomendados y los principios de Microsoft para SAP en Azure. Examine la [guía de introducción a SAP en máquinas virtuales de Azure](get-started.md) para saber usar máquinas virtuales certificadas y soluciones de almacenamiento para la estabilidad, la confiabilidad y el rendimiento.
> 
> Este marco de automatización también sigue las instrucciones de [Microsoft Cloud Adoption Framework para Azure](/azure/cloud-adoption-framework/).

Usará el plano de control del marco de automatización de la implementación de SAP para implementar tanto la infraestructura de SAP como la infraestructura de aplicaciones de SAP. La implementación usa plantillas de Terraform para crear la [infraestructura como servicio (IaaS)](https://azure.microsoft.com/overview/what-is-iaas) definida para hospedar las aplicaciones de SAP.

La configuración de aplicaciones se realizará desde el controlador de Ansible en el plano de control mediante un conjunto de cuadernos de estrategias predefinidos. Estos cuadernos:

- Configurarán el sistema operativo base.
- Configurarán el sistema operativo específico de SAP.
- Harán que el soporte de instalación esté disponible en el sistema.
- Instalarán el sistema SAP.
- Instalarán la base de datos de SAP (SAP HANA o AnyDB).
- Configurarán la alta disponibilidad (HA) mediante Pacemaker.
- Configurarán la alta disponibilidad (HA) para una base de datos de SAP.


## <a name="about-the-control-plane"></a>Acerca del plano de control

El plano de control aloja la infraestructura desde la que se implementarán otros entornos. Una vez que el plano de control se implementa, rara vez, o nunca, es necesario volver a implementarlo.

El plano de control proporciona los siguientes servicios:
-   Infraestructura de implementación de Terraform.
-   Controlador de Ansible.
-   Almacenamiento persistente para los archivos de estado de Terraform.
-   Almacenamiento persistente para el software SAP descargado.
-   Almacenamiento seguro para las credenciales de implementación.
-   Zona de DNS privado (opcional).

El plano de control suele ser un recurso regional implementado en la suscripción del centro en una [arquitectura en estrella tipo hub-and-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). 

Los componentes clave del plano de control son:
- Máquina virtual de implementación. 
- Cuenta de almacenamiento para los archivos de estado de Terraform
- Cuenta de almacenamiento para el soporte de instalación del soporte de instalación de SAP
- Almacén de claves para las credenciales de implementación


## <a name="about-the-sap-workload"></a>Acerca de la carga de trabajo de SAP

La carga de trabajo de SAP contiene todos los recursos de la infraestructura de Azure para las implementaciones de SAP. Estos recursos se implementan desde el plano de control. La carga de trabajo de SAP tiene dos componentes principales:
-   Zona de cargas de trabajo de SAP
-   Sistema SAP

## <a name="about-the-sap-workload-zone"></a>Acerca de la zona de cargas de trabajo de SAP

La zona de cargas de trabajo permite la creación de particiones de las implementaciones en diferentes entornos (desarrollo, prueba, producción). La zona de cargas de trabajo de SAP proporciona los siguientes servicios a los sistemas SAP.
-   Infraestructura de redes virtuales
-   Almacenamiento seguro para las credenciales del sistema (Virtual Machines y SAP)
-   Storage compartido (opcional)


## <a name="about-the-sap-system"></a>Acerca del sistema SAP

La implementación del sistema consta de las máquinas virtuales que ejecutarán la aplicación de SAP, incluidos los niveles web, de aplicación y de base de datos.

El sistema SAP proporciona los siguientes servicios
-   Máquina virtual, almacenamiento e infraestructura auxiliar para hospedar las aplicaciones de SAP.

## <a name="glossary"></a>Glosario

Los siguientes términos son conceptos importantes para conocer el marco de automatización.

### <a name="sap-concepts"></a>Conceptos de SAP

| Término | Descripción |
| ---- | ----------- |
| Sistema | Instancia de una aplicación de SAP que contiene los recursos que la aplicación necesita ejecutar. Definido por un identificador único de tres letras, el **SID**.
| Horizontal | Una colección de sistemas de diferentes entornos dentro de una aplicación de SAP. Por ejemplo, SAP ERP Central Component (ECC), SAP Customer Relationship Management (CRM) y SAP Business Warehouse (BW). |
| Zona de cargas de trabajo | Crea particiones de las aplicaciones de SAP en entornos, como entornos de producción y que no son de producción, o entornos de desarrollo, control de calidad y producción. Proporciona recursos compartidos, como redes virtuales y un almacén de claves, a todos los sistemas internos. |

En el diagrama siguiente se muestran las relaciones entre los sistemas SAP, las zonas de cargas de trabajo (entornos) y los entornos. En esta configuración de ejemplo, el cliente tiene tres entornos de SAP: ECC, CRM y BW. Cada entorno contiene tres zonas de cargas de trabajo: producción, control de calidad y desarrollo. Cada zona de cargas de trabajo puede contener uno o varios sistemas.

:::image type="content" source="./media/automation-deployment-framework/sap-terms.png" alt-text="Diagrama de la configuración de SAP con entornos, zonas de flujo de trabajo y sistemas.":::

### <a name="deployment-components"></a>Componentes de implementación

| Término | Descripción | Ámbito |
| ---- | ----------- | ----- |
| Implementador | Máquina virtual que puede ejecutar comandos de Terraform y Ansible. Se implementa en una red virtual, nueva o existente, que está emparejada con la red virtual de SAP. | Region |
| Biblioteca | Proporciona almacenamiento para los archivos de estado de Terraform y el soporte de instalación de SAP. | Region |
| Zona de cargas de trabajo | Contiene la red virtual en la que se implementa el sistema o los sistemas SAP. También contiene un almacén de claves que contiene las credenciales de los sistemas del entorno. | Zona de cargas de trabajo |
| Sistema | Unidad de implementación de la aplicación SAP (SID). Contiene máquinas virtuales y artefactos de la infraestructura auxiliares, como equilibradores de carga y conjuntos de disponibilidad. | Zona de cargas de trabajo |


## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Introducción al marco de automatización de implementaciones de la implementación](automation-get-started.md)
