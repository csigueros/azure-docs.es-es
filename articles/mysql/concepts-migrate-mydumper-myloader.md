---
title: Migración de bases de datos grandes a Azure Database for MySQL mediante mydumper/myloader
description: En este artículo se explican dos formas habituales de hacer una copia de seguridad y restaurar bases de datos en Azure Database for MySQL mediante la herramienta mydumper/myloader.
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: conceptual
ms.date: 06/18/2021
ms.openlocfilehash: e295e967b9164e9ab4744d18f407a18feb32481e
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323171"
---
# <a name="migrate-large-databases-to-azure-database-for-mysql-using-mydumpermyloader"></a>Migración de bases de datos grandes a Azure Database for MySQL mediante mydumper/myloader

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

Azure Database for MySQL es un servicio administrado que se usa para ejecutar, administrar y escalar bases de datos MySQL de alta disponibilidad en la nube. Para migrar bases de datos MySQL de más de 1 TB a Azure Database for MySQL, considere la posibilidad de usar herramientas de la comunidad como [mydumper/myloader](https://centminmod.com/mydumper.html), las cuales proporcionan las siguientes ventajas:

* Paralelismo, para ayudar a reducir el tiempo de migración.
* Mejor rendimiento, evitando rutinas de conversión de juegos de caracteres costosas.
* Formato de salida, con archivos independientes para tablas, metadatos, etc., lo cual facilita la visualización y el análisis de datos. Coherencia, manteniendo la instantánea en todos los subprocesos.
* Posiciones de registro principal y de réplica precisas.
* Administración sencilla, ya que admiten expresiones regulares compatibles con Perl (PCRE) para especificar las inclusiones y exclusiones de bases de datos y tablas.
* El esquema y los datos van juntos. No es necesario administrarlos por separado como en otras herramientas de migración lógicas.

En este inicio rápido se muestra cómo instalar, realizar copias de seguridad y restaurar una base de datos MySQL mediante mydumper/myloader.

## <a name="prerequisites"></a>Requisitos previos

Antes de empezar a migrar la base de datos MySQL, debe hacer lo siguiente:

1. Cree un servidor de Azure Database for MySQL mediante [Azure Portal](./flexible-server/quickstart-create-server-portal.md).

2. Cree una máquina virtual de Azure que ejecute Linux mediante [Azure Portal](../virtual-machines/linux/quick-create-portal.md) (preferiblemente Ubuntu).
    > [!Note]
    > Antes de instalar las herramientas, tenga en cuenta los siguientes puntos:
    >
    > * Si el origen es local y tiene una conexión con gran ancho de banda a Azure (mediante ExpressRoute), considere la posibilidad de instalar la herramienta en una máquina virtual de Azure.<br> 
    > * Si tiene alguna dificultad en el ancho de banda entre el origen y el destino, considere la posibilidad de instalar mydumper cerca del origen y myloader cerca del servidor de destino. Puede usar herramientas como **[Azcopy](../storage/common/storage-use-azcopy-v10.md)** para trasladar los datos desde el entorno local u otras soluciones en la nube a Azure.

3. Instale el cliente mysql y realice los pasos siguientes:

4. 

    * Actualice el índice del paquete en la máquina virtual de Azure que ejecuta Linux mediante la ejecución del siguiente comando:
        ```bash
        $ sudo apt update
        ```
    * Para instalar el paquete del cliente mysql, ejecute el comando siguiente:
        ```bash
        $ sudo apt install mysql-client
        ```

## <a name="install-mydumpermyloader"></a>Instalación de mydumper/myloader

Para instalar mydumper/myloader, siga estos pasos.

1. En función de la distribución del sistema operativo, descargue el paquete adecuado para mydumper/myloader y ejecute el siguiente comando:
2. 
    ```bash
    $ wget https://github.com/maxbube/mydumper/releases/download/v0.10.1/mydumper_0.10.1-2.$(lsb_release -cs)_amd64.deb
    ```

    > [!Note]
    > $(lsb_release -cs) le ayuda a identificar la distribución.

3. Para instalar el paquete .deb para mydumper, ejecute el siguiente comando:

    ```bash
    $ dpkg -i mydumper_0.10.1-2.$(lsb_release -cs)_amd64.deb
    ```

    > [!Tip]
    > El comando que use para instalar el paquete variará en función de la distribución de Linux que tenga, ya que los instaladores son diferentes. Mydumper/myloader está disponible para las siguientes distribuciones: Fedora, RedHat, Ubuntu, Debian, CentOS, openSUSE y MacOSX. Para más información, consulte **[Instalación de mydumper](https://github.com/maxbube/mydumper#how-to-install-mydumpermyloader)** .

## <a name="create-a-backup-using-mydumper"></a>Creación de una copia de seguridad mediante mydumper

* Para crear una copia de seguridad mediante mydumper, ejecute el siguiente comando:

    ```bash
    $ mydumper --host=<servername> --user=<username> --password=<Password> --outputdir=./backup --rows=100000 --compress --build-empty-files --threads=16 --compress-protocol --trx-consistency-only --ssl  --regex '^(<Db_name>\.)' -L mydumper-logs.txt
    ```

Este comando emplea las variables siguientes:

* **--host:** host al que se desea conectar
* **--user:** nombre de usuario con los privilegios necesarios 
* **--password:** contraseña del usuario
* **--rows:** intente dividir las tablas en fragmentos con este número de filas.
* **--outputdir:** directorio en el que se van a volcar los archivos de salida
* **--regex:** expresión regular para la coincidencia de base de datos.
* **--trx-consistency-only:** solo coherencia transaccional
* **--threads:** número de subprocesos que se van a usar, el valor predeterminado es 4. Se recomienda usar un valor igual a 2 veces el valor del núcleo virtual del equipo.

    >[!Note] 
    >Para más información sobre otras opciones que se pueden usar con mydumper, ejecute el siguiente comando: **mydumper --help**. Para más información, consulte la [documentación de mydumper\myloader](https://centminmod.com/mydumper.html).<br>
    Para volcar varias bases de datos en paralelo, puede modificar la variable regex como se indica en el ejemplo: **regex ’^(DbName1\.|DbName2\.)**

## <a name="restore-your-database-using-myloader"></a>Restauración de la base de datos mediante myloader

* Para restaurar la base de datos de la que ha hecho una copia de seguridad mediante mydumper, ejecute el siguiente comando:

    ```bash
    $ myloader --host=<servername> --user=<username> --password=<Password> --directory=./backup --queries-per-transaction=500 --threads=16 --compress-protocol --ssl --verbose=3 -e 2>myloader-logs.txt
    ```

Este comando emplea las variables siguientes:

* **--host:** host al que se desea conectar
* **--user:** nombre de usuario con los privilegios necesarios 
* **--password:** contraseña del usuario
* **--directory:** ubicación en la que se ha almacenado la copia de seguridad. 
* **--queries-per-transaction:** se recomienda establecer en un valor no superior a 500
* **--threads:** número de subprocesos que se van a usar, el valor predeterminado es 4. Se recomienda usar un valor igual a 2 veces el valor del núcleo virtual del equipo.

> [!Tip]
> Para más información sobre otras opciones que se pueden usar con myloader, ejecute el siguiente comando: **myloader --help**.

Una vez restaurada la base de datos, siempre se recomienda validar la coherencia de los datos entre las bases de datos de origen y de destino.

> [!Note]
> Envíe cualquier problema o comentario relacionado con las herramientas mydumper/myloader **[aquí](https://github.com/maxbube/mydumper/issues)** .

## <a name="next-steps"></a>Pasos siguientes

* Obtenga más información sobre el [proyecto mydumper/myloader en GitHub](https://github.com/maxbube/mydumper).
* Aprenda [cómo migrar bases de datos de MySQL grandes](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/best-practices-for-migrating-large-databases-to-azure-database/ba-p/1362699).
* [Tutorial: Migración con tiempo de inactividad mínimo de Azure Database for MySQL con servidor único a Azure Database for MySQL con servidor flexible](howto-migrate-single-flexible-minimum-downtime.md)
* Más información sobre la replicación de datos de entrada en [Replicación de datos en el servidor flexible de Azure Database for MySQL (versión preliminar)](flexible-server/concepts-data-in-replication.md) y [Configuración de la replicación de datos de entrada del servidor flexible de Azure Database for MySQL](./flexible-server/how-to-data-in-replication.md)
* [Errores de migración](./howto-troubleshoot-common-errors.md) que se experimentan con frecuencia