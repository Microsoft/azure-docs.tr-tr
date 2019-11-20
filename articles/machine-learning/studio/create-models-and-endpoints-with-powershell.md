---
title: Bir model için birden fazla uç nokta oluşturma
titleSuffix: ML Studio (classic) - Azure
description: Aynı algoritmaya ancak farklı eğitim veri kümelerine sahip birden çok Machine Learning modeli ve Web hizmeti uç noktaları oluşturmak için PowerShell 'i kullanın.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 04/04/2017
ms.openlocfilehash: d7999488302098980ff9306836ec9d54aceac3be
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837850"
---
# <a name="use-powershell-to-create-studio-classic-models-and-web-service-endpoints-from-one-experiment"></a>Bir deneyden Studio (klasik) modeller ve Web hizmeti uç noktaları oluşturmak için PowerShell 'i kullanma

Ortak bir makine öğrenimi sorunu: aynı eğitim iş akışına sahip birçok model oluşturmak ve aynı algoritmayı kullanmak istiyorsunuz. Ancak, giriş olarak farklı eğitim veri kümelerine sahip olmalarını istiyorsunuz. Bu makalede, yalnızca tek bir deneme kullanılarak bu Azure Machine Learning Studio (klasik) ölçekte nasıl yapılacağı gösterilmektedir.

Örneğin, küresel bir bisiklet Kiralık frankii işletmenizin olduğunu varsayalım. Geçmiş verilere göre Kiralama talebini tahmin etmek için bir regresyon modeli oluşturmak istiyorsunuz. Dünyanın dört bir yanındaki 1.000 Kiralama konumunuz var ve her konum için bir veri kümesi topladık. Bunlar, her konuma özgü tarih, saat, hava durumu ve trafik gibi önemli özellikleri içerir.

Tüm konumlarda tüm veri kümelerinin birleştirilmiş bir sürümünü kullanarak modelinizi bir kez eğitebilirsiniz. Ancak, konumlarınızın her biri benzersiz bir ortama sahiptir. Bu nedenle, her konum için veri kümesini kullanarak gerileme modelinizi ayrı olarak eğitebilmek daha iyi bir yaklaşım olabilir. Bu şekilde, eğitilen her bir model farklı mağaza boyutları, hacim, coğrafya, popülasyon, Bisiklet kullanımı kolay trafik ortamı ve daha fazlasını hesaba götürebileceği.

Bu en iyi yaklaşım olabilir ancak her biri benzersiz bir konum temsil eden Azure Machine Learning Studio klasik sürümünde 1.000 eğitim denemeleri oluşturmak istemezsiniz. Sürekli olarak bir görev olmasının yanı sıra, her bir deneyin eğitim veri kümesi dışında aynı bileşenlere sahip olması gerektiğinden de verimsiz bir şekilde görünür.

Neyse ki, [Azure Machine Learning Studio (klasik) yeniden eğitim API 'sini](/azure/machine-learning/studio/retrain-machine-learning-model) kullanarak ve görevi [Azure Machine Learning Studio (klasik) PowerShell](powershell-module.md)ile otomatikleştirerek bunu yapabilirsiniz.

> [!NOTE]
> Örneğin, örneğinizi daha hızlı bir şekilde çalıştırmak için 1.000 arasındaki konum sayısını 10 ' a küçültün. Ancak aynı ilkeler ve yordamlar 1.000 konumları için de geçerlidir. Ancak, 1.000 veri kümesinden eğitme yapmak istiyorsanız, aşağıdaki PowerShell betiklerini paralel olarak çalıştırmak isteyebilirsiniz. Bunun nasıl yapılacağı, bu makalenin kapsamı dışındadır, ancak Internet 'te PowerShell çoklu iş parçacığı örneklerini bulabilirsiniz.  
> 
> 

## <a name="set-up-the-training-experiment"></a>Eğitim denemesini ayarlama
[Cortana Intelligence Gallery](https://gallery.azure.ai)olan örnek [eğitim denemenizi](https://gallery.azure.ai/Experiment/Bike-Rental-Training-Experiment-1) kullanın. Bu denemeyi [Azure Machine Learning Studio (klasik)](https://studio.azureml.net) çalışma alanınızda açın.

> [!NOTE]
> Bu örnekle birlikte takip edebilmek için, ücretsiz bir çalışma alanı yerine standart bir çalışma alanı kullanmak isteyebilirsiniz. Her müşteri için bir uç nokta oluşturursunuz-toplam 10 uç nokta için ve ücretsiz çalışma alanı 3 uç noktayla sınırlı olduğundan standart bir çalışma alanı gerektirir.
> 
> 

Deneme, bir Azure Storage hesabından *customer001. csv* veri kümesini içeri aktarmak Için **veri alma** modülünü kullanır. Eğitim veri kümelerini tüm Bisiklet Kiralama konumlarından topladığınızı ve *rentalloc001. csv* ' den *rentalloc10. csv*' ye kadar dosya adlarıyla aynı BLOB depolama konumunda depoladığınız varsayıyoruz.

![Okuyucu modülü verileri bir Azure blobundan içeri aktarır](./media/create-models-and-endpoints-with-powershell/reader-module.png)

**Eğitim modeli** modülüne bir **Web hizmeti çıkış** modülü eklendiğini unutmayın.
Bu deneme bir Web hizmeti olarak dağıtıldığında, bu çıktı ile ilişkili uç nokta, eğitilen modeli bir. ilearner dosyası biçiminde döndürür.

Ayrıca, **veri alma** modülünün kullandığı URL 'yi tanımlayan bir Web hizmeti parametresi ayarlamayacağınızı unutmayın. Bu, her konum için modeli eğitmek üzere bireysel eğitim veri kümelerini belirtmek için parametresini kullanmanıza olanak sağlar.
Bunu yaptığınız başka yollar vardır. Bir SQL Azure veritabanından veri almak için bir Web hizmeti parametresiyle birlikte bir SQL sorgusu kullanabilirsiniz. Ya da Web hizmetine bir veri kümesi geçirmek için **Web hizmeti giriş** modülünü kullanabilirsiniz.

![Eğitilen model modülü bir Web hizmeti çıkış modülüne çıkış verir](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

Şimdi, eğitim veri kümesi olarak *rental001. csv* varsayılan değerini kullanarak bu eğitim denemesini çalıştıralım. **Değerlendirme** modülünün çıkışını (çıktıyı tıklatın ve **Görselleştir**' i seçin) görürseniz, *AUC* = 0,91 performans performansınızı görebilirsiniz. Bu noktada, bu eğitim denemenizin dışına bir Web hizmeti dağıtmaya hazırsınız demektir.

## <a name="deploy-the-training-and-scoring-web-services"></a>Eğitim ve Puanlama Web hizmetlerini dağıtma
Eğitim Web hizmetini dağıtmak için deneme tuvalinin altındaki **Web hizmeti ayarla** düğmesine tıklayın ve **Web Hizmeti Dağıt**' ı seçin. Bu Web hizmetini "Bisiklet Kiralama eğitimi" olarak çağırın.

Artık Puanlama Web hizmetini dağıtmanız gerekir.
Bunu yapmak için, tuvalin altında **Web hizmeti ayarla** ' ya tıklayın ve tahmine **dayalı Web hizmeti**' ni seçin. Bu, bir Puanlama denemesi oluşturur.
Web hizmeti olarak çalışmasını sağlamak için birkaç küçük ayar yapmanız gerekir. Giriş verilerinden "sayisi" etiket sütununu kaldırın ve çıktıyı yalnızca örnek kimliği ve buna karşılık gelen tahmin edilen değerle sınırlayın.

Bu işi kaydetmek için, önceden hazırlanmış olan galeride tahmine [dayalı](https://gallery.azure.ai/Experiment/Bike-Rental-Predicative-Experiment-1) denemeyi açabilirsiniz.

Web hizmetini dağıtmak için, tahmine dayalı denemeyi çalıştırın, sonra tuvalin altındaki **Web Hizmeti Dağıt** düğmesine tıklayın. Puanlama Web hizmeti "Bisiklet Kiralama Puanlama" olarak adlandırın.

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>PowerShell ile 10 özdeş Web hizmeti uç noktası oluşturma
Bu Web hizmeti varsayılan bir uç nokta ile birlikte gelir. Ancak güncelleştirilemediğinden, varsayılan uç nokta ile ilgilenmiş olursunuz. Yapmanız gerekenler her konum için bir tane olmak üzere 10 ek uç nokta oluşturmaktır. Bunu PowerShell ile yapabilirsiniz.

İlk olarak, PowerShell ortamını ayarlarsınız:

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and is properly set to point to the valid Workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

Ardından, aşağıdaki PowerShell komutunu çalıştırın:

    # Create 10 endpoints on the scoring web service.
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpointName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

Artık 10 uç noktası oluşturdunuz ve hepsi, *customer001. csv*üzerinde eğitilen eğitilen modeli içeriyor. Bunları Azure portal görüntüleyebilirsiniz.

![Portalda eğitilen modellerin listesini görüntüleyin](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>Uç noktaları PowerShell kullanarak ayrı eğitim veri kümeleri kullanacak şekilde güncelleştirme
Sonraki adım, her bir müşterinin bireysel verilerinde benzersiz olarak eğitilen modellerle uç noktaları güncelleştirmedir. Ancak öncelikle bu modelleri **Bisiklet Kiralama eğitimi** Web hizmetinden üretmeniz gerekir. **Bisiklet Kiralama eğitimi** Web hizmetine geri dönelim. 10 farklı model üretmek için BES bitiş noktası 10 kez 10 farklı eğitim veri kümesi ile çağırmanız gerekir. Bunu yapmak için **ınovkeamlwebservicebesendpoint** PowerShell cmdlet 'ini kullanın.

Ayrıca, `$configContent`' ye blob Storage hesabınızın kimlik bilgilerini sağlamanız gerekir. Yani, alanlar `AccountName`, `AccountKey`ve `RelativeLocation`. `AccountName`, **Azure Portal** (*depolama* sekmesi) görüldüğü gibi hesap adlarınızın biri olabilir. Bir depolama hesabına tıkladığınızda, en alttaki **erişim tuşlarını Yönet** düğmesine basılarak ve *birincil erişim anahtarı*kopyalanırken `AccountKey` bulunabilir. `RelativeLocation`, depolama alanı ile ilgili yeni bir modelin depolanacağı yoldur. Örneğin, aşağıdaki betikteki yol `hai/retrain/bike_rental/` `hai`adlı bir kapsayıcıya işaret eder ve `/retrain/bike_rental/` alt klasörlerdir. Şu anda, Portal Kullanıcı arabirimi aracılığıyla alt klasör oluşturamazsınız, ancak bunu yapmanıza izin veren [birkaç Azure depolama araştırması](../../storage/common/storage-explorers.md) vardır. Yeni eğitilen modelleri (. iLearner dosyaları) şu şekilde depolamak için depolama ortamınızda yeni bir kapsayıcı oluşturmanız önerilir: depolama sayfanızda, alttaki **Ekle** düğmesine tıklayın ve `retrain`adlandırın. Özet olarak, aşağıdaki betikte gerekli değişiklikler `AccountName`, `AccountKey`ve `RelativeLocation` (:`"retrain/model' + $seq + '.ilearner"`) ile ilgilidir.

    # Invoke the retraining API 10 times
    # This is the default (and the only) endpoint on the training web service
    $trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
    $submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
    $apiKey = $trainingSvcEp.PrimaryKey;
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
        $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
        Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
        Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
    }

> [!NOTE]
> BES uç noktası bu işlem için desteklenen tek moddur. RR 'ler, eğitilen modeller üretmek için kullanılamaz.
> 
> 

Yukarıda gördüğünüz gibi, 10 farklı BES iş yapılandırması JSON dosyası oluşturmak yerine, bunun yerine yapılandırma dizesini dinamik olarak oluşturursunuz. Ardından, **ınvokeamlwebservcebesendpoint** cmdlet 'Inin *jobconfigstring* parametresine akışı yapın. Gerçekten diskte kopya tutmaya gerek yoktur.

Her şey iyi olursa bir süre sonra, Azure depolama hesabınızda *model001. ilearner* 'den *model010. ilearner*'e kadar 10. ilearner dosyası görmeniz gerekir. Artık **Patch-AmlWebServiceEndpoint** PowerShell cmdlet 'ini kullanarak bu modellerle 10 Puanlama Web hizmeti uç noktasını güncelleştirmeye hazırsınız. Yalnızca programlı olarak daha önce oluşturduğunuz varsayılan olmayan uç noktalara yayacağınızdan emin olabilirsiniz.

    # Patch the 10 endpoints with respective .ilearner models
    $baseLoc = 'http://bostonmtc.blob.core.windows.net/'
    $sasToken = '<my_blob_sas_token>'
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
        Write-Host ('Patching endpoint ' + $endpointName + '...');
        Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
    }

Bu, oldukça hızlı çalışmalıdır. Yürütme tamamlandığında, 10 tahmine dayalı Web hizmeti uç noktası başarıyla oluşturdunuz. Her biri, tek bir eğitim denemesindeki bir kiralık konuma özgü veri kümesi üzerinde benzersiz şekilde eğitilen bir model içerir. Bunu doğrulamak için, **ınvokeamlwebservicerrsendpoint** cmdlet 'ini kullanarak bu uç noktaları çağırmayı deneyebilirsiniz ve bunları aynı giriş verileriyle birlikte sağlayabilirsiniz. Modeller farklı eğitim kümeleriyle eğitildiğinden, farklı tahmin sonuçları görmeyi beklemelisiniz.

## <a name="full-powershell-script"></a>Tam PowerShell betiği
Tam kaynak kodu listesi aşağıdadır:

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and properly set to point to the valid workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

    # Create 10 endpoints on the scoring web service
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpontName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

    # Invoke the retraining API 10 times to produce 10 regression models in .ilearner format
    $trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
    $submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
    $apiKey = $trainingSvcEp.PrimaryKey;
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
        $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
        Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
        Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
    }

    # Patch the 10 endpoints with respective .ilearner models
    $baseLoc = 'http://bostonmtc.blob.core.windows.net/'
    $sasToken = '?test'
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
        Write-Host ('Patching endpoint ' + $endpointName + '...');
        Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
    }
