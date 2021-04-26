---
title: Blob (nesne) depolamaya giriş
titleSuffix: Azure Storage
description: Metin veya ikili veriler gibi büyük miktarda yapılandırılmamış nesne verilerini depolamak için Azure Blob depolama 'yı kullanın. Azure Blob depolama, yüksek oranda ölçeklenebilir ve kullanılabilir.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 03/27/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 6d71fc7931db77f0cf19a4e7ea70731ba19d1f6d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105937320"
---
# <a name="introduction-to-azure-blob-storage"></a>Azure Blob depolamaya giriş

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

## <a name="blob-storage-resources"></a>BLOB depolama kaynakları

BLOB depolama üç tür kaynak sunar:

- **Depolama hesabı**
- Depolama hesabındaki bir **kapsayıcı**
- Kapsayıcıda bir **BLOB**

Aşağıdaki diyagramda bu kaynaklar arasındaki ilişki gösterilmektedir.

![Bir depolama hesabı, kapsayıcılar ve BLOB 'lar arasındaki ilişkiyi gösteren diyagram](./media/storage-blobs-introduction/blob1.png)

### <a name="storage-accounts"></a>Depolama hesapları

Depolama hesabı, Azure 'da verileriniz için benzersiz bir ad alanı sağlar. Azure Storage 'da depoladığınız her nesnenin benzersiz hesap adınızı içeren bir adresi vardır. Hesap adı ve Azure Storage blob uç noktası birleşimi, Depolama hesabınızdaki nesneler için temel adresi oluşturur.

Örneğin, depolama hesabınız *mystorageaccount* olarak adlandırılmışsa, blob depolaması için varsayılan uç nokta şu şekilde olur:

```
http://mystorageaccount.blob.core.windows.net
```

Depolama hesabı oluşturmak için bkz. [depolama hesabı oluşturma](../common/storage-account-create.md). Depolama hesapları hakkında daha fazla bilgi için bkz. [Azure depolama hesabına genel bakış](../common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="containers"></a>Kapsayıcılar

Bir kapsayıcı, bir dosya sistemindeki dizine benzer bir blob kümesini düzenler. Depolama hesabında sınırsız sayıda kapsayıcı olabilir ve her kapsayıcı sınırsız sayıda blob depolayabilir.

> [!NOTE]
> Kapsayıcı adındaki harfler küçük harf olmalıdır. Kapsayıcıları adlandırma hakkında daha fazla bilgi için bkz. [kapsayıcıları, Blobları ve meta verileri adlandırma ve başvuru](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).

### <a name="blobs"></a>Bloblar

Azure depolama, üç tür blob destekler:

- **Blok Blobları** metin ve ikili verileri depolar. Blok blobları, ayrı ayrı yönetilebilen veri bloklarından oluşur. Blok Blob 'ları yaklaşık 190,7 TiB 'ye kadar depolama sağlayabilir.
- **Ekleme Blobları** blok Blobları gibi bloklarından oluşur, ancak ekleme işlemleri için en iyi duruma getirilmiştir. Ekleme blobları sanal makine verilerini günlüğe kaydetme gibi senaryolar için idealdir.
- **Sayfa Blobları** , rastgele erişim dosyalarını, boyutu 8 TİB 'ye kadar depolar. Sayfa Blobları, sanal sabit sürücü (VHD) dosyalarını depolar ve Azure sanal makineleri için disk olarak görev yapar. Sayfa Blobları hakkında daha fazla bilgi için bkz. [Azure sayfa Bloblarına genel bakış](storage-blob-pageblob-overview.md)

Farklı blob türleri hakkında daha fazla bilgi için bkz. [blok bloblarını, ekleme bloblarını ve sayfa Bloblarını anlama](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

## <a name="move-data-to-blob-storage"></a>Verileri Blob depolamaya taşıma

Mevcut verilerin blob depolamaya geçirilmesi için bir dizi çözüm vardır:

- **AzCopy** , Windows ve Linux Için, blob depolamaya, kapsayıcılara veya depolama hesaplarına veri kopyalayan kullanımı kolay bir komut satırı aracıdır. AzCopy hakkında daha fazla bilgi için bkz. [AzCopy ile v10 arasındaki ile veri aktarma](../common/storage-use-azcopy-v10.md).
- **Azure depolama veri taşıma kitaplığı** , verileri Azure depolama hizmetleri arasında taşımak için bir .net kitaplığıdır. AzCopy yardımcı programı, veri taşıma kitaplığıyla oluşturulmuştur. Daha fazla bilgi için bkz. veri taşıma kitaplığı için [başvuru belgeleri](/dotnet/api/microsoft.azure.storage.datamovement) .
- **Azure Data Factory** , hesap anahtarı, paylaşılan erişim imzası, hizmet sorumlusu veya Azure kaynakları için Yönetilen kimlikler kullanarak blob depolamadan veri kopyalamayı destekler. Daha fazla bilgi için bkz. [Azure Data Factory kullanarak Azure Blob depolama alanına veya buradan veri kopyalama](../../data-factory/connector-azure-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
- **Blobsigortası** , Azure Blob depolama için bir sanal dosya sistemi sürücüsüdür. Blobsigortası kullanarak, Linux dosya sistemi aracılığıyla Depolama hesabınızdaki mevcut blok blob verilerinize erişebilirsiniz. Daha fazla bilgi için bkz. [BLOB depolamayı bir dosya sistemi olarak blobsigortası ile bağlama](storage-how-to-mount-container-linux.md).
- **Azure Data Box** hizmet, büyük veri kümeleri veya ağ kısıtlamaları, verilerin gerçekçi olmayan şekilde karşıya yüklenmesini sağlamak için şirket Içi verileri blob depolamaya aktarmak için kullanılabilir. Veri boyutunuza bağlı olarak, Microsoft 'tan [Azure Data Box disk](../../databox/data-box-disk-overview.md), [Azure Data Box](../../databox/data-box-overview.md)veya [Azure Data Box Heavy](../../databox/data-box-heavy-overview.md) cihazları isteyebilirsiniz. Daha sonra verilerinizi bu cihazlara kopyalayabilir ve bunları blob depolamaya yüklemek üzere Microsoft 'a gönderebilirsiniz.
- **Azure içeri/dışarı aktarma hizmeti** , sağladığınız sabit sürücüler kullanılarak depolama hesabınıza büyük miktarlarda verileri içeri veya dışarı aktarmak için bir yol sağlar. Daha fazla bilgi için bkz. [BLOB depolama alanına veri aktarmak için Microsoft Azure içeri/dışarı aktarma hizmetini kullanma](../../import-export/storage-import-export-service.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Depolama hesabı oluşturma](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [BLOB depolama için ölçeklenebilirlik ve performans hedefleri](scalability-targets.md)
