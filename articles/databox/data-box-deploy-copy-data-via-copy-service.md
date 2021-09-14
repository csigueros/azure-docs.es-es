---
title: 'Tutorial: Uso del servicio de copia de datos para realizar copias en el dispositivo'
titleSuffix: Azure Data Box
description: En este tutorial aprenderá a copiar datos en el dispositivo Azure Data Box mediante el servicio de copia de datos
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 08/26/2021
ms.author: alkohli
ms.openlocfilehash: 857b9ece50b4852e4d459915f9fd13477628781a
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123429287"
---
# <a name="tutorial-use-the-data-copy-service-to-copy-data-into-azure-data-box-preview"></a>Tutorial: Uso del servicio de copia de datos para copiar datos en Azure Data Box (versión preliminar)

En este tutorial se describe cómo realizar la ingesta de datos mediante el servicio de copia de datos sin host intermedio. El servicio de copia de datos se ejecuta localmente en Microsoft Azure Data Box, se conecta al dispositivo de almacenamiento conectado a la red (NAS) a través de SMB y copia los datos en Data Box.

Use el servicio de copia de datos:

- En entornos de NAS donde pudiera no haber hosts intermedios disponibles.
- Con archivos pequeños que demoran durante semanas la ingesta y carga de datos. El servicio de copia de datos mejora significativamente el tiempo de ingesta y de carga de archivos pequeños.

En este tutorial, aprenderá a:

> [!div class="checklist"]
>
> * Copia de datos a un dispositivo Data Box

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar, asegúrese de que:

1. Ha completado el tutorial: [Configuración de Azure Data Box](data-box-deploy-set-up.md).
2. Ha recibido su dispositivo Data Box y el estado del pedido en el portal es **Entregados**.
3. Tiene las credenciales del dispositivo NAS de origen que se conecta para la copia de datos.
4. Está conectado a una red de alta velocidad. Es muy recomendable tener una conexión de Ethernet de 10 Gigabit (GbE) como mínimo. Si no hay una conexión de 10 GbE disponible, puede usar un vínculo de datos de 1 GbE, pero las velocidades de copia se verán afectadas.

## <a name="copy-data-to-data-box"></a>Copia de datos a un dispositivo Data Box

Una vez conectado al dispositivo NAS, el siguiente paso es copiar los datos. Antes de comenzar la copia de datos, revise las consideraciones siguientes:

* Al copiar los datos, asegúrese de que el tamaño de los datos se ajusta a los límites descritos en el artículo sobre los [límites de almacenamiento de Azure y de Data Box](data-box-limits.md).

* Si los datos cargados por Data Box los están cargando a la vez otras aplicaciones fuera de Data Box, pueden producirse errores en el trabajo de carga y daños en los datos.

* Si se están modificando los datos mientras el servicio de copia de datos los está leyendo, podrían aparecer errores o daños en los datos.

> [!IMPORTANT]
> Asegúrese de conservar una copia de los datos de origen hasta que pueda confirmar que Data Box los ha transferido a Azure Storage.

Para copiar datos utilizando el servicio de copia de datos, tendrá que crear un trabajo:

1. En la interfaz de usuario de la web local del dispositivo Data Box, vaya a **Administrar** > **Copiar datos**.
2. En la página **Copiar datos**, seleccione **Crear**.

    ![Selección de Crear en la página "Copiar datos"](media/data-box-deploy-copy-data-via-copy-service/click-create.png)

3. En el cuadro de diálogo **Configurar trabajo e iniciar**, rellene los siguientes campos:
    
    |Campo                          |Value    |
    |-------------------------------|---------|
    |**Nombre del trabajo**                       |Un nombre exclusivo de menos de 230 caracteres para el trabajo. En el nombre del trabajo no se permiten los siguientes caracteres: \<, \>, \|, \?, \*, \\, \:, \/, \\ y \.         |
    |**Ubicación de origen**                |Proporcione la ruta de acceso SMB para el origen de datos en el formato: `\\<ServerIPAddress>\<ShareName>` o `\\<ServerName>\<ShareName>`.        |
    |**Nombre de usuario**                       |Nombre de usuario en formato `\\<DomainName><UserName>` para acceder al origen de datos. Si hay un administrador local conectándose, necesitará permisos de seguridad explícitos. Haga clic con el botón derecho en la carpeta, seleccione **Propiedades** y, luego, **Seguridad**. Esto debería agregar al administrador en la pestaña **Seguridad**.       |
    |**Contraseña**                       |Contraseña para acceder al origen de datos.           |
    |**Cuenta de almacenamiento de destino**    |Seleccione la cuenta de almacenamiento de destino para cargar datos desde la lista.         |
    |**Tipo de destino**       |Seleccione el tipo de almacenamiento de destino de la lista: **Blob en bloques**, **Blob en páginas** o **Azure Files**.        |
    |**Contenedor o recurso compartido de destino**    |Escriba el nombre del contenedor o el recurso compartido del cual desee cargar datos en la cuenta de almacenamiento de destino. El nombre puede ser un nombre de recurso compartido o de contenedor. Por ejemplo, use `myshare` o `mycontainer`. Además, puede escribir el nombre en el formato `sharename\directory_name` o `containername\virtual_directory_name`.        |
    |**Copiar los archivos que coincidan con el patrón**    | Puede escribir el patrón de coincidencia de nombre de archivo de las siguientes dos maneras:<ul><li>**Uso de expresiones de caracteres comodín:** En las expresiones de caracteres comodín solo se admiten `*` y `?`. Por ejemplo, la expresión `*.vhd` coincide con todos los archivos que tienen la extensión `.vhd`. De forma similar, `*.dl?` coincide con todos los archivos con la extensión `.dl` o que empiezan por `.dl`, como `.dll`. Del mismo modo, `*foo` coincide con todos los archivos cuyos nombres terminan por `foo`.<br>Puede especificar directamente la expresión de caracteres comodín en el campo. De forma predeterminada, el valor especificado en el campo se trata como expresión comodín.</li><li>**Uso de expresiones regulares:** se admiten expresiones regulares basadas en POSIX. Por ejemplo, una expresión regular `.*\.vhd` coincidirá con todos los archivos que tienen la extensión `.vhd`. Para la expresión regular, proporcione el `<pattern>` directamente como `regex(<pattern>)`. Para más información sobre las expresiones regulares, vaya a [Lenguaje de expresiones regulares - Referencia rápida](/dotnet/standard/base-types/regular-expression-language-quick-reference).</li><ul>|
    |**Optimización de archivos**              |Cuando esta característica está habilitada, se empaquetan los archivos inferiores a 1 MB durante la ingesta. Esto acelera la copia de datos para archivos pequeños. También ahorra un tiempo importante cuando el número de archivos supera el número de directorios.</br>Si usa la optimización de archivos:<ul><li>Después de ejecutar la preparación para el envío, puede [descargar un archivo BOM](data-box-logs.md#inspect-bom-during-prepare-to-ship), que muestra los nombres de archivo originales, para ayudarle a asegurarse de que se copian todos los archivos adecuados.</li><li>No elimine los archivos empaquetados, que se identifican mediante un GUID como nombre de archivo. Si elimina un archivo empaquetado, el archivo original no se cargará durante futuras copias de datos.</li><li>No copie con otros protocolos, como SMB, NFS o API REST, los mismos archivos que copia con el servicio de copia. El uso de protocolos diferentes puede provocar conflictos y errores durante las cargas de datos.</li></ul>    |
 
4. Seleccione **Inicio**. Se validan las entradas y, si la validación es correcta, se inicia el trabajo. El trabajo puede tardar unos minutos en iniciarse.

    ![Inicio de un trabajo desde el cuadro de diálogo "Configurar trabajo e iniciar"](media/data-box-deploy-copy-data-via-copy-service/configure-and-start.png)

5. Se crea un trabajo con la configuración especificada. Los trabajos se pueden pausar, reanudar, cancelar o reiniciar. Seleccione la casilla situada junto al nombre del trabajo y seleccione el botón correspondiente.

    ![Administración un trabajo en la página "Copiar datos"](media/data-box-deploy-copy-data-via-copy-service/select-job.png)
    
    - Puede pausar un trabajo si está afectando a los recursos del dispositivo NAS durante las horas punta:

        ![Pausa de un trabajo en la página "Copiar datos"](media/data-box-deploy-copy-data-via-copy-service/pause-job.png)

        Puede reanudar el trabajo más adelante durante las horas con menos actividad:

        ![Reanudación de un trabajo en la página "Copiar datos"](media/data-box-deploy-copy-data-via-copy-service/resume-job.png)

    - Puede cancelar un trabajo en cualquier momento:

        ![Cancelación de un trabajo en la página "Copiar datos"](media/data-box-deploy-copy-data-via-copy-service/cancel-job.png)
        
        Al cancelar un trabajo se necesita confirmación:

        ![Confirmar la cancelación de un trabajo](media/data-box-deploy-copy-data-via-copy-service/confirm-cancel-job.png)

        Si decide cancelar un trabajo, los datos que ya se hayan copiado no se eliminan. Para eliminar cualquier dato que haya copiado en el dispositivo Data Box, restablezca el dispositivo.

        ![Restablecimiento de un dispositivo](media/data-box-deploy-copy-data-via-copy-service/reset-device.png)

        >[!NOTE]
        > Si cancela o pausa un trabajo, los archivos de gran tamaño podrían haberse copiado solo parcialmente. Estos archivos parcialmente copiados se cargan en el mismo estado en Azure. Al cancelar o pausar un trabajo, asegúrese de que los archivos se hayan copiado correctamente. Para validar los archivos, examine los recursos compartidos de SMB o descargue el archivo BOM.

    - Puede reiniciar un trabajo si se ha producido un fallo debido a un error transitorio, como un problema de red. Pero no puede reiniciar un trabajo si ha alcanzado un estado terminal, como **Correcto** o **Completado con errores**. Los errores en el trabajo pueden deberse a problemas de nomenclatura o de tamaño de los archivos. Estos errores se registran, pero no se puede reiniciar el trabajo una vez que se ha completado.

        ![Reiniciar un trabajo con error](media/data-box-deploy-copy-data-via-copy-service/restart-failed-job.png)

        Si experimenta un error y no se puede reiniciar el trabajo, descargue los registros de error y examine el error en los archivos de registro. Una vez que haya corregido el problema, cree un nuevo trabajo para copiar los archivos. También puede [copiar los archivos mediante SMB](data-box-deploy-copy-data.md).
    
    - En esta versión no puede eliminar un trabajo.
    
    - Puede crear un número ilimitado de trabajos, pero solo puede ejecutar un máximo de 10 trabajos en paralelo en un momento dado.
    - Si la opción **Optimización de archivos** está activada, los archivos pequeños se empaquetan en la ingesta para mejorar el rendimiento de copia. En estos casos, verá un archivo empaquetado (que tendrá un GUID como nombre de archivo). No elimine este archivo. Se desempaquetará durante la carga.

6. Mientras el trabajo está en curso, en la página **Copiar datos**:

    - Puede ver el estado del trabajo de copia en la columna **Estado**. El estado puede ser:
        - **Ejecución**
        - **Erróneo**
        - **Correcto**
        - **Pausando**
        - **En pausa**
        - **Cancelando**
        - **Canceled**
        - **Completed with errors** (Completado con errores)
    - En la columna **Archivos**, puede ver el número de archivos que se va a copiar y su tamaño total.
    - En la columna **Procesado**, puede ver el número de archivos que se va a procesar y su tamaño total.
    - En la columna **Detalles del trabajo**, seleccione **Ver** para ver los detalles del trabajo.
    - Si se produce algún error durante el proceso de copia, como se muestra en la columna **N. º de errores**, vaya a la columna **Registro de errores** y descargue los registros de error para la solución de problemas.

Espere a que finalice el trabajo de copia. Como algunos errores solo se registran en la página **Conectar y copiar**, asegúrese de que el trabajo de copia haya finalizado sin errores antes de ir al paso siguiente.

![Sin errores en la página "Conectar y copiar"](media/data-box-deploy-copy-data-via-copy-service/verify-no-errors-on-connect-and-copy.png)

Para garantizar la integridad de los datos, la suma de comprobación se calcula a medida que los datos se copian. Una vez completada la copia, seleccione **Ver panel** para comprobar el espacio utilizado y el espacio disponible en el dispositivo.
    
![Comprobación del espacio libre y utilizado en el panel](media/data-box-deploy-copy-data-via-copy-service/verify-used-space-dashboard.png)

Una vez finalizado el trabajo de copia, puede seleccionar **Preparar para enviar**.

>[!NOTE]
> **Preparar para enviar** no se puede ejecutar con trabajos de copia en curso.

## <a name="next-steps"></a>Pasos siguientes

En el siguiente tutorial aprenderá a enviar el dispositivo Data Box a Microsoft.

> [!div class="nextstepaction"]
> [Envío del dispositivo Azure Data Box a Microsoft](./data-box-deploy-picked-up.md)