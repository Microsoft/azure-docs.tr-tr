---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: f188f2c7bea511f1109d37ef49563e0f745a770e
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/15/2019
ms.locfileid: "68385750"
---
Azure Resource Manager ile, şablonu dağıttığınızda kullanılacak değerler için parametreler tanımlayabilirsiniz. Şablon, tüm parametre `parameters` değerlerini içeren bir bölümü içerir. Her parametre değeri, dağıtmak istediğiniz kaynakları tanımlamak için şablon tarafından kullanılır.

> [!NOTE]
> Her zaman aynı kalan değerler için parametre tanımlamayın. Dağıtım yaptığınız projeye veya dağıttığınız ortama göre yalnızca değişiklik gösteren değerler için parametreleri tanımlayın.

Parametreleri tanımladığınızda:

* Bir kullanıcının dağıtım sırasında sağlayakullanabileceği izin verilen değerleri belirtmek için, **allowedValues** alanını kullanın.

* Dağıtım sırasında hiçbir değer sağlanmadığından parametreye varsayılan değerler atamak için **DefaultValue** alanını kullanın. 
