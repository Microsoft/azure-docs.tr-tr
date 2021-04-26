---
title: Azure Izleyici 'de ölçümleri Azure Relay | Microsoft Docs
description: Bu makalede, Azure Relay durumunu izlemek için Azure Izleyici 'yi nasıl kullanabileceğiniz hakkında bilgi verilmektedir.
services: service-bus-relay
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 7f91ac0aec46d0fc24901585156dceabb18640c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100586821"
---
# <a name="azure-relay-metrics-in-azure-monitor"></a>Azure Izleyici 'de Azure Relay ölçümleri 
Azure Relay ölçümler, Azure aboneliğinizdeki kaynakların durumunu sağlar. Zengin bir ölçüm verileri kümesiyle, geçiş kaynaklarınızın genel durumunu yalnızca ad alanı düzeyinde değil, aynı zamanda varlık düzeyinde değerlendirebilirsiniz. Bu istatistikler Azure Relay durumunu izlemenize yardımcı olduklarından önemli olabilir. Ölçümler Ayrıca, Azure desteği ile iletişim kurmaya gerek kalmadan, kök neden sorunları gidermenize yardımcı olabilir.

Azure Izleyici, çeşitli Azure hizmetleri genelinde izleme için birleştirilmiş kullanıcı arabirimleri sağlar. Daha fazla bilgi için bkz. [Microsoft Azure 'de izleme](../azure-monitor/overview.md) ve GitHub 'da [.net örneği ile Azure izleyici ölçümlerini alma](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) .

> [!IMPORTANT]
> Bu makale, WCF Geçişi değil, yalnızca Azure Relay Karma Bağlantılar özelliği için geçerlidir. 

## <a name="access-metrics"></a>Erişim ölçümleri

Azure Izleyici, ölçümlere erişmek için birden çok yol sağlar. Ölçümlere [Azure Portal](https://portal.azure.com)aracılığıyla erişebilir veya Azure Izleyici API 'LERINI (REST ve .net) ve Işlem yönetimi paketi ve Event Hubs gibi analiz çözümlerini kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure izleyici tarafından toplanan verileri izleme](../azure-monitor/data-platform.md).

Ölçümler varsayılan olarak etkinleştirilmiştir ve en son 30 güne kadar verilere erişebilirsiniz. Verileri daha uzun bir süre tutmanız gerekiyorsa, ölçüm verilerini bir Azure depolama hesabına arşivleyebilirsiniz. Bu, Azure Izleyici 'de [Tanılama ayarları](../azure-monitor/essentials/diagnostic-settings.md) 'nda yapılandırılır.

## <a name="access-metrics-in-the-portal"></a>Portalda erişim ölçümleri

[Azure Portal](https://portal.azure.com)ölçümleri zaman içinde izleyebilirsiniz. Aşağıdaki örnek, başarılı isteklerin ve gelen isteklerin hesap düzeyinde nasıl görüntüleneceğini göstermektedir:

!["Izleyici-ölçümler (Önizleme)" başlıklı bir sayfa, son 30 gün için bellek kullanımının bir çizgi grafiğini gösterir.][1]

Ölçümlere doğrudan ad alanı aracılığıyla da erişebilirsiniz. Bunu yapmak için ad alanınızı seçin ve ardından **ölçümler**' e tıklayın. 

Boyutları destekleyen ölçümler için, istenen boyut değeri ile filtrelemeniz gerekir.

## <a name="billing"></a>Faturalandırma

Azure Izleyici 'de ölçümlerin kullanılması şu anda önizleme aşamasında ücretsizdir. Ancak, ölçüm verilerini alan ek çözümler kullanıyorsanız, bu çözümler tarafından faturalandırılabiliriz. Örneğin, ölçüm verilerini bir Azure depolama hesabına arşivlerseniz Azure Storage tarafından faturalandırılırsınız. Ayrıca, ölçüm verilerini gelişmiş analizler için Azure Izleyici günlüklerine kadıysanız Azure Izleyici günlükleri tarafından da faturalandırılırsınız.

Aşağıdaki ölçümler, hizmetinizin sistem durumuna ilişkin bir genel bakış sağlar. 

> [!NOTE]
> Farklı bir ad altında taşındığı için birkaç ölçümü kullanımdan kaldıracağız. Bu işlem, başvurularınızı güncelleştirmenizi gerektirebilir. "Kullanım dışı" anahtar sözcüğüyle işaretlenen ölçümler bundan sonra desteklenmez.

Tüm ölçüm değerleri her dakika Azure Izleyici 'ye gönderilir. Zaman ayrıntı düzeyi, ölçüm değerlerinin sunulduğu zaman aralığını tanımlar. Tüm Azure Relay ölçümleri için desteklenen zaman aralığı 1 dakikadır.

## <a name="connection-metrics"></a>Bağlantı ölçümleri

| Ölçüm Adı | Description |
| ------------------- | ----------------- |
| ListenerConnections-Success  | Belirli bir süre içinde Azure Relay yapılan başarılı dinleyici bağlantısı sayısı. <br/><br/> Birim: sayım <br/> Toplama türü: Toplam <br/> Boyut: EntityName|
|ListenerConnections-ClientError |Belirli bir süre boyunca dinleyici bağlantılarında istemci hatalarının sayısı.<br/><br/> Birim: sayım <br/> Toplama türü: Toplam <br/> Boyut: EntityName|
|ListenerConnections-ServerError |Belirli bir süre boyunca dinleyici bağlantılarında sunucu hatalarının sayısı.<br/><br/> Birim: sayım <br/> Toplama türü: Toplam <br/> Boyut: EntityName|
|SenderConnections-Success |Belirli bir süre içinde yapılan başarılı gönderici bağlantısı sayısı.<br/><br/> Birim: sayım <br/> Toplama türü: Toplam <br/> Boyut: EntityName|
|SenderConnections-ClientError |Belirli bir süre boyunca gönderici bağlantılarında istemci hatalarının sayısı.<br/><br/> Birim: sayım <br/> Toplama türü: Toplam <br/> Boyut: EntityName|
|SenderConnections-ServerError |Belirli bir süre boyunca gönderici bağlantılarında sunucu hatalarının sayısı.<br/><br/> Birim: sayım <br/> Toplama türü: Toplam <br/> Boyut: EntityName|
|ListenerConnections-TotalRequests |Belirli bir süre içindeki dinleyici bağlantılarının toplam sayısı.<br/><br/> Birim: sayım <br/> Toplama türü: Toplam <br/> Boyut: EntityName|
|SenderConnections-TotalRequests |Belirli bir süre içinde göndericiler tarafından yapılan bağlantı istekleri.<br/><br/> Birim: sayım <br/> Toplama türü: Toplam <br/> Boyut: EntityName|
|ActiveConnections |Etkin bağlantı sayısı. Bu değer bir zaman noktası değeridir.<br/><br/> Birim: sayım <br/> Toplama türü: Toplam <br/> Boyut: EntityName|
|ActiveListeners |Etkin dinleyicilerinin sayısı. Bu değer bir zaman noktası değeridir.<br/><br/> Birim: sayım <br/> Toplama türü: Toplam <br/> Boyut: EntityName|
|Listenerdisconnect |Belirtilen bir süre içinde bağlantısı kesilen dinleyicilerinin sayısı.<br/><br/> Birim: bayt <br/> Toplama türü: Toplam <br/> Boyut: EntityName|
|Gönderenin bağlantısı kesiliyor |Belirli bir süre içinde bağlantısı kesilen gönderici sayısı.<br/><br/> Birim: bayt <br/> Toplama türü: Toplam <br/> Boyut: EntityName|

## <a name="memory-usage-metrics"></a>Bellek kullanım ölçümleri

| Ölçüm Adı | Description |
| ------------------- | ----------------- |
|BytesTransferred |Belirtilen bir süre içinde aktarılan bayt sayısı.<br/><br/> Birim: bayt <br/> Toplama türü: Toplam <br/> Boyut: EntityName|

## <a name="metrics-dimensions"></a>Ölçüm boyutları

Azure Relay, Azure Izleyici 'de ölçümler için aşağıdaki boyutları destekler. Ölçümlere boyutlar eklemek isteğe bağlıdır. Boyut eklemeyin, ölçümler ad alanı düzeyinde belirtilir. 

|Boyut adı|Description|
| ------------------- | ----------------- |
|EntityName| Azure Relay ad alanı altındaki mesajlaşma varlıklarını destekler.|

## <a name="next-steps"></a>Sonraki adımlar

Bkz. [Azure izlemeye genel bakış](../azure-monitor/overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png
