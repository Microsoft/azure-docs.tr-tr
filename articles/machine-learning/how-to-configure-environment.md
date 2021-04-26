---
title: Python geliştirme ortamını ayarlama
titleSuffix: Azure Machine Learning
description: Jupi Not defterleri, Visual Studio Code, Azure Databricks ve veri bilimi sanal makinelerinde Python geliştirme ortamları Azure Machine Learning ayarlayın.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 03/22/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: 7f35f9196fb4ab67de724ba73982fed69ac81361
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306849"
---
# <a name="set-up-a-python-development-environment-for-azure-machine-learning"></a>Azure Machine Learning için bir Python geliştirme ortamı ayarlama

Azure Machine Learning için bir Python geliştirme ortamı yapılandırmayı öğrenin.

Aşağıdaki tabloda, bu makalede ele alınan her geliştirme ortamı, profesyonelleri ve dezavantajlarla birlikte gösterilmektedir.

| Ortam | Avantajlar | Dezavantajlar |
| --- | --- | --- |
| [Yerel ortam](#local) | Geliştirme ortamınızın ve bağımlılıklarınızın tam denetimi. İstediğiniz herhangi bir yapı aracı, ortam veya IDE ile çalıştırın. | Kullanmaya başlamak için daha uzun sürer. Gerekli SDK paketlerinin yüklü olması ve henüz yoksa bir ortamın de yüklü olması gerekir. |
| [Veri Bilimi Sanal Makinesi (DSVM)](#dsvm) | Bulut tabanlı işlem örneğine benzer şekilde (Python ve SDK önceden yüklenmiş olarak), ancak daha popüler veri bilimi ve Machine Learning araçları önceden yüklenmiş olarak. Kolayca ölçeklendirilmesi ve diğer özel araçlar ve iş akışlarıyla birleştirmek. | Bulut tabanlı işlem örneğiyle karşılaştırıldığında daha yavaş bir başlangıç deneyimi. |
| [Azure Machine Learning işlem örneği](#compute-instance) | Başlamak için en kolay yol. Tüm SDK, çalışma alanı sanal makinenizde zaten yüklüdür ve Not defteri öğreticileri önceden klonlanır ve çalıştırılmaya hazırlanmıştır. | Geliştirme ortamınız ve bağımlılıklarınız üzerinde denetim olmaması. Linux VM için ek maliyet (sanal makine, ücretlerden kaçınmak için kullanımda olmadığında durdurulabilir). [Fiyatlandırma ayrıntılarına](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)bakın. |
| [Azure Databricks](how-to-configure-databricks-automl-environment.md) | Ölçeklenebilir Apache Spark platformunda büyük ölçekli yoğun makine öğrenimi iş akışlarını çalıştırmak için idealdir. | Deneysel makine öğrenimi veya daha küçük ölçekli denemeleri ve iş akışları için fazla sonlandırılmalıdır. Azure Databricks için ek ücret tahakkuk ettir. [Fiyatlandırma ayrıntılarına](https://azure.microsoft.com/pricing/details/databricks/)bakın. |

Bu makalede ayrıca aşağıdaki araçlar için ek kullanım ipuçları sunulmaktadır:

* Jupyter Not defterleri: zaten Jupyter not defterlerini kullanıyorsanız SDK 'nın yüklenmesi gereken bazı ek özellikleri vardır.

* Visual Studio Code: Visual Studio Code kullanıyorsanız, [Azure Machine Learning uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) Python için kapsamlı dil desteği ve Azure Machine Learning daha kolay ve üretken bir şekilde çalışmayı sağlayacak özellikler içerir.

## <a name="prerequisites"></a>Önkoşullar

* Azure Machine Learning çalışma alanı. Bir tane yoksa, [Azure Portal](how-to-manage-workspace.md), [Azure CLI](how-to-manage-workspace-cli.md#create-a-workspace)ve [Azure Resource Manager şablonları](how-to-create-workspace-template.md)aracılığıyla bir Azure Machine Learning çalışma alanı oluşturabilirsiniz.

### <a name="local-and-dsvm-only-create-a-workspace-configuration-file"></a><a id="workspace"></a> Yalnızca yerel ve DSVM: çalışma alanı yapılandırma dosyası oluşturma

Çalışma alanı yapılandırma dosyası, SDK 'nın Azure Machine Learning çalışma alanıyla nasıl iletişim kuracağını söyleyen bir JSON dosyasıdır. Dosya *config.js* olarak adlandırılır ve aşağıdaki biçime sahiptir:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Bu JSON dosyası, Python betikleri veya Jupyıter not defterlerini içeren dizin yapısında olmalıdır. Aynı dizinde, *. azureml* adlı bir alt dizin veya bir üst dizin içinde olabilir.

Bu dosyayı kodunuzda kullanmak için [`Workspace.from_config`](/python/api/azureml-core/azureml.core.workspace.workspace#from-config-path-none--auth-none---logger-none---file-name-none-) yöntemini kullanın. Bu kod, dosyadaki bilgileri yükler ve çalışma alanınıza bağlanır.

Aşağıdaki yöntemlerden birinde bir çalışma alanı yapılandırma dosyası oluşturun:

* Azure portalı

    **Dosyayı indirin**: [Azure Portal](https://ms.portal.azure.com), çalışma alanınızın **genel bakış** bölümünde **config.jsindir** ' i seçin.

    ![Azure portalı](./media/how-to-configure-environment/configure.png)

* Python SDK Azure Machine Learning

    Azure Machine Learning çalışma alanınıza bağlanmak için bir betik oluşturun ve [`write_config`](/python/api/azureml-core/azureml.core.workspace.workspace#write-config-path-none--file-name-none-) dosyanızı oluşturmak ve *. azureml/config.js* olarak kaydetmek için yöntemini kullanın. `subscription_id`,, Ve ' yi `resource_group` `workspace_name` kendi ile değiştirdiğinizden emin olun.

    ```python
    from azureml.core import Workspace

    subscription_id = '<subscription-id>'
    resource_group  = '<resource-group>'
    workspace_name  = '<workspace-name>'

    try:
        ws = Workspace(subscription_id = subscription_id, resource_group = resource_group, workspace_name = workspace_name)
        ws.write_config()
        print('Library configuration succeeded')
    except:
        print('Workspace not found')
    ```

## <a name="local-computer-or-remote-vm-environment"></a><a id="local"></a>Yerel bilgisayar veya uzak VM ortamı

Yerel bir bilgisayarda veya Azure Machine Learning işlem örneği veya Veri Bilimi VM'si gibi uzak bir sanal makinede ortam ayarlayabilirsiniz. 

Yerel bir geliştirme ortamını veya uzak VM 'yi yapılandırmak için:

1. Python sanal ortamı (virtualenv, Conda) oluşturun.

    > [!NOTE]
    > Gerekli olmasa da, Python sanal ortamlarını yönetmek ve paketleri yüklemek için [Anaconda](https://www.anaconda.com/download/) veya [miniconda](https://www.anaconda.com/download/) kullanmanız önerilir.

    > [!IMPORTANT]
    > Linux veya macOS 'ta çalışıyorsanız ve Bash dışında bir Shell kullanıyorsanız (örneğin, ZSH), bazı komutları çalıştırdığınızda hatalar alabilirsiniz. Bu sorunu geçici olarak çözmek için, `bash` komutunu kullanarak yeni bir bash kabuğu başlatın ve komutları burada çalıştırın.

1. Yeni oluşturduğunuz Python sanal ortamınızı etkinleştirin.
1. [Azure Machine Learning Python SDK 'sını](/python/api/overview/azure/ml/install)yükler.
1. Yerel ortamınızı Azure Machine Learning çalışma alanınızı kullanacak şekilde yapılandırmak için, [bir çalışma alanı yapılandırma dosyası oluşturun](#workspace) veya var olan bir dosyayı kullanın.

Yerel ortamınız ayarlamış olduğunuza göre artık Azure Machine Learning çalışmaya başlamaya hazırsınız. Başlamak için [Python başlangıç kılavuzu Azure Machine Learning](tutorial-1st-experiment-sdk-setup-local.md) bakın.

### <a name="jupyter-notebooks"></a><a id="jupyter"></a>Jupyıter Not defterleri

Yerel bir Jupyter Notebook sunucusu çalıştırırken, Python sanal ortamınız için bir IPython çekirdeği oluşturmanız önerilir. Bu, beklenen çekirdek ve paket içeri aktarma davranışının sağlanmasına yardımcı olur.

1. Ortama özel IPython çekirdekler 'yi etkinleştir

    ```bash
    conda install notebook ipykernel
    ```

1. Python sanal ortamınız için bir çekirdek oluşturun. Öğesini `<myenv>` Python sanal ortamınızın adıyla değiştirdiğinizden emin olun.

    ```bash
    ipython kernel install --user --name <myenv> --display-name "Python (myenv)"
    ```

1. Jupyter Notebook sunucusunu Başlat

Azure Machine Learning ve Jupyıter not defterlerini kullanmaya başlamak için [Azure Machine Learning Not defteri deposuna](https://github.com/Azure/MachineLearningNotebooks) bakın.

> [!NOTE]
> Topluluk odaklı bir örnek deposu adresinde bulunabilir https://github.com/Azure/azureml-examples .

### <a name="visual-studio-code"></a><a id="vscode"></a>Visual Studio Code

Geliştirme için Visual Studio Code kullanmak için:

1. [Visual Studio Code](https://code.visualstudio.com/Download)'i yükler.
1. [Azure Machine Learning Visual Studio Code uzantısını](tutorial-setup-vscode-extension.md) (Önizleme) yükler.

Visual Studio Code uzantısı yüklendikten sonra, şunları yapmak için kullanın:

* [Azure Machine Learning kaynaklarınızı yönetin](how-to-manage-resources-vscode.md)
* [Azure Machine Learning işlem örneğine bağlanma](how-to-set-up-vs-code-remote.md)
* [Çalıştır ve hata ayıkla denemeleri](how-to-debug-visual-studio-code.md)
* [Eğitilen modeller dağıtın](tutorial-train-deploy-image-classification-model-vscode.md).

## <a name="azure-machine-learning-compute-instance"></a><a id="compute-instance"></a>Azure Machine Learning işlem örneği

Azure Machine Learning [işlem örneği](concept-compute-instance.md) , veri bilimcilerinin bir Jupyter Notebook sunucusu, jupi ve tam olarak yönetilen bir makine öğrenimi ortamı sağlayan, güvenli, bulut tabanlı bir Azure iş istasyonudur.

Bir işlem örneği için yüklenecek veya yapılandırılacak bir şey yok.  

Azure Machine Learning çalışma alanınızın içinden dilediğiniz zaman oluşturun. Yalnızca bir ad girin ve Azure VM türünü belirtin. Şu öğreticiyle şimdi deneyin [: Kurulum ortamı ve çalışma alanı](tutorial-1st-experiment-sdk-setup.md).

Paketlerin nasıl yükleneceğine ilişkin işlem örnekleri hakkında daha fazla bilgi edinmek için, bkz. [Azure Machine Learning işlem örneği oluşturma ve yönetme](how-to-create-manage-compute-instance.md).

> [!TIP]
> Kullanılmayan bir işlem örneği için ücretleri engellemek üzere [işlem örneğini durdurun](how-to-create-manage-compute-instance.md#manage).

Jupyter Notebook Server ve JupyterLab ' a ek olarak, [Azure Machine Learning Studio 'nun içindeki tümleşik Not defteri özelliğindeki](how-to-run-jupyter-notebooks.md)işlem örneklerini kullanabilirsiniz.

Ayrıca, [vs Code kullanarak uzak bir işlem örneğine bağlanmak](how-to-set-up-vs-code-remote.md)için Azure Machine Learning Visual Studio Code uzantısını da kullanabilirsiniz.

## <a name="data-science-virtual-machine"></a><a id="dsvm"></a>Veri Bilimi Sanal Makinesi

Veri Bilimi VM'si, geliştirme ortamı olarak kullanabileceğiniz özelleştirilmiş bir sanal makine (VM) görüntüsüdür. Şu şekilde önceden yapılandırılmış araçlar ve yazılımlar için veri bilimi çalışması için tasarlanmıştır:

  - TensorFlow, PyTorch, Scikit-öğren, XGBoost ve Azure Machine Learning SDK gibi paketler
  - Spark tek başına ve detaya gitme gibi popüler veri bilimi araçları
  - Azure CLı, AzCopy ve Depolama Gezgini gibi Azure Araçları
  - Visual Studio Code ve Pydüğme gibi tümleşik geliştirme ortamları (IDEs)
  - Jupyter Notebook sunucusu

Araçların daha kapsamlı bir listesi için [veri bilimi VM'si araçları kılavuzuna](data-science-virtual-machine/tools-included.md)bakın.

> [!IMPORTANT]
> Eğitim veya ınişsiz işleriniz için Veri Bilimi VM'si [işlem hedefi](concept-compute-target.md) olarak kullanmayı planlıyorsanız, yalnızca Ubuntu desteklenir.

Veri Bilimi VM'si bir geliştirme ortamı olarak kullanmak için:

1. Aşağıdaki yöntemlerden birini kullanarak bir Veri Bilimi VM'si oluşturun:

    * [Ubuntu](data-science-virtual-machine/dsvm-ubuntu-intro.md) veya [Windows](data-science-virtual-machine/provision-vm.md) dsvm oluşturmak için Azure Portal kullanın.
    * [ARM şablonları kullanarak bir veri bilimi VM'si oluşturun](data-science-virtual-machine/dsvm-tutorial-resource-manager.md).
    * Azure CLI kullanma

        Ubuntu Veri Bilimi VM'si oluşturmak için aşağıdaki komutu kullanın:

        ```azurecli-interactive
        # create a Ubuntu Data Science VM in your resource group
        # note you need to be at least a contributor to the resource group in order to execute this command successfully
        # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
        az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
        ```

        Bir Windows DSVM oluşturmak için aşağıdaki komutu kullanın:

        ```azurecli-interactive
        # create a Windows Server 2016 DSVM in your resource group
        # note you need to be at least a contributor to the resource group in order to execute this command successfully
        az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
        ```

1. Azure Machine Learning SDK 'sını içeren Conda ortamını etkinleştirin.

    * Ubuntu Veri Bilimi VM'si için:

        ```bash
        conda activate py36
        ```

    * Windows Veri Bilimi VM'si için:

        ```bash
        conda activate AzureML
        ```

1. Veri Bilimi VM'si Azure Machine Learning çalışma alanınızı kullanacak şekilde yapılandırmak için, [bir çalışma alanı yapılandırma dosyası oluşturun](#workspace) veya var olan bir dosyayı kullanın.

Yerel ortamlara benzer şekilde, Azure Machine Learning etkileşim kurmak için Visual Studio Code ve [Azure Machine Learning Visual Studio Code uzantısını](#vscode) kullanabilirsiniz.

Daha fazla bilgi için bkz. [veri bilimi sanal makineleri](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).


## <a name="next-steps"></a>Sonraki adımlar

- Azure Machine Learning bir modeli, veri kümesi ile birlikte [eğitme](tutorial-train-models-with-aml.md) .
- [Python başvurusu için Azure Machine Learning SDK 'sına](/python/api/overview/azure/ml/intro)bakın. 
