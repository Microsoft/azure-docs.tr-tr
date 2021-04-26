---
title: Bir havuza sanal dosya sistemi bağlama
description: Bir Batch havuzunda sanal dosya sistemi bağlama hakkında bilgi edinin.
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 03/26/2021
ms.openlocfilehash: dcd56a12d8728b83cdcb7cea4c16c4aedd4251a7
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105757"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>Bir Batch havuzunda sanal dosya sistemi bağlama

Azure Batch, toplu Iş havuzlarınızdaki Windows veya Linux işlem düğümlerinde bulut depolama veya harici dosya sistemi bağlamayı destekler. Bir işlem düğümü bir havuza katıldığında, sanal dosya sistemi bağlanır ve bu düğümde yerel bir sürücü olarak değerlendirilir. Azure dosyaları, Azure Blob depolama, ağ dosya sistemi (NFS) gibi bir [avere vFXT önbelleği](../avere-vfxt/avere-vfxt-overview.md)veya ortak Internet dosya SISTEMI (CIFS) gibi dosya sistemlerini bağlayabilirsiniz.

Bu makalede, [.net Için Batch Yönetim Kitaplığı](/dotnet/api/overview/azure/batch)'nı kullanarak bir işlem düğümleri havuzuna bir sanal dosya sistemi bağlama hakkında bilgi edineceksiniz.

> [!NOTE]
> Bir sanal dosya sistemi bağlama yalnızca 8 Ağustos 2019 ' de veya sonrasında oluşturulan Batch havuzlarında desteklenir. Bu tarihten önce oluşturulan toplu iş havuzları, bu özelliği desteklemezler.

## <a name="benefits-of-mounting-on-a-pool"></a>Bir havuza bağlama avantajları

Dosya sistemini havuza bağlama, görevlerin büyük bir veri kümesinden kendi verilerini almasına izin vermek yerine, görevlerin gerekli verilere erişmesi daha kolay ve daha verimli hale gelir.

Bir filmi işleme gibi ortak bir veri kümesine erişmesi gereken birden çok görevle bir senaryo düşünün. Her görev, sahne dosyalarından bir seferde bir veya daha fazla kare oluşturur. Sahne dosyalarını içeren bir sürücü bağlayarak, işlem düğümlerinin paylaşılan verilere erişmesi daha kolay olur.

Ayrıca, temel alınan dosya sistemi, verilere eşzamanlı olarak erişen işlem düğümlerinin sayısı için gereken performans ve ölçeğe (aktarım hızı ve ıOPS) göre bağımsız olarak seçilebilir ve ölçeklenebilir. Örneğin, [avere vFXT](../avere-vfxt/avere-vfxt-overview.md) tarafından dağıtılan bir bellek içi önbellek, binlerce eşzamanlı işleme düğümü olan büyük hareket resmi ölçeğini desteklemek için kullanılabilir ve şirket içinde bulunan kaynak verilere erişim sağlayabilir. Alternatif olarak, bulut tabanlı blob depolamada zaten bulunan veriler için, [blobsigortası](../storage/blobs/storage-how-to-mount-container-linux.md) bu verileri yerel bir dosya sistemi olarak bağlamak için kullanılabilir. Blobsigortası yalnızca Linux düğümlerinde kullanılabilir, ancak [Azure dosyaları](../storage/files/storage-files-introduction.md) benzer bir iş akışı sağlar ve hem Windows hem de Linux 'ta kullanılabilir.

## <a name="mount-a-virtual-file-system-on-a-pool"></a>Bir havuza sanal dosya sistemi bağlama  

Bir havuza sanal dosya sistemi bağlamak, dosya sisteminin havuzdaki her işlem düğümü için kullanılabilir olmasını sağlar. Dosya sistemi, bir işlem düğümü bir havuza katıldığında veya düğüm yeniden başlatıldığında ya da yeniden görüntülendiğinde yapılandırılır.

Bir havuza dosya sistemi bağlamak için bir `MountConfiguration` nesne oluşturun. Sanal dosya sisteminize uygun olan nesneyi seçin: `AzureBlobFileSystemConfiguration` , `AzureFileShareConfiguration` , `NfsMountConfiguration` veya `CifsMountConfiguration` .

Tüm bağlama yapılandırma nesneleri için aşağıdaki temel Parametreler gereklidir. Bazı bağlama yapılandırmalarında, kullanılan dosya sistemine özgü parametreler bulunur ve bunlar kod örneklerinde daha ayrıntılı olarak ele alınmıştır.

- **Hesap adı veya kaynak**: sanal bir dosya paylaşımının bağlanması için depolama hesabının veya kaynağının adına sahip olmanız gerekir.
- **Göreli bağlama yolu veya kaynağı**: işlem düğümüne bağlı dosya sisteminin, `fsmounts` aracılığıyla düğüm üzerinde erişilebilen standart dizine göre konumu `AZ_BATCH_NODE_MOUNTS_DIR` . Tam konum, düğümde kullanılan işletim sistemine bağlı olarak değişir. Örneğin, bir Ubuntu düğümündeki fiziksel konum öğesine `mnt\batch\tasks\fsmounts` ve eşlendiği bir CentOS düğümüne eşlenir `mnt\resources\batch\tasks\fsmounts` .
- **Bağlama seçenekleri veya blobsigortası seçenekleri**: Bu seçenekler bir dosya sistemi bağlamak için belirli parametreleri anlatmaktadır.

`MountConfiguration`Nesne oluşturulduktan sonra, `MountConfigurationList` havuzu oluştururken nesneyi özelliğe atayın. Dosya sistemi, bir düğüm bir havuza katıldığında veya düğüm yeniden başlatıldığında ya da yeniden oluşturulduğunda bağlanır.

Dosya sistemi bağlandığında, `AZ_BATCH_NODE_MOUNTS_DIR` bağlı dosya sistemlerinin yanı sıra, sorun giderme ve hata ayıklama için yararlı olan günlük dosyalarının konumunu gösteren bir ortam değişkeni oluşturulur. Günlük dosyaları, [bağlama hatalarını Tanıla](#diagnose-mount-errors) bölümünde daha ayrıntılı olarak açıklanmıştır.  

> [!IMPORTANT]
> Bir havuzdaki en fazla bağlı dosya sistemi sayısı 10 ' dur. Ayrıntılar ve diğer sınırlar için bkz. [Batch hizmeti kotaları ve sınırları](batch-quota-limit.md#other-limits) .

## <a name="examples"></a>Örnekler

Aşağıdaki kod örnekleri, çeşitli dosya paylaşımlarının işlem düğümleri havuzuna bağlanmasını göstermektedir.

### <a name="azure-files-share"></a>Azure dosya paylaşma

Azure dosyaları, standart Azure bulut dosya sistemi sunumudur. Bağlama yapılandırma kodu örneğindeki parametrelerden herhangi birini alma hakkında daha fazla bilgi edinmek için bkz. [Azure dosyaları paylaşma-SMB kullanma](../storage/files/storage-how-to-use-files-windows.md) veya [Azure dosyaları paylaşma Ile-NFS kullanma](../storage/files/storage-files-how-to-create-nfs-shares.md).

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
                AccountName = "{storage-account-name}",
                AzureFileUrl = "https://{storage-account-name}.file.core.windows.net/{file-share-name}",
                AccountKey = "{storage-account-key}",
                RelativeMountPath = "S",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp"
            },
        }
    }
}
```

### <a name="azure-blob-file-system"></a>Azure blob dosya sistemi

Diğer bir seçenek de [blobsigortası](../storage/blobs/storage-how-to-mount-container-linux.md)aracılığıyla Azure Blob depolama kullanmaktır. BLOB dosya sistemi bağlamak `AccountKey` için bir veya `SasKey` depolama hesabınız olması gerekir. Bu anahtarları alma hakkında daha fazla bilgi için bkz. [depolama hesabı erişim anahtarlarını yönetme](../storage/common/storage-account-keys-manage.md) veya [paylaşılan ERIŞIM imzaları (SAS) kullanarak Azure depolama kaynaklarına sınırlı erişim verme](../storage/common/storage-sas-overview.md). Blobsigortası kullanma hakkında daha fazla bilgi ve ipucu için bkz. blobsigortası.

Blobsigortası bağlı dizinine varsayılan erişim sağlamak için, görevi **yönetici** olarak çalıştırın. Blobsigortası, dizini Kullanıcı alanında takar ve havuz oluşturulduğunda kök olarak bağlanır. Linux 'ta tüm **yönetici** görevleri köküdür. SIGORTASı modülü için tüm seçenekler, [Sigorta başvurusu sayfasında](https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html)açıklanmıştır.

Blobsigortası kullanma hakkında daha fazla bilgi ve ipucu için [sorun giderme hakkında SSS bölümüne](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ) bakın. Ayrıca, geçerli blobsigortası sorunlarını ve çözümlerini denetlemek için [blobsigortası deposundaki GitHub sorunlarını](https://github.com/Azure/azure-storage-fuse/issues) gözden geçirebilirsiniz.

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

Ağ dosya sistemleri (NFS) havuz düğümlerine bağlanarak geleneksel dosya sistemlerine Azure Batch tarafından erişilmesine izin verebilir. Bu, bulutta dağıtılan tek bir NFS sunucusu ya da bir sanal ağ üzerinden erişilen şirket içi NFS sunucusu olabilir. Alternatif olarak, veri yoğun yüksek performanslı bilgi işlem (HPC) görevleri için [avere vFXT](../avere-vfxt/avere-vfxt-overview.md) dağıtılmış bellek içi önbellek çözümünü kullanabilirsiniz.

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

Havuz düğümlerine [ortak Internet dosya sistemleri (CIFS)](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) bağlamak, geleneksel dosya sistemlerine erişim sağlamanın başka bir yoludur. CIFS, ağ sunucusu dosyaları ve hizmetleri istemek için açık ve platformlar arası bir mekanizma sağlayan dosya paylaşım protokolüdür. CIFS, Internet ve intranet dosya paylaşımı için [sunucu Ileti bloğu (SMB)](/windows-server/storage/file-server/file-server-smb-overview) protokolünün gelişmiş sürümünü temel alır ve Windows düğümlerine dış dosya sistemlerini bağlamak için kullanılabilir.

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

Bağlama yapılandırması başarısız olursa, havuzdaki işlem düğümü başarısız olur ve düğüm durumu olarak ayarlanır `unusable` . Bağlama yapılandırma hatasını tanılamak için, [`ComputeNodeError`](/rest/api/batchservice/computenode/get#computenodeerror) hata hakkındaki ayrıntılar için özelliği inceleyin.

Hata ayıklama için günlük dosyalarını almak üzere, dosyaları karşıya yüklemek için [OutputFiles](batch-task-output-files.md) kullanın `*.log` . `*.log`Dosyalar, konumdaki dosya sistemi bağlaması hakkında bilgiler içerir `AZ_BATCH_NODE_MOUNTS_DIR` . Bağlama günlük dosyaları şu biçimdedir: `<type>-<mountDirOrDrive>.log` her bir bağlama için. Örneğin, adlı bir bağlama dizininde bulunan bir `cifs` Mount adlı bir bağlama `test` günlük dosyası olacaktır: `cifs-test.log` .

## <a name="supported-skus"></a>Desteklenen SKU 'Lar

| Publisher | Sunduğu | SKU | Azure dosya paylaşma | Blobsigortası | NFS bağlama | CIFS bağlama |
|---|---|---|---|---|---|---|
| toplu iş | işleme-centos73 | çizmeye | :heavy_check_mark: <br>Note: CentOS 7,7 ile uyumlu</br>| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Canonical | UbuntuServer | 16,04-LTS, 18,04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Credavtiv | Debian | 8| :heavy_check_mark: | sayı | :heavy_check_mark: | :heavy_check_mark: |
| Credavtiv | Debian | 9 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Microsoft-ads | Linux-Data-Science-VM | linuxdsvm | :heavy_check_mark: <br>Note: CentOS 7,4 ile uyumludur. </br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Microsoft-Azure-Batch | CentOS-kapsayıcı | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Microsoft-Azure-Batch | CentOS-kapsayıcı-RDMA | 7.4 | :heavy_check_mark: <br>Note: A_8 veya 9 depolamayı destekler</br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Microsoft-Azure-Batch | Ubuntu-Server-Container | 16.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Microsoft-dsvm | Linux-Data-Science-VM-Ubuntu | linuxdsvmubuntu | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS-HPC | 7,4, 7,3, 7,1 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Oracle | Oracle-Linux | 7.6 | sayı | sayı | sayı | sayı |
| Windows | WindowsServer | 2012, 2016, 2019 | :heavy_check_mark: | sayı | sayı | sayı |

## <a name="networking-requirements"></a>Ağ gereksinimleri

Sanal [bir ağda Azure Batch havuzlarla](batch-virtual-network.md)sanal dosya bağlama kullanırken, aşağıdaki gereksinimleri göz önünde bulundurun ve gerekli trafiğin engellenmediğinden emin olun.

- **Azure dosyaları**:
  - "Depolama" hizmet etiketine giden/giden trafik için TCP bağlantı noktası 445 ' ün açılmasını gerektirir. Daha fazla bilgi için bkz. [Windows Ile Azure dosya paylaşma kullanma](../storage/files/storage-how-to-use-files-windows.md#prerequisites).
- **Blobsigortası**:
  - "Depolama" hizmet etiketine giden/giden trafik için TCP bağlantı noktası 443 ' ün açılmasını gerektirir.
  - https://packages.microsoft.comBlobsigortası ve GPG paketlerini indirmek Için VM 'lere erişimi olmalıdır. Yapılandırmanıza bağlı olarak, ek paketleri indirmek için diğer URL 'Lere da erişmeniz gerekebilir.
- **Ağ dosya sistemi (NFS)**:
  - Bağlantı noktası 2049 ' e erişim gerektirir (varsayılan olarak, yapılandırmanız başka gereksinimlere sahip olabilir).
  - NFS-Common (de, veya Ubuntu) veya NFS-utils (CentOS için) paketini indirmek için VM 'Lerin uygun paket yöneticisine erişimi olmalıdır. Bu URL, işletim sistemi sürümünüze bağlı olarak değişebilir. Yapılandırmanıza bağlı olarak, ek paketleri indirmek için diğer URL 'Lere da erişmeniz gerekebilir.
- **Ortak Internet dosya sistemi (CIFS)**:
  - TCP bağlantı noktası 445 için erişim gerektirir.
  - VM 'Ler, CIFS-utils paketini indirmek için uygun Paket Yöneticisi 'ne erişebilmelidir. Bu URL, işletim sistemi sürümünüze bağlı olarak değişebilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Windows](../storage/files/storage-how-to-use-files-windows.md) veya [Linux](../storage/files/storage-how-to-use-files-linux.md)ile Azure dosyaları paylaşma bağlama hakkında daha fazla bilgi edinin.
- [Blobsigortası](https://github.com/Azure/azure-storage-fuse) sanal dosya sistemlerini kullanma ve bağlama hakkında bilgi edinin.
- NFS ve uygulamaları hakkında bilgi edinmek için bkz. [ağ dosya sistemine genel bakış](/windows-server/storage/nfs/nfs-overview) .
- CIFS hakkında daha fazla bilgi için bkz. [MICROSOFT SMB protokolü ve CIFS protokolüne genel bakış](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) .
