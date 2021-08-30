---
title: Creación de una entidad de credencial
titleSuffix: Azure Cognitive Services
description: Procedimiento de creación de una entidad de credencial para administrar credenciales de manera segura.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 06/22/2021
ms.author: mbullwin
ms.openlocfilehash: 0e16b92ff4a2316042e2177f9525734ba1949db1
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114342057"
---
# <a name="how-to-create-a-credential-entity"></a>Procedimiento de creación de una entidad de credencial

Al incorporar una fuente de distribución de datos, debe seleccionar un tipo de autenticación, algunos tipos de autenticación como la *cadena de conexión de Azure SQL* y la *entidad de servicio* necesitan una entidad de credencial para almacenar información relacionada con las credenciales a fin de administrar credenciales de manera segura. En este artículo se explica cómo crear una entidad de credencial para distintos tipos de credenciales en Metrics Advisor.
    

## <a name="basic-procedure-create-a-credential-entity"></a>Procedimiento básico: Creación de una entidad de credencial

Puede crear una **entidad de credencial** para almacenar información relacionada con credenciales y usarla para autenticarse en los orígenes de datos. Puede compartir la entidad de credencial con otros usuarios y permitirles conectarse a sus orígenes de datos sin compartir las credenciales reales. Se puede crear en la pestaña "Agregar fuente de distribución de datos" o en la pestaña "Entidad de credencial". Después de crear una entidad de credencial para un tipo de autenticación específico, puede simplemente elegir una entidad de credencial que creó al agregar una fuente de distribución de datos, lo que será útil al crear varias fuentes de distribución de datos. A continuación se muestra el procedimiento general para crear y usar una entidad de credencial:

1. Seleccione "+" para crear nueva entidad de credencial en la pestaña "Agregar fuente de distribución de datos" (también puede crear una en la pestaña "Entidad de credencial").

   ![Creación de una entidad de credencial](../media/create-credential-entity.png)
 
2. Establezca el nombre de la entidad de credencial, la descripción (si es necesario), el tipo de credencial (que equivale al *tipo de autenticación*) y otro tipo de configuraciones.

   ![Establecimiento de una entidad de credencial](../media/set-credential-entity.png)
 
3. Después de crear una entidad de credencial, puede elegirla al especificar el tipo de autenticación.

   ![Elección de una entidad de credencial](../media/choose-credential-entity.png)
 
Hay **cuatro tipos de credenciales** en Metrics Advisor: Cadena de conexión de Azure SQL, Entidad de clave compartida de Azure Data Lake Storage Gen2, Entidad de servicio, Entidad de servicio de Key Vault. Si desea información sobre la configuración de otros tipos de credenciales, consulte las instrucciones siguientes.

## <a name="azure-sql-connection-string"></a>Cadena de conexión de Azure SQL

Debe establecer el **nombre** y la **cadena de conexión** y, luego, seleccione "Crear".

![Establecimiento de entidad de credencial para cadena de conexión de SQL](../media/credential-entity/credential-entity-sql-connection-string.png)

## <a name="azure-data-lake-storage-gen2-shared-key-entity"></a>Entidad de clave compartida de Azure Data Lake Storage Gen2

Debe establecer el **nombre** y la **clave de cuenta** y, luego, seleccione "Crear". La clave de cuenta se podría encontrar en el recurso de la cuenta de Azure Storage (Azure Data Lake Storage Gen2) en **Claves de acceso**.

<!-- 增加basic说明，tips是错的；增加一下怎么管理；加一个step1的link
-->
![Establecimiento de entidad de credencial para lago de datos](../media/credential-entity/credential-entity-data-lake.png)

## <a name="service-principal"></a>Entidad de servicio

Para crear una entidad de servicio para el origen de datos, puede seguir instrucciones detalladas en [Conexión con distintos orígenes de datos](../data-feeds-from-different-sources.md). Después de crear una entidad de servicio, debe rellenar las configuraciones siguientes en la entidad de credencial.

![Entidad de credencial de entidad de servicio](../media/credential-entity/credential-entity-service-principal.png)

* **Nombre:** establezca un nombre para la entidad de credencial de entidad de servicio.
* **Id. de inquilino e id. de cliente:** después de crear una entidad de servicio en Azure Portal, puede encontrar `Tenant ID` y `Client ID` en **Información general**.

    ![Id. de cliente e id. de inquilino de entidad de servicio](../media/credential-entity/sp-client-tenant-id.png)

* **Secreto de cliente:** después de crear una entidad de servicio en Azure Portal, debe ir a **Certificados y secretos** para crear un secreto de cliente, y el **valor** se debe usar como `Client Secret` en la entidad credencial. (Nota: El valor aparece solo una vez, por lo que es mejor que lo guarde en algún lado).


    ![Valor secreto de cliente de entidad de servicio](../media/credential-entity/sp-secret-value.png)

## <a name="span-idsp-from-kvservice-principal-from-key-vaultspan"></a><span id="sp-from-kv">Entidad de servicio de Key Vault</span>

Hay varios pasos para crear una entidad de servicio a partir del almacén de claves.

**Paso 1: Cree una entidad de servicio y concédale acceso a la base de datos.** Puede seguir las instrucciones detalladas que aparecen en [Conexión con distintos orígenes de datos](../data-feeds-from-different-sources.md) en la sección de creación de entidad de servicio para cada origen de datos. 

Después de crear una entidad de servicio en Azure Portal, puede encontrar `Tenant ID` y `Client ID` en **Información general**. El **Id. de directorio (inquilino)** debe ser `Tenant ID` en las configuraciones de entidad de credencial.

![Id. de cliente e id. de inquilino de entidad de servicio](../media/credential-entity/sp-client-tenant-id.png)

**Paso 2: Cree un secreto de cliente.** Debe ir a **Certificados y secretos** para crear un secreto de cliente y el **valor** se usará en los próximos pasos. (Nota: El valor aparece solo una vez, por lo que es mejor que lo guarde en algún lado).

![Valor secreto de cliente de entidad de servicio](../media/credential-entity/sp-secret-value.png)

**Paso 3: Cree un almacén de claves.** En [Azure Portal](https://ms.portal.azure.com/#home), seleccione **Almacenes de claves** para crear uno.

![Creación de un almacén de claves en Azure Portal](../media/credential-entity/create-key-vault.png)

Después de crear un almacén de claves, el **URI de almacén** es `Key Vault Endpoint` en la entidad de credencial de MA (Metrics Advisor).

![Punto de conexión del almacén de claves](../media/credential-entity/key-vault-endpoint.png)

**Paso 4: Cree secretos para Key Vault.** En Azure Portal, genere dos secretos en **Configuración > Secretos** para el almacén de claves.
El primero es para `Service Principal Client Id` y el otro, para `Service Principal Client Secret`. Ambos nombres se usarán en las configuraciones de la entidad de credencial.

![Generación de secretos](../media/credential-entity/generate-secrets.png)

* **Identificador de cliente de la entidad de servicio:** establezca un `Name` para este secreto; este nombre se usará en la configuración de la entidad de credencial y el valor debe ser la entidad de servicio `Client ID` del **paso 1**.

    ![secret1: id. de cliente de entidad de servicio](../media/credential-entity/secret-1-sp-client-id.png)

* **Secreto de cliente de la entidad de servicio:** establezca un `Name` para este secreto; este nombre se usará en la configuración de la entidad de credencial y el valor debe ser la entidad de servicio `Client Secret Value` del **paso 2**.

    ![secret2: id. de cliente de entidad de servicio](../media/credential-entity/secret-2-sp-secret-value.png)

Hasta ahora, el *Id. de cliente* y el *secreto de cliente* de la entidad de servicio se almacenan finalmente en Key Vault. A continuación, debe crear otra entidad de servicio para almacenar el almacén de claves. Por lo tanto, debe **crear dos entidades de servicio**, una para guardar el identificador de cliente y el secreto de cliente, los que se almacenarán en un almacén de claves, y la otra es para almacenar el almacén de claves.

**Paso 5: Cree una entidad de servicio para almacenar el almacén de claves.** 

1. Vaya a [Azure Portal AAD (Azure Active Directory)](https://portal.azure.com/?trace=diagnostics&feature.customportal=false#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) y cree un registro.

    ![Creación de un registro](../media/credential-entity/create-registration.png)

    Después de crear la entidad de servicio, el **identificador de aplicación (cliente)** en la Información general será el `Key Vault Client ID` en la configuración de la entidad de credencial.

2. En **Administrar > Certificados y secretos**, cree un secreto de cliente. Para ello, seleccione "Nuevo secreto de cliente". Luego debe **anotar el valor**, porque solo aparece una vez. El valor es `Key Vault Client Secret` en la configuración de la entidad de credencial.

    ![Incorporación del secreto de cliente](../media/credential-entity/add-client-secret.png)

**Paso 6: Conceda a la entidad de servicio acceso a Key Vault.**  Vaya al recurso de almacén de claves que creó, en **Configuración > Directivas de acceso**, y seleccione "Agregar directiva de acceso" para establecer una conexión entre el almacén de claves y la segunda entidad de servicio del **paso 5** y, luego, seleccione "Guardar".

![Concesión de acceso al almacén de claves a la entidad de servicio](../media/credential-entity/grant-sp-to-kv.png)


## <a name="configurations-conclusion"></a>Conclusión de las configuraciones
Para concluir, las configuraciones de la entidad de credencial en Metrics Advisor para la *entidad de servicio de Key Vault* y la manera de obtenerlas se muestran en la tabla que aparece a continuación:

| Configuración | Cómo obtenerla |
|-------------| ---------------------|
| Punto de conexión de Key Vault | **Paso 3:** URI del almacén de claves. |
| Id. de inquilino | **Paso 1:** Identificador del directorio (inquilino) de la primera entidad de servicio. |
| Id. de cliente del almacén de claves | **Paso 5:** Identificador de la aplicación (cliente) de la segunda entidad de servicio. |
| Secreto de cliente del almacén de claves | **Paso 5:** Valor del secreto de cliente de la segunda entidad de servicio. |
| Nombre del identificador de cliente de la entidad de servicio | **Paso 4:** Nombre del secreto que estableció para el identificador de cliente. |
| Nombre del secreto de cliente de la entidad de servicio | **Paso 4:** El nombre del secreto que estableció para el valor del secreto de cliente. |


## <a name="next-steps"></a>Pasos siguientes

- [Incorporación de los datos](onboard-your-data.md)
- [Conexión con distintos orígenes de datos](../data-feeds-from-different-sources.md)
