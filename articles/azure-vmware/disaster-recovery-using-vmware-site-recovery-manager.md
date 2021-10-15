---
title: Implementación de la recuperación ante desastres con VMware Site Recovery Manager
description: Implemente la recuperación ante desastres con VMware Site Recovery Manager (SRM) en la nube privada de Azure VMware Solution.
ms.topic: how-to
ms.date: 10/04/2021
ms.openlocfilehash: 2cefe85f71c770ce29a14fb4aad7a91efd91651c
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2021
ms.locfileid: "129430105"
---
# <a name="deploy-disaster-recovery-with-vmware-site-recovery-manager"></a>Implementación de la recuperación ante desastres con VMware Site Recovery Manager

En este artículo se explica cómo implementar la recuperación ante desastres para máquinas virtuales de VMware locales o máquinas virtuales basadas en Azure VMware Solution.  La solución de este artículo usa [VMware Site Recovery Manager (SRM)](https://docs.vmware.com/en/Site-Recovery-Manager/index.html) y vSphere Replication con Azure VMware Solution. Las instancias de SRM y los servidores de replicación se implementan tanto en los sitios protegidos como en los de recuperación.       

SRM es una solución de recuperación ante desastres diseñada para minimizar el tiempo de inactividad de las máquinas virtuales en un entorno de Azure VMware Solution en caso de desastre. SRM automatiza y orquesta la conmutación por error y la conmutación por recuperación, lo que garantiza un tiempo de inactividad mínimo si se produce un desastre. Además, las pruebas integradas sin interrupciones garantizan que se cumplan los objetivos de tiempo de recuperación. En general, SRM simplifica la administración mediante la automatización y garantiza tiempos de recuperación rápidos y muy predecibles.  

vSphere Replication es la tecnología de replicación basada en hipervisor de VMware diseñada para máquinas virtuales de vSphere. Protege las máquinas virtuales frente a errores de sitio parciales o completos. Además, simplifica la protección de recuperación ante desastres mediante la replicación centrada en máquinas virtuales independiente del almacenamiento. vSphere Replication se configura en cada máquina virtual, lo que permite controlar mejor qué máquinas virtuales se replican.

En este artículo, implementará la recuperación ante desastres para máquinas virtuales de VMware locales o máquinas virtuales basadas en Azure VMware Solution.


## <a name="supported-scenarios"></a>Escenarios admitidos

SRM le ayuda a planear, probar y ejecutar la recuperación de máquinas virtuales entre un sitio de vCenter Server protegido y un sitio de vCenter Server de recuperación. Puede usar SRM con Azure VMware Solution en los dos escenarios de recuperación ante desastres siguientes: 

- Recuperación ante desastres de VMware local en la nube privada de Azure VMware Solution 
- Recuperación ante desastres de la instancia principal de Azure VMware Solution en la nube privada de la instancia secundaria de Azure VMware Solution 

En el diagrama se muestra el escenario de implementación de la instancia principal de Azure VMware Solution en la instancia secundaria de Azure VMware Solution.

:::image type="content" source="media/vmware-srm-vsphere-replication/vmware-site-recovery-manager-diagram.png" alt-text="Diagrama en el que se muestra la solución de recuperación ante desastres VMware Site Recovery Manager (SRM) en Azure VMware Solution." border="false":::

Puede usar SRM para implementar diferentes tipos de recuperación, como los siguientes:

- La **migración planeada** comienza cuando los sitios principal y secundario de Azure VMware Solution están en ejecución y son totalmente funcionales. Se trata de una migración ordenada de máquinas virtuales del sitio protegido al sitio de recuperación, donde no se espera ninguna pérdida de datos cuando las cargas de trabajo se migran de forma ordenada. 

- La **recuperación ante desastres** mediante SRM se puede invocar cuando el sitio protegido de Azure VMware Solution se queda sin conexión de forma inesperada. Site Recovery Manager orquesta el proceso de recuperación con los mecanismos de replicación para minimizar la pérdida de datos y el tiempo de inactividad del sistema.

   En Azure VMware Solution, solo se pueden proteger máquinas virtuales individuales en un host mediante el uso de SRM en combinación con vSphere Replication.

- La **protección bidireccional** usa un único conjunto de sitios de SRM emparejados para proteger las máquinas virtuales en ambas direcciones. Cada sitio puede ser al mismo tiempo un sitio protegido y un sitio de recuperación, pero para un conjunto diferente de máquinas virtuales.

>[!IMPORTANT]
>Azure VMware Solution no admite lo siguiente: 
>
>- Grupos de protección de directivas de almacenamiento y replicación basados en matrices 
>- Grupos de protección de vVol 
>- Personalización de IP de SRM mediante herramientas de línea de comandos de SRM
>- Topología de tipo uno a varios y varios a uno 
>- Identificador de complemento de SRM personalizado o identificador de extensión


## <a name="deployment-workflow"></a>Flujo de trabajo de implementación

En el diagrama se muestra el flujo de trabajo de la instancia principal de Azure VMware Solution a la instancia secundaria. Además, se muestran los pasos que se deben seguir en Azure Portal y en los entornos de VMware de Azure VMware Solution para lograr la protección de un extremo a otro de las máquinas virtuales. 

:::image type="content" source="media/vmware-srm-vsphere-replication/site-recovery-manager-workflow.png" alt-text="Diagrama en el que se muestra el flujo de trabajo de implementación de VMware Site Recovery Manager en Azure VMware Solution." border="false":::

## <a name="prerequisites"></a>Requisitos previos

Asegúrese de que ha proporcionado explícitamente al usuario remoto los roles de administrador de VRM y administrador de SRM en el vCenter remoto.

### <a name="scenario-on-premises-to-azure-vmware-solution"></a>Escenario: del entorno local a Azure VMware Solution 

- Nube privada de Azure VMware Solution implementada como región secundaria.

- [Resolución DNS](configure-dns-azure-vmware-solution.md) en aplicaciones de SRM locales y en la nube virtuales.

   >[!NOTE]
   >En el caso de las nubes privadas creadas el 1 de julio de 2021 o después de esta fecha, puede configurar la resolución DNS privada. En el caso de las nubes privadas creadas antes del 1 de julio de 2021, que necesitan una resolución DNS privada, abra una [solicitud de soporte técnico](https://rc.portal.azure.com/#create/Microsoft.Support) para solicitar la **configuración de DNS privado**.

- Conectividad de ExpressRoute entre el entorno local y Azure VMware Solution (2 Gbps).

### <a name="scenario-primary-azure-vmware-solution-to-secondary"></a>Escenario: de la instancia principal de Azure VMware Solution a la instancia secundaria

- La nube privada de Azure VMware Solution debe implementarse en la región primaria y secundaria.

   :::image type="content" source="media/vmware-srm-vsphere-replication/two-private-clouds-different-regions.png" alt-text="Captura de pantalla en la que se muestran dos nubes privadas de Azure VMware Solution en regiones independientes.":::
 
- Conectividad (como Global Reach de ExpressRoute) entre la nube privada de origen y de destino de Azure VMware Solution.

   :::image type="content" source="media/vmware-srm-vsphere-replication/global-reach-connectity-to-on-premises.png" alt-text="Captura de pantalla en la que se muestra la conectividad entre las nubes privadas de origen y de destino.":::

 
## <a name="install-srm-in-azure-vmware-solution"></a>Instalación de SRM en Azure VMware Solution

1. En el centro de datos local, instale VMware SRM y vSphere.

   >[!NOTE]
   >Use el modelo de implementación consistente en la [topología de dos sitios con una instancia de vCenter Server por PSC](https://docs.vmware.com/en/Site-Recovery-Manager/8.4/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html). Además, asegúrese de que están abiertos los [puertos de red de vSphere Replication necesarios](https://kb.VMware.com/s/article/2087769).

1. En la nube privada de Azure VMware Solution, en **Administrar**, seleccione **Complementos** > **Recuperación ante desastres**.

   El usuario CloudAdmin predeterminado de la nube privada de Azure VMware Solution no tiene privilegios suficientes para instalar VMware SRM o vSphere Replication. El proceso de instalación incluye varios pasos, que se describen en la sección [Requisitos previos](#prerequisites). En su lugar, puede instalar VMware SRM con vSphere Replication como un servicio de complemento desde la nube privada de Azure VMware Solution.

   :::image type="content" source="media/VMware-srm-vsphere-replication/disaster-recovery-add-ons.png" alt-text="Captura de pantalla de la nube privada de Azure VMware Solution para instalar VMware SRM con vSphere Replication como complemento." border="true" lightbox="media/VMware-srm-vsphere-replication/disaster-recovery-add-ons.png":::

1. En el menú desplegable **Solución de recuperación ante desastres**, seleccione **VMware Site Recovery Manager (SRM) - vSphere Replication**. 

   :::image type="content" source="media/VMware-srm-vsphere-replication/disaster-recovery-solution-srm-add-on.png" alt-text="Captura de pantalla en la que se muestra la pestaña Recuperación ante desastres en Complementos, con la opción VMware Site Recovery Manager (SRM) - vSphere Replication seleccionada." border="true" lightbox="media/VMware-srm-vsphere-replication/disaster-recovery-solution-srm-add-on.png":::

1. Proporcione la clave de licencia, acepte los términos y condiciones y seleccione **Instalar**.

   >[!NOTE]
   >Si no proporciona la clave de licencia, SRM se instala en modo de evaluación. La licencia solo se usa para habilitar VMware SRM.
   
   :::image type="content" source="media/VMware-srm-vsphere-replication/disaster-recovery-solution-srm-licence.png" alt-text="Captura de pantalla en la que se muestra la pestaña Recuperación ante desastres en Complementos con el campo Clave de licencia seleccionado." border="true" lightbox="media/VMware-srm-vsphere-replication/disaster-recovery-solution-srm-licence.png":::


## <a name="install-the-vsphere-replication-appliance"></a>Instalación del dispositivo vSphere Replication

Una vez que el dispositivo SRM se haya instalado correctamente, deberá instalar los dispositivos vSphere Replication. Cada servidor de replicación admite hasta 200 máquinas virtuales protegidas. Escale o reduzca horizontalmente el tamaño según sus necesidades. 

1. En el menú desplegable **Replicación mediante**, en la pestaña **Recuperación ante desastres**, seleccione **vSphere Replication**.

   :::image type="content" source="media/vmware-srm-vsphere-replication/vsphere-replication-1.png" alt-text="Captura de pantalla en la que se muestra que se ha seleccionado vSphere Replication para la opción Replicación mediante.":::

1. Mueva el control deslizante del servidor de vSphere para indicar el número de servidores de replicación que quiere según el número de máquinas virtuales que se protegerán. A continuación, seleccione **Instalar**.

   :::image type="content" source="media/vmware-srm-vsphere-replication/vsphere-replication-2.png" alt-text="Captura de pantalla en la que se muestra cómo aumentar o disminuir el número de servidores de replicación.":::

1. Tras la instalación, compruebe que los dispositivos SRM y vSphere Replication se han instalado.

   >[!TIP]
   >La presencia del botón Desinstalar indica que los dispositivos SRM y vSphere Replication están instalados actualmente.

   :::image type="content" source="media/vmware-srm-vsphere-replication/vsphere-replication-3.png" alt-text="Captura de pantalla en la que se muestra que SRM y el dispositivo de replicación están instalados.":::
  

## <a name="configure-site-pairing-in-vcenter"></a>Configuración del emparejamiento de sitios en vCenter

Después de instalar VMware SRM y vSphere Replication, debe completar la configuración y el emparejamiento de sitios en vCenter.

1. Inicie sesión en vCenter como cloudadmin@vsphere.local.

1. Vaya a **Site Recovery**, compruebe el estado de vSphere Replication y de VMware SRM y, luego, seleccione **OPEN Site Recovery** (ABRIR Site Recovery) para iniciar el cliente.

   :::image type="content" source="media/vmware-srm-vsphere-replication/open-site-recovery.png" alt-text="Captura de pantalla en la que se muestra el cliente de vSphere, donde aparecen vSphere Replication y Site Recovery Manager con el estado de instalación Correcto." border="true":::


1. Seleccione **NEW SITE PAIR** (NUEVO PAR DE SITIOS) en el cliente de Site Recovery (SR) en la nueva pestaña que se abre.
   
   :::image type="content" source="media/vmware-srm-vsphere-replication/new-site-pair.png" alt-text="Captura de pantalla en la que se muestra el cliente de vSphere con el botón New Site Pair (Nuevo par de sitios) seleccionado para Site Recovery." border="true":::

1. Escriba los detalles del sitio remoto y seleccione **SIGUIENTE**.

   >[!NOTE]
   >La nube privada de Azure VMware Solution funciona con una instancia insertada de Platform Services Controller (PSC), por lo que solo se puede seleccionar una instancia de vCenter local. Si la instancia de vCenter remota usa una instancia insertada de Platform Services Controller (PSC), use el FQDN (o su dirección IP) y el puerto de vCenter para especificar el PSC. 
   >
   >El usuario remoto debe tener suficientes permisos para realizar los emparejamientos. Una manera fácil de asegurarse de esto consiste en asignar a ese usuario los roles de administrador de VRM y administrador de SRM en la instancia remota de vCenter. Para una nube privada remota de Azure VMware Solution, el usuario cloudadmin se configura con esos roles.

   :::image type="content" source="media/vmware-srm-vsphere-replication/pair-the-sites-specify-details.png" alt-text="Captura de pantalla en la que se muestran los detalles del nuevo par de sitios." border="true" lightbox="media/vmware-srm-vsphere-replication/pair-the-sites-specify-details.png":::

1. Seleccione **CONECTAR** para aceptar el certificado para la instancia remota de vCenter.

   En este momento, el cliente debe detectar los dispositivos VRM y SRM en ambos lados como servicios para emparejarlos.

1. Elija los dispositivos que quiere emparejar y seleccione **SIGUIENTE**.
   
   :::image type="content" source="media/vmware-srm-vsphere-replication/pair-the-sites-new-site.png" alt-text="Captura de pantalla en la que se muestran los detalles de vCenter Server y de los servicios para el nuevo par de sitios." border="true" lightbox="media/vmware-srm-vsphere-replication/pair-the-sites-new-site.png":::

1. Seleccione **CONECTAR** para aceptar los certificados para las instancias remotas de VMware SRM y de vCenter (de nuevo).

1. Seleccione **CONECTAR** para aceptar los certificados para las instancias locales de VMware SRM y de vCenter.

1. Revise la configuración y seleccione **FINALIZAR**.

   Si el proceso se ha realizado correctamente, el cliente mostrará otro panel para el emparejamiento. En caso contrario, se notificará una alarma.

1. En la esquina inferior derecha, seleccione la flecha doble hacia arriba para expandir el panel y mostrar **Tareas recientes** y **Alarmas**.

   >[!NOTE]
   >El cliente de SR a veces tarda mucho en actualizarse. Si le parece que una operación tarda demasiado o cree que se ha bloqueado, seleccione el icono de actualización en la barra de menús. 

1. Seleccione **VER DETALLES** para abrir el panel de emparejamiento de sitios remotos, que abre un cuadro de diálogo para iniciar sesión en la instancia remota de vCenter.

   :::image type="content" source="media/vmware-srm-vsphere-replication/view-details-remote-pairing.png" alt-text="Captura de pantalla en la que se muestran los detalles del nuevo par de sitios para Site Recovery Manager y vSphere Replication." border="true" lightbox="media/vmware-srm-vsphere-replication/view-details-remote-pairing.png":::

1. Escriba un nombre de usuario con permisos suficientes para realizar la replicación y la recuperación del sitio y seleccione **INICIAR SESIÓN**. 

   En el proceso de emparejamiento, el inicio de sesión (que suele ser un usuario diferente) es una acción única para establecer el emparejamiento. El cliente de SR requiere este inicio de sesión cada vez que se inicia el cliente para trabajar con el emparejamiento.

   >[!NOTE] 
   >El usuario con permisos suficientes debe tener asignados los roles de **administrador de VRM** y **administrador de SRM** en la instancia remota de vCenter. El usuario también debe tener acceso al inventario de la instancia remota de vCenter, como carpetas y almacenes de datos. En el caso de una nube privada de Azure VMware Solution, el usuario cloudadmin tiene los permisos y el acceso adecuados. 
   
   :::image type="content" source="media/vmware-srm-vsphere-replication/sign-into-remote-vcenter.png" alt-text="Captura de pantalla en la que se muestran las credenciales de vCenter Server." border="true":::

   Verá un mensaje de advertencia en el que se indica que la instancia insertada de VRS en la instancia local de VRM no se está ejecutando.  Esto se debe a que Azure VMware Solution no usa la instancia insertada de VRS en una nube privada de Azure VMware Solution.  En su lugar, usa dispositivos VRS. 

   :::image type=" content" source=" media/vmware-srm-vsphere-replication/pair-the-sites-summary.png" alt-text="Captura de pantalla en la que se muestra el resumen del par de sitios para Site Recovery Manager y vSphere Replication." border="true" lightbox="media/vmware-srm-vsphere-replication/pair-the-sites-summary.png":::

## <a name="srm-protection-reprotection-and-failback"></a>Protección, reprotección y conmutación por recuperación de SRM

Después de crear el emparejamiento de sitios, siga la documentación de VMware que se menciona a continuación para la protección de un extremo a otro de las máquinas virtuales en Azure Portal.

- [Uso de vSphere Replication con Site Recovery Manager (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-2C77C830-892D-45FF-BA4F-80AC10085DBE.html)

- [Asignaciones de inventario para grupos de protección de replicación basados en matrices y grupos de protección de vSphere Replication (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-2E2B4F84-D388-456B-AA3A-57FA8D47063D.html)

- [Acerca de las máquinas virtuales de marcador de posición (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-EFE73B20-1C68-4D2C-8C86-A6E3C6214F07.html)

- [Grupos de protección de vSphere Replication (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-CCF2E768-736E-4EAA-B3BE-50182635BC49.html)

- [Creación, prueba y ejecución de planes de recuperación (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-AF6BF11B-4FB7-4543-A873-329FDF1524A4.html)

- [Configuración de un plan de recuperación (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-FAC499CE-2994-46EF-9164-6D97EAF52C68.html)

- [Personalización de las propiedades de IP para máquinas virtuales (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-25B33730-14BE-4268-9D88-1129011AFB39.html)

- [Cómo reprotege Site Recovery Manager las máquinas virtuales con vSphere Replication (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-1DE0E76D-1BA7-44D8-AEA2-5B2218E219B1.html)

- [Realización de una conmutación por recuperación (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-556E84C0-F8B7-4F9F-AAB0-0891C084EDE4.html)



## <a name="ongoing-management-of-your-srm-solution"></a>Administración continua de la solución de SRM

Aunque Microsoft pretende simplificar la instalación de VMware SRM y vSphere Replication en una nube privada de Azure VMware Solution, usted es el responsable de administrar la licencia y el funcionamiento diario de la solución de recuperación ante desastres. 

## <a name="scale-limitations"></a>Limitaciones de escala

Las limitaciones de escala dependen de la nube privada.

| Configuración | Límite |
| --- | --- |
| Número de máquinas virtuales protegidas  | 1000  |
| Número de máquinas virtuales por plan de recuperación  | 1000  |
| Número de grupos de protección por plan de recuperación  | 250  |
| Valores de RPO  | 5 minutos o más*  |
| Número total de máquinas virtuales por grupo de protección  | 500  |
| Número total de planes de recuperación  | 250  |

\* Para información sobre el objetivo de punto de recuperación (RPO) de menos de 15 minutos, consulte [Funcionamiento del objetivo de punto de recuperación de 5 minutos](https://docs.vmware.com/en/vSphere-Replication/8.3/com.vmware.vsphere.replication-admin.doc/GUID-9E17D567-A947-49CD-8A84-8EA2D676B55A.html) en la _guía de administración de la replicación de vSphere_.



## <a name="srm-licenses"></a>Licencias de SRM

Puede instalar VMware SRM mediante una licencia de evaluación o una licencia de producción.  La licencia de evaluación es válida durante 60 días. Una vez transcurrido el período de evaluación, tendrá que obtener una licencia de producción de VMware SRM. 

No se pueden usar licencias locales preexistentes de VMware SRM para la nube privada de Azure VMware Solution. Colabore con VMware y con los equipos de ventas para adquirir una nueva licencia de producción basada en plazos de VMware SRM. 

Una vez que haya adquirido una licencia de producción de SRM, podrá usar el portal de Azure VMware Solution para actualizar SRM con la nueva licencia de producción. 


## <a name="uninstall-srm"></a>Desinstalación de SRM 

Si ya no necesita SRM, debe desinstalarlo correctamente. Antes de desinstalar SRM, debe quitar todas las configuraciones de SRM de ambos sitios en el orden correcto. Si no quita todas las configuraciones antes de desinstalar SRM, es posible que algunos componentes de SRM permanezcan en la infraestructura de Azure VMware Solution, como las máquinas virtuales de marcador de posición.

1. En el cliente de vSphere o en el cliente web de vSphere, seleccione **Site Recovery** > **Open Site Recovery** (Abrir Site Recovery).

2. En la pestaña de inicio de **Site Recovery**, elija un par de sitios y seleccione **Ver detalles**.

3. Seleccione la pestaña **Recovery Plans** (Planes de recuperación), haga clic con el botón derecho en un plan de recuperación y seleccione **Eliminar**.

   >[!NOTE]
   >No se pueden eliminar los planes de recuperación que se están ejecutando.

4. Seleccione la pestaña **Grupos de protección**, elija un grupo de protección y seleccione la pestaña **Máquinas virtuales**.

5. Seleccione todas las máquinas virtuales, haga clic con el botón derecho y seleccione **Remove Protection** (Eliminar protección).

   Al quitar la protección de una máquina virtual, se elimina del sitio de recuperación la máquina virtual de marcador de posición. Repita esta operación para todos los grupos de protección.

6. En la pestaña **Grupos de protección**, haga clic con el botón derecho en un grupo de protección y seleccione **Eliminar**.

   >[!NOTE] 
   >No se puede eliminar un grupo de protección incluido en un plan de recuperación. No se pueden eliminar grupos de protección de vSphere Replication que contengan máquinas virtuales en las que todavía esté configurada la protección.

7. Seleccione **Site Pair** > **Configure** (Par de sitios > Configurar) y quite todas las asignaciones de inventario.

   a. Seleccione las pestañas **Network Mappings** (Asignaciones de red), **Folder Mappings** (Asignaciones de carpetas) y **Resource Mappings** (Asignaciones de recursos).

   b. En cada pestaña, seleccione un sitio, haga clic con el botón derecho en una asignación y seleccione **Eliminar**.

8. Para ambos sitios, seleccione **Placeholder Datastores** (Almacenes de datos de marcador de posición), haga clic con el botón derecho en el almacén de datos de marcador de posición y seleccione **Quitar**.

9. Seleccione **Site Pair** > **Summary** (Par de sitios > Resumen) y seleccione **Break Site Pair** (Interrumpir par de sitios).

   >[!NOTE] 
   >La interrupción del emparejamiento de sitios quita toda la información relacionada con el registro de Site Recovery Manager con Site Recovery Manager, vCenter Server y Platform Services Controller en el sitio remoto.

10. En la nube privada, en **Administrar**, seleccione **Complementos** > **Recuperación ante desastres** y, luego, seleccione **Uninstall the replication appliances** (Desinstalar los dispositivos de replicación).

11. Una vez que se hayan desinstalado los dispositivos de replicación, en la pestaña **Recuperación ante desastres**, seleccione **Uninstall for the Site Recovery Manager** (Desinstalar para Site Recovery Manager).

12. Repita estos pasos en el sitio secundario de Azure VMware Solution.


## <a name="support"></a>Soporte técnico 

VMware SRM es una solución de recuperación ante desastres de VMware.  

Microsoft solo admite la instalación y la desinstalación de SRM y vSphere Replication Manager, así como el escalado y la reducción verticales de los dispositivos de vSphere Replication en Azure VMware Solution. 

Para cualquier otra cuestión, como la configuración y la replicación, póngase en contacto con VMware para obtener soporte técnico.

Los equipos de soporte técnico de VMware y Microsoft se pondrán en contacto entre sí según sea necesario para solucionar los problemas de SRM en Azure VMware Solution.


## <a name="references"></a>Referencias

- [Documentación de VMware Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
- [Matrices de compatibilidad para VMware Site Recovery Manager 8.3](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/rn/srm-compat-matrix-8-3.html)
- [Notas de la versión de VMware SRM 8.3](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/rn/srm-releasenotes-8-3.html)
- [Documentación de VMware vSphere Replication](https://docs.vmware.com/en/vSphere-Replication/index.html)
- [Matrices de compatibilidad para vSphere Replication 8.3](https://docs.vmware.com/en/vSphere-Replication/8.3/rn/vsphere-replication-compat-matrix-8-3.html)
- [Límites operativos de Site Recovery Manager 8.3](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.install_config.doc/GUID-3AD7D565-8A27-450C-8493-7B53F995BB14.html)
- [Límites operativos de vSphere Replication 8.3](https://docs.vmware.com/en/vSphere-Replication/8.3/com.vmware.vsphere.replication-admin.doc/GUID-E114BAB8-F423-45D4-B029-91A5D551AC47.html)
- [Cálculo del ancho de banda para vSphere Replication](https://docs.vmware.com/en/vSphere-Replication/8.3/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html)
- [Instalación y configuración de SRM](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.install_config.doc/GUID-B3A49FFF-E3B9-45E3-AD35-093D896596A0.html)
- [Administración de vSphere Replication](https://docs.vmware.com/en/vSphere-Replication/8.2/com.vmware.vsphere.replication-admin.doc/GUID-35C0A355-C57B-430B-876E-9D2E6BE4DDBA.html)
- [Requisitos previos y procedimientos recomendados para la instalación de SRM](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
- [Puertos de red para SRM](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.install_config.doc/GUID-499D3C83-B8FD-4D4C-AE3D-19F518A13C98.html)
- [Puertos de red para vSphere Replication](https://kb.vmware.com/s/article/2087769)
