---
title: Kod parçacıkları ekleme ve çalıştırma-Azure Logic Apps
description: Azure Logic Apps satır içi kodla kod parçacıkları ekleme ve çalıştırma
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: derek1ee, LADocs
ms.topic: article
ms.date: 05/14/2019
ms.openlocfilehash: 3b51215e0cf48df2d3cd9df85a3d4c5641a17215
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390798"
---
# <a name="add-and-run-code-snippets-by-using-inline-code-in-azure-logic-apps"></a>Azure Logic Apps içinde satır içi kod kullanarak kod parçacıkları ekleme ve çalıştırma

Mantıksal uygulamanızın içinde kod parçası çalıştırmak istediğinizde, yerleşik **satır Içi kod** eylemini mantıksal uygulamanızın iş akışına bir adım olarak ekleyebilirsiniz. Bu eylem, bu senaryoya uyan kodu çalıştırmak istediğinizde en iyi şekilde çalışır:

* JavaScript 'te çalışır. Yakında daha fazla dil geliyor.
* Beş saniye veya daha kısa bir süre içinde çalışmayı sonlandırır.
* Boyutu 50 MB 'a kadar olan verileri işler.
* Henüz desteklenmeyen [ **değişken** eylemlerle](../logic-apps/logic-apps-create-variables-store-values.md)çalışmayı gerektirmez.
* Node. js sürüm 8.11.1 kullanır. Daha fazla bilgi için bkz. [Standart yerleşik nesneler](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects). 

  > [!NOTE]
  > İşlev, JavaScript çalıştırmak için **satır içi kod** eylemi tarafından desteklenmiyor. `require()`

Bu eylem, kod parçacığını çalıştırır ve mantıksal uygulamanızdaki sonraki eylemlerde kullanabileceğiniz **Result**adlı bir belirteç olarak bu kod parçacığındaki çıktıyı döndürür. Kodunuz için bir işlev oluşturmak istediğiniz diğer senaryolar için, mantıksal uygulamanızda [bir Azure işlevi oluşturmayı ve çağırmayı](../logic-apps/logic-apps-azure-functions.md) deneyin.

Bu makalede, bir Office 365 Outlook hesabına yeni bir e-posta geldiğinde örnek mantıksal uygulama tetiklenir. Kod parçacığı, e-posta gövdesinde görünen tüm e-posta adreslerini ayıklar ve döndürür.

![Örneğe genel bakış](./media/logic-apps-add-run-inline-code/inline-code-example-overview.png)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* Tetikleyici dahil olmak üzere kod parçacığınızı eklemek istediğiniz mantıksal uygulama. Mantıksal uygulamanız yoksa bkz [. hızlı başlangıç: İlk mantıksal uygulamanızı](../logic-apps/quickstart-create-first-logic-app-workflow.md)oluşturun.

   Bu konudaki örnek mantık uygulaması bu Office 365 Outlook tetikleyicisini kullanır: **Yeni bir e-posta geldiğinde**

* Mantıksal uygulamanıza bağlı bir [tümleştirme hesabı](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

  > [!NOTE]
  > Kullanım örneği veya senaryonuz için uygun bir tümleştirme hesabı kullandığınızdan emin olun. Örneğin, [ücretsiz katman](../logic-apps/logic-apps-pricing.md#integration-accounts) tümleştirme hesapları yalnızca araştırmacı senaryolar ve iş yükleri için, üretim senaryolarında değil, kullanım ve verimlilik açısından sınırlı ve hizmet düzeyi SÖZLEŞMESI (SLA) tarafından desteklenmemektedir. Diğer katmanlar maliyetlere tabi olur, ancak SLA desteğini içerir, daha fazla verimlilik sunar ve daha yüksek sınırlara sahiptir. Tümleştirme hesabı [katmanları](../logic-apps/logic-apps-pricing.md#integration-accounts), [fiyatlandırma](https://azure.microsoft.com/pricing/details/logic-apps/)ve [sınırlamalar](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)hakkında daha fazla bilgi edinin.

## <a name="add-inline-code"></a>Satır içi kod Ekle

1. Henüz yapmadıysanız, [Azure Portal](https://portal.azure.com)mantıksal uygulama tasarımcısında mantıksal uygulamanızı açın.

1. Tasarımcıda, mantıksal uygulamanızın iş akışında istediğiniz konuma **satır Içi kod** eylemini ekleyin.

   * Eylemi iş akışınızın sonunda eklemek için **yeni adım**' ı seçin.

   * Varolan adımlar arasında eylem eklemek için fare işaretçinizi bu adımları bağlayan okun üzerine taşıyın. Artı işaretini ( **+** ) seçin ve **Eylem Ekle**' yi seçin.

   Bu örnek, Office 365 Outlook tetikleyicisinin altındaki **satır Içi kod** eylemini ekler.

   ![Yeni adım Ekle](./media/logic-apps-add-run-inline-code/add-new-step.png)

1. **Eylem seçin**altında, arama kutusuna filtreniz olarak "satır içi kod" yazın. Eylemler listesinden şu eylemi seçin: **JavaScript kodunu Yürüt**

   !["JavaScript kodunu Yürüt" i seçin](./media/logic-apps-add-run-inline-code/select-inline-code-action.png)

   Eylem tasarımcıda görünür ve dönüş ifadesi dahil olmak üzere bazı varsayılan örnek kod içerir.

   ![Varsayılan örnek kodlu satır içi kod eylemi](./media/logic-apps-add-run-inline-code/inline-code-action-default.png)

1. **Kod** kutusunda, örnek kodu silin ve çalıştırmak istediğiniz kodu girin. Yöntem imzasını tanımlamadan, bir yöntemin içine yerleştirdiğiniz kodu yazın. 

   Tanınan bir anahtar sözcük yazdığınızda, otomatik tamamlama listesi görüntülenir, böylece kullanılabilir anahtar sözcüklerden seçim yapabilirsiniz, örneğin:

   ![Anahtar sözcük otomatik tamamlama listesi](./media/logic-apps-add-run-inline-code/auto-complete.png)

   Bu örnek kod parçacığı ilk olarak, giriş metninde eşleştirilecek bir model belirten *normal ifade*depolayan bir değişken oluşturur. Daha sonra kod, tetikleyiciden e-posta gövdesi verilerini depolayan bir değişken oluşturur.

   ![Değişken oluşturma](./media/logic-apps-add-run-inline-code/save-email-body-variable.png)

   Tetikleyiciden ve önceki eylemlerin sonuçlarını daha kolay başvuruya getirmek için, imleiniz **kod** kutusunun içindeyken dinamik içerik listesi görüntülenir. Bu örnekte, liste, artık seçebileceğiniz **gövde** belirteci dahil olmak üzere tetikleyiciden kullanılabilir sonuçları gösterir.

   **Gövde** belirtecini seçtikten sonra, satır içi kod eylemi, belirteci e-postanın `workflowContext` `Body` özellik değerine başvuran bir nesneye çözümler:

   ![Sonuç seçin](./media/logic-apps-add-run-inline-code/inline-code-example-select-outputs.png)

   Kod kutusunda, **kod** parçacığında, salt okunurdur `workflowContext` nesnesini giriş olarak kullanabilirsiniz. Bu nesne, kodunuzu tetikleyiciden gelen sonuçlara ve iş akışınızdan önceki eylemlere erişim sağlayan alt özellikler içerir.
   Daha fazla bilgi için bu konunun ilerleyen kısımlarında bu bölüme bakın: [Başvuru tetikleyicisi ve eylem, kodunuzda sonuçlanır](#workflowcontext).

   > [!NOTE]
   >
   > Kod parçacığınızı nokta (.) işlecini kullanan eylem adlarına başvuruyorsa, bu eylem adlarını [ **Eylemler** parametresine](#add-parameters)eklemeniz gerekir. Bu başvurular Ayrıca eylem adlarını köşeli ayraç ([]) ve tırnak işaretleriyle birlikte içermelidir, örneğin:
   >
   > `// Correct`</br> 
   > `workflowContext.actions["my.action.name"].body`</br>
   >
   > `// Incorrect`</br>
   > `workflowContext.actions.my.action.name.body`

   Satır içi kod eylemi bir `return` ifade gerektirmez, ancak bir `return` deyimin sonuçları sonraki eylemlerde **sonuç** belirteci aracılığıyla başvuru için kullanılabilir. 
   Örneğin, kod parçacığı, normal ifadeye karşılık gelen e-posta `match()` gövdesinde eşleşmeleri bulan işlevi çağırarak sonucu döndürür. **Oluşturma** eylemi, satır içi kod eyleminin sonuçlarına başvurmak için **sonuç** belirtecini kullanır ve tek bir sonuç oluşturur.

   ![Tamamlanmış mantıksal uygulama](./media/logic-apps-add-run-inline-code/inline-code-complete-example.png)

1. İşiniz bittiğinde mantıksal uygulamanızı kaydedin.

<a name="workflowcontext"></a>

### <a name="reference-trigger-and-action-results-in-your-code"></a>Kodunuzda başvuru tetikleyicisi ve eylem sonuçları

Nesnesi,, `trigger`ve `actions` altözellikleriiçerenbuyapıyasahiptir`workflow`: `workflowContext`

```json
{
   "workflowContext": {
      "actions": {
         "<action-name-1>": @actions('<action-name-1>'),
         "<action-name-2>": @actions('<action-name-2>')
      },
      "trigger": {
         @trigger()
      },
      "workflow": {
         @workflow()
      }
   }
}
```

Bu tablo, bu alt özellikler hakkında daha fazla bilgi içerir:

| Özellik | Tür | Açıklama |
|----------|------|-------|
| `actions` | Nesne koleksiyonu | Kod parçacığınızdan önce çalışan eylemlerden elde edilen sonuç nesneleri. Her bir nesne, anahtarın bir eylemin adı olduğu ve değeri ile `@actions('<action-name>')` [eylem () işlevinin](../logic-apps/workflow-definition-language-functions-reference.md#actions) çağrılmasının eşdeğeri olan *anahtar-değer* çiftine sahiptir. Eylemin adı, temel alınan iş akışı tanımında kullanılan aynı eylem adını kullanır. Bu, eylem adındaki boşluklar ("") alt çizgi (_) ile değiştirir. Bu nesne, geçerli iş akışı örneğinden çalıştırılan eylem özelliği değerlerine erişim sağlar. |
| `trigger` | Object | Tetikleyiciden elde edilen sonuç nesnesi ve [Trigger () işlevini](../logic-apps/workflow-definition-language-functions-reference.md#trigger)çağırma ile eşdeğerdir. Bu nesne, geçerli iş akışı örneği çalıştırağından tetikleme özelliği değerlerine erişim sağlar. |
| `workflow` | Object | Workflow nesnesi ve [Workflow () işlevini](../logic-apps/workflow-definition-language-functions-reference.md#workflow)çağırmaya eşdeğerdir. Bu nesne, geçerli iş akışı örneğinden çalıştırılan iş akışı adı, çalıştırma KIMLIĞI gibi iş akışı özellik değerlerine erişim sağlar. |
|||

Bu konunun örneğinde, `workflowContext` nesnesi kodunuzun erişebileceği şu özelliklere sahiptir:

```json
{
   "workflowContext": {
      "trigger": {
         "name": "When_a_new_email_arrives",
         "inputs": {
            "host": {
               "connection": {
                  "name": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/office365"
               }
            },
            "method": "get",
            "path": "/Mail/OnNewEmail",
            "queries": {
               "includeAttachments": "False"
            }
         },
         "outputs": {
            "headers": {
               "Pragma": "no-cache",
               "Content-Type": "application/json; charset=utf-8",
               "Expires": "-1",
               "Content-Length": "962095"
            },
            "body": {
               "Id": "AAMkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgBGAAAAAABmZwxUQtCGTqSPpjjMQeD",
               "DateTimeReceived": "2019-03-28T19:42:16+00:00",
               "HasAttachment": false,
               "Subject": "Hello World",
               "BodyPreview": "Hello World",
               "Importance": 1,
               "ConversationId": "AAQkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgAQ",
               "IsRead": false,
               "IsHtml": true,
               "Body": "Hello World",
               "From": "<sender>@<domain>.com",
               "To": "<recipient-2>@<domain>.com;<recipient-2>@<domain>.com",
               "Cc": null,
               "Bcc": null,
               "Attachments": []
            }
         },
         "startTime": "2019-05-03T14:30:45.971564Z",
         "endTime": "2019-05-03T14:30:50.1746874Z",
         "scheduledTime": "2019-05-03T14:30:45.8778117Z",
         "trackingId": "1cd5ffbd-f989-4df5-a96a-6e9ce31d03c5",
         "clientTrackingId": "08586447130394969981639729333CU06",
         "originHistoryName": "08586447130394969981639729333CU06",
         "code": "OK",
         "status": "Succeeded"
      },
      "workflow": {
         "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>",
         "name": "<logic-app-workflow-name>",
         "type": "Microsoft.Logic/workflows",
         "location": "<Azure-region>",
         "run": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>/runs/08586453954668694173655267965CU00",
            "name": "08586453954668694173655267965CU00",
            "type": "Microsoft.Logic/workflows/runs"
         }
      }
   }
}
```

<a name="add-parameters"></a>

## <a name="add-parameters"></a>Parametre ekle

Bazı durumlarda, **satır Içi kod** eyleminin **tetikleyici veya** **eylem** parametrelerini ekleyerek tetikleyiciden gelen sonuçları veya kodun bağımlılıklar olarak başvurduğu belirli eylemleri içermesi gerekebilir. Bu seçenek, başvurulan sonuçların çalışma zamanında bulunamadığı senaryolar için yararlıdır.

> [!TIP]
> Kodunuzu yeniden kullanmayı planlıyorsanız, kodunuzun, tetikleyici veya eylemleri açık bağımlılıklar olarak eklemek yerine çözümlenen belirteç başvurularını içermesi için **kod** kutusunu kullanarak özelliklere başvurular ekleyin.

Örneğin, Office 365 Outlook Bağlayıcısı için **onay e-postası gönder** eyleminden **SelectedOption** sonucuna başvuran bir kodunuz olduğunu varsayalım. Oluşturma zamanında, Logic Apps altyapısı, herhangi bir tetikleyici veya eylem sonucuna başvurulduğunu ve bu sonuçları otomatik olarak içerip içermediğini anlamak için kodunuzu analiz eder. Çalışma zamanında, başvurulan tetikleyici veya eylem sonucunun belirtilen `workflowContext` nesnede kullanılamadığı bir hata almanız gerekir, bu tetikleyiciyi veya eylemi açık bir bağımlılık olarak ekleyebilirsiniz. Bu örnekte, **Eylemler** parametresini ekler ve **satır içi kod** eyleminin **onay e-postası gönder** eyleminden sonucu açıkça içermesini belirtirsiniz.

Bu parametreleri eklemek için **yeni parametre Ekle** listesini açın ve istediğiniz parametreleri seçin:

   ![Parametre ekle](./media/logic-apps-add-run-inline-code/inline-code-action-add-parameters.png)

   | Parametre | Açıklama |
   |-----------|-------------|
   | **Eylemler** | Önceki eylemlerden sonuçları dahil edin. Bkz. [eylem sonuçlarını dahil et](#action-results). |
   | **Tetikleyici** | Tetikleyiciden sonuçları ekleyin. Bkz. [tetikleyici sonuçlarını ekleme](#trigger-results). |
   |||

<a name="trigger-results"></a>

### <a name="include-trigger-results"></a>Tetikleyici sonuçlarını dahil et

**Tetikleyiciler**' i seçerseniz, tetikleyici sonuçlarının dahil edilip edilmeyeceğini girmeniz istenir.

* **Tetikleyici** listesinden **Evet**' i seçin.

<a name="action-results"></a>

### <a name="include-action-results"></a>Eylem sonuçlarını dahil et

**Eylemler**' i seçerseniz, eklemek istediğiniz eylemler istenir. Ancak, eylem eklemeye başlamadan önce mantıksal uygulamanın temel alınan iş akışı tanımında görüntülenen eylem adı sürümüne ihtiyacınız vardır.

* Bu özellik değişkenleri, döngüleri ve yineleme dizinlerini desteklemez.

* Mantıksal uygulamanızın iş akışı tanımındaki adlar boşluk değil bir alt çizgi (_) kullanır.

* Nokta işleci (.) kullanan eylem adları için bu işleçleri dahil edin, örneğin:

  `My.Action.Name`

1. Tasarımcı araç çubuğunda **kod görünümü**' ne tıklayın ve eylem adı için `actions` özniteliği içinde arama yapın.

   Örneğin, `Send_approval_email_` **onay e-postası gönder** eyleminin JSON adıdır.

   ![JSON 'da eylem adını bul](./media/logic-apps-add-run-inline-code/find-action-name-json.png)

1. Tasarımcı görünümü ' ne dönmek için, kod görünümü araç çubuğunda **Tasarımcı**' yı seçin.

1. İlk eylemi eklemek için, **Eylemler öğesi-1** kutusuna eylemin JSON adını girin.

   ![İlk eylemi girin](./media/logic-apps-add-run-inline-code/add-action-parameter.png)

1. Başka bir eylem eklemek için **Yeni öğe Ekle**' yi seçin.

## <a name="reference"></a>Başvuru

Mantıksal uygulamanızın temel alınan iş akışı tanımında Iş akışı tanım dilini kullanarak **JavaScript kodu yürütme** eyleminin yapısını ve sözdizimini Çalıştır hakkında daha fazla bilgi için, bu eylemin [başvuru bölümüne](../logic-apps/logic-apps-workflow-actions-triggers.md#run-javascript-code)bakın.

## <a name="next-steps"></a>Sonraki adımlar

[Azure Logic Apps Için bağlayıcılar](../connectors/apis-list.md) hakkında daha fazla bilgi edinin
