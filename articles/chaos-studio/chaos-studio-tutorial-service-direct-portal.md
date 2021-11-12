---
title: Creación de un experimento que usa un error directo del servicio con Azure Chaos Studio
description: Creación de un experimento que usa un error directo del servicio
author: johnkemnetz
ms.author: johnkem
ms.service: chaos-studio
ms.topic: how-to
ms.date: 11/01/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: fd945f5a96228bc30c7d1f801fcc16ca34717cde
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373448"
---
# <a name="create-a-chaos-experiment-that-uses-a-service-direct-fault-to-fail-over-an-azure-cosmos-db-instance"></a>Creación de un experimento de caos que usa un error directo del servicio para conmutar por error una instancia de Azure Cosmos DB

Puede usar un experimento de caos para comprobar que la aplicación es resistente a los errores provocando esos errores en un entorno controlado. En esta guía, provocará una conmutación por error de Azure Comos DB de varias lecturas y una sola escritura mediante un experimento de caos y Azure Chaos Studio. La ejecución de este experimento puede ayudarle a defenderse contra la pérdida de datos cuando se produce un evento de conmutación por error.

Estos mismos pasos se pueden usar para configurar y ejecutar un experimento para cualquier error directo del servicio. Un error **directo del servicio** se ejecuta directamente en un recurso de Azure sin necesidad de instrumentación, a diferencia de los errores basados en agente, que requieren la instalación del agente de caos.

## <a name="prerequisites"></a>Prerrequisitos

- Suscripción a Azure. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- Una cuenta de Azure Cosmos DB. Si no tiene una cuenta de Azure Cosmos DB, puede [seguir estos pasos para crear una](../cosmos-db/sql/create-cosmosdb-resources-portal.md).
- Al menos una configuración de región de lectura y escritura para la cuenta de Azure Cosmos DB.


## <a name="enable-chaos-studio-on-your-azure-cosmos-db-account"></a>Habilitación de Chaos Studio en la cuenta de Azure Cosmos DB

Chaos Studio no puede insertar errores en un recurso a menos que ese recurso se haya incorporado primero a Chaos Studio. Incorpore un recurso a Chaos Studio mediante la creación de un [destino y funcionalidades](chaos-studio-targets-capabilities.md) en el recurso. Las cuentas de Azure Cosmos DB solo tienen un tipo de destino (servicio directo) y una funcionalidad (conmutación por error), pero otros recursos pueden tener hasta dos tipos de destino (uno para los errores de servicio directo y otro para los errores basados en el agente) y muchas funcionalidades.

1. Abra [Azure Portal](https://portal.azure.com).
2. Busque **Chaos Studio (versión preliminar)** en la barra de búsqueda.
3. Haga clic en **Destinos** y vaya hasta la cuenta de Azure Cosmos DB.
![Visualización de los destinos en Azure Portal](images/tutorial-service-direct-targets.png)
4. Active la casilla situada junto a la cuenta de Azure Cosmos DB y haga clic en **Habilitar destinos** y, después, en **Habilitar destinos de servicio directo** en el menú desplegable.
![Habilitación de destinos en Azure Portal](images/tutorial-service-direct-targets-enable.png)
5. Aparecerá una notificación que indica que los recursos seleccionados se habilitaron correctamente.
![Notificación que muestra el destino habilitado correctamente](images/tutorial-service-direct-targets-enable-confirm.png)

Ya ha incorporado correctamente la cuenta de Azure Cosmos DB a Chaos Studio. En la vista **Destinos** también puede administrar las funcionalidades habilitadas en este recurso. Al hacer clic en el vínculo **Administrar acciones** junto a un recurso, se mostrarán las funcionalidades habilitadas para ese recurso.

## <a name="create-an-experiment"></a>Creación de un experimento
Con la cuenta de Azure Cosmos DB ahora incorporada, puede crear el experimento. Un experimento de caos define las acciones que desea realizar en los recursos de destino, organizados en pasos, que se ejecutan secuencialmente, y ramas, que se ejecutan en paralelo.

1. Haga clic en la pestaña **Experimentos** en el panel de navegación de Chaos Studio. En esta vista, puede ver y administrar todos los experimentos de caos. Haga clic en la vista **Agregar un experimento**
![ Experimentos en Azure Portal](images/tutorial-service-direct-add.png)
2. Rellene la **Suscripción**,el **Grupo de recursos** y la **Ubicación** donde desea implementar el experimento de caos. Ponga un **Nombre** al experimento. Haga clic en **Siguiente: Diseñador de experimentos >** 
![Agregar los detalles básicos del experimento](images/tutorial-service-direct-add-basics.png)
3. Ahora está en el diseñador de experimentos de Chaos Studio. El diseñador de experimentos permite compilar el experimento mediante la adición de pasos, ramas y errores. Asigne un nombre descriptivo a su **Paso** y **Rama** y,a continuación, haga clic en **Agregar error**.
![Diseñador de experimentos](images/tutorial-service-direct-add-designer.png)
4. Seleccione **Conmutación por error de CosmosDB** en la lista desplegable y rellene la **Duración** con el número de minutos que desea que el error dure y **readRegion** con la región de lectura de la cuenta de Azure Cosmos DB. Haga clic en **Siguiente: Recursos de destino >** 
![Propiedades del error](images/tutorial-service-direct-add-fault.png)
5. Seleccione la cuenta de Azure Cosmos DB y haga clic en **Siguiente**
![Agregar un destino](images/tutorial-service-direct-add-target.png)
6. Compruebe que el experimento tiene el aspecto correcto y, a continuación, haga clic en **Revisar y crear** y, después, en **Crear.** 
![Revisar y crear el experimento](images/tutorial-service-direct-add-review.png)

## <a name="give-experiment-permission-to-your-azure-cosmos-db-account"></a>Conceder permiso de experimento a la cuenta de Azure Cosmos DB
Al crear un experimento de caos, Chaos Studio crea una identidad administrada asignada por el sistema que ejecuta errores en los recursos de destino. Esta identidad debe tener los [permisos adecuados](chaos-studio-fault-providers.md) en el recurso de destino para que el experimento se ejecute correctamente.

1. Vaya a la cuenta de Azure Cosmos DB y haga clic en **Control de acceso (IAM)** .
![Página de información general sobre Azure Cosmos DB](images/tutorial-service-direct-access-resource.png)
2. Haga clic en **Agregar** y, a continuación, en **Agregar asignación de roles**.
![Introducción al control de acceso](images/tutorial-service-direct-access-iam.png)
3. Busque el **operador de Cosmos DB** y seleccione el rol. Haga clic en **Siguiente**
![Asignación del rol de operador de Azure Cosmos DB](images/tutorial-service-direct-access-role.png)
4. Haga clic en **Seleccionar miembros** y busque el nombre del experimento. Seleccione el experimento y haga clic en **Seleccionar**. Si hay varios experimentos en el mismo inquilino con el mismo nombre, el nombre del experimento se truncará con caracteres aleatorios agregados.
![Adición de un experimento al rol](images/tutorial-service-direct-access-experiment.png)
5. Haga clic en **Revisar y asignar** y, a continuación, en **Revisar y asignar**.

## <a name="run-your-experiment"></a>Ejecutar el experimento
Ahora ya puede ejecutar el experimento. Para ver el impacto, se recomienda abrir la información general de la cuenta de Azure Cosmos DB y ir a **Replicar datos globalmente** en una pestaña independiente del explorador. La actualización periódica durante el experimento mostrará el intercambio de regiones.

1. En la vista **Experimentos,** haga clic en el experimento y, a continuación, haga clic en **Iniciar** y, a continuación, haga clic en **Aceptar**.
2. Cuando el **estado** cambie a **En ejecución**,haga clic en **Detalles** de la última ejecución en **Historial** para ver los detalles del experimento en ejecución.

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha ejecutado un experimento directo del servicio Azure Cosmos DB, está listo para:
- [Crear un experimento que usa errores basados en agente](chaos-studio-tutorial-agent-based-portal.md)
- [Administrar el experimento](chaos-studio-run-experiment.md)
