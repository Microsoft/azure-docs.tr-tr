---
title: İş akışı tanımı dil Şeması Başvurusu
description: Azure Logic Apps içindeki iş akışlarını açıklayan Iş akışı tanımlama dili için JSON şemasına yönelik başvuru kılavuzu ve sözdizimi
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 71929cd449f4a00b91cc6c8620b33b0e0c6d506c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87078151"
---
# <a name="schema-reference-guide-for-the-workflow-definition-language-in-azure-logic-apps"></a>Azure Logic Apps Iş akışı tanım dili için şema başvurusu Kılavuzu

[Azure Logic Apps](../logic-apps/logic-apps-overview.md)' de bir mantıksal uygulama oluşturduğunuzda, mantıksal uygulamanızda mantıksal uygulamanızda çalışan gerçek mantığı açıklayan bir temel alınan iş akışı tanımı vardır. Bu iş akışı tanımı [JSON](https://www.json.org/) kullanır ve Iş akışı Tanım Dili şeması tarafından doğrulanan bir yapıyı izler. Bu başvuru, bu yapıyla ilgili bir genel bakış ve şemanın iş akışı tanımınızda öznitelikleri nasıl tanımladığını sağlar.

## <a name="workflow-definition-structure"></a>İş akışı tanımı yapısı

Bir iş akışı tanımı her zaman mantıksal uygulamanızı başlatmak için bir tetikleyici ve tetikleyici başladıktan sonra çalışan bir veya daha fazla eylemi içerir.

İş akışı tanımı için üst düzey yapı aşağıda verilmiştir:

```json
"definition": {
  "$schema": "<workflow-definition-language-schema-version>",
  "actions": { "<workflow-action-definitions>" },
  "contentVersion": "<workflow-definition-version-number>",
  "outputs": { "<workflow-output-definitions>" },
  "parameters": { "<workflow-parameter-definitions>" },
  "staticResults": { "<static-results-definitions>" },
  "triggers": { "<workflow-trigger-definitions>" }
}
```

| Öznitelik | Gerekli | Açıklama |
|-----------|----------|-------------|
| `definition` | Evet | İş akışı tanımınızın başlangıç öğesi |
| `$schema` | Yalnızca bir iş akışı tanımına dışarıdan başvurulduklarında | Burada bulabileceğiniz Iş akışı tanımı dil sürümünü açıklayan JSON Şema dosyasının konumu: <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json`</p> |
| `actions` | No | İş akışı çalışma zamanı 'nda yürütülecek bir veya daha fazla eylemin tanımları. Daha fazla bilgi için bkz. [Tetikleyiciler ve eylemler](#triggers-actions). <p><p>En fazla eylem: 250 |
| `contentVersion` | No | Varsayılan olarak "1.0.0.0" olan iş akışı tanımınızın sürüm numarası. Bir iş akışını dağıtmada doğru tanımlamayı belirlemek ve doğrulamak için kullanılacak bir değer belirtin. |
| `outputs` | No | Bir iş akışı çalıştırağından döndürülecek çıktıların tanımları. Daha fazla bilgi için bkz. [çıktılar](#outputs). <p><p>Maksimum çıkış sayısı: 10 |
| `parameters` | No | Mantıksal uygulamanızın çalışma zamanında kullanılacak değerleri geçen bir veya daha fazla parametre için tanımlar. Daha fazla bilgi için bkz. [Parametreler](#parameters). <p><p>Maksimum Parametreler: 50 |
| `staticResults` | No | Eylemler tarafından, bir veya daha fazla statik sonucun tanımları, bu eylemlerde statik sonuçlar etkinleştirildiğinde, bir veya daha fazla statik sonuç tanımlar. Her eylem tanımında `runtimeConfiguration.staticResult.name` öznitelik, içindeki karşılık gelen tanımına başvurur `staticResults` . Daha fazla bilgi için bkz. [statik sonuçlar](#static-results). |
| `triggers` | No | İş akışınızı örnekleyip oluşturan bir veya daha fazla tetikleyici için tanımlar. Logic Apps Tasarımcısı aracılığıyla görsel olarak değil, yalnızca Iş akışı tanımlama dili ile birden fazla tetikleyici tanımlayabilirsiniz. Daha fazla bilgi için bkz. [Tetikleyiciler ve eylemler](#triggers-actions). <p><p>En fazla Tetikleyiciler: 10 |
||||

<a name="triggers-actions"></a>

## <a name="triggers-and-actions"></a>Tetikleyiciler ve eylemler

Bir iş akışı tanımında, `triggers` ve `actions` bölümleri iş akışınızın yürütülmesi sırasında gerçekleşen çağrıları tanımlar. Söz dizimi ve bu bölümler hakkında daha fazla bilgi için bkz. [Iş akışı Tetikleyicileri ve eylemleri](../logic-apps/logic-apps-workflow-actions-triggers.md).

<a name="parameters"></a>

## <a name="parameters"></a>Parametreler

Dağıtım yaşam döngüsü genellikle geliştirme, test, hazırlama ve üretim için farklı ortamlara sahiptir. Mantıksal uygulamaları çeşitli ortamlara dağıttığınızda, büyük ihtimalle dağıtım gereksinimlerinize göre bağlantı dizeleri gibi farklı değerler kullanmak istersiniz. Ya da, bir veya daha fazla değişiklik yapmadan mantıksal uygulamanız genelinde yeniden kullanmak istediğiniz değerlere sahip olabilirsiniz. İş akışı tanımınızın `parameters` bölümünde, mantıksal uygulamanızın çalışma zamanında kullandığı değerler için parametreleri tanımlayabilir veya düzenleyebilirsiniz. Bu parametreleri iş akışı tanımınızın başka bir yerinde kaydedebilmek için önce bu parametreleri tanımlamanız gerekir.

Bir parametre tanımının genel yapısı aşağıda verilmiştir:

```json
"parameters": {
   "<parameter-name>": {
      "type": "<parameter-type>",
      "defaultValue": <default-parameter-value>,
      "allowedValues": [ <array-with-permitted-parameter-values> ],
      "metadata": {
         "description": "<parameter-description>"
      }
   }
},
```

| Öznitelik | Gerekli | Tür | Description |
|-----------|----------|------|-------------|
| <*parametre-adı*> | Evet | Dize | Tanımlamak istediğiniz parametrenin adı |
| <*parametre türü*> | Yes | int, float, String, bool, Array, Object, SecureString, secureobject <p><p>**Note**: tüm parolalar, anahtarlar ve gizli diziler için, `securestring` veya türlerini kullanın `secureobject` çünkü `GET` işlem bu türleri döndürmez. Parametrelerin güvenliğini sağlama hakkında daha fazla bilgi için bkz. [eylem ve giriş parametreleri Için güvenlik önerileri](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters). | Parametrenin türü |
| <*Varsayılan parametre değeri*> | Yes | Aynı `type` | İş akışı örnekedildiğinde hiçbir değer belirtilmemişse kullanılacak varsayılan parametre değeri. `defaultValue`Mantıksal uygulama Tasarımcısı 'nın parametreyi doğru bir şekilde gösterebilmesi için özniteliği gereklidir, ancak boş bir değer belirtebilirsiniz. |
| <*dizi ile izin verilen-parametre değerleri*> | No | Dizi | Parametrenin kabul edebileceği değerleri içeren bir dizi |
| <*parametre-açıklama*> | No | JSON nesnesi | Parametresi için açıklama gibi diğer parametre ayrıntıları |
||||

Ardından, iş akışı tanımınız için bir [Azure Resource Manager şablonu](../azure-resource-manager/templates/overview.md) oluşturun, dağıtım sırasında istediğiniz değerleri kabul eden şablon parametrelerini tanımlayın, sabit kodlanmış değerleri şablon veya iş akışı Tanım parametrelerine başvurularla değiştirin ve ayrı bir [parametre dosyasında](../azure-resource-manager/templates/parameter-files.md)dağıtımda kullanılacak değerleri depolayın. Bu şekilde, mantıksal uygulamanızı güncelleştirip yeniden dağıtmanıza gerek kalmadan parametre dosyası aracılığıyla bu değerleri daha kolay bir şekilde değiştirebilirsiniz. Gizli olan veya güvenli hale getirilmesi gereken (örneğin, Kullanıcı adları, parolalar ve gizli bilgiler), bu değerleri Azure Key Vault saklayabilir ve parametre dosyanızın anahtar kasasından bu değerleri almasına sahip olursunuz. Şablon ve iş akışı Tanım düzeylerinde parametreleri tanımlama hakkında daha fazla bilgi ve örnekler için bkz. [genel bakış: Azure Resource Manager şablonlarıyla Logic Apps için dağıtımı otomatikleştirin](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md).

<a name="static-results"></a>

## <a name="static-results"></a>Statik sonuçlar

`staticResults`Özniteliğinde, bir eylemin sahte `outputs` olduğunu ve eylemin `status` statik sonuç ayarı açık olduğunda eylemin döndürdüğü eylemi tanımlayın. Eylemin tanımında `runtimeConfiguration.staticResult.name` öznitelik, içindeki statik sonuç tanımının adına başvurur `staticResults` . [Statik sonuçlar ayarlayarak, sahte verilerle mantıksal uygulamaları nasıl test](../logic-apps/test-logic-apps-mock-data-static-results.md)kullanabileceğinizi öğrenin.

```json
"definition": {
   "$schema": "<...>",
   "actions": { "<...>" },
   "contentVersion": "<...>",
   "outputs": { "<...>" },
   "parameters": { "<...>" },
   "staticResults": {
      "<static-result-definition-name>": {
         "outputs": {
            <output-attributes-and-values-returned>,
            "headers": { <header-values> },
            "statusCode": "<status-code-returned>"
         },
         "status": "<action-status>"
      }
   },
   "triggers": { "<...>" }
}
```

| Öznitelik | Gerekli | Tür | Description |
|-----------|----------|------|-------------|
| <*statik-sonuç tanımı-adı*> | Evet | Dize | Bir eylem tanımının bir nesne aracılığıyla başvurabileceğinden, statik sonuç tanımının adı `runtimeConfiguration.staticResult` . Daha fazla bilgi için bkz. [çalışma zamanı yapılandırma ayarları](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-config-options). <p>İstediğiniz herhangi bir benzersiz adı kullanabilirsiniz. Varsayılan olarak, bu benzersiz ad, gerektiği şekilde artan bir sayıyla eklenir. |
| <*çıkış-öznitelikler-ve-değerler-döndürülen*> | Yes | Değişir | Bu özniteliklerin gereksinimleri farklı koşullara göre farklılık gösterir. Örneğin, olduğunda, `status` öznitelik, `Succeeded` `outputs` eylem tarafından sahte çıktılar olarak döndürülen öznitelikleri ve değerleri içerir. `status`İse öznitelik, `Failed` `outputs` `errors` hata bilgilerine sahip bir veya daha fazla hata nesnesi olan bir dizi olan özniteliği içerir `message` . |
| <*üst bilgi-değerler*> | Hayır | JSON | Eylem tarafından döndürülen üst bilgi değerleri |
| <*durum-kod döndürüldü*> | Evet | Dize | Eylem tarafından döndürülen durum kodu |
| <*eylem-durum*> | Evet | Dize | Eylemin durumu, örneğin `Succeeded` veya `Failed` |
|||||

Örneğin, bu HTTP eylem tanımında `runtimeConfiguration.staticResult.name` öznitelik, `HTTP0` `staticResults` eylem için olan model çıkışları tanımlı özniteliğin içinde başvuru yapıyor. `runtimeConfiguration.staticResult.staticResultOptions`Özniteliği statik sonuç AYARıNıN `Enabled` http eyleminde olduğunu belirtir.

```json
"actions": {
   "HTTP": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.microsoft.com"
      },
      "runAfter": {},
      "runtimeConfiguration": {
         "staticResult": {
            "name": "HTTP0",
            "staticResultOptions": "Enabled"
         }
      },
      "type": "Http"
   }
},
```

HTTP eylemi, `HTTP0` içindeki tanımdaki çıkışları döndürür `staticResults` . Bu örnekte, durum kodu için, sahte çıktı olur `OK` . Üst bilgi değerleri için, sahte çıktı olur `"Content-Type": "application/JSON"` . Eylemin durumu için, sahte çıktı olur `Succeeded` .

```json
"definition": {
   "$schema": "<...>",
   "actions": { "<...>" },
   "contentVersion": "<...>",
   "outputs": { "<...>" },
   "parameters": { "<...>" },
   "staticResults": {
      "HTTP0": {
         "outputs": {
            "headers": {
               "Content-Type": "application/JSON"
            },
            "statusCode": "OK"
         },
         "status": "Succeeded"
      }
   },
   "triggers": { "<...>" }
},
```

<a name="expressions"></a>

## <a name="expressions"></a>İfadeler

JSON ile, tasarım zamanında mevcut olan değişmez değer değerlerine sahip olabilirsiniz, örneğin:

```json
"customerName": "Sophia Owen",
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"],
"rainbowColorsCount": 7
```

Ayrıca çalışma zamanına kadar mevcut olmayan değerlere sahip olabilirsiniz. Bu değerleri temsil etmek için, çalışma zamanında değerlendirilen *ifadeleri* kullanabilirsiniz. İfade bir veya daha fazla [işlev](#functions), [işleç](#operators), [değişken](./logic-apps-create-variables-store-values.md), açık değer veya sabitler içerebilen bir dizidir. İş akışı tanımınızda, ifadesi @ işareti () ile önek olarak ekleyerek JSON dize değerinde herhangi bir yerde bir ifadeyi kullanabilirsiniz \@ . JSON değerini temsil eden bir ifadeyi değerlendirirken, ifade gövdesi, karakter kaldırılarak ayıklanır \@ ve her zaman başka BIR JSON değeriyle sonuçlanır.

Örneğin, daha önce tanımlanmış `customerName` olan özelliği için, bir ifadede [Parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) işlevini kullanarak özellik değerini alabilir ve bu değeri `accountName` özelliğine atayabilirsiniz:

```json
"customerName": "Sophia Owen",
"accountName": "@parameters('customerName')"
```

*Dize ilişkilendirme* Ayrıca \@ karakter ve küme ayraçları () tarafından Sarmalanan dizeler içinde birden çok ifade kullanmanızı sağlar {} . Sözdizimi şöyledir:

```json
@{ "<expression1>", "<expression2>" }
```

Sonuç her zaman bir dizedir ve bu özellik işleve benzer hale getirir `concat()` , örneğin: 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

Karakteriyle başlayan bir sabit dize varsa \@ , \@ karakteri \@ Çıkış karakteri olarak başka bir karakterle önek olarak ekleyin: \@\@

Bu örnekler, ifadelerin nasıl değerlendirildiğini gösterir:

| JSON değeri | Sonuç |
|------------|--------|
| "Sophia Owen" | Şu karakterleri Döndür: ' Sophia Owen ' |
| "dizi [1]" | Şu karakterleri Döndür: ' Array [1] ' |
| "\@\@" | Bu karakterleri tek karakterli bir dize olarak Döndür: ' \@ ' |
| " \@" | Bu karakterleri iki karakterli bir dize olarak Döndür: ' \@ ' |
|||

Bu örnekler için, "Mydoğum ayı" ' nı "Ocak" ve "myAge" değerinden 42 numaraya eşit olarak tanımladığınızı varsayalım:

```json
"myBirthMonth": "January",
"myAge": 42
```

Bu örneklerde aşağıdaki ifadelerin nasıl değerlendirildiği gösterilmektedir:

| JSON ifadesi | Sonuç |
|-----------------|--------|
| " \@ Parameters (' Mydoğum ayı ')" | Bu dizeyi Döndür: "Ocak" |
| " \@ {Parameters (' Mydoğum ayı ')}" | Bu dizeyi Döndür: "Ocak" |
| " \@ Parameters (' myAge ')" | Bu sayıyı Döndür: 42 |
| " \@ {Parameters (' myAge ')}" | Bu sayıyı bir dize olarak Döndür: "42" |
| "Yaşım \@ {Parameters (' myAge ')}" | Bu dizeyi Döndür: "yaşımı 42" |
| " \@ Concat (' Age 'Im ', dize (Parametreler (' myAge ')))" | Bu dizeyi Döndür: "yaşımı 42" |
| "Yaşım \@ \@ {Parameters (' myAge ')}" | Şu ifadeyi içeren bu dizeyi döndürün: "Age My \@ {Parameters (' myAge ')} ' |
|||

Logic Apps tasarımcısında görsel olarak çalışırken, Ifade Oluşturucu aracılığıyla ifadeler oluşturabilirsiniz, örneğin:

![Logic Apps tasarımcı > Ifade Oluşturucusu](./media/logic-apps-workflow-definition-language/expression-builder.png)

İşiniz bittiğinde, ifadesi iş akışı tanımınızda karşılık gelen özellik için görünür, örneğin, `searchQuery` burada özelliği:

```json
"Search_tweets": {
  "inputs": {
    "host": {
      "connection": {
        "name": "@parameters('$connections')['twitter']['connectionId']"
      }
    }
  },
  "method": "get",
  "path": "/searchtweets",
  "queries": {
    "maxResults": 20,
    "searchQuery": "Azure @{concat('firstName','', 'LastName')}"
  }
},
```

<a name="outputs"></a>

## <a name="outputs"></a>Çıkışlar

`outputs`Bölümünde, iş akışınızın, çalışmayı bitirdiğinde döndürebileceğinizi belirten verileri tanımlayın. Örneğin, her çalıştırmada belirli bir durumu veya değeri izlemek için, iş akışı çıktısının bu verileri döndürdüğünü belirtin.

> [!NOTE]
> Bir hizmetin REST API gelen isteklere yanıt vermediğinde, kullanmayın `outputs` . Bunun yerine, `Response` eylem türünü kullanın. Daha fazla bilgi için bkz. [Iş akışı Tetikleyicileri ve eylemleri](../logic-apps/logic-apps-workflow-actions-triggers.md).

Bir çıkış tanımının genel yapısı aşağıda verilmiştir:

```json
"outputs": {
  "<key-name>": {
    "type": "<key-type>",
    "value": "<key-value>"
  }
}
```

| Öznitelik | Gerekli | Tür | Description |
|-----------|----------|------|-------------|
| <*anahtar adı*> | Evet | Dize | Çıkış dönüş değeri için anahtar adı |
| <*anahtar türü*> | Yes | int, float, String, SecureString, bool, Array, JSON nesnesi | Çıkış dönüş değeri için tür |
| <*anahtar-değer*> | Yes | <*anahtar türüyle* aynı> | Çıkış dönüş değeri |
|||||

Bir iş akışı çalıştırmasının çıkışını almak için Azure portal mantıksal uygulamanızın çalıştırma geçmişini ve ayrıntılarını gözden geçirin veya [Iş akışı REST API](/rest/api/logic/workflows)kullanın. Ayrıca, panoları oluşturabilmeniz için çıktıyı dış sistemlere geçirebilirsiniz (örneğin, Power BI).

<a name="operators"></a>

## <a name="operators"></a>İşleçler

[İfadeler](#expressions) ve [işlevlerde](#functions), işleçler bir özellik başvurusu veya dizideki bir değer gibi belirli görevleri gerçekleştirir.

| Operatör | Görev |
|----------|------|
| ' | Dize sabit değerini girdi olarak veya ifadeler ve işlevler içinde kullanmak için, dizeyi yalnızca tek tırnak işaretleriyle sarın, örneğin, `'<myString>'` . Bir ifadenin tamamında JSON biçimlendirmesiyle çakışan çift tırnak işaretleri ("") kullanmayın. Örnek: <p>**Evet**: Uzunluk (' Merhaba ') </br>**Hayır**: length ("Hello") <p>Dizileri veya sayıları geçirdiğinizde, kaydırma noktalaması gerekmez. Örnek: <p>**Evet**: Uzunluk ([1, 2, 3]) </br>**Hayır**: length ("[1, 2, 3]") |
| [] | Bir dizideki belirli bir konumdaki (Dizin) bir değere başvurmak için köşeli ayraçları kullanın. Örneğin, bir dizide ikinci öğeyi almak için: <p>`myArray[1]` |
| . | Bir nesnedeki bir özelliğe başvurmak için, nokta işlecini kullanın. Örneğin, `name` bir JSON nesnesinin özelliğini almak için `customer` : <p>`"@parameters('customer').name"` |
| ? | Çalışma zamanı hatası olmadan bir nesnedeki null özelliklere başvurmak için soru işareti işlecini kullanın. Örneğin, bir tetikleyiciden gelen null çıkışları işlemek için şu ifadeyi kullanabilirsiniz: <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` |
|||

<a name="functions"></a>

## <a name="functions"></a>İşlevler

Bazı ifadeler, iş akışı tanımınız çalışmaya başladığında henüz mevcut olmayan çalışma zamanı eylemlerinden değerlerini alır. Deyimlerdeki bu değerlere başvurmak veya bunlarla çalışmak için, Iş akışı tanımı dilinin sağladığı [*işlevleri*](../logic-apps/workflow-definition-language-functions-reference.md) kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Iş akışı tanımı dil eylemleri ve Tetikleyicileri](../logic-apps/logic-apps-workflow-actions-triggers.md) hakkında bilgi edinin
* [Iş akışı](/rest/api/logic/workflows) ile programlı bir şekilde mantıksal uygulamalar oluşturma ve yönetme hakkında bilgi edinin REST API
