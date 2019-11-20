---
title: Azure Data Factory verileri Azure 'a kopyalama Veri Gezgini
description: Bu makalede, Azure Data Factory kopyalama aracını kullanarak Azure Veri Gezgini 'a veri alma (yükleme) hakkında bilgi edineceksiniz.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 860b1a579d9c8cee6c6e80ae4c4e7fdd7949d5c7
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300596"
---
# <a name="copy-data-to-azure-data-explorer-by-using-azure-data-factory"></a>Azure Data Factory kullanarak Azure Veri Gezgini veri kopyalama 

Azure Veri Gezgini, hızlı, tam olarak yönetilen bir veri analizi hizmetidir. Uygulamalar, Web siteleri ve IoT cihazları gibi birçok kaynaktan akış yapan büyük birimlerde gerçek zamanlı analizler sunar. Azure Veri Gezgini ile, verileri tekrarlayarak, ürünleri geliştirmek, müşteri deneyimlerini geliştirmek, cihazları izlemek ve işlemleri artırmak için desenleri ve anormallikleri belirleyebilirsiniz. Yeni soruları keşfetmenize ve dakikalar içinde yanıt almanıza yardımcı olur. 

Azure Data Factory, tam olarak yönetilen, bulut tabanlı bir veri tümleştirme hizmetidir. Bunu, Azure Veri Gezgini veritabanınızı mevcut sisteminizdeki verilerle doldurmak için kullanabilirsiniz. Analiz çözümlerini oluştururken zamandan tasarruf etmenize yardımcı olabilir.

Azure Veri Gezgini 'e veri yüklediğinizde, Data Factory aşağıdaki avantajları sağlar:

* **Kolay kurulum**: Komut dosyası gerekmeden sezgisel, beş adımlı bir sihirbaz alın.
* **Zengin veri deposu desteği**: Zengin bir şirket içi ve bulut tabanlı veri depoları kümesi için yerleşik destek alın. Ayrıntılı bir liste için [desteklenen veri depoları](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)tablosuna bakın.
* **Güvenli ve uyumlu**: Veriler HTTPS veya Azure ExpressRoute üzerinden aktarılır. Küresel hizmet varlığı, verilerinizin hiçbir şekilde coğrafi sınır bırakmamasını sağlar.
* **Yüksek performans**: Veri yükleme hızı, saniye başına 1 gigabayt (GBps) ve Azure Veri Gezgini. Daha fazla bilgi için bkz. [etkinlik performansını kopyalama](/azure/data-factory/copy-activity-performance).

Bu makalede, Amazon Simple Storage Service (S3) ' den Azure Veri Gezgini 'a veri yüklemek için Data Factory Veri Kopyalama aracını kullanırsınız. Diğer veri depolarından veri kopyalamak için benzer bir işlemi izleyebilirsiniz, örneğin:
* [Azure Blob Depolama](/azure/data-factory/connector-azure-blob-storage)
* [Azure SQL Veritabanı](/azure/data-factory/connector-azure-sql-database)
* [Azure SQL Veri Ambarı](/azure/data-factory/connector-azure-sql-data-warehouse)
* [Google BigQuery](/azure/data-factory/connector-google-bigquery)
* [Oracle](/azure/data-factory/connector-oracle)
* [Dosya sistemi](/azure/data-factory/connector-file-system)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/) oluşturun.
* [Azure Veri Gezgini kümesi ve veritabanı](create-cluster-database-portal.md).
* Veri kaynağı.

## <a name="create-a-data-factory"></a>Data factory oluştur

1. [Azure Portal](https://ms.portal.azure.com) oturum açın.

1. Sol bölmede, **kaynak** > **Analizi** > **Data Factory**oluştur ' u seçin.

   ![Azure portal veri fabrikası oluşturma](media/data-factory-load-data/create-adf.png)

1. **Yeni Veri Fabrikası** bölmesinde, aşağıdaki tabloda yer alan alanlar için değerler girin:

   !["Yeni veri fabrikası" bölmesi](media/data-factory-load-data/my-new-data-factory.png)  

   | Ayar  | Girilecek değer  |
   |---|---|
   | **Name** | Kutusunda, Data Factory 'niz için genel olarak benzersiz bir ad girin. Bir hata alırsanız, *Data Factory adı \"\" loadadxdemo kullanılamıyor*, Veri Fabrikası için farklı bir ad girin. Data Factory yapıtları adlandırma kuralları için bkz. [Data Factory adlandırma kuralları](/azure/data-factory/naming-rules).|
   | **Abonelik** | Açılan listede, veri fabrikasının oluşturulacağı Azure aboneliğini seçin. |
   | **Kaynak Grubu** | **Yeni oluştur**' u seçin ve ardından yeni bir kaynak grubunun adını girin. Zaten bir kaynak grubunuz varsa **var olanı kullan**' ı seçin. |
   | **Sürüm** | Açılan listede **v2**' yi seçin. |  
   | **Location** | Açılan listede, veri fabrikasının konumunu seçin. Listede yalnızca desteklenen konumlar görüntülenir. Data Factory tarafından kullanılan veri depoları diğer konumlarda veya bölgelerde bulunabilir. |

1. **Oluştur**’u seçin.

1. Oluşturma işlemini izlemek için araç çubuğunda **Bildirimler** ' i seçin. Data Factory 'yi oluşturduktan sonra seçin.
   
   **Data Factory** bölmesi açılır.

   ![Data Factory bölmesi](media/data-factory-load-data/data-factory-home-page.png)

1. Uygulamayı ayrı bir bölmede açmak için **yazar & İzleyici** kutucuğunu seçin.

## <a name="load-data-into-azure-data-explorer"></a>Verileri Azure 'a yükleme Veri Gezgini

Birçok [veri deposu](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) türünden Azure Veri Gezgini veri yükleyebilirsiniz. Bu makalede, Amazon S3 ' den nasıl veri yükleneceğini ele alınmaktadır.

Verilerinizi aşağıdaki yollarla yükleyebilirsiniz:

* Azure Data Factory Kullanıcı arabiriminde, sol bölmede, [Azure Data Factory Kullanıcı arabirimini kullanarak veri fabrikası oluşturma](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)konusunun "Veri Fabrikası oluşturma" bölümünde gösterildiği gibi **Yazar** simgesini seçin.
* Azure Data Factory Veri Kopyalama aracında, [verileri kopyalamak için veri kopyalama aracını kullanma](/azure/data-factory/quickstart-create-data-factory-copy-data-tool)bölümünde gösterildiği gibi.

### <a name="copy-data-from-amazon-s3-source"></a>Amazon S3 (kaynak) konumundan veri kopyalama

1. **Başlarken** bölmesinde **veri kopyalama**' i seçerek veri kopyalama aracını açın.

   ![Veri Kopyalama araç düğmesi](media/data-factory-load-data/copy-data-tool-tile.png)

1. **Özellikler** bölmesinde, **görev adı** kutusuna bir ad girin ve ardından **İleri**' yi seçin.

    ![Veri Kopyalama Özellikler bölmesi](media/data-factory-load-data/copy-from-source.png)

1. **Kaynak veri deposu** bölmesinde **Yeni bağlantı oluştur**' u seçin.

    ![Veri Kopyalama "kaynak veri deposu" bölmesi](media/data-factory-load-data/source-create-connection.png)

1. **Amazon S3**' i seçin ve ardından **devam**' ı seçin.

    ![Yeni bağlı hizmet bölmesi](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. **Yeni bağlı hizmet (Amazon S3)** bölmesinde şunları yapın:

    ![Amazon S3 bağlı hizmetini belirtin](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    a. **Ad** kutusuna yeni bağlı hizmetinizin adını girin.

    b. **Tümleştirme çalışma zamanı Ile Bağlan** aşağı açılan listesinden değeri seçin.

    c. **Erişim anahtarı kimliği** kutusuna değeri girin.
    
    > [!NOTE]
    > Amazon S3 ' de, erişim anahtarınızı bulmak için gezinti çubuğundaki Amazon Kullanıcı adınızı seçin ve **güvenlik kimlik bilgilerimi**seçin.
    
    d. **Gizli erişim anahtarı** kutusuna bir değer girin.

    e. Oluşturduğunuz bağlı hizmet bağlantısını test etmek için **Bağlantıyı Sına**' yı seçin.

    f. **Son**’u seçin.
    
      **Kaynak veri deposu** bölmesinde yeni AmazonS31 bağlantınız görüntülenir. 

1. **İleri**’yi seçin.

   ![Kaynak veri deposunun bağlantısı oluşturuldu](media/data-factory-load-data/source-data-store-created-connection.png)

1. **Girdi dosyasını veya klasörünü seçin** bölmesinde şunları yapın:

    a. Kopyalamak istediğiniz dosya veya klasöre gidin ve ardından seçin.

    b. İstediğiniz kopyalama davranışını seçin. **İkili kopya** onay kutusunun temizlenmiş olduğundan emin olun.

    c. **İleri**’yi seçin.

    ![Girdi dosyasını veya klasörünü seçin](media/data-factory-load-data/source-choose-input-file.png)

1. **Dosya biçimi ayarları** bölmesinde, dosyanız için ilgili ayarları seçin. ardından **İleri**' yi seçin.

   !["Dosya biçimi ayarları" bölmesi](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Verileri Azure Veri Gezgini 'a (hedef) kopyalama

Yeni Azure Veri Gezgini bağlı hizmeti, verileri bu bölümde belirtilen Azure Veri Gezgini hedef tablosuna (havuz) kopyalamak için oluşturulur.

#### <a name="create-the-azure-data-explorer-linked-service"></a>Azure Veri Gezgini bağlı hizmetini oluşturma

Azure Veri Gezgini bağlı hizmetini oluşturmak için aşağıdakileri yapın;

1. Mevcut bir veri deposu bağlantısını kullanmak veya yeni bir veri deposu belirtmek için, **hedef veri deposu** bölmesinde **Yeni bağlantı oluştur**' u seçin.

    ![Hedef veri deposu bölmesi](media/data-factory-load-data/destination-create-connection.png)

1. **Yeni bağlı hizmet** bölmesinde **Azure Veri Gezgini**' yi seçin ve ardından **devam**' ı seçin.

    ![Yeni bağlı hizmet bölmesi](media/data-factory-load-data/adx-select-new-linked-service.png)

1. **Yeni bağlı hizmet (Azure Veri Gezgini)** bölmesinde şunları yapın:

    ![Azure Veri Gezgini yeni bağlı hizmet bölmesi](media/data-factory-load-data/adx-new-linked-service.png)

   a. **Ad** kutusuna Azure Veri Gezgini bağlı hizmeti için bir ad girin.

   b. **Hesap seçim yöntemi**altında aşağıdakilerden birini yapın: 

    * **Azure aboneliğinden** seçim yapın ve ardından aşağı açılan listelerde **Azure aboneliğinizi** ve **kümenizi**seçin. 

        > [!NOTE]
        > **Küme** aşağı açılan denetimi, yalnızca aboneliğinizle ilişkili kümeleri listeler.

    * **El Ile gir**' i seçin ve ardından **uç**noktanızı girin.

   c. **Kiracı** kutusuna kiracı adını girin.

   d. **Hizmet sorumlusu kimliği** kutusuna HIZMET sorumlusu kimliğini girin.

   e. **Hizmet sorumlusu anahtarı** ' nı seçin ve ardından **hizmet sorumlusu anahtarı** kutusuna anahtar için değer girin.

   f. **Veritabanı** açılan listesinde, veritabanı adınızı seçin. Alternatif olarak, **Düzenle** onay kutusunu seçin ve ardından veritabanı adını girin.

   g. Oluşturduğunuz bağlı hizmet bağlantısını test etmek için **Bağlantıyı Sına**' yı seçin. Bağlı hizmetinize bağlanamıyorsanız, bölmede yeşil onay işareti ve **bağlantı başarılı** iletisi görüntülenir.

   h. Bağlı hizmet oluşturma işleminin tamamlanabilmesi için **son** ' u seçin.

    > [!NOTE]
    > Hizmet sorumlusu, Azure Veri Gezgini hizmetine erişmek için Azure Data Factory tarafından kullanılır. Hizmet sorumlusu oluşturmak için [Azure Active Directory (Azure AD) hizmet sorumlusu oluşturma](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal)bölümüne gidin. Azure Key Vault yöntemini kullanmayın.

#### <a name="configure-the-azure-data-explorer-data-connection"></a>Azure Veri Gezgini veri bağlantısını yapılandırma

Bağlı hizmet bağlantısını oluşturduktan sonra, **hedef veri deposu** bölmesi açılır ve oluşturduğunuz bağlantı kullanılabilir. Bağlantıyı yapılandırmak için aşağıdakileri yapın;

1. **İleri**’yi seçin.

    ![Azure Veri Gezgini "hedef veri deposu" bölmesi](media/data-factory-load-data/destination-data-store.png)

1. **Tablo eşleme** bölmesinde, hedef tablo adını ayarlayın ve ardından **İleri**' yi seçin.

    ![Hedef veri kümesi "Tablo eşleme" bölmesi](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. **Sütun eşleme** bölmesinde aşağıdaki eşlemeler gerçekleşir:

    a. İlk eşleme, [Azure Data Factory şema eşlemesine](/azure/data-factory/copy-activity-schema-and-type-mapping)göre Azure Data Factory gerçekleştirilir. Şunları yapın:

    * Azure Data Factory hedef tablosu için **sütun eşlemelerini** ayarlayın. Varsayılan eşleme kaynaktan Azure Data Factory hedef tablosuna görüntülenir.

    * Sütun eşlemenizi tanımlamak için ihtiyacınız olmayan sütunların seçimini iptal edin.

    b. İkinci eşleme, bu tablo verileri Azure Veri Gezgini 'da gösterildiğinde oluşur. Eşleme, [CSV eşleme kurallarına](/azure/kusto/management/mappings#csv-mapping)göre gerçekleştirilir. Kaynak veriler CSV biçiminde olmasa bile, Azure Data Factory verileri tablosal biçime dönüştürür. Bu nedenle, CSV eşlemesi bu aşamada yalnızca ilgili eşleme olur. Şunları yapın:

    * Seçim **Azure Veri Gezgini (kusto) havuz özellikleri**altında, sütun eşlemesinin kullanılabilmesi için ilgili alım **eşleme adını** ekleyin.

    * Alma **eşleme adı** belirtilmemişse, **sütun eşlemeleri** bölümünde tanımlanan *ada göre* eşleme sırası kullanılacaktır. *Ad-adı* eşleme başarısız olursa Azure Veri Gezgini, verileri *sütun sırasına göre* almaya çalışır (yani, varsayılan olarak konuma göre eşlenir).

    * **İleri**’yi seçin.

    ![Hedef veri kümesi "sütun eşleme" bölmesi](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. **Ayarlar** bölmesinde şunları yapın:

    a. **Hataya dayanıklılık ayarları**altında ilgili ayarları girin.

    b. **Performans ayarları**altında, **hazırlama** uygulanmaz ve **Gelişmiş ayarlar** maliyet konularını içerir. Belirli gereksinimleriniz yoksa, bu ayarları olduğu gibi bırakın.

    c. **İleri**’yi seçin.

    ![Verileri Kopyala "Ayarlar" bölmesi](media/data-factory-load-data/copy-data-settings.png)

1. **Özet** bölmesinde, ayarları gözden geçirin ve ardından **İleri**' yi seçin.

    ![Verileri Kopyala "Özet" bölmesi](media/data-factory-load-data/copy-data-summary.png)

1. **Dağıtım Tamam** bölmesinde şunları yapın:

    a. **İzleyici** sekmesine geçiş yapmak ve işlem hattının (yani, ilerleme, hatalar ve veri akışı) durumunu görüntülemek için **izleyici**' yi seçin.

    b. Bağlı hizmetleri, veri kümelerini ve işlem hatlarını düzenlemek için **ardışık düzeni Düzenle**' yi seçin.

    c. Veri kopyalama görevini gerçekleştirmek için **son** ' u seçin.

    !["Dağıtım Tamam" bölmesi](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>Sonraki adımlar

* Azure Data Factory [Azure Veri Gezgini Bağlayıcısı](/azure/data-factory/connector-azure-data-explorer) hakkında bilgi edinin.
* [Data Factory Kullanıcı arabirimindeki](/azure/data-factory/quickstart-create-data-factory-portal)bağlı hizmetleri, veri kümelerini ve işlem hatlarını düzenlemeyle ilgili daha fazla bilgi edinin.
* Veri sorgulama için [Azure Veri Gezgini sorguları](/azure/data-explorer/web-query-data) hakkında bilgi edinin.
