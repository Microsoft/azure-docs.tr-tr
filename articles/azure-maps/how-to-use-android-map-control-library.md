---
title: Android harita denetimi ile çalışmaya başlama | Microsoft Azure haritaları
description: Azure Haritalar Android SDK hakkında bilgi sahibi olun. Android Studio bir proje oluşturmayı, SDK 'yı yüklemeyi ve etkileşimli bir harita oluşturmayı öğrenin.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: a60ee8faf8d19afba59e46c52aaba9395c3a5292
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604456"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Azure haritalar 'ı kullanmaya başlama Android SDK

Azure Maps Android SDK, Android için bir vektör eşleme kitaplığıdır. Bu makale, Azure Maps Android SDK yükleme ve eşleme yükleme işlemlerinde size rehberlik eder.

## <a name="prerequisites"></a>Önkoşullar

[Hızlı başlangıç: Android uygulama belgesi oluşturma](quick-android-map.md) ' daki adımları tamamladığınızdan emin olun.

## <a name="localizing-the-map"></a>Haritayı yerelleştirme

Azure Haritalar Android SDK haritanın dilini ve bölgesel görünümünü ayarlamanın üç farklı yolunu sağlar. Aşağıdaki kod, dilin Fransızca ("fr-FR") ve bölgesel görünümün "Auto" olarak nasıl ayarlanacağını gösterir.

İlk seçenek, `AzureMaps` statik `setLanguage` ve `setView` yöntemleri genel olarak kullanarak dili geçirmektir ve bölgesel bilgileri sınıfa görüntüler. Bu işlem, uygulamanızda yüklü olan tüm Azure haritalar denetimlerinde varsayılan dili ve bölgesel görünümü ayarlar.

::: zone pivot="programming-language-java-android"

```java
static {
    //Alternatively use Azure Active Directory authenticate.
    AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");

    //Set your Azure Maps Key.
    //AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");   

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("Auto");
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
companion object {
    init {
        //Alternatively use Azure Active Directory authenticate.
        AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");

        //Set your Azure Maps Key.
        //AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");
    
        //Set the language to be used by Azure Maps.
        AzureMaps.setLanguage("fr-FR");
    
        //Set the regional view to be used by Azure Maps.
        AzureMaps.setView("Auto");
    }
}
```

::: zone-end

İkinci seçenek, dili geçirmektir ve harita denetim XML dosyasına bilgileri görüntüler.

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="Auto"
    />
```

Üçüncü seçenek, haritalar yöntemi kullanılarak eşlemenin dil ve bölgesel görünümünü programlı bir şekilde ayarlamanıza olanak sağlar `setStyle` . Bu işlem, eşlemenin dil ve bölgesel görünümünü değiştirmek için herhangi bir zamanda yapılabilir.

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    map.setStyle(
        language("fr-FR"),
        view("Auto")
    );
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl.onReady(OnReady { map: AzureMap ->
    map.setStyle(
        language("fr-FR"),
        view("Auto")
    )
})
```

::: zone-end

Dil "fr-FR" olarak ayarlanmış ve bölgesel görünüm "Auto" olarak ayarlanmış bir Azure Maps örneği aşağıda verilmiştir.

![Azure haritalar, etiketleri Fransızca olarak gösteren harita resmi](media/how-to-use-android-map-control-library/android-localization.png)

Desteklenen dillerin ve bölgesel görünümlerin tamamen listesi [burada](supported-languages.md)belgelenmiştir.

## <a name="navigating-the-map&quot;></a>Haritada gezinme

Haritanın Yakınlaştırılıp Yakınlaştırılıp döndürülmemiş, döndürülebileceği ve açık bir şekilde birçok farklı yolu vardır. Haritada gezinmek için kullanabileceğiniz farklı yollar aşağıda verilmiştir.

**Haritayı Yakınlaştır**

* Parmakların ölçeğini yakınlaştırmak veya yaymak için iki parmağınızla ve Pinç ile Haritayı dokunarak bir araya geçirin.
* Bir düzey yakınlaştırmak için haritaya çift dokunun.
* Haritayı bir düzey büyütmek için iki parmağınızla çift dokunun.
* İki kez dokunun; İkinci tap haritada parmak izi tutun ve yakınlaştırıp yakınlaştırmak için yukarı veya aşağı sürükleyin.

**Haritayı kaydır**

* Haritayı dokunarak istediğiniz yönde sürükleyin.

**Haritayı döndürme**

* İki parmağınızla haritaya dokunun ve döndürün.

**Haritayı sıklık**

* İki parmağınızla Haritayı dokunarak yukarı veya aşağı sürükleyin.

## <a name=&quot;azure-government-cloud-support&quot;></a>Azure Kamu bulut desteği

Azure Haritalar Android SDK Azure Kamu bulutunu destekler. Azure Maps Android SDK aynı Maven deposundan erişilir. Azure haritalar platformunun Azure Kamu bulut sürümüne bağlanmak için aşağıdaki görevlerin yapılması gerekir.

Azure haritalar kimlik doğrulama ayrıntılarının belirtildiği aynı yerde, eşlemenin Azure haritalar kamu bulutu etki alanını kullanmasını söylemek için aşağıdaki kod satırını ekleyin.

::: zone pivot=&quot;programming-language-java-android&quot;

```java
AzureMaps.setDomain(&quot;atlas.azure.us");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
AzureMaps.setDomain("atlas.azure.us")
```

::: zone-end

Harita ve hizmetlerin kimliğini doğrularken Azure 'un kimlik doğrulama ayrıntılarını Azure Kamu bulut platformunda kullandığınızdan emin olun.

## <a name="next-steps"></a>Sonraki adımlar

Haritada kaplama verileri eklemeyi öğrenin:

> [!div class="nextstepaction"]
> [Azure haritalar 'da kimlik doğrulamasını yönetme](how-to-manage-authentication.md)

> [!div class="nextstepaction"]
> [Android haritalar 'da harita stillerini değiştirme](set-android-map-styles.md)

> [!div class="nextstepaction"]
> [Sembol katmanı ekleme](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Çizgi katmanı ekleme](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Çokgen katmanı ekleme](how-to-add-shapes-to-android-map.md)
