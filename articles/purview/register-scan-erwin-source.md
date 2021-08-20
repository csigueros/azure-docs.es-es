---
title: Registro de Erwin Mart y configuración de los exámenes en Azure Purview
description: En este artículo, se explica cómo registrar Erwin Mart en Azure Purview y cómo configurar un examen.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 7/16/2021
ms.openlocfilehash: 55ec355cf28f08618b1c51670b876f93dee69cc8
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114393744"
---
# <a name="register-and-scan-erwin-mart-server-preview"></a>Registro de un servidor de Erwin Mart y configuración de un examen (versión preliminar)

En este artículo, se describe cómo registrar un servidor de Erwin Mart en Purview y cómo configurar un examen.

## <a name="supported-capabilities"></a>Funcionalidades admitidas

El origen de Erwin admite el examen completo para extraer los metadatos de un servidor de Erwin Mart. Los metadatos incluyen:

1.  Solo modelos lógicos con entidades, atributos y dominios, o bien
2.  Solo modelos físicos con tablas, columnas y tipos de datos, o bien
3.  Modelos lógicos y físicos

Este origen de datos también captura el linaje entre los recursos de datos.

> [!Note]
> Erwin como origen se admite actualmente en versión preliminar. Registre este origen y configure los exámenes en las cuentas de Purview que no son de producción y proporcione sus comentarios.

## <a name="prerequisites"></a>Requisitos previos

1.  Configure la versión más reciente del [entorno de ejecución de integración autohospedado](https://www.microsoft.com/download/details.aspx?id=39717).
    Para obtener más información, vea  
    [Creación y configuración de un entorno de ejecución de integración autohospedado](../data-factory/create-self-hosted-integration-runtime.md).

    > [!Note]
    > Asegúrese de ejecutar el entorno de ejecución de integración autohospedado en la máquina virtual en la que se ejecuta la instancia de Erwin Mart.

2.  Asegúrese de que [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) está instalado en la máquina virtual donde también lo está el entorno de ejecución de integración autohospedado.

3.  Asegúrese de que \"Visual C++ Redistributable 2012 Update 4\" esté instalado en la máquina virtual del entorno de ejecución de integración autohospedado. Si no está instalado, descárguelo [aquí](https://www.microsoft.com/download/details.aspx?id=30679).

4.  Las versiones admitidas de Erwin Mart son de la 9.x a la 2021.

## <a name="setting-up-authentication-for-a-scan"></a>Configuración de la autenticación para un examen

La única autenticación admitida para un origen de Erwin Mart es la **autenticación de servidor** en forma de nombre de usuario y contraseña.

## <a name="register-an-erwin-mart"></a>Registro de una instancia de Erwin Mart

Para registrar una nueva instancia de Erwin Mart en el catálogo de datos, haga lo siguiente:

1.  Vaya a la cuenta de Purview.
2.  Seleccione **Sources** (Orígenes) en el panel de navegación izquierdo.
3.  Seleccione **Registrar.**
4.  En Register sources (Registrar orígenes), seleccione **Erwin**. Seleccione **Continue** (Continuar).
    :::image type="content" source="media/register-scan-erwin-source/register-sources.png" alt-text="Registro de un origen de Erwin" border="true":::
    
En la pantalla Register sources (Erwin) (Registrar orígenes [Erwin]), haga lo siguiente:

1.  Escriba un **Name** (Nombre) con el que se muestre el origen de datos en el catálogo.

2.  Escriba el **nombre del servidor** de Erwin Mart. Este es el nombre de host de red que se usa para conectarse al servidor de Erwin Mart. Por ejemplo, localhost.

3.  Escriba el número de puerto que se usa al conectarse a Erwin Mart en el campo **Port** (Puerto). De manera predeterminada, este valor es 18170.

4.  Escriba un valor para **Application name** (Nombre de la aplicación).

    >[!Note]
    > Para encontrar los detalles anteriores, vaya a al modelador de datos de Erwin. Haga clic en Mart -\> Connect (Conectar) para ver los detalles relacionados con el nombre del servidor, el puerto y el nombre de la aplicación.

    :::image type="content" source="media/register-scan-erwin-source/erwin-details.png" alt-text="Buscar los detalles de Erwin" border="true":::
    
5.  Seleccione una colección o cree una nueva (opcional).

6.  Seleccione Finish (Finalizar) para registrar el origen de datos.

    :::image type="content" source="media/register-scan-erwin-source/register-erwin.png" alt-text="Registrar origen" border="true":::
    

## <a name="creating-and-running-a-scan"></a>Creación y ejecución de un examen

Para crear y ejecutar un nuevo examen, siga estos pasos:

1.  En el centro de administración, haga clic en Integration runtimes (Entornos de ejecución de integración). Asegúrese de que se haya configurado un entorno de ejecución de integración autohospedado en la máquina virtual en la que se ejecuta la instancia de Erwin Mart. Si no lo está, use los pasos que se indican [aquí](./manage-integration-runtimes.md) para configurar un entorno de ejecución de integración autohospedado.

2.  Vaya a **Sources** (Orígenes).

3.  Seleccione la instancia de **Erwin** registrada.

4.  Seleccione **+ New scan** (+ Nuevo examen).

5.  Especifique los detalles siguientes:

    a.  **Name** (Nombre): el nombre del examen.

    b.  **Connect via integration runtime** (Conectar mediante el entorno de ejecución de integración): seleccione el entorno de ejecución de integración autohospedado configurado.

    c.  El **nombre del servidor, el puerto** y el **nombre de la aplicación** se rellenan automáticamente en función de los valores especificados durante el registro.

    d.  **Credential** (Credenciales): seleccione las credenciales configuradas para conectarse al servidor de Erwin Mart. Al crear una credencial, asegúrese de lo siguiente:
    - Seleccione **Basic Authentication** (Autenticación básica) como método de autenticación.
    - Proporcione el nombre de usuario del servidor de Erwin Mart en el campo User name (Nombre de usuario).
    - Guarde la contraseña de usuario para la autenticación de servidor en un secreto del almacén de claves.

    Para más información sobre credenciales, consulte el vínculo que se indica [aquí](manage-credentials.md).

    e.  **Use Internet Information Services (IIS)** (Usar Internet Information Services [IIS]): seleccione True o False para notificar si se debe usar Microsoft Internet Information Services (IIS) al conectarse al servidor de Erwin Mart. De manera predeterminada, este valor se establece en False.

    f.  **Use Secure Sockets Layer (SSL)** (Usar Capa de sockets seguros [SSL]): seleccione True o False para notificar si se debe usar Capa de sockets seguros (SSL) al conectarse al servidor de Erwin Mart. De manera predeterminada, este valor se establece en False.

    > [!Note]
    > Este parámetro solo es aplicable a la versión 9.1 o posterior de Erwin Mart.

    g.  **Browse mode** (Modo de exploración): seleccione el modo para explorar Erwin Mart. Las opciones posibles son "Libraries and Models" (Bibliotecas y modelos) o "Libraries only" (Solo bibliotecas).

    h.  **Models** (Modelos): ámbito del examen, que se proporciona mediante una lista separada por puntos y comas de cadenas de localizador de modelo. Por ejemplo, mart://Mart/Samples/eMovies;mart://Mart/Erwin_Tutorial/AP_Physical

    i.  **Maximum memory available** (Memoria máxima disponible): memoria máxima (en GB) disponible en la máquina virtual del cliente que van a usar los procesos de examen. Depende del tamaño de la instancia de Erwin Mart que se va a examinar.
    :::image type="content" source="media/register-scan-erwin-source/setup-scan.png" alt-text="Desencadenar examen" border="true":::
   

6.  Haga clic en **Test connection** (Probar la conexión).

7.  Haga clic en **Continuar**.

8.  Elija el **desencadenador del examen**. Puede configurar una programación o ejecutar el examen una vez.

9.  Revise el examen y haga clic en **Save and run** (Guardar y ejecutar).

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
