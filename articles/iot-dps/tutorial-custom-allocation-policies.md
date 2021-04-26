---
title: Azure IoT Hub cihaz sağlama hizmeti (DPS) ile özel ayırma ilkeleri kullanma öğreticisi
description: Azure IoT Hub cihaz sağlama hizmeti (DPS) ile özel ayırma ilkeleri kullanma öğreticisi
author: wesmc7777
ms.author: wesmc
ms.date: 09/23/2020
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: f19f43b89cd2527a67827d7434f2e054ee40001e
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227390"
---
# <a name="tutorial-use-custom-allocation-policies-with-device-provisioning-service-dps"></a>Öğretici: cihaz sağlama hizmeti (DPS) ile özel ayırma ilkeleri kullanma

Özel bir ayırma ilkesi, cihazların bir IoT Hub 'ına nasıl atanabileceği konusunda daha fazla denetim sağlar. Bu, bir IoT Hub 'ına cihaz atamak için sağlama sırasında çalışan bir [Azure işlevinde](../azure-functions/functions-overview.md) özel kod kullanılarak gerçekleştirilir. Cihaz sağlama hizmeti, cihaz ve kayıt hakkında tüm ilgili bilgileri sağlayan Azure Işlev kodunuzu çağırır. İşlev kodunuz yürütülür ve cihazı sağlamak için kullanılan IoT Hub bilgilerini döndürür.

Özel ayırma ilkelerini kullanarak, cihaz sağlama hizmeti tarafından sunulan ilkeler senaryonuzun gereksinimlerini karşılamadığında kendi ayırma ilkelerinizi tanımlarsınız.

Örneğin, bir cihazın sağlama sırasında kullandığı sertifikayı incelemek ve cihazı bir sertifika özelliğine dayalı bir IoT Hub 'ına atamak isteyebilirsiniz. Ya da cihazlarınıza yönelik bir veritabanında depolanan bilgilere sahip olabilirsiniz ve bir cihazın hangi IoT Hub 'ına atanması gerektiğini öğrenmek için veritabanını sorgulamak gerekir.

Bu makalede, simetrik anahtarlar kullanılarak Toaster cihazları sağlamak Için C# dilinde yazılmış bir Azure Işlevi kullanan özel bir ayırma ilkesiyle bir kayıt grubu gösterilir. Bir Toaster cihazı olarak tanınmayan bir cihaz, bir IoT Hub 'ına sağlanmaz.

Cihazlar, [Azure IoT C SDK 'sına](https://github.com/Azure/azure-iot-sdk-c)dahil olan sağlama örnek kodu kullanılarak sağlama isteğinde bulunur.


Bu öğreticide şunları yapmanız gerekir:

> [!div class="checklist"]
> * Özel bir ayırma işlevini desteklemek için yeni bir Azure İşlev Uygulaması oluşturma
> * Özel ayırma ilkesi için bir Azure Işlevi kullanarak yeni bir grup kaydı oluşturma
> * İki cihaz için cihaz anahtarları oluşturma
> * [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 'sindeki cihaz kodu için geliştirme ortamını ayarlama
> * Cihazları çalıştırın ve özel ayırma ilkesine göre sağlandığını doğrulayın


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

* Bu makalede IoT Hub ve DPS örneğinizi oluşturmak için [Azure Portal cihaz sağlama hizmeti IoT Hub ayarlama](./quick-setup-auto-provision.md) adımlarını tamamladığınız varsayılmaktadır.

* [Git](https://git-scm.com/download/)'in en son sürümünün yüklemesi.

* Windows geliştirme ortamı için, [' C++ Ile masaüstü geliştirme '](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) iş yüküne sahip [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 gereklidir. Visual Studio 2015 ve Visual Studio 2017 de desteklenir.

* Linux veya macOS için, [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) belgelerinde [geliştirme ortamınızı hazırlama](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) konusunun ilgili bölümüne bakın.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-custom-allocation-function"></a>Özel ayırma işlevini oluşturma

Bu bölümde, özel ayırma ilkenizi uygulayan bir Azure işlevi oluşturacaksınız. Bu işlev, kayıt KIMLIĞININ **contoso-Toaster** dize önekini içerip içermediğini temel alarak IoT Hub bir cihazın kaydedilip kaydedilmeyeceğine karar verir.

1. [Azure portalında](https://portal.azure.com) oturum açın. Giriş sayfanızda **+ kaynak oluştur**' u seçin.

2. Market aramasını *Ara* kutusuna "işlev uygulaması" yazın. Aşağı açılan listeden **işlev uygulaması**' yi seçin ve ardından **Oluştur**' u seçin.

3. **İşlev uygulaması** Oluştur sayfasında, **temel bilgiler** sekmesinde, yeni işlev uygulamanız için aşağıdaki ayarları girin ve **gözden geçir + oluştur**' u seçin:

    **Abonelik**: birden fazla aboneliğiniz varsa ve istenen abonelik seçili değilse, kullanmak istediğiniz aboneliği seçin.

    **Kaynak grubu**: Bu alan, yeni bir kaynak grubu oluşturmanıza veya işlev uygulamasını içerecek mevcut bir tane seçmenize olanak sağlar. Daha önce test için oluşturduğunuz IoT Hub 'ını içeren aynı kaynak grubunu (örneğin, **Testresources**) seçin. İlgili tüm kaynakları aynı gruba birlikte koyarak, bunları birlikte yönetebilirsiniz.

    **İşlev uygulaması adı**: benzersiz bir işlev uygulama adı girin. Bu örnek **contoso-Function-App**' i kullanır.

    **Yayımla**: **kodun** seçildiğini doğrulayın.

    **Çalışma zamanı yığını**: açılan listeden **.NET Core** ' u seçin.

    **Bölge**: kaynak grubağınız ile aynı bölgeyi seçin. Bu örnek **Batı ABD** kullanır.

    > [!NOTE]
    > Varsayılan olarak, Application Insights etkindir. Bu makale için Application Insights gerekli değildir, ancak özel ayırma ile karşılaştığınız sorunları anlamanıza ve araştırmanıza yardımcı olabilir. İsterseniz, **izleme** sekmesini seçip **Etkinleştir Application Insights** için **Hayır** ' ı seçerek Application Insights devre dışı bırakabilirsiniz.

    ![Özel ayırma işlevini barındırmak için Azure İşlev Uygulaması oluşturma](./media/tutorial-custom-allocation-policies/create-function-app.png)

4. İşlev uygulamasını oluşturmak için **Özet** sayfasında **Oluştur** ' u seçin. Dağıtım birkaç dakika sürebilir. Tamamlandığında **Kaynağa Git**' i seçin.

5. Sol bölmede, **işlevler** ' i tıklatın ve ardından yeni bir işlev eklemek Için **+ Ekle** **' ye tıklayın** .

6. Şablonlar sayfasında, **http tetikleyicisi** kutucuğunu seçin, sonra **işlev oluştur**' u seçin. **HttpTrigger1** adlı bir işlev oluşturulur ve Portal, işleviniz için genel bakış sayfasını görüntüler.

7. Yeni işleviniz için **Code + test** ' e tıklayın. Portal, **Run. CSX** kod dosyasının içeriğini görüntüler. 

8. **HttpTrigger1** işlevi için kodu aşağıdaki kodla değiştirin ve **Kaydet**' i seçin. Özel ayırma kodunuz kullanılabilir.

    ```csharp
    #r "Newtonsoft.Json"

    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);

        log.LogInformation("Request.Body:...");
        log.LogInformation(requestBody);

        // Get registration ID of the device
        string regId = data?.deviceRuntimeContext?.registrationId;

        string message = "Uncaught error";
        bool fail = false;
        ResponseObj obj = new ResponseObj();

        if (regId == null)
        {
            message = "Registration ID not provided for the device.";
            log.LogInformation("Registration ID : NULL");
            fail = true;
        }
        else
        {
            string[] hubs = data?.linkedHubs.ToObject<string[]>();

            // Must have hubs selected on the enrollment
            if (hubs == null)
            {
                message = "No hub group defined for the enrollment.";
                log.LogInformation("linkedHubs : NULL");
                fail = true;
            }
            else
            {
                // This is a Contoso Toaster 
                if (regId.Contains("contoso-toaster"))
                {
                    //Log IoT hubs configured for the enrollment
                    foreach(string hubString in hubs)
                    {
                        log.LogInformation("linkedHub : " + hubString);
                    }

                    obj.iotHubHostName = hubs[0];
                    log.LogInformation("Selected hub : " + obj.iotHubHostName);
                }
                else
                {
                    fail = true;
                    message = "Unrecognized device registration.";
                    log.LogInformation("Unknown device registration");
                }
            }
        }

        log.LogInformation("\nResponse");
        log.LogInformation((obj.iotHubHostName != null) ? JsonConvert.SerializeObject(obj) : message);

        return (fail)
            ? new BadRequestObjectResult(message) 
            : (ActionResult)new OkObjectResult(obj);
    }

    public class ResponseObj
    {
        public string iotHubHostName {get; set;}
    }
    ```

9. **Run. CSX** kod dosyasının hemen altında, özel ayırma işlevinden günlüğe kaydetmeyi Izlemek için **Günlükler** ' e tıklayın. 


## <a name="create-the-enrollment"></a>Kayıt oluşturma

Bu bölümde, özel ayırma ilkesini kullanan yeni bir kayıt grubu oluşturacaksınız. Kolaylık olması için, bu makale kayıt ile [simetrik anahtar kanıtlama](concepts-symmetric-key-attestation.md) kullanır. Daha güvenli bir çözüm için, bir güven zinciri ile [X. 509.440 sertifika kanıtlama](concepts-x509-attestation.md) kullanmayı göz önünde bulundurun.

1. Hala [Azure Portal](https://portal.azure.com)sağlama hizmetinizi açın.

2. Sol bölmedeki kayıtları **Yönet** ' i seçin ve ardından sayfanın en üstündeki **kayıt grubu Ekle** düğmesini seçin.

3. **Kayıt grubu Ekle**' de, aşağıdaki tabloya bilgileri girin ve **Kaydet** düğmesine tıklayın.

    | Alan | Açıklama ve/veya önerilen değer |
    | :---- | :----------------------------- |
    | **Grup adı** | **Contoso-özel-ayrılan cihazları** girin |
    | **Kanıtlama türü** | **Simetrik anahtar** seçin |
    | **Anahtarları otomatik oluştur** | Bu onay kutusu zaten denetlenmelidir. |
    | **Cihazları hub 'lara nasıl atamak istediğinizi seçin** | Özel ' i seçin **(Azure Işlevini kullanın)** |
    | **Bu grubun atanaabileceği IoT Hub 'larını seçin** | Hızlı başlangıcı tamamladığınızda daha önce oluşturduğunuz IoT Hub 'ını seçin. |
    | **Azure Işlevi seçin** | Oluşturduğunuz işlev uygulamasını içeren aboneliği seçin. Ardından, işlev için **contoso-Function-App** ve **HttpTrigger1** seçin. |

    ![Simetrik anahtar kanıtlama için özel ayırma kayıt grubu ekleme](./media/tutorial-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. Kayıt kaydedildikten sonra yeniden açın ve **birincil anahtarı** bir yere getirin. Anahtarların oluşturulması için önce kaydı kaydetmelisiniz. Bu birincil simetrik anahtar, daha sonra sağlamayı deneyen cihazlar için benzersiz cihaz anahtarları oluşturmak üzere kullanılacaktır. 

## <a name="derive-unique-device-keys"></a>Benzersiz cihaz anahtarları türet

Cihazlar, birincil simetrik anahtarı doğrudan kullanmaz. Bunun yerine, her bir cihaz için bir cihaz anahtarı türetmede birincil anahtarı kullanın. Bu bölümde, iki benzersiz cihaz anahtarı oluşturacaksınız. Bir anahtar, sanal bir Toaster cihazı için kullanılacaktır. Diğer anahtar, sanal bir ısı pompa cihazı için kullanılacaktır. Oluşturulan anahtarlar her iki cihazın da kayıt denemesini sağlar. Özel ayırma ilkesi örnek kodu tarafından kabul edilmesi için yalnızca bir cihaz kayıt KIMLIĞI geçerli bir sonekine sahip olacaktır. Sonuç olarak, biri kabul edilir ve diğeri reddedildi

Cihaz anahtarını türetmek için, daha önce not ettiğiniz simetrik anahtarı kullanarak her bir cihaz için cihaz kayıt KIMLIĞI için [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) ' ı hesaplamanız ve sonucu base64 biçimine dönüştürmeniz gerekir. Kayıt gruplarıyla türetilmiş cihaz anahtarları oluşturma hakkında daha fazla bilgi için, [simetrik anahtar kanıtlama](concepts-symmetric-key-attestation.md)'nın grup kayıtları bölümüne bakın.

Bu makaledeki örnek için, her iki cihaz için bir cihaz anahtarı hesaplamak üzere aşağıdaki kodla aşağıdaki iki cihaz kayıt kimliğini kullanın:

* **Contoso-Toaster-007**
* **Contoso-heatpompa-088**

**Anahtar** değişkeninin değerini, kayıt grubunuz oluşturulduktan sonra, daha önce not ettiğiniz **birincil anahtarla** değiştirin. Aşağıdaki kodla gösterilen anahtar değer ve çıkış yalnızca bir örnektir.

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

Windows tabanlı bir iş istasyonu kullanıyorsanız, aşağıdaki örnekte gösterildiği gibi, türetilmiş cihaz anahtarınızı oluşturmak için PowerShell kullanabilirsiniz.

```powershell
$KEY='oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA=='

$REG_ID1='contoso-toaster-007'
$REG_ID2='contoso-heatpump-088'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($key)
$sig1 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID1))
$sig2 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID2))
$derivedkey1 = [Convert]::ToBase64String($sig1)
$derivedkey2 = [Convert]::ToBase64String($sig2)

echo "`n`n$REG_ID1 : $derivedkey1`n$REG_ID2 : $derivedkey2`n`n"
```

```powershell
contoso-toaster-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
contoso-heatpump-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
```

#### <a name="bash"></a>[Bash](#tab/bash)

Bir Linux iş istasyonu kullanıyorsanız, aşağıdaki Bash örneğinde gösterildiği gibi, türetilmiş cihaz anahtarlarınızı oluşturmak için OpenSSL kullanabilirsiniz.

```bash
KEY=oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA==

REG_ID1=contoso-toaster-007
REG_ID2=contoso-heatpump-088

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
devkey1=$(echo -n $REG_ID1 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)
devkey2=$(echo -n $REG_ID2 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)

echo -e $"\n\n$REG_ID1 : $devkey1\n$REG_ID2 : $devkey2\n\n"
```

```bash
contoso-toaster-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
contoso-heatpump-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
```

---


## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Azure IoT C SDK'sı için geliştirme ortamını hazırlama

Cihazlar, [Azure IoT C SDK 'sına](https://github.com/Azure/azure-iot-sdk-c)dahil olan sağlama örnek kodu kullanılarak sağlama isteğinde bulunur.

Bu bölümde, [Azure IoT C SDK 'sını](https://github.com/Azure/azure-iot-sdk-c)oluşturmak için kullanılan geliştirme ortamını hazırlarsınız. SDK, sanal cihaz için örnek kodu içerir. Simülasyon cihazı, cihazın önyükleme dizisi sırasında sağlamayı dener.

Bu bölüm, Windows tabanlı bir iş istasyonuna yönelir. Bir Linux örneği için bkz. [çok kiracılı için sağlama](how-to-provision-multitenant.md)bölümünde VM 'lerin kurulumu.

1. [CMake derleme sistemini](https://cmake.org/download/)indirin.

    `CMake` yüklemesine başlamadan **önce** makinenizde Visual Studio önkoşullarının (Visual Studio ve "C++ ile masaüstü geliştirme" iş yükü) yüklenmiş olması önemlidir. Önkoşullar sağlandıktan ve indirme doğrulandıktan sonra, CMake derleme sistemini yükleyin.

2. SDK 'nın [en son sürümü](https://github.com/Azure/azure-iot-sdk-c/releases/latest) için etiket adını bulun.

3. Komut istemini veya Git Bash kabuğunu açın. [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub deposunun en son sürümünü kopyalamak için aşağıdaki komutları çalıştırın. Önceki adımda bulunan etiketini parametre değeri olarak kullanın `-b` :

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Bu işlemin tamamlanması için birkaç dakika beklemeniz gerekebilir.

4. Git deposunun kök dizininde bir `cmake` alt dizini oluşturun ve o klasöre gidin. Dizininden aşağıdaki komutları çalıştırın `azure-iot-sdk-c` :

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. SDK’nın geliştirme istemci platformunuza ve özgü bir sürümünü oluşturmak için aşağıdaki komutu çalıştırın. `cmake` dizininde simülasyon cihazı için bir Visual Studio çözümü de oluşturulur. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    `cmake`C++ derleyicisini bulamazsa, komutunu çalıştırırken derleme hataları alabilirsiniz. Bu durumda, [Visual Studio komut isteminde](/dotnet/framework/tools/developer-command-prompt-for-vs)komutunu çalıştırmayı deneyin.

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

## <a name="simulate-the-devices"></a>Cihazların benzetimini yapın

Bu bölümde, daha önce ayarladığınız Azure IoT C SDK 'sında bulunan **prov \_ dev \_ Client \_ Sample** adlı bir sağlama örneğini güncelleştirmelisiniz.

Bu örnek kod, cihaz sağlama hizmeti örneğinize sağlama isteği gönderen bir cihaz önyükleme sırasının benzetimini yapar. Önyükleme sırası, Toaster cihazının özel ayırma ilkesi kullanılarak IoT Hub 'ına tanınmasına ve atanmasına neden olur.

1. Azure Portal'da Cihaz Sağlama hizmetiniz için **Genel Bakış** sekmesini seçin ve **_Kimlik Kapsamı_** değerini not alın.

    ![Portal dikey penceresinden Cihaz Sağlama Hizmeti uç noktası bilgilerini ayıklama](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. Visual Studio 'da, daha önce CMake çalıştırılarak oluşturulan **azure_iot_sdks. sln** çözüm dosyasını açın. Çözüm dosyası şu konumda olmalıdır:

    ```
    azure-iot-sdk-c\cmake\azure_iot_sdks.sln
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

6. `main()`İşlevinde, çağrısını bulun `Prov_Device_Register_Device()` . Bu çağrıdan hemen önce, [`Prov_Device_Set_Provisioning_Payload()`](/azure/iot-hub/iot-c-sdk-ref/prov-device-client-h/prov-device-set-provisioning-payload) sağlama sırasında Özel BIR JSON yükünü geçirmek için kullanan aşağıdaki kod satırlarını ekleyin. Bu, özel ayırma işlevleriniz hakkında daha fazla bilgi sağlamak için kullanılabilir. Bu, kayıt KIMLIĞINI incelemek yerine cihaz türünü iletmek için de kullanılabilir. Özel veri yüklerini DPS ile gönderme ve alma hakkında daha fazla bilgi için bkz. [cihazlar ve DPS arasında yükleri aktarma](how-to-send-additional-data.md).

    ```c
    // An example custom payload
    const char* custom_json_payload = "{\"MyDeviceFirmwareVersion\":\"12.0.2.5\",\"MyDeviceProvisioningVersion\":\"1.0.0.0\"}";

    prov_device_result = Prov_Device_Set_Provisioning_Payload(prov_device_handle, custom_json_payload);
    if (prov_device_result != PROV_DEVICE_RESULT_OK)
    {
        (void)printf("\r\nFailure setting provisioning payload: %s\r\n", MU_ENUM_TO_STRING(PROV_DEVICE_RESULT, prov_device_result));
    }
    ```

7. **prov\_dev\_client\_sample** projesine sağ tıklayın ve **Başlangıç Projesi Olarak Ayarla**’yı seçin.

### <a name="simulate-the-contoso-toaster-device"></a>Contoso Toaster cihazının benzetimini yapma

1. Toaster cihazının benzetimini yapmak için, bir `prov_dev_set_symmetric_key_info()` Açıklama eklenen **prov \_ dev \_ Client \_ Sample. c** dosyasında öğesine yapılan çağrıyı bulun.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    İşlev çağrısının açıklamasını kaldırın ve yer tutucu değerlerini (açılı ayraçlar dahil), daha önce oluşturduğunuz Toaster kayıt KIMLIĞI ve türetilmiş cihaz anahtarıyla değiştirin. Aşağıda gösterilen anahtar değeri **JC8F96eayuQwwz + PkE7IzjH2lIAjCUnAa61tDigBnSs =** yalnızca örnek olarak verilmiştir.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-toaster-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    Dosyayı kaydedin.

2. Çözümü çalıştırmak için Visual Studio menüsünde Hata **ayıklama**  >  **olmadan Başlat** ' ı seçin. Projeyi yeniden oluşturmak için istemde, çalıştırmadan önce projeyi yeniden derlemek için **Evet**' i seçin.

    Aşağıdaki metin, Toaster cihazı için çalışan özel ayırma işlevi kodundan alınan örnek günlük çıktıdır. Bir Toaster cihazı için bir hub 'ın başarıyla seçili olduğuna dikkat edin. Ayrıca, `payload` koda eklediğiniz özel JSON içeriğini içeren üyeye de dikkat edin. Bu, kodunuzun içinde kullanması için kullanılabilir `deviceRuntimeContext` .

    Bu günlüğe kaydetme, portalda işlev kodu altında **Günlükler** tıklatılarak kullanılabilir:

    ```cmd
    2020-09-23T11:44:37.505 [Information] Executing 'Functions.HttpTrigger1' (Reason='This function was programmatically called via the host APIs.', Id=4596d45e-086f-4e86-929b-4a02814eee40)
    2020-09-23T11:44:41.380 [Information] C# HTTP trigger function processed a request.
    2020-09-23T11:44:41.381 [Information] Request.Body:...
    2020-09-23T11:44:41.381 [Information] {"enrollmentGroup":{"enrollmentGroupId":"contoso-custom-allocated-devices","attestation":{"type":"symmetricKey"},"capabilities":{"iotEdge":false},"etag":"\"e8002126-0000-0100-0000-5f6b2a570000\"","provisioningStatus":"enabled","reprovisionPolicy":{"updateHubAssignment":true,"migrateDeviceData":true},"createdDateTimeUtc":"2020-09-23T10:58:31.62286Z","lastUpdatedDateTimeUtc":"2020-09-23T10:58:31.62286Z","allocationPolicy":"custom","iotHubs":["contoso-toasters-hub-1098.azure-devices.net"],"customAllocationDefinition":{"webhookUrl":"https://contoso-function-app.azurewebsites.net/api/HttpTrigger1?****","apiVersion":"2019-03-31"}},"deviceRuntimeContext":{"registrationId":"contoso-toaster-007","symmetricKey":{},"payload":{"MyDeviceFirmwareVersion":"12.0.2.5","MyDeviceProvisioningVersion":"1.0.0.0"}},"linkedHubs":["contoso-toasters-hub-1098.azure-devices.net"]}
    2020-09-23T11:44:41.687 [Information] linkedHub : contoso-toasters-hub-1098.azure-devices.net
    2020-09-23T11:44:41.688 [Information] Selected hub : contoso-toasters-hub-1098.azure-devices.net
    2020-09-23T11:44:41.688 [Information] Response
    2020-09-23T11:44:41.688 [Information] {"iotHubHostName":"contoso-toasters-hub-1098.azure-devices.net"}
    2020-09-23T11:44:41.689 [Information] Executed 'Functions.HttpTrigger1' (Succeeded, Id=4596d45e-086f-4e86-929b-4a02814eee40, Duration=4347ms)    
    ```

    Aşağıdaki örnek cihaz çıktısı, sanal dağıtım ilkesi tarafından TOAO IoT Hub 'ına atanacak olan benzetimli Toaster cihazını başarıyla yeniden başlatma ve sağlama hizmeti örneğine bağlanma işlemini göstermektedir:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: contoso-toaster-007

    Press enter key to exit:
    ```

### <a name="simulate-the-contoso-heat-pump-device"></a>Contoso ısı pompa cihazının benzetimini yapın

1. Isı pompa cihazının benzetimini yapmak için, `prov_dev_set_symmetric_key_info()` **prov \_ dev \_ Client \_ Sample. c** içindeki çağrısını daha önce oluşturduğunuz ısı pompa kayıt kimliği ve türetilmiş cihaz anahtarıyla yeniden güncelleştirin. Aşağıda gösterilen anahtar değer **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg =** yalnızca örnek olarak verilmiştir.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-heatpump-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    Dosyayı kaydedin.

2. Çözümü çalıştırmak için Visual Studio menüsünde Hata **ayıklama**  >  **olmadan Başlat** ' ı seçin. Projeyi yeniden oluşturmak için istemde, çalıştırmadan önce projeyi yeniden derlemek için **Evet** ' i seçin.

    Aşağıdaki metin, ısı pompa cihazı için çalışan özel ayırma işlevi kodundan alınan örnek günlük çıktıdır. Özel ayırma ilkesi HTTP hatası 400 Hatalı Isteğiyle bu kaydı reddeder. `payload`Koda eklediğiniz özel JSON içeriğini içeren üyeye dikkat edin. Bu, kodunuzun içinde kullanması için kullanılabilir `deviceRuntimeContext` .

    Bu günlüğe kaydetme, portalda işlev kodu altında **Günlükler** tıklatılarak kullanılabilir:

    ```cmd
    2020-09-23T11:50:23.652 [Information] Executing 'Functions.HttpTrigger1' (Reason='This function was programmatically called via the host APIs.', Id=2fa77f10-42f8-43fe-88d9-a8c01d4d3f68)
    2020-09-23T11:50:23.653 [Information] C# HTTP trigger function processed a request.
    2020-09-23T11:50:23.654 [Information] Request.Body:...
    2020-09-23T11:50:23.654 [Information] {"enrollmentGroup":{"enrollmentGroupId":"contoso-custom-allocated-devices","attestation":{"type":"symmetricKey"},"capabilities":{"iotEdge":false},"etag":"\"e8002126-0000-0100-0000-5f6b2a570000\"","provisioningStatus":"enabled","reprovisionPolicy":{"updateHubAssignment":true,"migrateDeviceData":true},"createdDateTimeUtc":"2020-09-23T10:58:31.62286Z","lastUpdatedDateTimeUtc":"2020-09-23T10:58:31.62286Z","allocationPolicy":"custom","iotHubs":["contoso-toasters-hub-1098.azure-devices.net"],"customAllocationDefinition":{"webhookUrl":"https://contoso-function-app.azurewebsites.net/api/HttpTrigger1?****","apiVersion":"2019-03-31"}},"deviceRuntimeContext":{"registrationId":"contoso-heatpump-088","symmetricKey":{},"payload":{"MyDeviceFirmwareVersion":"12.0.2.5","MyDeviceProvisioningVersion":"1.0.0.0"}},"linkedHubs":["contoso-toasters-hub-1098.azure-devices.net"]}
    2020-09-23T11:50:23.654 [Information] Unknown device registration
    2020-09-23T11:50:23.654 [Information] Response
    2020-09-23T11:50:23.654 [Information] Unrecognized device registration.
    2020-09-23T11:50:23.655 [Information] Executed 'Functions.HttpTrigger1' (Succeeded, Id=2fa77f10-42f8-43fe-88d9-a8c01d4d3f68, Duration=11ms)
    ```

    Aşağıdaki örnek cihaz çıktısı, bir IoT Hub 'ına özel ayırma ilkesi kullanılarak kaydolma girişiminde bulunan sanal ısı pompa cihazının önyüklemesinin ve sağlama hizmeti örneğine bağlanma işlemini göstermektedir. `Custom allocation failed with status code: 400`Özel ayırma ilkesi yalnızca Toaster cihazlarına izin verecek şekilde tasarlandığından, bu hata () hatasıyla başarısız olur:


    ```cmd
    Provisioning API Version: 1.3.7
    
    Registering Device
    
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_device_ll_client.c Func:_prov_transport_process_json_reply Line:658 Provisioning Failure: OperationId: 4.eb89f3e8407a3711.2525bd34-02e9-4e91-a9c0-4dbc4ad5de66 - Date: 2020-09-23T17:05:58.2363145Z - Msg: Custom allocation failed with status code: 400
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_transport_mqtt_common.c Func:_prov_transport_common_mqtt_dowork Line:1014 Unable to process registration reply.
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_device_ll_client.c Func:_on_transport_registration_data Line:770 Failure retrieving data from the provisioning service
    
    Failure registering device: PROV_DEVICE_RESULT_DEV_AUTH_ERROR
    Press enter key to exit:    
    ```
    
## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu makalede oluşturulan kaynaklarla çalışmaya devam etmeyi planlıyorsanız, bunları bırakabilirsiniz. Kaynakları kullanmaya devam etmeyi planlamıyorsanız, gereksiz ücretlerden kaçınmak için bu makalede oluşturulan tüm kaynakları silmek için aşağıdaki adımları kullanın.

Buradaki adımlarda, bu makaledeki tüm kaynakları **contoso-US-Resource-Group** adlı aynı kaynak grubunda belirtildiği şekilde oluşturduğunuz varsayılır.

> [!IMPORTANT]
> Silinen kaynak grupları geri alınamaz. Kaynak grubu ve içindeki tüm kaynaklar kalıcı olarak silinir. Yanlış kaynak grubunu veya kaynakları yanlışlıkla silmediğinizden emin olun. IoT Hub'ı tutmak istediğiniz kaynakların bulunduğu mevcut bir kaynak grubunda oluşturduysanız kaynak grubunu silmek yerine IoT Hub kaynağını silin.
>

Kaynak grubunu ada göre silmek için:

1. [Azure portalda](https://portal.azure.com) oturum açın ve **Kaynak grupları**’nı seçin.

2. **Ada göre filtrele...** metin kutusuna kaynaklarınızı içeren kaynak grubunun adını yazın, **contoso-US-Resource-Group**. 

3. Sonuç listesinde kaynak grubunuzun sağında **.** .. ' ı seçin ve **kaynak grubunu silin**.

4. Kaynak grubunun silinmesini onaylamanız istenir. Onaylamak için kaynak grubunuzun adını yeniden yazın ve ardından **Sil**' i seçin. Birkaç dakika sonra kaynak grubu ve içerdiği kaynakların tümü silinir.

## <a name="next-steps"></a>Sonraki adımlar

Daha ayrıntılı bir özel ayırma ilkesi örneği için bkz. 

> [!div class="nextstepaction"]
> [Özel ayırma ilkeleri kullanma](how-to-use-custom-allocation-policies.md)

* Daha fazla yeniden sağlama hakkında daha fazla bilgi için bkz.

> [!div class="nextstepaction"]
> [Cihaz yeniden sağlama kavramlarını IoT Hub](concepts-device-reprovision.md)

* Daha fazla sağlama sağlamayı öğrenmek için bkz.

> [!div class="nextstepaction"]
> [Daha önce yeniden sağlanan cihazların sağlamasını kaldırma](how-to-unprovision-devices.md)