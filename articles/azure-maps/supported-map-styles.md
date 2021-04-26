---
title: Desteklenen yerleşik Azure haritaları eşleme stilleri
description: Azure haritalar 'ın yol, blank_accessible, uydu, satellite_road_labels, road_shaded_relief ve gece gibi desteklediği yerleşik harita stilleri hakkında bilgi edinin.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: b3404c02dc159309ed1e9fcd6f9f6bb593fc7552
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92896948"
---
# <a name="azure-maps-supported-built-in-map-styles"></a>Azure haritalar 'da desteklenen yerleşik harita stilleri

Azure Maps, aşağıda açıklandığı gibi birkaç farklı yerleşik harita stilini destekler.

## <a name="road"></a>Road

**Yol** Haritası, yollar görüntüleyen standart bir eşlemedir. Ayrıca doğal ve yapay özellikleri ve bu özellikler için etiketleri görüntüler.

![yol haritası stili](./media/supported-map-styles/road.png)

**Geçerli API 'Ler:**

* [Harita resmi](/rest/api/maps/render/getmapimage)
* [Harita kutucuğu](/rest/api/maps/render/getmaptile)
* Web SDK eşlemesi denetimi
* Android harita denetimi
* Power BI görseli

## <a name="blank-and-blank_accessible"></a>boş ve blank_accessible

**Boş** ve **blank_accessible** eşleme stilleri verileri görselleştirmeye yönelik boş bir tuval sağlar. **Blank_accessible** stili, temel harita görüntülenmese de haritanın konum ayrıntıları ile ekran okuyucusu güncelleştirmeleri sağlamaya devam edecektir.

> [!Note]
> Web SDK 'sında harita DIV öğesinin CSS stilini ayarlayarak haritanın arka plan rengini değiştirebilirsiniz `background-color` .

**Geçerli API 'Ler:**

* Web SDK eşlemesi denetimi

## <a name="satellite"></a>satellite

**Uydu** stili, uydu ve hava imasıy 'nin bir birleşimidir.

![uydu kutucuk harita stili](./media/supported-map-styles/satellite.png)

**Geçerli API 'Ler:**

* [Uydu kutucuğu](/rest/api/maps/render/getmapimagerytilepreview)
* Web SDK eşlemesi denetimi
* Android harita denetimi
* Power BI görseli

## <a name="satellite_road_labels"></a>satellite_road_labels

Bu harita stili, uydu ve hava imakına göre popüler yollar ve Etiketler için bir karma değer.

![satellite_road_labels harita stili](./media/supported-map-styles/satellite-road-labels.png)

**Geçerli API 'Ler:**

* Web SDK eşlemesi denetimi
* Android harita denetimi
* Power BI görseli

## <a name="grayscale_dark"></a>grayscale_dark

**gri tonlamalı koyu** , yol haritası stilinin karanlık bir sürümüdür.

![gray_scale harita stili](./media/supported-map-styles/grayscale-dark.png)

**Geçerli API 'Ler:**

* [Harita resmi](/rest/api/maps/render/getmapimage)
* [Harita kutucuğu](/rest/api/maps/render/getmaptile)
* Web SDK eşlemesi denetimi
* Android harita denetimi
* Power BI görseli

## <a name="grayscale_light"></a>grayscale_light

**gri tonlama ışığı** , yol haritası stilinin hafif bir sürümüdür.

![gri tonlamalı hafif harita stili](./media/supported-map-styles/grayscale-light.png)

**Geçerli API 'Ler:**
* Web SDK eşlemesi denetimi
* Android harita denetimi
* Power BI görseli

## <a name="night"></a>Gece

**gece** , yol haritası stilinin renkli yollar ve semboller ile karanlık bir sürümüdür.

![gece harita stili](./media/supported-map-styles/night.png)

**Geçerli API 'Ler:**

* Web SDK eşlemesi denetimi
* Android harita denetimi
* Power BI görseli

## <a name="road_shaded_relief"></a>road_shaded_relief

**trafik gölgeli tahliye** , dünya dağılımlarıyla birlikte tamamlanan bir Azure Maps ana stilidir.

![gölgeli emniyet Haritası stili](./media/supported-map-styles/shaded-relief.png)

**Geçerli API 'Ler:**

* [Harita kutucuğu](/rest/api/maps/render/getmaptile)
* Web SDK eşlemesi denetimi
* Android harita denetimi
* Power BI görseli

## <a name="high_contrast_dark"></a>high_contrast_dark

**high_contrast_dark** , diğer stillerden daha yüksek karşıtlığı olan koyu bir harita stilidir.

![yüksek karşıtlık koyu harita stili](./media/supported-map-styles/high-contrast-dark.png)

**Geçerli API 'Ler:**

* Web SDK eşlemesi denetimi
* Power BI görseli

## <a name="next-steps"></a>Sonraki adımlar

Azure haritalar 'da harita stili ayarlama hakkında bilgi edinin:

[Harita stili seçme](./choose-map-style.md)