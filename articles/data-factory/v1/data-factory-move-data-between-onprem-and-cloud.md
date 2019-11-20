---
title: Veri Veri Yönetimi ağ geçidini taşıma
description: Şirket içi ve bulut arasında veri taşımak için bir veri ağ geçidi ayarlayın. Verilerinizi taşımak için Azure Data Factory Veri Yönetimi ağ geçidini kullanın.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.assetid: 7bf6d8fd-04b5-499d-bd19-eff217aa4a9c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 52bce71abd6ecf30b5a3661c2e6033537357db3a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682486"
---
# <a name="move-data-between-on-premises-sources-and-the-cloud-with-data-management-gateway"></a>Veri Yönetimi ağ geçidi ile şirket içi kaynaklar ve bulut arasında veri taşıma
> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [Data Factory kullanarak verileri şirket içi ve bulut arasında kopyalama](../tutorial-hybrid-copy-powershell.md).

Bu makalede, Data Factory kullanarak şirket içi veri depoları ile bulut veri depoları arasında veri tümleştirmeye genel bakış sunulmaktadır. [Veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesinde ve diğer Data Factory temel kavramları makalelerinde oluşturulur: veri [kümeleri](data-factory-create-datasets.md) ve işlem [hatları](data-factory-create-pipelines.md).

## <a name="data-management-gateway"></a>Veri Yönetimi Ağ Geçidi
Şirket içi bir veri deposundan veri taşımayı etkinleştirmek için şirket içi makinenize Veri Yönetimi ağ geçidi yüklemelisiniz. Ağ Geçidi, veri deposuna bağlanabildiği sürece veri deposuyla aynı makineye veya farklı bir makineye yüklenebilir.

> [!IMPORTANT]
> Veri Yönetimi ağ geçidi hakkındaki ayrıntılar için bkz. [veri yönetimi ağ geçidi](data-factory-data-management-gateway.md) makalesi. 

Aşağıdaki kılavuzda, verileri şirket içi **SQL Server** veritabanından bir Azure Blob depolama alanına taşıyan bir işlem hattı ile veri fabrikası oluşturma adımları gösterilmektedir. Adım adım kılavuzun bir parçası olarak makinenize Veri Yönetimi Ağ Geçidi yükleyip bunu yapılandıracaksınız.

## <a name="walkthrough-copy-on-premises-data-to-cloud"></a>İzlenecek yol: şirket içi verileri buluta kopyalama
Bu kılavuzda, aşağıdaki adımları uygulayın: 

1. Veri fabrikası oluşturma.
2. Veri Yönetimi Ağ Geçidi oluşturun. 
3. Kaynak ve havuz veri depoları için bağlı hizmetler oluşturun.
4. Girdi ve çıktı verilerini temsil edecek veri kümeleri oluşturun.
5. Verileri taşımak için kopyalama etkinliği ile işlem hattı oluşturma.

## <a name="prerequisites-for-the-tutorial"></a>Öğretici için Önkoşullar
Bu yönergeyi başlamadan önce, aşağıdaki önkoşullara sahip olmanız gerekir:

* **Azure aboneliği**.  Bir aboneliğiniz yoksa, yalnızca birkaç dakika içinde ücretsiz bir deneme hesabı oluşturabilirsiniz. Ayrıntılar için [ücretsiz deneme](https://azure.microsoft.com/pricing/free-trial/) makalesine bakın.
* **Azure depolama hesabı**. Blob depolamayı Bu öğreticide **hedef/havuz** veri deposu olarak kullanırsınız. Azure depolama hesabınız yoksa, oluşturma adımları için [Depolama hesabı oluşturma](../../storage/common/storage-quickstart-create-account.md) makalesine bakın.
* **SQL Server**. Bu öğreticide şirket içi SQL Server veritabanını bir **kaynak** veri deposu olarak kullanırsınız. 

## <a name="create-data-factory"></a>Veri fabrikası oluşturma
Bu adımda, **ADFTutorialOnPremDF**adlı bir Azure Data Factory örneği oluşturmak için Azure Portal kullanırsınız.

1. [Azure Portal](https://portal.azure.com)’da oturum açın.
2. **Kaynak oluştur ' a**tıklayın, **Intelligence + Analytics**' e tıklayın ve **Data Factory**' ye tıklayın.

   ![Yeni->DataFactory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)  
3. **Yeni Veri Fabrikası** sayfasında ad için **ADFTutorialOnPremDF** girin.

    ![Başlangıç panosuna Ekle](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

   > [!IMPORTANT]
   > Azure veri fabrikasının adı genel olarak benzersiz olmalıdır. Şu hatayı alırsanız: **"ADFTutorialOnPremDF" Veri Fabrikası adı kullanılamıyor**, veri fabrikasının adını değiştirin (örneğin, yournameADFTutorialOnPremDF) ve yeniden oluşturmayı deneyin. Bu öğreticide kalan adımları gerçekleştirirken bu adı ADFTutorialOnPremDF yerine kullanın.
   >
   > Veri fabrikasının adı gelecekte bir **DNS** adı olarak kaydedilmiş olabilir ve bu nedenle herkese açık bir şekilde görünür hale gelir.
   >
   >
4. Data factory’yi oluşturmak istediğiniz **Azure aboneliği**’ni seçin.
5. Mevcut bir **kaynak grubu** seçin ya da bir kaynak grubu oluşturun. Öğretici için: **ADFTutorialResourceGroup**adlı bir kaynak grubu oluşturun.
6. **Yeni Veri Fabrikası** sayfasında **Oluştur** ' a tıklayın.

   > [!IMPORTANT]
   > Data Factory örnekleri oluşturmak için abonelik/kaynak grubu düzeyinde [Data Factory Katılımcısı](../../role-based-access-control/built-in-roles.md#data-factory-contributor) rolünün üyesi olmanız gerekir.
   >
   >
7. Oluşturma işlemi tamamlandıktan sonra, aşağıdaki görüntüde gösterildiği gibi **Data Factory** sayfasını görürsünüz:

   ![Data Factory giriş sayfası](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

## <a name="create-gateway"></a>Ağ Geçidi oluştur
1. **Data Factory** sayfasında, Veri Fabrikası için **düzenleyiciyi** başlatmak üzere **yaz ve dağıt** kutucuğuna tıklayın.

    ![Geliştir ve Dağıt Kutucuğu](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png)
2. Data Factory düzenleyicisinde,... öğesine tıklayın **. Araç çubuğundan daha fazla bilgi** ve ardından **Yeni veri ağ geçidi**' ne tıklayın. Alternatif olarak, ağaç görünümünde **veri ağ geçitleri** ' ne sağ tıklayıp **Yeni veri ağ geçidi**' ne tıklayabilirsiniz.

   ![Araç çubuğundaki yeni veri ağ geçidi](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
3. **Oluştur** sayfasında, **ad**Için **adftutorialgateway** girin ve **Tamam**' a tıklayın.     

    ![Ağ Geçidi sayfası oluştur](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

    > [!NOTE]
    > Bu kılavuzda, mantıksal ağ geçidini yalnızca bir düğüm (Şirket içi Windows makinesi) ile oluşturursunuz. Birden çok şirket içi makineyi ağ geçidiyle ilişkilendirerek bir veri yönetimi ağ geçidini ölçeklendirebilirsiniz. Bir düğümde aynı anda çalışabilen veri taşıma işlerinin sayısını artırarak ölçeği artırabilirsiniz. Bu özellik, tek düğümlü bir mantıksal ağ geçidi için de kullanılabilir. Ayrıntılar için [Azure Data Factory makalesindeki veri yönetimi ağ geçidini ölçeklendirme](data-factory-data-management-gateway-high-availability-scalability.md) bölümüne bakın.  
4. **Yapılandır** sayfasında, **Bu bilgisayarda doğrudan yüklensin**' e tıklayın. Bu eylem ağ geçidinin yükleme paketini indirir, yükler, yapılandırır ve bilgisayara ağ geçidini kaydeder.  

   > [!NOTE]
   > Internet Explorer veya Microsoft ClickOnce uyumlu bir Web tarayıcısı kullanın.
   >
   > Chrome kullanıyorsanız, [Chrome web mağazasına](https://chrome.google.com/webstore/) gidin, “ClickOnce” araması yapın, ClickOnce uzantılarından birini seçin ve yükleyin.
   >
   > Firefox için aynısını yapın (eklenti yüklemesi). Araç çubuğunda **menüyü aç** düğmesine (sağ üst köşedeki**üç yatay çizgi** ) tıklayın, **Eklentiler**' e tıklayın, "ClickOnce" anahtar sözcüğü ile arama yapın, ClickOnce uzantılarından birini seçin ve yüklemeyi yapın.    
   >
   >

    ![Ağ Geçidi-sayfayı Yapılandır](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

    Bu şekilde, ağ geçidini tek bir adımda karşıdan yüklemek, yüklemek, yapılandırmak ve kaydettirmek için en kolay yol (tek tıklamayla) vardır. **Microsoft veri yönetimi Gateway Configuration Manager** uygulamasının bilgisayarınızda yüklü olduğunu görebilirsiniz. Ayrıca, çalıştırılabilir **Configmanager. exe** dosyasını şu klasörde bulabilirsiniz: **C:\Program Files\Microsoft veri yönetimi Gateway\2.0\Shared**.

    Ayrıca, bu sayfadaki bağlantıları kullanarak ağ geçidini el ile indirip yükleyebilir ve **yenı anahtar** metin kutusunda gösterilen anahtarı kullanarak kaydedebilirsiniz.

    Ağ geçidine ilişkin tüm ayrıntılar için bkz. [veri yönetimi ağ geçidi](data-factory-data-management-gateway.md) makalesi.

   > [!NOTE]
   > Veri Yönetimi ağ geçidini başarılı bir şekilde yüklemek ve yapılandırmak için yerel bilgisayarda yönetici olmanız gerekir. **Veri yönetimi ağ geçidi kullanıcıları** yerel Windows grubuna daha fazla kullanıcı ekleyebilirsiniz. Bu grubun üyeleri, ağ geçidini yapılandırmak için Veri Yönetimi ağ geçidi Configuration Manager aracını kullanabilir.
   >
   >
5. Birkaç dakika bekleyin veya aşağıdaki bildirim iletisini görene kadar bekleyin:

    ![Ağ geçidi yüklemesi başarılı](./media/data-factory-move-data-between-onprem-and-cloud/gateway-install-success.png)
6. Bilgisayarınızda **veri yönetimi Gateway Configuration Manager** uygulamasını başlatın. **Ara** penceresinde, bu yardımcı programa erişmek Için **veri yönetimi ağ geçidi** yazın. Ayrıca, çalıştırılabilir **Configmanager. exe** dosyasını şu klasörde bulabilirsiniz: **C:\Program Files\Microsoft veri yönetimi Gateway\2.0\Shared**

    ![Ağ Geçidi Configuration Manager](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)
7. `adftutorialgateway is connected to the cloud service` ileti görtığınızdan emin olun. Alttaki durum çubuğu, bir **yeşil onay işaretiyle**birlikte **bulut hizmetine bağlı** görüntüler.

    **Giriş** sekmesinde, aşağıdaki işlemleri de yapabilirsiniz:

   * Kayıt düğmesini kullanarak Azure portal bir anahtara sahip bir ağ geçidi **kaydedin** .
   * Ağ Geçidi makinenizde çalışan Veri Yönetimi ağ geçidi konak hizmetini **durdurun** .
   * **Güncelleştirmeleri** günün belirli bir saatinde yüklenecek şekilde zamanlayın.
   * Ağ geçidinin **en son**ne zaman güncelleştirildiğini görüntüleyin.
   * Ağ geçidinin bir güncelleştirmesinin yüklenebileceği süreyi belirtin.
8. **Ayarlar** sekmesine geçin. **Sertifika** bölümünde belirtilen sertifika, portalda belirttiğiniz şirket içi veri deposunun kimlik bilgilerini şifrelemek/şifrelerini çözmek için kullanılır. seçim Bunun yerine kendi sertifikanızı kullanmak için **Değiştir** ' e tıklayın. Varsayılan olarak, ağ geçidi Data Factory hizmeti tarafından otomatik olarak oluşturulan sertifikayı kullanır.

    ![Ağ Geçidi sertifika yapılandırması](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

    **Ayarlar** sekmesinde aşağıdaki işlemleri de yapabilirsiniz:

   * Ağ Geçidi tarafından kullanılan sertifikayı görüntüleyin veya dışarı aktarın.
   * Ağ Geçidi tarafından kullanılan HTTPS uç noktasını değiştirin.    
   * Ağ Geçidi tarafından kullanılacak bir HTTP proxy 'si ayarlayın.     
9. seçim **Tanılama** sekmesine geçin, ağ geçidiyle ilgili sorunları gidermek için kullanabileceğiniz ayrıntılı günlüğü etkinleştirmek istiyorsanız **ayrıntılı günlüğü etkinleştir** seçeneğini işaretleyin. Günlüğe kaydetme bilgileri, **uygulama ve hizmet günlükleri** -> **veri yönetimi ağ geçidi** düğümü altında **Olay Görüntüleyicisi** bulunabilir.

    ![Tanılama sekmesi](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

    **Tanılama** sekmesinde aşağıdaki eylemleri de gerçekleştirebilirsiniz:

   * Ağ geçidini kullanarak bir şirket içi veri kaynağı için **test bağlantısı** bölümünü kullanın.
   * Veri Yönetimi ağ geçidi günlüğünü bir Olay Görüntüleyicisi penceresinde görmek için **günlükleri görüntüle** ' ye tıklayın.
   * Sorunları gidermeye yardımcı olmak için son yedi güne ait günlükleri olan bir ZIP dosyasını Microsoft 'a yüklemek için **günlükleri Gönder** ' e tıklayın.
10. **Tanılama** sekmesindeki **test bağlantısı** bölümünde veri deposunun türü için **SqlServer** ' ı seçin, veritabanı sunucusunun adını, veritabanının adını girin, kimlik doğrulama türünü belirtin, Kullanıcı adını ve parolayı girin ve ardından Ağ geçidinin veritabanına bağlanıp bağlanamamadığını test etmek için **Test** .
11. Web tarayıcısına geçin ve **Azure Portal** **Yapılandır** sayfasında **Tamam** ' a tıklayın ve ardından **Yeni veri ağ geçidi** sayfasında.
12. Sol taraftaki ağaç görünümünde **veri ağ geçitleri** altında **adftutorialgateway** görmeniz gerekir.  Bu öğeyi tıklatırsanız, ilişkili JSON 'u görmeniz gerekir.

## <a name="create-linked-services"></a>Bağlı hizmetler oluşturma
Bu adımda, iki bağlı hizmet oluşturacaksınız: **AzureStorageLinkedService** ve **sqlserverlinkedservice**. **Sqlserverlinkedservice** , bir şirket içi SQL Server veritabanını ve **AzureStorageLinkedService** bağlı hizmetini bir Azure blob deposunu veri fabrikasına bağlar. Bu izlenecek yolda daha sonra şirket içi SQL Server veritabanından Azure Blob deposuna veri kopyalayan bir işlem hattı oluşturursunuz.

#### <a name="add-a-linked-service-to-an-on-premises-sql-server-database"></a>Şirket içi SQL Server veritabanına bağlı hizmet ekleme
1. **Data Factory düzenleyicisinde**, araç çubuğundaki **Yeni veri deposu** ' na tıklayın ve **SQL Server**' ı seçin.

   ![Yeni SQL Server bağlantılı hizmet](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png)
2. Sağdaki **JSON düzenleyicisinde** aşağıdaki adımları uygulayın:

   1. **GatewayName**için **adftutorialgateway**belirtin.    
   2. **ConnectionString**öğesinde aşağıdaki adımları uygulayın:    

      1. **ServerName**için, SQL Server veritabanını barındıran sunucunun adını girin.
      2. **DatabaseName**için veritabanının adını girin.
      3. Araç çubuğunda **şifreleme** düğmesine tıklayın. Kimlik bilgileri Yöneticisi uygulamasını görürsünüz.

         ![Kimlik bilgileri Yöneticisi uygulaması](./media/data-factory-move-data-between-onprem-and-cloud/credentials-manager-application.png)
      4. **Kimlik bilgilerini ayarla** iletişim kutusunda kimlik doğrulama türü, Kullanıcı adı ve parola belirtin ve **Tamam**' ı tıklatın. Bağlantı başarılı olursa, şifreli kimlik bilgileri JSON 'da depolanır ve iletişim kutusu kapanır.
      5. Otomatik olarak kapatılmadığı ve Azure portal sekmeye geri dönmek için iletişim kutusu başlatılan boş tarayıcı sekmesini kapatın.

         Ağ Geçidi makinesinde, bu kimlik bilgileri Data Factory hizmeti 'nin sahip olduğu bir sertifika kullanılarak **şifrelenir** . Bunun yerine Veri Yönetimi ağ geçidiyle ilişkili sertifikayı kullanmak istiyorsanız, bkz. kimlik bilgilerini güvenli şekilde ayarlama.    
   3. SQL Server bağlı hizmeti dağıtmak için komut çubuğunda **Dağıt** ' a tıklayın. Bağlı hizmeti ağaç görünümünde görmeniz gerekir.

      ![Ağaç görünümünde bağlı hizmeti SQL Server](./media/data-factory-move-data-between-onprem-and-cloud/sql-linked-service-in-tree-view.png)    

#### <a name="add-a-linked-service-for-an-azure-storage-account"></a>Azure depolama hesabı için bağlı hizmet ekleme
1. **Data Factory düzenleyicisinde**, komut çubuğunda **Yeni veri deposu** ' na tıklayın ve **Azure depolama**' ya tıklayın.
2. **Hesap adı**için Azure depolama hesabınızın adını girin.
3. **Hesap anahtarı**için Azure depolama hesabınızın anahtarını girin.
4. **AzureStorageLinkedService**dağıtmak için **Dağıt** ' a tıklayın.

## <a name="create-datasets"></a>Veri kümeleri oluşturun
Bu adımda, kopyalama işlemi için girdi ve çıktı verilerini temsil eden girdi ve çıktı veri kümeleri oluşturursunuz (Şirket içi SQL Server veritabanı = > Azure blob depolama). Veri kümeleri oluşturmadan önce aşağıdaki adımları uygulayın (listeden sonra ayrıntılı adımlar verilir):

* Veri fabrikasına bağlı hizmet olarak eklediğiniz SQL Server Veritabanında **emp** adlı bir tablo oluşturun ve tabloya birkaç örnek giriş ekleyin.
* Veri fabrikasına bağlı hizmet olarak eklediğiniz Azure blob depolama hesabında **adftutorial** adlı bir blob kapsayıcı oluşturun.

### <a name="prepare-on-premises-sql-server-for-the-tutorial"></a>Eğitim için şirket içi SQL Server hazırlama
1. Şirket içi SQL Server bağlı hizmeti için belirttiğiniz veritabanında (**SqlServerLinkedService**), aşağıdaki SQL betiğini kullanarak veritabanında **emp** tablosunu oluşturun.

    ```SQL   
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
        CONSTRAINT PK_emp PRIMARY KEY (ID)
    )
    GO
    ```
2. Tabloya bazı örnekler ekleyin:

    ```SQL
    INSERT INTO emp VALUES ('John', 'Doe')
    INSERT INTO emp VALUES ('Jane', 'Doe')
    ```

### <a name="create-input-dataset"></a>Girdi veri kümesi oluşturma

1. **Data Factory düzenleyicisinde**,... öğesine tıklayın **. Daha fazla**, komut çubuğunda **Yeni veri kümesi** ' ne tıklayın ve **SQL Server tablo**' ya tıklayın.
2. Sağ bölmedeki JSON öğesini aşağıdaki metinle değiştirin:

    ```JSON   
    {        
        "name": "EmpOnPremSQLTable",
        "properties": {
            "type": "SqlServerTable",
            "linkedServiceName": "SqlServerLinkedService",
            "typeProperties": {
                "tableName": "emp"
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }     
    ```     
   Aşağıdaki noktalara dikkat edin:

   * **tür** **sqlservertable**olarak ayarlandı.
   * **TableName** , **bir.**
   * **Linkedservicename** , **sqlserverlinkedservice** olarak ayarlanır (Bu bağlı hizmeti bu kılavuzda daha önce oluşturdunuz.).
   * Azure Data Factory başka bir işlem hattı tarafından oluşturulmayan bir giriş veri kümesi için, **External** **değerini true**olarak ayarlamanız gerekir. Giriş verilerinin Azure Data Factory hizmetine harici olarak oluşturulduğunu gösterir. İsteğe bağlı olarak, **ilke** bölümündeki **externaldata** öğesini kullanarak herhangi bir dış veri ilkesi belirtebilirsiniz.    

   JSON özellikleri hakkında ayrıntılı bilgi için bkz. [SQL Server veri taşıma/kimden](data-factory-sqlserver-connector.md) .
3. Veri kümesini dağıtmak için komut çubuğunda **Dağıt** ' a tıklayın.  

### <a name="create-output-dataset"></a>Çıktı veri kümesi oluşturma

1. **Data Factory düzenleyicisinde**, komut çubuğunda **Yeni veri kümesi** ' ne tıklayın ve **Azure Blob depolama**' ya tıklayın.
2. Sağ bölmedeki JSON öğesini aşağıdaki metinle değiştirin:

    ```JSON   
    {
        "name": "OutputBlobTable",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "adftutorial/outfromonpremdf",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
     }
    ```   
   Aşağıdaki noktalara dikkat edin:

   * **tür** **AzureBlob**olarak ayarlanır.
   * **Linkedservicename** **AzureStorageLinkedService** olarak ayarlanır (Bu bağlı hizmeti 2. adımda oluşturdunuz).
   * **FolderPath** , **adföğreticisi/outfromonpremdf** olarak ayarlanır; burada outfromonpremdf, adföğreticisi kapsayıcısında klasörüdür. Henüz yoksa **adföğreticisi** kapsayıcısını oluşturun.
   * **availability** **hourly** olarak ayarlanmıştır (**frequency** **hour**, **interval** de **1** olarak ayarlanmıştır).  Data Factory **hizmeti, Azure SQL veritabanındaki, her** saat bir çıktı veri dilimi oluşturur.

   Bir **çıkış tablosu**Için bir **dosya adı** belirtmezseniz, **FolderPath** 'teki oluşturulan dosyalar şu biçimde adlandırılır: `Data.<Guid>.txt` (örneğin:: Data. 0a45f8a-93ff-4c6f-B3BE-f69616f1df7a. txt.).

   **FolderPath** ve **filename** **'i, Bölüm** alanına göre dinamik olarak ayarlamak için partitionedby özelliğini kullanın. Aşağıdaki örnekte, folderPath SliceStart’taki (işlemdeki dilimin başlangıç zamanı) Yıl, Ay ve Gün öğelerini, fileName ise SliceStart’taki Saat öğesini kullanır. Örneğin, dilim 2014-10-20T08:00:00 için oluşturulduysa, folderName wikidatagateway/wikisampledataout/2014/10/20, fileName de 08.csv olarak ayarlanır.

    ```JSON
    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy":
    [

        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
    ],
    ```

    JSON özellikleri hakkında ayrıntılı bilgi için bkz. [Azure Blob depolama alanına/verilerine veri taşıma](data-factory-azure-blob-connector.md) .
3. Veri kümesini dağıtmak için komut çubuğunda **Dağıt** ' a tıklayın. Ağaç görünümündeki her iki veri kümesini görtığınızdan emin olun.  

## <a name="create-pipeline"></a>İşlem hattı oluşturma
Bu adımda, Input ve **Outputblobtable** olarak **Emponpremsqltable** adlı bir **kopyalama etkinliği** ile bir işlem **hattı** oluşturacaksınız.

1. Data Factory düzenleyicide, **... öğesine tıklayın. Daha fazla bilgi**Için **Yeni işlem hattı**' na tıklayın.
2. Sağ bölmedeki JSON öğesini aşağıdaki metinle değiştirin:    

    ```JSON   
     {
         "name": "ADFTutorialPipelineOnPrem",
         "properties": {
         "description": "This pipeline has one Copy activity that copies data from an on premises SQL to Azure blob",
         "activities": [
           {
             "name": "CopyFromSQLtoBlob",
             "description": "Copy data from on premises SQL server to blob",
             "type": "Copy",
             "inputs": [
               {
                 "name": "EmpOnPremSQLTable"
               }
             ],
             "outputs": [
               {
                 "name": "OutputBlobTable"
               }
             ],
             "typeProperties": {
               "source": {
                 "type": "SqlSource",
                 "sqlReaderQuery": "select * from emp"
               },
               "sink": {
                 "type": "BlobSink"
               }
             },
             "Policy": {
               "concurrency": 1,
               "executionPriorityOrder": "NewestFirst",
               "style": "StartOfInterval",
               "retry": 0,
               "timeout": "01:00:00"
             }
           }
         ],
         "start": "2016-07-05T00:00:00Z",
         "end": "2016-07-06T00:00:00Z",
         "isPaused": false
       }
     }
    ```   
   > [!IMPORTANT]
   > **start** özelliğinin değerini geçerli günle, **end** değerini de sonraki günle değiştirin.
   >
   >

   Aşağıdaki noktalara dikkat edin:

   * Etkinlikler bölümünde, yalnızca **türü** **Kopyala**olarak ayarlanan etkinlik vardır.
   * Etkinliğin **girişi** **Emponpremsqltable** olarak, etkinliğin **çıktısı** ise **outputblobtable**olarak ayarlanır.
   * **Typeproperties** bölümünde, **kaynak türü** olarak **SQLSource** belirtilir ve **blobsink** **Havuz türü**olarak belirtilir.
   * **SQLSource**'un **sqlreaderquery** özelliği için SQL sorgusu `select * from emp` belirtildi.

   Başlangıç ve bitiş tarih saatleri [ISO biçiminde](https://en.wikipedia.org/wiki/ISO_8601) olmalıdır. Örneğin: 2014-10-14T16:32:41Z. **End** zamanı isteğe bağlıdır; ancak bu öğreticide bunu kullanacağız.

   **end** özelliği için değer belirtmezseniz "**start + 48 hours**" olarak hesaplanır. İşlem hattını süresiz olarak çalıştırmak için **end** özelliği değerini **9/9/9999** olarak ayarlayın.

   Veri dilimlerinin her Azure Data Factory veri kümesi için tanımlanan **kullanılabilirlik** özelliklerine göre işlendiği süreyi tanımlamanız gerekir.

   Örnekte, her veri dilimi saatlik oluşturulduğundan 24 veri dilimi vardır.        
3. Veri kümesini dağıtmak için komut çubuğunda **Dağıt** ' a tıklayın (tablo dikdörtgen bir veri kümesidir). İşlem hattının, işlem **hatları** düğümü altında ağaç görünümünde görüntülendiğini doğrulayın.  
4. Şimdi, **ADFTutorialOnPremDF**için sayfanın **Data Factory** sayfasına geri dönmek Için sayfayı kapatmak üzere **X** simgesini iki kez tıklatın.

**Tebrikler!** Başarılı bir şekilde Azure Data Factory, bağlı hizmetler, veri kümeleri ve işlem hattı oluşturdunuz ve işlem hattını zamanladınız.

#### <a name="view-the-data-factory-in-a-diagram-view"></a>Data factory’yi Diyagram Görünümünde görüntüleme
1. **Azure Portal**, **ADFTutorialOnPremDF** Data Factory 'nin giriş sayfasında **Diyagram** Kutucuğu ' na tıklayın. :

    ![Diyagram bağlantısı](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)
2. Aşağıdaki görüntüye benzer bir diyagram görmeniz gerekir:

    ![Diyagram Görünümü](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

    Yakınlaştırabilir, yakınlaştırabilir, %100 oranında yakınlaştırabilir, sığacak kadar yakınlaştırabilir, işlem hatlarını ve veri kümelerini otomatik olarak konumlandırabilirsiniz ve kökenini bilgilerini gösterebilirsiniz (seçili öğelerin yukarı akış ve aşağı akış öğelerini vurgular).  Özelliklerini görmek için bir nesneye (giriş/çıkış veri kümesi veya işlem hattı) çift tıklayabilirsiniz.

## <a name="monitor-pipeline"></a>İşlem hattını izleme
Bu adımda, Azure data factory’de neler olduğunu izlemek için Azure Portal kullanacaksınız. Veri kümelerini ve işlem hatlarını izlemek için de PowerShell cmdlet'lerini kullanabilirsiniz. İzleme hakkındaki ayrıntılar için bkz. işlem [hatlarını izleme ve yönetme](data-factory-monitor-manage-pipelines.md).

1. Diyagramda **Emponpremsqltable öğesine**çift tıklayın.  

    ![EmpOnPremSQLTable dilimleri](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)
2. Ardışık düzen süresi (başlangıç saati bitiş saati) geçmişte olduğundan tüm veri dilimlerinin **kullanılabilir durumda olduğundan emin olun.** Ayrıca, verileri SQL Server veritabanına yerleştirdiğinize ve bu da her zaman bir süredir. Alttaki **sorun dilimleri** bölümünde hiçbir dilim gösterilmeyeceğini onaylayın. Tüm dilimleri görüntülemek için, dilimler listesinin en altında bulunan **daha fazlasını gör** ' e tıklayın.
3. Şimdi, **veri kümeleri** sayfasında **outputblobtable**' a tıklayın.

    ![OputputBlobTable dilimleri](./media/data-factory-move-data-between-onprem-and-cloud/OutputBlobTableSlicesBlade.png)
4. Listeden herhangi bir veri dilimine tıklayın ve **veri dilimi** sayfasını görmeniz gerekir. Dilim için etkinlik çalıştırmaları görürsünüz. Genellikle yalnızca bir etkinlik çalıştırması görürsünüz.  

    ![Veri dilimi dikey penceresi](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

    Dilim **Hazır** durumunda değilse, Hazır olmayan ve geçerli dilimin yürütülmesini engelleyen yukarı akış dilimlerini **Hazır olmayan yukarı akış dilimleri** listesinde görebilirsiniz.
5. **Etkinliğin çalışma ayrıntılarını**görmek için alttaki listeden **etkinlik çalıştırmasına** tıklayın.

   ![Etkinlik çalıştırma ayrıntıları sayfası](./media/data-factory-move-data-between-onprem-and-cloud/ActivityRunDetailsBlade.png)

   Aktarım hızı, süre ve verileri aktarmak için kullanılan ağ geçidi gibi bilgileri görürsünüz.
6. Tüm sayfaları kapatmak için **X** simgesini tıklatın
7. **ADFTutorialOnPremDF**için giriş sayfasına dönün.
8. seçim İşlem **hatları**' na tıklayın, **ADFTutorialOnPremDF**' a tıklayın ve giriş tablolarında (**tüketilen**) veya çıkış veri kümelerinde (**üretilmiş**) detaya gidin.
9. Her saat için bir blob/dosyanın oluşturulduğunu doğrulamak üzere [Microsoft Depolama Gezgini](https://storageexplorer.com/) gibi araçları kullanın.

   ![Azure Depolama Gezgini](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## <a name="next-steps"></a>Sonraki adımlar
* Veri Yönetimi ağ geçidiyle ilgili tüm ayrıntılar için bkz. [veri yönetimi ağ geçidi](data-factory-data-management-gateway.md) makalesi.
* Kaynak veri deposundan bir havuz veri deposuna veri taşımak için kopyalama etkinliğinin nasıl kullanılacağı hakkında bilgi edinmek için bkz. [Azure Blob 'Dan Azure SQL 'e veri kopyalama](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .
