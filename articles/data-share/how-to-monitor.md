---
title: Azure veri paylaşımından izleme
description: Azure veri paylaşımında davetiye durumunu izlemeyi, abonelikleri paylaşmayı ve anlık görüntü geçmişini nasıl izleyeceğinizi öğrenin
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: cc400922115f348090677661cd1b30434be19b72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92910483"
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

Bir paylaşımın **Geçmiş** sekmesinde, verilerin veri sağlayıcısından veri tüketicisinin veri deposuna ne zaman kopyalanacağını görüntüleyebilirsiniz. Her anlık görüntünün sıklığını, süresini ve durumunu izleyebilirsiniz. 

![Ekran görüntüsünde Azure portal gönderilen paylaşımlar gösterilmektedir.](./media/sent-shares.png "Anlık görüntü geçmişi") 

Çalıştırma başlangıç tarihi ' ne tıklayarak her bir anlık görüntü çalıştırması hakkında daha fazla ayrıntı görüntüleyebilirsiniz. Ardından, aktarılan veri miktarını, kopyalanan dosya/kayıt sayısını, anlık görüntü süresini, kullanılan sanal çekirdekleri sayısını ve varsa hata iletisini görüntülemek için her bir veri kümesinin durumuna tıklayın. 

En fazla 30 günlük anlık görüntü geçmişi görüntülenir. 30 günden fazla geçmişi kaydetmeniz ve görmeniz gerekiyorsa, tanılama ayarından yararlanabilirsiniz.

## <a name="diagnostic-setting"></a>Tanılama ayarı

Günlük verilerini veya olaylarını kaydetmek için tanılama ayarını yapılandırabilirsiniz. Izleme-> Tanılama Ayarları ' na gidin ve **Tanılama ayarı Ekle**' yi seçin. İlgilendiğiniz günlük verilerini veya olayları ve bunları nerede depolamak veya göndermek istediğinizi seçin. 

![Ekran görüntüsü Azure portal Tanılama Ayarları sayfasını gösterir.](./media/diagnostic-settings.png "Tanılama ayarları") 

## <a name="next-steps"></a>Sonraki Adımlar 

[Azure veri paylaşma terminolojisi](terminology.md) hakkında daha fazla bilgi edinin