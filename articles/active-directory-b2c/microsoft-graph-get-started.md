---
title: Microsoft Graph uygulaması kaydetme
titleSuffix: Azure AD B2C
description: Gerekli Graph API izinleri verilen bir uygulamayı kaydederek Microsoft Graph Azure AD B2C kaynaklarını yönetmeye hazırlanın.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/05/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0dcb959184e12ffa22ae25443087684123598e47
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382479"
---
# <a name="register-a-microsoft-graph-application"></a>Microsoft Graph uygulaması kaydetme

[Microsoft Graph][ms-graph] , müşteri Kullanıcı hesapları ve özel ilkeler dahil olmak üzere Azure AD B2C kiracınızdaki kaynakların birçoğunu yönetmenizi sağlar. [MICROSOFT Graph API][ms-graph-api]'sini çağıran komut dosyaları veya uygulamalar yazarak, şu şekilde kiracı yönetim görevlerini otomatikleştirebilirsiniz:

* Mevcut bir Kullanıcı deposunu Azure AD B2C kiracısına geçirme
* Azure DevOps 'da bir Azure işlem hattı ile özel ilkeler dağıtma ve özel ilke anahtarlarını yönetme
* Kullanıcı kaydını kendi sayfanızda barındırın ve arka planda Azure AD B2C dizininizde Kullanıcı hesapları oluşturun
* Uygulama kaydını otomatikleştirme
* Denetim günlüklerini al

Aşağıdaki bölümler, Azure AD B2C dizininizdeki kaynakların yönetimini otomatikleştirmek için Microsoft Graph API 'sini kullanmaya hazırlanmanıza yardımcı olur.

## <a name="microsoft-graph-api-interaction-modes"></a>Microsoft Graph API etkileşim modları

Azure AD B2C kiracınızdaki kaynakları yönetmek için Microsoft Graph API 'siyle çalışırken kullanabileceğiniz iki iletişim modu vardır:

* **Etkileşimli** -bir kez çalıştır görevleri için, yönetim görevlerini GERÇEKLEŞTIRMEK üzere B2C kiracısında bir yönetici hesabı kullanın. Bu mod, bir yöneticinin Microsoft Graph API 'YI çağırmadan önce kimlik bilgilerini kullanarak oturum açmasını gerektirir.

* **Otomatikleştirilmiş** -zamanlanmış veya sürekli olarak çalıştırma görevleri için, bu yöntem yönetim görevlerini gerçekleştirmek için gereken izinlerle yapılandırdığınız bir hizmet hesabını kullanır. Uygulama *(istemci) kimliğini* ve **OAuth 2,0 istemci kimlik bilgilerini** kullanarak kimlik doğrulaması için kullanılan uygulama ve betiklerinizin bir uygulamayı kaydederek Azure AD B2C ' de "hizmet hesabı" oluşturursunuz. Bu durumda, uygulama, daha önce açıklanan etkileşimli yönteminde olduğu gibi yönetici kullanıcıyı değil Microsoft Graph API 'sini çağırmak için kendisini kendisi olarak çalışır.

Aşağıdaki bölümlerde gösterilen bir uygulama kaydı oluşturarak **Otomatik** etkileşim senaryosunu etkinleştirirsiniz.

OAuth 2,0 istemci kimlik bilgileri verme akışı şu anda Azure AD B2C kimlik doğrulama hizmeti tarafından doğrudan desteklenmese de, Azure AD 'yi kullanarak istemci kimlik bilgileri akışını ve Azure AD B2C kiracınızdaki bir uygulama için Microsoft Identity platform/Token uç noktasını ayarlayabilirsiniz. Azure AD B2C kiracı, Azure AD kurumsal kiracılar ile bazı işlevleri paylaşır.

## <a name="register-management-application"></a>Yönetim uygulamasını Kaydet

Komut dosyalarınız ve uygulamalarınızın Azure AD B2C kaynaklarını yönetmek için [MICROSOFT Graph API][ms-graph-api] 'siyle etkileşime girebilmesi için, Azure AD B2C kiracınızda gerekli API izinlerini veren bir uygulama kaydı oluşturmanız gerekir.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Portal araç çubuğunda **Dizin + abonelik** simgesini seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Azure portal, araması yapın ve **Azure AD B2C** seçin.
1. **Uygulama kayıtları** öğesini seçin ve ardından **Yeni kayıt**' ı seçin.
1. Uygulama için bir **ad** girin. Örneğin, *managementapp1*.
1. **Yalnızca bu kuruluş dizininde hesaplar '** ı seçin.
1. **İzinler** altında, *openıd ve offline_access izinleri Için yönetici izni ver* onay kutusunu temizleyin.
1. **Kaydet**’i seçin.
1. Uygulamaya Genel Bakış sayfasında görüntülenen **uygulama (istemci) kimliğini** kaydedin. Bu değeri sonraki bir adımda kullanırsınız.

## <a name="grant-api-access"></a>API erişimi verme

Uygulamanızın Microsoft Graph verilere erişmesi için, kayıtlı uygulamaya ilgili [uygulama izinlerini](https://docs.microsoft.com/graph/permissions-reference)verin. Uygulamanızın etkili izinleri, izin tarafından ima edilen ayrıcalıkların tam düzeyidir. Örneğin, Azure AD B2C kiracınızdaki her kullanıcıyı *oluşturmak*, *okumak*, *güncelleştirmek* ve *silmek* için **User. ReadWrite. All** iznini ekleyin. 

> [!NOTE]
> **User. ReadWrite. All** izni, Kullanıcı hesabı parolalarını Güncelleştir özelliğini içermez. Uygulamanızın Kullanıcı hesabı parolalarını güncelleştirmesi gerekiyorsa, [Kullanıcı Yöneticisi rolüne izin verin](#optional-grant-user-administrator-role). [Kullanıcı yönetici](../active-directory/roles/permissions-reference.md#user-administrator) rolü verilirken, **User. ReadWrite. All** gerekli değildir. Kullanıcı Yöneticisi rolü, kullanıcıları yönetmek için gereken her şeyi içerir.

Uygulamanıza birden çok uygulama izinleri verebilirsiniz. Örneğin, uygulamanızın Azure AD B2C kiracınızdaki grupları yönetmesi gerekiyorsa, **Group. ReadWrite. All** iznini de ekleyin. 

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]


## <a name="optional-grant-user-administrator-role"></a>Seçim Kullanıcı Yöneticisi rolü verme

Uygulamanızın veya betiğinizin kullanıcıların parolalarını güncelleştirmesi gerekiyorsa, uygulamanıza *Kullanıcı Yöneticisi* rolünü atamanız gerekir. [Kullanıcı Yöneticisi](../active-directory/roles/permissions-reference.md#user-administrator) rolü, uygulamanıza verdiğiniz sabit bir izin kümesine sahiptir. 

*Kullanıcı Yöneticisi* rolünü eklemek için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com) oturum açın ve **Dizin + abonelik** filtresini kullanarak Azure AD B2C kiracınıza geçiş yapın.
1. Arama yapın ve **Azure AD B2C** seçin.
1. **Yönet** altında **Roller ve yöneticiler**' i seçin.
1. **Kullanıcı Yöneticisi** rolünü seçin. 
1. **Atama Ekle**' yi seçin.
1. Metin **Seç** kutusuna daha önce kaydettiğiniz uygulamanın adını veya kimliğini girin, örneğin, *managementapp1*. Arama sonuçlarında göründüğünde uygulamanızı seçin.
1. **Add (Ekle)** seçeneğini belirleyin. İzinlerin tam olarak yayılması birkaç dakika sürebilir.

## <a name="create-client-secret"></a>İstemci parolası oluştur

Uygulamanız bir belirteç istenirken kimliğini kanıtlamak için bir istemci gizli anahtarı gerektirir. İstemci parolasını eklemek için şu adımları izleyin:

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]


## <a name="next-steps"></a>Sonraki adımlar

Yönetim uygulamanızı kaydoldığınıza ve gerekli izinleri vermiş olduğunuza göre, uygulama ve hizmetleriniz (örneğin, Azure Pipelines) Microsoft Graph API 'siyle etkileşim kurmak için kimlik bilgilerini ve izinlerini kullanabilir. 

* [Azure AD'den erişim belirteci alma](/graph/auth-v2-service#4-get-an-access-token)
* [Microsoft Graph çağırmak için erişim belirtecini kullanın](/graph/auth-v2-service#4-get-an-access-token)
* [Microsoft Graph tarafından desteklenen B2C işlemleri](microsoft-graph-operations.md)
* [Microsoft Graph ile Azure AD B2C Kullanıcı hesaplarını yönetme](microsoft-graph-operations.md)
* [Azure AD Raporlama API 'SI ile denetim günlüklerini alın](view-audit-logs.md#get-audit-logs-with-the-azure-ad-reporting-api)

<!-- LINKS -->
[ms-graph]: /graph/
[ms-graph-api]: /graph/api/overview
