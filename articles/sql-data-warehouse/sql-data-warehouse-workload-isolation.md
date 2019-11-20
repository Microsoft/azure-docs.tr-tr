---
title: İş yükü yalıtımı
description: Azure SQL veri ambarı 'ndaki iş yükü grupları ile iş yükü yalıtımı ayarlama Kılavuzu.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 11/04/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: a31498ec5459604d89fa72a6f2a003dbc1189eed
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685360"
---
# <a name="sql-data-warehouse-workload-group-isolation-preview"></a>SQL veri ambarı Iş yükü grubu yalıtımı (Önizleme)

Bu makalede iş yükü gruplarının, iş yükü yalıtımı yapılandırmak, kaynakları içermesi ve sorgu yürütmesi için çalışma zamanı kuralları uygulamak amacıyla nasıl kullanılabileceği açıklanmaktadır.

## <a name="workload-groups"></a>İş yükü grupları

İş yükü grupları bir istek kümesi için kapsayıcılardır ve iş yükü yalıtımı da dahil olmak üzere, bir sistemde yapılandırılan iş yükü yönetiminin temelini oluşturur.  İş yükü grupları [oluşturma Iş yükü grubu](https://review.docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) sözdizimi kullanılarak oluşturulur.  Basit bir iş yükü yönetimi yapılandırması, veri yüklerini ve Kullanıcı sorgularını yönetebilir.  Örneğin, `wgDataLoads` adlı bir iş yükü grubu sisteme yüklenmekte olan verilerin iş yükü yönlerini tanımlayacaktır. Ayrıca, `wgUserQueries` adlı bir iş yükü grubu, verileri sistemden okumak için sorguları çalıştıran kullanıcıların iş yükü yönlerini tanımlar.

Aşağıdaki bölümler, iş yükü gruplarının yalıtım, kapsama, istek kaynağı tanımını tanımlama ve yürütme kurallarına bağlı olarak nasıl olduğunu vurgulayacaktır.

## <a name="workload-isolation"></a>İş yükü yalıtımı

İş yükü yalıtımı, kaynakların yalnızca bir iş yükü grubu için ayrılmış olması anlamına gelir.  İş yükü yalıtımı, MIN_PERCENTAGE_RESOURCE parametresi, [Iş yükü grubu oluşturma](https://review.docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) sözdiziminde sıfırdan büyük olacak şekilde yapılandırılarak elde edilir.  Sıkı SLA 'Lara uyması gereken sürekli yürütme iş yükleri için yalıtım, kaynakların iş yükü grubu için her zaman kullanılabilir olmasını sağlar. 

İş yükü yalıtımını yapılandırma, örtülü olarak garantili bir eşzamanlılık düzeyi tanımlar.  %30 ' a ayarlanmış bir MIN_PERCENTAGE_RESOURCE ve REQUEST_MIN_RESOURCE_GRANT_PERCENT %2 ' ye ayarlandığında, iş yükü grubu için 15 eşzamanlılık düzeyi garanti edilir.  Garantili eşzamanlılık belirlemek için aşağıdaki yöntemi göz önünde bulundurun:

[Garantili eşzamanlılık] = [`MIN_PERCENTAGE_RESOURCE`]/[`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE] 
> Min_percentage_resource için belirli hizmet düzeyi en düşük uygulanabilir değerler vardır.  Daha fazla bilgi için bkz. daha fazla ayrıntı için [geçerli değerler](https://review.docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest#effective-values) .

İş yükü yalıtımı yokluğunda, istekler [paylaşılan kaynak havuzunda](#shared-pool-resources) çalışır.  Paylaşılan havuzdaki kaynaklara erişim garanti edilmez ve [önemli](sql-data-warehouse-workload-importance.md) temelinde atanır.

İş yükü grubunda etkin bir istek olmasa bile, kaynaklar iş yükü grubuna ayrıldığından, iş yükü yalıtımını yapılandırmak dikkatli yapılmalıdır.  Daha fazla yapılandırma yalıtımı, genel sistem kullanımının azalmasına yol açabilir.

Kullanıcılar, %100 iş yükü yalıtımı yapılandıran bir iş yükü yönetimi çözümünü önlemelidir: tüm iş yükü grupları genelinde yapılandırılan min_percentage_resource toplamı %100 ' a eşit olduğunda %100 yalıtım elde edilmelidir.  Bu tür bir yapılandırma çok kısıtlayıcıdır ve rigıd, yanlışlıkla yanlış sınıflandırılmış kaynak istekleri için biraz yer bırakır.  Bir isteğin, yalıtım için yapılandırılmamış iş yükü gruplarından yürütülmesine izin vermek için bir sağlama vardır.  Bu istek için ayrılan kaynaklar, sistemlerde bir sıfır olarak görünür ve sistem tarafından ayrılmış kaynaklardan bir smallrc düzeyi kaynak izni sağlar.

> [!NOTE] 
> En iyi kaynak kullanımını sağlamak için, SLA 'ların karşılandığından ve [iş yükü önem derecesine](sql-data-warehouse-workload-importance.md)göre erişilen paylaşılan kaynaklarla karışmasını sağlamak için bazı yalıtımla yararlanan bir iş yükü yönetimi çözümü düşünün.

## <a name="workload-containment"></a>İş yükü kapsama

İş yükü kapsamı, bir iş yükü grubunun kullanabileceği kaynak miktarını sınırlandırmayı ifade eder.  İş yükü kapsama, CAP_PERCENTAGE_RESOURCE parametresi, [Iş yükü grubu oluşturma](https://review.docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) sözdiziminde 100 ' den küçük olacak şekilde yapılandırılarak elde edilir.  Kullanıcıların, geçici sorgular aracılığıyla bir durum çözümlemesi çalıştırabilmeleri için sisteme okuma erişimi olması gereken senaryoyu göz önünde bulundurun.  Bu tür istekler, sistemde çalışan diğer iş yükleri üzerinde olumsuz bir etkiye sahip olabilir.  Kapsamayı yapılandırmak, kaynak miktarının sınırlı olmasını sağlar.

İş yükü kapsamayı yapılandırmak, en yüksek eşzamanlılık düzeyini örtülü olarak tanımlar.  CAP_PERCENTAGE_RESOURCE, %60 ve REQUEST_MIN_RESOURCE_GRANT_PERCENT olarak ayarlanan bir ile iş yükü grubu için 60 eşzamanlılık düzeyine kadar izin verilir.  En fazla eşzamanlılık belirlemek için aşağıda verilen yöntemi göz önünde bulundurun:

[En fazla eşzamanlılık] = [`CAP_PERCENTAGE_RESOURCE`]/[`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE] 
> 0 ' dan büyük bir düzeye sahip iş yükü grupları oluşturulduğunda, bir iş yükü grubunun etkin CAP_PERCENTAGE_RESOURCE %100 ' a ulaşmaz.  Etkin çalışma zamanı değerleri için [sys. DM _workload_management_workload_groups_stats](https://review.docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?view=azure-sqldw-latest) bölümüne bakın.

## <a name="resources-per-request-definition"></a>İstek tanımına göre kaynaklar

İş yükü grupları, her istek için ayrılan en düşük ve en yüksek kaynak miktarını tanımlamak için bir mekanizma sağlar. [Iş yükü oluşturma grubu](https://review.docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) sözdiziminde REQUEST_MIN_RESOURCE_GRANT_PERCENT ve REQUEST_MAX_RESOURCE_GRANT_PERCENT parametreleri.  Bu durumda kaynaklar CPU ve bellektir.  Bu değerleri yapılandırmak, sistemde ne kadar kaynak ve ne kadar eşzamanlılık düzeyi elde edilebileceğini belirler.

> [!NOTE] 
> REQUEST_MAX_RESOURCE_GRANT_PERCENT, REQUEST_MIN_RESOURCE_GRANT_PERCENT için belirtilen değere varsayılan değer olan isteğe bağlı bir parametredir.

Kaynak sınıfı seçme gibi, REQUEST_MIN_RESOURCE_GRANT_PERCENT yapılandırmak bir istek tarafından kullanılan kaynakların değerini ayarlar.  Küme değeri tarafından belirtilen kaynak miktarı, yürütülmeye başlamadan önce isteğe ayrılmak üzere garanti edilir.  Kaynak sınıflarından iş yükü gruplarına geçiş yapan müşteriler için, başlangıç noktası olarak kaynak sınıflarından iş yükü gruplarına eşleme [yapma](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md) makalesini takip edin.

REQUEST_MIN_RESOURCE_GRANT_PERCENT 'den büyük bir değere REQUEST_MAX_RESOURCE_GRANT_PERCENT yapılandırmak, sistemin istek başına daha fazla kaynak ayırmasına izin verir.  Bir istek zamanlarken, sistem isteğe bağlı kaynak ayırmayı, REQUEST_MIN_RESOURCE_GRANT_PERCENT ile REQUEST_MAX_RESOURCE_GRANT_PERCENT arasında, paylaşılan havuzdaki kaynak kullanılabilirliğine ve geçerli yükün sistemin.  Sorgu zamanlandığında kaynakların paylaşılan kaynak [havuzunda](#shared-pool-resources) bulunması gerekir.  

> [!NOTE] 
> REQUEST_MIN_RESOURCE_GRANT_PERCENT ve REQUEST_MAX_RESOURCE_GRANT_PERCENT, etkin MIN_PERCENTAGE_RESOURCE ve CAP_PERCENTAGE_RESOURCE değerlerine bağımlı geçerli değerler içermelidir.  Etkin çalışma zamanı değerleri için [sys. DM _workload_management_workload_groups_stats](https://review.docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?view=azure-sqldw-latest) bölümüne bakın.

## <a name="execution-rules"></a>Yürütme kuralları

Geçici raporlama sistemlerinde, müşteriler yanlışlıkla başkalarının üretkenliğini önemli ölçüde etkileyebilecek bir ard arda sorgu yürütebilir.  Sistem yöneticileri, sistem kaynaklarını boşaltmak için ard arda yapılan sorguları sonlandırmaya zorlanır.  İş yükü grupları, belirtilen değeri aşmış sorguları iptal etmek için bir sorgu yürütme zaman aşımı kuralını yapılandırma olanağı sunar.  Kural, [Iş yükü grubu oluşturma](https://review.docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) sözdiziminde `QUERY_EXECUTION_TIMEOUT_SEC` parametresi ayarlanarak yapılandırılır.

## <a name="shared-pool-resources"></a>Paylaşılan havuz kaynakları

Paylaşılan havuz kaynakları, yalıtım için yapılandırılmamış kaynaklardır.  MIN_PERCENTAGE_RESOURCE olarak ayarlanmış bir iş yükü grupları, istekleri yürütmek için paylaşılan havuzdaki kaynaklardan faydalanır.  MIN_PERCENTAGE_RESOURCE 'den büyük bir CAP_PERCENTAGE_RESOURCE fazla iş yükü grubu, paylaşılan kaynaklar da kullandı.  Paylaşılan havuzdaki kullanılabilir kaynak miktarı aşağıdaki şekilde hesaplanır.

[Paylaşılan havuz] = 100-[tüm iş yükü grupları genelinde `MIN_PERCENTAGE_RESOURCE` toplamı]

Paylaşılan havuzdaki kaynaklara erişim bir [önem derecesine](sql-data-warehouse-workload-importance.md) göre ayrılır.  Aynı önem düzeyine sahip istekler, ilk ve ilk çıkar temelinde paylaşılan havuz kaynaklarına erişir.

## <a name="next-steps"></a>Sonraki adımlar

- [Hızlı başlangıç: iş yükü yalıtımını yapılandırma](quickstart-configure-workload-isolation-tsql.md)
- [IŞ YÜKÜ GRUBU OLUŞTUR](https://review.docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)
- [Kaynak sınıflarını iş yükü gruplarına dönüştürün](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md).
