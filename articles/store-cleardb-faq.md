---
title: Azure App Service ile ClearDB MySql veritabanları için SSS
description: Azure App Service ile ClearDB MySQL veritabanlarını kullanma hakkında sık sorulan soruların yanıtları.
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
ms.custom: references_regions
ms.openlocfilehash: d9d4ae746c192e8b4afd1781c1e072fc0dc70422
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98599790"
---
# <a name="faq-for-cleardb-mysql-databases-with-azure-app-service"></a>Azure App Service ile ClearDB MySql veritabanları için SSS
Bu SSS, Azure Web Apps için ClearDB MySQL veritabanlarını kullanma ve satın alma hakkında sık sorulan soruları yanıtlar.

> [!IMPORTANT]
> 13 Haziran 2018 itibariyle ClearDB, şu anda Microsoft tarafından faturalanmış Azure tabanlı müşterileri ClearDB ile doğrudan faturalandırma modeline geçti. Bu makaledeki bilgiler artık güncel değil. Artık Azure 'da oluşturulmuş bir ClearDB veritabanı oluşturmak veya yükseltmek mümkün olmayacaktır.
>
> Daha fazla ayrıntı ve sonraki adımlar için bkz. [ClearDB hizmet planlarına yapılan değişiklikler](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/).

## <a name="what-options-do-i-have-for-mysql-on-azure"></a>Azure 'da MySQL için hangi seçeneklere sahibim?
Bu hizmetle ilgili en son bilgiler için bkz. [ClearDB](https://w2.cleardb.net/) . ClearDB bir MySQL barındırma hizmetidir ve MySQL altyapısını sizin için yönetir. 

Azure 'da MySQL barındırmak için çeşitli diğer seçenekleriniz vardır:
* [MySQL için Azure Veritabanı](https://azure.microsoft.com/services/mysql/)
* [Azure VM üzerinde çalışan MySQL kümesi](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)
* [Azure VM 'de çalışan tek MySQL örneği](/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="do-i-need-a-credit-card-for-the-web-app--mysql-template-in-the-azure-marketplace"></a>Azure Marketi 'nde Web uygulaması + MySQL şablonu için bir kredi kartına ihtiyacım var mı?
Bu, kullanmakta olduğunuz aboneliğin türüne bağlıdır. Yaygın olarak kullanılan bazı abonelik türleri şunlardır:

* [Kullandıkça Öde](https://azure.microsoft.com/offers/ms-azr-0003p/): kredi kartı gerektirir ve ücretli bir MySQL veritabanı satın aldığınızda kredi kartınız ücretlendirilir.
* [Ücretsiz deneme](https://azure.microsoft.com/pricing/free-trial/): Microsoft Azure hizmetleriyle kullanım için krediler içerir ancak üçüncü taraf kaynakları satın almaya izin vermez. Üçüncü taraf hizmetlerini veya ücretli bir MySQL veritabanını satın almak için, kredi kartı etkinleştirilmiş bir abonelik kullanmanız gerekir. Web Apps için, ÜCRETSIZ bir ClearDB MySQL veritabanı oluşturabilirsiniz.
* [MSDN aboneliği](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/) ve **MSDN dev Test Kullandıkça Öde**: ücretsiz denemeye benzer şekilde, bir MSDN aboneliği, ClearDB 'den ücretli bir MySQL çözümü satın almak için kredi kartınız olmasını gerektirir.
* [Kurumsal Anlaşma (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/): EA müşterileri, ayrı ve birleştirilmiş bir faturada tüm Azure Marketi (üçüncü taraf) satın alımları için her üç ayda bir, her üç ayda bir faturalandırılır. Tüm Market satınalmaları için Azure ön ödemesi (daha önce parasal taahhüt olarak adlandırılır) dışında faturalandırılırsınız. Azure Mağazası 'nın Azerbaycan, Hırvatistan, Norveç ve Porto Riko 'ya kayıtlı müşteriler tarafından kullanılamayacağını lütfen unutmayın. 

## <a name="why-was-i-charged-350-for-a-web-app--mysql-from-the-azure-marketplace"></a>Azure Marketi 'nden bir Web uygulaması + MySQL için neden $3,50 ücret ödersiniz?
Varsayılan veritabanı seçeneği Titan 'dir ve bu $3,50 ' dir. Veritabanı oluşturma sırasında maliyeti göstermedik ve istemediğiniz bir veritabanını yanlışlıkla satın alabilirsiniz. Deneyimi geliştirmenin bir yolunu bulmaya çalışıyoruz ancak kaynakların dağıtımını başlatmadan önce Web **uygulaması ve veritabanı** için tüm seçtiğiniz fiyatlandırma katmanlarını denetlemeniz gerekir.

## <a name="i-am-running-mysql-on-my-own-azure-virtual-machine-can-i-connect-my-azure-web-app-to-my-database"></a>Kendi Azure sanal makinmda MySQL çalıştırıyorum. Azure Web uygulamamı veritabanıma bağlanabilir miyim?
Evet. Web uygulamanızı, Azure VM 'niz Web uygulamanıza uzaktan erişim verdiği sürece veritabanınıza bağlayabilirsiniz. Daha fazla bilgi için bkz. [bir sanal makineye MySQL 'ı Install](/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="in-which-countriesregions-are-cleardb-premium-mysql-clusters-supported"></a>Hangi ülkelerde/bölgelerde ClearDB Premium MySQL kümeleri desteklenir?
ClearDB Premium MySQL kümeleri, dünya çapındaki tüm Azure bölgelerinde Hindistan, Avustralya, Brezilya Güney ve Çin dışında kullanılabilir.

## <a name="can-i-create-a-new-cluster-prior-to-creating-a-database-with-cleardb-premium-cluster-solution"></a>ClearDB Premium küme çözümüyle bir veritabanı oluşturmadan önce yeni bir küme oluşturabilir miyim?
Hayır, boş ClearDB kümeleri oluşturma desteklenmez. Azure portal, bir kümede veritabanları oluşturmanıza olanak sağlar ve aynı zamanda yeni bir küme oluşturabilir.

## <a name="will-i-be-warned-if-i-try-to-delete-a-cleardb-mysql-database-that-is-in-use-by-one-of-my-applications"></a>Uygulamalarımdan biri tarafından kullanılmakta olan bir ClearDB MySQL veritabanını silmeye çalışırsam, uyarı alıyorum?
Hayır, uygulamanızın bağlı olduğu bir market satın alımı silerseniz Azure sizi uyarmaz.

## <a name="which-regions-can-i-create-cleardb-databases-in"></a>Hangi bölgeleri ClearDB veritabanlarını oluşturabilirim?
Azure Marketi, Azerbaycan, Hırvatistan, Norveç veya Porto Riko 'ya kayıtlı müşteriler tarafından kullanılamaz. ClearDB, bu bölgelerde kullanılamaz.

## <a name="what-pricing-tier-should-i-choose-for-a-production-web-app-and-database"></a>Üretim Web uygulaması ve veritabanı için hangi fiyatlandırma katmanını seçmem gerekir?
Web Apps için temel veya daha yüksek bir fiyatlandırma katmanını kullanın. ClearDB için, bir Saımor veya Jupiter planı önerilir. İhtiyaçlarınıza uygun olanı seçmek için her bir fiyatlandırma katmanının, hem [Web Apps](https://azure.microsoft.com/pricing/details/app-service/) hem de [ClearDB MySQL veritabanlarının](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/) sınırlamalarını & özelliklerini gözden geçirin.

## <a name="how-do-i-upgrade-my-cleardb-database-from-one-plan-to-another"></a>ClearDB veritabanımı bir plandan diğerine Yükselt mi Nasıl yaparım??
[Azure Portal](https://portal.azure.com), bir ClearDB paylaşılan barındırma veritabanını ölçeklendirebilirsiniz. Daha fazla bilgi edinmek için bu [makaleyi](https://blogs.msdn.microsoft.com/appserviceteam/2016/10/06/upgrade-your-cleardb-mysql-database-in-azure-portal/) okuyun. Şu anda Azure portal ClearDB Premium kümelerine yönelik yükseltmeyi desteklemiyoruz.

## <a name="i-cant-see-my-cleardb-database-in-azure-portal"></a>ClearDB veritabanımı Azure portal göremiyorum?
Klasik bir ClearDB veritabanı oluşturduysanız, veritabanınızı [Azure Portal](https://portal.azure.com)göremezsiniz. Bu senaryo için bir geçici çözüm yok.

## <a name="who-do-i-contact-for-support-when-my-database-is-down"></a>Veritabanım kapatıldığında kimler sizinle iletişime başvurmalıyım?
Herhangi bir veritabanı ile ilgili sorun için [ClearDB desteğiyle](https://www.cleardb.com/developers/help/support) iletişim kurun. Azure abonelik bilgilerinizi sunmaya hazırlıklı olun.

## <a name="can-i-create-additional-users-for-my-cleardb-mysql-database-cluster-solution"></a>ClearDB MySQL veritabanı kümesi çözümünüz için ek kullanıcılar oluşturabilir miyim?
Hayır. Ek kullanıcılar oluşturamazsınız, ancak ClearDB veritabanı kümenizde ek veritabanları oluşturabilirsiniz.  

## <a name="can-basicpro-series-databases-be-upgraded-in-place-similar-to-planetary-plans-today-on-cleardb-portal"></a>Temel/Pro Serisi veritabanları, şu anda ClearDB portalında Planetary planlarına benzer şekilde yükseltilebilir mi?
Evet, temel seri veritabanları yerinde yükseltilebilir (temel 60 ile temel 500 arasında). Pro serisi, Pro 60 hariç yerinde (Pro 125 ' den Pro 1000) yükseltilebilir. Pro 60 veritabanını yükseltmeyi Şu anda desteklemiyoruz. 

## <a name="when-i-migrate-my-resources-from-one-subscription-to-another-does-my-cleardb-mysql-database-get-migrated-as-well"></a>Kaynaklarımı bir abonelikten diğerine geçirdiğimde ClearDB MySQL Veritabanım de geçirilir mi?
Abonelikler arasında kaynak geçişi gerçekleştirdiğinizde, bazı [sınırlamalar](azure-resource-manager/management/move-support-resources.md) uygulanır. ClearDB MySQL veritabanı bir üçüncü taraf hizmetidir ve bu nedenle Azure abonelik geçişi sırasında geçirilmez. Azure kaynaklarını geçirmeden önce MySQL veritabanınızın geçişini yönetmeyin, ClearDB MySQL veritabanlarınız devre dışı bırakılabilir. Veritabanlarınızı kendiniz geçirin ve ardından Web uygulamanız için Azure abonelik geçişini gerçekleştirin. 

## <a name="i-hit-the-spending-limit-on-my-subscription-i-removed-the-limit-and-my-app-service-is-online-however-the-database-is-not-accessible-how-do-i-re-enable-the-cleardb-database"></a>Aboneliğimde harcama limitine ulaştı. Limiti kaldırdım ve App Service çevrimiçi, ancak veritabanı erişilebilir değil. ClearDB veritabanını yeniden etkinleştirmek Nasıl yaparım? istiyor musunuz?
Veritabanını yeniden etkinleştirmek için [ClearDB desteğiyle](https://www.cleardb.com/developers/help/support) iletişim kurun. Azure abonelik bilgilerinizi ve veritabanı adını sağlayın.

## <a name="can-i-transfer-a-cleardb-database-from-a-credit-card-subscription-to-an-ea-subscription"></a>Bir ClearDB veritabanını bir kredi kartı aboneliğinden EA aboneliğine aktarabilir miyim?
Mevcut ClearDB veritabanları, mevcut aboneliklerle ilişkili kredi kartını kullanır. EA aboneliğini kullanmak için verilerinizi yeni bir veritabanına geçirmeniz gerekir:

* EA aboneliğiniz ile yeni bir ClearDB veritabanı satın alın.
* Verilerinizi yeni veritabanınıza geçirin.
* Uygulamanızı yeni veritabanını kullanacak şekilde güncelleştirin.
* Eski ClearDB veritabanınızı silin.

MySQL (ClearDB) ile yeni bir Web uygulaması oluşturduğunuzda veya bir MySQL veritabanı (ClearDB) oluşturduğunuzda seçtiğiniz abonelik, hizmet için nasıl ödeme yapılacağını belirler. EA aboneliğiyle, Azure portal ClearDB gibi üçüncü taraf hizmetlerin temin etmeyecektir. EA abonelikleri, Azure ön ödemesi dışında faturalandırılır ve üç ayda bir ve daha sonra faturalandırılır. EA müşterisinin, herhangi bir üçüncü taraf Market hizmeti için ödeme yapmak üzere kredi kartı gibi bir ödeme yöntemi ayarlaması gerekir.

## <a name="where-can-i-see-the-charges-for-cleardb-resources-in-an-ea-subscription"></a>Bir EA aboneliğinde ClearDB kaynakları için ücretleri nereden görebilirim?
Doğrudan EA müşterileri için, Azure Marketi ücretleri Enterprise Portal görünür. Tüm Market satın alımların ve tüketiminin Azure ön ödemesi dışında faturalandırıldığını ve üç ayda bir ve daha sonra faturalandırıldığını unutmayın. EA müşterilerinin doğrudan üçüncü taraf hizmet sağlayıcılarına ödemesi gerekir ve bu, EA hesabıyla kredi kartı gibi bir ödeme yöntemi etkinleştirerek bunu yapabilir.

Dolaylı EA müşterileri, Enterprise Portal **abonelikleri Yönet** sayfasında Azure Market aboneliklerini bulabilir, ancak fiyatlandırma gizlenir. Müşteriler, Market fiyatları için LSP ile iletişime geçmelidir.

Üçüncü taraf hizmetler için Azure Marketi 'ne erişim, EA Azure kayıt yöneticileri tarafından yönetilebilir. Bu kişiler, Enterprise Portal hesaplar bölümünde yer alarak hesapları ve abonelikleri yönetme bölümündeki mağazadan üçüncü taraf satın alma işlemlerine erişimi devre dışı bırakabilir veya yeniden etkinleştirebilir.

## <a name="who-do-i-contact-for-questions-about-my-bill-for-cleardb-services-in-my-ea-subscription"></a>EA Aboneliğimde ClearDB Hizmetleri için faturamm hakkındaki sorulara kimler başvurmalıyım?
[Kurumsal müşteri desteğine](https://aka.ms/AzureEntSupport) başvurun ve bunların EA kaydı kapsamında faturalandırılması gerektiğini varsayar. EA Portalı Destek ekibi, sorularınızı yanıtlayacak veya sorununuzu çözmeye yardımcı olur.

## <a name="more-information"></a>Daha fazla bilgi
[Azure Market Hakkında SSS](https://azure.microsoft.com/marketplace/faq/)