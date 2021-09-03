---
title: Instalación de VMware HCX en Azure VMware Solution
description: Instale VMware HCX en la nube privada de Azure VMware Solution.
ms.topic: how-to
ms.date: 08/12/2021
ms.openlocfilehash: 2dec92fdee47c242a8e7e5b3df7befc586964910
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015626"
---
# <a name="install-and-activate-vmware-hcx-in-azure-vmware-solution"></a>Instalación y activación de VMware HCX en Azure VMware Solution

VMware HCX Advanced y su administrador de nube asociado ya no se implementan previamente en Azure VMware Solution. En su lugar, tendrá que instalarlo desde Azure Portal como complemento. El valor predeterminado es HCX Advanced, después del cual todavía puede solicitar VMware HCX Enterprise Edition al soporte técnico si necesita las características de la edición Enterprise. Todavía tendrá que descargar el archivo OVA de HCX Connector e implementar la aplicación virtual en la instancia local de vCenter.

HCX Advanced admite hasta tres conexiones de sitio (del entorno local a la nube o de nube a nube). Si necesita más de tres conexiones de sitio, use HCX Enterprise Edition. Para activar HCX Enterprise Edition, que actualmente se encuentra en versión preliminar pública en Azure VMware Solution, abra una solicitud de soporte técnico para habilitarla. Una vez que el servicio esté disponible con carácter general, tendrá 30 días para decidir los pasos siguientes. También puede desactivar o rechazar el servicio HCX Enterprise Edition, pero mantener HCX Advanced como parte del costo del nodo.

El cambio a una versión anterior de HCX Enterprise Edition a HCX Advanced es posible sin tener que repetir la implementación. En primer lugar, asegúrese de que ha revertido a un estado de configuración de HCX Advanced y que no usa las características de Enterprise. Si tiene previsto cambiar a una versión anterior, asegúrese de que no haya migraciones programadas, que características como RAV y MON no estén en uso, y que los emparejamientos de sitios sean tres o menos.

>[!TIP]
>También puede [desinstalar HCX Advanced](#uninstall-hcx-advanced) desde el portal. Al desinstalar HCX Advanced, asegúrese de que no tiene ninguna migración activa en curso. Al quitar HCX Advanced, se devuelven los recursos a la nube privada ocupada por las aplicaciones virtuales de HCX.

En esta guía paso a paso, hará lo siguiente:

* Instalar HCX Advanced desde Azure Portal
* Descargar e implementar el archivo OVA de VMware HCX Connector
* Activar HCX Advanced con una clave de licencia
* Desinstalar HCX Advanced

Cuando haya terminado, siga los pasos recomendados al final para continuar con los pasos de esta guía de introducción.

## <a name="prerequisites"></a>Requisitos previos

- [Preparación para las instalaciones de HCX](https://docs.vmware.com/en/VMware-HCX/4.1/hcx-user-guide/GUID-A631101E-8564-4173-8442-1D294B731CEB.html)

- [Serie de blogs de VMware: migración a la nube](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html)


## <a name="install-vmware-hcx-advanced"></a>Instalación de VMware HCX Advanced

1. En la nube privada de Azure VMware Solution, seleccione **Administrar** > **Complementos**.

1. Seleccione **Comenzar** en **HCX Workload Mobility**.

   :::image type="content" source="media/tutorial-vmware-hcx/deployed-hcx-migration-get-started.png" alt-text="Captura de pantalla en la que se muestra el botón Comenzar para HCX Workload Mobility.":::

1. Active la casilla **I agree with terms and conditions** (Acepto los términos y condiciones) y, después, seleccione **Install** (Instalar).

   La instalación de HCX Advanced y la configuración de Cloud Manager tardan unos 35 minutos. Una vez que estén instalados, la dirección URL del administrador de HCX y las claves de HCX necesarias para el emparejamiento del sitio del conector local de HCX se muestran en la pestaña **Migration using HCX** (Migración mediante HCX).

   :::image type="content" source="media/tutorial-vmware-hcx/deployed-hcx-migration-using-hcx-tab.png" alt-text="Captura de pantalla en la que se muestra la pestaña Migration using HCX (Migración mediante HCX) en Connectivity (Conectividad).":::


## <a name="download-and-deploy-the-vmware-hcx-connector-ova"></a>Descarga e implementación del archivo OVA de VMware HCX Connector 

En este paso, descargará el archivo OVA de VMware HCX Connector y, después, implementará VMware HCX Connector en la instancia local de vCenter.

1. Abra una ventana del explorador e inicie sesión en el Administrador de HCX de Azure VMware Solution en el puerto 443 en `https://x.x.x.9` con las credenciales de usuario **cloudadmin\@vsphere.local**.

1. En **Administración** > **Actualizaciones del sistema**, seleccione **Request Download Link** (Solicitar vínculo de descarga). Si la casilla está atenuada, espere unos segundos a que genere un vínculo.

1. Descargue o reciba un vínculo para el archivo OVA de VMware HCX Connector que va a implementar en la instancia local de vCenter.

1. En la instancia local de vCenter, seleccione una [plantilla OVF](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html) para implementar VMware HCX Connector.

1. Vaya al archivo OVA que descargó, selecciónelo y, a continuación, haga clic en **Abrir**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="Captura de pantalla de la búsqueda de una plantilla OVF." lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::

1. Seleccione un nombre y una ubicación y, luego, un recurso o clúster en el que va a implementar el conector de VMware HCX. A continuación, revise los detalles y los recursos necesarios y seleccione **Siguiente**.

1. Revise los términos de licencia, seleccione el almacenamiento y la red necesarios y, después, seleccione **Next** (Siguiente).

1. Seleccione el [segmento de red de administración de VMware HCX](plan-private-cloud-deployment.md#define-vmware-hcx-network-segments) que ha definido durante la fase de planificación. Luego, seleccione **Siguiente**.  

1. En **Plantilla personalizada**, escriba toda la información necesaria y seleccione **Siguiente**.

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="Captura de pantalla de los cuadros para personalizar una plantilla." lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. Compruebe la configuración y, después, seleccione **Finish** (Finalizar) para implementar el archivo OVA de VMware HCX Connector.

   >[!IMPORTANT]
   >Tendrá que activar la aplicación virtual manualmente.  Tras encenderlo, espere entre 10 y 15 minutos antes de continuar en el paso siguiente.


## <a name="activate-vmware-hcx"></a>Activación de VMware HCX

Después de implementar el archivo OVA de VMware HCX Connector en el entorno local e iniciar el dispositivo, está listo para activarlo. En primer lugar, tendrá que obtener una clave de licencia del portal de Azure VMware Solution y, después, activarla en VMware HCX Manager. Por último, necesitará una clave para cada conector HCX local implementado.

1. En la nube privada de Azure VMware Solution, seleccione **Supervisión** > **Complementos** > **Migration using HCX** (Migración mediante HCX). Después, copie la **Clave de activación**.

   :::image type="content" source="media/tutorial-vmware-hcx/hcx-activation-key.png" alt-text="Captura de pantalla en la que se muestra la clave de activación.":::   

1. Inicie sesión en el administrador de VMware HCX local en `https://HCXManagerIP:9443` con las credenciales de **administrador**.  Asegúrese de incluir el número de puerto `9443` con la dirección IP de VMware HCX Manager.

   >[!TIP]
   >Ha definido la contraseña de usuario de **admin** durante la implementación del archivo OVA del Administrador de VMware HCX.

1. En **Licencias**, escriba la clave para **HCX Advanced Key** (Clave de HCX Advanced) y seleccione **Activar**.

    >[!IMPORTANT]
    >VMware HCX Manager debe tener acceso abierto a Internet o un proxy configurado.

1. En la **Datacenter Location** (Ubicación del centro de datos), proporcione la ubicación más cercana donde va a instalar el administrador de VMware HCX. Después, seleccione **Continuar**.

1. En **Nombre del sistema**, modifique el nombre o acepte el valor predeterminado y seleccione **Continuar**.

1. Seleccione **Yes, Continue** (Sí, continuar).

1. En **Connect your vCenter** (Conectar vCenter), proporcione el FQDN o la dirección IP del servidor de vCenter y las credenciales adecuadas y, a continuación, seleccione **Continuar**.

   >[!TIP]
   >El servidor vCenter es donde ha implementado el conector de VMware HCX en el centro de datos.

1. En **Configure SSO/PSC** (Configurar SSO/PSC), proporcione el FQDN o la dirección IP de Platform Services Controller y, después, seleccione **Continue** (Continuar).

   >[!NOTE]
   >Normalmente, suele coincidir con la dirección IP o el FQDN de la instancia de vCenter.

1. Compruebe que toda la información especificada es correcta y seleccione **Reiniciar**.

   >[!NOTE]
   >Verá que el proceso se ralentiza después de reiniciar y antes de que se le solicite realizar el siguiente paso.

Una vez que se reinicien los servicios, verá que vCenter se muestra en verde en la pantalla que aparece. Tanto vCenter como SSO deben tener los parámetros de configuración adecuados, que deben ser los mismos que los de la pantalla anterior.

:::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="Captura de pantalla del panel con el estado de vCenter en color verde." lightbox="media/tutorial-vmware-hcx/activation-done.png":::


## <a name="uninstall-hcx-advanced"></a>Desinstalación de HCX Advanced

Puede desinstalar HCX Advanced desde el portal, lo que elimina el emparejamiento y el software existentes. 

>[!NOTE]
>Los recursos pueden tardar aproximadamente 30 minutos en devolverse a la nube privada ocupada por las aplicaciones virtuales HCX. 

1. Asegúrese de que no tiene ninguna migración activa en curso.

1. Asegúrese de que las extensiones L2 ya no son necesarias o que las redes se han "extendido" al destino. 

1. En el caso de las cargas de trabajo que usan MON, asegúrese de que ha quitado las puertas de enlace predeterminadas. De lo contrario, puede dar lugar a que las cargas de trabajo no puedan comunicarse o funcionar.  

1. En la nube privada de Azure VMware Solution, seleccione **Administrar** > **Complementos**.

1. Seleccione **Comenzar** en **HCX Workload Mobility** y después **Uninstall** (Desinstalar).
   
1. Escriba **yes** (sí) para confirmar la desinstalación.

En este momento, HCX Advanced ya no tiene el complemento vCenter y, si es necesario, puede volver a instalarlo en cualquier momento.


## <a name="next-steps"></a>Pasos siguientes

Continúe con el tutorial siguiente para configurar VMware HCX Connector.  Después de configurar VMware HCX Connector, tendrá un entorno listo para producción para crear máquinas virtuales (VM) y migrarlas. 


>[!div class="nextstepaction"]
>[Configuración de VMware HCX en Azure VMware Solution](configure-vmware-hcx.md)
