---
title: .NET-Azure Event Hubs için mesajlaşma özel durumları | Microsoft Docs
description: Bu makalede, Azure Event Hubs Mesajlaşma özel durumları ve Önerilen Eylemler listesini sağlar.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 09/25/2019
ms.author: shvija
ms.openlocfilehash: b6680902180a1d4a3c75080e232569cf760ba078
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309836"
---
# <a name="event-hubs-messaging-exceptions---net"></a>Event Hubs mesajlaşma özel durumları-.NET

Bu makalede, .NET Framework Event Hubs API 'Leri içeren Azure Service Bus mesajlaşma .NET API kitaplığı tarafından oluşturulan bazı .NET özel durumları listelenmektedir. Bu başvuru değişebilir, bu nedenle geri Güncelleştirmeleri denetle.

## <a name="exception-categories"></a>Özel durum kategorisi

Event Hubs .NET API 'Leri, çözümü gidermeye çalışmak için uygulayabileceğiniz ilgili eylemle birlikte, aşağıdaki kategorilere ayrılan özel durumlar oluşturur.

1. Kullanıcı kodlama hatası: [System. ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System. InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System. Operationolaydexception](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System. Runtime. Serialization. SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). Genel eylem: devam etmeden önce kod düzeltmeye çalışın.
2. Kurulum/yapılandırma hatası: [Microsoft. ServiceBus. Messaging. MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [Microsoft. Azure. EventHubs. MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception), [System. UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Genel eylem: yapılandırmanızı inceleyin ve gerekirse değiştirin.
3. Geçici özel durumlar: [Microsoft. ServiceBus. Messaging. MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft. ServiceBus. Messaging. serverbusonexception](#serverbusyexception), [Microsoft. Azure. Eventhubs. serverbusyıexception](#serverbusyexception), [ Microsoft. ServiceBus. Messaging. MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). Genel eylem: işlemi yeniden deneyin veya kullanıcılara bildirin.
4. Diğer özel durumlar: [System. Transactions. TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System. TimeoutException](#timeoutexception), [Microsoft. ServiceBus. Messaging. MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft. ServiceBus. Messaging. sessionlocklostexception](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception). Genel eylem: özel durum türü; özel aşağıdaki bölümdeki tabloya bakın. 

## <a name="exception-types"></a>Özel durum türleri
Aşağıdaki tabloda, Mesajlaşma özel durum türlerini ve nedenler ve notları önerilen eylem uygulayabileceğiniz listeler.

| Özel Durum Türü | Neden/açıklama/örnekleri | Önerilen eylem | Otomatik/hemen yeniden deneme sırasında dikkat edin. |
| -------------- | -------------------------- | ---------------- | --------------------------------- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Sunucu istenen işlemi tarafından denetlenen belirtilen süre içinde yanıt vermedi [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). Sunucu istenen işlemi tamamlanmamış olabilir. Bu durum, ağ veya diğer altyapı gecikmeler nedeniyle oluşabilir. |Tutarlılık için sistem durumunu denetleyin ve gerekirse yeniden deneyin.<br /> Bkz: [TimeoutException](#timeoutexception). | Bazı durumlarda yeniden başlatma yardımcı; yeniden deneme mantığı için kod ekleyin. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |İstenen kullanıcı işlemi, sunucu veya hizmet içinde izin verilmiyor. Özel durum iletisi ayrıntıları için bkz. Örneğin, [tam](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) iletisi alındı bu özel durum oluşturur [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) modu. | Kod ve belgelere bakın. İstenen işlem geçerli olduğundan emin olun. | Yeniden deneme yardımcı olmaz. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) | Zaten kapalı, durduruldu veya atılmış bir nesne üzerinde bir işlem çağırmak için bir deneme yapılır. Nadiren de olsa, ortam işlem zaten atıldı. | Kodu kontrol edip atılan nesneye işlemleri çağırma kullanılamaz olduğundan emin olun. | Yeniden deneme yardımcı olmaz. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) | [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) nesne bir belirteci alınamadı, belirteci geçersiz veya işlemi gerçekleştirmek için gerekli talep belirteci içermiyor. | Belirteç sağlayıcısı, doğru değerlerle oluşturulduğundan emin olun. Erişim denetimi hizmetinin yapılandırmasını denetleyin. | Bazı durumlarda yeniden başlatma yardımcı; yeniden deneme mantığı için kod ekleyin. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[Üretiliyor](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) | Yöntemine sağlanan bir veya daha fazla bağımsız değişken geçersiz. Sağlanan URI [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) veya [Oluştur](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) yolu segment(s) içerir. Sağlanan URI düzeni [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) veya [Oluştur](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) geçersiz. Özellik değeri, 32 KB'den daha büyük. | Çağıran kod denetleyin ve bağımsız değişkenlerin doğru olduğundan emin olun. | Yeniden deneme yardımcı olmaz. |
| [Microsoft.ServiceBus.Messaging MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /><br/> [Microsoft.Azure.EventHubs MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) | İşlemle ilişkili varlık yok veya silinmiş olabilir. | Varlığın mevcut olduğundan emin olun. | Yeniden deneme yardımcı olmaz. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) | İstemci, olay hub'ına bağlantı kuramıyor. |Sağlanan ana bilgisayar adının doğru olduğundan ve konağın erişilebilir olduğundan emin olun. | Aralıklı bağlantı sorunu yoksa, yeniden deneme yardımcı olabilir. |
| [Microsoft. ServiceBus. Messaging Serverbusyıexception](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> <br/>[Microsoft.Azure.EventHubs ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) | Hizmet şu anda isteğinizi mümkün değil. | İstemci bir süre bekleyin. ardından işlemi yeniden deneyin. <br /> Bkz: [ServerBusyException](#serverbusyexception). | İstemci, belirli bir süre sonra yeniden deneyebilir. Farklı bir özel durum yeniden oluşur, o özel durumu yeniden deneme davranışını kontrol edin. |
| [Istransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) | Aşağıdaki durumlarda oluşabilecek genel mesajlaşma özel durumu: Farklı bir varlık türüne (örneğin, bir konu) ait olan bir ad veya yol kullanarak bir [Queueclient](/dotnet/api/microsoft.servicebus.messaging.queueclient) oluşturmak için girişimde bulunuldu. 1 MB 'den büyük bir ileti göndermek için bir girişimde bulunuldu. Sunucu veya hizmet isteğinin işlenmesi sırasında bir hatayla karşılaştı. Özel durum iletisi ayrıntıları için bkz. Bu durum genellikle geçici bir istisnadır. | Kodunu kontrol edin ve yalnızca serileştirilebilir nesneler ileti gövdesi için kullanıldığından emin olun (veya özel bir serileştirici kullanın). Özelliklerin desteklenen değer türleri ve yalnızca desteklenen kullanım türleri için belgelere bakın. Denetleme [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) özelliği. Eğer öyleyse **true**, işlemi yeniden deneyin. | Yeniden deneme davranışı tanımsızdır ve yardımcı. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) | Bu hizmet ad alanındaki başka bir varlık tarafından zaten kullanılan bir ada sahip bir varlık oluşturmaya çalışır. | Var olan bir varlığa silin veya oluşturulacak varlığın farklı bir ad seçin. | Yeniden deneme yardımcı olmaz. |
| [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) | Mesajlaşma varlığı, izin verilen boyut üst sınırına. Bu özel durumun (olan 5) sayısı alıcılar bir tüketici her grubu düzeyinde açılan oluşabilir. | Alanı varlık içinde varlık veya onun alt iletilerini alma oluşturun. <br /> Bkz: [QuotaExceededException](#quotaexceededexception) | İletiler sırada kaldırdıysanız, yeniden deneme yardımcı olabilir. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) | Bir çalışma zamanı işlemi devre dışı bırakılmış bir varlık için isteği. |Varlık etkinleştirin. | Bu arada varlık etkinleştirdiyseniz, yeniden deneme yardımcı olabilir. |
| [Microsoft.ServiceBus.Messaging MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /><br/> [Microsoft.Azure.EventHubs MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | İleti yükü 1 MB sınırını aşıyor. Bu 1 MB sınır, sistem özelliklerini ve tüm .NET ek yükünü içerebilen toplam ileti içindir. | İleti yükü azaltın, ardından işlemi yeniden deneyin. |Yeniden deneme yardımcı olmaz. |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) belirli bir varlığa kotasının aşıldığını gösterir.

Bu özel durum sayısı (5) alıcılar bir tüketici her grubu düzeyinde açılan oluşabilir.

### <a name="event-hubs"></a>Event Hubs
Event Hubs olay hub'ı başına 20 tüketici grubu sınırı vardır. Daha fazla oluşturmaya çalıştığınızda, aldığınız bir [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

## <a name="timeoutexception"></a>TimeoutException
A [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) kullanıcı tarafından başlatılan bir işlemin işlemi zaman aşımı süresinden daha uzun sürdüğünü gösterir. 

Event Hubs için zaman aşımı veya bağlantı dizesinin veya aracılığıyla bir parçası olarak belirtilen [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). Hata iletisi farklılık gösterebilir, ancak her zaman geçerli işlem için belirtilen zaman aşımı değeri içerir. 

### <a name="common-causes"></a>Olası nedenler
Bu hata sık karşılaşılan iki nedeni vardır: yanlış yapılandırma veya bir geçici hizmet hatası.

1. **Yanlış yapılandırma** işlem zaman aşımı işletimsel koşul için çok küçük olabilir. İstemci SDK'sı işlemi zaman aşımı'için varsayılan değer 60 saniyedir. Kodunuzu değer olup olmadığını görmek için çok küçük bir şeye ayarlayın denetleyin. Ağ ve CPU kullanımı koşulu işlem zaman aşımı için küçük bir değere ayarlanmamalıdır böylece uygulamanın belirli bir işlemin tamamlanması için geçen süreyi etkileyebilir unutmayın.
2. **Geçici bir hizmet hatası** bazen Event Hubs hizmeti istekleri işliyor; Örneğin, yüksek trafiği dönemlerinde gecikme. Bu gibi durumlarda, işlemi başarılı olana kadar bir gecikmeden sonra işlemi yeniden deneyebilirsiniz. Aynı işlemi birden çok denemeden sonra yine başarısız olursa ziyaret [Azure hizmet durumu sitesine](https://azure.microsoft.com/status/) herhangi bir bilinen hizmet kesintileri olup olmadığını görmek için.

## <a name="serverbusyexception"></a>ServerBusyException

A [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) veya [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) bir sunucu aşırı yüklenmiş olduğunu gösterir. Bu özel durumun iki ilgili hata kodları vardır.

### <a name="error-code-50002"></a>Hata kodu 50002

Bu hata iki nedenlerden birinden dolayı oluşabilir:

1. Yük olay hub'ındaki tüm bölümler arasında eşit olarak dağıtılmaz ve bir bölüm yerel aktarım hızı birimi sınırlama denk gelir.
    
    Çözüm: Bölüm dağıtım stratejisini düzeltme ya da [Eventhubclient. Send (eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient) yardımcı olabilecek.

2. Yeterli işleme birimleri Event Hubs ad alanı yok (denetleyebilirsiniz **ölçümleri** olay ad alanı penceresinde Hubs ekran [Azure portalında](https://portal.azure.com) onaylamak için). Portal toplanan (1 dakika) bilgileri gösterir, ancak yalnızca tahmin, bu nedenle size gerçek zamanlı işleme ölçün.

    Çözüm: Ad alanındaki üretilen iş birimlerinin artırılması yardımcı olabilir. Portal, bu işlemi de yapabilirsiniz **ölçek** Event Hubs ad alanı ekranın penceresi. Veya, kullanabileceğiniz [otomatik şişme](event-hubs-auto-inflate.md).

### <a name="error-code-50001"></a>Hata kodu 50001

Bu hata nadiren gerçekleşmesi. Ad alanınız için kod kapsayıcıyı CPU üzerinde düşük – başlamadan önce olay hub'ları yük dengeleyici fazla birkaç saniye ortaya çıkar.

### <a name="limit-on-calls-to-the-getruntimeinformation-method"></a>GetRuntimeInformation yöntemiyle yapılan çağrıların sınırı
Azure Event Hubs, saniye başına Getruntimeınfo 'ya saniyede en fazla 50 çağrı destekler. Sınıra ulaşıldığında aşağıdakine benzer bir özel durum alabilirsiniz:

```
ExceptionId: 00000000000-00000-0000-a48a-9c908fbe84f6-ServerBusyException: The request was terminated because the namespace 75248:aaa-default-eventhub-ns-prodb2b is being throttled. Error code : 50001. Please wait 10 seconds and try again.
```

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki bağlantıları inceleyerek Event Hubs hakkında daha fazla bilgi edinebilirsiniz:

* [Event Hubs’a genel bakış](event-hubs-what-is-event-hubs.md)
* [Olay Hub’ı oluşturma](event-hubs-create.md)
* [Event Hubs ile ilgili SSS](event-hubs-faq.md)
