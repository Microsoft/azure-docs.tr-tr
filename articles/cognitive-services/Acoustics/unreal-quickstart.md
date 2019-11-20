---
title: Proje Acoustics hızlı başlangıç-gerçek olmayan
titlesuffix: Azure Cognitive Services
description: Proje Acoustics tasarım denetimleriyle gerçek zamanlı ve Wwise 'ta denemeler yapmak ve Windows Masaüstü ' ne dağıtmak için örnek içeriği kullanın.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: d3afcded894f72626a4f24bcbe85c34ac1329c29
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242915"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>Proje Acoustics Unreal/Wwise hızlı başlangıç
Bu hızlı başlangıçta, Unreal Engine ve Wwise için örnek içerik kullanarak Project Acoustics tasarım denetimleriyle denemeler yapacaksınız.

Örnek içeriği kullanmaya yönelik yazılım gereksinimleri:
* [Unreal Engine](https://www.unrealengine.com/) 4,22
* [Audiokinetik Wwise](https://www.audiokinetic.com/products/wwise/) 2019.1.2

## <a name="download-the-sample-package"></a>Örnek paketi indirin
[Projeyi Acoustics Unreal ve Wwise örnek paketini](https://www.microsoft.com/download/details.aspx?id=58090)indirin. Örnek paket şunları içerir:
- Unreal Engine projesi
- Gerçek olmayan proje için Wwise projesi
- Proje Acoustics Wwise eklentisi

## <a name="set-up-the-project-acoustics-sample-project"></a>Project Acoustics örnek projesini ayarlama
İlk olarak, Project Acoustics eklentisini Wwise 'a yüklemeniz gerekir. Sonra, Wwise ikililerini gerçek olmayan projeye dağıtın. Ardından, proje Acoustics desteklemek için Wwise Unreal eklentisini ayarlayın.

### <a name="install-the-project-acoustics-wwise-plug-in"></a>Projeyi Acoustics Wwise eklentisini yükler
Wwise başlatıcısı 'nı açın. **Eklentiler** sekmesinde, **yeni eklentiler yüklensin**bölümünde **dizinden Ekle**' yi seçin. İndirdiğiniz pakette bulunan *AcousticsWwisePlugin\ProjectAcoustics* dizinini seçin.

![Wwise eklentisini Wwise başlatıcısı 'na yüklemek için seçeneği](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>Proje Acoustics Unreal örnek projesine Wwise ikilileri ekleme
1. Wwise Başlatıcısı ' nda, **Unreal Engine** sekmesini seçin. 
1. **Son Real Engine projelerinin**yanındaki "hamburger" (Icon) menüsünü seçin ve ardından **proje için Gözat**' ı seçin. *AcousticsSample\AcousticsGame\AcousticsGame.uproject*paketindeki gerçek olmayan proje *. uıproject* dosyasını açın.

   ![Wwise başlatıcısı 'ndaki gerçek olmayan sekme](media/wwise-unreal-tab.png)

3. Project Acoustics örnek projesinin yanında, **projede Wwise 'ı tümleştirin**' ı seçin.

   ![Wwise başlatıcısı, tümleştir seçeneği vurgulanmış şekilde Acoustics Game Unreal projesini gösterir.](media/wwise-acoustics-game-project.png)

### <a name="extend-wwise-unreal-plug-in-functionality"></a>Wwise Unreal eklenti işlevlerini Genişlet
Project Acoustics Unreal eklentisinin, Wwise Unreal eklenti API 'sinden açık olan ek davranışları olması gerekir. Bu değişiklikleri otomatikleştirmek için Project Acoustics Unreal eklentisiyle birlikte gelen toplu iş dosyasını çalıştırın.
* *AcousticsGame\Plugins\ProjectAcoustics\Resources*Içinde, *patchwwise. bat*dosyasını çalıştırın.

    ![Bir Windows Explorer penceresinde vurgulanan Wwise projesine yama betiği](media/patch-wwise-script.png)

* DirectX SDK yüklü değilse: kullanmakta olduğunuz Wwise sürümüne bağlı olarak, *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs*içinde `DXSDK_DIR` içeren satırı açıklama eklemeniz gerekebilir:

    !["DXSDK" açıklama olarak gösterildiği kod Düzenleyicisi](media/directx-sdk-comment.png)

* Visual Studio 2019 kullanarak derlerseniz: bir bağlama hatasını Wwise ile çözmek Için, *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* Içinde *VSVersion* varsayılan değerini **vc150**olarak değiştirin:

    ![VSVersion içeren kod Düzenleyicisi "vc150" olarak değiştirildi](media/vsversion-comment.png)

### <a name="open-the-unreal-project"></a>Gerçek olmayan projeyi açma 
Gerçek olmayan projeyi açtığınızda, modülleri yeniden oluşturmanız istenir. **Evet**' i seçin.

Projeyi açmak derleme hataları nedeniyle başarısız olursa, Project Acoustics Wwise eklentisini Project Acoustics örnek projesinde kullanılan aynı Wwise sürümüne yüklediğinizden emin olun.

2019,1 sürümünden daha önce bir [Audiokinetik Wwise](https://www.audiokinetic.com/products/wwise/) sürümü kullanıyorsanız, Project Acoustics örnek projesini kullanarak ses bankalarını oluşturamazsınız. Wwise sürüm 2019,1 ' i örnek projede tümleştirmeniz gerekir.

## <a name="experiment-with-project-acoustics-design-controls"></a>Project Acoustics tasarım denetimleriyle deneyin
Gerçek olmayan düzenleyicide Oynat düğmesini seçerek sahnenin nasıl seslere dinleyebildiğini dinleyin. Gezinmek için W, A, S ve D tuşlarını ve fareyi kullanın. Ek denetimlerin klavye kısayollarını görmek için F1 ' i seçin.

Aşağıdaki bilgiler, deneyebileceğiniz bazı tasarım etkinliklerini açıklamaktadır.

### <a name="modify-occlusion-and-transmission"></a>Occlusiyon ve iletimi değiştirme
Her bir gerçek olmayan ses aktör üzerinde kaynak başına proje Acoustics tasarım denetimleri vardır.

![Unreal Editor Acoustics tasarım denetimleri](media/demo-scene-sound-source-design-controls.png)

**Occlusiyon** çarpanı 1 ' den büyükse (varsayılan 1 ' dir), occlusiyon kullanılır. 1 ' den küçük bir ayar, occlusiyon efektini daha hafif hale getirir.

Duvar üzerinden iletimi etkinleştirmek için, **iletim (DB)** kaydırıcısını en düşük düzeyinden uzağa taşıyın.

### <a name="modify-wetness-for-a-source"></a>Bir kaynak için wetliği değiştirme
Mesafede ne kadar hızlı bir şekilde değişiklik yapıldığını değiştirmek için, **algısal mesafe warp**kullanın. Project Acoustics, benzetim aracılığıyla boşluk boyunca ıslak düzeylerini hesaplar. Düzeyler, mesafede sorunsuzca farklılık gösterir ve algısal uzaklık ipuçları sağlar. Bu etkiyi ortadan kaldırmaya yönelik mesafeyi çarpmayı artırmak için mesafe ile ilgili ıslak düzeylerini artırın. 1 ' den küçük değerleri çarpıtma, uzaklık tabanlı ters getirme değişikliğini daha hafif hale getirir. Ayrıca, **Wettik (DB)** ayarı aracılığıyla bu etkiye yönelik daha ayrıntılı ayarlamalar yapabilirsiniz.

Boşluk boyunca zaman ölçeğini artırmak için, **zaman ölçeğini**ayarlayın. Simülasyon sonucunun 1,5 saniyelik bir süre olduğu bir durum düşünün. **Decay zaman ölçeğini** 2 olarak ayarlamak, kaynağa 3 saniyelik bir süre boyunca uygulanan bir süre boyunca neden olur.

### <a name="modify-distance-based-attenuation"></a>Uzaklık tabanlı zayıflamayı değiştirme
Project Acoustics Wwise Mixer eklentisi, Wwise içinde yerleşik olan kaynak başına uzaklık tabanlı zayıflalama ile uyar. Bu eğrinin değiştirilmesi, Kuru yol düzeyini değiştirir. Project Acoustics eklentisi, benzetim ve tasarım denetimleri tarafından belirtilen ıslak/kuru karışımını sürdürmek için ıslak düzeyini ayarlar.

![Simülasyon sınırından önce 0 ' a giderek zayıflalama gösteren, Wwise zayıflalama eğrisi bölmesi](media/demo-sounds-attenuation.png)

Project Acoustics, her bir sanal oynatıcı konumunun ortalanmasını sağlayan bir "simülasyon bölgesi" kutusunda hesaplar. Örnek paketteki Acoustics varlıkları, 45 ölçümün bir simülasyon bölge yarıçapı ile fırlaştırılmış. Zayıflamalar 45 metreden önce 0 ' a düşecek şekilde tasarlandı. Bu azalma kesin bir gereksinim olmadığından, desteklenmediği uyarısıyla yalnızca dinleyicinin 45 ölçümlerinin içindeki geometrinin, sesleri occside eder.

## <a name="next-steps"></a>Sonraki adımlar
* [Project Acoustics](unreal-integration.md) eklentisini gerçek olmayan projenizden tümleştirin.
* Kendi baklarınız için [bir Azure hesabı oluşturun](create-azure-account.md) .
