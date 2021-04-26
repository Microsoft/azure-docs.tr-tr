---
title: Azure Data Lake Storage 2. ACL 'Leri yönetmek için PowerShell kullanma
description: Hiyerarşik ad alanı (HNS) etkin olan depolama hesaplarında erişim denetim listelerini (ACL) yönetmek için PowerShell cmdlet 'lerini kullanın.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: dd522355d30564d84fec15bdc57c7397c1e6cfe4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "104702550"
---
# <a name="use-powershell-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage 2. ACL 'Leri yönetmek için PowerShell kullanma

Bu makalede, PowerShell kullanarak dizinlerin ve dosyaların erişim denetim listelerini alma, ayarlama ve güncelleştirme işlemlerinin nasıl yapılacağı gösterilir. 

ACL devralma, bir üst dizin altında oluşturulan yeni alt öğeler için zaten kullanılabilir. Ancak, bu değişiklikleri her alt öğe için ayrı ayrı yapmak zorunda kalmadan ACL 'Leri bir üst dizinin varolan alt öğelerine yinelemeli olarak ekleyebilir, güncelleştirebilir ve kaldırabilirsiniz. 

[Başvuru](/powershell/module/Az.Storage/)  |  [ÖZYINELEMELI ACL örnekleri](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D)  |  [Geri bildirimde](https://github.com/Azure/azure-powershell/issues) bulunun

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği. Bkz. [Azure ücretsiz deneme sürümü edinme](https://azure.microsoft.com/pricing/free-trial/).

- Hiyerarşik ad alanı (HNS) etkin olan bir depolama hesabı. Bir tane oluşturmak için [Bu](create-data-lake-storage-account.md) yönergeleri izleyin.

- Azure CLı sürümü `2.6.0` veya üzeri.

- Aşağıdaki güvenlik izinlerinden biri:

  - Hedef kapsayıcının, üst kaynak grubunun veya aboneliğin kapsamındaki [Depolama Blobu veri sahibi](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) rolüne atanan bir Azure ACTIVE DIRECTORY (ad) [güvenlik sorumlusu](../../role-based-access-control/overview.md#security-principal) .  

  - ACL ayarlarını uygulamayı planladığınız Hedef kapsayıcının veya dizinin sahibi olan kullanıcı. ACL 'Leri yinelemeli olarak ayarlamak için, hedef kapsayıcı veya dizindeki tüm alt öğeleri içerir.
  
  - Depolama hesabı anahtarı.

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

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Seçenek 1: Azure Active Directory kullanarak yetkilendirme alma (AD)

> [!NOTE]
> Erişimi yetkilendirmek için Azure Active Directory (Azure AD) kullanıyorsanız, güvenlik sorumlusuna [Depolama Blobu veri sahibi rolü](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)atandığından emin olun. ACL izinlerinin nasıl uygulandığı ve bunların nasıl değiştirileceği hakkında daha fazla bilgi edinmek için  [Azure Data Lake Storage 2. erişim denetimi modeli](./data-lake-storage-access-control-model.md)' ne bakın.

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

## <a name="get-acls"></a>ACL 'Leri al

Cmdlet 'ini kullanarak bir dizin veya dosyanın ACL 'sini alın `Get-AzDataLakeGen2Item` .

Bu örnek, bir **kapsayıcının** kök dizininin ACL 'sini alır ve ardından ACL 'yi konsola yazdırır.

```powershell
$filesystemName = "my-file-system"
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

Bu örnek, bir **DIZININ** ACL 'sini alır ve ardından ACL 'yi konsola yazdırır.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

Bu örnek, bir **DOSYANıN** ACL 'sini alır ve ardından ACL 'yi konsola yazdırır.

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

Aşağıdaki görüntüde bir dizinin ACL 'SI alındıktan sonra çıkış gösterilmektedir.

![Dizin için ACL çıkışı al](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

Bu örnekte, sahip olan kullanıcının okuma, yazma ve yürütme izinleri vardır. Sahip olan grubun yalnızca okuma ve yürütme izinleri vardır. Erişim denetim listeleri hakkında daha fazla bilgi için bkz. [Azure Data Lake Storage 2. Access Control](data-lake-storage-access-control.md).

## <a name="set-acls"></a>ACL 'Leri ayarla

Bir ACL *ayarladığınızda* , tüm ACL 'leri dahil olmak üzere tüm ACL 'yi **değiştirirsiniz** . Bir güvenlik sorumlusunun izin düzeyini değiştirmek veya var olan girişleri etkilemeden ACL 'ye yeni bir güvenlik sorumlusu eklemek istiyorsanız, ACL 'yi *güncelleştirmeniz* gerekir. Bunu değiştirmek yerine bir ACL 'yi güncelleştirmek için, bu makalenin [ACL 'Leri Güncelleştir](#update-acls) bölümüne bakın.  

ACL 'yi *ayarlamayı* seçerseniz, sahip olan kullanıcı için bir giriş, sahip olan grup için bir giriş ve diğer tüm kullanıcılar için bir girdi eklemeniz gerekir. Sahip olan Kullanıcı, sahip olan grup ve diğer tüm kullanıcılar hakkında daha fazla bilgi edinmek için bkz. [Kullanıcılar ve kimlikler](data-lake-storage-access-control.md#users-and-identities).

Bu bölümde nasıl yapılacağı gösterilmektedir:

- ACL ayarla
- ACL’leri özyinelemeli olarak belirleme

### <a name="set-an-acl"></a>ACL ayarla

`set-AzDataLakeGen2ItemAclObject`Sahip olan Kullanıcı, sahip olan grup veya diğer kullanıcılar için BIR ACL oluşturmak üzere cmdlet 'ini kullanın. Ardından, ACL 'yi `Update-AzDataLakeGen2Item` yürütmek için cmdlet 'ini kullanın.

Bu örnek, sahip olan Kullanıcı, sahip olan grup veya diğer kullanıcılar için bir **kapsayıcının** kök dizinindeki ACL 'yi ayarlar ve ardından ACL 'yi konsola yazdırır.

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Acl $acl
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

Bu örnek, ACL 'yi sahip olan Kullanıcı, sahip olan grup veya diğer kullanıcılar için bir **dizinde** ayarlar ve ardından ACL 'yi konsola yazdırır.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

> [!NOTE]
> **Varsayılan** bir ACL girişi ayarlamak Istiyorsanız, **set-AzDataLakeGen2ItemAclObject** komutunu çalıştırdığınızda **-DefaultScope** parametresini kullanın. Örneğin: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`. 

Bu örnek, sahip olan Kullanıcı, sahip olan grup veya diğer kullanıcılar için bir **DOSYADAKI** ACL 'yi ayarlar ve ardından ACL 'yi konsola yazdırır.

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath -Acl $acl
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

> [!NOTE]
> Belirli bir grubun veya kullanıcının ACL 'sini ayarlamak için ilgili nesne kimliklerini kullanın. Örneğin `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` veya `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` olabilir.

Aşağıdaki görüntüde, bir dosyanın ACL 'sini ayarlamadıktan sonra çıkış gösterilmektedir.

![Dosya için ACL çıkışı al](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

Bu örnekte, sahip olan Kullanıcı ve sahip olan Grup yalnızca okuma ve yazma izinlerine sahiptir. Diğer tüm kullanıcılar yazma ve yürütme izinlerine sahiptir. Erişim denetim listeleri hakkında daha fazla bilgi için bkz. [Azure Data Lake Storage 2. Access Control](data-lake-storage-access-control.md).

### <a name="set-acls-recursively"></a>ACL’leri özyinelemeli olarak belirleme

**Set-AzDataLakeGen2AclRecursive** cmdlet 'Ini kullanarak ACL 'leri yinelemeli olarak ayarlayın.

Bu örnek, adlı bir dizinin ACL 'sini ayarlar `my-parent-directory` . Bu girişler, sahip olan kullanıcıya okuma, yazma ve yürütme izinleri verir, sahip olan gruba yalnızca okuma ve yürütme izinleri verir ve tüm diğerlerine erişim vermez. Bu örnekteki son ACL girişi, "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" okuma ve yürütme izinlerine sahip belirli bir kullanıcıya izin verir.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission r-x -InputObject $acl 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "---" -InputObject $acl
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission r-x -InputObject $acl 

Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> **Varsayılan** bir ACL girişi ayarlamak Istiyorsanız, **set-AzDataLakeGen2ItemAclObject** komutunu çalıştırdığınızda **-DefaultScope** parametresini kullanın. Örneğin: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`.

Toplu iş boyutu belirterek ACL 'Leri yinelemeli olarak kümeler halinde ayarlayan bir örnek görmek için, [set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) Reference makalesine bakın.

## <a name="update-acls"></a>ACL 'Leri Güncelleştir

Bir ACL 'yi *güncelleştirdiğinizde* ACL 'yi DEğIşTIRMEk yerine ACL 'yi değiştirirsiniz. Örneğin, ACL 'de listelenen diğer güvenlik sorumlularını etkilemeden ACL 'ye yeni bir güvenlik sorumlusu ekleyebilirsiniz.  ACL 'yi güncelleştirmek yerine değiştirmek için, bu makalenin [ACL 'Leri ayarlama](#set-acls) bölümüne bakın.

Bir ACL 'yi güncelleştirmek için, güncelleştirmek istediğiniz ACL girdisiyle yeni bir ACL nesnesi oluşturun ve ACL 'yi Güncelleştir işleminde bu nesneyi kullanın. Mevcut ACL 'yi kullanmayın, yalnızca görüntülenecek ACL girdilerini sağlayın.

Bu bölümde nasıl yapılacağı gösterilmektedir:

- ACL güncelleştirme
- ACL 'Leri yinelemeli olarak güncelleştir

### <a name="update-an-acl"></a>ACL güncelleştirme

İlk olarak, ACL 'yi alın. Ardından, `set-AzDataLakeGen2ItemAclObject` BIR ACL girdisi eklemek veya güncelleştirmek için cmdlet 'ini kullanın. `Update-AzDataLakeGen2Item`ACL 'yi yürütmek için cmdlet 'ini kullanın.

Bu örnek, bir kullanıcı için **Dizin** üzerinde ACL oluşturur veya güncelleştirir.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -InputObject $acl 
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
```

> [!NOTE]
> **Varsayılan** bir ACL girişini güncelleştirmek Istiyorsanız, **set-AzDataLakeGen2ItemAclObject** komutunu çalıştırdığınızda **-DefaultScope** parametresini kullanın. Örneğin: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -DefaultScope`.

### <a name="update-acls-recursively"></a>ACL 'Leri yinelemeli olarak güncelleştir

**Update-AzDataLakeGen2AclRecursive** cmdlet 'Ini kullanarak ACL 'leri yinelemeli olarak güncelleştirin.

Bu örnekte, yazma izniyle bir ACL girişi güncelleştirilir.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx

Update-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Belirli bir grubun veya kullanıcının ACL 'sini ayarlamak için ilgili nesne kimliklerini kullanın. Örneğin `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` veya `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` olabilir.

Toplu iş boyutu belirterek ACL 'Leri yinelemeli olarak toplu halde güncelleştiren bir örnek görmek için [Update-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/update-azdatalakegen2aclrecursive) Reference makalesine bakın.

## <a name="remove-acl-entries"></a>ACL girdilerini kaldır

Bu bölümde nasıl yapılacağı gösterilmektedir:

- ACL girişini kaldırma
- ACL girdilerini yinelemeli olarak kaldır

### <a name="remove-an-acl-entry"></a>ACL girişini kaldırma

Bu örnek, var olan bir ACL 'den bir girişi kaldırır.

```powershell
$id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

# Create the new ACL object.
[Collections.Generic.List[System.Object]]$aclnew =$acl

foreach ($a in $aclnew)
{
    if ($a.AccessControlType -eq "User"-and $a.DefaultScope -eq $false -and $a.EntityId -eq $id)
    {
        $aclnew.Remove($a);
        break;
    }
}
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $aclnew
```

### <a name="remove-acl-entries-recursively"></a>ACL girdilerini yinelemeli olarak kaldır

Bir veya daha fazla ACL girişini yinelemeli olarak kaldırabilirsiniz. Bir ACL girişini kaldırmak için, ACL girdisi kaldırılacak yeni bir ACL nesnesi oluşturun ve sonra ACL 'yi kaldır işleminde bu nesneyi kullanın. Mevcut ACL 'yi almaz, kaldırılacak ACL girdilerini sağlamanız yeterlidir.

**Remove-AzDataLakeGen2AclRecursive** cmdlet 'INI kullanarak acl girişlerini kaldırın.

Bu örnek, kapsayıcının kök dizininden bir ACL girişini kaldırır.  

```powershell
$filesystemName = "my-container"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" 

Remove-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName  -Acl $acl
```

> [!NOTE]
> **Varsayılan** bir ACL girişini kaldırmak Istiyorsanız, **set-AzDataLakeGen2ItemAclObject** komutunu çalıştırdığınızda **-DefaultScope** parametresini kullanın. Örneğin: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" -DefaultScope`.

Toplu iş boyutu belirterek ACL 'Leri yinelemeli olarak toplu halde kaldıran bir örnek görmek için, [Remove-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/remove-azdatalakegen2aclrecursive) Reference makalesine bakın.

## <a name="recover-from-failures"></a>Hatalardan kurtarma

ACL 'Leri yinelemeli olarak değiştirirken çalışma zamanı veya izin hatalarıyla karşılaşabilirsiniz. 

Çalışma zamanı hataları için işlemi baştan başlatın. Güvenlik sorumlusu, değiştirilen Dizin hiyerarşisindeki bir dizinin veya dosyanın ACL 'sini değiştirmek için yeterli izne sahip değilse, izin hataları oluşabilir. İzin sorununu çözün ve sonra bir devamlılık belirteci kullanarak işlemi hata noktasından sürdürmeyi seçin ya da işlemi baştan başlatın. Baştan başlatmayı tercih ediyorsanız devamlılık belirtecini kullanmanız gerekmez. ACL girdilerini negatif bir etki olmadan yeniden uygulayabilirsiniz.

Bu örnek, değişkenine sonuçları döndürür ve sonra, başarısız girdileri biçimli bir tabloya yöneltmekte.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$result
$result.FailedEntries | ft 
```

Tablonun çıktısına bağlı olarak, tüm izin hatalarını giderebilir ve sonra devamlılık belirtecini kullanarak yürütmeyi sürdürebilirsiniz.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinuationToken $result.ContinuationToken
$result

```

Toplu iş boyutu belirterek ACL 'Leri yinelemeli olarak kümeler halinde ayarlayan bir örnek görmek için, [set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) Reference makalesine bakın.

İşlemin, izin hatalarıyla kesintiye uğramadan tamamlanmasını istiyorsanız, bunu belirtebilirsiniz.

Bu örnek, `ContinueOnFailure` işlem bir izin hatasıyla karşılaştığında bile yürütmenin devam etmesi için parametresini kullanır. 

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinueOnFailure

echo "[Result Summary]"
echo "TotalDirectoriesSuccessfulCount: `t$($result.TotalFilesSuccessfulCount)"
echo "TotalFilesSuccessfulCount: `t`t`t$($result.TotalDirectoriesSuccessfulCount)"
echo "TotalFailureCount: `t`t`t`t`t$($result.TotalFailureCount)"
echo "FailedEntries:"$($result.FailedEntries | ft) 
```

Toplu iş boyutu belirterek ACL 'Leri yinelemeli olarak kümeler halinde ayarlayan bir örnek görmek için, [set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) Reference makalesine bakın.

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Ayrıca bkz.

- [Bilinen sorunlar](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Depolama PowerShell cmdlet’leri](/powershell/module/az.storage)
- [Azure Data Lake Storage 2. 'de erişim denetimi modeli](data-lake-storage-access-control.md)
- [Azure Data Lake Storage 2. 'de erişim denetim listeleri (ACL 'Ler)](data-lake-storage-access-control.md)
