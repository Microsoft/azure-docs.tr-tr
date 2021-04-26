---
title: Azure Data Box için güvenlik | Microsoft Docs
description: Güvenlik kurallarını, yönergeleri ve konuları açıklar ve Azure Data Box güvenle yükleyip çalıştırabileceğinizi açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 08/13/2019
ms.author: alkohli
ms.openlocfilehash: f4a0739eb1ab14155a98b17e2427c5b08c9db788
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96020679"
---
# <a name="safely-install-and-operate-your-azure-data-box"></a>Azure Data Box güvenle yükleyip çalıştırın
![Uyarı simgesi ](./media/data-box-safety/warning_icon.png)
 ![ okuma güvenlik uyarısı SIMGESI ](./media/data-box-safety/read_safety_and_health_information_icon.png) **güvenlik ve durum bilgilerini oku**

Azure Data Box kullanmadan önce bu makaledeki tüm güvenlik bilgilerini okuyun. Yönergeleri izlemeden hata, yangın, elektrik sarsıntı veya diğer sakatlama ya da özelliklerine zarar verebilir.

## <a name="safety-icon-conventions"></a>Güvenlik simgesi kuralları
Data Box kurarken ve çalıştırırken gözlenecek güvenlik önlemleri gözden geçirdikten sonra bulacağınız simgeler aşağıda verilmiştir.

| Simge | Description |
|:--- |:--- |
| ![Danger simgesi ](./media/data-box-safety/warning_icon.png) **tehlike!** |Önedilmediği durumlarda, ölüm veya ciddi bir yaralama oluşmasına neden olan tehlikeli bir durum belirtir. Bu sinyal sözcüğü, en Extreme durumlarla sınırlı olacaktır. |
| ![Uyarı simgesi ](./media/data-box-safety/warning_icon.png) **uyarısı!** |Önedilmediği durumlarda, ölüm veya ciddi bir yaralama oluşmasına neden olabilecek tehlikeli bir durum belirtir. |
| ![Uyarı simgesi ](./media/data-box-safety/warning_icon.png) **dikkatli!** |Önedilmediği durumlarda küçük veya orta bir yaralanmaya neden olabilecek tehlikeli bir durum belirtir. |
| ![Bildirim simgesi ](./media/data-box-safety/notice_icon.png) **bildirimi:** |Önemli kabul edilen, ancak tehlike ile ilgili olmayan bilgileri gösterir. |
| ![Elektrik sarsıntısı simgesi ](./media/data-box-safety/electrical_shock_hazard_icon.png) **elektrik şok hasar** |Yüksek voltaj. |
| ![Ağır kalınlık simgesi ](./media/data-box-safety/heavy_weight_hazard_icon.png) **ağır ağırlık** | |
| ![Kullanıcı tarafından ](./media/data-box-safety/no_user_serviceable_parts_icon.png)  hizmet içermeyen parçalar simgesi yok |Uygun şekilde Eğitilmediğiniz müddetçe bu erişimi kullanmayın. |
| ![Güvenlik bildirimi okuma simgesi ](./media/data-box-safety/read_safety_and_health_information_icon.png) **Ilk olarak tüm yönergeleri oku** | |
| ![İpucu hasar simgesi ](./media/data-box-safety/tip_hazard_icon.png) **Ipucu rastlantı** | |

## <a name="handling-precautions"></a>Önlemleri işleme

![Uyarı simgesi ](./media/data-box-safety/warning_icon.png) ![ elektrik sarsıntı simgesi ](./media/data-box-safety/electrical_shock_hazard_icon.png)![ hiçbir Kullanıcı hizmet halinde bölümler simgesi yok ](./media/data-box-safety/no_user_serviceable_parts_icon.png)  

* Zararlar için *alınan as* cihazını inceleyin. Cihaz kasası hasar görmüşse, yerini almak için [Microsoft desteği başvurun](data-box-disk-contact-microsoft-support.md) . Cihazı çalıştırmayı denemeyin. 
* Cihaz, yetkisiz kanıtlama dayanıklı bir şekilde donatılmış. Cihazın arızalı olduğunu kuşkulanıyorsanız, bir değiştirme edinmek için [Microsoft desteği başvurun](data-box-disk-contact-microsoft-support.md) . Cihaza bakım yapmayı denemeyin. 
* Cihaz, hiçbir Kullanıcı-serviceable Bölümü içermiyor. Tehlikeli voltaj, geçerli ve enerji düzeyleri içinde mevcuttur. ' İ açmayın. Cihaza bakım için Microsoft 'a döndürün.

![Uyarı simgesi ](./media/data-box-safety/warning_icon.png) ![ ağır ağırlık simgesi ](./media/data-box-safety/heavy_weight_hazard_icon.png) **uyarısı!** 

* Tam olarak yapılandırılmış bir kutu 22,7 kg 'a (50 lbs) ağırlık verebilir; kendiniz de kaldırma denemeyin.
* Kasası taşımadan önce her zaman ağırlığı işlemek için iki kişinin kullanılabilir olduğundan emin olun. Bu ağırlığı kaldırmaya çalışan bir kişinin, sakatlama 'e dokunması gerektiğini unutmayın.


![Uyarı simgesi ](./media/data-box-safety/warning_icon.png) ![ Ipucu hasar simgesi ](./media/data-box-safety/tip_hazard_icon.png) **uyarısı!**
* Olası bir tıp hasar yaşamamak için cihazı düz, sabit ve kararlı bir yüzeye yerleştirin.
* Rafa takılan ekipman, raflar veya çalışma alanı olarak kullanılamaz. Data Box rafa takılan ekipmanın üzerine yerleştirmeyin. Genişletilmiş rafa takılan bir birime herhangi bir yük türü eklemek, yaralanma, ölüm veya ürün hasarını ortaya çıkabilecek olası bir tıp hasar oluşturabilir.

![Uyarı simgesi ](./media/data-box-safety/warning_icon.png) **uyarısı!**

* Cihazı, cihaz etrafında yeterli hava dolaşımını sağlayan bir iş alanında ayarlayın.
* Cihazı, sıcaklığın boşaltinden muaf olan sıcaklık denetimli bir ınkapılı alana yükler ve cihaz etrafında yeterli hava dolaşıma izin verin.
* Cihazı, sıvı ve aşırı insanlar ortamlarının kaynaklarından koruyun.


## <a name="electrical-precautions"></a>Elektrik önlemleri

![Uyarı simgesi ](./media/data-box-safety/warning_icon.png) ![ elektrik sarsıntı simgesi ](./media/data-box-safety/electrical_shock_hazard_icon.png) **uyarısı!**

* Güç kaynağı kablosu ile güvenli bir elektrik dünya bağlantısı sağlayın. AC kablosu üç telli bir grounding eklentisine sahiptir (bir grounding PIN 'i olan bir tak). Bu eklenti yalnızca bir topraklanmış AC prizine uyar. Grounding PIN 'inin amacını ertelemeyin.
* Güç kaynağı kablosu ana bağlantı kesme aygıtı olduğu için, dış cihazların cihazın yakınında bulunduğundan ve kolayca erişilebilir olduğundan emin olun.
* Aşağıdaki koşullardan herhangi biri mevcutsa, güç kablosunu çıkarın (kablosunu çekerek, yani, her türlü kablonun bağlantısını kesin):

    - Güç kablosu veya plug, Frayed veya hasarlı olur.
    - Bir şeyi cihazın büyük küçük harflere taşırın.
    - Cihaz, yağmur veya daha fazla Moisture 'e açıktır.
    - Cihaz bırakılmış ve cihazın büyük küçük harfleri hasarlı.
    - Cihazın hizmet veya onarım ihtiyacı olduğunu şüpheli.
* Birimi Taşımadan önce kalıcı olarak çıkarın veya herhangi bir şekilde hasar gördüğünü düşünün.
* Aşağıdaki güç belirtimlerini karşılamak için elektrik aşırı yüklemesi koruması ile uygun bir güç kaynağı sağlayın:

    - Voltaj: 100 V AC-240 V AC
    - Geçerli: 6 A, en fazla
    - Sıklık: 50 Hz ila 60 Hz

![Uyarı simgesi ](./media/data-box-safety/warning_icon.png) **dikkat:**

* Bu cihaz pille bir pil hücresi içeriyor. Cihaza bakım yapmayı denemeyin. Bu Cihazdaki piller Kullanıcı serviceable değildir. 
* **Yalnızca hizmet personeli için**: pil yanlış bir türle değiştirilirse açılım riski. Yönergelere göre kullanılan piller atılamadı.

![Bildirim simgesi ](./media/data-box-safety/notice_icon.png) **bildirimi:**

Cihazınızın doğru çalışması için ve ürün hasarı oluşmasını engellemek için:

* Cihaz çalışırken ön ve arka kapıların tamamen açık olduğundan emin olun.

## <a name="regulatory-information"></a>Mevzuat bilgileri

Bu bölüm Azure Data Box, mevzuat model numarası DB010 için yasal bilgiler içerir.

Bu cihaz:

- Tipik bir veri odası ortamında çalışacak şekilde tasarlanan bilgi teknolojisi donanımı (ITE) olarak değerlendirilir. Bu ürünün diğer ortamlar için uygunluğu daha fazla değerlendirme gerektirebilir.
- NRTL listelenen (UL, CSA, ETL, vb.) ve ıEC/EN 60950-1 ya da ıEC/EN 62368-1 uyumlu (CE işaretli) Information Technology donatısı ile kullanılmak üzere tasarlanmıştır.
- Aşağıdaki ortamda çalışmak üzere tasarlanmıştır. 
    - İşletim sıcaklığı: 50 ° ila 95 ° F (10 ° ila 35 ° C)
    - Depolama sıcaklığı:-4 ° ila 122 ° F (-20 ° ila 50 ° C)
    - Bağıl nem: %15-%85 (yoğunlaşmayan) 
    - İşletim yüksekliği: 6500 fit 'e kadar (0 ölçüm ile 2000 metreye) test edilmiştir

Elektrik tedarik derecelendirmeleri için, birimiyle birlikte sunulan cihaz derecelendirme etiketine bakın. 

![Bildirim simgesi ](./media/data-box-safety/notice_icon.png) **bildirimi:** 

Cihazda yapılan değişiklikler veya değişiklikler Microsoft tarafından açıkça onaylanmamış olarak, kullanıcının cihazı çalıştırma yetkisi yok edebilir.

**Kanada ve ABD:**

![Bildirim simgesi ](./media/data-box-safety/notice_icon.png) **bildirimi:** 

Bu ekipman test edilmiştir ve bir sınıftaki dijital bir cihaz için sınırlara uyum sağlamak üzere, FCC kuralların 15. bölümünde yer almışlardır. Bu sınırlar, ekipman ticari bir ortamda çalıştırıldığında zararlı girişim için makul bir koruma sağlamak üzere tasarlanmıştır. Bu ekipman radyo sıklığı enerjisi oluşturur ve kullanır ve yönerge kılavuzuna uygun şekilde yüklenip kullanılmayacaksa radyo iletişimine zararlı girişim yapılmasına neden olabilir. Bu ekipmanın bir yöresel alanındaki işlemi, zararlı bir girişim oluşmasına neden olur ve bu durumda kullanıcının, girişim kendi masrafına göre düzeltilmesi gerekecektir.

Bu cihaz, FCC kuralları ve sektör Kanada lisans muafiyeti RSS standardının 15. bölümüne uyar. İşlem şu iki koşula tabidir: (1) Bu cihaz zararlı bir girişim oluşmasına neden olabilir ve (2) Bu cihaz, cihazın istenmeyen çalışmasına neden olabilecek girişim dahil olmak üzere, alınan bir girişimi kabul etmelidir.

![Ekran görüntüsünde, Kanada için gereken bir bildirim gösterilir.](./media/data-box-safety/canada.png)

ICES-3 (A)/NMB-3 (A)

Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, ABD.

Birleşik Devletler: (800) 426-9400

Kanada: (800) 933-4750

**AVRUPA BIRLIĞI:**

[Conformity 'ın AB bildiriminin bir kopyasını isteyin](mailto:CSI_Compliance@microsoft.com).

![Uyarı simgesi ](./media/data-box-safety/warning_icon.png) **uyarısı:** 

Bu bir ürün sınıfındır. Yurtiçi bir ortamda, bu ürün radyo girişimine neden olabilir ve bu durumda kullanıcının yeterli ölçüler yapması gerekebilir.

**Çöp pillerinin ve elektrik ve elektronik ekipmanın elden çıkarılması:**

![Pil çıkarma simgesi](./media/data-box-safety/battery_disposal_icon.png)

Üründe veya pillerinde bulunan bu sembol, bu ürünün ve içerdiği tüm pillerin, ev isverünüzle birlikte atılmayacağı anlamına gelir. Bunun yerine, pil ve elektrik ve elektronik ekipmanın geri dönüştürülmesi için bunu ilgili bir koleksiyon noktasına ele almak sizin sorumluluğunuzdadır. Bu ayrı koleksiyon ve geri dönüşüm, doğal kaynakların korunmasına yardımcı olur ve pillerde ve elektrik ve elektronik ekipmanda olası tehlikeli bir etkinlik olması nedeniyle, uygun olmayan bir elden çıkarılmasından kaynaklanabilir. Pillerinizi ve elektrik ve elektronik alımlarınızı nereden kapatabileceğiniz hakkında daha fazla bilgi için lütfen yerel şehriniz/municiplik ofisiniz, ev atık servisi hizmeti veya bu ürünü satın aldığınız mağaza ekibine başvurun. WEEE hakkında daha fazla bilgi için *erecycle \@ Microsoft.com* başvurun.

Bu ürün, para hücresi pili (lar) içerir.

Microsoft Irlanda Sandyford ınest Dublin D18 KX32 Gu

Telefon numarası: + 353 1 295 3826

Faks numarası: + 353 1 706 4110 

**Japonya**

![Ekran görüntüsünde, Japonya için gereken bir bildirim gösterilir.](./media/data-box-safety/japan.png)

<!--**South Korea**

![South Korea](./media/data-box-safety/south-korea.png)

**China RoHS Hazardous Substances Table**

![China RoHS hazardous substances](./media/data-box-safety/south-korea.png)

**Taiwan**

![Taiwan](./media/data-box-safety/taiwan.png)-->

Bu güvenlik bildirimlerini inceledikten sonra cihazınızı ayarlayabilir ve dağıtabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Kablo ve Data Box bağlama](data-box-deploy-set-up.md)


