---
title: Microsoft Azure güvenli uygulamalar tasarlama
description: Bu makalede, Web uygulaması projenizin gereksinim ve tasarım aşamaları sırasında göz önünde bulundurmanız gereken en iyi yöntemler açıklanmaktadır.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 316ed596cfa49987e229004c388267286ff50927
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000977"
---
# <a name="design-secure-applications-on-azure"></a>Azure 'da güvenli uygulamalar tasarlama
Bu makalede, bulut için uygulama tasarlarken göz önünde bulundurmanız gereken güvenlik etkinlikleri ve denetimler sunuyoruz. Microsoft [güvenlik geliştirme yaşam döngüsü (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) gereksinimleri ve tasarım aşamaları sırasında göz önünde bulundurmanız gereken güvenlik sorularıyla ve kavramların yanı sıra eğitim kaynakları da ele alınmıştır. Amaç, daha güvenli bir uygulama tasarlamak için kullanabileceğiniz etkinlikleri ve Azure hizmetlerini tanımlamanıza yardımcı olmaktır.

Aşağıdaki SDL aşamaları bu makalede ele alınmıştır:

- Eğitim
- Gereksinimler
- Tasarım

## <a name="training"></a>Eğitim
Bulut uygulamanızı geliştirmeye başlamadan önce Azure 'da güvenlik ve gizliliği anlamak için zaman alın. Bu adımı gerçekleştirerek, uygulamanızdaki açıktan yararlanmaya yönelik güvenlik açıklarının sayısını ve önem derecesini azaltabilirsiniz. Sürekli değişen tehdit yataya uygun şekilde tepki vermeye daha fazla hazırlanacağız.

Geliştiriciler için kullanılabilen Azure hizmetlerini ve Azure 'da en iyi güvenlik uygulamalarını öğrenmek için eğitim aşamasında aşağıdaki kaynakları kullanın:

  - [Azure 'a yönelik Geliştirici Kılavuzu](https://azure.microsoft.com/campaigns/developer-guide/) , Azure ile çalışmaya başlamanızı gösterir. Kılavuzda, uygulamalarınızı çalıştırmak, verilerinizi depolamak, zekası eklemek, IoT uygulamaları oluşturmak ve çözümlerinizi daha verimli ve güvenli bir şekilde dağıtmak için kullanabileceğiniz hizmetler gösterilmektedir.

  - [Azure geliştiricileri için Başlarken Kılavuzu](../../guides/developer/azure-developer-guide.md) , geliştirme Ihtiyaçları için Azure platformunu kullanmaya başlamak isteyen geliştiriciler için önemli bilgiler sağlar.

  - [SDK 'lar ve araçlar](https://docs.microsoft.com/azure/index#pivot=sdkstools) , Azure 'da kullanılabilen araçları açıklar.

  - [Azure DevOps Services](https://docs.microsoft.com/azure/devops/) , geliştirme işbirliği araçları sağlar. Araçlar arasında yüksek performanslı işlem hatları, ücretsiz Git depoları, yapılandırılabilir Kanban panoları ve kapsamlı otomatik ve bulut tabanlı yük testi vardır.
    [DevOps kaynak merkezi](https://docs.microsoft.com/azure/devops/learn/) , Learning DevOps uygulamaları, git sürüm denetimi, çevik Yöntemler, Microsoft 'Ta DevOps ile nasıl çalışacağız ve kendi DevOps ilerinizi nasıl değerlendirdiğiniz ile ilgili kaynaklarımızı birleştirir.

  - [Üretime gönderilmeden önce dikkate alınması gereken ilk 5 güvenlik öğesi](https://docs.microsoft.com/learn/modules/top-5-security-items-to-consider/index?WT.mc_id=Learn-Blog-tajanca) , Azure 'da Web uygulamalarınızın güvenliğini sağlama ve uygulamalarınızı en yaygın ve tehlikeli Web uygulaması saldırılarına karşı koruma konusunda size yardımcı olur.

  - [Azure Için güvenli DevOps seti](https://azsk.azurewebsites.net/index.html) , kapsamlı Otomasyon kullanan çok sayıda Azure aboneliğine ve kaynak güvenliği gereksinimlerine göre sunulan bir komut dosyası, araç, uzantı ve otomatikleştirmeler koleksiyonudur. Azure için güvenli DevOps seti, güvenliği yerel DevOps iş akışlarınızla sorunsuz bir şekilde tümleştirme hakkında sizi gösterebilir. Kit, güvenlik doğrulama testleri (SVTs) gibi araçları adresleyen, geliştiricilerin güvenli kod yazmasına ve kodlama ve erken geliştirme aşamalarında bulut uygulamalarının güvenli yapılandırmasını test etmenize yardımcı olan araçları ele alabilir.

  - [Azure çözümleri için En Iyi güvenlik uygulamaları](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions) , Azure kullanarak bulut çözümlerinizi tasarlarken, dağıtırken ve yönetirken kullanabileceğiniz en iyi güvenlik uygulamalarının bir koleksiyonunu sağlar.

## <a name="requirements"></a>Gereksinimler
Gereksinimler tanım aşaması, uygulamanızın ne olduğunu ve ne zaman kullanıma sunulduktan sonra ne yapacakınızın tanımlanmasıyla önemli bir adımdır. Gereksinimler aşaması, uygulamanızda oluşturabileceğiniz güvenlik denetimleri hakkında düşünmek için de bir zaman vardır. Bu aşamada, bir güvenli uygulamayı serbest bıraktığınızdan ve dağıttığınızdan emin olmak için SDL genelinde kullanacağınız adımları da başlatabilirsiniz.

### <a name="consider-security-and-privacy-issues"></a>Güvenlik ve gizlilik sorunlarını göz önünde bulundurun
Bu aşama, temel güvenlik ve gizlilik sorunlarını göz önünde bulundurmanız için en iyi seçenektir. Projenin başlangıcında kabul edilebilir güvenlik ve gizlilik düzeylerini tanımlama, takıma yardımcı olur:

- Güvenlik sorunlarıyla ilişkili riskleri anlayın.
- Geliştirme sırasında güvenlik hatalarını belirleyip onarın.
- Tüm proje genelinde belirlenen güvenlik ve gizlilik düzeylerini uygulayın.

Uygulamanız için gereksinimleri yazdığınızda, uygulamanızın ve verilerinizin güvende tutulmasına yardımcı olabilecek güvenlik denetimlerini göz önünde bulundurduğunuzdan emin olun.

### <a name="ask-security-questions"></a>Güvenlik sorularını sorma
Şunun gibi güvenlik soruları sorun:

  - Uygulamam gizli veriler içeriyor mu?

  - Uygulamam, [Federal Finans Kurumu Inceleme Council (FFIEC)](../blueprints/ffiec-analytics-overview.md) veya [ödeme kartı sektör verileri güvenlik standartları (PCI DSS) gibi endüstri standartlarına ve uyumluluk programlarına bağlı kalmalarını gerektiren verileri toplar veya depolar ](../blueprints/pcidss-analytics-overview.md)?

  - Uygulamam, tek bir kişiyi tanımlamak, iletişim kurmak veya bulmak için kendi başına veya diğer bilgilerle kullanılabilecek gizli kişisel veya müşteri verileri toplar ya da içeriyor mu?

  - Uygulamam, bir bireyin, eğitim, mali veya istihdam bilgilerine erişmek için kullanılabilecek verileri toplar veya içeriyor mu? Gereksinimler aşamasında verilerinizin duyarlılığını belirlemek verilerinizi sınıflandırmanıza ve uygulamanız için kullanacağınız veri koruma yöntemini belirlemenize yardımcı olur.

  - Veri nerede depolanıyor? Uygulamanızın beklenmeyen değişiklikler (daha yavaş yanıt süreleri gibi) için kullandığı depolama hizmetlerini nasıl izleyecağınızı düşünün. Daha ayrıntılı veriler toplamak ve bir sorunu derinlemesine çözümlemek için günlüğe kaydetmeyi etkileyebilir misiniz?

  - Uygulamam herkese açık (Internet 'te) veya yalnızca dahili olarak kullanılabilir olacak mı? Uygulamanız herkese açık ise, toplanabilecek verilerin yanlış bir şekilde kullanılması için nasıl korunursunuz? Uygulamanız yalnızca dahili olarak kullanılabiliyorsa, kuruluşunuzun uygulamaya erişimi olması gerektiğini ve ne kadar süreyle erişimi olması gerektiğini göz önünde bulundurun.

  - Uygulamanızı tasarlamaya başlamadan önce kimlik modelinizi anladım mı? Kullanıcılara söyledikleri kim olduğunu ve bir kullanıcının ne işe yetkili olduğunu nasıl belirleyebilirim?

  - Uygulamam hassas veya önemli görevler (para aktarımı, kapıların kilidini açma veya ilaç sunma gibi) gerçekleştirsin mi?
    Gizli bir görevi gerçekleştiren kullanıcının görevi gerçekleştirme yetkisine sahip olduğunu ve kişilerin söyledikleri kim olduğunu nasıl doğrulayacağınızı düşünün. Yetkilendirme (AuthZ), kimliği doğrulanmış bir güvenlik sorumlusu iznini bir şey yapmak için verme işlemidir. Kimlik doğrulaması (AuthN), meşru kimlik bilgileri için bir tarafın zorlayıcı bir şekilde davranma işlemidir.

  - Uygulamam, kullanıcıların dosya veya diğer verileri karşıya yüklemesine veya indirmesine izin vermek gibi riskli yazılım etkinliklerini gerçekleştirsin mi? Uygulamanız riskli etkinlikler gerçekleştirmezse, uygulamanızın kullanıcıların kötü amaçlı dosyaları veya verileri işlemesini nasıl koruyacağınızı düşünün.

### <a name="review-owasp-top-10"></a>OWASP ilk 10 ' a bakın
[<span class="underline">OWASP en iyi 10 uygulama güvenliği risklerini</span>](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)gözden geçirmeyi düşünün.
OWASP En Iyi 10 Web uygulamalarına yönelik kritik güvenlik risklerini gidermektedir.
Bu güvenlik risklerinin farkında, uygulamanızda bu riskleri en aza indirecek gereksinim ve tasarım kararları almanıza yardımcı olabilir.

İhlallerinin önemli olduğunu engellemek için güvenlik denetimleri hakkında düşünce vardır.
Ancak, [bir ihlalin meydana gelir olduğunu varsayalım](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/security-in-devops) . İhlalin, güvenlikle ilgili bazı önemli soruların yanıtlanmasına yardımcı olur ve bu nedenle bir acil durum ile yanıtlanması gerekmez:

  - Bir saldırıyı nasıl algılayabilirim?

  - Bir saldırı veya ihlal olursa ne yapmam gerekir?

  - Veri sızması veya izinsiz oynama gibi saldırılardan nasıl kurtarılacağını nasıl önleyeceğiz?

## <a name="design"></a>Tasarım

Tasarım aşaması, tasarım ve işlevsel belirtimlerde en iyi uygulamalar oluşturmak için önemlidir. Ayrıca, bir proje genelinde güvenlik ve gizlilik sorunlarını azaltmaya yardımcı olan risk analizini gerçekleştirmek için de önemlidir.

Güvenlik gereksinimlerinizi karşıladığınızda ve güvenli tasarım kavramlarını kullandığınızda, bir güvenlik hatası için fırsatları önveya en aza indirmenize neden olabilirsiniz. Bir güvenlik kusuru, uygulama yayımlandıktan sonra bir kullanıcının kötü amaçlı veya beklenmedik eylemler gerçekleştirmesine izin veren uygulamanın tasarımında gözedir.

Tasarım aşaması sırasında, katmanlara nasıl güvenlik uygulayacağınızı da düşünün; tek bir savunma düzeyi yeterince gerekli değildir. Bir saldırgan Web uygulaması güvenlik duvarını (WAF) geçiyorsa ne olur? Bu saldırıya karşı savunmak için başka bir güvenlik denetimi istiyorsunuz.

Bu göz önüne alarak, güvenli uygulamalar tasarlarken aşağıdaki güvenli tasarım kavramlarını ve adresiniz olması gereken güvenlik denetimlerini tartıştık:

- Güvenli kodlama kitaplığı ve yazılım çatısı kullanın.
- Güvenlik açığı bulunan bileşenleri tarayın.
- Uygulama tasarımı sırasında tehdit modellemesini kullanın.
- Saldırı yüzeyinizi azaltın.
- Birincil güvenlik çevre birimi olarak bir kimlik ilkesi benimseyin.
- Önemli işlemler için yeniden kimlik doğrulaması gerektir.
- Anahtarlar, kimlik bilgileri ve diğer gizli dizileri güvenli hale getirmek için bir anahtar yönetimi çözümü kullanın.
- Hassas verileri koruyun.
- Başarısız-güvenli ölçüleri uygulayın.
- Hata ve özel durum işlemenin avantajlarından yararlanın.
- Günlüğe kaydetme ve uyarı kullanma.

### <a name="use-a-secure-coding-library-and-a-software-framework"></a>Güvenli kodlama kitaplığı ve yazılım çerçevesi kullanma

Geliştirme için güvenli bir kodlama kitaplığı ve ekli güvenlik içeren bir yazılım çerçevesi kullanın. Geliştiriciler mevcut, kanıtlanmış özellikleri (şifreleme, giriş Sanitation, çıkış kodlaması, anahtarlar veya bağlantı dizeleri ve güvenlik denetimi olarak kabul edilecek diğer her şeyi), sıfırdan güvenlik denetimleri geliştirmek yerine kullanabilirler. Bu, güvenlikle ilgili tasarım ve uygulama kusurlarına karşı koruma sağlamaya yardımcı olur.

Framework 'ün en son sürümünü ve çerçevede kullanılabilen tüm güvenlik özelliklerini kullandığınızdan emin olun. Microsoft, tüm geliştiriciler için bulut uygulamaları sunmak üzere tüm platformlarda veya dillerde çalışan kapsamlı bir [geliştirme araçları kümesi](https://azure.microsoft.com/product-categories/developer-tools/) sunar. Çeşitli [SDK](https://azure.microsoft.com/downloads/)'lardan seçim yaparak seçtiğiniz dille birlikte kod oluşturabilirsiniz.
Gelişmiş hata ayıklama özelliklerine ve yerleşik Azure desteğine sahip olan, tam özellikli tümleşik geliştirme ortamları (IDE) ve düzenleyicilerden faydalanabilirsiniz.

Microsoft, Azure 'da uygulama geliştirmek için kullanabileceğiniz çeşitli [Diller, çerçeveler ve araçlar](https://docs.microsoft.com/azure/index#pivot=sdkstools&panel=sdkstools-all) sunar. [.Net ve .NET Core geliştiricileri Için Azure](https://docs.microsoft.com/dotnet/azure/)bir örnektir. Sunduğumuz her dil ve çerçeve için hızlı başlangıç, öğreticiler ve API başvurularını hızla kullanmaya başlamanıza yardımcı olacak şekilde bulacaksınız.

Azure, Web sitelerini ve Web uygulamalarını barındırmak için kullanabileceğiniz çeşitli hizmetler sunar. Bu hizmetler, .NET, .NET Core, Java, Ruby, Node. js, PHP veya Python gibi en sevdiğiniz dilde geliştirme yapmanızı sağlar.
[Azure App Service Web Apps](../../app-service/overview.md) (Web Apps), bu hizmetlerden biridir.

Web Apps Microsoft Azure gücünü uygulamanıza ekler. Güvenlik, Yük Dengeleme, otomatik ölçeklendirme ve otomatik yönetimi içerir. Ayrıca, paket yönetimi, hazırlama ortamları, özel etki alanları, SSL/TLS sertifikaları ve Azure DevOps, GitHub, Docker Hub 'dan ve diğer kaynaklardan sürekli dağıtım gibi Web Apps DevOps özelliğinden faydalanabilirsiniz.

Azure, Web sitelerini ve Web uygulamalarını barındırmak için kullanabileceğiniz diğer hizmetler sunar. Çoğu senaryo için Web Apps en iyi seçenektir. Mikro hizmet mimarisi için [Azure Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric)göz önünde bulundurun.
Kodlarınızın çalıştığı sanal makineler üzerinde daha fazla denetime sahip olmanız gerekiyorsa [Azure Sanal Makineler](https://azure.microsoft.com/documentation/services/virtual-machines/)’i düşünün.
Bu Azure hizmetleri arasından nasıl seçim yapılacağı hakkında daha fazla bilgi için bkz. [Azure App Service, sanal makineler, Service Fabric ve Cloud Services karşılaştırması](/azure/architecture/guide/technology-choices/compute-decision-tree).

### <a name="apply-updates-to-components"></a>Güncelleştirmeleri bileşenlere Uygula

Güvenlik açıklarını engellemek için hem istemci tarafı hem de sunucu tarafı bileşenlerinizi (örneğin, çerçeveler ve kitaplıklar) ve bunların güncelleştirme bağımlılıklarını sürekli olarak envantere almalısınız. Yeni Güvenlik Açıkları ve güncelleştirilmiş yazılım sürümleri sürekli olarak serbest bırakılır. Kullandığınız kitaplıklarda ve bileşenlerde güncelleştirmeleri veya yapılandırma değişikliklerini izlemek, önceliklendirme ve uygulamak için devam eden bir planınız olduğundan emin olun.

Araç önerileri için [bilinen güvenlik açıklarını içeren bileşenleri kullanma](https://www.owasp.org/index.php/Top_10-2017_A9-Using_Components_with_Known_Vulnerabilities) sayfasındaki [Web uygulaması güvenlik projesi (OWASP) sayfasını açın](https://www.owasp.org/index.php/Main_Page) sayfasına bakın. Ayrıca, kullandığınız bileşenlerle ilgili güvenlik açıkları için e-posta uyarılarına abone olabilirsiniz.

### <a name="use-threat-modeling-during-application-design"></a>Uygulama tasarımı sırasında tehdit modellemesini kullanma

Tehdit modellemesi, işletmenizin ve uygulamanızın olası güvenlik tehditlerini tanımlama ve daha sonra uygun azaltmaları sağlama işlemidir. SDL, ekiplerin tasarım aşamasında tehdit modellemesinde, olası sorunları çözümlemede oldukça kolay ve ekonomik bir şekilde etkili olması gerektiğini belirtir. Tehdit modellemesinin tasarım aşamasında kullanılması, toplam geliştirme maliyetinizi büyük ölçüde azaltabilir.

Tehdit modelleme sürecini kolaylaştırmaya yardımcı olmak için, [SDL Threat Modeling Tool](threat-modeling-tool.md) , güvenlikle ilgili olmayan uzmanlar göz önünde bulundurularak tasarlandık. Bu araç, tehdit modellerini oluşturma ve çözümleme hakkında açık yönergeler sunarak tüm geliştiriciler için tehdit modellemesini kolaylaştırır.

Uygulama tasarımını modelleme ve [ilerleme](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) tehditleri numaralandırma — sızdırma, izinsiz, ret, bilgilerin açıklanması, hizmet reddi ve ayrıcalık yükselmesi — tüm güven sınırları genelinde tasarım hatalarını yakalamak için etkili bir yol kanıtlandı erken açık. Aşağıdaki tabloda, Ilerleme tehditleri listelenmekte ve Azure tarafından sunulan özellikleri kullanan bazı örnek azaltmaları sunulmaktadır. Bu azaltmaları her durumda çalışmaz.

| Tehdit | Güvenlik özelliği | Olası Azure platformu risk azaltma |
| ---------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Sızdır               | Authentication        | [HTTPS bağlantıları gerektir](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl?view=aspnetcore-2.1&tabs=visual-studio). |
| Kurcalama              | Bütünlük             | SSL/TLS sertifikalarını doğrulayın. SSL/TLS kullanan uygulamaların, bağlandıkları varlıkların X. 509.440 sertifikalarını tam olarak doğrulaması gerekir. [X509 sertifikalarınızı yönetmek](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-certificates)için Azure Key Vault sertifikaları kullanın. |
| Kar            | Red olmayan       | Azure [izleme ve tanılama 'yı](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)etkinleştirin.|
| Bilgilerin Açığa Çıkması | Gizlilik       | REST ve iletim [sırasında](../fundamentals/encryption-atrest.md) hassas verileri [](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit)şifreleyin. |
| Hizmet Reddi      | Kullanılabilirlik          | Olası hizmet reddi koşulları için performans ölçümlerini izleyin. Bağlantı filtrelerini uygulayın. [Azure DDoS koruması](../../virtual-network/ddos-protection-overview.md#next-steps), uygulama tasarımı en iyi uygulamaları ile birlikte, DDoS saldırılarına karşı savunma sağlar.|
| Ayrıcalık Yükseltme | Authorization         | Azure Active Directory <span class="underline"></span> [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)kullanın.|

### <a name="reduce-your-attack-surface"></a>Saldırı yüzeyinizi azaltın

Saldırı yüzeyi, olası güvenlik açıklarının gerçekleşebileceği toplam toplamıdır. Bu yazıda, uygulamanın saldırı yüzeyine odaklanıyoruz.
Odak, bir uygulamanın saldırıya karşı korunmasıyla gerçekleştirilir. Saldırı yüzeyini en aza indirmenin basit ve hızlı bir yolu, kullanılmayan kaynakları ve kodu uygulamanızdan kaldırmanız. Uygulamanız ne kadar küçükse, saldırı yüzeyiniz daha küçüktür. Örneğin, şunları kaldırın:

- Henüz kullanıma sunulmadığınız özellikler için kod.
- Destek kodunda hata ayıklama.
- Kullanılmayan veya kullanım dışı bırakılmış olan ağ arabirimleri ve protokolleri.
- Kullanmadığınız sanal makineler ve diğer kaynaklar.

Kaynaklarınızın düzenli olarak temizlenmesi ve kullanılmayan kodu kaldırmanızı sağlamak, kötü amaçlı aktörlerin saldırıya karşı daha az fırsat olduğundan emin olmanın harika yollarıdır.

Saldırı yüzeyini azaltmanın daha ayrıntılı ve ayrıntılı bir yolu, saldırı yüzeyi analizinin tamamlanmamaktır. Saldırı yüzeyi analizi, sistemin güvenlik açıklarına karşı incelenmesi ve sınanması gereken bölümlerini eşlemenizi sağlar.

Saldırı yüzeyi analizinin amacı, bir uygulamadaki risk alanları öğrenmektir, böylece geliştiriciler ve güvenlik uzmanlarının uygulamanın hangi bölümlerinin saldırıya açık olduğunu fark eder. Daha sonra, bu olasılığı en aza indirmenin yollarını bulabilirsiniz, saldırı yüzeyinin ne zaman ve nasıl değiştiği ve bunun ne anlama geldiğini takip edebilirsiniz.

Bir saldırı yüzeyi Analizi şunları belirlemenize yardımcı olur:

- Güvenlik açıklarını gözden geçirmeniz ve test etmeniz için gereken sistem işlevleri ve bölümleri.
- Derinlemesine savunma koruması gerektiren kodun yüksek riskli alanı (savunmanız gereken sistemin parçaları).
- Saldırı yüzeyini değiştirdiğinizde ve bir tehdit değerlendirmesini yenilemeniz gerektiğinde.

Saldırganların olası zayıf bir nokta veya güvenlik açığıyla yararlanmaya yönelik fırsatları azaltmak, uygulamanızın genel saldırı yüzeyini kapsamlı bir şekilde analiz etmeniz gerekir. Ayrıca, sistem hizmetlerine erişimi devre dışı bırakma veya kısıtlama, en az ayrıcalık ilkesini uygulama ve mümkün olan yerlerde katmanlı savunmalar kullanımını içerir.

SDL 'nin doğrulama aşamasında [saldırı yüzeyi incelemesi](secure-develop.md#conduct-attack-surface-review) yaparken tartışıyoruz.

> [!NOTE]
> **Tehdit modelleme ve saldırı yüzeyi Analizi arasındaki fark nedir?**
Tehdit modellemesi, uygulamanıza yönelik olası güvenlik tehditlerini belirleme ve tehditlere karşı doğru azaltmaları sağlama işlemidir. Saldırı yüzeyi analizi, saldırının açık olduğu yüksek riskli kod alanları tanımlar. Bu, uygulamanızın yüksek riskli bölgelerini savunmanın yollarını bulmayı ve uygulamayı dağıtmadan önce Bu kod bölümlerini gözden geçirmeyi ve test etmeyi içerir.

### <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Birincil güvenlik çevresi olarak kimlik ilkesini benimseyin

Bulut uygulamaları tasarladığınızda, ağ merkezli bir yaklaşımdan kimlik merkezli bir yaklaşıma yönelik güvenlik çevre odağınızı genişletmeniz önemlidir. Geçmişte, birincil şirket içi güvenlik çevresi bir kuruluşun ağı idi. Çoğu şirket içi güvenlik tasarımı, ağı birincil güvenlik özeti olarak kullanır. Bulut uygulamaları için, kimliği birincil güvenlik çevre birimi olarak ele alarak daha iyi bir şekilde sunulur.

Web uygulamaları geliştirmeye yönelik kimlik merkezli bir yaklaşım geliştirmek için yapabilecekleriniz:

- Kullanıcılar için Multi-Factor Authentication 'ı zorlayın.
- Güçlü kimlik doğrulama ve yetkilendirme platformları kullanın.
- En az ayrıcalık ilkesini uygulayın.
- Tam zamanında erişim uygulayın.

#### <a name="enforce-multi-factor-authentication-for-users"></a>Kullanıcılar için Multi-Factor Authentication 'ı zorlama

İki öğeli kimlik doğrulaması kullanın. İki öğeli kimlik doğrulama, kimlik doğrulama ve yetkilendirme için geçerli standarttır çünkü Kullanıcı adı ve parola türleri ile ilgili güvenlik zayıflarını önler. Azure Yönetim arabirimlerine (Azure portal/uzak PowerShell) ve müşteriye yönelik hizmetlere erişim, [azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md)kullanacak şekilde tasarlanmalı ve yapılandırılmalıdır.

#### <a name="use-strong-authentication-and-authorization-platforms"></a>Güçlü kimlik doğrulama ve yetkilendirme platformları kullanın

Özel kod yerine platform tarafından sağlanan kimlik doğrulama ve yetkilendirme mekanizmalarını kullanın. Bunun nedeni, özel kimlik doğrulama kodunun geliştirmesinin hataya yol açabilir. Ticari kod (örneğin, Microsoft 'tan) genellikle güvenlik için kapsamlı olarak gözden geçirilir. [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) , kimlik ve erişim yönetimi için Azure çözümüdür. Bu Azure AD araçları ve Hizmetleri, güvenli geliştirmeyle ilgili olarak size yardımcı olur:

- [Azure AD kimlik platformu (geliştiriciler Için Azure AD)](../../active-directory/develop/about-microsoft-identity-platform.md) , geliştiricilerin kullanıcılara güvenli bir şekilde oturum açtırmış uygulamalar oluşturmak için kullandığı bir bulut kimlik hizmetidir. Azure AD, çok kiracılı uygulamalar geliştirmek isteyen tek kiracılı, iş kolu (LOB) uygulamaları ve geliştiriciler oluşturan geliştiricilere yardımcı olur. Temel oturum açma 'ya ek olarak, Azure AD kullanılarak oluşturulan uygulamalar, Azure AD platformunda oluşturulmuş Microsoft API 'Lerini ve özel API 'Leri çağırabilir. Azure AD kimlik platformu, OAuth 2,0 ve OpenID Connect gibi sektör standardı protokolleri destekler.

- [Azure Active Directory B2C (Azure AD B2C)](../../active-directory-b2c/index.yml) , uygulamalarınızı kullandıklarında müşterilerin nasıl kaydolup oturum açmasını ve profillerini nasıl yönetebileceğinizi denetlemek için kullanabileceğiniz bir kimlik yönetimi hizmetidir. Bu, diğer kullanıcıların yanı sıra iOS, Android ve .NET için geliştirilen uygulamaları içerir. Azure AD B2C, müşteri kimliklerini korurken bu eylemlere izin vermez.

#### <a name="apply-the-principle-of-least-privilege"></a>En az ayrıcalık ilkesini uygulayın

[En az ayrıcalık](https://en.wikipedia.org/wiki/Principle_of_least_privilege) kavramı, kullanıcılara, işlerini yapmak için ihtiyaç duydukları ve başka hiçbir şeyin daha fazla erişim ve denetim yapma anlamına gelir.

Yazılım geliştiricisi etki alanı yöneticisi haklarına ihtiyaç duyuyor mu? Yönetim Yardımcısı 'nın kişisel bilgisayarlarındaki yönetim denetimlerine erişmesi gerekiyor mu? Yazılıma erişimin değerlendirilmesi farklı değildir. Kullanıcılara uygulamanızda farklı yetenekler ve yetki vermek için [rol tabanlı erişim denetimi (RBAC)](../../role-based-access-control/overview.md) kullanıyorsanız herkese her şeye erişim vermeyiz. Her rol için gerekli olan erişimi sınırlayarak, oluşan bir güvenlik sorunu riskini sınırlandırırsınız.

Uygulamanızın erişim desenlerinin tamamında [en az ayrıcalığı](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models#in-applications) zorladığından emin olun.

> [!NOTE]
>  En az ayrıcalık kurallarının yazılıma ve yazılımı oluşturan kişilere uygulanması gerekir. Yazılım geliştiricileri, çok fazla erişim verilirse BT güvenliği için büyük bir risk oluşturabilir. Bir geliştirici için kötü amaçlı bir amaç varsa veya çok fazla erişim verilirse sonuçlar ciddi bir şekilde olabilir. En az ayrıcalık kurallarının geliştirme yaşam döngüsü boyunca geliştiricilere uygulanmasını öneririz.

#### <a name="implement-just-in-time-access"></a>Tam zamanında erişim uygulama

Ayrıcalıkların etkilenme süresini daha da azaltmak için *tam zamanında* (JIT) erişimi uygulayın. [Azure AD Privileged Identity Management](../../active-directory/users-groups-roles/directory-admin-roles-secure.md#stage-3-build-visibility-and-take-full-control-of-admin-activity) için kullanın:

- Kullanıcılara yalnızca JıT için gereken izinleri verin.
- Ayrıcalıkların otomatik olarak iptal edildiğinden emin olmak için, kısa süreli bir süre için roller atayın.

### <a name="require-re-authentication-for-important-transactions"></a>Önemli işlemler için yeniden kimlik doğrulaması gerektir

[Siteler arası istek sahteciliği](https://docs.microsoft.com/aspnet/core/security/anti-request-forgery?view=aspnetcore-2.1) ( *XSRF* veya *CSRF*olarak da bilinir), kötü amaçlı bir Web uygulamasının, bir istemci tarayıcısı ile bu tarayıcıya güvenen bir Web uygulaması arasındaki etkileşimi etkilediği Web 'de barındırılan uygulamalara karşı bir saldırıya karşı bir saldırıdır. Web tarayıcıları her bir Web sitesi isteğiyle otomatik olarak bazı tür kimlik doğrulama belirteçleri göndertiğinden, siteler arası istek sahteciliği saldırıları mümkündür.
Bu kullanım biçimi, *tek tıklamayla saldırı* veya *oturum* , saldırı Kullanıcı tarafından daha önce kimliği doğrulanan oturumdan yararlandığından, bir tıklama olarak da bilinir.

Bu tür bir saldırıya karşı savunmanız gereken en iyi yol, kullanıcıdan satın alma, hesap devre dışı bırakma veya parola değişikliği gibi her önemli işlemden önce yalnızca kullanıcının sağlayabileceği bir şeyi ister. Kullanıcıdan parolasını yeniden girmesi, CAPTCHA 'yı tamamlaması veya yalnızca kullanıcının sahip olacağı bir gizli belirteç gönderebilmesini isteyebilirsiniz. En yaygın yaklaşım, gizli belirteçtir.

### <a name="use-a-key-management-solution-to-secure-keys-credentials-and-other-secrets"></a>Anahtarlar, kimlik bilgileri ve diğer gizli dizileri güvenli hale getirmek için anahtar yönetimi çözümü kullanma

Anahtar ve kimlik bilgilerinin kaybolması yaygın bir sorundur. Anahtarlarınızı ve kimlik bilgilerinizi kaybetmekten daha kötü bir şey, yetkisiz bir tarafın bunlara erişmesini sağlayabilir. Saldırganlar, GitHub gibi kod depolarında depolanan anahtarları ve gizli dizileri bulmak için otomatik ve el ile tekniklerin avantajlarından yararlanabilir. Bu genel kod depolarında veya diğer bir sunucuda anahtar ve gizli dizileri yerleştirmeyin.

Anahtar yönetimi çözümüne her zaman anahtarlarınızı, sertifikalarınızı, sırları ve bağlantı dizilerinizi koyun. Anahtar ve parolaların donanım güvenlik modüllerinde (HSM 'ler) depolandığı merkezi bir çözüm kullanabilirsiniz. Azure, [Azure Key Vault](../../key-vault/key-vault-overview.md)ile BULUTTA bir HSM sağlar.

*Gizli bir depo*olan Key Vault: uygulama gizli dizileri depolamak için merkezi bir bulut hizmetidir. Key Vault, uygulama gizli dizilerini tek bir merkezi konumda tutarak ve güvenli erişim, izin denetimi ve erişim günlüğü sağlayarak gizli verilerinizi güvende tutar.

Gizli *dizileri tek tek*kasaların içinde depolanır. Her kasanın, erişimi denetlemek için kendi yapılandırması ve güvenlik ilkeleri vardır. Verilerinize bir REST API veya birçok programlama dili için kullanılabilen bir istemci SDK 'Sı üzerinden ulaşabilirsiniz.

> [!IMPORTANT]
> Azure Key Vault, sunucu uygulamaları için yapılandırma gizli dizilerini depolamak üzere tasarlanmıştır. Uygulama kullanıcılarına ait olan verileri depolamak için tasarlanmamıştır. Bu, performans özelliklerine, API 'ye ve maliyet modeline yansıtılır.
>
> Kullanıcı verileri, Saydam Veri Şifrelemesi (TDE) veya Azure Depolama Hizmeti Şifrelemesi kullanan bir depolama hesabında bulunan bir Azure SQL veritabanı örneğinde olduğu gibi başka bir yerde depolanmalıdır. Uygulamanız tarafından bu veri depolarına erişmek için kullanılan gizli dizileri, Azure Key Vault tutulabilir.

### <a name="protect-sensitive-data"></a>Hassas verileri koruma

Verilerin korunması, güvenlik stratejinizin önemli bir parçasıdır.
Verilerinizi sınıflandırın ve veri koruma gereksinimlerinizi tanımlayarak uygulamanızı veri güvenliği göz önünde bulundurarak tasarlamanıza yardımcı olur. Depolanan verileri duyarlılık ve iş etkisine göre sınıflandırma (kategorilere ayırma), geliştiricilere verilerle ilişkili riskleri belirlemesine yardımcı olur.

Veri biçimlerinizi tasarlarken tüm uygulanabilir verileri hassas olarak etiketleyin. Uygulamanın ilgili verileri hassas olarak değerlendirdiğinden emin olun. Bu uygulamalar, hassas verilerinizi korumanıza yardımcı olabilir:

- Şifrelemeyi kullanın.
- Anahtarlar ve parolalar gibi sabit kodlardaki gizli dizileri kullanmaktan kaçının.
- Erişim denetimleri ve denetiminin yerinde olduğundan emin olun.

#### <a name="use-encryption"></a>Şifreleme kullan

Verilerin korunması, güvenlik stratejinizin önemli bir parçası olmalıdır.
Verileriniz bir veritabanında depolanıyorsa veya konumlar arasında geri ve ileri taşınırsa, [bekleyen](../fundamentals/encryption-atrest.md) verilerin şifrelemesini (veritabanında iken) ve [Aktarım sırasında verilerin](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit) şifrelemesini (Kullanıcı, veritabanı, API veya hizmet uç noktası) kullanın. Verileri Exchange için her zaman SSL/TLS protokollerini kullanmanızı öneririz. Şifreleme için TLS 'nin en son sürümünü kullandığınızdan emin olun (Şu anda bu sürüm 1,2 ' dir).

#### <a name="avoid-hard-coding"></a>Sabit kodlamadan kaçının

Bazı şeyler, yazılımınız için asla sabit olarak kodlanmamalıdır. Bazı örnekler ana bilgisayar adları, IP adresleri, URL 'Ler, e-posta adresleri, Kullanıcı adları, parolalar, depolama hesabı anahtarları ve diğer şifreleme anahtarlarıdır. Kodunuzun açıklama bölümlerine dahil olmak üzere kodunuzda sabit kodlanması gereken veya zor olabilecek gereksinimleri uygulamayı düşünün.

Kodunuzda açıklama yerleştirdiğinizde, hassas bilgileri saklamayın olduğunuzdan emin olun. Bu, e-posta adresinizi, parolalarınızı, bağlantı dizelerinizi, uygulamanız hakkındaki bilgileri, yalnızca kuruluşunuzdaki bir kişi tarafından bilinerek ve bir saldırganın uygulama veya kuruluşunuza saldırmasını engelleyen bir avantaj sunabildiğinde başka her şeyi içerir. .

Temel olarak, geliştirme projenizdeki her şeyin dağıtıldığında genel bilgi olacağını varsayalım. Projedeki herhangi bir türden hassas verileri dahil kullanmaktan kaçının.

Daha önce [Azure Key Vault](../../key-vault/key-vault-overview.md)tartışıyoruz. Key Vault kullanarak anahtarlar ve parolalar gibi gizli dizileri, onları sabit kodlamak yerine depolayın. Azure kaynakları için yönetilen kimliklerle birlikte Key Vault kullandığınızda Azure Web uygulamanız, kaynak denetiimde veya yapılandırmanızda gizli dizileri depolamadan kolayca ve güvenli bir şekilde gizli dizi yapılandırma değerlerine erişebilir. Daha fazla bilgi edinmek için bkz. [Azure Key Vault ile sunucu uygulamalarınızda gizli dizileri yönetme](https://docs.microsoft.com/learn/modules/manage-secrets-with-azure-key-vault/).

### <a name="implement-fail-safe-measures"></a>Başarısız-güvenli ölçüleri uygulama

Uygulamanız, yürütme sırasında oluşan [hataları](https://docs.microsoft.com/dotnet/standard/exceptions/) tutarlı bir şekilde işleyebilmelidir. Uygulamanın tüm hataları yakalayıp güvenli veya kapalı olması gerekir.

Ayrıca, şüpheli veya kötü amaçlı etkinlikleri belirlemek için hataların yeterli Kullanıcı bağlamına kaydedildiğinden emin olmanız gerekir. Gecikik analizine izin vermek için günlüklerin yeterli bir süre korunması gerekir. Günlükler, bir günlük yönetimi çözümü tarafından kolayca tüketilen bir biçimde olmalıdır. Güvenlikle ilgili hatalara yönelik uyarıların tetiklendiğinden emin olun. Yetersiz günlük kaydı ve izleme, saldırganların sistemleri daha fazla saldırı ve kalıcılık sağlama olanağı sağlar.

### <a name="take-advantage-of-error-and-exception-handling"></a>Hata ve özel durum işlemenin avantajlarından yararlanın

Doğru hata ve [özel durum işleme](https://docs.microsoft.com/dotnet/standard/exceptions/best-practices-for-exceptions) uygulamak, savunma kodlamasının önemli bir parçasıdır. Bir sistem güvenilir ve güvenli hale getirmek için hata ve özel durum işleme kritik öneme sahiptir. Hata işlemede hatalar, saldırganlar için bilgi sızıntısı ve saldırganların platformunuz ve tasarımı hakkında daha fazla bilgi anlamasına yardımcı olmak gibi farklı türlerde güvenlik açıklarına yol açabilir.

Emin olun:

- Koddaki yinelenen [try/catch bloklarını](https://docs.microsoft.com/dotnet/standard/exceptions/how-to-use-the-try-catch-block-to-catch-exceptions) önlemek için özel durumları merkezi bir şekilde işlemiş olursunuz.

- Tüm beklenmeyen davranışlar uygulama içinde işlenir.

- Kullanıcılara görüntülenen iletiler kritik verileri sızıntısına karşın sorunu açıklamak için yeterli bilgi sağlar.

- Özel durumlar günlüğe kaydedilir ve araştırılması için gerekli olan bir veya daha fazla bilgi sağlar.

[Azure Logic Apps](../../logic-apps/logic-apps-overview.md) , bağımlı sistemlerden kaynaklanan [hata ve özel durumları işlemeye](../../logic-apps/logic-apps-exception-handling.md) yönelik birinci sınıf bir deneyim sağlar. Kuruluşlar ve kuruluşlar arasında uygulamaları, verileri, sistemleri ve Hizmetleri tümleştiren görevleri ve işlemleri otomatikleştirmek için iş akışları oluşturmak üzere Logic Apps kullanabilirsiniz.

### <a name="use-logging-and-alerting"></a>Günlüğe kaydetme ve uyarı kullanma

Güvenlik araştırmalarınızın güvenlik sorunlarını [günlüğe kaydedin](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1) ve kişilerin sorunları zamanında bilmesini sağlamak için sorunlar hakkında uyarı tetikleyin. Tüm bileşenlerde denetim ve günlüğe kaydetmeyi etkinleştirin. Denetim günlükleri kullanıcı bağlamını yakalayıp tüm önemli olayları tanımlamalıdır.

Kullanıcının sitenize gönderdiği gizli verileri günlüğe kaydetme izniniz olup olmadığını denetleyin. Hassas verilere örnek olarak şunlar verilebilir:

- Kullanıcı kimlik bilgileri
- Sosyal güvenlik numaraları veya diğer tanımlama bilgileri
- Kredi kartı numaraları veya diğer finansal bilgiler
- Sağlık bilgileri
- Şifrelenmiş bilgilerin şifresini çözmek için kullanılabilen özel anahtarlar veya diğer veriler
- Uygulamaya daha etkin bir şekilde saldırmak için kullanılabilen sistem veya uygulama bilgileri

Uygulamanın başarılı ve başarısız Kullanıcı oturum açmaları, parola sıfırlama, parola değişiklikleri, hesap kilitleme ve Kullanıcı kaydı gibi Kullanıcı yönetimi olaylarını izlemediğinden emin olun. Bu olayların günlüğü, potansiyel olarak şüpheli davranışı saptamanıza ve bunlara yanıt almanıza yardımcı olur. Ayrıca, uygulamaya erişen işlemler gibi işlem verilerini toplamanıza olanak tanır.

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makalelerde, güvenli uygulamalar geliştirmenize ve dağıtmanıza yardımcı olabilecek güvenlik denetimleri ve etkinlikleri önerilir.

- [Güvenli uygulamalar geliştirme](secure-develop.md)
- [Güvenli uygulamalar dağıtma](secure-deploy.md)
