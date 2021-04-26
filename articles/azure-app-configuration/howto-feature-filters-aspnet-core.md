---
title: Koşullu Özellik bayraklarını etkinleştirmek için özellik filtrelerini kullanın
titleSuffix: Azure App Configuration
description: Koşullu Özellik bayraklarını etkinleştirmek için özellik filtrelerini nasıl kullanacağınızı öğrenin
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
author: AlexandraKemperMS
ms.author: alkemper
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 39455c4bc193cce036bd169c702b5c020d53d2f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98602270"
---
# <a name="use-feature-filters-to-enable-conditional-feature-flags"></a>Koşullu Özellik bayraklarını etkinleştirmek için özellik filtrelerini kullanın

Özellik bayrakları, uygulamanızdaki işlevselliği etkinleştirmenizi veya devre dışı bırakabilmeniz için izin verir. Basit bir özellik bayrağı açık veya kapalı olmalıdır. Uygulama her zaman aynı şekilde davranır. Örneğin, bir özellik bayrağının arkasında yeni bir özellik alabilirsiniz. Özellik bayrağı etkinleştirildiğinde, tüm kullanıcılar yeni özelliği görür. Özellik bayrağını devre dışı bırakmak yeni özelliği gizler.

Buna karşılık, _koşullu Özellik bayrağı_ Özellik bayrağının dinamik olarak etkinleştirilmesini veya devre dışı bırakılbilmesini sağlar. Uygulama, özellik bayrağı ölçütlerine bağlı olarak farklı şekilde davranmayabilir. Yeni özelliğinizi ilk başta kullanıcıların küçük bir alt kümesine göstermek istediğinizi varsayalım. Koşullu Özellik bayrağı, bazı kullanıcılar için özellik bayrağını diğerleri için devre dışı bırakarak etkinleştirmenizi sağlar. _Özellik filtreleri_ her değerlendirildiğinde Özellik bayrağının durumunu tespit edin.

`Microsoft.FeatureManagement`Kitaplık üç özellik filtresi içerir:

- `PercentageFilter` Özellik bayrağını bir yüzdeye göre izin vermez.
- `TimeWindowFilter` belirli bir zaman penceresi sırasında özellik bayrağını sunar.
- `TargetingFilter` Belirtilen kullanıcılar ve gruplar için özellik bayrağını sunar.

Ayrıca, [Microsoft. FeatureManagement. IFeatureFilter arabirimini](/dotnet/api/microsoft.featuremanagement.ifeaturefilter)uygulayan kendi özellik filtreinizi de oluşturabilirsiniz.

## <a name="registering-a-feature-filter"></a>Özellik filtresini kaydetme

`AddFeatureFilter`Yöntemi çağırarak, istenen özellik filtresinin tür adını belirterek bir özellik filtresi kaydedersiniz. Örneğin, aşağıdaki kod kaydedilir `PercentageFilter` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddFeatureManagement().AddFeatureFilter<PercentageFilter>();
}
```

## <a name="configuring-a-feature-filter-in-azure-app-configuration"></a>Azure Uygulama yapılandırmasında bir özellik filtresi yapılandırma

Bazı özellik filtrelerinin ek ayarları vardır. Örneğin, `PercentageFilter` bir yüzdeyi temel alarak bir özelliği etkinleştirir. Kullanılacak yüzdeyi tanımlayan bir ayara sahiptir.

Bu ayarları, Azure Uygulama yapılandırmasında tanımlanan özellik bayrakları için yapılandırabilirsiniz. Örneğin, `PercentageFilter` bir Web uygulamasına yönelik isteklerin %50 ' i için özellik bayrağını etkinleştirmek üzere kullanmak için şu adımları izleyin:

1. Bir özellik bayrağıyla Web uygulaması oluşturmak için [hızlı başlangıç: ASP.NET Core uygulamasına özellik bayrakları ekleme](./quickstart-feature-flag-aspnet-core.md) ' deki yönergeleri izleyin.

1. Azure portal, yapılandırma deponuza gidin ve **Feature Manager**' a tıklayın.

1. Hızlı başlangıçta oluşturduğunuz *Beta* özelliği bayrağının bağlam menüsüne tıklayın. **Düzenle**’ye tıklayın.

    > [!div class="mx-imgBorder"]
    > ![Beta Özellik bayrağını Düzenle](./media/edit-beta-feature-flag.png)

1. **Düzenleme** ekranında, zaten etkinleştirilmemişse **özellik bayrağını etkinleştir** onay kutusunu işaretleyin. Sonra **özellik filtresini kullan** onay kutusunu Işaretleyin ve **özel**' i seçin. 

1. **Ad** alanında *Microsoft. Percentage*' i seçin.

    > [!div class="mx-imgBorder"]
    > ![Özellik filtresi ekle](./media/feature-flag-add-filter.png)

1. Özellik filtresi adının yanındaki bağlam menüsüne tıklayın. **Filtre parametrelerini düzenle**' ye tıklayın.

    > [!div class="mx-imgBorder"]
    > ![Özellik filtresi parametrelerini Düzenle](./media/feature-flags-edit-filter-parameters.png)

1. Bir değer **adı** ve  50 **değeri** girin. **Değer** alanı, özellik filtresi etkinleştirilecek isteklerin yüzdesini gösterir.

    > [!div class="mx-imgBorder"]
    > ![Özellik filtresi parametrelerini ayarla](./media/feature-flag-set-filter-parameters.png)

1. **Özellik düzenleme bayrağı** ekranına dönmek için **Uygula** ' ya tıklayın. Ardından, özellik bayrağı ayarlarını kaydetmek için yeniden **Uygula** ' ya tıklayın.

1. Özellik **Yöneticisi** sayfasında, özellik bayrağının artık *özel* bir **özellik filtresi** değeri vardır. 

    > [!div class="mx-imgBorder"]
    > ![Özellik bayrağı, "Custom" özellik filtresi değeri ile listelendi](./media/feature-flag-filter-custom.png)

## <a name="feature-filters-in-action"></a>Eylemde özellik filtreleri

Bu özellik bayrağının etkilerini görmek için uygulamayı başlatın ve tarayıcınızdaki **yenileme** düğmesine birden çok kez basın. *Beta* öğesinin araç çubuğunda yaklaşık %50 ile göründüğünü görürsünüz. `PercentageFilter`Bir istek alt kümesi Için *Beta* özelliğini devre dışı bıraktığında, bu süre geri kalanı gizlidir. Aşağıdaki videoda bu davranış eylemi gösterilmektedir.

> [!div class="mx-imgBorder"]
> ![TargetingFilter eylemi](./media/feature-flags-percentagefilter.gif)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Hedeflenen izleyiciler için özelliklerin aşamalı dağıtımını etkinleştir](./howto-targetingfilter-aspnet-core.md)
