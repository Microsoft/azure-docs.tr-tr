---
title: Kaba yeniden yerelleştirme
description: Kaba yeniden yerelleştirmenin nasıl ve ne zaman kullanılacağını öğrenin. Kaba yeniden yerelleştirme size yakın olan bağlantıları bulmanıza yardımcı olur.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: d2737f58fa95d1aa45d9952e8b501c1b9be4d1b0
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600361"
---
# <a name="coarse-relocalization"></a>Kaba yeniden yerelleştirme

Kaba yeniden yerelleştirme, bu sorulara yaklaşık ancak hızlı yanıt vererek büyük ölçekli yerelleştirme sağlayan bir özelliktir: 
- *Cihazım şimdi nerede?* 
- *Hangi içerikleri gözlenmem gerekir?* 
 
Yanıt kesin değil. Şu biçimdedir: *Bu Tutturucuların yakınlıyız. Bunlardan birini bulmayı deneyin*.

Kaba yeniden yerelleştirme, bağlantıları daha sonra hızlı sorgulama için kullanılan çeşitli cihaz algılayıcı okumaları ile etiketleyerek işe yarar. Açık Hava senaryolarında, algılayıcı verileri genellikle cihazın GPS (Global Konumlandırma Sistemi) konumudur. GPS 'nin kullanılamadığı veya güvenilmez olduğu gibi, algılayıcı verileri, Aralık içinde Wi-Fi erişim noktalarından ve Bluetooth işaretleriyle oluşur. Toplanan algılayıcı verileri, hangi bağlayıcıların cihazınıza yakın olduğunu hızlıca öğrenmek için Azure uzamsal bağlantıları tarafından kullanılan bir uzamsal dizinin sürdürülmesi için katkıda bulunur.

## <a name="when-to-use-coarse-relocalization"></a>Ne zaman kaba yeniden yerelleştirme kullanılacağı

Tenis mahkemesinden daha büyük bir alanda 35 ' den fazla uzamsal tutturulmaya planlandıysanız, büyük olasılıkla daha fazla yerelleştirme, uzamsal dizin oluşturma avantajlarından faydalanabilirsiniz.

Kaba reyerelleştirme tarafından etkinleştirilen bağlayıcıların hızlı bir şekilde aranması, dünya ölçeğinde, yani milyonlarca coğrafi olarak dağıtılmış bağlayıcı koleksiyonları tarafından desteklenen uygulamaların geliştirilmesini kolaylaştırmak için tasarlanmıştır. Uzamsal dizin oluşturmanın karmaşıklığı tamamen gizlidir, bu sayede uygulama mantığınıza odaklanırsınız. Tüm zor işler, arka planda Azure uzamsal bağlantılarına göre yapılır.

## <a name="using-coarse-relocalization"></a>Kaba yeniden yerelleştirme kullanma

Aşağıda, büyük bir yeniden yerelleştirme ile Azure uzamsal çıpası oluşturup sorgulamak için tipik iş akışı verilmiştir:
1.  İstediğiniz algılayıcı verilerini toplamak için bir algılayıcı parmak izi sağlayıcısı oluşturun ve yapılandırın.
2.  Bir Azure uzamsal bağlayıcı oturumu başlatın ve bağlantıları oluşturun. Algılayıcı parmak izi etkin olduğundan, bağlantılar kaba bir yeniden yerelleştirme tarafından dağınık şekilde dizinlenir.
3.  Uzamsal bağlayıcıların oturumunda ayrılmış arama ölçütleri aracılığıyla kaba yeniden yerelleştirme kullanarak çevreleyen bağlantıları sorgulayın.

Uygulamanızda daha kaba bir yeniden yerelleştirme ayarlamak için bu öğreticilerden birine başvurabilirsiniz:
* [Unity 'de kaba yeniden yerelleştirme](../how-tos/set-up-coarse-reloc-unity.md)
* [Amaç için kaba yeniden yerelleştirme-C](../how-tos/set-up-coarse-reloc-objc.md)
* [Swift 'ta kaba yeniden yerelleştirme](../how-tos/set-up-coarse-reloc-swift.md)
* [Java 'da kaba yeniden yerelleştirme](../how-tos/set-up-coarse-reloc-java.md)
* [C++ ' da kaba yeniden yerelleştirme/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)
* [C++/Wınrt 'de kaba yeniden yerelleştirme](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

## <a name="sensors-and-platforms"></a>Algılayıcılar ve platformlar

### <a name="platform-availability"></a>Platform kullanılabilirliği

Bu algılayıcı verisi türlerini, bağlantı hizmetine gönderebilirsiniz:

* GPS konumu: enlem, Boylam, yükseklik
* Aralıktaki Wi-Fi erişim noktalarının sinyal gücü
* Aralıktaki Bluetooth işaretlerinin sinyal gücü

Bu tablo, desteklenen platformlarda algılayıcı verilerinin kullanılabilirliğini özetler ve farkında olmanız gereken bilgileri sağlar:

|                 | HoloLens | Android | iOS |
|-----------------|----------|---------|-----|
| **YUVASı**         | Hayır<sup>1</sup>  | Evet<sup>2</sup> | Evet<sup>3</sup> |
| **Wi-Fi**        | Evet<sup>4</sup> | Evet<sup>5</sup> | No  |
| **BLO işaretleri** | Evet<sup>6</sup> | Evet<sup>6</sup> | Evet<sup>6</sup>|


<sup>1</sup> BIR dış GPS cihazı, Hololens ile ilişkilendirilebilir. Bir GPS izleyici ile HoloLens kullanmak istiyorsanız [desteğimize](../spatial-anchor-support.md) başvurun.<br/>
<sup>2</sup> , [LocationManager][3] API 'LERI (GPS ve ağ) aracılığıyla desteklenir.<br/>
<sup>3</sup> [cllocationmanager][4] API 'leri aracılığıyla desteklenir.<br/>
<sup>4</sup> her 3 saniyede bir taramanın yaklaşık bir hızda desteklenir. <br/>
<sup>5</sup> API düzeyi 28 ile başlayarak, Wi-Fi taramalar her 2 dakikada dört çağrı ile kısıtlanır. Android 10 ' dan itibaren, bu azaltmayı **Geliştirici ayarları** menüsünden devre dışı bırakabilirsiniz. Daha fazla bilgi için bkz. [Android belgeleri][5].<br/>
<sup>6</sup> , [Eddystone][1] ve [ıişaret][2]ile sınırlıdır.

### <a name="which-sensor-to-enable"></a>Etkinleştirilecek algılayıcı

Algılayıcı seçimi, geliştirmekte olduğunuz uygulamaya ve platforma bağlıdır.
Bu diyagram, yerelleştirme senaryosuna bağlı olarak hangi sensörler birleşimini etkinleştirebileceğiniz belirlemek için bir başlangıç noktası sağlar:

![Çeşitli senaryolar için etkin algılayıcıları gösteren diyagram.](media/coarse-relocalization-enabling-sensors.png)

Aşağıdaki bölümler, her bir algılayıcı türünün avantajları ve sınırlamaları hakkında daha fazla öngörü sağlar.

### <a name="gps"></a>YUVASı

GPS, dış mekan senaryolar için go seçeneğidir.
Uygulamanızda GPS kullandığınızda, donanım tarafından sunulan okumalar genellikle şunları göz önünde bulundurun:

* Zaman uyumsuz ve düşük sıklık (1 Hz 'den az).
* Güvenilir olmayan/gürültülü (ortalama, 7-a standart sapma).

Genel olarak, hem cihaz işletim sistemi hem de uzamsal bağlantılar, bu sorunları azaltmak için ham GPS sinyalinin bazı filtrelenmesini ve dışlandırmayı yapar. Bu ek işleme yakınsama için zaman gerektirir, bu nedenle en iyi sonuçlar için şunu deneyin:

* Uygulamanızda mümkün olduğunca erken bir algılayıcı parmak izi sağlayıcısı oluşturun.
* Algılayıcı parmak izi sağlayıcısını birden çok oturum arasında canlı tutun.
* Algılayıcı parmak izi sağlayıcısını birden çok oturum arasında paylaşma.

Tüketici sınıfı GPS cihazları genellikle kesin olarak kullanılır. [Zandenbergen ve Barbeau][6] tarafından yapılan bir inceleme (2011), Yardımlı GPS (A-GPS) içeren cep telefonlarına ilişkin ortanca doğruluğun yaklaşık 7 ölçüm olduğunu bildiriyor. Bu, yok sayılacak büyük bir değer! Bu ölçüm hatalarına yönelik hesaba geçmek için hizmet, bir yere GPS alanı içinde olasılık dağıtımları olarak davranır. Bu nedenle, bir tutturucu, büyük olasılıkla (%95 ' den fazla güven), gerçek, bilinmeyen GPS konumunu içerdiğinden alanın bölgesidir.

Aynı düşünme, GPS kullanarak sorgulama yaptığınızda geçerlidir. Cihaz, doğru, bilinmeyen GPS konumu etrafında başka bir uzamsal güven bölgesi olarak temsil edilir. Yakın olan Tutturucuların bulunması, burada gösterildiği gibi, güvenilir bölgelere sahip olan bağlantıları cihazın güven bölgesine *yeterince yakın* bir şekilde bulmak için çeviri yapar:

![GPS kullanarak bağlantı adaylarını bulmayı gösteren diyagram.](media/coarse-reloc-gps-separation-distance.png)

### <a name="wi-fi"></a>Wi-Fi

HoloLens ve Android 'de Wi-Fi sinyal gücü, yoğun bir şekilde yeniden yerelleştirmeyi etkinleştirmenin iyi bir yolu olabilir.
Avantaj, ek kurulum gerektirmeyen Wi-Fi erişim noktalarının (örneğin, ofis alanları ve alışveriş mallarında ortak) potansiyel olarak kullanılabilirliğinden yararlanır.

> [!NOTE]
> iOS Wi-Fi sinyal gücünü okumak için bir API sağlamaz, bu nedenle Wi-Fi aracılığıyla etkinleştirilen kaba yeniden yerelleştirme için kullanılamaz.

Uygulamanızda Wi-Fi kullandığınızda, donanım tarafından sunulan okumalar genellikle şunları göz önünde bulundurun:

* Zaman uyumsuz ve düşük sıklık (0,1 Hz 'den az).
* İşletim sistemi düzeyinde kısıtlanmış olabilir.
* Güvenilmez/gürültülü (ortalama, 3-dBm standart sapma).

Uzamsal bağlantılar, bu sorunları azaltmak için bir denemede bir oturum sırasında Wi-Fi sinyal gücünün filtrelenmiş bir haritasını oluşturmaya çalışır. En iyi sonuçlar için şunu deneyin:

* İlk tutturucu yerleştirmadan önce oturum iyi bir şekilde oluşturun.
* Oturumu mümkün olduğunca uzun tutun. (Diğer bir deyişle, tek bir oturumda tüm tutturucuları ve sorgu oluşturun.)

### <a name="bluetooth-beacons"></a>Bluetooth işaretleri
<a name="beaconsDetails"></a>

Bluetooth işaretlerinin dikkatli bir şekilde dağıtılması, GPS 'nin eksik veya yanlış olduğu büyük ölçekli, daha iyi yerelleştirme senaryolarına yönelik iyi bir çözümdür. Ayrıca, üç platformda da desteklenen tek ınkapılı yöntemidir.

İşaretlerin, genellikle UUID 'ler ve MAC adresleri dahil olmak üzere her şeyin yapılandırılabileceği çok yönlü cihazlardır. Azure uzamsal bağlantıları, işaretlerinin UUID 'ler tarafından benzersiz şekilde tanımlanmasını bekler. Bu benzersizlik konusunda emin değilseniz muhtemelen yanlış sonuçlar elde edersiniz. En iyi sonuçlar için:

* İşaretlerine benzersiz UUID 'ler atayın.
* İşaretleri, alanınızı bir arada ele alan bir şekilde dağıtın ve alan içindeki herhangi bir noktadan en az üç işaret erişilebilir.
* Benzersiz işaret UUID 'ler listesini algılayıcı parmak izi sağlayıcısına geçirin.

Bluetooth gibi radyo sinyalleri, belirlenen engelleri aşmak tarafından etkilendi ve diğer radyo sinyalleriyle karışabilir. Bu nedenle, alanın tek bir şekilde ele alınmadığını tahmin etmek zor olabilir. Daha iyi bir müşteri deneyimi sağlamak için, işaretlerinin kapsamını el ile test etmenizi öneririz. Bir testi, aday cihazlarıyla alanınızda dolaşarak ve Bluetooth 'u aralıklı olarak gösteren bir uygulamayla yürüyerek gerçekleştirebilirsiniz. Kapsamı test ederken, alanınızda herhangi bir stratejik konumdan en az üç işareti ulaşabildiğinizden emin olun. Çok fazla sayıda işareti olması bunlar arasında daha fazla girişim oluşmasına neden olabilir ve bu da kaba yeniden yerelleştirme doğruluğunu iyileştirmez.

Alanda hiçbir koruyucu yoksa Bluetooth işaretleri genellikle 80 ölçü kapsar.
Bu nedenle, büyük bir obgrafik içermeyen bir alan için, her 40 ölçümle bir kılavuz düzeninde işaretlerini dağıtabilirsiniz.

Pilin tükenildiği bir işaret sonuçları etkiler, bu nedenle dağıtımınızı düşük veya ücretlendirilebilen piller için düzenli aralıklarla izlemenin doğru olduğundan emin olun.

Azure uzamsal bağlantıları, yalnızca bilinen işaret yakınlık UUID 'ler listesindeki Bluetooth işaretlerini izler. Ancak allowlistelenmiş UUID 'ler ile programlanmış kötü amaçlı işaretleri hizmetin kalitesini olumsuz etkileyebilir. Bu nedenle, işaret dağıtımını denetleyebileceğiniz, seçkin boşlukların en iyi sonucunu elde edersiniz.

### <a name="sensor-accuracy"></a>Algılayıcı doğruluğu

Her ikisi de bağlayıcı oluşturma sırasında ve sorgular sırasında GPS sinyalinin doğruluğu, döndürülen bağlayıcı kümesinde önemli bir etkiye sahiptir. Buna karşılık, Wi-Fi/işaretlerine dayanan sorgular, sorgu ile ortak olarak en az bir erişim noktası/işaret olan tüm bağlantıları kabul eder. Bu anlamda, Wi-Fi/işaret tabanlı bir sorgunun sonucu genellikle erişim noktalarının/işaretlerinin ve çevre engellerin fiziksel aralığına göre belirlenir.
Bu tabloda her algılayıcı türü için beklenen arama alanı tahmin edilecek:

| Algılayıcısı      | Arama alanı yarıçapı (yaklaşık) | Ayrıntılar |
|-------------|:-------:|---------|
| **YUVASı**         | 20 milyon ila 30 milyon | Diğer faktörlerin yanı sıra GPS belirsizlik tarafından belirlenir. Raporlanan sayılar,-GPS: 7 ölçüm ile cep telefonlarında ortanca GPS doğruluğu için tahmin edilir. |
| **Wi-Fi**        | 50 milyon-100 milyon | Kablosuz erişim noktalarının aralığına göre belirlenir. Sıklık, verici kuvveti, fiziksel engelleri, girişim vb. bağlıdır. |
| **BLO işaretleri** |  70 milyon | İşaret aralığına göre belirlenir. Sıklık, iletim gücüne, fiziksel engelleri, girişim vb. bağlıdır. |

<!-- Reference links in article -->
[1]: https://developer.estimote.com/eddystone/
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
