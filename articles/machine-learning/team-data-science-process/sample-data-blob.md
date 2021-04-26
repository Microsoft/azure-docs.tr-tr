---
title: Azure Blob depolama-takım veri bilimi Işlemindeki örnek veriler
description: Azure Blob depolamada depolanan verileri programlı bir şekilde indirip Python 'da yazılmış yordamları kullanarak örnekleyerek örnekleme.
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
ms.openlocfilehash: c5827a0e07e537b66684f852d8f3e1500cd9febb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98788850"
---
# <a name="sample-data-in-azure-blob-storage"></a><a name="heading"></a>Azure Blob depolamada örnek veriler

Bu makalede, Azure Blob depolamada depolanan örnekleme verileri, programlı bir şekilde indirilip Python 'da yazılan yordamlar kullanılarak örneklenerek ele alınmaktadır.

**Verileriniz neden örnekleyebilirsiniz?**
Çözümlemeyi planladığınız veri kümesi büyükse, daha küçük ancak temsili ve daha yönetilebilir bir boyutla azaltmak için verileri düşürmek genellikle iyi bir fikirdir. Örnekleme, veri anlama, araştırma ve özellik mühendisliğini kolaylaştırır. Cortana Analytics Işlemindeki rolü, veri işleme işlevlerinin ve makine öğrenimi modellerinin hızlı prototipini etkinleştirmektir.

Bu örnekleme görevi, [ekip veri bilimi işlemindeki (TDSP)](./index.yml)bir adımdır.

## <a name="download-and-down-sample-data"></a>Örnek verileri indirme ve azaltma
1. Aşağıdaki örnek Python kodundan blob hizmetini kullanarak Azure Blob depolama 'dan verileri indirin: 

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

2. Yukarıda indirilen dosyadan bir Pandas veri çerçevesine veri okuma.

    ```python
    import pandas as pd

    #directly ready from file on disk
    dataframe_blobdata = pd.read_csv(LOCALFILE)
    ```

3. Aşağıdaki gibi kullanarak verileri aşağı doğru örnekleyin `numpy` `random.choice` :

    ```python
    # A 1 percent sample
    sample_ratio = 0.01 
    sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
    sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
    dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]
    ```

Artık, daha fazla keşif ve özellik oluşturma için yukarıdaki veri çerçevesiyle birlikte bir yüzde örneği ile çalışabilirsiniz.

## <a name="upload-data-and-read-it-into-azure-machine-learning"></a><a name="heading"></a>Verileri karşıya yükleme ve Azure Machine Learning okuma
Aşağıdaki örnek kodu kullanarak verileri aşağı örnekleyebilirsiniz ve doğrudan Azure Machine Learning ' de kullanabilirsiniz:

1. Veri çerçevesini yerel bir dosyaya yaz

    ```python
    dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
    ```

2. Aşağıdaki örnek kodu kullanarak yerel dosyayı bir Azure blobuna yükleyin:

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
        print ("Something went wrong with uploading to the blob:"+ BLOBNAME)
    ```

3. Aşağıdaki görüntüde gösterildiği gibi [verileri içeri](/azure/machine-learning/studio-module-reference/import-data) Azure Machine Learning kullanarak Azure Blob 'dan verileri okuyun:

![okuyucu blobu](./media/sample-data-blob/reader_blob.png)
