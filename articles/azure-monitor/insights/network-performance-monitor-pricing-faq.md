---
title: Azure Ağ Performansı İzleyicisi için fiyatlandırma SSS | Microsoft Docs
description: Sık sorulan sorular-Azure Ağ Performansı İzleyicisi
ms.topic: conceptual
author: agummadi
ms.author: agummadi
ms.date: 04/02/2018
ms.openlocfilehash: 56163d3267a69099cbf6a18266168e9198ea04c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101717443"
---
# <a name="pricing-changes-for-azure-network-performance-monitor"></a>Azure Ağ Performansı İzleyicisi için fiyatlandırma değişiklikleri

> [!IMPORTANT]
> 1 Temmuz 2021 ' den itibaren, mevcut bir çalışma alanına yeni testler ekleyemez veya Ağ Performansı İzleyicisi yeni bir çalışma alanı etkinleştiremeyeceksiniz. 1 Temmuz 2021 ' den önce oluşturulan testleri kullanmaya devam edebilirsiniz. Geçerli iş yüklerinizde hizmet kesintisini en aza indirmek için, testlerinizi Ağ Performansı İzleyicisi 'den Azure ağ Izleyicisi 'ndeki [Yeni bağlantı izleyicisinden](../../network-watcher/migrate-to-connection-monitor-from-network-performance-monitor.md) , 29 Şubat 2024 tarihinden önce geçirin.

Geri bildiriminizi inceleyin ve kısa bir süre önce Azure 'daki çeşitli izleme hizmetleri için [Yeni bir fiyatlandırma deneyimi](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) sunduk. Bu makalede, kolay okunabilir bir soru ve yanıt biçiminde Azure [ağ performansı İzleyicisi](../../networking/network-monitoring-overview.md) (NPM) ile ilgili fiyatlandırma değişiklikleri yer alır.

Ağ Performansı İzleyicisi üç bileşenden oluşur:
* [Performans İzleyicisi](../../networking/network-monitoring-overview.md#performance-monitor)
* [Hizmet uç noktası Izleyicisi](../../networking/network-monitoring-overview.md)
* [ExpressRoute Izleyicisi](../../networking/network-monitoring-overview.md#expressroute-monitor)

Aşağıdaki bölümlerde NPM bileşenlerine ilişkin fiyatlandırma değişiklikleri açıklanmaktadır.

## <a name="performance-monitor"></a>Performans İzleyicisi

**Performans Izleyicisi kullanımı eski modelde nasıl faturalandırılır?**

NPM için faturalandırma, iki bileşenin kullanımına ve tüketimine dayalıdır:
* **Düğümler**: tüm yapay işlem, düğümlerde yapılır ve sonlandırılır. Düğümler, aracılar veya Microsoft Yönetim aracıları olarak da adlandırılır.
* **Veri**: çeşitli ağ testlerinin sonuçları Log Analytics çalışma alanında depolanır.

Eski modelin altında, fatura düğüm sayısına ve oluşturulan verilerin hacmine göre hesaplanır. 

**Performans Izleyicisi kullanımı yeni model kapsamında nasıl ücretlendirilir?**

NPM 'deki performans Izleyicisi özelliği şu bir birleşimine göre faturalandırılır: 

* İzlenen alt ağ bağlantıları
* Veri hacmi

**Alt ağ bağlantısı nedir?**

Performans Izleyicisi, ağ üzerindeki iki veya daha fazla konum arasındaki bağlantıyı izler. Bir alt ağda bulunan bir düğüm veya aracı arasındaki bağlantıya ve başka bir alt ağdaki bir düğüm grubuna alt ağ bağlantısı denir.

**İki alt ağa (A ve B) sahibim ve her alt ağda birkaç aracım var. Performans Izleyicisi A alt ağ üzerindeki tüm aracıların bağlantısını B alt ağındaki tüm aracılara izler. Alt ağ arası bağlantıların sayısına göre ücretlendirilmem gerekir mi?**

Hayır. Faturalama amacıyla alt ağ A 'dan alt ağ B 'ye yapılan tüm bağlantılar tek bir alt ağ bağlantısında birlikte gruplandırılır. Tek bir bağlantı için faturalandırılırsınız. Performans Izleyicisi her bir alt ağdaki çeşitli aracılar arasındaki bağlantıyı izlemeye devam eder.

**Bir alt ağ bağlantısını izlemeye yönelik maliyetler nelerdir?**

Tüm ay için tek bir alt ağ bağlantısını izlemenin maliyeti için, [ping kafesi](https://azure.microsoft.com/pricing/details/network-watcher/) bölümüne bakın.

**Performans Izleyicisinin oluşturduğu veriler için ücretler nelerdir?**

Alma ücreti (Azure Izleyici 'de Log Analytics çalışma alanına veri yükleme, işleme ve dizin oluşturma), veri alma bölümünde Log Analytics için [fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/log-analytics/) bulunabilir. Veri saklama ücreti (diğer bir deyişle, müşterinin seçeneğinde geçen veriler), veri saklama bölümünde [fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/log-analytics/)da kullanılabilir.


## <a name="expressroute-monitor"></a>ExpressRoute Izleyicisi

**ExpressRoute Izleyicisi kullanımı için ücretler nelerdir?**

ExpressRoute Izleyicisi ücretleri, izleme sırasında oluşturulan verilerin hacmine göre faturalandırılır. Daha fazla bilgi için bkz. "performans Izleyicisinin ürettiği veriler için ücretler nelerdir?"

**Birden çok ExpressRoute devresine izlemek için ExpressRoute Izleyicisini kullanıyorum. İzlenmekte olan devrelerin sayısına göre ücretlendirilir mi?**

Devre sayısı veya eşleme türü (örneğin, özel eşleme, Microsoft eşlemesi) temelinde ücretlendirilirsiniz. Daha önce açıklandığı gibi veri hacmine göre ücretlendirilirsiniz.

**ExpressRoute tek bir devreyi izlerken oluşturulan veri hacmi nedir?**

ExpressRoute özel bir eşleme bağlantısını izlerken, ayda oluşturulan veri hacmi aşağıdaki gibidir:

|Özelliğindeki      |Veri/ay (MB)|
| :---:          |           ---:|
|50<sup>TH</sup> |            192|
|60<sup>TH</sup> |            256|
|70<sup>TH</sup> |            360|
|80<sup>TH</sup> |            498|
|90<sup>TH</sup> |            870|
|95<sup>TH</sup> |           1560|


Bu tabloya göre, 50. yüzdedeki müşteriler 192 MB veri için ödeme yapar. İlk ay için ABD Doları $2.30/GB değerinde, bir devresinin izlenmesi için tahakkuk eden maliyet $0,43 ABD Doları (= 192 * 2,30/1024) olur.

**Veri hacminde çeşitliliğe ilişkin bazı nedenler nelerdir?**

Oluşturulan izleme verileri hacmi çeşitli etkenlere bağlıdır, örneğin:
* Aracı sayısı. Hata yalıtımının doğruluğu, aracıların sayısında bir artış ile artar.
* Ağdaki atlama sayısı.
* Kaynak ve hedef arasındaki yol sayısı.

Daha yüksek yüzdebirlik değeri (önceki tabloda) müşterileri, genellikle şirket içi ağı üzerinde birkaç bakış noktasından devrenlerini izler. Aynı zamanda, Service Provider Edge yönlendiricisinden daha da birçok aracı da daha ayrıntılı olarak yerleştirilir. Aracılar genellikle veri merkezlerinde birkaç Kullanıcı sitesine, dala ve raflara yerleştirilir.

## <a name="service-endpoint-monitor"></a>Hizmet uç noktası Izleyicisi

**Hizmet uç noktası Izleyicisi kullanımı için ücretler nelerdir?**

Hizmet uç noktası Izleyicisinin kullanımı için ücretler şu temel alınarak hesaplanır:
* Bağlantı sayısı
* Veri hacmi

**Bağlantı nedir?**

Bağlantı, tüm ay için tek bir aracıdan bir uç noktaya (URL veya ağ hizmeti) ulaşılabilmesi için bir sınamadır. Örneğin, üç aracıdan bing.com bağlantısının izlenmesi üç bağlantı oluşturur.

**Hizmet uç noktası Izleyicisi için maliyetler nelerdir?**

Tüm ay için bir uç nokta izlemenin maliyeti için [bağlantı izleme](https://azure.microsoft.com/pricing/details/network-watcher/) bölümüne bakın. Verilerin ücreti, veri alımı bölümünde Log Analytics için [fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/log-analytics/) kullanılabilir.

## <a name="references"></a>Başvurular

[Log Analytics FIYATLANDıRMASı SSS](https://azure.microsoft.com/pricing/details/log-analytics/): SSS bölümünde, düğüm başına fiyatlandırma ve diğer fiyatlandırma ayrıntıları hakkında ücretsiz katman bilgileri bulunur.