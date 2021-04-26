---
title: Power BI ile Azure Analysis Services bağlanma | Microsoft Docs
description: Power BI kullanarak bir Azure Analysis Services sunucusuna nasıl bağlanacağınızı öğrenin. Bağlandıktan sonra kullanıcılar model verilerini keşfedebilir.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 4/20/2021
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c5a430c5bb24032a2665ad078311dcb5137d2bb9
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816052"
---
# <a name="connect-with-power-bi"></a>Power BI ile bağlanma

Azure 'da bir sunucu oluşturup buna bir tablosal Model dağıttıktan sonra, kuruluşunuzdaki kullanıcılar bağlanmaya ve verileri keşfetmeye başlamaya hazırsınız. 

> [!NOTE]
> Power BI hizmeti bir Power BI Desktop modeli yayımlıyorsa, Azure Analysis Services sunucusunda Case-Sensitive harmanlama sunucusu özelliğinin seçili olmadığından emin olun (varsayılan). Case-Sensitive Server özelliği SQL Server Management Studio kullanılarak ayarlanabilir.
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Power BI Desktop'ta bağlanma

1. Power BI Desktop, **veri al**  >  **Azure**  >  **Azure Analysis Services veritabanı**' na tıklayın.

2. **Sunucu**' da sunucu adını girin. Tam URL 'YI eklediğinizden emin olun; Örneğin, asazure://westcentralus.asazure.windows.net/advworks.

3. **Veritabanında**, bağlamak istediğiniz tablosal model veritabanının veya perspektifinin adını biliyorsanız buraya yapıştırın. Aksi takdirde, bu alanı boş bırakıp daha sonra bir veritabanı veya perspektif seçebilirsiniz.

4. Bir bağlantı seçeneği seçin ve ardından **Bağlan**' a basın. 

    Hem **canlı Bağlan** hem de **içeri aktarma** seçenekleri desteklenir. Ancak, Içeri aktarma modunda bazı sınırlamalar olduğundan canlı bağlantılar kullanmanız önerilir; özellikle sunucu performansı, içeri aktarma sırasında etkilenebilir. Ayrıca, model Power BI hizmeti yenilenirse, **Power BI erişime Izin ver** ayarı yalnızca **canlı Bağlan**' ı seçerken geçerlidir.

5. İstenirse, oturum açma kimlik bilgilerinizi girin. 

   > [!NOTE]
   > Bir kerelik geçiş kodu (OTP) hesapları desteklenmez. 

6. **Gezgin**'de, sunucuyu genişletin, ardından bağlanmak istediğiniz modeli veya perspektifi seçin ve ardından **Bağlan**' a tıklayın. Bu görünüm için tüm nesneleri göstermek üzere bir modele veya perspektife tıklayın.

    Model, rapor görünümünde boş bir raporla Power BI Desktop açılır. Alanlar listesinde gizli olmayan tüm model nesneleri görüntülenir. Bağlantı durumu sağ alt köşede gösterilir.

## <a name="connect-in-power-bi-service"></a>Power BI (hizmet) içinde Bağlan

1. Sunucunuzdaki modelinize canlı bağlantısı olan Power BI Desktop bir dosya oluşturun.
2. [Power BI](https://powerbi.microsoft.com), **veri**  >  **dosyalarını** Al ' a tıklayın ve ardından. pbix dosyanızı bulup seçin.

## <a name="see-also"></a>Ayrıca bkz.
[Azure Analysis Services Bağlan](analysis-services-connect.md)   
[İstemci kitaplıkları](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true)
