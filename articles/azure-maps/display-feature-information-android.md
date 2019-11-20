---
title: Özellik bilgilerini Azure Maps Android SDK görüntüleyin | Microsoft Docs
description: Özellik bilgilerini Azure Maps Android SDK görüntülemeyi öğrenin.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 0dcabb0e5141a92394f2be38cbe7e71fa6e03d58
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73928548"
---
# <a name="display-feature-information"></a>Özellik bilgilerini görüntüleme

Uzamsal veriler genellikle noktaları, çizgileri ve çokgenler kullanılarak temsil edilir. Bu verilere genellikle ilişkili meta veri bilgileri sahip olur. Örneğin, bir nokta bir deponun konumunu ve bu restoran hakkındaki meta verileri temsil eden ad, adres ve hizmet verdiği yiyecek türü olabilir. Bu meta veriler, bir `JsonObject`kullanılarak bu özelliklerin özellikleri olarak eklenebilir. Aşağıdaki kod, "Merhaba Dünya!" değerine sahip `title` bir özelliği olan basit bir nokta özelliği oluşturur

```java
//Create a data source and add it to the map.
DataSource dataSource = new DataSource();
map.sources.add(dataSource);

//Create a point feature with some properties and add it to the data source.
JsonObject properties = new JsonObject();
properties.addProperty("title", "Hello World!");

//Create a point feature, pass in the metadata properties, and add it to the data source.
dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64), properties));
```

Kullanıcı haritada bir özellik ile etkileşime geçtiğinde, olaylar bu eylemlere tepki vermek için kullanılabilir. Yaygın bir senaryo, Kullanıcı tarafından etkileşim kurulan bir özelliğin meta veri özelliklerinden oluşan bir ileti görüntülemektir. `OnFeatureClick` olay, kullanıcının haritadaki bir özelliği dokunduğunda tespit etmek için kullanılan ana olaydır. Ayrıca `OnLongFeatureClick` bir olay da vardır. `OnFeatureClick` olayı haritaya eklenirken, bir katmanın kimliğini ile sınırlamak için bir katman kimliğine geçerek tek bir katmanla sınırlı olabilir. Katman KIMLIĞI geçirilmemişse, hangi katmanda olduğuna bakılmaksızın haritadaki herhangi bir özelliğe dokunarak bu olayı tetiklersiniz. Aşağıdaki kod, haritada nokta verisi işlemek için bir sembol katmanı oluşturur, sonra bir `OnFeatureClick` olayı ekler ve bu sembol katmanına kısıtlar.

```java
//Create a symbol and add it to the map.
SymbolLayer symbolLayer = new SymbolLayer(dataSource);
map.layers.add(symbolLayer);

//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).properties().get("title").getAsString();

    //Do something with the message.
}, symbolLayer.getId());    //Limit this event to the symbol layer.
```

## <a name="display-a-toast-message"></a>Bildirim iletisi görüntüle

Bildirim iletisi, kullanıcıya bilgi görüntülemenin en kolay yollarından biridir ve Android 'in tüm sürümlerinde kullanılabilir. Her tür Kullanıcı girişini desteklemez ve yalnızca kısa bir süre için görüntülenir. Kullanıcının ne dokundukları hakkında bir şey bilmesini hızlıca sağlamak istiyorsanız, bir bildirim iletisi iyi bir seçenek olabilir. Aşağıdaki kod, `OnFeatureClick` olayla bir bildirim iletisinin nasıl kullanılabileceğini gösterir.

```java
//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();
}, symbolLayer.getId());    //Limit this event to the symbol layer.
```

<center>

dokunmakta olan bir özelliğin animasyonunu ![ve bir bildirim iletisi görüntülendi](./media/display-feature-information-android/symbol-layer-click-toast-message.gif)</center>

Mesajların yanı sıra, bir özelliğin meta veri özelliklerini sunmak için birçok farklı yol vardır:

- [Snakbar pencere öğesi](https://developer.android.com/training/snackbar/showing.html) -snackçubuklar bir işlem hakkında hafif geri bildirim sağlar. Bu kişiler, ekranın alt kısmında mobil ve daha büyük cihazlarda sol alt kısımdaki kısa bir ileti gösterir. Snackçubuklar, ekrandaki diğer tüm öğelerin üstünde görünür ve tek seferde yalnızca bir gösterilebilir.
- İletişim [kutuları](https://developer.android.com/guide/topics/ui/dialogs) -bir iletişim kutusu, kullanıcıdan karar vermesini veya ek bilgiler girmesini isteyen küçük bir penceredir. Bir iletişim kutusu, ekranı doldurmaz ve normalde, kullanıcıların devam etmeden önce bir eylemde bulunması gereken kalıcı olaylar için kullanılır.
- Geçerli etkinliğe bir [parça](https://developer.android.com/guide/components/fragments) ekleyin.
- Başka bir etkinliğe veya görünüme gidin.

## <a name="next-steps"></a>Sonraki adımlar

Haritanızda daha fazla veri eklemek için:

> [!div class="nextstepaction"]
> [Sembol katmanı ekleme](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Android haritasına şekil ekleme](how-to-add-shapes-to-android-map.md)
