---
title: Descripción de acceso e identidad de vCenter
description: vCenter cuenta con un usuario local integrado denominado cloudadmin que tiene asignado el rol CloudAdmin.
ms.topic: include
ms.date: 08/31/2021
ms.openlocfilehash: 1fdc81c11b833b1239d37f613732eec42b463e1a
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123252550"
---
<!-- used in concepts-run-commands.md and tutorial-configure-identity-source.md -->

En Azure VMware Solution, vCenter tiene un usuario local integrado denominado *cloudadmin* que tiene asignado el rol CloudAdmin. Puede configurar usuarios y grupos en Active Directory (AD) con el rol CloudAdmin en la nube privada. En general, el rol CloudAdmin crea y administra las cargas de trabajo en la nube privada. En cambio, en Azure VMware Solution, el rol CloudAdmin tiene privilegios de vCenter que son distintos a los de otras soluciones de nube e implementaciones locales de VMware.

>[!IMPORTANT]
>El usuario local cloudadmin debe tratarse como una cuenta de acceso de emergencia para escenarios de urgencia en la nube privada. No es para actividades administrativas diarias ni para la integración con otros servicios. 

- En una implementación local de vCenter y ESXi, el administrador tiene acceso a la cuenta de administrador de vCenter\@vsphere.local. También se pueden tener más usuarios y grupos de AD asignados. 

- En una implementación de Azure VMware Solution, el administrador no tiene acceso a la cuenta de usuario de administrador. Sin embargo, puede asignar usuarios y grupos de AD al rol CloudAdmin en vCenter.  El rol CloudAdmin no tiene permisos para agregar un origen de identidad, como un servidor LDAP o LDAPS local a vCenter. Sin embargo, puede usar comandos de ejecución para agregar un origen de identidad y asignar el rol cloudadmin a usuarios y grupos.
 
El usuario de nube privada no tiene acceso a los componentes de administración específicos que Microsoft admite y administra, ni tampoco puede configurarlos. Por ejemplo, clústeres, hosts, almacenes de datos y conmutadores virtuales distribuidos.

>[!NOTE]
>En Azure VMware Solution, el dominio de SSO *vsphere.local* se proporciona como un recurso administrado para admitir operaciones de plataforma. No admite la creación y administración de grupos y usuarios locales distintos de los proporcionados de forma predeterminada con la nube privada.
