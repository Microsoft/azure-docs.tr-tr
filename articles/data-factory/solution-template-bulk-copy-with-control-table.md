---
title: Denetim tablosu kullanarak bir veritabanından toplu kopyalama
description: Azure Data Factory kullanarak kaynak tablolarının bölüm listesini depolamak için bir dış denetim tablosu kullanarak bir veritabanından toplu verileri kopyalamak için bir çözüm şablonu kullanmayı öğrenin.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/09/2020
ms.openlocfilehash: eed7a304bdd57846cd038cc9bf9a67e8150ca505
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100392468"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>Denetim tablosu ile bir veritabanından toplu kopyalama

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Oracle Server, Netezza, Teradata veya SQL Server içindeki bir veri ambarından verileri Azure SYNAPSE Analytics 'e kopyalamak için, birden fazla tablodan çok büyük miktarlarda veri yüklemeniz gerekir. Genellikle verilerin her tabloda bölümlenmesi gerekir, böylece birden çok iş parçacığı içeren satırları tek bir tablodan paralel olarak yükleyebilirsiniz. Bu makalede, bu senaryolarda kullanılacak bir şablon açıklanmaktadır.

 >! Azure SYNAPSE Analytics 'e görece küçük veri haciminden az sayıda tablodan veri kopyalamak istiyorsanız [Azure Data Factory veri kopyalama aracını](copy-data-tool.md)kullanmak daha etkilidir. Bu makalede açıklanan şablon, bu senaryoya yönelik gereksiniminden daha fazla.

## <a name="about-this-solution-template"></a>Bu çözüm şablonu hakkında

Bu şablon, bir dış denetim tablosundan Kopyalanacak kaynak veritabanı bölümlerinin listesini alır. Sonra kaynak veritabanındaki her bir bölümün üzerinde dolaşır ve verileri hedefe kopyalar.

Şablon üç etkinlik içerir:
- **Arama** , veritabanı bölümlerinin bir dış denetim tablosundan emin olma listesini alır.
- **ForEach** , arama etkinliğinden bölüm listesini alır ve her bölümü kopyalama etkinliğine yineler.
- **Kopya** , her bölümü kaynak veritabanı deposundan hedef depoya kopyalar.

Şablon aşağıdaki parametreleri tanımlar:
- *Control_Table_Name* , kaynak veritabanının bölüm listesini depolayan dış denetim tablonuz olur.
- *Control_Table_Schema_PartitionID* , her bölüm kimliğini depolayan dış denetim tablonuzda bulunan sütun adının adıdır. Bölüm KIMLIĞININ, kaynak veritabanındaki her bölüm için benzersiz olduğundan emin olun.
- *Control_Table_Schema_SourceTableName* , kaynak veritabanından her tablo adını depolayan dış denetim tablodır.
- *Control_Table_Schema_FilterQuery* , kaynak veritabanındaki her bölümden verileri almak için filtre sorgusunu depolayan dış denetim tablonuzun sütununun adıdır. Örneğin, verileri yıla göre bölümlüyetmeniz durumunda, her satırda depolanan sorgu, LastModifytime >= ' ' 2015-01-01 00:00:00 ' ' ve LastModifytime <= ' ' 2015-12-31 23:59:59.999 ' ' ' olan ' select * from DataSource ' a benzer olabilir.
- *Data_Destination_Folder_Path* , verilerin hedef deponuza kopyalandığı yoldur (seçtiğiniz hedef "dosya sistemi" veya "Azure Data Lake Storage 1." ise geçerlidir). 
- *Data_Destination_Container* , verilerin hedef deponuzda kopyalandığı kök klasör yoludur. 
- *Data_Destination_Directory* , verilerin hedef deponuza kopyalandığı kök altındaki Dizin yoludur. 

Hedef deponuzda yolu tanımlayan son üç parametre yalnızca seçtiğiniz hedef dosya tabanlı depolama ise görünür. Hedef depo olarak "Azure SYNAPSE Analytics" i seçerseniz bu parametreler gerekli değildir. Ancak Azure SYNAPSE Analytics 'teki tablo adları ve şema, kaynak veritabanındaki olanlarla aynı olmalıdır.

## <a name="how-to-use-this-solution-template"></a>Bu çözüm şablonunu kullanma

1. Toplu kopyalama için kaynak veritabanı bölüm listesini depolamak üzere SQL Server veya Azure SQL veritabanı 'nda bir denetim tablosu oluşturun. Aşağıdaki örnekte, kaynak veritabanında beş bölüm vardır. *Datasource_table* için üç bölüm vardır ve ikisi *project_table* içindir. *LastModifyTime* sütunu, tablo *datasource_table* içindeki verileri kaynak veritabanından bölümlemek için kullanılır. İlk bölümü okumak için kullanılan sorgu, LastModifytime >= ' ' 2015-01-01 00:00:00 ' ' ve LastModifytime <= ' ' 2015-12-31 23:59:59.999 ' ' ' olan ' select * from datasource_table. Diğer bölümlerden verileri okumak için benzer bir sorgu kullanabilirsiniz.

     ```sql
            Create table ControlTableForTemplate
            (
            PartitionID int,
            SourceTableName  varchar(255),
            FilterQuery varchar(255)
            );

            INSERT INTO ControlTableForTemplate
            (PartitionID, SourceTableName, FilterQuery)
            VALUES
            (1, 'datasource_table','select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''),
            (2, 'datasource_table','select * from datasource_table where LastModifytime >= ''2016-01-01 00:00:00'' and LastModifytime <= ''2016-12-31 23:59:59.999'''),
            (3, 'datasource_table','select * from datasource_table where LastModifytime >= ''2017-01-01 00:00:00'' and LastModifytime <= ''2017-12-31 23:59:59.999'''),
            (4, 'project_table','select * from project_table where ID >= 0 and ID < 1000'),
            (5, 'project_table','select * from project_table where ID >= 1000 and ID < 2000');
    ```

2. **Veritabanı şablonundan toplu kopyalama** sayfasına gidin. Adım 1 ' de oluşturduğunuz dış denetim tablosuna **Yeni** bir bağlantı oluşturun.

    ![Denetim tablosuna yeni bir bağlantı oluşturun](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. Verileri kopyalamakta olduğunuz kaynak veritabanına **Yeni** bir bağlantı oluşturun.

    ![Kaynak veritabanıyla yeni bir bağlantı oluşturun](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. Verileri kopyalamakta olduğunuz hedef veri deposuna **Yeni** bir bağlantı oluşturun.

    ![Hedef depoya yeni bir bağlantı oluşturun](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. **Bu şablonu kullan**' ı seçin.

6. Aşağıdaki örnekte gösterildiği gibi ardışık düzeni görürsünüz:

    ![İşlem hattını gözden geçirme](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. **Hata Ayıkla**' yı seçin, **parametreleri** girin ve ardından **son**' u seçin.

    ![* * Hata Ayıkla * * öğesine tıklayın](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. Aşağıdaki örneğe benzer sonuçlar görürsünüz:

    ![Sonucu gözden geçirin](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. Seçim Veri hedefi olarak "Azure SYNAPSE Analytics" seçeneğini belirlediyseniz Azure SYNAPSE Analytics PolyBase 'in gerektirdiği şekilde hazırlama için Azure Blob depolama alanına bir bağlantı girmeniz gerekir. Şablon, BLOB depolama alanınızı otomatik olarak bir kapsayıcı yolu oluşturacaktır. İşlem hattı çalıştırıldıktan sonra kapsayıcının oluşturulup oluşturulmadıysa emin olun.
    
    ![PolyBase ayarı](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>Sonraki adımlar

- [Azure Data Factory'ye giriş](introduction.md)
