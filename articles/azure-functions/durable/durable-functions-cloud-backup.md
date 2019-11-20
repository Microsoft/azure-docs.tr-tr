---
title: Dayanıklı İşlevler-Azure 'da fan-çıkış/fan senaryoları
description: Azure Işlevleri için Dayanıklı İşlevler uzantısında bir fanı-Out senaryosu uygulamayı öğrenin.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: e2f1042fe1210fe51ae79b1152e51191e7fb066a
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615023"
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Dayanıklı İşlevler-bulut yedekleme örneğinde fan-çıkış/fan senaryosu

*Fanı-Out/fanı* , birden fazla işlevi aynı anda yürütmenin ve sonra sonuçlar üzerinde bazı toplamalar gerçekleştiren bir düzene başvurur. Bu makalede, bir fan/fan senaryosu uygulamak için [dayanıklı işlevler](durable-functions-overview.md) kullanan bir örnek açıklanmaktadır. Örnek, bir uygulamanın tüm site içeriğini Azure depolama 'ya yedekleyen dayanıklı bir işlevdir.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Senaryoya genel bakış

Bu örnekte, işlevler belirtilen bir dizin altındaki tüm dosyaları yinelemeli olarak BLOB depolama alanına yükler. Ayrıca, karşıya yüklenen toplam bayt sayısını da sayar.

Her şeyi ele alan tek bir işlev yazmak mümkündür. Çalıştırmak istediğiniz ana sorun **ölçeklenebilirlik**. Tek bir işlev yürütmesi yalnızca tek bir VM üzerinde çalışabilir, bu yüzden üretilen iş bu tek VM 'nin verimlilik ile sınırlandırılır. Başka bir sorun **güvenilir**. Hata geçişli bir hata oluşursa veya işlemin tamamı 5 dakikadan fazla sürerse, yedekleme kısmen tamamlanmış durumda başarısız olabilir. Daha sonra yeniden başlatılması gerekir.

Daha sağlam bir yaklaşım, iki normal işlev yazmak olacaktır: biri, dosyaları numaralandırırın ve dosya adlarını bir kuyruğa ekleyecek ve başka bir şekilde kuyruktan yazacak ve dosyaları blob depolamaya yükleyecek. Bu yaklaşım verimlilik ve güvenilirlik açısından daha iyidir, ancak bir kuyruğu sağlamanızı ve yönetmenizi gerektirir. Daha önemlisi, **durum yönetimi** ve **koordinasyonuna** göre daha da fazla bir işlem yapmak istiyorsanız, karşıya yüklenen toplam bayt sayısını raporla gibi önemli karmaşıklıklar sunulmuştur.

Dayanıklı İşlevler bir yaklaşım, tüm bahsedilen avantajları çok düşük ek yük ile sunar.

## <a name="the-functions"></a>İşlevler

Bu makalede örnek uygulamada aşağıdaki işlevler açıklanmaktadır:

* `E2_BackupSiteContent`
* `E2_GetFileList`
* `E2_CopyFileToBlob`

Aşağıdaki bölümlerde, komut dosyası oluşturma için C# kullanılan yapılandırma ve kod açıklanmaktadır. Visual Studio geliştirme kodu makalenin sonunda gösterilmektedir.

## <a name="the-cloud-backup-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>Bulut yedekleme düzenlemesi (Visual Studio Code ve Azure portal örnek kodu)

`E2_BackupSiteContent` işlevi Orchestrator işlevleri için standart *function. JSON* ' i kullanır.

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/function.json)]

Orchestrator işlevini uygulayan kod aşağıda verilmiştir:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/run.csx)]

### <a name="javascript-functions-20-only"></a>JavaScript (yalnızca Işlevler 2,0)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

Bu Orchestrator işlevi temelde şunları yapar:

1. Bir `rootDirectory` değerini giriş parametresi olarak alır.
2. `rootDirectory`altındaki dosyaların özyinelemeli listesini almak için bir işlev çağırır.
3. Her bir dosyayı Azure Blob depolama alanına yüklemek için birden çok paralel işlev çağrısı yapar.
4. Tüm karşıya yüklemelerinin tamamlanmasını bekler.
5. Azure Blob depolamaya karşıya yüklenen toplam bayt sayısını döndürür.

`await Task.WhenAll(tasks);` (C#) ve `yield context.df.Task.all(tasks);` (JavaScript) satırlarına dikkat edin. `E2_CopyFileToBlob` işlevine yapılan tüm çağrılar *beklenmez* , bu da paralel çalışmasına izin verir. Bu görev dizisini `Task.WhenAll` (C#) veya `context.df.Task.all` (JavaScript) öğesine geçirdiğimiz zaman, *tüm kopyalama işlemleri tamamlanana kadar*tamamlanmamış bir görevi geri alırız. .NET veya JavaScript 'te [`Promise.all`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) görev paralel KITAPLıĞı (TPL) hakkında bilginiz varsa, bu sizin için yeni değildir. Fark, bu görevlerin eşzamanlı olarak birden çok VM üzerinde çalışabilmesidir ve Dayanıklı İşlevler uzantısı uçtan uca yürütmenin geri dönüşüm işleminin işlenmesine dayanıklı olmasını sağlar.

> [!NOTE]
> Görevler kavramsal olarak JavaScript 'e benzer olsa da, Orchestrator işlevlerinin görev paralel hale getirme yönetimi için `Promise.all` ve `Promise.race` yerine `context.df.Task.all` ve `context.df.Task.any` kullanması gerekir.

`Task.WhenAll` 'tan (veya `context.df.Task.all`) çalıştıktan sonra, tüm işlev çağrılarının tamamlandığını ve bize değer döndürmüş olduğunu biliyoruz. `E2_CopyFileToBlob` yapılan her bir çağrı, karşıya yüklenen bayt sayısını döndürür, bu nedenle toplam bayt sayısını hesaplamak, bu dönüş değerlerinin tümünü birlikte eklemenin bir önemi olur.

## <a name="helper-activity-functions"></a>Yardımcı etkinlik işlevleri

Yardımcı etkinlik işlevleri, diğer örneklerde olduğu gibi, yalnızca `activityTrigger` tetikleyici bağlamayı kullanan normal işlevlerdir. Örneğin, `E2_GetFileList` için *function. JSON* dosyası aşağıdaki gibi görünür:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/function.json)]

Uygulama şu şekildedir:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/run.csx)]

### <a name="javascript-functions-20-only"></a>JavaScript (yalnızca Işlevler 2,0)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

`E2_GetFileList` JavaScript uygulanması, dizin yapısını yinelemeli olarak okumak için `readdirp` modülünü kullanır.

> [!NOTE]
> Bu kodu neden henüz Orchestrator işlevine koyacağınızı merak ediyor olabilirsiniz. Ancak bu, Orchestrator işlevlerinin temel kurallarından birini bozacağından, yerel dosya sistemi erişimi de dahil olmak üzere hiç g/ç yapmam gerekir.

`E2_CopyFileToBlob` için *function. JSON* dosyası benzer bir işlemdir:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/function.json)]

C# Uygulama da basit bir işlemdir. Azure Işlevleri bağlamalarının bazı gelişmiş özelliklerini (yani `Binder` parametresinin kullanımı) kullanır, ancak bu izlenecek yol için bu ayrıntılar konusunda endişelenmeniz gerekmez.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/run.csx)]

### <a name="javascript-functions-20-only"></a>JavaScript (yalnızca Işlevler 2,0)

JavaScript uygulamasının Azure Işlevlerinin `Binder` özelliğine erişimi yoktur, bu nedenle [düğüm Için Azure depolama SDK 'sının](https://github.com/Azure/azure-storage-node) yerini alır.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

Uygulama, dosyayı diskten yükler ve içeriği zaman uyumsuz olarak "yedeklemeler" kapsayıcısında aynı ada sahip bir bloba akıtır. Dönüş değeri, depolamaya kopyalanmış baytların sayısıdır, daha sonra Orchestrator işlevi tarafından toplam toplamı hesaplamak için kullanılır.

> [!NOTE]
> Bu, g/ç işlemlerini `activityTrigger` işleve taşımaya yönelik kusursuz bir örnektir. İş yalnızca birçok farklı VM 'ye dağıtılmayabilir, ancak ilerlemeyi işaret eden bir şekilde gösterme avantajlarına da ulaşabilirsiniz. Ana bilgisayar işlemi herhangi bir nedenle sonlandırılırsa, hangi karşıya yüklemelerinin zaten tamamlandığını bilirsiniz.

## <a name="run-the-sample"></a>Örneği çalıştırma

Aşağıdaki HTTP POST isteğini göndererek düzenleme işlemini başlatabilirsiniz.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

> [!NOTE]
> Çağırdığınız `HttpStart` işlevi yalnızca JSON biçimli içerikle çalışır. Bu nedenle `Content-Type: application/json` üst bilgisi gereklidir ve dizin yolu bir JSON dizesi olarak kodlanır. Ayrıca, HTTP kod parçacığı, `host.json` dosyasında, tüm HTTP tetikleyici işlevleri URL 'Lerinden varsayılan `api/` önekini kaldıran bir giriş olduğunu varsayar. Bu yapılandırmanın işaretlemesini, örneklerdeki `host.json` dosyasında bulabilirsiniz.

Bu HTTP isteği `E2_BackupSiteContent` Orchestrator 'ı tetikler ve dize `D:\home\LogFiles` bir parametre olarak geçirir. Yanıt, yedekleme işleminin durumunu almak için bir bağlantı sağlar:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

İşlev uygulamanızda kaç günlük dosyasına sahip olduğunuza bağlı olarak, bu işlemin tamamlanması birkaç dakika sürebilir. Önceki HTTP 202 yanıtının `Location` üstbilgisindeki URL 'YI sorgulayarak en son durumu alabilirsiniz.

```
GET http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 202 Accepted
Content-Length: 148
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":"D:\\home\\LogFiles","output":null,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:16Z"}
```

Bu durumda, işlev hala çalışıyor olur. Orchestrator durumuna kaydedilen girişi ve son güncelleme saatini görebilirsiniz. Tamamlamayı yoklamak için `Location` üst bilgi değerlerini kullanmaya devam edebilirsiniz. Durum "tamamlandı" olduğunda aşağıdakine benzer bir HTTP yanıt değeri görürsünüz:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:26Z"}
```

Şimdi Orchestration 'un tamamlandığını ve tamamlanması için ne kadar zaman tamamlandığını görebilirsiniz. Ayrıca, `output` alanı için bir değer görürsünüz. Bu, 450 KB 'lık günlüklerin karşıya yüklendiğini gösterir.

## <a name="visual-studio-sample-code"></a>Visual Studio örnek kodu

Visual Studio projesindeki tek C# bir dosya olarak Orchestration aşağıda verilmiştir:

> [!NOTE]
> Aşağıdaki örnek kodu çalıştırmak için `Microsoft.Azure.WebJobs.Extensions.Storage` NuGet paketini yüklemeniz gerekir.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs)]

## <a name="next-steps"></a>Sonraki adımlar

Bu örnek, fan/fan deseninin nasıl uygulanacağını göstermiştir. Sonraki örnek, [dayanıklı zamanlayıcılar](durable-functions-timers.md)kullanılarak izleyici deseninin nasıl uygulanacağını gösterir.

> [!div class="nextstepaction"]
> [İzleme örneğini çalıştırma](durable-functions-monitor.md)