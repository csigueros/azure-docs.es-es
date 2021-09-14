---
title: Solución de problemas de implementación de la solución SAP de Azure Sentinel | Microsoft Docs
description: Obtenga información sobre cómo solucionar problemas específicos que pueden producirse en la implementación solución SAP de Azure Sentinel.
author: batamig
ms.author: bagold
ms.service: azure-sentinel
ms.topic: troubleshooting
ms.custom: mvc
ms.date: 08/09/2021
ms.subservice: azure-sentinel
ms.openlocfilehash: 525048346edc184744a69c70c8fd4dc0db1bd554
ms.sourcegitcommit: deb5717df5a3c952115e452f206052737366df46
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122681210"
---
# <a name="troubleshooting-your-azure-sentinel-sap-solution-deployment"></a>Solución de problemas de implementación de la solución SAP de Azure Sentinel

## <a name="useful-docker-commands"></a>Comandos útiles de Docker

Para solucionar problemas del conector de datos de SAP, puede que le resulten útiles los siguientes comandos:

|Función  |Comando  |
|---------|---------|
|**Detención del contenedor de Docker**     |  `docker stop sapcon-[SID]`       |
|**Inicio del contenedor de Docker**     |`docker start sapcon-[SID]`         |
|**Visualización de los registros del sistema de Docker**     |  `docker logs -f sapcon-[SID]`       |
|**Especificación del contenedor de Docker**     |   `docker exec -it sapcon-[SID] bash`      |
|     |         |

Para más información, consulte la [documentación sobre la CLI de Docker](https://docs.docker.com/engine/reference/commandline/docker/).

## <a name="review-system-logs"></a>Revisión de registros del sistema

Es muy recomendable que revise los registros del sistema después de instalar o [restablecer el conector de datos](#reset-the-sap-data-connector).

Ejecute:

```bash
docker logs -f sapcon-[SID]
```

## <a name="enable-debug-mode-printing"></a>Habilitación de la impresión en modo de depuración

**Para habilitar la impresión en modo de depuración, haga lo siguiente**:

1. Copie el siguiente archivo en el directorio **sapcon/[SID]** y, a continuación, cámbiele el nombre por `loggingconfig.yaml`: https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/template/loggingconfig_DEV.yaml

1. [Restablezca el conector de datos de SAP](#reset-the-sap-data-connector).

Por ejemplo, para SID `A4H`:

```bash
wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/template/loggingconfig_DEV.y
              cp loggingconfig.yaml ~/sapcon/A4H
              docker restart sapcon-A4H
```

**Para volver a deshabilitar la impresión en modo de depuración, ejecute la siguiente instrucción**:

```bash
mv loggingconfig.yaml loggingconfig.old
ls
docker restart sapcon-[SID]
```

## <a name="view-all-docker-execution-logs"></a>Ver todos los registros de ejecución de Docker

Para ver todos los registros de ejecución de la implementación del conector de datos de Azure Sentinel para SAP, ejecute uno de los siguientes comandos:

```bash
docker exec -it sapcon-[SID] bash && cd /sapcon-app/sapcon/logs
```

o

```bash
docker exec –it sapcon-[SID] cat /sapcon-app/sapcon/logs/[FILE_LOGNAME]
```

Debe aparecer una salida similar a la siguiente:

```bash
Logs directory:
root@644c46cd82a9:/sapcon-app# ls sapcon/logs/ -l
total 508
-rwxr-xr-x 1 root root      0 Mar 12 09:22 ' __init__.py'
-rw-r--r-- 1 root root    282 Mar 12 16:01  ABAPAppLog.log
-rw-r--r-- 1 root root   1056 Mar 12 16:01  ABAPAuditLog.log
-rw-r--r-- 1 root root    465 Mar 12 16:01  ABAPCRLog.log
-rw-r--r-- 1 root root    515 Mar 12 16:01  ABAPChangeDocsLog.log
-rw-r--r-- 1 root root    282 Mar 12 16:01  ABAPJobLog.log
-rw-r--r-- 1 root root    480 Mar 12 16:01  ABAPSpoolLog.log
-rw-r--r-- 1 root root    525 Mar 12 16:01  ABAPSpoolOutputLog.log
-rw-r--r-- 1 root root      0 Mar 12 15:51  ABAPTableDataLog.log
-rw-r--r-- 1 root root    495 Mar 12 16:01  ABAPWorkflowLog.log
-rw-r--r-- 1 root root 465311 Mar 14 06:54  API.log # view this log to see submits of data into Azure Sentinel
-rw-r--r-- 1 root root      0 Mar 12 15:51  LogsDeltaManager.log
-rw-r--r-- 1 root root      0 Mar 12 15:51  PersistenceManager.log
-rw-r--r-- 1 root root   4830 Mar 12 16:01  RFC.log
-rw-r--r-- 1 root root   5595 Mar 12 16:03  SystemAdmin.log
```

Para copiar sus registros en el sistema operativo host, ejecute lo siguiente:

```bash
docker cp sapcon-[SID]:/sapcon-app/sapcon/logs /directory
```

Por ejemplo:

```bash
docker cp sapcon-A4H:/sapcon-app/sapcon/logs /tmp/sapcon-logs-extract
```

## <a name="review-and-update-the-sap-data-connector-configuration"></a>Revisión y actualización de la configuración del conector de datos de SAP

Si desea comprobar el archivo de configuración del conector de datos de SAP y llevar a cabo actualizaciones manuales, realice los pasos siguientes:

1. En la máquina virtual, en el directorio principal del usuario, abra el archivo **~/sapcon/[SID]/systemconfig.ini**.
1. Actualice la configuración si es necesario y reinicie el contenedor:

    ```bash
    docker restart sapcon-[SID]
    ```

## <a name="reset-the-sap-data-connector"></a>Restablecimiento del conector de datos de SAP

En los siguientes pasos se restablece el conector y se vuelven a ingerir los registros de SAP de las últimas 24 horas.

1.  Detenga el conector. Ejecute:

    ```bash
    docker stop sapcon-[SID]
    ```

1.  Elimine el archivo **metadata.db** del directorio **sapcon/[SID]** . Ejecute:

    ```bash
    cd ~/sapcon/<SID>
    ls
    mv metadata.db metadata.old
    ```

    > [!NOTE]
    > El archivo **metadata.db** contiene la última marca de tiempo de cada uno de los registros y su cometido es evitar que se dupliquen.

1. Inicie de nuevo el conector. Ejecute:

    ```bash
    docker start sapcon-[SID]
    ```

Asegúrese de [revisar los registros del sistema](#review-system-logs) cuando haya terminado.



## <a name="common-issues"></a>Problemas comunes

Después de implementar el conector de datos de SAP y el contenido de seguridad, puede experimentar los siguientes errores o problemas:

### <a name="corrupt-or-missing-sap-sdk-file"></a>No se encuentra un archivo del SDK de SAP o está dañado

Este error puede producirse cuando el conector no arranca con PyRfc, o bien se muestran mensajes de error relacionados con ZIP.

1. Vuelva a instalar el SDK de SAP.
1. Compruebe que sea la versión correcta de 64 bits de Linux. A partir de la fecha en curso, el nombre de archivo de la versión es el siguiente: **nwrfc750P_8-70002752.zip**.

Si había instalado manualmente el conector de datos, asegúrese de haber copiado el archivo del SDK en el contenedor de Docker.

Ejecute:

```bash
Docker cp SDK by running docker cp nwrfc750P_8-70002752.zip /sapcon-app/inst/
```

### <a name="abap-runtime-errors-appear-on-a-large-system"></a>Errores en tiempo de ejecución de ABAP en un sistema grande

Si se producen errores en tiempo de ejecución de ABAP en sistemas grandes, intente definir un tamaño de fragmento más pequeño:

1. Edite el archivo **sapcon/SID/systemconfig.ini** y defina `timechunk = 5`.
2. [Restablezca el conector de datos de SAP](#reset-the-sap-data-connector).

> [!NOTE]
> El tamaño de **timechunk** se define en minutos.

### <a name="empty-or-no-audit-log-retrieved-with-no-special-error-messages"></a>Registro de auditoría vacío o no recuperado, sin mensajes de error especiales

1. Compruebe que el registro de auditoría esté habilitado en SAP.
1. Compruebe las transacciones **SM19** o **RSAU_CONFIG**.
1. Habilite los eventos necesarios.
1. Compruebe si llegan y hay mensajes en SAP **SM20** o **RSAU_READ_LOG**, sin que aparezcan errores especiales en el registro del conector.


### <a name="incorrect-azure-sentinel-workspace-id-or-key"></a>Clave o Id. de área de trabajo de Azure Sentinel incorrectos

Si se da cuenta de que ha indicado una clave o un Id. de área de trabajo incorrectos en el [script de implementación](sap-deploy-solution.md#create-key-vault-for-your-sap-credentials), actualice las credenciales almacenadas en Azure KeyVault.

Después de comprobar las credenciales en Azure Key Vault, reinicie el contenedor:

```bash
docker restart sapcon-[SID]
```

### <a name="incorrect-sap-abap-user-credentials-in-a-fixed-configuration"></a>Credenciales de usuario de SAP ABAP incorrectas en una configuración fija

Hablamos de "configuración fija" cuando la contraseña se almacena directamente en el archivo de configuración **systemconfig.ini**.

Si las credenciales del archivo no son correctas, compruébelas.

Aplique cifrado base64 al usuario y la contraseña. Puede usar herramientas de cifrado en línea para cifrar las credenciales, como https://www.base64encode.org/.

### <a name="incorrect-sap-abap-user-credentials-in-key-vault"></a>Credenciales de usuario de SAP ABAP incorrectas

Compruebe sus credenciales y corríjalas, si es necesario, aplicando los valores correctos a los valores **ABAPUSER** y **ABAPPASS** en Azure Key Vault.

A continuación, reinicie el contenedor:

```bash
docker restart sapcon-[SID]
```


### <a name="missing-abap-sap-user-permissions"></a>No se encuentran permisos de (usuario de SAP) ABAP

Si recibe un mensaje de error similar a **Falta la autorización RFC de back-end**, sus autorizaciones de SAP y el rol no se han aplicado correctamente.

1. Asegúrese de que el rol **MSFTSEN/SENTINEL_CONNECTOR** se haya importado como parte de un transporte de [solicitud de cambio](sap-solution-detailed-requirements.md#required-sap-log-change-requests) y aplicado al usuario del conector.

1. Ejecute el proceso de generación de roles y comparación de usuarios usando la transacción PFCG de SAP.

### <a name="missing-data-in-your-workbooks-or-alerts"></a>Faltan datos en sus libros o alertas

Si descubre que faltan datos en sus libros o alertas de Azure Sentinel, asegúrese de que la directiva **Auditlog** esté habilitada correctamente en SAP, sin errores en el archivo de registro. 

Use la transacción **RSAU_CONFIG_LOG** para este paso.


### <a name="missing-sap-change-request"></a>Falta una solicitud de cambio de SAP

Si ve errores de que le falta una [solicitud de cambio de SAP](sap-solution-detailed-requirements.md#required-sap-log-change-requests) necesaria, asegúrese de haber importado la solicitud correcta para el sistema.

Para más información, consulte [Configuración del sistema SAP](sap-deploy-solution.md#configure-your-sap-system).

### <a name="network-connectivity-issues"></a>Problemas de conectividad de red

Si tiene problemas de conectividad de red con el entorno de SAP o con Azure Sentinel, compruebe la conectividad de red para asegurarse de que los datos fluyan según lo previsto.

### <a name="other-unexpected-issues"></a>Otras incidencias inesperadas

Si tiene incidencias inesperadas que no aparecen en este artículo, pruebe los siguientes pasos:

- [Restablezca el conector y vuelva a cargar los registros.](#reset-the-sap-data-connector)
- [Actualice el conector](sap-deploy-solution.md#update-your-sap-data-connector) con la versión más reciente.

> [!TIP]
> También se recomienda restablecer el conector y asegurarse de tener las actualizaciones más recientes después de cambios importantes en la configuración.

### <a name="retrieving-an-audit-log-fails-with-warnings"></a>Error al recuperar un registro de auditoría, con advertencias

Si intenta recuperar un registro de auditoría sin haber implementado la [solicitud de cambio necesaria](sap-solution-detailed-requirements.md#required-sap-log-change-requests) o en una versión anterior o sin revisión y se produce un error en el proceso con advertencias, compruebe que el registro de auditoría de SAP se pueda recuperar usando uno de los siguientes métodos:

- Usando un modo de compatibilidad llamado *XAL* en versiones anteriores
- Usando una versión que no se haya actualizado recientemente
- Sin la solicitud de cambio necesaria instalada

Aunque el sistema debería cambiar automáticamente al modo de compatibilidad si es necesario, es posible que tenga que hacerlo usted manualmente. Para cambiar al modo de compatibilidad manualmente, haga lo siguiente:

1. En el directorio **sapcon/SID**, edite el archivo **systemconfig.ini**.

1. Defina `auditlogforcexal = True`.

1. Reinicie el contenedor de Docker:

    ```bash
    docker restart sapcon-[SID]
    ```

### <a name="sapcontrol-or-java-subsystems-unable-to-connect"></a>Los subsistemas SAPCONTROL o JAVA no se pueden conectar

Compruebe que el usuario del sistema operativo sea válido y pueda ejecutar el siguiente comando en el sistema SAP de destino:

```bash
sapcontrol -nr <SID> -function GetSystemInstanceList
```

### <a name="sapcontrol-or-java-subsystem-fails-with-timezone-related-error-message"></a>Error de los subsistemas SAPCONTROL o JAVA con un mensaje de error relacionado con la zona horaria

Si se produce un error de los subsistemas SAPCONTROL o JAVA con un mensaje de error relacionado con la zona horaria, como **Compruebe la configuración y el acceso de red al servidor SAP - "Etc/NZST"** , asegúrese de estar usando códigos de zona horaria estándar.

Por ejemplo, use `javatz = GMT+12` o `abaptz = GMT-3**`.


### <a name="unable-to-import-the-change-request-transports-to-sap"></a>No se pueden importar transportes de solicitud de cambio a SAP

Si no puede importar las [solicitudes de cambio de registro de SAP necesarias](sap-solution-detailed-requirements.md#required-sap-log-change-requests) y recibe un error sobre una versión de componente no válida, agregue `ignore invalid component version` al importar la solicitud de cambio.

### <a name="audit-log-data-not-ingested-past-initial-load"></a>Datos del registro de auditoría que no se ingieren después de la carga inicial

Si los datos del registro de auditoría de SAP, visibles en las transacciones **RSAU_READ_LOAD** o **SM200**, no se ingieren en Azure Sentinel después de la carga inicial, es posible que se produzca un error de configuración en el sistema SAP y el sistema operativo host de SAP.

- Las cargas iniciales se ingieren después de una instalación nueva del conector de datos de SAP o después de eliminar el archivo **metadata.db**.
- Un error de configuración de muestra podría producirse cuando la zona horaria del sistema SAP esté establecida en **CET** en la transacción **STZAC**, pero la zona horaria del sistema operativo host de SAP esté establecida en **UTC**.

Para comprobar si hay errores de configuración, ejecute el informe **RSDBTIME** en la transacción **SE38**. Si encuentra un error de coincidencia entre el sistema SAP y el sistema operativo host de SAP, haga lo siguiente:

1. Detenga el contenedor de Docker. Ejecutar

    ```bash
    docker stop sapcon-[SID]
    ```

1.  Elimine el archivo **metadata.db** del directorio **sapcon/[SID]** . Ejecute:

    ```bash
    rm ~/sapcon/[SID]/metadata.db
    ```

1. Actualice el sistema SAP y el sistema operativo host de SAP para que tengan una configuración coincidente, como la misma zona horaria. Para obtener más información, consulte la [wiki de la comunidad de SAP](https://wiki.scn.sap.com/wiki/display/Basis/Time+zone+settings%2C+SAP+vs.+OS+level).

1. Vuelva a iniciar el contenedor. Ejecute:

    ```bash
    docker start sapcon-[SID]
    ```

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte:

- [Implementación de la supervisión de amenazas continua de SAP (versión preliminar pública)](sap-deploy-solution.md)
- [Referencia sobre de los registros de la solución Azure Sentinel para SAP (versión preliminar pública)](sap-solution-log-reference.md)
- [Implementación del conector de datos de SAP de Azure Sentinel con SNC](sap-solution-deploy-snc.md)
- [Opciones de configuración de expertos, implementación local y orígenes de registro de SAPControl](sap-solution-deploy-alternate.md)
- [Solución Azure Sentinel para SAP: referencia de contenido de seguridad (versión preliminar pública)](sap-solution-security-content.md)
- [Requisitos detallados de SAP para la solución Azure Sentinel para SAP (versión preliminar pública)](sap-solution-detailed-requirements.md)
