---
title: Hive sorguları ile Hive tablolarındaki verileri araştırma-Team Data Science Process
description: HDInsight Hadoop kümesindeki Hive tablolarındaki verileri araştırmak için kullanılan örnek Hive betikleri kullanın.
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
ms.openlocfilehash: a7234a8c45c20c64dddb43a52a099aa92f2d297d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93321114"
---
# <a name="explore-data-in-hive-tables-with-hive-queries"></a>Hive sorguları ile Hive tablosundaki verileri keşfedin

Bu makalede, bir HDInsight Hadoop kümesindeki Hive tablolarındaki verileri araştırmak için kullanılan örnek Hive betikleri sağlanmıştır.

Bu görev, [ekip veri bilimi işlemindeki](overview.md)bir adımdır.

## <a name="prerequisites"></a>Önkoşullar
Bu makalede sahip olduğunuz varsayılır:

* Bir Azure depolama hesabı oluşturuldu. Yönergelere ihtiyacınız varsa bkz. [Azure depolama hesabı oluşturma](../../storage/common/storage-account-create.md)
* HDInsight hizmeti ile özelleştirilmiş bir Hadoop kümesi sağlandı. Yönergelere ihtiyacınız varsa bkz. [Gelişmiş analiz için Azure HDInsight Hadoop kümelerini özelleştirme](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md).
* Veriler Azure HDInsight Hadoop kümelerinde Hive tablolarına yüklendi. Bu yoksa, önce verileri Hive tablolarına yüklemek üzere [Hive tablolarına veri oluşturma ve yükleme](move-hive-tables.md) konusundaki yönergeleri izleyin.
* Kümeye uzaktan erişim etkinleştirildi. Yönergelere ihtiyacınız varsa bkz. [Hadoop kümesinin baş düğümüne erişme](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md).
* Hive sorgularının nasıl gönderileceği hakkında yönergeler gerekiyorsa bkz. [Hive sorguları gönderme](move-hive-tables.md#submit)

## <a name="example-hive-query-scripts-for-data-exploration"></a>Veri araştırması için örnek Hive sorgu betikleri
1. Bölüm başına gözlemlerin sayısını Al  `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`
2. Gün başına gözlemlerin sayısını Al  `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`
3. Kategorik bir sütundaki düzeyleri al  
    `SELECT  distinct <column_name> from <databasename>.<tablename>`
4. İki kategorik sütunun birleşimi içindeki düzeylerin sayısını Al  `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`
5. Sayısal sütunlara yönelik dağıtımı al  
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`
6. İki tablonun birleştirilmesinden kayıtları Ayıkla
   
    ```hiveql
    SELECT
        a.<common_columnname1> as <new_name1>,
        a.<common_columnname2> as <new_name2>,
        a.<a_column_name1> as <new_name3>,
        a.<a_column_name2> as <new_name4>,
        b.<b_column_name1> as <new_name5>,
        b.<b_column_name2> as <new_name6>
    FROM
        (
        SELECT <common_columnname1>,
            <common_columnname2>,
            <a_column_name1>,
            <a_column_name2>,
        FROM <databasename>.<tablename1>
        ) a
        join
        (
        SELECT <common_columnname1>,
            <common_columnname2>,
            <b_column_name1>,
            <b_column_name2>,
        FROM <databasename>.<tablename2>
        ) b
        ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>
    ```

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>Taxı seyahat verileri senaryoları için ek sorgu betikleri
[NYC TAXI seyahat veri](https://chriswhong.com/open-data/foil_nyc_taxi/) senaryolarına özgü sorgu örnekleri de [GitHub deposunda](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts)sunulmaktadır. Bu sorguların veri şeması zaten belirtilmiş ve çalıştırılmaya gönderilmeye hazırlanıyor.