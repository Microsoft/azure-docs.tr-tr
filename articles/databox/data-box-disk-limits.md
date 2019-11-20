---
title: Azure Data Box Disk sınırları | Microsoft Docs
description: Microsoft Azure Data Box Disk için sistem sınırlarını ve önerilen boyutları açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 11/05/2019
ms.author: alkohli
ms.openlocfilehash: cd40c5d11414c91ff2f2febc0621e1e06f79e9cf
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73646976"
---
# <a name="azure-data-box-disk-limits"></a>Azure Data Box Disk sınırları


Microsoft Azure Data Box Disk çözümünüzü dağıtırken ve işletirken bu limitleri göz önünde bulundurun.

## <a name="data-box-service-limits"></a>Hizmet sınırlarını Data Box

 - Data Box hizmet, [bölge kullanılabilirliği](data-box-disk-overview.md#region-availability)bölümünde listelenen Azure bölgelerinde kullanılabilir.
 - Tek bir depolama hesabı Data Box Disk desteklenir.

## <a name="data-box-disk-performance"></a>Data Box Disk performans

USB 3.0 bağlantısıyla yapılan testlerde disk performansının 430 MB/sn seviyesine çıkabildiği görülmüştür. Gerçek performans kullanılan dosya boyutuna göre değişiklik gösterecektir. Daha küçük dosyalarda performans daha düşük olabilir.

## <a name="azure-storage-limits"></a>Azure depolama sınırları

Bu bölümde, Data Box hizmetine uygun olarak Azure Storage hizmeti için sınırlamalar ve Azure dosyaları, Azure blok Blobları ve Azure sayfa Blobları için gerekli adlandırma kuralları açıklanmaktadır. Depolama sınırlarını dikkatlice gözden geçirin ve tüm önerileri izleyin.

Azure depolama hizmeti sınırları ve adlandırma paylaşımları, kapsayıcılar ve dosyaları için en iyi uygulamalar hakkında en son bilgiler için şuraya gidin:

- [Kapsayıcıları adlandırma ve başvuru](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Paylaşımları adlandırma ve onlara başvurma](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Blok Blobları ve Sayfa Blobu kuralları](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Azure depolama hizmeti sınırlarını aşan veya Azure dosyaları/blob adlandırma kurallarına uymayan herhangi bir dosya veya dizin varsa, bu dosya veya dizinlerin Data Box hizmeti aracılığıyla Azure depolama 'ya alınmaz.

## <a name="data-upload-caveats"></a>Karşıya veri yükleme uyarıları

- Verileri doğrudan disklere kopyalamayın. Verileri önceden oluşturulmuş *Blok Blobu*,*Pageblob*ve *AzureFile* klasörlerine kopyalayın.
- *Blockblob* ve *pageblob* altındaki bir klasör bir kapsayıcıdır. Örneğin, kapsayıcılar *Blok Blobu/kapsayıcı* ve *pageblob/Container*olarak oluşturulur.
- Bulutta bulunan nesneyle aynı ada sahip bir Azure nesneniz (örneğin, blob) varsa, Data Box Disk dosyayı bulutta dosya (1) olarak yeniden adlandıracaktır.
- *Blockblob* ve *pageblob* paylaşımlarına yazılan her dosya sırasıyla Blok Blobu ve Sayfa Blobu olarak yüklenir.
- *Blockblob* ve *pageblob* klasörleri altında oluşturulan boş dizin hiyerarşisi (herhangi bir dosya olmadan) karşıya yüklenmedi.
- Verileri Azure 'a yüklerken herhangi bir hata oluşursa, hedef depolama hesabında bir hata günlüğü oluşturulur. Bu hata günlüğüne yönelik yol, karşıya yükleme tamamlandığında portalda kullanılabilir ve düzeltici eylem gerçekleştirmek için günlüğü gözden geçirebilirsiniz. Karşıya yüklenen verileri doğrulamadan verileri kaynaktan silmeyin.
- Veriler Azure dosyalarına yüklendiğinde dosya meta verileri ve NTFS izinleri korunmaz. Örneğin, veriler kopyalanırken dosyaların *son değiştirilme* özniteliği tutulmaz.
- Sırasıyla yönetilen diskler belirttiyseniz, aşağıdaki ek konuları gözden geçirin:

    - Önceden oluşturulmuş tüm klasörler ve tüm Data Box Disk’ler arasında, bir kaynak grubunda belirli bir ada sahip yalnızca bir yönetilen diskiniz olabilir. Bu durum, önceden oluşturulan klasörlere yüklenen VHD'lerin benzersiz adlara sahip olması gerektiği anlamına gelir. Verilen adın bir kaynak grubunda zaten var olan bir yönetilen diskle eşleşmediğinden emin olun. VHD'ler aynı ada sahipse, o adı taşıyan yalnızca bir VHD yönetilen diske dönüştürülür. Diğer VHD'ler, hazırlama depolama hesabına sayfa blobları olarak yüklenir.
    - VHD'leri her zaman önceden oluşturulmuş klasörlerden birine kopyalayın. VHD'leri bu klasörlerin dışına veya oluşturduğunuz bir klasöre kopyalarsanız, VHD'ler Azure Depolama hesabına yönetilen diskler olarak değil sayfa blobları olarak yüklenir.
    - Yönetilen diskler oluşturmak için yalnızca sabit VHD'ler karşıya yüklenebilir. Dinamik VHD'ler, fark kayıt VHD'leri veya VHDX dosyaları desteklenmez.

## <a name="azure-storage-account-size-limits"></a>Azure depolama hesabı boyut sınırları

Depolama hesabına kopyalanmış verilerin boyutuna ilişkin sınırlar aşağıda verilmiştir. Karşıya yüklediğiniz verilerin bu sınırlara uyduğundan emin olun. Bu limitlerin en güncel bilgileri için [Azure Blob depolama ölçek hedefleri](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) ve [Azure dosyaları ölçek hedefleri](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets)' ne gidin.

| Azure depolama hesabına kopyalanmış verilerin boyutu                      | Varsayılan limit          |
|---------------------------------------------------------------------|------------------------|
| Blok Blobu ve Sayfa Blobu                                            | depolama hesabı başına 500 TB. <br> Bu, Data Box Disk dahil olmak üzere tüm kaynaklardaki verileri içerir.|


## <a name="azure-object-size-limits"></a>Azure nesne boyutu sınırları

Yazıılabilecek Azure nesnelerinin boyutları aşağıda verilmiştir. Karşıya yüklenen tüm dosyaların bu sınırlara uygun olduğundan emin olun.

| Azure nesne türü | Varsayılan limit                                             |
|-------------------|-----------------------------------------------------------|
| Blok Blobu        | ~ 4,75 TiB                                                 |
| Sayfa Blobu         | 8 TiB <br> (Sayfa Blobu biçiminde karşıya yüklenen her dosya 512 bayt hizalı olmalıdır, aksi takdirde karşıya yükleme başarısız olur. <br> Hem VHD hem de VHDX 512 bayt hizalı.) |
|Azure Dosyaları        | 1 TiB <br> En çok, paylaşımın boyutu 5 TiB     |
| Yönetilen diskler     |4 TiB <br> Boyut ve sınırlar hakkında daha fazla bilgi için bkz. <li>[Yönetilen diskler için ölçeklenebilirlik hedefleri](../virtual-machines/windows/disk-scalability-targets.md#managed-virtual-machine-disks)</li>|


## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Azure Blok Blobu, Sayfa Blobu ve dosya adlandırma kuralları

| Varlık                                       | Kurallar                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Blok Blobu ve Sayfa Blobu için kapsayıcı adları <br> Azure dosyaları için FileShare adları | 3 ile 63 karakter uzunluğunda geçerli bir DNS adı olmalıdır. <br>  Bir harf veya sayı ile başlamalıdır. <br> Yalnızca küçük harf, sayı ve kısa çizgi (-) içerebilir. <br> Kısa çizgiden (-) hemen önce ve sonra bir harf veya rakam gelmelidir. <br> Adlarda kısa çizgiler art arda kullanılamaz. |
| Azure dosyaları için dizin ve dosya adları     |<li> Büyük küçük harf koruma, büyük/küçük harfe duyarsız ve 255 karakter uzunluğunda olmalıdır. </li><li> Eğik çizgi (/) ile bitemez. </li><li>Sağlanmışsa, otomatik olarak kaldırılır. </li><li> Şu karakterlere izin verilmez: <code>" \\ / : \| < > * ?</code></li><li> Ayrılmış URL karakterleri doğru şekilde atlanmalıdır. </li><li> Geçersiz URL yolu karakterlerine izin verilmiyor. \\uE000 gibi kod noktaları geçerli Unicode karakterler değildir. Denetim karakterleri (0x00-0x1F, \\u0081 vb.) gibi bazı ASCII veya Unicode karakterlere de izin verilmez. HTTP/1.1 'de Unicode dizelerini yöneten kurallar için bkz. RFC 2616, Bölüm 2,2: temel kurallar ve RFC 3987. </li><li> Şu dosya adlarına izin verilmiyor: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK $, Dot karakteri (.) ve iki nokta karakteri (..).</li>|
| Blok blobu ve sayfa blobu için blob adları      | Blob adları büyük/küçük harfe duyarlıdır ve karakterler herhangi bir düzende sıralanabilir. <br> Blob adı 1 ila 1024 karakter uzunluğunda olmalıdır. <br> Ayrılmış URL karakterleri doğru şekilde atlanmalıdır. <br>Blob adını oluşturan yolun bölümleri 254 karakterden uzun olamaz. Yol bölümü, arka arkaya gelen sınırlayıcı karakterlerinin (örneğin eğik çizgi "/") arasında yer alan ve bir sanal dizinin adına karşılık gelen dizedir. |

## <a name="managed-disk-naming-conventions"></a>Yönetilen disk adlandırma kuralları

| Varlık | Kurallar                                             |
|-------------------|-----------------------------------------------------------|
| Yönetilen disk adları       | <li> Ad 1 ile 80 karakter uzunluğunda olmalıdır. </li><li> Ad bir harf veya sayı ile başlamalı, bir harf, sayı veya alt çizgi ile bitmelidir. </li><li> Ad yalnızca harf, sayı, alt çizgi, nokta veya kısa çizgi içerebilir. </li><li>   Ad boşluk veya `/`içermemelidir.                                              |

## <a name="next-steps"></a>Sonraki adımlar

- [Data Box disk sistem gereksinimlerini](data-box-disk-system-requirements.md) gözden geçirin
