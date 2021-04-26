---
title: Azure Active Directory bir acil durumda Kullanıcı erişimini iptal etme | Microsoft Docs
description: Azure Active Directory bir kullanıcının tüm erişimini iptal etme
services: active-directory
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
author: curtand
ms.author: curtand
manager: daveba
ms.reviewer: krbain
ms.date: 03/29/2021
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 578e8f5f3126542c579cd573c82b732049d407b6
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105959833"
---
# <a name="revoke-user-access-in-azure-active-directory"></a>Azure Active Directory Kullanıcı erişimini iptal etme

Bir yöneticinin bir kullanıcının tüm erişimini iptal etmesine gerek duyduğu senaryolar, güvenliği aşılmış hesaplar, çalışan sonlandırması ve diğer Insider tehditleri içerir. Ortamın karmaşıklığına bağlı olarak Yöneticiler, erişimin iptal edildiğinden emin olmak için birkaç adım sürebilir. Bazı senaryolarda, erişim iptalinin başlatılması ve erişimin etkin bir şekilde iptal edilmesi arasında bir süre olabilir.

Riskleri azaltmak için belirteçlerin nasıl çalıştığını anlamanız gerekir. Aşağıdaki bölümlerde bahsedilen desenlerden birine giren birçok belirteç türü vardır.

## <a name="access-tokens-and-refresh-tokens"></a>Belirteçleri ve yenileme belirteçlerini erişim

Erişim belirteçleri ve yenileme belirteçleri, genellikle kalın istemci uygulamalarıyla kullanılır ve aynı zamanda tek sayfalı uygulamalar gibi tarayıcı tabanlı uygulamalarda kullanılır.

- Kullanıcılar Azure AD 'de kimlik doğrulaması yaparken, kullanıcının belirli bir kaynağa erişim izni verilip verilmediğini belirlemede yetkilendirme ilkeleri değerlendirilir.  

- Yetkilendirilirse, Azure AD kaynak için bir erişim belirteci ve yenileme belirteci yayınlar.  

- Azure AD tarafından varsayılan olarak 1 saat boyunca yayınlanan erişim belirteçleri. Kimlik doğrulama protokolü izin veriyorsa, uygulama, erişim belirtecinin süresi dolduğu zaman yenileme belirtecini Azure AD 'ye geçirerek kullanıcıyı sessizce yeniden kimlik doğrulaması yapabilir.

Azure AD daha sonra yetkilendirme ilkelerini yeniden değerlendirin. Kullanıcı hala yetkilendirilirse, Azure AD yeni bir erişim belirteci yayınlar ve belirteci yeniler.

Erişim belirteçleri, genellikle bir saat içinde olan belirtecin yaşam süresinden daha kısa bir süre içinde iptal edilmeli bir güvenlik sorunu olabilir. Bu nedenle, Microsoft, Office 365 uygulamalarına [sürekli erişim değerlendirmesi](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-continuous-access-evaluation) getirmek için etkin bir şekilde çalışır ve bu da neredeyse gerçek zamanlı olarak erişim belirteçlerinin doğrulanması sağlanmasına yardımcı olur.  

## <a name="session-tokens-cookies"></a>Oturum belirteçleri (tanımlama bilgileri)

Tarayıcı tabanlı çoğu uygulama, erişim ve yenileme belirteçleri yerine oturum belirteçlerini kullanır.  

- Bir Kullanıcı bir tarayıcı açtığında ve Azure AD aracılığıyla bir uygulamanın kimliğini doğruladığında, Kullanıcı iki oturum belirteci alır. Bunlardan biri Azure AD 'den ve diğeri uygulamadan.  

- Bir uygulama kendi oturum belirtecini yaptıktan sonra, uygulamaya erişim uygulamanın oturumuna tabidir. Bu noktada, Kullanıcı yalnızca uygulamanın farkında olduğu yetkilendirme ilkelerinden etkilenir.

- Azure AD yetkilendirme ilkeleri, uygulamanın kullanıcıyı Azure AD 'ye geri göndermesi halinde yeniden değerlendirilir. Sıklık genellikle sessizce gerçekleşir, ancak sıklık uygulamanın nasıl yapılandırıldığına bağlıdır. Oturum belirteci geçerli olduğu sürece, uygulama hiçbir zaman kullanıcıyı Azure AD 'ye geri gönderemeyebilir.

- Bir oturum belirtecinin iptal edilmesi için, uygulamanın kendi yetkilendirme ilkelerine göre erişimi iptal etmeniz gerekir. Azure AD, bir uygulama tarafından verilen bir oturum belirtecini doğrudan iptal edemiyor.  

## <a name="revoke-access-for-a-user-in-the-hybrid-environment"></a>Karma ortamdaki bir kullanıcı için erişimi iptal etme

Şirket içi Active Directory Azure Active Directory ile eşitlenmiş bir karma ortamda, Microsoft, BT yöneticilerinin aşağıdaki işlemleri gerçekleştirmesini öneriyor.  

### <a name="on-premises-active-directory-environment"></a>Şirket içi Active Directory ortamı

Active Directory yönetici olarak şirket içi ağınıza bağlanın, PowerShell 'i açın ve aşağıdaki işlemleri gerçekleştirin:

1. Active Directory kullanıcıyı devre dışı bırakın. [Disable-ADAccount](https://docs.microsoft.com/powershell/module/addsadministration/disable-adaccount?view=win10-ps)öğesine bakın.

    ```PowerShell
    Disable-ADAccount -Identity johndoe  
    ```

2. Active Directory kullanıcının parolasını iki kez sıfırlayın. [Set-ADAccountPassword](https://docs.microsoft.com/powershell/module/addsadministration/set-adaccountpassword?view=win10-ps)bölümüne bakın.

    > [!NOTE]
    > Bir kullanıcının parolasını iki kez değiştirme nedeni, özellikle de şirket içi parola çoğaltmasında gecikme olması durumunda karma geçişi riskini hafifletmektir. Bu hesabı güvenli bir şekilde kabul ediyorsanız, parolayı yalnızca bir kez sıfırlayabilirsiniz.

    > [!IMPORTANT]
    > Aşağıdaki cmdlet 'lerde örnek parolalar kullanmayın. Parolaların rastgele bir dize olarak değiştirilmesini unutmayın.

    ```PowerShell
    Set-ADAccountPassword -Identity johndoe -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "p@ssw0rd1" -Force)
    Set-ADAccountPassword -Identity johndoe -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "p@ssw0rd2" -Force)
    ```

### <a name="azure-active-directory-environment"></a>Azure Active Directory ortamı

Azure Active Directory yönetici olarak PowerShell ' i açın, çalıştırın ``Connect-AzureAD`` ve aşağıdaki işlemleri gerçekleştirin:

1. Azure AD 'de kullanıcıyı devre dışı bırakın. [Set-AzureADUser](https://docs.microsoft.com/powershell/module/azuread/Set-AzureADUser?view=azureadps-2.0)öğesine bakın.

    ```PowerShell
    Set-AzureADUser -ObjectId johndoe@contoso.com -AccountEnabled $false
    ```

2. Kullanıcının Azure AD yenileme belirteçlerini iptal edin. [Revoke-AzureADUserAllRefreshToken](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0)öğesine başvurun.

    ```PowerShell
    Revoke-AzureADUserAllRefreshToken -ObjectId johndoe@contoso.com
    ```

3. Kullanıcının cihazlarını devre dışı bırakın. [Get-AzureADUserRegisteredDevice](https://docs.microsoft.com/powershell/module/azuread/get-azureaduserregistereddevice?view=azureadps-2.0)öğesine başvurun.

    ```PowerShell
    Get-AzureADUserRegisteredDevice -ObjectId johndoe@contoso.com | Set-AzureADDevice -AccountEnabled $false
    ```
## <a name="when-access-is-revoked"></a>Erişim iptal edildiğinde

Yöneticiler yukarıdaki adımları gerçekleştirdikten sonra, Azure Active Directory bağlı herhangi bir uygulama için Kullanıcı yeni belirteçler elde edebilir. İptal ve Kullanıcı erişimini kaybetme arasındaki geçen süre, uygulamanın erişim verme yöntemine bağlıdır:

- Erişim **belirteçleri kullanan uygulamalar** için, erişim belirtecinin süresi dolarsa Kullanıcı erişimi kaybeder.

- **Oturum belirteçleri kullanan uygulamalar** için, mevcut oturumların belirtecin süresi dolduktan hemen sonra sona erer. Kullanıcının devre dışı durumu uygulamayla eşitlenirse, uygulama bunu yapmak üzere yapılandırılmışsa, kullanıcının mevcut oturumlarını otomatik olarak iptal edebilir.  Bu süre, uygulama ile Azure AD arasındaki eşitlemenin sıklığına bağlıdır.

## <a name="best-practices"></a>En iyi uygulamalar

- Otomatik sağlama ve sağlamayı kaldırma çözümünü dağıtın. Kullanıcıların uygulama sağlamasını kaldırma, özellikle de oturum belirteçlerini kullanan uygulamalar için erişimi iptal etmenin etkili bir yoludur. Otomatik sağlamayı ve sağlamayı kaldırmayı desteklemeyen uygulamalar için kullanıcıların sağlamasını kaldırma sürecini geliştirin. Uygulamaların kendi oturum belirteçlerini iptal etmesine ve hala geçerli olsalar bile Azure AD erişim belirteçlerini kabul etmeyi durdurmasına dikkat edin.

  - [Azure AD SaaS uygulama sağlamasını](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)kullanın. Azure AD SaaS uygulaması sağlama genellikle her 20-40 dakikada bir otomatik olarak çalışır. Uygulamalarda devre dışı bırakılan kullanıcıları kaldırmak veya devre dışı bırakmak için [Azure AD sağlamasını yapılandırın](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) .
  
  - Azure AD SaaS uygulama sağlamasını kullanmayan uygulamalar için [Kimlik Yöneticisi (MıM)](https://docs.microsoft.com/microsoft-identity-manager/mim-how-provision-users-adds) veya 3. taraf çözümünü kullanarak kullanıcıların sağlamasını kaldırmayı otomatik hale getirin.  
  - El ile sağlamayı kaldırma gerektiren uygulamalar için bir işlem belirleyip geliştirin. Yöneticilerin gerektiğinde bu uygulamalardan uygulamayı kaldırmak için gerekli el ile görevleri hızlı bir şekilde çalıştırabileceğinizden emin olun.
  
- [Microsoft Intune ile cihazlarınızı ve uygulamalarınızı yönetin](https://docs.microsoft.com/mem/intune/remote-actions/device-management). Intune tarafından yönetilen [cihazlar, fabrika ayarlarına sıfırlanabilir](https://docs.microsoft.com/mem/intune/remote-actions/devices-wipe). Cihaz yönetilmiyorsa, [yönetilen uygulamalardan kurumsal verileri](https://docs.microsoft.com/mem/intune/apps/apps-selective-wipe)silebilirsiniz. Bu süreçler, son kullanıcıların cihazlarından potansiyel olarak hassas verileri kaldırmak için geçerlidir. Ancak, her iki işlemin de tetiklenmesi için cihazın İnternet 'e bağlı olması gerekir. Cihaz çevrimdışıysa cihaz, yerel olarak depolanan verilere erişmeye devam eder.

> [!NOTE]
> Bir silme işleminden sonra cihazdaki veriler kurtarılamaz.

- Uygun olduğunda [veri indirmeyi engellemek için Microsoft Cloud App Security (MCAS)](https://docs.microsoft.com/cloud-app-security/use-case-proxy-block-session-aad) kullanın. Verilere yalnızca çevrimiçi erişim sağlayabiliyorsa, kuruluşlar oturumları izleyebilir ve gerçek zamanlı ilke zorlaması elde edebilir.

- [Azure AD 'de sürekli erişim değerlendirmesini (CAE)](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-continuous-access-evaluation)etkinleştirin. CAE, yöneticilerin oturum belirteçlerini ve CAE özellikli uygulamalar için erişim belirteçlerini iptal etmesine olanak tanır.  

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD yöneticileri için güvenli erişim uygulamaları](https://docs.microsoft.com/azure/active-directory/roles/security-planning)
- [Kullanıcı profili bilgilerini ekleme veya güncelleştirme](../fundamentals/active-directory-users-profile-azure-portal.md)
