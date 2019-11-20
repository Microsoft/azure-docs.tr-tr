---
title: Azure Cosmos DB Faturanızı Anlama
description: Bu makalede, Azure Cosmos DB faturanızı bazı örneklerle nasıl anlayabileceğiniz açıklanır.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.reviewer: sngun
ms.openlocfilehash: 6d2edb7674a82a0388a0e028bee1b222e0e55004
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754719"
---
# <a name="understand-your-azure-cosmos-db-bill"></a>Azure Cosmos DB faturanızı anlayın

Tamamen yönetilen bir bulutta yerel veritabanı hizmeti olarak, Azure Cosmos DB yalnızca sağlanan aktarım hızı ve tüketilen depolama alanı için ücretlendirme yaparak faturalandırmayı basitleştirir. Şirket içi veya IaaS ile barındırılan diğer yöntemler ile karşılaştırıldığında ek lisans ücretleri, donanım, yardımcı program maliyetleri veya tesis maliyetleri yoktur. Azure Cosmos DB çoklu bölge yeteneklerini dikkate aldığınızda, veritabanı hizmeti mevcut şirket içi veya IaaS çözümleriyle karşılaştırıldığında maliyetlerde önemli bir düşüş sağlar.

Azure Cosmos DB, sağlanan verimlilik ve tüketilen depolama alanı temelinde saatlik olarak faturalandırılırsınız. Sağlanan aktarım hızı için, faturalandırma birimi saat başına 100 RU/sn, standart genel fiyatlandırmayı kabul eden bir saatte $0,008 ile ücretlendirilir, [fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/cosmos-db/)bakın. Tüketilen depolama alanı için aylık $0,25 GB depolama alanı üzerinden faturalandırılırsınız, [fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/cosmos-db/)bakın. 

Bu makalede, aylık faturada gördüğünüz ayrıntıları anlamanıza yardımcı olacak bazı örnekler kullanılmaktadır. Azure Cosmos kapsayıcılarınız, birden fazla bölgeye yayılacaklarsa veya bir ayda bir dönem için farklı şekilde çalıştırıldığında sağlanan farklı aktarım hızına sahip olduğunda örneklerde gösterilen sayılar farklı olabilir.

>! Not: Faturalandırma, 60 dakikalık bir süre değil, bir duvar saati saatinin herhangi bir bölümü için tasarlanmıştır.

## <a name="billing-examples"></a>Faturalandırma örnekleri

### <a name="billing-example---throughput-on-a-container-full-month"></a>Faturalandırma örneği-bir kapsayıcıda işleme (tam ay)

* Bir kapsayıcıda 1.000 RU/sn aktarım hızı kullandığınızı ve bu süre için 24 saat * 30 720 gün boyunca mevcut olduğunu varsayalım.  

* 1\.000 RU/sn, kapsayıcıların mevcut olduğu her saat için saat başına 100 RU/sn 'dir (yani, 1000/100 = 10). 

* Saat başına 10 birimi, $0,008 maliyeti (saat başına 100 RU/sn başına $0,08) ile çarpar. 

* Saat başına $0,08, ay içindeki saat sayısı $0,08 * 24 saat * 30 gün = $57,60.  

* Toplam aylık faturada 7.200 birim (/100 ru) gösterilecektir ve bu maliyet, $57,60.

### <a name="billing-example---throughput-on-a-container-partial-month"></a>Faturalandırma örneği-bir kapsayıcıda aktarım hızı (kısmi ay)

* Sağlanan 2.500 RU/sn için üretilen iş hızına sahip bir kapsayıcı oluşturduğumuz varsayın. Kapsayıcı ayda 24 saat sürer (örneğin, oluşturduktan sonra 24 saat sonra silinir).  

* Ardından, faturada 600 birim (2.500 RU/sn/100 RU/sn/Unit * 24 saat) göreceksiniz. Maliyet $4,80 (600 birim * $0.008/Unit) olacaktır.

* Aylık toplam fatura $4,80 olacaktır.

### <a name="billing-rate-if-storage-size-changes"></a>Depolama boyutu değişirse faturalandırma oranı

Depolama kapasitesi, bir aylık bir dönemde depolanan saatlik en fazla veri miktarı birimi (GB cinsinden) üzerinden faturalandırılır. Örneğin; ayın ilk yarısında 100 GB’lık, ikinci yarısında ise 50 GB’lık depolama kullandıysanız, bu ay için 75 GB’a eşdeğer depolama tutarı üzerinden faturalandırılırsınız.

### <a name="billing-rate-when-container-or-a-set-of-containers-are-active-for-less-than-an-hour"></a>Kapsayıcının veya kapsayıcı kümesinin bir saatten daha az etkin olduğu faturalandırma oranı

Kapsayıcının veya veritabanının mevcut olduğu her saat için, kullanım veya kapsayıcının veya veritabanının bir saatten daha az etkin olduğu durumlarda, her saat için sabit bir fiyat üzerinden faturalandırılırsınız. Örneğin, bir kapsayıcı veya veritabanı oluşturup 5 dakika sonra silerseniz faturanızda bir saat yer alır.

### <a name="billing-rate-when-throughput-on-a-container-or-database-scales-updown"></a>Bir kapsayıcı veya veritabanı üzerinde üretilen iş miktarı yukarı/aşağı ölçeklenirken faturalandırma oranı

9:30:400 RU/sn 'den 1.000 RU/sn 'ye kadar olan üretilen iş verimini artırdıysanız ve daha sonra 10:45 tarihinde 400 ru/sn 'ye geri dönmek istiyorsanız, iki saatlik 1.000 RU/sn üzerinden ücretlendirilirsiniz. 

Bir kapsayıcı için sağlanan aktarım hızını veya 9:30:100-K RU/sn ile 200-K RU/sn arası bir kapsayıcı kümesini artırırsanız, daha sonra 10:45 $-K RU/sn ' ye kadar ücret100 lendirilirsiniz.

### <a name="billing-example-multiple-containers-each-with-dedicated-provisioned-throughput-mode"></a>Faturalandırma örneği: her biri adanmış sağlanan verimlilik moduna sahip birden çok kapsayıcı

* Doğu ABD 2 içinde bir Azure Cosmos hesabını, sağlanan 500 RU/sn ve 700 RU/sn hızında üretilen iş hızına sahip iki kapsayıcı ile birlikte oluşturursanız, toplam 1.200 sağlanan% RU/sn hızına sahip olursunuz.  

* Ücretlendirilirsiniz/100 * $0,008 = $0.096/saat. 

* İşleme gereksinimleriniz değiştiyse ve her kapsayıcının kapasitesini 500 RU/sn ile artırdıysanız, ayrıca 20.000 RU/sn ile yeni bir sınırsız kapsayıcı oluştururken, genel olarak sağlanan kapasiteniz 22.200 RU/sn (1.000 RU/sn + 1.200 RU/sn + 20, 000RU/sn) olacaktır.  

* Faturanız daha sonra şu şekilde değişir: $0,008 x 222 = $1.776/Hour. 

* 720 saatlik bir ayda (24 saat * 30 gün), 500 saat için sağlanan aktarım hızı 1.200 RU/sn ise ve kalan 220 saat için sağlanan aktarım hızı 22.200 RU/sn ise aylık faturanız şu şekilde görünür: 500 x $0.096/saat + 220 x $1.776/saat = $438.72/ay.

![Adanmış üretilen iş faturası örneği](./media/understand-your-bill/bill-example1.png)

### <a name="billing-example-containers-with-shared-throughput-mode"></a>Faturalandırma örneği: paylaşılan işleme modundaki kapsayıcılar

* İki Azure Cosmos veritabanı ile Doğu ABD 2 içinde bir Azure Cosmos hesabı oluşturursanız (veritabanı düzeyinde üretilen işi paylaşan bir kapsayıcı kümesi ile), sırasıyla 50-K RU/sn ve 70-K RU/sn için sağlanan verimlilik ile, sağlanan toplam 120 K RU/sn aktarım hızı.  

* 1200 x $0,008 = $9.60/saat ücretlendirilirsiniz. 

* İşleme Gereksinimleriniz değişirse ve her bir veritabanı için her bir veritabanının sağlanan aktarım hızını 10.000 RU/sn olarak artırdıysanız ve paylaşılan aktarım hızı veritabanınıza 15-K RU/sn adanmış aktarım hızı moduna sahip ilk veritabanına yeni bir kapsayıcı eklerseniz, Toplam sağlanan kapasite 155-K RU/sn (60 K ru/sn + 80 K RU/sn + 15 K RU/sn) olacaktır.  

* Faturanız daha sonra şu şekilde değişir: 1.550 * $0,008 = $12.40/Hour.  

* 720 saatlik bir ayda saat 300 için sağlanan aktarım hızı 120-K RU/sn ve kalan 420 saat için sağlanan aktarım hızı 155-K RU/sn ise aylık faturanız şu şekilde görünür: 300 x $9.60/saat + 420 x $12.40/saat = $2.880 + $5.208 = $8088/ay. 

![Paylaşılan verimlilik faturası örneği](./media/understand-your-bill/bill-example2.png)

## <a name="billing-examples-with-geo-replication-and-multi-master"></a>Coğrafi çoğaltma ve çoklu yönetici ile faturalandırma örnekleri  

Dünyanın her yerindeki Azure bölgelerini Azure Cosmos veritabanı hesabınıza dilediğiniz zaman ekleyebilir/kaldırabilirsiniz. Çeşitli Azure Cosmos veritabanları ve kapsayıcıları için yapılandırdığınız aktarım hızı, Azure Cosmos veritabanı hesabınızla ilişkili Azure bölgelerinin her birine ayrılır. Azure Cosmos veritabanı hesabınızdaki (saat başına sağlanan) tüm veritabanları ve kapsayıcılar üzerinde yapılandırılan üretilen iş hacmi (RU/sn) toplamı T ise ve veritabanı hesabınızla ilişkili Azure bölgelerinin sayısı N ise toplam tek bir yazma bölgesiyle yapılandırılan (a) Azure Cosmos veritabanı hesabınız için sağlanan aktarım hızı T x N RU/sn 'ye eşittir ve (b) yazma işlemi yapabilen tüm bölgeler T x 'e eşit (N + 1) RU/sn 'ye eşittir anı. Sağlanan aktarım hızı (tek yazma bölgesi) maliyetleri $0.008/saat başına 100 RU/sn ve birden çok yazılabilir bölge (çok yöneticili yapılandırma) maliyetleriyle sağlanan aktarım hızı, 100 RU/sn başına (bkz. [fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/cosmos-db/)bakın). Tek bir yazma bölgesinin veya birden fazla yazma bölgesinin Azure Cosmos DB, herhangi bir bölgeden veri okumanızı sağlar.

### <a name="billing-example-multi-region-azure-cosmos-account-single-region-writes"></a>Faturalandırma örneği: çok bölgeli Azure Cosmos hesabı, tek bölgede yazma işlemleri

Batı ABD ' de bir Azure Cosmos Kapsayıcınız olduğunu varsayalım. Kapsayıcı 10.000 RU/sn aktarım hızı ile oluşturulur ve bu ay 1 TB veri depoladığınızda. Azure Cosmos hesabınıza her biri aynı depolama ve aktarım hızı ile üç bölge (Doğu ABD, Kuzey Avrupa ve Doğu Asya) eklediğinizi varsayalım. Toplam aylık faturanız olacaktır (bir ayda 30 gün varsayılır). Faturanız aşağıdaki gibi olacaktır: 

|**Öğe** |**Kullanım (ay)** |**Derecelendir** |**Aylık maliyet** |
|---------|---------|---------|-------|
|Batı ABD’deki kapsayıcı için aktarım hızı faturası      | 10.000 RU/sn * 24 * 30    |$0,008/saat başına 100 RU/sn   |$576|
|3 ek bölge (Doğu ABD, Kuzey Avrupa ve Doğu Asya) için aktarım hızı faturası       | 3 * 10.000 RU/sn * 24 * 30    |$0,008/saat başına 100 RU/sn  |$1.728|
|Batı ABD’deki kapsayıcı için depolama faturası      | 250 GB    |$0,25/GB  |$62,50|
|3 ek bölge (Doğu ABD, Kuzey Avrupa ve Doğu Asya) için depolama faturası      | 3 * 250 GB    |$0,25/GB  |$187,50|
|**Toplamda**     |     |  |**$2.554**|

*Ayrıca, verileri Doğu ABD, Kuzey Avrupa ve Doğu Asya çoğaltmak için Batı ABD kapsayıcıdaki her ay 100 GB veri çıkışı olduğunu da varsayalım. Veri aktarımı ücretleri itibariyle çıkış için faturalandırılırsınız.*

### <a name="billing-example-multi-region-azure-cosmos-account-multi-region-writes"></a>Faturalandırma örneği: çok bölgeli Azure Cosmos hesabı, çok bölgeli yazma işlemleri

Batı ABD içinde bir Azure Cosmos kapsayıcısı oluşturduğunuzu varsayalım. Kapsayıcı 10.000 RU/sn aktarım hızı ile oluşturulur ve bu ay 1 TB veri depoladığınızda. Her biri aynı depolama ve aktarım hızı ve Azure Cosmos hesabınızla ilişkili tüm bölgelerdeki kapsayıcılara yazmak istediğiniz üç bölge (Doğu ABD, Kuzey Avrupa ve Doğu Asya) eklediğinizi varsayalım. Toplam aylık faturanız şu şekilde olacaktır: (bir ayda 30 gün varsayılır):

|**Öğe** |**Kullanım (ay)**|**Derecelendir** |**Aylık maliyet** |
|---------|---------|---------|-------|
|Batı ABD kapsayıcı için üretilen iş faturası (tüm bölgeler yazılabilir)       | 10.000 RU/sn * 24 * 30    |$0,016/saat başına 100 RU/sn    |$1.152 |
|3 ek bölge için üretilen iş faturası Doğu ABD, Kuzey Avrupa ve Doğu Asya (tüm bölgeler yazılabilir)        | (3 + 1) * 10.000 RU/sn * 24 * 30    |$0,016/saat başına 100 RU/sn   |$4.608 |
|Batı ABD’deki kapsayıcı için depolama faturası      | 250 GB    |$0,25/GB  |$62,50|
|3 ek bölge (Doğu ABD, Kuzey Avrupa ve Doğu Asya) için depolama faturası      | 3 * 250 GB    |$0,25/GB  |$187,50|
|**Toplamda**     |     |  |**$6.010**|

*Ayrıca, verileri Doğu ABD, Kuzey Avrupa ve Doğu Asya çoğaltmak için Batı ABD kapsayıcıdaki her ay 100 GB veri çıkışı olduğunu da varsayalım. Veri aktarımı ücretleri itibariyle çıkış için faturalandırılırsınız.*

### <a name="billing-example-azure-cosmos-account-with-multi-master-database-level-throughput-including-dedicated-throughput-mode-for-some-containers"></a>Faturalandırma örneği: bazı kapsayıcılar için adanmış aktarım hızı modu dahil olmak üzere çok yöneticili, veritabanı düzeyinde aktarım hızı olan Azure Cosmos hesabı

Tüm bölgelerin yazılabilir olduğu çok bölgeli bir Azure Cosmos hesabımız olan aşağıdaki örneği ele alalım (çok yöneticili yapılandırma). Basitlik sağlamak için, depolama boyutunun sabit kalır ve bu örneği daha basit tutmak için burada değişmez ve yok sayacağız. Ay boyunca sağlanan aktarım hızı aşağıdaki gibi farklılık gösterir (30 gün veya 720 saat varsayılır): 

[0-100 saat]:  

* Tüm bölgelerin yazılabilir olduğu üç bölge Azure Cosmos hesabı (Batı ABD, Doğu ABD, Kuzey Avrupa) oluşturduk 

* Paylaşılan verimlilik 10.000 RU/sn ile bir veritabanı (D1) oluşturduk 

* Paylaşılan işleme 30-K RU/sn ile bir veritabanı (D2) oluşturdunuz ve  

* Adanmış aktarım hızı 20 K RU/sn ile bir kapsayıcı (C1) oluşturduk 

[101-200 saat]:  

* Veritabanını (D1) 50 K RU/sn 'ye ölçeklendirdik 

* Veritabanını (D2) 70 K RU/sn 'ye ölçeklendirdik  

* Kapsayıcıyı sildik (C1)  

[201-300 saat]:  

* Özel işleme 20 K RU/sn ile kapsayıcı (C1) oluşturdunuz 

[301-400 saat]:  

* Azure Cosmos hesabından bölgelerden birini kaldırdık (yazılabilir bölge sayısı artık 2 ' dir) 

* Veritabanını (D1) 10.000 RU/sn 'ye ölçeklendirdik 

* Veritabanını (D2) 80 K RU/sn 'ye ölçeklendirdik  

* Kapsayıcıyı (C1) yeniden sildiniz 

[401-500 saat]:  

* Veritabanını (D2) 10.000 RU/sn 'ye ölçeklendirdik  

* Özel işleme 20 K RU/sn ile kapsayıcı (C1) oluşturdunuz 

[501-700 saat]:  

* Veritabanını (D1) 20 K RU/sn 'ye ölçeklendirdik  

* Veritabanını (D2) 100 K RU/sn 'ye ölçeklendirdik  

* Kapsayıcıyı (C1) yeniden sildiniz  

[701-720 saat]:  

* Veritabanı (D2) ile 50 K RU/sn arasında ölçeklendirildi  

Ayda 720 saat boyunca toplam sağlanan aktarım hızı içindeki değişiklikler aşağıdaki şekilde gösterilmiştir: 

![Gerçek yaşam örneği](./media/understand-your-bill/bill-example3.png)

Toplam aylık fatura şöyle olacaktır (bir ayda 30 gün/720 saat varsayılır), aşağıdaki şekilde hesaplanır:

|**Saatlerinin**  |**RU/s** |**Öğe** |**Kullanım (saatlik)** |**Maliyet** |
|---------|---------|---------|-------|-------|
|[0-100] |D1:10.000 <br/>D2:30K <br/>C1:20K |Batı ABD kapsayıcı için üretilen iş faturası (tüm bölgeler yazılabilir)  | `D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 30 K RU/sec/100 * $0.016 * 100 hours = $480` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$960  |
| | |2 ek bölge için üretilen iş faturası: Doğu ABD, Kuzey Avrupa (tüm bölgeler yazılabilir)  |`(2 + 1) * (60 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |$2.880  |
|[101-200] |D1:50K <br/>D2:70K <br/>C1:-- |Batı ABD kapsayıcı için üretilen iş faturası (tüm bölgeler yazılabilir)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` |$1920  |
| | |2 ek bölge için üretilen iş faturası: Doğu ABD, Kuzey Avrupa (tüm bölgeler yazılabilir)  |`(2 + 1) * (120 K RU/sec /100 * $0.016) * 100 hours = $5,760`  |$5.760  |
|[201-300]  |D1:50K <br/>D2:70K <br/>C1:20K |Batı ABD kapsayıcı için üretilen iş faturası (tüm bölgeler yazılabilir)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$2.240  |
| | |2 ek bölge için üretilen iş faturası: Doğu ABD, Kuzey Avrupa (tüm bölgeler yazılabilir)  |`(2 + 1) * (140 K RU/sec /100 * $0.016-) * 100 hours = $6,720` |$6.720 |
|[301-400] |D1:10.000 <br/>D2:80K <br/>C1:-- |Batı ABD kapsayıcı için üretilen iş faturası (tüm bölgeler yazılabilir)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 80 K RU/sec/100 * $0.016 * 100 hours = $1,280`  |$1.440   |
| | |2 ek bölge için üretilen iş faturası: Doğu ABD, Kuzey Avrupa (tüm bölgeler yazılabilir)  |`(1 + 1) * (90 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |$2.880  |
|[401-500] |D1:10.000 <br>D2:10.000 <br>C1:20K |Batı ABD kapsayıcı için üretilen iş faturası (tüm bölgeler yazılabilir)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`D2: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$640  |
| | |2 ek bölge için üretilen iş faturası: Doğu ABD, Kuzey Avrupa (tüm bölgeler yazılabilir)  |`(1 + 1) * (40 K RU/sec /100 * $0.016) * 100 hours = $1,280`  |$1.280  |
|[501-700] |D1:20K <br>D2:100K <br>C1:-- |Batı ABD kapsayıcı için üretilen iş faturası (tüm bölgeler yazılabilir)  |`D1: 20 K RU/sec/100 * $0.016 * 200 hours = $640` <br>`D2: 100 K RU/sec/100 * $0.016 * 200 hours = $3,200` |$3.840  |
| | |2 ek bölge için üretilen iş faturası: Doğu ABD, Kuzey Avrupa (tüm bölgeler yazılabilir)  |`(1 + 1) * (120 K RU/sec /100 * $0.016) * 200 hours = $1,280`  |$7.680  |
|[701-720] |D1:20K <br/>D2:50K <br/>C1:-- |Batı ABD kapsayıcı için üretilen iş faturası (tüm bölgeler yazılabilir)  |`D1: 20 K RU/sec/100 *$0.016 * 20 hours = $64` <br/>`D2: 50 K RU/sec/100 *$0.016 * 20 hours = $160` |$224  |
| | |2 ek bölge için üretilen iş faturası: Doğu ABD, Kuzey Avrupa (tüm bölgeler yazılabilir)  |`(1 + 1) * (70 K RU/sec /100 * $0.016) * 20 hours = $448`  |$224  |
|| |**Aylık toplam maliyet**  | |**$38.688**   |

## <a name="proactively-estimating-your-monthly-bill"></a>Aylık faturanızı önceden tahmin etme  

Daha sonra faturanızı ayın sonundan önce önceden tahmin etmek istediğiniz başka bir örneği ele alalım. Faturanızı şu şekilde tahmin edebilirsiniz:

|**Depolama maliyeti** | |
|----|----|
|Ort kayıt boyutu (KB) |1 |
|Kayıt sayısı  |100.000.000  |
|Toplam depolama alanı (GB)  |100 |
|GB başına aylık maliyet  |$0,25  |
|Depolama için beklenen aylık maliyet   |$25,00  |

<br>

|**Verimlilik maliyeti** | | | |
|----|----|----|----|
|İşlem Türü| İstek/sn| Ort. RU/istek| RUs gerekli|
|Yazma| 100 | 5 | 500|
|Okuma| 400| 1| 400|

Toplam RU/sn: 500 + 400 = 900 saatlik maliyet: 900/100 * $0,008 = $0,072 Işleme için beklenen aylık maliyet (31 gün varsayılıyor): $0,072 * 24 * 31 = $53,57

**Aylık toplam maliyet**

Aylık toplam maliyet = depolama için aylık maliyet ve toplam verimlilik için aylık maliyet = $25,00 + $53,57 = $78,57

*Fiyatlandırma bölgeye göre farklılık gösterebilir. Güncel fiyatlandırma için bkz. [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/cosmos-db/).*

## <a name="billing-with-azure-cosmos-db-reserved-capacity"></a>Azure Cosmos DB ayrılmış kapasite ile faturalama

Azure Cosmos DB ayrılmış kapasite, tüm Azure bölgelerindeki tüm Azure Cosmos veritabanlarına ve kapsayıcılara (API veya veri modeli için) uygulanabilen, sağlanan aktarım hızını önceden (bir ayrılmış kapasite veya bir ayırma) satın almanıza olanak sağlar. Sağlanan aktarım hızı fiyatı bölge başına değiştiğinden, ayrılmış kapasiteyi indirimle satın aldığınız Parasal kredi olarak düşünmenize yardımcı olur. Bu, her bölgedeki ilgili fiyata sağlanan aktarım hızı için üzerinden çizilebilirler. Örneğin, 50-K RU/sn ile sağlanan tek bir kapsayıcıya sahip bir Azure Cosmos hesabınız olduğunu ve küresel olarak çoğaltılan iki bölgeyi Doğu ABD ve Japonya Doğu. Kullandıkça Öde seçeneğini belirlerseniz, ödeme yaparsınız:  

* Doğu ABD: 50-K RU/sn için, bu bölgedeki her 100 RU/sn hızında $0,008 

* Japonya Doğu: 50-K RU/sn için, bu bölgedeki her 100 RU/sn hızında $0,009

Toplam faturanız (ayrılmış kapasite olmadan) olacaktır (30 gün veya 720 saat varsayılır): 

|**Bölge**| **100 RU/sn başına saatlik fiyat**|**Birimler (RU/s)**|**Faturalandırılan tutar (saatlik)**| **Faturalandırılan tutar (aylık)**|
|----|----|----|----|----|
|Doğu ABD|$0,008 |50 K|$4|$2.880 |
|Doğu Japonya|$0,009 |50 K| $4,50 |$3.240 |
|Toplam|||$8,50|$6.120 |

Bunun yerine ayrılmış kapasite satın aldığınızı göz önünde bulundurun. 100-K RU/sn için ayrılan kapasiteyi bir yıl (%20 indirimli) veya saat başına $6,40 olan $56.064 oranında satın alabilirsiniz. Bkz. [fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/cosmos-db/)ayrılmış kapasite fiyatlandırması.  

* Verimlilik maliyeti (Kullandıkça öde): 100.000 RU/sn/100 * $0.008/saat * 8760 saat, yıl içinde = $70.080 

* Verimlilik maliyeti (ayrılmış kapasiteye sahip) $70.080 indirimli ve %20 = $56.064 

Kullanım dışı olarak satın almış olduğunuz miktar, $8 KB/sn 100 için saat başına, saat başına $6,40 fiyat üzerinden Doğu ABD. Daha sonra, aboneliğiniz için ayarlanan bölgesel liste fiyatlarındaki herhangi bir genel Azure bölgesindeki sağlanan aktarım hızı kapasitesi için saatlik olarak bu ön ödemeli üretilen iş hacmi üzerinden aşağı doğru çizebilirsiniz. Bu örnekte, Doğu ABD her biri için 50 K RU/sn sağladığınızda ve Japonya Doğu, saat başına sağlanan aktarım hızı $8,00 ' i çizecek ve saat başına $0,50 yaş (veya $360/ay) üzerinden faturalandırılırsınız. 

|**Bölge**| **100 RU/sn başına saatlik fiyat**|**Birimler (RU/s)**| **Faturalandırılan tutar (saatlik)**| **Faturalandırılan tutar (aylık)**|
|----|----|----|----|----|
|Doğu ABD|$0,008 |50 K|$4|$2.880 |
|Doğu Japonya|$0,009 |50 K| $4,50 |$3.240 |
|||Kullandıkça öde|$8,50|$6120|
|Satın Alınan Ayrılmış Kapasite|$0,0064 (%20 indirim) |100 RU/sn veya $8 kapasite önceden satın alındı |-$8|-$5.760 |
|Net Fatura|||$0,50 |$360 |

## <a name="next-steps"></a>Sonraki Adımlar

Ardından, aşağıdaki makalelerle Azure Cosmos DB maliyet iyileştirmesi hakkında daha fazla bilgi edinebilirsiniz:

* [Fiyatlandırma modelinin müşteriler için uygun maliyetli Cosmos DB](total-cost-ownership.md) hakkında daha fazla bilgi edinin
* [Geliştirme ve test Için iyileştirme](optimize-dev-test.md) hakkında daha fazla bilgi edinin
* [Verimlilik maliyetini iyileştirme](optimize-cost-throughput.md) hakkında daha fazla bilgi edinin
* [Depolama maliyetini iyileştirme](optimize-cost-storage.md) hakkında daha fazla bilgi edinin
* [Okuma ve yazma maliyetlerini iyileştirme](optimize-cost-reads-writes.md) hakkında daha fazla bilgi edinin
* [Sorguların maliyetini En Iyi duruma getirme](optimize-cost-queries.md) hakkında daha fazla bilgi edinin
* [Çok bölgeli Azure Cosmos hesaplarının maliyetini En Iyi duruma getirme](optimize-cost-regions.md) hakkında daha fazla bilgi edinin
