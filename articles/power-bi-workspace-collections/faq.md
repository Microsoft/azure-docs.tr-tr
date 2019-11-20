---
title: Power BI çalışma alanı koleksiyonları ile ilgili SSS
description: Power BI çalışma alanı koleksiyonları için ilgili sık sorulan sorular.
services: power-bi-workspace-collections
ms.service: power-bi-embedded
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/25/2017
ms.openlocfilehash: b737c7753ce374d0360738e37d83609d1db995b1
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672416"
---
# <a name="power-bi-workspace-collections-faq"></a>Power BI çalışma alanı koleksiyonları ile ilgili SSS

> [!IMPORTANT]
> Power BI Çalışma Alanı Koleksiyonları kullanım dışı bırakılmıştır ve Haziran 2018'e kadar veya anlaşmanızda belirtilen süre boyunca kullanılabilecektir. Uygulamanızda kesinti yaşanmaması için Power BI Embedded'a geçirmeyi planlamanız önerilir. Verilerinizi Power BI Embedded'a nasıl taşıyacağınızı öğrenmek için bkz. [Power BI Çalışma Alanı Koleksiyonları'nı Power BI Embedded'a geçirme](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="what-is-microsoft-power-bi-workspace-collections"></a>Microsoft Power BI çalışma alanı koleksiyonları nedir?

Power BI çalışma alanı koleksiyonları, uygulama geliştiricilerinin etkileyici sağlayan bir Azure hizmeti, tam etkileşimli raporlar ve görselleştirmeler müşteri uygulamaları zaman ve para harcamazsınız kendi denetimlerini sıfırdan oluşturmak zorunda karşılıklı ' dir. Artık Power BI çalışma alanı koleksiyonları kullanılabilir SLA ile 9 veri merkezlerinde dünya çapında sahibiz. Gelişmiş filtreleme için biz hizmetinde satır düzeyi güvenlik (RLS) Power BI çalışma alanı koleksiyonları ile veri güvenliği desteği gibi işlevler de iyileştirdik. Ayrıca basitleştirdik ve fiyatlandırma modeli Power BI çalışma alanı koleksiyonları güncelleştirildi.

## <a name="who-would-want-to-use-microsoft-power-bi-workspace-collections-and-why"></a>Microsoft Power BI çalışma alanı koleksiyonları kullanmak isteyeceğiniz kimin ve neden?

Kullanıcıları için etkileyici ve etkileşimli veri görselleştirme deneyimleri tüm kullanıcıların cihazlarını kendileri oluşturmanıza gerek kalmadan sunmak isteyen uygulama geliştiricileri için Microsoft Power BI çalışma alanı koleksiyonları ' dir. Power BI çalışma alanı koleksiyonları ile doğrudan sorgu her zaman yukarı-başından bu yana görünümlerle geliştiricilere sunabilirsiniz. Geliştiriciler ayrıca program aracılığıyla dağıtabileceğiniz ve yönetebileceğiniz Power BI Azure Resource Manager API'lerini ve Power BI API'leri ile otomatik hale getirin. Her şey gibi Power BI, embedded hizmetini otomatik olarak kullanımını ve uygulamanızın ihtiyaçlarını karşılayacak şekilde ölçeklendirilir. Power BI çalışma alanı koleksiyonları hizmeti, bir Kullandıkça Öde tüketim tabanlı fiyatlandırma modeli sunar.

## <a name="how-does-power-bi-workspace-collections-relate-to-the-power-bi-service"></a>Power BI çalışma alanı koleksiyonları Power BI hizmetine nasıl ilişkilidir?

Power BI çalışma alanı koleksiyonları ve Power BI hizmeti ayrı tekliflerdir. Power BI çalışma alanı koleksiyonları özellikleri bir kullanım tabanlı faturalandırma modeli, Azure Portalı aracılığıyla dağıtılır ve ISV'ler uygulamalarında kullanılacak müşterileri için veri görselleştirmeleri ekleyin sağlamak için tasarlanmıştır. Power BI hizmetinde faturalandırılır ve Microsoft 365 Yönetim Merkezi dağıtılan ve tek başına bir genel amaçlı BI teklifinin Kurumsal dahili kullanım öncelikle hedef alır. Power BI hizmeti hakkında daha fazla bilgi için bkz: [www.powerbi.com](https://powerbi.microsoft.com).

## <a name="how-does-power-bi-workspace-collections-improve-my-app"></a>Power BI çalışma alanı koleksiyonları uygulamamı nasıl artırır?

Kullanıcı, uygulamanızda doğrudan bildirmek için çarpıcı, etkileşimli veri görselleştirmeleri yararlanabilir, önemli ölçüde daha güçlü uygulamalardır. Power BI çalışma alanı koleksiyonları, yardımcı program uygulaması, kullanıcı memnuniyeti ve bağlılık artırabilir ve teslim bağlamsal analytics ile herhangi bir cihazda kolaylaştıran etkileşimli, her zaman güncel, zengin veri görselleştirmeleri ile uygulamanızı geliştirin sağlar.

## <a name="are-there-any-rules-or-restrictions-about-how-i-can-use-power-bi-workspace-collections-in-my-app"></a>Herhangi bir kural veya Power BI çalışma alanı koleksiyonları Uygulamam nasıl kullanabilirim ilgili kısıtlamalar var mı?

Power BI çalışma alanı koleksiyonları, üçüncü taraf kullanımı için sağlanan uygulamalarınıza yöneliktir. Power BI çalışma alanı koleksiyonları hizmetini dahili bir iş uygulaması oluşturmak için kullanmak istiyorsanız, dahili kullanıcılarınızın her bir Power BI Pro USL gerekir ve kuruluşunuz, Power BI çalışma alanı koleksiyonları hizmetinin tüketimi için ücretlendirilirsiniz Power BI Pro USL ücretlerine ek olarak. Hem Power BI Pro usl hem de dahili uygulamalara yönelik Power BI çalışma alanı koleksiyonları tüketim maliyetlerinin ücretlendirilmesine engel olmak amacıyla, Power BI hizmeti, kendi içerik ekleme özelliklerini Power BI çalışma alanı koleksiyonları dışında hiçbir ek maliyet sunar Power BI usl (dev.powerbi.com).

## <a name="can-power-bi-workspace-collections-be-used-to-create-internal-applications"></a>Power BI çalışma alanı koleksiyonları dahili uygulamalar oluşturmak için kullanılabilir mi?

Hayır, Power BI çalışma alanı koleksiyonları dış kullanıcılar tarafından yalnızca kullanılmak üzere tasarlanmış ve dahili iş uygulamalarında kullanılmamalıdır. Dahili iş uygulamalarında kullanmak üzere Power BI içeriği eklemek için Power BI hizmetini kullanmalısınız ve bu içerikten yararlanan tüm kullanıcıların, geçerli bir Power BI Ücretsiz veya Power BI Pro kullanıcı abonelik lisansı olmalıdır. İç uygulamaları Power BI hizmeti ile tümleştirme hakkında daha fazla bilgi edinilebilir [ https://dev.powerbi.com ](https://dev.powerbi.com).

## <a name="is-this-service-available-globally"></a>Bu hizmet dünya çapında kullanılabilir mi?

Power BI çalışma alanı koleksiyonları hizmet çoğu veri merkezlerinde kullanıma sunuldu. Her zaman en son kullanılabilirlik denetleyebilirsiniz [burada](https://azure.microsoft.com/status/).

## <a name="what-is-the-available-sla-for-the-service"></a>Hizmet için kullanılabilir SLA'sı nedir?

Standart Azure SLA ile Power BI çalışma alanı koleksiyonları. Bkz: [hizmet düzeyi sözleşmeleri](https://azure.microsoft.com/support/legal/sla/) daha fazla bilgi için.

## <a name="what-is-a-report-session-and-how-is-it-billed"></a>Bir rapor oturumu nedir ve nasıl faturalandırılır?

Oturum, son kullanıcı ve bir Power BI çalışma alanı koleksiyonları rapor arasındaki etkileşimler kümesidir. Bir kullanıcı, bir Power BI çalışma alanı koleksiyonları raporu görüntülenen her zaman bir oturum başlatılır ve abonelik sahibi bir oturum için ücretlendirilir. Oturumlar, rapordaki görsel öğe sayısından veya rapor içeriğinin ne sıklıkla yenilendiğinden bağımsız olarak sabit fiyat üzerinden ücretlendirilir. Ya da kullanıcı raporu kapattığında veya oturum zaman aşımına zaman oturumu sona erdirdiği bir saat sonra.

## <a name="do-you-offer-any-tools-or-guidance-to-help-me-estimate-how-many-renderssession-i-should-expect-how-will-i-know-how-many-renders-have-been-completed"></a>Herhangi bir araç veya bana kaç tane işleme/oturum tahmin yardımcı olan yönergeler sunuyor musunuz beklemeliyim? Olduğunu nasıl öğrenebilirim kaç işler tamamlanan?

Azure portalı kaç işler fatura ayrıntılarını sağlar. / rapor oturumları aboneliğinizi karşı gerçekleştirilen.

## <a name="do-i-need-a-power-bi-subscription-in-order-to-develop-applications-with-power-bi-workspace-collections-how-do-i-get-started"></a>Power BI aboneliği Power BI çalışma alanı koleksiyonları ile uygulamaları geliştirmek için ihtiyacım var? Nasıl kullanmaya başlayabilirim?

Uygulama geliştiricisi olarak, uygulamanızda kullanmak istediğiniz görselleştirmeler ve raporlar oluşturmak için Power BI aboneliği olması gerekmez. Bir Microsoft Azure aboneliği ve ücretsiz Power BI Desktop uygulaması gerekir.

Power BI çalışma alanı koleksiyonları Hizmeti'yle nasıl Ayrıntılar için hizmet belgelerimize bakın.

## <a name="i-have-an-azure-subscription-can-i-use-power-bi-workspace-collections-using-my-existing-subscription"></a>Bir Azure Aboneliğim var. Power BI çalışma alanı koleksiyonları kullanarak mevcut Aboneliğimi kullanabilir miyim?

Evet. Mevcut Azure aboneliğinizi sağlamak ve Microsoft Power BI çalışma alanı koleksiyonları hizmeti kullanmak için kullanabilirsiniz.

## <a name="does-my-application-end-user-need-a-power-bi-license"></a>My uygulama son kullanıcı, bir Power BI lisansı gerekiyor mu?

Hayır. Uygulamanızın son kullanıcılar ayrıca uygulama veri görselleştirmeleri erişmek için satın alma veya Power BI aboneliği için gerekli değildir. Power BI çalışma alanı koleksiyonları modelde, uygulama sağlayıcısı olarak, hizmet için Azure tüketim ölçüm faturalandırılır. Başvurmak [fiyatlandırma ve lisanslama sayfası](https://go.microsoft.com/fwlink/?LinkId=760527).

## <a name="how-does-user-authentication-work-with-power-bi-workspace-collections"></a>Kullanıcı kimlik doğrulaması, Power BI çalışma alanı koleksiyonları ile nasıl çalışır?

Power BI çalışma alanı koleksiyonları hizmet kimlik doğrulaması ve yetkilendirme açık son kullanıcı kimlik doğrulaması yerine uygulama belirteçlerini kullanır. Uygulama belirteci modelde, uygulamanız kimlik doğrulaması ve yetkilendirme son kullanıcılarınız için yönetir. Daha sonra gerektiğinde uygulamanızı oluşturur

ve istenen rapor oluşturulacak hizmetimiz bildiren uygulama belirteçlerini gönderir. Bunu yapabilirsiniz ancak bu tasarımı uygulamanız için kullanıcı kimlik doğrulaması ve yetkilendirme, Azure AD'yi kullanacak şekilde gerektirmez. Uygulama belirteçleri hakkında daha fazla bilgi [burada](app-token-flow.md). Ayrıca Gelişmiş Güvenlik senaryoları filtreleme için satır düzeyi güvenlik özelliği (RLS) Power BI çalışma alanı koleksiyonları'nda kullanıma sunduk.

## <a name="what-data-sources-are-currently-supported-with-power-bi-workspace-collections"></a>Hangi veri kaynakları şu anda Power BI çalışma alanı koleksiyonları ile desteklenir?

Temel kimlik bilgileri aracılığıyla doğrudan sorgu kullanın, bulut veri kaynaklarına erişimi desteklemek için kullanacağız. Bu, Azure SQL DB ile Azure SQL DW gibi kaynakları şu anda desteklenen anlamına gelir. Biz, diğer veri kaynakları için destek eklendi ve önümüzdeki aylarda türleri erişim. Daha fazla bilgi için [veri kaynağına bağlanma](connect-datasource.md).

## <a name="how-does-the-tenancy-model-work-for-power-bi-workspace-collections"></a>Power BI çalışma alanı koleksiyonları için kiralama modeli nasıl çalışır?

Power BI çalışma alanı koleksiyonları modelde, Azure AD kiracıları müşterileriniz için açık gereksinimi yoktur. Azure AD veya müşterileriniz için gerektirecek şekilde seçebilirsiniz. Sonuç olarak, uygulamanızın ve altyapınızın mimarisi ne Power BI çalışma alanı koleksiyonları için gerekli kiracılı model belirttiğini gösterir.

Geliştiriciler/çalıştığı veya uygulamanızı oluşturmaya çalışan olduğunda Azure aboneliğinizi ve çalışma alanı koleksiyonları Azure portalı üzerinden yönetmek için bir AAD kullanıcı hesabı olmalıdır. Geliştiricilerin raporları alın, bağlantı dizelerini değiştirmek ve Al'ı etkinleştirmek için program tabalı API'lerle URL'leri gücünden yararlanarak uygulama belirteçlerini kimlik doğrulaması için bunun yerine ekleme ve sonuç olarak, bir AAD gerektirmez.

## <a name="where-can-i-learn-more"></a>Nereden daha fazla bilgi edinebilirim?

Ziyaret ettiğiniz [Power BI çalışma alanı koleksiyonları belgeleri sayfasını](get-started.md). Bu hizmet hakkında güncel ederek kalabileceği [Power BI blogunu](https://powerbi.microsoft.com/blog/) veya dev.powerbi.com adresindeki Power BI Geliştirici Merkezi'ni ziyaret edin. Sorular da sorabilirsiniz [Stack Overflow](https://stackoverflow.com/questions/tagged/powerbi).

## <a name="how-do-i-get-started"></a>Nasıl kullanmaya başlayabilirim?

Şimdi ücretsiz başlayabilirsiniz! Bir Azure aboneliğiniz varsa, artık Power BI çalışma alanı koleksiyonları Azure portalından doğrudan sağlayabilirsiniz. Ayrıca, [ücretsiz Azure hesabı](https://azure.microsoft.com/free/). Power BI çalışma alanı koleksiyonları hizmet sağladıktan sonra kolayca doğrudan Power BI REST API'lerini kullanın, veya Geliştirici SDK'sı kullanılabilir [GitHub](https://go.microsoft.com/fwlink/?LinkID=746472). SDK'sı Geliştirici yararlanarak nasıl örnekleri verilmiştir.

## <a name="see-also"></a>Ayrıca bkz.

[Microsoft Power BI çalışma alanı koleksiyonları nedir](what-are-power-bi-workspace-collections.md)
[Microsoft Power BI çalışma alanı koleksiyonları ile çalışmaya başlama](get-started.md)
[örnek ile kullanmaya başlama](get-started-sample.md)   
[JavaScript Örnek Ekleme](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Başka sorunuz mu var? [Power BI Topluluğu'nu deneyin](https://community.powerbi.com/)