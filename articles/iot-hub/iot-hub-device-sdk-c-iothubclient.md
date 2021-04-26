---
title: C-IoTHubClient için Azure IoT cihaz SDK 'Sı | Microsoft Docs
description: IoT Hub ile iletişim kuran cihaz uygulamaları oluşturmak için, C için Azure IoT cihaz SDK 'sında IoTHubClient kitaplığını kullanma.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 08/29/2017
ms.author: robinsh
ms.custom: amqp
ms.openlocfilehash: f33521dd9110d7ba6ee84650345b38c8c6a4950b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92149144"
---
# <a name="azure-iot-device-sdk-for-c--more-about-iothubclient"></a>C için Azure IoT cihaz SDK 'Sı – IoTHubClient hakkında daha fazla bilgi

[C Için Azure IoT cihaz SDK 'sı](iot-hub-device-sdk-c-intro.md) , bu serinin **c için Azure IoT cihaz SDK 'sını** tanıtan ilk makaledir. Bu makalede, SDK 'da iki mimari katman olduğunu anlatılmıştı. Tabanında, IoT Hub iletişimi doğrudan yöneten **Iothubclient** kitaplığı vardır. Ayrıca, serileştirme hizmetleri sağlamak için üzerine yapı veren **serileştirici** kitaplığı vardır. Bu makalede, **ıothubclient** kitaplığı üzerinde ek ayrıntı sağlayacağız.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Önceki makalede, IoT Hub olayları göndermek ve iletileri almak için **Iothubclient** kitaplığı 'nın nasıl kullanılacağı açıklanır. Bu makalede, verileri gönderdiğinizde *ve alırken daha* **düşük düzeydeki API**'lere girerek bu tartışmayı daha hassas bir şekilde nasıl yöneteceğiniz açıklanarak bu tartışma genişletilir. Ayrıca, **ıothubclient** kitaplığındaki Özellik işleme özelliklerini kullanarak olaylara nasıl özellik iliştirileceğini (ve bunları iletilerle alma) açıklayacağız. Son olarak, IoT Hub alınan iletileri işlemenin farklı yollarından daha fazla açıklama sağlıyoruz.

Makale, cihaz kimlik bilgileri hakkında daha fazla konu ve yapılandırma seçenekleri aracılığıyla **Iothubclient** davranışının nasıl değiştirileceği dahil olmak üzere çeşitli birçok konuyu kapsayan ile sonlanır.

Bu konuları açıklamak için **Iothubclient** SDK örnekleri kullanacağız. Birlikte izlemek isterseniz, C için Azure IoT cihaz SDK 'sına dahil edilen **ıothub \_ istemci \_ örnek \_ http** ve **ıothub \_ Client \_ Sample \_ AMQP** uygulamalarına bakın. aşağıdaki bölümlerde açıklanan her şey bu örneklerde gösterilmiştir.

C GitHub deposu [**Için Azure IoT CIHAZ SDK**](https://github.com/Azure/azure-iot-sdk-c) 'sını bulabilir ve [c API başvurusunda](/azure/iot-hub/iot-c-sdk-ref/)API 'nin ayrıntılarını görüntüleyebilirsiniz.

## <a name="the-lower-level-apis"></a>Alt düzey API 'Ler

Önceki makalede, **ıothub \_ istemci \_ örneği \_ AMQP** uygulaması bağlamı içinde **iothubclient** 'ın temel işlemi açıklanmaktadır. Örneğin, bu kodu kullanarak kitaplığın nasıl başlatılacağını anlatılmıştı.

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Ayrıca, bu işlev çağrısını kullanarak olayların nasıl gönderileceğini de açıklamaktadır.

```C
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

Makale Ayrıca bir geri çağırma işlevi kaydederek ileti alma işlemini de açıklamaktadır.

```C
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

Makalede ayrıca, aşağıdaki gibi bir kod kullanılarak kaynakların nasıl boşaltıyapılacağı gösterildi.

```C
IoTHubClient_Destroy(iotHubClientHandle);
```

Bu API 'lerin her biri için ek işlevler vardır:

* IoTHubClient \_ ll \_ createfromconnectionstring
* IoTHubClient \_ ll \_ SendEventAsync
* IoTHubClient \_ ll \_ setmessagecallback
* IoTHubClient \_ ll \_ yok etme

Bu işlevlerin tümü API adında **ll** içerir. Adın diğer **bölümünde,** bu işlevlerin her bırının parametreleri ll olmayan karşılıklarıyla aynıdır. Ancak, bu işlevlerin davranışı önemli bir şekilde farklıdır.

**Iothubclient \_ createfromconnectionstring** öğesini çağırdığınızda, temeldeki kitaplıklar arka planda çalışan yeni bir iş parçacığı oluşturur. Bu iş parçacığı olayları öğesine gönderir ve IoT Hub iletileri alır. **Ll** API 'leriyle çalışırken böyle bir iş parçacığı oluşturulmaz. Arka plan iş parçacığının oluşturulması, geliştiriciye kolaylık sağlar. Olayları açıkça gönderme ve IoT Hub iletileri alma konusunda endişelenmeniz gerekmez; arka planda otomatik olarak gerçekleşir. Buna karşılık, **ll** API 'leri, ihtiyacınız varsa IoT Hub iletişim üzerinde açık bir denetim sağlar.

Bu kavramı daha iyi anlamak için bir örneğe göz atalım:

**Iothubclient \_ SendEventAsync**'ı çağırdığınızda, aslında yaptığınız işlem, olayı bir arabelleğe koyuyor. **Iothubclient \_ createfromconnectionstring** ' i çağırdığınızda oluşturulan arka plan iş parçacığı sürekli olarak bu arabelleği izler ve IoT Hub için içerdiği tüm verileri gönderir. Bu, ana iş parçacığının diğer işleri gerçekleştirirken aynı zamanda arka planda gerçekleşir.

Benzer şekilde, **Iothubclient \_ setmessagecallback** kullanarak iletiler için bir geri çağırma işlevi kaydettiğinizde, ana iş parçacığından bağımsız olarak bir ileti alındığında arka plan iş parçacığının geri çağırma işlevini ÇAĞıRMASıNı sağlamak için SDK 'yı öğreneceksiniz.

**Ll** API 'leri arka plan iş parçacığı oluşturmaz. Bunun yerine, IoT Hub verileri açıkça göndermek ve almak için yeni bir API çağrılmalıdır. Bu, aşağıdaki örnekte gösterilmiştir.

SDK 'ya dahil olan **ıothub \_ istemci \_ örnek \_ http** uygulaması, alt düzey API 'leri gösterir. Bu örnekte, aşağıdaki gibi kodla IoT Hub olayları göndereceğiz:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

İlk üç satır iletiyi oluşturur ve son satır olayı gönderir. Ancak, daha önce belirtildiği gibi olayın gönderilmesi, verilerin yalnızca bir arabelleğe yerleştirilmesi anlamına gelir. **Iothubclient \_ ll \_ SendEventAsync**' i çağırdığınızda ağ üzerinde hiçbir şey aktarılmaz. Verileri gerçekten IoT Hub almak için, bu örnekte olduğu gibi **Iothubclient \_ ll \_ DoWork** öğesini çağırmanız gerekir:

```C
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Bu kod ( **ıothub \_ istemci \_ örneği \_ http** uygulamasından), **ıothubclient \_ ll \_ DoWork** öğesini tekrar tekrar çağırır. Her **ıothubclient \_ ll \_ dowork** çağrıldığında, IoT Hub için arabellekteki bazı olayları gönderir ve cihaza gönderilen sıraya alınmış bir ileti alır. İkinci durum, iletiler için bir geri çağırma işlevi kaydettiğimiz anlamına gelir, ardından geri çağırma çağrılır (herhangi bir ileti sıraya alındığı varsayılarak). Bu tür bir geri çağırma işlevini aşağıdaki gibi kodla kaydediyoruz:

```C
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

**Iothubclient \_ ll \_ DoWork** , genellikle bir döngüde çağrıldığında, her çağrıldığında, IoT Hub arabelleğe alınmış olaylar gönderir ve cihaz için sıradaki *bir* *sonraki* iletiyi alır. Her çağrının, tüm arabelleğe alınmış olayları gönderme veya sıraya alınan tüm iletileri alma garantisi yoktur. Arabellekte tüm olayları göndermek ve sonra diğer işlemeyle devam etmek istiyorsanız, bu döngüyü aşağıdaki gibi kodla değiştirebilirsiniz:

```C
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Bu kod, arabellekteki tüm olaylar IoT Hub gönderilene kadar **Iothubclient \_ ll \_ DoWork** öğesini çağırır. Bu, tüm sıraya alınmış iletilerin alındığını de göstermez. Bunun nedeni, "tüm" iletilerin, bir eylemi belirleyici olmadığını denetlemesinin bir parçasıdır. İletilerin "tümünü" alırsanız ne olur, ancak daha sonra başka bir cihaz cihaza hemen sonra gönderilebilir? Programlanmış bir zaman aşımıyla, bununla başa çıkmak için daha iyi bir yoldur. Örneğin, ileti geri çağırma işlevi her çağrıldığında bir zamanlayıcıyı sıfırlayabilir. Daha sonra, son *X* saniye içinde hiç mesaj alınmıyorsa işleme devam etmek için mantık yazabilirsiniz.

Olayları ve iletileri alma işlemlerini tamamladıktan sonra, kaynakları temizlemek için karşılık gelen işlevi çağırdığınızdan emin olun.

```C
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Temel olarak, bir arka plan iş parçacığı ve arka plan iş parçacığı olmadan aynı şeyi yapan başka bir API kümesi ile veri göndermek ve almak için yalnızca bir API kümesi vardır. Çok sayıda geliştirici, LL olmayan API 'Leri tercih edebilir, ancak geliştirici ağ aktarımları üzerinde açık denetim istediğinde alt düzey API 'Ler kullanışlıdır. Örneğin, bazı cihazlar zaman içinde veri toplar ve yalnızca belirtilen aralıklarda olayları (örneğin, bir saat veya bir kez günde bir kez) toplar. Alt düzey API 'Ler, IoT Hub veri gönderdiğinizde ve alırken size açıkça denetim olanağı verir. Diğerleri, alt düzey API 'Lerin sağladığı basitliği tercih eder. Arka planda oluşan bazı işler yerine ana iş parçacığında her şey gerçekleşir.

Hangi modele seçeceğiniz, kullandığınız API 'Ler için tutarlı olduğundan emin olun. Başlatırsanız, **ıothubclient \_ ll \_ createfromconnectionstring** öğesini çağırarak, her bir izleme çalışması için yalnızca Ilgili alt düzey API 'leri kullandığınızdan emin olun:

* IoTHubClient \_ ll \_ SendEventAsync
* IoTHubClient \_ ll \_ setmessagecallback
* IoTHubClient \_ ll \_ yok etme
* IoTHubClient \_ ll \_ DoWork

Tersi de geçerlidir. **Iothubclient \_ createfromconnectionstring** ile başlatırsanız, ek işlemler Için ll olmayan API 'leri kullanın.

C için Azure IoT cihaz SDK 'sında, alt düzey API 'lerin tam bir örneği için **ıothub \_ istemci \_ örnek \_ http** uygulamasına bakın. **Iothub \_ istemci \_ örneği \_ AMQP** uygulamasına, ll olmayan API 'lerin tam bir örneği için başvurulabilir.

## <a name="property-handling"></a>Özellik işleme

Şimdiye kadar, veri gönderme açıklandığımızda iletinin gövdesine başvuruyoruz. Örneğin, aşağıdaki kodu düşünün:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Bu örnek, IoT Hub "Merhaba Dünya" metniyle bir ileti gönderir. Ancak IoT Hub ayrıca özelliklerin her iletiye iliştirilmesi de sağlar. Özellikler, iletiye iliştirilebilecek ad/değer çiftleridir. Örneğin, iletiye bir özellik eklemek için önceki kodu değiştirebiliriz:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

**Iothubmessage \_ özelliklerini** çağırarak ve İletimizin tanıtıcısını geçirerek başladık. Geri aldığımız özellikler ekleme işlemi başlatmamızı sağlayan bir **harita \_ tanıtıcı** başvurusu. İkincisi, harita tanıtıcısı, özellik adı ve özellik değeri için bir başvuru alan **Map \_ AddOrUpdate** çağırarak gerçekleştirilir \_ . Bu API ile, beğendiğimiz kadar çok özellik ekleyebiliriz.

Olay **Event Hubs** okunabilirse alıcı, özellikleri numaralandıralıp bunlara karşılık gelen değerleri alabilir. Örneğin, .NET ' te [EventData nesnesindeki özellikler koleksiyonuna](/dotnet/api/microsoft.servicebus.messaging.eventdata)erişerek bu durum gerçekleştirilir.

Önceki örnekte, IoT Hub gönderdiğimiz bir olaya özellikler ekledik. Özellikler, IoT Hub alınan iletilere de eklenebilir. Bir iletiden Özellikler almak istiyoruz, ileti geri arama işlevimizde aşağıdakiler gibi bir kod kullanabiliriz:

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .

    // Retrieve properties from the message
    MAP_HANDLE mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                printf("Message Properties:\r\n");
                for (size_t index = 0; index < propertyCount; index++)
                {
                    printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                printf("\r\n");
            }
        }
    }

    . . .
}
```

**Iothubmessage \_ özelliklerine** yapılan çağrı, **harita \_ tanıtıcı** başvurusunu döndürür. Daha sonra bu başvuruyu, ad/değer çiftleri dizisine (Ayrıca özelliklerin sayımına) başvuru almak için **\_ Getınterals eşlemesi** için geçiyoruz. Bu noktada, istediğiniz değerlere ulaşmak için özellikleri Numaralandırırken basit bir önemi vardır.

Uygulamanızdaki özellikleri kullanmak zorunda değilsiniz. Ancak, bunları olaylarda ayarlamanız veya iletilerden almanız gerekiyorsa, **ıothubclient** kitaplığı bu şekilde kolaylaşır.

## <a name="message-handling"></a>İleti işleme

Daha önce belirtildiği gibi, iletiler IoT Hub 'den ulaştığında, kayıtlı bir geri çağırma işlevini çağırarak **ıthubclient** kitaplığı yanıt verir. Bu işlevin, ek açıklama sunan bir dönüş parametresi var. **Iothub \_ istemci \_ örnek \_ http** örnek uygulamasındaki geri çağırma işlevinin bir alıntısı aşağıda verilmiştir:

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Dönüş türünün **iothubmessage \_ değerlendirme \_ sonucu** olduğunu ve bu özel durumda **iothubmessage \_ kabul edildiğini** unutmayın. Bu işlevden geri dönebilmemiz için, **ıothubclient** kitaplığının ileti geri çağırması için nasıl yeniden davrandığını değiştiren başka değerler de vardır. Seçenekler şunlardır.

* **Iothubmessage \_ KABUL EDILDI** – ileti başarıyla işlendi. **Iothubclient** kitaplığı, geri çağırma işlevini aynı iletiyle yeniden çağıramaz.

* **Iothubmessage \_ REDDEDILDI** – ileti işlenmedi ve gelecekte bunu yapmak zorunda değilsiniz. **Iothubclient** kitaplığı, geri çağırma işlevini aynı iletiyle yeniden çağırmamalıdır.

* **Iothubmessage \_ BıRAKıLDı** – ileti başarıyla işlenmedi, ancak **Iothubclient** kitaplığı, geri çağırma işlevini aynı iletiyle yeniden çağırmalıdır.

İlk iki dönüş kodu için **Iothubclient** kitaplığı, iletinin cihaz sırasından silinip yeniden teslim edilmediğini belirten IoT Hub bir ileti gönderir. Net etkisi aynıdır (ileti, cihaz sırasından silinir), ancak iletinin kabul edilip edilmediği veya reddedildiğini hala kaydedilir.  Bu ayrımı kaydetmek, geri bildirimde dinleme yapan ve bir cihazın belirli bir iletiyi kabul ettiğini veya reddettiğini öğrenmek için ileti gönderenler için yararlıdır.

Son durumda IoT Hub için bir ileti de gönderilir, ancak ileti yeniden teslim edilmesi gerektiğini gösterir. Genellikle bir hatayla karşılaşırsanız ancak iletiyi yeniden işlemeyi denemek istiyorsanız bir iletiyi iptal edersiniz. Buna karşılık, kurtarılamaz bir hatayla karşılaştığınızda (veya yalnızca iletiyi işlemek istediğinize karar verirseniz) bir iletiyi reddetmek uygun bir işlemdir.

Herhangi bir durumda, **ıothubclient** kitaplığından istediğiniz davranışı kabul edebilmeniz için farklı dönüş kodlarından haberdar olun.

## <a name="alternate-device-credentials"></a>Diğer cihaz kimlik bilgileri

Daha önce açıklandığı gibi, **Iothubclient** kitaplığıyla çalışırken yapmanız gereken ilk şey, aşağıdaki gibi bir çağrı Içeren bir **ıothub \_ istemci \_ işleyicisi** elde oluşturmaktır:

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

**Iothubclient \_ createfromconnectionstring** bağımsız değişkenleri, cihaz bağlantı dizesi ve IoT Hub ile iletişim kurmak için kullandığımız Protokolü gösteren bir parametredir. Cihaz bağlantı dizesi aşağıdaki gibi görünen bir biçime sahiptir:

```C
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

Bu dizede dört bilgi vardır: IoT Hub adı, IoT Hub soneki, cihaz KIMLIĞI ve paylaşılan erişim anahtarı. Azure portal IoT Hub örneğinizi oluştururken IoT Hub 'ın tam etki alanı adını (FQDN) elde edersiniz. bu size IoT Hub 'ı adı (FQDN 'nin ilk bölümü) ve IoT Hub soneki (FQDN 'nin geri kalanı) sağlar. Cihazınızı IoT Hub kaydettiğinizde ( [önceki makalede](iot-hub-device-sdk-c-intro.md)açıklandığı gibi) cihaz kimliğini ve paylaşılan erişim anahtarını alırsınız.

**Iothubclient \_ CreateFromConnectionString** , kitaplığı başlatmanın bir yolunu sunar. İsterseniz, cihaz bağlantı dizesi yerine bu bağımsız parametreleri kullanarak yeni bir **ıothub \_ istemci \_ tanıtıcısı** oluşturabilirsiniz. Bu, aşağıdaki kodla sağlanır:

```C
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

Bu, **Iothubclient \_ createfromconnectionstring** ile aynı şeyi gerçekleştirir.

Bu daha ayrıntılı başlatma yöntemi yerine **Iothubclient \_ createfromconnectionstring** kullanmak isteyebileceğiniz açık görünebilir. Ancak, IoT Hub bir cihazı kaydettiğinizde, ne zaman bir cihaz KIMLIĞI ve cihaz anahtarı (bağlantı dizesi değil) olduğunu aklınızda bulundurun. [Önceki makalede](iot-hub-device-sdk-c-intro.md) tanıtılan *cihaz Gezgini* SDK aracı, cihaz kimliği, cihaz anahtarı ve IoT Hub ana bilgisayar adından cihaz bağlantı dizesi oluşturmak için **Azure IoT hizmeti SDK 'sının** kitaplıklarını kullanır. Bu nedenle, bir bağlantı dizesi oluşturma adımını kaydettiği için **Iothubclient \_ ll \_ Create** çağrısı tercih edilebilir. Hangi yöntemin uygun olduğunu kullanın.

## <a name="configuration-options"></a>Yapılandırma seçenekleri

Şimdiye kadar, **Iothubclient** kitaplığı 'nın çalışma şekli hakkında açıklanan her şey, varsayılan davranışını yansıtır. Ancak, kitaplığın çalışma biçimini değiştirmek için ayarlayabileceğiniz birkaç seçenek vardır. Bu, **ıothubclient \_ ll \_ SetOption** API 'sinden yararlanarak gerçekleştirilir. Bu örneği ele alalım:

```C
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

Yaygın olarak kullanılan birkaç seçenek vardır:

* **Settoplu işleme** (bool) – **true** ise, IoT Hub gönderilen veriler toplu olarak gönderilir. **Yanlışsa**, iletiler ayrı ayrı gönderilir. Varsayılan değer **false**'dur. AMQP/AMQP-WS üzerinden toplu işleme ve D2C iletilerinde sistem özellikleri ekleme desteklenir.

* **Zaman aşımı** (işaretsiz int) – Bu değer milisaniye cinsinden temsil edilir. HTTPS isteği gönderiyorsanız veya yanıt alma bu süreden uzun sürerse bağlantı zaman aşımına uğrar.

Toplu işlem seçeneği önemlidir. Varsayılan olarak, kitaplık olayları ayrı ayrı (tek bir olay, **Iothubclient \_ ll \_ SendEventAsync**'e geçirdiğiniz her şey) olarak yeniden algılar. Toplu işlem seçeneği **true** ise, kitaplık, arabelleğin (IoT Hub kabul edeceği en fazla ileti boyutuna kadar) olduğu kadar çok sayıda olay toplar.  Olay toplu işi tek bir HTTPS çağrısında IoT Hub gönderilir (tek olaylar bir JSON dizisine paketlenmiştir). Toplu işleme özelliğinin etkinleştirilmesi, genellikle ağ gidiş dönüşlerinin azaltılmasından bu yana büyük performans kazancı elde ediyor. Tek tek her olay için bir üst bilgi kümesi yerine bir olay toplu işi ile bir adet HTTPS üst bilgisi kümesi gönderdiğiniz için bant genişliğini önemli ölçüde azaltır. Bunun için özel bir nedeniniz yoksa, genellikle toplu işlemeyi etkinleştirmek isteyeceksiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, **C Için Azure IoT cihaz SDK 'sında** bulunan **Iothubclient** kitaplığının davranışını ayrıntılı olarak açıklanmaktadır. Bu bilgilerle, **Iothubclient** kitaplığının yeteneklerini iyi kavramalısınız. Bu serideki ikinci makale, **seri hale getirici** kitaplığı üzerinde benzer ayrıntılar sağlayan [C-seri hale getirici için Azure IoT cihaz SDK 'sına](iot-hub-device-sdk-c-serializer.md)sahiptir.

IoT Hub için geliştirme hakkında daha fazla bilgi için bkz. [Azure IoT SDK 'ları](iot-hub-devguide-sdks.md).

IoT Hub yeteneklerini daha fazla incelemek için bkz. [Azure IoT Edge ile uç CIHAZLARA AI dağıtma](../iot-edge/quickstart-linux.md).