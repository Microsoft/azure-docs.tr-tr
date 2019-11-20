---
title: Azure Data Lake için U-SQL programlamasına yönelik yönergeler
description: Azure Data Lake Analytics ' de bulut tabanlı büyük veri platformu oluşturmanızı sağlayan hizmet kümesi hakkında bilgi edinin.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 63be271e-7c44-4d19-9897-c2913ee9599d
ms.topic: conceptual
ms.date: 06/30/2017
ms.openlocfilehash: dc55615d7a5c6ae9a393ed4fd5f49cd92aedc0f9
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162586"
---
# <a name="u-sql-programmability-guide"></a>U-SQL programlama kılavuzu

U-SQL, büyük veri iş yükleri türü için tasarlanan bir sorgu dilidir. U-SQL ' n i n benzersiz özelliklerinden biri, SQL benzeri bildirim dilinin tarafından C#sağlanmış olan genişletilebilirlik ve programlamasına sahip bir birleşimidir. Bu kılavuzda, tarafından C#etkinleştirilen U-SQL dilinin genişletilebilirlik ve programlamasına odaklanıyoruz.

## <a name="requirements"></a>Gereksinimler

[Visual Studio için Azure Data Lake araçları](https://www.microsoft.com/download/details.aspx?id=49504)indirin ve yükleyin.

## <a name="get-started-with-u-sql"></a>U-SQL kullanmaya başlama  

Aşağıdaki U-SQL betiğine bakın:

```
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

Bu betik iki satır kümesi tanımlar: `@a` ve `@results`. Satır kümesi `@results` `@a`tanımlı.

## <a name="c-types-and-expressions-in-u-sql-script"></a>C#U-SQL betikteki türler ve ifadeler

U-SQL Ifadesi, `AND`, C# `OR`ve `NOT`gibi U-SQL mantıksal işlemleriyle birleştirilmiş bir ifadedir. U-SQL Ifadeleri SELECT, EXTRACT, WHERE, HAVING, GROUP BY ve DECLARE ile kullanılabilir. Örneğin, aşağıdaki betik bir dizeyi bir tarih saat değeri olarak ayrıştırır.

```
@results =
  SELECT
    customer,
    amount,
    DateTime.Parse(date) AS date
  FROM @a;    
```

Aşağıdaki kod parçacığı bir dizeyi DECLARE ifadesinde DateTime değeri olarak ayrıştırır.

```
DECLARE @d = DateTime.Parse("2016/01/01");
```

### <a name="use-c-expressions-for-data-type-conversions"></a>Veri C# türü dönüştürmeleri için ifadeleri kullanma

Aşağıdaki örnek, ifadeleri kullanarak C# bir tarih saat veri dönüştürmeyi nasıl yapabileceğinizi göstermektedir. Bu senaryoda, dize datetime verileri, gece 00:00:00 saat gösterimi ile standart tarih saat biçimine dönüştürülür.

```
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

### <a name="use-c-expressions-for-todays-date"></a>Bugünün C# tarihi için ifadeleri kullanın

Bugünün tarihini çekmek için şu C# ifadeyi kullanabiliriz: `DateTime.Now.ToString("M/d/yyyy")`

Bu ifadenin bir betikte nasıl kullanılacağına ilişkin bir örnek aşağıda verilmiştir:

```
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

Bir .NET derlemesini U-SQL veritabanına yerleştirmek için `CREATE ASSEMBLY` ifadesini kullanın. Daha sonra, U-SQL betikleri `REFERENCE ASSEMBLY` ifadesini kullanarak bu derlemeleri kullanabilir. 

Aşağıdaki kod, bir derlemenin nasıl kaydedileceği gösterilmektedir:

```
CREATE ASSEMBLY MyDB.[MyAssembly]
   FROM "/myassembly.dll";
```

Aşağıdaki kod, bir derlemeye nasıl başvurululacağını gösterir:

```
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

Bu konuyu daha ayrıntılı olarak ele alan [derleme kayıt yönergelerine](https://blogs.msdn.microsoft.com/azuredatalake/2016/08/26/how-to-register-u-sql-assemblies-in-your-u-sql-catalog/) başvurun.


### <a name="use-assembly-versioning"></a>Derleme sürümü oluşturma kullan
Şu anda U-SQL .NET Framework 4,5 sürümünü kullanıyor. Bu nedenle, kendi derlemelerinizin çalışma zamanının bu sürümü ile uyumlu olduğundan emin olun.

Daha önce belirtildiği gibi, U-SQL kodu 64 bit (x64) biçiminde çalıştırır. Bu nedenle, kodunuzun x64 üzerinde çalışacak şekilde derlendiğinden emin olun. Aksi takdirde, daha önce gösterilen yanlış biçim hatasını alırsınız.

Karşıya yüklenen her derleme DLL 'SI ve farklı bir çalışma zamanı, yerel bir derleme veya bir yapılandırma dosyası gibi kaynak dosyası en fazla 400 MB olabilir. Dağıtılan kaynakların toplam boyutu, kaynak DAĞıTMA yoluyla veya derlemelere ve bunların ek dosyalarına başvurular aracılığıyla 3 GB 'ı aşamaz.

Son olarak, her U-SQL veritabanının belirli bir derlemenin yalnızca bir sürümünü içerebileceğini unutmayın. Örneğin, NewtonSoft Json.NET kitaplığının hem sürüm 7 hem de sürüm 8 ' e ihtiyacınız varsa, bunları iki farklı veritabanına kaydetmeniz gerekir. Ayrıca, her bir betik belirli bir derleme DLL 'inin yalnızca bir sürümüne başvurabilir. Bu bakımdan, U-SQL C# derleme yönetimi ve sürüm oluşturma semantiğini izler.

## <a name="use-user-defined-functions-udf"></a>Kullanıcı tanımlı işlevleri kullan: UDF
U-SQL Kullanıcı tanımlı işlevleri veya UDF, parametreleri kabul eden, bir eylem gerçekleştiren (karmaşık bir hesaplama gibi) ve bu eylemin sonucunu bir değer olarak döndüren programlama yordamlardır. UDF 'nin dönüş değeri yalnızca tek bir skaler olabilir. U-SQL UDF, U-SQL temel komut dosyasında herhangi bir C# skalar işlev gibi çağrılabilir.

U-SQL Kullanıcı tanımlı işlevlerini **ortak** ve **statik**olarak başlatmalarını öneririz.

```
public static string MyFunction(string param1)
{
    return "my result";
}
```

İlk olarak, UDF oluşturmaya yönelik basit bir örneğe bakalım.

Bu kullanım örneği senaryosunda, belirli bir kullanıcı için ilk oturum açma 'nın mali çeyreği ve mali ayı dahil olmak üzere mali dönemi belirlememiz gerekir. Senaryolarımızda yılın ilk mali ayı Haziran ' dır.

Mali dönemi hesaplamak için aşağıdaki C# işlevi sunuyoruz:

```
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

Bu, U- C# SQL projemizdeki kullanabilmemiz için normal bir işlevdir.

Arka plan kod bölümünün Bu senaryoda nasıl göründüğünü aşağıda bulabilirsiniz:

```
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

```
USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

Aşağıda gerçek U-SQL taban betiği verilmiştir:

```
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

```
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

Bu örnek, U-SQL ' de satır içi UDF 'nin basit bir kullanımını gösterir.

### <a name="keep-state-between-udf-invocations"></a>UDF çağırmaları arasında durumu koru
U-SQL C# programlama nesneleri daha karmaşık olabilir ve arka plan kod genel değişkenleri aracılığıyla etkileşimten faydalanma sağlayabilir. Aşağıdaki iş kullanım örneği senaryosuna bakalım.

Büyük kuruluşlarda, kullanıcılar iç uygulamaların değişen özellikleri arasında geçiş yapabilir. Bunlar, Microsoft Dynamics CRM, PowerBI vb. içerebilir. Müşteriler, kullanıcıların farklı uygulamalar arasında nasıl geçiş yaptığını, kullanım eğilimlerinin ne olduğunu vb. bir telemetri Analizi uygulamak isteyebilir. İş için amaç, uygulama kullanımını iyileştirmektir. Ayrıca, farklı uygulamaları veya belirli oturum açma yordamlarını birleştirmek isteyebilirsiniz.

Bu hedefe ulaşmak için, gerçekleşen son oturum arasındaki oturum kimliklerini ve gecikme süresini belirlememiz gerekir.

Önceki bir oturum açma bulduk ve bu oturum açma 'yı aynı uygulamaya oluşturulmakta olan tüm oturumlara atamanız gerekir. İlk zorluk, U-SQL taban betiğinizin, zaten GECIKME işlevli, önceden hesaplanmış sütunlar üzerinden hesaplamalar uygulamamıza izin vermediği bir sorundur. İkinci zorluk, belirli bir oturumu aynı süre içindeki tüm oturumlar için tutmanız gerekir.

Bu sorunu çözmek için, arka plan kod bölümünün içinde genel bir değişken kullanıyoruz: `static public string globalSession;`.

Bu genel değişken, komut dosyası yürütme sırasında tüm satır kümesine uygulanır.

U-SQL programımızın arka plan kod bölümü aşağıda verilmiştir:

```
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

Bu örnek, `getStampUserSession` işlevi içinde kullanılan `static public string globalSession;` genel değişkeni gösterir ve oturum parametresi her değiştirildiğinde yeniden başlatılır.

U-SQL taban betiği aşağıdaki gibidir:

```
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

İşlev `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` ikinci bellek satır kümesi hesaplaması sırasında burada çağırılır. `UserSessionTimestamp` sütununu geçirir ve `UserSessionTimestamp` değiştirilene kadar değeri döndürür.

Çıkış dosyası aşağıdaki gibidir:

```
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

## <a name="use-user-defined-types-udt"></a>Kullanıcı tanımlı türleri kullan: UDT
Kullanıcı tanımlı türler veya UDT, U-SQL ' n i n diğer bir programlama özelliğidir. U-SQL UDT, Kullanıcı tanımlı normal C# bir tür gibi davranır. C#, yerleşik ve özel kullanıcı tanımlı türlerin kullanılmasına izin veren, türü kesin belirlenmiş bir dildir.

U-SQL, bir UDT satır kümelerinde köşeler arasında geçirildiğinde rastgele olarak seri hale getirilemez veya seri durumdan çıkarılamıyor. Bu, kullanıcının IFormatter arabirimini kullanarak bir açık biçimlendirici sağlaması gerektiği anlamına gelir. Bu, UDT için serileştirme ve seri hale getirme yöntemleriyle U-SQL sağlar.

> [!NOTE]
> U-SQL ' n i n yerleşik ayıklayıcıları ve outputşu anda, ıbiçimlendirici kümesi ile birlikte, UDT verilerini veya dosyalarından seri hale getirilemez veya seri durumdan çıkarılamıyor. Bu nedenle, bir dosyayı çıkış bildirimiyle bir dosyaya yazarken veya bir ayıklayıcısı ile okurken, bunu bir dize veya bayt dizisi olarak geçirmeniz gerekir. Daha sonra serileştirme ve seri durumdan çıkarma kodunu (yani, UDT 'nin ToString () yöntemi) açık olarak çağırabilirsiniz. Diğer yandan Kullanıcı tanımlı ayıklayıcıları ve outputler, UDTs okuyup yazabilir.

UDT 'yi, (önceki SEÇIM dışında) EXTRACTOR veya OUTPUT' i kullanmaya çalışırsam, burada gösterildiği gibi:

```
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

Aşağıdaki hatayı alıyorsunuz:

```
Error   1   E_CSC_USER_INVALIDTYPEINOUTPUTTER: Outputters.Text was used to output column myfield of type
MyNameSpace.Myfunction_Returning_UDT.

Description:

Outputters.Text only supports built-in types.

Resolution:

Implement a custom outputter that knows how to serialize this type, or call a serialization method on the type in
the preceding SELECT.   C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\
USQL-Programmability\Types.usql 52  1   USQL-Programmability
```

Outputter 'da UDT ile çalışmak için, bunu ToString () yöntemiyle String 'e serileştirmemiz veya özel bir outputter oluşturmanız gerekir.

UDTs Şu anda GROUP BY içinde kullanılamaz. UDT GROUP BY içinde kullanılıyorsa aşağıdaki hata oluşur:

```
Error   1   E_CSC_USER_INVALIDTYPEINCLAUSE: GROUP BY doesn't support type MyNameSpace.Myfunction_Returning_UDT
for column myfield

Description:

GROUP BY doesn't support UDT or Complex types.

Resolution:

Add a SELECT statement where you can project a scalar column that you want to use with GROUP BY.
C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\USQL-Programmability\Types.usql
62  5   USQL-Programmability
```

Bir UDT tanımlamak için şunları yapmanız gerekir:

* Aşağıdaki ad alanlarını ekleyin:

```
using Microsoft.Analytics.Interfaces
using System.IO;
```

* UDT arabirimleri için gereken `Microsoft.Analytics.Interfaces`ekleyin. Ayrıca, `System.IO` IFormatter arabirimini tanımlamak için gerekebilir.

* SqlUserDefinedType özniteliğiyle kullanılan tanımlı bir tür tanımlayın.

**SqlUserDefinedType** , bir derlemedeki tür tanımını U-SQL içinde Kullanıcı tanımlı tür (udt) olarak işaretlemek için kullanılır. Öznitelikteki özellikler, UDT 'nin fiziksel özelliklerini yansıtır. Bu sınıf devralınamaz.

SqlUserDefinedType, UDT tanımı için gerekli bir özniteliktir.

Sınıfının Oluşturucusu:  

* SqlUserDefinedTypeAttribute (tür biçimlendirici)

* Tür biçimlendiricisi: bir UDT biçimlendiricisi tanımlamak için gerekli parametre--özellikle `IFormatter` arabirimin türü geçirilmelidir.

```
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* Tipik UDT, aşağıdaki örnekte gösterildiği gibi, IFormatter arabiriminin tanımını de gerektirir:

```
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

`IFormatter` arabirimi, \<typeparamref Name = "T" > kök türü ile bir nesne grafiğini serileştirir ve serbest olarak serileştirir.

\<typeparam Name = "T", serileştirme ve seri hale getirme için nesne grafiğinin kök türü >.

* **Seri durumdan çıkarma**: verileri belirtilen akışta ve nesnelerin grafının reconstitutes seri hale getirir.

* **Serileştirme**: belirtilen akışa verilen köke sahip bir nesneyi veya nesne grafiğini seri hale getirir.

`MyType` örneği: türünün örneği.  
`IColumnWriter` yazıcı/`IColumnReader` okuyucu: temeldeki sütun akışı.  
`ISerializationContext` bağlamı: serileştirme sırasında akış için kaynak veya hedef bağlamını belirten bir bayrak kümesini tanımlayan sabit listesi.

* **Ara**: kaynak veya hedef bağlamın kalıcı bir depo olduğunu belirtir.

* **Kalıcılık**: kaynak veya hedef bağlamın kalıcı bir mağaza olduğunu belirtir.

Normal C# bir tür olarak, bir U-SQL udt tanımı +/= =/! = gibi işleçler için geçersiz kılma işlemleri içerebilir. Statik yöntemler de içerebilir. Örneğin, bu UDT 'yi bir U-SQL MIN toplam işlevine parametre olarak kullanacaksanız, < işleci geçersiz kılmayı tanımlamanız gerekir.

Bu kılavuzda daha önce, `Qn:Pn (Q1:P10)`biçimindeki belirli bir tarihten itibaren mali dönem tanımlama için bir örnek gösterilmiştir. Aşağıdaki örnek, mali dönem değerleri için nasıl özel bir tür tanımlanacağını gösterir.

Özel UDT ve IFormatter arabirimi ile arka plan kod bölümünün bir örneği aşağıda verilmiştir:

```
[SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
public struct FiscalPeriod
{
    public int Quarter { get; private set; }

    public int Month { get; private set; }

    public FiscalPeriod(int quarter, int month):this()
    {
    this.Quarter = quarter;
    this.Month = month;
    }

    public override bool Equals(object obj)
    {
    if (ReferenceEquals(null, obj))
    {
        return false;
    }

    return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
    }

    public bool Equals(FiscalPeriod other)
    {
return this.Quarter.Equals(other.Quarter) && this.Month.Equals(other.Month);
    }

    public bool GreaterThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
    }

    public bool LessThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
    }

    public override int GetHashCode()
    {
    unchecked
    {
        return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
    }
    }

    public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
    {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
    }

    public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.Equals(c2);
    }

    public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
    {
    return !c1.Equals(c2);
    }
    public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.GreaterThan(c2);
    }
    public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.LessThan(c2);
    }
    public override string ToString()
    {
    return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
    }

}

public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
{
    public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
    {
    using (var binaryWriter = new BinaryWriter(writer.BaseStream))
    {
        binaryWriter.Write(instance.Quarter);
        binaryWriter.Write(instance.Month);
        binaryWriter.Flush();
    }
    }

    public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
    {
    using (var binaryReader = new BinaryReader(reader.BaseStream))
    {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
        return result;
    }
    }
}
```

Tanımlı tür iki sayı içerir: çeyrek ve ay. İşleçler `==/!=/>/<` ve statik yöntem `ToString()` burada tanımlanmıştır.

Daha önce belirtildiği gibi, UDT SELECT ifadelerinde kullanılabilir, ancak özel serileştirme olmadan OUTPUTTER/EXTRACTOR içinde kullanılamaz. `ToString()` bir dize olarak serileştirilmesi ya da özel bir OUTPUTTER/EXTRACTOR ile kullanılması gerekmektedir.

Şimdi UDT kullanımını tartışalım. Arka plan kod bölümünde, Getfcalperiod işlevimizi şu şekilde değiştirdik:

```
public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
{
    int FiscalMonth = 0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter = 0;
    if (FiscalMonth >= 1 && FiscalMonth <= 3)
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

    return new FiscalPeriod(FiscalQuarter, FiscalMonth);
}
```

Gördüğünüz gibi, Fcalperiod türünden değeri döndürür.

Burada U-SQL temel komut dosyasında nasıl kullanacağınızı gösteren bir örnek sunuyoruz. Bu örnek, U-SQL betikten farklı UDT çağırma biçimlerini gösterir.

```
DECLARE @input_file string = @"c:\work\cosmos\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"c:\work\cosmos\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT 
        guid AS start_id,
        dt,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Quarter AS fiscalquarter,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Month AS fiscalmonth,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt) + new USQL_Programmability.CustomFunctions.FiscalPeriod(1,7) AS fiscalperiod_adjusted,
        user,
        des
    FROM @rs0;

@rs2 =
    SELECT 
           start_id,
           dt,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           fiscalquarter,
           fiscalmonth,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS fiscalperiod,

       // This user-defined type was created in the prior SELECT.  Passing the UDT to this subsequent SELECT would have failed if the UDT was not annotated with an IFormatter.
           fiscalperiod_adjusted.ToString() AS fiscalperiod_adjusted,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```

Aşağıda, arka plan kod bölümünün bir örneği verilmiştir:

```
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.IO;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        static public DateTime? ToDateTime(string dt)
        {
            DateTime dtValue;

            if (!DateTime.TryParse(dt, out dtValue))
                return Convert.ToDateTime(dt);
            else
                return null;
        }

        public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
        {
            int FiscalMonth = 0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter = 0;
            if (FiscalMonth >= 1 && FiscalMonth <= 3)
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

            return new FiscalPeriod(FiscalQuarter, FiscalMonth);
        }



        [SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
        public struct FiscalPeriod
        {
            public int Quarter { get; private set; }

            public int Month { get; private set; }

            public FiscalPeriod(int quarter, int month):this()
            {
                this.Quarter = quarter;
                this.Month = month;
            }

            public override bool Equals(object obj)
            {
                if (ReferenceEquals(null, obj))
                {
                    return false;
                }

                return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
            }

            public bool Equals(FiscalPeriod other)
            {
return this.Quarter.Equals(other.Quarter) &&    this.Month.Equals(other.Month);
            }

            public bool GreaterThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
            }

            public bool LessThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
            }

            public override int GetHashCode()
            {
                unchecked
                {
                    return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
                }
            }

            public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
            {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
            }

            public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.Equals(c2);
            }

            public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
            {
                return !c1.Equals(c2);
            }
            public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.GreaterThan(c2);
            }
            public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.LessThan(c2);
            }
            public override string ToString()
            {
                return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
            }

        }

        public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
        {
public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
            {
                using (var binaryWriter = new BinaryWriter(writer.BaseStream))
                {
                    binaryWriter.Write(instance.Quarter);
                    binaryWriter.Write(instance.Month);
                    binaryWriter.Flush();
                }
            }

public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
            {
                using (var binaryReader = new BinaryReader(reader.BaseStream))
                {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
                    return result;
                }
            }
        }
    }
}
```

## <a name="use-user-defined-aggregates-udagg"></a>Kullanıcı tanımlı toplamaları kullan: UBAGG
Kullanıcı tanımlı toplamalar, U-SQL ile kullanıma sunulmayan toplamada ilgili tüm işlevlerdir. Örnek, özel matematik hesaplamaları, dize birleştirmeleri, dizelerdeki işlemler ve benzeri işlemleri gerçekleştirmek için bir toplama olabilir.

Kullanıcı tanımlı toplu temel sınıf tanımı aşağıdaki gibidir:

```csharp
    [SqlUserDefinedAggregate]
    public abstract class IAggregate<T1, T2, TResult> : IAggregate
    {
        protected IAggregate();

        public abstract void Accumulate(T1 t1, T2 t2);
        public abstract void Init();
        public abstract TResult Terminate();
    }
```

**SqlUserDefinedAggregate** , türün Kullanıcı tanımlı toplama olarak kaydedilmesi gerektiğini gösterir. Bu sınıf devralınamaz.

SqlUserDefinedType özniteliği UDADGG tanımı için **isteğe bağlıdır** .


Temel sınıf üç soyut parametre geçirmenize olanak sağlar: iki adet giriş parametresi ve diğeri sonuç olarak. Veri türleri değişkendir ve sınıf devralma sırasında tanımlanmalıdır.

```
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    { … }

    public override void Accumulate(string guid, string user)
    { … }

    public override string Terminate()
    { … }
}
```

* **Init** , hesaplama sırasında her grup için bir kez çağırır. Her toplama grubu için bir başlatma yordamı sağlar.  
* **Birikme** her bir değer için bir kez yürütülür. Toplama algoritması için ana işlevsellik sağlar. Bu, sınıf devralma sırasında tanımlanan çeşitli veri türleriyle değerleri toplamak için kullanılabilir. Değişken veri türlerinin iki parametresini kabul edebilir.
* **Sonlandırma** , her grup için sonucu çıkarmak üzere işleme sonunda toplama grubu başına bir kez yürütülür.

Doğru giriş ve çıkış veri türlerini bildirmek için aşağıdaki gibi sınıf tanımını kullanın:

```
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1: biriktirmek için Ilk parametre
* T2: biriktirmek için Ikinci parametre
* TResult: sonlandırma türü döndürme

Örnek:

```
public class GuidAggregate : IAggregate<string, int, int>
```

or

```
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>U-SQL içinde UıDAVGG kullanma
UıLıGG 'yi kullanmak için, önce kodu arka planda tanımlayın veya daha önce anlatıldığı gibi var olan programlama DLL 'sinden başvuru yapın.

Ardından aşağıdaki sözdizimini kullanın:

```
AGG<UDAGG_functionname>(param1,param2)
```

Aşağıda bir UBAGG örneği verilmiştir:

```
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    {
        guid_agg = "";
    }

    public override void Accumulate(string guid, string user)
    {
        if (user.ToUpper()== "USER1")
        {
        guid_agg += "{" + guid + "}";
        }
    }

    public override string Terminate()
    {
        return guid_agg;
    }

}
```

Ve Base U-SQL betiği:

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @" \usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    SELECT
        user,
        AGG<USQL_Programmability.GuidAggregate>(guid,user) AS guid_list
    FROM @rs0
    GROUP BY user;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

Bu kullanım örneği senaryosunda, belirli kullanıcılar için sınıf GUID 'Lerini birleştirme yaptık.

## <a name="use-user-defined-objects-udo"></a>Kullanıcı tanımlı nesneleri kullan: UDO
U-SQL, Kullanıcı tanımlı nesneler veya UDO olarak adlandırılan özel programlama nesneleri tanımlamanıza olanak sağlar.

U-SQL içindeki UDO 'in bir listesi aşağıda verilmiştir:

* Kullanıcı tanımlı ayıklayıcıları
    * Satırı satıra göre Ayıkla
    * Özel yapılandırılmış dosyalardan veri ayıklama uygulamak için kullanılır

* Kullanıcı tanımlı çıktıcılar
    * Satıra göre çıkış satırı
    * Özel veri türlerini veya özel dosya biçimlerini çıkarmak için kullanılır

* Kullanıcı tanımlı işlemciler
    * Tek satır alın ve bir satır oluşturun
    * Sütun sayısını azaltmak veya var olan bir sütun kümesinden türetilen değerlerle yeni sütunlar üretmek için kullanılır

* Kullanıcı tanımlı appliers
    * Tek satır alın ve 0-n satır oluşturun
    * Dış/çapraz uygulama ile kullanılır

* Kullanıcı tanımlı combiners
    * Satır kümelerini birleştirir--Kullanıcı tanımlı birleşimler

* Kullanıcı tanımlı azaltıcının
    * N satır al ve bir satır üret
    * Satır sayısını azaltmak için kullanılır

UıDO genellikle aşağıdaki U-SQL deyimlerinin bir parçası olarak U-SQL komut dosyasında açıkça çağırılır:

* SÜZMEK
* ÇıKTıLARıN
* SÜREÇ
* BILE
* AZAL

> [!NOTE]  
> UDO 'lar 0,5 GB bellek kullanmak üzere sınırlandırılmıştır.  Bu bellek sınırlaması yerel yürütmeler için uygulanmaz.

## <a name="use-user-defined-extractors"></a>Kullanıcı tanımlı ayıklayıcıları kullanma
U-SQL, ayıklama ifadesini kullanarak dış verileri içeri aktarmanıza olanak tanır. EXTRACT ifadesinde yerleşik UıDO ayıklayıcıları kullanılabilir:  

* *Ayıklayıcıları. Text ()* : farklı kodlamalardan sınırlandırılmış metin dosyalarından ayıklama sağlar.

* *Ayıklayıcıları. csv ()* : farklı kodlamalar için virgülle ayrılmış değer (CSV) dosyalarından ayıklama sağlar.

* *Ayıklayıcıları. TSV ()* : farklı kodlamalara ait sekmeyle ayrılmış değer (TSV) dosyalarından ayıklama sağlar.

Özel bir ayıklayıcısı geliştirmek yararlı olabilir. Aşağıdaki görevlerden herhangi birini yapmak istiyorsam, bu, veri içeri aktarma sırasında yararlı olabilir:

* Sütunları bölerek ve ayrı değerleri değiştirerek giriş verilerini değiştirin. IŞLEMCI işlevselliği sütunları birleştirmek için daha iyidir.
* Web sayfaları ve e-postalar gibi yapılandırılmamış verileri veya XML/JSON gibi yarı yapılandırılmamış verileri ayrıştırın.
* Desteklenmeyen Kodlamadaki verileri ayrıştırın.

Kullanıcı tanımlı bir ayıklayıcısı veya NCESI tanımlamak için bir `IExtractor` arabirimi oluşturmanız gerekir. Yok edilecek sütun/satır sınırlayıcıları ve kodlama gibi tüm giriş parametrelerinin, sınıfın oluşturucusunda tanımlanması gerekir. `IExtractor` arabirimi Ayrıca, aşağıdaki gibi `IEnumerable<IRow>` geçersiz kılma için bir tanım içermelidir:

```
[SqlUserDefinedExtractor]
public class SampleExtractor : IExtractor
{
     public SampleExtractor(string row_delimiter, char col_delimiter)
     { … }

     public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
     { … }
}
```

**SqlUserDefinedExtractor** özniteliği, türün Kullanıcı tanımlı bir ayıklayıcı olarak kaydedilmesi gerektiğini belirtir. Bu sınıf devralınamaz.

SqlUserDefinedExtractor, NCESI tanımı için isteğe bağlı bir özniteliktir. NCESI nesnesi için AtomicFileProcessing özelliğini tanımlamak üzere kullanılır.

* bool AtomicFileProcessing   

* **true** = bu ayıklayıcı atomik giriş dosyaları gerektirdiğini BELIRTIR (JSON, XML,...)
* **false** = bu Extractor, bölünmüş/dağıtılmış DOSYALARLA (CSV, seq,...) ilgilenebileceğini belirtir

Ana NCESI programlama nesneleri **giriş** ve **Çıkış**. Giriş nesnesi `IUnstructuredReader`olarak giriş verilerini numaralandırmak için kullanılır. Çıkış nesnesi, Extractor etkinliğinin sonucu olarak çıkış verilerini ayarlamak için kullanılır.

Giriş verilerine `System.IO.Stream` ve `System.IO.StreamReader`üzerinden erişilir.

Giriş sütunları numaralandırması için, önce bir satır sınırlayıcısı kullanarak giriş akışını böleceğiz.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

Ardından, giriş satırını daha fazla sütun bölümüne bölebilirsiniz.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
    string[] parts = line.Split(my_column_delimiter);
    foreach (string part in parts)
    { … }
}
```

Çıktı verilerini ayarlamak için `output.Set` yöntemini kullanırız.

Özel ayıklayıcı 'nın yalnızca çıkış ile tanımlanan sütunları ve değerleri çıktı olarak öğrendiğinden emin olmak önemlidir. Yöntem çağrısını ayarla.

```
output.Set<string>(count, part);
```

Gerçek ayıklayıcı çıktısı, `yield return output.AsReadOnly();`çağırarak tetiklenir.

Aşağıda, Extractor örneği verilmiştir:

```
[SqlUserDefinedExtractor(AtomicFileProcessing = true)]
public class FullDescriptionExtractor : IExtractor
{
     private Encoding _encoding;
     private byte[] _row_delim;
     private char _col_delim;

    public FullDescriptionExtractor(Encoding encoding, string row_delim = "\r\n", char col_delim = '\t')
    {
         this._encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
         this._row_delim = this._encoding.GetBytes(row_delim);
         this._col_delim = col_delim;

    }

    public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
    {
         string line;
         //Read the input line by line
         foreach (Stream current in input.Split(_encoding.GetBytes("\r\n")))
         {
        using (System.IO.StreamReader streamReader = new StreamReader(current, this._encoding))
         {
             line = streamReader.ReadToEnd().Trim();
             //Split the input by the column delimiter
             string[] parts = line.Split(this._col_delim);
             int count = 0; // start with first column
             foreach (string part in parts)
             {
    if (count == 0)
             {  // for column “guid”, re-generated guid
                 Guid new_guid = Guid.NewGuid();
                 output.Set<Guid>(count, new_guid);
             }
             else if (count == 2)
             {
                 // for column “user”, convert to UPPER case
                 output.Set<string>(count, part.ToUpper());

             }
             else
             {
                 // keep the rest of the columns as-is
                 output.Set<string>(count, part);
             }
             count += 1;
             }

         }
         yield return output.AsReadOnly();
         }
         yield break;
     }
}
```

Bu kullanım örneği senaryosunda, Extractor "GUID" sütunu için GUID 'ı yeniden oluşturur ve "user" sütununun değerlerini büyük harfe dönüştürür. Özel ayıklayıcıları, giriş verilerini ayrıştırarak ve işleyerek daha karmaşık sonuçlar üretebilir.

Aşağıda özel bir ayıklayıcısı kullanan temel U-SQL betiği verilmiştir:

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file
        USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 TO @output_file USING Outputters.Text();
```

## <a name="use-user-defined-outputters"></a>Kullanıcı tanımlı çıktıcılar kullanın
Kullanıcı tanımlı outputter, yerleşik U-SQL işlevlerini genişletmenizi sağlayan başka bir U-SQL UDO. Extractor ile benzer şekilde, çeşitli yerleşik outputler vardır.

* *Outputters. Text ()* : verileri farklı kodlamalara ait sınırlandırılmış metin dosyalarına yazar.
* *Outputters. csv ()* : farklı kodlamalar için verileri virgülle ayrılmış değer (CSV) dosyalarına yazar.
* *Outputters. TSV ()* : verileri farklı kodlamalara ait sekmeyle ayrılmış değer (TSV) dosyalarına yazar.

Özel çıktıcısı, verileri özel bir tanımlı biçimde yazmanızı sağlar. Bu, aşağıdaki görevler için yararlı olabilir:

* Yarı yapılandırılmış veya yapılandırılmamış dosyalara veri yazma.
* Veri yazma desteklenmeyen kodlamalar.
* Çıktı verilerini değiştirme veya özel öznitelikler ekleme.

Kullanıcı tanımlı outputter tanımlamak için `IOutputter` arabirimini oluşturuyoruz.

Temel `IOutputter` sınıfı uygulama aşağıda verilmiştir:

```
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

Outputter için sütun/satır sınırlayıcıları, kodlama vb. gibi tüm giriş parametrelerinin, sınıfın oluşturucusunda tanımlanması gerekir. `IOutputter` arabirimi, `void Output` geçersiz kılma için bir tanım de içermelidir. Öznitelik `[SqlUserDefinedOutputter(AtomicFileProcessing = true)`, isteğe bağlı olarak atomik dosya işleme için ayarlanabilir. Daha fazla bilgi için aşağıdaki ayrıntılara bakın.

```
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class MyOutputter : IOutputter
{

    public MyOutputter(myparam1, myparam2)
    {
      …
    }

    public override void Close()
    {
      …
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
      …
    }
}
```

* her giriş satırı için `Output` çağırılır. `IUnstructuredWriter output` satır kümesini döndürür.
* Oluşturucu sınıfı, parametreleri Kullanıcı tanımlı outputter 'a geçirmek için kullanılır.
* `Close`, isteğe bağlı olarak geçersiz kılmak veya son satırın ne zaman yazıldığını belirleyebilmek için kullanılır.

**SqlUserDefinedOutputter** özniteliği, türün Kullanıcı tanımlı bir outputter olarak kaydedilmesi gerektiğini belirtir. Bu sınıf devralınamaz.

SqlUserDefinedOutputter, Kullanıcı tanımlı bir outputter tanımı için isteğe bağlı bir özniteliktir. AtomicFileProcessing özelliğini tanımlamak için kullanılır.

* bool AtomicFileProcessing   

* **true** = bu outputter 'ın atomik çıkış dosyaları gerektirdiğini BELIRTIR (JSON, XML,...)
* **false** = bu outputter 'ın bölünmüş/dağıtılmış DOSYALARLA (CSV, seq,...) ilgilenebileceğini belirtir

Ana programlama nesneleri **satır** ve **çıktıdır**. **Satır** nesnesi, çıkış verilerini `IRow` arabirimi olarak listelemek için kullanılır. Çıktı verilerini hedef dosyaya ayarlamak için **Çıkış** kullanılır.

Çıkış verilerine `IRow` arabirimi üzerinden erişilir. Çıkış verileri bir seferde bir satıra geçirilir.

Bağımsız değerler, IRow arabiriminin get yöntemi çağırarak numaralandırılır:

```
row.Get<string>("column_name")
```

Tek tek sütun adları, `row.Schema`çağırarak belirlenebilir:

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Bu yaklaşım, tüm meta veri şemaları için esnek bir outputderme oluşturmanızı sağlar.

Çıkış verileri `System.IO.StreamWriter`kullanılarak dosyaya yazılır. Stream parametresi, `IUnstructuredWriter output`bir parçası olarak `output.BaseStream` olarak ayarlanır.

Her satır yinelemesi sonrasında dosyaya veri arabelleğini temizleme önemli olduğunu unutmayın. Ayrıca, `StreamWriter` nesnesi, etkin atılabilir özniteliğiyle birlikte kullanılmalıdır (varsayılan) ve **using** anahtar sözcüğüyle birlikte olmalıdır:

```
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

Aksi takdirde, her yinelemeden sonra açıkça flush () yöntemini çağırın. Bu, aşağıdaki örnekte gösterilmektedir.

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>Kullanıcı tanımlı outputter için üst bilgileri ve altbilgileri ayarlama
Bir üst bilgi ayarlamak için tek yineleme yürütme akışını kullanın.

```
public override void Output(IRow row, IUnstructuredWriter output)
{
 …
if (isHeaderRow)
{
    …                
}

 …
if (isHeaderRow)
{
    isHeaderRow = false;
}
 …
}
}
```

İlk `if (isHeaderRow)` bloğundaki kod yalnızca bir kez yürütülür.

Alt bilgi için, `System.IO.Stream` nesnesi örneğine başvuruyu kullanın (`output.BaseStream`). `IOutputter` arabiriminin Close () yönteminde alt bilgiyi yazın.  (Daha fazla bilgi için aşağıdaki örneğe bakın.)

Aşağıda, Kullanıcı tanımlı bir outputter örneği verilmiştir:

```
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class HTMLOutputter : IOutputter
{
    // Local variables initialization
    private string row_delimiter;
    private char col_delimiter;
    private bool isHeaderRow;
    private Encoding encoding;
    private bool IsTableHeader = true;
    private Stream g_writer;

    // Parameters definition            
    public HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    this.isHeaderRow = isHeader;
    this.encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
    }

    // The Close method is used to write the footer to the file. It's executed only once, after all rows
    public override void Close()
    {
    //Reference to IO.Stream object - g_writer
    StreamWriter streamWriter = new StreamWriter(g_writer, this.encoding);
    streamWriter.Write("</table>");
    streamWriter.Flush();
    streamWriter.Close();
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
    System.IO.StreamWriter streamWriter = new StreamWriter(output.BaseStream, this.encoding);

    // Metadata schema initialization to enumerate column names
    ISchema schema = row.Schema;

    // This is a data-independent header--HTML table definition
    if (IsTableHeader)
    {
        streamWriter.Write("<table border=1>");
        IsTableHeader = false;
    }

    // HTML table attributes
    string header_wrapper_on = "<th>";
    string header_wrapper_off = "</th>";
    string data_wrapper_on = "<td>";
    string data_wrapper_off = "</td>";

    // Header row output--runs only once
    if (isHeaderRow)
    {
        streamWriter.Write("<tr>");
        for (int i = 0; i < schema.Count(); i++)
        {
        var col = schema[i];
        streamWriter.Write(header_wrapper_on + col.Name + header_wrapper_off);
        }
        streamWriter.Write("</tr>");
    }

    // Data row output
    streamWriter.Write("<tr>");                
    for (int i = 0; i < schema.Count(); i++)
    {
        var col = schema[i];
        string val = "";
        try
        {
        // Data type enumeration--required to match the distinct list of types from OUTPUT statement
        switch (col.Type.Name.ToString().ToLower())
        {
            case "string": val = row.Get<string>(col.Name).ToString(); break;
            case "guid": val = row.Get<Guid>(col.Name).ToString(); break;
            default: break;
        }
        }
        // Handling NULL values--keeping them empty
        catch (System.NullReferenceException)
        {
        }
        streamWriter.Write(data_wrapper_on + val + data_wrapper_off);
    }
    streamWriter.Write("</tr>");

    if (isHeaderRow)
    {
        isHeaderRow = false;
    }
    // Reference to the instance of the IO.Stream object for footer generation
    g_writer = output.BaseStream;
    streamWriter.Flush();
    }
}

// Define the factory classes
public static class Factory
{
    public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    return new HTMLOutputter(isHeader, encoding);
    }
}
```

Ve U-SQL taban betiği:

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.html";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
         FROM @input_file
         USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 
    TO @output_file 
    USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Bu, tablo verileri içeren bir HTML dosyası oluşturan bir HTML çıktıdır.

### <a name="call-outputter-from-u-sql-base-script"></a>U-SQL taban betiğiyle çağrı çıktıcısı
Temel U-SQL betiğinden özel bir outputter çağırmak için, outputter nesnesinin yeni örneğinin oluşturulması gerekir.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Temel betikte nesnenin bir örneğini oluşturmaktan kaçınmak için, önceki örnekte gösterildiği gibi bir işlev sarmalayıcı oluşturuyoruz:

```csharp
        // Define the factory classes
        public static class Factory
        {
            public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                return new HTMLOutputter(isHeader, encoding);
            }
        }
```

Bu durumda, özgün çağrı aşağıdaki gibi görünür:

```
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="use-user-defined-processors"></a>Kullanıcı tanımlı işlemcileri kullanma
Kullanıcı tanımlı işlemci veya UDP, programlama özellikleri uygulayarak gelen satırları işlemenizi sağlayan bir U-SQL UDO türüdür. UDP sütunları birleştirip, değerleri değiştirmenize ve gerekirse yeni sütunlar eklemenize olanak sağlar. Temel olarak, gerekli veri öğelerini oluşturmak için bir satır kümesini işlemeye yardımcı olur.

Bir UDP tanımlamak için, UDP için isteğe bağlı olan `SqlUserDefinedProcessor` özniteliğiyle bir `IProcessor` arabirimi oluşturmanız gerekir.

Bu arabirim, aşağıdaki örnekte gösterildiği gibi `IRow` Interface satır kümesi geçersiz kılma tanımını içermelidir:

```
[SqlUserDefinedProcessor]
public class MyProcessor: IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
        …
 }
}
```

**Sqluserdefinedprocessor** , türün Kullanıcı tanımlı bir işlemci olarak kaydedilmesi gerektiğini gösterir. Bu sınıf devralınamaz.

SqlUserDefinedProcessor özniteliği UDP tanımı için **isteğe bağlıdır** .

Ana programlama nesneleri **giriş** ve **Çıkış**' dır. Giriş nesnesi, giriş sütunlarını ve çıkışını numaralandırmak ve çıkış verilerini işlemci etkinliğinin sonucu olarak ayarlamak için kullanılır.

Giriş sütunları numaralandırması için `input.Get` yöntemini kullanırız.

```
string column_name = input.Get<string>("column_name");
```

`input.Get` yöntemi parametresi, U-SQL temel betiğinin `PROCESS` bildiriminin `PRODUCE` yan tümcesinin bir parçası olarak geçirilmiş bir sütundur. Burada doğru veri türünü kullandık.

Çıktı için `output.Set` yöntemini kullanın.

Özel üreticinin yalnızca `output.Set` yöntemi çağrısıyla tanımlanan sütunları ve değerleri çıktı olarak gösterdiğine dikkat edin.

```
output.Set<string>("mycolumn", mycolumn);
```

Gerçek işlemci çıkışı `return output.AsReadOnly();`çağırarak tetiklenir.

Aşağıda bir işlemci örneği verilmiştir:

```
[SqlUserDefinedProcessor]
public class FullDescriptionProcessor : IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
     string user = input.Get<string>("user");
     string des = input.Get<string>("des");
     string full_description = user.ToUpper() + "=>" + des;
     output.Set<string>("dt", input.Get<string>("dt"));
     output.Set<string>("full_description", full_description);
     output.Set<Guid>("new_guid", Guid.NewGuid());
     output.Set<Guid>("guid", input.Get<Guid>("guid"));
     return output.AsReadOnly();
 }
}
```

Bu kullanım örneği senaryosunda, işlemci mevcut sütunları birleştirerek "full_description" adlı yeni bir sütun oluşturuyor (Bu durumda, büyük harfle "user" ve "des"). Ayrıca bir GUID 'YI yeniden oluşturur ve özgün ve yeni GUID değerlerini döndürür.

Önceki örnekte görebileceğiniz gibi, `output.Set` Yöntem çağrısı sırasında yöntemleri çağırabilirsiniz C# .

Aşağıda, özel bir işlemci kullanan bir Base U-SQL betiğinin örneği verilmiştir:

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
     PROCESS @rs0
     PRODUCE dt String,
             full_description String,
             guid Guid,
             new_guid Guid
     USING new USQL_Programmability.FullDescriptionProcessor();

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

## <a name="use-user-defined-appliers"></a>Kullanıcı tanımlı appliers kullanma
U-SQL Kullanıcı tanımlı bir uygulayıcı, bir sorgunun dış tablo ifadesiyle döndürülen C# her satır için özel bir işlev çağırmanızı sağlar. Doğru giriş, sol girdinin her bir satırı için değerlendirilir ve üretilen satırlar nihai çıkış için birleştirilir. APPLY işleci tarafından üretilen sütunların listesi, sol taraftaki sütun kümesinin ve doğru girişin birleşimidir.

Kullanıcı tanımlı Uygulayıcı, USQL SELECT ifadesinin bir parçası olarak çağrılır.

Kullanıcı tanımlı Uygulayıcı için tipik çağrı aşağıdakine benzer:

```
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

Bir SELECT ifadesinde appliers kullanma hakkında daha fazla bilgi için, bkz. [U-SQL SELECT-Apply ve Outer Apply 'Tan seçme](/u-sql/statements-and-expressions/select/from/select-selecting-from-cross-apply-and-outer-apply).

Kullanıcı tanımlı uygulayıcı temel sınıf tanımı aşağıdaki gibidir:

```
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

Kullanıcı tanımlı bir uygulayıcı tanımlamak için, Kullanıcı tanımlı uygulayıcı tanımı için isteğe bağlı olan [`SqlUserDefinedApplier`] özniteliğiyle `IApplier` arabirimini oluşturuyoruz.

```
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
    public ParserApplier()
    {
        …
    }

    public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
    {
        …
    }
}
```

* Apply, dış tablonun her satırı için çağırılır. `IUpdatableRow` çıktı satır kümesini döndürür.
* Oluşturucu sınıfı, parametreleri Kullanıcı tanımlı uygulayıcı geçirmek için kullanılır.

**Sqluserdefinedapp,** türün Kullanıcı tanımlı bir uygulayıcı olarak kaydedilmesi gerektiğini belirtir. Bu sınıf devralınamaz.

**Sqluserdefinedappfetıon** , Kullanıcı tanımlı bir uygulayıcı tanımı için **isteğe bağlıdır** .


Ana programlama nesneleri aşağıdaki gibidir:

```
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

Giriş satır kümeleri `IRow` girişi olarak geçirilir. Çıkış satırları `IUpdatableRow` çıkış arabirimi olarak oluşturulur.

Tek sütun adları, `IRow` şema yöntemi çağırarak belirlenebilir.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Gelen `IRow`gerçek veri değerlerini almak için `IRow` arabiriminin Get () yöntemini kullanırız.

```
mycolumn = row.Get<int>("mycolumn")
```

Ya da şema sütun adını kullanıyoruz:

```
row.Get<int>(row.Schema[0].Name)
```

Çıkış değerleri `IUpdatableRow` çıktı ile ayarlanmalıdır:

```
output.Set<int>("mycolumn", mycolumn)
```

Özel appliers yalnızca çıkış sütunlarının ve `output.Set` yöntemi çağrısıyla tanımlanmış değerlerin olduğunu anlamak önemlidir.

Gerçek çıktı, `yield return output.AsReadOnly();`çağırarak tetiklenir.

Kullanıcı tanımlı uygulayıcı parametreleri oluşturucuya geçirilebilir. Uygulayıcı, Base U-SQL betiğinde uygulayıcı çağrısı sırasında tanımlanması gereken sayıda sütun döndürebilir.

```
new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

Kullanıcı tanımlı uygulayıcı örneği aşağıda verilmiştir:

```
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
private string parsingPart;

public ParserApplier(string parsingPart)
{
    if (parsingPart.ToUpper().Contains("ALL")
    || parsingPart.ToUpper().Contains("MAKE")
    || parsingPart.ToUpper().Contains("MODEL")
    || parsingPart.ToUpper().Contains("YEAR")
    || parsingPart.ToUpper().Contains("TYPE")
    || parsingPart.ToUpper().Contains("MILLAGE")
    )
    {
    this.parsingPart = parsingPart;
    }
    else
    {
    throw new ArgumentException("Incorrect parameter. Please use: 'ALL[MAKE|MODEL|TYPE|MILLAGE]'");
    }
}

public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
{

    string[] properties = input.Get<string>("properties").Split(',');

    //  only process with correct number of properties
    if (properties.Count() == 5)
    {

    string make = properties[0];
    string model = properties[1];
    string year = properties[2];
    string type = properties[3];
    int millage = -1;

    // Only return millage if it is number, otherwise, -1
    if (!int.TryParse(properties[4], out millage))
    {
        millage = -1;
    }

    if (parsingPart.ToUpper().Contains("MAKE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("make", make);
    if (parsingPart.ToUpper().Contains("MODEL") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("model", model);
    if (parsingPart.ToUpper().Contains("YEAR") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("year", year);
    if (parsingPart.ToUpper().Contains("TYPE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("type", type);
    if (parsingPart.ToUpper().Contains("MILLAGE") || parsingPart.ToUpper().Contains("ALL")) output.Set<int>("millage", millage);
    }
    yield return output.AsReadOnly();            
}
}
```

Aşağıda, bu kullanıcı tanımlı Uygulayıcı için temel U-SQL betiği verilmiştir:

```
DECLARE @input_file string = @"c:\usql-programmability\car_fleet.tsv";
DECLARE @output_file string = @"c:\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        stocknumber int,
        vin String,
        properties String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        r.stocknumber,
        r.vin,
        properties.make,
        properties.model,
        properties.year,
        properties.type,
        properties.millage
    FROM @rs0 AS r
    CROSS APPLY
    new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

Bu kullanım örneği senaryosunda, Kullanıcı tanımlı Uygulayıcı, otomobil özellikleri için virgülle ayrılmış bir değer ayrıştırıcısı işlevi görür. Giriş dosyası satırları aşağıdaki gibi görünür:

```
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Chevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

Bu, oluşturma ve model gibi araba özelliklerini içeren bir özellikler sütunuyla sekmeyle ayrılmış tipik bir TSV dosyasıdır. Bu özellikler tablo sütunlarına ayrıştırılmalıdır. Ayrıca, verilen Uygulayıcı, sonuç satır kümesinde geçirilen parametreye göre dinamik bir dizi özellik oluşturmanıza olanak sağlar. Tüm özellikleri ya da yalnızca belirli bir özellikler kümesini oluşturabilirsiniz.

    …USQL_Programmability.ParserApplier ("all")
    …USQL_Programmability.ParserApplier ("make")
    …USQL_Programmability.ParserApplier ("make&model")

Kullanıcı tanımlı Uygulayıcı, uygulayıcı nesnesinin yeni bir örneği olarak çağrılabilir:

```
CROSS APPLY new MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```

Ya da sarmalayıcı fabrikası yönteminin çağrılması:

```csharp
    CROSS APPLY MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```

## <a name="use-user-defined-combiners"></a>Kullanıcı tanımlı combiners kullanma
Kullanıcı tanımlı Birleştirici veya UDC, satırları sol ve sağ satır kümelerine özel mantığa göre birleştirmenizi sağlar. Kullanıcı tanımlı birleştirici, BIRLEŞTIRME ifadesiyle birlikte kullanılır.

Bir birleştirici, hem giriş satır kümeleri, gruplama sütunları, beklenen sonuç şeması ve ek bilgiler hakkında gerekli bilgileri sağlayan BIRLEŞTIRME ifadesiyle çağrılır.

Bir temel U-SQL betiğinde bir birleştirici çağırmak için aşağıdaki sözdizimini kullanıyoruz:

```
Combine_Expression :=
    'COMBINE' Combine_Input
    'WITH' Combine_Input
    Join_On_Clause
    Produce_Clause
    [Readonly_Clause]
    [Required_Clause]
    USING_Clause.
```

Daha fazla bilgi için bkz. [birleştirme ifadesi (U-SQL)](/u-sql/statements-and-expressions/combine-expression).

Kullanıcı tanımlı bir birleştirici tanımlamak için, Kullanıcı tanımlı birleştirici tanımı için isteğe bağlı olan [`SqlUserDefinedCombiner`] özniteliğiyle `ICombiner` arabirimini oluşturuyoruz.

Temel `ICombiner` sınıf tanımı:

```
public abstract class ICombiner : IUserDefinedOperator
{
protected ICombiner();
public virtual IEnumerable<IRow> Combine(List<IRowset> inputs,
       IUpdatableRow output);
public abstract IEnumerable<IRow> Combine(IRowset left, IRowset right,
       IUpdatableRow output);
}
```

Bir `ICombiner` arabiriminin özel uygulanması, bir `IEnumerable<IRow>` birleştirme geçersiz kılma tanımını içermelidir.

```
[SqlUserDefinedCombiner]
public class MyCombiner : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    …
}
}
```

**SqlUserDefinedCombiner** özniteliği, türün Kullanıcı tanımlı birleştirici olarak kaydedilmesi gerektiğini gösterir. Bu sınıf devralınamaz.

**SqlUserDefinedCombiner** , birleştirici Mode özelliğini tanımlamak için kullanılır. Kullanıcı tanımlı birleştirici tanımı için isteğe bağlı bir özniteliktir.

CombinerMode modu

CombinerMode numaralandırması aşağıdaki değerleri alabilir:

* Tam (0) her çıkış satırı büyük olasılıkla aynı anahtar değeriyle soldan ve sağda tüm giriş satırlarına bağlıdır.

* Sol (1) her çıkış satırı, sol taraftaki tek bir giriş satırına bağlıdır (ve potansiyel olarak aynı anahtar değeri ile sağdaki tüm satırlardan).

* Sağ (2) her çıkış satırı, sağdaki tek bir giriş satırına bağlıdır (ve potansiyel olarak aynı anahtar değeri ile soldan tüm satırlar).

* İç (3) her çıkış satırı, tek bir giriş satırına, aynı değer ile soldan ve sağdan bağlıdır.

Örnek: [`SqlUserDefinedCombiner(Mode=CombinerMode.Left)`]


Ana programlama nesneleri şunlardır:

```csharp
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

Giriş satır kümeleri, arabirimin **sol** ve **sağ** `IRowset` türü olarak geçirilir. Her iki satır kümesi de işlenmek üzere numaralandırılmalıdır. Her bir arabirimi yalnızca bir kez numaralandırabilirsiniz, bu nedenle gerekirse bunları listeletmemiz ve önbelleğe almanız gerekiyordu.

Önbelleğe alma amacıyla, bir LINQ sorgu yürütmesinin sonucu olarak bir\<T\> bellek yapısı türü oluşturabilir, özellikle <`IRow`> listeleyin. Anonim veri türü ayrıca numaralandırma sırasında kullanılabilir.

IEnumerable\<t\> arabirimi hakkında daha fazla bilgi için LINQ sorguları hakkında daha fazla bilgi için bkz. [LINQ Sorgularına Giriş (C#)](/dotnet/csharp/programming-guide/concepts/linq/introduction-to-linq-queries) ve [IEnumerable\<t\> arabirimi](/dotnet/api/system.collections.generic.ienumerable-1) .

Gelen `IRowset`gerçek veri değerlerini almak için `IRow` arabiriminin Get () yöntemini kullanırız.

```
mycolumn = row.Get<int>("mycolumn")
```

Tek sütun adları, `IRow` şema yöntemi çağırarak belirlenebilir.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Ya da şema sütun adını kullanarak:

```
c# row.Get<int>(row.Schema[0].Name)
```

LINQ ile genel sabit listesi aşağıdaki gibi görünür:

```
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

Her iki satır kümesi numaralandırıldıktan sonra tüm satırlarda döngü ekleyeceğiz. Sol satır kümesindeki her satır için, birleştirici durumunu karşılayan tüm satırları bulacağız.

Çıkış değerleri `IUpdatableRow` çıktı ile ayarlanmalıdır.

```
output.Set<int>("mycolumn", mycolumn)
```

Gerçek çıktı, `yield return output.AsReadOnly();`çağırarak tetiklenir.

Aşağıda bir birleştirici örneği verilmiştir:

```
[SqlUserDefinedCombiner]
public class CombineSales : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    var internetSales =
    (from row in left.Rows
          select new
          {
              ProductKey = row.Get<int>("ProductKey"),
              OrderDateKey = row.Get<int>("OrderDateKey"),
              SalesAmount = row.Get<decimal>("SalesAmount"),
              TaxAmt = row.Get<decimal>("TaxAmt")
          }).ToList();

    var resellerSales =
    (from row in right.Rows
     select new
     {
     ProductKey = row.Get<int>("ProductKey"),
     OrderDateKey = row.Get<int>("OrderDateKey"),
     SalesAmount = row.Get<decimal>("SalesAmount"),
     TaxAmt = row.Get<decimal>("TaxAmt")
     }).ToList();

    foreach (var row_i in internetSales)
    {
    foreach (var row_r in resellerSales)
    {

        if (
        row_i.OrderDateKey > 0
        && row_i.OrderDateKey < row_r.OrderDateKey
        && row_i.OrderDateKey == 20010701
        && (row_r.SalesAmount + row_r.TaxAmt) > 20000)
        {
        output.Set<int>("OrderDateKey", row_i.OrderDateKey);
        output.Set<int>("ProductKey", row_i.ProductKey);
        output.Set<decimal>("Internet_Sales_Amount", row_i.SalesAmount + row_i.TaxAmt);
        output.Set<decimal>("Reseller_Sales_Amount", row_r.SalesAmount + row_r.TaxAmt);
        }

    }
    }
    yield return output.AsReadOnly();
}
}
```

Bu kullanım örneği senaryosunda, perakende için bir analiz raporu oluşturacağız. Amaç, $20.000 'den daha fazla maliyet sunan ve belirli bir zaman çerçevesinde normal satıcıdan daha hızlı satış yapan tüm ürünleri bulmaktır.

Temel U-SQL betiği aşağıda verilmiştir. Bir normal JOIN ve birleştirici arasındaki mantığı karşılaştırabilirsiniz:

```sql
DECLARE @LocalURI string = @"\usql-programmability\";

DECLARE @input_file_internet_sales string = @LocalURI+"FactInternetSales.txt";
DECLARE @input_file_reseller_sales string = @LocalURI+"FactResellerSales.txt";
DECLARE @output_file1 string = @LocalURI+"output_file1.tsv";
DECLARE @output_file2 string = @LocalURI+"output_file2.tsv";

@fact_internet_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    CustomerKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_internet_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@fact_reseller_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    ResellerKey int ,
    EmployeeKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_reseller_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@rs1 =
SELECT
    fis.OrderDateKey,
    fis.ProductKey,
    fis.SalesAmount+fis.TaxAmt AS Internet_Sales_Amount,
    frs.SalesAmount+frs.TaxAmt AS Reseller_Sales_Amount
FROM @fact_internet_sales AS fis
     INNER JOIN @fact_reseller_sales AS frs
     ON fis.ProductKey == frs.ProductKey
WHERE
    fis.OrderDateKey < frs.OrderDateKey
    AND fis.OrderDateKey == 20010701
    AND frs.SalesAmount+frs.TaxAmt > 20000;

@rs2 =
COMBINE @fact_internet_sales AS fis
WITH @fact_reseller_sales AS frs
ON fis.ProductKey == frs.ProductKey
PRODUCE OrderDateKey int,
        ProductKey int,
        Internet_Sales_Amount decimal,
        Reseller_Sales_Amount decimal
USING new USQL_Programmability.CombineSales();

OUTPUT @rs1 TO @output_file1 USING Outputters.Tsv();
OUTPUT @rs2 TO @output_file2 USING Outputters.Tsv();
```

Kullanıcı tanımlı bir birleştirici, uygulayıcı nesnesinin yeni bir örneği olarak çağrılabilir:

```
USING new MyNameSpace.MyCombiner();
```


Ya da sarmalayıcı fabrikası yönteminin çağrılması:

```
USING MyNameSpace.MyCombiner();
```

## <a name="use-user-defined-reducers"></a>Kullanıcı tanımlı azaltıcının kullanma

U-SQL, Kullanıcı tanımlı operatör genişletilebilirlik çerçevesini kullanarak ve C# bir IReducer arabirimi uygulayarak özel satır kümesi azaltıcının yazmanızı sağlar.

Kullanıcı tanımlı Reducer veya UDR, veri ayıklama (içeri aktarma) sırasında gereksiz satırları ortadan kaldırmak için kullanılabilir. Ayrıca, satırları ve sütunları işlemek ve değerlendirmek için de kullanılabilir. Programlama mantığını temel alarak, hangi satırların ayıklanmak gerektiğini de tanımlayabilir.

UDR sınıfını tanımlamak için, isteğe bağlı bir `SqlUserDefinedReducer` özniteliğiyle `IReducer` arabirimi oluşturmanız gerekir.

Bu sınıf arabirimi `IEnumerable` arabirimi satır kümesi geçersiz kılma için bir tanım içermelidir.

```
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
        …
    }

}
```

**SqlUserDefinedReducer** özniteliği, türün Kullanıcı tanımlı Reducer olarak kaydedilmesi gerektiğini gösterir. Bu sınıf devralınamaz.
**SqlUserDefinedReducer** , Kullanıcı tanımlı Reducer tanımı için isteğe bağlı bir özniteliktir. Isyinelemeli özelliği tanımlamak için kullanılır.

* bool Isyinelemeli    
* **true** = bu Reducer ilişkilendirilebilir ve iletişim olup olmadığını belirtir

Ana programlama nesneleri **giriş** ve **Çıkış**' dır. Giriş nesnesi, giriş satırlarını numaralandırmak için kullanılır. Çıktı, etkinliğin azaltılmasının sonucu olarak çıktı satırlarını ayarlamak için kullanılır.

Giriş satırları numaralandırması için `Row.Get` yöntemini kullanırız.

```
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

`Row.Get` yönteminin parametresi, U-SQL temel betiğinin `REDUCE` bildiriminin `PRODUCE` sınıfının bir parçası olarak geçirilmiş bir sütundur. Burada doğru veri türünü de kullanmanız gerekir.

Çıktı için `output.Set` yöntemini kullanın.

Özel Reducer yalnızca `output.Set` yöntemi çağrısıyla tanımlanan değerlerin çıkış yaptığını anlamak önemlidir.

```
output.Set<string>("mycolumn", guid);
```

Gerçek Reducer çıktısı `yield return output.AsReadOnly();`çağırarak tetiklenir.

Aşağıda bir Reducer örneği verilmiştir:

```
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
    string guid;
    DateTime dt;
    string user;
    string des;

    foreach (IRow row in input.Rows)
    {
        guid = row.Get<string>("guid");
        dt = row.Get<DateTime>("dt");
        user = row.Get<string>("user");
        des = row.Get<string>("des");

        if (user.Length > 0)
        {
        output.Set<string>("guid", guid);
        output.Set<DateTime>("dt", dt);
        output.Set<string>("user", user);
        output.Set<string>("des", des);

        yield return output.AsReadOnly();
        }
    }
    }

}
```

Bu kullanım örneği senaryosunda, Reducer boş Kullanıcı adına sahip satırları atlıyor. Satır kümesindeki her satır için, gerekli her sütunu okur, ardından Kullanıcı adının uzunluğunu değerlendirir. Yalnızca Kullanıcı adı değerinin uzunluğu 0 ' dan büyükse gerçek satırı verir.

Aşağıda, özel bir Reducer kullanan temel U-SQL betiği verilmiştir:

```
DECLARE @input_file string = @"\usql-programmability\input_file_reducer.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    REDUCE @rs0 PRESORT guid
    ON guid  
    PRODUCE guid string, dt DateTime, user String, des String
    USING new USQL_Programmability.EmptyUserReducer();

@rs2 =
    SELECT guid AS start_id,
           dt AS start_time,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS start_fiscalperiod,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```
