---
title: Apache Hive ile Azure HDInsight ile & işlem JSON 'ı çözümleme
description: JSON belgelerini nasıl kullanacağınızı ve Azure HDInsight 'ta Apache Hive kullanarak nasıl analiz edeceğinizi öğrenin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: 1c519533625835677ddae0a274c9ce9f10edc6dd
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73097993"
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>Azure HDInsight 'ta Apache Hive kullanarak JSON belgelerini işleme ve çözümleme

Azure HDInsight 'ta Apache Hive kullanarak JavaScript Nesne Gösterimi (JSON) dosyalarını nasıl işleyeceğini ve analiz edeceğinizi öğrenin. Bu makale aşağıdaki JSON belgesini kullanır:

```json
{
  "StudentId": "trgfg-5454-fdfdg-4346",
  "Grade": 7,
  "StudentDetails": [
    {
      "FirstName": "Peggy",
      "LastName": "Williams",
      "YearJoined": 2012
    }
  ],
  "StudentClassCollection": [
    {
      "ClassId": "89084343",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "High",
      "Score": 93,
      "PerformedActivity": false
    },
    {
      "ClassId": "78547522",
      "ClassParticipation": "NotSatisfied",
      "ClassParticipationRank": "None",
      "Score": 74,
      "PerformedActivity": false
    },
    {
      "ClassId": "78675563",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "Low",
      "Score": 83,
      "PerformedActivity": true
    }
  ]
}
```

Dosya `wasb://processjson@hditutorialdata.blob.core.windows.net/`adresinde bulunabilir. HDInsight ile Azure Blob depolama 'yı kullanma hakkında daha fazla bilgi için bkz. [HDInsight 'ta Apache Hadoop ile uyumlu Azure Blob depolamayı kullanma](../hdinsight-hadoop-use-blob-storage.md). Dosyayı kümenizin varsayılan kapsayıcısına kopyalayabilirsiniz.

Bu makalede Apache Hive konsolunu kullanırsınız. Hive konsolunun nasıl açılacağı hakkında yönergeler için bkz. [HDInsight 'ta Apache Hadoop Ile Apache ambarı Hive görünümünü kullanma](apache-hadoop-use-hive-ambari-view.md).

## <a name="flatten-json-documents"></a>JSON belgelerini Düzleştir

Sonraki bölümde listelenen yöntemler, JSON belgesinin tek bir satırdan meydana olmasını gerektirir. Bu nedenle, JSON belgesini bir dizeye düzleştirmeniz gerekir. JSON belgeniz zaten düzleştirildiğinde, bu adımı atlayabilir ve JSON verilerini analiz etmek için doğrudan bir sonraki bölüme gidebilirsiniz. JSON belgesini düzleştirmek için aşağıdaki betiği çalıştırın:

```sql
DROP TABLE IF EXISTS StudentsRaw;
CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

DROP TABLE IF EXISTS StudentsOneLine;
CREATE EXTERNAL TABLE StudentsOneLine
(
  json_body string
)
STORED AS TEXTFILE LOCATION '/json/students';

INSERT OVERWRITE TABLE StudentsOneLine
SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
      FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
      GROUP BY INPUT__FILE__NAME;

SELECT * FROM StudentsOneLine
```

Ham JSON dosyası `wasb://processjson@hditutorialdata.blob.core.windows.net/`konumunda bulunur. **Studentsraw** Hive tablosu düzleştirilmemiş ham JSON belgesine işaret eder.

**Studentsoneline** Hive tablosu, verileri **/JSON/Students/** Path altındaki HDInsight varsayılan dosya sisteminde depolar.

**Insert** Ifadesinde, **Studentoneline** tablosu düzleştirilmiş JSON verileriyle doldurulur.

**Select** deyimleri yalnızca bir satır döndürür.

**Select** ifadesinin çıktısı aşağıda verilmiştir:

![HDInsight JSON belgesini düzleştirme](./media/using-json-in-hive/hdinsight-flatten-json.png)

## <a name="analyze-json-documents-in-hive"></a>Hive 'de JSON belgelerini çözümleme

Hive, JSON belgelerindeki sorguları çalıştırmak için üç farklı mekanizma sağlar veya kendi kendinize yazabilirsiniz:

* Get_json_object Kullanıcı tanımlı işlevini (UDF) kullanın.
* Json_tuple UDF kullanın.
* Özel seri hale getirici/deserializer (SerDe) kullanın.
* Python veya diğer diller kullanarak kendi UDF 'nizi yazın. Hive ile kendi Python kodunuzu çalıştırma hakkında daha fazla bilgi için, bkz. [Apache Hive ve Apache Pig Ile Python UDF](./python-udf-hdinsight.md).

### <a name="use-the-get_json_object-udf"></a>Get_json_object UDF kullanın

Hive, çalışma zamanı sırasında JSON sorgulaması gerçekleştirebilen [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) adlı YERLEŞIK bir UDF sağlar. Bu yöntem iki bağımsız değişken alır--düzleştirilmiş JSON belgesi ve ayrıştırılabilmesi gereken JSON alanı içeren tablo adı ve Yöntem adı. Bu UDF 'nin nasıl çalıştığını görmek için bir örneğe bakalım.

Aşağıdaki sorgu, her öğrenci için adı ve soyadı döndürür:

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

Bu sorguyu konsol penceresinde çalıştırdığınızda çıkış şu şekildedir:

![JSON nesnesi UDF Apache Hive al](./media/using-json-in-hive/hdinsight-get-json-object.png)

Get_json_object UDF 'nin sınırlamaları vardır:

* Sorgudaki her alan sorgunun yeniden ayrıştırılmasını gerektirdiğinden performansı etkiler.
* **GET\_JSON_OBJECT ()** bir dizinin dize gösterimini döndürür. Bu diziyi bir Hive dizisine dönüştürmek için, "[" ve "]" köşeli ayraçlarını değiştirmek için normal ifadeler kullanmanız ve sonra diziyi almak için bölme çağrısı yapmanız gerekir.

Hive wiki, **json_tuple**kullanmanızı öneriyor.  

### <a name="use-the-json_tuple-udf"></a>Json_tuple UDF kullanın

Hive tarafından sunulan başka bir UDF, [get_ JSON _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object)öğesinden daha iyi sonuç veren [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple)olarak adlandırılır. Bu yöntem bir anahtarlar kümesi ve bir JSON dizesi alır ve bir işlev kullanarak bir değer grubu döndürür. Aşağıdaki sorgu, JSON belgesinden öğrenci KIMLIĞINI ve sınıfı döndürür:

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

Hive konsolundaki bu betiğin çıkışı:

![Apache Hive JSON sorgu sonuçları](./media/using-json-in-hive/hdinsight-json-tuple.png)

Json_tuple UDF, Hive içindeki [yan yana görünüm](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) sözdizimini kullanır. Bu, JSON\_kayıt düzeninin, özgün tablodaki her satıra udt işlevini uygulayarak bir sanal tablo oluşturmasına olanak sağlar. Karmaşık jler, **yan yana görünümün**yinelenen kullanımı nedeniyle çok zor hale gelir. Ayrıca, **JSON_TUPLE** iç Içe geçmiş jler 'i işleyemez.

### <a name="use-a-custom-serde"></a>Özel bir SerDe kullanma

SerDe, iç içe geçmiş JSON belgelerini ayrıştırmak için en iyi seçenektir. JSON şemasını tanımlamanıza olanak tanır ve ardından belgeyi ayrıştırmak için şemayı kullanabilirsiniz. Yönergeler için bkz. [Microsoft Azure HDInsight ile özel bır JSON SerDe kullanımı](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Özet

Sonuç olarak, seçtiğiniz Hive içindeki JSON işlecinin türü senaryonuza bağlıdır. Basit bir JSON belgeniz varsa ve aranacak yalnızca bir alanınız varsa Hive UDF **get_json_object**kullanmayı seçebilirsiniz. Arama yapmak için birden fazla anahtarınız varsa, **json_tuple**kullanabilirsiniz. İç içe geçmiş bir belgeniz varsa **JSON SerDe**' nı kullanmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

İlgili makaleler için bkz.:

* [Örnek Apache Log4J dosyasını çözümlemek için HDInsight 'ta Apache Hadoop Apache Hive ve HiveQL kullanın](../hdinsight-use-hive.md)
* [HDInsight 'ta etkileşimli sorgu kullanarak Uçuş gecikmesi verilerini çözümleme](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
* [HDInsight 'ta Apache Hive kullanarak Twitter verilerini çözümleme](../hdinsight-analyze-twitter-data-linux.md)
