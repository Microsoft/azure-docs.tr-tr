---
title: Power BI uygulama teklifi için teklif ayarları | Azure Marketi
description: Microsoft AppSource Market için Power BI uygulama teklifi için teklif ayarlarını yapılandırın.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: ca326b10a0707d5e4b1a5f05dccc303c9ec28269
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822675"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Power BI uygulamalar teklif ayarları sekmesi

Hizmet uygulamaları için **yeni teklif** sayfasını açtığınızda, önce **teklif ayarları** sekmesini görürsünüz. Bu sekmede teklifinizin birincil tanımlayıcılarını ve adını sağlarsınız. Bir yıldız işareti (*) gerekli bir alanı gösterir.

![Teklif Ayarları sekmesi](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Teklif ayarları alanları 

**Teklif ayarları** sekmesinde, aşağıdaki gerekli alanlara bilgi girmeniz gerekir. Gerekli alanlar bir yıldız işareti (*) ile tanımlanır.

|  Alan        |  Açıklama                                                               |
|---------------|----------------------------------------------------------------------------|
| **Teklif KIMLIĞI\***  | Teklif için benzersiz bir tanımlayıcı (yayımcı profili içinde). Bu tanımlayıcı, ürün URL 'Lerinde, Azure Resource Manager şablonlarında ve faturalandırma raporlarında görünür olacaktır. En fazla 50 karakter uzunluğunda olur. Yalnızca küçük harfli alfasayısal karakterler ve tireler (-) içerebilir. Tireyle bitemez. Bu tanımlayıcı, bir teklif canlı olduktan sonra değiştirilemez. Contoso, teklif KIMLIĞI `sample-SvcApp`bir teklif yayımladığında, teklifin AppSource URL 'SI `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`atanır.      |
| **Yayımcı\*** | Kuruluşunuzun [Appsource](https://appsource.microsoft.com)'da benzersiz tanımlayıcısı. Tüm tekliflerinizin yayımcı KIMLIĞINIZLE ilişkilendirilmesi gerekir. Teklif kaydedildikten sonra bu değer değiştirilemez.                         |
| **Ad\***      | Teklifiniz için bir görünen ad. Bu ad AppSource üzerinde ve Bulut İş Ortağı Portalı görüntülenir. En fazla 50 karakter uzunluğunda olur. Ürününüz için tanınabilir bir marka adı kullanın. Uygulama bu adla pazarlanmadığı takdirde kuruluşunuzun adını buraya eklemeyin. Bu teklifi diğer web siteleri ve yayınlar için sağladıysanız, tüm yayınlarda aynı adı kullanın.    <br/>Power BI uygulamalar için önizleme dönemi boyunca bir teklifi serbest bırakırsanız, aşağıdaki gibi `(Preview)` dize adını uygulamanızın adının sonuna ekleyin: `Sample Scv App (Preview)`. |
|     |     |


## <a name="next-steps"></a>Sonraki adımlar

Bir sonraki sekmede teklifiniz için [teknik bilgileri](./cpp-technical-info-tab.md) belirtirsiniz.
