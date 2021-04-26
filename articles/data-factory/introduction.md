---
title: Azure Data Factory'ye giriş
description: Verilerin taşınmasını ve dönüştürülmesini düzenleyen ve otomatikleştiren bir bulut veri tümleştirme hizmeti olan Azure Data Factory hakkında bilgi edinin.
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.topic: overview
ms.date: 09/30/2019
ms.openlocfilehash: 8fef4bbc0c0e77c1e152b8c2c44254e091b0e5a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100392553"
---
# <a name="what-is-azure-data-factory"></a>Azure Data Factory nedir?

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Büyük veri dünyasında ham ve düzensiz veriler genellikle ilişkisel, ilişkisel olmayan ve diğer depolama sistemlerinde depolanır. Ancak, ham veriler kendi başlarına analiz uzmanlarına, veri bilimcilerine veya iş karar mekanizmalarına anlamlı bilgiler sağlamak için uygun bağlama veya anlama sahip değildir. 

Büyük veriler, ham verilerin bu zenginleştirme mağazalarını uygulanabilir iş öngörülerine daraltmak için işlemleri düzenleyebilen ve işleştirebilecek bir hizmet gerektirir. Azure Data Factory, bu karmaşık karma ayıkla-dönüştür-yükle (ETL), ayıkla-yükle-dönüştür (ELT) ve veri tümleştirme projeleri için oluşturulmuş, yönetilen bir bulut hizmetidir.

Örneğin bulutta çalışan oyunların oluşturduğu petabaytlarca oyun günlüğünü toplayan bir oyun şirketini düşünün. Şirket müşteri tercihleri, demografik bilgiler ve kullanıcı davranışları hakkında içgörü elde etme amacıyla bu günlükleri analiz etmek istiyor. Ayrıca yukarı satış ve çapraz satış fırsatlarını belirlemek, yeni cazip özellikler geliştirmek, işleri büyütmek ve müşterilerine daha iyi bir deneyim sunmayı amaçlamaktadır.

Bu günlükleri analiz etmek için, şirketin şirket içi veri deposunda bulunan müşteri bilgileri, oyun bilgileri ve pazarlama kampanyası bilgileri gibi başvuru verilerini kullanması gerekir. Şirket bu verileri şirket içi veri deposundan bir bulut veri deposunda sahip olduğu ek günlük verileriyle bir arada kullanmak istemektedir. 

Öngörüleri ayıklamak için, bulutta (Azure HDInsight) bir Spark kümesi kullanarak birleştirilmiş verileri işlemeyi ve bu verilerin üzerine kolayca bir rapor oluşturmak için dönüştürülen verileri Azure SYNAPSE Analytics gibi bir bulut veri ambarında yayımlamayı planlamaktadır. Bu iş akışını otomatikleştirmek ve bunu günlük olarak izleyip yönetmek istiyor. Ayrıca bu iş akışını blob deposu kapsayıcısına dosyalar ulaştığında yürütmek istiyor.

Azure Data Factory, bu tür veri senaryolarını çözen platformdur. Veri *hareketini düzenlemek ve verileri ölçeklendirmeye dönüştürmek için veri odaklı iş akışları oluşturmanıza olanak sağlayan bulut tabanlı ETL ve veri tümleştirme hizmetidir*. Azure Data Factory platformunu kullanarak farklı veri depolarından veri alabilen veri odaklı iş akışları (işlem hattı olarak adlandırılır) oluşturabilir ve zamanlayabilirsiniz. Verileri görsel olarak veri akışları ile veya Azure HDInsight Hadoop, Azure Databricks ve Azure SQL veritabanı gibi işlem hizmetlerini kullanarak dönüştüren karmaşık ETL işlemleri oluşturabilirsiniz. 

Ayrıca, dönüştürülmüş verilerinizi iş zekası için Azure SYNAPSE Analytics for Business Intelligence (BI) uygulamaları gibi veri depolarında yayımlayabilirsiniz. Sonuç olarak, Azure Data Factory sayesinde ham veriler daha iyi iş kararları için anlamlı veri depoları ve veri gölleri halinde düzenlenebilir.

![Data Factory'nin üstten görünümü](media/data-flow/overview.png)

## <a name="how-does-it-work"></a>Nasıl çalışır?

Data Factory, veri mühendisleri için uçtan uca tam kapsamlı bir platform sunan bağlantılı sistemlerden oluşur.

### <a name="connect-and-collect"></a>Bağlanma ve toplama

Kuruluşlar şirket içinde, bulutta bulunan yapılandırılmış, yapılandırılmamış veya yarı yapılandırılmış ve tümü farklı aralık ve hızlarda gelen farklı kaynaklarda bulunan çeşitli veri türlerine sahiptir. 

Bilgi üretim sistemi oluşturmanın ilk adımı hizmet olarak yazılım (SaaS) hizmetleri, veritabanları, dosya paylaşımları, FTP, web hizmetleri gibi tüm gerekli veri kaynaklarına ve işleme çalışmalarına bağlanmaktır. Sonraki adım ise takip eden işleme çalışmaları için gerektiğinde verileri merkezi bir konuma taşımaktır.

Data Factory olmadığında, kuruluşların bu veri kaynaklarını ve işleme çalışmalarını tümleştirmek için özel veri taşıma bileşenleri oluşturması veya özel hizmetler yazması gerekir. Bu tür sistemleri tümleştirmenin ve bakımını yapmanın maliyeti yüksektir. Buna ek olarak bu sistemlerde tamamen yönetilebilir bir hizmetin sunduğu kurumsal sınıf izleme, uyarı oluşturma ve denetim özellikleri mevcut değildir.

Data Factory, veri işlem hattındaki [kopyalama etkinliğini](copy-activity-overview.md) kullanarak daha fazla analiz için hem şirket içi hem de bulut kaynak veri mağazalarından verileri bulutta bir merkezileşmeyi veri deposuna taşıyabilirsiniz. Örneğin, Azure Data Lake Storage verileri toplayabilir ve Azure Data Lake Analytics bir işlem hizmeti kullanarak verileri daha sonra dönüştürebilirsiniz. Verileri Azure Blob depolama alanından toplayıp daha sonra Azure HDInsight Hadoop kümesi kullanarak da dönüştürebilirsiniz.

### <a name="transform-and-enrich"></a>Dönüştürme ve zenginleştirme
Veriler buluttaki merkezi bir veri deposunda mevcut olduktan sonra, toplanan verileri ADF eşleme veri akışlarını kullanarak işleyin veya dönüştürün. Veri akışları, veri mühendislerinin Spark kümelerini veya Spark programlamayı anlamaya gerek kalmadan Spark üzerinde yürütülen veri dönüştürme grafikleri oluşturmasına ve bakımını uygulamasına olanak tanır.

Dönüştürmeleri el ile kodlayarak, ADF, HDInsight Hadoop, Spark, Data Lake Analytics ve Machine Learning gibi işlem hizmetlerinde dönüşümlerinizi yürütmek için dış etkinlikleri destekler.

### <a name="cicd-and-publish"></a>CI/CD ve yayımlama
Data Factory, Azure DevOps ve GitHub kullanarak veri işlem hatlarınızın [CI/CD 'si için tam destek sunar](continuous-integration-deployment.md) . Bu, tamamlanmış ürünü yayımlamadan önce ETL işlemlerinizi artımlı olarak geliştirmenize ve sunmanıza olanak tanır. Ham veriler iş için kullanılabilir biçime getirildikten sonra, verileri Azure Veri Ambarı, Azure SQL Veritabanı, Azure CosmosDB'ye veya şirket kullanıcılarınızın iş zekası araçlarından işaret edebildiği herhangi bir analiz altyapısına yükleyebilirsiniz.
### <a name="monitor"></a>İzleyici
Veri tümleştirme işlem hattınızı başarıyla oluşturup dağıtarak iyileştirilmiş verilerden iş değeri elde ettikten sonra, başarı ve hata oranları için zamanlanmış etkinlikleri ve işlem hatlarını izleyin. Azure Data Factory, Azure Izleyici, API, PowerShell, Azure Izleyici günlükleri ve Azure portal sistem durumu bölmeleri aracılığıyla işlem hattı izleme için yerleşik desteğe sahiptir.

## <a name="top-level-concepts"></a>Üst düzey kavramlar
Azure aboneliğinin bir veya birden çok Azure Data Factory örneği (veya veri fabrikası) olabilir. Azure Data Factory, aşağıdaki anahtar bileşenlerinden oluşur.
- Pipelines
- Etkinlikler
- Veri kümeleri
- Bağlı hizmetler
- Veri Akışları
- Tümleştirme Çalışma Zamanları

Bu bileşenler, üzerinde veri taşıma ve dönüştürme adımları ile veri odaklı iş akışları oluşturabileceğiniz platformu sağlamak üzere birlikte çalışır.

### <a name="pipeline"></a>İşlem Hattı
Bir veri fabrikasında bir veya daha fazla işlem hattı olabilir. İşlem hattı, bir iş birimini gerçekleştiren etkinliklerden oluşan mantıksal gruptur. İşlem hattındaki etkinlikler birlikte bir görevi gerçekleştirir. Örneğin, bir işlem hattı Azure blobundan verileri alan ve ardından HDInsight kümesinde Hive sorgusu çalıştırarak verileri bölümlere ayıran bir grup etkinlik içerebilir. 

İşlem hattının avantajı, etkinliklerin her birini tek tek yönetmek yerine bir küme olarak yönetmenize olanak tanımasıdır. Bir işlem hattındaki etkinlikler, sırayla çalışmak üzere birbirine zincirlenebilir veya paralel olarak birbirinden bağımsız çalışabilir.

### <a name="mapping-data-flows"></a>Veri akışlarını eşleme
Herhangi bir boyuttaki verileri dönüştürmek için kullanabileceğiniz veri dönüştürme mantığının grafiklerini oluşturun ve yönetin. Yeniden kullanılabilir bir veri dönüştürme yordamları kitaplığı oluşturabilir ve bu işlemleri ADF işlem hatlarınızın ölçeği genişletilmiş bir şekilde yürütebilirsiniz. Data Factory, mantığınızı, ihtiyacınız olduğunda açılan ve aşağı dönerek bir Spark kümesinde yürütecektir. Kümeleri yönetmeniz veya korumanız gerekmez.

### <a name="activity"></a>Etkinlik
Etkinlikler bir işlem hattındaki işleme adımını temsil eder. Örneğin, bir veri deposundan başka bir veri deposuna veri kopyalamak için kopyalama etkinliğini kullanabilirsiniz. Benzer şekilde, verilerinizi dönüştürmek veya analiz etmek amacıyla Azure HDInsight kümesinde bir Hive sorgusu çalıştıran bir Hive etkinliği kullanabilirsiniz. Data Factory üç tür etkinliği destekler: veri taşıma etkinlikleri, veri dönüştürme etkinlikleri ve denetim etkinlikleri.

### <a name="datasets"></a>Veri kümeleri
Veri kümeleri, veri depoları içinde etkinliklerinizde giriş veya çıkış olarak kullanmak istediğiniz verilere işaret eden veya başvuruda bulunan veri yapılarını temsil eder. 

### <a name="linked-services"></a>Bağlı hizmetler
Bağlı hizmetler, dış kaynaklara bağlanmak için Data Factory'ye gereken bağlantı bilgilerini tanımlayan bağlantı dizelerine çok benzer. Şöyle düşünün: bağlı bir hizmet, veri kaynağıyla bağlantıyı tanımlar ve veri kümesi verilerin yapısını temsil eder. Örneğin, Azure Depolama bağlı hizmeti Azure Depolama hesabına bağlanacak bağlantı dizesini belirtir. Ayrıca, bir Azure blob veri kümesi blob kapsayıcıyı ve verileri içeren klasörü belirtir.

Bağlı hizmetler Data Factory’de iki amaçla kullanılır:

- Bir SQL Server veritabanı, Oracle veritabanı, dosya paylaşma veya Azure Blob depolama hesabı içeren, ancak bunlarla sınırlı olmayan bir **veri deposunu** temsil etmek için. Desteklenen veri depolarının listesi için [kopyalama etkinliği](copy-activity-overview.md) makalesine bakın.

- Bir etkinliğin yürütülmesini barındırameyen bir **işlem kaynağını** temsil etmek için. Örneğin, HDInsightHive etkinliği bir HDInsight Hadoop kümesinde yürütülür. Dönüştürme etkinlikleri ve desteklenen işlem ortamlarının listesi için [veri dönüştürme](transform-data.md) makalesine bakın.

### <a name="triggers"></a>Tetikleyiciler
Tetikleyiciler, bir işlem hattı çalıştırmasının başlatılması gereken zamanı belirleyen işlem birimini temsil eder. Farklı etkinlik türleri için farklı tetikleyici türleri vardır.

### <a name="pipeline-runs"></a>İşlem hattı çalıştırmaları
İşlem hattı çalıştırması, işlem hattı yürütme örneğidir. İşlem hattı çalıştırmaları örneği genelde bağımsız değişkenlerin işlem hatlarında tanımlanan parametrelere iletilmesiyle oluşturulur. Bağımsız değişkenler el ile veya tetikleyici tanımı içinde geçirilebilir.

### <a name="parameters"></a>Parametreler
Parametreler salt okunur yapılandırmanın anahtar-değer çiftleridir.Parametreler işlem hattında tanımlanır. Tanımlı parametrelerin bağımsız değişkenleri, bir tetikleyici tarafından oluşturulan çalıştırma bağlamı veya el ile yürütülen işlem hattından yürütme sırasında geçirilir. İşlem hattındaki etkinlikler parametre değerlerini kullanır.

Veri kümesi, türü kesin olarak belirtilmiş bir parametre ve yeniden kullanılabilir/başvurulabilir bir varlıktır. Bir etkinlik, veri kümelerine başvurabilir ve veri kümesi tanımında belirtilen özellikleri kullanabilir.

Bağlı hizmet de türü kesin olarak belirtilmiş ve veri deposu ya da işlem ortamı ile bağlantı bilgilerini içeren bir parametredir. Bu da yeniden kullanılabilir/başvurulabilir bir varlıktır.

### <a name="control-flow"></a>Denetim akışı
Denetim akışı, işlem hattı düzeyinde ve işlem hattı talep üzerine ya da bir tetikleyiciden çağrılırken geçirilen bağımsız değişkenlerde tanımlanabilen dizi, dallanma ve parametrelerdeki zincirleme etkinliklerini içeren işlem hattı etkinliklerinin düzenlenmesidir. Ayrıca özel durum geçirme ve döngü kapsayıcılarını, diğer bir deyişle For-each yineleyicilerini içerir.

### <a name="variables"></a>Değişkenler
Değişkenler, geçici değerleri depolamak için ardışık düzen içinde kullanılabilir ve ayrıca işlem hatları, veri akışları ve diğer etkinlikler arasında değer geçirmeyi etkinleştirmek için parametrelerle birlikte kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar
Araştırılacak sonraki adım belgelerde aşağıdaki önemli adımları bulabilirsiniz:

- [Veri kümeleri ve bağlı hizmetler](concepts-datasets-linked-services.md)
- [İşlem hatları ve etkinlikler](concepts-pipelines-activities.md)
- [Tümleştirme çalışma zamanı](concepts-integration-runtime.md)
- [Veri akışlarını eşleme](concepts-data-flow-overview.md)
- [Azure portalındaki Data Factory kullanıcı arabirimi](quickstart-create-data-factory-portal.md)
- [Azure portalındaki Veri Kopyalama aracı](quickstart-create-data-factory-copy-data-tool.md)
- [PowerShell](quickstart-create-data-factory-powershell.md)
- [.NET](quickstart-create-data-factory-dot-net.md)
- [Python](quickstart-create-data-factory-python.md)
- [REST](quickstart-create-data-factory-rest-api.md)
- [Azure Resource Manager şablonu](quickstart-create-data-factory-resource-manager-template.md)
 
