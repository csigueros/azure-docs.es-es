---
title: Uso de los servicios de plataforma como servicio (PaaS)
description: Obtenga información sobre cómo usar los servicios de plataforma como servicio (PaaS) en Azure DevTest Labs.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 8d4bcf98d425e322c18e13ec99b525cf1442bc49
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128548134"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>Uso de servicios de plataforma como servicio (PaaS) en Azure DevTest Labs
PaaS se admite en DevTest Labs a través de la característica de entornos. Los entornos en DevTest Labs se admiten mediante plantillas preconfiguradas de Azure Resource Manager en un repositorio de Git. Los entornos pueden contener recursos de IaaS y PaaS. Le permiten crear sistemas complejos que pueden incluir recursos de Azure, como máquinas virtuales, bases de datos, redes virtuales y aplicaciones web, que se personalizan para que trabajen en conjunto. Estas plantillas permiten una implementación coherente y una mejor administración de entornos con control de código fuente. 

Una correcta configuración del sistema permite los siguientes escenarios: 

- La posibilidad de que los desarrolladores tengan varios entornos, incluidos entornos independientes.
- Pruebas en diferentes configuraciones de forma asincrónica.
- Integración en las canalizaciones de ensayo y producción sin realizar ningún cambio en la plantilla.
- La posibilidad de tener entornos y máquinas de desarrollo en el mismo laboratorio mejora la facilidad de administración e informes de costo.  

El proveedor de recursos de DevTest Labs crea recursos en nombre del usuario del laboratorio, por lo que no es necesario asignar ningún permiso adicional a los usuarios del laboratorio para habilitar el uso de los recursos de PaaS. La siguiente imagen muestra un clúster de Service Fabric como entorno en el laboratorio.

![Clúster de Service Fabric como entorno](./media/create-environment-service-fabric-cluster/cluster-created.png)

Para obtener más información sobre cómo configurar los entornos, consulte [Create multi-VM environments and PaaS resources with Azure Resource Manager templates](devtest-lab-create-environment-from-arm.md) (Creación de entornos de varias VM y recursos de PaaS con las plantillas de Azure Resource Manager). DevTest Labs tiene un repositorio público de plantillas de Azure Resource Manager que puede usar para crear entornos sin tener que conectarse a un origen externo de GitHub por sí mismo. Para más información sobre los entornos públicos, consulte [Configuración y uso de entornos públicos en Azure DevTest Labs](devtest-lab-configure-use-public-environments.md).

En organizaciones grandes, los equipos de desarrollo suelen proporcionan entornos, como entornos de prueba aislados y personalizados. Puede haber entornos usados por todos los equipos de una división o unidad de negocio. El grupo de TI puede proporcionar sus entornos, que pueden usarlos todos los equipos de la organización.  

## <a name="customizations"></a>Personalizaciones

#### <a name="sandbox"></a>Espacio aislado 
El propietario del laboratorio puede personalizar los entornos de laboratorio para cambiar el rol del usuario de **lector** a **colaborador** dentro del grupo de recursos. Esta funcionalidad está en la página **Configuración del laboratorio** en **Configuración y directivas** del laboratorio. Este cambio en el rol permite que el usuario agregue o quite recursos dentro de ese entorno. Si quiere restringir aún más el acceso, use las directivas de Azure. Esta funcionalidad le permite personalizar los recursos o la configuración sin el acceso en el nivel de suscripción.

#### <a name="custom-tokens"></a>Tokens personalizados
Hay cierta información de laboratorio personalizada que está fuera del grupo de recursos y es específica para los entornos a los que puede tener acceso la plantilla. Estas son algunas de ellas: 

- Identificación de la red del laboratorio
- Location
- Cuenta de Storage donde se almacenan los archivos de plantilla de Resource Manager. 
 
#### <a name="lab-virtual-network"></a>Red virtual del laboratorio
En el artículo [Conexión de entornos a la red virtual del laboratorio](connect-environment-lab-virtual-network.md) se describe cómo modificar la plantilla de Resource Manager para usar el token `$(LabSubnetId)`. Cuando se crea un entorno, el token `$(LabSubnetId)` se reemplaza por la primera marca de la subred donde la opción **Usar en la creación de máquinas virtuales** está establecida en **true**. Permite que nuestro entorno use redes creadas anteriormente. Si quiere usar las mismas plantillas de Resource Manager en entornos de prueba como ensayo y producción, use `$(LabSubnetId)` como valor predeterminado en un parámetro de plantilla de Resource Manager. 

#### <a name="environment-storage-account"></a>Cuenta de Storage del entorno
DevTest Labs admite el uso de [plantillas anidadas de Resource Manager](../azure-resource-manager/templates/linked-templates.md). En el artículo [Deploy nested Azure Resource Manager templates for testing environments](deploy-nested-template-environments.md) (Implementación de plantillas anidadas de Resource Manager para ambientes de prueba) se explica cómo usar los tokens `_artifactsLocation` y `_artifactsLocationSasToken` para crear un URI para una plantilla de Resource Manager en la misma carpeta que la de la plantilla principal o en una carpeta anidada de esta. Para obtener más información sobre estos dos tokens, consulte la sección **Deployment artifacts** (Artefactos de implementación) de [Azure Resource Manager – Best Practices Guide](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md) (Azure Resource Manager: guía de procedimientos recomendados).

## <a name="user-experience"></a>Experiencia del usuario

## <a name="developer"></a>Desarrollador
Los desarrolladores usan el mismo flujo de trabajo para crear una VM que para crear un entorno específico. Seleccionan el entorno frente a la imagen de la máquina y escriben la información necesaria requerida por la plantilla. Cada desarrollador que tenga un entorno permite la implementación de cambios y una depuración mejorada del bucle interno. El entorno se puede crear en cualquier momento mediante la plantilla más reciente.  Esta característica permite destruir los entornos y volver a crearlos para ayudar a reducir el tiempo de inactividad al tener que crear el sistema de forma manual o recuperarse de pruebas de errores.  

### <a name="testing"></a>Prueba
Los entornos de DevTest Labs permiten comprobaciones independientes de configuraciones y código específicos de forma asincrónica. Lo más habitual es configurar el entorno con el código de la solicitud de incorporación de cambios individuales e iniciar las pruebas automatizadas. Una vez que las pruebas automatizadas se ejecutan hasta completarse, se pueden ejecutar prueba manuales en el entorno específico. Normalmente, este proceso se realiza como parte de la canalización de CI/CD. 

## <a name="management-experience"></a>Experiencia de administración

### <a name="cost-tracking"></a>Seguimiento de costos
La característica de seguimiento de costos incluye recursos de Azure en los distintos entornos como parte de la tendencia de costos total. El costo por recursos no interrumpe los distintos recursos dentro del entorno, pero muestra el entorno como un solo costo.

### <a name="security"></a>Seguridad
Una suscripción a Azure configurada correctamente con DevTest Labs puede [limitar el acceso a recursos de Azure solo a través del laboratorio](devtest-lab-add-devtest-user.md). Con los entornos, un propietario de laboratorio puede permitir que los usuarios tengan acceso a los recursos de PaaS con configuraciones aprobadas sin permitir el acceso a otros recursos de Azure. En el caso en que los usuarios del laboratorio personalizan entornos, el propietario del laboratorio puede permitir el acceso de colaborador. El acceso de colaborador permite que el usuario del laboratorio agregue o quite recursos de Azure solo dentro del grupo de recursos administrados. Permite un seguimiento y una administración más fáciles en relación con el permiso de acceso de colaborador usuario a la suscripción.

### <a name="automation"></a>Automatización
Automation es un componente clave para una gran escala y un ecosistema eficaz. Automation es necesaria para controlar la administración o el seguimiento de varios entornos a través de suscripciones y laboratorios.

### <a name="cicd-pipeline"></a>Canalización de CI/CD
Los servicios de PaaS en DevTest Labs pueden ayudar a mejorar la canalización de CI/CD centrándose en las implementaciones en las que el acceso lo controla el laboratorio.

## <a name="next-steps"></a>Pasos siguientes
Consulte estos artículos para obtener detalles sobre los entornos: 

- 
- [Creación de entornos de varias máquinas virtuales y recursos de PaaS con plantillas de Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configuración y uso de entornos públicos en Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Creación de un entorno con un clúster de Service Fabric independiente en Azure DevTest Labs](create-environment-service-fabric-cluster.md)
- [Conexión de un entorno a la red virtual del laboratorio en Azure DevTest Labs](connect-environment-lab-virtual-network.md)
- [Integración de entornos en las canalizaciones CI/CD de Azure DevOps](integrate-environments-devops-pipeline.md)
 
