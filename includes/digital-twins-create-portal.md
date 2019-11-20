---
title: include dosyası
description: include dosyası
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 11/12/2019
ms.custom: include file
ms.openlocfilehash: 4ed5be09d952d4d64c269e3eaf698ad7a74fffdd
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014070"
---
1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Giriş yan çubuğunu seçin ve ardından **kaynak oluştur ' a**tıklayın. 

   [![giriş yan çubuğunu genişletip + kaynak oluştur ' u seçin.](./media/create-digital-twins-portal/create-a-resource.png)](./media/create-digital-twins-portal/create-a-resource.png#lightbox)

1. **Dijital TWINS**araması yapın ve **dijital TWINS**' i seçin. 

   [Yeni bir dijital TWINS örneği oluşturmak için ![seçimleri](./media/create-digital-twins-portal/create-digital-twins.png)](./media/create-digital-twins-portal/create-digital-twins.png#lightbox)

   Alternatif olarak, **nesnelerin interneti**öğesini seçin ve **dijital TWINS (Önizleme)** öğesini seçin.

1. Dağıtım işlemini başlatmak için **Oluştur**’u seçin.

   [![kaynağın dağıtımını oluşturma ve onaylama](./media/create-digital-twins-portal/create-and-confirm-resource.png)](./media/create-digital-twins-portal/create-and-confirm-resource.png#lightbox)

1. **Digital Twins** bölmesine şu bilgileri girin:
   * **Kaynak Adı**: Digital Twins örneğiniz için benzersiz bir ad oluşturun.
   * **Abonelik**: Bu Digital Twins örneğini oluşturmak için kullanmak istediğiniz aboneliği seçin. 
   * **Kaynak grubu**: Digital Twins örneği için bir [kaynak grubu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) seçin veya oluşturun.
   * **Konum**: Cihazlarınıza en yakın konumu seçin.

     [girilen bilgilerle dijital TWINS bölmesi ![](./media/create-digital-twins-portal/create-digital-twins-param.png)](./media/create-digital-twins-portal/create-digital-twins-param.png#lightbox)

1. Dijital TWINS bilgilerinizi gözden geçirin ve ardından **Oluştur**' u seçin. Dijital TWINS örneğinizin oluşturulması birkaç dakika sürebilir. İlerleme durumunu **Bildirimler** bölmesinden izleyebilirsiniz.

1. Digital Twins örneğinizin **Genel Bakış** bölmesini açın. **Yönetim API 'si**altındaki bağlantıyı aklınızda edin. **YÖNETIM API** URL 'si şöyle biçimlendirilir: 
   
   ```URL
   https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger
   ```
   
   Bu URL, örneğiniz için geçerli olan Azure Digital Twins REST API belgesini açar. Bu API belgelerini okumayı ve kullanmayı öğrenmek için bkz. [Azure Digital Twins Swagger'ı kullanma](../articles/digital-twins/how-to-use-swagger.md). **YÖNETIM API** 'si URL 'sini şu biçimde kopyalayın ve değiştirin: 
    
   ```URL
   https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/
   ```
    
   Uygulamanız, değiştirilen URL'yi örneğinize erişmek için temel URL olarak kullanır. Değiştirdiğiniz URL'yi geçici bir dosyaya kopyalayın. Bu, sonraki bölümde gerekecektir.

   [![yönetim API 'sine genel bakış](./media/create-digital-twins-portal/digital-twins-management-api.png)](./media/create-digital-twins-portal/digital-twins-management-api.png#lightbox)