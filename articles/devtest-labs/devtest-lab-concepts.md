---
title: Conceptos de Azure DevTest Labs
description: Aprenda los conceptos básicos de DevTest Labs y cómo puede facilitar la creación, la administración y la supervisión de máquinas virtuales de Azure
ms.topic: conceptual
ms.date: 10/29/2021
ms.openlocfilehash: e66e3180900495d0d481742ab0fed172e25d9f3b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131475908"
---
# <a name="devtest-labs-concepts"></a>Conceptos de DevTest Labs

En este artículo se enumeran los conceptos y definiciones clave de DevTest Labs:

## <a name="lab"></a>Laboratorio
Un laboratorio es la infraestructura que abarca un grupo de recursos, como máquinas virtuales (VM), que permite una mejor administración de esos recursos especificando límites y cuotas.

## <a name="virtual-machine"></a>Máquina virtual
Una máquina virtual de Azure es un tipo de [recursos informáticos a petición y escalables](/azure/architecture/guide/technology-choices/compute-decision-tree) que ofrece Azure. Las máquinas virtuales de Azure le ofrecen la flexibilidad de la virtualización sin necesidad de adquirir ni mantener el hardware físico que las ejecutan.

[Información general sobre las máquinas virtuales Windows en Azure](../virtual-machines/windows/overview.md) proporciona información para tener en cuenta antes de crear una máquina virtual, cómo crearla y cómo administrarla.

## <a name="claimable-vm"></a>Creación de máquinas virtuales reclamables
Una máquina virtual reclamable de Azure es una máquina virtual disponible para cualquier usuario de laboratorio con permisos. Los administradores del laboratorio pueden preparar máquinas virtuales con imágenes base y artefactos específicos y luego guardarlas en un grupo compartido. Los usuarios del laboratorio pueden reclamar una máquina virtual del grupo cuando necesiten una con esa configuración específica.

Una máquina virtual reclamable no se asigna inicialmente a ningún usuario concreto, pero se mostrará en la lista de todos los usuarios en el grupo de máquinas virtuales que se pueden reclamar. Después de que un usuario reclame una máquina virtual, se mueve hasta su área **Mis máquinas virtuales** y ya no podrá reclamarla otro usuario.

## <a name="environment"></a>Entorno
En DevTest Labs, un entorno hace referencia a una colección de recursos de Azure en un laboratorio. En el artículo sobre la [creación de un entorno](./devtest-lab-create-environment-from-arm.md) se describe cómo crear entornos de varias máquinas virtuales a partir de las plantillas de Azure Resource Manager.

## <a name="base-images"></a>Imágenes base
Las imágenes base son imágenes de máquina virtual con todas las herramientas y la configuración preinstaladas y configuradas. Puede crear una máquina virtual seleccionando una base existente y agregando un artefacto para instalar su agente de prueba. El uso de imágenes base reduce el tiempo de creación de la máquina virtual.

## <a name="artifacts"></a>Artefactos
Los artefactos se utilizan para implementar y configurar la aplicación después de aprovisionar una máquina virtual. Los artefactos pueden ser:

* Herramientas que desea instalar en la máquina virtual, como agentes, Fiddler y Visual Studio.
* Acciones que desea ejecutar en la máquina virtual, como la clonación de un repositorio.
* Aplicaciones que desea probar.

Los artefactos son archivos JSON de [Azure Resource Manager](../azure-resource-manager/management/overview.md) que contienen instrucciones para implementar y aplicar la configuración.

## <a name="artifact-repositories"></a>Repositorios de artefacto
Los repositorios de artefactos son repositorios de Git donde se insertan los artefactos. Es posible agregar los repositorios de artefactos a varios laboratorios de la organización a fin de reutilizarlos y compartirlos.

## <a name="formulas"></a>Fórmulas
Las fórmulas proporcionan un mecanismo para el aprovisionamiento rápido de máquinas virtuales. Una fórmula en DevTest Labs es una lista de valores de propiedad predeterminados que se usan para crear una máquina virtual de laboratorio.
Mediante las fórmulas, las máquinas virtuales que tengan el mismo conjunto de propiedades, como la imagen base, el tamaño de máquina virtual, la red virtual y los artefactos, se pueden crear sin necesidad de especificar esas propiedades todas las veces. Al crear una máquina virtual a partir de una fórmula, los valores predeterminados pueden usarse tal como están o modificarlos.

## <a name="policies"></a>Directivas
Las directivas ayudan a controlar los costos en su laboratorio. Por ejemplo, puede crear una directiva para apagar automáticamente las máquinas virtuales según una programación definida.

## <a name="caps"></a>Límites
Los límites son un mecanismo que sirve para minimizar la pérdida en el laboratorio. Por ejemplo, puede establecer un límite para restringir el número de máquinas virtuales que se pueden crear por usuario o en un laboratorio.

## <a name="security-levels"></a>Niveles de seguridad
El acceso de seguridad lo determina el control de acceso basado en roles de Azure (Azure RBAC). Para entender cómo funciona el acceso, es importante comprender las diferencias entre un permiso, un rol y un ámbito según se define en Azure RBAC.

|Término | Descripción |
|---|---|
|Permiso|Acceso definido a una acción específica (por ejemplo, acceso de lectura a todas las máquinas virtuales).|
|Rol| Conjunto de permisos que se pueden agrupar y asignar a un usuario. Por ejemplo, el rol de *propietario de la suscripción* tiene acceso a todos los recursos dentro de una suscripción.|
|Ámbito| Nivel dentro de la jerarquía de un recurso de Azure, como un grupo de recursos, un único laboratorio o toda la suscripción.|


Dentro del ámbito de DevTest Labs, hay dos tipos de roles para definir los permisos de usuario: usuario de laboratorio y propietario de laboratorio.

|Rol | Descripción |
|---|---|
|Propietario del &nbsp; laboratorio| Tiene acceso a los recursos del laboratorio. El propietario de un laboratorio puede modificar las directivas, leer cualquier máquina virtual y escribir en ella, cambiarla, etc.|
|Usuario de laboratorio | Puede ver todos los recursos de laboratorio, como máquinas virtuales, directivas y redes virtuales, pero no puede modificar las directivas ni las máquinas virtuales creadas por otros usuarios.|

Para ver cómo crear roles personalizados en DevTest Labs, consulte el artículo [Concesión de permisos de usuario a directivas específicas de laboratorio](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Dado que los ámbitos son jerárquicos, cuando un usuario tiene permisos en un ámbito determinado, también tiene permisos en cada ámbito de nivel inferior. Los propietarios de la suscripción tienen acceso a todos los recursos de una suscripción, que incluyen máquinas virtuales, redes virtuales y laboratorios. El propietario de una suscripción hereda automáticamente el rol de propietario de laboratorio. Sin embargo, lo contrario no es cierto. El propietario de un laboratorio tiene acceso a un laboratorio, que es un ámbito inferior al del nivel de suscripción. Por lo tanto, un propietario del laboratorio no puede ver máquinas virtuales ni redes virtuales ni ningún recurso que se encuentra fuera del laboratorio.

## <a name="azure-resource-manager-templates"></a>Plantillas del Administrador de recursos de Azure
Los conceptos tratados en este artículo pueden configurarse mediante el uso de las plantillas de Azure Resource Manager (ARM). Las plantillas de ARM le permiten definir la configuración y la infraestructura de la solución de Azure, e implementarla varias veces en un estado coherente.

[Formato de plantilla](../azure-resource-manager/templates/syntax.md#template-format) describe la estructura de una plantilla de Azure Resource Manager y las propiedades que están disponibles en las diferentes secciones de una plantilla.

## <a name="next-steps"></a>Pasos siguientes

[Creación de un laboratorio en DevTest Labs](devtest-lab-create-lab.md)