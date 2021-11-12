---
title: Paneles de Azure Data Studio
description: Paneles de Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: cea97bab303ce2d009cecc67ed30ec89b0992118
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131554343"
---
# <a name="azure-data-studio-dashboards"></a>Paneles de Azure Data Studio

[Azure Data Studio](/sql/azure-data-studio/what-is) proporciona una experiencia similar a la de Azure Portal para ver información sobre los recursos de Azure Arc.  Estas vistas se denominan **paneles** y tienen un diseño y unas opciones similares a lo que se puede ver sobre un recurso determinado en el Azure Portal, pero le ofrecen la flexibilidad de ver esa información localmente en su entorno en los casos en los que no tiene una conexión disponible para Azure.


## <a name="connecting-to-a-data-controller"></a>Conexión a un controlador de datos

### <a name="prerequisites"></a>Requisitos previos

- Descargar [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)
- Extensión de Azure Arc instalada



### <a name="connect"></a>Conectar

1. Procedimiento para abrir Azure Data Studio
2. Seleccione la pestaña **Conexiones** a la izquierda.
3. Expanda el panel llamado **Azure Arc Controllers** (Controladores de Azure Arc).
4. Haga clic en el botón **Connect Controller** (Conectar controlar). Se abrirá una hoja en el lado derecho.
5. De forma predeterminada, Azure Data Studio intentará leer el archivo kube.config en el directorio predeterminado, enumerar los contextos de clúster de Kubernetes disponibles y seleccionar previamente el contexto de clúster actual. Si es el clúster adecuado para conectarse, escriba el espacio de nombres donde está implementado el controlador de datos de Azure Arc en la entrada de **Espacio de nombres**. Si necesita recuperar el espacio de nombres donde está implementado el controlador de datos de Azure Arc, puede ejecutar ```kubectl get datacontrollers -A``` en el clúster de Kubernetes. 
6. Opcionalmente, agregue un nombre para mostrar para el controlador de datos de Azure Arc en la entrada de **Nombre**.
7. Seleccione **Conectar**.


Ahora que está conectado a un controlador de datos, puede ver los paneles del controlador de datos y las instancias administradas de SQL o los recursos del grupo de servidores de hiperescala de PostgreSQL que tenga.

## <a name="view-the-data-controller-dashboard"></a>Vista del panel del controlador de datos

Haga clic con el botón derecho en el controlador de datos del panel de conexiones del panel extensible **Controladores de Arc** y seleccione **Administrar**.

Aquí puede ver los detalles sobre el recurso del controlador de datos como el nombre, la región, el modo de conexión, el grupo de recursos, la suscripción, el punto de conexión del controlador y el espacio de nombres.  También puede ver una lista de todos los recursos de bases de datos administrados por el controlador de datos.

Observará que el diseño es similar al que puede ver en Azure Portal.

Puede iniciar la creación de una instancia administrada de SQL o un grupo de servidores de hiperescala de PostgreSQL fácilmente si hace clic en el botón + Nueva instancia.

También puede abrir el Azure Portal en el contexto de este controlador de datos si hace clic en el botón Abrir en Azure Portal.

## <a name="view-the-sql-managed-instance-dashboards"></a>Vista de los paneles de una instancia administrada de SQL

Si ha creado algunas instancias administradas de SQL, puede verlas en el panel Conexiones, en el panel extensible Controladores de datos de Azure, en el controlador de datos que las administra.

Para ver el panel de instancias administradas de SQL de una instancia determinada, haga clic con el botón derecho en la instancia y elija Administrar.

El panel Conexión aparecerá a la derecha y le pedirá el nombre de inicio de sesión y la contraseña para conectarse a esa instancia de SQL. Si conoce la información de conexión, puede escribirla y hacer clic en Conectar.  Si no la sabe, puede hacer clic en Cancelar.  En cualquier caso, se le dirigirá al panel cuando se cierre el panel Conexión.

En la pestaña Información general, puede ver detalles sobre la instancia administrada de SQL, como grupo de recursos, controlador de datos, identificador de suscripción, estado, región, etc.  También puede ver un vínculo en el que puede hacer clic para ir a los paneles Grafana o Kibana en el contexto de esa instancia administrada de SQL.

Si puede conectarse a la instancia administrada de SQL, puede ver más información aquí.

Puede eliminar la instancia administrada de SQL desde aquí o abrir Azure Portal para verla ahí.

Si hace clic en la pestaña Cadenas de conexión de la izquierda, puede ver una lista de cadenas de conexión construidas previamente para la instancia administrada de SQL, lo que facilita las acciones de copiar y pegar en otras aplicaciones o códigos.

## <a name="view-the-postgresql-hyperscale-server-group-dashboards"></a>Vista de los paneles del grupo de servidores de hiperescala de PostgreSQL

Si ha creado algunos grupos de servidores Hiperescala de PostgreSQL, puede verlos en el panel Conexiones, en el panel extensible Controladores de datos de Azure, en el controlador de datos que los administra.

Para ver el panel del grupo de servidores de hiperescala de PostgreSQL de un grupo de servidores determinado, haga clic con el botón derecho en el grupo de servidores y elija Administrar.

En la pestaña Información general, puede ver detalles sobre el grupo de servidores, como el grupo de recursos, el controlador de datos, el identificador de suscripción, el estado, la región, etc.  También puede ver un vínculo en el que puede hacer clic para ir a los paneles Grafana o Kibana en el contexto de ese grupo de servidores.

Puede eliminar el grupo de servidores desde aquí o abrir el Azure Portal para ver el grupo de servidores en el Azure Portal.

Si hace clic en la pestaña Cadenas de conexión de la izquierda, puede ver una lista de cadenas de conexión construidas previamente para ese grupo de servidores, lo que facilita las acciones de copiar y pegar en otras aplicaciones o códigos.

Si hace clic en la pestaña Propiedades de la izquierda, puede ver más detalles.

Si hace clic en la pestaña Estado de los recursos de la izquierda, puede ver el estado actual de alto nivel de ese grupo de servidores.

Si hace clic en la pestaña Diagnosticar y solucionar problemas a la izquierda, puede iniciar el cuaderno de solución de problemas de PostgreSQL.

Si hace clic en la pestaña Nueva solicitud de soporte técnico de la izquierda, puede iniciar el Azure Portal en el contexto del grupo de servidores y crear una solicitud de soporte técnico de Azure desde allí.