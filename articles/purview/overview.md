---
title: Azure purview 'a giriş (Önizleme)
description: Bu makalede, özellikleri ve adresleyen sorunlar da dahil olmak üzere Azure purview 'a genel bakış sunulmaktadır. Azure takip görünümü, herhangi bir kullanıcının veri kaynaklarını kaydetmesini, bulmasını, anlamasını ve kullanmasını sağlar.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 11/30/2020
ms.openlocfilehash: 3fadfa9d4467209ddbe997afabba27a3752f685d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103419941"
---
# <a name="what-is-azure-purview"></a>Azure Purview nedir?

> [!IMPORTANT]
> Azure takip görünümü şu anda ÖNIZLEME aşamasındadır. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) , Beta, önizleme veya başka bir şekilde genel kullanıma sunulmayan Azure özelliklerine uygulanan ek yasal koşulları içerir.

Azure purview, şirket içi, çok bulut ve hizmet olarak yazılım (SaaS) verilerinizi yönetmenize ve yönetmenize yardımcı olan Birleşik bir veri idare hizmetidir. Otomatik veri bulma, hassas veri sınıflandırması ve uçtan uca veri kökenini ile verilerinizin yatay olarak bir bütünsel, güncel haritasını kolayca oluşturun. Değerli ve güvenilir verileri bulmak için veri tüketicilerini güçlendirin.

Azure purview veri eşlemesi, veri bulma ve etkili veri yönetimi için temel sağlar. Purview veri eşlemi, şirket içinde ve bulutta bulunan analiz ve işletim sistemlerinde bulunan kurumsal verilerle ilgili meta verileri yakalayan bir bulut Yerel PaaS hizmetidir. Yerleşik otomatik tarama ve sınıflandırma sistemi sayesinde purview veri eşlemesi otomatik olarak güncel tutulur. İş kullanıcıları, bir sezgisel kullanıcı arabirimi aracılığıyla purview veri eşlemini yapılandırabilir ve kullanabilir ve geliştiriciler açık kaynaklı Apache Atlas 2,0 API 'Lerini kullanarak veri eşlemesiyle programlı bir şekilde etkileşim kurabilir.

Azure purview veri haritası, purview veri kataloğunu güçlendirir ve Data Insights 'ı purview Studio 'daki birleştirilmiş deneyimler olarak takip edin.
 
Purview Veri Kataloğu ile, iş ve teknik kullanıcılar, Sözlük terimleri, sınıflandırmalar, duyarlılık etiketleri ve daha fazlası gibi çeşitli mercekler temelinde, bir arama deneyimi kullanarak ilgili verileri kolayca bulabilir &. Konu uzmanları, veri uzmanları ve officekullanıcıları için, takip görünümü veri kataloğu, iş sözlüğü yönetimi ve veri varlıklarının sözlük koşullarına göre etiketlemesini otomatik hale getirme yeteneği gibi veri kullanımı özellikleri sağlar. Veri tüketicileri ve üreticileri, aynı zamanda şirket içindeki işletimsel sistemlerden başlayarak Şirket içindeki çalışma sistemlerinden, taşıma, dönüştürme & zenginleştirme aracılığıyla, bulutta bulunan ve Power BI gibi bir analiz sisteminde tüketim sağlamak için çeşitli veri depolama & işleme sistemlerini de görsel olarak izleyebilir.

Veri ofisleri ve güvenlik ofislerinin Izlenmesi sayesinde, veri ofislerinin ve güvenlik ofislerinin bir kuşbakışı görünümü alabilir ve gizli verilerin ne olduğu ve nasıl taşındığı, hangi verilerin etkin şekilde tarandığı hakkında bir bakışta göz atalım.

## <a name="discovery-challenges-for-data-consumers"></a>Veri tüketicileri için bulma zorlukları

Geleneksel olarak, kurumsal veri kaynaklarını keşfetmek, işle ilgili bilgileri temel alan bir organik işlemdir. Bu yaklaşım, bilgi varlıklarından en fazla değer isteyen şirketler için birçok zorluk gösterir:

* Veri kaynaklarını kaydettirmek için merkezi bir konum olmadığından, kullanıcılar başka bir işlemin parçası olarak sizinle iletişim kurmadıkça bir veri kaynağı farkında olabilir.
* Kullanıcılar bir veri kaynağının konumunu bilemediği müddetçe, bir istemci uygulaması kullanarak verilere bağlanamaz. Veri tüketimi deneyimleri, kullanıcıların bağlantı dizesini veya yolu bilmesini gerektirir.
* Verilerin amaçlanan kullanımı, bir veri kaynağının belgelerinin konumunu bilemedikleri müddetçe kullanıcılara gizlenir. Veri kaynakları ve belgeler birçok yerde bulunabilir ve farklı deneyimler aracılığıyla tüketilebilir.
* Kullanıcıların bir bilgi varlığıyla ilgili soruları varsa, verilerden sorumlu olan uzmanı veya ekibi bulması ve bunlarla çevrimdışı etkileşim kurması gerekir. Veriler ile kullanımı üzerinde perspektifi olan uzmanlar arasında açık bir bağlantı yoktur.
* Kullanıcılar veri kaynağına erişim isteme işlemini anlamadığı sürece, veri kaynağının ve belgelerinin bulunması veriye erişemez.

## <a name="discovery-challenges-for-data-producers"></a>Veri üreticileri için bulma zorlukları

Veri tüketicileri daha önce sözü geçen sorunlarla karşılaşırken, bilgi varlıkları oluşturmaktan ve bunların bakımını yapmaktan sorumlu kullanıcılar da kendilerine özgü zorluklarla yüz yüze gelmektedir:

* Veri kaynaklarına açıklayıcı meta verilerle ek açıklama ekleme, genellikle sonuç vermeyen bir işlemdir. İstemci uygulamaları genellikle veri kaynağında depolanan açıklamaları yok sayar.
* Veri kaynakları için belge oluşturma zor olabilir ve belgeleri veri kaynaklarıyla eşitlenmiş halde tutmak sürekli bir sorumluluktur. Kullanıcılar, güncel olmadığı algılanan belgelere güvenmeyebilir.
* Veri kaynaklarına yönelik belgelerin oluşturulması ve bakımının yapılması karmaşık ve zaman alan işlemlerdir. Bu belgelerin, veri kaynağını kullanan herkesin erişimine hazır hale getirilmesi ise daha da zordur.
* Veri kaynaklarına erişimin kısıtlanması ve veri tüketicilerinin nasıl erişim isteneceğini bilmesinin sağlanması sürekli karşılaşılan bir zorluktur.

Bu tür sorunlar birleştirildiğinde, kurumsal verilerin kullanımını ve öğrenilmesini teşvik etmek ve yükseltmek isteyen şirketler için önemli bir engel sunsun.

## <a name="discovery-challenges-for-security-administrators"></a>Güvenlik yöneticileri için bulma sorunları

Kuruluşun verilerinin güvenliğinin sağlanmasından sorumlu olan kullanıcılar, veri tüketicileri ve üreticileri olarak yukarıda listelenen güçlüklerden herhangi birine ve aşağıdaki ek zorlukları de içerebilir:

* Kuruluşun verileri sürekli büyümekte, depolanır ve yeni yönlere paylaşılır. Hassas verilerinizi bulma, koruma ve yöneten görev hiçbir şekilde bitmez. Kuruluşunuzun içeriğinin doğru kişilerle, uygulamalarla ve doğru izinlerle paylaşıldığından emin olmak istiyorsunuz.
* Kuruluşunuzun verilerinde risk düzeylerini anlamak, anahtar sözcükler, RegEx desenleri ve/veya hassas veri türleri gibi içeriklerinize ayrıntılı bir şekilde sahip olmanızı gerektirir. Hassas veri türleri, kredi kartı numaralarını, sosyal güvenlik numaralarını veya banka hesabı numaralarını, birkaç kez ad içerebilir. En düşük veri kaybı miktarı kuruluşunuzda önemli olabilecek şekilde hassas içerik için tüm veri kaynaklarını sürekli olarak izlemeniz gerekir.
* Kuruluşunuzun kurumsal güvenlik ilkeleriyle uyumlu olmaya devam ettiğinden, içeriğiniz büyüdükçe ve değiştikçe ve bu gereksinimler ve ilkeler dijital yeniden sayılanların değiştirilmesine yönelik olarak güncelleştirildiğinden zorlu bir görevdir. Güvenlik yöneticileri genellikle, mümkün olan en kısa sürede veri güvenliği sağlamaya göre yapılır.

## <a name="azure-purview-advantages"></a>Azure takip görünümü avantajları

Azure purview, önceki bölümlerde bahsedilen sorunları gidermek ve kuruluşların mevcut bilgi varlıklarından en iyi şekilde yararlanmalarına yardımcı olmak için tasarlanmıştır. Katalog, veri kaynaklarını verileri yöneten kullanıcılar tarafından kolayca bulunabilir ve anlaşılabilir hale getirir.

Azure purview, veri kaynaklarını kaydedebilmeniz için kullanabileceğiniz bulut tabanlı bir hizmet sağlar. Kayıt sırasında, veriler mevcut konumunda kalır, ancak meta verilerinin bir kopyası, veri kaynağı konumuna yönelik bir başvuru ile birlikte Azure purview 'e eklenir. Bu meta veriler ayrıca her bir veri kaynağının arama ile kolayca bulunabilmesini ve bunları bulan kullanıcılar tarafından anlaşılabilmesini sağlamak üzere dizine alınır.

Bir veri kaynağını kaydettikten sonra verileri zenginleştirebilirsiniz. Veri kaynağını ya da kuruluştaki başka bir kullanıcıyı kaydeden Kullanıcı meta verileri ekler. Herhangi bir Kullanıcı, veri kaynağı erişimi istemek için açıklamalar, Etiketler veya diğer meta veriler sağlayarak bir veri kaynağına ek açıklama ekleyebilir. Bu tanımlayıcı meta veriler, veri kaynağından kaydedilen, sütun adları ve veri türleri gibi yapısal meta verileri tamamlar.

Veri kaynaklarını ve bunların kullanımını bulup anlamak, kaynakların kaydedilmesindeki birincil amaçtır. Kurumsal kullanıcıların iş zekası, uygulama geliştirme, veri bilimi veya doğru verilerin gerektiği başka herhangi bir görevde verilere ihtiyacı olabilir. Veri Kataloğu bulma deneyimini, ihtiyaçları ile eşleşen verileri hızlı bir şekilde bulmak, amaca uygunluk durumunu değerlendirmek için verileri anlamak ve veri kaynağını tercih ettiğiniz aracında açarak verileri tüketmek için kullanırlar.

Diğer yandan, kullanıcılar zaten kaydettikleri veri kaynakları için etiketler, belgeler ve ek açıklamalar ekleyerek kataloğa katkıda bulunabilir. Ayrıca, Katalog kullanıcıları topluluğu tarafından bulunan, anlamış ve tüketilen yeni veri kaynaklarını kaydedebilirler.

## <a name="in-region-data-residency"></a>Bölge içi veri yerleşimi
Azure purview, müşteri verilerini dağıtıldığı bölgenin dışına taşımaz veya depolamaz.

## <a name="next-steps"></a>Sonraki adımlar

Azure purview ile çalışmaya başlamak için bkz. [Azure purview hesabı oluşturma](create-catalog-portal.md).
