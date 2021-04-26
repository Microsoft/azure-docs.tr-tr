---
title: Bir gereç kullanarak Azure 'a veri aktarımı seçenekleri | Microsoft Docs
description: Data Box Edge, Azure Dosya Eşitleme ve StorSimple 8000 serisi arasında Azure 'a şirket içi veri aktarımı için doğru gereci seçme hakkında bilgi edinin.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 1345486e6bda7501a862612652b722b0075e190f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791178"
---
# <a name="compare-storsimple-with-azure-file-sync-and-data-box-edge-data-transfer-options"></a>StorSimple’ı Azure Dosya Eşitleme ve Data Box Edge veri aktarma seçenekleriyle karşılaştırma 

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]
 
Bu belgede, Azure 'a yönelik şirket içi veri aktarımı seçeneklerine genel bir bakış sunulmaktadır: Data Box Edge vs. Azure Dosya Eşitleme vs. StorSimple 8000 serisi karşılaştırması.

- **[Data Box Edge](../databox-online/azure-stack-edge-overview.md)** – Data Box Edge, verileri Azure 'a ve dışına taşıtan ve karşıya yükleme sırasında verileri önceden işlemeye yönelik bir şirket içi ağ aygıtıdır. Data Box Gateway, cihazın sanal bir sürümüdür ancak aynı veri aktarımı yeteneklerine sahiptir.
- **[Azure dosya eşitleme](../storage/file-sync/file-sync-deployment-guide.md)** – Azure dosya eşitleme, kuruluşunuzun dosya paylaşımlarını Azure dosyalarında merkezileştirirken, şirket içi bir dosya sunucusunun esnekliğini, performansını ve uyumluluğunu koruyarak kullanılabilir. Azure Dosya Eşitleme, Windows Server’ı Azure dosya paylaşımınızın hızlı bir önbelleğine dönüştürür. Azure Dosya Eşitleme genel kullanılabilirliği 2018 ' de daha önce duyuruldu.
- **[StorSimple](./storsimple-overview.md)** – StorSimple, kuruluşların depolama altyapısını birincil depolama, veri koruma, arşivleme ve olağanüstü durum kurtarma için tek bir çözümde birleştirerek Azure depolama ile sıkı bir şekilde tümleştirmenize yardımcı olan bir karma cihazdır. StorSimple için ürün yaşam döngüsü [burada](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series)bulunabilir.

## <a name="comparison-summary"></a>Karşılaştırma Özeti

|                           |StorSimple 8000   |Azure Dosya Eşitleme   |Data Box Edge           |
|---------------------------|----------------------------------------|-------------------------------|-----------------------------------------|
|**Genel Bakış**     |Katmanlı karma depolama ve arşivleme|Bulut katmanlaması ve çok siteli eşitleme ile genel dosya sunucusu depolaması.  |Verileri önceden işlemek ve ağ üzerinden Azure 'a göndermek için depolama çözümü.        |
|**Senaryolar**    |Dosya sunucusu, arşivleme, yedekleme hedefi |Dosya sunucusu, arşivleme (çok siteli)   |Veri aktarımı, ML ınkleme, IoT, arşivleme dahil verileri önceden işleme    |
|**Edge işlem** |Kullanılamaz |Kullanılamaz |Azure IoT Edge kullanarak kapsayıcıları çalıştırmayı destekler    |
|**Form faktörü**  |Fiziksel cihaz   |Windows Server 'da yüklü aracı |Fiziksel cihaz   |
|**Donanım**     |Hizmetin bir parçası olarak Microsoft 'tan gelen fiziksel cihaz | Müşteri tarafından sağlanmış |Hizmetin bir parçası olarak Microsoft 'tan gelen fiziksel cihaz  |
|**Veri biçimi**  |Özel biçim   |Dosyalar         |Bloblar veya dosyalar    |
|**Protokol desteği** |iSCSI          |SMB, NFS    | SMB veya NFS      |
|**Fiyatlandırma**      |[StorSimple](https://azure.microsoft.com/pricing/details/storsimple/) |[Azure Dosya Eşitleme](https://azure.microsoft.com/pricing/details/storage/files/)  |[Data Box Edge](https://azure.microsoft.com/pricing/details/storage/databox/edge/)  |

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Data Box Edge](../databox-online/azure-stack-edge-overview.md) ve [Azure Data Box Gateway](../databox-gateway/data-box-gateway-overview.md) hakkında bilgi edinin
- [Azure dosya eşitleme](../storage/file-sync/file-sync-deployment-guide.md) hakkında bilgi edinin