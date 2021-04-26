---
title: Ölçüm uyarılarını Yapılandırma-Azure portal-MariaDB için Azure veritabanı
description: Bu makalede, Azure portal MariaDB için Azure veritabanı 'nın ölçüm uyarılarının nasıl yapılandırılacağı ve erişebileceği açıklanır.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 689e830d2486420be8d551d1f88dccba8d982428
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100593061"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mariadb"></a>MariaDB için Azure veritabanı ölçümleri hakkında uyarı ayarlamak için Azure portal kullanın

Bu makalede, Azure portal kullanarak MariaDB uyarıları için Azure veritabanı 'nın nasıl ayarlanacağı gösterilmektedir. Azure hizmetleriniz için izleme ölçümlerini temel alan bir uyarı alabilirsiniz.

Uyarı, belirtilen bir ölçümün değeri atadığınız bir eşiği aştığında tetiklenir. Uyarı hem koşul ilk karşılandığında hem de daha sonra bu koşul karşılanamadığında daha sonra tetiklenir.

Bir uyarıyı, tetiklendiğinde aşağıdaki eylemleri yapmak üzere yapılandırabilirsiniz:
* Hizmet yöneticisine ve ortak yöneticilere e-posta bildirimleri gönderme
* Belirttiğiniz ek e-postalara e-posta gönderin.
* Web kancası çağırma

Kullanarak uyarı kuralları hakkında bilgi alabilir ve bunları alabilirsiniz:
* [Azure portalı](../azure-monitor/alerts/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../azure-monitor/alerts/alerts-metric.md#with-azure-cli)
* [Azure İzleyici REST API'si](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric"></a>Ölçümle ilgili uyarı kuralı oluşturma
1. [Azure Portal](https://portal.azure.com/), Izlemek Istediğiniz MariaDB sunucusu Için Azure veritabanı ' nı seçin.

2. Kenar çubuğunun **izleme** bölümü altında gösterildiği gibi **Uyarılar** ' ı seçin:

   ![Uyarı kurallarını seçin](./media/howto-alert-metric/2-alert-rules.png)

3. **Ölçüm uyarısı Ekle** (+ simgesi) seçeneğini belirleyin.

4. **Kural oluştur** sayfası aşağıda gösterildiği gibi açılır. Gereken bilgileri doldurun:

   ![Ölçüm uyarısı formu Ekle](./media/howto-alert-metric/4-add-rule-form.png)

5. **Koşul** bölümünde **Koşul Ekle**' yi seçin.

6. Uyarı almak için sinyaller listesinden bir ölçüm seçin. Bu örnekte, "depolama alanı yüzdesi" ni seçin.
   
   ![Ölçüm seçin](./media/howto-alert-metric/6-configure-signal-logic.png)

7. **Koşul** (örn.) gibi uyarı mantığını yapılandırın. "Büyüktür"), **eşik** (örn. yüzde 85), **zaman toplama**, ölçüm kuralının uyarı tetikleyiciden önce karşılanması **gereken süre (** örn. "Son 30 dakika içinde") ve **Sıklık**.
   
   Tamamlandığında **bitti** ' yi seçin.

   ![Ölçüm 2 ' yi seçin](./media/howto-alert-metric/7-set-threshold-time.png)

8. Uyarı üzerinde bildirim almak üzere yeni bir grup oluşturmak için **eylem grupları** bölümünde **Yeni oluştur** ' u seçin.

9. "Eylem grubu Ekle" formunu bir ad, kısa ad, abonelik ve kaynak grubuyla doldurun.

10. **E-posta/SMS/Push/Voice** eylem türünü yapılandırın.
    
    Bildirimleri almak için abonelik sahipleri, katkıda bulunanlar ve okuyucular seçmek üzere "e-posta Azure Resource Manager rolü" nü seçin.
   
    İsteğe bağlı olarak, uyarı tetiklendiğinde çağırılabilmesi istiyorsanız **Web kancası** alanında GEÇERLI bir URI sağlayın.

    Tamamlandığında **Tamam ' ı** seçin.

    ![Eylem grubu](./media/howto-alert-metric/10-action-group-type.png)

11. Bir uyarı kuralı adı, açıklaması ve önem derecesi belirtin.

    ![Eylem grubu 2](./media/howto-alert-metric/11-name-description-severity.png) 

12. Uyarı oluşturmak için **Uyarı kuralı oluştur** ' u seçin.

    Birkaç dakika içinde, uyarı etkin ve daha önce açıklandığı gibi tetikler.

## <a name="manage-your-alerts"></a>Uyarılarınızı yönetme
Bir uyarı oluşturduktan sonra, bunu seçebilir ve aşağıdaki eylemleri gerçekleştirebilirsiniz:

* Ölçüm eşiğini ve Bu uyarıyla ilgili olan önceki günün gerçek değerlerini gösteren bir grafik görüntüleyin.
* Uyarı kuralını **düzenleyin** veya **silin** .
* Bildirimleri almayı geçici olarak durdurmak veya yeniden başlatmak istiyorsanız, uyarıyı **devre dışı bırakın** veya **etkinleştirin** .


## <a name="next-steps"></a>Sonraki adımlar
* [Uyarılarda Web kancalarını yapılandırma](../azure-monitor/alerts/alerts-webhooks.md)hakkında daha fazla bilgi edinin.
* Hizmetinizin kullanılabilir olduğundan ve yanıt verebilmesini sağlamak için [ölçüm koleksiyonuna genel bakış](../azure-monitor/data-platform.md) alın.