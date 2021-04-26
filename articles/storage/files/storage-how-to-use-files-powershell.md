---
title: Azure PowerShell ile Azure dosya paylaşımlarını yönetme hızlı başlangıcı
description: Azure PowerShell kullanarak Azure dosya paylaşımlarını yönetmeyi öğrenmek için bu hızlı başlangıcı kullanın.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 8b4bd9ece5f010f1294356ad4673543834e5076a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94626920"
---
# <a name="quickstart-create-and-manage-an-azure-file-share-with-azure-powershell"></a>Hızlı Başlangıç: Azure PowerShell ile Azure dosya paylaşımını oluşturma ve yönetme 
Bu kılavuzda, PowerShell kullanarak [Azure dosya paylaşımlarıyla](storage-files-introduction.md) çalışmanın temel bilgileri gösterilmektedir. Azure dosya paylaşımları diğer dosya paylaşımları gibidir, ancak bulutta depolanır ve Azure platformu tarafından desteklenir. Azure dosya paylaşımları, sektör standart sunucu Ileti bloğu (SMB) protokolünü, ağ dosya sistemi (NFS) protokolünü (Önizleme) destekler ve birden çok makine, uygulama ve örnek üzerinde dosya paylaşımını sunar. 

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

PowerShell 'i yerel olarak yüklemek ve kullanmak istiyorsanız, bu kılavuzda Azure PowerShell modülü az 0,7 veya üzeri bir sürümü gerekir. Azure PowerShell modülünün hangi sürümünü çalıştırdığınızı öğrenmek için `Get-Module -ListAvailable Az` komutunu yürütün. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-Az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure hesabınızda oturum açmak için `Login-AzAccount` komutunu da çalıştırmanız gerekir.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma
Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Henüz bir Azure Kaynak grubunuz yoksa, [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet 'ini kullanarak yeni bir tane oluşturabilirsiniz. 

Aşağıdaki örnek, Batı ABD 2 bölgesinde *Myresourcegroup* adlı bir kaynak grubu oluşturur:

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$region = "westus2"

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $region | Out-Null
```

## <a name="create-a-storage-account"></a>Depolama hesabı oluşturma
Depolama hesabı, Azure dosya paylaşımlarını dağıtmak için kullanabileceğiniz, paylaşılan bir depolama havuzudur. Bir depolama hesabında sınırsız sayıda paylaşım olabilir; paylaşım da, depolama hesabının kapasite sınırları içinde sınırsız sayıda dosya depolayabilir. Bu örnek, sabit disk sürücüsü (HDD) rotasyonuna yönelik standart Azure dosya paylaşımlarını veya blob 'lar ya da kuyruklar gibi diğer depolama kaynaklarını depolayabilen genel amaçlı sürüm 2 ' yi (GPv2 Storage hesabı) oluşturur. Azure dosyaları ayrıca Premium katı hal disk sürücüleri (SSD 'Ler) destekler; Premium Azure dosya paylaşımları, FileStorage depolama hesaplarında oluşturulabilir.

Bu örnek, [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) cmdlet 'ini kullanarak bir depolama hesabı oluşturur. Depolama hesabı *mystorageaccount\<random number>* olarak adlandırılır ve **$storageAcct** değişkeninde, o depolama hesabına yönelik bir başvuru depolanır. Depolama hesabı adları benzersiz olmalıdır; bu nedenle `Get-Random` kullanarak adın sonuna bir sayı ekleyip adı benzersiz hale getirin. 

```azurepowershell-interactive 
$storageAccountName = "mystorageacct$(Get-Random)"

$storageAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $region `
    -Kind StorageV2 `
    -SkuName Standard_ZRS `
    -EnableLargeFileShare
```

> [!Note]  
> 5 TiB 'den büyük olan paylaşımlar (paylaşım başına en fazla 100 TiB 'ye kadar) yalnızca yerel olarak yedekli (LRS) ve bölge yedekli (ZRS) depolama hesaplarında kullanılabilir. Coğrafi olarak yedekli (GRS) veya coğrafi bölge-yedekli (GZRS) depolama hesabı oluşturmak için, `-EnableLargeFileShare` parametresini kaldırın.

## <a name="create-an-azure-file-share"></a>Azure dosya paylaşımı oluşturma
Artık ilk Azure dosya paylaşımınızı oluşturabilirsiniz. [New-Azrmstoragesshare](/powershell/module/az.storage/New-AzRmStorageShare) cmdlet 'ini kullanarak bir dosya paylaşımı oluşturabilirsiniz. Bu örnek, `myshare` adlı bir paylaşım oluşturur.

```azurepowershell-interactive
$shareName = "myshare"

New-AzRmStorageShare `
    -StorageAccount $storageAcct `
    -Name $shareName `
    -EnabledProtocol SMB `
    -QuotaGiB 1024 | Out-Null
```

Paylaşım adları tümüyle küçük harf, sayı ve tek kısa çizgilerden oluşmalı ve kısa çizgiyle başlamamalıdır. Dosya paylaşımlarını ve dosyaları adlandırma hakkında tüm ayrıntılar için bkz. [adlandırma ve başvuru paylaşımları, dizinler, dosyalar ve meta veriler](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Azure dosya paylaşımınızı kullanma
Azure Dosyaları, Azure dosya paylaşımınızdaki dosya ve klasörler ile çalışmak için iki yöntem sunar: sektör standardı [Sunucu İleti Bloğu (SMB) protokolü](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) ve [Dosya REST protokolü](/rest/api/storageservices/file-service-rest-api). 

Bir dosya paylaşımını SMB ile bağlayabilmeniz için işletim sisteminize göre aşağıdaki belgeye bakın:
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Dosya REST protokolü ile bir Azure dosya paylaşımını kullanma 
Dosya REST protokolüyle doğrudan çalışma olasılığı vardır (yani el ile REST HTTP çağrıları), ancak dosya REST protokolünü kullanmanın en yaygın yolu Azure PowerShell modülünü, [Azure CLI](storage-how-to-use-files-cli.md)'yi veya bir Azure depolama SDK 'sını kullanarak tercih ettiğiniz komut dosyası/programlama DILINDE Dosya REST Protokolü etrafında iyi bir sarmalayıcı sağlar.  

Kullanabilmeyi umduğunuz uygulama ve araçlarını kullanmanıza izin vereceği için çoğu durumda Azure dosya paylaşımları ile SMP protokolü üzerinden çalışmanızı bekliyoruz, ancak SMB yerine Dosya REST API'si kullanmanın aşağıdaki gibi bazı avantajları bulunmaktadır:

- Dosya paylaşımınıza (SMB üzerinden dosya paylaşımı bağlayamayan) PowerShell Cloud Shell'den göz atıyorsanız.
- [Azure İşlevleri](../../azure-functions/functions-overview.md) gibi sunucusuz kaynaklardan yararlanıyorsanız. 
- Yedekleme veya virüsten koruma taraması yapma gibi birçok Azure dosya paylaşımı ile etkileşime girebilen bir değer ekleme hizmeti oluşturuyorsunuz.

Aşağıdaki örneklerde, Azure dosya paylaşımınızı Dosya REST protokolüyle işlemek için Azure PowerShell modülünün nasıl kullanılacağı gösterilmektedir. `-Context`Parametresi, dosya paylaşımında belirtilen eylemleri gerçekleştirmek üzere depolama hesabı anahtarını almak için kullanılır. Depolama hesabı anahtarını almak için depolama hesabında Azure rolüne sahip olmanız gerekir `Owner` .

#### <a name="create-directory"></a>Dizin oluşturma
Azure dosya paylaşımınızın kökünde *mydirectory* adlı yeni bir dizin oluşturmak için [New-azstoragedirectory](/powershell/module/az.storage/New-AzStorageDirectory) cmdlet 'ini kullanın.

```azurepowershell-interactive
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Path "myDirectory"
```

#### <a name="upload-a-file"></a>Dosyayı karşıya yükleme
[Set-AzStorageFileContent](/powershell/module/az.storage/Set-AzStorageFileContent) cmdlet 'ini kullanarak bir dosyanın nasıl karşıya yükleneceğini göstermek için, önce karşıya yüklemek üzere PowerShell Cloud Shell 'nin karalama sürücünüzün içinde bir dosya oluşturmanız gerekir. 

Bu örnek, karalama sürücünüzdeki yeni bir dosyaya geçerli tarih ve saati ekler, sonra dosyayı dosya paylaşımına yükler.

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
cd "~/CloudDrive/"
Get-Date | Out-File -FilePath "SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Source "SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

PowerShell’i yerel olarak çalıştırıyorsanız, `~/CloudDrive/` bölümünün yerine makinenizde var olan bir yol kullanmalısınız.

Dosyayı karşıya yükledikten sonra [Get-AzStorageFile](/powershell/module/Az.Storage/Get-AzStorageFile) cmdlet 'ini kullanarak dosyanın Azure dosya paylaşımınıza yüklendiğinden emin olabilirsiniz. 

```azurepowershell-interactive
Get-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\" 
```

#### <a name="download-a-file"></a>Dosya indirme
Yeni yüklediğiniz dosyanın karalama Cloud Shell sürücüsüne yüklediğiniz dosyanın bir kopyasını indirmek için [Get-AzStorageFileContent](/powershell/module/az.storage/Get-AzStorageFilecontent) cmdlet 'ini kullanabilirsiniz.

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item `
    -Path "SampleDownload.txt" `
    -Force `
    -ErrorAction SilentlyContinue

Get-AzStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\SampleUpload.txt" `
    -Destination "SampleDownload.txt"
```

Dosyayı indirdikten sonra, `Get-ChildItem` komutunu kullanarak dosyanın PowerShell Cloud Shell’inizin karalama sürücüsüne indirildiğini görebilirsiniz.

```azurepowershell-interactive
Get-ChildItem | Where-Object { $_.Name -eq "SampleDownload.txt" }
``` 

#### <a name="copy-files"></a>Dosyaları kopyalama
Ortak bir görev, dosyaları bir dosya paylaşımından başka bir dosya paylaşımında kopyalamak için kullanılır. Bu işlevi göstermek için, yeni bir paylaşma oluşturabilir ve yeni yüklediğiniz dosyayı [Start-AzStorageFileCopy](/powershell/module/az.storage/Start-AzStorageFileCopy) cmdlet 'ini kullanarak bu yeni paylaşıma kopyalayabilirsiniz. 

```azurepowershell-interactive
$otherShareName = "myshare2"

New-AzRmStorageShare `
    -StorageAccount $storageAcct `
    -Name $otherShareName `
    -EnabledProtocol SMB `
    -QuotaGiB 1024 | Out-Null
  
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName $otherShareName `
   -Path "myDirectory2"

Start-AzStorageFileCopy `
    -Context $storageAcct.Context `
    -SrcShareName $shareName `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestShareName $otherShareName `
    -DestFilePath "myDirectory2\SampleCopy.txt" `
    -DestContext $storageAcct.Context
```

Şimdi, yeni paylaşımdaki dosyaları listelerseniz kopyalanan dosyanızı görmeniz gerekir.

```azurepowershell-interactive
Get-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $otherShareName `
    -Path "myDirectory2" 
```

Cmdlet, `Start-AzStorageFileCopy` Azure dosya paylaşımları arasında geçici dosya taşıma işlemleri için uygun olsa da, geçişler ve daha büyük veri hareketleri Için `robocopy` Windows ve `rsync` MacOS ve Linux üzerinde önerilir. `robocopy` ve, `rsync` Dosyasıest API 'si yerine veri taşımalarını gerçekleştirmek IÇIN SMB 'yi kullanın.

## <a name="create-and-manage-share-snapshots"></a>Paylaşım anlık görüntülerini oluşturma ve yönetme
Azure dosya paylaşımıyla yerine getirebileceğiniz kullanışlı bir diğer görev de paylaşım anlık görüntüleri oluşturmaktır. Anlık görüntü, Azure dosya paylaşımının zamanın bir noktasındaki durumunu saklar. Paylaşım anlık görüntüleri, aşağıdakiler gibi zaten tanıyor olabileceğiniz işletim sistemi teknolojilerine benzer:

- NTFS ve ReFS gibi Windows dosya sistemleri için [birim gölge kopyası hizmeti (VSS)](/windows/desktop/VSS/volume-shadow-copy-service-portal) .
- Linux sistemleri için [mantıksal birim Yöneticisi (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) anlık görüntüleri.
- MacOS için [Apple dosya sistemi (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) anlık görüntüleri. 

`Snapshot` [Get-Azstoragesshare](/powershell/module/az.storage/Get-AzStorageShare) cmdlet 'i ile alınan bir dosya paylaşımı için PowerShell nesnesinde yöntemini kullanarak bir paylaşım için paylaşım anlık görüntüsü oluşturabilirsiniz. 

```azurepowershell-interactive
$share = Get-AzStorageShare -Context $storageAcct.Context -Name $shareName
$snapshot = $share.CloudFileShare.Snapshot()
```

### <a name="browse-share-snapshots"></a>Paylaşım anlık görüntülerine göz atma
`Get-AzStorageFile` cmdlet’inin `-Share` parametresine anlık görüntü başvurusunu (`$snapshot`) geçirerek paylaşım anlık görüntüsünün içeriklerine göz atabilirsiniz.

```azurepowershell-interactive
Get-AzStorageFile -Share $snapshot
```

### <a name="list-share-snapshots"></a>Paylaşım anlık görüntülerini listeleme
Paylaşımınız için aldığınız anlık görüntülerin listesini aşağıdaki komutla görebilirsiniz.

```azurepowershell-interactive
Get-AzStorageShare `
        -Context $storageAcct.Context | `
    Where-Object { $_.Name -eq $shareName -and $_.IsSnapshot -eq $true }
```

### <a name="restore-from-a-share-snapshot"></a>Paylaşım anlık görüntüsünden geri yükleme
Daha önce kullandığımız `Start-AzStorageFileCopy` komutu kullanarak dosyayı geri yükleyebilirsiniz. Bu hızlı başlangıçta ilk olarak önceden karşıya yüklediğimiz `SampleUpload.txt` dosyamızı silerek anlık görüntüden bu dosyayı geri yükleyeceğiz.

```azurepowershell-interactive
# Delete SampleUpload.txt
Remove-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\SampleUpload.txt"

# Restore SampleUpload.txt from the share snapshot
Start-AzStorageFileCopy `
    -SrcShare $snapshot `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestContext $storageAcct.Context `
    -DestShareName $shareName `
    -DestFilePath "myDirectory\SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Paylaşım anlık görüntüsünü silme
[Remove-Azstoragesshare](/powershell/module/az.storage/Remove-AzStorageShare) cmdlet 'ini kullanarak bir paylaşım anlık görüntüsünü parametre başvurusunu içeren değişkenle silebilirsiniz `$snapshot` `-Share` .

```azurepowershell-interactive
Remove-AzStorageShare `
    -Share $snapshot `
    -Confirm:$false `
    -Force
```

## <a name="clean-up-resources"></a>Kaynakları temizleme
İşiniz bittiğinde, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) cmdlet 'ini kullanarak kaynak grubunu ve tüm ilgili kaynakları kaldırabilirsiniz. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

Alternatif olarak kaynakları tek tek de kaldırabilirsiniz:

- Bu hızlı başlangıç için oluşturduğumuz Azure dosya paylaşımlarını kaldırmak için.

    ```azurepowershell-interactive
    Get-AzRmStorageShare -StorageAccount $storageAcct | Remove-AzRmStorageShare -Force
    ```

    > [!Note]  
    > Azure dosya paylaşımını silmeden önce oluşturduğunuz Azure dosya paylaşımları için tüm paylaşım anlık görüntülerini silmeniz gerekir.

- Depolama hesabının kendisini kaldırmak için (bu işlem örtülü olarak hem oluşturduğumuz Azure dosya paylaşımlarını hem de oluşturmuş olabileceğiniz Azure Blob depolama kapsayıcısı gibi diğer depolama kaynaklarını kaldırır).

    ```azurepowershell-interactive
    Remove-AzStorageAccount `
        -ResourceGroupName $storageAcct.ResourceGroupName `
        -Name $storageAcct.StorageAccountName
    ```

## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"]
> [Azure Dosyalar nedir?](storage-files-introduction.md)