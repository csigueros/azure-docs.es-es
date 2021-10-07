---
title: Registro de Looker y configuración de los análisis en Azure Purview
description: En este artículo se describe cómo registrar un origen de Looker en Azure Purview y cómo configurar un análisis.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-map
ms.topic: overview
ms.date: 09/27/2021
ms.openlocfilehash: 1f39a1d261b4c8ed4223d66ebb34c94be7310b2e
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129214792"
---
# <a name="register-and-scan-looker-preview"></a>Registro y examen de Looker (versión preliminar)

En este artículo se describe cómo registrar un servidor de Looker en Purview y cómo configurar un análisis.

## <a name="supported-capabilities"></a>Funcionalidades admitidas

El origen de Looker admite el análisis completo para extraer metadatos de un servidor de Looker. Importó metadatos de un servidor de Looker, incluidas las conexiones de base de datos, los modelos LookML y los informes asociados (aspectos y paneles). Este origen de datos también captura el linaje entre los recursos de datos.

> [!Note]
> Looker como origen se admite actualmente en la versión preliminar privada. Registre este origen y configure los análisis en las cuentas de Purview que no son de producción y proporcione sus comentarios.

## <a name="prerequisites"></a>Requisitos previos

1.  Configure la versión más reciente del [entorno de ejecución de integración autohospedado](https://www.microsoft.com/download/details.aspx?id=39717).
    Para obtener más información, consulte [Creación y configuración de un entorno de ejecución de integración autohospedado](../data-factory/create-self-hosted-integration-runtime.md).

2.  Asegúrese de que [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) está instalado en la máquina virtual donde también lo está el entorno de ejecución de integración autohospedado.

3.  Asegúrese de que \"Visual C++ Redistributable 2012 Update 4\" esté instalado en la máquina virtual del entorno de ejecución de integración autohospedado. Si no está instalado, descárguelo [aquí](https://www.microsoft.com/download/details.aspx?id=30679).

4.  La versión compatible del servidor de Looker es la versión 7.2

## <a name="setting-up-authentication-for-a-scan"></a>Configuración de la autenticación para un examen

Se necesita una clave API3 para conectarse al servidor de Looker. La clave API3 consta de un client_id público y un client_secret privado y sigue un patrón de autenticación de OAuth2.

## <a name="register-a-looker-server"></a>Registro de un servidor de Looker

Para registrar un nuevo servidor de Looker en el catálogo de datos, haga lo siguiente:

1. Vaya a la cuenta de Purview.
2. Seleccione **Data Map** (Mapa de datos) en el panel de navegación izquierdo.
3. Seleccione **Registrar.**
4. En Register sources (Registrar orígenes), seleccione **Looker**. Seleccione **Continue** (Continuar).
    :::image type="content" source="media/register-scan-looker-source/register-sources.png" alt-text="registro de origen de Looker" border="true":::


En la pantalla Register sources (Registrar orígenes) (Looker), haga lo siguiente:

1. Escriba un **nombre** con el que se mostrará el origen de datos en el catálogo.

2. Escriba la dirección URL de la API de Looker en el campo **Dirección URL de la API del servidor**. El puerto predeterminado para las solicitudes de API es el puerto 19999. Además, todos los puntos de conexión de la API de Looker requieren una conexión HTTPS. Por ejemplo, "https://azurepurview.cloud.looker.com".

3. Seleccione una colección o cree una nueva (opcional).

4. Seleccione Finish (Finalizar) para registrar el origen de datos.

    :::image type="content" source="media/register-scan-looker-source/scan-source.png" alt-text="origen de Looker de análisis" border="true":::

## <a name="creating-and-running-a-scan"></a>Creación y ejecución de un examen

Para crear y ejecutar un nuevo examen, siga estos pasos:

1. En el centro de administración, seleccione Entornos de ejecución de integración. Si no lo está, use los pasos que se indican [aquí](./manage-integration-runtimes.md) para configurar un entorno de ejecución de integración autohospedado.

2. Vaya a **Sources** (Orígenes).

3. Seleccione el servidor de **Looker** registrado.

4. Seleccione **+ New scan** (+ Nuevo examen).

5. Especifique los detalles siguientes:

    a.  **Name** (Nombre): el nombre del examen.

    b.  **Connect via integration runtime** (Conectar mediante el entorno de ejecución de integración): seleccione el entorno de ejecución de integración autohospedado configurado.

    c.  El campo **Dirección URL de la API del servidor** se rellena automáticamente en función del valor especificado durante el registro.

    d.  **Credencial:** al configurar las credenciales de Looker, asegúrese de hacer lo siguiente:

    - Seleccione **Autenticación básica** como método de autenticación.
    - Proporcione el id. de cliente de la clave API3 de Looker en el campo Nombre de usuario.
    - Guarde el secreto de cliente de la clave API3 de Looker en el secreto del almacén de claves.

    **Nota:** Para acceder al id. de cliente y al secreto de cliente, vaya a Looker -\>Administrador -\> Usuarios -\> Seleccione **Editar** en un usuario -\> Seleccione **Editar claves** -\> Use el id. de cliente y el secreto de cliente o cree uno nuevo.
    :::image type="content" source="media/register-scan-looker-source/looker-details.png" alt-text="obtener detalles de Looker" border="true":::
    

    Para obtener más información sobre credenciales, vea el vínculo que se indica [aquí](manage-credentials.md).

    e.  **Filtro del proyecto**: el ámbito del análisis que se proporciona mediante una lista de proyectos de Looker separados por puntos y comas. Esta opción se usa para seleccionar los aspectos y los paneles por su proyecto principal.

    f.  **Maximum memory available** (Memoria máxima disponible): memoria máxima (en GB) disponible en la máquina virtual del cliente que van a usar los procesos de examen. Depende del tamaño de la instancia de Erwin Mart que se va a examinar.

    :::image type="content" source="media/register-scan-looker-source/setup-scan.png" alt-text="Desencadenar examen" border="true":::

6. Seleccione **Test Connection** (Probar conexión).

7. Seleccione **Continuar**.

8. Elija el **desencadenador del examen**. Puede configurar una programación o ejecutar el examen una vez.

9. Revise el examen y seleccione **Save and run** (Guardar y ejecutar).

## <a name="viewing-your-scans-and-scan-runs"></a>Visualización de los exámenes y las ejecuciones de exámenes

1. Vaya al centro de administración. Seleccione **Data sources** (Orígenes de datos) en la sección **Sources and scanning** (Orígenes y examen).

2. Seleccione el origen de datos que desee. Verá una lista de los exámenes existentes en ese origen de datos.

3. Seleccione el examen cuyos resultados le interesa ver.

4. En esta página se muestran todas las ejecuciones de exámenes anteriores junto con las métricas y el estado de cada ejecución del examen. También mostrará si el análisis se ha programado o es manual, a cuántos recursos se han aplicado clasificaciones, cuántos recursos totales se han detectado, la hora de inicio y finalización del examen y la duración total del examen.

## <a name="manage-your-scans"></a>Administración de exámenes

Para administrar o eliminar un examen, haga lo siguiente:

1. Vaya al centro de administración. Seleccione **Data sources** (Orígenes de datos) en la sección **Sources and scanning** (Orígenes y examen) y, a continuación, seleccione el origen de datos deseado.

2. Seleccione el examen que desea administrar. Seleccione **Edit** (Editar) para editar el examen.

3. Seleccione **Delete** (Eliminar) para eliminar el examen.

## <a name="next-steps"></a>Pasos siguientes

- [Examen del catálogo de datos de Azure Purview](how-to-browse-catalog.md)
- [Búsqueda en el catálogo de datos de Azure Purview](how-to-search-catalog.md)
