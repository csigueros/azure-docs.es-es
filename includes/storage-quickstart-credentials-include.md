---
title: Archivo de inclusión
description: archivo de inclusión
services: storage
author: twooley
ms.service: storage
ms.topic: include
ms.date: 07/23/2021
ms.author: twooley
ms.custom: include file
ms.openlocfilehash: 463d251cc46e0a5735b1b5146bac30b3bc506f46
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2021
ms.locfileid: "114674387"
---
### <a name="copy-your-credentials-from-the-azure-portal"></a>Copia de las credenciales desde Azure Portal

Cuando la aplicación de ejemplo realiza una solicitud a Azure Storage, debe estar autorizada. Para autorizar una solicitud, agregue a la aplicación las credenciales de la cuenta de almacenamiento en forma de cadena de conexión. Para ver las credenciales de la cuenta de almacenamiento, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Busque su cuenta de almacenamiento.
3. En el panel del menú de la cuenta de almacenamiento, en **Seguridad y redes**, seleccione **Claves de acceso**. Aquí puede ver las claves de acceso de la cuenta, así como la cadena de conexión completa de cada clave.

    ![Captura de pantalla que muestra dónde se encuentra la configuración de las claves de acceso en Azure Portal](./media/storage-access-keys-portal/portal-access-key-settings.png)
 
1. En el panel **Claves acceso**, seleccione **Mostrar claves**.
1. En la sección **key1**, busque el valor de **Cadena de conexión**. Seleccione el icono **Copiar al portapapeles** para copiar la cadena de conexión. En la siguiente sección, agregará el valor de la cadena de conexión a una variable de entorno.

    ![Captura de pantalla que muestra cómo copiar una cadena de conexión desde Azure Portal](./media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>Configuración de la cadena de conexión de almacenamiento.

Una vez que haya copiado la cadena de conexión, escríbala en una variable de entorno nueva en la máquina local que ejecuta la aplicación. Para establecer la variable de entorno, abra una ventana de consola y siga las instrucciones de su sistema operativo. Reemplace `<yourconnectionstring>` por la cadena de conexión real.

#### <a name="windows"></a>Windows

```cmd
setx AZURE_STORAGE_CONNECTION_STRING "<yourconnectionstring>"
```

Después de agregar la variable de entorno en Windows, debe iniciar una nueva instancia de la ventana de comandos.

#### <a name="linux"></a>Linux

```bash
export AZURE_STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

#### <a name="macos"></a>macOS

```bash
export AZURE_STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

#### <a name="restart-programs"></a>Reiniciar programas

Después de agregar la variable de entorno, reinicie todos los programas en ejecución que necesiten leer esta variable. Por ejemplo, reinicie el entorno de desarrollo o el editor antes de continuar.
