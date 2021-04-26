---
title: Android haritalar 'da Özellik bilgilerini görüntüleme | Microsoft Azure haritaları
description: Kullanıcılar harita özellikleriyle etkileşim kurarken bilgileri görüntülemeyi öğrenin. Bildirim iletilerini ve diğer ileti türlerini göstermek için Azure Haritalar Android SDK kullanın.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: ea9ed2c74651a7719533340a24c4741d4e25b805
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605731"
---
# <a name="display-feature-information"></a>Özellik bilgilerini görüntüleme

Uzamsal veriler genellikle noktaları, çizgileri ve çokgenler kullanılarak temsil edilir. Bu verilere genellikle ilişkili meta veri bilgileri sahip olur. Örneğin, bir nokta, Restoran konumunu temsil edebilir ve bu restoran hakkında, hizmet verdiği yiyecek 'nin adı, adresi ve veri türü olabilir. Bu meta veriler coğrafi JSON 'ın özellikleri olarak eklenebilir `Feature` . Aşağıdaki kod, `title` "Merhaba Dünya!" değerine sahip bir özelliği olan basit bir nokta özelliği oluşturur

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64));

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!");

//Create a point feature, pass in the metadata properties, and add it to the data source.
source.add(feature);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64))

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!")

//Create a point feature, pass in the metadata properties, and add it to the data source.
source.add(feature)
```

::: zone-end

Verileri oluşturma ve haritaya ekleme yolları için [veri kaynağı oluşturma](create-data-source-android-sdk.md) belgelerine bakın.

Kullanıcı haritada bir özellik ile etkileşime geçtiğinde, olaylar bu eylemlere tepki vermek için kullanılabilir. Yaygın bir senaryo, Kullanıcı tarafından etkileşim kurulan bir özelliğin meta veri özelliklerinden oluşan bir ileti görüntülemektir. `OnFeatureClick`Olay, kullanıcının haritadaki bir özelliği dokunduğunda tespit etmek için kullanılan ana olaydır. Ayrıca bir olay da vardır `OnLongFeatureClick` . `OnFeatureClick`Olayı haritaya eklerken, bir katmanın kimliğini ile sınırlamak için bir katman kimliğine geçerek tek bir katmanla sınırlı olabilir. Katman KIMLIĞI geçirilmemişse, hangi katmanda olduğuna bakılmaksızın haritadaki herhangi bir özelliğe dokunarak bu olayı tetiklersiniz. Aşağıdaki kod, haritada nokta verisi işlemek için bir sembol katmanı oluşturur, sonra bir `OnFeatureClick` olay ekler ve bu sembol katmanına kısıtlar.

::: zone pivot="programming-language-java-android"

```java
//Create a symbol and add it to the map.
SymbolLayer layer = new SymbolLayer(source);
map.layers.add(layer);

//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Do something with the message.

    //Return a boolean indicating if event should be consumed or continue bubble up.
    return false;
}, layer.getId());    //Limit this event to the symbol layer.
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a symbol and add it to the map.
val layer = SymbolLayer(source)
map.layers.add(layer)

//Add a feature click event to the map.
map.events.add(OnFeatureClick { features: List<Feature> ->
    //Retrieve the title property of the feature as a string.
    val msg = features[0].getStringProperty("title")

    //Do something with the message.

    //Return a boolean indicating if event should be consumed or continue bubble up.
    return false
}, layer.getId()) //Limit this event to the symbol layer.
```

::: zone-end

## <a name="display-a-toast-message"></a>Bildirim iletisi görüntüle

Bildirim iletisi, kullanıcıya bilgi görüntülemenin en kolay yollarından biridir ve Android 'in tüm sürümlerinde kullanılabilir. Her tür Kullanıcı girişini desteklemez ve yalnızca kısa bir süre için görüntülenir. Kullanıcının ne dokundukları hakkında bir şey bilmesini hızlıca sağlamak istiyorsanız, bir bildirim iletisi iyi bir seçenek olabilir. Aşağıdaki kod, bir bildirim iletisinin olayla nasıl kullanılabileceğini gösterir `OnFeatureClick` .

::: zone pivot="programming-language-java-android"

```java
//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();

    //Return a boolean indicating if event should be consumed or continue bubble up.
    return false;
}, layer.getId());    //Limit this event to the symbol layer.
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Add a feature click event to the map.
map.events.add(OnFeatureClick { features: List<Feature> ->
    //Retrieve the title property of the feature as a string.
    val msg = features[0].getStringProperty("title")

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show()

    //Return a boolean indicating if event should be consumed or continue bubble up.
    return false
}, layer.getId()) //Limit this event to the symbol layer.
```

::: zone-end

![Dokunulmakta olan bir özelliğin animasyonu ve bir bildirim iletisi görüntüleniyor](media/display-feature-information-android/symbol-layer-click-toast-message.gif)

Mesajların yanı sıra, bir özelliğin meta veri özelliklerini sunmak için birçok farklı yol vardır:

- [Snackbar pencere öğesi](https://developer.android.com/training/snackbar/showing.html)  -  `Snackbars` bir işlem hakkında hafif geri bildirim sağlayın. Bu kişiler, ekranın alt kısmında mobil ve daha büyük cihazlarda sol alt kısımdaki kısa bir ileti gösterir. `Snackbars` Tüm diğer öğelerin ekranda görünmesini ve tek seferde yalnızca bir tane görüntülenmesini sağlayabilirsiniz.
- İletişim [kutuları](https://developer.android.com/guide/topics/ui/dialogs) -bir iletişim kutusu, kullanıcıdan karar vermesini veya ek bilgiler girmesini isteyen küçük bir penceredir. Bir iletişim kutusu, ekranı doldurmaz ve normalde, kullanıcıların devam etmeden önce bir eylemde bulunması gereken kalıcı olaylar için kullanılır.
- Geçerli etkinliğe bir [parça](https://developer.android.com/guide/components/fragments) ekleyin.
- Başka bir etkinliğe veya görünüme gidin.

## <a name="display-a-popup"></a>Açılan pencere görüntüle

Azure Haritalar Android SDK, `Popup` harita üzerindeki bir konuma SABITLENMIŞ UI ek açıklama öğelerini oluşturmayı kolaylaştıran bir sınıf sağlar. Açılanlar için, açılan menü seçeneğine göreli düzen ile bir görünüm geçirmeniz gerekir `content` . Aşağıda, bir while arka planının üzerine koyu metin görüntüleyen basit bir düzen örneği verilmiştir.

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:orientation="vertical"
    android:background="#ffffff"
    android:layout_margin="8dp"
    android:padding="10dp"

    android:layout_height="match_parent">

    <TextView
        android:id="@+id/message"
        android:layout_width="wrap_content"
        android:text=""
        android:textSize="18dp"
        android:textColor="#222"
        android:layout_height="wrap_content"
        android:width="200dp"/>

</RelativeLayout>
```

Yukarıdaki düzenin, bir uygulamanın klasöründe adlı bir dosyada depolandığını varsayarak, `popup_text.xml` `res -> layout` Aşağıdaki kod bir açılan pencere oluşturur ve Haritayı haritaya ekler. Bir özellik tıklandığında, `title` özellik düzen kullanılarak görüntülenir `popup_text.xml` ve düzen alt orta ile haritada belirtilen konuma sabitlenmiş olur.

::: zone pivot="programming-language-java-android"

```java
//Create a popup and add it to the map.
Popup popup = new Popup();
map.popups.add(popup);

map.events.add((OnFeatureClick)(feature) -> {
    //Get the first feature and it's properties.
    Feature f = feature.get(0);
    JsonObject props = f.properties();

    //Retrieve the custom layout for the popup.
    View customView = LayoutInflater.from(this).inflate(R.layout.popup_text, null);

    //Access the text view within the custom view and set the text to the title property of the feature.
    TextView tv = customView.findViewById(R.id.message);
    tv.setText(props.get("title").getAsString());

    //Get the coordinates from the clicked feature and create a position object.
    List<Double> c = ((Point)(f.geometry())).coordinates();
    Position pos = new Position(c.get(0), c.get(1));

    //Set the options on the popup.
    popup.setOptions(
        //Set the popups position.
        position(pos),

        //Set the anchor point of the popup content.
        anchor(AnchorType.BOTTOM),

        //Set the content of the popup.
        content(customView)

        //Optionally, hide the close button of the popup.
        //, closeButton(false)
    );

    //Open the popup.
    popup.open();

    //Return a boolean indicating if event should be consumed or continue bubble up.
    return false;
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a popup and add it to the map.
val popup = Popup()
map.popups.add(popup)

map.events.add(OnFeatureClick { feature: List<Feature> ->
    //Get the first feature and it's properties.
    val f = feature[0]
    val props = f.properties()

    //Retrieve the custom layout for the popup.
    val customView: View = LayoutInflater.from(this).inflate(R.layout.popup_text, null)

    //Access the text view within the custom view and set the text to the title property of the feature.
    val tv: TextView = customView.findViewById(R.id.message)
    tv.text = props!!["title"].asString

    //Get the coordinates from the clicked feature and create a position object.
    val c: List<Double> = (f.geometry() as Point?).coordinates()
    val pos = Position(c[0], c[1])

    //Set the options on the popup.
    popup.setOptions( 
        //Set the popups position.
        position(pos),  

        //Set the anchor point of the popup content.
        anchor(AnchorType.BOTTOM),  

        //Set the content of the popup.
        content(customView) 

        //Optionally, hide the close button of the popup.
        //, closeButton(false)
    )

    //Open the popup.
    popup.open()

    //Return a boolean indicating if event should be consumed or continue bubble up.
    return false
})
```

::: zone-end

Aşağıdaki ekran yakalama, Özellikler tıklandığında açılan pencereleri gösterir ve taşırken haritada belirtilen konumlarına tutturulduğu sırada görünür.

![Görüntülenmekte olan bir açılan pencerenin animasyonu ve eşleme, haritada bir konuma sabitlenmiş bir açılan pencere ile taşındı](./media/display-feature-information-android/android-popup.gif)

## <a name="next-steps"></a>Sonraki adımlar

Haritanızda daha fazla veri eklemek için:

> [!div class="nextstepaction"]
> [Eşleme olaylarına tepki verme](android-map-events.md)

> [!div class="nextstepaction"]
> [Veri kaynağı oluşturma](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Sembol katmanı ekleme](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Baloncuk katmanı ekleme](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Çizgi katmanı ekleme](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Çokgen katmanı ekleme](how-to-add-shapes-to-android-map.md)
