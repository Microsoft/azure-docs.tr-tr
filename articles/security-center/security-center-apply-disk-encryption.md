---
title: Azure Güvenlik Merkezi 'nde disk şifrelemeyi uygulama | Microsoft Docs
description: Bu belgede, Azure Güvenlik Merkezi önerisi ' nin **disk şifrelemesini**nasıl uygulayacaksınız.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6cc7824a-8d6b-4a5f-ab40-e3bbaebc4a91
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: aa4e8f165320327788255605470bda789047c347
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72592488"
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>Azure Güvenlik Merkezi 'nde disk şifrelemeyi uygulama
Azure Güvenlik Merkezi, Azure disk şifrelemesi kullanılarak şifrelenmeyen Windows veya Linux VM disklerinizde bulunan disk şifrelemeyi uygulamanızı önerir. Disk şifrelemesi, Windows ve Linux IaaS VM disklerini şifrelemenize olanak tanır.  Şifreleme hem işletim sistemi hem de VM’nizin üzerindeki veri birimleri için önerilir.

Disk şifrelemesi Windows 'un sektör standardı [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) özelliğini ve Linux 'un [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) özelliğini kullanır. Bu özellikler, verilerinizi korumaya ve korumaya yardımcı olmak ve kurumsal güvenlik ve uyumluluk taahhütlerinizi karşılamak için işletim sistemi ve veri şifrelemesi sağlar. Disk şifrelemesi, Key Vault aboneliğinizdeki disk şifreleme anahtarlarını ve gizli dizileri denetlemenize ve yönetmenize yardımcı olmak için [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) ile tümleşiktir. böylece, VM disklerindeki tüm verilerin [Azure depolamadaki](https://azure.microsoft.com/documentation/services/storage/)geri kalanında şifrelenmesini sağlayabilirsiniz.

> [!NOTE]
> Azure disk şifrelemesi, aşağıdaki Windows Server işletim sistemlerinde desteklenir-Windows Server 2008 R2, Windows Server 2012 ve Windows Server 2012 R2. Disk şifrelemesi, aşağıdaki Linux sunucu işletim sistemlerinde (Ubuntu, CentOS, SUSE ve SUSE Linux Enterprise Server (SLES) desteklenir.
>
>

## <a name="implement-the-recommendation"></a>Öneriyi uygulayın
1. **Öneriler** dikey penceresinde **disk şifrelemeyi Uygula**' yı seçin.
2. **Disk şifrelemeyi Uygula** dikey penceresinde disk şifrelemesi Için önerilen sanal makinelerin listesini görürsünüz.
3. Bu VM 'lere şifreleme uygulamak için yönergeleri izleyin.

![][1]

Şifreleme gerektiren güvenlik merkezi tarafından tanımlanan Azure sanal makinelerini şifrelemek için aşağıdaki adımları öneririz:

* Azure PowerShell'i yükleyip yapılandırın. Bu, Azure sanal makinelerini şifrelemek için gereken önkoşulları ayarlamak için gerekli PowerShell komutlarını çalıştırmanızı sağlar.
* Azure disk şifrelemesi önkoşulları Azure PowerShell betiği edinin ve çalıştırın.
* Sanal makinelerinizi şifreleyin.

[Windows IaaS VM 'sini Azure PowerShell Ile şifreleme](../virtual-machines/windows/disk-encryption-powershell-quickstart.md) , bu adımlarda size yol gösterir. Bu konu, disk şifrelemesini yapılandırdığınız bir Windows istemci makinesini kullandığınızı varsayar.

Azure sanal makineleri için kullanılabilecek birçok yaklaşım vardır. Azure PowerShell veya Azure CLI konusunda zaten bilgiliyseniz alternatif yaklaşımlar kullanmayı tercih edebilirsiniz. Bu diğer yaklaşımlar hakkında bilgi edinmek için bkz. [Azure disk şifrelemesi](../security/fundamentals/encryption-overview.md).

## <a name="see-also"></a>Ayrıca bkz.
Bu belge, güvenlik merkezi 'nin "disk şifrelemeyi uygulama" önerilerini nasıl uygulayacağınızı gösterdi. Disk şifrelemesi hakkında daha fazla bilgi edinmek için aşağıdakilere bakın:

* [Şifreleme ve anahtar yönetimi Azure Key Vault](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (video, 36 dakikalık 39 sn)--verilerinizi korumaya ve korumaya yardımcı olmak Için IaaS VM 'leri için disk şifreleme yönetimi ve Azure Key Vault nasıl kullanacağınızı öğrenin.
* [Azure disk şifrelemesi](../security/fundamentals/encryption-overview.md) (belge)-Windows ve Linux VM 'leri için disk şifrelemeyi etkinleştirmeyi öğrenin.

Güvenlik Merkezi hakkında daha fazla bilgi edinmek için şunlara bakın:

* [Azure Güvenlik Merkezi 'nde güvenlik Ilkelerini ayarlama](tutorial-security-policy.md) -güvenlik ilkelerini yapılandırma hakkında bilgi edinin.
* [Azure Güvenlik Merkezi 'Nde güvenlik durumu izleme](security-center-monitoring.md) -Azure kaynaklarınızın sistem durumunu izlemeyi öğrenin.
* [Azure Güvenlik Merkezi'nde güvenlik uyarılarını yönetme ve yanıtlama](security-center-managing-and-responding-alerts.md) -- Güvenlik uyarılarını yönetme ve yanıtlama hakkında bilgi edinin.
* [Azure Güvenlik Merkezi 'nde güvenlik önerilerini yönetme](security-center-recommendations.md) -önerilerin Azure kaynaklarınızı korumanıza nasıl yardımcı olduğunu öğrenin.
* [Azure Güvenlik Merkezi ile ilgili SSS](security-center-faq.md) -- Hizmeti kullanımı ile ilgili sık sorulan soruları bulabilirsiniz.
* [Azure Güvenlik blogu](https://blogs.msdn.com/b/azuresecurity/) -Azure güvenliği ve uyumluluğu ile ilgili blog gönderilerini bulun.

<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png
