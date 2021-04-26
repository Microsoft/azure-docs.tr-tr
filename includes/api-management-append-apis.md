---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 04/16/2021
ms.author: vlvinogr
ms.openlocfilehash: 329ea156b296810395eb7b8e8310bed5ee0ee4c9
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107601931"
---
## <a name="append-other-apis"></a>Diğer API'leri ekleme

Farklı hizmetler tarafından sunulan API 'lerin bir API 'sini şu şekilde oluşturabilirsiniz:
* Openapı belirtimi
* BIR SOAP APı 'SI
* Azure App Service API Apps özelliği
* Azure İşlev Uygulaması
* Azure Logic Apps
* Azure Service Fabric

Aşağıdaki adımları kullanarak var olan API 'nize farklı bir API ekleyin. 

>[!NOTE] 
> Başka bir API'yi içeri aktardığınızda işlemler geçerli API'nize eklenir.

1. Azure portal'da Azure API Management örneğinize gidin.

    :::image type="content" source="./media/api-management-append-apis/service-page.png" alt-text="Azure API MGMT örneğine git":::

1. Soldaki menüden **API'ler** seçeneğini belirleyin.

    :::image type="content" source="./media/api-management-append-apis/api-select.png" alt-text="API 'Leri seçin":::

1. Başka bir API eklemek istediğiniz API'nin yanındaki **...** simgesine tıklayın.
1. Açılan menüden **İçeri aktar**'ı seçin.

    :::image type="content" source="./media/api-management-append-apis/append-01.png" alt-text="İçeri Aktar 'ı seçin":::

1. API'nin içeri aktarılacağı hizmeti seçin.

    :::image type="content" source="./media/api-management-append-apis/select-to-import.png" alt-text="Hizmet seçin":::