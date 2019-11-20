---
title: 'Öğretici: Azure IoT Hub cihaz sağlama hizmeti için cihazı ayarlama'
description: 'Öğretici: cihaz üretim işlemi sırasında cihaz sağlama hizmeti IoT Hub ile sağlama için cihazı ayarlama'
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: e7f6151968fb14d44f1e330fb6ddc06fabad3ee6
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112763"
---
# <a name="tutorial-set-up-a-device-to-provision-using-the-azure-iot-hub-device-provisioning-service"></a>Öğretici: Azure IoT Hub cihaz sağlama hizmeti 'ni kullanarak sağlama için bir cihaz ayarlama

Önceki öğreticide, IoT hub’ınıza cihazlarınızı otomatik olarak sağlamak için Azure IoT Hub Cihazı Sağlama Hizmeti’nin nasıl ayarlanacağını öğrendiniz. Bu öğretici, cihazınızın IOT Hub ile otomatik olarak sağlanmasına imkan vermek için üretim işlemi boyunca cihazınızın ayarlanacağını gösterir. Cihazınız, [Kanıtlama mekanizmasına](concepts-device.md#attestation-mechanism) dayalı olarak, ilk önyüklemenin ve sağlama hizmetiyle bağlantının ardından sağlanır. Bu öğretici aşağıdaki görevleri kapsar:

> [!div class="checklist"]
> * Platforma özgü Cihaz Sağlama Hizmetleri İstemci SDK’sını derleme
> * Güvenlik yapılarını ayıklama
> * Cihaz kayıt yazılımı oluşturma

Bu öğreticide önceki [Bulut kaynaklarını ayarlama](tutorial-set-up-cloud.md) öğreticisinde yer alan yönergeleri kullanarak Cihaz Sağlama Hizmeti örneğinizi ve bir IoT hub’ı oluşturduğunuz kabul edilmektedir.

Bu öğretici, C için Cihaz Sağlama Hizmeti İstemci SDK’sını içeren [C deposu için Azure IoT SDK’ları ve kitaplıkları](https://github.com/Azure/azure-iot-sdk-c)’nı kullanır. SDK şu anda Windows veya Ubuntu uygulamalarında çalıştırılan cihazlar için TPM ve X.509 desteği sağlar. Bu öğretici, Visual Studio ile temel yeterliliğinizi varsayan bir Windows geliştirme istemcisi kullanımını temel alır. 

Otomatik sağlama işlemini bilmiyorsanız, devam etmeden önce [Otomatik sağlama kavramlarını](concepts-auto-provisioning.md) gözden geçirdiğinizden emin olun. 


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 veya üzeri ' de [' masaüstü geliştirme C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) iş yükü etkin.
* [Git](https://git-scm.com/download/)'in en son sürümünün yüklemesi.



## <a name="build-a-platform-specific-version-of-the-sdk"></a>SDK’nın platforma özgü sürümünü derleme

Cihaz Sağlama Hizmeti İstemci SDK’sı, cihaz kaydı yazılımınızı uygulamanıza yardımcı olur. Ancak bunu kullanabilmeniz için önce geliştirme istemci platformunuza ve kanıtlama mekanizmanıza özgü bir SDK sürümü derlemeniz gerekir. Bu öğreticide, desteklenen bir kanıtlama türü için Windows geliştirme platformunda Visual Studio kullanan bir SDK oluşturursunuz:

1. [CMake derleme sistemini](https://cmake.org/download/)indirin.

    **yüklemesine başlamadan**önce`CMake` makinenizde Visual Studio önkoşullarının (Visual Studio ve "C++ ile masaüstü geliştirme" iş yükü) yüklenmiş olması önemlidir. Önkoşullar sağlandıktan ve indirme doğrulandıktan sonra, CMake derleme sistemini yükleyin.

1. Komut istemini veya Git Bash kabuğunu açın. Aşağıdaki komutu yürüterek [Azure IoT C SDK'sı](https://github.com/Azure/azure-iot-sdk-c) GitHub deposunu kopyalayın:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    Bu işlemin tamamlanması için birkaç dakika beklemeniz gerekebilir.


1. Git deposunun kök dizininde bir `cmake` alt dizini oluşturun ve o klasöre gidin. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. SDK'yı kullanacağınız kanıtlama mekanizmasıyla tercih edeceğiniz geliştirme platformuna göre derleyin. Aşağıdaki komutlardan birini kullanın (her komutun sonundaki yan yana iki nokta işaretine dikkat edin). Tamamlandığında CMake, cihazınıza özgü içerikle `/cmake` alt dizinini derler:
 
    - Kanıtlama için TPM simülatörünü kullanan cihazlar için:

        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
        ```

    - Başka bir cihaz için (fiziksel TPM/HSM/X.509 veya sanal X.509 sertifikası):

        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON ..
        ```


Şimdi cihaz kayıt kodunuzu derlemek için SDK’yı kullanmaya hazırsınız. 
 
<a id="extractsecurity"></a> 

## <a name="extract-the-security-artifacts"></a>Güvenlik yapılarını ayıklama 

Bir sonraki adım, cihazınız tarafından kullanılan kanıtlama mekanizması için güvenlik yapılarını ayıklamaktır. 

### <a name="physical-devices"></a>Fiziksel cihazlar 

SDK'yı kanıtlama için fiziksel TPM/HSM veya X.509 sertifikası kullanacak şekilde derlemenize bağlı olarak güvenlik yapıtlarını aşağıdaki şekilde alabilirsiniz:

- TPM cihazı için, TPM yongası üreticisinden bu TPM cihazıyla ilişkili **Onay Anahtarı**’nı belirlemeniz gerekir. Onay anahtarını karma hale getirerek TPM cihazınız için benzersiz bir **Kayıt Kimliği** türetebilirsiniz.  

- Bir X.509 cihazı için cihazlarınıza verilmiş sertifikaları almanız gerekir. Sağlama hizmeti, X.509 kanıtlama mekanizmasını kullanan cihazlara erişimi kontrol eden iki kayıt girişi türü sunar. Gerekli sertifikalar, kullanacağınız kayıt türüne göre değişiklik gösterir.

    1. Bireysel kayıtlar: Tek bir cihaz kaydı. Bu kayıt girişi türü için [end-entity, "leaf", certificates](concepts-security.md#end-entity-leaf-certificate) kullanılması gerekir.
    1. Kayıt grupları: Bu kayıt girişi türü için ara veya kök sertifika kullanılması gerekir. Daha fazla bilgi için bkz. [X.509 sertifikalarıyla sağlama hizmetine cihaz erişimini denetleme](concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

### <a name="simulated-devices"></a>Sanal cihazlar

SDK'yı kanıtlama için fiziksel TPM veya X.509 sertifikası kullanacak şekilde derlemenize bağlı olarak güvenlik yapıtlarını aşağıdaki şekilde alabilirsiniz:

- Sanal TPM cihazı için:

   1. Windows Komut İstemi açın, `azure-iot-sdk-c` dizinine gidin ve TPM simülatörünü çalıştırın. 2321 ve 2322 bağlantı noktalarında bulunan bir yuva üzerinden dinler. Bu komut penceresini kapatmayın; aşağıdaki Hızlı Başlangıcın sonuna kadar bu simülatörü çalışır durumda tutmanız gerekir. 

      `azure-iot-sdk-c` alt dizininden aşağıdaki komutu çalıştırarak simülatörü başlatın:

      ```cmd/sh
      .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
      ```

      > [!NOTE]
      > Bu adım için Git Bash komut istemini kullanıyorsanız ters eğik çizgileri eğik çizgi olarak değiştirmeniz gerekir: `./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe`.

   1. Visual Studio kullanarak, *adlı*cmake`azure_iot_sdks.sln` klasöründe oluşturulan çözümü açın ve "Derleme" menüsündeki "Çözümü derle" komutunu kullanarak bunu derleyin.

   1. Visual Studio'nın *Çözüm Gezgini* bölmesinde **Sağlama\_Araçlar** klasörüne gidin. **tpm_device_provision** projesine sağ tıklayın ve **Başlangıç Projesi Olarak Ayarla**’yı seçin. 

   1. "Hata Ayıklama" menüsündeki “Başlat” komutlarından birini kullanarak çözümü çalıştırın. Çıktı penceresi, cihaz kaydı için gereken TPM simülatörün **_Kayıt Kimliği_** ’ni ve **_Kayıt Anahtarı_** ’nı görüntüler. Daha sonra kullanmak için bu değerleri kopyalayın. Bu pencereyi (Kayıt Kimliği ve Onay Anahtarı ile) kapatabilirsiniz, ancak 1. adımda başlattığınız TPM simülatörü penceresini çalışır durumda bırakın.

- Sanal X.509 cihazı için:

  1. Visual Studio kullanarak, *adlı*cmake`azure_iot_sdks.sln` klasöründe oluşturulan çözümü açın ve "Derleme" menüsündeki "Çözümü derle" komutunu kullanarak bunu derleyin.

  1. Visual Studio'nın *Çözüm Gezgini* bölmesinde **Sağlama\_Araçlar** klasörüne gidin. **dice\_device\_enrollment** projesine sağ tıklayın ve **Başlangıç Projesi Olarak Ayarla**’yı seçin. 
  
  1. "Hata Ayıklama" menüsündeki “Başlat” komutlarından birini kullanarak çözümü çalıştırın. Çıktı penceresinde, istendiğinde tek kayıt için **i** girin. Çıktı penceresi, sanal cihazınız için yerel olarak oluşturulmuş X.509 sertifikasını görüntüler. *-----BEGIN CERTIFICATE-----* ile başlayıp ilk *-----END PUBLIC KEY-----* ile biten çıktıyı panoya kopyalayın ve bu iki satırı da dahil ettiğinizden emin olun. Yalnızca çıktı penceresindeki ilk sertifikayı kopyalamanız gerekir.
 
  1. **_X509testcert.pem_** adlı bir dosya oluşturun, dosyayı dilediğiniz metin düzenleyicide açın ve panonun içeriklerini bu dosyaya kopyalayın. Cihaz kaydı için daha sonra kullanacağınızdan, dosyayı kaydedin. Kayıt yazılımınız çalıştırıldığında, otomatik sağlamadaki aynı sertifikayı kullanır.    

Cihaz Sağlama Hizmeti’nde cihazınızın kaydı sırasında bu güvenlik yapıları gerekir. Sağlama hizmeti, cihazın önyüklenmesini ve daha sonra buna bağlanmasını bekler. Cihazınız ilk kez önyüklendiğinde, istemci SDK’sı mantığı, cihazdaki güvenlik yapılarını ayıklamak için yonganız (veya simülatör) ile etkileşime geçer ve Cihaz Sağlama hizmetinize kaydı doğrular. 

## <a name="create-the-device-registration-software"></a>Cihaz kayıt yazılımı oluşturma

Son adım, cihazı IoT Hub hizmetine kaydetmek için Cihaz Sağlama Hizmeti istemci SDK’sını kullanan bir kayıt uygulaması yazmaktır. 

> [!NOTE]
> Bu adım için, iş istasyonunuzdan SDK örnek kayıt uygulaması çalıştırılarak gerçekleştirilen bir sanal cihaz kullanıldığı varsayılacaktır. Ancak fiziksel cihaza dağıtım için bir kayıt uygulaması derliyorsanız aynı kavramlar geçerlidir. 

1. Azure portalında, Cihaz Sağlama hizmetiniz için **Genel Bakış** dikey penceresini seçin ve **_Kimlik Kapsamı_** değerini kopyalayın. *Kimlik Kapsamı* genellikle hizmet tarafından oluşturulur ve benzersizliği garanti eder. Bu sabittir ve kayıt kimliklerini benzersiz şekilde tanımlamak için kullanılır.

    ![Portal dikey penceresinden Cihaz Sağlama Hizmeti uç noktası bilgilerini ayıklama](./media/tutorial-set-up-device/extract-dps-endpoints.png) 

1. Makinenizdeki Visual Studio *Çözüm Gezgini*'nde **Sağlama\_Örnekler** klasörüne gidin. **prov\_dev\_client\_sample** adlı örnek projeyi seçip **prov\_dev\_client\_sample.c** kaynak dosyasını açın.

1. 1\. adımda elde edilen _Kimlik Kapsamı_ değerini `id_scope` değişkenine atayın (left/`[` ve right/`]` ayraçlarını kaldırarak): 

    ```c
    static const char* global_prov_uri = "global.azure-devices-provisioning.net";
    static const char* id_scope = "[ID Scope]";
    ```

    Başvuru için, `global_prov_uri` IoT Hub istemci kaydı API’sinin, belirlenen Cihaz Sağlama Hizmeti örneğine bağlanmasına olanak sağlayan `IoTHubClient_LL_CreateFromDeviceAuth` değişkeni.

1. Aynı dosyadaki **main()** işlevinde, cihazınızın kayıt yazılımı (TPM veya X.509) tarafından kullanılmakta olan kanıtlama mekanizmasıyla eşleşen `hsm_type` değişkenine açıklama ekleyin veya bu değişkenin açıklamasını kaldırın: 

    ```c
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

1. Değişikliklerinizi kaydedin ve "Derleme" menüsünden "Çözümü derle" seçeneğini belirleyerek **prov\_dev\_client\_sample** örneğini yeniden derleyin. 

1. **Sağlama\_Örnekler\_ klasörünün altında \_prov**dev**client\_sample** projesine sağ tıklayın ve **Başlangıç Projesi Olarak Ayarla**’yı seçin. Henüz örnek uygulamayı ÇALIŞTIRMAYIN.

> [!IMPORTANT]
> Henüz cihazı çalıştırmayın/başlatmayın! Cihazı başlatmadan önce ilk olarak Cihaz Sağlama Hizmeti’ne cihazı kaydederek işlemi tamamlamanız gerekir. Aşağıda yer alan Sonraki adımlar, sonraki makale için size yol gösterecektir.

### <a name="sdk-apis-used-during-registration-for-reference-only"></a>Kayıt sırasında kullanılan SDK API’leri (yalnızca başvuru)

Başvuru için SDK, kayıt sırasında kullanmak üzere uygulamanız için aşağıdaki API’leri sağlar. Bu API’ler, cihazınızın önyüklenirken Cihaz Sağlama Hizmetine bağlanmasına ve kaydolmasına yardımcı olur. Buna karşılık cihazınız, IoT Hub örneğinizle bağlantı kurmak için gerekli bilgileri alır:

```C
// Creates a Provisioning Client for communications with the Device Provisioning Client Service.  
PROV_DEVICE_LL_HANDLE Prov_Device_LL_Create(const char* uri, const char* scope_id, PROV_DEVICE_TRANSPORT_PROVIDER_FUNCTION protocol)

// Disposes of resources allocated by the provisioning Client.
void Prov_Device_LL_Destroy(PROV_DEVICE_LL_HANDLE handle)

// Asynchronous call initiates the registration of a device.
PROV_DEVICE_RESULT Prov_Device_LL_Register_Device(PROV_DEVICE_LL_HANDLE handle, PROV_DEVICE_CLIENT_REGISTER_DEVICE_CALLBACK register_callback, void* user_context, PROV_DEVICE_CLIENT_REGISTER_STATUS_CALLBACK reg_status_cb, void* status_user_ctext)

// Api to be called by user when work (registering device) can be done
void Prov_Device_LL_DoWork(PROV_DEVICE_LL_HANDLE handle)

// API sets a runtime option identified by parameter optionName to a value pointed to by value
PROV_DEVICE_RESULT Prov_Device_LL_SetOption(PROV_DEVICE_LL_HANDLE handle, const char* optionName, const void* value)
```

Ayrıca, ilk olarak bir sanal cihaz kullanarak Cihaz Sağlama Hizmeti istemci kaydı uygulamanızı iyileştirmeniz ve hizmet kurulumunu test etmeniz gerektiğini de fark edebilirsiniz. Uygulamanız test ortamında çalıştığında, cihazınız için uygulamayı derleyebilir ve yürütülebilir dosyayı cihazınızın görüntüsüne kopyalayabilirsiniz. 

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu noktada, Cihaz Sağlama ve IoT Hub hizmetleri portalda çalışıyor olabilir. Cihaz sağlama kurulumundan çıkmak ve/veya bu öğretici serisinin tamamlanmasını geciktirmek istiyorsanız, gereksiz yinelenen maliyetler oluşmasını önlemek için bunların kapatılmasını öneririz.

1. Azure portalında sol taraftaki menüden **Tüm kaynaklar**’ı ve ardından Cihaz Sağlama hizmetini seçin. **Tüm kaynaklar** dikey pencerenin en üstündeki **Sil** seçeneğine tıklayın.  
1. Azure portalında sol taraftaki menüden **Tüm kaynaklar**’ı ve ardından IoT hub’ınızı seçin. **Tüm kaynaklar** dikey pencerenin en üstündeki **Sil** seçeneğine tıklayın.  

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Platforma özgü Cihaz Sağlama Hizmeti İstemci SDK’sını derleme
> * Güvenlik yapılarını ayıklama
> * Cihaz kayıt yazılımı oluşturma

Otomatik sağlama için cihazı Azure IoT Hub Cihazı Sağlama Hizmeti’ne kaydederek IoT hub’ına nasıl sağlanacağı hakkında bilgi edinmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [IoT hub’ınıza cihazı sağlama](tutorial-provision-device-to-hub.md)

