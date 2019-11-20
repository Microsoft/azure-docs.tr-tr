---
title: Unity ile Project Acoustics hızlı başlangıç
titlesuffix: Azure Cognitive Services
description: Unity 'de Project Acoustics tasarım denetimleriyle denemek ve Windows masaüstüne dağıtmak için örnek içeriği kullanın.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: fabdd221ef99414eae0156babbd76dedb1f0745d
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243015"
---
# <a name="project-acoustics-unity-quickstart"></a>Project Acoustics Unity hızlı başlangıç
Simülasyon ile desteklenen tasarım denetimleriyle denemek için Unity için Project Acoustics örnek içeriğini kullanın.

Yazılım gereksinimleri:
* Windows için [Unity 2018.2 +](https://unity3d.com)
* [Project Acoustics örnek içerik paketi](https://www.microsoft.com/download/details.aspx?id=57346)

Örnek pakete neler dahildir?
* Geometri, ses kaynakları ve oyunplay denetimleriyle Unity sahneyi
* Project Acoustics eklentisi
* Sahne için bakmış Acoustics varlıkları

## <a name="import-the-sample-package"></a>Örnek paketi içeri aktarma
Örnek paketi yeni bir Unity projesine aktarın.
1. Unity 'de **varlıklar**' a  > **paketi içeri aktar** > **özel paket**' e gidin.

    ![Unity Içeri aktarma paketi seçenekleri](media/import-package.png)  

1. **Projectacoustik. unitypackage**' ı seçin.

1. Unity paketini projenize bütünleştirmek için **Içeri aktar** düğmesini seçin.  
  
    ![Unity Içeri aktarma paketi iletişim kutusu](media/import-dialog.png)  

Paketi var olan bir projeye içeri aktarmak için bkz. ek adımlar ve notlar için [Unity tümleştirmesi](unity-integration.md) .

>[!NOTE]
>İçeri aktarma işlemi tamamlandıktan sonra konsol günlüğünde çeşitli hata iletileri görüntülenir. Sonraki adıma geçin ve Unity 'yi yeniden başlatın.

## <a name="restart-unity"></a>Unity 'yi yeniden Başlat
Acoustics araç setinin hazırlama kısmı .NET 4 gerektirir. *x* Scripting Runtime sürümü. Paket içeri aktarma, Unity oynatıcı ayarlarınızı güncelleştirir. Bu ayarın etkili olması için Unity 'yi yeniden başlatın. Ayarın geçerli olduğunu doğrulamak için **oynatıcı** ayarları ' nı açın:

![Unity proje ayarları menüsü](media/player-settings.png)  

![.NET 4. x seçiliyken Unity oynatıcı ayarları paneli](media/net45.png)  

>[!NOTE]
>Bu ekran görüntüsü Unity 2018 ' den alınmıştır. *x*. Görüntü, Unity 'nin daha yeni sürümlerinde farklılık gösterebilir.

## <a name="open-the-project-acoustics-bake-window"></a>Project Acoustics hazırlama penceresini açın
Unity 'de **pencere** menüsünde **Acoustics** öğesini seçin.

![Pencere menüsünde Acoustics seçeneği vurgulanmış olan Unity Düzenleyicisi](media/window-acoustics.png)

Kayan **Acoustics** penceresi açılır. Bu pencere, akustik simülasyonu 'nin özelliklerini ayarladığınız yerdir.

![Acoustics penceresi açık olan Unity Düzenleyicisi](media/unity-editor-plugin-window.png)  

## <a name="experiment-with-the-design-controls"></a>Tasarım denetimleriyle deneyin
*Projectacousticssample* klasöründe örnek sahneyi açın ve Unity düzenleyicisinde Oynat düğmesini seçin. Gezinmek için W, A, S ve D tuşlarını ve fareyi kullanın. Sahnenin Acoustics ve olmadan nasıl seslendiğini karşılaştırmak için, kaplama metni kırmızıya dönüşene kadar R tuşunu ve "Acoustics: Disabled" öğesini gösterir. Daha fazla denetim için klavye kısayollarını görmek için F1 ' i seçin. Ayrıca, bir eylem seçmek için sağ tıklayabilir ve ardından bu eylemi yapmak için sol ' a tıklayabilirsiniz.

*AcousticsAdjust* betiği, örnek sahnedeki ses kaynaklarına iliştirilir. Kaynak başına tasarım parametrelerine izin vermez.

![Unity AcousticsAdjust betiği](media/acoustics-adjust.png)

Aşağıdaki bölümler, kullanılabilir denetimleri kullanarak oluşturabileceğiniz bazı etkileri keşfedebilir. Her denetim hakkında ayrıntılı bilgi için [Project Acoustics Unity tasarım öğreticisine](unity-workflow.md)bakın.

### <a name="modify-distance-based-attenuation"></a>Uzaklık tabanlı zayıflamayı değiştirme
**Project Acoustics** Unity spatializer eklentisindeki ses dijital sinyal Işleme, Unity Düzenleyicisi 'nde yerleşik olarak bulunan kaynak başına uzaklık tabanlı zayıflamasına uyar. Uzaklık tabanlı zayıflalama denetimleri, **3B ses ayarları**altındaki ses kaynakları **denetçisi** panelinde bulunan **ses kaynağı** bileşenidir:

![Unity uzaklık zayıflalama seçenekleri bölmesi](media/distance-attenuation.png)

Project Acoustics, oynatıcı konum etrafında ortalanan bir "simülasyon bölgesi" kutusunda hesaplar. Örnek paketteki Acoustics varlıkları, oyuncu etrafında 45 ölçüm benzetimi bölge boyutuna bakmıştı. Bu nedenle, ses zayıflalama, 45 ile yaklaşık 0 ' a düşecek şekilde tasarlanmalıdır.

### <a name="modify-occlusion-and-transmission"></a>Occlusiyon ve iletimi değiştirme
* **Occlusiyon** çarpanı 1 ' den büyükse (varsayılan 1 ' dir), occlusiyon kullanılır. Daha hafif hale getirmek için, bunu 1 ' den küçük olarak ayarlayın.

* Duvar üzerinden iletimi etkinleştirmek için, **iletim (DB)** kaydırıcısını en düşük ayardan uzağa taşıyın.

### <a name="modify-wetness-for-a-source"></a>Bir kaynak için wetliği değiştirme
* Mesafede ne kadar hızlı bir şekilde değişiklik yapıldığını değiştirmek için, **algısal mesafe warp**kullanın. Simülasyon aracılığıyla Project Acoustics, algısal uzaklık ipuçları sağlayan ve uzaklığın düzgün şekilde değişen ıslak düzeylerini hesaplar. Uzaklık çarpmasının artırılması, mesafeyi ilgili ıslak düzeylerini artırarak bu etkiyi ayırır. 1 ' den küçük değerleri çarpıtma, uzaklık tabanlı ters getirme değişikliğini daha hafif hale getirir.

   Bu etkiye yönelik daha ayrıntılı ayarlamalar yapmak için, **Wettik (DB)** ayarını değiştirin.

* Boşluk boyunca zaman ölçeğini artırmak için, **Decay zaman ölçeğini**ayarlayın. Belirli bir kaynak dinleyicisi konum çiftinin simülasyon sonucu 1,5 saniyelik bir zaman alıyorsa ve **Decay zaman ölçeği** 2 olarak ayarlandıysa, kaynağa uygulanan Decay süresi 3 saniyedir.

## <a name="next-steps"></a>Sonraki adımlar
* [Unity tabanlı proje Acoustics tasarım denetimlerinde](unity-workflow.md)ayrıntıları okuyun.
* [Tasarım sürecinin](design-process.md)arkasındaki kavramları daha ayrıntılı bir şekilde araştırın.
* Ön ek ve hazırlama sürecini araştırmak için [bir Azure hesabı oluşturun](create-azure-account.md) .
