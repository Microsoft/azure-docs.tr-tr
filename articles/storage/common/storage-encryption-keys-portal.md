---
title: Azure portal Azure depolama şifrelemesi için müşteri tarafından yönetilen anahtarları yapılandırın
description: Azure depolama şifrelemesi için müşteri tarafından yönetilen anahtarları yapılandırmak üzere Azure portal nasıl kullanacağınızı öğrenin. Müşteri tarafından yönetilen anahtarlar, erişim denetimleri oluşturmanıza, döndürmenize, devre dışı bırakmanızı ve iptal edebilmesini sağlar.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/15/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 74ca6f15baeeb9fe8adad4bda80e313a4b4cf03a
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376234"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-the-azure-portal"></a>Azure portal Azure depolama şifrelemesi için müşteri tarafından yönetilen anahtarları yapılandırın

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Bu makalede, [Azure Portal](https://portal.azure.com/)kullanarak, müşteri tarafından yönetilen anahtarlarla bir anahtar kasasının nasıl yapılandırılacağı gösterilmektedir. Azure portal kullanarak bir Anahtar Kasası oluşturmayı öğrenmek için bkz. [hızlı başlangıç: Azure Portal kullanarak Azure Key Vault gizli dizi ayarlama ve alma](../../key-vault/quick-create-portal.md). 

> [!IMPORTANT]
> Azure depolama şifrelemesi ile müşteri tarafından yönetilen anahtarların kullanılması, anahtar kasasında iki özellik ayarlanmasını, **geçici silme** ve **Temizleme işlemi**yapılmasını gerektirir. Bu özellikler varsayılan olarak etkinleştirilmemiştir. Bu özellikleri etkinleştirmek için PowerShell veya Azure CLı kullanın.
> Yalnızca RSA anahtarları ve anahtar boyutu 2048 desteklenir.

## <a name="enable-customer-managed-keys"></a>Müşteri tarafından yönetilen anahtarları etkinleştir

Azure portal müşteri tarafından yönetilen anahtarları etkinleştirmek için şu adımları izleyin:

1. Depolama hesabınıza gidin.
1. Depolama hesabının **Ayarlar** dikey penceresinde **şifreleme**' ye tıklayın. Aşağıdaki şekilde gösterildiği gibi **kendi anahtarınızı kullanın** seçeneğini belirleyin.

    ![Şifreleme seçeneğini gösteren Portal ekran görüntüsü](./media/storage-encryption-keys-portal/ssecmk1.png)

## <a name="specify-a-key"></a>Bir anahtar belirtin

Müşteri tarafından yönetilen anahtarları etkinleştirdikten sonra, depolama hesabıyla ilişkilendirilecek bir anahtar belirtme fırsatına sahip olacaksınız.

### <a name="specify-a-key-as-a-uri"></a>URI olarak bir anahtar belirtin

Bir anahtarı URI olarak belirtmek için şu adımları izleyin:

1. Azure portal anahtar URI 'sini bulmak için, anahtar kasanıza gidin ve **anahtarlar** ayarını seçin. İstediğiniz anahtarı seçin ve ardından ayarlarını görüntülemek için anahtara tıklayın. URI sağlayan **anahtar tanımlayıcı** alanının değerini kopyalayın.

    ![Anahtar Kasası anahtar URI 'sini gösteren ekran görüntüsü](media/storage-encryption-keys-portal/key-uri-portal.png)

1. Depolama hesabınızın **şifreleme** ayarları ' nda **anahtar URI 'si girin** seçeneğini belirleyin.
1. **Anahtar URI 'si** alanında URI 'yi belirtin.

   ![Anahtar URI 'sini girmeyi gösteren ekran görüntüsü](./media/storage-encryption-keys-portal/ssecmk2.png)

### <a name="specify-a-key-from-a-key-vault"></a>Anahtar kasasından anahtar belirtme

Anahtar kasasından bir anahtar belirtmek için öncelikle anahtar içeren bir anahtar kasanızın olduğundan emin olun. Anahtar kasasından bir anahtar belirtmek için şu adımları izleyin:

1. **Key Vault seçin** seçeneğini belirleyin.
2. Kullanmak istediğiniz anahtarı içeren anahtar kasasını seçin.
3. Anahtar kasasından anahtarı seçin.

   ![Müşteri tarafından yönetilen anahtar seçeneğini gösteren ekran görüntüsü](./media/storage-encryption-keys-portal/ssecmk3.png)

## <a name="update-the-key-version"></a>Anahtar sürümünü güncelleştirme

Bir anahtarın yeni bir sürümünü oluşturduğunuzda, yeni sürümü kullanmak için depolama hesabını güncelleştirmeniz gerekir. Şu adımları uygulayın:

1. Depolama hesabınıza gidin ve **şifreleme** ayarlarını görüntüleyin.
1. Yeni anahtar sürümünün URI 'sini belirtin. Alternatif olarak, sürümü güncelleştirmek için anahtar kasasını ve anahtarı yeniden seçebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Bekleyen veriler için Azure depolama şifrelemesi](storage-service-encryption.md)
- [Azure Key Vault nedir](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
