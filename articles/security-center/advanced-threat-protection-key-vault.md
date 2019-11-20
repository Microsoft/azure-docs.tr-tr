---
title: Azure Key Vault için Gelişmiş tehdit koruması nasıl ayarlanır | Microsoft Docs
description: Bu makalede, Azure Güvenlik Merkezi 'nde Azure Key Vault için Gelişmiş tehdit koruması ayarlama açıklanmaktadır
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: b28f03f0997b6bb2c494c35cee9695a478906c47
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521884"
---
# <a name="how-to-set-up-advanced-threat-protection-for-azure-key-vault-preview"></a>Azure Key Vault için Gelişmiş tehdit koruması ayarlama (Önizleme)

Azure Key Vault için Gelişmiş tehdit koruması, ek bir güvenlik zekası katmanı sağlar. Bu araç Key Vault hesaplara erişmeye veya açıktan yararlanmaya yönelik olabilecek zararlı girişimleri algılar. Güvenlik Merkezi 'nin yerel Gelişmiş tehdit korumasını kullanarak tehditleri güvenlik uzmanı olmadan ve ek güvenlik izleme sistemleri öğrenmeksizin ele alabilirsiniz.

Güvenlik Merkezi anormal etkinlik algıladığında uyarıları görüntüler. Ayrıca, şüpheli etkinliğin ayrıntılarını ve belirlenen tehditleri araştırıp düzeltme önerilerini içeren abonelik yöneticisine e-posta gönderir. 

> [!NOTE]
> Azure Key Vault için Gelişmiş tehdit koruması Şu anda yalnızca Kuzey Amerika bölgelerde kullanılabilir.

## <a name="to-set-up-advanced-threat-protection-from-azure-security-center"></a>Azure Güvenlik Merkezi 'nden Gelişmiş tehdit koruması ayarlamak için

Varsayılan olarak, güvenlik merkezi 'nin standart katmanına abone olduğunuzda Gelişmiş tehdit koruması tüm Key Vault hesaplarınız için etkinleştirilmiştir (bkz. [fiyatlandırma](security-center-pricing.md)). 

Belirli bir aboneliğin korumasını etkinleştirmek veya devre dışı bırakmak için:

1. Güvenlik Merkezi 'nin kenar çubuğundan **fiyatlandırma & ayarları**' na tıklayın.
1. Tehdit korumasını etkinleştirmek veya devre dışı bırakmak istediğiniz depolama hesaplarına sahip aboneliği seçin.
1. **Fiyatlandırma katmanı**' na tıklayın.
1. **Fiyatlandırma katmanını kaynak türüne göre seçin** grubunda, Anahtar kasaları satırını bulun ve **etkin** veya **devre dışı**' ya tıklayın.
    [Azure Güvenlik Merkezi 'nde Key Vault için Gelişmiş tehdit korumasını etkinleştirme veya devre dışı bırakma ![](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)
1. **Kaydet** düğmesine tıklayın.


## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Azure Key Vault için Gelişmiş tehdit koruması 'nı etkinleştirmeyi ve devre dışı bırakmayı öğrendiniz. 

Diğer ilgili malzemeler için aşağıdaki makaleye bakın:

- [Güvenlik Merkezi 'Ndeki Azure hizmetleri katmanları Için tehdit algılama](security-center-alerts-service-layer.md) -bu makalede Azure Key Vault için Gelişmiş tehdit koruması ile ilgili uyarılar açıklanmaktadır