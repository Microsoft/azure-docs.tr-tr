---
title: "Verileri yönetmek için PowerShell 'i kullanma: Azure Data Lake Storage 2."
description: Hiyerarşik ad alanı etkinleştirilmiş depolama hesaplarındaki dizinleri ve dosyaları yönetmek için PowerShell cmdlet 'lerini kullanın.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 552d53ff0257105ff61397e281504c5270512319
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103573872"
---
# <a name="use-powershell-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>PowerShell kullanarak Azure Data Lake Storage 2. dizinleri ve dosyaları yönetme

Bu makalede, PowerShell kullanarak hiyerarşik bir ad alanı olan depolama hesaplarında dizin ve dosya oluşturma ve bunları yönetme işlemlerinin nasıl yapılacağı gösterilir.

Dizinlerin ve dosyaların erişim denetim listelerini (ACL) alma, ayarlama ve güncelleştirme hakkında bilgi edinmek için bkz. [Azure Data Lake Storage 2. ACL 'leri yönetmek Için PowerShell kullanma](data-lake-storage-acl-powershell.md).

[Başvuru](/powershell/module/Az.Storage/)  |  [Gen1 to Gen2 Mapping](#gen1-gen2-map)  |  [Geri bildirimde](https://github.com/Azure/azure-powershell/issues) bulunun

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği. Bkz. [Azure ücretsiz deneme sürümü edinme](https://azure.microsoft.com/pricing/free-trial/).

- Hiyerarşik ad alanı etkin olan bir depolama hesabı. Bir tane oluşturmak için [Bu](create-data-lake-storage-account.md) yönergeleri izleyin.

- .NET Framework, 4.7.2 veya üzeri bir sürümü yüklendi. Bkz. [indirme .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).

- PowerShell sürümü `5.1` veya üzeri.

## <a name="install-the-powershell-module"></a>PowerShell modülünü yükleme

1. `5.1`Aşağıdaki komutu kullanarak yüklü PowerShell sürümünün veya daha yüksek olduğunu doğrulayın.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```

   PowerShell sürümünüzü yükseltmek için bkz. [var olan Windows PowerShell 'ı yükseltme](/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell)

2. **Az. Storage** modülünü yükler.

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   PowerShell modüllerinin nasıl yükleneceği hakkında daha fazla bilgi için bkz [. Azure PowerShell modülünü Install](/powershell/azure/install-az-ps)

## <a name="connect-to-the-account"></a>Hesaba Bağlan

Komutlarınızın depolama hesabı için nasıl yetkilendirme elde etmek istediğinizi seçin. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-azure-ad"></a>Seçenek 1: Azure Active Directory kullanarak yetkilendirme alma (Azure AD)

Bu yaklaşımda sistem, Kullanıcı hesabınızın uygun Azure rol tabanlı erişim denetimi (Azure RBAC) atamalarına ve ACL izinlerine sahip olmasını sağlar.

1. Bir Windows PowerShell komut penceresi açın ve komutuyla Azure aboneliğinizde oturum açın `Connect-AzAccount` ve ekrandaki yönergeleri izleyin.

   ```powershell
   Connect-AzAccount
   ```

2. Kimliğiniz birden fazla abonelikle ilişkiliyse, etkin aboneliğinizi ' de dizin oluşturup yönetmek istediğiniz depolama hesabının aboneliğine ayarlayın. Bu örnekte, `<subscription-id>` yer tutucu değerini ABONELIĞINIZIN kimliğiyle değiştirin.

   ```powershell
   Select-AzSubscription -SubscriptionId <subscription-id>
   ``` 

3. Depolama hesabı bağlamını alın.

   ```powershell
   $ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
   ```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Seçenek 2: depolama hesabı anahtarını kullanarak yetkilendirme alma

Bu yaklaşımda sistem Azure RBAC veya ACL izinlerini denetlemez. Hesap anahtarı kullanarak depolama hesabı bağlamını alın.

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -StorageAccountKey '<storage-account-key>'
```

## <a name="create-a-container"></a>Kapsayıcı oluşturma

Bir kapsayıcı dosyalarınız için bir dosya sistemi görevi görür. Cmdlet 'ini kullanarak bir tane oluşturabilirsiniz `New-AzStorageContainer` . 

Bu örnek adlı bir kapsayıcı oluşturur `my-file-system` .

```powershell
$filesystemName = "my-file-system"
New-AzStorageContainer -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>Dizin oluşturma

Cmdlet 'ini kullanarak bir dizin başvurusu oluşturun `New-AzDataLakeGen2Item` . 

Bu örnek, bir kapsayıcıya adlı bir dizin ekler `my-directory` .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory
```

Bu örnek aynı dizini ekler, ancak izinler, umask, özellik değerleri ve meta veri değerlerini de ayarlar. 

```powershell
$dir = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory -Permission rwxrwxrwx -Umask ---rwx---  -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
```

## <a name="show-directory-properties"></a>Dizin özelliklerini göster

Bu örnek, cmdlet 'ini kullanarak bir dizin alır `Get-AzDataLakeGen2Item` ve sonra özellik değerlerini konsola yazdırır.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir =  Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
$dir.Permissions
$dir.Group
$dir.Owner
$dir.Properties
$dir.Properties.Metadata
```

> [!NOTE]
> Kapsayıcının kök dizinini almak için `-Path` parametresini atlayın.

## <a name="rename-or-move-a-directory"></a>Bir dizini yeniden adlandırma veya taşıma

Cmdlet 'ini kullanarak bir dizini yeniden adlandırın veya taşıyın `Move-AzDataLakeGen2Item` .

Bu örnek, adından adı olan bir dizini yeniden adlandırır `my-directory` `my-new-directory` .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

> [!NOTE]
> `-Force`Komut istemleri olmadan üzerine yazmak istiyorsanız parametresini kullanın.

Bu örnek adlı bir dizini `my-directory` adlı bir alt dizine taşır `my-directory-2` `my-subdirectory` . 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

## <a name="delete-a-directory"></a>Bir dizini silme

Cmdlet 'ini kullanarak bir dizini silin `Remove-AzDataLakeGen2Item` .

Bu örnek adlı bir dizini siler `my-directory` .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

`-Force`Dosyayı sormadan kaldırmak için parametresini kullanabilirsiniz.

## <a name="download-from-a-directory"></a>Bir dizinden indir

Cmdlet 'ini kullanarak bir dizinden dosya indirin `Get-AzDataLakeGen2ItemContent` .

Bu örnek, `upload.txt` adlı bir dizinden adlı bir dosyayı indirir `my-directory` .

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>Dizin içeriğini listeleme

Cmdlet 'ini kullanarak bir dizinin içeriğini listeleyin `Get-AzDataLakeGen2ChildItem` . `-OutputUserPrincipalName`Kullanıcının adını (nesne kimliği yerine) almak için isteğe bağlı parametresini kullanabilirsiniz.

Bu örnek, adlı bir dizinin içeriğini listeler `my-directory` .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -OutputUserPrincipalName
```

Aşağıdaki örnek, `ACL` `Permissions` `Group` dizindeki her öğenin,, ve `Owner` özelliklerini listeler. `-FetchProperty`Özelliği için değerleri almak için parametresi gereklidir `ACL` . 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchProperty
$properties.ACL
$properties.Permissions
$properties.Group
$properties.Owner
```

> [!NOTE]
> Kapsayıcının kök dizininin içeriğini listelemek için `-Path` parametresini atlayın.

## <a name="upload-a-file-to-a-directory"></a>Dizine dosya yükleme

Cmdlet 'ini kullanarak bir dosyayı dizine yükleyin `New-AzDataLakeGen2Item` .

Bu örnek adlı bir dizine adlı bir dosyayı yükler `upload.txt` `my-directory` . 

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force 
```

Bu örnek, aynı dosyayı karşıya yükler, ancak ardından hedef dosyanın izinlerini, umaskesini, özellik değerlerini ve meta veri değerlerini ayarlar. Bu örnek ayrıca konsola bu değerleri de yazdırır.

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.Properties
$file1.Properties.Metadata

```

> [!NOTE]
> Kapsayıcının kök dizinine bir dosya yüklemek için `-Path` parametresini atlayın.

## <a name="show-file-properties"></a>Dosya özelliklerini göster

Bu örnek, cmdlet 'ini kullanarak bir dosya alır `Get-AzDataLakeGen2Item` ve sonra özellik değerlerini konsola yazdırır.

```powershell
$filepath =  "my-directory/upload.txt"
$filesystemName = "my-file-system"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filepath
$file
$file.ACL
$file.Permissions
$file.Group
$file.Owner
$file.Properties
$file.Properties.Metadata
```

## <a name="delete-a-file"></a>Dosyayı silme

Cmdlet 'ini kullanarak bir dosyayı silin `Remove-AzDataLakeGen2Item` .

Bu örnek adlı bir dosyayı siler `upload.txt` . 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

`-Force`Dosyayı sormadan kaldırmak için parametresini kullanabilirsiniz.

<a id="gen1-gen2-map"></a>

## <a name="gen1-to-gen2-mapping"></a>Gen1 to Gen2 Mapping

Aşağıdaki tabloda, Data Lake Storage 2. için cmdlet 'lerle Data Lake Storage 1. eşleme için kullanılan cmdlet 'ler gösterilmektedir.

|Data Lake Storage 1. cmdlet 'i| Data Lake Storage 2. cmdlet 'i| Notlar |
|--------|---------|-----|
|Get-AzDataLakeStoreChildItem|Get-AzDataLakeGen2ChildItem|Varsayılan olarak Get-AzDataLakeGen2ChildItem cmdlet 'i yalnızca ilk düzey alt öğeleri listeler. -Recurse parametresi, alt öğeleri yinelemeli olarak listeler. |
|Get-AzDataLakeStoreItem<br>Get-AzDataLakeStoreItemAclEntry<br>Get-AzDataLakeStoreItemOwner<br>Get-AzDataLakeStoreItemPermission|Get-AzDataLakeGen2Item|Get-AzDataLakeGen2Item cmdlet 'inin çıkış öğeleri şu özelliklere sahiptir: ACL, sahip, Grup, Izin.|
|Get-AzDataLakeStoreItemContent|Get-AzDataLakeGen2FileContent|Get-AzDataLakeGen2FileContent cmdlet 'i dosya içeriğini yerel dosyaya indirir.|
|Move-AzDataLakeStoreItem|Move-AzDataLakeGen2Item||
|New-AzDataLakeStoreItem|New-AzDataLakeGen2Item|Bu cmdlet yeni dosya içeriğini yerel bir dosyadan yükler.|
|Remove-AzDataLakeStoreItem|Remove-AzDataLakeGen2Item||
|Set-AzDataLakeStoreItemOwner<br>Set-AzDataLakeStoreItemPermission<br>Set-AzDataLakeStoreItemAcl|Update-AzDataLakeGen2Item|Update-AzDataLakeGen2Item cmdlet 'i yalnızca tek bir öğeyi güncelleştirir ve yinelemeli olarak içermez. Yinelemeli olarak güncelleştirmek istiyorsanız, Get-AzDataLakeStoreChildItem cmdlet 'ini kullanarak öğeleri listeleyin ve ardından Update-AzDataLakeGen2Item cmdlet 'ine işlem hattı yapın.|
|Test-AzDataLakeStoreItem|Get-AzDataLakeGen2Item|Öğe yoksa Get-AzDataLakeGen2Item cmdlet 'i bir hata bildirir.|

## <a name="see-also"></a>Ayrıca bkz.

- [Bilinen sorunlar](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Depolama PowerShell cmdlet’leri](/powershell/module/az.storage)
