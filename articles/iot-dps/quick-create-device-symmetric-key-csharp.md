---
title: 'Hızlı başlangıç-C kullanarak Azure IoT Hub bir cihaz sağlamak için simetrik anahtar kullanın #'
description: Bu hızlı başlangıçta, bir IoT Hub 'ına simetrik anahtar cihazı sağlamak üzere cihaz sağlama hizmeti (DPS) için C# cihaz SDK 'sını kullanacaksınız
author: wesmc7777
ms.author: wesmc
ms.date: 10/21/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom: mvc
ms.openlocfilehash: a38d58eecd200ec312e7677a05531e27bf4ba6b0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868708"
---
# <a name="quickstart-provision-a-symmetric-key-device-using-c"></a>Hızlı başlangıç: C kullanarak bir simetrik anahtar cihazı sağlama #

Bu hızlı başlangıçta, C# kullanarak bir IoT Hub 'ına cihaz olarak Windows geliştirme makinesi sağlamayı öğreneceksiniz. Bu cihaz, bir IoT Hub 'ına atanmak üzere bir cihaz sağlama hizmeti (DPS) örneğiyle kimlik doğrulaması yapmak için bir simetrik anahtar ve tek bir kayıt kullanır. [C# için Azure IoT örneklerinden](https://github.com/Azure-Samples/azure-iot-samples-csharp) örnek kod, cihazı sağlamak için kullanılacaktır. 

Bu makalede tek bir kayıt ile sağlama işlemi gösterilmektedir, ancak kayıt grupları da kullanabilirsiniz. Kayıt grupları kullanılırken bazı farklılıklar vardır. Örneğin, cihaz için benzersiz bir kayıt KIMLIĞIYLE türetilmiş bir cihaz anahtarı kullanmanız gerekir. [Simetrik anahtarlarla cihaz sağlama](how-to-legacy-device-symm-key.md) bir kayıt grubu örneği sağlar. Kayıt grupları hakkında daha fazla bilgi için bkz. [simetrik anahtar kanıtlama Için Grup](concepts-symmetric-key-attestation.md#group-enrollments)kayıtları.

Otomatik sağlama işlemini bilmiyorsanız, [sağlamaya](about-iot-dps.md#provisioning-process) genel bakış konusunu gözden geçirin. 

Ayrıca, bu hızlı başlangıçla devam etmeden önce [IoT Hub Cihazı Sağlama Hizmetini Azure portalla ayarlama](./quick-setup-auto-provision.md) bölümünde bulunan adımları tamamladığınızdan emin olun. Bu hızlı başlangıç, Cihaz Sağlama Hizmeti örneğinizi zaten oluşturmuş olmanızı gerektirir.

Bu makale Windows tabanlı bir iş istasyonuna yöneliktir. Ancak yordamları Linux üzerinde gerçekleştirebilirsiniz. Linux örneği için bkz. [çok kiracılı sağlama](how-to-provision-multitenant.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Önkoşullar

* Windows tabanlı makinenizde [.NET Core 2,1 SDK](https://dotnet.microsoft.com/download) veya sonraki bir sürümünün yüklü olduğundan emin olun.

* [Git](https://git-scm.com/download/)'in en son sürümünün yüklemesi.

<a id="setupdevbox"></a>


## <a name="create-a-device-enrollment"></a>Cihaz kaydı oluşturma

1. [Azure Portal](https://portal.azure.com)oturum açın, sol taraftaki menüden **tüm kaynaklar** düğmesini seçin ve CIHAZ sağlama hizmeti (DPS) örneğinizi açın.

2. Kayıtları **Yönet** sekmesini seçin ve ardından üst kısımdaki **tek kayıt Ekle** düğmesini seçin. 

3. **Kayıt Ekle** panelinde, aşağıdaki bilgileri girin ve **Kaydet** düğmesine basın.

   - **Mekanizma**: Kimlik onay *Mekanizması* olarak **Simetrik Anahtar** seçeneğini belirleyin.

   - **Anahtarları otomatik oluştur**: Bu kutuyu işaretleyin.

   - **Kayıt Kimliği**: Kaydı tanımlamak için bir kayıt kimliği girin. Yalnızca küçük alfasayısal karakterler ile kısa çizgi ('-') karakterlerini kullanın. Örneğin, **SYMM-Key-CSharp-Device-01**.

   - **IoT Hub Cihaz Kimliği:** Bir cihaz tanımlayıcısı girin. Örneğin, **CSharp-Device-01**.

     ![Portalda simetrik anahtar kanıtı için bireysel kayıt ekleme](./media/quick-create-device-symmetric-key-csharp/create-individual-enrollment-csharp.png)

4. Kaydınız kaydedildikten sonra, **birincil anahtar** ve **İkincil anahtar** oluşturulur ve kayıt girişine eklenir. Simetrik anahtar cihaz kaydınız, *bireysel* kayıtlar SEKMESINDEKI *kayıt kimliği* sütununun altında **SYMM-Key-CSharp-Device-01** olarak görünür. 

5. Kaydı açın ve üretilen **birincil anahtar** ve **İkincil anahtarınızın** değerini kopyalayın. Bu anahtar değerini ve **kayıt kimliğini** daha sonra, cihaz sağlama örnek kodu ile kullanmak üzere ortam değişkenleri eklediğinizde kullanacaksınız.



## <a name="prepare-the-c-environment"></a>C# ortamını hazırlama 

1. Git CMD veya git Bash komut satırı ortamını açın. Aşağıdaki komutu kullanarak C# GitHub deposu [Için Azure IoT örneklerini](https://github.com/Azure-Samples/azure-iot-samples-csharp) kopyalayın:

    ```cmd
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```



<a id="firstbootsequence"></a>

## <a name="prepare-the-device-provisioning-code"></a>Cihaz sağlama kodunu hazırlama

Bu bölümde, simetrik anahtar cihazınızı sağlamak üzere cihaz sağlama örnek kodu için parametre olarak kullanılacak aşağıdaki dört ortam değişkenini ekleyeceksiniz. 

* `DPS_IDSCOPE`
* `PROVISIONING_REGISTRATION_ID`
* `PRIMARY_SYMMETRIC_KEY`
* `SECONDARY_SYMMETRIC_KEY`

Sağlama kodu, cihazınızın kimliğini doğrulamak için bu değişkenlere göre DPS örneğiyle iletişim kuracaktır. Daha sonra cihaz, bireysel kayıt yapılandırmasına göre zaten DPS örneğine bağlanmış olan bir IoT Hub 'ına atanır. Sağlandıktan sonra örnek kod, IoT Hub 'ına bazı test telemetrisi gönderir.

1. [Azure Portal](https://portal.azure.com), cihaz sağlama hizmeti menünüzde **genel bakış** ' ı seçin ve _hizmet uç_ noktanızı ve _kimlik kapsamınızı_ kopyalayın. `PROVISIONING_HOST`Ve ortam değişkenleri için bu değerleri kullanacaksınız `DPS_IDSCOPE` .

    ![Hizmet bilgileri](./media/quick-create-device-symmetric-key-csharp/extract-dps-endpoints.png)

2. Bir komut istemi açın ve kopyalanan örnekler deposundaki *Symmetrickeysample* ' a gidin:

    ```cmd
    cd provisioning\Samples\device\SymmetricKeySample
    ```

3. *Symmetrickeysample* klasöründe, *program. cs* ' yi bir metin düzenleyicisinde açın ve ve dizelerini belirten kod satırlarını bulun `individualEnrollmentPrimaryKey` `individualEnrollmentSecondaryKey` . Anahtar kodlanması yerine ortam değişkenlerinin kullanılması için bu kod satırlarını aşağıdaki gibi güncelleştirin.
 
    ```csharp
        //These are the two keys that belong to your individual enrollment. 
        // Leave them blank if you want to try this sample for an individual enrollment instead
        //private const string individualEnrollmentPrimaryKey = "&quot;;
        //private const string individualEnrollmentSecondaryKey = &quot;&quot;;

        private static string individualEnrollmentPrimaryKey = Environment.GetEnvironmentVariable(&quot;PRIMARY_SYMMETRIC_KEY");;
        private static string individualEnrollmentSecondaryKey = Environment.GetEnvironmentVariable("SECONDARY_SYMMETRIC_KEY");;
    ```

    Ayrıca, `registrationId` aşağıdaki gibi bir ortam değişkeni de kullanmak için, dizeyi ayarlayan ve aşağıdaki gibi güncelleştiren kod satırını bulun:

    ```csharp
        //This field is mandatory to provide for this sample
        //private static string registrationId = "";

        private static string registrationId = Environment.GetEnvironmentVariable("PROVISIONING_REGISTRATION_ID");;
    ```

    *Program. cs*' ye değişiklikleri kaydedin.

3. Komut istemindeki KIMLIK kapsamı, kayıt KIMLIĞI, birincil ve ikincil simetrik anahtarlar için, önceki bölümde tek bir kayıttan kopyaladığınız ortam değişkenlerini ekleyin.  

    Aşağıdaki komutlar komut sözdizimini göstermek için örnektir. Doğru değerlerinizi kullandığınızdan emin olun.

    ```console
    set DPS_IDSCOPE=0ne00000A0A
    ```

    ```console
    set PROVISIONING_REGISTRATION_ID=symm-key-csharp-device-01
    ```

    ```console
    set PRIMARY_SYMMETRIC_KEY=sbDDeEzRuEuGKag+kQKV+T1QGakRtHpsERLP0yPjwR93TrpEgEh/Y07CXstfha6dhIPWvdD1nRxK5T0KGKA+nQ==
    ```

    ```console
    set SECONDARY_SYMMETRIC_KEY=Zx8/eE7PUBmnouB1qlNQxI7fcQ2HbJX+y96F1uCVQvDj88jFL+q6L9YWLLi4jqTmkRPOulHlSbSv2uFgj4vKtw==
    ```


4. Aşağıdaki komutu kullanarak örnek kodu derleyin ve çalıştırın.

    ```console
    dotnet run
    ```

5. Beklenen çıktı, ayrı kayıt ayarlarına bağlı olarak cihazın atandığı bağlantılı IoT Hub 'ını gösteren aşağıdakine benzer olmalıdır. Bir örnek "TestMessage" dizesi bir test olarak hub 'a gönderilir:

    ```output
    D:\azure-iot-samples-csharp\provisioning\Samples\device\SymmetricKeySample>dotnet run
    RegistrationID = symm-key-csharp-device-01
    ProvisioningClient RegisterAsync . . . Assigned
    ProvisioningClient AssignedHub: docs-test-iot-hub.azure-devices.net; DeviceID: csharp-device-01
    Creating Symmetric Key DeviceClient authentication
    DeviceClient OpenAsync.
    DeviceClient SendEventAsync.
    DeviceClient CloseAsync.
    Enter any key to exit
    ```
    
6. Azure portal, sağlama hizmetinize bağlı olan IoT Hub 'ına gidin ve **IoT cihazları** dikey penceresini açın. Simetrik anahtar cihazı hub 'a başarıyla sağlamaktan sonra cihaz KIMLIĞI, **etkin** olarak *durumuyla* gösterilir. Cihaz örnek kodunu çalıştırmadan önce dikey pencereyi zaten açtıysanız, en üstteki **Yenile** düğmesine basmanız gerekebilir. 

    ![Cihaz IOT hub'da kayıtlı](./media/quick-create-device-symmetric-key-csharp/hub-registration-csharp.png) 

> [!NOTE]
> Cihazınız için *başlangıç cihaz ikizi durumu* ayarının kayıt girişindeki varsayılan değerini değiştirdiyseniz istenen ikili durumu hub'dan çekerek ona göre hareket edebilir. Daha fazla bilgi için bkz. [IoT Hub cihaz TWINS 'ı anlama ve kullanma](../iot-hub/iot-hub-devguide-device-twins.md).
>


## <a name="clean-up-resources"></a>Kaynakları temizleme

Cihaz istemci örneğini üzerinde çalışmaya ve keşfetmeye devam etmeyi planlıyorsanız, bu hızlı başlangıçta oluşturulan kaynakları temizlemeyin. Devam etmeyi planlamıyorsanız, bu hızlı başlangıç tarafından oluşturulan tüm kaynakları silmek için aşağıdaki adımları kullanın.

1. Azure portal sol taraftaki menüden **tüm kaynaklar** ' ı seçin ve ardından cihaz sağlama hizmetinizi seçin. Hizmetiniz için kayıtları **Yönet** ' i açın ve **bireysel** kayıtlar sekmesini seçin. Bu hızlı başlangıçta kaydettiğiniz cihazın *kayıt kimliği* ' nin yanındaki onay kutusunu işaretleyin ve bölmenin en üstündeki **Sil** düğmesine basın. 
1. Azure portal sol taraftaki menüden **tüm kaynaklar** ' ı seçin ve ardından IoT Hub 'ınızı seçin. , Hub 'ınız için **IoT cihazlarını** açın, bu hızlı başlangıçta kaydettiğiniz CIHAZıN *cihaz kimliği* ' nin yanındaki onay kutusunu işaretleyin ve ardından bölmenin en üstündeki **Sil** düğmesine basın.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, IoT Hub cihaz sağlama hizmetini kullanarak IoT Hub 'ınıza Windows tabanlı bir simetrik anahtar cihazı sağlamış olursunuz. C# kullanarak X. 509.440 sertifika cihazlarını sağlamayı öğrenmek için, X. 509.440 cihazları için aşağıdaki hızlı başlangıç ile devam edin. 

> [!div class="nextstepaction"]
> [Azure hızlı başlangıç-DPS ve C kullanarak X. 509.440 cihazları sağlama #](quick-create-simulated-device-x509-csharp.md)
