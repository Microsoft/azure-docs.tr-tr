---
title: Panda - Team Data Science Process ile Azure blob depolamadaki verileri keşfedin
description: Pandas Python paketini kullanarak Azure blob kapsayıcısında depolanan verileri araştırmak nasıl.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 23fef994d01917f5f120c7fcb80871f6f2c82ab2
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358594"
---
# <a name="explore-data-in-azure-blob-storage-with-pandas"></a>Panda ile Azure blob depolamadaki verileri keşfedin

Bu makalede, Azure blob kapsayıcısı kullanarak içinde depolanan verileri araştırmak nasıl ele alınmaktadır [pandas](https://pandas.pydata.org/) Python paket.

Bu görev bir adımdır [Team Data Science Process](overview.md).

## <a name="prerequisites"></a>Önkoşullar
Bu makalede, olduğunu varsayar:

* Bir Azure depolama hesabı oluşturuldu. Yönergelere ihtiyacınız varsa bkz [bir Azure depolama hesabı oluşturma](../../storage/common/storage-quickstart-create-account.md)
* Verilerinizi bir Azure blob depolama hesabında depolanır. Yönergelere ihtiyacınız varsa bkz [için ve Azure Depolama'dan veri taşıma](../../storage/common/storage-moving-data.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Pandas DataFrame verileri yükleme
Keşfedin veya bir veri kümesini değiştirmek için önce blob kaynağından bir pandas DataFrame yüklenebilir yerel bir dosyaya indirilmelidir. Bu yordam için izlenmesi gereken adımlar şunlardır:

1. Verileri Azure blob'tan blob hizmeti kullanarak aşağıdaki Python kod örneği ile indirme. Aşağıdaki kod içindeki değişkene belirli değerleriniz ile değiştirin:

```python
from azure.storage.blob import BlockBlobService
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
print(("It takes %s seconds to download "+blobname) % (t2 - t1))
```

1. İçine bir pandas DataFrame indirilen dosyasından verileri okur.

```python
# LOCALFILE is the file path
dataframe_blobdata = pd.read_csv(LOCALFILE)
```

Verileri keşfetme ve bu veri kümesi özellikleri oluşturmak hazırsınız.

## <a name="blob-dataexploration"></a>Veri keşfi pandas kullanma örnekleri
Panda kullanarak verileri araştırmak için gösteren bazı örnekleri şunlardır:

1. İnceleme **satır ve sütun sayısı**

```python
print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
```

1. **İnceleme** ilk veya son birkaç **satırları** aşağıdaki kümesindeki:

```python
dataframe_blobdata.head(10)

dataframe_blobdata.tail(10)
```

1. Denetleme **veri türü** her sütun, aşağıdaki örnek kodu kullanarak olarak içeri aktarıldı

```python
for col in dataframe_blobdata.columns:
    print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
```

1. Denetleme **temel istatistikleri** sütunların veri şu şekilde ayarlayın

```python
dataframe_blobdata.describe()
```

1. Girdi sayısı bu değeri için her bir sütun değeri şu şekilde bakın

```python
dataframe_blobdata['<column_name>'].value_counts()
```

1. **Eksik değerleri saymak** girdileri aşağıdaki örnek kodu kullanarak her sütunda gerçek sayısı

```python
miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
print miss_num
```

1. Varsa **eksik değerleri** için belirli bir sütuna veri, bunları aşağıdaki gibi silebilirsiniz:

```python
dataframe_blobdata_noNA = dataframe_blobdata.dropna()
dataframe_blobdata_noNA.shape
```

Eksik değerleri değiştirmek için başka bir yol ile modu işlevdir:

```python
dataframe_blobdata_mode = dataframe_blobdata.fillna(
    {'<column_name>': dataframe_blobdata['<column_name>'].mode()[0]})
```

1. Oluşturma bir **histogram** bir değişkenin dağıtım çizmek için değişken sayıda depo kullanarak Çiz

```python
dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')

np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
```

1. Bakmak **bağıntılar** arasında bir dağılım grafiği veya yerleşik bağıntı işlevi kullanarak değişkenleri

```python
# relationship between column_a and column_b using scatter plot
plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])

# correlation between column_a and column_b
dataframe_blobdata[['<column_a>', '<column_b>']].corr()
```
