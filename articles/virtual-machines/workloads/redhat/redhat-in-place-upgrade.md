---
title: Actualización local de imágenes de Red Hat Enterprise Linux en Azure
description: Obtenga información sobre cómo realizar una actualización local desde imágenes de Red Hat Enterprise 7.x a la versión 8.x más reciente.
author: mathapli
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.openlocfilehash: ccd3e58babc6627a384636dbea28ef82969be9fc
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696749"
---
# <a name="red-hat-enterprise-linux-in-place-upgrades"></a>Actualizaciones locales de Red Hat Enterprise Linux

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux 

En este artículo se proporcionan instrucciones sobre cómo realizar una actualización local desde Red Hat Enterprise Linux (RHEL) 7 a Red Hat Enterprise Linux 8. Las instrucciones usan la herramienta `leapp` de Azure. Durante la actualización local, el sistema operativo RHEL 7 existente se sustituye por la versión de RHEL 8.

>[!Note] 
> Las ofertas de SQL Server en Red Hat Enterprise Linux no admiten actualizaciones locales en Azure.

## <a name="what-to-expect-during-the-upgrade"></a>Qué esperar durante la actualización
Durante la actualización, el sistema se reiniciará varias veces. El reinicio final actualiza la máquina virtual a la versión secundaria de RHEL 8 más reciente. 

El proceso de actualización puede tardar entre 20 minutos y 2 horas. El tiempo total depende de varios factores, como el tamaño de la máquina virtual y el número de paquetes instalados en el sistema.

## <a name="preparations"></a>Preparaciones
Red Hat y Azure recomiendan usar una actualización local para llevar a cabo la transición de un sistema a la versión principal siguiente. 

Antes de iniciar la actualización, tenga en cuenta las consideraciones siguientes. 

>[!Important] 
> Tome una instantánea de la imagen antes de iniciar la actualización.

* Asegúrese de estar usando la versión de RHEL 7 más reciente. Actualmente, la versión más reciente es RHEL 7.9. Si usa una versión bloqueada y no puede actualizar a RHEL 7.9, siga [estos pasos para cambiar a un repositorio que no sea compatible con la actualización extendida (EUS)](./redhat-rhui.md#switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock).

* Ejecute el comando siguiente para comprobar la actualización y ver si se realizará correctamente. El comando debe generar el archivo */var/log/leapp/leapp-report.txt*. Este archivo explica el proceso, lo que está ocurriendo y si es posible realizar la actualización.

    >[!NOTE]
    > Use la cuenta raíz para ejecutar los comandos de este artículo. 

    ```bash
    leapp preupgrade --no-rhsm
    ```
* Asegúrese de que la consola serie es funcional. Usará esta consola para la supervisión durante el proceso de actualización.

* Habilite el acceso raíz SSH en */etc/ssh/sshd_config*:
    1. Abra el archivo */etc/ssh/sshd_config*.
    1. Busque `#PermitRootLogin yes`.
    1. Quite el signo de número (`#`) para quitar la marca de comentario de la cadena.

## <a name="upgrade-steps"></a>Pasos de actualización

Siga estos pasos con cuidado. Se recomienda intentar la actualización en una máquina de prueba antes de probarla en las instancias de producción.

1. Realice una actualización `yum` para capturar los paquetes de cliente más frecuentes.
    ```bash
    yum update -y
    ```

1. Instale los `leapp-client-package`.
    ```bash
    yum install leapp-rhui-azure
    ```
    
1. En el [portal de Red Hat](https://access.redhat.com/articles/3664871), obtenga el archivo *leapp-data.tar.gz* que contiene *repomap.csv* y *pes-events.json*. Extraiga el archivo *leapp-data.tar.gz*.
    1. Descargue el archivo *leapp-data.tar.gz*.
    1. Extraiga el contenido y quite el archivo. Use el comando siguiente:
    ```bash
    tar -xzf leapp-data12.tar.gz -C /etc/leapp/files && rm leapp-data12.tar.gz
    ```

1. Agregue un archivo `answers` para `leapp`.
    ```bash
    leapp answer --section remove_pam_pkcs11_module_check.confirm=True --add
    ``` 

1. Inicie la actualización.
    ```bash
    leapp upgrade --no-rhsm
    ```
1.  Cuando el comando `leapp upgrade` finalice sin errores, reinicie manualmente el sistema para completar el proceso. El sistema no está disponible porque se reiniciará un par de veces. Supervise el proceso a través de la consola serie.

1.  Compruebe que la actualización se completó correctamente.
    ```bash
    uname -a && cat /etc/redhat-release
    ```

1. Cuando finalice la actualización, quite el acceso SSH raíz:
    1. Abra el archivo */etc/ssh/sshd_config*.
    1. Busque `#PermitRootLogin yes`.
    1. Agregue un signo de número (`#`) para comentar la cadena.

1. Reinicie el servicio SSHD para aplicar los cambios.
    ```bash
    systemctl restart sshd
    ```
## <a name="common-problems"></a>Problemas comunes

Los errores siguientes suelen ocurrir cuando se produce un error en el proceso `leapp preupgrade` o en el proceso `leapp upgrade`:

* **Error**: no se encontraron coincidencias para los patrones de complemento deshabilitados siguientes.

    ```plaintext
    STDERR:
    No matches found for the following disabled plugin patterns: subscription-manager
    Warning: Packages marked by Leapp for upgrade not found in repositories metadata: gpg-pubkey
    ```

    **Solución**: deshabilite el complemento subscription-manager. Para hacerlo, edite el archivo */etc/yum/pluginconf.d/subscription-manager.conf* y cambie `enabled` a `enabled=0`.

    Este error se produce cuando el complemento `yum` de subscription-manager que está habilitado no se usa para las máquinas virtuales `PAYG`.

* **Error**: problemas posibles con el inicio de sesión remoto mediante la raíz.

    Puede que vea este error cuando se produzca un error en `leapp preupgrade`:

    ```structured-text
    ============================================================
                         UPGRADE INHIBITED
    ============================================================
    
    Upgrade has been inhibited due to the following problems:
        1. Inhibitor: Possible problems with remote login using root account
    Consult the pre-upgrade report for details and possible remediation.
    
    ============================================================
                         UPGRADE INHIBITED
    ============================================================
    ```
    **Solución**: habilite el acceso raíz en */etc/sshd_config*.

    Este error se produce cuando el acceso SSH raíz no está habilitado en */etc/sshd_config*. Para más información, consulte la sección [Preparaciones](#preparations) de este artículo. 


## <a name="next-steps"></a>Pasos siguientes
* Obtenga más información sobre las [imágenes de Red Hat en Azure](./redhat-images.md).
* Obtenga más información sobre la [infraestructura de actualización de Red Hat](./redhat-rhui.md).
* Obtenga más información sobre la [oferta BYOS de RHEL](./byos.md).
* Para más información sobre los procesos de actualización local de Red Hat, consulte la documentación de Red Hat sobre la [actualización de RHEL 7 a RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index).
* Para más información sobre las directivas de soporte técnico de Red Hat para todas las versiones de Red Hat, consulte la documentación de Red Hat sobre el [ciclo de vida de Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).