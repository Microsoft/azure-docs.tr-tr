---
title: Web hizmetini yeniden eğitme
titleSuffix: ML Studio (classic) - Azure
description: Azure Machine Learning Studio (klasik) sürümünde yeni eğitilen makine öğrenimi modelini kullanmak üzere bir Web hizmetini güncelleştirmeyi öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/14/2019
ms.openlocfilehash: c24eb50688efcf220b26b5a0f352d012876dbab3
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838676"
---
# <a name="retrain-and-deploy-a-machine-learning-model"></a>Makine öğrenimi modelini yeniden eğitme ve dağıtma

Yeniden eğitim, makine öğrenimi modellerinin doğru kalmasını sağlamanın ve kullanılabilir en uygun verilere göre bir yoldur. Bu makalede, bir makine öğrenimi modelinin Studio 'da yeni bir Web hizmeti olarak nasıl yeniden eğitilmesi ve dağıtılması gösterilmektedir (klasik). Klasik bir Web hizmetini yeniden eğiteyorsanız, [Bu nasıl yapılır makalesini görüntüleyin.](retrain-classic-web-service.md)

Bu makalede önceden bir tahmine dayalı Web hizmetiniz olduğunu varsaymış olursunuz. Önceden tahmine dayalı bir Web hizmetiniz yoksa, [burada bir Studio (klasik) Web hizmeti dağıtmayı öğrenin.](deploy-a-machine-learning-web-service.md)

Machine Learning yeni Web hizmeti 'ni yeniden eğitmek ve dağıtmak için şu adımları izleyin:

1. **Yeniden eğitim Web hizmeti** dağıtma
1. **Yeniden eğitme Web hizmetinizi** kullanarak yeni bir model eğitme
1. Yeni modeli kullanmak için mevcut tahmine **dayalı denemenizi** güncelleştirin

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="deploy-the-retraining-web-service"></a>Yeniden eğitme Web hizmetini dağıtma

Yeniden eğitim Web hizmeti, modelinize yeni veriler gibi yeni bir parametre kümesiyle yeniden eğmenize ve daha sonra daha sonra kaydetmenizi sağlar. Bir **Web hizmeti çıkışını** **eğitme modeline**bağladığınızda, eğitim denemesi kullanabileceğiniz yeni bir model çıkarır.

Bir yeniden eğitme Web hizmeti dağıtmak için aşağıdaki adımları kullanın:

1. Bir **Web hizmeti giriş** modülünü veri girişine bağlayın. Genellikle, giriş verilerinizin özgün eğitim verileriyle aynı şekilde işlenmesini sağlamak istersiniz.
1. Bir **Web hizmeti çıkış** modülünü **tren modelinizin**çıktısına bağlayın.
1. Bir **model değerlendirme** modülünüzün varsa, değerlendirme sonuçlarını çıkarmak Için bir **Web hizmeti çıkış** modülü bağlayabilirsiniz
1. Denemenizi çalıştırın.

    Deneme hesabınızı çalıştırdıktan sonra, elde edilen iş akışının aşağıdaki görüntüye benzer olması gerekir:

    ![Sonuç iş akışı](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE04.png)

    Şimdi eğitim deneme deneyimini eğitilen bir model ve model değerlendirme sonuçları veren bir yeniden eğitme Web hizmeti olarak dağıtırsınız.

1. Deneme tuvalinin alt kısmındaki **Web hizmeti ayarla** ' ya tıklayın.
1. **Web hizmetini dağıt [yeni]** öğesini seçin. Azure Machine Learning Web Hizmetleri portalı **Web Hizmeti Dağıt** sayfasında açılır.
1. Web hizmetiniz için bir ad yazın ve bir ödeme planı seçin.
1. **Dağıt**'ı seçin.

## <a name="retrain-the-model"></a>Modeli yeniden eğitme

Bu örnekte, yeniden eğitim uygulaması oluşturmak C# için kullanıyoruz. Bu görevi gerçekleştirmek için Python veya R örnek kodunu da kullanabilirsiniz.

Yeniden eğitim API 'Lerini çağırmak için aşağıdaki adımları kullanın:

1. Visual Studio C# 'da bir konsol uygulaması oluşturun: **Yeni** > **Project** > **Visual C#**  > **Windows Klasik Masaüstü** > **konsol uygulaması (.NET Framework)** .
1. Machine Learning Web Hizmetleri portalında oturum açın.
1. Üzerinde çalıştığınız Web hizmetine tıklayın.
1. **Tüketme**' ye tıklayın.
1. Kullanım **sayfasının en** altında, **örnek kod** bölümünde **toplu işlem**' e tıklayın.
1. Toplu yürütme için C# örnek kodu kopyalayın ve program.cs dosyasına yapıştırın. Ad alanının bozulmadan kaldığından emin olun.

Açıklamalarda belirtilen Microsoft. AspNet. WebApi. Client NuGet paketini ekleyin. Başvuruyu Microsoft. WindowsAzure. Storage. dll ' ye eklemek için [Azure Storage Hizmetleri için istemci kitaplığı](https://www.nuget.org/packages/WindowsAzure.Storage)'nı yüklemeniz gerekebilir.

Aşağıdaki ekran görüntüsünde, Azure Machine Learning Web Hizmetleri portalındaki **kullanma sayfası gösterilmektedir** .

![Tüketme sayfası](media/retrain-machine-learning/machine-learning-retrain-models-consume-page.png)

### <a name="update-the-apikey-declaration"></a>Apikey bildirimini güncelleştirme

**Apikey** bildirimini bulun:

    const string apiKey = "abc123"; // Replace this with the API key for the web service

Kullanım sayfasının **temel tüketim bilgileri** bölümünde, birincil anahtarı bulun ve **apikey** bildirimine kopyalayın.

### <a name="update-the-azure-storage-information"></a>Azure depolama bilgilerini güncelleştirme

BES örnek kodu, yerel sürücüden bir dosyayı (örneğin, "C:\temp\censusınput,CSV") Azure depolama 'ya yükler, işler ve sonuçları Azure depolama 'ya geri yazar.

1. Azure portalda oturum açma
1. Sol gezinti sütununda, **diğer hizmetler**' e tıklayın, **depolama hesapları**' nı arayın ve seçin.
1. Depolama hesapları listesinden, geri çekme modelini depolamak için bir tane seçin.
1. Sol gezinti sütununda **erişim tuşları**' na tıklayın.
1. **Birincil erişim anahtarını**kopyalayın ve kaydedin.
1. Sol gezinti sütununda, **Bloblar**' a tıklayın.
1. Var olan bir kapsayıcıyı seçin veya yeni bir kapsayıcı oluşturun ve adı kaydedin.

*StorageAccountName*, *Storageaccountkey*ve *storagecontainername* bildirimlerini bulun ve portaldan kaydettiğiniz değerleri güncelleştirin.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

Ayrıca, giriş dosyasının kodda belirttiğiniz konumda kullanılabilir olduğundan emin olmanız gerekir.

### <a name="specify-the-output-location"></a>Çıkış konumunu belirtin

Istek yükünde çıkış konumunu belirttiğinizde, ' ın *Relatıvelocation* 'da belirtilen uzantısının `ilearner`olarak belirtilmesi gerekir.

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

Yeniden eğitim çıkışının bir örneği aşağıda verilmiştir:

![Yeniden eğitim çıkışı](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE06.png)

### <a name="evaluate-the-retraining-results"></a>Yeniden eğitim sonuçlarını değerlendirin

Uygulamayı çalıştırdığınızda, çıkış, değerlendirme sonuçlarına erişmek için gerekli olan URL ve paylaşılan erişim imzaları belirtecini içerir.

*Output2* için çıkış sonuçlarından *baselocation*, *Relatıvelocation*ve *sasblobtoken* ' ı birleştirerek ve tüm URL 'yi tarayıcı adres çubuğuna yapıştırarak, geri çekme modelinin performans sonuçlarını görebilirsiniz.

Yeni eğitilen modelin mevcut olandan daha iyi bir performans gerçekleştirip gerçekleştirmediğini belirleme sonuçlarını inceleyin.

*Baselocation*, *relatıvelocation*ve *sasblobtoken* 'ı çıkış sonuçlarından kaydedin.

## <a name="update-the-predictive-experiment"></a>Tahmine dayalı denemeyi güncelleştirme

### <a name="sign-in-to-azure-resource-manager"></a>Azure Resource Manager oturum açın

İlk olarak, [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 'Ini kullanarak PowerShell ortamının içinden Azure hesabınızda oturum açın.

### <a name="get-the-web-service-definition-object"></a>Web hizmeti tanım nesnesini Al

Ardından, [Get-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservice) cmdlet 'Ini çağırarak Web hizmeti tanım nesnesini alın.

    $wsd = Get-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Var olan bir Web hizmetinin kaynak grubu adını öğrenmek için, aboneliğinizdeki Web hizmetlerini göstermek üzere herhangi bir parametre olmadan Get-AzMlWebService cmdlet 'ini çalıştırın. Web hizmeti ' ni bulun ve Web hizmeti KIMLIĞINE bakın. Kaynak grubunun adı, *ResourceGroups* öğesinden hemen sonra, kimliğin dördüncü öğesidir. Aşağıdaki örnekte, kaynak grubu adı varsayılan-Machinöğrenim-Güneydoğu ABD ' dir.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

Alternatif olarak, var olan bir Web hizmetinin kaynak grubu adını öğrenmek için, Azure Machine Learning Web Hizmetleri portalında oturum açın. Web hizmetini seçin. Kaynak grubu adı, Web hizmeti URL 'sinin, *ResourceGroups* öğesinden hemen sonra beşinci öğesidir. Aşağıdaki örnekte, kaynak grubu adı varsayılan-Machinöğrenim-Güneydoğu ABD ' dir.

    https://services.azureml.net/subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237

### <a name="export-the-web-service-definition-object-as-json"></a>Web hizmeti tanım nesnesini JSON olarak dışarı aktarma

Eğitilen modelin tanımını yeni eğitilen modeli kullanacak şekilde değiştirmek için, önce [Export-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/export-azmlwebservice) cmdlet 'INI kullanarak JSON biçimli bir dosyaya dışarı aktarmanız gerekir.

    Export-AzMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

### <a name="update-the-reference-to-the-ilearner-blob"></a>İlearner blob başvurusunu güncelleştirme

Varlıklarda [eğitilen model] öğesini bulun, *LocationInfo* düğümündeki *URI* değerini ilearner blob URI 'siyle güncelleştirin. URI, BES yeniden eğitim çağrısının çıktısından *Baselocation* ve *Relatıvelocation* birleştirilerek oluşturulur.

     "asset3": {
        "name": "Retrain Sample [trained model]",
        "type": "Resource",
        "locationInfo": {
          "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
        },
        "outputPorts": {
          "Results dataset": {
            "type": "Dataset"
          }
        }
      },

### <a name="import-the-json-into-a-web-service-definition-object"></a>JSON 'ı bir Web hizmeti tanım nesnesine içeri aktarma

Değiştirilen JSON dosyasını, tahmine dayalı denemeyi güncelleştirmek için kullanabileceğiniz bir Web hizmeti tanım nesnesine geri dönüştürmek için [Import-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/import-azmlwebservice) cmdlet 'ini kullanın.

    $wsd = Import-AzMlWebService -InputFile "C:\temp\mlservice_export.json"

### <a name="update-the-web-service"></a>Web hizmetini güncelleştirme

Son olarak, tahmine dayalı denemesini güncelleştirmek için [Update-azmlwebservice](https://docs.microsoft.com/powershell/module/az.machinelearning/update-azmlwebservice) cmdlet 'ini kullanın.

    Update-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

## <a name="next-steps"></a>Sonraki adımlar

Web hizmetlerini yönetme veya birden çok denemeleri çalıştırmasını izleme hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

* [Web Hizmetleri portalını keşfet](manage-new-webservice.md)
* [Deneme yinelemelerini yönetme](manage-experiment-iterations.md)
