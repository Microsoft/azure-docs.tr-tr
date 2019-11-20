---
title: "Azure sanal ağ 'da Hive kullanarak verileri dönüştürme "
description: Bu öğretici, Azure Data Factory'de Hive etkinliğini kullanarak verileri dönüştürmeye ilişkin adım adım yönergeler sağlar.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/04/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 73a43bdb859d39bd0cb8e3d4a3ed3f114fb2c156
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683436"
---
# <a name="transform-data-in-azure-virtual-network-using-hive-activity-in-azure-data-factory"></a>Azure Data Factory’de Hive etkinliğini kullanarak Azure Sanal Ağ’daki verileri dönüştürme
Bu öğreticide, Azure portalını kullanarak Azure Sanal Ağ’daki bir HDInsight kümesinde Hive Etkinliği ile verileri dönüştüren bir Data Factory işlem hattı oluşturursunuz. Bu öğreticide aşağıdaki adımları gerçekleştireceksiniz:

> [!div class="checklist"]
> * Veri fabrikası oluşturma. 
> * Şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma
> * Azure Depolama ve Azure HDInsight bağlı hizmetleri oluşturma
> * Hive etkinliği ile bir işlem hattı oluşturun.
> * İşlem hattı çalıştırması tetikleyin.
> * İşlem hattı çalıştırmasını izleme 
> * Çıktıyı doğrulama

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure Depolama hesabı**. Bir hive betiği oluşturun ve Azure depolama alanına yükleyin. Hive betiğinin çıktısı bu depolama hesabında depolanır. Bu örnekte, HDInsight kümesi bu Azure Depolama hesabını birincil depolama alanı olarak kullanır. 
- **Azure Sanal Ağ.** Bir Azure sanal ağınız yoksa [bu yönergeleri](../virtual-network/quick-create-portal.md) izleyerek bir tane oluşturun. Bu örnekte HDInsight bir Azure Sanal Ağ içindedir. Azure Sanal Ağ’ın örnek yapılandırması aşağıda verilmiştir. 

    ![Sanal ağ oluşturma](media/tutorial-transform-data-using-hive-in-vnet-portal/create-virtual-network.png)
- **HDInsight kümesi.** Bir HDInsight kümesi oluşturun ve önceki adımda oluşturduğunuz sanal ağa katmak için şu makaleyi izleyin: [Bir Azure Sanal Ağ kullanarak Azure HDInsight’ı genişletme](../hdinsight/hdinsight-extend-hadoop-virtual-network.md). Bir sanal ağda HDInsight’ın örnek yapılandırması aşağıda verilmiştir. 

    ![Sanal ağda HDInsight](media/tutorial-transform-data-using-hive-in-vnet-portal/hdinsight-virtual-network-settings.png)
- **Azure PowerShell**. [Azure PowerShell’i yükleme ve yapılandırma](/powershell/azure/install-Az-ps) bölümündeki yönergeleri izleyin.
- **Bir sanal makine**. Bir Azure sanal makinesi oluşturun ve HDInsight kümenizi içeren sanal ağa ekleyin. Ayrıntılar için bkz. [Sanal makine oluşturma](../virtual-network/quick-create-portal.md#create-virtual-machines). 

### <a name="upload-hive-script-to-your-blob-storage-account"></a>Hive betiğini Blob Depolama hesabınıza yükleme

1. Aşağıdaki içeriğe sahip **hivescript.hql** adlı bir Hive SQL dosyası oluşturun:

   ```sql
   DROP TABLE IF EXISTS HiveSampleOut; 
   CREATE EXTERNAL TABLE HiveSampleOut (clientid string, market string, devicemodel string, state string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' 
   STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

   INSERT OVERWRITE TABLE HiveSampleOut
   Select 
       clientid,
       market,
       devicemodel,
       state
   FROM hivesampletable
   ```
2. Azure Blob depolama alanınızda henüz yoksa **adftutorial** adlı bir kapsayıcı oluşturun.
3. **hivescripts** adlı bir klasör oluşturun.
4. **hivescript.hql** dosyasını **hivescripts** alt klasörüne yükleyin.

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

1. **Microsoft Edge** veya **Google Chrome** web tarayıcısını açın. Şu anda Data Factory kullanıcı arabirimi yalnızca Microsoft Edge ve Google Chrome web tarayıcılarında desteklenmektedir.
1. [Azure Portal](https://portal.azure.com/)’da oturum açın.    
2. Soldaki menüde **Yeni**, **Veri + Analiz** ve **Data Factory** öğesine tıklayın. 
   
   ![Yeni->DataFactory](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-data-factory-menu.png)
3. **Yeni veri fabrikası** sayfasında **ad** için **ADFTutorialHiveFactory** adını girin. 
      
     ![Yeni veri fabrikası sayfası](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-azure-data-factory.png)
 
   Azure data factory adı **küresel olarak benzersiz** olmalıdır. Aşağıdaki hatayı alırsanız veri fabrikasının adını değiştirin (örneğin adınızMyAzureSsisDataFactory) ve yeniden oluşturmayı deneyin. Data Factory yapıtlarının adlandırma kuralları için [Data Factory - Adlandırma Kuralları](naming-rules.md) makalesine bakın.
  
       `Data factory name “MyAzureSsisDataFactory” is not available`
3. Veri fabrikasını oluşturmak istediğiniz Azure **aboneliğini** seçin. 
4. **Kaynak Grubu** için aşağıdaki adımlardan birini uygulayın:
     
   - **Var olanı kullan**’ı seçin ve ardından açılır listeden var olan bir kaynak grubu belirleyin. 
   - **Yeni oluştur**’u seçin ve bir kaynak grubunun adını girin.   
         
     Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure kaynaklarınızı yönetmek için kaynak gruplarını kullanma](../azure-resource-manager/resource-group-overview.md).  
4. **Sürüm** için **V2**'yi seçin.
5. Data factory için **konum** seçin. Listede yalnızca veri fabrikası oluşturma için desteklenen konumlar gösterilir.
6. **Panoya sabitle**’yi seçin.     
7. **Oluştur**'a tıklayın.
8. Panoda şu kutucuğu ve üzerinde şu durumu görürsünüz: **Veri fabrikası dağıtılıyor**. 

     ![veri fabrikası dağıtılıyor kutucuğu](media/tutorial-transform-data-using-hive-in-vnet-portal/deploying-data-factory.png)
9. Oluşturma işlemi tamamlandıktan sonra, resimde gösterildiği gibi **Data Factory** sayfasını görürsünüz.
   
    ![Data factory giriş sayfası](./media/tutorial-transform-data-using-hive-in-vnet-portal/data-factory-home-page.png)
10. Azure Data Factory Kullanıcı Arabirimini (UI) ayrı bir sekmede açmak için **Geliştir ve İzle**’ye tıklayın.
11. **Başlarken** sayfasında, aşağıdaki resimde gösterildiği gibi sol bölmede bulunan **Düzenle** sekmesine geçin: 

    ![Düzenle sekmesi](./media/tutorial-transform-data-using-hive-in-vnet-portal/get-started-page.png)

## <a name="create-a-self-hosted-integration-runtime"></a>Şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma
Hadoop kümesi bir sanal ağın içinde olduğundan, aynı sanal ağa şirket içinde barındırılan bir tümleştirme çalışma zamanı (IR) yüklemeniz gerekir. Bu bölümde yeni bir VM oluşturur, bu VM’yi aynı sana ağa katar ve VM’ye şirket içinde barındırılan IR yüklersiniz. Şirket içinde barındırılan IR, Data Factory hizmetinin bir sanal ağ içindeki HDInsight gibi bir işlem hizmetine işleme istekleri göndermesine imkan tanır. Ayrıca, bir sanal ağ içindeki veri depoları ile Azure arasında veri taşımanıza imkan sağlar. Veri deposu veya işlem de şirket içi bir ortamda olduğunda, şirket içinde barındırılan IR kullanırsınız. 

1. Azure Data Factory kullanıcı arabiriminde, pencerenin en altından **Bağlantılar**’a tıklayın, **Tümleştirme Çalışma Zamanları** sekmesine geçin ve araç çubuğunda **+ Yeni** düğmesine tıklayın. 

   ![Yeni tümleştirme çalışma zamanı menüsü](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-integration-runtime-menu.png)
2. **Tümleştirme Çalışma Zamanı Kurulumu** penceresinde **Dış işlemlere veri taşıma ve dağıtım etkinlikleri gerçekleştir** seçeneğini belirleyip **İleri**’ye tıklayın. 

   ![Veri taşıma ve dağıtım etkinlikleri gerçekleştir seçeneği](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-perform-data-movement-compute-option.png)
3. **Özel Ağ** seçeneğini belirleyip **İleri**’ye tıklayın.
    
   ![Özel ağı seçin](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-private-network.png)
4. **Ad** için **MySelfHostedIR** adını girip **İleri**’ye tıklayın. 

   ![Tümleştirme çalışma zamanı adını belirleyin](./media/tutorial-transform-data-using-hive-in-vnet-portal/integration-runtime-name.png) 
5. Kopyala düğmesine tıklayarak tümleştirme çalışma zamanının **kimlik doğrulama anahtarını** kopyalayın ve kaydedin. Pencereyi açık tutun. Bir sanal makinede yüklü IR’yi kaydetmek için bu anahtarı kullanırsınız. 

   ![Kimlik doğrulama anahtarını kopyalama](./media/tutorial-transform-data-using-hive-in-vnet-portal/copy-key.png)

### <a name="install-ir-on-a-virtual-machine"></a>Bir sanal makineye IR yükleme

1. Azure VM’ye [şirket içinde barındırılan tümleştirme çalışma zamanını](https://www.microsoft.com/download/details.aspx?id=39717) indirin. Şirket içinde barındırılan tümleştirme çalışma zamanını el ile kaydetmek için önceki adımda elde edilen **kimlik doğrulama anahtarını** kullanın. 

    ![Tümleştirme çalışma zamanını kaydetme](media/tutorial-transform-data-using-hive-in-vnet-portal/register-integration-runtime.png)

2. Şirket içinde barındırılan tümleştirme çalışma zamanı başarıyla kaydedildiğinde aşağıdaki iletiyi görürsünüz. 
   
    ![Başarıyla kaydedildi](media/tutorial-transform-data-using-hive-in-vnet-portal/registered-successfully.png)
3. **Configuration Manager'ı Başlat**’a tıklayın. Düğüm bulut hizmetine bağlandığında şu sayfayı görürsünüz: 
   
    ![Düğüm bağlı](media/tutorial-transform-data-using-hive-in-vnet-portal/node-is-connected.png)

### <a name="self-hosted-ir-in-the-azure-data-factory-ui"></a>Azure Data Factory kullanıcı arabiriminde şirket içinde barındırılan IR

1. **Azure Data Factory kullanıcı arabiriminde**, şirket içinde barındırılan sanal makinenin adını ve durumunu görürsünüz.

   ![Mevcut şirket içinde barındırılan düğümler](./media/tutorial-transform-data-using-hive-in-vnet-portal/existing-self-hosted-nodes.png)
2. **Son**’a tıklayarak **Tümleştirme Çalışma Zamanı** penceresini kapatın. Tümleştirme çalışma zamanları listesinde şirket içinde barındırılan IR’yi görürsünüz.

   ![Listedeki şirket içinde barındırılan IR](./media/tutorial-transform-data-using-hive-in-vnet-portal/self-hosted-ir-in-list.png)


## <a name="create-linked-services"></a>Bağlı hizmetler oluşturma

Bu bölümde iki Bağlı Hizmet oluşturup dağıtacaksınız:
- Bir Azure Depolama hesabını veri fabrikasına bağlayan **Azure Depolama Bağlı Hizmeti**. Bu depolama, HDInsight kümeniz tarafından kullanılan birincil depolamadır. Bu durumda, Hive betiğini ve betiğin çıktısını depolamak için de bu Azure Depolama hesabını kullanırsınız.
- Bir **HDInsight Bağlı Hizmeti**. Azure Data Factory, Hive betiğini yürütmek üzere bu HDInsight kümesine gönderir.

### <a name="create-azure-storage-linked-service"></a>Azure Storage bağlı hizmeti oluşturma

1. **Bağlı Hizmetler** sekmesine geçin ve **Yeni**’ye tıklayın.

   ![Yeni bağlı hizmet düğmesi](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-linked-service.png)    
2. **Yeni Bağlı Hizmet** penceresinde **Azure Blob Depolama**’yı seçip **Devam**’a tıklayın. 

   ![Azure Blob Depolama’yı seçin](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-azure-storage.png)
3. **Yeni Bağlı Hizmet** penceresinde aşağıdaki adımları izleyin:

    1. **Ad** için **AzureStorageLinkedService** adını girin.
    2. **Tümleştirme çalışma zamanı aracılığıyla bağlan** için **MySelfHostedIR** seçeneğini belirleyin.
    3. **Depolama hesabı adı** için Azure depolama hesabınızı seçin. 
    4. Depolama hesabı bağlantısını test etmek için **Bağlantıyı sına**’ya tıklayın.
    5. **Kaydet** düğmesine tıklayın.
   
        ![Azure Blob Depolama hesabını belirtme](./media/tutorial-transform-data-using-hive-in-vnet-portal/specify-azure-storage-account.png)

### <a name="create-hdinsight-linked-service"></a>HDInsight bağlı hizmeti oluşturma

1. Bir kere daha **Yeni**’ye tıklayarak başka bir bağlı hizmet oluşturun. 
    
   ![Yeni bağlı hizmet düğmesi](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-linked-service.png)    
2. **İşlem** sekmesine geçin, **Azure HDInsight**’ı seçin ve **Devam**’a tıklayın.

    ![Azure HDInsight’ı seçin](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-hdinsight.png)
3. **Yeni Bağlı Hizmet** penceresinde aşağıdaki adımları izleyin:

    1. **Ad** için **AzureHDInsightLinkedService** adını girin.
    2. **Kendi HDInsight’ınızı getirin**’i seçin. 
    3. **Hdi kümesi** için HDInsight kümenizi seçin. 
    4. HDInsight kümesi için **kullanıcı adını** girin.
    5. Kullanıcının **parolasını** girin. 
    
        ![Azure HDInsight ayarları](./media/tutorial-transform-data-using-hive-in-vnet-portal/specify-azure-hdinsight.png)

Bu makalede, kümeye internet üzerinden erişebildiğiniz varsayılır. Örneğin, `https://clustername.azurehdinsight.net` konumundaki kümeye bağlanabildiğiniz kabul edilir. Bu adres, İnternet'ten erişimi kısıtlamak için ağ güvenlik grupları (NSG) veya kullanıcı tanımlı yollar (UDR) kullandıysanız kullanılabilir olmayan ortak ağ geçidi kullanır. Data Factory’nin işleri Azure Sanal Ağdaki HDInsight kümesine gönderebilmesi için Azure Sanal Ağınızı URL’nin HDInsight tarafından kullanılan ağ geçidine ait özel IP adresine çözümlenebileceği şekilde yapılandırmanız gerekir.

1. Azure portalından, HDInsight’ın içinde bulunduğu Sanal Ağı açın. Adı `nic-gateway-0` ile başlayan ağ arabirimini açın. Özel IP adresini not edin. Örneğin, 10.6.0.15. 
2. Azure sanal ağınızda DNS sunucusu varsa, HDInsight kümesi `https://<clustername>.azurehdinsight.net` URL’sinin `10.6.0.15` hedefine çözümlenebilmesi için DNS kaydını güncelleştirin. Azure Sanal Ağınızda bir DNS sunucusu yoksa, aşağıdaki gibi bir giriş ekleyerek şirket içinde barındırılan tümleştirme çalışma zamanı düğümleri olarak kaydedilmiş tüm VM’lerin ana bilgisayar dosyalarını (C:\Windows\System32\drivers\etc) düzenleyerek geçici bir çözüm bulabilirsiniz: 

    `10.6.0.15 myHDIClusterName.azurehdinsight.net`

## <a name="create-a-pipeline"></a>İşlem hattı oluşturma 
Bu adımda, Hive etkinliği ile bir işlem hattı oluşturacaksınız. Etkinlik, bir örnek tablodan veri döndürmek ve tanımladığınız bir yola kaydetmek üzere Hive betiğini yürütür.

Aşağıdaki noktalara dikkat edin:

- **scriptPath**, MyStorageLinkedService için kullandığınız Azure Depolama Hesabında Hive betiğinin yoluna işaret eder. Bu yol büyük/küçük harfe duyarlıdır.
- **Çıktı**, Hive betiğinde kullanılan bir değişkendir. Azure Depolama hesabınızda var olan bir klasörü işaret etmek için `wasbs://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/` biçimini kullanın. Bu yol büyük/küçük harfe duyarlıdır. 

1. Data Factory kullanıcı arabiriminde, sol bölmedeki **+ (artı)** seçeneğine tıklayıp **İşlem Hattı**’na tıklayın. 

    ![Yeni işlem hattı menüsü](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-pipeline-menu.png)
2. **Etkinlikler** araç kutusunda **HDInsight**’ı genişletin ve **Hive** etkinliğini sürükleyerek işlem hattı tasarımcısının yüzeyine bırakın. 

    ![Hive etkinliğini sürükleyip bırakma](./media/tutorial-transform-data-using-hive-in-vnet-portal/drag-drop-hive-activity.png)
3. Özellikler penceresinde **HDI Kümesi** sekmesine geçin ve **HDInsight Bağlı Hizmeti** için **AzureHDInsightLinkedService** hizmetini seçin.

    ![HDInsight bağlı hizmetini seçme](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-hdinsight-linked-service.png)
4. **Betikler** sekmesine geçin ve aşağıdaki adımları uygulayın: 

    1. **Betik Bağlı Hizmeti** için **AzureStorageLinkedService** hizmetini seçin. 
    2. **Dosya Yolu** için **Depolamaya Gözat**’a tıklayın. 
 
        ![Depolamaya gözat](./media/tutorial-transform-data-using-hive-in-vnet-portal/browse-storage-hive-script.png)
    3. **Dosya veya klasör seçin** penceresinde **adftutorial** kapsayıcısının **hivescripts** klasörüne gidin, **hivescript.hql** dosyasını seçin ve **Son**'a tıklayın.  
        
        ![Dosya veya klasör seçme](./media/tutorial-transform-data-using-hive-in-vnet-portal/choose-file-folder.png) 
    4. **Dosya Yolu** olarak **adftutorial/hivescripts/hivescript.hql** yolunu gördüğünüzü onaylayın.

        ![Betik ayarları](./media/tutorial-transform-data-using-hive-in-vnet-portal/confirm-hive-script-settings.png)
    5. **Betik** sekmesinde **Gelişmiş** bölümünü genişletin. 
    6. **Parametreler** için **Betikten otomatik olarak doldur**’a tıklayın. 
    7. **Çıktı** parametresinin değerini şu biçimde girin: `wasbs://<Blob Container>@<StorageAccount>.blob.core.windows.net/outputfolder/`. Örneğin: `wasbs://adftutorial@mystorageaccount.blob.core.windows.net/outputfolder/`.
 
        ![Betik bağımsız değişkenleri](./media/tutorial-transform-data-using-hive-in-vnet-portal/script-arguments.png)
1. Yapıtları Data Factory’de yayımlamak için **Yayımla**’ya tıklayın.

    ![Yayımlama](./media/tutorial-transform-data-using-hive-in-vnet-portal/publish.png)

## <a name="trigger-a-pipeline-run"></a>İşlem hattı çalıştırması tetikleme

1. İlk olarak araç çubuğundaki **Doğrula** düğmesine tıklayarak işlem hattını doğrulayın. **Sağ ok (>>)** seçeneğine tıklayarak **İşlem Hattı Doğrulama Çıktı** penceresini kapatın. 

    ![İşlem hattını doğrulama](./media/tutorial-transform-data-using-hive-in-vnet-portal/validate-pipeline.png) 
2. Bir işlem hattı çalıştırması tetiklemek için araç çubuğunda Tetikle’ye tıklayıp Şimdi Tetikle’ye tıklayın. 

    ![Şimdi tetikle](./media/tutorial-transform-data-using-hive-in-vnet-portal/trigger-now-menu.png)

## <a name="monitor-the-pipeline-run"></a>İşlem hattı çalıştırmasını izleme

1. Soldaki **İzleyici** sekmesine geçin. **İşlem Hattı Çalıştırmaları** listesinde bir işlem hattı çalıştırması görürsünüz. 

    ![İşlem hattı çalıştırmalarını izleme](./media/tutorial-transform-data-using-hive-in-vnet-portal/monitor-pipeline-runs.png)
2. Listeyi yenilemek için **Yenile**’ye tıklayın.
4. İşlem hattı çalıştırmasıyla ilişkili etkinlik çalıştırmalarını görüntülemek için **Eylemler** sütunundan **Etkinlik çalıştırmalarını göster**’e tıklayın. Diğer eylem bağlantıları, işlem hattının durdurulması/yeniden çalıştırılması içindir. 

    ![Etkinlik çalıştırmalarını görüntüleme](./media/tutorial-transform-data-using-hive-in-vnet-portal/view-activity-runs-link.png)
5. İşlem hattında **HDInsightHive** türünde tek bir etkinlik olduğundan, yalnızca bir etkinlik çalıştırması görürsünüz. Önceki görünüme dönmek için üstteki **İşlem hatları** bağlantısına tıklayın.

    ![Etkinlik çalıştırmaları](./media/tutorial-transform-data-using-hive-in-vnet-portal/view-activity-runs.png)
6. **adftutorial** kapsayıcısının **outputfolder** klasöründe bir çıktı dosyası gördüğünüzü onaylayın. 

    ![Çıktı dosyası](./media/tutorial-transform-data-using-hive-in-vnet-portal/output-file.png)

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide aşağıdaki adımları gerçekleştirdiniz: 

> [!div class="checklist"]
> * Veri fabrikası oluşturma. 
> * Şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma
> * Azure Depolama ve Azure HDInsight bağlı hizmetleri oluşturma
> * Hive etkinliği ile bir işlem hattı oluşturun.
> * İşlem hattı çalıştırması tetikleyin.
> * İşlem hattı çalıştırmasını izleme 
> * Çıktıyı doğrulama

Azure üzerinde bir Spark kümesi kullanarak veri dönüştürme hakkında bilgi edinmek için aşağıdaki öğreticiye geçin:

> [!div class="nextstepaction"]
>[Data Factory denetim akışında dal oluşturma ve zincirleme](tutorial-control-flow-portal.md)



