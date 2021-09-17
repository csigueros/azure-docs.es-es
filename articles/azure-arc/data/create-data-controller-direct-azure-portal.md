---
title: Implementación del controlador de datos de Azure Arc desde Azure Portal | Modo de conexión directa
description: Explica cómo implementar el controlador de datos en modo de conexión directa desde Azure Portal.
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 07/30/2021
ms.topic: overview
ms.openlocfilehash: 6ac96cb0420ecdba934b364c185bedf01f9f4321
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122822655"
---
#  <a name="create-azure-arc-data-controller-from-azure-portal---direct-connectivity-mode"></a>Creación de un controlador de datos de Azure Arc desde Azure Portal: modo de conexión directa

En este artículo se describe cómo implementar el controlador de datos de Azure Arc en modo de conexión directa durante la versión preliminar actual de esta característica. 

## <a name="complete-prerequisites"></a>Realización de los requisitos previos

Antes de empezar, compruebe que ha completado los requisitos previos que se describen en [Implementación del controlador de datos: modo de conexión directa (requisitos previos)](create-data-controller-direct-prerequisites.md).

## <a name="deploy-azure-arc-data-controller"></a>Implementación del controlador de datos de Azure Arc

El flujo de creación de un controlador de datos de Azure Arc se puede iniciar desde Azure Portal de una de las maneras siguientes:

- En la barra de búsqueda de Azure Portal, busque "Controladores de datos de Azure Arc" y seleccione "+ Crear".
- En la página Información general del clúster de Kubernetes habilitado para Azure Arc:
  - En Configuración, seleccione "Extensiones".
  - Seleccione "Agregar" en la página de información general de Extensiones y, a continuación, seleccione "Azure Arc data controller" (Controlador de datos de Azure Arc).
  - Seleccione Crear en la galería de marketplace de controladores de datos de Azure Arc.
  
Cualquiera de estas acciones debería llevarle a la página de requisitos previos del flujo de creación del controlador de datos de Azure Arc.

- Asegúrese de que la opción Arc-enabled Kubernetes cluster (Direct connectivity mode) [Clúster de Kubernetes habilitado para Azure Arc (modo de conexión directa)] está seleccionada. Seleccione "Next : Data controller details" (Siguiente: Detalles del controlador de datos).
- En la página **Data controller details** (Detalles del controlador de datos):
  - Seleccione la suscripción de Azure y el grupo de recursos donde se proyectará el controlador de datos de Azure Arc.
  - Escriba un **nombre** para el controlador de datos.
  - Seleccione una **Ubicación personalizada** creada previamente o seleccione "Crear nueva" para crear una nueva ubicación personalizada. Si decide crear una ubicación personalizada, escriba un nombre para la nueva ubicación personalizada, seleccione el clúster de Kubernetes habilitado para Azure Arc en la lista desplegable y, a continuación, escriba un espacio de nombres que se asociará a la nueva ubicación personalizada y, por último, seleccione Crear en la ventana Create new custom location (Crear ubicación personalizada). Más información sobre las [ubicaciones personalizadas](../kubernetes/conceptual-custom-locations.md)
  - **Configuración de Kubernetes**: seleccione la plantilla de configuración de Kubernetes que mejor se adapte a la distribución de Kubernetes en la lista desplegable. Si decide usar su propia configuración o tiene un perfil personalizado que desea usar, seleccione la opción Plantilla personalizada en la lista desplegable. En la hoja que se abre en el lado derecho, escriba los detalles para credenciales de Docker, información del repositorio, etiqueta de imagen, directiva de extracción de imágenes, tipo de infraestructura, configuración de almacenamiento para datos, registros y sus tamaños, tipo de servicio y puertos para el proxy de administración y controlador. Seleccione Aplicar cuando se proporcione toda la información necesaria. También puede elegir cargar su propio archivo de plantilla seleccionando "Upload a template (JSON)" [Cargar una plantilla (JSON)] en la parte superior de la hoja. Si usa la configuración personalizada y desea descargar una copia de esa configuración, use "Download this template (JSON)" [Descargar esta plantilla (JSON)] para hacerlo. Obtenga más información sobre los [perfiles de configuración personalizados](create-custom-configuration-template.md).
  - Seleccione el **Tipo de servicio** adecuado para su entorno.
  - **Cuenta de administrador**: escriba las credenciales para el inicio de sesión y la contraseña del controlador de datos.
  - **Entidad de servicio**: escriba el identificador de cliente, el identificador de inquilino y la información del secreto de cliente para la cuenta de entidad de servicio que se va a usar.
  - Seleccione el botón "Siguiente: Configuración adicional" para continuar después de proporcionar toda la información necesaria.
- En la página **Configuración adicional**:
  - Si decide cargar los registros en Azure Log Analytics automáticamente, escriba el identificador del área de trabajo de Log Analytics y la clave de acceso compartido de Log Analytics.
  - Si decide NO cargar los registros en Azure Log Analytics automáticamente, desactive la casilla "Enable logs upload" (Habilitar carga de registros).
  - Seleccione "Siguiente: Etiquetas" para continuar.
- En la página **Etiquetas**, escriba los nombres y valores de las etiquetas y seleccione "Siguiente: Revisar y crear".
- En la página **Revisar y crear**, vea el resumen de la implementación. Asegúrese de que toda la configuración sea correcta y seleccione "Crear" para empezar a implementar el controlador de datos de Azure Arc.

## <a name="monitor-the-creation-from-azure-portal"></a>Supervisión de la creación desde Azure Portal

Al seleccionar el botón "Crear" del paso anterior, se debe abrir la página de información general de la implementación de Azure, que muestra el progreso de la implementación del controlador de Azure Arc.

## <a name="monitor-the-creation-from-your-kubernetes-cluster"></a>Supervisión de la creación desde el clúster de Kubernetes

El progreso de implementación del controlador de datos de Azure Arc se puede supervisar de la siguiente manera:

- Compruebe si los CRD se han creado; para ello, ejecute ```kubectl get crd ``` desde el clúster.  
- Compruebe si el espacio de nombres se ha creado; para ello, ejecute ```kubectl get ns``` desde el clúster.
- Compruebe si la ubicación personalizada se ha creado; para ello, ejecute ```az customlocation list --resource-group <resourcegroup> -o table```. 
- Compruebe el estado de implementación de pods; para ello, ejecute ```kubectl get pods -ns <namespace>```.

## <a name="next-steps"></a>Pasos siguientes

[Creación de una Instancia administrada de Azure SQL en Azure Arc](create-sql-managed-instance.md)

[Creación de un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc desde Azure Portal](create-postgresql-hyperscale-server-group.md)
