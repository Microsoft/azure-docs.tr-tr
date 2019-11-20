---
title: PowerShell kullanarak Grup ayarlarını yapılandırma-Azure AD | Microsoft Docs
description: Azure Active Directory cmdlet 'lerini kullanarak grupların ayarlarını yönetme
services: active-directory
documentationcenter: ''
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
ms.openlocfilehash: 6a1b900d4a67390ae867d770c3b984c43fd501b5
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74026749"
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Grup ayarlarını yapılandırmak için Azure Active Directory cmdlet'leri

Bu makale, grupları oluşturmak ve güncelleştirmek için Azure Active Directory (Azure AD) PowerShell cmdlet 'lerini kullanmaya yönelik yönergeler içerir. Bu içerik yalnızca Office 365 grupları için geçerlidir (bazen birleştirilmiş gruplar olarak adlandırılır).

> [!IMPORTANT]
> Bazı ayarlarda Azure Active Directory Premium P1 lisansı gerekir. Daha fazla bilgi için [şablon ayarları](#template-settings) tablosuna bakın.

Yönetici olmayan kullanıcıların güvenlik grupları oluşturmasını engelleme hakkında daha fazla bilgi için, [set-MSOLCompanySettings](https://docs.microsoft.com/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)bölümünde açıklandığı gibi `Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False` ayarlayın.

Office 365 grupları ayarları, bir ayarlar nesnesi ve bir SettingsTemplate nesnesi kullanılarak yapılandırılır. İlk olarak, dizininiz varsayılan ayarlarla yapılandırıldığından, dizininizde herhangi bir ayar nesnesi görmezsiniz. Varsayılan ayarları değiştirmek için, bir ayarlar şablonu kullanarak yeni bir ayar nesnesi oluşturmanız gerekir. Ayarlar şablonları Microsoft tarafından tanımlanır. Birçok farklı ayar şablonu vardır. Dizininizde Office 365 grup ayarlarını yapılandırmak için "Group. Unified" adlı şablonu kullanın. Tek bir grupta Office 365 grup ayarlarını yapılandırmak için, "Group. Unified. Guest" adlı şablonu kullanın. Bu şablon, bir Office 365 grubuna konuk erişimini yönetmek için kullanılır. 

Cmdlet 'ler Azure Active Directory PowerShell V2 modülünün bir parçasıdır. Modülün bilgisayarınıza nasıl indirileceği ve yükleneceğine ilişkin yönergeler için [PowerShell sürüm 2 Azure Active Directory](https://docs.microsoft.com/powershell/azuread/)makalesine bakın. Modülün sürüm 2 sürümünü [PowerShell galerisinden](https://www.powershellgallery.com/packages/AzureAD/)yükleyebilirsiniz.

## <a name="install-powershell-cmdlets"></a>PowerShell cmdlet'lerini yükleme

PowerShell komutlarını çalıştırmadan önce Windows PowerShell Graph için Azure Active Directory PowerShell Modülünün eski sürümlerini kaldırın ve [Graph için Azure Active Directory PowerShell - Genel Önizleme Sürümünü 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) yükleyin.

1. Windows PowerShell uygulamasını yönetici olarak açın.
2. Eski AzureADPreview sürümlerini kaldırın.
  
   ``` PowerShell
   Uninstall-Module AzureADPreview
   Uninstall-Module azuread
   ```

3. En son AzureADPreview sürümünü yükleyin.
  
   ``` PowerShell
   Install-Module AzureADPreview
   ```
   
## <a name="create-settings-at-the-directory-level"></a>Dizin düzeyinde ayarlar oluşturma
Bu adımlar dizin düzeyinde, dizindeki tüm Office 365 grupları için uygulanan ayarları oluşturur. Get-AzureADDirectorySettingTemplate cmdlet 'i yalnızca [Graph Için Azure AD PowerShell önizleme modülünde](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137)kullanılabilir.

1. DirectorySettings cmdlet 'lerinde, kullanmak istediğiniz SettingsTemplate KIMLIĞINI belirtmeniz gerekir. Bu KIMLIĞI görmüyorsanız, bu cmdlet tüm ayarlar şablonlarının listesini döndürür:
  
   ```powershell
   Get-AzureADDirectorySettingTemplate

   ```
   Bu cmdlet çağrısı kullanılabilir tüm şablonları döndürür:
  
   ```powershell
   Id                                   DisplayName         Description
   --                                   -----------         -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Office 365 group
   16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
   ```
2. Kullanım Kılavuzu URL 'SI eklemek için, önce kullanım kılavuzu URL 'SI değerini tanımlayan SettingsTemplate nesnesini almanız gerekir; Yani, Group. Unified şablonu:
  
   ```powershell
   $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
   ```
3. Ardından, bu şablonu temel alan yeni bir ayar nesnesi oluşturun:
  
   ```powershell
   $Setting = $template.CreateDirectorySetting()
   ```  
4. Ardından Kullanım Kılavuzu değerini güncelleştirin:
  
   ```powershell
   $Setting["UsageGuidelinesUrl"] = "https://guideline.example.com"
   ```  
5. Daha sonra ayarı uygulayın:
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
6. Şunu kullanarak değerleri okuyabilirsiniz:

   ```powershell
   $Setting.Values
   ```  
## <a name="update-settings-at-the-directory-level"></a>Ayarları dizin düzeyinde Güncelleştir
Ayar şablonunda Usageusagelinesurl değerini güncelleştirmek için yukarıdaki 4. adımda URL 'YI düzenleyin, ardından yeni değeri ayarlamak için 5. adımı gerçekleştirin.

Usagekılavuz Linesurl değerini kaldırmak için, URL 'YI yukarıdaki 4. adım kullanarak boş bir dize olacak şekilde düzenleyin:

   ```powershell
   $Setting["UsageGuidelinesUrl"] = ""
   ```  
Ardından yeni değeri ayarlamak için 5. adımı gerçekleştirin.

## <a name="template-settings"></a>Şablon ayarları
Burada, Group. Unified SettingsTemplate içinde tanımlanan ayarlar verilmiştir. Aksi belirtilmedikçe, bu özellikler Azure Active Directory Premium P1 lisansı gerektirir. 

| **Ayar** | **Açıklama** |
| --- | --- |
|  <ul><li>EnableGroupCreation<li>Tür: Boolean<li>Varsayılan: true |Yönetici olmayan kullanıcılar tarafından, dizinde Office 365 Grup oluşturmaya izin verilip verilmeyeceğini belirten bayrak. Bu ayar Azure Active Directory Premium P1 lisansı gerektirmez.|
|  <ul><li>GroupCreationAllowedGroupId<li>Türü: Dize<li>Varsayılan: "" |Enablegroupcreate = = false olduğunda bile üyelerin Office 365 grupları oluşturmalarına izin verilen güvenlik grubunun GUID 'SI. |
|  <ul><li>Usagekılavuz Linesurl 'Si<li>Türü: Dize<li>Varsayılan: "" |Grup kullanım yönergelerine bir bağlantı. |
|  <ul><li>ClassificationDescriptions<li>Türü: Dize<li>Varsayılan: "" | Sınıflandırma açıklamalarının virgülle ayrılmış listesi. ClassificationDescriptions değeri yalnızca bu biçimde geçerlidir:<br>$setting ["ClassificationDescriptions"] = "sınıflandırma: Açıklama, sınıflandırma: Açıklama"<br>Burada sınıflandırma, ClassificationList dizeler ile eşleşir.|
|  <ul><li>DefaultClassification<li>Türü: Dize<li>Varsayılan: "" | Hiçbiri belirtilmemişse, bir grup için varsayılan sınıflandırma olarak kullanılacak sınıflandırma.|
|  <ul><li>PrefixSuffixNamingRequirement<li>Türü: Dize<li>Varsayılan: "" | Office 365 grupları için yapılandırılmış adlandırma kuralını tanımlayan en fazla 64 karakter uzunluğunda bir dize. Daha fazla bilgi için bkz. [Office 365 grupları için adlandırma Ilkesi zorlama](groups-naming-policy.md). |
| <ul><li>CustomBlockedWordsList<li>Türü: Dize<li>Varsayılan: "" | Kullanıcıların Grup adlarında veya diğer adlarla kullanmasına izin verilmeyen, virgülle ayrılmış deyimlerin dizesi. Daha fazla bilgi için bkz. [Office 365 grupları için adlandırma Ilkesi zorlama](groups-naming-policy.md). |
| <ul><li>EnableMSStandardBlockedWords<li>Tür: Boolean<li>Varsayılan: "false" | Kullanmayın
|  <ul><li>AllowGuestsToBeGroupOwner<li>Tür: Boolean<li>Varsayılan: false | Konuk kullanıcının grupların sahibi olup olmayacağını gösteren Boolean. |
|  <ul><li>AllowGuestsToAccessGroups<li>Tür: Boolean<li>Varsayılan: true | Konuk kullanıcının Office 365 grupları içeriğine erişip erişemeyeceğini gösteren Boolean.  Bu ayar Azure Active Directory Premium P1 lisansı gerektirmez.|
|  <ul><li>Guestusagekılavuz Linesurl 'Si<li>Türü: Dize<li>Varsayılan: "" | Konuk kullanım yönergelerine bir bağlantının URL 'si. |
|  <ul><li>AllowAddGuests<li>Tür: Boolean<li>Varsayılan: true | Bu dizine Konuk ekleme izni verilip verilmeyeceğini gösteren bir Boole değeri.|
|  <ul><li>ClassificationList<li>Türü: Dize<li>Varsayılan: "" |Office 365 gruplarına uygulanabilen geçerli sınıflandırma değerlerinin virgülle ayrılmış bir listesi. |

## <a name="example-configure-guest-policy-for-groups-at-the-directory-level"></a>Örnek: dizin düzeyindeki gruplar için konuk ilkesini yapılandırma
1. Tüm ayar şablonlarını al:
   ```powershell
   Get-AzureADDirectorySettingTemplate
   ```
2. Dizin düzeyindeki grupların Konuk ilkesini ayarlamak için Group. Unified şablonuna ihtiyacınız vardır
   ```powershell
   $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
   ```
3. Ardından, bu şablonu temel alan yeni bir ayar nesnesi oluşturun:
  
   ```powershell
   $Setting = $template.CreateDirectorySetting()
   ```  
4. Ardından AllowAddGuests ayarını güncelleştirin
   ```powershell
   $Setting["AllowAddGuests"] = $False
   ```  
5. Daha sonra ayarı uygulayın:
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
6. Şunu kullanarak değerleri okuyabilirsiniz:

   ```powershell
   $Setting.Values
   ```   

## <a name="read-settings-at-the-directory-level"></a>Dizin düzeyindeki ayarları oku

Almak istediğiniz ayarın adını biliyorsanız, geçerli ayarlar değerini almak için aşağıdaki cmdlet 'i kullanabilirsiniz. Bu örnekte, "Usagekılavuz Linesurl" adlı bir ayarın değerini aldık. 

   ```powershell
   (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
   ```
Bu adımlar dizin düzeyindeki ayarları okur ve dizindeki tüm Office grupları için geçerlidir.

1. Tüm mevcut dizin ayarlarını okuyun:
   ```powershell
   Get-AzureADDirectorySetting -All $True
   ```
   Bu cmdlet tüm dizin ayarlarının bir listesini döndürür:
   ```powershell
   Id                                   DisplayName   TemplateId                           Values
   --                                   -----------   ----------                           ------
   c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
   ```

2. Belirli bir grubun tüm ayarlarını okuyun:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
   ```

3. Belirli bir dizin ayarları nesnesinin tüm dizin ayarları değerlerini, ayarlar KIMLIĞI GUID 'ı kullanarak okuyun:
   ```powershell
   (Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values
   ```
   Bu cmdlet bu belirli Grup için bu ayarlar nesnesindeki adları ve değerleri döndürür:
   ```powershell
   Name                          Value
   ----                          -----
   ClassificationDescriptions
   DefaultClassification
   PrefixSuffixNamingRequirement
   CustomBlockedWordsList        
   AllowGuestsToBeGroupOwner     False 
   AllowGuestsToAccessGroups     True
   GuestUsageGuidelinesUrl
   GroupCreationAllowedGroupId
   AllowAddGuests              True
   UsageGuidelinesUrl            https://guideline.example.com
   ClassificationList
   EnableGroupCreation           True
   ```

## <a name="remove-settings-at-the-directory-level"></a>Dizin düzeyindeki ayarları kaldır
Bu adım dizin düzeyindeki ayarları kaldırır ve dizindeki tüm Office grupları için geçerlidir.
   ```powershell
   Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
   ```

## <a name="create-settings-for-a-specific-group"></a>Belirli bir grup için ayarlar oluşturma

1. "Gruplar. Unified. Guest" adlı ayarlar şablonunu arayın
   ```powershell
   Get-AzureADDirectorySettingTemplate
  
   Id                                   DisplayName            Description
   --                                   -----------            -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified          ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest    Settings for a specific Office 365 group
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application            ...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule Settings ...
   ```
2. Gruplar. Unified. Guest şablonu için şablon nesnesini alın:
   ```powershell
   $Template1 = Get-AzureADDirectorySettingTemplate -Id 08d542b9-071f-4e16-94b0-74abb372e3d9
   ```
3. Şablondan yeni bir ayarlar nesnesi oluşturun:
   ```powershell
   $SettingCopy = $Template1.CreateDirectorySetting()
   ```

4. Ayarı gerekli değer olarak ayarlayın:
   ```powershell
   $SettingCopy["AllowAddGuests"]=$False
   ```
5. Bu ayarı uygulamak istediğiniz grubun KIMLIĞINI alın:
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
6. Dizinde gerekli grup için yeni ayarı oluşturun:
   ```powershell
   New-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -DirectorySetting $SettingCopy
   ```
7. Ayarları doğrulamak için şu komutu çalıştırın:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="update-settings-for-a-specific-group"></a>Belirli bir grup için ayarları güncelleştirme
1. Ayarını güncelleştirmek istediğiniz grubun KIMLIĞINI alın:
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
2. Grup ayarını alın:
   ```powershell
   $Setting = Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
3. Grup ayarını gereksinim duyduğunuz şekilde güncelleştirin, ör.
   ```powershell
   $Setting["AllowAddGuests"] = $True
   ```
4. Sonra bu belirli Grup için ayarın KIMLIĞINI alın:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
   Şuna benzer bir yanıt alacaksınız:
   ```powershell
   Id                                   DisplayName            TemplateId                             Values
   --                                   -----------            -----------                            ----------
   2dbee4ca-c3b6-4f0d-9610-d15569639e1a Group.Unified.Guest    08d542b9-071f-4e16-94b0-74abb372e3d9   {class SettingValue {...
   ```
5. Daha sonra bu ayar için yeni değeri ayarlayabilirsiniz:
   ```powershell
   Set-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -Id 2dbee4ca-c3b6-4f0d-9610-d15569639e1a -DirectorySetting $Setting
   ```
6. Doğru güncelleştirildiğinden emin olmak için ayarın değerini okuyabilirsiniz:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="cmdlet-syntax-reference"></a>Cmdlet söz dizimi başvurusu
[Azure Active Directory cmdlet 'lerinde](/powershell/azure/install-adv2?view=azureadps-2.0)daha fazla Azure Active Directory PowerShell belgesi bulabilirsiniz.

## <a name="additional-reading"></a>Ek okuma

* [Azure Active Directory grupları ile kaynaklara erişimi yönetme](../fundamentals/active-directory-manage-groups.md)
* [Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](../hybrid/whatis-hybrid-identity.md)
