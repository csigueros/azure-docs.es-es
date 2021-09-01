---
title: Configuración de un dispositivo de Azure Migrate con un script
description: Aprenda a configurar un dispositivo de Azure Migrate con un script
ms.topic: how-to
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.date: 03/18/2021
ms.openlocfilehash: bcd455590e804ad337f25afbd38d729f03ef3dc4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743236"
---
# <a name="set-up-an-appliance-with-a-script"></a>Configuración de un dispositivo con un script

Siga este artículo para implementar un [dispositivo de Azure Migrate](./migrate-appliance-architecture.md) mediante un script de PowerShell para:
- detección, evaluación y replicación sin agente de servidores que se ejecutan en el entorno de VMware
- detección y evaluación de servidores que se ejecutan en el entorno de Hyper-V.

Puede implementar el dispositivo para servidores de Hyper-V y VMware mediante un script o mediante una plantilla (OVA/VHD) que se descarga de Azure Portal. El uso de un script es útil si no se puede crear una aplicación mediante la plantilla descargada.

- Para usar una plantilla, siga los tutoriales de [VMware](./tutorial-discover-vmware.md) e [Hyper-V](./tutorial-discover-hyper-v.md).
- Para configurar una aplicación para servidores físicos, solo puede usar un script. Siga [este artículo](how-to-set-up-appliance-physical.md).
- Para configurar un dispositivo en una nube de Azure Government, solo puede usar un script. Siga [este artículo](deploy-appliance-script-government.md).

## <a name="prerequisites"></a>Requisitos previos

Puede usar el script para implementar el dispositivo de Azure Migrate en un servidor existente en el entorno de VMware o Hyper-V.

- El servidor que hospeda el dispositivo debe cumplir los siguientes requisitos de hardware y sistema operativo:

Escenario | Requisitos
--- | ---
VMware | Windows Server 2016, con 32 GB de memoria, ocho vCPU y alrededor de 80 GB de almacenamiento en disco.
Hyper-V | Windows Server 2016, con 16 GB de memoria, ocho vCPU y alrededor de 80 GB de almacenamiento en disco.

- El servidor también necesita un conmutador virtual externo. Requiere una dirección IP estática o dinámica. 
- Antes de implementar el dispositivo, consulte los requisitos detallados del dispositivo para [VMware](migrate-appliance.md#appliance---vmware) e [Hyper-V](migrate-appliance.md#appliance---hyper-v).
- Si ejecuta el script en un servidor con el dispositivo de Azure Migrate ya instalado, puede elegir limpiar la configuración existente e instalar un nuevo dispositivo con la configuración deseada. Al ejecutar el script, recibirá una notificación como se muestra a continuación:

    ![Instalación del dispositivo con la configuración deseada](./media/deploy-appliance-script/script-reconfigure-appliance.png)

## <a name="set-up-the-appliance-for-vmware"></a>Instalación de la aplicación para VMware

1. Para instalar el dispositivo, descargue el archivo comprimido llamado AzureMigrateInstaller.zip desde el portal o desde [aquí](https://go.microsoft.com/fwlink/?linkid=2116601).
1. Extraiga el contenido en el servidor en el que desea implementar el dispositivo.
1. Ejecute el script de PowerShell para iniciar el administrador de configuración del dispositivo.
1. Instale el dispositivo y configúrelo por primera vez.

### <a name="verify-security"></a>Comprobación de la seguridad

Compruebe que el archivo comprimido es seguro, antes de implementarlo.

1. En el servidor en el que descargó el archivo, abra una ventana de comandos de administrador.
2. Ejecute el siguiente comando para generar el código hash para el archivo ZIP:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Ejemplo de uso: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
3.  Compruebe la versión más reciente del dispositivo y el valor hash:

    **Descargar** | **Valor del código hash**
    --- | ---
    [La versión más reciente](https://go.microsoft.com/fwlink/?linkid=2116601) | b4668be44c05836bf0f2ac1c8b1f48b7a9538afcf416c5212c7190629e3683b2

> [!NOTE]
> Se puede usar el mismo script para configurar el dispositivo de VMware para la nube pública de Azure o la nube de Azure Government.

### <a name="run-the-script"></a>Ejecute el script.

1. Extraiga el archivo comprimido en la carpeta del servidor que hospedará el dispositivo.  No ejecute el script en un servidor con un dispositivo de Azure Migrate existente.
2. Inicie PowerShell en el servidor anterior con privilegios administrativos (elevados).
3. Cambie el directorio de PowerShell a la carpeta en la que se ha extraído el contenido del archivo comprimido descargado.
4. Ejecute el script denominado **AzureMigrateInstaller.ps1** ejecutando el comando siguiente:

    
    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1 ```

5. Seleccione entre las opciones de escenario, nube y conectividad para implementar un dispositivo con la configuración deseada. Por ejemplo, la selección que se muestra a continuación configura un dispositivo para detectar, evaluar y migrar **servidores que se ejecutan en el entorno de VMware** a un proyecto de Azure Migrate con **conectividad predeterminada** (punto de conexión público) en la **nube pública de Azure**.

    :::image type="content" source="./media/deploy-appliance-script/script-vmware-default-inline.png" alt-text="Captura de pantalla que muestra cómo configurar un dispositivo para VMware con la configuración deseada." lightbox="./media/deploy-appliance-script/script-vmware-default-expanded.png":::

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

Asegúrese de que la aplicación puede conectarse a las direcciones URL de Azure para la nube [pública](migrate-appliance.md#public-cloud-urls).

## <a name="set-up-the-appliance-for-hyper-v"></a>Configure la aplicación para Hyper-V

1. Para instalar el dispositivo, descargue el archivo comprimido llamado AzureMigrateInstaller.zip desde el portal o desde [aquí](https://go.microsoft.com/fwlink/?linkid=2116657).
1. Extraiga el contenido en el servidor en el que desea implementar el dispositivo.
1. Ejecute el script de PowerShell para iniciar el administrador de configuración del dispositivo.
1. Instale el dispositivo y configúrelo por primera vez.

### <a name="verify-security"></a>Comprobación de la seguridad

Compruebe que el archivo comprimido es seguro, antes de implementarlo.

1. En el servidor en el que descargó el archivo, abra una ventana de comandos de administrador.
2. Ejecute el siguiente comando para generar el código hash para el archivo ZIP:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Ejemplo de uso: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
3.  Compruebe la versión más reciente del dispositivo y el valor hash:

    **Descargar** | **Valor del código hash**
    --- | ---
    [La versión más reciente](https://go.microsoft.com/fwlink/?linkid=2116657) | b4668be44c05836bf0f2ac1c8b1f48b7a9538afcf416c5212c7190629e3683b2

> [!NOTE]
> Se puede usar el mismo script para configurar el dispositivo de Hyper-V para la nube pública de Azure o la nube de Azure Government.

### <a name="run-the-script"></a>Ejecute el script.

1. Extraiga el archivo comprimido en la carpeta del servidor que hospedará el dispositivo.  No ejecute el script en un servidor con un dispositivo de Azure Migrate existente.
2. Inicie PowerShell en el servidor anterior con privilegios administrativos (elevados).
3. Cambie el directorio de PowerShell a la carpeta en la que se ha extraído el contenido del archivo comprimido descargado.
4. Ejecute el script denominado **AzureMigrateInstaller.ps1** ejecutando el comando siguiente:

    
    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1 ```

5. Seleccione entre las opciones de escenario, nube y conectividad para implementar un dispositivo con la configuración deseada. Por ejemplo, la selección que se muestra a continuación configura un dispositivo para detectar y evaluar **servidores que se ejecutan en el entorno de Hyper-V** a un proyecto de Azure Migrate con **conectividad predeterminada** (punto de conexión público) en la **nube pública de Azure**.

    :::image type="content" source="./media/deploy-appliance-script/script-hyperv-default-inline.png" alt-text="Captura de pantalla que muestra cómo configurar un dispositivo de Hyper-V con la configuración deseada." lightbox="./media/deploy-appliance-script/script-hyperv-default-expanded.png":::

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

Asegúrese de que la aplicación puede conectarse a las direcciones URL de Azure para la nube [pública](migrate-appliance.md#public-cloud-urls).

## <a name="next-steps"></a>Pasos siguientes

Después de implementar la aplicación, es preciso que la configure y la registre en el proyecto.

- Configure el dispositivo para [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance).
- Configure el dispositivo para [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
