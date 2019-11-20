---
title: Proje Acoustics Unreal ve Wwise tümleştirmesi
titlesuffix: Azure Cognitive Services
description: Bu makalede, Project Acoustics Unreal ve Wwise eklentilerinin projenizin tümleştirilmesi açıklanır.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: e57212a3002390754aaebc5f2aa9ffb10af230a2
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243053"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>Proje Acoustics Unreal ve Wwise tümleştirmesi
Bu makalede, Project Acoustics eklenti paketinin mevcut olmayan gerçek ve Wwise oyun projenize nasıl tümleştirileceği açıklanır.

Yazılım gereksinimleri:
* [Unreal Engine](https://www.unrealengine.com/) 4.20 +
* [Audiokinetik Wwise](https://www.audiokinetic.com/products/wwise/) 2018,1
* [Gerçek olmayan için Wwise eklentisi](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  
  Wwise Unreal eklentisi yerine Wwise SDK 'sının doğrudan tümleştirmesini kullanıyorsanız, Project Acoustics Unreal eklentisini inceleyin ve Wwise API çağrılarını ayarlayın.

Proje Acoustics 'yi Wwise dışında bir ses altyapısıyla kullanmak için, [Project Acoustics tartışma forumu](https://github.com/microsoft/ProjectAcoustics/issues)'nda bir geliştirme isteği oluşturun. Acoustics verilerini sorgulamak ve altyapıya API çağrıları yapmak için Project Acoustics Unreal eklentisini kullanabilirsiniz.

## <a name="download-project-acoustics"></a>Proje Acoustics indir
Henüz yapmadıysanız [Project Acoustics Unreal ve Wwise eklenti paketini](https://www.microsoft.com/download/details.aspx?id=58090) indirin.

Pakette bir Unreal Engine eklentisi ve bir Wwise karıştırıcı eklentisi bulunur. Unreal eklentisi düzenleyici ve çalışma zamanı tümleştirmesi sağlar. Oyunplay sırasında Project Acoustics Unreal eklentisi her bir kare için her bir oyun nesnesi için occlusiyon gibi parametreleri hesaplar. Bu parametreler Wwise API çağrılarına çevrilir.

## <a name="integration-steps"></a>Tümleştirme adımları

Paketi yüklemek ve oyununuza dağıtmak için bu adımları izleyin.

### <a name="install-the-project-acoustics-mixer-plug-in"></a>Project Acoustics Mixer eklentisini yükler
1. Wwise başlatıcısı 'nı açın. **Eklentiler** sekmesinde, **yeni eklentiler yüklensin**bölümünde **dizinden Ekle**' yi seçin.

    ![Wwise başlatıcısında eklenti yüklemesi](media/wwise-install-new-plugin.png)

1. İndirme paketindeki *AcousticsWwisePlugin\ProjectAcoustics* dizinini seçin. Wwise karıştırıcı eklentisi grubunu içerir.

   Wwise eklentiyi yükleyecek. Proje Acoustics, Wwise 'daki yüklü eklentiler listesinde görünmelidir.  
![Project Acoustics yüklemesinden sonra, Wwise yüklü eklentiler listesi](media/unreal-integration-post-mixer-plugin-install.png)

### <a name="dedeploy-wwise-into-your-game"></a>Wwise 'i oyununuza geri dağıtmayı sağlama
Wwise 'ı zaten tümleştirmiş olsanız bile oyununuza yeniden dağıtın. Bu adım proje Acoustics Wwise eklentisini tümleştirir.

   > [!NOTE]
   > **Motor eklentisi:** Gerçek C++ olmayan bir projede oyun eklentisi olarak yüklüyse, bu adımı atlayın. Eğer gerçek olmayan projeniz yalnızca Blueprint olduğundan, örneğin, bir altyapı eklentisi olarak yüklenirse, karıştırıcı eklentisiyle Wwise dağıtımı daha karmaşıktır. Kukla boş olmayan bir C++ proje oluşturun. Açık olmayan düzenleyiciyi kapatın ve Wwise 'ı kukla projeye dağıtmak için kalan yordamı izleyin. Ardından, dağıtılan Wwise eklentisini kopyalayın.
 
1. Wwise Başlatıcısı ' ndan, **Unreal Engine** sekmesini seçin. **son olmayan altyapı projelerinin** yanındaki "hamburger" (Icon) menüsünü seçin ve ardından **proje için araştır**' ı seçin. Oyununuzun gerçek olmayan proje *. proje* dosyasını açın.

    ![Wwise başlatıcısı gerçek olmayan sekmesi](media/wwise-unreal-tab.png)

1. Projede **Wwise 'ı tümleştir** ' i seçin veya **Wwise 'ı projede değiştirin**. Bu adım, proje Acoustics Mixer eklentisi de dahil olmak üzere Wwise ikililerini projenize tümleştirir.

   > [!NOTE]
   > **Motor eklentisi:** Motor eklentisi olarak Wwise kullanıyorsanız ve daha önce açıklandığı gibi sözde projeyi oluşturduysanız, Wwise dağıtılan klasörü kopyalayın: *[DummyUProject] \Plugins\Wwise*. *[Uesource] \Engine\Plugins\Wwise*üzerine yapıştırın. *[DummyUProject]* , gerçek olmayan bir C++ proje yolundan ve *[Uesource]* , gerçek olmayan altyapı kaynaklarının yüklendiği yerdir. Klasörü kopyaladıktan sonra, sözde projeyi silebilirsiniz.

### <a name="add-the-project-acoustics-unreal-plug-in-to-your-game"></a>Oyununuza Acoustics projesini gerçek zamanlı olmayan eklentiyi ekleyin
 
1. Eklenti paketindeki *Unreal\projectacoustika* klasörünü kopyalayın. *[Uprojectdir] \ Plugins\projectacoustik*adlı yeni bir klasör oluşturun. burada *[uprojectdir]* , oyununuzun *. uıproject* dosyasını içeren proje klasörüdür.

   > [!NOTE]
   > **Motor eklentisi**: Wwise 'ı bir motor eklentisi olarak kullanıyorsanız, Project Acoustics ' ı gerçek olmayan bir altyapı eklentisi olarak kullanmanız gerekir. Daha önce alıntı yapılan hedef dizin yerine *[Uesource] \Engine\plugins\projectacoustiği*kullanın.

1. *Projectacoustika* klasörüyle birlikte bir *Wwise* klasörü görtığınızdan emin olun. Daha önce dağıttığınız karıştırıcı eklentisinin ikili dosyaları ile birlikte Wwise eklentisini içerir.

### <a name="extend-wwise-unreal-plug-in-functionality"></a>Wwise Unreal eklenti işlevlerini Genişlet
Project Acoustics Unreal eklentisi, [Bu kurallar](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html)uyarınca Wwise Unreal eklentisi API 'sinden sunulan ek davranışları gerektirir. Düzeltme eki uygulama yordamını otomatikleştirmek için bir toplu iş dosyası ekledik.

* *Plugins\projectacoustics\resources*Içinde, *patchwwise. bat*dosyasını çalıştırın. Aşağıdaki örnek görüntü, AcousticsGame örnek projemizi kullanır.

    ![Wwise 'a düzeltme eki uygulamak için betiğe sahip bir Windows Explorer penceresi](media/patch-wwise-script.png)

* DirectX SDK yüklü değilse: kullanmakta olduğunuz Wwise sürümüne bağlı olarak, *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs*içinde `DXSDK_DIR` içeren satırı açıklama eklemeniz gerekebilir:

    ![' DXSDK ' öğesini gösteren kod Düzenleyicisi açıklama](media/directx-sdk-comment.png)

* Visual Studio 2019 kullanarak derlerseniz: bir bağlama hatasını Wwise ile çözmek Için *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* içindeki varsayılan `VSVersion` değerini el ile **vc150**olarak değiştirin:

    !["VSVersion" öğesini gösteren kod Düzenleyicisi "vc150" olarak değiştirildi](media/vsversion-comment.png)

### <a name="build-the-game-and-check-that-python-is-enabled"></a>Oyunu oluşturun ve Python 'un etkin olduğunu denetleyin

1. Oyununuzu derleyin ve doğru şekilde oluşturulduğundan emin olun. Derlenmezse, devam etmeden önce önceki adımları dikkatle kontrol edin.

1. Projenizi gerçek olmayan düzenleyicide açın.

    > [!NOTE]
    > **Motor eklentisi:** Bir altyapı eklentisi olarak Projectacoustik kullanıyorsanız, "yerleşik" eklentiler altında da etkinleştirildiğinden emin olun.

    Project Acoustics 'in tümleştirildiğini gösteren yeni bir mod görmeniz gerekir.

    ![Acoustics modu tamamen gerçek değil](media/acoustics-mode-full.png)

1. Düzenleyici tümleştirmesi 'nin doğru çalışması için, Unreal için Python eklentisinin etkinleştirildiğinden emin olun.

    ![Gerçek olmayan düzenleyicideki Python uzantıları etkin](media/ensure-python.png)

### <a name="set-up-your-wwise-project-to-use-project-acoustics"></a>Proje Acoustics kullanmak için Wwise projenizi ayarlama

Örnek indirme ' ye örnek bir Wwise projesi dahildir. Bu yönergelerden birlikte görüntülemenizi öneririz. Bu makalenin ilerleyen bölümlerindeki ekran görüntüleri bu projeden alınır.

#### <a name="bus-setup"></a>Veri yolu kurulumu
Project Acoustics Unreal eklentisi, tam adı `Project Acoustics Bus` olan bir veri yolundaki ilişkili karıştırıcı eklentisini arayacaktır. Aynı ada sahip yeni bir ses veri yolu oluşturun. Karıştırıcı eklentisi çeşitli yapılandırmalarda çalışabilir. Ancak şimdilik yalnızca yankı işleme için kullanılacaksınız. Bu veri yolu, Acoustics kullanan tüm kaynaklar için karma ters b sinyalini taşır. Herhangi bir veri yolu karıştırma yapısına yukarı akış karışımı verebilir. Örnek indirmeye dahil edilen Wwise örnek projesinden bir örnek gösterilir.

![Project Acoustics Bus 'ı gösteren Wwise veri yolları](media/acoustics-bus.png)

1. Veri yolundaki kanal yapılandırmasını *1,0*, *2,0*, *4,0*, *5,1*veya *7,1*olarak ayarlayın. Diğer herhangi bir ayar, veri yolunda çıkış olmadan sonuçlanır.

    ![Project Acoustics Bus için kanal yapılandırma seçenekleri](media/acoustics-bus-channel-config.png)

1. Project Acoustics Bus ayrıntılarına gidin ve **karıştırıcı eklentisi** sekmesini gördiğinizden emin olun.

    ![Proje Acoustics Bus etkin için karıştırıcı eklenti sekmesi ile Wwise](media/mixer-tab-enable.png)

1. **Karıştırıcı eklenti** sekmesine gidin ve Project Acoustics Mixer eklentisini Bus 'a ekleyin.

    ![Wwise Bus 'a Project Acoustics Mixer eklentisinin nasıl ekleneceğini gösteren diyagram](media/add-mixer-plugin.png)

#### <a name="actor-mixer-hierarchy-setup"></a>Aktör-karıştırıcı hiyerarşisi kurulumu
En iyi performansı elde etmek için Project Acoustics, tüm kaynaklara aynı anda ses dijital sinyal işleme uygular. Bu nedenle, eklentinin bir karıştırıcı eklentisi olarak çalışması gerekir. Wwise, çıkış veri yolu, genellikle kuru çıkış sinyalini taşısa da, karıştırıcı eklentilerinin çıkış veri yolunda olmasını gerektirir. Project Acoustics,, IST sinyali `Project Acoustics Bus` ' a taşınırken, ınsesignal 'ın AUX veri yolları aracılığıyla yönlendirilmesini gerektirir. Aşağıdaki işlem, bu sinyal akışına aşamalı geçişi destekler.

En üst düzeyde *giyme adımları, silah*ve diğerleri içeren biraktör karıştırıcı hiyerarşisi olan mevcut bir projeniz olduğunu varsayalım. Her birinin, Kuru karışımı için karşılık gelen bir çıkış veri yolu vardır. Acoustics kullanma adımlarını geçirmek istediğinizi varsayalım. İlk olarak, ilgili bir AUX veri yolu oluşturun ve bu, bir alt karışımı çıkış veri yolunun alt karışımını taşır. Örneğin, tam ad önemli olmamasına rağmen, yolları düzenlemek için aşağıdaki görüntüde bir "kuru" öneki kullandınız. İşle ilgili veri yolunda sahip olduğunuz ölçüm veya efektler daha önce olduğu gibi çalışır.

![Önerilen Wwise kuru karışımı kurulumu](media/wwise-dry-mix-setup.png)

Daha sonra, aşağıdaki gibi,,,, **Çıkış veri**yolu olarak ayarlanan *Project Acoustics Bus* ve *Dry_Footsteps* Kullanıcı tanımlı bir AUX veri yolu olarak ayarlanmış şekilde, bu oyuncu için veri yolu çıkış yapısını aşağıda gösterildiği gibi değiştirin.

![Önerilen Wwise aktör karıştırıcı veri yolu kurulumu](media/actor-mixer-bus-settings.png)

Artık tüm bu adımlar Acoustics işleme alır ve proje Acoustics veri yolunda ters b 'yi çıktı. Kuru sinyali, her zamanki gibi Dry_Footsteps ve maça aracılığıyla yönlendirilir.

Project Acoustics yalnızca dünyanın bir 3B konumuna sahip olan sesler için geçerlidir. Aşağıdaki [Wwise belgelerinde](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/), konumlandırma özelliklerinin gösterildiği gibi ayarlanması gerekir. **3B Istenmeyen kullanım** ayarı, gerektiğinde *konum* veya *konum + yönlendirme* olabilir.

![Önerilen Wwise aktör konumlandırma ayarları](media/wwise-positioning.png)

**Çıktı veri** yolunu *Project Acoustics Bus*içine yukarı akış sağlayan diğer bir veri yoluna ayarlayamazsınız. Wwise, bu gereksinimi karıştırıcı eklentilerde uygular.

It adımlarında aktör-karıştırıcı hiyerarşisinde Acoustics kullanmayan bir alt öğe istiyorsanız, bunu kabul etmek için üzerinde "üst öğeyi geçersiz kıl" kullanabilirsiniz.

Oydaki herhangi bir oyuncu karıştırıcı için oyun veya Kullanıcı tanımlı kullanıcı tanımlı gönderme kullanıyorsanız, her iki kez ters uygulamayı önlemek için bu oyuncu karıştırıcı üzerinde kapatın.

#### <a name="spatialization"></a>Gereksiz duruma getirme
Project Acoustics Wwise Mixer eklentisi, varsayılan olarak, bir evlenme geri çağrı uygular. Project Acoustics 'i yalnızca bu varsayılan ters b yapılandırmasında kullandığınızda, Kuru karışımında herhangi bir kanal yapılandırması ve gereksiz hale getirme yöntemini kullanabilirsiniz. Bu nedenle, Acoustics ters b projesi ile neredeyse tüm spatializer 'ı karıştırabilir ve eşleştirebilirsiniz. Seçenekleriniz [Ambisonics tabanlı Binaural spatialsleyiciler](https://www.audiokinetic.com/products/ambisonics-in-wwise/) ve [Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound)' i içerir.
 
Project Acoustics, hem nesne tabanlı yüksek çözünürlüklü HRTF 'leri oluşturmayı hem de kaydırmayı destekleyen, isteğe bağlı bir spatializer içerir. Karıştırıcı eklenti ayarları ' nda **Istenmeyen kullanım yap** onay kutusunu seçin ve *HRTF* veya *kaydırma*arasında seçim yapın. Ayrıca, Project Acoustics Mixer eklentisi ve Wwise tarafından iki kez çıkarmamak için Kullanıcı tanımlı yedek göndermeleri tüm kurutma yollarına devre dışı bırakın. Gereksiz duruma getirme modu, bir ses Bankası yeniden oluşturma gerektirdiğinden gerçek zamanlı olarak değiştirilemez. Gerçek zamanlı olmayan şekilde yeniden başlatın ve ardından, **Istenmeyen kullanım** oluşturma onay kutusu ayarı gibi karıştırıcı eklentisi yapılandırma değişikliklerini bütünleştirmek için Yürüt ' ü seçmeden önce soundbankalarını yeniden oluşturun.

![Wwise karıştırıcı eklenti gereksiz kullanım ayarları](media/mixer-spatial-settings.png)

Ne yazık ki, diğer nesne tabanlı spatializer eklentileri şu anda desteklenmemektedir. Bunlar, karıştırıcı eklentileri olarak uygulanır ve Wwise, birden çok karıştırıcı eklentisinin tek bir aktör Mixer 'e atanmasına izin vermez.  

### <a name="audio-setup-in-unreal"></a>Ses kurulumu, gerçek olmayan
1. İlk olarak, *Content\Acoustics*'e yerleştirilecek bir Acoustics varlığı oluşturmak için oyun düzeyinize bakmeniz gerekir. [Gerçek olmayan bakışta öğreticiye](unreal-baking.md)danışın. Önceden tanımlanmış bazı düzeyler örnek paketine dahildir.

1. Sahnede bir Acoustics Space aktör oluşturun. Bu aktörlerin yalnızca birini bir düzey içinde oluşturun, çünkü tüm düzeyin Acoustics temsil eder.

    ![Gerçek olmayan düzenleyicide Acoustics Space aktör oluşturma](media/create-acoustics-space.png)

1. Bakmış akustik veri varlığını Acoustics Space aktör üzerindeki Acoustics veri yuvasına atayın. Sahnede artık Acoustics vardır!

    ![Acoustics varlık ataması-gerçek olmayan düzenleyicide](media/acoustics-asset-assign.png)

1. Boş aktör ekleyin. Bunu aşağıdaki şekilde yapılandırın.

    ![Unreal Düzenleyicisi, Acoustics bileşeni kullanımını boş bir aktör içinde gösterir](media/acoustics-component-usage.png)

       
    <sup>1</sup> aktöre Acoustics ses bileşeni ekleyin. Bu bileşen, Wwise ses bileşenine Project Acoustics işlevselliği ekler.
        
    <sup>2</sup> **Başlat kutusunda çal** kutusu varsayılan olarak seçilidir. Bu ayar, düzey başlangıcında ilişkili bir Wwise olayını tetikler.</li>
         
    <sup>3</sup> kaynak hakkında ekran hata ayıklama bilgilerini yazdırmak Için **Acoustics parametrelerini göster** onay kutusunu kullanın.

    ![Hata ayıklama değerleri etkin olan ses kaynağında gerçek olmayan düzenleyici Acoustics paneli](media/debug-values.png)

    <sup>4</sup> her zamanki Wwise iş akışı başına bir Wwise olayı atayın.
       
    <sup>5</sup> **uzamsal ses kullan** özelliğinin kapalı olduğundan emin olun. Belirli bir ses bileşeni için Project Acoustics kullanıyorsanız, Acoustics için Wwise 'ın uzamsal ses altyapısını aynı anda kullanamazsınız.</li>
       
Hazırsınız. Sahnenin etrafında ilerleyin ve akustik etkileri keşfedebilirsiniz!

## <a name="next-steps"></a>Sonraki adımlar
* [Projeyi Acoustics Unreal/Wwise tasarım öğreticisini](unreal-workflow.md)deneyin.
* Oyun sahneleri için [nasıl bir bakışta](unreal-baking.md) bilgi alabileceğinizi öğrenin.
