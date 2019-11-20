---
title: CI/CD ve Azure DevOps ile Azure Stream Analytics işleri dağıtma
description: Bu makalede, Azure DevOps Services kullanarak CI/CD ile Stream Analytics işinin nasıl dağıtıldığı açıklanır.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 7810cf9950d919469c4b47053a878518726f4393
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72925076"
---
# <a name="tutorial-deploy-an-azure-stream-analytics-job-with-cicd-using-azure-pipelines"></a>Öğretici: Azure Pipelines kullanarak CI/CD ile Azure Stream Analytics işi dağıtma
Bu öğreticide, Azure Pipelines kullanarak bir Azure Stream Analytics işi için sürekli tümleştirme ve dağıtımın nasıl ayarlanacağı açıklanır. 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Projenize kaynak denetimi ekleme
> * Azure Pipelines’da derleme işlem hattı oluşturma
> * Azure Pipelines’da yayın işlem hattı oluşturma
> * Uygulamayı otomatik olarak dağıtma ve yükseltme

## <a name="prerequisites"></a>Önkoşullar
Başlamadan önce şunlara sahip olduğunuzdan emin olun:

* Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.
* [Visual Studio](stream-analytics-tools-for-visual-studio-install.md)’yu ve **Azure geliştirme** veya **Veri Depolama ve İşleme** iş yüklerini yükleyin.
* [Visual Studio’da Stream Analytics projesi](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-vs) oluşturun.
* [Azure DevOps](https://visualstudio.microsoft.com/team-services/) kuruluşu oluşturun.

## <a name="configure-nuget-package-dependency"></a>NuGet paketi bağımlılığını yapılandırma
Rastgele bir makinede otomatik derleme ve otomatik dağıtım yapmak için `Microsoft.Azure.StreamAnalytics.CICD` NuGet paketini kullanmanız gerekir. Bu, Stream Analytics Visual Studio projelerinin sürekli tümleştirme ve dağıtım işlemini destekleyen MSBuild, yerel çalıştırma ve dağıtım araçlarını sağlar. Daha fazla bilgi için bkz. [Stream Analytics CI/CD araçları](stream-analytics-tools-for-visual-studio-cicd.md).

Proje dizininize **packages.config** ekleyin.

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
<package id="Microsoft.Azure.StreamAnalytics.CICD" version="1.0.0" targetFramework="net452" />
</packages>
```

## <a name="share-your-visual-studio-solution-to-a-new-azure-repos-git-repo"></a>Visual Studio çözümünüzü yeni bir Azure Repos Git deposunda paylaşma

Derlemeler oluşturabilmek için uygulamanızın kaynak dosyalarını Azure DevOps’daki bir projede paylaşın.  

1. Visual Studio’nun sağ alt köşesindeki durum çubuğunda **Kaynak Denetimine Ekle**’yi ve sonra da **Git**’i seçerek projeniz için yeni bir yerel Git deposu oluşturun. 

2. **Takım Gezgini**’ndeki **Eşitleme** görünümünde **Azure DevOps Services’a Gönder**’in altında yer alan **Git Deposunda Yayımla** düğmesini seçin.

   ![Git depo Azure DevOps Services yayımlama düğmesine Gönder](./media/stream-analytics-tools-visual-studio-cicd-vsts/publish-git-repo-devops.png)

3. E-postanızı doğrulayın ve **Azure DevOps Services Etki Alanı** açılır listesinde kuruluşunuzu seçin. Deponuzun adını girin ve **Depoyu yayımla**’yı seçin.

   ![Git deposu yayımlama depoyu Gönder düğmesi](./media/stream-analytics-tools-visual-studio-cicd-vsts/publish-repository-devops.png)

    Depoyu yayımlamak, kuruluşunuzda yerel depoyla aynı ada sahip yeni bir proje oluşturur. Var olan projede depoyu oluşturmak için, **Depo adının** yanındaki **Gelişmiş**’e tıklayın ve bir proje seçin. **Web üzerinde görüntüleyin**’i seçerek kodunuzu tarayıcıda görüntüleyebilirsiniz.
 
## <a name="configure-continuous-delivery-with-azure-devops"></a>Azure DevOps ile sürekli teslimi yapılandırma
Azure Pipelines derleme işlem hattı, sırayla yürütülen derleme adımlarından oluşturulmuş bir iş akışını açıklar. [Azure Pipelines derleme işlem hatları](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav) hakkında daha fazla bilgi edinin. 

Bir Azure Pipelines yayın işlem hattı, kümeye uygulama paketi dağıtan bir iş akışını açıklar. Derleme işlem hattı ve yayın işlem hattı birlikte kullanıldığında kaynak dosyalarla başlayan ve kümenizde çalışan bir uygulamayla biten iş akışının tamamını yürütür. Azure Pipelines [yayın işlem hatları](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts) hakkında daha fazla bilgi edinin.

### <a name="create-a-build-pipeline"></a>Derleme işlem hattı oluşturma
Bir web tarayıcısı açın ve [Azure DevOps](https://app.vsaex.visualstudio.com/)’da az önce oluşturduğunuz projeye gidin. 

1. **Derleme ve Yayınlama** sekmesi altında **Derlemeler**’i ve **+Yeni**’yi seçin.  **Azure DevOps Services Git**’i ve **Devam Et**’i seçin.
    
    ![Azure DevOps 'da DevOps git kaynağını seçme](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-source-devops.png)

2. Boş bir işlem hattıyla başlamak için **Şablon seçin**’de **Boş İşlem**’e tıklayın.
    
    ![DevOps 'daki şablon seçeneklerinden boş işlem seçin](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-template-empty-process.png)

3. **Tetikleyiciler**’in altında **Sürekli tümleştirmeyi etkinleştir** tetikleyici durumunu işaretleyerek sürekli tümleştirmeyi etkinleştirin.  Derlemeyi el ile başlatmak için **Kaydet ve kuyruğa al**’ı seçin. 
    
    ![Sürekli Tümleştirme tetikleyicisi durumunu etkinleştir](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-trigger-status-ci.png)

4. Derlemeler gönderme veya iade işlemleriyle de tetiklenir. Derleme ilerleme durumunu denetlemek için **derlemeler** sekmesine geçin.  Derlemeyi başarıyla yürüttüğünü doğruladıktan sonra, uygulamanızı bir kümeye dağıtan bir yayın işlem hattı tanımlamanız gerekir. Derleme işlem hattınızın yanındaki üç noktaya sağ tıklayın ve **Düzenle**’yi seçin.

5.  **Görevler**’de **Aracı kuyruğu** olarak "Hosted" girin.
    
    ![Görevler menüsünde aracı kuyruğu seçin](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-agent-queue-task.png) 

6. **1. Aşama**’da **+** öğesine tıklayın ve bir **NuGet** görevi ekleyin.
    
    ![Aracı kuyruğuna NuGet görevi ekleme](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-add-nuget-task.png)

7. **Gelişmiş**’i genişletin ve **Hedef dizini**’ne `$(Build.SourcesDirectory)\packages` ekleyin. Diğer varsayılan NuGet yapılandırması değerlerini olduğu gibi bırakın.

   ![NuGet geri yükleme görevini yapılandırma](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget-restore-config.png)

8. **1. Aşama**’da **+** seçeneğine tıklayın ve bir **MSBuild** görevi ekleyin.

   ![Aracı kuyruğuna MSBuild görevi ekleme](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-add-msbuild-task.png)

9. **MSBuild Bağımsız Değişkenleri**’ni şöyle değiştirin:

   ```
   /p:CompilerTaskAssemblyFile="Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll"  /p:ASATargetsFilePath="$(Build.SourcesDirectory)\packages\Microsoft.Azure.StreamAnalytics.CICD.1.0.0\build\StreamAnalytics.targets"
   ```

   ![DevOps 'da MSBuild görevini yapılandırma](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-config-msbuild-task.png)

10. **1. Aşama**’da **+** seçeneğine tıklayın ve **Azure Kaynak Grubu Dağıtımı** görevini ekleyin. 
    
    ![Azure Kaynak Grubu Dağıtımı görevi ekleme](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-add-resource-group-deployment.png)

11. **Azure Ayrıntıları**’nı genişletin ve yapılandırmayı aşağıdakilerle doldurun:
    
    |**Ayar**  |**Önerilen değer**  |
    |---------|---------|
    |Abonelik  |  Aboneliğinizi seçin.   |
    |Eylem  |  Kaynak grubu oluşturma veya güncelleştirme   |
    |Kaynak Grubu  |  Kaynak grubu adı girin.   |
    |Şablon  | [Çözüm yolunuz]\bin\Debug\Deploy\\[Projenizin adı].JobTemplate.json   |
    |Şablon parametreleri  | [Çözüm yolunuz]\bin\Debug\Deploy\\[Projenizin adı].JobTemplate.parameters.json   |
    |Şablon parametrelerini geçersiz kılma  | Metin kutusunda geçersiz kılmak için şablon parametrelerini yazın. Örneğin, –storageName fabrikam –adminUsername $(vmusername) -adminPassword $(password) –azureKeyVaultName $(fabrikamFibre). Bu özellik isteğe bağlı olsa da, anahtar parametreleriniz geçersiz kılınmamışsa derlemeniz hatalı bitecektir.    |
    
    ![Azure Kaynak grubu dağıtımı için özellikleri ayarla](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deployment-properties.png)

12. Derleme işlem hattınızı test etmek için **Kaydet ve Kuyruğa Al**’a tıklayın.
    
    ![Derlemeyi DevOps 'a Kaydet ve kuyruğa al](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-save-and-queue-build.png)

### <a name="failed-build-process"></a>Başarısız derleme işlemi
Derleme işlem hattınızın **Azure Kaynak Grubu Dağıtımı** görevinde şablon parametrelerini geçersiz kılmadıysanız, null dağıtım parametreleri nedeniyle hata alabilirsiniz. Derleme işlem hattına dönün ve hatayı gidermek için null parametreleri geçersiz kılın.

   ![DevOps Stream Analytics oluşturma işlemi başarısız oldu](./media/stream-analytics-tools-visual-studio-cicd-vsts/devops-build-process-failed.png)

### <a name="commit-and-push-changes-to-trigger-a-release"></a>Yayını tetiklemek için değişiklikleri işleme ve gönderme
Azure DevOps'da yapılan bazı kod değişikliklerini denetleyerek sürekli tümleştirme işlem hattının çalıştığını doğrulayın.    

Siz kodunuzu yazarken, değişiklikleriniz Visual Studio tarafından otomatik olarak izlenir. Sağ alt kısımdaki durum çubuğunda bekleyen değişiklikler simgesini seçerek, değişiklikleri yerel Git deponuza işleyin.

1. Takım Gezgini'ndeki **Değişiklikler** görünümünde, güncelleştirmenizi açıklayan bir ileti ekleyin ve değişikliklerinizi işleyin.

    ![Visual Studio 'dan depo değişiklikleri yapma](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-commit-changes-visual-studio.png)

2. Yayımlanmamış değişiklikler durum çubuğu simgesini veya Takım Gezgini'nde Eşitleme görünümünü seçin. Azure DevOps’da kodunuzu güncelleştirmek için **Gönder**'i seçin.

    ![Visual Studio 'dan değişiklikleri gönder](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes-visual-studio.png)

Değişikliklerin Azure DevOps Services’a gönderilmesi otomatik olarak derlemeyi tetikler.  Derleme işlem hattı başarıyla tamamlandığında, otomatik olarak bir yayın oluşturulur ve kümede işi güncelleştirme işlemini başlatır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu, akış işini ve tüm ilgili kaynakları silin. İşin silinmesi, iş tarafından kullanılan akış birimlerinin faturalanmasını önler. İşi gelecekte kullanmayı planlıyorsanız, durdurup daha sonra gerektiğinde yeniden başlatabilirsiniz. Bu işi kullanmaya devam etmeyecekseniz aşağıdaki adımları kullanarak bu öğreticiyle oluşturulan tüm kaynakları silin:

1. Azure portalında sol taraftaki menüden, **Kaynak grupları**'na ve ardından oluşturduğunuz kaynağın adına tıklayın.  
2. Kaynak grubu sayfanızda, **Sil**'e tıklayın, metin kutusuna silinecek kaynağın adını yazın ve ardından **Sil**'e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

Visual Studio için Azure Stream Analytics araçlarını kullanarak sürekli tümleştirme ve dağıtım işlemi ayarlama hakkında daha fazla bilgi edinmek için, CI/CD işlem hattını ayarlama makalesiyle devam edin:

> [!div class="nextstepaction"]
> [Stream Analytics araçlarıyla sürekli tümleştirme ve geliştirme](stream-analytics-tools-for-visual-studio-cicd.md)
