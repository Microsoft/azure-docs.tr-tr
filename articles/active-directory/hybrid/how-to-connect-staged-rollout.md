---
title: 'Azure AD Connect: bulut kimlik doğrulaması ile hazırlanan piyasaya çıkma | Microsoft Docs'
description: Federal Kimlik doğrulamasından, hazırlanan bir dağıtım kullanılarak bulut kimlik doğrulamasına nasıl geçiş yapılacağını açıklar.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2596091324acde5c4fdc3f7c467849f90266fec9
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847222"
---
# <a name="cloud-authentication-staged-rollout-public-preview"></a>Bulut kimlik doğrulaması: hazırlanan dağıtım (Genel Önizleme)

Bu özellik, hazırlanmış bir yaklaşım kullanarak federal kimlik doğrulamasından bulut kimlik doğrulamasına geçiş yapmanıza olanak sağlar.

Federal Kimlik doğrulamasından uzaklaşmak için bazı etkileri vardır. Örneğin, aşağıdakilerden biri varsa:
    
-  Şirket içi MFA sunucusu 
-  kimlik doğrulaması için akıllı kartları kullanıyor 
-  yalnızca diğer Federasyon özellikleri

Bu özelliklerin bulut kimlik doğrulamasına geçmeden önce dikkate alınması gerekir.  Bu özelliği denemeden önce doğru kimlik doğrulama yöntemini seçme Kılavuzumuzu incelemenizi öneririz. Daha fazla ayrıntı için [Bu tabloya](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn#comparing-methods) bakın.

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>Ön koşullar

-   Federasyon etki alanları içeren bir Azure AD kiracınız var.

-   Parola karması eşitleme + sorunsuz SSO **(seçenek A)** ya da geçişli kimlik doğrulaması + sorunsuz SSO **(seçenek B)** öğesine geçmeye karar verdiniz. Kusursuz SSO isteğe bağlı olsa da, kurumsal ağ içinden etki alanına katılmış makineler kullanan kullanıcılar için sessiz bir oturum açma deneyimi elde etmek üzere sorunsuz SSO 'yu etkinleştirmenizi öneririz.

-   Bulut kimlik doğrulamasına geçirilmiş kullanıcılar için gereken tüm uygun kiracı markalamasını ve koşullu erişim ilkelerini yapılandırdınız.

-   Azure Multi-Factor Authentication kullanmayı planlıyorsanız, kullanıcılarınızın kimlik doğrulama yöntemlerini bir kez kaydetmesini sağlamak için [self servis parola sıfırlama (SSPR) ve Azure MFA için yakınsama kaydı](../authentication/concept-registration-mfa-sspr-combined.md) kullanmanızı öneririz.

-   Bu özelliği kullanmak için kiracınızda genel yönetici olmanız gerekir.

-   Belirli bir AD ormanında sorunsuz SSO 'yu etkinleştirmek için etki alanı yöneticisi olmanız gerekir.

## <a name="supported-scenarios"></a>Desteklenen senaryolar

Bu senaryolar, hazırlanan dağıtım için desteklenir:

- Bu özellik yalnızca Azure AD Connect kullanılarak Azure AD 'ye sağlanan kullanıcılar için geçerlidir ve yalnızca bulutta bulunan kullanıcılar için geçerli değildir.

- Bu özellik yalnızca tarayıcılardaki ve modern kimlik doğrulama istemcilerindeki Kullanıcı oturum açma trafiği için geçerlidir. Eski kimlik doğrulaması kullanan uygulamalar veya bulut Hizmetleri, federal kimlik doğrulama akışlarına geri döner. (Örnek: Modern kimlik doğrulaması kapalıyken Exchange Online veya modern kimlik doğrulamasını desteklemeyen Outlook 2010.)

## <a name="unsupported-scenarios"></a>Desteklenmeyen senaryolar

Bu senaryolar, hazırlanan dağıtım için desteklenmez:

- Belirli uygulamalar, kimlik doğrulama sırasında "etki alanı\_ipucu" sorgu parametresini Azure AD 'ye gönderir. Bu akışlar devam eder ve hazırlanan dağıtım için etkinleştirilen kullanıcılar kimlik doğrulaması için Federasyon kullanmaya devam eder.

<!-- -->

- Yönetici, güvenlik gruplarını kullanarak bulut kimlik doğrulamasını alabilir. (Bulut güvenlik grupları, şirket içi AD güvenlik grupları kullanılırken eşitleme gecikmesini önlemek için önerilir.)

    - **Her özellik için en fazla 10 grup**kullanabilirsiniz; Yani, her Parola karması eşitleme/geçişli kimlik doğrulaması/sorunsuz SSO için.

    - İç içe gruplar **desteklenmiyor**. Bu, genel önizlemeye yönelik kapsamdır.

    - Dinamik Gruplar, hazırlanan dağıtım için **desteklenmez** .

    - Grup içindeki kişi nesneleri, eklenen grup formunun engellenmesini engeller.

- Federe 'dan bulut kimlik doğrulamasına kadar olan son tam geçişi 'in hala Azure AD Connect veya PowerShell kullanılarak gerçekleştirilmesi gerekir. Bu özellik etki alanlarını Federasyondan yönetilene geçmez.

- Hazırlanmış dağıtım için ilk olarak bir güvenlik grubu eklediğinizde, UX 'in zaman aşımına uğramaması için 200 kullanıcıyla sınırlıdır. Grup UX 'e eklendikten sonra, gerekli şekilde gruba daha fazla kullanıcı ekleyebilirsiniz.

## <a name="get-started-with-staged-rollout"></a>Hazırlanan piyasaya çıkma ile çalışmaya başlama

Hazırlanan dağıtım kullanarak parola karması eşitleme (PHS) oturum açma sınamasını yapmak istiyorsanız, Parola karması eşitleme aşamalı dağıtımını etkinleştirmek için lütfen aşağıdaki ön çalışmayı doldurun.

Kullanılan PowerShell cmdlet 'leri hakkında daha fazla bilgi için bkz. [Azuread 2,0 Preview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)

## <a name="pre-work-for-password-hash-sync"></a>Parola karması eşitleme için önceden çalışma

1. Azure AD Connect ' de sayfada bulunan [Isteğe bağlı özelliklerden](how-to-connect-install-custom.md#optional-features) parola karma eşitlemesini etkinleştirin. 

   ![Azure Active Directory Connect 'de Isteğe bağlı özellikler sayfasının ekran görüntüsü](media/how-to-connect-staged-rollout/sr1.png)

1. Tüm kullanıcıların parola karmalarını Azure AD 'ye eşitlenebilmesi için tam Parola karması eşitleme döngüsünün çalışır durumda olduğundan emin olun. Parola karması eşitleme durumunu denetlemek için [buradaki](tshoot-connect-password-hash-synchronization.md) PowerShell tanılamayı kullanabilirsiniz.

   ![AADConnect sorun giderme günlüğünün ekran görüntüsü](./media/how-to-connect-staged-rollout/sr2.png)

Hazırlanan dağıtım kullanarak doğrudan kimlik doğrulama (PTA) oturum açma sınamasını test etmek istiyorsanız lütfen, hazırlanan dağıtım için PTA 'yı etkinleştirmek üzere aşağıdaki ön çalışmayı doldurun.

## <a name="pre-work-for-pass-through-authentication"></a>Doğrudan kimlik doğrulaması için önceden çalışma

1. Kimlik doğrulama aracısının çalışmasını istediğiniz Windows Server 2012 R2 veya üstünü çalıştıran bir sunucuyu belirleyin (**Azure AD Connect sunucusunu seçme**). Sunucunun etki alanına katılmış olduğundan, Active Directory Seçili kullanıcıların kimliğini doğrulayabilmesi ve giden bağlantı noktalarında/URL 'lerde Azure AD ile iletişim kurabildiğinden emin olun (bkz. ayrıntılı [Önkoşullar](how-to-connect-sso-quick-start.md)).

1.  [indirin](https://aka.ms/getauthagent) & sunucuya Microsoft Azure AD Connect kimlik doğrulama aracısını yükleyin. 

1.  [Yüksek kullanılabilirliği](how-to-connect-sso-quick-start.md)etkinleştirmek için diğer sunuculara ek kimlik doğrulama aracıları yüklersiniz.

1. [Akıllı kilitleme ayarlarınızı](../authentication/howto-password-smart-lockout.md) uygun şekilde yapılandırdığınızdan emin olun. Bu, kullanıcılarınızın şirket içi Active Directory hesaplarının kötü aktör tarafından kilitlenmemesini sağlamaktır.

Hazırlanmış dağıtım için seçtiğiniz oturum açma yönteminden (PHS veya PTA) bağımsız olarak sorunsuz SSO etkinleştirmeniz önerilir. Lütfen, hazırlanan dağıtım için sorunsuz SSO 'yu etkinleştirmek üzere aşağıdaki ön çalışmayı doldurun.

## <a name="pre-work-for-seamless-sso"></a>Sorunsuz SSO için önceden çalışma

PowerShell kullanarak AD ormanlarda sorunsuz SSO 'yu etkinleştirin. Birden fazla AD ormanınız varsa, lütfen her orman için aynı olanı tek tek etkinleştirin. Sorunsuz SSO yalnızca, hazırlanan dağıtım için seçilen kullanıcılara tetiklenir ve mevcut Federasyon kurulumunuzu etkilemez.

**Adımların Özeti**

1. İlk olarak Azure AD Connect sunucuda oturum açın.

2. % ProgramFiles%\\Microsoft Azure Active Directory Connect klasörüne gidin.

3. Şu komutu kullanarak sorunsuz SSO PowerShell modülünü içeri aktarın: `Import-Module .\AzureADSSO.psd1`.

4. PowerShell 'i yönetici olarak çalıştırın. PowerShell 'de `New-AzureADSSOAuthenticationContext`çağırın. Bu komut, kiracınızın genel yönetici kimlik bilgilerini girebileceğiniz bir iletişim kutusu vermelidir.

5.  `Get-AzureADSSOStatus | ConvertFrom-Json`çağırın. Bu komut, bu özelliğin etkinleştirildiği AD ormanları listesini (\"etki alanları\" listesine bakın) sağlar. Varsayılan olarak, kiracı düzeyinde false olarak ayarlanır.

   > **Örnek:** 
   > ![Windows PowerShell çıkışının örneğini](./media/how-to-connect-staged-rollout/sr3.png)

6.  `$creds = Get-Credential`çağırın. İstendiğinde, hedeflenen AD ormanının etki alanı yönetici kimlik bilgilerini girin.

7. `Enable-AzureADSSOForest -OnPremCredentials $creds`çağırın. Bu komut, sorunsuz SSO için gerekli olan bu Active Directory orman için şirket içi etki alanı denetleyicisinden AZUREADSSOACC bilgisayar hesabını oluşturur.

8. Sorunsuz SSO, URL 'Lerin intranet bölgesinde olmasını gerektirir. Bu URL 'nin grup Ilkelerini kullanarak dağıtılması için lütfen [sorunsuz çoklu oturum açma hızlı başlangıç](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature) bölümüne bakın.

9.  Ayrıca, tüm yönergeler için sorunsuz SSO için [dağıtım planlarımızı](https://aka.ms/SeamlessSSODPDownload) indirebilirsiniz.

## <a name="enable-staged-rollout"></a>Hazırlanan dağıtımı etkinleştir

Bir gruptaki kullanıcı kümesine belirli bir özelliği (doğrudan kimlik doğrulama/Parola karması eşitlemesi/sorunsuz SSO) almak için aşağıdaki adımları kullanın:

### <a name="enable-the-staged-rollout-of-a-specific-feature-on-your-tenant"></a>Kiracınızda belirli bir özelliğin hazırlanmış dağıtımını etkinleştirme

Bu seçeneklerden birini dağıtabilirsiniz

-   Parola karması eşitleme + sorunsuz SSO **(seçenek A)**

-   Geçişli kimlik doğrulaması + sorunsuz SSO **(seçenek B)**

-   Parola karması eşitleme + geçişli kimlik doğrulaması + sorunsuz SSO **-\>** ***desteklenmez***

Şu adımları uygulayın:

1. Önizleme UX 'e erişmek için aşağıdaki URL 'YI kullanarak Azure AD portalında oturum açın.

   > <https://aka.ms/stagedrolloutux>

2. Yönetilen Kullanıcı oturumu açma için hazırlanan dağıtımı etkinleştir ' e tıklayın (Önizleme)

   *Örneğin:* (**B seçeneği**) parola KARMASı eşitlemesini ve sorunsuz SSO 'Yu etkinleştirmek Istiyorsanız lütfen parola karma eşitlemesini ve sorunsuz çoklu oturum açma özelliklerini aşağıda gösterildiği gibi **' açık '** ' a kaydırın.

   ![](./media/how-to-connect-staged-rollout/sr4.png)

   ![](./media/how-to-connect-staged-rollout/sr5.png)

3. Doğrudan kimlik doğrulamayı ve sorunsuz çoklu oturum açmayı etkinleştirmek için ilgili grupları özelliğe ekleyin. Lütfen güvenlik gruplarında UX zaman aşımını önlemek için başlangıçta 200 ' den fazla üye bulunmadığından emin olun.

   ![](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >Bir gruptaki üyeler, hazırlanan dağıtım için otomatik olarak etkinleştirilir. İç içe ve dinamik gruplar, hazırlanan dağıtım için desteklenmez.

## <a name="auditing"></a>Denetim

Hazırlanmış dağıtım için gerçekleştirdiğimiz farklı eylemler için denetim olaylarını etkinleştirdik.

- Parola karması eşitleme/geçişli kimlik doğrulaması/sorunsuz SSO için hazırlanan dağıtımı etkinleştirdiğinizde olay denetimi.

  >[!NOTE]
  >SeamlessSSO, Stagedpiyasaya **çıkma kullanılarak açıldığında** günlüğe kaydedilen denetim olayı.

  ![](./media/how-to-connect-staged-rollout/sr7.png)

  ![](./media/how-to-connect-staged-rollout/sr8.png)

- Parola karması eşitleme/geçişli kimlik doğrulaması/sorunsuz SSO 'ya bir grup eklendiğinde olay denetimi.

  >[!NOTE]
  >Hazırlanmış dağıtım için Parola karması eşitlemesine bir grup eklendiğinde günlüğe kaydedilen denetim olayı

  ![](./media/how-to-connect-staged-rollout/sr9.png)

  ![](./media/how-to-connect-staged-rollout/sr10.png)

- Gruba eklenen bir Kullanıcı hazırlanan dağıtım için etkinleştirildiğinde denetim olayı

  ![](media/how-to-connect-staged-rollout/sr11.png)

  ![](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>Doğrulama

Parola karması eşitleme veya doğrudan kimlik doğrulama ile oturum açma sınamasını yapmak için (Kullanıcı adı/parola oturum açma):

1. Extranet 'ten özel bir tarayıcı oturumunda <https://myapps.microsoft.com> gidin ve hazırlanan dağıtım için seçilen kullanıcı hesabının UserPrincipalName (UPN) değerini girin.

1. Kullanıcı hazırlanan dağıtım için hedeflendiyse, Kullanıcı Federe oturum açma sayfanıza yönlendirilmez ve bunun yerine Azure AD kiracı markalı oturum açma sayfasında oturum açması istenir.

1. [Azure AD oturum açma etkinlik raporunda](../reports-monitoring/concept-sign-ins.md) userPrincipalName ile filtreleyerek oturum açma 'nın başarıyla göründüğünden emin olun.

Sorunsuz SSO ile oturum açmayı test etmek için:

1. İntranetten özel bir tarayıcı oturumunda <https://myapps.microsoft.com>/bir kişiye gidin ve hazırlanan dağıtım için seçilen kullanıcı hesabının UserPrincipalName (UPN) değerini girin.

1. Kullanıcı sorunsuz SSO 'nun hazırlanmasını hedeflediyse, Kullanıcı "oturum açmaya çalışıyor..." sessizce oturum açmadan önce ileti.

1. [Azure AD oturum açma etkinlik raporunda](../reports-monitoring/concept-sign-ins.md) userPrincipalName ile filtreleyerek oturum açma 'nın başarıyla göründüğünden emin olun.

Federasyon sağlayıcılarında Kullanıcı oturum açma işlemlerinin hala devam ettiğinden emin olmak için:

[Bu yönergeleri](https://docs.microsoft.com/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events)kullanarak, seçili hazırlanan piyasaya çıkma kullanıcıları için AD FS hala devam eden Kullanıcı oturum açma işlemlerini izleyebilirsiniz. Bu üçüncü taraf Federasyon sağlayıcılarında bunu nasıl denettacağına ilişkin satıcı belgelerini denetleyin.

## <a name="roll-back"></a>Geri al

### <a name="remove-a-user-from-staged-rollout"></a>Bir kullanıcıyı hazırlanan piyasaya çıkarma

1.  Kullanıcının gruptan kaldırılması, Kullanıcı için hazırlanan dağıtımı devre dışı bırakır.

2.  Hazırlanmış dağıtım özelliğini devre dışı bırakmak istiyorsanız, lütfen aşamalı dağıtımı devre dışı bırakmak için özelliği **' kapalı '** durumuna kaydırın.


## <a name="frequently-asked-questions"></a>Sık sorulan sorular

-   **S: bir müşteri üretimde bu özelliği kullanabilir mi?**

-   Y: Evet, bu özellik üretim kiracınızda kullanılabilir, ancak ilk olarak bu özelliği test kiracınızda denemenizi öneririz.

-   **S: Bu özellik, bazı kullanıcıların federal kimlik doğrulaması ve diğer bulut kimlik doğrulamasını kullandığı kalıcı bir "ortak varlık" korumak için kullanılabilir mi?**

-   Y: Hayır, bu özellik, Federasyon 'dan bulut kimlik doğrulamasına geçiş yapmak ve sonunda bulut kimlik doğrulamasına geçmek için tasarlanmıştır. Bu, beklenmeyen kimlik doğrulama akışlarına yol açacağından kalıcı bir karma durum önermiyoruz.

-   **S: PowerShell 'i hazırlanan dağıtım gerçekleştirmek için kullanabilir miyim?**

-   Y: Evet, lütfen PowerShell 'i kullanarak [hazırlanan dağıtımı gerçekleştirin](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout).

## <a name="next-steps"></a>Sonraki Adımlar
- [AzureAD 2,0 Önizleme](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout )
