---
title: Azure Load Balancer giden bağlantılarda sorun giderme
description: Azure Load Balancer aracılığıyla giden bağlantılarla ilgili sık karşılaşılan sorunlara yönelik çözümler.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 05/7/2020
ms.author: errobin
ms.openlocfilehash: 1f52900086afef09d69b80bf44474d5514e25235
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748889"
---
# <a name="troubleshooting-outbound-connections-failures"></a><a name="obconnecttsg"></a> Giden bağlantı hatalarıyla ilgili sorunları giderme

Bu makale, bir Azure Load Balancer giden bağlantılarla oluşabilecek yaygın sorunlara yönelik çözümler sağlamaya yöneliktir. Müşterilerin deneyimlerine giden bağlantı ile ilgili çoğu sorun, SNAT bağlantı noktası tükenmesi ve bırakılan paketlere yönelik bağlantı zaman aşımları nedeniyle yapılır. Bu makalede, bu sorunların her birini azaltmaya yönelik adımlar sağlanmaktadır.

## <a name="managing-snat-pat-port-exhaustion"></a><a name="snatexhaust"></a> SNAT (PAT) bağlantı noktası tükenmesi yönetimi
[Pat](load-balancer-outbound-connections.md) Için kullanılan [kısa ömürlü bağlantı noktaları](load-balancer-outbound-connections.md) , genel IP [adresi olmayan tek BAŞıNA VM](load-balancer-outbound-connections.md) 'de ve [genel IP adresi olmayan yük dengeli VM](load-balancer-outbound-connections.md)'de açıklandığı gibi, tüketilmeyen bir kaynaktır. [Bu](./load-balancer-standard-diagnostics.md#how-do-i-check-my-snat-port-usage-and-allocation) Kılavuzu kullanarak, daha kısa ömürlü bağlantı noktaları kullanımınızı izleyebilir ve geçerli ayırınızla karşılaştırabilir.

Aynı hedef IP adresine ve bağlantı noktasına giden çok sayıda giden TCP veya UDP bağlantısı başlattığdığınızı ve başarısız olmuş bağlantıları gözlemlebildiğinizi veya SNAT bağlantı noktalarını ( [Pat](load-balancer-outbound-connections.md)tarafından kullanılan önceden ayrılan [kısa ömürlü bağlantı noktaları](load-balancer-outbound-connections.md#preallocatedports) ) tüketmenin bir şekilde önerdiğini biliyorsanız, çeşitli genel risk azaltma seçenekleriniz vardır. Bu seçenekleri gözden geçirin ve senaryonuz için nelerin kullanılabilir ve en iyisi olduğuna karar verin. Bir veya daha fazla bu senaryonun yönetilmesine yardımcı olabilir.

Giden bağlantı davranışını anlamakta sorun yaşıyorsanız, IP yığın istatistiklerini (netstat) kullanabilirsiniz. Ya da paket yakalamaları kullanılarak bağlantı davranışlarını gözlemlemek faydalı olabilir. Bu paket yakalamalarını örneğinizin Konuk işletim sisteminde gerçekleştirebilir veya [paket yakalama Için Ağ İzleyicisi](../network-watcher/network-watcher-packet-capture-manage-portal.md)kullanabilirsiniz. 

## <a name="manually-allocate-snat-ports-to-maximize-snat-ports-per-vm"></a><a name ="manualsnat"></a>Sanal makine başına SNAT bağlantı noktalarını en üst düzeye çıkarmak için SNAT bağlantı noktalarını
[Önceden ayrılan bağlantı noktalarında](load-balancer-outbound-connections.md#preallocatedports)tanımlandığı gibi, yük dengeleyici arka uçtaki sanal makine sayısına göre bağlantı noktalarını otomatik olarak ayırır. Bu, varsayılan olarak ölçeklenebilirlik sağlamak için yapılır. Arka uçta sahip olduğunuz en fazla VM sayısını biliyorsanız, her giden kuralda SNAT bağlantı noktalarını el ile ayırabilirsiniz. Örneğin, en fazla 10 VM olacağını biliyorsanız, varsayılan 1.024 yerine VM başına 6.400 SNAT bağlantı noktası ayırabilirsiniz. 

## <a name="modify-the-application-to-reuse-connections"></a><a name="connectionreuse"></a>Bağlantıyı yeniden kullanmak için uygulamayı değiştirme 
Uygulamanızdaki bağlantıları yeniden kullandığınızda, SNAT için kullanılan kısa ömürlü bağlantı noktaları için talebi azaltabilirsiniz. Bağlantı yeniden kullanımı özellikle HTTP/1.1 gibi protokollerle ilgilidir, burada bağlantı yeniden kullanım varsayılandır. Ve taşıma olarak HTTP kullanan diğer protokoller (örneğin, REST) sırasıyla faydalanabilir. 

Her istek için bireysel, atomik TCP bağlantılarından yeniden kullanım her zaman daha iyidir. Sonuçları daha performanslı, çok verimli bir TCP işlemi ile yeniden kullanın.

## <a name="modify-the-application-to-use-connection-pooling"></a><a name="connection pooling"></a>Uygulamayı bağlantı havuzunu kullanacak şekilde değiştirme
Uygulamanızda, isteklerin dahili bir bağlantı kümesi arasında dahili olarak dağıtıldığı (mümkün olduğunda yeniden kullanıldığı) bir bağlantı havuzu oluşturabilirsiniz. Bu düzen, kullanımda olan kısa ömürlü bağlantı noktalarının sayısını kısıtlar ve daha öngörülebilir bir ortam oluşturur. Bu düzen, bir işlem yanıtında tek bir bağlantı yapıldığında birden çok eş zamanlı işleme izin vererek isteklerin verimini de artırabilir.  

Bağlantı havuzu, uygulamanızı geliştirmek için kullandığınız çerçeve içinde veya uygulamanızın yapılandırma ayarlarını zaten içerebilir. Bağlantı havuzunu bağlantı yeniden kullanımı ile birleştirebilirsiniz. Birden çok istekleriniz daha sonra aynı hedef IP adresi ve bağlantı noktası için sabit, öngörülebilir bir bağlantı noktası sayısı kullanır. İstekler, gecikme ve kaynak kullanımını azaltan TCP işlemlerinin verimli kullanımından de yararlanır. UDP işlemleri de yararlı olabilir, çünkü UDP akışlarının sayısını yönetmek, koşulları tüketmekten kaçınmak ve SNAT bağlantı noktası kullanımını yönetmektir.

## <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a><a name="retry logic"></a>Uygulamayı daha az agresif yeniden deneme mantığını kullanacak şekilde değiştirin
[Pat](load-balancer-outbound-connections.md) için kullanılan [kısa ömürlü bağlantı noktaları](load-balancer-outbound-connections.md#preallocatedports) tükendiğinde ya da uygulama hatalarından oluşması durumunda, Decay ve geri dönüş mantığı olmadan, agresif veya deneme yanılma denemesi kesintileri meydana gelir veya kalıcı hale getirme. Daha az bir agresif yeniden deneme mantığı kullanarak, kısa ömürlü bağlantı noktaları için talebi azaltabilirsiniz. 

Kısa ömürlü bağlantı noktalarında 4 dakikalık boşta kalma zaman aşımı (ayarlanamaz) vardır. Yeniden denemeler çok ısrarlı ise, tükenmenin kendi kendine temizleme olanağı yoktur. Bu nedenle, uygulamanızın yeniden deneme işlemleri, tasarımın kritik bir parçasıdır.

## <a name="assign-a-public-ip-to-each-vm"></a><a name="assignilpip"></a>Her VM 'ye genel IP atama
Genel IP adresi atamak, senaryonuzu [BIR VM 'ye genel IP](load-balancer-outbound-connections.md)'ye dönüştürür. Her VM için kullanılan genel IP 'nin tüm kısa ömürlü bağlantı noktaları, sanal makine için kullanılabilir. (Genel bir IP 'nin kısa ömürlü bağlantı noktaları, ilgili arka uç havuzuyla ilişkili tüm VM 'Ler ile paylaşıldığından senaryolar aksine.) Genel IP adreslerinin ek maliyeti ve çok sayıda ayrı IP adresini filtrelemeye yönelik olası etkileri gibi göz önünde bulundurulması gereken bir denge vardır.

>[!NOTE] 
>Bu seçenek Web çalışanı rolleri için kullanılamaz.

## <a name="use-multiple-frontends"></a><a name="multifesnat"></a>Birden çok ön uç kullan
Ortak Standart Load Balancer kullanırken, [giden bağlantılar için birden çok ön uç IP adresi](load-balancer-outbound-connections.md) atar ve [mevcut SNAT bağlantı noktası sayısını çarpın](load-balancer-outbound-connections.md#preallocatedports).  SNAT 'nin, ön uç 'nin genel IP 'si için bir ön uç IP yapılandırması, kuralı ve arka uç havuzu oluşturun.  Kuralın çalışması gerekmez ve sistem durumu araştırmasının başarılı olması gerekmez.  Gelen (yalnızca giden) için birden çok ön uç kullanırsanız, güvenilirliği sağlamak için özel sistem durumu araştırmalarını de kullanmanız gerekir.

>[!NOTE]
>Çoğu durumda, SNAT bağlantı noktalarının tükenmesi hatalı tasarımın bir imzadır.  SNAT bağlantı noktalarını eklemek için daha fazla ön ek kullanarak bağlantı noktalarını neden tüketdiğinizi anladığınızdan emin olun.  Daha sonra hataya neden olabilecek bir sorunu maskeleyerek bir sorun olabilir.

## <a name="scale-out"></a><a name="scaleout"></a>Ölçeği genişletme
[Önceden ayrılmış bağlantı noktaları](load-balancer-outbound-connections.md#preallocatedports) , arka uç havuzu boyutu temel alınarak atanır ve bir sonraki daha büyük arka uç havuzu boyut katmanına uyum sağlamak için bağlantı noktalarından bazılarının yeniden ayrılması gerektiğinde kesintiyi en aza indirmek için katmanlara gruplanırlar.  Arka uç havuzunuzu belirli bir katman için en büyük boyuta ölçeklendirerek, belirli bir ön uç için SNAT bağlantı noktası kullanımını artırma seçeneğiniz olabilir.  Uygulamanın, risk SNAT tükenmesi olmadan etkili bir şekilde ölçeklendirilmesi için varsayılan bağlantı noktası ayırmasının gerekli olduğu akılda tutulması önerilir.

Örneğin, arka uç havuzundaki iki sanal makinede, IP yapılandırması başına 1024 SNAT bağlantı noktası bulunabilir ve dağıtım için toplam 2048 SNAT bağlantı noktasına izin verilir.  Dağıtımın 50 sanal makinelere yükselymiş olması halinde, önceden ayrılan bağlantı noktalarının sayısı sanal makine başına sabit kalırsa bile, dağıtım tarafından toplam 51.200 (50 x 1024) SNAT bağlantı noktası kullanılabilir.  Dağıtımınızın ölçeğini genişletmek istiyorsanız katman başına [önceden ayrılmış bağlantı noktası](load-balancer-outbound-connections.md#preallocatedports) sayısını denetleyerek ilgili katman için en yüksek ölçeğe göre şekillerinizi şekillendirdiğinizden emin olun.  Yukarıdaki örnekte, 50 örnek yerine 51 ' e ölçeklendirmeye seçtiyseniz, bir sonraki katmana ilerleyerek VM başına daha az SNAT bağlantı noktası ve toplam olarak da sona erdir olur.

Bir sonraki daha büyük arka uç havuzu boyut katmanına ölçeklendirirseniz, ayrılan bağlantı noktalarının yeniden ayrılması durumunda giden bağlantılarınızın bazılarının zaman aşımına uğrar.  Yalnızca SNAT bağlantı noktalarından bazılarını kullanıyorsanız, sonraki daha büyük arka uç havuzu boyutunun ölçeğini ölçeklendirilmeye hazır değildir.  Sonraki arka uç havuz katmanına her geçtiğinizde, mevcut bağlantı noktalarının yarısı yeniden tahsis edilir.  Bunun gerçekleşmesini istemiyorsanız, dağıtımınızı katman boyutuna şekillendirmeniz gerekir.  Ya da uygulamanızın gerektiği şekilde algılayıp yeniden denenebildiğini doğrulayın.  TCP keepcanlı tutma, SNAT bağlantı noktalarının yeniden ayrılması nedeniyle artık işlev olmadığını algılamaya yardımcı olabilir.

## <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a><a name="idletimeout"></a>Giden boşta kalma zaman aşımını sıfırlamak için keepcanlı tutmayı kullanın
Giden bağlantılarda 4 dakikalık bir boşta kalma zaman aşımı vardır. Bu zaman aşımı, [giden kuralları](outbound-rules.md)aracılığıyla ayarlanabilir. Ayrıca, boş bir akışı yenilemek ve gerekirse bu boşta kalma zaman aşımını sıfırlamak için taşıma (örneğin, TCP keepcanlı tutma) veya uygulama katmanı keepcanlı tutma özelliğini de kullanabilirsiniz.  

TCP keepcanlı hale geldiğinde, bağlantının tek tarafında etkinleştirilmesi yeterlidir. Örneğin, bunları sunucu tarafında etkinleştirmek yeterlidir ve akışın boşta kalma zamanlayıcısını sıfırlayın ve her iki taraf da her iki tarafın da TCP keepcanlı hale getirilir olması gerekmez.  Veritabanı istemci-sunucu yapılandırmalarına dahil olmak üzere uygulama katmanı için benzer kavramlar vardır.  Uygulamaya özgü keepcanlı olarak hangi seçeneklerin mevcut olduğunu kontrol etmek için sunucu tarafını denetleyin.

## <a name="next-steps"></a>Sonraki Adımlar
Müşterilerimizin deneyimini geliştirmek için her zaman bakıyoruz. Bu makalede listelenmeyen veya çözülen giden bağlantılarla ilgili sorun yaşıyorsanız, bu sayfanın en altında GitHub aracılığıyla geri bildirim gönderin ve geri bildirimlerinizi mümkün olan en kısa sürede ele edeceğiz.
