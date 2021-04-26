---
title: Pandas 'lar ile Azure Blob depolama 'daki verileri araştırma-ekip veri bilimi Işlemi
description: Pandas Python paketini kullanarak Azure Blob kapsayıcısında depolanan verileri araştırma.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 855998b887f1d446ee8d196ff4628e066cb5d675
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98805684"
---
# <a name="explore-data-in-azure-blob-storage-with-pandas"></a>Pandas ile Azure Blob depolamada verileri araştırma

Bu makalede, Azure Blob kapsayıcısında [Pandas](https://pandas.pydata.org/) Python paketi kullanılarak depolanan verilerin nasıl araştırabileceği ele alınmaktadır.

Bu görev, [ekip veri bilimi işlemindeki](overview.md)bir adımdır.

## <a name="prerequisites"></a>Önkoşullar
Bu makalede sahip olduğunuz varsayılır:

* Bir Azure depolama hesabı oluşturuldu. Yönergelere ihtiyacınız varsa bkz. [Azure depolama hesabı oluşturma](../../storage/common/storage-account-create.md)
* Verileriniz bir Azure Blob depolama hesabında depolanıyor. Yönergelere ihtiyacınız varsa bkz. [Azure depolama 'ya ve Azure Storage 'a veri taşıma](../../storage/common/storage-choose-data-transfer-solution.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Verileri bir Pandas DataFrame 'e yükleme
Bir veri kümesini araştırmak ve işlemek için, önce blob kaynağından bir yerel dosyaya indirilmeli ve bu da bir Pandas DataFrame 'e yüklenebilirler. Bu yordamda izlenecek adımlar aşağıda verilmiştir:

1. Blob hizmetini kullanarak aşağıdaki Python kod örneğiyle Azure blobundan verileri indirin. Aşağıdaki koddaki değişkeni belirli değerlerinizle değiştirin:

    ```python
    from azure.storage.blob import BlockBlobService
    import pandas as pd
    import tables

    STORAGEACCOUNTNAME= <storage_account_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    LOCALFILENAME= <local_file_name>
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>

    #download from blob
    t1=time.time()
    blob_service=BlockBlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
    t2=time.time()
    print(("It takes %s seconds to download "+BLOBNAME) % (t2 - t1))
    ```

1. İndirilen dosyadaki verileri bir Pandas DataFrame 'e okuyun.

    ```python
    # LOCALFILE is the file path
    dataframe_blobdata = pd.read_csv(LOCALFILENAME)
    ```

Artık verileri keşfetmeye ve bu veri kümesinde Özellik oluşturmaya hazırsınız.

## <a name="examples-of-data-exploration-using-pandas"></a><a name="blob-dataexploration"></a>Pandas kullanarak veri araştırma örnekleri
Aşağıda, Pandas kullanarak verileri keşfetmeye yönelik yöntemlere birkaç örnek verilmiştir:

1. **Satır ve sütun sayısını** İnceleme

    ```python
    print('the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape)
    ```

1. Aşağıdaki veri kümesindeki ilk veya son birkaç **satırı** **inceleyin** :

    ```python
    dataframe_blobdata.head(10)

    dataframe_blobdata.tail(10)
    ```

1. Aşağıdaki örnek kodu kullanarak her bir sütunun içeri aktarıldığı **veri türünü** denetleyin

    ```python
    for col in dataframe_blobdata.columns:
        print(dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype)
    ```

1. Veri kümesindeki sütunların **temel istatistiklerini** aşağıda gösterildiği gibi denetleyin

    ```python
    dataframe_blobdata.describe()
    ```

1. Her bir sütun değeri için aşağıdaki gibi giriş sayısına bakın

    ```python
    dataframe_blobdata['<column_name>'].value_counts()
    ```

1. Aşağıdaki örnek kodu kullanarak **eksik değerleri** ve her bir sütundaki gerçek girdi sayısını say

    ```python
    miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
    print(miss_num)
    ```

1. Verilerdeki belirli bir sütun için **eksik değerler** varsa, bunları aşağıdaki gibi bırakabilirsiniz:

    ```python
    dataframe_blobdata_noNA = dataframe_blobdata.dropna()
    dataframe_blobdata_noNA.shape
    ```

    Eksik değerleri değiştirmek için başka bir yol da Mode işlevidir:

    ```python
    dataframe_blobdata_mode = dataframe_blobdata.fillna(
        {'<column_name>': dataframe_blobdata['<column_name>'].mode()[0]})
    ```

1. Bir değişkenin dağılımını çizmek için değişken sayıda depo gözü kullanarak bir **histogram** çizimi oluşturma

    ```python
    dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')

    np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    ```

1. Bir dağınık terçiz kullanarak veya yerleşik bağıntı işlevini kullanarak değişkenler arasında **bağıntılar** 'e bakın

    ```python
    # relationship between column_a and column_b using scatter plot
    plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])

    # correlation between column_a and column_b
    dataframe_blobdata[['<column_a>', '<column_b>']].corr()
    ```
