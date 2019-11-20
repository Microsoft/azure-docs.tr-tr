---
title: Azure Data Factory ile Data Factory sürüm 1'in karşılaştırılması
description: Bu makale Azure Data Factory ile Azure Data Factory sürüm 1’i karşılaştırır.
services: data-factory
documentationcenter: ''
author: kromerm
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: overview
ms.date: 04/09/2018
ms.author: makromer
ms.openlocfilehash: cab4f156aa1fb2fa4d3b122377be1b28337f15a9
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681600"
---
# <a name="compare-azure-data-factory-with-data-factory-version-1"></a>Azure Data Factory ile Data Factory sürüm 1'in karşılaştırılması
Bu makale Data Factory ile Data Factory sürüm 1’i karşılaştırır. Data Factory hakkında giriş bilgileri için bkz. [Data Factory'ye giriş](introduction.md).Data Factory sürüm 1 hakkında giriş bilgileri için bkz. [Azure Data Factory'ye Giriş](v1/data-factory-introduction.md). 

## <a name="feature-comparison"></a>Özellik karşılaştırması
Aşağıdaki tabloda Data Factory ile Data Factory sürüm 1'in özellikleri karşılaştırılmaktadır. 

| Özellik | Sürüm 1 | Geçerli sürüm | 
| ------- | --------- | --------- | 
| Veri kümeleri | Etkinliklerinizde girdi ve çıktı olarak kullanmak istediğiniz verilere başvuran verilerin adlandırılmış bir görünümüdür. Veri kümeleri tablolar, dosyalar, klasörler ve belgeler gibi farklı veri depolarındaki verileri tanımlar. Örneğin Azure Blob veri kümesi, etkinliğin verileri okuması için gereken blob kapsayıcısını ve Azure Blob depolama klasörünü belirtir.<br/><br/>**Kullanılabilirlik**, veri kümesi (saatlik, günlük gibi) için model dilimleme işleme penceresini tanımlar. | Veri kümeleri geçerli sürümde aynıdır. Ancak, veri kümeleri için **kullanılabilirlik** zaman çizelgelerini tanımlamanız gerekmez. İşlem hatlarını bir zaman çizelgesi oluşturma paradigmasından zamanlayabilen bir tetikleyici kaynak belirleyebilirsiniz. Daha fazla bilgi için [Tetikleyiciler](concepts-pipeline-execution-triggers.md#triggers) ve [Veri Kümeleri](concepts-datasets-linked-services.md) bölümlerine bakın. | 
| Bağlı hizmetler | Bağlı hizmetler, dış kaynaklara bağlanmak için Data Factory'ye gereken bağlantı bilgilerini tanımlayan bağlantı dizelerine çok benzer. | Bağlı hizmetler Data Factory V1 ile aynıdır, ancak Data Factory'nin geçerli sürümünün Integration Runtime bilgi işlem ortamından yararlanmak için yeni bir **connectVia** özelliği de bulunur. Daha fazla bilgi için bkz. [Azure Data Factory’de tümleştirme çalışma zamanı](concepts-integration-runtime.md) ve [Azure Blob depolama için bağlı hizmeti özellikleri](connector-azure-blob-storage.md#linked-service-properties). |
| İşlem hatları | Bir veri fabrikasında bir veya daha fazla işlem hattı olabilir. İşlem hattı, bir araya geldiğinde bir görev gerçekleştiren mantıksal etkinlik grubudur. İşlem hatlarını zamanlamak ve çalıştırmak için startTime, endTime ve isPaused kullanırsınız. | İşlem hatları, veriler üzerinde uygulanacak etkinlik gruplarıdır. Ancak işlem hattındaki etkinliklerin zamanlanması, tetikleyici kaynaklarına ayrılmıştır. Data Factory'nin geçerli sürümü üzerindeki işlem hatlarını, tetikleyiciler aracılığıyla ayrı olarak zamanladığınız “iş akışı birimleri” olarak düşünebilirsiniz. <br/><br/>Data Factory'nin geçerli sürümünde, işlem hatlarının yürütüleceği zaman “pencereleri” yoktur. Data Factory V1’in startTime, endTime, ve isPaused kavramları, Data Factory'nin geçerli sürümünde bulunmaz. Daha fazla bilgi bkz. [İşlem hattı yürütme ve tetikleyicileri](concepts-pipeline-execution-triggers.md) ve [İşlem hatları ve etkinlikler](concepts-pipelines-activities.md). |
| Etkinlikler | Etkinlikler, bir işlem hattındaki verilerinizde gerçekleştirilecek eylemleri tanımlar. Veri taşıma (kopyalama etkinliği) ve veri dönüştürme etkinlikleri (Hive, Pig ve MapReduce gibi) desteklenir. | Geçerli Data Factory sürümünde, etkinlikler bir işlem hattının içinde tanımlanmış eylemlerdir. Data Factory geçerli sürümü, yeni [Denetim akışı etkinliklerini](concepts-pipelines-activities.md#control-activities)tanıtır. Bu etkinlikleri denetim akışında (döngü ve dal oluşturma) kullanırsınız. V1’de desteklenen veri taşıma ve veri dönüştürmek etkinlikleri, geçerli sürümde de desteklenir. Geçerli sürümde dönüşüm etkinliklerini veri kümelerini kullanmadan da tanımlayabilirsiniz. |
| Karma veri taşıma ve etkinlik dağıtma | Adı Integration Runtime olarak değişen [Veri Yönetimi Ağ Geçidi](v1/data-factory-data-management-gateway.md), şirket içi ile bulut arasında veri taşıma desteği sunuyordu.| Veri Yönetimi Ağ Geçidi artık Şirket İçinde Barındırılan Integration Runtime olarak adlandırılmaktadır. V1’deki özelliklerin aynısını sunar. <br/><br/> Data Factory'nin geçerli sürümündeki Azure-SSIS Integration Runtime, bulutta SQL Server Integration Services (SSIS) dağıtımını ve çalıştırılmasını destekler. Daha fazla bilgi için bkz. [Azure Data Factory'de tümleştirme çalışma zamanı](concepts-integration-runtime.md).|
| Parametreler | NA | Parametreler, işlem hatlarında tanımlanan salt okunur yapılandırma ayarlarının anahtar-değer çiftleridir. İşlem hattını el ile çalıştırırken, bağımsız değişkenleri parametrelere geçirebilirsiniz. Bir zamanlayıcı tetikleyicisi kullanıyorsanız, tetikleyici de parametrelere ilişkin değerleri geçirebilir. İşlem hattındaki etkinlikler parametre değerlerini kullanır.  |
| İfadeler | Data Factory V1, veri seçimi sorgularında ve etkinlik/veri kümesi özelliklerinde işlevleri ve sistem değişkenlerini kullanmanızı sağlar. | Data Factory'nin geçerli sürümünde, ifadeleri JSON dizesi değerinin tüm kısımlarında kullanabilirsiniz. Daha fazla bilgi için bkz. [Data Factory'nin geçerli sürümünde ifadeler ve işlevler](control-flow-expression-language-functions.md).|
| İşlem hattı çalıştırmaları | NA | İşlem hattı yürütmesinin tek örneği. Örneğin, 08:00, 09:00 ve 10:00'da çalışan bir işlem hattınız olduğunu kabul edelim. Bu durumda işlem hattının üç ayrı çalıştırması (işlem hattı çalıştırması) olacaktır. Her işlem hattı çalıştırması benzersiz bir işlem hattı çalıştırma kimliğine sahiptir. İşlem hattı çalıştırma kimliği, belirli bir işlem hattı çalıştırmasını benzersiz bir şekilde tanımlayan bir GUID’dir. İşlem hattı çalıştırmaları örneği genelde bağımsız değişkenlerin işlem hatlarında tanımlanan parametrelere iletilmesiyle oluşturulur. |
| Etkinlik çalıştırmaları | NA | Bir işlem hattı içinde bir etkinlik yürütmesi örneği. | 
| Tetikleyici çalıştırmaları | NA | Bir tetikleyici yürütmesi örneği. Daha fazla bilgi için bkz. [Tetikleyiciler](concepts-pipeline-execution-triggers.md). |
| Zamanlama | Zamanlama, işlem hattının başlangıç/bitiş zamanlarına ve veri kümesinin kullanılabilirliğine dayanır. | Zamanlayıcı tetikleyicisi veya dış zamanlayıcı aracılığıyla yürütme. Daha fazla bilgi için bkz. [İşlem hattı yürütme ve tetikleyiciler](concepts-pipeline-execution-triggers.md). |

Aşağıdaki bölümlerde geçerli sürümün özellikleri hakkında daha fazla bilgi verilmektedir. 

## <a name="control-flow"></a>Denetim akışı  
Data Factory'nin geçerli sürümü, modern veri ambarında çeşitli tümleştirme akışlarını ve desenlerini desteklemek için artık zaman dizisi verilerine bağlı olmayan yeni bir esnek veri işlem hattı modelini etkinleştirmiştir. Daha önce mümkün olmayıp şu anda etkin olan birkaç yaygın akış mevcuttur. Bunlar aşağıdaki bölümlerde açıklanmıştır.   

### <a name="chaining-activities"></a>Zincirleme etkinlikleri
V1’de, etkinlikleri zincirlemek için bir etkinliğin çıktısını, başka bir etkinliğin girdisi olarak yapılandırmanız gerekiyordu. Geçerli sürümde ise bir işlem hattı içindeki sıralı etkinlikleri zincirleyebilirsiniz. Bir etkinliği yukarı akış etkinliğiyle zincirlemek için etkinliğin içindeki **dependsOn** özelliğini kullanabilirsiniz. Daha fazla bilgi almak ve örnekleri incelemek için [İşlem hatları ve etkinlikler](concepts-pipelines-activities.md#multiple-activities-in-a-pipeline) ile [Dal oluşturma ve zincirleme etkinlikleri](tutorial-control-flow.md) makalelerine bakın. 

### <a name="branching-activities"></a>Dal oluşturma etkinlikleri
Geçerli sürümde bir işlem hattı içindeki etkinlikleri dallara ayırabilirsiniz. [If-condition etkinliği](control-flow-if-condition-activity.md), programlama dillerindeki `if` deyimiyle aynı işlevselliği sağlar. Koşul `true` sonucunu verdiğinde bir dizi etkinliği, `false` sonucu verdiğinde ise başka bir dizi etkinliği değerlendirmeye alır. Dal oluşturma etkinliklerinin örneklerini görmek için [Dal oluşturma ve zincirleme etkinlikleri](tutorial-control-flow.md) öğreticisine bakın.

### <a name="parameters"></a>Parametreler 
İşlem hattı düzeyinde parametre tanımlayabilir ve işlem hattı talep üzerine ya da bir tetikleyici ile çağrılırken bağımsız değişkenler geçirebilirsiniz. Etkinlikler işlem hattına geçirilen bağımsız değişkenleri kullanabilir. Daha fazla bilgi için bkz. [İşlem hatları ve tetikleyiciler](concepts-pipeline-execution-triggers.md). 

### <a name="custom-state-passing"></a>Özel durum geçirme
Durum da dahil olmak üzere etkinlik çıktıları, işlem hattının sonraki bir etkinliği tarafından kullanılabilir. Örneğin, bir etkinliğin JSON tanımlamasında, aşağıdaki söz dizimini kullanarak bir önceki etkinliğin çıktısına erişebilirsiniz: `@activity('NameofPreviousActivity').output.value`. Bu özelliği kullanarak, değerlerin etkinliklerden geçebileceği iş akışları oluşturabilirsiniz.

### <a name="looping-containers"></a>Döngü kapsayıcıları
[ForEach etkinliği](control-flow-for-each-activity.md), işlem hattınızda yinelenen bir denetim akışını tanımlar. Bu etkinlik bir koleksiyon üzerinde yinelemek için kullanılır ve bir döngüde belirtilen etkinlikleri çalıştırır. Bu etkinliğin döngü uygulaması, programlama dillerindeki Foreach döngü yapısına benzer. 

[Until](control-flow-until-activity.md) etkinliği, programlama dillerindeki do-until döngü yapısıyla aynı işlevselliği sağlar. Etkinlikle ilişkilendirilmiş olan koşul `true` sonucunu verene kadar bir dizi etkinliği döngüsel olarak çalıştırır. Data Factory'de bitiş etkinliği için bir zaman aşımı değeri belirtebilirsiniz.  

### <a name="trigger-based-flows"></a>Tetikleyici temelli akışlar
İşlem hatları talep üzerine (blob gönderisi gibi olay tabanlı) veya duvar saati zamanına göre tetiklenebilir. [İşlem hatları ve tetikleyiciler](concepts-pipeline-execution-triggers.md) makalesi, tetikleyiciler hakkında ayrıntılı bilgiler sunar. 

### <a name="invoking-a-pipeline-from-another-pipeline"></a>Bir işlem hattını başka bir işlem hattından çağırma
[İşlem Hattı Yürütme etkinliği](control-flow-execute-pipeline-activity.md) bir Data Factory işlem hattının başka bir işlem hattını çağırmasını sağlar.

### <a name="delta-flows"></a>Delta akışlar
ETL düzenlerindeki bir anahtar kullanım örneği, bir işlem hattının son yinelenmesinden itibaren yalnızca verilerin değiştiği “değişiklik yüklemeleri”dir. Geçerli sürümdeki [arama etkinliği](control-flow-lookup-activity.md), esnek zamanlama ve denetim akışı gibi yeni özellikler, bu kullanım örneğini doğal bir biçimde sunar. Adım adım yönergeler içeren bir öğretici için bkz. [Öğretici: Artımlı kopya](tutorial-incremental-copy-powershell.md).

### <a name="other-control-flow-activities"></a>Diğer denetim akışı etkinlikleri
Data Factory'nin geçerli sürümü tarafından desteklenen denetim akışı etkinliklerinin bazıları aşağıda verilmiştir. 

Denetim etkinliği | Açıklama
---------------- | -----------
[ForEach etkinliği](control-flow-for-each-activity.md) | İşlem hattınızda yinelenen bir denetim akışını tanımlar. Bu etkinlik bir koleksiyon üzerinde yinelemek için kullanılır ve bir döngüde belirtilen etkinlikleri çalıştırır. Bu etkinliğin döngü uygulaması, programlama dillerindeki Foreach döngü yapısına benzer.
[Web etkinliği](control-flow-web-activity.md) | Bir Data Factory işlem hattından özel bir REST uç noktasını çağırmak için kullanılabilir. Etkinlik tarafından kullanılacak ve erişilecek veri kümelerini ve bağlı hizmetleri geçirebilirsiniz. 
[Arama etkinliği](control-flow-lookup-activity.md) | Herhangi bir dış kaynaktan bir kaydı veya tablo adı değerini okur ya da arar. Sonraki etkinliklerde bu çıktıya daha fazla başvurulabilir. 
[Meta veri alma etkinliği](control-flow-get-metadata-activity.md) | Azure Data Factory’deki herhangi bir verinin meta verilerini alır. 
[Bekleme etkinliği](control-flow-wait-activity.md) | İşlem hattını belirli bir süre boyunca duraklatır.

## <a name="deploy-ssis-packages-to-azure"></a>SSIS paketlerini Azure'a dağıtma 
SSIS iş yüklerinizi buluta taşımak, geçerli sürümü kullanarak bir veri fabrikası oluşturmak ve bir Azure-SSIS Integration Runtime sağlamak istiyorsanız Azure-SSIS kullanırsınız.

Azure-SSIS Integration Runtime, bulutta SSIS paketlerinizi çalıştırmaya ayrılmış Azure VM'lerin (düğümler) tam yönetilen bir kümesidir. Azure-SSIS Integration Runtime sağladıktan sonra, SSIS paketlerini şirket içi SSIS ortamına dağıtmak için kullandığınız araçları kullanabilirsiniz. 

Örneğin, SQL Server Veri Araçları veya SQL Server Management Studio’yu kullanarak Azure’da bu çalışma zamanına SSIS paketleri dağıtabilirsiniz. Adım adım yönergeler için bkz. [SQL Server tümleştirme hizmetleri paketlerini Azure'a dağıtma](tutorial-create-azure-ssis-runtime-portal.md). 

## <a name="flexible-scheduling"></a>Esnek zamanlama
Data Factory'nin geçerli sürümünde veri kümesi kullanılabilirlik zaman çizelgelerini tanımlamanız gerekmez. İşlem hatlarını bir zaman çizelgesi oluşturma paradigmasından zamanlayabilen bir tetikleyici kaynak belirleyebilirsiniz. Esnek bir zamanlama ve yürütme modeli için, parametreleri tetikleyiciden işlem hatlarına geçirebilirsiniz. 

Data Factory'nin geçerli sürümünde, işlem hatlarının yürütüleceği zaman “pencereleri” yoktur. Data Factory V1’in startTime, endTime, ve isPaused kavramları, Data Factory'nin geçerli sürümünde mevcut değildir. Data Factory'nin geçerli sürümünde işlem hattı oluşturma ve ardından bunu zamanlama hakkında daha fazla bilgi için bkz. [İşlem hattı yürütme ve tetikleyiciler](concepts-pipeline-execution-triggers.md).

## <a name="support-for-more-data-stores"></a>Daha fazla veri deposu desteği
Geçerli sürümde V1’e kıyasla daha fazla veri deposuna ve daha fazla veri deposundan veri kopyalanabilir. Desteklenen veri depoları listesi için aşağıdaki makalelere bakın:

- [Sürüm 1 - Desteklenen veri depoları](v1/data-factory-data-movement-activities.md#supported-data-stores-and-formats)
- [Geçerli sürüm - Desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats)

## <a name="support-for-on-demand-spark-cluster"></a>İsteğe bağlı Spark kümesi desteği
Geçerli sürüm, isteğe bağlı Azure HDInsight Spark kümesi oluşturulmasını destekler. İsteğe bağlı bir Spark kümesi oluşturmak için, steğe bağlı HDInsight bağlı servis tanımınızdaki küme türünü Spark olarak belirtin. Ardından, işlem hattınızdaki Spark etkinliğini bu bağlı hizmeti kullanacak biçimde yapılandırabilirsiniz. 

Çalışma zamanında, etkinlik yürütüldüğünde, Data Factory hizmeti otomatik olarak Spark kümesini oluşturur. Daha fazla bilgi için aşağıdaki makalelere bakın:

- [Data Factory'nin geçerli sürümündeki Spark etkinliği](transform-data-using-spark.md)
- [İsteğe bağlı Azure HDInsight bağlı hizmeti](compute-linked-services.md#azure-hdinsight-on-demand-linked-service)

## <a name="custom-activities"></a>Özel etkinlikler
V1’de, IDotNetActivity arabiriminin Execute yöntemini uygulayan bir sınıfa sahip olan bir .NET sınıf kitaplığı projesi oluşturarak (özel) DotNet Etkinliği kodu uygularsınız. Bu nedenle özel kodunuzu .NET Framework 4.5.2’de yazmanız ve Windows tabanlı Azure Batch Havuzu düğümlerinde çalıştırmanız gerekir. 

Geçerli sürümdeki özel etkinlikte .NET arabirimi uygulamanız gerekmez. Doğrudan komutlarla betikler çalıştırabilir ve yürütülmeye uygun olan kendi özel kodunuzu çalıştırabilirsiniz. 

Daha fazla bilgi için bkz. [Data Factory ve sürüm 1’de özel etkinlikler arasındaki farklar](transform-data-using-dotnet-custom-activity.md#compare-v2-v1).

## <a name="sdks"></a>SDK'lar
 Data Factory'nin geçerli sürümü işlem hatlarını yazmak, yönetmek ve izlemek için kullanılabilen daha geniş SDK seçenekleri sunar.

- **.NET SDK**: .NET SDK, geçerli sürüm için güncelleştirilmiştir.

- **PowerShell**: PowerShell cmdlet'leri geçerli sürüm için güncelleştirilmiştir. Geçerli sürümün cmdlet 'leri adında **DataFactoryV2** vardır, örneğin: Get-AzDataFactoryV2. 

- **Python SDK**: Bu SDK geçerli sürüm içindir.

- **REST API**: REST API, geçerli sürüm için güncelleştirilmiştir. 

Geçerli sürüm için güncelleştirilmiş olan SDK'lar V1 istemcileriyle uyumlu değildir. 

## <a name="authoring-experience"></a>Yazma deneyimi

| &nbsp; | V2 | V1 |
| ------ | -- | -- | 
| Azure portal | [Evet](quickstart-create-data-factory-portal.md) | Hayır |
| Azure PowerShell | [Evet](quickstart-create-data-factory-powershell.md) | [Evet](data-factory-build-your-first-pipeline-using-powershell.md) |
| .NET SDK | [Evet](quickstart-create-data-factory-dot-net.md) | [Evet](data-factory-build-your-first-pipeline-using-vs.md) |
| REST API | [Evet](quickstart-create-data-factory-rest-api.md) | [Evet](data-factory-build-your-first-pipeline-using-rest-api.md) |
| Python SDK | [Evet](quickstart-create-data-factory-python.md) | Hayır |
| Resource Manager şablonu | [Evet](quickstart-create-data-factory-resource-manager-template.md) | [Evet](data-factory-build-your-first-pipeline-using-arm.md) | 

## <a name="roles-and-permissions"></a>Roller ve izinler

Data Factory sürüm 1 Katkıda Bulunan rolü, Data Factory'nin geçerli sürümünde kaynakları oluşturmak ve yönetmek için kullanılabilir. Daha fazla bilgi için bkz. [Data Factory Katılımcısı](../role-based-access-control/built-in-roles.md#data-factory-contributor).

## <a name="monitoring-experience"></a>İzleme deneyimi
Geçerli sürümde [Azure İzleyici](monitor-using-azure-monitor.md)’yi kullanarak veri fabrikalarını izleyebilirsiniz. Yeni PowerShell cmdlet’leri, [tümleştirme çalışma zamanlarını](monitor-integration-runtime.md) izlemeyi destekler. V1 ve V2, Azure portalından başlatılabilen izleme uygulaması aracılığıyla görsel izleme desteği sunar.


## <a name="next-steps"></a>Sonraki adımlar
Veri fabrikası oluşturma hakkında bilgi edinmek için şu hızlı başlangıçlarda verilen adım adım yönergeleri izleyin: [PowerShell](quickstart-create-data-factory-powershell.md), [.NET](quickstart-create-data-factory-dot-net.md), [Python](quickstart-create-data-factory-python.md), [REST API](quickstart-create-data-factory-rest-api.md). 
