---
title: İş yükü yönetimi portalı izleme
description: Azure SYNAPSE Analytics 'te iş yükü yönetimi portalı izleme Kılavuzu.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/01/2021
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 702cc1fc8b135b2eb4af9106a81946873918c4fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101694402"
---
# <a name="azure-synapse-analytics--workload-management-portal-monitoring"></a>Azure SYNAPSE Analytics – Iş yükü Yönetim Portalı Izleme

Bu makalede [iş yükü grubu](sql-data-warehouse-workload-isolation.md#workload-groups) kaynak kullanımı ve sorgu etkinliğinin nasıl izleneceği açıklanır.
Azure Ölçüm Gezgini yapılandırma hakkında ayrıntılı bilgi için bkz. [Azure ile çalışmaya başlama Ölçüm Gezgini](../../azure-monitor/essentials/metrics-getting-started.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) makalesi.  Sistem kaynak tüketiminin nasıl izleneceği hakkında ayrıntılı bilgi edinmek için Azure SYNAPSE Analytics Izleme belgelerindeki [kaynak kullanımı](sql-data-warehouse-concept-resource-utilization-query-activity.md#resource-utilization) bölümüne bakın.
İş yükü yönetimini izlemek için belirtilen iki farklı iş yükü grubu ölçümü kategorisi vardır: kaynak ayırma ve sorgu etkinliği.  Bu ölçümler, iş yükü grubuna göre bölünebilir ve filtrelenebilir.  Ölçümler, sistem tanımlı (kaynak sınıfı iş yükü grupları) veya Kullanıcı tanımlı ( [Iş yükü grubu oluşturma](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) sözdizimi olan kullanıcı tarafından oluşturulan) temelinde ayrılabilir ve filtrelenebilir.

## <a name="workload-management-metric-definitions"></a>İş yükü yönetimi Ölçüm tanımları

|Ölçüm Adı                    |Description  |Toplama Türü |
|-------------------------------|-------------|-----------------|
|Etkin uç kaynak yüzdesi | *Etkin sınır yüzdesi* , diğer iş yükü grupları için ayrılan *En düşük kaynak yüzdesine* bağlı olarak, iş yükü grubu tarafından erişilebilen kaynakların yüzdesine yönelik sabit bir sınır olur. *Etkin uç kaynak yüzdesi* ölçümü, `CAP_PERCENTAGE_RESOURCE` [iş yükü grubu oluşturma](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) sözdiziminde parametresi kullanılarak yapılandırılır.  Geçerli değer burada açıklanmıştır.<br><br>Örneğin `DataLoads` , = 100 ile bir iş yükü grubu oluşturulduysa `CAP_PERCENTAGE_RESOURCE` ve geçerli bir en düşük kaynak yüzdesi %25 olan başka bir iş yükü grubu oluşturulduysa, iş yükü grubu için *geçerli sınır kaynak* yüzdesi `DataLoads` %75 ' dir.<br><br>*Etkin sınır kaynağı yüzdesi* , iş yükü grubunun elde edilebileceği eşzamanlılık (ve dolayısıyla potansiyel aktarım hızı) üst sınırını belirler.  Geçerli *sınır kaynak yüzdesi* ölçümü tarafından şu anda raporlanan ek aktarım hızı gerekiyorsa, `CAP_PERCENTAGE_RESOURCE` `MIN_PERCENTAGE_RESOURCE` daha fazla kaynak eklemek için, diğer iş yükü gruplarının sayısını azaltın veya örneği ölçeğini artırın.  Azaltmak `REQUEST_MIN_RESOURCE_GRANT_PERCENT` eşzamanlılık artırabilir, ancak genel aktarım hızını artıramayabilir.| Min, AVG, Max |
|Geçerli Min kaynak yüzdesi |*Geçerli Min kaynak yüzdesi* , hizmet düzeyi en düşük hesaba sahip iş yükü grubu için ayrılan ve yalıtılmış kaynakların en düşük yüzdesidir.  Etkin Min kaynak yüzdesi ölçümü, `MIN_PERCENTAGE_RESOURCE` [Iş yükü grubu oluşturma](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) sözdiziminde parametresi kullanılarak yapılandırılır.  Geçerli değer [burada](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json?view=azure-sqldw-latest&preserve-view=true#effective-values)açıklanmıştır.<br><br>Bu ölçüm filtrelenmemiş olduğunda Sum toplama türünü kullanın ve sistemde yapılandırılan toplam iş yükü yalıtımını izlemek için bölmeyi kaldırın.<br><br>*Geçerli Min kaynak yüzdesi* , garantili eşzamanlılık (ve bu nedenle garanti edilen aktarım hızı) için bir iş yükü grubunun alabileceği alt sınırı belirler.  Geçerli *En düşük kaynak yüzdesi* ölçümü tarafından şu anda raporlanan ek garantili kaynaklar gerekliyse, `MIN_PERCENTAGE_RESOURCE` iş yükü grubu için yapılandırılmış parametreyi artırın.  Azaltmak `REQUEST_MIN_RESOURCE_GRANT_PERCENT` eşzamanlılık artırabilir, ancak genel aktarım hızını artıramayabilir. |Min, AVG, Max|
|İş yükü grubu etkin sorguları  |Bu ölçüm, iş yükü grubu içindeki etkin sorguları raporlar.  Bu metriğin filtrelenmemiş olarak kullanılması, sistemde çalışan tüm etkin sorguları görüntüler.|Sum         |
|Sınır kaynağına göre iş yükü grubu ayırma yüzdesi |Bu ölçüm, toplam iş yükü grubu başına düşen *etkin sınır kaynağına* göre kaynakların yüzde ayırmasını görüntüler.  Bu ölçüm, iş yükü grubunun etkin kullanımını sağlar.<br><br>`DataLoads`%75 ve %25 ' lik yapılandırılmış *etkin bir sınır kaynağına* sahip bir iş yükü grubu düşünün `REQUEST_MIN_RESOURCE_GRANT_PERCENT` .  *Sınır kaynağına göre filtrelenen Iş yükü grubu ayırma* değeri `DataLoads` %33 (%25/75%) olacaktır Bu iş yükü grubunda tek bir sorgu çalışıyorsa.<br><br>İş yükü grubunun kullanımını belirlemek için bu ölçümü kullanın.  %100 ' e yakın bir değer, iş yükü grubu için kullanılabilir tüm kaynakların kullanıldığını gösterir.  Ayrıca, sıfırdan büyük bir değeri gösteren aynı iş yükü grubu için *Iş yükü grubu sıraya alınmış sorgular ölçümü* , iş yükü grubunun ayrılsa ek kaynakları kullanacağındığını gösterir.  Buna karşılık, bu ölçüm sürekli düşüktür ve *Iş yükü grubu etkin sorguları* düşükse iş yükü grubu kullanılmaz.  Bu durum özellikle, etkin bir *uç kaynak yüzdesi* sıfırdan büyükse sorunlu olur ve bu da [aşırı kullanılan iş yükü yalıtımı](#underutilized-workload-isolation)olduğunu gösterir.|Min, AVG, Max |
|Sistem iş yükü grubuna göre ayırma yüzdesi | Bu ölçüm, tüm sisteme göre kaynakların yüzde ayırmasını görüntüler.<br><br>`DataLoads`%25 ' te yapılandırılmış bir iş yükü grubunu düşünün `REQUEST_MIN_RESOURCE_GRANT_PERCENT` .  Filtre uygulanan *sistem yüzdesi değerine göre Iş yükü grubu ayırması* `DataLoads` %25 (%25/100%) Bu iş yükü grubunda tek bir sorgu çalışıyorsa.|Min, AVG, Max |
|İş yükü grubu sorgu zaman aşımları |Zaman aşımına uğramış iş yükü grubu için sorgular.  Bu ölçüm tarafından bildirilen sorgu zaman aşımları yalnızca sorgu yürütülmeye başladıktan sonra (kilitleme veya kaynak bekleme nedeniyle bekleme süresi içermez).<br><br>Sorgu zaman aşımı, `QUERY_EXECUTION_TIMEOUT_SEC` [Iş yükü grubu oluşturma](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) sözdiziminde parametresi kullanılarak yapılandırılır.  Değerin artırılması sorgu zaman aşımı sayısını azaltabilir.<br><br>`REQUEST_MIN_RESOURCE_GRANT_PERCENT`Zaman aşımı miktarını azaltmak ve sorgu başına daha fazla kaynak ayırmak için iş yükü grubunun parametresini artırmayı göz önünde bulundurun.  , Artırma, `REQUEST_MIN_RESOURCE_GRANT_PERCENT` iş yükü grubu için eşzamanlılık miktarını azaltır. |Sum |
|İş yükü grubu sıraya alınmış sorgular | Şu anda yürütmenin yürütülmeye başlamasını bekleyen sıraya alınmış iş yükü grubu için sorgular.  Sorgular, kaynakları veya kilitleri beklediği için kuyruk olabilir.<br><br>Sorgular çok sayıda nedenden dolayı bekleniyor.  Sistem aşırı yüklenmişse ve eşzamanlılık talebi kullanılabilir olandan fazlaysa sorgular sıraya alınır.<br><br>İş yükü grubu `CAP_PERCENTAGE_RESOURCE` [Oluştur](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) deyimindeki parametreyi artırarak iş yükü grubuna daha fazla kaynak eklemeyi düşünün.  , `CAP_PERCENTAGE_RESOURCE` *Etkin sınır kaynak yüzdesi* ölçüsüne fazlaysa, diğer iş yükü grubu için yapılandırılan iş yükü yalıtımı, bu iş yükü grubuna ayrılan kaynakları etkiler.  `MIN_PERCENTAGE_RESOURCE`Diğer iş yükü gruplarını azaltmayı veya daha fazla kaynak eklemek için örneği ölçeklendirmenizi düşünün. |Sum |

## <a name="monitoring-scenarios-and-actions"></a>İzleme senaryoları ve eylemler

Sorunu gidermek için ilgili eylemlerle birlikte sorun giderme için iş yükü yönetimi ölçüm kullanımını vurgulayabilecek bir dizi grafik yapılandırması aşağıda verilmiştir.

### <a name="underutilized-workload-isolation"></a>Aşırı kullanılan iş yükü yalıtımı

Adlı bir iş yükü grubunun `wgPriority` oluşturulduğu ve *TheCEO* `membername` `wcCEOPriority` iş yükü Sınıflandırıcısı kullanılarak onunla eşlendiği aşağıdaki iş yükü grubunu ve sınıflandırıcı yapılandırmasını göz önünde bulundurun.  `wgPriority`İş yükü grubu için %25 iş yükü yalıtımı yapılandırıldı ( `MIN_PERCENTAGE_RESOURCE` = 25).  *TheCEO* tarafından gönderilen her sorguya sistem kaynaklarının %5 ' i ( `REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 5) verilir.

```sql
CREATE WORKLOAD GROUP wgPriority
WITH ( MIN_PERCENTAGE_RESOURCE = 25
      ,CAP_PERCENTAGE_RESOURCE = 50
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5);

CREATE WORKLOAD CLASSIFIER wcCEOPriority
WITH ( WORKLOAD_GROUP = 'wgPriority'
      ,MEMBERNAME = 'TheCEO');
```

Aşağıdaki grafik aşağıdaki şekilde yapılandırılır:<br>
Ölçüm 1: *geçerli en düşük kaynak yüzdesi* (ortalama toplama, `blue line` )<br>
Ölçüm 2: *sisteme göre Iş yükü grubu ayırma yüzdesi* (ort toplama, `purple line` )<br>
Filtre: [Iş yükü grubu] = `wgPriority`<br>
![Ekran görüntüsünde iki ölçüm ve filtreye sahip bir grafik gösterilir.](./media/sql-data-warehouse-workload-management-portal-monitor/underutilized-wg.png)
Grafik, %25 iş yükü yalıtımına sahip olduğunu gösterir, ortalama olarak yalnızca %10 kullanılır.  Bu durumda, `MIN_PERCENTAGE_RESOURCE` parametre değeri 10 veya 15 arasında düşürülemez ve sistemdeki diğer iş yüklerinin kaynakları kullanmasına izin verebilir.

### <a name="workload-group-bottleneck"></a>İş yükü grubu performans sorunu

Adlı bir iş yükü grubunun `wgDataAnalyst` oluşturulduğu ve *veri analistinin* `membername` `wcDataAnalyst` iş yükü Sınıflandırıcısı kullanılarak kendisine eşlendiği, aşağıdaki iş yükü grubunu ve sınıflandırıcı yapılandırmasını göz önünde bulundurun.  `wgDataAnalyst`İş yükü grubu için %6 iş yükü yalıtımı yapılandırıldı ( `MIN_PERCENTAGE_RESOURCE` = 6) ve %9 ( `CAP_PERCENTAGE_RESOURCE` = 9) kaynak sınırı vardır.  *Veri analisti* tarafından gönderilen her sorguya sistem kaynaklarının %3 ' i ( `REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 3) verilir.

```sql
CREATE WORKLOAD GROUP wgDataAnalyst  
WITH ( MIN_PERCENTAGE_RESOURCE = 6
      ,CAP_PERCENTAGE_RESOURCE = 9
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 3);

CREATE WORKLOAD CLASSIFIER wcDataAnalyst
WITH ( WORKLOAD_GROUP = 'wgDataAnalyst'
      ,MEMBERNAME = 'DataAnalyst');
```

Aşağıdaki grafik aşağıdaki şekilde yapılandırılır:<br>
Ölçüm 1: *etkin uç kaynak yüzdesi* (ortalama toplama, `blue line` )<br>
Ölçüm 2: *sınır kaynağına göre Iş yükü grubu ayırması yüzdesi* (ort toplama, `purple line` )<br>
Ölçüm 3: *Iş yükü grubu sıraya alınmış sorgular* (Toplam toplama, `turquoise line` )<br>
Filtre: [Iş yükü grubu] = `wgDataAnalyst`<br>
![Ekran görüntüsünde, üç ölçüm ve filtreye sahip bir grafik gösterilir.](./media/sql-data-warehouse-workload-management-portal-monitor/bottle-necked-wg.png)
Grafik, kaynaklar üzerinde %9 Cap ile, iş *yükü grubunun%* 90 ' a kadar olduğunu gösterir.  *Sıraya alınmış sorgular ölçümünün Iş yükü grubundan* gösterildiği gibi düzenli bir sorgu sırası vardır.  Bu durumda, ' ı `CAP_PERCENTAGE_RESOURCE` %9 ' dan büyük bir değere yükseltmek daha fazla sorgunun eşzamanlı olarak yürütülmesine izin verir.  Arttırmak için `CAP_PERCENTAGE_RESOURCE` yeterli kaynak olduğunu ve diğer iş yükü grupları tarafından yalıtılmamış olduğunu varsaymaktadır.  *Etkin uç kaynak yüzdesi ölçümünü* denetleyerek, Cap 'in arttığını doğrulayın.  Daha fazla işleme isteniyorsa, öğesini `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 3 ' ten büyük bir değere artırmayı de göz önünde bulundurun.  ' Nin artırılması `REQUEST_MIN_RESOURCE_GRANT_PERCENT` sorguların daha hızlı çalışmasına izin verebilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Hızlı başlangıç: T-SQL kullanarak iş yükü yalıtımını yapılandırma](quickstart-configure-workload-isolation-tsql.md)<br>
- [Iş yükü grubu oluşturma (Transact-SQL)](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)<br>
- [Iş yükü SıNıFLANDıRıCıSı oluşturma (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)<br>
- [İzleme kaynak kullanımı](sql-data-warehouse-concept-resource-utilization-query-activity.md)
