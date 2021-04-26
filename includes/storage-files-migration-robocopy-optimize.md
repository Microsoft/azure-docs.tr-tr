---
title: include dosyası
description: include dosyası
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 4/05/2021
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 57d14ae6e6da7cfa883f1aed74cce390c0185d98
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491704"
---
Belirli bir RoboCopy çalışmasının hız ve başarı oranı, çeşitli faktörlere bağlıdır:

* Kaynak ve hedef depolamada ıOPS
* Kaynak ve hedef arasındaki kullanılabilir ağ bant genişliği
* bir ad alanındaki dosyaları ve klasörleri hızlı bir şekilde işleyebilme
* RoboCopy çalıştırmaları arasındaki değişiklik sayısı


### <a name="iops-and-bandwidth-considerations"></a>IOPS ve bant genişliği konuları

Bu kategoride, **kaynak depolamanın**, **hedef depolamanın** ve bunları bağlayan **ağın** yeteneklerini göz önünde bulundurmanız gerekir. Olası en yüksek aktarım hızı, bu üç bileşenden en yavaş şekilde belirlenir. Ağ altyapınızın en iyi yeteneklere en iyi aktarım hızlarını destekleyecek şekilde yapılandırıldığından emin olun.

> [!CAUTION]
> Mümkün olduğunca hızlı kopyalama genellikle en çok desibleken, genellikle iş açısından kritik görevler için yerel ağınızın ve NAS gerecinin kullanımını göz önünde bulundurun.

Mümkün olduğunca hızlı kopyalama, geçişin kullanılabilir kaynakları tekeline geçirebilmesini gerektiren bir risk olduğunda istenmeyebilir.

* Geçiş çalıştırmak için ortamınızda en iyi şekilde geçiş yapmayı düşünün: gün, saat kapalı veya hafta sonları sırasında.
* Ayrıca, RoboCopy hızını kısıtlamak için bir Windows Server üzerinde ağ QoS 'yi de göz önünde bulundurun.
* Geçiş araçları için gereksiz çalışmaktan kaçının.

RobCopy, `/IPG:n` `n` Robocopy paketleri arasında milisaniye cinsinden ölçülen anahtarı belirterek paket arası gecikmeler ekleyebilir. Bu anahtarı kullanmak, hem GÇ kısıtlı cihazlarda hem de kalabalık ağ bağlantılarında kaynakların tek bir şekilde oluşmasını önlemeye yardımcı olabilir.

`/IPG:n` belirli bir MB/sn olan kesin ağ azaltma için kullanılamaz. Bunun yerine Windows Server Network QoS kullanın. RoboCopy, tüm ağ gereksinimleriniz için SMB protokolüne tamamen dayanır. SMB kullanmanın, RoboCopy 'nin ağ aktarım hızını etkilemesinin nedeni, ancak kullanımını yavaşlatabilir. 

Benzer bir düşünme satırı, NAS üzerinde gözlemlenen ıOPS için geçerlidir. NAS birimi, paket boyutları ve diğer faktörlerdeki küme boyutu, gözlemlenen ıOPS 'yi etkiler. Paket içi gecikmeye giriş, genellikle NAS üzerindeki yükü denetleyen en kolay yoldur. Birden çok değeri, örneğin yaklaşık 20 milisaniyeye (n = 20) bu sayının katları ile test edin. Bir gecikmeyle karşılaşırsanız, diğer uygulamalarınızın artık beklendiği gibi çalıştığını değerlendirebilirsiniz. Bu iyileştirme stratejisi, ortamınızda en iyi RoboCopy hızını bulmanıza olanak sağlayacak.

### <a name="processing-speed"></a>İşlem hızı

RoboCopy, işaret ettiği ad alanını gezer ve kopya için her dosya ve klasörü değerlendirir. Her dosya, ilk kopyalama sırasında ve yakalama kopyaları sırasında değerlendirilir. Örneğin, RoboCopy/MıR 'nin aynı kaynak ve hedef depolama konumlarına karşı yinelenen çalıştırmaları. Bu yinelenen çalıştırmalar, kullanıcılar ve uygulamalar için kapalı kalma süresini en aza indirmek ve geçirilen dosyaların genel başarı oranını artırmak için kullanışlıdır.

Genellikle, bir geçişte en sınırlayıcı faktör olarak bant genişliğini göz önünde bulunduruyoruz ve bu değer doğru olabilir. Ancak bir ad alanını listeleme özelliği, daha küçük dosyalarla daha büyük ad alanları için daha da fazla kopya kopyalamak üzere toplam süreyi etkileyebilir. Küçük dosyaları kopyalamanın 1 TiB ' nin daha az ancak daha büyük dosya kopyalamaktan önemli ölçüde uzun sürmekte olduğunu düşünün. Diğer tüm değişkenlerin aynı kaldığı varsayılarak.

Bu farkın nedeni, bir ad alanı üzerinde gezinmek için gereken işleme gücünden sorumludur. RoboCopy, `/MT:n` işlemci iş parçacığı sayısı için n tarafından temsil edildiği parametresi aracılığıyla çok iş parçacıklı kopyaları destekler. Bu nedenle, özellikle RoboCopy için bir makine sağlanırken, işlemci çekirdeği sayısını ve bunların sağladıkları iş parçacığı sayısıyla ilişkilerini göz önünde bulundurun. En yaygın olarak çekirdek başına iki iş parçacığı bulunur. Bir makinenin çekirdek ve iş parçacığı sayısı, hangi çoklu iş parçacığı değerlerini belirtmeniz gerektiğine karar vermek için önemli bir veri noktasıdır `/MT:n` . Ayrıca, belirli bir makinede paralel olarak çalıştırmayı planladığınız kaç RoboCopy işi de göz önünde bulundurun.

Daha fazla iş parçacığı, küçük dosya örneğimizi daha az iş parçacığından önemli ölçüde daha hızlı kopyalayacaktır. Aynı zamanda, 1 TiB daha büyük dosyalardaki ek kaynak yatırımı orantılı avantajlar kazanmayabilir. Yüksek iş parçacığı sayısı ağ üzerinde aynı anda büyük dosyaların daha fazlasını kopyalamaya çalışır. Bu ek ağ etkinliği, verimlilik veya depolama ıOPS ile sınırlı olma olasılığını artırır.

### <a name="avoid-unnecessary-work"></a>Gereksiz çalışmayı önleyin

Ad uzayındaki büyük ölçekli değişikliklerden kaçının. Örneğin, dizinler arasında dosya taşıma, büyük ölçekte özellikleri değiştirme veya izinleri değiştirme (NTFS ACL 'Leri). Genellikle, klasör hiyerarşisinde daha düşük dosyalar üzerinde basamaklı bir değişiklik etkisi olduğundan, özellikle ACL değişikliklerinin yüksek bir etkisi olabilir. Sonuçlar şu olabilir:

* bir ACL değişikliğinden etkilenen her dosya ve klasörün güncelleştirilmesi gerektiğinden genişletilmiş RoboCopy işi çalışma süresi
* daha önce taşınan verileri yeniden kullanmak için yeniden kopyalanması gerekebilir. Örneğin, dosyalar daha önce kopyalandıktan sonra klasör yapıları değiştiğinde daha fazla verinin kopyalanması gerekir. Bir RoboCopy işi, bir ad alanı değişikliğini "yeniden yürütemiyor". Sonraki iş daha önce eski klasör yapısına taşınan dosyaları temizlemelidir ve yeni klasör yapısındaki dosyaları yeniden karşıya yükler.

Diğer önemli bir boyut, RoboCopy aracını etkin bir şekilde kullanmaktır. Önerilen RoboCopy betiği ile hatalar için bir günlük dosyası oluşturup kaydedebilirsiniz. Kopyalama hataları gerçekleşebilir-Bu normaldir. Bu hatalar genellikle RoboCopy gibi bir kopyalama aracının birden fazla boyutunu çalıştırmanın gerekli hale getirir. İlk çalıştırma, bir NAS 'dan DataBox veya bir sunucudan Azure dosya paylaşımının olduğunu varsayalım. Ve bir veya daha fazla ek, bir veya daha fazla sayıda, kopyalanamayan dosyaları yakalamak ve yeniden denemek için/MıR anahtarıyla çalışır.

Belirli bir ad alanı kapsamında RoboCopy 'nin birden çok sayısını çalıştırmaya hazır olmanız gerekir. Daha kısa bir sürede kopyalamak daha hızlı tamamlanır, ancak ad alanı işleme hızına göre daha fazla kısıtlanmıştır. Birden çok sayıyı çalıştırdığınızda, RoboCopy her bir turun belirli bir çalıştırmada her şeyi kopyalamaya makul bir şekilde gerek kalmadan hızlandıramaz. Bu RoboCopy anahtarları önemli bir farklılık yapabilir:

* `/R:n` n = başarısız bir dosyayı kopyalamayı ne sıklıkta yeniden deneirsiniz ve 
* `/W:n` n = yeniden denemeler arasında beklenecek saniye sayısı

`/R:5 /W:5` , dilediğiniz şekilde ayarlayabileceğiniz makul bir ayardır. Bu örnekte, başarısız olan bir dosya beş kez yeniden denenecektir ve yeniden denemeler arasında beş saniyelik bekleme süresi vardır. Dosya hala kopyalayamazsa, sonraki RoboCopy işi yeniden dener. Genellikle, kullanımda olduklarından veya zaman aşımı sorunları nedeniyle başarısız olan dosyalar bu şekilde başarıyla kopyalanabilir.
   
