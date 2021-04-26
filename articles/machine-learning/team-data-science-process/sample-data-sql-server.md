---
title: Azure 'da SQL Server örnek veriler-ekip veri bilimi Işlemi
description: SQL veya Python programlama dili kullanılarak Azure üzerinde SQL Server depolanan örnek veriler, sonra da Azure Machine Learning taşıyın.
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
ms.openlocfilehash: 7ac1fc5688dad3406041f36ff858e6fd27c7272f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93321876"
---
# <a name="sample-data-in-sql-server-on-azure"></a><a name="heading"></a>Azure’da SQL Server’daki örnek veriler

Bu makalede, SQL veya Python programlama dili kullanılarak Azure üzerinde SQL Server depolanan örnek verilerin nasıl oluşturulacağı gösterilmektedir. Ayrıca, örneği bir dosyaya kaydederek, bir Azure blobuna yükleyerek ve sonra da Azure Machine Learning Studio okuyarak, Örneklenmiş verilerin Azure Machine Learning nasıl taşınacağını gösterir.

Python örneklemesi [pyodbc](https://code.google.com/p/pyodbc/) ODBC kitaplığını kullanarak örnekleme yapmak için Azure 'daki SQL Server ve [Pandas](https://pandas.pydata.org/) kitaplığı 'na bağlanır.

> [!NOTE]
> Bu belgedeki örnek SQL kodu, verilerin Azure 'da SQL Server olduğunu varsayar. Aksi takdirde, verilerinizi Azure 'da SQL Server taşımaya ilişkin yönergeler için [Azure 'da SQL Server verileri taşıma](move-sql-server-virtual-machine.md) makalesine başvurun.
> 
> 

**Verileriniz neden örnekleyebilirsiniz?**
Çözümlemeyi planladığınız veri kümesi büyükse, daha küçük ancak temsili ve daha yönetilebilir bir boyutla azaltmak için verileri düşürmek genellikle iyi bir fikirdir. Örnekleme, veri anlama, araştırma ve özellik mühendisliğini kolaylaştırır. [Ekip veri bilimi işlemindeki (TDSP)](./index.yml) rolü, veri işleme işlevlerinin ve makine öğrenimi modellerinin hızlı prototipini etkinleştirmektir.

Bu örnekleme görevi, [ekip veri bilimi işlemindeki (TDSP)](./index.yml)bir adımdır.

## <a name="using-sql"></a><a name="SQL"></a>SQL kullanma
Bu bölümde, veritabanındaki verilere karşı basit rastgele örnekleme gerçekleştirmek için SQL kullanan çeşitli yöntemler açıklanmıştır. Veri boyutunuzu ve dağılımını temel alan bir yöntem seçin.

Aşağıdaki iki öğe, `newid` örnekleme işlemini gerçekleştirmek için SQL Server ' de nasıl kullanılacağını göstermektedir. Seçtiğiniz yöntem, örneğin, örnek (Aşağıdaki örnek kodda pk_id, bir otomatik olarak oluşturulan birincil anahtar olarak kabul edilir) ne kadar rastgele olmasını istediğinize bağlıdır.

1. Daha az sıkı rastgele örnek

    ```sql
    select  * from <table_name> where <primary_key> in 
    (select top 10 percent <primary_key> from <table_name> order by newid())
    ```

2. Daha fazla rastgele örnek 

    ```sql
    SELECT * FROM <table_name>
    WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)
    ```

Can, verileri örneklemede de kullanılabilir. Bu seçenek, veri boyutunuz büyükse (farklı sayfalardaki verilerin bağıntılı olmadığı varsayıldığında) ve sorgunun makul bir süre içinde tamamlanacağı daha iyi bir yaklaşım olabilir.

```sql
SELECT *
FROM <table_name> 
TABLESAMPLE (10 PERCENT)
```

> [!NOTE]
> Yeni bir tabloda depolayarak bu örneklenmiş verilerden Özellikler araştırabilir ve oluşturabilirsiniz
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Azure Machine Learning bağlanılıyor
[Verileri Içeri aktarma][import-data] modülünde Azure Machine Learning yukarıdaki örnek sorguları doğrudan kullanarak, verileri anında örnekleyebilirsiniz ve bir Azure Machine Learning denemesine taşıyabilirsiniz. Örnek verileri okumak için okuyucu modülünü kullanmanın ekran görüntüsü burada gösterilmektedir:

![okuyucu SQL][1]

## <a name="using-the-python-programming-language"></a><a name="python"></a>Python programlama dilini kullanma
Bu bölümde, Python 'da bir SQL Server veritabanına ODBC bağlantısı kurmak için [pyodbc kitaplığı](https://code.google.com/p/pyodbc/) kullanılması gösterilmektedir. Veritabanı bağlantı dizesi şu şekildedir: (ServerName, dbname, username ve Password öğesini yapılandırmanızla değiştirin):

```python
#Set up the SQL Azure connection
import pyodbc    
conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')
```

Python 'daki [Pandas](https://pandas.pydata.org/) kitaplığı, Python programlamasına yönelik veri işleme için zengin veri yapıları ve veri çözümleme araçları sağlar. Aşağıdaki kod, Azure SQL veritabanındaki bir tablodaki verilerin% 0,1 örneğini bir Pandas verilerine okur:

```python
import pandas as pd

# Query database and load the returned results in pandas data frame
data_frame = pd.read_sql('''select column1, column2... from <table_name> tablesample (0.1 percent)''', conn)
```

Artık Pandas veri çerçevesindeki örneklenmiş verilerle çalışabilirsiniz. 

### <a name="connecting-to-azure-machine-learning"></a><a name="python-aml"></a>Azure Machine Learning bağlanılıyor
Aşağıdaki örnek kodu kullanarak, aşağı örneklenir verileri bir dosyaya kaydedebilir ve bir Azure Blob 'una yükleyebilirsiniz. Blob 'daki veriler, [verileri Içeri aktarma][import-data] modülünü kullanarak doğrudan bir Azure Machine Learning denemesine okunabilir. Adımlar şu şekildedir: 

1. Pandas veri çerçevesini yerel bir dosyaya yaz

    ```python
    dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
    ```

2. Yerel dosyayı Azure Blob 'a yükleme

    ```python
    from azure.storage import BlobService
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

3. Aşağıdaki ekranda gösterildiği gibi Azure Machine Learning [veri alma][import-data] modülünü kullanarak Azure Blob 'dan verileri okuyun:

![okuyucu blobu][2]

## <a name="the-team-data-science-process-in-action-example"></a>Takım veri bilimi Işlemi eylem örneği
Ortak veri kümesi kullanan bir ekip veri bilimi Işlemi örneğini görmek için, bkz. [Team Data Science Process ın Action: using SQL Server](sql-walkthrough.md).

[1]: ./media/sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/sample-sql-server-virtual-machine/reader_blob.png

[import-data]: /azure/machine-learning/studio-module-reference/import-data