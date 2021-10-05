---
title: Orquestación de la implementación
description: En este artículo se proporcionan instrucciones para orquestar la implementación de Azure DevTest Labs en su organización.
ms.topic: how-to
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 10ca9af9f68f6d95181418721ec90dac5dfd4ad4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601489"
---
# <a name="orchestrate-the-implementation-of-azure-devtest-labs"></a>Orquestación de la implementación de Azure DevTest Labs
En este artículo se proporciona un enfoque recomendado para una rápida implementación de Azure DevTest Labs. La siguiente imagen resalta el proceso general como orientación prescriptiva observando la flexibilidad para admitir diversos escenarios y requisitos del sector.

![Pasos para implementar Azure DevTest Labs](./media/devtest-lab-guidance-orchestrate-implementation/implementation-steps.png)

## <a name="assumptions"></a>Supuestos
En este artículo se supone que tiene los siguientes elementos antes de implementar un piloto de DevTest Labs:

- **Suscripción de Azure**: el equipo piloto tiene acceso a la implementación de recursos en una suscripción de Azure. Si las cargas de trabajo son solo de desarrollo y pruebas, se recomienda seleccionar la oferta Enterprise DevTest para otras imágenes disponibles y tarifas más bajas en máquinas virtuales Windows.
- **Acceso local**: por si es necesario, el acceso local ya se ha configurado. El acceso local puede realizarse a través de una conexión VPN de sitio a sitio o mediante ExpressRoute. La conectividad mediante ExpressRoute suele puede tardar muchas semanas en establecerse; se recomienda disponer de ExpressRoute antes de iniciar el proyecto.
- **Equipos piloto**: se identificaron los equipos de proyectos de desarrollo inicial que usan DevTest Labs junto con actividades de desarrollo o pruebas aplicables y se establecieron requisitos y objetivos para dichos equipos.

## <a name="milestone-1-establish-initial-network-topology-and-design"></a>Hito 1: Establecer el diseño y la topología de red inicial
Lo primero a lo que hay que prestar atención al implementar una solución de Azure DevTest Labs es establecer la conectividad planeada para las máquinas virtuales. En los siguientes pasos se describen los procedimientos necesarios:

1. Defina los **intervalos de direcciones IP iniciales** que se asignan a la suscripción de DevTest Labs en Azure. Este paso requiere prever el uso esperado del número de máquinas virtuales, para poder proporcionar un gran bloque que sea suficiente para una futura expansión.
2. Identifique los **métodos de acceso deseado** en DevTest Labs (por ejemplo, acceso interno o externo). Un punto clave en este paso es determinar si las máquinas virtuales tienen direcciones IP públicas (es decir, si se puede acceder a ellas directamente desde Internet).
3. Identifique y establezca **métodos de conectividad** con el resto del entorno local y en la nube de Azure. Si el enrutamiento forzado con ExpressRoute está habilitado, es probable que las máquinas virtuales necesiten configuraciones de proxy adecuadas para atravesar el firewall corporativo.
4. Si las máquinas virtuales se van a **unir a un dominio**, determine si se deben unir a un dominio basado en la nube (por ejemplo, Servicios de directorio de AAD) o a un dominio local. Si se trata de un dominio local, determine a qué unidad organizativa del directorio activo se van a unir las máquinas virtuales. Además, confirme que los usuarios tengan acceso para unirse, o bien establezca una cuenta de servicio que pueda crear registros de máquina en el dominio.

## <a name="milestone-2-deploy-the-pilot-lab"></a>Hito 2: Implementar el laboratorio piloto
Cuando ya exista la topología de red, se puede crear el primer laboratorio o un laboratorio piloto mediante los siguientes pasos:

1. Cree un entorno inicial de DevTest Labs.
2. Determine los tamaños y las imágenes de máquina virtual permitidos para su uso con el laboratorio. Decida si se pueden cargar imágenes personalizadas en Azure para su uso con DevTest Labs.
3. Proteja el acceso al laboratorio mediante la creación de controles de acceso basados en roles (Azure RBAC) iniciales para el laboratorio (propietarios del laboratorio y usuarios del laboratorio). Se recomienda utilizar cuentas de Active Directory sincronizadas con Azure Active Directory para la identidad con DevTest Labs.
4. Configure DevTest Labs para usar directivas, como programaciones, administración de costos, máquinas virtuales reclamables, imágenes personalizadas o fórmulas.
5. Establezca un repositorio en línea como Azure Repos/Git.
6. Decida sobre el uso de repositorios públicos o privados o una combinación de ambos. Organice las plantillas JSON para las implementaciones y un sostenimiento a largo plazo.
7. Si es necesario, cree artefactos personalizados. Este paso es opcional. 

## <a name="milestone-3-documentation-support-learn-and-improve"></a>Hito 3: Documentación, asistencia, aprendizaje y mejoras
Los equipos pilotos iniciales pueden requerir asistencia exhaustiva para empezar. Use sus experiencias para asegurarse de que cuenta con la documentación y la asistencia apropiadas para la implementación continua de Azure DevTest Labs.

1. Presente a los equipos pilotos sus nuevos recursos de DevTest Labs (demostraciones y documentación).
2. Según las experiencias de los equipos pilotos, planee y proporcione documentación según sea necesario.
3. Formalice el proceso de incorporación de equipos nuevos (creación y configuración de laboratorios, proporcionar acceso, etc).
4. Según la aceptación inicial, verifique si la previsión original de espacio de direcciones IP todavía es razonable y precisa.
5. Asegúrese de que se han llevado a cabo las revisiones de cumplimiento y seguridad apropiadas.

## <a name="next-steps"></a>Pasos siguientes
Lea el siguiente artículo de esta serie: [Gobernanza de la infraestructura de Azure DevTest Labs](devtest-lab-guidance-governance-resources.md)
