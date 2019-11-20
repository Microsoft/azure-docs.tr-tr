---
title: Azure Multi-Factor Authentication Kullanıcı durumları-Azure Active Directory
description: Azure Multi-Factor Authentication 'daki Kullanıcı durumları hakkında bilgi edinin.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a2d22c4a7a8b95f5a200518a3c46fc33f55c66a
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73569858"
---
# <a name="how-to-require-two-step-verification-for-a-user"></a>Bir kullanıcı için iki aşamalı doğrulama gerektirme

İki adımlı doğrulama gerektiren iki yaklaşımdan birini, her ikisi de genel yönetici hesabı kullanılmasını gerektirir. İlk seçenek, her kullanıcıyı Azure Multi-Factor Authentication (MFA) için etkinleştirmektir. Kullanıcılar ayrı ayrı etkinleştirildiğinde, her oturum açtıklarında iki aşamalı doğrulama gerçekleştirir (güvenilir IP adreslerinden oturum açtıklarında veya _anımsanan cihazlar_ özelliği açık olduğunda olduğu gibi bazı özel durumlarla birlikte). İkinci seçenek, belirli koşullarda iki adımlı doğrulama gerektiren bir koşullu erişim ilkesi ayarlamaya yönelik bir seçenektir.

> [!TIP]
> Koşullu erişim ilkelerini kullanarak Azure Multi-Factor Authentication etkinleştirme önerilen yaklaşımdır. Lisanslarınız, kullanıcıların her oturum açtıklarında MFA gerçekleştirmesini gerektirecek şekilde koşullu erişim içermediği sürece Kullanıcı durumlarının değiştirilmesi artık önerilmez.

## <a name="choose-how-to-enable"></a>Nasıl etkinleştirileceğini seçin

**Kullanıcı durumu değiştirilerek etkinleştirildi** -bu, iki adımlı doğrulama gerektirmek için geleneksel bir yöntemdir ve bu makalede ele alınmıştır. Hem bulutta hem de Azure MFA sunucusunda Azure MFA ile birlikte kullanılabilir. Bu yöntemin kullanılması, kullanıcıların **her** oturum açtıklarında iki aşamalı doğrulama gerçekleştirmesini gerektirir ve koşullu erişim ilkelerini geçersiz kılar.

Koşullu erişim ilkesi tarafından etkinleştirildi-kullanıcılarınız için iki aşamalı doğrulamayı etkinleştirmek üzere en esnek yollardan biridir. Koşullu erişim ilkesini kullanmanın etkinleştirilmesi yalnızca Bulutta Azure MFA için geçerlidir ve Azure AD 'nin Premium bir özelliğidir. Bu yöntem hakkında daha fazla bilgi, [bulut tabanlı Azure Multi-Factor Authentication dağıtma](howto-mfa-getstarted.md)bölümünde bulunabilir.

Azure AD Kimlik Koruması tarafından etkinleştirildi-bu yöntem, yalnızca tüm bulut uygulamaları için oturum açma riskini temel alan iki adımlı doğrulama istemek için Azure AD Kimlik Koruması risk ilkesini kullanır. Bu yöntem Azure Active Directory P2 lisanslama gerektirir. Bu yöntem hakkında daha fazla bilgi için [Azure Active Directory kimlik koruması](../identity-protection/howto-sign-in-risk-policy.md) bulunabilir

> [!Note]
> Lisanslar ve fiyatlandırma hakkında daha fazla bilgi için [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/
) ve [Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) fiyatlandırma sayfalarında bulabilirsiniz.

## <a name="enable-azure-mfa-by-changing-user-state"></a>Kullanıcı durumunu değiştirerek Azure MFA 'yı etkinleştirme

Azure Multi-Factor Authentication Kullanıcı hesapları aşağıdaki üç farklı duruma sahiptir:

| Durum | Açıklama | Etkilenen tarayıcı olmayan uygulamalar | Etkilenen tarayıcı uygulamaları | Modern kimlik doğrulaması etkilendi |
|:---:|:---:|:---:|:--:|:--:|
| Devre dışı |Azure MFA 'da kayıtlı olan yeni bir kullanıcının varsayılan durumu. |Hayır |Hayır |Hayır |
| Etkin |Kullanıcı Azure MFA 'ya kaydoldu, ancak kaydolmadı. Bir sonraki oturum açışlarında kaydolmak için bir istem alırlar. |Hayır.  Kayıt işlemi tamamlanana kadar çalışmaya devam eder. | Evet. Oturumun süresi dolduktan sonra, Azure MFA kaydı gereklidir.| Evet. Erişim belirtecinin süresi dolduktan sonra, Azure MFA kaydı gereklidir. |
| Uygulandı |Kullanıcı kaydedildi ve Azure MFA için kayıt işlemini tamamladı. |Evet. Uygulamalar uygulama parolaları gerektirir. |Evet. Azure MFA, oturum açma sırasında gereklidir. | Evet. Azure MFA, oturum açma sırasında gereklidir. |

Bir kullanıcının durumu, yöneticinin Azure MFA 'ya kaydolduğunu ve kayıt sürecini tamamlamadığını yansıtır.

Tüm kullanıcılar *devre dışı bırakıldı*. Azure MFA 'da kullanıcıları kaydettiğinizde, durumları *etkin*olarak değişir. Etkin kullanıcılar oturum açıp kayıt işlemini tamamladıktan sonra, durumu *Zorlanmış*olarak değişir.  

### <a name="view-the-status-for-a-user"></a>Kullanıcının durumunu görüntüleme

Kullanıcı durumlarını görüntüleyebileceğiniz ve yönetebileceğiniz sayfaya erişmek için aşağıdaki adımları kullanın:

1. [Azure Portal](https://portal.azure.com)’da yönetici olarak oturum açın.
2. **Tüm kullanıcıları** > **Azure Active Directory** > **Kullanıcılar ve gruplar** 'a gidin.
3. **Multi-Factor Authentication**seçin.
   ![Multi-Factor Authentication seçin](./media/howto-mfa-userstates/selectmfa.png)
4. Kullanıcı durumlarını görüntüleyen yeni bir sayfa açılır.
   Multi-Factor Authentication Kullanıcı durumunu ![-ekran görüntüsü](./media/howto-mfa-userstates/userstate1.png)

### <a name="change-the-status-for-a-user"></a>Kullanıcının durumunu değiştirme

1. Azure Multi-Factor Authentication **kullanıcıları** sayfasına ulaşmak için yukarıdaki adımları kullanın.
2. Azure MFA için etkinleştirmek istediğiniz kullanıcıyı bulun. Üstteki görünümü değiştirmeniz gerekebilir.
   ![kullanıcılar sekmesinden durumu değiştirmek için kullanıcıyı seçin](./media/howto-mfa-userstates/enable1.png)
3. Adının yanındaki kutuyu işaretleyin.
4. Sağ tarafta **hızlı adımlar**' ın altında **Etkinleştir** veya **devre dışı bırak**' ı seçin.
   ![hızlı adımlar menüsünde Etkinleştir ' i tıklatarak seçili kullanıcıyı etkinleştirin](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *Etkin* kullanıcılar, Azure MFA için kaydolduklarında otomatik olarak *zorlandı* . Kullanıcı durumunu *Zorlanmış*olarak el ile değiştirmeyin.

5. Açılan pencere penceresinde seçiminizi onaylayın.

Kullanıcıları etkinleştirdikten sonra, e-posta ile bildirim alın. Bunlara bir dahaki sefer oturum açtıklarında kaydolmaları istenir. Ayrıca, kuruluşunuz modern kimlik doğrulamayı desteklemeyen tarayıcı olmayan uygulamalar kullanıyorsa, uygulama parolaları oluşturmaları gerekir. Kullanmaya başlamanıza yardımcı olması için [Azure MFA Son Kullanıcı Kılavuzu ' na](../user-help/multi-factor-authentication-end-user.md) de bir bağlantı ekleyebilirsiniz.

### <a name="use-powershell"></a>PowerShell kullanma

[Azure AD PowerShell](/powershell/azure/overview)kullanarak Kullanıcı durumunu değiştirmek için `$st.State`değiştirin. Üç olası durum vardır:

* Etkin
* Uygulandı
* Devre dışı  

Kullanıcıları doğrudan *Zorlanmış* duruma taşımayın. Bunu yaparsanız, tarayıcı tabanlı olmayan uygulamalar çalışmayı durdurur çünkü Kullanıcı Azure MFA kaydını ve bir [uygulama parolası](howto-mfa-mfasettings.md#app-passwords)elde etmez.

Önce modülünü, şunu kullanarak yüklemeniz gerekir:

   ```PowerShell
   Install-Module MSOnline
   ```

> [!TIP]
> **Connect-MsolService** kullanarak önce bağlanmayı unutmayın

Bu örnek PowerShell betiği, tek bir kullanıcı için MFA 'yı sunar:

   ```PowerShell
   Import-Module MSOnline
   $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
   $st.RelyingParty = "*"
   $st.State = "Enabled"
   $sta = @($st)
   Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
   ```

Kullanıcıları toplu olarak etkinleştirmeniz gerektiğinde PowerShell kullanmak iyi bir seçenektir. Örnek olarak, aşağıdaki komut dosyası bir kullanıcı listesi ile geçer ve hesaplarında MFA 'yı sunar:

   ```PowerShell
   $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
   foreach ($user in $users)
   {
       $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
       $st.RelyingParty = "*"
       $st.State = "Enabled"
       $sta = @($st)
       Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
   }
   ```

MFA 'yı devre dışı bırakmak için şu betiği kullanın:

   ```PowerShell
   Get-MsolUser -UserPrincipalName user@domain.com | Set-MsolUser -StrongAuthenticationMethods @()
   ```

Ayrıca, şu şekilde kısaltılarak:

   ```PowerShell
   Set-MsolUser -UserPrincipalName user@domain.com -StrongAuthenticationRequirements @()
   ```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Kullanıcı başına MFA 'dan koşullu erişim tabanlı MFA 'ya Kullanıcı dönüştürme

Aşağıdaki PowerShell, koşullu erişim tabanlı Azure Multi-Factor Authentication dönüştürmeyi yaparken size yardımcı olabilir.

Bu PowerShell 'i bir ıSE penceresinde çalıştırın veya olarak kaydedin. Yerel olarak çalıştırılacak PS1 dosyası.

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> Kısa süre önce yukarıdaki davranışı ve PowerShell betiğini değiştirdik. Daha önce, komut dosyası MFA yöntemlerine kaydedilir, MFA 'yı devre dışı bırakmış ve yöntemleri geri yükledi. Artık devre dışı bırakma için varsayılan davranış metotları temizlemez.

## <a name="next-steps"></a>Sonraki adımlar

* Bir Kullanıcı neden MFA gerçekleştirmedi ya da istenmedi? [Azure Multi-Factor Authentication 'Daki raporlarda Azure AD oturum açma işlemleri raporu](howto-mfa-reporting.md#azure-ad-sign-ins-report)bölümüne bakın.
* Güvenilen IP 'Ler, özel ses iletileri ve sahtekarlık uyarıları gibi ek ayarları yapılandırmak için [Azure Multi-Factor Authentication ayarlarını yapılandırma](howto-mfa-mfasettings.md) makalesine bakın
* Azure Multi-Factor Authentication için Kullanıcı ayarlarını yönetme hakkında bilgiler, [bulutta azure Multi-Factor Authentication ile Kullanıcı ayarlarını yönetme](howto-mfa-userdevicesettings.md) makalesinde bulunabilir.
