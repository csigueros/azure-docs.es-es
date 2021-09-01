---
title: Cifrado del servicio Metrics Advisor
titleSuffix: Azure Cognitive Services
description: Cifrado de datos en reposo del servicio Metrics Advisor.
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 07/02/2021
ms.author: mbullwin
ms.openlocfilehash: 03f6a30bbe857b59c3fa8a6fa668bdac7cb681b1
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114342305"
---
# <a name="metrics-advisor-service-encryption-of-data-at-rest"></a>Cifrado de datos en reposo del servicio Metrics Advisor

El servicio Metrics Advisor cifra automáticamente los datos al guardarlos en la nube. El cifrado del servicio Metrics Advisor protege los datos y le ayuda a satisfacer los requisitos de cumplimiento y de seguridad de la organización.

[!INCLUDE [cognitive-services-about-encryption](../../cognitive-services/includes/cognitive-services-about-encryption.md)]

Metrics Advisor admite CMK y el cifrado doble mediante BYOS (traiga su propio almacenamiento). 

## <a name="steps-to-create-a-metrics-advisor-with-byos"></a>Pasos para crear una instancia de Metrics Advisor con BYOS

### <a name="step1-create-an-azure-database-for-postgresql-and-set-admin"></a>Paso 1. Creación de una instancia de Azure Database for PostgreSQL y establecimiento del administrador

- Creación de una instancia de Azure Database for PostgreSQL

    Inicie sesión en Azure Portal y cree un recurso de Azure Database for PostgreSQL. Un par de cosas que hay que tener en cuenta:

    1. Seleccione la opción de implementación de **servidor único**. 
    2. Al elegir "Origen de datos", especifique **"Ninguno"** .
    3. En el caso de la "Ubicación", asegúrese de crearlo en la **misma ubicación** que el recurso de Metrics Advisor.
    4. La versión debe establecerse en la **11**. 
    5. En "Proceso y almacenamiento" debe elegir una SKU "Optimizada para memoria" con al menos **32 núcleos virtuales**.
    
    ![Creación de una instancia de Azure Database for PostgreSQL](media/cmk-create.png)

- Establecimiento del administrador de Active Directory para la nueva instancia de PG

    Después de crear correctamente la instancia de Azure Database for PostgreSQL (PG). Vaya a la página de recursos del recurso de Azure PG recién creado. Seleccione la pestaña "Administrador de Active Directory" y establézcase usted mismo como administrador.


### <a name="step2-create-a-metrics-advisor-resource-and-enable-managed-identity"></a>Paso 2: Creación de un recurso de Metrics Advisor y habilitación de la identidad administrada

- Cree un recurso de Metrics Advisor en Azure Portal.

    Vaya a Azure Portal de nuevo y busque "Metrics Advisor". Al crear Metrics Advisor, recuerde lo siguiente:

    1. Elija la **misma "región"** en la que creó la instancia de Azure Database for PostgreSQL. 
    2. Marque "Traiga su propio almacenamiento" como **"Sí"** y seleccione la instancia de Azure Database for PostgreSQL que acaba de crear en la lista desplegable.

- Habilite la identidad administrada para Metrics Advisor.

    Después de crear el recurso de Metrics Advisor, seleccione "Identidad" y establezca "Estado" en **"Activo"** para habilitar la identidad administrada.

- Obtenga el identificador de aplicación de la identidad administrada.

    Vaya a Azure Active Directory y seleccione "Aplicaciones empresariales". Cambie "Tipo de aplicación" a **"Identidad administrada"** , copie el nombre del recurso de Metrics Advisor y busque. A continuación, puede ver el "identificador de aplicación" en el resultado de la consulta y copiarlo.

### <a name="step3-grant-metrics-advisor-access-permission-to-your-azure-database-for-postgresql"></a>Paso 3. Concesión del permiso de acceso para Metrics Advisor a la instancia de Azure Database for PostgreSQL

- Conceda el rol **"Propietario"** para la identidad administrada de la instancia de Azure Database for PostgreSQL

- Establecimiento de reglas de firewall

    1. Establezca Permitir el acceso a servicios de Azure en "Sí". 
    2. Agregue la dirección clientIP para iniciar sesión en Azure Database for PostgreSQL.

- Obtenga el token de acceso de la cuenta con el tipo de recurso "https://ossrdbms-aad.database.windows.net". El token de acceso es la contraseña que necesita para iniciar sesión en Azure Database for PostgreSQL mediante su cuenta. Este es un ejemplo que usa el cliente `az`:

   ```
   az login
   az account get-access-token --resource https://ossrdbms-aad.database.windows.net
   ```

- Después de obtener el token, úselo para iniciar sesión en la instancia de Azure Database for PostgreSQL. Reemplace "servername" por el que puede encontrar en la "información general" de la instancia de Azure Database for PostgreSQL.

   ```
   export PGPASSWORD=<access-token>
   psql -h <servername> -U <adminaccount@servername> -d postgres
   ```

- Después del inicio de sesión, ejecute los siguientes comandos para conceder a Metrics Advisor permiso de acceso a Azure Database for PostgreSQL. Reemplace el "appid" por el que obtiene en el paso 2.

   ```
   SET aad_validate_oids_in_tenant = off;
   CREATE ROLE metricsadvisor WITH LOGIN PASSWORD '<appid>' IN ROLE azure_ad_user;
   ALTER ROLE metricsadvisor CREATEDB;
   GRANT azure_pg_admin TO metricsadvisor;
   ```

Al completar todos los pasos anteriores, ha creado correctamente un recurso de Metrics Advisor compatible con CMK. Espere un par de minutos hasta que Metrics Advisor sea accesible.

## <a name="next-steps"></a>Pasos siguientes

* [Formulario de solicitud de claves administradas por el cliente del servicio Metrics Advisor](https://aka.ms/cogsvc-cmk)
* [Más información sobre Azure Key Vault](../../key-vault/general/overview.md)