---
title: Azure Tablo depolamada ölçeklenebilir ve performanslı tablolar tasarlayın. | Microsoft Belgeleri
description: Azure Tablo depolamada ölçeklenebilir ve performans tabloları tasarlamayı öğrenin. Tablo bölümlerini, varlık grubu Işlemlerini ve kapasite ve maliyet konularını gözden geçirin.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.topic: article
ms.date: 03/09/2020
ms.subservice: tables
ms.openlocfilehash: 8f3bd2a998066804bfb589e3262ac5e68db601fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93306944"
---
# <a name="design-scalable-and-performant-tables"></a>Ölçeklenebilir ve performansa yönelik tablolar tasarlama

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Ölçeklenebilir ve performanslı tablolar tasarlamak için performans, ölçeklenebilirlik ve maliyet gibi faktörleri göz önünde bulundurmanız gerekir. Daha önce ilişkisel veritabanları için şemaları tasarlamış olmanız durumunda bu noktalar tanıdık gelecektir, ancak Azure Tablo hizmeti depolama modeli ve ilişkisel modeller arasında bazı benzerlikler vardır. Ayrıca önemli farklılıklar da vardır. Bu farklılıklar genellikle, ilişkisel veritabanlarına tanıdık bir kişiye sayaç sezgisel veya yanlış görünebilen farklı tasarımlara yol açabilir, ancak Azure Tablo hizmeti gibi bir NoSQL anahtar/değer deposu için tasarlandıysanız anlamlı hale gelecektir. Tasarım farklarınızın birçoğu, tablo hizmeti 'nin, verilerin milyarlarca varlık (veya ilişkisel veritabanı terminolojisinde satır) içerebilen bulut ölçekli uygulamaları desteklemek üzere tasarlandığına veya yüksek işlem birimlerini desteklemesi gereken veri kümelerine yönelik olarak tasarlanmasıdır. Bu nedenle, verilerinizi nasıl depoladığınıza ve tablo hizmeti 'nin nasıl çalıştığını nasıl anlayacağınızı göz önünde bulundurmalısınız. İyi tasarlanmış bir NoSQL veri deposu, çözümünüzün daha fazla ve ilişkisel bir veritabanı kullanan bir çözüme kıyasla daha düşük bir maliyetle ölçeklendirilmesini sağlayabilir. Bu kılavuz, bu konularda size yardımcı olur.  

## <a name="about-the-azure-table-service"></a>Azure Tablo hizmeti hakkında
Bu bölümde, özellikle performans ve ölçeklenebilirlik için tasarlanmasıyla ilgili olan tablo hizmeti 'nin bazı önemli özellikleri vurgulanmıştır. Azure depolama ve tablo hizmeti için yeni başladıysanız, ilk olarak [Microsoft Azure depolama giriş sayfasını](../../storage/common/storage-introduction.md) okuyun ve bu makalenin geri kalanını okumadan önce [.NET kullanarak Azure Tablo depolama ile çalışmaya](../../cosmos-db/tutorial-develop-table-dotnet.md) başlayın. Bu kılavuzun odağı tablo hizmeti üzerinde olsa da, Azure kuyruğu ve BLOB hizmetlerinin tartışılması ve bunları tablo hizmeti ile nasıl kullanabileceğinizi de kapsar.  

Tablo hizmeti nedir? Adından bekleneceğiniz gibi tablo hizmeti, verileri depolamak için tablolu bir biçim kullanır. Standart terminolojisinde, tablonun her satırı bir varlığı temsil eder ve sütunlar ilgili varlığın çeşitli özelliklerini depolar. Her varlığın benzersiz şekilde tanımlanması için bir çift anahtar ve tablo hizmetinin varlığın en son ne zaman güncelleştirildiğini izlemek için kullandığı bir zaman damgası sütunu vardır. Zaman damgası otomatik olarak uygulanır ve zaman damgasının rastgele bir değerle el ile üzerine yazabilirsiniz. Tablo hizmeti, iyimser eşzamanlılık yönetimi için bu son değiştirme zaman damgasını (LMT) kullanır.  

> [!NOTE]
> Tablo hizmeti REST API işlemler, LMT 'den türetilen bir **ETag** değeri de döndürür. Bu belge, aynı temel verilere başvurdukları için ETag ve LOMT birbirinin yerine terimlerini kullanır.  
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


Şimdiye kadar, bu veriler ilişkisel bir veritabanındaki bir tabloya benzer ve anahtar farklılıkları zorunlu sütunlar ve aynı tabloda birden çok varlık türünü depolayabilme özelliği ile benzer şekilde görünür. Ayrıca, **ad** veya **yaş** gibi Kullanıcı tanımlı özelliklerin her biri, ilişkisel veritabanındaki bir sütun gibi bir veri türüne (örneğin, tam sayı veya dize) sahiptir. İlişkisel bir veritabanının aksine, tablo hizmetinin şema-daha az doğası, bir özelliğin her varlıkta aynı veri türüne sahip olması gerektiği anlamına gelir. Karmaşık veri türlerini tek bir özellikte depolamak için JSON veya XML gibi serileştirilmiş bir biçim kullanmanız gerekir. Desteklenen veri türleri, desteklenen tarih aralıkları, adlandırma kuralları ve boyut kısıtlamaları gibi tablo hizmeti hakkında daha fazla bilgi için bkz. [Tablo hizmeti veri modelini anlama](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model).

**Partitionkey** ve **rowkey** seçiminiz, iyi tablo tasarımı için temel seçenektir. Bir tabloda depolanan her varlık **partitionkey** ve **rowkey** öğesinin benzersiz bir birleşimine sahip olmalıdır. İlişkisel bir veritabanı tablosundaki anahtarlarda olduğu gibi, **partitionkey** ve **rowkey** değerleri, hızlı görünümü etkinleştirmek için kümelenmiş bir dizin oluşturmak üzere dizinlenir. Ancak tablo hizmeti ikincil dizinler oluşturmaz, bu nedenle **partitionkey** ve **rowkey** yalnızca dizinli özelliklerdir. [Tablo tasarım desenlerinde](table-storage-design-patterns.md) açıklanan bazı desenler, bu görünür sınırlamayı geçici olarak nasıl çözebileceğinizi göstermektedir.  

Bir tablo bir veya daha fazla bölümden oluşur ve yaptığınız tasarım kararlarının birçoğu, çözümünüzü iyileştirmek için uygun bir **partitionkey** ve **rowkey** seçme konusunda olacaktır. Bir çözüm, bölümler halinde düzenlenmiş tüm varlıklarınızı içeren tek bir tablodan oluşabilir, ancak genellikle bir çözümde birden çok tablo vardır. Tablolar, varlıklarınızı mantıksal olarak düzenlemenize, erişim denetim listelerini kullanarak verilere erişimi yönetmenize yardımcı olur ve tek bir depolama işlemi kullanarak tüm tabloyu bırakabilirsiniz.  

## <a name="table-partitions"></a>Tablo bölümleri
Hesap adı, tablo adı ve **Partitionkey** birlikte tablo hizmetinin varlığı depoladığı depolama hizmeti içindeki bölümü belirler. Varlıklar için adresleme şemasının bir parçası olan bölümler, işlemler için bir kapsam tanımlar (aşağıdaki [varlık grubu işlemlerine](#entity-group-transactions) bakın) ve tablo hizmetinin ölçeklendirme temelini oluşturur. Bölümler hakkında daha fazla bilgi için bkz. [Tablo Depolaması Için performans ve ölçeklenebilirlik denetim listesi](storage-performance-checklist.md).  

Tablo hizmetinde, tek bir düğüm bir veya daha fazla tam bölüm hizmetleri ve hizmet, düğümler arasında dinamik olarak yük dengeleme bölümlerine göre ölçeklendirilir. Bir düğüm Load altındaysa, tablo hizmeti bu düğüm tarafından hizmet verilen bölümlerin aralığını farklı düğümlere *bölebilir* ; trafik alt taraflarından, hizmet, Bölüm aralıklarını sessiz düğümlerden tek bir düğüme geri *birleştirebilirler* .  

Tablo hizmetinin iç ayrıntıları ve hizmetin bölümleri nasıl yönettiği hakkında daha fazla bilgi için, bkz. [Microsoft Azure depolama: güçlü tutarlılık Içeren yüksek oranda kullanılabilir bir bulut depolama hizmeti](/archive/blogs/windowsazurestorage/sosp-paper-windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency).  

## <a name="entity-group-transactions"></a>Varlık grubu Işlemleri
Tablo hizmetinde, varlık grubu Işlemleri (Yumurlar), birden çok varlık arasında atomik güncelleştirmeler gerçekleştirmeye yönelik tek yerleşik mekanizmadır. Yumurtları bazen *toplu* işlem olarak da adlandırılır. Yumurtları yalnızca aynı bölümde depolanan varlıklar üzerinde çalışabilir (diğer bir deyişle, belirli bir tabloda aynı bölüm anahtarını paylaşır). Bu nedenle, birden çok varlık genelinde atomik işlem davranışına ihtiyaç duyuyorsanız, bu varlıkların aynı bölümde olduğundan emin olmanız gerekir. Bu genellikle, farklı varlık türleri için birden çok tablo kullanmayan aynı tabloda (ve bölümünde) birden çok varlık türünü tutmanın bir nedenidir. Tek bir EGT, en fazla 100 varlık üzerinde çalışabilir.  İşlenmek üzere birden çok eş zamanlı Yumurtları gönderirseniz, bu yumurtların Yumurtları genelinde ortak olan varlıklarda çalışmamaları güvence altına almak önemlidir; Aksi takdirde, işleme gecikebilir.

Yumurtları, tasarımınızda değerlendirmenize yönelik potansiyel bir ticaretle da tanıtılmaktadır. Diğer bir deyişle, Azure 'un düğümler arasında yük dengeleme isteklerini daha fazla fırsata sahip olduğu için daha fazla bölüm kullanmak uygulamanızın ölçeklenebilirliğini artırır. Ancak daha fazla bölüm kullanılması, uygulamanızın Atomik işlemler gerçekleştirme becerisini sınırlayabilir ve verileriniz için güçlü tutarlılık sağlayabilir. Ayrıca, tek bir düğüm için bekleneceğiniz işlem verimini sınırlayabilen bir bölüm düzeyinde belirli ölçeklenebilirlik hedefleri vardır. Azure Standart depolama hesapları için ölçeklenebilirlik hedefleri hakkında daha fazla bilgi için bkz. [Standart depolama hesapları Için ölçeklenebilirlik hedefleri](../common/scalability-targets-standard-account.md). Tablo hizmeti için ölçeklenebilirlik hedefleri hakkında daha fazla bilgi için bkz. [Tablo depolama Için ölçeklenebilirlik ve performans hedefleri](scalability-targets.md).

## <a name="capacity-considerations"></a>Kapasiteye ilişkin önemli noktalar

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="cost-considerations"></a>Maliyetle ilgili konular
Tablo Depolaması nispeten ucuzdur, ancak herhangi bir tablo hizmeti çözümünün değerlendirmesi kapsamında hem kapasite kullanımı hem de işlem miktarı için maliyet tahminleri dahil edilmelidir. Ancak, Çoğu senaryoda, çözümünüzün performansını veya ölçeklenebilirliğini geliştirmek için Normalleştirilmemiş veya yinelenen verilerin depolanması geçerli bir yaklaşımdır. Fiyatlandırma hakkında daha fazla bilgi için bkz. [Azure Depolama fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="next-steps"></a>Sonraki adımlar

- [Tablo tasarım desenleri](table-storage-design-patterns.md)
- [İlişkileri modelleme](table-storage-design-modeling.md)
- [Sorgulama için tasarım](table-storage-design-for-query.md)
- [Tablo verilerini şifreleme](table-storage-design-encrypt-data.md)
- [Veri değişikliği için tasarım](table-storage-design-for-modification.md)