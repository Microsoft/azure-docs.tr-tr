---
title: Ücretsiz Azure Active Directory geliştirici kiracısı oluşturma
description: Bu makalede bir geliştirici hesabının nasıl oluşturulacağı gösterilmektedir
services: active-directory
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: how-to
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: a50c8b083c1cd453dbe3c51c63ec9cf53859c3bd
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587275"
---
# <a name="how-to-create-a-free-azure-active-directory-developer-tenant"></a>Ücretsiz Azure Active Directory geliştirici kiracısı oluşturma

> [!IMPORTANT]
> Azure Active Directory doğrulanabilir kimlik bilgileri şu anda genel önizlemede.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!NOTE]
> Önizlemede bir P2 lisansı gerekir. 

P2 deneme lisansıyla ücretsiz Azure Active Directory oluşturmanın iki yolu vardır; bu sayede doğrulanabilir kimlik bilgisi veren hizmetini yükleyebilir ve doğrulanabilen kimlik bilgilerini oluşturma ve doğrulama sınamasını yapabilirsiniz:

- Ücretsiz Microsoft 365 Geliştirici programına [katılarak](https://aka.ms/o365devprogram) ücretsiz bir korumalı alan, araç ve P2 lisanslarıyla Azure Active Directory gibi diğer kaynakları alın. Yapılandırılmış kullanıcılar, gruplar, posta kutuları vb.
- Yeni bir [kiracı](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) oluşturun ve yeni kiracınızda Azure AD Premium P1 veya P2 [ücretsiz deneme](https://azure.microsoft.com/trial/get-started-active-directory/) sürümünü etkinleştirin.

Ücretsiz Microsoft 365 Geliştirici programına kaydolmaya karar verirseniz, birkaç kolay adımı izlemeniz gerekir:

1. Ekranda **Şimdi Birleştir** düğmesine tıklayın.

2. Yeni bir Microsoft hesabı ile oturum açın veya zaten sahip olduğunuz mevcut bir (iş) hesabı kullanın.

3. Kaydolma sayfasında bölgenizi seçin, bir şirket adı girin ve **İleri**' ye tıklamadan önce programın hüküm ve koşullarını kabul edin.

4. **Aboneliği ayarla**' ya tıklayın. Yeni kiracınızı oluşturmak istediğiniz bölgeyi belirtin, bir Kullanıcı adı, etki alanı oluşturun ve bir parola girin. Bu, yeni bir kiracı ve kiracının ilk Yöneticisi oluşturacak.

5. Yeni kiracınızın yönetici hesabını korumak için gereken güvenlik bilgilerini girin. Bu, hesap için MFA kimlik doğrulamasını ayarlar.


Bu noktada, 25 E5 kullanıcı lisansına sahip bir kiracı oluşturdunuz. E5 lisansları Azure AD P2 lisanslarını içerir. İsteğe bağlı olarak, geliştirme ortamınızda test etmenize yardımcı olmak için kullanıcılar, gruplar, posta ve SharePoint ile örnek veri paketleri ekleyebilirsiniz. Doğrulanabilir kimlik bilgisi veren hizmeti için, bu gerekli değildir.

Size kolaylık olması için, yeni oluşturulan kiracıda kendi iş hesabınızı [Konuk](/azure/active-directory/external-identities/b2b-quickstart-add-guest-users-portal) olarak ekleyebilir ve bu hesabı kiracıyı yönetmek üzere kullanabilirsiniz. Konuk hesabının doğrulanabilir kimlik bilgisi hizmetini yönetebilmesini isterseniz, ' genel yönetici ' rolünü bu kullanıcıya atamanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Artık bir geliştirici hesabınız olduğuna göre, doğrulanabilen kimlik bilgileri hakkında daha fazla bilgi edinmek için [ilk Öğreticimizi](get-started-verifiable-credentials.md) deneyebilirsiniz.
