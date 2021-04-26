---
title: Yönetilen örnek (Azure portal) için uyarıları ve bildirimleri ayarlayın
description: Belirttiğiniz koşullar karşılandığında bildirimleri veya Otomasyonu tetikleyebilen SQL yönetilen örnek uyarıları oluşturmak için Azure portal kullanın.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 05/04/2020
ms.openlocfilehash: d37465223000399aa42895626c48426ddadc0c72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100592718"
---
# <a name="create-alerts-for-azure-sql-managed-instance-using-the-azure-portal"></a>Azure portalı kullanarak Azure SQL Yönetilen Örneği için uyarılar oluşturma
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Bu makalede, Azure SQL yönetilen örnek veritabanındaki veritabanları için Azure portal kullanarak nasıl uyarı ayarlanacağı gösterilmektedir. Uyarılar; örnek depolama boyutu veya CPU kullanımı gibi belirli bir ölçümün önceden tanımlanmış olan eşiğe ulaşması durumunda size e-posta gönderebilir, bir web kancasını çağırabilir, bir Azure İşlevi yürütebilir, runbook çalıştırabilir, ITSM uyumlu bir dış bilet sistemini çağırabilir, sizi arayabilir veya SMS gönderebilir. Bu makale, uyarı dönemlerini ayarlamaya yönelik en iyi yöntemleri de sağlar.


## <a name="overview"></a>Genel Bakış

Azure hizmetleriniz, veya üzerindeki olaylar için izleme ölçümlerini temel alarak bir uyarı alabilirsiniz.

* **Ölçüm değerleri** -belirtilen bir ölçümün değeri her iki yönde atadığınız bir eşiği aştığında, uyarı tetiklenir. Yani, hem koşul ilk karşılandığında hem de daha sonra bu koşul karşılanamadığında bu durum tetiklenir.

Bir uyarıyı, tetiklendiğinde aşağıdakileri yapmak için yapılandırabilirsiniz:

* Hizmet yöneticisine ve ortak yöneticilere e-posta bildirimleri gönderme
* Belirttiğiniz ek e-postalara e-posta gönderin.
* Sesli İstemiyle telefon numarası çağırma
* Telefon numarasına kısa mesaj gönder
* Web kancası çağırma
* Azure Işlevini çağır
* Azure runbook 'u çağırma
* Dış anahtar kullanımı ile uyumlu bir sistem çağrısı yapın

[Azure Portal, PowerShell veya Azure CLI](../../azure-monitor/alerts/alerts-classic-portal.md) veya [Azure izleyici REST API](/rest/api/monitor/alertrules)kullanarak uyarı kuralları hakkında bilgi alabilirsiniz. 

## <a name="alerting-metrics-available-for-managed-instance"></a>Yönetilen örnek için kullanılabilir uyarı ölçümleri

> [!IMPORTANT]
> Uyarı ölçümleri yalnızca yönetilen örnek için kullanılabilir. Yönetilen örnekteki ayrı veritabanları için uyarı ölçümleri kullanılamıyor. Veritabanı tanılama telemetrisi, [tanılama günlükleri](../database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md#diagnostic-telemetry-for-export)biçimindeki diğer taraftan kullanılabilir. Tanılama günlüklerindeki uyarılar, yönetilen örnek için [günlük uyarısı betikleri](../../azure-monitor/insights/azure-sql.md#creating-alerts-for-sql-managed-instance) kullanılarak [SQL Analytics](../../azure-monitor/insights/azure-sql.md) ürünü içinden ayarlanabilir.

Aşağıdaki yönetilen örnek ölçümleri uyarı yapılandırması için kullanılabilir:

| Metric | Açıklama | Ölçü birimi \ olası değerler |
| :--------- | --------------------- | ----------- |
| Ortalama CPU yüzdesi | Seçilen dönemdeki CPU kullanımının ortalama yüzdesi. | 0-100 (yüzde) |
| Okunan GÇ baytları | Seçilen dönemde GÇ baytları okundu. | Bayt |
| Yazılan GÇ baytları | Seçilen dönemde yazılan GÇ baytları. | Bayt |
| GÇ istek sayısı | Seçilen dönemdeki GÇ isteklerinin sayısı. | Sayısal |
| Ayrılan depolama alanı | Yönetilen örnek için ayrılan geçerli en fazla depolama alanı. Kaynak ölçeklendirme işlemiyle değişiklikler. | MB (megabayt) |
| Kullanılan depolama alanı | Seçili dönemde kullanılan depolama alanı. Veritabanlarına ve örneğe göre depolama tüketimine sahip değişiklikler. | MB (megabayt) |
| Sanal çekirdek sayısı | Yönetilen örnek için sağlanan sanal çekirdekler. Kaynak ölçeklendirme işlemiyle değişiklikler. | 4-80 (sanal çekirdekler) |

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Azure portal bir ölçümde uyarı kuralı oluşturma

1. Azure [portalında](https://portal.azure.com/), izlemeyi ilgilendiğiniz yönetilen örneği bulun ve seçin.

2. Izleme bölümünde **ölçümler** menü öğesini seçin.

   ![İzleme](./media/alerts-create/mi-alerting-menu-annotated.png)
  
3. Açılan menüde, uyarıyı ayarlamak istediğiniz ölçülerden birini seçin (kullanılan depolama alanı örnekte gösterilir).

4. Belirli bir dönemde ortalama, en düşük veya en fazla ulaşılan toplama süresi (Ort, Min veya Max) seçin. 

5. **Yeni uyarı kuralı** Seç

6. Uyarı kuralı oluştur bölmesinde **koşul adı** ' na tıklayın (örnekte kullanılan depolama alanı gösterilir)

   ![Koşulu tanımla](./media/alerts-create/mi-create-metrics-alert-smaller-annotated.png)

7. Sinyal mantığını Yapılandır bölmesinde Işleç, toplama türü ve eşik değerini tanımlayın

   * İşleç türü seçenekleri büyüktür, eşittir ve küçüktür (eşik değeri)
   * Toplama türü seçenekleri min, Max veya Average (toplama ayrıntı düzeyi döneminde)
   * Eşik değeri, işleç ve toplama ölçütlerine göre değerlendirilecek olan uyarı değeridir
   
   ![Configure_signal_logic](./media/alerts-create/mi-configure-signal-logic-annotated.png)
   
   Ekran görüntüsünde gösterilen örnekte, 1840876 MB değeri, 1,8 TB 'lik bir eşik değerini temsil eden kullanılır. Örnekteki işleç büyüktür olarak ayarlandığı için, yönetilen örnekteki depolama alanı tüketimi 1,8 TB 'tan fazlaysa uyarı oluşturulur. Depolama alanı ölçümlerinin eşik değerinin MB cinsinden ifade edilmesi gerektiğini unutmayın.

8. Değerlendirme süresi toplama ayrıntı düzeyini dakikalar içinde ve değerlendirme sıklığıyla ayarlayın. Değerlendirme sıklığı, uyarı sisteminin, eşik koşulunun karşılanıp karşılanmadığını zaman içinde kontrol eder.

9. Eylem grubunu seçin. Eylem grup bölmesi, varolan bir seçimi seçebileceğiniz veya yeni bir eylem oluşturabileceğiniz görünür. Bu eylem, bir uyarının tetiklenmesi (örneğin, e-posta gönderme, telefonda bir Web kancası, Azure işlevi veya Runbook yürütme) için gerçekleştirilecek olduğunu tanımlar.

   ![Select_action_group](./media/alerts-create/mi-select-action-group-smaller-annotated.png)

   * Yeni eylem grubu oluşturmak için **+ eylem grubu oluştur** ' u seçin.

      ![Create_action_group_alerts](./media/alerts-create/mi-create-alert-action-group-smaller-annotated.png)
   
   * Nasıl uyarılmak istediğinizi tanımlayın: eylem grubu adı, kısa ad, eylem adı ve seçim eylem türü girin. Eylem türü e-posta, SMS mesajı, sesli çağrı veya belki de Web kancası, Azure işlevi, runbook 'un yürütülüp yürütülmeyeceğini veya uyumlu sisteminizde ıTSM bileti oluşturulup bildirilmeyeceğini tanımlar.

      ![Define_how_to_be_alerted](./media/alerts-create/mi-add-alerts-action-group-annotated.png)

10. Kayıtlarınızın uyarı kuralı ayrıntılarını doldurarak önem derecesi türünü seçin.

      ![Rule_description](./media/alerts-create/mi-rule-details-complete-smaller-annotated.png)

   * Uyarı kuralı **Oluştur** düğmesine tıklayarak uyarı kuralını oluşturma ' yı doldurun.

Yeni uyarı kuralı birkaç dakika içinde etkin hale gelecek ve ayarlarınıza göre tetiklenecektir.

## <a name="verifying-alerts"></a>Uyarıları doğrulama

> [!NOTE]
> Gürültülü uyarıları anlamak için, bkz. [eylem kurallarını kullanarak gizleme of Alerts](../../azure-monitor/alerts/alerts-action-rules.md#suppression-of-alerts).

Bir uyarı kuralı ayarladıktan sonra, uyarı tetikleyicisinin ve sıklığının karşılandığını doğrulayın. Kullanılan depolama alanı üzerinde bir uyarı ayarlamak için bu sayfada gösterilen örnek için, uyarı seçeneğinizi e-posta ise, aşağıda gösterildiği gibi bir e-posta alabilirsiniz.

   ![alert_example](./media/alerts-create/mi-email-alert-example-smaller-annotated.png)

E-postada uyarı adı, eşiğin ayrıntıları ve uyarının neden tetiklendiğine ilişkin uyarı görüntülenir. Azure portal e-posta yoluyla alınan uyarıyı görüntülemek için **Azure Portal düğmesini göster '** i kullanabilirsiniz. 

## <a name="view-suspend-activate-modify-and-delete-existing-alert-rules"></a>Mevcut uyarı kurallarını görüntüleme, askıya alma, etkinleştirme, değiştirme ve silme

> [!NOTE]
> Mevcut uyarıların Azure portal panosundan uyarılar menüsünden yönetilmesi gerekir. Mevcut uyarılar yönetilen örnek kaynağı dikey penceresinden değiştirilemez.

Var olan uyarıları görüntülemek, askıya almak, etkinleştirmek, değiştirmek ve silmek için:

1. Azure portal arama kullanarak uyarı arayın. Uyarılar ' a tıklayın.

   ![find_alerts](./media/alerts-create/mi-manage-alerts-browse-smaller-annotated.png)

   Alternatif olarak, yapılandırdıysanız, Azure gezinti çubuğundaki Uyarılar ' a de tıklayabilirsiniz.

2. Uyarılar bölmesinde, uyarı kurallarını yönet ' i seçin.

   ![modify_alerts](./media/alerts-create/mi-manage-alert-rules-smaller-annotated.png)

   Var olan uyarıların listesi görünür. Yönetmek için, mevcut bir uyarı kuralı seçin. Mevcut etkin kurallar değiştirilebilir ve tercihinize göre ayarlanabilir. Etkin kurallar silinmeden da askıya alınabilir. 

## <a name="next-steps"></a>Sonraki adımlar

* Azure Izleyici uyarı sistemi hakkında bilgi edinin, bkz. [Microsoft Azure uyarılara genel bakış](../../azure-monitor/alerts/alerts-overview.md)
* Ölçüm uyarıları hakkında daha fazla bilgi için bkz. [ölçüm uyarılarının Azure izleyici 'de nasıl çalıştığını anlama](../../azure-monitor/alerts/alerts-metric-overview.md)
* Uyarılarda bir Web kancası yapılandırma hakkında bilgi edinin, bkz. [Klasik bir ölçüm uyarısı ile Web kancası çağırma](../../azure-monitor/alerts/alerts-webhooks.md)
* PowerShell kullanarak uyarıları yapılandırma ve yönetme hakkında bilgi edinin, bkz. [Eylem kuralları](/powershell/module/az.monitor/add-azmetricalertrulev2)
* API kullanarak uyarıları yapılandırma ve yönetme hakkında bilgi edinmek için bkz. [Azure izleyici REST API başvurusu](/rest/api/monitor/)