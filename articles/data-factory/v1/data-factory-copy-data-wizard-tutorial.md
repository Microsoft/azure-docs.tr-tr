---
title: "Öğretici: Kopyalama Sihirbazı'nı kullanarak bir işlem hattı oluşturma "
description: Bu öğreticide, Data Factory ile desteklenen Kopyalama Sihirbazı’nı kullanarak Kopyalama Etkinlikli bir Azure Data Factory işlem hattı oluşturursunuz
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: b87afb8e-53b7-4e1b-905b-0343dd096198
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 3afe58baa083ccbfe075476f9a7a8b36c30ca9c8
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682811"
---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-data-factory-copy-wizard"></a>Öğretici: Data Factory Kopyalama Sihirbazı kullanarak Kopyalama Etkinliği ile işlem hattı oluşturma
> [!div class="op_single_selector"]
> * [Genel bakış ve önkoşullar](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Kopyalama Sihirbazı](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager şablonu](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API’si](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız bkz. [kopyalama etkinliği öğreticisi](../quickstart-create-data-factory-dot-net.md). 


Bu öğretici, verileri bir Azure blob depolamadan Azure SQL veritabanına kopyalamak için **Kopyalama Sihirbazı**’nın nasıl kullanılacağını gösterir. 

Azure Data Factory **Kopyalama Sihirbazı**, verileri desteklenen kaynak veri deposundan desteklenen bir hedef veri deposuna kopyalayan veri işlem hattını hızlıca oluşturmanıza olanak tanır. Bu nedenle, veri taşıma senaryonuza yönelik bir örnek işlem hattı oluşturmanın ilk adımı olarak sihirbazı kullanmanız önerilir. Kaynak ve hedef olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](data-factory-data-movement-activities.md#supported-data-stores-and-formats).  

Bu öğretici bir Azure veri fabrikası oluşturma ve Kopyalama Sihirbazı’nı başlatma işlemlerini göstermesinin yanı sıra veri alma/taşıma senaryonuza ilişkin ayrıntılar sağlayan bir dizi adım uygular. Sihirbazdaki adımları tamamladığınızda sihirbaz bir Azure blob depolama alanından Azure SQL veritabanına veri kopyalamak için Kopyalama Etkinliği içeren bir işlem hattını otomatik olarak oluşturur. Kopyalama Etkinliği hakkında daha fazla bilgi için bkz. [veri taşıma etkinlikleri](data-factory-data-movement-activities.md).

## <a name="prerequisites"></a>Ön koşullar
Bu öğreticiyi uygulamadan önce [Öğreticiye Genel Bakış](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) makalesinde listelenen önkoşulları tamamlayın.

## <a name="create-data-factory"></a>Veri fabrikası oluşturma
Bu adımda **ADFTutorialDataFactory** adlı bir Azure data factory oluşturmak için Azure Portal’ı kullanırsınız.

1. [Azure portalı](https://portal.azure.com)’nda oturum açın.
2. Sol üst köşedeki **Kaynak oluştur** öğesine **Veri ve analiz**’e ve **Data Factory** öğesine tıklayın. 
   
   ![Yeni->DataFactory](./media/data-factory-copy-data-wizard-tutorial/new-data-factory-menu.png)
2. **Yeni data factory** dikey penceresinde:
   
   1. **Ad** için **ADFTutorialDataFactory** girin.
       Azure veri fabrikasının adı genel olarak benzersiz olmalıdır. `Data factory name “ADFTutorialDataFactory” is not available` hatasını alırsanız veri fabrikasının adını değiştirin (örneğin, yournameADFTutorialDataFactoryYYYYMMDD) ve yeniden oluşturmayı deneyin. Data Factory yapıtlarının adlandırma kuralları için [Data Factory - Adlandırma Kuralları](data-factory-naming-rules.md) konusuna bakın.  
      
       ![Data Factory adı yok](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-not-available.png)    
   2. Azure **aboneliğinizi** seçin.
   3. Kaynak Grubu için aşağıdaki adımlardan birini uygulayın: 
      
      - Var olan bir kaynak grubu seçmek için **Var olanı kullan**’ı seçin.
      - Bir kaynak grubunun adını girmek için **Yeni oluştur**’u seçin.
          
        Bu öğreticideki adımlardan bazıları kaynak grubu için şu adı kullandığınızı varsayar: **ADFTutorialResourceGroup**. Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure kaynaklarınızı yönetmek için kaynak gruplarını kullanma](../../azure-resource-manager/resource-group-overview.md).
   4. Veri fabrikası için bir **konum** seçin.
   5. Dikey pencerenin alt kısmındaki **Panoya sabitle** onay kutusunu seçin.  
   6. **Oluştur**'a tıklayın.
      
       ![Yeni veri fabrikası dikey penceresi](media/data-factory-copy-data-wizard-tutorial/new-data-factory-blade.png)            
3. Oluşturma işlemi tamamlandıktan sonra, aşağıdaki görüntüde gösterildiği gibi **Data Factory** dikey penceresini görürsünüz:
   
   ![Data factory giriş sayfası](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-home-page.png)

## <a name="launch-copy-wizard"></a>Kopyalama Sihirbazı'nı başlatma
1. Data Factory dikey penceresinde **Veri kopyala** öğesine tıklayarak **Kopyalama Sihirbazı**’nı başlatın. 
   
   > [!NOTE]
   > Web tarayıcısının "Yetkilendiriliyor..." durumunda takıldığını görürseniz, tarayıcı ayarlarından **Üçüncü taraf tanımlama bilgilerini ve site verilerini engelle** ayarını devre dışı bırakın/ayarının işaretini kaldırın (veya) etkin durumda bırakıp **login.microsoftonline.com** için bir özel durum oluşturun ve ardından sihirbazı yeniden başlatmayı deneyin.
2. **Özellikler** sayfasında:
   
   1. **Görev adı** için **CopyFromBlobToAzureSql** girin
   2. **Açıklama** girin (isteğe bağlı).
   3. Bitiş tarihini bugüne, başlangıç tarihini ise beş gün öncesine ayarlayacak şekilde **Başlangıç tarihi ve saati** ile **Bitiş tarihi ve saati** değerlerini değiştirin.  
   4. **İleri**’ye tıklayın.  
      
      ![Kopyalama Aracı - Özellikler sayfası](./media/data-factory-copy-data-wizard-tutorial/copy-tool-properties-page.png) 
3. **Kaynak veri deposu** sayfasında **Azure Blob Storage** kutucuğuna tıklayın. Kopyalama görevine yönelik kaynak veri deposunu belirtmek için bu sayfayı kullanın. 
   
    ![Kopyalama Aracı - Kaynak veri deposu sayfası](./media/data-factory-copy-data-wizard-tutorial/copy-tool-source-data-store-page.png)
4. **Azure Blob depolama hesabı belirtin** sayfasında:
   
   1. **Bağlı hizmet adı** için **AzureStorageLinkedService** girin.
   2. **Hesap seçme yöntemi** için **Azure aboneliklerinden** seçeneğinin belirlendiğini onaylayın.
   3. Azure **aboneliğinizi** seçin.  
   4. Seçili abonelikte bulunan Azure depolama hesapları listesinden bir **Azure depolama hesabı** seçin. Ayrıca **Hesap seçme yöntemi** için **El ile gir** öğesini seçip **İleri**’ye tıklayarak depolama hesabı ayarlarını el ile girmeyi seçebilirsiniz. 
      
      ![Kopyalama Aracı - Azure Blob depolama hesabı belirtin](./media/data-factory-copy-data-wizard-tutorial/copy-tool-specify-azure-blob-storage-account.png)
5. **Girdi dosyası veya klasörü seçin** sayfasında:
   
   1. **adftutorial** seçeneğine (klasör) çift tıklayın.
   2. **emp.txt** dosyasını seçip **Seç** öğesine tıklayın
      
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
2. İzleme uygulaması, web tarayıcınızdaki ayrı bir sekmede başlatılır.   
   
   ![İzleme Uygulaması](./media/data-factory-copy-data-wizard-tutorial/monitoring-app.png)   
3. Saatlik dilimlerin en son durumu görmek için alt kısımdaki **ETKİNLİK PENCERELERİ** listesinde **Yenile** düğmesine tıklayın. İşlem hattının başlangıç ve bitiş saatleri arasındaki beş gün için beş etkinlik görürsünüz. Liste otomatik olarak yenilenmez. Bu nedenle, tüm etkinlik pencerelerini Hazır durumunda görebilmeniz için Yenile düğmesine birkaç kez tıklamanız gerekebilir. 
4. Listeden bir etkinlik penceresi seçin. Ayrıntılarını sağ taraftaki **Etkinlik Penceresi Gezgini**’nde görebilirsiniz.

    ![Etkinlik penceresi ayrıntıları](media/data-factory-copy-data-wizard-tutorial/activity-window-details.png)    

    11, 12, 13, 14 ve 15 tarihlerinin yeşil renkli olduğuna dikkat edin; yeşil renk, bu tarihlerin günlük çıktı dilimlerinin zaten oluşturulduğu anlamına gelir. Bu renk kodlamasını, diyagram görünümünde işlem hattı ve çıktı veri kümesinde de görebilirsiniz. Önceki adımda, iki dilimin zaten oluşturulduğuna, bir dilimin o anda işlenmekte olduğuna ve diğer ikisinin işlenmeyi beklediğine dikkat edin (renk kodlamasına göre). 

    Bu uygulamayı kullanma hakkında daha fazla bilgi için [İzleme Uygulamasını kullanarak işlem hattını izleme ve yönetme](data-factory-monitor-manage-app.md) makalesine bakın.

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, bir kopyalama işleminde kaynak veri deposu olarak Azure blob depolama alanını ve hedef veri deposu olarak Azure SQL veritabanını kullandınız. Aşağıdaki tabloda, kopyalama etkinliği tarafından kaynaklar ve hedefler olarak desteklenen veri depolarının listesi sağlanmıştır: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Bir veri deposu için kopyalama sihirbazında gördüğünüz alanlar/özellikler hakkında bilgi için tablodaki veri deposu bağlantısına tıklayın. 