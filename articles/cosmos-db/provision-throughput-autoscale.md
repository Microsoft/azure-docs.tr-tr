---
title: Azure Cosmos kapsayıcıları ve veritabanlarını otomatik ölçeklendirme modunda oluşturun.
description: Avantajlar, kullanım örnekleri ve Azure Cosmos veritabanları ile kapsayıcıları otomatik ölçeklendirme modunda sağlama hakkında bilgi edinin.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/11/2020
ms.custom: seo-nov-2020
ms.openlocfilehash: ba0dd347c4ee2cb41b34c2fc34f1848a7295dc3a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97368673"
---
# <a name="create-azure-cosmos-containers-and-databases-with-autoscale-throughput"></a>Otomatik ölçeklendirme işleme ile Azure Cosmos kapsayıcıları ve veritabanları oluşturma
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB, veritabanlarınızda ve kapsayıcılarınızda standart (el ile) ya da otomatik ölçeklendirme sağlanan aktarım hızını yapılandırabilirsiniz. Azure Cosmos DB tarafından otomatik olarak sağlanan aktarım hızı **, veritabanınızın veya kapsayıcının verimini (ru/s) otomatik olarak ve anında ölçeklendirmenize** olanak tanır. Aktarım hızı, kullanımın kullanılabilirliğini, gecikme süresini, verimini veya performansını etkilemeden, kullanıma göre ölçeklendirilir.

Otomatik ölçeklendirme sağlanan aktarım hızı, değişken veya öngörülemeyen trafik desenleri olan görev açısından kritik iş yükleri için uygundur ve yüksek performans ve ölçekte SLA 'Lar gerektirir. Bu makalede otomatik ölçeklendirme ile sağlanan işlem hızının avantajları ve kullanım örnekleri açıklanır.

## <a name="benefits-of-autoscale"></a>Otomatik ölçeklendirmeyi avantajları

Otomatik ölçeklendirme sağlanan aktarım hızı ile yapılandırılan Azure Cosmos veritabanları ve kapsayıcıları aşağıdaki avantajlara sahiptir:

* **Basit:** Otomatik ölçeklendirme, RU/s 'yi özel betik oluşturma veya el ile ölçeklendirme kapasitesi ile yönetme karmaşıklığını ortadan kaldırır. 

* **Ölçeklenebilir:** Veritabanları ve kapsayıcılar, sağlanan aktarım hızını gerektiği şekilde otomatik olarak ölçeklendirir. İstemci bağlantıları, uygulamalar veya Azure Cosmos DB SLA 'Lara etkisi yoktur.

* Uygun **maliyetli:** Otomatik ölçeklendirme, kullanımda olmadığında ölçeği ölçeklendirerek RU/s kullanımınızı ve maliyet kullanımınızı iyileştirmenize yardımcı olur. Yalnızca iş yüklerinizin saat başına ihtiyaç duyduğu kaynaklar için ödeme yaparsınız. Ayın tüm saatlerinde, otomatik ölçeklendirme en fazla RU/sn (Tmax) ayarlarsanız ve saat veya daha az %66 için tam miktar Tmax kullanırsanız, otomatik ölçeklendirme ile tasarruf edersiniz. Daha fazla bilgi edinmek için bkz. [Standart (el ile) ve otomatik ölçeklendirme sağlanan üretilen iş](how-to-choose-offer.md) oluşturma makalesini seçme.

* **Yüksek oranda kullanılabilir:** Otomatik ölçeklendirme kullanan veritabanları ve kapsayıcılar, veri dayanıklılığı ve yüksek kullanılabilirlik sağlamak için aynı küresel olarak dağıtılmış, hataya dayanıklı, yüksek oranda kullanılabilir Azure Cosmos DB arka ucunu kullanır.

## <a name="use-cases-of-autoscale"></a>Otomatik ölçeklendirme durumlarını kullanma

Otomatik ölçeklendirmeyi kullanım örnekleri şunları içerir:

* **Değişken veya öngörülemeyen iş yükleri:** İş yükleriniz, kullanımda olan veya öngörülemeyen ani artışlar olduğunda, otomatik ölçeklendirme, kullanıma göre otomatik olarak ölçeği artırma ve azaltma ile yardımcı olur. Farklı trafik desenlerine sahip olan perakende Web siteleri, mevsimsellik 'e bağlı olarak verilebilir; Gün boyunca çeşitli zamanlarda ani artışlar olan ıOT iş yükleri; en yoğun kullanımı bir ay veya yılda birkaç kez ve daha fazlasını görmek için iş kolu uygulamaları. Otomatik ölçeklendirme ile, artık tepe veya ortalama kapasite için el ile sağlamanız gerekmez. 

* **Yeni uygulamalar:** Yeni bir uygulama geliştiriyorsanız ve ihtiyacınız olan aktarım hızı (RU/s) hakkında emin değilseniz, otomatik ölçeklendirme çalışmaya başlamanızı kolaylaştırır. 400-4000 RU/s otomatik ölçeklendirme giriş noktasıyla başlayabilir, kullanımınızı izleyebilir ve zaman içinde doğru RU/s 'yi belirleyebilirsiniz.

* **Sık kullanılmayan uygulamalar:** Yalnızca birkaç saat için bir gün, hafta veya ayda birkaç kez kullanılan bir uygulamanız varsa (düşük hacimli bir uygulama/web/blog sitesi gibi), otomatik ölçeklendirme, yoğun kullanımı işlemek için kapasiteyi ayarlar ve üzerindeyken ölçeği ölçeklendirir. 

* **Geliştirme ve test iş yükleri:** Siz veya ekibiniz iş saatleri sırasında Azure Cosmos veritabanlarını ve kapsayıcılarını kullanıyorsa, ancak bunlara gece veya hafta sonları gerekmiyorsa, otomatik ölçeklendirme, kullanımda olmadığında minimum olarak ölçeklendirerek maliyetten tasarruf etmenize yardımcı olur. 

* **Zamanlanan üretim iş yükleri/sorguları:** Boşta kalma süreleri sırasında çalıştırmak istediğiniz bir dizi zamanlanmış isteğiniz, işlem veya sorgunuz varsa, bunu otomatik ölçeklendirme ile kolayca yapabilirsiniz. İş yükünü çalıştırmanız gerektiğinde, üretilen iş için otomatik olarak ölçeklenecektir ve daha sonra ölçeği ölçeklendirirsiniz. 

Bu sorunlara özel bir çözüm oluşturmak yalnızca çok büyük bir süre gerektirmesinin yanı sıra uygulamanızın yapılandırmasında veya kodunda karmaşıklık de sağlar. Otomatik ölçeklendirme, yukarıdaki senaryolara izin vermez ve kapasitenin özel veya el ile ölçeklendirilmesi gereksinimini ortadan kaldırır. 

## <a name="how-autoscale-provisioned-throughput-works"></a>Otomatik ölçeklendirme sağlanan aktarım hızı nasıl kullanılır

Kapsayıcılar ve veritabanlarını otomatik ölçeklendirme ile yapılandırırken, gereken en yüksek aktarım hızını belirtirsiniz `Tmax` . Azure Cosmos DB, aktarım hızını `T` ölçeklendirir `0.1*Tmax <= T <= Tmax` . Örneğin, en fazla üretilen işi 20.000 RU/sn olarak ayarlarsanız, verimlilik 2000 ila 20.000 RU/s arasında ölçeklendirecektir. Ölçeklendirme otomatik ve anlık olduğundan, herhangi bir zamanda herhangi bir zaman, hiçbir gecikme olmadan sağlanmış olarak kullanabilirsiniz `Tmax` . 

Her saat, `T` sistemin saat içinde ölçeklendirileceği en yüksek aktarım hızı için faturalandırılırsınız.

Otomatik ölçeklendirme en yüksek aktarım hızı için giriş noktası, `Tmax` 400-4000 ru/s arasında ölçeklendirilebilen 4000 ru/sn 'den başlar. `Tmax`1000 ru/sn 'lik artışlarla ayarlanabilir ve değeri dilediğiniz zaman değiştirebilirsiniz.  

## <a name="enable-autoscale-on-existing-resources"></a>Varolan kaynaklarda otomatik ölçeklendirmeyi etkinleştir

Varolan bir veritabanı veya kapsayıcıda otomatik ölçeklendirmeyi etkinleştirmek için [Azure Portal](how-to-provision-autoscale-throughput.md#enable-autoscale-on-existing-database-or-container), [Azure Resource Manager şablonu](how-to-provision-autoscale-throughput.md#azure-resource-manager), [CLI](how-to-provision-autoscale-throughput.md#azure-cli) veya [PowerShell](how-to-provision-autoscale-throughput.md#azure-powershell) kullanın. İstediğiniz zaman otomatik ölçeklendirme ve standart (el ile) sağlanan aktarım hızı arasında geçiş yapabilirsiniz. Daha fazla bilgi için bu [belgelere](autoscale-faq.md#how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work) bakın.

## <a name="throughput-and-storage-limits-for-autoscale"></a><a id="autoscale-limits"></a> Otomatik ölçeklendirme için üretilen iş ve depolama sınırları

Herhangi bir değeri için `Tmax` , veritabanı veya kapsayıcı bir toplam depolayabilirler `0.01 * Tmax GB` . Bu depolama alanı miktarına ulaşıldıktan sonra maksimum RU/sn değeri yeni depolama alanı değerine göre otomatik olarak artırılır ve bu durum uygulamanızı etkilemez. 

Örneğin 50.000 RU/sn (5000 ile 50.000 RU/sn arasında ölçeklendirilir) maksimum RU/sn değeriyle başlarsanız en fazla 500 GB veri depolayabilirsiniz. 500 GB düzeyini aşarsanız (örneğin depolama alanı 600 GB olursa) yeni maksimum RU/sn değeri 60.000 RU/sn (6000 ile 60.000 RU/sn arasında ölçeklendirilir) olacaktır.

Otomatik ölçeklendirme ile veritabanı düzeyinde işlem hızını kullanırsanız 40 GB depolama alanını aşmadığınız sürece ilk 25 kapsayıcının 4000 (400 ile 4000 RU/sn arasında ölçeklendirilir) otomatik ölçeklendirme maksimum RU/sn değerini paylaşmasını sağlayabilirsiniz. Daha fazla bilgi için bu [belgelere](autoscale-faq.md#can-i-change-the-max-rus-on-the-database-or-container) bakın.

## <a name="comparison--containers-configured-with-manual-vs-autoscale-throughput"></a>Karşılaştırma – elle ve otomatik ölçeklendirme işleme ile yapılandırılmış kapsayıcılar
Daha fazla ayrıntı için, standart (el ile) ve otomatik ölçeklendirme verimlilik arasından seçim yapma hakkında bu [belgelere](how-to-choose-offer.md) bakın.  

|| Standart (el ile) aktarım hızı olan kapsayıcılar  | Otomatik ölçeklendirme işleme içeren kapsayıcılar |
|---------|---------|---------|
| **Sağlanan aktarım hızı (RU/s)** | El ile sağlandı. | Otomatik olarak ve anında iş yükü kullanım desenlerine göre ölçeklendirildi. |
| **İsteklerin/işlemlerin hız sınırlaması (429)**  | Tüketim sağlanan kapasiteyi aşarsa meydana gelebilir. | , Belirlediğiniz otomatik ölçeklendirme üretilen iş aralığı içinde RU/s kullanırsanız gerçekleşmeyecektir.    |
| **Kapasite planlaması** |  Kapasite planlaması yapmanız ve ihtiyacınız olan tam üretimi sağlamanız gerekir. |    Sistem, kapasite planlama ve kapasite yönetimini otomatik olarak gerçekleştirir. |
| **Fiyatlandırma** | Saatte el ile sağlanan RU/s için, [saat başına standart (el ile) ru/s](https://azure.microsoft.com/pricing/details/cosmos-db/)ücreti üzerinden ödeme yaparsınız. | Sistem, en yüksek RU/sn için saatte bir saat içinde ödersiniz. <br/><br/> Tek bir yazma bölgesi hesabında, [her saat için otomatik ÖLÇEKLENDIRME ru/sn](https://azure.microsoft.com/pricing/details/cosmos-db/)'yi kullanarak saatlik olarak kullanılan ru/s için ödeme yaparsınız. <br/><br/>Birden çok yazma bölgesi olan hesaplar için otomatik ölçeklendirme için ek ücret alınmaz. [Saat başına aynı çok bölgeli yazma oranını](https://azure.microsoft.com/pricing/details/cosmos-db/)kullanarak saatlik olarak kullanılan aktarım hızı için ödeme yaparsınız. |
| **İş yükü türleri için en uygun** |  Öngörülebilir ve kararlı iş yükleri|   Tahmin edilemeyen ve değişken iş yükleri  |

## <a name="next-steps"></a>Sonraki adımlar

* [Otomatik ÖLÇEKLENDIRME SSS](autoscale-faq.md)'sini inceleyin.
* [El ile ve otomatik ölçeklendirme üretimi arasından seçim](how-to-choose-offer.md)yapmayı öğrenin.
* [Azure Cosmos veritabanı veya kapsayıcısı üzerinde otomatik ölçeklendirme işleme sağlamayı](how-to-provision-autoscale-throughput.md)öğrenin.
* Azure Cosmos DB bölümünde [bölümlendirme](partitioning-overview.md) hakkında daha fazla bilgi edinin.


