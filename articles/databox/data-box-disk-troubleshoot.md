---
title: Azure Data Box Disk sorunlarını giderme | Microsoft Docs
description: Azure Data Box Disk sorunlarının nasıl giderileceği anlatılmaktadır.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: f8116ec0836623adf803991017950ddc7f960923
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67805719"
---
# <a name="use-logs-to-troubleshoot-validation-issues-in-azure-data-box-disk"></a>Azure Data Box Disk doğrulama sorunları gidermek için günlükleri kullanma

Bu makale, Microsoft Azure Data Box Disk için geçerlidir. Makale, bu çözümü dağıttığınızda izleyebildiler doğrulama sorunlarını gidermek için günlükleri kullanmayı açıklar.

## <a name="validation-tool-log-files"></a>Doğrulama Aracı günlük dosyaları

Kullanarak disk üzerindeki verileri doğrulamak zaman [doğrulama aracını](data-box-disk-deploy-copy-data.md#validate-data)e *error.xml* tüm hataları günlüğe kaydetmek için oluşturulur. Günlük dosyası bulunan `Drive:\DataBoxDiskImport\logs` sürücünüzün klasör. Doğrulama çalıştırdığınızda hata günlüğüne bir bağlantı sağlanır.

<!--![Validation tool with link to error log](media/data-box-disk-troubleshoot/validation-tool-link-error-log.png)-->

Ardından, doğrulama için birden çok oturumu çalıştırdığınızda bir hata günlüğü oturum başına oluşturulur.

- İşte bir örnek için hata günlüğünü veri ile yüklendiğinde `PageBlob` klasör 512 bayt hizalı değil. PageBlob için yüklenmiş herhangi bir veri 512-hizalanmış, bayt VHD veya VHDX gibi olması gerekir. Bu dosyadaki hataları bulundukları `<Errors>` ve Uyarıları `<Warnings>`.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
            <SessionId>session#1</SessionId>
            <ItemType>PageBlob</ItemType>
            <SourceDirectory>D:\Dataset\TestDirectory</SourceDirectory>
            <Errors>
                <Error Code="Not512Aligned">
                    <Description>The file is not 512 bytes aligned.</Description>
                    <List>
                        <File Path="\Practice\myScript.ps1" />
                    </List>
                    <Count>1</Count>
                </Error>
            </Errors>
            <Warnings />
        </ErrorLog>
    ```

- Kapsayıcı adı geçerli değil, hata günlüğü örneği aşağıdadır. Altında oluşturduğunuz klasöre `BlockBlob`, `PageBlob`, veya `AzureFile` diskteki klasör Azure depolama hesabınızdaki kapsayıcı haline gelir. Kapsayıcının adı izlemelidir [Azure adlandırma kurallarına](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions).

    ```xml
        <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
          <SessionId>bbsession</SessionId>
          <ItemType>BlockBlob</ItemType>
          <SourceDirectory>E:\BlockBlob</SourceDirectory>
          <Errors>
            <Error Code="InvalidShareContainerFormat">
              <List>
                <Container Name="Azu-reFile" />
                <Container Name="bbcont ainer1" />
              </List>
              <Count>2</Count>
            </Error>
          </Errors>
          <Warnings />
    </ErrorLog>
    ```

## <a name="validation-tool-errors"></a>Doğrulama Aracı hataları

Bulunan hataları *error.xml* önerilen eylemler ilgili olan aşağıdaki tabloda özetlenmiştir.

| Hata kodu| Açıklama                       | Önerilen Eylemler               |
|------------|--------------------------|-----------------------------------|
| `None` | Verileri başarıyla doğrulandı. | İşlem yapmanız gerekmez. |
| `InvalidXmlCharsInPath` |Dosya yolu geçerli olmayan karakterler içerdiğinden bir bildirim dosyası oluşturulamadı. | Devam etmek için bu karakterleri kaldırın.  |
| `OpenFileForReadFailed`| Dosya işlenemedi. Bu, bir erişim sorunu veya dosya sistemi bozulması nedeniyle olabilir.|Bir hata nedeniyle dosya okunamadı. Özel durumda hata ayrıntılar bulunur. |
| `Not512Aligned` | Bu dosya PageBlob klasör için geçerli bir biçimde değil.| 512 bayt yalnızca karşıya yüklenen veriler hizalanan `PageBlob` klasör. PageBlob klasöründen dosyayı kaldırın veya BlockBlob klasörüne taşıyın. Doğrulamayı yeniden deneyin.|
| `InvalidBlobPath` | Dosya yolu, Azure Blob adlandırma kuralları uyarınca bulutta geçerli blob yola eşlemiyor.|Dosya yolu yeniden adlandırmak için Azure adlandırma yönergeleri izleyin. |
| `EnumerationError` | Doğrulama için dosya numaralandıramadı. |Bu hata için birden çok nedeni olabilir. En olası sebep, dosyanın erişimdir. |
| `ShareSizeExceeded` | Bu dosyayı Azure dosya paylaşımı boyutu 5 TB'lık Azure sınırını aşmasına neden oldu.|Böylece uyacağını paylaşımdaki verilere boyutunu [Azure nesne boyutu sınırları](data-box-disk-limits.md#azure-object-size-limits). Doğrulamayı yeniden deneyin. |
| `AzureFileSizeExceeded` | Azure dosya boyutu sınırları dosya boyutunu aşıyor.| Böylece uyacağını dosya veya veri boyutunu küçültmek [Azure nesne boyutu sınırları](data-box-disk-limits.md#azure-object-size-limits). Doğrulamayı yeniden deneyin.|
| `BlockBlobSizeExceeded` | Azure blok blobu boyut sınırları dosya boyutunu aşıyor. | Böylece uyacağını dosya veya veri boyutunu küçültmek [Azure nesne boyutu sınırları](data-box-disk-limits.md#azure-object-size-limits). Doğrulamayı yeniden deneyin. |
| `ManagedDiskSizeExceeded` | Azure yönetilen Disk boyutu sınırları dosya boyutunu aşıyor. | Böylece uyacağını dosya veya veri boyutunu küçültmek [Azure nesne boyutu sınırları](data-box-disk-limits.md#azure-object-size-limits). Doğrulamayı yeniden deneyin. |
| `PageBlobSizeExceeded` | Azure yönetilen Disk boyutu sınırları dosya boyutunu aşıyor. | Böylece uyacağını dosya veya veri boyutunu küçültmek [Azure nesne boyutu sınırları](data-box-disk-limits.md#azure-object-size-limits). Doğrulamayı yeniden deneyin. |
| `InvalidShareContainerFormat`  |Dizin adları Azure adlandırma kurallarına kapsayıcılar veya paylaşımlar için uygun.         |Disk üzerinde önceden mevcut olan klasör altında oluşturulan ilk klasörü, depolama hesabınızdaki bir kapsayıcıya haline gelir. Bu paylaşımı veya kapsayıcı adı için Azure adlandırma kurallarına uymuyor. Dosyayı yeniden adlandırın uyacağını [Azure adlandırma kurallarına](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Doğrulamayı yeniden deneyin.   |
| `InvalidBlobNameFormat` | Dosya yolu, Azure Blob adlandırma kuralları uyarınca bulutta geçerli blob yola eşlemiyor.|Dosyayı yeniden adlandırın uyacağını [Azure adlandırma kurallarına](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Doğrulamayı yeniden deneyin. |
| `InvalidFileNameFormat` | Dosya yolu için geçerli bir dosya yolu buluttaki Azure dosya adlandırma kuralları uyarınca eşlemiyor. |Dosyayı yeniden adlandırın uyacağını [Azure adlandırma kurallarına](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Doğrulamayı yeniden deneyin. |
| `InvalidDiskNameFormat` | Dosya yolu, geçerli disk adına Azure yönetilen diski adlandırma kurallarına göre bulutta eşlemiyor. |Dosyayı yeniden adlandırın uyacağını [Azure adlandırma kurallarına](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Doğrulamayı yeniden deneyin.       |
| `NotPartOfFileShare` | Dosyaları karşıya yükleme yolu geçerli değil. Azure dosyaları bir klasörde dosya yükleme.   | Hata dosyaları kaldırın ve bu dosyaları precreated klasöre yükleyin. Doğrulamayı yeniden deneyin. |
| `NonVhdFileNotSupportedForManagedDisk` | Yönetilen disk olarak olmayan VHD dosyası karşıya yüklenemiyor. |VHD olmayan dosyaların kaldırmak `ManagedDisk` bu klasöre desteklenmemektedir veya bu dosyalara Taşı bir `PageBlob` klasör. Doğrulamayı yeniden deneyin. |


## <a name="next-steps"></a>Sonraki adımlar

- Sorun giderme [verileri karşıya yükleme hataları](data-box-disk-troubleshoot-upload.md).
