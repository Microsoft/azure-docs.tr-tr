---
title: 'Öğretici: Machine Learning ile çalışmaya başlama-Python'
titleSuffix: Azure Machine Learning
description: Kişisel geliştirme ortamınızda çalışan Python için Azure Machine Learning SDK ile çalışmaya başlayın.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 02/11/2021
ms.custom: devx-track-python, contperf-fy21q3
adobe-target: true
ms.openlocfilehash: 69340245cdebda570b526afd4f11ac8314384b43
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210630"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-your-development-environment-part-1-of-4"></a>Öğretici: geliştirme ortamınızda Azure Machine Learning kullanmaya başlayın (Bölüm 1/4)

Bu *dört bölümden oluşan öğretici serisinde*, Azure bulut platformunda Azure Machine Learning ve işlerin temel aldığı Python Machine Learning görevlerinin temellerini öğrenirsiniz. 

Bu öğretici serisinin 1. bölümünde şunları yapmanız gerekir:

> [!div class="checklist"]
> * Azure Machine Learning SDK 'sını yükler.
> * Kod için dizin yapısını ayarlayın.
> * Azure Machine Learning çalışma alanı oluşturun.
> * Yerel geliştirme ortamınızı yapılandırın.
> * Modellerinizi eğitmek için bulut tabanlı bir kaynak olan bir işlem kümesi ayarlayın.

Bu öğretici serisi **toplu işleri** göndermek için gereken Azure Machine Learning kavramlara odaklanır. Bu, kodun buluta gönderildiği ve herhangi bir kullanıcı etkileşimi olmadan arka planda çalışacağı yerdir. Bu, tekrar tekrar çalıştırmak istediğiniz tamamlanmış betikler veya kodlar için ya da işlem yoğunluklu makine öğrenimi görevleri için yararlıdır.

Azure Machine Learning kullanmaya başlamak için harika bir yol, bir Jupileter not defteriyle birlikte bulunur.  Bkz. [öğretici: Jupyter not defterlerinde Azure Machine Learning kullanmaya başlama](tutorial-1st-experiment-sdk-setup.md).

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce ücretsiz bir hesap oluşturun. [Azure Machine Learning](https://aka.ms/AMLFree)deneyin.
- Python sanal ortamlarını yönetmek ve paketleri yüklemek için [Anaconda](https://www.anaconda.com/download/) veya [miniconda](https://www.anaconda.com/download/) .  
- Conda kullanma hakkında bilgi sahibi değilseniz bkz. [Conda ile çalışmaya](https://conda.io/projects/conda/en/latest/user-guide/getting-started.html)başlama.
- Python betiklerinizi oluşturmak için herhangi bir IDE veya metin düzenleyici.

## <a name="install-the-azure-machine-learning-sdk"></a>Azure Machine Learning SDK 'sını yükler

Bu öğreticide, Python için Azure Machine Learning SDK kullanacaksınız. Python bağımlılığı sorunlarını önlemek için yalıtılmış bir ortam oluşturacaksınız. Bu öğretici serisi, bu ortamı oluşturmak için Conda kullanır. , Veya Docker gibi diğer çözümleri kullanmayı tercih ediyorsanız `venv` , `virtualenv` >= 3,5 ve 3,9 < bir Python sürümü kullandığınızdan emin olun.

Sisteminizde Conda 'nın yüklü olup olmadığını denetleyin:
    
```bash
conda --version
```
    
Bu komut bir hata döndürürse `conda not found` , [Miniconda indirin ve yükleyin](https://docs.conda.io/en/latest/miniconda.html). 

Conda 'yı yükledikten sonra, yeni bir ortam oluşturmak için bir Terminal veya Anaconda Istemi penceresi kullanın:

```bash
conda create -n tutorial python=3.8
```

Sonra, Azure Machine Learning SDK 'sını oluşturduğunuz Conda ortamına yükleyebilirsiniz:

```bash
conda activate tutorial
pip install azureml-core
```
    
> [!NOTE]
> Azure Machine Learning SDK yüklemesinin tamamlanabilmesi yaklaşık 2 dakika sürer.
>
> Bir zaman aşımı hatası alırsanız, `pip install --default-timeout=100 azureml-core` bunun yerine deneyin.


> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://www.research.net/r/7C8Z3DN?issue=install-sdk) [SDK 'yı yükledim](?success=install-sdk#dir)

## <a name="create-a-directory-structure-for-code"></a><a name="dir"></a>Kod için dizin yapısı oluşturma

Bu öğretici için aşağıdaki basit dizin yapısını ayarlamanızı öneririz:

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/directory-structure-1.png" alt-text="Dizin yapısı:. azureml alt dizini ile öğretici üst düzeyi":::


- `tutorial`: Projenin en üst düzey dizini.
- `.azureml`: Azure Machine Learning yapılandırma dosyalarını depolamak için gizli alt dizin.

Örneğin, bunu bir bash penceresinde oluşturmak için:

```bash
mkdir tutorial
cd tutorial
mkdir .azureml
```

> [!TIP]
> Yapıyı grafik pencerede oluşturmak veya görüntülemek için, önce gizli dosya ve klasörleri görme ve oluşturma özelliğini etkinleştirin:
>
> * Mac Finder penceresinde, **Command + SHIFT + kullanın.** gizli dosyaların/klasörlerin görüntülenmesini değiştirme.  
> * Bir Windows 10 Dosya Gezgininde bkz. [Gizli dosya ve klasörleri görüntüleme](https://support.microsoft.com/en-us/windows/view-hidden-files-and-folders-in-windows-10-97fbc472-c603-9d90-91d0-1166d1d9f4b5). 
> * Linux grafik arabiriminde, **CTRL + h** veya **Görünüm** menüsünü kullanın ve **gizli dosyaları göstermek** için kutuyu işaretleyin.




> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://www.research.net/r/7C8Z3DN?issue=create-dir) [bir dizin](?success=create-dir#workspace) oluşturdum

## <a name="create-an-azure-machine-learning-workspace"></a><a name="workspace"></a>Azure Machine Learning çalışma alanı oluşturma

Çalışma alanı, Azure Machine Learning için en üst düzey kaynaktır ve merkezi bir yerdir:

- İşlem gibi kaynakları yönetin.
- Not defterleri, ortamlar, veri kümeleri, işlem hatları, modeller ve uç noktalar gibi varlıkları depolayın.
- Diğer ekip üyeleriyle işbirliği yapın.

Üst düzey dizinde, `tutorial` aşağıdaki kodu kullanarak adlı yeni bir Python dosyası ekleyin `01-create-workspace.py` . Parametreleri (ad, abonelik KIMLIĞI, kaynak grubu ve [konum](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)) tercihlerinizi uyarlayın.

Kodu etkileşimli bir oturumda veya bir Python dosyası olarak çalıştırabilirsiniz.

>[!NOTE]
> Yerel bir geliştirme ortamı (örneğin, bilgisayarınız) kullandığınızda, aşağıdaki kodu ilk kez çalıştırdığınızda bir *cihaz kodu* kullanarak çalışma alanınızda kimlik doğrulaması yapmanız istenir. Ekrandaki yönergeleri takip edin.

```python
# tutorial/01-create-workspace.py
from azureml.core import Workspace

ws = Workspace.create(name='<my_workspace_name>', # provide a name for your workspace
                      subscription_id='<azure-subscription-id>', # provide your subscription ID
                      resource_group='<myresourcegroup>', # provide a resource group name
                      create_resource_group=True,
                      location='<NAME_OF_REGION>') # For example: 'westeurope' or 'eastus2' or 'westus2' or 'southeastasia'.

# write out the workspace details to a configuration file: .azureml/config.json
ws.write_config(path='.azureml')
```

Etkinleştirilen *Tutorial1* Conda ortamı olan pencerede, bu kodu `tutorial` dizinden çalıştırın.

```bash
cd <path/to/tutorial>
python ./01-create-workspace.py
```

> [!TIP]
> Bu kodu çalıştırmak, aboneliğe erişiminizin olmadığı bir hata veriyorsa, bkz. kimlik doğrulama seçenekleri hakkında bilgi için [çalışma alanı oluşturma](how-to-manage-workspace.md?tab=python#create-multi-tenant) .


*01-Create-Workspace.py* başarıyla çalıştırdıktan sonra klasör yapınız şöyle görünür:

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/directory-structure-2.png" alt-text="Dosya config.js, 01-create-workspace.py çalıştırıldıktan sonra. azureml alt dizininde görüntülenir":::

Dosya, `.azureml/config.json` Azure Machine Learning çalışma alanınıza bağlanmak için gereken meta verileri içerir. Yani, abonelik KIMLIĞINIZI, kaynak grubunuzu ve çalışma alanı adını içerir. 

> [!NOTE]
> İçeriği `config.json` gizli değil. Bu ayrıntıların paylaşılması iyidir.
>
> Azure Machine Learning çalışma alanınız ile etkileşimde bulunmak için kimlik doğrulaması yine de gereklidir.

> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://www.research.net/r/7C8Z3DN?issue=create-workspace) [bir çalışma alanı oluşturdum](?success=create-workspace#cluster)

## <a name="create-an-azure-machine-learning-compute-cluster"></a><a name="cluster"></a> Azure Machine Learning işlem kümesi oluşturma

`tutorial`Adlı üst düzey dizinde bir Python betiği oluşturun `02-create-compute.py` . Sıfır ve dört düğüm arasında otomatik ölçeklendirme yapılacak bir Azure Machine Learning işlem kümesi oluşturmak için aşağıdaki kodla doldurun:

```python
# tutorial/02-create-compute.py
from azureml.core import Workspace
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

ws = Workspace.from_config() # This automatically looks for a directory .azureml

# Choose a name for your CPU cluster
cpu_cluster_name = "cpu-cluster"

# Verify that the cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           idle_seconds_before_scaledown=2400,
                                                           min_nodes=0,
                                                           max_nodes=4)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)
```

Etkinleştirilen *Tutorial1* Conda ortamını içeren pencerede, Python dosyasını çalıştırın:

```bash
python ./02-create-compute.py
```


> [!NOTE]
> Küme oluşturulduğunda, 0 düğümü sağlanır. Bir iş gönderene kadar küme ücret *ödemez* . Bu küme, 2.400 saniye boyunca boşta kaldığında ölçeği ölçeklendirecektir (40 dakika).

Klasör yapınız şimdi şöyle görünür:

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/directory-structure-3.png" alt-text="Öğretici dizinine 02-create-compute.py ekleme":::

> [!div class="nextstepaction"]
> [Bir sorunla karşılaşdığım](https://www.research.net/r/7C8Z3DN?issue=create-compute-cluster) [bir işlem kümesi](?success=create-compute-cluster#next-steps) oluşturdum

## <a name="view-in-the-studio"></a>Studio 'da görüntüle

Oluşturduğunuz çalışma alanını ve işlem örneğini görüntülemek için [Azure Machine Learning Studio](https://ml.azure.com) 'da oturum açın.

1. Çalışma alanını oluşturmak için kullandığınız **aboneliği** seçin.
1. Oluşturduğunuz **Machine Learning çalışma alanını** , *öğretici-WS*' ı seçin.
1. Çalışma alanı yüklendiğinde, sol taraftaki **işlem**' i seçin.
1. En üstte, **işlem kümeleri** sekmesini seçin.

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/compute-instance-in-studio.png" alt-text="Ekran görüntüsü: çalışma alanınızdaki işlem örneğini görüntüleyin.":::

Bu görünümde, sağlanan işlem kümesi, boştaki düğüm sayısı, meşgul düğüm ve sağlaması kaldırılan düğümler ile birlikte gösterilir.  Kümeyi henüz kullanmadıysanız, tüm düğümlerin Şu anda sağlaması kaldırılır.

## <a name="next-steps"></a>Sonraki adımlar

Bu kurulum öğreticisinde şunları yapabilirsiniz:

- Azure Machine Learning çalışma alanı oluşturuldu.
- Yerel geliştirme ortamınızı ayarlayın.
- Azure Machine Learning işlem kümesi oluşturuldu.

Bu öğreticinin diğer bölümlerinde şunları öğreneceksiniz:

* Bölüm 2. Python için Azure Machine Learning SDK kullanarak bulutta kod çalıştırın.
* Bölüm 3. Model eğitimi için kullandığınız Python ortamını yönetin.
* Bölüm 4. Verileri Azure 'a yükleyin ve eğitiminde bu verileri kullanın.

Azure Machine Learning işlem kümesine bir betik göndermeyi adım adım incelemek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Öğretici: "Hello World!" Çalıştır Azure 'da Python betiği](tutorial-1st-experiment-hello-world.md)
