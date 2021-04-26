---
title: Azure Data Lake Storage 1. verileri Gen2 'e Kopyala
description: Azure Data Lake Storage 1. verileri Gen2 'e kopyalamak için Azure Data Factory kullanma
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 4a4bc0b55928fcaa40cd1cb03ca0522e611df476
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727728"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>Azure Data Lake Storage 1. verileri Azure Data Factory ile Gen2 'a kopyalama

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Lake Storage 2., [Azure Blob depolamada](../storage/blobs/storage-blobs-introduction.md)yerleşik olan büyük veri analizlerine adanmış bir dizi özellik kümesidir. Bu hizmet sayesinde hem dosya sistemi hem de nesne depolama alanı yaklaşımlarını kullanarak verilerinize arabirim oluşturabilirsiniz.

Şu anda Azure Data Lake Storage 1. kullanıyorsanız, Azure Data Factory kullanarak Data Lake Storage 1. verileri Gen2 'e kopyalayarak Azure Data Lake Storage 2. değerlendirebilirsiniz.

Azure Data Factory, tam olarak yönetilen bulut tabanlı bir veri tümleştirme hizmetidir. Bu hizmeti kullanarak Gölü, bir dizi şirket içi ve bulut tabanlı veri mağazalarından verilerle doldurabilir ve analiz çözümlerinizi oluştururken zamandan tasarruf edebilirsiniz. Desteklenen bağlayıcıların listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats)tablosuna bakın.

Azure Data Factory, genişleme, yönetilen bir veri taşıma çözümü sağlar. Data Factory genişleme mimarisi nedeniyle, verileri yüksek bir aktarım hızına alabilir. Daha fazla bilgi için bkz. [etkinlik performansını kopyalama](copy-activity-performance.md).

Bu makalede veri kopyalama aracının Azure Data Lake Storage 1. verileri Azure Data Lake Storage 2. 'e kopyalamak için Data Factory nasıl kullanılacağı gösterilmektedir. Diğer veri deposu türlerinden veri kopyalamak için benzer adımları izleyebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
* Hesabı içindeki verileri Azure Data Lake Storage 1..
* Data Lake Storage 2. etkin Azure depolama hesabı. Depolama hesabınız yoksa, [bir hesap oluşturun](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

1. Sol taraftaki menüden veri ve analiz **kaynak oluştur**' u seçin  >    >  **Data Factory**.
   
   ![Yeni bölmede Data Factory seçimi](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. **Yeni Veri Fabrikası** sayfasında, aşağıdaki görüntüde gösterilen alanlar için değerler girin: 
      
   ![Yeni Veri Fabrikası sayfası](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **Ad**: Azure Data Factory 'niz için genel olarak benzersiz bir ad girin. "Data Factory Name \" loadadlsdemo \" kullanılamıyor" hatasını alırsanız Veri Fabrikası için farklı bir ad girin. Örneğin, _**adınız**_**ADFTutorialDataFactory** adını kullanın. Data Factory 'yi tekrar oluşturun. Data Factory yapıtlarını adlandırma kuralları için bkz. [Data Factory adlandırma kuralları](naming-rules.md).
    * **Abonelik**: veri fabrikasının oluşturulacağı Azure aboneliğinizi seçin. 
    * **Kaynak grubu**: açılan listeden var olan bir kaynak grubunu seçin. Ayrıca **Yeni oluştur** seçeneğini belirleyip bir kaynak grubunun adını girebilirsiniz. Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure kaynaklarınızı yönetmek için kaynak gruplarını kullanma](../azure-resource-manager/management/overview.md). 
    * **Sürüm**: **V2**'yi seçin.
    * **Konum**: veri fabrikasının konumunu seçin. Açılan listede yalnızca desteklenen konumlar görüntülenir. Veri fabrikası tarafından kullanılan veri depoları başka konumlarda ve bölgelerde olabilir. 

3. **Oluştur**’u seçin.
4. Oluşturma işlemi tamamlandıktan sonra, veri fabrikanıza gidin. Aşağıdaki görüntüde gösterildiği gibi **Data Factory** giriş sayfasını görürsünüz: 
   
   ![Data factory giriş sayfası](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

5. Veri tümleştirme uygulamasını ayrı bir sekmede başlatmak için **yazar & izleyici** kutucuğunu seçin.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage 2. Nesil'e verileri yükleme

1. **Başlarken** sayfasında, veri kopyalama aracını başlatmak için **veri kopyalama** kutucuğunu seçin. 

   ![Veri kopyalama araç kutucuğu](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. **Özellikler** sayfasında, **görev adı** alanı için **CopyFromADLSGen1ToGen2** belirtin. **İleri**’yi seçin.

    ![Özellikler sayfası](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. **Kaynak veri deposu** sayfasında **+ Yeni bağlantı oluştur**' u seçin.

    ![Kaynak veri deposu sayfası](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
4. Bağlayıcı galerisinden **Azure Data Lake Storage 1. Nesil**'i seçin ve sonra da **Devam**'ı seçin.
    
    ![Kaynak veri deposu Azure Data Lake Storage 1. sayfası](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
5. **Azure Data Lake Storage 1. bağlantı belirle** sayfasında, aşağıdaki adımları izleyin:

   a. Hesap adı için Data Lake Storage 1. seçin ve **kiracıyı** belirtin veya doğrulayın.
  
   b. Ayarları doğrulamak için **Bağlantıyı Sına** ' yı seçin. Ardından **son**' u seçin.
  
   c. Yeni bir bağlantı oluşturulduğunu görürsünüz. **İleri**’yi seçin.
   
   > [!IMPORTANT]
   > Bu kılavuzda, Azure Data Lake Storage 1. kimlik doğrulaması yapmak için Azure kaynakları için yönetilen bir kimlik kullanırsınız. Azure Data Lake Storage 1. yönetilen kimliğe uygun izinler vermek için [Bu yönergeleri](connector-azure-data-lake-store.md#managed-identity)izleyin.
   
   ![Azure Data Lake Storage 1. hesabı belirtin](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
6. **Girdi dosyasını veya klasörünü seçin** sayfasında, üzerine kopyalamak istediğiniz klasöre ve dosyaya gidin. Klasörü veya dosyayı seçin ve Seç ' i **seçin.**

    ![Girdi dosyasını veya klasörünü seçin](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

7. **Dosyaları yinelemeli olarak Kopyala** ve **ikili kopya** seçeneklerini belirleyerek kopyalama davranışını belirtin. **İleri**’yi seçin.

    ![Ekran görüntüsü, dosyayı yinelemeli olarak Kopyala ve Ikili kopya seçeneklerini seçebileceğiniz giriş dosyasını veya klasörü seçin öğesini gösterir.](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
8. **Hedef veri deposu** sayfasında **+ Yeni bağlantı oluştur**  >  **Azure Data Lake Storage 2.**  >  **devam et**' i seçin.

    ![Hedef veri deposu sayfası](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

9. **Azure Data Lake Storage 2. bağlantı belirle** sayfasında, aşağıdaki adımları izleyin:

   a. **Depolama hesabı adı** açılır listesinden Data Lake Storage 2. özellikli hesabınızı seçin.
   
   b. **Son**'u seçerek bağlantıyı oluşturun. Sonra **İleri**’yi seçin.
   
   ![Azure Data Lake Storage 2. hesabı belirtin](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

10. **Çıktı dosyasını veya klasörünü seçin** sayfasında, çıkış klasörü adı olarak **Copyfromadlsgen1** girin ve **İleri**' yi seçin. Data Factory, varsa kopyalama sırasında karşılık gelen Azure Data Lake Storage 2. dosya sistemi ve alt klasörleri oluşturur.

    ![Ekran görüntüsü girdiğiniz klasör yolunu gösterir.](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

11. Varsayılan ayarları kullanmak için **Ayarlar** sayfasında **İleri**'yi seçin.

12. **Özet** sayfasında, ayarları gözden geçirin ve **İleri**' yi seçin.

    ![Özet sayfası](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
13. **Dağıtım sayfasında**, işlem hattını Izlemek için **izleyici** ' yi seçin.

    ![Dağıtım sayfası](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
14. Soldaki **İzleyici** sekmesinin otomatik olarak seçildiğine dikkat edin. **Eylemler** sütunu, etkinlik çalıştırması ayrıntılarını görüntüleme ve işlem hattını yeniden çalıştırma bağlantılarını içerir.

    ![İşlem hattı çalıştırmalarını izleme](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

15. Işlem hattı çalıştırmasıyla ilişkili etkinlik çalıştırmalarını görüntülemek için **Eylemler** sütunundaki **etkinlik çalıştırmalarını görüntüle** bağlantısını seçin. İşlem hattında yalnızca bir etkinlik (kopyalama etkinliği) olduğundan tek bir girdi görürsünüz. İşlem hattı çalıştırmaları görünümüne geri dönmek için üstteki işlem **hatları** bağlantısını seçin. Listeyi yenilemek için **Yenile**’yi seçin. 

    ![Etkinlik çalıştırmalarını izleme](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

16. Her kopyalama etkinliğinin yürütme ayrıntılarını izlemek için, etkinlik izleme görünümündeki **Eylemler** altında **Ayrıntılar** bağlantısını (gözlük resmi) seçin. Kaynaktan havuza, veri işleme, karşılık gelen süre ile yürütme adımlarına ve kullanılan yapılandırmalara göre veri hacmi gibi ayrıntıları izleyebilirsiniz.

    ![Etkinlik çalıştırma ayrıntılarını izle](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

17. Verilerin Azure Data Lake Storage 2. hesabınıza kopyalandığını doğrulayın.

## <a name="best-practices"></a>En iyi uygulamalar

Azure Data Lake Storage 2. Azure Data Lake Storage 1. yükseltmeyi genel olarak değerlendirmek için, bkz. [Azure Data Lake Storage 1. büyük veri analizi çözümlerinizi Azure Data Lake Storage 2. 'e yükseltme](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md). Aşağıdaki bölümlerde, Data Lake Storage 1. Data Lake Storage 2. 'e veri yükseltmesine yönelik Data Factory kullanmak için en iyi uygulamalar tanıtılmaktadır.

### <a name="data-partition-for-historical-data-copy"></a>Geçmiş veri kopyası için veri bölümü

- Data Lake Storage 1. toplam veri boyutu 30 TB 'den küçükse ve dosya sayısı 1.000.000 ' den küçükse, tüm verileri tek bir kopyalama etkinliği çalıştırmasında kopyalayabilirsiniz.
- Kopyalanacak daha büyük bir veriniz varsa veya veri geçişini toplu işlerle yönetme esnekliği ve bunların her birinin belirli bir zaman çerçevesinde tamamlanmasını sağlamak istiyorsanız, verileri bölümleyin. Bölümlendirme, beklenmedik bir sorunun riskini de azaltır.

Uçtan uca çözümü doğrulamak ve ortamınızda kopya verimini test etmek için bir kavram kanıtı kullanın. Başlıca kavram kanıtı adımları: 

1. Tek bir kopyalama etkinliği ile bir Data Factory işlem hattı oluşturarak, bir kopyalama performansı temeli almak için Data Lake Storage 1. birden fazla veri TBs 'yi Data Lake Storage 2. 'e kopyalayın. [Veri tümleştirme birimleri (DIUs)](copy-activity-performance-features.md#data-integration-units) ile 128 olarak başlayın. 
2. 1. adımda aldığınız aktarım hızına bağlı olarak, tüm veri geçişi için gereken tahmini süreyi hesaplayın. 
3. Seçim Geçirilecek dosyaları bölümlemek için bir denetim tablosu oluşturun ve dosya filtresini tanımlayın. Dosyaları bölümetmenin yolu: 

    - Klasör adına veya klasör adına joker karakter filtresiyle bölüm. Bu yöntemi öneririz.
    - Dosyanın son değiştirilme zamanına göre bölüm.

### <a name="network-bandwidth-and-storage-io"></a>Ağ bant genişliği ve depolama g/ç 

Data Lake Storage 1. verileri okuyan ve Data Lake Storage 2. veri yaza Data Factory kopyalama işlerinin Eşzamanlılığını denetleyebilir. Bu şekilde, geçiş sırasında Data Lake Storage 1. normal iş işinin etkilenmesinden kaçınmak için bu depolama g/ç üzerinde kullanımını yönetebilirsiniz.

### <a name="permissions"></a>İzinler 

Data Factory, [Data Lake Storage 1. Bağlayıcısı](connector-azure-data-lake-store.md) Azure Kaynak kimlik doğrulamaları için hizmet sorumlusu ve yönetilen kimliği destekler. [Data Lake Storage 2. Bağlayıcısı](connector-azure-data-lake-storage.md) , Azure Kaynak kimlik doğrulamaları için hesap anahtarını, hizmet sorumlusunu ve yönetilen kimliği destekler. Data Factory, ihtiyacınız olan tüm dosyaları veya erişim denetim listelerini (ACL 'Ler) geziniyor ve kopyalayabilmesini sağlamak için, tüm dosyaları erişmek, okumak veya yazmak ve tercih ediyorsanız ACL 'Leri ayarlamak için sağladığınız hesap için yüksek sayıda izin verin. Geçiş dönemi boyunca buna bir süper kullanıcı veya sahip rolü verin. 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>Data Lake Storage 1. ACL 'Leri koru

Data Lake Storage 1. ' den Data Lake Storage 2. sürümüne yükselttiğinizde ACL 'Leri veri dosyalarıyla birlikte çoğaltmak istiyorsanız bkz. [Data Lake Storage 1. ACL 'Leri koruma](connector-azure-data-lake-storage.md#preserve-acls). 

### <a name="incremental-copy"></a>Artımlı kopya 

Yalnızca yeni veya güncelleştirilmiş dosyaları Data Lake Storage 1. yüklemek için birkaç yaklaşımdan yararlanabilirsiniz:

- Yeni veya güncelleştirilmiş dosyaları zamana göre bölümlenmiş klasöre veya dosya adına göre yükleyin. Bir örnek/2019/05/13/* olur.
- Yeni veya güncelleştirilmiş dosyaları LastModifiedDate göre yükleyin.
- Herhangi bir üçüncü taraf araç veya çözüme göre yeni veya güncelleştirilmiş dosyaları belirler. Sonra, dosya veya klasör adını parametre veya tablo ya da dosya aracılığıyla Data Factory işlem hattına geçirin. 

Artımlı yükleme yapmak için uygun sıklık, Azure Data Lake Storage 1. içindeki toplam dosya sayısına ve her seferinde yüklenecek yeni veya güncelleştirilmiş dosya hacmine bağlıdır. 

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Kopyalama etkinliğine genel bakış](copy-activity-overview.md) 
>  [Azure Data Lake Storage 1. Bağlayıcısı](connector-azure-data-lake-store.md) 
>  [Azure Data Lake Storage 2. Bağlayıcısı](connector-azure-data-lake-storage.md)