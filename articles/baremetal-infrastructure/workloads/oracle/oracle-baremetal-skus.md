---
title: Oracle iş yükleri için BareMetal SKU 'Ları
description: Oracle BareMetal altyapı iş yükleri için SKU 'Lar hakkında bilgi edinin.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: 42ff26b9ea9bcc022f1ddbf3dddcb041b2cea3a2
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588862"
---
# <a name="baremetal-skus-for-oracle-workloads"></a>Oracle iş yükleri için BareMetal SKU 'Ları

Bu makalede, Oracle iş yükleri için özelleştirilmiş BareMetal altyapı SKU 'Larına göz atalım.

Oracle SKU 'Larının BareMetal altyapısı, iki yuvadan en fazla dört yuva arasındadır. Ayrıca, iş yükünüzün gereksinimlerini karşılamak için çeşitli CPU çekirdekleri ve bellek boyutları arasından seçim yapabilirsiniz. İşte kullanılabilir SKU 'ların özelliklerini özetleyen bir tablo.
 
| **Oracle sertifikalı**  **donanım** | **Modelleme** | **Toplam bellek** | **Depolama** | **Kullanılabilirlik** |
| --- | --- | --- | --- | --- |
| EVET | SAP HANA Azure S32m-2 x Intel® Xeon® I6234 Işlemcisi 16 CPU çekirdekleri ve 32 CPU iş parçacığı | 1,5 TB | --- | Kullanılabilir |
| EVET | SAP HANA Azure S64m-4 x Intel® Xeon® I6234 Processor 32 CPU çekirdekleri ve 64 CPU iş parçacıkları | 3,0 TB | --- | Kullanılabilir |
| EVET | Azure S96 üzerinde SAP HANA – 2 x Intel® Xeon® E7-8890 v4 Işlemcisi 48 CPU çekirdekleri ve 96 CPU iş parçacıkları | 768 GB | 3,0 TB | Kullanılabilir |
| EVET | Azure S224 üzerinde SAP HANA – 4 x Intel® Xeon® Platinum 8276 işlemci 112 CPU çekirdekleri ve 224 CPU iş parçacıkları | 3,0 TB | 6,3 TB | Kullanılabilir |
| EVET | Azure S224m üzerinde SAP HANA – 4 x Intel® Xeon® Platinum 8276 işlemci 112 CPU çekirdekleri ve 224 CPU iş parçacıkları | 6,0 TB | 10,5 TB | Kullanılabilir |

- CPU çekirdekleri = sunucu biriminin hiper iş parçacıklı olmayan CPU çekirdekleri (Toplam fiziksel işlemci sayısı) toplamı. 
- CPU iş parçacıkları = sunucu biriminin hiper iş parçacıklı CPU çekirdekleri (Toplam mantıksal işlemci sayısı) tarafından sunulan işlem iş parçacıklarının toplamı. Çoğu birim, Hyper-Threading teknolojisini kullanmak için varsayılan olarak yapılandırılır.
- Sunucular müşterilere ayrılmıştır.
- Müşterinin kök erişimi (hiper yönetici yok) vardır.
- Sunucular doğrudan Azure VNET 'lerde değildir.

## <a name="next-steps"></a>Sonraki adımlar

Oracle için BareMetal altyapısı tarafından sunulan depolama hakkında bilgi edinin.

> [!div class="nextstepaction"]
> [Oracle iş yükleri için BareMetal 'da depolama](oracle-baremetal-storage.md)
