---
title: Azure Veri Kataloğu hakkında sık sorulan sorular
description: Veri kaynağı bulma, ek açıklama ve yönetime yönelik yetenekler dahil olmak üzere Azure Veri Kataloğu hakkında sık sorulan sorular.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 3877648723a8866ec7d9c5f1987ae3a276114d5e
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73152049"
---
# <a name="azure-data-catalog-frequently-asked-questions"></a>Azure Veri Kataloğu hakkında sık sorulan sorular
Bu makale, Azure Veri Kataloğu hizmetiyle ilgili sık sorulan soruların yanıtlarını sağlar.

## <a name="what-is-azure-data-catalog"></a>Azure Veri Kataloğu nedir?
Veri Kataloğu, kurumsal veri kaynakları için kayıt ve bulma sistemi olarak görev yapan, Microsoft Azure ' de barındırılan, tam olarak yönetilen bir hizmettir. Veri Kataloğu, analistlerden veri bilimcilerine ve geliştiricilere kadar olan tüm kullanıcılar veri kaynaklarını kaydedebilir, bulabilir, anlayabilir ve kullanabilir.

## <a name="what-customer-challenges-does-it-solve"></a>Hangi müşteri zorlukları çözüyor?
Veri Kataloğu, kullanıcıların kurumsal veri kaynaklarını bulabilmesi ve anlayabilmesi için veri kaynağı bulma ve "koyu veri" sorunlarını ele alır.

## <a name="what-are-its-target-audiences"></a>Hedef kitleleri nelerdir?
Veri Kataloğu, aşağıdakiler dahil olmak üzere teknik ve teknik olmayan kullanıcılar için tasarlanmıştır:

* Veri geliştiricileri ve bı ve Analytics uzmanları: başkalarının tüketmesi için veri ve analiz içeriği üretmekten sorumlu kişiler.
* Veri özellikleri: veriler hakkında bilgi sahibi olan kişiler, ne anlama gelir ve nasıl kullanılması amaçlanan.
* Veri tüketicileri: kendi tercih ettiği aracı kullanarak işlerini yapmak için ihtiyaç duydukları verileri kolayca bulabilmeleri, anlaması ve bunlara bağlanabilmesi gereken kişiler.
* Orta BT: iş kullanıcıları tarafından yüzlerce veri kaynağı keşfetmek isteyen kişiler ve verilerin kullanılma ve kim tarafından nasıl kullanıldığı hakkında daha fazla bakış sahibi olması gerekir.

## <a name="what-is-its-availability-by-region"></a>Bölgeye göre kullanılabilirliği nedir?
Veri Kataloğu Hizmetleri şu anda aşağıdaki veri merkezlerinde kullanılabilir:

* Batı ABD
* Doğu ABD
* Batı Avrupa
* Kuzey Avrupa
* Doğu Avustralya
* Güneydoğu Asya

## <a name="what-are-its-limits-on-the-number-of-data-assets"></a>Veri varlıklarının sayısı sınırları nelerdir?
Veri Kataloğu 'nun ücretsiz sürümü 5.000 kayıtlı veri varlığı ile sınırlıdır.

Veri Kataloğu 'nun standart sürümü en fazla 100.000 kayıtlı veri varlığını destekler.

Tablolar, görünümler, dosyalar ve raporlar gibi veri kataloğunda kayıtlı olan herhangi bir nesne, veri varlığı olarak sayılır.

## <a name="what-are-its-supported-data-source-and-asset-types"></a>Desteklenen veri kaynağı ve varlık türleri nelerdir?
Şu anda desteklenen veri kaynaklarının listesi için bkz. [Veri Kataloğu DSR](data-catalog-dsr.md).

## <a name="how-do-i-request-support-for-another-data-source"></a>Nasıl yaparım? başka bir veri kaynağı için destek isteği mi?
Özellik isteklerini ve diğer geri bildirimleri göndermek için [Azure geri bildirim forumlarında veri kataloğuna](https://feedback.azure.com/forums/906052-data-catalog/category/320788-data-sources)gidin.

## <a name="why-do-i-get-an-error-catalog-already-exists-when-i-try-to-create-a-new-catalog"></a>Yeni bir katalog oluşturmaya çalıştığımda neden bir hata *kataloğu* aldım?

Office 365 E5 Power BI Pro lisansıyla satın aldığınızda, Microsoft, aboneliğin bölgesinde otomatik olarak varsayılan bir katalog oluşturur. Bu katalog, Ücretsiz SKU 'YU kullanır. Office 365/Power BI Kullanıcı Lisansı Office 365 Yönetim sayfasında yönetilir. 

Ancak, bu tür veri kataloğunda **yönetici seçeneği** yoktur ve **Azure Portal**görünmez. Bu tür veri kataloğunu silemezsiniz. Benzer şekilde, veri kataloğunu yeniden adlandırmanıza izin verilmez ve başka bir bölgeye taşıyamazsınız. 

Power BI Pro lisansı atanan kullanıcı hesaplarının, Power BI Pro lisansıyla Office 365 E5 'a kaydolduklarında lisans anlaşması nedeniyle otomatik olarak veri kataloğuna erişimi vardır. Bu tür bir Kullanıcı, yönetim ayrıcalıklarına sahip olmayan veri kataloğu varlıklarına tam erişime sahiptir. Bu tür bir Kullanıcı, Azure Veri Kataloğu 'nda **Katalog Kullanıcı** rolünün bir parçası *değildir* .


## <a name="how-do-i-get-started-with-data-catalog"></a>Veri Kataloğu 'Nu kullanmaya başlamak Nasıl yaparım? misiniz?
Başlamak için en iyi yöntem, [Veri Kataloğu 'nu kullanmaya başlamanızı](data-catalog-get-started.md)sağlar. Bu makale, hizmette yetenekler hakkında uçtan uca bir genel bakış sağlar.

## <a name="how-do-i-register-my-data"></a>Nasıl yaparım? verilerimi kaydetmek istiyor musunuz?
Verilerinizi veri kataloğuna kaydetmek için:
1. Azure Veri Kataloğu portalında, **Yayımla** alanında Azure Veri Kataloğu Kayıt Aracı ' nı başlatın. 
2. Veri Kataloğu veri kaynağı kayıt aracında, veri kataloğu portalına erişmek için kullandığınız kimlik bilgileriyle oturum açın.
3. Veri kaynağını ve kaydetmek istediğiniz belirli varlıkları seçin.

## <a name="what-properties-does-it-extract-for-data-assets-that-are-registered"></a>Kayıtlı veri varlıkları için hangi özellikleri ayıklar?
Belirli özellikler veri kaynağından veri kaynağına farklılık gösterir, ancak genel olarak, veri kataloğu yayımlama hizmeti aşağıdaki bilgileri ayıklar:

* Varlık adı
* Varlık türü
* Varlık açıklaması
* Öznitelik/sütun adları
* Öznitelik/sütun veri türleri
* Öznitelik/sütun açıklaması

> [!IMPORTANT]
> Veri varlıklarını veri kataloğuna kaydetme, verilerinizi buluta taşımaz veya kopyalamaz. Bir veri kaynağından varlık kaydetme, varlıkların meta verilerini Azure 'a kopyalar, ancak veriler var olan veri kaynağı konumunda kalır. Bu kuralın istisnası, varlıkları kaydettiğinizde önizleme kayıtlarını veya bir veri profilini karşıya yüklemeyi tercih ediyorsanız yapılır. Önizleme eklediğinizde, her bir varlıktan 20 ' ye kadar kayıt kopyalanır ve veri kataloğunda anlık görüntü olarak depolanır. Bir veri profili eklediğinizde, toplu bilgiler hesaplanır ve Katalogda depolanan meta verilere dahil edilir. Toplu bilgiler, tabloların boyutunu, sütun başına null değer yüzdesini veya sütunlar için en düşük, en yüksek ve ortalama değerleri içerebilir. 
>
>

> [!NOTE]
> Birinci sınıf **Açıklama** özelliğine sahip SQL Server Analysis Services gibi veri kaynakları Için, Veri Kataloğu veri kaynağı kayıt aracı bu özellik değerini ayıklar. Birinci sınıf **Açıklama** özelliği olmayan *Şirket içi* SQL Server Ilişkisel veritabanları için, Veri Kataloğu veri kaynağı kayıt aracı, **MS_Description** genişletilmiş özelliğinden değeri nesneler ve sütunlar için ayıklar. Daha fazla bilgi için bkz. [veritabanı nesnelerinde genişletilmiş özellikleri kullanma](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx).
>
>

## <a name="how-long-should-it-take-for-newly-registered-assets-to-appear-in-the-catalog"></a>Yeni kayıtlı varlıkların katalogda görünmesi ne kadar sürer?
Varlıkları Veri Kataloğu ile kaydettikten sonra, veri kataloğu portalında görünmeleri için 5 ila 10 saniye arasında bir süre olabilir.

## <a name="how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>Kayıtlı veri varlıklarım için not ekleme ve meta verileri zenginleştirme Nasıl yaparım? misiniz?
Kayıtlı varlıklar için meta verileri sağlamanın en basit yolu, veri kataloğu portalındaki varlığı seçmek ve ardından seçilen nesne için Özellikler bölmesi veya şema bölmesinde değerleri girmesidir.

Ayrıca, kayıt işlemi sırasında uzmanlar ve Etiketler gibi bazı meta veriler de sağlayabilirsiniz. Veri Kataloğu yayımlama hizmetinde sağladığınız değerler, o anda kayıtlı olan tüm varlıklar için geçerlidir. Ek açıklama için portalda son kaydedilen nesneleri görüntülemek için, Veri Kataloğu veri kaynağı kayıt aracının son ekranındaki **portalı görüntüle** düğmesini seçin.

## <a name="how-do-i-delete-my-registered-data-objects"></a>Nasıl yaparım? kayıtlı veri nesnelerimi silmek mi istiyorsunuz?
Portalda nesneyi seçip **Sil** düğmesine tıklayarak veri kataloğundan bir nesne silebilirsiniz. Nesnenin kaldırılması veri kataloğundan meta verilerini kaldırır, ancak temel alınan veri kaynağını etkilemez.

## <a name="what-is-an-expert"></a>Uzman nedir?
Uzman, veri nesnesi hakkında bilinçli bir perspektife sahip kişidir. Bir nesne birden çok uzmana sahip olabilir. Bir uzman nesnenin bir nesne için "Owner" olması gerekmez, ancak verilerin nasıl kullanılabileceğini ve nasıl kullanılacağını bilen bir kişidir.

## <a name="how-do-i-share-information-with-the-data-catalog-team-if-i-encounter-problems"></a>Sorun yaşarsanız, veri kataloğu ekibiyle bilgi Nasıl yaparım?.
Sorunları raporlamak, bilgileri paylaşmak ve soru sormak için [Azure Veri Kataloğu forumuna](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)gidin.

## <a name="does-the-catalog-work-with-another-data-source-that-im-interested-in"></a>Katalog ilgilendiğiniz başka bir veri kaynağıyla mi çalışıyor?
Veri kataloğuna daha fazla veri kaynağı ekleme konusunda etkin bir şekilde çalışıyoruz. Desteklenen belirli bir veri kaynağını görmek isterseniz, [Azure geri bildirim forumlarında veri kataloğuna](https://feedback.azure.com/forums/906052-data-catalog)giderek bunu önerin (veya zaten önerildiyse desteğiniz sesinizi).

## <a name="what-permissions-do-i-need-to-register-assets-with-data-catalog"></a>Veri Kataloğu ile varlıkları kaydetmek için hangi izinlere ihtiyacım var?
Veri Kataloğu kayıt aracı 'nı çalıştırmak için, kaynaktan meta verileri okumanızı sağlayan veri kaynağında izinlere ihtiyacınız vardır. Ayrıca, bir önizleme de dahil etmek için, kaydedilen nesnelerden verileri okumanızı sağlayan izinleriniz olmalıdır.

Veri Kataloğu, Katalog yöneticilerinin kataloğa meta veri ekleyebileceği kullanıcıları ve grupları kısıtlamak için de izin verir. Daha fazla bilgi için bkz. [Veri Kataloğu ve veri varlıklarına erişimi güvenli hale getirme](data-catalog-how-to-secure-catalog.md).

## <a name="will-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>Veri Kataloğu, şirket içi dağıtım için de kullanılabilir hale getirilir mi?
Veri Kataloğu, karma veri kaynağı bulma çözümü sunmak için hem bulut hem de şirket içi veri kaynaklarıyla birlikte çalışabilme bir bulut hizmetidir. Şu anda şirket içinde çalışan veri kataloğu hizmetinin bir sürümü için bir plan yoktur.

## <a name="can-i-extract-more-or-richer-metadata-from-the-data-sources-i-register"></a>Kaydoldum veri kaynaklarından daha fazla veya daha zengin meta veri ayıklayabilir miyim?
Veri Kataloğu 'nun yeteneklerini genişletmek için etkin bir şekilde çalışıyoruz. Kayıt sırasında veri kaynağından ayıklanan ek meta verilere sahip olmak istiyorsanız, [Azure geri bildirim forumlarındaki veri kataloğunda](https://feedback.azure.com/forums/906052-data-catalog)bu işlemi önerin (veya zaten önerildiyse). 

Bu meta verilerin veri kaynağı kayıt aracı tarafından ayıklanmayan veri kaynakları için sütun/şema meta verilerini, önizlemeleri veya veri profillerini eklemek isterseniz, bu meta verileri eklemek için veri kataloğu API 'sini kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure veri kataloğu REST API](https://docs.microsoft.com/rest/api/datacatalog/).

## <a name="how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>Yalnızca belirli kişilerin bulabilmesi için kayıtlı veri varlıklarının görünürlüğünü kısıtlamak Nasıl yaparım? misiniz?
Veri kataloğunda veri varlıkları ' nı seçin ve ardından **Sahipliği Al** düğmesine tıklayın. Veri kataloğunda veri varlıklarının sahipleri, tüm kullanıcıların sahip olan varlıkları bulmasına izin vermek veya görünürlüğü belirli kullanıcılarla kısıtlayabilmesi için görünürlük ayarlarını değiştirebilir. Daha fazla bilgi için bkz. [Azure Veri Kataloğu 'nda veri varlıklarını yönetme](data-catalog-how-to-manage.md).

## <a name="how-do-i-update-the-registration-for-a-data-asset-so-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>Veri kaynağındaki değişikliklerin kataloğa yansıtılması için bir veri varlığının kaydını Nasıl yaparım? güncelleştirin mi?
Katalogda zaten kayıtlı olan veri varlıklarının meta verilerini güncelleştirmek için, varlıkları içeren veri kaynağını yeniden kaydetmeniz yeterlidir. Veri kaynağında bulunan veya tablolardaki veya görünümlerde eklenen ya da kaldırılan sütunlar gibi tüm değişiklikler katalogda güncelleştirilir, ancak kullanıcılar tarafından sunulan tüm ek açıklamalar korunur.

## <a name="my-question-isnt-answered-here-where-can-i-go-for-answers"></a>Sorum burada Yanıtlanmıyor. Yanıtlar için nereye gidebilirim?
[Azure Veri Kataloğu forumuna](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)gidin. Bu soruları burada bulabilirsiniz.
