---
title: Azure Uygulama teklifi için bir çözüm şablonu planlayın
description: Microsoft Iş Ortağı Merkezi 'nde ticari Market portalını kullanarak yeni bir Azure uygulaması teklifi için bir çözüm şablonu planı oluşturmak için gerekenleri öğrenin.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: aab856b1e1d2d991cc4964d061a990dbedbeddb7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98876520"
---
# <a name="plan-a-solution-template-for-an-azure-application-offer"></a>Azure Uygulama teklifi için bir çözüm şablonu planlayın

Bu makalede, bir Azure Uygulama teklifi için çözüm şablonu planı yayımlama gereksinimleri açıklanmaktadır. Çözüm şablonu planı, Azure Uygulama teklifleri tarafından desteklenen iki plan türünden biridir. Bu iki plan türü arasındaki fark hakkında daha fazla bilgi için bkz. [plan türleri](plan-azure-application-offer.md#plans). Daha önce yapmadıysanız, [bir Azure Uygulama teklifi planı](plan-azure-application-offer.md)makalesini okuyun.

Çözüm altyapısı plan türü, çözüm altyapınızı otomatik olarak dağıtmak için bir [Azure Resource Manager şablonu (ARM şablonu)](../azure-resource-manager/templates/overview.md) gerektirir.

## <a name="solution-template-requirements"></a>Çözüm şablonu gereksinimleri

| Gereksinimler | Ayrıntılar |
| ------------ | ------------- |
| Faturalandırma ve ölçüm | Çözüm şablonu planları transactable değildir, ancak Microsoft ticari Market aracılığıyla faturalandırılan ücretli VM tekliflerini dağıtmak için kullanılabilirler. Çözümün ARM şablonunun dağıttığı kaynaklar müşterinin Azure aboneliğinde ayarlanır. Kullandıkça Öde sanal makineleri, müşteri ile Microsoft aracılığıyla gerçekleştirilir ve müşterinin Azure aboneliği aracılığıyla faturalandırılır. <br><br> Kendi lisansını getir (KLG) faturanızı, Microsoft 'un müşteri aboneliğinde tahakkuk eden altyapı maliyetleri olmasına karşın, yazılım lisans ücretlerinizi müşteriyle doğrudan Transact. |
| Azure ile uyumlu sanal sabit disk (VHD) | VM 'Ler Windows veya Linux üzerinde oluşturulmalıdır. Daha fazla bilgi için bkz.<ul><li>[Azure VM teknik varlığı oluşturma](./azure-vm-create-certification-faq.md#address-a-vulnerability-or-an-exploit-in-a-vm-offer) (Windows VHD 'ler için)</li><li>[Azure 'da desteklenen Linux dağıtımları](../virtual-machines/linux/endorsed-distros.md) (Linux VHD 'ler için).</li></ul> |
| Müşteri kullanımı ilişkilendirmesi | Azure Market 'te yayımlanan tüm çözüm şablonlarında müşteri kullanım attributıon özelliğinin etkinleştirilmesi gerekir. Müşteri kullanımı atımı ve nasıl etkinleştirileceği hakkında daha fazla bilgi için bkz. [Azure iş ortağı müşteri kullanımı atısyonu](azure-partner-customer-usage-attribution.md). |
| Yönetilen diskleri kullanma | [Yönetilen diskler](../virtual-machines/managed-disks-overview.md) , Azure 'da hizmet olarak altyapı (IaaS) VM 'lerinin kalıcı diskleri için varsayılan seçenektir. Çözüm şablonlarında yönetilen diskleri kullanmanız gerekir.<ul><li>Çözüm şablonlarınızı güncelleştirmek için [Azure Resource Manager şablonlarda yönetilen diskleri kullanma](../virtual-machines/using-managed-disks-template-deployments.md)bölümündeki yönergeleri izleyin ve sağlanan [örnekleri](https://github.com/Azure/azure-quickstart-templates)kullanın.</li><li>VHD 'YI Azure Marketi 'nde bir görüntü olarak yayımlamak için, yönetilen disklerin temel VHD 'sini [Azure PowerShell](/previous-versions/azure/virtual-machines/scripts/virtual-machines-powershell-sample-copy-managed-disks-vhd) veya [Azure CLI](/previous-versions/azure/virtual-machines/scripts/virtual-machines-cli-sample-copy-managed-disks-vhd) kullanarak bir depolama hesabına aktarın.</ul> |
| Dağıtım paketi | Müşterilerin planınızı dağıtmasını sağlayacak bir dağıtım paketi gerekir. Aynı teknik yapılandırmayı gerektiren birden çok plan oluşturursanız, aynı plan paketini kullanabilirsiniz. Ayrıntılar için bkz. sonraki bölüm: dağıtım paketi. |
|||

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

> [!NOTE]
> Özel bir plan yayımlarsanız, görünürlüğünü daha sonra herkese açık olarak değiştirebilirsiniz. Ancak, bir genel planı yayımladıktan sonra görünürlüğünü özel olarak değiştiremezsiniz.

Çözüm şablonu planları için, planı Azure Marketi 'nden gizlemeyi de seçebilirsiniz. Planı yalnızca başka bir çözüm şablonu veya yönetilen uygulama aracılığıyla dolaylı olarak dağıtılırsa bunu yapmak isteyebilirsiniz.

> [!NOTE]
> Özel planlar, bulut çözümü sağlayıcısı programı 'nın (CSP) satıcısı aracılığıyla oluşturulan Azure abonelikleri ile desteklenmez.

Daha fazla bilgi için bkz. [Microsoft ticari Market 'Teki özel teklifler](private-offers.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Ticari Market 'te Azure uygulaması teklifi oluşturma](create-new-azure-apps-offer.md)