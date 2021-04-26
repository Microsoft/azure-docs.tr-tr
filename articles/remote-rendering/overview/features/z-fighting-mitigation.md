---
title: Z dikişini azaltma
description: Z mücadele yapıtları azaltma tekniklerini açıklar
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: a399565d62b20f62b72257bcb9f3beb2c910ac98
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594070"
---
# <a name="z-fighting-mitigation"></a>Z dikişini azaltma

İki yüzey çakıştığında, bunlardan birinin üzerine oluşturulması gereken açık olmaz. Sonuç, piksel başına farklılık gösterir ve kamera görünümüne bağımlı yapıtlar ile sonuçlanır. Sonuç olarak, kamera veya ağ taşırken, bu desenler önemli ölçüde titreşdiğinde. Bu yapıt *z-Fighting* olarak adlandırılır. AR ve VR uygulamalarında, baş bağlı cihazlar doğal olarak her zaman hareket ettiğinden sorun belirlenir. Görüntüleyiciye engel olmak için, Azure uzaktan Işlemede risk azaltma işlevselliği kullanılabilir.

## <a name="z-fighting-mitigation-modes"></a>Z-mücadele azaltma modları

|Olanını                        | Sonuç                               |
|---------------------------------|:-------------------------------------|
|Normal z-Fighting               |![Kırmızı ve yeşil Quads arasında belirleyici öncelik yok](./media/zfighting-0.png)|
|Z-mücadele azaltma etkin    |![Kırmızı dörtlü öncelik vardır](./media/zfighting-1.png)|
|Dama tahtası vurgusu etkin|![Dama tahtası düzeninde kırmızı ve yeşil dört durumlu tercih](./media/zfighting-2.png)|

Aşağıdaki kod, z-Fighting hafifletme imkanı sunar:

```cs
void EnableZFightingMitigation(RenderingSession session, bool highlight)
{
    ZFightingMitigationSettings settings = session.Connection.ZFightingMitigationSettings;

    // enabling z-fighting mitigation
    settings.Enabled = true;

    // enabling checkerboard highlighting of z-fighting potential
    settings.Highlighting = highlight;
}
```

```cpp
void EnableZFightingMitigation(ApiHandle<RenderingSession> session, bool highlight)
{
    ApiHandle<ZFightingMitigationSettings> settings = session->Connection()->GetZFightingMitigationSettings();

    // enabling z-fighting mitigation
    settings->SetEnabled(true);

    // enabling checkerboard highlighting of z-fighting potential
    settings->SetHighlighting(highlight);
}
```

> [!NOTE]
> Z-Fighting, tüm işlenmiş kafesleri etkileyen genel bir ayardır.

## <a name="reasons-for-z-fighting"></a>Z-Fighting nedenleri

Z-Fighting genellikle iki nedenden dolayı gerçekleşir:

1. yüzeyler kameradan çok uzakta olduğunda, derinlik değerlerinin duyarlığı düşer ve değerler ayırt edilemez hale gelir
1. bir kafesteki yüzeyler fiziksel olarak çakıştığında

İlk sorun her zaman meydana gelebilir ve ortadan kaldırmak zordur. Uygulamanızda bu durum oluşursa, en *yakın düzlem* uzaklığın, en düşük *düzey uzaklığı oranının* pratik olduğundan emin olun. Örneğin, 0,01 uzaklığına yakın bir düzme ve 1000 uzaklığın en solundaki düzlem, 0,1 ve en fazla 20 uzaklığın üzerinde neredeyse düzleden daha önce bu sorunu oluşturacaktır.

İkinci sorun hatalı yazılmış içerik için bir göstergedir. Gerçek dünyada, iki nesne aynı anda aynı yerde olamaz. Uygulamaya bağlı olarak, kullanıcılar çakışan yüzeylerin mevcut olup olmadığını ve nerede olduğunu bilmesini isteyebilir. Örneğin, gerçek bir dünya yapımı için temel olan bir binanın CAD sahneyi, fiziksel olarak imkansız bir yüzey kesişimleri içermemelidir. Görsel denetimine izin vermek için, vurgulama modu kullanılabilir ve bu da animasyonlu dama tahtası deseninin olası z fighini gösterir.

## <a name="limitations"></a>Sınırlamalar

Sunulan z mücadele azaltma, en iyi çabadır. Tüm z-mücadele ' i kaldıran garanti yoktur. Ayrıca, başka bir yüzeyi de otomatik olarak tercih eder. Bu nedenle, birbirine çok yakın yüzeyler varsa, "yanlış" yüzeyi en üstte sona erecek olabilir. Yaygın bir sorun durumu, metin ve diğer Decal 'lerin bir yüzeye uygulandığı durumdur. Z özellikli hafifletme özelliği etkinken bu ayrıntılar kolayca biraz kolay olabilir.

## <a name="performance-considerations"></a>Performansla ilgili önemli noktalar

* Z-Fighting azaltma özelliğinin etkinleştirilmesi, performans yükünü ortadan kaldırır.
* Ayrıca, z-Fighting kaplamasını etkinleştirmek, sahneye bağlı olarak değişebileceğinden, önemsiz olmayan bir performans yükü doğurur.

## <a name="api-documentation"></a>API belgeleri

* [C# RenderingConnection. ZFightingMitigationSettings özelliği](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.zfightingmitigationsettings)
* [C++ RenderingConnection:: ZFightingMitigationSettings ()](/cpp/api/remote-rendering/renderingconnection#zfightingmitigationsettings)

## <a name="next-steps"></a>Sonraki adımlar

* [İşleme modları](../../concepts/rendering-modes.md)
* [Geç aşama yeniden projeksiyonu](late-stage-reprojection.md)