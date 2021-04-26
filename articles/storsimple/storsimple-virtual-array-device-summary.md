---
title: StorSimple Sanal dizi cihazı Özet dikey penceresi | Microsoft Docs
description: StorSimple Device Manager için cihaz Özeti dikey penceresini açıklar ve StorSimple Sanal dizininizdeki sistem durumunu izlemek için nasıl kullanılacağını açıklar.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: a13c1ea7-6428-4234-84a6-0ebf51670a85
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2016
ms.author: manuaery
ms.openlocfilehash: 1ab72788c768568366f2627055015c74028330b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96005886"
---
# <a name="use-the-device-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>StorSimple Sanal dizisine bağlı StorSimple Device Manager için cihaz Özeti dikey penceresini kullanın

## <a name="overview"></a>Genel Bakış

StorSimple Device Manager cihaz dikey penceresi, belirli bir StorSimple Device Manager kayıtlı bir StorSimple Sanal dizisinin Özet görünümünü sağlar ve bu da Sistem Yöneticisi 'nin dikkatini çekmesini gerektiren cihaz sorunlarını vurgular. Bu öğretici, cihaz Özeti dikey penceresini tanıtır, içerik ve işlevi açıklar ve bu dikey pencereden gerçekleştirebileceğiniz görevleri açıklar.

Cihaz Özeti dikey penceresinde aşağıdaki bilgiler görüntülenir:

![Cihaz panosu](./media/storsimple-virtual-array-device-summary/device-blade.png)



## <a name="management"></a>Yönetim

StorSimple cihaz dikey penceresinde, StorSimple cihazınızı yönetme seçeneklerini görürsünüz. Yönetim komutlarını dikey pencerenin üst kısmında ve sol tarafta görürsünüz. Paylaşımlar veya birimler eklemek veya sanal dizinizi güncelleştirmek ya da devretmek için bu seçenekleri kullanın.

Essentials alanı, durum, model, yazılım sürümü ve dizinin **Web Kullanıcı arabirimine** bir bağlantı gibi önemli özelliklerden bazılarını yakalar. Bir iç ağ kullanıyorsanız, sanal dizinizi yönetmek için doğrudan [Yerel Web Kullanıcı arabirimini](storsimple-ova-web-ui-admin.md) başlatabilirsiniz.

![Cihaz temelleri](./media/storsimple-virtual-array-device-summary/device-essentials.png)

## <a name="storsimple-device-summary"></a>StorSimple cihaz Özeti

* **Uyarılar** kutucuğu, sanal diziniz için uyarı önem derecesine göre gruplandırılan tüm etkin uyarıların anlık görüntüsünü sağlar. Kutucuğa tıklayarak **Uyarılar** dikey penceresini açın ve ardından bu uyarıya ilişkin ek ayrıntıları görüntülemek için önerilen eylemler dahil olmak üzere tek bir uyarıya tıklayın. Sorun çözümlenirse uyarıyı da temizleyebilirsiniz.

* **Kapasite** kutucuğu, sanal cihaz genelinde sağlanan ve kalan toplam depolama alanı ile ilişkili birincil depolama alanını görüntüler. **Sağlanan** , kullanıma hazır ve kullanım için ayrılan depolama miktarına başvuruyorsa, **kalan** , bu cihaz genelinde sağlanabilen kalan kapasiteyi ifade eder. **Kalan katmanlı** kapasite, bulut dahil olmak üzere sağlanan kapasiteye sahip olsa da, **kalan yerel** , bu sanal diziye bağlı disklerde kalan kapasiteye sahip olur.

* **Kullanım** grafiğinde, sanal diziniz genelinde kullanılan birincil depolamayı, son 7 gün içinde tüketilen bulut depolama alanını ve varsayılan zaman aralığını görüntüleyebilirsiniz. Farklı bir zaman ölçeği seçmek için grafiğin sağ üst köşesindeki **Düzenle** seçeneğini kullanın.

* **Paylaşımlar** veya **birimler** kutucuğu, cihazınızdaki durum ile gruplanan paylaşımların veya birimlerin bir özetini sağlar. Kutucuğa tıklayarak **Paylaşımlar**  veya **birimler** listesi dikey penceresini açın ve ardından tek bir paylaşıma veya birime tıklayarak özelliklerini görüntüleyin veya değiştirin. Daha fazla bilgi için bkz. [paylaşımları yönetme](storsimple-virtual-array-manage-shares.md) veya [birimleri yönetme](storsimple-virtual-array-manage-volumes.md).

## <a name="next-steps"></a>Sonraki adımlar
Şunları nasıl yapacağınızı öğrenin:
- [StorSimple Sanal dizisindeki paylaşımları yönetme](storsimple-virtual-array-manage-shares.md)
    
- [StorSimple Sanal dizisindeki birimleri yönetme](storsimple-virtual-array-manage-volumes.md)

