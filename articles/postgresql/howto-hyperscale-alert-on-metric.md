---
title: PostgreSQL için Azure veritabanı için ölçüm uyarılarını yapılandırma-hiper ölçek (Citus)
description: Bu makalede PostgreSQL için Azure veritabanı-hiper ölçek (Citus) için ölçüm uyarılarını yapılandırma ve erişme açıklanmaktadır
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d5f45df16ac294f50e99cc7e05ab6eba43c0ae85
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516025"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---hyperscale-citus"></a>PostgreSQL için Azure veritabanı-hiper ölçek (Citus) ölçümlerinde uyarıları ayarlamak için Azure portal kullanın

Bu makalede, Azure portal kullanarak PostgreSQL için Azure veritabanı uyarılarını ayarlama konusu gösterilmektedir. Azure hizmetleriniz için izleme ölçümlerini temel alan bir uyarı alabilirsiniz.

Belirtilen bir ölçümün değeri bir eşiği aştığında tetiklenecek bir uyarı ayarlayacağız. Uyarı, koşul ilk karşılandığında tetiklenir ve daha sonra tetiklemeye devam eder.

Bir uyarıyı, tetiklendiğinde aşağıdaki eylemleri yapmak üzere yapılandırabilirsiniz:
* Hizmet yöneticisine ve ortak yöneticilere e-posta bildirimleri gönderin.
* Belirttiğiniz ek e-postalara e-posta gönderin.
* Web kancası çağırın.

Kullanarak uyarı kuralları hakkında bilgi alabilir ve bunları alabilirsiniz:
* [Azure Portal](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [Azure Izleyici REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Azure portal bir ölçümde uyarı kuralı oluşturma
1. [Azure Portal](https://portal.azure.com/), Izlemek Istediğiniz PostgreSQL Için Azure veritabanı sunucusu ' nu seçin.

2. Kenar çubuğunun **izleme** bölümü altında gösterildiği gibi **Uyarılar** ' ı seçin:

   ![Uyarı kurallarını seçin](./media/howto-hyperscale-alert-on-metric/2-alert-rules.png)

3. **Yeni uyarı kuralı** (+ simge) seçeneğini belirleyin.

4. **Kural oluştur** sayfası aşağıda gösterildiği gibi açılır. Gerekli bilgileri girin:

   ![Ölçüm uyarısı formu Ekle](./media/howto-hyperscale-alert-on-metric/4-add-rule-form.png)

5. **Koşul** bölümünde **Ekle**' yi seçin.

6. Uyarı almak için sinyaller listesinden bir ölçüm seçin. Bu örnekte, "depolama alanı yüzdesi" ni seçin.
   
   ![Ölçüm seçin](./media/howto-hyperscale-alert-on-metric/6-configure-signal-logic.png)

7. Uyarı mantığını yapılandırın:

    * **İşleç** (örn. "Büyüktür")
    * **Eşik değeri** (örn. yüzde 85)
    * **Toplama ayrıntı düzeyi** , uyarı tetikleyiciden önce ölçüm kuralının karşılanması gereken süre (örn. "Son 30 dakika içinde")
    * ve **değerlendirme sıklığı** (örn. "1 dakika")
   
   Tamamlandığında **bitti** ' yi seçin.

   ![Ölçüm seçin](./media/howto-hyperscale-alert-on-metric/7-set-threshold-time.png)

8. Uyarı üzerinde bildirim almak üzere yeni bir grup oluşturmak için **eylem grupları** bölümünde **Yeni oluştur** ' u seçin.

9. "Eylem grubu Ekle" formunu bir ad, kısa ad, abonelik ve kaynak grubuyla doldurun.

    ![Eylem grubu](./media/howto-hyperscale-alert-on-metric/9-add-action-group.png)

10. **E-posta/SMS/Push/Voice** eylem türünü yapılandırın.
    
    Abonelik sahiplerine, katkıda bulunanlar ve okuyucular için bildirim göndermek üzere "e-posta Azure Resource Manager rolü" ni seçin.
   
    Tamamlandığında **Tamam ' ı** seçin.

    ![Eylem grubu](./media/howto-hyperscale-alert-on-metric/10-action-group-type.png)

11. Bir uyarı kuralı adı, açıklaması ve önem derecesi belirtin.

    ![Eylem grubu](./media/howto-hyperscale-alert-on-metric/11-name-description-severity.png) 

12. Uyarı oluşturmak için **Uyarı kuralı oluştur** ' u seçin.

    Birkaç dakika içinde, uyarı etkin ve daha önce açıklandığı gibi tetikler.

## <a name="manage-your-alerts"></a>Uyarılarınızı yönetin

Bir uyarı oluşturduktan sonra, bunu seçebilir ve aşağıdaki eylemleri gerçekleştirebilirsiniz:

* Ölçüm eşiğini ve Bu uyarıyla ilgili olan önceki günün gerçek değerlerini gösteren bir grafik görüntüleyin.
* Uyarı kuralını **düzenleyin** veya **silin** .
* Bildirimleri almayı geçici olarak durdurmak veya yeniden başlatmak istiyorsanız, uyarıyı **devre dışı bırakın** veya **etkinleştirin** .

## <a name="next-steps"></a>Sonraki adımlar
* [Uyarılarda Web kancalarını yapılandırma](../azure-monitor/platform/alerts-webhooks.md)hakkında daha fazla bilgi edinin.
* Hizmetinizin kullanılabilir olduğundan ve yanıt verebilmesini sağlamak için [ölçüm koleksiyonuna genel bakış](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) alın.
