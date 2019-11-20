---
title: include dosyası
description: include dosyası
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 07/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 262880997c6b065dc5293a18d9a07c52ac836f37
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73591526"
---
> [!IMPORTANT]
> Azure ayrılmış Konakları Şu anda genel önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> **Bilinen önizleme sınırlamaları**
> - Sanal Makine Ölçek Kümeleri Şu anda adanmış konaklarda desteklenmiyor.
> - Önizleme ilk sürümü şu VM serisini destekler: DSv3 ve ESv3. 


## <a name="create-a-host-group"></a>Konak grubu oluştur

**Konak grubu** , adanmış Konakları koleksiyonunu temsil eden yeni bir kaynaktır. Bir bölge ve kullanılabilirlik bölgesinde bir konak grubu oluşturur ve bu gruba ana bilgisayar ekleyebilirsiniz. Yüksek kullanılabilirlik için planlama yaparken ek seçenekler vardır. Aşağıdaki seçeneklerden birini veya her ikisini de adanmış konaklarınız ile kullanabilirsiniz: 
- Birden çok kullanılabilirlik alanı arasında yayılır. Bu durumda, kullanmak istediğiniz her bölgede bir konak grubunuz olması gerekir.
- Fiziksel raflarla eşlenen birden çok hata etki alanı arasında yayılma. 
 
Her iki durumda da, konak grubunuz için hata etki alanı sayısını sağlamanız gerekir. Grubunuzdaki hata etki alanlarına yayılması istemiyorsanız, hata etki alanı sayısı 1 ' i kullanın. 

Hem kullanılabilirlik bölgelerini hem de hata etki alanlarını kullanmaya karar verebilirsiniz. 

Bu örnekte, 1 kullanılabilirlik alanı ve 2 hata etki alanı kullanarak bir konak grubu oluşturacağız. 


1. Azure [portalını](https://portal.azure.com)açın.
1. Sol üst köşedeki **kaynak oluştur** ' u seçin.
1. **Konak grubu** araması yapın ve ardından sonuçlardan **konak grupları (Önizleme)** seçeneğini belirleyin.

    ![Konak grupları arama sonucu.](./media/virtual-machines-common-dedicated-hosts-portal/host-group.png)
1. **Konak grupları (Önizleme)** sayfasında **Oluştur**' u seçin.
1. Kullanmak istediğiniz aboneliği seçin ve yeni bir kaynak grubu oluşturmak için **Yeni oluştur** ' u seçin.
1. **Ad** olarak *Myayrılmış hostsrg* yazın ve ardından **Tamam**' ı seçin.
1. **Konak grubu adı**Için *myhostgroup*yazın.
1. **Konum**için **Doğu ABD**' yi seçin.
1. **Kullanılabilirlik bölgesi**için **1**' i seçin.
1. **Hata etki alanı sayısı**için **2**' yi seçin.
1. **Gözden geçir + oluştur** ' u seçin ve doğrulama için bekleyin.

    ![Konak grubu ayarları](./media/virtual-machines-common-dedicated-hosts-portal/host-group-settings.png)
1. **Doğrulama başarılı** iletisini gördüğünüzde, konak grubunu oluşturmak için **Oluştur** ' u seçin.

Konak grubunun oluşturulması yalnızca birkaç dakika sürer.

## <a name="create-a-dedicated-host"></a>Adanmış konak oluşturma

Şimdi konak grubunda ayrılmış bir konak oluşturun. Konak için bir ada ek olarak, ana bilgisayar için SKU sağlamanız gerekir. Ana bilgisayar SKU 'SU, desteklenen VM serisini ve adanmış ana bilgisayarınız için donanım oluşturmayı yakalar.  Önizleme sırasında şu konak SKU değerlerini destekliyoruz: DSv3_Type1 ve ESv3_Type1.

Konak SKU 'Ları ve fiyatlandırma hakkında daha fazla bilgi için bkz. [Azure ayrılmış ana bilgisayar fiyatlandırması](https://aka.ms/ADHPricing).

Konak grubunuz için bir hata etki alanı sayısı ayarlarsanız, ana bilgisayarınız için hata etki alanını belirtmeniz istenir.  

1. Sol üst köşedeki **kaynak oluştur** ' u seçin.
1. **Adanmış ana bilgisayar** için arama yapın ve ardından sonuçlardan **adanmış konaklar (Önizleme)** seçeneğini belirleyin.

    ![Konak grupları arama sonucu.](./media/virtual-machines-common-dedicated-hosts-portal/host.png)
1. **Adanmış konaklar (Önizleme)** sayfasında **Oluştur**' u seçin.
1. Kullanmak istediğiniz aboneliği seçin.
1. **Kaynak grubu**olarak, *Myayrılmış hostsrg* öğesini seçin.
1. **Örnek ayrıntıları**' nda **ad** için *myhost* yazın ve konum için *Doğu ABD* seçin.
1. **Donanım profili**' nde, **Boyut ailesi**için *Standart Es3 Family-Type 1* ' i seçin, **konak grubu** için *myhostgroup* ' u seçin ve ardından **hata etki alanı**için *1* ' i seçin. Diğer alanları için varsayılan değerleri bırakın.
1. İşiniz bittiğinde, **gözden geçir + oluştur** ' u seçin ve doğrulama için bekleyin.

    ![Konak ayarları](./media/virtual-machines-common-dedicated-hosts-portal/host-settings.png)
1. **Doğrulama başarılı** iletisini gördüğünüzde, konağı oluşturmak için **Oluştur** ' u seçin.


