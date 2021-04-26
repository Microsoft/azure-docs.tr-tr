---
title: Azure portal kullanarak Cloud Services (genişletilmiş destek) için ARM şablonu oluşturma
description: Azure portal kullanarak Cloud Services (genişletilmiş destek) için ARM şablonu ve parametre dosyası oluşturun ve indirin
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 03/07/2021
ms.custom: ''
ms.openlocfilehash: a4f206d68df3cd8dd4dd5b1b411d316e7aacde92
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077132"
---
# <a name="generate-arm-template-for-cloud-services-extended-support-using-the-azure-portal"></a>Azure portal kullanarak Cloud Services (genişletilmiş destek) için ARM şablonu oluşturma

Bu makalede, bulut hizmetiniz için [Azure Portal](https://portal.azure.com) ARM şablonu ve parametre dosyasının nasıl indirileceği açıklanır. ARM şablonu ve parametre dosyası, bir bulut hizmeti oluşturmak veya güncelleştirmek için PowerShell aracılığıyla dağıtımda kullanılabilir

## <a name="get-arm-template-via-portal"></a>Portal aracılığıyla ARM şablonu al

  1. Azure portal gidin ve [Yeni bir bulut hizmeti oluşturun](deploy-portal.md). Bulut hizmeti yapılandırmanızı, paket ve tanım dosyalarınızı ekleyin. 
    :::image type="content" source="media/deploy-portal-4.png" alt-text="Görüntü oluşturma sırasında temel bilgiler sekmesinin karşıya yükleme bölümünü gösterir.":::
  
  2. Tüm alanlar tamamlandıktan sonra, dağıtım yapılandırmanızı doğrulamak için Inceleme ve Oluştur sekmesine gidin ve bulut hizmetinizi **Otomasyon Için indirme şablonu** ' na tıklayın (genişletilmiş destek).
    :::image type="content" source="media/download-template-portal-1.png" alt-text="Görüntü, Azure portal bulut hizmeti altında (genişletilmiş destek) şablonu indirmeyi gösterir.":::
  
  3. Şablonunuzu ve parametre dosyalarınızı indirin. 
    :::image type="content" source="media/generate-template-portal-3.png" alt-text="Görüntü, Azure portal şablon dosyasını indirmeyi gösterir.":::
  
  4. İnceleme ve oluşturma sekmesinden paket SAS URI 'SI ve yapılandırma SAS URI 'sini kopyalayın ve bunları dosyaya parameter.jsekleyin. Bu dosyalar artık PowerShell aracılığıyla yeni bir bulut hizmeti oluşturmak için kullanılabilir.
    :::image type="content" source="media/download-template-portal-2.png" alt-text="Görüntü, Azure portal paket SAS URI 'sini ve yapılandırma SAS URI parametrelerini gösterir.":::
  
## <a name="next-steps"></a>Sonraki adımlar 
- Cloud Services için [sık sorulan soruları](faq.md) gözden geçirin (genişletilmiş destek).
- [Azure Portal](deploy-portal.md) kullanarak bir bulut hizmeti (genişletilmiş destek) dağıtma
  
