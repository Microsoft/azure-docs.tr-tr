---
title: Azure Stream Analytics işleri için .NET Standard işlevleri geliştirme (Önizleme)
description: Stream Analytics işleri için C# Kullanıcı tanımlı işlevleri yazmayı öğrenin.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/10/2020
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 52fa6f05db5452a2e7b8ec4f93d65525873c8c7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020579"
---
# <a name="develop-net-standard-user-defined-functions-for-azure-stream-analytics-jobs-preview"></a>Azure Stream Analytics işleri için .NET Standard Kullanıcı tanımlı işlevler geliştirme (Önizleme)

Azure Stream Analytics, olay verilerinin akışları üzerinde dönüşümler ve hesaplamalar gerçekleştirmek için SQL benzeri bir sorgu dili sunar. Birçok yerleşik işlev vardır ancak bazı karmaşık senaryolar ek esneklik gerektirir. Kullanıcı tanımlı işlevler (UDF) .NET Standard sayesinde, Stream Analytics sorgu dilini genişletmek için herhangi bir .NET standart dilinde (C#, F #, vb.) yazılmış kendi işlevlerinizi çağırabilirsiniz. UDF 'ler, karmaşık matematik hesaplamaları gerçekleştirmenizi, ML.NET kullanarak özel ML modellerini içeri aktarmanızı ve eksik veriler için özel imputation mantığı kullanmanıza imkan tanır. Stream Analytics işleri için UDF özelliği şu anda önizlemededir ve üretim iş yükleri içinde kullanılmamalıdır.

Bulut işlerinin .NET Kullanıcı tanımlı işlevi, ' de kullanılabilir:
* Orta Batı ABD
* Kuzey Avrupa
* Doğu ABD
* Batı ABD
* Doğu ABD 2
* West Europe

Bu özelliği başka bir bölgede kullanmaya ilgileniyorsanız, [erişim isteyebilirsiniz](https://aka.ms/ccodereqregion).

## <a name="package-path"></a>Paket yolu

Herhangi bir UDF paketinin biçimi yolu vardır `/UserCustomCode/CLR/*` . Dinamik bağlantı kitaplıkları (dll 'Ler) ve kaynakları klasörü altına kopyalanır ve `/UserCustomCode/CLR/*` Bu, Kullanıcı dll 'lerinin sistem ve Azure Stream Analytics dll 'lerden yalıtılmasına yardımcı olur. Bu paket yolu, bunları kullanmak için kullanılan yöntemden bağımsız olarak tüm işlevler için kullanılır.

## <a name="supported-types-and-mapping"></a>Desteklenen türler ve eşleme

C# dilinde kullanılacak Azure Stream Analytics değerler için, bir ortamdan diğerine sıralanması gerekir. Bir UDF 'nin tüm giriş parametreleri için sıralama gerçekleşir. Her Azure Stream Analytics türü, aşağıdaki tabloda gösterilen C# dilinde karşılık gelen bir türe sahiptir:

|**Azure Stream Analytics türü** |**C# türü** |
|---------|---------|
|bigint | long |
|float | double |
|nvarchar (max) | string |
|datetime | DateTime |
|Kayıt | Sözlük\<string, object> |
|Dizi | Object [] |

Aynı durum, verilerin C# ' den Azure Stream Analytics, UDF 'nin çıkış değerinde meydana gelen olarak sıralanması gerektiğinde de geçerlidir. Aşağıdaki tabloda hangi türlerin desteklendiğini gösterilmektedir:

|**C# türü**  |**Azure Stream Analytics türü**  |
|---------|---------|
|long  |  bigint   |
|double  |  float   |
|string  |  nvarchar (max)   |
|DateTime  |  tarih saat   |
|struct  |  Kayıt   |
|object  |  Kayıt   |
|Object []  |  Dizi   |
|Sözlük\<string, object>  |  Kayıt   |

## <a name="develop-a-udf-in-visual-studio-code"></a>Visual Studio Code 'da UDF geliştirin

[Azure Stream Analytics için Visual Studio Code araçları](quick-create-visual-studio-code.md) , UDF 'leri yazmanızı, işlerinizi yerel olarak test etme (çevrimdışı) ve Stream Analytics işinizi Azure 'da yayımlamayı kolaylaştırır.

Visual Studio Code araçlarında .NET Standard UDF 'Leri uygulamak için iki yol vardır.

* Yerel dll 'lerden UDF
* Yerel projeden UDF

### <a name="local-project"></a>Yerel proje

Kullanıcı tanımlı işlevler, daha sonra bir Azure Stream Analytics sorgusunda başvurulan bir derlemede yazılabilir. Bu, .NET Standard dilinin, yordamsal mantık veya özyineleme gibi ifade dilinin ötesinde tam gücünü gerektiren karmaşık işlevler için önerilen seçenektir. Yerel bir projeden gelen UDF 'ler, birkaç Azure Stream Analytics sorgu arasında işlev mantığını paylaşmanız gerektiğinde de kullanılabilir. Yerel projenize UDF 'ler eklemek, işlevlerinizi yerel olarak hata ayıklama ve test etme olanağı sunar.

Yerel bir projeye başvurmak için:

1. Yerel makinenizde yeni bir .NET standart sınıf kitaplığı oluşturun.
2. Kodunuzu sınıfınıza yazın. Sınıfların *ortak* olarak tanımlanması gerektiğini ve nesnelerin *statik ortak* olarak tanımlanması gerektiğini unutmayın.
3. Azure Stream Analytics projenize yeni bir CSharp Function yapılandırma dosyası ekleyin ve CSharp Class Kitaplığı projesine başvurun.
4. Derleme yolunu iş yapılandırma dosyasında, `JobConfig.json` **customcodestorage** bölümünde yapılandırın. Bu adım yerel test için gerekli değildir.

### <a name="local-dlls"></a>Yerel dll 'Ler

Kullanıcı tanımlı işlevleri de içeren yerel dll 'Lere de başvurabilirsiniz.

### <a name="example"></a>Örnek

Bu örnekte, **csharpudfproject** bir C# sınıf kitaplığı projem ve **Asaudfdemo** , **csharpudfproject**'e başvuracak Azure Stream Analytics projem.

:::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-demo.png" alt-text="Visual Studio Code Azure Stream Analytics proje":::

Aşağıdaki UDF, tamsayının karesini oluşturmak için bir tamsayıyı kendisi ile çarpar bir işleve sahiptir. Sınıfların *ortak* olarak tanımlanması ve nesnelerin *statik ortak* olarak tanımlanması gerekir.

```csharp
using System;

namespace CSharpUDFProject
{
    // 
    public class Class1
    {
        public static Int64 SquareFunction(Int64 a)
        {
            return a * a;
        }
    }
}
```

Aşağıdaki adımlarda Stream Analytics projenize C# UDF işlevi ekleme işlemi gösterilmektedir.

1. **İşlevler** klasörüne sağ tıklayın ve **öğe Ekle**' yi seçin.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-add-function.png" alt-text="Azure Stream Analytics projesine yeni işlev Ekle":::

2. Azure Stream Analytics projenize bir C# işlevi **squarefunction** ekleyin.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-add-function-2.png" alt-text="VS Code Stream Analytics projeden CSharp Function 'ı seçin":::

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-add-function-name.png" alt-text="VS Code CSharp işlev adını girin":::

3. C# işlev yapılandırmasında, açılan listeden C# projenizi seçmek için **kitaplık proje yolu seç** ' i seçin ve projenizi derlemek Için **projeyi oluştur** ' u seçin. Ardından, **Sınıf Seç** ' i seçin ve açılan listeden ilgili sınıf ve yöntem adını seçmek için yöntem ' i **seçin** . Stream Analytics sorgusundaki yöntemlere, türlere ve işlevlere başvurmak için sınıfların *ortak* olarak tanımlanması ve nesnelerin *statik ortak* olarak tanımlanması gerekir.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-choose-project.png" alt-text="Stream Analytics C diyez işlevi yapılandırma VS Code":::

    Bir DLL 'den C# UDF kullanmak istiyorsanız, DLL 'yi seçmek için **kitaplık dll yolu seç** ' i seçin. Ardından, **Sınıf Seç** ' i seçin ve açılan listeden ilgili sınıf ve yöntem adını seçmek için yöntem ' i **seçin** .

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-choose-dll.png" alt-text="Stream Analytics C Sharp işlev yapılandırması":::

4. Azure Stream Analytics sorgunuzda UDF 'yi çağırın.

   ```sql
    SELECT price, udf.SquareFunction(price)
    INTO Output
    FROM Input 
   ```

5. İşi Azure 'a göndermeden önce, iş yapılandırma dosyasında, `JobConfig.json` **customcodestorage** bölümünde paket yolunu yapılandırın. Aboneliğinizi seçmek ve açılan listeden depolama hesabı ve kapsayıcı adı ' nı seçmek için CodeLens 'teki **aboneliğinizden Seç** ' i kullanın. **Yolu** varsayılan olarak bırakın. Bu adım yerel test için gerekli değildir.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-configure-storage-account.png" alt-text="Kitaplık yolu seçin":::

## <a name="develop-a-udf-in-visual-studio"></a>Visual Studio 'da UDF geliştirme

Visual Studio araçlarında UDF 'Leri uygulamak için üç yol vardır.

* Bir ASA projesindeki CodeBehind dosyaları
* Yerel projeden UDF
* Azure Storage hesabından mevcut bir paket

### <a name="codebehind"></a>CodeBehind

Kullanıcı tanımlı işlevleri **Script. ASQL** codebehind içinde yazabilirsiniz. Visual Studio Araçları otomatik olarak CodeBehind dosyasını derleme dosyasına derler. Derlemeler bir zip dosyası olarak paketlenir ve işinizi Azure 'a gönderdiğinizde depolama hesabınıza yüklenir. [Stream Analytics Edge işleri öğreticisi Için C# UDF](stream-analytics-edge-csharp-udf.md) 'Yi izleyerek codebehind kullanarak c# UDF yazma hakkında bilgi edinebilirsiniz. 

### <a name="local-project"></a>Yerel proje

Visual Studio 'da yerel bir projeye başvurmak için:

1. Çözümünüzde yeni bir .NET Standard sınıf kitaplığı oluşturun
2. Kodunuzu sınıfınıza yazın. Sınıfların *ortak* olarak tanımlanması gerektiğini ve nesnelerin *statik ortak* olarak tanımlanması gerektiğini unutmayın. 
3. Projenizi yapılandırın. Araçlar bin klasöründeki tüm yapıtları bir ZIP dosyasına paketler ve ZIP dosyasını depolama hesabına yükler. Dış başvurular için NuGet paketi yerine derleme başvurusunu kullanın.
4. Azure Stream Analytics projenizdeki yeni sınıfa başvurun.
5. Azure Stream Analytics projenize yeni bir işlev ekleyin.
6. Derleme yolunu iş yapılandırma dosyasında yapılandırın `JobConfig.json` . Derleme yolunu **Yerel proje başvurusu veya codebehind** olarak ayarlayın.
7. Hem işlev projesini hem de Azure Stream Analytics projeyi yeniden derleyin.  

### <a name="example"></a>Örnek

Bu örnekte, **udftest** bir C# sınıf kitaplığı projem ve **Asaudfdemo** , **udftest**'e başvuracaktır Azure Stream Analytics projem.

:::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png" alt-text="Visual Studio 'da IoT Edge Azure Stream Analytics projesi":::

1. C# projenizi oluşturun, bu, Azure Stream Analytics sorgusundan C# UDF 'nize bir başvuru eklemenize olanak sağlar.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png" alt-text="Visual Studio 'da Azure Stream Analytics IoT Edge projesi oluşturma":::

2. Başvuruyu ASA projesindeki C# projesine ekleyin. Başvurular düğümüne sağ tıklayın ve başvuru Ekle ' yi seçin.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png" alt-text="Visual Studio 'da C# projesine başvuru ekleme":::

3. Listeden C# proje adını seçin.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png" alt-text="Başvuru listesinden C# proje adınızı seçin":::

4. **Çözüm Gezgini** **Başvurular** altında listelenen **udftest** ' i görmeniz gerekir.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png" alt-text="Çözüm Gezgini 'nde Kullanıcı tanımlı işlev başvurusunu görüntüleme":::

5. **İşlevler** klasörüne sağ tıklayın ve **Yeni öğe**' yi seçin.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png" alt-text="Azure Stream Analytics Edge çözümünde IŞLEVLERE yeni öğe ekleme":::

6. Azure Stream Analytics projenize bir C# işlevi **SquareFunction.js** ekleyin.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png" alt-text="Visual Studio 'da Stream Analytics Edge öğelerinden CSharp işlevi seçme":::

7. Yapılandırma iletişim kutusunu açmak için **Çözüm Gezgini** işlevine çift tıklayın.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png" alt-text="Visual Studio 'da C Sharp işlev yapılandırması":::

8. C# işlev yapılandırmasında, **asa proje başvurusundan yükle** ' yi ve açılan listeden ilgili derleme, sınıf ve yöntem adlarını seçin. Stream Analytics sorgusundaki yöntemlere, türlere ve işlevlere başvurmak için sınıfların *ortak* olarak tanımlanması ve nesnelerin *statik ortak* olarak tanımlanması gerekir.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png" alt-text="Stream Analytics C Sharp işlev yapılandırması Visual Studio":::

## <a name="existing-packages"></a>Mevcut paketler

.NET Standard UDF 'Leri dilediğiniz IDE 'de yazabilir ve Azure Stream Analytics sorgınızdan çağırabilirsiniz. İlk olarak kodunuzu derleyin ve tüm dll 'Leri paketleyin. Paketin biçimi yolu vardır `/UserCustomCode/CLR/*` . Ardından, `UserCustomCode.zip` Azure Depolama hesabınızdaki kapsayıcının köküne yükleyin.

Azure depolama hesabınıza derleme ZIP paketleri yüklendikten sonra, Azure Stream Analytics sorgulardaki işlevleri kullanabilirsiniz. Tüm yapmanız gereken Stream Analytics iş yapılandırmasındaki depolama bilgilerini içerir. Visual Studio Araçları paketinizi indirmediği için işlevi bu seçenekle yerel olarak test edebilirsiniz. Paket yolu doğrudan hizmete ayrıştırılır. 

İş yapılandırma dosyasında derleme yolunu yapılandırmak için `JobConfig.json` :

**Kullanıcı Tanımlı Kod Yapılandırması** bölümünü genişletin ve yapılandırmaya aşağıdaki önerilen değerleri ekleyin:

   |**Ayar**|**Önerilen değer**|
   |-------|---------------|
   |Genel depolama ayarları kaynağı|Geçerli hesaptaki veri kaynağını seçin|
   |Genel depolama ayarları aboneliği| Aboneliğinizi < >|
   |Genel depolama ayarları depolama hesabı| Depolama hesabınızı < >|
   |Özel kod depolama ayarları kaynağı|Geçerli hesaptaki veri kaynağını seçin|
   |Özel kod depolama ayarları depolama hesabı|Depolama hesabınızı < >|
   |Özel kod depolama ayarları kapsayıcısı|Depolama kapsayıcınızı < >|
   |Özel kod derleme kaynağı|Buluttan mevcut derleme paketleri|
   |Özel kod derleme kaynağı|UserCustomCode.zip|

## <a name="user-logging"></a>Kullanıcı günlüğü

Günlüğe kaydetme mekanizması, bir iş çalışırken özel bilgileri yakalamanızı sağlar. Hata ayıklamak veya gerçek zamanlı olarak özel kodun doğruluğunu değerlendirmek için günlük verilerini kullanabilirsiniz.

`StreamingContext`Sınıfı, işlevini kullanarak tanılama bilgilerini yayımlamanıza olanak sağlar `StreamingDiagnostics.WriteError` . Aşağıdaki kodda Azure Stream Analytics tarafından sunulan arabirim gösterilmektedir.

```csharp
public abstract class StreamingContext
{
    public abstract StreamingDiagnostics Diagnostics { get; }
}

public abstract class StreamingDiagnostics
{
    public abstract void WriteError(string briefMessage, string detailedMessage);
}
```

`StreamingContext` , UDF yöntemine bir giriş parametresi olarak geçirilir ve özel günlük bilgilerini yayımlamak için UDF içinde kullanılabilir. Aşağıdaki örnekte, `MyUdfMethod` sorgu tarafından belirtilen bir **veri** girişi ve  `StreamingContext` çalışma zamanı altyapısı tarafından sağlanmış olan olarak bir bağlam girişi tanımlanmaktadır. 

```csharp
public static long MyUdfMethod(long data, StreamingContext context)
{
    // write log
    context.Diagnostics.WriteError("User Log", "This is a log message");
    
    return data;
}
```

`StreamingContext`DEĞERIN SQL sorgusu tarafından geçirilmesi gerekmez. Azure Stream Analytics bir giriş parametresi varsa otomatik olarak bir bağlam nesnesi sağlar. `MyUdfMethod`Aşağıdaki sorguda gösterildiği gibi, öğesinin kullanımı değişmez:

```sql
SELECT udf.MyUdfMethod(input.value) as udfValue FROM input
```

Günlük iletilerine [tanılama günlükleri](data-errors.md)aracılığıyla erişebilirsiniz.

## <a name="limitations"></a>Sınırlamalar

UDF önizlemesi Şu anda aşağıdaki sınırlamalara sahiptir:

* .NET Standard UDF 'ler, yalnızca Visual Studio Code veya Visual Studio 'da yazılabilir ve Azure 'da yayımlanabilir. .NET Standard UDF 'Leri salt okuma sürümleri Azure portal **işlevler** altında görüntülenebilir. .NET Standard işlevlerinin yazılması Azure portal desteklenmez.

* Azure portal sorgu Düzenleyicisi portalda .NET Standard UDF kullanırken bir hata gösterir. 

* Özel kod Azure Stream Analytics altyapısı ile bağlam paylaştığından, özel kod Azure Stream Analytics kodla çakışan bir ad alanı/dll_name olan herhangi bir şeye başvuramaz. Örneğin, *Newtonsoft JSON* öğesine başvurulamıyor.

* Projeye dahil olan destekleyici dosyalar, işi buluta yayımladığınızda kullanılan Kullanıcı özel kod ZIP dosyasına kopyalanır. Alt klasörlerdeki tüm dosyalar, daraltılmış durumda, bulutta bulunan Kullanıcı özel kod klasörünün köküne doğrudan kopyalanır. ZIP açıldığında "düzleştirilmiş" olur.

* Kullanıcı özel kodu boş klasörleri desteklemez. Projedeki destekleyici dosyalara boş klasörler eklemeyin.

## <a name="next-steps"></a>Sonraki adımlar

* [Öğretici: Azure Stream Analytics işi için C# Kullanıcı tanımlı bir işlev yazma (Önizleme)](stream-analytics-edge-csharp-udf.md)
* [Öğretici: Azure Stream Analytics JavaScript kullanıcı tanımlı işlevleri](stream-analytics-javascript-user-defined-functions.md)
* [Visual Studio Code Azure Stream Analytics iş oluşturma](quick-create-visual-studio-code.md)