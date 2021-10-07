---
title: Configuración de un dispositivo de Azure Migrate en Azure Government
description: Aprenda a configurar un dispositivo de Azure Migrate en Azure Government
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: 84bb24daf4586f874c0caf3994bc59773e663337
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129093599"
---
# <a name="set-up-an-appliance-for-azure-government-cloud"></a>Configuración de un dispositivo para la nube de Azure Government

Siga este artículo para implementar un [dispositivo de Azure Migrate](./migrate-appliance-architecture.md) para la nube de Azure Government para realizar lo siguiente:

- detección, evaluación y replicación sin agente de servidores que se ejecutan en el entorno de VMware.
- detección y evaluación de servidores que se ejecutan en el entorno de Hyper-V.
- detección y evaluación de servidores físicos o servidores que se ejecutan en otras nubes, como AWS, GCP, Xen, etc.

Si quiere configurar una aplicación en la nube pública, siga [este artículo](deploy-appliance-script.md).

> [!NOTE]
> La opción para implementar un dispositivo mediante una plantilla (para servidores que se ejecutan en el entorno de VMware o de Hyper-V) no es compatible con Azure Government. Solo debe usar el script del instalador.

## <a name="prerequisites"></a>Requisitos previos

Puede usar el script para implementar el dispositivo de Azure Migrate en un servidor físico o virtualizado existente.

- El servidor que actuará como el dispositivo debe ejecutar Windows Server 2016 y cumplir otros requisitos para [VMware](migrate-appliance.md#appliance---vmware), [Hyper-V](migrate-appliance.md#appliance---hyper-v) y los [servidores físicos](migrate-appliance.md#appliance---physical).
- Si ejecuta el script en un servidor con el dispositivo de Azure Migrate ya instalado, puede elegir limpiar la configuración existente e instalar un nuevo dispositivo con la configuración deseada. Al ejecutar el script, recibirá una notificación como se muestra a continuación:
  
    :::image type="content" source="./media/deploy-appliance-script/script-reconfigure-appliance.png" alt-text="Captura de pantalla que muestra cómo volver a configurar un dispositivo":::.

## <a name="set-up-the-appliance-for-vmware"></a>Instalación de la aplicación para VMware

1. Para instalar el dispositivo, descargue el archivo comprimido llamado AzureMigrateInstaller.zip desde el portal o desde [aquí](https://go.microsoft.com/fwlink/?linkid=2140334).
1. Extraiga el contenido en el servidor en el que desea implementar el dispositivo.
1. Ejecute el script de PowerShell para iniciar el administrador de configuración del dispositivo.
1. Instale el dispositivo y configúrelo por primera vez.

### <a name="download-the-script"></a>Descarga del script

1. En **Objetivos de migración** > **Windows, Linux y SQL Servers** >  (Servidores Windows, Linux y SQL) **Azure Migrate: Detección y evaluación**, haga clic en **Detectar**.
2. En **Detectar servidor** >  **¿Están virtualizados sus servidores?** , seleccione **Sí, con hipervisor VMware vSphere.**
3. Proporcione un nombre de dispositivo y genere una clave de proyecto en el portal.
3. Haga clic en **Descargar** para descargar el archivo comprimido.

### <a name="verify-security"></a>Comprobación de la seguridad

Compruebe que el archivo comprimido es seguro, antes de implementarlo.

1. En el servidor en el que descargó el archivo, abra una ventana de comandos de administrador.
2. Ejecute el siguiente comando para generar el código hash para el archivo ZIP:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Ejemplo de uso: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
3.  Compruebe la versión más reciente del dispositivo y el valor hash:

    **Descargar** | **Valor del código hash**
    --- | ---
    [La versión más reciente](https://go.microsoft.com/fwlink/?linkid=2140337) | BA84B58E88DDFE23E5D4CE73530227EBBC187B3634B66A3E0F0B3E5DF5F0A94F


### <a name="run-the-script"></a>Ejecute el script.

1. Extraiga el archivo comprimido en la carpeta del servidor que hospedará el dispositivo.  No ejecute el script en un servidor con un dispositivo de Azure Migrate existente.

2. Inicie PowerShell en el servidor anterior con privilegios administrativos (elevados).

3. Cambie el directorio de PowerShell a la carpeta en la que se ha extraído el contenido del archivo comprimido descargado.

4. Ejecute el script denominado **AzureMigrateInstaller.ps1** ejecutando el comando siguiente:

   `PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1 `

5. Seleccione entre las opciones de escenario, nube y conectividad para implementar un dispositivo con la configuración deseada. Por ejemplo, la selección que se muestra a continuación configura un dispositivo para detectar, evaluar y migrar **servidores que se ejecutan en el entorno de VMware** a un proyecto de Azure Migrate con **conectividad predeterminada _(punto de conexión público)_** en la **nube de Azure Government**.

   :::image type="content" source="./media/deploy-appliance-script-government/script-vmware-gov-inline.png" alt-text="Captura de pantalla que muestra cómo configurar un dispositivo con la configuración deseada para VMware" lightbox="./media/deploy-appliance-script-government/script-vmware-gov-expanded.png":::

6. El script del instalador hace lo siguiente:

   - Instala agentes y una aplicación web.
   - Instala los roles de Windows, incluido el servicio de activación de Windows, IIS y PowerShell ISE.
   - Descarga e instala un módulo de reescritura de IIS.
   - Actualiza una clave del registro (HKLM) con detalles de configuración persistentes para Azure Migrate.
   - Crea los siguientes archivos en la ruta de acceso:
     - **Archivos de configuración**:%Programdata%\Microsoft Azure\Config
     - **Archivos de registro**:%Programdata%\Microsoft Azure\Logs

Una vez que el script se haya ejecutado correctamente, el administrador de configuración del dispositivo se iniciará automáticamente.

### <a name="verify-access"></a>Comprobación del acceso

Asegúrese de que el dispositivo pueda conectarse a las direcciones URL de Azure para las [nubes gubernamentales](migrate-appliance.md#government-cloud-urls).

## <a name="set-up-the-appliance-for-hyper-v"></a>Configuración del dispositivo para Hyper-V

1. Para instalar el dispositivo, descargue el archivo comprimido llamado AzureMigrateInstaller.zip desde el portal o desde [aquí](https://go.microsoft.com/fwlink/?linkid=2140334).
1. Extraiga el contenido en el servidor en el que desea implementar el dispositivo.
1. Ejecute el script de PowerShell para iniciar el administrador de configuración del dispositivo.
1. Instale el dispositivo y configúrelo por primera vez.

### <a name="download-the-script"></a>Descarga del script

1.  En **Objetivos de migración** > **Windows, Linux y SQL Servers** >  (Servidores Windows, Linux y SQL) **Azure Migrate: Detección y evaluación**, haga clic en **Detectar**.
2.  En **Detectar servidores** >  **¿Están sus servidores virtualizados?** , seleccione **Sí, con Hyper-V**.
3. Proporcione un nombre de dispositivo y genere una clave de proyecto en el portal.
3. Haga clic en **Descargar** para descargar el archivo comprimido. 

### <a name="verify-security"></a>Comprobación de la seguridad

Compruebe que el archivo comprimido es seguro, antes de implementarlo.

1. En el servidor en el que descargó el archivo, abra una ventana de comandos de administrador.
2. Ejecute el siguiente comando para generar el código hash para el archivo ZIP:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Ejemplo de uso: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
3.  Compruebe la versión más reciente del dispositivo y el valor hash:

    **Descargar** | **Valor del código hash**
    --- | ---
    [La versión más reciente](https://go.microsoft.com/fwlink/?linkid=2140424) | BA84B58E88DDFE23E5D4CE73530227EBBC187B3634B66A3E0F0B3E5DF5F0A94F

### <a name="run-the-script"></a>Ejecute el script.

1. Extraiga el archivo comprimido en la carpeta del servidor que hospedará el dispositivo.  No ejecute el script en un servidor con un dispositivo de Azure Migrate existente.

2. Inicie PowerShell en el servidor anterior con privilegios administrativos (elevados).

3. Cambie el directorio de PowerShell a la carpeta en la que se ha extraído el contenido del archivo comprimido descargado.

4. Ejecute el script denominado **AzureMigrateInstaller.ps1** ejecutando el comando siguiente:

   `PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1`

5. Seleccione entre las opciones de escenario, nube y conectividad para implementar un dispositivo con la configuración deseada. Por ejemplo, la selección que se muestra a continuación configura un dispositivo para detectar y evaluar **servidores que se ejecutan en el entorno de Hyper-V** a un proyecto de Azure Migrate con **conectividad predeterminada _(punto de conexión público)_** en la **nube de Azure Government**.

    :::image type="content" source="./media/deploy-appliance-script-government/script-hyperv-gov-inline.png" alt-text="Captura de pantalla que muestra cómo configurar un dispositivo con la configuración deseada para Hyper-V" lightbox="./media/deploy-appliance-script-government/script-hyperv-gov-expanded.png":::

6. El script del instalador hace lo siguiente:

    - Instala agentes y una aplicación web.
    - Instala los roles de Windows, incluido el servicio de activación de Windows, IIS y PowerShell ISE.
    - Descarga e instala un módulo de reescritura de IIS.
    - Actualiza una clave del registro (HKLM) con detalles de configuración persistentes para Azure Migrate.
    - Crea los siguientes archivos en la ruta de acceso:
    - **Archivos de configuración**:%Programdata%\Microsoft Azure\Config
    - **Archivos de registro**:%Programdata%\Microsoft Azure\Logs

Una vez que el script se haya ejecutado correctamente, el administrador de configuración del dispositivo se iniciará automáticamente.

### <a name="verify-access"></a>Comprobación del acceso

Asegúrese de que el dispositivo pueda conectarse a las direcciones URL de Azure para las [nubes gubernamentales](migrate-appliance.md#government-cloud-urls).


## <a name="set-up-the-appliance-for-physical-servers"></a>Configuración del dispositivo para servidores físicos

1. Para instalar el dispositivo, descargue el archivo comprimido llamado AzureMigrateInstaller.zip desde el portal o desde [aquí](https://go.microsoft.com/fwlink/?linkid=2140334).
1. Extraiga el contenido en el servidor en el que desea implementar el dispositivo.
1. Ejecute el script de PowerShell para iniciar el administrador de configuración del dispositivo.
1. Instale el dispositivo y configúrelo por primera vez.

### <a name="download-the-script"></a>Descarga del script

1. En **Objetivos de migración** > **Windows, Linux y SQL Servers** >  (Servidores Windows, Linux y SQL) **Azure Migrate: Detección y evaluación**, haga clic en **Detectar**.
2. En **Detectar servidores** >  **¿Los servidores están virtualizados?** , seleccione **Físico o de otro tipo (AWS, GCP, Xen, etc.)** .
3. Haga clic en **Descargar** para descargar el archivo comprimido.

### <a name="verify-security"></a>Comprobación de la seguridad

Compruebe que el archivo comprimido es seguro, antes de implementarlo.

1. En el servidor en el que descargó el archivo, abra una ventana de comandos de administrador.
2. Ejecute el siguiente comando para generar el código hash para el archivo ZIP:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Ejemplo de uso: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
3.  Compruebe la versión más reciente del dispositivo y el valor hash:

    **Descargar** | **Valor del código hash**
    --- | ---
    [La versión más reciente](https://go.microsoft.com/fwlink/?linkid=2140338) | BA84B58E88DDFE23E5D4CE73530227EBBC187B3634B66A3E0F0B3E5DF5F0A94F

> [!NOTE]
> El mismo script se puede usar para configurar el dispositivo físico para la nube de Azure Government con conectividad de punto de conexión privado o público.

### <a name="run-the-script"></a>Ejecute el script.

1. Extraiga el archivo comprimido en la carpeta del servidor que hospedará el dispositivo.  No ejecute el script en un servidor con un dispositivo de Azure Migrate existente.

2. Inicie PowerShell en el servidor anterior con privilegios administrativos (elevados).

3. Cambie el directorio de PowerShell a la carpeta en la que se ha extraído el contenido del archivo comprimido descargado.

4. Ejecute el script denominado **AzureMigrateInstaller.ps1** ejecutando el comando siguiente:

    `PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1 `

5. Seleccione entre las opciones de escenario, nube y conectividad para implementar un dispositivo con la configuración deseada. Por ejemplo, la selección que se muestra a continuación configura un dispositivo y evalúa **servidores físicos** _(o servidores que se ejecutan en otras nubes, como AWS, GCP, Xen, etc.)_ en un proyecto de Azure Migrate con la **conectividad predeterminada _(punto de conexión público)_** en la **nube de Azure Government**.

    :::image type="content" source="./media/deploy-appliance-script-government/script-physical-gov-inline.png" alt-text="Captura de pantalla que muestra cómo configurar un dispositivo con la configuración deseada para servidores físicos." lightbox="./media/deploy-appliance-script-government/script-physical-gov-expanded.png":::

6. El script del instalador hace lo siguiente:

    - Instala agentes y una aplicación web.
    - Instala los roles de Windows, incluido el servicio de activación de Windows, IIS y PowerShell ISE.
    - Descarga e instala un módulo de reescritura de IIS.
    - Actualiza una clave del registro (HKLM) con detalles de configuración persistentes para Azure Migrate.
    - Crea los siguientes archivos en la ruta de acceso:
        - **Archivos de configuración**:%Programdata%\Microsoft Azure\Config
        - **Archivos de registro**:%Programdata%\Microsoft Azure\Logs

Una vez que el script se haya ejecutado correctamente, el administrador de configuración del dispositivo se iniciará automáticamente.

> [!NOTE]
> En caso de que surja algún problema, puede acceder a los registros de script en C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log para solucionarlo.

### <a name="verify-access"></a>Comprobación del acceso

Asegúrese de que el dispositivo pueda conectarse a las direcciones URL de Azure para las [nubes gubernamentales](migrate-appliance.md#government-cloud-urls).

## <a name="next-steps"></a>Pasos siguientes

Después de implementar la aplicación, es preciso que la configure y la registre en el proyecto.

- Configure el dispositivo para [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance).
- Configure el dispositivo para [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
- Configure el dispositivo para [servidores físicos](how-to-set-up-appliance-physical.md).
