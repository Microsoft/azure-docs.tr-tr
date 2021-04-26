---
title: Müşteri tarafından yönetilen anahtarları yapılandırmak için Azure portal kullanın
titleSuffix: Cognitive Services
description: Azure portal kullanarak müşteri tarafından yönetilen anahtarları Azure Key Vault ile yapılandırma hakkında bilgi edinin. Müşteri tarafından yönetilen anahtarlar, erişim denetimleri oluşturmanıza, döndürmenize, devre dışı bırakmanızı ve iptal edebilmesini sağlar.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 05/28/2020
ms.author: egeaney
ms.openlocfilehash: c79846b0a5b675c34e4e7919e9ecd9d591bfefe5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96356367"
---
## <a name="customer-managed-keys-with-azure-key-vault"></a>Azure Key Vault ile müşteri tarafından yönetilen anahtarlar

Müşteri tarafından yönetilen anahtarları depolamak için Azure Key Vault kullanmanız gerekir. Kendi anahtarlarınızı oluşturabilir ve bunları bir anahtar kasasında saklayabilir veya Azure Key Vault API 'Lerini kullanarak anahtarlar oluşturabilirsiniz. Bilişsel hizmetler kaynağı ve Anahtar Kasası aynı bölgede ve aynı Azure Active Directory (Azure AD) kiracısında olmalıdır, ancak farklı aboneliklerde olabilir. Azure Key Vault hakkında daha fazla bilgi için bkz. [Azure Key Vault nedir?](../../key-vault/general/overview.md).

Yeni bir bilişsel hizmetler kaynağı oluşturulduğunda, Microsoft tarafından yönetilen anahtarlar kullanılarak her zaman şifrelenir. Kaynağın oluşturulduğu sırada müşteri tarafından yönetilen anahtarların etkinleştirilmesi mümkün değildir. Müşteri tarafından yönetilen anahtarlar Azure Key Vault depolanır ve anahtar kasasının bilişsel hizmetler kaynağıyla ilişkili yönetilen kimliğe anahtar izinleri veren erişim ilkeleriyle sağlanması gerekir. Yönetilen kimlik yalnızca kaynak, CMK için gereken fiyatlandırma katmanı kullanılarak oluşturulduktan sonra kullanılabilir.

Müşteri tarafından yönetilen anahtarların etkinleştirilmesi, bir Azure AD 'nin bir özelliği olan bir sistem tarafından atanmış [yönetilen kimliği](../../active-directory/managed-identities-azure-resources/overview.md)de etkinleştirir. Sistem tarafından atanan yönetilen kimlik etkinleştirildikten sonra, bu kaynak Azure Active Directory kaydedilir. Kaydolduktan sonra yönetilen kimliğe, müşteri tarafından yönetilen anahtar kurulumu sırasında seçilen Key Vault erişim verilir. 

> [!IMPORTANT]
> Sistem tarafından atanan yönetilen kimlikleri devre dışı bırakırsanız, anahtar kasasına erişim kaldırılır ve müşteri anahtarlarıyla şifrelenen verilere artık erişilemeyecektir. Bu verilere bağımlı olan özellikler çalışmayı durdurur.

> [!IMPORTANT]
> Yönetilen kimlikler Şu anda çapraz dizin senaryolarını desteklemez. Azure portal müşteri tarafından yönetilen anahtarları yapılandırırken, bir yönetilen kimlik, kapakların altında otomatik olarak atanır. Daha sonra aboneliği, kaynak grubunu veya kaynağı bir Azure AD dizininden diğerine taşırsanız, kaynakla ilişkili yönetilen kimlik yeni kiracıya aktarılmaz, bu nedenle müşterinin yönettiği anahtarlar artık çalışmayabilir. Daha fazla bilgi için bkz. SSS 'de **Azure AD dizinleri arasında bir abonelik aktarma** [ve Azure kaynakları için yönetilen kimliklerle ilgili bilinen sorunlar](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

## <a name="configure-azure-key-vault"></a>Azure Key Vault'u yapılandırma

Müşteri tarafından yönetilen anahtarların kullanılması, anahtar kasasında iki özellik ayarlanmasını, **geçici silme** ve **Temizleme işlemi** yapılmasını gerektirir. Bu özellikler varsayılan olarak etkinleştirilmez, ancak yeni veya var olan bir anahtar kasasında PowerShell veya Azure CLı kullanılarak etkinleştirilebilir.

> [!IMPORTANT]
> **Yumuşak silme** ve **Temizleme** özellikleri etkin değilse ve anahtarınızı silerseniz, bilişsel hizmet kaynağınızın verilerini kurtaramazsınız.

Mevcut bir anahtar kasasında bu özellikleri etkinleştirmeyi öğrenmek için aşağıdaki makalelerden birinde **geçici silme özelliğini etkinleştirme** ve **Temizleme korumasını etkinleştirme** başlıklı bölümlere bakın:

- [PowerShell ile geçici silme nasıl kullanılır](../../key-vault/general/key-vault-recovery.md).
- [CLI ile geçici silme nasıl kullanılır](../../key-vault/general/key-vault-recovery.md).

Azure depolama şifrelemesi ile yalnızca 2048 boyutundaki RSA anahtarları desteklenir. Anahtarlar hakkında daha fazla bilgi için bkz. [Azure Key Vault anahtarlar, gizli diziler ve sertifikalar hakkında](../../key-vault/general/about-keys-secrets-certificates.md) **Key Vault anahtarlar** .

## <a name="enable-customer-managed-keys-for-your-resource"></a>Kaynağınız için müşteri tarafından yönetilen anahtarları etkinleştirin

Azure portal müşteri tarafından yönetilen anahtarları etkinleştirmek için şu adımları izleyin:

1. Bilişsel hizmetler kaynağına gidin.
1. Bilişsel hizmetler kaynağınızın **Ayarlar** dikey penceresinde **şifreleme**' ye tıklayın. Aşağıdaki şekilde gösterildiği gibi, **müşteri tarafından yönetilen anahtarlar** seçeneğini belirleyin.

    ![Müşteri tarafından yönetilen anahtarların nasıl seçileceğini gösteren ekran görüntüsü](../media/cognitive-services-encryption/selectcmk.png)

## <a name="specify-a-key"></a>Bir anahtar belirtin

Müşteri tarafından yönetilen anahtarları etkinleştirdikten sonra bilişsel hizmetler kaynağıyla ilişkilendirilecek bir anahtar belirtme fırsatına sahip olacaksınız.

### <a name="specify-a-key-as-a-uri"></a>URI olarak bir anahtar belirtin

Bir anahtarı URI olarak belirtmek için şu adımları izleyin:

1. Azure portal anahtar URI 'sini bulmak için, anahtar kasanıza gidin ve **anahtarlar** ayarını seçin. İstediğiniz anahtarı seçin ve ardından sürümlerini görüntülemek için anahtara tıklayın. Bu sürümün ayarlarını görüntülemek için bir anahtar sürüm seçin.
1. URI sağlayan **anahtar tanımlayıcı** alanının değerini kopyalayın.

    ![Anahtar Kasası anahtar URI 'sini gösteren ekran görüntüsü](../media/cognitive-services-encryption/key-uri-portal.png)

1. Depolama hesabınızın **şifreleme** ayarları ' nda **anahtar URI 'si girin** seçeneğini belirleyin.
1. Kopyaladığınız URI 'yi **anahtar URI** alanına yapıştırın.

   ![Anahtar URI 'sini girmeyi gösteren ekran görüntüsü](../media/cognitive-services-encryption/ssecmk2.png)

1. Anahtar kasasını içeren aboneliği belirtin.
1. Yaptığınız değişiklikleri kaydedin.

### <a name="specify-a-key-from-a-key-vault"></a>Anahtar kasasından anahtar belirtme

Anahtar kasasından bir anahtar belirtmek için öncelikle anahtar içeren bir anahtar kasanızın olduğundan emin olun. Anahtar kasasından bir anahtar belirtmek için şu adımları izleyin:

1. **Key Vault seçin** seçeneğini belirleyin.
1. Kullanmak istediğiniz anahtarı içeren anahtar kasasını seçin.
1. Anahtar kasasından anahtarı seçin.

   ![Müşteri tarafından yönetilen anahtar seçeneğini gösteren ekran görüntüsü](../media/cognitive-services-encryption/ssecmk3.png)

1. Yaptığınız değişiklikleri kaydedin.

## <a name="update-the-key-version"></a>Anahtar sürümünü güncelleştirme

Bir anahtarın yeni bir sürümünü oluşturduğunuzda, bilişsel hizmetler kaynağını yeni sürümü kullanacak şekilde güncelleştirin. Şu adımları izleyin:

1. Bilişsel hizmetler kaynağınız ' ne gidin ve **şifreleme** ayarlarını görüntüleyin.
1. Yeni anahtar sürümünün URI 'sini girin. Alternatif olarak, sürümü güncelleştirmek için anahtar kasasını ve anahtarı yeniden seçebilirsiniz.
1. Yaptığınız değişiklikleri kaydedin.

## <a name="use-a-different-key"></a>Farklı bir anahtar kullanın

Şifreleme için kullanılan anahtarı değiştirmek için şu adımları izleyin:

1. Bilişsel hizmetler kaynağınız ' ne gidin ve **şifreleme** ayarlarını görüntüleyin.
1. Yeni anahtar için URI girin. Alternatif olarak, anahtar kasasını seçip yeni bir anahtar seçebilirsiniz.
1. Yaptığınız değişiklikleri kaydedin.

## <a name="rotate-customer-managed-keys"></a>Müşteri tarafından yönetilen anahtarları döndür

Azure Key Vault, müşteri tarafından yönetilen bir anahtarı uyumluluk ilkelerinize göre döndürebilirsiniz. Anahtar döndürüldüğünde, bilişsel hizmetler kaynağını yeni anahtar URI 'sini kullanacak şekilde güncelleştirmeniz gerekir. Azure portal, anahtarın yeni bir sürümünü kullanmak üzere güncelleştirme hakkında bilgi edinmek için bkz. [anahtar sürümünü güncelleştirme](#update-the-key-version).

Anahtarın döndürülmesi kaynaktaki verilerin yeniden şifrelenmesini tetiklemez. Kullanıcıdan başka bir eylem gerekli değildir.

## <a name="revoke-access-to-customer-managed-keys"></a>Müşterinin yönettiği anahtarlara erişimi iptal etme

Müşteri tarafından yönetilen anahtarlara erişimi iptal etmek için PowerShell veya Azure CLı kullanın. Daha fazla bilgi için bkz. PowerShell veya [Azure Key Vault clı](/cli/azure/keyvault) [Azure Key Vault](/powershell/module/az.keyvault//) . Erişim iptal edildiğinde bilişsel hizmetler kaynağındaki tüm verilere erişimi etkin bir şekilde engeller. Bu, şifreleme anahtarına bilişsel hizmetler tarafından erişilemez.

## <a name="disable-customer-managed-keys"></a>Müşteri tarafından yönetilen anahtarları devre dışı bırak

Müşteri tarafından yönetilen anahtarları devre dışı bıraktığınızda, bilişsel hizmetler kaynağınız daha sonra Microsoft tarafından yönetilen anahtarlarla şifrelenir. Müşteri tarafından yönetilen anahtarları devre dışı bırakmak için şu adımları izleyin:

1. Bilişsel hizmetler kaynağınız ' ne gidin ve **şifreleme** ayarlarını görüntüleyin.
1. **Kendi anahtarınızı kullanın** ayarının yanındaki onay kutusunun işaretini kaldırın.