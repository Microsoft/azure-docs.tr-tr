---
title: Şema güncelleştirmeleri Haziran 1-2016-Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps 'de Logic App tanımları için şema sürümü 2016-06-01 güncelleştirildi
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: 349d57e8-f62b-4ec6-a92f-a6e0242d6c0e
ms.topic: article
ms.date: 07/25/2016
ms.openlocfilehash: 0558c309cc22f39c2ed439b7930443ca0adb071e
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385384"
---
# <a name="schema-updates-for-azure-logic-apps---june-1-2016"></a>Azure Logic Apps için şema güncelleştirmeleri-1 Haziran 2016

Azure Logic Apps için [güncelleştirilmiş şema](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json) ve API sürümü, mantıksal uygulamaları daha güvenilir ve kullanımı kolay hale getirmek için önemli geliştirmeler içerir:

* [Kapsamlar](#scopes) eylemleri bir dizi koleksiyon olarak gruplandırmasına veya yuvadeğiştirmenize olanak sağlar.
* [Koşullar ve döngüler](#conditions-loops) artık birinci sınıf eylemlerdir.
* `runAfter` Özelliği ile eylemleri çalıştırmaya yönelik daha kesin sıralama, değiştirme`dependsOn`

Logic Apps 'i 1 Ağustos 2015 Preview şemasından 1 Haziran 2016 şemasına yükseltmek için [Yükseltme bölümüne bakın](#upgrade-your-schema).

<a name="scopes"></a>

## <a name="scopes"></a>Kapsamlar

Bu şema, eylemleri birlikte gruplandırmanızı sağlayan kapsamları ve birbirini iç içe bir şekilde gruplamanıza olanak sağlayan kapsamları içerir. Örneğin, bir koşul başka bir koşul içerebilir. [Kapsam sözdizimi](../logic-apps/logic-apps-loops-and-scopes.md)hakkında daha fazla bilgi edinin veya bu temel kapsam örneğini gözden geçirin:

```json
{
   "actions": {
      "Scope": {
         "type": "Scope",
         "actions": {                
            "Http": {
               "inputs": {
                   "method": "GET",
                   "uri": "https://www.bing.com"
               },
               "runAfter": {},
               "type": "Http"
            }
         }
      }
   }
}
```

<a name="conditions-loops"></a>

## <a name="conditions-and-loops-changes"></a>Koşullar ve döngüler değişiklikleri

Önceki şema sürümlerinde, koşullar ve döngüler tek bir eylemle ilişkili parametrelerdi. Bu şema bu kısıtlamayı durdurur, bu nedenle koşullar ve döngüler artık eylem türleri olarak kullanılabilir. [Döngüler ve kapsamlar](../logic-apps/logic-apps-loops-and-scopes.md), [koşullar](../logic-apps/logic-apps-control-flow-conditional-statement.md)hakkında daha fazla bilgi edinin veya bir koşul eylemi gösteren bu temel örneği gözden geçirin:

```json
{
   "Condition - If trigger is some trigger": {
      "type": "If",
      "expression": "@equals(triggerBody(), '<trigger-name>')",
      "runAfter": {},
      "actions": {
         "Http_2": {
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com"
            },
            "runAfter": {},
            "type": "Http"
         }
      },
      "else": 
      {
         "Condition - If trigger is another trigger": {}
      }  
   }
}
```

<a name="run-after"></a>

## <a name="runafter-property"></a>' runAfter ' özelliği

`runAfter` Özelliği,öncekieylemlerindurumunagöreeylemleriçinÇalıştırmasırasınıbelirttiğinizde`dependsOn`daha fazla duyarlık sağlar. `dependsOn` Özelliği, "eylem çalıştı ve başarılı oldu" olarak, önceki eylemin başarılı, başarısız ya da bu eylemi çalıştırmak istediğiniz kaç kez atlanıp atlanmadığını belirtti. `runAfter` Özelliği, nesnenin çalıştıktan sonra tüm eylem adlarını belirten bir nesne olarak esneklik sağlar. Bu özellik ayrıca tetikleyici olarak kabul edilebilir bir durumlar dizisini tanımlar. Örneğin, eylem başarılı olduktan sonra bir eylemin çalışmasını istiyorsanız ve ayrıca işlem başarılı veya başarısız olduktan sonra, bu `runAfter` özelliği ayarlayın:

```json
{
   // Other parts in action definition
   "runAfter": {
      "A": ["Succeeded"],
      "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrade-your-schema"></a>Şemanızı yükseltme

[En son şemaya](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json)yükseltmek için yalnızca birkaç adım gerçekleştirmeniz gerekir. Yükseltme işlemi, yükseltme betiğini çalıştırmayı, yeni bir mantıksal uygulama olarak kaydetmeyi ve isterseniz, büyük olasılıkla önceki mantıksal uygulamanın üzerine yazılmasını içerir.

1. Azure portal mantıksal uygulamanızı açın.

2. **Genel Bakış ' a**gidin. Mantıksal uygulama araç çubuğunda **Şemayı Güncelleştir**' i seçin.
   
   ![Şemayı Güncelleştir ' i seçin][1]
   
   Yükseltilen tanım döndürülür ve gerekirse bir kaynak tanımına kopyalayabilir ve yapıştırabilirsiniz. 

   > [!IMPORTANT]
   > **Farklı kaydet** ' i *seçtiğinizden emin olun* , böylece tüm bağlantı başvurularının yükseltilen mantıksal uygulamada geçerli kalmasını sağlayın.

3. Yükseltme dikey penceresinde **farklı kaydet**' i seçin.

4. Mantıksal adı ve durumu girin. Yükseltilen mantıksal uygulamanızı dağıtmak için **Oluştur**' u seçin.

5. Yükseltilen mantıksal uygulamanızın beklendiği gibi çalıştığından emin olun.
   
   > [!NOTE]
   > El ile veya istek tetikleyicisi kullanıyorsanız, geri çağırma URL 'SI yeni mantıksal uygulamanızda değişir. Uçtan uca deneyimin çalıştığından emin olmak için yeni URL 'YI test edin. Önceki URL 'Leri korumak için, mevcut mantıksal uygulamanızı kopyalayabilirsiniz.

6. *Isteğe bağlı* Önceki mantıksal uygulamanızın yeni şema sürümüyle üzerine yazmak için, araç çubuğunda, **Şemayı Güncelleştir**' ın yanındaki **Kopyala**' yı seçin. Bu adım yalnızca mantıksal uygulamanızın aynı kaynak KIMLIĞINI veya istek tetikleyicisi URL 'sini tutmak istiyorsanız gereklidir.

## <a name="upgrade-tool-notes"></a>Yükseltme aracı notları

### <a name="mapping-conditions"></a>Eşleme koşulları

Yükseltilen tanımda araç, doğru ve yanlış dal eylemlerini bir kapsam olarak gruplandırmada en iyi çabayı yapar. Özellikle tasarımcı deseninin `@equals(actions('a').status, 'Skipped')` bir `else` eylem olarak görünmesi. Ancak araç tanınmayan desenler algılarsa, araç hem true hem de false dalı için ayrı koşullar oluşturabilir. Gerekirse, yükseltmeden sonra eylemleri yeniden eşleyebilirsiniz.

#### <a name="foreach-loop-with-condition"></a>Condition ile ' foreach ' döngüsü

Yeni şemada, her bir döngü **için** bir öğe başına bir koşul kullanan düzeni çoğaltmak için filtre eylemini kullanabilirsiniz. Ancak, yükselttiğinizde değişiklik otomatik olarak gerçekleşir. Koşul, **her** bir döngüden önce görüntülenen, yalnızca koşulla eşleşen bir öğe dizisi döndüren ve bu diziyi **her eylem için** geçirerek bir filtre eylemi olur. Bir örnek için bkz. [döngüler ve kapsamlar](../logic-apps/logic-apps-loops-and-scopes.md).

### <a name="resource-tags"></a>Kaynak etiketleri

Yükseltmeden sonra kaynak etiketleri kaldırılır, bu nedenle bunları yükseltilen iş akışı için sıfırlamanız gerekir.

## <a name="other-changes"></a>Diğer değişiklikler

### <a name="renamed-manual-trigger-to-request-trigger"></a>' Manual ' tetikleyicisi ' Request ' tetikleyicisi olarak yeniden adlandırıldı

Tetikleyici türü kullanımdan kaldırılmıştır ve türüyle `http`olarak `request` yeniden adlandırıldı. `manual` Bu değişiklik, tetikleyicinin derlemek için kullanılan model türü için daha fazla tutarlılık oluşturur.

### <a name="new-filter-action"></a>Yeni ' filtre ' eylemi

Büyük bir diziyi daha küçük bir öğe kümesine filtrelemek için, yeni `filter` tür bir diziyi ve koşulu kabul eder, her öğe için koşulu değerlendirir ve koşulu karşılayan öğeleri içeren bir dizi döndürür.

### <a name="restrictions-for-foreach-and-until-actions"></a>' Foreach ' ve ' Until ' eylemlerine yönelik kısıtlamalar

`foreach` Ve`until` döngüsü tek bir eylemle kısıtlıdır.

### <a name="new-trackedproperties-for-actions"></a>Eylemler için yeni ' trackedProperties '

Eylemler artık `trackedProperties` `runAfter` ve özelliklerineeşdüzeyolanadlıekbirözelliğesahipolabilir.`type` Bu nesne, bir iş akışının parçası olarak yayınlanan Azure tanılama telemetrisine dahil etmek istediğiniz belirli eylem girdilerini veya çıkışları belirtir. Örneğin:

``` json
{
   "Http": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com"
      },
      "runAfter": {},
      "type": "Http",
      "trackedProperties": {
         "responseCode": "@action().outputs.statusCode",
         "uri": "@action().inputs.uri"
      }
   }
}
```

## <a name="next-steps"></a>Sonraki adımlar

* [Logic Apps için iş akışı tanımları oluşturma](../logic-apps/logic-apps-author-definitions.md)
* [Mantıksal uygulama dağıtımını otomatikleştirme](logic-apps-azure-resource-manager-templates-overview.md)

<!-- Image references -->
[1]: ./media/logic-apps-schema-2016-04-01/upgradeButton.png
