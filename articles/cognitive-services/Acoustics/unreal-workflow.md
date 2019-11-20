---
title: Project Acoustics Unreal tasarım öğreticisi
titlesuffix: Azure Cognitive Services
description: Bu öğretici proje Acoustics için tasarım iş akışını gerçek zamanlı ve Wwise 'ta açıklar.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 817a11171c5b4b4ef205e5fbb04f9b6d6d85b248
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854257"
---
# <a name="project-acoustics-unrealwwise-design-tutorial"></a>Proje Acoustics Unreal/Wwise tasarım öğreticisi
Bu öğreticide, proje Acoustics için gerçek zamanlı ve Wwise 'da tasarım kurulumu ve iş akışı açıklanmaktadır.

Yazılım önkoşulları:
* Proje Acoustics Wwise ve Unreal eklentileri olan gerçek olmayan bir proje

Project Acoustics ile gerçek olmayan bir proje almak için şunları yapabilirsiniz:
* Proje Acoustics 'i gerçek olmayan projenize eklemek için [Acoustics Unreal Integration](unreal-integration.md) yönergelerini izleyin
* Ya da [Project Acoustics örnek projesini](unreal-quickstart.md)kullanın.

## <a name="setup-project-wide-wwise-properties"></a>Proje genelindeki Wwise özelliklerini ayarlama
Wwise, proje Acoustics eklentisinin Wwise ses DSP 'sini nasıl kullandığını etkileyen küresel düşüşe ve occlusiyon eğrilerine sahiptir.

### <a name="design-wwise-occlusion-curves"></a>Wwise occlusiyon eğrileri tasarlama
Project Acoustics etkin olduğunda, Wwise 'ta ayarladığınız occlusiyon birimine, düşük geçiren filtreye (LPF) ve yüksek geçiren filtre (HPF) eğrilerine yanıt verir. 100 sayısının Occlusiyon değeri için birim eğrisi türünü-100 dB değeriyle doğrusal olarak ayarlamayı öneririz.

Bu ayar ile, Project Acoustics simülasyonu-18 dB bir occluson değerini hesapladıysa, X = 18 ' de aşağıdaki eğriye giriş yapılır ve ilgili Y değeri, uygulanan zayıflalama. Yarı occlusıto yapmak için,-100 dB yerine-50 dB veya-200 dB 'yi bir occlusıda olarak ayarlayın. Oyununuza en uygun olan herhangi bir eğriyi uyarlayabilirsiniz ve ince ayar yapabilirsiniz.
 
![Wwise occlusiyon eğrisi Düzenleyicisi ekran görüntüsü](media/wwise-occlusion-curve.png)

### <a name="disable-wwise-obstruction-curves"></a>Wwise düşüşe eğrileri devre dışı bırak
Wwise düşüşe eğrileri, kurutma düzeyini yalıtımlı olarak etkiler ancak Project Acoustics, Wet/kuru oranlarını zorlamak için tasarım denetimleri ve simülasyonu kullanır. Düşüşe birim eğrisinin devre dışı bırakılmasını öneririz. Wetliği tasarlamak için, daha sonra açıklanan Wetlik ayarlama denetimini kullanın.
 
Başka amaçlar için düşüşe LPF/HPF eğrileri kullanıyorsanız, bunları X = 0 konumunda Y = 0 olarak ayarlamış olduğunuzdan emin olun (yani, düşüşe olmadığında bir LPF veya HPF yoktur).

![Wwise düşüşe eğrisi Düzenleyicisi ekran görüntüsü](media/wwise-obstruction-curve.png)

### <a name="design-project-acoustics-mixer-parameters"></a>Tasarım Projesi Acoustics karıştırıcı parametreleri
Project Acoustics Bus 'ın karıştırıcı eklenti sekmesini ziyaret ederek küresel yankı özelliklerini denetleyebilirsiniz. "Project Acoustics Mixer (Custom)" üzerine çift tıklayarak karıştırıcı eklentisinin ayarlar panelini açın.

Ayrıca, karıştırıcı eklentisinin bir "Istenmeyen kullanım yapma" seçeneği olduğunu da görebilirsiniz. Proje akustik yerleşik istenmeyen kullanım kullanımını tercih ediyorsanız, "Istenmeyen kullanım yapma" onay kutusunu işaretleyin ve HRTF veya kaydırma seçeneklerinden birini belirleyin. Ayarlamış olduğunuz tüm kurutma yedek zamanlarını devre dışı bıraktığınızdan emin olun, aksi takdirde doğrudan yolu iki kez duyarsınız. Yankı karışımı üzerinde genel denetim sağlamak için "Wetlık ayarı" ve "yankı zaman ölçeği faktörünü" kullanın. Göz önünde olmayan bir şekilde yeniden başlatmanız ve ardından ' gereksiz kullanım yapma ' onay kutusu gibi karıştırıcı eklenti yapılandırma değişikliklerini seçmek için Play 'e geçmeden önce soundbankalarını yeniden oluşturmanız gerekir.

![Project Acoustics Wwise Mixer eklenti seçeneklerinin ekran görüntüsü](media/mixer-plugin-global-settings.png)

## <a name="set-project-acoustics-design-controls-in-the-wwise-actor-mixer-hierarchy"></a>Wwise aktör karıştırıcı hiyerarşisinde Project Acoustics tasarım denetimlerini ayarlama
Tek bir aktör karıştırıcı parametrelerini denetlemek için aktör-mixer ' e çift tıklayın ve ardından karıştırıcı eklenti sekmesine tıklayın. Burada, her türlü parametreyi ses başına düzeyinde değiştirebilirsiniz. Bu değerler, gerçek olmayan taraftan (aşağıda açıklanmıştır) ayarlanmış olanlar ile birleştirilir. Örneğin, Project Acoustics Unreal eklentisi, Outdoorness düzeltmesini bir nesne üzerinde 0,5 olarak ayarlıyor ve Wwise onu-0,25 olarak ayarlarsa, bu sese uygulanan Outdoorness ayarlaması 0,25.

![Wwise aktör-karıştırıcı hiyerarşisinde ses karıştırıcı ayarlarına göre ekran görüntüsü](media/per-sound-mixer-settings.png)

### <a name="ensure-the-aux-bus-has-dry-send-and-output-bus-has-wet-send"></a>Aux veri yolunun kuruma gönderilmesini ve çıkış veri yolu 'nın gönderilmesini sağlayın
Gerekli aktör karıştırıcı kurulumunun her zamanki kuru ve ıslak yönlendirmeyi Wwise 'a yönlendireceğini unutmayın. Aktör karıştırıcı 'nın çıkış veri yolu (Project Acoustics Bus olarak ayarlanır) ve Kullanıcı tanımlı Aux veri yolu üzerinde sinyal sinyali üzerine yankı sinyali üretir. Bu yönlendirme, proje Acoustics Wwise eklentisinin kullandığı Wwise Mixer eklenti API 'sinin özellikleri nedeniyle gereklidir.

![Proje Acoustics için ses tasarımı yönergelerini gösteren Wwise Düzenleyicisi ekran görüntüsü](media/voice-design-guidelines.png)
 
### <a name="set-up-distance-attenuation-curves"></a>Uzaklık zayıflalama eğrileri ayarlama
Project Acoustics kullanan aktör-mixers tarafından kullanılan herhangi bir zayıflalama eğrisinin, Kullanıcı tanımlı yedek gönderme kümesine "çıkış veri yolu birimi" olarak ayarlanmış olduğundan emin olun. Wwise, yeni oluşturulan zayıflalama eğrileri için varsayılan olarak bunu yapar. Mevcut bir projeyi geçiriyorsanız, eğri ayarlarınızı kontrol edin.

Varsayılan olarak, Project Acoustics simülasyonu, oynatıcı konumu etrafında 45 ölçü içerir. Genellikle, zayıflalama eğayarınızı bu mesafede-200 dB 'ye ayarlamayı öneririz. Bu uzaklık sabit bir kısıtlama değildir. Silaapons gibi bazı seslerde daha büyük bir yarıçap istemeniz gerekebilir. Bu gibi durumlarda, desteklenmediği uyarısıyla yalnızca oyuncu konumunun 45 Oyuncu bir odadayken ve bir ses kaynağı odanın dışındaysa ve 100 ' den uzakta ise, düzgün bir şekilde eklenebilir olur. Kaynak bir odadaydaysanız ve oyuncu dışarıda ve 100 ise, düzgün bir şekilde, uygun değildir.

![Wwise zayıflalama eğrilerinin ekran görüntüsü](media/atten-curve.png)

### <a name="post-mixer-equalization"></a>Gönderi karıştırıcı ile eşitleme ###
 Yapmak isteyebileceğiniz bir başka şey de bir gönderi karıştırıcı Equalizer 'ı eklemektir. Project Acoustics Bus 'ı tipik bir reverb veri yolu (varsayılan ters yankı modunda) olarak kabul edebilir ve buna eşitleme yapmak için bir filtre koyabilirsiniz. Project Acoustics Wwise örnek projesinde bunun bir örneğini görebilirsiniz.

![Wwise karıştırıcı sonrası EQ ekran görüntüsü](media/wwise-post-mixer-eq.png)

Örneğin, yüksek bir pass filtresi, en yakın alan kayıtlarından bası, gerçekçi yankı veren bir şekilde işlemeye yardımcı olabilir. Ayrıca, EQ ' i Rtpc 'Ler aracılığıyla ayarlayarak daha fazla göndere denetim elde edebilirsiniz ve bu da oyun sırasında Reverb rengini değiştirebilirsiniz.

## <a name="set-up-scene-wide-project-acoustics-properties"></a>Sahne-Wide Project Acoustics özelliklerini ayarlama

Acoustics Space aktör, sistemin davranışını değiştiren ve hata ayıklama sırasında yararlı olan birçok denetimi kullanıma sunar.

![Unreal Acoustics Space denetimlerinin ekran görüntüsü](media/acoustics-space-controls.png)

* **Acoustics verileri:** Bu alana Content/Acoustics dizininden bakmış bir Acoustics varlığı atanmalıdır. Project Acoustics eklentisi, Content/Acoustics dizinini projenizin paketlenmiş dizinlerine otomatik olarak ekler.
* **Döşeme boyutu:** Acoustics verilerinin RAM 'e yüklenmesini istediğiniz dinleyicinin etrafındaki bölgenin kapsamları. Player 'ın hemen yanındaki dinleyici Araştırmaları ' de yüklendiği sürece, sonuçlar tüm yoklamalara yönelik akustik verileri yüklerken de aynıdır. Daha büyük kutucuklar daha fazla RAM kullanır, ancak disk g/ç 'yi azaltır
* **Otomatik akış:** Etkinleştirildiğinde, dinleyici yüklenen bölgenin kenarına ulaştığında yeni kutucuklara otomatik olarak yüklenir. Devre dışı bırakıldığında, kod veya planlar aracılığıyla yeni kutucukları el ile yüklemeniz gerekir
* **Önbellek ölçeği:** akustik sorgular için kullanılan önbelleğin boyutunu denetler. Daha küçük bir önbellek daha az RAM kullanır, ancak her bir sorgu için CPU kullanımını artırabilir.
* **Acoustics etkin:** Acoustics simülasyonu için hızlı A/B 'yi değiştirmeye olanak sağlayan bir hata ayıklama denetimi. Bu denetim, gönderim yapılandırmalarında yok sayılır. Denetim, belirli bir ses hatasının Acoustics hesaplamalar veya Wwise projesindeki başka bir sorun olup olmadığını bulmak için yararlıdır.
* **Uzaklıkları güncelleştir:** Uzaklık sorguları için önceden bakmış Acoustics bilgilerini kullanmak istiyorsanız bu seçeneği kullanın. Bu sorgular, ışın yayınlarına benzerdir, ancak önceden hesaplandıktan sonra çok daha az CPU sürer. Örnek kullanım, dinleyiciye en yakın yüzeyden farklı yansımalar için kullanılır. Bundan tamamen yararlanmak için, uzaklıkları sorgulamak için kod veya şemaları kullanmanız gerekir.
* **Istatistikleri çiz:** UE, size `stat Acoustics` CPU bilgileri sağlayabilirken, bu durum görüntüsü şu anda yüklü olan ACE dosyasını, RAM kullanımını ve ekranın sol üst kısmındaki diğer durum bilgilerini gösterir.
* **Voxler çiz:** Çalışma zamanı ilişkilendirme sırasında kullanılan Voxel kılavuzunu gösteren dinleyiciye yakın şekilde yapışır. Bir verici çalışma zamanının Voxel içindeyse, akustik sorgular başarısız olur.
* **Beraberlik Araştırmaları:** Bu sahnenin tüm araştırmalarını göster. Bunlar, yükleme durumlarına bağlı olarak farklı renklerdir.
* **Uzaklıkları çiz:** Uzaklıkları Güncelleştir etkinse bu, dinleyiciye yönelik olarak en yakın yüzeyde bir kutu gösterir.

## <a name="actor-specific-acoustics-design-controls"></a>Aktör 'e özgü Acoustics tasarım denetimleri
Bu tasarım denetimleri, gerçek olmayan bir ses bileşeninin kapsamına alınır.

![Gerçek olmayan ses bileşen denetimlerinin ekran görüntüsü](media/audio-component-controls.png)

* **Occlusiyon çarpanı:** , Occluson efektini denetler. > 1 değerleri, occlusion 'ı artıracaktır. < 1 değerleri en aza indirir.
* **Wetlik ayarı:** Ek yankı dB
* **Decay zaman çarpanı:** Acoustics simülasyonuna göre RT60 çeşitdüzeyini denetler
* **Outdoorness ayarlaması:** Ters kapıların ne kadar olduğunu denetler. 0 ' a yakın değerler daha fazla dışarıda, 1 ' den daha fazla dışarıda bırakılamaz. Bu ayarlama eklenebilir, bu nedenle-1 olarak ayarlandığında-1 olarak ayarlanması, dışarıda kapıların uygulanmasını zorlayacaktır.
* **İletim DB:** Bu ses düzeyi ile ek bir ses ile, bu ses düzeyi tabanlı uzaklık zayıflalama ile birlikte daha fazla.
* **Wet oranı uzaklık warp:** Doğrudan yolu etkilemeden, kaynaktaki geri alma özelliklerini daha yakın veya daha fazla gibi ayarlar.
* **Başlangıç 'ta Çal:** Sesin sahne başlatması üzerinde otomatik olarak çalıp çalınmayacağını belirtmek için değiştirin. Varsayılan olarak etkindir.
* **Akustik parametreleri göster:** Hata ayıklama bilgilerini doğrudan bileşen oyunun üzerine görüntüleyin. (yalnızca gönderi olmayan yapılandırmalarda)

## <a name="blueprint-functionality"></a>Blueprint işlevi
Acoustics alanı aktörine, bir harita yükleme veya düzey komut dosyası aracılığıyla ayarları değiştirme gibi işlevler sağlayan Blueprint aracılığıyla erişilebilir. Burada iki örnek sağlıyoruz.

### <a name="add-finer-grained-control-over-streaming-load"></a>Akış yükü üzerine daha ayrıntılı denetim ekleme
Oynatıcı konumuna göre otomatik olarak akış yerine akustik veri akışını yönetmek için, yük kutucuğu şemasını zorla şema işlevini kullanabilirsiniz:

![Gerçek zamanlı olmayan Blueprint akış seçeneklerinin ekran görüntüsü](media/blueprint-streaming.png)

* **Hedef** AcousticsSpace aktör
* **Orta konum:** Veri yüklenmesi gereken bölgenin merkezi
* **Araştırmaları kutucuk dışına kaldır:** İşaretliyse, yeni bölgede yer alan tüm yoklamalar RAM 'den kaldırılır. İşaretlenmezse, yeni bölge belleğe yüklenir ve var olan araştırmaların belleğe yüklenmiş olarak bırakılması gerekir
* **Tamamlanmayı engelle:** Kutucuğun zaman uyumlu bir işlem yüklemesini sağlar

Zorla yükleme kutucuğu çağrılmadan önce kutucuk boyutu ayarlanmış olmalıdır. Örneğin, bir ACE dosyası yüklemek, kutucuk boyutunuzu ve Stream 'i bir bölgede ayarlamak için şuna benzer bir şey yapabilirsiniz:

![Gerçek olmayan akış Kurulum seçeneklerinin ekran görüntüsü](media/streaming-setup.png)

Bu örnekte kullanılan Load Acoustics Data şema işlevi aşağıdaki parametrelere sahiptir:

* **Hedef** AcousticsSpace aktör.
* **Yeni Bake:** Yüklenecek Acoustics veri varlığı. Bu boş bırakın/null olarak ayarlandığında, yeni bir yükleme yapmadan geçerli fırt kaldırılır.

### <a name="optionally-query-for-surface-proximity"></a>Yüzey yakınlığı için isteğe bağlı sorgu
Kapatma yüzeylerinin dinleyici etrafında belirli bir yönde nasıl olduğunu görmek isterseniz, sorgu uzaklığı işlevini kullanabilirsiniz. Bu işlev, yönlü geciktirilen yansımaları veya yüzey yakınlığı tarafından yönetilen diğer oyun mantığını yönlendiren yararlı olabilir. Sonuçlar Acoustics arama tablosundan çekilmekte olduğundan sorgu bir Ray cast 'dan daha ucuz.

![Şema uzaklığı sorgusunun örnek görüntüsü](media/distance-query.png)

* **Hedef** AcousticsSpace aktör
* **Yönü ara:** Dinleyiciye ortalanmış olan sorgu yönü
* **Uzaklık** Sorgu başarılı olursa en yakın yüzey arasındaki mesafe
* **Dönüş değeri:** Boolean-sorgu başarılı olursa true, aksi takdirde false

## <a name="next-steps"></a>Sonraki adımlar
* [Tasarım sürecinin](design-process.md) arkasındaki kavramları keşfet
* Kendi sahnüsizin için [bir Azure hesabı oluşturun](create-azure-account.md)


