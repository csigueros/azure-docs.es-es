---
title: Administración de clústeres de Apache Hadoop en HDInsight mediante Azure Portal
description: Aprenda a crear y administrar clústeres de Azure HDInsight mediante Azure Portal.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/24/2020
ms.openlocfilehash: 571a01b71556e6c2de6d0940da9daa2eb180f00e
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2021
ms.locfileid: "122651903"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Administración de clústeres de Apache Hadoop en HDInsight mediante Azure Portal

[!INCLUDE [selector](includes/hdinsight-portal-management-selector.md)]

En [Azure Portal](https://portal.azure.com), puede administrar clústeres de [Apache Hadoop](https://hadoop.apache.org/) en Azure HDInsight. Use el selector de pestañas anterior para más información sobre cómo administrar clústeres de Hadoop en HDInsight con otras herramientas.

## <a name="prerequisites"></a>Prerrequisitos

Un clúster existente de Apache Hadoop en HDInsight.  Consulte [Crear clústeres basados en Linux en HDInsight con Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="getting-started"></a>Introducción

Inicie sesión en [https://portal.azure.com](https://portal.azure.com).

## <a name="list-and-show-clusters"></a><a name="showClusters"></a> Enumeración y visualización de clústeres

En la página de **clústeres de HDInsight** se muestran los clústeres existentes.  En el portal:
1. En el menú izquierdo, seleccione **Todos los servicios**.
2. Seleccione **Clústeres de HDInsight** en **ANALYTICS**.

## <a name="cluster-home-page"></a><a name="homePage"></a> Página principal del clúster

Seleccione el nombre del clúster en la página [**Clústeres de HDInsight**](#showClusters).  Se abrirá la vista de **Información general**, que se parece a la siguiente imagen:

:::image type="content" source="./media/hdinsight-administer-use-portal-linux/hdinsight-essentials2.png" alt-text="Azure Portal: aspectos básicos del clúster de HDInsight":::

**Menú superior:**  

| Elemento| Descripción |
|---|---|
|Move|mueve el clúster a otro grupo de recursos o a otra suscripción.|
|Eliminar|elimina el clúster. |
|Actualizar|Actualiza la vista.|

**Menú de la izquierda:**  

  - **Menú de la parte superior izquierda**

    | Elemento| Descripción |
    |---|---|
    |Información general|proporciona información general del clúster.|
    |Registro de actividades|muestra y consulta los registros de actividad.|
    |Control de acceso (IAM)|usa asignaciones de roles.  Consulte [Asignación de roles de Azure mediante Azure Portal](../role-based-access-control/role-assignments-portal.md).|
    |Etiquetas|las etiquetas permiten establecer pares clave-valor para definir una taxonomía personalizada de Cloud Services. Por ejemplo, puede crear una clave denominada **proyecto** y luego usar un valor común para todos los servicios asociados a un proyecto específico.|
    |Diagnosticar y solucionar problemas|muestra información de solución de problemas.|
    |Guía de inicio rápido|muestra información que lo ayuda a empezar a usar HDInsight.|
    |Herramientas|información de ayuda para herramientas relacionadas con HDInsight.|

  - **Menú Configuración**  

    | Elemento| Descripción |
    |---|---|
    |Tamaño del clúster|comprueba, aumenta o reduce el número de nodos de trabajo del clúster. Consulte [Escalado de clústeres](hdinsight-administer-use-portal-linux.md#scale-clusters).|
    |Límites de cuota|muestra los núcleos utilizados y disponibles de la suscripción.|
    |SSH e inicio de sesión del clúster|muestra las instrucciones para conectarse al clúster mediante la conexión de Secure Shell (SSH). Para más información, consulte [Uso SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).|
    |Data Lake Storage Gen1|configura el acceso a Data Lake Storage Gen1.  Consulte [Quickstart: Configuración de clústeres en HDInsight](./hdinsight-hadoop-provision-linux-clusters.md).|
    |Cuentas de almacenamiento|muestra las cuentas de almacenamiento y las claves. Las cuentas de almacenamiento se configuran durante el proceso de creación del clúster.|
    |APLICACIONES|agrega o quita aplicaciones de HDInsight.  Vea [Instalación de aplicaciones de HDInsight personalizadas](hdinsight-apps-install-custom-applications.md).|
    |Acciones de script|ejecuta scripts de Bash en el clúster. Consulte [Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)](hdinsight-hadoop-customize-cluster-linux.md).|
    |Tiendas de metadatos externas|consulte las tiendas de metadatos de [Apache Hive](https://hive.apache.org/) y [Apache Oozie](https://oozie.apache.org/). Las tiendas de metadatos solo pueden configurarse durante el proceso de creación del clúster.|
    |Asociado de HDInsight|agrega o quita el asociado de HDInsight actual.|
    |Propiedades|vea las [propiedades del clúster](#properties).|
    |Bloqueos|agrega bloqueos para evitar la modificación o eliminación del clúster.|
    |Exportación de la plantilla|muestra y exporta la plantilla de Azure Resource Manager para el clúster. Actualmente, solo se puede exportar la cuenta de Azure Storage dependiente. Consulte [Creación de clústeres de Apache Hadoop basados en Linux en HDInsight con plantillas de Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).|

  - **Menú Supervisión**

    | Elemento| Descripción |
    |---|---|
    |Alertas|administra las alertas y acciones.|
    |Métricas|Supervise las métricas del clúster en los registros de Azure Monitor.|
    |Configuración de diagnóstico|configuración donde almacenar las métricas de diagnóstico.|
    |Azure Monitor|Supervise el clúster en Azure Monitor|

  - **Menú Soporte y solución de problemas**

    | Elemento| Descripción |
    |---|---|
    |Estado de los recursos|consulte [Introducción a Azure Resource Health](../service-health/resource-health-overview.md).|
    |Nueva solicitud de soporte|permite crear una incidencia de soporte técnico con el soporte técnico de Microsoft.|

## <a name="cluster-properties"></a><a name="properties"></a> Propiedades del clúster

En la [página principal del clúster](#homePage), en **Configuración**, seleccione **Propiedades**.

|Elemento | Descripción |
|---|---|
|NOMBRE DE HOST|Nombre del clúster.|
|DIRECCIÓN URL DEL CLÚSTER|La dirección URL para la interfaz web de Ambari.|
|Punto de conexión privado|El punto de conexión privado del clúster.|
|Secure Shell (SSH)|el nombre de usuario y el nombre de host que se usarán para acceder al clúster mediante SSH.|
|STATUS|Uno de los valores siguientes: Aborted, Accepted, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, Operational, Running, Error, Deleting, Deleted, Timedout, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued o ClusterCustomization.|
|REGIÓN|ubicación de Azure. Para ver una lista de ubicaciones de Azure admitidas, consulte el cuadro de lista desplegable **Región** en [Precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).|
|FECHA DE CREACIÓN|la fecha en que se implementó el clúster.|
|SISTEMA OPERATIVO|**Windows** o **Linux**.|
|TYPE|Hadoop, HBase, Storm o Spark.|
|Versión|Consulte [Versiones de HDInsight](hdinsight-component-versioning.md).|
|Versión de TLS mínima|Versión de TLS.|
|SUSCRIPCIÓN|el nombre de la suscripción.|
|ORIGEN DE DATOS PREDETERMINADO|el sistema de archivos predeterminado del clúster.|
|Tamaño de los nodos de trabajo|el tamaño de máquina virtual seleccionado de los nodos de trabajo.|
|Tamaño del nodo principal|el tamaño de máquina virtual seleccionado de los nodos principales.|
|Virtual network|El nombre de la red virtual en la que se implementa el clúster (en caso de haber seleccionado uno durante la implementación).|

## <a name="move-clusters"></a>Mover clústeres

Puede mover un clúster de HDInsight a otro grupo de recursos de Azure u otra suscripción.

En la [página principal del clúster](#homePage):

1. Seleccione **Mover** en el menú superior.
2. Seleccione **Mover a otro grupo de recursos** o **Mover a otra suscripción**.
3. Siga las instrucciones de la nueva página.

## <a name="delete-clusters"></a>Eliminación de clústeres

Al eliminar un clúster, no se elimina la cuenta de almacenamiento predeterminada ni otras cuentas de almacenamiento vinculadas. Puede volver a crear el clúster con las mismas cuentas de almacenamiento y las mismas tiendas de metadatos. Se recomienda usar un nuevo contenedor de blobs predeterminado cuando vuelva a crear el clúster.

En la [página principal del clúster](#homePage):

1. Seleccione **Eliminar** en el menú.
2. Siga las instrucciones de la nueva página.

Vea también [Pausa o apagado de clústeres](#pauseshut-down-clusters).

## <a name="add-additional-storage-accounts"></a>Adición de cuentas de almacenamiento adicionales

Después de crear el clúster, puede agregar más cuentas de Azure Storage y cuentas de Azure Data Lake Storage. Para más información, consulte [Adición de más cuentas de almacenamiento a HDInsight](./hdinsight-hadoop-add-storage.md).

## <a name="scale-clusters"></a>Escalado de clústeres

La característica de escalado de clústeres permite cambiar el número de nodos de trabajo que usa un clúster de Azure HDInsight sin necesidad de volver a crear el clúster.

Consulte [Escalabilidad de clústeres de HDInsight](./hdinsight-scaling-best-practices.md) para tener información completa.

## <a name="pauseshut-down-clusters"></a>Pausa o apagado de clústeres

La mayoría de los trabajos de Hadoop son trabajos por lotes que se ejecutan solo ocasionalmente. En la mayoría de los clústeres de Hadoop, hay grandes períodos de tiempo en los que el clúster no se usa para el procesamiento. Con HDInsight, los datos se almacenan en Azure Storage, por lo que puede eliminar un clúster de forma segura cuando no se esté usando.
Los clústeres de HDInsight se cobran aunque no se estén usando. Como en muchas ocasiones los cargos por el clúster son mucho más elevados que los cargos por el almacenamiento, desde el punto de vista económico tiene sentido eliminar clústeres cuando no se usen.

Hay muchas maneras de programar el proceso:

- Usar Azure Data Factory. Consulte [Creación de clústeres de Apache Hadoop basados en Linux en HDInsight a petición con Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) para crear servicios vinculados a HDInsight a petición.
- Usar Azure PowerShell.  Vea [Análisis de datos de retrasos de vuelos](./interactive-query/interactive-query-tutorial-analyze-flight-data.md).
- Uso de CLI de Azure. Consulte [Administración de clústeres de Azure HDInsight mediante la CLI de Azure](hdinsight-administer-use-command-line.md).
- Usar .NET SDK de HDInsight. Consulte [Envío de trabajos de Apache Hadoop](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Para información sobre precios, vea [Precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/). Para eliminar un clúster desde el portal, vea [Eliminación de clústeres](#delete-clusters)

## <a name="upgrade-clusters"></a>Actualización de clústeres

Vea [Actualización del clúster de HDInsight a una versión más reciente](./hdinsight-upgrade-cluster.md).

## <a name="open-the-apache-ambari-web-ui"></a>Apertura de la interfaz de usuario web de Apache Ambari

Ambari proporciona una intuitiva y sencilla interfaz de usuario web de administración de Hadoop respaldada por sus API RESTful. Ambari permite a los administradores de sistema administrar y supervisar clústeres de Hadoop.

En la [página principal del clúster](#homePage):

1. Seleccione **Paneles de clúster**.

    :::image type="content" source="./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu2.png" alt-text="Menú de clúster de Apache Hadoop en HDInsight":::

1. Seleccione **Inicio de Ambari** en la nueva página.
1. Escriba el nombre de usuario y la contraseña del clúster.  El nombre de usuario predeterminado del clúster es _admin_.

Para obtener más información, consulte [Administración de clústeres de HDInsight con la interfaz de usuario web de Apache Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="change-passwords"></a>Cambio de contraseñas

Un clúster de HDInsight puede tener dos cuentas de usuario. La cuenta de usuario del clúster de HDInsight (cuenta de usuario HTTP) y la cuenta de usuario de SSH se crean durante el proceso de creación. Puede usar el portal para cambiar la contraseña de la cuenta de usuario del clúster, y las acciones de script para cambiar la cuenta de usuario de SSH.

### <a name="change-the-cluster-user-password"></a>Cambio de la contraseña de usuario del clúster

> [!NOTE]  
> El cambio de la contraseña de usuario (admin) del clúster puede provocar que las acciones de script que se ejecutan en este clúster no lo hagan correctamente. Si tiene cualquier acción de script persistente cuyo destino son nodos de trabajo, estos scripts pueden producir un error al agregar nodos al clúster a través de operaciones de cambio de tamaño. Para más información sobre acciones de script, consulte [Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)](hdinsight-hadoop-customize-cluster-linux.md).

En la [página principal del clúster](#homePage):
1. Seleccione **SSH + Cluster login** (SSH e inicio de sesión del clúster) en **Configuración**.
2. Seleccione **Restablecer credenciales**.
3. Escriba y confirme la nueva contraseña en los cuadros de texto.
4. Seleccione **Aceptar**.

La contraseña se cambia en todos los nodos del clúster.

### <a name="change-the-ssh-user-password-or-public-key"></a>Cambio de la contraseña de usuario o la clave pública de SSH

1. Con un editor de texto, guarde el texto siguiente como un archivo llamado **changecredentials.sh**.

    > [!IMPORTANT]  
    > Debe utilizar un editor que use LF como final de líneas. Si el editor utiliza CRLF, el script no funcionará.

    ```bash
    #! /bin/bash
    USER=$1
    PASS=$2
    usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
    ```

2. Cargue el archivo en una ubicación de almacenamiento a la que pueda accederse desde HDInsight con una dirección HTTP o HTTPS. Por ejemplo, un almacén de archivos público como OneDrive o Almacenamiento de blobs de Azure. Guarde el identificador URI (dirección HTTP o HTTPS) en el archivo, ya que este URI se necesitará en el paso siguiente.
3. En la [página principal del clúster](#homePage), seleccione **Acciones de script** en **Configuración**.
4. En la página **Acciones de script**, seleccione **Enviar nuevo**.
5. En la página **Enviar acción de script**, escriba la siguiente información:

> [!NOTE]
> Las contraseñas SSH no pueden contener los siguientes caracteres:
> ```
> " ' ` / \ < % ~ | $ & ! 
> ```

   | Campo | Value |
   | --- | --- |
   | Tipo de script | Seleccione **- Personalizado** en la lista desplegable.|
   | Nombre |"Change ssh credentials" |
   | URI de script de Bash |El URI para el archivo changecredentials.sh |
   | Tipos de nodo: (principal, de trabajo, nimbus, supervisor, zookeeper, etc.) |✓ para todos los tipos de nodo indicados |
   | Parámetros |Escriba el nombre de usuario de SSH y la contraseña nueva. Debe haber un espacio entre el nombre de usuario y la contraseña. |
   | Conservar esta acción de script... |Deje este campo en sin activar. |

6. Seleccione **Crear** para aplicar el script. Una vez que finalice el script, puede conectarse al clúster mediante SSH con las nuevas credenciales.

## <a name="find-the-subscription-id"></a>Búsqueda del identificador de la suscripción

Cada clúster está asociado a una suscripción de Azure.  El identificador de la suscripción de Azure es visible desde la [página principal del clúster](#homePage).

## <a name="find-the-resource-group"></a>Búsqueda del grupo de recursos

En el modo de Azure Resource Manager, cada clúster de HDInsight se crea con un grupo de Azure Resource Manager. El grupo de Resource Manager es visible desde la [página principal del clúster](#homePage).

## <a name="find-the-storage-accounts"></a>Búsqueda de las cuentas de almacenamiento

Los clústeres de HDInsight usan una cuenta de Azure Storage o Azure Data Lake Storage para almacenar los datos. Cada clúster de HDInsight puede tener una cuenta de almacenamiento predeterminada y una serie de cuentas de almacenamiento vinculadas. Para enumerar las cuentas de almacenamiento, en la [página principal del clúster](#homePage), en **Configuración**, seleccione **Cuentas de almacenamiento**.

## <a name="monitor-jobs"></a>Supervisión de trabajos

Consulte [Administración de clústeres de HDInsight con la interfaz de usuario web de Apache Ambari](hdinsight-hadoop-manage-ambari.md#monitoring).

## <a name="cluster-size"></a>Tamaño del clúster

El icono **Tamaño del clúster** de la [página principal del clúster](#homePage) muestra el número de núcleos asignados a este clúster y cómo se asignan a los nodos de este clúster.

> [!IMPORTANT]  
> Para supervisar los servicios que proporciona el clúster de HDInsight, debe usar la web de Ambari o la API de REST de Ambari. Para más información sobre el uso de Ambari, consulte [Administración de clústeres de HDInsight con Apache Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="connect-to-a-cluster"></a>Conectarse a un clúster

- [Uso de Apache Hive con HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
- [Uso de SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Pasos siguientes

En este artículo ha aprendido algunas funciones administrativas básicas. Para más información, vea los siguientes artículos:

- [Administración de HDInsight con PowerShell de Azure](hdinsight-administer-use-powershell.md)
- [Administración de HDInsight con la CLI de Azure](hdinsight-administer-use-command-line.md)
- [Creación de clústeres de HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Detalles sobre el uso de la API REST de Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
- [Uso de Apache Hive en HDInsight](hadoop/hdinsight-use-hive.md)
- [Uso de Apache Sqoop en HDInsight](hadoop/hdinsight-use-sqoop.md)
- [Uso de funciones definidas por el usuario (UDF) de Python con Apache Hive y Apache Pig en HDInsight](hadoop/python-udf-hdinsight.md)
- [¿Qué versión de Apache Hadoop tiene en Azure HDInsight?](hdinsight-component-versioning.md)