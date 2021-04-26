---
title: StorSimple yerel olarak sabitlenmiş birimler hakkında SSS | Microsoft Docs
description: StorSimple yerel olarak sabitlenmiş birimler hakkında sık sorulan soruların yanıtlarını sağlar.
services: storsimple
documentationcenter: NA
author: manuaery
manager: syadav
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2017
ms.author: manuaery
ms.openlocfilehash: 483fa81b409e1bd740af85b431a86b6c814831e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96002723"
---
# <a name="storsimple-locally-pinned-volumes-frequently-asked-questions-faq"></a>StorSimple yerel olarak sabitlenmiş birimler: sık sorulan sorular (SSS)
## <a name="overview"></a>Genel Bakış
Aşağıda, StorSimple yerel olarak sabitlenmiş bir birim oluşturduğunuzda, katmanlı bir birimi yerel olarak sabitlenmiş bir birime (ve tam tersi) dönüştürebileceğiniz ya da yerel olarak sabitlenmiş bir birimi yedeklemeniz ve geri yüklemeniz gerekebilecek sorular ve yanıtlar verilmiştir.

Sorular ve yanıtlar aşağıdaki kategorilerde düzenlenir

* Yerel olarak sabitlenmiş birim oluşturma
* Yerel olarak sabitlenmiş bir yedekleme
* Katmanlı bir birimi yerel olarak sabitlenmiş bir birime dönüştürme
* Yerel olarak sabitlenmiş bir birimi geri yükleme
* Yerel olarak sabitlenmiş bir birimin üzerinden yük devrediliyor

## <a name="questions-about-creating-a-locally-pinned-volume"></a>Yerel olarak sabitlenmiş birim oluşturma hakkında sorular
**Ç.** 8000 serisi cihazlarda oluşturabileceğiniz, yerel olarak sabitlenmiş bir birimin en büyük boyutu nedir?

 Storsimple 8000 serisi güncelleştirme 3,0 çalıştıran cihazlarda, 8100 CIHAZıNDA 200 TB 'a kadar, yerel olarak sabitlenmiş birimler IÇIN 8,5 TB veya katmanlı birimler sağlayabilirsiniz. Daha büyük olan 8600 cihazında 22,5 TB'a kadar yerel olarak sabitlenmiş birim ya da 500 TB'a kadar katmanlı birim sağlayabilirsiniz.

**Ç.** 8100 cihazımı son zamanlarda güncelleştirme 3,0 ' ye yükseltiyorum ve yerel olarak sabitlenmiş bir birim oluşturmayı denediğimde, kullanılabilir maksimum boyut yalnızca 6 TB ve 8,5 TB değil. Neden 8,5 TB 'lık birim oluşturamıyorum?

**Bir** cihazınız güncelleştirme 3,0 çalıştırıyorsa, 8100 CIHAZıNDA 8,5 TB 'A kadar veya katmanlı 200 birimlere göre yerel olarak sabitlenmiş birimler sağlayabilirsiniz. Cihazınızın katmanlı birimleri zaten varsa, yerel olarak sabitlenmiş bir birim oluşturmak için kullanılabilir alan bu maksimum sınırdan daha düşük bir değer olacaktır. Örneğin, 8100 cihazınızda yaklaşık 106 TB 'lık katmanlı birim zaten sağlanmışsa (katmanlı kapasitenin yarısı), bu durumda, 8100 cihazında oluşturabileceğiniz bir yerel birimin en büyük boyutu 4 TB 'a indirgenecek (en büyük yerel olarak sabitlenmiş birim kapasitesinin kabaca yarısı).

Cihazdaki bazı yerel alan, katmanlı birimlerin çalışma kümesini barındırmak için kullanıldığından, cihazda katmanlı birimler varsa, yerel olarak sabitlenmiş bir birim oluşturmak için kullanılabilir alan azalır. Buna karşılık, yerel olarak sabitlenmiş bir birimin oluşturulması katmanlı birimler için kullanılabilir alanı azaltır. Aşağıdaki tablolar, yerel olarak sabitlenmiş birimler oluşturulduğunda 8100 ve 8600 cihazlarındaki kullanılabilir katmanlı kapasiteyi özetler.

#### <a name="update-30"></a>Güncelleştirme 3,0 

| Yerel olarak sabitlenmiş birimler sağlanan kapasite | Katmanlı birimlerde sağlanmak üzere kullanılabilir kapasite-8100 | Katmanlı birimlerde sağlanmak üzere kullanılabilir kapasite-8600 |
| --- | --- | --- |
| 0 |200 TB |500 TB |
| 1 TB |176,5 TB |477,8 TB |
| 4 TB |105,9 TB |411,1 TB |
| 8,5 TB |0 TB |311,1 TB |
| 10 TB |NA |277,8 TB |
| 15 TB |NA |166,7 TB |
| 22,5 TB |NA |0 TB |

**Ç.** Yerel olarak sabitlenmiş birim oluşturma uzun süre çalışan bir işlem mi?

**A.** Yerel olarak sabitlenmiş birimler bol alanla sağlandı. Cihazın yerel katmanlarında alan oluşturmak için, mevcut katmanlı birimlerden bazı veriler sağlama işlemi sırasında buluta itilmiş olabilir. Bu, sağlanan birimin boyutuna, cihazınızdaki mevcut verilere ve buluta yönelik kullanılabilir bant genişliğine bağlı olduğundan, yerel bir birim oluşturmak için geçen süre birkaç saat olabilir.

**Ç.** Yerel olarak sabitlenmiş bir birim oluşturmak ne kadar sürer?

**A.** Yerel olarak sabitlenmiş birimler bol alanla sağlandığı için, katmanlı birimlerden bazı mevcut veriler sağlama sürecinde buluta itilmiş olabilir. Bu nedenle, yerel olarak sabitlenmiş bir birim oluşturmak için harcanan süre birimin boyutu, cihazınızdaki veriler ve kullanılabilir bant genişliği dahil olmak üzere birden çok etkene bağlıdır. Birimi olmayan, daha önce yüklenmiş bir cihazda, yerel olarak sabitlenmiş bir birim oluşturma süresi, terabaytlık veri başına yaklaşık 10 dakikadır. Ancak, yerel birimlerin oluşturulması, yukarıda açıklanan faktörlere bağlı olarak, kullanımda olan bir cihazda birkaç saat sürebilir.

**Ç.** Yerel olarak sabitlenmiş bir birim oluşturmak istiyorum. Farkında olmam gereken en iyi uygulamalar var mı?

**A.** Yerel olarak sabitlenmiş birimler, her zaman veri garantisi gerektiren ve bulut gecikme sürelerine duyarlı olan iş yükleri için uygundur. İş yüklerinizden herhangi biri için yerel birimlerin kullanımını göz önünde bulundurarak, lütfen aşağıdakilere dikkat edin:

* Yerel olarak sabitlenmiş birimler bol alanla sağlandı ve yerel birimler oluşturmak katmanlı birimler için kullanılabilir alanı etkiler. Bu nedenle, daha küçük ölçekli hacimlerle başlayıp depolama gereksiniminin arttığı sürece ölçeği artırdık.
* Yerel birimlerin sağlanması, mevcut verilerin katmanlı birimlerden buluta gönderilmesi gerekebilecek uzun süredir çalışan bir işlemdir. Sonuç olarak, bu birimlerde daha düşük performans yaşayabilirsiniz.
* Yerel birimlerin sağlanması zaman alan bir işlemdir. İlgili gerçek süre, birden fazla etkene bağlıdır: sağlanmakta olan birimin boyutu, cihazınızdaki veriler ve kullanılabilir bant genişliği. Mevcut birimlerinizi buluta yedeklediniz, birim oluşturma daha yavaştır. Yerel bir birim sağlamadan önce var olan birimlerinizin bulut anlık görüntülerini almanızı öneririz.
* Var olan katmanlı birimleri yerel olarak sabitlenmiş birimlere dönüştürebilirsiniz ve bu dönüştürme, yerel olarak sabitlenmiş birim için cihazdaki alanın sağlanmasını içerir (varsa, bu, kuruluşunuzda katmanlı verileri getirmek için ek olarak). Bu, yukarıda ele aldığımız faktörlere bağlı olarak uzun süren bir işlemdir. Mevcut birimler yedeklenmemişse, işlem daha yavaş olacak şekilde dönüştürmeden önce mevcut birimlerinizi yedeklemenizi öneririz. Cihazınız Ayrıca bu işlem sırasında düşük performans ile karşılaşabilir.

[Yerel olarak sabitlenmiş birim oluşturma](storsimple-8000-manage-volumes-u2.md#add-a-volume) hakkında daha fazla bilgi

**Ç.** Aynı anda birden çok yerel olarak sabitlenmiş birim oluşturabilir miyim?

**A.** Evet, ancak yerel olarak sabitlenmiş birim oluşturma ve genişletme işleri sırayla işlenir.

Yerel olarak sabitlenmiş birimler bol alanla sağlandı ve bu, cihazda yerel alan oluşturulmasını gerektirir (Bu, katmanlı birimlerden mevcut verilerin sağlama işlemi sırasında buluta gönderilmesine neden olabilir). Bu nedenle, bir sağlama işi devam ediyorsa, diğer yerel birim oluşturma işleri bu iş tamamlanana kadar sıraya alınır.

Benzer şekilde, varolan bir yerel birim genişletilir veya katmanlı bir birim yerel olarak sabitlenmiş bir birime dönüştürülürse, önceki iş tamamlanana kadar yeni bir yerel olarak sabitlenmiş birimin oluşturulması sıraya alınır. Yerel olarak sabitlenmiş bir birimin boyutunu genişletmek, o birim için var olan yerel alanın genişlemesine yöneliktir. Katmanlı ve yerel olarak sabitlenmiş bir birime dönüştürme, sonuçta elde edilen yerel olarak sabitlenmiş birim için yerel alan oluşturulmasını da içerir. Bu işlemlerin her ikisinde de yerel alanın oluşturulması veya genişletilmesi uzun süredir çalışan bir iş.

Bu işleri StorSimple Device Manager hizmetinin **işler** dikey penceresinde görüntüleyebilirsiniz. Etkin olarak işlenen iş, alan sağlama işleminin ilerlemesini yansıtacak şekilde sürekli olarak güncelleştirilir. Kalan yerel olarak sabitlenmiş birim işleri çalışıyor olarak işaretlenir, ancak ilerleme durumu durduruldu ve bunlar kuyruklanmış sırada çekilir.

**Ç.** Yerel olarak sabitlenmiş bir birimi sildim. Yeni bir birim oluşturmayı denediğimde, geri kazanılan alanı neden kullanılabilir alana yansıtmalıyım?

**A.** Yerel olarak sabitlenmiş bir birimi silerseniz, yeni birimler için kullanılabilir alan hemen güncelleştirilmemiş olabilir. StorSimple Device Manager hizmeti, her saat yaklaşık olarak kullanılabilir yerel alanı güncelleştirir. Yeni birimi oluşturmayı denemeden önce bir saat beklemeniz önerilir.

**Ç.** Bulut gerecinde yerel olarak sabitlenmiş birimler destekleniyor mu?

**A.** Yerel olarak sabitlenmiş birimler, bulut gerecinde (daha önce StorSimple Sanal cihazı olarak adlandırılan 8010 ve 8020 cihazlarda) desteklenmez.

**Ç.** Azure PowerShell cmdlet 'lerini yerel olarak sabitlenmiş birimler oluşturmak ve yönetmek için kullanabilir miyim?

**A.** Hayır, Azure PowerShell cmdlet 'leri aracılığıyla yerel olarak sabitlenmiş birimler oluşturamazsınız (Azure PowerShell aracılığıyla oluşturduğunuz herhangi bir birim katmanlı olur). Ayrıca, yerel olarak sabitlenmiş bir birimin özelliklerini değiştirmek için Azure PowerShell cmdlet 'lerini kullanarak birim türünü katmanlı olarak değiştirmenin istenmeyen bir etkisi olacaktır.

## <a name="questions-about-backing-up-a-locally-pinned-volume"></a>Yerel olarak sabitlenmiş bir birimi yedekleme hakkında sorular
**Ç.** Yerel olarak sabitlenmiş birimlerin yerel anlık görüntüleri destekleniyor mu?

**A.** Evet, yerel olarak sabitlenmiş birimlerinizin yerel anlık görüntülerini alabilirsiniz. Bununla birlikte, verilerinizin olağanüstü durum durumunda korunmasını sağlamak için, yerel olarak sabitlenmiş birimlerinizi bulut anlık görüntüleriyle düzenli olarak yedeklemeniz önemle tavsiye ederiz.

Yerel olarak sabitlenmiş birimlerin yerel anlık görüntülerinin de buluta katman katmadığını ve cihazın yerel katmanında kalmayabileceğini unutmayın.

**Ç.** Yerel görüntüleri yerel olarak sabitlenmiş birimlerde yönetmeye yönelik yönergeler var mı?

**A.** Yerel olarak sabitlenmiş birimdeki yüksek oranda veri dalgalanmasına sahip sık kullanılan yerel anlık görüntüler, cihazdaki yerel alanın hızla tüketilmesine neden olabilir ve katmanlı birimlerden verilerin buluta gönderilmesine yol açabilir. Bu nedenle, yerel anlık görüntü sayısını en aza indirmenizi öneririz.

**Ç.** Yerel olarak sabitlenmiş birimlerin yerel anlık görüntülerimin geçersiz kılınmadığını belirten bir uyarı aldım. Bu durum ne zaman olabilir?

**A.** Yerel olarak sabitlenmiş birimdeki yüksek oranda veri dalgalanmasına sahip sık kullanılan yerel anlık görüntüler, cihazdaki yerel alanın hızla tüketilmesine neden olabilir. Cihazın yerel katmanları yoğun bir şekilde kullanılıyorsa, genişletilmiş bir bulut kesintisi cihazın tam hale gelmesine neden olabilir ve birime gelen yazma işlemleri anlık görüntülerin doğrulanmasına neden olabilir (anlık görüntülerin üzerine yazılmış eski veri bloklarına başvuracak bir alan olmadığından). Böyle bir durumda, birime yazma işlemleri sunulmaya devam eder, ancak yerel anlık görüntüler geçersiz olabilir. Mevcut bulut anlık görüntülerinizin bir etkisi yoktur.

Uyarı uyarısı, böyle bir durumun ortaya çıkabilecek ve daha az sık yerel anlık görüntüler almak veya artık gerekli olmayan eski yerel anlık görüntüleri silmek için yerel anlık görüntüleri inceleyerek aynı zamanda aynı şekilde adreslenebilmesini sağlamak için size bildirimde bulunur.

Yerel anlık görüntüler geçersiz kılındığında, belirli yedekleme ilkesi için yerel anlık görüntülerin geçersiz kılınan yerel anlık görüntülerin zaman damgalarıyla birlikte geçersiz kılındığından emin olarak bir bilgi uyarısı alırsınız. Bu anlık görüntüler otomatik olarak silinecek ve artık Azure portal, bu dosyaları artık **Yedekleme katalogları** dikey penceresinde görüntüleyemeyeceksiniz.

## <a name="questions-about-converting-a-tiered-volume-to-a-locally-pinned-volume"></a>Katmanlı bir birimi yerel olarak sabitlenmiş bir birime dönüştürme hakkında sorular
**Ç.** Katmanlı bir birimi yerel olarak sabitlenmiş bir birime dönüştürürken cihazda biraz yavaşlıyorum. Bu neden gerçekleşiyor?

**A.** Dönüştürme işlemi iki adımdan oluşur:

1. Yakında dönüştürülmüş yerel olarak sabitlenmiş birim için cihazda alan sağlama.
2. Yerel güvence garantisi sağlamak için buluttan katmanlı verileri indirme.

Bu adımların her ikisi de, dönüştürülen birimin boyutuna, cihazdaki verilere ve kullanılabilir bant genişliğine bağlı olan uzun süren işlemlerdir. Mevcut katmanlı birimlerden bazı veriler, sağlama sürecinin bir parçası olarak buluta taşımayabilir, bu süre boyunca cihazınız düşük performans ile karşılaşabilir. Ayrıca, dönüştürme işlemi şu durumlarda daha yavaş olabilir:

* Mevcut birimler buluta yedeklenmedi; Bu nedenle, bir dönüştürmeyi başlatmadan önce birimlerinizi yedeklemenizi öneririz.
* Bant genişliği azaltma ilkeleri uygulandı, bu, kullanılabilir bant genişliğini buluta kısıtlayabilir. Bu nedenle, buluta yönelik adanmış 40 Mbps veya daha fazla bağlantınız olması önerilir.
* Dönüştürme işlemi, yukarıda açıklanan birden çok etken nedeniyle birkaç saat sürebilir; Bu nedenle, son tüketicilerle ilgili etkiyi önlemek için bu işlemi, yoğun olmayan saatlerde veya bir hafta sonu sırasında gerçekleştirmenizi öneririz.

[Katmanlı bir birimin yerel olarak sabitlenmiş bir birime nasıl dönüştürüleceği](storsimple-8000-manage-volumes-u2.md#change-the-volume-type) hakkında daha fazla bilgi

**Ç.** Birim dönüştürme işlemini iptal edebilir miyim?

**A.** Hayır, başlatıldığında dönüştürme işlemini iptal edemezsiniz. Önceki soruda anlatıldığı gibi, lütfen işlem sırasında karşılaşabileceğiniz olası performans sorunlarından haberdar olun ve dönüştürmeyi planlarken yukarıda listelenen en iyi uygulamaları izleyin.

**Ç.** Dönüştürme işlemi başarısız olursa hacime ne olur?

**A.** Birim dönüştürme, bulut bağlantısı sorunları nedeniyle başarısız olabilir. Katmanlı verileri buluttan getirmek için bir dizi başarısız girişimden sonra, cihaz sonunda dönüştürme işlemini durdurabilir. Böyle bir senaryoda, birim türü dönüştürme öncesinde kaynak birim türü olmaya devam edecektir ve:

* Birim dönüştürme hatası hakkında sizi bilgilendirmek için kritik bir uyarı oluşturulur. [Yerel olarak sabitlenmiş birimlerle ilgili uyarılar](storsimple-8000-manage-alerts.md#locally-pinned-volume-alerts) hakkında daha fazla bilgi
* Katmanlı bir şekilde yerel olarak sabitlenmiş bir birime dönüştürüyorsanız, veriler bulutta yer alabiliyorsa, birim katmanlı bir birimin özelliklerini göstermeye devam eder. Bağlantı sorunlarını çözmenizi ve ardından dönüştürme işlemini yeniden denemeyi öneririz.
* Benzer şekilde, yerel olarak sabitlenmiş bir katmanlı birime dönüştürme işlemi başarısız olsa da birim yerel olarak sabitlenmiş bir birim olarak işaretlenir, ancak veri buluta atılanabileceğinden katmanlı birim olarak çalışır. Ancak, cihazın yerel katmanlarında alan kaplamasına devam edecektir. Bu alan, yerel olarak sabitlenmiş birimler için kullanılamaz. Birim dönüştürmenin tamamlandığından ve cihazdaki yerel alanın geri kazanılabileceğini sağlamak için bu işlemi yeniden denemenizi öneririz.

## <a name="questions-about-restoring-a-locally-pinned-volume"></a>Yerel olarak sabitlenmiş bir birimi geri yüklemeyle ilgili sorular
**Ç.** Yerel olarak sabitlenmiş birimler anında geri yüklendi mi?

**A.** Evet, yerel olarak sabitlenmiş birimler anında geri yüklenir. Birimin meta veri bilgileri geri yükleme işleminin bir parçası olarak buluttan çekiliyorsa, birim çevrimiçi duruma getirilir ve ana bilgisayar tarafından erişilebilir. Ancak, tüm veriler buluttan indirilene kadar birim verileri için yerel garantiler mevcut olmayacaktır ve geri yükleme süresince bu birimlerde daha düşük performans yaşayabilirsiniz.

**Ç.** Yerel olarak sabitlenmiş bir birimi geri yüklemek ne kadar sürer?

**A.** Yerel olarak sabitlenmiş birimler anında geri yüklenir ve birim meta veri bilgileri buluttan alındıktan hemen sonra çevrimiçi hale getirilir ve birim verileri arka planda indirilmeye devam eder. Geri yükleme işleminin bu ikinci bölümü--birim verileri için yerel garantisini geri alma--uzun süren bir işlemdir ve tüm verilerin yerel olarak yeniden yapılması birkaç saat sürebilir. Aynı işleminin tamamlanma süresi, geri yüklenmekte olan birimin boyutu ve kullanılabilir bant genişliği gibi birden çok etkene bağlıdır. Geri yüklenen orijinal birim silinmişse, geri yükleme işleminin bir parçası olarak cihazdaki yerel alanı oluşturmak için ek süre alınacaktır.

**Ç.** Mevcut yerel olarak sabitlenmiş birimimi eski bir anlık görüntüye geri yükleme yapmam gerekir (birim katmanlı olduğunda alınır). Birim bu durumda katmanlı olarak geri yüklenecek mi?

**A.** Hayır, birim yerel olarak sabitlenmiş bir birim olarak geri yüklenir. Anlık görüntü tarihleri birimin katmanlı hale geldiğinde, var olan birimleri geri yüklerken, StorSimple her zaman diskte bulunan birimin türünü kullanır.

**Ç.** Yerel olarak sabitlenmiş birimimi son zamanlarda genişlettim, ancak artık birimin boyutu daha küçük olan bir saate geri yükleme yapmam gerekiyor. Geri yüklenecek geçerli birim yeniden boyutlandırılır ve geri yükleme tamamlandıktan sonra birimin boyutunu genişlemem gerekir mi?

**A.** Evet, geri yükleme birimi yeniden boyutlandırılacak ve geri yükleme tamamlandıktan sonra birimin boyutunu genişletmeniz gerekecektir.

**Ç.** Geri yükleme sırasında bir birimin türünü değiştirebilir miyim?

**A.** Hayır, geri yükleme sırasında birim türünü değiştiremezsiniz.

* Silinmiş birimler, anlık görüntüde depolanan tür olarak geri yüklenir.
* Mevcut birimler, anlık görüntüde depolanan türden bağımsız olarak geçerli türlerine göre geri yüklenir (önceki iki soruya bakın).

**Ç.** Yerel olarak sabitlenmiş birimimi geri yükleme yapmam gerekir, ancak zaman içinde yanlış bir nokta anlık görüntüsü aldım. Geçerli geri yükleme işlemini iptal edebilir miyim?

**A.** Evet, bir devam eden geri yükleme işlemini iptal edebilirsiniz. Birimin durumu geri yüklemenin başlangıcında duruma geri alınacaktır. Ancak geri yükleme devam ederken birimde yapılan tüm yazma işlemleri kaybedilir.

**Ç.** Yerel olarak sabitlenmiş birimlerimin birinde bir geri yükleme işlemi başlatıyorum ve şimdi kapsam kataloğum içinde kullanıp oluşturmadığım bir anlık görüntü görüyorum. Bu ne için kullanılır?

**A.** Bu, geri yükleme işleminden önce oluşturulan geçici anlık görüntüdür ve geri yüklemenin iptal edilmesi veya başarısız olması durumunda geri alma için kullanılır. Bu anlık görüntüyü silmeyin; geri yükleme tamamlandığında otomatik olarak silinir. Bu davranış, geri yükleme işiniz yalnızca yerel olarak sabitlenmiş birimler veya yerel olarak sabitlenmiş ve katmanlı birimler karışımı içeriyorsa meydana gelebilir. Geri yükleme işi yalnızca katmanlı birimler içeriyorsa, bu davranış gerçekleşmeyecektir.

**Ç.** Yerel olarak sabitlenmiş bir birimi kopyalayabilir miyim?

**A.** Evet, yazabilirsiniz. Ancak, yerel olarak sabitlenmiş birim varsayılan olarak katmanlı bir birim olarak kopyalanır. [Yerel olarak sabitlenmiş bir birimi kopyalama](storsimple-8000-clone-volume-u2.md) hakkında daha fazla bilgi

## <a name="questions-about-failing-over-a-locally-pinned-volume"></a>Yerel olarak sabitlenmiş bir birimin yük devretme hakkında sorular
**Ç.** Cihazımın yükünü başka bir fiziksel cihaza devreder. Yerel olarak sabitlenmiş birimlerimin yerel olarak sabitlenmiş veya katmanlı olarak yük devretme yapılsın mı?

**A.** Hedef cihaz StorSimple 8000 serisi güncelleştirme 3 veya üstünü çalıştırıyorsa, yerel olarak sabitlenmiş birimler yerel olarak sabitlenmiş olarak yük devretmez.

[Sürümler arasında yerel olarak sabitlenmiş birimlerin yük devretmesi ve Dr](storsimple-8000-device-failover-disaster-recovery.md#device-failover-across-software-versions) hakkında daha fazla bilgi

**Ç.** Yerel olarak sabitlenmiş birimler olağanüstü durum kurtarma (DR) sırasında anında geri yüklendi mi?

**A.** Evet, yerel olarak sabitlenmiş birimler yük devretme sırasında anında geri yüklenir. Birime ait meta veri bilgileri yük devretme işleminin bir parçası olarak buluttan çekiliyorsa, birim hedef cihaza çevrimiçi hale getirilir ve konak tarafından erişilebilir. Bu arada, birim verileri arka planda indirilmeye devam eder ve yük devretme süresince bu birimlerde daha düşük performans yaşayabilirsiniz.

**Ç.** Yük devretme işini tamamladım, hedef cihaza geri yüklenmekte olan yerel olarak sabitlenmiş birimin ilerlemesini nasıl izleyebilirim?

**A.** Yük devretme işlemi sırasında, yük devretme kümesindeki tüm birimler anında geri yüklendikten ve hedef cihazda çevrimiçi duruma getirildikten sonra yük devretme işi tamam olarak işaretlenir. Bu, yük devredilebilecek yerel olarak sabitlenmiş birimleri içerir; Ancak, verilerin yerel garantisi yalnızca birim için tüm veriler indirildiyse kullanılabilir olur. Yük devretmenin bir parçası olarak oluşturulan ilgili geri yükleme işlerini izleyerek, yük devredilen her yerel olarak sabitlenmiş birim için bu ilerlemeyi izleyebilirsiniz. Bu tek tek geri yükleme işleri yalnızca yerel olarak sabitlenmiş birimler için oluşturulur.

**Ç.** Yük devretme sırasında bir birimin türünü değiştirebilir miyim?

**A.** Hayır, yük devretme sırasında birim türünü değiştiremezsiniz. StorSimple 8000 serisi güncelleştirme 3 ' ü çalıştıran başka bir fiziksel cihaza yük devrettikten sonra, anlık görüntüde depolanan birim türüne göre birimler yük devrediliyor.

**Ç.** Bulut gerecine yerel olarak sabitlenmiş birimler içeren bir birim kapsayıcısının yükünü devreder miyim?

**A.** Evet, yazabilirsiniz. Yerel olarak sabitlenmiş birimler katmanlı birimler olarak yük devretmez. [Sürümler arasında yerel olarak sabitlenmiş birimlerin yük devretmesi ve Dr](storsimple-8000-device-failover-disaster-recovery.md#common-considerations-for-device-failover) hakkında daha fazla bilgi

