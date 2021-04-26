---
title: Ölçek ve performans için Azure Cosmos DB tabloları tasarlama
description: 'Azure Tablo depolama tasarım kılavuzu: Azure Cosmos DB ve Azure Tablo depolamadaki ölçeklenebilir ve performanslı tablolar'
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 06/19/2020
author: sakash279
ms.author: akshanka
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 603c891e53e5712d489fcef8415e3db55328c9ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99988452"
---
# <a name="azure-table-storage-table-design-guide-scalable-and-performant-tables"></a>Azure Tablo Depolama tablo tasarım kılavuzu: Ölçeklenebilir ve yüksek performanslı tablolar
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Ölçeklenebilir ve performansa yönelik tablolar tasarlamak için maliyet de dahil olmak üzere çeşitli faktörleri göz önünde bulundurmanız gerekir. Daha önce ilişkisel veritabanları için şema tasarladıysanız dikkat edilmesi gereken bu yönler size tanıdık gelir. Azure Tablo depolaması ve ilişkisel modeller arasında bazı benzerlikler olsa da birçok önemli farklılıklar da bulunur. Bu farklılıklar, ilişkisel veritabanlarına aşina olan bir kullanıcıya genelde sezgisel olmayan veya yanlış olarak gözükebilir. Ancak, Tablo depolaması gibi bir NoSQL anahtar/değer deposu için tasarım yapıyorsanız bu anlamlı olur.

Tablo depolaması, verilerin milyarlarca varlık (ilişkisel veritabanı terminolojisinde "satırlar") veya yüksek işlem birimlerini desteklemesi gereken veri kümeleri için içerebilen bulut ölçekli uygulamaları destekleyecek şekilde tasarlanmıştır. Bu nedenle, verilerinizi nasıl depoladığınıza ve tablo depolamanın nasıl çalıştığını nasıl anlayacağınızı düşünün. İyi tasarlanmış bir NoSQL veri deposu, çözümünüzün daha fazla (ve daha düşük bir maliyetle) ilişkisel bir veritabanı kullanan bir çözümden daha fazla ölçeklenebilmesini sağlayabilir. Bu kılavuz, bu konularda size yardımcı olur.  

## <a name="about-azure-table-storage"></a>Azure Tablo depolama hakkında
Bu bölümde, özellikle performans ve ölçeklenebilirlik için tasarlanmasıyla ilgili tablo depolamanın bazı temel özellikleri vurgulanmıştır. Azure depolama ve tablo depolama 'yı yeni kullanmaya başladıysanız, bu makalenin geri kalanını okumadan önce .NET kullanarak [Microsoft Azure depolama giriş](../storage/common/storage-introduction.md) ve [Azure Tablo depolama ile çalışmaya başlama](./tutorial-develop-table-dotnet.md) bölümüne bakın. Bu kılavuzun odağı tablo depolama alanında olsa da, Azure kuyruk depolama ve Azure Blob depolama hakkında bazı tartışmalar ve bunları bir çözümde tablo depolama ile birlikte nasıl kullanabileceğinizi de kapsar.  

Tablo depolama, verileri depolamak için tablolu bir biçim kullanır. Standart terminolojisinde, tablonun her satırı bir varlığı temsil eder ve sütunlar ilgili varlığın çeşitli özelliklerini depolar. Her varlığın benzersiz bir şekilde tanımlanması için bir çift anahtar ve tablo depolamanın, varlığın en son ne zaman güncelleştirildiğini izlemek için kullandığı bir zaman damgası sütunu vardır. Zaman damgası alanı otomatik olarak eklenir ve zaman damgasını rastgele bir değerle el ile geçersiz kılabilirsiniz. Tablo depolaması, iyimser eşzamanlılık yönetimi için bu son değiştirilme zaman damgasını (LMT) kullanır.  

> [!NOTE]
> Tablo depolama REST API işlemler `ETag` , LMT 'den türetilen bir değer de döndürür. Bu belgede, aynı temel verilere başvurdukları için ETag ve LMT terimleri birbirinin yerine kullanılır.  
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
<td>Marketing</td>
<td>00001</td>
<td>2014-08-22T00:50:32Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Yaş</th>
<th>E-posta</th>
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
<td>Marketing</td>
<td>00002</td>
<td>2014-08-22T00:50:34Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Yaş</th>
<th>E-posta</th>
</tr>
<tr>
<td>Haz</td>
<td>Cao dili</td>
<td>47</td>
<td>junc@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>Bölüm</td>
<td>2014-08-22T00:50:30Z</td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Marketing</td>
<td>153</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>Sales</td>
<td>00010</td>
<td>2014-08-22T00:50:44Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Yaş</th>
<th>E-posta</th>
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


Şimdiye kadar, bu tasarım ilişkisel veritabanındaki bir tabloya benzer şekilde görünür. Önemli farklılıklar, zorunlu sütunlardır ve birden çok varlık türünü aynı tabloda depolayabilme özelliğidir. Ayrıca, **ad** veya **yaş** gibi Kullanıcı tanımlı özelliklerin her biri, ilişkisel veritabanındaki bir sütun gibi bir veri türüne (örneğin, bir tamsayı veya dize) sahiptir. Ancak, ilişkisel bir veritabanının aksine, tablo depolamanın şema-daha az doğası, bir özelliğin her varlıkta aynı veri türüne sahip olması gerektiği anlamına gelir. Karmaşık veri türlerini tek bir özellikte depolamak için JSON veya XML gibi serileştirilmiş bir biçim kullanmanız gerekir. Daha fazla bilgi için bkz. [Tablo depolama veri modelini anlama](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model).

Ve seçiminiz, `PartitionKey` `RowKey` iyi tablo tasarımı için temel seçenektir. Bir tabloda depolanan her varlık, ve benzersiz bir birleşimine sahip olmalıdır `PartitionKey` `RowKey` . İlişkisel bir veritabanı tablosundaki anahtarlarda olduğu gibi, `PartitionKey` ve değerleri de `RowKey` Hızlı görünüm sağlayan kümelenmiş bir dizin oluşturmak için dizinlenir. Ancak tablo depolama, ikincil dizinler oluşturmaz, bu nedenle yalnızca iki dizinli özellik bulunur (daha sonra açıklanan bazı desenler, daha sonra bu görünen sınırlamanın nasıl geçici bir şekilde çalışabilir).  

Bir tablo, bir veya daha fazla bölümden oluşur ve yaptığınız birçok tasarım kararı, `PartitionKey` çözümünüzü iyileştirmek için uygun bir seçim ve `RowKey` çözümü iyileştirmek olacaktır. Bir çözüm, bölümler halinde düzenlenmiş tüm varlıklarınızı içeren tek bir tablodan oluşabilir, ancak genellikle bir çözümde birden çok tablo vardır. Tablolar, varlıklarınızı mantıksal olarak düzenlemenize ve erişim denetim listelerini kullanarak verilere erişimi yönetmenize yardımcı olur. Tek bir depolama işlemi kullanarak bir tablonun tamamını bırakabilirsiniz.  

### <a name="table-partitions"></a>Tablo bölümleri
Hesap adı, tablo adı ve birlikte, `PartitionKey` tablo depolamanın varlığı depoladığı depolama hizmeti içindeki bölümü belirler. Varlıklar için adresleme şemasının bir parçası olan bölümler, işlemler için bir kapsam tanımlar (Bu makalenin ilerleyen bölümüne, [varlık grubu işlemlerine](#entity-group-transactions)bakın) ve tablo depolamanın nasıl ölçeklendirilebildiği temel bir şekilde oluşturur. Tablo bölümleri hakkında daha fazla bilgi için bkz. [Tablo depolama Için performans ve ölçeklenebilirlik denetim listesi](../storage/tables/storage-performance-checklist.md).  

Tablo depolamada, tek bir düğüm bir veya daha fazla tam bölüm hizmetleri ve hizmet, düğümler arasında dinamik olarak yük dengeleme bölümlerine göre ölçeklendirilir. Bir düğüm Load altındaysa tablo depolaması, bu düğüm tarafından hizmet verilen bölümlerin aralığını farklı düğümlere bölebilir. Trafik alt tarafları, tablo depolaması, Bölüm aralıklarını sessiz düğümlerden tek bir düğüm üzerine geri birleştirebilirler.  

Tablo depolamanın iç ayrıntıları hakkında daha fazla bilgi edinmek ve belirli bölümleri nasıl yönettiği hakkında daha fazla bilgi için, bkz. [Microsoft Azure depolama: güçlü tutarlılık ile yüksek oranda kullanılabilir bir bulut depolama hizmeti](/archive/blogs/windowsazurestorage/sosp-paper-windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency).  

### <a name="entity-group-transactions"></a>Varlık grubu işlemleri
Tablo depolamadaki varlık grubu işlemleri (Yumurlar), birden çok varlıkta atomik güncelleştirmeler gerçekleştirmeye yönelik tek yerleşik mekanizmadır. Yumurtları *toplu işlemler* olarak da adlandırılır. Yumurtları yalnızca aynı bölümde depolanan varlıklar üzerinde çalışabilir (belirli bir tabloda aynı bölüm anahtarını paylaşıyor), bu nedenle birden çok varlık genelinde atomik işlem davranışına ihtiyacınız varsa, bu varlıkların aynı bölümde olduğundan emin olun. Bu genellikle, farklı varlık türleri için birden çok tablo kullanmadan, birden çok varlık türünü aynı tabloda (ve bölümde) tutmanın bir nedenidir. Tek bir EGT, en fazla 100 varlık üzerinde çalışabilir.  İşlenmek üzere birden çok eş zamanlı Yumurtları gönderirseniz, bu yumurtların Yumurtları genelinde ortak varlıklar üzerinde çalışmalarından emin olmak önemlidir. Aksi takdirde, işlemeyi erteleyerek risk alırsınız.

Yumurtları, tasarımınızda değerlendirmenize yönelik potansiyel bir ticaretle da tanıtılmaktadır. Azure 'un düğümler arasında yük dengeleme isteklerini daha fazla fırsata sahip olduğu için daha fazla bölüm kullanmak uygulamanızın ölçeklenebilirliğini artırır. Ancak bu durum, uygulamanızın Atomik işlemler gerçekleştirmesini ve verilerinize yönelik güçlü tutarlılığı korumanıza yönelik yeteneği sınırlayabilir. Ayrıca, tek bir düğüm için bekleneceğiniz işlem verimini sınırlayabilen bir bölüm düzeyinde belirli ölçeklenebilirlik hedefleri vardır.

Azure depolama hesapları için ölçeklenebilirlik hedefleri hakkında daha fazla bilgi için bkz. [Standart depolama hesapları Için ölçeklenebilirlik hedefleri](../storage/common/scalability-targets-standard-account.md). Tablo depolama için ölçeklenebilirlik hedefleri hakkında daha fazla bilgi için bkz. [Tablo Depolaması Için ölçeklenebilirlik ve performans hedefleri](../storage/tables/scalability-targets.md). Bu kılavuzun sonraki bölümlerinde, bu gibi ticari koşulları yönetmenize yardımcı olan çeşitli tasarım stratejileri ele alınmaktadır ve istemci uygulamanızın belirli gereksinimlerine göre Bölüm anahtarınızı ne kadar en iyi şekilde seçebileceğinizi tartışmaktadır.  

### <a name="capacity-considerations"></a>Kapasiteye ilişkin önemli noktalar
Aşağıdaki tabloda, bir tablo depolama çözümü tasarlarken farkında olacak bazı anahtar değerler yer almaktadır:  

| Bir Azure depolama hesabının toplam kapasitesi | 500 TB |
| --- | --- |
| Bir Azure depolama hesabındaki tablo sayısı |Yalnızca depolama hesabının kapasitesine göre sınırlandırılır. |
| Tablodaki bölüm sayısı |Yalnızca depolama hesabının kapasitesine göre sınırlandırılır. |
| Bir bölümdeki varlıkların sayısı |Yalnızca depolama hesabının kapasitesine göre sınırlandırılır. |
| Tek bir varlığın boyutu |Maksimum 1 MB, en fazla 255 Özellik (, `PartitionKey` `RowKey` ve dahil `Timestamp` ). |
| Boyutunu `PartitionKey` |Boyutu 1 KB 'a kadar olan bir dize. |
| Boyutunu `RowKey` |Boyutu 1 KB 'a kadar olan bir dize. |
| Bir varlık grubu işleminin boyutu |Bir işlem en fazla 100 varlığı içerebilir ve yükün boyutu 4 MB 'tan az olmalıdır. EGT, bir varlığı yalnızca bir kez güncelleştirebilir. |

Daha fazla bilgi için bkz. [Tablo hizmeti veri modelini anlama](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model).  

### <a name="cost-considerations"></a>Maliyetle ilgili konular
Tablo Depolaması nispeten ucuzdur, ancak her iki kapasite kullanımı için maliyet tahminleri ve tablo depolamayı kullanan herhangi bir çözüm değerlendirmesinin bir parçası olarak işlem miktarı dahil edilmelidir. Ancak, Çoğu senaryoda, çözümünüzün performansını veya ölçeklenebilirliğini geliştirmek için, Normalleştirilmemiş veya yinelenen verileri depolamak için geçerli bir yaklaşım vardır. Fiyatlandırma hakkında daha fazla bilgi için bkz. [Azure Depolama fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="guidelines-for-table-design"></a>Tablo tasarımı için yönergeler
Bu listeler, tablolarınızı tasarlarken aklınızda bulundurmanız gereken önemli yönergelerinden bazılarını özetler. Bu kılavuz, bunların hepsini daha sonra daha ayrıntılı bir şekilde ele alır. Bu yönergeler, genellikle ilişkisel veritabanı tasarımı için takip ettiğiniz kılavuzlardan farklıdır.  

Tablo *depolamayı etkili bir* şekilde tasarlamak için tasarlama:

* **Okuma ağır uygulamalarda sorgulama için tasarım.** Tablolarınızı tasarlarken, varlıklarınızı nasıl güncelleştireceğiz konusunda düşündüğünüzden önce çalıştıracağınız sorgular (özellikle gecikme süresine duyarlı olanlar) hakkında düşünün. Bu, genellikle etkili ve performanslı bir çözüme neden olur.  
* **Sorgularınızı hem hem de belirtin `PartitionKey` `RowKey` .** Bunlar gibi *nokta sorguları* en verimli tablo depolama sorgulardır.  
* **Varlıkların yinelenen kopyalarını depolamayı göz önünde bulundurun.** Tablo Depolaması bir tek EAP olduğundan, daha verimli sorgular sağlamak için aynı varlığı birden çok kez (farklı anahtarlarla) depolamayı düşünün.  
* **Verilerinizi kabul etmeyi düşünün.** Tablo Depolaması bir şebedir, bu nedenle verilerinizi kabul etmeyi düşünün. Örneğin, toplama verileri sorgularının yalnızca tek bir varlığa erişmesi için, Özet varlıkları depolayın.  
* **Bileşik anahtar değerlerini kullanın.** Yalnızca kullandığınız anahtarlar `PartitionKey` ve `RowKey` . Örneğin, varlıklara alternatif anahtarlı erişim yolları sağlamak için bileşik anahtar değerlerini kullanın.  
* **Sorgu projeksiyonu kullanın.** Yalnızca ihtiyaç duyduğunuz alanları seçerek ağ üzerinden aktardığınız veri miktarını azaltabilirsiniz.  

Tablo Depolamayı, *yazma* etkili olacak şekilde tasarlama:  

* **Etkin bölümler oluşturmayın.** İsteklerinizi dilediğiniz zaman bir noktada birden çok bölüme yaymaya olanak sağlayan anahtarlar ' ı seçin.  
* **Trafikte ani artışlar önleyin.** Trafiği makul bir süre içinde dağıtın ve trafikte ani artışlar önleyin.
* **Her varlık türü için ayrı bir tablo oluşturmanız gerekmez.** Varlık türleri arasında Atomik işlemler gerektirdiğinde, bu birden çok varlık türünü aynı tablodaki aynı bölümde saklayabilirsiniz.
* **Elde etmeniz gereken en yüksek aktarım hızını göz önünde bulundurun.** Tablo depolaması için ölçeklenebilirlik hedeflerini bilmeniz ve tasarımınızın bu özellikleri aşmanıza neden olmamasını sağlamalısınız.  

Bu kılavuzun ilerleyen kısımlarında, tüm bu ilkeleri uygulamaya yerleştirtirecek örnekleri görürsünüz.  

## <a name="design-for-querying"></a>Sorgulama için tasarım
Tablo depolama, yoğun okuma, yoğun yazma veya ikisinin karışımı olabilir. Bu bölüm, okuma işlemlerini verimli şekilde desteklemeye yönelik tasarımı dikkate alır. Genellikle, okuma işlemlerini etkin şekilde destekleyen bir tasarım, yazma işlemleri için de etkilidir. Bununla birlikte, yazma işlemlerini desteklemek için tasarlanırken ek hususlar vardır. Bunlar, [veri değişikliğini tasarlamak üzere](#design-for-data-modification)bir sonraki bölümde ele alınmıştır.

Verileri etkili bir şekilde okumanızı sağlamak için iyi bir başlangıç noktası, "uygulamamın gerek duyduğu verileri almak için hangi sorguları çalıştırması gerekir?" sormanız.  

> [!NOTE]
> Tablo depolamayla, daha sonra değiştirmek zor ve pahalı olduğundan tasarımın en baştan olması önemlidir. Örneğin, ilişkisel bir veritabanında, var olan bir veritabanına dizinler ekleyerek genellikle performans sorunlarını ele almak mümkündür. Bu, tablo depolamada bir seçenek değildir.  

### <a name="how-your-choice-of-partitionkey-and-rowkey-affects-query-performance"></a>Seçtiğiniz `PartitionKey` ve `RowKey` sorgu performansının nasıl etkilediği
Aşağıdaki örneklerde, tablo depolamanın çalışan varlıklarını aşağıdaki yapıyla depoladığını varsaymaktadır (örneklerin çoğu `Timestamp` açıklık için özelliği gözardı eder):  

| Sütun adı | Veri türü |
| --- | --- |
| `PartitionKey` (Departman adı) |Dize |
| `RowKey` (Çalışan KIMLIĞI) |Dize |
| `FirstName` |Dize |
| `LastName` |Dize |
| `Age` |Tamsayı |
| `EmailAddress` |Dize |

Tablo depolama sorguları tasarlamak için bazı genel yönergeler aşağıda verilmiştir. Aşağıdaki örneklerde kullanılan filtre sözdizimi tablo depolama REST API. Daha fazla bilgi için bkz. [Sorgu varlıkları](/rest/api/storageservices/Query-Entities).  

* *Nokta sorgusu* , kullanılacak en etkili aramadır ve en düşük gecikme süresini gerektiren yüksek hacimli aramalar veya aramalar için önerilir. Böyle bir sorgu, ve değerlerini belirterek tek bir varlığı etkin bir şekilde bulmak için dizinleri kullanabilir `PartitionKey` `RowKey` . Örneğin: `$filter=(PartitionKey eq 'Sales') and (RowKey eq '2')`.  
* İkinci en iyi *Aralık sorgusudur*. Birden `PartitionKey` `RowKey` fazla varlık döndürmek için bir değer aralığı üzerinde ve filtrelerini kullanır. `PartitionKey`Değer belirli bir bölümü tanımlar ve `RowKey` değerler bu bölümdeki varlıkların bir alt kümesini tanımlar. Örneğin: `$filter=PartitionKey eq 'Sales' and RowKey ge 'S' and RowKey lt 'T'`.  
* Üçüncü en iyi *bölüm taramasından*. `PartitionKey`Anahtar olmayan başka bir özellik üzerinde, ve filtrelerini kullanır ve birden fazla varlık döndürebilir. `PartitionKey`Değer belirli bir bölümü tanımlar ve özellik değerleri ilgili bölümdeki varlıkların bir alt kümesini seçer. Örneğin: `$filter=PartitionKey eq 'Sales' and LastName eq 'Smith'`.  
* *Tablo taraması* , `PartitionKey` herhangi bir eşleşen varlık için tablonuzu oluşturan tüm bölümleri aradığı için, ' yi içermez ve verimsiz olur. Filtrenizin ' i kullanıp kullanmadığını bakılmaksızın tablo taraması gerçekleştirir `RowKey` . Örneğin: `$filter=LastName eq 'Jones'`.  
* Birden çok varlık döndüren Azure Tablo depolama sorguları bunları `PartitionKey` ve sırasını sıralar `RowKey` . İstemcideki varlıkları yeniden kullanmaktan kaçınmak için `RowKey` en yaygın sıralama düzenini tanımlayan bir seçin. Azure Cosmos DB Azure Tablo API'si tarafından döndürülen sorgu sonuçları bölüm anahtarına veya satır anahtarına göre sıralanmaz. Özellik farklarının ayrıntılı bir listesi için, [Azure Cosmos DB ve Azure Tablo depolamadaki tablo API'si arasındaki farklara](table-api-faq.md#table-api-vs-table-storage)bakın.

Değerleri temel alan bir filtre belirtmek için "**or**" kullanılması `RowKey` , Bölüm taramasıyla sonuçlanır ve Aralık sorgusu olarak değerlendirilmez. Bu nedenle, şu gibi filtreler kullanan sorgulardan kaçının: `$filter=PartitionKey eq 'Sales' and (RowKey eq '121' or RowKey eq '322')` .  

Etkili sorgular çalıştırmak için depolama Istemci kitaplığını kullanan istemci tarafı kodu örnekleri için, bkz.:  

* [Depolama Istemci kitaplığını kullanarak bir nokta sorgusu çalıştırma](#run-a-point-query-by-using-the-storage-client-library)
* [LINQ kullanarak birden çok varlık alma](#retrieve-multiple-entities-by-using-linq)
* [Sunucu tarafı projeksiyonu](#server-side-projection)  

Aynı tabloda depolanan birden çok varlık türünü işleyebilen istemci tarafı kodu örnekleri için, bkz.:  

* [Heterojen varlık türleriyle çalışma](#work-with-heterogeneous-entity-types)  

### <a name="choose-an-appropriate-partitionkey"></a>Uygun bir `PartitionKey`
Tercih ettiğiniz seçim, `PartitionKey` varlıklarınızı birden çok bölüme dağıtmak (ölçeklenebilir bir çözüm sağlamak için) gereksinimini karşılamak için gereken yumurtların kullanılmasını (tutarlılık sağlamak için) sağlar.  

Bir Extreme 'de, tüm varlıklarınızı tek bir bölümde saklayabilirsiniz. Ancak bu, çözümünüzün ölçeklenebilirliğini sınırlandırabilir ve tablo depolamanın yük dengeleme isteklerini önleyebilmesini engelliyor olabilir. Diğer bir deyişle, bölüm başına bir varlık depolayabilirler. Bu yüksek oranda ölçeklenebilir ve tablo depolamayı Yük Dengeleme isteklerine olanak sağlar, ancak varlık grubu işlemlerini kullanmanızı önler.  

İdeal `PartitionKey` olarak, verimli sorgular kullanmanıza olanak sağlar ve çözümünüzün ölçeklenebilir olmasını sağlamak için yeterli bölüm vardır. Genellikle, varlıklarınızın varlıklarınızı yeterli bölüm genelinde dağıtan uygun bir özelliği olacağını göreceksiniz.

> [!NOTE]
> Örneğin, kullanıcılar veya çalışanlar hakkındaki bilgileri depolayan bir sistemde `UserID` iyi bir fikir olabilir `PartitionKey` . Bölüm anahtarı olarak belirli bir kullanan birkaç varlık olabilir `UserID` . Bir kullanıcı hakkında veri depolayan her varlık tek bir bölümde gruplandırılır. Bu varlıklara, önemli ölçüde ölçeklenebilir olmaya devam ederken, Yumurtları aracılığıyla erişilebilir.
> 
> 

Seçtiğiniz `PartitionKey` varlıkları ekleme, güncelleştirme ve silme konularıyla ilgili ek konular vardır. Daha fazla bilgi için bu makalenin ilerleyen kısımlarında [veri değişikliği Için tasarım](#design-for-data-modification) bölümüne bakın.  

### <a name="optimize-queries-for-table-storage"></a>Tablo depolama için sorguları iyileştirme
Tablo depolaması, `PartitionKey` `RowKey` tek bir kümelenmiş dizindeki ve değerlerini kullanarak varlıklarınızı otomatik olarak dizinler. Bu, nokta sorgularının kullanım açısından en verimli olmasının nedenidir. Ancak, ve üzerindeki kümelenmiş dizinde başka bir dizin yoktur `PartitionKey` `RowKey` .

Birçok tasarım, varlıkların birden çok ölçüte göre aramasını etkinleştirmek için gereksinimlere uymalıdır. Örneğin, e-posta, çalışan KIMLIĞI veya soyadı temelinde çalışan varlıklarını bulma. Bölüm [tablosu tasarım desenlerinde](#table-design-patterns) bulunan aşağıdaki desenler, bu tür gereksinimlerin adresleridir. Desenler, tablo depolamanın ikincil dizinler sağlamayacağından sonra çalışma yollarını da anlatmaktadır.  

* [Bölüm içi ikincil dizin kalıbı](#intra-partition-secondary-index-pattern): farklı `RowKey` değerler (aynı bölümde) kullanarak her varlığın birden çok kopyasını depolayın. Bu sayede hızlı ve verimli aramalar ve farklı değerler kullanarak sıralama düzenleri alternatif olarak sağlanır `RowKey` .  
* Bölümler [arası ikincil dizin kalıbı](#inter-partition-secondary-index-pattern): `RowKey` ayrı bölümlerde veya ayrı tablolarda farklı değerler kullanarak her varlığın birden çok kopyasını depolayın. Bu sayede hızlı ve verimli aramalar ve farklı değerler kullanarak sıralama düzenleri alternatif olarak sağlanır `RowKey` .  
* [Dizin varlıkları stili](#index-entities-pattern): varlık listeleri döndüren etkili aramaları etkinleştirmek için Dizin varlıklarını koruyun.  

### <a name="sort-data-in-table-storage"></a>Tablo depolamadaki verileri sıralama

Tablo Depolaması sorgu sonuçlarını, ve daha sonra öğesine göre artan sırada sıralanmış olarak döndürür `PartitionKey` `RowKey` .

> [!NOTE]
> Azure Cosmos DB Azure Tablo API'si tarafından döndürülen sorgu sonuçları bölüm anahtarına veya satır anahtarına göre sıralanmaz. Özellik farklarının ayrıntılı bir listesi için, [Azure Cosmos DB ve Azure Tablo depolamadaki tablo API'si arasındaki farklara](table-api-faq.md#table-api-vs-table-storage)bakın.

Tablo depolamadaki anahtarlar dize değerleridir. Sayısal değerlerin doğru şekilde sıralanmasını sağlamak için, bunları sabit bir uzunluğa dönüştürmeniz ve bunları sıfırlarla birlikte yapmanız gerekir. Örneğin, olarak kullandığınız çalışan KIMLIĞI değeri `RowKey` bir tamsayı değeri ise, **123** çalışan kimliğini **00000123** olarak dönüştürmeniz gerekir. 

Birçok uygulamanın, farklı siparişlerde sıralanmış verileri kullanma gereksinimleri vardır: Örneğin, çalışanları ada göre veya birleştirme tarihine göre sıralama. Bölüm [tablosu tasarım desenlerindeki](#table-design-patterns) aşağıdaki desenler, varlıklarınız için nasıl alternatif sıralama düzenleri ele alma bölümüne yöneliktir:  

* [Bölüm içi ikincil dizin kalıbı](#intra-partition-secondary-index-pattern): farklı `RowKey` değerler (aynı bölümde) kullanarak her varlığın birden çok kopyasını depolayın. Bu sayede hızlı ve verimli aramalar ve farklı değerler kullanarak sıralama düzenleri alternatif olarak sağlanır `RowKey` .  
* Bölümler [arası ikincil dizin](#inter-partition-secondary-index-pattern), ayrı tablolarda ayrı bölümlerde farklı değerler kullanarak her varlığın birden çok kopyasını depolayın `RowKey` . Bu sayede hızlı ve verimli aramalar ve farklı değerler kullanarak sıralama düzenleri alternatif olarak sağlanır `RowKey` .
* [Günlük kuyruğu düzeni](#log-tail-pattern):  `RowKey` ters tarih ve saat düzeninde sıralama yapan bir değer kullanarak bir bölüme en son eklenen n varlıklarını alın.  

## <a name="design-for-data-modification"></a>Veri değişikliği için tasarım
Bu bölüm, eklemeleri, güncelleştirmeleri ve silmeleri iyileştirmeye yönelik tasarım konularına odaklanır. Bazı durumlarda, veri değişikliği için optimize eden tasarımlara göre sorgulama için optimize eden tasarımlar arasındaki dengelemeyi değerlendirmeniz gerekir. Bu değerlendirme, ilişkisel veritabanları için tasarımlarda yaptığınız işe benzerdir (ancak, tasarım sunmaların yönetilmesine yönelik teknikler ilişkisel bir veritabanında farklı olur). Bölüm [tablosu tasarım desenleri](#table-design-patterns) , tablo depolaması için bazı ayrıntılı tasarım düzenlerini açıklar ve bu ticaretin bazılarını vurgular. Uygulamada, varlıkları sorgulamak için en iyi duruma getirilmiş birçok tasarım, varlıkları değiştirmek için de iyi çalışır.  

### <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>INSERT, Update ve DELETE işlemlerinin performansını iyileştirin
Bir varlığı güncelleştirmek veya silmek için, ve değerlerini kullanarak belirleyebilmelisiniz `PartitionKey` `RowKey` . Bu şekilde, `PartitionKey` `RowKey` varlıkları değiştirmek için ve seçiminiz, nokta sorgularını desteklemek için tercih ettiğiniz ölçütlere uygun kriterleri izlemelidir. Varlıkları mümkün olduğunca verimli bir şekilde tanımlamak istiyorsunuz. `PartitionKey` `RowKey` Güncelleştirmek veya silmek için gereken değerleri bulmak için bir varlığı bulmak üzere verimsiz bölüm veya tablo taraması kullanmak istemezsiniz.  

Bölüm [tablosu tasarım desenlerindeki](#table-design-patterns) aşağıdaki desenler, ekleme, güncelleştirme ve silme işlemlerinin performansını en iyi duruma getirme adresidir:  

* [Yüksek hacimli silme stili](#high-volume-delete-pattern): eşzamanlı silme için tüm varlıkları kendi ayrı tablosunda depolayarak yüksek hacimli varlıkların silinmesini etkinleştirin. Tabloyu silerek varlıkları silersiniz.  
* [Veri serisi stili](#data-series-pattern): yaptığınız istek sayısını en aza indirmek için, tüm veri serisini tek bir varlıkta depolayın.  
* [Geniş varlıklar stili](#wide-entities-pattern): 252 'den fazla özelliği olan mantıksal varlıkları depolamak için birden çok fiziksel varlık kullanın.  
* [Büyük varlıklar stili](#large-entities-pattern): büyük özellik değerlerini depolamak için BLOB depolama kullanın.  

### <a name="ensure-consistency-in-your-stored-entities"></a>Saklı varlıklarınızda tutarlılık sağlama
Veri değişikliklerini iyileştirmeye yönelik anahtar seçiminizi etkileyen diğer anahtar faktörü, Atomik işlemler kullanılarak tutarlılık sağlama sağlamaktır. Bir EGT 'yi yalnızca aynı bölümde depolanan varlıklar üzerinde çalışmak için kullanabilirsiniz.  

Bölüm [tablosu tasarım desenlerinde](#table-design-patterns) aşağıdaki desenler tutarlılığı yönetme adresi:  

* [Bölüm içi ikincil dizin kalıbı](#intra-partition-secondary-index-pattern): farklı `RowKey` değerler (aynı bölümde) kullanarak her varlığın birden çok kopyasını depolayın. Bu sayede hızlı ve verimli aramalar ve farklı değerler kullanarak sıralama düzenleri alternatif olarak sağlanır `RowKey` .  
* Bölümler [arası ikincil dizin kalıbı](#inter-partition-secondary-index-pattern): `RowKey` ayrı bölümlerde veya ayrı tablolarda farklı değerler kullanarak her varlığın birden çok kopyasını depolayın. Bu sayede hızlı ve verimli aramalar ve farklı değerler kullanarak sıralama düzenleri alternatif olarak sağlanır `RowKey` .  
* [Sonuçta tutarlı işlem kriteri](#eventually-consistent-transactions-pattern): Azure kuyrukları kullanarak bölüm sınırları veya depolama sistemi sınırları genelinde sürekli tutarlı davranışı etkinleştirin.
* [Dizin varlıkları stili](#index-entities-pattern): varlık listeleri döndüren etkili aramaları etkinleştirmek için Dizin varlıklarını koruyun.  
* [Denormalleştirme stili](#denormalization-pattern): tek bir nokta sorgusuyla ihtiyacınız olan tüm verileri almanızı sağlamak için ilgili verileri tek bir varlıkta birleştirin.  
* [Veri serisi stili](#data-series-pattern): yaptığınız istek sayısını en aza indirmek için, tüm veri serisini tek bir varlıkta depolayın.  

Daha fazla bilgi için bu makalenin ilerleyen kısımlarında [varlık grubu işlemleri](#entity-group-transactions) konusuna bakın.  

### <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Etkili değişiklikler için tasarımınızın etkili sorgular olmasını sağlar
Birçok durumda, verimli bir şekilde sorgulama yapmak için bir tasarım etkili değişiklikler sağlar, ancak bunun belirli senaryonuz için durum olup olmadığını her zaman değerlendirin. Bölüm [tablosu tasarım desenlerindeki](#table-design-patterns) desenlerden bazıları, varlıkları sorgulama ve değiştirme arasındaki dengelemeler açıkça değerlendirir ve her bir işlem türünün sayısını her zaman dikkate almanız gerekir.  

Bölüm [tablosu tasarım desenlerinde](#table-design-patterns) aşağıdaki desenler, verimli sorgular tasarlama ve verimli veri değişikliği için tasarlama arasında denge sağlar:  

* [Bileşik anahtar stili](#compound-key-pattern): `RowKey` bir istemcinin tek nokta sorgusuyla ilgili verileri araması için bileşik değerler kullanın.  
* [Günlük kuyruğu düzeni](#log-tail-pattern):  `RowKey` ters tarih ve saat düzeninde sıralama yapan bir değer kullanarak bir bölüme en son eklenen n varlıklarını alın.  

## <a name="encrypt-table-data"></a>Tablo verilerini şifreleme
.NET Azure depolama istemci kitaplığı, ekleme ve değiştirme işlemleri için dize varlık özelliklerinin şifrelenmesini destekler. Şifrelenmiş dizeler hizmette ikili özellikler olarak depolanır ve şifre çözme sonrasında dizelere geri dönüştürülür.    

Tablolar için, şifreleme ilkesine ek olarak, kullanıcıların şifrelenecek özellikleri belirtmesi gerekir. Bir öznitelik belirtin `EncryptProperty` (öğesinden türetilen POCO varlıkları için `TableEntity` ) veya istek seçeneklerinde bir şifreleme Çözümleyicisi belirtin. Şifreleme çözümleyici, Bölüm anahtarını, satır anahtarını ve özellik adını alan bir temsilcisidir ve bu özelliğin şifrelenip şifrelenmeyeceğini belirten bir Boole değeri döndürür. Şifreleme sırasında, istemci kitaplığı bu bilgileri bir özelliğin, Tel yazarken şifrelenmesi gerekip gerekmediğine karar vermek için kullanır. Temsilci, özelliklerin nasıl şifrelendiğini gösteren mantık olasılığını da sağlar. (Örneğin, X ise, özelliğini şifreleyin; Aksi halde A ve B özelliklerini şifreleyin.) Varlıkları okurken veya sorgularken bu bilgilerin sağlanması gerekli değildir.

Birleştirme Şu anda desteklenmiyor. Özelliklerin bir alt kümesi daha önce farklı bir anahtar kullanılarak şifrelendiğinden, yalnızca yeni özellikleri birleştirmek ve meta verileri güncelleştirmek veri kaybına neden olur. Birleştirme, önceden var olan varlığı hizmetten okumak ya da özellik başına yeni bir anahtar kullanmak için ek hizmet çağrıları yapılmasını gerektirir. Bunların hiçbiri performans nedenleriyle uygun değildir.     

Tablo verilerini şifreleme hakkında daha fazla bilgi için bkz. [Microsoft Azure depolama Için istemci tarafı şifreleme ve Azure Key Vault](../storage/common/storage-client-side-encryption.md).  

## <a name="model-relationships"></a>Model ilişkileri
Etki alanı modellerinin oluşturulması, karmaşık sistemlerin tasarımında önemli bir adımdır. Genellikle, iş etki alanını anlamanız ve sisteminizin tasarımını bilgilendirmenin bir yolu olarak, varlıkları ve aralarındaki ilişkileri tanımlamak için modelleme sürecini kullanırsınız. Bu bölüm, etki alanı modellerinde bulunan bazı ortak ilişki türlerinden bazılarını tablo depolaması için tasarımlara nasıl çevrilekullanabileceğinizi ele alınmaktadır. Bir mantıksal veri modelinden fiziksel NoSQL tabanlı veri modeliyle eşleme işlemi, ilişkisel bir veritabanı tasarlarken kullanılan öğesinden farklıdır. İlişkisel veritabanları tasarımı genellikle bir veri normalleştirme işleminin artıklığı en iyi duruma getirme için optimize eder. Bu tür tasarımda ayrıca, veritabanının nasıl çalıştığı ile ilgili uygulamayı soyutlayan bildirime dayalı bir sorgulama özelliği de varsayılır.  

### <a name="one-to-many-relationships"></a>Bire çok ilişkileri
İş etki alanı nesneleri arasında bire çok ilişki sık gerçekleşir: Örneğin, bir departmanın birçok çalışanı vardır. Tablo depolamada, her biri belirli senaryoya uygun olabilecek profesyonelleri ve dezavantajlarla bire çok ilişkiler uygulamak için birkaç yol vardır.  

On binlerce departman ve çalışan varlığı ile çok uluslu bir kuruluşun örneğini göz önünde bulundurun. Her departmanın birçok çalışanı vardır ve her çalışan, belirli bir departmanla ilişkilendirilir. Tek bir yaklaşım, aşağıdakiler gibi ayrı departmanı ve çalışan varlıklarını depomaktır:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE01.png" alt-text="Bir departman varlığını ve bir çalışan varlığını gösteren grafik":::

Bu örnek, değer temelinde, türleri arasında örtük bir çoktan çoğa ilişki gösterir `PartitionKey` . Her departmanın birçok çalışanı olabilir.  

Bu örnekte ayrıca aynı bölümdeki bir departman varlığı ve ilgili çalışan varlıkları da gösterilmektedir. Farklı varlık türleri için farklı bölümler, tablolar veya hatta depolama hesapları kullanmayı seçebilirsiniz.  

Aşağıdaki örnekte gösterildiği gibi, verilerinizi benimseme ve yalnızca daha fazla çalışan departman verilerine sahip çalışan varlıklarını depolayan alternatif bir yaklaşım. Bu senaryoda, bir departman yöneticisinin ayrıntılarını değiştirebilmeniz için gerekli olan bu yaklaşım en iyi olmayabilir. Bunu yapmak için, departmandaki her çalışanı güncelleştirmeniz gerekir.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE02.png" alt-text="Verilerinizin nasıl alınacağını gösteren ve yalnızca yoğun olarak kullanılan departman verileriyle çalışan varlıklarını depolayan çalışan varlık grafiği.":::

Daha fazla bilgi için bu kılavuzun [ilerleyen kısımlarında daha sonra, bkz](#denormalization-pattern) ..  

Aşağıdaki tabloda, bire çok ilişkisine sahip olan çalışan ve departman varlıklarını depolamanın her bir yaklaşımının uzmanları ve dezavantajları özetlenmektedir. Ayrıca, çeşitli işlemleri gerçekleştirmek için ne kadar sıklıkla beklediğinizi de göz önünde bulundurmanız gerekir. Bu işlem yalnızca seyrek gerçekleşseydiğinde pahalı bir işlem içeren bir tasarıma sahip olmak için kabul edilebilir.  

<table>
<tr>
<th>Yaklaşım</th>
<th>Avantajlar</th>
<th>Dezavantajlar</th>
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
<li>Aynı bölümde depolama işlemleri gerçekleşecektir. Yüksek işlem birimlerinde bu, bir etkin noktaya neden olabilir.</li>
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

Bu seçenekler arasından seçim yapma ve uzmanlarının ve dezavantajlarının en önemli olduğu, belirli uygulama senaryolarınıza bağlıdır. Örneğin, departman varlıklarını ne sıklıkta değiştirirsiniz? Tüm çalışan sorgularınız ek departman bilgilerine ihtiyaç duyuyor mu? Bölümlerinizde veya depolama hesabınızda ölçeklenebilirlik limitleriniz ne kadar yakınlanıyor?  

### <a name="one-to-one-relationships"></a>Bire bir ilişkiler
Etki alanı modelleri, varlıklar arasında bire bir ilişki içerebilir. Tablo depolamada bire bir ilişki uygulamanız gerekiyorsa, her ikisini de almanız gerektiğinde iki ilişkili varlığı bağlamayı da seçmeniz gerekir. Bu bağlantı, her bir varlıktaki bir bağlantıyı `PartitionKey` ve ilgili varlığına bir bağlantı depolayarak, anahtar değerlerinde bir kurala göre örtülü veya açık olabilir `RowKey` . Aynı bölümde ilgili varlıkların depolanması gerekip gerekmediğini öğrenmek için, [tek-çok ilişkileri](#one-to-many-relationships)bölümüne bakın.  

Ayrıca, tablo depolamada bire bir ilişki uygulamanıza yol açabilecek uygulama konuları da vardır:  

* Büyük varlıkları işleme (daha fazla bilgi için bkz. [büyük varlıklar kalıbı](#large-entities-pattern)).  
* Erişim denetimleri uygulama (daha fazla bilgi için bkz. [paylaşılan erişim imzaları Ile denetim erişimi](#control-access-with-shared-access-signatures)).  

### <a name="join-in-the-client"></a>İstemciye ekleme
Tablo depolamadaki ilişkileri modellemekle ilgili yollar vardır, ancak tablo depolamayı kullanmanın iki ana nedeni ölçeklenebilirlik ve performans sağlar. Çözümünüzün performansını ve ölçeklenebilirliğini tehlikeye atabilecek birçok ilişki modellebileceğinizi fark ederseniz, tüm veri ilişkilerini tablo tasarımınızda oluşturmak için gerekliyse kendinize danışmalısınız. İstemci uygulamanızın gerekli birleştirmeleri gerçekleştirmesini istiyorsanız, tasarımı basitleştirebiliyor ve çözümünüzün ölçeklenebilirlik ve performansını geliştirebilirsiniz.  

Örneğin, sıklıkla değişmeyen veriler içeren küçük tablolar varsa, bu verileri bir kez alabilir ve istemcide önbelleğe alabilirsiniz. Bu, aynı verileri almak için yinelenen gidiş dönüşlerini önleyebilir. Bu kılavuzda incelediğimiz örneklerde küçük bir kuruluştaki bölüm kümesinin küçük olması ve seyrek olarak değiştirilmesi olasıdır. Bu, bir istemci uygulamanın bir kez indirebileceğiniz ve arama verileri olarak önbelleğe aldığı veriler için iyi bir aday sağlar.  

### <a name="inheritance-relationships"></a>Devralma ilişkileri
İstemci uygulamanız, iş varlıklarını temsil etmek üzere devralma ilişkisinin bir parçasını oluşturan bir sınıf kümesi kullanıyorsa, bu varlıkları tablo depolamadaki kolayca kalıcı hale getirebilirsiniz. Örneğin, istemci uygulamanızda tanımlanmış, soyut bir sınıf olan aşağıdaki sınıf kümesine sahip olabilirsiniz `Person` .

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE03.png" alt-text="Devralma ilişkilerinin diyagramı":::

Tek bir tablo kullanarak tablo depolamada iki somut sınıfın örneklerini kalıcı hale getirebilirsiniz `Person` . Aşağıdaki gibi görünen varlıkları kullanın:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE04.png" alt-text="Müşteri varlığı ve çalışan varlığını gösteren grafik":::

İstemci kodunda aynı tabloda birden fazla varlık türüyle çalışma hakkında daha fazla bilgi için bu kılavuzun ilerleyen kısımlarında bulunan [heterojen varlık türleriyle çalışma](#work-with-heterogeneous-entity-types) konusuna bakın. Bu, istemci kodundaki varlık türünün nasıl tanınacağını örnekler sağlar.  

## <a name="table-design-patterns"></a>Tablo tasarımı desenleri
Önceki bölümlerde, sorgu kullanarak varlık verilerini almak ve varlık verilerini eklemek, güncelleştirmek ve silmek için tablo tasarımınızı nasıl iyileştirebileceğinizi öğrendiniz. Bu bölümde, tablo depolamayla kullanım için uygun bazı desenler açıklanmaktadır. Buna ek olarak, bu kılavuzda daha önce ortaya çıkan bazı sorunlar ve dengelemeler için nasıl çözüm kullanabileceğinizi göreceksiniz. Aşağıdaki diyagramda, farklı desenler arasındaki ilişkiler özetlenmektedir:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE05.png" alt-text="Tablo Tasarım desenlerinin diyagramı":::

Desen eşleme, bu kılavuzda belgelenen desenler (mavi) ve kenar desenleri (turuncu) arasındaki ilişkileri vurgular. Dikkate değer veren birçok farklı desen vardır. Örneğin, tablo depolaması için önemli senaryolardan biri, [komut sorgu sorumluluğu](/previous-versions/msp-n-p/jj554200(v=pandp.10)) ayırma düzeninden [gerçekleştirilmiş görünüm deseninin](/previous-versions/msp-n-p/dn589782(v=pandp.10)) kullanılması.  

### <a name="intra-partition-secondary-index-pattern"></a>Bölüm içi ikincil dizin kalıbı
Farklı `RowKey` değerler (aynı bölümde) kullanarak her varlığın birden çok kopyasını depolayın. Bu sayede hızlı ve verimli aramalar ve farklı değerler kullanarak sıralama düzenleri alternatif olarak sağlanır `RowKey` . Kopyalar arasındaki güncelleştirmeler, yumurtalar kullanılarak tutarlı tutulabilir.  

#### <a name="context-and-problem"></a>Bağlam ve sorun
Tablo depolama, ve değerlerini kullanarak varlıkları otomatik olarak dizinler `PartitionKey` `RowKey` . Bu, bir istemci uygulamanın bu değerleri kullanarak bir varlığı etkin bir şekilde almasını sağlar. Örneğin, aşağıdaki tablo yapısını kullanarak, bir istemci uygulaması departman adını ve çalışan KIMLIĞINI ( `PartitionKey` ve değerlerini) kullanarak tek bir çalışan varlığını almak için bir nokta sorgusu kullanabilir `RowKey` . Ayrıca, bir istemci her bir departman içindeki çalışan KIMLIĞINE göre sıralanmış varlıkları alabilir.

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE06.png" alt-text="Bir istemci uygulamanın, Bölüm adı ve çalışan KIMLIĞI (PartitionKey ve RowKey değerleri) kullanarak bireysel bir çalışan varlığı almak için bir nokta sorgusu kullanabileceği çalışan varlık grafiği.":::

Ayrıca, e-posta adresi gibi başka bir özelliğin değerine göre bir çalışan varlığı bulmak istiyorsanız, bir eşleşme bulmak için daha az verimli bir bölüm taraması kullanmanız gerekir. Bunun nedeni tablo depolamanın ikincil dizinler sağlamadır. Ayrıca, bir çalışan listesini Order 'dan farklı bir sırada sıralanmış olarak isteme seçeneği yoktur `RowKey` .  

#### <a name="solution"></a>Çözüm
İkincil dizinlerin eksikliğine geçici bir çözüm bulmak için, her bir kopyanın farklı bir değer kullanarak her bir varlığın birden çok kopyasını saklayabilirsiniz `RowKey` . Aşağıdaki yapılarla bir varlık depoluysanız, e-posta adresini veya çalışan KIMLIĞINI temel alarak çalışan varlıklarını etkin bir şekilde alabilirsiniz. ,, Ve için ön ek değerleri, bir dizi `RowKey` `empid_` `email_` e-posta adresi veya çalışan kimliği kullanarak tek bir çalışan veya bir dizi çalışan için sorgulamanızı sağlar.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE07.png" alt-text="Değişen RowKey değerleri olan çalışan varlığını gösteren grafik":::

Aşağıdaki iki filtre ölçütü (bir çalışan KIMLIĞI tarafından bir arama ve e-posta adresiyle arama) her ikisi de nokta sorguları belirler:  

* $filter = (PartitionKey EQ ' Sales ') ve (RowKey EQ ' empid_000223 ')  
* $filter = (PartitionKey EQ ' Sales ') ve (RowKey EQ ' email_jonesj@contoso.com ')  

Bir dizi çalışan varlığı için sorgulama yaparsanız, çalışan KIMLIĞI sırasında sıralanmış bir Aralık veya e-posta adresi sırasında sıralanan bir Aralık belirleyebilirsiniz. İçinde uygun öneki olan varlıklar için sorgu `RowKey` .  

* 000100 ile 000199 arasında bir çalışan KIMLIĞI olan Satış departmanındaki tüm çalışanları bulmak için, şunu kullanın: $filter = (PartitionKey EQ ' Sales ') ve (RowKey Ge ' empid_000100 ') ve (RowKey Le ' empid_000199 ')  
* "A" harfiyle başlayan bir e-posta adresine sahip satış departmanındaki tüm çalışanları bulmak için, şunu kullanın: $filter = (PartitionKey EQ ' Sales ') ve (RowKey Ge ' email_a ') ve (RowKey lt ' email_b ')  
  
Yukarıdaki örneklerde kullanılan filtre sözdizimi tablo depolama REST API. Daha fazla bilgi için bkz. [Sorgu varlıkları](/rest/api/storageservices/Query-Entities).  

#### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Bu düzenin nasıl uygulanacağına karar verirken aşağıdaki noktaları göz önünde bulundurun:  

* Tablo depolama alanı görece bir şekilde kullanılmak üzere, yinelenen verileri depolamanın maliyet ek yükü önemli bir sorun değildir. Bununla birlikte, tahmini depolama gereksinimlerinize göre tasarımınızın maliyetini her zaman değerlendirmeli ve yalnızca istemci uygulamanızın çalışacağı sorguları destekleyecek şekilde yinelenen varlıklar ekleyebilirsiniz.  
* İkincil dizin varlıkları özgün varlıklarla aynı bölümde depolandığından, tek bir bölüm için ölçeklenebilirlik hedeflerini aşmayın emin olun.  
* Varlığın iki kopyasını otomatik olarak güncelleştirmek için Yumurtları kullanarak, yinelenen varlıklarınızı birbirleriyle tutarlı tutabilirsiniz. Bu, bir varlığın tüm kopyalarını aynı bölümde depolamanız gerektiğini gösterir. Daha fazla bilgi için bkz. [varlık grubu Işlemlerini kullanma](#entity-group-transactions).  
* İçin kullanılan değer `RowKey` her varlık için benzersiz olmalıdır. Bileşik anahtar değerlerini kullanmayı düşünün.  
* İçindeki sayısal değerleri doldurma `RowKey` (örneğin, çalışan kimliği 000223), üst ve alt sınırlara göre doğru sıralamayı ve filtrelemeyi sunar.  
* Varlığınızın tüm özelliklerini yinelememeniz gerekmez. Örneğin, varlıklarda e-posta adresini kullanarak varlıkları aramak için `RowKey` hiçbir zaman çalışan kullanım ömrü gerekmez, bu varlıklar aşağıdaki yapıya sahip olabilir:

  :::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE08.png" alt-text="Çalışan varlığı grafiği":::

* Genellikle, yinelenen verileri depolamak ve bir varlık bulmak için tek bir sorgu kullanmanın yanı sıra gerekli verileri aramak için tek bir sorgu ile ihtiyacınız olan tüm verileri almanızı sağlamak daha iyidir.  

#### <a name="when-to-use-this-pattern"></a>Bu düzenin kullanılacağı durumlar
Bu düzeni aşağıdaki durumlarda kullanın:

- İstemci uygulamanızın çeşitli farklı anahtarlar kullanarak varlıkları alması gerekir.
- İstemcinizin farklı sıralama emirlerindeki varlıkları alması gerekir.
- Her varlığı, çeşitli benzersiz değerler kullanarak tanımlayabilirsiniz.

Ancak, farklı değerleri kullanarak varlık aramaları gerçekleştirirken bölüm ölçeklenebilirlik sınırlarını aşmayın emin olun `RowKey` .  

#### <a name="related-patterns-and-guidance"></a>İlgili düzenler ve kılavuzlar
Bu düzen uygulanırken aşağıdaki düzenler ve yönergeler de yararlı olabilir:  

* [Bölümler arası ikincil dizin kalıbı](#inter-partition-secondary-index-pattern)
* [Bileşik anahtar stili](#compound-key-pattern)
* [Varlık grubu işlemleri](#entity-group-transactions)
* [Heterojen varlık türleriyle çalışma](#work-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>Bölümler arası ikincil dizin kalıbı
Ayrı bölümlerde veya ayrı tablolarda farklı değerler kullanarak her varlığın birden çok kopyasını depolayın `RowKey` . Bu sayede hızlı ve verimli aramalar ve farklı değerler kullanarak sıralama düzenleri alternatif olarak sağlanır `RowKey` .  

#### <a name="context-and-problem"></a>Bağlam ve sorun
Tablo depolama, ve değerlerini kullanarak varlıkları otomatik olarak dizinler `PartitionKey` `RowKey` . Bu, bir istemci uygulamanın bu değerleri kullanarak bir varlığı etkin bir şekilde almasını sağlar. Örneğin, aşağıdaki tablo yapısını kullanarak, bir istemci uygulaması departman adını ve çalışan KIMLIĞINI ( `PartitionKey` ve değerlerini) kullanarak tek bir çalışan varlığını almak için bir nokta sorgusu kullanabilir `RowKey` . Ayrıca, bir istemci her bir departman içindeki çalışan KIMLIĞINE göre sıralanmış varlıkları alabilir.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE09.png" alt-text="Kullanıldığında, bir istemci uygulaması, departman adı ve çalışan kimliği (partitionkey ve rowkey değerleri) kullanarak bireysel bir çalışan varlığını almak için bir nokta sorgusu kullanabilir."::: tuşlarına

Ayrıca, e-posta adresi gibi başka bir özelliğin değerine bağlı olarak bir çalışan varlığı bulabilmek istiyorsanız, bir eşleşme bulmak için daha az verimli bir bölüm taraması kullanmanız gerekir. Bunun nedeni tablo depolamanın ikincil dizinler sağlamadır. Ayrıca, bir çalışan listesini Order 'dan farklı bir sırada sıralanmış olarak isteme seçeneği yoktur `RowKey` .  

Bu varlıklara yönelik yüksek hacimli işlemlere benimsemeyi bekleme olursunuz ve tablo depolama hızının istemcinizi sınırlayan riskini en aza indirmek istersiniz.  

#### <a name="solution"></a>Çözüm
İkincil dizinlerin eksikliğine geçici bir çözüm bulmak için, her bir kopyanın farklı ve değerleri kullanarak her bir varlığın birden çok kopyasını `PartitionKey` saklayabilirsiniz `RowKey` . Aşağıdaki yapılarla bir varlık depoluysanız, e-posta adresini veya çalışan KIMLIĞINI temel alarak çalışan varlıklarını etkin bir şekilde alabilirsiniz. ,, Ve için önek değerleri, `PartitionKey` `empid_` `email_` bir sorgu için kullanmak istediğiniz dizini tanımlamanızı sağlar.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE10.png" alt-text="Birincil dizin ve ikincil dizinli çalışan varlığı olan çalışan varlığını gösteren grafik":::

Aşağıdaki iki filtre ölçütü (bir çalışan KIMLIĞI tarafından bir arama ve e-posta adresiyle arama) her ikisi de nokta sorguları belirler:  

* $filter = (PartitionKey EQ ' empid_Sales ') ve (RowKey EQ ' 000223 ')
* $filter = (PartitionKey EQ ' email_Sales ') ve (RowKey EQ ' jonesj@contoso.com ')  

Bir dizi çalışan varlığı için sorgulama yaparsanız, çalışan KIMLIĞI sırasında sıralanmış bir Aralık veya e-posta adresi sırasında sıralanan bir Aralık belirleyebilirsiniz. İçinde uygun öneki olan varlıklar için sorgu `RowKey` .  

* Satış departmanındaki tüm çalışanları **000100** - **000199** aralığında, çalışan kimliği SıRASıYLA sıralanan bir çalışan kimliği ile bulmak için, şunu kullanın: $Filter = (partitionkey EQ ' empid_Sales ') ve (rowkey Ge ' 000100 ') ve (rowkey Le ' 000199 ')  
* Satış departmanındaki tüm çalışanları "a" ile başlayan, e-posta adresi sırasıyla sıralanmış bir e-posta adresiyle bulmak için: $filter = (PartitionKey EQ ' email_Sales ') ve (RowKey Ge ' a ') ve (RowKey lt ' b ') kullanın  

Yukarıdaki örneklerde kullanılan filtre sözdiziminin tablo depolama REST API olduğunu unutmayın. Daha fazla bilgi için bkz. [Sorgu varlıkları](/rest/api/storageservices/Query-Entities).  

#### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Bu düzenin nasıl uygulanacağına karar verirken aşağıdaki noktaları göz önünde bulundurun:  

* Birincil ve ikincil dizin varlıklarını sürdürmek için [sürekli tutarlı işlemler modelini](#eventually-consistent-transactions-pattern) kullanarak, yinelenen varlıklarınızın birbirleriyle sürekli tutarlı kalmasını sağlayabilirsiniz.  
* Tablo depolama alanı görece bir şekilde kullanılmak üzere, yinelenen verileri depolamanın maliyet ek yükü önemli bir sorun olmamalıdır. Ancak, tahmin edilen depolama gereksinimlerinize göre tasarımınızın maliyetini her zaman değerlendirin ve yalnızca, istemci uygulamanızın çalışacağı sorguları destekleyecek şekilde yinelenen varlıklar ekleyin.  
* İçin kullanılan değer `RowKey` her varlık için benzersiz olmalıdır. Bileşik anahtar değerlerini kullanmayı düşünün.  
* İçindeki sayısal değerleri doldurma `RowKey` (örneğin, çalışan kimliği 000223), üst ve alt sınırlara göre doğru sıralamayı ve filtrelemeyi sunar.  
* Varlığınızın tüm özelliklerini yinelememeniz gerekmez. Örneğin, varlıklarda e-posta adresini kullanarak varlıkları aramak için `RowKey` hiçbir zaman çalışan kullanım ömrü gerekmez, bu varlıklar aşağıdaki yapıya sahip olabilir:
  
  :::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE11.png" alt-text="İkincil dizinli çalışan varlığını gösteren grafik":::

* Genellikle, yinelenen verileri depolamak ve tek bir sorgu ile ihtiyacınız olan tüm verileri, ikincil dizini kullanarak bir varlığı bulmak için bir sorgu kullanmanın ve birincil dizinde gerekli verileri aramak için bir sorgu kullanmaktan emin olmak daha iyidir.  

#### <a name="when-to-use-this-pattern"></a>Bu düzenin kullanılacağı durumlar
Bu düzeni aşağıdaki durumlarda kullanın:

- İstemci uygulamanızın çeşitli farklı anahtarlar kullanarak varlıkları alması gerekir.
- İstemcinizin farklı sıralama emirlerindeki varlıkları alması gerekir.
- Her varlığı, çeşitli benzersiz değerler kullanarak tanımlayabilirsiniz.

Farklı değerleri kullanarak varlık aramaları gerçekleştirirken bölüm ölçeklenebilirlik sınırlarını aşmamak istiyorsanız bu stili kullanın `RowKey` .  

#### <a name="related-patterns-and-guidance"></a>İlgili düzenler ve kılavuzlar
Bu düzen uygulanırken aşağıdaki düzenler ve yönergeler de yararlı olabilir:  

* [Sonuçta tutarlı işlem kriteri](#eventually-consistent-transactions-pattern)  
* [Bölüm içi ikincil dizin kalıbı](#intra-partition-secondary-index-pattern)  
* [Bileşik anahtar stili](#compound-key-pattern)  
* [Varlık grubu işlemleri](#entity-group-transactions)  
* [Heterojen varlık türleriyle çalışma](#work-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>Sonuçta tutarlı işlem kriteri
Azure kuyruklarını kullanarak bölüm sınırları veya depolama sistemi sınırları genelinde sonuçta tutarlı davranışı etkinleştirin.  

#### <a name="context-and-problem"></a>Bağlam ve sorun
Yumurtları aynı bölüm anahtarını paylaşan birden çok varlıkta atomik işlemleri etkinleştirir. Performans ve ölçeklenebilirlik nedenleriyle, tutarlılık gereksinimlerine sahip varlıkları ayrı bölümlerde veya ayrı bir depolama sisteminde depolamaya karar verebilirsiniz. Böyle bir senaryoda, tutarlılığı sağlamak için Yumurtları kullanamazsınız. Örneğin, arasında son tutarlılığı sürdürmek için bir gereksiniminize sahip olabilirsiniz:  

* Aynı tabloda, farklı tablolarda veya farklı depolama hesaplarında bulunan iki farklı bölümde depolanan varlıklar.  
* Tablo depolama alanında depolanan bir varlık ve BLOB depolamada depolanan bir BLOB.  
* Tablo depolama alanında depolanan bir varlık ve dosya sistemindeki bir dosya.  
* Henüz Azure Bilişsel Arama kullanılarak dizini oluşturulmuş tablo depolamada depolanan bir varlık.  

#### <a name="solution"></a>Çözüm
Azure kuyruklarını kullanarak, iki veya daha fazla bölüm veya depolama sisteminde nihai tutarlılık sağlayan bir çözüm uygulayabilirsiniz.

Bu yaklaşımı göstermek için, eski çalışan varlıklarını arşivlemek için bir gereksinimin olduğunu varsayalım. Eski çalışan varlıkları nadiren sorgulanır ve geçerli çalışanlarla ilgilenen etkinliklerden çıkarılmalıdır. Bu gereksinimi uygulamak için, **geçerli** tablodaki etkin çalışanları ve **Arşiv** tablosundaki eski çalışanları depolarlar. Bir çalışanın arşivlenmesi, varlığı **geçerli** tablodan silmenizi ve varlığı **Arşiv** tablosuna eklemenizi gerektirir.

Ancak bu iki işlemi gerçekleştirmek için EGT kullanamazsınız. Bir başarısızlığın bir varlığın her iki tabloda veya hiç tablo halinde görünmesine neden olması riskini önlemek için Arşiv işleminin sonunda tutarlı olması gerekir. Aşağıdaki sıra diyagramı bu işlemdeki adımları özetler.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE12.png" alt-text="Nihai tutarlılık için çözüm diyagramı":::

İstemci, Azure kuyruğuna bir ileti yerleştirerek arşiv işlemini başlatır (Bu örnekte, çalışan #456 arşivlemek için). Bir çalışan rolü yeni iletiler için kuyruğu yoklar; bir tane bulduğunda iletiyi okur ve kuyrukta gizli bir kopya bırakır. Çalışan rolü, **geçerli** tablodaki varlığın bir kopyasını getirir, **Arşiv** tablosuna bir kopya ekler ve ardından **geçerli** tablodan orijinali siler. Son olarak, önceki adımlardan bir hata yoksa, çalışan rolü gizli iletiyi kuyruktan siler.  

Bu örnekte, diyagramdaki 4. adım, çalışanı **Arşiv** tablosuna ekler. Çalışan BLOB depolama alanındaki bir bloba veya dosya sistemindeki bir dosya ekleyebilir.  

#### <a name="recover-from-failures"></a>Hatalardan kurtarma
Çalışan rolünün arşiv işlemini yeniden başlatması gerektiğinden, diyagramdaki 4-5. adımlarda gerçekleştirilen işlemlerin *ıdempotent* olması önemlidir. Tablo Depolaması kullanıyorsanız, 4. adım için "Ekle veya Değiştir" işlemini kullanmanız gerekir. 5. adımda, kullanmakta olduğunuz istemci kitaplığındaki "varsa sil" işlemini kullanmanız gerekir. Başka bir depolama sistemi kullanıyorsanız, uygun bir ıdempotent işlemi kullanmanız gerekir.  

Çalışan rolü diyagramda 6. adımı hiçbir zaman tamamlarsa, bir zaman aşımından sonra ileti tekrar işlemeyi denemek için çalışan rolü için ayrılan sırada yeniden görüntülenir. Çalışan rolü, sıradaki bir iletinin kaç kez okunduğunu denetleyebilir ve gerekirse, bunu ayrı bir kuyruğa göndererek araştırma için "Poison" iletisi olarak işaretleyin. Sıra iletilerini okuma ve sıradan çıkarma sayısını denetleme hakkında daha fazla bilgi için bkz. [Iletileri alma](/rest/api/storageservices/Get-Messages).  

Tablo depolama ve kuyruk depolamadaki bazı hatalar geçici hatalardır ve istemci uygulamanız onları işlemek için uygun yeniden deneme mantığını içermelidir.  

#### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Bu düzenin nasıl uygulanacağına karar verirken aşağıdaki noktaları göz önünde bulundurun:  

* Bu çözüm, işlem yalıtımı sağlamaz. Örneğin, bir istemci, çalışan rolü Diyagramdaki Adım 4-5 arasında olduğunda **geçerli** ve **Arşiv** tablolarını okuyabilir ve verilerin tutarsız bir görünümünü görürsünüz. Veriler sonunda tutarlı olacaktır.  
* Nihai tutarlılığı sağlamak için 4-5 adımlarının ıdempotent olduğundan emin olmanız gerekir.  
* Birden çok kuyruk ve çalışan rolü örneği kullanarak çözümü ölçeklendirebilirsiniz.  

#### <a name="when-to-use-this-pattern"></a>Bu düzenin kullanılacağı durumlar
Farklı bölümlerde veya tablolarda bulunan varlıklar arasında nihai tutarlılığı garantilemek istediğinizde bu stili kullanın. Tablo depolama ve BLOB depolama genelindeki işlemlere ve veritabanı ya da dosya sistemi gibi diğer Azure dışı depolama veri kaynaklarına yönelik nihai tutarlılığı sağlamak için bu kalıbı genişletebilirsiniz.  

#### <a name="related-patterns-and-guidance"></a>İlgili düzenler ve kılavuzlar
Bu düzen uygulanırken aşağıdaki düzenler ve yönergeler de yararlı olabilir:  

* [Varlık grubu işlemleri](#entity-group-transactions)  
* [Birleştir veya Değiştir](#merge-or-replace)  

> [!NOTE]
> Çözümünüz için işlem yalıtımı önemliyse, Yumurtları kullanmanıza olanak tanımak için tablolarınızı yeniden tasarlamayı düşünün.  
> 
> 

### <a name="index-entities-pattern"></a>Dizin varlıkları kalıbı
Varlık listeleri döndüren etkili aramaları etkinleştirmek için Dizin varlıklarını koruyun.  

#### <a name="context-and-problem"></a>Bağlam ve sorun
Tablo depolama, ve değerlerini kullanarak varlıkları otomatik olarak dizinler `PartitionKey` `RowKey` . Bu, bir istemci uygulamanın bir nokta sorgusu kullanarak bir varlığı etkin bir şekilde almasını sağlar. Örneğin, aşağıdaki tablo yapısını kullanarak, bir istemci uygulaması departman adını ve çalışan KIMLIĞINI (ve) kullanarak bireysel bir çalışan varlığını verimli bir şekilde alabilir `PartitionKey` `RowKey` .  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE13.png" alt-text="Bir istemci uygulamanın, departman adı ve çalışan KIMLIĞI (PartitionKey ve RowKey) kullanarak bireysel bir çalışan varlığını verimli bir şekilde alabileceği çalışan varlık yapısının grafiği.":::

Ayrıca, soyadı gibi benzersiz olmayan başka bir özelliğin değerine göre çalışan varlıklarının bir listesini alabilmek istiyorsanız, daha az verimli bir bölüm taraması kullanmanız gerekir. Bu tarama, bunları doğrudan aramak için bir dizin kullanmak yerine eşleşmeleri bulur. Bunun nedeni tablo depolamanın ikincil dizinler sağlamadır.  

#### <a name="solution"></a>Çözüm
Önceki varlık yapısıyla son ada göre aramayı etkinleştirmek için, çalışan kimliklerinin listesini korumanız gerekir. Can gibi belirli bir soyadı olan çalışan varlıklarını almak isterseniz, ilk adı olarak Jones ile çalışanlar için çalışan kimlikleri listesini bulmanız ve ardından bu çalışan varlıklarını almanız gerekir. Çalışan kimliklerinin listesini depolamak için üç ana seçenek vardır:  

* Blob depolamayı kullanın.  
* Dizin varlıklarını, çalışan varlıklarıyla aynı bölümde oluşturun.  
* Ayrı bir bölümde veya tabloda Dizin varlıkları oluşturun.  

Seçenek 1: blob depolamayı kullanma  

Her benzersiz soyadı için bir blob oluşturun ve her bir blob 'da, `PartitionKey` Bu soyadı olan çalışanların (departman) ve `RowKey` (çalışan kimliği) değerlerinin bir listesini depolar. Bir çalışan eklediğinizde veya sildiğinizde, ilgili Blob içeriğinin çalışan varlıklarıyla tutarlı olduğundan emin olun.  

2. seçenek: aynı bölümde Dizin varlıkları oluşturma  

Aşağıdaki verileri depolayan Dizin varlıklarını kullanın:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE14.png" alt-text="Aynı soyadı taşıyan çalışan kimliklerinin bir listesini içeren bir dize içeren çalışan varlığı gösteren grafik":::

`EmployeeIDs`Özelliği, içinde depolanan son ada sahip çalışanlar için çalışan kimliklerinin bir listesini içerir `RowKey` .  

Aşağıdaki adımlarda, yeni bir çalışan eklerken izlemeniz gereken işlem ana hatlarıyla verilmiştir. Bu örnekte, satış departmanında 000152 KIMLIĞI ve soyadı Jones olan bir çalışan ekliyoruz:  

1. `PartitionKey`"Sales" değerine ve `RowKey` "Jones" değerine sahip dizin varlığını alın. 2. adımda kullanmak üzere bu varlığın ETag öğesini kaydedin.  
2. Yeni çalışan varlığını ("Sales" değeri ve "000152" değeri) ekleyen bir varlık grubu işlemi (bir toplu işlem) oluşturun `PartitionKey` `RowKey` ve Dizin varlığını (" `PartitionKey` Sales" değeri ve `RowKey` "can" değeri) güncelleştirir. EGT bunu, yeni çalışan KIMLIĞINI Employeıdds alanındaki listeye ekleyerek yapar. Yumurtalar hakkında daha fazla bilgi için bkz. [varlık grubu işlemleri](#entity-group-transactions).  
3. EGT, iyimser bir eşzamanlılık hatası nedeniyle başarısız olursa (yani başka biri dizin varlığını değiştirmişse), 1. adımda baştan başlamanız gerekir.  

İkinci seçeneği kullanıyorsanız, bir çalışanı silmek için benzer bir yaklaşım kullanabilirsiniz. Çalışanın soyadını değiştirme işlemi, üç varlığı güncelleştiren bir EGT çalıştırmanız gerektiğinden biraz daha karmaşıktır: çalışan varlığı, eski soyadı için Dizin varlığı ve yeni soyadı için Dizin varlığı. Daha sonra iyimser eşzamanlılık kullanarak güncelleştirmeleri gerçekleştirmek için kullanabileceğiniz ETag değerlerini almak için herhangi bir değişiklik yapmadan önce her bir varlığı almalısınız.  

Aşağıdaki adımlarda, bir departmandaki belirli bir soyadı olan tüm çalışanları aramanız gerektiğinde izlemeniz gereken işlem ana hatlarıyla verilmiştir. Bu örnekte, satış departmanında en son adı Jones olan tüm çalışanları bakıyoruz:  

1. `PartitionKey`"Sales" değerine ve `RowKey` "Jones" değerine sahip dizin varlığını alın.  
2. Alandaki çalışan kimliklerinin listesini ayrıştırın `EmployeeIDs` .  
3. Bu çalışanların (e-posta adresleri gibi) her biri hakkında ek bilgilere ihtiyacınız varsa, `PartitionKey` "Sales" değerini ve `RowKey` 2. adımda elde ettiğiniz çalışanların listesinden alınan değerleri kullanarak çalışan varlıklarının her birini alın.  

Seçenek 3: ayrı bir bölümde veya tabloda Dizin varlıkları oluşturma  

Bu seçenek için aşağıdaki verileri depolayan Dizin varlıklarını kullanın:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE15.png" alt-text="RowKey ve PartitionKey içinde depolanan son ada sahip çalışanlar için çalışan kimliklerinin bir listesini içeren çalışan dizin varlığını gösteren ekran görüntüsü.":::

`EmployeeDetails`Özelliği, içinde depolanan son ada sahip çalışanlar için çalışan kimliklerinin ve bölüm adı çiftlerinin bir listesini içerir `RowKey` .

Dizin varlıkları çalışan varlıklarından ayrı bir bölümde olduğundan tutarlılığı sağlamak için Yumurtları kullanamazsınız. Dizin varlıklarının en sonunda çalışan varlıklarıyla tutarlı olduğundan emin olun.  

#### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Bu düzenin nasıl uygulanacağına karar verirken aşağıdaki noktaları göz önünde bulundurun:  

* Bu çözüm, eşleşen varlıkları almak için en az iki sorgu gerektirir: biri değer listesini almak için Dizin varlıklarını sorgular `RowKey` ve ardından listedeki her varlığı almak için sorgular.  
* Tek bir varlık en fazla 1 MB 'lık bir değer içerdiğinden, çözümde 2. seçenek ve seçenek 3 seçeneği, belirli bir soyadı için çalışan kimlikleri listesinin hiç bir kez 1 MB olduğunu varsayar. Çalışan kimlikleri listesinin boyutu 1 MB 'tan fazla olursa, 1. seçeneği kullanın ve dizin verilerini BLOB depolama alanında depolayın.  
* Seçenek 2 ' yi (çalışanları ekleme ve silme işlemlerini ve bir çalışanın soyadını değiştirmeyi işlemek için Yumurlar kullanarak) kullanırsanız, işlem hacmi belirli bir bölümdeki ölçeklenebilirlik sınırlarına yaklaşıp yaklaşmayacak şekilde değerlendirmeniz gerekir. Bu durumda, sonunda tutarlı bir çözüm (seçenek 1 veya seçenek 3) göz önünde bulundurmanız gerekir. Bu, güncelleştirme isteklerini işlemek için kuyrukları kullanır ve Dizin varlıklarınızı çalışan varlıklarından ayrı bir bölümde depolamanıza olanak tanır.  
* Bu çözümde 2. seçenek, bir departmandaki son ada göre bakmak istediğinizi varsayar. Örneğin, satış departmanında soyadı Jones olan çalışanların bir listesini almak istiyorsunuz. Tüm kuruluş genelinde en son bir ada sahip tüm çalışanları aramak istiyorsanız 1 veya seçenek 3 ' ü kullanın.
* Nihai tutarlılığı sunan kuyruk tabanlı bir çözüm uygulayabilirsiniz. Daha fazla ayrıntı için, [sonuçta tutarlı işlemler düzenine](#eventually-consistent-transactions-pattern)bakın.  

#### <a name="when-to-use-this-pattern"></a>Bu düzenin kullanılacağı durumlar
Tüm çalışanlar gibi ortak bir özellik değerini paylaşan bir varlık kümesini (can soyadı Jones olan tüm çalışanlar gibi) aramak istediğinizde bu stili kullanın.  

#### <a name="related-patterns-and-guidance"></a>İlgili düzenler ve kılavuzlar
Bu düzen uygulanırken aşağıdaki düzenler ve yönergeler de yararlı olabilir:  

* [Bileşik anahtar stili](#compound-key-pattern)  
* [Sonuçta tutarlı işlem kriteri](#eventually-consistent-transactions-pattern)  
* [Varlık grubu işlemleri](#entity-group-transactions)  
* [Heterojen varlık türleriyle çalışma](#work-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>Denormalleştirme stili
Tek bir nokta sorgusuyla ihtiyacınız olan tüm verileri almanızı sağlamak için ilgili verileri tek bir varlıkta birlikte birleştirin.  

#### <a name="context-and-problem"></a>Bağlam ve sorun
İlişkisel bir veritabanında, sorgular birden çok tablodan veri aldığında oluşan yinelemeyi kaldırmak için genellikle verileri normalleştirin. Verilerinizi Azure tablolarında normalleştirin, ilişkili verilerinizi almak için istemciden sunucuya birden çok gidiş dönüş yapmanız gerekir. Örneğin, aşağıdaki tablo yapısıyla, bir departmanın ayrıntılarını almak için iki gidiş dönüş gerekir. Bir seyahat, yöneticinin KIMLIĞINI içeren departman varlığını getirir ve ikinci seyahat, yöneticinin ayrıntılarını bir çalışan varlığında getirir.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE16.png" alt-text="Departman varlığı ve çalışan varlığı grafiği":::

#### <a name="solution"></a>Çözüm
Verileri iki ayrı varlıkta depolamak yerine, verileri yeniden oluşturup, Bölüm varlığındaki yöneticinin ayrıntılarının bir kopyasını saklayın. Örnek:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE17.png" alt-text="Yoğun ve birleştirilmiş departman varlığının grafiği":::

Bu özelliklerle depolanan departman varlıkları sayesinde, bir nokta sorgusu kullanarak bir departmanla ilgili ihtiyaç duyduğunuz tüm ayrıntıları alabilirsiniz.  

#### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Bu düzenin nasıl uygulanacağına karar verirken aşağıdaki noktaları göz önünde bulundurun:  

* Bazı verileri iki kez depolamaya ilişkin maliyet ek yükü vardır. Tablo depolamaya daha az istek elde eden performans avantajı, genellikle depolama maliyetlerindeki marguinal artışa göre yapılır. Ayrıca, bu maliyet, bir departmanın ayrıntılarını getirmek için ihtiyaç duyduğunuz işlem sayısı azalmasıyla kısmen denkleştirilir.  
* Yöneticileriyle ilgili bilgileri depolayan iki varlığın tutarlılığını korumanız gerekir. Tek bir atomik işlemde birden fazla varlığı güncelleştirmek için yumurtalar kullanarak tutarlılık sorununu işleyebilirsiniz. Bu durumda, departman varlığı ve Departman Yöneticisi için çalışan varlığı aynı bölümde depolanır.  

#### <a name="when-to-use-this-pattern"></a>Bu düzenin kullanılacağı durumlar
İlgili bilgileri sık sık aramanız gerektiğinde bu stili kullanın. Bu model, istemcinizin gerektirdiği verileri almak için yapması gereken sorgu sayısını azaltır.  

#### <a name="related-patterns-and-guidance"></a>İlgili düzenler ve kılavuzlar
Bu düzen uygulanırken aşağıdaki düzenler ve yönergeler de yararlı olabilir:  

* [Bileşik anahtar stili](#compound-key-pattern)  
* [Varlık grubu işlemleri](#entity-group-transactions)  
* [Heterojen varlık türleriyle çalışma](#work-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>Bileşik anahtar stili
`RowKey`Bir istemcinin tek nokta sorgusuyla ilgili verileri araması için bileşik değerleri kullanın.  

#### <a name="context-and-problem"></a>Bağlam ve sorun
İlişkisel bir veritabanında, ilgili veri parçalarını tek bir sorgudaki istemciye döndürmek için sorgularda birleştirmeleri kullanmak doğal bir veritabanıdır. Örneğin, ilgili varlıkların bir listesini aramak ve bu çalışana yönelik verileri gözden geçirmek için çalışan KIMLIĞINI kullanabilirsiniz.  

Aşağıdaki yapıyı kullanarak, çalışan varlıklarını tablo depolamada depoladığını varsayın:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE18.png" alt-text="Çalışan varlıklarını tablo depolamada depolamak için kullanmanız gereken çalışan varlık yapısının grafiği.":::

Ayrıca, çalışanın kuruluşunuz için çalıştığı her bir yıla ait incelemeler ve performansla ilgili geçmiş verileri depolamanız ve bu bilgilere yıla göre erişebilmek için ihtiyacınız vardır. Bir seçenek, aşağıdaki yapıyla varlıkları depolayan başka bir tablo oluşturmaktır:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE19.png" alt-text="Çalışan gözden geçirme varlığı grafiği":::

Bu yaklaşımda, verileri tek bir istekle almanızı sağlamak için yeni varlıktaki bazı bilgileri (örneğin, ilk adı ve soyadı) çoğaltmaya karar verebilirsiniz. Ancak, bu iki varlığı otomatik olarak güncelleştirmek için EGT 'yi kullanamadığından güçlü tutarlılığı koruyamıyorum.  

#### <a name="solution"></a>Çözüm
Aşağıdaki yapıyla varlıkları kullanarak özgün tablonuzda yeni bir varlık türü depolayın:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE20.png" alt-text="Bileşik anahtarla çalışan varlığının grafiği":::

Nasıl `RowKey` bir bileşik anahtar olduğunu, çalışan kimliği ve gözden geçirme verilerinin yılından nasıl yapıldığını fark edin. Bu, çalışanın performansını almanızı ve tek bir varlık için tek bir istekle verileri incelemenizi sağlar.  

Aşağıdaki örnek, belirli bir çalışana ait tüm gözden geçirme verilerini (örneğin, satış departmanında çalışan 000123) nasıl alacağınızı özetler:  

$filter = (PartitionKey EQ ' Sales ') ve (RowKey Ge ' empid_000123 ') ve (RowKey lt ' empid_000124 ') &$select = RowKey, yönetici derecelendirmesi, eş derecelendirme, açıklamalar  

#### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Bu düzenin nasıl uygulanacağına karar verirken aşağıdaki noktaları göz önünde bulundurun:  

* Değeri ayrıştırmayı kolaylaştıran uygun bir ayırıcı karakter kullanmanız gerekir `RowKey` : Örneğin, **000123_2012**.  
* Ayrıca, bu varlığı aynı çalışanla ilgili verileri içeren diğer varlıklarla aynı bölümde depoluyorsunuz. Bu, güçlü tutarlılığı sürdürmek için Yumurtları kullanabileceğiniz anlamına gelir.
* Bu düzenin uygun olup olmadığını anlamak için verileri ne sıklıkta sorgulayacağınızı düşünmeniz gerekir. Örneğin, İnceleme verilerine sık sık ve ana çalışan verileri sıklıkla eriştiğinizde, onları ayrı varlıklar olarak saklamanız gerekir.  

#### <a name="when-to-use-this-pattern"></a>Bu düzenin kullanılacağı durumlar
Sık olarak Sorgulayabileceğiniz bir veya daha fazla ilgili varlığı depolamanız gerektiğinde bu stili kullanın.  

#### <a name="related-patterns-and-guidance"></a>İlgili düzenler ve kılavuzlar
Bu düzen uygulanırken aşağıdaki düzenler ve yönergeler de yararlı olabilir:  

* [Varlık grubu işlemleri](#entity-group-transactions)  
* [Heterojen varlık türleriyle çalışma](#work-with-heterogeneous-entity-types)  
* [Sonuçta tutarlı işlem kriteri](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>Günlük kuyruk kalıbı
 `RowKey` Ters tarih ve saat düzeninde sıralama yapan bir değer kullanarak bir bölüme en son eklenen n varlıklarını alın.  

> [!NOTE]
> Azure Cosmos DB Azure Tablo API'si tarafından döndürülen sorgu sonuçları bölüm anahtarına veya satır anahtarına göre sıralanmaz. Bu nedenle, bu model tablo depolaması için uygun olsa da Azure Cosmos DB için uygun değildir. Özellik farklarının ayrıntılı bir listesi için, [Azure Cosmos DB ve Azure Tablo depolamadaki tablo API'si arasındaki farklara](table-api-faq.md#table-api-vs-table-storage)bakın.

#### <a name="context-and-problem"></a>Bağlam ve sorun
Yaygın bir gereksinim, en son oluşturulan varlıkları (örneğin, bir çalışan tarafından gönderilen en son gider taleplerini) alabilmelidir. Tablo sorguları bir `$top` kümeden ilk *n* varlığı döndürmek için bir sorgu işlemini destekler. Bir küme içindeki son *n* varlığı döndürmek için eşdeğer bir sorgu işlemi yoktur.  

#### <a name="solution"></a>Çözüm
`RowKey`Doğal olarak tarih/saat düzeninde sıralama yapan bir kullanarak varlıkları depolar, bu nedenle en son giriş tablodaki her zaman ilk ilkdir.  

Örneğin, bir çalışan tarafından gönderilen en son gider taleplerini alabilmesi için, geçerli tarih/saatten türetilmiş bir ters değer değeri kullanabilirsiniz. Aşağıdaki C# kod örneği, `RowKey` en sonuncudan en yeniye doğru sıralama yapan bir için uygun bir "ters kullanım" değeri oluşturmanın bir yolunu gösterir:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Aşağıdaki kodu kullanarak tarih/saat değerine geri dönebilirsiniz:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

Tablo sorgusu şöyle görünür:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Bu düzenin nasıl uygulanacağına karar verirken aşağıdaki noktaları göz önünde bulundurun:  

* Dize değerinin beklendiği gibi sıralandığından emin olmak için ters değer değerini önünde sıfır ile birlikte ayarlamalısınız.  
* Bir bölüm düzeyinde ölçeklenebilirlik hedeflerini bilmeniz gerekir. Etkin nokta bölümleri oluşturmamaya dikkat edin.  

#### <a name="when-to-use-this-pattern"></a>Bu düzenin kullanılacağı durumlar
Varlıklara ters tarih/saat düzeninde erişmeniz gerektiğinde veya en son eklenen varlıklara erişmeniz gerektiğinde bu düzeni kullanın.  

#### <a name="related-patterns-and-guidance"></a>İlgili düzenler ve kılavuzlar
Bu düzen uygulanırken aşağıdaki düzenler ve yönergeler de yararlı olabilir:  

* [Prepend/Append kenar yumuşatma](#prepend-append-anti-pattern)  
* [Varlıkları al](#retrieve-entities)  

### <a name="high-volume-delete-pattern"></a>Yüksek hacimli silme kalıbı
Aynı anda tüm varlıkları kendi ayrı tablolarındaki eşzamanlı silme için depolayarak yüksek hacimli varlıkların silinmesini etkinleştirin. Tabloyu silerek varlıkları silersiniz.  

#### <a name="context-and-problem"></a>Bağlam ve sorun
Birçok uygulama, artık bir istemci uygulaması için kullanılabilir olmayan eski verileri siler ya da uygulamanın başka bir depolama ortamına arşivlenmesi gerekir. Genellikle bu verileri bir tarihle belirlersiniz. Örneğin, 60 günden daha eski olan tüm oturum açma isteklerinin kayıtlarını silme gereksinimleriniz vardır.  

Olası bir tasarım, oturum açma isteğinin tarih ve saatini ' de kullanmaktır `RowKey` :  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE21.png" alt-text="Oturum açma denemesi varlığı grafiği":::

Bu yaklaşım bölüm etkin noktalarını önler çünkü uygulama, her kullanıcı için ayrı bir bölümdeki oturum açma varlıklarını ekleyebilir ve silebilir. Ancak, çok sayıda varlık varsa bu yaklaşım maliyetli ve zaman alıcı olabilir. Öncelikle, silinecek tüm varlıkların tanımlanması için bir tablo taraması gerçekleştirmeniz ve ardından bir eski varlığı silmeniz gerekir. Birden çok Delete isteğini bölümlere ayırarak eski varlıkları silmek için gereken sunucuya gidiş dönüş sayısını azaltabilirsiniz.  

#### <a name="solution"></a>Çözüm
Oturum açma girişimlerinin her günü için ayrı bir tablo kullanın. Varlıkları eklerken etkin noktaları önlemek için önceki varlık tasarımını kullanabilirsiniz. Eski varlıkların silinmesi, her gün yüzlerce ve binlerce ayrı oturum açma varlığını bulmak ve silmek yerine yalnızca bir tabloyu her gün (tek bir depolama işlemi) silme sorularından biridir.  

#### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Bu düzenin nasıl uygulanacağına karar verirken aşağıdaki noktaları göz önünde bulundurun:  

* Tasarımınız, uygulamanızın belirli varlıkları aramak, diğer verilerle bağlantı kurmak veya toplu bilgi oluşturmak gibi verileri kullanacağı diğer yolları destekliyor mu?  
* Yeni varlıkları eklerken tasarımınız etkin noktaları önmidir?  
* Aynı tablo adını sildikten sonra yeniden kullanmak istiyorsanız bir gecikme bekliyor. Her zaman benzersiz tablo adları kullanmak daha iyidir.  
* Yeni bir tabloyu ilk kez kullandığınızda, tablo depolaması erişim düzenlerini öğrenirken ve bölümleri düğümler arasında dağıttığı sırada bazı hız sınırlaması beklenir. Yeni tablolar oluşturmanız gereken sıklığı göz önünde bulundurmanız gerekir.  

#### <a name="when-to-use-this-pattern"></a>Bu düzenin kullanılacağı durumlar
Aynı anda silmeniz gereken yüksek bir varlık hacminin olması durumunda bu kalıbı kullanın.  

#### <a name="related-patterns-and-guidance"></a>İlgili düzenler ve kılavuzlar
Bu düzen uygulanırken aşağıdaki düzenler ve yönergeler de yararlı olabilir:  

* [Varlık grubu işlemleri](#entity-group-transactions)
* [Varlıkları değiştirme](#modify-entities)  

### <a name="data-series-pattern"></a>Veri serisi stili
Yaptığınız istek sayısını en aza indirmek için, tüm veri serisini tek bir varlıkta depolayın.  

#### <a name="context-and-problem"></a>Bağlam ve sorun
Yaygın bir senaryo, bir uygulamanın, genellikle hepsini bir kez alması gereken veri serisini depolaması içindir. Örneğin, uygulamanız her bir çalışanın kaç tane ım iletisi göndereceğini kaydedebilir ve sonra bu bilgileri kullanarak her bir kullanıcının önceki 24 saat içinde kaç ileti gönderdiğini çizebilirsiniz. Tek bir tasarım, her çalışan için 24 varlık depolamak olabilir:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE22.png" alt-text="İleti istatistikleri varlığının grafiği":::

Bu tasarım sayesinde, uygulamanın ileti sayısı değerini güncelleştirmesi gerektiğinde her bir çalışana yönelik varlık için kolayca bulma ve güncelleştirme yapabilirsiniz. Bununla birlikte, önceki 24 saat için etkinliğin bir grafiğini çizmek üzere bilgileri almak için, 24 varlık almanız gerekir.  

#### <a name="solution"></a>Çözüm
Her saat için ileti sayısını depolamak üzere ayrı bir özellikle aşağıdaki tasarımı kullanın:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE23.png" alt-text="Ayrılmış özelliklerle ileti istatistikleri varlığını gösteren grafik":::

Bu tasarımla, bir çalışana ait ileti sayısını belirli bir saat için güncelleştirmek üzere bir birleştirme işlemi kullanabilirsiniz. Şimdi, tek bir varlık için bir istek kullanarak grafiği çizmek için ihtiyacınız olan tüm bilgileri alabilirsiniz.  

#### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Bu düzenin nasıl uygulanacağına karar verirken aşağıdaki noktaları göz önünde bulundurun:  

* Tüm veri seriniz tek bir varlığa sığmıyorsa (bir varlık en fazla 252 özellik içerebilir), blob gibi alternatif bir veri deposu kullanın.  
* Bir varlığı aynı anda güncelleştiren birden fazla istemciniz varsa, iyimser eşzamanlılık uygulamak için **ETag** 'i kullanın. Çok sayıda istemciniz varsa, yüksek çekişme yaşayabilirsiniz.  

#### <a name="when-to-use-this-pattern"></a>Bu düzenin kullanılacağı durumlar
Tek bir varlıkla ilişkili bir veri serisini güncelleştirmeniz ve almanız gerektiğinde bu stili kullanın.  

#### <a name="related-patterns-and-guidance"></a>İlgili düzenler ve kılavuzlar
Bu düzen uygulanırken aşağıdaki düzenler ve yönergeler de yararlı olabilir:  

* [Büyük varlıklar kalıbı](#large-entities-pattern)  
* [Birleştir veya Değiştir](#merge-or-replace)  
* [Sonuçta tutarlı işlemler deseninin](#eventually-consistent-transactions-pattern) (veri serisini bir blob 'da depoluyorsanız)  

### <a name="wide-entities-pattern"></a>Geniş varlıklar kalıbı
252 'den fazla özelliği olan mantıksal varlıkları depolamak için birden çok fiziksel varlık kullanın.  

#### <a name="context-and-problem"></a>Bağlam ve sorun
Tek bir varlık 252 'den fazla özelliğe sahip olabilir (zorunlu sistem özellikleri hariç) ve toplamda 1 MB 'den fazla veri depolayamıyorum. İlişkisel bir veritabanında, genellikle yeni bir tablo ekleyerek ve aralarında 1 ' den fazla ilişki zorunlu tutarak bir satırın boyutuyla ilgili her türlü sınırı geçici olarak çözebilirsiniz.  

#### <a name="solution"></a>Çözüm
Tablo Depolamayı kullanarak, birden fazla varlığı, 252 'den fazla özelliği olan tek bir büyük ölçekli bir iş nesnesini temsil etmek üzere saklayabilirsiniz. Örneğin, son 365 gün boyunca her bir çalışan tarafından gönderilen anlık ileti iletilerinin sayısını depolamak istiyorsanız, farklı şemalarla iki varlık kullanan aşağıdaki tasarımı kullanabilirsiniz:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE24.png" alt-text="Rowkey 01 ve Rowkey 02 ile ileti istatistikleri varlığı ile ileti istatistikleri varlığını gösteren grafik":::

Her iki varlığın de birbirleriyle eşitlenmiş halde tutulması için güncelleştirilmesi gereken bir değişiklik yapmanız gerekiyorsa, bir EGT kullanabilirsiniz. Aksi takdirde, belirli bir güne ait ileti sayısını güncelleştirmek için tek bir birleştirme işlemi kullanabilirsiniz. Tek bir çalışana ait tüm verileri almak için her iki varlığı de almalısınız. Bunu hem hem de bir değeri kullanan iki verimli istek ile yapabilirsiniz `PartitionKey` `RowKey` .  

#### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Bu düzenin nasıl uygulanacağını saptarken aşağıdaki noktayı göz önünde bulundurun:  

* Tüm mantıksal varlıkların alınması, en az iki depolama işlemi içerir: bir fiziksel varlığın alınması için bir tane.  

#### <a name="when-to-use-this-pattern"></a>Bu düzenin kullanılacağı durumlar
Boyutu veya özellikleri olan varlıkları, tablo depolamadaki tek bir varlığın sınırlarını aşan varlıkları depolamanız gerektiğinde bu stili kullanın.  

#### <a name="related-patterns-and-guidance"></a>İlgili düzenler ve kılavuzlar
Bu düzen uygulanırken aşağıdaki düzenler ve yönergeler de yararlı olabilir:  

* [Varlık grubu işlemleri](#entity-group-transactions)
* [Birleştir veya Değiştir](#merge-or-replace)

### <a name="large-entities-pattern"></a>Büyük varlıklar kalıbı
Büyük özellik değerlerini depolamak için BLOB depolama kullanın.  

#### <a name="context-and-problem"></a>Bağlam ve sorun
Tek bir varlık, toplamda 1 MB 'tan fazla veri depolayamıyorum. Özelliklerden biri veya birkaçı, varlığınızın toplam boyutuna neden olan değerleri bu değere aşarsa, tüm varlığı tablo depolamadaki depokaydedemezsiniz.  

#### <a name="solution"></a>Çözüm
Bir veya daha fazla özellik büyük miktarda veri içerdiği için varlığınız 1 MB 'ı aşarsa, verileri BLOB depolama alanında saklayabilir ve sonra Blobun adresini varlıktaki bir özellikte saklayabilirsiniz. Örneğin, bir çalışanın fotoğrafını BLOB depolama alanında saklayabilir ve `Photo` çalışan varlığınızın özelliğinde fotoğrafın bağlantısını kaydedebilirsiniz:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE25.png" alt-text="Blob depolamaya işaret eden fotoğrafın bulunduğu çalışan varlığı gösteren grafik":::

#### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Bu düzenin nasıl uygulanacağına karar verirken aşağıdaki noktaları göz önünde bulundurun:  

* Tablo depolamadaki varlık ve BLOB depolamada bulunan veriler arasında nihai tutarlılığı sürdürmek için, varlıklarınızı sürdürmek üzere [sonuçta tutarlı işlem düzenlerini](#eventually-consistent-transactions-pattern) kullanın.
* Tüm varlıkların alınması, en az iki depolama işlemi içerir: bir varlık ve blob verilerini almak için bir tane.  

#### <a name="when-to-use-this-pattern"></a>Bu düzenin kullanılacağı durumlar
Boyutu, tablo depolamadaki tek bir varlık için olan sınırları aşan varlıkları depolamanız gerektiğinde bu stili kullanın.  

#### <a name="related-patterns-and-guidance"></a>İlgili düzenler ve kılavuzlar
Bu düzen uygulanırken aşağıdaki düzenler ve yönergeler de yararlı olabilir:  

* [Sonuçta tutarlı işlem kriteri](#eventually-consistent-transactions-pattern)  
* [Geniş varlıklar kalıbı](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>Prepend/Append kenar yumuşatma
Çok sayıda araya sahip olduğunuzda, eklemeleri birden fazla bölüme yayarak ölçeklenebilirliği artırın.  

#### <a name="context-and-problem"></a>Bağlam ve sorun
Depolanan varlıklarınıza ön bekliyor veya varlıklar eklemek, genellikle uygulamanın bölüm dizisinin ilk veya son bölümüne yeni varlıklar eklenmesine neden olur. Bu durumda, belirli bir zamanda tüm ekler aynı bölümde gerçekleşirken bir etkin nokta oluşturulur. Bu, tablo depolamanın yük dengelemeden birden çok düğüm arasında yer almasını önler ve büyük olasılıkla uygulamanızın bölüm için ölçeklenebilirlik hedeflerine ulaşmasına neden olur. Örneğin, çalışanlar tarafından ağ ve kaynak erişimini kaydeden bir uygulamanın durumunu göz önünde bulundurun. Aşağıdaki gibi bir varlık yapısı, işlem hacmi tek bir bölüm için ölçeklenebilirlik hedefine ulaşırsa, geçerli saatin bölümünün bir etkin noktaya dönüşmesine neden olabilir:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE26.png" alt-text="İşlem hacmi tek bir bölüm için ölçeklenebilirlik hedefine ulaşırsa, geçerli saatin bölümünün bir etkin noktaya dönüşmesine neden olan bir varlık yapısının grafiği.":::

#### <a name="solution"></a>Çözüm
Aşağıdaki alternatif varlık yapısı, uygulama olayları günlüğe kaydettiği için belirli bir bölümdeki etkin noktayı önler:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE27.png" alt-text="Yıl, ay, gün, saat ve olay KIMLIĞINI temel alan RowKey ile çalışan varlığı gösteren grafik":::

Bu örnekle, ve öğelerinin bileşik anahtarların nasıl olduğuna dikkat edin `PartitionKey` `RowKey` . , `PartitionKey` Günlüğü birden çok bölüme dağıtmak için hem departmanı hem de çalışan kimliğini kullanır.  

#### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Bu düzenin nasıl uygulanacağına karar verirken aşağıdaki noktaları göz önünde bulundurun:  

* Ekleme üzerinde etkin bölümler oluşturmayı önleyen alternatif anahtar yapısı, istemci uygulamanızın yaptığı sorguları verimli bir şekilde destekler mi?  
* Beklenen işlem hacmi, tek bir bölüm için ölçeklenebilirlik hedeflerine ulaşmanıza ve tablo depolaması ile kısıtlamanıza izin veriyor mu?  

#### <a name="when-to-use-this-pattern"></a>Bu düzenin kullanılacağı durumlar
Bir sıcak bölüme eriştiğinizde, işlem hackleriniz, tablo depolamaya göre sınırlama oranı ile sonuçlanırken, ön uç/ekleme önleme deseninin önüne kaçının.  

#### <a name="related-patterns-and-guidance"></a>İlgili düzenler ve kılavuzlar
Bu düzen uygulanırken aşağıdaki düzenler ve yönergeler de yararlı olabilir:  

* [Bileşik anahtar stili](#compound-key-pattern)  
* [Günlük kuyruk kalıbı](#log-tail-pattern)  
* [Varlıkları değiştirme](#modify-entities)  

### <a name="log-data-anti-pattern"></a>Günlük verileri Anti-model
Genellikle, günlük verilerini depolamak için tablo depolaması yerine BLOB depolama kullanmanız gerekir.  

#### <a name="context-and-problem"></a>Bağlam ve sorun
Günlük verileri için genel kullanım örneği, belirli bir tarih/saat aralığı için günlük girişlerinin bir seçimini almadır. Örneğin, uygulamanızın 15:04 ile 15:06 arasında günlüğe kaydedildiği tüm hata ve kritik iletileri belirli bir tarihte bulmak istiyorsunuz. Günlük varlıklarının kaydedileceği bölümü öğrenmek için günlük iletisinin tarih ve saatini kullanmak istemezsiniz. Bu, belirli bir zamanda, tüm günlük varlıklarının aynı değeri paylaştığından bu, bir sıcak bölüm ile sonuçlanır `PartitionKey` (bkz. [Prepend/Append Anti-model](#prepend-append-anti-pattern)). Örneğin, bir günlük iletisi için aşağıdaki varlık şeması, etkin bir bölüme neden olur, çünkü uygulama tüm günlük iletilerini geçerli tarih ve saat için bölüme Yazar:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE28.png" alt-text="Bir günlük iletisinin varlık şemasını gösteren grafik, etkin bir bölüme neden olur.":::

Bu örnekte, `RowKey` günlük iletilerinin tarih/saat düzeninde sıralanmasını sağlamak üzere günlük iletisinin tarih ve saatini içerir. `RowKey`Ayrıca, birden çok günlük iletisinin aynı tarih ve saati paylaştığı durumlarda bir ILETI kimliği de içerir.  

Farklı bir yaklaşım, `PartitionKey` uygulamanın iletileri bir dizi bölüme yazmalarını sağlayan bir kullanmaktır. Örneğin, günlük iletisinin kaynağı birçok bölüm arasında ileti dağıtmak için bir yol sağlıyorsa aşağıdaki varlık şemasını kullanabilirsiniz:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE29.png" alt-text="Günlük iletisi varlığının grafiği":::

Bununla birlikte, bu şemayla ilgili sorun belirli bir zaman aralığı için tüm günlük iletilerini almak için tablodaki her bölümde arama yapmanız gerekir.

#### <a name="solution"></a>Çözüm
Önceki bölümde, günlük girişlerini depolamak için Tablo Depolamayı kullanma girişimi ve önerilen iki yetersiz tasarım sorunu vurgulanmıştır. Sık erişimli bir bölüme yapılan bir çözüm, günlük iletilerinin düşük performans yazma riskiyle birlikte çalışır. Diğer çözüm, belirli bir zaman aralığı için günlük iletilerini almak üzere tablodaki her bölümü taramak için gerekli olan sorgu performansının düşmesine neden oldu. BLOB depolama, bu tür bir senaryo için daha iyi bir çözüm sunar ve Azure Storage Analytics 'in topladığı günlük verilerini depoladığı bir çözümdür.  

Bu bölümde, genellikle aralığa göre Sorgulayabileceğiniz verileri depolamada bu yaklaşımın bir açıklaması olarak depolama analizinin, blob depolamada günlük verilerini nasıl depoladığı özetlenmektedir.  

Depolama analizi, günlük iletilerini birden fazla blobda ayrılmış bir biçimde depolar. Ayrılmış biçim, bir istemci uygulamanın günlük iletisindeki verileri ayrıştırmayı kolaylaştırır.  

Depolama analizi, arama yaptığınız günlük iletilerini içeren Blobu (veya Blobları) bulmanızı sağlayan Bloblar için bir adlandırma kuralı kullanır. Örneğin, "Queue/2014/07/31/1800/000001. log" adlı bir blob, 31 Temmuz 2014 tarihinde 18:00 ' de başlayan saat için kuyruk hizmetiyle ilgili günlük iletileri içerir. "000001", bu dönemin ilk günlük dosyası olduğunu gösterir. Depolama Analizi Ayrıca, blob 'un meta verilerinin bir parçası olarak dosyada depolanan ilk ve son günlük iletilerinin zaman damgalarını kaydeder. BLOB depolama için API, bir ad önekini temel alarak bir kapsayıcıdaki Blobları bulmanıza izin verebilir. 18:00 ile başlayan saat için kuyruk günlüğü verilerini içeren tüm Blobları bulmak için, "Queue/2014/07/31/1800" önekini kullanabilirsiniz.  

Depolama Analizi günlük iletilerini dahili olarak arabelleğe alır ve ilgili blobu düzenli aralıklarla güncelleştirir veya en son günlük girişi toplu işlem ile yeni bir tane oluşturur. Bu, BLOB depolama için gerçekleştirmesi gereken yazma sayısını azaltır.  

Kendi uygulamanızda benzer bir çözüm gerçekleştiriyorsanız, güvenilirliği ve maliyet ve ölçeklenebilirlik arasında dengelemeyi yönetmeyi göz önünde bulundurun. Diğer bir deyişle, uygulamanızdaki güncelleştirmeleri arabelleğe alma ve bunları toplu halde blob depolamaya yazma karşılaştırması ile karşılaştırıldığında, her günlük girişini blob depolamaya yazma etkisini değerlendirin.  

#### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Günlük verilerini nasıl depolayacağınıza karar verirken aşağıdaki noktaları göz önünde bulundurun:  

* Olası sık kullanılan bölümleri engelleyen bir tablo tasarımı oluşturursanız, günlük verilerinize verimli bir şekilde erişemeyebilirsiniz.  
* Günlük verilerini işlemek için bir istemcinin genellikle birçok kayıt yüklemesi gerekir.  
* Günlük verileri genellikle yapılandırılmış olsa da, BLOB depolama daha iyi bir çözüm olabilir.  

### <a name="implementation-considerations"></a>Uygulama konuları
Bu bölümde, önceki bölümlerde açıklanan desenleri uyguladığınızda göz önünde bulundurmanız gereken bazı noktalar açıklanmaktadır. Bu bölümün çoğu, C# dilinde yazılan ve depolama Istemci kitaplığını kullanan örnekleri kullanır (sürüm 4.3.0, yazma sırasında).  

### <a name="retrieve-entities"></a>Varlıkları al
[Sorgulama için bölüm tasarımında](#design-for-querying)anlatıldığı gibi, en verimli sorgu bir nokta sorgusudur. Ancak, bazı senaryolarda birden fazla varlık almanız gerekebilir. Bu bölümde, depolama Istemci kitaplığı kullanılarak varlıkların alınması için bazı yaygın yaklaşımlar açıklanmaktadır.  

#### <a name="run-a-point-query-by-using-the-storage-client-library"></a>Depolama Istemci kitaplığını kullanarak bir nokta sorgusu çalıştırma
Nokta sorgusu çalıştırmanın en kolay yolu, tablo **Al** işlemini kullanmaktır. Aşağıdaki C# kod parçacığında gösterildiği gibi, bu işlem `PartitionKey` "Sales" değerine sahip bir varlığı ve `RowKey` "212" değerini alır:  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Bu örneğin, aldığı varlığın tür olmasını nasıl beklediğini unutmayın `EmployeeEntity` .  

#### <a name="retrieve-multiple-entities-by-using-linq"></a>LINQ kullanarak birden çok varlık alma
Depolama Istemci kitaplığı ile LINQ kullanarak birden çok varlık alabilir ve **WHERE** yan tümcesi içeren bir sorgu belirtebilirsiniz. Bir tablo taramasını önlemek için, her zaman `PartitionKey` değeri where yan tümcesine ve `RowKey` tablo ve bölüm taramalarından kaçınmak için değeri olması gerekir. Tablo depolama, WHERE yan tümcesinde kullanılmak üzere sınırlı sayıda karşılaştırma işleci (büyüktür, büyüktür veya eşittir, küçüktür, küçüktür veya eşittir, eşittir ve eşit değildir) kümesini destekler. Aşağıdaki C# kod parçacığı, son adı "B" ile başlayan tüm çalışanları ( `RowKey` son adı depoladığını varsayarak) Satış departmanındaki ( `PartitionKey` bölüm adını depoladığını varsayarak) bulur:  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

`RowKey`Daha iyi performans sağlamak için sorgunun hem a hem de ' i nasıl belirttiğinden `PartitionKey` emin olun.  

Aşağıdaki kod örneği, Fluent API kullanarak eşdeğer işlevselliği gösterir (genel olarak akıcı API 'Ler hakkında daha fazla bilgi için bkz. [Fluent API tasarlamaya yönelik en iyi uygulamalar](https://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)):  

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
> Örnek, `CombineFilters` üç filtre koşullarını dahil etmek için birden çok yöntem içerir.  
> 
> 

#### <a name="retrieve-large-numbers-of-entities-from-a-query"></a>Bir sorgudan çok sayıda varlık alma
En iyi sorgu bir `PartitionKey` değere ve bir değere göre tek bir varlık döndürür `RowKey` . Bununla birlikte, bazı senaryolarda aynı bölümden veya birçok bölümden birçok varlık döndürme gereksinimine sahip olabilirsiniz. Bu senaryolarda uygulamanızın performansını her zaman tam olarak test etmelisiniz.  

Tablo depolamaya yönelik bir sorgu, tek seferde en fazla 1.000 varlık döndürebilir ve en fazla beş saniye çalışabilir. Tablo depolama, aşağıdakilerden herhangi biri doğruysa istemci uygulamanın sonraki varlık kümesini istemesini sağlamak için bir devamlılık belirteci döndürür:

- Sonuç kümesi 1.000 'den fazla varlık içeriyor.
- Sorgu beş saniye içinde tamamlanmadı.
- Sorgu Bölüm sınırını aşar. 

Devamlılık belirteçlerinin nasıl çalıştığı hakkında daha fazla bilgi için bkz. [sorgu zaman aşımı ve sayfalandırma](/rest/api/storageservices/Query-Timeout-and-Pagination).  

Depolama Istemci kitaplığını kullanıyorsanız, tablo depolamadan varlık döndürdüğünden, devamlılık belirteçlerini otomatik olarak işleyebilir. Örneğin, aşağıdaki C# kod örneği, tablo depolaması bunları bir yanıt olarak döndürürse devamlılık belirteçlerini otomatik olarak işler:  

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

Aşağıdaki C# kodu, devamlılık belirteçlerini açıkça işler:  

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

Devamlılık belirteçlerini açık bir şekilde kullanarak, uygulamanızın sonraki veri segmentini ne zaman alacağını denetleyebilirsiniz. Örneğin, istemci uygulamanız kullanıcıların bir tabloda depolanan varlıklar aracılığıyla sayfa oluşturmasını sağladığından, bir Kullanıcı sorgu tarafından alınan tüm varlıklarda sayfa kullanmamaya karar verebilir. Uygulamanız yalnızca bir devamlılık belirteci kullanarak Kullanıcı, geçerli kesimdeki tüm varlıklar aracılığıyla disk belleğini bitirdiğinde bir sonraki segmenti alır. Bu yaklaşımın çeşitli avantajları vardır:  

* Tablo depolamadan alınacak veri miktarını sınırlayabilir ve ağ üzerinden geçiş yapabilirsiniz.  
* .NET 'te zaman uyumsuz g/ç gerçekleştirebilirsiniz.  
* Devamlılık belirtecini kalıcı depolama alanına seri hale getirebilirsiniz, böylece uygulama kilitlenmesi durumunda devam edebilirsiniz.  

> [!NOTE]
> Devamlılık belirteci genellikle 1.000 varlık içeren bir segmenti döndürür, ancak daha az olabilir. Bu durum, bir sorgunun döndürdüğü giriş sayısını, arama ölçütlerinizle eşleşen ilk n varlığı döndürmek için **Al** ' ı kullanarak sınırlandırdıysanız de olur. Tablo depolama, kalan varlıkları almanızı sağlamak için en az n varlık içeren bir segment döndürebilir.  
> 
> 

Aşağıdaki C# kodu, bir segment içinde döndürülen varlıkların sayısının nasıl değiştirileceğini göstermektedir:  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>Sunucu tarafı projeksiyonu
Tek bir varlık en fazla 255 özelliğe sahip olabilir ve boyutu en fazla 1 MB olabilir. Tabloyu sorgulayıp varlıkları aldığınızda, tüm özelliklere ihtiyaç duymayabilir ve verilerin gereksiz yere aktarılmasını önleyebilirsiniz (gecikme süresini ve maliyeti azaltmaya yardımcı olmak için). Yalnızca ihtiyacınız olan özellikleri aktarmak için sunucu tarafı projeksiyonu kullanabilirsiniz. Aşağıdaki örnek, `Email` `PartitionKey` `RowKey` `Timestamp` `ETag` sorgu tarafından seçilen varlıklardan yalnızca özelliğini (,,, ve ile birlikte) alır.  

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

`RowKey`Alınacak özellikler listesine dahil edilmese de değerin nasıl kullanılabilir olduğunu unutmayın.  

### <a name="modify-entities"></a>Varlıkları değiştirme
Depolama Istemci kitaplığı, varlıkları ekleyerek, silerek ve güncelleştirerek tablo depolamada depolanan varlıklarınızı değiştirmenize olanak sağlar. Gerekli gidiş dönüş sayısını azaltmak ve çözümünüzün performansını artırmak için, birden çok ekleme, güncelleştirme ve silme işlemini birlikte toplu olarak toplu olarak kullanabilirsiniz.  

Depolama Istemci kitaplığı bir EGT çalıştırdığında oluşturulan özel durumlar genellikle toplu işin başarısız olmasına neden olan varlığın dizinini içerir. Bu, Yumurtları kullanan kod hata ayıklaması yaparken yararlıdır.  

Ayrıca, tasarımınızın, istemci uygulamanızın eşzamanlılık ve güncelleştirme işlemlerini nasıl işlediğini nasıl etkilediğini de göz önünde bulundurmanız gerekir.  

#### <a name="managing-concurrency"></a>Eşzamanlılığı yönetme
Varsayılan olarak, tablo depolama alanı ekleme, birleştirme ve silme işlemleri için tek tek varlıkların düzeyindeki iyimser eşzamanlılık denetimleri uygular, ancak bir istemcinin tablo depolama alanını bu denetimleri atlayacak şekilde zorlaması mümkündür. Daha fazla bilgi için bkz. [Microsoft Azure depolama eşzamanlılık yönetimi](../storage/blobs/concurrency-manage.md).  

#### <a name="merge-or-replace"></a>Birleştir veya Değiştir
`Replace` `TableOperation` Sınıfının yöntemi her zaman tablo depolamadaki tüm varlığın yerini alır. Saklı varlıkta bu özellik varsa istek içine bir özellik eklemezseniz, istek bu özelliği saklı varlıktan kaldırır. Bir özelliği saklı bir varlıktan açıkça kaldırmak istemediğiniz müddetçe, istekteki her özelliği dahil etmeniz gerekir.  

`Merge` `TableOperation` Bir varlığı güncelleştirmek istediğinizde tablo depolamaya gönderilen veri miktarını azaltmak için sınıfının yöntemini kullanabilirsiniz. `Merge`Yöntemi, saklı varlıktaki tüm özellikleri, istekte yer alan varlıktaki özellik değerleriyle değiştirir. Bu yöntem, saklı varlıktaki istekte bulunmayan tüm özellikleri bozulmadan bırakır. Bu, büyük varlıklarınız varsa ve yalnızca bir istekteki az sayıda özelliği güncelleştirmeniz gerekiyorsa yararlıdır.  

> [!NOTE]
> `*Replace` `Merge` Varlık yoksa ve yöntemleri başarısız olur. Alternatif olarak, `InsertOrReplace` `InsertOrMerge` mevcut değilse yeni bir varlık oluşturan ve yöntemlerini kullanabilirsiniz.  
> 
> 

### <a name="work-with-heterogeneous-entity-types"></a>Heterojen varlık türleriyle çalışma
Tablo depolama, *şema için daha az* tablo deposudur. Bu, tek bir tablonun birden çok türden varlıkları depolayabileceği ve tasarımınızda harika esneklik sağladığı anlamına gelir. Aşağıdaki örnek, hem çalışan hem de departman varlıklarını depolayan bir tabloyu göstermektedir:  

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
<th>FirstName</th>
<th>LastName</th>
<th>Yaş</th>
<th>E-posta</th>
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
<th>FirstName</th>
<th>LastName</th>
<th>Yaş</th>
<th>E-posta</th>
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
<th>FirstName</th>
<th>LastName</th>
<th>Yaş</th>
<th>E-posta</th>
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

Her varlığın `PartitionKey` , `RowKey` , ve değerleri hala olmalıdır `Timestamp` , ancak herhangi bir özellik kümesine sahip olabilir. Ayrıca, bu bilgileri bir yere depolamayı seçmediğiniz müddetçe bir varlığın türünü belirten bir şey yoktur. Varlık türünü belirlemek için iki seçenek vardır:  

* Varlık türünü `RowKey` (veya belki de büyük olasılıkla) önüne ekleyin `PartitionKey` . Örneğin, `EMPLOYEE_000123` veya `DEPARTMENT_SALES` `RowKey` değerlerini.  
* Aşağıdaki tabloda gösterildiği gibi, varlık türünü kaydetmek için ayrı bir özellik kullanın.  

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
<th>FirstName</th>
<th>LastName</th>
<th>Yaş</th>
<th>E-posta</th>
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
<th>FirstName</th>
<th>LastName</th>
<th>Yaş</th>
<th>E-posta</th>
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
<th>FirstName</th>
<th>LastName</th>
<th>Yaş</th>
<th>E-posta</th>
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

Varlık türü için önceden bekleyen ilk seçenek, `RowKey` farklı türlerde iki varlığın aynı anahtar değerine sahip olabileceği bir olasılık olduğunda yararlıdır. Ayrıca, bölümünde aynı türdeki varlıkları birlikte gruplandırır.  

Bu bölümde ele alınan teknikler özellikle[Devralma ilişkileri](#inheritance-relationships)hakkındaki tartışmayla ilgilidir.  

> [!NOTE]
> İstemci uygulamalarının POCO nesnelerini ve farklı sürümlerle çalışmasını sağlamak için varlık türü değerine bir sürüm numarası dahil etmeyi göz önünde bulundurun.  
> 
> 

Bu bölümün geri kalanında, aynı tablodaki birden çok varlık türüyle çalışmayı kolaylaştıran depolama Istemci kitaplığındaki bazı özellikler açıklanmaktadır.  

#### <a name="retrieve-heterogeneous-entity-types"></a>Heterojen varlık türlerini al
Depolama Istemci kitaplığını kullanıyorsanız, birden fazla varlık türüyle çalışmak için üç seçeneğiniz vardır.  

Belirli ve değerleri ile depolanan varlık türünü biliyorsanız, varlığı alırken `RowKey` `PartitionKey` varlık türünü belirtebilirsiniz. Bunu, türü varlıkları alan önceki iki örnekte gördünüz `EmployeeEntity` : [depolama istemci kitaplığını kullanarak bir nokta sorgusu çalıştırma](#run-a-point-query-by-using-the-storage-client-library) ve [LINQ kullanarak birden çok varlık alma](#retrieve-multiple-entities-by-using-linq).  

İkinci seçenek `DynamicTableEntity` , somut POCO varlık türü yerine türü (bir özellik paketi) kullanmaktır. Bu seçenek, varlığı .NET türlerine serileştirmek ve serisini kaldırmak zorunda olmadığından performansı da iyileştirebilir. Aşağıdaki C# kodu, tablodaki farklı türlerin birden çok varlığını potansiyel olarak alır, ancak tüm varlıkları örnek olarak döndürür `DynamicTableEntity` . Ardından, her bir `EntityType` varlığın türünü belirlemekte özelliğini kullanır:  

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

Diğer özellikleri almak için, `TryGetValue` sınıfının özelliğinde yöntemini kullanmanız gerekir `Properties` `DynamicTableEntity` .  

Üçüncü bir seçenek `DynamicTableEntity` türü ve örneği kullanarak birleştirme `EntityResolver` . Bu, aynı sorgudaki birden çok POCO türüne çözüm yapmanızı sağlar. Bu örnekte, temsilci, `EntityResolver` `EntityType` sorgunun döndürdüğü iki varlık türü arasında ayrım yapmak için özelliğini kullanıyor. `Resolve`Yöntemi `resolver` örnekleri örneklere çözümlemek için temsilciyi kullanır `DynamicTableEntity` `TableEntity` .  

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

#### <a name="modify-heterogeneous-entity-types"></a>Heterojen varlık türlerini değiştirme
Silmek için bir varlığın türünü bilmeniz gerekmez ve onu eklediğinizde bir varlığın türünü her zaman bilirsiniz. Ancak, türünü `DynamicTableEntity` bilmeden bir varlığı güncelleştirmek için ve bır POCO varlık sınıfı kullanmadan bu türü kullanabilirsiniz. Aşağıdaki kod örneği tek bir varlık alır ve `EmployeeCount` güncelleştirmeden önce özelliğin var olduğunu denetler.  

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

### <a name="control-access-with-shared-access-signatures"></a>Paylaşılan erişim imzaları ile erişimi denetleme
Doğrudan tablo depolamayla kimlik doğrulaması gerektirmeden, istemci uygulamalarının tablo varlıklarını doğrudan değiştirmesine (ve sorgulayabilmesi) için paylaşılan erişim imzası (SAS) belirteçlerini kullanabilirsiniz. Genellikle, uygulamanızda SAS kullanmanın üç ana avantajı vardır:  

* Bu cihazın tablo depolamadaki varlıklara erişmesine ve bunları değiştirmesine izin vermek için depolama hesabı Anahtarınızı güvenli olmayan bir platforma (bir mobil cihaz gibi) dağıtmanız gerekmez.  
* Web ve çalışan rollerinin varlıklarınızı yönetirken gerçekleştirdiği bazı işleri devretmek için bu işlemleri çalıştırabilirsiniz. Son Kullanıcı bilgisayarları ve mobil cihazlar gibi istemci cihazlara yük devreatayabilirsiniz.  
* Bir istemciye kısıtlanmış ve zaman sınırlı bir izin kümesi atayabilirsiniz (örneğin, belirli kaynaklara salt okuma erişimine izin verme).  

Tablo depolama ile SAS belirteçlerini kullanma hakkında daha fazla bilgi için bkz. [paylaşılan erişim imzaları (SAS) kullanma](../storage/common/storage-sas-overview.md).  

Ancak, hala tablo depolamadaki varlıklara bir istemci uygulaması veren SAS belirteçlerini oluşturmanız gerekir. Bunu, depolama hesabı Anahtarlarınıza güvenli erişim sağlayan bir ortamda yapın. Genellikle, SAS belirteçlerini oluşturmak ve varlıklarınıza erişmesi gereken istemci uygulamalarına göndermek için bir Web veya çalışan rolü kullanırsınız. SAS belirteçlerini istemcilere oluşturma ve sunma konusunda hala bir ek yük olduğundan, özellikle de yüksek hacimli senaryolarda bu yükü azaltmanın en iyi şekilde göz önünde bulundurmanız gerekir.  

Bir tablodaki varlıkların bir alt kümesine erişim veren bir SAS belirteci oluşturmak mümkündür. Varsayılan olarak, tüm tablo için bir SAS belirteci oluşturursunuz. Ancak SAS belirtecinin bir dizi değere veya bir aralığa ya da değere erişim izni vermesini belirtmek de mümkündür `PartitionKey` `PartitionKey` `RowKey` . Her kullanıcının SAS belirteci yalnızca tablo depolamadaki kendi varlıklarına erişmelerine izin verecek şekilde, sisteminizin bireysel kullanıcıları için SAS belirteçleri oluşturmayı tercih edebilirsiniz.  

### <a name="asynchronous-and-parallel-operations"></a>Zaman uyumsuz ve paralel işlemler
İsteklerinizi birden çok bölüme yaymakta olduğunuz için, zaman uyumsuz veya paralel sorgular kullanarak aktarım hızını ve istemci yanıt hızını artırabilirsiniz.
Örneğin, tablolarınıza paralel olarak erişen iki veya daha fazla çalışan rolü örneği olabilir. Belirli bölüm kümelerinden sorumlu tek tek çalışan rolleriniz olabilir veya her biri bir tablodaki tüm bölümlere erişebilen birden fazla çalışan rolü örneğine sahip olabilirsiniz.  

İstemci örneği içinde, depolama işlemlerini zaman uyumsuz olarak çalıştırarak aktarım hızını artırabilirsiniz. Depolama Istemci kitaplığı, zaman uyumsuz sorguları ve değişiklikleri yazmayı kolaylaştırır. Örneğin, aşağıdaki C# kodunda gösterildiği gibi, bir bölümdeki tüm varlıkları alan zaman uyumlu yöntemle başlayabilirsiniz:  

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

Sorgunun zaman uyumsuz olarak çalışması için aşağıdaki gibi kolayca bu kodu değiştirebilirsiniz:  

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

* Yöntem imzası artık `async` değiştiriciyi ekler ve bir `Task` örnek döndürür.  
* Yöntemi sonuçları almak için çağırmak yerine, yöntemi `ExecuteSegmented` artık `ExecuteSegmentedAsync` yöntemini çağırır. Yöntemi `await` sonuçları zaman uyumsuz olarak almak için değiştiricisini kullanır.  

İstemci uygulaması, bu yöntemi parametresi için farklı değerlerle birden çok kez çağırabilir `department` . Her sorgu ayrı bir iş parçacığında çalışır.  

`Execute` `TableQuery` `IEnumerable` Arabirim zaman uyumsuz numaralandırmayı desteklemediğinden, sınıfında yönteminin zaman uyumsuz bir sürümü yoktur.  

Ayrıca varlıkları zaman uyumsuz olarak ekleyebilir, güncelleştirebilir ve silebilirsiniz. Aşağıdaki C# örneği, bir çalışan varlığı eklemek veya değiştirmek için basit ve zaman uyumlu bir yöntem göstermektedir:  

```csharp
private static void SimpleEmployeeUpsert(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = employeeTable
        .Execute(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Bu kodu, güncelleştirmenin zaman uyumsuz olarak çalışması için, aşağıdaki gibi kolayca değiştirebilirsiniz:  

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

* Yöntem imzası artık `async` değiştiriciyi ekler ve bir `Task` örnek döndürür.  
* `Execute`Varlığı güncelleştirmek için yöntemini çağırmak yerine, yöntemi şimdi `ExecuteAsync` yöntemini çağırır. Yöntemi `await` sonuçları zaman uyumsuz olarak almak için değiştiricisini kullanır.  

İstemci uygulaması, bu gibi birden çok zaman uyumsuz yöntemi çağırabilir ve her bir yöntem çağrısı ayrı bir iş parçacığında çalışır.