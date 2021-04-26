---
title: 'Hızlı başlangıç: Azure Active Directory kiracı oluşturma'
titleSuffix: Microsoft identity platform
description: Bu hızlı başlangıçta, kimlik doğrulaması ve yetkilendirme için Microsoft Identity platformunu kullanan uygulamalar geliştirmede kullanılmak üzere Azure Active Directory kiracı oluşturmayı öğreneceksiniz.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: quickstart
ms.date: 02/15/2021
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: cb4995c0c71d0bcebb3cdd8ec471a92b7820cb9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100535182"
---
# <a name="quickstart-set-up-a-tenant"></a>Hızlı Başlangıç: Kiracıyı ayarlama

Kimlik ve erişim yönetimi için Microsoft Identity platformunu kullanan uygulamalar oluşturmak için bir Azure Active Directory (Azure AD) *kiracısına* erişmeniz gerekir. Uygulamalarınızı kaydedip yönettiğiniz, Microsoft 365 ve diğer Web API 'Lerinde verilere erişimini yapılandıran ve koşullu erişim gibi özellikleri etkinleştiren Azure AD kiracısında.

Kiracı bir kuruluşu temsil eder. Bu, bir kuruluşun veya uygulama geliştiricisinin Microsoft ile bir ilişkinin başlangıcında aldığı adanmış bir Azure AD örneğidir. Bu ilişki, örneğin Azure, Microsoft Intune veya Microsoft 365 kaydolma ile başlayabilir.

Her Azure AD kiracısı diğer Azure AD kiracılarından ayrıdır. Kendi iş ve okul kimlikleri, tüketici kimlikleri (bir Azure AD B2C kiracı ise) ve uygulama kayıtları için kendi temsili vardır. Kiracınızda bir uygulama kaydı, yalnızca kiracınızdaki veya tüm kiracılarınızdaki hesaplardan gelen kimlik doğrulamaları için izin verebilir.

## <a name="prerequisites"></a>Önkoşullar

Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="determining-the-environment-type"></a>Ortam türünü belirleme

İki tür ortam oluşturabilirsiniz. Ortam, yalnızca uygulamanızın kimlik doğrulaması yapılacak Kullanıcı türlerine bağlıdır. 

Bu hızlı başlangıçta, derlemek istediğiniz uygulama türü için iki senaryo ele alınmaktadır:

* İş ve okul (Azure AD) hesapları veya Microsoft hesapları (örneğin, Outlook.com ve Live.com)
* Sosyal ve yerel (Azure AD B2C) hesaplar

## <a name="work-and-school-accounts-or-personal-microsoft-accounts"></a>İş ve okul hesapları veya kişisel Microsoft hesapları

İş ve okul hesapları ya da kişisel Microsoft hesapları için bir ortam oluşturmak üzere mevcut bir Azure AD kiracısı kullanabilir veya yeni bir tane oluşturabilirsiniz.
### <a name="use-an-existing-azure-ad-tenant"></a>Mevcut bir Azure AD kiracısı kullanma

Birçok geliştirici, Microsoft 365 veya Azure abonelikleri gibi Azure AD kiracılarına bağlı hizmetler veya abonelikler aracılığıyla kiracıları zaten vardır.

Kiracıyı denetlemek için:

1. <a href="https://portal.azure.com/" target="_blank">Azure portalında</a> oturum açın. Uygulamanızı yönetmek için kullanacağınız hesabı kullanın.
1. Sağ üst köşeyi kontrol edin. Bir kiracınız varsa, otomatik olarak oturumunuz açık olur. Kiracı adını doğrudan hesap adınızın altına görürsünüz.
   * Adınızı, e-posta adresinizi, dizininizi veya kiracı KIMLIĞINIZI (bir GUID) ve etki alanını görmek için hesap adınızın üzerine gelin.
   * Hesabınız birden çok kiracıyla ilişkiliyse, kiracılar arasında geçiş yapabileceğiniz bir menüyü açmak için hesap adınızı seçebilirsiniz. Her kiracının kendi kiracı kimliği vardır.

> [!TIP]
> Kiracı KIMLIĞINI bulmak için şunları yapabilirsiniz:
> * Dizin veya kiracı KIMLIĞINI almak için hesap adınızın üzerine gelin.
> * Azure Portal **Azure Active Directory**  >  **özellikleri**  >  **Kiracı kimliği** ' ni arayın ve seçin.

Hesabınızla ilişkili bir kiracınız yoksa, hesap adınızın altında bir GUID görürsünüz. Bir Azure AD kiracısı oluşturana kadar uygulama kaydetme gibi eylemler yapamazsınız.

### <a name="create-a-new-azure-ad-tenant"></a>Yeni Azure AD kiracısı oluşturma

Zaten bir Azure AD kiracınız yoksa veya geliştirme için yeni bir tane oluşturmak istiyorsanız bkz. [Azure AD 'de yeni kiracı oluşturma](../fundamentals/active-directory-access-create-new-tenant.md). Veya Azure portal [Dizin oluşturma deneyimini](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) kullanın. 

Yeni kiracınızı oluşturmak için aşağıdaki bilgileri sağlayın:

- **Kuruluş adı**
- **İlk etki alanı** -bu etki alanı *. onmicrosoft.com ' nin parçasıdır. Etki alanını daha sonra özelleştirebilirsiniz.
- **Ülke veya bölge**

> [!NOTE]
> Kiracınızı adlandırırken, alfasayısal karakterler kullanın. Özel karakterlere izin verilmez. Ad 256 karakterden uzun olmamalıdır.

## <a name="social-and-local-accounts"></a>Sosyal ve yerel hesaplar

Sosyal ve yerel hesaplarda oturum açarken uygulamalar oluşturmaya başlamak için Azure AD B2C kiracı oluşturun. Başlamak için bkz. [Azure AD B2C kiracı oluşturma](../../active-directory-b2c/tutorial-create-tenant.md).

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> Microsoft Identity platform ile tümleştirilecek [uygulamayı kaydedin](quickstart-register-app.md) .
