---
title: Configuración de un laboratorio para enseñar administración de bases de datos relacionales | Microsoft Docs
description: Aprenda a configurar un laboratorio para enseñar la administración de bases de datos relacionales.
author: emaher
ms.topic: how-to
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: bd14a53dffbc7d3480bd12dc35ed686d95b3184c
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177030"
---
# <a name="set-up-a-lab-to-teach-database-management-for-relational-databases"></a>Configuración de un laboratorio para enseñar administración de bases de datos relacionales

En este artículo se describe cómo configurar un laboratorio para una clase de administración de bases de datos básica en Azure Lab Services. Los conceptos de las bases de datos son uno de los cursos introductorios en la mayoría de los departamentos de informática de las universidades. El lenguaje de consulta estructurado (SQL) es un estándar internacional. SQL es el lenguaje estándar para la administración de bases de datos de relaciones, como la adición, el acceso y la administración de contenido en una base de datos.  Se destaca por su procesamiento rápido, su confiabilidad comprobada, su facilidad y flexibilidad de uso.

En este artículo, le mostraremos cómo configurar una plantilla de máquina virtual en un laboratorio con el servidor de base de datos MySQL y el servidor de SQL Server 2019.  [MySQL](https://www.mysql.com/) es un sistema de administración de bases de datos relacionales (RDBMS) de código abierto disponible de forma gratuita.  [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) es la versión más reciente del RDBMS de Microsoft.

## <a name="lab-configuration"></a>Configuración del laboratorio

Para configurar este laboratorio, para empezar, necesita una suscripción a Azure y una cuenta de laboratorio. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar. Una vez que obtenga una suscripción a Azure, puede crear una nueva cuenta de laboratorio en Azure Lab Services. Para obtener más información sobre la creación de una nueva cuenta de laboratorio, consulte el [tutorial para configurar una cuenta de laboratorio](tutorial-setup-lab-account.md).  También puede usar una cuenta de laboratorio existente.

### <a name="lab-account-settings"></a>Configuración de la cuenta de laboratorio

Habilite la configuración que se describe en la tabla siguiente para la cuenta de laboratorio. Para obtener más información sobre cómo habilitar imágenes de Marketplace, consulte [Especificación de las imágenes de Marketplace disponibles para los creadores de laboratorios](./specify-marketplace-images.md).

| Configuración de la cuenta de laboratorio | Instructions |
| ------------------- | ------------ |
|Imagen de Marketplace| Habilite la imagen "SQL Server 2019 Standard en Windows Server 2019" para usarla en su cuenta de laboratorio.|

### <a name="lab-settings"></a>Configuración del laboratorio

Use las opciones de la tabla siguiente al configurar un laboratorio educativo.  Para obtener más información sobre cómo crear un laboratorio de clase, consulte el [tutorial Configuración de un laboratorio educativo](tutorial-setup-classroom-lab.md).

| Configuración del laboratorio | Valor/instrucciones |
| ------------ | ------------------ |
|Tamaño de la máquina virtual| Mediana. Este tamaño es el más adecuado para bases de datos relacionales, análisis y almacenamiento en caché en memoria.|
|Imagen de máquina virtual| SQL Server 2019 Standard en Windows Server 2019|

## <a name="template-machine-configuration"></a>Configuración de la máquina de plantilla

Para instalar MySQL en Windows Server 2019, puede seguir los pasos mencionados en [Instalación y ejecución de MySQL Community Server en una máquina virtual](/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json#install-and-run-mysql-community-server-on-the-virtual-machine).

SQL Server 2019 está preinstalado en la imagen de máquina virtual que elegimos al crear el nuevo laboratorio.

## <a name="cost-estimate"></a>Estimación del costo

Vamos a cubrir una posible estimación de costos para esta clase.  Usaremos una clase de 25 alumnos.  Hay 20 horas de tiempo de clase programado.  Además, cada alumno obtiene una cuota de 10 horas para deberes o tareas fuera del tiempo de clase programado.  El tamaño de la máquina virtual que elegimos es el medio, que tiene 42 unidades de laboratorio.

A continuación se incluye un ejemplo de una posible estimación del costo para esta clase:

25 alumnos \* (20 horas programadas + 10 horas de cuota) \* 0,42 USD por hora = 315,00 USD

Para más detalles sobre los precios, consulte [Precios de Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusión

En este artículo se explican los pasos necesarios para crear un laboratorio para los conceptos básicos de administración de bases de datos mediante MySQL y SQL Server. Puede usar una configuración similar para otras clases de bases de datos.

## <a name="next-steps"></a>Pasos siguientes

Los pasos siguientes son comunes a la configuración de cualquier laboratorio.

- [Creación y administración de plantillas](how-to-create-manage-template.md)
- [Incorporación de usuarios](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Establecimiento de la cuota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Establecimiento de la programación](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Vínculos de registro por correo electrónico para los alumnos](how-to-configure-student-usage.md#send-invitations-to-users)