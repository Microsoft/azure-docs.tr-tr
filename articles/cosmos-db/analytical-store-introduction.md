---
title: Azure Cosmos DB analiz deposu nedir?
description: Azure Cosmos DB işlemsel (satır tabanlı) ve analitik (sütun tabanlı) depolama hakkında bilgi edinin. Analitik deponun avantajları, büyük ölçekli iş yükleri için performans etkisi ve verileri işlemsel depodan analitik depoya otomatik olarak eşitleme
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: rosouz
ms.custom: seo-nov-2020
ms.openlocfilehash: eaabc663ba243423bddf7ef6abfe41182e06b4f9
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364619"
---
# <a name="what-is-azure-cosmos-db-analytical-store"></a>Analitik depo Azure Cosmos DB nedir?
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Analitik depo Azure Cosmos DB, işlemsel iş yüklerinize herhangi bir etkisi olmadan, Azure Cosmos DB işletimsel verilere karşı büyük ölçekli analizler sağlamak için tam olarak yalıtılmış bir sütun deposudur. 

Azure Cosmos DB işlemsel depolama şeması belirsiz ve şema veya dizin yönetimiyle uğraşmak zorunda kalmadan işlem uygulamalarınızda yineleme yapmanızı sağlar. Bunun aksine, analitik depolamanın, analitik sorgu performansını iyileştirmek için şema Azure Cosmos DB. Bu makalede analitik depolama hakkında ayrıntılı bilgi açıklanmaktadır.

## <a name="challenges-with-large-scale-analytics-on-operational-data"></a>İşletimsel verilerde büyük ölçekli analiz sorunları

Azure Cosmos DB kapsayıcısındaki çok modelli işletimsel veriler, dahili olarak sıralı satır tabanlı "işlem deposunda" depolanır. Satır deposu biçimi, milisaniye olarak yanıt süreleriyle ve işletimsel sorgularda hızlı işlem okuma ve yazma işlemleri için tasarlanmıştır. Veri kümeniz büyük büyürse, karmaşık analitik sorgular bu biçimde depolanan veriler üzerinde sağlanan aktarım hızı açısından pahalı olabilir. Sağlanan aktarım hızının yüksek kullanımı, gerçek zamanlı Uygulamalarınız ve hizmetleriniz tarafından kullanılan işlem iş yüklerinin performansını etkiler.

Geleneksel olarak, büyük miktarlarda verileri analiz etmek için, işletimsel veriler Azure Cosmos DB işlem deposundan ayıklanır ve ayrı bir veri katmanında depolanır. Örneğin, veriler bir veri ambarında veya Veri Gölü uygun bir biçimde depolanır. Bu veriler daha sonra büyük ölçekli çözümlemeler için kullanılır ve Apache Spark kümeleri gibi işlem altyapısı kullanılarak çözümlenir. İşlem iş yüklerinizde olası etkiyi en aza indirmek için ETL (Ayıkla, Dönüştür, Yükle) işlem hatları daha az sıklıkta çalıştırıldığından, analitik depolama ve işlem katmanlarının işlemsel verilerden ayrılması ek gecikme süresine neden olur.

ETL ardışık düzenleri, işletimsel verilere yapılan güncelleştirmeleri işlerken yalnızca yeni alınan işletimsel verileri işlemeye kıyasla de karmaşık hale gelir. 

## <a name="column-oriented-analytical-store"></a>Sütun odaklı analitik depo

Azure Cosmos DB analitik depo, geleneksel ETL işlem hatları ile oluşan karmaşıklık ve gecikme süreli zorluklara yöneliktir. Azure Cosmos DB analitik depo, işletimsel verilerinizi otomatik olarak ayrı bir sütun deposuna eşitleyebilir. Sütun deposu biçimi, büyük ölçekli analitik sorguların en iyi duruma getirilmiş bir şekilde gerçekleştirilmesi için uygundur, bu da bu sorguların gecikme süresini artırır.

Azure SYNAPSE bağlantısı 'nı kullanarak artık Azure SYNAPSE Analytics 'ten Azure Cosmos DB analitik depoya doğrudan bağlayarak hiçbir ETL HTAP çözümü oluşturabilirsiniz. İşletimsel verilerinizde neredeyse gerçek zamanlı büyük ölçekli analiz çalıştırmanızı sağlar.

## <a name="features-of-analytical-store"></a>Analitik depo özellikleri 

Azure Cosmos DB kapsayıcısında analitik depoyu etkinleştirdiğinizde, yeni bir sütun deposu, kapsayıcıdaki işletimsel verilere göre dahili olarak oluşturulur. Bu sütun deposu, bu kapsayıcı için satır odaklı işlem deposundan ayrı olarak kalıcı hale getirilir. İşletimsel verilerinize ekler, güncelleştirmeler ve silmeler otomatik olarak analitik depola eşitlenir. Verileri eşitlemek için değişiklik akışına veya ETL 'ye ihtiyacınız yoktur.

### <a name="column-store-for-analytical-workloads-on-operational-data"></a>İşletimsel verilerdeki analitik iş yükleri için sütun deposu

Analitik iş yükleri genellikle Seçili alanların toplamaları ve sıralı taramalarını içerir. Verileri sütun birincil sırada depolayarak, analitik mağaza her alan için bir değer grubunun birlikte serileştirilmesine olanak tanır. Bu biçim, belirli alanlar üzerinde istatistikleri taramak veya hesaplamak için gereken ıOPS 'yi azaltır. Büyük veri kümeleri üzerindeki taramaların sorgu yanıt sürelerini önemli ölçüde geliştirir. 

Örneğin, işletimsel tablolarınız aşağıdaki biçimdedir:

:::image type="content" source="./media/analytical-store-introduction/sample-operational-data-table.png" alt-text="Örnek işletimsel tablo" border="false":::

Satır deposu, yukarıdaki verileri diskte satır başına serileştirilmiş biçimde devam ettirir. Bu biçim, "Product1 hakkında bilgi Döndür" gibi daha hızlı işlem okuma, yazma ve işlem sorgularının kullanılmasına olanak tanır. Ancak, veri kümesi büyük olarak büyüdükçe ve karmaşık analitik sorguları verilerde çalıştırmak istiyorsanız, pahalı olabilir. Örneğin, farklı iş birimleri ve aylar arasında ' ekipman ' adlı kategori altında bir ürüne ilişkin satış eğilimleri almak istiyorsanız, karmaşık bir sorgu çalıştırmanız gerekir. Bu veri kümesindeki büyük taramalar, sağlanan aktarım hızı açısından pahalı olabilir ve ayrıca gerçek zamanlı uygulamalarınızı ve hizmetlerinizi destekleyen işlem iş yüklerinin performansını etkileyebilir.

Sütun deposu olan analitik depo, benzer veri alanlarını birlikte serileştirdiğinden ve disk ıOPS 'yi azalttığından bu sorgular için daha uygundur.

Aşağıdaki görüntüde işlem satır deposu ve analitik sütun deposu Azure Cosmos DB gösterilmektedir:

:::image type="content" source="./media/analytical-store-introduction/transactional-analytical-data-stores.png" alt-text="Azure Cosmos DB 'de işlem satır deposu vs analitik sütun deposu" border="false":::

### <a name="decoupled-performance-for-analytical-workloads"></a>Analitik iş yükleri için ayrılmış performans

Analitik depo, işlem deposundan ayrı olduğundan, analitik sorgular nedeniyle işlem iş yüklerinizin performansı üzerinde hiçbir etkisi yoktur.  Analitik depoda, ayrılan ayrı istek birimleri (ru) gerekli değildir.

### <a name="auto-sync"></a>Otomatik eşitleme

Otomatik eşitleme, işletimsel verileri ekleme, güncelleştirme ve silme işlevinin neredeyse gerçek zamanlı olarak analitik depoya otomatik olarak eşitlendiği Azure Cosmos DB tam olarak yönetilen özelliği anlamına gelir. Otomatik eşitleme gecikmesi genellikle 2 dakika içinde olur. Çok sayıda kapsayıcı içeren paylaşılan üretilen iş veritabanı durumlarında, tek tek kapsayıcıların otomatik eşitleme gecikmesi daha yüksek olabilir ve 5 dakikaya kadar sürebilir. Bu gecikme süresinin senaryolarınıza uygun olduğunu öğrenmek istiyoruz. Bunun için lütfen [Azure Cosmos DB ekibine](mailto:cosmosdbsynapselink@microsoft.com)ulaşın.

Otomatik eşitleme özelliği analitik depolarla birlikte aşağıdaki başlıca avantajları sağlar:

### <a name="scalability--elasticity"></a>Ölçeklenebilirlik & ölçeklenebilirlik

Azure Cosmos DB işlem deposu, yatay bölümleme kullanarak depolama ve aktarım hızını herhangi bir kesinti olmadan ölçeklendirebilir esnek olabilir. İşlemsel depodaki yatay bölümlendirme, verilerin neredeyse gerçek zamanlı olarak analitik depola eşitlendiğinden emin olmak için otomatik eşitlemede ölçeklenebilirlik & ölçeklenebilirlik sağlar. Veri eşitleme, işlem trafiği aktarım hızına bakılmaksızın, 1000 işlem/sn veya 1.000.000 işlem/sn olmasına bakılmaksızın gerçekleşir ve işlem deposunda sağlanan aktarım hızını etkilemez. 

### <a name="automatically-handle-schema-updates"></a><a id="analytical-schema"></a>Şema güncelleştirmelerini otomatik olarak işle

Azure Cosmos DB işlemsel depolama şeması belirsiz ve şema veya dizin yönetimiyle uğraşmak zorunda kalmadan işlem uygulamalarınızda yineleme yapmanızı sağlar. Bunun aksine, analitik depolamanın, analitik sorgu performansını iyileştirmek için şema Azure Cosmos DB. Otomatik eşitleme özelliğiyle, Azure Cosmos DB işlem deposundan en son güncelleştirmeler için şema çıkarımını yönetir.  Ayrıca, iç içe geçmiş veri türlerini işlemeyi içeren analitik depodaki şema gösterimini de yönetir.

Şemanız geliştikçe ve zaman içinde yeni özellikler eklendikçe, analitik depo otomatik olarak, işlem deposundaki tüm geçmiş şemalar genelinde bir birleştirilmiş şema sunar.

#### <a name="schema-constraints"></a>Şema kısıtlamaları

Aşağıdaki kısıtlamalar, analitik depolamayı otomatik olarak göstermek ve şemayı doğru temsil etmek üzere etkinleştirdiğinizde Azure Cosmos DB işletimsel veriler üzerinde geçerlidir:

* Şemada herhangi bir iç içe geçme düzeyinde en fazla 1000 özellik ve en fazla iç içe geçme derinliği 127 olabilir.
  * Analitik depoda yalnızca ilk 1000 özellikleri temsil edilir.
  * Analitik depoda yalnızca ilk 127 iç içe geçmiş düzeyler temsil edilir.

* JSON belgeleri (ve Cosmos DB koleksiyonları/kapsayıcıları) benzersizlik perspektifinden büyük küçük harfe duyarlı olsa da analitik depo değildir.

  * **Aynı belgede:** Aynı düzeydeki Özellikler adları, Case insensitively ile karşılaştırıldığında benzersiz olmalıdır. Örneğin, aşağıdaki JSON belgesinde aynı düzeyde "Name" ve "Name" vardır. Geçerli bir JSON belgesi olsa da, benzersizlik kısıtlamasını karşılamaz ve bu nedenle analitik depoda tam olarak gösterilmeyecektir. Bu örnekte, "ad" ve "ad", büyük/küçük harfe duyarsız bir şekilde karşılaştırıldığında aynıdır. `"Name": "fred"`İlk oluşum olduğu için yalnızca analitik depoda temsil edilir. Ve hiç `"name": "john"` gösterilmeyecektir.
  
  
  ```json
  {"id": 1, "Name": "fred", "name": "john"}
  ```
  
  * **Farklı belgelerde:** Aynı düzeydeki ve aynı ada sahip olan özellikler, ancak farklı durumlarda, ilk oluşumun ad biçimi kullanılarak aynı sütun içinde temsil edilir. Örneğin, aşağıdaki JSON belgeleri `"Name"` aynı düzeyde ve ' `"name"` dir. İlk belge biçimi olduğundan `"Name"` , bu, analitik depodaki özellik adını temsil etmek için kullanılır. Diğer bir deyişle, analitik depodaki sütun adı olacaktır `"Name"` . Her ikisi de `"fred"` `"john"` sütununda temsil edilir `"Name"` .


  ```json
  {"id": 1, "Name": "fred"}
  {"id": 2, "name": "john"}
  ```


* Koleksiyonun ilk belgesi ilk analitik depo şemasını tanımlar.
  * Belgenin ilk düzeyindeki Özellikler sütun olarak temsil edilir.
  * İlk şemadan daha fazla özelliği olan belgeler, analitik depoda yeni sütunlar oluşturacak.
  * Sütunlar kaldırılamaz.
  * Bir koleksiyondaki tüm belgelerin silinmesi analitik depo şemasını sıfırlayamaz.
  * Şema sürümü oluşturma yok. İşlemsel depodan çıkarılan son sürüm, analitik depoda göreceğiniz şeydir.

* Şu anda boşluklar (boşluk) içeren Azure SYNAPSE Spark okuma sütun adlarını desteklemiyoruz.

* Açık değerlere göre farklı davranış beklenir `null` :
  * Azure SYNAPSE 'daki Spark havuzları bu değerleri `0` (sıfır) olarak okur.
  * Azure SYNAPSE ' deki SQL sunucusuz havuzları, `NULL` aynı özellik için, veri türüne sahip bir değer olan, koleksiyonun ilk belgesi olan bu değerleri okur `non-numeric` .
  * Azure SYNAPSE ' deki SQL sunucusuz havuzları, `0` aynı özellik için, veri türü olan bir değer olan bu değerleri (sıfır) olarak okur `numeric` .

* Eksik sütunlara göre farklı davranış bekliyor:
  * Azure SYNAPSE 'daki Spark havuzları bu sütunları olarak temsil eder `undefined` .
  * Azure SYNAPSE 'deki SQL sunucusuz havuzlar, bu sütunları olarak temsil eder `NULL` .

#### <a name="schema-representation"></a>Şema gösterimi

Analiz deposunda iki şema gösterimi modu var. Bu modlarda sütunlu gösterimin basitliği, polimorfik şemaları işleme ve sorgu deneyiminin basitliği arasında denge kuruluyor:

* İyi tanımlanmış şema gösterimi
* Tam uygunluk şeması temsili

> [!NOTE]
> SQL (çekirdek) API hesapları için, analitik depo etkinleştirildiğinde, analitik depodaki varsayılan şema gösterimi iyi tanımlanmıştır. MongoDB hesapları için Azure Cosmos DB API 'SI için, analitik depodaki varsayılan şema gösterimi tam bir uygunluk şeması gösterimidir. Bu API 'lerin her biri için varsayılan tekliften farklı bir şema temsili gerektiren senaryolar varsa, bunu etkinleştirmek için [Azure Cosmos DB ekibine](mailto:cosmosdbsynapselink@microsoft.com) ulaşın.

**İyi tanımlanmış şema gösterimi**

İyi tanımlanmış şema gösterimi, işlem deposundaki şema belirsiz verilerin basit tablolu bir gösterimini oluşturur. İyi tanımlanmış şema gösterimi aşağıdaki noktalara dikkat edin:

* Bir özellik, birden çok öğe arasında her zaman aynı türe sahiptir.
* Null değerinden başka herhangi bir veri türüne yalnızca 1 tür değişikliğine izin veririz. Null olmayan ilk oluşum, sütun veri türünü tanımlar.

  * Örneğin, `{"a":123} {"a": "str"}` `"a"` bazen bir dize ve bazen bir sayı olduğundan, iyi tanımlanmış bir şemaya sahip değildir. Bu durumda, analitik depo veri türünü `"a"` `“a”` kapsayıcının kullanım ömrü içinde ilk oluşan öğede veri türü olarak kaydeder. Belge yine analitik depoya dahil edilecek, ancak veri türünün farklı olduğu öğeler `"a"` Bu şekilde olmayacaktır.
  
    Bu koşul, null özellikler için uygulanmaz. Örneğin, `{"a":123} {"a":null}` hala iyi tanımlanmış.

* Dizi türleri tek bir tekrarlanmış tür içermelidir.

  * Örneğin, `{"a": ["str",12]}` dizi tamsayı ve dize türleri karışımı içerdiğinden iyi tanımlanmış bir şema değildir.

> [!NOTE]
> Azure Cosmos DB analitik depo iyi tanımlanmış şema gösterimini izliyorsa ve yukarıdaki belirtim belirli öğeler tarafından ihlal edilirse, bu öğeler analitik depoya dahil edilmez.

* İyi tanımlanmış şemadaki farklı türlere göre farklı davranışlar beklenir:
  * Azure SYNAPSE 'daki Spark havuzları bu değerleri olarak temsil eder `undefined` .
  * Azure SYNAPSE 'deki SQL sunucusuz havuzlar bu değerleri olarak temsil eder `NULL` .


**Tam uygunluk şeması temsili**

Tam uygunluk şeması temsili, şema belirsiz işlemsel verilerde çok biçimli şemaların tam kapsamını işlemek için tasarlanmıştır. Bu şema gösteriminde, iyi tanımlanmış şema kısıtlamaları (karışık veri türü alanları veya karışık veri türü dizileri olmayan) ihlal edildiğinde hiçbir öğe analitik depodan atılamaz.

Bu, işletimsel verilerin yaprak özelliklerinin, özelliğindeki değerlerin veri türüne göre ayrı sütunlara sahip analitik depoya çevrilirken elde edilir. Yaprak Özellik adları, analitik depo şemasında, belirsizlik olmadan sorgu olabilecek bir sonek olarak veri türleriyle genişletilir.

Örneğin, işlem deposunda aşağıdaki örnek belgeyi ele alalım:

```json
{
name: "John Doe",
age: 32,
profession: "Doctor",
address: {
  streetNo: 15850,
  streetName: "NE 40th St.",
  zip: 98052
},
salary: 1000000
}
```

`streetNo`İç içe yerleştirilmiş nesne içindeki yaprak özelliği, `address` analitik depo şemasında bir sütun olarak temsil edilir `address.object.streetNo.int32` . Veri türü, sütuna bir sonek olarak eklenir. Bu şekilde, yaprak özelliğinin değerinin `streetNo` "123" olduğu (bir dize olduğunu notta), analitik deponun şeması, önceden yazılmış bir sütunun türünü değiştirmeden otomatik olarak gelişir. `address.object.streetNo.string`Bu "123" değerinin depolandığı şekilde analitik depoya eklenen yeni bir sütun.

**Sonek eşlemesine veri türü**

Bu, tüm özellik veri türlerinin ve bunların son ek temsillerini analitik deposunda bir eşlemedir:

|Özgün veri türü  |Önekini  |Örnek  |
|---------|---------|---------|
| Çift |  ". float64" |    24,99|
| Dizi | ". Array" |    ["a", "b"]|
|İkili | ". Binary" |0|
|Boole    | ". bool"   |Doğru|
|Int32  | ". Int32"  |123|
|Int64  | ". Int64"  |255486129307|
|Null   | ". null"   | null|
|Dize|    ". String" | "ABC"|
|Timestamp |    ". Timestamp" |  Zaman damgası (0, 0)|
|DateTime   |". Date"    | Iztarihi ("2020-08-21T07:43:07.375 Z")|
|ObjectId   |". ObjectID"    | ObjectID ("5f3f7b59330ec25c132623a2")|
|Belge   |". Object" |    {"a": "a"}|

### <a name="cost-effective-archival-of-historical-data"></a>Geçmiş verilerin düşük maliyetli Arşivi

Veri katmanlaması, farklı senaryolar için optimize edilmiş depolama altyapıları arasındaki verilerin ayrılmasının anlamına gelir. Böylece uçtan uca veri yığınının genel performansını ve maliyet verimliliğini artırmaktır. Analitik depolarla Azure Cosmos DB artık, işlem deposundan farklı veri düzenleriyle analitik depoya verilerin otomatik olarak katmanlamasını desteklemektedir. Depolama maliyeti ile karşılaştırıldığında, işlemsel depolamayla karşılaştırıldığında analitik depolamayla en iyi duruma getirilmiş bir işlem, geçmiş analizi için işletimsel verilerin çok daha uzun Horizons.

Analitik depo etkinleştirildikten sonra, işlem iş yüklerinin veri bekletme gereksinimlerine bağlı olarak, belirli bir süre sonra işlem deposundan kayıtları otomatik olarak silmek için ' Işlem depolama süresi canlı (Işlem TTL) ' özelliğini yapılandırabilirsiniz. Benzer şekilde, ' etkin analitik depolama süresi (analitik TTL) ', analitik depoda saklanan veri yaşam döngüsünü işlem deposundan bağımsız olarak yönetmenizi sağlar. Analitik depoyu etkinleştirerek ve TTL özelliklerini yapılandırarak, iki depo için veri saklama süresini sorunsuzca düzenleyebilir ve tanımlayabilirsiniz.

### <a name="global-distribution"></a>Genel Dağıtım

Küresel olarak dağıtılmış bir Azure Cosmos DB hesabınız varsa, bir kapsayıcı için analitik depoyu etkinleştirdikten sonra, bu hesabın tüm bölgelerinde kullanılabilir olacaktır.  İşletimsel verilerde yapılan tüm değişiklikler genel olarak tüm bölgelerde çoğaltılır. Analitik sorguları, Azure Cosmos DB verilerinizin en yakın bölgesel kopyasına göre etkili bir şekilde çalıştırabilirsiniz.

### <a name="security"></a>Güvenlik

Analitik depo ile kimlik doğrulaması, belirli bir veritabanı için işlem deposuyla aynıdır. Kimlik doğrulaması için birincil veya salt okunurdur anahtarlar kullanabilirsiniz. Spark Not defterlerindeki Azure Cosmos DB anahtarlarının yapıştırmayı engellemek için SYNAPSE Studio 'daki bağlı hizmetten yararlanabilirsiniz. Bu bağlı hizmete erişim, çalışma alanına erişimi olan herkes tarafından kullanılabilir.

### <a name="support-for-multiple-azure-synapse-analytics-runtimes"></a>Birden çok Azure SYNAPSE Analytics çalışma zamanı desteği

Analitik depo, işlem çalıştırma sürelerine hiçbir bağımlılığı olmadan analitik iş yükleri için ölçeklenebilirlik, esneklik ve performans sağlamak üzere iyileştirilmiştir. Depolama teknolojisi, el ile gerçekleştirilen çalışmalar olmadan analiz iş yüklerinizi iyileştirmek için kendi kendine yönetilir.

Analitik depolama sistemi analitik işlem sisteminden ayrıldıktan sonra, Azure Cosmos DB analitik depodaki veriler Azure SYNAPSE Analytics tarafından desteklenen farklı analiz çalışma zamanları ile aynı anda sorgulanabilir. Bugün itibariyle Azure SYNAPSE Analytics, Azure Cosmos DB analitik depolama ile Apache Spark ve sunucusuz SQL havuzunu destekler.

> [!NOTE]
> Yalnızca Azure SYNAPSE Analytics çalışma zamanını kullanarak analitik depodan okuyabilirsiniz. Verileri işlem deponuza bir hizmet katmanı olarak geri yazabilirsiniz.

## <a name="pricing"></a><a id="analytical-store-pricing"></a> Fiyat

Analitik mağaza, ücretlendirilebilen tüketim tabanlı bir fiyatlandırma modeli izler:

* Depolama: analitik TTL tarafından tanımlanan geçmiş veriler de dahil olmak üzere her ay analitik depoda tutulan verilerin hacmi.

* Analitik yazma işlemleri: işletimsel veri güncelleştirmelerinin tam olarak yönetilen işlem, işlem deposundan analitik depoya eşitlenmesi (otomatik eşitleme)

* Analitik okuma işlemleri: Azure SYNAPSE Analytics Spark havuzundan ve sunucusuz SQL havuzu çalışma süreleriyle analitik depoya karşı gerçekleştirilen okuma işlemleri.

Analitik mağaza fiyatlandırması, işlem Mağazası fiyatlandırma modelinden ayrıdır. Analitik depoda sağlanan RUs kavramı yoktur. Analitik mağaza için fiyatlandırma modeliyle ilgili tüm ayrıntılar için bkz. [Azure Cosmos DB fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/cosmos-db/).

Azure Cosmos DB kapsayıcısında analitik depolamayı etkinleştirmek üzere yüksek düzeyde bir maliyet tahmini almak için, [Azure Cosmos DB kapasite planlayıcısını](https://cosmos.azure.com/capacitycalculator/) kullanabilir ve analitik depolama ve yazma işlemleri maliyetlerinizin bir tahminini alabilirsiniz. Analitik okuma işlemleri, analiz iş yükü özelliklerine bağlıdır, ancak üst düzey bir tahmin olarak, analitik depoda 1 TB 'lık verilerin taranması genellikle 130.000 analitik okuma işlemlerine neden olur ve maliyeti $0,065 ile sonuçlanır.

## <a name="analytical-time-to-live-ttl"></a><a id="analytical-ttl"></a> Analitik yaşam süresi (TTL)

Analiz TTL değeri, bir kapsayıcı için verilerin analiz deponuzda ne kadar süreyle tutulacağını belirtir. 

Analitik depo etkinleştirilmişse, işlem TTL yapılandırmasından bağımsız olarak, işlem verileri ekleme, güncelleştirme ve silme işlemi, işlemsel depodan analitik depoya otomatik olarak eşitlenir. Bu işletimsel verileri analitik depoda bekletme, aşağıda belirtildiği gibi, kapsayıcı düzeyindeki analitik TTL değeri tarafından denetlenebilir:

Bir kapsayıcıda analitik TTL, özelliği kullanılarak ayarlanır `AnalyticalStoreTimeToLiveInSeconds` :

* Değer "0" olarak ayarlanırsa (veya null olarak ayarlandıysa): analitik depo devre dışıdır ve hiçbir veri işlemsel depodan analitik depoya çoğaltılmaz

* Varsa ve değer "-1" olarak ayarlandıysa, analitik mağaza verilerin işlem deposunda saklanması ne olursa olsun tüm geçmiş verilerini korur. Bu ayar, analitik deponun işletimsel verilerinizin sonsuz bir şekilde bekletildiğini belirtir

* Varsa ve değer bazı pozitif bir sayıya ayarlanırsa "n": öğelerin, işlem deposunda son değiştirilme zamanından sonraki "n" saniye içinde süresi dolacak. İşlemsel depodaki verilerin saklanması ne olursa olsun, işletimsel verilerinizi analitik depoda sınırlı bir süre boyunca saklamak isterseniz, bu ayar yararlanılabilir olabilir

Dikkat edilmesi gereken bazı noktalar:

*   Analitik depo bir analitik TTL değeri ile etkinleştirildikten sonra, daha sonra farklı bir geçerli değere güncelleştirilebilen olabilir. 
*   İşlem TTL 'SI kapsayıcı veya öğe düzeyinde ayarlanırken, analitik TTL yalnızca şu anda kapsayıcı düzeyinde ayarlanabilir.
*   Analitik deposunda, kapsayıcı düzeyinde analitik TTL >= işlem TTL ayarını yaparak, işletimsel verilerinizin daha uzun süre bekletmesini elde edebilirsiniz.
*   Analitik depo, analitik TTL = işlem TTL 'SI ayarlanarak işlem deposunu yansıtmak için yapılabilir.

Bir kapsayıcıda analitik depoyu etkinleştirdiğinizde:

* Azure portal, analitik TTL seçeneği-1 varsayılan değerine ayarlanır. Veri Gezgini altındaki kapsayıcı ayarlarına giderek bu değeri ' n ' saniye olarak değiştirebilirsiniz. 
 
* Azure SDK veya PowerShell ya da CLı 'dan, analitik TTL seçeneği-1 veya ' n ' olarak ayarlanarak etkinleştirilebilir. 

Daha fazla bilgi için bkz. [bir kapsayıcıda ANALITIK TTL yapılandırma](configure-synapse-link.md#create-analytical-ttl).

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki belgelere bakın:

* [Azure Cosmos DB için Azure Synapse Link](synapse-link.md)

* [Azure Cosmos DB için Azure Synapse Link'i kullanmaya başlama](configure-synapse-link.md)

* [Azure Cosmos DB için Synapse Link hakkında sık sorulan sorular](synapse-link-frequently-asked-questions.md)

* [Azure Cosmos DB için Azure Synapse Link kullanım örnekleri](synapse-link-use-cases.md)
