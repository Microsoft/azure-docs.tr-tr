---
title: 'Azure AD Connect: ADSyncTools PowerShell Başvurusu | Microsoft Docs'
description: Bu belge, ADSyncTools. psm1 PowerShell modülü için başvuru bilgileri sağlar.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 11/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1652c52dcc6870e396d74a2f38fe63c304d37df5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97672277"
---
# <a name="azure-ad-connect--adsynctools-powershell-reference"></a>Azure AD Connect: ADSyncTools PowerShell Başvurusu
Aşağıdaki belgeler Azure AD Connect eklenen ADSyncTools. psm1 PowerShell modülü için başvuru bilgileri sağlar.

## <a name="install-the-adsynctools-powershell-module"></a>ADSyncTools PowerShell modülünü yükler
ADSyncTools PowerShell modülünü yüklemek için aşağıdakileri yapın:

1.  Windows PowerShell 'i yönetici ayrıcalıklarındaki açık
2.  Aşağıdakileri yazın veya kopyalayıp yapıştırın: 
    ``` powershell
        [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
        Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
        Import-module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\Tools\AdSyncTools"
    ```
3.  ENTER tuşuna basın.
4.  Modülün yüklendiğini doğrulamak için aşağıdakileri girin veya kopyalayıp yapıştırın "
    ```powershell
    Get-module AdSyncTools
    ```
5.  Artık modülle ilgili bilgileri görmeniz gerekir.


## <a name="clear-adsynctoolsconsistencyguid"></a>Clear-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>ÖZET
AD kullanıcısının mS-DS-ımced GUID 'sini temizle

### <a name="syntax"></a>SÖZ DİZİMİ

```
Clear-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>AÇIKLAMA
Hedef AD kullanıcısı için mS-DS-ımsısıguıd içindeki değeri temizleyin

### <a name="examples"></a>ÖRNEKLER

#### <a name="example-1"></a>ÖRNEK 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>ÖRNEK 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRELER

#### <a name="-user"></a>-User
AD 'de hedef Kullanıcı ayarı

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Bu cmdlet şu genel parametreleri destekler: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction ve -WarningVariable.
Daha fazla bilgi için bkz. about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="confirm-adsynctoolsadmoduleloaded"></a>Confirm-ADSyncToolsADModuleLoaded

### <a name="synopsis"></a>ÖZET
{{Özeti}}

### <a name="syntax"></a>SÖZ DİZİMİ

```
Confirm-ADSyncToolsADModuleLoaded
```

### <a name="description"></a>AÇIKLAMA
{{Açıklamayı doldur}}

### <a name="examples"></a>ÖRNEKLER

#### <a name="example-1"></a>Örnek 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Buraya örnek açıklama ekleyin}}

## <a name="connect-adsyncdatabase"></a>Connect-AdSyncDatabase

### <a name="synopsis"></a>ÖZET
{{Özeti}}

### <a name="syntax"></a>SÖZ DİZİMİ

```
Connect-AdSyncDatabase [-Server] <String> [[-Instance] <String>] [[-Database] <String>] [[-UserName] <String>]
 [[-Password] <String>] [<CommonParameters>]
```

### <a name="description"></a>AÇIKLAMA
{{Açıklamayı doldur}}

### <a name="examples"></a>ÖRNEKLER

#### <a name="example-1"></a>Örnek 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Buraya örnek açıklama ekleyin}}

### <a name="parameters"></a>PARAMETRELER

#### <a name="-database"></a>-Veritabanı
{{Fill veritabanı açıklaması}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-instance"></a>-Örnek
{{Fill örnek açıklaması}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-password"></a>-Password
{{Fill Password açıklaması}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-server"></a>-Server
{{Fill Server açıklaması}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-username"></a>-UserName
{{Fill Kullanıcı adı açıklaması}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Bu cmdlet şu genel parametreleri destekler: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction ve -WarningVariable.
Daha fazla bilgi için bkz. about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="export-adsynctoolsconsistencyguidmigration"></a>Export-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>ÖZET
' Me GUID raporunu dışarı aktar

### <a name="syntax"></a>SÖZ DİZİMİ

```
Export-ADSyncToolsConsistencyGuidMigration [-AlternativeLoginId] [-UserPrincipalName] <String>
 [-ImmutableIdGUID] <String> [-Output] <String> [<CommonParameters>]
```

### <a name="description"></a>AÇIKLAMA
Import-ADSyncToolsImmutableIdMigration bir içeri aktarma CSV dosyasını temel alan bir '% bir GUID raporu oluşturur

### <a name="examples"></a>ÖRNEKLER

#### <a name="example-1"></a>ÖRNEK 1
```
Import-Csv .\AllSyncUsers.csv | Export-ADSyncToolsConsistencyGuidMigration -Output ".\AllSyncUsers-Report"
```

#### <a name="example-2"></a>ÖRNEK 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRELER

#### <a name="-alternativeloginid"></a>-Değişim Tiveloginıd
Alternatif oturum açma KIMLIĞI (posta) kullan

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-userprincipalname"></a>-UserPrincipalName
UserPrincipalName

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-immutableidguid"></a>-Immutableıdguid
Immutableıdguid

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-output"></a>-Çıkış
CSV ve günlük dosyaları için çıkış dosya adı

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Bu cmdlet şu genel parametreleri destekler: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction ve -WarningVariable.
Daha fazla bilgi için bkz. about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="get-adsyncsqlbrowserinstances"></a>Get-ADSyncSQLBrowserInstances

### <a name="synopsis"></a>ÖZET
{{Özeti}}

### <a name="syntax"></a>SÖZ DİZİMİ

```
Get-ADSyncSQLBrowserInstances [[-hostName] <String>]
```

### <a name="description"></a>AÇIKLAMA
{{Açıklamayı doldur}}

### <a name="examples"></a>ÖRNEKLER

#### <a name="example-1"></a>Örnek 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Buraya örnek açıklama ekleyin}}

### <a name="parameters"></a>PARAMETRELER

#### <a name="-hostname"></a>-hostName
{{Fill hostName açıklaması}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="get-adsynctoolsaduser"></a>Get-ADSyncToolsADuser

### <a name="synopsis"></a>ÖZET
Kullanıcıyı AD 'den al

### <a name="syntax"></a>SÖZ DİZİMİ

```
Get-ADSyncToolsADuser [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>AÇIKLAMA
YAPıLACAK bir AD nesnesi döndürür: çoklu orman desteği

### <a name="examples"></a>ÖRNEKLER

#### <a name="example-1"></a>ÖRNEK 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>ÖRNEK 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRELER

#### <a name="-user"></a>-User
AD 'de hedef Kullanıcı.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Bu cmdlet şu genel parametreleri destekler: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction ve -WarningVariable.
Daha fazla bilgi için bkz. about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="get-adsynctoolsconsistencyguid"></a>Get-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>ÖZET
AD kullanıcısının mS-DS-ımlarımguıd 'sini alın

### <a name="syntax"></a>SÖZ DİZİMİ

```
Get-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>AÇIKLAMA
GUID biçiminde hedef AD kullanıcısının mS-DS-ımsısıguıd özniteliğinde bulunan değeri döndürür

### <a name="examples"></a>ÖRNEKLER

#### <a name="example-1"></a>ÖRNEK 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>ÖRNEK 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRELER

#### <a name="-user"></a>-User
AD 'de hedef Kullanıcı ayarı

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Bu cmdlet şu genel parametreleri destekler: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction ve -WarningVariable.
Daha fazla bilgi için bkz. about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="get-adsynctoolsobjectguid"></a>Get-ADSyncToolsObjectGuid

### <a name="synopsis"></a>ÖZET
AD kullanıcısının objectGUID değerini Al

### <a name="syntax"></a>SÖZ DİZİMİ

```
Get-ADSyncToolsObjectGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>AÇIKLAMA
GUID biçiminde hedef AD kullanıcısının Objectguıd özniteliğinde değeri döndürür

### <a name="examples"></a>ÖRNEKLER

#### <a name="example-1"></a>ÖRNEK 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>ÖRNEK 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRELER

#### <a name="-user"></a>-User
AD 'de hedef Kullanıcı ayarı

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Bu cmdlet şu genel parametreleri destekler: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction ve -WarningVariable.
Daha fazla bilgi için bkz. about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="get-adsynctoolsrunhistory"></a>Get-ADSyncToolsRunHistory

### <a name="synopsis"></a>ÖZET
AAD Connect çalıştırma geçmişini al

### <a name="syntax"></a>SÖZ DİZİMİ

```
Get-ADSyncToolsRunHistory [[-Days] <Int32>] [<CommonParameters>]
```

### <a name="description"></a>AÇIKLAMA
XML biçiminde AAD Connect çalıştırma geçmişini döndüren işlev

### <a name="examples"></a>ÖRNEKLER

#### <a name="example-1"></a>ÖRNEK 1
```
Get-ADSyncToolsRunHistory
```

#### <a name="example-2"></a>ÖRNEK 2
```
Get-ADSyncToolsRunHistory -Days 1
```

### <a name="parameters"></a>PARAMETRELER

#### <a name="-days"></a>-Gün
{{Fill Days açıklaması}}

```yaml
Type: Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: 3
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Bu cmdlet şu genel parametreleri destekler: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction ve -WarningVariable.
Daha fazla bilgi için bkz. about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="get-adsynctoolssourceanchorchanged"></a>Get-ADSyncToolsSourceAnchorChanged

### <a name="synopsis"></a>ÖZET
Kaynak bağlantısı değişmiş hataları olan kullanıcıları al

### <a name="syntax"></a>SÖZ DİZİMİ

```
Get-ADSyncToolsSourceAnchorChanged [-sourcePath] <Object> [-outputPath] <Object> [<CommonParameters>]
```

### <a name="description"></a>AÇIKLAMA
İşlev AAD Connect çalıştırma geçmişini sorgular ve hata bildiren tüm kullanıcıları dışarı aktarır: "Sourcetutturucu özniteliği değişti."

### <a name="examples"></a>ÖRNEKLER

#### <a name="example-1"></a>ÖRNEK 1
```
#Required Parameters
```

$sourcePath = Read-Host-Prompt "dosya adı" # "olan günlük dosyası yolunu girin" \<Source_Path\> $outputPath = Read-Host-Prompt "dosya adı" # "olan çıkış dosya yolunu girin \<Out_Path\>

 Get-ADSyncToolsUsersSourceAnchorChanged-sourcePath $sourcePath-outputPath $outputPath

#### <a name="example-2"></a>ÖRNEK 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRELER

#### <a name="-sourcepath"></a>-sourcePath
{{Fill sourcePath açıklaması}}

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-outputpath"></a>-outputPath
{{Fill outputPath açıklaması}}

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Bu cmdlet şu genel parametreleri destekler: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction ve -WarningVariable.
Daha fazla bilgi için bkz. about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="import-adsynctoolsimmutableidmigration"></a>Import-ADSyncToolsImmutableIdMigration

### <a name="synopsis"></a>ÖZET
AAD 'den ImmutableID içeri aktar

### <a name="syntax"></a>SÖZ DİZİMİ

```
Import-ADSyncToolsImmutableIdMigration [-Output] <String> [-IncludeSyncUsersFromRecycleBin]
 [<CommonParameters>]
```

### <a name="description"></a>AÇIKLAMA
GUID biçimi gereksinimlerinde ImmutableID değerini içeren tüm Azure AD eşitlenmiş kullanıcıları içeren bir dosya oluşturur: MSOnline PowerShell modülü

### <a name="examples"></a>ÖRNEKLER

#### <a name="example-1"></a>ÖRNEK 1
```
Import-ADSyncToolsImmutableIdMigration -OutputFile '.\AllSyncUsers.csv'
```

#### <a name="example-2"></a>ÖRNEK 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRELER

#### <a name="-output"></a>-Çıkış
Çıkış CSV dosyası

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-includesyncusersfromrecyclebin"></a>-Includesyncusersfromrecyıclebin
Azure AD geri dönüşüm kutusu 'ndan eşitlenmiş kullanıcıları al

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Bu cmdlet şu genel parametreleri destekler: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction ve -WarningVariable.
Daha fazla bilgi için bkz. about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .


## <a name="invoke-adsyncdatabasequery"></a>Invoke-AdSyncDatabaseQuery

### <a name="synopsis"></a>ÖZET
{{Özeti}}

### <a name="syntax"></a>SÖZ DİZİMİ

```
Invoke-AdSyncDatabaseQuery [-SqlConnection] <SqlConnection> [[-Query] <String>] [<CommonParameters>]
```

### <a name="description"></a>AÇIKLAMA
{{Açıklamayı doldur}}

### <a name="examples"></a>ÖRNEKLER

#### <a name="example-1"></a>Örnek 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Buraya örnek açıklama ekleyin}}

### <a name="parameters"></a>PARAMETRELER

#### <a name="-query"></a>-Sorgu
{{Fill sorgu açıklaması}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-sqlconnection"></a>-SqlConnection
{{Fill SqlConnection açıklaması}}

```yaml
Type: SqlConnection
Parameter Sets: (All)
Aliases:

Required: True
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Bu cmdlet şu genel parametreleri destekler: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction ve -WarningVariable.
Daha fazla bilgi için bkz. about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="remove-adsynctoolsexpiredcertificates"></a>Remove-ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>ÖZET
UserCertificate özniteliğinden süre dolma sertifikaları kaldırma betiği

### <a name="syntax"></a>SÖZ DİZİMİ

```
Remove-ADSyncToolsExpiredCertificates [-TargetOU] <String> [[-BackupOnly] <Boolean>] [-ObjectClass] <String>
 [<CommonParameters>]
```

### <a name="description"></a>AÇIKLAMA
Bu betik, nesne sınıfı (Kullanıcı/bilgisayar) tarafından filtrelenen Active Directory etki alanındaki bir hedef kuruluş biriminden tüm nesneleri alır ve UserCertificate özniteliğinde bulunan tüm süre dolma sertifikaları siler.
Varsayılan olarak (yalnızca Backupmode), zaman aşımına uğradı sertifikaları bir dosyaya yedekler ve AD 'de herhangi bir değişiklik yapamazlar.
Yalnızca-Backup$false kullanırsanız, bu nesneler için UserCertificate özniteliğinde bulunan tüm süre sonu sertifikalar, dosyaya kopyalandıktan sonra AD 'den kaldırılır.
Her sertifika, ayrılmış bir dosya adına yedeklenecek: ObjectClass_ObjectGUID_CertThumprint. cer komut dosyası aynı zamanda, alınan gerçek eylem (atlandı/verildi/silindi) dahil olmak üzere, geçerli veya zaman aşımına geçen sertifikalara sahip tüm kullanıcıları gösteren CSV biçiminde bir günlük dosyası oluşturur.

### <a name="examples"></a>ÖRNEKLER

#### <a name="example-1"></a>ÖRNEK 1
```
Check all users in target OU - Expired Certificates will be copied to separated files and no certificates will be removed
```

Remove-ADSyncToolsExpiredCertificates-TargetOU "OU = kullanıcılar, OU = Corp, DC = contoso, DC = com"-ObjectClass User

#### <a name="example-2"></a>ÖRNEK 2
```
Delete Expired Certs from all Computer objects in target OU - Expired Certificates will be copied to files and removed from AD
```

Remove-ADSyncToolsExpiredCertificates-TargetOU "OU = Computers, OU = Corp, DC = contoso, DC = com"-ObjectClass bilgisayar-BackupOnly $false

### <a name="parameters"></a>PARAMETRELER

#### <a name="-targetou"></a>-TargetOU
AD nesnelerini aramak için hedef OU

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-backuponly"></a>-Yalnızca Backup
BackupOnly, AD 'deki herhangi bir sertifikayı silmez

```yaml
Type: Boolean
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: True
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-objectclass"></a>-ObjectClass
Nesne sınıfı filtresi

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Bu cmdlet şu genel parametreleri destekler: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction ve -WarningVariable.
Daha fazla bilgi için bkz. about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="repair-adsynctoolsautoupgradestate"></a>Repair-ADSyncToolsAutoUpgradeState

### <a name="synopsis"></a>ÖZET
Kısa açıklama

### <a name="syntax"></a>SÖZ DİZİMİ

```
Repair-ADSyncToolsAutoUpgradeState
```

### <a name="description"></a>AÇIKLAMA
Uzun açıklama

### <a name="examples"></a>ÖRNEKLER

#### <a name="example-1"></a>ÖRNEK 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>ÖRNEK 2
```
Another example of how to use this cmdlet
```

## <a name="resolve-adsynchostaddress"></a>Resolve-ADSyncHostAddress

### <a name="synopsis"></a>ÖZET
{{Özeti}}

### <a name="syntax"></a>SÖZ DİZİMİ

```
Resolve-ADSyncHostAddress [[-hostName] <String>]
```

### <a name="description"></a>AÇIKLAMA
{{Açıklamayı doldur}}

### <a name="examples"></a>ÖRNEKLER

#### <a name="example-1"></a>Örnek 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Buraya örnek açıklama ekleyin}}

### <a name="parameters"></a>PARAMETRELER

#### <a name="-hostname"></a>-hostName
{{Fill hostName açıklaması}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="restore-adsynctoolsexpiredcertificates"></a>Restore-ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>ÖZET
(yapılacak) Bir sertifika dosyasından AD UserCertificate özniteliğini geri yükler

### <a name="syntax"></a>SÖZ DİZİMİ

```
Restore-ADSyncToolsExpiredCertificates
```

### <a name="description"></a>AÇIKLAMA
Uzun açıklama

### <a name="examples"></a>ÖRNEKLER

#### <a name="example-1"></a>ÖRNEK 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>ÖRNEK 2
```
Another example of how to use this cmdlet
```

## <a name="set-adsynctoolsconsistencyguid"></a>Set-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>ÖZET
AD kullanıcısının mS-DS-ımced GUID 'sini ayarlama

### <a name="syntax"></a>SÖZ DİZİMİ

```
Set-ADSyncToolsConsistencyGuid [-User] <Object> [-Value] <Object> [<CommonParameters>]
```

### <a name="description"></a>AÇIKLAMA
Hedef AD kullanıcısı için mS-DS-ımsısıguıd özniteliğinde bir değer ayarlayın

### <a name="examples"></a>ÖRNEKLER

#### <a name="example-1"></a>ÖRNEK 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>ÖRNEK 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRELER

#### <a name="-user"></a>-User
AD 'de hedef Kullanıcı.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-value"></a>-Değer
ImmutableID (byte dizisi, GUID, GUID String veya Base64 dize)

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Bu cmdlet şu genel parametreleri destekler: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction ve -WarningVariable.
Daha fazla bilgi için bkz. about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="test-adsyncnetworkport"></a>Test-ADSyncNetworkPort

### <a name="synopsis"></a>ÖZET
{{Özeti}}

### <a name="syntax"></a>SÖZ DİZİMİ

```
Test-ADSyncNetworkPort [[-hostName] <String>] [[-port] <String>]
```

### <a name="description"></a>AÇIKLAMA
{{Açıklamayı doldur}}

### <a name="examples"></a>ÖRNEKLER

#### <a name="example-1"></a>Örnek 1
```powershell
PS C:\> {{ Add example code here }}
```

{{Buraya örnek açıklama ekleyin}}

### <a name="parameters"></a>PARAMETRELER

#### <a name="-hostname"></a>-hostName
{{Fill hostName açıklaması}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-port"></a>-bağlantı noktası
{{Fill Port açıklaması}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="trace-adsynctoolsadimport"></a>Trace-ADSyncToolsADImport

### <a name="synopsis"></a>ÖZET
Ve AD Içeri aktarma adımından bir izleme dosyası oluşturur

### <a name="syntax"></a>SÖZ DİZİMİ

```
Trace-ADSyncToolsADImport [[-ADConnectorXML] <String>] [[-dc] <String>] [[-rootDN] <String>]
 [[-filter] <String>] [-SkipCredentials] [[-ADwatermark] <String>] [<CommonParameters>]
```

### <a name="description"></a>AÇIKLAMA
Belirli bir AD filigranı denetim noktasından (Bölüm tanımlama bilgisi) bir AAD Connect AD Içeri aktarma çalıştırmasının tüm LDAP sorgularını izler. Geçerli klasörde bir '. \ ADimportTrace_yyyyMMddHHmmss. log ' izleme dosyası oluşturur.

### <a name="examples"></a>ÖRNEKLER

#### <a name="example-1"></a>ÖRNEK 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>ÖRNEK 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRELER

#### <a name="-adconnectorxml"></a>-ADConnectorXML
{{Fill ADConnectorXML açıklaması}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dc"></a>-DC
AD Bağlayıcısı dışarı aktarma XML dosyası

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: DC1.domain.contoso.com
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-rootdn"></a>-rootDN
Hedef etki alanı denetleyicisi

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: DC=Domain,DC=Contoso,DC=com
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-filter"></a>-Filtre
Orman kök DN 'si

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: (&(objectClass=*))
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipcredentials"></a>-SkipCredentials
İzlenecek AD nesnelerinin türleri \> * = tüm nesne türleri

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adwatermark"></a>-Adfiligran
Zaten etki alanı yöneticisi olarak çalışıyorsa AD kimlik bilgilerini sağlamanız gerekmez.
"TVNEUwMAAAAXyK9ir1zSAQAAAAAAAAAA (...)" gibi, XML dosyası yerine filigranın el ile girişi $ADwatermark.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 5
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Bu cmdlet şu genel parametreleri destekler: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction ve -WarningVariable.
Daha fazla bilgi için bkz. about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="trace-adsynctoolsldapquery"></a>Trace-ADSyncToolsLdapQuery

### <a name="synopsis"></a>ÖZET
Kısa açıklama

### <a name="syntax"></a>SÖZ DİZİMİ

```
Trace-ADSyncToolsLdapQuery [-Context] <String> [-Server] <String> [-Port] <Int32> [-Filter] <String>
 [<CommonParameters>]
```

### <a name="description"></a>AÇIKLAMA
Uzun açıklama

### <a name="examples"></a>ÖRNEKLER

#### <a name="example-1"></a>ÖRNEK 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>ÖRNEK 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>PARAMETRELER

#### <a name="-context"></a>-Bağlam
Param1 yardım açıklaması

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-server"></a>-Server
Param2 yardım açıklaması

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: Localhost
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-port"></a>-Bağlantı noktası
Param2 yardım açıklaması

```yaml
Type: Int32
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: 389
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-filter"></a>-Filtre
Param2 yardım açıklaması

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 4
Default value: (objectClass=*)
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Bu cmdlet şu genel parametreleri destekler: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction ve -WarningVariable.
Daha fazla bilgi için bkz. about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .

## <a name="update-adsynctoolsconsistencyguidmigration"></a>Update-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>ÖZET
Yeni bir IBU GUID (ImmutableID) ile kullanıcıları güncelleştirir

### <a name="syntax"></a>SÖZ DİZİMİ

```
Update-ADSyncToolsConsistencyGuidMigration [[-DistinguishedName] <String>] [-ImmutableIdGUID] <String>
 [-Action] <String> [-Output] <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>AÇIKLAMA
Yeni bir Ise GUID (ImmutableID) değeri olan kullanıcıları güncelleyen GUID raporundan güncelleştirir bu işlev, whatIf anahtarını destekler: ıDemiliter

### <a name="examples"></a>ÖRNEKLER

#### <a name="example-1"></a>ÖRNEK 1
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsConsistencyGuidMigration -Output .\AllSyncUsersTEST-Result2 -WhatIf
```

#### <a name="example-2"></a>ÖRNEK 2
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsConsistencyGuidMigration -Output .\AllSyncUsersTEST-Result2
```

### <a name="parameters"></a>PARAMETRELER

#### <a name="-distinguishedname"></a>-DistinguishedName 'dir
DistinguishedName 'dir

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: False
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-immutableidguid"></a>-Immutableıdguid
Immutableıdguid

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-action"></a>-Eylem
Eylem

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-output"></a>-Çıkış
GÜNLÜK dosyaları için çıkış dosya adı

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
Cmdlet çalıştırılıyorsa ne olacağını gösterir.
Cmdlet çalıştırılmaz.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Cmdlet'i çalıştırmadan önce sizden onay ister.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Bu cmdlet şu genel parametreleri destekler: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction ve -WarningVariable.
Daha fazla bilgi için bkz. about_CommonParameters ( https://go.microsoft.com/fwlink/?LinkID=113216) .
