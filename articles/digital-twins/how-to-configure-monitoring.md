---
title: İzlemeyi Yapılandırma-Azure dijital TWINS | Microsoft Docs
description: Azure dijital TWINS için izleme ve günlüğe kaydetme seçeneklerini yapılandırmayı öğrenin.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/01/2019
ms.custom: seodec18
ms.openlocfilehash: 38d55df5f705aa3639540c4731c4c17c61c9b9ac
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74005969"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>Azure dijital TWINS 'de izlemeyi yapılandırma

Azure dijital TWINS, sağlam günlük kaydı, izleme ve analiz desteği. Çözümler geliştiricileri, IoT uygulamasının karmaşık izleme ihtiyaçlarını desteklemek için Azure Izleyici günlüklerini, tanılama günlüklerini, etkinlik günlüğü 'nü ve diğer hizmetleri kullanabilir. Günlüğe kaydetme seçenekleri, çeşitli hizmetlerde kayıtları sorgulamak veya göstermek ve birçok hizmet için parçalı günlük kapsamı sağlamak üzere birleştirilebilir.

Bu makalede günlüğe kaydetme ve izleme seçenekleri ve bunları Azure dijital TWINS 'e özgü yollarla birleştirme özetlenmektedir.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="review-activity-logs"></a>Etkinlik günlüklerini gözden geçirme

Azure [etkinlik günlükleri](../azure-monitor/platform/activity-logs-overview.md) her bir Azure hizmet örneği için abonelik düzeyinde olay ve işlem geçmişlerine hızlı öngörüler sağlar.

Abonelik düzeyi olaylar şunları içerir:

* Kaynak oluşturma
* Kaynak kaldırma
* Uygulama parolaları oluşturma
* Diğer hizmetlerle tümleştirme

Azure dijital TWINS için etkinlik günlüğü varsayılan olarak etkindir ve Azure portal ile Şu şekilde bulunabilir:

1. Azure dijital TWINS örneğinizi seçme.
1. Görüntüleme panelini açmak için **etkinlik günlüğü** seçiliyor:

    [Etkinlik günlüğünü ![](media/how-to-configure-monitoring/activity-log.png)](media/how-to-configure-monitoring/activity-log.png#lightbox)

Gelişmiş etkinlik günlüğü için:

1. **Etkinlik günlüğü analizi genel bakışı**göstermek için **Günlükler** seçeneğini belirleyin:

    [![seçimi](media/how-to-configure-monitoring/activity-log-select.png)](media/how-to-configure-monitoring/activity-log-select.png#lightbox)

1. **Etkinlik günlüğü analizi genel bakış** , önemli etkinlik günlüğü verilerini özetler:

    [![etkinlik günlüğü analizinden genel bakış]( media/how-to-configure-monitoring/log-analytics-overview.png)]( media/how-to-configure-monitoring/log-analytics-overview.png#lightbox)

>[!TIP]
>Abonelik düzeyi olaylar hakkında hızlı Öngörüler için **etkinlik günlüklerini** kullanın.

## <a name="enable-customer-diagnostic-logs"></a>Müşteri tanılama günlüklerini etkinleştir

Azure [Tanılama ayarları](../azure-monitor/platform/resource-logs-overview.md) , her bir Azure örneği için etkinlik günlüğünü tamamlamak üzere ayarlanabilir. Etkinlik günlükleri abonelik düzeyindeki etkinliklerle ilgilidir, tanılama günlüğü kaynakların işletimsel geçmişiyle ilgili öngörüler sağlar.

Tanılama günlüğü örnekleri şunları içerir:

* Kullanıcı tanımlı bir işlev için yürütme süresi
* Başarılı bir API isteğinin yanıt durum kodu
* Bir kasadan uygulama anahtarı alma

Bir örnek için tanılama günlüklerini etkinleştirmek için:

1. Kaynağı Azure portal alın.
1. **Tanılama ayarlarını**seçin:

    [![tanılama ayarlarını tek](media/how-to-configure-monitoring/diagnostic-settings-one.png)](media/how-to-configure-monitoring/diagnostic-settings-one.png#lightbox)

1. Veri toplamak için **tanılamayı aç '** ı seçin (daha önce etkinleştirilmemişse).
1. İstenen alanları girin ve verilerin nasıl ve nerede kaydedileceğini seçin:

    [![tanılama ayarlarını iki](media/how-to-configure-monitoring/diagnostic-settings-two.png)](media/how-to-configure-monitoring/diagnostic-settings-two.png#lightbox)

    Tanılama günlükleri genellikle [Azure dosya depolama](../storage/files/storage-files-deployment-guide.md) kullanılarak kaydedilir ve [Azure izleyici günlükleriyle](../azure-monitor/log-query/get-started-portal.md)paylaşılır. Her iki seçenek de seçilebilir.

>[!TIP]
>Kaynak işlemlerine yönelik Öngörüler için **tanılama günlüklerini** kullanın.

## <a name="azure-monitor-and-log-analytics"></a>Azure izleyici ve Log Analytics

IoT uygulamaları, farklı kaynakları, cihazları, konumları ve verileri tek bir konuma ayırır. Hassas günlüğe kaydetme, genel uygulama mimarisinin her bir parçası, hizmet veya bileşeni hakkında ayrıntılı bilgiler sağlar, ancak bakım ve hata ayıklama için genellikle Birleşik bir genel bakış gereklidir.

Azure Izleyici, günlük kaynaklarının tek bir konumda görüntülenmesine ve çözümlenme olanağı sağlayan güçlü Log Analytics hizmetini içerir. Bu nedenle, Gelişmiş IoT Apps 'teki günlükleri çözümlemek için Azure Izleyici son derece yararlıdır.

Kullanım örnekleri şunlardır:

* Birden çok tanılama günlüğü geçmişi sorgulanıyor
* Kullanıcı tanımlı birkaç işlev için günlükleri görme
* Belirli bir zaman çerçevesi içinde iki veya daha fazla hizmet için günlükleri görüntüleme

Tam günlük sorgulama [Azure izleyici günlükleri](../azure-monitor/log-query/log-query-overview.md)aracılığıyla sağlanır. Bu güçlü özellikleri ayarlamak için:

1. Azure portal **Log Analytics** için arama yapın.
1. Kullanılabilir **Log Analytics çalışma alanı** örneklerinizi görürsünüz. Bir tane seçin ve sorgu için **günlükleri** seçin:

    [![Log Analytics](media/how-to-configure-monitoring/log-analytics.png)](media/how-to-configure-monitoring/log-analytics.png#lightbox)

1. Zaten bir **Log Analytics çalışma alanı** örneğiniz yoksa, **Ekle** düğmesini seçerek bir çalışma alanı oluşturabilirsiniz:

    [OMS oluşturma ![](media/how-to-configure-monitoring/log-analytics-oms.png)](media/how-to-configure-monitoring/log-analytics-oms.png#lightbox)

**Log Analytics çalışma alanı** örneğiniz sağlandıktan sonra, tek tek günlüklerde girişleri bulmak veya **günlük yönetimi**kullanarak belirli ölçütleri kullanarak aramak için güçlü sorgular kullanabilirsiniz:

   [![günlük yönetimi](media/how-to-configure-monitoring/log-analytics-management.png)](media/how-to-configure-monitoring/log-analytics-management.png#lightbox)

Güçlü sorgu işlemleri hakkında daha fazla bilgi için bkz. [sorguları](../azure-monitor/log-query/get-started-queries.md)kullanmaya başlama.

> [!NOTE]
> İlk kez **Log Analytics çalışma alanına** olay gönderirken 5 dakikalık bir gecikmeyle karşılaşabilirsiniz.

Azure Izleyici günlükleri Ayrıca, **Tanılama ve çözme sorunlarını**belirleyerek görüntülenebilen güçlü hata ve uyarı bildirim hizmetleri sağlar:

   [Uyarı ve hata bildirimlerini ![](media/how-to-configure-monitoring/log-analytics-notifications.png)](media/how-to-configure-monitoring/log-analytics-notifications.png#lightbox)

>[!TIP]
>Birden çok uygulama işlevi, abonelik veya hizmet için günlük geçmişlerini sorgulamak üzere **Log Analytics çalışma alanını** kullanın.

## <a name="other-options"></a>Diğer seçenekler

Azure dijital TWINS ayrıca uygulamaya özgü günlüğe kaydetme ve güvenlik denetimini destekler. Azure dijital TWINS örneğiniz için sunulan tüm Azure günlük seçeneklerine ilişkin kapsamlı bir genel bakış için bkz. [Azure günlük denetimi](../security/fundamentals/log-audit.md) makalesi.

## <a name="next-steps"></a>Sonraki adımlar

- Azure [etkinlik günlükleri](../azure-monitor/platform/activity-logs-overview.md)hakkında daha fazla bilgi edinin.

- [Tanılama günlüklerine genel bir bakış](../azure-monitor/platform/resource-logs-overview.md)okuyarak Azure tanılama ayarlarını daha ayrıntılı olarak inceleyin.

- [Azure izleyici günlükleri](../azure-monitor/log-query/get-started-portal.md)hakkında daha fazla bilgi edinin.
