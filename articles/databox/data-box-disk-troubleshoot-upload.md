---
title: Günlükleri kullanarak karşıya veri yükleme sorunlarını giderme
titleSuffix: Azure Data Box Disk
description: Günlüklerin nasıl kullanılacağını ve Azure Data Box Disk verileri karşıya yüklerken görülen sorunları nasıl giderebileceğinizi açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: 4b53cf607bdf60c785c7324d9ede526a0983b7e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97605284"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Azure Data Box Disk veri yükleme sorunlarını gidermek için günlükleri anlama

Bu makale, Microsoft Azure Data Box Disk için geçerlidir ve Azure 'a veri yüklerken gördüğünüz sorunları açıklar.

## <a name="about-upload-logs"></a>Günlükleri karşıya yükleme hakkında

Veriler veri merkezinde Azure 'a yüklendiğinde `_error.xml` ve `_verbose.xml` her depolama hesabı için dosyalar oluşturulur. Bu Günlükler, verileri karşıya yüklemek için kullanılan depolama hesabına yüklenir. 

Her iki günlük de aynı biçimde ve verileri diskten Azure depolama hesabına kopyalarken oluşan olayların XML açıklamalarını içerir.

Ayrıntılı günlük, her blob veya dosya için kopyalama işleminin durumuyla ilgili tüm bilgileri içerir, ancak hata günlüğü yalnızca blob veya karşıya yükleme sırasında hatalarla karşılaşan dosyalar için bilgileri içerir.

Hata günlüğü, ayrıntılı günlük ile aynı yapıya sahip, ancak başarılı işlemleri filtreliyor.

## <a name="download-logs"></a>İndirme günlükleri

Karşıya yükleme günlüklerini bulmak için aşağıdaki adımları uygulayın.

1. Verileri Azure 'a yüklerken bir hata oluşursa, Portal tanılama günlüklerinin bulunduğu klasörün yolunu görüntüler.

    ![Portalda günlüklere bağlantı](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. **Gmallara** gidin.

    ![hata ve ayrıntılı Günlükler](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

Her durumda, hata günlüklerini ve ayrıntılı günlükleri görürsünüz. Her günlüğü seçin ve Yerel kopyayı indirin.

## <a name="sample-upload-logs"></a>Örnek karşıya yükleme günlükleri

Bir örneği `_verbose.xml` aşağıda gösterilmiştir. Bu durumda, sipariş hatasız olarak başarıyla tamamlanır.

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Blob Status="Completed">
    <BlobPath>$root/botetapageblob.vhd</BlobPath>
    <FilePath>\PageBlob\botetapageblob.vhd</FilePath>
    <Length>1073742336</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <PageRangeList>
      <PageRange Offset="0" Length="4194304" Status="Completed" />
      <PageRange Offset="4194304" Length="4194304" Status="Completed" />
      <PageRange Offset="8388608" Length="4194304" Status="Completed" />
      --------CUT-------------------------------------------------------
      <PageRange Offset="1061158912" Length="4194304" Status="Completed" />
      <PageRange Offset="1065353216" Length="4194304" Status="Completed" />
      <PageRange Offset="1069547520" Length="4194304" Status="Completed" />
      <PageRange Offset="1073741824" Length="512" Status="Completed" />
    </PageRangeList>
  </Blob>
  <Blob Status="Completed">
    <BlobPath>$root/botetablockblob.txt</BlobPath>
    <FilePath>\BlockBlob\botetablockblob.txt</FilePath>
    <Length>19</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <BlockList>
      <Block Offset="0" Length="19" Status="Completed" />
    </BlockList>
  </Blob>
  <File Status="Completed">
    <FileStoragePath>botetaazurefilesfolder/botetaazurefiles.txt</FileStoragePath>
    <FilePath>\AzureFile\botetaazurefilesfolder\botetaazurefiles.txt</FilePath>
    <Length>20</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <FileRangeList>
      <FileRange Offset="0" Length="20" Status="Completed" />
    </FileRangeList>
  </File>
  <Status>Completed</Status>
</DriveLog>
```

Aynı sırada bir örneği `_error.xml` aşağıda gösterilmiştir.

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Summary>
    <ValidationErrors>
      <None Count="3" />
    </ValidationErrors>
    <CopyErrors>
      <None Count="3" Description="No errors encountered" />
    </CopyErrors>
  </Summary>
  <Status>Completed</Status>
</DriveLog>
```

`_error.xml`Aşağıda, siparişin hatalarla tamamlandığı yerde bir örneği gösterilmiştir. 

Bu örnekte hata dosyası, `Summary` tüm dosya düzeyi hatalarını içeren bir bölümü ve başka bir bölümü vardır. 

, `Summary` Ve içerir `ValidationErrors` `CopyErrors` . Bu durumda, 8 dosya veya klasör Azure 'a yüklenmiş ve doğrulama hatası yoktu. Veriler Azure Storage hesabına kopyalanırken, 5 dosya veya klasör başarıyla karşıya yüklenir. Geri kalan 3 dosya veya klasör, Azure Container adlandırma kurallarına göre yeniden adlandırıldı ve Azure 'a başarıyla yüklendi.

Dosya düzeyi durumu, `BlobStatus` Blobları karşıya yüklemek için gerçekleştirilen eylemleri açıklar. Bu durumda, verilerin kopyalandığı klasörler kapsayıcılar için Azure adlandırma kurallarıyla uyumlu olmadığı için üç kapsayıcı yeniden adlandırılır. Bu kapsayıcılara yüklenen Bloblar için, yeni kapsayıcı adı, Azure 'daki Blobun yolu, özgün geçersiz dosya yolu ve BLOB boyutu dahildir.
  
```xml
 <?xml version="1.0" encoding="utf-8"?>
  <DriveLog Version="2018-10-01">
    <DriveId>18041C582D7E</DriveId>
    <Summary>
     <!--Summary for validation and data copy to Azure -->
      <ValidationErrors>
        <None Count="8" />
      </ValidationErrors>
      <CopyErrors>
        <Completed Count="5" Description="No errors encountered" />
        <ContainerRenamed Count="3" Description="Renamed the container as the original container name does not follow Azure conventions." />
      </CopyErrors>
    </Summary>
    <!--List of renamed containers with the new names, new file path in Azure, original invalid file path, and size -->
    <Blob Status="ContainerRenamed">
      <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/private.vhd</BlobPath>
      <OriginalFilePath>\PageBlob\pageblob test\private.vhd</OriginalFilePath>
      <SizeInBytes>10490880</SizeInBytes>
    </Blob>
    <Blob Status="ContainerRenamed">
      <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/resource.vhd</BlobPath>
      <OriginalFilePath>\PageBlob\pageblob test\resource.vhd</OriginalFilePath>
      <SizeInBytes>71528448</SizeInBytes>
    </Blob>
    <Blob Status="ContainerRenamed">
      <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/role.vhd</BlobPath>
      <OriginalFilePath>\PageBlob\pageblob test\role.vhd</OriginalFilePath>
      <SizeInBytes>10490880</SizeInBytes>
    </Blob>
    <Status>CompletedWithErrors</Status>
  </DriveLog>
```

## <a name="data-upload-errors"></a>Karşıya veri yükleme hataları

Verileri Azure 'a yüklerken oluşturulan hatalar aşağıdaki tabloda özetlenmiştir.

| Hata kodu | Description                   |
|-------------|------------------------------|
|`None` |  Başarıyla tamamlandı.           |
|`Renamed` | Blob başarıyla yeniden adlandırıldı.   |
|`CompletedWithErrors` | Karşıya yükleme hatalarla tamamlandı. Hata içindeki dosyaların ayrıntıları günlük dosyasına dahil edilir.  |
|`Corrupted`|Veri alma sırasında hesaplanan CRC, karşıya yükleme sırasında hesaplanan CRC ile eşleşmiyor.  |  
|`StorageRequestFailed` | Azure depolama isteği başarısız oldu.   |     
|`LeasePresent` | Bu öğe kiralanır ve başka bir kullanıcı tarafından kullanılıyor. |
|`StorageRequestForbidden` |Kimlik doğrulama sorunları nedeniyle karşıya yüklenemedi. |
|`ManagedDiskCreationTerminalFailure` | Yönetilen diskler olarak karşıya yüklenemedi. Dosyalar, hazırlama depolama hesabında sayfa Blobları olarak kullanılabilir. Sayfa bloblarını yönetilen disklere el ile dönüştürebilirsiniz.  |
|`DiskConversionNotStartedTierInfoMissing` | VHD dosyası, önceden oluşturulmuş katman klasörlerinin dışına kopyalandığı için, yönetilen bir disk oluşturulmadı. Dosya, sipariş oluşturma sırasında belirtildiği gibi, hazırlama depolama hesabına Sayfa Blobu olarak yüklenir. Bunu yönetilen diske el ile dönüştürebilirsiniz.|
|`InvalidWorkitem` | Azure adlandırma ve limit kurallarına uygun olmadığından veriler karşıya yüklenemedi.|
|`InvalidPageBlobUploadAsBlockBlob` | Ön ekine sahip bir kapsayıcıda blok Blobları olarak karşıya yüklendi `databoxdisk-invalid-pb-` .|
|`InvalidAzureFileUploadAsBlockBlob` | Ön eki olan bir kapsayıcıda blok Blobları olarak karşıya yüklendi `databoxdisk-invalid-af` .|
|`InvalidManagedDiskUploadAsBlockBlob` | Ön eki olan bir kapsayıcıda blok Blobları olarak karşıya yüklendi `databoxdisk-invalid-md` .|
|`InvalidManagedDiskUploadAsPageBlob` |Ön ekine sahip bir kapsayıcıda sayfa Blobları olarak karşıya yüklendi `databoxdisk-invalid-md-` . |
|`MovedToOverflowShare` |Özgün paylaşımın boyutu en fazla Azure boyut sınırını aştığından dosya yeni bir paylaşıma yüklendi. Yeni dosya paylaşımının adının özgün adı sonekli olarak düzeltildi `-2` .   |
|`MovedToDefaultAzureShare` |Herhangi bir klasörün parçası olmayan dosyalar varsayılan bir paylaşıma yüklendi. Paylaşma adı ile başlar `databox-` . |
|`ContainerRenamed` |Bu dosyaların kapsayıcısı Azure adlandırma kurallarına uymuyor ve yeniden adlandırıldı. Yeni ad ile başlar `databox-` ve özgün ADıN SHA1 karması ile sonekli olur |
|`ShareRenamed` |Bu dosyaların paylaşılması Azure adlandırma kurallarına uymuyor ve yeniden adlandırıldı. Yeni ad ile başlar `databox-` ve özgün ADıN SHA1 karması ile son olarak sabitlenir. |
|`BlobRenamed` |Bu dosyalar Azure adlandırma kurallarına uygun değildi ve yeniden adlandırıldı. `BlobPath`Yeni adın alanını denetleyin. |
|`FileRenamed` |Bu dosyalar Azure adlandırma kurallarına uygun değildi ve yeniden adlandırıldı. `FileStoragePath`Yeni adın alanını denetleyin. |
|`DiskRenamed` |Bu dosyalar Azure adlandırma kurallarına uygun değildi ve yeniden adlandırıldı. `BlobPath`Yeni adın alanını denetleyin. |


## <a name="next-steps"></a>Sonraki adımlar

- [Data Box disk sorunları için bir destek bileti açın](data-box-disk-contact-microsoft-support.md).
