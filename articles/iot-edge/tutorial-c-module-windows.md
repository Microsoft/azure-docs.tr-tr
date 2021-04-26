---
title: Öğretici-Azure IoT Edge kullanarak Windows için C modülleri geliştirme
description: Bu öğreticide, C kodu ile IoT Edge modülleri oluşturma ve bunları IoT Edge çalıştıran Windows cihazlarına dağıtma işlemlerinin nasıl yapılacağı gösterilmektedir.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
monikerRange: =iotedge-2018-06
ms.openlocfilehash: a67bf92dbee9bf3d444add3be32544e4c3eba4f3
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106221611"
---
# <a name="tutorial-develop-c-iot-edge-modules-using-windows-containers"></a>Öğretici: Windows kapsayıcıları kullanarak C IoT Edge modülleri geliştirme

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Bu makalede C kodu geliştirmek ve Azure IoT Edge çalıştıran bir Windows cihazına dağıtmak için Visual Studio 'Nun nasıl kullanılacağı gösterilmektedir.

>[!NOTE]
>IoT Edge 1,1 LTS, Windows kapsayıcılarını destekleyen son sürüm kanaldır. Sürüm 1,2 ' den başlayarak Windows kapsayıcıları desteklenmez. Windows cihazlarda IoT Edge çalıştırmak için [Windows 'Ta Linux için IoT Edge](iot-edge-for-linux-on-windows.md) kullanmayı veya taşımayı düşünün.

Azure IoT Edge modüllerini, iş mantığınızı doğrudan IoT Edge cihazlarınızda uygulayan kodu dağıtmak için kullanabilirsiniz. Bu öğreticide, algılayıcı verilerini filtreleyen bir IoT Edge modülü oluşturma ve dağıtma işlemlerinin adımları açıklanmaktadır.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> * C SDK 'sını temel alan bir IoT Edge modülü oluşturmak için Visual Studio 'Yu kullanın.
> * Visual Studio ve Docker kullanarak bir Docker görüntüsü oluşturun ve bunu Kayıt defterinize yayımlayın.
> * Modülü IoT Edge cihazınıza dağıtma.
> * Oluşturulan verileri görüntüleme.

Bu öğreticide oluşturacağınız IoT Edge modülü, cihazınız tarafından oluşturulan sıcaklık verilerini filtreler. Modül, yalnızca sıcaklık belirtilen eşiği aşarsa yukarı akış iletileri gönderir. Bu tür bir analiz, buluta iletilen ve bulutta depolanan veri miktarını azaltmak için yararlıdır.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide, Visual Studio 2019 kullanarak C 'de bir modülün nasıl geliştirilmesi ve sonra bir Windows cihazına dağıtılması gösterilmektedir. Linux kapsayıcıları kullanarak modüller geliştiriyorsanız bunun yerine [Linux kapsayıcıları kullanarak C IoT Edge modülleri geliştirme](tutorial-csharp-module.md) bölümüne gidin.

Windows kapsayıcıları kullanarak C modülleri geliştirme ve dağıtmaya yönelik seçeneklerinizi anlamak için aşağıdaki tabloya bakın:

| C | Visual &nbsp; Studio &nbsp; kodu | Visual Studio 2017 &nbsp; ve &nbsp; 2019 |
| -- | ------------------ | :------------------: |
| Windows AMD64 |  | ![Visual Studio 'da WinAMD64 için C modülleri geliştirme](./media/tutorial-c-module/green-check.png) |

Bu öğreticiye başlamadan önce, [Windows kapsayıcıları kullanarak IoT Edge modülleri](tutorial-develop-for-windows.md) geliştirme öğreticisindeki yönergeleri izleyerek geliştirme ortamınızı ayarlayın. Bu işlem tamamlandıktan sonra ortamınız aşağıdaki önkoşulları içerir:

* Azure 'da ücretsiz veya Standart katmanlı [IoT Hub 'ı](../iot-hub/iot-hub-create-through-portal.md) .
* [Azure IoT Edge çalıştıran bir Windows cihazı](how-to-install-iot-edge-windows-on-windows.md).
* [Azure Container Registry](../container-registry/index.yml)gibi bir kapsayıcı kayıt defteri.
* [Visual Studio 2019](/visualstudio/install/install-visual-studio), [Azure IoT Edge araçları](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) uzantısıyla yapılandırıldı.
* Windows kapsayıcıları çalıştıracak şekilde yapılandırılmış [Docker Desktop](https://docs.docker.com/docker-for-windows/install/).

Aşağıdaki komutları çalıştırarak, Windows x64 için Azure IoT C SDK 'sını vcpkg aracılığıyla yüklersiniz:

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```

> [!TIP]
> Visual Studio 2017 (sürüm 15,7 veya üzeri) kullanıyorsanız, [Visual Studio Market](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)visual Studio 2017 Için Azure IoT Edge araçları indirin ve yükleyin.

## <a name="create-a-module-project"></a>Modül projesi oluşturma

Bu bölümde, Visual Studio ve Azure IoT Edge araçları uzantısı kullanılarak C SDK 'sını temel alan IoT Edge bir modül projesi oluşturacaksınız. Bir proje şablonu oluşturduktan sonra modül, bildirilen özelliklerine göre iletileri filtreleyerek yeni kod ekleyeceksiniz.

### <a name="create-a-new-project"></a>Yeni proje oluşturma

Kendi yazacağınız kodla özelleştirebileceğiniz bir C çözüm şablonu oluşturun.

1. Visual Studio 2019 ' i açın ve ardından **Yeni proje oluştur**' u seçin.

1. **Yeni proje oluştur** bölmesinde **IoT Edge** araması yapın ve ardından sonuçlar listesinde **Azure IoT Edge (Windows amd64)** projesini seçin.

   !["Yeni proje oluştur" bölmesinin IoT Edge ekran görüntüsü.](./media/tutorial-c-module-windows/new-project.png)

1. **İleri**’yi seçin.

    **Yeni projenizi yapılandırın** bölmesi açılır.

   !["Yeni projenizi yapılandırma" bölmesinin ekran görüntüsü.](./media/tutorial-c-module-windows/configure-project.png)

1. **Yeni projenizi yapılandırın** bölmesinde, proje ve çözümü **CTutorialApp** gibi daha açıklayıcı bir şekilde yeniden adlandırın. 

1. Projeyi oluşturmak için **Oluştur**'u seçin.

   **Modül Ekle** bölmesi açılır.

   ![Projenizi yapılandırmak için "Modül Ekle" bölmesinin ekran görüntüsü.](./media/tutorial-c-module-windows/add-application-and-module.png)

1. **Yeni projenizi yapılandırın** sayfasında, aşağıdakileri yapın:

   a. Sol bölmede **C modülü** şablonunu seçin.  
   b. **Modül adı** kutusuna **cmodule** yazın.  
   c. **Depo URL 'si** kutusunda, **localhost: 5000** ' i Azure Container kayıt defterinizin **oturum açma sunucusu** değeriyle aşağıdaki biçimde değiştirin:`<registry name>.azurecr.io/cmodule`

    > [!NOTE]
    > Görüntü deposu, kapsayıcı kayıt defterinizin adını ve kapsayıcı görüntünüzün adını içerir. Kapsayıcı resminiz, Module proje-adı değerinden önceden doldurulur.  Oturum açma sunucusunu Azure portal kapsayıcı kayıt defterinizin genel bakış sayfasından alabilirsiniz.

1. Projeyi oluşturmak için **Ekle** ' yi seçin.

### <a name="add-your-registry-credentials"></a>Kayıt defteri kimlik bilgilerinizi ekleme

Dağıtım bildirimi, IoT Edge çalışma zamanına sahip kapsayıcı kayıt defteriniz için kimlik bilgilerini paylaşır. Çalışma zamanı, özel görüntülerinizi IoT Edge cihazına çekmek için bu kimlik bilgilerine ihtiyaç duyar. Azure Container Registry 'nizin **erişim anahtarları** bölümünden kimlik bilgilerini kullanın.

1. Visual Studio Çözüm Gezgini ' de dosya *üzerindedeployment.template.js* açın.

1. $EdgeAgent istenen özelliklerde **Registrycredentials** özelliğini arayın. Özelliğin kayıt defteri adresi, projeyi oluştururken verdiğiniz bilgilerle birlikte tekrar doldurulmalıdır. Kullanıcı adı ve parola alanları, değişken adlarını içermelidir. Örnek:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

1. Modül çözümünüzde ortam (ENV) dosyasını açın. Varsayılan olarak, dosya Çözüm Gezgini gizlidir, bu nedenle görüntülemek için **tüm dosyaları göster** düğmesini seçmeniz gerekebilir. ENV dosyası, dosyasında *deployment.template.js* gördüğünüz Kullanıcı adı ve parola değişkenlerini içermelidir.

1. Azure Container Registry 'nizden **Kullanıcı adı** ve **parola** değerlerini ekleyin.

1. ENV dosyasında yaptığınız değişiklikleri kaydedin.

### <a name="update-the-module-with-custom-code"></a>Modülü özel kodla güncelleştirme

Varsayılan modül kodu bir giriş sırasındaki iletileri alır ve bunları bir çıkış kuyruğu aracılığıyla geçirir. Modülün iletileri IoT Hub 'ınıza iletmeden önce daha fazla kod ekleyelim. Her iletideki sıcaklık verilerini analiz etmek ve yalnızca sıcaklığın belirli bir eşiği aşması durumunda iletiyi IoT Hub 'ına göndermesi için modülü güncelleştirin.

1. Bu senaryoda sensörden alınan veriler JSON biçimindedir. JSON biçimindeki iletileri filtreleme amacıyla C için bir JSON kitaplığını içeri aktarın. Bu öğreticide Parson kullanılmıştır.

   a. [Parson GitHub deposunu](https://github.com/kgabis/parson)indirin.  
   b. *Parson. c* ve *Parson. h* dosyalarını cmodule projesine kopyalayın.  
   c. Visual Studio 'da, CModule proje klasöründen *CMakeLists.txt* dosyasını açın.  
   d. Dosyanın en üstünde Parson dosyalarını **my_parson** adlı bir kitaplık olarak içeri aktarın.

      ```txt
      add_library(my_parson
          parson.c
          parson.h
      )
      ```  
   e. `my_parson` *CMakeLists.txt* dosyasının "target_link_libraries" bölümündeki kitaplık listesine ekleyin.  
   f. *CMakeLists.txt* dosyasını kaydedin.  
   örneğin: **Cmodule**  >  **Main. c**' yi seçin. Include deyimlerinin listesinin en altında, JSON desteği için dahil edilecek yeni bir deyim ekleyin `parson.h` :

      ```c
      #include "parson.h"
      ```

1. *Main. c* dosyasında, değişkeninin yanına adlı bir genel değişken ekleyin `temperatureThreshold` `messagesReceivedByInput1Queue` . Bu değişken ölçülen sıcaklığın, veri IoT Hub 'ınıza gönderilmesi için aşmaması gereken değeri ayarlar.

    ```c
    static double temperatureThreshold = 25;
    ```

1. `CreateMessageInstance` *Main. c* içinde işlevi bulun. İç *if-else* ifadesini aşağıdaki kodla değiştirin, bu da birkaç işlev satırı ekler:

   ```c
   if ((messageInstance->messageHandle = IoTHubMessage_Clone(message)) == NULL)
   {
       free(messageInstance);
       messageInstance = NULL;
   }
   else
   {
       messageInstance->messageTrackingId = messagesReceivedByInput1Queue;
       MAP_HANDLE propMap = IoTHubMessage_Properties(messageInstance->messageHandle);
       if (Map_AddOrUpdate(propMap, "MessageType", "Alert") != MAP_OK)
       {
          printf("ERROR: Map_AddOrUpdate Failed!\r\n");
       }
   }
   ```

   *Else* deyimindeki kodun yeni satırları iletiye bir uyarı olarak etiketleyen yeni bir özellik ekler. Bu kod tüm iletileri uyarı olarak etiketlediği için, yalnızca yüksek sıcaklıklar bildirdiklerinde IoT Hub 'ına ileti gönderen işlevler ekleyeceğiz.

1. İşlevini bulun `InputQueue1Callback` ve işlevin tamamını aşağıdaki kodla değiştirin. Bu işlev gerçek bir mesajlaşma filtresi uygular. Bir ileti alındığında, bildirilen sıcaklığın eşiği aşıp aşmadığını denetler. Sıcaklık eşiği aşarsa, işlev iletiyi çıkış kuyruğu aracılığıyla iletir. Eşik değeri aşmazsa, işlev iletiyi yoksayar.

    ```c
    static unsigned char *bytearray_to_str(const unsigned char *buffer, size_t len)
    {
        unsigned char *ret = (unsigned char *)malloc(len + 1);
        memcpy(ret, buffer, len);
        ret[len] = '\0';
        return ret;
    }

    static IOTHUBMESSAGE_DISPOSITION_RESULT InputQueue1Callback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
        IOTHUBMESSAGE_DISPOSITION_RESULT result;
        IOTHUB_CLIENT_RESULT clientResult;
        IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle = (IOTHUB_MODULE_CLIENT_LL_HANDLE)userContextCallback;

        unsigned const char* messageBody;
        size_t contentSize;

        if (IoTHubMessage_GetByteArray(message, &messageBody, &contentSize) == IOTHUB_MESSAGE_OK)
        {
            messageBody = bytearray_to_str(messageBody, contentSize);
        } else
        {
            messageBody = "<null>";
        }

        printf("Received Message [%zu]\r\n Data: [%s]\r\n",
                messagesReceivedByInput1Queue, messageBody);

        // Check whether the message reports temperatures that exceed the threshold
        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;

        // If temperature exceeds the threshold, send to output1
        if (json_object_dotget_value(root_object, "machine.temperature") != NULL && (temperature = json_object_dotget_number(root_object, "machine.temperature")) > temperatureThreshold)
        {
            printf("Machine temperature %f exceeds threshold %f\r\n", temperature, temperatureThreshold);
            // This message should be sent to next stop in the pipeline, namely "output1".  What happens at "outpu1" is determined
            // by the configuration of the Edge routing table setup.
            MESSAGE_INSTANCE *messageInstance = CreateMessageInstance(message);
            if (NULL == messageInstance)
            {
                result = IOTHUBMESSAGE_ABANDONED;
            }
            else
            {
                printf("Sending message (%zu) to the next stage in pipeline\n", messagesReceivedByInput1Queue);

                clientResult = IoTHubModuleClient_LL_SendEventToOutputAsync(iotHubModuleClientHandle, messageInstance->messageHandle, "output1", SendConfirmationCallback, (void *)messageInstance);
                if (clientResult != IOTHUB_CLIENT_OK)
                {
                    IoTHubMessage_Destroy(messageInstance->messageHandle);
                    free(messageInstance);
                    printf("IoTHubModuleClient_LL_SendEventToOutputAsync failed on sending msg#=%zu, err=%d\n", messagesReceivedByInput1Queue, clientResult);
                    result = IOTHUBMESSAGE_ABANDONED;
                }
                else
                {
                    result = IOTHUBMESSAGE_ACCEPTED;
                }
            }
        }
        // If message does not exceed the threshold, do not forward
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

1. `moduleTwinCallback` işlevi ekleyin. Bu yöntem modül ikizinin istenen özellikleri üzerinde yapılan güncelleştirmeleri alır ve **temperatureThreshold** değişkenini buna göre güncelleştirir. Tüm modüllerin kendi modülü ikizi vardır ve bu, modülün içinde çalışan kodu doğrudan buluttan yapılandırmanıza olanak tanır.

    ```c
    static void moduleTwinCallback(DEVICE_TWIN_UPDATE_STATE update_state, const unsigned char* payLoad, size_t size, void* userContextCallback)
    {
        printf("\r\nTwin callback called with (state=%s, size=%zu):\r\n%s\r\n",
            MU_ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
        JSON_Value *root_value = json_parse_string(payLoad);
        JSON_Object *root_object = json_value_get_object(root_value);
        if (json_object_dotget_value(root_object, "desired.TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_dotget_number(root_object, "desired.TemperatureThreshold");
        }
        if (json_object_get_value(root_object, "TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_get_number(root_object, "TemperatureThreshold");
        }
    }
    ```

1. `SetupCallbacksForModule`İşlevi bulun. İşlevi, ikizi modülünün güncelleştirilip güncelleştirilmediğini denetlemek için bir *Else-if* ifadesini ekleyen aşağıdaki kodla değiştirin.

   ```c
   static int SetupCallbacksForModule(IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle)
   {
       int ret;

       if (IoTHubModuleClient_LL_SetInputMessageCallback(iotHubModuleClientHandle, "input1", InputQueue1Callback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetInputMessageCallback(\"input1\")..........FAILED!\r\n");
           ret = MU_FAILURE;
       }
       else if (IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, moduleTwinCallback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetModuleTwinCallback(default)..........FAILED!\r\n");
           ret = MU_FAILURE;
       }
       else
       {
           ret = 0;
       }

       return ret;
   }
   ```

1. *main.c* dosyasını kaydedin.

1. deployment.template.jsdosya *üzerinde* açın.

1. İkizi **Cmodule** modülünü dağıtım bildirimine ekleyin. Aşağıdaki JSON içeriğini `moduleContent` bölümünün en altına, `$edgeHub` modül ikizinin arkasına ekleyin:

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![Dağıtım şablonuna eklenmekte olan ikizi modülünü gösteren ekran görüntüsü.](./media/tutorial-c-module-windows/module-twin.png)

1. *deployment.template.json* dosyasını kaydedin.

## <a name="build-and-push-your-module"></a>Modülünüzü derleyin ve gönderin

Önceki bölümde, bildirilen makine sıcaklığının kabul edilebilir eşiğin altında olduğu iletileri filtrelemek için bir IoT Edge çözümü oluşturdunuz ve **Cmodule** 'e kod eklediniz. Şimdi çözümü kapsayıcı görüntüsü olarak derlemeniz ve kapsayıcı kayıt defterine göndermeniz gerekiyor.

### <a name="sign-in-to-docker"></a>Docker 'da oturum açın

Kapsayıcı görüntünüzü kayıt defterinde depolanacak şekilde gönderebilmesi için, kapsayıcı kayıt defteri kimlik bilgilerinizi geliştirme makinenizde Docker 'a sağlayın.

1. PowerShell 'i veya bir komut Istemi penceresini açın.

1. Kayıt defterini oluşturduktan sonra kaydettiğiniz Azure Container Registry kimlik bilgileriyle Docker 'da oturum açın.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Kullanımını öneren bir güvenlik uyarısı alabilirsiniz `--password-stdin` . Bu, üretim senaryoları için en iyi uygulama olarak önerdiğimiz halde Bu öğreticinin kapsamı dışındadır. Daha fazla bilgi için bkz. [Docker oturum açma başvurusu](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

### <a name="build-and-push"></a>Derleme ve gönderme

Geliştirme makinenizin artık kapsayıcı Kayıt defterinize erişimi vardır ve IoT Edge cihazlarınız da çok fazla olur. Proje kodunu bir kapsayıcı görüntüsüne dönüştürmek zaman.

1. Visual Studio Çözüm Gezgini, derlemek istediğiniz projenin adına sağ tıklayın. Varsayılan ad **AzureIotEdgeApp1**' dir. Bu öğreticide, **CTutorialApp** adını seçtik ve bir Windows modülü oluştururken, uzantının **Windows. amd64** olması gerekir.

1. **Derleme ve gönderme IoT Edge modüllerini** seçin.

   Build ve push komutu üç işlem başlatır:
   * İlk olarak, *yapılandırma* adlı çözümde tam dağıtım bildirimini tutan yeni bir klasör oluşturur. Dağıtım şablonundaki bilgilerden ve diğer çözüm dosyalarında oluşturulmuştur. 
   * İkincisi, `docker build` hedef mimariniz için uygun Dockerfile 'ı temel alan kapsayıcı görüntüsünü oluşturmak için çalışır. 
   * Son olarak, `docker push` görüntü deposunu kapsayıcı Kayıt defterinize göndermek için çalışır.

   Bu işlem ilk kez birkaç dakika sürebilir, ancak komutları bir sonraki çalıştırışınızda daha hızlı bir şekilde devam eder.

## <a name="deploy-modules-to-the-device"></a>Modülleri cihaza dağıt

Modül projesini IoT Edge cihazınıza dağıtmak için Visual Studio Cloud Explorer ve Azure IoT Edge araçları uzantısını kullanın. Senaryonuz için *yapılandırma* klasöründeki *deployment.windows-amd64.js* bir dağıtım bildirimi hazırladınız. Tek yapmanız gereken dağıtımı almak üzere bir cihaz seçmek.

IoT Edge cihazınızın çalışır ve çalışıyor olduğundan emin olun.

1. Visual Studio Cloud Explorer 'da IoT cihazlarınızın listesini görüntülemek için kaynakları genişletin.

1. Dağıtımı almak istediğiniz IoT Edge cihazının adına sağ tıklayın.

1. **Dağıtım oluştur**' u seçin.

1. Visual Studio dosya Gezgini 'nde, çözümünüzün *yapılandırma* klasöründeki dosya *deployment.windows-amd64.js* seçin.

1. Cihazınızın altında listelenen dağıtılan modülleri görüntülemek için Cloud Explorer 'ı yenileyin.

## <a name="view-generated-data"></a>Oluşturulan verileri görüntüleme

Dağıtım bildirimini IoT Edge cihazınıza uyguladıktan sonra, cihazdaki IoT Edge çalışma zamanı yeni dağıtım bilgilerini toplar ve üzerinde yürütmeye başlar. Cihazda çalışan ancak dağıtım bildirimine dahil olmayan tüm modüller durdurulur. Cihazda eksik olan modüller başlatılır.

IoT Hub 'ınıza geldikçe iletileri görüntülemek için IoT Edge araçları uzantısını kullanabilirsiniz.

1. Visual Studio Cloud Explorer 'da IoT Edge cihazınızın adını seçin.

1. **Eylemler** listesinde, **Izlemeyi Başlat yerleşik olay uç noktası**' nı seçin.

1. IoT Hub 'ınıza ulaşan iletileri görüntüleyin. IoT Edge cihazın yeni dağıtımını alması ve tüm modülleri başlatması gerektiğinden, iletilerin gelmesi biraz zaman alabilir. CModule kodundaki değişiklikler, iletiler gönderilmeden önce makine sıcaklığının 25 dereceye ulaşması bitinceye kadar beklemeniz gerekir. Kod ayrıca, bu sıcaklık eşiğine ulaşan iletilere ileti türü **uyarısını** ekler.

   ![IoT Hub 'ına ulaşan iletileri görüntüleyen çıkış penceresinin ekran görüntüsü.](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>İkizi modülünü düzenleme

Sıcaklık eşiğini 25 derece ayarlamak için CModule modülünü ikizi kullandınız. Modül kodunu güncelleştirmek zorunda kalmadan işlevselliği değiştirmek için ikizi modülünü kullanabilirsiniz.

1. Visual Studio 'da *deployment.windows-amd64.jsdosya üzerinde* açın. 

   *Deployment. Template* *dosyasını açmayın.* Çözüm Gezgini içindeki *yapılandırma* dosyasında dağıtım bildirimini görmüyorsanız, Çözüm Gezgini araç çubuğunda **tüm dosyaları göster** simgesini seçin.

1. CModule ikizi bulun ve **temperatureThreshold** parametresinin değerini, en son bildirilen sıcaklığın üzerinde 5 ila 10 derece daha yüksek olan yeni bir sıcaklığa değiştirin.

1. *deployment.windows-amd64.js* dosyaya kaydedin.

1. Güncelleştirilmiş dağıtım bildirimini cihazınıza uygulamak için dağıtım yönergelerini yeniden izleyin.

1. Gelen cihazdan buluta iletileri izleyin. Yeni sıcaklık eşiğine ulaşılana kadar iletiler durdurulmalıdır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Önerilen bir sonraki makaleye devam etmeyi planlıyorsanız, bu öğreticide oluşturduğunuz kaynakları ve konfigürasyonları tutabilir ve yeniden kullanabilirsiniz. Aynı IoT Edge cihazını test cihazı olarak kullanmaya devam edebilirsiniz.

Aksi takdirde, ücretlendirmesini önlemek için, burada kullandığınız yerel konfigürasyonları ve Azure kaynaklarını silebilirsiniz.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide IoT Edge cihazınız tarafından üretilen ham verileri filtreleme kodunu içeren bir IoT Edge modülü oluşturdunuz.

Azure IoT Edge, verileri işlemek ve analiz etmek için Azure bulut hizmetleri 'ni dağıtmanıza nasıl yardımcı olabileceğini öğrenmek için, sonraki öğreticilere devam edin.

> [!div class="nextstepaction"]
> [Azure işlevleri](tutorial-deploy-function.md) 
>  [Azure Stream Analytics](tutorial-deploy-stream-analytics.md) 
>  [Azure Machine Learning](tutorial-deploy-machine-learning.md) 
>  [Özel görüntü işleme hizmeti](tutorial-deploy-custom-vision.md)
