---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 0019e50615f3e66778709ad8cb28f92967c66e2e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018466"
---
## <a name="deployment-customization"></a>Dağıtım özelleştirmesi

Dağıtım işlemi, gönderdiğiniz. zip dosyasının, bir çalıştırma uygulaması içerdiğini varsayar. Varsayılan olarak, hiçbir özelleştirme çalıştırılmaz. Sürekli tümleştirme ile aldığınız yapı süreçlerini etkinleştirmek için, uygulama ayarlarınıza aşağıdakileri ekleyin:

`SCM_DO_BUILD_DURING_DEPLOYMENT=true`

. Zip Push dağıtımını kullandığınızda, bu ayar varsayılan olarak **false 'tur** . Varsayılan değer sürekli tümleştirme dağıtımları için **geçerlidir** . **Doğru** olarak ayarlandığında, dağıtım sırasında dağıtımınız ile ilgili ayarlar kullanılır. Bu ayarları, uygulama ayarları veya. zip dosyanızın kökünde bulunan bir. Deployment yapılandırma dosyasında yapılandırabilirsiniz. Daha fazla bilgi için bkz. Dağıtım başvurusunda [Depo ve dağıtımla ilgili ayarlar](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings) .