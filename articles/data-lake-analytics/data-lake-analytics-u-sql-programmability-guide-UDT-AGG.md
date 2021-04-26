---
title: Azure Data Lake için U-SQL UDT ve UDADGG Programlama Kılavuzu
description: İyi USQL betiği oluşturmanızı sağlamak için Azure Data Lake Analytics 'de U-SQL UDT ve UDADGG programlamasına ilişkin bilgi edinin.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 10fcce9a667d9a08318f5adab804f482387052ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97606666"
---
# <a name="u-sql-programmability-guide---udt-and-udagg"></a>U-SQL programlama kılavuzu-UDT ve UDADGG



## <a name="use-user-defined-types-udt"></a>Kullanıcı tanımlı türleri kullan: UDT
Kullanıcı tanımlı türler veya UDT, U-SQL ' n i n diğer bir programlama özelliğidir. U-SQL UDT, normal bir C# Kullanıcı tanımlı tür gibi davranır. C#, yerleşik ve özel kullanıcı tanımlı türlerin kullanılmasına izin veren, türü kesin belirlenmiş bir dildir.

U-SQL, bir UDT satır kümelerinde köşeler arasında geçirildiğinde rastgele olarak seri hale getirilemez veya seri durumdan çıkarılamıyor. Bu, kullanıcının IFormatter arabirimini kullanarak bir açık biçimlendirici sağlaması gerektiği anlamına gelir. Bu, UDT için serileştirme ve seri hale getirme yöntemleriyle U-SQL sağlar.

> [!NOTE]
> U-SQL ' n i n yerleşik ayıklayıcıları ve outputşu anda, ıbiçimlendirici kümesi ile birlikte, UDT verilerini veya dosyalarından seri hale getirilemez veya seri durumdan çıkarılamıyor. Bu nedenle, bir dosyayı çıkış bildirimiyle bir dosyaya yazarken veya bir ayıklayıcısı ile okurken, bunu bir dize veya bayt dizisi olarak geçirmeniz gerekir. Daha sonra serileştirme ve seri durumdan çıkarma kodunu (yani, UDT 'nin ToString () yöntemi) açık olarak çağırabilirsiniz. Diğer yandan Kullanıcı tanımlı ayıklayıcıları ve outputler, UDTs okuyup yazabilir.

UDT 'yi, (önceki SEÇIM dışında) EXTRACTOR veya OUTPUT' i kullanmaya çalışırsam, burada gösterildiği gibi:

```usql
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

Aşağıdaki hatayı alıyorsunuz:

```output
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

```output
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

1. Aşağıdaki ad alanlarını ekleyin:

```csharp
using Microsoft.Analytics.Interfaces
using System.IO;
```

2. `Microsoft.Analytics.Interfaces`Udt arabirimleri için gerekli olan öğesini ekleyin. Ayrıca, `System.IO` IFormatter arabirimini tanımlamak gerekebilir.

3. SqlUserDefinedType özniteliğiyle kullanılan tanımlı bir tür tanımlayın.

**SqlUserDefinedType** , bir derlemedeki tür tanımını U-SQL içinde Kullanıcı tanımlı tür (udt) olarak işaretlemek için kullanılır. Öznitelikteki özellikler, UDT 'nin fiziksel özelliklerini yansıtır. Bu sınıf devralınamaz.

SqlUserDefinedType, UDT tanımı için gerekli bir özniteliktir.

Sınıfının Oluşturucusu:  

* SqlUserDefinedTypeAttribute (tür biçimlendirici)

* Tür biçimlendirici: bir UDT biçimlendirici tanımlamak için gerekli parametre--özellikle `IFormatter` arabirimin türü geçirilmelidir.

```csharp
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* Tipik UDT, aşağıdaki örnekte gösterildiği gibi, IFormatter arabiriminin tanımını de gerektirir:

```csharp
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

`IFormatter`Arabirim, kök türü ile bir nesne grafiğini seri hale getirir ve serbest olarak serileştirir \<typeparamref name="T"> .

\<typeparam name="T">Serileştirme ve seri hale getirme için nesne grafiğinin kök türü.

* **Seri durumdan çıkarma**: verileri belirtilen akışta ve nesnelerin grafının reconstitutes seri hale getirir.

* **Serileştirme**: belirtilen akışa verilen köke sahip bir nesneyi veya nesne grafiğini seri hale getirir.

`MyType` Örnek: türünün örneği.  
`IColumnWriter` Yazıcı/ `IColumnReader` Okuyucu: temel alınan sütun akışı.  
`ISerializationContext` Bağlam: serileştirme sırasında akış için kaynak veya hedef bağlamını belirten bir bayrak kümesini tanımlayan sabit listesi.

* **Ara**: kaynak veya hedef bağlamın kalıcı bir depo olduğunu belirtir.

* **Kalıcılık**: kaynak veya hedef bağlamın kalıcı bir mağaza olduğunu belirtir.

Normal bir C# türü olarak, bir U-SQL UDT tanımı +/= =/! = gibi işleçler için geçersiz kılma işlemleri içerebilir. Statik yöntemler de içerebilir. Örneğin, bu UDT 'yi bir U-SQL MIN toplam işlevine parametre olarak kullanacaksanız, < işleci geçersiz kılmayı tanımlamanız gerekir.

Bu kılavuzda daha önce, biçimdeki belirli bir tarihten itibaren mali dönem tanımlama için bir örnek verilmiştir `Qn:Pn (Q1:P10)` . Aşağıdaki örnek, mali dönem değerleri için nasıl özel bir tür tanımlanacağını gösterir.

Özel UDT ve IFormatter arabirimi ile arka plan kod bölümünün bir örneği aşağıda verilmiştir:

```csharp
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

Tanımlı tür iki sayı içerir: çeyrek ve ay. Operators `==/!=/>/<` ve static yöntemi `ToString()` burada tanımlanmıştır.

Daha önce belirtildiği gibi, UDT SELECT ifadelerinde kullanılabilir, ancak özel serileştirme olmadan OUTPUTTER/EXTRACTOR içinde kullanılamaz. Ya da `ToString()` Özel BIR OUTPUTTER/Extractor ile kullanılan bir dize olarak serileştirilmelidir.

Şimdi UDT kullanımını tartışalım. Arka plan kod bölümünde, Getfcalperiod işlevimizi şu şekilde değiştirdik:

```csharp
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

```usql
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

```csharp
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
        }        [SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
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

```csharp
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

```csharp
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1: biriktirmek için Ilk parametre
* T2: biriktirmek için Ikinci parametre
* TResult: sonlandırma türü döndürme

Örnek:

```csharp
public class GuidAggregate : IAggregate<string, int, int>
```

veya

```csharp
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>U-SQL içinde UıDAVGG kullanma
UıLıGG 'yi kullanmak için, önce kodu arka planda tanımlayın veya daha önce anlatıldığı gibi var olan programlama DLL 'sinden başvuru yapın.

Ardından aşağıdaki sözdizimini kullanın:

```csharp
AGG<UDAGG_functionname>(param1,param2)
```

Aşağıda bir UBAGG örneği verilmiştir:

```csharp
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

```usql
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

## <a name="next-steps"></a>Sonraki adımlar
* [U-SQL programlama kılavuzu-genel bakış](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL programlama kılavuzu-UDO](data-lake-analytics-u-sql-programmability-guide-UDO.md)
