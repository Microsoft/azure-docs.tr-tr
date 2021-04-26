---
title: Azure VMware Çözüm dağıtımı veya sağlama hatası desteği
description: Azure VMware Çözüm dağıtımı veya sağlama hatası için bir hizmet isteği sağlamak üzere Azure VMware Çözüm özel bulutunuzun bilgilerini alın.
ms.topic: how-to
ms.date: 10/28/2020
ms.openlocfilehash: 27b645f4ca225fdd74bca6499b6581b3803e41a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94542414"
---
# <a name="open-a-support-request-for-an-azure-vmware-solution-deployment-or-provisioning-failure"></a>Azure VMware Çözüm dağıtımı veya sağlama hatası için bir destek isteği açın

Bu makalede bir [destek isteğinin](https://rc.portal.azure.com/#create/Microsoft.Support) nasıl açılacağı ve bir Azure VMware Çözüm dağıtımı veya sağlama hatası için anahtar bilgileri sağlayabilmeniz gösterilmektedir. 

Özel bulutunuzda bir hata olduğunda, Azure portal bir destek isteği açmanız gerekir. Bir destek isteği açmak için öncelikle Azure portal bazı önemli bilgileri alın:

- Bağıntı Kimliği
- Azure ExpressRoute bağlantı hattı KIMLIĞI
- Hata iletileri

## <a name="get-the-correlation-id"></a>Bağıntı KIMLIĞINI al
 
Azure 'da özel bir bulut veya herhangi bir kaynak oluşturduğunuzda kaynak için bir bağıntı KIMLIĞI otomatik olarak oluşturulur. İsteği daha hızlı açmak ve çözmek için destek isteğinize özel bulut bağıntı KIMLIĞINI dahil edin.

Azure portal, bir kaynağın bağıntı KIMLIĞINI iki şekilde edinebilirsiniz:

* **Genel bakış** bölmesi
* Dağıtım günlükleri
 
 ### <a name="get-the-correlation-id-from-the-resource-overview"></a>Kaynak genel bakış 'dan bağıntı KIMLIĞINI alın

İlişki KIMLIĞI seçili olan başarısız bir özel bulut dağıtımının işlem ayrıntılarına bir örnek aşağıda verilmiştir:

:::image type="content" source="media/fix-deployment-provisioning-failures/failed-private-cloud-deployment.png" alt-text="Bağıntı KIMLIĞI seçili olan başarısız bir özel bulut dağıtımını gösteren ekran görüntüsü.":::

Özel bir buluta **genel bakış** bölmesinde dağıtım sonuçlarına erişmek için:

1. Azure portal özel bulutunuzu seçin.

1. Sol menüde **genel bakış**' ı seçin.

Dağıtım başlatıldıktan sonra, dağıtımın sonuçları özel buluta **genel bakış** bölmesinde gösterilir.

Hizmet isteğine dahil etmek için özel bulut dağıtımı bağıntı KIMLIĞINI kopyalayın ve kaydedin.

### <a name="get-the-correlation-id-from-the-deployment-log"></a>Dağıtım günlüğünden bağıntı KIMLIĞI al

Dağıtım etkinlik günlüğünü Azure portal arayarak başarısız bir dağıtım için bağıntı KIMLIĞINI alabilirsiniz.

Dağıtım günlüğüne erişmek için:

1. Azure portal özel bulutunuzu seçin ve ardından Bildirimler simgesini seçin.

   :::image type="content" source="media/fix-deployment-provisioning-failures/open-notifications.png" alt-text="Azure portal Bildirimler simgesini gösteren ekran görüntüsü.":::

1. **Bildirimler** bölmesinde **etkinlik günlüğünde diğer olaylar**' ı seçin:

    :::image type="content" source="media/fix-deployment-provisioning-failures/more-events-in-activity-log.png" alt-text="Bildirimler bölmesinde seçilen etkinlik günlüğü bağlantısında daha fazla olayı gösteren ekran görüntüsü.":::

1. Başarısız dağıtımı ve bağıntı KIMLIĞINI bulmak için kaynağın adını veya kaynağı oluşturmak için kullandığınız diğer bilgileri arayın. 

    Aşağıdaki örnek, PC03 adlı bir özel bulut kaynağı için arama sonuçlarını gösterir.
 
    :::image type="content" source="media/fix-deployment-provisioning-failures/find-past-deployments.png" alt-text="Örnek bir özel bulut kaynağı ve bir PrivateCloud oluştur veya Güncelleştir bölmesi için arama sonuçlarını gösteren ekran görüntüsü.":::
 
1. **Etkinlik günlüğü** bölmesindeki arama sonuçlarında, başarısız dağıtımın işlem adını seçin.

1. **PrivateCloud oluştur veya Güncelleştir** bölmesinde **JSON** sekmesini seçin ve `correlationId` gösterilen günlükte öğesine bakın. Değeri, `correlationId` destek isteğinize dahil etmek için kopyalayın. 
 
## <a name="copy-error-messages"></a>Hata iletilerini Kopyala

Dağıtım sorununuzu çözmeye yardımcı olmak için Azure portal gösterilen tüm hata iletilerini ekleyin. Hataların özetini görmek için bir uyarı iletisi seçin:
 
:::image type="content" source="media/fix-deployment-provisioning-failures/summary-of-errors.png" alt-text="Hatalar bölmesinin Özet sekmesinde, Kopyala simgesi seçili olarak hata ayrıntılarını gösteren ekran görüntüsü.":::

Hata iletisini kopyalamak için Kopyala simgesini seçin. Kopyalanmış iletiyi, destek isteğinize dahil etmek üzere kaydedin.
 
## <a name="get-the-expressroute-id-uri"></a>ExpressRoute KIMLIĞI (URI) Al
 
Özel bulut ExpressRoute bağlantı hattını kullanarak var olan bir özel bulutu ölçeklendirmeye veya ona eşlemenize çalışıyor olabilirsiniz. Bu senaryoda, destek isteğinize dahil etmek için ExpressRoute KIMLIĞI gereklidir.

ExpressRoute KIMLIĞINI kopyalamak için:

1. Azure portal özel bulutunuzu seçin.
1. Sol taraftaki menüde, **Yönet** altında **bağlantı**' yı seçin. 
1. Sağ bölmede **ExpressRoute** sekmesini seçin.
1. **ExpressRoute kimliği** için Kopyala simgesini seçin ve destek talebinizdeki kullanılacak değeri kaydedin.
 
:::image type="content" source="media/fix-deployment-provisioning-failures/expressroute-id.png" alt-text="ExpressRoute KIMLIĞINI panoya kopyalayın."::: 
 
## <a name="pre-validation-failures"></a>Ön doğrulama sorunları

Özel bulut ön doğrulama denetimi başarısız olduysa (dağıtımdan önce), bir bağıntı KIMLIĞI üretilmez. Bu senaryoda, destek isteğinize aşağıdaki bilgileri verebilirsiniz:

- Hata ve hata iletileri. Bu iletiler birçok hatalarda, örneğin, kotayla ilgili sorunlar için yardımcı olabilir. Bu iletileri kopyalamak ve bu makalede açıklandığı gibi destek isteğine eklemek önemlidir.
- Aşağıdakiler dahil olmak üzere Azure VMware çözümü özel bulutu oluşturmak için kullandığınız bilgiler:
  - Konum
  - Kaynak grubu
  - Kaynak adı

## <a name="create-your-support-request"></a>Destek isteğinizi oluşturun

Destek isteği oluşturma hakkında genel bilgi için bkz. [Azure destek isteği oluşturma](../azure-portal/supportability/how-to-create-azure-support-request.md). 

Azure VMware Çözüm dağıtımı veya sağlama hatası için bir destek isteği oluşturmak için:

1. Azure portal, **Yardım** simgesini seçin ve ardından **Yeni destek isteği**' ni seçin.

    :::image type="content" source="media/fix-deployment-provisioning-failures/open-sr-on-avs.png" alt-text="Azure portal yeni destek isteği bölmesinin ekran görüntüsü.":::

1. Gerekli bilgileri girin veya seçin:

   1. **Temel Bilgiler** sekmesinde:

      1. **Sorun türü** için **yapılandırma ve kurulum sorunları**' nı seçin.

      1. **Sorun alt türü** için **özel bulut sağla**' yı seçin.

   1. **Ayrıntılar** sekmesinde:

      1. Gerekli bilgileri girin veya seçin.

      1. Bağıntı KIMLIĞINIZI veya ExpressRoute KIMLIĞINIZI bu bilgilerin istendiği yere yapıştırın. Bu değerler için belirli bir metin kutusu görmüyorsanız, **sorunu, sorun hakkındaki ayrıntıları sağla** metin kutusuna yapıştırın.

    1. Kopyaladığınız hata veya hata iletileri dahil olmak üzere hata ayrıntılarını, **sorun hakkındaki ayrıntıları girin** metin kutusuna yapıştırın.

1. Girdilerinizi gözden geçirin ve sonra destek isteğinizi oluşturmak için **Oluştur** ' u seçin.
