---
title: Implementación y configuración de certificados de entidad de certificación de empresa para Azure Firewall Premium
description: Obtenga información sobre cómo implementar y configurar certificados de entidad de certificación de empresa para Azure Firewall Premium.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 07/15/2021
ms.author: victorh
ms.openlocfilehash: 067daef5782697357d08edc0a6c09bea8af085f6
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114441079"
---
# <a name="deploy-and-configure-enterprise-ca-certificates-for-azure-firewall"></a>Implementación y configuración de certificados de entidad de certificación de empresa para Azure Firewall


En Azure Firewall Premium se incluye una característica de inspección de TLS, que requiere una cadena de autenticación de certificado. En el caso de las implementaciones de producción, debe usar una infraestructura de clave pública de la organización para generar los certificados que se usan con Azure Firewall Prémium. Use este artículo para crear y administrar un certificado de entidad de certificación intermedio para Azure Firewall Premium.

Para más información sobre los certificados que usa Azure Firewall Premium, consulte [Certificados de Azure Firewall Premium](premium-certificates.md).

## <a name="prerequisites"></a>Requisitos previos

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

Para usar una entidad de certificación de empresa para generar un certificado para usarlo con Azure Firewall Premium, debe tener los siguientes recursos: 

- un bosque de Active Directory 
- una entidad de certificación raíz de servicios de certificación de Active Directory con inscripción en web habilitada 
- una instancia de Azure Firewall Prémium con directiva de firewall de nivel Prémium 
- una instancia de Azure Key Vault 
- una identidad administrada con permisos de lectura para los **certificados y secretos** definidos en la directiva de acceso de Key Vault 

## <a name="request-and-export-a-certificate"></a>Solicitar y exportar un certificado

1. Obtenga acceso al sitio de inscripción en web en la entidad de certificación raíz, normalmente `https://<servername>/certsrv`, y seleccione **Solicitar un certificado**.
1. Seleccione **Solicitud de certificado avanzada**.
1. Seleccione **Crear y enviar una solicitud a esta CA**.
1. Rellene el formulario mediante la plantilla Entidad de certificación subordinada.
1. Envíe la solicitud e instale el certificado.
1. Suponiendo que esta solicitud se realice desde una instancia de Windows Server con Internet Explorer, abra **Opciones de Internet**.
1. Vaya a la pestaña **Contenido** y seleccione **Certificados**.
1. Seleccione el certificado que acaba de emitir y, a continuación, seleccione **Exportar**.
1. Seleccione **Siguiente** para iniciar el asistente. Seleccione **Sí, exportar la clave privada** y, después, haga clic en **Siguiente**.
1. El formato de archivo .pfx está seleccionado de forma predeterminada. Desactive **Incluir todos los certificados en la ruta de certificación si es posible**. Si exporta toda la cadena de certificados, se producirá un error en el proceso de importación a Azure Firewall.
1. Asigne y confirme una contraseña para proteger la clave y, a continuación, seleccione **Siguiente**.
1. Elija un nombre de archivo y una ubicación de exportación y, a continuación, seleccione **Siguiente**.
1. Seleccione **Finalizar** y mueva el certificado exportado a una ubicación segura.

## <a name="add-the-certificate-to-a-firewall-policy"></a>Agregar el certificado a una directiva de firewall

1. En Azure Portal, vaya a la página Certificados de su instancia de Key Vault y seleccione **Generar o importar**.
1. Seleccione **Importar** como método de creación, asigne un nombre al certificado, seleccione el archivo .pfx exportado, escriba la contraseña y, después, seleccione **Crear**.
1. Vaya a la página **Inspección de TLS** de la directiva de firewall y seleccione la identidad administrada, Key Vault y el certificado. 
1. Seleccione **Guardar**.
   :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/tls-inspection.png" alt-text="La inspección de TLS":::

## <a name="validate-tls-inspection"></a>Validar la inspección de TLS

1. Cree una regla de aplicación mediante la inspección de TLS en la dirección URL de destino o el nombre de dominio completo que elija.  Por ejemplo: `*bing.com`.
1. En un equipo unido a un dominio dentro del intervalo de origen de la regla, vaya a su destino y seleccione el símbolo de bloqueo situado junto a la barra de direcciones del explorador. El certificado debe mostrar que lo emitió la entidad de certificación de empresa en lugar de una entidad de certificación pública.
1. Muestre el certificado para mostrar más detalles, incluida la ruta de acceso del certificado.
   :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/certificate-details.png" alt-text="detalles del certificado":::
1. En Log Analytics, ejecute la siguiente consulta de KQL para devolver todas las solicitudes que han estado sujetas a la inspección de TLS:
   ```
   AzureDiagnostics 
   | where ResourceType == "AZUREFIREWALLS" 
   | where Category == "AzureFirewallApplicationRule" 
   | where msg_s contains "Url:" 
   | sort by TimeGenerated desc
   ```
   En el resultado se muestra la dirección URL completa del tráfico inspeccionado: :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/kql-query.png" alt-text="consulta de KQL":::

## <a name="next-steps"></a>Pasos siguientes

[Azure Firewall Premium en Azure Portal](premium-portal.md)
