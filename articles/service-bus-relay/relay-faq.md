---
title: Azure Relay SSS | Microsoft Docs
description: Azure Relay hakkında sık sorulan bazı soruların yanıtlarını alın.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 886d2c7f-838f-4938-bd23-466662fb1c8e
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2018
ms.author: spelluru
ms.openlocfilehash: 207f73bbf9a92d26be1791fc11ce81fe68252705
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422965"
---
# <a name="azure-relay-faqs"></a>Azure Relay SSS

Bu makalede [Azure Relay](https://azure.microsoft.com/services/service-bus/)hakkında sık sorulan bazı sorular (SSS) yanıtlanmaktadır. Genel Azure fiyatlandırması ve destek bilgileri için bkz. [Azure desteği SSS](https://azure.microsoft.com/support/faq/).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions"></a>Genel sorular
### <a name="what-is-azure-relay"></a>Azure Geçiş nedir?
[Azure Relay hizmeti](relay-what-is-it.md) , kurumsal bir kurumsal ağ içinde bulunan hizmetleri genel buluta daha güvenli bir şekilde kullanıma sunmanıza yardımcı olarak karma uygulamalarınızı kolaylaştırır. Bir güvenlik duvarı bağlantısı açmadan Hizmetleri açığa çıkarmak ve kurumsal ağ altyapısına müdahale eden değişiklikler yapmanıza gerek kalmadan kullanıma sunabilirsiniz.

### <a name="what-is-a-relay-namespace"></a>Geçiş ad alanı nedir?
[Ad alanı](relay-create-namespace-portal.md) , uygulamanızdaki geçiş kaynaklarını ele almak için kullanabileceğiniz bir kapsam kapsayıcıdır. Geçiş kullanmak için bir ad alanı oluşturmanız gerekir. Bu, kullanmaya başlarken ilk adımlardan biridir.

### <a name="what-happened-to-service-bus-relay-service"></a>Service Bus Relay Service 'e ne oldu?
Daha önce adlandırılmış Service Bus geçiş hizmeti artık [WCF geçişi](service-bus-relay-tutorial.md)olarak adlandırılır. Bu hizmeti her zamanki gibi kullanmaya devam edebilirsiniz. Karma Bağlantılar özelliği, Azure BizTalk Services tarafından oluşturulan bir hizmetin güncelleştirilmiş sürümüdür. WCF Geçişi ve Karma Bağlantılar her ikisi de desteklenmeye devam eder.

## <a name="pricing"></a>Fiyatlandırma
Bu bölümde, geçiş Fiyatlandırma yapısı hakkında sık sorulan bazı sorular yanıtlanmaktadır. Ayrıca, genel Azure fiyatlandırma bilgileri için [Azure Destek SSS](https://azure.microsoft.com/support/faq/) ' ye bakabilirsiniz. Geçiş fiyatlandırması hakkında ayrıntılı bilgi için bkz. [Service Bus fiyatlandırma ayrıntıları][Pricing overview].

### <a name="how-do-you-charge-for-hybrid-connections-and-wcf-relay"></a>Karma Bağlantılar ve WCF Geçişi nasıl ücretlendirilir?
Geçiş fiyatlandırması hakkında ayrıntılı bilgi için, Service Bus fiyatlandırma ayrıntıları sayfasında [karma bağlantılar ve WCF geçişleri][Pricing overview] tablosuna bakın. Bu sayfada belirtilen fiyatlara ek olarak, uygulamanızın sağlandığı veri merkezinin dışında çıkış için ilişkili veri aktarımları için ücretlendirilirsiniz.

### <a name="how-am-i-billed-for-hybrid-connections"></a>Karma Bağlantılar için nasıl faturalandırılırım?
Karma Bağlantılar için üç örnek faturalandırma senaryosu aşağıda verilmiştir:

*   Senaryo 1:
    *   Karma Bağlantılar Yöneticisi 'nin bir örneği ve tüm ay için sürekli çalışan gibi tek bir dinleyiciniz vardır.
    *   Ay boyunca bağlantı üzerinden 3 GB veri gönderirsiniz. 
    *   Toplam ücretiniz $5 ' dir.
*   Senaryo 2:
    *   Karma Bağlantılar Yöneticisi 'nin bir örneği ve tüm ay için sürekli çalışan gibi tek bir dinleyiciniz vardır.
    *   Ay boyunca bağlantı üzerinden 10 GB veri gönderirsiniz.
    *   Toplam ücretiniz $7,50 ' dir. Bu, bağlantı için $5 ve 5 GB 'lık 5 GB veri için ilk 5 GB + $2,50 ' dir.
*   Senaryo 3:
    *   Karma Bağlantılar Manager 'ın bir ve B, her ay için sürekli olarak çalışan iki örneki vardır.
    *   Ayda 3 GB veri gönderebilirsiniz.
    *   Ayda 6 GB veri gönderebilirsiniz.
    *   Toplam ücretiniz $10,50 ' dir. Bu, bağlantı B + $0,50 için bir + $5 bağlantısı için $5 (B bağlantısı üzerindeki altıncı gigabayt için).

Örneklerde kullanılan fiyatların yalnızca Karma Bağlantılar önizleme döneminde geçerli olduğunu unutmayın. Fiyatlar, Karma Bağlantılar genel kullanıma sunulmasıyla ilgili olarak değişebilir.

### <a name="how-are-hours-calculated-for-relay"></a>Geçiş için saatler nasıl hesaplanır?

WCF Geçişi yalnızca Standart katman ad alanlarında kullanılabilir. Geçiş için fiyatlandırma ve [bağlantı kotaları](../service-bus-messaging/service-bus-quotas.md) , aksi durumda değişmemiştir. Yani, geçişlerin ileti sayısına (işlem değil) ve geçiş saatlerine göre ücretlendirilmeye devam eder. Daha fazla bilgi için, fiyatlandırma ayrıntıları sayfasındaki ["karma bağlantılar ve WCF geçişleri"](https://azure.microsoft.com/pricing/details/service-bus/) tablosuna bakın.

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-specific-relay"></a>Belirli bir geçişe bağlı birden fazla dinleyici varsa ne yapmalıyım?
Bazı durumlarda, tek bir geçiş birden fazla bağlantılı dinleyicilerine sahip olabilir. En az bir geçiş dinleyicisi bağlandığında bir geçiş açık kabul edilir. Açık geçiş için dinleyicileri eklemek ek geçiş saatleriyle sonuçlanır. Geçişe bağlı olan geçiş gönderenlerin sayısı (geçişleri çağıran veya iletileri gönderen istemciler) geçiş saatleri hesaplamasını etkilemez.

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>WCF geçişleri için ileti ölçümü nasıl hesaplanır?
(**Bu yalnızca WCF geçişleri için geçerlidir. Mesajlar Karma Bağlantılar maliyeti değildir.** )

Genel olarak, geçişler için faturalanabilir iletiler, daha önce açıklanan aracılı varlıklar (kuyruklar, konular ve abonelikler) için kullanılan yöntemi kullanarak hesaplanır. Ancak bazı önemli farklılıklar vardır.

Service Bus geçişine ileti göndermek, iletiyi alan geçiş dinleyicisine "tam olarak" olarak değerlendirilir. Service Bus geçişine gönderme işlemi olarak değerlendirilmez ve ardından geçiş dinleyicisine gönderim yapılır. Bir geçiş dinleyicisine karşı bir istek-yanıt stil hizmeti çağrısı (64 KB 'ye kadar), iki adet faturalanabilir ileti ile sonuçlanır: istek için bir faturalanabilir ileti ve yanıt için bir faturalanabilir ileti (yanıtın de 64 KB veya daha küçük olduğu varsayılırsa). Bu, bir istemci ile hizmet arasında aracılık için bir sıra kullanmaktan farklıdır. Bir istemci ile bir hizmet arasında aracılık için bir sıra kullanırsanız, aynı istek-yanıt deseninin kuyruğa gönderilmesi için bir istek gönderimi ve sonra sıradan hizmete bir sıra oluşturma/teslim olması gerekir. Bunun ardından, başka bir kuyruğa gönderme yanıtı ve bu kuyruktan istemciye bir sıra oluşturma/teslim işlemi gönderilir. Aynı boyut varsayımlarını (64 KB 'a kadar) kullanarak, ortalandırılmış sıra deseninin 4 faturalanabilir ileti ile sonucu vardır. Geçiş kullanarak yaptığınız aynı kalıbı uygulamak için ileti sayısının iki katı için faturalandırılırsınız. Tabii ki, dayanıklılık ve yük dengeleme gibi bu düzene ulaşmak için kuyrukları kullanmanın avantajları vardır. Bu avantajlar ek giderleri de gösterebilir.

**Nettcprelay** WCF bağlaması kullanılarak açılan geçişler, tek tek iletiler olarak olmayan iletileri kabul eder, ancak sistem üzerinden akan veri akışı olarak işlenir. Bu bağlamayı kullandığınızda, yalnızca gönderici ve dinleyici, gönderilen ve alınan ayrı mesajların çerçeveleme halinde görünürlüğe sahiptir. **Nettcprelay** bağlamasını kullanan geçişler için tüm veriler, faturalandırılabilir iletileri hesaplamak için bir akış olarak değerlendirilir. Bu durumda Service Bus, her bir geçiş aracılığıyla gönderilen veya alınan toplam veri miktarını 5 dakikalık bir esasına göre hesaplar. Daha sonra, bu süre boyunca o geçiş için faturalandırılabilir ileti sayısını öğrenmek üzere bu toplam veri miktarını 64 KB 'ye böler.

## <a name="quotas"></a>Kotalar
| Kota adı | `Scope` |  Notlar | Value |
| --- | --- | --- | --- |
| Geçiş sırasında eş zamanlı dinleyiciler |Varlık |Daha sonraki ek bağlantı istekleri reddedilir ve çağıran kod tarafından bir özel durum alınır. |25 |
| Hizmet ad alanındaki tüm geçiş uç noktaları başına eş zamanlı geçiş bağlantıları |Ad Alanı |- |5,000 |
| Hizmet ad alanı başına geçiş uç noktaları |Ad Alanı |- |10,000 |
| [NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) ve [Neteventrelaybinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) geçişleri için ileti boyutu |Ad Alanı |Bu kotaları aşan gelen iletiler reddedilir ve çağıran kod tarafından bir özel durum alınır. |64 KB |
| [Httprelaytransportbindingelement](/dotnet/api/microsoft.servicebus.httprelaytransportbindingelement) ve [Nettcprelaybinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) geçişleri için ileti boyutu |Ad Alanı |İleti boyutu için sınır yoktur. |Sınırsız |

### <a name="does-relay-have-any-usage-quotas"></a>Geçiş, kullanım kotalarına sahip mi?
Varsayılan olarak, herhangi bir bulut hizmeti için, Microsoft tüm müşteri abonelikleri genelinde hesaplanan bir toplu aylık kullanım kotası ayarlar. İhtiyaçlarınızı Bu limitlerin aşılabileceğini anladık. Müşteri Hizmetleri 'ne dilediğiniz zaman başvurabilirsiniz, böylelikle ihtiyaçlarınızı anlayabiliriz ve bu limitleri uygun şekilde ayarlayabiliriz. Service Bus için, toplam kullanım kotaları aşağıdaki gibidir:

* 5 milyar ileti
* 2 milyon geçiş saati

Aylık kullanım kotalarını aşan bir hesabı devre dışı bırakma hakkını ayırdık, ancak e-posta bildirimi sağlıyoruz ve herhangi bir işlem gerçekleştirmeden önce müşteriyle iletişim kurmaya yönelik birden çok girişim yaptık. Bu kotaları aşan müşteriler hala fazla ücretlerden sorumludur.

### <a name="naming-restrictions"></a>Adlandırma kısıtlamaları
Geçiş ad alanı adı 6 ila 50 karakter uzunluğunda olmalıdır.

## <a name="subscription-and-namespace-management"></a>Abonelik ve ad alanı yönetimi
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Bir ad alanını başka bir Azure aboneliğine geçirmek Nasıl yaparım? mı?

Bir ad alanını bir Azure aboneliğinden başka bir aboneliğe taşımak için [Azure Portal](https://portal.azure.com) kullanabilir ya da PowerShell komutlarını kullanabilirsiniz. Bir ad alanını başka bir aboneliğe taşımak için ad alanı zaten etkin olmalıdır. Komutları çalıştıran kullanıcının hem kaynak hem de hedef aboneliklerde yönetici kullanıcı olması gerekir.

#### <a name="azure-portal"></a>Azure portal

Bir abonelikteki Azure Relay ad alanlarını başka bir aboneliğe geçirmek üzere Azure portal kullanmak için bkz. [kaynakları yeni bir kaynak grubuna veya aboneliğe taşıma](../azure-resource-manager/resource-group-move-resources.md#use-the-portal). 

#### <a name="powershell"></a>PowerShell

Bir ad alanını bir Azure aboneliğinden başka bir aboneliğe taşımak üzere PowerShell 'i kullanmak için aşağıdaki komut dizisini kullanın. Bu işlemi yürütmek için, ad alanı zaten etkin olmalıdır ve PowerShell komutlarını çalıştıran kullanıcı hem kaynak hem de hedef aboneliklerde yönetici kullanıcı olmalıdır.

```azurepowershell-interactive
# Create a new resource group in the target subscription.
Select-AzSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzResourceGroup -Name 'targetRG' -Location 'East US'

# Move the namespace from the source subscription to the target subscription.
Select-AzSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="troubleshooting"></a>Sorun giderme
### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-suggested-actions-you-can-take"></a>Azure Relay API 'Leri tarafından oluşturulan özel durumların bazıları ve gerçekleştirebileceğiniz önerilen eylemler nelerdir?
Ortak özel durumlar ve gerçekleştirebileceğiniz önerilen eylemlerin bir açıklaması için bkz. [geçiş özel durumları][Relay exceptions].

### <a name="what-is-a-shared-access-signature-and-which-languages-can-i-use-to-generate-a-signature"></a>Paylaşılan erişim imzası nedir ve imza oluşturmak için hangi dilleri kullanabilirim?
Paylaşılan erişim Imzaları (SAS), SHA-256 güvenli karmaları veya URI 'Leri temel alan bir kimlik doğrulama mekanizmasıdır. Node. js, PHP, Python, Java, C ve C#' de kendi imzalarınızı oluşturma hakkında daha fazla bilgi için bkz. [paylaşılan erişim imzaları ile Service Bus kimlik doğrulaması][Shared Access Signatures].

### <a name="is-it-possible-to-whitelist-relay-endpoints"></a>Geçiş uç noktalarını beyaz listelemek mümkün mü?
Evet. Geçiş istemcisi tam etki alanı adlarını kullanarak Azure Relay hizmetine bağlantı yapar. Müşteriler, DNS beyaz listesini destekleyen `*.servicebus.windows.net` güvenlik duvarları için bir giriş ekleyebilir.

## <a name="next-steps"></a>Sonraki adımlar
* [Ad alanı oluşturma](relay-create-namespace-portal.md)
* [.NET kullanmaya başlama](relay-hybrid-connections-dotnet-get-started.md)
* [Node kullanmaya başlama](relay-hybrid-connections-node-get-started.md)

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Shared Access Signatures]: ../service-bus-messaging/service-bus-sas.md
