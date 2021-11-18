---
title: Integración de Azure DevTest Labs y DevOps
description: Obtenga información sobre cómo usar los laboratorios dentro de canalizaciones de integración continua (CI) o entrega continua (CD) en un entorno empresarial.
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: 59c259a27fa7e171a6e6aa6bd7bc2ffa25fa89fd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286343"
---
# <a name="integrate-azure-devtest-labs-and-azure-devops"></a>Integración de Azure DevTest Labs y Azure DevOps
DevOps es una metodología de desarrollo de software que integra el desarrollo de software (Dev) con las operaciones (Ops) en un sistema. Este sistema puede ofrecer nuevas características, actualizaciones y correcciones acordes a los objetivos empresariales. Esta metodología abarca todo, desde el diseño de nuevas características en función de objetivos, patrones de uso y comentarios de los clientes, hasta la corrección, recuperación y protección del sistema cuando se producen problemas. Un componente de esta metodología fácil de identificar es la canalización de integración continua (CI) y entrega continua (CD). Una canalización de CI/CD toma información, código y recursos de una confirmación a través de una serie de pasos para generar el sistema. Los pasos incluyen la creación, las pruebas y la implementación. Este artículo se centra en distintas maneras de usar eficazmente los laboratorios en una canalización en un entorno empresarial. 

## <a name="benefits-of-using-labs-in-devops-workflow"></a>Ventajas del uso de laboratorios de flujo de trabajo de DevOps 

### <a name="focused-access"></a>Acceso centrado 
El uso de un laboratorio como un componente permite asociar un ecosistema específico a un grupo limitado de usuarios. Normalmente, a un equipo o grupo que trabaja en un área común o una característica específica se le asigna un laboratorio.   

### <a name="infrastructure-replication-in-the-cloud"></a>Replicación de la infraestructura en la nube 
Un desarrollador puede configurar rápidamente un ecosistema de desarrollo que incluya un marco de desarrollo con código fuente y herramientas. El desarrollador también puede crear un entorno prácticamente idéntico a la configuración de producción. Este entorno facilita el desarrollo de bucles internos más rápido. 

### <a name="pre-production"></a>Preproducción 
Tener un laboratorio en la canalización de CI/CD facilita la presencia de varios entornos o equipos de preproducción diferentes que se ejecutan al mismo tiempo para realizar pruebas asincrónicas. En un laboratorio se pueden implementar y administrar otras infraestructuras de soporte, como los agentes de compilación. 

## <a name="devops-with-devtest-labs"></a>DevOps con DevTest Labs 

### <a name="development--operation"></a>Desarrollo y operación 
Un laboratorio se debe centrar en un equipo que trabaje en un área de características. Este enfoque común admite el uso compartido de recursos específicos del área, como herramientas, scripts o plantillas de Resource Manager. El enfoque común permite cambios más rápidos al tiempo que limita los efectos negativos a un grupo más reducido. Los recursos compartidos permiten al desarrollador crear máquinas virtuales de desarrollo con todo el código, herramientas y configuración necesarios. Los recursos se pueden crear dinámicamente o en un sistema que crea imágenes base con personalizaciones. El desarrollador puede crear máquinas virtuales y también entornos de DevTest Labs según las plantillas necesarias para crear los recursos de Azure adecuados en el laboratorio. Los cambios o el trabajo destructivo se pueden realizar en el entorno de laboratorio sin que afecten a nadie más. Considere el escenario donde el producto es un sistema independiente instalado en el equipo del cliente. En este escenario, DevTest Labs ha mejorado la creación de máquinas virtuales que incluye la instalación de software mediante artefactos y la compilación previa de configuraciones de cliente para realizar pruebas más rápidas del bucle interno del código. 
  
## <a name="cicd-pipeline"></a>Canalización de integración y entrega continuas 
La canalización de CI/CD es uno de los componentes críticos de DevOps. La canalización traslada el código de la solicitud de incorporación de cambios de un desarrollador, lo integra con el código existente y lo implementa en el ecosistema de producción. No es necesario que todos los recursos estén dentro de un laboratorio. Por ejemplo, se podría configurar un host Jenkins fuera de laboratorio como un recurso más persistente. Estos son algunos ejemplos específicos de la integración de laboratorios en la canalización. 

### <a name="build"></a>Compilar 
La canalización de compilación se centra en crear un paquete de componentes que se prueban de forma conjunta para su entrega a la canalización de versión. Los laboratorios pueden formar parte de la canalización de compilación como ubicación para los agentes de compilación y otros recursos de soporte técnico. La capacidad de crear la infraestructura de forma dinámica permite tener un mayor control. Con la capacidad de tener varios entornos en un laboratorio, cada compilación se puede ejecutar de forma asincrónica. El identificador de compilación forma parte de la información del entorno que identifica de forma única los recursos de la compilación específica.   

Para los agentes de compilación, la capacidad del laboratorio de restringir el acceso aumenta la seguridad y reduce la posibilidad de daños accidentales.  

### <a name="test"></a>Prueba 
DevTest Labs permite que una canalización de CI/CD automatice la creación de recursos de Azure como máquinas virtuales y entornos, y usarlos para realizar pruebas automatizadas y manuales. Las máquinas virtuales se crean mediante artefactos o fórmulas que usan la información del proceso de compilación para crear las distintas configuraciones personalizadas necesarias para las pruebas.   

### <a name="release"></a>Release 
DevTest Labs se suele usar para la comprobación en la sección de versión antes de implementar el código. El proceso es similar a la realización de pruebas en la sección de compilación. Los recursos de producción no se deben implementar dentro de DevTest Labs. 

### <a name="customization"></a>Personalización 
En Azure DevOps, hay tareas existentes que permiten la manipulación de máquinas virtuales y entornos en laboratorios específicos. Azure DevOps Services es una manera de administrar la canalización de CI/CD. Puede integrar el laboratorio en cualquier sistema que admita la capacidad de llamar a las API de REST, ejecutar scripts de PowerShell o usar la CLI de Azure. 

Algunos administradores de canalización de CI/CD tienen complementos de código abierto existentes que pueden administrar los recursos dentro de Azure y DevTest Labs. Es posible que tenga que usar algún scripting personalizado para ajustarse a las necesidades específicas de la canalización.  Con eso en mente, al ejecutar una tarea, se usa una entidad de servicio con el rol adecuado para acceder al laboratorio. Normalmente, la entidad de servicio necesita acceso de rol de colaborador al laboratorio. Para más información, consulte [Integración de Azure DevTest Labs en la canalización de entrega e integración continuas de Azure DevOps](devtest-lab-integrate-ci-cd.md). 
 