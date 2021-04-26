---
title: 'Hızlı başlangıç-C kullanarak sanal bir X. 509.440 cihazını Azure IoT Hub sağlama #'
description: Hızlı başlangıç-Azure IoT Hub cihaz sağlama hizmeti (DPS) için C# cihaz SDK 'sını kullanarak bir X. 509.440 cihazı oluşturun ve sağlayın. Bu hızlı başlangıçta bireysel kayıtlar kullanılmaktadır.
author: wesmc7777
ms.author: wesmc
ms.date: 02/01/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: 7aca75d1abed5470d51de22f9285459381f684bd
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868600"
---
# <a name="quickstart-create-and-provision-an-x509-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>Hızlı başlangıç: IoT Hub cihaz sağlama hizmeti için C# cihaz SDK 'sını kullanarak bir X. 509.440 cihazı oluşturma ve sağlama

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Bu adımlarda, bir X. 509.440 cihazını sağlamak için [C# için Azure IoT örneklerinden](https://github.com/Azure-Samples/azure-iot-samples-csharp) cihaz kodunun nasıl kullanılacağı gösterilmektedir. Bu makalede, cihaz sağlama hizmeti 'ni kullanarak bir IoT Hub bağlanmak için geliştirme makinenizde cihaz örnek kodunu çalıştıracaksınız.

## <a name="prerequisites"></a>Önkoşullar

Oto sağlama işlemini bilmiyorsanız, [sağlamaya](about-iot-dps.md#provisioning-process) genel bakış konusunu gözden geçirin. Ayrıca devam etmeden önce [IoT Hub Cihazı Sağlama Hizmetini Azure portalıyla ayarlama](./quick-setup-auto-provision.md) bölümünde bulunan adımları tamamladığınızdan emin olun.

Azure IoT Cihaz Sağlama Hizmeti iki tür kaydı destekler:
- [Kayıt grupları](concepts-service.md#enrollment-group): Birden fazla ilgili cihazı kaydetmek için kullanılır.
- [Bireysel kayıtlar](concepts-service.md#individual-enrollment): Tek bir cihazı kaydetmek için kullanılır.

Bu makalede bireysel kayıtlar gösterilmektedir.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

<a id="setupdevbox"></a>
## <a name="prepare-the-development-environment"></a>Geliştirme ortamını hazırlama 

1. `git` uygulamasının makinenizde yüklü olduğundan ve komut penceresinden erişilebilir ortam değişkenlerine eklendiğinden emin olun. Yüklenecek `git` araçlarının son sürümleri için [Software Freedom Conservancy’nin Git istemci araçlarına](https://git-scm.com/download/) bakın. Bunlara yerel Git deponuzla etkileşim kurmak için kullanabileceğiniz bir komut satırı uygulaması olan **Git Bash** dahildir. 

1. Bir komut istemi veya Git Bash’i açın. C# GitHub deposu için Azure IoT örneklerini kopyalayın:
    
    ```bash
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```

1. Makinenizde [.NET Core 3,1 SDK veya sonraki bir sürümünün](https://dotnet.microsoft.com/download) yüklü olduğundan emin olun. Sürümünüzü denetlemek için aşağıdaki komutu kullanabilirsiniz.

    ```bash
    dotnet --info
    ```

## <a name="create-a-self-signed-x509-device-certificate"></a>Otomatik olarak imzalanan X.509 sertifikası oluşturma

Bu bölümde, `iothubx509device1` Konu ortak adı olarak kullanarak otomatik olarak imzalanan bir X. 509.440 test sertifikası oluşturacaksınız. Aşağıdaki noktaları göz önünde bulundurmanız önemlidir:

* Otomatik olarak imzalanan sertifikalar yalnızca test amaçlıdır ve üretimde kullanılmamalıdır.
* Otomatik olarak imzalanan sertifikanın varsayılan sona erme tarihi bir yıldır.
* IoT cihazının cihaz KIMLIĞI, sertifikadaki Konu ortak adı olacaktır. [CIHAZ kimliği dize gereksinimleriyle](../iot-hub/iot-hub-devguide-identity-registry.md#device-identity-properties)uyumlu bir konu adı kullandığınızdan emin olun.

Cihaza yönelik bireysel kayıt girişiyle kullanılacak sertifikayı oluşturmak için [X509Sample](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/X509Sample) 'deki örnek kodu kullanacaksınız.


1. Bir PowerShell isteminde dizinleri X. 509.952 cihaz sağlama örneği için proje dizini olarak değiştirin.

    ```powershell
    cd .\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample
    ```

2. Örnek kod parola korumalı PKCS12 biçimindeki bir dosya (certificate.pfx) içinde depolanan X.509 sertifikalarını kullanacak şekilde ayarlanmıştır. Ayrıca, bu hızlı başlangıçta tek bir kayıt oluşturmak için ortak anahtar sertifika dosyası (Certificate. cer) gerekir. Otomatik olarak imzalanan sertifikayı ve ilişkili. cer ve. pfx dosyalarını oluşturmak için aşağıdaki komutu çalıştırın:

    ```powershell
    PS D:\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample> .\GenerateTestCertificate.ps1 iothubx509device1
    ```

3. Bu betik sizden PFX parolası ister. Bu parolayı hatırlayın, örneği çalıştırdığınızda daha sonra tekrar kullanmanız gerekir. `certutil`' İ çalıştırarak sertifikanın dökümünü yapabilir ve konu adını doğrulayabilirsiniz.

    ```powershell
    PS D:\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample> certutil .\certificate.pfx
    Enter PFX password:
    ================ Certificate 0 ================
    ================ Begin Nesting Level 1 ================
    Element 0:
    Serial Number: 7b4a0e2af6f40eae4d91b3b7ff05a4ce
    Issuer: CN=iothubx509device1, O=TEST, C=US
     NotBefore: 2/1/2021 6:18 PM
     NotAfter: 2/1/2022 6:28 PM
    Subject: CN=iothubx509device1, O=TEST, C=US
    Signature matches Public Key
    Root Certificate: Subject matches Issuer
    Cert Hash(sha1): e3eb7b7cc1e2b601486bf8a733887a54cdab8ed6
    ----------------  End Nesting Level 1  ----------------
      Provider = Microsoft Strong Cryptographic Provider
    Signature test passed
    CertUtil: -dump command completed successfully.    
    ```

 ## <a name="create-an-individual-enrollment-entry-for-the-device"></a>Cihaz için bireysel kayıt girişi oluşturma


1. Azure portal oturum açın, sol taraftaki menüden **tüm kaynaklar** düğmesini seçin ve sağlama hizmetinizi açın.

2. Cihaz sağlama hizmeti menüsünden kayıtları **Yönet**' i seçin. **Bireysel** kayıtlar sekmesini seçin ve üst kısımdaki **tek kayıt Ekle** düğmesini seçin. 

3. **Kayıt Ekle** panelinde, aşağıdaki bilgileri girin:
   - Kimlik onay *Mekanizması* olarak **X.509**'u seçin.
   - *Birincil sertifika. pek veya. cer dosyası*' nın altında, önceki adımlarda oluşturulan sertifika **. cer** sertifika dosyasını seçmek için *Dosya Seç* ' i seçin.
   - **Cihaz Kimliği** alanını boş bırakın. Cihazınız, cihaz kimliği X.509 sertifikasındaki ortak ad (CN) olan **iothubx509device1** olacak şekilde sağlanır. Bu ortak ad aynı zamanda tek kayıt girişinin kayıt KIMLIĞI için kullanılan ad olacaktır. 
   - İsteğe bağlı olarak, aşağıdaki bilgileri sağlayabilirsiniz:
       - Sağlama hizmetinizle bağlanacak IoT hub'ını seçin.
       - **Başlangıç cihaz ikizi durumu** alanını cihaz için istenen başlangıç yapılandırmasına göre güncelleştirin.
   - Tamamlandıktan sonra **Kaydet** düğmesine basın. 

     [![Portalda X. 509.440 kanıtlama için bireysel kayıt ekleme](./media/quick-create-simulated-device-x509-csharp/device-enrollment.png)](./media/quick-create-simulated-device-x509-csharp/device-enrollment.png#lightbox)
    
   Kayıt başarıyla tamamlandığında, X.509 kayıt girişiniz cihazınız *Bireysel Kayıtlar* sekmesindeki *Kayıt Kimliği* sütununun altında **iothubx509device1** olarak gösterilir. 



## <a name="provision-the-device"></a>Cihazı sağlama

1. Sağlama hizmetinizin **genel bakış** dikey penceresinden, **_kimlik kapsamı_** değerini aklınızda olun.

    ![Portal dikey penceresinden Cihaz Sağlama Hizmeti uç noktası bilgilerini ayıklama](./media/quick-create-simulated-device-x509-csharp/copy-scope.png) 


2. X.509 cihaz sağlama örneğini derlemek ve çalıştırmak için aşağıdaki komutu yazın. `<IDScope>` değerinin yerine sağlama hizmetinizin Kimlik Kapsamını girin. 

    Sertifika dosyası varsayılan olarak *./Certificate.exe* olur ve. pfx parolasını ister.  

    ```powershell
    dotnet run -- -s <IDScope>
    ```

    Her şeyi parametre olarak geçirmek istiyorsanız aşağıdaki örnek biçimi kullanabilirsiniz.

    ```powershell
    dotnet run -- -s 0ne00000A0A -c certificate.pfx -p 1234
    ```


3. Cihaz, DPS 'e bağlanır ve bir IoT Hub atanır. Cihaza Ayrıca hub 'a bir telemetri iletisi gönderilir.

    ```output
    Loading the certificate...
    Found certificate: 10952E59D13A3E388F88E534444484F52CD3D9E4 CN=iothubx509device1, O=TEST, C=US; PrivateKey: True
    Using certificate 10952E59D13A3E388F88E534444484F52CD3D9E4 CN=iothubx509device1, O=TEST, C=US
    Initializing the device provisioning client...
    Initialized for registration Id iothubx509device1.
    Registering with the device provisioning service...
    Registration status: Assigned.
    Device iothubx509device2 registered to sample-iot-hub1.azure-devices.net.
    Creating X509 authentication for IoT Hub...
    Testing the provisioned device with IoT Hub...
    Sending a telemetry message...
    Finished.
    ```

4. Cihazın sağlandığını doğrulayın. Cihazın, sağlama hizmetinize bağlı olan IoT Hub 'ına başarıyla sağlanması sırasında, cihazın **IoT cihazları** dikey PENCERESINDE cihaz kimliği görüntülenir. 

    ![Cihaz IOT hub'da kayıtlı](./media/quick-create-simulated-device-x509-csharp/registration.png) 

    Cihazınız için *başlangıç cihaz ikizi durumu* ayarının kayıt girişindeki varsayılan değerini değiştirdiyseniz istenen ikili durumu hub'dan çekerek ona göre hareket edebilir. Daha fazla bilgi için bkz. [IoT Hub'daki cihaz ikizlerini kavrama ve kullanma](../iot-hub/iot-hub-devguide-device-twins.md)


## <a name="clean-up-resources"></a>Kaynakları temizleme

Cihaz istemci örneğini üzerinde çalışmaya ve keşfetmeye devam etmeyi planlıyorsanız, bu hızlı başlangıçta oluşturulan kaynakları temizlemeyin. Devam etmeyi planlamıyorsanız, bu hızlı başlangıç tarafından oluşturulan tüm kaynakları silmek için aşağıdaki adımları kullanın.

1. Makinenizde cihaz istemci örnek çıktı penceresini kapatın.
1. Makinenizde TPM simülatörü penceresini kapatın.
1. Azure portal sol taraftaki menüden **tüm kaynaklar** ' ı seçin ve ardından cihaz sağlama hizmetinizi seçin. **Genel bakış** dikey penceresinin üst kısmında, bölmenin en üstünde bulunan **Sil** ' e basın.  
1. Azure portal sol taraftaki menüden **tüm kaynaklar** ' ı seçin ve ardından IoT Hub 'ınızı seçin. **Genel bakış** dikey penceresinin üst kısmında, bölmenin en üstünde bulunan **Sil** ' e basın.  

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure IoT Hub cihaz sağlama hizmeti 'ni kullanarak IoT Hub 'ınıza bir X. 509.952 cihazı sağlamış olursunuz. X. 509.952 cihazınızı programlı bir şekilde kaydetmeyi öğrenmek için, X. 509.440 cihazlarının programlı kaydı için hızlı başlangıç 'a geçin. 

> [!div class="nextstepaction"]
> [Azure hızlı başlangıç-X. 509.440 cihazlarını Azure IoT Hub cihaz sağlama hizmeti 'ne kaydetme](quick-enroll-device-x509-csharp.md)
