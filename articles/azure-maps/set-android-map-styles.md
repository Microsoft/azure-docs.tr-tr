---
title: Azure haritalar 'da harita stili işlevleri | Microsoft Docs
description: Android SDK için Azure Maps stilinde ilgili işlevler hakkında bilgi edinin.
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 5bcc73c63e7c9d804d01df98551aa51b81d98d07
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844856"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Azure haritalar 'ı kullanarak harita stili ayarlama Android SDK

Bu makalede, Azure Haritalar Android SDK kullanarak harita stillerini ayarlamak için kullanabileceğiniz iki yol gösterilmektedir. Azure haritalar 'ın aralarından seçim yapabileceğiniz altı farklı harita stili vardır. Desteklenen harita stilleri hakkında daha fazla bilgi için bkz. [Azure Maps 'de desteklenen harita stilleri](./supported-map-styles.md).


## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki işlemi gerçekleştirmek için, bir harita yüklemek üzere [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) yüklemeniz gerekir.


## <a name="set-map-style-in-the-layout"></a>Düzende harita stili ayarla

Etkinlik sınıfınızın düzen dosyasında bir harita stili ayarlayabilirsiniz. **> >. Xml**' i düzenleyin, bu nedenle aşağıdaki gibi görünür:

```XML
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    >

    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/mapcontrol"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:mapcontrol_centerLat="47.602806"
        app:mapcontrol_centerLng="-122.329330"
        app:mapcontrol_zoom="12"
        app:mapcontrol_style="grayscale_dark"
        />

</FrameLayout>
```

Yukarıdaki öznitelik, Map stilini grayscale_dark olarak ayarlar. `mapcontrol_style` 

<center>

![Style-grayscale_dark](./media/set-android-map-styles/grayscale-dark.png)</center>

## <a name="set-map-style-in-the-activity-class"></a>Etkinlik sınıfında harita stilini ayarla

Eşleme stili etkinlik sınıfında ayarlanabilir. Aşağıdaki kod parçacığını `MainActivity.java` sınıfınızın **OnCreate ()** yöntemine kopyalayın. Bu, harita stilini **satellite_road_labels**olarak ayarlar.

```Java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![Stil-uydu-yol-Etiketler](./media/set-android-map-styles/satellite-road-labels.png)</center>