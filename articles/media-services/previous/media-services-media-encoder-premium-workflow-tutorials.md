---
title: Gelişmiş Media Encoder Premium Workflow öğreticileri
description: Bu belge, Media Encoder Premium Workflow ile gelişmiş görevlerin nasıl gerçekleştirileceğini ve ayrıca İş Akışı Tasarımcısı ile karmaşık iş akışlarının nasıl oluşturulacağını gösteren izlenecek yollar içerir.
services: media-services
documentationcenter: ''
author: xstof
manager: femila
editor: ''
ms.assetid: 1ba52865-b4a8-4ca0-ac96-920d55b9d15b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: christoc
ms.reviewer: xpouyat; juliako
ms.openlocfilehash: 1ab70d56bd3def58d0e814035070cf027a88cd3d
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/07/2019
ms.locfileid: "69016723"
---
# <a name="advanced-media-encoder-premium-workflow-tutorials"></a>Gelişmiş Media Encoder Premium Workflow öğreticileri
## <a name="overview"></a>Genel Bakış
Bu belge, **iş akışı Tasarımcısı**iş akışlarının nasıl özelleştirileceğini gösteren izlenecek yollar içerir. Gerçek iş akışı dosyalarını [buradan](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples)bulabilirsiniz.  

## <a name="toc"></a>TABLOSUNA
Aşağıdaki konular ele alınmıştır:

* [MXF 'yi tek bir bit hızı MP4 olarak kodlama](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
  * [Yeni bir iş akışı başlatılıyor](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new)
  * [Medya dosyası girişini kullanma](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
  * [Medya akışlarını İnceleme](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
  * [İçin bir video Kodlayıcısı ekleniyor. MP4 dosyası oluşturma](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
  * [Ses akışını kodlama](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
  * [Bir MP4 kapsayıcısına ses ve video akışlarını çoğullama](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
  * [MP4 dosyası yazılıyor](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
  * [Çıkış dosyasından Media Services varlık oluşturma](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
  * [Tamamlanmış iş akışını yerel olarak test etme](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
* [MXF 'yi MultiBit hızında MP4 'leri-dinamik paketleme etkin olarak kodlama](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
  * [Bir veya daha fazla ek MP4 çıkışı ekleme](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
  * [Dosya çıkış adlarını yapılandırma](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
  * [Ayrı bir ses Izi ekleme](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
  * ["ISM" SMıL dosyasını ekleme](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
* [MXF 'yi çoklu bit hızına sahip MP4 ile kodlama-Gelişmiş şema](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
  * İyileştirmek için iş akışına genel bakış
  * [Dosya adlandırma kuralları](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
  * [Bileşen özellikleri iş akışı köküne yayımlanıyor](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
  * [Yayımlanmış özellik değerlerini kullanan çıkış dosyası adlarını oluşturmuş olmanız](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
* [Çoklu bit hızı MP4 çıkışına küçük resim ekleme](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
  * Küçük resimleri eklemek için iş akışına genel bakış
  * [JPG kodlaması ekleniyor](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
  * [Renk alanı dönüştürmesiyle ilgili](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
  * [Küçük resimleri yazma](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
  * [Bir iş akışındaki hataları algılama](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
  * [Tamamlanan Iş akışı](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
* [Çoklu bit hızı MP4 çıkışının zaman tabanlı bölünmesi](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
  * [Kırpma eklemeye başlamak için iş akışına genel bakış](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
  * [Akış kırpıcıyı kullanma](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
  * [Tamamlanan Iş akışı](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
* [Komut dosyalı bileşene giriş](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
  * [Bir iş akışı içinde betik oluşturma: Merhaba Dünya](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
* [Çoklu bit hızı MP4 çıkışının çerçeve tabanlı bölünmesi](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
  * [Şemayı eklemeye başlamak için Blueprint genel bakışı](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
  * [Klip Listesi XML 'i kullanma](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
  * [Betikleştirilmiş bir bileşenden klip listesini değiştirme](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
  * [Clienppingenabled kolay özelliği ekleme](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

## <a id="MXF_to_MP4"></a>MXF 'yi tek bir bit hızı MP4 olarak kodlama
Bu bölümde, tek bit hızı oluşturma gösterilmektedir. AAC dosyası AAC ile kodlanmış ses olan MP4 dosyası. MXF giriş dosyası.

### <a id="MXF_to_MP4_start_new"></a>Yeni bir iş akışı başlatılıyor
İş Akışı Tasarımcısı açın ve Dosya > yeni çalışma alanı > transcode Blueprint ' i seçin

Yeni iş akışı üç öğe gösterir:

* Birincil kaynak dosyası
* Klip Listesi XML 'i
* Çıkış dosyası/varlık  

![Yeni kodlama Iş akışı](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

*Yeni kodlama Iş akışı*

### <a id="MXF_to_MP4_with_file_input"></a>Medya dosyası girişini kullanma
Giriş medya dosyasını kabul etmek için, bir medya dosyası giriş bileşeni eklemekle başlar. İş akışına bir bileşen eklemek için depo arama kutusunda bunu bulun ve istenen girişi tasarımcı bölmesine sürükleyin. Medya dosyası girişi için eylemi tekrarlayın ve birincil kaynak dosya bileşenini medya dosyası girişinden dosya adı giriş iğnesine bağlayın.

![Bağlı medya dosyası girişi](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

*Bağlı medya dosyası girişi*

Başlangıçta, özel bir iş akışı tasarlarken kullanılacak uygun bir örnek dosya olduğunu belirler. Bunu yapmak için tasarımcı bölmesi arka planına tıklayın ve sağ Özellik bölmesinde birincil kaynak dosyası özelliğini arayın. Klasör simgesine tıklayın ve iş akışını test etmek için istenen dosyayı seçin. Medya dosyası giriş bileşeni, dosyayı inceler ve çıktı iğnelerinin incelenen örnek dosyanın ayrıntılarını yansıtacak şekilde doldurur.

![Doldurulmuş medya dosyası girişi](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

*Doldurulmuş medya dosyası girişi*

Artık giriş doldurulduğundan, sonraki adım çıkış kodlama ayarlarını ayarlamaya yönelik olur. Birincil kaynak dosyanın yapılandırılmasına benzer şekilde, şimdi yalnızca altındaki çıkış klasörü değişken özelliğini yapılandırın.

![Yapılandırılmış giriş ve çıkış özellikleri](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

*Yapılandırılmış giriş ve çıkış özellikleri*

### <a id="MXF_to_MP4_streams"></a>Medya akışlarını İnceleme
Genellikle akışın iş akışı üzerinden akar gibi nasıl göründüğünü öğrenmek istenir. İş akışındaki herhangi bir noktada bir akışı incelemek için, herhangi bir bileşenden yalnızca bir çıktı veya giriş PIN 'ini tıklatmanız yeterlidir. Bu durumda, medya dosyası girişinde sıkıştırılmamış video çıkış PIN 'ini tıklatmayı deneyin. Giden videoyu incelemeye izin veren bir iletişim kutusu açılır.

![Sıkıştırılmamış video çıkış PIN 'i inceleniyor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

*Sıkıştırılmamış video çıkış PIN 'i inceleniyor*

Bu durumda, videonun yaklaşık 2 dakikalık bir video için 4:2:2 örnekleme sırasında saniyede 24 karede bir 1920x1080 girişi içerdiğini gösterir.

### <a id="MXF_to_MP4_file_generation"></a>İçin bir video Kodlayıcısı ekleniyor. MP4 dosyası oluşturma
Şimdi, sıkıştırılmamış bir video ve birden çok sıkıştırılmamış ses çıkış PIN 'leri medya dosyası girişinde kullanılabilir. Gelen videoyu kodlamak için, bu durumda oluşturma için iş akışına bir kodlama bileşeni eklenmesi gerekir. MP4 dosyaları.

Video akışını H. bir ile kodlamak için, bir tasarımcı yüzeyine AVC video Kodlayıcısı bileşenini ekleyin. Bu bileşen, giriş olarak sıkıştırılmış bir video akışı alır ve çıkış PIN kodu üzerinde AVC sıkıştırılmış bir video akışı sunar.

![Bağlı olmayan AVC Kodlayıcısı](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

*Bağlı olmayan AVC Kodlayıcısı*

Özellikleri, kodlamanın tam olarak nasıl gerçekleştiğini tespit. Daha önemli ayarlardan bazılarına göz atalım:

* Çıkış genişliği ve çıkış yüksekliği: kodlanan videonun çözümlenmesini belirler. Bu durumda, 640 x 360 iyi bir ayardır.
* Kare hızı: geçiş olarak ayarlandığında kaynak çerçeve hızını benimseyin, ancak bunu geçersiz kılmak mümkündür. Bu bit kare dönüştürme işlemi hareket dengedeğildir.
* Profil ve düzey: AVC profilini ve düzeyini belirler. Farklı düzeyler ve profiller hakkında rahat daha fazla bilgi edinmek için, AVC video Kodlayıcısı bileşenindeki soru işareti simgesine tıklayın ve Yardım sayfasında her bir düzeyin hakkında daha fazla ayrıntı gösterilmektedir. Bu örnekte, 3,2 düzeyinde ana profil kullanın (varsayılan).
* Hız Denetim modu ve bit hızı (Kbps): Bu senaryoda, 1200 Kbps hızında sabit bit hızı (CBR) çıktısını kabul edin
* Video biçimi: H. lenebilir akışa yazılan VUı (video kullanılabilirlik bilgileri) hakkında bilgi sağlar (bir kod çözücü tarafından, doğru şekilde kod çözme için gerekli değildir):
* NTSC (30 fps kullanarak ABD veya Japonya için tipik)
* PAL (Avrupa için tipik, 25 fps kullanan)
* GOP boyut modu: kapalı GOPs ile 2 saniyelik anahtar aralığı ile bizim için sabit GOP boyutunu ayarlayın. 2 saniyelik ayar, Azure Media Services dinamik paketleme ile uyumluluğunu sağlar.

AVC Kodlayıcısı 'nın akışını yapmak için, medya dosyası giriş bileşeninden sıkıştırılmamış video çıkış PIN 'ini, AVC Kodlayıcısındaki sıkıştırılmamış video giriş iğnesine bağlayın.

![Bağlı AVC Kodlayıcısı](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

*Bağlı AVC ana Kodlayıcısı*

### <a id="MXF_to_MP4_audio"></a>Ses akışını kodlama
Bu noktada, başlangıçtaki sıkıştırılmamış ses akışının yine de sıkıştırılması gerekir. Ses akışının sıkıştırılması için iş akışına bir AAC Kodlayıcısı (Dolby) bileşeni ekleyin.

![Bağlı olmayan AVC Kodlayıcısı](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

*Bağlı olmayan AAC Kodlayıcısı*

Artık bir uyumsuzluk var: yalnızca, medya dosyası girişinin iki farklı sıkıştırılmamış ses akışı var olduğu sürece yalnızca bir adet sıkıştırılmamış ses giriş PIN 'i var: bir soldaki ses kanalı ve diğeri sağ için bir tane. (Altı kanallı olan surround sesiyle uğraşıyorsanız.) Bu nedenle, medyayı medya dosyası giriş kaynağından AAC ses kodlayıcısıyla doğrudan bağlamak mümkün değildir. AAC bileşeni, "Aralanmış" ses akışı gerektirir: birbirleriyle hem sol hem de sağ kanal araya eklemeli tek bir akış. Kaynak medya dosyasında, ses izlemelerinin kaynaktaki hangi konumda olduğunu öğrendiğimiz bir şekilde, sol ve sağ için doğru olarak atanmış konuşmacı konumlarına sahip bu tür Aralanmış ses akışını üretiyoruz.

Birincisi, bir, gerekli kaynak ses kanallarından araya eklemeli bir akış oluşturmak istemektedir. Ses akışı ınterleaver bileşeni bunu bizim için işler. İş akışına ekleyin ve medya dosyası girişinden ses çıkışlarını buna bağlayın.

![Bağlı ses akışı ınterleaver](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

*Bağlı ses akışı ınterleaver*

Artık araya eklemeli bir ses akışımız olduğuna göre, hala sol veya sağ konuşmacı konumlarının nerede atanacağını belirtmedik. Bunu belirtmek için, konuşmacı konumundan bir kişi seçebilirsiniz.

![Konuşmacı konumu ekleme yacı](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

*Konuşmacı konumu ekleme yacı*

"Custom" adlı bir kodlayıcı önceden ayarlanmış filtresi ve "2,0 (L, R)" adlı kanal önayarı aracılığıyla bir stereo giriş akışı ile kullanmak üzere konuşmacı konumunu yapılandırın. (Bu, sol hoparlör konumunu Kanal 1 ' e, doğru hoparlör konumunu Kanal 2 ' ye atar.)

Konuşmacı konumunun çıkışını, AAC Kodlayıcısı girdisine bağlayın. Daha sonra, AAC Kodlayıcısın bir "2,0 (L, R)" kanal ön ayarıyla çalışmasını söyleyin, bu nedenle stereo ses girişi olarak ele almak için.

### <a id="MXF_to_MP4_audio_and_fideo"></a>Bir MP4 kapsayıcısına ses ve video akışlarını çoğullama
AVC kodlamalı video akışımız ve AAC kodlamalı ses akışımız verildiğinde, her ikisini de bir içinde yakalabiliriz. MP4 kapsayıcısı. Farklı akışları tek bir olarak karıştırma işlemi "çoğullama" (veya "çoğullama") olarak adlandırılır. Bu durumda, ses ve video akışlarını tek bir tutarlı olarak araya getiriyoruz. MP4 paketi. İçin bunu koordine eden bileşen. MP4 kapsayıcısına ISO MPEG-4 Çoğullayıcı adı verilir. Tasarımcı yüzeyine bir tane ekleyin ve hem AVC video Kodlayıcısı 'nı hem de AAC Encoder 'ı girdilerine bağlayın.

![Bağlı MPEG4 Çoğullayıcı](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

*Bağlı MPEG4 Çoğullayıcı*

### <a id="MXF_to_MP4_writing_mp4"></a>MP4 dosyası yazılıyor
Bir çıkış dosyası yazılırken, dosya çıktı bileşeni kullanılır. Çıktıyı diske yazılacak şekilde ISO MPEG-4 Çoğullayıcı çıktısına bağlayabiliriz. Bunu yapmak için, kapsayıcı (MPEG-4) çıkış PIN 'ini dosya çıktısının yazma giriş iğnesine bağlayın.

![Bağlı dosya çıkışı](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

*Bağlı dosya çıkışı*

Kullanılan dosya adı dosya özelliğine göre belirlenir. Bu özellik belirli bir değere kodlanması mümkün olsa da, büyük olasılıkla bunun yerine bir ifade aracılığıyla ayarlamak istemektedir.

İş akışının çıkış dosyası adı özelliğini bir ifadeden otomatik olarak belirlemesini sağlamak için dosya adının yanındaki düğmeye (klasör simgesinin yanında) tıklayın. Açılan menüden "Ifade" i seçin. Bu, ifade düzenleyicisini getirir. Önce düzenleyicinin içeriğini temizleyin.

![Boş Ifade Düzenleyicisi](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

*Boş Ifade Düzenleyicisi*

İfade Düzenleyicisi, bir veya daha fazla değişkenle karışık olan herhangi bir sabit değer girmenize olanak sağlar. Değişkenler dolar işaretiyle başlar. $ Tuşuna ulaşırsanız, düzenleyici kullanılabilir değişkenler seçimine sahip bir açılan kutu gösterir. Bizim örneğimizde, çıkış dizini değişkeninin ve temel giriş dosya adı değişkeninin bir birleşimini kullanacağız:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4

![Doldurulmuş Ifade Düzenleyicisi](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

*Doldurulmuş Ifade Düzenleyicisi*

> [!NOTE]
> Azure 'da kodlama işinizin çıkış dosyasını görmek için, ifade düzenleyicisinde bir değer sağlamanız gerekir.
>
>

Tamam 'a vurarak ifadeyi onaylamanız durumunda, özellik penceresi dosya özelliğinin bu noktada hangi değere çözümlendiğini gösterir.

![Dosya Ifadesi çıkış dizini çözümleniyor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

*Dosya Ifadesi çıkış dizini çözümleniyor*

### <a id="MXF_to_MP4_asset_from_output"></a>Çıkış dosyasından Media Services varlık oluşturma
Bir MP4 çıkış dosyası yazdığımızda, bu dosyanın bu iş akışını yürütmenin sonucu olarak Media Services 'in ürettiği çıkış varlığına ait olduğunu belirtmemiz gerekir. Bu uçta, iş akışı tuvalindeki çıkış dosyası/varlık düğümü kullanılır. Bu düğümdeki tüm gelen dosyalar, sonuçta elde edilen Azure Media Services varlığın bir parçasını yapar.

İş akışını tamamlaması için dosya çıktı bileşenini çıkış dosyasına/varlık bileşenine bağlayın.

![Tamamlanan Iş akışı](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*Tamamlanan Iş akışı*

### <a id="MXF_to_MP4_test"></a>Tamamlanmış iş akışını yerel olarak test etme
İş akışını yerel olarak test etmek için üstteki araç çubuğundan Oynat düğmesine basın. İş akışı yürütmeyi bitirdiğinde, yapılandırılmış çıkış klasöründe oluşturulan çıktıyı inceleyin. MXF giriş kaynak dosyasından kodlanan tamamlanmış MP4 çıkış dosyasını görürsünüz.

## <a id="MXF_to_MP4_with_dyn_packaging"></a>MXF 'yi MP4 'e kodlama-MultiBit hızı dinamik paketleme etkin
Bu izlenecek yol, tek bir çoklu bit hızı MP4 dosyası kümesi oluşturur ve tek başına AAC kodlamalı ses. MXF giriş dosyası.

Çoklu bit hızına sahip bir varlık çıkışı, Azure Media Services tarafından sunulan dinamik paketleme özellikleriyle birlikte kullanılmak üzere isteniyorsa, her biri farklı bir bit hızında ve çözümlenmeye yönelik birden çok GOP hizalanmış MP4 dosyası oluşturulması gerekir. Bunu yapmak için, [MXF kodlamasının tek bir bit hızı MP4 yönergeye dönüştürülmesi](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4) , bize iyi bir başlangıç noktası sağlar.

![Iş akışı başlatılıyor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

*Iş akışı başlatılıyor*

### <a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>Bir veya daha fazla ek MP4 çıkışı ekleme
Elde edilen Azure Media Services varlıklarımızda bulunan her MP4 dosyası, farklı bir bit hızı ve çözünürlüğü destekler. İş akışına bir veya daha fazla MP4 çıkış dosyası ekleyelim.

Tüm video kodlayıcılarımızın aynı ayarlarla oluşturulduğundan emin olmak için, zaten mevcut olan AVC video Kodlayıcısı 'nı çoğaltmak ve farklı bir çözünürlük ve bit hızı birleşimi yapılandırmak (bir saniyede 960 x 540 ' den bir tane, 2,5 Mbps ). Varolan kodlayıcıyı çoğaltmak için, Kopyala öğesini tasarımcı yüzeyine yapıştırın.

Medya dosyası girişinin sıkıştırılmamış video çıkış PIN 'ini yeni AVC bileşenimize bağlayın.

![İkinci AVC Kodlayıcısı bağlandı](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

*İkinci AVC Kodlayıcısı bağlandı*

Şimdi yeni AVC Kodlayıcımız için yapılandırmayı 2,5 Mbps hızında çıkış 960x540 olarak uyarlayın. (Bunun için "çıktı genişliği", "çıkış yüksekliği" ve "bit hızı (Kbps)" özelliklerini kullanın.)

Sonuçta elde edilen varlığı Azure Media Services ' dinamik paketleme ile birlikte kullanmak istiyoruz, akış uç noktasının bu MP4 dosyalarından, her bir şekilde birbirlerine tam olarak hizalanmış ve parçalanmış MP4/DASH parçalarından oluşturulması gerekir. farklı bitoranlar arasında geçiş yapan istemciler, tek bir kesintisiz sürekli video ve ses deneyimi alır. Bunun gerçekleşmesini sağlamak için, her iki MP4 dosyası için hem AVC kodlayıcılara ait özelliklerin her ikisi de tarafından yapılabilen 2 saniyeye ayarlanmış olduğundan emin olunması gerekir:

* GOP boyut modunu sabit GOP boyutuna ayarlama ve
* Anahtar çerçeve aralığı iki saniyeye kadar.
* Ayrıca, tüm GOPs 'ın bağımlılıklar olmadan kendi üzerinde yer aldığından emin olmak için GOP ıDR denetimini kapalı GOP olarak ayarlayın

Bu iş akışının anlaşılması daha kolay hale getirmek için ilk AVC Encoder 'ı "AVC video Kodlayıcısı 640 x 360 1200 kbps" ve ikinci AVC Encoder "AVC video encoder 960x540 2500 kbps" olarak yeniden adlandırın.

Şimdi ikinci bir ISO MPEG-4 Çoğullayıcı ve ikinci bir dosya çıkışı ekleyin. Çoğullayıcı 'yı yeni AVC Kodlayıcısı 'na bağlayın ve çıktısının dosya çıktısına yönlendirildiğinden emin olun. Ayrıca, AAC ses Kodlayıcısı çıkışını yeni Çoğullayıcı girdisine bağlayın. Sırasıyla dosya çıktısı, oluşturulacak Media Services varlığına eklemek için çıkış dosyasına/varlık düğümüne bağlanabilir.

![İkinci muxer ve dosya çıkışı bağlandı](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

*İkinci muxer ve dosya çıkışı bağlandı*

Dinamik paketleme Azure Media Services uyumluluk için, Çoğullayıcı öbek modunu GOP sayısı veya süre olarak yapılandırın ve yığın başına GOPs 'u 1 olarak ayarlayın. (Bu, varsayılan değer olmalıdır.)

![Muxer öbek modları](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

*Muxer öbek modları*

Note: varlık çıktısına eklenmesini istediğiniz diğer bit hızı ve çözünürlük birleşimleri için bu işlemi tekrarlamak isteyebilirsiniz.

### <a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>Dosya çıkış adlarını yapılandırma
Çıkış varlığına birden fazla tane tek dosya eklenmiş. Bu, her bir çıkış dosyası için dosya adlarının birbirinden farklı olduğundan emin olmak ve belki de ilgilendiğiniz dosya adından net hale gelmesi için dosya adlandırma kuralı da uygulamamalıdır.

Dosya çıktısı adlandırması, tasarımcıda ifadeler aracılığıyla denetlenebilir. Dosya çıkış bileşenlerinden biri için özellik bölmesini açın ve dosya özelliği için ifade düzenleyicisini açın. İlk çıkış dosyası aşağıdaki ifade kullanılarak yapılandırıldı ( [MXF 'den tek bir bit hızı MP4 çıktısına](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)gitmek için öğreticiye bakın):

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4

Bu, dosya adınızın iki değişken tarafından belirlendiği anlamına gelir: yazılacak çıkış dizini ve kaynak dosya tabanı adı. İlki, iş akışı kökünde bir özellik olarak sunulur ve ikincisi gelen dosya tarafından belirlenir. Çıkış dizini, yerel test için kullandığınız şeydir; iş akışı Azure Media Services içindeki bulut tabanlı medya işlemcisi tarafından yürütüldüğünde, bu özellik iş akışı altyapısı tarafından geçersiz kılınır.
Çıkış Dosyalarınıza her ikisine de tutarlı bir çıktı adlandırması vermek için, ilk dosya adlandırma ifadesini şu şekilde değiştirin:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

ve ikincisinin:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4

Her iki MP4 çıkış dosyasının düzgün şekilde oluşturulduğundan emin olmak için bir ara test çalıştırması yürütün.

### <a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>Ayrı bir ses Izi ekleme
Daha sonra MP4 çıkış dosyalarınıza gitmek üzere bir. ISM dosyası oluşturduğumuzda, uyarlamalı akışımız için ses izi olarak yalnızca bir ses MP4 dosyası da gerekecektir. Bu dosyayı oluşturmak için, iş akışına (ISO-MPEG-4 Çoğullayıcı) ek bir muxer ekleyin ve 1. parça için AAC Kodlayıcısı çıkış PIN 'ini giriş PIN kodu ile bağlayın.

![Ses muxer eklendi](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

*Ses muxer eklendi*

Muxer 'den giden akışı çıkarmak için bir üçüncü dosya çıkış bileşeni oluşturun ve dosya adlandırma ifadesini şu şekilde yapılandırın:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4

![Ses muxer dosya çıkışı oluşturma](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

*Ses muxer dosya çıkışı oluşturma*

### <a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>Ekleniyor. ISM SMıL dosyası
Dinamik paketleme 'nın Media Services varlıklarımızda hem MP4 dosyaları (hem de yalnızca ses MP4) ile birlikte çalışması için bir bildirim dosyası ("SMıL" dosyası da denir) gerekir: Eşitlenmiş multimedya tümleştirme dili). Bu dosya, dinamik paketleme için hangi MP4 dosyalarının kullanılabilir olduğunu ve bunların ses akışı için göz önünde bulundurduhangilerinin Azure Media Services belirtir. Tek bir ses akışına sahip bir MP4's kümesi için tipik bir bildirim dosyası şuna benzer:

```xml
    <?xml version="1.0" encoding="utf-8" standalone="yes"?>
    <smil xmlns="https://www.w3.org/2001/SMIL20/Language">
      <head>
        <meta name="formats" content="mp4" />
      </head>
      <body>
        <switch>
          <video src="H264_1900kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_1300kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_900kbps_AAC_und_ch2_96kbps.mp4" />
          <audio src="AAC_ch2_96kbps.mp4" title="AAC_und_ch2_96kbps" />
        </switch>
      </body>
    </smil>
```

. ISM dosyası bir switch ifadesinin içinde, tek tek MP4 video dosyalarının her birine bir başvuru, aynı zamanda yalnızca ses içeren bir MP4 'ye bir (veya daha fazla) ses dosyası başvurusu olan bir.

MP4's kümesi için bildirim dosyasının oluşturulması "AMS bildirim yazıcısı" adlı bir bileşen aracılığıyla yapılabilir. Bunu kullanmak için, yüzeyine sürükleyin ve üç dosya çıkış bileşeninden "yazma tamam" çıkış iğnelerini AMS bildirim yazıcısı girişine bağlayın. Ardından AMS bildirim yazıcısının çıkışını çıkış dosyasına/varlığına bağlamayı unutmayın.

Diğer dosya çıkış bileşenlerimizde olduğu gibi,. ISM dosyası çıkış adını bir ifadeyle yapılandırın:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism

Tamamlanan iş akışımız aşağıdaki gibi görünür:

![MXF, MultiBit hızı MP4 iş akışına tamamlandı](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

*MXF, MultiBit hızı MP4 iş akışına tamamlandı*

## <a id="MXF_to__multibitrate_MP4"></a>MXF 'yi çoklu bit hızına sahip MP4 ile kodlama-Gelişmiş şema
[Önceki iş akışında](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging) , tek BIR MXF giriş varlığının çoklu BIT hızı MP4 dosyaları, bir salt ses MP4 dosyası ve Azure Media Services dinamik paketlemeyle birlikte kullanılmak üzere bir bildirim dosyası olan bir çıkış varlığına nasıl dönüştürülebileceğini gördük.

Bu izlenecek yolda, bazı yönlerinin nasıl iyileştirilen ve daha uygun hale getirilbileceği gösterilmektedir.

### <a id="MXF_to_multibitrate_MP4_overview"></a>İyileştirmek için iş akışına genel bakış
![Geliştirmeye yönelik çoklu bit hızı MP4 iş akışı](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

*Geliştirmeye yönelik çoklu bit hızı MP4 iş akışı*

### <a id="MXF_to__multibitrate_MP4_file_naming"></a>Dosya adlandırma kuralları
Önceki iş akışında, çıkış dosyası adları oluşturma temeli olarak basit bir ifade belirttik. Ancak bazı çoğaltıyoruz vardır: Bu tür bir ifade belirtilen tek tek çıkış dosyası bileşenleri.

Örneğin, ilk video dosyası için dosya çıkış bileşenimiz Bu ifadeyle yapılandırılır:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

İkinci çıkış videosu için, şöyle bir ifade sunuyoruz:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4

Bu çoğaltmanın bazılarını kaldırabilmemiz ve bunun yerine daha fazla yapılandırılabilir hale getiriyoruz, temizleyici, daha az hata ve daha kolay bir sorun olması gerekir mi? Luckily: iş akışı kökümüzde özel özellikler oluşturma özelliği ile birlikte tasarımcı 'nın ifade özellikleri, ek bir kolaylık düzeyi sağlar.

Dosya adı yapılandırmasını bireysel MP4 dosyalarının bit oranlarından geçireceğiz. Bu bitfiyatlar, dosya adı oluşturmayı yapılandırma ve sürücü olarak kullanılacak yerden bir merkezi yerde (Graf köğimizin kökünde) yapılandırmak için hedeflenir. Bunu yapmak için, hem AVC Kodlayıcılarından hem de hem kök hem de AVC Kodlayıcılarından erişilebilen bit hızı özelliğini iş akışımızın köküne yayımlayarak başlayacağız. (İki farklı nokta halinde görüntülense bile, yalnızca bir temel değer vardır.)

### <a id="MXF_to__multibitrate_MP4_publishing"></a>Bileşen özellikleri iş akışı köküne yayımlanıyor
İlk AVC Encoder ' ı açın, bit hızı (Kbps) özelliğine gidin ve açılan listeden Yayımla ' yı seçin.

![Bit hızı özelliği yayımlanıyor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

*Bit hızı özelliği yayımlanıyor*

Yayımla iletişim kutusunu, "video1bitrate" yayımlanmış adı ve "video 1 bit hızı" okunabilir görünen adı ile iş akışı Graf köküne yayımlamak üzere yapılandırın. "Akış bit hızları" adlı özel bir grup adı yapılandırın ve Yayınla ' ya basın.

![Bit hızı özelliği yayımlanıyor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

*Bit hızı özelliği için yayımlama iletişim kutusu*

İkinci AVC Kodlayıcısı 'nın bit hızı özelliği için aynısını tekrarlayın ve "video2bitrate" adını "video 2 bit hızı" görünen adıyla "akış bit fiyatları" olarak adlandırın.

Artık iş akışı kök özelliklerini inceleriz, yayımlanan iki özelliği gösteren özel grubumuzu görüyoruz. Her ikisi de ilgili AVC Kodlayıcısı bit hızı değerini yansıtır.

![İş akışı kökünde yayınlanan bit hızı props](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

Koddan veya bir ifadeden bu özelliklere erişmek istediğimiz için bunu şöyle yapabiliriz:

* bir bileşenden alınan satır içi koddan: node. getPropertyAsString ('.. /video1bit hızı ', null)
* bir ifade içinde: $ {ROOT_video1bitrate}

Ses izi bit hızını aynı zamanda yayımlayarak "akış bit oranları" grubunu tamamlayalım. AAC Kodlayıcısı 'nın özellikleri içinde, bit hızı ayarını arayın ve yanındaki açılan listeden Yayımla ' yı seçin. "Audio1bitrate" adlı grafiğin köküne ve "akış bit fiyatları" özel grubumuzdaki "ses 1 bit hızı" görünen adına yayımlayın.

![Ses hızı için yayımlama iletişim kutusu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

*Ses hızı için yayımlama iletişim kutusu*

![Kök üzerinde elde edilen video ve ses props](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

*Kök üzerinde elde edilen video ve ses props*

Bu üç değerin herhangi birini değiştirmek Ayrıca, bağlı oldukları ilgili bileşenlerde (ve ' den yayımlandığında) değerleri yeniden yapılandırır ve değiştirir.

### <a id="MXF_to__multibitrate_MP4_output_files"></a>Yayımlanmış özellik değerlerini kullanan çıkış dosyası adlarını oluşturmuş olmanız
Oluşturulan dosya adlarımızla ilgili olarak kodlanması yerine, artık, grafik kökünde yayımladığımız bit hızı özelliklerine göre dosya çıkış bileşenlerinde dosya adı ifademizi değiştirebiliriz. İlk dosya çıktımız ile başlayarak, dosya özelliğini bulun ve ifadeyi şu şekilde düzenleyin:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4

Bu ifadedeki farklı parametrelere, ifade penceresinde klavyede dolar işaretine vurarak erişilebilir ve girilebilir. Kullanılabilir parametrelerden biri, daha önce yayımladığımız video1bitrate özelliğidir.

![Bir ifade içindeki parametrelere erişme](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

*Bir ifade içindeki parametrelere erişme*

İkinci videoımız için dosya çıktısı için de aynısını yapın:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4

ve yalnızca ses dosyası çıkışı için:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4

Artık herhangi bir video veya ses dosyasının bit hızını değiştirdik, ilgili kodlayıcı yeniden yapılandırılır ve bit hızı tabanlı dosya adı kuralı tüm otomatik olarak kabul edilir.

## <a id="thumbnails_to__multibitrate_MP4"></a>Çoklu bit hızı MP4 çıkışına küçük resim ekleme
Bir [MXF girişinden çoklu bit hızı MP4 çıkışı](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)üreten bir iş akışından başlayarak, artık çıktıya küçük resim ekleme konusu olacak.

### <a id="thumbnails_to__multibitrate_MP4_overview"></a>Küçük resimleri eklemek için iş akışına genel bakış
![Başlangıç için çoklu bit hızı MP4 iş akışı](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

*Başlangıç için çoklu bit hızı MP4 iş akışı*

### <a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>JPG kodlaması ekleniyor
Küçük resim oluşturma aracımız, jpg Kodlayıcısı bileşeni olan ve JPG dosyalarını kaydedebilecek olan kalp olacaktır.

![JPG Kodlayıcısı](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

*JPG Encoder*

Ancak, sıkıştırılmamış video akışımız medya dosyası girişinden JPG kodlayıcısıyla doğrudan bağlanamadık. Bunun yerine, tek tek çerçeveleri de almak bekler. Bu, video çerçeve kapısı bileşeni aracılığıyla yapabiliriz.

![Bir çerçeve kapısını JPG Kodlayıcısı 'na bağlama](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

*Bir çerçeve kapısını JPG Kodlayıcısı 'na bağlama*

Çerçeve kapısı her bu kadar çok saniye veya kare bir video çerçevesinin geçmesine izin verir. Bu zaman aralığı ve bu durum, özelliklerde yapılandırılabilir.

![Video çerçevesi kapı özellikleri](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

*Video çerçevesi kapı özellikleri*

Modu saat (saniye) ve aralığı 60 olarak ayarlayarak her dakika sonra bir küçük resim oluşturalım.

### <a id="thumbnails_to__multibitrate_MP4_color_space"></a>Renk alanı dönüştürmesiyle ilgili
Çerçeve kapısında hem sıkıştırılmamış video PIN 'leri mantıklı, hem de medya dosyası girişi artık bağlı olabilir, bunu yapacağımız bir uyarı alırız.

![Giriş renk alanı hatası](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

*Giriş renk alanı hatası*

Bunun nedeni, MXF 'den gelen özgün ham sıkıştırılmamış video akışımızda gösterilen renk bilgilerinin, JPG Kodlayıcısı 'nın beklediği andan farklı olmasından kaynaklanır. Daha özel olarak, "RGB" veya "gri tonlamalı" olarak adlandırılan "renk uzayı" adı içinde Flow bekleniyor. Bu, video çerçevesi kapısını gelen video akışının, önce renk alanı ile ilgili olarak bir dönüştürme uygulamış olması gerektiği anlamına gelir.

Renk alanı dönüştürücüsünü-Intel olan iş akışına sürükleyin ve çerçeve kapımıza bağlayın.

![Renk alanı dönüştürmesi bağlama](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

*Renk alanı dönüştürmesi bağlama*

Özellikler penceresinde, önceden ayarlanmış listeden BGR 24 girişini seçin.

### <a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>Küçük resimleri yazma
MP4 videoızdan farklı olarak, JPG Kodlayıcısı bileşeni birden fazla dosya çıkışı verir. Bu sorunu ele almak için bir sahne araması JPG dosya yazıcısı bileşeni kullanılabilir: gelen JPG küçük resimlerini alır ve bunları yazar, her dosya adı farklı bir sayı ile sabitlenir. (Genellikle küçük resmin çizilme akışındaki saniye/birim sayısını belirtir.)

![Sahne araması JPG dosya yazıcısına giriş](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

*Sahne araması JPG dosya yazıcısına giriş*

Çıkış klasörü yolu özelliğini şu ifadeyle yapılandırın: $ {ROOT_outputWriteDirectory}

ve dosya adı öneki özelliği:

    ${ROOT_sourceFileBaseName}_thumb_

Ön ek, küçük resim dosyalarının nasıl adlandırıldığını belirler. Bu değerler, Thumb 'in akıştaki konumunu belirten bir sayı ile sabitlenmiştir.

![Sahne arama JPG dosyası yazıcı özellikleri](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

*Sahne arama JPG dosyası yazıcı özellikleri*

Sahne Search JPG dosya yazıcısını çıkış dosyasına/varlık düğümüne bağlayın.

### <a id="thumbnails_to__multibitrate_MP4_errors"></a>Bir iş akışındaki hataları algılama
Renk alanı dönüştürücüsünün girişini, ham sıkıştırılmamış video çıktısına bağlayın. Şimdi iş akışı için bir yerel test çalıştırması gerçekleştirin. İş akışının aniden yürütmeyi durdurması ve bir hatayla karşılaşan bileşende kırmızı bir ana hat ile belirtmesi iyi bir şansa sahip olur:

![Renk alanı dönüştürücü hatası](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

*Renk alanı dönüştürücü hatası*

Kodlama denemesinin başarısız olma nedenini görmek için renk alanı Dönüştürücüsü bileşeninin sağ üst köşesindeki küçük kırmızı "E" simgesine tıklayın.

![Renk alanı dönüştürücü hatası iletişim kutusu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

*Renk alanı dönüştürücü hatası iletişim kutusu*

Gördüğünüz gibi, renk alanı Dönüştürücüsü için gelen renk alanı standardının rec601, istenen YUV 'nin RGB 'ye dönüştürümmiz için olması gerektiğini de kapatır. Akışımızda rec601 göstermez. (REC 601, dijital video biçimindeki aralıklı analog video sinyalleri kodlamaya yönelik bir standarttır. Satır başına 720 ışıklılık örneği ve 360 kazance örneği kapsayan etkin bir bölge belirtir. Renk kodlama sistemi, YCbCr 4:2:2 olarak bilinir.)

Bunu yapmak için, rec601 içeriğiyle ilgilendiğimiz akışımız meta verileri belirteceğiz. Bunu yapmak için ham kaynağınıza ve renk alanı dönüştürme bileşenine koyacağımız bir video veri türü Güncelleştirici bileşeni kullanacağız. Bu veri türü Güncelleştirici, belirli video veri türü özelliklerinin el ile güncelleştirilmesini sağlar. Bunu, "REC 601" öğesinin renk alanı standardını belirtecek şekilde yapılandırın. Bu, henüz tanımlanmış bir renk alanı yoksa video veri türü güncelleştiricisi 'nin akışı "REC 601" renk alanı ile etiketlamasına neden olur. (Geçersiz kılma onay kutusu işaretlenmediği takdirde, var olan tüm meta verileri geçersiz kılmaz.)

![Veri türü Güncelleştirici üzerinde renk alanı standardı güncelleştiriliyor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

*Veri türü Güncelleştirici üzerinde renk alanı standardı güncelleştiriliyor*

### <a id="thumbnails_to__multibitrate_MP4_finish"></a>Tamamlanan Iş akışı
İş akışınız tamamlandığına göre, daha sonra başarılı olduğunu görmek için başka bir test çalıştırması yapın.

![Küçük resim içeren çok MP4 çıkışı için iş akışı tamamlandı](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

*Küçük resim içeren çok MP4 çıkışı için iş akışı tamamlandı*

## <a id="time_based_trim"></a>Çoklu bit hızı MP4 çıkışının zaman tabanlı bölünmesi
Bir [MXF girişinden çoklu bit hızı MP4 çıkışı](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)üreten bir iş akışından başlayarak, artık kaynak videosunu zaman damgalarına göre kırpıyoruz.

### <a id="time_based_trim_start"></a>Kırpma eklemeye başlamak için iş akışına genel bakış
![Kırpma eklemek için iş akışı başlatılıyor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

*Kırpma eklemek için iş akışı başlatılıyor*

### <a id="time_based_trim_use_stream_trimmer"></a>Akış kırpıcıyı kullanma
Akış ayarlayıcısı bileşeni, zamanlama bilgilerinde bir giriş akışı tabanının başlangıcını ve bitimesini kırpmanıza olanak sağlar (saniye, dakika,...). Ayarlayıcısı, çerçeve tabanlı kırpmayı desteklemez.

![Akış ayarlayıcısı](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

*Akış ayarlayıcısı*

AVC kodlayıcılarını ve konuşmacı konumunu doğrudan medya dosyası girişine bağlamak yerine, bu akış kırpıcısı arasına konacağız. (Video sinyali için bir diğeri ise araya eklemeli ses sinyali için.)

![Akış kırpıcıyı arasına yerleştir](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

*Akış kırpıcıyı arasına yerleştir*

Video ve ses videosunu yalnızca 15 saniye ve 60 saniye arasında işleyebilmemiz için kırpıcıyı yapılandıralim.

Video akışı ayarlayıcısı 'nın özelliklerine gidin ve başlangıç zamanı (15 s) ve bitiş zamanı (60 s) özelliklerini yapılandırın. Hem ses hem de video ayarlayıcısı 'nın her zaman aynı başlangıç ve bitiş değerlerine yapılandırıldığından emin olmak için, bunları iş akışının köküne yayımladık.

![Akış kırpıcısı 'ndan başlangıç zamanı özelliği yayımlama](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

*Akış kırpıcısı 'ndan başlangıç zamanı özelliği yayımlama*

![Başlangıç zamanı için özellik Yayımla iletişim kutusu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

*Başlangıç zamanı için özellik Yayımla iletişim kutusu*

![Bitiş zamanı için özellik Yayımla iletişim kutusu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

*Bitiş zamanı için özellik Yayımla iletişim kutusu*

Artık iş akışımızın kökünü inceliyoruz, her iki özellik de aynı şekilde görüntülenir ve yapılandırılabilir.

![Kökte bulunan yayımlanmış Özellikler](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

*Kökte bulunan yayımlanmış Özellikler*

Şimdi ses kırpıcısı ' ndan kırpma özelliklerini açın ve iş akışımızın kökündeki yayımlanan özelliklere başvuran bir ifadeyle hem başlangıç hem de bitiş zamanlarını yapılandırın.

Ses kırpma başlangıç zamanı için:

    ${ROOT_TrimmingStartTime}

ve bitiş saati için:

    ${ROOT_TrimmingEndTime}

### <a id="time_based_trim_finish"></a>Tamamlanan Iş akışı
![Tamamlanan Iş akışı](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*Tamamlanan Iş akışı*

## <a id="scripting"></a>Komut dosyalı bileşene giriş
Komut dosyalı bileşenler, iş akışımızın yürütme aşamaları sırasında rastgele betikler yürütebilir. Her biri belirli özelliklere ve iş akışı yaşam döngüsünde kendilerine ait olan, yürütülebilecek dört farklı betik vardır:

* **commandScript**
* **realizeScript**
* **processInputScript**
* **lifeCycleScript**

Komut dosyalı bileşenin belgeleri, yukarıdaki her biri için daha ayrıntılı olarak sonuçlanır. [Aşağıdaki bölümde](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim), **Realizescript** betik bileşeni, iş akışı başladığında anında bir cliplist XML oluşturmak için kullanılır. Bu betik, bir bileşen kurulumu sırasında çağrılır ve bu işlem yaşam döngüsünün yalnızca bir kez gerçekleşir.

### <a id="scripting_hello_world"></a>Bir iş akışı içinde betik oluşturma: Merhaba Dünya
Komut dosyalı bir bileşeni tasarımcı yüzeyine sürükleyin ve yeniden adlandırın (örneğin, "SetClipListXML").

![Komut dosyalı bileşen ekleme](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Komut dosyalı bileşen ekleme*

Komut dosyalı bileşenin özelliklerini incelemenize sonra, her biri farklı bir komut dosyası için yapılandırılabilir dört farklı betik türü gösterilir.

![Betikleştirilmiş bileşen özellikleri](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Betikleştirilmiş bileşen özellikleri*

Processınputscript ' i temizleyin ve realizeScript için düzenleyiciyi açın. Şimdi betik oluşturmaya başlamaya hazır hale getiriyoruz.

Betikler, Java ile uyumluluğu koruyan Java platformu için dinamik olarak derlenmiş bir komut dosyası dili olan groovy dilinde yazılır. Aslında, çoğu Java kodu geçerli groovy kodudur.

RealizeScript 'ın bağlamına basit bir Hello World groovy betiği yazalım. Düzenleyicide aşağıdakini girin:

    node.log("hello world");

Şimdi bir yerel test çalıştırması yürütün. Bu çalıştırıldıktan sonra (komut dosyalı bileşendeki sistem sekmesi aracılığıyla) Günlükler özelliğini inceleyin.

![Merhaba Dünya günlüğü çıkışı](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

*Merhaba Dünya günlüğü çıkışı*

Günlük yöntemini çağırdığımız düğüm nesnesi, geçerli "düğümümüzü" veya komut dosyası içinde yaptığımız bileşen anlamına gelir. Bu gibi her bileşen, sistem sekmesinden erişilebilen günlüğe kaydetme verilerinin çıktısını alma olanağına sahiptir. Bu durumda, "Hello World" dize sabit değerini çıktımız gerekir. Burada anlaşılması gereken önemli bir hata ayıklama aracı olduğunu kanıtlayabileceğinden, betiğinin gerçekten yaptığına ilişkin öngörüler sağlar.

Komut dosyası ortamımızın içinden diğer bileşenlere yönelik özelliklere de erişebiliyoruz. Şunu deneyin:

```java
    //inspect current node:
    def nodepath = node.getNodePath();
    node.log("this node path: " + nodepath);

    //walking up to other nodes:
    def parentnode = node.getParentNode();
    def parentnodepath = parentnode.getNodePath();
    node.log("parent node path: " + parentnodepath);

    //read properties from a node:
    def sourceFileExt = parentnode.getPropertyAsString( "sourceFileExtension", null );
    def sourceFileName = parentnode.getPropertyAsString("sourceFileBaseName", null);
    node.log("source file name with extension " + sourceFileExt + " is: " + sourceFileName);
```

Günlük penceremiz aşağıda verilmiştir:

![Düğüm yollarına erişmek için günlük çıktısı](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

*Düğüm yollarına erişmek için günlük çıktısı*

## <a id="frame_based_trim"></a>Çoklu bit hızı MP4 çıkışının çerçeve tabanlı bölünmesi
Bir [MXF girişinden çoklu bit hızı MP4 çıkışı](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)üreten bir iş akışından başlayarak, artık çerçeve sayılarına göre kaynak videosunu kırpmaya bakıyoruz.

### <a id="frame_based_trim_start"></a>Şemayı eklemeye başlamak için Blueprint genel bakışı
![Kırpma eklemeye başlamak için iş akışı](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

*Kırpma eklemeye başlamak için iş akışı*

### <a id="frame_based_trim_clip_list"></a>Klip Listesi XML 'i kullanma
Önceki tüm iş akışı öğreticilerinde, video giriş kaynağınıza göre medya dosyası giriş bileşenini kullandık. Bu senaryoya karşın, bunun yerine klip listesi kaynak bileşenini kullanacağız. Bu, çalışmak için tercih edilen bir yöntem olmamalıdır; yalnızca bir gerçek neden olduğunda (örneğin, klip listesi kırpma yeteneklerini kullandığımızda) klip listesi kaynağını kullanın.

Medya dosyası girdiğimiz listeden klip listesi kaynağına geçiş yapmak için klip listesi kaynak bileşenini tasarım yüzeyine sürükleyin ve Clip List XML PIN 'ini iş akışı Tasarımcısı ' nın klip listesi XML düğümüne bağlayın. Bu, giriş videolarımıza göre klip listesi kaynağını çıkış iğnelerine göre doldurur. Şimdi, sıkıştırılmamış videoyu ve sıkıştırılmamış ses PIN 'lerini klip listesi kaynağından ilgili AVC kodlayıcılara ve ses akışı karşılıklı bağlantı kaynaklarına bağlayın. Şimdi medya dosyası girişini kaldırın.

![Medya dosyası girişi, klip listesi kaynağıyla değiştirilmiştir](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

*Medya dosyası girişi, klip listesi kaynağıyla değiştirilmiştir*

Klip listesi kaynak bileşeni, "Clip List XML" girdisini alır. Yerel olarak test edilecek kaynak dosyayı seçerken, bu klip listesi XML 'i sizin için otomatik olarak doldurulur.

![Otomatik olarak doldurulan klip listesi XML özelliği](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

*Otomatik olarak doldurulan klip listesi XML özelliği*

XML 'e biraz yaklaşarak bu, şöyle görünür:

![Klip listesini Düzenle iletişim kutusu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

*Klip listesini Düzenle iletişim kutusu*

Bu ancak, klip listesi XML 'nin yeteneklerini yansıtmaz. Bizim gibi bir seçenek de video ve ses kaynağı altına bir "trim" öğesi eklemektir, örneğin:

![Klip listesine trim öğesi ekleme](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

*Klip listesine trim öğesi ekleme*

Yukarıda olduğu gibi klip listesi XML 'sini değiştirir ve bir yerel test çalıştırması gerçekleştirirseniz, videonun videoda 10 ile 20 saniye arasında doğru şekilde kırpıldığının göreceksiniz.

Ancak, yerel bir çalıştırma yaptığınızda ne olacağı gibi durumlarda, aynı cliplist XML, Azure Media Services ' de çalışan bir iş akışında uygulandığında aynı etkiye sahip olmaz. Azure Premium Kodlayıcısı başladığında, her seferinde, kodlama işinin verildiği giriş dosyasına göre Pano listesi XML 'i oluşturulur. Bu, XML üzerinde yaptığımız tüm değişikliklerin geçersiz kılınabileceğini gösterir.

Bir kodlama işi başlatıldığında cliplist XML 'in temizlenmeden silinebilmemiz için iş akışımızın başlangıcından hemen sonra da bunu yeniden oluşturabilirsiniz. Bu tür özel eylemler, "komut dosyalı bileşen" olarak adlandırılmaktadır. Daha fazla bilgi için bkz. [komut dosyalı bileşeni tanıtma](media-services-media-encoder-premium-workflow-tutorials.md#scripting).

Komut dosyalı bir bileşeni tasarımcı yüzeyine sürükleyin ve "SetClipListXML" olarak yeniden adlandırın.

![Komut dosyalı bileşen ekleme](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Komut dosyalı bileşen ekleme*

Komut dosyalı bileşenin özelliklerini incelemenize sonra, her biri farklı bir betiğe göre yapılandırılabilir dört farklı betik türü gösterilir.

![Betikleştirilmiş bileşen özellikleri](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Betikleştirilmiş bileşen özellikleri*

### <a id="frame_based_trim_modify_clip_list"></a>Betikleştirilmiş bir bileşenden klip listesini değiştirme
İş akışı başlangıcında oluşturulan cliplist XML 'sini yeniden yazmadan önce, cliplist XML özelliğine ve içeriğine erişimi olması gerekir. Bunu şöyle yapabiliriz:

```java
    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);
```

![Günlüğe kaydedilen gelen klip listesi](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

*Günlüğe kaydedilen gelen klip listesi*

İlk olarak, videoyu kırpmak istediğimiz noktayı anlamak için bir yol gerekir. Bunu iş akışının daha az teknik kullanıcısına uygun hale getirmek için grafiğin köküne iki özellik yayımlayın. Bunu yapmak için tasarımcı yüzeyine sağ tıklayın ve "Özellik Ekle" yi seçin:

* İlk özellik: "Clıppingtimestart" türü: "TIMECODE"
* İkinci özellik: Türün "ClippingTimeEnd": "TIMECODE"

![Kırpma başlangıç zamanı için Özellik Ekle iletişim kutusu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

*Kırpma başlangıç zamanı için Özellik Ekle iletişim kutusu*

![İş akışı kökünde yayınlanan kırpma süresi props](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

*İş akışı kökünde yayınlanan kırpma süresi props*

Her iki özelliği de uygun bir değere yapılandırın:

![Kırpma başlangıç ve bitiş özelliklerini yapılandırma](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

*Kırpma başlangıç ve bitiş özelliklerini yapılandırma*

Şimdi betiğimizin içinden aşağıdaki gibi her iki özelliğe de erişebiliriz:

```java
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);
```

![Kırpma başlangıcını ve bitişini gösteren günlük penceresi](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

*Kırpma başlangıcını ve bitişini gösteren günlük penceresi*

Daha basit bir normal ifade kullanarak timecode dizelerini daha kullanışlı bir biçimde ayrıştıralım:

```java
    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart);
    startregresult.matches();
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);

    //parse the end timing:
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches();
    def endtimecode = endregresult.group(1);
    node.log("timecode end is: " + endtimecode);
    def endframerate = endregresult.group(2);
    node.log("framerate end is: " + endframerate);
```

![Ayrıştırılmış timecode çıkışıyla günlük penceresi](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-output-parsed-timecode.png)

*Ayrıştırılmış timecode çıkışıyla günlük penceresi*

Bu bilgilerle, şimdi cliplist XML ' i, filmin tam kare doğru kırpması için başlangıç ve bitiş zamanlarını yansıtacak şekilde değiştirebiliriz.

![Kırpma öğeleri eklemek için betik kodu](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

*Kırpma öğeleri eklemek için betik kodu*

Bu, normal dize işleme işlemleri aracılığıyla yapılır. Sonuç olarak değiştirilen klip listesi XML 'i, iş akışı kökündeki "setProperty" yöntemiyle clipListXML özelliğine geri yazılır. Başka bir test çalıştırdıktan sonra günlük penceresinde şunlar gösterilir:

![Elde edilen klip listesini günlüğe kaydetme](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

*Elde edilen klip listesini günlüğe kaydetme*

Video ve ses akışlarının nasıl kırpılacağını görmek için bir test çalıştırması yapın. Kırpma noktaları için farklı değerlerle birden fazla test çalıştırması yapacaksınız, ancak bunların hesaba alınmayacak olduğunu fark edeceksiniz. Bunun nedeni, tasarımcının Azure çalışma zamanının aksine, her çalıştırma için cliplist XML 'i geçersiz kılmaz. Diğer bir deyişle, yalnızca ilk kez ın ve çıkan noktaları ayarladığınızda, XML 'in diğer tüm zamanımızın (yani (`clipListXML.indexOf("<trim>") == -1`)) aynı anda bir tane varsa, iş akışının başka bir kırpma öğesi eklemesini önleyemeyeceği anlamına gelir.

İş akışınızı yerel olarak test etmek için uygun hale getirmek üzere, bir kırpma öğesi zaten mevcut olup olmadığını inceleyerek en iyi şekilde bir düzenleyici kodu ekleyeceğiz. Varsa, XML 'yi yeni değerlerle değiştirerek devam etmeden önce bunu kaldırabiliriz. Düz dize işlemeleri kullanmak yerine, bunu gerçek XML nesne modeli ayrıştırması aracılığıyla yapmak daha güvenlidir.

Böyle bir kodu ekleyebilmemiz için önce betiğimizin başlangıcında bir dizi import deyimi eklememiz gerekir:

```java
    import javax.xml.parsers.*;
    import org.xml.sax.*;
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*;
    import javax.xml.transform.*;
    import javax.xml.transform.stream.*;
    import javax.xml.transform.dom.*;
```

Bundan sonra gerekli temizleme kodunu ekleyebiliriz:

```java
    //for local testing: delete any pre-existing trim elements from the clip list xml by parsing the xml into a DOM:
    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML));
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim";
    NodeList trimelems = xpath.evaluate(findAllTrimElements,dom,XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection
    Set<Element> elementsToDelete = new HashSet<Element>();
    for (int i = 0; i < trimelems.getLength(); i++) {
        Element e = (Element)trimelems.item(i);
        elementsToDelete.add(e);
    }

    node.log("about to delete any existing trim nodes");
     //delete the trim nodes:
    elementsToDelete.each{
        e -> e.getParentNode().removeChild(e);
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();
```

Bu kod, cliplist XML 'e kırpma öğelerini eklediğimiz noktanın hemen üstüne gider.

Bu noktada, iş akışımızı, her zaman uygulanmış değişiklikler olduğu kadar çalıştırabiliriz ve değiştirebiliriz.    

### <a id="frame_based_trim_clippingenabled_prop"></a>Clienppingenabled kolay özelliği ekleme
Her zaman kırpma işleminin gerçekleşmesini istemediğimizden, kırpma/kırpma 'yı etkinleştirmek isteyip istemediğinizi belirten uygun bir Boole bayrağı ekleyerek iş akışımuzu sonlandıralım.

Daha önce olduğu gibi, "BOOLEAN" türünde "ClippingEnabled" adlı iş akışınızın köküne yeni bir özellik yayımlayın.

![Kırpması etkinleştirmek için bir özellik yayımlandı](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

*Kırpması etkinleştirmek için bir özellik yayımlandı*

Aşağıdaki basit Guard yan tümcesiyle, kırpmaya ihtiyaç olup olmadığını kontrol etmemiz ve klip listeimizin değiştirilmesi gerekip gerekmediği konusunda karar veririz.

```java
    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString());
    if(clippingrequired == null || clippingrequired == false)
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return;
    }
```

### <a id="code"></a>Kodu doldurun

```java
    import javax.xml.parsers.*;
    import org.xml.sax.*;
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*;
    import javax.xml.transform.*;
    import javax.xml.transform.stream.*;
    import javax.xml.transform.dom.*;

    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: \n" + clipListXML);
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart);
    startregresult.matches();
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);

    //parse the end timing:
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches();
    def endtimecode = endregresult.group(1);
    node.log("timecode end is: " + endtimecode);
    def endframerate = endregresult.group(2);

    node.log("framerate end is: " + endframerate);

    //for local testing: delete any pre-existing trim elements
    //from the clip list xml by parsing the xml into a DOM:

    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML));
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim";
    NodeList trimelems = xpath.evaluate(findAllTrimElements, dom, XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection
    Set<Element> elementsToDelete = new HashSet<Element>();
    for (int i = 0; i < trimelems.getLength(); i++) {
        Element e = (Element)trimelems.item(i);
        elementsToDelete.add(e);
    }

    node.log("about to delete any existing trim nodes");
    //delete the trim nodes:
    elementsToDelete.each{ e ->
        e.getParentNode().removeChild(e);
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();

    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString());
    if(clippingrequired == null || clippingrequired == false)
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return;
    }

    //add trim elements to cliplist xml
    if ( clipListXML.indexOf("<trim>") == -1 )
    {
        //trim video
        clipListXML = clipListXML.replace("<videoSource>","<videoSource>\n <trim>\n <inPoint fps=\""+
            startframerate +"\">" + starttimecode +
            "</inPoint>\n" + "<outPoint fps=\"" + endframerate +"\"> " + endtimecode +
            " </outPoint>\n </trim> \n");
        //trim audio
        clipListXML = clipListXML.replace("<audioSource>","<audioSource>\n <trim>\n <inPoint fps=\""+
            startframerate +"\">" + starttimecode +
            "</inPoint>\n" + "<outPoint fps=\""+ endframerate +"\">" +
            endtimecode + "</outPoint>\n </trim>\n");
        node.log( "clip list going out: \n" +clipListXML );
        node.setProperty("../clipListXml",clipListXML);
    }
```

## <a name="also-see"></a>Ayrıca bkz.
[Azure Media Services Premium kodlamaya giriş](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[Azure Media Services içinde Premium kodlama kullanma](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[Azure Medya hizmeti ile Isteğe bağlı Içeriği kodlama](media-services-encode-asset.md#media-encoder-premium-workflow)

[Media Encoder Premium İş Akışı Biçimleri ve Kod Çözücüleri](media-services-premium-workflow-encoder-formats.md)

[Örnek iş akışı dosyaları](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Azure Media Services Gezgini aracı](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geri bildirimde bulunma
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
