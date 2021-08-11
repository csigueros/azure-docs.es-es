---
title: Solución de problemas con la experiencia de usuario de Azure Data Factory
description: Obtenga información acerca de la solución de problemas relacionados con la experiencia de usuario de Azure Data Factory.
author: ceespino
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 06/01/2020
ms.author: ceespino
ms.reviewer: susabat
ms.openlocfilehash: 3739fd5f758d0d41b5c6f9b5caf10edb2987073a
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/02/2021
ms.locfileid: "110784436"
---
# <a name="troubleshoot-azure-data-factory-ux-issues"></a>Solución de problemas con la experiencia de usuario de Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este artículo se exploran métodos comunes de solución de problemas con la experiencia de usuario de Azure Data Factory.

## <a name="adf-ux-not-loading"></a>No carga la experiencia de usuario de Azure Data Factory

> [!NOTE]
> La experiencia de usuario de Azure Data Factory admite oficialmente Microsoft Edge y Google Chrome. El uso de otros exploradores web puede dar lugar a un comportamiento inesperado o no documentado.

### <a name="third-party-cookies-blocked"></a>Cookies de terceros bloqueadas

La experiencia de usuario de ADF usa cookies del explorador para conservar la sesión del usuario y habilitar las experiencias interactivas de desarrollo y supervisión. Es posible que el explorador bloquee las cookies de terceros si usa una sesión de incógnito o tiene habilitado un bloqueador de anuncios. El bloqueo de las cookies de terceros puede provocar problemas al cargar el portal, como que se le redirija a una página en blanco, “https://adf.azure.com/accesstoken.html”, o que se muestre un mensaje de advertencia que indique que las cookies de terceros están bloqueadas. Para solucionar este problema, habilite las opciones de cookies de terceros en el explorador mediante estos pasos:

### <a name="google-chrome"></a>Google Chrome

#### <a name="allow-all-cookies"></a>Permitir todas las cookies

1. Vaya a **chrome://settings/cookies** en el explorador.
1. Seleccione la opción **Permitir todas las cookies**. 

    ![Permitir todas las cookies en Chrome](media/data-factory-ux-troubleshoot-guide/chrome-allow-all-cookies.png)
1. Actualice la experiencia de usuario de ADF e inténtelo de nuevo.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Permitir que solo la experiencia de usuario de ADF utilice cookies
Si no desea permitir todas las cookies, puede permitir que solo las utilice la experiencia de usuario de ADF:
1. Vaya a **chrome://settings/cookies**.
1. Seleccione **Añadir** en la opción **Sitios web que pueden usar cookies siempre**. 

    ![Agregar la experiencia de usuario de ADF a sitios permitidos en Chrome](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-1.png)
1. Agregue el sitio **adf.azure.com**, active la opción **Todas las cookies** y guarde. 

    ![Permitir todas las cookies del sitio de la experiencia de usuario de ADF](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-2.png)
1. Actualice la experiencia de usuario de ADF e inténtelo de nuevo.

### <a name="microsoft-edge"></a>Microsoft Edge

1. Vaya a **edge://settings/content/cookies** en el explorador.
1. Asegúrese de que **Permitir que los sitios guarden y lean datos de cookies** está habilitado y que la opción **Bloquear las cookies de terceros** está deshabilitada. 

    ![Permitir todas las cookies en Edge](media/data-factory-ux-troubleshoot-guide/edge-allow-all-cookies.png)
1. Actualice la experiencia de usuario de ADF e inténtelo de nuevo.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Permitir que solo la experiencia de usuario de ADF utilice cookies

Si no desea permitir todas las cookies, puede permitir que solo las utilice la experiencia de usuario de ADF:

1. Vaya a **edge://settings/content/cookies**.
1. En la sección **Permitir**, seleccione **Agregar** y agregue el sitio **adf.azure.com**. 

    ![Agregar la experiencia de usuario de ADF a sitios permitidos en Edge](media/data-factory-ux-troubleshoot-guide/edge-allow-adf-cookies.png)
1. Actualice la experiencia de usuario de ADF e inténtelo de nuevo.

## <a name="connection-failed-on-adf-ux"></a>Error en la conexión de la experiencia de usuario de ADF

A veces, puede aparecer el error "Error de conexión" en la interfaz de usuario de ADF similar a la captura de pantalla siguiente después de hacer clic en **Probar conexión**, **Versión preliminar**, etc. Significa que ADF no pudo realizar la operación porque no se puede conectar al servicio ADF desde la máquina.

![Error en la conexión](media/data-factory-ux-troubleshoot-guide/connection-failed.png)

Para resolver el problema, puede intentar primero la misma operación con el modo de exploración InPrivate del explorador.

Si sigue sin funcionar, busque el **nombre del servidor** en el mensaje de error (en este ejemplo, **dpnortheurope.svc.datafactory.azure.com**), escriba el **nombre del servidor** directamente en la barra de direcciones del explorador. 

- Si ve 404 en el explorador, esto normalmente significa que el lado cliente es correcto y que el problema está en el lado del servicio ADF. Abra una incidencia de soporte técnico con el **identificador de actividad** del mensaje de error.

    ![Recurso no encontrado](media/data-factory-ux-troubleshoot-guide/status-code-404.png)

- Si no ve un error 404 o un error similar a continuación en el explorador, esto suele significar que hay algún problema del lado cliente. Siga los pasos de solución de problemas.

    ![Error del lado cliente](media/data-factory-ux-troubleshoot-guide/client-side-error.png)

Para solucionar más problemas, abra el **símbolo del sistema** y escriba `nslookup dpnortheurope.svc.datafactory.azure.com`. Una respuesta normal debería tener este aspecto:

![Respuesta del comando 1](media/data-factory-ux-troubleshoot-guide/command-response-1.png)

- Si ve una respuesta DNS normal, póngase en contacto con el equipo de soporte técnico de TI local para comprobar la configuración del firewall para ver si la conexión HTTPS a este nombre de host está bloqueada o no. Si no se pudo resolver el problema, abra una incidencia de soporte técnico con el **identificador de actividad** del mensaje de error.

- Si esto es todo lo que puede ver, esto normalmente significa que hay algún problema con el servidor DNS al resolver el nombre DNS. Normalmente, el cambio de ISP (proveedor de servicios de Internet) o del DNS (por ejemplo, a Google DNS 8.8.8.8) podría ser una posible solución. Si el problema persiste, podría probar con `nslookup datafactory.azure.com` y `nslookup azure.com` para ver en qué nivel se produjo el error en la resolución de DNS y enviar toda la información al equipo de soporte técnico de TI local o a su ISP para solucionar el problema. Si considera que el problema está todavía en Microsoft, abra una incidencia de soporte técnico con el **identificador de actividad** del mensaje de error.

    ![Respuesta del comando 2](media/data-factory-ux-troubleshoot-guide/command-response-2.png)

## <a name="change-linked-service-type-in-datasets"></a>Cambio del tipo de servicio vinculado en conjuntos de datos

El conjunto de datos con formato de archivo se puede usar con todos los conectores basados en archivo; por ejemplo, puede configurar un conjunto de datos de Parquet en blobs de Azure o Azure Data Lake Storage Gen2. Tenga en cuenta que cada conector admite un conjunto diferente de configuración relacionada con el almacén de datos en la actividad y con un modelo de aplicación distinto. 

En la interfaz de usuario de creación de ADF, cuando se utiliza un conjunto de datos con formato de archivo en una actividad (incluidas las actividades Copia, Búsqueda, GetMetadata y Eliminar), y en el conjunto de archivos que desea apuntar a un servicio vinculado de tipo diferente del actual (por ejemplo, cambiar de sistema de archivos a ADLS Gen2), verá el siguiente mensaje de advertencia. Para asegurarse de que se trata de un cambio limpio, según su consentimiento, las canalizaciones y las actividades, que hacen referencia a este conjunto de datos, se modificarán para usar el nuevo tipo también, y cualquier configuración del almacén de datos existente, que es incompatible con el nuevo tipo, se borrará cuando ya no se aplique.

Para obtener más información sobre la configuración del almacén de datos admitida para cada conector, puede ir al artículo del conector correspondiente -> propiedades de la actividad de copia para ver la lista de propiedades detallada. Consulte [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [File System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md) y [SFTP](connector-sftp.md).

![Mensaje de advertencia](media/data-factory-ux-troubleshoot-guide/warning-message.png)

## <a name="could-not-load-resource-while-opening-pipeline"></a>No se pudo cargar el recurso al abrir la canalización. 

Cuando el usuario accede a la canalización mediante la herramienta de creación de la GUI de ADF, el mensaje de error indica que no se pudo cargar el recurso "xxxxxx". Asegúrese de que no hay errores en el archivo JSON y de que existen recursos a los que se hace referencia. Estado: TypeError: no se puede leer la propiedad "xxxxx" de undefined, Posible motivo: TypeError: No se puede leer la propiedad "xxxxxxx" de undefined".

El origen del mensaje de error es un archivo JSON que describe la canalización. Esto sucede cuando el cliente usa la integración de Git y los archivos JSON de canalización se dañan por algún motivo. Verá un error (punto rojo con x) a la izquierda en el nombre de la canalización, como se muestra a continuación.

![Error JSON de canalización](media/data-factory-ux-troubleshoot-guide/pipeline-json-error.png)

La solución es corregir los archivos JSON al principio y, a continuación, volver a abrir la canalización mediante la herramienta de creación.



## <a name="next-steps"></a>Pasos siguientes

Para obtener ayuda para solucionar problemas, pruebe estos recursos:

* [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Solicitud de características de Data Factory](https://feedback.azure.com/forums/270578-data-factory)
* [Foro de Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Información de Twitter sobre Data Factory](https://twitter.com/hashtag/DataFactory)
* [Vídeos de Azure](https://azure.microsoft.com/resources/videos/index/)
* [Página de preguntas y respuestas de Microsoft](/answers/topics/azure-data-factory.html)
