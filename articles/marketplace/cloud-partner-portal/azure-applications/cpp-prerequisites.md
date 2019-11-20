---
title: Azure Uygulama teklifi önkoşulları | Azure Marketi
description: Azure Market 'te Azure uygulaması teklifi yayımlama önkoşulları.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: 261af94e233bdb6189d7819f8f28c7e23b5dd112
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826137"
---
# <a name="azure-application-prerequisites"></a>Azure uygulama önkoşulları

Bu makalede, Azure Marketi 'nde yönetilen bir uygulama teklifi yayımlamaya yönelik teknik ve iş önkoşulları açıklanmaktadır.  Daha önce yapmadıysanız, aşağıdaki bilgi kaynaklarını gözden geçirin:
- SKU türüne bağlı olarak, [Azure uygulamaları: çözüm şablonu teklif yayımlama Kılavuzu](../../marketplace-solution-templates.md) ya da [Azure uygulamaları: yönetilen uygulama teklifi Yayımlama Kılavuzu](../../marketplace-managed-apps.md)
- [Azure Marketi videosu Için çözüm şablonları ve yönetilen uygulamalar oluşturma](https://channel9.msdn.com/Events/Build/2018/BRK3603)


## <a name="technical-requirements"></a>Teknik gereksinimler

Teknik gereksinimler aşağıdaki öğeleri içerir:

*   Azure Resource Manager şablonlar daha fazla bilgi Için bkz. [Azure Resource Manager şablonlarının yapısını ve sözdizimini anlama](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Bu makalede bir Azure Resource Manager şablonunun yapısı açıklanır. Bir şablonun farklı bölümlerini ve bu bölümlerde kullanılabilen özellikleri gösterir. Şablon, dağıtımınız için değer oluşturmak üzere kullanabileceğiniz JSON ve ifadelerden oluşur. 
* Azure hızlı başlangıç şablonları.<br> Daha fazla bilgi için bkz.

  * [Azure hızlı başlangıç şablonları](https://azure.microsoft.com/documentation/templates/). Daha fazlasını gerçekleştirmek için topluluk tarafından sağlanan kaynaklarla Azure Resource Manager üzerinden Azure kaynaklarını dağıtın. Azure Resource Manager, uygulamalarınızı bildirim temelli bir şablon aracılığıyla sağlamanıza olanak tanır. Tek bir şablonda birden çok hizmeti bağımlılıklarıyla birlikte dağıtabilirsiniz. Uygulama yaşam döngüsünün her aşamasında uygulamanızı tekrar tekrar dağıtmak için aynı şablonu kullanırsınız.
  * [GitHub: Azure Resource Manager hızlı başlangıç şablonları](https://github.com/azure/azure-quickstart-templates). Bu depo, topluluk tarafından katkıda bulunulan Azure Resource Manager mevcut olan tüm şablonları içerir. Aranabilir bir şablon dizini https://azure.microsoft.com/documentation/templates/olarak korunur.
* UI tanımı oluştur<br>
Daha fazla bilgi için bkz. [Yönetilen uygulamanız için Azure Portal Kullanıcı arabirimi oluşturma](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview). Bu makalede Createuıdefinition. json dosyasının temel kavramları tanıtılmaktadır. Azure portal, yönetilen bir uygulama oluşturmak için Kullanıcı arabirimini oluşturmak üzere bu dosyayı kullanır.


## <a name="business-requirements"></a>İş gereksinimleri

İş gereksinimleri aşağıdaki yordamsal, sözleşmeli ve yasal yükümlülükleri içerir:

* Kayıtlı bir bulut marketi yayımcısı olmanız gerekir. Kaydolmadıysanız, bu makaledeki adımları izleyerek [bir bulut marketi yayımcısı haline gelir](https://docs.microsoft.com/azure/marketplace/become-publisher
).

>[!NOTE]
>Bulut İş Ortağı Portalı oturum açmak için aynı Microsoft Geliştirici Merkezi kayıt hesabını kullanmanız gerekir. Azure Market teklifleriniz için yalnızca bir Microsoft hesabı olmalıdır. Bu hesabın ayrı hizmetlere veya tekliflere özgü olmaması gerekir.

* Şirketiniz (veya yan kuruluşu), Azure Marketi tarafından desteklenen bir satış-ülke/bölge içinde olmalıdır. Bu ülkelerin/bölgelerin geçerli listesi için bkz. [katılım ilkeleri Microsoft Azure Market](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
* Ürününüz, Azure Marketi tarafından desteklenen faturalandırma modelleriyle uyumlu olacak şekilde lisanslanmalıdır. Daha fazla bilgi için bkz. Azure Marketi 'ndeki [faturalandırma seçenekleri](https://docs.microsoft.com/azure/marketplace/marketplace-commercial-transaction-capabilities-and-considerations) .
* Müşterilerin ticari açıdan makul bir şekilde kullanılabilmesi için teknik destek hazırlıyoruz. Bu destek ücretsiz, ücretli veya topluluk yaklaşımları aracılığıyla yapılabilir.
* Yazılımınızın ve üçüncü taraf yazılım bağımlılıklarının lisanslanması sizin sorumluluğunuzdadır.
* Teklifinizin Azure Marketi 'nde ve Azure portal listelenmesi için ölçütlere uyan içerikleri sağlamanız gerekir.
* Microsoft Azure Market katılım Ilkelerinin ve yayımcı sözleşmesinin koşullarını kabul etmeniz gerekir.
* Microsoft Azure Web sitesi kullanım koşulları, Microsoft gizlilik bildirimi ve Microsoft Azure Sertifikalı program sözleşmesi ile uymanız gerekir.


## <a name="publishing-requirements"></a>Yayımlama gereksinimleri

Yeni bir Azure uygulaması teklifi yayımlamak için aşağıdaki önkoşulları karşılamanız gerekir:

* Meta verilerizin kullanıma hazırlanıyor. Aşağıdaki liste (ayrıntılı olmayan) Bu meta verilerin bir örneğini gösterir:
  * Bir başlık
  * Bir açıklama (HTML biçiminde)
  * Bir logo resmi (PNG biçiminde) ve bu sabit resim boyutları: 40 x 40 piksel, 90 x 90 piksel, 115 x 115 piksel ve 255 x 115 piksel.
* *Kullanım koşulları* ve *Gizlilik ilkesi* belgeleri
* Uygulama belgeleri
* Destek kişileri


## <a name="next-steps"></a>Sonraki adımlar

Tüm gereksinimleri karşıladıktan sonra, [bir Azure Uygulama teklifi oluşturmaya](./cpp-create-offer.md)hazırlanın. 
 
