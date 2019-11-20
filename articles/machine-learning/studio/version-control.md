---
title: Uygulama yaşam döngüsü yönetimi
titleSuffix: ML Studio (classic) - Azure
description: Azure Machine Learning Studio klasik sürümünde uygulama yaşam döngüsü yönetimi en iyi yöntemlerini uygulayın
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.date: 10/27/2016
ms.openlocfilehash: 2dd401918edc0b2d08390b1012efbf44fe2be1e9
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839428"
---
# <a name="application-lifecycle-management-in-azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio 'de uygulama yaşam döngüsü yönetimi (klasik)
Azure Machine Learning Studio (klasik), Azure bulut platformunda çalışır durumda olan Machine Learning denemeleri geliştirmeye yönelik bir araçtır. Visual Studio IDE ve ölçeklenebilir bulut hizmeti tek bir platformda birleştirilmiştir. Standart uygulama yaşam döngüsü yönetimi (ALM) uygulamalarını, çeşitli varlıkların sürümlerini otomatik yürütmeye ve dağıtıma, klasik Azure Machine Learning Studio, klasik sürümüne ekleyebilirsiniz. Bu makalede bazı seçenekler ve yaklaşımların bazıları açıklanmaktadır.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="versioning-experiment"></a>Sürüm oluşturma denemesi
Denemeleri sürümünüzü kullanmanın iki yolu vardır. Yerleşik çalıştırma geçmişine güvenebilirsiniz veya denemeyi dışarıdan yönetmek için bir JSON biçiminde dışarı aktarabilirsiniz. Her yaklaşım, profesyonelleri ve dezavantajlarıyla birlikte gelir.

### <a name="experiment-snapshots-using-run-history"></a>Çalıştırma geçmişi kullanarak anlık görüntüleri deneme
Azure Machine Learning Studio Learning denemesinin klasik sürümünün yürütme modelinde, deneme düzenleyicide **Çalıştır** ' ı her tıklattığınızda deneme için sabit bir anlık görüntü gönderilir. Bu anlık görüntü listesini görüntülemek için, deneme düzenleyici görünümündeki komut çubuğunda **Çalıştır geçmişi** ' ne tıklayın.

![Çalışma geçmişi düğmesi](./media/version-control/runhistory.png)

Daha sonra, denemeyi çalıştırmak için bir deneme adı ve anlık görüntü alındığı sırada deneme adına tıklayarak anlık görüntüyü kilitli modda açabilirsiniz. Yalnızca listedeki ilk öğenin, geçerli denemeyi temsil eden, düzenlenebilir bir durumda olduğunu fark edersiniz. Ayrıca, her bir anlık görüntünün, tamamlandı (kısmi çalıştırma), başarısız, başarısız (kısmi çalıştırma) veya taslak dahil çeşitli durum durumlarında de yer aldığı fark edebilirsiniz.

![Çalışma geçmişi listesi](./media/version-control/runhistorylist.png)

Açıldıktan sonra, anlık görüntü denemesini yeni bir deneme olarak kaydedebilir ve sonra değiştirebilirsiniz. Deneme anlık görüntüleriniz, eğitilen modeller, dönüşümler veya güncelleştirilmiş sürümlerin bulunduğu veri kümeleri gibi varlıklar içeriyorsa, anlık görüntü çekilirken, anlık görüntü özgün sürüme başvuruları korur. Kilitli anlık görüntüyü yeni bir deneme olarak kaydederseniz, Azure Machine Learning Studio klasik sürümü bu varlıkların daha yeni bir sürümünün varlığını algılar ve yeni deneyde onları otomatik olarak güncelleştirir.

Denemeyi silerseniz, bu denemenin tüm anlık görüntüleri silinir.

### <a name="exportimport-experiment-in-json-format"></a>JSON biçiminde dışa/içeri aktarma denemesi
Çalışma geçmişi anlık görüntüleri, çalıştırmanın her gönderildiği her seferinde klasik sürümünde Azure Machine Learning Studio, denemenin sabit bir sürümünü tutar. Ayrıca, denemenin yerel bir kopyasını kaydedebilir ve Team Foundation Server gibi en sevdiğiniz kaynak denetim sistemine iade edebilir ve daha sonra bu yerel dosyadaki bir denemeyi yeniden oluşturabilirsiniz. Bunu gerçekleştirmek için [Azure Machine Learning PowerShell](https://aka.ms/amlps) [*Commandexport-amlexperimentgraph*](https://github.com/hning86/azuremlps#export-amlexperimentgraph) ve [*Import-amlexperimentgraph*](https://github.com/hning86/azuremlps#import-amlexperimentgraph) ' i kullanabilirsiniz.

JSON dosyası, bir veri kümesi veya eğitilen model gibi çalışma alanındaki varlıklara başvuru içerebilen deneme grafiğinin metinsel bir gösterimidir. Varlığın serileştirilmiş bir sürümünü içermez. JSON belgesini yeniden çalışma alanına aktarmaya çalışırsanız, başvurulan varlıkların denemeye başvuruda bulunulan varlık kimlikleri ile zaten mevcut olması gerekir. Aksi takdirde, içeri aktarılan denemenize erişemezsiniz.

## <a name="versioning-trained-model"></a>Eğitilen model sürümü
Klasik Azure Machine Learning Studio eğitilen bir model, iLearner dosyası (`.iLearner`) olarak bilinen bir biçimde serileştirilir ve çalışma alanıyla ilişkili Azure Blob depolama hesabında depolanır. İLearner dosyasının bir kopyasını almanın bir yolu yeniden eğitim API 'sidir. [Bu makalede](/azure/machine-learning/studio/retrain-machine-learning-model) , yeniden eğitim API 'sinin nasıl çalıştığı açıklanır. Üst düzey adımlar:

1. Eğitim denemenizi ayarlayın.
2. Eğitim modeli modülüne bir Web hizmeti çıkış bağlantı noktası ya da model Hyperparameter ayarlama veya R modeli oluşturma gibi eğitilen modeli üreten modüle ekleyin.
3. Eğitim denemenizi çalıştırın ve model eğitimi Web hizmeti olarak dağıtın.
4. Eğitim Web hizmetinin BES uç noktasını çağırın ve depolanacağı istenen iLearner dosya adını ve BLOB depolama hesabı konumunu belirtin.
5. BES çağrısı bittikten sonra üretilen iLearner dosyasını toplama.

İlearner dosyasını almanın bir başka yolu da PowerShell komutunu [*Download-amlexperimentnodeoutput*](https://github.com/hning86/azuremlps#download-amlexperimentnodeoutput)yoludur. Modeli programlı bir şekilde yeniden eğitmeniz gerekmeden iLearner dosyasının bir kopyasını almak istiyorsanız bu daha kolay olabilir.

Eğitilen modeli içeren iLearner dosyasına sahip olduktan sonra kendi sürüm oluşturma stratejinizi kullanabilirsiniz. Strateji, adlandırma kuralı olarak bir ön/sonek uygulamak ve yalnızca IComparer dosyasını blob depolamada bırakmak veya sürüm denetim sisteminize kopyalamak/içeri aktarmak kadar kolay olabilir.

Kaydedilen iLearner dosyası daha sonra dağıtılan Web Hizmetleri aracılığıyla Puanlama için kullanılabilir.

## <a name="versioning-web-service"></a>Web hizmeti sürümü oluşturma
Azure Machine Learning Studio (klasik) denemenizin iki tür Web Hizmeti dağıtabilirsiniz. Klasik Web hizmeti, çalışma alanının yanı sıra deneme ile sıkı bir şekilde bağlanmış. Yeni Web hizmeti Azure Resource Manager çerçevesini kullanır ve artık özgün denemesiz veya çalışma alanıyla birlikte bulunmaz.

### <a name="classic-web-service"></a>Klasik Web hizmeti
Klasik bir Web hizmetinin sürümünü oluşturmak için Web hizmeti uç noktası yapısı 'ndan yararlanabilirsiniz. Tipik bir akış aşağıda verilmiştir:

1. Tahmine dayalı Deneyinizden, varsayılan bir uç nokta içeren yeni bir klasik Web hizmeti dağıtırsınız.
2. Deneme/eğitim modelinin geçerli sürümünü kullanıma sunan EP2 adlı yeni bir uç nokta oluşturursunuz.
3. Tahmine dayalı denemenize ve eğitilen modelinize geri dönerek devam edersiniz.
4. Tahmine dayalı denemeyi yeniden dağıtırsınız ve ardından varsayılan uç noktayı günceltırsınız. Ancak bu, EP2 ' i değiştirmez.
5. Deneme ve eğitim modelinin yeni sürümünü sunan EP3 adlı ek bir uç nokta oluşturursunuz.
6. Gerekirse adım 3 ' e geri dönün.

Zaman içinde, aynı Web hizmetinde oluşturulmuş çok sayıda uç nokta olabilir. Her uç nokta, eğitilen modelin zaman içindeki bir yerinde sürümünü içeren deneyin zaman içinde bir nokta kopyasını temsil eder. Daha sonra, hangi uç noktanın çağrılacağını belirlemek için dış mantığı kullanabilirsiniz. Bu, Puanlama çalıştırması için eğitilen bir modelin bir sürümünü seçmenin etkili olması anlamına gelir.

Ayrıca, aynı etkiyi elde etmek için birçok özdeş Web hizmeti uç noktası oluşturabilir ve ardından iLearner dosyasının farklı sürümlerini uç noktaya indirebilirsiniz. [Bu makalede, bunun](create-models-and-endpoints-with-powershell.md) nasıl yapılacağı hakkında daha ayrıntılı bilgi verilmektedir.

### <a name="new-web-service"></a>Yeni Web hizmeti
Yeni bir Azure Resource Manager tabanlı Web hizmeti oluşturursanız, uç nokta yapısı artık kullanılamaz. Bunun yerine, [Export-AmlWebServiceDefinitionFromExperiment](https://github.com/hning86/azuremlps#export-amlwebservicedefinitionfromexperiment) PowerShell commandlet kullanarak veya [*Export-azmlwebservice*](https://docs.microsoft.com/powershell/module/az.machinelearning/export-azmlwebservice) kullanarak, tahmine dayalı denemenizin içinden Web hizmeti tanım (WSD) dosyaları oluşturabilirsiniz. Dağıtım Kaynak Yöneticisi tabanlı bir Web hizmetinden PowerShell komutunu.

İçe aktarılmış WSD dosyası ve sürüm denetimine sahip olduktan sonra, WSD 'yi farklı bir Azure bölgesindeki farklı bir Web hizmeti planına yeni bir Web hizmeti olarak da dağıtabilirsiniz. Yalnızca uygun depolama hesabı yapılandırmasını ve yeni Web hizmeti planı KIMLIĞINI girdiğinizden emin olun. Farklı iLearner dosyalarında düzeltme eki uygulamak için, WSD dosyasını değiştirebilir ve eğitilen modelin konum başvurusunu güncelleştirebilir ve yeni bir Web hizmeti olarak dağıtabilirsiniz.

## <a name="automate-experiment-execution-and-deployment"></a>Deneme yürütme ve dağıtımı otomatikleştirin
ALM 'nin önemli bir yönü, uygulamanın yürütme ve dağıtım sürecini otomatikleştirebilmelidir. Azure Machine Learning Studio klasik sürümünde, [PowerShell modülünü](https://aka.ms/amlps)kullanarak bunu yapabilirsiniz. Aşağıda, [Azure Machine Learning Studio (klasik) PowerShell modülünü](https://aka.ms/amlps)kullanarak standart bir ALM otomatik yürütme/dağıtım işlemiyle ilgili uçtan uca adımlara örnek verilmiştir. Her bir adım, bu adımı gerçekleştirmek için kullanabileceğiniz bir veya daha fazla PowerShell commandine bağlıdır.

1. [Bir veri kümesini karşıya yükleyin](https://github.com/hning86/azuremlps#upload-amldataset).
2. [Çalışma alanındaki veya](https://github.com/hning86/azuremlps#copy-amlexperiment) galerideki bir eğitim denemesini çalışma alanına ya da [Galeriden](https://github.com/hning86/azuremlps#copy-amlexperimentfromgallery) [dışarı](https://github.com/hning86/azuremlps#export-amlexperimentgraph) [aktarın](https://github.com/hning86/azuremlps#import-amlexperimentgraph) .
3. Eğitim [denemesindeki veri kümesini güncelleştirin](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) .
4. [Eğitim deneme deneyimini çalıştırın](https://github.com/hning86/azuremlps#start-amlexperiment).
5. [Eğitilen modeli yükseltin](https://github.com/hning86/azuremlps#promote-amltrainedmodel).
6. Tahmine [dayalı bir](https://github.com/hning86/azuremlps#copy-amlexperiment) denemeyi çalışma alanına kopyalayın.
7. Tahmine dayalı deneyde [eğitilen modeli güncelleştirin](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) .
8. Tahmine [dayalı denemeyi çalıştırın](https://github.com/hning86/azuremlps#start-amlexperiment).
9. Tahmine dayalı deneyden [bir Web hizmeti dağıtın](https://github.com/hning86/azuremlps#new-amlwebservice) .
10. Web hizmeti [RR 'leri](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) veya [bes](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint) uç noktasını test edin.

## <a name="next-steps"></a>Sonraki adımlar
* [Azure Machine Learning Studio (klasik) PowerShell](https://aka.ms/amlps) modülünü INDIRIN ve ALM görevlerinizi otomatikleştirmeye başlayın.
* PowerShell ve yeniden eğitme API aracılığıyla [yalnızca tek bir deneme kullanarak çok sayıda ml modeli oluşturmayı ve yönetmeyi](create-models-and-endpoints-with-powershell.md) öğrenin.
* [Azure Machine Learning Web hizmetlerini dağıtma](deploy-a-machine-learning-web-service.md)hakkında daha fazla bilgi edinin.
