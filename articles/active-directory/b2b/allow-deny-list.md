---
title: Belirli kuruluşlara yapılan davetlere izin verme veya bunları engelleme-Azure Active Directory | Microsoft Docs
description: Bir yöneticinin, belirli etki alanlarından B2B kullanıcılarına izin vermek veya bunları engellemek için bir erişim veya reddetme listesi ayarlamak üzere Azure portal veya PowerShell 'i nasıl kullanabileceğinizi gösterir.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/15/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: sasubram
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cd0cc6b2343a84287bd2ffdfd9df8d832f17fc8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474178"
---
# <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Belirli kuruluşların B2B kullanıcılarına yönelik davetlere izin verme veya bunları engelleme

Belirli kuruluşlardan B2B kullanıcılarına izin vermek veya bu kullanıcılara yönelik davetleri engellemek için bir izin verilenler listesi veya reddetme listesi kullanabilirsiniz. Örneğin, kişisel e-posta adresi etki alanlarını engellemek istiyorsanız Gmail.com ve Outlook.com gibi etki alanlarını içeren bir reddetme listesi ayarlayabilirsiniz. Ya da işletmenizde Contoso.com, Fabrikam.com ve Litware.com gibi diğer işletmelerle bir ortaklığı varsa ve davetleri yalnızca bu kuruluşlara kısıtlamak istiyorsanız, izin verilenler listenize Contoso.com, Fabrikam.com ve Litware.com ekleyebilirsiniz.
  
## <a name="important-considerations"></a>Önemli konular

- Bir izin verilenler listesi ya da reddetme listesi oluşturabilirsiniz. Her iki liste türünü de ayarlayamazsınız. Varsayılan olarak, izin verilenler listesinde olmayan etki alanları Reddedilenler listesinde bulunur ve tam tersi de geçerlidir. 
- Her kuruluş için yalnızca bir ilke oluşturabilirsiniz. İlkeyi daha fazla etki alanı içerecek şekilde güncelleştirebilir veya yeni bir tane oluşturmak için ilkeyi silebilirsiniz. 
- İzin verilenler listesine veya reddetme listesine ekleyebileceğiniz etki alanı sayısı yalnızca ilkenin boyutuyla sınırlıdır. Tüm ilkenin en büyük boyutu 25 KB 'tır (25.000 karakter), bu da izin verilenler listesini veya reddetme listesini ve diğer özellikler için yapılandırılmış diğer parametreleri içerir.
- Bu liste OneDrive Iş ve SharePoint Online izin/engelleme listelerinden bağımsız olarak çalışmaktadır. SharePoint Online 'da bireysel dosya paylaşımını kısıtlamak istiyorsanız, OneDrive Iş ve SharePoint Online için bir izin verme veya reddetme listesi ayarlamanız gerekir. Daha fazla bilgi için bkz. [SharePoint Online ve OneDrive iş 'Te kısıtlanmış etki alanları paylaşma](https://support.office.com/article/restricted-domains-sharing-in-sharepoint-online-and-onedrive-for-business-5d7589cd-0997-4a00-a2ba-2320ec49c4e9).
- Liste, daveti zaten kullanan dış kullanıcılara uygulanmaz. Liste kurulduktan sonra liste uygulanır. Bir Kullanıcı daveti bekleme durumundaysa ve etki alanını engelleyen bir ilke ayarlarsanız, kullanıcının daveti kullanma girişimi başarısız olur.

## <a name="set-the-allow-or-deny-list-policy-in-the-portal"></a>Portalda izin verme veya reddetme listesi ilkesini ayarlama

Varsayılan olarak, **herhangi bir etki alanına (en kapsamlı) yapılan davetlere Izin ver** ayarı etkinleştirilir. Bu durumda, herhangi bir kuruluştan B2B kullanıcıları davet edebilirsiniz.

### <a name="add-a-deny-list"></a>Reddetme listesi ekle

Bu, kuruluşunuzun neredeyse her kuruluşla çalışmak istediği, ancak belirli etki alanlarından kullanıcıların B2B kullanıcısı olarak davet edilmesi için en yaygın senaryodur.

Reddetme listesi eklemek için:

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. **Kullanıcı ayarlarını** > **Azure Active Directory** > **kullanıcıları** ' nı seçin.
3. **Dış kullanıcılar**altında, **dış işbirliği ayarlarını yönet**' i seçin.
4. **İşbirliği kısıtlamaları**' nın altında, **belirtilen etki alanlarına davetleri Reddet**' i seçin.
5. **Hedef etkı alanları**altında, engellemek istediğiniz etki alanlarından birinin adını girin. Birden çok etki alanı için, her etki alanını yeni bir satıra girin. Örneğin:

   ![Eklenen etki alanları ile reddetme seçeneğini gösterir](./media/allow-deny-list/DenyListSettings.png)
 
6. İşiniz bittiğinde **Kaydet**' e tıklayın.

İlkeyi ayarladıktan sonra, engellenen bir etki alanından bir kullanıcıyı davet etmeye çalışırsanız, kullanıcının etki alanının şu anda davet ilkeniz tarafından engellendiğini söyleyen bir ileti alırsınız.
 
### <a name="add-an-allow-list"></a>İzin verilenler listesi ekle

Bu, izin verilenler listesinde belirli etki alanlarını ayarlayabileceğiniz ve hiçbir başka kuruluşa veya hiçbir etki alanına yönelik davetleri kısıtlayabileceğiniz daha kısıtlayıcı bir yapılandırmadır. 

Bir izin verilenler listesi kullanmak istiyorsanız, işletmenizin ihtiyaçlarını tam olarak değerlendirmek için zaman harcadığınızdan emin olun. Bu ilkeyi çok kısıtlayıcı yaparsanız, kullanıcılarınız e-posta üzerinden belge göndermek veya diğer BT tasdiksiz ve işbirliği yapmak için diğer güncel olmayan yollar bulmak için seçim yapabilir.


Bir izin verilenler listesi eklemek için:

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. **Kullanıcı ayarlarını** > **Azure Active Directory** > **kullanıcıları** ' nı seçin.
3. **Dış kullanıcılar**altında, **dış işbirliği ayarlarını yönet**' i seçin.
4. **İşbirliği kısıtlamaları**' nın altında **yalnızca belirtilen etki alanlarına (en kısıtlayıcı) davetlere izin ver**' i seçin.
5. **Hedef etkı alanları**altında, izin vermek istediğiniz etki alanlarından birinin adını girin. Birden çok etki alanı için, her etki alanını yeni bir satıra girin. Örneğin:

   ![Eklenmiş etki alanları ile izin ver seçeneğini gösterir](./media/allow-deny-list/AllowListSettings.png)
 
6. İşiniz bittiğinde **Kaydet**' e tıklayın.

İlkeyi ayarladıktan sonra, izin verilenler listesinde olmayan bir etki alanından bir kullanıcıyı davet etmeye çalışırsanız, kullanıcının etki alanının şu anda davet ilkeniz tarafından engellendiğini söyleyen bir ileti alırsınız.

### <a name="switch-from-allow-to-deny-list-and-vice-versa"></a>İzin verme/reddetme listesine geç veya tam tersi 

Bir ilkeden diğerine geçiş yaparsanız, bu, var olan ilke yapılandırmasını atar. Anahtarı gerçekleştirmeden önce yapılandırmanızın ayrıntılarını yedeklediğinizden emin olun. 

## <a name="set-the-allow-or-deny-list-policy-using-powershell"></a>PowerShell kullanarak izin verme veya reddetme listesi ilkesini ayarlama

### <a name="prerequisite"></a>Önkoşul

> [!Note]
> AzureADPreview modülü önizlemede olduğundan, tam olarak desteklenmeyen bir modül değil. 

PowerShell kullanarak izin ver veya Reddet listesini ayarlamak için, Windows PowerShell için Azure Active Directory modülünün önizleme sürümünü yüklemelisiniz. Özellikle, AzureADPreview modülü sürüm 2.0.0.98 veya üstünü yükler.

Modülün sürümünü denetlemek için (ve yüklüyse bkz.):
 
1. Windows PowerShell 'i yükseltilmiş kullanıcı (yönetici olarak çalıştır) olarak açın. 
2. Bilgisayarınızda Windows PowerShell için Azure Active Directory modülünün herhangi bir sürümünün yüklü olup olmadığını görmek için aşağıdaki komutu çalıştırın:

   ```powershell  
   Get-Module -ListAvailable AzureAD*
   ```

Modül yüklü değilse veya gerekli bir sürüme sahip değilseniz, aşağıdakilerden birini yapın:

- Hiçbir sonuç döndürülmezse, AzureADPreview modülünün en son sürümünü yüklemek için aşağıdaki komutu çalıştırın:
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- Sonuçlarda yalnızca AzureAD modülü gösteriliyorsa, AzureADPreview modülünü yüklemek için aşağıdaki komutları çalıştırın: 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- Sonuçlarda yalnızca AzureADPreview modülü gösterilirse, ancak sürüm 2.0.0.98 ' den küçükse, güncelleştirmek için aşağıdaki komutları çalıştırın: 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
   ```

- Sonuçlarda hem AzureAD hem de AzureADPreview modülleri gösterilirse, ancak AzureADPreview modülünün sürümü 2.0.0.98 'den küçükse, güncelleştirmek için aşağıdaki komutları çalıştırın: 

   ```powershell 
   Uninstall-Module AzureAD 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
    ```

### <a name="use-the-azureadpolicy-cmdlets-to-configure-the-policy"></a>İlkeyi yapılandırmak için AzureADPolicy cmdlet 'lerini kullanın

Bir izin verme veya reddetme listesi oluşturmak için [New-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview) cmdlet 'ini kullanın. Aşağıdaki örnekte, "live.com" etki alanını engelleyen bir reddetme listesinin nasıl ayarlanacağı gösterilmektedir.

```powershell 
$policyValue = @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}")

New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
```

Aşağıdaki örnek, ilke tanımıyla birlikte satır içi olarak aynı örneği göstermektedir.

```powershell  
New-AzureADPolicy -Definition @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}") -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
```

İzin verme veya reddetme listesi ilkesini ayarlamak için, [set-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview) cmdlet 'ini kullanın. Örneğin:

```powershell   
Set-AzureADPolicy -Definition $policyValue -Id $currentpolicy.Id 
```

İlkeyi almak için [Get-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview) cmdlet 'ini kullanın. Örneğin:

```powershell
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy'} | select -First 1 
```

İlkeyi kaldırmak için [Remove-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview) cmdlet 'ini kullanın. Örneğin:

```powershell
Remove-AzureADPolicy -Id $currentpolicy.Id 
```

## <a name="next-steps"></a>Sonraki adımlar

- Azure AD B2B 'ye genel bakış için bkz. [Azure AD B2B işbirliği nedir?](what-is-b2b.md)
- Koşullu erişim ve B2B işbirliği hakkında daha fazla bilgi için bkz. [B2B işbirliği kullanıcıları Için koşullu erişim](conditional-access.md).



