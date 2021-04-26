---
title: SQL ve Python-Team veri bilimi Işlemini kullanarak SQL Server özellikler oluşturma
description: Team Data Science Işleminin SQL ve Python-parçası kullanılarak Azure 'da bir SQL Server VM depolanan veriler için özellikler oluşturun.
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
ms.openlocfilehash: 3c20bf1c5856276c4c7ee0e37ed4ef2120d1d93d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93322038"
---
# <a name="create-features-for-data-in-sql-server-using-sql-and-python"></a>SQL ve Python kullanarak SQL Server’daki verilerin özelliklerini oluşturma
Bu belgede, algoritmalardan daha verimli bir şekilde bilgi edinmenize yardımcı olan Azure 'da SQL Server VM depolanan veriler için nasıl özellik oluşturulacağı gösterilmektedir. Bu görevi gerçekleştirmek için SQL veya Python gibi bir programlama dilini kullanabilirsiniz. Burada her iki yaklaşım da gösterilmiştir.

Bu görev, [ekip veri bilimi işlemindeki (TDSP)](./index.yml)bir adımdır.

> [!NOTE]
> Pratik bir örnek için, [NYC TAXI veri kümesine](https://www.andresmh.com/nyctaxitrips/) başvurabilirsiniz ve [IPython Not defteri 'ni kullanarak ıpnb başlıklı NYC Data denetimi](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) öğesine başvurabilirsiniz ve uçtan uca bir izlenecek yol SQL Server.
> 
> 

## <a name="prerequisites"></a>Önkoşullar
Bu makalede sahip olduğunuz varsayılır:

* Bir Azure depolama hesabı oluşturuldu. Yönergelere ihtiyacınız varsa bkz. [Azure depolama hesabı oluşturma](../../storage/common/storage-account-create.md)
* Verileriniz SQL Server depolandı. Aksi takdirde, verileri buraya taşıma hakkında yönergeler için bkz. [verileri Azure SQL veritabanına taşıma Azure Machine Learning](move-sql-azure.md) .

## <a name="feature-generation-with-sql"></a><a name="sql-featuregen"></a>SQL ile özellik oluşturma
Bu bölümde, SQL kullanarak özellik oluşturma yollarını anlatmaktadır:  

* [Sayı tabanlı özellik oluşturma](#sql-countfeature)
* [Özellik oluşturmayı atma](#sql-binningfeature)
* [Tek bir sütundan özellikler kullanıma alınıyor](#sql-featurerollout)

> [!NOTE]
> Ek özellikler oluşturduktan sonra, bunları mevcut tabloya sütunlar olarak ekleyebilir veya ek özellikler ve birincil anahtarla birlikte, özgün tabloyla birleştirilebilecek yeni bir tablo oluşturabilirsiniz.
> 
> 

### <a name="count-based-feature-generation"></a><a name="sql-countfeature"></a>Sayı tabanlı özellik oluşturma
Bu belgede, Count özellikleri oluşturmanın iki yolu gösterilmektedir. İlk yöntem koşullu Sum kullanır ve ikinci yöntem ' WHERE ' yan tümcesini kullanır. Bu yeni özellikler daha sonra orijinal verilerle birlikte sayı özelliklerinin olması için özgün tabloyla (birincil anahtar sütunları kullanılarak) birleştirilmiş olabilir.

```sql
select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3>

select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename>
where <column_name3> = '<some_value>' group by <column_name1>,<column_name2>
```

### <a name="binning-feature-generation"></a><a name="sql-binningfeature"></a>Özellik oluşturmayı atma
Aşağıdaki örnek, bir özellik olarak kullanılabilecek bir sayısal sütun (beş bölme kullanılarak) binerek nasıl oluşturulduğu gösterilmektedir:

```sql
SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>
```


### <a name="rolling-out-the-features-from-a-single-column"></a><a name="sql-featurerollout"></a>Tek bir sütundan özellikler kullanıma alınıyor
Bu bölümde, ek özellikler oluşturmak için tablodaki tek bir sütunun nasıl alınacağını gösteririz. Örnek, özellik oluşturmaya çalıştığınız tabloda bir enlem veya boylam sütununun olduğunu varsayar.

Latitude/Boylam konum verilerine (StackOverflow kaynağından alınan) ilişkin kısa bir açıklama aşağıda verilmiştir `https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude` . Burada, alandan Özellikler oluşturmadan önce konum verileri hakkında anlaşılması gereken bazı yararlı noktalar verilmiştir:

* İşaret, dünyanın dört bir yanında Kuzey veya Güney, Doğu veya Batı olduğunu gösterir.
* Sıfır olmayan yüzlerce basamak Boylam 'yi gösterir, enlem kullanılır.
* Onlarca, yaklaşık 1.000 kiloters bir konum sağlar. Bu, ne kıdığımız veya okyanumuz hakkında faydalı bilgiler sağlar.
* Birim sayısı (bir ondalık derece), 111 kilolara kadar (60 nadeniz mili, yaklaşık 69 mil) bir konum verir. Kabaca, hangi büyük eyalet veya ülke/bölge olduğunu gösterir.
* İlk ondalık basamak 11,1 km 'ye kadar olur: büyük bir şehrin konumunu bir komşu büyük şehirden ayırt edebilir.
* İkinci ondalık basamak 1,1 km 'ye kadar olur: sonraki bir ikisi de bir köyana ayrılabilir.
* Üçüncü ondalık basamak 110.100 ' e kadar olur: büyük bir agricultürel alanını veya kurum kampılcığu tanımlayabilir.
* Dördüncü ondalık basamak, 11 ' e kadar olur: bir Land düzeyini tanımlayabilir. Hiçbir girişim olmadan düzeltilmeyen bir GPS biriminin tipik doğruluğu ile karşılaştırılabilir.
* Beşinci ondalık basamak 1,1 e kadar olur: ağaçları birbirinden ayırır. Ticari GPS birimleri ile bu düzeyin doğruluğu yalnızca değişiklik düzeltme ile elde edilebilir.
* Altıncı ondalık basamak 0,11 ' ye kadar olur: Bu düzeyi, yapıları tasarlamak için, yapıları oluşturmak amacıyla yapıları ayrıntılı olarak düzenlemek için kullanabilirsiniz. Bu, glatik ve Rivers hareketlerini izlemek için yeterince iyi olmalıdır. Bu amaca, farklı şekilde düzeltilen GPS gibi GPS ile sorunsuz ölçüler elde edilebilir.

Konum bilgileri bölge, konum ve şehir bilgilerini ayırarak eklenebilir. Bir kez, Bing Haritalar API 'SI gibi bir REST uç noktasını da çağırabilir ( `https://msdn.microsoft.com/library/ff701710.aspx` bölge/bölge bilgilerini almak için bkz.).

```sql
select
    <location_columnname>
    ,round(<location_columnname>,0) as l1        
    ,l2=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 1 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),1,1) else '0' end     
    ,l3=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 2 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),2,1) else '0' end     
    ,l4=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 3 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),3,1) else '0' end     
    ,l5=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 4 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),4,1) else '0' end     
    ,l6=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 5 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),5,1) else '0' end     
    ,l7=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 6 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),6,1) else '0' end     
from <tablename>
```

Bu konum tabanlı özellikler, daha önce açıklandığı gibi ek sayı özellikleri oluşturmak için daha fazla kullanılabilir.

> [!TIP]
> Seçtiğiniz dili kullanarak kayıtları program aracılığıyla ekleyebilirsiniz. Yazma verimliliğini artırmak için verileri parçalara eklemeniz gerekebilir. [Bu, pyodbc kullanarak bunun nasıl yapılacağını gösteren bir örnektir](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python).
> [Bcp yardımcı programını](/sql/tools/bcp-utility) kullanarak veritabanına veri eklemek diğer bir alternatiftir
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Azure Machine Learning bağlanılıyor
Yeni oluşturulan özellik var olan bir tabloya bir sütun olarak eklenebilir veya yeni bir tabloda depolanır ve Machine Learning için özgün tabloyla birleştirilmiş olur. Aşağıda gösterildiği gibi, Azure ML 'de [veri Içeri aktarma](/azure/machine-learning/studio-module-reference/import-data) modülü kullanılarak önceden oluşturulmuş özellikler oluşturulabilir veya erişilebilir:

![Azure ML okuyucuları](./media/sql-server-virtual-machine/reader_db_featurizedinput.png)

## <a name="using-a-programming-language-like-python"></a><a name="python"></a>Python gibi bir programlama dilini kullanma
Veriler SQL Server özellikler oluşturmak için Python kullanma Python kullanarak Azure Blob 'daki verileri işlemeye benzerdir. Karşılaştırma için bkz. [veri bilimi ortamınızda Azure blob verilerini işleme](data-blob.md). Verileri daha sonra işlemek için veritabanından bir Pandas veri çerçevesine yükleyin. Veritabanına bağlanma ve verileri veri çerçevesine yükleme işlemi bu bölümde belgelenmiştir.

Aşağıdaki bağlantı dizesi biçimi pyodbc (ServerName, dbname, username ve Password değerlerini belirli değerlerinizle değiştirin) kullanılarak Python 'dan bir SQL Server veritabanına bağlanmak için kullanılabilir:

```python
#Set up the SQL Azure connection
import pyodbc
conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')
```

Python 'daki [Pandas kitaplığı](https://pandas.pydata.org/) , Python programlamasına yönelik veri işleme için zengin veri yapıları ve veri çözümleme araçları sağlar. Aşağıdaki kod, bir SQL Server veritabanından bir Pandas veri çerçevesine döndürülen sonuçları okur:

```python
# Query database and load the returned results in pandas data frame
data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)
```

Artık, [Panda kullanarak Azure Blob depolama verilerine yönelik özellikler oluşturma](./explore-data-blob.md)konu başlığı altında, Pandas veri çerçevesiyle birlikte çalışabilirsiniz.