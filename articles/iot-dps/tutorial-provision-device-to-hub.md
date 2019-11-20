---
title: "Öğretici: Azure IoT Hub cihaz sağlama hizmeti 'ni kullanarak cihaz sağlama"
description: "Öğretici: Azure IoT Hub cihaz sağlama hizmetini kullanarak cihazınızı tek bir IoT Hub 'ına sağlama"
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: be0b926b6beae2cb339ca232d2b792f50834d801
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112046"
---
# <a name="tutorial-provision-the-device-to-an-iot-hub-using-the-azure-iot-hub-device-provisioning-service"></a>Öğretici: Azure IoT Hub cihaz sağlama hizmeti 'ni kullanarak bir IoT Hub 'ına cihaz sağlama

Önceki öğreticide, Cihaz Sağlama hizmetine bağlanmak için bir cihazın nasıl ayarlanacağını öğrendiniz. Bu öğreticide, ootmatik sağlama ve **_kayıt listelerini_** kullanarak, cihazınızı tek bir IoT hub’a sağlamak için bu hizmetin nasıl kullanılacağını öğreneceksiniz. Bu öğretici şunların nasıl yapıldığını gösterir:

> [!div class="checklist"]
> * Cihazı kaydedin
> * Cihazı başlatın
> * Cihaz kayıtlı olduğunu doğrulayın

## <a name="prerequisites"></a>Önkoşullar

Devam etmeden önce, [Azure IoT Hub Cihazı Sağlama Hizmeti’ni kullanarak bir cihazı sağlamak üzere ayarlama](./tutorial-set-up-device.md) öğreticisinde açıklandığı gibi cihazınızı yapılandırdığınızdan emin olun.

Otomatik sağlama işlemini bilmiyorsanız, devam etmeden önce [Otomatik sağlama kavramlarını](concepts-auto-provisioning.md) gözden geçirdiğinizden emin olun.

<a id="enrolldevice"></a>
## <a name="enroll-the-device"></a>Cihazı kaydedin

Bu adım, cihazın benzersiz güvenlik yapılarının Cihaz Sağlama Hizmeti’ne eklenmesini kapsar. Bu güvenlik yapıları, aşağıdaki gibi cihazın [Kanıtlama mekanizması](concepts-device.md#attestation-mechanism)’nı temel alır:

- TPM tabanlı cihazlar için şunlar gerekir:
    - TPM yongası veya simülasyonu için benzersiz olan, TPM yonga üreticisinden alınan *Onay Anahtarı*.  Daha fazla bilgi için [TPM Onay Anahtarını Anlama](https://technet.microsoft.com/library/cc770443.aspx) bölümünü okuyun.
    - Ad alanındaki/kapsamdaki bir cihazı benzersiz şekilde tanımlamak için kullanılan *Kayıt Kimliği*. Bu kimlik, cihaz kimliğiyle aynı olabilir veya olmayabilir. Kimlik her cihaz için zorunludur. TPM tabanlı cihazlar için kayıt kimliği, TPM’den türetilebilir; örneğin, TPM Onay Anahtarının SHA-256 karması.

      [![Portalda TPM için kayıt bilgileri](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png)](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png#lightbox)  

- X.509 tabanlı cihazlar için şunlar gerekir:
    - [.pem](https://msdn.microsoft.com/library/windows/desktop/bb540819.aspx) veya *.cer* dosyası şeklinde *X.509 yongasına ya da simülasyonuna düzenlenen sertifika*. Bireysel kayıt için, X. 509.952 sisteminiz için cihaz başına *imzalı sertifikayı* kullanmanız gerekir, ancak kayıt grupları için *kök sertifikayı*kullanmanız gerekir. 

      [![Portalda X.509 kanıtı için tek kayıt ekleme](./media/tutorial-provision-device-to-hub/individual-enrollment.png)](./media/tutorial-provision-device-to-hub/individual-enrollment.png#lightbox)

Cihaz Sağlama Hizmeti’ne cihazı kaydetmenin iki yolu vardır:

- **Kayıt Grupları** Bu, belirli bir kanıtlama mekanizmasını paylaşan bir aygıt grubunu temsil eder. İstenen bir ilk yapılandırmayı paylaşan çok sayıda cihaz için veya aynı kiracıya giden tüm cihazlar için bir kayıt grubu kullanılmasını öneririz. Kayıt grupları için kimlik kanıtlaması hakkında daha fazla bilgi için bkz. [Güvenlik](concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

    [![Portalda X.509 kanıtı için grup kaydı ekleme](./media/tutorial-provision-device-to-hub/group-enrollment.png)](./media/tutorial-provision-device-to-hub/group-enrollment.png#lightbox)

- **Bireysel Kayıtlar** Bu, Cihaz Sağlama Hizmeti’ne kaydolabilecek tek bir cihaz için bir girdiyi temsil eder. Bireysel kayıtlar, kanıtlama mekanizması olarak x509 sertifikalarını veya SAS belirteçlerini (gerçek ya da sanal TPM’de) kullanabilir. Benzersiz ilk yapılandırma gerektiren cihazlar için veya kanıtlama mekanizması olarak TPM ya da sanal TPM aracılığıyla yalnızca SAS belirteçlerini kullanabilen cihazlar için bireysel kayıtların kullanılmasını öneririz. Bireysel kayıtlar için istenen IoT hub cihazı kimliği belirtilmiş olabilir.

Şimdi cihazın kanıtlama mekanizmasına dayalı olarak gerekli güvenlik yapılarını kullanarak Cihaz Sağlama Hizmeti örneğinize cihazı kaydedersiniz: 

1. Azure portalında oturum açın, sol taraftaki menüden **Tüm kaynaklar** düğmesine tıklayın ve Cihaz Sağlama hizmetinizi açın.

2. Cihaz Sağlama Hizmeti özet dikey penceresinde, **Kayıtları yönet**’i seçin. Cihaz kurulumunuza göre **Bireysel Kayıtlar** sekmesini veya **Kayıt Grupları** sekmesini seçin. Üstteki **Ekle** düğmesine tıklayın. Kimlik kanıtlama **Mekanizması** olarak **TPM** veya *X.509* seçeneğini belirleyin ve önceden açıklandığı şekilde uygun güvenlik yapılarını girin. Yeni bir **IoT Hub cihaz kimliği** girebilirsiniz. Tamamlandığında **Kaydet** düğmesine tıklayın. 

3. Cihaz başarıyla kaydedildiğinde cihazın portalda şu şekilde görüntülendiğini görmeniz gerekir:

    ![Portalda başarılı TPM kaydı](./media/tutorial-provision-device-to-hub/tpm-enrollment-success.png)

Kayıttan sonra sağlama hizmeti, cihazın önyüklenmesini ve daha sonra buna bağlanmasını bekler. Cihazınız ilk kez önyüklendiğinde, istemci SDK’sı kitaplığı, cihazdaki güvenlik yapılarını ayıklamak için yonganız ile etkileşime geçer ve Cihaz Sağlama hizmetinize kaydı doğrular. 

## <a name="start-the-iot-device"></a>IoT cihazını başlatma

IoT cihazınız gerçek veya sanal bir cihaz olabilir. IoT cihazı, Cihaz Sağlama Hizmeti örneği ile kaydedildiğinden açılabilir ve kanıtlama mekanizmasını kullanarak tanınmak için sağlama hizmetini çağırabilir. Sağlama hizmeti cihazı tanıdıktan sonra bir IoT hub'a atar. 

C, Java, C#, Node.js ve Python için hem TPM hem de X.509 kanıtlamasını kullanan sanal cihaz örnekleri sağlanmıştır. Örneğin TPM ve [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)'sını kullanan bir sanal cihaz, [Cihaz için ilk önyükleme sırası simülasyonu yapma](quick-create-simulated-device.md#simulate-first-boot-sequence-for-the-device) bölümünde anlatılmıştır. Aynı cihazın X.509 sertifikası kanıtlamasını kullanan sürümü bu [önyükleme sırası](quick-create-simulated-device-x509.md#simulate-first-boot-sequence-for-the-device) bölümünde yer alır.

Gerçek cihaz örneği için bkz. [MXChip Iot DevKit kılavuzu](how-to-connect-mxchip-iot-devkit.md).

Cihazınızın istemci uygulamasının, Cihaz Sağlama Hizmetinize kaydı başlatmasını sağlamak için cihazı başlatın.  

## <a name="verify-the-device-is-registered"></a>Cihaz kayıtlı olduğunu doğrulayın

Cihazınız önyüklendikten sonra aşağıdaki eylemler gerçekleşmelidir:

1. Cihaz, Cihaz Sağlama hizmetinize bir kayıt isteği gönderir.
2. TPM cihazları için Cihaz Sağlama Hizmeti bir kayıt sınaması gönderir ve cihazınız da buna yanıt verir. 
3. Kayıt başarılı olduğunda Cihaz Sağlama hizmeti, IoT hub URI’sini, cihaz kimliğini ve şifrelenmiş anahtarı cihaza geri gönderir. 
4. Sonra cihazdaki IoT Hub istemci uygulaması, hub’ınıza bağlanır. 
5. Hub’a başarılı bağlantı gerçekleştiğinde, IoT hub’ın **IoT Cihazları** gezgininde cihazın görünmesi gerekir. 

    ![Portalda hub ile başarılı bağlantı](./media/tutorial-provision-device-to-hub/hub-connect-success.png)

Daha fazla bilgi için bkz. cihaz istemci örneği sağlama, [prov_dev_client_sample. c](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c). Örnek, TPM, X. 509.440 sertifikaları ve simetrik anahtarlar kullanılarak sanal bir cihazın sağlanmasını gösterir. Örneği kullanmaya ilişkin adım adım yönergeler için [TPM](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device), [X. 509.440](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device-x509)ve [simetrik anahtar](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device-symm-key) kanıtlama hızlı başlangıçlarına geri bakın.

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Cihazı kaydedin
> * Cihazı başlatın
> * Cihaz kayıtlı olduğunu doğrulayın

Yük dengeli hublar arasında birden çok cihaz sağlamayı öğrenmek için sonraki öğreticiye ilerleyin. 

> [!div class="nextstepaction"]
> [Yük dengeli IoT hubları arasında cihaz sağlama](./tutorial-provision-multiple-hubs.md)
