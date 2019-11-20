---
title: Değerleri depolamak için değişkenler oluşturma ve yönetme-Azure Logic Apps
description: Azure Logic Apps değişkenleri kullanarak değerleri depolama ve yönetme
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 0f7947e4a96a49e3a7a3b0059a1b20b21ac8cbd1
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71180794"
---
# <a name="store-and-manage-values-by-using-variables-in-azure-logic-apps"></a>Azure Logic Apps değişkenleri kullanarak değerleri depolayın ve yönetin

Bu makalede, mantıksal uygulamanızda değerleri depolamak için kullandığınız değişkenlerin nasıl oluşturulacağı ve bunlarla nasıl çalışılacağı gösterilmektedir. Örneğin, değişkenler bir döngünün kaç kez çalışacağını izlemenize yardımcı olabilir. Bir diziyi yinelemek veya belirli bir öğe için bir diziyi denetlemek için, her bir dizi öğesi için dizin numarasına başvurmak üzere bir değişken kullanabilirsiniz.

Integer, float, Boolean, String, Array ve Object gibi veri türleri için değişkenler oluşturabilirsiniz. Bir değişken oluşturduktan sonra, diğer görevleri yerine getirebilirsiniz, örneğin:

* Değişkenin değerine erişin veya başvurun.
* Değişkeni *artırma* ve *azaltma*olarak da bilinen sabit bir değer ile artırın veya azaltın.
* Değişkenine farklı bir değer atayın.
* Değişkenin değerini bir dize veya dizide son kez *ekleyin veya ekleyin* .

Değişkenler vardır ve yalnızca bunları oluşturan Logic App örneği içinde geneldir. Ayrıca, bir mantıksal uygulama örneği içindeki tüm döngü yinelemeleri üzerinde kalır. Bir değişkene başvuru yaptığınızda, bir eylemin çıktılarına başvurmak için her zamanki yol olan eylemin adını değil, değişkenin adını belirteç olarak kullanın.

> [!IMPORTANT]
> Varsayılan olarak, "for each" döngüsünde döngü, paralel olarak çalışır. Döngülerde değişkenler kullandığınızda, değişkenlerin öngörülebilir sonuçlar döndürmesi için döngüyü [ardışık](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop) olarak çalıştırın.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* Değişkeni oluşturmak istediğiniz mantıksal uygulama

  Logic Apps 'e yeni başladıysanız [ne Azure Logic Apps? ne olduğunu](../logic-apps/logic-apps-overview.md) gözden geçirin ve [hızlı başlangıç: İlk mantıksal uygulamanızı](../logic-apps/quickstart-create-first-logic-app-workflow.md)oluşturun.

* Mantıksal uygulamanızda ilk adım olarak bir [tetikleyici](../logic-apps/logic-apps-overview.md#logic-app-concepts)

  Oluşturma ve değişkenlerle çalışma için Eylemler ekleyebilmeniz için, mantıksal uygulamanızın bir tetikleyiciyle başlaması gerekir.

<a name="create-variable"></a>

## <a name="initialize-variable"></a>Değişken başlat

Bir değişken oluşturabilir ve veri türünü ve başlangıç değerini, mantıksal uygulamanızda tek bir eylem içinde bildirebilirsiniz. Yalnızca genel düzeyde değişkenleri, kapsam, koşullar ve döngüler içinde değil, bildirebilirsiniz.

1. [Azure Portal](https://portal.azure.com) veya Visual Studio 'Da mantıksal uygulama tasarımcısında mantıksal uygulamanızı açın.

   Bu örnek, Azure portal ve bir mantıksal uygulamayı mevcut bir tetikleyicisiyle kullanır.

1. Mantıksal uygulamanızda, değişken eklemek istediğiniz adım altında aşağıdaki adımlardan birini izleyin: 

   * Son adım altında bir eylem eklemek için **yeni adım**' ı seçin.

     ![Eylem ekle](./media/logic-apps-create-variables-store-values/add-action.png)

   * Adımlar arasında bir eylem eklemek için, artı işareti ( **+** ) görünecek şekilde farenizi bağlama okuna taşıyın. Artı işaretini seçin ve ardından **Eylem Ekle**' yi seçin.

1. **Eylem seçin**altında, arama kutusuna filtreniz olarak girin `variables` . Eylemler listesinden, **değişkeni Başlat**' ı seçin.

   ![Eylem Seç](./media/logic-apps-create-variables-store-values/select-initialize-variable-action.png)

1. Aşağıdaki konularda, değişkeniniz hakkında şu bilgileri sağlayın:

   | Özellik | Gerekli | Value |  Açıklama |
   |----------|----------|-------|--------------|
   | **Name** | Evet | <*değişken adı*> | Artış değişkeninin adı |
   | **Tür** | Evet | <*değişken türü*> | Değişken için veri türü |
   | **Değer** | Hayır | <*başlangıç-değer*> | Değişkeninizin ilk değeri <p><p>**İpucu**: İsteğe bağlı olsa da, bu değeri en iyi uygulama olarak ayarlarsanız, değişkeninizin başlangıç değerini her zaman bilirsiniz. |
   |||||

   Örneğin:

   ![Değişken başlat](./media/logic-apps-create-variables-store-values/initialize-variable.png)

1. Şimdi istediğiniz eylemleri eklemeye devam edin. İşiniz bittiğinde, Tasarımcı araç çubuğunda **Kaydet**' i seçin.

Tasarımcıdan kod görünümü düzenleyicisine geçiş yaparsanız, **değişken başlatma** eyleminin JAVASCRIPT nesne GÖSTERIMI (JSON) biçiminde olan mantıksal uygulama tanımınızda görünmesini sağlayan bir yol aşağıda verilmiştir:

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "Count",
               "type": "Integer",
               "value": 0
          } ]
      },
      "runAfter": {}
   }
},
```

> [!NOTE]
> **Değişken Başlat** eyleminin bir dizi olarak yapılandırılmış `variables` bir bölümü olsa da, eylem tek seferde yalnızca bir değişken oluşturabilir. Her yeni değişken, tek bir **değişken başlatma** eylemi gerektirir.

Diğer bazı değişken türleri için örnekler aşağıda verilmiştir:

*Dize değişkeni*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myStringVariable",
               "type": "String",
               "value": "lorem ipsum"
          } ]
      },
      "runAfter": {}
   }
},
```

*Boolean değişkeni*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myBooleanVariable",
               "type": "Boolean",
               "value": false
          } ]
      },
      "runAfter": {}
   }
},
```

*Tamsayılarla dizi*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myArrayVariable",
               "type": "Array",
               "value": [1, 2, 3]
          } ]
      },
      "runAfter": {}
   }
},
```

*Dizeler içeren dizi*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myArrayVariable",
               "type": "Array",
               "value": ["red", "orange", "yellow"]
          } ]
      },
      "runAfter": {}
   }
},
```

<a name="get-value"></a>

## <a name="get-the-variables-value"></a>Değişkenin değerini Al

Bir değişkenin içeriğini almak veya başvurmak için, mantıksal uygulama Tasarımcısı ve kod görünümü Düzenleyicisi 'ndeki [değişkenler () işlevini](../logic-apps/workflow-definition-language-functions-reference.md#variables) de kullanabilirsiniz. Bir değişkene başvurulduğunda, bir eylemin çıktılarına başvurmak için her zamanki yol olan eylemin adını değil, değişkenin adını belirteç olarak kullanın.

Örneğin, bu ifade `variables()` işlevi kullanılarak [Bu makalede daha önce oluşturulan](#append-value) dizi değişkeninden öğeleri alır. `string()` İşlev, değişkenin içeriğini dize biçiminde döndürür:`"1, 2, 3, red"`

```json
@{string(variables('myArrayVariable'))}
```

<a name="increment-value"></a>

## <a name="increment-variable"></a>Artış değişkeni 

Bir değişkeni sabit *bir değere göre artırmak veya arttırmak* için, mantıksal uygulamanıza **artırma değişkeni** eylemini ekleyin. Bu eylem yalnızca tamsayı ve kayan değişkenlerle birlikte kullanılabilir.

1. Mantıksal uygulama Tasarımcısı ' nda, var olan bir değişkeni artırmak istediğiniz adım altında **yeni adım**' ı seçin. 

   Örneğin, bu mantıksal uygulamanın zaten bir tetikleyicisi ve bir değişken oluşturan bir eylemi vardır. Bu nedenle, aşağıdaki adımlarla yeni bir eylem ekleyin:

   ![Eylem ekle](./media/logic-apps-create-variables-store-values/add-increment-variable-action.png)

   Varolan adımlar arasında bir eylem eklemek için, artı işareti (+) görünecek şekilde farenizi bağlama okuna taşıyın. Artı işaretini seçin ve ardından **Eylem Ekle**' yi seçin.

1. Arama kutusuna filtreniz olarak "artış değişkeni" yazın. Eylemler listesinde **artırma değişkeni**' ni seçin.

   !["Değişken artırma" eylemini seçin](./media/logic-apps-create-variables-store-values/select-increment-variable-action.png)

1. Değişkeninizi arttırın şu bilgileri sağlayın:

   | Özellik | Gerekli | Value |  Açıklama |
   |----------|----------|-------|--------------|
   | **Name** | Evet | <*değişken adı*> | Artış değişkeninin adı |
   | **Değer** | Hayır | <*artış değeri*> | Değişkeni artırma için kullanılan değer. Varsayılan değer bir değeridir. <p><p>**İpucu**: İsteğe bağlı olsa da, bu değeri en iyi uygulama olarak ayarlarsanız, değişkeninizi arttırmanın her zaman belirli bir değerini bilirsiniz. |
   ||||

   Örneğin:

   ![Artış değeri örneği](./media/logic-apps-create-variables-store-values/increment-variable-action-information.png)

1. İşiniz bittiğinde, Tasarımcı araç çubuğunda **Kaydet**' i seçin.

Tasarımcıdan kod görünümü düzenleyicisine geçiş yaparsanız, **değişken artış** EYLEMININ, JSON biçiminde olan mantıksal uygulama tanımınızın içinde görünme yolu şudur:

```json
"actions": {
   "Increment_variable": {
      "type": "IncrementVariable",
      "inputs": {
         "name": "Count",
         "value": 1
      },
      "runAfter": {}
   }
},
```

## <a name="example-create-loop-counter"></a>Örnek: Döngü sayacı oluştur

Değişkenler, bir döngünün kaç kez çalışacağını saymak için yaygın olarak kullanılır. Bu örnek, bir e-postadaki ekleri sayan bir döngü oluşturarak bu görev için değişkenleri nasıl oluşturup kullanacağınızı gösterir.

1. Azure portal, boş bir mantıksal uygulama oluşturun. Yeni e-posta ve ekleri denetleyen bir tetikleyici ekleyin.

   Bu örnek, **Yeni bir e-posta geldiğinde**Office 365 Outlook tetikleyicisini kullanır. Bu tetikleyiciyi yalnızca e-postada ekleri olduğunda harekete getirebilirsiniz. Ancak, Outlook.com Bağlayıcısı gibi ekleri olan yeni e-postaları denetleyen herhangi bir bağlayıcıyı kullanabilirsiniz.

1. Tetikleyicide, ekleri denetlemek ve bu ekleri mantıksal uygulamanızın iş akışına iletmek için şu özellikler için **Evet** ' i seçin:

   * **Eki Var**
   * **Ekleri Dahil Et**

   ![Ekleri denetle ve dahil et](./media/logic-apps-create-variables-store-values/check-include-attachments.png)

1. [ **Değişken Başlat** eylemini](#create-variable)ekleyin. Sıfır başlangıç değeri olan adlı `Count` bir tamsayı değişkeni oluşturun.

   !["Değişken Başlat" eylemi Ekle](./media/logic-apps-create-variables-store-values/initialize-variable.png)

1. Her bir ekte geçiş yapmak için her döngü *için* bir ekleyin.

   1. **Değişken başlatma** eyleminin altında **yeni adım**' ı seçin.

   1. **Eylem seçin**altında, **yerleşik**' i seçin. Arama kutusuna arama filtrenizi olarak `for each` girin ve **her biri için**öğesini seçin.

      !["For each" döngüsü Ekle](./media/logic-apps-create-variables-store-values/add-loop.png)

1. Döngüde, **önceki adımlardan bir çıkış seçin** kutusuna tıklayın. Dinamik içerik listesi göründüğünde, **ekler**' i seçin.

   !["Ekler"i seçin](./media/logic-apps-create-variables-store-values/select-attachments.png)

   **Attachments** özelliği, tetikleyicinizin çıktısından gelen e-posta eklerine döngüsünde bir diziyi geçirir.

1. **For each** döngüsünde **Eylem Ekle**' yi seçin.

   !["Eylem Ekle" yi seçin](./media/logic-apps-create-variables-store-values/add-action-2.png)

1. Arama kutusuna filtreniz olarak "artış değişkeni" yazın. Eylemler listesinden **artış değişkeni**' ni seçin.

   > [!NOTE]
   > **Değişken artırma** eyleminin döngünün içinde göründüğünden emin olun. Eylem döngünün dışında görünürse, eylemi döngüye sürükleyin.

1. **Değişken artırma** eyleminde, **ad** listesinden **sayı** değişkenini seçin.

   !["Say" değişkenini seçin](./media/logic-apps-create-variables-store-values/add-increment-variable-example.png)

1. Döngü altında, size ek sayısı gönderen herhangi bir eylem ekleyin. Eylemde, **sayı** değişkeninden değeri ekleyin, örneğin:

   ![Sonuçları gönderen bir eylem ekleme](./media/logic-apps-create-variables-store-values/send-email-results.png)

1. Mantıksal uygulamanızı kaydedin. Tasarımcı araç çubuğunda **Kaydet**' i seçin.

### <a name="test-your-logic-app"></a>Mantıksal uygulamanızı test etme

1. Mantıksal uygulamanız etkinleştirilmemişse, mantıksal uygulama menüsünde **genel bakış**' ı seçin. Araç çubuğunda **Etkinleştir**' i seçin.

1. Mantıksal uygulama Tasarımcısı araç çubuğunda **Çalıştır**' ı seçin. Bu adım mantıksal uygulamanızı el ile başlatır.

1. Bu örnekte kullandığınız e-posta hesabına bir veya daha fazla ek içeren bir e-posta gönderin.

   Bu adım, mantıksal uygulamanın iş akışı için bir örnek oluşturup çalıştıran mantıksal uygulamanın tetikleyicisini tetikler. Sonuç olarak, mantıksal uygulama size gönderdiğiniz e-postadaki eklerin sayısını gösteren bir ileti veya e-posta gönderir.

Tasarımcıdan kod görünümü düzenleyicisine geçiş yaparsanız, **her döngü için** , mantıksal uygulama TANıMıNıZDA, JSON biçiminde olan **değişken artırma** eylemiyle birlikte görünür.

```json
"actions": {
   "For_each": {
      "type": "Foreach",
      "actions": {
         "Increment_variable": {
           "type": "IncrementVariable",
            "inputs": {
               "name": "Count",
               "value": 1
            },
            "runAfter": {}
         }
      },
      "foreach": "@triggerBody()?['Attachments']",
      "runAfter": {
         "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

<a name="decrement-value"></a>

## <a name="decrement-variable"></a>Değişken azaltma

Bir değişkeni sabit *bir değere göre azaltmak veya azaltmak* için, değişken **azaltma** eylemini bulup seçmeniz dışında [bir değişkeni artırma](#increment-value) adımlarını izleyin. Bu eylem yalnızca tamsayı ve kayan değişkenlerle birlikte kullanılabilir.

**Değişken azaltma** eyleminin özellikleri şunlardır:

| Özellik | Gerekli | Value |  Açıklama |
|----------|----------|-------|--------------|
| **Name** | Evet | <*değişken adı*> | Azaltılacak değişkenin adı | 
| **Değer** | Hayır | <*artış değeri*> | Değişkeni azaltma değeri. Varsayılan değer bir değeridir. <p><p>**İpucu**: İsteğe bağlı olsa da, bu değeri en iyi uygulama olarak ayarlarsanız, değişkeninizi azaltma için her zaman belirli bir değeri bilirsiniz. |
||||| 

Tasarımcıdan kod görünümü düzenleyicisine geçiş yaparsanız, **azaltma değişkeni** EYLEMININ, JSON biçiminde olan mantıksal uygulama tanımınızda görünme yolu vardır.

```json
"actions": {
   "Decrement_variable": {
      "type": "DecrementVariable",
      "inputs": {
         "name": "Count",
         "value": 1
      },
      "runAfter": {}
   }
},
```

<a name="assign-value"></a>

## <a name="set-variable"></a>Değişken ayarla

Varolan bir değişkene farklı bir değer atamak için, [bir değişkeni](#increment-value) şu şekilde artırma adımlarını izleyin:

1. Bunun yerine **değişkeni ayarla** eylemini bulup seçin.

1. Atamak istediğiniz değişken adı ve değeri belirtin. Hem yeni değer hem de değişken aynı veri türüne sahip olmalıdır. Bu eylem varsayılan bir değere sahip olmadığından değer gereklidir.

**Değişken ayarla** eyleminin özellikleri şunlardır:

| Özellik | Gerekli | Value |  Açıklama |
|----------|----------|-------|--------------|
| **Name** | Evet | <*değişken adı*> | Değiştirilecek değişkenin adı |
| **Değer** | Evet | <*Yeni değer*> | Değişkeni atamak istediğiniz değer. Her ikisi de aynı veri türüne sahip olmalıdır. |
||||| 

> [!NOTE]
> Değişkenleri artırmadığınız veya azaltıyorsanız, döngüler içindeki değişkenlerin değiştirilmesi beklenmeyen sonuçlar *oluşturabilir,* çünkü döngüler paralel veya eşzamanlı olarak çalışır, varsayılan olarak. Bu gibi durumlarda, döngülerinizi sıralı olarak çalışacak şekilde ayarlamayı deneyin. Örneğin, döngü içindeki değişken değerine başvurmak istediğinizde ve bu döngü örneğinin başlangıcında ve sonunda aynı değeri bekleliyorsanız, döngünün nasıl çalışacağını değiştirmek için aşağıdaki adımları izleyin: 
>
> 1. Döngünüz sağ üst köşesinde üç nokta ( **...** ) düğmesini ve ardından **Ayarlar**' ı seçin.
> 
> 2. **Eşzamanlılık denetimi**altında, **geçersiz kılma varsayılan** ayarını **Açık**olarak değiştirin.
>
> 3. **Paralellik** sürgüsünün derecesini **1**' e sürükleyin.

Tasarımcıdan kod görünümü düzenleyicisine geçiş yaparsanız, **değişken ayarla** EYLEMININ, JSON biçiminde olan mantıksal uygulama tanımınızın içinde görünme yolu vardır. Bu örnek, `Count` değişkenin geçerli değerini başka bir değer olarak değiştirir.

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "Count",
               "type": "Integer",
               "value": 0
          } ]
      },
      "runAfter": {}
   },
   "Set_variable": {
      "type": "SetVariable",
      "inputs": {
         "name": "Count",
         "value": 100
      },
      "runAfter": {
         "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

<a name="append-value"></a>

## <a name="append-to-variable"></a>Değişkene Ekle

Dizeleri veya dizileri depolayan değişkenler için, değişkenin değerini bu dizelerde veya dizilerde son öğe olarak *ekleyebilir veya ekleyebilirsiniz* . Bunun yerine aşağıdaki adımları izlemeniz dışında, [bir değişkeni artırma](#increment-value) adımlarını izleyebilirsiniz: 

1. Değişkeninizin bir dize mi yoksa dizi mu olduğunu temel alarak bu eylemlerden birini bulun ve seçin: 

   * **Dize değişkenine Ekle**
   * **Dizi değişkenine Ekle** 

1. Dizedeki veya dizide son öğe olarak eklenecek değeri belirtin. Bu değer gereklidir.

**Append...** eylemlerinin özellikleri şunlardır:

| Özellik | Gerekli | Value |  Açıklama |
|----------|----------|-------|--------------|
| **Name** | Evet | <*değişken adı*> | Değiştirilecek değişkenin adı |
| **Değer** | Evet | <*Append-değer*> | Eklemek istediğiniz değer, herhangi bir tür içerebilir |
|||||

Tasarımcıdan kod görünümü düzenleyicisine geçiş yaparsanız, **dizi değişkenine Ekle** eyleminin JSON biçiminde olan mantıksal uygulama tanımınızın içinde görünme yolu vardır. Bu örnek, bir dizi değişkeni oluşturur ve dizideki son öğe olarak başka bir değer ekler. Sonuçlarınız, bu diziyi içeren güncelleştirilmiş bir değişkendir:`[1,2,3,"red"]`

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
            "name": "myArrayVariable",
            "type": "Array",
            "value": [1, 2, 3]
         } ]
      },
      "runAfter": {}
   },
   "Append_to_array_variable": {
      "type": "AppendToArrayVariable",
      "inputs": {
         "name": "myArrayVariable",
         "value": "red"
      },
      "runAfter": {
        "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

## <a name="next-steps"></a>Sonraki adımlar

* [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin
