---
title: Directiva de seguridad y cumplimiento
description: En este artículo se proporciona orientación sobre la directiva de la empresa y el cumplimiento de la infraestructura de Azure DevTest Labs.
ms.topic: how-to
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 659f025e45ba3101b6bdebb30e9043490e5a3106
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128623634"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---company-policy-and-compliance"></a>Gobernanza de la infraestructura de Azure DevTest Labs: directiva de la empresa y cumplimiento
En este artículo se proporciona orientación sobre la directiva de la empresa y el cumplimiento de la infraestructura de Azure DevTest Labs. 

## <a name="public-vs-private-artifact-repository"></a>Repositorio de artefactos público frente a privado

### <a name="question"></a>Pregunta
¿Cuándo debería una organización utilizar un repositorio de artefactos público frente a un repositorio de artefactos privado en DevTest Labs?

### <a name="answer"></a>Respuesta
El [repositorio de artefactos público](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) proporciona un conjunto inicial de paquetes de software que se utilizan con mayor frecuencia. Ayuda a una rápida implementación sin tener que invertir tiempo en reproducir herramientas y complementos comunes para desarrolladores. Puede elegir implementar su propio repositorio privado. Puede utilizar un repositorio público y uno privado en paralelo. También puede elegir deshabilitar el repositorio público. Los criterios para implementar un repositorio privado deben estar guiados por las siguientes preguntas y consideraciones:

- ¿Tiene la organización el requisito de tener un software corporativo con licencia como parte de su oferta de DevTest Labs? Si la respuesta es afirmativa, entonces se debe crear un repositorio privado.
- ¿Desarrolla la organización software personalizado que proporciona una operación específica, que se requiere como parte del proceso general de aprovisionamiento? Si la respuesta es afirmativa, se debe implementar un repositorio privado.
- Si la directiva de gobernanza de la organización requiere aislamiento y los repositorios externos no están bajo la administración directa de la configuración por parte de la organización, se debe implementar un repositorio de artefactos privado. Como parte de este proceso, una copia inicial del repositorio público se puede copiar e integrar con el repositorio privado. Después, el repositorio público se puede deshabilitar para que nadie dentro de la organización pueda acceder a él. Este enfoque obliga a todos los usuarios dentro de la organización a tener un único repositorio aprobado por ella y minimizar la deriva de la configuración.

### <a name="single-repository-or-multiple-repositories"></a>Repositorio único o varios repositorios 

### <a name="question"></a>Pregunta
¿Debería una organización planear para un único repositorio o permitir múltiples repositorios?

### <a name="answer"></a>Respuesta
Como parte de la estrategia general de gobernanza y administración de configuración de la organización, le recomendamos que utilice un repositorio centralizado. Cuando utiliza varios repositorios, pueden convertirse en silos de software no administrado con el tiempo. Con un repositorio central, múltiples equipos pueden consumir artefactos de este repositorio para sus proyectos. Impone la estandarización, la seguridad, la facilidad de administración y elimina la duplicación de esfuerzos. Como parte de la centralización, las siguientes acciones son procedimientos recomendados para la administración a largo plazo y la sostenibilidad:

- Asocie los servicios de Azure Repos con el mismo inquilino de Azure Active Directory que la suscripción de Azure está utilizando para la autenticación y autorización.
- Cree un grupo llamado **Todos los desarrolladores de DevTest Labs** en Azure Active Directory que se administre de forma centralizada. Cualquier desarrollador que contribuya al desarrollo de artefacto debe colocarse en este grupo.
- El mismo grupo de Azure Active Directory puede usarse para proporcionar acceso al repositorio de Azure Repos y al laboratorio.
- En Azure Repos, las ramificaciones o bifurcaciones deben utilizarse para separar un repositorio en desarrollo del repositorio de producción principal. El contenido solo se agrega a la rama principal con una solicitud de incorporación de cambios después de una revisión apropiada del código. Una vez que el revisor de código aprueba el cambio, un desarrollador jefe, que es responsable del mantenimiento de la rama principal, combina el código actualizado. 

## <a name="corporate-security-policies"></a>Directivas de seguridad corporativas

### <a name="question"></a>Pregunta
¿Cómo puede una organización asegurarse de que las directivas de seguridad corporativas están en vigor?

### <a name="answer"></a>Respuesta
Una organización puede lograrlo realizando las siguientes acciones:

1. Desarrollar y publicar una directiva de seguridad integral. La directiva articula las reglas de uso aceptable asociadas con el uso de recursos de nube y software. También define lo que claramente infringe la directiva. 
2. Desarrollar una imagen personalizada, artefactos personalizados y un proceso de implementación que permita la orquestación dentro del ámbito de la seguridad que se define con el directorio activo. Este enfoque refuerza los límites corporativos y establece un conjunto común de controles ambientales. Estos controles contra el entorno que un desarrollador puede considerar al desarrollar y seguir un ciclo de vida de desarrollo seguro como parte de su proceso general. El objetivo también es proporcionar un entorno que no sea excesivamente restrictivo y que pueda obstaculizar el desarrollo, sino un conjunto razonable de controles. Las directivas de grupo en la unidad organizativa que contiene máquinas virtuales de laboratorio podrían ser un subconjunto del total de directivas de grupo que se encuentran en producción. Además, pueden ser un conjunto adicional para mitigar adecuadamente cualquier riesgo identificado.

## <a name="data-integrity"></a>Integridad de datos

### <a name="question"></a>Pregunta
¿Cómo puede una organización garantizar la integridad de datos para asegurar que los desarrolladores remotos no puedan quitar código o introducir malware o software no aprobado?

### <a name="answer"></a>Respuesta
Hay varias capas de control para mitigar la amenaza de consultores externos, contratistas o empleados remotos para colaborar en DevTest Labs. 

Como se dijo anteriormente, el primer paso debe tener una directiva de uso aceptable redactada y definida que describa claramente las consecuencias cuando alguien infringe la directiva. 

La primera capa de controles para el acceso remoto es aplicar una directiva de acceso remoto mediante una conexión VPN que no esté conectada directamente al laboratorio. 

La segunda capa de controles consiste en aplicar un conjunto de objetos de directiva de grupo que impiden copiar y pegar mediante el escritorio remoto. Se podría implementar una directiva de red para no permitir que los servicios salientes del entorno, como los servicios FTP y RDP, queden fuera del entorno. El enrutamiento definido por el usuario podría obligar a todo el tráfico de red de Azure de nuevo a un entorno local, pero el enrutamiento no podría dar cuenta de todas las direcciones URL que podrían permitir la carga de datos a menos que se controlen mediante un proxy para escanear el contenido y las sesiones. Las direcciones IP públicas podrían restringirse dentro de la red virtual que admite DevTest Labs para no permitir el protocolo de puente de un recurso de red externo.

En última instancia, es necesario aplicar el mismo tipo de restricciones en toda la organización, lo que también tendría que tener en cuenta todos los métodos posibles de medios extraíbles o direcciones URL externas que podrían aceptar un mensaje de contenido. Consulte con el profesional de seguridad para revisar e implementar una directiva de seguridad. Para más recomendaciones, consulte [Ciberseguridad de Microsoft](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).


## <a name="next-steps"></a>Pasos siguientes
Consulte [Integración y migración de aplicaciones](devtest-lab-guidance-governance-application-migration-integration.md).
