---
title: IoT Tak ve Kullan köprüsü için bağdaştırıcı oluşturma | Microsoft Docs
description: IoT Tak ve Kullan Köprü bağdaştırıcısı bileşenlerini belirler. Kendi bağdaştırıcınızı yazarak köprüyü genişletmeyi öğrenin.
author: usivagna
ms.author: ugans
ms.date: 1/20/2021
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ac359e73ae71fd2163fb178caab4a2b5f908a008
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057465"
---
# <a name="extend-the-iot-plug-and-play-bridge"></a>IoT Tak ve Kullan köprüsünü genişletme
[Iot Tak ve Kullan köprüsü](concepts-iot-pnp-bridge.md#iot-plug-and-play-bridge-architecture) , bir ağ geçidine bağlı mevcut Cihazları IoT Hub 'ınıza bağlamanıza olanak tanır. IoT Tak ve Kullan arabirimlerini iliştirilmiş cihazlara eşlemek için köprü kullanırsınız. IoT Tak ve Kullan arabirimi, bir cihazın gönderdiği Telemetriyi, cihaz ile bulut arasında eşitlenen özellikleri ve cihazın yanıt verdiği komutları tanımlar. Açık kaynaklı köprü uygulamasını Windows veya Linux ağ geçitlerine yükleyebilir ve yapılandırabilirsiniz. Ayrıca, köprü Azure IoT Edge çalışma zamanı modülü olarak çalıştırılabilir.

Bu makalede nasıl yapılacağını ayrıntılı olarak açıklanmaktadır:

- IoT Tak ve Kullan köprüsünü bir bağdaştırıcı ile genişletin.
- Köprü bağdaştırıcısı için ortak geri çağırmaları uygulayın.

Köprünün nasıl kullanılacağını gösteren basit bir örnek için bkz. [Linux veya Windows üzerinde çalışan ıot Tak ve Kullan köprüsü örneğini IoT Hub olarak bağlama](howto-use-iot-pnp-bridge.md).

Bu makaledeki kılavuz ve örnekler, [Azure dijital TWINS](../digital-twins/overview.md) ve [IoT Tak ve kullan](overview-iot-plug-and-play.md)temel duyumuzu varsayar. Ayrıca, bu makalede [ıot Tak ve Kullan köprüsü oluşturma ve dağıtma](howto-build-deploy-extend-pnp-bridge.md)hakkında daha fazla bilginiz vardır.

## <a name="design-guide-to-extend-the-iot-plug-and-play-bridge-with-an-adapter"></a>IoT Tak ve Kullan köprüsünü bir bağdaştırıcıyla genişletmek için tasarım kılavuzu

Köprünün yeteneklerini genişletmek için kendi köprü bağdaştırıcılarınızı yazabilirsiniz.

Köprü bağdaştırıcıları şu amaçlarla kullanır:

- Bir cihaz ile bulut arasında bağlantı kurun.
- Bir cihaz ile bulut arasında veri akışını etkinleştirin.
- Cihaz yönetimini buluttan etkinleştirin.

Her köprü bağdaştırıcısında şunları yapmanız gerekir:

- Dijital bir TWINS arabirimi oluşturun.
- Cihaz tarafı işlevselliğini telemetri, Özellikler ve komutlar gibi bulut tabanlı yeteneklere bağlamak için arabirimini kullanın.
- Cihaz donanımı veya üretici yazılımıyla denetim ve veri iletişimi kurun.

Her köprü bağdaştırıcısı, bağdaştırıcının cihaza bağlanıp cihazla etkileşime girdiği belirli bir cihaz türü ile etkileşime girer. Bir cihazla iletişim, bir anlaşma Protokolü kullandığından bile, bir köprü bağdaştırıcısında verileri cihazdan yorumlayacağınız birden çok yol olabilir. Bu senaryoda, Köprü bağdaştırıcısı bağdaştırıcının verileri ayrıştırmak için kullanması gereken *arabirim yapılandırmasını* belirleyebilmek için yapılandırma dosyasındaki bağdaştırıcının bilgilerini kullanır.

Bir köprü bağdaştırıcısı, cihazla etkileşim kurmak için cihaz ve API 'Ler tarafından desteklenen bir iletişim protokolü ve temel alınan işletim sistemi ya da cihaz satıcısı tarafından kullanılır.

Bir köprü bağdaştırıcısı, bulutla etkileşimde bulunmak için Azure IoT cihaz C SDK 'Sı tarafından telemetri göndermek, dijital ikizi arabirimleri oluşturmak, özellik güncelleştirmeleri göndermek ve özellik güncelleştirmeleri ve komutları için geri çağırma işlevleri oluşturmak için tarafından sunulan API 'Leri kullanır.

### <a name="create-a-bridge-adapter"></a>Köprü bağdaştırıcısı oluşturma

Köprü, [_PNP_ADAPTER](https://github.com/Azure/iot-plug-and-play-bridge/blob/9964f7f9f77ecbf4db3b60960b69af57fd83a871/pnpbridge/src/pnpbridge/inc/pnpadapter_api.h#L296) arabiriminde tanımlanan API 'leri uygulamak için bir köprü bağdaştırıcısı bekler:

```c
typedef struct _PNP_ADAPTER {
  // Identity of the IoT Plug and Play adapter that is retrieved from the config
  const char* identity;

  PNPBRIDGE_ADAPTER_CREATE createAdapter;
  PNPBRIDGE_COMPONENT_CREATE createPnpComponent;
  PNPBRIDGE_COMPONENT_START startPnpComponent;
  PNPBRIDGE_COMPONENT_STOP stopPnpComponent;
  PNPBRIDGE_COMPONENT_DESTROY destroyPnpComponent;
  PNPBRIDGE_ADAPTER_DESTOY destroyAdapter;
} PNP_ADAPTER, * PPNP_ADAPTER;
```

Bu arabirimde:

- `PNPBRIDGE_ADAPTER_CREATE` bağdaştırıcıyı oluşturur ve arabirim Yönetimi kaynaklarını ayarlar. Bağdaştırıcı ayrıca bağdaştırıcı oluşturmak için genel bağdaştırıcı parametrelerine de sahip olabilir. Bu işlev, tek bir bağdaştırıcı için bir kez çağrılır.
- `PNPBRIDGE_COMPONENT_CREATE` Dijital ikizi istemci arabirimlerini oluşturur ve geri çağırma işlevlerini bağlar. Bağdaştırıcı, cihaza iletişim kanalını başlatır. Bağdaştırıcı, telemetri akışını etkinleştirmek için kaynakları ayarlayabilir, ancak çağrılana kadar raporlama telemetrisi başlatamaz `PNPBRIDGE_COMPONENT_START` . Bu işlev, yapılandırma dosyasındaki her arabirim bileşeni için bir kez çağrılır.
- `PNPBRIDGE_COMPONENT_START` Köprü bağdaştırıcısının, cihazdan dijital ikizi istemcisine telemetri iletmeye başlamasını sağlamak için çağrılır. Bu işlev, yapılandırma dosyasındaki her arabirim bileşeni için bir kez çağrılır.
- `PNPBRIDGE_COMPONENT_STOP` Telemetri akışını sonlandırır.
- `PNPBRIDGE_COMPONENT_DESTROY` Digital ikizi istemcisini ve ilişkili arabirim kaynaklarını yok eder. Bu işlev, köprü bozuk olduğunda veya önemli bir hata oluştuğunda yapılandırma dosyasındaki her arabirim bileşeni için bir kez çağrılır.
- `PNPBRIDGE_ADAPTER_DESTROY` Köprü bağdaştırıcısı kaynaklarını temizler.

### <a name="bridge-core-interaction-with-bridge-adapters"></a>Köprü bağdaştırıcıları ile Köprü çekirdek etkileşimi

Aşağıdaki listede köprü başlatıldığında ne olacağı özetlenmektedir.

1. Köprü başlatıldığında, köprü bağdaştırıcı Yöneticisi yapılandırma dosyasında tanımlanan her arabirim bileşenini arar ve `PNPBRIDGE_ADAPTER_CREATE` uygun bağdaştırıcı üzerinde çağırır. Bağdaştırıcı, çeşitli *arabirim yapılandırmalarını* destekleyecek kaynakları ayarlamak için genel bağdaştırıcı yapılandırma parametrelerini kullanabilir.
1. Yapılandırma dosyasındaki her cihaz için, köprü Yöneticisi uygun köprü bağdaştırıcısını çağırarak arabirim oluşturmayı başlatır `PNPBRIDGE_COMPONENT_CREATE` .
1. Bağdaştırıcı, arabirim bileşeni için tüm isteğe bağlı bağdaştırıcı yapılandırma ayarlarını alır ve bu bilgileri cihazla bağlantı kurmak için kullanır.
1. Bağdaştırıcı, dijital ikizi istemci arabirimlerini oluşturur ve özellik güncelleştirmeleri ve komutları için geri çağırma işlevlerini bağlar. Cihaz bağlantıları kurulması, dijital ikizi arabirimi oluşturma başarılı olduktan sonra geri çağırmaların döndürülmesini engellemez. Etkin cihaz bağlantısı, köprünün oluşturduğu etkin arabirim istemcisinden bağımsızdır. Bağlantı başarısız olursa, bağdaştırıcı cihazın etkin olmadığını varsayar. Köprü bağdaştırıcısı bu bağlantıyı yapmayı yeniden denemeyi tercih edebilir.
1. Köprü bağdaştırıcısı Yöneticisi, yapılandırma dosyasında belirtilen tüm arabirim bileşenlerini oluşturduktan sonra, tüm arabirimleri Azure IoT Hub kaydeder. Kayıt, engelleyici, zaman uyumsuz bir çağrıdır. Arama tamamlandığında, köprü bağdaştırıcısında, buluttan özellik ve komut geri çağırmaları işlemeye başlayabileceğiniz bir geri çağırma tetikler.
1. Daha sonra köprü bağdaştırıcısı Yöneticisi `PNPBRIDGE_INTERFACE_START` her bileşeni çağırır ve Köprü bağdaştırıcısı, dijital ikizi istemcisine rapor Telemetriyi başlatır.

### <a name="design-guidelines"></a>Tasarım yönergeleri

Yeni bir köprü bağdaştırıcısı geliştirirken aşağıdaki yönergeleri izleyin:

- Hangi cihaz yeteneklerinin desteklendiğini ve bu bağdaştırıcıyı kullanan bileşenlerin arabirim tanımının nasıl göründüğünü saptayın.
- Bağdaştırıcınızda yapılandırma dosyasında tanımlanan arabirim ve genel parametreleri saptayın.
- Bileşen özelliklerini ve komutlarını desteklemek için gereken alt düzey cihaz iletişimini belirler.
- Bağdaştırıcının ham verileri cihazdan nasıl ayrıştıracağını ve IoT Tak ve Kullan arabirim tanımının belirttiği telemetri türlerine nasıl dönüştürüleceğini belirler.
- Daha önce açıklanan Köprü bağdaştırıcısı arabirimini uygulayın.
- Yeni bağdaştırıcıyı bağdaştırıcı bildirimine ekleyin ve köprü oluşturun.

### <a name="enable-a-new-bridge-adapter"></a>Yeni bir köprü bağdaştırıcısını etkinleştir

[Adapter_manifest. c](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/shared/adapter_manifest.c)' de bir başvuru ekleyerek, köprüdeki bağdaştırıcıları etkinleştirebilirsiniz:

```c
  extern PNP_ADAPTER MyPnpAdapter;
  PPNP_ADAPTER PNP_ADAPTER_MANIFEST[] = {
    .
    .
    &MyPnpAdapter
  }
```

> [!IMPORTANT]
> Köprü bağdaştırıcısı geri çağırmaları ardışık olarak çağrılır. Bu, köprü çekirdeğin ilerleme yapmasını önlediği için, bir bağdaştırıcı geri çağırma işlemini engelmemelidir.

### <a name="sample-camera-adapter"></a>Örnek kamera bağdaştırıcısı

[Kamera bağdaştırıcısı Benioku dosyası](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/Camera/readme.md) , etkinleştirebileceğiniz bir örnek kamera bağdaştırıcısı tanımlar.

## <a name="code-examples-for-common-adapter-scenarioscallbacks"></a>Ortak bağdaştırıcı senaryoları/geri çağırmaları için kod örnekleri

Aşağıdaki bölümde, bir köprü bağdaştırıcısının bir dizi yaygın senaryolar ve kullanımları için geri çağırmaları nasıl uygulayacağından ilgili ayrıntılar sağlanmaktadır. Bu bölümde aşağıdaki geri çağrılar ele alınır:
- [Özellik güncelleştirmesini al (bulutu cihaza)](#receive-property-update-cloud-to-device)
- [Bir özellik güncelleştirmesini (cihazdan buluta) bildir](#report-a-property-update-device-to-cloud)
- [Telemetriyi (cihazdan buluta) gönderin](#send-telemetry-device-to-cloud)
- [Buluttan komut güncelleştirme geri aramasını alın ve cihaz tarafında (buluttan cihaza) işlem yapın](#receive-command-update-callback-from-the-cloud-and-process-it-on-the-device-side-cloud-to-device)
- [Cihaz tarafında (cihazdan buluta) komut güncelleştirmesine yanıt verme](#respond-to-command-update-on-the-device-side-device-to-cloud)

Aşağıdaki örnekler, [ortam algılayıcısı örnek bağdaştırıcısını](https://github.com/Azure/iot-plug-and-play-bridge/tree/master/pnpbridge/src/adapters/samples/environmental_sensor)temel alır.

### <a name="receive-property-update-cloud-to-device"></a>Özellik güncelleştirmesini al (bulutu cihaza)
İlk adım bir geri çağırma işlevi kaydetmasıdır:

```c
PnpComponentHandleSetPropertyUpdateCallback(BridgeComponentHandle, EnvironmentSensor_ProcessPropertyUpdate);
```
Sonraki adım, cihazdaki Özellik güncelleştirmesini okumak için geri çağırma işlevinin uygulanması için kullanılır:

```c
void EnvironmentSensor_ProcessPropertyUpdate(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    void* userContextCallback
)
{
  // User context for the callback is set to the IoT Hub client handle, and therefore can be type-cast to the client handle type
    SampleEnvironmentalSensor_ProcessPropertyUpdate(userContextCallback, PropertyName, PropertyValue, version, PnpComponentHandle);
}

// SampleEnvironmentalSensor_ProcessPropertyUpdate receives updated properties from the server.  This implementation
// acts as a simple dispatcher to the functions to perform the actual processing.
void SampleEnvironmentalSensor_ProcessPropertyUpdate(
    void * ClientHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
  if (strcmp(PropertyName, sampleEnvironmentalSensorPropertyBrightness) == 0)
    {
        SampleEnvironmentalSensor_BrightnessCallback(ClientHandle, PropertyName, PropertyValue, version, PnpComponentHandle);
    }
    else
    {
        // If the property is not implemented by this interface, presently we only record a log message but do not have a mechanism to report back to the service
        LogError("Environmental Sensor Adapter:: Property name <%s> is not associated with this interface", PropertyName);
    }
}

// Process a property update for bright level.
static void SampleEnvironmentalSensor_BrightnessCallback(
    void * ClientHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
    IOTHUB_CLIENT_RESULT iothubClientResult;
    STRING_HANDLE jsonToSend = NULL;
    char targetBrightnessString[32];

    LogInfo("Environmental Sensor Adapter:: Brightness property invoked...");

    PENVIRONMENT_SENSOR EnvironmentalSensor = PnpComponentHandleGetContext(PnpComponentHandle);

    if (json_value_get_type(PropertyValue) != JSONNumber)
    {
        LogError("JSON field %s is not a number", PropertyName);
    }
    else if(EnvironmentalSensor == NULL || EnvironmentalSensor->SensorState == NULL)
    {
        LogError("Environmental sensor device context not initialized correctly.");
    }
    else if (SampleEnvironmentalSensor_ValidateBrightness(json_value_get_number(PropertyValue)))
    {
        EnvironmentalSensor->SensorState->brightness = (int) json_value_get_number(PropertyValue);
        if (snprintf(targetBrightnessString, sizeof(targetBrightnessString), 
            g_environmentalSensorBrightnessResponseFormat, EnvironmentalSensor->SensorState->brightness) < 0)
        {
            LogError("Unable to create target brightness string for reporting result");
        }
        else if ((jsonToSend = PnP_CreateReportedPropertyWithStatus(EnvironmentalSensor->SensorState->componentName,
                    PropertyName, targetBrightnessString, PNP_STATUS_SUCCESS, g_environmentalSensorPropertyResponseDescription,
                    version)) == NULL)
        {
            LogError("Unable to build reported property response");
        }
        else
        {
            const char* jsonToSendStr = STRING_c_str(jsonToSend);
            size_t jsonToSendStrLen = strlen(jsonToSendStr);

            if ((iothubClientResult = SampleEnvironmentalSensor_RouteReportedState(ClientHandle, PnpComponentHandle, (const unsigned char*)jsonToSendStr, jsonToSendStrLen,
                                        SampleEnvironmentalSensor_PropertyCallback,
                                        (void*) &EnvironmentalSensor->SensorState->brightness)) != IOTHUB_CLIENT_OK)
            {
                LogError("Environmental Sensor Adapter:: SampleEnvironmentalSensor_RouteReportedState for brightness failed, error=%d", iothubClientResult);
            }
            else
            {
                LogInfo("Environmental Sensor Adapter:: Successfully queued Property update for Brightness for component=%s", EnvironmentalSensor->SensorState->componentName);
            }

            STRING_delete(jsonToSend);
        }
    }
}

```

### <a name="report-a-property-update-device-to-cloud"></a>Bir özellik güncelleştirmesini (cihazdan buluta) bildir
Bileşeniniz oluşturulduktan sonra herhangi bir noktada, cihazınız şu durumla özellikleri buluta rapor edebilir: 
```c
// Environmental sensor's read-only property, device state indiciating whether its online or not
//
static const char sampleDeviceStateProperty[] = "state";
static const unsigned char sampleDeviceStateData[] = "true";
static const int sampleDeviceStateDataLen = sizeof(sampleDeviceStateData) - 1;

// Sends a reported property for device state of this simulated device.
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_ReportDeviceStateAsync(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const char * ComponentName)
{

    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;
    STRING_HANDLE jsonToSend = NULL;

    if ((jsonToSend = PnP_CreateReportedProperty(ComponentName, sampleDeviceStateProperty, (const char*) sampleDeviceStateData)) == NULL)
    {
        LogError("Unable to build reported property response for propertyName=%s, propertyValue=%s", sampleDeviceStateProperty, sampleDeviceStateData);
    }
    else
    {
        const char* jsonToSendStr = STRING_c_str(jsonToSend);
        size_t jsonToSendStrLen = strlen(jsonToSendStr);

        if ((iothubClientResult = SampleEnvironmentalSensor_RouteReportedState(NULL, PnpComponentHandle, (const unsigned char*)jsonToSendStr, jsonToSendStrLen,
            SampleEnvironmentalSensor_PropertyCallback, (void*)sampleDeviceStateProperty)) != IOTHUB_CLIENT_OK)
        {
            LogError("Environmental Sensor Adapter:: Unable to send reported state for property=%s, error=%d",
                                sampleDeviceStateProperty, iothubClientResult);
        }
        else
        {
            LogInfo("Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=%s, propertyValue=%s",
                        sampleDeviceStateProperty, sampleDeviceStateData);
        }

        STRING_delete(jsonToSend);
    }

    return iothubClientResult;
}


// Routes the reported property for device or module client. This function can be called either by passing a valid client handle or by passing
// a NULL client handle after components have been started such that the client handle can be extracted from the PnpComponentHandle
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_RouteReportedState(
    void * ClientHandle,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const unsigned char * ReportedState,
    size_t Size,
    IOTHUB_CLIENT_REPORTED_STATE_CALLBACK ReportedStateCallback,
    void * UserContextCallback)
{
    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;

    PNP_BRIDGE_CLIENT_HANDLE clientHandle = (ClientHandle != NULL) ?
            (PNP_BRIDGE_CLIENT_HANDLE) ClientHandle : PnpComponentHandleGetClientHandle(PnpComponentHandle);

    if ((iothubClientResult = PnpBridgeClient_SendReportedState(clientHandle, ReportedState, Size,
            ReportedStateCallback, UserContextCallback)) != IOTHUB_CLIENT_OK)
    {
        LogError("IoTHub client call to _SendReportedState failed with error code %d", iothubClientResult);
        goto exit;
    }
    else
    {
        LogInfo("IoTHub client call to _SendReportedState succeeded");
    }

exit:
    return iothubClientResult;
}

```

### <a name="send-telemetry-device-to-cloud"></a>Telemetriyi (cihazdan buluta) gönderin
```c
//
// SampleEnvironmentalSensor_SendTelemetryMessagesAsync is periodically invoked by the caller to
// send telemetry containing the current temperature and humidity (in both cases random numbers
// so this sample will work on platforms without these sensors).
//
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_SendTelemetryMessagesAsync(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
    IOTHUB_CLIENT_RESULT result = IOTHUB_CLIENT_OK;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;
    PENVIRONMENT_SENSOR device = PnpComponentHandleGetContext(PnpComponentHandle);

    float currentTemperature = 20.0f + ((float)rand() / RAND_MAX) * 15.0f;
    float currentHumidity = 60.0f + ((float)rand() / RAND_MAX) * 20.0f;

    char currentMessage[128];
    sprintf(currentMessage, "{\"%s\":%.3f, \"%s\":%.3f}", SampleEnvironmentalSensor_TemperatureTelemetry, 
            currentTemperature, SampleEnvironmentalSensor_HumidityTelemetry, currentHumidity);


    if ((messageHandle = PnP_CreateTelemetryMessageHandle(device->SensorState->componentName, currentMessage)) == NULL)
    {
        LogError("Environmental Sensor Adapter:: PnP_CreateTelemetryMessageHandle failed.");
    }
    else if ((result = SampleEnvironmentalSensor_RouteSendEventAsync(PnpComponentHandle, messageHandle,
            SampleEnvironmentalSensor_TelemetryCallback, device)) != IOTHUB_CLIENT_OK)
    {
        LogError("Environmental Sensor Adapter:: SampleEnvironmentalSensor_RouteSendEventAsync failed, error=%d", result);
    }

    IoTHubMessage_Destroy(messageHandle);

    return result;
}

// Routes the sending asynchronous events for device or module client
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_RouteSendEventAsync(
        PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
        IOTHUB_MESSAGE_HANDLE EventMessageHandle,
        IOTHUB_CLIENT_EVENT_CONFIRMATION_CALLBACK EventConfirmationCallback,
        void * UserContextCallback)
{
    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;
    PNP_BRIDGE_CLIENT_HANDLE clientHandle = PnpComponentHandleGetClientHandle(PnpComponentHandle);
    if ((iothubClientResult = PnpBridgeClient_SendEventAsync(clientHandle, EventMessageHandle,
            EventConfirmationCallback, UserContextCallback)) != IOTHUB_CLIENT_OK)
    {
        LogError("IoTHub client call to _SendEventAsync failed with error code %d", iothubClientResult);
        goto exit;
    }
    else
    {
        LogInfo("IoTHub client call to _SendEventAsync succeeded");
    }

exit:
    return iothubClientResult;
}

```
### <a name="receive-command-update-callback-from-the-cloud-and-process-it-on-the-device-side-cloud-to-device"></a>Buluttan komut güncelleştirme geri aramasını alın ve cihaz tarafında (buluttan cihaza) işlem yapın
```c
// SampleEnvironmentalSensor_ProcessCommandUpdate receives commands from the server.  This implementation acts as a simple dispatcher
// to the functions to perform the actual processing.
int SampleEnvironmentalSensor_ProcessCommandUpdate(
    PENVIRONMENT_SENSOR EnvironmentalSensor,
    const char* CommandName,
    JSON_Value* CommandValue,
    unsigned char** CommandResponse,
    size_t* CommandResponseSize)
{
    if (strcmp(CommandName, sampleEnvironmentalSensorCommandBlink) == 0)
    {
        return SampleEnvironmentalSensor_BlinkCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else if (strcmp(CommandName, sampleEnvironmentalSensorCommandTurnOn) == 0)
    {
        return SampleEnvironmentalSensor_TurnOnLightCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else if (strcmp(CommandName, sampleEnvironmentalSensorCommandTurnOff) == 0)
    {
        return SampleEnvironmentalSensor_TurnOffLightCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else
    {
        // If the command is not implemented by this interface, by convention we return a 404 error to server.
        LogError("Environmental Sensor Adapter:: Command name <%s> is not associated with this interface", CommandName);
        return SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_NotImplemented);
    }
}

// Implement the callback to process the command "blink". Information pertaining to the request is
// specified in the CommandValue parameter, and the callback fills out data it wishes to
// return to the caller on the service in CommandResponse.

static int SampleEnvironmentalSensor_BlinkCallback(
    PENVIRONMENT_SENSOR EnvironmentalSensor,
    JSON_Value* CommandValue,
    unsigned char** CommandResponse,
    size_t* CommandResponseSize)
{
    int result = PNP_STATUS_SUCCESS;
    int BlinkInterval = 0;

    LogInfo("Environmental Sensor Adapter:: Blink command invoked. It has been invoked %d times previously", EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled);

    if (json_value_get_type(CommandValue) != JSONNumber)
    {
        LogError("Cannot retrieve blink interval for blink command");
        result = PNP_STATUS_BAD_FORMAT;
    }
    else
    {
        BlinkInterval = (int)json_value_get_number(CommandValue);
        LogInfo("Environmental Sensor Adapter:: Blinking with interval=%d second(s)", BlinkInterval);
        EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled++;
        EnvironmentalSensor->SensorState->blinkInterval = BlinkInterval;

        result = SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_BlinkResponse);
    }

    return result;
}

```
### <a name="respond-to-command-update-on-the-device-side-device-to-cloud"></a>Cihaz tarafında (cihazdan buluta) komut güncelleştirmesine yanıt verme

```c
    static int SampleEnvironmentalSensor_BlinkCallback(
        PENVIRONMENT_SENSOR EnvironmentalSensor,
        JSON_Value* CommandValue,
        unsigned char** CommandResponse,
        size_t* CommandResponseSize)
    {
        int result = PNP_STATUS_SUCCESS;
        int BlinkInterval = 0;
    
        LogInfo("Environmental Sensor Adapter:: Blink command invoked. It has been invoked %d times previously", EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled);
    
        if (json_value_get_type(CommandValue) != JSONNumber)
        {
            LogError("Cannot retrieve blink interval for blink command");
            result = PNP_STATUS_BAD_FORMAT;
        }
        else
        {
            BlinkInterval = (int)json_value_get_number(CommandValue);
            LogInfo("Environmental Sensor Adapter:: Blinking with interval=%d second(s)", BlinkInterval);
            EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled++;
            EnvironmentalSensor->SensorState->blinkInterval = BlinkInterval;
    
            result = SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_BlinkResponse);
        }
    
        return result;
    }
    
    // SampleEnvironmentalSensor_SetCommandResponse is a helper that fills out a command response
    static int SampleEnvironmentalSensor_SetCommandResponse(
        unsigned char** CommandResponse,
        size_t* CommandResponseSize,
        const unsigned char* ResponseData)
    {
        int result = PNP_STATUS_SUCCESS;
        if (ResponseData == NULL)
        {
            LogError("Environmental Sensor Adapter:: Response Data is empty");
            *CommandResponseSize = 0;
            return PNP_STATUS_INTERNAL_ERROR;
        }
    
        *CommandResponseSize = strlen((char*)ResponseData);
        memset(CommandResponse, 0, sizeof(*CommandResponse));
    
        // Allocate a copy of the response data to return to the invoker. Caller will free this.
        if (mallocAndStrcpy_s((char**)CommandResponse, (char*)ResponseData) != 0)
        {
            LogError("Environmental Sensor Adapter:: Unable to allocate response data");
            result = PNP_STATUS_INTERNAL_ERROR;
        }
    
        return result;
}
```

## <a name="next-steps"></a>Sonraki adımlar

IoT Tak ve Kullan köprüsü hakkında daha fazla bilgi edinmek için [ıot Tak ve Kullan köprüsü](https://github.com/Azure/iot-plug-and-play-bridge) GitHub deposunu ziyaret edin.
