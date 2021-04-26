---
title: Azure AD Connect eşitleme hizmeti özellikleri ve yapılandırması | Microsoft Docs
description: Azure AD Connect eşitleme hizmeti için hizmet tarafı özelliklerini açıklar.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 213aab20-0a61-434a-9545-c4637628da81
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/18/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 261ab5d0f039705a2566b7c28ff4c06778bb661a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94410547"
---
# <a name="azure-ad-connect-sync-service-features"></a>Azure AD Connect eşitleme hizmeti özellikleri

Azure AD Connect eşitleme özelliğinin iki bileşeni vardır:

* **Eşitleme altyapısı** olarak da adlandırılan **Azure AD Connect Sync** adlı şirket içi bileşen.
* Azure AD 'de bulunan hizmet **Azure AD Connect eşitleme hizmeti** olarak da bilinir

Bu konu, **Azure AD Connect eşitleme hizmetinin** aşağıdaki özelliklerinin nasıl çalıştığını ve Windows PowerShell kullanarak bunları nasıl yapılandırabileceğinizi açıklamaktadır.

Bu ayarlar, [Windows PowerShell için Azure Active Directory modülü](/previous-versions/azure/jj151815(v=azure.100))tarafından yapılandırılır. Azure AD Connect 'den ayrı olarak indirin ve yükleyin. Bu konuda belgelenen cmdlet 'ler [2016 Mart sürümünde (derleme 9031,1)](https://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1)sunulmuştur. Bu konu başlığında belgelenmeyen cmdlet 'ler yoksa veya aynı sonucu üretmedikleri takdirde, en son sürümü çalıştırdığınızdan emin olun.

Azure AD dizininizde yapılandırmayı görmek için çalıştırın `Get-MsolDirSyncFeatures` .  
![Get-MsolDirSyncFeatures sonucu](./media/how-to-connect-syncservice-features/getmsoldirsyncfeatures.png)

Bu ayarların birçoğu yalnızca Azure AD Connect ile değiştirilebilir.

Aşağıdaki ayarlar tarafından yapılandırılabilir `Set-MsolDirSyncFeature` :

| DirSyncFeature | Yorum |
| --- | --- |
| [EnableSoftMatchOnUpn](#userprincipalname-soft-match) |Birincil SMTP adresinin yanı sıra, nesnelerin userPrincipalName öğesine katılmasına izin verir. |
| [SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) |Eşitleme altyapısının yönetilen/lisanslanmış (Federe olmayan) kullanıcılar için userPrincipalName özniteliğini güncelleştirmesine izin verir. |

Bir özellik etkinleştirildikten sonra yeniden devre dışı bırakılamaz.

> [!NOTE]
> 24 Ağustos 2016 ' den yeni Azure AD dizinleri için özellik *yinelenen öznitelik dayanıklılığı* varsayılan olarak etkinleştirilmiştir. Bu özellik ayrıca bu tarihten önce oluşturulan dizinlerde de kullanıma sunulacaktır ve etkinleştirilir. Dizininiz bu özelliği etkinleştirmek üzere olduğunda bir e-posta bildirimi alacaksınız.
> 
> 

Aşağıdaki ayarlar Azure AD Connect tarafından yapılandırılır ve şu şekilde değiştirilemez `Set-MsolDirSyncFeature` :

| DirSyncFeature | Yorum |
| --- | --- |
| Devicegeri yazma |[Azure AD Connect: cihaz geri yazmayı etkinleştirme](how-to-connect-device-writeback.md) |
| DirectoryExtensions |[Eşitleme Azure AD Connect: Dizin uzantıları](how-to-connect-sync-feature-directory-extensions.md) |
| [DuplicateProxyAddressResiliency <br/> duplicateupnresiliency](#duplicate-attribute-resiliency) |Bir özniteliğin dışarı aktarma sırasında tüm nesnenin başarısız olması yerine başka bir nesnenin yinelemesi olduğunda karantinaya almasına izin verir. |
| Parola Karması Eşitleme |[Azure AD Connect Sync ile parola karması eşitlemesi uygulama](how-to-connect-password-hash-synchronization.md) |
|Doğrudan Kimlik Doğrulama|[Azure Active Directory Geçişli Kimlik Doğrulaması ile kullanıcı oturumu açma](how-to-connect-pta.md)|
| UnifiedGroupWriteback |Grup geri yazma|
| Usergeri yazma |Şu anda desteklenmiyor. |

## <a name="duplicate-attribute-resiliency"></a>Yinelenen öznitelik dayanıklılığı

Yinelenen UPN/proxyAddresses ile nesne sağlamak yerine, çoğaltılan öznitelik "karantinaya alındı" ve geçici bir değer atanır. Çakışma çözümlenirse, geçici UPN otomatik olarak doğru değere değiştirilir. Daha ayrıntılı bilgi için bkz. [kimlik eşitlemesi ve yinelenen öznitelik dayanıklılığı](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="userprincipalname-soft-match"></a>UserPrincipalName Soft Match

Bu özellik etkinleştirildiğinde, her zaman etkin olan [BIRINCIL SMTP adresine](https://support.microsoft.com/kb/2641663)ek olarak UPN için geçici eşleşme etkinleştirilir. Geçici eşleşme, Azure AD 'deki mevcut bulut kullanıcılarını şirket içi kullanıcılarla eşleştirmek için kullanılır.

Bulutta oluşturulan mevcut hesaplarla şirket içi AD hesaplarını değiştirmeniz gerekiyorsa ve Exchange Online 'ı kullanmıyorsanız, bu özellik kullanışlıdır. Bu senaryoda, genellikle bulutta SMTP özniteliğini ayarlama nedeninizi yoktur.

Bu özellik, yeni oluşturulan Azure AD dizinleri için varsayılan olarak açık olur. Aşağıdakileri çalıştırarak bu özelliğin sizin için etkinleştirilip etkinleştirilmediğini görebilirsiniz:  

```powershell
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

Bu özellik Azure AD dizininiz için etkinleştirilmemişse, şunu çalıştırarak etkinleştirebilirsiniz:  

```powershell
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## <a name="synchronize-userprincipalname-updates"></a>UserPrincipalName güncelleştirmelerini eşitler

Geçmişte, bu koşulların her ikisi de doğru olmadığı takdirde, şirket içi bilgisayardan eşitleme hizmetini kullanan UserPrincipalName özniteliğinde yapılan güncelleştirmeler engellenmiştir:

* Kullanıcı yönetiliyor (Federasyon dışı).
* Kullanıcıya bir lisans atanmadı.

> [!NOTE]
> Mart 2019 ' den, Federal Kullanıcı hesapları için UPN değişikliklerini eşitlemeye izin verilir.
> 

Bu özelliğin etkinleştirilmesi, eşitleme altyapısının şirket içinde değiştirildiğinde userPrincipalName öğesini güncelleştirmesine ve parola karması eşitlemesini ya da geçişli kimlik doğrulamasını kullanmanıza olanak sağlar.

Bu özellik, yeni oluşturulan Azure AD dizinleri için varsayılan olarak açık olur. Aşağıdakileri çalıştırarak bu özelliğin sizin için etkinleştirilip etkinleştirilmediğini görebilirsiniz:  

```powershell
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

Bu özellik Azure AD dizininiz için etkinleştirilmemişse, şunu çalıştırarak etkinleştirebilirsiniz:  

```powershell
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

Bu özelliği etkinleştirdikten sonra, mevcut userPrincipalName değerleri olduğu gibi kalır. Şirket içinde userPrincipalName özniteliğinin bir sonraki değiştiğinde, kullanıcılar üzerindeki normal Delta eşitlemesi UPN 'yi güncelleştirecektir.  

## <a name="see-also"></a>Ayrıca bkz.

* [Azure AD Connect eşitleme](how-to-connect-sync-whatis.md)
* [Şirket içi kimliklerinizi Azure Active Directory Ile tümleştirme](whatis-hybrid-identity.md).