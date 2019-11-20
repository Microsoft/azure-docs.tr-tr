---
title: Windows 'da verileri ve modeli keşfet
titleSuffix: Azure Data Science Virtual Machine
description: Windows Veri Bilimi Sanal Makinesi veri keşif ve modelleme görevleri gerçekleştirin.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: dc8a870d692108f3a33b89a1c3826d421dfd1f63
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824393"
---
# <a name="ten-things-you-can-do-on-the-windows-data-science-virtual-machine"></a>Windows Veri Bilimi Sanal Makinesi yapabileceğiniz on işlem

Windows Veri Bilimi Sanal Makinesi (DSVM), veri keşif ve modelleme görevlerini gerçekleştirebileceğiniz, güçlü bir veri bilimi geliştirme ortamıdır. Ortam, şirket içi, bulut veya Karma dağıtımlar için analizinizi kullanmaya başlamanıza olanak sağlayan çeşitli popüler veri analizi araçlarıyla önceden oluşturulup paketlenmiştir. 

DSVM, Azure hizmetleriyle yakından birlikte çalışmaktadır. Azure SQL veri ambarı, Azure Data Lake, Azure depolama veya Azure Cosmos DB Azure 'da zaten depolanmış olan verileri okuyabilir ve işleyebilir. Ayrıca, Azure Machine Learning ve Azure Data Factory gibi diğer analiz araçlarından de faydalanabilir.

Bu makalede, veri bilimi görevlerini gerçekleştirmek ve diğer Azure hizmetleriyle etkileşim kurmak için DSVM 'nizi nasıl kullanacağınızı öğreneceksiniz. DSVM 'de yapabileceğiniz bazı şeyler aşağıda verilmiştir:

- Microsoft Machine Learning Server ve Python kullanarak DSVM 'de yerel olarak veri araştırma ve modeller geliştirme.
- Bir Jupyter Not defteri kullanarak, Python 2, Python 3 ve Microsoft R kullanarak bir tarayıcıda verilerinize denemeler yapın. (Microsoft R, bir R 'nin performans için tasarlanan kurumsal kullanıma yönelik bir sürümüdür.)
- İstemci uygulamaların modellerinize basit bir Web hizmeti arabirimi kullanarak erişebilmesi için, R ve Python ile oluşturulan modelleri Azure Machine Learning.
- Azure portal veya PowerShell 'i kullanarak Azure kaynaklarınızı yönetin.
- DSVM 'niz üzerinde bir Azure dosya paylaşımından bağlanabilir sürücü olarak depolama alanınızı genişletin ve büyük ölçekli veri kümelerini/kodları tüm ekibiniz üzerinde paylaşabilirsiniz.
- GitHub kullanarak kodu ekibinizle paylaşabilirsiniz. Önceden yüklenmiş Git istemcilerini kullanarak deponuza erişin: git Bash ve git GUI.
- Azure Blob depolama, Azure Data Lake, Azure HDInsight (Hadoop), Azure Cosmos DB, Azure SQL veri ambarı ve Azure SQL veritabanı gibi Azure verilerine ve analiz hizmetlerine erişin.
- DSVM 'de önceden yüklenmiş Power BI Desktop örneğini kullanarak raporlar ve bir pano oluşturun ve bunları buluta dağıtın.
- DSVM 'nizi projenizin ihtiyaçlarını karşılayacak şekilde dinamik olarak ölçeklendirin.
- Sanal makinenize ek araçlar yükler.   

> [!NOTE]
> Bu makalede listelenen veri depolama ve analiz hizmetlerinin birçoğu için ek kullanım ücretleri uygulanır. Ayrıntılar için bkz. [Azure fiyatlandırma](https://azure.microsoft.com/pricing/) sayfası.
> 
> 

## <a name="prerequisites"></a>Ön koşullar

* Bir Azure aboneliğine ihtiyacınız vardır. [Ücretsiz deneme için kaydolabilirsiniz](https://azure.microsoft.com/free/).
* Azure portal bir Veri Bilimi Sanal Makinesi sağlamaya yönelik yönergeler, [sanal makine oluşturma](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016)bölümünde bulunabilir.


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="explore-data-and-develop-models-with-microsoft-machine-learning-server"></a>Microsoft Machine Learning Server ile verileri araştırma ve modeller geliştirme
Veri analizinizi DSVM üzerinde doğrudan yapmak için R ve Python gibi dilleri kullanabilirsiniz.

R için, başlangıç menüsünde veya masaüstünde bulunan RStudio gibi bir IDE kullanabilirsiniz. İsterseniz Visual Studio için R Araçları de kullanabilirsiniz. Microsoft, ölçeklenebilir analizleri etkinleştirmek ve paralel öbekli çözümlemede izin verilen bellek boyutundan daha büyük verileri analiz etmek üzere açık kaynaklı CRAN R 'nin üzerine ek kitaplıklar sağladı. 

Python için, Visual Studio için Python Araçları (PTV) uzantısının önceden yüklenmiş olduğu Visual Studio Community Edition gibi bir IDE kullanabilirsiniz. Varsayılan olarak, PTV 'lerde yalnızca Python 3,6, kök Conda ortamı yapılandırılır. Anaconda Python 2,7 ' i etkinleştirmek için aşağıdaki adımları uygulayın:

1. Her **sürüm için özel ortamlar oluşturun > Python** **araçları** > **Python ortamları**' na giderek ve ardından Visual Studio Community Edition 'da **+ özel** ' i seçin.
1. Anaconda Python 2,7 için bir açıklama verin ve ortam ön eki yolunu **c:\anaconda\envs\python2** olarak ayarlayın.
1. Ortamı kaydetmek için **otomatik algıla** > **Uygula** ' yı seçin.

Python ortamları oluşturma hakkında daha fazla bilgi için bkz. [PTV belgeleri](https://aka.ms/ptvsdocs) .

Şimdi yeni bir Python projesi oluşturmak için hazırsınız. **Dosya** > **yeni** > **projesi** > **Python** ' a gidin ve oluşturmakta olduğunuz Python uygulamasının türünü seçin. Python **ortamları** ' na sağ tıklayıp **Python ortamlarını Ekle/Kaldır**' ı seçerek, geçerli projenin Python ortamını istenen sürüme (Python 2,7 veya 3,6) ayarlayabilirsiniz. [Ürün belgelerinde](https://aka.ms/ptvsdocs)PTV ile çalışma hakkında daha fazla bilgi edinebilirsiniz.

## <a name="use-jupyter-notebooks"></a>Jupyter not defterlerini kullanma
Jupyter Notebook, veri araştırması ve modelleme için tarayıcı tabanlı bir IDE sağlar. Bir Jupyter not defterinde Python 2, Python 3 veya R (açık kaynak ve Microsoft R Server) kullanabilirsiniz.

Jupyter Notebook başlatmak için **Başlat** menüsünde veya masaüstünde **Jupyter Notebook** simgesini seçin. DSVM komut isteminde, mevcut not defterleriniz veya yeni not defterleri oluşturmak istediğiniz dizinden ```jupyter notebook``` komutunu da çalıştırabilirsiniz.  

Jupyıter 'ı başlattıktan sonra, DSVM 'ye önceden paketlenmiş birkaç örnek Not defteri içeren bir dizin görmeniz gerekir. Artık şunları yapabilirsiniz:

* Kodu görmek için Not defterini seçin.
* Her hücreyi SHIFT + enter ' i seçerek çalıştırın.
* **Çalıştırılan** > **hücreyi** seçerek tüm not defterini çalıştırın.
* Jupyter simgesini (sol üst köşedeki) seçerek yeni bir not defteri oluşturun, sağdaki **Yeni** düğmesini seçin ve sonra da Not defteri dilini (kernels olarak da bilinir) seçin.   

> [!NOTE]
> Şu anda, bir Jupyter 'da Python 2,7, Python 3,6, R, Julia ve pyspark çekirdekler destekleniyor. R Kernel hem açık kaynaklı R hem de Microsoft R 'de programlamayı destekler.   
> 
> 

Not defteri 'nde olduğunuzda verilerinizi inceleyebilir, modeli oluşturabilir ve kitaplıkları tercih ettiğiniz kitaplıkları kullanarak test edebilirsiniz.

## <a name="train-and-deploy-models-by-using-azure-machine-learning"></a>Azure Machine Learning kullanarak modelleri eğitme ve dağıtma
Modelinizi oluşturup doğruladıktan sonra, bir sonraki adım genellikle üretime dağıtılır. Bu adım, istemci uygulamalarınızın gerçek zamanlı veya toplu işlem modu temelinde model tahminleri çağırmasına olanak sağlar. Azure Machine Learning, R veya Python 'da oluşturulmuş bir modeli kullanıma sunmaya yönelik bir mekanizma sağlar.

Modelinizi Azure Machine Learning ' de çalıştırdığınızda bir Web hizmeti gösterilir. İstemcilerin giriş parametrelerini geçiren ve çıkış olarak modelden tahmin alabilen REST çağrıları yapmasına olanak sağlar.

### <a name="build-and-operationalize-python-models"></a>Python modellerini derleme ve operationleştir
Aşağıda, bir Python Jupyter not defterinde geliştirilen ve Scikit-öğren kitaplığı kullanarak basit bir model oluşturan kod parçacığı verilmiştir:

```python
# IRIS classification
from sklearn import datasets
from sklearn import svm
clf = svm.SVC()
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf.fit(X, y)
```

Azure Machine Learning için Python modellerinizi dağıtmak için kullanılan yöntem, modelin tahminini bir işleve sarmalar ve önceden yüklenmiş Azure Machine Learning Python kitaplığı tarafından belirtilen özniteliklerle düzenler. Öznitelikleri Azure Machine Learning çalışma alanı KIMLIĞINIZI, API anahtarını ve giriş ve dönüş parametrelerini gösterir.  

```python
from azureml import services
@services.publish(workspaceid, auth_token)
@services.types(sep_l=float, sep_w=float, pet_l=float, pet_w=float)
@services.returns(int)  # 0, or 1, or 2
def predictIris(sep_l, sep_w, pet_l, pet_w):
    inputArray = [sep_l, sep_w, pet_l, pet_w]


return clf.predict(inputArray)
```

İstemci artık Web hizmetine çağrı yapabilir. Kullanışlı sarmalayıcılar REST API isteklerini oluşturur. Web hizmetini kullanmak için örnek kod aşağıda verilmiştir:

```python
# Consume through web service URL and keys
from azureml import services
@services.service(url, api_key)
@services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
@services.returns(float)
def IrisPredictor(sep_l, sep_w, pet_l, pet_w):
pass

IrisPredictor(3,2,3,4)
```

> [!NOTE]
> Şu anda Azure Machine Learning kitaplığı yalnızca Python 2,7 ' de desteklenir.   
> 
> 

### <a name="build-and-operationalize-r-models"></a>R modellerini derleme ve operationleştir
Veri Bilimi Sanal Makinesi veya başka bir yerde oluşturulmuş R modellerini, Python için nasıl yapıldığına benzer bir şekilde Azure Machine Learning dağıtabilirsiniz. Adımlar aşağıdaki gibidir:

1. Çalışma alanı KIMLIĞINIZI ve kimlik doğrulama belirtecinizi sağlamak için bir Settings. JSON dosyası oluşturun. 
2. Modelin tahmin işlevi için bir sarmalayıcı yazın.
3. İşlev sarmalayıcısında geçirilecek Azure Machine Learning kitaplığındaki ```publishWebService``` çağırın.  

Azure Machine Learning bir modeli bir Web hizmeti olarak ayarlamak, derlemek, yayımlamak ve kullanmak için aşağıdaki yordamı ve kod parçacıklarını kullanın.

#### <a name="set-up"></a>Kurulum

Giriş dizininiz altında ```.azureml``` adlı bir dizin altında bir Settings. JSON dosyası oluşturun. Azure Machine Learning çalışma alanınızdan parametreleri girin.

Settings. JSON dosya yapısı aşağıda verilmiştir:

```json
{"workspace":{
"id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
"authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
}}
```

#### <a name="build-a-model-in-r-and-publish-it-in-azure-machine-learning"></a>R 'de model oluşturun ve Azure Machine Learning yayımlayın

```r
library(AzureML)
ws <- workspace(config="~/.azureml/settings.json")

if(!require("lme4")) install.packages("lme4")
library(lme4)
set.seed(1)
train <- sleepstudy[sample(nrow(sleepstudy), 120),]
m <- lm(Reaction ~ Days + Subject, data = train)

# Define a prediction function to publish based on the model:
sleepyPredict <- function(newdata){
        predict(m, newdata=newdata)
}

ep <- publishWebService(ws, fun = sleepyPredict, name="sleepy lm", inputSchema = sleepstudy, data.frame=TRUE)
```

#### <a name="consume-the-model-deployed-in-azure-machine-learning"></a>Azure Machine Learning dağıtılan modeli tüketme
Modeli bir istemci uygulamasından kullanmak için, yayımlanan Web hizmetini ada göre aramak üzere Azure Machine Learning kitaplığını kullanın. Uç noktayı öğrenmek için `services` API çağrısını kullanın. Ardından `consume` işlevini çağırır ve tahmin edilecek veri çerçevesini geçitirsiniz.

Azure Machine Learning Web hizmeti olarak yayınlanan modeli kullanmak için aşağıdaki kodu kullanın:

```r
library(AzureML)
library(lme4)
ws <- workspace(config="~/.azureml/settings.json")

s <-  services(ws, name = "sleepy lm")
s <- tail(s, 1) # use the last published function, in case of duplicate function names

ep <- endpoints(ws, s)

# Try this out, and compare with raw data
ans = consume(ep, sleepstudy)$ans
```

[Machine Learning studio 'de R paketleri](/azure/machine-learning/studio-module-reference/r-packages-supported-by-azure-machine-learning)hakkında daha fazla bilgi görüntüleyin.

## <a name="manage-azure-resources"></a>Azure kaynaklarını yönetme
DSVM yalnızca, sanal makinede analiz çözümünüzü yerel olarak oluşturmanıza izin vermez. Ayrıca, Azure bulut platformunda hizmetlere erişmenize de olanak tanır. Azure, DSVM 'nizden yönetebileceğiniz ve erişebileceğiniz çeşitli işlem, depolama, veri analizi ve diğer hizmetler sağlar.

Azure aboneliğinizi ve bulut kaynaklarınızı yönetmek için iki seçeneğiniz vardır:
+ Tarayıcınızı kullanın ve [Azure Portal](https://portal.azure.com)gidin.

+ PowerShell betikleri kullanın. Azure PowerShell masaüstündeki veya **Başlat** menüsünden bir kısayoldan çalıştırın. Tüm ayrıntılar için [Microsoft Azure PowerShell belgelerine](../../powershell-azure-resource-manager.md) bakın. 

## <a name="extend-storage-by-using-shared-file-systems"></a>Paylaşılan dosya sistemlerini kullanarak depolamayı genişletme
Veri bilimcileri, büyük veri kümelerini, kodları veya ekip içindeki diğer kaynakları paylaşabilir. DSVM hakkında yaklaşık 45 GB alan mevcuttur. Depolama alanınızı genişletmek için Azure dosyalarını kullanabilir ve bir veya daha fazla DSVM örneğine bağlayabilir ya da bir REST API aracılığıyla erişebilirsiniz. Ayrıca, ek adanmış veri diskleri eklemek için [Azure Portal](../../virtual-machines/windows/attach-managed-disk-portal.md) kullanabilir veya [Azure PowerShell](../../virtual-machines/windows/attach-disk-ps.md) kullanabilirsiniz. 

> [!NOTE]
> Azure dosya paylaşımındaki en büyük alan 5 TB 'tır. Her dosya için boyut sınırı 1 TB 'tır. 

Bu betiği, Azure dosya paylaşımının oluşturulması için Azure PowerShell kullanabilirsiniz:

```powershell
# Authenticate to Azure.
Connect-AzAccount
# Select your subscription
Get-AzSubscription –SubscriptionName "<your subscription name>" | Select-AzSubscription
# Create a new resource group.
New-AzResourceGroup -Name <dsvmdatarg>
# Create a new storage account. You can reuse existing storage account if you want.
New-AzStorageAccount -Name <mydatadisk> -ResourceGroupName <dsvmdatarg> -Location "<Azure Data Center Name For eg. South Central US>" -Type "Standard_LRS"
# Set your current working storage account
Set-AzCurrentStorageAccount –ResourceGroupName "<dsvmdatarg>" –StorageAccountName <mydatadisk>

# Create an Azure Files share
$s = New-AzStorageShare <<teamsharename>>
# Create a directory under the file share. You can give it any name
New-AzStorageDirectory -Share $s -Path <directory name>
# List the share to confirm that everything worked
Get-AzStorageFile -Share $s
```

Artık bir Azure dosya paylaşımının oluşturulduğuna göre, Azure 'daki herhangi bir sanal makineye bağlayabilirsiniz. Gecikme ve veri aktarımı ücretlerinden kaçınmak için VM 'yi depolama hesabı olarak aynı Azure veri merkezine yerleştirmenizi öneririz. Aşağıda, sürücüyü DSVM 'ye bağlamak için Azure PowerShell komutları verilmiştir:

```powershell
# Get the storage key of the storage account that has the Azure Files share from the Azure portal. Store it securely on the VM to avoid being prompted in the next command.
cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

# Mount the Azure Files share as drive Z on the VM. You can choose another drive letter if you want.
net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>
```

Artık bu sürücüye, sanal makinede normal sürücü gibi erişebilirsiniz.

## <a name="share-code-in-github"></a>GitHub 'da kod paylaşma
GitHub, geliştirici topluluğu tarafından paylaşılan teknolojileri kullanarak çeşitli araçlara yönelik kod örneklerini ve kaynakları bulabileceğiniz bir kod deposudur. Kod dosyalarının sürümlerini izlemek ve depolamak için bir teknoloji olarak git 'i kullanır. GitHub Ayrıca, takımınızın paylaşılan kodunu ve belgelerini depolamak, sürüm denetimi uygulamak ve kodu görüntülemek ve katkıda bulunmak için kimin erişimi olduğunu denetlemek için kendi deponuzu oluşturabileceğiniz bir platformdur. 

Git kullanma hakkında daha fazla bilgi için [GitHub yardım sayfalarını](https://help.github.com/) ziyaret edin. GitHub 'ı ekibinizle işbirliği yapmak, topluluk tarafından geliştirilen kodu kullanmak ve kodu topluluğa geri katkıda bulunmak için kullanabileceğiniz bir şekilde kullanabilirsiniz.

DSVM, GitHub deposuna erişmek için komut satırında ve GUI 'de istemci araçları ile birlikte yüklenir. Git ve GitHub ile birlikte çalışarak komut satırı aracına git Bash adı verilir. Visual Studio, DSVM 'ye yüklenir ve git uzantılarına sahiptir. Bu araçların simgelerini **Başlangıç** menüsünde ve masaüstünde bulabilirsiniz.

Bir GitHub deposundan kodu indirmek için ```git clone``` komutunu kullanın. Örneğin, Microsoft tarafından yayınlanan veri bilimi deposunu geçerli dizine indirmek için, git Bash ' de aşağıdaki komutu çalıştırabilirsiniz:

    git clone https://github.com/Azure/DataScienceVM.git

Visual Studio 'da aynı kopyalama işlemini gerçekleştirebilirsiniz. Aşağıdaki ekran görüntüsünde, Visual Studio 'da git ve GitHub araçlarına nasıl erişebileceğiniz gösterilmektedir:

![GitHub bağlantısı görüntülenirken Visual Studio ekran görüntüsü](./media/vm-do-ten-things/VSGit.PNG)

Github.com ' de bulunan kaynaklardan GitHub deponuzla çalışmak için git kullanma hakkında daha fazla bilgi edinebilirsiniz. Tek [sayfa](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) kullanışlı bir başvurudur.

## <a name="access-azure-data-and-analytics-services"></a>Azure veri ve analiz hizmetlerine erişin
### <a name="azure-blob-storage"></a>Azure Blob depolama
Azure Blob depolama, büyük ve küçük veriler için güvenilir, ekonomik bir bulut depolama hizmetidir. Bu bölümde, blob depolamaya verileri nasıl taşıyacağınız ve bir Azure Blob 'da depolanan verilere erişim açıklanmaktadır.

#### <a name="prerequisites"></a>Ön koşullar

* [Azure Portal](https://portal.azure.com)Azure Blob Storage hesabınızı oluşturun.

   ![Azure portal depolama hesabı oluşturma işleminin ekran görüntüsü](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Komut satırı AzCopy aracının önceden yüklü olduğunu onaylayın: ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```. AzCopy. exe ' yi içeren dizin, PATH ortam değişkeninizden zaten bulunur, bu nedenle bu aracı çalıştırırken tam komut yolunu yazmaktan kaçınabilirsiniz. AzCopy aracı hakkında daha fazla bilgi için bkz. [AzCopy belgeleri](../../storage/common/storage-use-azcopy.md).
* Azure Depolama Gezgini aracını başlatın. [Depolama Gezgini Web sayfasından](https://storageexplorer.com/)indirebilirsiniz. 

   ![Bir depolama hesabına erişen Azure Depolama Gezgini ekran görüntüsü](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azcopy"></a>Bir VM 'den Azure Blob 'a veri taşıma: AzCopy

Yerel dosyalarınız ve BLOB depolama alanı arasında veri taşımak için, komut satırında veya PowerShell 'de AzCopy komutunu kullanabilirsiniz:

    AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

**C:\folder** ' ı, dosyanızın depolandığı yol, BLOB depolama hesabı adınızla **mystorageaccount** , kapsayıcı adı ile **myContainer** ve **depolama hesabı anahtarı** ile BLOB depolama erişim anahtarınızla değiştirin. Depolama hesabı kimlik bilgilerinizi [Azure Portal](https://portal.azure.com)bulabilirsiniz.

PowerShell 'de AzCopy komutunu veya bir komut isteminden çalıştırın. AzCopy komutunun bazı örnek kullanımları aşağıda verilmiştir:

```powershell
# Copy *.sql from a local machine to an Azure blob
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

# Copy back all files from an Azure blob container to a local machine

"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
```

Bir Azure blobuna kopyalamak için AzCopy komutunu çalıştırdıktan sonra, dosyanız Azure Depolama Gezgini görünür.

![Karşıya yüklenen CSV dosyasını görüntüleyen depolama hesabının ekran görüntüsü](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azure-storage-explorer"></a>Bir VM 'den Azure Blob 'a veri taşıma: Azure Depolama Gezgini

Ayrıca, Azure Depolama Gezgini kullanarak sanal makinenizde yerel dosyadaki verileri karşıya yükleyebilirsiniz:

* Bir kapsayıcıya veri yüklemek için hedef kapsayıcıyı seçin ve **karşıya yükle** düğmesini seçin. Azure Depolama Gezgini](./media/vm-do-ten-things/storage-accounts.png) karşıya yükleme düğmesinin ekran görüntüsünü![
* **Dosyalar** kutusunun sağ tarafındaki üç nokta ( **...** ) simgesini seçin, dosya sisteminden karşıya yüklenecek bir veya birden çok dosya seçin ve karşıya yükleme Işlemini başlatmak için **karşıya yükle** ' yi seçin. dosyaları karşıya yükle iletişim kutusunun ekran görüntüsünü![](./media/vm-do-ten-things/upload-files-to-blob.png)

#### <a name="read-data-from-an-azure-blob-machine-learning-reader-module"></a>Azure Blob 'dan veri okuma: Machine Learning okuyucu modülü

Azure Machine Learning Studio, blob 'ınızdan verileri okumak için verileri Içeri aktarma modülünü kullanabilirsiniz.

![Machine Learning Studio veri alma modülünün ekran görüntüsü](./media/vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

#### <a name="read-data-from-an-azure-blob-python-odbc"></a>Azure blobundan verileri okuma: Python ODBC

Bir Jupyter Not defteri 'ndeki veya bir Python programındaki bloba doğrudan veri okumak için BlobService kitaplığını kullanabilirsiniz.

İlk olarak, gerekli paketleri içeri aktarın:

```python
import pandas as pd
from pandas import Series, DataFrame
import numpy as np
import matplotlib.pyplot as plt
from time import time
import pyodbc
import os
from azure.storage.blob import BlobService
import tables
import time
import zipfile
import random
```

Sonra BLOB depolama hesabı kimlik bilgilerinizi takın ve Blobun verileri okuyun:

```python
CONTAINERNAME = 'xxx'
STORAGEACCOUNTNAME = 'xxxx'
STORAGEACCOUNTKEY = 'xxxxxxxxxxxxxxxx'
BLOBNAME = 'nyctaxidataset/nyctaxitrip/trip_data_1.csv'
localfilename = 'trip_data_1.csv'
LOCALDIRECTORY = os.getcwd()
LOCALFILE =  os.path.join(LOCALDIRECTORY, localfilename)

#download from blob
t1 = time.time()
blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILE)
t2 = time.time()
print(("It takes %s seconds to download "+BLOBNAME) % (t2 - t1))

#unzip downloaded files if needed
#with zipfile.ZipFile(ZIPPEDLOCALFILE, "r") as z:
#    z.extractall(LOCALDIRECTORY)

df1 = pd.read_csv(LOCALFILE, header=0)
df1.columns = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime','passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude']
print 'the size of the data is: %d rows and  %d columns' % df1.shape
```

Veriler veri çerçevesi olarak okundu:

![İlk 10 veri satırı ekran görüntüsü](./media/vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Azure Data Lake
Azure Data Lake Storage, büyük veri analizi iş yükleri için hiper ölçekli bir depodur ve Hadoop Dağıtılmış Dosya Sistemi (bir) ile uyumludur. Hadoop, Spark ve Azure Data Lake Analytics ile birlikte kullanılır. Bu bölümde, Azure Data Lake Analytics kullanarak Azure Data Lake Storage verileri nasıl taşıyabileceğinizi ve analizi nasıl çalıştıracağınızı öğreneceksiniz.

#### <a name="prerequisites"></a>Ön koşullar

* Azure Data Lake Analytics örneğinizi [Azure Portal](https://portal.azure.com)oluşturun.

   ![Azure portal Data Lake Analytics örneği oluşturma ekran görüntüsü](./media/vm-do-ten-things/Azure_Data_Lake_Create_v2.png)

* [Visual Studio eklentisi için Azure Data Lake ve Stream Analytics araçları](https://www.microsoft.com/download/details.aspx?id=49504) , sanal makinede Visual Studio Community Edition 'da zaten yüklüdür. Visual Studio 'Yu başlattıktan ve Azure aboneliğinizde oturum açtıktan sonra, Visual Studio 'nun sol bölmesinde Azure veri analizi hesabınızı ve depolamayı görmeniz gerekir.

   ![Visual Studio 'da Data Lake Araçları eklentisinin ekran görüntüsü](./media/vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

#### <a name="move-data-from-a-vm-to-data-lake-azure-data-lake-explorer"></a>Data Lake bir VM 'den veri taşıma: Azure Data Lake Gezgini

[Data Lake Storage sanal makinenizde yerel dosyalardaki verileri karşıya yüklemek](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)Için Azure Data Lake Gezginini kullanabilirsiniz.

Ayrıca, [Azure Data Factory](https://azure.microsoft.com/services/data-factory/)kullanarak Azure Data Lake veri taşınmanızı oluşturmak için bir veri işlem hattı da oluşturabilirsiniz. [Bu makale](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/) , veri işlem hatlarını oluşturma adımlarında size kılavuzluk eder.

#### <a name="read-data-from-an-azure-blob-to-data-lake-u-sql"></a>Azure blobundan Data Lake verileri okuma: U-SQL

Verileriniz Azure Blob depolama alanında yer alıyorsa, bir U-SQL sorgusunda Azure blobundan doğrudan veri okuyabilirsiniz. U-SQL sorgunuzu oluşturmadan önce, BLOB depolama hesabınızın Azure Data Lake örneğinize bağlı olduğundan emin olun. Azure portal gidin, Azure Data Lake Analytics panonuzu bulun, **veri kaynağı Ekle**' yi seçin, **Azure Storage**depolama türünü seçin ve Azure depolama hesabınızın adını ve anahtarını takın. Daha sonra depolama hesabında depolanan verilere başvurabilirsiniz.

![Veri kaynağı Ekle iletişim kutusunun ekran görüntüsü](./media/vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

Visual Studio 'da blob depolamadaki verileri okuyabilir, verileri işleyebilir, mühendisler ve elde edilen verileri Azure Data Lake ya da Azure Blob depolama alanına gönderebilirsiniz. Blob depolamadaki verilere başvurduğunuzda, **wasb://** kullanın. Azure Data Lake verilere başvurduğunuzda, **swbhdfs://** kullanın.

Visual Studio 'da aşağıdaki U-SQL sorgularını kullanabilirsiniz:

```usql
@a =
    EXTRACT medallion string,
            hack_license string,
            vendor_id string,
            rate_code string,
            store_and_fwd_flag string,
            pickup_datetime string,
            dropoff_datetime string,
            passenger_count int,
            trip_time_in_secs double,
            trip_distance double,
            pickup_longitude string,
            pickup_latitude string,
            dropoff_longitude string,
            dropoff_latitude string

    FROM "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Input Data File Name>"
    USING Extractors.Csv();

@b =
    SELECT vendor_id,
    COUNT(medallion) AS cnt_medallion,
    SUM(passenger_count) AS cnt_passenger,
    AVG(trip_distance) AS avg_trip_dist,
    MIN(trip_distance) AS min_trip_dist,
    MAX(trip_distance) AS max_trip_dist,
    AVG(trip_time_in_secs) AS avg_trip_time
    FROM @a
    GROUP BY vendor_id;

OUTPUT @b   
TO "swebhdfs://<Azure Data Lake Storage Account Name>.azuredatalakestore.net/<Folder Name>/<Output Data File Name>"
USING Outputters.Csv();

OUTPUT @b   
TO "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Output Data File Name>"
USING Outputters.Csv();
```

Sorgunuz sunucuya gönderildikten sonra, bir diyagram işinizin durumunu gösterir.

![İş durumu diyagramının ekran görüntüsü](./media/vm-do-ten-things/USQL_Job_Status.PNG)

#### <a name="query-data-in-data-lake-u-sql"></a>Data Lake sorgu verileri: U-SQL

Veri kümesi Azure Data Lake alındıktan sonra, verileri sorgulamak ve araştırmak için [U-SQL dilini](../../data-lake-analytics/data-lake-analytics-u-sql-get-started.md) kullanabilirsiniz. U-SQL dili T-SQL ile benzerdir, ancak kullanıcıların özelleştirilmiş modüller ve Kullanıcı C# tanımlı işlevler yazabilmesi için bazı özellikleri ' den birleştirir. Önceki adımda betikleri kullanabilirsiniz.

Sorgu sunucuya gönderildikten sonra, tripdata_summary. CSV Azure Data Lake Explorer 'da görünür. Dosyaya sağ tıklayarak verilerin önizlemesini yapabilirsiniz.

![Data Lake Explorer 'da CSV dosyasının ekran görüntüsü](./media/vm-do-ten-things/USQL_create_summary.png)

Dosya bilgileri görüntülenir:

![Dosya Özet bilgisinin ekran görüntüsü](./media/vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="hdinsight-hadoop-clusters"></a>HDInsight Hadoop kümeleri
Azure HDInsight, bulutta yönetilen bir Apache Hadoop, Spark, HBase ve fırtınası hizmetidir. Veri Bilimi Sanal Makinesi Azure HDInsight kümeleriyle kolayca çalışabilirsiniz.

#### <a name="prerequisites"></a>Ön koşullar

* [Azure Portal](https://portal.azure.com)Azure Blob Storage hesabınızı oluşturun. Bu depolama hesabı, HDInsight kümelerine yönelik verileri depolamak için kullanılır.

   ![Azure portal depolama hesabı oluşturma ekran görüntüsü](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Azure HDInsight Hadoop kümelerini [Azure Portal](../team-data-science-process/customize-hadoop-cluster.md)özelleştirin.
  
   Oluşturduğunuz zaman HDInsight kümeniz ile oluşturulan depolama hesabını bağlayın. Bu depolama hesabı, küme içinde işlenebilecek verilere erişmek için kullanılır.

   ![HDInsight kümesiyle oluşturulan depolama hesabını bağlama seçimleri](./media/vm-do-ten-things/Create_HDI_v4.PNG)

* Oluşturulduktan sonra kümenin baş düğümüne uzak masaüstü erişimini etkinleştirin. Sonraki yordamda ihtiyacınız olacak şekilde burada belirttiğiniz uzaktan erişim kimlik bilgilerini unutmayın.

   ![HDInsight kümesine uzaktan erişimi etkinleştirmek için Uzak Masaüstü düğmesi](./media/vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

* Azure Machine Learning çalışma alanı oluşturun. Machine Learning denemeleri bu Machine Learning çalışma alanında depolanır. Aşağıdaki ekran görüntüsünde gösterildiği gibi portalda vurgulanan seçenekleri seçin:

   ![Bir Azure Machine Learning çalışma alanı oluşturma](./media/vm-do-ten-things/Create_ML_Space.PNG)

* Çalışma alanınızın parametrelerini girin.

   ![Machine Learning çalışma alanı parametrelerini girin](./media/vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

* IPython Not defterini kullanarak verileri karşıya yükleyin. Gerekli paketleri içeri aktarın, kimlik bilgilerini takın, depolama hesabınızda bir veritabanı oluşturun ve ardından verileri HDI kümelerine yükleyin.

```python
# Import required packages
import pyodbc
import time as time
import json
import os
import urllib
import urllib2
import warnings
import re
import pandas as pd
import matplotlib.pyplot as plt
from azure.storage.blob import BlobService
warnings.filterwarnings("ignore", category=UserWarning, module='urllib2')


# Create the connection to Hive by using ODBC
SERVER_NAME = 'xxx.azurehdinsight.net'
DATABASE_NAME = 'nyctaxidb'
USERID = 'xxx'
PASSWORD = 'xxxx'
DB_DRIVER = 'Microsoft Hive ODBC Driver'
driver = 'DRIVER={' + DB_DRIVER + '}'
server = 'Host=' + SERVER_NAME + ';Port=443'
database = 'Schema=' + DATABASE_NAME
hiveserv = 'HiveServerType=2'
auth = 'AuthMech=6'
uid = 'UID=' + USERID
pwd = 'PWD=' + PASSWORD
CONNECTION_STRING = ';'.join(
    [driver, server, database, hiveserv, auth, uid, pwd])
connection = pyodbc.connect(CONNECTION_STRING, autocommit=True)
cursor = connection.cursor()


# Create the Hive database and tables
queryString = "create database if not exists nyctaxidb;"
cursor.execute(queryString)

queryString = """
                create external table if not exists nyctaxidb.trip
                (
                    medallion string,
                    hack_license string,
                    vendor_id string,
                    rate_code string,
                    store_and_fwd_flag string,
                    pickup_datetime string,
                    dropoff_datetime string,
                    passenger_count int,
                    trip_time_in_secs double,
                    trip_distance double,
                    pickup_longitude double,
                    pickup_latitude double,
                    dropoff_longitude double,
                    dropoff_latitude double)  
                PARTITIONED BY (month int)
                ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');
            """
cursor.execute(queryString)

queryString = """
                create external table if not exists nyctaxidb.fare
                (
                    medallion string,
                    hack_license string,
                    vendor_id string,
                    pickup_datetime string,
                    payment_type string,
                    fare_amount double,
                    surcharge double,
                    mta_tax double,
                    tip_amount double,
                    tolls_amount double,
                    total_amount double)
                PARTITIONED BY (month int)
                ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');
            """
cursor.execute(queryString)


# Upload data from Blob storage to an HDI cluster
for i in range(1, 13):
    queryString = "LOAD DATA INPATH 'wasb:///nyctaxitripraw2/trip_data_%d.csv' INTO TABLE nyctaxidb2.trip PARTITION (month=%d);" % (
        i, i)
    cursor.execute(queryString)
    queryString = "LOAD DATA INPATH 'wasb:///nyctaxifareraw2/trip_fare_%d.csv' INTO TABLE nyctaxidb2.fare PARTITION (month=%d);" % (
        i, i)
    cursor.execute(queryString)
```

Alternatif olarak, [Bu yönergeyi](../team-data-science-process/hive-walkthrough.md) , NYC TAXI VERILERINI HDI kümesine yüklemek için de izleyebilirsiniz. Ana adımlar şunlardır:
  
* Genel bloba ait yerel klasörünüze ZIP CSV 'leri indirmek için AzCopy kullanın.
* Yerel klasörden bir HDI kümesine ZIP CSV 'leri yüklemek için AzCopy kullanın.
* Hadoop kümesinin baş düğümünde oturum açın ve araştırmacı veri analizi hazırlayın.

Veriler HDI kümesine yüklendikten sonra, Azure Depolama Gezgini verilerinizi kontrol edebilirsiniz. Ve nyctaxidb veritabanı HDI kümesinde oluşturulmuştur.

#### <a name="data-exploration-hive-queries-in-python"></a>Veri araştırması: Python 'da Hive sorguları

Veriler bir Hadoop kümesinde olduğundan, araştırma ve özellik Mühendisliği yapmak üzere Hive kullanarak Hadoop kümelerine ve sorgu veritabanlarına bağlanmak için pyodbc paketini kullanabilirsiniz. Önkoşul adımında oluşturduğunuz mevcut tabloları görüntüleyebilirsiniz.

```python
queryString = """
    show tables in nyctaxidb2;
    """
pd.read_sql(queryString, connection)
```

![Mevcut tabloları görüntüle](./media/vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

Her ay içindeki kayıt sayısına ve seyahat sıklığına göre eğimli veya geçiş tablosunda olup olmadığına bakalım:

```python
queryString = """
    select month, count(*) from nyctaxidb.trip group by month;
    """
results = pd.read_sql(queryString,connection)

%matplotlib inline

results.columns = ['month', 'trip_count']
df = results.copy()
df.index = df['month']
df['trip_count'].plot(kind='bar')
```

![Her ayın kayıt sayısını çizdirme](./media/vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)

```python
queryString = """
    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;
    """
results = pd.read_sql(queryString, connection)

results.columns = ['tipped', 'trip_count']
df = results.copy()
df.index = df['tipped']
df['trip_count'].plot(kind='bar')
```

![İpucu sıklıkların çizimi](./media/vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

Ayrıca, toplama konumu ve bırakma konumu arasındaki mesafeyi de hesaplar ve ardından seyahat mesafesini karşılaştırın.

```python
queryString = """
                select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
                    3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
                    *radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
                    *cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
                    /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
                    +cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*
                    pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance
                    from nyctaxidb.trip
                    where month=1
                        and pickup_longitude between -90 and -30
                        and pickup_latitude between 30 and 90
                        and dropoff_longitude between -90 and -30
                        and dropoff_latitude between 30 and 90;
            """
results = pd.read_sql(queryString, connection)
results.head(5)
```

![Toplama ve bırakma tablosunun en üstteki satırları](./media/vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

```python
results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude',
                   'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
df = results.loc[results['trip_distance'] <= 100]  # remove outliers
df = df.loc[df['direct_distance'] <= 100]  # remove outliers
plt.scatter(df['direct_distance'], df['trip_distance'])
```

![Çekme uzaklığına ilişkin toplama/bırakma uzaklığı çizimi](./media/vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)

Şimdi, modelleme için bir aşağı örneklenmiş (yüzde 1) veri kümesi hazırlayalım. Bu verileri Machine Learning okuyucu modülünde kullanabilirsiniz.

```python
queryString = """
create  table if not exists nyctaxi_downsampled_dataset_testNEW (
medallion string,
hack_license string,
vendor_id string,
rate_code string,
store_and_fwd_flag string,
pickup_datetime string,
dropoff_datetime string,
pickup_hour string,
pickup_week string,
weekday string,
passenger_count int,
trip_time_in_secs double,
trip_distance double,
pickup_longitude double,
pickup_latitude double,
dropoff_longitude double,
dropoff_latitude double,
direct_distance double,
payment_type string,
fare_amount double,
surcharge double,
mta_tax double,
tip_amount double,
tolls_amount double,
total_amount double,
tipped string,
tip_class string
)
row format delimited fields terminated by ','
lines terminated by '\\n'
stored as textfile;
"""
cursor.execute(queryString)
```

Şimdi, önceki iç tabloya birleştirmenin içeriğini ekleyin.

```python
queryString = """
insert overwrite table nyctaxi_downsampled_dataset_testNEW
select
t.medallion,
t.hack_license,
t.vendor_id,
t.rate_code,
t.store_and_fwd_flag,
t.pickup_datetime,
t.dropoff_datetime,
hour(t.pickup_datetime) as pickup_hour,
weekofyear(t.pickup_datetime) as pickup_week,
from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
t.passenger_count,
t.trip_time_in_secs,
t.trip_distance,
t.pickup_longitude,
t.pickup_latitude,
t.dropoff_longitude,
t.dropoff_latitude,
t.direct_distance,
f.payment_type,
f.fare_amount,
f.surcharge,
f.mta_tax,
f.tip_amount,
f.tolls_amount,
f.total_amount,
if(tip_amount>0,1,0) as tipped,
if(tip_amount=0,0,
if(tip_amount>0 and tip_amount<=5,1,
if(tip_amount>5 and tip_amount<=10,2,
if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class
from
(
select
medallion,
hack_license,
vendor_id,
rate_code,
store_and_fwd_flag,
pickup_datetime,
dropoff_datetime,
passenger_count,
trip_time_in_secs,
trip_distance,
pickup_longitude,
pickup_latitude,
dropoff_longitude,
dropoff_latitude,
3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
/sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
+cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance,
rand() as sample_key

from trip
where pickup_latitude between 30 and 90
    and pickup_longitude between -90 and -30
    and dropoff_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
)t
join
(
select
medallion,
hack_license,
vendor_id,
pickup_datetime,
payment_type,
fare_amount,
surcharge,
mta_tax,
tip_amount,
tolls_amount,
total_amount
from fare
)f
on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
where t.sample_key<=0.01
"""
cursor.execute(queryString)
```

Bir süre sonra, verilerin Hadoop kümelerinde yüklendiğini görebilirsiniz:

```python
queryString = """
    select * from nyctaxi_downsampled_dataset limit 10;
    """
cursor.execute(queryString)
pd.read_sql(queryString, connection)
```

![Tablodaki en üstteki veri satırları](./media/vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)

#### <a name="read-data-from-hdi-by-using-azure-machine-learning-studio-classic-reader-module"></a>Azure Machine Learning Studio (klasik) kullanarak HDI 'den veri okuma: okuyucu modülü

Bir Hadoop kümesinde veritabanına erişmek için Azure Machine Learning Studio (klasik) ' de okuyucu modülünü de kullanabilirsiniz. HDI kümelerinde bir veritabanı kullanarak makine öğrenimi modelleri oluşturmayı etkinleştirmek için HDI kümelerinizin ve Azure depolama hesabınızın kimlik bilgilerini takın.

![Okuyucu modülü özellikleri](./media/vm-do-ten-things/AML_Reader_Hive.PNG)

Daha sonra, puanlanmış veri kümesini görüntüleyebilirsiniz:

![Puanlanmış veri kümesini görüntüle](./media/vm-do-ten-things/AML_Model_Results.PNG)

### <a name="azure-sql-data-warehouse-and-databases"></a>Azure SQL veri ambarı ve veritabanları
Azure SQL veri ambarı, kurumsal sınıf SQL Server deneyimine sahip bir hizmet olarak elastik bir veri ambarıdır.

[Bu makaledeki](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)yönergeleri IZLEYEREK Azure SQL veri Ambarınızı sağlayabilirsiniz. SQL veri Ambarınızı sağlamadıktan sonra, SQL veri ambarı içindeki verileri kullanarak veri yükleme, araştırma ve Modelleme işlemleri yapmak için [Bu](../team-data-science-process/sqldw-walkthrough.md) Kılavuzu kullanabilirsiniz.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB, buluttaki bir NoSQL veritabanıdır. Bunu, JSON gibi belgelerle çalışmak ve belgeleri depolamak ve sorgulamak için kullanabilirsiniz.

DSVM 'den Azure Cosmos DB erişmek için aşağıdaki önkoşul adımlarını kullanın:

1. Azure Cosmos DB Python SDK, DSVM üzerinde zaten yüklü. Güncelleştirmek için bir komut isteminden ```pip install pydocumentdb --upgrade``` çalıştırın.
2. [Azure Portal](https://portal.azure.com)bir Azure Cosmos DB hesabı ve veritabanı oluşturun.
3. Azure Cosmos DB veri geçiş aracı 'nı [Microsoft Indirme merkezi](https://www.microsoft.com/download/details.aspx?id=53595) ' nden indirin ve seçtiğiniz bir dizine ayıklayın.
4. Bir [genel Blobun](https://cahandson.blob.core.windows.net/samples/volcano.json) IÇINDE depolanan JSON verilerini (Volcano verileri), geçiş aracına aşağıdaki komut parametreleriyle Azure Cosmos DB içine aktarın. (Azure Cosmos DB Data Migration aracını yüklediğiniz dizinden dtuı. exe ' yi kullanın.) Şu parametrelerle kaynak ve hedef konumu girin:
   
    `/s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

Verileri içeri aktardıktan sonra Jupyter ' a gidip *Documentdbsample*adlı Not defterini açabilirsiniz. Azure Cosmos DB erişmek ve bazı temel sorgulama yapmak için Python kodu içerir. Hizmetin [Belgeler sayfasını](https://docs.microsoft.com/azure/cosmos-db/)ziyaret ederek Azure Cosmos DB hakkında daha fazla bilgi edinebilirsiniz.

## <a name="use-power-bi-reports-and-dashboards"></a>Power BI raporlarını ve panoları kullanma 
Verilerle ilgili görsel Öngörüler elde etmek için Power BI Desktop önceki Azure Cosmos DB örneğinde bulunan Volcano JSON dosyasını görselleştirebilirsiniz. Ayrıntılı adımlar [Power BI makalesinde](../../cosmos-db/powerbi-visualize.md)bulunabilir. Üst düzey adımlar şunlardır:

1. Power BI Desktop açın ve **veri al**' ı seçin. URL 'YI şu şekilde belirtin: https://cahandson.blob.core.windows.net/samples/volcano.json.
2. Liste olarak içeri aktarılmış JSON kayıtlarını görmeniz gerekir. Power BI onunla birlikte çalışabilmek için listeyi bir tabloya dönüştürün.
4. Genişlet (ok) simgesini seçerek sütunları genişletin.
5. Konumun bir **kayıt** alanı olduğuna dikkat edin. Kaydı genişletin ve yalnızca koordinatları seçin. **Koordinat** bir liste sütunudur.
6. Liste koordinat sütununu, virgülle ayrılmış bir **LatLong** sütuna dönüştürmek için yeni bir sütun ekleyin. ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```formülünü kullanarak koordinat listesi alanındaki iki öğeyi birleştirir.
7. **Yükseltme** sütununu Decimal olarak dönüştürdükten sonra **Kapat** ve **Uygula** düğmelerini seçin.

Önceki adımlar yerine aşağıdaki kodu yapıştırabilirsiniz. Veri dönüştürmelerini bir sorgu dilinde yazmak için Power BI Gelişmiş Düzenleyici kullanılan adımları betikler.

```pqfl
let
    Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
    #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
    #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
    #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
    #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
in
    #"Changed Type"
```

Artık Power BI veri modelinizdeki verilere sahipsiniz. Power BI Desktop örneğiniz aşağıdaki gibi görünmelidir:

![Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

Veri modelini kullanarak raporlar ve görselleştirmeler oluşturmaya başlayabilirsiniz. [Bu Power BI makalesindeki](../../cosmos-db/powerbi-visualize.md#build-the-reports) adımları izleyerek bir rapor oluşturabilirsiniz.

## <a name="scale-the-dsvm-dynamically"></a>DSVM 'YI dinamik olarak ölçeklendirin 
Uygulamanızın ihtiyaçlarını karşılamak için DSVM 'nin ölçeğini değiştirebilir ve azaltabilirsiniz. Sanal makineyi akşam veya hafta sonları üzerinde kullanmanız gerekmiyorsa, [Azure Portal](https://portal.azure.com)VM 'yi kapatabilirsiniz.

> [!NOTE]
> VM 'deki işletim sistemi için yalnızca kapalı düğmesini kullanırsanız işlem ücretlerine tabi olursunuz.  
> 
> 

Büyük ölçekli analizler oluşturmanız ve daha fazla CPU, bellek veya disk kapasitesi yapmanız gerekebilir. Bu durumda, CPU çekirdekleri, ayrıntılı öğrenme için GPU tabanlı örnekler, bellek kapasitesi ve işlem ve budgetary ihtiyaçlarınızı karşılayan disk türleri (katı hal sürücüleri dahil) açısından bir sanal makine boyutu seçimi bulabilirsiniz. VM 'lerin tam listesi, saatlik işlem fiyatlandırmasıyla birlikte [Azure sanal makineler fiyatlandırma](https://azure.microsoft.com/pricing/details/virtual-machines/) sayfasında kullanılabilir.

Benzer şekilde, VM işleme kapasitesi için ihtiyacınız olan işlem ağır bir şekilde azalmayabilir. (Örneğin: bir Hadoop veya Spark kümesine büyük bir iş yükü taşıdınız.) Daha sonra [Azure Portal](https://portal.azure.com) kümenin ölçeğini değiştirebilir ve sanal makine örneğinizin ayarlarına gidebilirsiniz. 

## <a name="add-more-tools"></a>Daha fazla araç ekleyin
DSVM 'de önceden yerleşik olarak bulunan araçlar birçok ortak veri analizi ihtiyaçlarını ele alabilir. Ortamlarınızı tek tek yükleyip yapılandırmanız gerekmiyorsa bu size zaman kazandırır. Yalnızca kullandığınız kaynaklar için ödeme yaptığınız için de paradan tasarruf etmenizi sağlar.

Analiz ortamınızı geliştirmek için bu makalede profili oluşturulan diğer Azure veri ve analiz hizmetlerini kullanabilirsiniz. Bazı durumlarda, bazı özel iş ortağı araçları da dahil olmak üzere ek araçlara ihtiyacınız vardır. İhtiyaç duyduğunuz yeni araçları yüklemek için sanal makinede tam yönetici erişiminiz vardır. Python ve R 'ye önceden yüklenmemiş ek paketler de yükleyebilirsiniz. Python için ```conda``` ya da ```pip```kullanabilirsiniz. R için, R konsolundaki ```install.packages()``` kullanabilir veya IDE 'yi kullanabilir ve paketleri **yükleyebilir** > **paketleri** seçebilirsiniz.

## <a name="deep-learning"></a>Derin öğrenme

Framework tabanlı örneklere ek olarak, DSVM 'de doğrulanan kapsamlı bir izlenecek yol kümesi alabilirsiniz. Bu izlenecek yollar, görüntü ve metin/dil anlama gibi etki alanlarında derin öğrenme uygulamalarınızı geliştirmeye başlamanıza yardımcı olur.   


- [Farklı çerçeveler arasında sinir ağlarını çalıştırma](https://github.com/ilkarman/DeepLearningFrameworks): Bu izlenecek yol, kodun bir çerçeveden diğerine nasıl ekleneceğini gösterir. Ayrıca, modeller ve çalışma zamanı performansının çerçeveler arasında nasıl karşılaştırılacağını gösterir. 

- Görüntü [içindeki ürünleri algılamaya yönelik uçtan uca bir çözüm oluşturmak için nasıl yapılır Kılavuzu](https://github.com/Azure/cortana-intelligence-product-detection-from-images): görüntü algılama, resimleri içindeki nesneleri bulup sınıflandırmanın bir tekniğidir. Bu teknoloji, çok sayıda gerçek hayatta iş etki alanında büyük bir rekele sunmayı mümkün hale getirir. Örneğin, satıcılar bu tekniği kullanarak bir müşterinin rafı hangi ürüne kullandığını tespit edebilir. Sırasıyla bu bilgiler, ürün envanterini yönetme mağazalarına yardımcı olur. 

- [Ses Için derin öğrenme](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/): Bu öğreticide, [kentsel sesler veri kümesinde](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html)ses olayı algılaması için derin öğrenme modelinin nasıl eğeceği gösterilmektedir. Ayrıca, ses verileriyle çalışma hakkında genel bakış sağlar.

- [Metin belgelerinin sınıflandırılması](https://github.com/anargyri/lstm_han): Bu kılavuzda iki sinir ağ mimarisi oluşturma ve eğitme gösterilmektedir: hiyerarşik dikkat ağı ve uzun kısa dönem belleğı (lstm) ağı. Bu sinir Networks, metin belgelerinin sınıflandırılmasında derin öğrenme için keras API 'sini kullanır. Keras, en popüler derin öğrenme çerçevelerinin üçüne (Microsoft Cognitive Toolkit, TensorFlow ve Ano) önde bir ön ucu.

## <a name="summary"></a>Özet
Bu makalede, Microsoft Veri Bilimi Sanal Makinesi 'de yapabileceğiniz bazı şeyler açıklanmaktadır. DSVM 'yi etkin bir analiz ortamı yapmak için yapabileceğiniz çok daha fazla şey vardır.

