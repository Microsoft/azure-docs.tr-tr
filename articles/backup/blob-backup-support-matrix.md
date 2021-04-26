---
title: Azure Blobları yedeklemesi için destek matrisi
description: Azure Blobları yedeklenirken destek ayarlarının ve sınırlamaların özetini sağlar (önizlemede)
ms.topic: conceptual
ms.date: 02/16/2021
ms.custom: references_regions
ms.openlocfilehash: 12d289fdc3f84e7cbb3489a3ece283179e51772c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561908"
---
# <a name="support-matrix-for-azure-blobs-backup-in-preview"></a>Azure Blobları için destek matrisi yedekleme (önizlemede)

Bu makalede bölgesel kullanılabilirlik, desteklenen senaryolar ve Blobların işletimsel yedeğinin sınırlamaları özetlenmektedir.

## <a name="supported-regions"></a>Desteklenen bölgeler

Blob 'lar için işletimsel yedekleme şu anda şu bölgelerde kullanılabilir: Avustralya Orta, Avustralya Doğu, Brezilya Güney, Kanada Orta, Orta Hindistan, Orta ABD, Doğu Asya, Doğu ABD, Doğu ABD 2, Almanya Orta Batı, Japonya Doğu, Japonya Batı, Kore Orta, Kore Güney, Kuzey Avrupa, Orta Güney ABD, Güney Doğu Asya, İsviçre Kuzey, BAE Kuzey, UK Güney, UK Batı, Orta Batı ABD, Batı Avrupa , Batı ABD Batı ABD 2

## <a name="limitations"></a>Sınırlamalar

Blobların işletimsel yedeklemesi, blob 'un zaman içinde geri yüklenmesi, blob sürümü oluşturma, Bloblar için geçici silme, Bloblar için bir akış değiştirme ve yerel bir yedekleme çözümü sağlamak üzere kilit silme kullanır. Bu nedenle, bu yetenekler için uygulanan sınırlamalar işlemsel yedekleme için de geçerlidir.

**Desteklenen senaryolar:** İşletimsel yedekleme yalnızca standart genel amaçlı v2 depolama hesaplarında blok bloblarını destekler. ADLS 2. hesapları desteklenmez. Ayrıca, Depolama hesabınızdaki tüm sayfa Blobları, ekleme Blobları ve Premium Bloblar geri yüklenmez ve yalnızca blok Blobları geri yüklenecektir.

**Diğer sınırlamalar:**

- Saklama döneminde bir kapsayıcıyı sildiyseniz, bu kapsayıcı, zaman içinde geri yükleme işlemi ile geri yüklenmez. Silinen bir kapsayıcıda blob 'ları içeren bir dizi blobu geri yüklemeye çalışırsanız, zaman içinde geri yükleme işlemi başarısız olur. Kapsayıcıları silinmeye karşı koruma hakkında daha fazla bilgi için bkz. [kapsayıcılar Için geçici silme (Önizleme)](../storage/blobs/soft-delete-container-overview.md).
- Bir blob, mevcut bir süre ve geri yükleme noktası arasındaki dönemdeki sık ve seyrek katmanlar arasında taşınırsa, blob önceki katmanına geri yüklenir. Arşiv katmanındaki blok bloblarının geri yüklenmesi desteklenmez. Örneğin, sık kullanılan katmandaki bir blob arşiv katmanına iki gün önce taşınmışsa ve geri yükleme işlemi üç gün önce bir noktaya geri yüklenirse, blob etkin katmana geri yüklenmez. Arşivlenmiş bir blobu geri yüklemek için önce Arşiv katmanının dışına taşıyın. Daha fazla bilgi için bkz. [Arşiv katmanından blob verilerini yeniden doldurma](../storage/blobs/storage-blob-rehydration.md).
- [URL 'den](/rest/api/storageservices/put-block-from-url) [PUT bloğu veya](/rest/api/storageservices/put-block) put bloğu aracılığıyla karşıya yüklenen ancak [PUT bloğu listesi](/rest/api/storageservices/put-block-list)aracılığıyla kaydedilmemiş olan bir blok, blob 'un bir parçası değildir ve bu nedenle geri yükleme işleminin bir parçası olarak geri yüklenmez.
- Etkin kiralamaya sahip bir blob geri yüklenemez. Etkin kiralamaya sahip bir blob geri yüklenecek blob aralığına dahil edilmezse geri yükleme işlemi otomatik olarak başarısız olur. Geri yükleme işlemini başlatmadan önce tüm etkin kiraları kesin.
- Anlık görüntüler, geri yükleme işleminin bir parçası olarak oluşturulmaz veya silinmez. Yalnızca temel blob önceki durumuna geri yüklendi.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Blobları için işlemsel yedeklemeye genel bakış (önizlemede)](blob-backup-overview.md)