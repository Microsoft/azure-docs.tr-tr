---
title: Grafik bağlama
description: Grafik bağlamaları ve kullanım durumlarının kurulumu
author: florianborn71
manager: jlyons
services: azure-remote-rendering
titleSuffix: Azure Remote Rendering
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.service: azure-remote-rendering
ms.custom: devx-track-csharp
ms.openlocfilehash: 69bcc521b4cd00320a5fbecc5244e913ac16c68b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99593917"
---
# <a name="graphics-binding"></a>Grafik bağlama

Azure uzaktan oluşturma 'yı özel bir uygulamada kullanabilmeniz için uygulamanın işleme ardışık düzenine entegre olması gerekir. Bu tümleştirme, grafik bağlamanın sorumluluğundadır.

Ayarladıktan sonra grafik bağlama, işlenen görüntüyü etkileyen çeşitli işlevlere erişim sağlar. Bu işlevler iki kategoriye ayrılabilir: her zaman kullanılabilir olan genel işlevler ve yalnızca seçili olan belirli işlevler `Microsoft.Azure.RemoteRendering.GraphicsApiType` .

## <a name="graphics-binding-in-unity"></a>Unity 'de grafik bağlama

Unity 'de, tüm bağlama `RemoteUnityClientInit` geçirilen yapı tarafından işlenir `RemoteManagerUnity.InitializeManager` . Grafik modunu ayarlamak için `GraphicsApiType` alanın seçilen bağlamaya ayarlanması gerekir. Alan, bir XRDevice var olup olmadığına bağlı olarak otomatik olarak doldurulur. Davranışın aşağıdaki davranışlar ile el ile geçersiz kılınabilmesi:

* **HoloLens 2**: [Windows Mixed Reality](#windows-mixed-reality) grafik bağlaması her zaman kullanılır.
* **Düz UWP masaüstü uygulaması**: [Benzetim](#simulation) her zaman kullanılır.
* **Unity Düzenleyicisi**: BIR WMR VR kulaklık bağlantısı yoksa [simülasyon](#simulation) her zaman kullanılır ve bu durumda, uygulamanın ARR ile ilgili olmayan bölümlerinin hata ayıklamasına izin vermek için ARR 'nin devre dışı bırakılması gerekir. Ayrıca bkz. [holographic Remoting](../how-tos/unity/holographic-remoting.md).

Unity 'nin yalnızca diğer ilgili bölümü [temel bağlamaya](#access)erişiyor, aşağıdaki diğer tüm bölümler atlanabilir.

## <a name="graphics-binding-setup-in-custom-applications"></a>Özel uygulamalarda grafik bağlama kurulumu

Grafik bağlamayı seçmek için aşağıdaki iki adımı uygulayın: Ilk olarak, program başlatıldığında grafik bağlamasının statik olarak başlatılması gerekir:

```cs
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization();
managerInit.GraphicsApi = GraphicsApiType.WmrD3D11;
managerInit.ConnectionType = ConnectionType.General;
managerInit.Right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

```cpp
RemoteRenderingInitialization managerInit;
managerInit.GraphicsApi = GraphicsApiType::WmrD3D11;
managerInit.ConnectionType = ConnectionType::General;
managerInit.Right = ///...
StartupRemoteRendering(managerInit); // static function in namespace Microsoft::Azure::RemoteRendering

```

Yukarıdaki çağrı, Holographic API 'Lerinde Azure uzaktan Işlemeyi başlatmak için gereklidir. Bu işlev, herhangi bir holographic API çağrılmadan önce ve diğer uzaktan Işleme API 'Lerine erişilmadan önce çağrılmalıdır. Benzer şekilde, `RemoteManagerStatic.ShutdownRemoteRendering();` artık holographic API 'lerini çağırıldıktan sonra karşılık gelen de init işlevi çağrılmalıdır.

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">Grafik bağlamaya erişme

İstemci kurulduktan sonra, alıcı ile temel grafik bağlamaya erişilebilir `RenderingSession.GraphicsBinding` . Örnek olarak, son çerçeve istatistikleri şu şekilde alınabilir:

```cs
RenderingSession currentSession = ...;
if (currentSession.GraphicsBinding != null)
{
    FrameStatistics frameStatistics;
    if (currentSession.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        ...
    }
}
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
if (ApiHandle<GraphicsBinding> binding = currentSession->GetGraphicsBinding())
{
    FrameStatistics frameStatistics;
    if (binding->GetLastFrameStatistics(&frameStatistics) == Result::Success)
    {
        ...
    }
}
```

## <a name="graphic-apis"></a>Grafik API 'Leri

Şu anda seçilebilir iki grafik API 'si vardır `WmrD3D11` `SimD3D11` . Üçüncü bir tane `Headless` var ancak istemci tarafında henüz desteklenmiyor.

### <a name="windows-mixed-reality"></a>Windows Mixed Reality

`GraphicsApiType.WmrD3D11` , HoloLens 2 üzerinde çalıştırılacak varsayılan bağlamadır. `GraphicsBindingWmrD3d11`Bağlama oluşturacaktır. Bu modda Azure uzaktan Işleme kancaları doğrudan holographic API 'Lerine takılır.

Türetilmiş grafik bağlamalarına erişmek için, taban, `GraphicsBinding` atama olmalıdır.
WMR bağlamasını kullanmak için yapılması gereken iki şey vardır:

#### <a name="inform-remote-rendering-of-the-used-coordinate-system"></a>Kullanılan koordinat sisteminin uzaktan Işlenmesini bilgilendirme

```cs
RenderingSession currentSession = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (wmrBinding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
void* ptr = ...; // native pointer to ISpatialCoordinateSystem
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
if (wmrBinding->UpdateUserCoordinateSystem(ptr) == Result::Success)
{
    //...
}
```

Yukarıdaki, `ptr` `ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem` API 'deki koordinatların ifade edildiği dünya alanı koordinat sistemini tanımlayan yerel bir nesne işaretçisi olmalıdır.

#### <a name="render-remote-image"></a>Uzak görüntüyü işle

Her çerçevenin başlangıcında, uzak çerçevenin geri arabelleğin oluşturulması gerekir. Bu `BlitRemoteFrame` , her iki gözde hem renk hem de derinlik bilgilerini o anda bağlı işleme hedefine dolduracak şekilde çağırarak yapılır. Bu nedenle, tam arabelleği bir işleme hedefi olarak bağladıktan sonra bunu yapmak önemlidir.

> [!WARNING]
> Uzak görüntü geri arabelleğe alındıktan sonra, yerel içeriğin tek taramalı bir stereo işleme tekniği kullanılarak işlenmesi gerekir, örn. **SV_RenderTargetArrayIndex** kullanılıyor. Her bir gözü ayrı bir geçişte işleme gibi diğer stereo işleme tekniklerini kullanmak, önemli performans düşüşüne veya grafik yapılarına neden olabilir ve kaçınılması gerekir.

```cs
RenderingSession currentSession = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
wmrBinding.BlitRemoteFrame();
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
wmrBinding->BlitRemoteFrame();
```

### <a name="simulation"></a>Simülasyon

`GraphicsApiType.SimD3D11` simülasyon bağlamadır ve seçili ise `GraphicsBindingSimD3d11` grafik bağlamayı oluşturur. Bu arabirim, örneğin bir masaüstü uygulamasındaki baş taşımanın benzetimini yapmak için kullanılır ve tek bir scopc görüntüsü oluşturur.

Simülasyon bağlamasını uygulamak için, [Kamera](../overview/features/camera.md) sayfasında açıklanan şekilde, yerel kamera ile uzak çerçeve arasındaki farkı anlamak önemlidir.

İki kamera gereklidir:

* **Yerel kamera**: Bu kamera, uygulama mantığı tarafından yönetilen geçerli kamera konumunu temsil eder.
* **Proxy kamera**: Bu kamera, sunucu tarafından gönderilen geçerli *uzak kareyle* eşleşir. Bir çerçeve isteyen istemci arasında bir zaman gecikmesi olduğu için, *uzak çerçeve* her zaman yerel kameranın hareketinin arkasında bir bit olur.

Burada temel yaklaşım, hem uzak görüntünün hem de yerel içeriğin, proxy Kamerası kullanılarak bir ekran hedefine işlenmelerdir. Daha sonra, proxy görüntüsü daha sonra, [geç aşama yeniden projeksiyonda](../overview/features/late-stage-reprojection.md)açıklanan yerel kamera alanına yeniden yansıtılmıştı.

`GraphicsApiType.SimD3D11` Ayrıca, aşağıdaki kurulum çağrısı sırasında etkinleştirilmesi gereken Stereoscopic işlemesini destekler `InitSimulation` . Kurulum biraz daha karmaşıktır ve aşağıdaki gibi çalışmaktadır:

#### <a name="create-proxy-render-target"></a>Proxy oluşturma hedefi oluştur

Uzak ve yerel içeriğin, işlev tarafından verilen proxy kamera verilerini kullanarak ' Proxy ' adlı bir ekran genişliği renk/derinlik işleme hedefine işlenmesi gerekir `GraphicsBindingSimD3d11.Update` .

Proxy, arka arabelleğin çözümlenmesinden eşleşmelidir ve *DXGI_FORMAT_R8G8B8A8_UNORM* veya *DXGI_FORMAT_B8G8R8A8_UNORM* biçimindeki tamsayı olmalıdır. Stereoscopic işleme durumunda hem Color proxy dokusu hem de derinlik kullanılırsa, derinlik ara sunucu dokusunun yerine iki dizi katmanı olması gerekir. Bir oturum başlamaya başladıktan sonra, `GraphicsBindingSimD3d11.InitSimulation` kendisine bağlanmadan önce çağrılması gerekir:

```cs
RenderingSession currentSession = ...;
IntPtr d3dDevice = ...; // native pointer to ID3D11Device
IntPtr color = ...; // native pointer to ID3D11Texture2D
IntPtr depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
bool stereoscopicRendering = false;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
simBinding.InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically, stereoscopicRendering);
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
void* d3dDevice = ...; // native pointer to ID3D11Device
void* color = ...; // native pointer to ID3D11Texture2D
void* depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
bool stereoscopicRendering = false;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();
simBinding->InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically, stereoscopicRendering);
```

İnit işlevinin, yerel D3D-cihazının işaretçileriyle birlikte, proxy oluşturma hedefinin renk ve derinlik dokusunu sağlaması gerekir. Başlatıldıktan `RenderingSession.ConnectAsync` ve `Disconnect` birden çok kez çağrılabilir, ancak farklı bir oturuma geçiş yapıldığında, `GraphicsBindingSimD3d11.DeinitSimulation` `GraphicsBindingSimD3d11.InitSimulation` başka bir oturumda çağrılabilmesi için önce eski oturumda çağrılmalıdır.

#### <a name="render-loop-update"></a>Döngü güncelleştirmesini işle

İşleme döngüsü güncelleştirmesi birden çok adımdan oluşur:

1. Her bir çerçeve, herhangi bir işleme gerçekleşmeden önce, `GraphicsBindingSimD3d11.Update` işlenecek sunucuya gönderilen geçerli kamera dönüşümüyle çağrılır. Aynı zamanda, proxy oluşturma hedefine işlemek için, döndürülen proxy dönüştürmesinin proxy kameraya uygulanması gerekir.
Döndürülen proxy güncelleştirmesi `SimulationUpdate.frameId` null ise, henüz uzak veri yok. Bu durumda, proxy oluşturma hedefine işlemek yerine, tüm yerel içerik, geçerli kamera verileri kullanılarak doğrudan arabellekte işlenmelidir ve sonraki iki adım atlanır.
1. Uygulama artık proxy oluşturma hedefini ve çağrısını bağlamalıdır `GraphicsBindingSimD3d11.BlitRemoteFrameToProxy` . Bu, uzak renk ve derinlik bilgilerini ara sunucu işleme hedefine doldurur. Tüm yerel içerikler artık proxy kamera dönüşümü kullanılarak proxy üzerinde oluşturulabilir.
1. Ardından, arka arabelleğin bir işleme hedefi olarak bağlanması ve `GraphicsBindingSimD3d11.ReprojectProxy` arka arabelleğin sunulabileceği noktada çağrılması gerekir.

```cs
RenderingSession currentSession = ...;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
SimulationUpdateParameters updateParameters = new SimulationUpdateParameters();
// Fill out camera data with current camera data
// (see "Simulation Update structures" section below)
...
SimulationUpdateResult updateResult = new SimulationUpdateResult();
simBinding.Update(updateParameters, out updateResult);
// Is the frame data valid?
if (updateResult.FrameId != 0)
{
    // Bind proxy render target
    simBinding.BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding.ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

```cpp
ApiHandle<RenderingSession> currentSession;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();

SimulationUpdateParameters updateParameters;
// Fill out camera data with current camera data
// (see "Simulation Update structures" section below)
...
SimulationUpdateResult updateResult;
simBinding->Update(updateParameters, &updateResult);
// Is the frame data valid?
if (updateResult.FrameId != 0)
{
    // Bind proxy render target
    simBinding->BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding->ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

#### <a name="simulation-update-structures"></a>Simülasyon güncelleştirme yapıları

Önceki bölümde bulunan **işleme döngüsü güncelleştirmesi** , her çerçeve için yerel kameraya karşılık gelen bir dizi kamera parametresi yazmanızı ve bir sonraki kullanılabilir çerçevenin kameraya karşılık gelen bir kamera parametreleri kümesini döndürmelidir. Bu iki küme `SimulationUpdateParameters` `SimulationUpdateResult` sırasıyla ve yapılarında kapsanır:

```cs
public struct SimulationUpdateParameters
{
    public int FrameId;
    public StereoMatrix4x4 ViewTransform;
    public StereoCameraFov FieldOfView;
};

public struct SimulationUpdateResult
{
    public int FrameId;
    public float NearPlaneDistance;
    public float FarPlaneDistance;
    public StereoMatrix4x4 ViewTransform;
    public StereoCameraFov FieldOfView;
};
```

Yapı üyeleri aşağıdaki anlamlara sahiptir:

| Üye | Description |
|--------|-------------|
| Frameıd | Sürekli çerçeve tanımlayıcısı. SimulationUpdateParameters girişi için gereklidir ve her yeni çerçeve için sürekli olarak arttırılmaları gerekir. Henüz kullanılabilir çerçeve verisi yoksa SimulationUpdateResult içinde 0 olur. |
| ViewTransform | Çerçevenin kamera görünümü dönüştürme matrislerinin sol sağ-Stereo çifti. Monoscopic işleme için yalnızca `Left` üye geçerlidir. |
| FieldOfView | [Görünüm kuralı 'Ndaki Openxr alanında](https://www.khronos.org/registry/OpenXR/specs/1.0/html/xrspec.html#angles)çerçeve kameranın görünüm alanlarının sol alt düğmesi çifti. Monoscopic işleme için yalnızca `Left` üye geçerlidir. |
| Kabul eden bir Planemesafeyi | geçerli uzak çerçevenin İzdüşüm matrisi için kullanılan, neredeyse düzlem uzaklığı. |
| FarPlaneDistance | geçerli uzak çerçevenin İzdüşüm matrisi için kullanılan, uzak düzlem uzaklığı. |

`ViewTransform` `FieldOfView` Stereoscopic oluşturma özelliği etkin olduğunda, stereo çiftleri ve hem göz-kamera değerlerini ayarlamaya izin verir. Aksi takdirde, `Right` Üyeler göz ardı edilir. Gördüğünüz gibi, projeksiyon matrisleri belirtilmediği sürece yalnızca kameranın dönüşümü düz 4x4 dönüşüm matrisleri olarak geçirilir. Gerçek Matrisler, Azure uzaktan Işleme tarafından, belirtilen görünüm alanları ve [CAMERASETTINGS API](../overview/features/camera.md)'sinde bulunan geçerli neredeyse düzlem ve en yüksek düzlem kullanılarak dahili olarak hesaplanır.

Çalışma zamanı sırasında [CameraSettings](../overview/features/camera.md) üzerinde neredeyse düzlemi ve en son düzlemi istediğiniz gibi değiştirebileceğinizden ve hizmet bu ayarları zaman uyumsuz olarak uygulayacağından, her SimulationUpdateResult, ilgili çerçevenin işlenmesi sırasında kullanılan belirli bir neredeyse düzlemi ve en yüksek düzlemi de taşır. Bu düzlem değerlerini, yerel nesneleri işlemeye yönelik projeksiyon matrislerinizi uzak çerçeve işlemeyle eşleşecek şekilde uyarlamak için kullanabilirsiniz.

Son olarak, **simülasyon güncelleştirme** çağrısı Için OpenXR kuralındaki görünüm alanı gerekir, bu arada, standartlaşma ve algoritmik güvenlik nedenleriyle, aşağıdaki yapı popülasyonu örneklerinde gösterilen dönüştürme işlevlerinin kullanımını sağlayabilirsiniz:

```cs
public SimulationUpdateParameters CreateSimulationUpdateParameters(int frameId, Matrix4x4 viewTransform, Matrix4x4 projectionMatrix)
{
    SimulationUpdateParameters parameters = default;
    parameters.FrameId = frameId;
    parameters.ViewTransform.Left = viewTransform;
    if (parameters.FieldOfView.Left.FromProjectionMatrix(projectionMatrix) != Result.Success)
    {
        // Invalid projection matrix
        throw new ArgumentException("Invalid projection settings");
    }
    return parameters;
}

public void GetCameraSettingsFromSimulationUpdateResult(SimulationUpdateResult result, out Matrix4x4 projectionMatrix, out Matrix4x4 viewTransform, out int frameId)
{
    projectionMatrix = default;
    viewTransform = default;
    frameId = 0;

    if (result.FrameId == 0)
    {
        // Invalid frame data
        return;
    }

    // Use the screenspace depth convention you expect for your projection matrix locally
    if (result.FieldOfView.Left.ToProjectionMatrix(result.NearPlaneDistance, result.FarPlaneDistance, DepthConvention.ZeroToOne, out projectionMatrix) != Result.Success)
    {
        // Invalid field-of-view
        return;
    }
    viewTransform = result.ViewTransform.Left;
    frameId = result.FrameId;
}
```

```cpp
SimulationUpdateParameters CreateSimulationUpdateParameters(uint32_t frameId, Matrix4x4 viewTransform, Matrix4x4 projectionMatrix)
{
    SimulationUpdateParameters parameters;
    parameters.FrameId = frameId;
    parameters.ViewTransform.Left = viewTransform;
    if (FovFromProjectionMatrix(projectionMatrix, parameters.FieldOfView.Left) != Result::Success)
    {
        // Invalid projection matrix
        return {};
    }
    return parameters;
}

void GetCameraSettingsFromSimulationUpdateResult(const SimulationUpdateResult& result, Matrix4x4& projectionMatrix, Matrix4x4& viewTransform, uint32_t& frameId)
{
    if (result.FrameId == 0)
    {
        // Invalid frame data
        return;
    }

    // Use the screenspace depth convention you expect for your projection matrix locally
    if (FovToProjectionMatrix(result.FieldOfView.Left, result.NearPlaneDistance, result.FarPlaneDistance, DepthConvention::ZeroToOne, projectionMatrix) != Result::Success)
    {
        // Invalid field-of-view
        return;
    }
    viewTransform = result.ViewTransform.Left;
    frameId = result.FrameId;
}
```

Bu dönüştürme işlevleri, yerel işleme gereksinimlerinize bağlı olarak, alan görünüm belirtimi ve düz 4x4 perspektif projeksiyon matrisi arasında hızlı geçişe izin verir. Bu dönüştürme işlevleri doğrulama mantığını içerir ve giriş projeksiyon matrislerinin veya giriş alanlarının geçersiz olması durumunda geçerli bir sonuç ayarlamadan hataları döndürür.

## <a name="api-documentation"></a>API belgeleri

* [C# RemoteManagerStatic. StartupRemoteRendering ()](/dotnet/api/microsoft.azure.remoterendering.remotemanagerstatic.startupremoterendering)
* [C# GraphicsBinding sınıfı](/dotnet/api/microsoft.azure.remoterendering.graphicsbinding)
* [C# GraphicsBindingWmrD3d11 sınıfı](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingwmrd3d11)
* [C# GraphicsBindingSimD3d11 sınıfı](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11)
* [C++ Remoterenderingınitialization yapısı](/cpp/api/remote-rendering/remoterenderinginitialization)
* [C++ GraphicsBinding sınıfı](/cpp/api/remote-rendering/graphicsbinding)
* [C++ GraphicsBindingWmrD3d11 sınıfı](/cpp/api/remote-rendering/graphicsbindingwmrd3d11)
* [C++ GraphicsBindingSimD3d11 sınıfı](/cpp/api/remote-rendering/graphicsbindingsimd3d11)

## <a name="next-steps"></a>Sonraki adımlar

* [Kamera](../overview/features/camera.md)
* [Geç aşama yeniden projeksiyonu](../overview/features/late-stage-reprojection.md)
* [Öğretici: uzaktan işlenmiş modelleri görüntüleme](../tutorials/unity/view-remote-models/view-remote-models.md)