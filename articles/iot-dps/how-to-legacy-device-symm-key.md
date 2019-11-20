---
title: Azure IoT Hub cihaz sağlama hizmeti ile eski cihazları sağlamak için simetrik anahtarlar kullanma | Microsoft Docs
description: Cihaz sağlama hizmeti örneğiniz ile eski cihazları sağlamak için simetrik anahtarlar kullanma
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: 13e22d772ef9b90f415f10b65e4a4290a1f7bd81
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72434825"
---
# <a name="how-to-provision-legacy-devices-using-symmetric-keys"></a>Simetrik anahtarlar kullanarak eski cihazları sağlama

Birçok eski cihazda yaygın bir sorun, genellikle tek bir bilgi parçasına sahip olan bir kimliğe sahip olmalarıdır. Bu kimlik bilgileri genellikle bir MAC adresi veya seri numarasıdır. Eski cihazlarda, cihazı güvenli bir şekilde tanımlamak için kullanılabilecek bir sertifika, TPM veya başka bir güvenlik özelliği bulunmayabilir. IoT Hub için cihaz sağlama hizmeti simetrik anahtar kanıtlama içerir. Simetrik anahtar kanıtlama, MAC adresi veya seri numarası gibi bilgileri temel alarak bir cihazı belirlemek için kullanılabilir.

Bir [donanım güvenlik modülünü (HSM)](concepts-security.md#hardware-security-module) ve bir sertifikayı kolayca yükleyebiliyorsanız, cihazlarınızı tanımlamaya ve sağlamaya yönelik daha iyi bir yaklaşım olabilir. Bu yaklaşım, tüm cihazlarınıza dağıtılan kodu güncelleştirme işlemini atlamanıza izin verebilir ve cihaz yansımanıza gömülü bir gizli anahtar yoktur.

Bu makalede, ne bir HSM veya bir sertifikanın uygun bir seçenek olduğu varsayılır. Ancak, bu cihazları sağlamak için cihaz kodu güncelleştirme bir yöntem, cihaz sağlama hizmetini kullanmak üzere bir yöntem olduğunu varsayın. 

Bu makalede ayrıca, ana grup anahtarına veya türetilmiş cihaz anahtarına yetkisiz erişimi engellemek için cihaz güncelleştirmesinin güvenli bir ortamda gerçekleştiği varsayılmaktadır.

Bu makale Windows tabanlı bir iş istasyonuna yöneliktir. Ancak yordamları Linux üzerinde gerçekleştirebilirsiniz. Bir Linux örneği için, bkz. [Çoklu kiracı için sağlama](how-to-provision-multitenant.md).

> [!NOTE]
> Bu makalede kullanılan örnek C dilinde yazılmıştır. Ayrıca bir [ C# cihaz sağlama simetrik anahtar örneği](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/SymmetricKeySample) de mevcuttur. Bu örneği kullanmak için, [Azure-IoT-Samples-CSharp](https://github.com/Azure-Samples/azure-iot-samples-csharp) deposunu indirin veya kopyalayın ve örnek kodda satır içi yönergeleri izleyin. Bu makaledeki yönergeleri izleyerek, portalı kullanarak bir simetrik anahtar kayıt grubu oluşturabilir ve örneği çalıştırmak için gereken KIMLIK kapsamını ve kayıt grubu birincil ve ikincil anahtarlarını bulabilirsiniz. Ayrıca, örneği kullanarak ayrı kayıtlar da oluşturabilirsiniz.

## <a name="overview"></a>Genel Bakış

Her bir cihaz için, cihazı tanımlayan bilgileri temel alan benzersiz bir kayıt KIMLIĞI tanımlanır. Örneğin, MAC adresi veya seri numarası.

[Simetrik anahtar kanıtlama](concepts-symmetric-key-attestation.md) kullanan bir kayıt grubu, cihaz sağlama hizmeti ile oluşturulur. Kayıt grubu, bir grup ana anahtarı içerir. Bu ana anahtar, her bir cihaz için benzersiz bir cihaz anahtarı oluşturmak üzere her benzersiz kayıt KIMLIĞINI karma hale almak için kullanılacaktır. Cihaz, cihaz sağlama hizmeti ile test etmek için bu türetilmiş Cihaz anahtarını benzersiz kayıt KIMLIĞIYLE kullanır ve bir IoT Hub 'ına atanır.

Bu makalede gösterilen cihaz kodu, [hızlı başlangıç: simetrik anahtarlarla bir sanal cihaz sağlama ile](quick-create-simulated-device-symm-key.md)aynı kalıbı izler. Kod, [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)' dan bir örnek kullanarak bir cihazın benzetimini yapar. Sanal cihaz, hızlı başlangıçta gösterildiği gibi tek bir kayıt yerine kayıt grubuyla test eder.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Önkoşullar

* [IoT Hub cihazı sağlama hizmetini Azure Portal](./quick-setup-auto-provision.md) hızlı başlangıç ile tamamlama.
* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 veya üzeri ' de [' masaüstü geliştirme C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) iş yükü etkin.
* [Git](https://git-scm.com/download/)'in en son sürümünün yüklemesi.


## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Azure IoT C SDK'sı için geliştirme ortamını hazırlama

Bu bölümde, [Azure IoT C SDK'sını](https://github.com/Azure/azure-iot-sdk-c) oluşturmak için kullanılan geliştirme ortamını hazırlayacaksınız. 

SDK, sanal cihaz için örnek kodu içerir. Simülasyon cihazı, cihazın önyükleme dizisi sırasında sağlamayı dener.

1. [CMake derleme sistemini](https://cmake.org/download/)indirin.

    `CMake` yüklemesine başlamadan **önce** makinenizde Visual Studio önkoşullarının (Visual Studio ve "C++ ile masaüstü geliştirme" iş yükü) yüklenmiş olması önemlidir. Önkoşullar sağlandıktan ve indirme doğrulandıktan sonra, CMake derleme sistemini yükleyin.

2. Komut istemini veya Git Bash kabuğunu açın. Aşağıdaki komutu yürüterek Azure IoT C SDK'sı GitHub deposunu kopyalayın:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    Bu işlemin tamamlanması için birkaç dakika beklemeniz gerekebilir.


3. Git deposunun kök dizininde bir `cmake` alt dizini oluşturun ve o klasöre gidin. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. SDK'nın geliştirme istemci platformunuza ve özgü bir sürümünü derlemek için aşağıdaki komutu çalıştırın. `cmake` dizininde simülasyon cihazı için bir Visual Studio çözümü de oluşturulur. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```
    
    `cmake`, C++ derleyicinizi bulamazsa yukarıdaki komutu çalıştırırken derleme hatalarıyla karşılaşabilirsiniz. Bu durumda bu komutu [Visual Studio komut isteminde](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs) çalıştırmayı deneyin. 

    Derleme başarılı olduktan sonra, son birkaç çıkış satırı aşağıdaki çıkışa benzer olacaktır:

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```


## <a name="create-a-symmetric-key-enrollment-group"></a>Simetrik anahtar kayıt grubu oluşturma

1. [Azure Portal](https://portal.azure.com)oturum açın ve cihaz sağlama hizmeti örneğinizi açın.

2. Kayıtları **Yönet** sekmesini seçin ve ardından sayfanın en üstündeki **kayıt grubu Ekle** düğmesine tıklayın. 

3. **Kayıt grubu Ekle**sayfasında, aşağıdaki bilgileri girin ve **Kaydet** düğmesine tıklayın.

   - **Grup adı**: **mylegçevrimcihazları**girin.

   - **Kanıtlama türü**: **simetrik anahtar**seçin.

   - **Anahtarları Otomatik Olarak Oluştur**: Bu kutuyu işaretleyin.

   - **Cihazlara cihazları nasıl atamak Istediğinizi seçin**: belirli bir hub 'a atayabilmeniz için **statik yapılandırma** ' yı seçin.

   - **Bu grubun atanabileceği IoT Hub 'Larını seçin**: hub 'larınızın birini seçin.

     ![Simetrik anahtar kanıtlama için kayıt grubu ekleme](./media/how-to-legacy-device-symm-key/symm-key-enrollment-group.png)

4. Ortamınızı kaydettikten sonra, **Birincil Anahtar** ve **İkincil Anahtar** oluşturularak kayıt girişine eklenir. Simetrik anahtar kayıt grubunuz, *kayıt grupları* sekmesindeki *Grup adı* sütununun altında **mylegçevrimcihazları** olarak görünür. 

    Kaydı açın ve oluşturduğunuz **Birincil Anahtar** değerini kopyalayın. Bu anahtar, ana grup anahtarınıza ait.


## <a name="choose-a-unique-registration-id-for-the-device"></a>Cihaz için benzersiz bir kayıt KIMLIĞI seçin

Her bir cihazı tanımlamak için benzersiz bir kayıt KIMLIĞI tanımlanmalıdır. MAC adresi, seri numarası veya cihazdan herhangi bir benzersiz bilgi kullanabilirsiniz. 

Bu örnekte, bir kayıt KIMLIĞI için aşağıdaki dizeyi oluşturan bir MAC adresi ve seri numarası birleşimini kullanırız.

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Cihazınız için benzersiz bir kayıt KIMLIĞI oluşturun. Geçerli karakterler küçük harfli alfasayısal ve tire ('-').


## <a name="derive-a-device-key"></a>Bir cihaz anahtarı türet 

Cihaz anahtarı oluşturmak için, cihaz için benzersiz kayıt KIMLIĞI için [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) hesaplamak ve sonucu base64 biçimine dönüştürmek için Grup ana anahtarını kullanın.

Grup ana anahtarınızı cihaz kodunuza eklemeyin.


#### <a name="linux-workstations"></a>Linux iş istasyonları

Bir Linux iş istasyonu kullanıyorsanız, aşağıdaki örnekte gösterildiği gibi, türetilen cihaz anahtarınızı oluşturmak için OpenSSL kullanabilirsiniz.

**Anahtarın** değerini, daha önce not ettiğiniz **birincil anahtarla** değiştirin.

**REG_ID** DEĞERINI kayıt Kimliğinizle değiştirin.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```


#### <a name="windows-based-workstations"></a>Windows tabanlı iş istasyonları

Windows tabanlı bir iş istasyonu kullanıyorsanız, aşağıdaki örnekte gösterildiği gibi, türetilmiş cihaz anahtarınızı oluşturmak için PowerShell kullanabilirsiniz.

**Anahtarın** değerini, daha önce not ettiğiniz **birincil anahtarla** değiştirin.

**REG_ID** DEĞERINI kayıt Kimliğinizle değiştirin.

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```


Cihazınız, sağlama sırasında kayıt grubuyla simetrik anahtar kanıtlama gerçekleştirmek için, benzersiz kayıt KIMLIĞINIZLE türetilmiş Cihaz anahtarını kullanır.



## <a name="create-a-device-image-to-provision"></a>Sağlamak için bir cihaz görüntüsü oluşturma

Bu bölümde, daha önce ayarladığınız Azure IoT C SDK 'sında bulunan **prov @ no__t-1dev @ no__t-2client @ no__t-3sample** adlı bir sağlama örneğini güncelleşirsiniz. 

Bu örnek kod, cihaz sağlama hizmeti örneğinize sağlama isteği gönderen bir cihaz önyükleme sırasının benzetimini yapar. Önyükleme sırası, cihazın tanınmasına ve kayıt grubunda yapılandırdığınız IoT Hub 'ına atanmasına neden olur.

1. Azure Portal'da Cihaz Sağlama hizmetiniz için **Genel Bakış** sekmesini seçin ve **_Kimlik Kapsamı_** değerini not alın.

    ![Portal dikey penceresinden Cihaz Sağlama Hizmeti uç noktası bilgilerini ayıklama](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. Visual Studio 'da, daha önce CMake çalıştırılarak oluşturulan **azure_iot_sdks. sln** çözüm dosyasını açın. Çözüm dosyası şu konumda olmalıdır:

    ```
    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. Visual Studio'nun *Çözüm Gezgini* penceresinde **Sağlama\_Örnekleri** klasörüne gidin. **prov\_dev\_client\_sample** adlı örnek projeyi genişletin. **Kaynak Dosyalar**'ı genişletin ve **prov\_dev\_client\_sample.c** dosyasını açın.

4. `id_scope` sabitini bulun ve değeri daha önce kopyalamış olduğunuz **Kimlik Kapsamı** değerinizle değiştirin. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Aynı dosyada `main()` işlevinin tanımını bulun. `hsm_type` değişkeninin aşağıda gösterildiği gibi `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` değerine ayarlandığından emin olun:

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. **Prov @ no__t-2Dev @ no__t-3client @ no__t-4sample. c** ' de, açıklama eklenen `prov_dev_set_symmetric_key_info()` ' a yönelik çağrıyı bulun.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    İşlev çağrısının açıklamasını kaldırın ve yer tutucu değerlerini (açılı ayraçlar dahil), cihazınızın benzersiz kayıt KIMLIĞI ve oluşturduğunuz türetilmiş cihaz anahtarı ile değiştirin.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6", "Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=");
    ```
   
    Dosyayı kaydedin.

7. **prov\_dev\_client\_sample** projesine sağ tıklayın ve **Başlangıç Projesi Olarak Ayarla**’yı seçin. 

8. Çözümü çalıştırmak için Visual Studio menüsünde **Hata Ayıkla** > **Hata ayıklama olmadan başlat**'ı seçin. Projeyi yeniden derleme isteminde **Evet**'e tıklayarak, çalıştırmadan önce projeyi yeniden derleyin.

    Aşağıdaki çıkış, bir simülasyon cihazının başarıyla önyüklemesini yapma ve bir IoT hub’ına atanmak üzere sağlama Hizmeti örneğine bağlanma işlemlerinin bir örneğidir:

    ```cmd
    Provisioning API Version: 1.2.8

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: 
    test-docs-hub.azure-devices.net, deviceId: sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

    Press enter key to exit:
    ```

9. Portalda, sanal cihazınızın atandığı IoT Hub 'ına gidin ve **IoT cihazları** sekmesine tıklayın. Hub 'ın simülasyonu başarıyla sağlanmasından sonra cihaz KIMLIĞI **IoT cihazları** dikey penceresinde, *durumu* **etkin**olarak görünür. En üstteki **Yenile** düğmesine tıklamanız gerekebilir. 

    ![Cihaz IOT hub'da kayıtlı](./media/how-to-legacy-device-symm-key/hub-registration.png) 



## <a name="security-concerns"></a>Güvenlik sorunları

Bunun, önerilen bir en iyi güvenlik uygulaması olmayan, görüntünün bir parçası olarak bulunan türetilmiş Cihaz anahtarını bırakdığının farkında olun. Bu, güvenliğin ve kullanım kolaylığının neden olmasının bir nedenidir. 





## <a name="next-steps"></a>Sonraki adımlar

* Daha fazla yeniden sağlama hakkında daha fazla bilgi için bkz. [cihaz yeniden sağlama kavramlarını IoT Hub](concepts-device-reprovision.md) 
* [Hızlı başlangıç: simetrik anahtarlarla sanal cihaz sağlama](quick-create-simulated-device-symm-key.md)
* Daha fazla sağlama sağlamayı öğrenmek için bkz. [daha önce otomatik olarak sağlanan cihazların sağlamasını kaldırma](how-to-unprovision-devices.md) 











