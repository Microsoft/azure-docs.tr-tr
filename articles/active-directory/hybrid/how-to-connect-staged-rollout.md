---
title: 'Azure AD Connect: hazırlanan dağıtım aracılığıyla bulut kimlik doğrulaması | Microsoft Docs'
description: Bu makalede, hazırlanmış bir dağıtım kullanılarak federe kimlik doğrulamasından bulut kimlik doğrulamasına nasıl geçiş yapılacağı açıklanır.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/03/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 756e5e96a8040fb3d93273a5521236d46879e60d
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306390"
---
# <a name="migrate-to-cloud-authentication-using-staged-rollout"></a>Hazırlanan dağıtım kullanarak bulut kimlik doğrulamasına geçiş

Hazırlanan dağıtım, etki alanlarınızı kesmeden önce Azure AD Multi-Factor Authentication (MFA), koşullu erişim, sızdırılan kimlik bilgileri için kimlik koruması, kimlik yönetimi ve diğerleri gibi bulut kimlik doğrulama özelliklerine sahip kullanıcı gruplarını seçmeli olarak test etmenize olanak tanır.  Bu makalede, anahtarın nasıl yapılacağı açıklanır. Ancak, hazırlanan piyasaya başlamadan önce, aşağıdaki koşullardan biri veya daha fazlası doğru olduğunda etkilerini göz önünde bulundurmanız gerekir:
    
-  Şu anda bir şirket içi Multi-Factor Authentication sunucusu kullanıyorsunuz. 
-  Kimlik doğrulaması için akıllı kartlar kullanıyorsunuz. 
-  Geçerli sunucunuz yalnızca Federasyon özelliklerini sunmaktadır.

Bu özelliği denemeden önce doğru kimlik doğrulama yöntemini seçme Kılavuzumuzu incelemenizi öneririz. Daha fazla bilgi için, [Azure Active Directory hibrit kimlik çözümünüz için doğru kimlik doğrulama yöntemini seçme](./choose-ad-authn.md#comparing-methods)içindeki "metotları karşılaştırma" tablosuna bakın.

Özelliğe genel bir bakış için bu "Azure Active Directory: hazırlanan dağıtım nedir?" öğesini görüntüleyin. video

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>Önkoşullar

-   Federasyon etki alanları içeren bir Azure Active Directory (Azure AD) kiracınız vardır.

-   İki seçenekten birine taşımaya karar verdiniz:
    - **Seçenek A**  -  *Parola karması eşitleme (eşitleme)*  +  *sorunsuz çoklu oturum açma (SSO)*.  Daha fazla bilgi için bkz. [Parola karması eşitleme](whatis-phs.md) nedir ve [sorunsuz SSO nedir?](how-to-connect-sso.md)
    - **Seçenek B**  -  *geçişli kimlik doğrulaması*  +  *sorunsuz SSO*.  Daha fazla bilgi için bkz. [doğrudan kimlik doğrulama nedir](how-to-connect-pta.md)  
    
    *Kusursuz SSO* isteğe bağlı olsa da, bir kurumsal ağın içinden etki alanına katılmış makineler çalıştıran kullanıcılar için sessiz oturum açma deneyimi elde etmenizi öneririz.

-   Bulut kimlik doğrulamasına geçirilmekte olan kullanıcılar için gereken tüm uygun kiracı marka ve koşullu erişim ilkelerini yapılandırdınız.

-   Azure AD Multi-Factor Authentication kullanmayı planlıyorsanız, kullanıcılarınızın kimlik doğrulama yöntemlerini bir kez kaydetmesi için [self servis parola sıfırlama (SSPR) ve Multi-Factor Authentication Birleşik kayıt](../authentication/concept-registration-mfa-sspr-combined.md) kullanmanızı öneririz. Bu şekilde, hazırlanan piyasaya çıkma sırasında parola sıfırlama veya parola değiştirme için SSPR kullanırken Azure AD Connect, sıfırlama sonrasında 2 dakika sürebilen yeni parola karmasını eşitlemeniz gerekir.

-   Hazırlanan dağıtım özelliğini kullanmak için kiracınızda genel yönetici olmanız gerekir.

-   Belirli bir Active Directory ormanında *sorunsuz SSO* 'yu etkinleştirmek için, bir etki alanı yöneticisi olmanız gerekir.

-  Hibrit Azure AD veya Azure AD katılımı dağıtıyorsanız, Windows 10 1903 Güncelleştirmesi ' ne yükseltmeniz gerekir.


## <a name="supported-scenarios"></a>Desteklenen senaryolar

Hazırlanan dağıtım için aşağıdaki senaryolar desteklenir. Özelliği yalnızca için geçerlidir:

- Azure AD Connect kullanılarak Azure AD 'ye sağlanan kullanıcılar. Yalnızca bulutta bulunan kullanıcılar için geçerlidir.

- Tarayıcılarda ve *modern kimlik doğrulama* istemcilerinde Kullanıcı oturum açma trafiği. Eski kimlik doğrulaması kullanan uygulamalar veya bulut Hizmetleri, federal kimlik doğrulama akışlarına geri döner. Bir örnek, modern kimlik doğrulaması kapalı veya modern kimlik doğrulamayı desteklemeyen Outlook 2010 ile Exchange Online olabilir.

- Grup boyutu şu anda 50.000 kullanıcıyla sınırlıdır.  Daha büyük ve 50.000 Kullanıcı gruplarınız varsa, bu grubu hazırlanmış dağıtım için birden çok gruba bölmeniz önerilir.

- Windows 10 karma birleşimi veya Azure AD 'ye katılarak birincil yenileme belirteci alımı, Windows 10 sürüm 1903 ve daha yeni bir sürümü için federasyon sunucusuna, Kullanıcı UPN 'si yönlendirilebilir olduğunda ve Azure AD 'de etki alanı son eki doğrulandığında, ön ek.

## <a name="unsupported-scenarios"></a>Desteklenmeyen senaryolar

Aşağıdaki senaryolar hazırlanan dağıtım için desteklenmez:

- POP3 ve SMTP gibi eski kimlik doğrulaması desteklenmez.

- Belirli uygulamalar, kimlik doğrulama sırasında "domain_hint" sorgu parametresini Azure AD 'ye gönderir. Bu akışlar devam eder ve hazırlanan dağıtım için etkinleştirilen kullanıcılar kimlik doğrulaması için Federasyon kullanmaya devam eder.

<!-- -->

- Yöneticiler, güvenlik gruplarını kullanarak bulut kimlik doğrulamasını alabilir. Şirket içi Active Directory güvenlik grupları kullanırken eşitleme gecikmesini önlemek için, bulut güvenlik grupları kullanmanızı öneririz. Aşağıdaki koşullar geçerlidir:

    - Her özellik için en fazla 10 grup kullanabilirsiniz. Diğer bir deyişle, *Parola karması eşitleme*, *geçişli kimlik doğrulama* ve *sorunsuz SSO* için 10 grup kullanabilirsiniz.
    - İç içe gruplar *desteklenmiyor*. 
    - Dinamik Gruplar, hazırlanan dağıtım için *desteklenmez* .
    - Grup içindeki kişi nesneleri grubun eklenmesini engeller.

- Hazırlanmış dağıtım için ilk olarak bir güvenlik grubu eklediğinizde, bir UX zaman aşımını önlemek için 200 kullanıcıyla sınırlı olursunuz. Grubu ekledikten sonra, gerektiğinde doğrudan buna daha fazla kullanıcı ekleyebilirsiniz.

- Kullanıcılar hazırlanan piyasaya sürülirken, Enforcecıpasswordpolicyforpasswordsyncedusers etkinleştirildiğinde parola süre sonu ilkesi, özelleştirme seçeneği olmadan 90 gün olarak ayarlanır. 

- Windows 10 karma birleşimi veya Azure AD 'ye katılması birincil yenileme belirteci 1903 'den eski Windows 10 sürümü için alma. Bu senaryo, oturum açan kullanıcı tarafından hazırlanan dağıtım kapsamında olsa bile, Federasyon sunucusunun WS-Trust uç noktasına geri döner.

- Windows 10 hibrit JOIN veya Azure AD JOIN birincil yenileme belirteci alma, kullanıcının şirket içi UPN 'si yönlendirilebilir olmadığında. Bu senaryo, hazırlanan dağıtım modundayken WS-Trust uç noktasına geri dönecektir, ancak hazırlanan geçiş tamamlandığında ve Kullanıcı oturum açma artık federasyon sunucusuna bağlı olmadığında çalışmayı durduracaktır.

  >[!NOTE]
  >Azure AD Connect veya PowerShell kullanarak, Federasyon kimlik doğrulamasından en son tam geçişi ' i yine de yapmanız gerekir. Hazırlanan dağıtım etki alanlarını federe 'dan yönetilene geçmez.  Etki alanı cutover hakkında daha fazla bilgi için bkz. [Federasyon 'dan parola karma eşitlemesine geçiş](plan-migrate-adfs-password-hash-sync.md#step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso) ve [Federasyondan geçişli kimlik doğrulamaya](plan-migrate-adfs-pass-through-authentication.md#step-2-change-the-sign-in-method-to-pass-through-authentication-and-enable-seamless-sso)geçiş.
  
## <a name="get-started-with-staged-rollout"></a>Hazırlanan piyasaya çıkma ile çalışmaya başlama

Hazırlanan dağıtım kullanarak *Parola karması eşitlemesini* test etmek için, sonraki bölümde yer aldığı ön iş yönergelerini izleyin.

Hangi PowerShell cmdlet 'lerinin kullanılacağı hakkında daha fazla bilgi için bkz. [Azure AD 2,0 Preview](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#staged_rollout).

## <a name="pre-work-for-password-hash-sync"></a>Parola karması eşitleme için önceden çalışma

1. Azure AD Connect 'de [Isteğe bağlı özellikler](how-to-connect-install-custom.md#optional-features) sayfasından *Parola karması eşitlemesini* etkinleştirin. 

   ![Azure Active Directory Connect 'daki "Isteğe bağlı özellikler" sayfasının ekran görüntüsü](media/how-to-connect-staged-rollout/sr1.png)

1. Tüm kullanıcıların parola karmalarının Azure AD ile eşitlenmesi için tam *Parola karması eşitleme* döngüsünün çalıştığından emin olun. *Parola karması eşitleme* durumunu denetlemek için PowerShell tanılamayı, [Azure AD Connect Sync ile parola karması eşitleme sorunlarını gidermek](tshoot-connect-password-hash-synchronization.md)için kullanabilirsiniz.

   ![AADConnect sorun giderme günlüğünün ekran görüntüsü](./media/how-to-connect-staged-rollout/sr2.png)

Aşamalı dağıtım kullanarak *doğrudan kimlik doğrulaması* oturum açma 'yı test etmek istiyorsanız, sonraki bölümde yer aldığı ön iş yönergelerini izleyerek etkinleştirin.

## <a name="pre-work-for-pass-through-authentication"></a>Doğrudan kimlik doğrulaması için önceden çalışma

1. *Doğrudan kimlik doğrulama* aracısının çalışmasını Istediğiniz Windows Server 2012 R2 veya üstünü çalıştıran bir sunucu belirler. 

   Azure AD Connect *sunucusunu seçme.* Sunucunun etki alanına katılmış olduğundan, Active Directory Seçili kullanıcıların kimliğini doğrulayabilmesi ve giden bağlantı noktalarında ve URL 'Lerde Azure AD ile iletişim kurabildiğinden emin olun. Daha fazla bilgi için [hızlı başlangıç: Azure AD sorunsuz çoklu oturum açma](how-to-connect-sso-quick-start.md)konusunun "1. Adım: önkoşulları denetleme" bölümüne bakın.

1. [Azure AD Connect kimlik doğrulama aracısını indirip](https://aka.ms/getauthagent)sunucuya yükleyin. 

1. [Yüksek kullanılabilirliği](how-to-connect-sso-quick-start.md)etkinleştirmek için diğer sunuculara ek kimlik doğrulama aracıları yüklersiniz.

1. [Akıllı kilitleme ayarlarınızı](../authentication/howto-password-smart-lockout.md) uygun şekilde yapılandırdığınızdan emin olun. Bunun yapılması, kullanıcılarınızın şirket içi Active Directory hesaplarının kötü aktör tarafından kilitlenmemesini sağlamaya yardımcı olur.

Hazırlanan dağıtım için seçtiğiniz oturum açma yönteminden (*Parola karması eşitlemesi* veya *geçişli kimlik doğrulaması*) bağımsız olarak *sorunsuz SSO* etkinleştirmenizi öneririz. *Sorunsuz SSO*'yu etkinleştirmek için sonraki bölümde yer aldığı ön iş yönergelerini izleyin.

## <a name="pre-work-for-seamless-sso"></a>Sorunsuz SSO için önceden çalışma

PowerShell kullanarak Active Directory ormanlarda *sorunsuz SSO* 'yu etkinleştirin. Birden fazla Active Directory ormanına sahipseniz, her orman için tek tek etkinleştirin. *Sorunsuz SSO* yalnızca, hazırlanan dağıtım için seçilen kullanıcılar için tetiklenir. Mevcut Federasyon kurulumunuzu etkilemez.

Aşağıdakileri yaparak *sorunsuz SSO* 'yu etkinleştirin:

1. Azure AD Connect sunucuda oturum açın.

2. *% ProgramFiles% \\ Microsoft Azure Active Directory Connect* klasörüne gidin.

3. Aşağıdaki komutu çalıştırarak *sorunsuz SSO* PowerShell modülünü içeri aktarın: 

   `Import-Module .\AzureADSSO.psd1`

4. PowerShell'i yönetici olarak çalıştırın. PowerShell 'de, çağırın `New-AzureADSSOAuthenticationContext` . Bu komut, kiracınızın genel yönetici kimlik bilgilerini girebileceğiniz bir pencere açar.

5. Çağrısı yapın `Get-AzureADSSOStatus | ConvertFrom-Json` . Bu komut, Active Directory ormanlarının bir listesini görüntüler (Bu özelliğin etkinleştirildiği "etki alanları" listesine bakın). Varsayılan olarak, kiracı düzeyinde false olarak ayarlanır.

   ![Windows PowerShell çıkışı örneği](./media/how-to-connect-staged-rollout/sr3.png)

6. Çağrısı yapın `$creds = Get-Credential` . İstemde, hedeflenen Active Directory ormanın etki alanı yönetici kimlik bilgilerini girin.

7. Çağrısı yapın `Enable-AzureADSSOForest -OnPremCredentials $creds` . Bu komut, *sorunsuz SSO* için gereken Active Directory ormanı için şirket içi etki alanı denetleyicisinden AZUREADSSOACC bilgisayar hesabını oluşturur.

8. *Sorunsuz SSO* , URL 'lerin intranet bölgesinde olmasını gerektirir. Bu URL 'Leri Grup ilkeleri kullanarak dağıtmak için bkz. [hızlı başlangıç: Azure AD sorunsuz çoklu oturum açma](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature).

9. Tüm yönergeler için, *sorunsuz SSO* için [dağıtım planlarımızı](https://aka.ms/SeamlessSSODPDownload) de indirebilirsiniz.

## <a name="enable-staged-rollout"></a>Hazırlanan dağıtımı etkinleştir

Belirli bir özelliği (*doğrudan kimlik doğrulama*, *Parola karması EŞITLEME* veya *sorunsuz SSO*) bir gruptaki bir grup kullanıcı kümesine aktarmak için, sonraki bölümlerdeki yönergeleri izleyin.

### <a name="enable-a-staged-rollout-of-a-specific-feature-on-your-tenant"></a>Kiracınızda belirli bir özelliğin hazırlanmış bir dağıtımını etkinleştirin

Aşağıdaki seçeneklerden birini kullanabilirsiniz:

- **Seçenek A**  -  *Parola karması eşitleme*  +  *sorunsuz SSO*
- **Seçenek B**  -  *geçişli kimlik doğrulaması*  +  *sorunsuz SSO*
- **Desteklenmiyor**  -  *Parola karması eşitleme*  +  *geçişli kimlik doğrulaması*  +  *sorunsuz SSO*

Şunları yapın:

1. UX 'e erişmek için [Azure AD portalında](https://aka.ms/stagedrolloutux)oturum açın.

2. **Yönetilen Kullanıcı oturum açma bağlantısı için hazırlanan dağıtımı etkinleştir** ' i seçin.

   Örneğin, *seçenek A*'yı etkinleştirmek istiyorsanız, aşağıdaki görüntülerde gösterildiği gibi **parola karma eşitlemesini** ve **kesintisiz çoklu oturum açma** denetimlerini **üzerine** kaydırın.

   

  

3. *Doğrudan kimlik doğrulamayı* ve *sorunsuz SSO*'yu etkinleştirmek için grupları özelliğe ekleyin. Bir UX zaman aşımını önlemek için, güvenlik gruplarının başlangıçta 200 ' den fazla üye içermediğinden emin olun.

   

   >[!NOTE]
   >Bir gruptaki üyeler, hazırlanan dağıtım için otomatik olarak etkinleştirilir. İç içe ve dinamik gruplar, hazırlanan dağıtım için desteklenmez.
   >Yeni bir grup eklenirken, gruptaki kullanıcılar (yeni bir grup için 200 kullanıcıya kadar), hemen yönetilen kimlik doğrulaması kullanacak şekilde güncelleştirilir. Bir grubu (Kullanıcı ekleme veya kaldırma) düzenlemeyle, değişikliklerin etkili olması 24 saate kadar sürebilir.
   >Sorunsuz SSO yalnızca, kullanıcılar sorunsuz SSO grubsunlar ve ayrıca bir PTA veya PHS grubunda ise geçerlidir.

## <a name="auditing"></a>Denetim

Hazırlanan dağıtım için gerçekleştirdiğimiz çeşitli eylemler için denetim olaylarını etkinleştirdik:

- *Parola karması eşitleme*, *geçişli kimlik doğrulama* veya *sorunsuz SSO* için hazırlanmış bir dağıtımı etkinleştirdiğinizde olay denetimi.

  >[!NOTE]
  >Hazırlanmış dağıtım kullanılarak *sorunsuz SSO* açık olduğunda bir denetim olayı günlüğe kaydedilir.

  !["Özellik için dağıtım ilkesi oluşturma" bölmesi-etkinlik sekmesi](./media/how-to-connect-staged-rollout/sr7.png)

  !["Özellik için dağıtım ilkesi oluşturma" bölmesi-değiştirilen özellikler sekmesi](./media/how-to-connect-staged-rollout/sr8.png)

- *Parola karması eşitleme*, *geçişli kimlik doğrulama* veya *sorunsuz SSO*'ya bir grup eklendiğinde olay denetimi.

  >[!NOTE]
  >Hazırlanmış dağıtım için *Parola karması eşitlemesine* bir grup eklendiğinde bir denetim olayı günlüğe kaydedilir.

  !["Özellik dağıtımı 'na grup ekleme" bölmesi-etkinlik sekmesi](./media/how-to-connect-staged-rollout/sr9.png)

  !["Özellik dağıtımı 'na grup ekleme" bölmesi-değiştirilen özellikler sekmesi](./media/how-to-connect-staged-rollout/sr10.png)

- Gruba eklenen bir Kullanıcı hazırlanan dağıtım için etkinleştirildiğinde, denetim olayı.

  !["Özellik dağıtımı 'na Kullanıcı Ekle" bölmesi-etkinlik sekmesi](media/how-to-connect-staged-rollout/sr11.png)

  !["Özellik dağıtımı 'na Kullanıcı Ekle" bölmesi-hedef sekmesi](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>Doğrulama

Oturum açmayı *Parola karması eşitleme* veya *geçişli kimlik doğrulaması* ile test etmek için (Kullanıcı adı ve parola oturum açma) şunları yapın:

1. Extranet üzerinde, özel bir tarayıcı oturumunda [uygulamalar sayfasına](https://myapps.microsoft.com) gidin ve ardından hazırlanan dağıtım için seçilen kullanıcı hesabının USERPRINCIPALNAME (UPN) değerini girin.

   Hazırlanmış dağıtım için hedeflenen kullanıcılar, Federasyon oturum açma sayfanıza yönlendirilmez. Bunun yerine, Azure AD kiracı markalı oturum açma sayfasında oturum açması istenir.

1. [Azure AD oturum açma etkinlik raporunda](../reports-monitoring/concept-sign-ins.md) userPrincipalName ile filtreleyerek oturum açma 'nın başarıyla göründüğünden emin olun.

*Sorunsuz SSO* ile oturum açmayı test etmek için:

1. İntranette, özel bir tarayıcı oturumunda [uygulamalar sayfasına](https://myapps.microsoft.com) gidin ve ardından hazırlanan dağıtım için seçilen kullanıcı hesabının USERPRINCIPALNAME (UPN) değerini girin.

   *Sorunsuz SSO* 'yu aşamalı olarak sunmak için hedeflenen kullanıcılar "oturum açmaya çalışılıyor..." ile sunulmaktadır. sessizce oturum açmadan önce ileti.

1. [Azure AD oturum açma etkinlik raporunda](../reports-monitoring/concept-sign-ins.md) userPrincipalName ile filtreleyerek oturum açma 'nın başarıyla göründüğünden emin olun.

   Seçili hazırlanmış dağıtım kullanıcıları için Active Directory Federasyon Hizmetleri (AD FS) (AD FS) üzerinde hala gerçekleşen Kullanıcı oturum açma işlemlerini izlemek için, [AD FS sorun giderme: olaylar ve günlüğe kaydetme](/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events)bölümündeki yönergeleri izleyin. Bu üçüncü taraf Federasyon sağlayıcılarında bunu nasıl denetlayacağına ilişkin satıcı belgelerine bakın.

## <a name="remove-a-user-from-staged-rollout"></a>Bir kullanıcıyı hazırlanan piyasaya çıkarma

Bir kullanıcıyı gruptan kaldırmak, bu kullanıcı için hazırlanan dağıtımı devre dışı bırakır. Hazırlanan piyasaya çıkma özelliğini devre dışı **bırakmak için,** denetimi geri kaydırın.

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

**S: Bu özelliği üretimde kullanabilir miyim?**

Y: Evet, bu özelliği üretim kiracınızda kullanabilirsiniz, ancak ilk olarak test kiracınızda denemenizi öneririz.

**S: Bu özellik, bazı kullanıcıların federal kimlik doğrulaması kullandığı ve diğer kullanıcıların bulut kimlik doğrulamasını kullandığı kalıcı bir "ortak varlığı" korumak için kullanılabilir mi?**

Y: Hayır, bu özellik bulut kimlik doğrulamasını test etmek için tasarlanmıştır. Birkaç kullanıcı grubunu başarıyla test ettikten sonra, bulut kimlik doğrulamasına kadar kesmeniz gerekir. Kalıcı bir karma durum kullanılması önerilmez, çünkü bu yaklaşım beklenmedik kimlik doğrulama akışlarına yol açabilir.

**S: PowerShell 'i, hazırlanan dağıtım gerçekleştirmek için kullanabilir miyim?**

C: Evet. PowerShell kullanarak hazırlanan dağıtımı gerçekleştirme hakkında bilgi edinmek için bkz. [Azure AD önizleme](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#staged_rollout).

## <a name="next-steps"></a>Sonraki adımlar
- [Azure AD 2,0 Preview](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#staged_rollout )
- [Oturum açma yöntemini Parola karması eşitlemesi olarak değiştirme](plan-migrate-adfs-password-hash-sync.md#step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso)
- [Geçiş kimlik doğrulaması için oturum açma yöntemini değiştirme](plan-migrate-adfs-password-hash-sync.md#step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso)
