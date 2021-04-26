---
title: Key Vault için Azure Defender uyarılarına yanıt verme
description: Key Vault için Azure Defender 'daki uyarılara yanıt vermek için gereken adımlar hakkında bilgi edinin.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 67c556e44f07240b1ad1bcde61f40042da46def8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96122209"
---
# <a name="respond-to-azure-defender-for-key-vault-alerts"></a>Key Vault için Azure Defender uyarılarına yanıt verme
Key Vault için Azure Defender 'dan bir uyarı aldığınızda, uyarıyı araştırıp aşağıda açıklandığı gibi uyarmanızı öneririz. Key Vault için Azure Defender uygulamaları ve kimlik bilgilerini korur, dolayısıyla uyarıyı tetikleyen uygulamayı veya kullanıcıyı tanıyor olsanız bile her uyarıyı çevreleyen durumu doğrulamanız önemlidir.  

Key Vault için Azure Defender 'daki her uyarı, aşağıdaki öğeleri içerir:

- Nesne kimliği
- Şüpheli kaynağın Kullanıcı asıl adı veya IP adresi

> [!TIP]
> Oluşan erişim *türüne* göre bazı alanlar kullanılamayabilir. Örneğin anahtar kasanıza bir uygulama erişiyorsa, ilişkili Kullanıcı Asıl Adını görmezsiniz. Trafik Azure'ın dışından kaynaklandıysa Nesne Kimliğini görmezsiniz.

## <a name="step-1-contact"></a>Adım 1. İletişim

1. Trafiğin Azure kiracınızın içinden kaynaklanıp kaynaklanmadığını doğrulayın. Anahtar Kasası güvenlik duvarı etkinleştirilmişse, bu uyarıyı tetikleyen Kullanıcı veya uygulamaya erişim sağladınız.
1. Trafiğin kaynağını doğrulayadıysanız adım 2 ' ye geçin [. Anında risk azaltma](#step-2-immediate-mitigation).
1. Kiracınızdaki trafiğin kaynağını belirleyebiliyorsanız, uygulamanın kullanıcısına veya sahibine başvurun. 

> [!CAUTION]
> Key Vault için Azure Defender, çalınmış kimlik bilgilerinin neden olduğu şüpheli etkinliklerin tanımlanmasına yardımcı olmak için tasarlanmıştır. Kullanıcı veya uygulamayı tanıladığınız için uyarıyı **kapatmayın** . Uygulamanın sahibine veya kullanıcıya başvurun ve etkinliğin meşru olduğunu doğrulayın. Gerekirse paraziti ortadan kaldırmak için bir gizleme kuralı oluşturabilirsiniz. [Azure Defender 'dan uyarıları gösterme](alerts-suppression-rules.md)konusunda daha fazla bilgi edinin.


## <a name="step-2-immediate-mitigation"></a>Adım 2. Anında risk azaltma 
Kullanıcı veya uygulamayı tanımıyorsanız veya erişimin yetkilendirilmediğini düşünüyorsanız:

- Trafik tanınmayan bir IP adresinden geldiyse:
    1. [Azure Key Vault güvenlik duvarlarını ve sanal ağları yapılandırma](../key-vault/general/network-security.md)başlığı altında açıklandığı gibi Azure Key Vault güvenlik duvarını etkinleştirin.
    1. Güvenlik duvarını güvenilen kaynaklarla ve sanal ağlarla yapılandırın.

- Uyarının kaynağı yetkisiz bir uygulama veya şüpheli bir kullanıcı ise:
    1. Anahtar kasasının erişim ilkesi ayarlarını açın.
    1. İlgili güvenlik sorumlusunu kaldırın veya güvenlik sorumlusunun gerçekleştirebileceği işlemleri kısıtlayın.  

- Uyarının kaynağının kiracınızda bir Azure Active Directory rolü varsa:
    1. Yöneticinize başvurun.
    1. Azure Active Directory izinleri azaltma veya iptal etme gereksinimi olup olmadığını belirleme.

## <a name="step-3-identify-impact"></a>3. Adım Etkiyi tanımla 
Etki azaltıldığında, Anahtar Kasanızda etkilenen gizli dizileri araştırın:
1. Azure Key Vault "güvenlik" sayfasını açın ve tetiklenen uyarıyı görüntüleyin.
1. Tetiklenen belirli bir uyarıyı seçin.
    Erişilen gizli dizi ve zaman damgası listesini gözden geçirin.
1. İsteğe bağlı olarak, Anahtar Kasası tanılama günlüklerinizi etkinleştirdiyseniz, ilgili çağıran IP, Kullanıcı sorumlusu veya nesne KIMLIĞI için önceki işlemleri gözden geçirin.  

## <a name="step-4-take-action"></a>4. Adım: İşlem yapın 
Kuşkulu Kullanıcı veya uygulama tarafından erişilen gizli dizi, anahtar ve sertifika listenizi derlediyseniz, bu nesneleri hemen döndürmeniz gerekir.

1. Etkilenen gizli dizileri anahtar kasanızdan devre dışı bırakılmalıdır veya silinmelidir.
1. Belirli bir uygulama için kimlik bilgileri kullanılmışsa:
    1. Uygulamanın yöneticisine başvurun ve bu kişilerin, tehlikede olduğu gibi güvenliği aşılmış kimlik bilgilerinin kullanımları için kendi ortamlarını denetlemesini isteyin.
    1. Güvenliği aşılmış kimlik bilgileri kullanılmışsa, uygulama sahibi erişilen bilgileri tanımlamalıdır ve etkiyi azaltır.


## <a name="next-steps"></a>Sonraki adımlar

Bu sayfa, Key Vault için Azure Defender 'dan bir uyarıya yanıt verme işlemini açıklanmıştı. İlgili bilgiler için aşağıdaki sayfalara bakın:

- [Key Vault için Azure Defender 'a giriş](defender-for-key-vault-introduction.md)
- [Azure Defender 'dan uyarıları gösterme](alerts-suppression-rules.md)
- [Güvenlik Merkezi verilerini sürekli dışa aktarma](continuous-export.md)