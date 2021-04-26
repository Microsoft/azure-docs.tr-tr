---
title: Microsoft kimlik doğrulaması kitaplığı 'na (MSAL) geçiş
titleSuffix: Microsoft identity platform
description: Microsoft kimlik doğrulama kitaplığı (MSAL) ve Azure AD kimlik doğrulama kitaplığı (ADAL) arasındaki farklar ve MSAL 'e geçiş yapma hakkında bilgi edinin.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/07/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 6517cdd7aafa7ae2fe351b349e62a66104469dcd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653788"
---
# <a name="migrate-applications-to-the-microsoft-authentication-library-msal"></a>Uygulamaları Microsoft kimlik doğrulama kitaplığı 'na geçirme (MSAL)

Birçok geliştirici Azure Active Directory kimlik doğrulama kitaplığı 'nı (ADAL) kullanarak oluşturulmuş ve dağıtılmış uygulamalar vardır. Artık Azure AD varlıklarının kimlik doğrulaması ve yetkilendirmesi için Microsoft kimlik doğrulama kitaplığı 'nı (MSAL) kullanmanızı öneririz.

ADAL yerine MSAL kullanarak:

- Daha geniş bir kimlik kümesinin kimliğini doğrulayabilirsiniz:
  - Azure AD kimlikleri
  - Microsoft hesapları
  - Azure AD B2C kullanarak sosyal ve yerel hesaplar
- Kullanıcılarınız en iyi çoklu oturum açma deneyimini alacak.
- Uygulamanız artımlı onayı etkinleştirebilir.
- Koşullu erişimin desteklenmesi daha kolay.
- Yeniliklerden faydalanabilirsiniz. Tüm Microsoft geliştirme çabaları artık MSAL 'e odaklandığından, ADAL içinde yeni bir özellik uygulanmaz.

**Msal, artık Microsoft Identity platformu ile kullanılmak üzere önerilen kimlik doğrulama kitaplığıdır**.

## <a name="migration-guidance"></a>Geçiş kılavuzu

Aşağıdaki makaleler MSAL 'e geçiş yapmanıza yardımcı olabilir:

- [MSAL.Android’e geçiş](migrate-android-adal-msal.md)
- [MSAL.iOS / macOS’e Geçiş](migrate-objc-adal-msal.md)
- [MSAL Java’ya geçiş](migrate-adal-msal-java.md)
- [MSAL.js'ye geçirme](msal-compare-msal-js-and-adal-js.md)
- [MSAL.NET'e geçirme](msal-net-migration.md)
- [MSAL Python’a geçiş](migrate-python-adal-msal.md)
- [Aracıları kullanarak Xamarin uygulamalarını MSAL.NET’e geçirme](msal-net-migration-ios-broker.md)

## <a name="frequently-asked-questions-faq"></a>Sık sorulan sorular (SSS)

__S: ADAL kullanım dışı mı?__  
C: Evet. 30 Haziran 2020 ' den itibaren, artık ADAL 'e yeni özellikler ekleyeceğiz. 30 Haziran 2022 ' e kadar ADAL 'e kritik güvenlik düzeltmeleri eklemeye devam edeceğiz. Bu tarihten sonra, ADAL kullanan uygulamalarınız çalışmaya devam eder, ancak en son özelliklerden yararlanmak ve güvende kalmak için MSAL sürümüne yükseltmeniz önerilir.

__S: var olan ADAL uygulamalarım çalışmayı durduracak mi?__  
C: Hayır. Mevcut uygulamalarınız değişiklik yapılmadan çalışmaya devam edecektir. 30 Haziran 2022 ' den fazla tutmaya planlandıysanız, uygulamalarınızı güvenli tutmak için MSAL 'ye yükseltmeniz ve var olan işlevleri sürdürmek için MSAL 'e geçiş yapmanız gerekir.

__S: Nasıl yaparım? uygulamalarımın ADAL kullandığını öğrensin mi?__  
Y: uygulamanın kaynak koduna sahipseniz, uygulamanın hangi kitaplığı kullandığını ve MSAL 'e nasıl geçireceğinizi belirlemenize yardımcı olması için yukarıdaki geçiş kılavuzlarıyla başvurabilirsiniz. ISV ile ortaklıyorsanız, MSAL 'e geçiş yolculuğunu anlamak için doğrudan bu kişilere ulaşmanız önerilir.

__S: neden MSAL 'e taşınmalıyım?__  
Y: MSAL artımlı onay, çoklu oturum açma ve belirteç önbelleği yönetimi dahil olmak üzere ADAL içinde olmayan yeni özellikler içerir. Ayrıca, ADAL 'ın aksine MSAL, 30 Haziran 2022 ' den sonraki güvenlik düzeltme eklerini almaya devam edecektir. [Daha fazla bilgi edinin](msal-overview.md).

__S: Microsoft, kendi uygulamalarını MSAL olarak güncelleştirecek mi?__  
Evet. Microsoft, destek son tarihine kadar uygulamalarını MSAL 'e geçirme sürecinde, bu da MSAL 'ın devam eden güvenlik ve özellik geliştirmelerinden faydalanabilir.

__S: uygulamalarımı ADAL 'ten MSAL 'ye taşımaya yardımcı olacak bir araç serbest bıraksın mı?__  
C: Hayır. Kitaplıklar arasındaki farklılıklar, başka bir şekilde MSAL iyileştirmek için harcanacak olan aracı geliştirme ve bakım için ayrılmış kaynaklar gerektirir. Ancak, uygulamanızda gerekli değişiklikleri yapmanıza yardımcı olmak için önceki geçiş kılavuzu kümesini sağlıyoruz.

__S: MSAL nasıl çalışır? AD FS?__  
Y: MSAL.NET AD FS 2019 ' de kimlik doğrulaması yapmak için belirli senaryoları destekler. Uygulamanızın AD FS daha önceki bir sürümünden belirteç edinmesi gerekiyorsa, ADAL üzerinde kalabilmelisiniz. [Daha fazla bilgi edinin](msal-net-adfs-support.md).

__S: uygulamamı geçirirken yardım almak Nasıl yaparım??__  
A: Bu makalenin [Geçiş Kılavuzu](#migration-guidance) bölümüne bakın. Uygulamanızın platformu için kılavuzu okuduktan sonra ek sorularınız varsa, [Microsoft Q&A](/answers/topics/azure-ad-adal-deprecation.html) 'dan etiketiyle `[azure-ad-adal-deprecation]` veya kitaplığın GitHub deposunda bir sorun açabilirsiniz. Her kitaplığın deposunun bağlantıları için MSAL genel bakış makalesinin [Diller ve çerçeveler](msal-overview.md#languages-and-frameworks) bölümüne bakın.

## <a name="next-steps"></a>Sonraki adımlar

- [Uygulamalarınızı Microsoft kimlik doğrulama kitaplığı ve Microsoft Graph API kullanmak üzere güncelleştirme](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)
- [Microsoft Identity platformu 'na genel bakış](v2-overview.md)
- [MSAL Code örneklerimizi gözden geçirin](sample-v2-code.md)