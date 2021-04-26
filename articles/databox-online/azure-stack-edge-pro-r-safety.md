---
title: Azure Stack Edge Pro R Güvenlik Kılavuzu | Microsoft Docs
description: Güvenlik kuralları, yönergeler, hususlar ve Azure Stack Edge Pro R cihazınızı güvenli bir şekilde yüklemeyi ve çalıştırmayı açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: alkohli
ms.openlocfilehash: 7b3589349feed2e20711aa756d8be8ebc433ac0b
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077610"
---
# <a name="azure-stack-edge-pro-r-safety-instructions"></a>Azure Stack Edge Pro R güvenlik yönergeleri

![Uyarı simgesi ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ okuma güvenlik uyarısı SIMGESI ](./media/azure-stack-edge-pro-r-safety/icon-safety-read-all-instructions.png)
 **güvenlik ve durum bilgilerini oku**

Azure Stack Edge Pro R cihazınızı kullanmadan önce bu makaledeki tüm güvenlik bilgilerini okuyun. Yönergeleri izlemeden hata tetiklenmesi, elektrik kesintisi, sakatlama veya özelliklerinizi zarar verebilir. Azure Stack Edge Pro R 'yi kullanmadan önce aşağıdaki tüm güvenlik bilgilerini okuyun.

## <a name="safety-icon-conventions"></a>Güvenlik simgesi kuralları

Hasar uyarısı işaretlerine yönelik aşağıdaki sinyal sözcükleri şunlardır:

| Simge | Description |
|:--- |:--- |
| ![Hasar simgesi](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)| **Danger:** Önedilmediği durumlarda, ölüm veya ciddi bir yaralama oluşmasına neden olan tehlikeli bir durum belirtir. <br> **Uyarı:** Önedilmediği durumlarda, ölüm veya ciddi bir yaralama oluşmasına neden olabilecek tehlikeli bir durum belirtir. <br> **Dikkat:** Önedilmediği durumlarda küçük veya orta bir yaralanmaya neden olabilecek tehlikeli bir durum belirtir.|
|

Azure Stack Edge Pro R Edge cihazınızı ayarlarken ve çalıştırırken aşağıdaki rastlantı simgeleri gözlemlenecek:

| Simge | Description |
|:--- |:--- |
| ![Önce tüm yönergeleri okuyun](./media/azure-stack-edge-pro-r-safety/icon-safety-read-all-instructions.png) | Önce tüm yönergeleri okuyun |
| ![Bildirim simgesi ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **bildirimi:** | Önemli kabul edilen, ancak tehlike ile ilgili olmayan bilgileri gösterir. |
| ![Hasar simgesi](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) | Hasar simgesi |
| ![İpucu rastlantı simgesi](./media/azure-stack-edge-pro-r-safety/icon-safety-tip-hazard.png)  | İpucu hasar|
| ![Ağır ağırlık simgesi](./media/azure-stack-edge-pro-r-safety/icon-safety-heavy-weight.png)  | Ağır rastlantı|
| ![Elektrik sarsıntı simgesi](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) | Elektrik şok hasar |
| ![Kullanıcı Serviceable parçası yok simgesi](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) | Hiçbir Kullanıcı hizmet konusu parçası yok. Uygun şekilde Eğitilmediğiniz müddetçe bu erişimi kullanmayın. |
| ![Birden çok güç kaynağı simgesi](./media/azure-stack-edge-pro-r-safety/icon-safety-disconnect-all-power.png)  | Birden çok güç kaynağı. Tüm güç kimliklerinin, ekipmandaki tüm gücü kaldırmak için bağlantısını kesin. |
| ![Noktaları yeniden getiriliyor simgesi](./media/azure-stack-edge-pro-r-safety/icon-pinching-points.png)  | Sıkıştırılmış noktaları var. |
| ![Sık kullanılan bileşenler veya yüzeyler simgesi](./media/azure-stack-edge-pro-r-safety/icon-hot-component-surface.png)  | Sık kullanılan bileşenleri veya yüzeyleri gösterir. |


## <a name="handling-precautions-and-site-selection"></a>Önlemler ve site seçimini işleme

![Uyarı simgesi ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) **uyarısı:**

* Uygun ekipman (örneğin, palet jakı) ve kişisel koruyucu donanım (PPE), örneğin, sevk edilen cihazı taşırken ve işlerken kullanılmalıdır.

![Uyarı simgesi ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)![ Ipucu hasar simgesi ](./media/azure-stack-edge-pro-r-safety/icon-safety-tip-hazard.png) **uyarısı:**

* Potansiyel bir ipucunu veya kırıltı bir hasar oluşmasını önlemek için donatımı düz, sabit ve kararlı bir yüzeye yerleştirin.
* Birbirleriyle 2 ' den fazla cihaz yığmayın.
* Yığınlama öncesinde sistemin güvenli olduğundan emin olun.
* Yığılmış cihazları yeniden konumlandırmayın veya taşımayın.
* Dış kablolar ile enerji taşıyan cihazları yığmayın.
* Yığın işlemleri sırasında Power corler 'in kırlanıp veya zarar görmediğinden emin olun.
* Cihazlar tablo veya çalışma alanı olarak kullanılamaz.

![Uyarı simgesi ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ elektrik ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png)
 ![ sarsıntısız, hiçbir Kullanıcı hizmet vermez parçalar simgesi uyarı ](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) **:**

* Zararlar için *alınan as* cihazını inceleyin. Cihaz kasası hasar görmüşse, yerini almak için [Microsoft desteği başvurun](azure-stack-edge-contact-microsoft-support.md) . Cihazı çalıştırmayı denemeyin.
* Cihazın arızalı olduğunu kuşkulanıyorsanız, bir değiştirme edinmek için [Microsoft desteği başvurun](azure-stack-edge-contact-microsoft-support.md) . Cihaza bakım yapmayı denemeyin.
* Cihaz, hiçbir Kullanıcı-serviceable Bölümü içermiyor. Tehlikeli voltaj, geçerli ve enerji düzeyleri içinde mevcuttur. ' İ açmayın. Cihaza bakım için Microsoft 'a döndürün.

![Uyarı simgesi ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ ağır ağırlık simgesi ](./media/azure-stack-edge-pro-r-safety/icon-safety-heavy-weight.png) **uyarısı:**

* KGK güç kaynağı modülünün kaldırılması, UPS içindeki enerji parçalarını ortaya çıkarır. Güç kaynağı modülü bölmesine yabancı nesneler eklemeyin.
* Azure Stack Edge Pro R Edge cihazını kendiniz çalıştırmayı denemeyin. Bir kutu 52 kg ve 93 kg (115 lbs ve 205 lbs) arasında ağırlık içerebilir; ekipman taşıma ve kaldırma sırasında mekanik yardım veya diğer uygun yardımı kullanın. Ekipman taşıma ve kaldırma sırasında yerel meslek sistem durumu ve güvenlik gereksinimlerine uygun.
* Doğru mekanik yardım olmadan donatımı denemeyin. Bu ağırlığı kaldırmaya çalışan ciddi sakatlama neden olabilir.

![Uyarı simgesi ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) **uyarısı:**  

* Sistem denetlenen bir ortamda çalışmak üzere tasarlanmıştır. Şu şekilde bir site seçin:
  * Doğrudan güneş ve radiators da dahil olmak üzere ısı kaynaklarından iyi bir şekilde.
  * Nemi veya yağmma gösterilmez.
  * Titreşimi ve fiziksel sarsıntı en aza indiren bir alanda bulunur.  Sistem, MIL-STD-810G 'e göre darbe ve titreşim için tasarlanmıştır.
  * Elektrik cihazları tarafından üretilen güçlü elektromanyetik alanlardan yalıtılmıştır.
  * Doğru şekilde topraklanmış olarak sunulur.
  * Güç kaynağı kablosunu, ürünün ana güç bağlantısı kesildiğinde sundukları için yeterli alanla birlikte sunulur.
* Ürünle birlikte Ethernet kabloları sağlanmaz. Elektromanyetik paraziti azaltmak için, Cat 6 korumalı çift çifti (STP) kablolarının kullanılması önerilir.
* Ekipman etrafında yeterli hava dolaşımını sağlayan bir iş alanında ekipman ayarlama; Cihaz çalışırken ön ve arka kapakların tamamen kaldırıldığından emin olun.
* Ürünle birlikte Ethernet kabloları sağlanmaz. Elektromanyetik paraziti azaltmak için, Cat 6 korumalı (STP) kablolama kullanılması önerilir.
* Ekipmanları, uçak açısından en azımların ücretsiz olarak, sıcaklığın yanı sıra ekipman genelinde sorunsuz bir şekilde dolaşıma izin verecek şekilde yükler.
* Donatımları, sıvı ve fazla sayıda insandaki ortamların kaynaklarından koruyun.
* Herhangi bir likit veya herhangi bir yabancı nesnenin sisteme girmesine izin vermeyin. Meşruları veya herhangi bir diğer likit kapsayıcıyı sisteme veya yanına yerleştirmeyin.

## <a name="heater-precautions"></a>Isıtıcı önlemleri

![Uyarı simgesi ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ sık erişimli bileşenler veya yüzeyler simgesi 1 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **Uyarı:** 

* Sistem açıkken otomatik ısıtıcı işlemi, ısıtıcı derleme kapasitesinde yüksek yüzey sıcaklıkları nedeniyle bir dokunmatik hasar oluşturabilir. Sistem açık durumdayken bu yüzeye dokunmayın. Sistemin gücü kapatıldıktan sonra 10 dakikalık soğuk bir süre kullanılmasına izin verin.

![Uyarı simgesi ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ işaret eden noktaları simgesi 1 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **Uyarı:** 

* Sistem açık olduğunda, arka plenum kapısının otomatik olarak incelenmesi bir Pinç nokta rastlantı oluşturabilir. Sistemin açık olduğu durumlarda bu alanın açık kalmasını sağlayın.

## <a name="electrical-precautions"></a>Elektrik önlemleri

![Uyarı simgesi ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ elektrik sarsıntı simgesi ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **uyarısı:**

* Güç kaynağı kablosu ile güvenli bir elektrik dünya bağlantısı sağlayın. Değişen geçerli (AC) kabloda üç telli bir grounding eklentisine (bir grounding PIN 'i olan bir tak) sahiptir. Bu eklenti yalnızca bir topraklanmış AC prizine uyar. Grounding PIN 'inin amacını ertelemeyin.
* Güç kaynağı kablosu ana bağlantı kesme aygıtı olduğu için, dış cihazların cihazın yakınında bulunduğundan ve kolayca erişilebilir olduğundan emin olun.
* Aşağıdaki koşullardan herhangi biri mevcutsa, güç kablolarını (yani, bu eklentiyi çekerek) çıkarın ve tüm kabloların bağlantısını kesin:

  * Güç kablosu veya plug, Frayed veya hasarlı olur.
  * Bir şeyi cihazın büyük küçük harflere taşırın.
  * Cihaz, yağmur veya daha fazla Moisture 'e açıktır.
  * Cihaz bırakılmış ve cihazın büyük küçük harfleri hasarlı.
  * Cihazın hizmet veya onarım ihtiyacı olduğunu şüpheli.
* Birimi Taşımadan önce kalıcı olarak çıkarın veya herhangi bir şekilde hasar gördüğünü düşünün.
* Yüksek sızıntı durumunu engellemek için, tek bir aktarma durumunda birden fazla kesintisiz güç kaynağı (UPS) olduğunda, her bir KGK 'nın bağımsız bir dal devresine bağlanması önerilir. Bununla birlikte, her bir UPS 'nin güvenlik tarafında PDU 'nun tek bir besleyiciyi temel alan bir güç dağıtım birimi (PDU) veya başka bir cihazın kullanıldığı durumlarda, her bir UPS 'in içindeki grounding terminali de ek bir bina kasasının kullanılması gerekir.

  > [!NOTE]
  > Zaten ek bir ek yönetmen olan bir PDU kullanılıyorsa, KGK üzerinde ek grounding terminali kullanılması gerekli değildir.

* Aşağıdaki güç belirtimlerini karşılamak için elektrik aşırı yüklemesi koruması ile uygun bir güç kaynağı sağlayın:

  * Voltaj: 100-240 volt AC
  * Geçerli: her güç kablosu için en fazla 20 A. Güç kablosu sağlanır.
  * Sıklık: 50 ila 60 Hz

![Uyarı simgesi ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ elektrik sarsıntı simgesi ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png)
 ![ birden çok güç kaynağı ](./media/azure-stack-edge-pro-r-safety/icon-safety-disconnect-all-power.png) **uyarısı:**

* Kesintisiz güç kaynağı (UPS) olmayan sistemler için tüm AC güç kablosunu sökün ve donanımlardan tamamen AC gücünü tamamen kaldırın.
* UPS içeren sistemler için tüm AC güç kablosunu çıkarın ve UPS güç anahtarını kullanarak sistemi yeniden enerji katın. KGK, tehlikeli AC ve DC voltages içerir.
* Bir sistem bir UPS içeriyorsa, UPS korumalı bir giriş güç kablosu ile sağlanmıştı. Korumalı giriş güç kablosunu kullanmanız gerekir, kablonun yerini değiştirmeyin veya değiştirmeyin.

![Uyarı simgesi ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ elektrik sarsıntı simgesi ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **uyarısı:**

* UPS ile donatılmış sistemler için AC ve/veya DC voltajı her zaman pille veya yardımcı programından oluşturulan KGK çıktılarından oluşan bir AC gerilimi riskini içerir. Ekipman hasar veya kişisel yarasayı önlemek için, birincil güç kaynağıyla bağlantısı kesilse bile, UPS çıktısında bir voltaj olabileceğini her zaman varsayın.
* UPS ile donatılmış sistemler için, tüm UPS enerji dış bağlantıları kadın. Büyük/küçük harf kaldırmayın veya bu veya KGK üzerinde herhangi bir bağlayıcıya bir şey eklemeyin.

![Uyarı simgesi ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ elektrik sarsıntı simgesi ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **uyarısı:**

* Ekipman ile sağlandıklardan farklı AC güç kablosu (ler) i değiştirmeyi veya kullanmayı denemeyin.

![Uyarı simgesi ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ hiçbir Kullanıcı Serviceable bölümü yok simgesi ](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) **Uyarı:**

* Bu ekipman, Lityum para hücresi ve/veya lityum Iron fospnefret pilleri içerir. Ekipmana hizmet vermeyi denemeyin. Bu ekipmandaki piller Kullanıcı serviceable değildir. Pil, yanlış bir türle değiştirilirse açılım riski.
* KGK pil modülünün kaldırılması, UPS içindeki enerji parçalarını ortaya çıkarır. Yabancı nesneleri pil modülü bölmesi içine eklemeyin.

## <a name="regulatory-information"></a>Mevzuat bilgileri

Bu bölüm Azure Stack Edge Pro R cihazının yasal bilgilerini içerir, mevzuat model numarası: Azure Stack Edge Pro R.

Azure Stack Edge Pro R Edge cihazı, NRTL listelenen (UL, CSA, ETL, vb.) ve ıEC/EN 60950-1 ya da ıEC/EN 62368-1 uyumlu (CE işaretli) Information Technology donatımı ile kullanılmak üzere tasarlanmıştır.

Cihaz, aşağıdaki ortamlarda çalışacak şekilde tasarlanmıştır:

| Ortam | Belirtimler |
|:--- |:--- |
|Sıcaklık belirtimleri | <ul><li>Depolama sıcaklığı: – 33 &deg; c – 63 &deg; c (– 28 &deg; f-145 &deg; F) </li><li>Sürekli işlem: 5 &deg; C – 43 &deg; C (41 &deg; f – 110 &deg; F)</li><li>Maksimum Sıcaklık gradyanı (işletim ve depolama): 20 &deg; C/h (68 &deg; F/h)</li></ul> |
|Bağıl nem belirtimleri | <ul><li>Depolama: 33 C (91 F) ile 95% RH ile %5 arasındaki &deg; &deg; en fazla Dew noktası. Atmosphere her zaman yoğunlaşmayan olmalıdır.</li><li>Çalışma: %5 %85, 29 &deg; C (84,2 &deg; F) ile ilgili arasındaki nem</li></ul> |
| En fazla yükseklik belirtimleri | <ul><li>İşletim (UPS olmadan): 15.000 ft (4.572 ölçü)</li><li>İşletim (UPS Ile): 10.000 ft (3.048 ölçü)</li><li>Depolama alanı: 40.000 ft (12.192 ölçü)</li></ul> |

<!--|Standard operating temperature specifications | <ul>Continuous operation (for altitude less than 950 m or 3117 ft): +5&deg;C–45&deg;C (41&deg;F–113&deg;F) with no direct sunlight on the equipment</ui>
|Expanded operating temperature specifications | <ul><li>Continuous operation: 5&deg;C to 45&deg;C at 5% to 85% RH with 29&deg;C dew point.</li><li></= 1% of annual operating hours: –5&deg;C to 55&deg;C at 5% to 90% RH with 29&deg;C dew point. |
| Expanded operating temperature restrictions | <ul><li>Do not perform cold start below -15&deg;C according to IEC 60945.</li><li>The operating temperature specified is for a maximum altitude of 950 meters.</li></ul> |
| Gaseous contamination specifications | <ul><li>Copper coupon corrosion rate: < 300 &Aring;/month per Class G1 as defined by ANSI/ISA71.04-1985.</li><li>Silver coupon corrosion rate: < 200 &Aring;/month as defined by AHSRAE TC9.9.</li></ul>|

> [!NOTE]
> Outside the standard operating temperature (10&deg;C to 35&deg;C):
> - The system can operate continuously in temperatures as low as 5&deg;C and as high as 45&deg;C. For temperatures between 35&deg;C and 45&deg;C, de-rate maximum allowable temperature by 1&deg;C per 175 m above 950 m (1&deg;F per 319 ft).
> - The system can operate down to –5&deg;C or up to 55&deg;C for a maximum of 1% of its annual operating hours. For temperatures between 45&deg;C and 55&deg;C, de-rate maximum allowable temperature by 1&deg;C per 125 m above 950 m (1&deg;F per 228 ft).
>
> When operating in the expanded temperature range:
> - System performance may be impacted.
> - Ambient temperature warnings may be reported in the System Event Log.     
>
> Maximum corrosive contaminant levels measured at &lt;/= 50% relative humidity. 


A device that has a UPS installed is designed to operate in the following environments:

| Environment | Specifications |
|:---  |:--- |
|Temperature specifications | <ul><li>Storage temperature: –40&deg;C–70&deg;C (–40&deg;F-158&deg;F) per Mil-Std 810G Method 501.5, Proc 1</li><li>Continuous operation (for altitude less than 950 m or 3117 ft): 5&deg;C–45&deg;C (41&deg;F–113&deg;F), with no direct sunlight on the equipment</li><li>Excursion temperature: 50&deg;C per Mil-Std 810G</li><li>Maximum temperature gradient (operating and storage): 20&deg;C/h (68&deg;F/h)</li></ul> |
|Relative humidity specifications | <ul><li>Storage: 5% to 95% RH with 33&deg;C (91&deg;F) maximum dew point. Atmosphere must be non-condensing at all times.</li><li>Operating: 5% to 85% relative humidity with 29&deg;C (84.2&deg;F) maximum dew point</li></ul>|
|Maximum altitude specifications | <ul><li>Storage: Mil-Std 810G method 500.5, Proc. I, 40,000 ft for 1 hour after stabilization </li><li>Operating: Mil-Std 810G method 500.5, Proc. II, air carriage, 15,000 ft for 1 hour after stabilization</li></ul>|
|Standard operating temperature specifications |<ul><li>Continuous operation (for altitude less than 950 m or 3117 ft)</li><li>+5&deg;C–45&deg;C (41&deg;F–113&deg;F) with no direct sunlight on the equipment</li></ul>|
|Expanded operating temperature specifications | <ul><li>Continuous operation: 5&deg;C to 45&deg;C at 5% to 85% RH with 29&deg;C dew point.</li><li>&lt;/= 1% of annual operating hours: 0&deg;C to 50&deg;C at 5% to 90% RH with 29&deg;C dew point.</li></ul>|
|Expanded operating temperature restrictions | <ul><li>Do not perform cold start below -15C Per IEC 60945</li><li>The operating temperature specified is for a maximum altitude of 950 meters</li></ul> |
|Gaseous contamination specifications | <ul><li>Copper coupon corrosion rate: < 300 &Aring;/month per Class G1 as defined by ANSI/ISA71.04-1985.</li><li>Silver coupon corrosion rate: < 200 &Aring;/month as defined by AHSRAE TC9.9.|-->

<!--
> [!NOTE]
> Outside the standard operating temperature (10&deg;C to 35&deg;C):
> - The system can operate continuously in temperatures as low as 5&deg;C and as high as 45&deg;C. For temperatures between 35&deg;C and 45&deg;C, de-rate maximum allowable temperature by 1&deg;C per 175 m above 950 m (1&deg;F per 319 ft).
> - The system can operate down to 0&deg;C or up to 50&deg;C for a maximum of 1% of its annual operating hours. For temperatures between 45&deg;C and 55&deg;C, de-rate maximum allowable temperature by 1&deg;C per 125 m above 950 m (1&deg;F per 228 ft).
> 
> When operating in the expanded temperature range:
> - System performance may be impacted.
> - Ambient temperature warnings may be reported in the System Event Log.
>
> Maximum corrosive contaminant levels measured at &lt;/= 50% relative humidity. --> 


> ![Bildirim simgesi-2 ](./media/azure-stack-edge-pro-r-safety/icon-safety-notice.png) **bildirim:** &nbsp; Microsoft tarafından açıkça onaylanmamış ekipmanlarda yapılan değişiklikler veya değişiklikler, kullanıcının donatımını çalıştırma yetkisini geçersiz edebilir.

#### <a name="canada-and-usa"></a>Kanada ve ABD:

> ![Uyarı simgesi-2 ](./media/azure-stack-edge-pro-r-safety/icon-safety-notice.png) **bildirim:** &nbsp; Bu ekipman test edilmiştir ve bir sınıftaki dijital bir cihaz için sınırlara uyum sağlamak Için, FCC bu kuralların 15. bölümüne uyun. Bu sınırlar, ekipman ticari bir ortamda çalıştırıldığında zararlı girişim için makul bir koruma sağlamak üzere tasarlanmıştır. Bu ekipman radyo sıklığı enerjisi oluşturur ve kullanır ve yönerge kılavuzuna uygun şekilde yüklenip kullanılmayacaksa radyo iletişimine zararlı girişim yapılmasına neden olabilir. Bu ekipmanın bir yöresel alanındaki işlemi, zararlı bir girişim oluşmasına neden olur ve bu durumda kullanıcının, girişim kendi masrafına göre düzeltilmesi gerekecektir.

Bu cihaz, FCC kuralları ve sektör Kanada lisans muafiyeti RSS standardının 15. bölümüne uyar. İşlem şu iki koşula tabidir: (1) Bu cihaz zararlı bir girişim oluşmasına neden olabilir ve (2) Bu cihaz, cihazın istenmeyen çalışmasına neden olabilecek girişim dahil olmak üzere, alınan bir girişimi kabul etmelidir.

![Mevzuat bilgileri uyarısı 1](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)


, ICES-3 (A)/NMB-3 (A) Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, ABD Birleşik Devletler: (800) 426-9400 Kanada: (800) 933-4750

#### <a name="european-union"></a>AVRUPA BIRLIĞI:

Conformity 'in AB bildiriminin bir kopyasını isteyin. [CSI_Compliance@microsoft.com](mailto:CSI_Compliance@microsoft.com) adresine e-posta gönderin.

![Uyarı simgesi ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) **uyarısı!**

Bu bir ürün sınıfındır. Yurtiçi bir ortamda, bu ürün radyo girişimine neden olabilir ve bu durumda kullanıcının yeterli ölçüler yapması gerekebilir.

Çöp pillerinin ve elektrik ve elektronik ekipmanın elden çıkarılması:

![Uyarı simgesi 14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

Üründe veya pillerinde bulunan bu sembol, bu ürünün ve içerdiği tüm pillerin, ev isverünüzle birlikte atılmayacağı anlamına gelir. Bunun yerine, pil ve elektrik ve elektronik ekipmanın geri dönüştürülmesi için bunu ilgili bir koleksiyon noktasına ele almak sizin sorumluluğunuzdadır. Bu ayrı koleksiyon ve geri dönüşüm, doğal kaynakların korunmasına yardımcı olur ve pillerde ve elektrik ve elektronik ekipmanda olası tehlikeli bir etkinlik olması nedeniyle, uygun olmayan bir elden çıkarılmasından kaynaklanabilir. Pillerinizi ve elektrik ve elektronik alımlarınızı nereden kapatabileceğiniz hakkında daha fazla bilgi için lütfen yerel şehriniz/municiplik ofisiniz, ev atık servisi hizmeti veya bu ürünü satın aldığınız mağaza ekibine başvurun. erecycle@microsoft.comWEEE hakkında daha fazla bilgi için iletişim kurun.

Bu ürün, para hücresi pili (lar) içerir.

Microsoft Irlanda Sandyford IND EST Dublin D18 KX32 Gu telefon numarası: + 353 1 295 3826 Faks numarası: + 353 1 706 4110


## <a name="next-steps"></a>Sonraki adımlar

- [Azure Stack Edge Pro R Edge 'i dağıtmaya hazırlanma](azure-stack-edge-pro-r-deploy-prep.md)
