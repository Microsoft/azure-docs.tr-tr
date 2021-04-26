---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 11/04/2020
ms.author: inhenkel
ms.custom: portal
ms.openlocfilehash: c3a27218f03980e9b42a4a56fa739203c43754a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96013284"
---
<!--Set the encryption on storage account in the portal-->

## <a name="set-the-encryption-on-a-storage-account"></a>Depolama hesabında şifrelemeyi ayarlama

1. Azure portal, ekranın üst kısmındaki **arama** alanına şifrelemek istediğiniz depolama hesabının adını girin.  Eşleşmeler arama alanının altında görüntülenir.
1. Aradığınız depolama hesabını seçin. Depolama hesabı ekranı görüntülenir.
1. **Şifrelemeyi** seçin.
1. Microsoft tarafından yönetilen anahtarlar veya müşteri tarafından yönetilen anahtarlar ' ı seçin.

### <a name="use-microsoft-managed-keys"></a>Microsoft tarafından yönetilen anahtarları kullan

Varsayılan olarak, depolama hesabındaki veriler Microsoft tarafından yönetilen anahtarlar kullanılarak şifrelenir.

### <a name="use-customer-managed-keys"></a>Müşteri tarafından yönetilen anahtarları kullanma

1. **Müşteri tarafından yönetilen anahtarlar**' ı seçin.
1. **Anahtar URI 'Si girin** veya **anahtar kasasından seçim** yapın.
    1. **Anahtar URI 'Si girin**' i seçerseniz, anahtar URI 'si ALANıNA anahtar URI 'sini girin ve aboneliği seçin. (Sizin için zaten seçilebilir olabilir.)
    1. **Anahtar kasasından Seç**' i seçerseniz, **Anahtar Kasası ve anahtar seçin**' i seçin. Azure Key Vault anahtarı Seç ekranı görüntülenir.
1. Kullanmak istediğiniz **Key Vault** seçin ve Anahtar Kasanızda zaten sahip olduğunuz bir anahtarı seçin ya da **Yeni bir anahtar oluşturun**.
    1. Yeni bir anahtar oluşturmayı seçerseniz **Seçenekler** açılan listesinden **Oluştur** veya **Al** ' ı seçin. Yalnızca RSA anahtarlarını içeri aktarabilirsiniz.
    1. Yeni bir anahtar oluşturmak için, **ad** alanına anahtar adını verin ve anahtar türünü seçin:
        1. RSA-anahtar boyutları: 2048, 3072 veya 4096. Bu, çoğu müşterinin seçim yaptığı şeydir.
        1. EC-eliptik eğri adları: P-256, P-384, P-521 veya P-256K
        1. İsteğe bağlı olarak, anahtarın etkinleştirme ve sona erme tarihlerini ayarlayabilirsiniz.
        1. Otomatik anahtar döndürmeyi etkinleştirmek için **Evet** ' i seçin.
        1. **Oluştur**’u seçin.
    1. Bir anahtarı içeri aktarmak için **Dosya Seç alanında** herhangi bir yere tıklayarak karşıya yüklenecek dosyayı seçin.
        1. Anahtara **ad** alanına bir ad verin.
        1. İsteğe bağlı olarak, anahtarın etkinleştirme ve sona erme tarihlerini ayarlayabilirsiniz.
        1. Otomatik anahtar döndürmeyi etkinleştirmek için **Evet** ' i seçin.
        1. **Oluştur**’u seçin.
    1. Depolama hesabınızı şifrelemek için bu anahtarı seçmek **üzere Seç ' i seçin** . Şifreleme ekranına geri yönlendirilirsiniz.
1. **ÖNEMLI!** Şifreleme ayarlarınızı veya yaptığınız her şeyi kaydetmek için **Kaydet** ' i seçin.
