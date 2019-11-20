---
title: Bir gereç kullanarak Azure 'a veri aktarımı seçenekleri | Microsoft Docs
description: Azure 'a veri aktarmak için doğru gereci seçme hakkında bilgi edinin
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 9456d975ffb6d7726166f9badbcb8fe0ccbe5256
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68965353"
---
# <a name="compare-storsimple-with-azure-file-sync-and-data-box-edge-data-transfer-options"></a>Azure Dosya Eşitleme ve Data Box Edge veri aktarımı seçenekleriyle StorSimple ile karşılaştırın 

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]
 
Bu belgede, Azure 'a yönelik şirket içi veri aktarımına yönelik seçeneklere genel bir bakış sağlanır. Data Box Edge vs. Azure Dosya Eşitleme vs. StorSimple 8000 serisi.

- **[Data Box Edge](/azure/databox-online/data-box-edge-overview)** – Data Box Edge, verileri Azure 'a ve dışına taşıtan ve karşıya yükleme sırasında verileri önceden işlemeye yönelik bir şirket içi ağ aygıtıdır. Data Box Gateway, aynı veri aktarımı özelliklerine sahip bir cihazın sanal sürümüdür.
- **[Azure dosya eşitleme](/azure/storage/files/storage-sync-files-deployment-guide)** – Azure dosya eşitleme, kuruluşunuzun dosya paylaşımlarını Azure dosyalarında merkezileştirirken, şirket içi bir dosya sunucusunun esnekliğini, performansını ve uyumluluğunu koruyarak kullanılabilir. Azure Dosya Eşitleme, Windows Server 'ı Azure dosya paylaşımınızın hızlı önbelleğine dönüştürür. Azure Dosya Eşitleme genel kullanılabilirliği 2018 ' de daha önce duyuruldu.
- **[StorSimple](/azure/storsimple/storsimple-overview)** – StorSimple, kuruluşların depolama altyapısını birincil depolama, veri koruma, arşivleme ve olağanüstü durum kurtarma için tek bir çözümde birleştirerek Azure depolama ile sıkı bir şekilde tümleştirmenize yardımcı olan bir karma cihazdır. StorSimple için ürün yaşam döngüsü [burada](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series)bulunabilir.

## <a name="comparison-summary"></a>Karşılaştırma Özeti

|                           |StorSimple 8000   |Azure Dosya Eşitleme   |Data Box Edge           |
|---------------------------|----------------------------------------|-------------------------------|-----------------------------------------|
|Genel Bakış         |Katmanlı karma depolama ve arşivleme|Bulut katmanlaması ve çok siteli eşitleme ile genel dosya sunucusu depolaması.  |Verileri önceden işlemek ve ağ üzerinden Azure 'a göndermek için depolama çözümü.        |
|Senaryolar        |Dosya sunucusu, arşivleme, yedekleme hedefi |Dosya sunucusu, arşivleme (çok siteli)   |Veri aktarımı, ML ınkleme, IoT, arşivleme dahil verileri önceden işleme    |
|Uç işlemi     |Yok |Yok |Azure IoT Edge kullanarak kapsayıcıları çalıştırmayı destekler    |
|Form faktörü      |Fiziksel cihaz   |Windows Server 'da yüklü aracı |Fiziksel cihaz   |
|Donanım         |Hizmetin bir parçası olarak Microsoft 'tan gelen fiziksel cihaz | Müşteri tarafından sağlanmış |Hizmetin bir parçası olarak Microsoft 'tan gelen fiziksel cihaz  |
|Veri biçimi      |Özel biçim   |Dosyalar         |Bloblar veya dosyalar    |
|Protokol desteği |iSCSI          |SMB, NFS    | SMB veya NFS      |
|Fiyatlandırma          |[StorSimple](https://azure.microsoft.com/pricing/details/storsimple/) |[Azure Dosya Eşitleme](https://azure.microsoft.com/pricing/details/storage/files/)  |[Data Box Edge](https://azure.microsoft.com/pricing/details/storage/databox/edge/)  |

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Data Box Edge](/azure/databox-online/data-box-edge-overview) ve [Azure Data Box Gateway](/azure/databox-online/data-box-gateway-overview) hakkında bilgi edinin
- [Azure dosya eşitleme](/azure/storage/files/storage-sync-files-deployment-guide) hakkında bilgi edinin
