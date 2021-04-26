---
title: Azure AD Connect Cloud sağlama Aracısı gMSA PowerShell cmdlet 'leri
description: Azure AD Connect Cloud sağlama Aracısı gMSA PowerShell cmdlet 'lerini kullanmayı öğrenin.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46b5c94c84bdbd0e5bb0d7b08ba9296ebfcd1838
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306067"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-gmsa-powershell-cmdlets"></a>Azure AD Connect Cloud sağlama Aracısı gMSA PowerShell cmdlet 'leri

Bu belgenin amacı, Azure AD Connect Cloud sağlama Aracısı gMSA PowerShell cmdlet 'lerini tanımlıyor. Bu cmdlet 'ler, hizmet hesabına (gMSA) uygulanan izinler üzerinde daha fazla ayrıntı sahibi olmasını sağlar. Varsayılan olarak, Azure AD Connect bulut eşitlemesi varsayılan gMSA veya özel bir gMSA 'ya Azure AD Connect benzer tüm izinleri uygular. 

Bu belge aşağıdaki cmdlet 'leri kapsar:  

`Set-AADCloudSyncRestrictedPermissions`

`Set-AADCloudSyncPermissions` 

## <a name="how-to-use-the-cmdlets"></a>Cmdlet 'leri kullanma:  

Bu cmdlet 'leri kullanmak için aşağıdaki Önkoşullar gereklidir.

1. Sağlama aracısını yükler. 
2. Sağlama Aracısı PS modülünü bir PowerShell oturumuna içeri aktarın. 

 ```PowerShell
 Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Microsoft.CloudSync.Powershell.dll"  
 ```
3. Var olan izinleri kaldırın.  Hizmet hesabındaki tüm mevcut izinleri kaldırmak için, kendi kendine kullanımı hariç: `Set-AADCloudSyncRestrictedPermission` .  

    Bu cmdlet, geçirilebilen bir parametre gerektirir ya da bu `Credential` , olmadan çağrılırsa istem ister.

    Değişken kullanımı oluşturmak için  

   `$credential = Get-Credential` 

   Bu, kullanıcıdan Kullanıcı adı ve parola girmesini ister. Kimlik bilgileri, en az bir etki alanı Yöneticisi (aracının yüklü olduğu etki alanında) olmalıdır ve kurumsal yönetici de olabilir. 

4.  Daha sonra ek izinleri kaldırmak için cmdlet 'ini çağırabilirsiniz: 
   ```PowerShell
   Set-AADCloudSyncRestrictedPermissions -Credential $credential 
   ```
5. Ya da yalnızca 

   `Set-AADCloudSyncRestrictedPermissions` Bu, kimlik bilgilerini ister. 

 6.  Belirli izin türü ekleyin.  Eklenen izinler Azure AD Connect aynıdır.  İzinleri ayarlamayla ilgili örnekler için bkz. [set-AADCloudSyncPermissions kullanma](#using-set-aadcloudsyncpermissions) .

## <a name="using-set-aadcloudsyncpermissions"></a>Set-AADCloudSyncPermissions kullanma 
`Set-AADCloudSyncPermissions` , Azure AD Connect tarafından kullanılan izinlerle aynı olan aşağıdaki izin türlerini destekler. Aşağıdaki izin türleri desteklenir: 

|İzin türü|Açıklama|
|-----|-----|
|BasicRead| Azure AD Connect için [Basicread](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#configure-basic-read-only-permissions) izinlerine bakın|
|PasswordHashSync|Azure AD Connect için [Passwordhashsync](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-password-hash-synchronization) izinlerine bakın|
|Passwordgeri yazma|Azure AD Connect için [Passwordgeri yazma](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-password-writeback) izinleri bölümüne bakın|
|HybridExchangePermissions|Azure AD Connect için bkz. [Hybridexchangepermissions](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-exchange-hybrid-deployment) izinleri| 
|ExchangeMailPublicFolderPermissions| Azure AD Connect için [Exchangemailpublicfolderpermissions](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-exchange-mail-public-folders-preview) izinleri bölümüne bakın| 
|CloudHR| ' Alt Kullanıcı nesneleri ' üzerinde ' Full Control ' ve ' Bu nesne ve tüm alt nesneler ' üzerinde ' Kullanıcı nesneleri oluştur/Sil ' öğesine uygular| 
|Tümü|Yukarıdaki tüm izinleri ekler.| 



AADCloudSyncPermissions öğesini iki şekilde kullanabilirsiniz:
- [Yapılandırılan tüm etki alanlarına belirli bir izin verme](#grant-a-certain-permission-to-all-configured-domains) 
- [Belirli bir etki alanına belirli bir izin verme](#grant-a-certain-permission-to-a-specific-domain) 
## <a name="grant-a-certain-permission-to-all-configured-domains"></a>Yapılandırılan tüm etki alanlarına belirli bir izin verme 
Tüm yapılandırılmış etki alanlarına belirli izinlerin verilmesi, bir kurumsal yönetici hesabının kullanılmasını gerektirir.


 ```PowerShell
Set-AADCloudSyncPermissions -PermissionType “Any mentioned above” -EACredential $credential (prepopulated same as above [$credential = Get-Credential]) 
```

## <a name="grant-a-certain-permission-to-a-specific-domain"></a>Belirli bir etki alanına belirli bir izin verme 
Belirli bir etki alanına belirli izinler verilmesi, eklemeye çalıştığınız etki alanının en az bir etki alanı yönetici hesabında kullanılmasını gerektirir.


 ```PowerShell
Set-AADCloidSyncPermissions -PermissionType “Any mentioned above” -TargetDomain “FQDN of domain” (has to be already configured through wizard) -TargetDomaincredential $credential(same as above) 
```
 

Note: 1. Kimlik bilgileri, en düşük kurumsal yöneticde olmalıdır. 

2 için. Kimlik bilgileri, etki alanı yöneticisi veya kuruluş yöneticisi olabilir. 

  

