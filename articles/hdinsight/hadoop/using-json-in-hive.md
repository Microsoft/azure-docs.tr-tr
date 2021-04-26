---
title: Apache Hive ile Azure HDInsight ile & işlem JSON 'ı çözümleme
description: JSON belgelerini nasıl kullanacağınızı ve Azure HDInsight 'ta Apache Hive kullanarak nasıl analiz edeceğinizi öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: d3c5077450dd4ec59f5ea7bc8f37879f9aa775bf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104868945"
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

Dosya şurada bulunabilir: `wasb://processjson@hditutorialdata.blob.core.windows.net/` . HDInsight ile Azure Blob depolama 'yı kullanma hakkında daha fazla bilgi için bkz. [HDInsight 'ta Apache Hadoop ile uyumlu Azure Blob depolamayı kullanma](../hdinsight-hadoop-use-blob-storage.md). Dosyayı kümenizin varsayılan kapsayıcısına kopyalayabilirsiniz.

Bu makalede Apache Hive konsolunu kullanırsınız. Hive konsolunun nasıl açılacağı hakkında yönergeler için bkz. [HDInsight 'ta Apache Hadoop Ile Apache ambarı Hive görünümünü kullanma](apache-hadoop-use-hive-ambari-view.md).

> [!NOTE]  
> Hive görünümü artık HDInsight 4,0 ' de kullanılamaz.

## <a name="flatten-json-documents"></a>JSON belgelerini Düzleştir

Sonraki bölümde listelenen yöntemler, JSON belgesinin tek bir satırdan oluşması gerekir. Bu nedenle, JSON belgesini bir dizeye düzleştirmeniz gerekir. JSON belgeniz zaten düzleştirildiğinde, bu adımı atlayabilir ve JSON verilerini analiz etmek için doğrudan bir sonraki bölüme gidebilirsiniz. JSON belgesini düzleştirmek için aşağıdaki betiği çalıştırın:

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

Ham JSON dosyası konumunda bulunur `wasb://processjson@hditutorialdata.blob.core.windows.net/` . **Studentsraw** Hive tablosu düzleştirilmemiş ham JSON belgesine işaret eder.

**Studentsoneline** Hive tablosu, verileri **/JSON/Students/** Path altındaki HDInsight varsayılan dosya sisteminde depolar.

**Insert** Ifadesinde, **Studentoneline** tablosu düzleştirilmiş JSON verileriyle doldurulur.

**Select** deyimleri yalnızca bir satır döndürür.

**Select** ifadesinin çıktısı aşağıda verilmiştir:

:::image type="content" source="./media/using-json-in-hive/hdinsight-flatten-json.png" alt-text="HDInsight JSON belgesini düzleştirme" border="true":::

## <a name="analyze-json-documents-in-hive"></a>Hive 'de JSON belgelerini çözümleme

Hive, JSON belgelerindeki sorguları çalıştırmak için üç farklı mekanizma sağlar veya kendi kendinize yazabilirsiniz:

* Kullanıcı tanımlı get_json_object işlevini (UDF) kullanın.
* Json_tuple UDF kullanın.
* Özel seri hale getirici/deserializer (SerDe) kullanın.
* Python veya diğer diller kullanarak kendi UDF 'nizi yazın. Hive ile kendi Python kodunuzu çalıştırma hakkında daha fazla bilgi için, bkz. [Apache Hive ve Apache Pig Ile Python UDF](./python-udf-hdinsight.md).

### <a name="use-the-get_json_object-udf"></a>Get_json_object UDF kullanın

Hive, çalışma zamanı sırasında JSON 'yi sorgulayan [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) ADLı yerleşik UDF sağlar. Bu yöntem iki bağımsız değişkeni alır: tablo adı ve Yöntem adı. Yöntem adı, düzleştirilmiş JSON belgesine ve ayrıştırılabilmesi gereken JSON alanına sahiptir. Bu UDF 'nin nasıl çalıştığını görmek için bir örneğe bakalım.

Aşağıdaki sorgu, her öğrenci için adı ve soyadı döndürür:

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

Bu sorguyu konsol penceresinde çalıştırdığınızda çıkış şu şekildedir:

:::image type="content" source="./media/using-json-in-hive/hdinsight-get-json-object.png" alt-text="Apache Hive JSON nesnesi UDF alır" border="true":::

Get_json_object UDF kısıtlamaları vardır:

* Sorgudaki her alan sorgunun yeniden ayrıştırılmasını gerektirdiğinden performansı etkiler.
* **Al \_ JSON_OBJECT ()** , bir dizinin dize gösterimini döndürür. Bu diziyi bir Hive dizisine dönüştürmek için, "[" ve "]" köşeli ayraçlarını değiştirmek için normal ifadeler kullanmanız ve sonra diziyi almak için bölme çağrısı yapmanız gerekir.

Bu dönüştürme, Hive wiki 'nin **json_tuple** kullanmanızı öneriyor.  

### <a name="use-the-json_tuple-udf"></a>Json_tuple UDF kullanın

Hive tarafından sunulan başka bir UDF [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple)denir ve bu [get_ JSON _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object)daha iyi hale gelir. Bu yöntem bir anahtarlar kümesi ve bir JSON dizesi alır. Sonra bir değer grubu döndürür. Aşağıdaki sorgu, JSON belgesinden öğrenci KIMLIĞINI ve sınıfı döndürür:

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

Hive konsolundaki bu betiğin çıkışı:

:::image type="content" source="./media/using-json-in-hive/hdinsight-json-tuple.png" alt-text="Apache Hive JSON sorgu sonuçları" border="true":::

`json_tuple`UDF, Hive içindeki [yan yana görünüm](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) sözdizimini kullanır. Bu, JSON \_ kayıt düzeninin, özgün tablodaki her satıra udt işlevini uygulayarak bir sanal tablo oluşturmasını sağlar. Karmaşık jler, **yan yana görünümün** yinelenen kullanımı nedeniyle çok zor hale gelir. Ayrıca, **JSON_TUPLE** iç Içe geçmiş jdönemleri işleyemez.

### <a name="use-a-custom-serde"></a>Özel bir SerDe kullanma

SerDe, iç içe geçmiş JSON belgelerini ayrıştırmak için en iyi seçenektir. JSON şemasını tanımlamanıza olanak tanır ve ardından belgeyi ayrıştırmak için şemayı kullanabilirsiniz. Yönergeler için bkz. [Microsoft Azure HDInsight ile özel bır JSON SerDe kullanımı](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Özet

Seçtiğiniz Hive içindeki JSON işlecinin türü senaryonuza bağlıdır. Basit bir JSON belgesi ve aranacak bir alan ile, Hive UDF **get_json_object** seçin. Arama yapmak için birden fazla anahtarınız varsa **json_tuple** kullanabilirsiniz. İç içe geçmiş belgeler için **JSON SerDe** kullanın.

## <a name="next-steps"></a>Sonraki adımlar

İlgili makaleler için bkz.:

* [Örnek Apache Log4J dosyasını çözümlemek için HDInsight 'ta Apache Hadoop Apache Hive ve HiveQL kullanın](./hdinsight-use-hive.md)
* [HDInsight 'ta etkileşimli sorgu kullanarak Uçuş gecikmesi verilerini çözümleme](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)