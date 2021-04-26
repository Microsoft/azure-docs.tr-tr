---
title: 'Azure AD Connect: AD DS bağlayıcı hesabı Izinlerini yapılandırma | Microsoft Docs'
description: Bu belgede AD DS bağlayıcı hesabının yeni ADSyncConfig PowerShell modülüyle nasıl yapılandırılacağı anlatılmaktadır
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/18/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 62bfc528886767bc09159ca2a2696c8c9264b307
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96349948"
---
# <a name="azure-ad-connectconfigure-ad-ds-connector-account-permissions"></a>Azure AD Connect: AD DS Bağlayıcı Hesabı İzinlerini Yapılandırma 

[Adsyncconfig. psm1](reference-connect-adsyncconfig.md) adlı PowerShell modülü, Azure AD Connect dağıtımınız için doğru Active Directory izinlerini yapılandırmanıza yardımcı olacak cmdlet 'lerin bir koleksiyonunu içeren Build 1.1.880.0 (2018 Ağustos ayında yayımlanmıştır) ile tanıtılmıştır. 

## <a name="overview"></a>Genel Bakış 
Aşağıdaki PowerShell cmdlet 'leri, Azure AD Connect etkinleştirmek üzere seçtiğiniz her bir özellik için AD DS bağlayıcı hesabının Active Directory izinlerini ayarlamak için kullanılabilir. Herhangi bir sorunu engellemek için, ormanınıza bağlanmak üzere özel bir etki alanı hesabı kullanarak Azure AD Connect yüklemek istediğinizde Active Directory izinleri önceden hazırlamanız gerekir. Bu ADSyncConfig modülü, Azure AD Connect dağıtıldıktan sonra izinleri yapılandırmak için de kullanılabilir.

![AD DS hesabına genel bakış](media/how-to-connect-configure-ad-ds-connector-account/configure1.png)

Azure AD Connect Express yüklemesinde, otomatik olarak oluşturulan bir hesap (MSOL_nnnnnnnnnn) tüm gerekli izinlerle Active Directory oluşturulur. bu nedenle, kurumsal birimlerde veya Azure AD 'ye eşitlenmesini istediğiniz belirli Active Directory nesnelerinde izin devralmayı engelleyemiyorsanız bu ADSyncConfig modülünü kullanmanız gerekmez. 
 
### <a name="permissions-summary"></a>İzin özeti 
Aşağıdaki tabloda AD nesnelerinde gerekli izinlerin özeti verilmiştir: 

| Özellik | İzinler |
| --- | --- |
| ms-DS-ımlarımguıd özelliği |[Tasarım kavramları](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor)bölümünde belgelenen ms-DS-ımıbu GUID özniteliğinde okuma ve yazma izinleri | 
| Parola karması eşitleme |<li>Dizin Değişikliklerini Çoğalt</li>  <li>Dizin değişikliklerini çoğaltma |
| Exchange karma dağıtımı |Kullanıcılar, gruplar ve kişiler için [Exchange hibrit geri yazma](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) bölümünde belgelenen özniteliklere yönelik okuma ve yazma izinleri. |
| Exchange posta genel klasörü |Ortak klasörler için [Exchange posta ortak klasöründe](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) belgelenen özniteliklere yönelik okuma izinleri. | 
| Parola geri yazma |Kullanıcılar için [parola yönetimine](../authentication/tutorial-enable-sspr-writeback.md) Başlarken bölümünde belgelenen özniteliklere yönelik okuma ve yazma izinleri. |
| Cihaz geri yazma |Cihaz nesnelerine ve [cihaz geri yazma](how-to-connect-device-writeback.md)bölümünde belgelenen kapsayıcılara yönelik okuma ve yazma izinleri. |
| Grup geri yazma |Eşitlenmiş **Office 365 grupları** için Grup nesnelerini okuyun, oluşturun, güncelleştirin ve silin.|

## <a name="using-the-adsyncconfig-powershell-module"></a>ADSyncConfig PowerShell modülünü kullanma 
ADSyncConfig modülü AD DS PowerShell modülüne ve araçlarına bağlı olduğundan, [AD DS için uzak sunucu yönetim araçları (RSAT)](/windows-server/remote/remote-server-administration-tools) gerektirir. AD DS için RSAT 'yi yüklemek için, ' yönetici olarak Çalıştır ' ile bir Windows PowerShell penceresi açın ve yürütün: 

``` powershell
Install-WindowsFeature RSAT-AD-Tools 
```
![Yapılandırma](media/how-to-connect-configure-ad-ds-connector-account/configure2.png)

>[!NOTE]
>Ayrıca, **C:\Program Files\Microsoft Azure Active Directory Connect\adsyncconfig\adsyncconfig.exe. psm1** dosyasını, zaten AD DS yüklü olan ve bu PowerShell modülünü buradan kullanan bir etki alanı denetleyicisine kopyalayabilirsiniz.

ADSyncConfig ' i kullanmaya başlamak için modülü bir Windows PowerShell penceresinde yüklemeniz gerekir: 

``` powershell
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1" 
```

Bu modüle dahil edilen tüm cmdlet 'leri denetlemek için şunu yazabilirsiniz:  

``` powershell
Get-Command -Module AdSyncConfig  
```

![İşaretli](media/how-to-connect-configure-ad-ds-connector-account/configure3.png)

Her cmdlet, AD DS bağlayıcı hesabı ve bir AdminSDHolder anahtarı girmek için aynı parametrelere sahiptir. AD DS bağlayıcı hesabınızı belirtmek için hesap adı ve etki alanı ya da yalnızca hesap ayırt edici adı (DN) sağlayabilirsiniz.

Örneğin:

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <ADAccountName> -ADConnectorAccountDomain <ADDomainName>
```

Veya

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <ADAccountDN>
```

`<ADAccountName>`, Ve ' ı `<ADDomainName>` `<ADAccountDN>` ortamınız için uygun değerlerle değiştirdiğinizden emin olun.

AdminSDHolder kapsayıcısında izinleri değiştirmek istemiyorsanız, anahtarını kullanın `-SkipAdminSdHolders` . 

Varsayılan olarak, tüm küme izinleri cmdlet 'leri ormandaki her etki alanının kökünde AD DS izinleri ayarlamaya çalışır, yani PowerShell oturumunu çalıştıran kullanıcının ormandaki her etki alanında etki alanı yöneticisi hakları olması gerekir.  Bu gereksinim nedeniyle, orman kökünden Kurumsal Yönetici kullanılması önerilir. Azure AD Connect dağıtımınızda birden çok AD DS Bağlayıcısı varsa, bir AD DS Bağlayıcısı bulunan her ormanda aynı cmdlet 'i çalıştırmak gerekecektir. 

Ayrıca, belirli bir OU veya AD DS nesnesi üzerinde izinleri, `-ADobjectDN` izinleri ayarlamak istediğiniz hedef NESNENIN DN 'sini kullanarak da ayarlayabilirsiniz. Bir hedef ADobjectDN kullanılırken, cmdlet yalnızca bu nesne üzerindeki izinleri ayarlar ve etki alanı kökünde veya AdminSDHolder kapsayıcısında değil. Bu parametre, izin kalıtımı devre dışı bırakılmış bazı OU 'Lar veya AD DS nesneler olduğunda yararlı olabilir (bkz. izin devralmayla AD DS nesneleri bulma devre dışı) 

Bu ortak parametrelerin özel durumları `Set-ADSyncRestrictedPermissions` , AD DS bağlayıcı hesabının kendisi üzerindeki izinleri ayarlamak için kullanılan cmdlet 'dir ve `Set-ADSyncPasswordHashSyncPermissions` Parola karması eşitleme için gereken izinler yalnızca etki alanı kökünde ayarlandıklarından cmdlet 'i, bu cmdlet `-ObjectDN` veya `-SkipAdminSdHolders` parametrelerini içermez.

### <a name="determine-your-ad-ds-connector-account"></a>AD DS bağlayıcı hesabınızı belirleme 
Azure AD Connect zaten yüklüyse ve şu anda Azure AD Connect tarafından kullanılan AD DS bağlayıcı hesabının ne olduğunu denetlemek istiyorsanız, cmdlet 'ini çalıştırabilirsiniz: 

``` powershell
Get-ADSyncADConnectorAccount 
```
### <a name="locate-ad-ds-objects-with-permission-inheritance-disabled"></a>İzin devralmayla AD DS nesneleri bulma devre dışı 
İzin devralmayla devre dışı bırakılmış AD DS nesne olup olmadığını denetlemek isterseniz, şunu çalıştırabilirsiniz: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' 
```
Varsayılan olarak, bu cmdlet yalnızca devre dışı devralma özellikli OU 'Ları arar, ancak diğer AD DS nesne sınıflarını `-ObjectClass` parametrede belirtebilir veya tüm nesne sınıfları için ' * ' öğesini aşağıdaki şekilde kullanabilirsiniz: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' -ObjectClass * 
```
 
### <a name="view-ad-ds-permissions-of-an-object"></a>Bir nesnenin AD DS izinlerini görüntüleme 
Aşağıdaki cmdlet 'i kullanarak, bir Active Directory nesnesi üzerinde şu anda ayarlanan izin listesini, distinguishedName 'dir sağlayarak görüntüleyebilirsiniz: 

``` powershell
Show-ADSyncADObjectPermissions -ADobjectDN '<DistinguishedName>' 
```

## <a name="configure-ad-ds-connector-account-permissions"></a>AD DS Bağlayıcı Hesabı İzinlerini Yapılandırma 
 
### <a name="configure-basic-read-only-permissions"></a>Temel Read-Only Izinlerini yapılandırma 
Herhangi bir Azure AD Connect özelliği kullanmadığınız sırada AD DS bağlayıcı hesabı için temel salt okuma izinlerini ayarlamak için şunu çalıştırın: 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


veya 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```


Bu cmdlet aşağıdaki izinleri ayarlar: 
 

|Tür |Name |Access |Uygulanan Öğe| 
|-----|-----|-----|-----|
|İzin Ver |AD DS bağlayıcı hesabı |Tüm özellikleri oku |Alt cihaz nesneleri| 
|İzin Ver |AD DS bağlayıcı hesabı|Tüm özellikleri oku |Alt InetOrgPerson nesneleri| 
|İzin Ver |AD DS bağlayıcı hesabı |Tüm özellikleri oku |Alt bilgisayar nesneleri| 
|İzin Ver |AD DS bağlayıcı hesabı |Tüm özellikleri oku |Descendant foreignSecurityPrincipal nesneleri| 
|İzin Ver |AD DS bağlayıcı hesabı |Tüm özellikleri oku |Alt grup nesneleri| 
|İzin Ver |AD DS bağlayıcı hesabı |Tüm özellikleri oku |Alt Kullanıcı nesneleri| 
|İzin Ver |AD DS bağlayıcı hesabı |Tüm özellikleri oku |Alt öğe Iletişim nesneleri| 

 
### <a name="configure-ms-ds-consistency-guid-permissions"></a>MS-DS-tutarlılık-GUID Izinlerini yapılandırma 
Kaynak bağlantısı olarak ms-DS-tutarlılık-GUID özniteliği ("bana kaynak bağlayıcısını benim için Azure 'un yönetmesine Izin ver" seçeneği olarak da bilinir) kullanılırken AD DS bağlayıcı hesabı izinlerini ayarlamak için şunu çalıştırın: 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```

veya 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Bu cmdlet aşağıdaki izinleri ayarlar: 

|Tür |Name |Access |Uygulanan Öğe|
|-----|-----|-----|-----| 
|İzin Ver|AD DS bağlayıcı hesabı|Okuma/yazma özelliği|Alt Kullanıcı nesneleri|

### <a name="permissions-for-password-hash-synchronization"></a>Parola karması eşitleme izinleri 
Parola karması eşitlemesini kullanırken AD DS bağlayıcı hesabı izinlerini ayarlamak için şunu çalıştırın: 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [<CommonParameters>] 
```


veya 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [<CommonParameters>] 
```

Bu cmdlet aşağıdaki izinleri ayarlar: 

|Tür |Name |Access |Uygulanan Öğe|
|-----|-----|-----|-----| 
|İzin Ver |AD DS bağlayıcı hesabı |Dizin değişikliklerini çoğaltma |Yalnızca bu nesne (etki alanı kökü)| 
|İzin Ver |AD DS bağlayıcı hesabı |Dizin değişikliklerini çoğaltma |Yalnızca bu nesne (etki alanı kökü)| 
  
### <a name="permissions-for-password-writeback"></a>Parola geri yazma izinleri 
Parola geri yazma özelliğini kullanırken AD DS bağlayıcı hesabı izinlerini ayarlamak için şunu çalıştırın: 

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


veya

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Bu cmdlet aşağıdaki izinleri ayarlar: 

|Tür |Name |Access |Uygulanan Öğe|
|-----|-----|-----|-----| 
|İzin Ver |AD DS bağlayıcı hesabı |Parola Sıfırlama |Alt Kullanıcı nesneleri| 
|İzin Ver |AD DS bağlayıcı hesabı |Write özelliği lockoutTime |Alt Kullanıcı nesneleri| 
|İzin Ver |AD DS bağlayıcı hesabı |Write özelliği pwdLastSet |Alt Kullanıcı nesneleri| 

### <a name="permissions-for-group-writeback"></a>Grup geri yazma izinleri 
Grup geri yazma kullanılırken AD DS bağlayıcı hesabı izinlerini ayarlamak için şunu çalıştırın: 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```
veya 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>]
```
 
Bu cmdlet aşağıdaki izinleri ayarlar: 

|Tür |Name |Access |Uygulanan Öğe|
|-----|-----|-----|-----| 
|İzin Ver |AD DS bağlayıcı hesabı |Genel okuma/yazma |Nesne türü grubu ve alt nesnelerin tüm öznitelikleri| 
|İzin Ver |AD DS bağlayıcı hesabı |Alt nesne Oluştur/Sil |Nesne türü grubu ve alt nesnelerin tüm öznitelikleri| 
|İzin Ver |AD DS bağlayıcı hesabı |Ağaç nesnelerini Sil/Sil|Nesne türü grubu ve alt nesnelerin tüm öznitelikleri|

### <a name="permissions-for-exchange-hybrid-deployment"></a>Exchange karma dağıtımı izinleri 
Exchange karma dağıtımı kullanırken AD DS bağlayıcı hesabı izinlerini ayarlamak için şunu çalıştırın: 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


veya 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Bu cmdlet aşağıdaki izinleri ayarlar:  
 

|Tür |Name |Access |Uygulanan Öğe|
|-----|-----|-----|-----| 
|İzin Ver |AD DS bağlayıcı hesabı |Tüm özellikleri oku/yaz |Alt Kullanıcı nesneleri| 
|İzin Ver |AD DS bağlayıcı hesabı |Tüm özellikleri oku/yaz |Alt InetOrgPerson nesneleri| 
|İzin Ver |AD DS bağlayıcı hesabı |Tüm özellikleri oku/yaz |Alt grup nesneleri| 
|İzin Ver |AD DS bağlayıcı hesabı |Tüm özellikleri oku/yaz |Alt öğe Iletişim nesneleri| 

### <a name="permissions-for-exchange-mail-public-folders-preview"></a>Exchange posta ortak klasörlerinin izinleri (Önizleme) 
Exchange posta ortak klasörleri 'ni kullanırken AD DS bağlayıcı hesabı izinlerini ayarlamak için şunu çalıştırın: 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


veya 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Bu cmdlet aşağıdaki izinleri ayarlar: 

|Tür |Name |Access |Uygulanan Öğe|
|-----|-----|-----|-----| 
|İzin Ver |AD DS bağlayıcı hesabı |Tüm özellikleri oku |Descendant PublicFolder nesneleri| 

### <a name="restrict-permissions-on-the-ad-ds-connector-account"></a>AD DS bağlayıcı hesabında Izinleri kısıtla 
Bu PowerShell betiği, bir parametre olarak sağlanmış olan AD Bağlayıcısı hesabının izinlerini güçlendirilecektir. Sıkı bir şekilde izinleri aşağıdaki adımları içerir: 

- Belirtilen nesnede devralmayı devre dışı bırak 
- Kendisine özgü olan Ace 'Ler hariç, kendı kendine özel izinleri korumak istediğimiz için, belirli bir nesnedeki tüm Ace 'Leri kaldırın. 
 
  -ADConnectorAccountDN parametresi, izinlerinin daha sıkı bir şekilde getirilmesi gereken AD hesabıdır. Bu, genellikle AD DS Bağlayıcısı 'nda yapılandırılan MSOL_nnnnnnnnnnnn etki alanı hesabıdır (bkz. AD DS bağlayıcı hesabınızı belirleme). -Credential parametresi, hedef AD nesnesinde Active Directory izinleri kısıtlamak için gerekli ayrıcalıklara sahip yönetici hesabını belirtmek için gereklidir. Bu genellikle kuruluş veya etki alanı yöneticisidir.  

``` powershell
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential> [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>] 
```
 
Örneğin: 

``` powershell
$credential = Get-Credential 
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN'CN=ADConnectorAccount,CN=Users,DC=Contoso,DC=com' -Credential $credential  
```

Bu cmdlet aşağıdaki izinleri ayarlar: 

|Tür |Name |Access |Uygulanan Öğe|
|-----|-----|-----|-----| 
|İzin Ver |SİSTEM |Tam Denetim |Bu nesne 
|İzin Ver |Enterprise Admins |Tam Denetim |Bu nesne 
|İzin Ver |Etki Alanı Yöneticileri |Tam Denetim |Bu nesne 
|İzin Ver |Yöneticiler |Tam Denetim |Bu nesne 
|İzin Ver |Kurumsal etki alanı denetleyicileri |Liste Içerikleri |Bu nesne 
|İzin Ver |Kurumsal etki alanı denetleyicileri |Tüm Özellikleri Oku |Bu nesne 
|İzin Ver |Kurumsal etki alanı denetleyicileri |Okuma Izinleri |Bu nesne 
|İzin Ver |Kimliği Doğrulanmış Kullanıcılar |Liste Içerikleri |Bu nesne 
|İzin Ver |Kimliği Doğrulanmış Kullanıcılar |Tüm Özellikleri Oku |Bu nesne 
|İzin Ver |Kimliği Doğrulanmış Kullanıcılar |Okuma Izinleri |Bu nesne 

## <a name="next-steps"></a>Sonraki Adımlar
- [Azure AD Connect: Hesaplar ve izinler](reference-connect-accounts-permissions.md)
- [Hızlı yükleme](how-to-connect-install-express.md)
- [Özel yükleme](how-to-connect-install-custom.md)
- [ADSyncConfig Başvurusu](reference-connect-adsyncconfig.md)