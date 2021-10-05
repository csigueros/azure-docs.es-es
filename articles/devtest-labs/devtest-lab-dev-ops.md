---
title: Integración de Azure DevTest Labs y DevOps
description: Obtenga información sobre cómo usar los laboratorios de Azure DevTest Labs dentro de canalizaciones de integración continua (CI) o entrega continua (CD) en un entorno empresarial.
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: 9c18df3c651dc3f11bbaaec4e71aea38644dffe1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128595092"
---
# <a name="integration-of-azure-devtest-labs-and-azure-devops"></a>Integración de Azure DevTest Labs y DevOps
DevOps es una metodología de desarrollo de software que integra el desarrollo de software (Dev) con las operaciones (Ops) en un sistema. Este sistema puede ofrecer nuevas características, actualizaciones y correcciones acordes a los objetivos empresariales. Esta metodología abarca todo, desde el diseño de nuevas características en función de objetivos, patrones de uso y comentarios de los clientes, hasta la corrección, recuperación y protección del sistema cuando se producen problemas. Un componente de esta metodología fácil de identificar es la canalización de integración continua (CI) y entrega continua (CD). Una canalización de CI/CD toma la información, el código y los recursos de una confirmación a través de una serie de pasos que incluyen compilación, pruebas e implementación, para generar el sistema. Este artículo se centra en distintas maneras de usar eficazmente los laboratorios en una canalización en un entorno empresarial. 

## <a name="benefits-of-using-labs-in-devops-workflow"></a>Ventajas del uso de laboratorios de flujo de trabajo de DevOps 

### <a name="focused-access"></a>Acceso centrado 
El uso de un laboratorio como un componente permite asociar un ecosistema específico a un grupo limitado de usuarios. Normalmente, a un equipo o grupo que trabaja en un área común o una característica específica se le asigna un laboratorio.   

### <a name="infrastructure-replication-in-the-cloud"></a>Replicación de la infraestructura en la nube 
Un desarrollador puede configurar rápidamente un ecosistema de desarrollo que incluya un marco de desarrollo con código fuente y herramientas. El desarrollador también puede crear un entorno prácticamente idéntico a la configuración de producción. Facilita el desarrollo de bucles internos más rápido. 

### <a name="pre-production"></a>Preproducción 
Tener un laboratorio en la canalización de CI/CD facilita la presencia de varios entornos o equipos de preproducción diferentes que se ejecutan al mismo tiempo para realizar pruebas asincrónicas. En un laboratorio se pueden implementar y administrar otras infraestructuras de soporte, como los agentes de compilación. 

## <a name="devops-with-devtest-labs"></a>DevOps con DevTest Labs 

### <a name="development--operation"></a>Desarrollo y operación 
Un laboratorio se debe centrar en un equipo que trabaje en un área de características. Este enfoque común admite el uso compartido de recursos específicos del área, como herramientas, scripts o plantillas de Resource Manager. Permite cambios más rápidos al tiempo que limita los efectos negativos a un grupo más reducido. Estos recursos compartidos permiten al desarrollador crear máquinas virtuales de desarrollo con todo el código, herramientas y configuración necesarios. Se pueden crear de forma dinámica, o bien contar con un sistema que cree imágenes base con las personalizaciones. El desarrollador no solo puede crear máquinas virtuales, sino también entornos de DevTest Labs según las plantillas necesarias para crear los recursos de Azure adecuados en el laboratorio. Los cambios o el trabajo destructivo se pueden realizar en el entorno de laboratorio sin que afecten a nadie más. Considere el escenario donde el producto es un sistema independiente instalado en el equipo del cliente. En este escenario, DevTest Labs ha mejorado la creación de máquinas virtuales que incluye la instalación de software adicional mediante artefactos y la compilación previa de configuraciones de cliente para realizar pruebas más rápidas del bucle interno del código. 
  
## <a name="cicd-pipeline"></a>Canalización de integración y entrega continuas 
La canalización de CI/CD es uno de los componentes fundamentales de DevOps que mueve código de una solicitud de incorporación de cambios del desarrollador, lo integra con el código existente y lo implementa en el ecosistema de producción. No es necesario que todos los recursos estén dentro de un laboratorio. Por ejemplo, se podría configurar un host Jenkins fuera de laboratorio como un recurso más persistente. Estos son algunos ejemplos específicos de la integración de laboratorios en la canalización. 

### <a name="build"></a>Compilar 
La canalización de compilación se centra en crear un paquete de componentes que se probarán de forma conjunta para su entrega a la canalización de versión. Los laboratorios pueden formar parte de la canalización de compilación como ubicación para los agentes de compilación y otros recursos de soporte técnico. La capacidad de crear la infraestructura de forma dinámica permite tener un mayor control. Con la capacidad de tener varios entornos en un laboratorio, cada compilación se puede ejecutar de forma asincrónica mientras se usa el identificador de compilación como parte de la información del entorno para identificar de forma exclusiva los recursos necesarios para la compilación específica.   

Para los agentes de compilación, la capacidad del laboratorio de restringir el acceso aumenta la seguridad y reduce la posibilidad de daños accidentales.  

### <a name="test"></a>Prueba 
DevTest Labs permite que una canalización de CI/CD automatice la creación de recursos de Azure (máquinas virtuales, entornos) que se pueden usar para realizar pruebas automatizadas y manuales. Las máquinas virtuales se crearían mediante artefactos o fórmulas que usan la información del proceso de compilación para crear las distintas configuraciones personalizadas necesarias para las pruebas.   

### <a name="release"></a>Release 
DevTest Labs se suele usar para la comprobación en la sección de versión antes de implementar el código. Es similar a la realización de pruebas en la sección de compilación. Los recursos de producción no se deben implementar dentro de DevTest Labs. 

### <a name="customization"></a>Personalización 
En Azure DevOps, hay tareas existentes que permiten la manipulación de máquinas virtuales y entornos en laboratorios específicos. Aunque Azure DevOps Services sea una manera de administrar la canalización de CI/CD, puede integrar el laboratorio en cualquier sistema compatible con la capacidad de llamar a las API REST, ejecutar scripts de PowerShell o usar la CLI de Azure. 

Aunque algunos administradores de la canalización de CI/CD tienen complementos de código abierto existentes que pueden administrar los recursos dentro de Azure y DevTest Labs, es posible que tenga que usar scripting personalizado para satisfacer las necesidades específicas de la canalización.  Con eso en mente, al ejecutar una tarea, se usa una entidad de servicio con el rol adecuado para obtener acceso al laboratorio. Normalmente, la entidad de servicio necesita acceso de rol de colaborador al laboratorio. Para más información, vea [Integración de Azure DevTest Labs en la canalización de entrega e integración continuas de Azure DevOps](devtest-lab-integrate-ci-cd.md). 
 