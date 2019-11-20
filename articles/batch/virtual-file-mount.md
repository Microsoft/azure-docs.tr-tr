---
title: Bir havuza sanal dosya sistemi bağlama-Azure Batch | Microsoft Docs
description: Bir Batch havuzunda sanal dosya sistemi bağlama hakkında bilgi edinin.
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2019
ms.author: lahugh
ms.openlocfilehash: 1c990c864f9daa98460832166b31f43fece1ed15
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093850"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>Bir Batch havuzunda sanal dosya sistemi bağlama

Azure Batch artık Batch havuzlarınızdaki Windows veya Linux işlem düğümlerinde bulut depolama veya harici bir dosya sistemi bağlamayı desteklemektedir. Bir işlem düğümü bir havuza katıldığında, sanal dosya sistemi bağlanır ve bu düğümde yerel bir sürücü olarak değerlendirilir. Azure dosyaları, Azure Blob depolama, ağ dosya sistemi (NFS) gibi bir [avere vFXT önbelleği](../avere-vfxt/avere-vfxt-overview.md)veya ortak Internet dosya SISTEMI (CIFS) gibi dosya sistemlerini bağlayabilirsiniz.

Bu makalede, [.net Için Batch Yönetim Kitaplığı](https://docs.microsoft.com/dotnet/api/overview/azure/batch?view=azure-dotnet)'nı kullanarak bir işlem düğümleri havuzuna bir sanal dosya sistemi bağlama hakkında bilgi edineceksiniz.

> [!NOTE]
> Bir sanal dosya sistemi bağlama, 2019-08-19 tarihinde veya sonrasında oluşturulan Batch havuzlarında desteklenir. 2019-08-19 ' den önce oluşturulan toplu iş havuzları bu özelliği desteklemez.
> 
> Dosya sistemlerini bir işlem düğümüne bağlamak için API 'Ler [Batch .net](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch?view=azure-dotnet) kitaplığı 'nın bir parçasıdır.

## <a name="benefits-of-mounting-on-a-pool"></a>Bir havuza bağlama avantajları

Dosya sistemini havuza bağlama, görevlerin büyük bir veri kümesinden kendi verilerini almasına izin vermek yerine, görevlerin gerekli verilere erişmesi daha kolay ve daha verimli hale gelir.

Bir filmi işleme gibi ortak bir veri kümesine erişmesi gereken birden çok görevle bir senaryo düşünün. Her görev, sahne dosyalarından bir seferde bir veya daha fazla kare oluşturur. Sahne dosyalarını içeren bir sürücü bağlayarak, işlem düğümlerinin paylaşılan verilere erişmesi daha kolay olur. Ayrıca, temel alınan dosya sistemi, verilere eşzamanlı olarak erişen işlem düğümlerinin sayısı için gereken performans ve ölçeğe (aktarım hızı ve ıOPS) göre bağımsız olarak seçilebilir ve ölçeklenebilir. Örneğin, [avere vFXT](../avere-vfxt/avere-vfxt-overview.md) tarafından dağıtılan bir bellek içi önbellek, binlerce eşzamanlı işleme düğümü olan büyük hareket resmi ölçeğini desteklemek için kullanılabilir ve şirket içinde bulunan kaynak verilere erişim sağlayabilir. Alternatif olarak, bulut tabanlı blob depolamada zaten bulunan veriler için, [blobsigortası](../storage/blobs/storage-how-to-mount-container-linux.md) bu verileri yerel bir dosya sistemi olarak bağlamak için kullanılabilir. Blobsigortası yalnızca Linux düğümlerinde kullanılabilir, ancak [Azure dosyaları](https://azure.microsoft.com/blog/a-new-era-for-azure-files-bigger-faster-better/) benzer bir iş akışı sağlar ve hem Windows hem de Linux 'ta kullanılabilir.

## <a name="mount-a-virtual-file-system-on-a-pool"></a>Bir havuza sanal dosya sistemi bağlama  

Bir havuza sanal dosya sistemi bağlamak, dosya sisteminin havuzdaki her işlem düğümü için kullanılabilir olmasını sağlar. Dosya sistemi, bir işlem düğümü bir havuza katıldığında veya düğüm yeniden başlatıldığında ya da yeniden görüntülendiğinde yapılandırılır.

Bir havuza dosya sistemi bağlamak için bir `MountConfiguration` nesne oluşturun. Sanal dosya sisteminize uygun olan nesneyi seçin `AzureBlobFileSystemConfiguration`:, `AzureFileShareConfiguration`, `NfsMountConfiguration`veya `CifsMountConfiguration`.

Tüm bağlama yapılandırma nesneleri için aşağıdaki temel Parametreler gereklidir. Bazı bağlama yapılandırmalarında, kullanılan dosya sistemine özgü parametreler bulunur ve bunlar kod örneklerinde daha ayrıntılı olarak ele alınmıştır.

- **Hesap adı veya kaynak**: Sanal bir dosya paylaşımının bağlanması için depolama hesabının veya kaynağının adının olması gerekir.
- **Göreli bağlama yolu veya kaynağı**: `fsmounts` Kullanılarak`AZ_BATCH_NODE_MOUNTS_DIR`düğüm üzerinde erişilebilen standart dizine göre işlem düğümüne takılan dosya sisteminin konumu. Tam konum, düğümde kullanılan işletim sistemine bağlı olarak değişir. Örneğin, bir Ubuntu düğümündeki fiziksel konum öğesine `mnt\batch\tasks\fsmounts`ve `mnt\resources\batch\tasks\fsmounts`eşlendiği bir CentOS düğümüne eşlenir.
- **Bağlama seçenekleri veya blobsigortası seçenekleri**: Bu seçenekler bir dosya sistemi bağlamak için belirli parametreleri anlatmaktadır.

Nesne oluşturulduktan sonra, havuzu oluştururken nesneyi `MountConfigurationList` özelliğe atayın. `MountConfiguration` Dosya sistemi, bir düğüm bir havuza katıldığında veya düğüm yeniden başlatıldığında ya da yeniden oluşturulduğunda bağlanır.

Dosya sistemi bağlandığında, bağlı dosya sistemlerinin yanı sıra, `AZ_BATCH_NODE_MOUNTS_DIR` sorun giderme ve hata ayıklama için yararlı olan günlük dosyalarının konumunu gösteren bir ortam değişkeni oluşturulur. Günlük dosyaları, [bağlama hatalarını Tanıla](#diagnose-mount-errors) bölümünde daha ayrıntılı olarak açıklanmıştır.  

> [!IMPORTANT]
> Bir havuzdaki en fazla bağlı dosya sistemi sayısı 10 ' dur. Ayrıntılar ve diğer sınırlar için bkz. [Batch hizmeti kotaları ve sınırları](batch-quota-limit.md#other-limits) .

## <a name="examples"></a>Örnekler

Aşağıdaki kod örnekleri, çeşitli dosya paylaşımlarının işlem düğümleri havuzuna bağlanmasını göstermektedir.

### <a name="azure-files-share"></a>Azure dosya paylaşma

Azure dosyaları, standart Azure bulut dosya sistemi sunumudur. Bağlama yapılandırma kodu örneğindeki parametrelerden herhangi birini alma hakkında daha fazla bilgi edinmek için bkz. [Azure dosyaları paylaşma kullanma](../storage/files/storage-how-to-use-files-windows.md).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureFileShareConfiguration = new AzureFileShareConfiguration
            {
                AccountName = "AccountName",
                AzureFileUrl = "AzureFileShareUrl",
                AccountKey = "StorageAccountKey",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp"
            },
        }
    }
}
```

### <a name="azure-blob-file-system"></a>Azure blob dosya sistemi

Diğer bir seçenek de [blobsigortası](../storage/blobs/storage-how-to-mount-container-linux.md)aracılığıyla Azure Blob depolama kullanmaktır. BLOB dosya sistemi bağlamak için bir `AccountKey` veya `SasKey` depolama hesabınız olması gerekir. Bu anahtarları alma hakkında daha fazla bilgi için bkz. [Hesap anahtarlarını görüntüleme](../storage/common/storage-account-manage.md#view-account-keys-and-connection-string)veya [paylaşılan ERIŞIM imzaları (SAS) kullanma](../storage/common/storage-dotnet-shared-access-signature-part-1.md). Blobsigortası kullanma hakkında daha fazla bilgi için bkz. blobsigortası [sorun GIDERME SSS](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ). Blobsigortası bağlı dizinine varsayılan erişim sağlamak için, görevi **yönetici**olarak çalıştırın. Blobsigortası, dizini Kullanıcı alanında takar ve havuz oluşturulduğunda kök olarak bağlanır. Linux 'ta tüm **yönetici** görevleri köküdür. SIGORTASı modülü için tüm seçenekler, [Sigorta başvurusu sayfasında](http://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html)açıklanmaktadır.

Sorun giderme kılavuzuna ek olarak, blobsigortası deposundaki GitHub sorunları geçerli blobsigortası sorunlarını ve çözümlerini denetlemek için faydalı bir yoldur. Daha fazla bilgi için bkz. [blobsigortası sorunları](https://github.com/Azure/azure-storage-fuse/issues).

> [!NOTE]
> Blobsigortası Şu anda etkin değil. Daha fazla bilgi için bkz. [desteklenen SKU 'lar](#supported-skus) .

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureBlobFileSystemConfiguration = new AzureBlobFileSystemConfiguration
            {
                AccountName = "StorageAccountName",
                ContainerName = "containerName",
                AccountKey = "StorageAccountKey",
                SasKey = "",
                RelativeMountPath = "RelativeMountPath",
                BlobfuseOptions = "-o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120 "
            },
        }
    }
}
```

### <a name="network-file-system"></a>Ağ dosya sistemi

Ağ dosya sistemleri (NFS), geleneksel dosya sistemlerine Azure Batch düğümlere kolayca erişilmesine izin veren havuz düğümlerine de bağlanabilir. Bu, bulutta dağıtılan tek bir NFS sunucusu ya da bir sanal ağ üzerinden erişilen şirket içi NFS sunucusu olabilir. Alternatif olarak, şirket içi depolamaya sorunsuz bağlantı sağlayan [avere vFXT](../avere-vfxt/avere-vfxt-overview.md) ile dağıtılmış bellek içi önbellek çözümünün avantajlarından yararlanın, önbelleğe verileri isteğe bağlı olarak okur ve bulut tabanlı işlem için yüksek performans ve ölçeklendirme olanağı sunar düğümlerini.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            NfsMountConfiguration = new NFSMountConfiguration
            {
                Source = "source",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "options ver=1.0"
            },
        }
    }
}
```

### <a name="common-internet-file-system"></a>Ortak Internet dosya sistemi

Ortak Internet dosya sistemleri (CIFS), geleneksel dosya sistemlerine Azure Batch düğümlere kolayca erişilmesine izin veren havuz düğümlerine de bağlanabilir. CIFS, ağ sunucusu dosyaları ve hizmetleri istemek için açık ve platformlar arası bir mekanizma sağlayan dosya paylaşım protokolüdür. CIFS, Microsoft 'un Internet ve intranet dosya paylaşımı için sunucu Ileti bloğu (SMB) protokolünün gelişmiş sürümünü temel alır ve Windows düğümlerine dış dosya sistemlerini bağlamak için kullanılır. SMB hakkında daha fazla bilgi edinmek için bkz. [dosya sunucusu ve SMB](https://docs.microsoft.com/windows-server/storage/file-server/file-server-smb-overview).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            CifsMountConfiguration = new CIFSMountConfiguration
            {
                Username = "StorageAccountName",
                RelativeMountPath = "cifsmountpoint",
                Source = "source",
                Password = "StorageAccountKey",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,serverino"
            },
        }
    }
}
```

## <a name="diagnose-mount-errors"></a>Bağlama hatalarını tanılama

Bağlama yapılandırması başarısız olursa, havuzdaki işlem düğümü başarısız olur ve düğüm durumu kullanılamaz hale gelir. Bağlama yapılandırma hatasını tanılamak için, hata hakkındaki ayrıntılar [`ComputeNodeError`](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) için özelliği inceleyin.

Hata ayıklama için günlük dosyalarını almak üzere, `*.log` dosyaları karşıya yüklemek için [OutputFiles](batch-task-output-files.md) kullanın. Dosyalar, `AZ_BATCH_NODE_MOUNTS_DIR` konumdaki dosya sistemi bağlaması hakkında bilgiler içerir. `*.log` Bağlama günlük dosyaları şu biçimdedir: `<type>-<mountDirOrDrive>.log` her bir bağlama için. Örneğin, `cifs` adlı `test` bir bağlama dizininde bulunan bir Mount adlı bir bağlama günlük dosyası olacaktır: `cifs-test.log`.

## <a name="supported-skus"></a>Desteklenen SKU 'Lar

| Yayımcı | Sunduğu | SKU | Azure dosya paylaşma | Blobsigortası | NFS bağlama | CIFS bağlama |
|---|---|---|---|---|---|---|
| toplu iş | işleme-centos73 | çizmeye | :heavy_check_mark: <br>Not: CentOS 7,7 ile uyumlu</br>| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Canonical | UbuntuServer | 16,04-LTS, 18,04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| credativ | Debian | 8, 9 | :heavy_check_mark: | sayı | :heavy_check_mark: | :heavy_check_mark: |
| Microsoft-ads | linux-data-science-vm | linuxdsvm | :heavy_check_mark: <br>Not: CentOS 7,4 ile uyumludur. </br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Microsoft-Azure-Batch | CentOS-kapsayıcı | 7,6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Microsoft-Azure-Batch | CentOS-kapsayıcı-RDMA | 7.4 | :heavy_check_mark: <br>Not: A_8 veya 9 depolamayı destekler</br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Microsoft-Azure-Batch | Ubuntu-Server-Container | 16.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Microsoft-dsvm | Linux-Data-Science-VM-Ubuntu | linuxdsvmubuntu | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS | 7,6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS-HPC | 7,4, 7,3, 7,1 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Oracle | Oracle-Linux | 7,6 | sayı | sayı | sayı | sayı |
| Windows | WindowsServer | 2012, 2016, 2019 | :heavy_check_mark: | sayı | sayı | sayı |

## <a name="next-steps"></a>Sonraki adımlar

- [Windows](../storage/files/storage-how-to-use-files-windows.md) veya [Linux](../storage/files/storage-how-to-use-files-linux.md)ile Azure dosyaları paylaşımının bağlanması hakkında daha fazla bilgi edinin.
- [Blobsigortası](https://github.com/Azure/azure-storage-fuse) sanal dosya sistemlerini kullanma ve bağlama hakkında bilgi edinin.
- NFS ve uygulamaları hakkında bilgi edinmek için bkz. [ağ dosya sistemine genel bakış](https://docs.microsoft.com/windows-server/storage/nfs/nfs-overview) .
- CIFS hakkında daha fazla bilgi için bkz. [MICROSOFT SMB protokolü ve CIFS protokolüne genel bakış](https://docs.microsoft.com/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) .
