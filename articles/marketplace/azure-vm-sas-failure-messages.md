---
title: Sanal makine SAS hata iletileri-Azure Marketi
description: Paylaşılan erişim imzaları (SAS) ile çalışırken sıkça sorulan sorular.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/15/2020
ms.openlocfilehash: 1c89887117c10ca77ec4c04b3adbe3e2d9923479
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93126847"
---
# <a name="virtual-machine-sas-failure-messages"></a>Sanal makine SAS hata iletileri

Aşağıda, paylaşılan erişim imzaları ile çalışırken (çözümünüz için karşıya yüklenen VHD 'leri tanımlamak ve paylaşmak için kullanılan) ve önerilen çözümlerin yanı sıra karşılaşılan yaygın sorunlar verilmiştir.

| Sorun | Hata Iletisi | Düzeltme |
| --------- | ------------------- | ------- |
| *Görüntüler kopyalanırken hata oluştu* |  |  |
| SAS URI 'sinde "?" bulunamadı | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Önerilen araçları kullanarak SAS URI 'sini güncelleştirin. |
| SAS URI 'SI içinde "St" ve "sa" parametreleri yok | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | SAS URI 'sini uygun **Başlangıç tarihi** ve **bitiş tarihi** değerleriyle güncelleştirin. |
| SAS URI 'SI içinde "SP = rl" değil | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | SAS URI 'sini, ve olarak ayarlanan izinlerle `Read` güncelleştirin `List` . |
| SAS URI 'SI, VHD adında boşluk içeriyor | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Boşluk kaldırmak için SAS URI 'sini güncelleştirin. |
| SAS URI yetkilendirme hatası | `Failure: Copying Images. Not able to download blob due to authorization error.` | SAS URI biçimini gözden geçirin ve düzeltin. Gerekirse yeniden oluşturun. |
| SAS URI 'SI "St" ve "o" parametrelerinin tam tarih-saat belirtimi yoktur | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | SAS URI 'SI **Başlangıç tarihi** ve **bitiş tarihi** parametreleri ( `st` ve alt `se` dizeleri), gibi tam bir tarih-saat biçimine sahip olmalıdır `11-02-2017T00:00:00Z` . Kısaltılmış sürümler geçersiz (Azure CLı 'deki bazı komutlar varsayılan olarak kısaltılmış değerler oluşturabilir). |
|  |  |  |

Ayrıntılar için bkz. [paylaşılan erişim Imzalarını kullanma (SAS)](../storage/common/storage-sas-overview.md).

## <a name="next-steps"></a>Sonraki adımlar

- [SAS URI 'SI oluştur](azure-vm-get-sas-uri.md)