---
title: Azure Izleyici 'de Azure Relay ölçümleri (Önizleme) | Microsoft Docs
description: Azure Relay izlemek için Azure Izlemeyi kullanın
services: service-bus-relay
documentationcenter: .NET
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: spelluru
ms.openlocfilehash: 9933b7857580f29cbb6a54ebc82d4e7207456ad4
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261782"
---
# <a name="azure-relay-metrics-in-azure-monitor-preview"></a>Azure Izleyici 'de Azure Relay ölçümleri (Önizleme)
Azure Relay ölçümler, Azure aboneliğinizdeki kaynakların durumunu sağlar. Zengin bir ölçüm verileri kümesiyle, geçiş kaynaklarınızın genel durumunu yalnızca ad alanı düzeyinde değil, aynı zamanda varlık düzeyinde değerlendirebilirsiniz. Bu istatistikler Azure Relay durumunu izlemenize yardımcı olduklarından önemli olabilir. Ölçümler, Azure desteğine başvurun gerek kalmadan kök neden sorunlarını da yardımcı olabilir.

Azure İzleyici, çeşitli Azure Hizmetleri genelinde izleme için birleştirilmiş bir kullanıcı arabirimi sağlar. Daha fazla bilgi için [Microsoft Azure'da izleme](../monitoring-and-diagnostics/monitoring-overview.md) ve [almak Azure İzleyici ölçümleri .NET ile](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) GitHub üzerinde örnek.

> [!IMPORTANT]
> Bu makale, WCF Geçişi değil, yalnızca Azure Relay Karma Bağlantılar özelliği için geçerlidir. 

## <a name="access-metrics"></a>Erişim ölçümleri

Azure İzleyici ölçümlerine erişim birden çok yol sağlar. Ya da erişim ölçümleri ile yapabilecekleriniz [Azure portalında](https://portal.azure.com), veya Azure İzleyici API'leri (REST ve .NET) ve Operations Management Suite ve Event Hubs gibi analiz çözümleri kullanın. Daha fazla bilgi için [İzleme verilerine Azure İzleyicisi tarafından toplanan](../azure-monitor/platform/data-platform.md).

Ölçümler, varsayılan olarak etkindir ve en son 30 Günün verilerini erişebilir. Uzun bir süre saklamak istiyorsanız ölçüm verileri bir Azure depolama hesabına arşivleyebilir. Bu yapılandırılan [tanılama ayarları](../azure-monitor/platform/diagnostic-settings.md) Azure İzleyici'de.

## <a name="access-metrics-in-the-portal"></a>Portalda erişim ölçümlerini

Zaman içinde ölçümleri izleyebilirsiniz [Azure portalında](https://portal.azure.com). Aşağıdaki örnek, başarılı istekleri ve hesap düzeyinde gelen istekleri görüntülemek gösterilmektedir:

![][1]

Ad alanı aracılığıyla doğrudan ölçümleri de erişebilirsiniz. Bunu yapmak için ad alanınızı seçin ve ardından **ölçümler (Önizleme)** öğesine tıklayın. 

Boyutları destekleyen ölçümler için, istenen boyut değeri ile filtrelemeniz gerekir.

## <a name="billing"></a>Faturalandırma

Ölçümleri kullanarak Azure İzleyici'de önizleme şu an ücretsizdir. Ölçüm verilerini alma, ek çözümleri kullanırsanız, ancak bu çözümler tarafından faturalandırılırsınız. Örneğin, ölçüm verileri bir Azure depolama hesabına arşivleme, Azure Depolama tarafından faturalandırılır. Ayrıca, ölçüm verilerini gelişmiş analizler için Azure Izleyici günlüklerine kadıysanız Azure Izleyici günlükleri tarafından da faturalandırılırsınız.

Aşağıdaki ölçümler size sistem hizmetinizin genel bakış sunar. 

> [!NOTE]
> Farklı bir adla geçildiği size çeşitli ölçümleri kullanımdan. Bu, başvurularını güncelleştirmek gerektirebilir. "Kullanım dışı" anahtar sözcüğüyle işaretli ölçümleri, bundan sonra desteklenmeyecek.

Azure İzleyici, tüm ölçüm değerleri dakikada gönderilir. Zaman ayrıntı düzeyi ölçüm değerleri sunulduğu zaman aralığını tanımlar. Tüm Azure Relay ölçümleri için desteklenen zaman aralığı 1 dakikadır.

## <a name="connection-metrics"></a>Bağlantı ölçümü

| Ölçüm Adı | Açıklama |
| ------------------- | ----------------- |
| ListenerConnections-başarılı (Önizleme) | Belirli bir süre içinde Azure Relay yapılan başarılı dinleyici bağlantısı sayısı. <br/><br/> Birim Count <br/> Toplama türü: Toplam <br/> Boyut EntityName|
|ListenerConnections-ClientError (Önizleme)|Belirli bir süre boyunca dinleyici bağlantılarında istemci hatalarının sayısı.<br/><br/> Birim Count <br/> Toplama türü: Toplam <br/> Boyut EntityName|
|ListenerConnections-Sunucuhatası (Önizleme)|Belirli bir süre boyunca dinleyici bağlantılarında sunucu hatalarının sayısı.<br/><br/> Birim Count <br/> Toplama türü: Toplam <br/> Boyut EntityName|
|SenderConnections-başarılı (Önizleme)|Belirli bir süre içinde yapılan başarılı gönderici bağlantısı sayısı.<br/><br/> Birim Count <br/> Toplama türü: Toplam <br/> Boyut EntityName|
|SenderConnections-ClientError (Önizleme)|Belirli bir süre boyunca gönderici bağlantılarında istemci hatalarının sayısı.<br/><br/> Birim Count <br/> Toplama türü: Toplam <br/> Boyut EntityName|
|SenderConnections-Sunucuhatası (Önizleme)|Belirli bir süre boyunca gönderici bağlantılarında sunucu hatalarının sayısı.<br/><br/> Birim Count <br/> Toplama türü: Toplam <br/> Boyut EntityName|
|ListenerConnections-TotalRequests (Önizleme)|Belirli bir süre içindeki dinleyici bağlantılarının toplam sayısı.<br/><br/> Birim Count <br/> Toplama türü: Toplam <br/> Boyut EntityName|
|SenderConnections-TotalRequests (Önizleme)|Belirli bir süre içinde göndericiler tarafından yapılan bağlantı istekleri.<br/><br/> Birim Count <br/> Toplama türü: Toplam <br/> Boyut EntityName|
|ActiveConnections (Önizleme)|Belirli bir dönemdeki etkin bağlantı sayısı.<br/><br/> Birim Count <br/> Toplama türü: Toplam <br/> Boyut EntityName|
|ActiveListeners (Önizleme)|Belirli bir dönemdeki etkin dinleyici sayısı.<br/><br/> Birim Count <br/> Toplama türü: Toplam <br/> Boyut EntityName|
|Listenerdisconnect (Önizleme)|Belirtilen bir süre içinde bağlantısı kesilen dinleyicilerinin sayısı.<br/><br/> Birim Bayt <br/> Toplama türü: Toplam <br/> Boyut EntityName|
|Gönderenin bağlantısı kesiliyor (Önizleme)|Belirli bir süre içinde bağlantısı kesilen gönderici sayısı.<br/><br/> Birim Bayt <br/> Toplama türü: Toplam <br/> Boyut EntityName|

## <a name="memory-usage-metrics"></a>Bellek kullanım ölçümleri

| Ölçüm Adı | Açıklama |
| ------------------- | ----------------- |
|BytesTransferred (Önizleme)|Belirtilen bir süre içinde aktarılan bayt sayısı.<br/><br/> Birim Bayt <br/> Toplama türü: Toplam <br/> Boyut EntityName|

## <a name="metrics-dimensions"></a>Ölçümleri boyutları

Azure Relay, Azure Izleyici 'de ölçümler için aşağıdaki boyutları destekler. Boyutları için ölçümlerinizi eklemek isteğe bağlıdır. Ölçümleri, boyutları eklemezseniz, ad alanı düzeyinde belirtilir. 

|Boyut adı|Açıklama|
| ------------------- | ----------------- |
|EntityName| Azure Relay ad alanı altındaki mesajlaşma varlıklarını destekler.|

## <a name="next-steps"></a>Sonraki adımlar

Bkz: [Azure izlemeye genel bakış](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png




