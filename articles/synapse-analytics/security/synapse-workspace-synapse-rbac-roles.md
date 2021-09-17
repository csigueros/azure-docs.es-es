---
title: Roles de Synapse RBAC
description: En este artículo se describen los roles de Synapse RBAC integrados
author: meenalsri
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: mesrivas
ms.reviewer: jrasnick
ms.openlocfilehash: cc516b917d56d6b41f83e0c81354453dafc41f42
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122822927"
---
# <a name="synapse-rbac-roles"></a>Roles de Synapse RBAC

En este artículo se describen los roles de Synapse RBAC integrados, los permisos que conceden y los ámbitos en los que se pueden usar.  

## <a name="whats-changed-since-the-preview"></a>¿Qué ha cambiado desde la versión preliminar?
Para los usuarios que estén familiarizados con los roles de Synapse RBAC proporcionados durante la versión preliminar, se aplican los siguientes cambios:
- Se cambió el nombre del administrador del área de trabajo a **Administrador de Synapse**
- Se cambió el nombre del administrador de Apache Spark a **Administrador de Synapse Apache Spark** y ahora tiene permiso para ver todos los artefactos de código publicados, incluidos los scripts de SQL.  Este rol ya no concede permiso para usar el MSI del área de trabajo, que requiere el rol de usuario de credenciales de Synapse.  Este permiso es necesario para ejecutar canalizaciones. 
- Se cambió el nombre del administrador de SQL a **Administrador de Synapse SQL** y ahora tiene permiso para ver todos los artefactos de código publicados, incluidos los cuadernos y trabajos de Spark.  Este rol ya no concede permiso para usar el MSI del área de trabajo, que requiere el rol de usuario de credenciales de Synapse. Este permiso es necesario para ejecutar canalizaciones.
- Se han incorporado **nuevos roles de Synapse RBAC más precisos** que se centran en la compatibilidad de los roles de desarrollo y de operaciones en lugar de los entornos de ejecución de análisis específicos.  
- Se han incorporado **nuevos ámbitos de nivel inferior** para varios roles.  Estos ámbitos permiten que los roles se limiten a recursos u objetos específicos.

>[!Note]
>Los **nuevos roles de Synapse RBAC y los ámbitos de nivel inferior están actualmente en versión preliminar**.  Se recomienda usar estos nuevos roles y ámbitos, que son totalmente compatibles y proporcionar comentarios sobre su uso.

## <a name="built-in-synapse-rbac-roles-and-scopes"></a>Roles y ámbitos integrados de Synapse RBAC

En la tabla siguiente se describen los roles integrados y los ámbitos en los que se pueden usar.

>[!Note]
> Los usuarios con cualquier rol de Synapse RBAC en cualquier ámbito adquieren automáticamente el rol de usuario de Synapse en el ámbito del área de trabajo. 

|Role |Permisos|Ámbitos|
|---|---|-----|
|Administrador de Synapse  |Acceso total a grupos de SQL sin servidor, grupos de Apache Spark y entornos de ejecución de integración en Synapse.  Incluye acceso de creación, lectura, actualización y eliminación a todos los artefactos de código publicados.  Incluye el operador de proceso, el administrador de datos vinculados y los permisos de usuario de credenciales en la credencial de identidad del sistema del área de trabajo.  Incluye la asignación de roles de Synapse RBAC. Además del administrador de SYNAPSE, los propietarios de Azure también pueden asignar roles RBAC de Synapse. Los permisos de Azure son necesarios para crear, eliminar y administrar recursos de proceso. </br></br>_Puede leer y escribir artefactos</br> Puede realizar todas las acciones en actividades de Spark.</br> Puede ver los registros de los grupos de Spark</br> Puede ver la salida de la canalización y el cuaderno guardado </br> Puede usar los secretos almacenados por los servicios vinculados o las credenciales</br>Puede asignar y revocar roles RBAC de Synapse en el ámbito actual_|Área de trabajo </br> Grupo de Spark<br/>Tiempo de ejecución de integración </br>Servicio vinculado</br>Credential: |
|Administrador de Synapse Apache Spark</br>|Acceso total a grupos de Apache Spark en Synapse.  Acceso de creación, lectura, actualización y eliminación a las definiciones de trabajos de Spark publicadas, los cuadernos y sus salidas, y a las bibliotecas, los servicios vinculados y las credenciales.  Incluye acceso de lectura a todos los demás artefactos de código publicados. No incluye permisos para usar las credenciales y ejecutar canalizaciones. No incluye el acceso de concesión. </br></br>_Puede realizar todas las acciones en los artefactos de Spark</br>Puede realizar todas las acciones en las actividades de Spark_|Área de trabajo</br>Grupo de Spark|
|Administrador de Synapse SQL|Acceso total a grupos de SQL sin servidor en Synapse.  Acceso de creación, lectura, actualización y eliminación a los scripts de SQL publicados, las credenciales y los servicios vinculados.  Incluye acceso de lectura a todos los demás artefactos de código publicados.  No incluye permisos para usar las credenciales y ejecutar canalizaciones. No incluye el acceso de concesión. </br></br>*Puede realizar todas las acciones en scripts de SQL<br/>Puede conectarse a puntos de conexión de SQL sin servidor con permisos `db_datareader`, `db_datawriter`, `connect` y `grant` de SQL*|Área de trabajo|
|Colaborador de Synapse|Acceso total a grupos de Apache Spark y entornos de ejecución de integración en Synapse. Incluye acceso de creación, lectura, actualización y eliminación a todos los artefactos de código publicados y sus salidas, incluidas las credenciales y los servicios vinculados.  Incluye permisos de operador de proceso. No incluye permisos para usar las credenciales y ejecutar canalizaciones. No incluye el acceso de concesión. </br></br>_Puede leer y escribir artefactos</br>Puede ver la salida de la canalización y el cuaderno guardado</br>Puede realizar todas las acciones en las actividades de Spark</br>Pueden ver los registros de los grupos de Spark_|Área de trabajo </br> Grupo de Spark<br/> Tiempo de ejecución de integración|
|Editor de artefactos de Synapse|Acceso de creación, lectura, actualización y eliminación a los artefactos de código publicados y sus salidas. No incluye permisos para ejecutar código o canalizaciones ni para conceder acceso. </br></br>_Puede leer artefactos publicados y publicar artefactos</br>Puede ver los cuadernos guardados, los trabajos de Spark y las salidas de las canalizaciones_|Área de trabajo
|Usuario de artefactos de Synapse|Acceso de lectura a los artefactos de código publicados y sus salidas. Puede crear nuevos artefactos, pero no puede publicar cambios ni ejecutar código sin permisos adicionales.|Área de trabajo
|Operador de proceso de Synapse |Envíe trabajos y cuadernos de Spark y vea los registros.  Incluye la cancelación de trabajos de Spark enviados por cualquier usuario. Requiere permisos de credenciales de uso adicionales en la identidad del sistema del área de trabajo para ejecutar canalizaciones, ver ejecuciones y salidas de canalización. </br></br>_Puede enviar y cancelar trabajos, incluidos los trabajos enviados por otros</br>Puede ver los registros de los grupos de Spark_|Área de trabajo</br>Grupo de Spark</br>Tiempo de ejecución de integración|
|Usuario de credenciales Synapse|Uso de secretos en el entorno de ejecución y al momento de la configuración en las credenciales y los servicios vinculados en actividades como la ejecución de canalizaciones. Para ejecutar canalizaciones, se requiere este rol, con el ámbito de la identidad del sistema del área de trabajo. </br></br>_En el ámbito de una credencial, permite el acceso a los datos a través de un servicio vinculado protegido por la credencial (también requiere el permiso de uso de proceso) </br>Permite la ejecución de canalizaciones protegidas por la credencial de identidad del sistema del área de trabajo (con el permiso de uso de proceso adicional)_|Área de trabajo </br>Servicios vinculados</br>Credential:
|Administrador de datos vinculado a Synapse|Creación y administración de puntos de conexión privados administrados, servicios vinculados y credenciales. Puede crear puntos de conexión privados administrados que usan servicios vinculados protegidos por credenciales|Área de trabajo|
|Usuario de Synapse|Enumerar y ver los detalles de los grupos de SQL, los grupos de Apache Spark, los entornos de ejecución de integración y las credenciales y los servicios vinculados publicados. No incluye otros artefactos de código publicados.  Puede crear nuevos artefactos, pero no puede publicar ni ejecutar sin permisos adicionales.</br></br>_Puede enumerar y leer grupos de Spark, entornos de ejecución de integración._|Área de trabajo, grupo de Spark</br>Servicio vinculado </br>Credential:|

## <a name="synapse-rbac-roles-and-the-actions-they-permit"></a>Roles de Synapse RBAC y las acciones que permiten

>[!Note]
>- Todas las acciones que figuran en las tablas siguientes tienen el prefijo "Microsoft.Synapse/..."</br>
>- Todas las acciones de lectura, escritura y eliminación de artefactos se realizan con respecto a los artefactos publicados en el servicio activo.  Estos permisos no afectan el acceso a los artefactos en un repositorio de Git conectado.  

En la tabla siguiente se enumeran los roles integrados, así como las acciones y permisos que admite cada uno de ellos.

Role|Acciones
--|--
Administrador de Synapse|workspaces/read</br>workspaces/roleAssignments/write, delete</br>workspaces/managedPrivateEndpoint/write, delete</br>workspaces/bigDataPools/useCompute/action</br>workspaces/bigDataPools/viewLogs/action</br>workspaces/integrationRuntimes/useCompute/action</br>workspaces/artifacts/read</br>workspaces/notebooks/write, delete</br>workspaces/sparkJobDefinitions/write, delete</br>workspaces/sqlScripts/write, delete</br>workspaces/dataFlows/write, delete</br>workspaces/pipelines/write, delete</br>workspaces/triggers/write, delete</br>workspaces/datasets/write, delete</br>workspaces/libraries/write, delete</br>workspaces/linkedServices/write, delete</br>workspaces/credentials/write, delete</br>workspaces/notebooks/viewOutputs/action</br>workspaces/pipelines/viewOutputs/action</br>workspaces/linkedServices/useSecret/action</br>workspaces/credentials/useSecret/action|
|Administrador de Synapse Apache Spark|workspaces/read</br>workspaces/bigDataPools/useCompute/action</br>workspaces/bigDataPools/viewLogs/action</br>workspaces/notebooks/viewOutputs/action</br>workspaces/artifacts/read</br>workspaces/notebooks/write, delete</br>workspaces/sparkJobDefinitions/write, delete</br>workspaces/libraries/write, delete</br>workspaces/linkedServices/write, delete</br>workspaces/credentials/write, delete|
|Administrador de Synapse SQL|workspaces/read</br>workspaces/artifacts/read</br>workspaces/sqlScripts/write, delete</br>workspaces/linkedServices/write, delete</br>workspaces/credentials/write, delete|
|Colaborador de Synapse|workspaces/read</br>workspaces/bigDataPools/useCompute/action</br>workspaces/bigDataPools/viewLogs/action</br>workspaces/integrationRuntimes/useCompute/action</br>workspaces/integrationRuntimes/viewLogs/action</br>workspaces/artifacts/read</br>workspaces/notebooks/write, delete</br>workspaces/sparkJobDefinitions/write, delete</br>workspaces/sqlScripts/write, delete</br>workspaces/dataFlows/write, delete</br>workspaces/pipelines/write, delete</br>workspaces/triggers/write, delete</br>workspaces/datasets/write, delete</br>workspaces/libraries/write, delete</br>workspaces/linkedServices/write, delete</br>workspaces/credentials/write, delete</br>workspaces/notebooks/viewOutputs/action</br>workspaces/pipelines/viewOutputs/action|
|Editor de artefactos de Synapse|workspaces/read</br>workspaces/artifacts/read</br>workspaces/notebooks/write, delete</br>workspaces/sparkJobDefinitions/write, delete</br>workspaces/sqlScripts/write, delete</br>workspaces/dataFlows/write, delete</br>workspaces/pipelines/write, delete</br>workspaces/triggers/write, delete</br>workspaces/datasets/write, delete</br>workspaces/libraries/write, delete</br>workspaces/linkedServices/write, delete</br>workspaces/credentials/write, delete</br>workspaces/notebooks/viewOutputs/action</br>workspaces/pipelines/viewOutputs/action|
|Usuario de artefactos de Synapse|workspaces/read</br>workspaces/artifacts/read</br>workspaces/notebooks/viewOutputs/action</br>workspaces/pipelines/viewOutputs/action|
|Operador de proceso de Synapse |workspaces/read</br>workspaces/bigDataPools/useCompute/action</br>workspaces/bigDataPools/viewLogs/action</br>workspaces/integrationRuntimes/useCompute/action</br>workspaces/integrationRuntimes/viewLogs/action|
|Usuario de credenciales Synapse|workspaces/read</br>workspaces/linkedServices/useSecret/action</br>workspaces/credentials/useSecret/action|
|Administrador de datos vinculado a Synapse|workspaces/read</br>workspaces/managedPrivateEndpoint/write, delete</br>workspaces/linkedServices/write, delete</br>workspaces/credentials/write, delete|
|Usuario de Synapse|workspaces/read|

## <a name="synapse-rbac-actions-and-the-roles-that-permit-them"></a>Acciones de Synapse RBAC y los roles que las permiten

En la tabla siguiente se enumeran las acciones de Synapse y los roles integrados que permiten realizar estas acciones:

Acción|Role
--|--
workspaces/read|Administrador de Synapse</br>Administrador de Synapse Apache Spark</br>Administrador de Synapse SQL</br>Colaborador de Synapse</br>Editor de artefactos de Synapse</br>Usuario de artefactos de Synapse</br>Operador de proceso de Synapse </br>Usuario de credenciales Synapse</br>Administrador de datos vinculado a Synapse</br>Usuario de Synapse 
workspaces/roleAssignments/write, delete|Administrador de Synapse
workspaces/managedPrivateEndpoint/write, delete|Administrador de Synapse</br>Administrador de datos vinculado a Synapse
workspaces/bigDataPools/useCompute/action|Administrador de Synapse</br>Administrador de Synapse Apache Spark</br>Colaborador de Synapse</br>Operador de proceso de Synapse 
workspaces/bigDataPools/viewLogs/action|Administrador de Synapse</br>Administrador de Synapse Apache Spark</br>Colaborador de Synapse</br>Operador de proceso de Synapse 
workspaces/integrationRuntimes/useCompute/action|Administrador de Synapse</br>Colaborador de Synapse</br>Operador de proceso de Synapse 
workspaces/artifacts/read|Administrador de Synapse</br>Administrador de Synapse Apache Spark</br>Administrador de Synapse SQL</br>Colaborador de Synapse</br>Editor de artefactos de Synapse</br>Usuario de artefactos de Synapse
workspaces/notebooks/write, delete|Administrador de Synapse</br>Administrador de Synapse Apache Spark</br>Colaborador de Synapse</br>Editor de artefactos de Synapse
workspaces/sparkJobDefinitions/write, delete|Administrador de Synapse</br>Administrador de Synapse Apache Spark</br>Colaborador de Synapse</br>Editor de artefactos de Synapse
workspaces/sqlScripts/write, delete|Administrador de Synapse</br>Administrador de Synapse SQL</br>Colaborador de Synapse</br>Editor de artefactos de Synapse
workspaces/dataFlows/write, delete|Administrador de Synapse</br>Colaborador de Synapse</br>Editor de artefactos de Synapse
workspaces/pipelines/write, delete|Administrador de Synapse</br>Colaborador de Synapse</br>Editor de artefactos de Synapse
workspaces/triggers/write, delete|Administrador de Synapse</br>Colaborador de Synapse</br>Editor de artefactos de Synapse
workspaces/datasets/write, delete|Administrador de Synapse</br>Colaborador de Synapse</br>Editor de artefactos de Synapse
workspaces/libraries/write, delete|Administrador de Synapse</br>Administrador de Synapse Apache Spark</br>Colaborador de Synapse</br>Editor de artefactos de Synapse
workspaces/linkedServices/write, delete|Administrador de Synapse</br>Colaborador de Synapse</br>Editor de artefactos de Synapse</br>Administrador de datos vinculado a Synapse
workspaces/credentials/write, delete|Administrador de Synapse</br>Colaborador de Synapse</br>Editor de artefactos de Synapse</br>Administrador de datos vinculado a Synapse
workspaces/notebooks/viewOutputs/action|Administrador de Synapse</br>Administrador de Synapse Apache Spark</br>Colaborador de Synapse</br>Editor de artefactos de Synapse</br>Editor de artefactos de Synapse
workspaces/pipelines/viewOutputs/action|Administrador de Synapse</br>Colaborador de Synapse</br>Editor de artefactos de Synapse</br>Editor de artefactos de Synapse
workspaces/linkedServices/useSecret/action|Administrador de Synapse</br>Usuario de credenciales Synapse
workspaces/credentials/useSecret/action|Administrador de Synapse</br>Usuario de credenciales Synapse

## <a name="synapse-rbac-scopes-and-their-supported-roles"></a>Ámbitos de Synapse RBAC y los roles compatibles

En la tabla siguiente se enumeran los ámbitos de Synapse RBAC y los roles que se pueden asignar en cada ámbito. 

>[!note]
>Para crear o eliminar un objeto, debe tener permisos en un ámbito de nivel superior.

Ámbito|Roles
--|--
Área de trabajo |Administrador de Synapse</br>Administrador de Synapse Apache Spark</br>Administrador de Synapse SQL</br>Colaborador de Synapse</br>Editor de artefactos de Synapse</br>Usuario de artefactos de Synapse</br>Operador de proceso de Synapse </br>Usuario de credenciales Synapse</br>Administrador de datos vinculado a Synapse</br>Usuario de Synapse
Grupo de Apache Spark | Administrador de Synapse </br>Colaborador de Synapse </br> Operador de proceso de Synapse
Tiempo de ejecución de integración | Administrador de Synapse </br>Colaborador de Synapse </br> Operador de proceso de Synapse
Servicio vinculado |Administrador de Synapse </br>Usuario de credenciales Synapse
Credential: |Administrador de Synapse </br>Usuario de credenciales Synapse
 
>[!note]
>El ámbito de todos los roles y acciones de artefactos se encuentra en el nivel de área de trabajo. 

## <a name="next-steps"></a>Pasos siguientes

Aprenda a [examinar las asignaciones de roles de Synapse RBAC](./how-to-review-synapse-rbac-role-assignments.md) para un área de trabajo.

Aprenda a [asignar roles de Synapse RBAC](./how-to-manage-synapse-rbac-role-assignments.md)
