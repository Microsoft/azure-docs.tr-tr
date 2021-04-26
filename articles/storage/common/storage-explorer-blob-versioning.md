---
title: Azure Depolama Gezgini blob sürüm oluşturma kılavuzu | Microsoft Docs
description: Azure Depolama Gezgini için blob sürüm oluşturma kılavuzu
services: storage
author: chuye
ms.service: storage
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: chuye
ms.openlocfilehash: e20733cb4b93fcfac7606895746645727f12d6c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92783836"
---
# <a name="azure-storage-explorer-blob-versioning-guide"></a>Azure Depolama Gezgini blob sürüm oluşturma kılavuzu

Microsoft Azure Depolama Gezgini, blob sürümlerinin kolay erişim ve yönetimini sağlar. Bu kılavuz, blob sürümü oluşturma 'nın Depolama Gezgini 'da nasıl çalıştığını anlamanıza yardımcı olur. Devam etmeden önce [BLOB sürümü oluşturma](../blobs/versioning-overview.md)hakkında daha fazla bilgi okumanız önerilir.

## <a name="terminology"></a>Terminoloji

Bu bölümde, bu makaledeki kullanımları anlamanıza yardımcı olacak bazı tanımlar sağlanmaktadır.

- Geçici silme: alternatif bir otomatik veri koruma özelliği. [Burada](../blobs/soft-delete-blob-overview.md)geçici silme hakkında daha fazla bilgi edinebilirsiniz.
- Etkin blob: etkin durumda bir blob veya blob sürümü oluşturulur. Yalnızca etkin durumdaki Bloblar veya blob sürümleri üzerinde işlem yapabilirsiniz.
- Geçici olarak silinen blob: bir blob veya geçici silme olarak işaretlenen blob sürümü. Geçici olarak silinen blob 'lar yalnızca bekletme döneminde tutulur.
- Blob sürümü: blob sürümü oluşturma etkinken oluşturulan bir BLOB. Her blob sürümü bir sürüm KIMLIĞIYLE ilişkilendirilir.
- Geçerli sürüm: geçerli sürüm olarak işaretlenmiş bir blob sürümü.
- Önceki sürüm: geçerli sürüm olmayan bir blob sürümü.
- Sürüm olmayan BLOB: blob sürümü oluşturma ile oluşturulan bir blob devre dışı bırakıldı. Sürüm olmayan bir blob sürüm KIMLIĞINE sahip değil.

## <a name="view-blob-versions"></a>Blob sürümlerini görüntüle

Depolama Gezgini Blobları görüntülemek için dört farklı görünümü destekler.

| Görünüm | Etkin sürüm olmayan BLOB 'lar | Geçici silinen sürüm olmayan Bloblar | Blob sürümleri |
| ---- | :----------: | :-----------: | :------------------: |
| Etkin blob 'lar | Yes | Hayır | Yalnızca geçerli sürüm |
| Etkin Bloblar ve geçici olarak silinen blob 'lar | Yes | Yes | Yalnızca geçerli sürüm |
| Geçerli sürüm olmadan etkin blob 'lar ve Bloblar | Yes | Hayır | Güncel sürüm veya en son etkin sürüm |
| Tüm Bloblar ve geçerli sürüm olmadan blob 'lar | Yes | Yes | Güncel sürüm veya en son sürüm |

### <a name="active-blobs"></a>Etkin blob 'lar

Bu görünümde Depolama Gezgini şunu görüntüler:

- Etkin sürüm olmayan BLOB 'lar
- Güncel sürümler

### <a name="active-blobs-and-soft-deleted-blobs"></a>Etkin Bloblar ve geçici olarak silinen blob 'lar

Bu görünümde Depolama Gezgini şunu görüntüler:

- Etkin sürüm olmayan BLOB 'lar
- Geçici silinen sürüm olmayan Bloblar
- Güncel sürümler.

### <a name="active-blobs-and-blobs-without-current-version"></a>Geçerli sürüm olmadan etkin blob 'lar ve Bloblar

Bu görünümde Depolama Gezgini şunu görüntüler:

- Etkin sürüm olmayan BLOB 'lar
- Güncel sürümler
- En son etkin önceki sürümler. 

Geçerli sürümü olmayan ancak etkin bir önceki sürüme sahip olmayan Bloblar için Depolama Gezgini, en son etkin önceki sürümü bu Blobun temsili olarak görüntüler.

### <a name="all-blobs-and-blobs-without-current-version"></a>Tüm Bloblar ve geçerli sürüm olmadan blob 'lar

Bu görünümde Depolama Gezgini şunu görüntüler:

- Etkin sürüm olmayan BLOB 'lar
- Geçici silinen sürüm olmayan Bloblar
- Güncel sürümler
- En son önceki sürümler. 

Geçerli sürümü olmayan Bloblar için Depolama Gezgini en son önceki sürümlerini bu Blobun temsili olarak görüntüler.

> [!Note]
> Hizmet sınırlaması nedeniyle Depolama Gezgini blob sürümlerini listelerken sanal dizinlerinizin hiyerarşik bir görünümünü almak için bazı ek işlemeye ihtiyaç duyuyor. Blobları aşağıdaki görünümlerde listelemek daha uzun sürer:
> 
> - Geçerli sürüm olmadan etkin blob 'lar ve Bloblar
> - Tüm Bloblar ve geçerli sürüm olmadan blob 'lar

## <a name="manage-blob-versions"></a>Blob sürümlerini yönetme

### <a name="view-versions-of-a-blob"></a>Bir Blobun sürümlerini görüntüleme

Depolama Gezgini bir blob 'un tüm sürümlerini görüntülemek için bir **sürümü Yönet** komutu sağlar. Bir Blobun sürümlerini görüntülemek için, sürümlerini görüntülemek istediğiniz blobu seçin ve araç çubuğundan ya da bağlam menüsünden **geçmişi Yönet &rarr; sürümleri** ' ni seçin.

### <a name="download-blob-versions"></a>Blob sürümlerini indir

Bir veya daha fazla BLOB sürümünü indirmek için, indirmek istediğiniz blob sürümlerini seçin ve araç çubuğundan veya bağlam menüsünden **İndir** ' i seçin.

Bir Blobun birden çok sürümünü indiriyorsanız, indirilen dosyalar dosya adlarının başlangıcında sürüm kimliklerine sahip olur.

### <a name="delete-blob-versions"></a>Blob sürümlerini Sil

Bir veya daha fazla BLOB sürümünü silmek için, silmek istediğiniz blob sürümlerini seçin ve araç çubuğundan veya bağlam menüsünden **Sil** ' i seçin.

Blob sürümleri, geçici silme ilkenize tabidir. Geçici silme etkinse, blob sürümleri geçici olarak silinir. Bir özel durum geçerli sürümü siliyor. Geçerli bir sürümün silinmesi, bunun yerine otomatik olarak etkin bir önceki sürüm olmasını sağlayacaktır.

### <a name="promote-blob-version"></a>Blob sürümünü yükselt

Önceki bir sürümü geçerli sürüm olacak şekilde yükselterek bir Blobun içeriğini geri yükleyebilirsiniz. Yükseltmek istediğiniz blob sürümünü seçin ve araç çubuğundan veya bağlam menüsünden **sürümü yükselt** ' i seçin.

Yükseltilen blob sürümüyle sürüm olmayan Blobların üzerine yazılacak. Bu verilere artık ihtiyacınız olmadığından emin olun veya işlemi onaylamadan önce verileri kendiniz yedekleyin. Geçerli sürümler otomatik olarak önceki sürümlere dönüşecek Depolama Gezgini onay istemez.

### <a name="undelete-blob-version"></a>Blob sürümünü geri al

Blob sürümlerinin tek tek silinmesi geri alınamaz. Her seferinde bir kez silinmeleri gerekir. Bir Blobun tüm blob sürümlerini geri almak için Blobun sürümlerinden birini seçin ve araç çubuğundan veya bağlam menüsünde **Seçileni geri al** ' ı seçin.

### <a name="change-access-tier-of-blob-versions"></a>Blob sürümlerinin erişim katmanını değiştirme

Her blob sürümünün kendi erişim katmanı vardır. Blob sürümlerinin erişim katmanını değiştirmek için, erişim katmanını değiştirmek istediğiniz blob sürümlerini seçin ve bağlam menüsünden **erişim katmanını değiştir...** seçeneğini belirleyin.

## <a name="see-also"></a>Ayrıca Bkz.

* [Blob sürümü oluşturma](../blobs/versioning-overview.md)
* [Bloblar için geçici silme](../blobs/soft-delete-blob-overview.md)