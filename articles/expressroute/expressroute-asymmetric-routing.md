---
title: 'Azure ExpressRoute: asimetrik yönlendirme'
description: Bu makalede asimetrik bir hedefe birden çok bağlantı olan bir ağa yönlendirme ile yüz sorunları size kılavuzluk eder.
services: expressroute
author: osamazia
ms.service: expressroute
ms.topic: article
ms.date: 10/10/2016
ms.author: osamam
ms.openlocfilehash: 8adfcc6559e3e2d48aabd3cfeec4fe20541917c3
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072150"
---
# <a name="asymmetric-routing-with-multiple-network-paths"></a>Birden çok ağ yoluyla Asimetrik yönlendirme
Bu makalede, ağ kaynağı ile hedef arasında birden çok yol varsa iletme ve döndürme ağ trafiğinin nasıl farklı rotalar izleyebileceği açıklanmaktadır.

Asimetrik yönlendirmeyi anlayabilmek için iki konsepti anlamak önemlidir. Bunlardan biri, birden çok ağ yolunun etkisidir. Diğeri ise cihazların, bir güvenlik duvarı gibi nasıl durumlarını koruduğudur. Bu tür cihazlara durum bilgisi olan cihazlar denir. Bu iki faktörün bileşimi, durum bilgisi olan bir cihazın ağ trafiğinin bu cihazın kendisiyle başladığını algılamadığı için söz konusu trafikten çıktığı senaryolar oluşturur.

## <a name="multiple-network-paths"></a>Birden çok ağ yolu
Kurumsal bir ağın, İnternet hizmet sağlayıcısı üzerinden tek bir İnternet bağlantısı varsa İnternet’e giden ve buradan gelen tüm trafik aynı yolu izler. Şirketler çoğunlukla ağ çalışma süresini artırmak için yedek yollar olarak birden çok devre satın alır. Böyle durumlarda, ağ dışına, yani İnternet’e giden trafik bir bağlantı üzerinden geçerken dönüş trafiği farklı bir bağlantı üzerinden geçebilir. Bu duruma yaygın olarak asimetrik yönlendirme adı verilir. Asimetrik yönlendirmede, ters yöndeki ağ trafiği özgün akıştan farklı bir yol alır.

![Birden çok yola sahip ağ](./media/expressroute-asymmetric-routing/AsymmetricRouting3.png)

Esas olarak İnternet üzerinde meydana gelse de asimetrik yönlendirme, birden çok yol bileşimi bulunan başka durumlar için de geçerlidir. Örneğin, aynı hedefe yönelik bir İnternet yolu ve özel bir yol veya aynı hedefe yönelik birden çok özel yol için asimetrik yönlendirme uygulanır.

Kaynaktan hedefe giden yol boyunca her yönlendirici hedefe ulaşmak için en uygun yolu hesaplar. Yönlendiricinin yaptığı en uygun yol belirlemesi iki ana faktöre bağlıdır:

* Dış ağlar arası yönlendirme, Sınır Ağ Geçidi Protokolü (BGP) olarak bilinen bir yönlendirme protokolüne bağlıdır. BGP, komşulardan tanıtımları toplar ve bunları bir dizi adımdan geçirip hedefe yönelik en uygun yolu belirler. BGP, bu en uygun yolu yönlendirme tablosunda depolar.
* Bir rota ile ilişkili alt ağ maskesinin uzunluğu yönlendirme yollarını etkiler. Yönlendirici, aynı IP adresi için farklı alt ağ maskeleri olan birden çok tanıtım alırsa, alt ağ maskesi daha uzun olan tanıtım daha belirli bir yol olarak görüldüğü için bu tanıtım seçilir.

## <a name="stateful-devices"></a>Durum bilgisi olan cihazlar
Yönlendiriciler, yönlendirme amacıyla paketlerin IP üst bilgisine bakar. Bazı cihazlar, pakete daha da ayrıntılı şekilde bakar. Bu cihazlar, genellikle Katman4 (İletim Denetimi Protokolü veya TCP ya da Kullanıcı Veri Birimi Protokolü veya UDP) ve hatta Katman7 (Uygulama Katmanı) üst bilgilerine bakar. Bu tür cihazlar, güvenlik cihazları ya da bant genişliği iyileştirme cihazlarıdır. 

Durum bilgisi olan cihazlar için tipik bir örnek güvenlik duvarıdır. Güvenlik duvarı; protokol, TCP/UDP bağlantı noktası ve URL üst bilgileri gibi çeşitli alanlara bağlı olarak bir paketin arabirimleri üzerinden geçmesine izin verir veya bunu reddeder. Bu düzeyde bir paket denetimi nedeniyle cihaz üzerinde ağır bir işlem yükü oluşur. Güvenlik duvarı, performansı artırmak için bir akıştaki ilk paketi denetler. Paketin geçmesine izin verirse akış bilgilerini durum tablosunda tutar. İlk belirlemeye dayalı olarak bu akışla ilgili sonraki tüm paketlere de izin verilir. Mevcut bir akışın parçası olan bir paket, güvenlik duvarına ulaşabilir. Güvenlik duvarının önceden bu paketle ilgili bir durum bilgisi yoksa, paket bırakılır.

## <a name="asymmetric-routing-with-expressroute"></a>ExpressRoute ile asimetrik yönlendirme
Azure ExpressRoute üzerinden Microsoft’a bağlandığınızda, ağınız aşağıdaki şekilde değişir:

* Birden fazla Microsoft bağlantınız var. Bağlantıların biri, var olan İnternet bağlantınız diğeri de ExpressRoute aracılığıyla olan bağlantı. Microsoft’a yönelik trafiğin bir kısmı İnternet üzerinden gidip ExpressRoute üzerinden dönebilir ya da tam tersi gerçekleşebilir.
* ExpressRoute üzerinden daha belirli IP adresleri alırsınız. Bu nedenle, ExpressRoute aracılığıyla sunulan hizmetlere ilişkin olarak ağınızdan Microsoft’a giden trafik için yönlendiriciler her zaman ExpressRoute seçeneğini tercih eder.

Bu iki değişikliğin bir ağ üzerindeki etkisini anlamak için bazı senaryolara göz atalım. Örnek olarak, İnternet’e yönelik tek bir devreniz olduğunu ve tüm Microsoft hizmetlerini İnternet üzerinden tükettiğinizi varsayalım. Ağınızdan Microsoft’a giden ve ters yönde gelen trafik aynı İnternet bağlantısını kullanır ve güvenlik duvarından geçer. Güvenlik duvarı ilk paketi gördüğünde akışı kaydeder ve akış, durum tablosunda var olduğundan dönüş paketlerine izin verilir.

![ExpressRoute ile asimetrik yönlendirme](./media/expressroute-asymmetric-routing/AsymmetricRouting1.png)

Ardından, ExpressRoute hizmetini açıyorsunuz ve Microsoft tarafından ExpressRoute üzerinden sunulan hizmetleri tüketiyorsunuz. Microsoft tarafından sunulan diğer tüm hizmetler İnternet üzerinden tüketiliyor diyelim. ExpressRoute’a bağlanan ucunuzda ayrı bir güvenlik duvarı kullanıyorsunuz. Microsoft, belirli hizmetler için ExpressRoute üzerinden ağınıza daha belirli ön ekler tanıtır. Yönlendirme altyapınız bu ön ekler için tercih edilen yol olarak ExpressRoute’u seçer. Genel IP adreslerinizi ExpressRoute üzerinden Microsoft’a tanıtmıyorsanız Microsoft, genel IP adreslerinizle İnternet üzerinden iletişim kurar. Ağınızdan Microsoft’a giden trafik ExpressRoute’u kullanırken, Microsoft’tan dönen trafik İnternet’i kullanır. Uçtaki güvenlik duvarı, durum tablosunda bulamadığı bir akışa ilişkin bir yanıt paketi gördüğünde dönüş trafiğini bırakır.

ExpressRoute ve Internet için aynı ağ adresi çevirisi (NAT) havuzunu tanıtmayı seçerseniz, ağınızdaki istemcilerle özel IP adresleri hakkında benzer sorunlar görürsünüz. Windows Update gibi hizmetlerin IP adresleri ExpressRoute aracılığıyla tanıtılmadığından, bu hizmetlere yönelik istekler İnternet üzerinden gider. Ancak, dönüş trafiği ExpressRoute üzerinden geri gelir. Microsoft, İnternet ve ExpressRoute’tan aynı alt ağ maskesine sahip bir IP adresi alırsa ExpressRoute’u İnternet’e tercih eder. ExpressRoute’a yönelik ağ ucunuzdaki bir güvenlik duvarının ya da durum bilgisi olan başka bir cihazın akışla ilgili daha önceden bilgisi yoksa, söz konusu akışa ait olan paketler bırakılır.

## <a name="asymmetric-routing-solutions"></a>Asimetrik yönlendirme çözümleri
Asimetrik yönlendirme sorununu çözmek için iki ana seçeneğiniz vardır. Sorunu yönlendirme aracılığıyla ya da kaynak tabanlı NAT (SNAT) kullanarak çözebilirsiniz.

### <a name="routing"></a>Yönlendirme
Genel IP adreslerinizin uygun geniş alan ağı (WAN) bağlantılarına tanıtıldığından emin olmanız gerekir. Örneğin, kimlik doğrulama trafiği için İnternet ve posta trafiğiniz için ExpressRoute kullanmak istiyorsanız, Active Directory Federasyon Hizmetleri (AD FS) genel IP adreslerinizi ExpressRoute üzerinden tanıtmamanız gerekir. Benzer şekilde, yönlendiricinin ExpressRoute üzerinden aldığı IP adreslerini bir şirket içi AD FS sunucusunun kullanımına sunmamalısınız. ExpressRoute üzerinden alınan rotalar daha belirli olduğundan, bunlar Microsoft’a yönelik kimlik doğrulama trafiği için ExpressRoute’u tercih edilen yol haline getirir. Bu durum asimetrik yönlendirmeye yol açar.

Kimlik doğrulaması için ExpressRoute’u kullanmak istiyorsanız AD FS genel IP adreslerini ExpressRoute üzerinden NAT olmadan tanıttığınızdan emin olun. Bu şekilde, Microsoft'tan kaynaklanan ve şirket içi bir AD FS sunucusuna giden trafik ExpressRoute üzerinden gider. Müşteriden Microsoft’a giden dönüş trafiği, İnternet üzerinden tercih edilen yol olduğundan ExpressRoute’u kullanır.

### <a name="source-based-nat"></a>Kaynak tabanlı NAT
Asimetrik yönlendirme sorunlarını çözmenin bir başka yolu da SNAT kullanmaktır. Örneğin, şirket içi bir Basit Posta Aktarım Protokolü (SMTP) sunucusunun genel IP adresini ExpressRoute üzerinden tanıtmadınız, çünkü bu tür iletişimler için İnternet’i kullanmayı amaçlıyorsunuz. Microsoft’tan kaynaklanıp daha sonra şirket içi SMTP sunucunuza giden bir istek İnternet’ten gönderilir. Gelen isteğe SNAT uygulayarak bir dahili IP adresine yönlendirirsiniz. SMTP sunucusundan kaynaklanan ters yöndeki trafik, ExpressRoute üzerinden gitmek yerine NAT için kullandığınız uç güvenlik duvarına gider. Dönüş trafiği İnternet üzerinden gider.

![Kaynak tabanlı NAT ağ yapılandırması](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## <a name="asymmetric-routing-detection"></a>Asimetrik yönlendirmenin algılanması
Traceroute, ağ trafiğinizin beklenen yoldan gittiğinden emin olmanın en iyi yoludur. Şirket içi SMTP sunucunuzdan Microsoft’a giden trafiğin İnternet yolunu tercih etmesini bekliyorsanız, beklenen traceroute SMTP sunucusundan Office 365’e gider. Sonuç, ağınızdan çıkan trafiğin ExpressRoute’a değil, gerçekten de İnternet’e gittiğini doğrular.

