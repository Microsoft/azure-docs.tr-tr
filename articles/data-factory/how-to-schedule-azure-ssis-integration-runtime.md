---
title: Azure-SSIS Integration Runtime zamanlama
description: Bu makalede Azure Data Factory kullanılarak Azure-SSIS Integration Runtime başlatma ve durdurma işlemlerinin nasıl planlanmasının nasıl yapılacağı açıklanır.
ms.service: data-factory
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/09/2020
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 40c3b8ab228c93fd4c681281d89d16f88ddf30f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100384376"
---
# <a name="how-to-start-and-stop-azure-ssis-integration-runtime-on-a-schedule"></a>Azure-SSIS Integration Runtime'ı belirli bir zamanlamaya göre başlatma ve durdurma

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Bu makalede, Azure Data Factory (ADF) kullanılarak Azure-SSIS Integration Runtime (IR) başlatma ve durdurma işlemlerinin nasıl planlanmasının nasıl yapılacağı açıklanır. Azure-SSIS IR, SQL Server Integration Services (SSIS) paketlerini yürütmek için adanmış, ADF işlem kaynağıdır. Azure-SSIS IR çalıştırmak, onunla ilişkili bir maliyettir. Bu nedenle, genellikle yalnızca Azure 'da SSIS paketlerini yürütmeniz gerektiğinde ve artık ihtiyacınız olmadığında IR 'nizi durdurmak istediğinizde IR 'nizi çalıştırmak istersiniz. Ayrıca, [IR 'yi el ile başlatmak veya durdurmak](manage-azure-ssis-integration-runtime.md)Için ADF kullanıcı ARABIRIMI (UI)/app veya Azure PowerShell kullanabilirsiniz.

Alternatif olarak, günlük ETL iş yüklerinizi yürütmeden ve bu işlemi tamamladıktan sonra öğleden sonra durdurmadan önce, örneğin günde bir başlangıç yapmak/durdurmak için ADF işlem hatları 'nda Web etkinlikleri oluşturabilirsiniz.  Ayrıca, IR 'nizi başlatan ve durduran iki Web etkinliği arasında bir SSIS paketi yürütme etkinliğini zincirleyebilirsiniz. böylece, IR 'niz, paket yürütmeden önce/sonra isteğe bağlı olarak başlayacak/bu şekilde durdurulur. SSIS paketi yürütme etkinliği hakkında daha fazla bilgi için bkz. [ADF işlem hattı 'NDAKI SSIS paketi yürütme etkinliğini kullanarak SSIS paketi çalıştırma](how-to-invoke-ssis-package-ssis-activity.md) .

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar
Azure-SSIS IR zaten sağlamadıysanız [öğreticideki](./tutorial-deploy-ssis-packages-azure.md)yönergeleri izleyerek sağlayın. 

## <a name="create-and-schedule-adf-pipelines-that-start-and-or-stop-azure-ssis-ir"></a>Azure-SSIS IR başlatılan ve durduran ADF işlem hatlarını oluşturma ve zamanlama
Bu bölümde, Azure-SSIS IR zamanlamaya göre başlatmak/durdurmak için Web etkinliklerinin nasıl kullanılacağı ve isteğe bağlı olarak & başlatılması gösterilmektedir. Üç işlem hattı oluşturmak için size kılavuzluk ederiz: 

1. İlk işlem hattı, Azure-SSIS IR Başlatan bir Web etkinliği içerir. 
2. İkinci işlem hattı, Azure-SSIS IR durduran bir Web etkinliği içerir.
3. Üçüncü işlem hattı, Azure-SSIS IR başlayan/durduran iki Web etkinliği arasında zincirleme bir SSIS paketi yürütme etkinliği içerir. 

Bu işlem hatlarını oluşturup test ettikten sonra, bir zamanlama tetikleyicisi oluşturabilir ve bunu herhangi bir ardışık düzen ile ilişkilendirebilirsiniz. Zamanlama tetikleyicisi, ilişkili ardışık düzeni çalıştırmaya yönelik bir zamanlama tanımlar. 

Örneğin, ilki 6 ' da günlük çalışacak şekilde zamanlanır ve birinci işlem hattı ile ilişkilendirilirken ikinci diğeri 6 PM 'de günlük çalışacak şekilde zamanlanır ve ikinci işlem hattı ile ilişkilendirilir.  Bu şekilde, IR 'nin çalıştığı her gün, günlük ETL iş yüklerinizi yürütmeye hazırlamak için 6 ila 6 saat arasında bir süre vardır.  

Her gün gece yarısı çalışacak ve üçüncü işlem hattı ile ilişkili olan üçüncü bir tetikleyici oluşturursanız, bu işlem hattı her gün gece yarısı çalışır, paket yürütmeden hemen önce IR 'niz başlatılır, daha sonra paketinizi yürütmekte ve artık IR 'niz, paket yürütmeden hemen sonra durdurulduktan sonra IR 'nin çalışması çalışmaz.

### <a name="create-your-adf"></a>ADF 'nizi oluşturma

1. [Azure Portal](https://portal.azure.com/)oturum açın.    
2. Soldaki menüde **Yeni**, **Veri + Analiz** ve **Data Factory** öğesine tıklayın. 
   
   ![Yeni->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
   
3. **Yeni Veri Fabrikası** sayfasında ad Için **Myazuressisdatafactory** **adını** girin. 
      
   ![Yeni veri fabrikası sayfası](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   ADF 'nizin adı genel olarak benzersiz olmalıdır. Aşağıdaki hatayı alırsanız ADF 'nizin adını (ör. yournameMyAzureSsisDataFactory) değiştirin ve yeniden oluşturmayı deneyin. ADF yapıtları için adlandırma kuralları hakkında bilgi edinmek için [Data Factory adlandırma kuralları](naming-rules.md) makalesine bakın.
  
   `Data factory name MyAzureSsisDataFactory is not available`
      
4. ADF 'nizi oluşturmak istediğiniz Azure **aboneliğinizi** seçin. 
5. **Kaynak Grubu** için aşağıdaki adımlardan birini uygulayın:
     
   - **Var olanı kullan**’ı seçin ve ardından açılır listeden var olan bir kaynak grubu belirleyin. 
   - **Yeni oluştur**' u seçin ve yeni kaynak grubunuzun adını girin.   
         
   Kaynak grupları hakkında bilgi edinmek için bkz. [Azure kaynaklarınızı yönetmek için kaynak gruplarını kullanma](../azure-resource-manager/management/overview.md) makalesi.
   
6. **Sürüm** için **v2** 'yi seçin.
7. **Konum** için, açılır listeden ADF oluşturma için desteklenen konumlardan birini seçin.
8. **Panoya sabitle**’yi seçin.     
9. **Oluştur**’a tıklayın.
10. Azure panosu 'nda şu durum ile şu kutucuğu görürsünüz: **dağıtım Data Factory**. 

    ![veri fabrikası dağıtılıyor kutucuğu](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
   
11. Oluşturma işlemi tamamlandıktan sonra, ADF sayfanıza aşağıda gösterildiği gibi bakabilirsiniz.
   
    ![Data factory giriş sayfası](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
   
12. ADF Kullanıcı arabirimini/uygulamayı ayrı bir sekmede başlatmak için **& Izleyiciyi yaz** ' a tıklayın.

### <a name="create-your-pipelines"></a>İşlem hatlarınızı oluşturun

1. **Haydi başlayalım** sayfasında Işlem **hattı oluştur**' u seçin. 

   ![Başlarken sayfası](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
   
2. **Etkinlikler** araç kutusunda **genel** menü ' i genişletin ve bir **Web** etkinliğini ardışık düzen Tasarımcısı yüzeyine bırakın & sürükleyin. Etkinlik özellikleri penceresinin **genel** sekmesinde, etkinlik adını **startmyar** olarak değiştirin. **Ayarlar** sekmesine geçin ve aşağıdaki eylemleri yapın.

    1. **URL** için, Azure-SSIS IR,,, ve yerine IR 'nizin gerçek değerleriyle başlayan REST API için aşağıdaki URL 'yi girin `{subscriptionId}` `{resourceGroupName}` `{factoryName}` `{integrationRuntimeName}` : `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/start?api-version=2018-06-01` Alternatif olarak, yukarıdaki URL 'nin aşağıdaki kısmını değiştirmek için, IR 'nin kaynak kimliğini ADF Kullanıcı arabirimi/uygulama üzerindeki izleme sayfasından kopyalamak & de yapıştırabilirsiniz:`/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}`
    
       ![ADF SSIS IR kaynak KIMLIĞI](./media/how-to-schedule-azure-ssis-integration-runtime/adf-ssis-ir-resource-id.png)
  
    2. **Yöntemi** için **gönderi**' ı seçin. 
    3. **Gövde** için girin `{"message":"Start my IR"}` . 
    4. **Kimlik doğrulaması** IÇIN, ADF 'niz için yönetilen kimliği kullanmak üzere **MSI** ' yi seçin. daha fazla bilgi için bkz. [Data Factory için yönetilen kimlik](./data-factory-service-identity.md) makalesi.
    5. **Kaynak** için girin `https://management.azure.com/` .
    
       ![ADF Web etkinlik zamanlaması SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-schedule-ssis-ir.png)
  
3. İkinci bir tane oluşturmak için ilk işlem hattını klonlayın, etkinlik adını **Stopmyır** olarak değiştirin ve aşağıdaki özellikleri değiştirin.

    1. **URL** için, Azure-SSIS IR durduran REST API,,, `{subscriptionId}` `{resourceGroupName}` `{factoryName}` ve `{integrationRuntimeName}` değerlerini, IR 'nizin gerçek değerleriyle değiştirerek aşağıdaki URL 'yi girin:`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/stop?api-version=2018-06-01`
    
    2. **Gövde** için girin `{"message":"Stop my IR"}` . 

4. Üçüncü bir işlem hattı oluşturun, **Eylemler** araç kutusundan bir **SSIS paketi yürütme** etkinliğini bir işlem hattı Tasarımcısı yüzeyine bırakın & SÜRÜKLEYIP, [ADF makalesinde SSIS paketi yürütme etkinliğini kullanarak SSIS paketini çağırma](how-to-invoke-ssis-package-ssis-activity.md) bölümündeki yönergeleri izleyerek yapılandırın.  Alternatif olarak, bir **saklı yordam** etkinliği kullanarak, [ADF makalesinde saklı yordam ETKINLIĞINI kullanarak bir SSIS paketini çağırma](how-to-invoke-ssis-package-stored-procedure-activity.md) bölümündeki yönergeleri izleyerek yapılandırabilirsiniz.  Ardından, ilk/ikinci işlem hatlarındaki bu Web etkinliklerine benzer şekilde, IR 'yi Başlatan/durduran iki Web etkinliği arasında Execute SSIS Package/saklı yordam etkinliğini zincirler.

   ![Istek üzerine ADF Web etkinliği, SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-on-demand-ssis-ir.png)

5. ADF, **katkıda** bulunan bir rolün yönetilen kimliğini kendisine atayın. böylece, işlem hatlarında Web etkinlikleri, sağlanan Azure-SSIS IRS 'yi başlatmak/durdurmak için REST API çağırabilir.  Azure portal ADF sayfanızda, **erişim denetimi (IAM)** öğesine tıklayın, **+ rol ataması Ekle**' ye tıklayın ve ardından **rol ataması Ekle** dikey penceresinde aşağıdaki eylemleri yapın.

    1. **Rol** Için **katkıda bulunan**' ı seçin. 
    2. **Erişim atama** Için, **Azure AD Kullanıcı, Grup veya hizmet sorumlusu**' nı seçin. 
    3. **Seç** için ADF adınızı arayın ve seçin. 
    4. **Kaydet**’e tıklayın.
    
   ![ADF yönetilen kimlik rolü ataması](./media/how-to-schedule-azure-ssis-integration-runtime/adf-managed-identity-role-assignment.png)

6. Fabrika/ardışık düzen araç çubuğunda **Tümünü doğrula/Doğrula ' ya** tıklayarak ADF ve tüm işlem hattı ayarlarınızı doğrulayın. Düğmeye tıklayarak **fabrika/Işlem hattı doğrulama çıkışını** kapatın **>>** .  

   ![İşlem hattını doğrulama](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-your-pipelines"></a>İşlem hatlarınızı test edin

1. Her işlem hattı için araç çubuğunda **test çalıştırması** ' nı seçin ve alt bölmedeki **Çıkış** penceresi ' ni görüntüleyin. 

   ![Test çalıştırması](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
    
2. Üçüncü işlem hattını test etmek için SQL Server Management Studio başlatın (SSMS). **Sunucuya Bağlan** penceresinde aşağıdaki eylemleri yapın. 

    1. **Sunucu adı** için **&lt; sunucu adı &gt; . Database.Windows.net** girin.
    2. **>>seçeneklerini** belirleyin.
    3. **Veritabanına Bağlan** Için **SSISDB**' yi seçin.
    4. **Bağlan**’ı seçin. 
    5. **Integration Services katalogları**  ->  **sssıı** ' nı genişletin-klasör > **projelerinizi** >-SSIS proje-> **paketlerinizi**>. 
    6. Çalıştırmak için belirtilen SSIS paketine sağ tıklayın ve **raporlar**  ->  **Standart raporlar**  ->  **Tüm yürütmeler**' i seçin. 
    7. Çalıştığını doğrulayın. 

   ![SSIS paketi çalıştırmasını doğrula](./media/how-to-schedule-azure-ssis-integration-runtime/verify-ssis-package-run.png)

### <a name="schedule-your-pipelines"></a>İşlem hatlarınızı zamanlama

İşlem hatlarınız beklediğiniz gibi çalışmadığına göre, bunları belirtilen bir noktada çalıştırmak için Tetikleyiciler de oluşturabilirsiniz. Tetikleyicileri işlem hatlarıyla ilişkilendirme hakkında ayrıntılar için bkz. [bir zamanlama](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule) makalesinde Işlem hattını tetikleme.

1. İşlem hattı araç çubuğunda **tetikleyici** ' i seçin ve **Yeni/Düzenle**' yi seçin. 

   ![Tetikleyici-> yeni/Düzenle menü seçeneğini vurgulayan ekran görüntüsü.](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)

2. **Tetikleyici Ekle** bölmesinde **+ Yeni**' yi seçin.

   ![Tetikleyici Ekle-yeni](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)

3. **Yeni tetikleyici** bölmesinde aşağıdaki eylemleri yapın: 

    1. **Ad** için tetikleyici için bir ad girin. Aşağıdaki örnekte **günlük Çalıştır** tetikleyici adıdır. 
    2. **Tür** için **zamanlama**' yı seçin. 
    3. **Başlangıç tarihi (UTC)** IÇIN, UTC olarak bir başlangıç tarihi ve saati girin. 
    4. **Yinelenme** için, tetikleyici için bir temposunda girin. Aşağıdaki örnekte, **günlük** olarak bir kez kullanılır. 
    5. **Son** için **Hayır** ' ı seçin veya **Tarih '** i seçtikten sonra bitiş tarihi ve saati girin. 
    6. ADF ayarlarını tamamen yayımladıktan sonra tetikleyiciyi etkinleştirmek için **etkinleştirildi** ' i seçin. 
    7. **İleri**’yi seçin.

   ![Tetikleyici-> yeni/Düzenle](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
    
4. **Tetikleyici çalıştırma parametreleri** sayfasında, tüm uyarıları gözden geçirin ve **son**' u seçin. 
5. Tüm ADF ayarlarını fabrika araç çubuğundan **Yayımla** ' yı seçerek yayımlayın. 

   ![Tümünü Yayımla](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)

### <a name="monitor-your-pipelines-and-triggers-in-azure-portal"></a>Azure portal işlem hatlarınızı ve tetiklerinizi izleyin

1. Tetikleyici çalıştırmalarını ve işlem hattı çalıştırmalarını izlemek için ADF Kullanıcı arabirimi/uygulamanın solundaki **izleyici** sekmesini kullanın. Ayrıntılı adımlar için bkz. işlem [hattı izleme](quickstart-create-data-factory-portal.md#monitor-the-pipeline) makalesi.

   ![İşlem hattı çalıştırmaları](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)

2. Bir Işlem hattı çalıştırmasıyla ilişkili etkinlik çalıştırmalarını görüntülemek için **Eylemler** sütunundaki ilk bağlantıyı (**etkinlik çalıştırmalarını görüntüle**) seçin. Üçüncü işlem hattı için, bir işlem hattındaki her zincirleme etkinlik (IR 'yi başlatmak için Web etkinliği, paketinizi yürütmek için saklı yordam etkinliği ve IR 'nizi durdurmak için Web etkinliği) için bir tane olmak üzere üç etkinlik çalıştırması görürsünüz. İşlem hattı çalıştırmalarını yeniden görüntülemek için üstteki işlem **hatları** bağlantısını seçin.

   ![Etkinlik çalıştırmaları](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)

3. Tetikleyici çalıştırmalarını görüntülemek için en üstteki işlem **hattı çalıştırmaları** altında bulunan açılan listeden **tetikleme çalıştırmaları** ' nı seçin. 

   ![Tetikleyici çalıştırmaları](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

### <a name="monitor-your-pipelines-and-triggers-with-powershell"></a>İşlem hatlarınızı ve tetiklerinizi PowerShell ile izleme

İşlem hatlarınızı ve tetiklerinizi izlemek için aşağıdaki örneklerde olduğu gibi betikler kullanın.

1. İşlem hattı çalıştırmasının durumunu alır.

   ```powershell
   Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $myPipelineRun
   ```

2. Tetikleyici hakkında bilgi alın.

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name  "myTrigger"
   ```

3. Tetikleyici çalıştırmasının durumunu alır.

   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "myTrigger" -TriggerRunStartedAfter "2018-07-15" -TriggerRunStartedBefore "2018-07-16"
   ```

## <a name="create-and-schedule-azure-automation-runbook-that-startsstops-azure-ssis-ir"></a>Azure-SSIS IR'yi başlatan/durduran Azure Otomasyonu runbook'unu oluşturma ve zamanlama

Bu bölümde, PowerShell betiğini yürüten Azure Otomasyonu runbook 'u oluşturmayı, Azure-SSIS IR bir zamanlamaya göre başlatmayı/durdurmayı öğreneceksiniz.  Bu, ön/son işleme için IR 'nizi başlattıktan veya durdurmadan önce ek betikler yürütmek istediğinizde yararlıdır.

### <a name="create-your-azure-automation-account"></a>Azure Otomasyonu hesabınızı oluşturun

Zaten bir Azure Otomasyonu hesabınız yoksa, bu adımdaki yönergeleri izleyerek bir tane oluşturun. Ayrıntılı adımlar için bkz. [Azure Otomasyonu hesabı oluşturma](../automation/automation-quickstart-create-account.md) makalesi. Bu adımın bir parçası olarak, bir **Azure farklı çalıştır** hesabı (Azure Active Directory bir hizmet sorumlusu) oluşturup Azure aboneliğinizde **katkıda** bulunan bir rol atamanız gerekir. Azure SSIS IR ile ADF 'nizi içeren aboneliğin aynı olduğundan emin olun. Azure Otomasyonu, Azure Resource Manager kimlik doğrulamak ve kaynaklarınızın üzerinde çalışmak için bu hesabı kullanır. 

1. **Microsoft Edge** veya **Google Chrome** web tarayıcısını açın. Şimdilik, ADF Kullanıcı arabirimi/uygulama yalnızca Microsoft Edge ve Google Chrome Web tarayıcılarında desteklenir.
2. [Azure Portal](https://portal.azure.com/)oturum açın.    
3. Sol menüden **Yeni** ' yi seçin, **izleme ve yönetim**' yi seçin ve **Otomasyon**' u seçin. 

   ![İzleme ve Yönetim > Otomasyon seçeneğini vurgulayan ekran görüntüsü.](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
    
2. **Otomasyon hesabı ekle** bölmesinde aşağıdaki eylemleri yapın.

    1. **Ad** Için Azure Otomasyonu hesabınız için bir ad girin. 
    2. **Abonelik** IÇIN Azure-SSIS IR ADF 'niz olan aboneliği seçin. 
    3. **Kaynak grubu** Için yeni **Oluştur** ' u seçerek yeni bir kaynak grubu **oluşturun veya mevcut** bir kaynak grubunu seçin. 
    4. **Konum** Için, Azure Otomasyonu hesabınız için bir konum seçin. 
    5. **Azure farklı çalıştır hesabı oluşturmayı** **Evet** olarak onaylayın. Azure Active Directory bir hizmet sorumlusu oluşturulur ve Azure aboneliğinizde **katkıda bulunan** bir rol atanır.
    6. Azure panosu 'nda kalıcı olarak göstermek için **panoya sabitle ' yi** seçin. 
    7. **Oluştur**’u seçin. 

   ![New-> İzleme ve Yönetim-> Otomasyonu](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
   
3. Azure panosu ve bildirimler ' de Azure Otomasyonu hesabınızın dağıtım durumunu görürsünüz. 
    
   ![Otomasyon dağıtılıyor](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
    
4. Başarılı bir şekilde oluşturulduktan sonra Azure Otomasyonu hesabınızın giriş sayfasını görürsünüz. 

   ![Otomasyon giriş sayfası](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-adf-modules"></a>ADF modüllerini içeri aktar

1. Sol menüdeki **paylaşılan kaynaklarda** **modüller** ' i seçin ve modüller listesinde **az. DataFactory**  +  **az. Profile** sahip olup olmadığını doğrulayın.

   ![Gerekli modülleri doğrulama](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image1.png)

2.  **Az. DataFactory**'niz yoksa, [az. datafactory modülüne](https://www.powershellgallery.com/packages/Az.DataFactory/)yönelik PowerShell Galerisi gidin, **Azure Otomasyonu 'na dağıt**' ı seçin, Azure Otomasyonu hesabınızı seçin ve ardından **Tamam**' ı seçin. Sol menüdeki **PAYLAŞıLAN kaynaklarda** **modülleri** görüntüleyin bölümüne geri dönün ve  **az. DataFactory** modülünün **kullanılabilir** olarak değiştiğini bekleyin.

    ![Data Factory modülünü doğrulama](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image2.png)

3.  **Az. Profile** yoksa, [az. profile Module](https://www.powershellgallery.com/packages/Az.profile/)Için PowerShell Galerisi gidin, **Azure Otomasyonu 'na dağıt**' ı seçin, Azure Otomasyonu hesabınızı seçin ve ardından **Tamam**' ı seçin. Sol menüdeki **PAYLAŞıLAN kaynaklarda** **modülleri** görüntüle bölümüne dönün ve **az. Profile** **modülünün,** **kullanılabilir** olarak değiştiğini bekleyin.

    ![Profil modülünü doğrulama](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image3.png)

### <a name="create-your-powershell-runbook"></a>PowerShell runbook 'unuzu oluşturma

Aşağıdaki bölümde PowerShell runbook 'u oluşturma adımları sağlanmaktadır. Runbook 'iyle ilişkili komut dosyası, **işlem** parametresi için belirttiğiniz komuta göre başlar/duraklar Azure-SSIS IR. Bu bölüm, runbook oluşturmaya yönelik tüm ayrıntıları sağlamaz. Daha fazla bilgi için bkz. [runbook oluşturma](../automation/automation-quickstart-create-runbook.md) makalesi.

1. **Runbook 'lar** sekmesine geçin ve araç çubuğundan **+ runbook Ekle** ' yi seçin. 

   ![+ Runbook Ekle düğmesini vurgulayan ekran görüntüsü.](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
   
2. **Yeni runbook oluştur** ' u seçin ve aşağıdaki eylemleri yapın: 

    1. **Ad** için **StartStopAzureSsisRuntime** girin.
    2. **Runbook türü** için **PowerShell**' i seçin.
    3. **Oluştur**’u seçin.
    
   ![Runbook ekleme düğmesi](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
   
3. Aşağıdaki PowerShell betiğini Kopyala & runbook betik pencerenize yapıştırın. Araç çubuğundaki **Kaydet** ve **Yayımla** düğmelerini kullanarak runbook 'unuzu kaydedin ve sonra yayımlayın. 

    ```powershell
    Param
    (
          [Parameter (Mandatory= $true)]
          [String] $ResourceGroupName,
    
          [Parameter (Mandatory= $true)]
          [String] $DataFactoryName,
    
          [Parameter (Mandatory= $true)]
          [String] $AzureSSISName,
    
          [Parameter (Mandatory= $true)]
          [String] $Operation
    )
    
    $connectionName = "AzureRunAsConnection"
    try
    {
        # Get the connection "AzureRunAsConnection "
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         
    
        "Logging in to Azure..."
        Connect-AzAccount `
            -ServicePrincipal `
            -TenantId $servicePrincipalConnection.TenantId `
            -ApplicationId $servicePrincipalConnection.ApplicationId `
            -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    }
    catch {
        if (!$servicePrincipalConnection)
        {
            $ErrorMessage = "Connection $connectionName not found."
            throw $ErrorMessage
        } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
        }
    }
    
    if($Operation -eq "START" -or $operation -eq "start")
    {
        "##### Starting #####"
        Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $AzureSSISName -Force
    }
    elseif($Operation -eq "STOP" -or $operation -eq "stop")
    {
        "##### Stopping #####"
        Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    }  
    "##### Completed #####"    
    ```

   ![PowerShell runbook 'unu Düzenle](./media/how-to-schedule-azure-ssis-integration-runtime/edit-powershell-runbook.png)
    
4. Araç çubuğundaki **Başlat** düğmesini seçerek runbook 'unuzu test edin. 

   ![Runbook 'u Başlat düğmesi](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
    
5. **Runbook 'U Başlat** bölmesinde aşağıdaki işlemleri yapın: 

    1. **Kaynak grubu adı** IÇIN Azure-SSIS IR ADF 'niz olan kaynak grubunun adını girin. 
    2. **Data Factory adı** IÇIN Azure-SSIS IR ADF 'nizin adını girin. 
    3. **Azuressisname** için Azure-SSIS IR adını girin. 
    4. **İşlem** için **Başlat** girin. 
    5. **Tamam**’ı seçin.  

   ![Runbook 'u Başlat penceresi](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
   
6. İş penceresinde **Çıkış** Kutucuğu ' nı seçin. Çıkış penceresinde **# # # # # tamamlandı # #** # # # iletisini gördüğünüzde # # # # **# ile # #**# # # # # # # # # # # # # # #. Azure-SSIS IR başlamak yaklaşık 20 dakika sürer. **İş** penceresini kapatın ve **runbook** penceresine geri dönün.

   ![Çıkış kutucuğunu vurgulayan ekran görüntüsü.](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
    
7. **İşlem** değeri olarak **stop** kullanarak önceki iki adımı tekrarlayın. Araç çubuğundaki **Başlat** düğmesini seçerek runbook 'unuzu yeniden başlatın. Kaynak grubunuzu, ADF 'yi ve Azure-SSIS IR adlarını girin. **İşlem** için **Durdur** yazın. Çıkış penceresinde **# # # # # tamamlandı # # # #** # iletisini gördüğünüzde # # # **# # durduruluyor # #**# # #. Azure-SSIS IR durdurulduğunda, başlatma uzun sürmez. **İş** penceresini kapatın ve **runbook** penceresine geri dönün.

8. Ayrıca, **Web kancaları** menü öğesini seçerek veya aşağıda belirtildiği gibi **zamanlamalar** menü öğesini seçerek oluşturulabilen bir zamanlamaya göre runbook 'unuzu de tetikleyebilirsiniz.  

## <a name="create-schedules-for-your-runbook-to-startstop-azure-ssis-ir"></a>Runbook 'larınız için başlatılacak/durdurulacak zamanlamalar oluşturun Azure-SSIS IR

Önceki bölümde, Azure-SSIS IR başlatabilir veya durdurabilir Azure Otomasyonu runbook 'unuzu oluşturdunuz. Bu bölümde, runbook 'niz için iki zamanlama oluşturacaksınız. İlk zamanlamayı yapılandırırken, **Işlemi** **Başlat** ' ı belirlersiniz. Benzer şekilde, ikincisini yapılandırırken **Işlemi** **Durdur** ' u belirtirsiniz. Zamanlamalar oluşturma hakkında ayrıntılı adımlar için bkz. [zamanlama oluşturma](../automation/shared-resources/schedules.md#create-a-schedule) makalesi.

1. **Runbook** penceresinde **zamanlamalar**' ı seçin ve + araç çubuğunda **bir zamanlama Ekle** ' yi seçin. 

   ![Azure SSIS IR-başlatıldı](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
   
2. **Runbook 'U zamanla** bölmesinde aşağıdaki işlemleri yapın: 

    1. **Runbook 'a bir zamanlama bağla**' yı seçin. 
    2. **Yeni zamanlama oluştur**' u seçin.
    3. **Yeni zamanlama** bölmesinde, **ad** için **günlük başlangıç IR** girin. 
    4. İçin **,** geçerli zamandan birkaç dakika geçmiş bir saat girin. 
    5. **Yinelenme** için **yineleme**' yi seçin. 
    6. **Her yineleme** için **1** girin ve **günü** seçin. 
    7. **Oluştur**’u seçin. 

   ![Azure SSIS IR başlangıç zamanlaması](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
    
3. Parametrelere geçin **ve ayarları Çalıştır** sekmesine geçin. Kaynak grubunuzu, ADF 'yi ve Azure-SSIS IR adlarını belirtin. **İşlem** için **Başlat** girin ve **Tamam**' ı seçin. Runbook 'larınızın **zamanlamalar** üzerinde zamanlama sayfasını görmek Için yeniden **Tamam ' ı** seçin. 

   ![Işlem alanını vurgulayan ekran görüntüsü.](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
    
4. **Günde stop ar** adlı bir zamanlama oluşturmak için önceki iki adımı tekrarlayın. **Başlangıç IR günlük** zamanlamasını en az 30 dakika sonra belirlediğiniz zamandan sonra girin. **İşlem** için **Durdur** yazın ve **Tamam**' ı seçin. Runbook 'larınızın **zamanlamalar** üzerinde zamanlama sayfasını görmek Için yeniden **Tamam ' ı** seçin. 

5. **Runbook** penceresinde Sol menüdeki **işler** ' i seçin. Zamanlamalarınız tarafından oluşturulan işleri belirtilen saatlerde ve bunların durumlarına göre görmeniz gerekir. Runbook 'unuzu test ettikten sonra gördüklerinize benzer şekilde, çıktısı gibi iş ayrıntılarını görebilirsiniz. 

   ![Azure SSIS IR 'yi başlama için zamanlama](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
    
6. Testi tamamladıktan sonra, bunları düzenleyerek zamanlamalarınızı devre dışı bırakın. Sol menüdeki **zamanlamalar** ' ı seçin, günde günlük **/IR 'Yi günde bir Başlat**' ı seçin ve **etkin** için **Hayır** ' ı seçin. 

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki blog gönderisine bakın:
-   [ADF/ELT iş akışlarınızı, ADF işlem hatlarında SSIS etkinlikleri ile modernleştirin ve genişletme](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)

SSIS belgelerinde aşağıdaki makalelere bakın: 

- [Azure’da bir SSIS paketi dağıtma, çalıştırma ve izleme](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Azure’da SSIS kataloğuna bağlanma](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Azure’da paket yürütmeyi zamanlama](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Windows kimlik doğrulaması ile şirket içi veri kaynaklarına bağlanma](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)