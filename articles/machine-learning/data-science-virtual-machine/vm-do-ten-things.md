---
title: Windows 'da verileri ve modeli keşfet
titleSuffix: Azure Data Science Virtual Machine
description: Windows Veri Bilimi Sanal Makinesi veri keşif ve modelleme görevleri gerçekleştirin.
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: c44c40f2ddab53cf0beb5ecd48127d126fdb4333
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "101657409"
---
# <a name="data-science-with-a-windows-data-science-virtual-machine"></a>Windows Veri Bilimi Sanal Makinesi ile veri bilimi

Windows Veri Bilimi Sanal Makinesi (DSVM), veri keşif ve modelleme görevlerini gerçekleştirebileceğiniz, güçlü bir veri bilimi geliştirme ortamıdır. Ortam, şirket içi, bulut veya Karma dağıtımlar için analizinizi kullanmaya başlamanıza olanak sağlayan çeşitli popüler veri analizi araçlarıyla önceden oluşturulup paketlenmiştir. 

DSVM, Azure hizmetleriyle yakından birlikte çalışmaktadır. Azure 'da, Azure Synapse (eski adıyla SQL DW), Azure Data Lake, Azure depolama veya Azure Cosmos DB daha önce depolanan verileri okuyabilir ve işleyebilir. Ayrıca, Azure Machine Learning gibi diğer analiz araçlarından de faydalanabilir.

Bu makalede, veri bilimi görevlerini gerçekleştirmek ve diğer Azure hizmetleriyle etkileşim kurmak için DSVM 'nizi nasıl kullanacağınızı öğreneceksiniz. DSVM 'de yapabileceğiniz bazı şeyler aşağıda verilmiştir:

- Python 2, Python 3 ve Microsoft R kullanarak verileri tarayıcıda denemek için Jupyter Notebook kullanın. (Microsoft R, bir R 'nin performans için tasarlanan kurumsal kullanıma yönelik bir sürümüdür.)
- Microsoft Machine Learning Server ve Python kullanarak DSVM 'de yerel olarak veri araştırma ve modeller geliştirme.
- Azure portal veya PowerShell 'i kullanarak Azure kaynaklarınızı yönetin.
- DSVM 'niz üzerinde bir Azure dosya paylaşımından bağlanabilir sürücü olarak depolama alanınızı genişletin ve büyük ölçekli veri kümelerini/kodları tüm ekibiniz üzerinde paylaşabilirsiniz.
- GitHub kullanarak kodu ekibinizle paylaşabilirsiniz. Önceden yüklenmiş Git istemcilerini kullanarak deponuza erişin: git Bash ve git GUI.
- Azure Blob depolama, Azure Cosmos DB, Azure Synapse (eski adıyla SQL DW) ve Azure SQL veritabanı gibi Azure verilerine ve analiz hizmetlerine erişin.
- DSVM 'de önceden yüklenmiş Power BI Desktop örneğini kullanarak raporlar ve bir pano oluşturun ve bunları buluta dağıtın.

- Sanal makinenize ek araçlar yükler.   

> [!NOTE]
> Bu makalede listelenen veri depolama ve analiz hizmetlerinin birçoğu için ek kullanım ücretleri uygulanır. Ayrıntılar için bkz. [Azure fiyatlandırma](https://azure.microsoft.com/pricing/) sayfası.
> 
> 

## <a name="prerequisites"></a>Önkoşullar

* Bir Azure aboneliğine ihtiyacınız vardır. [Ücretsiz deneme için kaydolabilirsiniz](https://azure.microsoft.com/free/).
* Azure portal bir Veri Bilimi Sanal Makinesi sağlamaya yönelik yönergeler, [sanal makine oluşturma](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016)bölümünde bulunabilir.


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


## <a name="use-jupyter-notebooks"></a>Jupyter Notebook'u kullanma
Jupyter Notebook, veri araştırması ve modelleme için tarayıcı tabanlı bir IDE sağlar. Bir Jupyter Notebook Python 2, Python 3 veya R (açık kaynak ve Microsoft R Server) kullanabilirsiniz.

Jupyter Notebook başlatmak için **Başlat** menüsünde veya masaüstünde **Jupyter Notebook** simgesini seçin. DSVM komut isteminde, ```jupyter notebook``` mevcut not defterlerine sahip olduğunuz veya yeni not defterleri oluşturmak istediğiniz dizinden komutunu da çalıştırabilirsiniz.  

Jupyıter 'ı başlattıktan sonra, `/notebooks` DSVM 'ye önceden paketlenmiş örnek Not defterleri için dizine gidin. Artık şunları yapabilirsiniz:

* Kodu görmek için Not defterini seçin.
* Her hücreyi SHIFT + enter ' i seçerek çalıştırın.
* **Hücre** Çalıştır ' i seçerek tüm not defterini çalıştırın  >  .
* Jupyter simgesini (sol üst köşedeki) seçerek yeni bir not defteri oluşturun, sağdaki **Yeni** düğmesini seçin ve sonra da Not defteri dilini (kernels olarak da bilinir) seçin.   

> [!NOTE]
> Şu anda, bir Jupyter 'da Python 2,7, Python 3,6, R, Julia ve pyspark çekirdekler destekleniyor. R Kernel hem açık kaynaklı R hem de Microsoft R 'de programlamayı destekler.   
> 
> 

Not defteri 'nde olduğunuzda verilerinizi inceleyebilir, modeli oluşturabilir ve kitaplıkları tercih ettiğiniz kitaplıkları kullanarak test edebilirsiniz.

## <a name="explore-data-and-develop-models-with-microsoft-machine-learning-server"></a>Microsoft Machine Learning Server ile verileri araştırma ve modeller geliştirme
Veri analizinizi DSVM üzerinde doğrudan yapmak için R ve Python gibi dilleri kullanabilirsiniz.

R için, başlangıç menüsünde veya masaüstünde bulunan RStudio gibi bir IDE kullanabilirsiniz. İsterseniz Visual Studio için R Araçları de kullanabilirsiniz. Microsoft, ölçeklenebilir analizleri etkinleştirmek ve paralel öbekli çözümlemede izin verilen bellek boyutundan daha büyük verileri analiz etmek üzere açık kaynaklı CRAN R 'nin üzerine ek kitaplıklar sağladı. 

Python için, Visual Studio için Python Araçları (PTV) uzantısının önceden yüklenmiş olduğu Visual Studio Community Edition gibi bir IDE kullanabilirsiniz. Varsayılan olarak, PTV 'lerde yalnızca Python 3,6, kök Conda ortamı yapılandırılır. Anaconda Python 2,7 ' i etkinleştirmek için aşağıdaki adımları uygulayın:

1. **Araçlar**  >  **Python araçları**  >  **Python ortamları**' na giderek ve ardından Visual Studio Community Edition 'da **+ özel** ' i seçerek her bir sürüm için özel ortamlar oluşturun.
1. Anaconda Python 2,7 için bir açıklama verin ve ortam ön eki yolunu **c:\anaconda\envs\python2** olarak ayarlayın.
1. Ortamı kaydetmek için **Otomatik Algıla**  >  **Uygula** ' yı seçin.

Python ortamları oluşturma hakkında daha fazla bilgi için bkz. [PTV belgeleri](/visualstudio/python/) .

Şimdi yeni bir Python projesi oluşturmak için hazırsınız. **Dosya**  >  **Yeni**  >  **Proje**  >  **Python** ' a gidin ve oluşturmakta olduğunuz Python uygulamasının türünü seçin. Python **ortamları** ' na sağ tıklayıp **Python ortamlarını Ekle/Kaldır**' ı seçerek, geçerli projenin Python ortamını istenen sürüme (Python 2,7 veya 3,6) ayarlayabilirsiniz. [Ürün belgelerinde](/visualstudio/python/)PTV ile çalışma hakkında daha fazla bilgi edinebilirsiniz.



## <a name="manage-azure-resources"></a>Azure kaynaklarını yönetme
DSVM yalnızca, sanal makinede analiz çözümünüzü yerel olarak oluşturmanıza izin vermez. Ayrıca, Azure bulut platformunda hizmetlere erişmenize de olanak tanır. Azure, DSVM 'nizden yönetebileceğiniz ve erişebileceğiniz çeşitli işlem, depolama, veri analizi ve diğer hizmetler sağlar.

Azure aboneliğinizi ve bulut kaynaklarınızı yönetmek için iki seçeneğiniz vardır:
+ Tarayıcınızı kullanın ve [Azure Portal](https://portal.azure.com)gidin.

+ PowerShell betikleri kullanın. Azure PowerShell masaüstündeki veya **Başlat** menüsünden bir kısayoldan çalıştırın. Tüm ayrıntılar için [Microsoft Azure PowerShell belgelerine](../../azure-resource-manager/management/manage-resources-powershell.md) bakın. 

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

```bash
git clone https://github.com/Azure/DataScienceVM.git
```

Visual Studio 'da aynı kopyalama işlemini gerçekleştirebilirsiniz. Aşağıdaki ekran görüntüsünde, Visual Studio 'da git ve GitHub araçlarına nasıl erişebileceğiniz gösterilmektedir:

![GitHub bağlantısı görüntülenirken Visual Studio ekran görüntüsü](./media/vm-do-ten-things/VSGit.png)

Github.com ' de bulunan kaynaklardan GitHub deponuzla çalışmak için git kullanma hakkında daha fazla bilgi edinebilirsiniz. Tek [sayfa](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) kullanışlı bir başvurudur.

## <a name="access-azure-data-and-analytics-services"></a>Azure veri ve analiz hizmetlerine erişin
### <a name="azure-blob-storage"></a>Azure Blob depolama
Azure Blob depolama, büyük ve küçük veriler için güvenilir, ekonomik bir bulut depolama hizmetidir. Bu bölümde, blob depolamaya verileri nasıl taşıyacağınız ve bir Azure Blob 'da depolanan verilere erişim açıklanmaktadır.

#### <a name="prerequisites"></a>Önkoşullar

* [Azure Portal](https://portal.azure.com)Azure Blob Storage hesabınızı oluşturun.

   ![Azure portal depolama hesabı oluşturma işleminin ekran görüntüsü](./media/vm-do-ten-things/create-azure-blob.png)

* Komut satırı AzCopy aracının önceden yüklü olduğunu doğrulayın: ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe``` . azcopy.exe içeren dizin, PATH ortam değişkeninizden zaten bulunur, bu nedenle bu aracı çalıştırırken tam komut yolunu yazmaktan kaçınabilirsiniz. AzCopy aracı hakkında daha fazla bilgi için bkz. [AzCopy belgeleri](../../storage/common/storage-use-azcopy-v10.md).
* Azure Depolama Gezgini aracını başlatın. [Depolama Gezgini Web sayfasından](https://storageexplorer.com/)indirebilirsiniz. 

   ![Bir depolama hesabına erişen Azure Depolama Gezgini ekran görüntüsü](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azcopy"></a>Bir VM 'den Azure Blob 'a veri taşıma: AzCopy

Yerel dosyalarınız ve BLOB depolama alanı arasında veri taşımak için, komut satırında veya PowerShell 'de AzCopy komutunu kullanabilirsiniz:

```powershell
AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt
```

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

* Bir kapsayıcıya veri yüklemek için hedef kapsayıcıyı seçin ve **karşıya yükle** düğmesini seçin. ![ Azure Depolama Gezgini 'de karşıya yükle düğmesinin ekran görüntüsü](./media/vm-do-ten-things/storage-accounts.png)
* **Dosyalar** kutusunun sağ tarafındaki üç nokta (**...**) simgesini seçin, dosya sisteminden karşıya yüklenecek bir veya birden çok dosya seçin ve karşıya yükleme Işlemini başlatmak için **karşıya yükle** ' yi seçin. ![ Dosyaları karşıya yükle iletişim kutusunun ekran görüntüsü](./media/vm-do-ten-things/upload-files-to-blob.png)

#### <a name="read-data-from-an-azure-blob-python-odbc"></a>Azure blobundan verileri okuma: Python ODBC

Blob hizmeti kitaplığını, bir Jupyter Notebook veya bir Python programındaki bir bloba doğrudan veri okumak için kullanabilirsiniz.

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

![İlk 10 veri satırı ekran görüntüsü](./media/vm-do-ten-things/IPNB_data_readin.png)


### <a name="azure-synapse-analytics-and-databases"></a>Azure SYNAPSE Analytics ve veritabanları
Azure SYNAPSE Analytics, kurumsal sınıf SQL Server deneyimine sahip bir hizmet olarak elastik bir veri ambarıdır.

[Bu makaledeki](../../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md)yönergeleri Izleyerek Azure SYNAPSE Analytics sağlayabilirsiniz. Azure SYNAPSE Analytics 'i sağlamadıktan sonra, Azure SYNAPSE Analytics içindeki verileri kullanarak karşıya veri yükleme, araştırma ve Modelleme işlemleri yapmak için [Bu](../team-data-science-process/sqldw-walkthrough.md) Kılavuzu kullanabilirsiniz.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB, buluttaki bir NoSQL veritabanıdır. Bunu, JSON gibi belgelerle çalışmak ve belgeleri depolamak ve sorgulamak için kullanabilirsiniz.

DSVM 'den Azure Cosmos DB erişmek için aşağıdaki önkoşul adımlarını kullanın:

1. Azure Cosmos DB Python SDK, DSVM üzerinde zaten yüklü. Güncelleştirmek için ```pip install pydocumentdb --upgrade``` bir komut isteminden çalıştırın.
2. [Azure Portal](https://portal.azure.com)bir Azure Cosmos DB hesabı ve veritabanı oluşturun.
3. Azure Cosmos DB veri geçiş aracı 'nı [Microsoft Indirme merkezi](https://www.microsoft.com/download/details.aspx?id=53595) ' nden indirin ve seçtiğiniz bir dizine ayıklayın.
4. Bir [genel Blobun](https://data.humdata.org/dataset/a60ac839-920d-435a-bf7d-25855602699d/resource/7234d067-2d74-449a-9c61-22ae6d98d928/download/volcano.json) IÇINDE depolanan JSON verilerini (Volcano verileri), geçiş aracına aşağıdaki komut parametreleriyle Azure Cosmos DB içine aktarın. (Azure Cosmos DB veri geçiş aracı 'nı yüklediğiniz dizinden dtui.exe kullanın.) Şu parametrelerle kaynak ve hedef konumu girin:
   
    `/s:JsonFile /s.Files:https://data.humdata.org/dataset/a60ac839-920d-435a-bf7d-25855602699d/resource/7234d067-2d74-449a-9c61-22ae6d98d928/download/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

Verileri içeri aktardıktan sonra Jupyter ' a gidip *Documentdbsample* adlı Not defterini açabilirsiniz. Azure Cosmos DB erişmek ve bazı temel sorgulama yapmak için Python kodu içerir. Hizmetin [Belgeler sayfasını](../../cosmos-db/index.yml)ziyaret ederek Azure Cosmos DB hakkında daha fazla bilgi edinebilirsiniz.

## <a name="use-power-bi-reports-and-dashboards"></a>Power BI raporlarını ve panoları kullanma 
Verilerle ilgili görsel Öngörüler elde etmek için Power BI Desktop önceki Azure Cosmos DB örneğinde bulunan Volcano JSON dosyasını görselleştirebilirsiniz. Ayrıntılı adımlar [Power BI makalesinde](../../cosmos-db/powerbi-visualize.md)bulunabilir. Üst düzey adımlar şunlardır:

1. Power BI Desktop'ı açın ve **Veri Al**'ı seçin. URL 'YI şöyle belirtin: `https://cahandson.blob.core.windows.net/samples/volcano.json` .
2. Liste olarak içeri aktarılmış JSON kayıtlarını görmeniz gerekir. Power BI onunla birlikte çalışabilmek için listeyi bir tabloya dönüştürün.
4. Genişlet (ok) simgesini seçerek sütunları genişletin.
5. Konumun bir **kayıt** alanı olduğuna dikkat edin. Kaydı genişletin ve yalnızca koordinatları seçin. **Koordinat** bir liste sütunudur.
6. Liste koordinat sütununu, virgülle ayrılmış bir **LatLong** sütuna dönüştürmek için yeni bir sütun ekleyin. Formülü kullanarak koordinat listesi alanındaki iki öğeyi birleştirir ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})``` .
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
> VM 'deki işletim sistemi için yalnızca kapalı düğmesini kullanırsanız işlem ücretlerine tabi olursunuz. Bunun yerine, Azure portal veya Cloud Shell kullanarak DSVM 'nizi serbest bırakın.
> 
> 

Büyük ölçekli analizler oluşturmanız ve daha fazla CPU, bellek veya disk kapasitesi yapmanız gerekebilir. Bu durumda, CPU çekirdekleri, ayrıntılı öğrenme için GPU tabanlı örnekler, bellek kapasitesi ve işlem ve budgetary ihtiyaçlarınızı karşılayan disk türleri (katı hal sürücüleri dahil) açısından bir sanal makine boyutu seçimi bulabilirsiniz. VM 'lerin tam listesi, saatlik işlem fiyatlandırmasıyla birlikte [Azure sanal makineler fiyatlandırma](https://azure.microsoft.com/pricing/details/virtual-machines/) sayfasında kullanılabilir.


## <a name="add-more-tools"></a>Daha fazla araç ekleyin
DSVM 'de önceden yerleşik olarak bulunan araçlar birçok ortak veri analizi ihtiyaçlarını ele alabilir. Ortamlarınızı tek tek yükleyip yapılandırmanız gerekmiyorsa bu size zaman kazandırır. Yalnızca kullandığınız kaynaklar için ödeme yaptığınız için de paradan tasarruf etmenizi sağlar.

Analiz ortamınızı geliştirmek için bu makalede profili oluşturulan diğer Azure veri ve analiz hizmetlerini kullanabilirsiniz. Bazı durumlarda, bazı özel iş ortağı araçları da dahil olmak üzere ek araçlara ihtiyacınız vardır. İhtiyaç duyduğunuz yeni araçları yüklemek için sanal makinede tam yönetici erişiminiz vardır. Python ve R 'ye önceden yüklenmemiş ek paketler de yükleyebilirsiniz. Python için ya da kullanabilirsiniz ```conda``` ```pip``` . R için, ```install.packages()``` r konsolunda kullanabilir veya IDE 'yi kullanabilir ve **paketler** için paketleri  >  **yükleyebilirsiniz** seçeneğini belirleyebilirsiniz.

## <a name="deep-learning"></a>Derin öğrenme

Framework tabanlı örneklere ek olarak, DSVM 'de doğrulanan kapsamlı bir izlenecek yol kümesi alabilirsiniz. Bu izlenecek yollar, görüntü ve metin/dil anlama gibi etki alanlarında derin öğrenme uygulamalarınızı geliştirmeye başlamanıza yardımcı olur.   


- [Farklı çerçeveler arasında sinir ağlarını çalıştırma](https://github.com/ilkarman/DeepLearningFrameworks): Bu izlenecek yol, kodun bir çerçeveden diğerine nasıl ekleneceğini gösterir. Ayrıca, modeller ve çalışma zamanı performansının çerçeveler arasında nasıl karşılaştırılacağını gösterir. 

- Görüntü [içindeki ürünleri algılamaya yönelik uçtan uca bir çözüm oluşturmak için nasıl yapılır Kılavuzu](https://github.com/Azure/cortana-intelligence-product-detection-from-images): görüntü algılama, resimleri içindeki nesneleri bulup sınıflandırmanın bir tekniğidir. Bu teknoloji, çok sayıda gerçek hayatta iş etki alanında büyük bir rekele sunmayı mümkün hale getirir. Örneğin, satıcılar bu tekniği kullanarak bir müşterinin rafı hangi ürüne kullandığını tespit edebilir. Sırasıyla bu bilgiler, ürün envanterini yönetme mağazalarına yardımcı olur. 

- [Ses Için derin öğrenme](/archive/blogs/machinelearning/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure): Bu öğreticide, [kentsel sesler veri kümesinde](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html)ses olayı algılaması için derin öğrenme modelinin nasıl eğeceği gösterilmektedir. Ayrıca, ses verileriyle çalışma hakkında genel bakış sağlar.

- [Metin belgelerinin sınıflandırılması](https://github.com/anargyri/lstm_han): Bu kılavuzda iki sinir ağ mimarisi oluşturma ve eğitme gösterilmektedir: hiyerarşik dikkat ağı ve uzun kısa dönem belleğı (lstm) ağı. Bu sinir Networks, metin belgelerinin sınıflandırılmasında derin öğrenme için keras API 'sini kullanır. 

## <a name="summary"></a>Özet
Bu makalede, Microsoft Veri Bilimi Sanal Makinesi 'de yapabileceğiniz bazı şeyler açıklanmaktadır. DSVM 'yi etkin bir analiz ortamı yapmak için yapabileceğiniz çok daha fazla şey vardır.
