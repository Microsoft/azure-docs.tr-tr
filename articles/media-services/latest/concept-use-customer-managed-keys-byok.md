---
title: Kendi anahtarınızı getirin (müşteri tarafından yönetilen anahtarlar)
description: Media Services ile müşteri tarafından yönetilen bir anahtar (kendi anahtarını getir) kullanabilirsiniz.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: bc7b1a0742e79c3c84bf533e75467b5def2706b4
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277734"
---
# <a name="bring-your-own-key-customer-managed-keys-with-media-services"></a>Media Services ile kendi anahtarınızı getirin (müşteri tarafından yönetilen anahtarlar)

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Kendi Anahtarını Getir (BYOK), müşterilerin iş yüklerini buluta taşımasına yardımcı olan bir Azure genelindeki girişimdir. Müşteri tarafından yönetilen anahtarlar müşterilerin sektör uyumluluğu düzenlemelerine bağlı kalmalarını sağlar ve bir hizmetin kiracı yalıtımını geliştirir. Kullanıcılara şifreleme anahtarları denetimi sağlamak, Microsoft hizmetlerinde gereksiz erişimi ve denetimi ve güvenle derlemeyi en aza indirmenin bir yoludur.

## <a name="keys-and-key-management"></a>Anahtarlar ve anahtar yönetimi

Media Services 2020-05-01 API 'sini kullandığınızda kendi anahtarınızı Media Services kullanabilirsiniz. Media Services sahip bir sistem anahtarı tarafından şifrelenen tüm hesaplar için varsayılan hesap anahtarı oluşturulur. Kendi anahtarınızı kullandığınızda, hesap anahtarı anahtarınızla şifrelenir. İçerik anahtarları hesap anahtarı tarafından şifrelenir. Jobınputhttp URL 'leri ve simetrik belirteç doğrulama anahtarları da şifrelenir.

:::image type="content" source="./media/customer-managed-key/customer-managed-key.svg" alt-text="Müşteri tarafından yönetilen anahtar, sistem tarafından yönetilen anahtarın yerini alır":::

Media Services, size ait bir Key Vault anahtarınızı okumak için Media Services hesabının yönetilen kimliğini kullanır. Media Services, Key Vault hesap ile aynı bölgede olmasını ve geçici silme ve Temizleme korumasının etkinleştirilmesini gerektirir.

Anahtarınız 2048, 3072 veya 4096 RSA anahtarı olabilir ve hem HSM hem de yazılım anahtarları desteklenir.

> [!NOTE]
> EC anahtarları desteklenmez.

Anahtar adı ve anahtar sürümü ya da yalnızca anahtar adı belirtebilirsiniz. Yalnızca bir anahtar adı kullandığınızda Media Services en son anahtar sürümünü kullanacaktır. Müşteri anahtarlarının yeni sürümleri otomatik olarak algılanır ve hesap anahtarı yeniden şifrelenir.

> [!WARNING]
> Media Services müşteri anahtarına erişimi izler. Müşteri anahtarı erişilemez hale gelirse (örneğin, anahtar silinmişse veya Key Vault silinmişse veya erişim izni kaldırılmışsa), Media Services hesabı müşteri anahtarına erişilemez duruma geçirecek (hesabı etkin bir şekilde devre dışı bırakır). Ancak, hesap bu durumda silinebilir. Yalnızca hesap al, LISTELE ve SIL işlemleri desteklenir; Hesap anahtarına erişim geri yüklenene kadar diğer tüm istekler (kodlama, akış vb.) başarısız olur.

## <a name="double-encryption"></a>Çift şifreleme

Media Services otomatik olarak çift şifrelemeyi destekler. Bekleyen veriler için, ilk şifreleme katmanı, hesaptaki ayara bağlı olarak bir müşteri tarafından yönetilen anahtar veya Microsoft tarafından yönetilen anahtar kullanır `AccountEncryption` .  Bekleyen veriler için ikinci şifreleme katmanı, ayrı bir Microsoft tarafından yönetilen anahtar kullanılarak otomatik olarak sağlanır. Çift şifreleme hakkında daha fazla bilgi edinmek için bkz. [Azure Çift şifreleme](../../security/fundamentals/double-encryption.md).

> [!NOTE]
> Media Services hesapta Çift şifreleme otomatik olarak etkinleştirilir. Ancak, müşteri tarafından yönetilen anahtarı ve depolama hesabınızda çift şifrelemeyi ayrı olarak yapılandırmanız gerekir. Bkz., [storege şifreleme](../../storage/common/storage-service-encryption.md).

## <a name="tutorials"></a>Öğreticiler

- [Azure portal, müşteri tarafından yönetilen anahtarları veya BYOK 'u Media Services ile birlikte kullanmak için kullanın](security-customer-managed-keys-portal-tutorial.md)
- [Media Services REST API ile müşteri tarafından yönetilen anahtarları veya BYOK kullanın](security-customer-managed-keys-rest-postman-tutorial.md).

## <a name="next-steps"></a>Sonraki adımlar

[Media Services dinamik şifreleme ile içeriğinizi koruyun](drm-content-protection-concept.md)