---
title: Azure Stack Edge Mini güvenlik kılavuzu | Microsoft Docs
description: Güvenlik kuralları, yönergeler, hususlar ve Azure Stack Edge Mini R cihazınızı güvenli bir şekilde yüklemeyi ve çalıştırmayı açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: alkohli
ms.openlocfilehash: eb42a9a77927d8577dfec3c9167294eb8f809fec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100382642"
---
# <a name="azure-stack-edge-mini-r-safety-instructions"></a>Azure Stack Edge Mini güvenlik yönergeleri

![Uyarı simgesi 1 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ Okuma Güvenlik BILDIRIMI SIMGESI ](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png)
 **güvenlik ve durum bilgilerini oku**

Azure Stack Edge Mini R cihazınızı, tek bir pil paketinin bir oluşumunu, bir AC/DC takılmış güç kaynağını, bir modül güç bağdaştırıcısını ve bir sunucu modülünü kullanmadan önce bu makaledeki tüm güvenlik bilgilerini okuyun. Yönergeleri izlemeden hata tetiklenmesi, elektrik kesintisi, sakatlama veya özelliklerinizi zarar verebilir. Azure Stack Edge Mini R kullanmadan önce aşağıdaki tüm güvenlik bilgilerini okuyun.

## <a name="safety-icon-conventions"></a>Güvenlik simgesi kuralları

Hasar uyarısı işaretlerine yönelik aşağıdaki sinyal sözcükleri şunlardır:

| Simge | Description |
|:--- |:--- |
| ![Hasar simgesi](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)| **Danger:** Önedilmediği durumlarda, ölüm veya ciddi bir yaralama oluşmasına neden olan tehlikeli bir durum belirtir. <br> **Uyarı:** Önedilmediği durumlarda, ölüm veya ciddi bir yaralama oluşmasına neden olabilecek tehlikeli bir durum belirtir. <br> **Dikkat:** Önedilmediği durumlarda küçük veya orta bir yaralanmaya neden olabilecek tehlikeli bir durum belirtir.|
|

Azure Stack Edge Mini R cihazınızı ayarlarken ve çalıştırırken aşağıdaki rastlantı simgeleri gözlemlenecek:

| Simge | Description |
|:--- |:--- |
| ![Önce tüm yönergeleri okuyun](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png) | Önce tüm yönergeleri okuyun |
| ![Bildirim simgesi ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **bildirimi:** | Önemli kabul edilen, ancak tehlike ile ilgili olmayan bilgileri gösterir. |
| ![Hasar simgesi](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) | Hasar simgesi |
| ![Elektrik sarsıntı simgesi](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) | Elektrik şok hasar |
| ![Yalnızca iç kullanım](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) | Yalnızca iç kullanım |
| ![Kullanıcı Serviceable parçası yok simgesi](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) | Hiçbir Kullanıcı hizmet konusu parçası yok. Uygun şekilde Eğitilmediğiniz müddetçe bu erişimi kullanmayın. |
|

## <a name="handling-precautions-and-site-selection"></a>Önlemler ve site seçimini işleme

Azure Stack Edge Mini R cihazının aşağıdaki işleme önlemleri ve site seçim ölçütleri vardır:

![Uyarı simgesi 2 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ elektrik sarsıntı simgesi ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
 ![ hiçbir Kullanıcı hizmet halinde bölüm simgesi yok ](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) **:**

* Zararlar için *alınan as* cihazını inceleyin. Cihaz kasası hasar görmüşse, yerini almak için [Microsoft desteği başvurun](azure-stack-edge-placeholder.md) . Cihazı çalıştırmayı denemeyin.
* Cihazın arızalı olduğunu kuşkulanıyorsanız, bir değiştirme edinmek için [Microsoft desteği başvurun](azure-stack-edge-placeholder.md) . Cihaza bakım yapmayı denemeyin.
* Cihaz, hiçbir Kullanıcı-serviceable Bölümü içermiyor. Tehlikeli voltaj, geçerli ve enerji düzeyleri içinde mevcuttur. ' İ açmayın. Cihaza bakım için Microsoft 'a döndürün.

![Uyarı simgesi 3 Uyarı ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **:**

Sistemin çalışması önerilir:

* Doğrudan güneş ve kdiators dahil olmak üzere ısı kaynaklarından uzakta.
* Nemi veya yağmur olarak görünmeyen konumlarda.
* Titreşimi ve fiziksel sarsıntı en aza indiren bir alanda bulunur.  Sistem, MIL-STD-810G 'e göre darbe ve titreşim için tasarlanmıştır.
* Elektrik cihazları tarafından üretilen güçlü elektromanyetik alanlardan yalıtılmıştır.
* Herhangi bir likit veya herhangi bir yabancı nesnenin sisteme girmesine izin vermeyin. Meşruları veya herhangi bir diğer likit kapsayıcıyı sisteme veya yanına yerleştirmeyin.

![Uyarı simgesi 4 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ Kullanıcı Serviceable bölümü yok simgesi ](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) **Uyarı:**

* Bu ekipman bir lityum pil içerir. Pil paketini hizmette denemeyin. Bu ekipmandaki piller Kullanıcı serviceable değildir. Pil, yanlış bir türle değiştirilirse açılım riski.

![Uyarı simgesi 5 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Uyarı:**

Yalnızca Azure Stack Edge Mini R cihazının bir parçası olduğunda pil paketini ücretlendirin, ayrı bir cihaz olarak ücretlendirmeyin.

![Uyarı simgesi 6 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **dikkat:**

* Pil paketindeki açık/kapalı anahtarı, pili yalnızca sunucu modülüne borçlandırmayı sağlar. Güç bağdaştırıcısı pil paketine takılıysa, anahtar kapalı konumundayken bile sunucu modülüne güç geçirilir.

![Uyarı simgesi 7 Uyarı ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **:**

* Pil paketini yazma veya kısa devre vermeyin. Geri dönüştürülüp doğru bir şekilde atılmalıdır.

![Uyarı simgesi 8 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Uyarı:**

* Bu sistemde, belirtilen AC/DC güç kaynağını kullanmanın yanı sıra, 2590 pil türünde bir alan kullanma seçeneği de bulunur. Bu durumda, son kullanıcı tüm ilgili güvenliği, ulaşım, çevresel ve diğer ulusal/yerel düzenlemeleri karşıladığını doğrular.
* Sistemi 2590 pil türü ile çalıştırırken, pil üreticisi tarafından belirtilen kullanım koşulları dahilinde pili çalışır.

![Uyarı simgesi 9 Uyarı ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **:**

Bu cihazda iki adet SFP + bağlantı noktası bulunur ve bu, optik alıcı vericiler ile birlikte kullanılabilir. Tehlikeli lazer yarıçapmasını önlemek için yalnızca sınıf 1 alıcı vericileri kullanın.

## <a name="electrical-precautions"></a>Elektrik önlemleri

Azure Stack Edge Mini R cihazının aşağıdaki elektrik önlemleri vardır:

![Uyarı simgesi 10 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) ![ elektrik sarsıntısı simgesi ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) **uyarısı:**

Güç kaynağı bağdaştırıcısı ile birlikte kullanıldığında:

* Güç kaynağı kablosu ile güvenli bir elektrik dünya bağlantısı sağlayın. Değişen geçerli (AC) kabloda üç telli bir grounding eklentisine (bir grounding PIN 'i olan bir tak) sahiptir. Bu eklenti yalnızca bir topraklanmış AC prizine uyar. Grounding PIN 'inin amacını ertelemeyin.
* Güç kaynağı kablosu ana bağlantı kesme aygıtı olduğu için, dış cihazların cihazın yakınında bulunduğundan ve kolayca erişilebilir olduğundan emin olun.
* Aşağıdaki koşullardan herhangi biri mevcutsa, güç kablolarını (yani, bu eklentiyi çekerek) çıkarın ve tüm kabloların bağlantısını kesin:

  * Güç kablosu veya plug, Frayed veya hasarlı olur.
  * Cihaz yağmur, daha fazla Moisture veya diğer lılar tarafından sunulur.
  * Cihaz bırakılmış ve cihazın büyük küçük harfleri bozulmuş.
  * Cihazın hizmet veya onarım ihtiyacı olduğunu şüpheli.
* Birimi Taşımadan önce kalıcı olarak çıkarın veya herhangi bir şekilde hasar gördüğünü düşünün.

* Aşağıdaki güç belirtimlerini karşılamak için elektrik aşırı yüklemesi koruması ile uygun bir güç kaynağı sağlayın:

* Voltaj: 100-240 volt AC
* Geçerli: 1,7 Amperes
* Sıklık: 50 ila 60 Hz

![Uyarı simgesi 11 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ elektrik darbe simgesi ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) **uyarısı:**

* Ekipman ile sağlandıklardan farklı AC güç kablosu (ler) i değiştirmeyi veya kullanmayı denemeyin.

![Uyarı simgesi 12 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ elektrik sarsıntı simgesi ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
 ![ yalnızca uyarı kullanımı ](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) **:**

* Bu sembolle etiketlenmiş güç kaynağı yalnızca iç kullanım için derecelendirilir.

## <a name="regulatory-information"></a>Mevzuat bilgileri

Aşağıda Azure Stack Edge Mini R cihazı, mevzuat model numarası: TMA01 için yasal bilgiler yer almaktadır.

Azure Stack Edge Mini R cihazı, NRTL listelenmiş (UL, CSA, ETL, vb.) ve ıEC/EN 60950-1 ya da ıEC/EN 62368-1 uyumlu (CE işaretli) Information Technology donatısı ile kullanılmak üzere tasarlanmıştır.

ABD ve Kanada dışındaki ülkelerde ağ kabloları (ekipman ile sağlanmayan), uzunlukları 3 metreden fazlaysa bu ekipmanla birlikte yüklenmez.

Ekipman, aşağıdaki ortamlarda çalışacak şekilde tasarlanmıştır:

| Ortam | Belirtimler |
|:---  |:--- |
| Sistem sıcaklık belirtimleri | <ul><li>Depolama sıcaklığı: – 20 &deg; c – 50 &deg; c (– 4 &deg; f-122 &deg; f)</li><li>Sürekli işlem: 0 &deg; c – 40 &deg; c (32 &deg; f – 104 &deg; F)</li></ul> |
| Bağıl nem (RH) belirtimleri | <ul><li>Depolama: %5 ile %95 bağıl nem</li><li>Çalışan: %90 bağıl nem %10</li></ul>|
| En fazla yükseklik belirtimleri | <ul><li>Çalışma: 15.000 fit (4.572 ölçüm)</li><li>Çalışma dışı: 40.000 fit (12.192 ölçü)</li></ul>|

> ![Bildirim simgesi-2 ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **bildirim:** &nbsp; Microsoft tarafından açıkça onaylanmamış ekipmanlarda yapılan değişiklikler veya değişiklikler, kullanıcının donatımını çalıştırma yetkisini geçersiz edebilir.

#### <a name="canada-and-usa"></a>Kanada ve ABD:

> ![Bildirim simgesi-3 ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **Uyarı:** &nbsp; Bu ekipman test edilmiştir ve bir sınıftaki dijital bir cihaz için sınırlara uyum sağlamak Için, FCC bu kuralların 15. bölümüne uyun. Bu sınırlar, ekipman ticari bir ortamda çalıştırıldığında zararlı girişim için makul bir koruma sağlamak üzere tasarlanmıştır. Bu ekipman radyo sıklığı enerjisi oluşturur ve kullanır ve yönerge kılavuzuna uygun şekilde yüklenip kullanılmayacaksa radyo iletişimine zararlı girişim yapılmasına neden olabilir. Bu ekipmanın bir yöresel alanındaki işlemi, zararlı bir girişim oluşmasına neden olur ve bu durumda kullanıcının, girişim kendi masrafına göre düzeltilmesi gerekecektir.

Bu ekipmanla birlikte sağlanan Netgear A6150 WiFi USB bağdaştırıcısının, dünyanın her yerinde belirli bir kimlik tarifesi (çın) uyumluluğu için, insan gövdesine yakın bir şekilde çalıştırılması amaçlanmıştır. FCC ayarlanan çın ÖIB, 1 1,6 ' dan fazla bir değer üzerinden W/kg olur. Ürünü kaydederken veya gövdelerinizi kullanırken kullandığınızda, RF pozlama gereksinimleriyle uyumluluğu sağlamak için gövdeden 10 mm 'lik bir mesafe saklayın.

Netgear A6150 WiFi USB bağdaştırıcısı ANSI/IEEE C 95.1-1999 ile uyumludur ve OET Bülteni 65 ek C ' de belirtilen ölçüm yöntemlerine ve yordamlarına göre test edilmiştir.

Netgear A6150 özgü Absorption oranı (çın ÖIB): 1,18 W/kg, 1 oranında dokulu ortalama

Netgear A6150 WiFi USB bağdaştırıcısı yalnızca onaylanmış antenlerle birlikte kullanılacaktır. Bu cihazın ve anteninin, FCC çok kiracılı ürün yordamlarına uygun olması dışında başka bir antenle veya vericiyle birlikte birlikte bulunması veya kullanılması gerekir. ABD pazarında bulunan ürünler için yalnızca Kanal 1 ~ 11 işletilebilir. Diğer kanalların seçimi mümkün değildir.

Bant 5150:5250 MHz 'de işlem yalnızca, ortak kanal Mobil Uydu sistemlerine yönelik zararlı girişim potansiyelini azaltmak için ınkapılı kullanım içindir.

![Mevzuat bilgileri uyarısı-kapılı kullanım](./media/azure-stack-edge-mini-r-safety/regulatory-information-indoor-use-only.png)

Kullanıcılara yüksek kaliteli kdarların 5250 – 5350 MHz ve 5650 – 5850 MHz bantlarından birincil kullanıcılar (öncelikli kullanıcılar) olarak ayrılması önerilir ve bu kdars, girişim ve/veya LE-LAN cihazlarına zarar verebilir.

Bu ekipman, radyo frekansı enerji sağlar ve bunları kullanır ve bu yönergeye uygun olarak yüklenip kullanılmayacaksa, radyo iletişimine zararlı girişim yapılmasına neden olabilir. Ancak, belirli bir yüklemede girişim gerçekleşmeyecek bir garanti yoktur.

Bu ekipman, donatımın kapalı ve açık olmasına göre belirlenebilir olan radyo veya televizyon alımına zararlı girişim oluşmasına neden olursa, kullanıcıdan aşağıdaki ölçülerden bir veya daha fazlasını yaparak girişimi düzeltmesini denemeye teşvik edilir:

- Alıcı antenini yeniden yönlendir veya Yeniden Konumlandır.
- Ekipman ve alıcı arasındaki ayrımı artırın.
- Ekipmanları, alıcının bağlandığı bir bağlantı hattı üzerindeki bir çıkış üzerine bağlayın.
- Yardım almak için dağıtıcıya veya deneyimli bir radyo/TV teknisyene danışın.

Girişim sorunları hakkında daha fazla bilgi için [fcc.gov/cgb/consumerfacts/interference.html](https://www.fcc.gov/consumers/guides/interference-radio-tv-and-telephone-signals)konumundaki FCC Web sitesine gidin. Ayrıca, girişim ve telefon girişim olgu sayfaları istemek için 1-888.

' Deki yeni Web sitesinde [https://www.fcc.gov/general/radio-frequency-safety-0](https://www.fcc.gov/general/radio-frequency-safety-0) ve Endüstri Kanada Web sitesinde yer alan FCC oluşan güvenlik ile ilgili ek bilgiler bulabilirsiniz [http://www.ic.gc.ca/eic/site/smt-gst.nsf/eng/sf01904.html](http://www.ic.gc.ca/eic/site/smt-gst.nsf/eng/sf01904.html) .

Bu ürün, uyumlu çevresel cihazların kullanımını ve sistem bileşenleri arasında tam korumalı kabloları içeren koşullar altında EMC uyumluluğu göstermiştir. Radyolara, televizyon kümelerine ve diğer elektronik cihazlara girişim olma olasılığını azaltmak için, sistem bileşenleri arasında uyumlu çevresel aygıtlar ve korumalı kablolar kullanmanız önemlidir.

Bu cihaz, FCC kuralları ve sektör Kanada lisans muafiyeti RSS standardının 15. bölümüne uyar. İşlem şu iki koşula tabidir: (1) Bu cihaz zararlı bir girişim oluşmasına neden olabilir ve (2) Bu cihaz, cihazın istenmeyen çalışmasına neden olabilecek girişim dahil olmak üzere, alınan bir girişimi kabul etmelidir.

![Mevzuat bilgileri uyarısı 1](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)

ICES-3 (A)/NMB-3 (A)

Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, USA

Birleşik Devletler: (800) 426-9400

Kanada: (800) 933-4750

Netgear A6150 WiFi USB bağdaştırıcısı FCC KIMLIK: PY318300429
 
Netgear A6150 WiFi USB bağdaştırıcısı ıC KIMLIĞI: 4054A-18300429

Bu donatımla birlikte sunulan Netgear A6150 WiFi USB bağdaştırıcısı, ŞA RSS-102 ' de Genel popülasyon/denetlenmeyen pozlama sınırları için ÖIB ile uyumludur ve IEEE 1528 ' de belirtilen ölçüm yöntemlerine ve yordamlarına göre test edilmiştir. Body-Worn koşulu için en az 10 mm mesafeyi koruyun.

Netgear A6150 WiFi USB bağdaştırıcısı, denetlenmeyen bir ortam için belirtilen Kanada taşınabilir RF etkilenme sınırına uyar ve el ile açıklandığı gibi amaçlanan işlem için güvenlidir. Daha fazla RF pozlaması, ürünün gövdesinden mümkün olduğunca veya bu tür bir işlev kullanılabilir olduğunda daha düşük bir çıkış gücüne ayarlanarak elde edilebilir.

Her ürün için 1 g üzerinden ortalaması alınan belirli bir Masorption ücreti (çın) içeren bir tablo, yukarıdaki ABD bölümünde görülebilir.

![Mevzuat bilgileri uyarısı 2](./media/azure-stack-edge-mini-r-safety/regulatory-information-2.png)

#### <a name="european-union"></a>AVRUPA BIRLIĞI:

Bu ekipman için AB bildiriminin bir kopyasını isteyin. [CSI_Compliance@microsoft.com](mailto:CSI_Compliance@microsoft.com) adresine e-posta gönderin.

Bu ekipmanla birlikte sunulan Netgear A6150 WiFi USB bağdaştırıcısı, 2014/53/AB yönergesinde ve isteğe bağlı olarak da sağlanmalıdır.

![Uyarı simgesi 13 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Uyarı:**  

Bu bir ürün sınıfındır. Yurtiçi bir ortamda, bu ürün radyo girişimine neden olabilir ve bu durumda kullanıcının yeterli ölçüler yapması gerekebilir.

Çöp pillerinin ve elektrik ve elektronik ekipmanın elden çıkarılması:

![Uyarı simgesi 14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

Üründe veya pillerinde bulunan bu sembol, bu ürünün ve içerdiği tüm pillerin, ev isverünüzle birlikte atılmayacağı anlamına gelir. Bunun yerine, pil ve elektrik ve elektronik ekipmanın geri dönüştürülmesi için bunu ilgili bir koleksiyon noktasına ele almak sizin sorumluluğunuzdadır. Bu ayrı koleksiyon ve geri dönüşüm, doğal kaynakların korunmasına yardımcı olur ve pillerde ve elektrik ve elektronik ekipmanda olası tehlikeli bir etkinlik olması nedeniyle, uygun olmayan bir elden çıkarılmasından kaynaklanabilir. Pillerinizi ve elektrik ve elektronik alımlarınızı nereden kapatabileceğiniz hakkında daha fazla bilgi için lütfen yerel şehriniz/municiplik ofisiniz, ev atık servisi hizmeti veya bu ürünü satın aldığınız mağaza ekibine başvurun. erecycle@microsoft.comWEEE hakkında daha fazla bilgi için iletişim kurun.

Bu ürün, para hücresi pili (lar) içerir.

Bu ekipmanla birlikte sağlanan Netgear A6150 WiFi USB bağdaştırıcısının, insan gövdesine yakın bir şekilde çalıştırılması amaçlanmıştır ve gövde-Worn özgü kimlik oranı (çın) uyumluluğu (örneğin, aşağıdaki değerlere bakın) için test edilmiştir. Bir ürünü kaydederken veya gövdelerinizi kullanırken kullandığınızda, RF pozlama gereksinimleriyle uyumluluğu sağlamak için gövdeden 10 mm 'lik bir mesafe koruyun.

**Netgear A6150 özgü Absorption oranı (çın ÖİB):** 0,54 W/kg, dokulu

 
Bu cihaz, AB 'nin tüm üye durumlarında çalışmayabilir. Cihazın kullanıldığı ulusal ve yerel düzenlemeleri gözlemleyin. Bu cihaz, yalnızca aşağıdaki ülkelerde 5150-5350 MHz sıklık aralığında çalışırken, ınkapaklı kullanımı ile kısıtlıdır:  

![Yalnızca bir ınkapısı kullanımı gerektiren AB ülkeleri](./media/azure-stack-edge-mini-r-safety/mini-r-safety-eu-indoor-use-only.png)

Aşağıdaki tabloda, 10.8 (a) ve 10.8 (b) makalesine uygun olarak, aşağıdaki tabloda kullanılan sıklık bantları ve Netgear kablosuz ürünlerinin maksimum RF iletim gücü, AB 'de satışa sunulmuştur:

**WiFi**

| Sıklık aralığı (MHz) | Kullanılan kanallar | En fazla Iletme gücü (dBm/mW) |
| --------------------- | ------------- | --------------------------- |
| 2400-2483.5 | 1-13    | ODFM: 19,9 dBm (97,7 mW) <br> CCK: 17,9 dBm (61,7 mW) |
| 5150-5320   | 36-48   | 22,9 dBm (195 mW) |
| 5250-5350   | 52-64   | TPC ile 22,9 dBm (195 mW) <br> 19,9 dBm (97,7 mW) tolmayan bilgisayar |
| 5470-5725   | 100-140 | TPC ile 29,9 dBm (977 mW) <br> 29,6 dBm (490 mW) tolmayan bilgisayar |

Microsoft Irlanda Sandyford ınest Dublin D18 KX32 Gu

Telefon numarası: + 353 1 295 3826

Faks numarası: + 353 1 706 4110

#### <a name="singapore"></a>Singapur:

Bu donatımla birlikte sunulan Netgear A6150 WiFi USB bağdaştırıcısı, ıMDA standartlarına uyar.


## <a name="next-steps"></a>Sonraki adımlar

- [Azure Stack Edge Mini R 'yi dağıtmaya hazırlanma](azure-stack-edge-mini-r-deploy-prep.md)
