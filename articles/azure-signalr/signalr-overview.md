---
title: Azure SignalR hizmeti nedir?
description: Azure SignalR kullanmaya yönelik tipik kullanım örneği senaryolarını daha iyi anlayın ve Azure SignalR 'nin önemli avantajlarını öğrenin.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 31cb4570dd0952f60fbff2fa1131b3af36c278cb
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74157584"
---
# <a name="what-is-azure-signalr-service"></a>Azure SignalR hizmeti nedir?

Azure SignalR Hizmeti, HTTP üzerinden uygulamalara gerçek zamanlı web işlevselliği ekleme işlemini basitleştirir. Bu gerçek zamanlı işlevsellik, hizmetin bağlı istemcilere tek sayfalık bir web veya mobil uygulaması gibi içerik güncelleştirmeleri göndermesine olanak tanır. Sonuç olarak, istemciler sunucuyu yoklama veya yeni HTTP isteklerini güncelleştirmek üzere gönderme gereksinimi olmadan güncelleştirilir.


Bu makalede Azure SignalR Hizmetine genel bir bakış sunulmaktadır.

## <a name="what-is-azure-signalr-service-used-for"></a>Azure SignalR Hizmeti ne için kullanılır?

Verilerin sunucudan istemciye gerçek zamanlı olarak gönderilmesi gereken herhangi bir senaryo, Azure SignalR hizmetini kullanabilir.

Genellikle sunucudan yoklama gerektiren geleneksel gerçek zamanlı özellikler de Azure SignalR hizmetini kullanabilir.

Azure SignalR hizmeti, gerçek zamanlı içerik güncelleştirmeleri gerektiren tüm uygulama türleri için çok çeşitli sektörlerde kullanılmıştır. Azure SignalR hizmetini kullanmak için iyi bazı örnekler listeliyoruz:

* **Yüksek frekanslı veri güncelleştirmeleri:** oyun, oylama, yoklama, açık artırma.
* **Panolar ve izleme:** şirket panosu, finansal pazar verileri, Instant Sales Update, çok oyunculu oyun lideri panosu ve IoT izleme.
* **Sohbet:** canlı sohbet odası, sohbet bot, çevrimiçi müşteri desteği, gerçek zamanlı alışveriş yardımcısı, Messenger, oyun oyunu vb.
* **Haritadaki gerçek zamanlı konum:** lojistik Tracking, teslim durumu izleme, ulaşım durum GÜNCELLEŞTIRMELERI, GPS Apps.
* **Gerçek zamanlı hedeflenen reklamlar:** kişiselleştirilmiş gerçek zamanlı anında iletme reklamları ve teklifleri, etkileşimli reklamlar.
* **Işbirliğine dayalı uygulamalar:** coauthoring, beyaz tahta uygulamaları ve takım toplantısı yazılımı.
* **Anında iletme bildirimleri:** sosyal ağ, e-posta, oyun, seyahat uyarısı.
* **Gerçek zamanlı yayınlar:** canlı ses/video yayını, canlı açıklamalı altyazı, çeviri, olaylar/haber yayınlama.
* **IoT ve bağlı cihazlar:** gerçek zamanlı IoT ölçümleri, uzaktan denetim, gerçek zamanlı durum ve konum izleme.
* **Otomasyon:** yukarı akış olaylarından gerçek zamanlı tetikleyici.

## <a name="what-are-the-benefits-using-azure-signalr-service"></a>Azure SignalR hizmeti 'ni kullanmanın avantajları nelerdir?

**Standart tabanlı:**

SignalR, gerçek zamanlı web uygulamaları derlemek için kullanılan birkaç tekniğin özetini sunar. [WebSockets](https://wikipedia.org/wiki/WebSocket) ideal aktarım yöntemidir ancak başka seçenek olmadığında [Sunucu ile Gönderilen Olaylar (SSE)](https://wikipedia.org/wiki/Server-sent_events) ve Uzun Yoklama gibi diğer teknikler de kullanılır. SignalR, sunucu ve istemci üzerinde desteklenen özelliklere göre uygun taşıma tekniğini otomatik olarak algılar ve başlatır.

**Yerel ASP.NET Core desteği:**

SignalR hizmeti, ASP.NET Core ve ASP.NET ile yerel programlama deneyimi sağlar. SignalR hizmeti ile yeni bir SignalR uygulaması geliştirme veya mevcut bir SignalR tabanlı uygulamadan SignalR hizmetine geçiş için en az çaba olması gerekir.
SignalR hizmeti Ayrıca ASP.NET Core 'ın yeni özelliğini, sunucu tarafı Blazor destekler.

**Geniş istemci desteği:**

SignalR hizmeti, Web ve mobil tarayıcılar, masaüstü uygulamaları, mobil uygulamalar, sunucu işlemi, IoT cihazları ve oyun konsolları gibi çok çeşitli istemcilerle çalışmaktadır. SignalR hizmeti, SDK 'Ları farklı dillerde sunmaktadır. SignalR hizmeti, yerel ASP.NET Core veya C# ASP.NET SDK 'lara ek olarak JavaScript istemci SDK 'sını, Web istemcilerini etkinleştirmek ve birçok JavaScript çerçevesini de sağlar. Java istemci SDK 'Sı, Android yerel uygulamaları dahil olmak üzere Java uygulamaları için de desteklenir. SignalR hizmeti, Azure Işlevleri ve Event Grid tümleştirmeler aracılığıyla REST API ve sunucusuz destekler.

**Büyük ölçekli istemci bağlantılarını işle:**

SignalR hizmeti büyük ölçekli gerçek zamanlı uygulamalar için tasarlanmıştır. SignalR hizmeti birden fazla örneğinin birlikte çalışarak milyonlarca istemci bağlantısına ölçeklenebilmesini sağlar. Hizmet aynı zamanda parçalama, yüksek kullanılabilirlik veya olağanüstü durum kurtarma amacıyla birden çok genel bölgeyi destekler.

**Self-Host SignalR 'nin yükünü kaldırın:**

Kendi kendine barındırılan SignalR uygulamalarıyla karşılaştırıldığında, SignalR hizmetine geçiş yapmak, ölçek ve istemci bağlantılarını işleyen arka düzlemleri yönetme ihtiyacını ortadan kaldırır. Tam olarak yönetilen hizmet ayrıca Web uygulamalarını basitleştirir ve barındırma maliyetini kaydeder. SignalR hizmeti küresel erişim ve birinci sınıf veri merkezi ve ağ sunar, milyonlarca bağlantıya ölçeklenirken, Azure Standard 'da tüm uyumluluk ve güvenliği sağlarken SLA garantisi verir.

![Yönetilen SignalR hizmeti](./media/signalr-overview/managed-signalr-service.png)

**Farklı mesajlaşma desenleri için zengin API 'Ler sunun:**

SignalR hizmeti, sunucunun belirli bir bağlantıya, tüm bağlantılara veya belirli bir kullanıcıya ait olan ya da rastgele bir gruba yerleştirilmiş bir bağlantı alt kümesine ileti göndermesini sağlar.

## <a name="how-to-use-azure-signalr-service"></a>Azure SignalR Service Nasıl Kullanılır

Burada listelenen bazı örnekler için Azure SignalR hizmeti ile programlama için birçok farklı yol vardır:

- **[Bir ASP.NET Core SignalR Uygulamasını Ölçeklendirme](signalr-concept-scale-aspnet-core.md)** - Yüzbinlerce bağlantıya ölçeklendirmek için Azure SignalR Hizmetini bir ASP.NET Core SignalR uygulaması ile tümleştirin.
- **[Sunucusuz, gerçek zamanlı uygulamalar oluşturma](signalr-concept-azure-functions.md)** JavaScript, C# ve Java gibi dillerde sunucusuz, gerçek zamanlı uygulamalar oluşturmak için Azure İşlevleri tümleştirmesini Azure SignalR Hizmeti ile kullanın.
- **[REST API aracılığıyla istemcilere sunucudan iletileri gönderme](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)** - Azure SignalR Hizmeti, REST kullanılabilen tüm dillerle uygulamaların SignalR Hizmeti ile bağlı istemcilere iletiler gönderebilmesi için REST API’sini sunar.
