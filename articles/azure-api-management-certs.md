---
title: Azure hizmet yönetimi sertifikasını karşıya yükleme | Microsoft Docs
description: Azure portal için hizmet yönetimi sertifikasını karşıya yüklemeyi öğrenin.
services: cloud-services
documentationcenter: .net
author: georgewallace
manager: carmonm
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: article
ms.date: 08/01/2017
ms.author: gwallace
ms.openlocfilehash: 5ce96796cbfdcefbaf5568ff199cba6a87f65e05
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359881"
---
# <a name="upload-an-azure-service-management-certificate"></a>Azure hizmet yönetimi sertifikasını karşıya yükleme
Yönetim sertifikaları, Azure tarafından sunulan klasik dağıtım modeliyle kimlik doğrulaması yapmanıza olanak sağlar. Birçok program ve araç (Visual Studio veya Azure SDK gibi), çeşitli Azure hizmetlerinin yapılandırmasını ve dağıtımını otomatik hale getirmek için bu sertifikaları kullanır. 

> [!WARNING]
> Dikkat et! Bu tür sertifikalar, bunlarla ilişkili oldukları aboneliği yönetmek için kimliğini doğrulayan herkese izin verir.
>
>

Azure sertifikaları (otomatik olarak imzalanan sertifika oluşturma dahil) hakkında daha fazla bilgi istiyorsanız bkz. [azure Cloud Services sertifikalara genel bakış](cloud-services/cloud-services-certs-create.md#what-are-management-certificates).

Otomasyon amacıyla istemci kodunun kimliğini doğrulamak için [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) de kullanabilirsiniz.

**Not:** Yönetim sertifikaları altında herhangi bir işlem gerçekleştirmek için abonelikte ortak yönetici olmanız gerekir. Yeni Azure portalından ortak yönetici ekleme veya kaldırma hakkında [daha fazla bilgi edinin](https://go.microsoft.com/fwlink/?linkid=849300) 

## <a name="upload-a-management-certificate"></a>Yönetim sertifikasını karşıya yükleme
Bir yönetim sertifikanız oluşturulduktan sonra (yalnızca ortak anahtarla. cer dosyası) portala karşıya yükleyebilirsiniz. Sertifika portalda kullanılabilir olduğunda, eşleşen sertifikaya sahip olan herkes (özel anahtar) Yönetim API 'sinden bağlanabilir ve ilişkili aboneliğin kaynaklarına erişebilir.

1. [Azure Portal](https://portal.azure.com)’da oturum açın.
2. Alt Azure hizmeti listesinde **tüm hizmetler** ' e tıklayın ve ardından _genel_ hizmet grubundaki **abonelikler** ' i seçin.

    ![Abonelik menüsü](./media/azure-api-management-certs/subscriptions_menu.png)

3. Sertifikayla ilişkilendirmek istediğiniz doğru aboneliği seçtiğinizden emin olun.     
4. Doğru aboneliği seçtikten sonra, _Ayarlar_ grubunda **yönetim sertifikaları** ' na basın.

    ![Ayarlar](./media/azure-api-management-certs/mgmtcerts_menu.png)

5. **Karşıya yükle** düğmesine basın.

    ![Sertifikalar sayfasında karşıya yükle sayfası](./media/azure-api-management-certs/certificates_page.png)
6. İletişim bilgilerini doldurup **karşıya yükle**' ye basın.

    ![Ayarlar](./media/azure-api-management-certs/certificate_details.png)

## <a name="next-steps"></a>Sonraki adımlar
Artık bir abonelikle ilişkili bir yönetim sertifikanız olduğuna göre, (eşleşen sertifikayı yerel olarak yükledikten sonra), otomatik olarak [klasik dağıtım modeline bağlanıp REST API](/azure/#pivot=sdkstools) ve çeşitli Azure kaynaklarını otomatikleştirin aynı zamanda bu abonelikle de ilişkilendirilir.
