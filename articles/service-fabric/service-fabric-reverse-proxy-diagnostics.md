---
title: Azure Service Fabric ters proxy tanılaması | Microsoft Docs
description: İstek işlemenin ters proxy 'de nasıl izleneceğini ve tanılacağınızı öğrenin.
services: service-fabric
documentationcenter: .net
author: kavyako
manager: vipulm
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 08/08/2017
ms.author: kavyako
ms.openlocfilehash: 6074b799e992371d41de050f68690e450f008789
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933961"
---
# <a name="monitor-and-diagnose-request-processing-at-the-reverse-proxy"></a>İstek işlemesini ters ara sunucuda izleme ve tanılama

Service Fabric 5,7 sürümünden itibaren, ters proxy olayları koleksiyon için kullanılabilir. Olaylar, yalnızca ters ara sunucuda ve hem başarılı hem de başarısız istekler için girişlerle birlikte ayrıntılı olayları içeren ikinci kanalda yalnızca istek işleme hatası ile ilgili hata olaylarını içeren iki kanalda kullanılabilir.

Yerel ve Azure Service Fabric kümelerinde bu kanallardan olay toplamayı etkinleştirmek için [ters proxy olayları toplama](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations) bölümüne bakın.

## <a name="troubleshoot-using-diagnostics-logs"></a>Tanılama günlüklerini kullanarak sorun giderme
Aşağıda, bir birinin karşılaşabileceği ortak hata günlüklerini yorumlama hakkında bazı örnekler verilmiştir:

1. Ters proxy, yanıt durum kodu 504 (zaman aşımı) döndürüyor.

    Bir nedenden dolayı hizmetin istek zaman aşımı süresi içinde yanıt vermesi başarısız olabilir.
   Aşağıdaki ilk olay, ters proxy 'de alınan isteğin ayrıntılarını günlüğe kaydeder. 
   İkinci olay, "iç hata = ERROR_WINHTTP_TIMEOUT" nedeniyle isteğin hizmete iletilirken başarısız olduğunu gösterir 

    Yük şunları içerir:

   * **TraceID**: Bu GUID, tek bir istekle eşleşen tüm olayları ilişkilendirmek için kullanılabilir. Aşağıdaki iki olay olan TraceID = **2f87b722-e254-4AC2-A802-fd315c1a0271**, aynı isteğe ait olduğunu kesin.
   * **requestURL**: ISTEĞIN gönderildiği URL (ters proxy URL 'si).
   * **fiil**: http fiili.
   * **RemoteAddress**: isteği gönderen istemcinin adresi.
   * **resolvedServiceUrl**: gelen isteğin çözümlendiği hizmet uç noktası URL 'si. 
   * **ErrorDetails**: hata hakkında ek bilgi.

     ```
     {
     "Timestamp": "2017-07-20T15:57:59.9871163-07:00",
     "ProviderName": "Microsoft-ServiceFabric",
     "Id": 51477,
     "Message": "2f87b722-e254-4ac2-a802-fd315c1a0271 Request url = https://localhost:19081/LocationApp/LocationFEService?zipcode=98052, verb = GET, remote (client) address = ::1, resolved service url = Https://localhost:8491/LocationApp/?zipcode=98052, request processing start time =     15:58:00.074114 (745,608.196 MSec) ",
     "ProcessId": 57696,
     "Level": "Informational",
     "Keywords": "0x1000000000000021",
     "EventName": "ReverseProxy",
     "ActivityID": null,
     "RelatedActivityID": null,
     "Payload": {
      "traceId": "2f87b722-e254-4ac2-a802-fd315c1a0271",
      "requestUrl": "https://localhost:19081/LocationApp/LocationFEService?zipcode=98052",
      "verb": "GET",
      "remoteAddress": "::1",
      "resolvedServiceUrl": "Https://localhost:8491/LocationApp/?zipcode=98052",
      "requestStartTime": "2017-07-20T15:58:00.0741142-07:00"
     }
     }

     {
     "Timestamp": "2017-07-20T16:00:01.3173605-07:00",
     ...
     "Message": "2f87b722-e254-4ac2-a802-fd315c1a0271 Error while forwarding request to service: response status code = 504, description = Reverse proxy Timeout, phase = FinishSendRequest, internal error = ERROR_WINHTTP_TIMEOUT ",
     ...
     "Payload": {
      "traceId": "2f87b722-e254-4ac2-a802-fd315c1a0271",
      "statusCode": 504,
      "description": "Reverse Proxy Timeout",
      "sendRequestPhase": "FinishSendRequest",
      "errorDetails": "internal error = ERROR_WINHTTP_TIMEOUT"
     }
     }
     ```

2. Ters proxy, yanıt durum kodu 404 (bulunamadı) döndürüyor. 
    
    Bu, eşleşen hizmet uç noktasını bulamadığı için ters proxy 'nin 404 döndürdüğü örnek bir olay aşağıda verilmiştir.
    İlgilendiğiniz yük girişleri şunlardır:
   * **Processrequestphase**: hata oluştuğunda istek işleme sırasındaki aşamayı gösterir, ***trygetendpoint*** ör iletmek için hizmet uç noktası getirilmeye çalışılırken. 
   * **ErrorDetails**: uç nokta arama ölçütlerini listeler. Burada listenerName belirtilen = **Frontendlistener** olduğunu ve çoğaltma uç noktası listesinin yalnızca **oldlistener**adlı bir dinleyici içerdiğini görebilirsiniz.
    
     ```
     {
     ...
     "Message": "c1cca3b7-f85d-4fef-a162-88af23604343 Error while processing request, cannot forward to service: request url = https://localhost:19081/LocationApp/LocationFEService?ListenerName=FrontEndListener&zipcode=98052, verb = GET, remote (client) address = ::1, request processing start time = 16:43:02.686271 (3,448,220.353 MSec), error = FABRIC_E_ENDPOINT_NOT_FOUND, message = , phase = TryGetEndoint, SecureOnlyMode = false, gateway protocol = https, listenerName = FrontEndListener, replica endpoint = {\"Endpoints\":{\"\":\"Https:\/\/localhost:8491\/LocationApp\/\"}} ",
     "ProcessId": 57696,
     "Level": "Warning",
     "EventName": "ReverseProxy",
     "Payload": {
      "traceId": "c1cca3b7-f85d-4fef-a162-88af23604343",
      "requestUrl": "https://localhost:19081/LocationApp/LocationFEService?ListenerName=NewListener&zipcode=98052",
      ...
      "processRequestPhase": "TryGetEndoint",
      "errorDetails": "SecureOnlyMode = false, gateway protocol = https, listenerName = FrontEndListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Https:\/\/localhost:8491\/LocationApp\/\"}}"
     }
     }
     ```
     Ters proxy 'nin 404 döndürdüğü başka bir örnek: ApplicationGateway\Http yapılandırma parametresi **Secureonlymode** , **https**üzerinde dinleme yapan ters proxy ile true olarak ayarlanır, ancak tüm çoğaltma uç noktaları güvenli değildir ( HTTP üzerinde dinleme).
     Ters proxy, isteği iletmek için HTTPS üzerinde dinleme yapan bir uç nokta bulamadığı için 404 döndürür. Olay yükünde parametreleri çözümlemek sorunu daraltmaya yardımcı olur:
    
     ```
      "errorDetails": "SecureOnlyMode = true, gateway protocol = https, listenerName = NewListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Http:\/\/localhost:8491\/LocationApp\/\", \"NewListener\":\"Http:\/\/localhost:8492\/LocationApp\/\"}}"
     ```

3. Ters ara sunucuya yönelik istek bir zaman aşımı hatasıyla başarısız oluyor. 
    Olay günlükleri alınan istek ayrıntılarına sahip bir olay içerir (burada gösterilmez).
    Sonraki olay, hizmetin 404 durum kodu ile yanıt verdiğini ve ters proxy 'nin yeniden çözümlenme işlemini başlattığını gösterir. 

    ```
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Request to service returned: status code = 404, status description = , Reresolving ",
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "statusCode": 404,
        "statusDescription": ""
      }
    }
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Re-resolved service url = Https://localhost:8491/LocationApp/?zipcode=98052 ",
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "requestUrl": "Https://localhost:8491/LocationApp/?zipcode=98052"
      }
    }
    ```
    Tüm olayları toplarken, her çözümle ve ileriye doğru girişimi gösteren bir olay eğitimi görürsünüz.
    Serideki son olay, istek işlemenin başarısız bir zaman aşımı ile başarısız olduğunu gösterir ve bu da başarılı çözümleme denemeleri sayısıdır.
    
    > [!NOTE]
    > Varsayılan olarak, ayrıntılı kanal olay koleksiyonunun devre dışı bırakılması ve gereksinim temelinde sorun giderme için etkinleştirilmesi önerilir.

    ```
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Error while processing request: number of successful resolve attempts = 12, error = FABRIC_E_TIMEOUT, message = , phase = ResolveServicePartition,  ",
      "EventName": "ReverseProxy",
      ...
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "resolveCount": 12,
        "errorval": -2147017729,
        "errorMessage": "",
        "processRequestPhase": "ResolveServicePartition",
        "errorDetails": ""
      }
    }
    ```
    
    Koleksiyon yalnızca kritik/hata olayları için etkinleştirilirse, zaman aşımı ve çözümleme denemelerinin sayısı hakkındaki ayrıntıları içeren bir olay görürsünüz. 
    
    Kullanıcıya 404 durum kodu gönderilmesini sağlayan hizmetler yanıta bir "X-ServiceFabric" üst bilgisi eklemeli. Üst bilgi yanıta eklendikten sonra, ters proxy durum kodunu istemciye geri iletir.  

4. İstemcinin isteği bağlantısı kesildiğinde oluşan durumlar.

    Ters proxy istemciye yanıtı iletirken, ancak istemcinin bağlantısı kesildiğinde aşağıdaki olay kaydedilir:

    ```
    {
      ...
      "Message": "6e2571a3-14a8-4fc7-93bb-c202c23b50b8 Unable to send response to client: phase = SendResponseHeaders, error = -805306367, internal error = ERROR_SUCCESS ",
      "ProcessId": 57696,
      "Level": "Warning",
      ...
      "EventName": "ReverseProxy",
      "Payload": {
        "traceId": "6e2571a3-14a8-4fc7-93bb-c202c23b50b8",
        "sendResponsePhase": "SendResponseHeaders",
        "errorval": -805306367,
        "winHttpError": "ERROR_SUCCESS"
      }
    }
    ```
5. Ters proxy 404 FABRIC_E_SERVICE_DOES_NOT_EXIST döndürür

    FABRIC_E_SERVICE_DOES_NOT_EXIST hatası, hizmet bildiriminde hizmet uç noktası için URI şeması belirtilmemişse döndürülür.

    ```
    <Endpoint Name="ServiceEndpointHttp" Port="80" Protocol="http" Type="Input"/>
    ```

    Sorunu çözmek için, bildirimde URI şemasını belirtin.
    ```
    <Endpoint Name="ServiceEndpointHttp" UriScheme="http" Port="80" Protocol="http" Type="Input"/>
    ```

> [!NOTE]
> WebSocket istek işlemeyle ilgili olaylar şu anda günlüğe kaydedilmez. Bu, sonraki sürüme eklenecektir.

## <a name="next-steps"></a>Sonraki adımlar
* Azure kümelerinde günlük toplamayı etkinleştirmek için [Windows Azure tanılama kullanan olay toplama ve toplama](service-fabric-diagnostics-event-aggregation-wad.md) .
* Visual Studio 'da Service Fabric olaylarını görüntülemek için bkz. [yerel olarak izleme ve tanılama](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).
* Farklı hizmet sertifikası doğrulama seçenekleriyle güvenli ters proxy 'yi yapılandırmak üzere Azure Resource Manager şablon örnekleri için [güvenli hizmetlere bağlanmak üzere ters proxy 'Yi yapılandırma](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample#configure-reverse-proxy-to-connect-to-secure-services) konusuna bakın.
* Daha fazla bilgi edinmek için [Service Fabric ters proxy](service-fabric-reverseproxy.md) 'yi okuyun.
