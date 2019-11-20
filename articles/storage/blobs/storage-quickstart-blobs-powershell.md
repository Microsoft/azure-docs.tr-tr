---
title: Azure Hızlı Başlangıç - Azure PowerShell kullanarak nesne depolamada blob oluşturma | Microsoft Docs
description: Bu hızlı başlangıçta nesne (Blob) depolamada Azure PowerShell’i kullanırsınız. Sonra, Azure Depolama’ya blob yüklemek, blob indirmek ve bir kapsayıcıdaki blobları listelemek için PowerShell’i kullanırsınız.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: tamram
ms.openlocfilehash: b0e9cc37f6269c3b878e16b754ec3a49aee13f72
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698993"
---
# <a name="quickstart-upload-download-and-list-blobs-by-using-azure-powershell"></a>Hızlı Başlangıç: Azure PowerShell kullanarak Blobları karşıya yükleme, indirme ve listeleme

Azure kaynaklarını oluşturmak ve yönetmek için Azure PowerShell modülünü kullanın. Azure kaynaklarını oluşturma ve yönetme işlemleri PowerShell komut satırından veya betiklerden gerçekleştirilebilir. Bu kılavuzda yerel disk ile Azure Blob depolama arasında dosyaları aktarmak için PowerShell kullanma hakkında bilgi sağlanır.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Önkoşullar

Azure depolama 'ya erişmek için bir Azure aboneliğine sahip olmanız gerekir. Aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Bu hızlı başlangıçta Azure PowerShell modülü az 0,7 veya üzeri bir sürüm gerekir. Sürümü bulmak için `Get-InstalledModule -Name Az -AllVersions | select Name,Version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure PowerShell Modülü yükleme](/powershell/azure/install-Az-ps).

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>Bir kapsayıcı oluşturma

Bloblar her zaman bir kapsayıcıya yüklenir. Bu, blob gruplarını bilgisayarınızdaki dosyaları klasörler halinde düzenlediğiniz gibi düzenleyebilmenizi sağlar.

Kapsayıcı adını ayarlayın, ardından [New-AzStorageContainer](/powershell/module/az.storage/new-AzStoragecontainer)kullanarak kapsayıcıyı oluşturun. Dosyalara genel erişime izin vermek için izinleri `blob` olarak ayarlayın. Bu örnekteki kapsayıcı adı *quickstartblobs*’tur.

```powershell
$containerName = "quickstartblobs"
New-AzStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-to-the-container"></a>Blobları kapsayıcıya yükleme

Blob depolama blok blobları, ekleme bloblarını ve sayfa bloblarını destekler. IaaS VM’lerini yedekleyen VHD dosyaları sayfa bloblarıdır. Ekleme bloblarını, bir dosyaya yazıp daha sonradan daha fazla bilgi eklemek istediğiniz durumlarda günlüğe kaydetmek için kullanın. Blob depolamada depolanan çoğu dosya blok blobudur. 

Bir dosyayı bir blok blobuna yüklemek için, bir kapsayıcı başvurusu alın ve bu kapsayıcıdaki blok blobuna bir başvuru alın. Blob başvurusunu aldıktan sonra [set-AzStorageBlobContent](/powershell/module/az.storage/set-AzStorageblobcontent)kullanarak verileri karşıya yükleyebilirsiniz. Bu işlemle, daha önce oluşturulmadıysa bir blob oluşturulur, blob varsa blobun üzerine yazılır.

Aşağıdaki örneklerde yerel diskteki *D:\\_TestImages* klasöründen *Image001.jpg* ve *Image002.png* dosyaları az önce oluşturduğunuz kapsayıcıya yüklenmektedir.

```powershell
# upload a file
Set-AzStorageBlobContent -File "D:\_TestImages\Image001.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx 

# upload another file
Set-AzStorageBlobContent -File "D:\_TestImages\Image002.png" `
  -Container $containerName `
  -Blob "Image002.png" `
  -Context $ctx
```

Devam etmeden önce istediğiniz sayıda dosyayı karşıya yükleyin.

## <a name="list-the-blobs-in-a-container"></a>Blob’ları bir kapsayıcıda listeleme

[Get-AzStorageBlob](/powershell/module/az.storage/get-AzStorageblob)kullanarak kapsayıcıdaki Blobların listesini alın. Bu örnekte karşıya yüklenen blobların yalnızca adları gösterilmektedir.

```powershell
Get-AzStorageBlob -Container $ContainerName -Context $ctx | select Name
```

## <a name="download-blobs"></a>Blob’ları indirme

Blobları yerel diskinize indirin. İndirmek istediğiniz her blob için, adını ayarlayın ve blobu indirmek için [Get-AzStorageBlobContent](/powershell/module/az.storage/get-AzStorageblobcontent) öğesini çağırın.

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

[AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) yardımcı programı, Azure Depolama’ya yüksek performanslı betik oluşturulabilir veri aktarımı için diğer bir seçenektir. Blob, Dosya ve Tablo depolamaları arasında veri aktarmak için AzCopy kullanın.

Hızlı bir örnek olarak, bir PowerShell penceresinden *myfile.txt* adlı dosyayı *mystoragecontainer* kapsayıcısına yüklemek için AzCopy komutu aşağıda gösterilmiştir.

```powershell
./AzCopy `
    /Source:C:\myfolder `
    /Dest:https://mystorageaccount.blob.core.windows.net/mystoragecontainer `
    /DestKey:<storage-account-access-key> `
    /Pattern:"myfile.txt"
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Oluşturduğunuz tüm varlıkları kaldırın. Varlıkları kaldırmanın en kolay yolu kaynak grubunu silmektir. Kaynak grubunu kaldırdığınızda o grubun içindeki tüm kaynaklar da silinir. Aşağıdaki örnekte, kaynak grubu kaldırıldığında depolama hesabı ve kaynak grubunun kendisi de kaldırılır.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, dosyaları yerel bir disk ile Azure Blob depolama arasında aktardınız. PowerShell'i kullanarak Blob depolama ile çalışma hakkında daha fazla bilgi edinmek için Azure PowerShell'i Azure Depolama'yla kullanma öğreticisine geçin.

> [!div class="nextstepaction"]
> [Azure Depolama ile Azure PowerShell’i kullanma](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

### <a name="microsoft-azure-powershell-storage-cmdlets-reference"></a>Microsoft Azure PowerShell Depolama cmdlet’leri başvurusu

* [Depolama PowerShell cmdlet’leri](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Depolama Gezgini

* [Microsoft Azure Depolama Gezgini](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), Microsoft’un Windows, macOS ve Linux üzerinde Azure Depolama verileriyle görsel olarak çalışmanızı sağlayan ücretsiz ve tek başına uygulamasıdır.
