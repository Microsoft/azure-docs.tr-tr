---
title: Veri tümleştirme hizmeti Data Factory giriş
description: 'Azure Data Factory’nin ne olduğunu öğrenin: verilerin taşınmasını ve dönüştürülmesini düzenleyen ve otomatikleştiren bir bulut veri tümleştirme hizmetidir.'
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: cec68cb5-ca0d-473b-8ae8-35de949a009e
ms.service: data-factory
ms.workload: data-services
ms.topic: overview
ms.date: 01/22/2018
ms.openlocfilehash: 30578f204ff05443de82015627d67d3d4d357dce
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73666809"
---
# <a name="introduction-to-azure-data-factory"></a>Azure Data Factory'ye giriş 
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](data-factory-introduction.md)
> * [Sürüm 2 (geçerli sürüm)](../introduction.md)

> [!NOTE]
> Bu makale, Azure Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız bkz. [Data Factory V2’ye giriş](../introduction.md).


## <a name="what-is-azure-data-factory"></a>Azure Data Factory nedir?
Büyük veri dünyasında, işletmede mevcut verilerden nasıl yararlanılır? Şirket içi veri kaynaklarından veya diğer dağınık veri kaynaklarından elde edilen başvuru verilerini kullanarak bulutta oluşturulan verileri zenginleştirmek mümkün mü? 

Örneğin, bir oyun şirketi, oyunlar tarafından üretilen günlükleri bulutta toplamaktadır. Şirket, bu günlükleri analiz ederek müşteri tercihleri, demografik verileri, kullanım davranışı vb. hakkında bilgi sahibi olmak istemektedir. Ayrıca yukarı satış ve çapraz satış fırsatlarını belirlemek, işleri büyütmek için yeni cazip özellikler geliştirmek ve müşterilerine daha iyi bir deneyim sunmak istemektedir. 

Bu günlükleri analiz etmek için, şirketin şirket içi veri deposunda bulunan müşteri bilgileri, oyun bilgileri ve pazarlama kampanyası bilgileri gibi başvuru verilerini kullanması gerekir. Bu nedenle, şirket bulut veri deposundan günlük verilerini ve şirket içi veri deposundan başvuru verilerini almak istemektedir. 

Şirket sonraki adımda verileri bulutta Hadoop (Azure HDInsight) kullanarak işlemek istemektedir. Ayrıca sonuç verilerini Azure SQL Veri Ambarı gibi bir bulut veri ambarında veya SQL Server gibi bir şirket içi veri deposunda yayımlamak istemektedir. Şirket bu iş akışının haftada bir çalışmasını istemektedir. 

Şirketin hem şirket içindeki hem de bulut üzerindeki veri depolarından veri alabilen iş akışları oluşturabileceği bir platforma ihtiyacı vardır. Şirketin aynı zamanda Hadoop gibi var olan işlem hizmetlerini kullanarak verileri dönüştürme veya işlemenin yanı sıra sonuçları BI uygulamalarının kullanması için şirket içindeki veya bulut üzerindeki veri depolarında yayımlamaya ihtiyacı vardır. 

![Data Factory'ye genel bakış](media/data-factory-introduction/what-is-azure-data-factory.png) 

Azure Data Factory, bu tür senaryolara yönelik platformdur. *Bulutta veri hareketi ve veri dönüştürmeyi düzenleyip otomatikleştirmek için veri odaklı iş akışları oluşturmanıza olanak tanıyan, bulut tabanlı bir veri tümleştirme hizmetidir*. Azure Data Factory hizmetini kullanarak aşağıdaki görevleri gerçekleştirebilirsiniz: 

- Farklı veri depolarından veri alabilen veri odaklı iş akışları (işlem hattı olarak adlandırılır) oluşturabilir ve zamanlayabilirsiniz.

- Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics ve Azure Machine Learning gibi işlem hizmetlerini kullanarak verileri işleyebilir veya dönüştürebilirsiniz.

-  Çıktı verilerini iş zekası (BI) uygulamalarının kullanması için Azure SQL Veri Ambarı gibi veri depolarında yayımlayabilirsiniz.  

Bu, geleneksel bir Ayıklama-Dönüştürme-Yükleme (ETL) platformu yerine daha çok Ayıklama-Dönüştürme (EL) ve sonra Dönüştürme-Yükleme (TL) platformudur. Dönüştürmeler verileri türetilmiş sütun ekleme, satır sayısını belirleme, veri sıralama vb. yerine işlem hizmetlerini kullanarak işler. 

Şu anda Azure Data Factory'de iş akışları tarafından kullanılan ve üretilen veriler, *zaman dilimli verilerdir* (saatlik, günlük, haftalık vs.). Örneğin, bir işlem hattı günde bir kez giriş verilerini okuyabilir, verileri işleyebilir ve çıktı üretebilir. Bir iş akışını yalnızca bir kez de çalıştırabilirsiniz.  
  

## <a name="how-does-it-work"></a>Nasıl çalışır? 
Azure Data Factory’deki işlem hatları (veri odaklı iş akışları) genellikle aşağıdaki üç adımı gerçekleştirir:

![Azure Data Factory’nin üç aşaması](media/data-factory-introduction/three-information-production-stages.png)

### <a name="connect-and-collect"></a>Bağlanma ve toplama
Kuruluşların dağınık kaynaklarda yer alan çeşitli türlerde verileri vardır. Bilgi üretim sistemi oluşturmanın ilk adımı, gerekli tüm veri ve işlem kaynaklarını bağlamaktır. Bu kaynaklar SaaS hizmetleri, dosya paylaşımları, FTP ve web hizmetleri olabilir. Ardından takip eden işleme çalışmaları için gerektiğinde verileri merkezi bir konuma taşımanız gerekir.

Data Factory olmadığında, kuruluşların bu veri kaynaklarını ve işleme çalışmalarını tümleştirmek için özel veri taşıma bileşenleri oluşturması veya özel hizmetler yazması gerekir. Bu tür sistemleri tümleştirmenin ve bakımını yapmanın maliyeti yüksektir. Ayrıca bu sistemlerde tamamen yönetilebilir bir hizmetin sunduğu kurumsal sınıf izleme, uyarı oluşturma ve denetim özellikleri mevcut değildir.

Data Factory ile, veri işlem hattında Kopyalama Etkinliği’ni kullanarak hem şirket içinde hem de buluttaki kaynak veri depolarını daha fazla analiz için merkezi bir veri deposuna taşıyabilirsiniz. 

Örneğin, Azure Data Lake Store'da veri toplayabilir ve daha sonra Azure Data Lake Analytics işlem hizmetini kullanarak verileri dönüştürebilirsiniz. Verileri Azure blob depolama alanından toplayıp daha sonra Azure HDInsight Hadoop kümesi kullanarak da dönüştürebilirsiniz.

### <a name="transform-and-enrich"></a>Dönüştürme ve zenginleştirme
Veriler buluttaki merkezi bir veri deposuna sunulduktan sonra HDInsight Hadoop, Spark, Data Lake Analytics veya Machine Learning gibi işlem hizmetlerini kullanarak işleyin veya dönüştürün. Üretim ortamlarının güvenilir verilerle beslenmesi için sürdürülebilir ve denetlenebilir bir zamanlamaya göre dönüştürülmüş verileri güvenilir bir şekilde üretmeniz gerekir. 

### <a name="publish"></a>Yayımlama 
Dönüştürülen verileri buluttan SQL Server gibi şirket içi kaynaklara gönderebilirsiniz. Alternatif olarak BI uygulamaları, analiz araçları ve diğer uygulamalar tarafından kullanılmak üzere bulut depolama kaynaklarınızda tutabilirsiniz.

## <a name="key-components"></a>Başlıca bileşenler
Azure aboneliğinin bir veya birden çok Azure Data Factory örneği (veya veri fabrikası) olabilir. Azure Data Factory dört temel bileşenden oluşur. Bu bileşenler, üzerinde veri taşıma ve dönüştürme adımları ile veri odaklı iş akışları oluşturabileceğiniz platformu sağlamak üzere birlikte çalışır. 

### <a name="pipeline"></a>İşlem hattı
Bir veri fabrikasında bir veya daha fazla işlem hattı olabilir. İşlem hattı bir grup etkinliktir. İşlem hattındaki etkinlikler birlikte bir görev gerçekleştirir. 

Örneğin, bir işlem hattı Azure blobundan verileri alan ve ardından HDInsight kümesinde Hive sorgusu çalıştırarak verileri bölümlere ayıran bir grup etkinlik içerebilir. İşlem hattının avantajı, etkinliklerin her birini tek tek yönetmek yerine bir küme olarak yönetmenize olanak tanımasıdır. Örneğin, bağımsız etkinlikler zamanlamak yerine işlem hattını dağıtabilir ve zamanlayabilirsiniz. 

### <a name="activity"></a>Etkinlik
İşlem hattında bir veya daha fazla etkinlik olabilir. Etkinlikler, verilerinizde gerçekleştirilecek eylemleri tanımlayın. Örneğin, bir veri deposundan başka bir veri deposuna veri kopyalamak için kopyalama etkinliğini kullanabilirsiniz. Bir Hive etkinliğini de benzer şekilde kullanabilirsiniz. Hive etkinliği, verilerinizi dönüştürmek veya analiz etmek amacıyla Azure HDInsight kümesinde bir Hive sorgusu çalıştırır. Data Factory iki tür etkinliği destekler: veri taşıma etkinlikleri ve veri dönüştürme etkinlikleri.

### <a name="data-movement-activities"></a>Veri taşıma etkinlikleri
Data Factory’deki Kopyalama Etkinliği bir kaynak veri deposundan havuz veri deposuna verileri kopyalar. Herhangi bir kaynaktan gelen veriler herhangi bir havuza yazılabilir. Bir depoya veya depodan veri kopyalama hakkında bilgi edinmek için veri deposunu seçin. Data Factory aşağıdaki veri depolarını destekler:

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Daha fazla bilgi için bkz. [Kopyalama Etkinliğiyle veri taşıma](data-factory-data-movement-activities.md).

### <a name="data-transformation-activities"></a>Veri dönüştürme etkinlikleri
[!INCLUDE [data-factory-transformation-activities](../../../includes/data-factory-transformation-activities.md)]

Daha fazla bilgi için bkz. [Kopyalama Etkinliğiyle veri taşıma](data-factory-data-transformation-activities.md).

### <a name="custom-net-activities"></a>Özel .NET etkinlikleri
Kopyalama Etkinliğinin desteklemediği bir veri deposuna/veri deposundan veri taşımanız ya da kendi mantığınızı kullanarak verileri dönüştürmeniz gerekirse özel bir .NET etkinliği oluşturun. Özel bir etkinlik oluşturma ve kullanma hakkında ayrıntılı bilgi için bkz. [Azure Data Factory işlem hattında özel etkinlikler kullanma](data-factory-use-custom-activities.md).

### <a name="datasets"></a>Veri kümeleri
Bir etkinlik girdi olarak sıfır veya daha fazla veri kümesi ve çıktı olarak bir ya da daha fazla veri kümesi alır. Veri kümeleri, veri depolarındaki veri yapılarını temsil eder. Bu yapılar, etkinliklerinizde kullanmak istediğiniz verilere (giriş veya çıkış olarak) işaret eder veya başvurur. 

Örneğin Azure blob veri kümesi, işlem hattının verileri okuması gereken blob kapsayıcısını ve Azure blob depolama klasörünü belirtir. Veya bir Azure SQL tablosu veri kümesi, çıktı verilerinin etkinlik tarafından yazılacağı tabloyu belirtir. 

### <a name="linked-services"></a>Bağlı hizmetler
Bağlı hizmetler, dış kaynaklara bağlanmak için Data Factory'ye gereken bağlantı bilgilerini tanımlayan bağlantı dizelerine çok benzer. Şöyle düşünün: bağlı bir hizmet, veri kaynağıyla bağlantıyı tanımlar ve veri kümesi verilerin yapısını temsil eder. 

Örneğin, Azure Depolama bağlı hizmeti Azure Depolama hesabına bağlanacak bağlantı dizesini belirtir. Ayrıca, bir Azure blob veri kümesi blob kapsayıcıyı ve verileri içeren klasörü belirtir.   

Bağlı hizmetler Data Factory'de iki nedenle kullanılır:

* Bir *veri deposunu*, buradakilerle, ancak bunlarla sınırlı olmamak şartıyla göstermek için: şirket içi SQL Server veritabanı, Oracle veritabanı, dosya paylaşımı veya bir Azure blob depolama hesabı. Desteklenen veri depolarının bir listesi için [Veri taşıma etkinlikleri](#data-movement-activities) bölümüne bakın.

* Etkinlik yürütülmesini barındırabilen *işlem kaynağını* temsil etmek için. Örneğin, HDInsightHive etkinliği bir HDInsight Hadoop kümesinde yürütülür. Desteklenen işlem ortamlarının listesi için [Veri dönüştürme etkinlikleri](#data-transformation-activities) bölümüne bakın.

### <a name="relationship-between-data-factory-entities"></a>Data Factory varlıkları arasındaki ilişki

![Diyagram: Bir bulut veri tümleştirme hizmeti olan Data Factory - temel kavramlar](./media/data-factory-introduction/data-integration-service-key-concepts.png)

## <a name="supported-regions"></a>Desteklenen bölgeler
Şu anda Batı ABD, Doğu ABD ve Kuzey Avrupa bölgelerinde veri fabrikaları oluşturabilirsiniz. Ancak, verileri veri depoları arasında taşımak ve işlem hizmetlerini kullanarak verileri işlemek amacıyla veri fabrikası başka Azure bölgelerindeki veri depolarına ve işlem hizmetlerine erişebilir.

Azure Data Factory’nin kendisi verileri depolamaz. [Desteklenen veri depoları](#data-movement-activities) arasındaki veri taşıma işlemlerini yönetmek için veri odaklı iş akışları oluşturmanızı sağlar. Ayrıca diğer bölgelerdeki veya şirket içi ortamdaki [işlem hizmetlerini](#data-transformation-activities) kullanarak verileri işlemenizi sağlar. Hem programlama, hem de kullanıcı arabirimi mekanizmalarını kullanarak [iş akışlarını izlemenizi ve yönetmenizi](data-factory-monitor-manage-pipelines.md) de sağlar.

Data Factory yalnızca Batı ABD, Doğu ABD ve Kuzey Avrupa bölgelerinde kullanılabilir. Ancak, Data Factory'deki veri taşıma işlemlerini mümkün kılan hizmet birden fazla bölgede [genel olarak](data-factory-data-movement-activities.md#global) kullanılabilir. Veri deposunun güvenlik duvarı ardında kaldığı durumlarda şirket içi ortamınızda yüklü bir [Veri Yönetimi Ağ Geçidi](data-factory-move-data-between-onprem-and-cloud.md) bunun yerine verileri taşır.

Örneğin, Azure HDInsight kümesi ve Azure Machine Learning gibi işlem ortamlarınızın Batı Avrupa bölgesinde bulunduğunu varsayalım. Kuzey Avrupa bölgesinde Azure Data Factory örneği oluşturabilir ve kullanabilirsiniz. Ardından bunu kullanarak Batı Avrupa'daki işlem ortamlarınızda iş zamanlayabilirsiniz. Data Factory'nin işlem ortamınızda işi tetiklemesi birkaç milisaniye alsa da, bilgi işlem ortamınızda işin çalıştırılma süresi değişmez.

## <a name="get-started-with-creating-a-pipeline"></a>İşlem hattı oluşturmaya başlama
Azure Data Factory'de veri işlem hatları oluşturmak için bu araç veya API'lerden birini kullanabilirsiniz: 

- Visual Studio
- PowerShell
- .NET API’si
- REST API
- Azure Resource Manager şablonu

Veri işlem hatları ile veri fabrikaları oluşturmayı öğrenmek için aşağıdaki öğreticilerde yer alan adım adım yönergeleri izleyin:

| Öğretici | Açıklama |
| --- | --- |
| [İki bulut veri deposu arasında veri taşıma](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) |Blob depolamadan SQL veritabanına veri taşıyan bir işlem hattı ile veri fabrikası oluşturun. |
| [Hadoop kümesi kullanarak veri dönüştürme](data-factory-build-your-first-pipeline.md) |Bir Azure HDInsight (Hadoop) kümesinde Hive betiği çalıştırarak veri işleyen bir veri işlem hattı ile ilk Azure veri fabrikanızı oluşturun. |
| [Veri Yönetimi Ağ Geçidini kullanarak verileri şirket içi veri deposu ile bulut veri deposu arasında taşıma](data-factory-move-data-between-onprem-and-cloud.md) |Şirket içi SQL Server veritabanından Azure blobuna veri taşıyan bir işlem hattı ile veri fabrikası oluşturun. Adım adım kılavuzun bir parçası olarak makinenize Veri Yönetimi Ağ Geçidi yükleyip bunu yapılandıracaksınız. |
