---
title: Migración de Azure Germany a Azure global
description: Introducción a la migración de los recursos de Azure desde Azure Germany a Azure global.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate
ms.openlocfilehash: 37dde9cf2f105c3e8e8c3dc86e59340c205f9254
ms.sourcegitcommit: f0168d80eb396ce27032aa02fe9da5a0c10b5af3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/23/2021
ms.locfileid: "117029163"
---
# <a name="overview-of-migration-guidance-for-azure-germany"></a>Introducción a la guía de migración para Azure Alemania

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

Los artículos de esta sección se han creado para ayudarle a migrar las cargas de trabajo desde Azure Germany a Azure global. Aunque [Azure Migration Center](https://azure.microsoft.com/migration/) proporciona herramientas que le ayudan a migrar recursos, algunas de las herramientas de Azure Migration Center son útiles solo para las migraciones que ocurren en el mismo inquilino o en la misma región.

Las dos regiones de Alemania son completamente independientes de Azure global. Las nubes de Azure global y de Alemania tienen sus propias instancias independientes de Azure Active Directory (Azure AD). Debido a esto, los inquilinos de Azure Alemania están separados de los inquilinos de Azure global. En este artículo se describen las herramientas de migración que están disponibles cuando se migra entre *diferentes* inquilinos.

Las instrucciones sobre la identidad o inquilinos está pensada solo para los clientes de Azure. Si usa inquilinos comunes de Azure Active Directory (Azure AD) para Azure y Microsoft 365 (u otros productos de Microsoft), existen dificultades en la migración de identidades y primero debe consultar el artículo [Acciones y efectos de las fases de migración desde Microsoft Cloud Deutschland](/microsoft-365/enterprise/ms-cloud-germany-transition-phases?view=o365-worldwide). Si tiene alguna pregunta, póngase en contacto con el administrador de cuentas o con el soporte técnico de Microsoft.

Los proveedores de soluciones en la nube de Azure deben realizar pasos adicionales para dar soporte técnico a los clientes durante la transición a la nueva región del centro de datos alemán y después de esta. Obtenga más información sobre estos [pasos adicionales](/microsoft-365/enterprise/ms-cloud-germany-transition-add-csp).

## <a name="migration-process"></a>Proceso de migración

El proceso que se utiliza para migrar una carga de trabajo de Azure Alemania a Azure global suele ser similar al proceso que se utiliza para migrar aplicaciones a la nube. Los pasos del proceso de migración son:

![Imagen que muestra los cuatro pasos del proceso de migración: Evaluar, Planear, Migrar y Validar](./media/germany-migration-main/migration-steps.png)

### <a name="assess"></a>Evaluar

Es importante entender la huella de Azure Alemania de la organización mediante la reunión de propietarios de cuentas de Azure, administradores de suscripciones, administradores de inquilinos y equipos de finanzas y contabilidad. Las personas que trabajan con estos roles pueden proporcionar una imagen completa del uso de Azure para una organización grande.

En la fase de evaluación, compile un inventario de recursos:
  - Cada administrador de suscripción y administrador de inquilinos debe ejecutar una serie de scripts para mostrar los grupos de recursos, los recursos de cada grupo de recursos y la configuración de implementación de los grupos de recursos.
  - Documente las dependencias entre las aplicaciones en Azure y con los sistemas externos.
  - Documente el número de cada recurso de Azure y la cantidad de datos que está asociada con cada instancia que desea migrar.
  - Asegúrese de que los documentos de arquitectura de la aplicación sean coherentes con la lista de recursos de Azure.

Al final de esta fase, tendrá:

- Una lista completa de los recursos de Azure que están en uso.
- Una lista de las dependencias entre recursos.
- Información acerca de la complejidad del esfuerzo de migración.

### <a name="plan"></a>Plan

En la fase de planeación, complete las tareas siguientes:

- Utilice el resultado del análisis de dependencia completado en la etapa de evaluación para definir los componentes relacionados. Considere la posibilidad de migrar todos los componentes relacionados juntos en un *paquete de migración*.
- (Opcional) Utilice la migración como una oportunidad de aplicar los [criterios Gartner 5-R](https://www.gartner.com/en/documents/3873016/evaluation-criteria-for-cloud-management-platforms-and-t) y optimizar la carga de trabajo.
- Determine el entorno de destino en Azure global:
  1. Identifique al inquilino de Azure global de destino (cree uno, si es necesario).
  1. Cree las suscripciones.
  1. Elija qué ubicación de Azure global desea migrar.
  1. Ejecute los escenarios de migración de prueba que coinciden con la arquitectura de Azure Alemania con la arquitectura en Azure global.
- Determine la escala de tiempo adecuada y el programa para la migración. Cree un plan de prueba de aceptación de usuario para cada paquete de migración.

### <a name="migrate"></a>Migrar

En la fase de migración, utilice las herramientas, técnicas y recomendaciones que se tratan en los artículos sobre migración de Azure Alemania para crear nuevos recursos en Azure global. A continuación, configure las aplicaciones.

### <a name="validate"></a>Validación

En la fase de validación, complete las tareas siguientes:

1. Realice las pruebas de aceptación de usuario.
1. Asegúrese de que las aplicaciones funcionen según lo previsto.
1. Sincronice los datos más recientes en el entorno de destino, si procede.
1. Cambie a una nueva instancia de la aplicación en Azure global.
1. Compruebe que el entorno de producción funciona según lo previsto.
1. Retire los recursos de Azure Alemania.

## <a name="terms"></a>Términos

Estos términos se utilizan en los artículos sobre la migración de Azure Alemania:

**Origen** describe de dónde se están migrando los recursos (por ejemplo, Azure Alemania):

- **Nombre del inquilino de origen**: el nombre del inquilino de Azure Alemania (todo lo situado después de **\@** en el nombre de cuenta). Todos los nombres de inquilinos en Azure Alemania terminan en **microsoftazure.de**.
- **Identificador de inquilino de origen**: el identificador del inquilino de Azure Alemania. El identificador de inquilino aparece en Azure Portal cuando se mueve el cursor sobre el nombre de la cuenta, en la esquina superior derecha.
- **Identificador de suscripción de origen**: el identificador de la suscripción de recursos en Azure Alemania. Puede tener más de una suscripción en el mismo inquilino. Asegúrese siempre de que está usando la suscripción correcta.
- **Región de origen**: cualquier región de Centro de Alemania (**germanycentral**) o Nordeste de Alemania (**germanynortheast**), en función de donde se encuentre el recurso que desea migrar.

**Destino** describe hacia dónde está migrando los recursos **:**

- **Nombre del inquilino de destino**: el nombre del inquilino en Azure global.
- **Identificador del inquilino de destino**: el identificador del inquilino en Azure global.
- **Identificador de la suscripción de destino**: el identificador de la suscripción para el recurso en Azure global.
- **Región de destino**: puede usar casi cualquier región de Azure global. Es probable que quiera migrar los recursos a Oeste de Europa (**westeurope**) o a Norte de Europa (**northeurope**).

> [!NOTE]
> Compruebe que el servicio de Azure que está migrando se ofrece en la región de destino. Todos los servicios de Azure que están disponibles en Azure Alemania están disponibles en Oeste de Europa. Todos los servicios de Azure que están disponibles en Azure Alemania están disponibles en Norte de Europa, excepto para Azure Machine Learning Studio (clásico) y para las máquinas virtuales de la serie G/GS de Azure Virtual Machines.

Es una buena idea marcar los portales de origen y destino en el explorador:

- El portal de Azure Alemania se encuentra en [https://portal.microsoftazure.de/](https://portal.microsoftazure.de/).
- El portal de Azure global está en [https://portal.azure.com/](https://portal.azure.com/).

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre herramientas, técnicas y recomendaciones para migrar los recursos en las siguientes categorías de servicio:

- [Proceso](./germany-migration-compute.md)
- [Redes](./germany-migration-networking.md)
- [Storage](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [Bases de datos](./germany-migration-databases.md)
- [Analytics](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Integración](./germany-migration-integration.md)
- [Identidad](./germany-migration-identity.md)
- [Seguridad](./germany-migration-security.md)
- [Herramientas de administración](./germany-migration-management-tools.md)
- [Elementos multimedia](./germany-migration-media.md)
