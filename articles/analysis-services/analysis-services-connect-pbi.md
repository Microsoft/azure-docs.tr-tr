---
title: Power BI ile Azure Analysis Services bağlanma | Microsoft Docs
description: Power BI kullanarak bir Azure Analysis Services sunucusuna nasıl bağlanacağınızı öğrenin. Bağlandıktan sonra kullanıcılar model verilerini keşfedebilir.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: db0c00268c343cd99e439bb49460523cf0563c3c
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73573110"
---
# <a name="connect-with-power-bi"></a>Power BI ile bağlanma

Azure 'da bir sunucu oluşturup buna bir tablosal Model dağıttıktan sonra, kuruluşunuzdaki kullanıcılar bağlanmaya ve verileri keşfetmeye başlamaya hazırsınız. 

> [!TIP]
> [Power BI Desktop](https://powerbi.microsoft.com/desktop/)en son sürümünü kullandığınızdan emin olun.
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Power BI Desktop'ta bağlanma

1. Power BI Desktop'ta, **Veri Al** > **Azure** > **Azure Analysis Services veritabanı**'na tıklayın.

2. **Sunucu**' da sunucu adını girin. Tam URL 'YI eklediğinizden emin olun; Örneğin, asazure://westcentralus.asazure.windows.net/advworks.

3. **Veritabanında**, bağlamak istediğiniz tablosal model veritabanının veya perspektifinin adını biliyorsanız buraya yapıştırın. Aksi takdirde, bu alanı boş bırakıp daha sonra bir veritabanı veya perspektif seçebilirsiniz.

4. Bir bağlantı seçeneği seçin ve ardından **Bağlan**' a basın. 

    Hem **canlı Bağlan** hem de **içeri aktarma** seçenekleri desteklenir. Ancak, Içeri aktarma modunda bazı sınırlamalar olduğundan canlı bağlantılar kullanmanız önerilir; özellikle sunucu performansı, içeri aktarma sırasında etkilenebilir. Ayrıca, model Power BI hizmeti yenilenirse, **Power BI erişime Izin ver** ayarı yalnızca **canlı Bağlan**' ı seçerken geçerlidir.

5. İstenirse, oturum açma kimlik bilgilerinizi girin. 

6. **Gezgin**'de, sunucuyu genişletin, ardından bağlanmak istediğiniz modeli veya perspektifi seçin ve ardından **Bağlan**' a tıklayın. Bu görünüm için tüm nesneleri göstermek üzere bir modele veya perspektife tıklayın.

    Model, rapor görünümünde boş bir raporla Power BI Desktop açılır. Alanlar listesi, tüm gizli olmayan model nesnelerini görüntüler. Bağlantı durumu sağ alt köşede gösterilir.

## <a name="connect-in-power-bi-service"></a>Power BI (hizmet) içinde Bağlan

1. Sunucunuzdaki modelinize canlı bağlantısı olan Power BI Desktop bir dosya oluşturun.
2. [Power BI](https://powerbi.microsoft.com), veri > **dosyalarını** **Al** ' a tıklayın ve ardından. pbix dosyanızı bulun ve seçin.

## <a name="see-also"></a>Ayrıca bkz.
[Azure Analysis Services  bağlanma](analysis-services-connect.md)  
[İstemci kitaplıkları](analysis-services-data-providers.md)

