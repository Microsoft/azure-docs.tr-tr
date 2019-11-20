---
title: Yedekten geri yükleme-Azure App Service
description: Uygulamanızı bir anlık görüntüden nasıl geri yükleyeceğinizi öğrenin.
services: app-service
documentationcenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: 4164f9b5-f735-41c6-a2bb-71f15cdda417
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: multiple
ms.topic: article
ms.date: 04/04/2018
ms.author: aelnably
ms.reviewer: nicking
ms.custom: seodec18
ms.openlocfilehash: 4b3b10177b119c9362f807206a915d4b0b367e16
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639746"
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

1. [Azure Portal](https://portal.azure.com)uygulamanızın **Ayarlar** sayfasında yedeklemeler sayfasını göstermek için **yedeklemeler** ' e tıklayın. Ardından **anlık görüntü (Önizleme)** bölümünün altında **geri yükle** ' ye tıklayın.
   
    ![](./media/app-service-web-restore-snapshots/1.png)

2. **Geri yükle** sayfasında geri yüklenecek anlık görüntüyü seçin.
   
    ![](./media/app-service-web-restore-snapshots/2.png)
   
3. **Geri yükleme hedefinde**uygulama geri yükleme hedefini belirtin.
   
    ![](./media/app-service-web-restore-snapshots/3.png)
   
   > [!WARNING]
   > **Üzerine yaz**seçeneğini belirlerseniz, uygulamanızın geçerli dosya sistemindeki tüm mevcut veriler silinir ve üzerine yazılır. **Tamam**' a tıklamadan önce, yapmak istediğiniz durum olduğundan emin olun.
   > 
   > 
      
   > [!Note]
   > Geçerli teknik sınırlamalar nedeniyle, yalnızca aynı ölçek birimindeki uygulamalara geri yükleyebilirsiniz. Bu sınırlama gelecekteki bir sürümde kaldırılacak.
   > 
   > 
   
    Bir yuvaya geri yüklemek için **var olan uygulamayı** seçebilirsiniz. Bu seçeneği kullanmadan önce, uygulamanızda zaten bir yuva oluşturmuş olmanız gerekir.

4. Site yapılandırmanızı geri yüklemeyi seçebilirsiniz.
   
    ![](./media/app-service-web-restore-snapshots/4.png)

5. **Tamam**'ı tıklatın.
