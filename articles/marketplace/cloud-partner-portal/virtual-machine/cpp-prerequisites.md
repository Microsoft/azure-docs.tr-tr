---
title: Microsoft Azure için sanal makine önkoşulları | Azure Marketi
description: Azure Marketi 'ne bir VM teklifi yayımlamak için gereken önkoşulların listesi.
services: Azure, Marketplace, Cloud Partner Portal
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: a657ceaacf3680de54dc0d639a3f1a0aff6a6a03
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824433"
---
# <a name="virtual-machine-prerequisites"></a>Sanal makine önkoşulları

Bu makalede, [Azure Marketi](https://azuremarketplace.microsoft.com/marketplace/)'NE bir VM teklifi yayımlamadan önce karşılamanız gereken teknik ve iş gereksinimleri listelenmektedir.  Daha önce yapmadıysanız, [Sanal Makine teklifi yayımlama kılavuzunu](../../marketplace-virtual-machines.md)gözden geçirin.


## <a name="technical-requirements"></a>Teknik gereksinimler

Bir sanal makine (VM) çözümünü yayımlamaya yönelik teknik Önkoşullar basittir:

- Etkin bir Azure hesabınız olmalıdır. Bir tane yoksa [Microsoft Azure sitesinde](https://azure.microsoft.com)kaydolabilirsiniz.  
- Windows ya da Linux VM geliştirmesini desteklemek için yapılandırılmış bir ortamınız olmalıdır.  Daha fazla bilgi için bkz. ilişkili VM belgeleri sitesi:
    - [Linux VM belgeleri](https://docs.microsoft.com/azure/virtual-machines/linux/)
    - [Windows VM 'Leri belgeleri](https://docs.microsoft.com/azure/virtual-machines/windows/)


## <a name="business-requirements"></a>İş gereksinimleri

İş gereksinimleri, yordamsal, sözleşmeli ve yasal yükümlülükleri içerir: 

<!-- TD: Aren't most of these business requirements common to all AMP offerings?  If yes, then move to higher level, perhaps to the AMP section "Become a Cloud Marketplace Publisher" -->
<!-- TD: Need references for remaining docs/business reqs!-->

- Kayıtlı bir bulut marketi yayımcısı olmanız gerekir.  Henüz kaydolmadıysanız, bu makaledeki adımları izleyerek [bir Cloud Market yayımcısı haline gelir](https://docs.microsoft.com/azure/marketplace/become-publisher).

    > [!NOTE]
    > [Bulut iş ortağı portalı](https://cloudpartner.azure.com)oturum açmak Için aynı Microsoft Geliştirici Merkezi kayıt hesabını kullanmanız gerekir.
    > Azure Market teklifleriniz için yalnızca bir Microsoft hesabı olmalıdır. Bireysel hizmetlere veya tekliflere özgü olmamalıdır.
    
- Şirketiniz (veya yan kuruluşu), Azure Marketi tarafından desteklenen bir satış-ülke/bölge içinde bulunmalıdır.  Bu ülkelerin/bölgelerin geçerli listesi için bkz. [katılım ilkeleri Microsoft Azure Market](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
- Ürününüz, Azure Marketi tarafından desteklenen faturalandırma modelleriyle uyumlu olacak şekilde lisanslanmalıdır.  Daha fazla bilgi için bkz. [Azure Marketi 'Ndeki faturalandırma seçenekleri](https://docs.microsoft.com/azure/marketplace/billing-options-azure-marketplace). 
- Müşterilerin ticari açıdan makul bir şekilde kullanılabilmesi için teknik destek sağlamaktan siz sorumlusunuz. Bu destek ücretsiz, ücretli veya topluluk yaklaşımları aracılığıyla yapılabilir.
- Yazılımınızın ve üçüncü taraf yazılım bağımlılıklarının lisanslanması sizin sorumluluğunuzdadır.
- Teklifinizin Azure Marketi 'nde ve Azure portal listelenmesi için ölçütlere uyan içerikleri sağlamanız gerekir. <!-- TD: Meaning/links? -->
- [Microsoft Azure Market katılım ilkelerinin](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) ve yayımcı sözleşmesinin koşullarını kabul etmeniz gerekir.
- [Microsoft Azure Web sitesi kullanım koşulları](https://azure.microsoft.com/support/legal/website-terms-of-use/), [Microsoft gizlilik bildirimi](https://privacy.microsoft.com/privacystatement)ve [Microsoft Azure Sertifikalı program sözleşmesi](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/)ile uymanız gerekir.


## <a name="next-steps"></a>Sonraki adımlar

Bu önkoşulları karşıladıktan sonra [sanal makine teklifinizi oluşturabilirsiniz](./cpp-create-offer.md).
