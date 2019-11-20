---
title: Azure Data Factory ile bir denetim tablosu kullanarak bir veritabanından Delta kopyası
description: Yalnızca Azure Data Factory olan bir veritabanından yeni veya güncelleştirilmiş satırları artımlı olarak kopyalamak için bir çözüm şablonu kullanmayı öğrenin.
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
ms.date: 12/24/2018
ms.openlocfilehash: c9ab1d005cf71dbe03546ce5b6014f616a872f8d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684214"
---
# <a name="delta-copy-from-a-database-with-a-control-table"></a>Denetim tablosu ile bir veritabanından Delta kopyası

Bu makalede, bir veritabanı tablosundan yeni veya güncelleştirilmiş satırları, bir üst eşik değerini depolayan bir dış denetim tablosu kullanılarak Azure 'a artımlı olarak yüklemek için kullanabileceğiniz bir şablon açıklanmaktadır.

Bu şablon, kaynak veritabanı şemasının bir zaman damgası sütunu içermesi veya yeni veya güncelleştirilmiş satırları tanımlamak için anahtar artırmasını gerektirir.

>[!NOTE]
> Kaynak veritabanınızda yeni veya güncelleştirilmiş satırları tanımlamak için bir zaman damgası sütunu varsa ancak Delta kopyası için kullanmak üzere bir dış denetim tablosu oluşturmak istemiyorsanız, bunun yerine bir işlem hattı almak için [Azure Data Factory veri kopyalama aracını](copy-data-tool.md) kullanabilirsiniz. Bu araç, kaynak veritabanından yeni satırları okumak için bir değişken olarak tetikleyici zamanlanan bir süre kullanır.

## <a name="about-this-solution-template"></a>Bu çözüm şablonu hakkında

Bu şablon ilk olarak eski eşik değerini alır ve geçerli filigran değeriyle karşılaştırır. Bundan sonra, iki filigran değeri arasındaki karşılaştırmaya göre yalnızca kaynak veritabanındaki değişiklikleri kopyalar. Son olarak, yeni yüksek filigrana değerini bir sonraki adımda Delta verileri için bir dış denetim tablosuna depolar.

Şablon dört etkinlik içerir:
- **Arama** , bir dış denetim tablosunda depolanan eski üst eşik değerini alır.
- Başka bir **arama** etkinliği, geçerli üst eşik değerini kaynak veritabanından alır.
- **Kopyalama** yalnızca kaynak veritabanındaki değişiklikleri hedef depoya kopyalar. Kaynak veritabanındaki değişiklikleri tanımlayan sorgu ' SELECT * FROM Data_Source_Table, TIMESTAMP_Column > "Last High-filigran" ve TIMESTAMP_Column < = "geçerli yüksek filigran" ' ile benzerdir.
- **Sqlserverstoredprocedure** , bir sonraki sefer değişim kopyası için geçerli yüksek eşik değerini bir dış denetim tablosuna yazar.

Şablon beş parametreyi tanımlar:
- *Data_Source_Table_Name* , kaynak veritabanındaki, verileri yüklemek istediğiniz tablodur.
- *Data_Source_WaterMarkColumn* , yeni veya güncelleştirilmiş satırları tanımlamak için kullanılan kaynak tablodaki sütunun adıdır. Bu sütunun türü genellikle *DateTime*, *Int*veya benzerdir.
- *Data_Destination_Folder_Path* veya *Data_Destination_Table_Name* , verilerin hedef deponuzda kopyalandığı yerdir.
- *Control_Table_Table_Name* , yüksek eşik değerini depolayan dış denetim tablosudur.
- *Control_Table_Column_Name* , üst eşik değerini depolayan dış denetim tablosundaki sütundur.

## <a name="how-to-use-this-solution-template"></a>Bu çözüm şablonunu kullanma

1. Yüklemek istediğiniz kaynak tabloyu bulun ve yeni veya güncelleştirilmiş satırları tanımlamak için kullanılabilecek yüksek filigranlı sütunu tanımlayın. Bu sütunun türü *DateTime*, *Int*veya benzeri olabilir. Yeni satır eklendikçe bu sütunun değeri artar. Aşağıdaki örnek kaynak tablosundan (data_source_table), *LastModifyTime* sütununu yüksek filigran sütunu olarak kullanabiliriz.

    ```sql
            PersonID    Name    LastModifytime
            1   aaaa    2017-09-01 00:56:00.000
            2   bbbb    2017-09-02 05:23:00.000
            3   cccc    2017-09-03 02:36:00.000
            4   dddd    2017-09-04 03:21:00.000
            5   eeee    2017-09-05 08:06:00.000
            6   fffffff 2017-09-06 02:23:00.000
            7   gggg    2017-09-07 09:01:00.000
            8   hhhh    2017-09-08 09:01:00.000
            9   iiiiiiiii   2017-09-09 09:01:00.000
    ```
    
2. SQL Server veya Azure SQL veritabanı 'nda, Delta veri yüklemesi için yüksek filigrandaki değeri depolamak üzere bir denetim tablosu oluşturun. Aşağıdaki örnekte, denetim tablosunun adı *su marktable*olur. Bu tabloda, *sulu markdeğer* üst-eşik değerini depolayan sütundur ve türü *DateTime*olur.

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. Aynı SQL Server veya denetim tablosunu oluşturmak için kullandığınız Azure SQL veritabanı örneğinde saklı yordam oluşturun. Saklı yordam, daha sonra Delta verileri yükleme için dış denetim tablosuna yeni yüksek boyutlu bir değer yazmak için kullanılır.

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. **Veritabanı şablonundan Delta kopyasına** gidin. Veri kopyalamak istediğiniz kaynak veritabanına **Yeni** bir bağlantı oluşturun.

    ![Kaynak tabloya yeni bir bağlantı oluşturun](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. Verileri kopyalamak istediğiniz hedef veri deposuna **Yeni** bir bağlantı oluşturun.

    ![Hedef tabloyla yeni bir bağlantı oluşturun](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. 2 ve 3. adımlarda oluşturduğunuz dış denetim tablosuna ve saklı yordama **Yeni** bir bağlantı oluşturun.

    ![Denetim tablosu veri deposuna yeni bir bağlantı oluşturun](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. **Bu şablonu kullan**' ı seçin.

     ![Bu şablonu kullan](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable7.png)
    
8. Aşağıdaki örnekte gösterildiği gibi kullanılabilir ardışık düzeni görürsünüz:

     ![İşlem hattını gözden geçirme](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. **Saklı yordam**' i seçin. **Saklı yordam adı**için **[update_watermark]** seçeneğini belirleyin. **Parametreyi Içeri aktar**' ı seçin ve ardından **dinamik içerik Ekle**' yi seçin.  

     ![Saklı yordam etkinliğini ayarlama](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png) 

10. **{Activity (' LookupCurrentWaterMark '). Output. firstRow. Newsulu Markvalue} içeriğini\@** yazın ve ardından **son**' u seçin.  

     ![Saklı yordamın parametrelerinin içeriğini yazma](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)      
     
11. **Hata Ayıkla**' yı seçin, **parametreleri**girin ve ardından **son**' u seçin.

    ![\* * Hata Ayıkla * * öğesini seçin](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. Aşağıdaki örneğe benzer sonuçlar görüntülenir:

    ![Sonucu gözden geçirin](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. Kaynak tablonuzda yeni satırlar oluşturabilirsiniz. Yeni satırlar oluşturmak için örnek SQL dili aşağıda verilmiştir:

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```
14. İşlem hattını yeniden çalıştırmak için **Hata Ayıkla**' yı seçin, **parametreleri**girin ve ardından **son**' u seçin.

    ![\* * Hata Ayıkla * * öğesini seçin](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

    Hedefe yalnızca yeni satırların kopyalandığını görürsünüz.

15. Seçim Veri hedefi olarak SQL veri ambarı ' nı seçtiyseniz, SQL veri ambarı PolyBase için gerekli olan hazırlama için Azure Blob depolama alanına da bir bağlantı sağlamanız gerekir. Kapsayıcının blob depolamada zaten oluşturulduğundan emin olun.
    
    ![PolyBase 'i yapılandırma](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>Sonraki adımlar

- [Azure Data Factory bir denetim tablosu kullanarak bir veritabanından toplu kopyalama](solution-template-bulk-copy-with-control-table.md)
- [Azure Data Factory ile birden çok kapsayıcıdan dosya kopyalama](solution-template-copy-files-multiple-containers.md)
