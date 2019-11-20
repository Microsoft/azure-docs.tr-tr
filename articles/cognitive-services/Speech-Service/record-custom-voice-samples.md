---
title: Özel ses örneklerini kaydet-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Üretim kalitesindeki özel sesli, sağlam bir komut dosyası hazırlanıyor, iyi sesli beceri işe ve profesyonel kaydını yapın.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 47e1f8bf178723a5d4be323f24724214a67499e2
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559187"
---
# <a name="record-voice-samples-to-create-a-custom-voice"></a>Özel ses oluşturma kayıt ses örnekleri

Yüksek kaliteli üretim özel sesli sıfırdan oluşturma, sıradan bir iş değildir. Özel ses bileşeni İnsan konuşma ses örnekleri büyük bir koleksiyonudur. Bu ses kayıtları yüksek kaliteli olması önemlidir. Bu tür bir kayıt yapma deneyimi olan bir sesli beceri seçin ve bunları kullanarak profesyonel ekipman yetkin kaydı mühendisi tarafından kaydedilmiş.

Ancak, bu kayıtları yapmadan önce bir komut dosyası gerekir: Ses örneği oluşturmak için ses beceri tarafından konuşulan sözcükleri. En iyi sonuçlar için iyi fonetik kapsamı ve özel ses modeli eğitmek için yeterli çeşitli betiğinizi olması gerekir.

Birçok küçük ancak önemli ayrıntıları profesyonel ses kaydı oluşturma uygulamasına gidin. Bu kılavuz, iyi ve tutarlı sonuçlar elde etmenize yardımcı olacak bir işlem için bir yol haritası içindir.

> [!TIP]
> Yüksek kaliteli sonuçlar için özel sesli geliştirmenize yardımcı olmak amacıyla Microsoft ilgi çekici göz önünde bulundurun. Microsoft, Cortana ve Office gibi kendi ürünler için yüksek kaliteli ses üretme kapsamlı bir deneyimi vardır.

## <a name="voice-recording-roles"></a>Ses kaydını rolleri

Özel ses kaydı projede dört temel rolü vardır:

Rol|Amaç
-|-
Ses beceri        |Bu kişinin ses, özel sesli temelini oluşturacak.
Kayıt mühendisi  |Kayıt teknik yönlerini denetleyen ve kayıt donanım çalışır.
Direktörü            |Komut dosyası hazırlar ve ses beceri'nın performans coaches.
Düzenleyici              |Ses dosyalarını sonlandırır ve karşıya yükleme özel sesli portalına hazırlayabilirsiniz.

Bireysel birden fazla rol doldurabilirsiniz. Bu kılavuz, öncelikle yöneticisi rolü doldurma ve ses beceri hem bir kayıt mühendisi işe varsayar. Kayıtları hale getirmek isterseniz kendiniz, bu makalede kaydı bir mühendislik rolü hakkında bazı bilgiler içerir. Düzenleyici Rol Yöneticisi veya kayıt mühendisi tarafından gerçekleştirilen şekilde oturum kadar sonra gerek yoktur.

## <a name="choose-your-voice-talent"></a>Kendi ses beceri seçin

Voiceover ya da ses karakter çalışma deneyimiyle aktörler iyi özel sesli beceri olun. Ayrıca sık announcers ve haber okuyucular arasında uygun beceri bulabilirsiniz.

Ses yetenek, doğal ses seçin ister. Benzersiz "character" sesleri oluşturmak mümkündür, ancak tutarlı bir şekilde gerçekleştirmek çoğu beceri çok güçtür ve çabayı ses yükü neden olabilir.

> [!TIP]
> Genellikle, özel sesli oluşturmak için tanınan kişilerden daha fazlasını kullanarak önlemek — Elbette ünlü ses üretmek için amacınız olmadığı sürece. Daha az bilinen sesleri kullanıcılara genellikle daha az dikkat dağıtıcı.

Ses beceri seçme tek en önemli faktör tutarlılık ' dir. Aynı odada aynı günde yapılan gibi kayıtlarınızı tüm ses. Bu ideal iyi kayıt yöntemleri ve mühendislik yaklaşımı.

Eşitlik diğer yarısı, sesli yeteneğiniz olur. Tutarlı hız, birim düzeyi, sıklık ve ton ile konuşabilmeleri gerekir. NET diction zorunluluktur. Ayrıca, taçüsyon 'nın, kendi Aralık çeşitlemesini, dipersel etkileri ve konuşma maniz MS 'leri tam olarak denetleyebilmeleri gerekir.

Özel ses örnekleri kaydı diğer tür üslup çalışmalar daha fazla fatiguing olabilir. Çoğu ses beceri günde iki veya üç saat kaydedebilirsiniz. Mümkünse oturumları üç veya dört haftada bir, bir günü raflarının ile sınırlayın.

Bir ses modeli için yapılan kayıtları bağın bağımsız olmalıdır. Diğer bir deyişle, Üzgün utterance Üzgün şekilde okunur olmamalıdır. Sunu yaparken izleyicilerinizin prosody denetimleri üzerinden Sentezlenen konuşma eklenebilir. "Özel ses genel ses ve duygusal dilini tanımlar kişi" geliştirmek için sesli beceri ile çalışır. İşlemde, "belirsiz" ne benzer bu kişi için saptayın.

Bir kişi, örneğin, bir doğal olarak upbeat kişilik olabilir. Bu nedenle zaman bile neutrally onların konuştuğu "their" ses optimism Not gerçekleştirmek. Ancak, bu tür bir kişilik nitelik zarif ve tutarlı olmalıdır. Okumaları için amaçlayan hakkında bir fikir edinmek için var olan kişilerden daha fazlasını tarafından dinleyin.

> [!TIP]
> Genellikle, yaptığınız ses kayıtları sahip olmasını istersiniz. Ses yetenek, tfs'deki proje için bir iş için işe alma sözleşmesi olması gerekir.

## <a name="create-a-script"></a>Bir komut dosyası oluşturma

Başlangıç oturumu kaydetme herhangi bir özel sesli, sesli beceri tarafından söylenir konuşma içeren betik noktasıdır. ("Konuşma" terimi, tam cümlelerden hem kısa tümcecikleri kapsar.)

Betiğinizde konuşma yerden gelebilir: kurgu kurgu olmayan, konuşmalarını, haber raporlar ve bulunan başka herhangi bir şey dökümleri yazdırılan form. De (örneğin, TIP terminolojisi veya programlama terminolojisinin) bir kelimelerin belirli tür üzerinde sesinizi desteklediğinden emin olmak istiyorsanız, cümleleri akademik incelemeler veya teknik belgelerden eklemek isteyebilirsiniz. Yasal sorunlara ilişkin kısa bir açıklama için bkz. ["Legalities"](#legalities) bölümü. Ayrıca, kendi metninizi yazabilirsiniz.

Konuşma, aynı kaynağa veya kaynak aynı türden olması gerekmez. Birbiriyle yapmak için herhangi bir şey olması bile gerekmez. Ancak, eğer kullanım ayarlamak ifadeleri (örneğin, ", başarılı oturum açma"), konuşma uygulamanızda betiğinizde eklediğinizden emin olun. Bu, özel sesli bu tümcecikleri iyi pronouncing, daha iyi bir fırsat sunar. Ve Sentezlenen konuşma yerine bir kaydı kullanmaya karar verirseniz zaten aynı ses sahip.

Tutarlılık ses beceri seçme içinde anahtar olsa da, çeşitli iyi bir betiğin günlerinden ' dir. Betiğinizi birçok farklı sözcükleri ve tümceleri cümle uzunlukları, yapılar ve ruh çeşitli içermelidir. Her ses dilde gösterilen birden çok kez ve çeşitli bağlamlarda olmalıdır (adlı *fonetik kapsamı*).

Ayrıca, metnin belirli bir ses yazılı olarak temsil edilir ve her ses cümleleri çeşitli yerlerde yerleştirin tüm yolları eklemeniz gerekir. Bildirim temelli cümleler hem sorular bulunan ve uygun tonlama ile okuyun.

Sağlayan bir komut dosyası yazmak zordur *yeterli* iyi sesli oluşturmak özel konuşma tanıma portalı izin vermek için veri. Uygulamada güçlü fonetik kapsamı elde eden bir betik yapmak en kolay yolu çok sayıda örnekleri eklemektir. Microsoft'un sunduğu standart sesi binlerce konuşma oluşturulmuştur. Bir üretim kalitesinde özel sesli oluşturmak için en az birkaç bin konuşma birkaç kaydetmek hazırlıklı olmalıdır.

Betik hataları için dikkatli bir şekilde denetleyin. Mümkünse, çok denetleyin başka birisi vardır. Komut dosyası, beceri ile çalıştırdığınızda, birkaç daha fazla hata büyük olasılıkla catch.

### <a name="script-format"></a>Komut dosyası biçimi

Microsoft Word içinde betiğinizi yazabilirsiniz. Bunu çalışmak kolay bir şekilde ayarlayabilirsiniz bu nedenle betiği kayıt oturumu sırasında kullanılır. Özel ses portal tarafından ayrı olarak gerekli olan bir metin dosyası oluşturun.

Temel betik biçimi üç sütunu içerir:

* 1'den başlayarak utterance sayısı. Numaralandırma kolaylaştırır herkes için belirli bir utterance başvurmak için studio ("sayı 356 yeniden deneyelim"). Tablonun satırlarını otomatik olarak numaralandırmak için özellik numaralandırma Word paragraf kullanabilirsiniz.
* Burada alma sayısı yazma veya tamamlanmış kayıt bulmanıza yardımcı olmak için her utterance kodunu zaman boş bir sütun.
* Utterance metni.

![Örnek betik](media/custom-voice/script.png)

> [!NOTE]
> Çoğu studios kısa Segment olarak bilinen kayıt *alır*. Her 10 ila 24 konuşma genellikle içerir. Yalnızca alma sayısı belirtmeye, daha sonra bir utterance bulmak yeterlidir. Uzun kayıtları yapmak için tercih ettiği bir Studio'da kaydediyorsanız, bunun yerine bir zaman kodundan Not isteyebilirsiniz. Studio belirgin süre görüntü olacaktır.

Her satır sonra notları yazmak için yeterli alan bırakın. Hiçbir utterance sayfalar arasında bölünür emin olun. Sayfa numarası ve kağıt bir tarafında kodunuzu yazdırın.

Komut dosyası üç kopyasını yazdırma: bir yetenek, mühendislik için diğeri için Müdürü (,). Kağıt küçük staples yerine kullanın: deneyimli ses sanatçı sayfaları sayfaları açık olarak gürültü yapmaktan kaçınmak için ayrılır.

### <a name="legalities"></a>Legalities

Telif hakkı yasaları altında bir aktörün okuma telif haklı metin iş yazarı dengelenebilecek bir performans olabilir. Bu performans yer alan son ürünü, özel sesli tanınabilir olmayacaktır. Buna rağmen yasallığı, telif haklı bir çözüm bu amaçla kullanma, iyi kurulu değil. Microsoft, bu sorunla ilgili yasal öneriler sağlayamaz; kendi Konseyi başvurun.

Neyse ki, bu sorunları tamamen önlemek mümkündür. Metin izni veya lisans kullanabileceğiniz birçok kaynak vardır.

|Metin kaynak|Açıklama|
|-|-|
|[CMU kutup gövde](http://festvox.org/cmu_arctic/)|Yaklaşık 1100 cümleler çıkış, telif hakkı works konuşma sentezi projelerinde kullanılmak üzere özel olarak seçilir. Harika bir başlangıç noktası.|
|Artık çalışır<br>Telif hakkı altında|Genellikle works önce 1923 yayımladı. İngilizce, [proje Gutenberg](https://www.gutenberg.org/) on binlerce gibi çalışır sunar. Yeni çalışır, dil için modern İngilizce yakın olarak odaklanmak isteyebilirsiniz.|
|Kamu&nbsp;çalışır|Kamu, Birleşik Devletler kamu tarafından oluşturulan çalışmalar Birleşik Devletler tarafından desteklenmiyor, ancak kamu, diğer ülkelerde/bölgelerde telif hakkı talep edebilir.|
|Genel etki alanı|Hangi telif hakkı açıkça elverişlilik veya Works ortak etki alanı için ayrılmış. Telif Hakkı tamamen bazı ayları mümkün olmayabilir.|
|Permissively lisanslı çalışır|Creative Commons veya GNU ücretsiz belgeleri lisans (GFDL) gibi bir lisans altında dağıtılan çalışır. Wikipedia GFDL kullanır. Bazı lisans ancak bir özel sesli modeli oluşturulmasını etkileyebilecek lisanslı içerik performansının kısıtlamaları dayatır şekilde okuyun lisans dikkatli bir şekilde.|

## <a name="recording-your-script"></a>Betiğinizi kaydetme

Betiğinizi uzmanlaşmış bir profesyonel kaydı studio, sesli işlerinde kaydedin. Bunlar, bir kaydı standına, doğru donanım ve işletmek için doğru kişilere sahip olacaksınız. Bu kayıt skimp değil öder.

Projeyi, Studio 'nun kayıt mühendisiyle tartışın ve bunların ihbarını dinleyin. Kayıt, çok az kayıpla veya hiç dinamik aralık sıkıştırma (en fazla 4:1) sahip olmalıdır. Ses birimi tutarlı ve yüksek bir sinyal/gürültü oranına istenmeyen sesler ücretsiz olmanın yanı sıra olduğunu önemlidir.

### <a name="do-it-yourself"></a>Bu işlemi kendiniz yapın

Kayıt yapmak istiyorsanız kendiniz kaydı studio uygulamasına gidip yerine İşte kısa öncü. Giriş kaydı ve pod yayımlama Yükselişi sayesinde iyi kayıt talimatı ve kaynakları çevrimiçi bulmak için her zamankinden daha kolaydır.

Küçük bir oda belirgin Yankı veya "odası ton.", "kayıt standına" olmalıdır Sessiz ve mümkün olduğunca soundproof olmalıdır. Yankı azaltmak ve nötrleştirmek ya da "yer sesi deaden" perdeleri duvarlarının üzerinde kullanılabilir.

Ses kaydetmek için hedeflenen yüksek kaliteli studio Kondansatör mikrofon (kısaca "MIC") kullanın. Sennheiser AKG ve hatta yeni yakınlaştırma mikrofonlardan iyi sonuçlara yol açabilir. Bir MIC satın alın veya yerel işitsel kiralama kesin bir kira. USB arabirimi ile arayın. Bu tür bir MIC rahatça mikrofon öğesi, preamp ve -sayısala dönüştürücü birleştirme basitleştirme bir pakete birleştirir.

Bir analog mikrofon de kullanabilirsiniz. Birçok kiralama görev açısından kritik uygulamaları için kendi ses karakter ünlü "Hasat Yılı" mikrofonlar sunar. Profesyonel analog dişli kullanan Not tüketici ekipmanını kullanılan 1/4 inç Tak yerine XLR bağlayıcılar dengeli. Aynı zamanda analog giderseniz, bir preamp ve bu bağlayıcıları olan bir bilgisayarda ses arabirimi gerekir.

Bir yedek veya ok mikrofon yükleyin ve "p" ve "b" gibi "plosive" ünsüzler paraziti ortadan kaldırmak için mikrofon önünde pop filtre yükleyin Bazı mikrofonlar bunları vibrations yararlıdır bağımsız olarak öğesinden ayırır bir askıya alma bağlama gelir.

Ses beceri mikrofondan tutarlı bir uzaklıkta kalması gerekir. Banttaki bandı, tek yapmanız gereken yerleri işaretlemek için kullanın. Beceri tercih durumda kalmaya devam ediyorsa, MIC uzaklık izlemek ve sandalye gürültüsünü önlemek için özel dikkat edin.

Betiğini tutmak için kullanın. Böylece doğru mikrofon ses yansıtabilir öne angling kaçının.

İşletim kaydı ekipman kişi — mühendislik — bazı yolunu kaydı standına içinde beceri konuşmak talent, ayrı bir oda olmalıdır (bir *talkback devre).*

Kayıt bir 80-db sinyal/gürültü oranına veya daha iyi bir hedef ile mümkün olduğunca az gürültü olarak içermelidir.

İçinde "standına," Burada tüm gürültü geldiğini ve neden kaldırın şekil sessizlik kaydını yakından dinlemek. Ortak kaynakları gürültü olasılığını hava olaylarını, floresan ışık ballasts, trafiği yollar ve donanım fanlar (hatta dizüstü bilgisayarlar, fanlar olabilir) yakındaki üzerindedir. Mikrofon ve kablolarını, yakındaki AC kablo, genellikle bir sesleri elektrik paraziti yerden devam edebiliyorduk veya haberleri. Bir hareket da neden olabilir bir *çığır döngü*, hangi nedeni birden fazla elektrik devre takılı ekipman sağlayarak.

> [!TIP]
> Bazı durumlarda, her zaman, kaynakta durdurmak en iyi olmasına rağmen parazit, sonuçlarından kaldırılmasına yardımcı olması için bir dengeleyici veya gürültü azaltma yazılım eklentisini kullanmanız mümkün olabilir.

Bu nedenle overdriving olmadan dijital kaydı kullanılabilir dinamik aralığının en çok kullanılan düzeyleri ayarlayın. Anlamına sesi yüksek ayarlanmış ancak onun sesli olmayan şekilde bozulur. İyi bir kayıt dalga örneği aşağıdaki görüntüde gösterilmiştir:

![İyi kayıt dalga](media/custom-voice/good-recording.png)

Burada, çoğu (yükseklik) aralığının kullanılmaktadır, ancak üst veya alt pencerenin en yüksek en yüksek sayılar sinyal erişmez. Sessizlik kayıtta ince bir yatay çizgi yakın düşük gürültü kat belirten atabilirsiniz. Bu kayıt, kabul edilebilir dinamik aralık ve sinyal/gürültü oranına sahiptir.

Kayıtta doğrudan yüksek kaliteli ses arabirimi veya kullanmakta olduğunuz MIC bağlı olarak bir USB bağlantı noktası üzerinden bilgisayara. Ses zinciri analog için basit tutun: MIC, preamp, ses arabirimi, bilgisayar. Her ikisi de lisans [Avid uzmanı araçlarını](https://www.avid.com/en/pro-tools) ve [Adobe Audition](https://www.adobe.com/products/audition.html) makul bir ücret ödemeden aylık. Bütçenizi çok sıkı olması durumunda, ücretsiz olarak deneyin [Audacity](https://www.audacityteam.org/).

44,1 kHz 16 bit monophonic (CD kalitede) kaydettiğinizden ya da daha iyi. Geçerli durumu-ürünü 48 kHz 24 bit ise donanımınızın da destekler. Aşağı ses 16 kHz 16 bit için özel sesli portala göndermeden önce örnek. Yine de düzenlemeleri gerekli olayda bir yüksek kaliteli özgün kaydı öder.

İdeal olarak, Hizmet Yöneticisi, mühendislik ve beceri rollerinde farklı kişilerin sahip. Bunu yapmanın çalışmayın tüm bunları kendiniz. Bir tabletinizde tek bir kişi hem Direktörü hem de mühendisi olabilir.

### <a name="before-the-session"></a>Oturumdan önce

Studio zaman harcamamak için önce kayıt oturumu aracılığıyla, sesli beceri komut dosyasını çalıştırın. Sesli taçın metni öğrenirken, Bilmediğiniz sözcüklerin söylenişini açıklığa kavuşturacak.

> [!NOTE]
> Çoğu kayıt studios kaydı standına betiklerde elektronik görünümünü sunar. Bu durumda, sınama yayınını notlarınıza doğrudan betiğin belgeye yazın. Yine de not oturumu boyunca, ancak almak için basılı kopya istersiniz. Çoğu mühendisleri sabit kopyasını çok isteyeceksiniz. Ve üçüncü bir kopya bilgisayar kapalı durumda yetenek için yedek olarak yazdırılır. yine de istersiniz.

Kendi ses beceri bir utterance ("operative sözcüğü") vurgulanmış istediğiniz hangi word isteyebilir. Bunlara, belirli bir vurgu olmadan doğal bir okuma istediğinizi söyleyin. Konuşma oluşturulan Vurgu eklenebilmesi için; özgün kaydın parçası olmamalıdır.

Sözcükleri sonuçlanmaz telaffuz edilir beceri yönlendirir. Her sözcüğün betik yazıldığı gibi telaffuz. Ses atlanırsa veya birlikte, rastgele okuma özelliği, yaygın olarak bulunur slurred *bunlar böylece kodda yazılmış sürece*.

|Yazılı metin|İstenmeyen sıradan söylenişi|
|-|-|
|hiçbir zaman size gidip|hiçbir zaman size gidip|
|dört ışıklar vardır.|dört ışıkları üzeresiniz|
|nasıl hava durumu hemen mi|th'nasıl olduğunu ' Bugün hava durumu|
|küçük arkadaşım Merhaba deyin|Örneğin Belgelerim lil hello' friend|

Beceri gereken *değil* kelimeler arasındaki farklı duraklamaları ekleyin. Cümlenin hala doğal olarak, daha küçük bir resmi uyarabilir sırasında akışı. Bu iyi ayrım doğru hale getirmek için uygulama alabilir.

### <a name="the-recording-session"></a>Kayıt oturumu

Kaydı, bir başvuru veya *eşleşme dosyası* tipik bir utterance oturumunun başlangıcında. Bu satırı yinelenecek beceri her sayfanın isteyin veya bunu. Her zaman başvurmak için yeni kayıt karşılaştırın. Bu yöntem, birim, temposu, aralık ve tonlama tutarlı kalmasını beceri yardımcı olur. Bu arada, mühendislik eşleşme dosyasını bir başvuru olarak düzeyleri ve ses genel tutarlılık için kullanabilirsiniz.

Sonra bir kesme veya başka bir günlük kaydı devam edince, eşleşen dosya özellikle önemlidir. Beceri için birkaç kez yürütmek ve bunları her zaman iyi eşleşen kadar yinelemek istersiniz.

Derin neden olan aktivitelere ve bir süre önce her utterance duraklatılıyor almak, beceri koltuk. Birkaç saniye arasında konuşma sessizlik kaydedin. Bunlar, bağlam göz önünde bulundurularak her zaman görünür sözcükleri aynı şekilde telaffuz. "Farklı"kaydından"bir fiil ifade eden isim olarak belirgin olur, kaydedebilir".

Bir iyi beş "odası ton." yakalamak için saniye önce ilk kaydı sessizlik kaydedin Bu yöntem kalan ses kayıtları, dengelemek özel sesli portalı yardımcı olur.

> [!TIP]
> Gerçekten yakalamak için ihtiyacınız olan ses talent, kendi satırların monophonic (kanal tek) kaydı anlamanızı sağlayacak. Ancak stereo olarak kaydederseniz, ikinci kanalı chatter belirli satırlar tartışılması yakalamak için Denetim odasında kaydetmek için kullanabilirsiniz veya alır. Bu izleme, özel sesli portalına karşıya yüklenen sürümü kaldırın.

Yakın ses beceri'nın performans yerdeyseniz dinleyin. İyi ancak doğal diction, doğru telaffuz ve istenmeyen sesleri eksikliği bakıyorsunuz. Bu standartlar sağlamayan bir utterance yeniden kaydetmek için beceri isteyin çekinmeyin.

> [!TIP]
> Çok sayıda konuşma kullanıyorsanız, tek bir utterance sonuç özel sesli üzerinde fark edilebilir bir etkiye sahip olmayabilir. Daha fazla expedient sorunları herhangi bir konuşma bulundurmak, veri kümesinden dışlamak ve nasıl özel sesinizi ettik bkz olabilir. Her zaman Studio'ya geri dönün ve daha sonra kaçırılan örnekleri kaydedin.

Sınav zamanı numarasını not edin veya betiğinizi kodu için her utterance zaman. Kayıt ait meta verileri veya işaret sayfası her utterance işaretlemek için mühendislik isteyin.

Düzenli kesintiler yapın ve sesinizin sesini iyi bir şekilde tutmasına yardımcı olmak için bir içecek sağlayın.

### <a name="after-the-session"></a>Sonra oturumu

Modern kaydı studios bilgisayarlarda çalıştırın. Oturum sonunda, bir veya daha fazla ses dosyaları, bir bant alırsınız. Bu dosyalar, büyük olasılıkla CD kalite (44,1 kHz 16-bit) ya da WAV AIFF biçiminde olması veya daha yüksek. Sık kullanılan ve istenen 48 kHz 24 bit. 96 kHz gibi daha yüksek bir örnekleme oranı genellikle gerekli değildir.

Özel ses portal her sağlanan utterance kendi dosyasında olmasını gerektiriyor. Birden çok konuşma studio tarafından sunulan her bir ses dosyası içerir. Bu nedenle birincil üretim sonrası bölme kayıtlarını ve bunları gönderimi için hazırlamak için bir görevdir. Kayıt mühendisi'ü işaretçileri dosyasına yerleştirilir (veya ayrı işaret sayfası sağlanan) her utterance başladığı belirtmek için.

Notlarınızı tam alan bulmak için istediğiniz ve daha sonra yardımcı programı gibi bir ses kullanmak [Avid uzmanı araçlarını](https://www.avid.com/en/pro-tools), [Adobe Audition](https://www.adobe.com/products/audition.html), ya da ücretsiz [Audacity](https://www.audacityteam.org/), her kopyalamak için Yeni bir dosyaya utterance.

Sessizlik başlangıcına ve sonuna ilk dışında her küçük yalnızca yaklaşık 0.2 saniyelik bırakın. Bu dosyanın tam beş saniye sessizlik başlamanız gerekir. Dosya sessiz bölümlerine "sıfır çıkış" bir ses düzenleyiciyi kullanmaz. "Odası sesi" gibi algoritmalar için tüm kalan arka plan gürültüsü dengelemek özel sesli yardımcı olur.

Her dosya için dikkatli bir şekilde dinler. Bu aşamada bir satırından önce bir hafif LIP smack gibi kayıt sırasında kaçırdığınıza küçük istenmeyen ses çıkış düzenleyebilir, ancak herhangi bir gerçek konuşma kaldırmamanız dikkatli olun. Bir dosya giderilemezse, veri kümesinden kaldırmak ve bunu gerçekleştirmiş dikkat edin.

Her dosyayı kaydetmeden önce 16 bit ve bir örnek hızı 16 kHz dönüştürün ve studio chatter kaydettiyse, ikinci kanalı kaldırın. WAV biçimde betiğinizi utterance numarası dosyalarını adlandırma, her bir dosyayı kaydedin.

Son olarak, oluşturma *döküm* , her bir WAV dosyası karşılık gelen utterance metin sürümü ile ilişkilendirir. [Özel ses tipi oluşturma](how-to-customize-voice-font.md) gerekli biçime ayrıntılarını içerir. Doğrudan komut dosyasından metin kopyalayabilirsiniz. WAV dosyalarını ve metin dökümü bir Zip dosyası oluşturun.

Güvenli bir yerde özgün kayıtları daha sonra gerektiği durumlarda arşivleme. Betik ve notlar çok korur.

## <a name="next-steps"></a>Sonraki adımlar

Kayıtlarınızı karşıya yüklemek ve özel sesinizi oluşturmak hazır olursunuz.

> [!div class="nextstepaction"]
> [Özel ses tipi olarak oluşturma](how-to-customize-voice-font.md)
