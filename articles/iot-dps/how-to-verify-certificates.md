---
title: Azure IoT Hub cihaz sağlama hizmeti ile X. 509.440 CA sertifikaları için birlikte kullanma
description: Azure IoT Hub cihaz sağlama hizmeti ile X. 509.440 CA sertifikaları için birlikte kullanma
author: wesmc7777
ms.author: wesmc
ms.date: 02/26/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: e762a1ab307bdc5ca9369c3f2e424cf6fd35f163
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890639"
---
# <a name="how-to-do-proof-of-possession-for-x509-ca-certificates-with-your-device-provisioning-service"></a>Cihaz sağlama hizmeti ile X. 509.440 CA sertifikaları için birlikte kullanma

Doğrulanmış bir X. 509.440 sertifika yetkilisi (CA) sertifikası, karşıya yüklenen ve sağlama hizmetinize kaydedilen ve hizmetle birlikte bulunan bir CA sertifikasıdır. 

Sahip olma kanıtı aşağıdaki adımları içerir:
1. X. 509.440 CA sertifikanız için sağlama hizmeti tarafından oluşturulan benzersiz bir doğrulama kodu alın. Bunu Azure portal yapabilirsiniz.
2. Onaylama kodu olan bir X. 509.952 doğrulama sertifikası oluşturun ve sertifikayı X. 509.440 CA sertifikanız ile ilişkili özel anahtarla imzalayın.
3. İmzalı doğrulama sertifikasını hizmete yükleyin. Hizmet, CA sertifikasının doğrulanması için ortak kısmını kullanarak doğrulama sertifikasını doğrular ve bu nedenle CA sertifikasının özel anahtarını elinde bulabilirsiniz.

Doğrulanan sertifikalar, kayıt grupları kullanılırken önemli bir rol oynar. Sertifika sahipliğinin doğrulanması, sertifikanın yükleyici 'nin sertifikanın özel anahtarıyla aynı olduğundan emin olarak ek bir güvenlik katmanı sağlar. Doğrulama kötü niyetli bir aktörün bir ara sertifikayı ayıklamasını ve bu sertifikayı kullanarak kendi sağlama hizmetinde bir kayıt grubu oluşturmasını ve cihazlarınızı etkili bir şekilde ele almasını engeller. Bir sertifika zincirindeki kök veya ara sertifikanın sahipliğini seçerek, bu kayıt grubunun bir parçası olarak kaydedilecek cihazlar için yaprak sertifikaları oluşturma izninizin olduğunu size sunuyoruz. Bu nedenle, bir kayıt grubunda yapılandırılan kök veya ara sertifikanın doğrulanmış bir sertifika olması veya bir cihazın hizmet ile kimliğini doğruladığında sunduğu sertifika zincirindeki doğrulanmış bir sertifikaya toplaması gerekir. Kayıt grupları hakkında daha fazla bilgi edinmek için bkz. [x. 509.440 sertifikaları](concepts-security.md#x509-certificates) ve [x. 509.440 sertifikalarıyla sağlama hizmetine cihaz erişimini denetleme](concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

## <a name="register-the-public-part-of-an-x509-certificate-and-get-a-verification-code"></a>Bir X. 509.952 sertifikasının ortak bölümünü kaydedin ve doğrulama kodu alın

Sağlama hizmetinize bir CA sertifikası kaydetmek ve sahip olma sürecinde kullanabileceğiniz bir doğrulama kodu almak için aşağıdaki adımları izleyin. 

1. Azure portal, sağlama hizmetinize gidin ve sol taraftaki menüden **Sertifikalar** ' ı açın. 
2. Yeni bir sertifika eklemek için **Ekle** ' ye tıklayın.
3. Sertifikanız için kolay bir görünen ad girin. X. 509.440 sertifikanızın ortak bölümünü temsil eden. cer veya. ped dosyasına gidin. **Karşıya Yükle**'ye tıklayın.
4. Sertifikanızın başarıyla karşıya yüklendiğini belirten bir bildirim aldıktan sonra **Kaydet**' e tıklayın.

    ![Sertifikayı karşıya yükleme](./media/how-to-verify-certificates/add-new-cert.png)  

   Sertifikanız, **sertifika Gezgini** listesinde gösterilir. Bu sertifikanın **durumunun** *doğrulanmamış*olduğunu unutmayın.

5. Önceki adımda eklediğiniz sertifikaya tıklayın.

6. **Sertifika ayrıntıları**' nda **doğrulama kodu oluştur**' a tıklayın.

7. Sağlama Hizmeti, sertifika sahipliğini doğrulamak için kullanabileceğiniz bir **doğrulama kodu** oluşturur. Kodu panonuza kopyalayın. 

   ![Sertifikayı doğrula](./media/how-to-verify-certificates/verify-cert.png)  

## <a name="digitally-sign-the-verification-code-to-create-a-verification-certificate"></a>Doğrulama sertifikası oluşturmak için doğrulama kodunu dijital olarak imzala

Şimdi, *doğrulama kodunu* , bir Imza üreten X. 509.440 CA sertifikanız ile ilişkili özel anahtarla imzalamanız gerekir. Bu, sahip olma [kanıtı](https://tools.ietf.org/html/rfc5280#section-3.1) ve imzalı doğrulama sertifikası sonuçları olarak bilinir.

Microsoft, imzalı doğrulama sertifikası oluşturmanıza yardımcı olabilecek araçlar ve örnekler sağlar: 

- **Azure IoT Hub C SDK** 'sı, geliştirme için CA ve yaprak sertifikaları oluşturmanıza ve bir doğrulama kodu kullanarak sahip olma kanıtını gerçekleştirmenize yardımcı olmak için PowerShell (Windows) ve Bash (Linux) betikleri sağlar. Sisteminizle ilgili [dosyaları](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) çalışan bir klasöre indirebilir ve CA [sertifikalarını yönetme Benioku](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) dosyasındaki yönergeleri izleyerek CA sertifikasına sahip olma işlemini gerçekleştirebilirsiniz. 
- **Azure IoT Hub C# SDK 'sı** , sahip olma kanıtını yapmak Için kullanabileceğiniz [Grup sertifikası doğrulama örneğini](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample)içerir.
 
> [!IMPORTANT]
> Aynı zamanda, sahip olma sağlamasını gerçekleştirmeye ek olarak, daha önce alıntı yapılan PowerShell ve Bash betikleri, cihazların kimliğini doğrulamak ve sağlamak için kullanılabilecek kök sertifikalar, ara sertifikalar ve yaprak sertifikalar oluşturmanıza de olanak tanır. Bu sertifikalar yalnızca geliştirme için kullanılmalıdır. Bunlar bir üretim ortamında asla kullanılmamalıdır. 

Belgelerde ve SDK 'larda verilen PowerShell ve Bash betikleri [OpenSSL](https://www.openssl.org/)'e dayanır. Ayrıca, aynı zamanda OpenSSL veya diğer üçüncü taraf araçları da kullanabilirsiniz. SDK 'lar ile birlikte sunulan araç hakkında daha fazla bilgi için bkz. [SDK 'larda sunulan araçları kullanma](how-to-use-sdk-tools.md). 


## <a name="upload-the-signed-verification-certificate"></a>İmzalı doğrulama sertifikasını karşıya yükle

1. Sonuç olarak verilen imzayı, portaldaki sağlama hizmetinize doğrulama sertifikası olarak yükleyin. Azure portal **sertifika ayrıntıları** ' nda, imzalı doğrulama sertifikasını sisteminizden karşıya yüklemek Için **doğrulama sertifikası. pek veya. cer dosya** alanının yanındaki _Dosya Gezgini_ simgesini kullanın.

2. Sertifika başarıyla karşıya yüklendikten sonra **Doğrula**' ya tıklayın. Sertifika **Gezgini** listesinde sertifikanızın **durumu** **_doğrulandı_** olarak değişir. Otomatik olarak güncelleştirmezse **Yenile** ' ye tıklayın.

   ![Sertifika doğrulamasını karşıya yükle](./media/how-to-verify-certificates/upload-cert-verification.png)  

## <a name="next-steps"></a>Sonraki adımlar

- Portal 'ın bir kayıt grubu oluşturmak üzere nasıl kullanılacağı hakkında bilgi edinmek için bkz. [Azure Portal cihaz kayıtlarını yönetme](how-to-manage-enrollments.md).
- Hizmet SDK 'larının bir kayıt grubu oluşturmak için nasıl kullanılacağı hakkında bilgi edinmek için bkz. [cihaz kayıtlarını hizmet SDK 'ları Ile yönetme](how-to-manage-enrollments-sdks.md).










