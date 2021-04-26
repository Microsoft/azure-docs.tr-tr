---
title: İş yükü yalıtımı
description: Azure SYNAPSE Analytics 'teki iş yükü grupları ile iş yükü yalıtımı ayarlama Kılavuzu.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 506aed16f1b8a6c631a759bb1367aef8242859ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98734789"
---
# <a name="azure-synapse-analytics-workload-group-isolation"></a>Azure SYNAPSE Analytics iş yükü grubu yalıtımı

Bu makalede iş yükü gruplarının, iş yükü yalıtımı yapılandırmak, kaynakları içermesi ve sorgu yürütmesi için çalışma zamanı kuralları uygulamak amacıyla nasıl kullanılabileceği açıklanmaktadır.

## <a name="workload-groups"></a>İş yükü grupları

İş yükü grupları bir istek kümesi için kapsayıcılardır ve iş yükü yalıtımı da dahil olmak üzere, bir sistemde yapılandırılan iş yükü yönetiminin temelini oluşturur.  İş yükü grupları [oluşturma Iş yükü grubu](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) sözdizimi kullanılarak oluşturulur.  Basit bir iş yükü yönetimi yapılandırması, veri yüklerini ve Kullanıcı sorgularını yönetebilir.  Örneğin, adlı bir iş yükü grubu `wgDataLoads` sisteme yüklenmekte olan verilerin iş yükü yönlerini tanımlayacaktır. Ayrıca, adlı bir iş yükü grubu, `wgUserQueries` verileri sistemden okumak için sorguları çalıştıran kullanıcıların iş yükü yönlerini tanımlar.

Aşağıdaki bölümler, iş yükü gruplarının yalıtım, kapsama, istek kaynağı tanımını tanımlama ve yürütme kurallarına bağlı olarak nasıl olduğunu vurgulayacaktır.

## <a name="workload-isolation"></a>İş yükü yalıtımı

İş yükü yalıtımı, kaynakların yalnızca bir iş yükü grubu için ayrılmış olması anlamına gelir.  İş yükü yalıtımı, MIN_PERCENTAGE_RESOURCE parametresi, [Iş yükü grubu oluşturma](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) sözdiziminde sıfırdan büyük olacak şekilde yapılandırılarak elde edilir.  Sıkı SLA 'Lara uyması gereken sürekli yürütme iş yükleri için yalıtım, kaynakların iş yükü grubu için her zaman kullanılabilir olmasını sağlar.

İş yükü yalıtımını yapılandırma, örtülü olarak garantili bir eşzamanlılık düzeyi tanımlar. Örneğin, `MIN_PERCENTAGE_RESOURCE` %30 ' a ayarlanmış ve %2 ' ye ayarlanmış bir iş yükü grubu `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 15 eşzamanlılık olarak garanti edilir.  % 15-2 kaynak yuvaları her zaman iş yükü grubu içinde ayrıldığından eşzamanlılık düzeyi garanti edilir (yapılandırıldıktan bağımsız olarak `REQUEST_*MAX*_RESOURCE_GRANT_PERCENT` ).  , `REQUEST_MAX_RESOURCE_GRANT_PERCENT` Şundan büyükse `REQUEST_MIN_RESOURCE_GRANT_PERCENT` ve `CAP_PERCENTAGE_RESOURCE` ek kaynaklardan daha büyükse `MIN_PERCENTAGE_RESOURCE` istek başına eklenir.  `REQUEST_MAX_RESOURCE_GRANT_PERCENT`Ve `REQUEST_MIN_RESOURCE_GRANT_PERCENT` eşitse ve `CAP_PERCENTAGE_RESOURCE` daha büyükse `MIN_PERCENTAGE_RESOURCE` , ek eşzamanlılık mümkündür.  Garantili eşzamanlılık belirlemek için aşağıdaki yöntemi göz önünde bulundurun:

[Garantili eşzamanlılık] = [ `MIN_PERCENTAGE_RESOURCE` ]/[ `REQUEST_MIN_RESOURCE_GRANT_PERCENT` ]

> [!NOTE]
> Min_percentage_resource için belirli hizmet düzeyi en düşük uygun değerler vardır.  Daha fazla bilgi için bkz. daha fazla ayrıntı için [geçerli değerler](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json?view=azure-sqldw-latest&preserve-view=true#effective-values) .

İş yükü yalıtımı yokluğunda, istekler [paylaşılan kaynak havuzunda](#shared-pool-resources) çalışır.  Paylaşılan havuzdaki kaynaklara erişim garanti edilmez ve [önemli](sql-data-warehouse-workload-importance.md) temelinde atanır.

İş yükü grubunda etkin bir istek olmasa bile, kaynaklar iş yükü grubuna ayrıldığından, iş yükü yalıtımını yapılandırmak dikkatli yapılmalıdır. Daha fazla yapılandırma yalıtımı, genel sistem kullanımının azalmasına yol açabilir.

Kullanıcılar, %100 iş yükü yalıtımı yapılandıran bir iş yükü yönetimi çözümünü önlemelidir: tüm iş yükü grupları genelinde yapılandırılan min_percentage_resource toplamı %100 ' a eşit olduğunda %100 yalıtım elde edilmelidir.  Bu tür bir yapılandırma çok kısıtlayıcıdır ve rigıd, yanlışlıkla yanlış sınıflandırılmış kaynak istekleri için biraz yer bırakır. Bir isteğin, yalıtım için yapılandırılmamış iş yükü gruplarından yürütülmesine izin vermek için bir sağlama vardır. Bu istek için ayrılan kaynaklar, sistemlerde bir sıfır olarak görünür ve sistem tarafından ayrılmış kaynaklardan bir smallrc düzeyi kaynak izni sağlar.

> [!NOTE]
> En iyi kaynak kullanımını sağlamak için, SLA 'ların karşılandığından ve [iş yükü önem derecesine](sql-data-warehouse-workload-importance.md)göre erişilen paylaşılan kaynaklarla karışmasını sağlamak için bazı yalıtımla yararlanan bir iş yükü yönetimi çözümü düşünün.

## <a name="workload-containment"></a>İş yükü kapsama

İş yükü kapsamı, bir iş yükü grubunun kullanabileceği kaynak miktarını sınırlandırmayı ifade eder.  İş yükü kapsama, CAP_PERCENTAGE_RESOURCE parametresi, [Iş yükü grubu oluşturma](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)  sözdiziminde 100 ' den az olacak şekilde yapılandırılarak elde edilir.  Kullanıcıların, geçici sorgular aracılığıyla bir durum çözümlemesi çalıştırabilmeleri için sisteme okuma erişimi olması gereken senaryoyu göz önünde bulundurun.  Bu tür istekler, sistemde çalışan diğer iş yükleri üzerinde olumsuz bir etkiye sahip olabilir.  Kapsamayı yapılandırmak, kaynak miktarının sınırlı olmasını sağlar.

İş yükü kapsamayı yapılandırmak, en yüksek eşzamanlılık düzeyini örtülü olarak tanımlar.  %60 olarak ayarlanan ve bir REQUEST_MIN_RESOURCE_GRANT_PERCENT %1 ' e ayarlanmış CAP_PERCENTAGE_RESOURCE, iş yükü grubu için 60 eşzamanlılık düzeyine kadar izin verilir.  En fazla eşzamanlılık belirlemek için aşağıda verilen yöntemi göz önünde bulundurun:

[En fazla eşzamanlılık] = [ `CAP_PERCENTAGE_RESOURCE` ]/[ `REQUEST_MIN_RESOURCE_GRANT_PERCENT` ]

> [!NOTE]
> Bir iş yükü grubunun geçerli CAP_PERCENTAGE_RESOURCE, sıfırdan büyük MIN_PERCENTAGE_RESOURCE olan iş yükü grupları oluşturulduğunda %100 ' a ulaşmaz.  Etkin çalışma zamanı değerleri için [sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) bakın.

## <a name="resources-per-request-definition"></a>İstek tanımına göre kaynaklar

İş yükü grupları, her istek için ayrılan minimum ve maksimum kaynak miktarını tanımlamak için bir mekanizma sağlar REQUEST_MIN_RESOURCE_GRANT_PERCENT ve [Iş yükü oluşturma grubu](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) sözdiziminde REQUEST_MAX_RESOURCE_GRANT_PERCENT parametreleri.  Bu durumda kaynaklar CPU ve bellektir.  Bu değerleri yapılandırmak, sistemde ne kadar kaynak ve ne kadar eşzamanlılık düzeyi elde edilebileceğini belirler.

> [!NOTE]
> REQUEST_MAX_RESOURCE_GRANT_PERCENT, REQUEST_MIN_RESOURCE_GRANT_PERCENT için belirtilen değere varsayılan değer olan isteğe bağlı bir parametredir.

Kaynak sınıfı seçme gibi, REQUEST_MIN_RESOURCE_GRANT_PERCENT yapılandırmak bir istek tarafından kullanılan kaynaklar için değeri ayarlar.  Küme değeri tarafından belirtilen kaynak miktarı, yürütülmeye başlamadan önce isteğe ayrılmak üzere garanti edilir.  Kaynak sınıflarından iş yükü gruplarına geçiş yapan müşteriler için, başlangıç noktası olarak kaynak sınıflarından iş yükü gruplarına eşleme [yapma](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md) makalesini takip edin.

REQUEST_MAX_RESOURCE_GRANT_PERCENT REQUEST_MIN_RESOURCE_GRANT_PERCENT daha büyük bir değere yapılandırmak sistemin istek başına daha fazla kaynak ayırmasına izin verir.  Bir istek zamanlarken, sistem isteğe gerçek kaynak ayırmayı, REQUEST_MIN_RESOURCE_GRANT_PERCENT ve REQUEST_MAX_RESOURCE_GRANT_PERCENT arasında, paylaşılan havuzdaki kaynak kullanılabilirliğine ve sistemdeki geçerli yüke göre belirler.  Sorgu zamanlandığında kaynakların paylaşılan kaynak [havuzunda](#shared-pool-resources) bulunması gerekir.  

> [!NOTE]
> REQUEST_MIN_RESOURCE_GRANT_PERCENT ve REQUEST_MAX_RESOURCE_GRANT_PERCENT, etkin MIN_PERCENTAGE_RESOURCE ve CAP_PERCENTAGE_RESOURCE değerlerine bağımlı etkin değerler vardır.  Etkin çalışma zamanı değerleri için [sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) bakın.

## <a name="execution-rules"></a>Yürütme kuralları

Geçici raporlama sistemlerinde, müşteriler yanlışlıkla başkalarının üretkenliğini önemli ölçüde etkileyebilecek bir ard arda sorgu yürütebilir.  Sistem yöneticileri, sistem kaynaklarını boşaltmak için ard arda yapılan sorguları sonlandırmaya zorlanır.  İş yükü grupları, belirtilen değeri aşmış sorguları iptal etmek için bir sorgu yürütme zaman aşımı kuralını yapılandırma olanağı sunar.  Kural, `QUERY_EXECUTION_TIMEOUT_SEC` [Iş yükü oluşturma grubu](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) sözdiziminde parametresi ayarlanarak yapılandırılır.

## <a name="shared-pool-resources"></a>Paylaşılan havuz kaynakları

Paylaşılan havuz kaynakları, yalıtım için yapılandırılmamış kaynaklardır.  Bir MIN_PERCENTAGE_RESOURCE sıfır olarak ayarlanan iş yükü grupları, istekleri yürütmek için paylaşılan havuzdaki kaynaklardan faydalanır.  CAP_PERCENTAGE_RESOURCE MIN_PERCENTAGE_RESOURCE daha büyük olan iş yükü grupları, paylaşılan kaynaklar da kullandı.  Paylaşılan havuzdaki kullanılabilir kaynak miktarı aşağıdaki şekilde hesaplanır.

[Paylaşılan havuz] = 100-[ `MIN_PERCENTAGE_RESOURCE` tüm iş yükü grupları genelinde toplam]

Paylaşılan havuzdaki kaynaklara erişim bir [önem derecesine](sql-data-warehouse-workload-importance.md) göre ayrılır.  Aynı önem düzeyine sahip istekler, ilk ve ilk çıkar temelinde paylaşılan havuz kaynaklarına erişir.

## <a name="next-steps"></a>Sonraki adımlar

- [Hızlı başlangıç: iş yükü yalıtımını yapılandırma](quickstart-configure-workload-isolation-tsql.md)
- [IŞ YÜKÜ GRUBU OLUŞTUR](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Kaynak sınıflarını iş yükü gruplarına dönüştürün](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md).
- [Iş yükü izleme yönetim portalı](sql-data-warehouse-workload-management-portal-monitor.md).  
