---
title: Azure Veri Kataloğu'nu benimseme yaklaşımı ve işlemi
description: Bu makale, Azure Veri Kataloğu'nu benimsemeyi planlayan kuruluşlar için, vizyon tanımlama, işle ilgili önemli kullanım durumlarını belirleme ve pilot proje seçme aşamaları da dahil olmak üzere bir yaklaşım ve süreç sağlamaktadır.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 63d1213a2dded41df3f462722b354b03fe9f6e33
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72801885"
---
# <a name="approach-and-process-for-adopting-azure-data-catalog"></a>Azure Veri Kataloğu'nu benimseme yaklaşımı ve işlemi

Bu makale, kuruluşunuzda **Azure Veri Kataloğu**'nu benimsemeye başlamanıza yardımcı olacaktır. **Azure Veri Kataloğu**'nu başarıyla benimseyebilmek için üç temel öğeye odaklanırsınız: vizyonunuzu tanımlama, kuruluşunuz dahilindeki önemli iş kullanımı durumlarını belirleme ve pilot proje seçme.

## <a name="introducing-the-azure-data-catalog"></a>Azure Veri Kataloğu'na giriş

İş dünyasında insanların veri varlıkları hakkında uzman bilgilerine ulaşabilmek için başvuracakları yola yönelik beklentileri değişmiştir. Günümüzde Yammer gibi sosyal medya araçlarının iş yerinde yaygın şekilde kullanılmasıyla, insanlar çok çeşitli konularda hızlı bir şekilde yardım ve öneri alabilmeyi beklemektedir. **Azure Veri Kataloğu**, işletmelerin ve ekiplerin kurumsal veri varlıklarıyla ilgili bilgileri merkezi bir depoda bir araya getirmesine yardımcı olur. Veri tüketicileri, bu veri varlıklarını bulabilir ve konu uzmanlarının sunduğu bilgileri edinebilir.

Bu makalede, **Azure Veri Kataloğu** ile çalışmaya başlamaya yönelik bir yaklaşım sunulmaktadır. Makalede, Adventure Works adlı kurgusal şirket için genel bir Veri Kataloğu benimseme planı açıklanmaktadır.

**Azure Veri Kataloğu nedir?**

**Azure Veri Kataloğu**, Azure'da bulunan tam yönetimli bir hizmet olmasının yanı sıra self servis veri kaynağı bulmaya olanak tanıyan, kuruluş genelinde geçerli bir bilgi (meta veri) kataloğudur. Veri Kataloğu ile veri varlıklarını kaydeder, bulur, bunlara açıklama ekler ve bağlanırsınız. Veri Kataloğu, veri varlıklarının bulunmasını, anlaşılmasını ve bunlara bağlanılmasını kolaylaştırmak için farklı bilgi varlıklarını yönetecek şekilde tasarlanmıştır. Kullanılabilir verilerden bilgi edinme süresini kısaltır ve kuruluşların elde edebileceği değeri artırır. Daha fazla bilgi için bkz. [Microsoft Azure Veri Kataloğu](https://azure.microsoft.com/services/data-catalog/).

## <a name="azure-data-catalog-adoption-plan"></a>Azure Veri Kataloğu benimseme planı

**Azure Veri Kataloğu** benimseme planında, hizmet kullanımının sağladığı avantajların paydaşlara ve kullanıcılara nasıl iletildiği ve kullanıcılara sağladığınız eğitimin türü açıklanır. Veri Kataloğu'nu benimsemeye yönelik önemli bir başarı ölçütü, hizmetin değerini kullanıcılara ve paydaşlara iletmede göstereceğiniz etkinliktir. İlk benimseme planındaki birincil hedef kitleler, hizmetin kullanıcılarıdır. Paydaşlardan ne kadar destek alırsanız alın, Veri Kataloğu hizmetinizin kullanıcıları veya müşterileri bunu kendi kullanımlarına dahil etmediği sürece benimseme başarılı olmaz. Bu nedenle, bu makalede paydaş desteğine sahip olduğunuz varsayılarak Veri Kataloğu'nun kullanıcılar tarafından benimsenmesine yönelik bir plan oluşturulmasına odaklanılmıştır.
Etkili bir benimseme planı, kişilerin Veri Kataloğu'ndaki olanaklara ilgi göstermesini sağlar ve bunu elde etmek için gerekli bilgileri ve rehberliği onlara sunar. Kullanıcıların, Veri Kataloğu'nun kendi işlerinde başarılı olmalarına yardımcı olmak için sağladığı değeri kavraması gerekir. Kişiler Veri Kataloğu'nun verilerle daha fazla sonuç elde etmelerine yardımcı olabileceğini gördüğünde, Veri Kataloğu'nu benimsemenin değeri ortaya çıkar. Değişim zordur; bu nedenle etkili bir planda değişimin zorluklarının da hesaba katılması gerekir.

Benimseme planı, kişilerin başarılı olması ve hedeflerine ulaşması için kritik önem taşıyan unsurları iletmenize yardımcı olur. Genel bir plan, Veri Kataloğu'nun kullanıcının hayatını nasıl kolaylaştıracağını açıklar ve şu bölümleri içerir:

* **Vizyon Mesajı** - Bu, kullanıcılar ve paydaşlar ile benimseme planına yönelik olarak kısaca görüşmenize yardımcı olur. Bu, kısa sunum fırsatınızdır.
* **Pilot ekip ve Etki sahipleri** - Bir pilot ekipten ve etki sahiplerinden öğrenilenler, ekip ve kullanıcıları Veri Kataloğu ile tanıştırma konusunda ayrıntılı düzenlemeler yapmanıza yardımcı olur. Etki sahipleri, diğer kullanıcılara akran koçluğu yapabilir. Ayrıca bu yolla benimsemenin önündeki engelleri ve benimsemeyi teşvik eden unsurları da belirleyebilirsiniz.
* **İletişim ve Hareket Planı** - Bu, kullanıcıların Veri Kataloğu'nun kendilerine nasıl yardımcı olabileceğini anlamasına yardımcı olur, ekiplerde ve nihai olarak tüm kuruluş dahilinde organik bir benimseme elde edilmesini sağlayabilir.
* **Eğitim Planı** - Kapsamlı eğitim genellikle benimsemenin başarılı olmasına ve olumlu sonuçlara yol açar.

Aşağıda **Azure Veri Kataloğu** benimseme planını tanımlamaya yönelik birkaç ipucu verilmiştir.

## <a name="define-your-data-catalog-project-vision"></a>Veri Kataloğu proje vizyonunuzu tanımlama

**Azure Veri Kataloğu** benimseme planı tanımlamanın ilk adımı, elde etmeye çalıştığınız başarının istek uyandıran bir açıklamasını yazmaktır. Vizyon mesajını geniş tutmak, ancak kısa vadede ve uzun vadede ayrıntılı hedefler tanımlayacak kadar net olmasını sağlamak en iyi seçenektir.

Aşağıda vizyonunuzu tanımlamanıza yardımcı olacak birkaç ipucu verilmiştir:

* **Önemli dağıtım teşvikini tanımlama** - Veri Kataloğu kullanılarak ele alınabilecek, işle ilgili belirli veri kaynağı yönetim ihtiyaçlarını düşünün. Bu, Veri Kataloğu'nu kullanmanın en önemli avantajlarını belirtmenize yardımcı olur. Örneğin, tüm yeni çalışanların; hakkında bilgi edinmesi ve kullanması gereken genel veri kaynakları veya yalnızca birkaç anahtar kişinin ayrıntılı olarak kavraması gereken önemli ve karmaşık veri kaynakları söz konusu olabilir. **Azure Veri Kataloğu** bu veri kaynaklarının bulunmasını ve anlaşılmasını kolaylaştırmaya yardımcı olabilir; böylece, bilinen bu zorluk noktaları, hizmetin benimsenme aşamasında doğrudan ve erken bir noktada ele alınabilir.
* **Kısa ve net bir içerik sağlayın** - Vizyonun net bir şekilde anlaşılması, Veri Kataloğu'nun kuruluşa sağladığı değer ve vizyonun kuruluş hedeflerine verdiği destekle ilgili olarak herkesin aynı noktada buluşmasını sağlar.
* **Veri Kataloğu'nu kullanmaları için ilham verin** - Vizyonunuz ve iletişim planınız kişilere, verilerle daha fazlasını başarmak üzere veri kaynaklarını bulmak ve bunlara bağlanmak için Veri Kataloğu'ndan yararlanabileceklerini göstermelidir.
* **Hedefler ve zaman çizelgesi belirtin** - Bu, benimseme planınızın ayrıntılı ve erişilebilir sonuçlara sahip olmasını sağlar. Zaman çizelgesi herkesin odağını korur ve başarıyı ölçmeye yönelik denetim noktalarına olanak tanır.

Aşağıda, Adventure Works adlı kurgusal şirket için örnek bir Veri Kataloğu vizyon mesajı verilmiştir:

**Azure Veri Kataloğu** , Adventure Works finans ekibini önemli veri kaynakları üzerinde işbirliği yapmak üzere güçler, böylece her ekip üyesi ihtiyaç duydukları verileri kolayca bulabilir ve kullanabilir ve kendi bilgilerini ekiple bir bütün olarak paylaşabilir.

Net bir vizyon mesajınız olduğunda, Veri Kataloğu için uygun bir pilot proje tanımlamanız gerekir. Genellikle Veri Kataloğu için birkaç senaryo mevcuttur; bu nedenle sonraki bölümde ilgili kullanım durumlarını tanımlamaya yönelik birkaç ipucu sağlanmıştır.

## <a name="identify-data-catalog-business-use-cases"></a>Veri Kataloğu iş kullanım durumları tanımlama

Veri Kataloğu ile ilgili kullanım durumlarını tanımlamak için, çeşitli iş birimlerinden uzmanlarla görüşerek ilgili kullanım durumlarını ve çözülecek iş sorunlarını tanımlayın. Kişilerin veri varlıklarını tanımlama ve anlama konusunda karşılaştığı mevcut sorunları gözden geçirin. Örneğin, ekipler veri varlıkları hakkında bilgileri yalnızca kuruluş içinde ilgili veri kaynaklarına sahip olan birkaç kişiye danıştıktan sonra mı ediniyor?

Kolay hedefleri temsil eden kullanım durumlarının tercih edilmesi en iyi seçenektir; bu durumlar, önemli oldukları kadar Veri Kataloğu ile çözülerek yüksek oranda başarıya ulaşılacak durumlardır.

Aşağıda durumları tanımlamaya yönelik birkaç ipucu verilmiştir:

* **Ekibin hedeflerini tanımlayın** - Ekip hedeflerine nasıl ulaşır? Bu aşamada objektif olmak isteyeceğiniz için Veri Kataloğu'na odaklanmayın. Konunun teknoloji değil, iş sonuçları olduğunu unutmayın.
* **İş sorununu tanımlayın** - Ekibin veri varlıklarını bulma ve bunlar hakkında bilgi edinme konusunda karşılaştığı sorunlar nelerdir? Örneğin, önemli veri kaynakları ile ilgili bilgiler bir ağ klasöründeki Excel çalışma kitaplarında bulunabilir ve ekip, çalışma kitaplarını bulmak için çok fazla zaman harcayabilir.
* **Değişimle bağlantılı olarak ekip kültürünü anlayın** - Birçok benimseme zorluğu, yeni bir aracın uygulanmasından ziyade, değişime gösterilen dirençle ilgilidir. Bir ekibin değişime verdiği yanıt, kullanım durumlarının tanımlanması sırasında önem taşır. Bunun nedeni, var olan işlemin "her zaman böyle yapıldığı" için veya "bozuk olmayan bir şey neden düzeltilsin?" gerekçesi ile kullanımda olabileceği gerçeğidir. Herhangi bir yeni aracın veya işlemin benimsenmesi, durumdan etkilenen kişilerin değişimin getireceği değeri anlaması ve çözülecek sorunların önemini kabul etmesi durumunda her zaman çok daha kolay bir şekilde gerçekleşir.
* **Odağı veri varlıklarıyla ilgili olarak tut** -bir ekip yüzünde iş sorunlarını ele alırken, "Weeds aracılığıyla kesmeniz" gerekir ve kurumsal veri varlıklarını daha verimli bir şekilde kullanmaya yönelik yeniliklere odaklanırsınız.

Aşağıda Veri Kataloğu ile ilgili bazı örnek kullanım durumları verilmiştir:

### <a name="example-use-cases"></a>Örnek kullanım durumları

* **Merkezi yüksek değerli veri kaynaklarını kaydedin** - BT, kuruluş genelinde kullanılan veri kaynaklarını yönetir. BT, genel kurumsal veri kaynaklarını kaydederek ve bunlara açıklama ekleyerek Veri Kataloğu'nu kullanmaya başlayabilir.
* **Ekip tabanlı veri kaynaklarını kaydedin** - Farklı ekipler faydalı iş kolu veri kaynaklarına sahiptir. Birçok farklı ekip tarafından kullanılan önemli veri kaynaklarını tanımlayarak ve kaydederek **Azure Veri Kataloğu** ile çalışmaya başlayın ve ekibin bilgilerini **Azure Veri Kataloğu** ek açıklamalarına kaydedin.
* **Self servis iş zekası** - Ekipler, birden çok kaynaktan veri birleştirmeye çok fazla zaman ayırır. El ile veri kaynağı bulma işlemini ortadan kaldırmak için, veri kaynaklarını kaydetme ve bunlara açıklama ekleme işlemlerini merkezi bir konumda gerçekleştirin.

Veri Kataloğu senaryoları hakkında daha fazla bilgi edinmek için bkz. [Azure Veri Kataloğu genel senaryoları](data-catalog-common-scenarios.md).

Veri Kataloğu için birkaç kullanım durumu tanımlamanızın ardından, genel senaryolar ortaya çıkacaktır. Sonraki bölümde, bir kullanım durumunu temel alarak ilk pilot projenizi nasıl tanımlayacağınız anlatılmaktadır.

## <a name="choose-a-data-catalog-pilot-project"></a>Veri Kataloğu pilot projesini seçme

Önemli bir başarı unsuru, basitleştirmek ve küçük başlamaktır. İyi tanımlanmış, kısıtlı kapsama sahip bir pilot proje, fazla karmaşık olan veya çok fazla katılımcı içeren bir projeyle çıkmaza sürüklenmeden projenin ilerlemeye devam etmesine yardımcı olur. Ancak erken benimseyenlerden şüphecilere kadar karışık bir kullanıcı tabanının dahil edilmesi de önemlidir. Çözümü benimseyen kullanıcılar, gelecekteki iletişim ve hareket planınızı geliştirmenize yardımcı olur. Şüpheciler, engelleyici sorunları tanımlamanıza ve ele almanıza yardımcı olur. Şüpheciler kazananlara dönüşürken, başarıyı teşvik eden unsurları belirlemek için onların geri bildiriminden yararlanabilirsiniz.

Pilot planınız, Veri Kataloğu ile elde etmek istediğiniz iş hedeflerini kademeli olarak sunmalıdır. İlk pilot projeyle bilgilerinizi ilerlettikçe, kullanıcı tabanınızı genişletebilirsiniz. Başlangıç için kapalı bir pilot proje, ölçülebilir başarının belirlenmesinde yeterlidir ancak nihai hedef, organik veya viral büyümeye yöneliktir. Veri Kataloğu'nun organik büyümesiyle, kullanıcılar kendi veri kullanımı üzerinde denetim sahibi olur, başkalarını kataloğu benimseme ve kataloğa katkıda bulunma konusunda etkileyebilir ve teşvik edebilir.

### <a name="target-the-right-team"></a>Doğru ekibi hedefleme

Pilot projenizi belirlerken, var olan bir iş sorununu çözen en cazip senaryolara sahip ekibi seçin. Örneğin, bir iş analistinin SQL Server veritabanından rapor oluşturması. Sorun, yalnızca birkaç iş arkadaşını konuşduktan sonra veri kaynağını haberdar edebiliyor. Son olarak, hangi veri kaynaklarını kullanacağınızı bulmaya çalıştıktan sonra, her bir veri kaynağının açıklamasını içeren bir Excel çalışma kitabı hakkında daha fazla bilgi bulabilirsiniz. Excel çalışma kitabı, gerek duydukları tabloları yeterince açıklasa da, **Azure Veri Kataloğu**'nda kaydolduklarında ve açıklandıklarında bu veri kaynaklarını hızlıca bulmaları gerekir.

### <a name="identify-data-heroes"></a>Veri hero'larını tanımlama

Ekibin dengeli bir sunuma sahip olabilmesi için ilk projeniz, veri üreten ve veri tüketen birkaç kişi içermelidir.

**Veri Üreticileri** veri kaynakları hakkında uzmanlığı olan kişilerdir. Örneğin, başka bir ekipte bulunan David, Adventure Works'ün önemli veri kaynaklarıyla kapsamlı olarak çalışmıştır. David, **Azure Veri Kataloğu**'nun benimsenmesinden önce Adventure Works'ün veri kaynakları ile ilgili bilgileri yakalamak üzere bir Excel çalışma kitabı oluşturmuştur.

**Veri Tüketicileri**, verilerin iş sorunlarını çözmeye yönelik kullanımı konusunda uzmanlık sahibi kişilerdir. Örneğin, Nancy, veri analizi için Adventure Works SQL Server veri kaynaklarını kullanan bir iş analistidir.

**Azure Veri Kataloğu**'nun çözdüğü iş sorunlarından biri, **Veri Üreticileri**'nin **Veri Tüketicileri**’ne bağlanmasıdır. Bunu, kurumsal veri kaynakları hakkındaki bilgiler için merkezi bir depo görevi görerek gerçekleştirir. David, Veri Kataloğu'nu kullanarak Adventure Works ve SQL Server veri kaynaklarını kaydeder. Bu veri kaynağını bulan herhangi bir kullanıcının crowdsourusing özelliği, bulduğu verileri kullanmanın yanı sıra veri üzerinde kendi eklentilerini paylaşabilir. Örneğin, Nancy katalogda arama yaparak veri kaynaklarını bulur ve verilerle ilgili uzman bilgilerini paylaşır.  Böylece kuruluş bünyesindeki diğer kişiler katalogda arama yaparak paylaşılan bilgilerden faydalanabilir.

* Veri kaynaklarını kaydetme hakkında daha fazla bilgi için bkz. [Veri kaynaklarını kaydetme](data-catalog-get-started.md).
* Veri kaynaklarını bulma hakkında daha fazla bilgi için bkz. [Veri kaynaklarını arama](data-catalog-get-started.md).

### <a name="start-small-and-focused"></a>Küçük ve odaklı bir başlangıç

Çoğu kurumsal pilot projede, iş kullanıcılarının Veri Kataloğu'nun değerini hızlı bir şekilde görebilmesi için, kataloğu yüksek değere sahip çekirdek veri kaynaklarıyla doldurmanız gerekir. BT, pilot ekibinizin ilgisini çekecek genel veri kaynaklarını tanımlamak için iyi bir başlangıç noktasıdır. SQL Server gibi desteklenen veri kaynakları için **Azure Veri Kataloğu** veri kaynağı kayıt aracının kullanılmasını öneririz. Veri kaynağı kayıt aracıyla, SQL Server ve Oracle veritabanları ve SQL Server Reporting Services raporları da dahil olmak üzere çok çeşitli veri kaynaklarını kaydedebilirsiniz. Geçerli veri kaynaklarının tam listesi için bkz. [Azure Veri Kataloğu desteklenen veri kaynakları](data-catalog-dsr.md).

Önemli veri kaynaklarını tanımlamanızın ve kaydetmenizin ardından, başka konumlarda depolanan veri kaynağı açıklamalarının içeri aktarılması da mümkündür. Veri Kataloğu API'si ile geliştiriciler, açıklamaları ve ek açıklamaları, David'in oluşturduğu ve bakımını yaptığı Excel Çalışma Kitabı gibi başka bir konumdan yükleyebilir.

Sonraki bölümde, Adventure Works şirketine ait bir örnek proje açıklanmaktadır.

### <a name="an-example-project"></a>Örnek proje

Bu örnekte, iş analisti olan Nancy, SQL Server veritabanındaki verileri kullanarak ekibi için raporlar oluşturmaktadır. Sorun, yalnızca birkaç iş arkadaşını konuşduktan sonra veri kaynağını haberdar edebiliyor. Kayıtlı olmaları durumunda bu veri kaynaklarını hızla bulmaları ve **Azure Veri Kataloğu**gibi merkezi bir konumda Açıklanmaları gerekir.

Nancy ve ekibinin çok değerli olan verileri ne kadar kolay bulabileceğini göstermek için, veri kaynağı kayıt aracını kullanarak Kataloğu veri kaynakları ile ilgili bilgilerle (meta veriler) doldurursunuz. Böylece, veritabanı ile ilgili bilgiler yalnızca birkaç kişi tarafından değil, ekip ve kuruluş tarafından kullanılabilir. Veri kaynakları Veri Kataloğu'na kaydedildikten sonra, Nancy ve ekibi bunları kolayca kullanabilir. Sonuç olarak ekibi için ve kuruluş için daha kapsamlı, ilgi düzeyi yüksek bir veri kataloğu elde edilir. Veri Kataloğu'nu benimseyen ekiplerin sayısı arttıkça işle ilgili veri kaynaklarının bulunması ve kullanılması kolaylaşır; böylece, verilerinizden daha fazla sonuç elde edilebilmesi için daha veri merkezli bir kültüre olanak tanınır.

Veri kaynağı kayıt aracı hakkında daha fazla bilgi için bkz. [Azure Veri Kataloğu ile çalışmaya başlama](data-catalog-get-started.md).

Nancy'nin ekibi ayrıca, pilot projenin bir parçası olarak, David ve iş arkadaşlarının bakımını yaptığı bir Excel çalışma kitabında açıklanan veri kaynaklarını kullanmaktadır. Kuruluştaki diğer ekipler de veri kaynaklarını açıklamak için Excel çalışma kitaplarını kullandığından, BT ekibi Excel çalışma kitabının Veri Kataloğu'na geçişini sağlamak üzere bir araç oluşturmaya karar verir. Pilot proje ekibi, var olan ek açıklamaları içeri aktarmak için Veri Kataloğu REST API'sini kullanarak, veri kaynağı kayıt aracını kullanma yoluyla veri kaynaklarından ayıklanan meta verilerden oluşan eksiksiz bir veri kataloğuna sahip olabilir ve bu katalog, el ile yeniden girdi olmaksızın veri üreticileri ve tüketicileri tarafından önceden belgelenmiş olan verileri eksiksiz olarak içerir. Kurumsal veri kataloğu büyüdükçe kuruluş, genel veri kaynakları için veri kaynağı kayıt aracını, özel kaynaklar ile genel olmayan senaryolar için de Veri Kataloğu API'sini kullanabilir.

> [!NOTE]
> Bir Excel çalışma kitabının Veri Kataloğu'na geçişini sağlamak için **Azure Veri Kataloğu** API'sini kullanan bir örnek araç geliştirdik. Veri Kataloğu API'si ve örnek araç hakkında bilgi edinmek için [Geçici çalışma kitabı kod örneğini indirin](https://github.com/Azure-Samples/data-catalog-dotnet-excel-register-data-assets) ve [Azure Veri Kataloğu REST API'si](/rest/api/datacatalog/) belgelerine başvurun.

Pilot proje kullanıma alındığında, Veri Kataloğu benimseme planınızı yürütmenin zamanı gelmiştir.

### <a name="execute"></a>Yürütme

Bu aşamada, Veri Kataloğu için kullanım durumlarını ve ilk projenizi tanımlamış durumdasınız. Bunun yanı sıra, önemli Adventure Works veri kaynaklarını kaydettiniz ve BT'nin geliştirdiği aracı kullanarak mevcut Excel çalışma kitabının içerdiği bilgileri eklediniz. Artık, Veri Kataloğu benimseme işlemini başlatmak için pilot ekiple çalışma zamanı geldi.

Aşağıda başlamanıza yardımcı olacak birkaç ipucu verilmiştir:

* **Heyecan yaratın** - İş kullanıcıları, **Azure Veri Kataloğu**'nun hayatlarını kolaylaştırdığına inanmaları hâlinde heyecan duyar. Teknolojiden ziyade, çözüm ve sağladığı avantajlar üzerine konuşmaya çalışın.
* **Değişimi kolaylaştırın** - Küçük başlayın ve planı iş kullanıcılarına iletin. Başarılı olmak için, kullanıcıların başlangıçtan itibaren sürece dahil edilmesi hayati önem taşır; böylece kullanıcılar sonucu etkiler ve çözüme ilişkin bir sahiplik duygusu geliştirir.
* **Erken benimseyenlere özenle yaklaşın** - Erken benimseyenler, yaptıkları işe tutkuyla bağlı olan ve **Azure Veri Kataloğu**'nun avantajlarını iş arkadaşlarına anlatmaktan heyecan duyan iş kullanıcılarıdır.
* **Eğitim hedefleri belirleyin** - İşletme kullanıcılarının Veri Kataloğu ile ilgili tüm bilgilere sahip olması gerekmez; bu nedenle belirli ekip hedeflerine ulaşmaya yönelik eğitim hedefleri belirleyin. Kullanıcıların **Azure Veri Kataloğu**'nu günlük yordamlarına dahil etmek için ne yapacağına ve bu süreçte bazı görevlerinin ne şekilde değişebileceğine odaklanın.
* **Başarısız olmaya ılımlı bakın** - Pilot proje istenen sonuçları elde etmezse projeyi yeniden değerlendirin ve değiştirilecek alanları tanımlayın; daha büyük bir kapsama geçmeden önce pilot projedeki sorunları düzeltin.

Pilot ekibiniz Veri Kataloğu'nu kullanmaya başlamadan önce, pilot projeye yönelik beklentileri görüşmek ve ilk eğitimi sağlamak için bir başlangıç toplantısı zamanlayın.

### <a name="set-expectations"></a>Beklentileri belirleme

Beklenti ve hedeflerin belirlenmesi, iş kullanıcılarının belirli sonuçlara odaklanmasına olanak tanır. Projenin planlandığı gibi ilerlemesi için düzenli (örneğin, pilot projenin kapsamına ve süresine bağlı olarak günlük veya haftalık) ev ödevleri atayın. Veri Kataloğu'nun en değerli özelliklerinden biri, veri varlıklarına yönelik kitle kaynak kullanımıdır; böylece iş kullanıcıları, kurumsal verilere ait bilgilerden faydalanabilir. Her bir pilot ekip üyesinin kullandığı veri kaynaklarından en az bir tanesini kaydetmesi veya buna açıklama eklemesi iyi bir ev ödevidir. Bkz. [Veri kaynağını kaydetme](data-catalog-get-started.md) ve [Veri kaynaklarına açıklama ekleme](data-catalog-get-started.md).

Bazı ek açıklamaları gözden geçirmek için ekiple düzenli aralıklarla toplanın. Veri kaynakları ile ilgili etkili ek açıklamalar, Veri Kataloğu'nun başarıyla benimsenmesinde temel rol oynar; bunun nedeni, söz konusu ek açıklamaların merkezi bir konumda anlamlı veri kaynağı öngörüleri sağlamasıdır. Etkili ek açıklamalar olmadan, veri kaynakları ile ilgili bilgiler kuruluş geneline dağılmış halde kalır. Bkz. [Veri kaynaklarına açıklama ekleme](data-catalog-get-started.md).

Projeyi test edecek nihai unsur, kullanıcıların kullanmaya ihtiyaç duydukları veri kaynaklarını bulma ve anlama başarısıdır. Pilot kullanıcılar, günlük işleri için kullandıkları veri kaynaklarının ilgili olduğundan emin olmak için kataloğu düzenli olarak test etmelidir. Gerekli bir veri kaynağı eksik olduğunda veya bu veri kaynağına düzgün olmayan bir şekilde açıklama eklendiğinde bu, ek veri kaynaklarının kaydedilmesine veya ilave ek açıklamaların sağlanmasına yönelik bir hatırlatma işlevi görecektir. Bu uygulama, pilot girişime değer kazandırmanın yanı sıra pilot projenin tamamlanmasının ardından diğer ekiplere devredilecek etkili alışkanlıkların oluşturulmasını sağlar.

### <a name="provide-training"></a>Eğitim verme

Eğitim, kullanıcılara başlangıç için gerekli bilgileri sağlayacak düzeyde olmalı ve pilot ekip üyelerinin ayrıntılı hedeflerine ve deneyim düzeyine uyarlanmalıdır. Eğitime başlamak için [Azure veri Kataloğu ile çalışmaya başlama](data-catalog-get-started.md) makalesindeki adımları uygulayabilirsiniz. Ayrıca [Azure Veri Kataloğu Pilot Proje Eğitimi sunumunu](https://github.com/Azure-Samples/data-catalog-dotnet-get-started/blob/master/Azure%20Data%20Catalog%20Training.pptx?raw=true) indirebilirsiniz. Bu PowerPoint sunumu, Veri Kataloğu'nu pilot ekip üyelerinize tanıtmaya başlamanıza yardımcı olacaktır.

## <a name="conclusion"></a>Sonuç

Pilot ekibinizin oldukça düzgün bir çalışma sağlamasının ve ilk hedeflerinize ulaşmanızın ardından, Veri Kataloğu'nun benimsenmesini daha fazla ekibe genişletmeniz gerekir. Pilot projenizden öğrendiklerinizi uygulayıp iyileştirerek Veri Kataloğu kullanımını kuruluşunuz genelinde genişletin.

Pilot projeye katılan erken benimseyenler, Veri Kataloğu'nu benimsemenin getirdiği avantajları duyurma konusunda yardımcı olabilir. Bu kişiler; Veri Kataloğu'nun, ekiplerinin iş sorunlarını çözmesine, veri kaynaklarını daha kolay bulmasına ve kullandığı veri kaynaklarına yönelik öngörüleri paylaşmasına nasıl yardımcı olduğunu diğer ekiplerle paylaşabilir. Örneğin, Adventure Works pilot ekibindeki erken benimseyenler, eskiden bulması ve anlaşılması zor olan Adventure Works veri varlıkları ile ilgili bilgileri bulmanın ne kadar kolay olduğunu başkalarına gösterebilir.

Bu makalede, kuruluşunuzda **Azure Veri Kataloğu** ile çalışmaya başlama konusu ele alınmıştır. Bir Veri Kataloğu pilot projesi başlatabildiğinizi ve Veri Kataloğu kullanımını kuruluşunuz genelinde genişletebildiğinizi umuyoruz.

## <a name="next-steps"></a>Sonraki adımlar

[Azure Veri Kataloğu oluşturma](data-catalog-get-started.md)
