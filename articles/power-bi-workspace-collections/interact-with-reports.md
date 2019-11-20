---
title: JavaScript API’sini kullanarak raporlarla etkileşim kurma | Microsoft Belgeleri
description: Power BI JavaScript API’si, Power BI raporlarını uygulamalarınıza kolaylıkla eklemenizi sağlar.
services: power-bi-workspace-collections
ms.service: power-bi-embedded
author: rkarlin
ms.author: rkarlin
ms.topic: conceptual
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: 8203070fa8a48420dd192208701c539dc3ca7f25
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67669073"
---
# <a name="interact-with-power-bi-reports-using-the-javascript-api"></a>JavaScript API’sini kullanarak Power BI raporlarıyla etkileşim kurma

Power BI JavaScript API’si, Power BI raporlarını uygulamalarınıza kolaylıkla eklemenizi sağlar. API ile uygulamalarınız sayfalar ve filtreler gibi farklı rapor öğeleri ile program aracılığıyla etkileşim kurabilir. Bu etkileşim Power BI raporlarını uygulamanızın daha tümleşik bir parçası yapar.

> [!IMPORTANT]
> Power BI Çalışma Alanı Koleksiyonları kullanım dışı bırakılmıştır ve Haziran 2018'e kadar veya anlaşmanızda belirtilen süre boyunca kullanılabilecektir. Uygulamanızda kesinti yaşanmaması için Power BI Embedded'a geçirmeyi planlamanız önerilir. Verilerinizi Power BI Embedded'a nasıl taşıyacağınızı öğrenmek için bkz. [Power BI Çalışma Alanı Koleksiyonları'nı Power BI Embedded'a geçirme](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Uygulamanızın bir parçası olarak barındırılacak Power BI raporunu uygulamaya iframe kullanarak ekleyin. Aşağıdaki görüntüde görebileceğiniz gibi bu iframe uygulamanızla rapor arasında sınır işlevi görür:

![Javascript API'si içermeyen Power BI Çalışma Alanı Koleksiyonu iframe'i](media/interact-with-reports/iframe-without-javacript.png)

iframe katıştırma işlemini çok daha kolaylaştırır, ancak JavaScript API’si olmadan rapor ve uygulamanız birbiriyle etkileşim kuramaz. Bu etkileşimin kurulamaması raporun uygulamanızın bir parçası değilmiş gibi görünmesine neden olabilir. Rapor ve uygulama aşağıdaki görüntüde olduğu gibi çift yönlü bir iletişim kurmalıdır:

![Javascript API'si içeren Power BI Çalışma Alanı Koleksiyonu iframe'i](media/interact-with-reports/iframe-with-javascript.png)

Power BI JavaScript API’si iframe sınırından güvenli bir şekilde geçebilecek bir kod yazmanızı sağlar. Bunun yapılması uygulamanızın bir raporda program aracılığıyla eylem gerçekleştirmesini ve kullanıcıların rapor içinde gerçekleştirdiği eylemlerdeki olayları dinlemesini sağlar.

## <a name="what-can-you-do-with-the-power-bi-javascript-api"></a>Power BI JavaScript API’si ile neler yapabilirsiniz?

JavaScript API’si ile raporları yönetebilir, bir rapordaki sayfalarda gezinebilir, raporu filtreleyebilir ve katıştırma olaylarını gerçekleştirebilirsiniz. Aşağıdaki diyagramda API’nin yapısı gösterilmektedir.

![Power BI JavaScript API’si diyagramı](media/interact-with-reports/javascript-api-diagram.png)

### <a name="manage-reports"></a>Raporları yönetme
Javascript API’si rapor ve sayfa düzeyindeki davranışı yönetmenizi sağlar:

* Belirli bir Power BI Raporunu uygulamanıza güvenli bir şekilde katıştırma - [katıştırma demo uygulamasını](https://azure-samples.github.io/powerbi-angular-client/#/scenario1) deneyin
  * Erişim belirteci ayarlama
* Raporu yapılandırma
  * Filtre bölmesini ve sayfa gezinti bölmesini etkinleştirme ve devre dışı bırakma - [ayar güncelleştirme demo uygulamasını](https://azure-samples.github.io/powerbi-angular-client/#/scenario6) deneyin
  * Sayfalar ve filtreler için varsayılanları ayarlama - [varsayılanları ayarlama demosunu](https://azure-samples.github.io/powerbi-angular-client/#/scenario5) deneyin
* Tam ekran moduna giriş ve çıkış

[Bir raporu ekleme hakkında daha fazla bilgi edinin](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embedding-Basics)

### <a name="navigate-to-pages-in-a-report"></a>Rapordaki sayfalarda gezinme
JavaScript API'si bir rapordaki tüm sayfaları bulmanızı ve geçerli sayfayı ayarlamanızı sağlar. [Gezinti demo uygulamasını](https://azure-samples.github.io/powerbi-angular-client/#/scenario3) deneyin.

[Sayfa gezintisi hakkında daha fazla bilgi edinin](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Page-Navigation)

### <a name="filter-a-report"></a>Bir raporu filtreleme
JavaScript API’si katıştırılmış raporlar ve rapor sayfaları için temel ve gelişmiş filtreleme özellikleri sağlar. [Filtreleme demo uygulamasını](https://azure-samples.github.io/powerbi-angular-client/#/scenario4) deneyin ve giriş niteliğindeki bazı kodları burada gözden geçirin.

#### <a name="basic-filters"></a>Temel filtreler
Temel filtre bir sütuna veya hiyerarşi düzeyine yerleştirilir ve dahil edilecek ya da hariç tutulacak değerler listesini içerir.

```typescript
const basicFilter: pbi.models.IBasicFilter = {
  $schema: "https://powerbi.com/product/schema#basic",
  target: {
    table: "Store",
    column: "Count"
  },
  operator: "In",
  values: [1,2,3,4]
}
```

#### <a name="advanced-filters"></a>Gelişmiş filtreler
Gelişmiş filtreler AND veya OR mantıksal işlecini kullanır ve her biri kendi işlecine ve değerine sahip bir ya da iki koşulu kabul eder. Desteklenen koşullar şunlardır:

* None
* LessThan
* LessThanOrEqual
* GreaterThan
* GreaterThanOrEqual
* Contains
* DoesNotContain
* StartsWith
* DoesNotStartWith
* Is
* IsNot
* IsBlank
* IsNotBlank

```typescript
const advancedFilter: pbi.models.IAdvancedFilter = {
  $schema: "https://powerbi.com/product/schema#advanced",
  target: {
    table: "Store",
    column: "Name"
  },
  logicalOperator: "Or",
  conditions: [
    {
      operator: "Contains",
      value: "Wash"
    },
    {
      operator: "Contains",
      value: "Park"
    }
  ]
}
```

[Filtreleme hakkında daha fazla bilgi edinin](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Filters)

### <a name="handling-events"></a>Olayları işleme

iframe’e bilgi göndermeye ek olarak uygulamanız iframe’den gelen aşağıdaki olaylara ilişkin bilgi de alabilir:

* Embed
  * loaded
  * error
* Raporlar
  * pageChanged
  * dataSelected (yakında)

[Olayları işleme hakkında daha fazla bilgi edinin](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Handling-Events)

## <a name="next-steps"></a>Sonraki adımlar

Power BI JavaScript API’si hakkında daha fazla bilgi için aşağıdaki bağlantılara göz atın:

* [JavaScript API’si Wiki](https://github.com/Microsoft/PowerBI-JavaScript/wiki)
* [Nesne modeli başvurusu](https://microsoft.github.io/powerbi-models/modules/_models_.html)
* [Canlı tanıtım](https://microsoft.github.io/PowerBI-JavaScript/demo/)