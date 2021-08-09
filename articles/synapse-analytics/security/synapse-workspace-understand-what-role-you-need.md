---
title: Descripción de los roles necesarios para realizar tareas comunes en Synapse
description: En este artículo se describen los roles RBAC de Synapse integrados que se necesitan para realizar tareas específicas.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 87156914769c2cb76b7509b74ebfeb13b15af95e
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/02/2021
ms.locfileid: "110784201"
---
# <a name="understand-the-roles-required-to-perform-common-tasks-in-synapse"></a>Descripción de los roles necesarios para realizar tareas comunes en Synapse

Este artículo le ayudará a comprender qué rol RBAC de Synapse (control de acceso basado en rol) o de Azure necesita para realizar el trabajo en Synapse Studio.  

## <a name="synapse-studio-access-control-and-workflow-summary"></a>Resumen del control de acceso y flujo de trabajo de Synapse Studio 

### <a name="accessing-synapse-studio-and-viewing-its-content"></a>Acceso a Synapse Studio y visualización de su contenido

- Puede abrir Synapse Studio, ver los detalles del área de trabajo y enumerar los recursos de Azure (grupos de SQL, grupos de Spark o entornos de ejecución de integración) si se le ha asignado algún rol RBAC de Synapse o tiene el rol de lector, colaborador o propietario de Azure en el área de trabajo.

### <a name="resource-management"></a>Administración de recursos

- Puede crear grupos de SQL, grupos de Apache Spark y entornos de ejecución de integración si es propietario o colaborador de Azure en el área de trabajo.
- Puede pausar o escalar un grupo de SQL dedicado y configurar un grupo de Spark o un entorno de ejecución de integración si es propietario o colaborador de Azure en el área de trabajo o en ese recurso.

### <a name="viewing-and-editing-code-artifacts"></a>Vista y edición de artefactos de código

- Con el acceso a Synapse Studio, puede crear nuevos artefactos de código, como scripts SQL, cuadernos, trabajos de Spark, servicios vinculados, canalizaciones, flujos de trabajo, desencadenadores y credenciales.  (Estos artefactos se pueden publicar o guardar con permisos adicionales).  
- Si es un usuario o editor de artefactos de Synapse, o bien un colaborador o administrador de Synapse, puede mostrar, abrir y editar los artefactos de código ya publicados.

### <a name="executing-your-code"></a>Ejecución del código

- Puede ejecutar scripts SQL en grupos de SQL si tiene los permisos SQL necesarios definidos en los grupos de SQL.  
- Puede ejecutar cuadernos y trabajos de Spark si tiene permisos de operador de proceso de Synapse en el área de trabajo o en grupos de Apache Spark específicos.  
- Con los permisos de operador de proceso en el área de trabajo o en entornos de ejecución de integración específicos y los permisos de credenciales adecuados, puede ejecutar canalizaciones.

### <a name="monitoring-and-managing-execution"></a>Supervisión y administración de la ejecución
- Puede revisar el estado de los cuadernos y trabajos en ejecución en los grupos de Apache Spark si es un usuario de Synapse.
- Puede revisar los registros y cancelar los trabajos y las canalizaciones en ejecución si es un operador de proceso de Synapse en el área de trabajo o para un grupo o canalización de Spark específico.  

### <a name="publishing-and-saving-your-code"></a>Publicación y almacenamiento del código

- Puede publicar artefactos de código nuevos o actualizados en el servicio si es un editor de artefactos de Synapse o bien un colaborador o administrador de Synapse. 
- Puede confirmar los artefactos de código en una rama de trabajo de un repositorio de GIT si el área de trabajo está habilitada para GIT y usted tiene permisos de Git. Con GIT habilitado, solo se permite la publicación desde la rama de colaboración.
- Si cierra Synapse Studio sin publicar o confirmar los cambios en los artefactos de código, se perderán dichos cambios.


## <a name="tasks-and-required-roles"></a>Tareas y roles necesarios

En la tabla siguiente se muestran las tareas comunes y, para cada tarea, los roles RBAC de Synapse o Azure necesarios.  

>[!Note]
>- El administrador de Synapse no se muestra para cada tarea a menos que sea el único rol que proporciona el permiso necesario.  Un administrador de Synapse puede realizar todas las tareas que habilitan otros roles RBAC de Synapse.</br>
>- Se muestra el rol RBAC de Synapse mínimo requerido.
>- Todos los roles RBAC de Synapse de cualquier ámbito le proporcionan permisos de usuario de Synapse en el área de trabajo.
>- Todos los permisos o acciones de RBAC de Synapse que se muestran en la tabla tienen el prefijo Microsoft/Synapse/áreas de trabajo/... </br>


Tarea (quiero...) |Rol (necesito ser...)|Permiso/acción de RBAC de Synapse
--|--|--
|Abrir Synapse Studio en un área de trabajo|Usuario de Synapse o|leer
| |propietario, colaborador o lector de Azure en el área de trabajo|ninguno
|Mostrar grupos de SQL, grupos de Apache Spark, entornos de ejecución de integración y acceso a los detalles de configuración|Usuario de Synapse o|leer|
||propietario, colaborador o lector de Azure en el área de trabajo|ninguno
|Mostrar servicios vinculados, credenciales y puntos de conexión privados administrados|Usuario de Synapse|leer
GRUPOS DE SQL|
Crear un grupo de SQL dedicado o un grupo de SQL sin servidor|Propietario o colaborador de Azure en el área de trabajo|ninguno
Administrar (pausar, escalar o eliminar) un grupo de SQL dedicado|Propietario o colaborador de Azure en el grupo de SQL o el área de trabajo|ninguno
Crear un script de SQL</br>|Usuario de Synapse o </br>propietario o colaborador de Azure en el área de trabajo </br>*Se requieren permisos SQL adicionales para ejecutar, publicar o confirmar un script de SQL*.|
Mostrar y abrir cualquier script de SQL publicado| Usuario de artefactos de Synapse, editor de artefactos, colaborador de Synapse|artefactos/lectura
Ejecutar un script de SQL en un grupo de SQL sin servidor|Permisos SQL en el grupo (concedidos automáticamente a un administrador de Synapse)|ninguno
Ejecutar un script de SQL en un grupo de SQL dedicado|Permisos de SQL en el grupo|ninguno
Publicar un script de SQL nuevo, actualizado o eliminado|Editor de artefactos de Synapse, colaborador de Synapse|sqlScripts/escritura, eliminación
Confirmar cambios en un script de SQL el un repositorio de Git|Requiere permisos de GIT en el repositorio.|
Asignar un administrador de Active Directory en el área de trabajo (mediante las propiedades del área de trabajo de Azure Portal)|Propietario o colaborador de Azure en el área de trabajo |
GRUPOS DE APACHE SPARK|
Creación de un grupo de Apache Spark|Propietario o colaborador de Azure en el área de trabajo|
Supervisión de aplicaciones de Apache Spark| Usuario de Synapse|leer
Ver los registros de la ejecución del cuaderno y el trabajo |Operador de proceso de Synapse|
Cancelar cualquier trabajo del cuaderno o de Spark que se ejecute en un grupo de Apache Spark|Operador de proceso de Synapse en el grupo de Apache Spark.|bigDataPools/useCompute
Crear un cuaderno o una definición de trabajo|Usuario de Synapse o </br>propietario, colaborador o lector de Azure en el área de trabajo</br> *Se requieren permisos adicionales para ejecutar, publicar o confirmar cambios*|leer</br></br></br></br></br> 
Mostrar y abrir una definición de trabajo o un cuaderno publicado, incluida la revisión de las salidas guardadas|Usuario o editor de artefactos de Synapse, colaborador de Synapse en el área de trabajo|artefactos/lectura
Ejecutar un cuaderno y revisar su salida, o bien enviar un trabajo de Spark|Administrator de Apache Spark de Synapse, operador de proceso de Synapse en el grupo de Apache Spark seleccionado|bigDataPools/useCompute 
Publicar o eliminar una definición de trabajo o un cuaderno (incluida la salida) en el servicio|Editor de artefactos en el área de trabajo, administrador de Apache Spark de Synapse|cuadernos/escritura, eliminación
Confirmar cambios en una definición de trabajo o un cuaderno en el repositorio de Git|Permisos de Git|ninguno
CANALIZACIONES, ENTORNOS DE EJECUCIÓN, FLUJOS DE DATOS, CONJUNTOS DE DATOS Y DESENCADENADORES|
Crear, actualizar o eliminar un entorno de ejecución de integración|Propietario o colaborador de Azure en el área de trabajo|
Supervisar el estado del entorno de ejecución de integración|Usuario de Synapse|lectura, canalizaciones/viewOutputs
Revisar las ejecuciones de canalización|Editor de artefactos de Synapse/colaborador de Synapse|lectura, canalizaciones/viewOutputs 
Crear una canalización |Usuario de Synapse</br>*Se requieren permisos adicionales de Synapse para depurar, agregar desencadenadores, publicar o confirmar cambios*|leer
Crear un flujo de datos o un conjunto de datos |Usuario de Synapse</br>*Se requieren permisos adicionales de Synapse para publicar o confirmar cambios*|leer
Mostrar y abrir una canalización publicada |Usuario de artefactos de Synapse | artefactos/lectura
Obtener una vista previa de los datos del conjunto de datos|Usuario de Synapse + usuario de credenciales de Synapse en WorkspaceSystemIdentity| 
Depurar una canalización mediante el entorno de ejecución de integración predeterminado|Usuario de Synapse + usuario de credenciales de Synapse en la credencial de WorkspaceSystemIdentity|lectura, </br>credenciales/useSecret
Crear un desencadenador, incluido el de desencadenar ahora (requiere permiso para ejecutar la canalización)|Usuario de Synapse + usuario de credenciales de Synapse en WorkspaceSystemIdentity|lectura, credenciales/useSecret/acción
Ejecutar una canalización|Usuario de Synapse + usuario de credenciales de Synapse en WorkspaceSystemIdentity|lectura, credenciales/useSecret/acción
Copiar datos mediante la herramienta Copiar datos|Usuario de Synapse + usuario de credenciales de Synapse en la identidad del sistema del área de trabajo|lectura, credenciales/useSecret/acción
Ingerir datos (mediante una programación)|Usuario de Synapse + usuario de credenciales de Synapse en la identidad del sistema del área de trabajo|lectura, credenciales/useSecret/acción
Publicar una canalización, un flujo de datos o un desencadenador nuevo, actualizado o eliminado en el servicio|Editor de artefactos de Synapse en el área de trabajo|canalizaciones/escritura, eliminación</br>flujos de datos/escritura, eliminación</br>desencadenadores/escritura, eliminación
Confirmar cambios en canalizaciones, flujos de datos, conjuntos de datos y desencadenadores en el repositorio de Git |Permisos de Git|ninguno 
SERVICIOS VINCULADOS|
Crear un servicio vinculado (incluye la asignación de una credencial)|Usuario de Synapse</br>*Se requieren permisos adicionales para usar un servicio vinculado con credenciales, o para publicar o confirmar cambios*|leer
Mostrar y abrir un servicio vinculado publicado|Usuario de artefactos de Synapse|linkedServices/escritura, eliminación  
Probar la conexión en un servicio vinculado protegido por una credencial|Usuario de Synapse más usuario de credenciales Synapse|credenciales/useSecret/acción|
Publicar un servicio vinculado|Editor de artefactos de Synapse, Data Manager de Synapse Link|linkedServices/escritura, eliminación
Confirmar definiciones de servicios vinculados en el repositorio de Git|Permisos de Git|ninguno
ADMINISTRACIÓN DE ACCESO|
Revisar asignaciones de roles RBAC de Synapse en cualquier ámbito|Usuario de Synapse|leer
Asignar y quitar asignaciones de roles RBAC de Synapse para usuarios, grupos y entidades de servicio| Administrador de Synapse en el área de trabajo o en un ámbito de elemento de área de trabajo específico|roleAssignments/escritura, eliminación 

>[!Note]
>Los usuarios invitados de otro inquilino también podrán revisar, agregar o cambiar las asignaciones de roles cuando se hayan asignado como administradores de Synapse. 

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre [cómo revisar asignaciones de roles RBAC de Synapse](./how-to-review-synapse-rbac-role-assignments.md).

Obtenga información sobre [cómo administrar asignaciones de roles RBAC de Synapse](./how-to-manage-synapse-rbac-role-assignments.md). 
