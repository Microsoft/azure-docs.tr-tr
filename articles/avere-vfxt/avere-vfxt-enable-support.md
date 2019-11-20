---
title: Avere vFXT-Azure desteğini etkinleştir
description: Azure için avere vFXT 'den destek yüklemelerini etkinleştirme
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: ac7db46a681fcde6bfcbb7695e2d66724f738918
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256223"
---
# <a name="enable-support-uploads"></a>Yükleme desteğini etkinleştir

Azure için avere vFXT, kümeniz hakkında Destek verilerini otomatik olarak karşıya yükleyebilir. Bu karşıya yüklemeler, destek personelinin olası en iyi müşteri hizmetini sağlamasına imkan tanır.

## <a name="steps-to-enable-uploads"></a>Karşıya yüklemeleri etkinleştirme adımları

Desteği etkinleştirmek için avere denetim masasından bu adımları izleyin. (Avere Denetim Masası 'nı açmayı öğrenmek için [vFXT kümesine erişin](avere-vfxt-cluster-gui.md) .)

1. Üstteki **Ayarlar** sekmesine gidin.
1. Sol taraftaki **destek** bağlantısına tıklayın ve gizlilik ilkesini kabul edin.

   ![Gizlilik ilkesini kabul etmek için avere Denetim Masası 'Nı ve onay düğmesini içeren açılır pencereyi gösteren ekran görüntüsü](media/avere-vfxt-privacy-policy.png)

1. Bölümü genişletmek için **Müşteri bilgisinin** solundaki üçgeni tıklatın.
1. **Karşıya yükleme bilgilerini yeniden doğrula** düğmesine tıklayın.
1. **Benzersiz küme adında** kümenin destek adını ayarlayın-personeli desteklemek için kümenizi benzersiz şekilde tanımladığından emin olun.
1. **Istatistik izleme**, **genel bilgiler karşıya yükleme**ve **kilitlenme bilgileri karşıya yükleme**kutularını işaretleyin.
1. **Gönder**' e tıklayın.

   ![Destek ayarları sayfasının tamamlanan müşteri bilgisi bölümünün bulunduğu ekran görüntüsü](media/avere-vfxt-support-info.png)

1. Bölümü genişletmek için **güvenli proaktif destek (SPS)** sol tarafındaki üçgeni tıklatın.
1. **SPS 'Yi etkinleştir bağlantısının**kutusunu işaretleyin.
1. **Gönder**' e tıklayın.

   ![Destek ayarları sayfasında tamamlanmış güvenli proaktif destek bölümünün bulunduğu ekran görüntüsü](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>Sonraki adımlar

Kümeye şirket içi veya mevcut bir bulut depolama sistemi eklemeniz gerekiyorsa, [depolamayı yapılandırma](avere-vfxt-add-storage.md)bölümündeki yönergeleri izleyin. 

Kümeye istemci eklemeye başlamaya hazırsanız, [avere vFXT kümesini bağlama](avere-vfxt-mount-clients.md)konusunu okuyun.