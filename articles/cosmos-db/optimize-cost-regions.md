---
title: Azure Cosmos DB 'de çok bölgeli dağıtımlar için maliyeti iyileştirin
description: Bu makalede Azure Cosmos DB içinde çok bölgeli dağıtımların maliyetlerinin nasıl yönetileceği açıklanmaktadır.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2020
ms.openlocfilehash: a559a51feafa310a4645282dc6368f520fc6b972
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96459617"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>Azure Cosmos DB'de birden çok bölgenin maliyetini iyileştirme
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos hesabınıza istediğiniz zaman bölge ekleyebilir veya kaldırabilirsiniz. Çeşitli Azure Cosmos veritabanları ve kapsayıcıları için yapılandırdığınız aktarım hızı, hesabınızla ilişkili her bölgede ayrılır. Saat başına sağlanan aktarım hızı, Azure Cosmos hesabınız için tüm veritabanları ve kapsayıcılar genelinde yapılandırılan RU/sn 'nin toplamı `T` ve veritabanı hesabınızla Ilişkili Azure bölgelerinin sayısı ise, `N` belirli bir saat Için Cosmos hesabınız için sağlanan toplam üretilen iş hacmi değerine eşittir `T x N RU/s` .

Tek yazma bölgesinde sağlanan aktarım hızı saniyede 100 RU ile saat başına 0,008 ABD doları, birden fazla yazılabilir bölgede sağlanan aktarım hızı saniyede 100 RU ile saat başına 0,016 ABD doları ile ücretlendirilir. Daha fazla bilgi için bkz. Azure Cosmos DB [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/cosmos-db/).

## <a name="costs-for-multiple-write-regions"></a>Birden çok yazma bölgesinin maliyeti

Çok bölgeli bir yazma sisteminde, yazma işlemleri için net kullanılabilir RUs, `N` `N` yazma bölgesi sayısı olan zamanları artırır. Tek bölge yazmalarından farklı olarak, her bölge artık yazılabilir ve çakışma çözümünü destekler. Maliyet Planlama noktasından, `M` dünyanın her yerindeki yazma işlemini gerçekleştirmek için `RUs` bir kapsayıcıya veya veritabanı düzeyine M sağlamanız gerekir. Daha sonra istediğiniz kadar bölge ekleyebilir ve bunları dünya genelindeki yazma işlemleri için yazma işlemleri için kullanabilirsiniz `M` .

### <a name="example"></a>Örnek

Batı ABD, bu ay 0,5 TB veri depolayan 10.000 RU/sn aktarım hızı ile sağlanan, tek bölgeli yazma işlemleri için yapılandırılmış bir Kapsayıcınız olduğunu düşünün. Aynı depolama ve aktarım hızı ile Doğu ABD bir bölge ekleyin ve uygulamanızdan her iki bölgede de kapsayıcılara yazmak istediğinizi varsayalım. Yeni toplam aylık faturanız (bir ayda 730 saat varsayılır) aşağıdaki gibi olacaktır:

|**Öğe**|**Kullanım (aylık)**|**Hız**|**Aylık maliyet**|
|----|----|----|----|
|Batı ABD (tek yazma bölgesi) kapsayıcısı için üretilen iş faturası |10.000 RU/s * 730 saat |$0,008/saat başına 100 RU/sn |$584 |
|2 bölgede kapsayıcı için üretilen iş faturası-Batı ABD & Doğu ABD (birden fazla yazma bölgesi) |2 * 10.000 RU/s * 730 saat |$0,016/saat başına 100 RU/sn |$2.336 |
|Batı ABD kapsayıcı için depolama faturası |0,5 TB (veya 512 GB) |$0,25/GB |$128 |
|2 bölgede kapsayıcı için depolama faturası-Batı ABD & Doğu ABD |2 * 0,5 TB (veya 1.024 GB) |$0,25/GB |$256 |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>Bölge bazında üretilen iş kullanımını geliştirme

Örneğin, bir veya daha fazla kullanılan okuma bölgesi olan bir veya daha fazla kullanım alanı için bir veya daha fazla okuma bölgesi kullanıyorsanız, oku-Region ' ı kullanarak bir veya daha fazla okuma bölgesinde değişiklik akışını kullanarak en fazla İlk olarak yazma bölgesinde sağlanan üretilen işi (ru) iyileştirdiğinizden emin olmanız gerekir. Çok büyük sorgular bile, hatta kullanımı zorda zor olabilir. Genel olarak, Bölgelerinizdeki tüketilen üretilen işi izleyin ve okuma ve yazma aktarım hızını ölçeklendirmek için isteğe bağlı bölge ekleyin veya kaldırın. böylece, aynı bölgede dağıtılan tüm uygulamalar için gecikme süresini anladığınızdan emin olun.

## <a name="next-steps"></a>Sonraki adımlar

Daha sonra, aşağıdaki makalelerle Azure Cosmos DB maliyet iyileştirmesi hakkında daha fazla bilgi edinebilirsiniz:

* [Geliştirme ve test Için iyileştirme](optimize-dev-test.md) hakkında daha fazla bilgi edinin
* [Azure Cosmos DB Faturanızı Anlama](understand-your-bill.md) hakkında daha fazla bilgi edinin
* [Verimlilik maliyetini iyileştirme](optimize-cost-throughput.md) hakkında daha fazla bilgi edinin
* [Depolama maliyetini iyileştirme](optimize-cost-storage.md) hakkında daha fazla bilgi edinin
* [Okuma ve yazma maliyetlerini iyileştirme](optimize-cost-reads-writes.md) hakkında daha fazla bilgi edinin
* [Sorguların maliyetini En Iyi duruma getirme](./optimize-cost-reads-writes.md) hakkında daha fazla bilgi edinin
