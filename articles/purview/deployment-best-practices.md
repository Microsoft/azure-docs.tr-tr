---
title: Dağıtım en iyi uygulamaları
description: Bu makalede, Azure purview dağıtımı için en iyi yöntemler sunulmaktadır. Azure takip görünümü, herhangi bir kullanıcının veri kaynaklarını kaydetmesini, bulmasını, anlamasını ve kullanmasını sağlar.
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: e02ad9187743603d46259d70965e49d6839ecd71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104949847"
---
# <a name="azure-purview-deployment-best-practices"></a>Azure purview dağıtımı en iyi uygulamaları

Bu makalede, üretim ortamına purview dağıtmanıza yardımcı olabilecek ortak görevler tanımlanmaktadır. Bu görevler, bir ay veya daha uzun bir süre boyunca aşamalar halinde tamamlanabilir. Bu kılavuzu zaten dağıtmış olan kuruluşlar bile, yatırımlarından en iyi şekilde yararlanmak için bu kılavuzu kullanabilir.

Bir veri yönetimi platformunun (Azure purview gibi) iyi planlanmış bir dağıtımı, aşağıdaki avantajları sağlayabilir:

- Daha iyi veri bulma
- Geliştirilmiş analitik işbirliği
- Büyütülmüş, yatırımdaki dönüş.

## <a name="prerequisites"></a>Önkoşullar

* Geliştirme veya üretim aboneliğiyle Microsoft Azure erişim
* Purview dahil Azure kaynakları oluşturma yeteneği
* Test, geliştirme veya üretim ortamlarında Azure Data Lake Storage veya Azure SQL gibi veri kaynaklarına erişim
  * Data Lake Storage için, taranacak gerekli rol okuyucu rolüne sahiptir
  * SQL için, kimliğin sınıflandırmaların örneklemesini sağlamak üzere tabloları sorgulayabilmesi gerekir
* Azure Güvenlik Merkezi 'ne erişim veya veri etiketleme için Güvenlik Merkezi Yöneticisi ile işbirliği yapma

## <a name="identify-objectives-and-goals"></a>Hedefleri ve hedefleri belirleme

Birçok kuruluş, kuruluş genelinde yalıtılmış grupların ve veri etki alanlarının belirli gereksinimlerine karşılık gelen bireysel çözümler geliştirip veri idare yolculuğunu başlattı. Deneyimler sektöre, ürüne ve kültüre bağlı olarak değişebilir, ancak çoğu kuruluş bu çözüm türleri için tutarlı denetimleri ve ilkeleri korumayı zorlaştırır.

Erken aşamalarda tanımlamak isteyebileceğiniz bazı yaygın veri idare hedefleri şunları içerir:

* Verilerinizin iş değerini en üst düzeye çıkarma
* Veri tüketicilerinin verileri kolayca bulabileceği, yorumlayabildiği ve güvenebileceği bir veri kültürünü etkinleştirme
* Tutarlı bir veri deneyimi sağlamak için çeşitli iş birimleri arasında işbirliğini artırma
* Bulutun avantajlarından yararlanmaya yönelik veri analizlerini hızlandırarak yeniliği hızlandırma
* Çeşitli beceri grupları için self servis seçenekleri aracılığıyla verileri keşfetme süresini düşürme
* Hizmeti müşterilerine iyileştiren analiz çözümlerinin teslimatı için pazara harcanan süreyi azaltma
* Etki alanına özgü araçların ve desteklenmeyen teknolojinin kullanılması nedeniyle oluşan işlemsel riskleri azaltma

Genel yaklaşım, bu fazla bu hedefleri çeşitli kategorilere ve hedeflere bölmek için kullanılır. Bazı örnekler şunlardır:

|Kategori|Hedef|
|---------|---------|
|Bulma|Yönetici kullanıcılar, veri varlıkları ile ilgili bilgileri otomatik olarak toplamak için Azure ve Azure olmayan veri kaynaklarını (Şirket içi kaynaklar dahil) tarayabilmesi gerekir.|
|Sınıflandırma|Platform, verileri bir örneklemeye göre otomatik olarak sınıflandırmalı ve özel sınıflandırmalar kullanarak el ile geçersiz kılmaya izin verir.|
|Tüketim|İş kullanıcıları, hem iş hem de teknik meta veriler için her varlık hakkındaki bilgileri bulabilmelidir.|
|Köken|Her varlık, kullanıcıların özgün kaynakları ve hangi değişikliklerin yapıldığını anlayabilmesi için temel alınan veri kümelerinin grafik görünümünü göstermesi gerekir.|
|İşbirliği|Platform, her veri varlığı hakkında ek bilgiler sunarak kullanıcıların işbirliği yapmasına izin vermelidir.|
|Raporlama|Kullanıcılar, önemli veriler ve ek zenginleştirme gerektiren veriler de dahil olmak üzere verileri raporlayan bildirimde görüntüleyebilmelidir.|
|Veri idaresi|Platform, yöneticinin erişim denetimi için ilke tanımlamasına ve her kullanıcıya göre veri erişimini otomatik olarak zorlayamalıdır.|
|İş akışı|Platform, platform içinde çeşitli görevleri genişletmek ve otomatikleştirmek için iş akışını oluşturup değiştirme yeteneğine sahip olmalıdır.|
|Tümleştirme|Bilet oluşturma veya düzenleme gibi diğer üçüncü taraf teknolojiler, komut dosyası veya REST API 'Leri aracılığıyla platformla tümleştirilebilmelidir.|

## <a name="top-questions-to-ask"></a>Sorun için sık sorulan sorular

Kuruluşunuz üst düzey hedefleri ve hedefleri kabul ederse, birden çok gruptan çok sayıda soru olacaktır. Tüm kaygıları ele almak için bir plan yapmak üzere bu soruların toplanması önemlidir. İlk aşamada çalıştırabileceğiniz bazı örnek sorular:

1. Ana kuruluş veri kaynakları ve veri sistemleri nelerdir?
2. Henüz purview tarafından desteklenmeyen veri kaynakları için seçeneklerim nelerdir?
3. Kaç tane purview örneğine ihtiyacım var?
4. Kullanıcılar kim?
5. Yeni veri kaynaklarını kimlerin tarayabilmesi?
6. Purview 'ın içindeki içerikleri kimler düzenleyebilir?
7. Purview 'daki veri kalitesini artırmak için hangi işlemleri kullanabilirim?
8. Mevcut kritik varlıklar, Sözlük terimleri ve ilgili kişilerle platformu önyükleme
9. Mevcut sistemlerle nasıl tümleştirilir?
10. Geri bildirim toplama ve sürdürülebilirlik bir işlem oluşturma

Bu soruların büyük bir kısmını hemen cevaplamadığınızda, kuruluşunuzun bu projeyi taşımasına ve tüm "gerekli-sahip" gereksinimlerinin karşılanmasını sağlamanıza yardımcı olabilir.

## <a name="include-the-right-stakeholders"></a>Doğru paydaşları dahil et

Tüm kuruluş için takip eden bir işlemin başarısını sağlamak üzere, doğru proje katılımcıları eklemek önemlidir. İlk aşamada yalnızca birkaç kişi yer alır. Ancak, kapsam genişledikçe projeye katkıda bulunmak ve geri bildirim sağlamak için ek kişiler gerekir.

Dahil etmek isteyebileceğiniz bazı anahtar hissedarları:

|Bilgisini|Roller|
|---------|---------|
|**Veri müdürü**|CDO, veri yönetimi, veri kalitesi, ana veri yönetimi, veri bilimi, iş zekası ve veri oluşturma stratejisi içerebilen bir dizi işlevi görür. Bu, purview uygulama projesinin sponsori olabilir.|
|**Etki alanı/Iş sahibi**|Araçların kullanımını etkileyen ve bütçe denetimi olan iş elemanı|
|**Veri Çözümleyici**|İş için bir sorunu çerçeveedebilir ve verileri çözümleyerek liderlerin iş kararları almasına yardımcı olun|
|**Veri Mimarı**|Veri güvenliği tasarlama ve uygulama ile birlikte görev açısından kritik iş kolu uygulamalarına yönelik veritabanları tasarlayın|
|**Veri Mühendisi**|Veri yığınını çalıştırma ve sürdürme, farklı kaynaklardan veri çekme, verileri tümleştirme ve hazırlama, veri işlem hatlarını ayarlama|
|**Veri Bilimcisi**|Analitik modeller oluşturun ve API 'Ler tarafından erişilecek veri ürünlerini ayarlayın|
|**DB Yöneticisi**|Hizmet düzeyi sözleşmeleri (SLA 'Lar) içindeki veritabanıyla ilgili olayları ve istekleri kendi kendinize, takip edin ve çözün; Veri işlem hatlarını ayarlayabilir|
|**DevOps**|Iş kolu uygulaması geliştirme ve uygulama; betikleri ve düzenleme yeteneklerini yazmayı içerebilir|
|**Veri güvenliği uzmanı**|Önemli ağ ve veri güvenliğini değerlendirin.|

## <a name="identify-key-scenarios"></a>Anahtar senaryolarını tanımla

Purview, bulut ve şirket içi ortamları kapsayan bir kuruluşun verileri genelinde veri yönetimini merkezi olarak yönetmek için kullanılabilir. Başarılı bir uygulamaya sahip olmak için, iş için kritik olan önemli senaryoları belirlemeniz gerekir. Bu senaryolar, iş birimi sınırlarını düzenleyebilir veya birden çok kullanıcıyı yukarı akış ya da aşağı akış olarak etkileyebilir.

Bu senaryolar çeşitli yollarla yazılabilir, ancak en az şu beş boyutu eklemeniz gerekir:

1. Kişi: kullanıcılar kim?
2. Kaynak sistem: Azure Data Lake Storage 2. veya Azure SQL veritabanı gibi veri kaynakları nelerdir?
3. Etki alanı: bu senaryonun kategorisi nedir?
4. Ayrıntılı senaryolar: kullanıcıların sorunları çözümlemek için purview kullanma şekli nelerdir?
5. Beklenen sonuç: başarı kriterleri nedir?

Senaryolar, ölçülebilir sonuçlarla belirli, işlem yapılabilir ve çalıştırılabilir olmalıdır. Kullanabileceğiniz bazı örnek senaryolar:

|Senaryo|Ayrıntı|Bilgisini|
|---------|---------|---------|
|İş açısından kritik varlıkları katalogla|Ne olduğunu iyi anlamak için her veri kümesiyle ilgili bilgilere sahip olmanız gerekir. Bu senaryo, katalogdaki veri kümesiyle ilgili olarak hem iş hem de teknik meta veri verilerini içerir. Veri kaynakları Azure Data Lake Storage 2., Azure SYNAPSE DW ve/veya Power BI içerir. Bu senaryo, SQL Server gibi şirket içi kaynağı da içerir.|İş analist, veri Bilimconu, veri mühendisi|
|İş açısından kritik varlıkları bulma|Katalogdaki tüm meta verilerde arama yapmak için bir arama motoruna gerek duydum. Joker karakter kullanarak basit ya da karmaşık arama ile teknik dönem, iş şartı kullanarak arama yapabiliyorum.|İş analist, veri Bilimconu, veri mühendisi, veri Yöneticisi|
|Kaynağını anlamak ve veri sorunlarını gidermek için verileri izleyin|Raporlar, tahminler veya modellerdeki verileri, özgün kaynağına geri izlemek ve değişiklikleri anlamak ve verilerin veri yaşam döngüsü boyunca bulunduğu yerleri anlamak için Data kökenini 'a sahip olmanız gerekir. Bu senaryonun, ve Databricks Azure Data Factory önceliklendirilmiş veri işlem hatlarını desteklemesi gerekir.|Veri mühendisi, veri bilimcisi|
|Kritik veri varlıklarıyla verileri zenginleştirin|Katalogdaki veri kümesini otomatik olarak oluşturulan teknik meta verilerle zenginleştirmem gerekiyor. Sınıflandırma ve etiketleme bazı örneklerdir.|Veri mühendisi, etki alanı/Işletme sahibi|
|Kolay Kullanıcı deneyimiyle veri varlıklarını yönetir|İşe özel meta veriler için Iş sözlüğün olması gerekir. İş kullanıcıları, kendi verilerine açıklama eklemek ve verilerin arama yoluyla kolayca keşfedilmesini sağlamak için self servis senaryoları için takip edebilir.|Etki alanı/Iş sahibi, Iş analist, veri Bilimconu, veri mühendisi|

## <a name="deployment-models"></a>Dağıtım modelleri

Temel Tüketim kullanım örnekleri ile takip eden yalnızca bir küçük grubunuz varsa, yaklaşım, tüm gruba hizmet vermek için bir takip görünümü örneğine sahip olmak kadar basittir. Bununla birlikte, kuruluşunuzun birden fazla purview örneğine ihtiyacı olup olmadığını da merak edebilirsiniz. Birden çok takip eden örnek kullanıyorsanız, çalışanlar varlıkları bir aşamadan diğerine nasıl yükseltebilirler.

### <a name="determine-the-number-of-purview-instances"></a>Takip görünümü örneklerinin sayısını belirleme

Çoğu durumda, kuruluşun tamamı için yalnızca bir purview hesabı olmalıdır. Bu yaklaşım, platform değerinin platformun içinde yer alan verilerin bir işlevi olarak üstel olarak arttığı "ağ etkileri" nin en yüksek avantajlarından yararlanır.

Ancak, bu düzenin özel durumları vardır:

1. **Yeni yapılandırmaların test edilmesi** – kuruluşlar, yalıtılmış ortamlarda tarama yapılandırmalarının veya sınıflandırmaların test edilmesi için birden çok örnek oluşturmak isteyebilir. Örneğin sözlük gibi platformun bazı alanlarında "sürüm oluşturma" özelliği olsa da, serbestçe test etmek için "atılabilir" örneğinin olması daha kolay olacaktır.
2. **Test, ön üretim ve üretimi ayırma** – kuruluşlar farklı ortamlarda depolanan farklı veri türleri için farklı platformlar oluşturmak ister. Bu tür verilerin farklı içerik türleri olması önerilir. Üst hiyerarşi düzeyinde veya kategoride sözlük terimi kullanarak içerik türlerini ayırabilirsiniz.
3. **Yarışmaları ve federe model** – yarışmaların genellikle ayrı olarak çalışan çok sayıda iş birimi (Bus) vardır ve bazı durumlarda, faturalandırmayla aynı şekilde de faturalandırılır. Bu gibi durumlarda kuruluş her bir BU için bir takip görünümü örneği oluşturmayı sona acaktır. Bu model ideal değildir, ancak özellikle de veri yolu faturalandırmayı paylaşmak istemediğinden gerekli olabilir.
4. **Uyumluluk** : hatta meta verileri hassas olarak kabul eden ve belirli bir Coğrafya 'da olmasını gerektiren bazı sıkı uyumluluk Regimes vardır. Bir şirkette birden çok coğrafi konum varsa tek çözüm, her coğrafya için bir tane olmak üzere birden çok takip eden örneğe sahip olur.

### <a name="create-a-process-to-move-to-production"></a>Üretime geçmek için bir işlem oluşturma

Bazı kuruluşlar, purview 'un tek bir üretim sürümü ile çalışarak şeyleri basit tutmaya karar verebilir. Büyük olasılıkla bulma, arama ve gezinme senaryolarından daha fazla gitmeleri gerekmez. Bazı varlıkların Sözlük terimleri yanlış ise, insanların kendine doğru olmasını sağlamak oldukça önemlidir. Bununla birlikte, çeşitli iş birimleri genelinde takip etmek isteyen kuruluşların çoğu bir işlem ve Denetim formu olmasını ister.

Üretim sürecinizi dahil etmek için başka bir önemli boyut, sınıflandırmaların ve etiketlerin nasıl geçirilebileceğini de kapsar. Purview, 90 sistem sınıflandırıcılara sahiptir. Dosya, tablo veya sütun varlıklarına sistem veya özel sınıflandırmalar uygulayabilirsiniz. Sınıflandırmalar konu etiketlerine benzer ve tarama sırasında veriniz içinde bulunan belirli bir türün içeriğini işaretlemek ve tanımlamak için kullanılır. Duyarlılık etiketleri, kuruluş verilerinizde sınıflandırma türlerinin kategorilerini belirlemek için kullanılır ve ardından her kategoriye uygulamak istediğiniz ilkeleri gruplayın. Microsoft 365 ile aynı hassas bilgi türlerini kullanır. böylece, tüm içeriklerinizi ve verilerinizi kapsayan mevcut güvenlik ilkelerinizi ve korumanızı uzatmanıza olanak sağlar. Belgeleri tarayabilir ve otomatik olarak sınıflandırabilir. Örneğin, multiple.docx adlı bir dosyanız varsa ve içeriğinde Ulusal KIMLIK numarası varsa, purview varlık ayrıntısı sayfasında AB Ulusal kimlik numarası gibi sınıflandırma ekler.

Purview 'ta Katalog yöneticilerinin yaşam döngüsü boyunca tutarlılık ve bakım en iyi yöntemlerini güvence altına almak için ihtiyaç duyduğu birkaç alan vardır:

* **Veri varlıkları** – veri kaynaklarının ortamlar arasında yeniden taranıp olması gerekir. Yalnızca geliştirmede tarama yapmanız ve ardından üretim aşamasındaki API 'Leri kullanarak bunları yeniden oluşturmanız önerilmez. Asıl neden, bu tarayıcıların, verileri farklı bir takip görünümü örneğine taşımak karmaşık olabilecek, veri varlıkları üzerindeki arka planda daha fazla "kablolama" yapabileceği anlamına gelir. Yalnızca aynı veri kaynağını üretime eklemek ve kaynakları yeniden taramak çok daha kolay. En iyi yöntem, kullanılan tüm taramaların, bağlantıların ve kimlik doğrulama mekanizmalarının belgelerinden biridir.
* **Kural kümelerini Tara** – bu, algılamaya yönelik dosya türü ve sınıflandırmalar gibi belirli bir taramaya atanan kuralların koleksiyonudur. Bu çok sayıda tarama kuralı ayarlamazsanız, bunları üretim aracılığıyla el ile yeniden oluşturmak mümkündür. Bu, iç işlem ve iyi bir belge gerektirir. Ancak, filtre kümelerini günlük veya haftalık olarak değiştirirseniz, bu, REST API rota araştırılarak çözülebilir.
* **Özel sınıflandırmalar** : sınıflandırmalarınız, düzenli olarak aynı zamanda değişebilir. Dağıtımın ilk aşamasında, özel sınıflandırmalarla ilgili çeşitli gereksinimlerin anlaşılması biraz zaman alabilir. Ancak, bu işlem kapatıldığında çok az değişiklik yapılması gerekir. Bu nedenle buradaki öneri, tüm özel sınıflandırmaları el ile geçirmek veya REST API kullanmak için gereklidir.
* **Sözlük** : bir UX aracılığıyla sözlük koşullarını dışa ve içe aktarmak mümkündür. Otomasyon senaryolarında REST API de kullanabilirsiniz.
* **Kaynak kümesi model ilkeleri** – Bu işlevsellik, her türlü tipik kuruluşun uygulanabilmesi için oldukça gelişmiş bir davranıştır. Bazı durumlarda, Azure Data Lake Storage klasör adlandırma kurallarına ve belirli bir yapıya sahiptir ve bu durum, kaynak kümesini oluşturmak için purview 'a sorun oluşmasına neden olabilir. İş biriminiz, kaynak kümesi oluşturmayı iş ihtiyaçlarına uyacak şekilde ek özelleştirmeler ile değiştirmek de isteyebilir. Bu senaryo için, REST API aracılığıyla tüm değişiklikleri izlemek ve değişiklikleri dış sürüm oluşturma platformunda belgelemek en iyisidir.
* **Rol ataması** : Bu, ne, ne olduğunu ve sahip oldukları izinleri kim ve hangilerinin erişebileceğini denetlediğiniz yerdir. Ayrıca, Kullanıcı ve rollerin dışarı ve içeri aktarılmasını desteklemek için de REST API vardır ancak bu, Atlas API uyumlu değildir. Bunun yerine, bir Azure Güvenlik grubu atamak ve grup üyeliğini yönetmek için öneri vardır.

### <a name="plan-and-implement-different-integration-points-with-purview"></a>Purview ile farklı tümleştirme noktaları planlayın ve uygulayın

Çok büyük bir kuruluşun zaten mevcut bir veri kataloğu vardır. Temel soru, var olan teknolojinin kullanılıp kullanılmayacağını ve purview ile eşitlenmesine devam edilip edilmeyeceğini belirtir. Bir kuruluştaki mevcut ürünlerle eşitlemeyi işlemek için, purview, Atlas REST API 'Leri sağlar. Atlas API 'Leri, hem itme hem de çekme senaryolarını işlemek için güçlü ve esnek bir mekanizma sağlar. Bilgiler, önyükleme için Atlas API 'Leri kullanarak veya başka bir sistemden en son güncelleştirmeleri takip edilecek şekilde göndererek takip edebilir. Purview 'da bulunan bilgiler, Atlas API 'Leri kullanılarak okunabilir ve ardından mevcut ürünlere geri eşitlenebilir. 

Anahtar oluşturma, özel kullanıcı arabirimi ve düzenleme gibi diğer tümleştirme senaryolarında Atlas API 'Leri ve Kafka uç noktalarını kullanabilirsiniz. Genel olarak, purview ile dört tümleştirme noktası vardır:

* **Veri varlığı** – bu, bu varlıkların ne olduğunu listelemek ve bunlarla ilgili tüm kullanılabilir meta verileri toplamak için bir deponun varlıklarını taramasını sağlar. Bu nedenle, SQL bu gibi yerlerde tutulan veritabanları, tablolar, saklı yordamlar, görünümler ve yapılandırma verilerinin bir listesi olabilir `sys.tables` . Azure Data Factory (ADF) gibi bir şeyler için bu, tüm işlem hatlarını listelemek ve Oluşturulma sırasında, son çalıştırılan, geçerli durum üzerinde veri almak olabilir.
* **Kökenini** – bu, takiview 'ın verilerin nasıl gezinme konusunda analiz/veri mutasyon sisteminden bilgi toplamasını sağlar. Spark gibi bir şey için, Not defterinin yürütüldüğü verileri, nasıl dönüştürüleceklerini ve nereye kopyalandığını görmek için bir not defterinin yürütülmesinden bilgi toplanmalıdır. SQL gibi bir şey için, hangi mutasyon işlemlerinin yürütüldüğü ve ne yaptığımız ile ilgili tersine mühendislik uygulanacak sorgu günlüklerini analiz edilebilir. Gereksinimlerinize bağlı olarak hem itme hem de çekme tabanlı kökenini destekliyoruz.
* **Sınıflandırma** : Bu, purview 'ın veri kaynaklarından fiziksel örnekler almasını ve sınıflandırma sistemimizle bunları çalıştırmasını sağlar. Sınıflandırma sistemi bir veri parçasının semantiğini belirler. Örneğin, bir dosyanın bir Parquet dosyası olduğunu ve üç sütunu olduğunu ve üçüncü birinin bir dize olduğunu biliyoruz. Ancak örneklerde çalıştırdığımız sınıflandırıcılar, dizenin bir ad, adres veya telefon numarası olduğunu bize bildirir. Bu tümleştirme noktasını aydınlatma, purview 'ın Not defterleri, işlem hatları, Parquet dosyaları, tablolar ve kapsayıcılar gibi nesneleri nasıl açabildiğimiz anlamına gelir.
* **Katıştırılmış deneyim** – "Studio" gibi deneyım (ADF, SYNAPSE, SQL Studio, PBI ve Dynamics gibi), genellikle kullanıcıların etkileşimde bulunmak istedikleri verileri bulmasını ve ayrıca çıktı verilerine yönelik yerleri bulmasını sağlamak ister. Takip etme kataloğu, bir ekleme deneyimi sağlayarak bu deneyimleri hızlandırmaya yardımcı olabilir. Bu deneyim, iş ortağının seçeneğinde API veya UX düzeyinde gerçekleşebilir. Kuruluş, purview 'a bir çağrı katıştırarak veri varlıklarını bulmak için veri kaynaklarını bulma, bkz. kökenini, şemaları denetleme, derecelendirmelere bakma, kişiler vb.

## <a name="phase-1-pilot"></a>1. Aşama: pilot

Bu aşamada, çok küçük bir kullanıcı kümesi için purview oluşturulmalı ve yapılandırılmalıdır. Genellikle, tek bir grup 2-3, birlikte çalışarak uçtan uca senaryolar aracılığıyla çalışmak üzere çalışır. Kuruluşlarındaki purview 'ın advoki olarak değerlendirilir. Bu aşamanın ana amacı, önemli işlevlerin karşılanabileceği ve doğru paydaşların projenin farkında olduğundan emin olmaktır.

### <a name="tasks-to-complete"></a>Tamamlanacak görevler

|Görev|Ayrıntı|Süre|
|---------|---------|---------|
|Gereksinimlerde & kabul edin|Eksiksiz bir gereksinim kümesi toplamak için tüm paydaşlarla tartışın. Farklı kişiler, projenin her aşaması için tamamlanacak gereksinimlerin bir alt kümesini kabul etmek üzere katılması gerekir.|1 hafta|
|Başlangıç paketini ayarlama|[Purview hızlı başlangıç](create-catalog-portal.md) ' e gidin ve tüm paydaşlara demo yapmak için [purview başlangıç paketini](tutorial-scan-data.md) ayarlayın.|1 gün|
|Takip görünümü gezinti|Giriş sayfasından purview kullanmayı anlayın.|1 gün|
|Kökenini için ADF 'yi yapılandırma|Anahtar işlem hatlarını ve veri varlıklarını belirler. Bir iç ADF hesabına bağlanmak için gereken tüm bilgileri toplayın.|1 gün|
|Azure Data Lake Storage gibi bir veri kaynağını tarayın|Veri kaynağını ekleyin ve bir tarama ayarlayın. Taramanın tüm varlıkları başarıyla algıladığını doğrulayın.|2 gün|
|Ara ve araştır|Son kullanıcıların purview 'a erişmesine ve uçtan uca arama ve gezinme senaryolarına izin verin.|1 gün|

### <a name="acceptance-criteria"></a>Kabul ölçütleri

* Purview hesabı kuruluş kiracısında kuruluş aboneliğinde başarıyla oluşturuldu.
* Birden çok rolü olan küçük bir Kullanıcı grubu, purview 'a erişebilir.
* Takip görünümü en az bir veri kaynağını taramak üzere yapılandırılmıştır.
* Kullanıcılar, şu gibi bir takip görünümü 'nin anahtar değerlerini ayıklayabilmelidir:
  * Ara ve araştır
  * Köken
* Kullanıcılar varlık sayfasından varlık sahipliğini atayabilmelidir.
* Önemli hissedarlara daha fazla tanıma yükseltmek için sunum ve tanıtım.
* MVP aşamasına yönelik ek kaynakları onaylamak için yönetimden satın alın.

## <a name="phase-2-minimum-viable-product"></a>2. Aşama: en düşük uygulanabilir ürün

Takip edilen gereksinimlere ve katılan iş birimlerine bir adım eklemek için, bir sonraki adım en düşük uygun ürün (MVP) sürümü üzerinde çalışır. Bu aşamada, takip görünümü kullanımını, yatay ve dikey olarak ek ihtiyaçlarına sahip olacak daha fazla kullanıcıya genişletecaksınız. Sözlük terimleri, arama ve tarama gibi tüm kullanıcılar için yatay olarak karşılanması gereken önemli senaryolar olacaktır. Ayrıca, her bir iş birimi veya grubu için kökenini gibi belirli bir uçtan uca senaryoyu kapsayacak şekilde, her bir departman veya grubun, Power BI Azure Data Lake Storage.

### <a name="tasks-to-complete"></a>Tamamlanacak görevler

|Görev|Ayrıntı|Süre|
|---------|---------|---------|
|[Azure SYNAPSE Analytics 'i tarama](register-scan-azure-synapse-analytics.md)|Veritabanı kaynaklarınızı ekleme ve bunları önemli varlıkları doldurmak üzere taramaya başlayın|2 gün|
|[Özel sınıflandırmalar ve kurallar oluşturma](create-a-custom-classification-and-classification-rule.md)|Varlıklarınız tarandıktan sonra kullanıcılarınız, takip eden varsayılan sınıflandırmaların yanında daha fazla sınıflandırma için ek kullanım durumları olduğunu fark edebilir.|2-4 hafta|
|[Tarama Power BI](register-scan-power-bi-tenant.md)|Kuruluşunuz Power BI kullanıyorsa, veri bilimcileri veya veri analistleri tarafından kullanılan tüm veri varlıklarını, depolama katmanından kökenini dahil etmek için gereksinimleri olan bir araya getirmek üzere Power BI tarama yapabilirsiniz.|1-2 hafta|
|[Sözlük koşullarını içeri aktar](how-to-create-import-export-glossary.md)|Çoğu durumda, kuruluşunuz varlık için bir sözlük terimleri ve terim ataması koleksiyonu zaten geliştirebilir. Bu,. csv dosyası aracılığıyla bir içeri aktarma işlemi yapılmasını gerektirir.|1 hafta|
|Varlıklara kişi ekleme|Popüler varlıklar için, diğer kişilerin kişileri atamasına veya REST API 'Leri aracılığıyla içeri aktarmaya izin veren bir işlem oluşturmak isteyebilirsiniz.|1 hafta|
|Gizli Etiketler ve tarama ekleme|Bu, M365 ' dan etiketleme kullanımına bağlı olarak bazı kuruluşlar için isteğe bağlı olabilir.|1-2 hafta|
|Sınıflandırmayı ve hassas öngörüleri edinin|Raporlamadaki raporlama ve öngörü için, çeşitli raporlar elde etmek ve yönetimi için sunum sağlamak üzere bu işlevselliğe erişebilirsiniz.|1 gün|
|Yönetilen kullanıcıları takip eden kullanıcı ekleme|Bu adım, purview yöneticisinin yeni güvenlik grupları oluşturmak için Azure Active Directory Yöneticisi ile çalışmasını gerektirir.|1 hafta|

### <a name="acceptance-criteria"></a>Kabul ölçütleri

* Daha büyük bir kullanıcı grubunu Takiview 'a başarıyla ekleme (50 +)
* İş açısından kritik veri kaynaklarını Tara
* Tüm kritik sözlük koşullarını içeri ve ata
* Anahtar varlıklarda önemli etiketlemenin başarıyla test edilmesi
* Katılmış iş birimleri kullanıcıları için en düşük senaryolar başarıyla karşılanmadı

## <a name="phase-3-pre-production"></a>3. Aşama: ön üretim

MVP aşaması geçtikten sonra, üretim öncesi kilometre taşını planlamaya zaman atalım. Kuruluşunuz, ön üretim ve üretim için ayrı bir purview örneğine izin verebilir veya aynı örneği koruyabilir ancak erişimi kısıtlayabilir. Ayrıca, bu aşamada SQL Server gibi şirket içi veri kaynaklarına tarama dahil etmek isteyebilirsiniz. Veri kaynaklarında, purview tarafından desteklenmeyen bir boşluk varsa, ek seçenekleri anlamak için Atlas API 'sini araştırmanız zaman alabilir.

### <a name="tasks-to-complete"></a>Tamamlanacak görevler

|Görev|Ayrıntı|Süre|
|---------|---------|---------|
|Tarama kural kümesini kullanarak taramayı daraltın|Kuruluşunuz ön üretim için çok fazla veri kaynağına sahip olacaktır. Sınıflandırmalar ve dosya uzantısının Pano genelinde tutarlı bir şekilde uygulanabilmesi için, tarama için anahtar ölçütlerini önceden tanımlamanız önemlidir.|1-2 gün|
|Tarama için bölge kullanılabilirliğini değerlendirme|Veri kaynaklarının bölgesine ve uyumluluk ve güvenlik üzerindeki kuruluş gereksinimlerine bağlı olarak, hangi bölgelerin taramak için kullanılabilir olması gerektiğini düşünmek isteyebilirsiniz.|1 gün|
|Tarama sırasında güvenlik duvarı kavramını anlama|Bu adım, kuruluşun güvenlik duvarını nasıl yapılandırdığını ve veri kaynaklarına tarama için erişmek üzere purview 'in kimliğini nasıl doğrulayacağını araştırmayı gerektirir.|1 gün|
|Tarama sırasında özel bağlantı kavramını anlama|Kuruluşunuz özel bağlantı kullanıyorsa, gereksinimlerin bir parçası olarak özel bağlantıyı dahil etmek için ağ güvenliği temelini oluşturmanız gerekir.|1 gün|
|[Şirket içi SQL Server Tara](register-scan-on-premises-sql-server.md)|Şirket içi SQL Server varsa bu isteğe bağlıdır. Tarama, [Şirket içinde barındırılan Integration Runtime](manage-integration-runtimes.md) ayarlamayı ve veri kaynağı olarak SQL Server eklemeyi gerektirir.|1-2 hafta|
|Tümleştirme senaryoları için purview REST API kullanma|Purview 'ı düzenleme veya bilet oluşturma sistemi gibi diğer üçüncü taraf teknolojileriyle tümleştirmede gereksinimleriniz varsa, REST API alanı araştırmak isteyebilirsiniz.|1-4 hafta|
|Purview fiyatlandırmasını anlayın|Bu adım, kuruluşun karar vermesi için önemli finansal bilgileri sağlar.|1-5 gün|

### <a name="acceptance-criteria"></a>Kabul ölçütleri

* Tüm kullanıcıları olan en az bir iş birimi başarıyla yüklendi
* SQL Server gibi şirket içi veri kaynağını tarayın
* REST API kullanarak en az bir tümleştirme senaryosu POC
* Bir planı, altyapı ve güvenlikle ilgili anahtar alanların dahil olması gereken üretime gitmek için tamamlayın

## <a name="phase-4-production"></a>4. Aşama: üretim

Daha iyi idare programları için temel olan etkili bir bilgi yönetimi oluşturmak için yukarıdaki aşamaların izlenmesi gerekir. Veri yönetimi, kuruluşunuzun AI, Hadoop, IoT ve blok zinciri gibi artan eğilimler için hazırlanmasına yardımcı olur. Bu yalnızca birçok şey için veri ve analiz için başlar ve ele alınılan çok daha fazla şey vardır. Bu çözümün sonucu şunları sağlar:

* **Iş odaklı** -teknik gereksinimler üzerinde iş gereksinimlerine ve senaryolara hizalanmış bir çözümdür.
* **Gelecekte hazır** -bir çözüm, platformun varsayılan özelliklerini en üst düzeye çıkarır ve platformun gelişiminde/evrimini desteklemek üzere yapılandırma veya betik oluşturma etkinlikleri için standartlaştırılmış sektör uygulamalarını kullanır.

### <a name="tasks-to-complete"></a>Tamamlanacak görevler

|Görev|Ayrıntı|Süre|
|---------|---------|---------|
|Güvenlik Duvarı etkinken üretim verileri kaynaklarını Tara|Güvenlik Duvarı yerinde olduğunda bu isteğe bağlıdır, ancak altyapınızın sağlamlaştırılmasını sağlamak için seçenekleri incelemek önemlidir.|1-5 gün|
|Özel bağlantıyı etkinleştir|Özel bağlantı kullanıldığında bu isteğe bağlıdır. Aksi takdirde, özel etkin olduğunda bir olması gereken bir ölçüt olduğu için bunu atlayabilirsiniz.|1-5 gün|
|Otomatikleştirilmiş iş akışı oluşturma|İş akışı, onay, yükseltme, İnceleme ve sorun yönetimi gibi işlemleri otomatikleştirmek için önemlidir.|2-3 hafta|
|İşlem belgeleri|Veri yönetimi tek seferlik bir proje değil. Veri odaklı karar verme ve iş fırsatlarının oluşturulması için sürekli bir programdır. Anahtar yordamı ve iş standartlarını belgelemek önemlidir.|1 hafta|

### <a name="acceptance-criteria"></a>Kabul ölçütleri

* Tüm iş birimleri ve kullanıcıları başarıyla yüklendi
* Üretim için altyapı ve güvenlik gereksinimleri başarıyla karşılayamadı
* Kullanıcılar için gereken tüm kullanım durumları başarıyla karşılayamadı

## <a name="platform-hardening"></a>Platform sağlamlaştırma

Ek sağlamlaştırma adımları alınabilir:

* Güvenlik Duvarı kaynaklarında taramayı etkinleştirerek veya özel bağlantıyı kullanırken güvenlik duruşunu artırma
* Tarama performansını artırmak için kapsam taramasını ince ayar yapın
* Yedekleme ve kurtarma için kritik meta verileri ve özellikleri dışarı aktarmak için REST API 'Lerini kullanma
* İnsan hatalarını önlemek için bilet ve olay işlemlerini otomatikleştirmek için iş akışını kullanın

## <a name="next-steps"></a>Sonraki adımlar

- [Öğretici: başlangıç paketini çalıştırma ve verileri tarama](tutorial-scan-data.md)
- [Öğretici: giriş sayfasında gezinme ve varlık arama](tutorial-asset-search.md)