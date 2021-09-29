---
title: Uso de funcionalidades de notificación
description: Obtenga información sobre los diferentes escenarios para usar las funcionalidades reclamación o de anulación de reclamaciones de Azure DevTest Labs
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: d50ab660433ac9b2616a897e1110d466eb143a9a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128559292"
---
# <a name="use-claim-capabilities-in-azure-devtest-labs"></a>Usar las funcionalidades de reclamación en Azure DevTest Labs
El servicio Azure DevTest Labs mejora la efectividad y la eficiencia de los desarrolladores y los evaluadores. Este artículo se centra en la capacidad de reclamar o anular la reclamación de las máquinas virtuales en Azure DevTest Labs. También enumera varias mejoras que ofrece esta característica en la experiencia del usuario. Antes de ver diferentes escenarios en los que se puede usar esta característica, veamos qué es una **reclamación** y cómo funciona.

## <a name="claimable-machines"></a>Máquinas reclamables
Una máquina reclamable es una máquina virtual (VM) que se crea en un laboratorio sin un propietario. Una vez que se reclama la máquina, el usuario tiene una gama completa de opciones para esa VM. Cuando un usuario reclama una máquina, se realizan algunos cambios. La VM se mueve de la lista **Máquinas virtuales que se pueden reclamar** a la lista **Mis máquinas virtuales** en Azure Portal. 

El usuario puede conectarse a la VM, personalizar artefactos y reiniciar, detener o reclamar la máquina. Hay un par de formas de hacer que una VM sea reclamable:

- Cree una máquina y reclámela para que se mueva al grupo reclamable. 
- Cree una VM y colóquela en el grupo compartido mediante la [configuración avanzada](https://azure.microsoft.com/updates/azure-devtest-labs-claim-lab-vms-from-a-shared-pool/).

Hay dos casos en los que las funcionalidades de reclamación y anulación de la reclamación se pueden usar de manera eficaz. El primer caso requiere más previsión y planificación, para ser diseñado y ejecutado adecuadamente. Asimismo, el segundo es algo más situacional. A continuación tiene algunos ejemplos de los diferentes casos.

## <a name="designed-use-of-claimable-machines"></a>Uso diseñado de máquinas reclamables

- **Desarrollo y prueba de software**: permita que los desarrolladores o evaluadores sean más productivos con máquinas configuradas preparadas y en estado de anulación de reclamación. Al tener un conjunto de VM con diferentes configuraciones, herramientas necesarias y con el último código, los usuarios pueden reclamar una VM y comenzar a trabajar sin tener que perder el tiempo configurando una máquina. Antes de reclamar las VM, las máquinas se aprovisionan pero también se apagan, lo que minimiza el costo de tener máquinas que se usan con menos frecuencia. Cuando se necesitan las VM, el usuario solo debe reclamar la VM, lo que iniciará la máquina. La opción de anular la reclamación no es tan útil en este caso, ya que crear una nueva VM a menudo es más fácil y más barato.
- **Aulas y laboratorios**: tenga ya configuradas las máquinas virtuales de una clase o un laboratorio, y así los alumnos puedan conectarse de inmediato a una máquina con Azure Portal.  Una vez que un alumno reclama una VM, el laboratorio se asegura de que nadie pueda reclamar la misma máquina. La automatización de este proceso garantiza que esté disponible el número requerido de máquinas con el entorno especificado. Si los alumnos no se presentan o llegan tarde, las máquinas no reclamadas pueden mantenerse disponibles hasta que termine la sesión con un costo mínimo. La opción de anular la reclamación no es tan efectiva en este escenario ya que la VM tiene en un estado desconocido cuando el usuario anterior termina de usarla.
- **Demostraciones**: use máquinas para realizar demostraciones, donde las máquinas del laboratorio estén configuradas con entornos específicos. Esta funcionalidad es útil cuando varias personas quieren ofrecer una demostración al mismo tiempo o en momentos aleatorios, como en una conferencia. La opción de anular la reclamación puede ser útil en esta situación, ya que la demostración no cambia el estado de la máquina, lo que permite a los usuarios devolver una VM al grupo reclamable para la próxima demostración. Dado que la máquina no reclamada se desaprovisiona y tiene un costo mínimo, las VM se pueden dejar en el laboratorio durante períodos de tiempo más largos.
- **Trabajadores temporales o por contrato**: permita que los usuarios usen una máquina. Cuando se van, devuelven la VM al grupo reclamable sin que se pierdan datos. En cuanto a la VM no reclamada, otro usuario puede reclamarla y continuar trabajando o revisar la máquina para obtener información adicional.
- **En general**: la posibilidad de tener un único origen para configurar e implementar automáticamente máquinas virtuales, a un ritmo específico, es útil en muchas situaciones diferentes. Hay varias situaciones diferentes en las que la característica de reclamación o de anulación de la reclamación ayuda a los usuarios a ser más eficientes, ya que cuentan con un proceso automatizado para compilar las VM en un estado de no reclamación con una configuración establecida. Las configuraciones pueden incluir diferentes sistemas operativos, idiomas, discos u [otro software (artefactos)](devtest-lab-artifact-author.md) según sus necesidades. La capacidad de reclamar una VM del laboratorio permite al usuario de ese laboratorio obtener un sistema configurado correctamente sin perder el tiempo o dedicar sus esfuerzos en configurar la máquina. El administrador del laboratorio puede usar el estado de reclamación de las VM para mejorar la cantidad de máquinas generadas, limpiar máquinas y determinar la prioridad de las configuraciones. El [generador de imágenes](image-factory-create.md) es un buen ejemplo de un proceso automatizado para compilar VM e imágenes para varios laboratorios. Los scripts pueden modificarse para ejecutar cualquiera de las siguientes situaciones con los cambios apropiados o usarse como referencia para crear un sistema personalizado.

## <a name="situational-use-of-claimable-machines"></a>Uso situacional de máquinas reclamables

- Use la funcionalidad de reclamación o de anulación de reclamaciones para permitir que lo usuarios se pasen el control de las máquinas de uno a otro y así no tener que saber explícitamente quién usará la máquina a continuación.
- Desarrollo, pruebas y depuración de un escenario en el que una configuración específica de la máquina puede reproducir un error; a continuación, se puede anular la reclamación de la máquina para permitir que otro desarrollador pueda reclamarla y continuar trabajando. Esta característica es especialmente útil, ya que más personas trabajan de forma remota en diferentes áreas del mundo. 
- Los miembros del equipo pueden trabajar con un solo entorno. Por ejemplo, puede configurar manualmente un entorno complejo que no se puede automatizar o crear recursos que solo puedan manejar las modificaciones de una sola entrada, como imágenes. En el pasado, este problema se solucionaba con una máquina dedicada en funcionamiento. La característica reclamable es una mejora del proceso manual, ya que tiene control de acceso de usuario integrado e identificación visual cuando esté disponible. Cuando no se reclama, la VM se desactiva para reducir costos.
- Debe tener un disco de datos conectado a una VM. Cada disco de hasta 1 TB de datos le permite pasar un gran volumen de datos sin tener que copiar o duplicarlos. La VM se crea inicialmente con un disco conectado que tiene un gran volumen de datos.  Cualquier usuario puede reclamar la máquina y acceder a esos datos. Cuando termine, anule la reclamación de la VM para permitir que otros usuarios accedan a la misma.

Existen algunas advertencias sobre el uso de máquinas reclamables, las más comunes se refieren al acceso a la máquina. Si la máquina está unida al dominio, entonces el usuario que la reclama deberá tener acceso; esto se hace generalmente otorgando acceso a un grupo que abarca a todos los usuarios del laboratorio cuando se crea la VM. Si la máquina no está unida al dominio, será necesario ejecutar el artefacto **Restablecer la contraseña de VM** en el repositorio público para agregar al usuario como administrador.  Los artefactos se pueden aplicar incluso después de que la máquina se haya iniciado o reclamado.

## <a name="next-steps"></a>Pasos siguientes
Consulte el artículo [Creación y administración de VM reclamables en Azure DevTest Labs](devtest-lab-add-claimable-vm.md).
