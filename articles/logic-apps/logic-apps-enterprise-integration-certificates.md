---
title: Sertifikalarla güvenli B2B iletileri-Azure Logic Apps | Microsoft Docs
description: Enterprise Integration Pack Azure Logic Apps içindeki güvenli B2B iletilerine sertifika ekleme
services: logic-apps
ms.service: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, LADocs
manager: jeconnoc
ms.assetid: 4cbffd85-fe8d-4dde-aa5b-24108a7caa7d
ms.suite: integration
ms.topic: article
ms.date: 08/17/2018
ms.openlocfilehash: be3dbc386b0ac89a85bc1719081df944523c28ce
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997831"
---
# <a name="secure-b2b-messages-with-certificates"></a>Sertifikalarla güvenli B2B iletileri

B2B iletişimini gizli tutmanız gerektiğinde, tümleştirme hesabınıza sertifikalar ekleyerek kurumsal tümleştirme uygulamalarınızın, özellikle mantıksal uygulamalarınızın B2B iletişimini güvenli hale getirebilirsiniz. Sertifikalar, elektronik iletişimlerdeki katılımcılar için kimlikleri denetleyen ve bu yollarla iletişimin güvenliğini sağlamanıza yardımcı olan dijital belgelerdir:

* İleti içeriğini şifreleyin.
* İletileri dijital olarak imzala. 

Bu sertifikaları, kurumsal tümleştirme uygulamalarınızda kullanabilirsiniz:

* Ortak bir internet [sertifika yetkilisinden (CA)](https://en.wikipedia.org/wiki/Certificate_authority) satın almanız gereken ancak herhangi bir anahtar gerektirmeyen [ortak sertifikalar](https://en.wikipedia.org/wiki/Public_key_certificate). 

* Kendi oluşturduğunuz ve aynı zamanda özel anahtarlar gerektiren özel sertifikalar veya [*otomatik olarak imzalanan sertifikalar*](https://en.wikipedia.org/wiki/Self-signed_certificate). 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upload-a-public-certificate"></a>Ortak sertifikayı karşıya yükle

B2B özelliklerine sahip Logic Apps 'te *ortak bir sertifika* kullanmak için önce sertifikayı tümleştirme hesabınıza yüklemeniz gerekir. Oluşturduğunuz [anlaşmalarda](logic-apps-enterprise-integration-agreements.md) özellikleri tanımladıktan sonra, B2B iletilerinizi güvenli hale getirmenize yardımcı olmak için sertifika kullanılabilir.

1. [Azure Portal](https://portal.azure.com) oturum açın. Ana Azure menüsünde **tüm kaynaklar**' ı seçin. Arama kutusuna, tümleştirme hesabınızın adını girin ve ardından istediğiniz tümleştirme hesabını seçin.

   ![Tümleştirme hesabınızı bulun ve seçin](media/logic-apps-enterprise-integration-certificates/select-integration-account.png)  

2. **Bileşenler**bölümünde **Sertifikalar** kutucuğunu seçin.

   !["Sertifikalar" ı seçin](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

3. **Sertifikalar**altında **Ekle**' yi seçin. **Sertifika ekle**' nin altında, sertifikanız için bu ayrıntıları sağlayın. İşiniz bittiğinde **Tamam**' ı seçin.

   | Özellik | Value | Açıklama | 
   |----------|-------|-------------|
   | **Name** | <*Sertifika adı*> | Bu örnekte, sertifikanızın adı "publicCert" | 
   | **Sertifika türü** | Genel | Sertifikanızın türü |
   | **Sertifika** | <*sertifika-dosya adı*> | Karşıya yüklemek istediğiniz sertifika dosyasını bulmak ve seçmek için, **sertifika** kutusunun yanındaki klasör simgesini seçin. |
   ||||

   !["Ekle" seçeneğini belirleyin, sertifika bilgilerini sağlayın](media/logic-apps-enterprise-integration-certificates/public-certificate-details.png)

   Azure seçiminizi doğruladıktan sonra, Azure sertifikanızı karşıya yükler.

   ![Azure yeni sertifika görüntülüyor](media/logic-apps-enterprise-integration-certificates/new-public-certificate.png) 

## <a name="upload-a-private-certificate"></a>Özel bir sertifikayı karşıya yükle

B2B özelliklerine sahip Logic Apps 'te *özel bir sertifika* kullanmak için önce sertifikayı tümleştirme hesabınıza yüklemeniz gerekir. Ayrıca, [Azure Key Vault](../key-vault/key-vault-get-started.md)için ilk eklediğiniz özel bir anahtarınız olması gerekir. 

Oluşturduğunuz [anlaşmalarda](logic-apps-enterprise-integration-agreements.md) özellikleri tanımladıktan sonra, B2B iletilerinizi güvenli hale getirmenize yardımcı olmak için sertifika kullanılabilir.

> [!NOTE]
> Özel sertifikalar için, [AS2 sözleşmesinin](logic-apps-enterprise-integration-as2.md) iletileri imzalama ve şifreleme ayarlarında görünen karşılık gelen bir ortak sertifika eklediğinizden emin olun.

1. [Özel anahtarınızı Azure Key Vault ekleyin](../key-vault/certificate-scenarios.md#import-a-certificate) ve **anahtar adı**belirtin.
   
2. Azure Key Vault üzerinde işlem gerçekleştirmek için Azure Logic Apps Yetkilendir. Logic Apps Hizmet sorumlusuna erişim vermek için, [set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)PowerShell komutunu kullanın, örneğin:

   `Set-AzKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. [Azure Portal](https://portal.azure.com) oturum açın. Ana Azure menüsünde **tüm kaynaklar**' ı seçin. Arama kutusuna, tümleştirme hesabınızın adını girin ve ardından istediğiniz tümleştirme hesabını seçin.

   ![Tümleştirme hesabınızı bulun](media/logic-apps-enterprise-integration-certificates/select-integration-account.png) 

4. **Bileşenler**bölümünde **Sertifikalar** kutucuğunu seçin.  

   ![Sertifikalar kutucuğunu seçin](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

5. **Sertifikalar**altında **Ekle**' yi seçin. **Sertifika ekle**' nin altında, sertifikanız için bu ayrıntıları sağlayın. İşiniz bittiğinde **Tamam**' ı seçin.

   | Özellik | Value | Açıklama | 
   |----------|-------|-------------|
   | **Name** | <*Sertifika adı*> | Bu örnekte, sertifikanızın adı "privateCert" | 
   | **Sertifika türü** | Özel | Sertifikanızın türü |
   | **Sertifika** | <*sertifika-dosya adı*> | Karşıya yüklemek istediğiniz sertifika dosyasını bulmak ve seçmek için, **sertifika** kutusunun yanındaki klasör simgesini seçin. Özel anahtar için bir Anahtar Kasası kullanırken, karşıya yüklenen dosya ortak sertifika olacaktır. | 
   | **Kaynak Grubu** | <*Integration-Account-Resource-Group*> | Bu örnekte "MyResourceGroup" olan tümleştirme hesabınızın kaynak grubu | 
   | **Anahtar Kasası** | <*Anahtar Kasası adı*> | Azure anahtar kasasının adı |
   | **Anahtar adı** | <*key-name*> | Anahtarınızın adı |
   ||||

   !["Ekle" seçeneğini belirleyin, sertifika bilgilerini sağlayın](media/logic-apps-enterprise-integration-certificates/private-certificate-details.png)

   Azure seçiminizi doğruladıktan sonra, Azure sertifikanızı karşıya yükler.

   ![Azure yeni sertifika görüntülüyor](media/logic-apps-enterprise-integration-certificates/new-private-certificate.png) 

## <a name="next-steps"></a>Sonraki adımlar

* [B2B sözleşmesi oluşturma](logic-apps-enterprise-integration-agreements.md)
