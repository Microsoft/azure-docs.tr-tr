---
title: "Öğretici: Kopyalama Sihirbazı&quot;nı kullanarak bir işlem hattı oluşturma | Microsoft Belgeleri"
description: "Bu öğreticide, Data Factory ile desteklenen Kopyalama Sihirbazı’nı kullanarak Kopyalama Etkinlikli bir Azure Data Factory işlem hattı oluşturursunuz"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: b87afb8e-53b7-4e1b-905b-0343dd096198
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/02/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: fbf77e9848ce371fd8d02b83275eb553d950b0ff
ms.openlocfilehash: 5a50f583831b398ae22416e7ade23c33846de55c
ms.lasthandoff: 02/03/2017


---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-data-factory-copy-wizard"></a>Öğretici: Data Factory Kopyalama Sihirbazı kullanarak Kopyalama Etkinliği ile işlem hattı oluşturma
> [!div class="op_single_selector"]
> * [Genel bakış ve önkoşullar](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Kopyalama Sihirbazı](data-factory-copy-data-wizard-tutorial.md)
> * [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager şablonu](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API’si](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 
> 

Azure Data Factory **Kopyalama Sihirbazı**, veri alma/taşıma senaryosu uygulayan bir işlem hattını kolayca ve hızlıca oluşturmanıza olanak sağlar. Bu nedenle, veri taşıma senaryosuna yönelik bir örnek işlem hattı oluşturmanın ilk adımı olarak sihirbazı kullanmanız önerilir. Bu öğretici bir Azure veri fabrikası oluşturma ve Kopyalama Sihirbazı’nı başlatma işlemlerini göstermesinin yanı sıra veri alma/taşıma senaryonuza ilişkin ayrıntılar sağlayan bir dizi adım uygular. Sihirbazdaki adımları tamamladığınızda sihirbaz bir Azure blob depolama alanından Azure SQL veritabanına veri kopyalamak için Kopyalama Etkinliği içeren bir işlem hattını otomatik olarak oluşturur. Kopyalama etkinliği hakkında ayrıntılı bilgi için [Veri Taşıma Etkinlikleri](data-factory-data-movement-activities.md) makalesine bakın. 

## <a name="prerequisites"></a>Ön koşullar
- Öğreticiye genel bir bakış atmak ve **ön koşul** adımlarını tamamlamak için [Öğreticiye Genel Bakış ve Ön Koşullar](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) bölümündeki adımları tamamlayın.


## <a name="create-data-factory"></a>Veri fabrikası oluşturma
Bu adımda **ADFTutorialDataFactory** adlı bir Azure data factory oluşturmak için Azure Portal’ı kullanırsınız.

1. [Azure portalında](https://portal.azure.com) oturum açtıktan sonra sol üst köşesdeki **+ YENİ** öğesine ve ardından **Intelligence + analytics** ve **Data Factory** öğelerine tıklayın. 
   
   ![Yeni->DataFactory](./media/data-factory-copy-data-wizard-tutorial/new-data-factory-menu.png)
2. **Yeni data factory** dikey penceresinde:
   
   1. **Ad** için **ADFTutorialDataFactory** girin.
       Azure veri fabrikasının adı genel olarak benzersiz olmalıdır. Şu hatayı alırsanız: **“ADFTutorialDataFactory” veri fabrikası adı yok**, veri fabrikasının adını değiştirin (örneğin, yournameADFTutorialDataFactory) ve oluşturmayı yeniden deneyin. Data Factory yapıtlarının adlandırma kuralları için [Data Factory - Adlandırma Kuralları](data-factory-naming-rules.md) konusuna bakın.  
      
       ![Data Factory adı yok](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-not-available.png)
      
      > [!NOTE]
      > Data factory adı gelecekte bir DNS adı olarak kaydedilmiş olabilir; bu nedenle herkese görünür hale gelmiştir.
      > 
      > 
   2. Azure **aboneliğinizi** seçin.
   3. Kaynak Grubu için aşağıdaki adımlardan birini uygulayın: 
      
      - Var olan bir kaynak grubu seçmek için **Var olanı kullan**’ı seçin.
      - Bir kaynak grubunun adını girmek için **Yeni oluştur**’u seçin.
         
          Bu öğreticideki adımlardan bazıları kaynak grubu için şu adı kullandığınızı varsayar: **ADFTutorialResourceGroup**. Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure kaynaklarınızı yönetmek için kaynak gruplarını kullanma](../azure-resource-manager/resource-group-overview.md).
   4. Veri fabrikası için bir **konum** seçin.
   5. Dikey pencerenin alt kısmındaki **Panoya sabitle** onay kutusunu seçin.  
   6. **Oluştur**’ tıklayın.
      
       ![Yeni veri fabrikası dikey penceresi](media/data-factory-copy-data-wizard-tutorial/new-data-factory-blade.png)            
3. Oluşturma işlemi tamamlandıktan sonra, aşağıdaki görüntüde gösterildiği gibi **Data Factory** dikey penceresini görürsünüz:
   
   ![Data factory giriş sayfası](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-home-page.png)

## <a name="launch-copy-wizard"></a>Kopyalama Sihirbazı'nı başlatma
1. Data Factory giriş sayfasında **Veri kopyala** kutucuğuna tıklayarak **Kopyalama Sihirbazı**’nı başlatın. 
   
   > [!NOTE]
   > Web tarayıcısının "Yetkilendiriliyor..." durumunda takıldığını görürseniz **Üçüncü taraf tanımlama bilgilerini ve site verilerini engelle** ayarını devre dışı bırakın/işaretini kaldırın (ya da) etkin durumda bırakıp **login.microsoftonline.com** için bir özel durum oluşturun ve ardından sihirbazı yeniden başlatmayı deneyin.
   > 
   > 
2. **Özellikler** sayfasında:
   
   1. **Görev adı** için **CopyFromBlobToAzureSql** girin
   2. **Açıklama** girin (isteğe bağlı).
   3. Bitiş tarihini bugüne, başlangıç tarihini ise geçerli günden beş gün öncesine ayarlayacak şekilde **Başlangıç tarihi ve saati** ile **Bitiş tarihi ve saati** değerlerini değiştirin.  
   4. **İleri**’ye tıklayın.  
      
      ![Kopyalama Aracı - Özellikler sayfası](./media/data-factory-copy-data-wizard-tutorial/copy-tool-properties-page.png) 
3. **Kaynak veri deposu** sayfasında **Azure Blob Storage** kutucuğuna tıklayın. Kopyalama görevine yönelik kaynak veri deposunu belirtmek için bu sayfayı kullanın. Yeni bir veri deposu belirtmek için mevcut bir veri deposu bağlı hizmetini kullanabilirsiniz (veya) yeni bir veri deposu belirtebilirsiniz. Mevcut bir bağlı hizmeti kullanmak için **MEVCUT BAĞLI HİZMETLERDEN** öğesine tıklayın ve doğru bağlı hizmeti seçin. 
   
    ![Kopyalama Aracı - Kaynak veri deposu sayfası](./media/data-factory-copy-data-wizard-tutorial/copy-tool-source-data-store-page.png)
4. **Azure Blob depolama hesabı belirtin** sayfasında:
   
   1. **Bağlı hizmet adı** için **AzureStorageLinkedService** girin.
   2. **Hesap seçme yöntemi** için **Azure aboneliklerinden** seçeneğinin belirlendiğini onaylayın.
   3. Azure **aboneliğinizi** seçin.  
   4. Seçili abonelikte bulunan Azure depolama hesapları listesinden bir **Azure depolama hesabı** seçin. Ayrıca **Hesap seçme yöntemi** için **El ile gir** öğesini seçip **İleri**’ye tıklayarak depolama hesabı ayarlarını el ile girmeyi seçebilirsiniz. 
      
      ![Kopyalama Aracı - Azure Blob depolama hesabı belirtin](./media/data-factory-copy-data-wizard-tutorial/copy-tool-specify-azure-blob-storage-account.png)
5. **Girdi dosyası veya klasörü seçin** sayfasında:
   
   1. **adftutorial** klasörüne gidin.
   2. **emp.txt** dosyasını seçip **Seç** öğesine tıklayın
   3. **İleri**’ye tıklayın. 
      
      ![Kopyalama Aracı - Girdi dosyası veya klasörü seçin](./media/data-factory-copy-data-wizard-tutorial/copy-tool-choose-input-file-or-folder.png)
6. **Girdi dosyası veya klasörü seçin** sayfasında **İleri**’ye tıklayın. **İkili kopya**’yı seçmeyin. 
   
    ![Kopyalama Aracı - Girdi dosyası veya klasörü seçin](./media/data-factory-copy-data-wizard-tutorial/chose-input-file-folder.png) 
7. **Dosya biçimi ayarları** sayfasında sınırlayıcıları ve sihirbaz tarafından dosya ayrıştırılarak otomatik olarak algılanan düzeni görürsünüz. Kopyalama sihirbazının otomatik algılamasını durdurmak veya geçersiz kılmak için sınırlayıcıları el ile de girebilirsiniz. Sınırlayıcıları gözden geçirin verilerin önizlemesini gördükten sonra **İleri**’ye tıklayın. 
   
    ![Kopyalama Aracı - Dosya biçimi ayarları](./media/data-factory-copy-data-wizard-tutorial/copy-tool-file-format-settings.png)  
8. Hedef veri deposu sayfasında **Azure SQL Veritabanı** öğesini ve **İleri**’yi seçin.
   
    ![Kopyalama Aracı - Hedef depo seçme](./media/data-factory-copy-data-wizard-tutorial/choose-destination-store.png)
9. **Azure SQL veritabanı belirtin** sayfasında:
   
   1. **Bağlantı adı** için **AzureSqlLinkedService** girin.
   2. **Sunucu / veritabanı seçme yöntemi** için **Azure aboneliklerinden** seçeneğinin belirlendiğini onaylayın.
   3. Azure **aboneliğinizi** seçin.  
   4. **Sunucu adı** ve **Veritabanı** öğelerini seçin.
   5. **Kullanıcı Adı** ve **Parola** girin.
   6. **İleri**’ye tıklayın.  
      
      ![Kopyalama Aracı - Azure SQL veritabanı belirtme](./media/data-factory-copy-data-wizard-tutorial/specify-azure-sql-database.png)
10. **Tablo eşleme** sayfasında **Hedef** alanı için aşağı açılan listeden **emp** öğesini seçin, **aşağı oka** tıklayarak (isteğe bağlı) şemayı ve verilerin önizlemesini görün.
    
     ![Kopyalama Aracı - Tablo eşleme](./media/data-factory-copy-data-wizard-tutorial/copy-tool-table-mapping-page.png) 
11. **Şema eşleme** sayfasında **İleri**’ye tıklayın.
    
    ![Kopyalama Aracı - düzen eşlemesi](./media/data-factory-copy-data-wizard-tutorial/schema-mapping-page.png)
12. **Performans ayarları** sayfasında **İleri**’ye tıklayın. 
    
    ![Kopyalama Aracı - performans ayarları](./media/data-factory-copy-data-wizard-tutorial/performance-settings.png)
13. **Özet** sayfasındaki bilgileri gözden geçirin ve **Son**’a tıklayın. Sihirbaz, veri fabrikasında (Kopyalama Sihirbazı’nı başlattığınız yer) iki bağlı hizmet, iki veri kümesi (girdi ve çıktı) ve bir işlem hattı oluşturur. 
    
    ![Kopyalama Aracı - performans ayarları](./media/data-factory-copy-data-wizard-tutorial/summary-page.png)

## <a name="launch-monitor-and-manage-application"></a>Uygulama İzleme ve Yönetmeyi başlatma
1. **Dağıtım** sayfasında, şu bağlantıya tıklayın: `Click here to monitor copy pipeline`.
   
   ![Kopyalama Aracı - Dağıtım başarılı](./media/data-factory-copy-data-wizard-tutorial/copy-tool-deployment-succeeded.png)  
2. Oluşturduğunuz işlem hattını izleme hakkında bilgi almak için [İzleme Uygulamasını kullanarak işlem hattını izleme ve yönetme](data-factory-monitor-manage-app.md) bölümündeki yönergeleri kullanın. Dilimi görmek için **ETKİNLİK PENCERELERİ** listesindeki **Yenile** simgesine tıklayın. 
   
   ![İzleme Uygulaması](./media/data-factory-copy-data-wizard-tutorial/monitoring-app.png) 
   
   
   En son durumu görmek için alt kısmındaki **ETKİNLİK PENCERELERİ** listesinde **Yenile** düğmesine tıklayın. Durum otomatik olarak yenilenmez. 

> [!NOTE]
> Bu öğreticideki veri işlem hattı, bir kaynak veri deposundaki verileri hedef veri deposuna kopyalar. Çıkış verileri üretmek için giriş verilerini dönüştürmez. Azure Data Factory kullanarak verileri dönüştürme hakkında bir öğretici için bkz. [Öğretici: Hadoop kümesi kullanarak verileri dönüştürmek için ilk işlem hattınızı oluşturma](data-factory-build-your-first-pipeline.md).
> 
> Bir etkinliğin çıkış veri kümesini diğer etkinliğin giriş veri kümesi olarak ayarlayarak iki etkinliği zincirleyebilir, yani bir etkinliği diğerinden sonra çalıştırılmasını sağlayabilirsiniz. Ayrıntılı bilgi için bkz. [Data Factory’de zamanlama ve yürütme](data-factory-scheduling-and-execution.md).

## <a name="see-also"></a>Ayrıca Bkz.
| Konu | Açıklama |
|:--- |:--- |
| [İşlem hatları](data-factory-create-pipelines.md) |Bu makale, Azure Data Factory’de işlem hatlarının ve etkinliklerini anlamanıza ve senaryonuz ya da işletmeniz için uçtan uca veri odaklı iş akışları oluşturmak amacıyla bunları nasıl kullanacağınızı anlamanıza yardımcı olur. |
| [Veri kümeleri](data-factory-create-datasets.md) |Bu makale, Azure Data Factory’deki veri kümelerini anlamanıza yardımcı olur. |
| [Zamanlama ve yürütme](data-factory-scheduling-and-execution.md) |Bu makalede Azure Data Factory uygulama modelinin zamanlama ve yürütme yönleri açıklanmaktadır. |


