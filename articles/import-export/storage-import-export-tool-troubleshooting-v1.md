---
title: Azure Içeri/dışarı aktarma 'da içeri ve dışarı aktarma sorunlarını giderme | Microsoft Docs
description: Azure Içeri/dışarı aktarma kullanırken yaygın sorunları nasıl ele alabileceğinizi öğrenin.
author: v-dalc
services: storage
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/19/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 555529b52d586078ba7e1832373ec126ba545c11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98706683"
---
# <a name="troubleshoot-issues-in-azure-importexport"></a>Azure Içeri/dışarı aktarma sorunlarını giderme
Bu makalede, Azure Içeri/dışarı aktarma 'da verileri içeri ve dışarı aktarma sırasında sık karşılaşılan sorunların nasıl giderileceği açıklanır.

## <a name="a-copy-session-failed-what-i-should-do"></a>Bir kopyalama oturumu başarısız oldu. Ne yapmam gerekir?  

Bir kopyalama oturumu başarısız olduğunda iki seçeneğiniz vardır:  
* Hata yeniden denenebiliyorsanız-Örneğin, ağ paylaşımının kısa bir süre için çevrimdışı olması ve şimdi yeniden çevrimiçi olması durumunda kopyalama oturumunu sürdürebilirsiniz.
* Hata yeniden denenemez-Örneğin, komut satırı parametrelerinde yanlış kaynak dosya dizini belirttiyseniz, kopyalama oturumunu iptal etmeniz gerekir.
 
<!--For information about resuming and aborting copy sessions, see [Preparing Hard Drives for an Import Job](../storage-import-export-tool-preparing-hard-drives-import-v1.md  - Article we removed from TOC. File remains.-->

## <a name="i-cant-resume-or-abort-a-copy-session"></a>Bir kopyalama oturumunu sürdürülemiyor veya iptal edemiyorum.

Kopyalama oturumu bir sürücü için ilk kopyalama oturumundaysanız, hata iletisi şu şekilde olmalıdır: "ilk kopya oturumu devam ettirilemez veya iptal edilemez." Bu durumda, eski günlük dosyasını silip komutunu yeniden çalıştırabilirsiniz.  

Bir kopya oturumu bir sürücü için ilk değilse, oturum her zaman sürdürülür veya iptal edilebilir.  

## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>Günlük dosyası kayboldu. İşi oluşturmaya devam edebilir miyim?

Bir sürücünün günlük dosyası, veri kopyadan alınan tüm oturum bilgilerini içerir. Sürücüye dosya eklediğinizde, günlük dosyası bir içeri aktarma işi oluşturmak için kullanılır. Günlük dosyasını kaybederseniz, sürücünün tüm kopyalama oturumlarını yeniden yapmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Içeri/dışarı aktarma aracı 'nı ayarlama](storage-import-export-tool-setup-v1.md)
* [Sabit sürücüleri bir içeri aktarma işi için hazırlama](storage-import-export-data-to-blobs.md#step-1-prepare-the-drives)
* [Dosya kopyalama günlük dosyalarıyla iş durumunu gözden geçirme](storage-import-export-tool-reviewing-job-status-v1.md)
* [İçeri aktarma işini onarma](storage-import-export-tool-repairing-an-import-job-v1.md)
* [Dışarı aktarma işini onarma](storage-import-export-tool-repairing-an-export-job-v1.md)