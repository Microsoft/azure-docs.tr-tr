---
title: Uygulamayı bir anlık görüntüden geri yükleme
description: Uygulamanızı bir anlık görüntüden nasıl geri yükleyeceğinizi öğrenin. Premium katmandaki beklenmedik veri kaybını otomatik gölge kopyalarla kurtarın.
ms.assetid: 4164f9b5-f735-41c6-a2bb-71f15cdda417
ms.topic: article
ms.date: 04/04/2018
ms.reviewer: nicking
ms.custom: seodec18
ms.openlocfilehash: f7edb632559dc8da2de32c58d994a7c51b1b09e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86169994"
---
# <a name="restore-an-app-in-azure-from-a-snapshot"></a>Azure 'da bir uygulamayı bir anlık görüntüden geri yükleme
Bu makalede bir anlık görüntüden [Azure App Service](../app-service/overview.md) bir uygulamanın nasıl geri yükleneceği gösterilmektedir. Uygulamanızı uygulamanızın anlık görüntülerinden birine göre önceki bir duruma geri yükleyebilirsiniz. Anlık görüntü yedeklemesini etkinleştirmeniz gerekmez, platform veri kurtarma amacıyla tüm uygulamaların anlık görüntüsünü otomatik olarak kaydeder.

Anlık görüntüler artımlı gölge kopyalardır ve düzenli [yedeklemeler](manage-backup.md)üzerinde çeşitli avantajlar sunar:
- Dosya kilitleri nedeniyle dosya kopyalama hatası yok.
- Depolama boyutu kısıtlaması yok.
- Yapılandırma gerekmiyor.

Anlık görüntülerden geri yükleme, **Premium** katmanda veya üzeri sürümlerde çalışan uygulamalar tarafından kullanılabilir. Uygulamanızı ölçeklendirme hakkında daha fazla bilgi için bkz. [Azure 'da bir uygulamayı](manage-scale-up.md)ölçeklendirme.

## <a name="limitations"></a>Sınırlamalar

- Bu özellik şu anda önizleme aşamasındadır.
- Yalnızca aynı uygulamaya veya bu uygulamaya ait bir yuvaya geri yükleyebilirsiniz.
- App Service, geri yüklemeyi yaparken hedef uygulamayı veya hedef yuvayı durduruyor.
- App Service platform veri kurtarma amacıyla anlık görüntüleri üç aya tutar.
- Yalnızca son 30 günün anlık görüntülerini geri yükleyebilirsiniz.
- App Service Ortamı çalışan uygulama hizmetleri anlık görüntüleri desteklemez.
 

## <a name="restore-an-app-from-a-snapshot"></a>Bir uygulamayı anlık görüntüden geri yükleme

1. [Azure Portal](https://portal.azure.com)uygulamanızın **Ayarlar** **sayfasında yedeklemeler sayfasını göstermek** için **yedeklemeler** ' e tıklayın. Ardından **anlık görüntü (Önizleme)** bölümünün altında **geri yükle** ' ye tıklayın.
   
    ![Anlık görüntü yedeklemesinden bir uygulamanın nasıl geri yükleneceğini gösteren ekran görüntüsü.](./media/app-service-web-restore-snapshots/1.png)

2. **Geri yükle** sayfasında geri yüklenecek anlık görüntüyü seçin.
   
    ![Geri yüklenecek anlık görüntünün nasıl seçdiğinin gösterildiği ekran görüntüsü. ](./media/app-service-web-restore-snapshots/2.png)
   
3. **Geri yükleme hedefinde** uygulama geri yükleme hedefini belirtin.
   
    ![Geri yükleme hedefinin nasıl belirtildiğinin gösterildiği ekran görüntüsü.](./media/app-service-web-restore-snapshots/3.png)
   
   > [!WARNING]
   > **Üzerine yaz** seçeneğini belirlerseniz, uygulamanızın geçerli dosya sistemindeki tüm mevcut veriler silinir ve üzerine yazılır. **Tamam**' a tıklamadan önce, yapmak istediğiniz durum olduğundan emin olun.
   > 
   > 
      
   > [!Note]
   > Geçerli teknik sınırlamalar nedeniyle, yalnızca aynı ölçek birimindeki uygulamalara geri yükleyebilirsiniz. Bu sınırlama gelecekteki bir sürümde kaldırılacak.
   > 
   > 
   
    Bir yuvaya geri yüklemek için **var olan uygulamayı** seçebilirsiniz. Bu seçeneği kullanmadan önce, uygulamanızda zaten bir yuva oluşturmuş olmanız gerekir.

4. Site yapılandırmanızı geri yüklemeyi seçebilirsiniz.
   
    ![Site yapılandırmasını geri yüklemeyi gösteren ekran görüntüsü.](./media/app-service-web-restore-snapshots/4.png)

5. **Tamam**'a tıklayın.
