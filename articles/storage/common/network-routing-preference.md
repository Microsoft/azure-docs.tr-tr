---
title: Ağ yönlendirme tercihi
titleSuffix: Azure Storage
description: Ağ yönlendirme tercihi, ağ trafiğinin Internet üzerinden istemcilerden hesabınıza nasıl yönlendirildiğini belirtmenizi sağlar.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.custom: references_regions
ms.openlocfilehash: bf2270fe6f71dfe5be31db8e82f6c44696f28074
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103601500"
---
# <a name="network-routing-preference-for-azure-storage"></a>Azure depolama için ağ yönlendirme tercihi

Ağ trafiğinin internet üzerinden kendi hesabınıza nasıl yönlendirildiğini belirtmek için, Azure depolama hesabınız için ağ [yönlendirme tercihini](../../virtual-network/routing-preference-overview.md) yapılandırabilirsiniz. Varsayılan olarak, internet 'ten gelen trafik, [Microsoft Global ağı](../../networking/microsoft-global-network.md)üzerinden depolama hesabınızın genel uç noktasına yönlendirilir. Azure depolama, trafiğin depolama hesabınıza nasıl yönlendirildiğini yapılandırmak için ek seçenekler sağlar.

Yönlendirme tercihini yapılandırmak, Premium ağ performansı veya maliyeti için trafiğinizi iyileştirmek için esneklik sağlar. Bir yönlendirme tercihi yapılandırdığınızda, trafiğin varsayılan olarak depolama hesabınız için genel uç noktaya nasıl yönlendirilmeyeceğini belirtirsiniz. Depolama hesabınız için rotaya özgü uç noktaları da yayımlayabilirsiniz.

> [!NOTE]
> Bu özellik, Premium performans katmanını veya bölgesel olarak yedekli depolamayı (ZRS) kullanmak üzere yapılandırılmış depolama hesaplarında desteklenmez.

## <a name="microsoft-global-network-versus-internet-routing"></a>Microsoft Global Network ve Internet yönlendirme karşılaştırması

Varsayılan olarak, Azure ortamının dışındaki istemciler, depolama hesabınıza Microsoft küresel ağı üzerinden erişir. Microsoft Global Network, yüksek güvenilirlikle Premium ağ performansı sunmak için düşük gecikmeli yol seçimi için iyileştirilmiştir. Hem gelen hem de giden trafik, istemciye en yakın olan varlık noktası (POP) aracılığıyla yönlendirilir. Bu varsayılan yönlendirme yapılandırması, depolama hesabınıza gelen ve giden trafiğin yolun toplu olarak Microsoft Global ağı üzerinden geçiş yapmalarını sağlar ve ağ performansını en üst düzeye çıkarır.

Depolama hesabınızın yönlendirme yapılandırmasını, hem gelen hem de giden trafiğin depolama hesabına en yakın POP aracılığıyla Azure ortamı dışındaki istemcilere yönlendirilmesi için değiştirebilirsiniz. Bu rota, Microsoft küresel ağı üzerinden trafiğinizi çapraz geçişi en aza indirir ve bu hizmet ilk fırsatta transit ISS 'ye teslim edilmesini sağlar. Bu yönlendirme yapılandırmasının kullanılmasıyla ağ maliyetlerini düşürür.

Aşağıdaki diyagramda, her yönlendirme tercihi için istemci ve depolama hesabı arasındaki trafik akışı gösterilmektedir:

![Azure depolama için yönlendirme seçeneklerine genel bakış](media/network-routing-preference/routing-options-diagram.png)

Azure 'da yönlendirme tercihi hakkında daha fazla bilgi için bkz. [yönlendirme tercihi nedir?](../../virtual-network/routing-preference-overview.md).

## <a name="routing-configuration"></a>Yönlendirme yapılandırması

Yönlendirme tercihi ve rotaya özgü uç noktaların nasıl yapılandırılacağını gösteren adım adım yönergeler için bkz. [Azure Storage için ağ yönlendirmeyi yapılandırma tercihi](configure-network-routing-preference.md).

Depolama hesabınızın genel uç noktası için varsayılan yönlendirme tercihi olarak Microsoft küresel ağ ve internet yönlendirmesi arasında seçim yapabilirsiniz. Varsayılan yönlendirme tercihi, Azure dışındaki istemcilerden gelen tüm trafiğe uygulanır ve Azure Data Lake Storage 2., BLOB depolama, Azure dosyaları ve statik Web siteleri için uç noktaları etkiler. Yönlendirme tercihini yapılandırma, Azure kuyrukları veya Azure tabloları için desteklenmez.

Depolama hesabınız için rotaya özgü uç noktaları da yayımlayabilirsiniz. Rotaya özgü uç noktaları yayımladığınızda, Azure Storage, depolama hesabınız için trafiği istenen yol üzerinden yönlendiren yeni genel uç noktalar oluşturur. Bu esneklik, varsayılan yönlendirme tercihinizi değiştirmeden, trafiği belirli bir rota üzerinden depolama hesabınıza yönlendirmenizi sağlar.

Örneğin, ' StorageAccountA ' için internet 'e özgü bir uç nokta yayımlama, depolama hesabınız için aşağıdaki uç noktaları yayımlayacak:

| Depolama hizmeti        | Rotaya özgü uç nokta                                  |
| :--------------------- | :------------------------------------------------------- |
| Blob hizmeti           | `StorageAccountA-internetrouting.blob.core.windows.net`  |
| Data Lake Storage 2. Nesil | `StorageAccountA-internetrouting.dfs.core.windows.net`   |
| Dosya hizmeti           | `StorageAccountA-internetrouting.file.core.windows.net`  |
| Statik Web siteleri        | `StorageAccountA-internetrouting.web.core.windows.net`   |

Bir Okuma Erişimli Coğrafi olarak yedekli depolama (RA-GRS) veya Okuma Erişimli Coğrafi bölge yedekli depolama (RA-GZRS) depolama hesabınız varsa, rotaya özgü uç noktalar yayımlama, otomatik olarak, okuma erişimi için ikincil bölgede ilgili uç noktaları da oluşturur.

| Depolama hizmeti        | Rotaya özgü salt okunurdur ikincil uç nokta                        |
| :--------------------- | :----------------------------------------------------------------- |
| Blob hizmeti           | `StorageAccountA-internetrouting-secondary.blob.core.windows.net`  |
| Data Lake Storage 2. Nesil | `StorageAccountA-internetrouting-secondary.dfs.core.windows.net`   |
| Dosya hizmeti           | `StorageAccountA-internetrouting-secondary.file.core.windows.net`  |
| Statik Web siteleri        | `StorageAccountA-internetrouting-secondary.web.core.windows.net`   |

Yayımlanan rotaya özgü uç noktalara yönelik bağlantı dizeleri [Azure Portal](https://portal.azure.com)aracılığıyla kopyalanabilir. Bu bağlantı dizeleri, paylaşılan anahtar yetkilendirmesi için mevcut tüm Azure depolama SDK 'ları ve API 'Leri ile kullanılabilir.

## <a name="regional-availability"></a>Bölgesel kullanılabilirlik

Azure depolama için yönlendirme tercihi aşağıdaki bölgelerde kullanılabilir:

- Central US 
- EUAP Orta ABD
- Doğu ABD 
- Doğu ABD 2
- Doğu ABD 2 
- EUAP Doğu ABD 2
- Orta Güney ABD
- Orta Batı ABD
- Batı ABD 
- Batı ABD 2 
- Orta Fransa 
- Güney Fransa 
- Almanya Kuzey 
- Almanya Orta Batı 
- Orta Kuzey ABD
- Kuzey Avrupa 
- Norveç Doğu 
- İsviçre Kuzey
- İsviçre Batı
- Güney Birleşik Krallık 
- Batı Birleşik Krallık 
- West Europe 
- BAE Orta
- Doğu Asya 
- Güneydoğu Asya 
- Doğu Japonya 
- Batı Japonya 
- Batı Hindistan
- Doğu Avustralya 
- Güneydoğu Avustralya 

Aşağıdaki bilinen sorunlar, Azure depolama için yönlendirme tercihini etkiler:

- Microsoft Global Network 'e yönelik rotaya özgü uç noktaya yönelik erişim istekleri HTTP hatası 404 veya eşdeğeri ile başarısız olur. Microsoft Global ağı üzerinden yönlendirme, genel uç nokta için varsayılan yönlendirme tercihi olarak ayarlandığında beklendiği gibi çalışmaktadır.

## <a name="pricing-and-billing"></a>Fiyatlandırma ve Faturalama

Fiyatlandırma ve faturalandırma ayrıntıları için, [yönlendirme tercihi nedir?](../../virtual-network/routing-preference-overview.md#pricing)bölümündeki **fiyatlandırma** bölümüne bakın.

## <a name="next-steps"></a>Sonraki adımlar

- [Yönlendirme tercihi nedir?](../../virtual-network/routing-preference-overview.md)
- [Ağ yönlendirme tercihini yapılandırma](configure-network-routing-preference.md)
- [Azure Depolama güvenlik duvarlarını ve sanal ağları yapılandırma](storage-network-security.md)
- [BLOB depolama için güvenlik önerileri](../blobs/security-recommendations.md)
