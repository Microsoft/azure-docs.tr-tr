---
title: Azure geçişi 'nde değerlendirme araçları Ekle | Microsoft Docs
description: Azure geçiş hub 'ında değerlendirme araçlarının nasıl ekleneceğini açıklar.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: 8d13222ec05fc97fa591ac67d636a1f8c5c88845
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73715861"
---
# <a name="add-assessment-tools"></a>Değerlendirme araçları ekleme

Bu makalede, [Azure geçişi](migrate-overview.md)'nde değerlendirme araçlarının nasıl ekleneceği açıklanmaktadır.

Azure geçişi, değerlendirme ve Azure 'a geçiş için bir araç merkezi sağlar. Azure geçiş araçlarının yanı sıra diğer araçlar ve bağımsız yazılım satıcısı (ISV) tekliflerini da içerir.

Bir değerlendirme aracı eklemek isterseniz ve henüz bir Azure geçişi projeniz yoksa, bu [makaleye](how-to-add-tool-first-time.md)uyun.

## <a name="select-a-tool"></a>Araç seçin

Değerlendirme için Azure dışı bir geçiş aracı seçerseniz, bir lisans alarak başlayın veya ücretsiz deneme için kayıt yaparak araç ilkesine uygun olarak kaydolun. Araçların Azure geçişi 'ne bağlanma seçeneği vardır. Aracı Azure geçişi 'ne bağlamak için yönergeleri ve belgeleri izleyin. Araçlar hakkında [daha fazla bilgi edinin](migrate-services-overview.md) .


## <a name="select-an-assessment-scenario"></a>Değerlendirme senaryosu seçin

1. Azure geçişi projesinde **Genel Bakış ' a**tıklayın.
2. Kullanmak istediğiniz değerlendirme senaryosunu seçin:

    - Azure 'a geçiş için makineleri ve iş yüklerini bulup değerlendirmek üzere **sunucuları değerlendir ve geçir**' i seçin.
    - Şirket içi SQL makinelerini değerlendirmek için **veritabanlarını değerlendir ve geçir**' i seçin.
    - Şirket içi Web uygulamalarını değerlendirmek için **Web uygulamalarını değerlendir ve geçir**' i seçin.

    ![Değerlendirme senaryosu](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>Sunucu değerlendirmesi aracı seçin 

1. **Sunucuları değerlendir ve geçir**' e tıklayın.
2. **Azure geçişi-sunucularında**, bir değerlendirme aracı eklemediyseniz, değerlendirme **araçları**' nın altında, değerlendirme **Aracı eklemek için buraya tıklayın '** ı seçin. Değerlendirme araçlarını zaten eklediyseniz, **daha fazla değerlendirme aracı ekle**' de **Değiştir**' i seçin.

    > [!NOTE]
    > Farklı bir projeye gitmeniz gerekiyorsa, **Azure geçişi-sunucular**' da, **farklı bir geçiş projesinin ayrıntılarını gör**' ün yanındaki **buraya tıklayın**' e tıklayın.

3. **Azure geçişi**' nde, kullanmak istediğiniz değerlendirme aracını seçin.

    - Azure geçişi sunucu değerlendirmesi kullanıyorsanız, değerlendirmeleri doğrudan Azure geçişi projesinde ayarlayabilir, çalıştırabilir ve görüntüleyebilirsiniz.
    - Farklı bir değerlendirme aracı kullanıyorsanız, siteleri için belirtilen bağlantıya gidin ve değerlendirdikleri yönergelere uygun olarak değerlendirmeyi çalıştırın.


## <a name="select-a-database-assessment-tool"></a>Bir veritabanı değerlendirmesi aracı seçin

1. **Veritabanlarını değerlendir ve geçir** ' e tıklayın
2. **Veritabanları**' nda **araç ekle**' ye tıklayın.
3. Bir araç ekleyin > **değerlendirme aracı**' nı seçin, veritabanınızı değerlendirmek için kullanmak istediğiniz aracı seçin.

## <a name="select-a-web-app-assessment-tool"></a>Bir Web uygulaması değerlendirme aracı seçin

1. **Web uygulamalarını değerlendir ve geçir**' e tıklayın.
2. Azure App Service geçiş aracının bağlantısını izleyin. Geçiş aracını kullanarak şunları yapın:

    - **Uygulamaları çevrimiçi değerlendirin**: Azure App Service Geçiş Yardımcısı kullanarak, ortak bir URL 'si ile uygulamaları değerlendirmenizi sağlayabilirsiniz.
    - **.Net/php**: iç .net ve PHP uygulamaları için Geçiş Yardımcısı indirebilir ve çalıştırabilirsiniz.



## <a name="next-steps"></a>Sonraki adımlar

[VMware](tutorial-prepare-vmware.md) VM 'Leri, [Hyper-V](tutorial-prepare-hyper-v.md)veya [fiziksel sunucular](tutorial-prepare-physical.md) için Azure geçişi sunucu değerlendirmesini kullanarak bir değerlendirme deneyin
