---
title: Hızlı başlangıç-PowerShell ile blob oluşturma
titleSuffix: Azure Storage
description: Bu hızlı başlangıçta nesne (Blob) depolamada Azure PowerShell’i kullanırsınız. Sonra, Azure Depolama’ya blob yüklemek, blob indirmek ve bir kapsayıcıdaki blobları listelemek için PowerShell’i kullanırsınız.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 03/31/2020
ms.author: tamram
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f3fdeadcf90d94203a972946ccceefca74cd5c3d
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012492"
---
# <a name="quickstart-upload-download-and-list-blobs-with-powershell"></a>Hızlı başlangıç: PowerShell ile Blobları karşıya yükleme, indirme ve listeleme

Azure kaynaklarını oluşturmak ve yönetmek için Azure PowerShell modülünü kullanın. Azure kaynaklarını oluşturma ve yönetme işlemleri PowerShell komut satırından veya betiklerden gerçekleştirilebilir. Bu kılavuzda yerel disk ile Azure Blob depolama arasında dosyaları aktarmak için PowerShell kullanma hakkında bilgi sağlanır.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Önkoşullar

Azure depolama 'ya erişmek için bir Azure aboneliğine sahip olmanız gerekir. Aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

Ayrıca, Azure depolama kapsayıcıları ve bloblarını okumak, yazmak ve silmek için Depolama Blobu veri katılımcısı rolüne ihtiyacınız olacaktır.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Bu hızlı başlangıçta Azure PowerShell modülü az 0,7 veya üzeri bir sürüm gerekir. Sürümü bulmak için `Get-InstalledModule -Name Az -AllVersions | select Name,Version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure PowerShell Modülü yükleme](/powershell/azure/install-az-ps).

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>Kapsayıcı oluşturma

Bloblar her zaman bir kapsayıcıya yüklenir. Bu, blob gruplarını bilgisayarınızdaki dosyaları klasörler halinde düzenlediğiniz gibi düzenleyebilmenizi sağlar.

Kapsayıcı adını ayarlayın, ardından [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer)kullanarak kapsayıcıyı oluşturun. Dosyalara genel erişime izin vermek için izinleri `blob` olarak ayarlayın. Bu örnekteki kapsayıcı adı *quickstartblobs*’tur.

```powershell
$containerName = "quickstartblobs"
New-AzStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-to-the-container"></a>Blobları kapsayıcıya yükleme

Blob depolama blok blobları, ekleme bloblarını ve sayfa bloblarını destekler. IaaS VM’lerini yedekleyen VHD dosyaları sayfa bloblarıdır. Ekleme bloblarını, bir dosyaya yazıp daha sonradan daha fazla bilgi eklemek istediğiniz durumlarda günlüğe kaydetmek için kullanın. Blob depolamada depolanan çoğu dosya blok blobudur. 

Bir dosyayı bir blok blobuna yüklemek için, bir kapsayıcı başvurusu alın ve bu kapsayıcıdaki blok blobuna bir başvuru alın. Blob başvurusunu aldıktan sonra [set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent)kullanarak verileri karşıya yükleyebilirsiniz. Bu işlemle, daha önce oluşturulmadıysa bir blob oluşturulur, blob varsa blobun üzerine yazılır.

Aşağıdaki örneklerde yerel diskteki *D:\\_TestImages* klasöründen *Image001.jpg* ve *Image002.png* dosyaları az önce oluşturduğunuz kapsayıcıya yüklenmektedir.

```powershell
# upload a file to the default account (inferred) access tier
Set-AzStorageBlobContent -File "D:\_TestImages\Image000.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx 

# upload a file to the Hot access tier
Set-AzStorageBlobContent -File "D:\_TestImages\Image001.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx `
  -StandardBlobTier Hot

# upload another file to the Cool access tier
Set-AzStorageBlobContent -File "D:\_TestImages\Image002.png" `
  -Container $containerName `
  -Blob "Image002.png" `
  -Context $ctx `
  -StandardBlobTier Cool

# upload a file to a folder to the Archive access tier
Set-AzStorageBlobContent -File "D:\_TestImages\foldername\Image003.jpg" `
  -Container $containerName `
  -Blob "Foldername/Image003.jpg" `
  -Context $ctx `
  -StandardBlobTier Archive
```

Devam etmeden önce istediğiniz sayıda dosyayı karşıya yükleyin.

## <a name="list-the-blobs-in-a-container"></a>Kapsayıcıdaki blobları listeleme

[Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob)kullanarak kapsayıcıdaki Blobların listesini alın. Bu örnekte karşıya yüklenen blobların yalnızca adları gösterilmektedir.

```powershell
Get-AzStorageBlob -Container $ContainerName -Context $ctx | select Name
```

## <a name="download-blobs"></a>Blob’ları indirme

Blobları yerel diskinize indirin. İndirmek istediğiniz her blob için, adını ayarlayın ve blobu indirmek için [Get-AzStorageBlobContent](/powershell/module/az.storage/get-azstorageblobcontent) öğesini çağırın.

Bu örnekte, bloblar yerel diskteki *D:\\_TestImages\Downloads* klasörüne indirilir. 

```powershell
# download first blob
Get-AzStorageBlobContent -Blob "Image001.jpg" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 

# download another blob
Get-AzStorageBlobContent -Blob "Image002.png" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx
```

## <a name="data-transfer-with-azcopy"></a>AzCopy ile veri aktarımı

AzCopy komut satırı yardımcı programı, Azure depolama için yüksek performanslı ve komut dosyalı veri aktarımı sağlar. BLOB depolama ve Azure dosyalarına veri aktarmak için AzCopy kullanabilirsiniz. AzCopy 'in en son sürümü olan AzCopy ile v10 arasındaki hakkında daha fazla bilgi için bkz. [AzCopy ile çalışmaya başlama](../common/storage-use-azcopy-v10.md). AzCopy ile v10 arasındaki 'i blob Storage ile kullanma hakkında bilgi edinmek için bkz. [AzCopy ve BLOB Storage ile veri aktarma](../common/storage-use-azcopy-v10.md#transfer-data).

Aşağıdaki örnek, bir Blobun yerel bir dosyayı karşıya yüklemek için AzCopy kullanır. Örnek değerleri kendi değerlerinizle değiştirmeyi unutmayın:

```powershell
azcopy login
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Oluşturduğunuz tüm varlıkları kaldırın. Varlıkları kaldırmanın en kolay yolu kaynak grubunu silmektir. Kaynak grubunu kaldırdığınızda o grubun içindeki tüm kaynaklar da silinir. Aşağıdaki örnekte, kaynak grubu kaldırıldığında depolama hesabı ve kaynak grubunun kendisi de kaldırılır.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, dosyaları yerel bir dosya sistemi ve Azure Blob depolama arasında aktardınız. PowerShell kullanarak blob depolamayla çalışma hakkında daha fazla bilgi edinmek için, BLOB depolama için Azure PowerShell örnekleri keşfedebilirsiniz.

> [!div class="nextstepaction"]
> [Azure Blob depolama için Azure PowerShell örnekleri](storage-samples-blobs-powershell.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

### <a name="microsoft-azure-powershell-storage-cmdlets-reference"></a>Microsoft Azure PowerShell Depolama cmdlet’leri başvurusu

* [Depolama PowerShell cmdlet’leri](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Depolama Gezgini

* [Microsoft Azure Depolama Gezgini](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), Microsoft’un Windows, macOS ve Linux üzerinde Azure Depolama verileriyle görsel olarak çalışmanızı sağlayan ücretsiz ve tek başına uygulamasıdır.
