---
title: Registro y examen de un origen de Cassandra
description: En este artículo, se describe cómo registrar un servidor de Cassandra en Azure Purview y configurar un examen para extraer metadatos.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-map
ms.topic: overview
ms.date: 09/27/2021
ms.openlocfilehash: 5f8feffebff71c25f2a0d62d775894e7c4431615
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130005323"
---
# <a name="register-and-scan-a-cassandra-source-preview"></a>Registro y examen de un origen de Cassandra (versión preliminar)

En este artículo, se describe cómo registrar un servidor de Cassandra en Azure Purview y configurar un examen.

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Puede usar Purview para realizar exámenes completos en Cassandra a fin de extraer metadatos y linaje entre recursos de datos. 

## <a name="prerequisites"></a>Requisitos previos

1.  Configure la versión más reciente del [entorno de ejecución de integración autohospedado](https://www.microsoft.com/download/details.aspx?id=39717).
    Para obtener más información, consulte [Creación y configuración de un entorno de ejecución de integración autohospedado](../data-factory/create-self-hosted-integration-runtime.md).

2.  Asegúrese de que [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) esté instalado en la máquina virtual donde también lo esté el entorno de ejecución de integración autohospedado.

3.  Asegúrese de que Visual C++ Redistributable para Visual Studio 2012 Update 4 esté instalado en la máquina del entorno de ejecución de integración autohospedado. Si no tiene instalada esta actualización, [descárguela aquí](https://www.microsoft.com/download/details.aspx?id=30679).

4.  Asegúrese de que el servidor de Cassandra sea la versión 3.*x* o 4.*x*.

## <a name="register-a-cassandra-server"></a>Registro de un servidor de Cassandra

Para registrar un nuevo servidor de Cassandra en el catálogo de datos, haga lo siguiente:

1.  Vaya a la cuenta de Purview.
2.  Seleccione **Mapa de datos** en el panel izquierdo.
3.  Seleccione **Registrar**.
4.  En la pantalla **Register sources** (Registrar orígenes), seleccione **Cassandra** y, luego, elija **Continuar**:

    :::image type="content" source="media/register-scan-cassandra-source/register-sources.png" alt-text="Captura de pantalla que muestra la pantalla Register sources (Registrar orígenes)." border="true":::
   
1. En la pantalla **Register sources (Cassandra)** (Registrar orígenes [Cassandra]), haga lo siguiente:

   1. Escriba un **nombre**. El origen de datos usará este nombre en el catálogo.

   2. En el cuadro **Host**, escriba la dirección del servidor donde se ejecuta el servidor de Cassandra. Por ejemplo, 20.190.193.10.

   3. En el cuadro **Puerto**, escriba el puerto que usa el servidor de Cassandra.
   4. Seleccione una colección o cree una (opcional).
    :::image type="content" source="media/register-scan-cassandra-source/configure-sources.png" alt-text="Captura de la pantalla Register sources (Cassandra) (Registrar orígenes [Cassandra])." border="true":::
   5.  Seleccione **Registrar**.


## <a name="create-and-run-a-scan"></a>Creación y ejecución de un trabajo

Para crear y ejecutar un nuevo examen, siga estos pasos:

1.  En el centro de administración, seleccione **Entornos de ejecución de integración**. Asegúrese de que está configurado un entorno de ejecución de integración autohospedado. Si no tiene uno configurado, lleve a cabo [estos pasos para configurar un entorno de ejecución de integración autohospedado](./manage-integration-runtimes.md).
    

2.  Vaya a **Orígenes**.

3.  Seleccione el servidor de Cassandra registrado.

4.  Seleccione **New scan** (Nuevo examen).

5.  Proporcione los detalles siguientes:

    a.  **Nombre**: especifique un nombre para el examen.

    b.  **Connect via integration runtime** (Conectar mediante el entorno de ejecución de integración): seleccione el entorno de ejecución de integración autohospedado configurado.

    c.  **Credencial**: al configurar las credenciales de Cassandra, asegúrese de realizar las siguientes acciones:

    - Seleccione **Autenticación básica** como método de autenticación.
    - En el cuadro **Nombre de usuario**, proporcione el nombre del usuario con el que va a realizar la conexión. 
    - En el secreto del almacén de claves, guarde la contraseña del usuario de Cassandra con la que va a realizar la conexión.

    Para más información, consulte [Credenciales para la autenticación de origen en Purview](manage-credentials.md).

    d.  **Espacios de claves**: especifique una lista de espacios de claves de Cassandra que se van a importar. Si hay varios espacios de claves, se deben separar con punto y coma. Por ejemplo, keyspace1; keyspace2. Si la lista está vacía, se importan todos los espacios de claves disponibles.
    
    Puede usar patrones de nombres de espacio de claves que emplean la sintaxis de la expresión LIKE de SQL, como %. 

    Por ejemplo, A%; %B; %C%; D

    Esta expresión significa algo de lo siguiente:
    - Comienza por A
    - Termina en B
    - Contiene C
    - Es igual a D    

    No se puede usar NOT ni caracteres especiales.
    
    e. **Use Secure Sockets Layer (SSL)** (Usar Capa de sockets seguros [SSL]): seleccione **True** o **False** para notificar si se debe usar Capa de sockets seguros (SSL) al conectarse al servidor de Cassandra. De forma predeterminada, el valor de esta opción es **False**.

    f. **Maximum memory available** (Memoria máxima disponible): memoria máxima (en GB) disponible en la máquina virtual del cliente que se va a usar en los procesos de examen. Este valor depende del tamaño del servidor de Cassandra que se va a examinar.
        :::image type="content" source="media/register-scan-cassandra-source/scan.png" alt-text="Examinar el origen de Cassandra" border="true":::

6.  Seleccione **Test Connection** (Probar conexión).

7.  Seleccione **Continuar**.

8.  Seleccione un **desencadenador de examen**. Puede configurar una programación o ejecutar el examen una vez.

9.  Revise el examen y, luego, seleccione **Guardar y ejecutar**.

## <a name="view-your-scans-and-scan-runs"></a>Visualización de los exámenes y las ejecuciones de exámenes

1. Vaya al Centro de administración. Seleccione **Orígenes de datos** en la sección **Sources and scanning** (Orígenes y examen).

2. Seleccione el origen de datos cuyos exámenes quiere ver. Verá una lista de los exámenes existentes en ese origen de datos.

3. Seleccione aquel cuyos resultados quiere ver.

   La página resultante mostrará todas las ejecuciones de examen anteriores junto con las métricas y el estado de cada una. 
   También indicará lo siguiente: 
   - Si el examen se programó o fue manual. 
   - Número de recursos a los que se aplicaron clasificaciones. 
   - Cuántos recursos totales se detectaron. 
   - Hora de inicio y finalización del examen.
   - Duración del examen.

## <a name="manage-your-scans"></a>Administración de exámenes

Para administrar o eliminar un examen, haga lo siguiente:

1. Vaya al Centro de administración. Seleccione **Orígenes de datos** en la sección **Sources and scanning** (Orígenes y examen). A continuación, seleccione el origen de datos cuyo examen quiere administrar.

2. Seleccione el examen que quiere administrar. 
   - Seleccione **Edit** (Editar) para editar el examen.

   - Puede eliminar el examen si selecciona **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

- [Examen del catálogo de datos de Azure Purview](how-to-browse-catalog.md)
- [Búsqueda en el catálogo de datos de Azure Purview](how-to-search-catalog.md)
