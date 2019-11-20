---
title: Öğretici Windows için C modülü geliştirme-Azure IoT Edge | Microsoft Docs
description: Bu öğreticide, C kodu ile IoT Edge modülü oluşturma ve çalıştıran bir Windows cihazına dağıtma gösterilmektedir IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 05/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: c098b67ab2782fa3cf29b5b19aa198f899ba69c0
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890608"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Öğretici: Windows cihazları için C IoT Edge modülü geliştirme

C kodu geliştirmek ve Azure IoT Edge çalıştıran bir Windows cihazına dağıtmak için Visual Studio 'Yu kullanın. 

İş mantığınızı uygulayan kodu doğrudan IoT Edge cihazlarınıza dağıtmak için Azure IoT Edge modüllerini kullanabilirsiniz. Bu öğreticide, algılayıcı verilerini filtreleyen bir IoT Edge modülü oluşturma ve dağıtma işlemlerinin adımları açıklanmaktadır. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:    

> [!div class="checklist"]
> * C SDK 'sını temel alan bir IoT Edge modülü oluşturmak için Visual Studio 'Yu kullanın.
> * Visual Studio ve Docker kullanarak bir Docker görüntüsü oluşturun ve bunu Kayıt defterinize yayımlayın.
> * Modülü IoT Edge cihazınıza dağıtma.
> * Oluşturulan verileri görüntüleme.

Bu öğreticide oluşturacağınız IoT Edge modülü, cihazınız tarafından oluşturulan sıcaklık verilerini filtreler. İletileri yalnızca sıcaklık belirtilen bir eşiğin üzerindeyse yukarı yönde gönderir. Bu tür bir analiz, buluta iletilen ve bulutta depolanan veri miktarını azaltmak için yararlıdır. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Çözüm kapsamı

Bu öğreticide, **Visual Studio 2019**kullanılarak **C** 'de bir modülün nasıl geliştirileceği ve bir **Windows cihazına**nasıl dağıtılacağı gösterilmektedir. Linux cihazları için modüller geliştiriyorsanız bunun yerine [Linux cihazları Için C IoT Edge modülü geliştirme](tutorial-c-module.md) bölümüne gidin. 

Windows cihazlarına C modülleri geliştirme ve dağıtmaya yönelik seçeneklerinizi anlamak için aşağıdaki tabloyu kullanın: 

| C | Visual Studio Code | Visual Studio 2017/2019 | 
| -- | ------------------ | ------------------ |
| **Windows AMD64** |  | ![Visual Studio 'da WinAMD64 için C modülleri geliştirme](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Ön koşullar

Bu öğreticiye başlamadan önce, Windows kapsayıcı geliştirmesi için geliştirme ortamınızı ayarlamak üzere önceki öğreticiden çıkmalısınız: [Windows cihazları için IoT Edge modüller](tutorial-develop-for-windows.md)geliştirme. Bu Öğreticiyi tamamladıktan sonra, aşağıdaki önkoşulların yerine gelmelidir: 

* Azure'da ücretsiz veya standart katman [IoT Hub'ı](../iot-hub/iot-hub-create-through-portal.md).
* [Azure IoT Edge çalıştıran bir Windows cihazı](quickstart.md).
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/)gibi bir kapsayıcı kayıt defteri.
* [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) [Azure IoT Edge araçları](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) uzantısıyla yapılandırıldı.
* Windows kapsayıcıları çalıştıracak şekilde yapılandırılmış [Docker Desktop](https://docs.docker.com/docker-for-windows/install/) .
* Vcpkg aracılığıyla Windows x64 için Azure IoT C SDK 'sını yükler:

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```
   
> [!TIP]
> Visual Studio 2017 (sürüm 15,7 veya üzeri) kullanıyorsanız, lütfen Visual Studio marketi 'nden VS 2017 için [Azure IoT Edge araçları](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) 'nı indirip yükleyin

## <a name="create-a-module-project"></a>Modül projesi oluşturma

Aşağıdaki adımlarda, Visual Studio ve Azure IoT Edge araçları uzantısı kullanılarak C SDK temel alınarak IoT Edge bir modül projesi oluşturulur. Bir proje şablonu oluşturduktan sonra, modülün iletileri kendilerine bildirilen özelliklerine göre filtreleyeceğini için yeni kod ekleyin. 

### <a name="create-a-new-project"></a>Yeni bir proje oluşturma

Kendi yazacağınız kodla özelleştirebileceğiniz bir C çözüm şablonu oluşturun.

1. Visual Studio 2019 ' u başlatın ve **Yeni proje oluştur**' u seçin.

2. Yeni proje penceresinde **IoT Edge** proje arayın ve **Azure IoT Edge (Windows amd64)** projesi seçin. **İleri**’ye tıklayın. 

   ![Yeni bir Azure IoT Edge projesi oluştur](./media/tutorial-c-module-windows/new-project.png)

3. Yeni projeyi yapılandırın penceresinde, projeyi ve çözümü **CTutorialApp**gibi açıklayıcı bir şekilde yeniden adlandırın. Projeyi oluşturmak için **Oluştur** ' a tıklayın. 

   ![Yeni bir Azure IoT Edge projesi yapılandırma](./media/tutorial-c-module-windows/configure-project.png)

4. IoT Edge uygulama ve modül penceresinde projenizi aşağıdaki değerlerle yapılandırın: 

   | Alan | Değer |
   | ----- | ----- |
   | Şablon seçin | **C modülünü**seçin. | 
   | Modül proje adı | Modülünüze **CModule** adını verin. | 
   | Docker görüntü deposu | Görüntü deposu, kapsayıcı kayıt defterinizin adını ve kapsayıcı görüntünüzün adını içerir. Kapsayıcı resminiz modül proje adı değerinden önceden doldurulur. **localhost:5000** yerine Azure kapsayıcı kayıt defterinizden alacağınız oturum açma sunucusu değerini yazın. Oturum açma sunucusunu Azure portalda kapsayıcı kayıt defterinizin Genel bakış sayfasından alabilirsiniz. <br><br> Son görüntü deposu, \<kayıt defteri adı\>. azurecr.io/cmodule gibi görünüyor. |

   ![Projenizi hedef cihaz, modül türü ve kapsayıcı kayıt defteri için yapılandırma](./media/tutorial-c-module-windows/add-application-and-module.png)

5. Projeyi oluşturmak için **Ekle** ' yi seçin. 

### <a name="add-your-registry-credentials"></a>Kayıt defteri kimlik bilgilerinizi ekleme

Dağıtım bildirimi, IoT Edge çalışma zamanına sahip kapsayıcı kayıt defteriniz için kimlik bilgilerini paylaşır. Çalışma zamanı, özel görüntülerinizi IoT Edge cihazına çekmek için bu kimlik bilgilerine ihtiyaç duyar. Azure Container Registry 'nizin **erişim anahtarları** bölümünden kimlik bilgilerini kullanın. 

1. Visual Studio Çözüm Gezgini ' nde **Deployment. Template. JSON** dosyasını açın. 

2. $EdgeAgent istenen özelliklerde **Registrycredentials** özelliğini bulun. 

3. Bu biçimi izleyerek özelliği kimlik bilgilerinizle güncelleştirin: 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

4. Deployment. Template. json dosyasını kaydedin. 

### <a name="update-the-module-with-custom-code"></a>Modülü özel kodla güncelleştirme

Varsayılan modül kodu bir giriş sırasındaki iletileri alır ve bunları bir çıkış kuyruğu aracılığıyla geçirir. Modülün iletileri IoT Hub iletmek için önce, daha fazla kod ekleyelim. Her iletideki sıcaklık verilerini analiz etmek için modülünü güncelleştirin ve yalnızca sıcaklığın belirli bir eşiği aşması durumunda iletiyi IoT Hub gönderir. 


1. Bu senaryoda sensörden alınan veriler JSON biçimindedir. JSON biçimindeki iletileri filtreleme amacıyla C için bir JSON kitaplığını içeri aktarın. Bu öğreticide Parson kullanılmıştır.

   1. [Parson GitHub deposunu](https://github.com/kgabis/parson)indirin. **Parson. c** ve **Parson. h** dosyalarını **cmodule** projesine kopyalayın.

   2. Visual Studio 'da CModule proje klasöründen **Cmakelists. txt** dosyasını açın. Dosyanın en üstünde Parson dosyalarını **my_parson** adlı bir kitaplık olarak içeri aktarın.

      ```
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. CMakeLists. txt dosyasının **target_link_libraries** bölümündeki kitaplıklar listesine `my_parson` ekleyin.

   4. **CMakeLists.txt** dosyasını kaydedin.

   5. **Cmodule** > **Main. c**' i açın. Include deyimlerinin listesinin en altında, JSON desteği için `parson.h` dahil etmek üzere yeni bir tane ekleyin:

      ```c
      #include "parson.h"
      ```

2.  **Main. c** dosyasında, messagesReceivedByInput1Queue değişkeninin yanına `temperatureThreshold` adlı bir genel değişken ekleyin. Bu değişken, IoT Hub'a veri gönderilmesi için ölçülen sıcaklığın aşması gereken değeri ayarlar.

    ```c
    static double temperatureThreshold = 25;
    ```

3. Main. c içinde `CreateMessageInstance` işlevini bulun. İç if-else ifadesini, birkaç işlev satırı ekleyen aşağıdaki kodla değiştirin: 

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

   Else deyimindeki kodun yeni satırları iletiye bir uyarı olarak etiketleyen yeni bir özellik ekler. Bu kod, tüm iletileri uyarı olarak etiketlediği için, yalnızca yüksek sıcaklıklar bildirdiklerinde IoT Hub ileti gönderen işlevler ekleyeceğiz. 

4. `InputQueue1Callback` işlevini bulun ve işlevin tamamını aşağıdaki kodla değiştirin. Bu işlev gerçek bir mesajlaşma filtresi uygular. Bir ileti alındığında, bildirilen sıcaklığın eşiği aşıp aşmadığını denetler. Yanıt Evet ise, iletiyi çıkış kuyruğu aracılığıyla iletir. Aksi takdirde, iletiyi yoksayar. 

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

        // Check if the message reports temperatures higher than the threshold
        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;

        // If temperature exceeds threshold, send to output1
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
        // If message does not exceed threshold, do not forward
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

5. `moduleTwinCallback` işlevi ekleyin. Bu yöntem modül ikizinin istenen özellikleri üzerinde yapılan güncelleştirmeleri alır ve **temperatureThreshold** değişkenini buna göre güncelleştirir. Tüm modüllerin, doğrudan buluttan bir modülün içinde çalışan kodu yapılandırmanıza izin veren kendi modül ikizi vardır.

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

6. `SetupCallbacksForModule` işlevini bulun. Bu işlevi, ikizi modülünün güncelleştirilip güncelleştirilmediğini denetlemek için bir **Else if** ifadesini ekleyen aşağıdaki kodla değiştirin. 

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

7. Main. c dosyasını kaydedin.

8. **Deployment. Template. JSON** dosyasını açın. 

9. Dağıtım bildirimine CModule modül ikizini ekleyin. Aşağıdaki JSON içeriğini `moduleContent` bölümünün en altına, `$edgeHub` modül ikizinin arkasına ekleyin:

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![Dağıtım şablonuna CModule ikizi ekleme](./media/tutorial-c-module-windows/module-twin.png)

1. **deployment.template.json** dosyasını kaydedin.

## <a name="build-and-push-your-module"></a>Modülünüzü derleyin ve gönderin

Önceki bölümde, bildirilen makine sıcaklığının kabul edilebilir eşiğin altında olduğu iletileri filtrelemek için bir IoT Edge çözümü oluşturdunuz ve **Cmodule** 'e kod eklediniz. Şimdi çözümü kapsayıcı görüntüsü olarak derlemeniz ve kapsayıcı kayıt defterine göndermeniz gerekiyor. 

1. Geliştirme makinenizde Docker 'da oturum açmak için aşağıdaki komutu kullanın. Azure Container Registry 'nizden Kullanıcı adı, parola ve oturum açma sunucusu ile oturum açın. Azure portal kayıt defterinizin **erişim tuşları** bölümünden bu değerleri alabilirsiniz.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   `--password-stdin`kullanımını öneren bir güvenlik uyarısı alabilirsiniz. Bu en iyi uygulama, üretim senaryolarında önerilse de, Bu öğreticinin kapsamı dışındadır. Daha fazla bilgi için bkz. [Docker oturum açma](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) başvurusu.

2. Visual Studio Çözüm Gezgini ' nde, derlemek istediğiniz proje adına sağ tıklayın. Varsayılan ad **AzureIotEdgeApp1** ' dir ve bir Windows modülü oluştururken, uzantı **Windows. amd64**olmalıdır. 

3. **Derleme ve gönderme IoT Edge modüllerini**seçin. 

   Build ve push komutu üç işlem başlatır. İlk olarak, **yapılandırma** adlı çözümde, dağıtım şablonunda bilgi ve diğer çözüm dosyalarını içeren tam dağıtım bildirimini tutan yeni bir klasör oluşturur. İkinci olarak, hedef mimariniz için uygun dockerfile 'ı temel alan kapsayıcı görüntüsünü oluşturmak için `docker build` çalıştırır. Sonra, görüntü deposunu kapsayıcı Kayıt defterinize göndermek için `docker push` çalıştırır. 

## <a name="deploy-modules-to-device"></a>Modülleri cihaza dağıt

Modül projesini IoT Edge cihazınıza dağıtmak için Visual Studio Cloud Explorer ve Azure IoT Edge araçları uzantısını kullanın. Zaten senaryonuz için hazırlanan bir dağıtım bildiriminiz var, yapılandırma klasöründeki **Deployment. JSON** dosyası. Tek yapmanız gereken dağıtımı almak üzere bir cihaz seçmek.

IoT Edge cihazınızın çalışır ve çalışıyor olduğundan emin olun. 

1. Visual Studio Cloud Explorer 'da IoT cihazlarınızın listesini görmek için kaynakları genişletin. 

2. Dağıtımı almak istediğiniz IoT Edge cihazının adına sağ tıklayın. 

3. **Dağıtım oluştur**' u seçin.

4. Dosya Gezgini 'nde, çözümünüzün yapılandırma klasöründe **Deployment. Windows-AMD64** dosyasını seçin. 

5. Cihazınızın altında listelenen dağıtılan modülleri görmek için Cloud Explorer 'ı yenileyin. 


## <a name="view-generated-data"></a>Oluşturulan verileri görüntüleme

Dağıtım bildirimini IoT Edge cihazınıza uyguladıktan sonra cihazdaki IoT Edge çalışma zamanı yeni dağıtım bilgilerini toplar ve yürütmeye başlar. Cihazda çalışan ve dağıtım bildiriminde bulunmayan modüller durdurulur. Cihazda eksik olan modüller başlatılır. 

İletileri IoT Hub geldikçe görüntülemek için IoT Edge araçları uzantısını kullanabilirsiniz. 

1. Visual Studio Cloud Explorer 'da IoT Edge cihazınızın adını seçin. 

2. **Eylemler** listesinde, **Izlemeyi Başlat yerleşik olay uç noktası**' nı seçin. 

3. IoT Hub gelen iletileri görüntüleyin. IoT Edge cihazın yeni dağıtımını alması ve tüm modülleri başlatması gerektiğinden, iletilerin gelmesi biraz zaman alabilir. Ardından, CModule kodunda yaptığımız değişiklikler, makine sıcaklığının İleti göndermeden önce 25 dereceye ulaşması bitinceye kadar bekler. Ayrıca, bu sıcaklık eşiğine ulaşan iletilere ileti türü **uyarısını** ekler. 

   ![IoT Hub gelen iletileri görüntüle](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>İkizi modülünü düzenleme

Sıcaklık eşiğini 25 derece ayarlamak için CModule modülünü ikizi kullandık. Modül kodunu güncelleştirmek zorunda kalmadan işlevselliği değiştirmek için ikizi modülünü kullanabilirsiniz.

1. Visual Studio 'da **Deployment. Windows-AMD64. JSON** dosyasını açın. (Deployment. Template dosyası değil. Çözüm Gezgini 'nde yapılandırma dosyasında dağıtım bildirimini görmüyorsanız gezgin araç çubuğunda **tüm dosyaları göster** simgesini seçin.)

2. CModule ikizi bulun ve **temperatureThreshold** parametresinin değerini, en son bildirilen sıcaklığın üzerinde olan 10 derece daha yüksek olacak şekilde değiştirin. 

3. **Deployment. Windows-AMD64. JSON** dosyasını kaydedin.

4. Güncelleştirilmiş dağıtım bildirimini cihazınıza uygulamak için dağıtım adımlarını yeniden izleyin. 

5. Gelen cihazdan buluta iletileri izleyin. Yeni sıcaklık eşiğine ulaşılana kadar iletilerin durulabileceğini görmeniz gerekir. 

## <a name="clean-up-resources"></a>Kaynakları temizleme 

Bir sonraki önerilen makaleye geçmeyi planlıyorsanız, oluşturduğunuz kaynaklarla yapılandırmaları tutabilir ve yeniden kullanabilirsiniz. Aynı IoT Edge cihazını test cihazı olarak kullanmaya devam edebilirsiniz. 

Aksi takdirde, ücretlerden kaçınmak için bu makalede kullandığınız yerel konfigürasyonları ve Azure kaynaklarını silebilirsiniz. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide IoT Edge cihazınız tarafından üretilen ham verileri filtreleme kodunu içeren bir IoT Edge modülü oluşturdunuz. Kendi modüllerinizi oluşturmaya hazırsanız, [kendi IoT Edge modüllerinizi geliştirme](module-development.md) veya [Visual Studio ile modül geliştirme](how-to-visual-studio-develop-module.md)hakkında daha fazla bilgi edinebilirsiniz. Azure IoT Edge bir sonraki öğreticilere devam ederek, verileri kenarda işlemek ve analiz etmek için Azure Cloud Services 'ı dağıtmanıza nasıl yardımcı olabileceğini öğrenebilirsiniz.

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
