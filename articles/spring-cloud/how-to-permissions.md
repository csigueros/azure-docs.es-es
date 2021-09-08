---
title: Uso de permisos en Azure Spring Cloud
description: En este artículo se muestra cómo crear roles personalizados que delegan permisos en recursos de Azure Spring Cloud.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: 61c4eb0322de60bb90d49bd71b111fe8b2db07ea
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015548"
---
# <a name="how-to-use-permissions-in-azure-spring-cloud"></a>Uso de permisos en Azure Spring Cloud
En este artículo se muestra cómo crear roles personalizados que delegan permisos en recursos de Azure Spring Cloud. Los roles personalizados amplían los [roles integrados de Azure](../role-based-access-control/built-in-roles.md) con varios permisos estándar.

Implementaremos los siguientes roles personalizados:

* **Rol de desarrollador**:

    * Implementar
    * Prueba
    * Reiniciar aplicaciones
    * Aplicar y realizar cambios en las configuraciones de aplicaciones del repositorio de Git
    * Obtener secuencias de registros

* **Rol "Ingeniero de DevOps":**

    * Crear, leer, actualizar y eliminar todo el contenido de Azure Spring Cloud
* **Rol "Operaciones - Ingeniería de fiabilidad de sitios":**

    * Reiniciar aplicaciones
    * Obtener secuencias de registros
    * No: realizar cambios en aplicaciones o configuraciones

* **Rol "Azure Pipelines/Jenkins/Acciones de GitHub":**

    * Realizar operaciones de creación, lectura, actualización y eliminación
    * Usar Terraform o plantillas de ARM para crear y configurar todo en Azure Spring Cloud y aplicaciones con una instancia de servicio: Azure Pipelines, Jenkins o Acciones de GitHub

## <a name="define-the-developer-role"></a>Definición del rol "Desarrollador"

El rol "Desarrollador" incluye permisos para reiniciar aplicaciones y ver sus secuencias de registros. Este rol no puede realizar cambios en aplicaciones o configuraciones.

#### <a name="portal"></a>[Portal](#tab/Azure-portal)
1. En Azure Portal, abra la suscripción donde quiera que se pueda asignar el rol personalizado.
2. Abra **Control de acceso (IAM)** .
3. Seleccione **Agregar**.
4. Seleccione **Agregar rol personalizado**.
5. Seleccione **Siguiente**:

   ![Captura de pantalla en la que se muestra la pestaña "Conceptos básicos" de la ventana "Crear un rol personalizado"](media/spring-cloud-permissions/create-custom-role.png)

6. Seleccione **Agregar permisos**:

   ![Captura de pantalla en la que se muestra el botón "Agregar permisos"](media/spring-cloud-permissions/add-permissions.png)


7. En el cuadro de búsqueda, busque **Microsoft.app**. Seleccione **Microsoft Azure Spring Cloud**:

   ![Captura de pantalla en la que se muestran los resultados de la búsqueda de "Microsoft.app"](media/spring-cloud-permissions/spring-cloud-permissions.png)

8. Seleccione los permisos para el rol "Desarrollador".

   En **Microsoft.AppPlatform/Spring**, seleccione lo siguiente:
   * **Write : Create or Update Azure Spring Cloud service instance (Escritura: Crear o actualizar instancia del servicio Azure Spring Cloud)**
   * **Read : Get Azure Spring Cloud service instance (Lectura: Obtener instancia del servicio Azure Spring Cloud)**
   * **Other : List Azure Spring Cloud service instance test keys (Otro: Enumerar claves de prueba de la instancia del servicio Azure Spring Cloud)**

   En **Microsoft.AppPlatform/Spring/apps**, seleccione lo siguiente:
   * **Read : Read Microsoft Azure Spring Cloud application (Lectura: Leer aplicación de Microsoft Azure Spring Cloud)**
   * **Other : Get Microsoft Azure Spring Cloud application resource upload URL (Otro: Obtener dirección URL de carga de recursos de aplicación de Microsoft Azure Spring Cloud)**

   En **Microsoft.AppPlatform/Spring/apps/bindings**, seleccione lo siguiente:
   * **Read : Read Microsoft Azure Spring Cloud application binding (Lectura: Leer enlace de aplicación de Microsoft Azure Spring Cloud)**

   En **Microsoft.AppPlatform/Spring/apps/deployments**, seleccione lo siguiente:
   * **Write : Write Microsoft Azure Spring Cloud application deployment (Escritura: Escribir implementación de aplicación de Microsoft Azure Spring Cloud)**
   * **Read : Read Microsoft Azure Spring Cloud application deployment (Lectura: Leer implementación de aplicación de Microsoft Azure Spring Cloud)**
   * **Other : Start Microsoft Azure Spring Cloud application deployment (Otro: Iniciar implementación de aplicación de Microsoft Azure Spring Cloud)**
   * **Other : Stop Microsoft Azure Spring Cloud application deployment (Otro: Detener implementación de aplicación de Microsoft Azure Spring Cloud)**
   * **Other : Restart Microsoft Azure Spring Cloud application deployment (Otro: Reiniciar implementación de aplicación de Microsoft Azure Spring Cloud)**
   * **Other : Get Microsoft Azure Spring Cloud application deployment log file URL (Otro: Obtener dirección URL de archivo de registro de implementación de aplicación de Microsoft Azure Spring Cloud)**

   En **Microsoft.AppPlatform/Spring/apps/domains**, seleccione lo siguiente:
   * **Read : Read Microsoft Azure Spring Cloud application custom domain (Lectura: Leer dominio personalizado de aplicación de Microsoft Azure Spring Cloud)**

   En **Microsoft.AppPlatform/Spring/certificates**, seleccione lo siguiente:
   * **Read : Read Microsoft Azure Spring Cloud certificate (Lectura: Leer certificado de Microsoft Azure Spring Cloud)**

   En **Microsoft.AppPlatform/locations/operationResults/Spring**, seleccione lo siguiente:
   * **Read : Read operation result (Lectura: Leer resultado de la operación)**

   En **Microsoft.AppPlatform/locations/operationStatus/operationId**, seleccione lo siguiente:
   * **Read : Read operation status (Lectura: Leer estado de la operación)**

    [ ![Captura de pantalla en la que se muestran las opciones seleccionadas para los permisos de "Desarrollador"](media/spring-cloud-permissions/developer-permissions-box.png) ](media/spring-cloud-permissions/developer-permissions-box.png#lightbox)

9. Seleccione **Agregar**.

10. Revise los permisos.

11. Seleccione **Revisar y crear**.

#### <a name="json"></a>[JSON](#tab/JSON)
1. En Azure Portal, abra la suscripción donde quiera que se pueda asignar el rol personalizado.
2. Abra **Control de acceso (IAM)** .
3. Seleccione **Agregar**.
4. Seleccione **Agregar rol personalizado**.
5. Seleccione **Siguiente**.

6. Seleccione la pestaña **JSON**.

7. Seleccione **Editar** y, a continuación, elimine el texto predeterminado:

   ![Captura de pantalla en la que se muestra el texto JSON predeterminado](media/spring-cloud-permissions/create-custom-role-edit-json.png)

8. Pegue el siguiente código JSON para definir el rol "Desarrollador":

   ```json
   {
     "properties": {
       "roleName": "Developer",
       "description": "",
       "assignableScopes": [
         "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
       ],
       "permissions": [
         {
           "actions": [
             "Microsoft.AppPlatform/Spring/write",
             "Microsoft.AppPlatform/Spring/read",
             "Microsoft.AppPlatform/Spring/listTestKeys/action",
             "Microsoft.AppPlatform/Spring/apps/read",
             "Microsoft.AppPlatform/Spring/apps/getResourceUploadUrl/action",
             "Microsoft.AppPlatform/Spring/apps/bindings/read",
             "Microsoft.AppPlatform/Spring/apps/domains/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/write",
             "Microsoft.AppPlatform/Spring/apps/deployments/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/start/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/stop/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/restart/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/getLogFileUrl/action",
             "Microsoft.AppPlatform/Spring/certificates/read",
             "Microsoft.AppPlatform/locations/operationResults/Spring/read",
             "Microsoft.AppPlatform/locations/operationStatus/operationId/read"
           ],
           "notActions": [],
           "dataActions": [],
           "notDataActions": []
         }
       ]
     }
   }
   ```

   ![Captura de pantalla en la que se muestra el código JSON correspondiente al rol "Desarrollador"](media/spring-cloud-permissions/create-custom-role-json.png)

9. Seleccione **Guardar**.

10. Revise los permisos.

11. Seleccione **Revisar y crear**.

---

## <a name="define-the-devops-engineer-role"></a>Definición del rol "Ingeniero de DevOps"

Este procedimiento define un rol con permisos para implementar, probar y reiniciar aplicaciones de Azure Spring Cloud.

#### <a name="portal"></a>[Portal](#tab/Azure-portal)
1. Repita los pasos del 1 al 4 del procedimiento para agregar el rol "Desarrollador".

2. Seleccione los permisos para el rol "Ingeniero de DevOps":

   En **Microsoft.AppPlatform/Spring**, seleccione lo siguiente:
   * **Write : Create or Update Azure Spring Cloud service instance (Escritura: Crear o actualizar instancia del servicio Azure Spring Cloud)**
   * **Delete : Delete Azure Spring Cloud service instance (Eliminación: Eliminar instancia del servicio Azure Spring Cloud)**
   * **Read : Get Azure Spring Cloud service instance (Lectura: Obtener instancia del servicio Azure Spring Cloud)**
   * **Other : Enable Azure Spring Cloud service instance test endpoint (Otro: Habilitar punto de conexión de prueba de la instancia del servicio Azure Spring Cloud)**
   * **Other : Disable Azure Spring Cloud service instance test endpoint (Otro: Deshabilitar punto de conexión de prueba de la instancia del servicio Azure Spring Cloud)**
   * **Other : List Azure Spring Cloud service instance test keys (Otro: Enumerar claves de prueba de la instancia del servicio Azure Spring Cloud)**
   * **Other : Regenerate Azure Spring Cloud service instance test keys (Otro: Regenerar clave de prueba de la instancia del servicio Azure Spring Cloud)**

   En **Microsoft.AppPlatform/Spring/apps**, seleccione lo siguiente:
   * **Write : Write Microsoft Azure Spring Cloud application (Escritura: Escribir aplicación de Microsoft Azure Spring Cloud)**
   * **Delete : Delete Microsoft Azure Spring Cloud application (Eliminación: Eliminar aplicación de Microsoft Azure Spring Cloud)**
   * **Read : Read Microsoft Azure Spring Cloud application (Lectura: Leer aplicación de Microsoft Azure Spring Cloud)**
   * **Other : Get Microsoft Azure Spring Cloud application resource upload URL (Otro: Obtener dirección URL de carga de recursos de aplicación de Microsoft Azure Spring Cloud)**
   * **Other : Validate Microsoft Azure Spring Cloud application custom domain (Otro: Validar dominio personalizado de aplicación de Microsoft Azure Spring Cloud)**

   En **Microsoft.AppPlatform/Spring/apps/bindings**, seleccione lo siguiente:
   * **Write : Write Microsoft Azure Spring Cloud application binding (Escritura: Escribir enlace de aplicación de Microsoft Azure Spring Cloud)**
   * **Delete : Delete Microsoft Azure Spring Cloud application binding (Eliminación: Eliminar enlace de aplicación de Microsoft Azure Spring Cloud)**
   * **Read : Read Microsoft Azure Spring Cloud application binding (Lectura: Leer enlace de aplicación de Microsoft Azure Spring Cloud)**

   En **Microsoft.AppPlatform/Spring/apps/deployments**, seleccione lo siguiente:
   * **Write : Write Microsoft Azure Spring Cloud application deployment (Escritura: Escribir implementación de aplicación de Microsoft Azure Spring Cloud)**
   * **Delete : Delete Azure Spring Cloud application deployment (Eliminación: Eliminar implementación de aplicación de Azure Spring Cloud)**
   * **Read : Read Microsoft Azure Spring Cloud application deployment (Lectura: Leer implementación de aplicación de Microsoft Azure Spring Cloud)**
   * **Other : Start Microsoft Azure Spring Cloud application deployment (Otro: Iniciar implementación de aplicación de Microsoft Azure Spring Cloud)**
   * **Other : Stop Microsoft Azure Spring Cloud application deployment (Otro: Detener implementación de aplicación de Microsoft Azure Spring Cloud)**
   * **Other : Restart Microsoft Azure Spring Cloud application deployment (Otro: Reiniciar implementación de aplicación de Microsoft Azure Spring Cloud)**
   * **Other : Get Microsoft Azure Spring Cloud application deployment log file URL (Otro: Obtener dirección URL de archivo de registro de implementación de aplicación de Microsoft Azure Spring Cloud)**

   En **Microsoft.AppPlatform/Spring/apps/deployments/skus**, seleccione lo siguiente:
   * **Read : List application deployment available skus (Lectura: Enumerar SKU disponibles de implementación de aplicación)**

   En **Microsoft.AppPlatform/locations**, seleccione lo siguiente:
   * **Other : Check name availability (Otro: Comprobar disponibilidad del nombre)**

   En **Microsoft.AppPlatform/locations/operationResults/Spring** seleccione lo siguiente:
   * **Read : Read operation result (Lectura: Leer resultado de la operación)**

   En **Microsoft.AppPlatform/locations/operationStatus/operationId**, seleccione lo siguiente:
   * **Read : Read operation status (Lectura: Leer estado de la operación)**

   En **Microsoft.AppPlatform/skus**, seleccione lo siguiente:
   * **Read : List available skus (Lectura: Enumerar SKU disponibles)**

   [ ![Captura de pantalla en la que se muestran las opciones seleccionadas para los permisos de "Ingeniero de DevOps"](media/spring-cloud-permissions/dev-ops-permissions.png) ](media/spring-cloud-permissions/dev-ops-permissions.png#lightbox)

3. Seleccione **Agregar**.

4. Revise los permisos.

5. Seleccione **Revisar y crear**.

#### <a name="json"></a>[JSON](#tab/JSON)

1. Repita los pasos del 1 al 4 del procedimiento para agregar el rol "Desarrollador".
2. Seleccione **Siguiente**.

3. Seleccione la pestaña **JSON**.

4. Seleccione **Editar** y, a continuación, elimine el texto predeterminado:

   ![Captura de pantalla en la que se muestra el texto JSON predeterminado](media/spring-cloud-permissions/create-custom-role-edit-json.png)

5. Pegue el siguiente código JSON para definir el rol "Ingeniero de DevOps":

   ```json
   {
     "properties": {
       "roleName": "DevOps engineer",
       "description": "",
       "assignableScopes": [
         "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
       ],
       "permissions": [
         {
           "actions": [
             "Microsoft.AppPlatform/Spring/write",
             "Microsoft.AppPlatform/Spring/delete",
             "Microsoft.AppPlatform/Spring/read",
             "Microsoft.AppPlatform/Spring/enableTestEndpoint/action",
             "Microsoft.AppPlatform/Spring/disableTestEndpoint/action",
             "Microsoft.AppPlatform/Spring/listTestKeys/action",
             "Microsoft.AppPlatform/Spring/regenerateTestKey/action",
             "Microsoft.AppPlatform/Spring/apps/write",
             "Microsoft.AppPlatform/Spring/apps/delete",
             "Microsoft.AppPlatform/Spring/apps/read",
             "Microsoft.AppPlatform/Spring/apps/getResourceUploadUrl/action",
             "Microsoft.AppPlatform/Spring/apps/validateDomain/action",
             "Microsoft.AppPlatform/Spring/apps/bindings/write",
             "Microsoft.AppPlatform/Spring/apps/bindings/delete",
             "Microsoft.AppPlatform/Spring/apps/bindings/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/write",
             "Microsoft.AppPlatform/Spring/apps/deployments/delete",
             "Microsoft.AppPlatform/Spring/apps/deployments/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/start/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/stop/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/restart/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/getLogFileUrl/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/skus/read",
             "Microsoft.AppPlatform/locations/checkNameAvailability/action",
             "Microsoft.AppPlatform/locations/operationResults/Spring/read",
             "Microsoft.AppPlatform/locations/operationStatus/operationId/read",
             "Microsoft.AppPlatform/skus/read"
           ],
           "notActions": [],
           "dataActions": [],
           "notDataActions": []
         }
       ]
     }
   }
   ```

6. Revise los permisos.

7. Seleccione **Revisar y crear**.

---

## <a name="define-the-ops---site-reliability-engineering-role"></a>Definición del rol "Operaciones - Ingeniería de fiabilidad de sitios"

Este procedimiento define un rol con permisos para implementar, probar y reiniciar aplicaciones de Azure Spring Cloud.

#### <a name="portal"></a>[Portal](#tab/Azure-portal)
1. Repita los pasos del 1 al 4 del procedimiento para agregar el rol "Desarrollador".
2. Seleccione los permisos para el rol de operaciones/ingeniería de confiabilidad de sitios:

   En **Microsoft.AppPlatform/Spring**, seleccione lo siguiente:
   * **Read : Get Azure Spring Cloud service instance (Lectura: Obtener instancia del servicio Azure Spring Cloud)**
   * **Other : List Azure Spring Cloud service instance test keys (Otro: Enumerar claves de prueba de la instancia del servicio Azure Spring Cloud)**

   En **Microsoft.AppPlatform/Spring/apps**, seleccione lo siguiente:
   * **Read : Read Microsoft Azure Spring Cloud application (Lectura: Leer aplicación de Microsoft Azure Spring Cloud)**

   En **Microsoft.AppPlatform/apps/deployments**, seleccione lo siguiente:
   * **Read : Read Microsoft Azure Spring Cloud application deployment (Lectura: Leer implementación de aplicación de Microsoft Azure Spring Cloud)**
   * **Other : Start Microsoft Azure Spring Cloud application deployment (Otro: Iniciar implementación de aplicación de Microsoft Azure Spring Cloud)**
   * **Other : Stop Microsoft Azure Spring Cloud application deployment (Otro: Detener implementación de aplicación de Microsoft Azure Spring Cloud)**
   * **Other : Restart Microsoft Azure Spring Cloud application deployment (Otro: Reiniciar implementación de aplicación de Microsoft Azure Spring Cloud)**

   En **Microsoft.AppPlatform/locations/operationResults/Spring**, seleccione lo siguiente:
   * **Read : Read operation result (Lectura: Leer resultado de la operación)**

   En **Microsoft.AppPlatform/locations/operationStatus/operationId**, seleccione lo siguiente:
   * **Read : Read operation status (Lectura: Leer estado de la operación)**

   [ ![Captura de pantalla en la que se muestran las opciones seleccionadas para los permisos de "Operaciones - Ingeniería de fiabilidad de sitios"](media/spring-cloud-permissions/ops-sre-permissions.png)](media/spring-cloud-permissions/ops-sre-permissions.png#lightbox)

3. Seleccione **Agregar**.

4. Revise los permisos.

5. Seleccione **Revisar y crear**.

#### <a name="json"></a>[JSON](#tab/JSON)
1. Repita los pasos del 1 al 4 del procedimiento para agregar el rol "Desarrollador".
2. Seleccione **Siguiente**.

3. Seleccione la pestaña **JSON**.

4. Seleccione **Editar** y, a continuación, elimine el texto predeterminado:

   ![Captura de pantalla en la que se muestra el texto JSON predeterminado](media/spring-cloud-permissions/create-custom-role-edit-json.png)

5. Pegue el siguiente código JSON para definir el rol "Operaciones - Ingeniería de fiabilidad de sitios":

   ```json
   {
     "properties": {
       "roleName": "Ops - Site Reliability Engineering",
       "description": "",
       "assignableScopes": [
         "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
       ],
       "permissions": [
         {
           "actions": [
             "Microsoft.AppPlatform/Spring/read",
             "Microsoft.AppPlatform/Spring/listTestKeys/action",
             "Microsoft.AppPlatform/Spring/apps/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/start/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/stop/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/restart/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/getLogFileUrl/action",
             "Microsoft.AppPlatform/locations/operationResults/Spring/read",
             "Microsoft.AppPlatform/locations/operationStatus/operationId/read"
           ],
           "notActions": [],
           "dataActions": [],
           "notDataActions": []
         }
       ]
     }
   }
   ```

6. Revise los permisos.

7. Seleccione **Revisar y crear**.

---

## <a name="define-the-azure-pipelines--jenkins--github-actions-role"></a>Definición del rol "Azure Pipelines/Jenkins/Acciones de GitHub"

Este rol puede crear y configurar todo en Azure Spring Cloud y aplicaciones con una instancia de servicio. Este rol se usa para liberar o implementar código.

#### <a name="portal"></a>[Portal](#tab/Azure-portal)

1. Repita los pasos del 1 al 4 del procedimiento para agregar el rol "Desarrollador".
2. Abra las opciones de **Permisos**.

3. Seleccione los permisos para el rol "Azure Pipelines/Jenkins/Acciones de GitHub":

   En **Microsoft.AppPlatform/Spring**, seleccione lo siguiente:
   * **Write : Create or Update Azure Spring Cloud service instance (Escritura: Crear o actualizar instancia del servicio Azure Spring Cloud)**
   * **Delete : Delete Azure Spring Cloud service instance (Eliminación: Eliminar instancia del servicio Azure Spring Cloud)**
   * **Read : Get Azure Spring Cloud service instance (Lectura: Obtener instancia del servicio Azure Spring Cloud)**
   * **Other : Enable Azure Spring Cloud service instance test endpoint (Otro: Habilitar punto de conexión de prueba de la instancia del servicio Azure Spring Cloud)**
   * **Other : Disable Azure Spring Cloud service instance test endpoint (Otro: Deshabilitar punto de conexión de prueba de la instancia del servicio Azure Spring Cloud)**
   * **Other : List Azure Spring Cloud service instance test keys (Otro: Enumerar claves de prueba de la instancia del servicio Azure Spring Cloud)**
   * **Other : Regenerate Azure Spring Cloud service instance test keys (Otro: Regenerar clave de prueba de la instancia del servicio Azure Spring Cloud)**

   En **Microsoft.AppPlatform/Spring/apps**, seleccione lo siguiente:
   * **Write : Write Microsoft Azure Spring Cloud application (Escritura: Escribir aplicación de Microsoft Azure Spring Cloud)**
   * **Delete : Delete Microsoft Azure Spring Cloud application (Eliminación: Eliminar aplicación de Microsoft Azure Spring Cloud)**
   * **Read : Read Microsoft Azure Spring Cloud application (Lectura: Leer aplicación de Microsoft Azure Spring Cloud)**
   * **Other : Get Microsoft Azure Spring Cloud application resource upload URL (Otro: Obtener dirección URL de carga de recursos de aplicación de Microsoft Azure Spring Cloud)**
   * **Other : Validate Microsoft Azure Spring Cloud application custom domain (Otro: Validar dominio personalizado de aplicación de Microsoft Azure Spring Cloud)**

   En **Microsoft.AppPlatform/Spring/apps/bindings**, seleccione lo siguiente:
   * **Write : Write Microsoft Azure Spring Cloud application binding (Escritura: Escribir enlace de aplicación de Microsoft Azure Spring Cloud)**
   * **Delete : Delete Microsoft Azure Spring Cloud application binding (Eliminación: Eliminar enlace de aplicación de Microsoft Azure Spring Cloud)**
   * **Read : Read Microsoft Azure Spring Cloud application binding (Lectura: Leer enlace de aplicación de Microsoft Azure Spring Cloud)**

   En **Microsoft.AppPlatform/Spring/apps/deployments**, seleccione lo siguiente:
   * **Write : Write Microsoft Azure Spring Cloud application deployment (Escritura: Escribir implementación de aplicación de Microsoft Azure Spring Cloud)**
   * **Delete : Delete Azure Spring Cloud application deployment (Eliminación: Eliminar implementación de aplicación de Azure Spring Cloud)**
   * **Read : Read Microsoft Azure Spring Cloud application deployment (Lectura: Leer implementación de aplicación de Microsoft Azure Spring Cloud)**
   * **Other : Start Microsoft Azure Spring Cloud application deployment (Otro: Iniciar implementación de aplicación de Microsoft Azure Spring Cloud)**
   * **Other : Stop Microsoft Azure Spring Cloud application deployment (Otro: Detener implementación de aplicación de Microsoft Azure Spring Cloud)**
   * **Other : Restart Microsoft Azure Spring Cloud application deployment (Otro: Reiniciar implementación de aplicación de Microsoft Azure Spring Cloud)**
   * **Other : Get Microsoft Azure Spring Cloud application deployment log file URL (Otro: Obtener dirección URL de archivo de registro de implementación de aplicación de Microsoft Azure Spring Cloud)**

   En **Microsoft.AppPlatform/Spring/apps/deployments/skus**, seleccione lo siguiente:
   * **Read : List application deployment available skus (Lectura: Enumerar SKU disponibles de implementación de aplicación)**

   En **Microsoft.AppPlatform/locations**, seleccione lo siguiente:
   * **Other : Check name availability (Otro: Comprobar disponibilidad del nombre)**

   En **Microsoft.AppPlatform/locations/operationResults/Spring**, seleccione lo siguiente:
   * **Read : Read operation result (Lectura: Leer resultado de la operación)**

   En **Microsoft.AppPlatform/locations/operationStatus/operationId**, seleccione lo siguiente:
   * **Read : Read operation status (Lectura: Leer estado de la operación)**

   En **Microsoft.AppPlatform/skus**, seleccione lo siguiente:
   * **Read : List available skus (Lectura: Enumerar SKU disponibles)**

   [ ![Captura de pantalla en la que se muestran las opciones seleccionadas para los permisos de "Azure Pipelines/Jenkins/Acciones de GitHub"](media/spring-cloud-permissions/pipelines-permissions-box.png) ](media/spring-cloud-permissions/pipelines-permissions-box.png#lightbox)

4. Seleccione **Agregar**.

5. Revise los permisos.

6. Seleccione **Revisar y crear**.

#### <a name="json"></a>[JSON](#tab/JSON)

1. Repita los pasos del 1 al 4 del procedimiento para agregar el rol "Desarrollador".
2. Seleccione **Siguiente**.

3. Seleccione la pestaña **JSON**.

4. Seleccione **Editar** y, a continuación, elimine el texto predeterminado:

   ![Captura de pantalla en la que se muestra el texto JSON predeterminado](media/spring-cloud-permissions/create-custom-role-edit-json.png)

5. Pegue el siguiente código JSON para definir el rol "Azure Pipelines/Jenkins/Acciones de GitHub":

   ```json
   {
     "properties": {
       "roleName": "Azure Pipelines/Provisioning",
       "description": "",
       "assignableScopes": [
         "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
       ],
       "permissions": [
         {
           "actions": [
             "Microsoft.AppPlatform/Spring/write",
             "Microsoft.AppPlatform/Spring/delete",
             "Microsoft.AppPlatform/Spring/read",
             "Microsoft.AppPlatform/Spring/enableTestEndpoint/action",
             "Microsoft.AppPlatform/Spring/disableTestEndpoint/action",
             "Microsoft.AppPlatform/Spring/listTestKeys/action",
             "Microsoft.AppPlatform/Spring/regenerateTestKey/action",
             "Microsoft.AppPlatform/Spring/apps/write",
             "Microsoft.AppPlatform/Spring/apps/delete",
             "Microsoft.AppPlatform/Spring/apps/read",
             "Microsoft.AppPlatform/Spring/apps/getResourceUploadUrl/action",
             "Microsoft.AppPlatform/Spring/apps/validateDomain/action",
             "Microsoft.AppPlatform/Spring/apps/bindings/write",
             "Microsoft.AppPlatform/Spring/apps/bindings/delete",
             "Microsoft.AppPlatform/Spring/apps/bindings/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/write",
             "Microsoft.AppPlatform/Spring/apps/deployments/delete",
             "Microsoft.AppPlatform/Spring/apps/deployments/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/start/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/stop/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/restart/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/getLogFileUrl/action",
             "Microsoft.AppPlatform/skus/read",
             "Microsoft.AppPlatform/locations/checkNameAvailability/action",
             "Microsoft.AppPlatform/locations/operationResults/Spring/read",
             "Microsoft.AppPlatform/locations/operationStatus/operationId/read"
           ],
           "notActions": [],
           "dataActions": [],
           "notDataActions": []
         }
       ]
     }
   }
   ```

6. Seleccione **Agregar**.

7. Revise los permisos.

---

## <a name="see-also"></a>Consulte también

* [Creación o actualización de roles personalizados de Azure mediante Azure Portal](../role-based-access-control/custom-roles-portal.md)

Para obtener más información sobre los tres métodos que definen permisos personalizados, consulte lo siguiente:
* [Clonación de un rol](../role-based-access-control/custom-roles-portal.md#clone-a-role)
* [Comienzo desde cero](../role-based-access-control/custom-roles-portal.md#start-from-scratch)
* [Inicio desde un archivo JSON](../role-based-access-control/custom-roles-portal.md#start-from-json)
