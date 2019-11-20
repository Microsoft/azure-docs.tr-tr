---
title: Azure haritalar 'da Android Maps 'a bir sembol katmanı ekleme | Microsoft Docs
description: Azure haritalar kullanarak bir haritaya semboller ekleme Android SDK
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 0292c8a441589a01241fbef6923246b4bcafb5c8
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976254"
---
# <a name="add-a-symbol-layer-to-a-map-using-azure-maps-android-sdk"></a>Azure haritalar 'ı kullanarak haritaya bir sembol katmanı ekleme Android SDK

Bu makalede, Azure Maps Android SDK kullanarak bir harita üzerinde sembol katmanı olarak bir veri kaynağındaki nokta verilerinin nasıl işleneceğini gösterir.

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki adımları tamamen izlemek için, bir harita yüklemek üzere [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) yüklemeniz gerekir.

## <a name="add-a-symbol-layer"></a>Sembol katmanı ekleme

Sembol katmanını kullanarak haritada bir işaretleyici eklemek için aşağıdaki adımları izleyin:

1. Şu XML gibi görünmesi için **res** > **düzeni** > **activity_main. xml** ' i düzenleyin:
    
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
            app:mapcontrol_centerLat="47.64"
            app:mapcontrol_centerLng="-122.33"
            app:mapcontrol_zoom="12"
            />

    </FrameLayout>
    ```

2. Aşağıdaki kod parçacığını `MainActivity.java` sınıfınızın **OnCreate ()** yöntemine kopyalayın.

    ```Java
    mapControl.onReady(map -> {
    
        //Create a data source and add it to the map.
        DataSource dataSource = new DataSource();
        map.sources.add(dataSource);
    
        //Create a point feature and add it to the data source.
        dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
    
        //Add a custom image icon to the map resources.
        map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
    
        //Create a symbol layer and add it to the map.
        map.layers.add(new SymbolLayer(dataSource,
            iconImage("my-icon")));
        });
    
    ```
    
    Yukarıdaki kod parçacığı ilk olarak, **Onready ()** geri çağırma yöntemini kullanarak bir Azure haritalar harita denetim örneği edinir. Daha sonra **DataSource** sınıfını kullanarak bir veri kaynağı nesnesi oluşturur ve bunu haritaya ekler. Ardından bir nokta geometrisi içeren bir **özellik** ekler. Kırmızı bir işaret resmi, ardından sembol için simge olarak ayarlanır. Bir **sembol katmanı** , veri kaynağında kaydırılan nokta tabanlı verileri haritada sembol olarak işlemek için metin veya simgeleri kullanır. Daha sonra bir sembol katmanı oluşturulur ve veri kaynağı işlemek için iletilir ve sonra haritanın katmanlarına eklenir.
    
    Yukarıdaki `MainActivity.java` kod parçacığını ekledikten sonra aşağıdaki gibi görünmelidir:
    
    ```Java
    package com.example.myapplication;
    
    import android.app.Activity;
    import android.os.Bundle;
    import com.mapbox.geojson.Feature;
    import com.mapbox.geojson.Point;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;
    import static com.microsoft.azure.maps.mapcontrol.options.SymbolLayerOptions.iconImage;
    public class MainActivity extends AppCompatActivity {
        
        static{
                AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
            }
    
        MapControl mapControl;
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapControl = findViewById(R.id.mapcontrol);
    
            mapControl.onCreate(savedInstanceState);
    
            mapControl.onReady(map -> {
    
                //Create a data source and add it to the map.
                DataSource dataSource = new DataSource();
                map.sources.add(dataSource);
            
                //Create a point feature and add it to the data source.
                dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
            
                //Add a custom image icon to the map resources.
                map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
            
                //Create a symbol layer and add it to the map.
                map.layers.add(new SymbolLayer(dataSource,
                    iconImage("my-icon")));
            });
        }
    
        @Override
        public void onStart() {
            super.onStart();
            mapControl.onStart();
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapControl.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapControl.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapControl.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapControl.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapControl.onSaveInstanceState(outState);
        }
    }
    ```
    
Bu noktada, uygulamanızı çalıştırırsanız burada gösterildiği gibi haritada bir işaret görmeniz gerekir:

<center>

![Android eşleme PIN 'i](./media/how-to-add-symbol-to-android-map/android-map-pin.png)</center>


## <a name="next-steps"></a>Sonraki adımlar

Haritanızda daha fazla bilgi eklemek için bkz.:

> [!div class="nextstepaction"]
> [Android haritasına şekil ekleme](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Özellik bilgilerini görüntüle](display-feature-information-android.md)