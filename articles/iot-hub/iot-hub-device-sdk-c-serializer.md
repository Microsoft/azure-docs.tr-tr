---
title: C-serileştirici için Azure IoT cihaz SDK 'Sı | Microsoft Docs
description: IoT Hub ile iletişim kuran cihaz uygulamaları oluşturmak için, C için Azure IoT cihaz SDK 'sında seri hale getirici kitaplığı 'nı kullanma.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 09/06/2016
ms.author: robinsh
ms.openlocfilehash: a18f52f0d0979477ff8d6de6745694676f4b4d0e
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883161"
---
# <a name="azure-iot-device-sdk-for-c--more-about-serializer"></a>C için Azure IoT cihaz SDK 'Sı – serileştirici hakkında daha fazla bilgi

Bu serideki ilk makale, [C Için Azure IoT cihaz SDK 'Sına giriş olarak](iot-hub-device-sdk-c-intro.md)tanıtılmıştır. Sonraki makalede, [C--IoTHubClient Için Azure IoT cihaz SDK 'sının](iot-hub-device-sdk-c-iothubclient.md)daha ayrıntılı bir açıklaması verilmiştir. Bu makale, kalan bileşenin daha ayrıntılı bir açıklamasını sağlayarak SDK 'nın kapsamını tamamlar: **serileştirici** kitaplığı.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub, **seri hale getirici** kitaplığı 'nı kullanarak olayları gönderme ve iletileri alma hakkında açıklanan tanıtım makalesi. Bu makalede, verilerin **serileştirici** makro diliyle nasıl modelleceği hakkında daha ayrıntılı bir açıklama sunarak bu tartışmayı genişlettik. Makale Ayrıca kitaplığın iletileri serileştirme (ve bazı durumlarda serileştirme davranışını nasıl denetleyebilmeniz) hakkında daha fazla ayrıntı içerir. Ayrıca, oluşturduğunuz modellerin boyutunu belirleyecek değişiklik yaptığınız bazı parametreleri de açıklayacağız.

Son olarak, makale, ileti ve özellik işleme gibi önceki makalelerde ele alınan bazı konuları yeniden ziyaret ediyor. Öğrendiğimiz gibi bu özellikler, **ıthubclient** kitaplığı ile çalıştıkları gibi **serileştirici** kitaplığı ile aynı şekilde çalışır.

Bu makalede açıklanan her şey, **serileştirici** SDK örneklerine dayalıdır. Birlikte izlemek isterseniz, C için Azure IoT cihaz SDK 'sına eklenen **\_SimpleSample AMQP** ve **\_SimpleSample http** uygulamalarına bakın.

C GitHub deposu [**Için Azure IoT CIHAZ SDK**](https://github.com/Azure/azure-iot-sdk-c) 'sını bulabilir ve [c API başvurusunda](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)API 'nin ayrıntılarını görüntüleyebilirsiniz.

## <a name="the-modeling-language"></a>Modelleme dili

Bu serideki [c için Azure IoT cihaz SDK 'sı](iot-hub-device-sdk-c-intro.md) makalesi, **\_SimpleSample AMQP** uygulamasında sağlanan örnek aracılığıyla **c modelleme için Azure IoT cihaz SDK 'sını** kullanıma sunmuştur:

```C
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(double, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

Gördüğünüz gibi, modelleme dili C makrolarını temel alır. **Başlangıç\_ad alanı** ile tanımınızı her zaman başlatın ve **bitiş\_ad**alanıyla her zaman sonlandırın. Şirketinizin ad alanını veya bu örnekteki gibi, üzerinde çalıştığınız projeyi adlandırmak yaygın bir addır.

Ad alanı içinde olan özellikler model tanımlardır. Bu durumda, bir Anemometre için tek bir model vardır. Bir kez daha, model herhangi bir şey olarak adlandırılabilir, ancak genellikle model IoT Hub ile değiştirmek istediğiniz cihaz veya veri türü için adlandırılır.  

Modeller, IoT Hub ( *veriler*) ve IoT Hub ( *Eylemler*) içinden alacağınız iletiler için kullanabileceğiniz olayların bir tanımını içerir. Örnekte görebileceğiniz gibi, olayların türü ve adı vardır; eylemlerde bir ad ve isteğe bağlı parametreler (her biri bir tür ile) vardır.

Bu örnekte gösterilmediğinden, SDK tarafından desteklenen ek veri türleri de verilmiştir. Bundan sonra ele alınacaktır.

> [!NOTE]
> IoT Hub, bir cihazın kendisine *olay*olarak gönderdiği verileri ifade eder, ancak modelleme dili bunu *veri* olarak ifade eder ( **WITH_DATA**kullanılarak tanımlanır). Benzer şekilde, IoT Hub, aygıtlara *ileti*olarak gönderilen verileri, modelleme dili ise *eylem* olarak ifade eder ( **WITH_ACTION**kullanılarak tanımlanır). Bu koşulların, bu makalede birbirinin yerine kullanılabileceğini unutmayın.
> 
> 

## <a name="supported-data-types"></a>Desteklenen veri türleri

Aşağıdaki veri türleri, **serileştirici** kitaplığı ile oluşturulan modellerde desteklenir:

| Type | Açıklama |
| --- | --- |
| double |çift duyarlık kayan nokta sayısı |
| int |32 bit tamsayı |
| float |tek duyarlıklı kayan noktalı sayı |
| long |uzun tamsayı |
| int8\_t |8 bit tamsayı |
| int16\_t |16 bit tamsayı |
| int32\_t |32 bit tamsayı |
| int64\_t |64 bit tamsayı |
| bool |boolean |
| ascii\_char\_ptr |ASCII dizesi |
| EDM\_DATE\_TIME\_OFFSET |Tarih saat boşluğu |
| EDM\_GUID |GUID |
| EDM\_BINARY |binary |
| DECLARE\_STRUCT |karmaşık veri türü |

Son veri türüyle başlayalım. **DECLARE\_STRUCT** , diğer temel türlerin gruplandırmaları olan karmaşık veri türleri tanımlamanızı sağlar. Bu gruplandırmalar şuna benzer bir model tanımlamamızı sağlar:

```C
DECLARE_STRUCT(TestType,
double, aDouble,
int, aInt,
float, aFloat,
long, aLong,
int8_t, aInt8,
uint8_t, auInt8,
int16_t, aInt16,
int32_t, aInt32,
int64_t, aInt64,
bool, aBool,
ascii_char_ptr, aAsciiCharPtr,
EDM_DATE_TIME_OFFSET, aDateTimeOffset,
EDM_GUID, aGuid,
EDM_BINARY, aBinary
);

DECLARE_MODEL(TestModel,
WITH_DATA(TestType, Test)
);
```

Modelimiz **TestType**türünde tek bir veri olayı içeriyor. **TestType** , **seri hale getirici** modelleme dili tarafından desteklenen temel türleri topluca gösteren çeşitli Üyeler içeren karmaşık bir türdür.

Bunun gibi bir modelde, aşağıdaki gibi görünen IoT Hub veri göndermek için kod yazalım:

```C
TestModel* testModel = CREATE_MODEL_INSTANCE(MyThermostat, TestModel);

testModel->Test.aDouble = 1.1;
testModel->Test.aInt = 2;
testModel->Test.aFloat = 3.0f;
testModel->Test.aLong = 4;
testModel->Test.aInt8 = 5;
testModel->Test.auInt8 = 6;
testModel->Test.aInt16 = 7;
testModel->Test.aInt32 = 8;
testModel->Test.aInt64 = 9;
testModel->Test.aBool = true;
testModel->Test.aAsciiCharPtr = "ascii string 1";

time_t now;
time(&now);
testModel->Test.aDateTimeOffset = GetDateTimeOffset(now);

EDM_GUID guid = { { 0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09, 0x0A, 0x0B, 0x0C, 0x0D, 0x0E, 0x0F } };
testModel->Test.aGuid = guid;

unsigned char binaryArray[3] = { 0x01, 0x02, 0x03 };
EDM_BINARY binaryData = { sizeof(binaryArray), &binaryArray };
testModel->Test.aBinary = binaryData;

SendAsync(iotHubClientHandle, (const void*)&(testModel->Test));
```

Temel olarak, **Test** yapısının her üyesine bir değer atacağız ve **Test** verileri olayını buluta göndermek için **sendadsync** çağrılıyor. **Sendadsync** , IoT Hub için tek bir veri olayı gönderen bir yardımcı işlevdir:

```C
void SendAsync(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const void *dataEvent)
{
    unsigned char* destination;
    size_t destinationSize;
    if (SERIALIZE(&destination, &destinationSize, *(const unsigned char*)dataEvent) ==
    {
        // null terminate the string
        char* destinationAsString = (char*)malloc(destinationSize + 1);
        if (destinationAsString != NULL)
        {
            memcpy(destinationAsString, destination, destinationSize);
            destinationAsString[destinationSize] = '\0';
            IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromString(destinationAsString);
            if (messageHandle != NULL)
            {
                IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)0);

                IoTHubMessage_Destroy(messageHandle);
            }
            free(destinationAsString);
        }
        free(destination);
    }
}
```

Bu işlev, belirtilen veri olayını seri hale getirir ve **\_iothubclient SendEventAsync**kullanarak IoT Hub gönderir. Bu, önceki makalelerde ele alınan aynı koddur (**Sendadsync** mantığı uygun bir işlev halinde Kapsüller).

Önceki kodda kullanılan başka bir yardımcı işlev **GetDateTimeOffset**. Bu işlev, verilen süreyi **EDM\_Tarih\_saat\_kayması**türünde bir değere dönüştürür:

```C
EDM_DATE_TIME_OFFSET GetDateTimeOffset(time_t time)
{
    struct tm newTime;
    gmtime_s(&newTime, &time);
    EDM_DATE_TIME_OFFSET dateTimeOffset;
    dateTimeOffset.dateTime = newTime;
    dateTimeOffset.fractionalSecond = 0;
    dateTimeOffset.hasFractionalSecond = 0;
    dateTimeOffset.hasTimeZone = 0;
    dateTimeOffset.timeZoneHour = 0;
    dateTimeOffset.timeZoneMinute = 0;
    return dateTimeOffset;
}
```

Bu kodu çalıştırırsanız aşağıdaki ileti IoT Hub gönderilir:

```C
{"aDouble":1.100000000000000, "aInt":2, "aFloat":3.000000, "aLong":4, "aInt8":5, "auInt8":6, "aInt16":7, "aInt32":8, "aInt64":9, "aBool":true, "aAsciiCharPtr":"ascii string 1", "aDateTimeOffset":"2015-09-14T21:18:21Z", "aGuid":"00010203-0405-0607-0809-0A0B0C0D0E0F", "aBinary":"AQID"}
```

Serileştirme, **seri hale getirici** kitaplığı tarafından oluşturulan BIÇIM olan JSON 'da olduğunu unutmayın. Ayrıca, serileştirilmiş JSON nesnesinin her üyesinin, modelimizde tanımladığımız **TestType** üyeleriyle eşleştiğini unutmayın. Değerler kodda kullanılanlarla de tamamen eşleşir. Ancak, ikili verilerin base64 kodlamalı olduğunu unutmayın: "AQıD", {0x01, 0x02, 0x03} öğesinin Base64 kodlamasında.

Bu örnek, **serileştirici** Kitaplığı kullanmanın avantajlarından yararlanır; bu, uygulamamızda serileştirme ile açıkça uğraşmak zorunda kalmadan JSON 'ı buluta göndermemizi sağlar. Endişelenmemiz gerekenler, modelimizin veri olaylarının değerlerini ayarlamamız ve bu olayları buluta göndermek için basit API 'Ler çağırıyor.

Bu bilgilerle, karmaşık türler de dahil olmak üzere desteklenen veri türleri aralığını içeren modeller tanımlayabiliriz (diğer karmaşık türler içinde karmaşık türler de dahil olmak üzere). Ancak, yukarıdaki örnek tarafından oluşturulan serileştirilmiş JSON önemli bir nokta getirir. **Seri hale getirici** kitaplığı ile veri gönderme IŞLEMI, tam olarak JSON nasıl biçimlendirildiğini belirler. Bu belirli nokta, ileri ele alacağız.

## <a name="more-about-serialization"></a>Serileştirme hakkında daha fazla bilgi

Önceki bölümde, **serileştirici** kitaplığı tarafından oluşturulan çıktının bir örneği vurgulanmıştır. Bu bölümde, kitaplığın verileri serileştirerek, serileştirme API 'Lerini kullanarak bu davranışı nasıl denetleyebileceğini açıklayacağız.

Serileştirme ile ilgili tartışmayı ilerletmek için, bir termostat temel alınarak yeni bir modelle çalışacağız. İlk olarak, ele veriyoruz senaryosunda bir arka plan sağlamamız.

Sıcaklık ve nem ölçtüğünden oluşan bir termostat modellemek istiyoruz. Her veri parçası IoT Hub farklı şekilde gönderilir. Varsayılan olarak, termostat her 2 dakikada bir sıcaklık olayını ihlal eden bir sıcaklığa sahiptir; her 15 dakikada bir nem olayı görüntülenir. Her iki olay da kullanıldığında, karşılık gelen sıcaklık veya nem ölçdüğü süreyi gösteren bir zaman damgası içermelidir.

Bu senaryo verildiğinde, verileri modellemenin iki farklı yolunu göstereceğiz ve modelleme 'nin serileştirilmiş çıktıda sahip olduğu etkiyi açıklayacağız.

### <a name="model-1"></a>Model 1

Bir modelin önceki senaryoyu destekleyen ilk sürümü aşağıda verilmiştir:

```C
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(TemperatureEvent,
int, Temperature,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_STRUCT(HumidityEvent,
int, Humidity,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureEvent, Temperature),
WITH_DATA(HumidityEvent, Humidity)
);

END_NAMESPACE(Contoso);
```

Modelin iki veri olayı içerdiğine unutmayın: **Sıcaklık** ve **nem**. Önceki örneklerden farklı olarak, her olayın türü **Declare\_struct**kullanılarak tanımlanan bir yapıdır. **TemperatureEvent** bir sıcaklık ölçümü ve bir zaman damgası içerir; **Humidtyevent** , bir nem ölçümü ve bir zaman damgası içerir. Bu model, yukarıda açıklanan senaryo için verileri modeletmenin doğal bir yolunu sunar. Buluta bir olay gönderdiğimiz zaman, bir sıcaklık/zaman damgası ya da bir nem/zaman damgası çifti göndereceğiz.

Aşağıdaki gibi bir kod kullanarak bir sıcaklık olayını buluta gönderebiliriz:

```C
time_t now;
time(&now);
thermostat->Temperature.Temperature = 75;
thermostat->Temperature.Time = GetDateTimeOffset(now);

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Örnek kodda sıcaklık ve nem için sabit kodlanmış değerler kullanacağız, ancak gerçekten de termostat 'daki karşılık gelen algılayıcıları örnekleyerek bu değerleri aldığımızda düşünün.

Yukarıdaki kod, daha önce tanıtılan **GetDateTimeOffset** yardımcısını kullanır. Daha sonra açık hale gelecek nedenlerden dolayı, bu kod, etkinliği serileştirme ve gönderme görevini açıkça ayırır. Önceki kod, sıcaklık olayını bir arabelleğe seri hale getirir. Sonra, **SendMessage** , olayı IoT Hub gönderen bir yardımcı işlevdir ( **SimpleSample\_AMQP**'ye dahildir):

```C
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId);

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
}
```

Bu kod, önceki bölümde açıklanan **Sendadsync** Yardımcısı 'nın bir alt kümesidir. bu nedenle, burada yeniden gidemezsiniz.

Sıcaklık olayını göndermek için önceki kodu çalıştırdığımızda, olayın bu serileştirilmiş formu IoT Hub gönderilir:

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

**TemperatureEvent** türünde olan ve bu yapı bir **sıcaklık** ve **saat** üyesi içeren bir sıcaklık gönderiyoruz. Bu, doğrudan serileştirilmiş verilere yansıtılır.

Benzer şekilde, bu kodla nem olayı gönderebiliriz:

```C
thermostat->Humidity.Humidity = 45;
thermostat->Humidity.Time = GetDateTimeOffset(now);
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

IoT Hub gönderilen seri hale getirilmiş form aşağıdaki gibi görünür:

```C
{"Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Bu, beklenildiği gibidir.

Bu modelle, ek olayların nasıl kolayca eklenebileceklerini hayal edebilirsiniz. **Declare\_struct**kullanarak daha fazla yapı tanımlayabilir ve ilgili olayı, **\_verilerle**kullanarak modele dahil edersiniz.

Şimdi, modeli aynı verileri, ancak farklı bir yapıyla birlikte içerecek şekilde değiştirelim.

### <a name="model-2"></a>Model 2

Bu alternatif modeli yukarıdaki bir şekilde düşünün:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Bu durumda, **Declare\_yapı** makrolarını ortadan kaldırdık ve yalnızca modelleme dilinden basit türler kullanarak senaryomızdan veri öğeleri tanımlanıyor.

Yalnızca o **sırada, zaman** olayını yoksayın. Bu şekilde, **sıcaklık**girişi için kod aşağıda verilmiştir:

```C
time_t now;
time(&now);
thermostat->Temperature = 75;

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Bu kod, IoT Hub için aşağıdaki serileştirilmiş olayı gönderir:

```C
{"Temperature":75}
```

Ve nem olayını gönderme kodu şu şekilde görünür:

```C
thermostat->Humidity = 45;
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Bu kod bunu IoT Hub gönderir:

```C
{"Humidity":45}
```

Şimdiye kadar hiç sürme yok. Şimdi SERILEŞTIRME makrosunu kullandığımıza bakalım.

**Seri hale getirme** makrosu birden çok veri olayını bağımsız değişken olarak alabilir. Bu, **sıcaklık** ve **nem** olayını birlikte serileştirmenizi ve bunları tek bir çağrıda IoT Hub göndermemizi sağlar:

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Bu kodun sonucunun IoT Hub için iki veri olayının gönderildiğini tahmin edebilirsiniz:

[{"Sıcaklık": 75}, {"nem": 45}]

Diğer bir deyişle, bu kodun **sıcaklık** ve **nem** gönderme ile aynı olduğunu tahmin edebilirsiniz. Her iki olayı da aynı çağrıda serileştirmek üzere geçirmek kolaylık vardır. Ancak, bu durum böyle değildir. Bunun yerine, yukarıdaki kod IoT Hub için bu tek veri olayını gönderir:

{"Sıcaklık": 75, "nem": 45}

Modelimiz, iki *ayrı* olay olarak **sıcaklık** ve **nem** tanımladığından bu işlem garip görünebilir:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Daha fazla nokta için, **sıcaklık** ve **nem** 'nin aynı yapıda olduğu bu olayları modelliyoruz:

```C
DECLARE_STRUCT(TemperatureAndHumidityEvent,
int, Temperature,
int, Humidity,
);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureAndHumidityEvent, TemperatureAndHumidity),
);
```

Bu modeli kullandığımızda, **sıcaklık** ve **nem** 'nin aynı seri hale getirilmiş mesajta nasıl gönderileceğini anlamak daha kolay olacaktır. Ancak, model 2 ' ye kullanarak her iki veri olayını serileştirmek üzere geçirdiğinizde bu şekilde neden bu şekilde çalışmadığını temizlemeyebilir.

**Seri hale getirici** kitaplığı 'nın yapmakta olduğu varsayımları biliyorsanız, bu davranışın anlaşılması daha kolay olur. Bu izin hakkında daha fazla fikir sahibi olmak için modelinize geri dönebilirsiniz:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Bu modeli nesne odaklı koşullara göz önünde bulundurun. Bu durumda, fiziksel bir cihaz (bir termostat) modelliyoruz ve bu cihaz **sıcaklık** ve **nem**gibi öznitelikler içerir.

Modelimizin tüm durumunu aşağıdaki gibi kodla gönderebiliriz:

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity, thermostat->Time) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Sıcaklık, nem ve zaman değerlerinin ayarlandığı varsayılarak, bu IoT Hub gönderildiği gibi bir olay görüyoruz:

```C
{"Temperature":75, "Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Bazen yalnızca modelin *bazı* özelliklerini buluta göndermek isteyebilirsiniz (Bu, modelinizde çok sayıda veri olayı varsa bu özellikle doğrudur). Önceki örnekte olduğu gibi, veri olaylarının yalnızca bir alt kümesini göndermek yararlı olur:

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Bu, tıpkı model 1 ile yaptığımız gibi, **sıcaklık** ve **saat** üyesi ile bir **TemperatureEvent** tanımladığımız gibi, tam olarak aynı serileştirilmiş olay oluşturur. Bu durumda, **serileştirme** 'i farklı bir şekilde çağırdığımız için farklı bir model (model 2) kullanarak tam olarak aynı serileştirilmiş olay oluşturuyoruz.

Önemli nokta, **seri hale getirmek** için birden çok veri olayı geçirirseniz, her olayın tek bir JSON nesnesinde bir özellik olduğunu varsaymaktadır.

En iyi yaklaşım size ve modeliniz hakkında nasıl düşündüğünüzü size bağlıdır. Buluta "olaylar" gönderiyorsanız ve her bir olay tanımlı bir özellikler kümesi içeriyorsa, ilk yaklaşım çok daha anlamlı hale gelir. Bu durumda, her bir olayın yapısını tanımlamak için **Declare\_struct** ' ı kullanın ve ardından bunları, **WITH\_Data** makrosu ile modelinize dahil edebilirsiniz. Ardından, her olayı yukarıdaki ilk örnekte yaptığımız gibi gönderirsiniz. Bu yaklaşımda yalnızca **seri hale getirici**'e tek bir veri olayı geçitirsiniz.

Modelinizi nesne odaklı bir biçimde düşünüyorsanız ikinci yaklaşım size uygun olabilir. Bu durumda, **ile\_** kullanılarak tanımlanan öğeler nesnenizin "özelliklerdir". "Nesnenizin" durumunun buluta göndermek istediğiniz miktarına bağlı olarak, istediğiniz **seri hale getirmek** için herhangi bir olay alt kümesini geçitirsiniz.

Nether yaklaşımı doğru veya yanlış. **Seri hale getirici** kitaplığının nasıl çalıştığını bilmeniz ve gereksinimlerinize en uygun modelleme yaklaşımını seçmeniz yeterlidir.

## <a name="message-handling"></a>İleti işleme

Bu makalede şu ana kadar, yalnızca IoT Hub olayları ve iletileri alma hakkında değinildiği açıklanmaktadır. Bunun nedeni, iletileri alma hakkında bilmemiz gereken, [C Için Azure IoT CIHAZ SDK](iot-hub-device-sdk-c-intro.md)makalesinde büyük ölçüde ele alınmıştır. İleti geri çağırma işlevini kaydederek iletileri işlemekte olduğunuz makaleden geri çekin:

```C
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

Daha sonra bir ileti alındığında çağrılan geri çağırma işlevini yazarsınız:

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
            memcpy(temp, buffer, size);
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

**Iothubmessage** uygulamasının bu uygulama, modelinizdeki her bir eylem için belirli bir işlevi çağırır. Örneğin, modeliniz bu eylemi tanımlıyorsa:

```C
WITH_ACTION(SetAirResistance, int, Position)
```

Şu imzaya sahip bir işlev tanımlamanız gerekir:

```C
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

Daha sonra bu ileti cihazınıza gönderildiğinde, **Setaırdiri** çağırılır.

Henüz açıklanmadığımız ileti, serileştirilmiş bir ileti olan sürümünün nasıl görüneceğine ilişkin bir şeydir. Diğer bir deyişle, cihazınıza bir **Setaırdire** iletisi göndermek istiyorsanız ne gibi görünüyor?

Bir cihaza ileti gönderiyorsanız Azure IoT hizmeti SDK 'Sı üzerinden bunu yapabilirsiniz. Yine de belirli bir eylemi çağırmak için hangi dizenin gönderileceğini bilmeniz gerekir. İleti göndermek için genel biçim aşağıdaki gibi görünür:

```C
{"Name" : "", "Parameters" : "" }
```

İki özelliği olan serileştirilmiş JSON nesnesi gönderiyorsunuz: **Ad** , eylemin adı (ileti) ve **Parametreler** bu eylemin parametrelerini içerir.

Örneğin, **Setaırdiri** çağırmak için bu iletiyi bir cihaza gönderebilirsiniz:

```C
{"Name" : "SetAirResistance", "Parameters" : { "Position" : 5 }}
```

Eylem adı, modelinizde tanımlanan bir eylemle tam olarak eşleşmelidir. Parametre adlarının de aynı olması gerekir. Ayrıca büyük/küçük harf duyarlılığı. **Ad** ve **Parametreler** her zaman büyük harfle yazılır. Modelinizdeki eylem adınızın ve parametrelerinizin durumuyla eşleştiğinden emin olun. Bu örnekte, eylem adı "Setaırdirenc" ve "setaırdirenc" değildir.

Bu iletileri bir cihaza göndererek, iki diğer eylem **Açık** ve **devre dışı bırakma** çağrılabilir:

```C
{"Name" : "TurnFanOn", "Parameters" : {}}
{"Name" : "TurnFanOff", "Parameters" : {}}
```

Bu bölümde, olay gönderirken ve **seri hale getirici** kitaplığı ile ileti alırken bilmeniz gereken her şey açıklanmaktadır. ' In üzerine geçmeden önce, modelinizin ne kadar büyük olduğunu denetlemek için bazı parametreleri kapsayalım.

## <a name="macro-configuration"></a>Makro yapılandırması

**Seri hale getirici** kitaplığını kullanıyorsanız, bılmenız için SDK 'nın önemli bir parçası Azure-c-Shared-Utility kitaplığı 'nda bulunur.

--Özyinelemeli seçeneğini kullanarak GitHub 'dan Azure-IoT-SDK-c deposunu Klonladığınız takdirde bu paylaşılan yardımcı program kitaplığını buradan bulabilirsiniz:

```C
.\\c-utility
```

Kitaplığı klonlandıysanız, [burada](https://github.com/Azure/azure-c-shared-utility)bulabilirsiniz.

Paylaşılan yardımcı program kitaplığı 'nda aşağıdaki klasörü bulabilirsiniz:

```C
azure-c-shared-utility\\macro\_utils\_h\_generator.
```

Bu klasör **makro\_yardımcı programları\_h\_Generator. sln**adlı bir Visual Studio çözümü içerir:

  ![Visual Studio çözümünün maco_utils_h_generator ekran görüntüsü](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.png)

Bu çözümdeki program **Macro\_Utils. h** dosyasını oluşturur. SDK 'ya eklenen bir varsayılan\_makro yardımcı programları. h dosyası vardır. Bu çözüm, bazı parametreleri değiştirmenize ve sonra bu parametrelere göre başlık dosyasını yeniden oluşturmanıza olanak sağlar.

İle ilgilenme yapılacak iki temel parametre, makro\_Utils.tt içinde bulunan bu iki satırda tanımlanan **naritmetik** ve **nmakrolardır** :

```C
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>
```

Bu değerler, SDK 'ya dahil edilen varsayılan parametrelerdir. Her parametrenin aşağıdaki anlamı vardır:

* nmakroparameters – bir Declare\_modeli makro tanımında kaç parametre kullanabileceğinizi denetler.
* Naritmetik: bir modelde izin verilen üyelerin toplam sayısını denetler.

Bu parametrelerin önemli olmasının nedeni, modelinizin ne kadar büyük olduğunu denetlamalardır. Örneğin, bu model tanımını göz önünde bulundurun:

```C
DECLARE_MODEL(MyModel,
WITH_DATA(int, MyData)
);
```

Daha önce belirtildiği gibi **,\_bildirme modeli** yalnızca bir C makrosu olur. Model ve **WITH\_Data** deyimlerinin (ancak başka bir makronun) adları, **Declare\_modelinin**parametreleri. **nmakroparameters** , **\_Declare modeline**kaç parametre ekleneceğini tanımlar. Etkin şekilde, bu, kaç veri olayı ve eylem bildiriminin sahip kalabileceğini tanımlar. Bu nedenle, varsayılan 124 sınırı ile bu, bir modeli, yaklaşık 60 eylemleri ve veri olayları ile tanımlayabilmeniz anlamına gelir. Bu sınırı aşmaya çalışırsanız şuna benzer bir derleyici hatası alırsınız:

  ![Makro parametreleri derleyici hatalarının ekran görüntüsü](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.png)

**Naritmetik** parametresi, makro dilinin uygulamanıza göre iç işleyişi hakkında daha fazla.  **DECLARE_STRUCT** makroları da dahil olmak üzere, modelinizde sahip olabilirsiniz Toplam üye sayısını denetler. Bu gibi derleyici hatalarını görmeye başladığınızda, **Naritmetiğini**artırmayı denemeniz gerekir:

   ![Aritmetik derleyici hatalarının ekran görüntüsü](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.png)

Bu parametreleri\_değiştirmek istiyorsanız, Macro Utils.tt dosyasındaki değerleri değiştirin, makroyu\_utils\_h\_Oluşturucu. sln çözümünü yeniden derleyin ve derlenen programı çalıştırın. Bunu yaptığınızda, yeni bir makro\_yardımcı programları. h dosyası oluşturulur ve içine yerleştirilir.\\ ortak\\Inc dizini.

Makro\_yardımcı programları. h ' nin yeni sürümünü kullanabilmeniz için, çözümünüzde **serileştirici** NuGet paketini çözümünüzden kaldırın ve onun yerine **serileştirici** Visual Studio projesini ekleyin. Bu, kodunuzun serileştirici kitaplığının kaynak koduna karşı derlenmesini sağlar. Bu, güncelleştirilmiş makro\_yardımcı programları. h ' i içerir. Bunu **SimpleSample\_AMQP**için yapmak istiyorsanız, seri hale getirici kitaplığı için NuGet paketini çözümden kaldırarak başlatın:

   ![Seri hale getirici kitaplığı için NuGet paketini kaldırma ekran görüntüsü](media/iot-hub-device-sdk-c-serializer/04-serializer-github-package.png)

Sonra bu projeyi Visual Studio çözümünüze ekleyin:

> . \\cseri\\hale getirici\\Derleme\\Windowsseri\\hale getirici. vcxproj
> 
> 

İşiniz bittiğinde çözümünüz şuna benzemelidir:

   ![Simplesample_amqp Visual Studio çözümünün ekran görüntüsü](media/iot-hub-device-sdk-c-serializer/05-serializer-project.png)

Artık çözümünüzü derlerken, diğer bir deyişle, güncelleştirilmiş makro\_olan Utils. h, ikiliye dahil edilmiştir.

Bu değerlerin yeterince artması, derleyici sınırlarını aşabilir. Bu noktada, **Nmakroparameters** , ilgilenme yapılacak ana parametredir. C99 spec, bir makro tanımında en az 127 parametreye izin verildiğini belirtir. Microsoft derleyicisi, özelliği tam olarak (ve 127 sınırı vardır) izler, bu nedenle **Nmakroparameters** 'ı varsayılan dışında artırmanız mümkün olmayacaktır. Diğer derleyiciler bunu yapmanıza izin verebilir (örneğin, GNU derleyicisi daha yüksek bir sınırı destekler).

Şimdiye kadar, **seri hale getirici** kitaplığı ile kod yazma hakkında bilmeniz gereken her şeyi sunuyoruz. İşlem öncesinde, daha önce merak ettiğiniz önceki makalelerden bazı konuları tekrar ziyaret edelim.

## <a name="the-lower-level-apis"></a>Alt düzey API 'Ler
Bu makalenin odaklandığı örnek uygulama, **SimpleSample\_AMQP**'dir. Bu örnek, olayları göndermek ve ileti almak için üst düzey (**ll**olmayan) API 'leri kullanır. Bu API 'Leri kullanırsanız, bir arka plan iş parçacığı çalışır ve bunlar hem gönderme olayları hem de ileti alma işlemini gerçekleştirir. Bununla birlikte, bu arka plan iş parçacığını ortadan kaldırmak ve buluttan ileti aldığınızda açık denetim almak için alt düzey (LL) API 'Lerini kullanabilirsiniz.

[Önceki makalede](iot-hub-device-sdk-c-iothubclient.md)açıklandığı gibi, daha üst düzey API 'lerden oluşan bir işlevler kümesi vardır:

* İothubclient\_createfromconnectionstring
* İothubclient\_SendEventAsync
* İothubclient\_setmessagecallback
* İothubclient\_yok etme

Bu API 'ler **SimpleSample\_AMQP**içinde gösterilmiştir.

Benzer bir alt düzey API 'Ler kümesi de vardır.

* İothubclient\_ll\_createfromconnectionstring
* İothubclient\_ll\_SendEventAsync
* İothubclient\_ll\_setmessagecallback
* İothubclient\_ll\_yok etme

Alt düzey API 'Lerin önceki makalelerde açıklananla tamamen aynı şekilde çalıştığını unutmayın. Bir arka plan iş parçacığının olayları göndermeyi ve ileti almasını işlemesini istiyorsanız ilk API kümesini kullanabilirsiniz. IoT Hub veri gönderdiğinizde ve alırken açık denetim istiyorsanız, ikinci API kümesini kullanırsınız. Her iki API kümesi de **serileştirici** kitaplığı ile aynı şekilde çalışır.

**Seri hale getirici** kitaplığı ile alt düzey API 'lerin nasıl kullanıldığına ilişkin bir örnek için bkz. **\_SimpleSample http** uygulaması.

## <a name="additional-topics"></a>Ek konu başlıkları
Alternatif olarak, diğer cihaz kimlik bilgilerini ve yapılandırma seçeneklerini kullanarak özellik işleme, diğer birkaç konu daha vardır. Bunlar, [önceki bir makalede](iot-hub-device-sdk-c-iothubclient.md)ele alınan tüm konulardır. Ana nokta, bu özelliklerin tümünün **ıothubclient** kitaplığı ile çalıştıkları şekilde **serileştirici** kitaplığı ile aynı şekilde çalışır. Örneğin, modelinizdeki bir olaya özellikler eklemek istiyorsanız, **\_iothubmessage özelliklerini** kullanır ve daha önce açıklandığı gibi**AddOrUpdate**'i **eşleyin**\_:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Olayın **seri hale getirici** kitaplığından oluşturulup oluşturulmayacağı veya **Iothubclient** kitaplığı kullanılarak el ile oluşturulan bir önemi yoktur.

Diğer cihaz kimlik bilgileri için, **ıothubclient\_ll\_Create** komutunu ve bir ıothub **\_** **\_istemcisiayırmakiçiniothubclientcreatefromconnectionstringöğesinikullanın\_ TANıTıCı**.

Son olarak, **seri hale getirici** kitaplığını kullanıyorsanız, ıothubclient **\_\_ll SetOption** yapılandırma seçeneklerini **iothubclient** kitaplığı kullanırken yaptığınız gibi ayarlayabilirsiniz.

**Seri hale getirici** kitaplığı için benzersiz olan bir özellik, başlatma API 'lardır. Kitaplığı ile çalışmaya başlayabilmeniz için önce **seri hale getirici\_init**' i çağırmanız gerekir:

```C
serializer_init(NULL);
```

Bu, yalnızca **iothubclient\_createfromconnectionstring**öğesini çağırmadan önce yapılır.

Benzer şekilde, kitaplıkla çalışmayı tamamladığınızda, yaptığınız son çağrı **seri hale getirici\_'nin deinit**olur:

```C
serializer_deinit();
```

Aksi halde, yukarıda listelenen diğer tüm özellikler, **ıthubclient** kitaplığındaki gibi **serileştirici** kitaplığında aynı şekilde çalışır. Bu konuların herhangi biri hakkında daha fazla bilgi için bu serideki [önceki makaleye](iot-hub-device-sdk-c-iothubclient.md) bakın.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, **C Için Azure IoT cihaz SDK 'sında**bulunan **seri hale getirici** kitaplığının benzersiz yönleri ayrıntılı olarak açıklanmaktadır. Belirtilen bilgilerle olayları göndermek ve IoT Hub ileti almak için modelleri kullanma konusunda iyi bir fikir sahibi olmanız gerekir.

Bu Ayrıca, **C Için Azure IoT cihaz SDK 'sı**ile uygulamaların nasıl geliştirileceği ile ilgili üç parçalı seriyi de sonlanır. Bu, yalnızca başlamanıza ve API 'Lerin nasıl çalıştığını iyice anlayabilmeniz için yeterli bilgi almalıdır. Daha fazla bilgi için, SDK 'da burada kapsanmayan birkaç örnek vardır. Aksi takdirde, [Azure ıOT SDK belgeleri](https://github.com/Azure/azure-iot-sdk-c) daha fazla bilgi için iyi bir kaynaktır.

IoT Hub için geliştirme hakkında daha fazla bilgi için bkz. [Azure IoT SDK 'ları](iot-hub-devguide-sdks.md).

IoT Hub yeteneklerini daha fazla incelemek için bkz. [Azure IoT Edge ile uç CIHAZLARA AI dağıtma](../iot-edge/tutorial-simulate-device-linux.md).