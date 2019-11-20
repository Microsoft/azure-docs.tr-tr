---
title: Mantıksal uygulama tanımları oluşturma, düzenleme veya genişletme-Azure Logic Apps
description: Azure Logic Apps mantıksal uygulama JSON tanımlarını yazma, düzenleme ve genişletme
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 01/01/2018
ms.openlocfilehash: 03203a5fdb6d61ed95e96581a7e4a979c911cd02
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680033"
---
# <a name="create-edit-or-extend-json-for-logic-app-definitions-in-azure-logic-apps"></a>Azure Logic Apps mantıksal uygulama tanımları için JSON oluşturun, düzenleyin veya genişletin

[Azure Logic Apps](../logic-apps/logic-apps-overview.md)içinde otomatik iş akışlarıyla kurumsal tümleştirme çözümleri oluşturduğunuzda, temel alınan mantıksal uygulama tanımları, [Iş akışı Tanım Dili (WDL) ile birlikte basit ve bildirim TEMELLI JavaScript nesne gösterimi (JSON) kullanır ](../logic-apps/logic-apps-workflow-definition-language.md)açıklaması ve doğrulama için şema. Bu biçimler, mantıksal uygulama tanımlarının kod hakkında çok daha fazla bilgi vermeden daha kolay okunmasını ve anlaşılmasını kolaylaştırır. Mantıksal uygulama oluşturma ve dağıtma işlemlerini otomatik hale getirmek istediğinizde, [Azure Resource Manager şablonları](../azure-resource-manager/template-deployment-overview.md)içinde [Azure kaynakları](../azure-resource-manager/resource-group-overview.md) olarak Logic App tanımlarını dahil edebilirsiniz. Mantıksal uygulamalar oluşturmak, yönetmek ve dağıtmak için [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.logicapp), [Azure CLı](../azure-resource-manager/resource-group-template-deploy-cli.md)veya [Azure Logic Apps REST API 'lerini](https://docs.microsoft.com/rest/api/logic/)kullanabilirsiniz.

JSON 'daki Logic App tanımlarıyla çalışmak için Azure portal veya Visual Studio 'da çalışırken kod görünümü düzenleyicisini açın veya tanımı istediğiniz düzenleyiciye kopyalayın. Logic Apps 'e yeni başladıysanız, [ilk mantıksal uygulamanızı oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md)konusunu inceleyin.

> [!NOTE]
> Mantıksal uygulama tanımlarında parametreler ve birden çok tetikleyici tanımlama gibi bazı Azure Logic Apps özellikleri, Logic Apps Tasarımcısı değil yalnızca JSON ile kullanılabilir.
> Bu nedenle, bu görevler için kod görünümü veya başka bir düzenleyicide çalışmanız gerekir.

## <a name="edit-json---azure-portal"></a>JSON-Azure portal Düzenle

1. <a href="https://portal.azure.com" target="_blank">Azure Portal</a>’ında oturum açın.

2. Sol menüden **tüm hizmetler**' i seçin. Arama kutusunda, "Logic Apps" i bulun ve ardından sonuçlardan mantıksal uygulamanızı seçin.

3. Mantıksal uygulamanızın menüsünde, **geliştirme araçları**' nın altında, **mantıksal uygulama kod görünümü**' nü seçin.

   Kod görünümü Düzenleyicisi açılır ve mantıksal uygulama tanımınızı JSON biçiminde gösterir.

## <a name="edit-json---visual-studio"></a>JSON düzenleme-Visual Studio

Visual Studio 'da mantıksal uygulama tanımınızda çalışabilmeniz [için önce gerekli araçları yüklediğinizden](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites)emin olun. Visual Studio ile bir mantıksal uygulama oluşturmak için hızlı başlangıcı inceleyin [: Azure Logic Apps-Visual Studio ile görevleri ve Işlemleri otomatikleştirin](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

Visual Studio 'da doğrudan Azure portal veya Visual Studio 'dan Azure Resource Manager projeler olarak oluşturulan ve dağıtılan Logic Apps 'i açabilirsiniz.

1. Mantıksal uygulamanızı içeren Visual Studio çözümünü veya [Azure Kaynak grubu](../azure-resource-manager/resource-group-overview.md) projesini açın.

2. Mantıksal uygulamanızın tanımını bulun ve açın. Bu, varsayılan olarak **logicapp. JSON**adlı bir [Kaynak Yöneticisi şablonunda](../azure-resource-manager/template-deployment-overview.md)görüntülenir. Bu şablonu, farklı ortamlara dağıtım için kullanabilir ve özelleştirebilirsiniz.

3. Mantıksal uygulama tanımınız ve şablonunuz için kısayol menüsünü açın. **Mantıksal Uygulama Tasarımcısı ile Aç**’ı seçin.

   ![Mantıksal uygulamayı Visual Studio çözümünde aç](./media/logic-apps-author-definitions/open-logic-app-designer.png)

   > [!TIP]
   > Visual Studio 2019 ' de bu komuta sahip değilseniz, Visual Studio için en son güncelleştirmelere sahip olup olmadığınızı kontrol edin.

4. Tasarımcının alt tarafında **kod görünümü**' ne tıklayın. 

   Kod görünümü Düzenleyicisi açılır ve mantıksal uygulama tanımınızı JSON biçiminde gösterir.

5. Tasarımcı görünümü ' ne dönmek için, kod görünümü Düzenleyicisi ' nin altında, **Tasarım**' ı seçin.

## <a name="parameters"></a>Parametreler

Dağıtım yaşam döngüsü genellikle geliştirme, test, hazırlama ve üretim için farklı ortamlara sahiptir. Mantıksal uygulama genelinde yeniden kullanmak istediğiniz değerlere sahip olduğunuzda veya dağıtım gereksinimlerinize göre değişiklik yaparken, mantıksal uygulamayı otomatikleştirebilmeniz için iş akışı tanımınız için bir [Azure Resource Manager şablonu](../azure-resource-manager/resource-group-overview.md) oluşturabilirsiniz. dağıtmak. 

Bunun yerine bu değerleri *parametreleştirmek*veya parametreleri tanımlamak ve kullanmak için bu genel adımları izleyin. Daha sonra bu değerleri şablonunuza geçiren ayrı bir parametre dosyasında değerleri sağlayabilirsiniz. Bu şekilde, mantıksal uygulamanızı güncelleştirip yeniden dağıtmanıza gerek kalmadan bu değerleri daha kolay bir şekilde değiştirebilirsiniz. Tam Ayrıntılar için bkz. [genel bakış: Azure Resource Manager şablonlarıyla Logic Apps için dağıtımı otomatikleştirin](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md).

1. Şablonunuzda, sırasıyla dağıtım ve çalışma zamanında kullanılacak değerleri kabul etmek için şablon parametreleri ve iş akışı Tanım parametrelerini tanımlayın.

   Şablon parametreleri iş akışı tanımınızın dışında bir parametre bölümünde tanımlanmıştır, iş akışı Tanım parametreleri iş akışı tanımınızın içinde olan bir parametreler bölümünde tanımlanmıştır.

1. Sabit kodlanmış değerleri bu parametrelere başvuran ifadelerle değiştirin. Şablon ifadeleri, iş akışı Tanım ifadelerinden farklı olan söz dizimini kullanır.

   Çalışma zamanında değerlendirilen iş akışı Tanım ifadeleri içinde, dağıtımda değerlendirilen şablon ifadelerini kullanmadan kodunuzu karmaşıkmaktan kaçının. Yalnızca iş akışı tanımınızın dışında bir şablon ifadesi kullanın. İş akışı tanımınızın içinde yalnızca iş akışı tanımı ifadelerini kullanın.

   İş akışı Tanım parametrelerinizin değerlerini belirttiğinizde, iş akışı tanımınızın dışında, ancak mantıksal uygulamanızın kaynak tanımında hala olan Parameters bölümünü kullanarak şablon parametrelerine başvurabilirsiniz. Bu şekilde, şablon parametre değerlerini iş akışı Tanım parametrelerinizle geçirebilirsiniz.

1. Parametrelerinizin değerlerini ayrı bir [parametre dosyasında](../azure-resource-manager/resource-group-template-deploy.md#parameter-files) depolayın ve bu dosyayı dağıtımınıza ekleyin.

## <a name="process-strings-with-functions"></a>İşlevlerle dizeleri işleme

Logic Apps dizelerle çalışmak için çeşitli işlevlere sahiptir. Örneğin, bir siparişten başka bir sisteme şirket adı geçirmek istediğinizi varsayalım. Ancak, karakter kodlamasının doğru işlemesi hakkında emin değilsiniz. Bu dize üzerinde Base64 kodlaması gerçekleştirebilirsiniz, ancak URL 'deki kaçışları önlemek için, bunun yerine birkaç karakteri değiştirebilirsiniz. Ayrıca, ilk beş karakter kullanılmadığından yalnızca şirket adı için bir alt dize gerekir.

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1",
        "companyName": "NAME=Contoso"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "request": {
      "type": "Request",
      "kind": "Http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

Bu adımlar, bu örneğin içinden dış olarak çalışan bu dizeyi nasıl işlemekte olduğunu anlatmaktadır:

```
"uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
```

1. Şirket adı için [`length()`](../logic-apps/logic-apps-workflow-definition-language.md) alın, böylece toplam karakter sayısını alırsınız.

2. Daha kısa bir dize almak için `5` çıkarın.

3. Şimdi bir [`substring()`](../logic-apps/logic-apps-workflow-definition-language.md)alın. @No__t_0 dizininden başlayın ve dizenin geri kalanına gidin.

4. Bu alt dizeyi bir [`base64()`](../logic-apps/logic-apps-workflow-definition-language.md) dizesine dönüştürün.

5. Artık tüm `+` karakterleri `-` karakterlerle [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) .

6. Son olarak, tüm `/` karakterleri `_` karakterle [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) .

## <a name="map-list-items-to-property-values-then-use-maps-as-parameters"></a>Liste öğelerini özellik değerlerine eşleyin, sonra haritaları parametre olarak kullanın

Bir özelliğin değerini temel alan farklı sonuçlar almak için, her özellik değeriyle eşleşen bir eşleme oluşturabilir ve bu eşlemeyi bir parametre olarak kullanabilirsiniz.

Örneğin, bu iş akışı bazı kategorileri parametreler olarak tanımlar ve belirli bir URL ile bu kategorilerle eşleşen bir eşleme. İlk olarak, iş akışı makalelerin bir listesini alır. Daha sonra iş akışı, her makale kategorisi ile eşleşen URL 'YI bulmak için Haritayı kullanır.

*   [@No__t_1](../logic-apps/logic-apps-workflow-definition-language.md) işlevi, kategorinin bilinen tanımlanmış bir kategori ile eşleşip eşleşmediğini denetler.

*   Eşleşen bir kategori alındıktan sonra örnek köşeli ayraç kullanarak öğeyi eşlemden çeker: `parameters[...]`

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "specialCategories": {
      "defaultValue": [
        "science",
        "google",
        "microsoft",
        "robots",
        "NSA"
      ],
      "type": "Array"
    },
    "destinationMap": {
      "defaultValue": {
        "science": "https://www.nasa.gov",
        "microsoft": "https://www.microsoft.com/en-us/default.aspx",
        "google": "https://www.google.com",
        "robots": "https://en.wikipedia.org/wiki/Robot",
        "NSA": "https://www.nsa.gov/"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "http"
    }
  },
  "actions": {
    "getArticles": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=https://feeds.wired.com/wired/index"
      }
    },
    "forEachArticle": {
      "type": "foreach",
      "foreach": "@body('getArticles').responseData.feed.entries",
      "actions": {
        "ifGreater": {
          "type": "if",
          "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)",
          "actions": {
            "getSpecialPage": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
              }
            }
          }
        }
      },
      "runAfter": {
        "getArticles": [
          "Succeeded"
        ]
      }
    }
  }
}
```

## <a name="get-data-with-date-functions"></a>Tarih işlevleri ile veri al

*Tetikleyicileri*yerel olarak desteklemeyen bir veri kaynağından veri almak Için, tarih işlevlerini kullanarak saat ve tarihlerle çalışma yapabilirsiniz. Örneğin, bu ifade, bu iş akışı adımlarının ne kadar süre sürdüğünü, içten dışarıya çalışmayı bulur:

``` json
"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
```

1. @No__t_0 eyleminden `startTime` ayıklayın.
2. @No__t_0 ile geçerli zamanı alın.
3. Bir saniye çıkar:

   [`addseconds(..., -1)`](../logic-apps/logic-apps-workflow-definition-language.md) 

   @No__t_0 veya `hours` gibi başka bir zaman birimi de kullanabilirsiniz.

3. Şimdi, bu iki değeri karşılaştırabilirsiniz. 

   İlk değer ikinci değerden küçükse, siparişin ilk yerleştirilmesi bu yana birden fazla saniye geçti.

Tarihleri biçimlendirmek için dize biçimlerini kullanabilirsiniz. Örneğin, RFC1123 almak için [`utcnow('r')`](../logic-apps/logic-apps-workflow-definition-language.md)kullanın. [Tarih biçimlendirme](../logic-apps/logic-apps-workflow-definition-language.md)hakkında daha fazla bilgi edinin.

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder-id"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://www.example.com/?id=@{parameters('order').id}"
      }
    },
    "ifTimingWarning": {
      "type": "If",
      "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
      "actions": {
        "timingWarning": {
          "type": "Http",
          "inputs": {
            "method": "GET",
            "uri": "https://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
          }
        }
      },
      "runAfter": {
        "order": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

## <a name="next-steps"></a>Sonraki adımlar

* [Bir koşula göre adımları çalıştırın (koşullu deyimler)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Farklı değerlere göre adımları Çalıştır (Switch deyimleri)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Çalıştır ve Yinele adımları (döngüler)](../logic-apps/logic-apps-control-flow-loops.md)
* [Paralel adımları çalıştırma veya birleştirme (dallar)](../logic-apps/logic-apps-control-flow-branches.md)
* [Gruplanmış eylem durumu (kapsamlar) temelinde adımları Çalıştır](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Azure Logic Apps Için Iş akışı Tanım Dili şeması](../logic-apps/logic-apps-workflow-definition-language.md) hakkında daha fazla bilgi edinin
* [Azure Logic Apps iş akışı eylemleri ve Tetikleyicileri](../logic-apps/logic-apps-workflow-actions-triggers.md) hakkında daha fazla bilgi edinin
