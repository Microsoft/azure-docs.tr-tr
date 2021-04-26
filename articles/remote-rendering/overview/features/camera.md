---
title: Kamera
description: Kamera ayarlarını ve kullanım örneklerini açıklar
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.openlocfilehash: dbe86313054706af974ccb324a39e942e9b5ca44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594138"
---
# <a name="camera"></a>Kamera

Yerel olarak ve uzaktan işlenmiş içeriklerin sorunsuz bir şekilde oluşturulabilir olduğundan emin olmak için, çeşitli kamera özelliklerinin sunucu ve istemci arasında eşitlenmiş durumda kalması gerekir. Çoğu özellikle kamera dönüştürme ve projeksiyon. Örneğin, yerel olarak işlenmiş içerik, en son uzak karenin birlikte işlendiği aynı kamera dönüşümünü ve projeksiyonu kullanmalıdır.

![Yerel ve uzak kamera](./media/camera.png)

Yukarıdaki görüntüde, yerel kamera sunucu tarafından gönderilen uzak çerçeveye göre taşınmıştır. Sonuç olarak, yerel içeriğin uzak kareyle aynı perspektiften oluşturulması gerekir ve son olarak, sonuçta elde edilen görüntü, [geç aşama yeniden projeksiyonundaki](late-stage-reprojection.md)ayrıntılı olarak açıklanan yerel kamera alanına yeniden yansıtılmıştır.

Uzak ve yerel işleme arasındaki gecikme, bu ayarların herhangi bir değişikliğinin gecikmeyle uygulandığı anlamına gelir. Bu nedenle, her yeni değer hemen aynı çerçeve üzerinde kullanılamaz.

Yakın ve en düzlem uzaklıkları Kamera ayarlarında ayarlanabilir. HoloLens 2 ' de, kalan dönüştürme ve projeksiyon verileri donanım tarafından tanımlanır. [Masaüstü benzetimi](../../concepts/graphics-bindings.md)kullanılırken, kendi işlevinin girişi aracılığıyla ayarlanır `Update` .

Değiştirilen değerler, ile işlenen ilk uzak çerçeve geldiğinde yerel olarak yerel olarak kullanılabilir. HoloLens 2 ' de, işleme için kullanılacak veriler başka bir holographic uygulamasında olduğu gibi *Holographicframe* tarafından sağlanır. [Masaüstü simülasyonu](../../concepts/graphics-bindings.md)üzerinde, işlevin çıktısı aracılığıyla alınır `Update` .

## <a name="camera-settings"></a>Kamera ayarları

Kamera ayarlarında aşağıdaki özellikler değiştirilebilir:

**Yakın ve uzak düzlem:**

Geçersiz Aralık ayarlanilemediğinizden emin olmak **için, yeniden, yeniden,** en fazla bir işlev salt **okunurdur ve aralığı** değiştirmek için ayrı bir **SetNearAndFarPlane** vardır. Bu veriler, çerçevenin sonundaki sunucuya gönderilir. Bu değerler ayarlanırken, en az bir **düzlem** **fardüzlemden** küçük olmalıdır. Aksi takdirde bir hata oluşur.

> [!IMPORTANT]
> Unity 'de, ana kameranın yakınında ve en dışı düzlemleri değiştirirken bu otomatik olarak işlenir.

**Enabledepth**:

Bazen hata ayıklama amacıyla uzak görüntünün derinlik arabelleği yazma işlemini devre dışı bırakmak yararlıdır. Derinliğin devre dışı bırakılması, sunucunun derinlik verileri göndermesini ve bu sayede bant genişliğini azaltmanızı da durdurur.

> [!TIP]
> Unity 'de, bu özelliği düzenleyici Kullanıcı arabiriminde değiştirmek için kullanılabilecek **Enabledepthcomponent** adlı bir hata ayıklama bileşeni sağlanır.

**Ters çevir**:

> [!NOTE]
> Bu ayar yalnızca `EnableDepth` , olarak ayarlanmışsa önemlidir `true` . Aksi takdirde bu ayarın etkisi yoktur.

Derinlik arabellekleri normalde, bir [0; 1] kayan nokta aralığında z değerlerini kaydeder; neredeyse düzlem derinliğini ve en yüksek düzlem derinliğini belirten 1. Bu aralığı ve [1; 0] aralığında kayıt derinlik değerlerini ters çevirmek de mümkündür; diğer bir deyişle, neredeyse düzlem derinliği 1 olur ve en yüksek düzlem derinliği 0 olur. Genellikle, ikinci olarak, kayan nokta duyarlığını doğrusal olmayan z aralığı boyunca dağılımı geliştirir.

> [!WARNING]
> Yaygın bir yaklaşım, kamera nesnelerinde neredeyse düzlem ve en yüksek değerleri tersine dönüştürmekte. Bu, üzerinde çalışırken bir hata ile Azure uzaktan Işleme için başarısız olur `CameraSettings` .

Azure uzaktan Işleme API 'sinin, uzaktan derinliği yerel derinlik arabelleğine doğru şekilde oluşturmak için yerel işleyicinizdeki derinlik arabelleği kuralını bilmesi gerekir. Derinlik arabellek aralığınızı [0; 1] ise bu bayrağı olarak bırakın `false` . [1; 0] aralığıyla ters çevrilmiş bir derinlik arabelleği kullanırsanız, `InverseDepth` bayrağını olarak ayarlayın `true` .

> [!NOTE]
> Unity için, doğru ayar tarafından zaten uygulanmış olduğundan `RenderingConnection` el ile müdahale gerekmez.

Kamera ayarlarının değiştirilmesi şu şekilde yapılabilir:

```cs
void ChangeCameraSetting(RenderingSession session)
{
    CameraSettings settings = session.Connection.CameraSettings;

    settings.SetNearAndFarPlane(0.1f, 20.0f);
    settings.EnableDepth = false;
    settings.InverseDepth = false;
}
```

```cpp
void ChangeCameraSetting(ApiHandle<RenderingSession> session)
{
    ApiHandle<CameraSettings> settings = session->Connection()->GetCameraSettings();

    settings->SetNearAndFarPlane(0.1f, 20.0f);
    settings->SetEnableDepth(false);
    settings->SetInverseDepth(false);
}
```

## <a name="api-documentation"></a>API belgeleri

* [C# CameraSettings](/dotnet/api/microsoft.azure.remoterendering.camerasettings)
* [C++ CameraSettings](/cpp/api/remote-rendering/camerasettings)
* [C# GraphicsBindingSimD3d11. Update işlevi](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11.update)
* [C++ GraphicsBindingSimD3d11:: Update işlevi](/cpp/api/remote-rendering/graphicsbindingsimd3d11#update)

## <a name="next-steps"></a>Sonraki adımlar

* [Grafik bağlama](../../concepts/graphics-bindings.md)
* [Geç aşama yeniden projeksiyonu](late-stage-reprojection.md)