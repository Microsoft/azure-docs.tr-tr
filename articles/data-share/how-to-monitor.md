---
title: Azure veri paylaşımından izleme
description: Azure veri paylaşımında davetiye durumunu izlemeyi, abonelikleri paylaşmayı ve anlık görüntü geçmişini nasıl izleyeceğinizi öğrenin
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 15089dd99de0471c244a6c0d93931438442599a7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490509"
---
# <a name="monitor-azure-data-share"></a>Azure Veri Paylaşımı’nı izleme  

Bu makalede, Azure veri paylaşımı 'nı kullanarak veri paylaşımlarınızı nasıl izleyebileceğiniz açıklanır. Veri sağlayıcı olarak, veri paylaşım ilişkilerinizin çeşitli yönlerini izleyebilirsiniz. Veri Tüketicileriniz veri paylaşımında davetinizi kabul etmiş olup olmadıkları ve bir paylaşma aboneliği oluşturulup oluşturulmayacağını ve verilerinizi kullanmaya başlamanızı sağlamak gibi ayrıntılar, izlemek için kullanılabilir. 

Bir veri tüketicisi olarak, Azure aboneliğinize tetiklenen anlık görüntüleri izleyebilirsiniz. 

## <a name="monitor-invitation-status"></a>Davet durumunu izleme

Gönderilen paylaşımlar-> davetlere giderek veri paylaşımı davetlerinizin durumunu görüntüleyin. 

![Davetiye durumu](./media/invitation-status.png "Davetiye durumu") 

Davetinizin bulunabileceği üç durum vardır:

* Bekleyen-veri paylaşma alıcısı daveti henüz kabul etmedi.
* Kabul edilen-veri paylaşma alıcısı daveti kabul etti.
* Reddedildi-veri paylaşma alıcısı daveti reddetti.

> [!IMPORTANT]
> Zaten kabul edildikten sonra bir daveti silerseniz, erişimi iptal etme eşdeğeri değildir. Gelecekteki anlık görüntülerin veri tüketicileri depolama hesabınıza kopyalanmasını durdurmak isterseniz, *abonelikleri paylaşma* sekmesinden erişimi iptal etmeniz gerekir. 

## <a name="monitor-share-subscriptions"></a>Paylaşma aboneliklerini izleme

Gönderilen paylaşımlar-> paylaşma aboneliklerine giderek paylaşım aboneliklerinizin durumunu görüntüleyin. Bu, davetinizi kabul ettikten sonra veri Tüketicileriniz tarafından oluşturulan etkin abonelikler hakkındaki ayrıntıları verir. Paylaşma aboneliğini seçerek ve *Iptal et*' i seçerek veri tüketicinizin gelecekteki güncelleştirmelerini durdurabilirsiniz. 

## <a name="snapshot-history"></a>Anlık görüntü geçmişi 

Geçmiş sekmesinde, veri tüketicisinin kiracısına kopyalanmış olan anlık görüntüleri görüntüleyebilirsiniz. Her bir anlık görüntü aralığının sıklığını ve süresini izleyebilirsiniz. 

![Anlık görüntü geçmişi](./media/sent-shares.png "Anlık görüntü geçmişi") 

Çalıştırma başlangıç tarihi ' ne tıklayarak her bir anlık görüntü çalıştırması hakkında daha fazla ayrıntı görüntüleyebilirsiniz. 

Varsayılan olarak en fazla 30 günlük anlık görüntü geçmişi görüntülenir. 30 günden fazla geçmişi görmeniz gerekiyorsa Izleme-> Tanılama Ayarları ' na gidin ve **Tanılama ayarı Ekle**' yi seçin. Bu günlükleri depolamak için bir depolama hesabı seçmeniz gerekecektir. 

![Anlık görüntü geçmişi](./media/diagnostic-settings.png "Tanılama ayarları") 

## <a name="next-steps"></a>Sonraki Adımlar 

[Azure veri paylaşma terminolojisi](terminology.md) hakkında daha fazla bilgi edinin