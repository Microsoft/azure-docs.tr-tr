---
title: 'Azure Data Factory: sık sorulan sorular '
description: Azure Data Factory hakkında sık sorulan soruların yanıtlarını alın.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: 2da12bbc760ff06ad0737ed9d48e12ea81260655
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73674722"
---
# <a name="azure-data-factory-faq"></a>Azure Data Factory SSS
Bu makale Azure Data Factory hakkında sık sorulan soruların yanıtlarını sağlar.  

## <a name="what-is-azure-data-factory"></a>Azure Data Factory nedir? 
Data Factory, verilerin hareketini ve dönüştürülmesini otomatikleştiren, tam olarak yönetilen, bulut tabanlı bir veri tümleştirme ETL hizmetidir. Ham malzemeleri mamul mallar halinde dönüştürmek üzere ekipman çalıştıran bir fabrikada olduğu gibi, ham verileri toplayıp kullanıma kullanım bilgilerine dönüştüren mevcut hizmetleri düzenler Azure Data Factory. 

Azure Data Factory kullanarak verileri şirket içi ve bulut veri depoları arasında taşımak için veri odaklı iş akışları oluşturabilirsiniz. Ve veri akışları ile verileri işleyebilir ve dönüştürebilirsiniz. ADF Ayrıca Azure HDInsight, Azure Databricks ve SQL Server Integration Services (SSIS) tümleştirme çalışma zamanı gibi işlem hizmetlerini kullanarak el kodlu dönüşümler için dış işlem altyapılarını destekler. 

Data Factory, veri işlelerinizi Azure tabanlı bir bulut hizmetinde veya SSIS, SQL Server veya Oracle gibi kendi şirket içinde barındırılan bir işlem ortamında yürütebilirsiniz. İhtiyacınız olan eylemi gerçekleştiren bir işlem hattı oluşturduktan sonra, düzenli aralıklarla (saatlik, günlük veya haftalık) çalışacak şekilde zamanlayabilirsiniz (örneğin, zaman penceresi zamanlaması), veya işlem hattını bir olay örneğinden tetikleyebilirsiniz. Daha fazla bilgi için bkz. [Azure Data Factory'ye giriş](introduction.md).

### <a name="control-flows-and-scale"></a>Denetim akışları ve ölçek 
Modern veri ambarındaki farklı tümleştirme akışlarını ve desenlerini desteklemek için Data Factory esnek veri ardışık düzen modellemesini sağlar. Bu, koşullu yürütme, veri işlem hatlarında dallanma ve bu akışlar içindeki ve içindeki parametreleri açık bir şekilde geçirme özelliği dahil olmak üzere tam denetim akışı programlama paradigmalarına gerektirir. Denetim akışı Ayrıca, kopyalama etkinliği aracılığıyla ölçekli veri taşıma dahil olmak üzere, dış yürütme altyapılarına ve veri akışı özelliklerine etkinlik gönderimi aracılığıyla veri dönüştürmeyi de kapsar.

Data Factory, veri tümleştirmesi için gerekli olan ve zamanlamaya göre veya sürekli olarak dağıtılan herhangi bir akış stilini modelleyebilir. Bu modelin izin veren bazı yaygın akışlar şunlardır:   

- Denetim akışları:
    - Etkinlikler bir işlem hattının içinde bir dizi içinde birbirine zincirlenebilir.
    - Etkinlikler bir işlem hattı içinde dallanmış olabilir.
    - Parametreler:
        - Parametreler işlem hattı düzeyinde tanımlanabilir ve işlem hattı talep üzerine veya bir tetikleyiciden çağırdığınızda bağımsız değişkenler geçirilebilir.
        - Etkinlikler işlem hattına geçirilen bağımsız değişkenleri kullanabilir.
    - Özel durum geçirme:
        - Durum da dahil olmak üzere etkinlik çıkışları, işlem hattındaki sonraki bir etkinlik tarafından tüketilebilir.
    - Döngü kapsayıcıları:
        - Foreach etkinliği, bir döngüde belirtilen bir etkinlik koleksiyonu üzerinde yinelenecek. 
- Tetikleyici tabanlı akışlar:
    - İşlem hatları talep üzerine veya duvar saati zamanına göre tetiklenebilir.
- Delta akışları:
    - Parametreler, verileri Gölü yüklemek için şirket içinde veya bulutta bulunan bir ilişkisel depodan boyut ya da başvuru tablolarını taşırken Delta kopyası için yüksek su işaretini tanımlamak üzere kullanılabilir. 

Daha fazla bilgi için bkz. [öğretici: denetim akışları](tutorial-control-flow.md).

### <a name="data-transformed-at-scale-with-code-free-pipelines"></a>Veriler, kod içermeyen işlem hatları ile ölçeğe dönüştürüldü
Tarayıcı tabanlı yeni araç deneyimi, modern ve etkileşimli bir Web tabanlı deneyimle kod ücretsiz işlem hattı yazma ve dağıtımı sağlar.

Visual Data Developers ve veri mühendislerine yönelik Data Factory Web Kullanıcı arabirimi, işlem hatları oluşturmak için kullanacağınız kod içermeyen tasarım ortamıdır. Visual Studio Online git ile tamamen tümleşiktir ve hata ayıklama seçenekleriyle CI/CD ve yinelemeli geliştirme için tümleştirme sağlar.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>Gelişmiş kullanıcılar için zengin platformlar arası SDK 'lar
Data Factory v2, aşağıdakiler dahil olmak üzere en sevdiğiniz IDE 'yi kullanarak işlem hatlarını yazmak, yönetmek ve izlemek için kullanılabilen zengin bir SDK kümesi sağlar:
* Python SDK
* PowerShell CLı
* C# SDK’sı

Kullanıcılar, Data Factory v2 ile arabirim sağlamak için belgelenen REST API 'Leri de kullanabilir.

### <a name="iterative-development-and-debugging-by-using-visual-tools"></a>Görsel araçları kullanarak yinelemeli geliştirme ve hata ayıklama
Azure Data Factory görsel araçları, yinelemeli geliştirme ve hata ayıklamayı etkinleştirir. İşlem hatlarınızı, tek bir kod satırı yazmadan işlem hattı tuvalindeki **hata ayıklama** özelliğini kullanarak oluşturabilirsiniz ve test çalıştırmaları yapabilirsiniz. Test çalıştırmalarının sonuçlarını ardışık düzen tuvaliniz **Çıkış** penceresinde görüntüleyebilirsiniz. Test çalıştıralım başarılı olduktan sonra, işlem hattınızı daha fazla etkinlik ekleyebilir ve hata ayıklamaya yinelemeli bir şekilde devam edebilirsiniz. Ayrıca, devam eden test çalıştırmalarınızı iptal edebilirsiniz. 

**Hata Ayıkla**seçmeden önce Data Factory hizmetinde yaptığınız değişiklikleri yayımlamanız gerekmez. Bu, geliştirme, test veya üretim ortamlarında Veri Fabrikası iş akışlarınızı güncelleştirmeden önce yeni eklemelerin veya değişikliklerin beklendiği gibi çalıştığından emin olmak istediğiniz senaryolarda yararlı olacaktır. 

### <a name="ability-to-deploy-ssis-packages-to-azure"></a>SSIS paketlerini Azure 'a dağıtma olanağı 
SSIS iş yüklerinizi taşımak istiyorsanız, bir Data Factory oluşturabilir ve bir Azure-SSIS tümleştirme çalışma zamanı sağlayabilirsiniz. Azure-SSIS Integration Runtime, bulutta SSIS paketlerinizi çalıştırmaya ayrılmış Azure VM 'lerin (düğümler) tam yönetilen bir kümesidir. Adım adım yönergeler için bkz. [SSIS paketlerini Azure 'A dağıtma](tutorial-create-azure-ssis-runtime-portal.md) öğreticisi. 
 
### <a name="sdks"></a>SDK’lar
İleri düzey bir kullanıcıysanız ve bir programlama arabirimi arıyorsanız Data Factory, sık kullandığınız IDE 'yi kullanarak işlem hatlarını yazmak, yönetmek veya izlemek için kullanabileceğiniz zengin bir SDK kümesi sağlar. Dil desteği .NET, PowerShell, Python ve REST içerir.

### <a name="monitoring"></a>İzleme
Veri fabrikalarınızı PowerShell, SDK veya tarayıcı kullanıcı arabirimindeki görsel Izleme araçları aracılığıyla izleyebilirsiniz. İsteğe bağlı, tetikleyici tabanlı ve saat odaklı özel akışları etkili ve etkili bir şekilde izleyebilir ve yönetebilirsiniz. Var olan görevleri iptal edin, bir bakışta hatalara göz atın, ayrıntılı hata iletileri almak için detaya gidin ve tüm sorunları ayıklayın, bağlam değiştirme veya ekranlar arasında geri gidilmesini sağlayın. 

### <a name="new-features-for-ssis-in-data-factory"></a>SSIS Data Factory için yeni özellikler
2017 ' deki ilk genel önizleme sürümünden itibaren, Data Factory SSIS için aşağıdaki özellikleri ekledi:

-   Proje/paketlerin SSIS veritabanını (SSıSDB) barındırmak için Azure SQL veritabanı 'nın üç daha yapılandırması/çeşitleri desteği:
-   Sanal ağ hizmet uç noktaları ile SQL veritabanı
-   Yönetilen örnek
-   Elastik havuz
-   Klasik bir sanal ağın en üstündeki Azure Resource Manager sanal ağı, daha sonra kullanımdan kalkacak şekilde, Azure-SSIS tümleştirme çalışma zamanını, sanal ağ hizmeti ile SQL veritabanı için yapılandırılmış bir sanal ağa eklemenize/eklemenize olanak sağlayan destek uç noktalar/mı/şirket içi veri erişimi. Daha fazla bilgi için bkz. bir [Azure-SSIS tümleştirme çalışma zamanına bir sanal ağa ekleme](join-azure-ssis-integration-runtime-virtual-network.md).
-   Azure Active Directory (Azure AD) kimlik doğrulaması ve SSıSDB 'ye bağlanmak için SQL kimlik doğrulaması desteği ve Azure kaynakları için Data Factory yönetilen Kimliğiniz ile Azure AD kimlik doğrulamasına izin verme
-   Azure Hibrit Avantajı seçeneğinden önemli maliyet tasarruflarını kazanmak için kendi şirket içi SQL Server lisansınızı getirme desteği
-   Gelişmiş/Premium özellikleri, ek bileşenleri/uzantıları yüklemek için özel bir kurulum arabirimi ve bir iş ortağı ekosistemi kullanmanıza olanak tanıyan Azure-SSIS tümleştirme çalışma zamanının Enterprise Edition desteği. Daha fazla bilgi için bkz. [Enterprise Edition, özel kurulum ve SSIS için üçüncü taraf GENIŞLETILEBILIRLIĞI ADF](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/). 
-   SSIS 'in daha derin tümleştirilmesi, Data Factory Işlem hatlarında birinci sınıf Execute SSIS paketi etkinliklerini çağırmanıza/tetiklemenize ve onları SSMS aracılığıyla zamanlamanıza imkan tanıyan Data Factory. Daha fazla bilgi için bkz. Ayrıca, [ADF işlem hatlarında SSIS etkinlikleriyle ETL/ELT iş akışlarınızı modernleştirin ve genişletme](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/).


## <a name="what-is-the-integration-runtime"></a>Tümleştirme çalışma zamanı nedir?
Integration Runtime, farklı ağ ortamlarında aşağıdaki veri tümleştirme yeteneklerini sağlamak için Azure Data Factory tarafından kullanılan işlem altyapısıdır:

- **Veri taşıma**: veri taşıma için Integration Runtime, verileri kaynak ve hedef veri depoları arasında taşılarken, yerleşik bağlayıcılar, biçim dönüştürme, sütun eşleme, performans ve ölçeklenebilir veri aktarımı için destek sağlar.
- **Dağıtım etkinlikleri**: dönüştürme için Integration RUNTIME, SSIS paketlerini yerel olarak yürütme yeteneği sağlar.
- **SSIS paketlerini yürütme**: Integration RUNTIME, SSIS paketlerini yönetilen bir Azure işlem ortamında yerel olarak yürütür. Integration Runtime Ayrıca Azure HDInsight, Azure Machine Learning, SQL veritabanı ve SQL Server gibi çeşitli bilgi işlem hizmetlerinde çalışan dönüştürme etkinliklerinin dağıtımını ve izlenmesini de destekler.

Tümleştirme çalışma zamanının bir veya daha fazla örneğini verileri taşımak ve dönüştürmek için gerektiği gibi dağıtabilirsiniz. Integration Runtime, Azure genel ağında veya özel bir ağda (Şirket içi, Azure sanal ağı veya Amazon Web Services sanal özel bulut [VPC]) çalıştırılabilir. 

Daha fazla bilgi için bkz. [Azure Data Factory'de tümleştirme çalışma zamanı](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Tümleştirme çalışma zamanları sayısı sınırı nedir?
Bir veri fabrikasında sahip olabilirsiniz tümleştirme çalışma zamanı örnekleri sayısında sabit sınır yoktur. Ancak, tümleştirme çalışma zamanının SSIS paketi yürütmesi için abonelik başına kullanabileceği VM çekirdeklerinin sayısı sınırlıdır. Daha fazla bilgi için bkz. [Data Factory sınırları](../azure-subscription-service-limits.md#data-factory-limits).

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Azure Data Factory en üst düzey kavramları nelerdir?
Azure aboneliğinin bir veya birden çok Azure Data Factory örneği (veya veri fabrikası) olabilir. Azure Data Factory, veri taşıma ve dönüştürme adımları ile veri odaklı iş akışları oluşturabileceğiniz bir platform olarak birlikte çalışan dört ana bileşeni içerir.

### <a name="pipelines"></a>İşlem hatları
Bir veri fabrikasında bir veya daha fazla işlem hattı olabilir. İşlem hattı, bir dizi iş gerçekleştirmeye yönelik mantıksal bir etkinlik gruplandırmasıdır. İşlem hattındaki etkinlikler birlikte bir görev gerçekleştirir. Örneğin, bir işlem hattı Azure blobundan verileri alan ve ardından HDInsight kümesinde verileri bölümlemek için bir Hive sorgusu çalıştıran bir etkinlik grubu içerebilir. Bu avantajda, etkinlikleri her bir etkinliği ayrı ayrı yönetmek yerine bir küme olarak yönetmek için bir işlem hattı kullanabilirsiniz. Etkinlikleri sırayla çalıştırmak için bir işlem hattındaki etkinlikleri birlikte zincirleyebilir veya bunları paralel olarak bağımsız olarak çalıştırabilirsiniz.

### <a name="data-flows"></a>Veri akışları
Veri akışları, arka uç Spark hizmetlerindeki ölçekteki verileri dönüştüren Data Factory görsel olarak oluşturduğunuz nesnelerdir. Programlama veya Spark iç yapıları anlamanız gerekmez. Grafikler (eşleme) veya elektronik tablolar (Wrangling) kullanarak veri dönüştürme hedefini tasarlamanızı yeterlidir.

### <a name="activities"></a>Etkinlikler
Etkinlikler bir işlem hattındaki işleme adımını temsil eder. Örneğin, bir veri deposundan başka bir veri deposuna veri kopyalamak için kopyalama etkinliğini kullanabilirsiniz. Benzer şekilde, verilerinizi dönüştürmek veya analiz etmek için bir Azure HDInsight kümesinde Hive sorgusu çalıştıran bir Hive etkinliği kullanabilirsiniz. Data Factory üç tür etkinliği destekler: veri taşıma etkinlikleri, veri dönüştürme etkinlikleri ve denetim etkinlikleri.

### <a name="datasets"></a>Veri kümeleri
Veri kümeleri, veri depoları içinde etkinliklerinizde giriş veya çıkış olarak kullanmak istediğiniz verilere işaret eden veya başvuruda bulunan veri yapılarını temsil eder. 

### <a name="linked-services"></a>Bağlı hizmetler
Bağlı hizmetler, dış kaynaklara bağlanmak için Data Factory’ye gereken bağlantı bilgilerini tanımlayan bağlantı dizelerine çok benzer. Bu şekilde düşünün: bağlı bir hizmet, veri kaynağıyla bağlantıyı tanımlar ve veri kümesi verilerin yapısını temsil eder. Örneğin, Azure depolama bağlı hizmeti Azure depolama hesabına bağlanacak bağlantı dizesini belirtir. Azure blob veri kümesi, blob kapsayıcısını ve verileri içeren klasörü belirtir.

Bağlı hizmetlerin Data Factory iki amacı vardır:

- Bir şirket içi SQL Server örneği, bir Oracle veritabanı örneği, dosya paylaşma veya bir Azure Blob depolama hesabı içeren, ancak bunlarla sınırlı olmayan bir *veri deposunu* temsil etmek için. Desteklenen veri depolarının bir listesi için, bkz. [kopyalama etkinliği Azure Data Factory](copy-activity-overview.md).
- Etkinlik yürütülmesini barındırabilen *işlem kaynağını* temsil etmek için. Örneğin, HDInsight Hive etkinliği bir HDInsight Hadoop kümesinde çalışır. Dönüştürme etkinlikleri ve desteklenen işlem ortamlarının listesi için bkz. [Azure Data Factory verileri dönüştürme](transform-data.md).

### <a name="triggers"></a>Tetikleyiciler
Tetikleyiciler, bir işlem hattı yürütmesinin ne zaman çıkarılan olduğunu belirten işleme birimlerini temsil eder. Farklı etkinlik türleri için farklı tetikleyici türleri vardır. 

### <a name="pipeline-runs"></a>İşlem hattı çalıştırmaları
İşlem hattı çalıştırması bir işlem hattı yürütme örneğidir. Genellikle bağımsız değişkenleri işlem hattında tanımlanmış parametrelere geçirerek bir işlem hattı çalıştırması örnekleyebilirsiniz. Bağımsız değişkenleri el ile veya tetikleyici tanımı içinde geçirebilirsiniz.

### <a name="parameters"></a>Parametreler
Parametreler, salt okuma yapılandırmasındaki anahtar-değer çiftleridir. Parametreleri bir işlem hattında tanımlarsınız ve bir çalıştırma bağlamından yürütme sırasında tanımlanan parametrelerin bağımsız değişkenlerini geçirirsiniz. Çalıştırma bağlamı bir tetikleyici veya el ile çalıştırdığınız bir işlem hattı tarafından oluşturulur. İşlem hattındaki etkinlikler parametre değerlerini kullanır.

Veri kümesi türü kesin belirlenmiş bir parametredir ve yeniden kullanabileceğiniz ya da başvurmanızı sağlayan bir varlıktır. Bir etkinlik, veri kümelerine başvurabilir ve veri kümesi tanımında tanımlanan özellikleri kullanabilir.

Bağlı hizmet Ayrıca, bir veri deposuna veya bir işlem ortamına bağlantı bilgilerini içeren, kesin olarak belirlenmiş bir parametredir. Ayrıca, yeniden kullanabileceğiniz veya başvuru yaptığınız bir varlıktır.

### <a name="control-flows"></a>Denetim akışları
Denetim akışları bir dizi, dallanma, işlem hattı düzeyinde tanımladığınız parametreler ve işlem hattını talep üzerine ya da bir tetikleyiciden çağırdığınızda geçirdiğiniz bağımsız değişkenler içeren işlem hattı etkinliklerini düzenler. Denetim akışları ayrıca özel durum geçirme ve döngü kapsayıcıları (yani, Foreach yineleyiciler) içerir.


Data Factory kavramları hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

- [Veri kümeleri ve bağlı hizmetler](concepts-datasets-linked-services.md)
- [İşlem hatları ve etkinlikler](concepts-pipelines-activities.md)
- [Tümleştirme çalışma zamanı](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Data Factory için fiyatlandırma modeli nedir?
Azure Data Factory fiyatlandırma ayrıntıları için bkz. [Data Factory fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/data-factory/).

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Data Factory hakkında bilgi edinmek için nasıl güncel kalabilirim?
Azure Data Factory hakkında en güncel bilgiler için aşağıdaki sitelere gidin:

- [Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Belgeler giriş sayfası](/azure/data-factory)
- [Ürün giriş sayfası](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Teknik kapsamlı bakış 

### <a name="how-can-i-schedule-a-pipeline"></a>Bir işlem hattını nasıl zamanlarım? 
Bir işlem hattı zamanlamak için Zamanlayıcı tetikleyicisi veya zaman penceresi tetikleyicisi ' ni kullanabilirsiniz. Tetikleyici, işlem hatlarını düzenli aralıklarla veya takvim tabanlı yinelenen desenlerde (örneğin, 6:00 PM ve Perşembe: 9:00 PM) zaman çizelgesine göre zamanlayabilen bir duvar saati takvim zamanlaması kullanır. Daha fazla bilgi için bkz. [İşlem hattı yürütme ve tetikleyiciler](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>Parametreleri bir işlem hattı çalıştırmasına geçirebilir miyim?
Evet, parametreler Data Factory ' de birinci sınıf, üst düzey bir kavramdır. İşlem hattı düzeyinde parametreler tanımlayabilir ve işlem hattını, isteğe bağlı olarak veya bir tetikleyici kullanarak çalıştırırken bağımsız değişkenleri geçirebilirsiniz.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>İşlem hattı parametreleri için varsayılan değerleri tanımlayabilir miyim? 
Evet. İşlem hatlarında parametreler için varsayılan değerleri tanımlayabilirsiniz. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>İşlem hattındaki bir etkinlik bir işlem hattı çalıştırmasına geçirilen bağımsız değişkenleri kullanıyor musunuz? 
Evet. İşlem hattının içindeki her etkinlik, işlem hattına aktarılan ve `@parameter` yapısıyla çalıştırılan parametre değerini tüketebilir. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Etkinlik çıktısı özelliği başka bir etkinlikte tüketilebilir mi? 
Evet. Etkinlik çıktısı, `@activity` yapısıyla sonraki bir etkinlikte tüketilebilir.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Nasıl yaparım? etkinlik çıkışında null değerler düzgün şekilde işlenemedi mi? 
Null değerleri düzgün bir şekilde işlemek için ifadelerde `@coalesce` yapısını kullanabilirsiniz. 

## <a name="mapping-data-flows"></a>Veri akışlarını eşleme

### <a name="i-need-help-troubleshooting-my-data-flow-logic-what-info-do-i-need-to-provide-to-get-help"></a>Veri akışı mantığımın sorunlarını gidermek için yardıma ihtiyacım var. Yardım almak için hangi bilgileri sağlamam gerekir?

Microsoft, veri akışları ile ilgili yardım veya sorun giderme işlemleri yaparken lütfen veri akışı betiğini sağlayın. Bu, veri akışı grafikinizdeki arka plan kod betiğiyle belirlenir. ADF kullanıcı arabiriminden, veri akışınızı açın ve sağ üst köşedeki "komut dosyası" düğmesine tıklayın. Bu betiği kopyalayıp yapıştırın veya bir metin dosyasına kaydedin.

### <a name="how-do-i-access-data-by-using-the-other-90-dataset-types-in-data-factory"></a>Data Factory Nasıl yaparım? diğer 90 veri kümesi türlerini kullanarak verilere erişin mi?

Veri akışı eşleme özelliği şu anda Azure SQL veritabanı, Azure SQL veri ambarı, Azure Blob depolama veya Azure Data Lake Storage 2. ile ayrılmış metin dosyaları ve BLOB depolamadan ya da kaynak ve havuz için yerel olarak Data Lake Storage 2. 

Diğer bağlayıcılardan herhangi birinden veri hazırlamak için kopyalama etkinliğini kullanın ve sonra verileri hazırlandıktan sonra dönüştürmek için bir veri akışı etkinliği yürütün. Örneğin, işlem hatlarınız önce BLOB depolama alanına kopyalanacak, sonra veri akışı etkinliği bu verileri dönüştürmek için kaynak içinde bir veri kümesi kullanacaktır.

### <a name="is-the-self-hosted-integration-runtime-available-for-data-flows"></a>Şirket içinde barındırılan tümleştirme çalışma zamanı veri akışları için kullanılabilir mi?

Şirket içinde barındırılan IR, şirket içi veya VM tabanlı veri kaynaklarına ve havuza veri almak veya buradan veri aktarmak için kopyalama etkinliğiyle birlikte kullanabileceğiniz bir ADF işlem hattı yapısıdır. Verileri bir kopyalama ile önce, ardından dönüştürme için veri akışı ve ardından bu dönüştürülmüş verileri şirket içi depoya geri taşımanız gerekiyorsa sonraki bir kopya.

## <a name="wrangling-data-flows"></a>Wrangling veri akışları

### <a name="what-are-the-supported-regions-for-wrangling-data-flow"></a>Denetimi veri akışı için desteklenen bölgeler nelerdir?

Wrangling veri akışı şu anda şu bölgelerde oluşturulan veri fabrikaları 'nda destekleniyor:

* Avustralya Doğu
* Orta Kanada
* Orta Hindistan
* Orta ABD
* Doğu ABD
* Doğu ABD 2
* Japonya Doğu
* Kuzey Avrupa
* Güneydoğu Asya
* Orta Güney ABD
* Birleşik Krallık Güney
* Batı Orta ABD
* Batı Avrupa
* Batı ABD
* Batı ABD 2

### <a name="what-are-the-limitations-and-constraints-with-wrangling-data-flow"></a>Denetimi veri akışı ile ilgili sınırlamalar ve kısıtlamalar nelerdir?

Veri kümesi adları yalnızca alfa sayısal karakterler içerebilir. Aşağıdaki veri depoları desteklenir:

* Hesap anahtarı kimlik doğrulamasını kullanarak Azure Blob depolama 'daki DelimitedText veri kümesi
* Hesap anahtarını veya hizmet sorumlusu kimlik doğrulamasını kullanarak Azure Data Lake Storage Gen2 içindeki DelimitedText veri kümesi
* Hizmet sorumlusu kimlik doğrulamasını kullanarak Azure Data Lake Storage Gen1 içindeki DelimitedText veri kümesi
* SQL kimlik doğrulaması kullanarak Azure SQL veritabanı ve veri ambarı. Aşağıdaki desteklenen SQL türlerini inceleyin. Veri ambarı için PolyBase veya hazırlama desteği yoktur.

Şu anda, bağlı Service Key Vault tümleştirme, denetimi veri akışlarında desteklenmez.

### <a name="what-is-the-difference-between-mapping-and-wrangling-data-flows"></a>Eşleme ve denetimi veri akışları arasındaki fark nedir?

Veri akışlarını eşleme, herhangi bir kodlama gerekmeden verileri ölçekteki dönüştürmek için bir yol sağlar. Bir dizi dönüştürme oluşturarak veri akışı tuvalinde bir veri dönüştürme işi tasarlayabilirsiniz. Herhangi bir sayıdaki kaynak dönüşümle başlayın ve ardından veri dönüştürme adımları. Sonuçlarınızı bir hedefe eklemek için veri akışınızı bir havuz ile doldurun. Veri akışı eşleme, veri ve kaynaklardaki bilinen ve bilinmeyen şemalar ile verileri eşlemede ve dönüştürmenin harika bir yoludur.

Wrangling veri akışları, Spark yürütmesi aracılığıyla ölçekte Power Query çevrimiçi karma düzenleyici 'yi kullanarak çevik veri hazırlama ve araştırma yapmanıza olanak sağlar. Verilerin yükseliğiyle birlikte, bazı durumlarda yalnızca bir veri kümesini keşfetmeniz veya Gölü bir veri kümesi oluşturmanız gerekir. Bilinen bir hedefle eşlememeniz yok. Wrangling veri akışları, daha az resmi ve model tabanlı analiz senaryoları için kullanılır.

### <a name="what-is-the-difference-between-power-platform-dataflows-and-wrangling-data-flows"></a>Power platform veri akışları ile denetimi veri akışları arasındaki fark nedir?

Power platform veri akışları, kullanıcıların çok çeşitli veri kaynaklarından veri içeri aktarıp Common Data Service ve Azure Data Lake PowerApps uygulamaları, Power BI raporları veya akış öngörülerini oluşturmasına olanak tanır. Power platform veri akışları, Power BI ve Excel 'e benzer şekilde, kurulu Power Query veri hazırlama deneyimlerini kullanır. Power platform veri akışları bir kuruluş içinde kolayca yeniden kullanım sağlar ve düzenlemeyi otomatik olarak işler (örn. önceki biri yenilendiğinde başka bir veri akışına bağlı olan veri akışlarını otomatik olarak yenileme).

Azure Data Factory (ADF), veri mühendislerinin ve vatandaşlık veri tümleştirmesinin karmaşık karma Ayıkla-Dönüştür-yükle (ETL) ve Ayıkla-yükle-Dönüştür (ELT) iş akışları oluşturmalarına olanak tanıyan bir yönetilen veri tümleştirme hizmetidir. ADF 'de wrangling veri akışı, kullanıcılara bulutta veri hazırlanmasını kolaylaştıran ve altyapı yönetimi gerektirmeyen herhangi bir veri boyutuna ölçeklendirebilen, kod içermeyen, sunucusuz bir ortama sahip olan kullanıcıları güçler. Verileri hazırlamak ve şekillendirmek için Power Query veri hazırlama teknolojisini (ayrıca Power platform veri akışları, Excel, Power BI) kullanır. Büyük veri tümleştirmesinin tüm karmaşıklıklarını ve ölçeklendirme güçlüklerine yönelik olarak tasarlanan, denetimi veri akışları, kullanıcıların Spark yürütme yoluyla verileri hızla kolayca hazırlamasına olanak sağlar. Kullanıcılar tarayıcı tabanlı arabirimimiz ile erişilebilir bir görsel ortamda dayanıklı veri işlem hatları oluşturabilir ve ADF 'nin Spark yürütmesinin karmaşıklıklarını işlemesini sağlar. İşlem hatlarınız için zamanlamalar oluşturun ve veri akışı yürütmelerini ADF izleme portalından izleyin. Veri kullanılabilirliği SLA 'larını ADF 'nin zengin kullanılabilirlik izleme ve uyarılarla kolayca yönetin ve akışlarınızı yönetilen bir ortamda kaydetmek ve yönetmek için yerleşik sürekli tümleştirme ve dağıtım özelliklerinden yararlanın. Veri akışlarınızı ayarlamanıza kadar mantığınızın planlandığını doğrulamak için uyarılar oluşturun ve yürütme planlarını görüntüleyin.

### <a name="supported-sql-types"></a>Desteklenen SQL türleri

Wrangling veri akışı, SQL 'de aşağıdaki veri türlerini destekler. Desteklenmeyen bir veri türü kullanmaya yönelik bir doğrulama hatası alacaksınız.

* Kısadır
* double
* gerçek
* float
* Char
* nchar
* varchar
* nvarchar
* integer
* int
* sürümleri
* boole
* smallint
* tinyint
* bigint
* Kalacağını
* metin
* date
* datetime
* datetime2
* girişin
* timestamp
* benzersiz tanımlayıcı
* xml

Diğer veri türleri gelecekte desteklenecektir.

## <a name="next-steps"></a>Sonraki adımlar
Veri Fabrikası oluşturmaya yönelik adım adım yönergeler için aşağıdaki öğreticilere bakın:

- [Hızlı başlangıç: Veri Fabrikası oluşturma](quickstart-create-data-factory-dot-net.md)
- [Öğretici: bulutta veri kopyalama](tutorial-copy-data-dot-net.md)
