---
title: Azure Active Directory için alternatif bir oturum açma KIMLIĞI olarak e-posta ile oturum açın
description: Kullanıcıların e-posta adreslerini alternatif bir oturum açma KIMLIĞI (Önizleme) olarak kullanarak Azure Active Directory oturum açmasını nasıl yapılandıracağınızı ve etkinleştireceğinizi öğrenin
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 10/01/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: calui
ms.openlocfilehash: 4e39d7f15e3ca3c6e241c767a5f881d7170c6379
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99255976"
---
# <a name="sign-in-to-azure-active-directory-using-email-as-an-alternate-login-id-preview"></a>Alternatif oturum açma KIMLIĞI (Önizleme) olarak e-posta kullanarak Azure Active Directory için oturum açın

> [!NOTE]
> Diğer bir oturum açma KIMLIĞI olan Azure Active Directory genel önizleme özelliği olduğundan, Azure AD 'de e-posta ile oturum açın. Önizlemeler hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Birçok kuruluş, kullanıcıların şirket içi dizin ortamıyla aynı kimlik bilgilerini kullanarak Azure Active Directory (Azure AD) üzerinde oturum açmalarına izin vermek ister. Karma kimlik doğrulaması olarak bilinen bu yaklaşım sayesinde, kullanıcıların yalnızca bir kimlik bilgileri kümesini hatırlamaları gerekir.

Bazı kuruluşlar aşağıdaki nedenlerden dolayı karma kimlik doğrulamasına taşınmadı:

* Varsayılan olarak, Azure AD Kullanıcı asıl adı (UPN) Şirket içi dizinle aynı UPN 'ye ayarlanır.
* Azure AD UPN 'nin değiştirilmesi, şirket içi ve Azure AD ortamları arasında, belirli uygulama ve hizmetlerle ilgili sorunlara neden olabilecek bir eşleşmeyen eşleşme oluşturur.
* İşletme veya uyumluluk nedenlerinden dolayı kuruluş, Azure AD 'de oturum açmak için şirket içi UPN 'yi kullanmak istemiyor.

Karma kimlik doğrulamasına taşımaya yardımcı olması için artık Azure AD 'yi yapılandırarak kullanıcıların doğrulanmış etki alanındaki bir e-posta ile farklı bir oturum açma KIMLIĞI olarak oturum açmasını sağlayabilirsiniz. Örneğin, *contoso* , eski UPN ile oturum açmaya devam etmek yerine *fabrikam*'a yeniden markalı ise, `balas@contoso.com` alternatif bir oturum açma kimliği olarak e-posta artık kullanılabilir. Bir uygulamaya veya hizmetlere erişmek için kullanıcılar, atanmış e-postalarını kullanarak Azure AD 'de oturum açabilirler `balas@fabrikam.com` .

Bu makalede, alternatif bir oturum açma KIMLIĞI olarak e-postayı nasıl etkinleştireceğinizi ve kullanacağınız gösterilmektedir. Bu özellik Azure AD Ücretsiz sürümünde ve üzeri sürümlerde kullanılabilir.

> [!NOTE]
> Bu özellik yalnızca bulutta kimliği doğrulanmış Azure AD kullanıcılarına yöneliktir.

> [!NOTE]
> Şu anda bu özellik, bulut kimlik doğrulaması olan kiracılar için Azure AD 'ye katılmış Windows 10 cihazlarında desteklenmez. Bu özellik, karma Azure AD 'ye katılmış cihazlar için geçerli değildir.

## <a name="overview-of-azure-ad-sign-in-approaches"></a>Azure AD oturum açma yaklaşımlarına genel bakış

Azure AD 'de oturum açmak için kullanıcılar hesabını benzersiz şekilde tanımlayan bir ad girer. Tarihsel olarak, Azure AD UPN 'yi yalnızca oturum açma adı olarak kullanabilirsiniz.

Şirket içi UPN 'nin kullanıcının tercih ettiği oturum açma e-postası olduğu kuruluşlarda bu yaklaşım harika. Bu kuruluşlar, Azure AD UPN 'yi şirket içi UPN ile tam aynı değere ayarlar ve kullanıcıların tutarlı bir oturum açma deneyimi vardır.

Ancak, bazı kuruluşlarda şirket içi UPN, oturum açma adı olarak kullanılmaz. Şirket içi ortamlarda, yerel AD DS alternatif bir oturum açma KIMLIĞIYLE oturum açmaya izin verecek şekilde yapılandırırsınız. Azure AD UPN 'yi şirket içi UPN ile aynı değere ayarlamak, daha sonra kullanıcıların bu değerle oturum açmasını gerektirirken bir seçenek değildir.

Bu sorunun tipik geçici çözümü, Azure AD UPN 'yi kullanıcının oturum açmasını beklediği e-posta adresine ayarlamaya yönelik bir çözümdür. Bu yaklaşım işe yarar, ancak şirket içi AD ile Azure AD arasında farklı UPN 'ler ile sonuçlanır ve bu yapılandırma tüm Microsoft 365 iş yükleri ile uyumlu değildir.

Farklı bir yaklaşım, Azure AD ve şirket içi UPN 'leri aynı değere eşitler ve ardından Azure AD 'yi, kullanıcıların Azure AD 'de doğrulanmış bir e-posta ile oturum açmalarına izin verecek şekilde yapılandırır. Bu özelliği sağlamak için, kullanıcının şirket içi dizinindeki *proxyAddresses* özniteliğinde bir veya daha fazla e-posta adresi tanımlarsınız. *ProxyAddresses* daha sonra Azure AD Connect KULLANıLARAK Azure AD ile otomatik olarak eşitlenir.

## <a name="preview-limitations"></a>Önizleme sınırlamaları

Azure AD Ücretsiz sürümünde ve üzerinde alternatif bir oturum açma KIMLIĞI olduğundan, Azure AD 'de e-postayla oturum açın.

Geçerli önizleme durumunda, bir Kullanıcı başka bir oturum açma KIMLIĞI olarak UPN olmayan bir e-posta ile oturum açtığında aşağıdaki sınırlamalar geçerlidir:

* Kullanıcılar UPN olmayan e-postalarıyla oturum açsa bile, UPN 'sini görebilirler. Aşağıdaki örnek davranış görünebilir:
    * Kullanıcıdan Azure AD oturum açma 'ya yönlendirdiğinizde UPN ile oturum açması istenir `login_hint=<non-UPN email>` .
    * Bir Kullanıcı UPN olmayan bir e-posta ile oturum açtığında ve yanlış bir parola girerse, UPN 'yi göstermek için *"parolanızı girin"* sayfası değişir.
    * Ve Microsoft Office gibi bazı Microsoft sitelerinde ve uygulamalarda [https://portal.azure.com](https://portal.azure.com) , genellikle sağ üst köşede görüntülenen **Hesap Yöneticisi** denetimi, oturum açmak için kullanılan UPN olmayan e-posta yerine kullanıcının UPN 'sini görüntüleyebilir.

* Bazı akışlar Şu anda UPN olmayan e-postayla uyumlu değildir, örneğin:
    * Kimlik koruması Şu anda *sızdırılan kimlik bilgileri* riski algılama ile e-posta alternatif oturum açma kimlikleriyle eşleşmiyor Bu risk algılama, sızdırılan kimlik bilgileriyle eşleştirmek için UPN 'yi kullanır. Daha fazla bilgi için bkz. [risk algılama ve düzeltme Azure AD kimlik koruması][identity-protection].
    * Alternatif bir oturum açma KIMLIĞI e-postasına gönderilen B2B davetleri tam olarak desteklenmez. Alternatif bir oturum açma KIMLIĞI olarak bir e-postaya gönderilen bir daveti kabul ettikten sonra, diğer e-posta ile oturum açma, kiralanan uç noktada Kullanıcı için çalışmayabilir.

## <a name="synchronize-sign-in-email-addresses-to-azure-ad"></a>Oturum açma e-posta adreslerini Azure AD ile eşitlemeyi

Geleneksel Active Directory Domain Services (AD DS) veya Active Directory Federasyon Hizmetleri (AD FS) (AD FS) kimlik doğrulaması doğrudan ağınızda gerçekleşir ve AD DS altyapınız tarafından işlenir. Karma kimlik doğrulamasıyla, kullanıcılar doğrudan Azure AD 'de oturum açabilirler.

Bu karma kimlik doğrulama yaklaşımını desteklemek için, şirket içi AD DS ortamınızı [Azure AD Connect][azure-ad-connect] kullanarak Azure AD 'ye eşitler ve parola karması eşitleme (PHS) veya Pass-Through kimlik doğrulaması (PTA) kullanacak şekilde yapılandırırsınız.

Her iki yapılandırma seçeneği de, Kullanıcı Kullanıcı adını ve parolasını Azure AD 'ye gönderir ve kimlik bilgilerini doğrular ve bir bilet yayınlar. Kullanıcılar Azure AD 'de oturum açtığında, kuruluşunuzun bir AD FS altyapısını barındırması ve yönetmesi için ihtiyacı ortadan kaldırır.

![Parola karması eşitleme ile Azure AD karma kimliği diyagramı](media/howto-authentication-use-email-signin/hybrid-password-hash-sync.png)

![Geçişli kimlik doğrulaması ile Azure AD karma kimliği diyagramı](media/howto-authentication-use-email-signin/hybrid-pass-through-authentication.png)

Azure AD Connect tarafından otomatik olarak eşitlenen Kullanıcı özniteliklerinden biri *proxyAddresses*. Kullanıcılar, *proxyAddresses* özniteliğinin bir parçası olarak şirket içi AD DS ortamında tanımlanmış bir e-posta adresine sahip olursa, otomatik olarak Azure AD ile eşitlenir. Bu e-posta adresi daha sonra doğrudan Azure AD oturum açma işleminde alternatif bir oturum açma KIMLIĞI olarak kullanılabilir.

> [!IMPORTANT]
> Yalnızca kiracı için doğrulanan etki alanındaki e-postalar Azure AD ile eşitlenir. Her Azure AD kiracısında sahip olduğunuz ve kendini kanıtlamış olan bir veya daha fazla doğrulanmış etki alanı vardır ve kiracınıza benzersiz bir şekilde bağlanır.
>
> Daha fazla bilgi için bkz. [Azure AD 'de özel etki alanı adı ekleme ve doğrulama][verify-domain].

Daha fazla bilgi için bkz. [Azure AD karma kimlik çözümünüz için doğru kimlik doğrulama yöntemini seçme][hybrid-auth-methods].

## <a name="enable-user-sign-in-with-an-email-address"></a>Kullanıcı oturumunu bir e-posta adresiyle etkinleştirin

*ProxyAddresses* özniteliği uygulandıktan sonra, Azure AD Connect KULLANıLARAK Azure AD 'ye eşitlendiğinde, kullanıcıların, kiracınız için alternatif bir oturum açma kimliği olarak e-postayla oturum açmaları için özelliği etkinleştirmeniz gerekir. Bu özellik, Azure AD oturum açma sunucularına yalnızca UPN değerlerine karşı oturum açma adını denetetmez, aynı zamanda e-posta adresi için *proxyAddresses* değerlerine karşılık gelen bir değer sağlar.

Önizleme süresince şu anda PowerShell 'i kullanarak alternatif bir oturum açma KIMLIĞI özelliği olarak oturum açma 'yı yalnızca bir e-posta ile etkinleştirebilirsiniz. Aşağıdaki adımları tamamlayabilmeniz için *Kiracı Yöneticisi* izinlerine sahip olmanız gerekir:

1. Yönetici olarak bir PowerShell oturumu açın ve ardından [Install-Module][Install-Module] cmdlet 'Ini kullanarak *azureadpreview* modülünü yüklersiniz:

    ```powershell
    Install-Module AzureADPreview
    ```

    İstenirse, NuGet ' i veya güvenilmeyen bir depodan yüklemek için **Y** ' yi seçin.

1. [Connect-AzureAD][Connect-AzureAD] cmdlet 'Ini kullanarak Azure AD kiracınızda *Kiracı Yöneticisi* olarak oturum açın:

    ```powershell
    Connect-AzureAD
    ```

    Komut, hesabınız, ortamınız ve kiracı KIMLIĞINIZ hakkındaki bilgileri döndürür.

1. *Homerealmdiscoverypolicy* ilkesinin aşağıdaki gibi [Get-azureadpolicy][Get-AzureADPolicy] cmdlet 'ini kullanarak kiracınızda zaten mevcut olup olmadığını denetleyin:

    ```powershell
    Get-AzureADPolicy | Where-Object Type -eq "HomeRealmDiscoveryPolicy" | Format-List *
    ```

1. Şu anda yapılandırılmış ilke yoksa, komut hiçbir şey döndürmez. Bir ilke döndürülürse, bu adımı atlayın ve mevcut bir ilkeyi güncelleştirmek için sonraki adıma geçin.

    *Barındırmerealmdiscoverypolicy* ilkesini kiracıya eklemek Için, [New-azureadpolicy][New-AzureADPolicy] cmdlet 'Ini kullanın ve *alternateıdlogin* özniteliğini aşağıdaki örnekte gösterildiği gibi *"Enabled"* olarak ayarlayın:

    ```powershell
    $AzureADPolicyDefinition = @(
      @{
         "HomeRealmDiscoveryPolicy" = @{
            "AlternateIdLogin" = @{
               "Enabled" = $true
            }
         }
      } | ConvertTo-JSON -Compress
    )
    $AzureADPolicyParameters = @{
      Definition            = $AzureADPolicyDefinition
      DisplayName           = "BasicAutoAccelerationPolicy"
      IsOrganizationDefault = $true
      Type                  = "HomeRealmDiscoveryPolicy"
    }
    New-AzureADPolicy @AzureADPolicyParameters
    ```

    İlke başarıyla oluşturulduğunda, komut aşağıdaki örnek çıktıda gösterildiği gibi ilke KIMLIĞINI döndürür:

    ```powershell
    Id                                   DisplayName                 Type                     IsOrganizationDefault
    --                                   -----------                 ----                     ---------------------
    5de3afbe-4b7a-4b33-86b0-7bbe308db7f7 BasicAutoAccelerationPolicy HomeRealmDiscoveryPolicy True
    ```

1. Zaten yapılandırılmış bir ilke varsa, **   Aşağıdaki örnek ilke çıkışında gösterildiği gibi alternateıdlogin özniteliğinin etkinleştirilip etkinleştirilmediğini denetleyin:

    ```powershell
    Id : 5de3afbe-4b7a-4b33-86b0-7bbe308db7f7
    OdataType :
    AlternativeIdentifier :
    Definition : {{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}}
    DisplayName : BasicAutoAccelerationPolicy
    IsOrganizationDefault : True
    KeyCredentials : {}
    Type : HomeRealmDiscoveryPolicy
    ```

    İlke varsa, ancak mevcut olmayan ya da etkinleştirilmemiş olan *Alternateıdlogin* özniteliği varsa veya korumak istediğiniz ilkede başka öznitelikler varsa, [set-azureadpolicy][Set-AzureADPolicy] cmdlet 'ini kullanarak mevcut ilkeyi güncelleştirin.

    > [!IMPORTANT]
    > İlkeyi güncelleştirdiğinizde, eski ayarları ve yeni *Alternateıdlogin* özniteliğini eklediğinizden emin olun.

    Aşağıdaki örnek, *Alternateıdlogin* özniteliğini ekler ve önceden ayarlanmış olabilecek *Allowcloudpasswordvalidation* özniteliğini korur:

    ```powershell
    $AzureADPolicyDefinition = @(
      @{
         "HomeRealmDiscoveryPolicy" = @{
            "AllowCloudPasswordValidation" = $true
            "AlternateIdLogin" = @{
               "Enabled" = $true
            }
         }
      } | ConvertTo-JSON -Compress
    )
    $AzureADPolicyParameters = @{
      ID                    = "b581c39c-8fe3-4bb5-b53d-ea3de05abb4b"
      Definition            = $AzureADPolicyDefinition
      DisplayName           = "BasicAutoAccelerationPolicy"
      IsOrganizationDefault = $true
      Type                  = "HomeRealmDiscoveryPolicy"
    }
    
    Set-AzureADPolicy @AzureADPolicyParameters
    ```

    Güncelleştirilmiş ilkenin değişikliklerinizi gösterdiğini ve *Alternateıdlogin* özniteliğinin artık etkin olduğunu doğrulayın:

    ```powershell
    Get-AzureADPolicy | Where-Object Type -eq "HomeRealmDiscoveryPolicy" | Format-List *
    ```

İlke uygulandığında, yayılır ve kullanıcıların alternatif oturum açma KIMLIĞINI kullanarak oturum açabiliyor.

## <a name="test-user-sign-in-with-email"></a>Kullanıcı oturumunu e-postayla test et

Kullanıcıların e-posta ile oturum açmasını test etmek için, gibi [https://myprofile.microsoft.com][my-profile] e-posta adreslerine bağlı olarak bir kullanıcı hesabı ile oturum açın, örneğin, `balas@fabrikam.com` UPN 'sini değil `balas@contoso.com` . Oturum açma deneyimi, UPN tabanlı bir oturum açma olayı ile aynı şekilde görünmelidir.

## <a name="enable-staged-rollout-to-test-user-sign-in-with-an-email-address"></a>Kullanıcı oturumunu bir e-posta adresiyle test etmek için hazırlanan dağıtımı etkinleştirin  

[Hazırlanan dağıtım][staged-rollout] , kiracı yöneticilerinin belirli gruplar için özellikleri etkinleştirmesine olanak sağlar. Kiracı yöneticilerinin Kullanıcı oturumunu bir e-posta adresiyle test etmek için hazırlanmış dağıtımı kullanması önerilir. Yöneticiler bu özelliği kiracının tamamına dağıtmaya hazırsanız, bir giriş bölgesi bulma ilkesi kullanmalıdır.  


Aşağıdaki adımları tamamlayabilmeniz için *Kiracı Yöneticisi* izinlerine sahip olmanız gerekir:

1. Yönetici olarak bir PowerShell oturumu açın ve ardından [Install-Module][Install-Module] cmdlet 'Ini kullanarak *azureadpreview* modülünü yüklersiniz:

    ```powershell
    Install-Module AzureADPreview
    ```

    İstenirse, NuGet ' i veya güvenilmeyen bir depodan yüklemek için **Y** ' yi seçin.

2. [Connect-AzureAD][Connect-AzureAD] cmdlet 'Ini kullanarak Azure AD kiracınızda *Kiracı Yöneticisi* olarak oturum açın:

    ```powershell
    Connect-AzureAD
    ```

    Komut, hesabınız, ortamınız ve kiracı KIMLIĞINIZ hakkındaki bilgileri döndürür.

3. Aşağıdaki cmdlet 'i kullanarak mevcut tüm hazırlanmış dağıtım ilkelerini listeleyin:
   
   ```powershell
   Get-AzureADMSFeatureRolloutPolicy
   ``` 

4. Bu özellik için mevcut bir hazırlanmış dağıtım ilkesi yoksa, yeni bir aşamalı dağıtım ilkesi oluşturun ve ilke KIMLIĞINI aklınızda yapın:

   ```powershell
   $AzureADMSFeatureRolloutPolicy = @{
      Feature    = "EmailAsAlternateId"
      DisplayName = "EmailAsAlternateId Rollout Policy"
      IsEnabled   = $true
   }
   New-AzureADMSFeatureRolloutPolicy @AzureADMSFeatureRolloutPolicy
   ```

5. Hazırlanan dağıtım ilkesine eklenecek grubun directoryObject KIMLIĞINI bulun. Bir sonraki adımda kullanılacak olduğundan, *ID* parametresi için döndürülen değeri aklınızda olun.
   
   ```powershell
   Get-AzureADMSGroup -SearchString "Name of group to be added to the staged rollout policy"
   ```

6. Aşağıdaki örnekte gösterildiği gibi, grubu, hazırlanan dağıtım ilkesine ekleyin. *-ID* parametresindeki değeri, 4. ADıMDAKI ilke kimliği için döndürülen değerle değiştirin ve *-refobjectıd* parametresindeki değeri 5. adımda belirtilen *kimlikle* değiştirin. Gruptaki kullanıcıların oturum açmak için proxy adreslerini kullanabilmesi 1 saate kadar sürebilir.

   ```powershell
   Add-AzureADMSFeatureRolloutPolicyDirectoryObject -Id "ROLLOUT_POLICY_ID" -RefObjectId "GROUP_OBJECT_ID"
   ```
   
Gruba eklenen yeni üyeler için, oturum açmak üzere proxy adreslerini kullanabilmeniz 24 saate kadar sürebilir.

### <a name="removing-groups"></a>Grupları kaldırma

Hazırlanmış bir dağıtım ilkesinden bir grubu kaldırmak için aşağıdaki komutu çalıştırın:

```powershell
Remove-AzureADMSFeatureRolloutPolicyDirectoryObject -Id "ROLLOUT_POLICY_ID" -ObjectId "GROUP_OBJECT_ID" 
```

### <a name="removing-policies"></a>İlkeleri kaldırma

Hazırlanmış bir dağıtım ilkesini kaldırmak için öncelikle ilkeyi devre dışı bırakın ve ardından sistemden kaldırın:

```powershell
Set-AzureADMSFeatureRolloutPolicy -Id "ROLLOUT_POLICY_ID" -IsEnabled $false 
Remove-AzureADMSFeatureRolloutPolicy -Id "ROLLOUT_POLICY_ID"
```

## <a name="troubleshoot"></a>Sorun giderme

Kullanıcılar, e-posta adreslerini kullanarak oturum açma olaylarıyla ilgili sorun yaşıyorsa, aşağıdaki sorun giderme adımlarını gözden geçirin:

1. Kullanıcı hesabının, şirket içi AD DS ortamında *proxyAddresses* özniteliği için kendi e-posta adresinin ayarlanmış olduğundan emin olun.
1. Azure AD Connect yapılandırıldığını ve şirket içi AD DS ortamından Kullanıcı hesaplarını Azure AD 'ye başarıyla eşitlediğini doğrulayın.
1. Azure AD *Homerealmdiscoverypolicy* Ilkesinin *alternateıdlogin* özniteliği *"Enabled"* olarak ayarlanmış olduğunu doğrulayın: doğru:

    ```powershell
    Get-AzureADPolicy | Where-Object Type -eq "HomeRealmDiscoveryPolicy" | Format-List *
    ```

## <a name="next-steps"></a>Sonraki adımlar

Azure AD Uygulaması Proxy veya Azure AD Domain Services gibi karma kimlik hakkında daha fazla bilgi edinmek için bkz. [Şirket içi iş yüklerinin erişimi ve yönetimi Için Azure AD hibrit kimliği][hybrid-overview].

Karma kimlik işlemleri hakkında daha fazla bilgi için bkz. [Parola karması eşitleme][phs-overview] veya [doğrudan kimlik doğrulama][pta-overview] eşitlemesi çalışma.

<!-- INTERNAL LINKS -->
[verify-domain]: ../fundamentals/add-custom-domain.md
[hybrid-auth-methods]: ../hybrid/choose-ad-authn.md
[azure-ad-connect]: ../hybrid/whatis-azure-ad-connect.md
[hybrid-overview]: ../hybrid/cloud-governed-management-for-on-premises.md
[phs-overview]: ../hybrid/how-to-connect-password-hash-synchronization.md
[pta-overview]: ../hybrid/how-to-connect-pta-how-it-works.md
[identity-protection]: ../identity-protection/overview-identity-protection.md#risk-detection-and-remediation

<!-- EXTERNAL LINKS -->
[Install-Module]: /powershell/module/powershellget/install-module
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
[Get-AzureADPolicy]: /powershell/module/azuread/get-azureadpolicy
[New-AzureADPolicy]: /powershell/module/azuread/new-azureadpolicy
[Set-AzureADPolicy]: /powershell/module/azuread/set-azureadpolicy
[staged-rollout]: /powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#staged-rollout
[my-profile]: https://myprofile.microsoft.com
