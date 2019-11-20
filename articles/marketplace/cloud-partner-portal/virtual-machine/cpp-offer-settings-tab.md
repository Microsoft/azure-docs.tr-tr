---
title: Azure Marketi için Bulut İş Ortağı Portalı sanal makine teklifi ayarları sekmesi
description: Azure Marketi VM teklifi oluşturma konusunda kullanılan teklif ayarları sekmesini açıklar.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: 6f7b90f6b02999869026db24836091233692143c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824434"
---
# <a name="virtual-machine-offer-settings-tab"></a>Sanal Makine teklifi ayarları sekmesi

Sanal makineler için **yeni teklif** sayfası, **teklif ayarları**adlı ilk sekmede açılır.  

![Sanal makineler için yeni teklif sayfası](./media/publishvm_004.png)


## <a name="offer-settings-fields"></a>Teklif ayarları alanları

**Teklif ayarları** sekmesinde, aşağıdaki alanları sağlamanız gerekir.  Alan adı üzerinde eklenen bir yıldız işareti (*) gerekli olduğunu gösterir. 

|  **Alan**       |     **Açıklama**                                                          |
|  ---------       |     ---------------                                                          |
| **Teklif KIMLIĞI\***   | Teklif için benzersiz bir tanımlayıcı (yayımcı profili içinde). Bu tanımlayıcı, ürün URL 'Lerinde, Azure Resource Manager şablonlarında ve faturalandırma raporlarında görünür olacaktır. En fazla 50 karakter uzunluğunda olabilir, yalnızca küçük harf alfasayısal karakterler ve kısa çizgilerden (-) oluşabilir, ancak kısa çizgi ile bitemez. Bu alan, bir teklif canlı olduktan sonra değiştirilemez. <br> Örneğin, contoso teklif KIMLIĞI **örneği-VM**ile bir teklif yayımladığında, Azure Market URL 'si `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview` atanır. |
| **Yayımcı\***  | Kuruluşunuzun Azure Marketi 'nde benzersiz tanımlayıcısı. Tüm tekliflerinizin yayımcı KIMLIĞINIZLE ilişkilendirilmesi gerekir. Teklif kaydedildikten sonra bu değer değiştirilemez. |
| **Ad\***       | Teklifiniz için görünen ad. Bu ad, Azure Marketi 'nde ve Bulut İş Ortağı Portalı görüntülenecektir. En fazla 50 karakter olabilir. Buradaki kılavuz, ürününüz için tanınabilir bir marka adı içermektedir. Pazarlanmadığı müddetçe kuruluşunuzun adını buraya eklemeyin. Bu teklifi diğer web siteleri ve yayımlarda pazartadıysanız, adın tüm yayınlarda tam olarak aynı olduğundan emin olun. |
|   |   |
 
Tüm alanları sağladıktan sonra **Kaydet** ' e tıklayın. 


## <a name="next-steps"></a>Sonraki adımlar

Bir sonraki sekmede, teklifiniz için [SKU 'ları](./cpp-skus-tab.md) ekleyeceksiniz.
