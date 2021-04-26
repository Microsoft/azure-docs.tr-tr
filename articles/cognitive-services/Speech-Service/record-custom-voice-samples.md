---
title: Özel ses örneklerini kaydet-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Sağlam bir betiği hazırlarken, iyi bir ses tasete ve profesyonel olarak kaydederek üretim kalitesinde özel bir ses oluşturun.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: erhopf
ms.openlocfilehash: 6d2ad87f6a8c79f61d62140a1549968cd88d83aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "101722322"
---
# <a name="record-voice-samples-to-create-a-custom-voice"></a>Özel bir ses oluşturmak için ses örneklerini kaydetme

Sıfırdan yüksek kaliteli bir üretim özel sesi oluşturmak, rastgele bir değer alma değildir. Özel bir sesin merkezi bileşeni, insan konuşmadan oluşan büyük bir ses örnekleri koleksiyonudur. Bu ses kayıtlarının yüksek kaliteli olması çok önemlidir. Bu tür kayıtları oluşturan deneyime sahip bir ses taçını seçin ve profesyonel ekipman kullanan bir uzman Kayıt mühendisine göre kaydedilmesini sağlayabilirsiniz.

Bu kayıtları yapabilmeniz için önce bir betiğinizin olması gerekir. ses örnekleri oluşturmak için ses tasemi tarafından söylenen sözcükler. En iyi sonuçları elde etmek için, betiğinizin, özel ses modelini eğitebilmeniz için iyi bir fonetik kapsama sahip olması ve yeterli

Birçok küçük ancak önemli ayrıntıların profesyonel bir ses kaydı oluşturma bölümüne bakın. Bu kılavuz, iyi, tutarlı sonuçlar almanıza yardımcı olacak bir süreç için yol haritası.

> [!NOTE]
> Bir sinir sesi eğmek isterseniz, özel bir ses modeli eğmek için kendi konuşma verilerini kullanma hakkında daha fazla ses onay dosyası içeren bir ses tatatsyon profili belirtmeniz gerekir. Kayıt betiğinizi hazırlarken aşağıdaki tümceyi eklediğinizden emin olun. 

> "I [soyadınız ve soyadınız], sesimin yapay bir sürümünü oluşturmak ve kullanmak için sesimin kayıtlarının [Şirket adı olarak durum] tarafından kullanılacağını biliyor."
Bu cümle, eğitim verilerinin onay yapan kişi tarafından gerçekleştirilip yapılkullanılmadığını doğrulamak için kullanılacaktır. [Sesli TATATK doğrulaması](/legal/cognitive-services/speech-service/custom-neural-voice/data-privacy-security-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) hakkında daha fazla bilgi edinmek için buraya bakın.

> Özel sinir Voice sınırlı erişimle kullanılabilir. [Sorumlu AI gereksinimlerini](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) anladığınızdan emin olun ve [erişimi buraya uygulayın](https://aka.ms/customneural). 

> [!TIP]
> En yüksek kaliteli sonuçlar için, özel sesinizi geliştirmeye yardımcı olmak üzere Microsoft 'a göz önünde bulundurun. Microsoft, Cortana ve Office dahil olmak üzere kendi ürünleri için yüksek kaliteli sesler üreten kapsamlı bir deneyimle sahiptir.

## <a name="voice-recording-roles"></a>Ses kayıt rolleri

Özel bir ses kayıt projesinde dört temel rol vardır:

Rol|Amaç
-|-
Ses tatatmi        |Bu kişinin sesi özel sesin temelini oluşturacak.
Kayıt mühendisi  |Kaydın teknik yönlerini aşırı görür ve kayıt donanımını çalıştırır.
Ktörü            |Betiği hazırlar ve ses taödünç'nin performansını Coaches.
Düzenleyici              |Ses dosyalarını sonlandırır ve bunları özel sesli portala yüklemeye hazırlar.

Bireysel bir rol, birden fazla rol doldurabilir. Bu kılavuzda öncelikle Direktörü rolünü doldurduğunuzu ve hem ses hem de bir kayıt mühendisinin nasıl desteklendirilecektir. Kayıtları kendiniz yapmak istiyorsanız, bu makale kayıt mühendisi rolüyle ilgili bazı bilgiler içerir. Oturum açana kadar düzenleyici rolü gerekli değildir, bu nedenle yönetmen veya kayıt mühendisi tarafından gerçekleştirilebilir.

## <a name="choose-your-voice-talent"></a>Ses tatatinizi seçin

VoiceOver veya ses karakterinde deneyim içeren aktörler, iyi bir özel ses tatatçinin Ayrıca, announcers ve gazeteçleri arasında genellikle uygun bir etatçi bulabilirsiniz.

Doğal sesini istediğiniz sesli tatatyi seçin. Benzersiz "karakter" seslerin oluşturulması mümkündür, ancak bunları tutarlı bir şekilde gerçekleştirmek çok daha zordur ve bu da çaba ses zorlamasına neden olabilir.

> [!TIP]
> Genel olarak, özel bir ses oluşturmak için tanınabilir sesler kullanmaktan kaçının — kuşkusuz, amacınız bir ünlüme sesi oluşturmak için kullanılır. Daha az bilinen sesler genellikle kullanıcılara daha az dikkat dağıtıcı.

Ses tatatçini seçmeye yönelik en önemli faktör tutarlılığa sahiptir. Kayıtlarınızın hepsi aynı odada aynı günde yapılmış gibi tüm seslerle aynı olmalıdır. Bu ideal olarak, iyi kayıt uygulamaları ve mühendisler aracılığıyla yaklaşımda bulabilirsiniz.

Sesiniz, denklemin diğer yarısıdır. Tutarlı hız, birim düzeyi, sıklık ve ton ile konuşabilmeleri gerekir. Açık sözlük bir olmalıdır. Ayrıca, taçüsyon 'nın, kendi Aralık çeşitlemesini, dipersel etkileri ve konuşma maniz MS 'leri tam olarak denetleyebilmeleri gerekir.

Özel ses örneklerini kaydetmek, diğer ses çalışmatürlerinden daha fazla bilgi alabilir. Çoğu ses tatatsi günde iki veya üç saat için kayıt yapabilir. Mümkün olduğunca bir güne kadar, oturumları üç veya dört haftada bir olacak şekilde sınırlayın.

Özel sesin genel sesini ve tek bir tonu tanımlayan bir "kişi" geliştirmek için sesinizle birlikte çalışın. İşlemde, bu kişi için "nötr" seslerin ne olduğunu bulacağız. Özel sinir ses özelliğini kullanarak, emomlar ile konuşacak bir model eğitebilirsiniz. "Konuşma stillerini" tanımlayın ve sesli tatatdinizin komut dosyasını istediğiniz stillerin bulunduğu bir şekilde okumasını isteyin.  

Bir kişi, örneğin doğal olarak bir kişilik olabilir. Bu nedenle "onların" sesi, doğru bir şekilde konuşduklarında bile optimize edilebilir bir şekilde bir nottur. Ancak, bu tür bir kişilik nitelik hafif ve tutarlı olmalıdır. Ne kadar iyi bir fikir sahibi olduğunuzu görmek için mevcut seslere göre okumaları dinleyin.

> [!TIP]
> Genellikle, yaptığınız ses kayıtlarına sahip olmak isteyeceksiniz. Ses tatatkinizin, proje için bir işe yönelik iş için bir işe yönelik bir sözleşme yapabilmesi gerekir.

## <a name="create-a-script"></a>Betik oluşturma

Herhangi bir özel ses kaydı oturumunun başlangıç noktası, sesli tasesen tarafından söylenen noktaları içeren betiktir. ("Utterslar" terimi hem tam cümleleri hem de daha kısa tümceleri kapsar.)

Betiğinizdeki söyleme, her yerden gelebilir: kurgu, kurgu, yöneticileriyle, haber raporlarının dökümü ve yazdırılmış biçimde başka herhangi bir şey olabilir. Sesinizin belirli sözcük türlerine (tıbbi terminoloji veya jargon 'u programlama gibi) uygun olduğundan emin olmak istiyorsanız yapmak teler veya teknik belgelerden cümleler dahil etmek isteyebilirsiniz. Olası yasal sorunlar hakkında kısa bir açıklama için, ["legalities"](#legalities) bölümüne bakın. Kendi metninizi de yazabilirsiniz.

Aradıklarınızın aynı kaynaktan gelmesi veya aynı kaynak türüyle olması gerekmez. Bunlar, birbirleriyle aynı şey yapmamaları bile gerekmez. Ancak, konuşma uygulamanızda, tümceleri ayarla (örneğin, "başarıyla oturum açtınız") seçeneğini kullanacaksanız, bunları betiğe eklediğinizden emin olun. Bu, özel sesinize pronouncing bu tümceciklerin iyi bir olasılığını sağlar. Birleştirilmiş konuşma yerine bir kayıt kullanmaya karar vermeniz gerekiyorsa, zaten aynı sesle sahip olacaksınız.

Tutarlılık, sesli tatatçın seçilmesi durumunda önemli olsa da, iyi bir betiğin Hallmark. Betiğinizin çeşitli tümce uzunluklarına, yapılarına ve Moods 'e sahip birçok farklı sözcük ve cümle içermesi gerekir. Dildeki her ses birden çok kez ve çeşitli bağlamlarda ( *Fonetik kapsam* olarak adlandırılır) temsil edilmelidir.

Ayrıca, metin, belirli bir sesin yazma sırasında temsil edilebilmesi için tüm yolları içermelidir ve her bir sesi cümlelerde farklı yerlere yerleştirebilir. Bildirim temelli cümleler ve soruların her ikisi de uygun bir şekilde dahil edilmelidir ve okunmalıdır.

Özel Konuşma Tanıma portalının iyi bir ses oluşturmasına izin vermek için *yeterli* veri sağlayan bir betik yazmak zordur. Uygulamada, güçlü fonetik kapsama elde eden bir betiği yapmanın en kolay yolu, çok sayıda örnek içermelidir. Microsoft 'un sağladığı standart sesler onlarca binlerce utüden oluşturulmuştur. Üretim kalitesinde özel bir ses oluşturmak için en az birkaç bin utde en düşük düzeyde kayda hazır olmanız gerekir.

Hatalar için betiği dikkatle denetleyin. Mümkünse, başka birisinin bunu da denetlemesini sağlayabilirsiniz. Tatatlanızla komut dosyası aracılığıyla çalıştırdığınızda birkaç hata daha yakalayacaksınız.

### <a name="script-format"></a>Betik biçimi

Komut dosyanızı Microsoft Word 'e yazabilirsiniz. Betik, kayıt oturumu sırasında kullanım için olduğundan, birlikte çalışmak istediğiniz her türlü şekilde ayarlayabilirsiniz. Özel ses portalı için gereken metin dosyasını ayrı olarak oluşturun.

Temel bir betik biçimi üç sütun içerir:

* 1 ' den başlayarak utterance 'in numarası. Numaralandırma, Studio 'daki herkesin belirli bir utterde ("deneme numarası 356 ' i yeniden deneme) başvurmasını kolaylaştırır. Tablodaki satırları otomatik olarak numaralandırmak için Word paragraf numaralandırma özelliğini kullanabilirsiniz.
* Tamamlandı kaydında bulmanıza yardımcı olmak için her bir utun alma numarasını veya zaman kodunu yazacağınız boş bir sütun.
* Söylenişi 'in kendi metni.

![Örnek betik](media/custom-voice/script.png)

> [!NOTE]
> Kısa kesimlerdeki çoğu Studios kaydı, *alır* olarak bilinir. Her alma, genellikle 10 ila 24 utlik içerir. Alma numarasını daha sonra bulmak için yeterli olduğunu dikkate almak yeterlidir. Daha uzun kayıtlar oluşturmak için tercih edilen bir Studio 'da kayıt yapıyorsanız, bunun yerine zaman kodu ' nu aklınızda bulabilirsiniz. Studio, belirgin bir zaman görüntüsüne sahip olacaktır.

Not yazmak için her satırdan sonra yeterli boş alan bırakın. Sayfalar arasında herhangi bir söylenme olmadığından emin olun. Sayfaları Sayın ve komut dosyanızı kağıdın bir tarafında yazdırın.

Betiğinin üç kopyasını yazdır: biri mühendisin, biri mühendis için ve diğeri de yönetmen (siz) için. Zımba yerine bir kağıt klibi kullan: deneyimli bir ses sanatçısı sayfaları, sayfalar kapalıyken gürültü yapmaktan kaçınmak üzere ayırır.

### <a name="legalities"></a>Legalities

Telif hakkı yasaları kapsamında, bir aktörün, telif haklı metinlerin okunması, işin yazarının dengelemeyeceği bir performans olabilir. Bu performans, son üründe özel ses olarak tanınmayacak. Bu nedenle bile, bu amaçla telif haklı bir işi kullanmanın yasallığı iyi bir şekilde kurulmaz. Microsoft bu sorun hakkında yasal bir öneri sağlayamaz; kendi sayınıza başvurun.

Neyse ki, bu sorunlardan tamamen kaçınmak mümkündür. İzin veya lisans olmadan kullanabileceğiniz birçok metin kaynağı vardır.

|Metin kaynağı|Description|
|-|-|
|[CMU arctik yapı](http://festvox.org/cmu_arctic/)|Telif hakkı dışında çalışarak, özellikle konuşma seniş projelerinde kullanılmak üzere 1100 tümce hakkında. Harika bir başlangıç noktası.|
|Artık işe yarar<br>Telif hakkı altında|Genellikle 1923 ' dan önce yayımlanır. Ingilizce için [Project Gutenberg](https://www.gutenberg.org/) , bu tür binlerce çalışma sunar. Dil modern Ingilizce 'ye yakın olacağı için daha yeni bir işe odaklanmak isteyebilirsiniz.|
|Kamu &nbsp; çalışmaları|Kamu, Birleşik Devletler kamu tarafından oluşturulan çalışmalar Birleşik Devletler tarafından desteklenmiyor, ancak kamu, diğer ülkelerde/bölgelerde telif hakkı talep edebilir.|
|Ortak etki alanı|Bu, açıkça veya genel etki alanı için ayrılmış olan bir telif hakkı için geçerlidir. Telif hakkını tamamen bazı dairede almak mümkün olmayabilir.|
|Yüksek oranda lisanslanmış çalışmalar|Creative Commons veya GNU ücretsiz belge lisansı (GFDL) gibi bir lisans altında dağıtılır. Vikipedi, GFDL kullanır. Ancak bazı lisanslar, özel bir ses modelinin oluşturulmasını etkileyebilecek lisanslı içeriğin performansına ilişkin kısıtlamalar verebilir, bu nedenle lisansı dikkatle okuyun.|

## <a name="recording-your-script"></a>Betiğinizi kaydetme

Betiğinizi, ses işlerinde uzmanlaşmış bir profesyonel Kayıt Studio 'ya kaydedin. Bir kayıt stand, doğru ekipman ve bunu çalıştırmak için doğru kişiler olacaktır. Kayıt sırasında skıop 'a ödeme yapar.

Projeyi, Studio 'nun kayıt mühendisiyle tartışın ve bunların ihbarını dinleyin. Kayıt, dinamik bir Aralık sıkıştırması (en fazla 4:1) içermelidir. Ses, istenmeyen seslerin serbest bırakılmakta olduğu kadar tutarlı hacimde ve yüksek sinyalten gürültü oranına sahip olmak önemlidir.

### <a name="do-it-yourself"></a>Kendiniz yapın

Kaydı bir kayıt Studio 'ya gitmek yerine kendiniz yapmak istiyorsanız, kısa bir öncü aşağıda verilmiştir. Giriş kaydı ve pod atama için teşekkürler, çevrimiçi olarak doğru kayıt önerisi ve kaynakları bulmayı her zamankinden daha kolay.

"Kayıt stand", dikkat çekici yankı veya "Oda tonu" olmadan küçük bir oda olmalıdır. Mümkün olduğunca sessiz ve soundkanıt olmalıdır. Duvarlardaki draçler, Yankıyı azaltmak veya odanın sesini düşürmek veya "ölümi" bırakmak için kullanılabilir.

Ses kaydı için tasarlanan yüksek kaliteli bir Studio (kısaca "Mic") mikrofonunu kullanın. Sennheiser, AKG, hatta daha yeni yakınlaştırma, iyi sonuçlar elde edebilir. Bir mikrofon satın alabilir veya yerel bir ses-görsel Kiralık firması üzerinden kiralayabilirsiniz. Bir USB arabirimiyle bir tane arayın. Bu tür bir MIC, mikrofon öğesi, ön kimlik MP ve analog-dijital dönüştürücüyü tek bir pakette birleştirerek, kancayı basitleştirir.

Örneksel bir mikrofon da kullanabilirsiniz. Birçok Kiralama, sesli karakterine sahip "Vini" mikrofonlarını sunmaktadır. Profesyonel analog dişli 'ın, Tüketici donanımı içinde kullanılan 1/4 inç bir eklenti yerine dengeli XLR bağlayıcıları kullandığını unutmayın. Analog 'a giderseniz, bu bağlayıcılara sahip bir ön kimlik MP ve bir bilgisayar ses arabirimine da ihtiyacınız olacaktır.

Mikrofonu bir stand veya Boom üzerine yükledikten sonra "plosive" ünsüzler "p" ve "b" gibi paraziti ortadan kaldırmak için mikrofonun önüne bir pop filtresi yüklersiniz. Bazı mikrofonlar, bu, yararlı olan her durumda, bunları stand içindeki vintlerden yalıtan bir askıya alma bağlaması ile gelir.

Ses tatatsıya mikrofondan tutarlı bir uzaklıktan haberdar olmalıdır. Banttaki bandı, tek yapmanız gereken yerleri işaretlemek için kullanın. Tatatçinin oturmasını tercih ediyorsa, MIC mesafesini izlemek ve sandalye gürültüsünü önlemek için özel bir işlem yapın.

Betiği tutmak için bir stand kullanın. Mikrofonu mikrofona doğru yansıtması için tek bir insandan kaçının.

Kayıt donanımını çalışan kişi (mühendis), kayıt standardından (bir *TalkBack devresi)* tatatçiyle konuşabilmek için bir yol ile tatathı 'den ayrı bir odada olmalıdır.

Kayıt, 80-DB sinyalden gürültü oranına veya daha iyi bir amaca sahip olabilecek olabildiğince az gürültü içermelidir.

"Stand" içinde bir sessizlik kaydına yakın bir şekilde dinleme yapın ve nedeni ortadan kaldırın. Yaygın gürültü kaynakları hava uygulamaları, akıcı oresan ışığı, yakındaki yolların trafiği ve ekipman fanı (hatta dizüstü bilgisayarların fanları olabilir). Mikrofonlar ve kablolar, yakın bir AC kablolarından (genellikle bir Hum veya Buzz) elektrik gürültüsü alabilir. Bir baz, donatımın birden fazla elektrik devresine takılmasından kaynaklanan bir *taban döngüsü* nedeniyle de oluşabilir.

> [!TIP]
> Bazı durumlarda, bir Equalizer veya bir gürültü azaltma yazılımı eklentisini, kendi kaynağında durmak her zaman en iyi şekilde durdurmak üzere kayıtınızdan paraziti kaldırmaya yardımcı olabilir.

Düzeyleri, kullanılabilir dinamik dijital kayıt aralığının büyük bir temeli olmadan kullanılması için ayarlayın. Diğer bir deyişle, Ses yüksek olarak ayarlanır, ancak bu kadar yüksek hale gelir. Aşağıdaki görüntüde, iyi bir kaydın Waveform örneğine bir örnek gösterilmektedir:

![Düzgün bir kayıt Waveform](media/custom-voice/good-recording.png)

Burada Aralık (yükseklik) kullanılır, ancak sinyalin en yüksek tepesine pencerenin üst veya alt kısmına ulaşmaz. Ayrıca, kayıt içindeki sessizlik, düşük bir gürültü olduğunu belirten ince yatay bir çizgiye yaklaştırır. Bu kayıtta, kabul edilebilir Dinamik Aralık ve sinyal gürültüsü oranı vardır.

Kullanmakta olduğunuz mikrofonuna bağlı olarak, yüksek kaliteli bir ses arabirimi veya USB bağlantı noktası aracılığıyla doğrudan bilgisayara kaydedin. Analog için, ses zincirini basit tutun: MIC, ön kimlik MP, ses arabirimi, bilgisayar. [AVID Pro araçları](https://www.avid.com/en/pro-tools) ve [Adobe sesleme](https://www.adobe.com/products/audition.html) aylık olarak makul bir maliyetle lisans yapabilirsiniz. Bütçenizin son derece sıkı olması durumunda ücretsiz [Aukacity](https://www.audacityteam.org/)'yi deneyin.

44,1 kHz 16 bit monophonic (CD kalitesi) veya daha iyi bir kayıt. Geçerli-son-grafik durumu 48 kHz 24 bit, donanımınızın destekliyorsa bunu destekler. Özel bir ana portala göndermeden önce sesinizi 16 kHz 16 bit olarak örnekleyebilirsiniz. Yine de, olay düzenlemelerinin yüksek kaliteli bir özgün kaydına sahip olmaya ödemesi gerekiyordu.

İdeal olarak, yönetmenin, mühendisin ve taçilerin rollerinde farklı kişilerin sunmasını sağlayabilirsiniz. Bunu kendiniz yapmayı denemeyin. Bir pinç 'de, bir kişi hem yönetmen hem de mühendis olabilir.

### <a name="before-the-session&quot;></a>Oturumdan önce

Stüdyo süresini ortadan kaldırmak için, kayıt oturumundan önce sesli tasele birlikte betiği çalıştırın. Sesli taçın metni öğrenirken, Bilmediğiniz sözcüklerin söylenişini açıklığa kavuşturacak.

> [!NOTE]
> Çoğu kayıt Studios, kayıt standında betiklerin elektronik olarak görüntülenmesini sağlar. Bu durumda, komut dosyası belgesine doğrudan çalıştırma notlarınızı yazın. Yine de bir kağıt kopyasının oturum sırasında not almak isteyeceksiniz, ancak. Birçok mühendisler de sabit bir kopya ister. Bu durumda, bilgisayarın bir daha düşük olması durumunda tatatçine yönelik olarak üçüncü yazdırılmış bir kopyayı de bir yedekleme olarak isteyeceksiniz.

Sesinizin taterli olması, bir tanesinde (&quot;çalışmayan sözcük") vurgulamasını istediğiniz kelimeyi sorabilir. Bunlara, belirli bir vurgu olmadan doğal bir okuma istediğinizi söyleyin. Konuşma sentezleştirilmiştir vurgu eklenebilir; özgün kaydın bir parçası olmamalıdır.

Tatatçinizi pronounce sözcüklere göre ayrı olarak yönlendirin. Betiğin her sözcüğü yazıldığı gibi görünmelidir. Sesler, *betikte bu şekilde yazılmadığı sürece*, sıradan konuşmaya ortak olduğu gibi atlanmamalıdır veya bir arada çıkarlanmamalıdır.

|Yazılı metin|İstenmeyen rastgele telaffuz|
|-|-|
|size hiçbir şekilde izin verme|size hiçbir şekilde izin verme|
|Dört ışık vardır|Dört ışık vardır|
|Hava durumu bugün|Bugün hava durumu|
|Küçük arkadaşlarıma merhaba deyin|LIL 'nin arkadaşım için merhaba deyin|

Taödünçte sözcükler arasında ayrı duraklamalar *eklememelidir.* Cümle, küçük bir resmi aksanız bile doğal olarak akışa alınır. Bu hassas ayrım yapmak, doğru bir deneyim sağlamak için gerekli olacaktır.

### <a name="the-recording-session"></a>Kayıt oturumu

Oturumun başlangıcında tipik bir utterlik için bir başvuru kaydı veya *eşleştirme dosyası* oluşturun. Tatatçisinden bu satırı her sayfada yinelemek için sorun. Her seferinde, yeni kaydı başvurusuyla karşılaştırın. Bu uygulama, birim, tempo, sıklık ve intonation 'da tutarlı olmaya devam eder. Bu arada, mühendis, eşleşme dosyasını düzeyler ve sesin genel tutarlılığı için bir başvuru olarak kullanabilir.

Bir kesmeyi veya başka bir gün sonra kaydetmeyi sürdürürseniz eşleşme dosyası özellikle önemlidir. Bu uygulamayı tatathı için birkaç kez yürütmek isteyeceksiniz ve bu her seferinde iyi eşleşene kadar her seferinde tekrarlamanız gerekir.

Tatatkinizi derin bir şekilde Coach ve her zaman önce bir süre duraklatıp. Söyleyenlerdeki birkaç saniyelik sessizlik kaydeder. Sözcüklerin her göründüğünde aynı şekilde görünmesi gerekir ve bağlam dikkate alınmalıdır. Örneğin, bir fiil olarak "kayıt", bir ad olarak "kayıt" ile farklı şekilde değerlendirilir.

"Oda tonu" yakalamak için ilk kayıttan önce beş saniyelik bir sessizlik kaydedin. Bu uygulama, kayıtlarda kalan tüm gürültü için özel ses portalı telafi sağlanmasına yardımcı olur.

> [!TIP]
> Gerçekten de yakalamanız gereken ses tasedir, bu sayede yalnızca satırlarının monophonic (tek kanallı) kaydını yapabilirsiniz. Ancak, stereo 'da kaydettiğinizde, belirli satırlardan oluşan tartışmayı yakalamak veya almak üzere denetim odasına chatter kaydetmek için ikinci kanalı kullanabilirsiniz. Bu izlemeyi özel ses portalına yüklenen sürümden kaldırın.

Kulaklık kullanarak, ses taödünçinizin performansına yakından dinleyin. Çok iyi, doğal bir sözlük, doğru telaffuz ve istenmeyen seslerin bir eksiği arıyorsunuz. Tatatkinizi, bu standartları karşılamayan bir şekilde yeniden kaydetmek için de sorun yapmayın.

> [!TIP]
> Çok sayıda ifade kullanıyorsanız, tek bir söylenişi, sonuçtaki özel ses üzerinde belirgin bir etkiye sahip olmayabilir. Sorunları gidermek, veri kümesinizden dışlamak ve özel sesinizin nasıl kaplandığına bakmak çok daha fazla olabilir. Her zaman Studio 'ya dönüp eksik örnekleri daha sonra kaydedebilirsiniz.

Her zaman için betiğinizdeki sayı veya zaman kodu alın. Mühendisde kaydın meta veri veya işaret listesi içindeki her bir şekilde işaret etmesini isteyin.

Düzenli kesintiler yapın ve sesinizin sesini iyi bir şekilde tutmasına yardımcı olmak için bir içecek sağlayın.

### <a name="after-the-session"></a>Oturumdan sonra

Bilgisayarlarda modern kayıt Studios çalışır. Oturumun sonunda, bir bant değil bir veya daha fazla ses dosyası alırsınız. Bu dosyalar muhtemelen, CD kalitesinde (44,1 kHz 16 bit) veya daha iyi bir şekilde WAV veya AıFF biçiminde olabilir. 48 kHz 24 bit, yaygın ve tercih edilir. 96 kHz gibi daha yüksek örnekleme ücretleri genellikle gerekli değildir.

Özel ses portalı, her bir belirtilen söylenişi 'in kendi dosyasında olmasını gerektirir. Studio tarafından sunulan her ses dosyası birden çok utde sahiptir. Bu nedenle, birincil üretim sonrası görevi kayıtları bölüp gönderim için hazırlamaktır. Kayıt mühendisi, her bir söylik 'in nerede başladığını göstermek için dosyada işaretçiler (veya ayrı bir ipucu sayfası sağlanmış) yerleştirmiş olabilir.

İstediğiniz kadar uygun olduğunu bulmak için notlarınızı kullanın ve ardından her bir buüteryi yeni bir dosyaya kopyalamak için [AVID Pro araçları](https://www.avid.com/en/pro-tools), [Adobe Audition](https://www.adobe.com/products/audition.html)veya ücretsiz [aunacity](https://www.audacityteam.org/)gibi bir ses düzenlemesi yardımcı programı kullanın.

Birincisi hariç her bir klibin başlangıcında ve sonunda yalnızca yaklaşık 0,2 saniyelik sessizlik olarak bırakın. Bu dosya, tam beş saniyelik sessizlik ile başlamalıdır. Dosyanın sessiz bölümleri için ses Düzenleyicisi kullanmayın. "Oda tonu" de dahil olmak üzere, özel ses algoritmalarının herhangi bir diğer arka plan gürültüsünü denge,

Her dosyayı dikkatle dinleyin. Bu aşamada, kayıt sırasında kaçırılmış küçük ve istenmeyen sesleri, bir satırdan önceki hafif bir LIP smack gibi düzenleyebilir, ancak gerçek konuşmayı kaldırmamaya dikkat edin. Bir dosyayı düzeltemedi, veri kümesinizden kaldırın ve bunu tamamladınız.

Kaydetmeden önce her bir dosyayı 16 bit ve örnek bir 16 kHz değerine dönüştürüp, Studio chatter ' yi kaydettiğinizde ikinci kanalı kaldırın. Dosyaları betiğinizdeki söylenişi numarasıyla adlandırarak, her dosyayı WAV biçiminde kaydedin.

Son olarak, her bir WAV dosyasını karşılık gelen utterance 'in bir metin sürümüyle *ilişkilentiğiniz* döküm oluşturun. [Özel sesler oluşturma](./how-to-custom-voice-create-voice.md) , gerekli biçimin ayrıntılarını içerir. Metni doğrudan betiğinizden kopyalayabilirsiniz. Ardından, WAV dosyalarının ve metin dökümü 'nin bir ZIP dosyasını oluşturun.

Daha sonra ihtiyacınız olması durumunda özgün kayıtları güvenli bir yerde arşivleyin. Betiğinizi ve notlarınızı de koruyun.

## <a name="next-steps"></a>Sonraki adımlar

Kayıtlarınızı karşıya yüklemeye ve özel sesinizi oluşturmaya hazırsınız.

> [!div class="nextstepaction"]
> [Özel ses yazı tipleri oluşturma](./how-to-custom-voice-create-voice.md)