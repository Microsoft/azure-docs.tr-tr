---
title: Hızlı başlangıç-Python kullanarak sanal bir X. 509.440 cihazını Azure IoT Hub sağlama
description: Hızlı başlangıç-IoT Hub cihaz sağlama hizmeti (DPS) için Python cihaz SDK 'sını kullanarak sanal bir X. 509.952 cihazı oluşturun ve sağlayın. Bu hızlı başlangıçta bireysel kayıtlar kullanılmaktadır.
author: wesmc7777
ms.author: wesmc
ms.date: 01/29/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: python
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 5db02f8ca1f0c311617a787525ee2fa5856eb5dc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045272"
---
# <a name="quickstart-create-and-provision-a-simulated-x509-device-using-python-device-sdk-for-iot-hub-device-provisioning-service"></a>Hızlı başlangıç: IoT Hub cihaz sağlama hizmeti için Python cihaz SDK 'sını kullanarak sanal bir X. 509.952 cihazı oluşturma ve sağlama

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Bu hızlı başlangıçta, Python X. 509.952 cihazı olarak bir geliştirme makinesi sağlayacaksınız. Cihazı IoT Hub 'ınıza bağlamak için [Azure IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python) 'dan örnek cihaz kodu kullanırsınız. Tek bir kayıt, bu örnekteki cihaz sağlama hizmeti (DPS) ile birlikte kullanılır.

## <a name="prerequisites"></a>Önkoşullar

- [Sağlama](about-iot-dps.md#provisioning-process) kavramları hakkında bilgi edinin.
- [Azure Portal Ile cihaz sağlama hizmeti IoT Hub ayarlama](./quick-setup-auto-provision.md)işlemi tamamlandı.
- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz bir tane oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Python 3,6 veya üzeri](https://www.python.org/downloads/)
- [Git](https://git-scm.com/download/).


[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>Ortamı hazırlama 

1. `git` uygulamasının makinenizde yüklü olduğundan ve komut penceresinden erişilebilir ortam değişkenlerine eklendiğinden emin olun. Yüklenecek `git` araçlarının son sürümleri için [Software Freedom Conservancy’nin Git istemci araçlarına](https://git-scm.com/download/) bakın. Bunlara yerel Git deponuzla etkileşim kurmak için kullanabileceğiniz bir komut satırı uygulaması olan **Git Bash** dahildir. 

2. Git Bash istemi açın. [Azure IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python)için GitHub deposunu kopyalayın.
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```


## <a name="create-a-self-signed-x509-device-certificate"></a>Otomatik olarak imzalanan X.509 sertifikası oluşturma 

Bu bölümde, otomatik olarak imzalanan bir X. 509.440 sertifikası oluşturacaksınız. Aşağıdaki noktaları göz önünde bulundurmanız önemlidir:

* Otomatik olarak imzalanan sertifikalar yalnızca test amaçlıdır ve üretimde kullanılmamalıdır.
* Otomatik olarak imzalanan sertifikanın varsayılan sona erme tarihi bir yıldır.

Cihaz kimlik doğrulaması için zaten cihaz sertifikalarınız yoksa, bu makaleyle test için OpenSSL ile otomatik olarak imzalanan bir sertifika oluşturabilirsiniz.  OpenSSL, git yüklemesine dahildir. 

1. Git Bash isteminde aşağıdaki komutu çalıştırın.

    # <a name="windows"></a>[Windows](#tab/windows)
    
    ```bash
    winpty openssl req -outform PEM -x509 -sha256 -newkey rsa:4096 -keyout ./python-device.key.pem -out ./python-device.pem -days 365 -extensions usr_cert -subj "//CN=Python-device-01"
    ```

    > [!IMPORTANT]
    > Konu adı () için verilen ek eğik çizgi `//CN=Python-device-01` yalnızca Windows platformlarında git ile dizeden çıkmak için gereklidir. 

    # <a name="linux"></a>[Linux](#tab/linux)
    
    ```bash
    openssl req -outform PEM -x509 -sha256 -newkey rsa:4096 -keyout ./python-device.key.pem -out ./python-device.pem -days 365 -extensions usr_cert -subj "/CN=Python-device-01"
    ```
    
    ---
    
2. **Pek Pass Ifadesi girmeniz** istendiğinde, `1234` Bu makaleyle test için pass deyimini kullanın.    

3. İstendiğinde bir kez daha doğruladıktan sonra, **pek Pass tümceciğini girin:**, pass tümceciğini `1234` yeniden kullanın.    

Komutu çalıştırdığınız dizinde bir test sertifika dosyası (*Python-Device. pek*) ve özel anahtar dosyası (*Python-Device. Key. ped*) oluşturulur `openssl` .


## <a name="create-an-individual-enrollment-entry-in-dps"></a>DPS içinde bireysel kayıt girişi oluşturma


Azure IoT Cihaz Sağlama Hizmeti iki tür kaydı destekler:

- [Kayıt grupları](concepts-service.md#enrollment-group): Birden fazla ilgili cihazı kaydetmek için kullanılır.
- [Bireysel](concepts-service.md#individual-enrollment)kayıtlar: tek bir cihazı kaydetmek için kullanılır.

Bu makalede, tek bir cihazın IoT Hub 'ı ile sağlanması için tek bir kayıt gösterilmektedir.

1. Azure portal oturum açın, sol taraftaki menüden **tüm kaynaklar** düğmesini seçin ve sağlama hizmetinizi açın.

2. Cihaz sağlama hizmeti menüsünden kayıtları **Yönet**' i seçin. **Bireysel** kayıtlar sekmesini seçin ve üst kısımdaki **tek kayıt Ekle** düğmesini seçin. 

3. **Kayıt Ekle** panelinde, aşağıdaki bilgileri girin:
   - Kimlik onay *Mekanizması* olarak **X.509**'u seçin.
   - *Birincil sertifika. pek veya. cer dosyası*' nın altında, daha önce oluşturulan test sertifikasını kullanıyorsanız **Python-Device. ped** sertifika dosyasını seçmek için *Dosya Seç* ' i seçin.
   - İsteğe bağlı olarak, aşağıdaki bilgileri sağlayabilirsiniz:
     - Sağlama hizmetinizle bağlanacak IoT hub'ını seçin.
     - **Başlangıç cihaz ikizi durumu** alanını cihaz için istenen başlangıç yapılandırmasına göre güncelleştirin.
   - Tamamlandıktan sonra **Kaydet** düğmesine basın. 

     [![Portalda X. 509.440 kanıtlama için bireysel kayıt ekleme](./media/python-quick-create-simulated-device-x509/device-enrollment.png)](./media/python-quick-create-simulated-device-x509/device-enrollment.png#lightbox)

   Kayıt başarıyla tamamlandığında, X. 509.440 cihazınız, *bireysel* kayıtlar SEKMESINDEKI *kayıt kimliği* sütununun altında **Python-Device-01** olarak görünür. Bu kayıt değeri, cihaz sertifikasındaki konu adından gelir. 

## <a name="simulate-the-device"></a>Cihazı benzetme

Python sağlama örneği, [provision_x509. Kopyala](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/provision_x509.py) `azure-iot-sdk-python/azure-iot-device/samples/async-hub-scenarios` dizininde bulunur. Bu örnek, DPS kullanarak bir IoT cihazının kimliğini doğrulamak ve sağlamak için altı ortam değişkeni kullanır. Bu ortam değişkenleri şunlardır:

| Değişken adı              | Description                                     |
| :------------------------- | :---------------------------------------------- |
| `PROVISIONING_HOST`        |  Bu değer, DPS kaynağına bağlanmak için kullanılan küresel uç noktasıdır |    
| `PROVISIONING_IDSCOPE`     |  Bu değer, DPS kaynağınızın KIMLIK kapsamıdır |    
| `DPS_X509_REGISTRATION_ID` |  Bu değer, cihazınızın KIMLIĞIDIR. Ayrıca, cihaz sertifikasındaki konu adıyla aynı olmalıdır |    
| `X509_CERT_FILE`           |  Cihaz Sertifikası dosya adı |    
| `X509_KEY_FILE`            |  Cihaz sertifikanızın özel anahtar dosya adı |
| `PASS_PHRASE`              |  Sertifikayı ve özel anahtar dosyasını () şifrelemek için kullandığınız Pass ifadesi `1234` . |    

1. Cihaz sağlama hizmeti menüsünden **genel bakış**' ı seçin. _Kimlik kapsamınızı_ ve _küresel cihaz uç_ noktanızı aklınızda edin.

    ![Hizmet bilgileri](./media/python-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. Git Bash isteminde, genel cihaz uç noktası ve KIMLIK kapsamı için ortam değişkenlerini eklemek üzere aşağıdaki komutları kullanın.

    ```bash
    $export PROVISIONING_HOST=global.azure-devices-provisioning.net
    $export PROVISIONING_IDSCOPE=<ID scope for your DPS resource>
    ```

3. IoT cihazının kayıt KIMLIĞI, cihaz sertifikasındaki konu adı ile aynı olmalıdır. Otomatik olarak imzalanan bir test sertifikası oluşturduysanız, `Python-device-01` cihazın konu adı ve kayıt kimliği olur. 

    Zaten bir cihaz sertifikanız varsa, `certutil` otomatik olarak imzalanan bir test sertifikası için aşağıda gösterildiği gibi cihazınız için kullanılan konu ortak adını doğrulamak üzere ' ı kullanabilirsiniz:

    ```bash
    $ certutil python-device.pem
    X509 Certificate:
    Version: 3
    Serial Number: fa33152fe1140dc8
    Signature Algorithm:
        Algorithm ObjectId: 1.2.840.113549.1.1.11 sha256RSA
        Algorithm Parameters:
        05 00
    Issuer:
        CN=Python-device-01
      Name Hash(sha1): 1dd88de40e9501fb64892b698afe12d027011000
      Name Hash(md5): a62c784820daa931b9d3977739b30d12
    
     NotBefore: 1/29/2021 7:05 PM
     NotAfter: 1/29/2022 7:05 PM
    
    Subject:
        ===> CN=Python-device-01 <===
      Name Hash(sha1): 1dd88de40e9501fb64892b698afe12d027011000
      Name Hash(md5): a62c784820daa931b9d3977739b30d12
    ```

    Git Bash isteminde, kayıt KIMLIĞI için ortam değişkenini aşağıdaki şekilde ayarlayın:

    ```bash
    $export DPS_X509_REGISTRATION_ID=Python-device-01
    ```

4. Git Bash isteminde, sertifika dosyası, özel anahtar dosyası ve Geçiş tümceciği için ortam değişkenlerini ayarlayın.

    ```bash
    $export X509_CERT_FILE=./python-device.pem
    $export X509_KEY_FILE=./python-device.key.pem
    $export PASS_PHRASE=1234
    ```

5. [Provision_x509. Kopyala](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/provision_x509.py) için kodu gözden geçirin **Python sürüm 3,7** veya üzerini kullanmıyorsanız, değişiklik yapmak için [burada bahsedilen kod değişikliğini](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios#advanced-iot-hub-scenario-samples-for-the-azure-iot-hub-device-sdk) değiştirin `asyncio.run(main())` ve değişikliklerinizi kaydedin. 

6. Örnek uygulamayı çalıştırın. Örnek bağlanır, cihazı bir hub 'a hazırlar ve hub 'a bazı test iletileri gönderir.

    ```bash
    $ winpty python azure-iot-sdk-python/azure-iot-device/samples/async-hub-scenarios/provision_x509.py
    RegistrationStage(RequestAndResponseOperation): Op will transition into polling after interval 2.  Setting timer.
    The complete registration result is
    Python-device-01
    TestHub12345.azure-devices.net
    initialAssignment
    null
    Will send telemetry from the provisioned device
    sending message #4
    sending message #7
    sending message #2
    sending message #8
    sending message #5
    sending message #9
    sending message #1
    sending message #6
    sending message #10
    sending message #3
    done sending message #4
    done sending message #7
    done sending message #2
    done sending message #8
    done sending message #5
    done sending message #9
    done sending message #1
    done sending message #6
    done sending message #10
    done sending message #3
    ```

7. Portalda, sağlama hizmetinize bağlı olan IoT Hub 'ına gidin ve Sol menüdeki **araştırıcılar** bölümünde bulunan **IoT cihazları** dikey penceresini açın. X.509 sanal cihazının hub'a başarıyla sağlanması durumunda, cihaz kimliği **Device Explorer** dikey penceresinde *DURUM* değeri **etkinleştirildi** olarak gösterilir. Örnek cihaz uygulamasını çalıştırmadan önce dikey pencereyi zaten açtıysanız, en üstteki **Yenile** düğmesine basmanız gerekebilir. 

    ![Cihaz IOT hub'da kayıtlı](./media/python-quick-create-simulated-device-x509/registration.png) 

> [!NOTE]
> Cihazınız için *başlangıç cihaz ikizi durumu* ayarının kayıt girişindeki varsayılan değerini değiştirdiyseniz istenen ikili durumu hub'dan çekerek ona göre hareket edebilir. Daha fazla bilgi için bkz. [IoT Hub cihaz TWINS 'ı anlama ve kullanma](../iot-hub/iot-hub-devguide-device-twins.md).
>

## <a name="clean-up-resources"></a>Kaynakları temizleme

Cihaz istemci örneğini üzerinde çalışmaya ve keşfetmeye devam etmeyi planlıyorsanız, bu hızlı başlangıçta oluşturulan kaynakları temizlemeyin. Devam etmeyi planlamıyorsanız, bu hızlı başlangıç tarafından oluşturulan tüm kaynakları silmek için aşağıdaki adımları kullanın.

1. Makinenizde cihaz istemci örnek çıktı penceresini kapatın.
2. Azure portal sol taraftaki menüden **tüm kaynaklar** ' ı seçin ve ardından cihaz sağlama hizmetinizi seçin. Hizmetiniz için kayıtları **Yönet** dikey penceresini açın ve **bireysel** kayıtlar sekmesini seçin. Bu hızlı başlangıçta kaydettiğiniz cihazın *kayıt kimliği* ' nin yanındaki onay kutusunu işaretleyin ve bölmenin en üstündeki **Sil** düğmesine basın. 
3. Azure portal sol taraftaki menüden **tüm kaynaklar** ' ı seçin ve ardından IoT Hub 'ınızı seçin. Hub 'ınız için **IoT cihazları** dikey penceresini açın, bu hızlı başlangıçta kaydettiğiniz CIHAZıN *cihaz kimliği* ' nin yanındaki onay kutusunu işaretleyin ve ardından bölmenin en üstündeki **Sil** düğmesine basın.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, geliştirme makinenizde sanal bir X. 509.952 cihazı oluşturdunuz ve portaldaki Azure IoT Hub cihaz sağlama hizmetini kullanarak IoT Hub 'ınıza sağladınız. X. 509.952 cihazınızı programlı bir şekilde kaydetmeyi öğrenmek için, X. 509.440 cihazlarının programlı kaydı için hızlı başlangıç 'a geçin. 

> [!div class="nextstepaction"]
> [Azure hızlı başlangıç-X. 509.440 cihazlarını Azure IoT Hub cihaz sağlama hizmeti 'ne kaydetme](quick-enroll-device-x509-python.md)
