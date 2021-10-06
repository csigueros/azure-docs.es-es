---
title: Registro de Cassandra como origen y configuración de los exámenes
description: En este artículo, se explica cómo registrar el servidor de Cassandra en Azure Purview y cómo configurar un análisis.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-map
ms.topic: overview
ms.date: 09/27/2021
ms.openlocfilehash: dcc0ef65654d8100a1c96cdb0c84a6ac758587a2
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129212745"
---
# <a name="register-and-scan-a-cassandra-source-preview"></a>Registro y examen de un origen de Cassandra (versión preliminar)

En este artículo se describe cómo registrar un servidor de Cassandra en Purview y cómo configurar un análisis.

## <a name="supported-capabilities"></a>Funcionalidades admitidas

El origen de Cassandra admite Examen completo para extraer metadatos de una base de datos de Cassandra y captura el linaje entre los recursos de datos.

## <a name="prerequisites"></a>Requisitos previos

1.  Configure la versión más reciente del [entorno de ejecución de integración autohospedado](https://www.microsoft.com/download/details.aspx?id=39717).
    Para obtener más información, consulte [Creación y configuración de un entorno de ejecución de integración autohospedado](../data-factory/create-self-hosted-integration-runtime.md).

2.  Asegúrese de que [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) está instalado en la máquina virtual donde también lo está el entorno de ejecución de integración autohospedado.

3.  Asegúrese de que \"Visual C++ Redistributable 2012 Update 4\" está instalado en la máquina del entorno de ejecución de integración autohospedado. Si aún no lo está, descárguelo desde [aquí](https://www.microsoft.com/download/details.aspx?id=30679).

4.  Las versiones de servidor de Cassandra admitidas son de 3.x a 4.x.

## <a name="register-a-cassandra-server"></a>Registro de un servidor de Cassandra

Para registrar un nuevo servidor de Cassandra en el catálogo de datos, haga lo siguiente:

1.  Vaya a la cuenta de Purview.
2.  Seleccione **Data Map** (Mapa de datos) en el panel de navegación izquierdo.
3.  Seleccione **Registrar.**
4.  En Register sources (Registrar orígenes), seleccione **Cassandra**. Seleccione **Continue** (Continuar).
    :::image type="content" source="media/register-scan-cassandra-source/register-sources.png" alt-text="Registro de un origen de Cassandra" border="true":::
   
En la pantalla Registrar orígenes (Cassandra) (Registrar orígenes [Cassandra]), haga lo siguiente:

1. Escriba un **nombre** con el que se mostrará el origen de datos en el catálogo.

2. Escriba la dirección del servidor donde se ejecuta Cassandra Server en el campo **Host**. Por ejemplo, 20.190.193.10.

3. Escriba el puerto usado por el servidor de Cassandra en el campo **Puerto**.
4. Seleccione una colección o cree una nueva (opcional).

5.  Seleccione **Registrar**.
    :::image type="content" source="media/register-scan-cassandra-source/configure-sources.png" alt-text="Configuración del origen de Cassandra" border="true":::

## <a name="creating-and-running-a-scan"></a>Creación y ejecución de un examen

Para crear y ejecutar un nuevo examen, siga estos pasos:

1.  En el centro de administración, seleccione Entornos de ejecución de integración. Asegúrese de que está configurado un entorno de ejecución de integración autohospedado. Si no lo está, use los pasos que se indican [aquí](./manage-integration-runtimes.md) para configurar un entorno de ejecución de integración autohospedado.

2.  Vaya a **Sources** (Orígenes).

3.  Seleccione el servidor de **Cassandra** registrado.

4.  Seleccione **+ New scan** (+ Nuevo examen).

5.  Especifique los detalles siguientes:

    a.  **Name** (Nombre): el nombre del examen.

    b.  **Connect via integration runtime** (Conectar mediante IR): seleccione el entorno de ejecución de integración configurado.

    c.  **Credencial**: al configurar las credenciales de Cassandra, asegúrese de:

    - Seleccionar **Basic Authentication** (Autenticación básica) como método de autenticación
    - Proporcione el nombre de usuario en nombre de la conexión que se va a realizar en el campo Nombre de usuario. 
    - Guarde la contraseña del usuario de Cassandra en cuyo nombre se realiza la conexión en el secreto del almacén de claves.

    Para más información sobre credenciales, vea el vínculo que se indica [aquí](manage-credentials.md).

    d.  **Espacios de claves**: especifique una lista de espacios de claves de Cassandra que se van a importar. Varios espacios de claves deben estar separados por punto y coma. Por ejemplo, keyspace1; keyspace2. Si la lista está vacía, se importan todos los espacios de claves disponibles.
    Los patrones de nombres de espacios de claves aceptables que usan la sintaxis de expresiones SQL LIKE incluyen el uso de %, 

    por ejemplo, A%; %B; %C%; D
    - empieza por A o
    - termina en B o
    - contiene C o
    - igual a D    
No se acepta el empleo de NOT ni de caracteres especiales.
    
    f. **Use Secure Sockets Layer (SSL)** (Usar Capa de sockets seguros [SSL]): seleccione True o False para notificar si se debe usar Capa de sockets seguros (SSL) al conectarse al servidor de Cassandra. De manera predeterminada, este valor se establece en False.

    g. **Maximum memory available** (Memoria máxima disponible): memoria máxima (en GB) disponible en la máquina virtual del cliente que van a usar los procesos de examen. Depende del tamaño del servidor de Cassandra que se va a examinar.
        :::image type="content" source="media/register-scan-cassandra-source/scan.png" alt-text="Examinar el origen de Cassandra" border="true":::

6.  Seleccione **Test Connection** (Probar conexión).

7.  Seleccione **Continuar**.

8.  Elija el **desencadenador del examen**. Puede configurar una programación o ejecutar el examen una vez.

9.  Revise el examen y seleccione **Save and run** (Guardar y ejecutar).

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