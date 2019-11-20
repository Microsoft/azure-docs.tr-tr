---
title: Azure Veri Gezgini kümenizin güvenliğini sağlama
description: Bu makalede, Azure portal içindeki Azure Veri Gezgini kümenizin güvenliğini nasıl güvence altına almak açıklanmaktadır.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: c6f7e921886a6acdaa31d0f69f57119c339c0b8b
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172603"
---
# <a name="secure-your-cluster-in-azure-data-explorer"></a>Azure Veri Gezgini kümenizin güvenliğini sağlama

[Azure disk şifrelemesi](/azure/security/azure-security-disk-encryption-overview) , kuruluşunuzun güvenlik ve uyumluluk taahhütlerinizi karşılamak için verilerinizi korumanıza ve korumaya yardımcı olur. Küme sanal makinelerinizin işletim sistemi ve veri diskleri için birim şifrelemesi sağlar. Ayrıca, disk şifreleme anahtarlarını ve gizli dizileri denetlemenize ve yönetmenize olanak tanıyan [Azure Key Vault](/azure/key-vault/) ile tümleşir ve Azure depolama 'da VM disklerindeki tüm verilerin Rest 'de şifrelendiğinden emin olun. 

Küme güvenlik ayarlarınız, kümenizde disk şifrelemeyi etkinleştirmenizi sağlar.
  
## <a name="enable-encryption-at-rest"></a>Bekleyen şifrelemeyi etkinleştir
  
Kümenizdeki [geri kalan şifrelemeyi](/azure/security/fundamentals/encryption-atrest) etkinleştirmek, depolanan veriler için veri koruması sağlar (bekleyen). 

1. Azure portal Azure Veri Gezgini küme kaynağına gidin. **Ayarlar** başlığı altında **güvenlik**' i seçin. 

    ![Bekleyen şifrelemeyi aç](media/manage-cluster-security/security-encryption-at-rest.png)

1. **Güvenlik** penceresinde, **disk şifreleme** güvenlik ayarı için **Açık** ' ı seçin. 

1. **Kaydet**’i seçin.
 
> [!NOTE]
> Etkinleştirildikten sonra şifrelemeyi devre dışı bırakmak için **kapalı** ' yı seçin.

## <a name="next-steps"></a>Sonraki adımlar

[Küme durumunu denetleme](/azure/data-explorer/check-cluster-health)
