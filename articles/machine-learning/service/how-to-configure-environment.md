---
title: Python geliştirme ortamı ayarlama
titleSuffix: Azure Machine Learning
description: Azure Machine Learning için geliştirme ortamınızı yapılandırmayı öğrenin. Conda ortamlarını kullanın, yapılandırma dosyaları oluşturun ve bulut tabanlı Not defteri sunucunuzu, Jupyter not defterlerini, Azure Databricks, IDEs, kod düzenleyicilerini ve Veri Bilimi Sanal Makinesi yapılandırın.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 10/25/2019
ms.custom: seodec18
ms.openlocfilehash: 19045b54b97fdb69f9fdab3d17066faa5dbcc435
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580729"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Azure Machine Learning için bir geliştirme ortamı yapılandırma
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Bu makalede, Azure Machine Learning ile çalışmak için bir geliştirme ortamının nasıl yapılandırılacağını öğreneceksiniz. Azure Machine Learning platform belirsiz. Geliştirme ortamınız için tek sabit gereksinim Python 3 ' dir. Anaconda veya virtualenv gibi yalıtılmış bir ortam de önerilir.

Aşağıdaki tabloda, bu makalede ele alınan her geliştirme ortamı, profesyonelleri ve dezavantajlarla birlikte gösterilmektedir.

| Ortam | Ları | Simgeler |
| --- | --- | --- |
| [Bulut tabanlı Azure Machine Learning Not defteri VM 'si](#notebookvm) | Başlamak için en kolay yol. Tüm SDK, çalışma alanı sanal makinenizde zaten yüklüdür ve Not defteri öğreticileri önceden klonlanır ve çalıştırılmaya hazırlanmıştır. | Geliştirme ortamınız ve bağımlılıklarınız üzerinde denetim olmaması. Linux VM için ek maliyet (sanal makine, ücretlerden kaçınmak için kullanımda olmadığında durdurulabilir). [Fiyatlandırma ayrıntılarına](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) bakın. |
| [Yerel ortam](#local) | Geliştirme ortamınızın ve bağımlılıklarınızın tam denetimi. İstediğiniz herhangi bir yapı aracı, ortam veya IDE ile çalıştırın. | Kullanmaya başlamak için daha uzun sürer. Gerekli SDK paketlerinin yüklü olması ve henüz yoksa bir ortamın de yüklü olması gerekir. |
| [Azure Databricks](#aml-databricks) | Ölçeklenebilir Apache Spark platformunda büyük ölçekli yoğun makine öğrenimi iş akışlarını çalıştırmak için idealdir. | Deneysel makine öğrenimi veya daha küçük ölçekli denemeleri ve iş akışları için fazla sonlandırılmalıdır. Azure Databricks için ek ücret tahakkuk ettir. [Fiyatlandırma ayrıntılarına](https://azure.microsoft.com/pricing/details/databricks/) bakın. |
| [Veri Bilimi Sanal Makinesi (DSVM)](#dsvm) | Bulut tabanlı Not defteri VM 'sine benzer (Python ve SDK önceden yüklenmiş), ancak daha popüler veri bilimi ve Machine Learning araçları önceden yüklenmiş olarak. Kolayca ölçeklendirilmesi ve diğer özel araçlar ve iş akışlarıyla birleştirmek. | Bulut tabanlı Notebook VM ile karşılaştırıldığında daha yavaş bir başlangıç deneyimi. |


Bu makalede ayrıca aşağıdaki araçlar için ek kullanım ipuçları sunulmaktadır:

* [Jupyter Not defterleri](#jupyter): zaten JUPYTER Notebook kullanıyorsanız SDK 'nın yüklenmesi gereken bazı ek özellikler vardır.

* [Visual Studio Code](#vscode): Visual Studio Code kullanıyorsanız, [Azure Machine Learning uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) Python için kapsamlı dil desteği ve Azure Machine Learning daha kolay ve üretken bir şekilde çalışmayı sağlayacak özellikler içerir.

## <a name="prerequisites"></a>Ön koşullar

Azure Machine Learning çalışma alanı. Çalışma alanını oluşturmak için, bkz. [Azure Machine Learning çalışma alanı oluşturma](how-to-manage-workspace.md). [Bulut tabanlı bir not defteri sunucusu](#notebookvm), [dsvm](#dsvm)veya [Azure Databricks](#aml-databricks)kullanmaya başlamak için bir çalışma alanı yeterlidir.

[Yerel bilgisayarınız](#local)için SDK ortamını yüklemek üzere [Jupyter Notebook sunucu](#jupyter) veya [Visual Studio Code](#vscode) şunları yapmanız gerekir:

- [Anaconda](https://www.anaconda.com/download/) ya da [miniconda](https://conda.io/miniconda.html) Paket Yöneticisi.

- Linux veya macOS 'da bash kabuğu gerekir.

    > [!TIP]
    > Linux veya macOS 'ta çalışıyorsanız ve Bash dışında bir Shell kullanıyorsanız (örneğin, ZSH), bazı komutları çalıştırdığınızda hatalar alabilirsiniz. Bu sorunu geçici olarak çözmek için `bash` komutunu kullanarak yeni bir bash kabuğu başlatın ve komutları burada çalıştırın.

- Windows 'ta, komut istemi veya Anaconda istemi (Anaconda ve Miniconda tarafından yüklenir) gerekir.

## <a id="notebookvm"></a>Kendi bulut tabanlı Not defteriniz VM 'niz

Azure Machine Learning Not defteri VM 'si, bir Jupyter Not defteri sunucusu, JupyterLab ve tamamen hazırlanmış ML ortamı ile veri bilimcileri sağlayan, güvenli, bulut tabanlı bir Azure iş istasyonudur.

Not defteri VM 'si:

Bir işlem örneği için yüklenecek veya yapılandırılacak bir şey yok.  Azure Machine Learning çalışma alanınızın içinden dilediğiniz zaman oluşturun. Yalnızca bir ad girin ve Azure VM türünü belirtin. Şu öğreticiyle şimdi deneyin [: Kurulum ortamı ve çalışma alanı](tutorial-1st-experiment-sdk-setup.md).


İşlem ücretlerini durdurmak için, [Not defteri VM 'yi durdurun](tutorial-1st-experiment-sdk-train.md#clean-up-resources).

## <a id="dsvm"></a>Veri Bilimi Sanal Makinesi

DSVM, özelleştirilmiş bir sanal makine (VM) görüntüsüdür. Önceden yapılandırılmış olan veri bilimi işleri için tasarlanmıştır:

  - TensorFlow, PyTorch, Scikit-öğren, XGBoost ve Azure Machine Learning SDK gibi paketler
  - Spark tek başına ve detaya gitme gibi popüler veri bilimi araçları
  - Azure CLı, AzCopy ve Depolama Gezgini gibi Azure Araçları
  - Visual Studio Code ve Pydüğme gibi tümleşik geliştirme ortamları (IDEs)
  - Jupyter Notebook sunucusu

Azure Machine Learning SDK, DSVM 'nin Ubuntu veya Windows sürümünde kullanılabilir. Ancak DSVM 'yi bir işlem hedefi olarak kullanmayı planlıyorsanız yalnızca Ubuntu desteklenir.

DSVM 'yi bir geliştirme ortamı olarak kullanmak için:

1. Aşağıdaki ortamların birinde bir DSVM oluşturun:

    * Azure portal:

        * [Ubuntu Veri Bilimi Sanal Makinesi oluşturma](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Windows Veri Bilimi Sanal Makinesi oluşturma](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * Azure CLı:

        > [!IMPORTANT]
        > * Azure CLı 'yı kullandığınızda, önce `az login` komutunu kullanarak Azure aboneliğinizde oturum açmanız gerekir.
        >
        > * Bu adımdaki komutları kullandığınızda, bir kaynak grubu adı, VM için bir ad, Kullanıcı adı ve parola sağlamanız gerekir.

        * Ubuntu Veri Bilimi Sanal Makinesi oluşturmak için aşağıdaki komutu kullanın:

            ```azurecli
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Bir Windows Veri Bilimi Sanal Makinesi oluşturmak için aşağıdaki komutu kullanın:

            ```azurecli
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```

2. Azure Machine Learning SDK DSVM 'de zaten yüklü. SDK 'Yı içeren Conda ortamını kullanmak için aşağıdaki komutlardan birini kullanın:

    * Ubuntu DSVM için:

        ```shell
        conda activate py36
        ```

    * Windows DSVM için:

        ```shell
        conda activate AzureML
        ```

1. SDK 'ya erişip sürümü denetbildiğinizi doğrulamak için aşağıdaki python kodunu kullanın:

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. DSVM 'yi Azure Machine Learning çalışma alanınızı kullanacak şekilde yapılandırmak için, [çalışma alanı yapılandırma dosyası oluşturma](#workspace) bölümüne bakın.

Daha fazla bilgi için bkz. [veri bilimi sanal makineleri](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a id="local"></a>Yerel bilgisayar

Yerel bir bilgisayar (Ayrıca, uzak bir sanal makine de olabilir) kullandığınızda, bir Anaconda ortamı oluşturun ve SDK 'Yı kurun. Bir örneği aşağıda verilmiştir:

1. Henüz yoksa [Anaconda](https://www.anaconda.com/distribution/#download-section) (Python 3,7 sürümü) sürümünü indirip yükleyin.

1. Bir Anaconda istemi açın ve aşağıdaki komutları kullanarak bir ortam oluşturun:

    Ortamı oluşturmak için aşağıdaki komutu çalıştırın.

    ```shell
    conda create -n myenv python=3.6.5
    ```

    Ardından ortamı etkinleştirin.

    ```shell
    conda activate myenv
    ```

    Bu örnek, Python 3.6.5 kullanarak bir ortam oluşturur, ancak herhangi bir belirli alt sürüm seçilebilir. SDK uyumluluğu belirli ana sürümlerle garanti edilemez (3,5 + önerilir) ve hatalar halinde çalıştırırsanız, Anaconda ortamınızda farklı bir sürüm/alt sürüm denemek önerilir. Bileşenler ve paketler indirilirken ortamın oluşturulması birkaç dakika sürer.

1. Ortama özel IPython kernels özelliğini etkinleştirmek için yeni ortamınızda aşağıdaki komutları çalıştırın. Bu, Anaconda ortamları içinde Jupyter Not defterleri ile çalışırken beklenen çekirdek ve paket içeri aktarma davranışının sağlanması gerekir:

    ```shell
    conda install notebook ipykernel
    ```

    Ardından, aşağıdaki komutu çalıştırarak çekirdeği oluşturun:

    ```shell
    ipython kernel install --user
    ```

1. Paketleri yüklemek için aşağıdaki komutları kullanın:

    Bu komut, temel Azure Machine Learning SDK 'sını Not defteri ve `automl` ek özellikler ile birlikte kurar. `automl` ekstra büyük bir yüklemedir ve otomatik makine öğrenimi denemeleri çalıştırmayı düşünmüyorsanız ayraçlardan kaldırılabilir. `automl` ekstra, varsayılan olarak bir bağımlılık olarak Azure Machine Learning Data Prep SDK 'sını içerir.

    ```shell
    pip install azureml-sdk[notebooks,automl]
    ```

   > [!NOTE]
   > * PyYAML 'nin kaldırılamayacağı bir ileti alırsanız, bunun yerine aşağıdaki komutu kullanın:
   >
   >   `pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML`
   >
   > * MacOS Catalina ile başlayarak, ZSH (Z kabuğu), varsayılan oturum açma kabuğu ve etkileşimli kabuktur. ZSH 'de, "\\" (ters eğik çizgi) ile köşeli ayraçları kaçış aşağıdaki komutu kullanın:
   >
   >   `pip install --upgrade azureml-sdk\[notebooks,automl\]`


   SDK 'nın yüklenmesi birkaç dakika sürer. Yükleme seçenekleri hakkında daha fazla bilgi için bkz. [Yükleme Kılavuzu](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

1. Machine Learning deneme için diğer paketleri yükler.

    Aşağıdaki komutlardan birini kullanın ve *\<yeni paket >* yüklemek istediğiniz paketle değiştirin. Paketlerin `conda install` aracılığıyla yüklenmesi için paketin geçerli kanalların bir parçası olması gerekir (yeni kanallar Anaconda buluta eklenebilir).

    ```shell
    conda install <new package>
    ```

    Alternatif olarak, paketleri `pip`aracılığıyla da yükleyebilirsiniz.

    ```shell
    pip install <new package>
    ```

### <a id="jupyter"></a>Jupyıter Not defterleri

Jupi Not defterleri [Jupyıter projesinin](https://jupyter.org/)bir parçasıdır. Bunlar, anlatıcı metin ve grafiklerle canlı kodu karıştırabileceğiniz belgeler oluşturduğunuz etkileşimli bir kodlama deneyimi sağlar. Jupi Not defterleri, kod bölümlerinin çıktısını belgeye kaydedebilmeniz için sonuçlarınızı başkalarıyla paylaşmanın harika bir yoludur. Jupyıter not defterlerini çeşitli platformlarda yükleyebilirsiniz.

[Yerel bilgisayar](#local) bölümündeki yordam, Jupyıter not defterlerini bir Anaconda ortamında çalıştırmak için gerekli bileşenleri yüklüyor.

Jupyter Notebook ortamınızda bu bileşenleri etkinleştirmek için:

1. Bir Anaconda istemi açın ve ortamınızı etkinleştirin.

    ```shell
    conda activate myenv
    ```

1. Örnek bir not defteri kümesi için [GitHub deposunu](https://aka.ms/aml-notebooks) kopyalayın.

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Aşağıdaki komutla Jupyter Notebook sunucusunu başlatın:

    ```shell
    jupyter notebook
    ```

1. Jupyter Notebook SDK 'Yı kullanıp kullanbildiğini doğrulamak için **Yeni** bir not defteri oluşturun, çekirdekte **Python 3** ' ü seçin ve ardından bir not defteri hücresinde aşağıdaki komutu çalıştırın:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Modüller içeri aktarma ve bir `ModuleNotFoundError`alma sorunlarıyla karşılaşırsanız, bir not defteri hücresinde aşağıdaki kodu çalıştırarak Jupyter çekirdeğin ortamınız için doğru yola bağlı olduğundan emin olun.

    ```python
    import sys
    sys.path
    ```

1. Jupyter Notebook Azure Machine Learning çalışma alanınızı kullanacak şekilde yapılandırmak için, [çalışma alanı yapılandırma dosyası oluşturma](#workspace) bölümüne gidin.


### <a id="vscode"></a>Visual Studio Code

Visual Studio Code, [Visual Studio marketi](https://marketplace.visualstudio.com/vscode)'nde bulunan uzantılar aracılığıyla kapsamlı bir programlama dili ve araç kümesini destekleyen çok popüler bir platformlar arası kod düzenleyicisidir. [Azure Machine Learning uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) , Python ortamlarının her türlü (sanal, Anaconda vb.) kodlama için [Python uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-python.python) yüklüyor. Buna ek olarak, Azure Machine Learning kaynaklarla çalışmak ve Visual Studio Code çıkmadan Azure Machine Learning denemeleri çalıştırmak için size kolay özellikler sağlar.

Geliştirme için Visual Studio Code kullanmak için:

1. Visual Studio Code için Azure Machine Learning uzantısını yükleyip bkz. [Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

    Daha fazla bilgi için bkz. [Visual Studio Code için Azure Machine Learning kullanma](how-to-vscode-tools.md).

1. Visual Studio Code herhangi bir Python geliştirme türü için nasıl kullanacağınızı öğrenin. bkz. [VSCode 'Da Python ile çalışmaya başlama](https://code.visualstudio.com/docs/python/python-tutorial).

    - SDK 'yı içeren SDK Python ortamını seçmek için VS Code açın ve ardından CTRL + SHIFT + P (Linux ve Windows) veya komut + SHIFT + P (Mac) öğesini seçin.
        - __Komut paleti__ açılır.

    - __Python girin: yorumlayıcı__' yı seçin ve ardından uygun ortamı seçin

1. SDK 'yı kullanacağınızı doğrulamak için, aşağıdaki kodu içeren yeni bir Python dosyası (. köpek) oluşturun:

    ```python
    #%%
    import azureml.core
    azureml.core.VERSION
    ```
    "Hücreyi Çalıştır" CodeLens ' e tıklayarak bu kodu çalıştırın veya yalnızca SHIFT-enter tuşlarına basın.
<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks
Azure Databricks, Azure bulutu 'nda Apache Spark tabanlı bir ortamdır. CPU veya GPU tabanlı işlem kümesi ile birlikte çalışan bir not defteri tabanlı ortam sağlar.

Azure Databricks Azure Machine Learning ile nasıl kullanılır:
+ Spark MLlib kullanarak bir modeli eğitebilirsiniz ve modeli Azure Databricks içinden ACG/AKS 'e dağıtabilirsiniz.
+ Ayrıca, Azure Databricks ile özel bir Azure ML SDK 'sında [otomatik makine öğrenimi](concept-automated-ml.md) özellikleri de kullanabilirsiniz.
+ Azure Databricks, bir [Azure Machine Learning](concept-ml-pipelines.md)işlem hattından işlem hedefi olarak kullanabilirsiniz.

### <a name="set-up-your-databricks-cluster"></a>Databricks kümenizi ayarlama

[Databricks kümesi](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)oluşturun. Bazı ayarlar yalnızca Databricks 'te otomatik makine öğrenimi için SDK 'Yı yüklerseniz geçerlidir.
**Kümenin oluşturulması birkaç dakika sürer.**

Şu ayarları kullanın:

| Ayar |Uygulama hedefi| Değer |
|----|---|---|
| Küme adı |Her| yourclustername |
| Databricks Çalışma Zamanı |Her|ML olmayan çalışma zamanı 6,0 (Scala 2,11, Spark 2.4.3) |
| Python sürümü |Her| 3 |
| Çalışanlarınız |Her| 2 veya üzeri |
| Çalışan düğümü VM türleri <br>(en fazla eşzamanlı yineleme sayısını belirler) |Otomatikleştirilmiş ML<br>yalnızca| Bellek için iyileştirilmiş VM tercih edilen |
| Otomatik Ölçeklendirmeyi Etkinleştirme |Otomatikleştirilmiş ML<br>yalnızca| Kutunun |

Devam etmeden önce küme çalışmaya kadar bekleyin.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>Databricks kitaplığına doğru SDK 'Yı yükler
Küme çalışmaya başladıktan sonra uygun Azure Machine Learning SDK paketini kümenize eklemek için [bir kitaplık oluşturun](https://docs.databricks.com/user-guide/libraries.html#create-a-library) .

1. Kitaplığı depolamak istediğiniz geçerli çalışma alanı klasörüne sağ tıklayın.  > **kitaplığı** **Oluştur** ' u seçin.

1. **Yalnızca bir** seçenek belirleyin (diğer SDK yüklemesi desteklenmez)

   |SDK&nbsp;paketi&nbsp;ek özellikler|Kaynak|Pypı&nbsp;adı&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Databricks için| Python Yumurg veya Pypı yükleme | azureml-SDK [databricks]|
   |Databricks için-ile<br> Otomatik ML özellikleri| Python Yumurg veya Pypı yükleme | azureml-SDK [Oto ml]|

   > [!Warning]
   > Başka SDK ek özellikleri yüklenemez. Yukarıdaki seçeneklerden yalnızca birini seçin [databricks] veya [Oto ml].

   * **Tüm kümelere otomatik olarak ekle**' yi seçmeyin.
   * Küme adınızın yanındaki **Ekle** ' yi seçin.

1. Durum **iliştirilene**kadar, bu işlem birkaç dakika sürebilir.  Bu adım başarısız olursa:

   Kümenizi şu şekilde yeniden başlatmayı deneyin:
   1. Sol bölmede **kümeler**' ı seçin.
   1. Tabloda, küme adınızı seçin.
   1. **Kitaplıklar** sekmesinde **Yeniden Başlat**' ı seçin.

   Ayrıca şunları göz önünde bulundurun:
   + Azure Databricks kullanırken, oto ml yapılandırmasında aşağıdaki parametreleri ekleyin:
       1. ```max_concurrent_iterations``` kümenizdeki çalışan düğüm sayısına bağlıdır.
        2. ```spark_context=sc```, varsayılan Spark bağlamını temel alır.
   + Ya da eski bir SDK sürümünüz varsa, kümenin yüklü olan kitaplıkların seçimini kaldırın ve çöp kutusuna geçiş yapın. Yeni SDK sürümünü yükleyip kümeyi yeniden başlatın. Yeniden başlatmadan sonra bir sorun varsa, kümenizi ayırın ve yeniden bağlayın.

Yüklemesi başarılı olduysa, içeri aktarılan kitaplık aşağıdakilerden biri gibi görünmelidir:

Databricks için Azure Machine Learning SDK ![otomatik makine öğrenimi **_olmadan_** Databricks sdk 'sı](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

Databricks 'de yüklü otomatik makine öğrenimi ile otomatik makine öğrenimi ![SDK **Ile** databricks için SDK](./media/how-to-configure-environment/automlonadb.png)

### <a name="start-exploring"></a>Keşfetmeye başlayın

Deneyin:
+ Birçok örnek Not defteri kullanılabilir, **ancak [Bu örnek Not defterleri](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) yalnızca Azure Databricks çalışır.**

+ Bu örnekleri doğrudan çalışma alanınızdan içeri aktarın. Aşağıya bakın: ![içeri aktar](media/how-to-configure-environment/azure-db-screenshot.png)
![Içeri aktarma paneli ' ni seçin](media/how-to-configure-environment/azure-db-import.png)

+ [Eğitim işlemi olarak Databricks ile bir işlem hattı oluşturmayı](how-to-create-your-first-pipeline.md)öğrenin.

## <a id="workspace"></a>Çalışma alanı yapılandırma dosyası oluşturma

Çalışma alanı yapılandırma dosyası, SDK 'nın Azure Machine Learning çalışma alanıyla nasıl iletişim kuracağını söyleyen bir JSON dosyasıdır. Dosya *config. JSON*olarak adlandırılır ve aşağıdaki biçimdedir:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Bu JSON dosyası, Python betikleri veya Jupyıter not defterlerini içeren dizin yapısında olmalıdır. Aynı dizinde, *. azureml*adlı bir alt dizin veya bir üst dizin içinde olabilir.

Bu dosyayı kodunuzda kullanmak için `ws=Workspace.from_config()`kullanın. Bu kod, dosyadaki bilgileri yükler ve çalışma alanınıza bağlanır.

Yapılandırma dosyasını üç şekilde oluşturabilirsiniz:

* Bir *config. JSON* dosyası yazmak için  **[ws. write_config](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py): kullanın**. Dosya, çalışma alanınızın yapılandırma bilgilerini içerir. *Config. json dosyasını* diğer geliştirme ortamlarına indirebilir veya kopyalayabilirsiniz.

* **Dosyayı indirin**: [Azure Portal](https://ms.portal.azure.com), çalışma alanınızın **genel bakış** bölümünde **config. json dosyasını indir** ' i seçin.

     ![Azure portal](./media/how-to-configure-environment/configure.png)

* **Dosyayı program aracılığıyla oluşturun**: Aşağıdaki kod parçacığında, abonelik kimliği, kaynak grubu ve çalışma alanı adı sağlayarak bir çalışma alanına bağlanırsınız. Daha sonra çalışma alanı yapılandırmasını dosyaya kaydeder:

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

    Bu kod, yapılandırma dosyasını *. azureml/config. JSON* dosyasına yazar.


## <a name="next-steps"></a>Sonraki adımlar

- Azure Machine Learning model veri kümesiyle [bir modeli eğitme](tutorial-train-models-with-aml.md)
- Python başvurusu [için Azure Machine Learning SDK 'sını](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) görüntüleme
