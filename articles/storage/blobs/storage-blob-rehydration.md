---
title: Arşiv katmanından blob verilerini yeniden doldurma
description: Verilere erişebilmek için bloblarınızı arşiv depolamadan yeniden doldurma.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/14/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: d6370509b49ae464b53525e7320676b04912bd12
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113713"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>Arşiv katmanından blob verilerini yeniden doldurma

Blob, arşiv erişim katmanında olduğunda çevrimdışı olarak kabul edilir ve okunamaz veya değiştirilemez. Blob meta verileri çevrimiçi ve kullanılabilir durumda kalır ve bu da blobu ve özelliklerini listelemenize olanak sağlar. Blob verilerini okuma ve değiştirme yalnızca sık erişimli veya seyrek erişimli gibi çevrimiçi katmanlarla kullanılabilir. Arşiv erişim katmanında depolanan verileri almak ve erişmek için iki seçenek vardır.

1. [Arşivlenmiş bir blob 'u çevrimiçi katmana yeniden](#rehydrate-an-archived-blob-to-an-online-tier) doldurma- [BLOB katmanını ayarla](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) işlemini kullanarak katmanını değiştirerek sık veya seyrek erişimli bir arşiv blobu.
2. [Arşivlenmiş bir blobu çevrimiçi katmana kopyalama](#copy-an-archived-blob-to-an-online-tier) - [BLOB kopyalama](https://docs.microsoft.com/rest/api/storageservices/copy-blob) işlemini kullanarak arşiv blobunun yeni bir kopyasını oluşturun. Farklı bir blob adı ve sık erişimli veya seyrek erişimli hedef katmanı belirtin.

 Katmanlar hakkında daha fazla bilgi için bkz. [Azure Blob depolama: sık erişimli, seyrek erişimli ve arşiv erişim katmanları](storage-blob-storage-tiers.md).

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Arşivlenmiş bir Blobun çevrimiçi bir katmana yeniden doldurma

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>Arşivlenmiş bir blobu çevrimiçi katmana kopyalama

Arşiv blobundan yeniden doldurma yapmak istemiyorsanız, bir [blobu kopyalama](https://docs.microsoft.com/rest/api/storageservices/copy-blob) işlemi yapabilirsiniz. İş üzerinde çalışmanız için çevrimiçi sık erişimli veya seyrek erişimli katmanda yeni bir blob oluşturulduğunda, özgün Blobun arşiv 'de değiştirilmemiş olarak kalır. Blobu kopyalama işleminde, blob kopyanızın oluşturulmasını istediğiniz önceliği belirtmek için isteğe bağlı *x-MS-rehibulunan öncelik* özelliğini standart veya yüksek (Önizleme) olarak da ayarlayabilirsiniz.

Arşiv blob 'ları yalnızca aynı depolama hesabı içindeki çevrimiçi hedef katmanlara kopyalanabilir. Bir arşiv blobunun başka bir arşiv blobuna kopyalanması desteklenmez.

Bir Blobun arşivden kopyalanması, seçili olan yeniden doldurma önceliğine bağlı olarak tamamlanması saatler sürebilir. Arka planda **kopyalama blobu** işlemi, seçili hedef katmanda yeni bir çevrimiçi blob oluşturmak için Arşiv kaynak blobunu okur. Blob 'ları listelediğinizde yeni blob görünebilir, ancak kaynak arşiv blobundan okuma tamamlanana ve veriler yeni çevrimiçi hedef bloba yazıldıktan sonra veriler kullanılamaz. Yeni blob bağımsız bir kopya olarak ve üzerinde yapılan herhangi bir değişiklik veya silme, kaynak Arşivi blob 'unu etkilemez.

## <a name="pricing-and-billing"></a>Fiyatlandırma ve Faturalama

Blob 'ların arşiv dışına, sık veya seyrek erişimli katmanlara yeniden doldurma işlemi, okuma işlemleri ve veri alımı olarak ücretlendirilir. Yüksek öncelikli (Önizleme) kullanımı, standart önceliğe kıyasla daha yüksek işlem ve veri alımı maliyetlerine sahiptir. Yüksek öncelikli yeniden doldurma işlemi faturanızda ayrı bir satır öğesi olarak gösterilir. Birkaç gigabaytlık bir arşiv blobu döndürmek için yüksek öncelikli bir istek 5 saatten fazla sürerse, yüksek öncelikli alma ücreti üzerinden ücretlendirilmezsiniz. Ancak, yeniden doldurma diğer isteklere göre önceliklendirildiğinden standart alma ücretleri yine de geçerlidir.

Blobların arşivden sık veya seyrek erişimli katmanlara kopyalanması, okuma işlemleri ve veri alımı olarak ücretlendirilir. Yeni blob kopyasının oluşturulması için bir yazma işlemi ücretlendirilir. Kaynak blobu arşiv katmanında değiştirilmemiş olarak kaldığı için, erken silme ücretleri bir çevrimiçi bloba kopyalanırken uygulanmaz. Seçilirse, yüksek öncelikli alma ücretleri uygulanır.

Arşiv katmanındaki Bloblar en az 180 gün önce depolanmalıdır. Arşivlenmiş blob 'ları 180 günden önce silmek veya yeniden doldurma işlemi erken silme ücretleri oluşturacak.

> [!NOTE]
> Blok Blobları ve veri yeniden doldurma fiyatlandırması hakkında daha fazla bilgi için bkz. [Azure Storage fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/blobs/). Giden veri aktarımı ücretleri hakkında daha fazla bilgi için bkz. [veri aktarımları fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="next-steps"></a>Sonraki Adımlar

* [BLOB depolama katmanları hakkında bilgi edinin](storage-blob-storage-tiers.md)
* [Blob depolama ve GPv2 hesaplarında bölgeye göre sık erişimli, seyrek erişimli ve arşiv fiyatlarını denetleme](https://azure.microsoft.com/pricing/details/storage/)
* [Azure Blob depolama yaşam döngüsünü yönetme](storage-lifecycle-management-concepts.md)
* [Veri aktarımı fiyatlandırmasını denetleme](https://azure.microsoft.com/pricing/details/data-transfers/)
