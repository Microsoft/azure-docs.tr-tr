---
title: Öğretici-Azure uzamsal bağlantıları kullanarak yeni bir HoloLens Unity uygulaması oluşturmaya yönelik adım adım yönergeler | Microsoft Docs
description: Bu öğreticide, Azure uzamsal bağlayıcıları kullanarak yeni bir HoloLens Unity uygulaması oluşturmayı öğreneceksiniz.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 07/05/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 7e7d4a542146fb8d342cce6c34f9d97e72349f84
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561393"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-hololens-unity-app-using-azure-spatial-anchors"></a>Öğretici: Azure uzamsal bağlayıcıları kullanarak yeni bir HoloLens Unity uygulaması oluşturmaya yönelik adım adım yönergeler

Bu öğreticide, Azure uzamsal bağlayıcılarla yeni bir HoloLens Unity uygulamasının nasıl oluşturulacağı gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için şunlar sahip olduğunuzdan emin olun:

1. **Evrensel Windows platformu geliştirme** iş yükü ve **Windows 10 SDK (10.0.18362.0 veya üzeri)** bileşeni ve <a href="https://git-scm.com/download/win" target="_blank">Windows için git</a>ile <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017 +</a> yüklü bir Windows makinesi.
2. Visual Studio için [ C++/wınrt Visual Studio uzantısı (VSIX)](https://aka.ms/cppwinrt/vsix) [Visual Studio Market](https://marketplace.visualstudio.com/)yüklenmelidir.
3. [Geliştirici modu](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) etkin bir HoloLens cihazı. Bu makalede, [Windows 10 ekim 2018 güncelleştirmesi](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (RS5 olarak da bilinir) Ile bir HoloLens cihazı gerekir. HoloLens 'te en son sürüme güncelleştirmek için **Ayarlar** uygulamasını açın, **güncelleştirme & güvenlik**' e gidin ve **Güncelleştirmeleri denetle** düğmesini seçin.

## <a name="getting-started"></a>Başlarken

İlk olarak proje ve Unity sahümüzü ayarlayacağız:
1. Unity 'yi başlatın.
2. **Yeni**'yi seçin.
4. **3B** 'in seçili olduğundan emin olun.
5. Projenizi adlandırın ve bir kaydetme **konumu**girin.
6. Tıklayın **proje oluştur**.
7. Boş varsayılan sahneyi kullanarak yeni bir dosyaya kaydedin:Dosya > **farklı kaydet**.
8. Yeni sahneyi **Main** olarak adlandırın ve **Kaydet** düğmesine basın.

**Proje ayarlarını ayarlama**

Şimdi geliştirme için Windows holographic SDK 'sını hedefmize yardımcı olan bazı Unity proje ayarlarını ayarlayacağız. 

İlk olarak, uygulamamız için kalite ayarları ayarlamanıza izin verir. 
1. **Proje ayarlarını** > Düzenle > **kalitesini** seçin
2. **Windows Mağazası** logosunun altındaki sütunda, **varsayılan** satırdaki oka tıklayın ve **çok düşük**' ı seçin. **Windows Mağazası** sütununda ve **çok düşük** satırdaki kutu yeşil olduğunda ayarın doğru uygulandığını bilirsiniz.

Unity 'nin dışarı aktarmaya çalıştığınız uygulamanın 2B görünümü yerine bir derinlikli görünüm oluşturması gerektiğini bilmesini istiyoruz. Windows 10 SDK 'sını hedefleyen Unity üzerinde sanal gerçeklik desteğini etkinleştirerek bir derinlikli görünüm oluşturuyoruz.

1.  > **Proje**ayarlarıoynatıcıyı > Düzenle ' ye gidin.
2. **Oynatıcı ayarları**Için **Inspector panelinde** **Windows Mağazası** simgesini seçin.
3. **XR ayarları** grubunu genişletin.
4. **Oluşturma** bölümünde, yeni bir **sanal gerçeklik SDK 'sı** listesi eklemek için **sanal gerçeklik destekleniyor** onay kutusunu işaretleyin.
5. **Windows Mixed Reality** 'nin listede göründüğünü doğrulayın. Aksi takdirde, listenin altındaki **+** düğmeyi seçin ve **Windows Mixed Reality**' yi seçin.
 
> [!NOTE]
> Windows Mağazası simgesini görmüyorsanız, yüklemeden önce Windows Mağazası .NET betiği arka ucunu seçtiğinizden emin olmak için çift işaretleyin. Aksi takdirde, Unity 'yi doğru Windows yüklemesiyle yeniden yüklemeniz gerekebilir.

**Komut dosyası arka uç yapılandırmasını doğrula**
1.  > **Proje ayarları** oyuncusunu Düzenle ' ye gidin (hala Player 'ın önceki adımdan açık olması gerekebilir). > 
2. **Oynatıcı ayarları**Için **Inspector panelinde** **Windows Mağazası** simgesini seçin.
3. **Diğer ayarlar** yapılandırması bölümünde, **komut dosyası arka** ucunun **IL2CPP**olarak ayarlandığından emin olun.

**Özellikleri ayarla**
1.  > **Proje ayarları** oyuncusunu Düzenle ' ye gidin (hala Player 'ın önceki adımdan açık olması gerekebilir). > 
2. **Oynatıcı ayarları**Için **Inspector panelinde** **Windows Mağazası** simgesini seçin.
3. **Yayımlama ayarları** yapılandırması bölümünde, **ınternetclientserver** ve **spatialperception**' yı denetleyin.

**Ana sanal kamerayı ayarlama**
1. **Hiyerarşi panelinde**, **ana kamera**' ı seçin.
2. **Denetçisinde**, dönüştürme konumunu **0, 0, 0**olarak ayarlayın.
3. **Clear Flags** özelliğini bulun ve açılan menüyü **ufuk kutusundan** **düz renk**olarak değiştirin.
4. Bir renk seçici açmak için **arka plan** alanına tıklayın.
5. **R, G, B ve A** 'yı **0**olarak ayarlayın.
6. **Bileşen Ekle** ' yi seçin ve **uzamsal eşleme Collider**eklemek için arama yapın.

**Betiğimizi oluşturun**
1. **Proje** bölmesinde, **varlıklar** klasörü altında yeni bir klasör, **komut dosyaları**oluşturun. 
2. Klasöre sağ tıklayın, ardından **> Oluştur**,  **C# komut dosyası**' nı seçin. Başlık **AzureSpatialAnchorsScript**. 
3. **Oyun nesnesi** -> **Oluştur boş**öğesine gidin. 
4. Bunu seçin ve **Inspector** 'Da **Gameobject** Iken **mixedrealitycloud**olarak yeniden adlandırın. **Bileşen Ekle** ' yi seçin ve **AzureSpatialAnchorsScript**ekleyin ve ekleyin.

**Sphere prefab oluşturma**
1. **Gameobject** -> **3B nesne** -> **Sphere**öğesine gidin.
2. **Denetçisinde**, ölçeğini **0,25, 0,25, 0,25**olarak ayarlayın.
3. **Hiyerarşi** bölmesinde **Sphere** nesnesini bulun. Üzerine tıklayın ve **Proje** bölmesindeki **varlıklar** klasörüne sürükleyin.
4. **Hiyerarşi** bölmesinde oluşturduğunuz orijinal küreyi sağ tıklayıp **silin** .

Artık **Proje** bölmesinizdeki bir Sphere prefab sahibi olmanız gerekir.

## <a name="trying-it-out"></a>Deneniyor
Her şeyin çalıştığını test etmek için, uygulamanızı **Unity** 'de derleyin ve **Visual Studio**'dan dağıtın. Mr temel 100 bölümünde bulunan [**Bölüm 6 ' yı izleyin: Unity** kursu](https://docs.microsoft.com/windows/mixed-reality/holograms-100#chapter-6---build-and-deploy-to-device-from-visual-studio) ile çalışmaya başlama. Unity başlangıç ekranı ' nı ve ardından temiz bir ekran görmeniz gerekir.

## <a name="place-an-object-in-the-real-world"></a>Gerçek dünyaya bir nesne yerleştirme
Uygulamanızı kullanarak bir nesne oluşturalım &. [Uygulamamızı](#trying-it-out)dağıttığımızda oluşturduğumuz Visual Studio çözümünü açın. 

İlk olarak, aşağıdaki içeri aktarmaları içine `Assembly-CSharp (Universal Windows)\Scripts\AzureSpatialAnchorsScript.cs`ekleyin:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=19-24)]

Ardından, aşağıdaki üye değişkenlerini sınıfınıza `AzureSpatialAnchorsScript` ekleyin: 

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=26-42,48-52,60-79)]

Devam etmeden önce, Spburprefab üye değişkenimizde oluşturduğumuz Sphere ön kümesini ayarlamanız gerekir. **Unity**'ye geri dönün.
1. **Unity**'de **hiyerarşi** bölmesinde **mixedrealitycloud** nesnesini seçin.
2. **Proje** bölmesinde kaydettiğiniz **Sphere** prefab öğesine tıklayın. Tıklattığınız **Sphere** öğesini **Inspector** bölmesindeki **Azure uzamsal bağlayıcı betiği (betik)** altındaki **Sphere prefab** alanına sürükleyin.

Artık betiğinizdeki ön **küm** kümesine sahip olmanız gerekir. **Unity** 'den derleyin ve ardından ortaya çıkan **Visual Studio** çözümünü yeniden [açın.](#trying-it-out) 

**Visual Studio**'da `AzureSpatialAnchorsScript.cs` yeniden açın. Aşağıdaki kodu `Start()` yöntemine ekleyin. Bu kod `GestureRecognizer`, bir AIR Tap ve Call `HandleTap`olduğunda tespit edilecek.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=81-90,93&highlight=4-10)]

Şimdi aşağıdaki `HandleTap()` `Update()`yöntemi eklememiz gerekir. Bu, bir Ray cast ve bir kürenin yerleştirileceği bir isabet noktası alır. 

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=267-277,299-300,304-312)]

Şimdi Sphere öğesini oluşturuyoruz. Sphere başlangıçta beyaz olur, ancak bu değer daha sonra üzerinde ayarlanır. Aşağıdaki `CreateAndSaveSphere()` yöntemi:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-325,390)]

Uygulamanızı bir kez daha doğrulamak için **Visual Studio** 'dan çalıştırın. Bu kez, beyaz küetinizi istediğiniz yüzey üzerine yerleştirmek & oluşturmak için ekrana dokunun.

## <a name="set-up-the-dispatcher-pattern"></a>Dağıtıcı modelini ayarlama

Unity ile çalışırken, Kullanıcı arabirimi güncelleştirmelerini yapmak için kullandığınız API 'ler için tüm Unity API 'Lerinin ana iş parçacığında gerçekleştirilmesi gerekir. Ancak yazılacak kodda, diğer iş parçacıklarında geri çağrılar yaptık. Bu geri çağırmalar içindeki kullanıcı arabirimini güncelleştirmek istiyoruz, bu nedenle bir yan iş parçacığından ana iş parçacığına gitmenin bir yolu olmalıdır. Bir yan iş parçacığından ana iş parçacığında kod yürütmek için dağıtıcı modelini kullanacağız. 

Bir eylem kuyruğu olan dispatchQueue olan bir üye değişkeni ekleyelim. Eylemleri sıraya göndereceğiz ve sonra ana iş parçacığında eylemleri yeniden kuyruğa alacak ve çalıştıracağız. 

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=38-51&highlight=6-9)]

Ardından, kuyruğa eylem eklemenin bir yolunu ekleyelim. Hemen `QueueOnUpdate()` sonra`Update()` ekle:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=107-117)]

Şimdi, kuyruğa alınmış bir eylem olup olmadığını denetlemek için Update () döngüsünü kullanalım. Varsa, eylemi sıradan çıkar ve çalıştıracağız.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=95-105&highlight=4-10)]

## <a name="get-the-azure-spatial-anchors-sdk"></a>Azure uzamsal çıpası SDK 'sını alın

Şimdi Azure uzamsal bağlayıcı SDK 'sını indireceğiz. [Azure uzamsal bağlantıları GitHub yayınları sayfasına](https://github.com/Azure/azure-spatial-anchors-samples/releases)gidin. Varlıklar altında **AzureSpatialAnchors. unitypackage** dosyasını indirin. 

Unity 'de **varlıklar**' a gidin, **paket** > **özel paketi al..** . öğesine tıklayın. Pakete gidin ve **Aç**' ı seçin.

Açılan yeni **Unity paketi Içeri aktarma** penceresinde sol alt kısımdaki **yok** ' u seçin. Ardından **AzureSpatialAnchorsPlugin** > **eklentileri**altında **ortak**, **Düzenleyici**ve **HoloLens**' i seçin. Sağ alt köşedeki **Içeri aktar** ' a tıklayın.

Azure uzamsal bağlayıcı SDK 'sını almak için artık NuGet paketlerini geri yüklemeniz gerekiyor. **Unity** 'den derleyin ve ardından ortaya çıkan **Visual Studio** çözümünü tekrar deneyin ve yeniden [oluşturun.](#trying-it-out) 

**Visual Studio** çözümünüzde aşağıdaki içeri aktarmayı `<ProjectName>\Assets\Scripts\AzureSpatialAnchorsScript.cs`ekleyin:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=23-26&highlight=1)]

Ardından, aşağıdaki üye değişkenlerini sınıfınıza `AzureSpatialAnchorsScript` ekleyin:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=48-63&highlight=6-11)]

## <a name="attach-a-local-azure-spatial-anchor-to-the-local-anchor"></a>Yerel bağlantı noktasına yerel bir Azure uzamsal Bağlayıcısı ekleyin

Azure uzamsal bağlayıcının CloudSpatialAnchorSession ayarlayalim. Aşağıdaki `InitializeSession()` yöntemi `AzureSpatialAnchorsScript` sınıfınızın içine ekleyerek başlayacağız. Bir kez çağrıldıktan sonra, uygulamanızın başlatılması sırasında bir Azure uzamsal bağlayıcı oturumunun oluşturulduğundan ve düzgün başlatılmış olduğundan emin olur.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=174-202,205-209)]

Şimdi temsilci çağrılarını işlemek için kod yazdık. Devam ettiğimiz için bunlara daha fazla ekleyeceğiz.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=211-226)]

Şimdi, yönteminizi uygulamanıza verlim `initializeSession()`. `Start()`

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=81-93&highlight=12)]

Son olarak, aşağıdaki kodu `CreateAndSaveSphere()` yöntemine ekleyin. Gerçek dünyaya yerleştirdiğimiz sphere öğesine yerel bir Azure uzamsal Bağlayıcısı ekler.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-338,390&highlight=14-25)]

Devam etmeden önce, henüz yoksa bir Azure uzamsal bağlayıcı hesap tanımlayıcısı ve anahtarı oluşturmanız gerekir. Elde etmek için aşağıdaki bölümü izleyin.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Yerel çıpası buluta yükleyin

Azure uzamsal bağlayıcılarınızın hesap tanımlayıcısı ve anahtarınız `Account Id` olduktan sonra öğesine gidin ve içine `SpatialAnchorsAccountId` ve `Account Key` içine `SpatialAnchorsAccountKey`yapıştırın.

Son olarak her şeyi bir araya alalım. `SpawnNewAnchoredObject()` Yönteminde aşağıdaki kodu ekleyin. Sphere oluşturulduktan hemen sonra `CreateAnchorAsync()` yöntemi çağıracaktır. Yöntem döndüğünde, aşağıdaki kod Sphere ' de bir son güncelleştirme gerçekleştirir ve rengini mavi olarak değiştirir.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-391&highlight=26-77)]

Uygulamanızı **Visual Studio** 'dan bir kez daha çalıştırın. Başlarınızın etrafında ilerleyin ve sonra kürenin yerleştirileceği yere dokunarak uçak yapın. Yeterli kare olduktan sonra Sphere, sarı olarak açılır ve bulut yüklemesi başlar. Karşıya yükleme tamamlandıktan sonra Sphere 'niz mavi olarak açılır. İsteğe bağlı olarak, uygulamanızın gönderdiği günlük iletilerini izlemek için **Visual Studio** içinde çıkış penceresini de kullanabilirsiniz. Oluşturma işleminin ilerleme durumunu ve karşıya yükleme tamamlandıktan sonra bulutun döndürdüğü bağlantı tanımlayıcısını izleyebilirsiniz.

> [!NOTE]
> "DllNotFoundException" alırsanız: ' AzureSpatialAnchors ' DLL dosyası yüklenemiyor: Belirtilen modül bulunamadı. ", çözümünüzü yeniden **temizlemeniz** ve **derlemeniz** gerekir.

## <a name="locate-your-cloud-spatial-anchor"></a>Bulut uzamsal çıpası bulun

Bir bağlantı, buluta yüklendi, yeniden bulmaya başlamaya hazırız. Aşağıdaki kodu `HandleTap()` yönteminizin içine ekleyelim. Bu kod şu şekilde olur:

* Çağırın `ResetSession()`, bunu `CloudSpatialAnchorSession` durdurur ve var olan mavi kümizi ekrandan kaldırır.
* Yeniden `CloudSpatialAnchorSession` başlatın. Bu, bulduğumuz bağlayıcının, oluşturduğumuz yerel bağlantı yerine buluttan geldiğinden emin olduğumuz için biz.
* Azure uzamsal bağlayıcılarında karşıya yüklemediğimiz bağlayıcıyı aramak için bir **izleyici** oluşturun.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=267-305&highlight=13-31,35-36)]

Şimdi `ResetSession()` ve`CleanupObjects()` yöntemlerinizi ekleyelim. Bunları aşağıya yerleştirebilirsiniz`QueueOnUpdate()`

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=119-172)]

Şimdi, sorgulamakta olduğumuz bağlayıcı bulunduğunda çağrılacak kodu yedeklemeniz gerekir. `InitializeSession()`İçinde, aşağıdaki geri çağırmaları ekleyin:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=200-206&highlight=4-5)]

 
Şimdi, CloudSpatialAnchor bulunduğu bir yeşil Sphere & oluşturacak kodu ekleyebilmenizi sağlar. Ayrıca, ekranı dokunarak bir kez daha yeniden etkinleştirerek tüm senaryoyu daha sonra tekrarlayabilirsiniz: başka bir yerel bağlayıcı oluşturun, karşıya yükleyin ve tekrar bulun.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=228-265)]

İşte bu kadar! Tüm senaryoyu uçtan uca denemek için uygulamanızı **Visual Studio** 'dan en son bir kez çalıştırın. Cihazınızın etrafında ilerleyin ve beyaz kürenin yerinizi koyun. Daha sonra, Sphere sarıya dönüşene kadar baş ortamınızı ortam verilerini yakalamaya devam ettirmeyi sürdürün. Yerel çıpası karşıya yüklenecek ve Sphere 'niz mavi kullanacaktır. Son olarak, daha sonra ekranınızı bir kez daha dokunduktan sonra yerel çıpası kaldırıldıktan sonra bulut karşılığına yönelik sorgu yapacağız. Bulut uzamsal bağlantı noktası bulunana kadar cihazınızı etrafında taşımaya devam edin. Yeşil Sphere 'ın doğru konumda görünmesi gerekir ve tüm senaryoyu yeniden tekrarlayabilirsiniz &.

[!INCLUDE [AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md)]