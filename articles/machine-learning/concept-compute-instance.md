---
title: Azure Machine Learning işlem örneği nedir?
titleSuffix: Azure Machine Learning
description: Tam olarak yönetilen bulut tabanlı bir iş istasyonu olan Azure Machine Learning işlem örneği hakkında bilgi edinin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 10/02/2020
ms.openlocfilehash: 309cf3882ade99de3f2e29a037d20ca50e35f490
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106066679"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Azure Machine Learning işlem örneği nedir?

Azure Machine Learning işlem örneği, veri bilimcileri için yönetilen bulut tabanlı bir iş istasyonudur.

İşlem örnekleri, Azure Machine Learning geliştirmeye başlamanızı kolaylaştırır ve BT yöneticileri için yönetim ve kurumsal hazırlık özellikleri sağlar.  

Makine öğrenimi için bulutta tam olarak yapılandırılmış ve yönetilen geliştirme ortamınız olarak bir işlem örneği kullanın. Ayrıca, geliştirme ve test amacıyla bilgi işlem hedefi olarak da kullanılabilir.  

Üretim sınıfı modeli eğitimi için, çok düğümlü ölçeklendirme özelliklerine sahip bir [Azure Machine Learning işlem kümesi](how-to-create-attach-compute-cluster.md) kullanın. Üretim sınıfı modeli dağıtımı için [Azure Kubernetes hizmet kümesi](how-to-deploy-azure-kubernetes-service.md)' ni kullanın.

İşlem örneği jupi işlevinin çalışması için, Web yuva iletişiminin devre dışı bırakılmadığından emin olun. Lütfen ağınızın *. instances.azureml.net ve *. instances.azureml.ms öğesine WebSocket bağlantılarına izin verdiğinden emin olun.

## <a name="why-use-a-compute-instance"></a>Neden bir işlem örneği kullanılmalıdır?

İşlem örneği, Machine Learning geliştirme ortamınız için iyileştirilmiş, tam olarak yönetilen bulut tabanlı bir iş istasyonudur. Aşağıdaki avantajları sağlar:

|Önemli avantajlar|Description|
|----|----|
|Üretkenlik|Tümleşik not defterlerini ve Azure Machine Learning Studio 'daki aşağıdaki araçları kullanarak modeller oluşturabilir ve dağıtabilirsiniz:<br/>-Jupyıter<br/>-Jupyıterlab<br/>-RStudio (Önizleme)<br/>İşlem örneği, Azure Machine Learning çalışma alanı ve Studio ile tamamen tümleşiktir. Çalışma alanındaki diğer veri bilimcileri ile not defterlerini ve verileri paylaşabilirsiniz.<br/> İşlem örnekleri ile [vs Code](https://techcommunity.microsoft.com/t5/azure-ai/power-your-vs-code-notebooks-with-azml-compute-instances/ba-p/1629630) de kullanabilirsiniz.
|Yönetilen & güvenli|Güvenlik ayak izini azaltın ve kurumsal güvenlik gereksinimleriyle uyumluluk ekleyin. İşlem örnekleri, güçlü yönetim ilkeleri ve güvenli ağ yapılandırmalarının yanı sıra şunları sağlar:<br/><br/>-Kaynak Yöneticisi şablonlarından veya Azure Machine Learning SDK 'dan oto sağlama<br/>- [Azure rol tabanlı erişim denetimi (Azure RBAC)](../role-based-access-control/overview.md)<br/>- [Sanal ağ desteği](./how-to-secure-training-vnet.md#compute-instance)<br/>-SSH erişimini etkinleştirmek/devre dışı bırakmak için SSH ilkesi<br/>TLS 1,2 etkin |
|&nbsp;Ml için önceden yapılandırılmış &nbsp;|Önceden yapılandırılmış ve güncel ML paketleri, derin öğrenme çerçeveleri, GPU sürücüleri ile kurulum görevlerinde zaman kazanın.|
|Tamamen özelleştirilebilir|GPU 'Lar ve kalıcı alt düzey özelleştirme dahil olmak üzere Azure VM türleri için kapsamlı destek; Örneğin, paket ve sürücü yükleme, gelişmiş senaryolar için bir Breeze sağlar. |

Kendiniz [bir işlem örneği oluşturabilirsiniz](how-to-create-manage-compute-instance.md?tabs=python#create) veya bir yönetici [sizin için bir işlem örneği oluşturabilir](how-to-create-manage-compute-instance.md?tabs=python#create-on-behalf-of-preview).

## <a name="tools-and-environments"></a><a name="contents"></a>Araçlar ve ortamlar

> [!IMPORTANT]
> Bu makaledeki işaretlenmiş öğeler (Önizleme) Şu anda genel önizleme aşamasındadır.
> Önizleme sürümü, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Machine Learning işlem örneği, çalışma alanınızdaki tamamen tümleşik bir not defteri deneyiminde modelleri yazmanıza, eğmenize ve dağıtmanıza olanak sağlar.

İşlem örneğini, SSH gerekmeden uzak sunucu olarak kullanarak [vs Code](https://techcommunity.microsoft.com/t5/azure-ai/power-your-vs-code-notebooks-with-azml-compute-instances/ba-p/1629630) jupi not defterlerini çalıştırabilirsiniz. Ayrıca, [uzak SSH uzantısı](https://devblogs.microsoft.com/python/enhance-your-azure-machine-learning-experience-with-the-vs-code-extension/)aracılığıyla vs Code tümleştirmesini etkinleştirebilirsiniz.

[Paketleri yükleyebilir](how-to-access-terminal.md#install-packages) ve işlem örneğinize [çekirdekler ekleyebilirsiniz](how-to-access-terminal.md#add-new-kernels) .  

Aşağıdaki araçlar ve ortamlar, işlem örneğinde zaten yüklüdür: 

|Genel araçlar & ortamları|Ayrıntılar|
|----|:----:|
|Sürücüler|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Intel MPı kitaplığı||
|Azure CLI’si ||
|Azure Machine Learning örnekleri ||
|Docker||
|Nginx||
|NCCL 2,0 ||
|Protobuf|| 

|**R** araçları & ortamları|Ayrıntılar|
|----|:----:|
|RStudio Server açık kaynak sürümü (Önizleme)||
|R çekirdeği||
|R için SDK Azure Machine Learning|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>SDK örnekleri|

|**Python** araçları & ortamları|Ayrıntılar|
|----|----|
|Anaconda Python||
|Jupi ve uzantıları||
|Jupi terlab ve uzantıları||
[Python için Azure Machine Learning SDK'sı](/python/api/overview/azure/ml/intro)</br>Pypı 'den|, Azureml ek paketlerin çoğunu içerir.  Tam listeyi görmek için, [işlem örneğiniz üzerinde bir Terminal penceresi açın](how-to-access-terminal.md) ve şunu çalıştırın: <br/> `conda list -n azureml_py36 azureml*` |
|Diğer Pypı paketleri|`jupytext`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Conda paketleri|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|Derin öğrenme paketleri|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|ONNX paketleri|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Azure Machine Learning Python & R SDK örnekleri||

Python paketlerinin hepsi **python 3,6-AzureML** ortamında yüklüdür.  

## <a name="accessing-files"></a>Dosyalara erişme

Not defterleri ve R betikleri, Azure dosya paylaşımında çalışma alanınızın varsayılan depolama hesabında depolanır.  Bu dosyalar "Kullanıcı dosyaları" dizininizin altında bulunur. Bu depolama, işlem örnekleri arasında Not defterlerinin paylaşılmasını kolaylaştırır. Depolama hesabı Ayrıca bir işlem örneğini durdurduğunuzda veya sildiğinizde Not defterlerinizin güvenle korunmalarını önler.

Çalışma alanınızın Azure dosya paylaşma hesabı, işlem örneğine bir sürücü olarak bağlanır. Bu sürücü, jupi, Jupyıter Labs ve RStudio için varsayılan çalışma dizinidir. Diğer bir deyişle, Jupiter, Jupiterlab veya RStudio 'da oluşturduğunuz Not defterleri ve diğer dosyalar otomatik olarak dosya paylaşımında depolanır ve diğer işlem örneklerinde kullanılabilir.

Dosya paylaşımındaki dosyalara aynı çalışma alanındaki tüm işlem örneklerinden erişilebilir. İşlem örneğindeki bu dosyalarda yapılan tüm değişiklikler, dosya paylaşımında güvenilir bir şekilde kalıcı olarak kalıcı hale getirilir.

Ayrıca, en son Azure Machine Learning örnekleri çalışma alanı dosya paylaşımındaki Kullanıcı dosyaları dizini altında klasörünüze kopyalayabilirsiniz.

Küçük dosyaların yazılması, ağ sürücülerinde, işlem örneği yerel diskinin üzerine yazılmasından daha yavaş olabilir.  Çok sayıda küçük dosya yazıyorsanız, Dizin gibi işlem örneğinde doğrudan bir dizin kullanmayı deneyin `/tmp` . Not Bu dosyalara diğer işlem örneklerinden erişilemeyecektir. 

`/tmp`Geçici verileriniz için işlem örneğindeki dizini kullanabilirsiniz.  Ancak, işlem örneğinin işletim sistemi diskine büyük veri dosyaları eklemeyin.  Bunun yerine [veri depoları](concept-azure-machine-learning-architecture.md#datasets-and-datastores) kullanın. Jupyıterlab git uzantısını yüklediyseniz, işlem örneği performansının yavaşlamasına da neden olabilir.

## <a name="managing-a-compute-instance"></a>İşlem örneğini yönetme

Azure Machine Learning Studio 'daki çalışma alanınızda **işlem**' ı seçin ve ardından en üstteki **işlem örneği** ' ni seçin.

![İşlem örneğini yönetme](./media/concept-compute-instance/manage-compute-instance.png)

Aşağıdaki eylemleri gerçekleştirebilirsiniz:

* [Bir işlem örneği oluşturun](#create). 
* İşlem örnekleri sekmesini yenileyin.
* Bir işlem örneğini başlatın, durdurun ve yeniden başlatın.  Her çalıştığında örnek için ödeme yaparsınız. Maliyeti azaltmak için kullanmıyorsanız, işlem örneğini durdurun. Bir işlem örneğinin durdurulması onu kaldırır. Daha sonra ihtiyacınız olduğunda yeniden başlatın. İşlem örneğinin durdurulması, işlem saatleri için faturalandırmayı durduruyor, ancak yine de disk, genel IP ve standart yük dengeleyici için faturalandırılacaksınız.
* Bir işlem örneğini silin.
* Yalnızca oluşturduğumuz göstermek için işlem ınstanced listesini filtreleyin.

Çalışma alanınızdaki her bir işlem örneği için kullanabileceğiniz şunları yapabilirsiniz:

* Jupyıter, Jupiterlab, RStudio 'yu işlem örneği üzerinde erişme
* İşlem örneğine SSH. SSH erişimi varsayılan olarak devre dışıdır ancak işlem örneği oluşturma sırasında etkinleştirilebilir. SSH erişimi, ortak/özel anahtar mekanizmasıyla gerçekleştirilir. Sekmesi, IP adresi, Kullanıcı adı ve bağlantı noktası numarası gibi SSH bağlantısı için Ayrıntılar verecektir.
* IP adresi ve bölge gibi belirli bir işlem örneği hakkındaki ayrıntıları alın.

[Azure RBAC](../role-based-access-control/overview.md) , çalışma alanındaki hangi kullanıcıların bir bilgi işlem örneği oluşturabileceğinizi, silebileceği, başlatabileceği, durdurabileceğinizi denetlemenize olanak tanır. Çalışma alanı katılımcısı ve sahip rolündeki tüm kullanıcılar çalışma alanı genelinde işlem örnekleri oluşturabilir, silebilir, başlatabilir, durdurabilir ve yeniden başlatabilir. Bununla birlikte, yalnızca belirli bir işlem örneği veya kendi adına oluşturulmuşsa atanan kullanıcı atanmış bir işlem örneği üzerinde Jupyıter, Jupiterlab ve RStudio erişimine izin verilir. Bir işlem örneği, kök erişimi olan tek bir kullanıcıya ayrılmıştır ve jupi/Jupiterlab/RStudio aracılığıyla oturum açabilir. İşlem örneğinde tek kullanıcı oturum açma işlemi olur ve tüm eylemler, bu kullanıcının Azure RBAC ve deneme çalıştırmaları için kimliğini kullanır. SSH erişimi, ortak/özel anahtar mekanizması aracılığıyla denetlenir.

Bu eylemler, Azure RBAC tarafından denetlenebilir:
* *Microsoft. MachineLearningServices/çalışma alanları/hesaplar/okundu*
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft. MachineLearningServices/çalışma alanları/hesaplar/Sil*
* *Microsoft. MachineLearningServices/çalışma alanları/hesaplar/Başlat/eylem*
* *Microsoft. MachineLearningServices/Workspaces/hesaplar/durdur/eylem*
* *Microsoft. MachineLearningServices/Workspaces/hesaplar/yeniden Başlat/eylem*

Bir işlem örneği oluşturmak için aşağıdaki eylemler için izinlerinizin olması gerekir:
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft.MachineLearningServices/workspaces/checkComputeNameAvailability/action*


### <a name="create-a-compute-instance"></a><a name="create"></a>İşlem örneği oluşturma

Azure Machine Learning Studio 'daki çalışma alanınızda, Not defterlerinizden birini çalıştırmaya hazırsanız **işlem** bölümünden veya **Not defterleri** bölümünde [Yeni bir işlem örneği oluşturun](how-to-create-attach-compute-studio.md#compute-instance) . 

Ayrıca, bir örnek oluşturabilirsiniz
* Doğrudan [Tümleşik Not defteri deneyiminden](tutorial-1st-experiment-sdk-setup.md#azure)
* Azure portal
* Azure Resource Manager şablondan. Örnek bir şablon için [Azure Machine Learning işlem örneği oluşturma şablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance)' na bakın.
* [Azure MACHINE LEARNING SDK](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/machine-learning/concept-compute-instance.md) ile
* [Azure Machine Learning Için CLI uzantısı](reference-azure-machine-learning-cli.md#computeinstance)

VM ailesi kotası başına bölge başına adanmış çekirdekler ve işlem örneği oluşturma için geçerli olan toplam bölgesel kota, Azure Machine Learning eğitim işlem kümesi kotasıyla birleştirilmiş ve paylaşılır. İşlem örneği durdurulduğunda, işlem örneğini yeniden başlatabileceksiniz emin olmak için kota serbest bırakılır.


### <a name="create-on-behalf-of-preview"></a>Adına oluştur (Önizleme)

Yönetici olarak, bir veri bilimcu adına bir işlem örneği oluşturabilir ve örneği bunlara ile atayabilirsiniz:
* [Azure Resource Manager şablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance).  Bu şablonda gereken Tenantıd ve objectID 'yi bulma hakkında daha fazla bilgi için bkz. [kimlik doğrulama yapılandırması için kimlik nesne kimliklerini bulma](../healthcare-apis/fhir/find-identity-object-ids.md).  Bu değerleri Azure Active Directory portalında da bulabilirsiniz.
* REST API

İçin işlem örneğini oluşturduğunuz veri bilimcisi, aşağıdaki Azure RBAC izinlerine ihtiyaç duyuyor: 
* *Microsoft. MachineLearningServices/çalışma alanları/hesaplar/Başlat/eylem*
* *Microsoft. MachineLearningServices/Workspaces/hesaplar/durdur/eylem*
* *Microsoft. MachineLearningServices/Workspaces/hesaplar/yeniden Başlat/eylem*
* *Microsoft. MachineLearningServices/çalışma alanları/hesaplar/applicationaccess/Action*

Veri bilimcisi, işlem örneğini başlatabilir, durdurabilir ve yeniden başlatabilir. Bu işlemler için işlem örneğini kullanabilir:
* Jupyter
* Jupyıterlab
* RStudio
* Tümleşik Not defterleri

## <a name="compute-target"></a>İşlem hedefi

İşlem örnekleri, Azure Machine Learning işlem eğitimi kümelerine benzer bir [eğitim işlem hedefi](concept-compute-target.md#train) olarak kullanılabilir. 

İşlem örneği:
* İş kuyruğu vardır.
* Bir sanal ağ ortamında, kuruluşların SSH bağlantı noktasını açmasına gerek olmadan işleri güvenli bir şekilde çalıştırır. İş kapsayıcılı bir ortamda yürütülür ve model bağımlılıklarınızı bir Docker kapsayıcısında paketleyebilir.
* Birden çok küçük işi paralel olarak çalıştırabilir (Önizleme).  Çekirdek başına iki iş paralel olarak çalışabilir, ancak işlerin geri kalanı sıraya alınır.
* Tek düğümlü çok GPU dağıtılmış eğitim işlerini destekler

Test/hata ayıklama senaryoları için, işlem örneğini yerel bir ınırm dağıtım hedefi olarak kullanabilirsiniz.

> [!TIP]
> İşlem örneğinde 12GB işletim sistemi diski vardır. Disk alanı tükenseniz, işlem örneğini [durdurmadan veya yeniden başlatmadan](how-to-create-manage-compute-instance.md#manage) önce en az 1-2 GB 'yi temizlemek için [terminali kullanın](how-to-access-terminal.md) .


## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>Not defteri VM 'sine ne oldu?

İşlem örnekleri, Not defteri VM 'sini değiştiriyor.  

Çalışma alanı dosya paylaşımında depolanan tüm not defteri dosyalarına ve çalışma alanı veri depolarındaki verilere bir işlem örneğinden erişilebilecektir. Ancak, daha önce bir not defteri VM 'sinde yüklü olan tüm özel paketlerin, işlem örneğine yeniden yüklenmesi gerekir. İşlem kümesi oluşturma için uygulanan kota sınırlamaları, işlem örneği oluşturma işlemi için de geçerlidir.

Yeni Not defteri VM 'Leri oluşturulamıyor. Ancak, tam işlevsellikle oluşturmuş olduğunuz not defteri VM 'lerine erişmeye ve bunları kullanmaya devam edebilirsiniz. İşlem örnekleri, mevcut not defteri VM 'leriyle aynı çalışma alanında oluşturulabilir.


## <a name="next-steps"></a>Sonraki adımlar

* [İşlem örneği oluşturma ve yönetme](how-to-create-manage-compute-instance.md)
* [Öğretici: Ilk ml modelinize eğitme](tutorial-1st-experiment-sdk-train.md) bir işlem örneğinin tümleşik bir not defteriyle nasıl kullanılacağını gösterir.
