---
title: SQL Server sanal makinedeki verileri araştırma-takım veri bilimi Işlemi
description: Azure 'da SQL Server bir sanal makinede bulunan ve Python ya da SQL kullanarak verileri bulun + işleme ve özellik oluşturun.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/23/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 877c639c35378b173b6ec9c8697725e3b3c09290
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053610"
---
# <a name="heading"></a>Azure 'da SQL Server sanal makinesindeki verileri işleme
Bu belgede, Azure 'daki bir SQL Server VM depolanan veriler için verilerin nasıl araştırılacak ve özelliklerin nasıl oluşturulacağı ele alınmaktadır. Bu, SQL kullanılarak veya Python gibi bir programlama dili kullanılarak veri denetimi tarafından yapılabilir.

> [!NOTE]
> Bu belgedeki örnek SQL deyimleri, verilerin SQL Server olduğunu varsayar. Değilse, verilerinizi SQL Server nasıl taşıyacağınızı öğrenmek için bulut veri bilimi süreç haritasına bakın.
> 
> 

## <a name="SQL"></a>SQL kullanma
Bu bölümde SQL kullanarak aşağıdaki veri denetimi görevlerini açıklıyoruz:

1. [Veri araştırması](#sql-dataexploration)
2. [Özellik oluşturma](#sql-featuregen)

### <a name="sql-dataexploration"></a>Veri araştırması
Aşağıda, SQL Server veri depolarını araştırmak için kullanılabilecek birkaç örnek SQL komut dosyası verilmiştir.

> [!NOTE]
> Pratik bir örnek için, [NYC TAXI veri kümesini](https://www.andresmh.com/nyctaxitrips/) kullanabilir ve [IPython Not defteri 'ni kullanarak ıpnb başlıklı NYC veri denetimi](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) öğesine başvurabilirsiniz ve bir uçtan uca izlenecek yol SQL Server.
> 
> 

1. Gün başına gözlemlerin sayısını Al
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Kategorik bir sütundaki düzeyleri al
   
    `select  distinct <column_name> from <databasename>`
3. İki kategorik sütunun birleşimi içindeki düzeylerin sayısını Al 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Sayısal sütunlara yönelik dağıtımı al
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

### <a name="sql-featuregen"></a>Özellik oluşturma
Bu bölümde, SQL kullanarak özellik oluşturma yollarını anlatmaktadır:  

1. [Sayı tabanlı özellik oluşturma](#sql-countfeature)
2. [Özellik oluşturmayı atma](#sql-binningfeature)
3. [Tek bir sütundan özellikler kullanıma alınıyor](#sql-featurerollout)

> [!NOTE]
> Ek özellikler oluşturduktan sonra, bunları mevcut tabloya sütunlar olarak ekleyebilir veya ek özellikler ve birincil anahtarla birlikte, özgün tabloyla birleştirilebilecek yeni bir tablo oluşturabilirsiniz. 
> 
> 

### <a name="sql-countfeature"></a>Sayı tabanlı özellik oluşturma
Aşağıdaki örneklerde, Count özellikleri oluşturmanın iki yolu gösterilmektedir. İlk yöntem koşullu Sum kullanır ve ikinci yöntem ' WHERE ' yan tümcesini kullanır. Bunlar, özgün verilerle birlikte Count özelliklerinin olması için özgün tabloyla (birincil anahtar sütunları kullanılarak) eklenebilir.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 

### <a name="sql-binningfeature"></a>Özellik oluşturmayı atma
Aşağıdaki örnek, bir özellik olarak kullanılabilecek bir sayısal sütun (beş bölme kullanılarak) binerek nasıl oluşturulduğu gösterilmektedir:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>Tek bir sütundan özellikler kullanıma alınıyor
Bu bölümde, ek özellikler oluşturmak için tablodaki tek bir sütunun nasıl alınacağını gösteririz. Örnek, özellik oluşturmaya çalıştığınız tabloda bir enlem veya boylam sütununun olduğunu varsayar.

Latitude/Boylam konum verilerine ilişkin kısa bir açıklama aşağıda verilmiştir. StackOverflow, [Latitude ve Boylam 'in doğruluğunu nasıl ölçecek?](https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude)). Bu, konum alanını değiştirmeden önce anlaşılması yararlı olur:

* Bu işaret, dünyanın dört bir yanında Kuzey veya Güney, Doğu veya Batı olduğunu bize söyler.
* Sıfır dışında yüzlerce basamak, enlem kullandığımızda Latitude değil, bu bizi söyler!
* Onlarca, yaklaşık 1.000 kiloters bir konum sağlar. Bu, ne kıtamızda veya okyanumuz hakkında faydalı bilgiler verir.
* Birim sayısı (bir ondalık derece), 111 kilolara kadar (60 nadeniz mili, yaklaşık 69 mil) bir konum verir. Bu, size kabaca hangi eyalet, ülke veya bölge hakkında bilgi verebilir.
* İlk ondalık basamak 11,1 km 'ye kadar olur: büyük bir şehrin konumunu bir komşu büyük şehirden ayırt edebilir.
* İkinci ondalık basamak 1,1 km 'ye kadar olur: sonraki bir ikisi de bir köyana ayrılabilir.
* Üçüncü ondalık basamak 110.100 ' e kadar olur: büyük bir agricultürel alanını veya kurum kampılcığu tanımlayabilir.
* Dördüncü ondalık basamak, 11 ' e kadar olur: bir Land düzeyini tanımlayabilir. Hiçbir girişim olmadan düzeltilmeyen bir GPS biriminin tipik doğruluğu ile karşılaştırılabilir.
* Beşinci ondalık basamak 1,1 e kadar olur: ağaçları birbirinden ayırır. Ticari GPS birimleri ile bu düzeyin doğruluğu yalnızca değişiklik düzeltme ile elde edilebilir.
* Altıncı ondalık basamak 0,11 ' ye kadar olur: bu yapıyı, yapıları tasarlamak için, ara dscapes 'yi tasarlamak için kullanabilirsiniz. Bu, glatik ve Rivers hareketlerini izlemek için yeterince iyi olmalıdır. Bu, aynı şekilde düzeltilen GPS gibi, GPS ile sorunsuz ölçümler alınarak elde edilebilir.

Konum bilgileri, bölge, konum ve şehir bilgilerini ayırarak aşağıdaki şekilde değiştirilebilir. Ayrıca bölge/bölge bilgilerini almak için [noktaya göre konum bul](https://msdn.microsoft.com/library/ff701710.aspx) ' da bulunan Bing Haritalar API 'si gıbı bir REST uç noktasını da çağırabileceğinizi unutmayın.

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

Bu konum tabanlı özellikler, daha önce açıklandığı gibi ek sayı özellikleri oluşturmak için daha fazla kullanılabilir. 

> [!TIP]
> Seçtiğiniz dili kullanarak kayıtları program aracılığıyla ekleyebilirsiniz. Yazma verimliliğini artırmak için verileri parçalara eklemeniz gerekebilir (pyodbc kullanarak bunun nasıl yapılacağı hakkında bir örnek için bkz. [Python Ile SqlServer 'e erişmek Için HelloWorld örneği](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python)). Diğer bir seçenek de [bcp yardımcı programını](https://msdn.microsoft.com/library/ms162802.aspx)kullanarak veritabanına veri eklemedir.
> 
> 

### <a name="sql-aml"></a>Azure Machine Learning bağlanılıyor
Yeni oluşturulan özellik var olan bir tabloya bir sütun olarak eklenebilir veya yeni bir tabloda depolanır ve Machine Learning için özgün tabloyla birleştirilmiş olur. Aşağıda gösterildiği gibi, Azure Machine Learning [veri Içeri aktarma][import-data] modülü kullanılarak önceden oluşturulmuş veya erişilebilir olan özellikler kullanılabilir:

![azureml okuyucuları][1] 

## <a name="python"></a>Python gibi bir programlama dilini kullanma
Veri [bilimi ortamınızda Azure blob verilerini işleme](data-blob.md)bölümünde belgelendiği gibi verileri araştırmak SQL Server ve verileri Incelemek için Python 'u kullanma, Python kullanarak Azure Blob 'daki verileri işlemeye benzer. Verilerin veritabanından bir Pandas veri çerçevesine yüklenmesi ve daha sonra işlenebilmesi gerekir. Veritabanına bağlanma ve verileri bu bölümdeki veri çerçevesine yükleme işlemini belgeliyoruz.

Aşağıdaki bağlantı dizesi biçimi pyodbc (ServerName, dbname, username ve Password değerlerini belirli değerlerinizle değiştirin) kullanılarak Python 'dan bir SQL Server veritabanına bağlanmak için kullanılabilir:

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

Python 'daki [Pandas kitaplığı](https://pandas.pydata.org/) , Python programlamasına yönelik veri işleme için zengin veri yapıları ve veri çözümleme araçları sağlar. Aşağıdaki kod, bir SQL Server veritabanından bir Pandas veri çerçevesine döndürülen sonuçları okur:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Artık, [veri bilimi ortamınızda Azure blob verilerini işleme](data-blob.md)makalesinde bahsedilen Pandas veri çerçevesiyle birlikte çalışabilirsiniz.

## <a name="azure-data-science-in-action-example"></a>Azure veri bilimi eylem örneği
Genel bir veri kümesi kullanan Azure veri bilimi Işleminin uçtan uca bir anlatım örneği için bkz. [Azure Data Science Process ın Action](sql-walkthrough.md).

[1]: ./media/sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

