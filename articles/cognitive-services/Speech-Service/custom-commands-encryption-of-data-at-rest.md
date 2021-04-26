---
title: Bekleyen özel komutlar hizmeti verileri şifreleme
titleSuffix: Azure Cognitive Services
description: Bekleyen verilerin özel komutları şifrelemesi.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2020
ms.author: sausin
ms.openlocfilehash: 89d7a6f8beb004f57a00dfe75e4cc387c8591b1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "101716593"
---
# <a name="custom-commands-encryption-of-data-at-rest"></a>Bekleyen verinin Özel Komutlarla şifrelenmesi

Özel komutlar, bulutta kalıcı olduğunda verilerinizi otomatik olarak şifreler. Özel komutlar hizmeti şifrelemesi, verilerinizi korur ve kurumsal güvenlik ve uyumluluk taahhütlerinizi karşılamanıza yardımcı olur.

> [!NOTE]
> Özel komutlar hizmeti, uygulamanızla ilişkili LUSıS kaynakları için şifrelemeyi otomatik olarak etkinleştirmez. Gerekirse, LUSıS kaynağınız için şifrelemeyi [buradan](../luis/encrypt-data-at-rest.md)etkinleştirmeniz gerekir.

## <a name="about-cognitive-services-encryption"></a>Bilişsel hizmetler şifreleme hakkında
[Fıps 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) uyumlu [256 bit AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) şifrelemesi kullanılarak veriler şifrelenir ve şifresi çözülür. Şifreleme ve şifre çözme saydamdır, bu da şifreleme ve erişimin sizin için yönetilip yönetilmediğini belirtir. Verileriniz varsayılan olarak koruma altındadır ve şifrelemeden yararlanmak için kodunuzu veya uygulamalarınızı değiştirmenize gerek yoktur.

## <a name="about-encryption-key-management"></a>Şifreleme anahtarı yönetimi hakkında

Özel komutlar kullandığınızda, konuşma hizmeti aşağıdaki verileri bulutta depolar:
* Özel komutlar uygulamasının arkasında Configuration JSON
* LUSıS yazma ve tahmin anahtarı

Aboneliğiniz varsayılan olarak Microsoft tarafından yönetilen şifreleme anahtarlarını kullanır. Öte yandan aboneliğinizi kendi şifreleme anahtarlarınızla da yönetebilirsiniz. Kendi Anahtarını Getir (KAG) olarak da bilinen Müşteri Tarafından Yönetilen Anahtarlar (CMK) erişim denetimlerini oluşturma, döndürme, devre dışı bırakma ve iptal etme konusunda daha fazla esneklik sunar. Verilerinizi korumak için kullanılan şifreleme anahtarlarını da denetleyebilirsiniz.


> [!IMPORTANT]
> Müşteri tarafından yönetilen anahtarlar yalnızca 27 Haziran 2020 ' den sonra oluşturulan kullanılabilir kaynaklardır. CMK 'yi konuşma hizmetleriyle birlikte kullanmak için yeni bir konuşma kaynağı oluşturmanız gerekir. Kaynak oluşturulduktan sonra, yönetilen kimliğinizi ayarlamak için Azure Key Vault kullanabilirsiniz.

Müşteri tarafından yönetilen anahtarlar kullanma yeteneği istemek için Customer-Managed anahtar Isteği formunu doldurun ve iletin. İsteğinizin durumunu öğrenmek yaklaşık 3-5 iş günü sürer. Talebe bağlı olarak, bir kuyruğa yerleştirilmiş ve alan kullanılabilir olduğunda onaylanmış olabilir. Konuşma Hizmetleri ile CMK kullanmaya onaylandıktan sonra, Azure portal yeni bir konuşma kaynağı oluşturmanız gerekir.
   > [!NOTE]
   > **Müşteri tarafından yönetilen anahtarlar (CMK) yalnızca özel komutlar için desteklenir.**
   >
   >  **Özel Konuşma Tanıma ve özel ses desteği yalnızca kendi depolama alanınızı (BYOS) getirir.**  [Daha fazla bilgi edinin](speech-encryption-of-data-at-rest.md)
   >
   > Bu hizmete erişmek için verilen konuşma kaynağını kullanıyorsanız, KCG 'LERI açıkça yapılandırarak uyumluluk ihtiyaçlarına ulaşılmalıdır.


## <a name="customer-managed-keys-with-azure-key-vault"></a>Azure Key Vault ile müşteri tarafından yönetilen anahtarlar

Müşteri tarafından yönetilen anahtarları depolamak için Azure Key Vault kullanmanız gerekir. Kendi anahtarlarınızı oluşturabilir ve bunları bir anahtar kasasında saklayabilir veya Azure Key Vault API 'Lerini kullanarak anahtarlar oluşturabilirsiniz. Konuşma kaynağı ve Anahtar Kasası aynı bölgede ve aynı Azure Active Directory (Azure AD) kiracısında olmalıdır, ancak farklı aboneliklerde olabilir. Azure Key Vault hakkında daha fazla bilgi için bkz. [Azure Key Vault nedir?](../../key-vault/general/overview.md).

Yeni bir konuşma kaynağı oluşturulduğunda ve özel komutlar sağlamak için kullanıldığında, veriler her zaman Microsoft tarafından yönetilen anahtarlar kullanılarak şifrelenir. Kaynağın oluşturulduğu sırada müşteri tarafından yönetilen anahtarların etkinleştirilmesi mümkün değildir. Müşteri tarafından yönetilen anahtarlar Azure Key Vault depolanır ve anahtar kasasının bilişsel hizmetler kaynağıyla ilişkili yönetilen kimliğe anahtar izinleri veren erişim ilkeleriyle sağlanması gerekir. Yönetilen kimlik yalnızca kaynak, CMK için gereken fiyatlandırma katmanı kullanılarak oluşturulduktan sonra kullanılabilir.

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

## <a name="enable-customer-managed-keys-for-your-speech-resource"></a>Konuşma kaynağınız için müşteri tarafından yönetilen anahtarları etkinleştirin

Azure portal müşteri tarafından yönetilen anahtarları etkinleştirmek için şu adımları izleyin:

1. Konuşma kaynağına gidin.
1. Konuşma kaynağınızın **Ayarlar** dikey penceresinde **şifreleme**' ye tıklayın. Aşağıdaki şekilde gösterildiği gibi, **müşteri tarafından yönetilen anahtarlar** seçeneğini belirleyin.

 ![Müşteri tarafından yönetilen anahtarların nasıl seçileceğini gösteren ekran görüntüsü](media/custom-commands/select-cmk.png)

## <a name="specify-a-key"></a>Bir anahtar belirtin

Müşteri tarafından yönetilen anahtarları etkinleştirdikten sonra bilişsel hizmetler kaynağıyla ilişkilendirilecek bir anahtar belirtme fırsatına sahip olacaksınız.

### <a name="specify-a-key-as-a-uri"></a>URI olarak bir anahtar belirtin

Bir anahtarı URI olarak belirtmek için şu adımları izleyin:

1. Azure portal anahtar URI 'sini bulmak için, anahtar kasanıza gidin ve **anahtarlar** ayarını seçin. İstediğiniz anahtarı seçin ve ardından sürümlerini görüntülemek için anahtara tıklayın. Bu sürümün ayarlarını görüntülemek için bir anahtar sürüm seçin.
1. URI sağlayan **anahtar tanımlayıcı** alanının değerini kopyalayın.

    ![Anahtar Kasası anahtar URI 'sini gösteren ekran görüntüsü](../media/cognitive-services-encryption/key-uri-portal.png)

1. Konuşma hizmetinizin **şifreleme** ayarları ' nda **anahtar URI 'si girin** seçeneğini belirleyin.
1. Kopyaladığınız URI 'yi **anahtar URI** alanına yapıştırın.

1. Anahtar kasasını içeren aboneliği belirtin.
1. Yaptığınız değişiklikleri kaydedin.

### <a name="specify-a-key-from-a-key-vault"></a>Anahtar kasasından anahtar belirtme

Anahtar kasasından bir anahtar belirtmek için öncelikle anahtar içeren bir anahtar kasanızın olduğundan emin olun. Anahtar kasasından bir anahtar belirtmek için şu adımları izleyin:

1. **Key Vault seçin** seçeneğini belirleyin.
1. Kullanmak istediğiniz anahtarı içeren anahtar kasasını seçin.
1. Anahtar kasasından anahtarı seçin.

   ![Müşteri tarafından yönetilen anahtar seçeneğini gösteren ekran görüntüsü](media/custom-commands/configure-cmk-fromKV.png)

1. Yaptığınız değişiklikleri kaydedin.

## <a name="update-the-key-version"></a>Anahtar sürümünü güncelleştirme

Bir anahtarın yeni bir sürümünü oluşturduğunuzda, yeni sürümü kullanmak için konuşma kaynağını güncelleştirin. Şu adımları izleyin:

1. Konuşma kaynağınız ' ne gidip **şifreleme** ayarlarını görüntüleyin.
1. Yeni anahtar sürümünün URI 'sini girin. Alternatif olarak, sürümü güncelleştirmek için anahtar kasasını ve anahtarı yeniden seçebilirsiniz.
1. Yaptığınız değişiklikleri kaydedin.

## <a name="use-a-different-key"></a>Farklı bir anahtar kullanın

Şifreleme için kullanılan anahtarı değiştirmek için şu adımları izleyin:

1. Konuşma kaynağınız ' ne gidip **şifreleme** ayarlarını görüntüleyin.
1. Yeni anahtar için URI girin. Alternatif olarak, anahtar kasasını seçip yeni bir anahtar seçebilirsiniz.
1. Yaptığınız değişiklikleri kaydedin.

## <a name="rotate-customer-managed-keys"></a>Müşteri tarafından yönetilen anahtarları döndür

Azure Key Vault, müşteri tarafından yönetilen bir anahtarı uyumluluk ilkelerinize göre döndürebilirsiniz. Anahtar döndürüldüğünde, konuşma kaynağını yeni anahtar URI 'sini kullanacak şekilde güncelleştirmeniz gerekir. Azure portal, anahtarın yeni bir sürümünü kullanmak üzere güncelleştirme hakkında bilgi edinmek için bkz. [anahtar sürümünü güncelleştirme](#update-the-key-version).

Anahtarın döndürülmesi kaynaktaki verilerin yeniden şifrelenmesini tetiklemez. Kullanıcıdan başka bir eylem gerekli değildir.

## <a name="revoke-access-to-customer-managed-keys"></a>Müşterinin yönettiği anahtarlara erişimi iptal etme

Müşteri tarafından yönetilen anahtarlara erişimi iptal etmek için PowerShell veya Azure CLı kullanın. Daha fazla bilgi için bkz. PowerShell veya [Azure Key Vault clı](/cli/azure/keyvault) [Azure Key Vault](/powershell/module/az.keyvault//) . Erişim iptal edildiğinde bilişsel hizmetler kaynağındaki tüm verilere erişimi etkin bir şekilde engeller. Bu, şifreleme anahtarına bilişsel hizmetler tarafından erişilemez.

## <a name="disable-customer-managed-keys"></a>Müşteri tarafından yönetilen anahtarları devre dışı bırak

Müşteri tarafından yönetilen anahtarları devre dışı bıraktığınızda, konuşma kaynağınız Microsoft tarafından yönetilen anahtarlarla şifrelenir. Müşteri tarafından yönetilen anahtarları devre dışı bırakmak için şu adımları izleyin:

1. Konuşma kaynağınız ' ne gidip **şifreleme** ayarlarını görüntüleyin.
1. **Kendi anahtarınızı kullanın** ayarının yanındaki onay kutusunun işaretini kaldırın.

## <a name="next-steps"></a>Sonraki adımlar

* [Konuşma Customer-Managed anahtar Istek Formu](https://aka.ms/cogsvc-cmk)
* [Azure Key Vault hakkında daha fazla bilgi edinin](../../key-vault/general/overview.md)
* [Yönetilen kimlikler nelerdir?](../../active-directory/managed-identities-azure-resources/overview.md)