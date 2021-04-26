---
title: Öğretici-Data Factory aracılığıyla Python betikleri çalıştırma
description: Azure Batch kullanarak Azure Data Factory bir işlem hattının parçası olarak Python betikleri çalıştırmayı öğrenin.
author: pkshultz
ms.devlang: python
ms.topic: tutorial
ms.date: 03/12/2021
ms.author: peshultz
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 241a47ccf9021c6065fea907b4d9914744a64972
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103461700"
---
# <a name="tutorial-run-python-scripts-through-azure-data-factory-using-azure-batch"></a>Öğretici: Azure Batch kullanarak Azure Data Factory Python betikleri çalıştırma

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Batch ve Depolama hesapları ile kimlik doğrulaması
> * Python 'da betik geliştirme ve çalıştırma
> * Bir uygulamayı çalıştırmak için işlem düğümleri havuzu oluşturma
> * Python iş yüklerinizi zamanlama
> * Analiz işlem hattınızı izleme
> * Günlük verilerinize erişin

Aşağıdaki örnek, bir BLOB depolama kapsayıcısından CSV girişi alan bir Python betiği çalıştırır, bir veri işleme işlemi gerçekleştirir ve çıktıyı ayrı bir BLOB depolama kapsayıcısına yazar.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

* Yerel test için yüklü bir [Python](https://www.python.org/downloads/) dağıtımı.
* [Azure-Storage-blob](https://pypi.org/project/azure-storage-blob/) `pip` paketi.
* [iris.csv veri kümesi](https://github.com/Azure-Samples/batch-adf-pipeline-tutorial/blob/master/iris.csv)
* Bir Azure Batch hesabı ve bağlı bir Azure Depolama hesabı. Batch hesaplarını oluşturma ve depolama hesaplarına bağlama hakkında daha fazla bilgi için bkz. [Batch hesabı oluşturma](quick-create-portal.md#create-a-batch-account) .
* Azure Data Factory hesabı. Azure portal aracılığıyla veri fabrikası oluşturma hakkında daha fazla bilgi için bkz. [Veri Fabrikası oluşturma](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory) .
* [Batch Explorer](https://azure.github.io/BatchExplorer/).
* [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/).

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]

## <a name="create-a-batch-pool-using-batch-explorer"></a>Batch Explorer kullanarak bir Batch havuzu oluşturma

Bu bölümde, Azure Data Factory Işlem hattının kullanacağı toplu Iş havuzunu oluşturmak için Batch Explorer kullanacaksınız. 

1. Batch Explorer için Azure kimlik bilgilerinizi kullanarak oturum açın.
1. Batch hesabınızı seçin
1. Sol taraftaki çubukta **havuzlar** ' ı seçerek bir havuz oluşturun, sonra arama formunun üzerindeki **Ekle** düğmesine basın. 
    1. Bir KIMLIK ve görünen ad seçin. `custom-activity-pool`Bu örnek için kullanacağız.
    1. Ölçek türünü **sabit boyut** olarak ayarlayın ve adanmış düğüm sayısını 2 olarak ayarlayın.
    1. **Veri bilimi** altında, işletim sistemi olarak **dsvm Windows** ' u seçin.
    1. `Standard_f2s_v2`Sanal makine boyutu olarak seçin.
    1. Başlangıç görevini etkinleştirin ve komutunu ekleyin `cmd /c "pip install azure-storage-blob pandas"` . Kullanıcı kimliği varsayılan **Havuz kullanıcısı** olarak kalabilir.
    1. **Tamam**’ı seçin.

## <a name="create-blob-containers"></a>Blob kapsayıcıları oluşturma

Burada, OCR toplu işi için giriş ve çıkış dosyalarınızı depolayacak blob kapsayıcıları oluşturacaksınız.

1. Depolama Gezgini için Azure kimlik bilgilerinizi kullanarak oturum açın.
1. Batch hesabınıza bağlı depolama hesabını kullanarak, [BLOB kapsayıcısı oluşturma](../vs-azure-tools-storage-explorer-blobs.md#create-a-blob-container)konumundaki adımları izleyerek iki blob kapsayıcı (bir diğeri çıkış dosyaları için bir tane) oluşturun.
    * Bu örnekte, giriş kapsayımuzu `input` ve çıkış kapsayımuzu arayacağız `output` .
1. [`iris.csv`](https://github.com/Azure-Samples/batch-adf-pipeline-tutorial/blob/master/iris.csv) `input` [BLOB kapsayıcısında blob 'ları yönetme](../vs-azure-tools-storage-explorer-blobs.md#managing-blobs-in-a-blob-container) adımlarını izleyerek Depolama Gezgini kullanarak giriş kapsayıcınıza yükleyin

## <a name="develop-a-script-in-python"></a>Python 'da betik geliştirme

Aşağıdaki Python betiği, `iris.csv` `input` veri kümesini kapsayıcılarınızdan yükler, bir veri işleme işlemi gerçekleştirir ve sonuçları kapsayıcıya geri kaydeder `output` .

``` python
# Load libraries
from azure.storage.blob import BlobClient
import pandas as pd

# Define parameters
connectionString = "<storage-account-connection-string>"
containerName = "output"
outputBlobName  = "iris_setosa.csv"

# Establish connection with the blob storage account
blob = BlobClient.from_connection_string(conn_str=connectionString, container_name=containerName, blob_name=outputBlobName)

# Load iris dataset from the task node
df = pd.read_csv("iris.csv")

# Take a subset of the records
df = df[df['Species'] == "setosa"]

# Save the subset of the iris dataframe locally in task node
df.to_csv(outputBlobName, index = False)

with open(outputBlobName, "rb") as data:
    blob.upload_blob(data)
```

Betiği kaydedin `main.py` ve **Azure depolama** `input` kapsayıcısına yükleyin. Blob kapsayıcınıza yüklemeden önce işlevselliğini yerel olarak test edin ve doğrulayın:

``` bash
python main.py
```

## <a name="set-up-an-azure-data-factory-pipeline"></a>Azure Data Factory işlem hattı ayarlama

Bu bölümde, Python komut dosyanızı kullanarak bir işlem hattı oluşturup doğrulayacaksınız.

1. [Bu makalenin](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory)"Veri Fabrikası oluşturma" bölümünde bir veri fabrikası oluşturmak için adımları izleyin.
1. **Fabrika kaynakları** kutusunda + (artı) düğmesini seçin ve ardından işlem **hattı** ' nı seçin.
1. **Genel** sekmesinde, işlem hattının adını "Python Çalıştır" olarak ayarlayın

    ![Genel sekmesinde, işlem hattının adını "Python Çalıştır" olarak ayarlayın](./media/run-python-batch-azure-data-factory/create-pipeline.png)

1. **Etkinlikler** kutusunda **Batch hizmeti**' ni genişletin. **Etkinlikler** araç kutusundan özel etkinliği işlem hattı tasarımcı yüzeyine sürükleyin. Özel etkinlik için aşağıdaki sekmeleri doldurun:
    1. **Genel** sekmesinde, ad Için **Testpipeline** ' ı ![ Genel sekmesinde belirtin, ad için testpipeline belirtin](./media/run-python-batch-azure-data-factory/create-custom-task.png)
    1. **Azure Batch** sekmesinde, önceki adımlarda oluşturulan **Batch hesabını** ekleyin ve başarılı olduğundan emin olmak için **bağlantıyı test** edin.
    ![Azure Batch sekmesinde, önceki adımlarda oluşturulan Batch hesabını ekleyin, ardından bağlantıyı test edin](./media/run-python-batch-azure-data-factory/integrate-pipeline-with-azure-batch.png)
    1. **Ayarlar** sekmesinde:
        1. **Komutunu** olarak ayarlayın `python main.py` .
        1. **Kaynak bağlı hizmeti** için, önceki adımlarda oluşturulan depolama hesabını ekleyin. Başarılı olduğundan emin olmak için bağlantıyı test edin.
        1. **Klasör yolu**' nda, Python betiğini ve ilişkili girişleri Içeren **Azure Blob depolama** kapsayıcısının adını seçin. Bu işlem, Python betiği yürütülmeden önce seçili dosyaları kapsayıcıdan havuzdan düğüm örneklerine indirir.

        ![Klasör yolu ' nda, Azure Blob depolama kapsayıcısının adını seçin](./media/run-python-batch-azure-data-factory/create-custom-task-py-script-command.png)

1. İşlem hattı ayarlarını doğrulamak için işlem hattı araç çubuğunda **Doğrula**'ya tıklayın. İşlem hattının başarıyla doğrulandığını onaylayın. Doğrulama çıktısını kapatmak için &gt;&gt; (sağ ok) düğmesini seçin.
1. İşlem hattını test etmek ve düzgün çalıştığından emin olmak için **Hata Ayıkla** ' ya tıklayın.
1. İşlem hattını yayımlamak için **Yayımla** ' ya tıklayın.
1. Python betiğini toplu işlemin bir parçası olarak çalıştırmak için **Tetikle** ' e tıklayın.

    ![Python betiğini toplu işlemin bir parçası olarak çalıştırmak için Tetikle ' e tıklayın](./media/run-python-batch-azure-data-factory/create-custom-task-py-success-run.png)

### <a name="monitor-the-log-files"></a>Günlük dosyalarını izleme

Uyarıları veya hataları komut dosyanızın yürütülmesi tarafından üretildiğinde, `stdout.txt` `stderr.txt` günlüğe kaydedilen çıktı hakkında daha fazla bilgi alabilirsiniz.

1. Batch Explorer sol taraftaki **işleri** seçin.
1. Veri fabrikanızın oluşturduğu işi seçin. Havuzunuzu adlandırdığınız varsayılarak `custom-activity-pool` , öğesini seçin `adfv2-custom-activity-pool` .
1. Hata çıkış kodu olan göreve tıklayın.
1. `stdout.txt` `stderr.txt` Sorununuzu inceleyin ve tanılamanıza ve tanılayın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

İşlerin ve görevlerin kendileri için sizden ücret alınmasa da işlem düğümleri için ücret alınır. Bu nedenle, havuzları yalnızca gerektiğinde ayırmanız önerilir. Havuzu sildiğinizde düğümler üzerindeki tüm görev çıkışları silinir. Ancak, giriş ve çıkış dosyaları depolama hesabında kalır. Artık gerekli değilse, Batch hesabını ve depolama hesabını da silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Batch ve Depolama hesapları ile kimlik doğrulaması
> * Python 'da betik geliştirme ve çalıştırma
> * Bir uygulamayı çalıştırmak için işlem düğümleri havuzu oluşturma
> * Python iş yüklerinizi zamanlama
> * Analiz işlem hattınızı izleme
> * Günlük verilerinize erişin

Azure Data Factory hakkında daha fazla bilgi edinmek için bkz.:

> [!div class="nextstepaction"]
> [Azure Data Factory genel bakış](../data-factory/introduction.md)
