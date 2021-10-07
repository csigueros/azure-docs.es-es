---
title: Solución de problemas de Azure Data Factory Studio
description: Conozca los pasos de solución de problemas relacionados con Azure Data Factory Studio.
author: ceespino
ms.service: data-factory
ms.subservice: authoring
ms.topic: troubleshooting
ms.date: 06/01/2021
ms.author: ceespino
ms.reviewer: susabat
ms.openlocfilehash: fda5131d4a4578de01dc5a34aada295c6b364848
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128663143"
---
# <a name="troubleshoot-azure-data-factory-studio-issues"></a>Solución de problemas de Azure Data Factory Studio

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este artículo se exploran métodos comunes de solución de problemas con [Azure Data Factory Studio](https://adf.azure.com), la interfaz de usuario del servicio.

## <a name="azure-data-factory-studio-fails-to-load"></a>Azure Data Factory Studio no se puede cargar

> [!NOTE]
> Azure Data Factory Studio admite oficialmente Microsoft Edge y Google Chrome. El uso de otros exploradores web puede dar lugar a un comportamiento inesperado o no documentado.

### <a name="third-party-cookies-blocked"></a>Cookies de terceros bloqueadas

Azure Data Factory Studio usa cookies de explorador para guardar el estado de la sesión del usuario y permitir experiencias interactivas de desarrollo y supervisión. Es posible que el explorador bloquee las cookies de terceros si usa una sesión de incógnito o tiene habilitado un bloqueador de anuncios. El bloqueo de las cookies de terceros puede causar problemas al cargar el portal.  Es posible que se le redirija a una página en blanco, a "https://adf.azure.com/accesstoken.html" o que encuentre un mensaje de advertencia que indica que las cookies de terceros están bloqueadas. Para solucionar este problema, habilite las opciones de cookies de terceros en el explorador mediante estos pasos:

# <a name="microsoft-edge"></a>[Microsoft Edge](#tab/edge)

#### <a name="allow-all-cookies"></a>Permitir todas las cookies

1. Vaya a **edge://settings/content/cookies** en el explorador.
1. Asegúrese de que **Permitir que los sitios guarden y lean datos de cookies** está habilitado y que la opción **Bloquear las cookies de terceros** está deshabilitada. 

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/edge-allow-all-cookies.png" alt-text="Permitir todas las cookies en Edge":::
1. Actualice Azure Data Factory Studio e inténtelo de nuevo.

#### <a name="only-allow-azure-data-factory-studio-to-use-cookies"></a>Permitir que solo Azure Data Factory Studio use cookies

Si no quiere permitir todas las cookies, puede permitirlas solo para ADF Studio:

1. Vaya a **edge://settings/content/cookies**.
1. En la sección **Permitir**, seleccione **Agregar** y agregue el sitio **adf.azure.com**. 

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/edge-allow-adf-cookies.png" alt-text="Agregar la experiencia de usuario de ADF a sitios permitidos en Edge":::
1. Actualice la experiencia de usuario de ADF e inténtelo de nuevo.

# <a name="google-chrome"></a>[Google Chrome](#tab/chrome)

#### <a name="allow-all-cookies"></a>Permitir todas las cookies

1. Vaya a **chrome://settings/cookies** en el explorador.
1. Seleccione la opción **Permitir todas las cookies**. 

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/chrome-allow-all-cookies.png" alt-text="Permitir todas las cookies en Chrome":::
1. Actualice Azure Data Factory Studio e inténtelo de nuevo.

#### <a name="only-allow-azure-data-factory-studio-to-use-cookies"></a>Permitir que solo Azure Data Factory Studio use cookies

Si no quiere permitir todas las cookies, puede permitirlas solo para ADF Studio:
1. Vaya a **chrome://settings/cookies**.
1. Seleccione **Añadir** en la opción **Sitios web que pueden usar cookies siempre**. 

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-1.png" alt-text="Agregar la experiencia de usuario de ADF a sitios permitidos en Chrome":::
1. Agregue el sitio **adf.azure.com**, active la opción **Todas las cookies** y guarde. 

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-2.png" alt-text="Permitir todas las cookies del sitio de la experiencia de usuario de ADF":::
1. Actualice la experiencia de usuario de ADF e inténtelo de nuevo.

---

## <a name="connection-failed-error-in-azure-data-factory-studio"></a>Error de conexión en Azure Data Factory Studio

A veces, es posible que vea un mensaje de error de conexión en Azure Data Factory Studio similar al de la captura de pantalla siguiente, por ejemplo, después de hacer clic en **Probar conexión** o **Vista previa**. Significa que la operación no se pudo realizar porque la máquina local no pudo conectarse al servicio ADF.

:::image type="content" source="media/data-factory-ux-troubleshoot-guide/connection-failed.png" alt-text="Error en la conexión":::

Para resolver el problema, puede intentar primero la misma operación con el modo de exploración InPrivate del explorador.

Si sigue sin funcionar, busque el **nombre del servidor** en el mensaje de error (en este ejemplo, **dpnortheurope.svc.datafactory.azure.com**), escriba el **nombre del servidor** directamente en la barra de direcciones del explorador. 

- Si ve 404 en el explorador, esto normalmente significa que el lado cliente es correcto y que el problema está en el lado del servicio ADF. Abra una incidencia de soporte técnico con el **identificador de actividad** del mensaje de error.

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/status-code-404.png" alt-text="Recurso no encontrado":::

- Si no ve un error 404 o un error similar a continuación en el explorador, esto suele significar que hay algún problema del lado cliente. Siga los pasos de solución de problemas.

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/client-side-error.png" alt-text="Error del lado cliente":::

Para solucionar más problemas, abra el **símbolo del sistema** y escriba `nslookup dpnortheurope.svc.datafactory.azure.com`. Una respuesta normal debería tener este aspecto:

:::image type="content" source="media/data-factory-ux-troubleshoot-guide/command-response-1.png" alt-text="Respuesta del comando 1":::

- Si ve una respuesta normal del Servicio de nombres de dominio (DNS), póngase en contacto con el soporte técnico de TI local para comprobar la configuración del firewall.  Asegúrese de que las conexiones HTTPS a este nombre de host no estén bloqueadas. Si el problema persiste, abra una incidencia de soporte técnico con ADF y proporcione el **identificador de actividad** del mensaje de error.

- Una respuesta DNS diferente de la respuesta normal anterior también podría significar que existe un problema con el servidor DNS al resolver el nombre DNS. En ese caso, puede que con cambiar el servidor DNS (por ejemplo, a Google DNS 8.8.8.8) se solucione el problema. 

- Si el problema persiste, podría probar con `nslookup datafactory.azure.com` y `nslookup azure.com` para ver en qué nivel se produjo el error en la resolución de DNS y enviar toda la información al equipo de soporte técnico de TI local o a su ISP para solucionar el problema. Si considera que el problema está todavía en Microsoft, abra una incidencia de soporte técnico con el **identificador de actividad** del mensaje de error.

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/command-response-2.png" alt-text="Respuesta del comando 2":::

## <a name="change-linked-service-type-warning-message-in-datasets"></a>Cambio del mensaje de advertencia del tipo de servicio vinculado en los conjuntos de datos

Es posible que se encuentre con el mensaje de advertencia que aparece a continuación cuando use un conjunto de datos de formato de archivo en una actividad y, más adelante, quiera apuntar a un servicio vinculado de un tipo diferente al que usó antes en ella (por ejemplo, del sistema de archivos a Azure Data Lake Storage Gen2).

:::image type="content" source="media/data-factory-ux-troubleshoot-guide/warning-message.png" alt-text="Mensaje de advertencia":::

Los conjuntos de datos con formato de archivo se pueden usar con todos los conectores basados en archivo; por ejemplo, puede configurar un conjunto de datos de Parquet en Blob de Azure o Azure Data Lake Storage Gen2. Tenga en cuenta que cada conector admite un conjunto diferente de configuración relacionada con el almacén de datos en la actividad y con un modelo de aplicación distinto. 

En la interfaz de usuario de creación de ADF, cuando se usa un conjunto de datos con formato de archivo en una actividad (incluidas las actividades Copia, Búsqueda, GetMetadata y Eliminar), y en un conjunto de datos que desea apuntar a un servicio vinculado de tipo diferente del actual (por ejemplo, cambiar de sistema de archivos a ADLS Gen2), verá este mensaje de advertencia. Para asegurarse de que se trata de un cambio limpio, tras su consentimiento, las canalizaciones y las actividades que hacen referencia a este conjunto de datos se modificarán para usar también el nuevo tipo, y cualquier configuración del almacén de datos existente que sea incompatible con el nuevo tipo se borrará cuando ya no se aplique.

Para obtener más información sobre la configuración del almacén de datos admitida para cada conector, puede ir al artículo del conector correspondiente -> propiedades de la actividad de copia para ver la lista de propiedades detallada. Consulte [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure Files](connector-azure-file-storage.md), [File System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md) y [SFTP](connector-sftp.md).


## <a name="could-not-load-resource-while-opening-pipeline"></a>No se pudo cargar el recurso al abrir la canalización. 

Cuando el usuario accede a una canalización mediante Azure Data Factory Studio, un mensaje de error indica que no se pudo cargar el recurso "xxxxxx".  Asegúrese de que no haya errores en el archivo JSON y de que existen recursos a los que se hace referencia. Estado: TypeError: no se puede leer la propiedad "xxxxx" de undefined, Posible motivo: TypeError: No se puede leer la propiedad "xxxxxxx" de undefined".

El origen del mensaje de error es un archivo JSON que describe la canalización. Esto sucede cuando el cliente usa la integración de Git y los archivos JSON de canalización se dañan por algún motivo. Verá un error (punto rojo con x) a la izquierda en el nombre de la canalización, como se muestra a continuación.

:::image type="content" source="media/data-factory-ux-troubleshoot-guide/pipeline-json-error.png" alt-text="Error JSON de canalización":::

La solución es corregir los archivos JSON al principio y, luego, volver a abrir la canalización mediante la herramienta de creación.


## <a name="next-steps"></a>Pasos siguientes

Para obtener ayuda para solucionar problemas, pruebe estos recursos:

* [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Solicitud de características de Data Factory](/answers/topics/azure-data-factory.html)
* [Foro de Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Información de Twitter sobre Data Factory](https://twitter.com/hashtag/DataFactory)
* [Vídeos de Azure](https://azure.microsoft.com/resources/videos/index/)
* [Página de preguntas y respuestas de Microsoft](/answers/topics/azure-data-factory.html)