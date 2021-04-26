---
title: Gelişmiş analiz ile Azure blob verilerini işleme-Team Data Science Işlemi
description: Gelişmiş analiz kullanarak Azure Blob depolama alanında depolanan verilerden veri bulun ve Özellikler oluşturun.
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
ms.openlocfilehash: 5a088d5918a957036b905db9136f9b16e5f0527e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93307155"
---
# <a name="process-azure-blob-data-with-advanced-analytics"></a><a name="heading"></a>Azure blob verilerini gelişmiş analiz ile işleme
Bu belge, Azure Blob depolamada depolanan verilerden veri araştırmayı ve özellikleri oluşturmayı içerir. 

## <a name="load-the-data-into-a-pandas-data-frame"></a>Verileri bir Pandas veri çerçevesine yükleme
Bir veri kümesini araştırmak ve işlemek için, blob kaynağından bir Pandas veri çerçevesine yüklenebilecek bir yerel dosyaya indirilmelidir. Bu yordamda izlenecek adımlar aşağıda verilmiştir:

1. Blob hizmetini kullanarak aşağıdaki örnek Python koduna sahip Azure blobundan verileri indirin. Aşağıdaki koddaki değişkeni belirli değerlerinizle değiştirin: 
   
    ```python
    from azure.storage.blob import BlobService
    import tables
   
    STORAGEACCOUNTNAME= <storage_account_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    LOCALFILENAME= <local_file_name>        
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>
   
    #download from blob
    t1=time.time()
    blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
    t2=time.time()
    print(("It takes %s seconds to download "+blobname) % (t2 - t1))
    ```
2. İndirilen dosyadaki verileri bir Pandas veri çerçevesine okuyun.
   
    ```python
    #LOCALFILE is the file path    
    dataframe_blobdata = pd.read_csv(LOCALFILE)
    ```

Artık verileri keşfetmeye ve bu veri kümesinde Özellik oluşturmaya hazırsınız.

## <a name="data-exploration"></a><a name="blob-dataexploration"></a>Veri araştırması
Aşağıda, Pandas kullanarak verileri keşfetmeye yönelik yöntemlere birkaç örnek verilmiştir:

1. Satır ve sütun sayısını İnceleme 
   
    ```python
    print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
    ```
2. Veri kümesindeki ilk veya son birkaç satırı aşağıda gösterildiği gibi inceleyin:
   
    ```python
    dataframe_blobdata.head(10)
   
    dataframe_blobdata.tail(10)
    ```
3. Aşağıdaki örnek kodu kullanarak her bir sütunun içeri aktarıldığı veri türünü denetleyin
   
    ```python
    for col in dataframe_blobdata.columns:
        print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
    ```
4. Veri kümesindeki sütunların temel istatistiklerini aşağıda gösterildiği gibi denetleyin
   
    ```python
    dataframe_blobdata.describe()
    ```
5. Her bir sütun değeri için aşağıdaki gibi giriş sayısına bakın
   
    ```python
    dataframe_blobdata['<column_name>'].value_counts()
    ```
6. Aşağıdaki örnek kodu kullanarak eksik değerleri ve her bir sütundaki gerçek girdi sayısını say
   
    ```python
    miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
    print miss_num
    ```
7. Verilerdeki belirli bir sütun için eksik değerler varsa, bunları aşağıdaki gibi bırakabilirsiniz:
   
    ```python
    dataframe_blobdata_noNA = dataframe_blobdata.dropna()
    dataframe_blobdata_noNA.shape
    ```
   
   Eksik değerleri değiştirmek için başka bir yol da Mode işlevidir:
   
    ```python
    dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})  
    ```      
8. Bir değişkenin dağılımını çizmek için değişken sayıda depo gözü kullanarak bir histogram çizimi oluşturma    
   
    ```python
    dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
    np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    ```
9. Bir dağınık terçiz kullanarak veya yerleşik bağıntı işlevini kullanarak değişkenler arasında bağıntılar 'e bakın
   
    ```python
    #relationship between column_a and column_b using scatter plot
    plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
    #correlation between column_a and column_b
    dataframe_blobdata[['<column_a>', '<column_b>']].corr()
    ```

## <a name="feature-generation"></a><a name="blob-featuregen"></a>Özellik oluşturma
Python kullanarak şu şekilde özellik üretebiliriz:

### <a name="indicator-value-based-feature-generation"></a><a name="blob-countfeature"></a>Gösterge değer tabanlı özellik oluşturma
Kategorik özellikler aşağıdaki gibi oluşturulabilir:

1. Kategorik sütununun dağıtımını inceleyin:
   
    ```python
    dataframe_blobdata['<categorical_column>'].value_counts()
    ```
2. Her sütun değeri için gösterge değerlerini oluştur
   
    ```python
    #generate the indicator column
    dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')
    ```
3. Gösterge sütununa özgün veri çerçevesini ekleyin 
   
    ```python
    #Join the dummy variables back to the original data frame
    dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)
    ```
4. Özgün değişkeni kaldırın:
   
    ```python
    #Remove the original column rate_code in df1_with_dummy
    dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)
    ```

### <a name="binning-feature-generation"></a><a name="blob-binningfeature"></a>Özellik oluşturmayı atma
Bağlanabilir özellikler oluşturmak için aşağıdaki gibi ilerliyoruz:

1. Bin sayısal bir sütuna bir sütun dizisi ekleyin
   
    ```python
    bins = [0, 1, 2, 4, 10, 40]
    dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
    ```
2. Binmek 'yi Boole değişkenleri dizisine Dönüştür
   
    ```python
    dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
    ```
3. Son olarak, kukla değişkenleri özgün veri çerçevesine geri katın
   
    ```python
    dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)  
    ```  

## <a name="writing-data-back-to-azure-blob-and-consuming-in-azure-machine-learning"></a><a name="sql-featuregen"></a>Verileri Azure Blob 'a geri yazma ve Azure Machine Learning kullanma
Verileri araştırdıktan ve gerekli özellikleri oluşturduktan sonra, verileri (örneklendirilmiş veya uygun) bir Azure blobuna yükleyebilir ve aşağıdaki adımları kullanarak Azure Machine Learning kullanabilirsiniz: ek özellikler, Azure Machine Learning Studio (klasik) içinde da oluşturulabilir. 

1. Veri çerçevesini yerel dosyaya yaz
   
    ```python
    dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
    ```
2. Verileri Azure Blob 'a aşağıdaki gibi yükleyin:
   
    ```python
    from azure.storage.blob import BlobService
    import tables
   
    STORAGEACCOUNTNAME= <storage_account_name>
    LOCALFILENAME= <local_file_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>
   
    output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
    localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
   
    try:
   
    #perform upload
    output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
   
    except:            
        print ("Something went wrong with uploading blob:"+BLOBNAME)
    ```
3. Artık veriler, aşağıdaki ekranda gösterildiği gibi Azure Machine Learning [veri alma][import-data] modülünü kullanarak bloba okunabilir:

![okuyucu blobu][1]

[1]: ./media/data-blob/reader_blob.png


<!-- Module References -->
[import-data]: /azure/machine-learning/studio-module-reference/import-data