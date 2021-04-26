---
title: Azure Event Hubs özel durumlar (eski)
description: Bu makale, Azure Event Hubs mesajlaşma özel durumlarının ve önerilen eylemlerin bir listesini sağlar.
ms.topic: article
ms.date: 02/10/2021
ms.openlocfilehash: b7c5016214bfa3e5813417177e5d113299e0284f
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311932"
---
# <a name="event-hubs-messaging-exceptions---net-legacy"></a>Event Hubs mesajlaşma özel durumları-.NET (eski)
Bu bölümde, .NET Framework API 'Leri tarafından oluşturulan .NET özel durumları listelenmektedir. 

> [!IMPORTANT]
> Makalede listelenen bazı özel durumlar yalnızca eski Event Hubs .NET kitaplığı için geçerlidir. Örneğin: Microsoft. ServiceBus. * özel durumları.
> 
> Yeni .NET kitaplığı tarafından oluşturulan EventHubsException hakkında daha fazla bilgi için bkz. [eventhubsexception-.net](exceptions-dotnet.md)

## <a name="exception-categories"></a>Özel durum kategorileri

Event Hubs .NET API 'Leri, aşağıdaki kategorilere ayrılan özel durumlar oluşturur ve bunları gidermeyi denemek için uygulayabileceğiniz ilişkili eylemi kullanabilirsiniz:

 - Kullanıcı kodlama hatası: 
 
   - [System. ArgumentException](/dotnet/api/system.argumentexception?view=netcore-3.1&preserve-view=true)
   - [System. InvalidOperationException](/dotnet/api/system.invalidoperationexception?view=netcore-3.1&preserve-view=true)
   - [System. Operationolaydexception](/dotnet/api/system.operationcanceledexception?view=netcore-3.1&preserve-view=true)
   - [System. Runtime. Serialization. SerializationException](/dotnet/api/system.runtime.serialization.serializationexception?view=netcore-3.1&preserve-view=true)
   
   Genel eylem: devam etmeden önce kodu gidermeyi deneyin.
 
 - Kurulum/yapılandırma hatası: 
 
   - [Microsoft. ServiceBus. Messaging. MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception)
   - [Microsoft. Azure. EventHubs. MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception)
   - [System. UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception?view=netcore-3.1&preserve-view=true)
   
   Genel eylem: yapılandırmanızı gözden geçirin ve gerekirse değiştirin.
   
 - Geçici özel durumlar: 
 
   - [Microsoft. ServiceBus. Messaging. MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception)
   - [Microsoft. ServiceBus. Messaging. Serverbusyıexception](#serverbusyexception)
   - [Microsoft. Azure. EventHubs. Serverbusonexception](#serverbusyexception)
   - [Microsoft. ServiceBus. Messaging. MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)
   
   Genel eylem: işlemi yeniden deneyin veya kullanıcılara bildirin.
 
 - Diğer özel durumlar: 
 
   - [System. Transactions. TransactionException](/dotnet/api/system.transactions.transactionexception?view=netcore-3.1&preserve-view=true)
   - [System. TimeoutException](#timeoutexception)
   - [Microsoft. ServiceBus. Messaging. MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception)
   - [Microsoft. ServiceBus. Messaging. SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception)
   
   Genel eylem: özel durum türüne özgü; Aşağıdaki bölümdeki tabloya bakın. 

## <a name="exception-types"></a>Özel durum türleri
Aşağıdaki tabloda mesajlaşma özel durum türleri ve nedenleri ve gerçekleştirebileceğiniz notlar önerilir.

| Özel durum türü | Açıklama/neden/örnekler | Önerilen eylem | Otomatik/anında yeniden denemeye göz atma |
| -------------- | -------------------------- | ---------------- | --------------------------------- |
| [TimeoutException](/dotnet/api/system.timeoutexception?view=netcore-3.1&preserve-view=true) |Sunucu, belirtilen süre içinde, [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings)tarafından denetlenen istenen işleme yanıt vermedi. Sunucu istenen işlemi tamamlamış olabilir. Bu özel durum, ağ veya diğer altyapı gecikmelerinden kaynaklanabilir. |Tutarlılık için sistem durumunu kontrol edin ve gerekirse yeniden deneyin.<br /> Bkz. [TimeoutException](#timeoutexception). | Yeniden deneme bazı durumlarda yardımcı olabilirler; koda yeniden deneme mantığı ekleyin. |
| [InvalidOperationException](/dotnet/api/system.invalidoperationexception?view=netcore-3.1&preserve-view=true) |İstenen Kullanıcı işlemine sunucu veya hizmet içinde izin verilmiyor. Ayrıntılar için özel durum iletisine bakın. Örneğin, ileti [Receiveanddelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) modunda alınmışsa, [tamamlanmış](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) bu özel durumu oluşturur. | Kodu ve belgeleri denetleyin. İstenen işlemin geçerli olduğundan emin olun. | Yeniden deneme yardımcı olmayacaktır. |
| [OperationCanceledException](/dotnet/api/system.operationcanceledexception?view=netcore-3.1&preserve-view=true) | Zaten kapatılmış, durdurulmuş veya atılmış bir nesne üzerinde bir işlemi çağırmak için bir girişimde bulunuldu. Nadir durumlarda, çevresel işlem zaten atıldı. | Kodu denetleyin ve çıkarılan bir nesne üzerinde işlemleri çağırmadığına emin olun. | Yeniden deneme yardımcı olmayacaktır. |
| [UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception?view=netcore-3.1&preserve-view=true) | [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) nesnesi belirteç alamadı, belirteç geçersiz veya belirteç işlemi yapmak için gereken talepleri içermiyor. | Belirteç sağlayıcısının doğru değerlerle oluşturulduğundan emin olun. Access Control Service yapılandırmasını denetleyin. | Yeniden deneme bazı durumlarda yardımcı olabilirler; koda yeniden deneme mantığı ekleyin. |
| [ArgumentException](/dotnet/api/system.argumentexception?view=netcore-3.1&preserve-view=true)<br /> [ArgumentNullException](/dotnet/api/system.argumentnullexception?view=netcore-3.1&preserve-view=true)<br />[ArgumentOutOfRangeException](/dotnet/api/system.argumentoutofrangeexception?view=netcore-3.1&preserve-view=true) | Yönteme sağlanan bir veya daha fazla bağımsız değişken geçersiz. [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) veya [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) için sağlanan URI yol kesimi (ler) içeriyor. [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) veya [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) için sağlanan URI şeması geçersiz. Özellik değeri 32 KB 'den büyük. | Çağıran kodu denetleyin ve bağımsız değişkenlerin doğru olduğundan emin olun. | Yeniden deneme, yardım etmez. |
| [Microsoft. ServiceBus. Messaging MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /><br/> [Microsoft. Azure. EventHubs MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) | İşlemle ilişkili varlık yok veya silinmiş. | Varlığın mevcut olduğundan emin olun. | Yeniden deneme, yardım etmez. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) | İstemci, Olay Hub 'ına bir bağlantı kuramıyor. |Sağlanan ana bilgisayar adının doğru olduğundan ve konağın erişilebilir olduğundan emin olun. | Yeniden dene, aralıklı bağlantı sorunları olup olmadığı konusunda yardımcı olabilir. |
| [Microsoft. ServiceBus. Messaging Serverbusyıexception](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> <br/>[Microsoft. Azure. EventHubs Serverbusonexception](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) | Hizmet şu anda isteği işleyemiyor. | İstemci bir süre bekleyip işlemi yeniden deneyin. <br /> Bkz. [Serverbusyıexception](#serverbusyexception). | İstemci belirli bir aralıktan sonra yeniden deneyebilir. Yeniden deneme farklı bir özel durumla sonuçlanırsa, bu özel durumun yeniden deneme davranışını denetleyin. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) | Aşağıdaki durumlarda oluşturulabilecek genel mesajlaşma özel durumu: farklı bir varlık türüne (örneğin, bir konu) ait olan bir ad veya yol kullanarak bir [Queueclient](/dotnet/api/microsoft.servicebus.messaging.queueclient) oluşturmak için girişimde bulunuldu. 1 MB 'den büyük bir ileti göndermek için bir girişimde bulunuldu. Sunucu veya hizmet, isteğin işlenmesi sırasında bir hatayla karşılaştı. Ayrıntılar için özel durum iletisine bakın. Bu özel durum genellikle geçici bir özel durumdur. | Kodu denetleyin ve ileti gövdesi için yalnızca serileştirilebilir nesnelerin kullanıldığından emin olun (veya özel bir seri hale getirici kullanın). Özelliklerin desteklenen değer türleri için belgeleri denetleyin ve yalnızca desteklenen türleri kullanın. [Isgeçici](/dotnet/api/microsoft.servicebus.messaging.messagingexception) özelliğini denetleyin. **Doğru** ise işlemi yeniden deneyebilirsiniz. | Yeniden deneme davranışı tanımsız ve yardım olmayabilir. |
| [Messagingentityalreadyvartsexception](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) | Bu hizmet ad alanındaki başka bir varlık tarafından zaten kullanılan bir ada sahip bir varlık oluşturma girişimi. | Mevcut varlığı silin veya oluşturulacak varlık için farklı bir ad seçin. | Yeniden deneme, yardım etmez. |
| [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) | Mesajlaşma varlığı izin verilen en büyük boyuta ulaştı. Bu özel durum, müşteri başına grup düzeyinde en fazla alıcı sayısı (5 ' i) zaten açıldıysa meydana gelebilir. | Varlıktan veya onun alt sıraları üzerinden ileti alarak varlıkta alan oluşturun. <br /> Bkz. [QuotaExceededException](#quotaexceededexception) | Yeniden deneme, iletilerin bu sırada kaldırılıp kaldırılmadığı konusunda yardımcı olabilir. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) | Devre dışı bırakılmış bir varlık üzerinde çalışma zamanı işlemi isteği. |Varlığı etkinleştirin. | Yeniden deneme, varlığın geçici bir şekilde etkinleştirilmiş olup olmadığı konusunda yardımcı olabilir. |
| [Microsoft. ServiceBus. Messaging Iletiizeexceededexception](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /><br/> [Microsoft. Azure. EventHubs Iletiizeexceededexception](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | İleti yükü 1 MB sınırı aşıyor. Bu 1 MB 'lık sınır, sistem özelliklerini ve tüm .NET ek yükünü içerebilen toplam ileti içindir. | İleti yükünün boyutunu küçültün, sonra işlemi yeniden deneyin. |Yeniden deneme, yardım etmez. |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception), belirli bir varlık için belirlenen kotanın aşıldığını gösterir.

Bu özel durum, müşteri başına grup düzeyinde en fazla alıcı sayısı (5) zaten açılırsa meydana gelebilir.

### <a name="event-hubs"></a>Event Hubs
Event Hubs Olay Hub 'ı başına 20 Tüketici grubu sınırı vardır. Daha fazla oluşturmaya çalıştığınızda bir [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception)alırsınız. 

## <a name="timeoutexception"></a>TimeoutException
[TimeoutException](/dotnet/api/system.timeoutexception?view=netcore-3.1&preserve-view=true) , Kullanıcı tarafından başlatılan bir işlemin işlem zaman aşımından daha uzun sürdüğünü gösterir. 

Event Hubs için zaman aşımı, bağlantı dizesinin parçası olarak veya [Servicebusconnectionstringbuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder)aracılığıyla belirtilir. Hata iletisinin kendisi farklılık gösterebilir, ancak her zaman geçerli işlem için belirtilen zaman aşımı değerini içerir. 

Hizmeti çalıştıran kaynaklarda Event Hubs hizmet güncelleştirmeleri (veya) işletim sistemi güncelleştirmeleri gibi bakım işlemleri sırasında veya içinde zaman aşımları gerçekleşmesi beklenir. İşletim sistemi güncelleştirmeleri sırasında varlıklar ' ın etrafında taşınır ve düğümler güncellenir veya yeniden başlatılır, bu da zaman aşımına neden olabilir. Azure Event Hubs hizmeti için hizmet düzeyi sözleşmesi (SLA) ayrıntıları için bkz. [Event Hubs Için SLA](https://azure.microsoft.com/support/legal/sla/event-hubs/). 


### <a name="common-causes"></a>Yaygın nedenler
Bu hatanın iki yaygın nedeni vardır: yanlış yapılandırma veya geçici bir hizmet hatası.

- **Yanlış yapılandırma** İşlem zaman aşımı, işlemsel koşul için çok küçük olabilir. İstemci SDK 'sında işlem zaman aşımı için varsayılan değer 60 saniyedir. Kodunuzun değerin çok küçük bir değere ayarlanmış olup olmadığını denetleyin. Ağ ve CPU kullanımının koşulu, belirli bir işlemin tamamlanma süresini etkileyebilir, bu nedenle işlem zaman aşımı küçük bir değere ayarlanamaz.
- **Geçici hizmet hatası** Bazen Event Hubs hizmeti isteklerindeki gecikmelerle karşılaşabilir; Örneğin, yüksek trafik dönemlerinde. Bu gibi durumlarda, işlem başarılı olana kadar işleminizi bir gecikmeden sonra yeniden deneyebilirsiniz. Aynı işlem birden çok denemeden sonra yine de başarısız olursa, bilinen hizmet kesintileri olup olmadığını görmek için [Azure hizmet durumu sitesini](https://azure.microsoft.com/status/) ziyaret edin.

## <a name="serverbusyexception"></a>ServerBusyException

Bir [Microsoft. ServiceBus. Messaging. serverbusonexception](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) veya [Microsoft. Azure. EventHubs. serverbusonexception](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) , bir sunucunun aşırı yüklü olduğunu gösterir. Bu özel durum için iki ilgili hata kodu vardır.

### <a name="error-code-50002"></a>Hata kodu 50002
Bu hata, iki nedenden biri nedeniyle oluşabilir:

- Yük, Olay Hub 'ındaki tüm bölümler arasında eşit olarak dağıtılmaz ve bir bölüm yerel üretilen iş birimi sınırlamasını aşmaktadır.
    
    **Çözüm**: Bölüm dağıtım stratejisini düzeltme ya da [Eventhubclient. Send (eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient) yardımcı olabilecek.

- Event Hubs ad alanı yeterli işleme birimi içermiyor (onaylamak için [Azure portal](https://portal.azure.com) Event Hubs ad alanı penceresinde **ölçümler** ekranını kontrol edebilirsiniz). Portal toplanmış (1 dakikalık) bilgileri gösterir, ancak aktarım hızını gerçek zamanlı olarak ölçyoruz; bu nedenle yalnızca bir tahmindir.

    **Çözüm**: ad alanındaki üretilen iş birimlerinin artırılması yardımcı olabilir. 

    İşleme birimlerini, Azure portal **Event Hubs ad alanı** sayfanızın **Ölçek** sayfasında veya **genel bakış** sayfasında yapılandırabilirsiniz. Ya da, kullanım ihtiyaçlarını karşılamak için üretilen iş birimi sayısını artırarak otomatik olarak ölçeklendirilen [Otomatik Şişir](event-hubs-auto-inflate.md)kullanabilirsiniz.

    İşleme birimleri (DTU 'lar) bir Event Hubs ad alanındaki tüm olay hub 'larına uygulanır. Bu, ad alanı düzeyinde bir tüs satın almanızı ve bu ad alanı altındaki Olay Hub 'ları arasında paylaşılmanızı anlamına gelir. Her bir TU, ad alanını aşağıdaki yetenekler sahibine:

    - Saniyede 1 MB 'a kadar giriş olayı (bir olay hub 'ına gönderilen olaylar), ancak saniyede 1000 giriş olayı, yönetim işlemi veya denetim API çağrısı yok.
    - Saniyede 2 MB 'a kadar çıkış olayı (bir olay hub 'ından tüketilen olaylar), ancak 4096 'den fazla çıkış olayı yoktur.
    - 84 GB 'a kadar olay depolama (varsayılan 24 saatlik saklama süresi için yeterlidir).
    
    **Genel bakış** sayfasında **ölçümleri göster** bölümünde, **üretilen iş** sekmesine geçin. X ekseninde 1 dakikalık aralıklar içeren daha büyük bir pencerede açmak için grafiği seçin. En yüksek değerlere bakın ve gelen bayt/saniye veya giden bayt/saniye almak için bunları 60 göre bölün. **İstekler** sekmesinde, saniye başına istek sayısını hesaplamak için benzer bir yaklaşım kullanın. 

    İzleme Sayısı * limitinden (giriş/saniye için 1000 saniyede 1 MB) daha yüksek değerler görürseniz (çıkış için saniyede 2 MB), daha yüksek bir şekilde ölçeklendirilmesi veya Event Hubs [Otomatik](event-hubs-auto-inflate.md) olarak ölçeklendirmek için bir Event Hubs ad alanının **Ölçek** (sol menü) sayfasını kullanarak, DTU sayısını artırın. Otomatik olarak Şişir yalnızca 20 ' ye kadar artış olabileceğini unutmayın. Bunu tam 40 bir şekilde yükseltmek için bir [destek isteği](../azure-portal/supportability/how-to-create-azure-support-request.md)gönderebilirsiniz.

### <a name="error-code-50008"></a>Hata kodu 50008

Bu hata nadiren gerçekleşmelidir. Ad alanınız için kodu çalıştıran kapsayıcı, Event Hubs yük dengeleyici başlamadan önce birkaç saniyeden daha fazla değil, CPU üzerinde düşük olduğunda gerçekleşir.

**Çözüm**: GetRuntimeInformation yöntemiyle yapılan çağrıları sınırlayın. Azure Event Hubs, saniyedeki Getruntimeınfo 'a her bir tüketici grubu için en fazla 50 çağrısı destekler. Sınıra ulaşıldığında aşağıdakine benzer bir özel durum alabilirsiniz:

```
ExceptionId: 00000000000-00000-0000-a48a-9c908fbe84f6-ServerBusyException: The request was terminated because the namespace 75248:aaa-default-eventhub-ns-prodb2b is being throttled. Error code : 50008. Please wait 10 seconds and try again.
```


## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki bağlantıları inceleyerek Event Hubs hakkında daha fazla bilgi edinebilirsiniz:

* [Event Hubs genel bakış](./event-hubs-about.md)
* [Olay Hub'ı oluşturma](event-hubs-create.md)
* [Event Hubs ile ilgili SSS](event-hubs-faq.yml)