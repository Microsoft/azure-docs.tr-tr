---
title: Azure Marketi için sanal makine teklifi Yayımlama Kılavuzu
description: Bu makalede, Market 'ten dağıtılması için bir sanal makine ve yazılım ücretsiz deneme sürümü yayımlama gereksinimleri açıklanmaktadır.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: ellacroi
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: 0f2047501b6c57b2bb98ff7e3c56498417644324
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818987"
---
# <a name="virtual-machine-offer-publishing-guide"></a>Sanal Makine teklifi Yayımlama Kılavuzu

Sanal makine görüntüleri, bir çözümü Azure Marketi 'nde yayımlamanın ana yöntemlerinden biridir. Bu teklifin gereksinimlerini anlamak için bu kılavuzu kullanın. 

Bunlar Market aracılığıyla dağıtılan ve faturalandırılan işlem teklifleridir. Kullanıcının gördüğü eyleme yapılan çağrı "Şimdi al" dır.

## <a name="free-trial"></a>Ücretsiz Deneme 

Kullanıcıları, kendi lisansını getir (KLG) Faturalandırma modelini kullanırken sınırlı süreli yazılım lisanslarına erişerek teklifinizi test etmek üzere düzenleyebilirsiniz. Bu teklifin dağıtılması için gereksinimler aşağıda verilmiştir. 

|Gereksinimler  |Ayrıntılar  |
|---------|---------|
|Ücretsiz deneme süresi ve deneme deneyimi     |   Müşterileriniz, uygulamanızı sınırlı bir süre için ücretsiz olarak deneyebilir. Müşterileriniz için herhangi bir lisans veya abonelik ücretini ödemek üzere müşterilerinizin olması gerekir. Müşterilerinizin temel alınan Microsoft birinci taraf ürün veya hizmeti için ödeme yapması gerekmez. Tüm deneme seçenekleri Azure aboneliğinize dağıtılır. Maliyet iyileştirmesi ve yönetimi için tek denetiminiz vardır. Ücretsiz bir deneme veya etkileşimli tanıtım seçebilirsiniz. Ne tercih ettiğinize bakılmaksızın ücretsiz deneme süreniz müşterilere ek bir ücret ödemeden teklifinizi denemek için önceden ayarlanmış bir süre vermelidir.|
|Kolayca yapılandırılabilir, kullanıma hazırlamış çözüm    |  Uygulamanızın yapılandırılması ve ayarlanması kolay ve hızlı olmalıdır.       |
|Kullanılabilirlik/çalışma süresi    |    SaaS uygulamanızın veya platformunuzun süresi en az% 99,9 olmalıdır.     |
|Azure Active Directory     |    Teklifinizin Azure Active Directory (Azure AD) Federal çoklu oturum açma (SSO) (Azure AD Federasyon SSO) onayı etkinleştirilmiş olarak izin vermelidir.     |

## <a name="test-drive"></a>Test Sürüşü

Hizmet olarak altyapı (IaaS) veya hizmet olarak yazılım (SaaS) uygulamaları aracılığıyla bir veya daha fazla sanal makine dağıtırsınız. Test sürüşü yayımlama seçeneğinin bir avantajı, bir sanal makinenin otomatik olarak sağlanması veya iş ortağının barındırdığı bir Kılavuzlu tura katılarak tamamen bir çözümdür. Sınama sürücüsü, müşterinize hiçbir ek ücret ödemeden bir değerlendirme sağlar. Müşterinizin deneme deneyimine yönelik mevcut bir Azure müşterisi olması gerekmez. 

Başlamak için [amp-testdrive](mailto:amp-testdrive@microsoft.com) 'da bizimle iletişim kurun. 

|Gereksinimler  |Ayrıntılar |
|---------|---------|
| Market uygulamanız var   |    IaaS veya SaaS aracılığıyla bir veya daha fazla sanal makine.      |

## <a name="interactive-demo"></a>Etkileşimli Tanıtım

Etkileşimli bir tanıtım kullanarak müşterilerinizin çözümüne yönelik kılavuzlu bir deneyim sağlarsınız. Etkileşimli Tanıtım yayımlama seçeneğinin avantajı, karmaşık çözümünüzün karmaşık bir şekilde sağlanması gerekmeden bir deneme deneyimi sağlamaktır. 

## <a name="virtual-machine-offer"></a>Sanal Makine teklifi

Müşterinizin ilişkili aboneliğine bir Sanal Gereç dağıtırken sanal makine teklif türünü kullanın. VM 'Ler, Kullandıkça Öde veya kendi lisansını getir (KLG) lisanslama modelleri kullanılarak tamamen ticari olarak etkinleştirilmiştir. Microsoft, ticari işlemi barındırır ve müşterinizin adına fatura alır. Müşteri ile Microsoft arasında, kurumsal sözleşmeler dahil tercih edilen ödeme ilişkilerini kullanmanın avantajını elde edersiniz.

> [!NOTE]
> Şu anda, bir Kurumsal Anlaşma ilişkili parasal taahhütler, VM 'nizin Azure kullanımında kullanılabilir, ancak yazılım lisans ücretlerine karşı kullanılamaz.  
> 
> [!NOTE]
> Görüntüyü ve fiyatlandırmayı özel bir teklif olarak yayımlayarak, sanal makinenizin bulma ve dağıtımını belirli bir müşteri kümesiyle kısıtlayabilirsiniz. Özel teklifler, en yakın müşterileriniz için özel teklifler oluşturma ve özelleştirilmiş yazılım ve koşulları sunma imkanını açar. Özelleştirilmiş terimler, özel fiyatlandırma ve koşullara sahip alan ve sınırlı yayın yazılımına erken erişim dahil olmak üzere çeşitli senaryolar vurgulamanızı sağlar. Özel teklifler, bu ayrıntılarla yeni bir SKU oluşturarak belirli fiyatlandırma veya ürünleri sınırlı bir müşteri kümesine vermenizi sağlar.  
> *   Özel teklifler hakkında daha fazla bilgi için, [Azure.Microsoft.com/blog/Private-offers-on-Azure-Marketplace](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace)adresinde bulunan Azure Marketi 'Ndeki özel teklifleri ziyaret edin.  

| Gereksinim | Ayrıntılar |  
|:--- |:--- | 
| Faturalandırma ve ölçüm | SANAL makinenizin KLG veya Kullandıkça Öde aylık faturalandırmasını desteklemesi gerekir. |  
| Azure ile uyumlu sanal sabit disk (VHD) | VM 'Ler Windows veya Linux üzerinde oluşturulmalıdır. <ul> <li>Linux VHD oluşturma hakkında daha fazla bilgi için bkz. [Azure 'da desteklenen Linux dağıtımları](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Windows VHD oluşturma hakkında daha fazla bilgi için bkz. [Azure ile uyumlu bır VHD oluşturma](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |  

>[!Note]
>Bulut çözümü sağlayıcıları (CSP) iş ortağı kanalı kabul etme artık kullanılabilir.  Teklifinizi Microsoft CSP iş ortağı kanalları aracılığıyla pazarlama hakkında daha fazla bilgi için lütfen bkz. [bulut çözümü sağlayıcıları](./cloud-solution-providers.md) .

## <a name="next-steps"></a>Sonraki adımlar

Daha önce yapmadıysanız, 

- Market 'e [kaydolun](https://azuremarketplace.microsoft.com/sell) .

Kaydolduysanız ve yeni bir teklif oluşturuyor ya da mevcut bir teklif üzerinde çalışıyorsanız,

- Teklifinizin oluşturulması veya tamamlanabilmesi için [bulut iş ortağı portalı oturum açın](https://cloudpartner.azure.com) .
- Daha fazla bilgi için bkz. [Sanal Makine teklifi](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-virtual-machine-offer) .
