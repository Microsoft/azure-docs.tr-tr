---
title: Azure Veri Kataloğu geliştirici kavramları
description: Azure Veri Kataloğu kavramsal modelinde, Katalog REST API kullanıma sunulan temel kavramlara giriş.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 80adc98255cfc9145d583ac775bbc490d599234e
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976821"
---
# <a name="azure-data-catalog-developer-concepts"></a>Azure Veri Kataloğu geliştirici kavramları
Microsoft **Azure Veri Kataloğu** , veri kaynağı bulma ve veri kaynağı meta verilerini alma için yetenekler sağlayan, tam olarak yönetilen bir bulut hizmetidir. Geliştiriciler hizmeti REST API 'Leri aracılığıyla kullanabilir. Hizmette uygulanan kavramların anlaşılmasına, geliştiricilerin **Azure Veri Kataloğu**ile başarıyla tümleştirileceği önemli bir öneme sahiptir.

## <a name="key-concepts"></a>Önemli kavramlar
**Azure Veri Kataloğu** kavramsal modeli, dört temel kavramı temel alır: **Katalog**, **Kullanıcılar**, **varlıklar**ve **ek açıklamalar**.

![Azure Veri Kataloğu kavramsal model çizimi](./media/data-catalog-developer-concepts/concept2.png)

*Şekil 1-Azure Veri Kataloğu Basitleştirilmiş kavramsal model*

### <a name="catalog"></a>Katalog
Bir **Katalog** , kuruluşun depoladığı tüm meta veriler için en üst düzey kapsayıcıdır. Azure hesabı başına bir **katalogda** izin verilir. Kataloglar bir Azure aboneliğine bağlıdır, ancak bir hesap birden çok aboneliğe sahip olsa bile, belirli bir Azure hesabı için yalnızca bir **Katalog** oluşturulabilir.

Bir katalog, **kullanıcıları** ve **varlıkları**içerir.

### <a name="users"></a>Kullanıcılar
Kullanıcılar, katalogda eylem gerçekleştirme izinleri olan güvenlik ilkeleridir (katalogda arama yapın, öğe ekleme, düzenleme veya kaldırma, vb.).

Bir kullanıcının sahip olduğu birkaç farklı rol vardır. Roller hakkında daha fazla bilgi için bkz. roller ve yetkilendirme bölümü.

Bireysel kullanıcılar ve güvenlik grupları eklenebilir.

Azure Veri Kataloğu, kimlik ve erişim yönetimi için Azure Active Directory kullanır. Her bir katalog kullanıcısının hesap için Active Directory bir üyesi olması gerekir.

### <a name="assets"></a>Varlıklar
**Katalog** , veri varlıkları içerir. **Varlıklar** , katalog tarafından yönetilen ayrıntı düzeyi birimidir.

Bir varlığın ayrıntı düzeyi veri kaynağına göre farklılık gösterir. SQL Server veya Oracle Database için bir varlık bir tablo veya görünüm olabilir. SQL Server Analysis Services için bir varlık bir ölçü, bir boyut veya bir ana performans göstergesi (KPI) olabilir. SQL Server Reporting Services, bir varlık bir rapordur.

Bir **varlık** , bir katalogdan eklediğiniz veya kaldırdığınız şeydir. **Aramadan**geri alacağınız sonuç birimidir.

Bir **varlık** adından, konumundan ve türünden, daha fazla açıklama içeren açıklamalardan oluşur.

### <a name="annotations"></a>Açıklamaları
Ek açıklamalar, varlıklar hakkındaki meta verileri temsil eden öğelerdir.

Ek açıklamaların örnekleri açıklama, Etiketler, şema, belgeler, vb. Varlık türleri ve ek açıklama türlerinin tam listesi varlık nesne modeli bölümünde bulunur.

## <a name="crowdsourcing-annotations-and-user-perspective-multiplicity-of-opinion"></a>Crowdkaynağını belirleme ek açıklamaları ve Kullanıcı perspektifi (önemli çeşitlilikte)
Azure Veri Kataloğu 'nun önemli bir yönü, sistemdeki meta verilerin listesini oluşturmayı destekler. Wiki yaklaşımından farklı olarak, yalnızca bir sorun ve son yazıcı olduğunda Azure Veri Kataloğu modeli, birden çok OPTA 'ın sistemde yan yana canlı çalışmasına izin verir.

Bu yaklaşım, farklı kullanıcıların belirli bir varlık üzerinde farklı perspektiflere sahip olduğu kurumsal verilerin gerçek dünyasını yansıtır:

* Bir veritabanı Yöneticisi hizmet düzeyi sözleşmeleri veya toplu ETL işlemleri için kullanılabilir işlem penceresi hakkında bilgi verebilir
* Bir veri, varlığın uygulandığı iş süreçleriyle ilgili bilgiler veya işletmenin kendisine uyguladığı sınıflandırmalar hakkında bilgi verebilir
* Finans analisti, dönem sonu raporlama görevleri sırasında verilerin nasıl kullanıldığı hakkında bilgi verebilir

Bu örneği desteklemek için, her Kullanıcı-DBA, veri ve Analist, kataloğa kaydedilmiş tek bir tabloya bir açıklama ekleyebilir. Tüm açıklamalar sistemde tutulur ve Azure Veri Kataloğu portalında tüm açıklamalar görüntülenir.

Bu model nesne modelindeki öğelerin çoğuna uygulanır, bu nedenle JSON yükünde nesne türleri genellikle tek bir tahmin beklenbileceğiniz özellikler için dizilerdir.

Örneğin, varlık kökü altında bir açıklama nesneleri dizisi bulunur. Array özelliği "Descriptions" olarak adlandırılmıştır. Bir açıklama nesnesinin bir özellik açıklaması vardır. Bu model, açıklama türü olan her kullanıcının, Kullanıcı tarafından sağlanan değer için oluşturulan bir açıklama nesnesini aldığı bir açıklamadır.

UX daha sonra birleşiminin nasıl görüntüleneceğini seçebilir. Görüntülenmek üzere üç farklı desen vardır.

* En basit model "tümünü göster" dir. Bu düzende, tüm nesneler liste görünümünde gösterilir. Azure Veri Kataloğu portalı UX, açıklama için bu kalıbı kullanır.
* Başka bir model "Merge". Bu düzende, farklı kullanıcılardan alınan tüm değerler birlikte birleştirilir ve yinelenen kaldırılır. Azure Veri Kataloğu portalı UX içindeki bu modele örnek olarak Etiketler ve uzmanlar özellikleri verilebilir.
* Üçüncü bir model "son yazıcı kazandı". Bu düzende yalnızca içinde yazılan en son değer gösterilir. friendlyName bu düzene bir örnektir.

## <a name="asset-object-model"></a>Varlık nesne modeli
Temel kavramlar bölümünde sunulan **Azure Veri Kataloğu** nesne modeli, varlıklar veya ek açıklamalar içerebilen öğeleri içerir. Öğelerin özellikleri vardır, bu, isteğe bağlı veya gerekli olabilir. Bazı özellikler tüm öğeler için geçerlidir. Bazı özellikler tüm varlıklar için geçerlidir. Bazı özellikler yalnızca belirli varlık türlerine uygulanır.

### <a name="system-properties"></a>Sistem özellikleri
<table><tr><td><b>Özellik adı</b></td><td><b>Veri türü</b></td><td><b>Yorumlar</b></td></tr><tr><td>timestamp</td><td>DateTime</td><td>Öğenin son değiştirildiği zaman. Bu alan, bir öğe eklendiğinde ve bir öğe her güncelleştirildiği zaman sunucu tarafından oluşturulur. Bu özelliğin değeri, yayımlama işlemleri girişi sırasında yok sayılır.</td></tr><tr><td>id</td><td>Uri</td><td>Öğenin mutlak URL 'si (salt okunurdur). Bu öğe için benzersiz adreslenebilir URI 'dir.  Bu özelliğin değeri, yayımlama işlemleri girişi sırasında yok sayılır.</td></tr><tr><td>türü</td><td>Dize</td><td>Varlığın türü (salt okunurdur).</td></tr><tr><td>etag</td><td>Dize</td><td>Katalogdaki öğeleri güncelleştiren işlemler gerçekleştirilirken iyimser eşzamanlılık denetimi için kullanılabilen öğenin sürümüne karşılık gelen bir dize. "*", herhangi bir değeri eşleştirmek için kullanılabilir.</td></tr></table>

### <a name="common-properties"></a>Ortak özellikler
Bu özellikler tüm kök varlık türleri ve tüm ek açıklama türleri için geçerlidir.

<table>
<tr><td><b>Özellik adı</b></td><td><b>Veri türü</b></td><td><b>Yorumlar</b></td></tr>
<tr><td>fromSourceSystem</td><td>Boole değeri</td><td>Öğenin verilerinin bir kaynak sistemden (SQL Server veritabanı, Oracle Database gibi) veya bir kullanıcı tarafından yazılmış olup olmadığını gösterir.</td></tr>
</table>

### <a name="common-root-properties"></a>Ortak kök özellikleri
<p>
Bu özellikler tüm kök varlık türleri için geçerlidir.

<table><tr><td><b>Özellik adı</b></td><td><b>Veri türü</b></td><td><b>Yorumlar</b></td></tr><tr><td>name</td><td>Dize</td><td>Veri kaynağı konum bilgileri 'nden türetilmiş bir ad</td></tr><tr><td>DSL</td><td>DataSourceLocation</td><td>Veri kaynağını benzersiz bir şekilde açıklar ve varlığın tanımlayıcılarından biridir. (Bkz. çift kimlik bölümü).  DSL yapısı protokol ve kaynak türüne göre farklılık gösterir.</td></tr><tr><td>dataSource</td><td>Datasourceınfo</td><td>Varlık türü hakkında daha fazla ayrıntı.</td></tr><tr><td>lastRegisteredBy</td><td>SecurityPrincipal</td><td>Bu varlığı en son kaydeden kullanıcıyı açıklar.  Kullanıcı için benzersiz kimliği (UPN) ve görünen adı (soyadı ve adı) içerir.</td></tr><tr><td>Kimliği</td><td>Dize</td><td>Veri kaynağı için kapsayıcı varlığının kimliği. Bu özellik kapsayıcı türü için desteklenmiyor.</td></tr></table>

### <a name="common-non-singleton-annotation-properties"></a>Ortak tekil olmayan ek açıklama özellikleri
Bu özellikler, tekil olmayan ek açıklama türleri (varlık başına birden fazla olmasına izin verilen ek açıklamalar) için geçerlidir.

<table>
<tr><td><b>Özellik adı</b></td><td><b>Veri türü</b></td><td><b>Yorumlar</b></td></tr>
<tr><td>key</td><td>Dize</td><td>Geçerli koleksiyonda ek açıklamayı benzersiz bir şekilde tanımlayan Kullanıcı tarafından belirtilen anahtar. Anahtar uzunluğu 256 karakteri aşamaz.</td></tr>
</table>

### <a name="root-asset-types"></a>Kök varlık türleri
Kök varlık türleri, katalogda kayıtlı olabilecek çeşitli veri varlıkları türlerini temsil eden türlerdir. Her kök türü için, görünümde yer alan varlık ve ek açıklamaları açıklayan bir görünüm vardır. Bir varlık REST API kullanılarak yayımlandığında, ilgili {view_name} URL segmentinde görünüm adı kullanılmalıdır.

<table><tr><td><b>Varlık türü (görünüm adı)</b></td><td><b>Ek özellikler</b></td><td><b>Veri türü</b></td><td><b>İzin verilen ek açıklamalar</b></td><td><b>Yorumlar</b></td></tr><tr><td>Table ("Tables")</td><td></td><td></td><td>Açıklama<p>FriendlyName<p>Etiket<p>Şema<p>ColumnDescription<p>ColumnTag<p> Uzman<p>Önizleme<p>AccessInstruction<p>TableDataProfile<p>ColumnDataProfile<p>ColumnDataClassification<p>Belgeler<p></td><td>Tablo tüm tablo verilerini temsil eder.  Örneğin: SQL tablosu, SQL görünümü, Analysis Services tablolu tablo, Analysis Services çok boyutlu boyut, Oracle tablosu vb.   </td></tr><tr><td>Ölçü ("ölçüler")</td><td></td><td></td><td>Açıklama<p>FriendlyName<p>Etiket<p>Uzman<p>AccessInstruction<p>Belgeler<p></td><td>Bu tür bir Analysis Services ölçüsünü temsil eder.</td></tr><tr><td></td><td>ölçü</td><td>Sütun</td><td></td><td>Ölçüyü açıklayan meta veriler</td></tr><tr><td></td><td>ıshesaplanan </td><td>Boole değeri</td><td></td><td>Ölçünün hesaplanıp hesaplanmayacağını belirtir.</td></tr><tr><td></td><td>measureGroup</td><td>Dize</td><td></td><td>Ölçü için fiziksel kapsayıcı</td></tr><td>KPI ("KPI 'lar")</td><td></td><td></td><td>Açıklama<p>FriendlyName<p>Etiket<p>Uzman<p>AccessInstruction<p>Belgeler</td><td></td></tr><tr><td></td><td>measureGroup</td><td>Dize</td><td></td><td>Ölçü için fiziksel kapsayıcı</td></tr><tr><td></td><td>goalExpression</td><td>Dize</td><td></td><td>Bir MDX sayısal ifadesi veya KPI 'nın hedef değerini döndüren bir hesaplama.</td></tr><tr><td></td><td>valueExpression</td><td>Dize</td><td></td><td>KPI 'nın gerçek değerini döndüren MDX sayısal ifadesi.</td></tr><tr><td></td><td>statusExpression</td><td>Dize</td><td></td><td>Belirtilen zaman noktasındaki KPI 'nın durumunu temsil eden bir MDX ifadesi.</td></tr><tr><td></td><td>trendExpression</td><td>Dize</td><td></td><td>KPI değerini zaman içinde değerlendiren bir MDX ifadesi. Eğilim, belirli bir iş bağlamında yararlı olan herhangi bir zaman tabanlı ölçüt olabilir.</td>
<tr><td>Rapor ("Reports")</td><td></td><td></td><td>Açıklama<p>FriendlyName<p>Etiket<p>Uzman<p>AccessInstruction<p>Belgeler<p></td><td>Bu tür bir SQL Server Reporting Services raporunu temsil eder </td></tr><tr><td></td><td>assetCreatedDate</td><td>Dize</td><td></td><td></td></tr><tr><td></td><td>assetCreatedBy</td><td>Dize</td><td></td><td></td></tr><tr><td></td><td>assetModifiedDate</td><td>Dize</td><td></td><td></td></tr><tr><td></td><td>assetModifiedBy</td><td>Dize</td><td></td><td></td></tr><tr><td>Kapsayıcı ("kapsayıcılar")</td><td></td><td></td><td>Açıklama<p>FriendlyName<p>Etiket<p>Uzman<p>AccessInstruction<p>Belgeler<p></td><td>Bu tür, bir SQL veritabanı, Azure Blob kapsayıcısı veya Analysis Services modeli gibi diğer varlıkların bir kapsayıcısını temsil eder.</td></tr></table>

### <a name="annotation-types"></a>Ek açıklama türleri
Ek açıklama türleri, kataloğun içindeki diğer türlere atanabilecek meta veri türlerini temsil eder.

<table>
<tr><td><b>Ek açıklama türü (Iç Içe görünüm adı)</b></td><td><b>Ek özellikler</b></td><td><b>Veri türü</b></td><td><b>Yorumlar</b></td></tr>

<tr><td>Açıklama ("açıklamalar")</td><td></td><td></td><td>Bu özellik bir varlık için açıklama içerir. Sistemin her bir kullanıcısı kendi açıklamalarını ekleyebilir.  Yalnızca bu kullanıcı açıklama nesnesini düzenleyebilir.  (Yöneticiler ve varlık sahipleri açıklama nesnesini silebilir, ancak düzenleyemez). Sistem kullanıcıların açıklamalarını ayrı tutar.  Bu nedenle, her bir varlık üzerinde bir açıklama dizisi vardır (varlık hakkında bilgi sahibi olan her bir kullanıcı için, veri kaynağından elde edilen bilgiler içeren tek bir kişi).</td></tr>
<tr><td></td><td>description</td><td>dize</td><td>Varlığın kısa açıklaması (2-3 satır)</td></tr>

<tr><td>Tag ("Etiketler")</td><td></td><td></td><td>Bu özellik bir varlık için bir etiket tanımlar. Sistemin her bir kullanıcısı, bir varlık için birden fazla etiket ekleyebilir.  Yalnızca etiket nesnelerini oluşturan kullanıcı bunları düzenleyebilir.  (Yöneticiler ve varlık sahipleri etiket nesnesini silebilir, ancak düzenleyemez). Sistem kullanıcıların etiketlerini ayrı tutar.  Bu nedenle, her bir varlık üzerinde bir etiket nesneleri dizisi vardır.</td></tr>
<tr><td></td><td>tag</td><td>dize</td><td>Varlığı açıklayan bir etiket.</td></tr>

<tr><td>FriendlyName ("friendlyName")</td><td></td><td></td><td>Bu özellik bir varlık için kolay bir ad içerir. FriendlyName tek bir ek açıklama-bir varlığa yalnızca bir FriendlyName eklenebilir.  Yalnızca FriendlyName nesnesini oluşturan kullanıcı tarafından düzenlenebilir. (Yöneticiler ve varlık sahipleri FriendlyName nesnesini silebilir ancak düzenleyemez). Sistem kullanıcıların kolay adlarını ayrı tutar.</td></tr>
<tr><td></td><td>friendlyName</td><td>dize</td><td>Varlığın kolay adı.</td></tr>

<tr><td>Şema ("şema")</td><td></td><td></td><td>Şema, verilerin yapısını açıklar.  Özniteliği (sütun, öznitelik, alan, vb.) adlarını, diğer meta verileri de listeler.  Bu bilgilerin tümü veri kaynağından türetilir.  Şema tek bir ek açıklama-varlık için yalnızca bir şema eklenebilir.</td></tr>
<tr><td></td><td>Sütunları</td><td>[] Sütunu</td><td>Sütun nesneleri dizisi. Bunlar, veri kaynağından elde edilen bilgileri içeren sütunu tanımlarlar.</td></tr>

<tr><td>ColumnDescription ("columnDescriptions")</td><td></td><td></td><td>Bu özellik bir sütun için açıklama içerir.  Sistemin her bir kullanıcısı, birden çok sütun için kendi açıklamalarını ekleyebilir (her sütun için en çok bir tane). Yalnızca ColumnDescription nesnelerini oluşturan kullanıcı bunları düzenleyebilir.  (Yöneticiler ve varlık sahipleri ColumnDescription nesnesini silebilir ancak düzenleyemez). Sistem bu kullanıcının sütun açıklamalarını ayrı ayrı tutar.  Bu nedenle, her bir varlık üzerinde bir ColumnDescription nesneleri dizisi vardır (veri kaynağından elde edilen bilgiler içeren, büyük olasılıkla bir sütun hakkında bilgisine katkıda bulunan her bir kullanıcı için her bir sütun için bir sütun).  ColumnDescription, bir şemaya gevşek bir şekilde bağlandığından, bu nedenle eşitlenmemiş olabilir. ColumnDescription, şemada artık mevcut olmayan bir sütunu açıklayabilir.  Açıklama ve şemayı eşitlenmiş halde tutmak için yazıcıya gidin.  Veri kaynağında Ayrıca sütun açıklama bilgileri bulunabilir ve araç çalıştırılırken oluşturulacak ek ColumnDescription nesnelerdir.</td></tr>
<tr><td></td><td>tation</td><td>Dize</td><td>Bu açıklamanın başvurduğu sütunun adı.</td></tr>
<tr><td></td><td>description</td><td>Dize</td><td>sütunun kısa açıklaması (2-3 satır).</td></tr>

<tr><td>ColumnTag ("columnTags")</td><td></td><td></td><td>Bu özellik bir sütun için etiket içerir. Sistemin her bir kullanıcısı, belirli bir sütun için birden fazla etiket ekleyebilir ve birden çok sütun için Etiketler ekleyebilir. Yalnızca ColumnTag nesnelerini oluşturan kullanıcı bunları düzenleyebilir. (Yöneticiler ve varlık sahipleri ColumnTag nesnesini silebilir, ancak düzenleyemez). Sistem bu kullanıcıların sütun etiketlerini ayrı ayrı tutar.  Bu nedenle, her bir varlık üzerinde ColumnTag nesnelerinden oluşan bir dizi vardır.  ColumnTag, bir şemaya gevşek bir şekilde bağlandığından, bu nedenle eşitlenmemiş olabilir. ColumnTag, şemada artık mevcut olmayan bir sütunu açıklayabilir.  Sütun etiketi ve şemayı eşitlenmiş halde tutmak için yazıcıya gidin.</td></tr>
<tr><td></td><td>tation</td><td>Dize</td><td>Bu etiketin başvurduğu sütunun adı.</td></tr>
<tr><td></td><td>tag</td><td>Dize</td><td>Sütunu açıklayan bir etiket.</td></tr>

<tr><td>Uzman ("uzmanlar")</td><td></td><td></td><td>Bu özellik, veri kümesinde uzman olarak kabul edilen bir Kullanıcı içerir. Uzmanlar, açıklamalar listelenirken UX 'in üst kısmına balon koyar. Her Kullanıcı kendi uzmanlarını belirtebilir. Yalnızca bu kullanıcı uzmanlar nesnesini düzenleyebilir. (Yöneticiler ve varlık sahipleri uzman nesneleri silebilir, ancak düzenleyemez).</td></tr>
<tr><td></td><td>uzman</td><td>SecurityPrincipal</td><td></td></tr>

<tr><td>Önizleme ("Önizlemeler")</td><td></td><td></td><td>Önizleme, varlık için ilk 20 veri satırı anlık görüntüsünü içerir. Önizleme yalnızca bazı varlık türleri için anlamlı hale gelir (tablo için mantıklı değildir ancak ölçü için değildir).</td></tr>
<tr><td></td><td>önizleme</td><td>Object []</td><td>Bir sütunu temsil eden nesne dizisi.  Her nesnenin, satır için bu sütun için bir değere sahip bir sütuna bir özellik eşlemesi vardır.</td></tr>

<tr><td>AccessInstruction ("Accessınstructions")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>mimeType</td><td>dize</td><td>İçeriğin MIME türü.</td></tr>
<tr><td></td><td>içerik</td><td>dize</td><td>Bu veri varlığına erişim alma yönergeleri. İçerik bir URL, e-posta adresi veya bir dizi yönerge olabilir.</td></tr>

<tr><td>TableDataProfile ("tableDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>numberOfRows</td></td><td>int</td><td>Veri kümesindeki satır sayısı</td></tr>
<tr><td></td><td>size</td><td>long</td><td>Veri kümesinin bayt cinsinden boyutu.  </td></tr>
<tr><td></td><td>schemaModifiedTime</td><td>dize</td><td>Şemanın değiştirildiği son zaman</td></tr>
<tr><td></td><td>dataModifiedTime</td><td>dize</td><td>Veri kümesinin son değiştirildiği zaman (veri eklendi, değiştirildi veya silindi)</td></tr>

<tr><td>Columns DataProfile ("Columns Dataprofiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Sütunları</td></td><td>ColumnDataProfile []</td><td>Sütun veri profillerinin dizisi.</td></tr>

<tr><td>ColumnDataClassification ("Columndatasınıflandırmaları")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>tation</td><td>Dize</td><td>Bu sınıflandırmanın başvurduğu sütunun adı.</td></tr>
<tr><td></td><td>sınıflandırma</td><td>Dize</td><td>Bu sütundaki verilerin sınıflandırılması.</td></tr>

<tr><td>Belgeler ("belgeler")</td><td></td><td></td><td>Belirli bir varlık kendisiyle ilişkili yalnızca bir belgeye sahip olabilir.</td></tr>
<tr><td></td><td>mimeType</td><td>dize</td><td>İçeriğin MIME türü.</td></tr>
<tr><td></td><td>içerik</td><td>dize</td><td>Belge içeriği.</td></tr>

</table>

### <a name="common-types"></a>Ortak türler
Ortak türler özellikler için türler olarak kullanılabilir, ancak öğeler değildir.

<table>
<tr><td><b>Ortak tür</b></td><td><b>Özelliklerinin</b></td><td><b>Veri türü</b></td><td><b>Yorumlar</b></td></tr>
<tr><td>Datasourceınfo</td><td></td><td></td><td></td></tr>
<tr><td></td><td>sourceType</td><td>dize</td><td>Veri kaynağının türünü açıklar.  Örneğin: SQL Server, Oracle Database, vb.  </td></tr>
<tr><td></td><td>Nesne</td><td>dize</td><td>Veri kaynağındaki nesne türünü açıklar. Örneğin: Tablo, SQL Server için görüntüleyin.</td></tr>

<tr><td>DataSourceLocation</td><td></td><td></td><td></td></tr>
<tr><td></td><td>protocol</td><td>dize</td><td>Gerekli. Veri kaynağıyla iletişim kurmak için kullanılan bir protokolü açıklar. Örneğin: SQl Server için "tds", Oracle için "Oracle" vb. Şu anda desteklenen protokollerin listesi için <a href="https://docs.microsoft.com/azure/data-catalog/data-catalog-dsr">veri kaynağı başvuru belirtimi-DSL yapısına</a> bakın.</td></tr>
<tr><td></td><td>adres</td><td>Sözlük&lt;dizesi, nesne&gt;</td><td>Gerekli. Adres, başvurulmakta olan veri kaynağını tanımlamak için kullanılan protokole özgü bir veri kümesidir. Belirli bir protokol kapsamındaki adres verileri, protokolü bilmeden anlamlı olduğu anlamına gelir.</td></tr>
<tr><td></td><td>kimlik doğrulaması</td><td>dize</td><td>İsteğe bağlı. Veri kaynağıyla iletişim kurmak için kullanılan kimlik doğrulama düzeni. Örneğin: windows, oauth vb.</td></tr>
<tr><td></td><td>connectionProperties</td><td>Sözlük&lt;dizesi, nesne&gt;</td><td>İsteğe bağlı. Bir veri kaynağına bağlanma hakkında ek bilgiler.</td></tr>

<tr><td>SecurityPrincipal</td><td></td><td></td><td>Arka uç, yayımlama sırasında AAD 'ye yönelik olarak belirtilen özelliklerin doğrulanmasını gerçekleştirmez.</td></tr>
<tr><td></td><td>upn</td><td>dize</td><td>Kullanıcının benzersiz e-posta adresi. ObjectID sağlanmazsa veya "lastRegisteredBy" özelliği bağlamında, aksi takdirde isteğe bağlı olarak belirtilmelidir.</td></tr>
<tr><td></td><td>objectId</td><td>Guid</td><td>Kullanıcı veya güvenlik grubu AAD kimliği. İsteğe bağlı. UPN sağlanmamışsa, aksi takdirde isteğe bağlı olarak belirtilmelidir.</td></tr>
<tr><td></td><td>firstName</td><td>dize</td><td>Kullanıcının ilk adı (görüntüleme amaçları için). İsteğe bağlı. Yalnızca "lastRegisteredBy" özelliği bağlamında geçerlidir. "Roles", "Permissions" ve "experts" için güvenlik sorumlusu sağlanarak belirtilemiyor.</td></tr>
<tr><td></td><td>lastName</td><td>dize</td><td>Kullanıcının Soyadı (görüntüleme amaçları için). İsteğe bağlı. Yalnızca "lastRegisteredBy" özelliği bağlamında geçerlidir. "Roles", "Permissions" ve "experts" için güvenlik sorumlusu sağlanarak belirtilemiyor.</td></tr>

<tr><td>Sütun</td><td></td><td></td><td></td></tr>
<tr><td></td><td>name</td><td>dize</td><td>Sütunun veya özniteliğin adı.</td></tr>
<tr><td></td><td>türü</td><td>dize</td><td>sütunun veya özniteliğin veri türü. Izin verilen türler, varlığın veri sourceType bağımlıdır.  Yalnızca türlerin bir alt kümesi desteklenir.</td></tr>
<tr><td></td><td>'In</td><td>int</td><td>Sütun veya öznitelik için izin verilen uzunluk üst sınırı. Veri kaynağından türetilir. Yalnızca bazı kaynak türleri için geçerlidir.</td></tr>
<tr><td></td><td>duyarlık</td><td>byte</td><td>Sütun veya öznitelik için duyarlık. Veri kaynağından türetilir. Yalnızca bazı kaynak türleri için geçerlidir.</td></tr>
<tr><td></td><td>IsNullable</td><td>Boole değeri</td><td>Sütunun null değere sahip olmasına izin verilip verilmeyeceğini belirtir. Veri kaynağından türetilir. Yalnızca bazı kaynak türleri için geçerlidir.</td></tr>
<tr><td></td><td>expression</td><td>dize</td><td>Değer hesaplanmış bir sütunsiyse, bu alan değeri ifade eden ifadeyi içerir. Veri kaynağından türetilir. Yalnızca bazı kaynak türleri için geçerlidir.</td></tr>

<tr><td>ColumnDataProfile</td><td></td><td></td><td></td></tr>
<tr><td></td><td>tation </td><td>dize</td><td>Sütunun adı</td></tr>
<tr><td></td><td>türü </td><td>dize</td><td>Sütunun türü</td></tr>
<tr><td></td><td>dk </td><td>dize</td><td>Veri kümesindeki en küçük değer</td></tr>
<tr><td></td><td>en çok </td><td>dize</td><td>Veri kümesindeki en büyük değer</td></tr>
<tr><td></td><td>ort </td><td>double</td><td>Veri kümesindeki ortalama değer</td></tr>
<tr><td></td><td>STDSAPMA </td><td>double</td><td>Veri kümesi için standart sapma</td></tr>
<tr><td></td><td>nullCount </td><td>int</td><td>Veri kümesindeki null değer sayısı</td></tr>
<tr><td></td><td>distinctCount  </td><td>int</td><td>Veri kümesindeki farklı değerlerin sayısı</td></tr>


</table>

## <a name="asset-identity"></a>Varlık kimliği
Azure Veri Kataloğu, varlık kimliğini oluşturmak için, kataloğun içindeki varlığı ele almak için kullanılan "adres" özellik çantasından "protokol" ve kimlik özelliklerini kullanır.
Örneğin, "tds" protokolünün kimlik özellikleri "sunucu", "veritabanı", "şema" ve "nesne" vardır. Protokolün ve kimlik özelliklerinin birleşimleri SQL Server tablo varlığının kimliğini oluşturmak için kullanılır.
Azure Veri Kataloğu, [veri kaynağı başvuru belirtimi-DSL yapısında](data-catalog-dsr.md)listelenen çeşitli yerleşik veri kaynağı protokolleri sağlar.
Desteklenen protokoller kümesi programlı olarak genişletilebilir (veri kataloğuna REST API başvuruya bakın). Kataloğun yöneticileri, özel veri kaynağı protokollerini kaydedebilir. Aşağıdaki tabloda, özel bir protokolü kaydetmek için gereken özellikler açıklanmaktadır.

### <a name="custom-data-source-protocol-specification"></a>Özel veri kaynağı protokol belirtimi
<table>
<tr><td><b>Tür</b></td><td><b>Özelliklerinin</b></td><td><b>Veri türü</b></td><td><b>Yorumlar</b></td></tr>

<tr><td>DataSourceProtocol</td><td></td><td></td><td></td></tr>
<tr><td></td><td>ad alanı</td><td>dize</td><td>Protokolün ad alanı. Ad alanı 1 ile 255 karakter uzunluğunda olmalıdır ve noktayla (.) ayrılmış bir veya daha fazla boş olmayan parçalar içermelidir. Her bölüm 1 ile 255 karakter uzunluğunda olmalı, bir harfle başlamalı ve yalnızca harf ve rakam içermelidir.</td></tr>
<tr><td></td><td>name</td><td>dize</td><td>Protokolün adı. Ad 1 ile 255 karakter uzunluğunda olmalı, bir harfle başlamalı ve yalnızca harf, rakam ve tire (-) karakteri içermelidir.</td></tr>
<tr><td></td><td>ıdentityproperties</td><td>Datasourceprotocolidentityözelliği []</td><td>Kimlik özelliklerinin listesi, en az bir tane içermesi gerekir, ancak 20 ' den fazla özellik içermemelidir. Örneğin: "sunucu", "veritabanı", "şema", "nesne", "tds" protokolünün kimlik özelliklerdir.</td></tr>
<tr><td></td><td>ıdentitysets</td><td>DataSourceProtocolIdentitySet []</td><td>Kimlik kümelerinin listesi. Geçerli varlığın kimliğini temsil eden kimlik özelliklerinin kümelerini tanımlar. En az bir tane içermeli, 20 ' den fazla küme içermelidir. Örneğin: {"sunucu", "veritabanı", "şema" ve "nesne"}, SQL Server tablo varlığının kimliğini tanımlayan "tds" protokolü için bir kimlik kümesidir.</td></tr>

<tr><td>Datasourceprotocolidentityözelliği</td><td></td><td></td><td></td></tr>
<tr><td></td><td>name</td><td>dize</td><td>Özelliğin adı. Ad 1 ile 100 karakter uzunluğunda olmalı, bir harfle başlamalı ve yalnızca harf ve rakam içermelidir.</td></tr>
<tr><td></td><td>türü</td><td>dize</td><td>Özelliğin türü. Desteklenen değerler: "bool", Boolean "," byte "," Guid "," int "," Integer "," Long "," String "," URL "</td></tr>
<tr><td></td><td>ignoreCase</td><td>bool</td><td>Özelliğin değeri kullanılırken büyük/küçük harf 'in yoksayılıp sayılmayacağını gösterir. Yalnızca "String" türü olan özellikler için belirtilebilir. Varsayılan değer false 'dur.</td></tr>
<tr><td></td><td>urlPathSegmentsIgnoreCase</td><td>bool []</td><td>URL 'nin yolunun her bir segmenti için büyük/küçük harf 'in yoksayılıp sayılmayacağını belirtir. Yalnızca "URL" türündeki özellikler için belirtilebilir. Varsayılan değer [false] şeklindedir.</td></tr>

<tr><td>DataSourceProtocolIdentitySet</td><td></td><td></td><td></td></tr>
<tr><td></td><td>name</td><td>dize</td><td>Kimlik kümesinin adı.</td></tr>
<tr><td></td><td>properties</td><td>String []</td><td>Bu kimlik kümesine eklenen kimlik özelliklerinin listesi. Yinelenen öğeler içeremez. Kimlik kümesi tarafından başvurulan her bir özelliğin, protokolün "ıdentityproperties" listesinde tanımlanması gerekir.</td></tr>

</table>

## <a name="roles-and-authorization"></a>Roller ve yetkilendirme
Microsoft Azure Veri Kataloğu, varlıklar ve ek açıklamaların CRUD işlemleri için yetkilendirme özellikleri sağlar.

## <a name="key-concepts"></a>Önemli kavramlar
Azure Veri Kataloğu iki yetkilendirme mekanizması kullanır:

* Rol tabanlı yetkilendirme
* İzin tabanlı yetkilendirme

### <a name="roles"></a>Roller
Üç rol vardır: **Yönetici**, **sahip**ve **katkıda bulunan**.  Her rolün kapsamı ve hakları vardır ve bu, aşağıdaki tabloda özetlenmiştir.

<table><tr><td><b>Rol</b></td><td><b>Kapsam</b></td><td><b>Haklarını</b></td></tr><tr><td>Yönetici</td><td>Katalog (katalogdaki tüm varlıklar/ek açıklamalar)</td><td>ViewRoles silme oku

Değişiklik sahibi ChangeVisibility ViewPermissions</td></tr><tr><td>Sahip</td><td>Her varlık (kök öğe)</td><td>ViewRoles silme oku

Değişiklik sahibi ChangeVisibility ViewPermissions</td></tr><tr><td>Katılımcı</td><td>Her bireysel varlık ve ek açıklama</td><td>Oku Update görünüm rollerini Sil Note: öğedeki okuma hakkı, katkıda bulunmasından iptal edildiğinde tüm haklar iptal edilir</td></tr></table>

> [!NOTE]
> **Okuma**, **güncelleştirme**, **silme**, **viewroles** hakları, tek bir öğe (varlık veya ek açıklama) için geçerlidir, çünkü **takesahiplik**, **değişiklik sahipliği**, **changevisibility**, **viewpermissions** yalnızca kök varlık için geçerlidir.
> 
> **Sil** hakkı bir öğe ve altındaki alt öğeler veya tek öğe için geçerlidir. Örneğin, bir varlığı silmek söz konusu varlık için ek açıklamaları da siler.
> 
> 

### <a name="permissions"></a>İzinler
İzin, erişim denetimi girdilerinin listesidir. Her erişim denetimi girişi, bir güvenlik sorumlusu için haklar kümesi atar. İzinler yalnızca bir varlık üzerinde (yani, kök öğe) belirtilebilir ve varlık ve tüm alt öğeleri için geçerlidir.

**Azure Veri Kataloğu** önizlemesi sırasında, bir varlığın görünürlüğünü kısıtlamak için senaryo etkinleştirmek üzere izinler listesinde yalnızca **okuma** hakkı desteklenir.

Varsayılan olarak, tüm kimliği doğrulanmış kullanıcılar, görünürlük içindeki her öğe için **okuma** hakkına sahiptir ve görünürlük, izinlerle ilgili bir sorumlu kümesiyle sınırlı değildir.

## <a name="rest-api"></a>REST API
**Yerleştirme** ve **gönderi** görünümü öğe istekleri, rol ve izinleri denetlemek için kullanılabilir: öğe yüküne ek olarak, iki sistem özelliği de **Roller** ve **izinler**belirtilebilir.

> [!NOTE]
> **izinler** yalnızca bir kök öğe için geçerlidir.
> 
> **Sahip** rolü yalnızca bir kök öğe için geçerlidir.
> 
> Katalogda bir öğe oluşturulduğunda varsayılan olarak kimliği doğrulanmış kullanıcı olarak ayarlanır . Öğe herkes tarafından güncelleştirilebilse, **katkıda bulunanlar** , öğe ilk yayımlandığında &lt;roller&gt; özelliğinde Everyone özel güvenlik sorumlusu olarak ayarlanmalıdır (aşağıdaki örneğe bakın). **Katkıda bulunan** değiştirilemez ve bir öğenin yaşam süresi boyunca aynı kalır (hatta **yönetici** veya **sahip** , **katkıda**bulunanı değiştirme hakkına sahip değildir). **Katkıda** bulunanı açık ayarı için desteklenen tek değer herkese &lt;&gt;açıktır: **Katkıda bulunan** yalnızca bir öğeyi veya &lt;herkesi&gt;oluşturan bir kullanıcı olabilir.
> 
> 

### <a name="examples"></a>Örnekler
**Bir öğeyi yayımlarken &lt;katkıda&gt; bulunan herkese ayarlayın.**
Özel güvenlik sorumlusu &lt;herkese&gt; "00000000-0000-0000-0000-000000000201" ObjectID vardır.
  Https **sonrası** :\//api.azuredatacatalog.com/catalogs/default/views/Tables/?api-Version=2016-03-30

> [!NOTE]
> Bazı HTTP istemci uygulamaları, sunucudan bir 302 'e yanıt olarak istekleri otomatik olarak yeniden verebilir, ancak genellikle kimlik doğrulama üst bilgilerini istekten çıkar. Azure Veri Kataloğu 'na istek yapmak için yetkilendirme üst bilgisi gerektiğinden, Azure Veri Kataloğu tarafından belirtilen yeniden yönlendirme konumuna bir istek yeniden verilirken yetkilendirme üstbilgisinin hala sağlandığından emin olmanız gerekir. Aşağıdaki örnek kod, .NET HttpWebRequest nesnesini kullanarak gösterir.
> 
> 

**Gövde**
```json
    {
        "roles": [
            {
                "role": "Contributor",
                "members": [
                    {
                        "objectId": "00000000-0000-0000-0000-000000000201"
                    }
                ]
            }
        ]
    }
```

  **Mevcut bir kök öğe için sahipleri ata ve görünürlüğü kısıtla**: Https 'yi yerleştir\/:/api.azuredatacatalog.com/catalogs/default/views/Tables/042297b0...1be45ecd462a?api-Version=2016-03-30

```json
    {
        "roles": [
            {
                "role": "Owner",
                "members": [
                    {
                        "objectId": "c4159539-846a-45af-bdfb-58efd3772b43",
                        "upn": "user1@contoso.com"
                    },
                    {
                        "objectId": "fdabd95b-7c56-47d6-a6ba-a7c5f264533f",
                        "upn": "user2@contoso.com"
                    }
                ]
            }
        ],
        "permissions": [
            {
                "principal": {
                    "objectId": "27b9a0eb-bb71-4297-9f1f-c462dab7192a",
                    "upn": "user3@contoso.com"
                },
                "rights": [
                    {
                        "right": "Read"
                    }
                ]
            },
            {
                "principal": {
                    "objectId": "4c8bc8ce-225c-4fcf-b09a-047030baab31",
                    "upn": "user4@contoso.com"
                },
                "rights": [
                    {
                        "right": "Read"
                    }
                ]
            }
        ]
    }
```

> [!NOTE]
> Bu, gövdesinde bir öğe yükü belirtmek için gerekli değildir: PUT, yalnızca rolleri ve/veya izinleri güncelleştirmek için kullanılabilir.
> 
