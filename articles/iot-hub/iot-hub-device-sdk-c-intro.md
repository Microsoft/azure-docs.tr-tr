---
title: C için Azure IoT cihaz SDK 'Sı | Microsoft Docs
description: C için Azure IoT cihaz SDK 'sını kullanmaya başlayın ve bir IoT Hub ile iletişim kuran cihaz uygulamaları oluşturmayı öğrenin.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: robinsh
ms.openlocfilehash: dd12f974b9b02d919752dcb932c9ce1709d7315b
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813782"
---
# <a name="azure-iot-device-sdk-for-c"></a>C için Azure IoT cihaz SDK 'Sı

**Azure IoT cihaz SDK 'sı** , **Azure IoT Hub** hizmetine ileti gönderme ve iletileri alma sürecini basitleştirmek için tasarlanan bir kitaplıklar kümesidir. Her biri belirli bir platformu hedefleyen SDK 'nın farklı çeşitlemeleri vardır, ancak bu makalede **C Için Azure IoT cihaz SDK 'sı**açıklanmaktadır.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

C için Azure IoT cihaz SDK 'Sı, taşınabilirliği en üst düzeye çıkarmak için ANSI C 'de (C99) yazılmıştır. Bu özellik, kitaplıkların birden çok platformda ve cihazlarda çalışmaya uygun olmasını sağlar, özellikle de disk ve bellek parmak İZLİĞİ en aza indirmeli.

SDK 'nın test edilmiş çok çeşitli platformlar vardır (Ayrıntılar için bkz. [IoT Için Azure Sertifikalı cihaz kataloğuna](https://catalog.azureiotsolutions.com/) bakın). Bu makalede, Windows platformunda çalışan örnek koda ilişkin izlenecek yollar bulunmasına rağmen, bu makalede açıklanan kod Desteklenen platformların aralığı genelinde aynıdır.

Aşağıdaki videoda, C için Azure IoT SDK 'ya genel bakış sunulmaktadır:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-IoT-C-SDK-insights/Player]

Bu makalede, C için Azure IoT cihaz SDK 'sının mimarisi açıklanır. Cihaz Kitaplığı başlatma, IoT Hub veri gönderme ve bundan ileti alma işlemlerinin nasıl yapılacağını gösterir. Bu makaledeki bilgiler SDK 'yı kullanmaya başlamak için yeterince olmalıdır, ancak kitaplıklar hakkında ek bilgilere işaretçiler de sağlar.

## <a name="sdk-architecture"></a>SDK mimarisi

C GitHub deposu [**Için Azure IoT CIHAZ SDK**](https://github.com/Azure/azure-iot-sdk-c) 'sını bulabilir ve [c API başvurusunda](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)API 'nin ayrıntılarını görüntüleyebilirsiniz.

Kitaplıkların en son sürümü deponun **ana** dalında bulunabilir:

  ![Deponun ana dalının ekran görüntüsü](./media/iot-hub-device-sdk-c-intro/RepoMasterBranch.png)

* SDK 'nın çekirdek uygulanması, SDK 'da en düşük API katmanının uygulamasını içeren **\_ıothub istemci** klasöründedir: **iothubclient** kitaplığı. **Iothubclient** kitaplığı, IoT Hub ileti göndermek için ham mesajlaşma uygulayan API 'leri ve IoT Hub iletileri almak Için API 'ler içerir. Bu kitaplığı kullanırken, ileti serileştirme uygulamaktan siz sorumlusunuz, ancak IoT Hub ile iletişim kurmanın diğer ayrıntıları sizin için işlenir.

* **Seri hale getirici** klasörü, istemci kitaplığını kullanarak Azure IoT Hub göndermeden önce verileri serileştirmek için yardımcı işlevleri ve örnekleri içerir. Seri hale getirici kullanımı zorunlu değildir ve kolaylık olarak sağlanır. **Seri hale getirici** kitaplığını kullanmak için, IoT Hub gönderileceği verileri ve bundan elde etmek istediğiniz iletileri belirten bir model tanımlarsınız. Model tanımlandıktan sonra SDK, serileştirme ayrıntıları hakkında endişelenmeden cihazdan buluta ve buluttan cihaza iletilerle kolayca çalışmanızı sağlayan bir API yüzeyi sağlar. Kitaplık, MQTT ve AMQP gibi protokolleri kullanarak Aktarım uygulayan diğer açık kaynak kitaplıklarına bağlıdır.

* **Iothubclient** kitaplığı diğer açık kaynak kitaplıklarına bağımlıdır:

  * Azure ile ilgili birçok C SDK 'Sı içinde gerekli olan temel görevler (dizeler, liste işleme ve GÇ gibi) için ortak işlevsellik sağlayan [Azure C Shared Utility](https://github.com/Azure/azure-c-shared-utility) kitaplığı.

  * Kaynak kısıtlı cihazlar için optimize edilmiş bir istemci tarafı uygulama olan [Azure uAMQP](https://github.com/Azure/azure-uamqp-c) kitaplığı.

  * MQTT protokolünü uygulayan ve kaynak kısıtlı cihazlar için optimize edilmiş genel amaçlı bir kitaplık olan [Azure uMQTT](https://github.com/Azure/azure-umqtt-c) kitaplığı.

Örnek koda bakarak bu kitaplıkların kullanımı daha kolay anlaşılır. Aşağıdaki bölümlerde, SDK 'ya dahil edilen örnek uygulamalarda birkaç adım gösterilmektedir. Bu izlenecek yol, SDK 'nın mimari katmanlarının çeşitli özellikleri ve API 'lerin nasıl çalıştığı hakkında bir giriş yapmanızı sağlar.

## <a name="before-you-run-the-samples"></a>Örnekleri çalıştırmadan önce

C için Azure IoT cihaz SDK 'sında örnekleri çalıştırmadan önce Azure aboneliğinizde [IoT Hub hizmetinin bir örneğini oluşturmanız](iot-hub-create-through-portal.md) gerekir. Ardından aşağıdaki görevleri tamamlayabilirsiniz:

* Geliştirme ortamınızı hazırlama
* Cihaz kimlik bilgilerini alın.

### <a name="prepare-your-development-environment"></a>Geliştirme ortamınızı hazırlama

Paketler, yaygın platformlar (örneğin, Windows için NuGet veya Deapt_get ve Ubuntu) için sağlanır ve örnekler bu paketleri kullanılabilir olduğunda kullanır. Bazı durumlarda, veya cihazınızda SDK 'Yı derlemeniz gerekir. SDK 'Yı derlemeniz gerekiyorsa, bkz. GitHub deposunda [geliştirme ortamınızı hazırlama](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) .

Örnek uygulama kodunu almak için, GitHub 'dan SDK 'nın bir kopyasını indirin. Kaynak kopyanızı [GitHub deposunun](https://github.com/Azure/azure-iot-sdk-c) **ana** dalından alın.


### <a name="obtain-the-device-credentials"></a>Cihaz kimlik bilgilerini alma

Örnek kaynak koduna sahip olduğunuza göre, bir sonraki şey bir cihaz kimlik bilgileri kümesi almak için kullanılır. Bir cihazın bir IoT Hub 'ına erişebilmesi için önce cihazı IoT Hub Identity kayıt defterine eklemeniz gerekir. Cihazınızı eklediğinizde, cihazın IoT Hub 'ına bağlanabilmesi için gereken bir cihaz kimlik bilgileri kümesi alırsınız. Sonraki bölümde ele alınan örnek uygulamalar bu kimlik bilgilerinin bir **Cihaz bağlantı dizesi**biçiminde olmasını bekler.

IoT Hub 'ınızı yönetmenize yardımcı olacak birkaç açık kaynak aracı vardır.

* [Cihaz Gezgini](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer)adlı bir Windows uygulaması.

* [Azure IoT araçları](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)adlı platformlar arası Visual Studio Code uzantısı.

* [Azure CLI Için IoT uzantısı](https://github.com/Azure/azure-iot-cli-extension)adlı platformlar arası Python CLI.

Bu öğretici, grafik *cihaz Gezgini* aracını kullanır. VS Code geliştirirseniz *vs Code Için Azure IoT araçları* 'nı kullanabilirsiniz. CLı aracını kullanmayı tercih ediyorsanız, *Azure clı 2,0 Için IoT uzantısı* 'nı da kullanabilirsiniz.

Cihaz Gezgini Aracı, IoT Hub cihaz ekleme dahil olmak üzere çeşitli işlevleri gerçekleştirmek için Azure IoT hizmeti kitaplıklarını kullanır. Cihaz eklemek için cihaz Gezgini aracını kullanırsanız, cihazınız için bir bağlantı dizesi alırsınız. Örnek uygulamaları çalıştırmak için bu bağlantı dizesine ihtiyacınız vardır.

Cihaz Gezgini aracı hakkında bilginiz yoksa, aşağıdaki yordamda bir cihaz eklemek ve bir cihaz bağlantı dizesi almak için nasıl kullanılacağı açıklanmaktadır.

1. Cihaz Gezgini aracını yüklemek için, bkz. [IoT Hub cihazlar için Device Explorer kullanma](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer).

1. Programı çalıştırdığınızda şu arabirimi görürsünüz:

   ![Device Explorer Ikizi ekran görüntüsü](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinConfigTab.png)

1. **IoT Hub bağlantı dizenizi** ilk alana girin ve **Güncelleştir**' e tıklayın. Bu adım, aracı IoT Hub ile iletişim kurabilmesi için yapılandırır. 

**Bağlantı dizesi** , **IoT Hub hizmet** > **ayarları** > **paylaşılan erişim ilkesi** > **ıthubowner**altında bulunabilir.

1. IoT Hub bağlantı dizesi yapılandırıldığında, **Yönetim** sekmesine tıklayın:

   ![Device Explorer Ikizi/yönetim ekran görüntüsü](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab.png)

Bu sekme, IoT Hub 'ınıza kayıtlı cihazları yönettiğiniz yerdir.

1. **Oluştur** düğmesine tıklayarak bir cihaz oluşturursunuz. Önceden doldurulmuş anahtarlar kümesi (birincil ve ikincil) içeren bir iletişim kutusu görüntülenir. Bir **CIHAZ kimliği** girin ve ardından **Oluştur**' a tıklayın.

   ![Cihaz oluştur ekran görüntüsü](./media/iot-hub-device-sdk-c-intro/CreateDevice.png)

1. Cihaz oluşturulduğunda, cihazlar, yeni oluşturduğunuz cihaz da dahil olmak üzere tüm kayıtlı cihazlarla güncellenir. Yeni cihazınıza sağ tıklarsanız şu menüyü görürsünüz:

   ![Device Explorer Ikizi sağ tıklama sonucu](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab_RightClick.png)

1. **Seçili cihaz için bağlantı dizesini Kopyala**' yı seçerseniz, cihaz bağlantı dizesi panoya kopyalanır. Cihaz bağlantı dizesinin bir kopyasını saklayın. Aşağıdaki bölümlerde açıklanan örnek uygulamaları çalıştırırken bu gereklidir.

Yukarıdaki adımları tamamladığınızda, bazı kodları çalıştırmaya başlamaya hazırsınız demektir. Çoğu örnek, ana kaynak dosyanın en üstünde bir bağlantı dizesi girmenizi sağlayan bir sabit değerdir. Örneğin, **\_iothub_client Samples\_iothub_convenience_sample** uygulamasındaki karşılık gelen satır aşağıdaki gibi görünür.

```c
static const char* connectionString = "[device connection string]";
```

## <a name="use-the-iothubclient-library"></a>IoTHubClient kitaplığını kullanma

[Azure-IoT-SDK-c](https://github.com/azure/azure-iot-sdk-c) deposundaki **ıothub\_istemci** klasörü içinde, **ıothub\_istemci\_örneği\_MQTT adlı bir uygulama içeren bir örnekler klasörü vardır** .

**İothub_client\_Samples\_iothub_convenience_sample** uygulamasının Windows sürümü aşağıdaki Visual Studio çözümünü içerir:

  ![Visual Studio Çözüm Gezgini](./media/iot-hub-device-sdk-c-intro/iothub-client-sample-mqtt.png)

> [!NOTE]
> Visual Studio, projeyi en son sürüme yeniden hedeflemeniz isterse, istemi kabul edin.

Bu çözüm tek bir proje içerir. Bu çözüme yüklenen dört NuGet paketi vardır:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft. Azure. ıothub. IoTHubClient
* Microsoft.Azure.umqtt

SDK ile çalışırken her zaman **Microsoft. Azure. C. Sharedusystemutility** paketi gerekir. Bu örnek MQTT protokolünü kullanır, bu nedenle **Microsoft. Azure. umqtt** ve **Microsoft. Azure. ıothub. mqtttransport** paketlerini (AMQP ve https için eşdeğer paketler vardır) eklemeniz gerekir. Örnek **iothubclient** kitaplığını kullandığından, çözümünüze **Microsoft. Azure. ıothub. iothubclient** paketini de eklemeniz gerekir.

Örnek uygulama için uygulamayı **\_iothub_client Samples\_iothub_convenience_sample** kaynak dosyasında bulabilirsiniz.

Aşağıdaki adımlarda, **Iothubclient** kitaplığını kullanmak için gereken özellikler konusunda size yol gösterecek bu örnek uygulama kullanılır.

### <a name="initialize-the-library"></a>Kitaplığı Başlat

> [!NOTE]
> Kitaplıklarla çalışmaya başlamadan önce platforma özgü bazı başlatma işlemleri yapmanız gerekebilir. Örneğin, Linux üzerinde AMQP kullanmayı planlıyorsanız, OpenSSL kitaplığını başlatmalısınız. [GitHub deposundaki](https://github.com/Azure/azure-iot-sdk-c) örnekler, istemci başlatıldığında yardımcı program işlevi **Platform\_init** ' i çağırır ve çıkmadan önce **Platform\_deinit** işlevini çağırır. Bu işlevler, platform. h üstbilgi dosyasında bildirilmiştir. İstemciye platforma özgü herhangi bir başlatma kodu eklemeniz gerekip gerekmediğini öğrenmek için [depodaki](https://github.com/Azure/azure-iot-sdk-c) hedef platformunuz için bu işlevlerin tanımlarını inceleyin.

Kitaplıklarla çalışmaya başlamak için önce bir IoT Hub istemci tutamacı ayırın:

```c
if ((iotHubClientHandle = 
  IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol)) == NULL)
{
    (void)printf("ERROR: iotHubClientHandle is NULL!\r\n");
}
else
{
    ...
```

Bu işleve cihaz Gezgini aracından edindiğiniz cihaz bağlantı dizesinin bir kopyasını geçirirsiniz. Ayrıca kullanılacak iletişim protokolünü belirlersiniz. Bu örnek MQTT kullanır, ancak AMQP ve HTTPS de seçenekleridir.

Geçerli bir **ıothub\_istemci\_tutamacına**sahip olduğunuzda, IoT Hub iletileri almak ve bu bilgisayardan ileti göndermek için API 'leri çağırmaya başlayabilirsiniz.

### <a name="send-messages"></a>İleti gönderme

Örnek uygulama, IoT Hub 'ınıza ileti göndermek için bir döngü kurar. Aşağıdaki kod parçacığı:

- Bir ileti oluşturur.
- İletiye bir özellik ekler.
- Bir ileti gönderir.

İlk olarak, bir ileti oluşturun:

```c
size_t iterator = 0;
do
{
    if (iterator < MESSAGE_COUNT)
    {
        sprintf_s(msgText, sizeof(msgText), "{\"deviceId\":\"myFirstDevice\",\"windSpeed\":%.2f}", avgWindSpeed + (rand() % 4 + 2));
        if ((messages[iterator].messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText))) == NULL)
        {
            (void)printf("ERROR: iotHubMessageHandle is NULL!\r\n");
        }
        else
        {
            messages[iterator].messageTrackingId = iterator;
            MAP_HANDLE propMap = IoTHubMessage_Properties(messages[iterator].messageHandle);
            (void)sprintf_s(propText, sizeof(propText), "PropMsg_%zu", iterator);
            if (Map_AddOrUpdate(propMap, "PropName", propText) != MAP_OK)
            {
                (void)printf("ERROR: Map_AddOrUpdate Failed!\r\n");
            }

            if (IoTHubClient_LL_SendEventAsync(iotHubClientHandle, messages[iterator].messageHandle, SendConfirmationCallback, &messages[iterator]) != IOTHUB_CLIENT_OK)
            {
                (void)printf("ERROR: IoTHubClient_LL_SendEventAsync..........FAILED!\r\n");
            }
            else
            {
                (void)printf("IoTHubClient_LL_SendEventAsync accepted message [%d] for transmission to IoT Hub.\r\n", (int)iterator);
            }
        }
    }
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1);

    iterator++;
} while (g_continueRunning);
```

Her ileti gönderdiğinizde, veriler gönderildiğinde çağrılan geri çağırma işlevine yönelik bir başvuru belirtirsiniz. Bu örnekte, geri çağırma işlevi **SendConfirmationCallback**olarak adlandırılır. Aşağıdaki kod parçacığında bu geri çağırma işlevi gösterilmektedir:

```c
static void SendConfirmationCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    EVENT_INSTANCE* eventInstance = (EVENT_INSTANCE*)userContextCallback;
    (void)printf("Confirmation[%d] received for message tracking id = %zu with result = %s\r\n", callbackCounter, eventInstance->messageTrackingId, MU_ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
    /* Some device specific action code goes here... */
    callbackCounter++;
    IoTHubMessage_Destroy(eventInstance->messageHandle);
}
```

İleti ile işiniz bittiğinde **\_iothubmessage Destroy** işlevine yapılan çağrıyı aklınızda bulabilirsiniz. Bu işlev, iletiyi oluştururken ayrılan kaynakları serbest bırakır.

### <a name="receive-messages"></a>İleti alma

İleti alınması zaman uyumsuz bir işlemdir. İlk olarak, cihaz bir ileti aldığında çağrılacak geri aramayı kaydedersiniz:

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext) != IOTHUB_CLIENT_OK)
{
    (void)printf("ERROR: IoTHubClient_LL_SetMessageCallback..........FAILED!\r\n");
}
else
{
    (void)printf("IoTHubClient_LL_SetMessageCallback...successful.\r\n");
    ...
```

Son parametre, istediğiniz her türlü void işaretçisidir. Örnekte, bir tam sayı işaretçisi, ancak daha karmaşık bir veri yapısına yönelik bir işaretçi olabilir. Bu parametre, geri çağırma işlevinin bu işlevin çağıranı ile paylaşılan durumda çalışmasını sağlar.

Cihaz bir ileti aldığında, kayıtlı geri arama işlevi çağrılır. Bu geri arama işlevi şunu alır:

* İletiden gelen ileti kimliği ve bağıntı kimliği.
* İleti içeriği.
* İletideki özel özellikler.

```c
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    int* counter = (int*)userContextCallback;
    const char* buffer;
    size_t size;
    MAP_HANDLE mapProperties;
    const char* messageId;
    const char* correlationId;

    // Message properties
    if ((messageId = IoTHubMessage_GetMessageId(message)) == NULL)
    {
        messageId = "<null>";
    }

    if ((correlationId = IoTHubMessage_GetCorrelationId(message)) == NULL)
    {
        correlationId = "<null>";
    }

    // Message content
    if (IoTHubMessage_GetByteArray(message, (const unsigned char**)&buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        (void)printf("unable to retrieve the message data\r\n");
    }
    else
    {
        (void)printf("Received Message [%d]\r\n Message ID: %s\r\n Correlation ID: %s\r\n Data: <<<%.*s>>> & Size=%d\r\n", *counter, messageId, correlationId, (int)size, buffer, (int)size);
        // If we receive the work 'quit' then we stop running
        if (size == (strlen("quit") * sizeof(char)) && memcmp(buffer, "quit", size) == 0)
        {
            g_continueRunning = false;
        }
    }

    // Retrieve properties from the message
    mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                size_t index;

                printf(" Message Properties:\r\n");
                for (index = 0; index < propertyCount; index++)
                {
                    (void)printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                (void)printf("\r\n");
            }
        }
    }

    /* Some device specific action code goes here... */
    (*counter)++;
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Bu örnekte bir dize olan iletiyi almak için **iothubmessage\_getbytearray** işlevini kullanın.

### <a name="uninitialize-the-library"></a>Kitaplığı Uninitialize

Olayları göndermeyi ve iletileri almayı bitirdiğinizde IoT kitaplığı Uninitialize sağlayabilirsiniz. Bunu yapmak için aşağıdaki işlev çağrısını yayınlayın:

```c
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Bu çağrı, **ıothubclient\_createfromconnectionstring** işlevi tarafından daha önce ayrılan kaynakları boşaltır.

Gördüğünüz gibi, **Iothubclient** kitaplığı ile ileti göndermek ve almak kolaydır. Kitaplığı, hangi protokolün kullanılacağı de dahil olmak üzere IoT Hub ile iletişim kurma ayrıntılarını işler (Geliştirici açısından, bu basit bir yapılandırma seçeneğidir).

**Iothubclient** kitaplığı, cihazınızın IoT Hub gönderdiği verileri serileştirmek için de kesin denetim sağlar. Bazı durumlarda bu denetim düzeyi avantajlıdır, ancak diğer bir deyişle, bununla ilgilenmenizi istemediğiniz bir uygulama ayrıntılarıdır. Bu durumda, sonraki bölümde açıklanan **serileştirici** kitaplığını kullanmayı düşünebilirsiniz.

## <a name="use-the-serializer-library"></a>Seri hale getirici kitaplığını kullanma

Kavramsal olarak **serileştirici** KITAPLıĞı, SDK 'Daki **Iothubclient** kitaplığının üzerinde bulunur. IoT Hub ile temel alınan iletişim için **Iothubclient** kitaplığını kullanır, ancak geliştiriciden ileti serileştirme ile ilgilenme yükünü kaldırmak için modelleme özellikleri ekler. Bu kitaplığın çalışması bir örnek tarafından en iyi şekilde gösterilmiştir.

[Azure-IoT-SDK-c deposundaki](https://github.com/Azure/azure-iot-sdk-c) **seri hale getirici** klasörü içinde, **SimpleSample\_MQTT**adlı bir uygulama içeren bir **Samples** klasörüdür. Bu örneğin Windows sürümü, aşağıdaki Visual Studio çözümünü içerir:

  ![MQTT için Visual Studio çözümü örneği](./media/iot-hub-device-sdk-c-intro/simplesample_mqtt.png)

> [!NOTE]
> Visual Studio, projeyi en son sürüme yeniden hedeflemeniz isterse, istemi kabul edin.

Önceki örnekte olduğu gibi, bu, çeşitli NuGet paketlerini de içerir:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft. Azure. ıothub. IoTHubClient
* Microsoft. Azure. ıothub. Serializer
* Microsoft.Azure.umqtt

Bu paketlerin çoğunu önceki örnekte gördünüz, ancak **Microsoft. Azure. ıothub. Serializer** yenidir. **Seri hale getirici** kitaplığını kullandığınızda bu paket gereklidir.

Örnek uygulamanın uygulamasını **\_iothub_client Samples\_iothub_convenience_sample** dosyasında bulabilirsiniz.

Aşağıdaki bölümler, bu örneğin önemli bölümlerinde size yol gösterir.

### <a name="initialize-the-library"></a>Kitaplığı Başlat

**Serileştirici** kitaplığıyla çalışmaya başlamak Için başlatma API 'lerini çağırın:

```c
if (serializer_init(NULL) != SERIALIZER_OK)
{
    (void)printf("Failed on serializer_init\r\n");
}
else
{
    IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle = IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol);
    srand((unsigned int)time(NULL));
    int avgWindSpeed = 10;

    if (iotHubClientHandle == NULL)
    {
        (void)printf("Failed on IoTHubClient_LL_Create\r\n");
    }
    else
    {
        ContosoAnemometer* myWeather = CREATE_MODEL_INSTANCE(WeatherStation, ContosoAnemometer);
        if (myWeather == NULL)
        {
            (void)printf("Failed on CREATE_MODEL_INSTANCE\r\n");
        }
        else
        {
...
```

**\_Serileştirici init** işlevine yapılan çağrı bir kerelik çağrıdır ve temel alınan kitaplığı başlatır. Ardından, iothubclient **\_ll\_createfromconnectionstring** işlevini çağırarak, **ıothubclient** örneğindeki ile aynı API 'dir. Bu çağrı, cihaz Bağlantı dizenizi ayarlar (Bu çağrı, kullanmak istediğiniz protokolü de seçersiniz). Bu örnek, taşıma olarak MQTT kullanır, ancak AMQP veya HTTPS kullanabilir.

Son olarak, **\_model örneği\_oluştur** işlevini çağırın. **Dalgalı istasyon** , modelin ad alanıdır ve **Contosoanemometer** modelin adıdır. Model örneği oluşturulduktan sonra, ileti göndermeye ve almaya başlamak için kullanabilirsiniz. Ancak, bir modelin ne olduğunu anlamak önemlidir.

### <a name="define-the-model"></a>Modeli tanımlama

**Seri hale getirici** kitaplığındaki bir model, cihazınızın IoT Hub gönderebileceği iletileri ve bu iletilerin alabileceği modelleme dilinde *Eylemler* olarak adlandırılan iletileri tanımlar. **İothub_client\_Samples\_iothub_convenience_sample** örnek uygulamasında olduğu gibi bir C makroları kümesini kullanarak bir model tanımlarsınız:

```c
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(int, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

**BEGIN\_ad** alanı ve **End\_Namespace** makroları her ikisi de modelin ad alanını bağımsız değişken olarak alır. Bu makrolar arasındaki herhangi bir şeyin modelinizin veya modellerinizin tanımı ve modellerin kullandığı veri yapıları olması beklenir.

Bu örnekte, **Contosoanemometer**adlı tek bir model vardır. Bu model, cihazınızın IoT Hub gönderebileceği iki veri parçasını tanımlar: **DeviceID** ve **wınte hızı**. Ayrıca, cihazınızın alabileceği üç eylem (ileti) de tanımlar: **Turnfanon**, **turnfanın**ve **setaırdiri**. Her veri öğesinin bir türü vardır ve her eylem bir ada (ve isteğe bağlı olarak bir parametre kümesine) sahiptir.

Modelde tanımlanan veriler ve Eylemler, IoT Hub ileti göndermek ve cihaza gönderilen iletilere yanıt vermek için kullanabileceğiniz bir API yüzeyi tanımlar. Bu modelin kullanımı en iyi bir örnek aracılığıyla anlaşıladır.

### <a name="send-messages"></a>İleti gönderme

Model, IoT Hub gönderebilmeniz için verileri tanımlar. Bu örnekte, **WITH_DATA** makrosu kullanılarak tanımlanan iki veri öğesinden biri anlamına gelir. Bir IoT Hub 'ına **DeviceID** ve **WINI hızı** değerlerini göndermek için birkaç adım gereklidir. İlk olarak, göndermek istediğiniz verileri ayarlamanız gerekir:

```c
myWeather->DeviceId = "myFirstDevice";
myWeather->WindSpeed = avgWindSpeed + (rand() % 4 + 2);
```

Daha önce tanımladığınız model, bir **yapının**üyelerini ayarlayarak değerleri ayarlamanıza olanak sağlar. Sonra, göndermek istediğiniz iletiyi serileştirin:

```c
unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, myWeather->DeviceId, myWeather->WindSpeed) != CODEFIRST_OK)
{
    (void)printf("Failed to serialize\r\n");
}
else
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
    free(destination);
}
```

Bu kod, cihazdan buluta bir arabelleğe ( **hedefe**göre başvurulur) seri hale getirir. Kod daha sonra iletiyi IoT Hub göndermek için **SendMessage** işlevini çağırır:

```c
static void sendMessage(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle == NULL)
    {
        printf("unable to create a new IoTHubMessage\r\n");
    }
    else
    {
        if (IoTHubClient_LL_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId) != IOTHUB_CLIENT_OK)
        {
            printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
            printf("IoTHubClient accepted the message for delivery\r\n");
        }
        IoTHubMessage_Destroy(messageHandle);
    }
    messageTrackingId++;
}
```

**İothubclient\_\_ll SendEventAsync** 'ın ikinci son parametresi, veriler başarıyla gönderildiğinde çağrılan bir geri çağırma işlevine başvurudur. Örnekteki geri çağırma işlevi aşağıdadır:

```c
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    unsigned int messageTrackingId = (unsigned int)(uintptr_t)userContextCallback;

    (void)printf("Message Id: %u Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", MU_ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

İkinci parametre, Kullanıcı bağlamı için bir işaretçidir; **iothubclient\_\_ll SendEventAsync**'e aynı işaretçi geçirildi. Bu durumda, bağlam basit bir sayaçtır, ancak istediğiniz herhangi bir şey olabilir.

Cihazdan buluta iletileri göndermek için bu şey vardır. Yalnızca kapsayan tek şey ileti alma.

### <a name="receive-messages"></a>İleti alma

İleti alma, iletilerin **Iothubclient** kitaplığı 'nda çalışma yöntemine benzer şekilde çalışır. İlk olarak, bir ileti geri çağırma işlevi kaydedersiniz:

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, 
  IoTHubMessage, myWeather) != IOTHUB_CLIENT_OK)
{
    printf("unable to IoTHubClient_SetMessageCallback\r\n");
}
else
{
...
```

Daha sonra, bir ileti alındığında çağrılan geri çağırma işlevini yazarsınız:

```c
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = IOTHUBMESSAGE_ABANDONED;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = IOTHUBMESSAGE_ABANDONED;
        }
        else
        {
            (void)memcpy(temp, buffer, size);
            temp[size] = '\0';
            EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
            result =
                (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
                (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
                IOTHUBMESSAGE_REJECTED;
            free(temp);
        }
    }
    return result;
}
```

Bu kod ortak olduğundan, her çözüm için de aynıdır. Bu işlev iletiyi alır ve **yürütme\_komutu**çağrısı aracılığıyla bunu uygun işleve yönlendirmenizi sağlar. Bu noktada çağrılan işlev, modelinizdeki eylemlerin tanımına bağlıdır.

Modelinizde bir eylem tanımladığınızda, cihazınız ilgili iletiyi aldığında çağrılan bir işlevi uygulamanız gerekir. Örneğin, modeliniz bu eylemi tanımlıyorsa:

```c
WITH_ACTION(SetAirResistance, int, Position)
```

Şu imzaya sahip bir işlev tanımlayın:

```c
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

İşlevin adının modeldeki eylemin adı ile nasıl eşleştiğini ve işlevin parametrelerinin eylem için belirtilen parametrelerle eşleşip eşleşmediğini unutmayın. İlk parametre her zaman gereklidir ve modelinizin örneğine bir işaretçi içerir.

Cihaz bu imzayla eşleşen bir ileti aldığında, karşılık gelen işlev çağrılır. Bu nedenle, **ıthubmessage**' den ortak kod dahil etmek zorunda kalmadan, ileti alma yalnızca modelinizde tanımlanan her bir eylem için basit bir işlev tanımlamanın bir önemi olur.

### <a name="uninitialize-the-library"></a>Kitaplığı Uninitialize

Veri göndermeyi ve iletileri almayı bitirdiğinizde IoT kitaplığı Uninitialize sağlayabilirsiniz:

```c
...
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_LL_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

Bu üç işlevden her biri, daha önce açıklanan üç başlatma işlevi ile hizalanır. Bu API 'Leri çağırmak, daha önce ayrılan kaynakları boşaltmanızı sağlar.

## <a name="next-steps"></a>Sonraki Adımlar

Bu makalede, **C Için Azure IoT cihaz SDK 'sında**kitaplıkları kullanmanın temelleri ele alınmıştır. SDK 'ya nelerin dahil edildiğini, mimarisini ve Windows örnekleri ile çalışmaya nasıl başladığınızı anlamak için yeterli bilgi sağlamaktadır. Sonraki makalede, [IoTHubClient Kitaplığı hakkında daha fazla bilgi](iot-hub-device-sdk-c-iothubclient.md)vererek SDK 'nın açıklaması devam etmektedir.

IoT Hub için geliştirme hakkında daha fazla bilgi için bkz. [Azure IoT SDK 'ları](iot-hub-devguide-sdks.md).

IoT Hub yeteneklerini daha fazla incelemek için bkz.:

* [Azure IoT Edge ile uç cihazlara AI dağıtma](../iot-edge/tutorial-simulate-device-linux.md)
