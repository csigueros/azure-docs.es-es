---
title: Registro de exámenes de configuración y proyectos de Google BigQuery en Azure Purview
description: En este artículo se describe cómo registrar un proyecto de Google BigQuery en Azure Purview y cómo configurar un análisis.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-map
ms.topic: overview
ms.date: 09/27/2021
ms.openlocfilehash: caaf78e14669d67f525e5756efd8e2fd301f9b38
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129218551"
---
# <a name="register-and-scan-google-bigquery-source-preview"></a>Registro y examen del origen de Google BigQuery (versión preliminar)

En este artículo se describe cómo registrar un proyecto de Google BigQuery en Purview y cómo configurar un análisis.

## <a name="supported-capabilities"></a>Funcionalidades admitidas

El origen de BigQuery admite un examen completo para extraer metadatos de un proyecto de BigQuery y captura el linaje entre recursos de datos.

## <a name="prerequisites"></a>Requisitos previos

1.  Configure la versión más reciente del [entorno de ejecución de integración autohospedado](https://www.microsoft.com/download/details.aspx?id=39717).
    Para obtener más información, consulte [Creación y configuración de un entorno de ejecución de integración autohospedado](../data-factory/create-self-hosted-integration-runtime.md).

2.  Asegúrese de que [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) está instalado en la máquina virtual donde también lo está el entorno de ejecución de integración autohospedado.

3.  Asegúrese de que \"Visual C++ Redistributable 2012 Update 4\" está instalado en la máquina del entorno de ejecución de integración autohospedado. Si aún no lo está, descárguelo desde [aquí](https://www.microsoft.com/download/details.aspx?id=30679).

4.  Tendrá que descargar manualmente de [aquí](https://cloud.google.com/bigquery/providers/simba-drivers) el controlador JDBC de BigQuery en la máquina virtual en que se ejecute el entorno de ejecución de integración autohospedado.

    > [!Note]
    > Todas las cuentas de la máquina virtual deben poder acceder al controlador. No lo instale en una cuenta de usuario.

5.  La versión compatible de Google BigQuery es la 11.0.0

## <a name="register-a-google-bigquery-project"></a>Registro de un proyecto de Google BigQuery

Para registrar un nuevo proyecto de Google BigQuery en el catálogo de datos, haga lo siguiente:

1.  Vaya a la cuenta de Purview.
2.  Seleccione **Data Map** (Mapa de datos) en el panel de navegación izquierdo.
3.  Seleccione **Registrar.**
4.  En Register sources (Registrar orígenes), seleccione **Google BigQuery**. Seleccione **Continue** (Continuar).
    :::image type="content" source="media/register-scan-google-bigquery-source/register-sources.png" alt-text="Registro de un origen de BigQuery" border="true":::
   
En la pantalla Register sources (Registrar orígenes) (Google BigQuery), siga estos pasos:

1.  Escriba en **Name** (Nombre), el nombre con el que el origen de datos se va a mostrar en el catálogo.

2.  Escriba el valor de **ProjectID**. Debe ser un identificador de proyecto completo. Por ejemplo, mydomain.com:myProject

3.  Seleccione una colección o cree una nueva (opcional).

4.  Seleccione **Registrar**.
    :::image type="content" source="media/register-scan-google-bigquery-source/configure-sources.png" alt-text="Configurar el origen de BigQuery" border="true":::

## <a name="creating-and-running-a-scan"></a>Creación y ejecución de un examen

Para crear y ejecutar un nuevo examen, siga estos pasos:

1.  En el centro de administración, seleccione Entornos de ejecución de integración. Asegúrese de que está configurado un entorno de ejecución de integración autohospedado. Si no lo está, use los pasos que se indican [aquí](./manage-integration-runtimes.md) para configurar un entorno de ejecución de integración autohospedado.

2.  Vaya a **Sources** (Orígenes).

3.  Seleccione el proyecto de **BigQuery** registrado.

4.  Seleccione **+ New scan** (+ Nuevo examen).

5.  Especifique los detalles siguientes:

    a.  **Name** (Nombre): el nombre del examen.

    b.  **Connect via integration runtime** (Conectar mediante IR): seleccione el entorno de ejecución de integración configurado.

    c.  **Credencial**: al configurar las credenciales de BigQuery, asegúrese de:

    - Seleccionar **Basic Authentication** (Autenticación básica) como método de autenticación
    - Especificar el identificador de correo electrónico de la cuenta de servicio en el campo User name (Nombre de usuario). Por ejemplo, xyz\@developer.gserviceaccount.com.
    - Guarde el archivo de clave privada de la cuenta de servicio en formato JSON en el secreto del almacén de claves.

    Para crear una clave privada desde la plataforma en la nube de Google, en el menú de navegación, seleccione IAM & Admin (IAM y admin.) -\> Service Accounts (Cuentas de servicio) -\> Select a project (Seleccionar un proyecto) -\> Seleccione en la dirección de correo electrónico de la cuenta de servicio para la que desea crear una clave -\> Seleccione en la pestaña **Keys** (Claves) -\> Seleccione el menú desplegable **Add key** (Agregar clave) y seleccione Create new key (Crear clave). Ahora elija el formato JSON.

      > [!Note]
      > El contenido de la clave privada se guarda en un archivo temporal en la máquina virtual cuando se ejecutan los procesos de examen. Este archivo temporal se elimina después de que los exámenes se hayan completado correctamente. Si se produce un error en cualquiera de ellos, el sistema seguirá reintentando la operación hasta que se ejecute correctamente. Asegúrese de que el acceso está restringido adecuadamente en la máquina virtual en la que se ejecuta SHIR.**

    Para más información sobre credenciales, vea el vínculo que se indica [aquí](manage-credentials.md).

    d.  **Driver location** (Ubicación del controlador): especifique la ruta de acceso a la ubicación del controlador JDBC en la máquina virtual donde se ejecuta el entorno de ejecución de integración autohospedado. Debe ser la ruta de acceso a la ubicación válida de la carpeta JAR.    
    > [!Note]
    > Todas las cuentas de la máquina virtual deben poder acceder al controlador. No lo instale en una cuenta de usuario.

    e.  **Conjunto de datos**: especifique una lista de conjuntos de datos de BigQuery que se importarán. Por ejemplo, dataset1; dataset2. Si la lista está vacía, se importan todos los conjuntos de datos disponibles.
        Los patrones de nombres de conjunto de datos aceptables que usan la sintaxis de expresiones SQL LIKE incluyen el uso de %, 

    por ejemplo, A%; %B; %C%; D
    - empieza por A o
    - termina en B o
    - contiene C o
    - igual a D    
No se acepta el empleo de NOT ni de caracteres especiales.
    
    f.  **Maximum memory available** (Memoria máxima disponible): memoria máxima (en GB) disponible en la máquina virtual del cliente que van a usar los procesos de examen. Depende del tamaño del proyecto de Google BigQuery que se va a examinar.
        :::image type="content" source="media/register-scan-google-bigquery-source/scan.png" alt-text="Examinar origen de BigQuery" border="true":::

6.  Seleccione **Test Connection** (Probar conexión).

7.  Seleccione **Continuar**.

8.  Elija el **desencadenador del examen**. Puede configurar una programación o ejecutar el examen una vez.

9.  Revise el examen y seleccione **Guardar y ejecutar**.

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