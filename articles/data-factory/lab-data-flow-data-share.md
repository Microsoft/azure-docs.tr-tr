---
title: Azure Data Factory ve Azure veri paylaşma kullanarak veri tümleştirme
description: Azure Data Factory ve Azure veri paylaşma kullanarak verileri kopyalama, dönüştürme ve paylaşma
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 04/16/2021
ms.openlocfilehash: 392b1a1650ab40951704d003f2a5e5337cf3c0f5
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566717"
---
# <a name="data-integration-using-azure-data-factory-and-azure-data-share"></a>Azure Data Factory ve Azure veri paylaşma kullanarak veri tümleştirme

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Müşteriler modern veri ambarı ve analiz projelerini önemli ölçüde daha fazla veri olmasa da, veri temelinde verileri üzerinde daha fazla görünürlük gerektirir. Bu atölye, Azure Data Factory ve Azure veri paylaşımındaki geliştirmelerin Azure 'da veri tümleştirmeyi ve yönetimini nasıl basitleştireceğinizi ele alır. 

Veri üzerinde kapsamlı bir görünüm oluşturmak için Code-Free ETL/ELT etkinleştirildikten sonra, Azure Data Factory iyileştirmeler veri mühendislerinizi daha fazla veri almak ve bu nedenle kuruluşunuza daha fazla değer getirmek için güçlendirilecek. Azure veri paylaşımı, iş paylaşımını yönetilen bir şekilde yapmanıza olanak sağlar.

Bu atölyde Azure Data Factory (ADF) kullanarak Azure SQL veritabanındaki verileri (ADLS 2.) Azure Data Lake Storage 2.. Verileri Gölü 'a ilettikten sonra veri akışlarını, Data Factory 'nin yerel dönüşüm hizmetini eşleyerek ve bunu Azure SYNAPSE Analytics 'e havuza alarak dönüştürebilirsiniz. Daha sonra, Azure veri paylaşımının kullanıldığı bazı ek verilerle birlikte dönüştürülmüş verilerle tabloyu paylaşabilirsiniz. 

Bu laboratuvarda kullanılan veriler New York City TAXI verileri. SQL veritabanı 'nda veritabanınıza içeri aktarmak için, [TAXI-Data bacpac dosyasını](https://github.com/djpmsft/ADF_Labs/blob/master/sample-data/taxi-data.bacpac)indirin.

## <a name="prerequisites"></a>Önkoşullar

* **Azure aboneliği**: Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

* **Azure SQL veritabanı**: SQL DB yoksa, [bir SQL DB hesabı oluşturmayı](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal) öğrenin

* **Azure Data Lake Storage 2. depolama hesabı**: ADLS 2. depolama hesabınız yoksa [ADLS 2. depolama hesabı oluşturmayı](../storage/common/storage-account-create.md)öğrenin.

* **Azure SYNAPSE Analytics**: Azure SYNAPSE analizinizde yoksa [Azure SYNAPSE Analytics örneği oluşturmayı](../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md)öğrenin.

* **Azure Data Factory**: bir veri fabrikası oluşturmadıysanız, bkz. [Veri Fabrikası oluşturma](./quickstart-create-data-factory-portal.md).

* **Azure veri paylaşma**: bir veri paylaşma oluşturmadıysanız, bkz. [veri paylaşma oluşturma](../data-share/share-your-data.md#create-a-data-share-account).

## <a name="set-up-your-azure-data-factory-environment"></a>Azure Data Factory ortamınızı ayarlama

Bu bölümde, Azure portal Azure Data Factory Kullanıcı deneyimine (ADF UX) nasıl erişebileceğinizi öğreneceksiniz. ADF UX 'e bir kez, kullanmakta olduğumuz her bir veri deposu için üç bağlı hizmeti yapılandıracaksınız: Azure SQL DB, ADLS 2. ve Azure SYNAPSE Analytics.

Azure Data Factory bağlı hizmetler, dış kaynaklara yönelik bağlantı bilgilerini tanımlar. Azure Data Factory Şu anda 85 bağlayıcılarından desteklemektedir.

### <a name="open-the-azure-data-factory-ux"></a>Azure Data Factory UX 'i açın

1. [Azure Portal](https://portal.azure.com) Microsoft Edge veya Google Chrome içinde açın.
1. Sayfanın üst kısmındaki arama çubuğunu kullanarak ' veri fabrikaları ' araması yapın

    ![Portal 1](media/lab-data-flow-data-share/portal1.png)
1. Veri Fabrikası kaynağına tıklayarak kaynak dikey penceresini açın.

    ![Portal 2](media/lab-data-flow-data-share/portal2.png)
1. ADF UX 'i açmak için **Yazar ve izleyici** ' ye tıklayın. ADF UX 'e adf.azure.com adresinden de erişilebilir.

    ![3. Portal](media/lab-data-flow-data-share/portal3.png)
1. ADF UX giriş sayfasına yönlendirilirsiniz. Bu sayfada, Veri Fabrikası kavramlarını öğrenmek için hızlı başlayan, eğitici videolar ve öğreticilerin bağlantıları bulunur. Yazmaya başlamak için sol taraftaki çubukta kurşun kalem simgesine tıklayın.

    ![Portal Yapılandırması](media/lab-data-flow-data-share/configure1.png)

### <a name="create-an-azure-sql-database-linked-service"></a>Azure SQL Veritabanı bağlı hizmeti oluşturma

1. Bağlı bir hizmet oluşturmak için, sol taraftaki çubukta hub 'ı **Yönet** ' i seçin, **Bağlantılar** bölmesinde **bağlı hizmetler** ' i seçin ve **ardından yeni ' yi seçerek yeni** bir bağlı hizmet ekleyin.

    ![Portal yapılandırma 2](media/lab-data-flow-data-share/configure2.png)
1. Yapılandıracağınız ilk bağlantılı hizmet bir Azure SQL DB 'dir. Veri deposu listesini filtrelemek için arama çubuğunu kullanabilirsiniz. **Azure SQL veritabanı** kutucuğuna tıklayın ve devam ' a tıklayın.

    ![Portal yapılandırma 4](media/lab-data-flow-data-share/configure-4.png)
1. SQL DB Yapılandırma bölmesinde, bağlı hizmet adınız olarak ' SQLDB ' girin. Data Factory 'nin veritabanınıza bağlanmasına izin vermek için kimlik bilgilerinizi girin. SQL kimlik doğrulaması kullanıyorsanız sunucu adı, veritabanı, Kullanıcı adı ve parolanızı girin. Bağlantıyı **Sına**' ya tıklayarak bağlantı bilgilerinizin doğru olduğunu doğrulayabilirsiniz. İşiniz bittiğinde **Oluştur** ' a tıklayın.

    ![Portal yapılandırma 5](media/lab-data-flow-data-share/configure5.png)

### <a name="create-an-azure-synapse-analytics-linked-service"></a>Azure SYNAPSE Analytics bağlı hizmeti oluşturma

1. Azure SYNAPSE Analytics bağlantılı hizmetini eklemek için aynı işlemi tekrarlayın. Bağlantılar sekmesinde **Yeni**' ye tıklayın. **Azure SYNAPSE Analytics** kutucuğunu seçin ve devam ' a tıklayın.

    ![Portal yapılandırma 6](media/lab-data-flow-data-share/configure-6.png)
1. Bağlı hizmet yapılandırması bölmesinde, bağlı hizmet adınız olarak ' SQLDW ' girin. Data Factory 'nin veritabanınıza bağlanmasına izin vermek için kimlik bilgilerinizi girin. SQL kimlik doğrulaması kullanıyorsanız sunucu adı, veritabanı, Kullanıcı adı ve parolanızı girin. Bağlantıyı **Sına**' ya tıklayarak bağlantı bilgilerinizin doğru olduğunu doğrulayabilirsiniz. İşiniz bittiğinde **Oluştur** ' a tıklayın.

    ![Portal yapılandırma 7](media/lab-data-flow-data-share/configure-7.png)

### <a name="create-an-azure-data-lake-storage-gen2-linked-service"></a>Azure Data Lake Storage 2. bağlı hizmeti oluşturma

1. Bu laboratuvar için gereken son bağlantılı hizmet bir Azure Data Lake Storage Gen2.  Bağlantılar sekmesinde **Yeni**' ye tıklayın. **Azure Data Lake Storage 2.** kutucuğunu seçin ve devam ' a tıklayın.

    ![Portal yapılandırma 8](media/lab-data-flow-data-share/configure8.png)
1. Bağlı hizmet yapılandırması bölmesinde, bağlı hizmet adı olarak ' ADLSGen2 ' girin. Hesap anahtarı kimlik doğrulaması kullanıyorsanız, **depolama hesabı adı** açılır listesinden ADLS 2. depolama hesabınızı seçin. Bağlantıyı **Sına**' ya tıklayarak bağlantı bilgilerinizin doğru olduğunu doğrulayabilirsiniz. İşiniz bittiğinde **Oluştur** ' a tıklayın.

    ![Portal yapılandırma 9](media/lab-data-flow-data-share/configure9.png)

### <a name="turn-on-data-flow-debug-mode"></a>Veri akışı hata ayıklama modunu aç

Veri *akışı eşleme kullanarak verileri dönüştürme* bölümünde, eşleme veri akışları oluşturacağız. Eşleme veri akışlarını oluşturmadan önce en iyi yöntem, etkin bir Spark kümesinde dönüştürme mantığını Saniyeler içinde test etmenizi sağlayan hata ayıklama modunu kullanmaktır.

Hata ayıklamayı açmak **için veri akışı aktivitenleriniz** olduğunda veri akışı tuvalinin veya işlem hattı tuvalinin üst çubuğundaki **veri akışı hata ayıklama** kaydırıcısını tıklatın. Onay iletişim kutusu açılır pencereleri sırasında Tamam ' ı tıklatın. Kümenin başlaması yaklaşık 5-7 dakika sürer. *Azure SQL DB 'den, başlatılırken kopyalama etkinliğini kullanarak ADLS 2. verileri* almak için devam edin.

![Portal 10 yapılandırma](media/lab-data-flow-data-share/configure10.png)

![Veri akışı hata ayıklama kaydırıcısının nerede olduğunu gösteren ekran görüntüsü.](media/lab-data-flow-data-share/configure-11.png)

## <a name="ingest-data-using-the-copy-activity"></a>Kopyalama etkinliğini kullanarak verileri alma

Bu bölümde, bir Azure SQL DB 'den bir tabloyu ADLS 2. depolama hesabına veren bir kopyalama etkinliğine sahip bir işlem hattı oluşturacaksınız. Bir işlem hattı eklemeyi, bir veri kümesini yapılandırmayı ve bir işlem hattından ADF UX aracılığıyla hata ayıklamanızı öğreneceksiniz. Bu bölümde kullanılan yapılandırma stili, ilişkisel bir veri deposundan dosya tabanlı veri deposuna kopyalamak için uygulanabilir.

Azure Data Factory işlem hattı, birlikte bir görevi gerçekleştiren etkinliklerin mantıksal gruplandırmasıdır. Bir etkinlik, verilerinizde gerçekleştirilecek bir işlem tanımlar. Veri kümesi, bağlantılı hizmette kullanmak istediğiniz verileri işaret eder.

### <a name="create-a-pipeline-with-a-copy-activity"></a>Kopyalama etkinliği ile işlem hattı oluşturma

1. Fabrika kaynakları bölmesinde, artı simgesine tıklayarak yeni kaynak menüsünü açın. İşlem **hattı**' nı seçin.

    ![Portal kopyası 1](media/lab-data-flow-data-share/copy1.png)
1. İşlem hattı tuvalinin **genel** sekmesinde, işlem hattınızı ' ıngestandtransformtaxıdata ' gibi açıklayıcı bir şekilde adlandırın.

    ![Portal kopyası 2](media/lab-data-flow-data-share/copy2.png)
1. İşlem hattı tuvalinin Etkinlikler bölmesinde, **Taşı ve Dönüştür** Accordion ' i açın ve **veri kopyalama** etkinliğini tuvale sürükleyin. Kopyalama etkinliğine ' IngestIntoADLS ' gibi açıklayıcı bir ad verin.

    ![Portal kopyası 3](media/lab-data-flow-data-share/copy3.png)

### <a name="configure-azure-sql-db-source-dataset"></a>Azure SQL DB kaynak veri kümesini yapılandırma

1. Kopyalama etkinliğinin **kaynak** sekmesine tıklayın. Yeni bir veri kümesi oluşturmak için **Yeni**' ye tıklayın. Kaynağınız ' dbo ' tablosu olacak. ' SQLDB ' bağlı hizmetinde bulunan Üçlü verileri daha önce yapılandırıldı.

    ![Portal kopyası 4](media/lab-data-flow-data-share/copy4.png)
1. **Azure SQL veritabanı** 'nı arayın ve devam ' a tıklayın.

    ![Portal kopyası 5](media/lab-data-flow-data-share/copy-5.png)
1. ' Üçlü Veri kümenizi ' çağırın. Bağlı hizmetiniz olarak ' SQLDB 'yi seçin. ' Dbo ' tablo adını seçin. Tablo adı açılır listesinden gezinme verileri. Şemayı **bağlantı/depodan** içeri aktarın. Bittiğinde Tamam ' a tıklayın.

    ![Portal kopyası 6](media/lab-data-flow-data-share/copy6.png)

Kaynak veri kümenizi başarıyla oluşturdunuz. Kaynak ayarlarında, varsayılan değer **tablosunun** sorgu kullan alanında seçili olduğundan emin olun.

### <a name="configure-adls-gen2-sink-dataset"></a>ADLS 2. havuz veri kümesini yapılandırma

1. Kopyalama etkinliğinin **Havuz** sekmesine tıklayın. Yeni bir veri kümesi oluşturmak için **Yeni**' ye tıklayın.

    ![Portal kopyası 7](media/lab-data-flow-data-share/copy7.png)
1. **Azure Data Lake Storage 2.** arayın ve devam ' a tıklayın.

    ![Portal kopyası 8](media/lab-data-flow-data-share/copy8.png)
1. Biçim seç bölmesinde, bir CSV dosyasına yazarken **Delimitedtext** ' i seçin. Devam ' a tıklayın.

    ![Portal kopyalama 9](media/lab-data-flow-data-share/copy9.png)
1. Havuz veri kümenizi ' Üçlü CSV ' olarak adlandırın. Bağlı hizmetiniz olarak ' ADLSGen2 ' seçeneğini belirleyin. CSV dosyanızı yazmak istediğiniz konumu girin. Örneğin, verilerinizi kapsayıcıda dosyaya yazabilirsiniz `trip-data.csv` `staging-container` . Çıktı verilerinizin üst bilgi olmasını istiyorsanız **ilk satırı üst bilgi olarak** ayarlayın. Hedefte henüz dosya olmadığından, **Içeri aktarma şemasını** **none** olarak ayarlayın. Bittiğinde Tamam ' a tıklayın.

    ![Portal kopyası 10](media/lab-data-flow-data-share/copy10.png)

### <a name="test-the-copy-activity-with-a-pipeline-debug-run"></a>Kopyalama etkinliğini bir işlem hattı hata ayıklama çalıştırması ile test etme

1. Kopyalama etkinliğinizi doğru şekilde çalıştığını doğrulamak için, bir hata ayıklama çalıştırması yürütmek üzere işlem hattı tuvalinin en üstündeki **Hata Ayıkla** ' ya tıklayın. Bir hata ayıklama çalıştırması, Veri Fabrikası hizmetine yayımlamadan önce işlem hattınızı uçtan uca veya bir kesme noktasına kadar test etmenize olanak sağlar.

    ![Portal kopyası 11](media/lab-data-flow-data-share/copy11.png)
1. Hata ayıklama çalıştırmanızı izlemek için işlem hattı tuvalinin **Çıkış** sekmesine gidin. İzleme ekranı, her 20 saniyede bir yenilenir veya Yenile düğmesine el ile tıkladığınızda yenilenir. Kopyalama etkinliği, **Eylemler** sütunundaki göz gözlük simgesine tıklanarak erişebilen özel bir izleme görünümüne sahiptir.

    ![Portal kopyası 12](media/lab-data-flow-data-share/copy12.png)
1. Kopyalama izleme görünümü etkinliğin yürütme ayrıntılarını ve performans özelliklerini sağlar. Veri okuma/yazma, satır okuma/yazma, dosya okuma/yazma ve aktarım hızı gibi bilgileri görebilirsiniz. Her şeyi doğru şekilde yapılandırdıysanız, ADLS havuzinizdeki bir dosyada 49.999 satır yazıldığını görmeniz gerekir.

    ![Portal kopyası 13](media/lab-data-flow-data-share/copy13.png)
1. Bir sonraki bölüme geçmeden önce, fabrika üst çubuğunda **Tümünü Yayımla** ' ya tıklayarak değişikliklerinizi Data Factory hizmetinde yayımlamanız önerilir. Bu laboratuvarda kapsanmayan Azure Data Factory tam git tümleştirmesini destekler. Git tümleştirmesi sürüm denetimi, depoya yinelemeli kaydetme ve bir veri fabrikasında işbirliği yapılmasına izin verir. Daha fazla bilgi için bkz. [Azure Data Factory kaynak denetimi](./source-control.md#troubleshooting-git-integration).

    ![Portal yayımlama 1](media/lab-data-flow-data-share/publish1.png)

## <a name="transform-data-using-mapping-data-flow"></a>Veri akışlarını eşlemeyi kullanarak verileri dönüştürme

Verileri başarıyla Azure Data Lake Storage olarak kopyaladığınıza göre, bu verileri bir veri ambarına birleştirme ve toplama işlemi zaman alabilir. Azure Data Factory, verileri görsel olarak tasarlanan dönüştürme hizmeti olan eşleme veri akışını kullanacağız. Veri akışlarını eşleme, kullanıcıların dönüştürme mantığı kodu ücretsiz geliştirmesine olanak tanır ve bunları ADF hizmeti tarafından yönetilen Spark kümelerinde yürütür.

Bu adımda oluşturulan veri akışı, önceki bölümde oluşturulan ' "Üçlü CSV" veri kümesini ' dbo ' tablosu ile birleştirir. Üçlü dört sütuna göre ' SQLDB ' içinde depolanan Üçlü Fares. Ardından veriler, `payment_type` belirli alanların ortalamasını hesaplamak ve bir Azure SYNAPSE Analytics tablosuna yazılmak üzere sütun temelinde toplanır.

### <a name="add-a-data-flow-activity-to-your-pipeline"></a>İşlem hattınızı bir veri akışı etkinliği ekleyin

1. İşlem hattı tuvalinin Etkinlikler bölmesinde **taşıma ve dönüştürme** Accordion ' ı açın ve **veri akışı** etkinliğini tuvale sürükleyin.

    ![Portal veri akışı 1](media/lab-data-flow-data-share/dataflow1.png)
1. Açılan yan bölmede **Yeni veri akışı oluştur** ' u seçin ve **veri akışını eşleme**' yi seçin. **Tamam**'a tıklayın.

    ![Portal veri akışı 2](media/lab-data-flow-data-share/dataflow2.png)
1. Dönüştürme mantığınızı oluşturacağınız veri akışı tuvaline yönlendirilirsiniz. Genel sekmesinde, veri akışınızı ' Joinandadggregatedata ' olarak adlandırın.

    ![Portal veri akışı 3](media/lab-data-flow-data-share/dataflow3.png)

### <a name="configure-your-trip-data-csv-source"></a>Seyahat verileri CSV kaynağınızı yapılandırma

1. Yapmak istediğiniz ilk şey, iki kaynak dönüştürmelerinizi yapılandırmaktır. İlk kaynak "Üçlü CSV" Demitedtext veri kümesini işaret eder. Bir kaynak dönüştürmesi eklemek için tuvaldeki **Kaynak Ekle** kutusuna tıklayın.

    ![Portal veri akışı 4](media/lab-data-flow-data-share/dataflow4.png)
1. Kaynağınızı ' Üçlü CSV ' olarak adlandırın ve kaynak açılan listesinden ' Üçlü Datacsv ' veri kümesini seçin. Hatırlamanız durumunda bu veri kümesini oluştururken başlangıçta bir şemayı içeri aktarmadınız. `trip-data.csv`Şimdi mevcut olduğundan, veri kümesi ayarları sekmesine gitmek Için **Düzenle** ' ye tıklayın.

    ![Portal veri akışı 5](media/lab-data-flow-data-share/dataflow5.png)
1. Sekme **şemasına** gidin ve **şemayı içeri aktar**' a tıklayın. Doğrudan dosya deposundan içeri aktarmak için **bağlantı/depolama alanından** seçim yapın. dize türünde 14 sütun görünmelidir.

    ![Portal veri akışı 6](media/lab-data-flow-data-share/dataflow6.png)
1. ' Joinandadggregatedata ' veri akışına geri dönün. Hata ayıklama kümeniz başlatıldıysa (hata ayıklama kaydırıcısının yanında yeşil bir daire ile belirtilir), veri **Önizleme** sekmesindeki verilerin anlık görüntüsünü alabilirsiniz. Veri önizlemesi getirmek için **Yenile** ' ye tıklayın.

    ![Portal veri akışı 7](media/lab-data-flow-data-share/dataflow7.png)

> [!Note]
> Veri önizleme verileri yazmıyor.

### <a name="configure-your-trip-fares-sql-db-source"></a>Seyahat etme Fares SQL DB kaynağınızı yapılandırma

1. Eklemekte olduğunuz ikinci kaynak SQL DB tablosunun ' dbo ' öğesine işaret eder. Üçlü. ' Üçlü Veri CSV ' kaynağınız, başka bir **Kaynak Ekle** kutusu olacaktır. Yeni bir kaynak dönüştürmesi eklemek için tıklayın.

    ![Portal veri akışı 8](media/lab-data-flow-data-share/dataflow8.png)
1. Bu kaynağı ' Üçlü Faressql ' olarak adlandırın. Yeni bir SQL VERITABANı veri kümesi oluşturmak için kaynak veri kümesi alanının yanındaki **Yeni** ' ye tıklayın.

    ![Portal veri akışı 9](media/lab-data-flow-data-share/dataflow9.png)
1. **Azure SQL veritabanı** kutucuğunu seçin ve devam ' a tıklayın. *Not: veri fabrikasındaki bağlayıcıların çoğunun eşleme veri akışında desteklenmediğini fark edebilirsiniz. Bu kaynaklardan birindeki verileri, kopyalama etkinliğini kullanarak desteklenen bir kaynağa dönüştürmek için* kullanın.

    ![Portal veri akışı 10](media/lab-data-flow-data-share/dataflow-10.png)
1. ' Üçlü Veri kümenizi çağırın. Bağlı hizmetiniz olarak ' SQLDB 'yi seçin. ' Dbo ' tablo adını seçin. Üçlü aşağı açılan tablo adı. Şemayı **bağlantı/depodan** içeri aktarın. Bittiğinde Tamam ' a tıklayın.

    ![Portal veri akışı 11](media/lab-data-flow-data-share/dataflow11.png)
1. Verilerinizi doğrulamak için veri **Önizleme** sekmesinde bir veri önizlemesi getirin.

    ![Portal veri akışı 12](media/lab-data-flow-data-share/dataflow12.png)

### <a name="inner-join-tripdatacsv-and-tripfaressql"></a>INNER JOIN, Üçlü CSV ve üçlü Faressql

1. Yeni bir dönüşüm eklemek için, ' Üçlü Veri CSV ' öğesinin sağ alt köşesindeki artı simgesine tıklayın. **Birden çok giriş/çıkış** altında, **Birleştir**' i seçin.

    ![Portal katılımı 1](media/lab-data-flow-data-share/join1.png)
1. ' Innerjoinwithüçlü Fares ' JOIN dönüşümünüzü adlandırın. Sağ Akış açılan menüsünde ' Üçlü Faressql ' seçeneğini belirleyin. Birleşim türü olarak **iç** öğesini seçin. Eşleme veri akışı 'nda farklı JOIN türleri hakkında daha fazla bilgi edinmek için bkz. [JOIN Types](./data-flow-join.md#join-types).

    Her akıştan eşleştirmek istediğiniz sütunları, **JOIN koşulu** açılan kutusu aracılığıyla seçin. Ek bir JOIN koşulu eklemek için, var olan bir koşulun yanındaki artı simgesine tıklayın. Varsayılan olarak, tüm JOIN koşulları bir ve işleciyle birleştirilir ve bu, bir eşleşme için tüm koşulların karşılanması gerektiği anlamına gelir. Bu laboratuvarda,,, ve sütunları ile eşleştirmek `medallion` istiyoruz `hack_license` `vendor_id``pickup_datetime`

    ![Portal katılımı 2](media/lab-data-flow-data-share/join2.png)
1. Bir veri önizlemesiyle 25 sütunlarınızın başarıyla birleştirildiğini doğrulayın.

    ![Portal katılımı 3](media/lab-data-flow-data-share/join3.png)

### <a name="aggregate-by-payment_type"></a>Payment_type göre topla

1. Birleştirme dönüşümünüzü tamamladıktan sonra, ' ınnerjoinwithüçlü Fares ' nin yanındaki artı simgesine tıklayarak bir toplama dönüştürmesi ekleyin. **Şema değiştiricisi** altında **Toplam** ' ı seçin.

    ![Portal AGG 1](media/lab-data-flow-data-share/agg1.png)
1. ' AggregateByPaymentType ' toplama dönüştürmeinizi adlandırın. `payment_type`Gruplandırma ölçütü sütunu olarak seçin.

    ![Portal AGG 2](media/lab-data-flow-data-share/agg2.png)
1. **Toplamlar** sekmesine gidin. Burada iki toplama belirtirsiniz:
    * Ödeme türüne göre gruplanan ortalama tarifeli havayolu
    * Ödeme türüne göre gruplandırılan toplam seyahat uzaklığı

    İlk olarak, ortalama tarifeli havayolu ifadesini oluşturacaksınız. **Sütun Ekle veya Seç** etiketli metin kutusunda ' average_fare ' girin.

    ![Portal AGG 3](media/lab-data-flow-data-share/agg3.png)
1. Toplama ifadesi girmek için, **ENTER ifadesi** etiketli mavi kutuya tıklayın. Bu işlem, giriş şeması, yerleşik işlevler ve işlemler ve Kullanıcı tanımlı parametreleri kullanarak görsel olarak veri akışı ifadeleri oluşturmak için kullanılan bir araç olan veri akışı ifade Oluşturucusu 'nu açar. İfade oluşturucusunun özellikleri hakkında daha fazla bilgi için bkz. [Expression Builder belgeleri](./concepts-data-flow-expression-builder.md).

    Ortalama tarifeli havayolu almak için `avg()` toplama işlevini kullanarak `total_amount` sütun için bir tamsayıya dönüştürme toplamını toplayın `toInteger()` . Veri akışı ifade dilinde, bu olarak tanımlanmıştır `avg(toInteger(total_amount))` . İşiniz bittiğinde Kaydet ' e tıklayın **ve son** ' a tıklayın.

    ![Portal AGG 4](media/lab-data-flow-data-share/agg4.png)
1. Ek toplama ifadesi eklemek için yanındaki artı simgesine tıklayın `average_fare` . **Sütun Ekle**' yi seçin.

    ![Portal AGG 5](media/lab-data-flow-data-share/agg5.png)
1. **Sütun Ekle veya Seç** etiketli metin kutusunda ' total_trip_distance ' girin. Son adımda olduğu gibi, ifadeye girmek için ifade oluşturucuyu açın.

    Toplam seyahat mesafesini almak için `sum()` toplama işlevini kullanarak `trip_distance` sütun için bir tamsayıya dönüştürme toplamını toplayın `toInteger()` . Veri akışı ifade dilinde, bu olarak tanımlanmıştır `sum(toInteger(trip_distance))` . İşiniz bittiğinde Kaydet ' e tıklayın **ve son** ' a tıklayın.

    ![Portal AGG 6](media/lab-data-flow-data-share/agg6.png)
1. **Veri önizleme** sekmesinde dönüştürme mantığınızı test edin. Gördüğünüz gibi, daha önce çok daha az sayıda satır ve sütun vardır. Yalnızca bu dönüşümde tanımlanan üç grup ve toplama sütunu, aşağı yönde devam eder. Örnekte yalnızca beş ödeme türü grubu olduğundan, yalnızca beş satır silinir.

    ![Portal AGG 7](media/lab-data-flow-data-share/agg7.png)

### <a name="configure-you-azure-synapse-analytics-sink"></a>Azure SYNAPSE Analytics havuzunuzu yapılandırma

1. Artık dönüştürme mantığımızı tamamladığımıza göre, verileri bir Azure SYNAPSE Analytics tablosunda havuza hazırlıyoruz. **Hedef** bölümün altına bir havuz dönüştürmesi ekleyin.

    ![Portal havuzu 1](media/lab-data-flow-data-share/sink1.png)
1. ' SQLDWSink ' havuzunuzu adlandırın. Yeni bir Azure SYNAPSE Analytics veri kümesi oluşturmak için havuz veri kümesi alanının yanındaki **Yeni** ' ye tıklayın.

    ![Portal Havuzu 2](media/lab-data-flow-data-share/sink2.png)

1. **Azure SYNAPSE Analytics** kutucuğunu seçin ve devam ' a tıklayın.

    ![Portal havuzu 3](media/lab-data-flow-data-share/sink-3.png)
1. ' Aggregary Taxıdata ' veri kümenizi çağırın. Bağlı hizmetiniz olarak ' SQLDW ' seçeneğini belirleyin. **Yeni tablo oluştur** ' u seçin ve yeni tabloyu dbo olarak adlandırın. AggregateTaxiData. Bittiğinde Tamam 'a tıklayın

    ![Portal havuzu 4](media/lab-data-flow-data-share/sink4.png)
1. Havuzun **Ayarlar** sekmesine gidin. Yeni bir tablo oluşturduğumuz için tablo eylemi altında **tabloyu yeniden oluştur** ' u seçmemiz gerekiyor. **Hazırlama** seçimini kaldırın; Bu işlem, satır satır veya toplu iş içinde mi yerleştiriyoruz? seçeneğini değiştirir.

    ![Portal havuzu 5](media/lab-data-flow-data-share/sink5.png)

Veri akışınızı başarıyla oluşturdunuz. Şimdi bunu bir işlem hattı etkinliğinde çalıştırmaya zaman atalım.

### <a name="debug-your-pipeline-end-to-end"></a>İşlem hattınızda uçtan uca hata ayıklama

1. **Ingestandtransformdata** işlem hattının sekmesine geri dönün. ' IngestIntoADLS ' kopyalama etkinliğindeki yeşil kutuya dikkat edin. ' Joinandadggregatedata ' veri akışı etkinliğine sürükleyin. Bu, veri akışı etkinliğinin yalnızca kopyalama başarılı olursa çalışmasına neden olan bir ' on Success ' oluşturur.

    ![Portal işlem hattı 1](media/lab-data-flow-data-share/pipeline1.png)
1. Kopyalama etkinliği için yaptığımız gibi, hata ayıklama çalıştırmasını yürütmek için **Hata Ayıkla** ' ya tıklayın. Hata ayıklama çalıştırmaları için, veri akışı etkinliği yeni bir küme yerine etkin hata ayıklama kümesini kullanır. Bu işlem hattının yürütülmesi bir dakikadan fazla sürer.

    ![Portal işlem hattı 2](media/lab-data-flow-data-share/pipeline2.png)
1. Kopyalama etkinliği gibi, veri akışı, etkinliğin tamamlanmasında göz gözlük simgesi tarafından erişilen özel bir izleme görünümüne sahiptir.

    ![Portal işlem hattı 3](media/lab-data-flow-data-share/pipeline3.png)
1. İzleme görünümünde, her yürütme aşamasında yürütme süreleri ve satırlarıyla birlikte basitleştirilmiş bir veri akışı grafiğine bakabilirsiniz. Doğru şekilde yapıldıysa, bu etkinlikte beş satıra toplam 49.999 satırı uygulamanız gerekir.

    ![Portal işlem hattı 4](media/lab-data-flow-data-share/pipeline4.png)
1. Bölümlendirme bilgileri ve yeni/güncelleştirilmiş/bırakılan sütunlar gibi yürütme hakkında daha fazla ayrıntı almak için bir dönüşüme tıklayabilirsiniz.

    ![Portal işlem hattı 5](media/lab-data-flow-data-share/pipeline5.png)

Artık bu laboratuvarın Data Factory bölümünü tamamladınız. Bunları tetikleyicilerle birlikte kullanmak istiyorsanız kaynaklarınızı yayımlayın. Azure SQL veritabanı 'ndan veri alan bir işlem hattını kopyalama etkinliğini kullanarak Azure Data Lake Storage ve ardından bu verileri bir Azure SYNAPSE Analytics 'e toplanmış şekilde başarıyla çalıştırdınız. Verilerin kendine SQL Server bakarak başarıyla yazıldığını doğrulayabilirsiniz.

## <a name="share-data-using-azure-data-share"></a>Azure Veri Paylaşımı ile veri paylaşma

Bu bölümde, Azure portal kullanarak yeni bir veri paylaşımının nasıl ayarlanacağını öğreneceksiniz. Bu, Azure Data Lake Store Gen2 ve Azure SYNAPSE Analytics 'te veri kümeleri içerecek yeni bir veri paylaşımının oluşturulmasını kapsar. Böylece, veri tüketicilerine kendileriyle paylaşılan verileri otomatik olarak yenileme seçeneği veren bir anlık görüntü zamanlaması yapılandıracaksınız. Ardından, alıcıları veri paylaşımınıza davet edeceksiniz. 

Bir veri paylaşımının oluşturulduktan sonra, HATS 'yi değiştireceksiniz ve *veri tüketicisi* olacak. Veri TÜKETİCİSİNDE, verilerin alınmasını ve veri kümelerini farklı depolama konumlarına eşlemenizi yapılandırmak için bir veri paylaşma davetini kabul etme akışında adım adım ilerleyebileceksiniz. Ardından, sizinle paylaşılan verileri belirtilen hedefle kopyalayacak bir anlık görüntü tetikleyeceksiniz. 

### <a name="sharing-data-data-provider-flow"></a>Verileri paylaşma (Veri Sağlayıcısı akışı)

1. Azure portal Microsoft Edge veya Google Chrome içinde açın.

1. Sayfanın üst kısmındaki arama çubuğunu kullanarak **veri paylaşımları** arayın

    ![Portal reklamları](media/lab-data-flow-data-share/portal-ads.png)

1. Adda ' Provider ' olan veri paylaşma hesabını seçin. Örneğin, **DataProvider0102**. 

1. **Verilerinizi paylaşmayı Başlat** ' ı seçin

    ![Paylaşmayı Başlat](media/lab-data-flow-data-share/ads-start-sharing.png)

1. Yeni veri paylaşımınızı yapılandırmaya başlamak için **+ Oluştur** ' u seçin. 

1. **Paylaşma adı** altında, tercih ettiğiniz bir ad belirtin. Bu, veri tüketicinizin görebilmesi için, bu adı TaxiData gibi açıklayıcı bir ad eklediğinizden emin olmanızı sağlar.

1. **Açıklama**' nın altında, veri paylaşımının içeriğini açıklayan bir cümle koyun. Veri paylaşımında, Azure SYNAPSE Analytics ve Azure Data Lake Store dahil olmak üzere çeşitli depolarda depolanan dünya genelindeki TAXI seyahat verileri yer alacak. 

1. **Kullanım koşulları** altında, veri tüketicinizin bağlı olmasını istediğiniz bir terim kümesi belirtin. Bazı örneklerde "Bu verileri kuruluşunuz dışında dağıtma" veya "yasal sözleşmeye başvurma" sayılabilir. 

    ![Ayrıntıları paylaşma](media/lab-data-flow-data-share/ads-details.png)

1. **Devam**’ı seçin. 

1. **Veri kümesi Ekle** 'yi seçin 

    ![Veri kümesi 1 ekle](media/lab-data-flow-data-share/add-dataset.png)

1. ADF dönüştürmelerinizin bulunduğu Azure SYNAPSE Analytics 'ten bir tablo seçmek için **Azure SYNAPSE Analytics** ' i seçin.

    ![Veri kümesi SQL ekleme](media/lab-data-flow-data-share/add-dataset-sql.png)


1. Devam edebilmeniz için önce çalıştırılacak bir komut dosyası verilecek. Belirtilen betik, Azure veri paylaşımının MSI adına kimlik doğrulaması yapmasına izin vermek için SQL veritabanında bir kullanıcı oluşturur. 

> [!IMPORTANT]
> Betiği çalıştırmadan önce, SQL Server için kendinizi Active Directory yönetici olarak ayarlamanız gerekir. 

1. Yeni bir sekme açın ve Azure portal gidin. Veri paylaşmak istediğiniz veritabanında bir kullanıcı oluşturmak için belirtilen betiği kopyalayın. Bunu, AAD kimlik doğrulamasını kullanarak, sorgu Gezgini 'ni (Önizleme) kullanarak EDW veritabanında oturum açarak yapın. 

    Kullanıcının oluşturulan kullanıcı ayraçları parantez içinde bulundurabilmesi için betiği değiştirmeniz gerekir. Örn
    
    Dış oturum açma işleminden [DataProvider-xxxx] kullanıcısını oluşturun;  exec sp_addrolemember db_owner, [DataProvider-xxxx];
    
1. Veri paylaşımınıza veri kümeleri eklediğiniz yerde Azure veri paylaşımında geri dönün. 

1. **EDW**' ı seçin ve ardından tablo Için **Aggregısedata** öğesini seçin. 

1. **Veri kümesi Ekle** 'yi seçin

    Artık veri kümizin kapsamında olan bir SQL tablosu sunuyoruz. Daha sonra, Azure Data Lake Store ek veri kümeleri ekleyeceğiz. 

1. **Veri kümesi Ekle** ' yi seçin ve **Azure Data Lake Store Gen2** seçin

    ![ADLS veri kümesi Ekle](media/lab-data-flow-data-share/add-dataset-adls.png)

1. **İleri**’yi seçin

1. *Wwtaxidata* öğesini genişletin. *Boston vergi verileri*' ni genişletin. Dosya düzeyinde paylaşılacağını fark edebilirsiniz. 

1. Tüm klasörü veri paylaşımınıza eklemek için *Boston. Data* klasörünü seçin. 

1. **Veri kümesi Ekle** 'yi seçin

1. Eklenmiş veri kümelerini gözden geçirin. Veri paylaşımınıza eklenmiş bir SQL tablonuz ve bir ADLS 2. klasörünüz olmalıdır. 

1. **Devam**'ı seçin

1. Bu ekranda, veri paylaşımınıza alıcı ekleyebilirsiniz. Eklediğiniz alıcılara veri paylaşımınıza davetiye gönderilir. Bu laboratuvarın amacı için 2 e-posta adresi eklemeniz gerekir:

    1. Kullandığınız Azure aboneliğinin e-posta adresi. 

        ![Alıcı ekleme](media/lab-data-flow-data-share/add-recipients.png)

    1. Adlı kurgusal veri tüketicisine ekleyin *janedoe@fabrikam.com* .

1. Bu ekranda, veri tüketicinizin anlık görüntü ayarını yapılandırabilirsiniz. Bu, sizin tarafınızdan tanımlanan bir aralıkta verilerinizin düzenli güncelleştirmelerini almasına izin verir. 

1. *Yinelenme* açılan listesini kullanarak **anlık görüntü zamanlamasını** denetleyin ve verilerinizin saatlik olarak yenilenmesini yapılandırın.  

1. **Oluştur**’u seçin.

    Artık etkin bir veri paylaşımınız var. Veri paylaşma oluştururken veri sağlayıcısı olarak neleri görebileceğinize ilişkin incelemeye izin verir. 

1. Daha önce oluşturduğunuz ve **DataProvider** başlıklı veri payını seçin. **Veri paylaşımında** **gönderilen paylaşımlar** ' i seçerek bu sayfaya gidebilirsiniz. 

1. Anlık görüntü zamanlaması ' na tıklayın. Seçeneğini belirlerseniz anlık görüntü zamanlamasını devre dışı bırakabilirsiniz. 

1. Ardından, **veri kümeleri** sekmesini seçin. Oluşturulduktan sonra, bu veri kümelerine ek veri kümeleri ekleyebilirsiniz. 

1. **Abonelikleri paylaşma** sekmesini seçin. Veri Tüketiciniz henüz davetinizi kabul etmediğinden, hiç paylaşma aboneliği yok.

1. **Davetler** sekmesine gidin. Burada, bekleyen davetler listesini görürsünüz. 

    ![Bekleyen davetler](media/lab-data-flow-data-share/pending-invites.png)

1. Davetini seçin *janedoe@fabrikam.com* . Sil’i seçin. Alıcının daveti henüz kabul etmediyse, artık bunu yapamayacaktır. 

1. **Geçmiş** sekmesini seçin. Veri Tüketiciniz henüz davetinizi kabul etmediğinden ve bir anlık görüntü tetiklemediğinden hiçbir şey yapılmadı. 

### <a name="receiving-data-data-consumer-flow"></a>Veri alma (veri tüketici akışı)

Veri paylaşımımızı incelediğimiz için, veri tüketicimizi ve bu aşamada bağlam ve aşmaya geçiş yapmaya hazır olduğumuz. 

Artık Microsoft Azure adresinden gelen kutunuzda bir Azure veri paylaşma davetine sahip olmanız gerekir. Outlook Web Erişimi (outlook.com) başlatın ve Azure aboneliğiniz için sağlanan kimlik bilgilerini kullanarak oturum açın.

Aldığınız e-postada, "davet > görüntüle" düğmesine tıklayın. Bu noktada, veri paylaşımıyla veri sağlayıcıları davetini kabul ederken veri tüketicisi deneyiminin benzetimini yapacağız. 

![E-posta davetiyesi](media/lab-data-flow-data-share/email-invite.png)

Bir abonelik seçmeniz istenebilir. Bu laboratuvar için çalıştığınız aboneliği seçtiğinizden emin olun. 

1. *DataProvider* adlı davete tıklayın. 

1. Bu davet ekranında, daha önce veri sağlayıcısı olarak yapılandırdığınız veri paylaşımıyla ilgili çeşitli ayrıntılar görürsünüz. Ayrıntıları gözden geçirin ve sağlanmışsa kullanım koşullarını kabul edin.

1. Laboratuvarınız için zaten mevcut olan aboneliği ve kaynak grubunu seçin. 

1. **Veri paylaşma hesabı** Için **dataconsumer**' ı seçin. Ayrıca, yeni bir veri paylaşma hesabı da oluşturabilirsiniz. 

1. **Alınan paylaşma adı**' nın yanında, varsayılan paylaşımın adının veri sağlayıcısı tarafından belirtilen ad olduğunu fark edeceksiniz. Paylaşıma, almak üzere olduğunuz verileri açıklayan kolay bir ad verin, örneğin, **Taxıdatashare**.

    ![Davet kabul eder](media/lab-data-flow-data-share/consumer-accept.png)

1. **Şimdi kabul edip yapılandırmayı** ve **daha sonra kabul edip yapılandırmayı** seçebilirsiniz. Şimdi kabul edip yapılandırmayı seçerseniz, tüm verilerin kopyalanması gereken bir depolama hesabı belirtirsiniz. Daha sonra kabul edip yapılandırmayı seçerseniz paylaşımdaki veri kümeleri eşlenmemiş olur ve bunları el ile eşlemeniz gerekir. Daha sonra bunu kabul edeceğiz. 

1. **Kabul et ve daha sonra Yapılandır ' ı** seçin. 

    Bu seçeneği yapılandırırken, bir paylaşılan abonelik oluşturulur, ancak hiçbir hedef eşlenmediğinden verilerin her yerde olması gerekir. 

    Daha sonra veri paylaşımında veri kümesi eşlemelerini yapılandıracağız. 

1. Alınan paylaşma (5. adımda belirttiğiniz ad) seçeneğini belirleyin.

    **Tetikleyici anlık görüntüsü** gri, ancak paylaşma etkin. 

1. **Veri kümeleri** sekmesini seçin. Her bir veri kümesinin eşlenmemiş olduğunu, yani Verilerin kopyalanacağı bir hedefi olmadığını unutmayın. 

    ![eşlenmemiş veri kümeleri](media/lab-data-flow-data-share/unmapped.png)

1. Azure SYNAPSE Analytics tablosunu seçin ve **+ hedefe eşle**' yi seçin.

1. Ekranın sağ tarafında, **hedef veri türü** açılan listesini seçin. 

    SQL verilerini çok çeşitli veri depoları ile eşleyebilirsiniz. Bu durumda, bir Azure SQL veritabanı ile eşleşeceğiz.

    ![eşleme](media/lab-data-flow-data-share/mapping-options.png)
    
    Seçim Hedef veri türü olarak **Azure Data Lake Store Gen2** öğesini seçin. 
    
    Seçim Üzerinde çalıştığınız aboneliği, kaynak grubunu ve depolama hesabını seçin. 
    
    Seçim Verileri Data Lake 'e CSV veya Parquet biçiminde almayı tercih edebilirsiniz. 

1. **Hedef veri türü**' nün YANıNDAKI Azure SQL veritabanı ' nı seçin. 

1. Üzerinde çalıştığınız aboneliği, kaynak grubunu ve depolama hesabını seçin. 

    ![SQL 'e eşle](media/lab-data-flow-data-share/map-to-sqldb.png)

1. Devam edebilmeniz için, belirtilen betiği çalıştırarak SQL Server yeni bir kullanıcı oluşturmanız gerekir. İlk olarak, belirtilen betiği panonuza kopyalayın. 

1. Yeni bir Azure portal sekmesi açın. Bir süre içinde geri dönebilmeniz için mevcut sekmelerinizi kapatmayın. 

1. Açtığınız yeni sekmede **SQL veritabanları**' na gidin.

1. SQL veritabanını seçin (yalnızca aboneliğinizde bir tane olmalıdır). Veri ambarını seçmemeye dikkat edin. 

1. **Sorgu Düzenleyicisi seçin (Önizleme)**

1. Sorgu Düzenleyicisi 'nde oturum açmak için AAD kimlik doğrulamasını kullanın. 

1. Veri paylaşımınızda belirtilen sorguyu çalıştırın (14. adımdaki Pano 'ya kopyalanır). 

    Bu komut, Azure veri paylaşma hizmeti 'nin, verileri içine kopyalayabilmesi için SQL Server kimlik doğrulaması yapmak üzere Azure hizmetleri için Yönetilen kimlikler kullanmasına izin verir. 

1. Özgün sekmesine dönüp **hedefe eşle**' yi seçin.

1. Sonra, veri kümesinin parçası olan Azure Data Lake Gen2 klasörünü seçin ve bunu bir Azure Blob depolama hesabıyla eşleyin. 

    ![depolama](media/lab-data-flow-data-share/storage-map.png)

    Tüm veri kümeleri eşleştirildiği için artık veri sağlayıcısından veri almaya başlamaya hazırsınız. 

    ![eşlendiği](media/lab-data-flow-data-share/all-mapped.png)
    
1. **Ayrıntılar**' ı seçin. 

    Veri paylaşımının içine kopyalanacak hedefleri olduğundan, **tetikleyici anlık görüntüsünün** artık gri olmadığına dikkat edin.

1. Tetikleyici anlık görüntüsünü seçin > tam kopya. 

    ![Tetikleyicinin](media/lab-data-flow-data-share/trigger-full.png)

    Bu, verileri yeni veri paylaşma hesabınıza kopyalamaya başlar. Gerçek bir Dünya senaryosunda bu veriler üçüncü bir taraftan geliyor olabilir. 

    Verilerin üzerinde gelmesi yaklaşık 3-5 dakika sürer. **Geçmiş** sekmesine tıklayarak ilerlemeyi izleyebilirsiniz. 

    Beklerken, özgün veri paylaşımında (DataProvider) gidin ve abonelikleri ve **geçmişi** **paylaşma** sekmesinin durumunu görüntüleyin. Artık etkin bir abonelik olduğunu ve veri sağlayıcısı olarak, veri tüketicisinin kendileriyle paylaşılan verileri almaya ne zaman başlatıldığını da izleyebileceğini görebilirsiniz. 

1. Veri tüketicisinin veri paylaşımında geri gidin. Tetikleyicinin durumu başarılı olduktan sonra, verilerin ilgili depolarda bulunduğunu görmek için hedef SQL veritabanı ve Veri Gölü sayfasına gidin. 

Tebrikler, Laboratuvarı tamamladınız!