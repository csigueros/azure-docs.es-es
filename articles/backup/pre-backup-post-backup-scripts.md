---
title: Uso de scripts anteriores y posteriores a la copia de seguridad
description: Este artículo contiene el procedimiento para especificar scripts anteriores y posteriores a la copia de seguridad. Azure Backup Server (MABS)
ms.topic: conceptual
ms.date: 07/06/2021
ms.openlocfilehash: 48d65679f4ff1b6486513067058c1f385e4f1434
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2021
ms.locfileid: "113568611"
---
# <a name="using-pre-backup-and-post-backup-scripts"></a>Uso de scripts anteriores y posteriores a la copia de seguridad

Se aplica a: Microsoft Azure Backup Server (MABS)

Un _script anterior a la copia de seguridad_ es un script que reside en un equipo protegido, se ejecuta antes de los trabajos de copia de seguridad de MABS, y prepara el origen de datos protegido para la copia de seguridad.

Un _script posterior a la copia de seguridad_ es un script que se ejecuta después de un trabajo de copia de seguridad de MABS para realizar el procesamiento posterior a la copia de seguridad, como volver a poner en línea la máquina virtual.

Cuando instala un agente de protección en un equipo, se agrega un archivo ScriptingConfig.xml en la carpeta _ruta de acceso de instalación_ \Microsoft Data Protection Manager\DPM\Scripting del equipo protegido. Para cada origen de datos protegido del equipo, puede especificar un script anterior a la copia de seguridad y otro posterior a la copia de seguridad en ScriptingConfig.xml.

>[!Note]
>Los scripts anteriores y posteriores a la copia de seguridad no pueden ser de VBScript. Por ello, tendrá que utilizar un comando contenedor si el script contiene **cscript myscript.vbs**.

Cuando MABS ejecuta un trabajo de protección, se comprueba el archivo ScriptingConfig.xml del equipo protegido. Si se ha especificado un script anterior a la copia de seguridad, MABS ejecuta el script y, a continuación, completa el trabajo. Si se ha especificado un script posterior a la copia de seguridad, MABS completa el trabajo y, a continuación, ejecuta el script.

>[!Note]
>Los trabajos de protección incluyen la creación de réplicas, las copias de seguridad completas rápidas, la sincronización y la comprobación coherencia.

MABS ejecuta los scripts de ejecución anterior y posterior a la copia de seguridad mediante la cuenta de sistema local. Como procedimiento recomendado, debe asegurarse de que los scripts tienen permisos de lectura y ejecución para el administrador y las cuentas de sistema local únicamente. Este nivel de permisos impide que los usuarios no autorizados modifiquen los scripts.

**ScriptingConfig.xml**

```
<?xml version="1.0" encoding="utf-8"?>
<ScriptConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
xmlns:xsd="http://www.w3.org/2001/XMLSchema" 
xmlns="http://schemas.microsoft.com/2003/dls/ScriptingConfig.xsd">
   <DatasourceScriptConfig DataSourceName="Data source">
     <PreBackupScript>”Path\Script Parameters” </PreBackupScript>
     <PostBackupScript>"Path\Script Parameters” </PostBackupScript>
     <TimeOut>30</TimeOut>
   </DatasourceScriptConfig>
</ScriptConfiguration>
```

Para especificar scripts anteriores y posteriores a la copia de seguridad

1. En el equipo protegido, abra el archivo ScriptingConfig.xml con un editor de texto o de XML.

   >[!Note]
   >El atributo DataSourceName deberá indicarse como **Unidad:** (por ejemplo, D: si el origen de datos se encuentra en la unidad D).

1. Para cada origen de datos, complete el elemento DatasourceScriptConfig del modo siguiente:


   1. Para el atributo DataSourceName, introduzca el volumen del origen de datos (para los orígenes de datos de archivo), o el nombre (para el resto de orígenes de datos). El nombre del origen de datos para datos de programa debería tener el formato de _Instancia\Base de datos_ para SQL, _Nombre del grupo de almacenamiento_ para Exchange, _Ruta de acceso lógica\Nombre del componente_ para Virtual Server, y _Granja de Sharepoint\Nombre de servidor de SQL Server\Nombre de instancia de SQL\Base de datos de configuración de SharePoint_ para Windows SharePoint Services.
   1. En la etiqueta _PreBackupScript_, escriba la ruta de acceso y el nombre del script.
   1. En la etiqueta _PreBackupCommandLine_, escriba los parámetros de línea de comandos que se pasan a los scripts, separados por espacios.
   1. En la etiqueta _PostBackupScript_, escriba la ruta de acceso y el nombre del script.
   1. En la etiqueta _PostBackupCommandLine_, escriba los parámetros de línea de comandos que se pasan a los scripts, separados por espacios.
   1. En la etiqueta _TimeOut_, introduzca la cantidad de tiempo, en minutos, que MABS debe esperar tras invocar un script antes de que se agote el tiempo de espera y se marque el script como erróneo.

1. Guarde el archivo ScriptingConfig.xml.

>[!Note]
>MABS agregará un parámetro booleano adicional (true/false) al comando de script posterior a la copia de seguridad, lo que indica el estado del trabajo de copia de seguridad de MABS.
