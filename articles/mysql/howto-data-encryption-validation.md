---
title: MySQL için Azure veritabanı 'nın doğrulanmasını güvence altına alma-veri şifreleme
description: Müşteriler tarafından yönetilen anahtarı kullanarak MySQL için Azure veritabanı şifrelemesini doğrulama hakkında bilgi edinin.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/28/2020
ms.openlocfilehash: 1f47b3d5679b70461eaba64b5815770162cb6fda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93241081"
---
# <a name="validating-data-encryption-for-azure-database-for-mysql"></a>MySQL için Azure veritabanı veri şifrelemesini doğrulama

Bu makale, MySQL için Azure veritabanı 'nda müşteri tarafından yönetilen anahtar kullanılarak veri şifrelemenin beklendiği gibi çalıştığını doğrulamanıza yardımcı olur.

## <a name="check-the-encryption-status"></a>Şifreleme durumunu denetleme

### <a name="from-portal"></a>Portaldan

1. Müşterinin anahtarının şifreleme için kullanıldığını doğrulamak istiyorsanız aşağıdaki adımları izleyin:

    * Azure Portal **Azure Key Vault**  ->  **anahtarlarına** gidin
    * Sunucu şifrelemesi için kullanılan anahtarı seçin.
    * **Etkin** anahtarın durumunu **Hayır** olarak ayarlayın.
  
       Bir süre sonra (yaklaşık **15 dakika**), MySQL Için Azure veritabanı sunucu **durumu** **erişilemez** olmalıdır. Sunucu üzerinde gerçekleştirilen g/ç işlemleri, sunucunun aslında müşteriler anahtarıyla şifrelenmiş olduğunu ve anahtarın şu anda geçerli olmadığını doğrulayan başarısız olur.
    
       Sunucuyu karşı **kullanılabilir** hale getirmek için anahtarı yeniden doğrulayabilirsiniz. 
    
    * Key Vault anahtarın durumunu **Evet** olarak ayarlayın.
    * Sunucu **veri şifrelemesi** üzerinde **anahtarı yeniden doğrula**' yı seçin.
    * Anahtarın yeniden doğrulanması başarılı olduktan sonra sunucu **durumu** **kullanılabilir** olarak değişir.

2. Azure portal, şifreleme anahtarının ayarlanmış olduğundan emin olmak için, veriler Azure portal kullanılan müşteriler anahtarı kullanılarak şifrelenir.

  :::image type="content" source="media/concepts-data-access-and-security-data-encryption/byok-validate.png" alt-text="Erişim ilkesine genel bakış":::

### <a name="from-cli"></a>CLı 'dan

1. MySQL için Azure veritabanı sunucusu için kullanılan anahtar kaynaklarını doğrulamak üzere *az CLI* komutunu kullanabiliriz.

    ```azurecli-interactive
   az mysql server key list --name  '<server_name>'  -g '<resource_group_name>'
    ```

    Veri şifreleme ayarı olmayan bir sunucu için, bu komut boş [] kümesine neden olur.

### <a name="azure-audit-reports"></a>Azure denetim raporları

Ayrıca, veri koruma standartları ve mevzuat gereksinimleriyle uyumluluk hakkında bilgi sağlayan [Denetim raporları](https://servicetrust.microsoft.com) da gözden geçirilebilir.

## <a name="next-steps"></a>Sonraki adımlar

Veri şifreleme hakkında daha fazla bilgi edinmek için bkz. [müşteri tarafından yönetilen anahtarla MySQL Için Azure veritabanı veri şifrelemesi](concepts-data-encryption-mysql.md).