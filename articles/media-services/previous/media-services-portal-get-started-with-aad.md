---
title: Azure portal kullanarak Azure AD kimlik doğrulaması ile çalışmaya başlama | Microsoft Docs
description: Azure Media Services API 'sini kullanmak için Azure Active Directory (Azure AD) kimlik doğrulamasına erişmek üzere Azure portal nasıl kullanacağınızı öğrenin.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: d9ad439fe3f41d9e2634fbf9a76cfd21114a5dbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103013066"
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Azure portalını kullanarak Azure AD kimlik doğrulamasını kullanmaya başlama

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Media Services v2’ye herhangi bir yeni özellik veya işlevsellik eklenmemektedir. <br/>[V3 Media Services](../latest/index.yml)en son sürüme göz atın. Ayrıca bkz. [v2 'den v3 'e geçiş kılavuzu](../latest/migrate-v-2-v-3-migration-introduction.md)

Azure Media Services API 'sine erişmek için Azure Active Directory (Azure AD) kimlik doğrulamasına erişmek üzere Azure portal nasıl kullanacağınızı öğrenin.

## <a name="prerequisites"></a>Önkoşullar

- Azure hesabı. Hesabınız yoksa [Azure Ücretsiz deneme sürümü](https://azure.microsoft.com/pricing/free-trial/)ile başlayın. 
- Bir Media Services hesabı. Daha fazla bilgi için [Azure Portal kullanarak Azure Media Services hesabı oluşturma](media-services-portal-create-account.md)konusuna bakın.

Azure Media Services ile Azure AD kimlik doğrulaması kullandığınızda, iki kimlik doğrulama seçeneğiniz vardır:

- **Hizmet sorumlusu kimlik doğrulaması**. Bir hizmetin kimliğini doğrulayın. Bu kimlik doğrulama yöntemini yaygın olarak kullanan uygulamalar, Daemon Hizmetleri, orta katman hizmetler veya zamanlanan işleri çalıştıran uygulamalardır: Web Apps, işlev uygulamaları, Logic Apps, API 'Ler veya mikro hizmet.
- **Kullanıcı kimlik doğrulaması**. Media Services kaynaklarla etkileşim kurmak için uygulamayı kullanan bir kişinin kimliğini doğrulayın. Etkileşimli uygulama öncelikle kullanıcıdan kimlik bilgilerini istemelidir. Yetkili kullanıcılar tarafından kodlama işlerini veya canlı akışı izlemek için kullanılan yönetim konsolu uygulaması bunun bir örneğidir. 

## <a name="access-the-media-services-api"></a>Media Services API’sine erişim

Bu sayfa, API 'ye bağlanmak için kullanmak istediğiniz kimlik doğrulama yöntemini seçmenizi sağlar. Bu sayfa, API 'ye bağlanmak için gereken değerleri de sağlar.

1. [Azure portal](https://portal.azure.com/)Media Services hesabınızı seçin.
2. Media Services API 'sine nasıl bağlanılacağını seçin.
3. **MEDIA SERVICES API 'Sine Bağlan** altında, bağlanmak ISTEDIĞINIZ Media Services API sürümünü seçin.

## <a name="service-principal-authentication--recommended"></a>Hizmet sorumlusu kimlik doğrulaması (önerilir)

Bir Azure Active Directory (Azure AD) uygulaması ve gizli anahtarı kullanarak bir hizmetin kimliğini doğrular. Bu, Media Services API 'sine çağıran tüm orta katman hizmetleri için önerilir. Örnekler şunlardır Web Apps, Işlevler, Logic Apps, API 'Ler ve mikro hizmetlerdir. Bu, önerilen kimlik doğrulama yöntemidir.

### <a name="manage-your-azure-ad-app-and-secret"></a>Azure AD uygulamanızı ve gizli anahtarı yönetme

**AAD uygulamanızı ve gizli anahtarı yönetme** bölümü, yeni BIR Azure AD uygulaması seçmenize veya oluşturmanıza ve gizli anahtar oluşturmanıza imkan tanır. Güvenlik nedeniyle, dikey pencere kapatıldıktan sonra gizli anahtar gösterilemez. Uygulama, medya Hizmetleri için geçerli bir belirteç almak üzere kimlik doğrulaması için uygulama KIMLIĞI ve gizli anahtarını kullanır.

Azure AD kiracınızla bir uygulamayı kaydetmek ve uygulamayı Azure aboneliğinizdeki bir role atamak için yeterli izinlere sahip olduğunuzdan emin olun. Daha fazla bilgi için bkz. [gerekli izinler](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

### <a name="connect-to-media-services-api"></a>Media Services API 'sine bağlanma

**Media Services Connect API 'si** , hizmet sorumlusu uygulamanızı bağlamak için kullandığınız değerleri sağlar. Metin değerleri alabilir veya JSON veya XML bloklarını kopyalayabilirsiniz.

## <a name="user-authentication"></a>Kullanıcı kimlik doğrulaması

Bu seçenek, Media Services kaynaklarla etkileşim kurmak için uygulama kullanan bir Azure Active Directory çalışanın veya üyenin kimliğini doğrulamak için kullanılabilir. Etkileşimli uygulama öncelikle kullanıcıdan kullanıcının kimlik bilgilerini istemelidir. Bu kimlik doğrulama yöntemi yalnızca yönetim uygulamaları için kullanılmalıdır.

### <a name="connect-to-media-services-api"></a>Media Services API 'sine bağlanma

Kullanıcı uygulamanızı **MEDIA SERVICES API 'ye Bağlan** bölümüne bağlamak için kimlik bilgilerinizi kopyalayın. Metin değerleri alabilir veya JSON veya XML bloklarını kopyalayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

[Hesabınıza dosya yüklemeye](media-services-portal-upload-files.md)başlayın.
