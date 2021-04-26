---
title: İstemci tarafı performans izlemeleri oluşturma
description: WPF kullanarak istemci tarafı performans profili oluşturma için en iyi yöntemler
author: florianborn71
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: 1d4ce68bdda5fbc3dfdb7396141289a58dab5bd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "92204104"
---
# <a name="create-client-side-performance-traces"></a>İstemci tarafı performans izlemeleri oluşturma

Azure uzaktan Işleme performansının istendiği kadar iyi olmasının pek çok nedeni vardır. Bulut sunucusunda saf işleme performansının dışında, özellikle ağ bağlantısının kalitesi deneyim üzerinde önemli bir etkiye sahiptir. Sunucunun performansını profili eklemek için, bölüm [sunucu tarafı performans sorguları](../overview/features/performance-queries.md)bölümüne bakın.

Bu bölümde, ile ilgili olası istemci tarafı performans sorunlarını belirleme konusu ele alınmaktadır *:::no-loc text="performance traces":::* .

## <a name="getting-started"></a>Kullanmaya başlama

Windows :::no-loc text="performance tracing"::: işlevselliğine yeni başladıysanız, bu bölüm en temel hüküm ve uygulamalardan yararlanacaktır.

### <a name="installation"></a>Yükleme

ARR ile izleme yapmak için kullanılan uygulamalar, tüm Windows geliştirme için kullanılabilen genel amaçlı araçlardır. [Windows performans araç seti](/windows-hardware/test/wpt/)aracılığıyla sağlanırlar. Bu araç setini almak için [Windows değerlendirme ve dağıtım seti](/windows-hardware/get-started/adk-install)'ni indirin.

### <a name="terminology"></a>Terminoloji

Performans izlemeleriyle ilgili bilgi ararken, bir dizi terim boyunca kaçınılmaz. En önemli olanlar şunlardır:

* `ETW`
* `ETL`
* `WPR`
* `WPA`

**ETW** , [ **W** Windows için bir **E-pul** kasasının](/windows/win32/etw/about-event-tracing)temsil eder. Bu, Windows 'da yerleşik olarak bulunan etkili çekirdek düzeyi izleme özelliği için çok daha fazla bir addır. Bu, ETW 'yi destekleyen uygulamalar, performans sorunlarını izlemenize yardımcı olabilecek günlük eylemlerine olay yayabileceğinden *olay* izleme olarak adlandırılır. Varsayılan olarak, işletim sistemi, disk erişimleri, görev geçişleri gibi şeyler için olayları zaten yayar. ARR gibi uygulamalar, bırakılan çerçeveler, ağ gecikmesi vb. gibi özel olayları da yayar.

**ETL** , **E-pul** yarış/ **Çıkış oranını** temsil eder.  Yalnızca bir izlemenin toplanması (günlüğe kaydedilmiş) ve bu nedenle genellikle izleme verilerini depolayan dosyalar için dosya uzantısı olarak kullanıldığı anlamına gelir. Bu nedenle, bir izleme yaptığınızda genellikle bir \* . ETL dosyanız olur.

**WPR** , [ **W** Windows **P** erformans **R** ecorder](/windows-hardware/test/wpt/windows-performance-recorder) için temsil eder ve olay izlemelerinin kaydını başlatan ve durduran uygulamanın adıdır. WPR \* , günlüğe kaydedilecek tam olayları yapılandıran bir profil dosyası (. wprp) alır. Bu tür bir `wprp` Dosya ARR SDK ile birlikte sağlanır. Bir masaüstü BILGISAYAR üzerinde izleme yaparken, WPR 'yi doğrudan başlatabilirsiniz. HoloLens üzerinde bir izleme yaparken, genellikle bunun yerine web arabiriminden ilerleirsiniz.

**WPA** , [ **W** Windows **P** erformans **A** nalyzer](/windows-hardware/test/wpt/windows-performance-analyzer) IÇIN temsil eder ve. etl dosyalarını açmak için kullanılan GUI uygulamasının adı ve \* performans sorunlarını belirlemek için verileri veri aracılığıyla kullanır. WPA, verileri çeşitli ölçütlere göre sıralamanıza, verileri çeşitli yollarla görüntülemenize, ayrıntıları daha sonra görmenizi ve bilgileri ilişkilendirmenize olanak tanır.

ETL izlemeleri herhangi bir Windows cihazında (yerel bılgısayar, HoloLens, bulut sunucusu vb.) oluşturulalese de, genellikle diske kaydedilir ve bir masaüstü BILGISAYAR üzerinde WPA ile çözümlenir. ETL dosyaları, bu geliştiricilerin bir görünüme sahip olması için diğer geliştiricilere gönderilebilir. Dosya yolları ve IP adresleri gibi hassas bilgilerin ETL izlemelerinde yakalanabileceğini unutmayın. ETW 'yi iki şekilde kullanabilirsiniz: izlemeleri kaydetmek veya izlemeleri çözümlemek için. İzleme kaydı, düzenli ve en az kurulum gerektirir. Diğer yandan izlemeleri analiz etmek, hem WPA aracının hem de araştırdığınız sorunun anlaşılmasına gerek duyar. Öğrenimi WPA için genel malzeme aşağıda verilmiştir ve ayrıca, ARR 'e özgü izlemelerin nasıl yorumlanacağı konusunda yönergeler sağlanır.

## <a name="recording-a-trace-on-a-local-pc"></a>Yerel bir bılgısayarda izleme kaydetme

ARR performans sorunlarını belirlemek için, gerçek performans özelliklerinin anlık görüntüsünü almanın tek yolu olduğundan, bir HoloLens üzerinde doğrudan bir izleme yapmayı tercih etmelisiniz. Ancak, HoloLens performans kısıtlamaları olmadan bir izleme yapmak istiyorsanız veya yalnızca WPA 'Yı kullanmayı ve gerçekçi bir izlemeye ihtiyaç duymasını öğrenmek istiyorsanız, bunun nasıl yapılacağını burada bulabilirsiniz.

### <a name="wpr-configuration"></a>WPR yapılandırması

1. [:::no-loc text="Windows Performance Recorder":::](/windows-hardware/test/wpt/windows-performance-recorder) *Başlat menüsünden* öğesini başlatın.
1. **Diğer seçenekleri** Genişlet
1. **Profil ekle 'ye tıklayın...**
1. *Azureremoterenderingnetworkprofil oluşturma. wprp* dosyasını seçin. Bu dosyayı, *Araçlar/ETLProfiles* altındaki ARR SDK 'sında bulabilirsiniz.
   Profil artık *özel ölçümler* altında WPR 'de listelenecektir. Etkin olan tek profil olduğundan emin olun.
1. *İlk düzey önceliklendirme*'i Genişlet:
    * Tek yapmak istiyorsanız, ARR ağ olaylarının hızlı bir izlemesini yakalayın, bu seçeneği **devre dışı bırakın** .
    * ARR ağ olaylarını CPU veya bellek kullanımı gibi diğer sistem özellikleriyle ilişkilendirmenize gerek duyuyorsanız, bu seçeneği **etkinleştirin** .
    * Bu seçeneği etkinleştirirseniz, izlemenin büyük olasılıkla boyutu büyük olasılıkla çok gigabayt olur ve WPA 'da kaydedilmesi ve açılması uzun zaman alır.

Daha sonra WPR yapılandırmanızın şöyle görünmesi gerekir:

![WPR yapılandırması](./media/wpr.png)

### <a name="recording"></a>Kayıt

İzlemeyi kaydetmeye başlamak için **Başlat** ' a tıklayın. Kaydı dilediğiniz zaman başlatabilir ve durdurabilirsiniz. Bunu yapmadan önce uygulamanızı kapatmanız gerekmez. Aynı şekilde, ETW 'nin tüm sistem için her zaman bir izleme kaydedebileceği için, hangi uygulamanın izlendiğinizi belirtmeniz gerekmez. `wprp`Dosya, kaydedilecek olay türlerini belirtir.

Kaydı durdurmak için **Kaydet** ' e tıklayın ve etl dosyasının depolanacağı yeri belirtin.

Artık doğrudan WPA 'da açabileceğiniz ya da başka birine gönderebileceğiniz bir ETL dosyanız var.

## <a name="recording-a-trace-on-a-hololens"></a>HoloLens üzerinde izleme kaydetme

HoloLens üzerinde bir izleme kaydetmek için cihazınızı önyükleyin ve *cihaz portalını* açmak için BIR tarayıcıya IP adresini girin.

![Cihaz portalı](./media/wpr-hl.png)

1. Sol tarafta *performans > performansı izleme*' ye gidin.
1. **Özel profiller** seçin
1. Tıklat **:::no-loc text="Browse...":::**
1. *Azureremoterenderingnetworkprofil oluşturma. wprp* dosyasını seçin. Bu dosyayı, *Araçlar/ETLProfiles* altındaki ARR SDK 'sında bulabilirsiniz.
1. **Izlemeyi Başlat** 'a tıklayın
1. HoloLens şimdi bir izleme kaydediyor. Araştırmak istediğiniz performans sorunlarını tetiklediğinizden emin olun. Sonra **Izlemeyi durdur**' a tıklayın.
1. İzleme, Web sayfasının alt kısmında listelenecektir. ETL dosyasını indirmek için sağ taraftaki disk simgesine tıklayın.

Artık doğrudan WPA 'da açabileceğiniz ya da başka birine gönderebileceğiniz bir ETL dosyanız var.

## <a name="analyzing-traces-with-wpa"></a>WPA ile izlemeleri çözümleme

### <a name="wpa-basics"></a>WPA temelleri

Windows Performans Çözümleyici, ETL dosyalarını açmak ve izlemeleri denetlemek için standart bir araçtır. Bu makalede WPA 'nın nasıl çalıştığı hakkında bir açıklama. Başlamak için şu kaynaklara göz atın:

* İlk genel bakış için [Tanıtım videolarını](/windows-hardware/test/wpt/windows-performance-analyzer) izleyin.
* WPA 'nın, yaygın adımları açıklayan bir *Başlangıç* sekmesi vardır. Mevcut konulara göz atın. Özellikle "verileri görüntüle" bölümünde, belirli veriler için grafik oluşturma hakkında hızlı bir giriş alırsınız.
* [Bu web sitesi hakkında](https://randomascii.wordpress.com/2015/09/24/etw-central/)harika bilgiler vardır ancak bunların hepsi yeni başlayanlar için uygun değildir.

### <a name="graphing-data"></a>Verileri grafikleme

ARR izlemeyi kullanmaya başlamak için aşağıdaki parçaların öğrenmesi iyi.

![Performans grafiği](./media/wpa-graph.png)

Yukarıdaki görüntüde, bir izleme verileri tablosu ve aynı verilerin grafik temsili gösterilmektedir.

Alttaki tabloda sarı (altın) çubuğun ve mavi çubuğun olduğunu aklınızda edin. Bu çubukları sürükleyip istediğiniz konuma yerleştirebilirsiniz.

**Sarı çubuğun solundaki tüm sütunlar** **anahtarlar** olarak yorumlanır. Anahtarlar, ağacı üst sol pencerede yapılandırmak için kullanılır. Burada, "sağlayıcı adı" ve "görev adı" olmak üzere iki *anahtar* sütunu var. Sonuç olarak sol üst penceredeki ağaç yapısı iki düzeyden daha derin. Sütunları yeniden sıralar veya anahtar alanından sütun ekleyip kaldırırsanız, ağaç görünümündeki yapı değişir.

**Mavi çubuğun sağındaki sütunlar** , sağ üst penceredeki **grafik görüntüsü** için kullanılır. Çoğu zaman yalnızca ilk sütun kullanılır, ancak bazı grafik modları birden çok veri sütunu gerektirir. Çizgi grafiklerin çalışması için söz konusu sütundaki *toplama modunun* ayarlanması gerekir. ' AVG ' veya ' Max ' kullanın. Toplama modu, bir piksel birden çok olaya sahip bir aralığı kapsıyorsa, belirtilen pikselde grafiğin değerini belirlemede kullanılır. Bu, toplama ' Sum ' olarak ayarlanarak ve sonra yakınlaştırılırken ve uzaklaştırarak gözlemlenebilir.

Ortadaki sütunlarda özel bir anlamı yoktur.

![Olaylar görünümü](./media/wpa-event-view.png)

*Genel olaylar görünümü Düzenleyicisi* 'nde, görüntülenecek tüm sütunları, toplama modunu, sıralamayı ve hangi sütunların anahtar veya grafik olarak kullanıldığını yapılandırabilirsiniz. Yukarıdaki örnekte, **alan 2** etkindir ve 3-6 alanı devre dışı bırakılır. Alan 2 genellikle ETW olayının ilk *özel veri* alanıdır ve bu nedenle, bazı ağ gecikmesi değerlerini temsil eden ARR "Framestatistik" olayları için. Bu olayın daha fazla değerini görmek için diğer "alan" sütunlarını etkinleştirin.

### <a name="presets"></a>'Nı

Bir izlemeyi doğru bir şekilde analiz etmek için kendi iş akışınızı ve tercih edilen veri görüntülemeyi belirlemeniz gerekir. Bununla birlikte, ARR 'e özgü olaylara hızlı bir genel bakış alabilmek için, Windows yazılım koruma platformu profili ve ön ayar dosyalarını klasör *araçları/ETLProfiles* klasörüne dahil ediyoruz. Tam bir profili yüklemek için, > profiller ' i seçin *..* . WPA menü çubuğundan veya *ön ayarlarım* panelini açın (*pencere Önayarlarımı >* ve *içeri aktar*' ı seçin. İlki, aşağıdaki görüntüde olduğu gibi tamamen bir WPA yapılandırması ayarlayacaktır. İkincisi yalnızca çeşitli görünüm yapılandırmalarının ön ayarlarını yapar ve belirli bir ARR olay verisi parçasına bakmak için hızlı bir şekilde bir görünüm açmanıza olanak tanır.

!['Nı](./media/wpa-arr-trace.png)

Yukarıdaki görüntüde, çeşitli ARR 'ya özgü olayların görünümleri ve genel CPU kullanımının görünümü gösterilmektedir.

## <a name="next-steps"></a>Sonraki adımlar

* [Sunucu tarafı performans sorguları](../overview/features/performance-queries.md)