---
title: Uso de Azure Lab Services para hackathon
description: En este artículo se describe cómo usar Azure Lab Services para crear laboratorios que puede usar para ejecutar hackathons.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 00a00228231152a1ac0817ea1562f84635d7090e
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130181129"
---
# <a name="use-azure-lab-services-for-your-next-hackathon"></a>Uso de Azure Lab Services para su próximo hackathon
Azure Lab Services está diseñado para ser ligero y fácil de usar, por lo que puede poner en marcha rápidamente un laboratorio de máquinas virtuales (VM) nuevo para el hackathon.  Use la lista de comprobación siguiente para asegurarse de que el hackathon se desarrolle lo más fluidamente posible. Esta lista de comprobación la debe completar el departamento de TI o los profesores responsables de crear y administrar el laboratorio para el hackathon. 

A fin de usar Lab Services para el hackathon, asegúrese de que tanto la cuenta de laboratorio como el laboratorio se creen al menos unos días antes del inicio del hackathon. Además, siga las instrucciones que se indican a continuación:

## <a name="guidance"></a>Guía

- **Cree el laboratorio en una región o ubicación más cercana a los participantes**. 

    Para reducir la latencia, cree el laboratorio en una región más cercana a los participantes del hackathon.  Si los participantes se encuentran en todo el mundo, debe aplicar su mejor juicio para crear un laboratorio que esté ubicado en un lugar central.  O bien, divida el hackathon para usar varios laboratorios en función de las ubicaciones donde se encuentran los participantes.
- **Elija un tamaño de proceso que se adapte mejor a las necesidades de uso**.

    Por lo general, cuanto mayor sea el tamaño de proceso, más rápido funcionará la máquina virtual. Sin embargo, para limitar los costos, deberá seleccionar el tamaño de proceso adecuado en función de las necesidades de los participantes. Consulte la [información sobre el ajuste de tamaño de la máquina virtual en la guía del administrador](administrator-guide.md#vm-sizing) para ver detalles sobre los tamaños de proceso disponibles.
- **Configure RDP\SSH para la conexión a Escritorio remoto a máquinas virtuales Linux**.

    Si el hackathon usa máquinas virtuales Linux, asegúrese de que Escritorio remoto esté habilitado para que los participantes puedan usar RDP (protocolo de escritorio remoto) o SSH (Secure Shell) para conectarse a sus máquinas virtuales. Este paso solo es necesario para las máquinas virtuales Linux y se debe habilitar al crear el laboratorio. Además, para RDP, es posible que tenga que instalar y configurar el servidor RDP y los paquetes de GUI en la máquina virtual de plantilla antes de realizar la publicación.  Para más información, consulte la [guía paso a paso sobre cómo habilitar el Escritorio remoto para Linux](how-to-enable-remote-desktop-linux.md).

- **Instale y detenga las actualizaciones de Windows**. 

    Si usa una imagen de Windows, se recomienda instalar las actualizaciones más recientes de Windows en la [máquina virtual de plantilla](how-to-create-manage-template.md) del laboratorio antes de publicarla para crear las máquinas virtuales de los laboratorios. Es por motivos de seguridad y para evitar interrupciones a los participantes durante el hackathon para instalar actualizaciones, lo que también puede hacer que sus máquinas virtuales se reinicien. También puede considerar la posibilidad de desactivar las actualizaciones de Windows para evitar interrupciones futuras. Consulte la [guía paso a paso sobre cómo instalar y configurar las actualizaciones de Windows](how-to-prepare-windows-template.md#install-and-configure-updates).
- **Decida cómo los alumnos harán una copia de seguridad de su trabajo**. 

    A cada alumno se le asigna una máquina virtual mientras dure el hackathon. Pueden guardar su trabajo directamente en la máquina, pero se recomienda que hagan una copia de seguridad de su trabajo para que tengan acceso a ella una vez que el hackathon haya finalizado. Por ejemplo, deberían guardarlo en una ubicación externa, como OneDrive, GitHub, etc. Para usar OneDrive, puede optar por configurarlo automáticamente en las máquinas virtuales de laboratorio de los alumnos. Consulte la [guía paso a paso sobre cómo instalar y configurar OneDrive](how-to-prepare-windows-template.md#install-and-configure-onedrive).
- **Establezca la capacidad de la VM según el número de participantes**. 

    Asegúrese de que la capacidad de la máquina virtual del laboratorio está establecida en función del número de participantes que espera en el hackathon. Al publicar la máquina virtual de plantilla, es posible que la creación de todas las máquinas del laboratorio tarde varias horas. Es por esto que se recomienda hacerlo con antelación al inicio del hackathon. Para más información, consulte la [guía paso a paso sobre cómo actualizar la capacidad de laboratorio](how-to-set-virtual-machine-passwords.md#update-the-lab-capacity).

- **Decida si quiere restringir el acceso al laboratorio**. 

    Al agregar usuarios al laboratorio, hay una opción para restringir el acceso que está habilitada de manera predeterminada. Esta característica requiere que agregue todos los correos electrónicos de los participantes del hackathon a la lista para que puedan registrarse y acceder al laboratorio con el vínculo de registro. Si tiene un hackathon en el que no sabe quiénes serán los participantes antes del evento, puede optar por deshabilitar la opción para restringir acceso, lo que permitirá a cualquier persona registrarse en el laboratorio mediante el vínculo de registro. Para más información, consulte la [guía paso a paso sobre cómo agregar usuarios](how-to-configure-student-usage.md).

- **Compruebe la configuración de la programación, la cuota y el apagado automático**. 

    Lab Services proporciona varios controles de costos para limitar el uso de las máquinas virtuales. Sin embargo, si estos valores están mal configurados, pueden provocar que las máquinas virtuales del laboratorio se apaguen inesperadamente. Para asegurarse de que estos valores están configurados correctamente para el hackathon, compruebe la configuración siguiente:

    **Programación**: una [programación](how-to-create-schedules.md) permite controlar de forma automática cuándo se inician y se apagan las máquinas de los laboratorios. De manera predeterminada, no se configura ninguna programación cuando se crea un laboratorio nuevo. Sin embargo, debe asegurarse de que la programación del laboratorio esté establecida según lo que convenga para el hackathon.  Por ejemplo, si el hackathon comienza el sábado a las 8:00 a. m. y termina el domingo a las 5:00 p. m., puede crear una programación que inicie automáticamente la máquina a las 7:30 a. m. del sábado (unos 30 minutos antes del inicio del hackathon) y la apaga a las 5:00 p. m. del domingo. En su lugar, también puede decidir no usar ninguna programación.

    **Cuota**: la [cuota](how-to-configure-student-usage.md#set-quotas-for-users) controla el número de horas que los participantes tendrán acceso a una máquina virtual fuera de las horas programadas. Si se alcanza la cuota mientras un participante está usando la máquina, esta se apagará automáticamente y el participante no podrá reiniciarla a menos que se aumente la cuota. De forma predeterminada, cuando se crea un laboratorio, la cuota se establece en 10 horas. De nuevo, debe asegurarse de establecer la cuota de modo que permita un tiempo suficiente para el hackathon, lo que es especialmente importante si no ha creado una programación.

    **Apagado automático**: cuando se habilita, la configuración de [apagado automático](how-to-enable-shutdown-disconnect.md) hace que las máquinas virtuales Windows se apaguen automáticamente después de un período de tiempo determinado, una vez que el alumno se haya desconectado de su sesión de RDP. De manera predeterminada esta opción está deshabilitada.

- **Configure las opciones del firewall para permitir conexiones a las máquinas virtuales del laboratorio**. 

    Asegúrese de que la configuración de firewall de su escuela u organización permite conectarse a las máquinas virtuales del laboratorio mediante RDP\SSH. Para más información, consulte la [guía paso a paso sobre cómo ajustar la configuración de firewall de la red](how-to-configure-firewall-settings.md).

- **Instale el cliente de RDP\SSH en las tabletas, equipos Mac, PC, etc. de los participantes**.

    Los participantes del hackathon deben tener un cliente de RDP o SSH instalado en las tabletas o los equipos portátiles que usarán para conectarse a las máquinas virtuales de laboratorio. Puede elegir entre distintos clientes de RDP o SSH, como:

    - La aplicación **Conexión a Escritorio remoto de Microsoft** de Microsoft para conexiones de RDP. La aplicación Conexión a Escritorio remoto es compatible con distintos tipos de plataformas, como Chromebooks y [Mac](https://techcommunity.microsoft.com/t5/azure-lab-services/connecting-to-azure-lab-services-environments-on-your-macos/ba-p/1290162).
    - [PuTTY](https://techcommunity.microsoft.com/t5/azure-lab-services/connecting-to-azure-lab-services-environments-on-your-macos/ba-p/1290162) para usar SSH para conectarse a una máquina virtual Linux.
- **Compruebe las máquinas virtuales del laboratorio**. 

    Una vez que haya publicado las máquinas virtuales del laboratorio, debe comprobar que están configuradas correctamente. Solo tiene que realizar esta comprobación para una de las máquinas virtuales del laboratorio del participante:

    1. Conéctese mediante RDP o SSH.
    2. Abra todas las aplicaciones y herramientas adicionales que instaló para personalizar la imagen de máquina virtual base.
    3. Recorra algunos escenarios básicos que son representativos de las actividades que los participantes realizarán para garantizar que el rendimiento de la máquina virtual sea adecuado en función del tamaño de proceso seleccionado.

## <a name="on-the-day-of-hackathon"></a>El día del hackathon
En esta sección se describen los pasos necesarios para completar el día del hackathon.

1. **Inicie las máquinas virtuales del laboratorio**.

    En función del sistema operativo, la máquina del laboratorio puede tardar hasta 30 minutos en iniciarse. Como resultado, es importante iniciar las máquinas antes de que se inicie el hackathon para que los participantes no tengan que esperar. Si usa una programación, asegúrese de que las máquinas virtuales se inicien automáticamente al menos 30 minutos antes.
2. **Invite a los alumnos a registrarse y a acceder a su máquina virtual del laboratorio**. 

    Proporcione a los participantes la información siguiente para que los participantes puedan acceder a sus máquinas virtuales del laboratorio. 

    - El vínculo de registro del laboratorio. 
    - Las credenciales que se deben usar para conectarse a la máquina. Este paso solo se aplica si el laboratorio usa una imagen basada en Windows y todas las máquinas virtuales están configuradas para que usen la misma contraseña.
    - Instrucciones sobre cómo los participantes se conectan a través de SSH o RDP a sus máquinas.

        Para más información, consulte la [guía paso a paso sobre cómo enviar invitaciones a los usuarios](how-to-configure-student-usage.md#send-invitations-to-users) y cómo [conectarse a máquinas virtuales Linux](how-to-use-remote-desktop-linux-student.md). 

## <a name="next-steps"></a>Pasos siguientes
Empiece por crear una cuenta de laboratorio en los laboratorios, siguiendo las instrucciones del artículo: [Tutorial: Configuración de una cuenta de laboratorio con Azure Lab Services](tutorial-setup-lab-account.md).
