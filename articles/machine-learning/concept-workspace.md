---
title: Çalışma alanı nedir?
titleSuffix: Azure Machine Learning
description: Çalışma alanı Azure Machine Learning için en üst düzey kaynaktır. Günlükler, ölçümler, çıktılar ve betiklerinizin bir anlık görüntüsü ile tüm eğitim çalıştırmaları geçmişini tutar.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 09/22/2020
ms.openlocfilehash: 215da0e38045a2e66a4a11b54204c26e7720815c
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719070"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>Azure Machine Learning çalışma alanı nedir?

Çalışma alanı, Azure Machine Learning için en üst düzey kaynaktır ve Azure Machine Learning kullandığınızda oluşturduğunuz tüm yapıtlarla çalışmak üzere merkezi bir yer sağlar.  Çalışma alanı; Günlükler, ölçümler, çıktılar ve betiklerinizin bir anlık görüntüsü dahil olmak üzere tüm eğitim çalıştırmaları geçmişini tutar. Hangi eğitim çalıştırmasının en iyi modeli ürettiğini öğrenmek için bu bilgileri kullanın.  

İstediğiniz bir modele sahip olduktan sonra, çalışma alanına kayıt yaptırmış olursunuz. Daha sonra Azure Container Instances, Azure Kubernetes hizmetine dağıtmak için kayıtlı model ve Puanlama betiklerini ve REST tabanlı HTTP uç noktası olarak bir alan ile programlanabilir kapı dizisine (FPGA) dağıtım yapmak için kullanılır. Modeli bir Azure IoT Edge cihazına modül olarak da dağıtabilirsiniz.

## <a name="taxonomy"></a>Sınıflandır 

Çalışma alanının bir sınıflandırması aşağıdaki diyagramda gösterilmiştir:

[![Çalışma alanı sınıflandırması](./media/concept-workspace/azure-machine-learning-taxonomy.png)](./media/concept-workspace/azure-machine-learning-taxonomy.png#lightbox)

Diyagramda bir çalışma alanının aşağıdaki bileşenleri gösterilmektedir:

+ Bir çalışma alanı, Azure Machine Learning çalıştırmak için gereken Python ortamıyla yapılandırılmış [Azure Machine Learning işlem örnekleri](concept-compute-instance.md), bulut kaynakları içerebilir.

+ [Kullanıcı rolleri](how-to-assign-roles.md) , çalışma alanınızı diğer kullanıcılar, takımlar veya projelerle paylaşmanızı sağlar.
+ [İşlem hedefleri](concept-azure-machine-learning-architecture.md#compute-targets) , denemeleri hesabınızı çalıştırmak için kullanılır.
+ Çalışma alanını oluşturduğunuzda [ilişkili kaynaklar](#resources) da sizin için oluşturulur.
+ [Denemeleri](concept-azure-machine-learning-architecture.md#experiments) , modellerinizi oluşturmak için kullandığınız eğitim çalışmalardır.  
+ İşlem [hatları](concept-azure-machine-learning-architecture.md#ml-pipelines) , modelinize eğitim ve yeniden eğitim için yeniden kullanılabilir iş akışlarıdır.
+ Veri [kümeleri](concept-azure-machine-learning-architecture.md#datasets-and-datastores) , model eğitimi ve işlem hattı oluşturma için kullandığınız verilerin yönetimine yardımcı olur.
+ Dağıtmak istediğiniz bir model olduktan sonra, kayıtlı bir model oluşturursunuz.
+ [Dağıtım uç noktası](concept-azure-machine-learning-architecture.md#endpoints)oluşturmak için kayıtlı modeli ve bir Puanlama betiğini kullanın.

## <a name="tools-for-workspace-interaction"></a>Çalışma alanı etkileşimi için Araçlar

Aşağıdaki yollarla çalışma alanım ile etkileşim kurabilirsiniz:

> [!IMPORTANT]
> Aşağıda işaretlenmiş araçlar (Önizleme) Şu anda genel önizlemede.
> Önizleme sürümü, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

+ Web 'de:
    + [Azure Machine Learning Studio ](https://ml.azure.com) 
    + [Azure Machine Learning tasarımcısı](concept-designer.md) 
+ Python [için Azure Machine Learning SDK 'sı](/python/api/overview/azure/ml/intro)olan herhangi bir Python ortamında.
+ [R (Önizleme) için Azure Machine Learning SDK 'sı](https://azure.github.io/azureml-sdk-for-r/reference/index.html)olan herhangi bir r ortamında.
+ Azure Machine Learning [CLI uzantısını](./reference-azure-machine-learning-cli.md) kullanarak komut satırında
+ [Azure Machine Learning VS Code uzantısı](how-to-manage-resources-vscode.md#workspaces)


## <a name="machine-learning-with-a-workspace"></a>Çalışma alanıyla makine öğrenimi

Machine Learning görevleri çalışma alanınıza yapıları okur ve/veya yazar.

+ Model yazma denemesi çalışma alanı için bir deneme çalıştırın.
+ Model yazma eğitim sonuçlarını çalışma alanına eğitme için otomatik ML kullanın.
+ Çalışma alanına bir model kaydettirin.
+ Model dağıtma-bir dağıtım oluşturmak için kayıtlı modeli kullanır.
+ Yeniden kullanılabilir iş akışları oluşturun ve çalıştırın.
+ Denemeleri, işlem hatları, modeller, dağıtımlar gibi makine öğrenimi yapılarını görüntüleyin.
+ Modelleri izleyin ve izleyin.

## <a name="workspace-management"></a>Çalışma alanı yönetimi

Ayrıca, aşağıdaki çalışma alanı yönetim görevlerini gerçekleştirebilirsiniz:

| Çalışma alanı yönetim görevi   | Portal              | Studio | Python SDK/R SDK       | CLI        | VS Code
|---------------------------|---------|---------|------------|------------|------------|
| Çalışma alanı oluşturma        | **&check;**     | | **&check;** | **&check;** | **&check;** |
| Çalışma alanı erişimini yönetme    | **&check;**   || |  **&check;**    ||
| İşlem kaynaklarını oluşturma ve yönetme    | **&check;**   | **&check;** | **&check;** |  **&check;**   ||
| Not defteri VM 'si oluşturma |   | **&check;** | |     ||

> [!WARNING]
> Azure Machine Learning çalışma alanınızı farklı bir aboneliğe taşımak veya sahip olunan aboneliğin yeni bir kiracıya taşınması desteklenmez. Bunun yapılması hatalara neden olabilir.

## <a name="create-a-workspace"></a><a name='create-workspace'></a> Çalışma alanı oluşturma

Çalışma alanı oluşturmanın birden çok yolu vardır:  

* Her adımda size yol göstermek için bir nokta ve tıklama arabirimi için [Azure Portal](how-to-manage-workspace.md?tabs=azure-portal#create-a-workspace) kullanın.
* Python betikleri veya jupi not defterlerinden anında çalışma alanı oluşturmak için [Python için Azure Machine Learning SDK 'sını](how-to-manage-workspace.md?tabs=python#create-a-workspace) kullanın
* Şirket güvenlik standartları ile oluşturma işlemini otomatik hale getirmeniz veya özelleştirmeniz gerektiğinde bir [Azure Resource Manager şablonu](how-to-create-workspace-template.md) veya [Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) kullanın.
* Visual Studio Code ' de çalışıyorsanız [vs Code uzantısını](how-to-manage-resources-vscode.md#create-a-workspace)kullanın.

> [!NOTE]
> Çalışma alanı adı büyük/küçük harfe duyarlıdır.

## <a name="associated-resources"></a><a name="resources"></a> İlişkili kaynaklar

Yeni bir çalışma alanı oluşturduğunuzda, bu, çalışma alanı tarafından kullanılan çeşitli Azure kaynaklarını otomatik olarak oluşturur:

+ [Azure depolama hesabı](https://azure.microsoft.com/services/storage/): çalışma alanı için varsayılan veri deposu olarak kullanılır.  Azure Machine Learning işlem örneklerinizde kullanılan jupi Not defterleri burada da depolanır. 
  
  > [!IMPORTANT]
  > Varsayılan olarak, depolama hesabı genel amaçlı bir v1 hesabıdır. Çalışma alanı oluşturulduktan sonra [bunu genel amaçlı v2 'ye yükseltebilirsiniz](../storage/common/storage-account-upgrade.md) . Genel amaçlı v2 'ye yükselttikten sonra depolama hesabında hiyerarşik ad alanını etkinleştirmeyin.

  Mevcut bir Azure Depolama hesabını kullanmak için BlobStorage türünde veya Premium bir hesap (Premium_LRS ve Premium_GRS) olamaz. Ayrıca hiyerarşik bir ad alanına sahip olamaz (Azure Data Lake Storage 2. ile kullanılır). Çalışma alanının _varsayılan_ depolama hesabıyla Premium Depolama veya hiyerarşik ad alanları desteklenmez. Premium Storage veya sıradüzensel ad alanını _varsayılan olmayan_ depolama hesaplarıyla birlikte kullanabilirsiniz.
  
+ [Azure Container Registry](https://azure.microsoft.com/services/container-registry/): eğitim sırasında ve bir modeli dağıtırken kullandığınız Docker kapsayıcılarını kaydeder. Maliyetleri en aza indirmek için, dağıtım görüntüleri oluşturuluncaya kadar ACR **yavaş yüklenir** .

+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): modelleriniz hakkında izleme bilgilerini depolar.

+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): işlem hedefleri ve çalışma alanı için gereken diğer hassas bilgiler tarafından kullanılan gizli dizileri depolar.

> [!NOTE]
> Bunun yerine, [BIR ARM şablonu kullanarak bir](how-to-create-workspace-template.md)çalışma alanını [Python SDK 'Sı](how-to-manage-workspace.md?tabs=python#create-a-workspace), [R SDK](https://azure.github.io/azureml-sdk-for-r/reference/create_workspace.html)veya CLI Azure Machine Learning CLI ile oluşturduğunuzda mevcut Azure Kaynak örneklerini kullanabilirsiniz.

<a name="wheres-enterprise"></a>

## <a name="what-happened-to-enterprise-edition"></a>Enterprise Edition 'a ne oldu?

Eylül 2020 itibariyle, Enterprise Edition çalışma alanlarında bulunan tüm yetenekler artık temel sürüm çalışma alanlarında de mevcuttur. Yeni kurumsal çalışma alanları artık oluşturulamaz.  Parametresini kullanan herhangi bir SDK, CLı veya Azure Resource Manager çağrısı çalışmaya `sku` devam edecektir, ancak temel bir çalışma alanı sağlanacak.

21 Aralık 'tan itibaren, tüm Enterprise Edition çalışma alanları otomatik olarak temel sürüme ayarlanacak, bu da aynı yeteneklere sahip olur. Bu işlem sırasında kesinti gerçekleşmeyecek. 1 Ocak 2021 ' de Enterprise Edition resmi olarak kullanımdan kalkacaktır. 

Her iki sürümde, müşteriler tüketilen Azure kaynaklarının maliyetlerinden sorumludur ve Azure Machine Learning için herhangi bir ek ücret ödemeyecektir. Daha fazla ayrıntı için lütfen [Azure Machine Learning fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/machine-learning/) bakın.

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning kullanmaya başlamak için bkz.:

+ [Azure Machine Learning genel bakış](overview-what-is-azure-ml.md)
+ [Çalışma alanı oluşturma ve yönetme](how-to-manage-workspace.md)
+ [Öğretici: geliştirme ortamınızda Azure Machine Learning kullanmaya başlayın](tutorial-1st-experiment-sdk-setup-local.md)
+ [Öğretici: bir işlem örneği üzerinde ilk ML denemenizi oluşturmaya başlama](tutorial-1st-experiment-sdk-setup.md)
+ [Öğretici: Otomatik makine öğrenmesiyle ilk sınıflandırma modelinizi oluşturma](tutorial-first-experiment-automated-ml.md) 
+ [Öğretici: Tasarımcıyla otomobil fiyatını tahmin etme](tutorial-designer-automobile-price-train-score.md)
