---
title: 'Azure AD Connect: AD DS bağlayıcı hesabı Izinlerini yapılandırma | Microsoft Docs'
description: Bu belgede AD DS bağlayıcı hesabının yeni ADSyncConfig PowerShell modülüyle nasıl yapılandırılacağı anlatılmaktadır
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eeb80c3a94e63a886e4a16c0b8fa445b2a8a34e4
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515826"
---
# <a name="azure-ad-connectconfigure-ad-ds-connector-account-permissions"></a>Azure AD Connect: AD DS bağlayıcı hesabı Izinlerini yapılandırma 

[Adsyncconfig. psm1](reference-connect-adsyncconfig.md) adlı PowerShell modülü, Azure AD Connect dağıtımınız için doğru Active Directory izinlerini yapılandırmanıza yardımcı olacak cmdlet 'lerin bir koleksiyonunu içeren Build 1.1.880.0 (2018 Ağustos ayında yayımlanmıştır) ile tanıtılmıştır. 

## <a name="overview"></a>Genel Bakış 
Aşağıdaki PowerShell cmdlet 'leri, Azure AD Connect etkinleştirmek üzere seçtiğiniz her bir özellik için AD DS bağlayıcı hesabının Active Directory izinlerini ayarlamak için kullanılabilir. Herhangi bir sorunu engellemek için, ormanınıza bağlanmak üzere özel bir etki alanı hesabı kullanarak Azure AD Connect yüklemek istediğinizde Active Directory izinleri önceden hazırlamanız gerekir. Bu ADSyncConfig modülü, Azure AD Connect dağıtıldıktan sonra izinleri yapılandırmak için de kullanılabilir.

![AD DS hesabına genel bakış](media/how-to-connect-configure-ad-ds-connector-account/configure1.png)

Azure AD Connect Express yüklemesinde, otomatik olarak oluşturulan bir hesap (MSOL_nnnnnnnnnn) tüm gerekli izinlerle Active Directory oluşturulur. bu nedenle, izinleri engellenmediğiniz takdirde bu ADSyncConfig modülünü kullanmanız gerekmez Kurumsal birimlerde veya Azure AD ile eşitlenmesini istediğiniz belirli Active Directory nesnelerinde devralım. 
 
### <a name="permissions-summary"></a>İzin özeti 
Aşağıdaki tabloda AD nesnelerinde gerekli izinlerin özeti verilmiştir: 

| Özellik | İzinler |
| --- | --- |
| ms-DS-ımlarımguıd özelliği |[Tasarım kavramları](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor)bölümünde belgelenen ms-DS-ımıbu GUID özniteliğinde okuma ve yazma izinleri | 
| Parola karması eşitleme |<li>Dizin Değişikliklerini Çoğalt</li>  <li>Dizin değişikliklerini çoğaltma |
| Exchange karma dağıtımı |Kullanıcılar, gruplar ve kişiler için [Exchange hibrit geri yazma](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) bölümünde belgelenen özniteliklere yönelik okuma ve yazma izinleri. |
| Exchange posta genel klasörü |Ortak klasörler için [Exchange posta ortak klasöründe](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) belgelenen özniteliklere yönelik okuma izinleri. | 
| Parola geri yazma |Kullanıcılar için [parola yönetimine](../authentication/howto-sspr-writeback.md) Başlarken bölümünde belgelenen özniteliklere yönelik okuma ve yazma izinleri. |
| Cihaz geri yazma |Cihaz nesnelerine ve [cihaz geri yazma](how-to-connect-device-writeback.md)bölümünde belgelenen kapsayıcılara yönelik okuma ve yazma izinleri. |
| Grup geri yazma |Eşitlenmiş **Office 365 grupları**için Grup nesnelerini okuyun, oluşturun, güncelleştirin ve silin.  Daha fazla bilgi için bkz. [Grup geri yazma](how-to-connect-preview.md#group-writeback).|

## <a name="using-the-adsyncconfig-powershell-module"></a>ADSyncConfig PowerShell modülünü kullanma 
ADSyncConfig modülü AD DS PowerShell modülüne ve araçlarına bağlı olduğundan, [AD DS için uzak sunucu yönetim araçları (RSAT)](https://docs.microsoft.com/windows-server/remote/remote-server-administration-tools) gerektirir. AD DS için RSAT 'yi yüklemek için, ' yönetici olarak Çalıştır ' ile bir Windows PowerShell penceresi açın ve yürütün: 

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

ör.:

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <ADAccountName> -ADConnectorAccountDomain <ADDomainName>
```

Veya

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <ADAccountDN>
```

@No__t_0, `<ADDomainName>` ve `<ADAccountDN>`, ortamınız için uygun değerlerle değiştirdiğinizden emin olun.

AdminSDHolder kapsayıcısında izinleri değiştirmek istemiyorsanız, `-SkipAdminSdHolders` anahtarını kullanın. 

Varsayılan olarak, tüm küme izinleri cmdlet 'leri ormandaki her etki alanının kökünde AD DS izinleri ayarlamaya çalışır, yani PowerShell oturumunu çalıştıran kullanıcının ormandaki her etki alanında etki alanı yöneticisi hakları olması gerekir.  Bu gereksinim nedeniyle, orman kökünden Kurumsal Yönetici kullanılması önerilir. Azure AD Connect dağıtımınızda birden çok AD DS Bağlayıcısı varsa, bir AD DS Bağlayıcısı bulunan her ormanda aynı cmdlet 'i çalıştırmak gerekecektir. 

Ayrıca, `-ADobjectDN` parametresini kullanarak belirli bir OU veya AD DS nesnesi üzerinde izinleri ayarlayabilir ve ardından izinleri ayarlamak istediğiniz hedef nesnenin DN 'si gelir. Bir hedef ADobjectDN kullanılırken, cmdlet yalnızca bu nesne üzerindeki izinleri ayarlar ve etki alanı kökünde veya AdminSDHolder kapsayıcısında değil. Bu parametre, izin kalıtımı devre dışı bırakılmış bazı OU 'Lar veya AD DS nesneler olduğunda yararlı olabilir (bkz. izin devralmayla AD DS nesneleri bulma devre dışı) 

Bu ortak parametrelerin özel durumları, AD DS bağlayıcı hesabının kendisi üzerindeki izinleri ayarlamak için kullanılan `Set-ADSyncRestrictedPermissions` cmdlet 'leridir ve parola karması eşitleme için gereken izinler yalnızca etki alanı kökünde ayarlandıklarından `Set-ADSyncPasswordHashSyncPermissions` cmdlet 'i kullanılır Bu nedenle, bu cmdlet `-ObjectDN` veya `-SkipAdminSdHolders` parametrelerini içermez.

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
Varsayılan olarak, bu cmdlet yalnızca devre dışı devralma özellikli OU 'Ları arar, ancak diğer AD DS nesne sınıflarını `-ObjectClass` parametresinde belirtebilir veya tüm nesne sınıfları için ' * ' öğesini aşağıdaki şekilde kullanabilirsiniz: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' -ObjectClass * 
```
 
### <a name="view-ad-ds-permissions-of-an-object"></a>Bir nesnenin AD DS izinlerini görüntüleme 
Aşağıdaki cmdlet 'i kullanarak, bir Active Directory nesnesi üzerinde şu anda ayarlanan izin listesini, distinguishedName 'dir sağlayarak görüntüleyebilirsiniz: 

``` powershell
Show-ADSyncADObjectPermissions -ADobjectDN '<DistinguishedName>' 
```

## <a name="configure-ad-ds-connector-account-permissions"></a>AD DS Bağlayıcısı hesabı Izinlerini yapılandırma 
 
### <a name="configure-basic-read-only-permissions"></a>Temel salt okuma Izinlerini yapılandırma 
Herhangi bir Azure AD Connect özelliği kullanmadığınız sırada AD DS bağlayıcı hesabı için temel salt okuma izinlerini ayarlamak için şunu çalıştırın: 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


Veya 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```


Bu cmdlet aşağıdaki izinleri ayarlar: 
 

|Tür |Adı |Access |Uygulama hedefi| 
|-----|-----|-----|-----|
|Allow |AD DS bağlayıcı hesabı |Tüm özellikleri oku |Alt cihaz nesneleri| 
|Allow |AD DS bağlayıcı hesabı|Tüm özellikleri oku |Alt InetOrgPerson nesneleri| 
|Allow |AD DS bağlayıcı hesabı |Tüm özellikleri oku |Alt bilgisayar nesneleri| 
|Allow |AD DS bağlayıcı hesabı |Tüm özellikleri oku |Descendant foreignSecurityPrincipal nesneleri| 
|Allow |AD DS bağlayıcı hesabı |Tüm özellikleri oku |Alt grup nesneleri| 
|Allow |AD DS bağlayıcı hesabı |Tüm özellikleri oku |Alt Kullanıcı nesneleri| 
|Allow |AD DS bağlayıcı hesabı |Tüm özellikleri oku |Alt öğe Iletişim nesneleri| 

 
### <a name="configure-ms-ds-consistency-guid-permissions"></a>MS-DS-tutarlılık-GUID Izinlerini yapılandırma 
Kaynak bağlantısı olarak ms-DS-tutarlılık-GUID özniteliğini kullanırken AD DS bağlayıcı hesabı izinlerini ayarlamak için (yani, Azure 'un kaynak bağlayıcısını benim için yönetme "seçeneği), şunu çalıştırın: 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```

Veya 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Bu cmdlet aşağıdaki izinleri ayarlar: 

|Tür |Adı |Access |Uygulama hedefi|
|-----|-----|-----|-----| 
|Allow|AD DS bağlayıcı hesabı|Okuma/yazma özelliği|Alt Kullanıcı nesneleri|

### <a name="permissions-for-password-hash-synchronization"></a>Parola karması eşitleme izinleri 
Parola karması eşitlemesini kullanırken AD DS bağlayıcı hesabı izinlerini ayarlamak için şunu çalıştırın: 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [<CommonParameters>] 
```


Veya 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [<CommonParameters>] 
```

Bu cmdlet aşağıdaki izinleri ayarlar: 

|Tür |Adı |Access |Uygulama hedefi|
|-----|-----|-----|-----| 
|Allow |AD DS bağlayıcı hesabı |Dizin değişikliklerini çoğaltma |Yalnızca bu nesne (etki alanı kökü)| 
|Allow |AD DS bağlayıcı hesabı |Dizin değişikliklerini çoğaltma |Yalnızca bu nesne (etki alanı kökü)| 
  
### <a name="permissions-for-password-writeback"></a>Parola geri yazma izinleri 
Parola geri yazma özelliğini kullanırken AD DS bağlayıcı hesabı izinlerini ayarlamak için şunu çalıştırın: 

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


Veya

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Bu cmdlet aşağıdaki izinleri ayarlar: 

|Tür |Adı |Access |Uygulama hedefi|
|-----|-----|-----|-----| 
|Allow |AD DS bağlayıcı hesabı |Parola Sıfırlama |Alt Kullanıcı nesneleri| 
|Allow |AD DS bağlayıcı hesabı |Write özelliği lockoutTime |Alt Kullanıcı nesneleri| 
|Allow |AD DS bağlayıcı hesabı |Write özelliği pwdLastSet |Alt Kullanıcı nesneleri| 

### <a name="permissions-for-group-writeback"></a>Grup geri yazma izinleri 
Grup geri yazma kullanılırken AD DS bağlayıcı hesabı izinlerini ayarlamak için şunu çalıştırın: 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```
Veya 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>]
```
 
Bu cmdlet aşağıdaki izinleri ayarlar: 

|Tür |Adı |Access |Uygulama hedefi|
|-----|-----|-----|-----| 
|Allow |AD DS bağlayıcı hesabı |Genel okuma/yazma |Nesne türü grubu ve alt nesnelerin tüm öznitelikleri| 
|Allow |AD DS bağlayıcı hesabı |Alt nesne Oluştur/Sil |Nesne türü grubu ve alt nesnelerin tüm öznitelikleri| 
|Allow |AD DS bağlayıcı hesabı |Ağaç nesnelerini Sil/Sil|Nesne türü grubu ve alt nesnelerin tüm öznitelikleri|

### <a name="permissions-for-exchange-hybrid-deployment"></a>Exchange karma dağıtımı izinleri 
Exchange karma dağıtımı kullanırken AD DS bağlayıcı hesabı izinlerini ayarlamak için şunu çalıştırın: 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


Veya 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Bu cmdlet aşağıdaki izinleri ayarlar:  
 

|Tür |Adı |Access |Uygulama hedefi|
|-----|-----|-----|-----| 
|Allow |AD DS bağlayıcı hesabı |Tüm özellikleri oku/yaz |Alt Kullanıcı nesneleri| 
|Allow |AD DS bağlayıcı hesabı |Tüm özellikleri oku/yaz |Alt InetOrgPerson nesneleri| 
|Allow |AD DS bağlayıcı hesabı |Tüm özellikleri oku/yaz |Alt grup nesneleri| 
|Allow |AD DS bağlayıcı hesabı |Tüm özellikleri oku/yaz |Alt öğe Iletişim nesneleri| 

### <a name="permissions-for-exchange-mail-public-folders-preview"></a>Exchange posta ortak klasörlerinin izinleri (Önizleme) 
Exchange posta ortak klasörleri 'ni kullanırken AD DS bağlayıcı hesabı izinlerini ayarlamak için şunu çalıştırın: 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


Veya 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Bu cmdlet aşağıdaki izinleri ayarlar: 

|Tür |Adı |Access |Uygulama hedefi|
|-----|-----|-----|-----| 
|Allow |AD DS bağlayıcı hesabı |Tüm özellikleri oku |Descendant PublicFolder nesneleri| 

### <a name="restrict-permissions-on-the-ad-ds-connector-account"></a>AD DS bağlayıcı hesabında Izinleri kısıtla 
Bu PowerShell betiği, bir parametre olarak sağlanmış olan AD Bağlayıcısı hesabının izinlerini güçlendirilecektir. Sıkı bir şekilde izinleri aşağıdaki adımları içerir: 

- Belirtilen nesnede devralmayı devre dışı bırak 
- Kendisine özgü olan Ace 'Ler hariç, kendı kendine özel izinleri korumak istediğimiz için, belirli bir nesnedeki tüm Ace 'Leri kaldırın. 
 
  -ADConnectorAccountDN parametresi, izinlerinin daha sıkı bir şekilde getirilmesi gereken AD hesabıdır. Bu genellikle AD DS Bağlayıcısı 'nda yapılandırılmış olan MSOL_nnnnnnnnnnnn etki alanı hesabıdır (bkz. AD DS bağlayıcı hesabınızı belirleme). -Credential parametresi, hedef AD nesnesinde Active Directory izinleri kısıtlamak için gerekli ayrıcalıklara sahip yönetici hesabını belirtmek için gereklidir. Bu genellikle kuruluş veya etki alanı yöneticisidir.  

``` powershell
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential> [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>] 
```
 
Örneğin: 

``` powershell
$credential = Get-Credential 
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN'CN=ADConnectorAccount,CN=Users,DC=Contoso,DC=com' -Credential $credential  
```

Bu cmdlet aşağıdaki izinleri ayarlar: 

|Tür |Adı |Access |Uygulama hedefi|
|-----|-----|-----|-----| 
|Allow |SISTEMIN |Tam denetim |Bu nesne 
|Allow |Kuruluş yöneticileri |Tam denetim |Bu nesne 
|Allow |Etki alanı yöneticileri |Tam denetim |Bu nesne 
|Allow |Yöneticiler |Tam denetim |Bu nesne 
|Allow |Kurumsal etki alanı denetleyicileri |Liste Içerikleri |Bu nesne 
|Allow |Kurumsal etki alanı denetleyicileri |Tüm özellikleri oku |Bu nesne 
|Allow |Kurumsal etki alanı denetleyicileri |Okuma Izinleri |Bu nesne 
|Allow |Kimliği doğrulanmış kullanıcılar |Liste Içerikleri |Bu nesne 
|Allow |Kimliği doğrulanmış kullanıcılar |Tüm özellikleri oku |Bu nesne 
|Allow |Kimliği doğrulanmış kullanıcılar |Okuma Izinleri |Bu nesne 

## <a name="next-steps"></a>Sonraki Adımlar
- [Azure AD Connect: Hesaplar ve izinler](reference-connect-accounts-permissions.md)
- [Hızlı yükleme](how-to-connect-install-express.md)
- [Özel yükleme](how-to-connect-install-custom.md)
- [ADSyncConfig başvurusu](reference-connect-adsyncconfig.md)

