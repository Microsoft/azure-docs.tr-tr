---
title: Özel durumları ve bunları nasıl çözebilmek Azure Relay | Microsoft Docs
description: Bunları çözmeye yardımcı olmak için uygulayabileceğiniz Azure Relay özel durumların ve önerilen eylemlerin listesi.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: e3f9fe2e8aac826bd63d9842bd3380be10e1b87e
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313955"
---
# <a name="azure-relay-exceptions"></a>Azure Relay özel durumlar

Bu makalede, Azure Relay API 'Leri tarafından oluşturulabilecek bazı özel durumlar listelenmektedir. Bu başvuru değişikliğe tabidir, bu nedenle güncelleştirmeleri geri çekin.

## <a name="exception-categories"></a>Özel durum kategorileri

Geçiş API 'Leri, aşağıdaki kategorilere ayrılan özel durumlar oluşturur. Ayrıca, özel durumları çözmeye yardımcı olmak için uygulayabileceğiniz önerenlerden de yararlanabilirsiniz.

*   **Kullanıcı kodlama hatası**: [System. ArgumentException](/dotnet/api/system.argumentexception), [System. InvalidOperationException](/dotnet/api/system.invalidoperationexception), [System. Operationolaydexception](/dotnet/api/system.operationcanceledexception), [System. Runtime. Serialization. SerializationException](/dotnet/api/system.runtime.serialization.serializationexception). 

    **Genel eylem**: devam etmeden önce kodu gidermeyi deneyin.
*   **Kurulum/yapılandırma hatası**: [System. UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception). 

    **Genel eylem**: yapılandırmanızı gözden geçirin. Gerekirse, yapılandırmayı değiştirin.
*   **Geçici özel durumlar**: [Microsoft. ServiceBus. Messaging. messagingexception](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft. ServiceBus. Messaging. Serverbusyexception](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception), [Microsoft. ServiceBus. Messaging. MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). 

    **Genel eylem**: işlemi yeniden deneyin veya kullanıcılara bildirin.
*   **Diğer özel durumlar**: [System. Transactions. TransactionException](/dotnet/api/system.transactions.transactionexception), [System. TimeoutException](/dotnet/api/system.timeoutexception). 

    **Genel eylem**: özel durum türüne özeldir. Aşağıdaki bölümde tablosuna bakın. 

## <a name="exception-types"></a>Özel durum türleri

Aşağıdaki tabloda mesajlaşma özel durum türleri ve nedenleri listelenmektedir. Ayrıca, özel durumları çözmeye yardımcı olmak için uygulayabileceğiniz önerilen eylemleri de not alır.

| **Özel durum türü** | **Açıklama** | **Önerilen eylem** | **Otomatik veya anında yeniden denemeye göz atma** |
| --- | --- | --- | --- |
| [Aş](/dotnet/api/system.timeoutexception) |Sunucu, belirtilen süre içinde, [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout)tarafından denetlenen istenen işleme yanıt vermedi. Sunucu istenen işlemi tamamlamış olabilir. Bu durum ağ veya diğer altyapı gecikmelerinden kaynaklanabilir. |Tutarlılık için sistem durumunu kontrol edin ve gerekirse yeniden deneyin. Bkz. [TimeoutException](#timeoutexception). |Yeniden deneme bazı durumlarda yardımcı olabilirler; koda yeniden deneme mantığı ekleyin. |
| [Geçersiz Işlem](/dotnet/api/system.invalidoperationexception) |Sunucu veya hizmet içinde istenen Kullanıcı işlemine izin verilmiyor. Ayrıntılar için özel durum iletisine bakın. |Kodu ve belgeleri denetleyin. İstenen işlemin geçerli olduğundan emin olun. |Yeniden deneme, yardım etmez. |
| [İşlem Iptal edildi](/dotnet/api/system.operationcanceledexception) |Zaten kapatılmış, durdurulmuş veya atılmış bir nesne üzerinde bir işlemi çağırmak için bir girişimde bulunuldu. Nadir durumlarda, çevresel işlem zaten atıldı. |Kodu denetleyin ve çıkarılan bir nesne üzerindeki işlemleri çağırmadığından emin olun. |Yeniden deneme, yardım etmez. |
| [Yetkisiz erişim](/dotnet/api/system.unauthorizedaccessexception) |[TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) nesnesi bir belirteç alamıyor, belirteç geçersiz veya belirteç işlemi gerçekleştirmek için gereken talepleri içermiyor. |Belirteç sağlayıcısının doğru değerlerle oluşturulduğundan emin olun. Access Control hizmetinin yapılandırmasını denetleyin. |Yeniden deneme bazı durumlarda yardımcı olabilirler; koda yeniden deneme mantığı ekleyin. |
| [Bağımsız değişken özel durumu](/dotnet/api/system.argumentexception),<br /> [Bağımsız değişken null](/dotnet/api/system.argumentnullexception),<br />[Bağımsız değişken aralık dışında](/dotnet/api/system.argumentoutofrangeexception) |Aşağıdakilerden biri veya birkaçı oluştu:<br />Yönteme sağlanan bir veya daha fazla bağımsız değişken geçersiz.<br /> [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) veya [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) için sağlanan URI bir veya daha fazla yol kesimi içeriyor.<br />[NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) veya [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) için sağlanan URI şeması geçersiz. <br />Özellik değeri 32 KB 'den büyük. |Çağıran kodu denetleyin ve bağımsız değişkenlerin doğru olduğundan emin olun. |Yeniden deneme, yardım etmez. |
| [Sunucu meşgul](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) |Hizmet şu anda isteği işleyemiyor. |İstemci bir süre bekleyip işlemi yeniden deneyin. |İstemci belirli bir aralıktan sonra yeniden deneyebilir. Yeniden deneme farklı bir özel durumla sonuçlanırsa, bu özel durumun yeniden deneme davranışını kontrol edin. |
| [Kota aşıldı](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |Mesajlaşma varlığı izin verilen en büyük boyuta ulaştı. |Varlıktan veya onun alt sıraları üzerinden ileti alarak varlıkta alan oluşturun. Bkz. [QuotaExceededException](#quotaexceededexception). |Yeniden deneme, iletilerin bu sırada kaldırılıp kaldırılmadığı konusunda yardımcı olabilir. |
| [İleti boyutu aşıldı](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |İleti yükü 256 KB sınırını aşıyor. 256 KB sınırının toplam ileti boyutu olduğunu unutmayın. Toplam ileti boyutu sistem özelliklerini ve tüm Microsoft .NET ek yükünü içerebilir. |İleti yükünün boyutunu küçültün, sonra işlemi yeniden deneyin. |Yeniden deneme, yardım etmez. |

## <a name="quotaexceededexception"></a>QuotaExceededException

[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception), belirli bir varlık için belirlenen kotanın aşıldığını gösterir.

Geçiş için, bu özel durum [System. ServiceModel. QuotaExceededException](/dotnet/api/system.servicemodel.quotaexceededexception)sarmalanmış ve bu uç nokta için en fazla dinleyici sayısını aşmış olduğunu gösterir. Bu, özel durum iletisinin **Maximumlistenersperendpoint** değerinde belirtilir.

## <a name="timeoutexception"></a>TimeoutException
[TimeoutException](/dotnet/api/system.timeoutexception) , Kullanıcı tarafından başlatılan bir işlemin işlem zaman aşımından daha uzun sürdüğünü gösterir. 

[ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) özelliğinin değerini denetleyin. Bu sınıra ulaşmak aynı zamanda [TimeoutException](/dotnet/api/system.timeoutexception)öğesine neden olabilir.

Geçiş için, ilk olarak bir geçiş gönderici bağlantısı açtığınızda zaman aşımı özel durumları alabilirsiniz. Bu özel durumun iki yaygın nedeni vardır:

*   [OpenTimeout](/previous-versions/) değeri çok küçük (saniyenin bir kesri bile) olabilir.
* Şirket içi geçiş dinleyicisi yanıt vermemeye devam edebilir (ya da dinleyicilerinin yeni istemci bağlantıları kabul etmesini engelleyen güvenlik duvarı kuralları sorunlarıyla karşılaşabilir) ve [OpenTimeout](/previous-versions/) değeri yaklaşık 20 saniyeden az olabilir.

Örnek:

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10.
The time allotted to this operation may have been a portion of a longer timeout.
```

### <a name="common-causes"></a>Yaygın nedenler
Bu hatanın iki yaygın nedeni vardır:

*   **Hatalı yapılandırma**
    
    İşlem zaman aşımı, işlemsel koşul için çok küçük olabilir. İstemci SDK 'sında işlem zaman aşımı için varsayılan değer 60 saniyedir. Kodunuzdaki değerin çok küçük bir değere ayarlanmış olup olmadığını kontrol edin. CPU kullanımının ve ağ koşulunun bir işlemin tamamlanabilmesi için geçen süreyi etkilediğini unutmayın. İşlem zaman aşımını çok küçük bir değere ayarlamak iyi bir fikirdir.
*   **Geçici hizmet hatası**

    Zaman zaman, geçiş hizmeti istekleri işlerken gecikmeler yaşayabilir. Bu, örneğin, yüksek trafik dönemlerinde meydana gelebilir. Bu gerçekleşirse, işlem başarılı olana kadar işleminizi bir gecikmeden sonra yeniden deneyin. Aynı işlem birden çok denemeden sonra başarısız olmaya devam ederse, bilinen hizmet kesintileri olup olmadığını görmek için [Azure hizmet durumu sitesine](https://azure.microsoft.com/status/) bakın.

## <a name="next-steps"></a>Sonraki adımlar
* [Azure Relay SSS](relay-faq.yml)
* [Geçiş ad alanı oluşturma](relay-create-namespace-portal.md)
* [Azure Relay ve .NET ile çalışmaya başlama](relay-hybrid-connections-dotnet-get-started.md)
* [Azure Relay ve Node ile çalışmaya başlama](relay-hybrid-connections-node-get-started.md)
