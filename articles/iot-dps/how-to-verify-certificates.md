---
title: Comprobación de los certificados de entidad de certificación X.509 con el servicio Azure IoT Hub Device Provisioning
description: Realización de una prueba de posesión de certificados de entidad de certificación X.509 con Azure IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 06/29/2021
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 3d3f8ee754f371680cf5e3420946a732e5060f37
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129456221"
---
# <a name="how-to-do-proof-of-possession-for-x509-ca-certificates-with-your-device-provisioning-service"></a>Realización de una prueba de posesión de certificados de entidad de certificación X.509 con el servicio Device Provisioning

Un certificado de entidad de certificación X.509 verificado es aquel que se ha cargado y registrado en su servicio de aprovisionamiento y se ha sometido a una prueba de posesión con el servicio. 

Los certificados verificados desempeñan un importante papel al usar grupos de inscripción. La verificación de la titularidad del certificado proporciona un nivel de seguridad adicional al garantizar que la persona que carga el certificado está en posesión de su clave privada. La verificación evita que un actor malintencionado que esté examinando su tráfico extraiga un certificado intermedio y lo utilice para crear un grupo de inscripción en su propio servicio de aprovisionamiento, secuestrando así sus dispositivos. Al probar la titularidad de la raíz o un certificado intermedio en una cadena de certificados, demuestra que tiene permiso para generar certificados de hoja para los dispositivos que se registrarán como parte del grupo de inscripción. Por este motivo, la raíz o certificado intermedio configurados en un grupo de inscripción deben ser un certificado verificado o deben acumularse en un certificado verificado en la cadena de certificados que un dispositivo presenta al autenticarse con el servicio. Para más información sobre la atestación de certificados X.509, consulte [Certificados X.509](concepts-x509-attestation.md) y [Control del acceso de dispositivo al servicio de aprovisionamiento con certificados X.509](concepts-x509-attestation.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

## <a name="automatic-verification-of-intermediate-or-root-ca-through-self-attestation"></a>Comprobación automática de una entidad de certificación intermedia o raíz a través de la autoatestación
Si usa una entidad de certificación intermedia o raíz en la que confía y sabe que tiene la propiedad completa del certificado, puede atestiguar automáticamente que ha comprobado el certificado.

Para agregar un certificado comprobado automáticamente, siga estos pasos:

1. En Azure Portal, vaya a su servicio de aprovisionamiento y abra **Certificados** en el menú izquierdo. 
2. Haga clic en **Agregar** para agregar un certificado nuevo.
3. Escriba un nombre para mostrar descriptivo para el certificado. Busque el archivo .cer o .pem que representa la parte pública del certificado X.509. Haga clic en **Cargar**.
4. Active la casilla situada junto a **Set certificate status to verified on upload** (Establecer el estado del certificado que se va a comprobar al cargar).

    ![Cargar certificate_with_verified](./media/how-to-verify-certificates/add-certificate-with-verified.png)

1. Haga clic en **Guardar**.
1. El certificado se muestra en la pestaña de certificados con el estado *Verified* (Comprobado).
  
    ![Certificate_Status](./media/how-to-verify-certificates/certificate-status.png)

## <a name="manual-verification-of-intermediate-or-root-ca"></a>Comprobación manual de una entidad de certificación intermedia o raíz

La prueba de posesión implica los pasos siguientes:
1. Obtenga un código de verificación exclusivo generado por el servicio de aprovisionamiento para su certificado de entidad de certificación X.509. Puede hacer esto en Azure Portal.
2. Cree un certificado de verificación X.509 con el código de verificación como asunto y firme el certificado con la clave privada asociada a su certificado de entidad de certificación X.509.
3. Cargue el certificado de verificación firmado en el servicio. El servicio valida el certificado de verificación con la parte pública del certificado de entidad de certificación que se verifica, lo que demuestra que posee la clave privada del certificado de entidad de certificación.


### <a name="register-the-public-part-of-an-x509-certificate-and-get-a-verification-code"></a>Registro de la parte pública de un certificado X.509 y obtención de un código de verificación

Para registrar un certificado de entidad de certificación con el servicio de aprovisionamiento y obtener un código de verificación que pueda usar durante la prueba de posesión, siga estos pasos. 

1. En Azure Portal, vaya a su servicio de aprovisionamiento y abra **Certificados** en el menú izquierdo. 
2. Haga clic en **Agregar** para agregar un certificado nuevo.
3. Escriba un nombre para mostrar descriptivo para el certificado. Busque el archivo .cer o .pem que representa la parte pública del certificado X.509. Haga clic en **Cargar**.
4. Una vez que reciba la notificación de que el certificado se ha cargado correctamente, haga clic en **Guardar**.

    ![Carga del certificado](./media/how-to-verify-certificates/add-new-cert.png)  

   El certificado se mostrará en la lista **Explorador de certificados**. Observe que el **ESTADO** de este certificado es *Sin comprobar*.

5. Haga clic en el certificado que agregó en el paso anterior.

6. En **Detalles del certificado**, haga clic en **Generar código de verificación**.

7. El servicio de aprovisionamiento crea un **código de verificación** que puede usar para validar la titularidad del certificado. Copie el código en el Portapapeles. 

   ![Comprobar certificado](./media/how-to-verify-certificates/verify-cert.png)  

### <a name="digitally-sign-the-verification-code-to-create-a-verification-certificate"></a>Firma digital del código de verificación para crear un certificado de verificación

Ahora tiene que firmar el *código de verificación* con la clave privada asociada con el certificado de entidad de certificación X.509, lo que genera una firma. Esto se conoce como [prueba de posesión](https://tools.ietf.org/html/rfc5280#section-3.1) y da lugar a un certificado de verificación firmado.

Microsoft proporciona herramientas y ejemplos que pueden ayudarle a crear un certificado de verificación firmado: 

- El **SDK C de Azure IoT Hub** proporciona scripts de PowerShell (Windows) y Bash (Linux) que le ayudan a crear certificados de entidad de certificación y de hoja para desarrollo y a realizar la prueba de posesión con un código de verificación. Puede descargar los [archivos](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) correspondientes a su sistema en una carpeta de trabajo y seguir las instrucciones que aparecen en el [archivo Léame de administración de certificados de entidad de certificación](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) para realizar la prueba de posesión en un certificado de entidad de certificación. 
- El **SDK de C# de Azure IoT Hub** contiene el [ejemplo de verificación de certificado de grupo](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/main/provisioning/Samples/service/GroupCertificateVerificationSample), que puede usar para realizar la prueba de posesión.
 
> [!IMPORTANT]
> Además de realizar la prueba de posesión, los scripts de PowerShell y Bash citados anteriormente también permiten crear certificados raíz, certificados intermedios y certificados de hoja que pueden usarse para autenticar y aprovisionar dispositivos. Estos certificados se deben utilizar solamente para desarrollo. Nunca deben utilizarse en un entorno de producción. 

Los scripts de PowerShell y Bash proporcionados en la documentación y los SDK se basan en [OpenSSL](https://www.openssl.org/). También puede usar OpenSSL u otras herramientas de terceros para ayudarle a realizar la prueba de posesión. Para obtener un ejemplo del uso de las herramientas que se proporcionan con los SDK, consulte [Creación de una cadena de certificados X.509](tutorial-custom-hsm-enrollment-group-x509.md#create-an-x509-certificate-chain). 


### <a name="upload-the-signed-verification-certificate"></a>Carga del certificado de verificación firmado

1. Cargue la firma resultante como un certificado de verificación en su servicio de aprovisionamiento en el portal. En **Detalles del certificado** en Azure Portal, use el icono del _Explorador de archivos_ situado junto al campo **Archivo .pem o .cer del certificado de verificación.** para cargar el certificado de verificación firmado de su sistema.

2. Una vez que el certificado se cargue correctamente, haga clic en **Verificar**. El **ESTADO** del certificado cambia a **_Verificado_** en la lista **Explorador de certificados**. Si no se actualiza automáticamente, haga clic en **Actualizar**.

   ![Cargar comprobación de certificado](./media/how-to-verify-certificates/upload-cert-verification.png)  

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información acerca de cómo usar el portal para crear un grupo de inscripción, consulte [Administración de inscripciones de dispositivos con Azure Portal](how-to-manage-enrollments.md).
- Para obtener información acerca de cómo usar los SDK de servicio para crear un grupo de inscripción, consulte [Administración de inscripciones de dispositivos con los SDK del servicio](./quick-enroll-device-x509.md).
