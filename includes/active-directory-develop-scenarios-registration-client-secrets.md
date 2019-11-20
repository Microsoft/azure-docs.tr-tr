---
title: include dosyası
description: Gizli istemci senaryosu giriş sayfaları için dosya ekleme (Daemon, Web uygulaması, Web API)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 8f98808aa0f8a2c32e2117447824114747091a82
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68912517"
---
## <a name="registration-of-secrets-or-certificates"></a>Parolaların veya sertifikaların kaydı

Gizli istemci uygulamaları gibi, bir gizli dizi veya sertifika kaydetmeniz gerekir. Uygulama gizli dizilerini [Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview)etkileşimli deneyim aracılığıyla veya komut satırı araçlarını (PowerShell gibi) kullanarak kaydedebilirsiniz

### <a name="registering-client-secrets-using-the-application-registration-portal"></a>Uygulama kayıt portalını kullanarak istemci gizli dizilerini kaydetme

İstemci kimlik bilgilerinin yönetimi, bir uygulamanın **sertifikalar & gizli** dizileri sayfasında oluşur:

![image](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets.png)

- Gizli istemci uygulamasının kaydı sırasında, uygulama gizli anahtarı (istemci gizli dizisi olarak da adlandırılır) Azure AD tarafından oluşturulur. **Yeni istemci parolası**' nı seçtiğinizde bu oluşturma gerçekleşir. Bu noktada, **Kaydet**' i seçmeden önce panodaki gizli dizesini uygulamanızda kullanmak üzere kopyalamanız gerekir. Bu dize daha fazla sunulmaz.
- Sertifika, **Sertifika Yükle** düğmesi kullanılarak uygulama kaydına yüklenir. Azure AD yalnızca uygulamada doğrudan kayıtlı olan sertifikaları destekler ve sertifika zincirlerini takip etmez.

Ayrıntılar için bkz [. hızlı başlangıç: İstemci uygulamasını Web API 'Lerine erişecek şekilde yapılandırma | Uygulamanıza kimlik bilgileri ekleme](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application)



### <a name="registering-client-secrets-using-powershell"></a>PowerShell kullanarak istemci gizli dizileri kaydetme

Alternatif olarak, komut satırı araçlarını kullanarak uygulamanızı Azure AD 'ye kaydedebilirsiniz. [Active-Directory-dotnetcore-Daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) örneği, bir uygulama parolasının veya bir SERTIFIKANıN Azure AD uygulaması ile nasıl kaydedileceği gösterilmektedir:

- Uygulama gizliliğini kaydetme hakkında ayrıntılı bilgi için bkz [. Appcreationscripts/configure. ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190)
- Uygulamayla bir sertifikayı kaydetme hakkında ayrıntılı bilgi için bkz [. AppCreationScripts-withCert/configure. ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178)
