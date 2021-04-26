---
title: Azure Data Box sorunları giderme Azure Data Box Heavy
description: Bu cihazlara veri kopyalanırken Azure Data Box ve Azure Data Box Heavy görülen sorunların nasıl giderileceği açıklanmaktadır.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: troubleshooting
ms.date: 09/10/2020
ms.author: alkohli
ms.openlocfilehash: bb70946fda4fad7a42fd885a2515cb0d82698eca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92124688"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-and-azure-data-box-heavy"></a>Azure Data Box ve Azure Data Box Heavy ilgili sorunları giderin

Bu makalede, içeri aktarma siparişleri için Azure Data Box veya Azure Data Box Heavy kullanırken görebileceğiniz sorunları gidermeye yönelik bilgiler ayrıntılı olarak anlatılmaktadır. Makale, veriler Data Box kopyalanırken veya veriler Data Box bir içeri aktarma sırası için karşıya yüklendiğinde görülen olası hataların listesini içerir.

Bu makaledeki bilgiler Data Box için oluşturulan dışarı aktarma siparişlerine uygulanmaz.

## <a name="error-classes"></a>Hata sınıfları

Data Box ve Data Box Heavy hataları şu şekilde özetlenmektedir:

| Hata kategorisi *        | Description        | Önerilen eylem    |
|----------------------------------------------|---------|--------------------------------------|
| Kapsayıcı veya paylaşma adları | Kapsayıcı veya paylaşma adları Azure adlandırma kurallarını takip etmez.  |Hata listelerini indirin. <br> Kapsayıcıları veya paylaşımları yeniden adlandırın. [Daha fazla bilgi edinin](#container-or-share-name-errors).  |
| Kapsayıcı veya paylaşma boyut sınırı | Kapsayıcıların veya paylaşımların içindeki toplam veri, Azure sınırını aşıyor.   |Hata listelerini indirin. <br> Kapsayıcıdaki veya paylaşımdaki genel verileri azaltın. [Daha fazla bilgi edinin](#container-or-share-size-limit-errors).|
| Nesne veya dosya boyutu sınırı | Kapsayıcılar veya paylaşımlardaki nesne veya dosyalar Azure sınırını aşıyor.|Hata listelerini indirin. <br> Kapsayıcıda veya paylaşımda dosya boyutunu küçültün. [Daha fazla bilgi edinin](#object-or-file-size-limit-errors). |    
| Veri veya dosya türü | Veri biçimi veya dosya türü desteklenmiyor. |Hata listelerini indirin. <br> Sayfa Blobları veya yönetilen diskler için, verilerin 512 bayt hizalı ve önceden oluşturulmuş klasörlere kopyalandığından emin olun. [Daha fazla bilgi edinin](#data-or-file-type-errors). |
| Kritik olmayan BLOB veya dosya hataları  | Blob veya dosya adları Azure adlandırma kurallarına uymalıdır veya dosya türü desteklenmiyor. | Bu blob veya dosyalar kopyalanmayabilir veya adlar değişebilir. [Bu hataları nasıl düzelteceğinizi öğrenin](#non-critical-blob-or-file-errors). |

\* İlk dört hata kategorisi kritik hatalardır ve göndermeye hazırlanmaya devam edebilmeniz için önce sabit olması gerekir.


## <a name="container-or-share-name-errors"></a>Kapsayıcı veya paylaşma adı hataları

Bunlar, kapsayıcı ve paylaşma adlarıyla ilgili hatalardır.

### <a name="error_container_or_share_name_length"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

**Hata açıklaması:** Kapsayıcı veya paylaşımın adı 3 ila 63 karakter arasında olmalıdır. 

**Önerilen çözüm:** Veri kopyaladığınız Data Box veya Data Box Heavy paylaşımının (SMB/NFS) klasörü, depolama hesabınızda bir Azure kapsayıcısı haline gelir. 

- Cihaz yerel Web Kullanıcı arabiriminin **Bağlan ve Kopyala** sayfasında, hata dosyalarını indirip inceleyerek, sorunları içeren klasör adlarını belirleyebilirsiniz.
- Aşağıdakileri yaptığınızdan emin olmak için Data Box veya Data Box Heavy paylaşımındaki klasör adını değiştirin:

    - Ad 3 ila 63 karakter arasındadır.
    - Adlar yalnızca harf, sayı ve kısa çizgi içerebilir.
    - Adlar kısa çizgi ile başlayamaz veya bitemez.
    - Adların ardışık kısa çizgileri olamaz.
    - Geçerli adların örnekleri: `my-folder-1` , `my-really-extra-long-folder-111`
    - Geçerli olmayan adların örnekleri: `my-folder_1` ,,, `my` `--myfolder` `myfolder--` , `myfolder!`

    Daha fazla bilgi için bkz. [kapsayıcı adları](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) ve [paylaşma adları](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)için Azure adlandırma kuralları.


### <a name="error_container_or_share_name_alpha_numeric_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

**Hata açıklaması:** Kapsayıcı veya paylaşımın adı yalnızca harf, sayı veya kısa çizgi içermelidir.

**Önerilen çözüm:** Veri kopyaladığınız Data Box veya Data Box Heavy paylaşımının (SMB/NFS) klasörü, depolama hesabınızda bir Azure kapsayıcısı haline gelir. 

- Cihaz yerel Web Kullanıcı arabiriminin **Bağlan ve Kopyala** sayfasında, hata dosyalarını indirip inceleyerek, sorunları içeren klasör adlarını belirleyebilirsiniz.
- Aşağıdakileri yaptığınızdan emin olmak için Data Box veya Data Box Heavy paylaşımındaki klasör adını değiştirin:

    - Ad 3 ila 63 karakter arasındadır.
    - Adlar yalnızca harf, sayı ve kısa çizgi içerebilir.
    - Adlar kısa çizgi ile başlayamaz veya bitemez.
    - Adların ardışık kısa çizgileri olamaz.
    - Geçerli adların örnekleri: `my-folder-1` , `my-really-extra-long-folder-111`
    - Geçerli olmayan adların örnekleri: `my-folder_1` ,,, `my` `--myfolder` `myfolder--` , `myfolder!`

    Daha fazla bilgi için bkz. [kapsayıcı adları](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) ve [paylaşma adları](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)için Azure adlandırma kuralları.

### <a name="error_container_or_share_name_improper_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

**Hata açıklaması:** Kapsayıcı adları ve paylaşma adları kısa çizgi ile başlayamaz veya bitemez ve ardışık kısa çizgilerden oluşabilir.

**Önerilen çözüm:** Veri kopyaladığınız Data Box veya Data Box Heavy paylaşımının (SMB/NFS) klasörü, depolama hesabınızda bir Azure kapsayıcısı haline gelir. 

- Cihaz yerel Web Kullanıcı arabiriminin **Bağlan ve Kopyala** sayfasında, hata dosyalarını indirip inceleyerek, sorunları içeren klasör adlarını belirleyebilirsiniz.
- Aşağıdakileri yaptığınızdan emin olmak için Data Box veya Data Box Heavy paylaşımındaki klasör adını değiştirin:

    - Ad 3 ila 63 karakter arasındadır.
    - Adlar yalnızca harf, sayı ve kısa çizgi içerebilir.
    - Adlar kısa çizgi ile başlayamaz veya bitemez.
    - Adların ardışık kısa çizgileri olamaz.
    - Geçerli adların örnekleri: `my-folder-1` , `my-really-extra-long-folder-111`
    - Geçerli olmayan adların örnekleri: `my-folder_1` ,,, `my` `--myfolder` `myfolder--` , `myfolder!`

    Daha fazla bilgi için bkz. [kapsayıcı adları](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) ve [paylaşma adları](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)için Azure adlandırma kuralları.
    
### <a name="error_file_or_directory_name_illegal"></a>ERROR_FILE_OR_DIRECTORY_NAME_ILLEGAL

**Hata açıklaması**: Dizin veya kapsayıcı adları geçersiz karakterler içeriyor.

**Önerilen çözüm**: kopyaladığınız dizin veya kapsayıcı adları desteklenmeyen karakterler içeriyor.

- Yerel Web Kullanıcı arabiriminin Bağlan ve Kopyala sayfasında, sorun ile klasör adlarını belirlemek için hata dosyalarını indirin ve gözden geçirin. 
- Azure adlandırma kurallarıyla uyumlu olduklarından emin olmak için dizini veya kapsayıcıları yeniden adlandırın.

Daha fazla bilgi için bkz. [dizinler](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) ve [kapsayıcılar](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names)için Azure adlandırma kuralları.

## <a name="container-or-share-size-limit-errors"></a>Kapsayıcı veya paylaşma boyut sınırı hataları

Bunlar, bir kapsayıcıda veya paylaşımda izin verilen verilerin boyutunu aşan verilerle ilgili hatalardır.

### <a name="error_container_or_share_capacity_exceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

**Hata açıklaması:** Azure dosya paylaşımı, bir paylaşımı 5 TiB veri ile sınırlandırır ve depolama hesabında büyük dosya paylaşımları etkinleştirilmemiştir. Bu sınır bazı paylaşımlar için aşıldı.

**Önerilen çözüm:** Yerel Web Kullanıcı arabiriminin **Bağlan ve Kopyala** sayfasında hata dosyalarını indirin ve gözden geçirin.

- Hata Günlüklerinden Bu soruna sahip klasörleri tanımlayabilir ve bu klasördeki dosyaların 5 TiB altında olduğundan emin olun.
- 5 TiB sınırı, büyük dosya paylaşımlarına izin veren bir depolama hesabı için geçerlidir. Ancak, siparişinizi yerleştirdiğinizde büyük dosya paylaşımlarınızın yapılandırılmış olması gerekir. 
  - [Microsoft desteği](data-box-disk-contact-microsoft-support.md) başvurun ve yeni bir sevkiyat etiketi isteyin.
  - [Depolama hesabında büyük dosya paylaşımlarını etkinleştirin.](../storage/files/storage-files-how-to-create-large-file-share.md#enable-large-files-shares-on-an-existing-account)
  - [Depolama hesabındaki dosya paylaşımlarını genişletin](../storage/files/storage-files-how-to-create-large-file-share.md#expand-existing-file-shares) ve kotayı 100 TİB olarak ayarlayın.
  
  
## <a name="object-or-file-size-limit-errors"></a>Nesne veya dosya boyutu sınırı hataları

Bunlar, en büyük nesne boyutunu veya Azure 'da izin verilen dosyayı aşan verilerle ilgili hatalardır. 

### <a name="error_blob_or_file_size_limit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

**Hata açıklaması:** Dosya boyutu karşıya yükleme için en büyük dosya boyutunu aşıyor.

**Önerilen çözüm:** Blob veya dosya boyutları karşıya yükleme için izin verilen üst sınırı aşıyor.

- Yerel Web Kullanıcı arabiriminin **Bağlan ve Kopyala** sayfasında hata dosyalarını indirin ve gözden geçirin.
- Blob ve dosya boyutlarının Azure nesne boyutu sınırlarını aşmadığından emin olun.

## <a name="data-or-file-type-errors"></a>Veri veya dosya türü hataları

Bunlar, kapsayıcıda veya paylaşımda bulunan desteklenmeyen dosya türü veya veri türüyle ilgili hatalardır. 

### <a name="error_blob_or_file_size_alignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

**Hata açıklaması:** Blob veya dosya yanlış hizalı.

**Önerilen çözüm:** Data Box veya Data Box Heavy üzerindeki Sayfa Blobu paylaşma yalnızca 512 bayt hizalı (örneğin, VHD/VHDX) dosyaları destekler. Sayfa Blobu paylaşımında kopyalanmış olan veriler Azure 'a sayfa Blobları olarak yüklenir.

Sayfa Blobu paylaşımından VHD olmayan/VHDX verilerini kaldırın. Blok Blobu veya Azure dosyaları için paylaşımları genel veriler için kullanabilirsiniz.

Daha fazla bilgi için bkz. [sayfa Bloblarına genel bakış](../storage/blobs/storage-blob-pageblob-overview.md).

### <a name="error_blob_or_file_type_unsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

**Hata açıklaması:** Yönetilen bir disk paylaşımında desteklenmeyen bir dosya türü var. Yalnızca sabit VHD 'lere izin verilir.

**Önerilen çözüm:**

- Yönetilen diskler oluşturmak için yalnızca sabit VHD 'leri karşıya yüklediğinizden emin olun.
- VHDX dosyaları veya **dinamik** ve **fark kayıt** VHD 'leri desteklenmez.

### <a name="error_directory_disallowed_for_type"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

**Hata açıklaması:** Yönetilen diskler için önceden var olan klasörlerde bir dizine izin verilmez. Bu klasörlerde yalnızca sabit VHD 'lere izin verilir.

**Önerilen çözüm:** Yönetilen diskler için, her bir paylaşımda, Depolama hesabınızdaki kapsayıcılara karşılık gelen aşağıdaki üç klasör oluşturulur: Premium SSD, Standart HDD ve Standart SSD. Bu klasörler, yönetilen disk için performans katmanına karşılık gelir.

- Sayfa Blobu verilerinizi (VHD) bu mevcut klasörlerden birine kopyalamadığınızdan emin olun.
- Bu mevcut klasörlerde bir klasöre veya dizine izin verilmiyor. Önceden var olan klasörler içinde oluşturduğunuz tüm klasörleri kaldırın.

Daha fazla bilgi için bkz. [yönetilen disklere kopyalama](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).

### <a name="reparse_point_error"></a>REPARSE_POINT_ERROR

**Hata açıklaması:** Linux 'ta sembolik bağlantılara izin verilmez. 

**Önerilen çözüm:** Sembolik bağlantılar genellikle bağlantılar, kanallar ve benzeri diğer dosyalardır. Bağlantıları kaldırın ya da bağlantıları çözün ve verileri kopyalayın.


## <a name="non-critical-blob-or-file-errors"></a>Kritik olmayan BLOB veya dosya hataları

Veri kopyalama sırasında görülen blob, dosya veya kapsayıcıların adlarıyla ilgili kritik olmayan tüm hatalar aşağıdaki bölümde özetlenmiştir. Bu hatalar varsa, adlar Azure adlandırma kurallarına uyacak şekilde değiştirilir. Karşıya veri yükleme için karşılık gelen sıra durumu **uyarılarla tamamlanacaktır**.  

### <a name="error_blob_or_file_name_character_control"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

**Hata açıklaması:** Blob veya dosya adları desteklenmeyen denetim karakterleri içeriyor.

**Önerilen çözüm:** Kopyaladığınız Bloblar veya dosyalar, desteklenmeyen karakterlere sahip adlar içeriyor.

Yerel Web Kullanıcı arabiriminin **Bağlan ve Kopyala** sayfasında hata dosyalarını indirin ve gözden geçirin.
Desteklenmeyen karakterleri kaldırmak için dosyaları kaldırın veya yeniden adlandırın.

Daha fazla bilgi için bkz. [BLOB adları](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) ve [dosya adları](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)için Azure adlandırma kuralları.

### <a name="error_blob_or_file_name_character_illegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

**Hata açıklaması:** Blob veya dosya adları geçersiz karakterler içeriyor.

**Önerilen çözüm:** Kopyaladığınız Bloblar veya dosyalar, desteklenmeyen karakterlere sahip adlar içeriyor.

Yerel Web Kullanıcı arabiriminin **Bağlan ve Kopyala** sayfasında hata dosyalarını indirin ve gözden geçirin.
Desteklenmeyen karakterleri kaldırmak için dosyaları kaldırın veya yeniden adlandırın.

Daha fazla bilgi için bkz. [BLOB adları](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) ve [dosya adları](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)için Azure adlandırma kuralları.


### <a name="error_blob_or_file_name_ending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

**Hata açıklaması:** Blob veya dosya adları hatalı karakterlerle bitiyor.

**Önerilen çözüm:** Kopyaladığınız Bloblar veya dosyalar, desteklenmeyen karakterlere sahip adlar içeriyor.

Yerel Web Kullanıcı arabiriminin **Bağlan ve Kopyala** sayfasında hata dosyalarını indirin ve gözden geçirin.
Desteklenmeyen karakterleri kaldırmak için dosyaları kaldırın veya yeniden adlandırın.

Daha fazla bilgi için bkz. [BLOB adları](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) ve [dosya adları](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)için Azure adlandırma kuralları.


### <a name="error_blob_or_file_name_segment_count"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

**Hata açıklaması:** Blob veya dosya adı çok fazla yol kesimi içeriyor.

**Önerilen çözüm:** Kopyaladığınız Bloblar veya dosyalar, yol kesimlerinin maksimum sayısını aşıyor. Yol kesimi, ardışık sınırlayıcı karakterler arasındaki dizedir, örneğin eğik çizgi/.

- Yerel Web Kullanıcı arabiriminin **Bağlan ve Kopyala** sayfasında hata dosyalarını indirin ve gözden geçirin.
- [BLOB adlarının](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) ve [dosya adlarının](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) Azure adlandırma kurallarına uyduğundan emin olun.

### <a name="error_blob_or_file_name_aggregate_length"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

**Hata açıklaması:** Blob veya dosya adı çok uzun.

**Önerilen çözüm:** Blob veya dosya adları en fazla uzunluğu aşıyor.

- Yerel Web Kullanıcı arabiriminin **Bağlan ve Kopyala** sayfasında hata dosyalarını indirin ve gözden geçirin.
- Blob adının 1.024 karakteri aşmaması gerekir.
- Adların 1024 karakteri aşmaması için Blobu veya dosyaları kaldırın veya yeniden adlandırın.

Daha fazla bilgi için bkz. blob adları ve dosya adları için Azure adlandırma kuralları.

### <a name="error_blob_or_file_name_component_length"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

**Hata açıklaması:** Blob veya dosya adındaki segmentlerin biri çok uzun.

**Önerilen çözüm:** Blob veya dosya adındaki yol parçalarından biri maksimum karakter sayısını aşıyor. Yol kesimi, ardışık sınırlayıcı karakterler arasındaki dizedir, örneğin eğik çizgi/.

- Yerel Web Kullanıcı arabiriminin **Bağlan ve Kopyala** sayfasında hata dosyalarını indirin ve gözden geçirin.
- [BLOB adlarının](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) ve [dosya adlarının](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) Azure adlandırma kurallarına uyduğundan emin olun.


### <a name="error_container_or_share_name_disallowed_for_type"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

**Hata açıklaması:** Yönetilen disk paylaşımları için yanlış kapsayıcı adları belirtildi.

**Önerilen çözüm:** Yönetilen diskler için, her bir paylaşımda, Depolama hesabınızdaki kapsayıcılara karşılık gelen aşağıdaki klasörler oluşturulur: Premium SSD, Standart HDD ve Standart SSD. Bu klasörler, yönetilen disk için performans katmanına karşılık gelir.

- Sayfa Blobu verilerinizi (VHD) bu mevcut klasörlerden birine kopyalamadığınızdan emin olun. Yalnızca bu mevcut kapsayıcılardan gelen veriler Azure 'a yüklenir.
- Premium SSD, Standart HDD ve Standart SSD ile aynı düzeyde oluşturulan diğer tüm klasörler geçerli bir performans katmanına karşılık gelmez ve kullanılamaz.
- Performans katmanları dışında oluşturulan dosya veya klasörleri kaldırın.

Daha fazla bilgi için bkz. [yönetilen disklere kopyalama](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).


## <a name="next-steps"></a>Sonraki adımlar

- [Data Box BLOB depolama sistem gereksinimleri](data-box-system-requirements-rest.md)hakkında bilgi edinin.