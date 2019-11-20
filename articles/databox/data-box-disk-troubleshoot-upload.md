---
title: Azure Data Box Disk sorun giderme verilerini karşıya yükleme günlüklerini kullanarak sorunları | Microsoft Docs
description: Günlükleri ve Azure Data Box Disk için karşıya veri yükleme sırasında görülen sorunlarını nasıl kullanılacağını açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: deaa9a220ee4d765650779b40742225e300ffdb7
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807519"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Azure Data Box Disk verileri karşıya yükleme sorunlarını gidermek için günlükleri anlama

Bu makale, Microsoft Azure Data Box Disk için geçerlidir ve verileri Azure'a karşıya yüklediğinizde gördüğünüz sorunları açıklar.

## <a name="about-upload-logs"></a>Karşıya yükleme günlükleri hakkında

Veri merkezinde Azure'a yüklendiğinde `_error.xml` ve `_verbose.xml` dosyaları için her depolama hesabı oluşturulur. Bu günlükler, verileri karşıya yüklemek için kullanılan aynı depolama hesabına yüklenir. 

Her iki günlük aynı biçimde olduğundan ve Azure depolama hesabına diskten verileri kopyalanırken gerçekleşen olayların XML açıklamaları içerir.

Hata günlüğü yalnızca bloblar veya karşıya yükleme sırasında hatalarla karşılaştı dosyaları bilgisini içerirken ayrıntılı günlüğü her blob veya dosya kopyalama işleminin durumunu ilgili eksiksiz bilgi içerir.

Hata günlüğü, ayrıntılı günlük, ancak filtreler başarılı işlemler aynı yapıda sahiptir.

## <a name="download-logs"></a>İndirme günlükleri

Karşıya yükleme günlüklerini bulmak için aşağıdaki adımları uygulayın.

1. Verileri Azure'a karşıya yüklerken herhangi bir hata varsa portalı tanılama günlüklerinin bulunduğu klasöre bir yol görüntüler.

    ![Portal'da günlüklerini bağlantı](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. Git **waies**.

    ![hata ve ayrıntılı günlükleri](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

Her durumda, hata günlüklerini ve ayrıntılı günlüklere bakın. Her günlük seçin ve yerel bir kopyasını indirin.

## <a name="sample-upload-logs"></a>Örnek günlükleri karşıya yükle

Bir örnek `_verbose.xml` aşağıda gösterilmiştir. Bu durumda, sipariş hatasız başarıyla tamamlandı.

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

Aynı sırada, bir örnek için `_error.xml` aşağıda gösterilmiştir.

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

Bir örnek `_error.xml` nerede gösterilen siparişin hatalarla tamamlandı. 

Bu durumda hata dosyasına sahip bir `Summary` bölümü ve tüm dosya içeren başka bir bölüme düzeyi hataları. 

`Summary` İçeren `ValidationErrors` ve `CopyErrors`. Bu durumda, 8 dosyaları veya klasörleri Azure'a karşıya yüklendi ve doğrulama hataları vardı. Verileri Azure depolama hesabına kopyalandığında, 5 dosyaları veya klasörleri başarıyla karşıya yüklendi. Kalan 3 dosyaları veya klasörleri Azure kapsayıcı adlandırma kurallarına uygun olarak yeniden adlandırıldı ve Azure'a başarıyla karşıya yüklendi.

Dosya düzeyi durumunu bulunduğunuz `BlobStatus` blobları karşıya yüklemek için gerçekleştirilen tüm eylemleri açıklar. Bu durumda, verilerin kopyalanacağı klasörler ile kapsayıcılar için Azure adlandırma kurallarına uymuyor çünkü üç kapsayıcı olarak adlandırılır. İçinde Bu kapsayıcılar karşıya yüklenen bloblara için yeni bir kapsayıcı adı, Azure blob yolu, özgün geçersiz dosya yolu ve blob boyutu dahil edilir.
    
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

## <a name="data-upload-errors"></a>Verileri karşıya yükleme hataları

Verileri Azure'a karşıya yüklenirken oluşturulan hatalar aşağıdaki tabloda özetlenmiştir.

| Hata kodu | Açıklama                   |
|-------------|------------------------------|
|`None` |  Başarıyla tamamlandı.           |
|`Renamed` | Başarılı bir şekilde blob'olarak yeniden adlandırıldı.   |
|`CompletedWithErrors` | Karşıya yükleme hatalarla tamamlandı. Dosyaların hata ayrıntıları günlük dosyasında dahil edilir.  |
|`Corrupted`|Veri alımı sırasında hesaplanan CRC karşıya yükleme sırasında hesaplanan CRC'yle eşleşmiyor.  |  
|`StorageRequestFailed` | Azure depolama isteği başarısız oldu.   |     
|`LeasePresent` | Bu öğe kiralanmış ve başka bir kullanıcı tarafından kullanılıyor. |
|`StorageRequestForbidden` |Kimlik doğrulama sorunları nedeniyle karşıya yüklenemedi. |
|`ManagedDiskCreationTerminalFailure` | Yönetilen diskler olarak karşıya yüklenemedi. Dosyaları hazırlama depolama hesabında sayfa blobları kullanılabilir. Bu gibi durumlarda, sayfa BLOB'ları el ile yönetilen disklere dönüştürebilirsiniz.  |
|`DiskConversionNotStartedTierInfoMissing` | Yönetilen disk, VHD dosyasının dışında precreated katmanı klasörleri kopyaladığınızdan oluşturulmadı. Dosya varsayılan olarak, sipariş oluşturma sırasında belirtilen Hazırlama depolama hesabına sayfa blobu olarak yüklenir. Bunu el ile bir yönetilen diskin dönüştürebilirsiniz.|
|`InvalidWorkitem` | Azure adlandırma için uygun değil ve kuralları sınırlar verileri karşıya yüklenemedi.|
|`InvalidPageBlobUploadAsBlockBlob` | Ön eki içeren bir kapsayıcıya blok blobları olarak karşıya `databoxdisk-invalid-pb-`.|
|`InvalidAzureFileUploadAsBlockBlob` | Ön eki içeren bir kapsayıcıya blok blobları olarak karşıya `databoxdisk-invalid-af`-.|
|`InvalidManagedDiskUploadAsBlockBlob` | Ön eki içeren bir kapsayıcıya blok blobları olarak karşıya `databoxdisk-invalid-md`-.|
|`InvalidManagedDiskUploadAsPageBlob` |Sayfa blobları bir kapsayıcıda ön eki olarak karşıya `databoxdisk-invalid-md-`. |
|`MovedToOverflowShare` |Yüklenen dosyalar için yeni bir paylaşım özgün paylaşım boyutu olarak maksimum Azure boyut sınırını aştı. Yeni dosya paylaşımı adı ile ve sonra özgün adına sahip `-2`.   |
|`MovedToDefaultAzureShare` |Varsayılan Paylaşım herhangi bir klasöre bir parçası olmayan yüklenen dosyalar. Paylaşım adı ile başlayan `databox-`. |
|`ContainerRenamed` |Bu dosyalar için kapsayıcı Azure adlandırma kurallarına uygun olmadı ve yeniden adlandırılır. Yeni adı ile başlayan `databox-` ve özgün adı SHA1 karması ile soneki |
|`ShareRenamed` |Paylaşım için bu dosyalar Azure adlandırma kurallarına uygun olmadı ve adlandırılır. Yeni adı ile başlayan `databox-` ve özgün adı SHA1 karması ile soneki. |
|`BlobRenamed` |Bu dosyalar Azure adlandırma kurallarına uygun olmadı ve değiştirilmiştir. Denetleme `BlobPath` yeni ad alanı. |
|`FileRenamed` |Bu dosyalar Azure adlandırma kurallarına uygun olmadı ve değiştirilmiştir. Denetleme `FileStoragePath` yeni ad alanı. |
|`DiskRenamed` |Bu dosyalar Azure adlandırma kurallarına uygun olmadı ve değiştirilmiştir. Denetleme `BlobPath` yeni ad alanı. |


## <a name="next-steps"></a>Sonraki adımlar

- [Data Box Disk sorunları için bir destek bileti](data-box-disk-contact-microsoft-support.md).
