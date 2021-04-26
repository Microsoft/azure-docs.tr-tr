---
title: Azure Uygulama teklifi için Azure yönetilen uygulaması planlayın
description: Microsoft Iş Ortağı Merkezi 'nde ticari Market portalını kullanarak yeni bir Azure uygulaması teklifi için yönetilen uygulama planı oluşturmak için gerekenleri öğrenin.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 694f501efc565ed498c1c8d8e2e38326277e8605
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96621425"
---
# <a name="plan-an-azure-managed-application-for-an-azure-application-offer"></a>Azure Uygulama teklifi için Azure yönetilen uygulaması planlayın

Azure _yönetilen uygulama_ planı, Azure Market 'te Azure uygulaması teklifi yayımlamanın bir yoludur. Daha önce yapmadıysanız, [ticari Market için bir Azure Uygulama teklifi planı](plan-azure-application-offer.md)makalesini okuyun.

Yönetilen uygulamalar, Azure Marketi aracılığıyla dağıtılan ve faturalandırılan Transact teklifleridir. Kullanıcının gördüğü listeleme seçeneği şimdi alın.

Azure uygulaması kullanın: aşağıdaki koşullar gerektiğinde yönetilen uygulama planı:

- Bir sanal makine (VM) ya da hizmet olarak altyapı (IaaS) tabanlı bir çözüm kullanarak müşteriniz için abonelik tabanlı bir çözüm dağıtırsınız.
- Siz veya müşteriniz çözümün bir iş ortağı tarafından yönetilmesini gerektirir. Örneğin, bir iş ortağı bir sistem tümleştirici veya yönetilen hizmet sağlayıcısı (MSP) olabilir.

## <a name="managed-application-offer-requirements"></a>Yönetilen uygulama teklifi gereksinimleri

| Gereksinimler | Ayrıntılar |
| ------------ | ------------- |
| Bir Azure aboneliği | Yönetilen uygulamalar bir müşterinin aboneliğine dağıtılmalıdır, ancak üçüncü taraf tarafından yönetilebilir. |
| Faturalandırma ve ölçüm | Kaynaklar bir müşterinin Azure aboneliğinde sağlanır. Kullandıkça Öde ödeme modelini kullanan VM 'Ler, müşteri ile Microsoft aracılığıyla işlem yapar ve müşterinin Azure aboneliği aracılığıyla faturalandırılır. <br><br> Kendi lisansını getir VM 'Leri için, Microsoft, müşteri aboneliğinde oluşan tüm altyapı maliyetlerini faturalandırır, ancak müşteriyle doğrudan Transact yazılım lisanslama ücretleri. |
| Azure ile uyumlu sanal sabit disk (VHD) | VM 'Ler Windows veya Linux üzerinde oluşturulmalıdır. Daha fazla bilgi için bkz.<br> • [Bir Azure VM teknik varlığı oluşturun](./azure-vm-create-certification-faq.md#address-a-vulnerability-or-an-exploit-in-a-vm-offer) (Windows VHD 'ler için).<br> •  [Linux dağıtımları Azure 'da](../virtual-machines/linux/endorsed-distros.md) (Linux VHD 'ler için) onaylı. |
| Müşteri kullanımı ilişkilendirmesi | Tüm yeni Azure Uygulama teklifleri Ayrıca bir [Azure iş ortağı müşteri kullanımı attributıon](azure-partner-customer-usage-attribution.md) GUID 'i de içermelidir. Müşteri kullanımı atımı ve nasıl etkinleştirileceği hakkında daha fazla bilgi için bkz. [Azure iş ortağı müşteri kullanımı atısyonu](azure-partner-customer-usage-attribution.md). |
| Dağıtım paketi | Müşterilerin planınızı dağıtmasını sağlayacak bir dağıtım paketi gerekir. Aynı teknik yapılandırmayı gerektiren birden çok plan oluşturuyorsanız aynı paketi kullanabilirsiniz. Ayrıntılar için bkz. sonraki bölüm: dağıtım paketi. |
|||

> [!NOTE]
> Yönetilen uygulamaların Azure Marketi aracılığıyla dağıtılabilir olması gerekir. Müşteri iletişimi sorun oluşturacaksa, müşteri adayı paylaşımını etkinleştirdikten sonra ilgilenen müşterilere ulaşın.

## <a name="deployment-package"></a>Dağıtım paketi

Dağıtım paketi, bu plan için gereken tüm şablon dosyalarını ve aynı zamanda. zip dosyası olarak paketlenmiş ek kaynakları içerir.

Tüm Azure uygulamalarının bu iki dosyayı bir. zip arşivi kök klasörüne eklemesi gerekir:

- [mainTemplate.js](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template)adlı kaynak yöneticisi şablon dosyası. Bu şablon, müşterinin Azure aboneliğine dağıtılacak kaynakları tanımlar. Kaynak Yöneticisi şablonlarının örnekleri için bkz. [Azure hızlı başlangıç şablonları Galerisi](https://azure.microsoft.com/documentation/templates/) veya ilgili [GitHub: Azure Resource Manager hızlı başlangıç şablonları](https://github.com/azure/azure-quickstart-templates) deposu.
- Azure uygulama oluşturma deneyimi için [createUiDefinition.js](../azure-resource-manager/managed-applications/create-uidefinition-overview.md)adlı bir kullanıcı arabirimi tanımı. Kullanıcı arayüzünde tüketicilerin parametre değerleri sağlamasına olanak tanıyan öğeleri belirlersiniz.

Desteklenen en büyük dosya boyutu:

- Toplam sıkıştırılmış. zip arşiv boyutu olan 1 GB 'a kadar
- . Zip arşivi içindeki her bir sıkıştırılmamış dosya için en fazla 1 GB

Tüm yeni Azure Uygulama teklifleri Ayrıca bir [Azure iş ortağı müşteri kullanımı attributıon](azure-partner-customer-usage-attribution.md) GUID 'i de içermelidir.

## <a name="azure-regions"></a>Azure bölgeleri

Planınızı Azure ortak bölgesi, Azure Kamu bölgesi veya her ikisine de yayımlayabilirsiniz. [Azure Kamu](../azure-government/documentation-government-manage-marketplace-partners.md)'da yayımlamadan önce, belirli uç noktalar farklı olabileceğinden planınızı ortamda test edin ve doğrulayın. Planınızı ayarlamak ve test etmek için [Microsoft Azure Kamu deneme](https://azure.microsoft.com/global-infrastructure/government/request/)sürümünden bir deneme hesabı isteyin.

Yayımcı olarak tüm uyumluluk denetimleri, güvenlik ölçüleri ve en iyi uygulamalardan sorumludur. Azure Kamu fiziksel olarak yalıtılmış veri merkezleri ve ağlar (yalnızca ABD 'de bulunur) kullanır.

Ticari Market tarafından desteklenen ülkelerin ve bölgelerin listesi için bkz. [coğrafi kullanılabilirlik ve para birimi desteği](marketplace-geo-availability-currencies.md).

Azure Kamu Hizmetleri, belirli kamu düzenlemelerine ve gereksinimlerine tabi olan verileri işler. Örneğin, Fedrampa, NıST 800,171 (DIB), ıTAR, ıRS 1075, DoD L4 ve CJıS. Bu programlara yönelik sertifikalarınıza yönelik olarak bir açıklama getirmek için, bunları tanımlayan 100 'e kadar bağlantı sağlayabilirsiniz. Bunlar, program üzerinde doğrudan listelemesine bağlantılar ya da kendi Web sitelerinizde uyumluluğun açıklamalarını bağlar olabilir. Bu bağlantılar yalnızca Azure Kamu müşterilerine görünür.

## <a name="choose-who-can-see-your-plan"></a>Planınızı kimlerin görebileceğini seçin

Her planı herkese (genel) veya yalnızca belirli bir hedef kitleye (özel) görünür olacak şekilde yapılandırabilirsiniz. En fazla 100 plan oluşturabilirsiniz ve bunların en fazla 45 ' e kadar özel olabilir. Belirli müşterilere farklı fiyatlandırma seçenekleri veya teknik konfigürasyonlar sunmak için özel bir plan oluşturmak isteyebilirsiniz.

Azure abonelik kimliklerini kullanarak, atadığınız her abonelik KIMLIĞININ açıklamasını ekleme seçeneğiyle bir özel plana erişim verirsiniz. Bir kullanarak en fazla 10 abonelik kimliği el ile veya 10.000 abonelik kimliği ekleyebilirsiniz. CSV dosyası. Azure abonelik kimlikleri, GUID 'Ler olarak temsil edilir ve harflerin küçük harf olması gerekir.

Özel planlar, bulut çözümü sağlayıcısı programı 'nın (CSP) satıcısı aracılığıyla oluşturulan Azure abonelikleri ile desteklenmez. Daha fazla bilgi için bkz. [Microsoft ticari Market 'Teki özel teklifler](private-offers.md).

> [!NOTE]
> Özel bir plan yayımlarsanız, görünürlüğünü daha sonra herkese açık olarak değiştirebilirsiniz. Ancak, bir genel planı yayımladıktan sonra görünürlüğünü özel olarak değiştiremezsiniz.

## <a name="define-pricing"></a>Fiyatlandırma tanımla

Her plan için aylık fiyat sağlamanız gerekir. Bu fiyat, bu çözüm tarafından dağıtılan kaynaklar tarafından tahakkuk eden tüm Azure altyapısına veya kullandıkça öde yazılım maliyetlerine ek olarak yapılır.

Aylık fiyata ek olarak, [ölçülen faturalandırmayı](partner-center-portal/azure-app-metered-billing.md)kullanarak standart olmayan birimlerin tüketimine yönelik fiyatlar da ayarlayabilirsiniz. Aylık fiyat fiyatını sıfır olarak ayarlayabilir ve isterseniz tarifeli faturalandırma kullanarak özel olarak ücretlendirme yapabilirsiniz.

Fiyatlar ABD Doları cinsinden ayarlanır (USD = Birleşik Devletler dolar), kaydedildiğinde geçerli döviz kurları kullanılarak tüm seçili piyasaların yerel para birimine dönüştürülür. Ancak, her bir pazar için müşteri fiyatlarını ayarlamayı tercih edebilirsiniz.

## <a name="just-in-time-jit-access"></a>Tam zamanında (JıT) erişim

JıT erişimi, sorun giderme ve bakım için yönetilen bir uygulamanın kaynaklarına yükseltilmiş erişim isteme imkanı sağlar. Kaynaklara her zaman salt okuma erişiminizin olması gerekir, ancak belirli bir süre için daha fazla erişime sahip olabilirsiniz. Daha fazla bilgi için bkz. [Azure yönetilen uygulamalar için tam zamanında erişimi etkinleştirme ve isteme](../azure-resource-manager/managed-applications/request-just-in-time-access.md).

> [!NOTE]
> `createUiDefinition.json`Bu özelliği desteklemek için dosyanızı güncelleştirdiğinizden emin olun.

## <a name="deployment-mode"></a>Dağıtım modu

Yönetilen bir uygulama planını, **tamamlanmış** veya **artımlı** Dağıtım modunu kullanacak şekilde yapılandırabilirsiniz. Tüm modda, kaynak [ üzerindemainTemplate.js](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template)tanımlanmamışsa, uygulamanın müşterinin yeniden dağıtımı yönetilen kaynak grubundaki kaynakların kaldırılmasına neden olur. Artımlı modda, uygulamanın yeniden dağıtımı mevcut kaynakları değişmeden bırakır. Daha fazla bilgi için bkz. [Azure Resource Manager Dağıtım modları](../azure-resource-manager/templates/deployment-modes.md).

## <a name="notification-endpoint-url"></a>Bildirim uç noktası URL 'SI

İsteğe bağlı olarak, bir planın yönetilen uygulama örneklerine ilişkin tüm CRUD işlemleri hakkında bildirim almak için bir HTTPS Web kancası uç noktası sağlayabilirsiniz.

## <a name="customize-allowed-customer-actions-optional"></a>İzin verilen müşteri eylemlerini özelleştirme (isteğe bağlı)

İsteğe bağlı olarak, `*/read` Varsayılan olarak kullanılabilir olan eylemlere ek olarak, müşterilerin yönetilen kaynaklarda gerçekleştirebileceği eylemleri belirtebilirsiniz.

Bu seçeneği belirlerseniz, denetim eylemlerini veya izin verilen veri eylemlerini ya da her ikisini de belirtmeniz gerekir. Daha fazla bilgi için bkz. [Azure kaynakları için reddedilen atamaları anlama](../role-based-access-control/deny-assignments.md). Kullanılabilir eylemler için bkz. [Azure Resource Manager kaynak sağlayıcısı işlemleri](../role-based-access-control/resource-provider-operations.md). Örneğin, tüketicilerin sanal makineleri yeniden başlatmasına izin vermek için `Microsoft.Compute/virtualMachines/restart/action` izin verilen eylemlere ekleyin.

## <a name="choose-who-can-manage-the-application"></a>Uygulamayı kimin yönetebileceğini seçin

Seçili bulutların her birinde yönetilen bir uygulamayı kimin yönetebileceğini belirtmeniz gerekir: _Genel Azure_ ve _Azure Kamu Bulutu_. Aşağıdaki bilgileri toplayın:

- **Azure Active Directory KIRACı kimliği** : izin vermek istediğiniz kullanıcıların, grupların veya uygulamaların kimliklerini IÇEREN Azure AD Kiracı kimliği (dizin kimliği olarak da bilinir). Azure portal [Azure Active Directory özelliklerinde](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)Azure AD kiracı kimliğinizi bulabilirsiniz.
- **Yetkilendirmeler** – yönetilen kaynak grubuna izin verilmesini istediğiniz her bir Kullanıcı, Grup veya uygulamanın Azure ACTIVE DIRECTORY nesne kimliğini ekleyin. Kullanıcıyı, [Azure portal Azure Active Directory Kullanıcıları dikey penceresinde](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)bulunan birincil kimliğine göre belirler.

Her bir asıl KIMLIK için, Azure AD yerleşik rollerinin birini (sahibi veya katkıda bulunan) ilişkilendirebilirsiniz. Seçtiğiniz rol, sorumlunun müşteri aboneliğindeki kaynaklarda sahip olacağı izinleri açıklar. Daha fazla bilgi için bkz. [Azure yerleşik rolleri](../role-based-access-control/built-in-roles.md). Rol tabanlı erişim denetimi (RBAC) hakkında daha fazla bilgi için bkz. [Azure Portal RBAC ile çalışmaya başlama](../role-based-access-control/overview.md).

> [!NOTE]
> Azure bölgesi başına 100 yetkilendirmeler ekleyebilseniz de genellikle daha kolay bir Active Directory Kullanıcı grubu oluşturmak ve KIMLIĞINI "asıl KIMLIK" içinde belirtmek daha kolaydır. Bu, plan dağıtıldıktan sonra yönetim grubuna daha fazla kullanıcı eklemenizi sağlar ve daha fazla yetkilendirmeler eklemek için planı güncelleştirme ihtiyacını azaltır.

## <a name="policy-settings"></a>İlke ayarları

Dağıtılan çözüme yönelik uyumluluk gereksinimlerini belirtmek için, yönetilen uygulamanıza [Azure ilkeleri](../governance/policy/index.yml) uygulayabilirsiniz. İlke tanımları ve parametre değerlerinin biçimi için bkz. [Azure İlke Örnekleri](../governance/policy/samples/index.md).

En fazla beş ilke ve her Ilke türünün yalnızca bir örneğini yapılandırabilirsiniz. Bazı ilke türleri ek parametreler gerektirir.

| İlke türü | İlke parametreleri gerekli |
| ------------ | ------------- |
| Azure SQL veritabanı şifrelemesi | No |
| Azure SQL Server denetim ayarları | Yes |
| Azure Data Lake Store şifreleme | No |
| Tanılama ayarını denetle | Yes |
| Kaynak konumu uyumluluğunu denetleme | No |
|||

Eklediğiniz her ilke türü için standart veya ücretsiz Ilke SKU 'sunu ilişkilendirmeniz gerekir. Denetim ilkeleri için standart SKU gereklidir. İlke adları 50 karakterle sınırlıdır.

## <a name="next-steps"></a>Sonraki adımlar

- [Ticari Market 'te Azure uygulaması teklifi oluşturma](create-new-azure-apps-offer.md)
