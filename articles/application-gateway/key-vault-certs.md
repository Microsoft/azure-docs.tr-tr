---
title: Azure Key Vault sertifikalarla SSL sonlandırma
description: HTTPS özellikli dinleyicilerine eklenen sunucu sertifikaları için Azure Application Gateway Key Vault nasıl tümleştirileceğini öğrenin.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 76807c8ed10e30c554b6aa06ec096c830a86e36e
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73571988"
---
# <a name="ssl-termination-with-key-vault-certificates"></a>Key Vault sertifikalarla SSL sonlandırma

[Azure Key Vault](../key-vault/key-vault-overview.md) , gizli dizileri, anahtarları ve SSL sertifikalarını korumak için kullanabileceğiniz, platform tarafından yönetilen bir gizli depodır. Azure Application Gateway, HTTPS özellikli dinleyicilerine eklenen sunucu sertifikaları için Key Vault tümleştirmeyi destekler. Bu destek, Application Gateway v2 SKU 'SU ile sınırlıdır.

Key Vault tümleştirme, SSL sonlandırma için iki model sunar:

- Dinleyiciye bağlı SSL sertifikalarını açıkça sağlayabilirsiniz. Bu model SSL sertifikalarını SSL sonlandırma için Application Gateway geçirmek için geleneksel bir yoldur.
- İsteğe bağlı olarak, HTTPS özellikli bir dinleyici oluştururken Mevcut bir Key Vault sertifikasına veya parolaya bir başvuru sağlayabilirsiniz.

Key Vault ile tümleştirme Application Gateway aşağıdakiler de dahil olmak üzere birçok avantaj sunar:

- SSL sertifikaları doğrudan uygulama geliştirme ekibi tarafından işlenmediğinden daha güçlü güvenlik. Tümleştirme, ayrı bir güvenlik ekibinin şunları yapmasına izin verir:
  * Uygulama ağ geçitlerini ayarlayın.
  * Uygulama ağ geçidi ömürleri kontrol edin.
  * Anahtar Kasanızda depolanan sertifikalara erişmek için seçili uygulama ağ geçitlerine izin verin.
- Mevcut sertifikaları anahtar kasanıza aktarma desteği. Ya da güvenilir Key Vault iş ortaklarıyla yeni sertifikalar oluşturup yönetmek için Key Vault API 'Leri kullanın.
- Anahtar Kasanızda depolanan sertifikaların otomatik yenilenmesi için destek.

Application Gateway Şu anda yalnızca yazılım tarafından doğrulanan sertifikaları desteklemektedir. Donanım güvenlik modülü (HSM)-doğrulanan sertifikalar desteklenmez. Application Gateway Key Vault sertifikaları kullanacak şekilde yapılandırıldıktan sonra, örnekleri sertifikayı Key Vault alır ve SSL sonlandırma için yerel olarak yükler. Örnekler Ayrıca, varsa sertifikanın yenilenen bir sürümünü almak için 24 saatlik aralıklarla Key Vault yoklama yapılır. Güncelleştirilmiş bir sertifika bulunursa, şu anda HTTPS dinleyicisiyle ilişkili SSL sertifikası otomatik olarak döndürülür.

## <a name="how-integration-works"></a>Tümleştirme nasıl çalışacaktır?

Key Vault ile tümleştirme Application Gateway üç adımlı bir yapılandırma işlemi gerektirir:

1. **Kullanıcı tarafından atanan yönetilen kimlik oluşturma**

   Application Gateway, Kullanıcı tarafından atanan mevcut bir yönetilen kimliği oluşturur veya yeniden kullanabilirsiniz. Bu, Key Vault sertifikaları sizin yerinize almak için kullanır. Daha fazla bilgi için bkz. [Azure kaynakları için Yönetilen kimlikler nelerdir?](../active-directory/managed-identities-azure-resources/overview.md). Bu adım Azure Active Directory kiracısında yeni bir kimlik oluşturur. Kimlik, kimlik oluşturmak için kullanılan abonelik tarafından güvenilirdir.

1. **Anahtar kasanızı yapılandırma**

   Daha sonra mevcut bir sertifikayı içeri aktarırsınız veya Anahtar Kasanızda yeni bir sertifika oluşturacaksınız. Sertifika, uygulama ağ geçidi aracılığıyla çalışan uygulamalar tarafından kullanılacaktır. Bu adımda, parola daha az, temel 64 kodlu PFX dosyası olarak depolanan bir Anahtar Kasası gizli anahtarını da kullanabilirsiniz. Anahtar kasasındaki sertifika türü nesneleriyle kullanılabilen otomatik yenileme özelliği nedeniyle bir sertifika türü kullanmanızı öneririz. Bir sertifika veya gizli dizi oluşturduktan sonra, kimliğe gizli dizi *erişimi verilmesini sağlamak* için anahtar kasasında erişim ilkeleri tanımlarsınız.

1. **Application Gateway 'i yapılandırma**

   Önceki iki adımı tamamladıktan sonra, mevcut bir uygulama ağ geçidini Kullanıcı tarafından atanan yönetilen kimliği kullanacak şekilde ayarlayabilir veya değiştirebilirsiniz. HTTP dinleyicisinin SSL sertifikasını, Key Vault sertifikasının veya gizli dizinin tüm URI 'sine işaret etmek üzere de yapılandırabilirsiniz.

   ![Anahtar Kasası sertifikaları](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Sonraki adımlar

[Azure PowerShell kullanarak SSL sonlandırmasını Key Vault sertifikalarla yapılandırma](configure-keyvault-ps.md)
