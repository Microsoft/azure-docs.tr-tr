---
title: Kullanıcı başına Multi-Factor Authentication etkinleştir-Azure Active Directory
description: Kullanıcı durumunu değiştirerek Kullanıcı başına Azure AD Multi-Factor Authentication etkinleştirmeyi öğrenin
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 08/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 749829f641119273813d3c8ca826daf8b4dc4d11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96742672"
---
# <a name="enable-per-user-azure-ad-multi-factor-authentication-to-secure-sign-in-events"></a>Oturum açma olaylarını güvenli hale getirmek için Kullanıcı başına Azure AD Multi-Factor Authentication etkinleştirme

Azure AD 'de Kullanıcı oturum açma olaylarını güvenli hale getirmek için çok faktörlü kimlik doğrulamasının (MFA) gerekli olmasını sağlayabilirsiniz. Koşullu erişim ilkelerini kullanarak Azure AD Multi-Factor Authentication etkinleştirme, kullanıcıları korumak için önerilen yaklaşımdır. Koşullu erişim bir Azure AD Premium P1 veya P2 özelliğidir. Bu özellik, belirli senaryolarda gereken MFA gerektirirken kurallar uygulamanıza olanak tanır. Koşullu erişimi kullanmaya başlamak için bkz. [öğretici: Azure AD Multi-Factor Authentication Ile güvenli Kullanıcı oturum açma olayları](tutorial-enable-azure-mfa.md).

Koşullu erişim olmadan Azure AD ücretsiz kiracılarında, [kullanıcıları korumak için güvenlik varsayılanlarını kullanabilirsiniz](../fundamentals/concept-fundamentals-security-defaults.md). Kullanıcılara gerektiğinde MFA sorulur, ancak davranışı denetlemek için kendi kurallarınızı tanımlayamazsınız.

Gerekirse, her bir hesabı kullanıcı başına Azure AD Multi-Factor Authentication için etkinleştirebilirsiniz. Kullanıcılar ayrı ayrı etkinleştirildiğinde, her oturum açtıklarında çok faktörlü kimlik doğrulaması gerçekleştirir (güvenilen IP adreslerinden oturum açtıklarında veya _güvenilir CIHAZLARDA MFA 'yı anımsa_ özelliği açık olduğunda olduğu gibi bazı özel durumlar ile).

Azure AD lisanslarınız koşullu erişim içermediği ve güvenlik varsayılanlarını kullanmak istemediğinizde, Kullanıcı durumlarının değiştirilmesi önerilmez. MFA 'yı etkinleştirmenin farklı yolları hakkında daha fazla bilgi için bkz. [Azure AD Multi-Factor Authentication Için Özellikler ve lisanslar](concept-mfa-licensing.md).

> [!IMPORTANT]
>
> Bu makalede, Kullanıcı başına Azure AD Multi-Factor Authentication durumunu görüntüleme ve değiştirme ayrıntıları anlatılmaktadır. Koşullu erişim veya güvenlik Varsayılanları kullanırsanız, bu adımları kullanarak Kullanıcı hesaplarını gözden geçirmezsiniz veya etkinleştiremezsiniz.
>
> Koşullu erişim ilkesi aracılığıyla Azure AD Multi-Factor Authentication etkinleştirilmesi, kullanıcının durumunu değiştirmez. Kullanıcılar devre dışı görünüyorsa uyarıda yok. Koşullu erişim durumu değiştirmez.
>
> **Koşullu erişim ilkeleri kullanıyorsanız, Kullanıcı başına Azure AD Multi-Factor Authentication etkinleştirmeyin veya zorlamaz.**

## <a name="azure-ad-multi-factor-authentication-user-states"></a>Azure AD Multi-Factor Authentication Kullanıcı durumları

Bir kullanıcının durumu, yönetici tarafından Kullanıcı başına Azure AD Multi-Factor Authentication kayıtlı olup olmadığını yansıtır. Azure AD Multi-Factor Authentication 'daki Kullanıcı hesapları aşağıdaki üç farklı duruma sahiptir:

| Durum | Açıklama | Eski kimlik doğrulaması etkilendi | Etkilenen tarayıcı uygulamaları | Modern kimlik doğrulaması etkilendi |
|:---:| --- |:---:|:--:|:--:|
| Devre dışı | Kullanıcı başına Azure AD Multi-Factor Authentication kayıtlı olmayan bir kullanıcının varsayılan durumu. | Hayır | Hayır | Hayır |
| Etkin | Kullanıcı, Kullanıcı başına Azure AD Multi-Factor Authentication kaydedilir, ancak eski kimlik doğrulaması için parolasını kullanmaya devam edebilir. Kullanıcı, MFA kimlik doğrulama yöntemlerini henüz kaydetmediyse, modern kimlik doğrulaması (örneğin, bir Web tarayıcısı aracılığıyla) kullanarak bir sonraki oturum açtıklarında kaydolmak üzere bir istem alırlar. | Hayır. Eski kimlik doğrulaması, kayıt işlemi tamamlanana kadar çalışmaya devam eder. | Evet. Oturumun süresi dolduktan sonra Azure AD Multi-Factor Authentication kaydı gereklidir.| Evet. Erişim belirtecinin süresi dolduktan sonra Azure AD Multi-Factor Authentication kaydı gereklidir. |
| Uygulandı | Kullanıcı Azure AD Multi-Factor Authentication 'de Kullanıcı başına kaydolur. Kullanıcı, kimlik doğrulama yöntemlerini henüz kaydetmediyse, modern kimlik doğrulaması (örneğin, bir Web tarayıcısı aracılığıyla) kullanarak bir sonraki oturum açtıklarında kaydolmak üzere bir istem alırlar. *Etkin* durumda kaydı gerçekleştiren kullanıcılar otomatik olarak *Zorlanmış* duruma taşınır. | Evet. Uygulamalar uygulama parolaları gerektirir. | Evet. Oturum açma sırasında Azure AD Multi-Factor Authentication gereklidir. | Evet. Oturum açma sırasında Azure AD Multi-Factor Authentication gereklidir. |

Tüm kullanıcılar *devre dışı bırakıldı*. Kullanıcıları Kullanıcı başına Azure AD Multi-Factor Authentication kaydettiğinizde, durumları *etkin* olarak değişir. Etkin kullanıcılar oturum açıp kayıt işlemini tamamladıktan sonra, durumu *Zorlanmış* olarak değişir. Yöneticiler, *Zorlanmış* durumdan *etkin* veya *devre dışı* olarak da dahil olmak üzere kullanıcıları durumlar arasında taşıyabiliriz.

> [!NOTE]
> Kullanıcı başına MFA bir kullanıcı için yeniden etkinleştirilmişse ve Kullanıcı yeniden kaydolmazsa, MFA durumu, MFA yönetimi Kullanıcı arabirimindeki *etkin* *' e geçiş* yapmaz. Yöneticinin kullanıcıyı doğrudan *Zorlanmış* olarak taşıması gerekir.

## <a name="view-the-status-for-a-user"></a>Kullanıcının durumunu görüntüleme

Kullanıcı durumlarını görüntülemek ve yönetmek için aşağıdaki adımları tamamlayarak Azure portal sayfasına erişin:

1. [Azure Portal](https://portal.azure.com)’da yönetici olarak oturum açın.
1. *Azure Active Directory* arayıp seçin, sonra **Kullanıcılar**  >  **tüm kullanıcılar**' ı seçin.
1. **Multi-Factor Authentication**'ı seçin. Bu menü seçeneğini görmek için sağa kaydırmanız gerekebilir. Tam Azure portal pencere ve menü konumunu görmek için aşağıdan örnek ekran görüntüsünü seçin: [ ![ Azure AD 'de kullanıcılar penceresinden Multi-Factor Authentication seçin.](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Aşağıdaki örnekte gösterildiği gibi, Kullanıcı durumunu görüntüleyen yeni bir sayfa açılır.
   ![Azure AD Multi-Factor Authentication için örnek Kullanıcı durumu bilgilerini gösteren ekran görüntüsü](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>Kullanıcının durumunu değiştirme

Bir kullanıcı için Kullanıcı başına Azure AD Multi-Factor Authentication durumunu değiştirmek için aşağıdaki adımları izleyin:

1. Bir kullanıcının Azure AD Multi-Factor Authentication **kullanıcıları** sayfasına ulaşmak üzere [durumunu görüntülemek](#view-the-status-for-a-user) için önceki adımları kullanın.
1. Kullanıcı başına Azure AD Multi-Factor Authentication için etkinleştirmek istediğiniz kullanıcıyı bulun. En üstteki görünümü **kullanıcılara** değiştirmeniz gerekebilir.
   ![Durumu Kullanıcı sekmesinden değiştirmek için kullanıcıyı seçin](./media/howto-mfa-userstates/enable1.png)
1. Durumu değiştirmek için Kullanıcı (ler) in yanındaki kutuyu işaretleyin.
1. Sağ tarafta **hızlı adımlar** altında **Etkinleştir** veya **devre dışı bırak**' ı seçin. Aşağıdaki örnekte *John Smith* kullanıcısı adının yanında bir denetim sahibi olur ve kullanım için Etkinleştiriliyor: ![ hızlı adımlar menüsünde Etkinleştir ' e tıklayarak Seçili kullanıcıyı etkinleştir](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *Etkin* kullanıcılar, Azure AD Multi-Factor Authentication kaydolduklarında otomatik olarak *zorlandı* . Kullanıcı zaten kayıtlı olmadığı veya kullanıcının eski kimlik doğrulama protokollerine yönelik bağlantılarda kesintiye *uğratılmasını* kabul etmediği takdirde Kullanıcı durumunu zorunlu olarak değiştirmeyin.

1. Açılan pencere penceresinde seçiminizi onaylayın.

Kullanıcıları etkinleştirdikten sonra, e-posta ile bildirim alın. Kullanıcılara bir dahaki sefer oturum açtıklarında kaydolmalarını istemek için bir istem görüntülendiğini söyleyin. Ayrıca, kuruluşunuz modern kimlik doğrulamayı desteklemeyen tarayıcı olmayan uygulamalar kullanıyorsa, uygulama parolaları oluşturmaları gerekir. Daha fazla bilgi için bkz. [Azure AD Multi-Factor Authentication Son Kullanıcı Kılavuzu](../user-help/multi-factor-authentication-end-user-first-time.md) , kullanmaya başlamanıza yardımcı olur.

## <a name="change-state-using-powershell"></a>PowerShell kullanarak durumu değiştirme

Kullanıcı durumunu [Azure AD PowerShell](/powershell/azure/)kullanarak değiştirmek için, `$st.State` bir kullanıcı hesabının parametresini değiştirirsiniz. Bir kullanıcı hesabının üç olası durumu vardır:

* *Etkin*
* *Uygulandı*
* *Devre dışı*  

Genel olarak, kullanıcıları zaten MFA için kayıtlı olmadıkları takdirde doğrudan *Zorlanmış* duruma taşımayın. Bunu yaparsanız, Kullanıcı Azure AD Multi-Factor Authentication kaydı olmadığından ve bir [uygulama parolası](howto-mfa-app-passwords.md)edinmediği için eski kimlik doğrulama uygulamaları çalışmayı durdurur. Bazı durumlarda bu davranış istenebilir, ancak Kullanıcı kaydoluncaya kadar Kullanıcı deneyimini etkiler.

Başlamak için, [Install-Module](/powershell/module/powershellget/install-module) kullanarak *MSOnline* modülünü aşağıdaki gibi çalıştırın:

```PowerShell
Install-Module MSOnline
```

Sonra [Connect-MsolService](/powershell/module/msonline/connect-msolservice)kullanarak bağlanın:

```PowerShell
Connect-MsolService
```

Aşağıdaki örnek PowerShell betiği, adlı tek bir kullanıcı için MFA 'yı sunar *bsimon@contoso.com* :

```PowerShell
$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
$st.RelyingParty = "*"
$st.State = "Enabled"
$sta = @($st)

# Change the following UserPrincipalName to the user you wish to change state
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
```

Kullanıcıları toplu olarak etkinleştirmeniz gerektiğinde PowerShell kullanmak iyi bir seçenektir. Aşağıdaki betik, bir kullanıcı listesi ile döngülerinde MFA 'yı sunar. Aşağıdaki gibi ilk satırda ayarlanan Kullanıcı hesaplarını tanımlayın `$users` :

   ```PowerShell
   # Define your list of users to update state in bulk
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

MFA 'yı devre dışı bırakmak için aşağıdaki örnek, [Get-MsolUser](/powershell/module/msonline/get-msoluser)ile bir kullanıcı alır ve [set-MsolUser](/powershell/module/msonline/set-msoluser)kullanarak tanımlı kullanıcı Için ayarlanmış olan *strongauthenticationrequirements* öğesini kaldırır:

```PowerShell
Get-MsolUser -UserPrincipalName bsimon@contoso.com | Set-MsolUser -StrongAuthenticationRequirements @()
```

[Set-MsolUser](/powershell/module/msonline/set-msoluser) kullanarak bir kullanıcı için MFA 'yı da doğrudan devre dışı bırakabilirsiniz:

```PowerShell
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements @()
```

## <a name="convert-users-from-per-user-mfa-to-conditional-access"></a>Kullanıcıları Kullanıcı başına MFA 'dan koşullu erişime dönüştürme

Aşağıdaki PowerShell, koşullu erişim tabanlı Azure AD Multi-Factor Authentication dönüştürmeyi yaparken size yardımcı olabilir.

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
> MFA bir kullanıcı üzerinde yeniden etkinleştirilmişse ve Kullanıcı yeniden kaydolmazsa, MFA durumu, MFA yönetimi Kullanıcı arabiriminde *etkin* durumuna geçmez.  Bu durumda, yöneticinin kullanıcıyı doğrudan *Zorlanmış* olarak taşıması gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Azure AD Multi-Factor Authentication ayarlarını yapılandırmak için bkz.  [Azure ad Multi-Factor Authentication ayarlarını yapılandırma](howto-mfa-mfasettings.md).

Azure AD Multi-Factor Authentication Kullanıcı ayarlarını yönetmek için bkz. [Azure ad Multi-Factor Authentication ile Kullanıcı ayarlarını yönetme](howto-mfa-userdevicesettings.md).

Bir kullanıcının neden MFA yapması istenmediğini anlamak için, bkz. [Azure AD Multi-Factor Authentication raporları](howto-mfa-reporting.md).
