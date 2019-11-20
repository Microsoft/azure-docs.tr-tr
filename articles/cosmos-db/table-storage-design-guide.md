---
title: Ölçeklendirmeyi ve performansı desteklemek için Azure Cosmos DB tabloları tasarlama
description: "Azure depolama tablo Tasarım Kılavuzu: Azure Cosmos DB ve Azure Storage tablosu 'nda ölçeklenebilir ve performanslı tablolar tasarlama"
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: conceptual
ms.date: 05/21/2019
author: wmengmsft
ms.author: wmeng
ms.custom: seodec18
ms.openlocfilehash: 499ac3a394339ebb07c36abeaaa761de22927941
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827768"
---
# <a name="azure-storage-table-design-guide-designing-scalable-and-performant-tables"></a>Azure depolama tablo Tasarım Kılavuzu: ölçeklenebilir ve Performank tabloları tasarlama

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Ölçeklenebilir ve performanslı tablolar tasarlamak için performans, ölçeklenebilirlik ve maliyet gibi çeşitli faktörleri göz önünde bulundurmanız gerekir. Daha önce ilişkisel veritabanları için şemaları tasarlamış olmanız durumunda bu noktalar size tanıdık gelecektir, ancak Azure Tablo hizmeti depolama modeli ve ilişkisel modeller arasında bazı benzerlikler olsa da birçok önemli fark vardır. Bu farklılıklar genellikle, ilişkisel veritabanlarına tanıdık bir kişiye sayaç sezgisel veya yanlış görünebilen, ancak Azure Tablo hizmeti gibi bir NoSQL anahtar/değer deposu için tasarlarken iyi bir fikir veren farklı tasarımlara yol açabilir. Tasarım farklarınızın birçoğu, tablo hizmetinin, verilerin milyarlarca varlık (ilişkisel veritabanı terminolojisinde satırlar) veya yüksek işlem desteklemesi gereken veri kümeleri içerebilen bulut ölçekli uygulamaları destekleyecek şekilde tasarlandığına ilişkin bir olay gösterir. birimler: Bu nedenle, verilerinizi nasıl depoladığınıza ve tablo hizmeti 'nin nasıl çalıştığını nasıl anlayacağınızı düşünün. İyi tasarlanmış bir NoSQL veri deposu, çözümünüzün daha fazla (ve daha düşük bir maliyetle), ilişkisel bir veritabanı kullanan bir çözümden daha fazla ölçeklenebilmesini sağlayabilir. Bu kılavuz, bu konularda size yardımcı olur.  

## <a name="about-the-azure-table-service"></a>Azure Tablo hizmeti hakkında
Bu bölümde, özellikle performans ve ölçeklenebilirlik için tasarlanmasıyla ilgili olan tablo hizmeti 'nin bazı önemli özellikleri vurgulanmıştır. Azure depolama ve tablo hizmeti için yeni başladıysanız, ilk olarak [Microsoft Azure depolama giriş sayfasını](../storage/common/storage-introduction.md) okuyun ve bu makalenin geri kalanını okumadan önce [.NET kullanarak Azure Tablo depolama ile çalışmaya](table-storage-how-to-use-dotnet.md) başlayın. Bu kılavuzun odağı tablo hizmeti üzerinde olsa da, Azure kuyruğu ve BLOB hizmetlerinin bazı tartışmasına ve bunları bir çözümde tablo hizmeti ile birlikte nasıl kullanabileceğinizi de kapsar.  

Tablo hizmeti nedir? Adından bekleneceğiniz gibi tablo hizmeti, verileri depolamak için tablolu bir biçim kullanır. Standart terminolojisinde, tablonun her satırı bir varlığı temsil eder ve sütunlar ilgili varlığın çeşitli özelliklerini depolar. Her varlığın benzersiz şekilde tanımlanması için bir çift anahtar ve tablo hizmetinin varlığın en son ne zaman güncelleştirildiğini izlemek için kullandığı bir zaman damgası sütunu vardır (zaman damgası alanı otomatik olarak eklenir ve zaman damgasının rastgele bir değerle el ile üzerine yazılmaz). Tablo hizmeti, iyimser eşzamanlılık yönetimi için bu son değiştirme zaman damgasını (LMT) kullanır.  

> [!NOTE]
> Tablo hizmeti REST API işlemler ayrıca son değiştirme zaman damgasından (LMT) türetilen bir **ETag** değeri döndürür. Bu belgede, aynı temel verilere başvurdukları için, terimleri ETag ve LMT birbirinin yerine fark edersiniz.  
> 
> 

Aşağıdaki örnek, çalışan ve departman varlıklarını depolamak için basit bir tablo tasarımını göstermektedir. Bu kılavuzda daha sonra gösterilen örneklerin birçoğu bu basit tasarıma dayalıdır.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Zaman damgası</th>
<th></th>
</tr>
<tr>
<td>Pazarlama</td>
<td>00001</td>
<td>2014-08-22T00:50:32Z</td>
<td>
<table>
<tr>
<th>firstName</th>
<th>Soyadı</th>
<th>Yaş</th>
<th>Email</th>
</tr>
<tr>
<td>Yüklemezseniz</td>
<td>Salonu</td>
<td>34</td>
<td>donh@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Pazarlama</td>
<td>00002</td>
<td>2014-08-22T00:50:34Z</td>
<td>
<table>
<tr>
<th>firstName</th>
<th>Soyadı</th>
<th>Yaş</th>
<th>Email</th>
</tr>
<tr>
<td>:</td>
<td>Cao dili</td>
<td>47</td>
<td>junc@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Pazarlama</td>
<td>Bölüm</td>
<td>2014-08-22T00:50:30Z</td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Pazarlama</td>
<td>153</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>Satış</td>
<td>00010</td>
<td>2014-08-22T00:50:44Z</td>
<td>
<table>
<tr>
<th>firstName</th>
<th>Soyadı</th>
<th>Yaş</th>
<th>Email</th>
</tr>
<tr>
<td>UK</td>
<td>Kwok</td>
<td>23</td>
<td>kenk@contoso.com</td>
</tr>
</table>
</td>
</tr>
</table>


Şimdiye kadar, bu tasarım, ilişkisel bir veritabanındaki bir tabloya benzer ve bu, aynı tabloda birden çok varlık türünü depolama özelliği olan ilişkisel bir veritabanındaki tabloya benzer. Buna ek olarak, **ad** veya **yaş** gibi Kullanıcı tanımlı özelliklerin her biri, ilişkisel veritabanındaki bir sütun gibi bir veri türüne (örneğin, tamsayı veya dize) sahiptir. İlişkisel bir veritabanının aksine, tablo hizmetinin şema-daha az doğası, bir özelliğin her varlıkta aynı veri türüne sahip olması gerektiği anlamına gelir. Karmaşık veri türlerini tek bir özellikte depolamak için JSON veya XML gibi serileştirilmiş bir biçim kullanmanız gerekir. Desteklenen veri türleri, desteklenen tarih aralıkları, adlandırma kuralları ve boyut kısıtlamaları gibi tablo hizmeti hakkında daha fazla bilgi için bkz. [Tablo hizmeti veri modelini anlama](https://msdn.microsoft.com/library/azure/dd179338.aspx).

Göreceğiniz gibi, **partitionkey** ve **rowkey** seçiminiz, iyi tablo tasarımı için temel seçenektir. Bir tabloda depolanan her varlık **partitionkey** ve **rowkey**öğesinin benzersiz bir birleşimine sahip olmalıdır. İlişkisel bir veritabanı tablosundaki anahtarlarda olduğu gibi, **partitionkey** ve **rowkey** değerleri, hızlı görünümü sağlayan bir kümelenmiş dizin oluşturmak için dizinlenir; Ancak, tablo hizmeti herhangi bir ikincil dizin oluşturmaz, bu nedenle bunlar iki dizinli özelliklerdir. (daha sonra açıklanan bazı desenler, daha sonra bu görünen sınırlamanın nasıl geçici bir şekilde çalışabileceksiniz).  

Bir tablo, bir veya daha fazla bölümden oluşur ve göreceğiniz gibi, yaptığınız tasarım kararlarının birçoğu, çözümünüzü iyileştirmek için uygun bir **partitionkey** ve **rowkey** seçme sonrasında olacaktır. Bir çözüm, bölümler halinde düzenlenmiş tüm varlıklarınızı içeren tek bir tablodan oluşabilir, ancak genellikle bir çözümde birden fazla tablo olacaktır. Tablolar, varlıklarınızı mantıksal olarak düzenlemenize, erişim denetim listelerini kullanarak verilere erişimi yönetmenize yardımcı olur ve tek bir depolama işlemi kullanarak tüm tabloyu bırakabilirsiniz.  

### <a name="table-partitions"></a>Tablo bölümleri
Hesap adı, tablo adı ve **Partitionkey** birlikte tablo hizmetinin varlığı depoladığı depolama hizmeti içindeki bölümü belirler. Varlıklar için adresleme şemasının bir parçası olan bölümler, işlemler için bir kapsam tanımlar (aşağıdaki [varlık grubu işlemlerine](#entity-group-transactions) bakın) ve tablo hizmetinin ölçeklendirme temelini oluşturur. Bölümler hakkında daha fazla bilgi için bkz. [Azure Storage ölçeklenebilirlik ve performans hedefleri](../storage/common/storage-scalability-targets.md).  

Tablo hizmetinde, tek bir düğüm hizmeti bir veya daha fazla tam bölüm ve hizmet, düğümler arasında dinamik yük dengeleme bölümlerine göre ölçeklendirilir. Bir düğüm Load altındaysa, tablo hizmeti bu düğüm tarafından hizmet verilen bölümlerin aralığını farklı düğümlere *bölebilir* ; trafik alt taraflarından, hizmet, Bölüm aralıklarını sessiz düğümlerden tek bir düğüme geri *birleştirebilirler* .  

Tablo hizmetinin iç ayrıntıları ve hizmetin bölümleri nasıl yönettiği hakkında daha fazla bilgi için, bkz. [Microsoft Azure depolama: güçlü tutarlılık Içeren yüksek oranda kullanılabilir bir bulut depolama hizmeti](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

### <a name="entity-group-transactions"></a>Varlık grubu Işlemleri
Tablo hizmetinde, varlık grubu Işlemleri (Yumurlar), birden çok varlık arasında atomik güncelleştirmeler gerçekleştirmeye yönelik tek yerleşik mekanizmadır. Yumurtları, bazı belgelerde *toplu işlemler* olarak da adlandırılır. Yumurtları yalnızca aynı bölümde depolanan varlıklar üzerinde çalışabilir (belirli bir tabloda aynı bölüm anahtarını paylaşabilirsiniz), bu nedenle birden çok varlık genelinde atomik işlem davranışına ihtiyacınız varsa, bu varlıkların aynı bölümde yer aldığından emin olmanız gerekir. Bu genellikle, farklı varlık türleri için birden çok tablo kullanmayan aynı tabloda (ve bölümünde) birden çok varlık türünü tutmanın bir nedenidir. Tek bir EGT, en fazla 100 varlık üzerinde çalışabilir.  İşlenmek üzere birden çok eş zamanlı Yumurtları gönderirseniz, bu yumurtların, aksi takdirde işleme gecikebilir.

Ayrıca, tasarımınızda değerlendirmeniz için olası bir denge sunar: Azure, düğümler arasında yük dengeleme istekleri için daha fazla fırsat içerdiğinden, daha fazla bölüm kullanmak uygulamanızın ölçeklenebilirliğini artırır, ancak bu durum şu özelliği sınırlayabilir: Uygulamanızın Atomik işlemler gerçekleştirmesi ve verilerinize yönelik güçlü tutarlılık sağlamak için uygulamanız. Ayrıca, tek bir düğüm için bekleeceğiniz işlemlerin verimini sınırlayan bir bölüm düzeyinde belirli ölçeklenebilirlik hedefleri vardır: Azure depolama hesapları ve tablo hizmeti için ölçeklenebilirlik hedefleri hakkında daha fazla bilgi için bkz. [Azure depolama ölçeklenebilirlik ve performans hedefleri](../storage/common/storage-scalability-targets.md). Bu kılavuzun sonraki bölümlerinde, bu gibi ticari koşulları yönetmenize yardımcı olan çeşitli tasarım stratejileri ele alınmaktadır ve istemci uygulamanızın belirli gereksinimlerine göre Bölüm anahtarınızı ne kadar en iyi şekilde seçebileceğinizi tartışmaktadır.  

### <a name="capacity-considerations"></a>Kapasite Konuları
Aşağıdaki tabloda, bir tablo hizmeti çözümü tasarlarken bilmeniz için bazı anahtar değerler yer almaktadır:  

| Bir Azure depolama hesabının toplam kapasitesi | 500 TB |
| --- | --- |
| Bir Azure depolama hesabındaki tablo sayısı |Yalnızca depolama hesabının kapasitesine göre sınırlandırılır |
| Tablodaki bölüm sayısı |Yalnızca depolama hesabının kapasitesine göre sınırlandırılır |
| Bir bölümdeki varlıkların sayısı |Yalnızca depolama hesabının kapasitesine göre sınırlandırılır |
| Tek bir varlığın boyutu |Maksimum 255 özelliği olan 1 MB 'a kadar ( **Partitionkey**, **Rowkey**ve **timestamp**dahil) |
| **Partitionkey** boyutu |Boyutu 1 KB 'a kadar olan bir dize |
| **Rowkey** boyutu |Boyutu 1 KB 'a kadar olan bir dize |
| Bir varlık grubu Işleminin boyutu |Bir işlem en fazla 100 varlığı içerebilir ve yükün boyutu 4 MB 'tan az olmalıdır. EGT, bir varlığı yalnızca bir kez güncelleştirebilir. |

Daha fazla bilgi için bkz. [Tablo Hizmeti Veri Modelini anlama](https://msdn.microsoft.com/library/azure/dd179338.aspx).  

### <a name="cost-considerations"></a>Maliyetle ilgili konular
Tablo Depolaması nispeten ucuzdur, ancak her iki kapasite kullanımı için maliyet tahminleri ve tablo hizmetini kullanan herhangi bir çözüm değerlendirmesinin bir parçası olarak işlem miktarı dahil edilmelidir. Ancak, çok daha fazla sayıda senaryoda, çözümünüzün performansını veya ölçeklenebilirliğini geliştirmek için büyük veya yinelenen verileri depolamak için geçerli bir yaklaşım vardır. Fiyatlandırma hakkında daha fazla bilgi için bkz. [Azure Depolama fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="guidelines-for-table-design"></a>Tablo tasarımı için yönergeler
Bu listeler, tablolarınızı tasarlarken göz önünde bulundurmanız gereken bazı temel yönergeleri özetler ve bu kılavuz, daha sonra içinde daha ayrıntılı olarak ele alınacaktır. Bu kılavuzlar, genellikle ilişkisel veritabanı tasarımı için izleyeceğiniz kılavuzlardan farklıdır.  

Tablo hizmeti *çözümünüzü etkili bir* şekilde tasarlamak için tasarlama:

* ***Okuma ağır uygulamalarda sorgulama için tasarım.*** Tablolarınızı tasarlarken, varlıklarınızı nasıl güncelleşeceğinize ilişkin düşündüğünüzden önce kullanacağınız sorguları (özellikle gecikme süresine duyarlı olanları) düşünün. Bu, genellikle etkili ve performanslı bir çözüme neden olur.  
* ***Sorgularınızda hem PartitionKey hem de RowKey belirtin.*** Bu gibi *nokta sorguları* en verimli tablo hizmeti sorgulardır.  
* ***Varlıkların yinelenen kopyalarını depolamayı göz önünde bulundurun.*** Tablo depolaması, daha verimli sorgular sağlamak için aynı varlığı birden çok kez depolamayı (farklı anahtarlarla) göz önünde bulundurun.  
* ***Verilerinizi kabul etmeyi düşünün.*** Tablo depolaması, veri izlemeyi düşünün. Örneğin, toplama verileri sorgularının yalnızca tek bir varlığa erişmesi için, Özet varlıkları depolayın.  
* ***Bileşik anahtar değerlerini kullanın.*** Yalnızca **partitionkey** ve **rowkey**olduğunuz anahtarlar vardır. Örneğin, varlıklara alternatif anahtarlı erişim yolları sağlamak için bileşik anahtar değerlerini kullanın.  
* ***Sorgu projeksiyonu kullanın.*** Yalnızca ihtiyaç duyduğunuz alanları seçerek ağ üzerinden aktardığınız veri miktarını azaltabilirsiniz.  

Tablo hizmeti çözümünüzü *yazma* etkili şekilde tasarlama:  

* ***Etkin bölümler oluşturmayın.*** İsteklerinizi dilediğiniz zaman bir noktada birden çok bölüme yaymaya olanak sağlayan anahtarlar ' ı seçin.  
* ***Trafikte ani artışlar önleyin.*** Trafiği makul bir süre boyunca düzgünleştirmek ve trafikte ani artışlar önleyin.
* ***Her varlık türü için ayrı bir tablo oluşturmanız gerekmez.*** Varlık türleri arasında Atomik işlemler gerektirdiğinde, bu birden çok varlık türünü aynı tablodaki aynı bölümde saklayabilirsiniz.
* ***Elde etmeniz gereken en yüksek aktarım hızını göz önünde bulundurun.*** Tablo hizmeti için ölçeklenebilirlik hedeflerini bilmeniz ve tasarımınızın bunları aşmanıza yol açmayacağından emin olmanız gerekir.  

Bu kılavuzu okurken, tüm bu ilkeleri uygulamaya yerleştirtirecek örnekleri görürsünüz.  

## <a name="design-for-querying"></a>Sorgulama için tasarım
Tablo hizmeti çözümleri yoğun bir şekilde okunabilir, yoğun yazma veya ikisinin karışımı olabilir. Bu bölüm, tablo hizmetinizi, okuma işlemlerini verimli bir şekilde destekleyecek şekilde tasarlarken göz önünde bulundurmanız gereken şeylere odaklanmaktadır. Genellikle, okuma işlemlerini etkin şekilde destekleyen bir tasarım, yazma işlemleri için de etkilidir. Ancak, [veri değişikliği Için tasarım](#design-for-data-modification), sonraki bölümde ele alınan yazma işlemlerini desteklemek için tasarlanırken dikkate alınması gereken ek hususlar vardır.

Tablo hizmeti çözümünüzü tasarlamak için iyi bir başlangıç noktası, verileri etkili bir şekilde okumanızı sağlamak üzere "uygulamamın hangi sorguları tablo hizmeti 'nden alması gerekir?" sormanız.  

> [!NOTE]
> Tablo hizmeti ile, daha sonra değiştirilmesi zor ve pahalı olduğundan tasarımın doğru olması önemlidir. Örneğin, ilişkisel bir veritabanında yalnızca var olan bir veritabanına dizinler ekleyerek performans sorunlarını ele almak mümkündür: Bu tablo hizmetinde bir seçenek değildir.  
> 
> 

Bu bölümde, tabloları sorgulamak için tasarlarken bilmeniz gereken önemli sorunlar ele alınmaktadır. Bu bölümde ele alınan konular şunları içerir:

* [PartitionKey ve RowKey seçimlerinizin sorgu performansını nasıl etkilediği](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
* [Uygun bir PartitionKey seçme](#choosing-an-appropriate-partitionkey)
* [Tablo hizmeti için sorguları iyileştirme](#optimizing-queries-for-the-table-service)
* [Tablo hizmetindeki verileri sıralama](#sorting-data-in-the-table-service)

### <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>PartitionKey ve RowKey seçimlerinizin sorgu performansını nasıl etkilediği
Aşağıdaki örneklerde, tablo hizmetinin çalışan varlıklarını aşağıdaki yapıyla depoladığını varsaymaktadır (örneklerin çoğu açıklık için **zaman damgası** özelliğini atlayın):  

| *Sütun adı* | *Veri türü* |
| --- | --- |
| **Partitionkey** (bölüm adı) |Dize |
| **Rowkey** (çalışan kimliği) |Dize |
| **FirstName** |Dize |
| **Soyadı** |Dize |
| **Ölçerin** |Tamsayı |
| **EmailAddress** |Dize |

Önceki bölümde Azure Tablo hizmetine genel bakış, Azure Tablo hizmeti 'nin sorgu tasarımı üzerinde doğrudan bir etkisi olan temel özelliklerinden bazılarını açıklar. Bunlar, tablo hizmeti sorguları tasarlamak için aşağıdaki genel yönergelere yol açabilir. Aşağıdaki örneklerde kullanılan filtre sözdizimi tablo hizmeti REST API. daha fazla bilgi için bkz. [Sorgu varlıkları](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

* ***Nokta sorgusu*** , en verimli arama ve en düşük gecikme süresi gerektiren aramalar için kullanılması önerilir. Bu tür bir sorgu, her iki **partitionkey** ve **rowkey** değerlerini belirterek tek bir varlığı etkin bir şekilde bulmak için dizinleri kullanabilir. Örneğin: $filter = (PartitionKey EQ ' Sales ') ve (RowKey EQ ' 2 ')  
* İkinci değer en iyisi, birden fazla varlık döndürmek için **partitionkey** değerlerini bir aralıktaki **rowkey** değerleri ve filtreleri kullanan bir ***Aralık sorgusudur*** . **Partitionkey** değeri belirli bir bölümü tanımlar ve **rowkey** değerleri söz konusu bölümdeki varlıkların bir alt kümesini tanımlar. Örneğin: $filter = PartitionKey EQ ' Sales ' ve RowKey Ge ' ve RowKey lt 'T '  
* Üçüncü en iyisi, bir diğer anahtar olmayan özellikte ve birden fazla varlık döndürebilen **Partitionkey** ve Filters kullanan bir ***bölüm taramasından*** biridir. **Partitionkey** değeri belirli bir bölümü tanımlar ve özellik değerleri söz konusu bölümdeki varlıkların bir alt kümesini seçer. Örneğin: $filter = PartitionKey EQ ' Sales ' ve LastName EQ ' Smith '  
* ***Tablo taraması*** **partitionkey** içermez ve herhangi bir eşleşen varlık için tabloyu oluşturan tüm bölümleri aradığı için verimsiz olur. Filtrenizin **Rowkey**kullanıp kullanmadığını bakılmaksızın tablo taraması gerçekleştirir. Örneğin: $filter = LastName EQ ' Jones '  
* Birden çok varlık döndüren Azure Tablo depolama sorguları, bunları **partitionkey** ve **rowkey** sırasına göre sıralanmış olarak döndürür. İstemcideki varlıkları yeniden kullanmaktan kaçınmak için en yaygın sıralama düzenini tanımlayan bir **Rowkey** seçin. Azure Cosmos DB Azure Tablo API'si tarafından döndürülen sorgu sonuçları bölüm anahtarına veya satır anahtarına göre sıralanmaz. Özellik farklarının ayrıntılı bir listesi için, [Azure Cosmos DB ve Azure Tablo depolamadaki tablo API'si arasındaki farklara](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior)bakın.

**Rowkey** değerlerini temel alan bir filtre belirtmek için "**or**" kullanılması, Bölüm taramasıyla sonuçlanır ve Aralık sorgusu olarak değerlendirilmez. Bu nedenle, şu gibi filtreler kullanan sorgulardan kaçınmalısınız: $filter = PartitionKey EQ ' Sales ' ve (RowKey EQ ' 121 ' veya RowKey EQ ' 322 ')  

Etkili sorgular yürütmek için depolama Istemci kitaplığını kullanan istemci tarafı kodu örnekleri için, bkz.:  

* [Depolama Istemci kitaplığını kullanarak bir nokta sorgusu yürütme](#executing-a-point-query-using-the-storage-client-library)
* [LINQ kullanarak birden çok varlık alma](#retrieving-multiple-entities-using-linq)
* [Sunucu tarafı projeksiyonu](#server-side-projection)  

Aynı tabloda depolanan birden çok varlık türünü işleyebilen istemci tarafı kodu örnekleri için, bkz.:  

* [Heterojen varlık türleriyle çalışma](#working-with-heterogeneous-entity-types)  

### <a name="choosing-an-appropriate-partitionkey"></a>Uygun bir PartitionKey seçme
**Partitionkey** tercih ettiğiniz bölüm, varlıklarınızı birden çok bölüme dağıtmak (ölçeklenebilir bir çözüm sağlamak için) gereksinimini karşılamak Için gereken yumurtların kullanılmasını (tutarlılık sağlamak için) sağlar.  

Bir Extreme 'de, tüm varlıklarınızı tek bir bölümde saklayabilirsiniz, ancak bu, çözümünüzün ölçeklenebilirliğini sınırlayabilir ve tablo hizmeti 'nin istekleri yük dengelemesine engel olabilir. Diğer bir deyişle, bölüm başına bir varlık saklayabilir ve bu, son derece ölçeklenebilir olan ve tablo hizmeti 'nin istekleri yük dengelemesine olanak tanıyan, ancak bu durum varlık grubu işlemlerini kullanmanızı engelleyecek.  

İdeal bir **Partitionkey** , çözümünüzün ölçeklenebilir olduğundan emin olmak için verimli sorgular kullanmanıza ve yeterli bölüm olmasına olanak sağlar. Genellikle, varlıklarınızın varlıklarınızı yeterli bölümler arasında dağıtan uygun bir özelliği olacağını göreceksiniz.

> [!NOTE]
> Örneğin, kullanıcılar veya çalışanlar hakkında bilgi depolayan bir sistemde, Kullanıcı kimliği iyi bir PartitionKey olabilir. Bölüm anahtarı olarak belirli bir kullanıcı kimliğini kullanan birkaç varlık olabilir. Bir kullanıcı hakkında veri depolayan her varlık tek bir bölümde gruplandırılır ve bu varlıkların varlık grubu işlemleri aracılığıyla erişilebilir, ancak hala yüksek oranda ölçeklenebilir hale getirilir.
> 
> 

Varlıkları ekleme, güncelleştirme ve silme işlemlerinin nasıl yapılacağı konusunda yer alan **Partitionkey** seçiminizle ilgili ek hususlar vardır: aşağıdaki [veri değişikliği için bölüm tasarımına](#design-for-data-modification) bakın.  

### <a name="optimizing-queries-for-the-table-service"></a>Tablo hizmeti için sorguları iyileştirme
Tablo hizmeti, tek bir kümelenmiş dizindeki **partitionkey** ve **rowkey** değerlerini kullanarak varlıklarınızı otomatik olarak dizinlenir, bu nedenle nokta sorgularının kullanım açısından en verimli olması neden olur. Ancak, **partitionkey** ve **rowkey**üzerindeki kümelenmiş dizinin dışında başka bir dizin yoktur.

Birçok tasarım, varlıkların birden çok ölçüte göre aramasını etkinleştirmek için gereksinimlere uymalıdır. Örneğin, e-posta, çalışan kimliği veya soyadı temelinde çalışan varlıklarını bulma. Bölüm [tablosu tasarım desenlerindeki](#table-design-patterns) aşağıdaki desenler, bu tür gereksinimleri ele alarak tablo hizmetinin ikincil dizinler sağlamadığına ilişkin yolları anlatmaktadır:  

* [Bölüm içi ikincil dizin düzeni](#intra-partition-secondary-index-pattern) **-farklı** rowkey değerleri kullanılarak hızlı ve verimli aramalar ve alternatif sıralama emirlerini etkinleştirmek için, her bir varlığın farklı **rowkey** değerlerini kullanarak birden çok kopyasını depolayın (aynı bölümde).  
* [Bölümler arası ikincil dizin düzeni](#inter-partition-secondary-index-pattern) -ayrı bölümlerde veya ayrı tablolarda farklı **rowkey** değerleri kullanarak her bir varlığın birden çok kopyasını depolayın ve farklı **şekilde kullanarak hızlı ve verimli aramalar ve alternatif sıralama düzenleri RowKey** değerleri.  
* [Dizin varlıkları model](#index-entities-pattern) -varlık listeleri döndüren etkili aramaları etkinleştirmek için Dizin varlıklarını koruyun.  

### <a name="sorting-data-in-the-table-service"></a>Tablo hizmetindeki verileri sıralama

Tablo hizmeti tarafından döndürülen sorgu sonuçları, **partitionkey** ve sonra **rowkey**temelinde artan düzende sıralanır.

> [!NOTE]
> Azure Cosmos DB Azure Tablo API'si tarafından döndürülen sorgu sonuçları bölüm anahtarına veya satır anahtarına göre sıralanmaz. Özellik farklarının ayrıntılı bir listesi için, [Azure Cosmos DB ve Azure Tablo depolamadaki tablo API'si arasındaki farklara](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior)bakın.

Azure Storage tablosundaki anahtarlar dize değerleridir ve sayısal değerlerin doğru şekilde sıralanmasını sağlamak için, bunları sabit bir uzunluğa dönüştürmeniz ve sıfırlarla yeniden örneklerinizin olması gerekir. Örneğin, **Rowkey** olarak kullandığınız çalışan kimliği değeri bir tamsayı değeri ise, **123** çalışan kimliğini **00000123**olarak dönüştürmeniz gerekir. 

Birçok uygulamanın, farklı siparişlerde sıralanmış verileri kullanma gereksinimleri vardır: Örneğin, çalışanları ada göre veya birleştirme tarihine göre sıralama. Bölüm [tablosu tasarım desenlerindeki](#table-design-patterns) aşağıdaki desenler, varlıklarınız için nasıl alternatif sıralama düzenleri ele alma bölümüne yöneliktir:  

* [Bölüm içi ikincil dizin düzeni](#intra-partition-secondary-index-pattern) -farklı rowkey değerleri kullanılarak hızlı ve verimli aramalar ve alternatif sıralama emirlerini etkinleştirmek için, her bir varlığın farklı rowkey değerlerini kullanarak birden çok kopyasını depolayın (aynı bölümde).  
* [Bölümler arası ikincil dizin düzeni](#inter-partition-secondary-index-pattern) -farklı rowkey değerleri kullanarak, hızlı ve verimli aramalar ve alternatif sıralama emirlerini etkinleştirmek üzere ayrı bölümlerde ayrı bölümlerdeki farklı rowkey değerleri kullanarak her bir varlığın birden çok kopyasını saklayın.
* [Günlük kuyruğu düzeni](#log-tail-pattern) -ters tarih ve saat düzeninde sıralama yaparak bir **rowkey** değeri kullanarak bir bölüme en son eklenen *n* varlıklarını alın.  

## <a name="design-for-data-modification"></a>Veri değişikliği için tasarım
Bu bölüm, eklemeleri, güncelleştirmeleri ve silmeleri iyileştirmeye yönelik tasarım konularına odaklanır. Bazı durumlarda, ilişkisel veritabanları için tasarımlarda yaptığınız gibi veri değişikliği için optimize eden tasarımlara sorgu için optimize eden tasarımlar arasındaki dengelemeyi değerlendirmeniz gerekir (ancak tasarım kredlarını yönetme teknikleri ilişkisel veritabanında farklı). Bölüm [tablosu tasarım desenleri](#table-design-patterns) , tablo hizmeti için bazı ayrıntılı tasarım düzenlerini açıklar ve bu ticaretin bazılarını vurgular. Uygulamada, varlıkları sorgulamak için en iyi duruma getirilmiş birçok tasarım, varlıkları değiştirmek için de iyi çalışır.  

### <a name="optimizing-the-performance-of-insert-update-and-delete-operations"></a>INSERT, Update ve DELETE işlemlerinin performansını iyileştirme
Bir varlığı güncelleştirmek veya silmek için, **partitionkey** ve **rowkey** değerlerini kullanarak belirleyebilmelisiniz. Bu şekilde, varlıkları değiştirmek için **partitionkey** ve **rowkey** tercihiniz, varlıkları mümkün olduğunca verimli bir şekilde tanımlamak istediğiniz için, nokta sorgularını desteklemek için gereken benzer ölçütlere uymalıdır. Bir varlığı, güncelleştirmek veya silmek için gereken **partitionkey** ve **rowkey** değerlerini bulmak için bir varlığı bulmak üzere verimsiz bir bölüm veya tablo taraması kullanmak istemezsiniz.  

Bölüm [tablosu tasarım desenlerinde](#table-design-patterns) , performansı en iyi duruma getirme ve ekleme, güncelleştirme ve silme işlemlerinizi ele alarak aşağıdaki desenler:  

* [Yüksek hacimli silme stili](#high-volume-delete-pattern) -aynı anda tüm varlıkları kendi ayrı tablolarındaki eşzamanlı silme için depolayarak yüksek hacimli varlıkların silinmesini etkinleştirin; tabloyu silerek varlıkları silersiniz.  
* [Veri serisi stili](#data-series-pattern) -yaptığınız istek sayısını en aza indirmek için tam veri serisini tek bir varlıkta depolayın.  
* [Geniş varlıklar stili](#wide-entities-pattern) -birden fazla fiziksel varlık kullanarak 252 'den fazla özelliği olan mantıksal varlıkları depolayın.  
* [Büyük varlıklar stili](#large-entities-pattern) -büyük özellik değerlerini depolamak için BLOB depolama kullanın.  

### <a name="ensuring-consistency-in-your-stored-entities"></a>Saklı varlıklarınızda tutarlılık sağlama
Veri değişikliklerini iyileştirmeye yönelik anahtar seçiminizi etkileyen diğer anahtar faktörü, Atomik işlemler kullanılarak tutarlılık sağlama sağlamaktır. Bir EGT 'yi yalnızca aynı bölümde depolanan varlıklar üzerinde çalışmak için kullanabilirsiniz.  

Bölüm [tablosu tasarım desenlerinde](#table-design-patterns) aşağıdaki desenler tutarlılığı yönetme adresi:  

* [Bölüm içi ikincil dizin düzeni](#intra-partition-secondary-index-pattern) **-farklı** rowkey değerleri kullanılarak hızlı ve verimli aramalar ve alternatif sıralama emirlerini etkinleştirmek için, her bir varlığın farklı **rowkey** değerlerini kullanarak birden çok kopyasını depolayın (aynı bölümde).  
* [Bölümler arası ikincil dizin düzeni](#inter-partition-secondary-index-pattern) -ayrı bölümlerde veya ayrı tablolarda farklı rowkey değerleri kullanarak her bir varlığın birden çok kopyasını depolayın ve farklı **rowkey** kullanarak hızlı ve verimli aramalar ve alternatif sıralama emirlerini etkinleştirin deðerler.  
* [Sonuçta tutarlı işlemler stili](#eventually-consistent-transactions-pattern) -Azure kuyrukları kullanarak bölüm sınırları veya depolama sistemi sınırları genelinde sürekli tutarlı davranışı etkinleştirin.
* [Dizin varlıkları model](#index-entities-pattern) -varlık listeleri döndüren etkili aramaları etkinleştirmek için Dizin varlıklarını koruyun.  
* [Normalleştirilmişleştirme stili](#denormalization-pattern) -tek bir nokta sorgusuyla ihtiyacınız olan tüm verileri almanızı sağlamak için ilgili verileri tek bir varlıkta birleştirin.  
* [Veri serisi stili](#data-series-pattern) -yaptığınız istek sayısını en aza indirmek için tam veri serisini tek bir varlıkta depolayın.  

Varlık grubu işlemleri hakkında daha fazla bilgi için bkz. [varlık grubu işlemleri](#entity-group-transactions)bölümü.  

### <a name="ensuring-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Tasarımınızın etkili değişiklikler yapmasını sağlamak etkili sorguları kolaylaştırır
Birçok durumda, verimli bir şekilde sorgulama yapmak için bir tasarım etkili değişiklikler sağlar, ancak bunun belirli senaryonuz için durum olup olmadığını her zaman değerlendirin. Bölüm [tablosu tasarım desenlerindeki](#table-design-patterns) desenlerden bazıları, varlıkları sorgulama ve değiştirme arasındaki dengelemeler açıkça değerlendirir ve her bir işlem türünün sayısını her zaman dikkate almanız gerekir.  

Bölüm [tablosu tasarım desenlerinde](#table-design-patterns) aşağıdaki desenler, verimli sorgular tasarlama ve verimli veri değişikliği için tasarlama arasında denge sağlar:  

* [Bileşik anahtar stili](#compound-key-pattern) -bir istemcinin tek nokta sorgusuyla ilgili verileri araması Için bileşik **rowkey** değerlerini kullanın.  
* [Günlük kuyruğu düzeni](#log-tail-pattern) -ters tarih ve saat düzeninde sıralama yaparak bir **rowkey** değeri kullanarak bir bölüme en son eklenen *n* varlıklarını alın.  

## <a name="encrypting-table-data"></a>Tablo verilerini şifreleme
.NET Azure depolama Istemci kitaplığı, ekleme ve değiştirme işlemleri için dize varlık özelliklerinin şifrelenmesini destekler. Şifrelenmiş dizeler hizmette ikili özellikler olarak depolanır ve şifre çözme sonrasında dizelere geri dönüştürülür.    

Tablolar için, şifreleme ilkesine ek olarak, kullanıcıların şifrelenecek özellikleri belirtmesi gerekir. Bu işlem, bir [EncryptProperty] özniteliği belirtilerek (TableEntity 'tan türetilmiş POCO varlıkları için) veya istek seçeneklerindeki bir şifreleme çözümleyicisine göre yapılabilir. Şifreleme çözümleyici, Bölüm anahtarını, satır anahtarını ve özellik adını alan ve bu özelliğin şifrelenmesi gerekip gerekmediğini belirten bir Boolean döndüren bir temsilcisidir. Şifreleme sırasında, istemci kitaplığı bu bilgileri, bir özelliğin hatta yazarken şifrelenmesi gerekip gerekmediğine karar vermek için kullanır. Temsilci, özelliklerin nasıl şifrelendiğini gösteren mantık olasılığını da sağlar. (Örneğin, X ise, özelliğini şifreleyin; Aksi halde A ve B özelliklerini şifreleyin.) Varlıkları okurken veya sorgularken bu bilgilerin sağlanması gerekli değildir.

Birleştirme Şu anda desteklenmiyor. Özelliklerin bir alt kümesi daha önce farklı bir anahtar kullanılarak şifrelendiğinden, yalnızca yeni özellikleri birleştirmek ve meta verileri güncelleştirmek veri kaybına neden olur. Birleştirme, hizmette önceden var olan varlığı okumak ya da her ikisi de performans nedenleriyle uygun olmayan yeni bir anahtar kullanmak için ek hizmet çağrıları yapılmasını gerektirir.     

Tablo verilerini şifreleme hakkında daha fazla bilgi için bkz. [Microsoft Azure depolama Için Istemci tarafı şifreleme ve Azure Key Vault](../storage/common/storage-client-side-encryption.md).  

## <a name="modeling-relationships"></a>İlişkileri modelleme
Etki alanı modellerinin oluşturulması, karmaşık sistemlerin tasarımında önemli bir adımdır. Genellikle, iş etki alanını anlamak ve sisteminizin tasarımını bilgilendirmek için bir yöntem olarak varlıkları ve aralarındaki ilişkileri tanımlamak için modelleme sürecini kullanırsınız. Bu bölüm, etki alanı modellerinde bulunan ortak ilişki türlerinden bazılarını tablo hizmeti için tasarımlara nasıl çevrilele, ' a odaklanır. Bir mantıksal veri modelinden fiziksel bir NoSQL tabanlı veri modeliyle eşleme işlemi, ilişkisel bir veritabanı tasarlarken kullanılan değerinden farklıdır. İlişkisel veritabanları tasarımı genellikle, bir veri normalleştirme işleminin artıklığı en iyi duruma getirme ve veritabanının nasıl çalıştığına ilişkin bir sorgulama özelliği olduğunu varsayar.  

### <a name="one-to-many-relationships"></a>Bire çok ilişkileri
İş etki alanı nesneleri arasında bire çok ilişki sık gerçekleşir: Örneğin, bir departmanın birçok çalışanı vardır. Tablo hizmetinde, her biri belirli senaryoya uygun olabilecek profesyonelleri ve dezavantajlarla tek-çok ilişkilerini uygulamak için birkaç yol vardır.  

Her departmanın, belirli bir departmanla ilişkili olarak birçok çalışana ve çalışana sahip olduğu on binlerce departman ve çalışan varlığı içeren büyük bir Multi-National Corporation örneğini göz önünde bulundurun. Tek bir yaklaşım, bunlar gibi ayrı departmanı ve çalışan varlıklarını depomaktır:  

![Departman ve çalışan varlığı][1]

Bu örnek, **Partitionkey** değerini temel alan türler arasında örtük bir çoktan çoğa ilişki gösterir. Her departmanın birçok çalışanı olabilir.  

Bu örnekte ayrıca aynı bölümdeki bir departman varlığı ve ilgili çalışan varlıkları da gösterilmektedir. Farklı varlık türleri için farklı bölümler, tablolar veya hatta depolama hesapları kullanmayı seçebilirsiniz.  

Aşağıdaki örnekte gösterildiği gibi, verilerinizi benimseme ve yalnızca daha fazla çalışan bölüm verilerine sahip çalışan varlıklarını depolamaya yönelik alternatif bir yaklaşım vardır. Bu senaryoda, bir departman yöneticisinin ayrıntılarını değiştirebilmeniz için gerekli olan bu yaklaşım en iyi olmayabilir. bu nedenle, departmandaki her çalışanı güncelleştirmeniz gerekir.  

![Çalışan varlığı][2]

Daha fazla bilgi için bu kılavuzun [ilerleyen kısımlarında daha sonra, bkz](#denormalization-pattern) .  

Aşağıdaki tabloda, bir-çok ilişkisine sahip olan çalışan ve departman varlıklarını depolamak için yukarıda özetlenen yaklaşımların her birinin profesyonelleri ve dezavantajları özetlenmektedir. Ayrıca, çeşitli işlemleri gerçekleştirmek için ne kadar sıklıkla beklediğinizi de göz önünde bulundurmanız gerekir: Bu işlem yalnızca seyrek olursa pahalı bir işlem içeren bir tasarıma sahip olmak için kabul edilebilir.  

<table>
<tr>
<th>Uygulanabilecek</th>
<th>Ları</th>
<th>Simgeler</th>
</tr>
<tr>
<td>Ayrı varlık türleri, aynı bölüm, aynı tablo</td>
<td>
<ul>
<li>Bir departman varlığını tek bir işlemle güncelleştirebilirsiniz.</li>
<li>Bir çalışan varlığını güncelleştirdiğinizde/eklediğinizde/sildiğinizde bir departman varlığını değiştirme gereksinimine sahipseniz tutarlılığı sürdürmek için EGT kullanabilirsiniz. Örneğin, her departman için bir departman çalışan sayısı bulundurursunuz.</li>
</ul>
</td>
<td>
<ul>
<li>Bazı istemci etkinlikleri için hem bir çalışan hem de bir departman varlığı almanız gerekebilir.</li>
<li>Aynı bölümde depolama işlemleri gerçekleşecektir. Yüksek işlem birimlerinde bu durum bir etkin nokta ile sonuçlanabilir.</li>
<li>Bir çalışanı EGT kullanarak yeni bir departmana taşıyamazsınız.</li>
</ul>
</td>
</tr>
<tr>
<td>Varlık türlerini, farklı bölümleri veya tabloları ya da depolama hesaplarını ayırın</td>
<td>
<ul>
<li>Bir departman varlığını veya çalışan varlığını tek bir işlemle güncelleştirebilirsiniz.</li>
<li>Yüksek işlem birimlerinde bu, yükü daha fazla bölüme yaymaya yardımcı olabilir.</li>
</ul>
</td>
<td>
<ul>
<li>Bazı istemci etkinlikleri için hem bir çalışan hem de bir departman varlığı almanız gerekebilir.</li>
<li>Bir çalışanı güncelleştirdiğinizde/eklediğinizde/sildiğinizde ve bir departmanı güncelleştirdiğinizde tutarlılığı sağlamak için Yumurtları kullanamazsınız. Örneğin, bir departman varlığındaki çalışan sayısını güncelleştirme.</li>
<li>Bir çalışanı EGT kullanarak yeni bir departmana taşıyamazsınız.</li>
</ul>
</td>
</tr>
<tr>
<td>Tek bir varlık türüne göre Normalleştirilmemiş</td>
<td>
<ul>
<li>Tek bir istek ile ihtiyacınız olan tüm bilgileri alabilirsiniz.</li>
</ul>
</td>
<td>
<ul>
<li>Bölüm bilgilerini güncelleştirmeniz gerekiyorsa tutarlılığı sağlamak pahalı olabilir (Bu, bir departmandaki tüm çalışanları güncelleştirmeniz gerekir).</li>
</ul>
</td>
</tr>
</table>

Bu seçenekler arasında seçim yapma ve uzmanlarının ve dezavantajlarının en önemli olduğu, belirli uygulama senaryolarınıza bağlıdır. Örneğin, departman varlıklarını ne sıklıkla değiştirirsiniz? Tüm çalışan sorgularınızın ek departman bilgileri olması gerekir; bölümlerinizde veya depolama hesabınızda ölçeklenebilirlik limitleriniz ne kadar yakınlanıyor?  

### <a name="one-to-one-relationships"></a>Bire bir ilişkiler
Etki alanı modelleri, varlıklar arasında bire bir ilişki içerebilir. Tablo hizmetinde bire bir ilişki uygulamanız gerekiyorsa, her ikisini de almanız gerektiğinde iki ilişkili varlığı bağlamayı da tercih etmeniz gerekir. Bu bağlantı, anahtar değerlerindeki bir kurala göre örtük veya bir bağlantıyı, her varlıktaki **partitionkey** ve **rowkey** değerleri ile ilgili varlığına depolayarak açık olabilir. Aynı bölümde ilgili varlıkların depolanması gerekip gerekmediğini öğrenmek için, [tek-çok ilişkileri](#one-to-many-relationships)bölümüne bakın.  

Ayrıca, tablo hizmetinde bire bir ilişkiler uygulamanıza yol açabilecek uygulama konuları da vardır:  

* Büyük varlıkları işleme (daha fazla bilgi için bkz. [büyük varlıklar kalıbı](#large-entities-pattern)).  
* Erişim denetimleri uygulama (daha fazla bilgi için bkz. [paylaşılan erişim imzaları ile erişimi denetleme](#controlling-access-with-shared-access-signatures)).  

### <a name="join-in-the-client"></a>İstemciye ekleme
Tablo hizmetindeki ilişkileri modellemenin yolları olsa da, tablo hizmetini kullanmanın iki ana nedeni ölçeklenebilirlik ve performans olarak unutmamalıdır. Çözümünüzün performansını ve ölçeklenebilirliğini tehlikeye atabilecek birçok ilişki modellebileceğinizi fark ederseniz, tüm veri ilişkilerini tablo tasarımınızda oluşturmak için gerekliyse kendinize danışmalısınız. İstemci uygulamanızın gerekli birleştirmeleri gerçekleştirmesini istiyorsanız, tasarımı basitleştirebiliyor ve çözümünüzün ölçeklenebilirliğini ve performansını geliştirebilirsiniz.  

Örneğin, sık değişmeyecek verileri içeren küçük tablolar varsa, bu verileri bir kez alabilir ve istemcide önbelleğe alabilirsiniz. Bu, aynı verileri almak için yinelenen gidiş dönüşlerini önleyebilir. Bu kılavuzda bakdığımız örneklerde, küçük bir kuruluştaki bölüm kümesinin küçük olması büyük olasılıkla, istemci uygulamanın bir kez ve arama verileri olarak önbelleğe aldığı veriler için iyi bir aday hale getirilmesi olasıdır.  

### <a name="inheritance-relationships"></a>Devralma ilişkileri
İstemci uygulamanız, iş varlıklarını temsil etmek üzere devralma ilişkisinin bir parçasını oluşturan bir sınıf kümesi kullanıyorsa, bu varlıkları tablo hizmetinde kolayca kalıcı hale getirebilirsiniz. Örneğin, bir **kişinin** soyut bir sınıf olduğu istemci uygulamanızda tanımlanmış aşağıdaki sınıf kümesine sahip olabilirsiniz.

![Devralma ilişkilerinin ER diyagramı][3]

Aşağıdaki gibi görünen varlıkları kullanarak tek bir kişi tablosu kullanarak tablo hizmetindeki iki somut sınıfın örneklerini kalıcı hale getirebilirsiniz:  

![Müşteri varlığının ve çalışan varlığının diyagramı][4]

İstemci kodunda aynı tabloda birden fazla varlık türüyle çalışma hakkında daha fazla bilgi için bu kılavuzun ilerleyen kısımlarında bulunan [heterojen varlık türleriyle çalışma](#working-with-heterogeneous-entity-types) bölümüne bakın. Bu, istemci kodundaki varlık türünün nasıl tanınacağını örnekler sağlar.  

## <a name="table-design-patterns"></a>Tablo tasarım desenleri
Önceki bölümlerde, sorgu kullanarak varlık verilerini almak ve varlık verilerini eklemek, güncelleştirmek ve silmek için tablo tasarımınızın nasıl iyileştirileceği hakkında ayrıntılı tartışmalar gördünüz. Bu bölümde, tablo hizmeti çözümleriyle kullanım için uygun bazı desenler açıklanmaktadır. Buna ek olarak, bu kılavuzda daha önce ortaya çıkan bazı sorunlar ve dengelemeler için nasıl çözüm kullanabileceğinizi öğreneceksiniz. Aşağıdaki diyagramda, farklı desenler arasındaki ilişkiler özetlenmektedir:  

![Tablo Tasarım desenlerinin görüntüsü][5]

Yukarıdaki desen haritasında, bu kılavuzda belgelenen desenler (mavi) ve kenar desenleri (turuncu) arasındaki bazı ilişkiler vurgulanmıştır. Dikkate değer veren birçok farklı desen vardır. Örneğin, tablo hizmeti için temel senaryolardan biri, [komut sorgu sorumluluğu ayırma (CQRS)](https://msdn.microsoft.com/library/azure/jj554200.aspx) deseninin [gerçekleştirilmiş görünüm modelini](https://msdn.microsoft.com/library/azure/dn589782.aspx) kullanmaktır.  

### <a name="intra-partition-secondary-index-pattern"></a>Bölüm içi ikincil dizin kalıbı
Farklı **rowkey değerleri kullanarak** hızlı ve verimli aramalar ve alternatif sıralama emirlerini etkinleştirmek için, farklı **rowkey** değerleri kullanarak her bir varlığın birden çok kopyasını depolayın (aynı bölümde). Kopyalar arasındaki güncelleştirmeler, yumurtalar kullanılarak tutarlı tutulabilir.  

#### <a name="context-and-problem"></a>Bağlam ve sorun
Tablo hizmeti, **partitionkey** ve **rowkey** değerlerini kullanarak varlıkları otomatik olarak dizine ekler. Bu, bir istemci uygulamanın bu değerleri kullanarak bir varlığı etkin bir şekilde almasını sağlar. Örneğin, aşağıda gösterilen tablo yapısını kullanarak, bir istemci uygulaması, departman adı ve çalışan kimliği ( **partitionkey** ve **rowkey** değerleri) kullanarak bireysel bir çalışan varlığı almak için bir nokta sorgusu kullanabilir. Ayrıca, bir istemci her bir departman içindeki çalışan kimliğine göre sıralanmış varlıkları alabilir.

![Çalışan varlığı][6]

Ayrıca, e-posta adresi gibi başka bir özelliğin değerine bağlı olarak bir çalışan varlığı bulabilmek istiyorsanız, bir eşleşme bulmak için daha az verimli bir bölüm taraması kullanmanız gerekir. Bunun nedeni, tablo hizmetinin ikincil dizinler sağlamasunmamalıdır. Ayrıca, bir çalışan listesini **Rowkey** sırasına göre farklı bir sırada sıralanmış olarak isteme seçeneği yoktur.  

#### <a name="solution"></a>Çözüm
İkincil dizinlerin eksikliğine geçici bir çözüm için, her bir varlığın birden çok kopyasını her bir kopya ile farklı bir **Rowkey** değeri kullanarak saklayabilirsiniz. Aşağıda gösterilen yapılarla bir varlık depoluysanız, e-posta adresini veya çalışan kimliğini temel alarak çalışan varlıklarını etkin bir şekilde alabilirsiniz. **Rowkey**, "empid_" ve "email_" için önek değerleri, bir dizi e-posta adresini veya çalışan kimliklerini kullanarak tek bir çalışan veya bir dizi çalışanı sorgulamanızı sağlar.  

![Değişen RowKey değerleri olan çalışan varlık][7]

Aşağıdaki iki filtre ölçütü (bir çalışan kimliği ve bir e-posta adresine göre arama), her ikisi de nokta sorguları belirler:  

* $filter = (PartitionKey EQ ' Sales ') ve (RowKey EQ ' empid_000223 ')  
* $filter = (PartitionKey EQ ' Sales ') ve (RowKey EQ 'email_jonesj@contoso.com')  

Bir dizi çalışan varlığı için sorgulama yaparsanız, **Rowkey**içinde uygun öneki olan varlıkları sorgulayarak, çalışan kimliği sırasında sıralanmış bir Aralık veya e-posta adresi sırasında sıralanmış bir Aralık belirleyebilirsiniz.  

* Satış departmanındaki tüm çalışanları 000100:000199 ' de aralığında bir çalışan kimliği ile bulmak için: $filter = (PartitionKey EQ ' Sales ') ve (rowkey Ge ' empid_000100 ') ve (RowKey Le ' empid_000199 ')  
* Satış Departmanında, ' a ' kullanımı ile başlayan bir e-posta adresine sahip tüm çalışanları bulmak için: $filter = (PartitionKey EQ ' Sales ') ve (rowkey Ge ' email_a ') ve (RowKey lt ' email_b ')  
  
  Yukarıdaki örneklerde kullanılan filtre sözdizimi tablo hizmeti REST API. daha fazla bilgi için bkz. [Sorgu varlıkları](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Bu düzenin nasıl uygulanacağına karar verirken aşağıdaki noktaları göz önünde bulundurun:  

* Tablo Depolaması görece bir şekilde kullanılmak üzere, yinelenen verileri depolamanın maliyet ek yükünün önemli bir sorun olmaması gerekir. Bununla birlikte, tahmini depolama gereksinimlerinize göre tasarımınızın maliyetini her zaman değerlendirin ve yalnızca, istemci uygulamanızın yürütüleceği sorguları destekleyecek şekilde yinelenen varlıklar ekleyebilirsiniz.  
* İkincil dizin varlıkları özgün varlıklarla aynı bölümde depolandığından, tek bir bölüm için ölçeklenebilirlik hedeflerini aşmamak zorunda olmadığınızdan emin olmanız gerekir.  
* Varlığın iki kopyasını otomatik olarak güncelleştirmek için Yumurtları kullanarak, yinelenen varlıklarınızı birbirleriyle tutarlı tutabilirsiniz. Bu, bir varlığın tüm kopyalarını aynı bölümde depolamanız gerektiğini gösterir. Daha fazla bilgi için [varlık grubu Işlemlerini kullanma](#entity-group-transactions)bölümüne bakın.  
* **Rowkey** için kullanılan değer her varlık için benzersiz olmalıdır. Bileşik anahtar değerlerini kullanmayı düşünün.  
* **Rowkey** 'teki sayısal değerleri doldurma (örneğin, çalışan kimliği 000223), üst ve alt sınırlara göre doğru sıralamayı ve filtrelemeyi sunar.  
* Varlığınızın tüm özelliklerini yinelemek zorunda değilsiniz. Örneğin, **Rowkey** içindeki e-posta adresini kullanan varlıkların aranacağı sorguların, çalışanın kullanım ömrü hiçbir zaman gerekmiyorsa, bu varlıklar aşağıdaki yapıya sahip olabilir:

![Çalışan varlığı][8]

* Yinelenen verileri depolamak ve bir varlık bulmak için bir sorgu kullanmak ve gerekli verileri aramak için tek bir sorgu ile ihtiyacınız olan tüm verileri almanızı sağlamak genellikle daha iyidir.  

#### <a name="when-to-use-this-pattern"></a>Bu düzenin kullanılacağı durumlar
Bu düzeni, istemci uygulamanızın çeşitli farklı anahtarlar kullanarak varlıkları alması gerektiğinde, istemcinizin farklı sıralama emirlerindeki varlıkları alması gerektiğinde ve her bir varlığı çeşitli benzersiz değerler kullanarak tanımlayabilmeniz gerektiğinde kullanın. Ancak, farklı **Rowkey** değerlerini kullanarak varlık aramaları gerçekleştirirken bölüm ölçeklenebilirlik sınırlarını aştığınızdan emin olmalısınız.  

#### <a name="related-patterns-and-guidance"></a>İlgili düzenler ve kılavuzlar
Bu düzeni uygularken aşağıdaki düzenler ve yönergeler de yararlı olabilir:  

* [Bölümler arası ikincil dizin kalıbı](#inter-partition-secondary-index-pattern)
* [Bileşik anahtar stili](#compound-key-pattern)
* [Varlık grubu Işlemleri](#entity-group-transactions)
* [Heterojen varlık türleriyle çalışma](#working-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>Bölümler arası ikincil dizin kalıbı
Farklı **rowkey değerleri kullanarak** , hızlı ve verimli aramalar ve alternatif sıralama emirlerini etkinleştirmek üzere ayrı bölümlerde veya ayrı tablolarda farklı **rowkey** değerleri kullanarak her bir varlığın birden çok kopyasını saklayın.  

#### <a name="context-and-problem"></a>Bağlam ve sorun
Tablo hizmeti, **partitionkey** ve **rowkey** değerlerini kullanarak varlıkları otomatik olarak dizine ekler. Bu, bir istemci uygulamanın bu değerleri kullanarak bir varlığı etkin bir şekilde almasını sağlar. Örneğin, aşağıda gösterilen tablo yapısını kullanarak, bir istemci uygulaması, departman adı ve çalışan kimliği ( **partitionkey** ve **rowkey** değerleri) kullanarak bireysel bir çalışan varlığı almak için bir nokta sorgusu kullanabilir. Ayrıca, bir istemci her bir departman içindeki çalışan kimliğine göre sıralanmış varlıkları alabilir.  

![Çalışan varlığı][9]

Ayrıca, e-posta adresi gibi başka bir özelliğin değerine bağlı olarak bir çalışan varlığı bulabilmek istiyorsanız, bir eşleşme bulmak için daha az verimli bir bölüm taraması kullanmanız gerekir. Bunun nedeni, tablo hizmetinin ikincil dizinler sağlamasunmamalıdır. Ayrıca, bir çalışan listesini **Rowkey** sırasına göre farklı bir sırada sıralanmış olarak isteme seçeneği yoktur.  

Bu varlıklara yönelik yüksek hacimli işlemler benimsemeyi bekleme ve istemcinizi sınırlayan tablo hizmeti oranının riskini en aza indirmek istiyorsunuz.  

#### <a name="solution"></a>Çözüm
İkincil dizinlerin eksikliğine geçici bir çözüm bulmak için, her bir varlığın farklı **partitionkey** ve **rowkey** değerlerini kullanarak her bir kopyanın birden çok kopyasını saklayabilirsiniz. Aşağıda gösterilen yapılarla bir varlık depoluysanız, e-posta adresini veya çalışan kimliğini temel alarak çalışan varlıklarını etkin bir şekilde alabilirsiniz. "Empid_" ve "email_" **Partitionkey**için önek değerleri, bir sorgu için kullanmak istediğiniz dizini tanımlamanızı sağlar.  

![İkincil dizinli birincil dizine ve çalışan varlığına sahip çalışan varlık][10]

Aşağıdaki iki filtre ölçütü (bir çalışan kimliği ve bir e-posta adresine göre arama), her ikisi de nokta sorguları belirler:  

* $filter = (PartitionKey EQ ' empid_Sales ') ve (RowKey EQ ' 000223 ')
* $filter = (PartitionKey EQ ' email_Sales ') ve (RowKey EQ 'jonesj@contoso.com')  

Bir dizi çalışan varlığı için sorgulama yaparsanız, **Rowkey**içinde uygun öneki olan varlıkları sorgulayarak, çalışan kimliği sırasında sıralanmış bir Aralık veya e-posta adresi sırasında sıralanmış bir Aralık belirleyebilirsiniz.  

* Satış departmanındaki tüm çalışanları, çalışan kimliği sırasına göre **000100** ile **000199** arasında bir çalışan kimliği ile bulmak için: $Filter = (partitionkey EQ ' empid_Sales ') ve (rowkey Ge ' 000100 ') ve (rowkey Le ' 000199 ')  
* Satış departmanındaki tüm çalışanları e-posta adresi sırası kullanımı ' a ' ile başlayan bir e-posta adresiyle bulmak için: $filter = (PartitionKey EQ ' email_Sales ') ve (RowKey Ge ' a ') ve (RowKey lt ' b ')  

Yukarıdaki örneklerde kullanılan filtre sözdiziminin tablo hizmeti REST API olduğunu unutmayın. daha fazla bilgi için bkz. [Sorgu varlıkları](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Bu düzenin nasıl uygulanacağına karar verirken aşağıdaki noktaları göz önünde bulundurun:  

* Birincil ve ikincil dizin varlıklarını sürdürmek için [sürekli tutarlı işlemler modelini](#eventually-consistent-transactions-pattern) kullanarak, yinelenen varlıklarınızın birbirleriyle sürekli tutarlı kalmasını sağlayabilirsiniz.  
* Tablo Depolaması görece bir şekilde kullanılmak üzere, yinelenen verileri depolamanın maliyet ek yükünün önemli bir sorun olmaması gerekir. Bununla birlikte, tahmini depolama gereksinimlerinize göre tasarımınızın maliyetini her zaman değerlendirin ve yalnızca, istemci uygulamanızın yürütüleceği sorguları destekleyecek şekilde yinelenen varlıklar ekleyebilirsiniz.  
* **Rowkey** için kullanılan değer her varlık için benzersiz olmalıdır. Bileşik anahtar değerlerini kullanmayı düşünün.  
* **Rowkey** 'teki sayısal değerleri doldurma (örneğin, çalışan kimliği 000223), üst ve alt sınırlara göre doğru sıralamayı ve filtrelemeyi sunar.  
* Varlığınızın tüm özelliklerini yinelemek zorunda değilsiniz. Örneğin, **Rowkey** içindeki e-posta adresini kullanarak varlıkları arayan sorguların, çalışanın kullanım ömrü hiçbir zaman ihtiyacı yoksa, bu varlıklar aşağıdaki yapıya sahip olabilir:
  
  ![İkincil dizinli çalışan varlığı][11]
* Yinelenen verileri depolamak ve tek bir sorgu ile ihtiyacınız olan tüm verileri, ikincil dizini kullanarak bir varlığı bulmak için bir sorgu kullanmaktan, diğeri ise birincil dizinde gerekli verileri aramak için bir sorgu kullanmak zorunda olduğunuzdan emin olmak genellikle daha iyidir.  

#### <a name="when-to-use-this-pattern"></a>Bu düzenin kullanılacağı durumlar
Bu düzeni, istemci uygulamanızın çeşitli farklı anahtarlar kullanarak varlıkları alması gerektiğinde, istemcinizin farklı sıralama emirlerindeki varlıkları alması gerektiğinde ve her bir varlığı çeşitli benzersiz değerler kullanarak tanımlayabilmeniz gerektiğinde kullanın. Farklı **Rowkey** değerlerini kullanarak varlık aramaları gerçekleştirirken bölüm ölçeklenebilirlik sınırlarını aşmamak istiyorsanız bu stili kullanın.  

#### <a name="related-patterns-and-guidance"></a>İlgili düzenler ve kılavuzlar
Bu düzeni uygularken aşağıdaki düzenler ve yönergeler de yararlı olabilir:  

* [Sonuçta tutarlı işlem kriteri](#eventually-consistent-transactions-pattern)  
* [Bölüm içi ikincil dizin kalıbı](#intra-partition-secondary-index-pattern)  
* [Bileşik anahtar stili](#compound-key-pattern)  
* [Varlık grubu Işlemleri](#entity-group-transactions)  
* [Heterojen varlık türleriyle çalışma](#working-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>Sonuçta tutarlı işlem kriteri
Azure kuyruklarını kullanarak bölüm sınırları veya depolama sistemi sınırları genelinde sonuçta tutarlı davranışı etkinleştirin.  

#### <a name="context-and-problem"></a>Bağlam ve sorun
Yumurtları aynı bölüm anahtarını paylaşan birden çok varlıkta atomik işlemleri etkinleştirir. Performans ve ölçeklenebilirlik nedenleriyle, tutarlılık gereksinimlerine sahip varlıkları ayrı bölümlerde veya ayrı bir depolama sisteminde depolamaya karar verebilirsiniz: böyle bir senaryoda, tutarlılığı sağlamak için Yumurtları kullanamazsınız. Örneğin, arasında son tutarlılığı sürdürmek için bir gereksiniminize sahip olabilirsiniz:  

* Aynı tabloda, farklı tablolarda veya farklı depolama hesaplarında bulunan iki farklı bölümde depolanan varlıklar.  
* Tablo hizmetinde depolanan bir varlık ve BLOB hizmetinde depolanan bir BLOB.  
* Tablo hizmetinde depolanan bir varlık ve dosya sistemindeki bir dosya.  
* Azure Bilişsel Arama hizmeti kullanılarak tablo hizmetindeki bir varlık deposu henüz dizinlenmiştir.  

#### <a name="solution"></a>Çözüm
Azure kuyruklarını kullanarak, iki veya daha fazla bölüm veya depolama sisteminde nihai tutarlılık sağlayan bir çözüm uygulayabilirsiniz.
Bu yaklaşımı göstermek için eski çalışan varlıklarını arşivlemek için bir gereksinimin olduğunu varsayalım. Eski çalışan varlıkları nadiren sorgulanır ve geçerli çalışanlarla ilgilenen etkinliklerden çıkarılmalıdır. Bu gereksinimi uygulamak için **geçerli** tablodaki etkin çalışanları ve **Arşiv** tablosunda eski çalışanları depolarlar. Bir çalışanın arşivlenmesi için, varlığı **geçerli** tablodan silmeniz ve varlığı **Arşiv** tablosuna eklemeniz gerekir, ancak bu iki işlemi gerçekleştirmek için EGT kullanamazsınız. Bir başarısızlığın bir varlığın her iki tabloda veya hiç tablo halinde görünmesine neden olması riskini önlemek için Arşiv işleminin sonunda tutarlı olması gerekir. Aşağıdaki sıra diyagramı bu işlemdeki adımları özetler. Aşağıdaki metinde özel durum yolları için daha fazla ayrıntı sağlanır.  

![Nihai tutarlılık için çözüm diyagramı][12]

İstemci, Azure kuyruğuna bir ileti yerleştirerek, çalışan #456 arşivlemek için bu örnekte arşiv işlemini başlatır. Bir çalışan rolü yeni iletiler için kuyruğu yoklar; bir tane bulduğunda iletiyi okur ve kuyrukta gizli bir kopya bırakır. Çalışan rolü, **geçerli** tablodaki varlığın bir kopyasını getirir, **Arşiv** tablosuna bir kopya ekler ve ardından **geçerli** tablodan orijinali siler. Son olarak, önceki adımlardan bir hata yoksa, çalışan rolü gizli iletiyi kuyruktan siler.  

Bu örnekte, 4. adım çalışanı **Arşiv** tablosuna ekler. Bu, çalışanı blob hizmetindeki bir bloba veya bir dosya sistemindeki bir dosyaya ekleyebilir.  

#### <a name="recovering-from-failures"></a>Hatalardan kurtarma
**4** ve **5** . adımlarda gerçekleştirilen işlemlerin, çalışan rolünün arşiv işlemini yeniden başlatması gereken durumlarda *ıdempotent* olması önemlidir. Tablo hizmetini kullanıyorsanız, **4** . adım için "Ekle veya Değiştir" işlemini kullanmanız gerekir. **5** . adımda, kullanmakta olduğunuz istemci kitaplığındaki "varsa sil" işlemini kullanmanız gerekir. Başka bir depolama sistemi kullanıyorsanız, uygun bir ıdempotent işlemi kullanmanız gerekir.  

Çalışan rolü hiçbir zaman **6**. adımı tamamlarsa, bir zaman aşımından sonra ileti tekrar işlemeyi denemek için çalışan rolü için ayrılan sırada yeniden görüntülenir. Çalışan rolü, sıradaki bir iletinin kaç kez okunduğunu denetleyebilir ve gerekirse, bu iletiyi ayrı bir kuyruğa göndererek araştırma için bir "Poison" mesajı olduğunu işaret edebilir. Sıra iletilerini okuma ve sıradan çıkarma sayısını denetleme hakkında daha fazla bilgi için bkz. [Iletileri alma](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Geçici hatalar tablo ve kuyruk hizmetlerine bazı hataları olan ve istemci uygulamanızı bunları işlemesi için uygun bir yeniden deneme mantığı içermelidir.  

#### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Bu düzenin nasıl uygulanacağına karar verirken aşağıdaki noktaları göz önünde bulundurun:  

* Bu çözüm, işlem yalıtımı sağlamaz. Örneğin, bir istemci, çalışan rolü **4** ve **5**. adım arasında olduğunda **geçerli** ve **Arşiv** tablolarını okuyabilir ve verilerin tutarsız bir görünümünü görürsünüz. Veriler sonunda tutarlı olacaktır.  
* Nihai tutarlılığı sağlamak için 4 ve 5. adımlarda ıdempotent olduğundan emin olmanız gerekir.  
* Birden çok kuyruk ve çalışan rolü örneği kullanarak çözümü ölçeklendirebilirsiniz.  

#### <a name="when-to-use-this-pattern"></a>Bu düzenin kullanılacağı durumlar
Farklı bölümlerde veya tablolarda bulunan varlıklar arasında nihai tutarlılığı garantilemek istediğinizde bu stili kullanın. Tablo hizmeti ve BLOB hizmeti ve veritabanı ya da dosya sistemi gibi diğer Azure dışı depolama veri kaynakları arasındaki işlemlerin nihai tutarlılığını sağlamak için bu kalıbı genişletebilirsiniz.  

#### <a name="related-patterns-and-guidance"></a>İlgili düzenler ve kılavuzlar
Bu düzeni uygularken aşağıdaki düzenler ve yönergeler de yararlı olabilir:  

* [Varlık grubu Işlemleri](#entity-group-transactions)  
* [Birleştir veya Değiştir](#merge-or-replace)  

> [!NOTE]
> Çözümünüz için işlem yalıtımı önemliyse, Yumurtları kullanmanıza olanak tanımak için tablolarınızı yeniden tasarlamaya göz önünde bulundurmanız gerekir.  
> 
> 

### <a name="index-entities-pattern"></a>Dizin varlıkları kalıbı
Varlık listeleri döndüren etkili aramaları etkinleştirmek için Dizin varlıklarını koruyun.  

#### <a name="context-and-problem"></a>Bağlam ve sorun
Tablo hizmeti, **partitionkey** ve **rowkey** değerlerini kullanarak varlıkları otomatik olarak dizine ekler. Bu, bir istemci uygulamanın bir nokta sorgusu kullanarak bir varlığı etkin bir şekilde almasını sağlar. Örneğin, aşağıda gösterilen tablo yapısını kullanarak, bir istemci uygulaması departman adını ve çalışan kimliğini ( **partitionkey** ve **rowkey**) kullanarak bireysel bir çalışan varlığını verimli bir şekilde alabilir.  

![Çalışan varlığı][13]

Ayrıca, son adı gibi, benzersiz olmayan başka bir özelliğin değerine göre çalışan varlıklarının bir listesini almak isterseniz, bunları doğrudan aramak için bir dizin kullanmak yerine eşleşmeleri bulmak için daha az verimli bir bölüm taraması kullanmanız gerekir. Bunun nedeni, tablo hizmetinin ikincil dizinler sağlamasunmamalıdır.  

#### <a name="solution"></a>Çözüm
Yukarıda gösterilen varlık yapısıyla, son ada göre aramayı etkinleştirmek için, çalışan kimliklerinin listesini korumanız gerekir. Can gibi belirli bir soyadı olan çalışan varlıklarını almak isterseniz, ilk adı olarak Jones ile çalışanlar için çalışan kimlikleri listesini bulmanız ve ardından bu çalışan varlıklarını almanız gerekir. Çalışan kimliklerinin listesini depolamak için üç ana seçenek vardır:  

* Blob depolamayı kullanın.  
* Dizin varlıklarını, çalışan varlıklarıyla aynı bölümde oluşturun.  
* Ayrı bir bölümde veya tabloda Dizin varlıkları oluşturun.  

<u>Seçenek #1: blob depolamayı kullanma</u>  

İlk seçenek için, her benzersiz soyadı için bir blob oluşturun ve her bir blob 'da bu soyadı olan çalışanların **Partitionkey** (departman) ve **rowkey** (çalışan kimliği) değerlerinin bir listesini depolar. Bir çalışan eklediğinizde veya sildiğinizde, ilgili Blob içeriğinin son olarak çalışan varlıklarıyla tutarlı olduğundan emin olmanız gerekir.  

<u>Seçenek #2:</u> Aynı bölümde Dizin varlıkları oluşturma  

İkinci seçenek için aşağıdaki verileri depolayan Dizin varlıklarını kullanın:  

![Aynı soyadı taşıyan çalışan kimliklerinin bir listesini içeren dize içeren çalışan varlık][14]

**Employeeids** özelliği, **rowkey**içinde depolanan son ada sahip çalışanlar için çalışan kimliklerinin bir listesini içerir.  

Aşağıdaki adımlarda, ikinci seçeneği kullanıyorsanız yeni bir çalışan eklerken izlemeniz gereken işlem ana hatlarıyla verilmiştir. Bu örnekte, kimliği 000152 olan bir çalışan ve satış departmanında soyadı Jones olan bir ad veriyoruz:  

1. "Sales" **partitionkey** değerine ve "Jones" **rowkey** değerine sahip dizin varlığını alın. 2\. adımda kullanmak üzere bu varlığın ETag öğesini kaydedin.  
2. Yeni çalışan varlığını (**partitionkey** value "Sales" ve **rowkey** değeri "000152") ekleyen bir varlık grubu işlemi (bir toplu işlem) oluşturun ve Dizin varlığını (**partitionkey** değeri "Sales" ve **rowkey) güncelleştirir** "can"), yeni çalışan kimliğini Employeıdds alanındaki listeye ekleyerek. Varlık grubu işlemleri hakkında daha fazla bilgi için bkz. [varlık grubu işlemleri](#entity-group-transactions).  
3. Bir iyimser eşzamanlılık hatası nedeniyle varlık grubu işlemi başarısız olursa (başka biri dizin varlığını değiştirdi), adım 1 ' de baştan başlamanız gerekir.  

İkinci seçeneği kullanıyorsanız, bir çalışanı silmek için benzer bir yaklaşım kullanabilirsiniz. Üç varlığı güncelleştiren bir varlık grubu işlemi yürütmeniz gerektiğinden, çalışanın soyadını değiştirmek biraz daha karmaşıktır: çalışan varlığı, eski soyadı için Dizin varlığı ve yeni soyadı için Dizin varlığı. Daha sonra iyimser eşzamanlılık kullanarak güncelleştirmeleri gerçekleştirmek için kullanabileceğiniz ETag değerlerini almak için herhangi bir değişiklik yapmadan önce her bir varlığı almalısınız.  

Aşağıdaki adımlar, ikinci seçeneği kullanıyorsanız, bir departmandaki belirli bir soyadı olan tüm çalışanları aramanız gerektiğinde izlemeniz gereken işlemi özetler. Bu örnekte, satış departmanında en son adı Jones olan tüm çalışanları arıyorsunuz:  

1. "Sales" **partitionkey** değerine ve "Jones" **rowkey** değerine sahip dizin varlığını alın.  
2. Employeıdds alanındaki çalışan kimlikleri listesini ayrıştırın.  
3. Bu çalışanların (e-posta adresleri gibi) her biri hakkında daha fazla bilgiye ihtiyacınız varsa, 2. adımda elde ettiğiniz çalışanlar listesinden **partitionkey** değeri "Sales" ve **rowkey** değerlerini kullanarak çalışan varlıklarının her birini alın.  

<u>Seçenek #3:</u> Ayrı bir bölümde veya tabloda Dizin varlıkları oluşturma  

Üçüncü seçenek için aşağıdaki verileri depolayan Dizin varlıklarını kullanın:  

![Aynı soyadı taşıyan çalışan kimliklerinin bir listesini içeren dize içeren çalışan varlık][15]

**Employeeids** özelliği, **rowkey**içinde depolanan son ada sahip çalışanlar için çalışan kimliklerinin bir listesini içerir.  

Üçüncü seçenekle, Dizin varlıkları çalışan varlıklarından ayrı bir bölümde bulunduğundan tutarlılığı sürdürmek için Yumurtları kullanamazsınız. Dizin varlıklarının en sonunda çalışan varlıklarıyla tutarlı olduğundan emin olmanız gerekir.  

#### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Bu düzenin nasıl uygulanacağına karar verirken aşağıdaki noktaları göz önünde bulundurun:  

* Bu çözüm, eşleşen varlıkları almak için en az iki sorgu gerektirir: bir tane, **Rowkey** değerlerinin listesini almak için Dizin varlıklarını sorgular ve ardından listedeki her varlığı almak için sorgular.  
* Tek bir varlığın en fazla 1 MB, seçenek #2 ve #3 seçeneği, çözümde, belirli bir soyadı için çalışan kimliği listesinin 1 MB 'den büyük olduğunu varsayar. Çalışan kimlikleri listesinin boyutu 1 MB 'tan büyükse, seçenek #1 kullanın ve dizin verilerini BLOB depolama alanında depolayın.  
* Seçenek #2 kullanırsanız (çalışanları ekleme ve silme işlemlerini yapmak ve bir çalışanın soyadını değiştirmek), işlem hacmi belirli bir bölümdeki ölçeklenebilirlik sınırlarına Yaklaşarsa değerlendirmeniz gerekir. Bu durumda, güncelleştirme isteklerini işlemek için kuyrukları kullanan ve Dizin varlıklarınızı çalışan varlıklarından ayrı bir bölümde depolamanıza olanak sağlayan, sonunda tutarlı bir çözüm (seçenek #1 veya seçenek #3) göz önünde bulundurmanız gerekir.  
* Bu çözümde #2 seçenek, bir departman içindeki soyadı ile aramak istediğiniz varsayılır: Örneğin, satış departmanında soyadı Jones olan çalışanların bir listesini almak istiyorsunuz. Tüm kuruluş genelinde can soyadı olan tüm çalışanları aramak istiyorsanız, #1 veya seçenek #3 seçeneğini kullanın.
* Nihai tutarlılığı sunan kuyruk tabanlı bir çözüm uygulayabilirsiniz (daha fazla ayrıntı için [sonuçta tutarlı işlem düzenine](#eventually-consistent-transactions-pattern) bakın).  

#### <a name="when-to-use-this-pattern"></a>Bu düzenin kullanılacağı durumlar
Tüm çalışanlar gibi ortak bir özellik değerini paylaşan bir varlık kümesini (can soyadı Jones olan tüm çalışanlar gibi) aramak istediğinizde bu stili kullanın.  

#### <a name="related-patterns-and-guidance"></a>İlgili düzenler ve kılavuzlar
Bu düzeni uygularken aşağıdaki düzenler ve yönergeler de yararlı olabilir:  

* [Bileşik anahtar stili](#compound-key-pattern)  
* [Sonuçta tutarlı işlem kriteri](#eventually-consistent-transactions-pattern)  
* [Varlık grubu Işlemleri](#entity-group-transactions)  
* [Heterojen varlık türleriyle çalışma](#working-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>Denormalleştirme stili
Tek bir nokta sorgusuyla ihtiyacınız olan tüm verileri almanızı sağlamak için ilgili verileri tek bir varlıkta birlikte birleştirin.  

#### <a name="context-and-problem"></a>Bağlam ve sorun
İlişkisel bir veritabanında, verileri genellikle birden çok tablodan veri alan sorgulara neden olan çoğaltmayı kaldırmak için normalleştirin. Verilerinizi Azure tablolarında normalleştirin, ilişkili verilerinizi almak için istemciden sunucuya birden çok gidiş dönüş yapmanız gerekir. Örneğin, aşağıda gösterilen tablo yapısıyla, bir departmanın ayrıntılarını almak için iki gidiş dönüş gerekir: biri, yöneticinin kimliğini içeren departman varlığını getirmek ve sonra yöneticinin ayrıntılarını bir çalışan varlığında getirmek için bir istek.  

![Departman varlığı ve çalışan varlığı][16]

#### <a name="solution"></a>Çözüm
Verileri iki ayrı varlıkta depolamak yerine, verileri yeniden oluşturup, Bölüm varlığındaki yöneticinin ayrıntılarının bir kopyasını saklayın. Örneğin:  

![Normalleştirilmemiş ve birleştirilmiş departman varlığı][17]

Bu özelliklerle depolanan departman varlıkları sayesinde, bir nokta sorgusu kullanarak bir departmanla ilgili olarak ihtiyaç duyduğunuz tüm ayrıntıları alabilirsiniz.  

#### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Bu düzenin nasıl uygulanacağına karar verirken aşağıdaki noktaları göz önünde bulundurun:  

* Bazı verileri iki kez depolamaya ilişkin maliyet ek yükü vardır. Performans avantajı (depolama hizmetine daha az istek elde edilen) genellikle depolama maliyetlerinde marguinal artışı artırır (Bu maliyet, bir departmanın ayrıntılarını getirmek için ihtiyaç duyduğunuz işlem sayısında bir azalmayla kısmen denkleştirilir ).  
* Yöneticileriyle ilgili bilgileri depolayan iki varlığın tutarlılığını korumanız gerekir. Tek bir atomik işlemde birden fazla varlığı güncelleştirmek için Yumurtları kullanarak tutarlılık sorununu işleyebilirsiniz: Bu durumda, departman varlığı ve departman yöneticisinin çalışan varlığı aynı bölümde depolanır.  

#### <a name="when-to-use-this-pattern"></a>Bu düzenin kullanılacağı durumlar
İlgili bilgileri sık sık aramanız gerektiğinde bu stili kullanın. Bu model, istemcinizin gerektirdiği verileri almak için yapması gereken sorgu sayısını azaltır.  

#### <a name="related-patterns-and-guidance"></a>İlgili düzenler ve kılavuzlar
Bu düzeni uygularken aşağıdaki düzenler ve yönergeler de yararlı olabilir:  

* [Bileşik anahtar stili](#compound-key-pattern)  
* [Varlık grubu Işlemleri](#entity-group-transactions)  
* [Heterojen varlık türleriyle çalışma](#working-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>Bileşik anahtar stili
Bir istemcinin tek nokta sorgusuyla ilgili verileri aramasını sağlamak için bileşik **Rowkey** değerlerini kullanın.  

#### <a name="context-and-problem"></a>Bağlam ve sorun
İlişkisel bir veritabanında, ilgili veri parçalarını tek bir sorgudaki istemciye döndürmek için sorgularda birleştirmeleri kullanmak doğal bir veritabanıdır. Örneğin, ilgili varlıkların bir listesini aramak ve bu çalışana yönelik verileri gözden geçirmek için çalışan kimliğini kullanabilirsiniz.  

Aşağıdaki yapıyı kullanarak, çalışan varlıklarını tablo hizmetinde depoladığını varsayın:  

![Çalışan varlığı][18]

Ayrıca, çalışanın kuruluşunuz için çalıştığı her bir yılda incelemeler ve performansla ilgili geçmiş verileri depolamanız gerekir ve bu bilgilere yıla göre erişebilmek için ihtiyacınız vardır. Bir seçenek, aşağıdaki yapıyla varlıkları depolayan başka bir tablo oluşturmaktır:  

![Çalışan gözden geçirme varlığı][19]

Bu yaklaşımda, verilerinizi tek bir istekle almanızı sağlamak için yeni varlıktaki bazı bilgileri (örneğin, ilk adı ve soyadı) çoğaltmaya karar verebilirsiniz. Ancak, bu iki varlığı otomatik olarak güncelleştirmek için EGT 'yi kullanamadığından güçlü tutarlılığı koruyamezsiniz.  

#### <a name="solution"></a>Çözüm
Aşağıdaki yapıya sahip varlıkları kullanarak özgün tablonuzda yeni bir varlık türü depolayın:  

![Bileşik anahtarla çalışan varlığı][20]

**Rowkey** 'in artık çalışan kimliğinden ve tek bir varlık için tek bir istek ile verileri incelemenize olanak sağlayan İnceleme verilerinin yılından oluşan bir bileşik anahtar olduğuna dikkat edin.  

Aşağıdaki örnek, belirli bir çalışana ait tüm gözden geçirme verilerini (örneğin, satış departmanında çalışan 000123) nasıl alacağınızı özetler:  

$filter = (PartitionKey EQ ' Sales ') ve (RowKey Ge ' empid_000123 ') ve (RowKey lt ' empid_000124 ') & $select = RowKey, yönetici derecelendirmesi, eş derecelendirme, açıklamalar  

#### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Bu düzenin nasıl uygulanacağına karar verirken aşağıdaki noktaları göz önünde bulundurun:  

* **Rowkey** değerini ayrıştırmayı kolaylaştıran uygun bir ayırıcı karakter kullanmanız gerekir: Örneğin, **000123_2012**.  
* Ayrıca, bu varlığı aynı çalışanla ilgili verileri içeren diğer varlıklarla aynı bölümde saklarsınız, bu da güçlü tutarlılığı sağlamak için Yumurtları kullanabileceğiniz anlamına gelir.
* Bu düzenin uygun olup olmadığını anlamak için verileri ne sıklıkta sorgulayacağınızı düşünmeniz gerekir.  Örneğin, İnceleme verilerine sık sık ve ana çalışan verilerine erişebiliyorsanız, bunları ayrı varlıklar olarak saklamanız gerekir.  

#### <a name="when-to-use-this-pattern"></a>Bu düzenin kullanılacağı durumlar
Sık olarak Sorgulayabileceğiniz bir veya daha fazla ilgili varlığı depolamanız gerektiğinde bu stili kullanın.  

#### <a name="related-patterns-and-guidance"></a>İlgili düzenler ve kılavuzlar
Bu düzeni uygularken aşağıdaki düzenler ve yönergeler de yararlı olabilir:  

* [Varlık grubu Işlemleri](#entity-group-transactions)  
* [Heterojen varlık türleriyle çalışma](#working-with-heterogeneous-entity-types)  
* [Sonuçta tutarlı işlem kriteri](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>Günlük kuyruk kalıbı
Ters tarih ve saat düzeninde sıralama yaparak bir **Rowkey** değeri kullanarak bir bölüme en son eklenen *n* varlıklarını alın.  

> [!NOTE]
> Azure Cosmos DB Azure Tablo API'si tarafından döndürülen sorgu sonuçları bölüm anahtarına veya satır anahtarına göre sıralanmaz. Bu nedenle, bu model Azure Cosmos DB değil, Azure Tablo depolama için uygundur. Özellik farklarının ayrıntılı bir listesi için, [Azure Cosmos DB ve Azure Tablo depolamadaki tablo API'si arasındaki farklara](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior)bakın.

#### <a name="context-and-problem"></a>Bağlam ve sorun
Yaygın bir gereksinim, en son oluşturulan varlıkları (örneğin, bir çalışan tarafından gönderilen en son gider taleplerini) alabilmelidir. Tablo sorguları bir kümeden ilk *n* varlığı döndürmek için **$top** bir sorgu işlemini destekler: bir küme içindeki son n varlıkları döndürmek için eşdeğer bir sorgu işlemi yoktur.  

#### <a name="solution"></a>Çözüm
Bir **Rowkey** kullanarak, Doğal Tarih/saat düzeninde sıralama yaparak, en son girişin her zaman tablodaki ilk bir değer olması için varlıkları saklayın.  

Örneğin, bir çalışan tarafından gönderilen en son gider taleplerini alabilmesi için, geçerli tarih/saatten türetilmiş bir ters değer değeri kullanabilirsiniz. Aşağıdaki C# kod örneğinde, en sonuncudan en eskiye doğru sıralayan bir **rowkey** için uygun bir "ters kullanım" değeri oluşturmanın bir yolu gösterilmektedir:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Aşağıdaki kodu kullanarak tarih saat değerine geri dönebilirsiniz:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

Tablo sorgusu şöyle görünür:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Bu düzenin nasıl uygulanacağına karar verirken aşağıdaki noktaları göz önünde bulundurun:  

* Dize değerinin beklenildiği şekilde sıralandığından emin olmak için ters değer değerini önünde sıfır ile ayarlamalısınız.  
* Bir bölüm düzeyinde ölçeklenebilirlik hedeflerini bilmeniz gerekir. Etkin nokta bölümleri oluşturmamaya dikkat edin.  

#### <a name="when-to-use-this-pattern"></a>Bu düzenin kullanılacağı durumlar
Varlıklara ters tarih/saat düzeninde erişmeniz gerektiğinde veya en son eklenen varlıklara erişmeniz gerektiğinde bu düzeni kullanın.  

#### <a name="related-patterns-and-guidance"></a>İlgili düzenler ve kılavuzlar
Bu düzeni uygularken aşağıdaki düzenler ve yönergeler de yararlı olabilir:  

* [Prepend/Append kenar yumuşatma](#prepend-append-anti-pattern)  
* [Varlıkları alma](#retrieving-entities)  

### <a name="high-volume-delete-pattern"></a>Yüksek hacimli silme kalıbı
Aynı anda tüm varlıkları kendi ayrı tablosunda eşzamanlı silme için depolayarak yüksek hacimli varlıkların silinmesini etkinleştirin; tabloyu silerek varlıkları silersiniz.  

#### <a name="context-and-problem"></a>Bağlam ve sorun
Birçok uygulama, artık bir istemci uygulaması için kullanılabilir olmayan eski verileri siler ya da uygulamanın başka bir depolama ortamına arşivlenmesi gerekir. Genellikle bu verileri bir tarih ile belirlersiniz: Örneğin, 60 günden daha eski olan tüm oturum açma isteklerinin kayıtlarını silme gereksinimleriniz vardır.  

Olası bir tasarım, **Rowkey**içindeki oturum açma isteğinin tarih ve saatini kullanmaktır:  

![Oturum açma denemesi varlığı][21]

Bu yaklaşım, uygulama ayrı bir bölümdeki her bir kullanıcı için oturum açma varlıkları ekleyip silebildiğinden, bölüm etkin noktalarını önler. Ancak, çok sayıda varlık varsa bu yaklaşım maliyetli ve zaman alıcı olabilir, çünkü öncelikle silinecek tüm varlıkların tanımlanması için tablo taraması gerçekleştirmeniz gerekir, sonra da her bir eski varlığı silmeniz gerekir. Birden çok Delete isteğini bölümlere ayırarak eski varlıkları silmek için gereken sunucuya gidiş dönüş sayısını azaltabilirsiniz.  

#### <a name="solution"></a>Çözüm
Oturum açma girişimlerinin her günü için ayrı bir tablo kullanın. Varlıkları eklerken etkin noktalara engel olmak için yukarıdaki varlık tasarımını kullanabilirsiniz ve eski varlıkları silmek, yüzlerce ve binlerce bireyi bulmak ve silmek yerine yalnızca bir tablonun her gün (tek bir depolama işlemi) silinmesine yönelik bir sorudır. varlıklarda her gün oturum açar.  

#### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Bu düzenin nasıl uygulanacağına karar verirken aşağıdaki noktaları göz önünde bulundurun:  

* Tasarımınız, uygulamanızın belirli varlıkları aramak, diğer verilerle bağlantı kurmak veya toplu bilgi oluşturmak gibi verileri kullanması için bu yolları destekliyor mu?  
* Yeni varlıkları eklerken tasarımınız etkin noktaları önmidir?  
* Aynı tablo adını sildikten sonra yeniden kullanmak istiyorsanız bir gecikme bekliyor. Her zaman benzersiz tablo adları kullanmak daha iyidir.  
* Tablo hizmeti erişim düzenlerini öğrenirken ve bölümleri düğümler arasında dağıttığı sırada yeni bir tablo kullandığınızda bazı hız sınırlaması beklenir. Yeni tablolar oluşturmanız gereken sıklığı göz önünde bulundurmanız gerekir.  

#### <a name="when-to-use-this-pattern"></a>Bu düzenin kullanılacağı durumlar
Aynı anda silmeniz gereken yüksek bir varlık hacminin olması durumunda bu kalıbı kullanın.  

#### <a name="related-patterns-and-guidance"></a>İlgili düzenler ve kılavuzlar
Bu düzeni uygularken aşağıdaki düzenler ve yönergeler de yararlı olabilir:  

* [Varlık grubu Işlemleri](#entity-group-transactions)
* [Varlıkları değiştirme](#modifying-entities)  

### <a name="data-series-pattern"></a>Veri serisi stili
Yaptığınız istek sayısını en aza indirmek için, tüm veri serisini tek bir varlıkta depolayın.  

#### <a name="context-and-problem"></a>Bağlam ve sorun
Yaygın bir senaryo, bir uygulamanın, genellikle hepsini bir kez alması gereken veri serisini depolaması içindir. Örneğin, uygulamanız her bir çalışanın kaç tane ım iletisi göndereceğini kaydedebilir ve sonra bu bilgileri kullanarak her bir kullanıcının önceki 24 saat içinde kaç ileti gönderdiğini çizebilirsiniz. Tek bir tasarım, her çalışan için 24 varlık depolamak olabilir:  

![İleti istatistikleri varlığı][22]

Bu tasarım sayesinde, uygulamanın ileti sayısı değerini güncelleştirmesi gerektiğinde her bir çalışana yönelik varlık için kolayca bulma ve güncelleştirme yapabilirsiniz. Bununla birlikte, önceki 24 saat için etkinliğin bir grafiğini çizmek üzere bilgileri almak için, 24 varlık almanız gerekir.  

#### <a name="solution"></a>Çözüm
Her saat için ileti sayısını depolamak üzere aşağıdaki tasarımı ayrı bir özellikle kullanın:  

![Ayrılmış özelliklerle ileti istatistikleri varlığı][23]

Bu tasarımla, bir çalışana ait ileti sayısını belirli bir saat için güncelleştirmek üzere bir birleştirme işlemi kullanabilirsiniz. Şimdi, tek bir varlık için bir istek kullanarak grafiği çizmek için ihtiyacınız olan tüm bilgileri alabilirsiniz.  

#### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Bu düzenin nasıl uygulanacağına karar verirken aşağıdaki noktaları göz önünde bulundurun:  

* Tüm veri seriniz tek bir varlığa uymuyorsa (bir varlık en fazla 252 özellik içerebilir), blob gibi alternatif bir veri deposu kullanın.  
* Bir varlığı aynı anda güncelleştiren birden fazla istemciniz varsa, iyimser eşzamanlılık uygulamak için **ETag** 'i kullanmanız gerekir. Çok sayıda istemciniz varsa, yüksek çekişme yaşayabilirsiniz.  

#### <a name="when-to-use-this-pattern"></a>Bu düzenin kullanılacağı durumlar
Tek bir varlıkla ilişkili bir veri serisini güncelleştirmeniz ve almanız gerektiğinde bu stili kullanın.  

#### <a name="related-patterns-and-guidance"></a>İlgili düzenler ve kılavuzlar
Bu düzeni uygularken aşağıdaki düzenler ve yönergeler de yararlı olabilir:  

* [Büyük varlıklar kalıbı](#large-entities-pattern)  
* [Birleştir veya Değiştir](#merge-or-replace)  
* [Sonuçta tutarlı işlem kriteri](#eventually-consistent-transactions-pattern) (veri serisini bir blob 'da depoluyorsanız)  

### <a name="wide-entities-pattern"></a>Geniş varlıklar kalıbı
252 'den fazla özelliği olan mantıksal varlıkları depolamak için birden çok fiziksel varlık kullanın.  

#### <a name="context-and-problem"></a>Bağlam ve sorun
Tek bir varlık 252 'den fazla özelliğe sahip olamaz (zorunlu sistem özellikleri hariç) ve toplamda 1 MB 'den fazla veri depolayabilirler. İlişkisel bir veritabanında, genellikle yeni bir tablo ekleyerek ve aralarında 1 ' den fazla ilişki zorunlu tutarak bir satırın boyutu için herhangi bir sınırı yuvarlayacaktır.  

#### <a name="solution"></a>Çözüm
Tablo hizmetini kullanarak, birden fazla varlığı, 252 'den fazla özelliği olan tek bir büyük ölçekli bir iş nesnesini temsil etmek üzere saklayabilirsiniz. Örneğin, son 365 gün boyunca her bir çalışan tarafından gönderilen anlık ileti iletilerinin sayısını depolamak istiyorsanız, farklı şemalarla iki varlık kullanan aşağıdaki tasarımı kullanabilirsiniz:  

![Rowkey 01 ve satır anahtarı 02 ile Ileti durumu varlığı içeren ileti istatistikleri varlığı][24]

Her iki varlığın de birbirleriyle eşitlenmiş halde tutulması için güncelleştirilmesi gereken bir değişiklik yapmanız gerekiyorsa, bir EGT kullanabilirsiniz. Aksi takdirde, belirli bir güne ait ileti sayısını güncelleştirmek için tek bir birleştirme işlemi kullanabilirsiniz. Tek bir çalışana ait tüm verileri almak için her iki varlığı de almalısınız. Bu, hem **Partitionkey** hem de **rowkey** değeri kullanan iki verimli istek ile gerçekleştirebilirsiniz.  

#### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Bu düzenin nasıl uygulanacağına karar verirken aşağıdaki noktaları göz önünde bulundurun:  

* Tüm mantıksal varlıkların alınması, en az iki depolama işlemi içerir: bir fiziksel varlığın alınması için bir tane.  

#### <a name="when-to-use-this-pattern"></a>Bu düzenin kullanılacağı durumlar
Boyutu veya özellikleri olan varlıkların, tablo hizmetindeki tek bir varlık için sınırları aşan varlıkları depolaması gerektiğinde bu stili kullanın.  

#### <a name="related-patterns-and-guidance"></a>İlgili düzenler ve kılavuzlar
Bu düzeni uygularken aşağıdaki düzenler ve yönergeler de yararlı olabilir:  

* [Varlık grubu Işlemleri](#entity-group-transactions)
* [Birleştir veya Değiştir](#merge-or-replace)

### <a name="large-entities-pattern"></a>Büyük varlıklar kalıbı
Büyük özellik değerlerini depolamak için BLOB depolama kullanın.  

#### <a name="context-and-problem"></a>Bağlam ve sorun
Tek bir varlık, toplamda 1 MB 'den fazla veri saklayabilir. Özelliklerden biri veya birkaçı, varlığınızın toplam boyutunun bu değeri aşmasına neden olan değerleri depolu, tüm varlığın tablo hizmetinde depolayamamanız gerekir.  

#### <a name="solution"></a>Çözüm
Bir veya daha fazla özellik büyük miktarda veri içerdiği için varlığınız 1 MB 'ı aşarsa, verileri blob hizmetinde saklayabilir ve sonra blob adresini varlıktaki bir özellikte saklayabilirsiniz. Örneğin, bir çalışanın fotoğrafını BLOB depolama alanında saklayabilir ve çalışan varlığınızın **fotoğraf** özelliğinde fotoğrafın bağlantısını kaydedebilirsiniz:  

![Blob depolamaya işaret eden fotoğrafın bulunduğu çalışan varlık][25]

#### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Bu düzenin nasıl uygulanacağına karar verirken aşağıdaki noktaları göz önünde bulundurun:  

* Tablo hizmetindeki varlık ve BLOB hizmetindeki veriler arasında nihai tutarlılığı sürdürmek için, varlıklarınızı sürdürmek üzere [sonuçta tutarlı işlem düzenlerini](#eventually-consistent-transactions-pattern) kullanın.
* Tüm varlıkların alınması, en az iki depolama işlemi içerir: bir varlık ve blob verilerini almak için bir tane.  

#### <a name="when-to-use-this-pattern"></a>Bu düzenin kullanılacağı durumlar
Boyutu, tablo hizmetindeki tek bir varlık için olan sınırları aşan varlıkları depolamanız gerektiğinde bu stili kullanın.  

#### <a name="related-patterns-and-guidance"></a>İlgili düzenler ve kılavuzlar
Bu düzeni uygularken aşağıdaki düzenler ve yönergeler de yararlı olabilir:  

* [Sonuçta tutarlı işlem kriteri](#eventually-consistent-transactions-pattern)  
* [Geniş varlıklar kalıbı](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>Prepend/Append kenar yumuşatma
Birçok bölüm arasında eklemeleri yayarak yüksek miktarda araya sahip olduğunuzda ölçeklenebilirliği artırın.  

#### <a name="context-and-problem"></a>Bağlam ve sorun
Depolanan varlıklarınıza ön bekliyor veya varlıklar eklemek, genellikle uygulamanın bölüm dizisinin ilk veya son bölümüne yeni varlıklar eklenmesine neden olur. Bu durumda, belirli bir zaman içindeki tüm ekler aynı bölümde gerçekleşirken, tablo hizmeti 'nin birden çok düğümde yük dengelemesi yapmasına engel olan ve muhtemelen uygulamanızın ölçeklenebilirlik hedeflerine ulaşmasına neden olan bir etkin nokta oluşturulur. bölümünüzün. Örneğin, çalışanlar tarafından ağ ve kaynak erişimini kaydeden bir uygulamanız varsa, işlem hacmi bir için ölçeklenebilirlik hedefine ulaşırsa, aşağıda gösterildiği gibi bir varlık yapısı, geçerli saatin bölümünün bir etkin noktaya dönüşmesine neden olabilir. ayrı Bölüm:  

![Çalışan varlığı][26]

#### <a name="solution"></a>Çözüm
Aşağıdaki alternatif varlık yapısı, uygulama günlüğe olay günlüğü olarak belirli bir bölümdeki etkin noktayı önler:  

![Yıl, ay, gün, saat ve olay KIMLIĞINI temel alan RowKey içeren çalışan varlık][27]

Bu örneğe, hem **Partitionkey** hem de **rowkey** 'in nasıl bileşik anahtarlar olduğuna dikkat edin. **Partitionkey** , günlüğü birden çok bölüme dağıtmak için hem departmanı hem de çalışan kimliğini kullanır.  

#### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Bu düzenin nasıl uygulanacağına karar verirken aşağıdaki noktaları göz önünde bulundurun:  

* Ekleme üzerinde etkin bölümler oluşturmayı önleyen alternatif anahtar yapısı, istemci uygulamanızın yaptığı sorguları verimli bir şekilde destekler mi?  
* Tahmini işlem hacmidir, tek bir bölüm için ölçeklenebilirlik hedeflerine ulaşmanıza ve depolama hizmeti tarafından kısıtlanacak.  

#### <a name="when-to-use-this-pattern"></a>Bu düzenin kullanılacağı durumlar
Bir sıcak bölüme eriştiğinizde, işlem hackleriniz depolama hizmeti tarafından sınırlama oranı ile sonuçlandığı zaman, ön uç/ekleme önleme örüniğinden kaçının.  

#### <a name="related-patterns-and-guidance"></a>İlgili düzenler ve kılavuzlar
Bu düzeni uygularken aşağıdaki düzenler ve yönergeler de yararlı olabilir:  

* [Bileşik anahtar stili](#compound-key-pattern)  
* [Günlük kuyruk kalıbı](#log-tail-pattern)  
* [Varlıkları değiştirme](#modifying-entities)  

### <a name="log-data-anti-pattern"></a>Günlük verileri Anti-model
Genellikle, günlük verilerini depolamak için tablo hizmeti yerine blob hizmetini kullanmanız gerekir.  

#### <a name="context-and-problem"></a>Bağlam ve sorun
Günlük verileri için yaygın kullanım örneği, belirli bir tarih/saat aralığı için günlük girişlerinin bir seçimini almadır: Örneğin, uygulamanızın 15:04 ve 15:06 arasında günlüğe kaydedildiği tüm hata ve kritik iletileri belirli bir tarihte bulmak istiyorsunuz. Günlük varlıklarının kaydedilmesini istediğiniz bölümü öğrenmek için günlük iletisinin tarih ve saatini kullanmak istemezsiniz: Bu, belirli bir zamanda, tüm günlük varlıklarının aynı **Partitionkey** değerini paylaşılacağını sağlar (bkz. [Bu bölüm, kenar yumuşatma ekleme](#prepend-append-anti-pattern)). Örneğin, bir günlük iletisi için aşağıdaki varlık şeması, uygulama tüm günlük iletilerini geçerli tarih ve saat için bölüme yazdığından, etkin bir bölüm ile sonuçlanır:  

![İleti varlığını günlüğe kaydet][28]

Bu örnekte, **Rowkey** , günlük iletilerinin tarih/saat düzeninde sıralanmış olarak depolanmasını sağlamak için günlük iletisinin tarih ve saatini içerir ve birden çok günlük iletisinin aynı tarih ve saati paylaştığı durumlarda bir ileti kimliği içerir.  

Başka bir yaklaşım, uygulamanın iletileri bir dizi bölüm arasında yazmalarını sağlayan bir **Partitionkey** kullanmaktır. Örneğin, günlük iletisinin kaynağı birçok bölüm arasında ileti dağıtmak için bir yol sağlıyorsa aşağıdaki varlık şemasını kullanabilirsiniz:  

![İleti varlığını günlüğe kaydet][29]

Bununla birlikte, bu şemayla ilgili sorun belirli bir zaman dilimi için tüm günlük iletilerini almak için tablodaki her bölümde arama yapmanız gerekir.

#### <a name="solution"></a>Çözüm
Önceki bölümde, günlük girişlerini depolamak için tablo hizmetini kullanma girişimi ve önerilen iki, yetersiz, tasarım sorunu vurgulanmıştır. Sık erişimli bir bölüme yönelik bir çözüm, günlük iletilerinin düşük performans yazma riskiyle birlikte çalışır; diğer çözüm, belirli bir zaman aralığı için günlük iletilerini almak üzere tablodaki her bölümü taramak için gerekli olan sorgu performansının düşmesine neden oldu. BLOB depolama, bu tür bir senaryo için daha iyi bir çözüm sunar ve Azure Depolama Analizi topladığı günlük verilerini depolar.  

Bu bölümde, genellikle aralığa göre sorgulayan verileri depolamak için bu yaklaşımın bir gösterimi olarak, Depolama Analizi günlük verilerini blob depolamada nasıl depolayacağı özetlenmektedir.  

Depolama Analizi, günlük iletilerini birden fazla blobda ayrılmış bir biçimde depolar. Ayrılmış biçim, bir istemci uygulamanın günlük iletisindeki verileri ayrıştırmayı kolaylaştırır.  

Depolama Analizi, arama yaptığınız günlük iletilerini içeren Blobu (veya Blobları) bulmanızı sağlayan Bloblar için bir adlandırma kuralı kullanır. Örneğin, "Queue/2014/07/31/1800/000001. log" adlı bir blob, 18:00 Temmuz 2014 tarihinde başlayan saat için kuyruk hizmetiyle ilgili günlük iletileri içerir. "000001", bu dönemin ilk günlük dosyası olduğunu gösterir. Depolama Analizi Ayrıca, blob 'un meta verilerinin bir parçası olarak dosyada depolanan ilk ve son günlük iletilerinin zaman damgalarını kaydeder. BLOB depolama için API, bir ad önekini temel alarak bir kapsayıcıdaki Blobları bulmanıza olanak sağlar: 18:00 ile başlayan saat için kuyruk günlüğü verilerini içeren tüm Blobları bulmak için, "Queue/2014/07/31/1800" önekini kullanabilirsiniz.  

Depolama Analizi günlük iletilerini dahili olarak arabelleğe alır ve uygun blobu düzenli olarak güncelleştirir veya en son günlük girişi toplu işlem ile yeni bir tane oluşturur. Bu, blob hizmetinde gerçekleştirmesi gereken yazma sayısını azaltır.  

Kendi uygulamanızda benzer bir çözüm uygularız, güvenilirlik (olduğu gibi BLOB depolamaya her günlük girişini yazma) ve maliyet ve ölçeklenebilirlik (uygulamanızdaki güncelleştirmeleri arabelleğe alma ve yazma gibi) arasında dengelemeyi yönetmeyi düşünmelisiniz Bunları toplu halde blob depolamaya atayabilirsiniz).  

#### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Günlük verilerini nasıl depolayacağınıza karar verirken aşağıdaki noktaları göz önünde bulundurun:  

* Olası sık kullanılan bölümleri engelleyen bir tablo tasarımı oluşturursanız, günlük verilerinize verimli bir şekilde erişemeyebilirsiniz.  
* Günlük verilerini işlemek için bir istemcinin genellikle birçok kayıt yüklemesi gerekir.  
* Günlük verileri genellikle yapılandırılmış olsa da, BLOB depolama daha iyi bir çözüm olabilir.  

### <a name="implementation-considerations"></a>Uygulama konuları
Bu bölümde, önceki bölümlerde açıklanan desenleri uyguladığınızda göz önünde bulundurmanız gereken bazı noktalar açıklanmaktadır. Bu bölümün çoğu, depolama Istemci kitaplığını ( C# yazma sırasında sürüm 4.3.0) kullanan içinde yazılmış örnekleri kullanır.  

### <a name="retrieving-entities"></a>Varlıkları alma
[Sorgulama için bölüm tasarımında](#design-for-querying)anlatıldığı gibi, en verimli sorgu bir nokta sorgusudur. Ancak, bazı senaryolarda birden fazla varlık almanız gerekebilir. Bu bölümde, depolama Istemci kitaplığı kullanılarak varlıkların alınması için bazı yaygın yaklaşımlar açıklanmaktadır.  

#### <a name="executing-a-point-query-using-the-storage-client-library"></a>Depolama Istemci kitaplığını kullanarak bir nokta sorgusu yürütme
Nokta sorgusu çalıştırmanın en kolay yolu, "Sales" değerli bir varlık **anahtarı** ve "212" değerli bir C# **rowkey** içeren bir varlığı alan aşağıdaki kod parçacığında gösterildiği gibi, tablo **Al** işlemini kullanmaktır:  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Bu örneğin, aldığı varlığın **employeeentity**türünde olmasını nasıl beklediğine dikkat edin.  

#### <a name="retrieving-multiple-entities-using-linq"></a>LINQ kullanarak birden çok varlık alma
Depolama Istemci kitaplığı ile LINQ kullanarak birden çok varlık alabilir ve **WHERE** yan tümcesi içeren bir sorgu belirtebilirsiniz. Tablo taramasını önlemek için WHERE yan tümcesinde **Partitionkey** değerini her zaman dahil etmeniz ve tablo ve bölüm taramalarından kaçınmak Için **rowkey** değeri olması gerekir. Tablo hizmeti WHERE yan tümcesinde kullanılmak üzere sınırlı sayıda karşılaştırma işleci (büyüktür, büyüktür veya eşittir, küçüktür, küçüktür veya eşittir, eşittir ve eşit değildir) kümesini destekler. Aşağıdaki C# kod parçacığı, son adı "B" ile başlayan tüm çalışanları bulur ( **rowkey** 'in soyadı **, Bölüm adını depoladığını** varsayarak).  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

Daha iyi performans sağlamak için sorgunun hem **Rowkey** hem de **partitionkey** 'i nasıl belirttiğinden emin olun.  

Aşağıdaki kod örneği, Fluent API kullanarak eşdeğer işlevselliği gösterir (genel olarak akıcı API 'Ler hakkında daha fazla bilgi için bkz. [akıcı BIR API tasarlamak Için En Iyi uygulamalar](https://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(
    TableQuery.CombineFilters(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition(
    "PartitionKey", QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.GenerateFilterCondition(
    "RowKey", QueryComparisons.GreaterThanOrEqual, "B")
),
TableOperators.And,
TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "C")
    )
);
var employees = employeeTable.ExecuteQuery(employeeQuery);  
```

> [!NOTE]
> Örnek, üç filtre koşullarını dahil etmek için birden çok **CombineFilters** yöntemi sağlar.  
> 
> 

#### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>Bir sorgudan çok sayıda varlık alma
En iyi sorgu, bir **Partitionkey** değerine ve **rowkey** değerine göre tek bir varlık döndürür. Ancak, bazı senaryolarda aynı bölümden veya birçok bölümden bile birçok varlık döndürme gereksinimiyle karşılaşabilirsiniz.  

Bu senaryolarda uygulamanızın performansını her zaman tam olarak test etmelisiniz.  

Tablo hizmetine yönelik bir sorgu, bir seferde en fazla 1.000 varlık döndürebilir ve en fazla beş saniye boyunca çalıştırılabilir. Sonuç kümesi 1.000 'den fazla varlık içeriyorsa, sorgu beş saniye içinde tamamlanmazsa veya sorgu Bölüm sınırını geçiyorsa, tablo hizmeti istemci uygulamanın bir sonraki varlık kümesini istemesini sağlamak için bir devamlılık belirteci döndürür. Devamlılık belirteçlerinin nasıl çalıştığı hakkında daha fazla bilgi için bkz. [sorgu zaman aşımı ve sayfalandırma](https://msdn.microsoft.com/library/azure/dd135718.aspx).  

Depolama Istemci kitaplığını kullanıyorsanız, tablo hizmetinden varlıkları döndürdüğünde, devamlılık belirteçlerini otomatik olarak işleyebilir. Aşağıdaki C# kod örneği, tablo hizmeti bunları bir yanıt halinde döndürürse, depolama istemci kitaplığı kullanılarak devam belirteçleri otomatik olarak işler:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
        ...
}  
```

Aşağıdaki C# kod, devamlılık belirteçlerini açıkça işler:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

TableContinuationToken continuationToken = null;

do
{
        var employees = employeeTable.ExecuteQuerySegmented(
        employeeQuery, continuationToken);
    foreach (var emp in employees)
    {
    ...
    }
    continuationToken = employees.ContinuationToken;
} while (continuationToken != null);  
```

Devamlılık belirteçlerini açık bir şekilde kullanarak, uygulamanızın sonraki veri segmentini ne zaman alacağını denetleyebilirsiniz. Örneğin, istemci uygulamanız kullanıcıların bir tabloda depolanan varlıklar aracılığıyla sayfa oluşturmasını sağladığından, bir Kullanıcı sorgu tarafından alınan tüm varlıklarda sayfa eklemek için, uygulamanızın bir sonraki segmenti almak için yalnızca bir devamlılık belirteci kullanmasını sağlayacak şekilde, Kullanıcı, geçerli kesimdeki tüm varlıklar aracılığıyla disk belleğini bitirdi. Bu yaklaşımın çeşitli avantajları vardır:  

* Tablo hizmetinden alınacak ve ağ üzerinden taşıdığınız veri miktarını sınırlandırmanıza olanak sağlar.  
* .NET ' te zaman uyumsuz GÇ gerçekleştirmenize olanak sağlar.  
* Uygulama kilitlenmesinin durumunda devam edebilmeniz için devamlılık belirtecini kalıcı depolama alanına serileştirmenize olanak sağlar.  

> [!NOTE]
> Devamlılık belirteci genellikle 1.000 varlık içeren bir segmenti döndürür, ancak daha az olabilir. Bu Ayrıca, arama ölçütlerinizle eşleşen ilk n varlığı döndürmek için **Take** kullanarak bir sorgunun döndürdüğü giriş sayısını sınırlamanız durumunda da olur: tablo hizmeti, bir devamlılık belirteci ile birlikte n 'den az varlık içeren bir segment döndürebilir kalan varlıkları almanızı sağlar.  
> 
> 

Aşağıdaki C# kod, bir segment içinde döndürülen varlıkların sayısının nasıl değiştirileceğini göstermektedir:  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>Sunucu tarafı projeksiyonu
Tek bir varlık en fazla 255 özelliğe sahip olabilir ve boyutu en fazla 1 MB olabilir. Tabloyu sorgulayıp varlıkları aldığınızda, tüm özelliklere ihtiyaç duymayabilir ve verilerin gereksiz yere aktarılmasını önleyebilirsiniz (gecikme süresini ve maliyeti azaltmaya yardımcı olmak için). Yalnızca ihtiyacınız olan özellikleri aktarmak için sunucu tarafı projeksiyonu kullanabilirsiniz. Aşağıdaki örnek, sorgu tarafından seçilen varlıklardan yalnızca **e-posta** özelliğini ( **partitionkey**, **Rowkey**, **timestamp**ve **ETag**ile birlikte) alır.  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
List<string> columns = new List<string>() { "Email" };
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter).Select(columns);

var entities = employeeTable.ExecuteQuery(employeeQuery);
foreach (var e in entities)
{
        Console.WriteLine("RowKey: {0}, EmployeeEmail: {1}", e.RowKey, e.Email);
}  
```

Alınacak özellikler listesine dahil edilmese de **Rowkey** değerinin nasıl kullanılabilir olduğuna dikkat edin.  

### <a name="modifying-entities"></a>Varlıkları değiştirme
Depolama Istemci kitaplığı, varlıkları ekleyerek, silerek ve güncelleştirerek tablo hizmetinde depolanan varlıklarınızı değiştirmenize olanak sağlar. Gerekli gidiş dönüş sayısını azaltmak ve çözümünüzün performansını artırmak için, birden çok ekleme, güncelleştirme ve silme işlemini bir araya toplamak üzere EGTs 'yi kullanabilirsiniz.  

Depolama Istemci kitaplığı bir EGT çalıştırdığında oluşturulan özel durumlar genellikle toplu işin başarısız olmasına neden olan varlığın dizinini içerir. Bu, Yumurtları kullanan kod hata ayıklaması yaparken yararlıdır.  

Ayrıca, tasarımınızın, istemci uygulamanızın eşzamanlılık ve güncelleştirme işlemlerini nasıl işlediğini nasıl etkilediğini de göz önünde bulundurmanız gerekir.  

#### <a name="managing-concurrency"></a>Eşzamanlılığı yönetme
Varsayılan olarak, tablo hizmeti, **ekleme**, **birleştirme**ve **silme** işlemleri için tek tek varlıkların düzeyinde iyimser eşzamanlılık denetimleri uygular, ancak bir istemcinin tablo hizmetini bunları atlayacak şekilde zorlaması mümkün olsa da denetleme. Tablo hizmetinin eşzamanlılık 'yi nasıl yönettiği hakkında daha fazla bilgi için bkz. [Microsoft Azure depolama eşzamanlılık yönetimi](../storage/common/storage-concurrency.md).  

#### <a name="merge-or-replace"></a>Birleştir veya Değiştir
**Tableoperation** sınıfının **Replace** yöntemi her zaman tablo hizmetindeki tüm varlığın yerini alır. Saklı varlıkta bu özellik varsa istek içine bir özellik eklemezseniz, istek bu özelliği saklı varlıktan kaldırır. Bir özelliği saklı bir varlıktan açıkça kaldırmak istemediğiniz müddetçe, istekteki her özelliği dahil etmeniz gerekir.  

Bir varlığı güncelleştirmek istediğinizde tablo hizmetine gönderilen veri miktarını azaltmak için **Tableoperation** sınıfının **merge** yöntemini kullanabilirsiniz. **Merge** yöntemi, saklı varlıktaki tüm özellikleri, istekte yer alan varlıktaki özellik değerleriyle değiştirir, ancak istekte bulunmayan saklı varlıktaki tüm özellikleri bozulmadan bırakır. Bu, büyük varlıklarınız varsa ve yalnızca bir istekteki az sayıda özelliği güncelleştirmeniz gerekiyorsa yararlıdır.  

> [!NOTE]
> Varlık yoksa, **Replace** ve **merge** yöntemleri başarısız olur. Alternatif olarak, mevcut değilse yeni bir varlık oluşturan **ınsertorreplace** ve **ınsertormerge** yöntemlerini kullanabilirsiniz.  
> 
> 

### <a name="working-with-heterogeneous-entity-types"></a>Heterojen varlık türleriyle çalışma
Tablo hizmeti, tek bir tablonun, tasarımınızda harika esneklik sağlayan birden çok türden varlıkları depolayabileceği anlamına gelen bir *şema daha az* tablo deposudur. Aşağıdaki örnek, hem çalışan hem de departman varlıklarını depolayan bir tabloyu göstermektedir:  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Zaman damgası</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>firstName</th>
<th>Soyadı</th>
<th>Yaş</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>firstName</th>
<th>Soyadı</th>
<th>Yaş</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>firstName</th>
<th>Soyadı</th>
<th>Yaş</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Her varlık **Partitionkey**, **Rowkey**ve **timestamp** değerlerine sahip olmalıdır, ancak herhangi bir özellik kümesine sahip olabilir. Ayrıca, bu bilgileri bir yere depolamayı seçmediğiniz müddetçe bir varlığın türünü belirten bir şey yoktur. Varlık türünü belirlemek için iki seçenek vardır:  

* Varlık türünü **rowkey** (veya büyük olasılıkla **partitionkey**) öğesine ekleyin. Örneğin, **EMPLOYEE_000123** veya **DEPARTMENT_SALES** **rowkey** değerleri olarak.  
* Varlık türünü aşağıdaki tabloda gösterildiği gibi kaydetmek için ayrı bir özellik kullanın.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Zaman damgası</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>firstName</th>
<th>Soyadı</th>
<th>Yaş</th>
<th>Email</th>
</tr>
<tr>
<td>Çalışan</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>firstName</th>
<th>Soyadı</th>
<th>Yaş</th>
<th>Email</th>
</tr>
<tr>
<td>Çalışan</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Bölüm</td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>firstName</th>
<th>Soyadı</th>
<th>Yaş</th>
<th>Email</th>
</tr>
<tr>
<td>Çalışan</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Varlık türünü **Rowkey**olarak önceden bekleyen ilk seçenek, farklı türlerde iki varlığın aynı anahtar değerine sahip olabileceği bir olasılık olduğunda yararlıdır. Ayrıca, bölümünde aynı türdeki varlıkları birlikte gruplandırır.  

Bu bölümde ele alınan teknikler özellikle bu kılavuzda daha önce açıklanan tartışma [Devralma ilişkilerle](#inheritance-relationships) ilgilidir.  

> [!NOTE]
> İstemci uygulamalarının POCO nesnelerini ve farklı sürümlerle çalışmasını sağlamak için varlık türü değerine bir sürüm numarası dahil etmeyi göz önünde bulundurmanız gerekir.  
> 
> 

Bu bölümün geri kalanında, aynı tablodaki birden çok varlık türüyle çalışmayı kolaylaştıran depolama Istemci kitaplığındaki bazı özellikler açıklanmaktadır.  

#### <a name="retrieving-heterogeneous-entity-types"></a>Heterojen varlık türlerini alma
Depolama Istemci kitaplığını kullanıyorsanız, birden fazla varlık türüyle çalışmak için üç seçeneğiniz vardır.  

Belirli bir **rowkey** ve **partitionkey** değerleriyle depolanan varlığın türünü biliyorsanız, bu durumda, varlığı, employeeentity türünde varlıklar alan önceki iki örnekte gösterildiği gibi belirtebilirsiniz.: [Depolama istemci kitaplığını kullanarak bir nokta sorgusu yürütme](#executing-a-point-query-using-the-storage-client-library) ve [LINQ kullanarak birden çok varlık alma](#retrieving-multiple-entities-using-linq).  

İkinci seçenek somut bir POCO varlık türü yerine **Dynamictableentity** türü (bir özellik paketi) kullanmaktır (Bu seçenek, varlığı .net türlerine Serileştirmeye ve serisini kaldırma gereksinimi olmadığı için performansı da iyileştirebilir). Aşağıdaki C# kod, tablodaki farklı türlerin birden çok varlığını potansiyel olarak alır, ancak tüm varlıkları **dynamictableentity** örnekleri olarak döndürür. Ardından, her bir varlığın türünü anlamak için **EntityType** özelliğini kullanır:  

```csharp
string filter = TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("PartitionKey",
    QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("RowKey",
                    QueryComparisons.GreaterThanOrEqual, "B"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey",
        QueryComparisons.LessThan, "F")
    )
);
TableQuery<DynamicTableEntity> entityQuery =
    new TableQuery<DynamicTableEntity>().Where(filter);
var employees = employeeTable.ExecuteQuery(entityQuery);

IEnumerable<DynamicTableEntity> entities = employeeTable.ExecuteQuery(entityQuery);
foreach (var e in entities)
{
EntityProperty entityTypeProperty;
if (e.Properties.TryGetValue("EntityType", out entityTypeProperty))
{
    if (entityTypeProperty.StringValue == "Employee")
    {
        // Use entityTypeProperty, RowKey, PartitionKey, Etag, and Timestamp
        }
    }
}  
```

Diğer özellikleri almak için, **Dynamictableentity** sınıfının **Properties** özelliğinde **TryGetValue** yöntemini kullanmanız gerekir.  

Üçüncü bir seçenek de, **Dynamictableentity** türü ve bir **entityresolver** örneği kullanılarak birleştirilir. Bu, aynı sorgudaki birden çok POCO türüne çözüm yapmanızı sağlar. Bu örnekte, **Entityresolver** temsilcisi, sorgunun döndürdüğü iki varlık türü arasında ayrım yapmak için **EntityType** özelliğini kullanıyor. **Resolve** yöntemi, **Dynamictableentity** örneklerini **tableentity** örneklerine çözümlemek için **çözümleyici** temsilcisini kullanır.  

```csharp
EntityResolver<TableEntity> resolver = (pk, rk, ts, props, etag) =>
{

        TableEntity resolvedEntity = null;
        if (props["EntityType"].StringValue == "Department")
        {
        resolvedEntity = new DepartmentEntity();
        }
        else if (props["EntityType"].StringValue == "Employee")
        {
        resolvedEntity = new EmployeeEntity();
        }
        else throw new ArgumentException("Unrecognized entity", "props");

        resolvedEntity.PartitionKey = pk;
        resolvedEntity.RowKey = rk;
        resolvedEntity.Timestamp = ts;
        resolvedEntity.ETag = etag;
        resolvedEntity.ReadEntity(props, null);
        return resolvedEntity;
};

string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<DynamicTableEntity> entityQuery =
        new TableQuery<DynamicTableEntity>().Where(filter);

var entities = employeeTable.ExecuteQuery(entityQuery, resolver);
foreach (var e in entities)
{
        if (e is DepartmentEntity)
        {
    ...
        }
        if (e is EmployeeEntity)
        {
    ...
        }
}  
```

#### <a name="modifying-heterogeneous-entity-types"></a>Heterojen varlık türlerini değiştirme
Silmek için bir varlığın türünü bilmeniz gerekmez ve onu eklediğinizde bir varlığın türünü her zaman bilirsiniz. Ancak, türünü bilmeksizin ve POCO varlık sınıfı kullanmadan bir varlığı güncelleştirmek için **Dynamictableentity** türünü kullanabilirsiniz. Aşağıdaki kod örneği tek bir varlık alır ve güncelleştirme yapmadan önce **Employeecount** özelliğinin var olduğunu denetler.  

```csharp
TableResult result =
        employeeTable.Execute(TableOperation.Retrieve(partitionKey, rowKey));
DynamicTableEntity department = (DynamicTableEntity)result.Result;

EntityProperty countProperty;

if (!department.Properties.TryGetValue("EmployeeCount", out countProperty))
{
        throw new
        InvalidOperationException("Invalid entity, EmployeeCount property not found.");
}
countProperty.Int32Value += 1;
employeeTable.Execute(TableOperation.Merge(department));  
```

### <a name="controlling-access-with-shared-access-signatures"></a>Paylaşılan erişim Imzaları ile erişimi denetleme
Doğrudan tablo hizmeti ile kimlik doğrulaması gerektirmeden, istemci uygulamalarının tablo varlıklarını doğrudan değiştirmesine (ve sorgulayabilmesi) izin vermek için paylaşılan erişim Imzası (SAS) belirteçlerini kullanabilirsiniz. Genellikle, uygulamanızda SAS kullanmanın üç ana avantajı vardır:  

* Bu cihazın tablo hizmetindeki varlıklara erişmesine ve bunları değiştirmesine izin vermek için depolama hesabı Anahtarınızı güvenli olmayan bir platforma (bir mobil cihaz gibi) dağıtmanız gerekmez.  
* Web ve çalışan rollerinin, varlıklarınızı son kullanıcı bilgisayarları ve mobil cihazlar gibi istemci cihazlarına yönetirken gerçekleştirdiği bazı işleri devretmek için bu işlemleri çalıştırabilirsiniz.  
* Bir istemciye kısıtlı ve zaman sınırlı bir izin kümesi atayabilirsiniz (örneğin, belirli kaynaklara salt okuma erişimine izin verme).  

Tablo hizmetiyle SAS belirteçleri kullanma hakkında daha fazla bilgi için bkz. [paylaşılan erişim imzaları (SAS) kullanma](../storage/common/storage-dotnet-shared-access-signature-part-1.md).  

Ancak, yine de tablo hizmetindeki varlıklara bir istemci uygulaması veren SAS belirteçlerini oluşturmanız gerekir: bunu, depolama hesabı Anahtarlarınıza güvenli erişim sağlayan bir ortamda yapın. Genellikle, SAS belirteçlerini oluşturmak ve varlıklarınıza erişmesi gereken istemci uygulamalarına göndermek için bir Web veya çalışan rolü kullanırsınız. SAS belirteçlerini istemcilere oluşturma ve sunma konusunda hala bir ek yük olduğundan, özellikle de yüksek hacimli senaryolarda bu yükü azaltmanın en iyi şekilde göz önünde bulundurmanız gerekir.  

Bir tablodaki varlıkların bir alt kümesine erişim veren bir SAS belirteci oluşturmak mümkündür. Varsayılan olarak, tüm tablo için bir SAS belirteci oluşturursunuz, ancak SAS belirtecinin bir dizi **partitionkey** değeri ya da bir **Partitionkey** ve **rowkey** değeri aralığı için erişim izni vermesini belirtmek de mümkündür. Her kullanıcının SAS belirteci yalnızca tablo hizmetindeki kendi varlıklarına erişmelerine izin verecek şekilde, sisteminizin bireysel kullanıcıları için SAS belirteçleri oluşturmayı tercih edebilirsiniz.  

### <a name="asynchronous-and-parallel-operations"></a>Zaman uyumsuz ve paralel işlemler
İsteklerinizi birden çok bölüme yaymakta olduğunuz için, zaman uyumsuz veya paralel sorgular kullanarak aktarım hızını ve istemci yanıt hızını artırabilirsiniz.
Örneğin, tablolarınıza paralel olarak erişen iki veya daha fazla çalışan rolü örneği olabilir. Belirli bölüm kümelerinden sorumlu tek tek çalışan rolleriniz olabilir veya her biri bir tablodaki tüm bölümlere erişebilen birden fazla çalışan rolü örneğine sahip olabilirsiniz.  

İstemci örneği içinde, depolama işlemlerini zaman uyumsuz olarak yürüterek aktarım hızını artırabilirsiniz. Depolama Istemci kitaplığı, zaman uyumsuz sorguları ve değişiklikleri yazmayı kolaylaştırır. Örneğin, aşağıdaki C# kodda gösterildiği gibi bir bölümdeki tüm varlıkları alan zaman uyumlu yöntemle başlayabilirsiniz:  

```csharp
private static void ManyEntitiesQuery(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

        TableContinuationToken continuationToken = null;

        do
        {
        var employees = employeeTable.ExecuteQuerySegmented(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
    {
        ...
    }
        continuationToken = employees.ContinuationToken;
        } while (continuationToken != null);
}  
```

Sorgunun zaman uyumsuz olarak çalışması için bu kodu kolayca değiştirebilirsiniz:  

```csharp
private static async Task ManyEntitiesQueryAsync(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);
        TableContinuationToken continuationToken = null;

        do
        {
        var employees = await employeeTable.ExecuteQuerySegmentedAsync(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            ...
        }
        continuationToken = employees.ContinuationToken;
            } while (continuationToken != null);
}  
```

Bu zaman uyumsuz örnekte, zaman uyumlu sürümden aşağıdaki değişiklikleri görebilirsiniz:  

* Yöntem imzası artık **zaman uyumsuz** değiştirici içeriyor ve bir **görev** örneği döndürüyor.  
* Sonuçları almak için **Executekesimli** yöntemi çağırmak yerine, yöntemi artık **ExecuteSegmentedAsync** yöntemini çağırır ve sonuçları zaman uyumsuz olarak almak için **await** değiştiricisini kullanır.  

İstemci uygulaması bu yöntemi birden çok kez çağırabilir ( **bölüm** parametresi için farklı değerlerle birlikte) ve her sorgu ayrı bir iş parçacığında çalışır.  

**IEnumerable** arabirimi zaman uyumsuz numaralandırmayı desteklemediğinden **Tablequery** sınıfında **Execute** yönteminin zaman uyumsuz sürümü yok.  

Ayrıca varlıkları zaman uyumsuz olarak ekleyebilir, güncelleştirebilir ve silebilirsiniz. Aşağıdaki C# örnek, bir çalışan varlığı eklemek veya değiştirmek için basit ve zaman uyumlu bir yöntemi gösterir:  

```csharp
private static void SimpleEmployeeUpsert(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = employeeTable
        .Execute(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Güncelleştirme zaman uyumsuz olarak aşağıdaki gibi çalışacak şekilde bu kodu kolayca değiştirebilirsiniz:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = await employeeTable
        .ExecuteAsync(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Bu zaman uyumsuz örnekte, zaman uyumlu sürümden aşağıdaki değişiklikleri görebilirsiniz:  

* Yöntem imzası artık **zaman uyumsuz** değiştirici içeriyor ve bir **görev** örneği döndürüyor.  
* Yöntemi, varlığı güncelleştirmek için **Execute** metodunu çağırmak yerine, şimdi **ExecuteAsync** yöntemini çağırır ve sonuçları zaman uyumsuz olarak almak için **await** değiştiricisini kullanır.  

İstemci uygulaması, bu gibi birden çok zaman uyumsuz yöntem çağırabilir ve her yöntem çağrısı ayrı bir iş parçacığında çalışır.  

### <a name="credits"></a>Krediler
Azure ekibinin aşağıdaki üyelerine katkılarına yönelik olarak teşekkür ederiz: Dominic, Jason Hogg, Jean Ghanem, JAI Haridas, Jeff Irwin, Vamshıdhar kommineni, Vinay Shah ve serdar ozler, Microsoft DX 'ten Tom Hollander. 

İnceleme döngüleri sırasında değerli geri bildirimleri için aşağıdaki Microsoft MVP 'leri de teşekkür ederiz: Igor Papirov ve Edward Bakker.

[1]: ./media/storage-table-design-guide/storage-table-design-IMAGE01.png
[2]: ./media/storage-table-design-guide/storage-table-design-IMAGE02.png
[3]: ./media/storage-table-design-guide/storage-table-design-IMAGE03.png
[4]: ./media/storage-table-design-guide/storage-table-design-IMAGE04.png
[5]: ./media/storage-table-design-guide/storage-table-design-IMAGE05.png
[6]: ./media/storage-table-design-guide/storage-table-design-IMAGE06.png
[7]: ./media/storage-table-design-guide/storage-table-design-IMAGE07.png
[8]: ./media/storage-table-design-guide/storage-table-design-IMAGE08.png
[9]: ./media/storage-table-design-guide/storage-table-design-IMAGE09.png
[10]: ./media/storage-table-design-guide/storage-table-design-IMAGE10.png
[11]: ./media/storage-table-design-guide/storage-table-design-IMAGE11.png
[12]: ./media/storage-table-design-guide/storage-table-design-IMAGE12.png
[13]: ./media/storage-table-design-guide/storage-table-design-IMAGE13.png
[14]: ./media/storage-table-design-guide/storage-table-design-IMAGE14.png
[15]: ./media/storage-table-design-guide/storage-table-design-IMAGE15.png
[16]: ./media/storage-table-design-guide/storage-table-design-IMAGE16.png
[17]: ./media/storage-table-design-guide/storage-table-design-IMAGE17.png
[18]: ./media/storage-table-design-guide/storage-table-design-IMAGE18.png
[19]: ./media/storage-table-design-guide/storage-table-design-IMAGE19.png
[20]: ./media/storage-table-design-guide/storage-table-design-IMAGE20.png
[21]: ./media/storage-table-design-guide/storage-table-design-IMAGE21.png
[22]: ./media/storage-table-design-guide/storage-table-design-IMAGE22.png
[23]: ./media/storage-table-design-guide/storage-table-design-IMAGE23.png
[24]: ./media/storage-table-design-guide/storage-table-design-IMAGE24.png
[25]: ./media/storage-table-design-guide/storage-table-design-IMAGE25.png
[26]: ./media/storage-table-design-guide/storage-table-design-IMAGE26.png
[27]: ./media/storage-table-design-guide/storage-table-design-IMAGE27.png
[28]: ./media/storage-table-design-guide/storage-table-design-IMAGE28.png
[29]: ./media/storage-table-design-guide/storage-table-design-IMAGE29.png

