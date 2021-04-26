---
title: Azure Data Lake için U-SQL programlamasına yönelik yönergeler
description: İyi USQL betiği oluşturmanıza olanak tanımak için Azure Data Lake Analytics 'de U-SQL genel bakış ve UDF programlamasına ilişkin bilgi edinin.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: d263f2616607a96a8aa14f1ad1c06330b1b44644
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96510871"
---
# <a name="u-sql-programmability-guide-overview"></a>U-SQL programlama kılavuzuna genel bakış

U-SQL, büyük veri iş yükleri türü için tasarlanan bir sorgu dilidir. U-SQL ' n i n benzersiz özelliklerinden biri, C# tarafından sunulan genişletilebilirlik ve programlama ile SQL benzeri bildirime dayalı dilin birleşimidir. Bu kılavuzda, C# tarafından etkinleştirilen U-SQL dilinin genişletilebilirlik ve programlamasına odaklanıyoruz.

## <a name="requirements"></a>Gereksinimler

[Visual Studio için Azure Data Lake araçları](https://www.microsoft.com/download/details.aspx?id=49504)indirin ve yükleyin.

## <a name="get-started-with-u-sql"></a>U-SQL kullanmaya başlama  

Aşağıdaki U-SQL betiğine bakın:

```usql
@a  = 
  SELECT * FROM 
    (VALUES
       ("Contoso",   1500.0, "2017-03-39"),
       ("Woodgrove", 2700.0, "2017-04-10")
    ) AS D( customer, amount, date );

@results =
  SELECT
    customer,
    amount,
    date
  FROM @a;    
```

Bu betik iki satır kümesi tanımlar: `@a` ve `@results` . Satır kümesi `@results` öğesinden tanımlandı `@a` .

## <a name="c-types-and-expressions-in-u-sql-script"></a>U-SQL betikteki C# türleri ve ifadeleri

U-SQL Ifadesi,, ve gibi U-SQL mantıksal işlemleriyle birleştirilmiş bir C# deyimidir `AND` `OR` `NOT` . U-SQL Ifadeleri SELECT, EXTRACT, WHERE, HAVING, GROUP BY ve DECLARE ile kullanılabilir. Örneğin, aşağıdaki betik bir dizeyi bir tarih saat değeri olarak ayrıştırır.

```usql
@results =
  SELECT
    customer,
    amount,
    DateTime.Parse(date) AS date
  FROM @a;    
```

Aşağıdaki kod parçacığı bir dizeyi DECLARE ifadesinde DateTime değeri olarak ayrıştırır.

```usql
DECLARE @d = DateTime.Parse("2016/01/01");
```

### <a name="use-c-expressions-for-data-type-conversions"></a>Veri türü dönüştürmeleri için C# ifadeleri kullanma

Aşağıdaki örnek, C# ifadelerini kullanarak bir tarih saat veri dönüştürmeyi nasıl yapabileceğinizi göstermektedir. Bu senaryoda, dize datetime verileri, gece 00:00:00 saat gösterimi ile standart tarih saat biçimine dönüştürülür.

```usql
DECLARE @dt = "2016-07-06 10:23:15";

@rs1 =
  SELECT 
    Convert.ToDateTime(Convert.ToDateTime(@dt).ToString("yyyy-MM-dd")) AS dt,
    dt AS olddt
  FROM @rs0;

OUTPUT @rs1 
  TO @output_file 
  USING Outputters.Text();
```

### <a name="use-c-expressions-for-todays-date"></a>Bugünün tarihi için C# ifadelerini kullanın

Bugünün tarihini çekmek için aşağıdaki C# ifadesini kullanabiliriz: `DateTime.Now.ToString("M/d/yyyy")`

Bu ifadenin bir betikte nasıl kullanılacağına ilişkin bir örnek aşağıda verilmiştir:

```usql
@rs1 =
  SELECT
    MAX(guid) AS start_id,
    MIN(dt) AS start_time,
    MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
    MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
    DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
    user,
    des
  FROM @rs0
  GROUP BY user, des;
```
## <a name="using-net-assemblies"></a>.NET derlemelerini kullanma

U-SQL ' i n genişletilebilirlik modeli, .NET derlemelerinden özel kod ekleme imkanını kullanır. 

### <a name="register-a-net-assembly"></a>.NET derlemesini kaydetme

Bir `CREATE ASSEMBLY` .NET derlemesini U-SQL veritabanına yerleştirmek için ifadesini kullanın. Daha sonra, U-SQL betikleri bu derlemeleri, ifadesini kullanarak kullanabilir `REFERENCE ASSEMBLY` . 

Aşağıdaki kod, bir derlemenin nasıl kaydedileceği gösterilmektedir:

```usql
CREATE ASSEMBLY MyDB.[MyAssembly]
   FROM "/myassembly.dll";
```

Aşağıdaki kod, bir derlemeye nasıl başvurululacağını gösterir:

```usql
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

Bu konuyu daha ayrıntılı olarak ele alan [derleme kayıt yönergelerine](/archive/blogs/azuredatalake/how-to-register-u-sql-assemblies-in-your-u-sql-catalog) başvurun.


### <a name="use-assembly-versioning"></a>Derleme sürümü oluşturma kullan
Şu anda U-SQL, .NET Framework sürümünü kullanır 4.7.2. Bu nedenle, kendi derlemelerinizin çalışma zamanının bu sürümü ile uyumlu olduğundan emin olun.

Daha önce belirtildiği gibi, U-SQL kodu 64 bit (x64) biçiminde çalıştırır. Bu nedenle, kodunuzun x64 üzerinde çalışacak şekilde derlendiğinden emin olun. Aksi takdirde, daha önce gösterilen yanlış biçim hatasını alırsınız.

Karşıya yüklenen her derleme DLL 'SI ve farklı bir çalışma zamanı, yerel bir derleme veya bir yapılandırma dosyası gibi kaynak dosyası en fazla 400 MB olabilir. Dağıtılan kaynakların toplam boyutu, kaynak DAĞıTMA yoluyla veya derlemelere ve bunların ek dosyalarına başvurular aracılığıyla 3 GB 'ı aşamaz.

Son olarak, her U-SQL veritabanının belirli bir derlemenin yalnızca bir sürümünü içerebileceğini unutmayın. Örneğin, NewtonSoft Json.NET kitaplığının hem sürüm 7 hem de sürüm 8 ' e ihtiyacınız varsa, bunları iki farklı veritabanına kaydetmeniz gerekir. Ayrıca, her bir betik belirli bir derleme DLL 'inin yalnızca bir sürümüne başvurabilir. Bu bakımdan, U-SQL C# derleme yönetimi ve sürüm oluşturma semantiğini izler.

## <a name="use-user-defined-functions-udf"></a>Kullanıcı tanımlı işlevleri kullan: UDF
U-SQL Kullanıcı tanımlı işlevleri veya UDF, parametreleri kabul eden, bir eylem gerçekleştiren (karmaşık bir hesaplama gibi) ve bu eylemin sonucunu bir değer olarak döndüren programlama yordamlardır. UDF 'nin dönüş değeri yalnızca tek bir skaler olabilir. U-SQL UDF, diğer C# skalar işlevleri gibi U-SQL temel komut dosyasında çağrılabilir.

U-SQL Kullanıcı tanımlı işlevlerini **ortak** ve **statik** olarak başlatmalarını öneririz.

```usql
public static string MyFunction(string param1)
{
    return "my result";
}
```

İlk olarak, UDF oluşturmaya yönelik basit bir örneğe bakalım.

Bu kullanım örneği senaryosunda, belirli bir kullanıcı için ilk oturum açma 'nın mali çeyreği ve mali ayı dahil olmak üzere mali dönemi belirlememiz gerekir. Senaryolarımızda yılın ilk mali ayı Haziran ' dır.

Mali dönemi hesaplamak için aşağıdaki C# işlevini sunuyoruz:

```usql
public static string GetFiscalPeriod(DateTime dt)
{
    int FiscalMonth=0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter=0;
    if (FiscalMonth >=1 && FiscalMonth<=3)
    {
    FiscalQuarter = 1;
    }
    if (FiscalMonth >= 4 && FiscalMonth <= 6)
    {
    FiscalQuarter = 2;
    }
    if (FiscalMonth >= 7 && FiscalMonth <= 9)
    {
    FiscalQuarter = 3;
    }
    if (FiscalMonth >= 10 && FiscalMonth <= 12)
    {
    FiscalQuarter = 4;
    }

    return "Q" + FiscalQuarter.ToString() + ":P" + FiscalMonth.ToString();
}
```

Yalnızca mali ayı ve çeyreği hesaplar ve bir dize değeri döndürür. Haziran için ilk mali çeyreğin ilk ayı olarak "Q1: P1" kullanırız. Temmuz için "Q1: P2", vb. kullanıyoruz.

Bu, U-SQL projemizdeki kullanabilmemiz için normal bir C# işlevidir.

Arka plan kod bölümünün Bu senaryoda nasıl göründüğünü aşağıda bulabilirsiniz:

```usql
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        public static string GetFiscalPeriod(DateTime dt)
        {
            int FiscalMonth=0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter=0;
            if (FiscalMonth >=1 && FiscalMonth<=3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return "Q" + FiscalQuarter.ToString() + ":" + FiscalMonth.ToString();
        }
    }
}
```

Şimdi bu işlevi temel U-SQL betiğiyle çağıracağız. Bunu yapmak için, bu örnekte ad alanı. Class. Function (parametre) olan ad alanı da dahil olmak üzere, işlev için tam nitelikli bir ad sağlamamız gerekir.
```usql
USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

Aşağıda gerçek U-SQL taban betiği verilmiştir:
```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt DateTime,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");

@rs1 =
    SELECT
        MAX(guid) AS start_id,
    MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

Komut dosyası yürütmenin çıkış dosyası aşağıda verilmiştir:

```output
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

Bu örnek, U-SQL ' de satır içi UDF 'nin basit bir kullanımını gösterir.

### <a name="keep-state-between-udf-invocations"></a>UDF çağırmaları arasında durumu koru
U-SQL C# programlama nesneleri daha karmaşık olabilir ve arka plan kod genel değişkenleri aracılığıyla etkileşimten faydalenebilir. Aşağıdaki iş kullanım örneği senaryosuna bakalım.

Büyük kuruluşlarda, kullanıcılar iç uygulamaların değişen özellikleri arasında geçiş yapabilir. Bunlar, Microsoft Dynamics CRM, Power BI vb. içerebilir. Müşteriler, kullanıcıların farklı uygulamalar arasında nasıl geçiş yaptığını, kullanım eğilimlerinin ne olduğunu vb. bir telemetri Analizi uygulamak isteyebilir. İş için amaç, uygulama kullanımını iyileştirmektir. Ayrıca, farklı uygulamaları veya belirli oturum açma yordamlarını birleştirmek isteyebilirsiniz.

Bu hedefe ulaşmak için, gerçekleşen son oturum arasındaki oturum kimliklerini ve gecikme süresini belirlememiz gerekir.

Önceki bir oturum açma bulduk ve bu oturum açma 'yı aynı uygulamaya oluşturulmakta olan tüm oturumlara atamanız gerekir. İlk zorluk, U-SQL taban betiğinizin, zaten GECIKME işlevli, önceden hesaplanmış sütunlar üzerinden hesaplamalar uygulamamıza izin vermediği bir sorundur. İkinci zorluk, belirli bir oturumu aynı süre içindeki tüm oturumlar için tutmanız gerekir.

Bu sorunu çözmek için, arka plan kod bölümünün içinde genel bir değişken kullanıyoruz: `static public string globalSession;` .

Bu genel değişken, komut dosyası yürütme sırasında tüm satır kümesine uygulanır.

U-SQL programımızın arka plan kod bölümü aşağıda verilmiştir:

```csharp
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQLApplication21
{
    public class UserSession
    {
        static public string globalSession;
        static public string StampUserSession(string eventTime, string PreviousRow, string Session)
        {

            if (!string.IsNullOrEmpty(PreviousRow))
            {
                double timeGap = Convert.ToDateTime(eventTime).Subtract(Convert.ToDateTime(PreviousRow)).TotalMinutes;
                if (timeGap <= 60) {return Session;}
                else {return Guid.NewGuid().ToString();}
            }
            else {return Guid.NewGuid().ToString();}

        }

        static public string getStampUserSession(string Session)
        {
            if (Session != globalSession && !string.IsNullOrEmpty(Session)) { globalSession = Session; }
            return globalSession;
        }

    }
}
```

Bu örnek, `static public string globalSession;` işlev içinde kullanılan genel değişkeni gösterir `getStampUserSession` ve oturum parametresi her değiştirildiğinde yeniden başlatılır.

U-SQL taban betiği aşağıdaki gibidir:

```usql
DECLARE @in string = @"\UserSession\test1.tsv";
DECLARE @out1 string = @"\UserSession\Out1.csv";
DECLARE @out2 string = @"\UserSession\Out2.csv";
DECLARE @out3 string = @"\UserSession\Out3.csv";

@records =
    EXTRACT DataId string,
            EventDateTime string,           
            UserName string,
            UserSessionTimestamp string

    FROM @in
    USING Extractors.Tsv();

@rs1 =
    SELECT 
        EventDateTime,
        UserName,
    LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,          
        string.IsNullOrEmpty(LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,           
        USQLApplication21.UserSession.StampUserSession
           (
            EventDateTime,
            LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC),
            LAG(UserSessionTimestamp, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)
           ) AS UserSessionTimestamp
    FROM @records;

@rs2 =
    SELECT 
        EventDateTime,
        UserName,
        LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,
        string.IsNullOrEmpty( LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,
        USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp) AS UserSessionTimestamp
    FROM @rs1
    WHERE UserName != "UserName";

OUTPUT @rs2
    TO @out2
    ORDER BY UserName, EventDateTime ASC
    USING Outputters.Csv();
```

İşlevi `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` ikinci bellek satır kümesi hesaplaması sırasında burada çağırılır. `UserSessionTimestamp`Sütunu geçirir ve değiştirilene kadar değeri döndürür `UserSessionTimestamp` .

Çıkış dosyası aşağıdaki gibidir:

```output
"2016-02-19T07:32:36.8420000-08:00","User1",,True,"72a0660e-22df-428e-b672-e0977007177f"
"2016-02-17T11:52:43.6350000-08:00","User2",,True,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-17T11:59:08.8320000-08:00","User2","2016-02-17T11:52:43.6350000-08:00",False,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-11T07:04:17.2630000-08:00","User3",,True,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-11T07:10:33.9720000-08:00","User3","2016-02-11T07:04:17.2630000-08:00",False,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-15T21:27:41.8210000-08:00","User3","2016-02-11T07:10:33.9720000-08:00",False,"4d2bc48d-bdf3-4591-a9c1-7b15ceb8e074"
"2016-02-16T05:48:49.6360000-08:00","User3","2016-02-15T21:27:41.8210000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-16T06:22:43.6390000-08:00","User3","2016-02-16T05:48:49.6360000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-17T16:29:53.2280000-08:00","User3","2016-02-16T06:22:43.6390000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T16:39:07.2430000-08:00","User3","2016-02-17T16:29:53.2280000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T17:20:39.3220000-08:00","User3","2016-02-17T16:39:07.2430000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-19T05:23:54.5710000-08:00","User3","2016-02-17T17:20:39.3220000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T05:48:37.7510000-08:00","User3","2016-02-19T05:23:54.5710000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T06:40:27.4830000-08:00","User3","2016-02-19T05:48:37.7510000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T07:27:37.7550000-08:00","User3","2016-02-19T06:40:27.4830000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T19:35:40.9450000-08:00","User3","2016-02-19T07:27:37.7550000-08:00",False,"3f385f0b-3e68-4456-ac74-ff6cef093674"
"2016-02-20T00:07:37.8250000-08:00","User3","2016-02-19T19:35:40.9450000-08:00",False,"685f76d5-ca48-4c58-b77d-bd3a9ddb33da"
"2016-02-11T09:01:33.9720000-08:00","User4",,True,"9f0cf696-c8ba-449a-8d5f-1ca6ed8f2ee8"
"2016-02-17T06:30:38.6210000-08:00","User4","2016-02-11T09:01:33.9720000-08:00",False,"8b11fd2a-01bf-4a5e-a9af-3c92c4e4382a"
"2016-02-17T22:15:26.4020000-08:00","User4","2016-02-17T06:30:38.6210000-08:00",False,"4e1cb707-3b5f-49c1-90c7-9b33b86ca1f4"
"2016-02-18T14:37:27.6560000-08:00","User4","2016-02-17T22:15:26.4020000-08:00",False,"f4e44400-e837-40ed-8dfd-2ea264d4e338"
"2016-02-19T01:20:31.4800000-08:00","User4","2016-02-18T14:37:27.6560000-08:00",False,"2136f4cf-7c7d-43c1-8ae2-08f4ad6a6e08"
```

Bu örnekte, tüm bellek satır kümesine uygulanan bir arka plan kod bölümü içinde genel bir değişken kullandığımız daha karmaşık bir kullanım örneği senaryosu gösterilmektedir.

## <a name="next-steps"></a>Sonraki adımlar
* [U-SQL programlama kılavuzu-UDT ve UDADGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)
* [U-SQL programlama kılavuzu-UDO](data-lake-analytics-u-sql-programmability-guide-UDO.md)