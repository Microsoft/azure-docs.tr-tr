---
title: Grupları yönetmek için PowerShell V2 örnekleri-Azure AD | Microsoft Docs
description: Bu sayfa, Azure Active Directory ' de gruplarınızı yönetmenize yardımcı olacak PowerShell örnekleri sağlar
keywords: Azure AD, Azure Active Directory, PowerShell, gruplar, Grup Yönetimi
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1aa696ccaecc24df700315962c1f01f3a298c56c
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74026703"
---
# <a name="azure-active-directory-version-2-cmdlets-for-group-management"></a>Grup yönetimi için sürüm 2 cmdlet 'leri Azure Active Directory

> [!div class="op_single_selector"]
> - [Azure Portal](../fundamentals/active-directory-groups-create-azure-portal.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
> - [PowerShell](groups-settings-v2-cmdlets.md)
>
>

Bu makalede, Azure Active Directory (Azure AD) içinde gruplarınızı yönetmek için PowerShell 'in nasıl kullanılacağına ilişkin örnekler yer almaktadır.  Ayrıca, Azure AD PowerShell modülü ile nasıl ayarlanalınacağını da söyler. İlk olarak, [Azure AD PowerShell modülünü indirmeniz](https://www.powershellgallery.com/packages/AzureAD/)gerekir.

## <a name="install-the-azure-ad-powershell-module"></a>Azure AD PowerShell modülünü yükleme

Azure AD PowerShell modülünü yüklemek için aşağıdaki komutları kullanın:

```powershell
    PS C:\Windows\system32> install-module azuread
    PS C:\Windows\system32> import-module azuread
```

Modülün kullanıma hazırsa emin olmak için aşağıdaki komutu kullanın:

```powershell
    PS C:\Windows\system32> get-module azuread

    ModuleType Version      Name                                ExportedCommands
    ---------- ---------    ----                                ----------------
    Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

Artık modüldeki cmdlet 'leri kullanmaya başlayabilirsiniz. Azure AD modülündeki cmdlet 'lerin tam açıklaması için lütfen [Azure Active Directory PowerShell sürüm 2](/powershell/azure/install-adv2?view=azureadps-2.0)' ye yönelik çevrimiçi başvuru belgelerine bakın.

## <a name="connect-to-the-directory"></a>Dizine Bağlan

Azure AD PowerShell cmdlet 'lerini kullanarak grupları yönetmeye başlayabilmeniz için önce, PowerShell oturumunuzu yönetmek istediğiniz dizine bağlamanız gerekir. Aşağıdaki komutu kullanın:

```powershell
    PS C:\Windows\system32> Connect-AzureAD
```

Cmdlet 'i, dizininize erişmek için kullanmak istediğiniz kimlik bilgilerini ister. Bu örnekte, tanıtım dizinine erişmek için karen@drumkit.onmicrosoft.com kullanıyoruz. Cmdlet, oturumun başarıyla bağlanıp bağlandığına ilişkin bir onay döndürür:

```powershell
    Account                       Environment Tenant
    -------                       ----------- ------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…
```

Artık, dizininizde grupları yönetmek için AzureAD cmdlet 'lerini kullanmaya başlayabilirsiniz.

## <a name="retrieve-groups"></a>Grupları Al

Dizininizden varolan grupları almak için Get-AzureADGroups cmdlet 'ini kullanın. 

Dizindeki tüm grupları almak için cmdlet 'ini parametresiz kullanın:

```powershell
    PS C:\Windows\system32> get-azureadgroup
```

Cmdlet 'i bağlı dizindeki tüm grupları döndürür.

Grubun ObjectID 'yi belirttiğiniz belirli bir grubu almak için-objectID parametresini kullanabilirsiniz:

```powershell
    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b
```

Cmdlet artık, NesneKimliği girdiğiniz parametrenin değeriyle eşleşen grubu döndürür:

```powershell
    DeletionTimeStamp            :
    ObjectId                     : e29bae11-4ac0-450c-bc37-6dae8f3da61b
    ObjectType                   : Group
    Description                  :
    DirSyncEnabled               :
    DisplayName                  : Pacific NW Support
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 9bb4139b-60a1-434a-8c0d-7c1f8eee2df9
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

-Filter parametresini kullanarak belirli bir grup için arama yapabilirsiniz. Bu parametre bir ODATA filtre yan tümcesini alır ve aşağıdaki örnekte olduğu gibi filtreyle eşleşen tüm grupları döndürür:

```powershell
    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

> [!NOTE]
> Azure AD PowerShell cmdlet 'leri OData sorgu standardını uygular. Daha fazla bilgi için bkz. OData [uç noktası kullanılarak OData sistem sorgu seçeneklerinde](https://msdn.microsoft.com/library/gg309461.aspx#BKMK_filter) **$Filter** .

## <a name="create-groups"></a>Grup Oluştur

Dizininizde yeni bir grup oluşturmak için New-AzureADGroup cmdlet 'ini kullanın. Bu cmdlet "pazarlama" adlı yeni bir güvenlik grubu oluşturur:

```powershell
    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"
```

## <a name="update-groups"></a>Güncelleştirme grupları

Var olan bir grubu güncelleştirmek için set-AzureADGroup cmdlet 'ini kullanın. Bu örnekte, "Intune yöneticileri" grubunun DisplayName özelliğini değiştiriyorsunuz. İlk olarak, Get-AzureADGroup cmdlet 'ini kullanarak grubu buluyoruz ve DisplayName özniteliğini kullanarak filtreliyoruz:

```powershell
    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

Sonra, Description özelliğini "Intune cihaz yöneticileri" yeni değeriyle değiştiriyorsunuz:

```powershell
    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"
```

Şimdi, grubu yeniden bulduk açıklama özelliğinin yeni değeri yansıtacak şekilde güncelleştirildiğini görüyoruz:

```powershell
    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"

    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Device Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

## <a name="delete-groups"></a>Grupları sil

Dizininizden grupları silmek için Remove-AzureADGroup cmdlet 'ini aşağıdaki gibi kullanın:

```powershell
    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b
```

## <a name="manage-group-membership"></a>Grup üyeliğini Yönet

### <a name="add-members"></a>Üye Ekle

Bir gruba yeni üyeler eklemek için Add-AzureADGroupMember cmdlet 'ini kullanın. Bu komut, önceki örnekte kullandığımız Intune yöneticileri grubuna bir üye ekler:

```powershell
    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

-ObjectID parametresi, üye eklemek istediğimiz grubun NesneKimliği ve-Refobjectıd, gruba üye olarak eklemek istediğimiz kullanıcının ObjectID.

### <a name="get-members"></a>Üyeleri al

Bir grubun var olan üyelerini almak için, Get-AzureADGroupMember cmdlet 'ini şu örnekte olduğu gibi kullanın:

```powershell
    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                          8120cc36-64b4-4080-a9e8-23aa98e8b34f User
```

### <a name="remove-members"></a>Üyeleri kaldır

Daha önce gruba eklediğimiz üyeyi kaldırmak için, burada gösterildiği gibi Remove-AzureADGroupMember cmdlet 'ini kullanın:

```powershell
    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

### <a name="verify-members"></a>Üyeleri doğrula

Bir kullanıcının grup üyeliklerini doğrulamak için, select-AzureADGroupIdsUserIsMemberOf cmdlet 'ini kullanın. Bu cmdlet, kendi parametrelerini, grup üyeliklerini denetlemek için kullanıcının ObjectID 'sini ve üyeliklerinin denetlenecek grupların bir listesini alır. Grup listesi, "Microsoft. Open. AzureAD. model. Groupıdsformembershipcheck" türünde bir karmaşık değişken biçiminde sağlanmalıdır. bu nedenle öncelikle bu türde bir değişken oluşturmanız gerekir:

```powershell
    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck
```

Ardından, bu karmaşık değişkenin "Groupıds" özniteliğini iade etmek için Groupıds değerlerini sağlıyoruz:

```powershell
    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"
```

Artık, ObjectID 72cd4bbd-2594-40a2-935c-016f3cfeeeea ' ı bir kullanıcının grup üyeliklerini $g gruplara göre denetlemek istiyorsam, şunu kullanacağız:

```powershell
    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                                 Value
    -------------                                                                                                  -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}
```

Döndürülen değer, bu kullanıcının üye olduğu grupların bir listesidir. Bu yöntemi, belirli bir Grup listesi için kişileri, grupları veya hizmet sorumluları üyeliğini, select-Azureadgroupidsınte Tısıı ' yi, select-AzureADGroupIdsGroupIsMemberOf ' i kullanarak denetlemek için de uygulayabilirsiniz. Select-Azureadgroupidsservicesprincipalismemberof

## <a name="disable-group-creation-by-your-users"></a>Kullanıcılarınız tarafından grup oluşturmayı devre dışı bırakma

Yönetici olmayan kullanıcıların güvenlik grupları oluşturmasını engelleyebilirsiniz. Microsoft Çevrimiçi Dizin Hizmetleri 'ndeki (MSODS) varsayılan davranış, yönetici olmayan kullanıcıların grup oluşturmalarına izin vermeksizin, Self Servis Grup Yönetimi (SSGM) de etkinleştirilip etkinleştirilmediğini belirtir. SSGM ayarı yalnızca uygulamalarım erişim panelinde davranışı denetler.

Yönetici olmayan kullanıcılar için Grup oluşturmayı devre dışı bırakmak için:

1. Yönetici olmayan kullanıcıların grup oluşturmalarına izin verildiğini doğrulayın:
   
   ```powershell
   PS C:\> Get-MsolCompanyInformation | fl UsersPermissionToCreateGroupsEnabled
   ```
  
2. `UsersPermissionToCreateGroupsEnabled : True`döndürürse yönetici olmayan kullanıcılar grupları oluşturabilir. Bu özelliği devre dışı bırakmak için:
  
   ```powershell 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```
  
## <a name="manage-owners-of-groups"></a>Grupların sahiplerini yönetme

Bir gruba sahip eklemek için Add-AzureADGroupOwner cmdlet 'ini kullanın:

```powershell
    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

-ObjectID parametresi, sahip eklemek istediğimiz grubun NesneKimliği ve-Refobjectıd, grubun sahibi olarak eklemek istediğimiz Kullanıcı veya hizmet sorumlusunun ObjectID.

Bir grubun sahiplerini almak için Get-AzureADGroupOwner cmdlet 'ini kullanın:

```powershell
    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
```

Cmdlet 'i, belirtilen grup için sahip (kullanıcılar ve hizmet sorumluları) listesini döndürür:

```powershell
    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          e831b3fd-77c9-49c7-9fca-de43e109ef67 User
```

Bir gruptan bir sahibi kaldırmak istiyorsanız, Remove-AzureADGroupOwner cmdlet 'ini kullanın:

```powershell
    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67
```

## <a name="reserved-aliases"></a>Ayrılmış diğer adlar

Bir grup oluşturulduğunda, belirli uç noktalar, son kullanıcının grubun e-posta adresinin bir parçası olarak kullanılacak bir Mailtakma ad veya diğer ad belirtmesini sağlar. Aşağıdaki yüksek ayrıcalıklı e-posta diğer adlarını içeren gruplar yalnızca bir Azure AD Genel Yöneticisi tarafından oluşturulabilir. 
  
* uygunsuz
* yöneticileri
* danışın
* HOSTMASTER
* Majordomo
* postmaster
* kök
* güvenli
* güvenlik
* SSL-yönetici
* Web

## <a name="group-writeback-to-on-premises-preview"></a>Şirket içi için Grup geri yazma (Önizleme)

Günümüzde, çok sayıda grup hala şirket içi Active Directory yönetilmektedir. Bulut gruplarını şirket içine geri eşitleme isteklerini yanıtlamak için, Azure AD için Office 365 grupları geri yazma özelliği artık önizleme için kullanılabilir.

Office 365 grupları bulutta oluşturulur ve yönetilir. Geri yazma özelliği, Exchange yüklü bir Active Directory ormanında Office 365 gruplarını dağıtım grupları olarak yazmanızı sağlar. Şirket içi Exchange posta kutularına sahip kullanıcılar, bu gruplardan e-posta gönderip alabilir. Grup geri yazma özelliği, Azure AD güvenlik gruplarını veya dağıtım gruplarını desteklemez.

Daha fazla ayrıntı için lütfen [Azure AD Connect Eşitleme hizmetine](../hybrid/how-to-connect-syncservice-features.md)yönelik belgelere bakın.

Office 365 grup geri yazma, Azure Active Directory (Azure AD) genel önizleme özelliğidir ve ücretli Azure AD lisans planıyla birlikte kullanılabilir. Önizlemeler hakkında bazı yasal bilgiler için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="next-steps"></a>Sonraki adımlar

[Azure Active Directory cmdlet 'lerinde](/powershell/azure/install-adv2?view=azureadps-2.0)daha fazla Azure Active Directory PowerShell belgesi bulabilirsiniz.

* [Azure Active Directory grupları ile kaynaklara erişimi yönetme](../fundamentals/active-directory-manage-groups.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](../hybrid/whatis-hybrid-identity.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
