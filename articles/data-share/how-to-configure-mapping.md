---
title: Azure veri paylaşımında bir veri kümesi eşlemesi yapılandırma
description: Azure veri paylaşımının kullanıldığı bir alınan paylaşımda veri kümesi eşlemesinin nasıl yapılandırılacağını öğrenin.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 754977788c5f6e5e574500552f670ba9083cf683
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490619"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Azure veri paylaşımında alınan bir paylaşıma yönelik veri kümesi eşlemesi yapılandırma

Bu makalede, Azure veri paylaşımının kullanıldığı bir alınan paylaşımda veri kümesi eşlemesinin nasıl yapılandırılacağı açıklanır. Bir veri paylaşma davetini kabul ettiyseniz ancak "daha sonra kabul et ve Yapılandır" seçeneğini tercih ettiyseniz bunu yapmak isteyeceksiniz. Sizinle paylaşılan verilerin hedefini değiştirmeniz veya bir SQL Server veri almak istiyorsanız, bir veri kümesi eşlemesi yapılandırmak isteyebilirsiniz. 

## <a name="navigate-to-a-received-data-share"></a>Alınan bir veri paylaşımında gezin

Azure veri paylaşma hizmetinde, aldığınız paylaşıma gidin ve **Ayrıntılar** sekmesini seçin. 

![Veri kümesi eşleme](./media/dataset-mapping.png "Veri kümesi eşleme") 

Hedef atamak istediğiniz veri kümesinin yanındaki kutuyu işaretleyin. Mevcut eşlemenin eşlemesini kaldırmak için **eşlemeyi kaldır** ' ı seçin. Yeni bir hedef depo seçmek için **+ hedefe eşle** ' yi seçin. 

![Hedefle eşle](./media/dataset-map-target.png "Hedefle eşle") 

## <a name="select-a-new-destination-store"></a>Yeni bir hedef depo seçin

Verilerin içinde olmasını istediğiniz bir hedef veri türü seçin. Önceden eşlenmiş depolama hesaplarında bulunan tüm verilerin otomatik olarak yeni hedefe taşınacağını unutmayın.

![Hedef depolama hesabı](./media/dataset-map-target-sql.png "Hedef depolama") 

## <a name="select-a-file-format-sql-sources-only"></a>Dosya biçimi seçin (yalnızca SQL kaynakları)

Kaynak veriler SQL tabanlı bir kaynaktan ise, hangi biçimin alındığını seçebilirsiniz. 

![Biçim seçin](./media/sql-file-formats.png "SQL dosya biçimleri")

## <a name="next-steps"></a>Sonraki adımlar

Verileri paylaşmaya nasıl başlayacağınızı öğrenmek için [verilerinizi paylaşma](share-your-data.md) öğreticiye geçin.



