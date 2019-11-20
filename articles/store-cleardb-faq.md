---
title: Azure Uygulama Hizmeti ile ClearDB MySQL veritabanları hakkında SSS
description: Azure App Service ile ClearDB MySQL veritabanları kullanma hakkında sık sorulan sorulara yanıtlar.
documentationcenter: php
services: mysql
author: sunbuild
manager: yochayk
tags: mysql
ms.service: multiple
ms.topic: article
ms.date: 10/27/2016
ms.author: sumuth
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 7b6d38f7755ebd97791959a5a414b09fe4ac769b
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718006"
---
# <a name="faq-for-cleardb-mysql-databases-with-azure-app-service"></a>Azure Uygulama Hizmeti ile ClearDB MySQL veritabanları hakkında SSS
Bu SSS ClearDB MySQL veritabanları, Azure Web Apps için satın alma ve kullanma hakkında sık sorulan soruları yanıtlar.

> [!IMPORTANT]
> 13 Haziran 2018'den itibaren ClearDB ile doğrudan faturalandırma modeliyle, şu anda Microsoft tarafından faturalandırılan Azure tabanlı müşteriler ClearDB geçti. Bu makaledeki bilgiler güncel değil. Artık oluşturabilir veya Azure'da oluşturulan bir ClearDB veritabanı yükseltme olacaktır.
>
> Daha fazla ayrıntı ve sonraki adımlar için bkz. [değişiklikleri ClearDB hizmet planları için](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/).

## <a name="what-options-do-i-have-for-mysql-on-azure"></a>Azure üzerinde MySQL için hangi seçenekler var mı?
Bkz: [ClearDB](https://w2.cleardb.net/) hizmetin en son bilgiler için. ClearDB, bir MySQL barındırma hizmeti olan ve MySQL altyapı sizin yerinize yönetir. 

Azure'da MySQL barındırma için birkaç seçeneğiniz vardır:
* [MySQL için Azure Veritabanı](https://azure.microsoft.com/services/mysql/)
* [Bir Azure sanal makinesinde çalışan MySQL kümesi](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)
* [Tek bir Azure sanal makinesinde çalışan MySQL örneği](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="do-i-need-a-credit-card-for-the-web-app--mysql-template-in-the-azure-marketplace"></a>Kredi kartı kullanmam gerekiyor için Web uygulaması + MySQL şablonu Azure Marketi'nde?
Bu, kullanmakta olduğunuz abonelik türüne bağlıdır. Bazı yaygın olarak kullanılan abonelik türleri şunlardır:

* [Kullandıkça Öde](https://azure.microsoft.com/offers/ms-azr-0003p/): Bir kredi kartı ve bir Ücretli MySQL veritabanı kredi kartınızdan ücret satın aldığınızda gerektirir.
* [Ücretsiz deneme sürümü](https://azure.microsoft.com/pricing/free-trial/): Microsoft Azure Hizmetleri ile kullanmak için kredi içerir, ancak üçüncü taraf kaynakların satın alma izin vermez. Üçüncü taraf Hizmetlerinde veya kredi kartı kullanmanız gerekir, ücretli bir MySQL veritabanı satın almak için abonelik etkin. Web Apps için ücretsiz ClearDB MySQL veritabanı oluşturabilirsiniz.
* [MSDN aboneliği](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/) ve **MSDN Geliştirme ve Test Kullandıkça Öde**: Benzer şekilde, ücretsiz deneme, MSDN aboneliği Cleardb'den MySQL çözüm Ücretli satın almak için bir kredi kartı olmasını gerektirir.
* [Kurumsal Anlaşma (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/): EA müşterileri EA'ya göre tüm Azure Marketi (üçüncü taraf) aldıklarını birleştirilmiş, ayrı bir fatura için her bir üç aylık olarak faturalandırılır. Marketi satın alma işlemleri için parasal taahhüdü dışında faturalandırılır. Şu anda Azure Store müşterileri tarafından Azerbaycan, Hırvatistan, Norveç ve Porto Riko kayıtlı değil, lütfen unutmayın. 

## <a name="why-was-i-charged-350-for-a-web-app--mysql-from-the-azure-marketplace"></a>Bir Web uygulaması + MySQL Azure Market'ten 3.50 neden ücretlendirildim?
Varsayılan veritabanı $3.50 olan Titan seçenektir. Maliyet veritabanı oluşturma sırasında gösterme ve bir veritabanı için istediğinize yaramadı yanlışlıkla satın alabilirsiniz. Deneyimini iyileştirmek üzere bir yolunu bulmak çalışıyoruz ancak bu tarihe kadar tıklatmadan önce tüm, seçili fiyatlandırma katmanları için web uygulaması ve veritabanı denetlemelisiniz **Oluştur** ve kaynakların dağıtımı başlatılıyor.

## <a name="i-am-running-mysql-on-my-own-azure-virtual-machine-can-i-connect-my-azure-web-app-to-my-database"></a>Kendi Azure sanal makinesine MySQL çalıştırıyorum. Veritabanım için Azure web uygulamamı bağlanabilir miyim?
Evet. Azure VM, web uygulamanız için uzaktan erişim verdiği sürece, web uygulamanızı veritabanına bağlanabilirsiniz. Daha fazla bilgi için [bir sanal makineye MySQL yükleme](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="in-which-countriesregions-are-cleardb-premium-mysql-clusters-supported"></a>İçinde ClearDB Premium MySQL kümeleri desteklenen ülkeler/bölgeler misiniz?
ClearDB Premium MySQL kümeleri, Hindistan, Avustralya, Brezilya Güney ve Çin hariç olmak üzere dünya çapındaki tüm Azure bölgelerinde kullanılabilir.

## <a name="can-i-create-a-new-cluster-prior-to-creating-a-database-with-cleardb-premium-cluster-solution"></a>ClearDB premium küme çözümü ile bir veritabanı oluşturmadan önce yeni bir küme oluşturabilir miyim?
Hayır, boş ClearDB kümeleri oluşturma desteklenmiyor. Azure portalındaki veritabanları aynı anda yeni bir küme oluşturabilir bir küme oluşturmanıza olanak sağlar.

## <a name="will-i-be-warned-if-i-try-to-delete-a-cleardb-mysql-database-that-is-in-use-by-one-of-my-applications"></a>Ben miyim kullanımda olan bir ClearDB MySQL veritabanını silmek uygulamalarımdan birine denerseniz uyarılır?
Hayır, Azure, uygulamanızın bağlı olduğu bir Market satın alımı silerseniz uyarmaz.

## <a name="which-regions-can-i-create-cleardb-databases-in"></a>Hangi bölgeler ClearDB veritabanlarında oluşturabilirim?
Azure Market müşterileri tarafından Azerbaycan, Hırvatistan, Norveç veya Porto Riko kayıtlı değil. ClearDB, bu bölgede kullanılamıyor.

## <a name="what-pricing-tier-should-i-choose-for-a-production-web-app-and-database"></a>Bir üretim web uygulaması ve veritabanı için hangi fiyatlandırma katmanını seçmeliyim?
Temel veya daha yüksek bir fiyatlandırma katmanı, Web Apps için kullanın. İçin ClearDB, Saturn veya Jupiter planı öneririz. Özellikler ve her ikisi için her fiyatlandırma katmanının sınırlamaları gözden [Web Apps](https://azure.microsoft.com/pricing/details/app-service/) ve [ClearDB MySQL veritabanları](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/) gereksinimlerinize en uygun olanı seçmeleri için.

## <a name="how-do-i-upgrade-my-cleardb-database-from-one-plan-to-another"></a>Nasıl ClearDB Veritabanım bir plandan diğerine yükseltebilirim?
İçinde [Azure portalında](https://portal.azure.com), ClearDB bir paylaşılan barındırma veritabanını ölçeklendirebilirsiniz. Bu okuma [makale](https://blogs.msdn.microsoft.com/appserviceteam/2016/10/06/upgrade-your-cleardb-mysql-database-in-azure-portal/) daha fazla bilgi için. Şu anda Azure portalında ClearDB Premium kümeler için yükseltme desteklemiyoruz.

## <a name="i-cant-see-my-cleardb-database-in-azure-portal"></a>ClearDB Veritabanım Azure portalında göremiyorum?
Klasik modelde bir ClearDB veritabanı oluşturduysanız, veritabanınızdaki görmek mümkün olmayacaktır [Azure portalında](https://portal.azure.com). Var olan hiçbir geçici bu senaryo için.

## <a name="who-do-i-contact-for-support-when-my-database-is-down"></a>Veritabanım kapalı olduğunda Kime desteği başvurmam gerekir?
İlgili kişi [ClearDB Destek](https://www.cleardb.com/developers/help/support) herhangi bir veritabanının ilgili sorunlar için. Azure abonelik bilgilerinizi sağlamaya hazır olun.

## <a name="can-i-create-additional-users-for-my-cleardb-mysql-database-cluster-solution"></a>ClearDB MySQL veritabanı kümesi çözümüm için ek kullanıcılar oluşturabilir miyim?
Hayır. Ek kullanıcılar oluşturamazsınız ancak ClearDB veritabanı kümenizde ek veritabanları oluşturabilirsiniz.  

## <a name="can-basicpro-series-databases-be-upgraded-in-place-similar-to-planetary-plans-today-on-cleardb-portal"></a>Veritabanları Basic/Pro serisi yükseltme yerinde ClearDB portalında bugün Planetary planlara benzer?
Evet, temel serisi veritabanları olabilir, yerinde (temel 500 aracılığıyla temel 60) yükseltildi. Pro serisi olabilir yerinde (Pro 125-Pro 1000) Pro 60 dışında yükseltildi. Pro 60 veritabanı şu anda yükseltiliyor desteklemez. 

## <a name="when-i-migrate-my-resources-from-one-subscription-to-another-does-my-cleardb-mysql-database-get-migrated-as-well"></a>Ben Kaynaklarım bir abonelikten diğerine geçiş yaptığınızda, ClearDB MySQL veritabanı de geçişi mu?
Gerçekleştirirken kaynak geçişi abonelikler arasında bazı [sınırlamaları](azure-resource-manager/move-support-resources.md) uygulayın. ClearDB MySQL veritabanı, bir üçüncü taraf hizmetidir ve bu nedenle Azure abonelik geçişi sırasında geçirilmedi. MySQL veritabanınızı Azure kaynakları geçirme önce geçişini yönetmeyin, ClearDB MySQL veritabanlarınızın devre dışı bırakılabilir. Veritabanlarınızı el ile ilk geçirin ve ardından web uygulamanız için Azure aboneliğine geçiş gerçekleştirin. 

## <a name="i-hit-the-spending-limit-on-my-subscription-i-removed-the-limit-and-my-app-service-is-online-however-the-database-is-not-accessible-how-do-i-re-enable-the-cleardb-database"></a>Miyim aboneliğime harcama sınırına ulaştı. Sınırı kaldırdım ve veritabanı erişilebilir değil ancak my App Service çevrimiçidir. ClearDB veritabanını yeniden nasıl etkinleştirebilirim?
İlgili kişi [ClearDB Destek](https://www.cleardb.com/developers/help/support) veritabanı yeniden etkinleştirmek için. Bunları Azure abonelik bilgilerini ve veritabanı adı ile sağlar.

## <a name="can-i-transfer-a-cleardb-database-from-a-credit-card-subscription-to-an-ea-subscription"></a>Bir Kurumsal Sözleşme aboneliğine bir ClearDB veritabanı bir kredi kartı aboneliğinden aktarabilirim?
ClearDB veritabanlarında mevcut abonelikleriyle ilişkili kredi kartı kullanın. Verilerinizi yeni bir veritabanına geçirmek için gereken bir EA aboneliği kullanmak için:

* EA aboneliğinize ile yeni bir ClearDB veritabanı satın alın.
* Verilerinizi yeni veritabanınızı geçirin.
* Yeni bir veritabanı kullanmak için uygulamanızı güncelleştirin.
* Eski, ClearDB veritabanını silin.

MySQL (ClearDB) ile yeni bir web uygulaması oluşturma veya MySQL veritabanı (ClearDB) oluşturma, seçtiğiniz abonelik, hizmet için nasıl ödeme yaparsınız belirler. Bir EA aboneliği ile Azure portalında üçüncü taraf hizmetleri gibi ClearDB tedarik biz engellemez. Kurumsal Anlaşma abonelikleri parasal taahhüdü dışında faturalandırılır ve üç aylık ve borç olarak faturalandırılır. EA müşterisinin herhangi bir üçüncü taraf Market Hizmetleri için ödeme için kredi kartı gibi bir ödeme yöntemi ayarlamanız gerekir.

## <a name="where-can-i-see-the-charges-for-cleardb-resources-in-an-ea-subscription"></a>Bir kurumsal Anlaşma aboneliklerinde ClearDB kaynak ücretleri nerede görebilirim?
Doğrudan EA müşterileri, Azure Market kullanım ücretlerinin Enterprise Portal'da görülebilir. Tüm Market alışverişleri ve tüketim parasal taahhüdü dışında faturalandırılır ve üç aylık ve tehirli olarak faturalandırılır ve unutmayın. EA müşterileri, doğrudan üçüncü taraf hizmeti sağlayıcıları için ödeme yapmam gerekir ve EA hesabıyla bir kredi kartı gibi bir ödeme yöntemi etkinleştirerek bunu yapabilirsiniz.

Doğrudan olmayan EA müşterileri, Azure Marketi abonelikleri üzerinde bulabilir **Aboneliklerini Yönet** Enterprise Portal sayfasının ancak fiyatlandırma gizlenir. Müşteriler, Market ücretlerini hakkında bilgi için LSP başvurmalıdır.

Üçüncü taraf hizmetleri için erişim Azure marketi, EA Azure kayıt yöneticileri tarafından yönetilebilir. Devre dışı bırakın veya erişim 3. taraf satın alımları için hesapları yönetme ve abonelikleri Store'dan Enterprise Portal'da hesaplar bölümü altında yeniden etkinleştirin.

## <a name="who-do-i-contact-for-questions-about-my-bill-for-cleardb-services-in-my-ea-subscription"></a>Kimin sorular için hakkında faturamı ClearDB hizmetler için EA aboneliğimde başvurmam gerekir?
İlgili kişi [Kurumsal Müşteri Destek](https://aka.ms/AzureEntSupport) kendi EA kaydı altında faturalama bakımından. EA Portal destek ekibi, sorunuzun yanıtlanması veya sorununuzu çözmenize yardımcı olması.

## <a name="more-information"></a>Daha fazla bilgi
[Azure Market SSS](https://azure.microsoft.com/marketplace/faq/)

