---
title: Azure Data Factory bir denetim tablosu kullanarak bir veritabanından toplu kopyalama
description: Azure Data Factory kullanarak kaynak tablolarının bölüm listesini depolamak için bir dış denetim tablosu kullanarak bir veritabanından toplu verileri kopyalamak için bir çözüm şablonu kullanmayı öğrenin.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/14/2018
ms.openlocfilehash: b651721e9b833c02e4789c79ff5ad0b49ce31343
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684285"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>Denetim tablosu ile bir veritabanından toplu kopyalama

Oracle Server, Netezza, Teradata veya SQL Server içindeki bir veri ambarından verileri Azure SQL veri ambarı 'na kopyalamak için, birden fazla tablodan çok büyük miktarlarda veri yüklemeniz gerekir. Genellikle verilerin her tabloda bölümlenmesi gerekir, böylece birden çok iş parçacığı içeren satırları tek bir tablodan paralel olarak yükleyebilirsiniz. Bu makalede, bu senaryolarda kullanılacak bir şablon açıklanmaktadır.

 >! NOTE görece küçük veri hacimlerden oluşan az sayıda tablodan SQL veri ambarı 'na veri kopyalamak istiyorsanız, [Azure Data Factory veri kopyalama aracını](copy-data-tool.md)kullanmak daha etkilidir. Bu makalede açıklanan şablon, bu senaryoya yönelik gereksiniminden daha fazla.

## <a name="about-this-solution-template"></a>Bu çözüm şablonu hakkında

Bu şablon, bir dış denetim tablosundan Kopyalanacak kaynak veritabanı bölümlerinin listesini alır. Sonra kaynak veritabanındaki her bir bölümün üzerinde dolaşır ve verileri hedefe kopyalar.

Şablon üç etkinlik içerir:
- **Arama** , veritabanı bölümlerinin bir dış denetim tablosundan emin olma listesini alır.
- **ForEach** , arama etkinliğinden bölüm listesini alır ve her bölümü kopyalama etkinliğine yineler.
- **Kopya** , her bölümü kaynak veritabanı deposundan hedef depoya kopyalar.

Şablon beş parametreyi tanımlar:
- *Control_Table_Name* , kaynak veritabanının bölüm listesini depolayan dış denetim tablonuz olur.
- *Control_Table_Schema_PartitionID* , her bölüm kimliğini depolayan dış denetim tablonuzda bulunan sütun adının adıdır. Bölüm KIMLIĞININ, kaynak veritabanındaki her bölüm için benzersiz olduğundan emin olun.
- *Control_Table_Schema_SourceTableName* , her tablo adını kaynak veritabanından depolayan dış denetim tablodır.
- *Control_Table_Schema_FilterQuery* , kaynak veritabanındaki her bölümden verileri almak için filtre sorgusunu depolayan dış denetim tablonuzun sütununun adıdır. Örneğin, verileri yıla göre bölümlüyetmeniz durumunda, her satırda depolanan sorgu, LastModifytime > = ' ' 2015-01-01 00:00:00 ' ' ve LastModifytime < = ' ' 2015-12-31 23:59:59.999 ' ' ' olan ' select * from DataSource ' a benzer olabilir.
- *Data_Destination_Folder_Path* , verilerin hedef deponuza kopyalandığı yoldur. Bu parametre yalnızca seçtiğiniz hedef dosya tabanlı depolama ise görünür. Hedef depo olarak SQL veri ambarı ' nı seçerseniz, bu parametre gerekli değildir. Ancak SQL veri ambarı 'ndaki tablo adları ve şema, kaynak veritabanındaki olanlarla aynı olmalıdır.

## <a name="how-to-use-this-solution-template"></a>Bu çözüm şablonunu kullanma

1. Toplu kopyalama için kaynak veritabanı bölüm listesini depolamak üzere SQL Server veya Azure SQL veritabanı 'nda bir denetim tablosu oluşturun. Aşağıdaki örnekte, kaynak veritabanında beş bölüm vardır. *Datasource_table*için üç bölüm vardır ve ikisi de *project_table*içindir. *LastModifyTime* sütunu, kaynak veritabanındaki *datasource_table* tablosundaki verileri bölümlemek için kullanılır. İlk bölümü okumak için kullanılan sorgu "select * from datasource_table WHERE LastModifytime > = ' ' 2015-01-01 00:00:00 ' ' ve LastModifytime < = ' ' 2015-12-31 23:59:59.999 ' ' '. Diğer bölümlerden verileri okumak için benzer bir sorgu kullanabilirsiniz.

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

    ![Bu şablonu kullan](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable5.png)
    
6. Aşağıdaki örnekte gösterildiği gibi ardışık düzeni görürsünüz:

    ![İşlem hattını gözden geçirme](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. **Hata Ayıkla**' yı seçin, **parametreleri**girin ve ardından **son**' u seçin.

    ![\* * Hata Ayıkla * * öğesine tıklayın](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. Aşağıdaki örneğe benzer sonuçlar görürsünüz:

    ![Sonucu gözden geçirin](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. Seçim Veri hedefi olarak SQL veri ambarı ' nı seçtiyseniz, SQL veri ambarı PolyBase 'in gerektirdiği şekilde hazırlama için Azure Blob depolama alanına bir bağlantı girmeniz gerekir. BLOB depolama alanındaki kapsayıcının zaten oluşturulduğundan emin olun.
    
    ![PolyBase ayarı](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>Sonraki adımlar

- [Azure Data Factory'ye giriş](introduction.md)
