---
title: Test sürüşü nedir? Microsoft ticari Market
description: Market test sürücüsü özelliğinin açıklaması
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 06/19/2020
ms.openlocfilehash: e44d5d94a8dc172962a26f3e0dae9ccbb7f8a865
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818899"
---
# <a name="what-is-a-test-drive"></a>Test sürüşü nedir?

Test sürücüsü, satın almadan önce denemeye, son derece nitelikli müşteri adayları oluşturmaya ve daha fazla dönüştürmeye neden olacak bir seçenek sunarak potansiyel müşterilere teklifinizi sergilemenin harika bir yoludur. Bir sınama sürücüsü, ürününüzü gerçek hayatta gerçek bir uygulama senaryosunda hayata getirir. Ürününüzü deneyen müşteriler benzer bir çözüm satın almak için açık bir amaç gösterir. Daha gelişmiş müşteri adaylarını izleyerek bunu avantajınıza kullanın.

Müşterileriniz bir test sürücüsünden da faydalanır. İlk olarak ürününüzü denemeye izin vererek, satın alma sürecinin bir kısmını azaltmaktadır. Buna ek olarak, test sürücüsü önceden sağlanmış olur, yani müşterilerin ürünü indirmesi, kurulumu veya yapılandırması gerekmez.

## <a name="how-does-it-work"></a>Nasıl çalışır?

Test sürücüleri, çözümünüzü veya uygulamanızı isteyen müşterilere talep üzerine Başlatan yönetilen örneklerdir. Bir test sürücü örneği atandıktan sonra, bu müşteri tarafından bir ayarlama dönemi için kullanılabilir. Süre sona erdikten sonra, başka bir müşteri için yer açmak üzere silinir.

Yayımcı olarak, Iş Ortağı Merkezi 'nde test sürücüsü ayarlarını yönetip yapılandırırsınız. Teknik yapılandırma ayrıntıları teklifin türüne göre farklılık gösterir. Ayrıntılı kılavuz için bkz. [sınama sürücüsü teknik yapılandırması](./test-drive-technical-configuration.md).

Potansiyel müşteriler, test sürücünüzü [Appsource](https://appsource.microsoft.com/en-US/)'ta teklifinizin bir CTA olarak bulur. Bunlar, iletişim bilgilerini sağlar ve teklifinizin hüküm ve gizlilik ilkesini kabul edebilir ve ardından sabit bir süre denemek için önceden yapılandırılmış ortamınıza erişin. Müşteriler, ürününüzün temel özelliklerine ve avantajlarına yönelik uygulamalı, kendinden destekli bir deneme alır ve değerli bir müşteri adayı elde edersiniz.

## <a name="types-of-test-drives"></a>Test sürücü türleri

Sunmakta olduğunuz ürün, senaryo ve Market türüne bağlı olarak, select teklifleri için ticari Market 'te bulunan farklı test sürücüleri mevcuttur:

- Azure Resource Manager
    - Azure uygulamaları
    - SaaS
    - Sanal Makineler
- Barındırılan test sürücüsü
    - Iş Merkezi için Dynamics 365 (Şu anda desteklenmiyor)
    - Müşteri Etkileşimi için Dynamics 365
    - Dynamics 365 for Operations
- Logic App (yalnızca destek modunda)
- Power BI

Bu test sürücülerinden birini yapılandırma hakkında ayrıntılı bilgi için bkz. [Test Drive Technical Configuration](./test-drive-technical-configuration.md). 

### <a name="azure-resource-manager-test-drive"></a>Test sürüşü Azure Resource Manager

Bu dağıtım şablonu, çözümünüzü oluşturan tüm Azure kaynaklarını içerir. Bu senaryoya uyan ürünler yalnızca Azure kaynaklarını kullanır. Azure Resource Manager test sürücüsü bu teklif türleri için kullanılabilir: 

- Azure uygulamaları
- SaaS
- Sanal makineler

>[!NOTE]
>Bu, sanal makine ve Azure Uygulama teklifleri için tek sınama sürücüsü seçeneğidir.

### <a name="hosted-test-drive-recommended"></a>Barındırılan test sürücüsü (önerilir)

Barındırılan bir test sürücüsü, Microsoft ana bilgisayarına izin vererek kurulum karmaşıklığını ortadan kaldırır ve test sürücüsü Kullanıcı sağlamasını ve sağlama işlemini gerçekleştiren hizmeti korur. Microsoft AppSource bir teklifiniz varsa, bir Dynamics AX/CRM örneğiyle bağlantı kurmak için test sürücünüzü oluşturun. Aşağıdaki AppSource tekliflerini kullanabilirsiniz:

- Sales, Service, Project Service ve Field hizmeti gibi bir müşteri katılımı sistemi için [müşteri katılımı ve güç uygulamaları Için Dynamics 365](dynamics-365-customer-engage-offer-setup.md) kullanın.
- Finans, işlemler ve üretim, tedarik zinciri gibi finans ve Işlemler kurumsal kaynak planlama sistemine yönelik [işlemler Için Dynamics 365](partner-center-portal/create-new-operations-offer.md) kullanın.

### <a name="logic-app-test-drive"></a>Mantıksal uygulama sınama sürücüsü

Bu tür test sürücüsü Microsoft tarafından barındırılmamaktadır ve Dynamics AX/CRM teklif türleri için Azure Resource Manager (ARM) şablonları kullanır. Azure aboneliğinizde gerekli kaynakları oluşturmak için ARM şablonunu çalıştırmanız gerekir. Logic app test sürücüsü şu anda yalnızca destek modunda ve Logic app test sürücüsü yapılandırma hakkında ayrıntılı bilgi Için bkz. [sınama sürücüsü teknik yapılandırması](./test-drive-technical-configuration.md).

### <a name="power-bi-test-drive"></a>Test sürüşü Power BI

Bu yalnızca özel olarak oluşturulmuş bir panoya eklenmiş bir bağlantıdır. Yalnızca etkileşimli bir Power BI görseli gösteren tüm ürünler bu test sürücüsü türünü kullanmalıdır.

## <a name="transforming-examples"></a>Örnekleri dönüştürme

Bir kaynak mimarisini bir test sürücüsüne çevirme işlemi, korunal olabilir. Geçerli mimarilerin en iyi şekilde dönüştürülmesiyle bu örneklere göz atın.

[Web sitesi şablonunu test sürücüsüne dönüştürme](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Website-Deployment-Template-for-Test-Drive)

[Bir sanal makine şablonunu test sürücüsüne dönüştürme](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive)

[Varolan bir Kaynak Yöneticisi şablonunu test sürücüsüne Dönüştür](https://github.com/Azure/AzureTestDrive/wiki/Deploying-Existing-Solutions)

## <a name="generate-leads-from-your-test-drive"></a>Test sürücünüzden müşteri adayları oluşturma

Ticari Market test sürüşü, pazarlamalara yönelik harika bir araçtır. İşletmeniz için daha fazla müşteri adayı oluşturmaya başladığınızda, bunu pazara sunma çabalarınıza dahil etmenizi öneririz. Ayrıntılı kılavuz için bkz. [ticari Market teklifinizin müşteri adayları](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads.md).

Bir test sürücüsü lideri ile bir anlaşmayı kapatırsanız, [Microsoft Iş ortağı Sales Connect](https://support.microsoft.com/help/3155788/getting-started-with-microsoft-partner-sales-connect)'e kaydettiğinizden emin olun. Ayrıca, bir test sürücüsünün bir rol oynadığı müşterilerin WINS 'niz hakkında bilgi almak için sevdiğimiz bir duyuyoruz.

## <a name="other-resources"></a>Diğer kaynaklar

Ek test sürücüsü kaynakları:

- [Sınama sürücüsü en iyi uygulamaları](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Genel bakış](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF; açılır pencere engelleyicinizin kapalı olduğundan emin olun)

## <a name="next-step"></a>Sonraki adım

- [Test sürüşü teknik yapılandırması](test-drive-technical-configuration.md)