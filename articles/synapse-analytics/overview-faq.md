---
title: SSS-Azure SYNAPSE Analytics
description: Azure SYNAPSE Analytics hakkında SSS
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 10/25/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 9a0fb8ed8ac54fa866b6db7d8f808c011c0c6758
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101695830"
---
# <a name="azure-synapse-analytics-frequently-asked-questions"></a>Azure SYNAPSE Analytics hakkında sık sorulan sorular

Bu kılavuzda, Azure SYNAPSE Analytics için en sık sorulan soruları bulacaksınız.

## <a name="general"></a>Genel

### <a name="q-how-can-i-use-rbac-roles-to-secure-my-workspace"></a>S: çalışma alanım 'ı güvenli hale getirmek için RBAC rollerini nasıl kullanabilirim?

Y: Azure SYNAPSE, çalışma alanınızın güvenliğini basitleştirecek bir dizi rol ve kapsamı kullanıma sunacaktır.

SYNAPSE RBAC rolleri:
* SYNAPSE Yöneticisi
* SYNAPSE SQL Yöneticisi
* SYNAPSE Spark Yöneticisi
* SYNAPSE katkıda bulunan (Önizleme)
* SYNAPSE yapıt yayımcısı (Önizleme)
* SYNAPSE yapıt kullanıcısı (Önizleme)
* SYNAPSE Işlem Işleci (Önizleme)
* SYNAPSE kimlik bilgileri kullanıcısı (Önizleme)

SYNAPSE çalışma alanınızı güvenli hale getirmek için RBAC rollerini şu RBAC kapsamlarına atayın:
* Çalışma Alanları
* Spark havuzları
* Tümleştirme çalışma zamanları
* Bağlı hizmetler
* Kimlik bilgileri

Ayrıca, adanmış SQL havuzları ile bildiğiniz ve sevdiğiniz tüm güvenlik özelliklerine sahip olursunuz.

### <a name="q-how-do-i-control-dedicated-sql-pools-serverless-sql-pools-and-serverless-spark-pools"></a>S: adanmış SQL havuzlarını, sunucusuz SQL havuzlarını ve sunucusuz Spark havuzlarını denetim Nasıl yaparım?.

Y: başlangıç noktası olarak Azure SYNAPSE, Azure abonelik düzeyinde sunulan yerleşik maliyet analizi ve maliyet uyarıları ile birlikte çalışarak.

- Adanmış SQL havuzları-özel SQL havuzlarının boyutlarını oluşturup belirttiğinden, maliyet üzerinde maliyet ve denetim konusunda doğrudan görünürlüğe sahip olursunuz. Kullanıcıların Azure RBAC rolleri ile adanmış SQL havuzları oluşturmalarına veya ölçeklendirebileceği daha fazla denetim sağlayabilirsiniz.

- Sunucusuz SQL havuzları: günlük, haftalık ve aylık düzeyde harcamalarınızı sağlayan izleme ve maliyet yönetimi denetimleriniz vardır. Daha fazla bilgi için [bkz. sunucusuz SQL havuzu Için maliyet yönetimi](./sql/data-processed.md) . 

- Sunucusuz Spark havuzları-SYNAPSE RBAC rolleriyle Spark havuzları oluşturabileceğiniz kişileri kısıtlayabilirsiniz.  

### <a name="q-will-synapse-workspace-support-folder-organization-of-objects-and-granularity-at-ga"></a>S: SYNAPSE çalışma alanı destek klasörü, nesne ve ayrıntı düzeyi için GA.

A: SYNAPSE çalışma alanları Kullanıcı tanımlı klasörleri destekler.

### <a name="q-can-i-link-more-than-one-power-bi-workspace-to-a-single-azure-synapse-workspace"></a>S: birden fazla Power BI çalışma alanını tek bir Azure SYNAPSE çalışma alanına bağlayabilir miyim?
    
Y: Şu anda, tek bir Power BI çalışma alanını Azure SYNAPSE çalışma alanına bağlayabilirsiniz. 

### <a name="q-is-synapse-link-to-cosmos-db-ga"></a>S: SYNAPSE Cosmos DB GA bağlantısı var mı?

A: Apache Spark için SYNAPSE bağlantısı GA 'dir. Sunucusuz SQL havuzu için SYNAPSE bağlantısı genel önizlemede.

### <a name="q-does-azure-synapse-workspace-support-cicd"></a>S: Azure SYNAPSE çalışma alanı CI/CD 'yi destekliyor mu? 

Y: Evet! Tüm işlem hattı yapıtları, Not defterleri, SQL betikleri ve Spark iş tanımları git içinde yer alır. Tüm havuz tanımları, git 'te ARM şablonları olarak depolanır. Adanmış SQL havuzu nesneleri (şemalar, tablolar, görünümler, vb.), CI/CD desteğiyle veritabanı projeleriyle yönetilir.

## <a name="pipelines"></a>Pipelines

### <a name="q-how-do-i-ensure-i-know-what-credential-is-being-used-to-run-a-pipeline"></a>S: bir ardışık düzen çalıştırmak için hangi kimlik bilgilerinin kullanıldığını Nasıl yaparım? emin olun. 

Y: bir Synapse işlem hattındaki her etkinlik, bağlantılı hizmette belirtilen kimlik bilgisi kullanılarak yürütülür.

### <a name="q-are-ssis-irs-supported-in-synapse-integrate"></a>S: SYNAPSE tümleştirmede SSIS IRS destekleniyor mu?

Y: Şu anda değil. 

### <a name="q-how-do-i-migrate-existing-pipelines-from-azure-data-factory-to-an-azure-synapse-workspace"></a>S: var olan işlem hatlarını Azure Data Factory bir Azure SYNAPSE çalışma alanına geçirmek Nasıl yaparım? istiyor musunuz?

Y: Şu anda, JSON 'u özgün işlem hattından dışarı aktararak ve SYNAPSE çalışma alanınıza aktararak Azure Data Factory işlem hatlarınızı ve ilgili yapıtları el ile yeniden oluşturmanız gerekir.

## <a name="apache-spark"></a>Apache Spark

### <a name="q-what-is-the-difference-between-apache-spark-for-synapse-and-apache-spark"></a>S: SYNAPSE ve Apache Spark için Apache Spark arasındaki fark nedir?

Y: SYNAPSE için Apache Spark, diğer hizmetlerle (AAD, AzureML, vb.) tümleştirmelere ve ek kitaplıklara (msmini tuıls, Hummingbird) ve önceden ayarlanmış performans yapılandırmalarına sahip olan Apache Spark.

Apache Spark üzerinde çalışmakta olan herhangi bir iş yükü, Azure SYNAPSE için değişiklik yapılmadan Apache Spark çalışır. 

### <a name="q-what-versions-of-spark-are-available"></a>S: Spark 'ın hangi sürümleri mevcuttur?

Y: Azure SYNAPSE Apache Spark, Spark 2,4 ' i tam olarak destekler. Çekirdek bileşenlerin tam listesi ve şu anda desteklenen sürüm için [Apache Spark sürüm desteği](./spark/apache-spark-version-support.md)' ne bakın.

### <a name="q-is-there-an-equivalent-of-dbutils-in-azure-synapse-spark"></a>S: Azure SYNAPSE Spark 'ta DButils 'ın eşdeğeri var mı?

Y: Evet, Azure SYNAPSE Apache Spark, **msmini yardımcı programları** kitaplığı sağlar. Yardımcı programın tam belgeleri için bkz. [Microsoft Spark Utilities 'e giriş](./spark/microsoft-spark-utilities.md).

### <a name="q-how-do-i-set-session-parameters-in-apache-spark"></a>S: Apache Spark oturum parametreleri Nasıl yaparım? ayarla

Y: oturum parametrelerini ayarlamak Için%% configure Magic kullanılabilir öğesini kullanın. Parametrelerin etkili olması için bir oturum yeniden başlatması gerekiyor. 

### <a name="q-how-do-i-set-cluster-level-parameters-in-a-serverless-spark-pool"></a>S: sunucusuz Spark havuzunda küme düzeyi parametrelerini ayarlamak Nasıl yaparım?.

Y: küme düzeyi parametrelerini ayarlamak Için Spark havuzu için bir Spark. conf dosyası sağlayabilirsiniz. Bu havuz daha sonra yapılandırma dosyasında geçen parametreleri kabul eder. 

### <a name="q-can-i-run-a-multi-user-spark-cluster-in-azure-synapse-analytics"></a>S: Azure SYNAPSE Analytics 'te çok kullanıcılı bir Spark kümesi çalıştırabilir miyim?
 
Y: Azure SYNAPSE, belirli kullanım durumları için amaca özgü altyapı sağlar. SYNAPSE için Apache Spark, bir küme modeli değil, bir iş hizmeti olarak tasarlanmıştır. İnsanların çok kullanıcılı bir küme modeli sormasının iki senaryosu vardır.

**Senaryo #1: bir kümeye erişen ve bı amaçlarına yönelik verileri sunmak için çok sayıda kullanıcı.**

Bu görevi gerçekleştirmenin en kolay yolu Spark ile verileri aşmanız ve bu veri kümelerine Power BI bağlanabilmeleri için SYNAPSE SQL 'in hizmet özelliklerini kullanmanın avantajlarından faydalanır.

**Senaryo #2: bir para tasarrufu sağlamak için tek bir kümede birden fazla geliştirici vardır.**
 
Bu senaryoyu karşılamak için, her geliştiriciye, az sayıda Spark kaynağı kullanacak şekilde ayarlanmış bir sunucusuz Spark havuzu vermeniz gerekir. Sunucusuz Spark havuzları hiçbir şey yapmadığından, etkin olarak kullanılana kadar çok sayıda geliştirici olduğunda maliyeti en aza indirir. Havuzlar, birbirleriyle kolayca çalışabilmek için meta verileri (Spark tabloları) paylaşır.

### <a name="q-how-do-i-include-manage-and-install-libraries"></a>S: kitaplıkları dahil Nasıl yaparım?, yönetin ve yükler mi?

Y: Spark havuzunu oluştururken, SYNAPSE çalışma alanından veya Azure portal, harici paketleri bir requirements.txt dosyası aracılığıyla yükleyebilirsiniz. Bkz. [Azure SYNAPSE Analytics 'te Apache Spark için kitaplıkları yönetme](./spark/apache-spark-azure-portal-add-libraries.md).

## <a name="dedicated-sql-pools"></a>Adanmış SQL havuzları

### <a name="q-what-are-the-functional-differences-between-dedicated-sql-pools-and-serverless-pools"></a>S: adanmış SQL havuzları ve sunucusuz havuzlar arasındaki işlevsel farklılıklar nelerdir?

[Y: SYNAPSE SQL Içindeki T-SQL Özellik farklılıklarının](./sql/overview-features.md)tam bir listesini bulabilirsiniz.

### <a name="q-now-that-azure-synapse-is-ga-how-do-i-move-my-dedicated-sql-pools-that-were-previously-standalone-into-azure-synapse"></a>S: artık Azure SYNAPSE, Azure SYNAPSE 'e daha önce tek başına olan adanmış SQL havuzlarımı nasıl taşıyabilirim? 

Y: "taşı" veya "geçiş" yoktur. Mevcut Havuzlarınızda yeni çalışma alanı özelliklerini etkinleştirmeyi seçebilirsiniz. Bunu yaparsanız, hiçbir önemli değişiklik olmaz, bunun yerine SYNAPSE Studio, Spark ve sunucusuz SQL havuzları gibi yeni özellikleri kullanabilirsiniz.

### <a name="q-what-is-the-default-deployment-of-dedicated-sql-pools-now"></a>S: adanmış SQL havuzlarının varsayılan dağıtımı şimdi nedir? 

A: varsayılan olarak, tüm yeni adanmış SQL havuzları bir çalışma alanına dağıtılacak; Ancak, bir tek başına form faktöründe adanmış bir SQL Havuzu (eski adıyla SQL DW) oluşturmaya devam edebilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Synapse Analytics ile çalışmaya başlama](get-started.md)
* [Çalışma alanı oluşturma](quickstart-create-workspace.md)
* [Sunucusuz SQL havuzu kullanma](quickstart-sql-on-demand.md)
