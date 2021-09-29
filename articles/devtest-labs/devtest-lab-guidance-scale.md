---
title: Escalado vertical de la infraestructura de Azure DevTest Labs
description: En este artículo se proporcionan instrucciones para escalar verticalmente la infraestructura de Azure DevTest Labs.
ms.topic: how-to
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: ceba7023df2a704d43a47f3804c18fd6c0bb113d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128550754"
---
# <a name="scale-up-your-azure-devtest-labs-infrastructure"></a>Escalado vertical de la infraestructura de Azure DevTest Labs
Antes de implementar DevTest Labs a escala empresarial, hay varios puntos de decisión claves. Reconocer estos puntos de decisión a un nivel alto ayuda a las organizaciones con las decisiones de diseño en el futuro. Sin embargo, estos puntos no deben impedir que una organización empiece una prueba de concepto. Las tres áreas principales para el planeamiento del escalado vertical inicial son:

- Redes y seguridad
- Topología de la suscripción
- Roles y responsabilidades

## <a name="networking-and-security"></a>Redes y seguridad
La seguridad y las redes son una parte fundamental en todas las organizaciones. Aunque una implementación en toda la empresa requiere un análisis más profundo, hay un número reducido de requisitos para realizar correctamente una prueba de concepto. Algunas áreas de enfoque clave incluyen:

- **Suscripción de Azure**: para implementar DevTest Labs, debe tener acceso a una suscripción de Azure con los derechos adecuados para crear recursos. Hay varias maneras de obtener acceso a las suscripciones de Azure, como un Contrato Enterprise y una suscripción de pago por uso. Para obtener más información sobre cómo obtener acceso a una suscripción de Azure, vea [Licencias de Azure para la empresa](https://azure.microsoft.com/pricing/enterprise-agreement/).
- **Acceso a recursos locales**: algunas organizaciones requieren que sus recursos de DevTest Labs tengan acceso a recursos locales. Se necesita una conexión segura desde el entorno local a Azure. Por tanto, es importante que configure una conexión de VPN o ExpressRoute antes de empezar. Para más información, vea [Información general sobre Virtual Network](../virtual-network/virtual-networks-overview.md).
- **Requisitos de seguridad adicionales**: otros requisitos de seguridad, como las directivas de equipo, el acceso a las direcciones IP públicas y la conexión a Internet, son escenarios que se deben antes de implementar una prueba de concepto. 

## <a name="subscription-topology"></a>Topología de la suscripción
La topología de la suscripción es una consideración de diseño fundamental para implementar DevTest Labs en la empresa. Sin embargo, no es necesario consolidar todas las decisiones hasta después de haber completado una prueba de concepto. Al evaluar el número de suscripciones necesarias para una implementación empresarial, hay dos extremos: 

- Una suscripción para toda la organización
- Suscripción por usuario

A continuación, se resaltan las ventajas de cada enfoque.

### <a name="one-subscription"></a>Una suscripción
A menudo, el enfoque de una suscripción no es fácil de administrar en una gran empresa. Sin embargo, la limitación del número de suscripciones proporciona las siguientes ventajas:

- **Previsión** de los costos de la empresa.  Realizar presupuestos resulta mucho más sencillo en una sola suscripción porque todos los recursos están en un único grupo. Este enfoque permite simplificar la toma de decisiones sobre cuándo adoptar medidas de control de costos en un momento dado de un ciclo de facturación.
- La **manejabilidad** de máquinas virtuales, artefactos, fórmulas, configuraciones de red, permisos, directivas, etc. es más fácil, ya que todas las actualizaciones son necesarias solo en una suscripción, en lugar de realizar actualizaciones en varias suscripciones.
- Los trabajos de **red** se simplifican en gran medida en una sola suscripción para empresas donde la conectividad local es un requisito. Conectar redes virtuales entre suscripciones (modelo tipo hub-and-spoke) es necesario con suscripciones adicionales, lo que requiere configuración adicional, administración, espacios de direcciones IP, etc.
- La **colaboración en equipo** es más fácil cuando todos trabajan en la misma suscripción: por ejemplo, es más fácil volver a asignar una máquina virtual a un compañero de trabajo, compartir los recursos del equipo, etc.

### <a name="subscription-per-user"></a>Suscripción por usuario
Una suscripción independiente por usuario proporciona igualdad de oportunidades en el espectro de alternativo. Las ventajas de tener muchas suscripciones incluyen:

- Las **cuotas de escalado de Azure** no impiden la adopción. Por ejemplo, en el momento en que se escribe este artículo, Azure admite 200 cuentas de almacenamiento por suscripción. Existen cuotas operativas para la mayoría de los servicios de Azure (muchas se pueden personalizar, pero otras no). En este modelo de una suscripción por usuario, es muy poco probable que se alcancen la mayoría de las cuotas. Para obtener más información sobre las cuotas de escalado de Azure actuales, vea: [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Los **contracargos** a grupos o desarrolladores individuales son más sencillos, lo que permite a las organizaciones tener en cuenta los costos mediante su modelo actual.
- La **propiedad y los permisos** de los entornos de DevTest Labs son sencillos. Se proporciona a los desarrolladores el acceso de nivel de suscripción y son responsables de todo, como configuración de redes, directivas de laboratorio y administración de máquinas virtuales.

En la empresa, puede haber suficientes restricciones en los extremos del espectro. Por tanto, puede que deba configurar suscripciones de forma que se encuentre en el medio de estos extremos. Como procedimiento recomendado, el objetivo de una organización debe ser usar el menor número de suscripciones posible, teniendo en cuenta las funciones forzosas que aumentan el número total de suscripciones. Hay que reiterar que topología de suscripción es fundamental para una implementación empresarial de DevTest Labs, pero no debe retrasar la prueba de concepto. Hay detalles adicionales en el artículo de [gobernanza](devtest-lab-guidance-governance-policy-compliance.md) sobre cómo decidir sobre la granularidad del laboratorio y la suscripción en la organización.

## <a name="roles-and-responsibilities"></a>Roles y responsabilidades
Una prueba de concepto de DevTest Labs tiene tres roles principales con responsabilidades definidas: propietario de la suscripción, propietario de DevTest Labs, usuario de DevTest Labs y, opcionalmente, un colaborador.

- **Propietario de la suscripción**: el propietario de la suscripción tiene derechos para administrar una suscripción de Azure, como asignar usuarios, administrar directivas, crear y administrar la topología de red, solicitar el aumento de cuotas, etc. Para obtener más información, consulte [este artículo](../role-based-access-control/rbac-and-directory-admin-roles.md).
- **Propietario de DevTest Labs**: el propietario de DevTest Labs tiene acceso administrativo completo al laboratorio. Esta persona es responsable de agregar o quitar usuarios, administrar la configuración de costos, la configuración de laboratorio general y otras tareas basadas en máquinas virtuales o artefactos. Un propietario de laboratorio también tiene todos los derechos de un usuario de DevTest Labs.
- **Usuario de DevTest Labs**: el usuario de DevTest Labs puede crear y consumir las máquinas virtuales del laboratorio. Estas personas tienen algunas funcionalidades administrativas mínimas en las máquinas virtuales que crean (iniciar, detener, eliminar y configurar sus máquinas virtuales). Los usuarios no pueden administrar máquinas virtuales de otros usuarios.

## <a name="next-steps"></a>Pasos siguientes
Consulte el siguiente artículo de esta serie: [Orquestación de la implementación de Azure DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)