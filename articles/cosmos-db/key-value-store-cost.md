---
title: Anahtar değeri deposu olarak Azure Cosmos DB için istek birimi ücreti
description: Bir anahtar/değer deposu olarak kullanıldığında, okuma işlemleri ve basit yazma için istek birimi ücreti, Azure Cosmos DB hakkında bilgi edinin.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 3a79db11ff05bcc9d18619c7f508a9864c17c3b8
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012804"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Anahtar değer deposu olarak Azure Cosmos DB-maliyete genel bakış

Azure Cosmos DB, yüksek oranda kullanılabilir ve büyük ölçekli uygulamaları kolayca oluşturmak için bir Global olarak dağıtılmış çok modelli veritabanı hizmetidir. Varsayılan olarak, Azure Cosmos DB tüm verileri otomatik olarak ve etkin şekilde dizine alır. Bu, verilerde hızlı ve tutarlı [SQL](how-to-sql-query.md) (ve [JavaScript](stored-procedures-triggers-udfs.md)) sorguları sunar. 

Bu makalede, Azure Cosmos DB maliyetini basit yazmak için açıklar ve bir anahtar/değer deposu olarak kullanıldığında, okuma işlemleri. Yazma işlemleri, veri öğelerinin eklemeleri, yerini alır, siler ve üst öğeleri içerir. Tüm çok bölgeli hesaplar için% 99,999 kullanılabilirlik SLA 'Sı < garantisi sağlamanın yanı sıra, Azure Cosmos DB, okumalar için 10 ms gecikme süresi ve (Dizinli) yazma işlemleri için 99. yüzdebirlik değeri sağlar. 

## <a name="why-we-use-request-units-rus"></a>İstek birimi (RU) neden kullanıyoruz

Azure Cosmos DB performans, [Istek birimleri](request-units.md) (ru/s) ile ifade edilen sağlanan aktarım hızı miktarına bağlıdır. Sağlama ikinci bir ayrıntı düzeyine sahiptir ve RU/s 'de satın alınır ([saatlik faturalandırma ile karıştırılmamalıdır](https://azure.microsoft.com/pricing/details/cosmos-db/)). Ru, uygulama için gerekli aktarım hızı sağlamayı kolaylaştıran bir mantıksal soyutlama (bir para birimi) olarak düşünülmelidir. Kullanıcıların okuma ve yazma verimlilik arasında ayrım yapması gerekmez. Tek bir para birimi model RU, sağlanan kapasiteyi okuma ve yazma işlemleri arasında paylaşmak için verimliliği oluşturur. Bu sağlanan kapasite modeli **, hizmetin öngörülebilir ve tutarlı bir işleme, garantili düşük gecikme süresi ve yüksek kullanılabilirlik**sağlamasına olanak sağlar. Son olarak, her sağlanan RU, üretilen iş miktarını belirleyen bir miktar kaynak (örn. bellek, çekirdek/CPU ve ıOPS) miktarına de sahiptir.

Küresel olarak dağıtılmış bir veritabanı sistemi olarak, Cosmos DB gecikme süresi, aktarım hızı, tutarlılık ve yüksek kullanılabilirliği kapsayan kapsamlı SLA 'Lar sağlayan tek Azure hizmetidir. Sağladığınız üretilen iş, Cosmos hesabınızla ilişkilendirilen her bölgeye uygulanır. Okumalar, Cosmos DB, birden çok, iyi tanımlanmış sağlar [tutarlılık düzeyleri](consistency-levels.md) aralarından seçim yapabileceğiniz. 

Aşağıdaki tabloda, 1 KB ve 100 KBs boyutundaki bir veri öğesine göre okuma ve yazma işlemleri gerçekleştirmek için gereken ru sayısı gösterilmektedir.

|Öğe boyutu|1 okuma|1 yazma|
|-------------|------|-------|
|1 KB|1 RU|5 ru|
|100 KB|10 RU|50 ru|

## <a name="cost-of-reads-and-writes"></a>Okuma ve yazma işlemleri maliyeti

1\.000 RU/s sağlarsanız, bu miktar 3.600.000 RU/saat olarak değişir ve saat (ABD ve Avrupa) için maliyet $0,08. 1 KB boyutlu bir veri öğesi için bu, sağlanan aktarım hızını kullanarak 3.600.000 okuma veya 720.000 yazma (3.600.000 RU/5) kullanabileceğiniz anlamına gelir. Milyon okuma ve yazma işlemleri için, maliyet $0,022/milyon okuma ($0,08/3,6) ve $0.111/milyon yazma ($0,08/0,72) olacaktır. Başına maliyet milyon aşağıdaki tabloda gösterildiği gibi en az olur.

|Öğe boyutu|1\.000.000 okuma maliyeti|1\.000.000 yazma maliyeti|
|-------------|-------|--------|
|1 KB|$0.022|$0.111|
|100 KB|$0.222|$1.111|


En temel blob veya okuma işlemi ve yazma milyon işlem başına 5 milyon başına nesne depoları hizmetler ücreti $0.40. En iyi şekilde kullanılırsa Cosmos DB, bu diğer çözümlerden (1 KB işlem için)% 98 'e kadar olabilir.

## <a name="next-steps"></a>Sonraki adımlar

* İş yükleriniz için üretilen işi tahmin etmek üzere [ru Hesaplayıcı](https://cosmos.azure.com/capacitycalculator/) kullanın.

