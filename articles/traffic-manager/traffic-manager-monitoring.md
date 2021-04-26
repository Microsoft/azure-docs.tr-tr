---
title: Azure Traffic Manager uç noktası izleme | Microsoft Docs
description: Bu makale, Azure müşterilerinin yüksek kullanılabilirliğe sahip uygulamalar dağıtmasına yardımcı olmak için Traffic Manager uç nokta izlemeyi ve otomatik uç nokta yük devretmesini nasıl kullandığını anlamanıza yardımcı olabilir
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/22/2021
ms.author: duau
ms.openlocfilehash: 455eeb83ef5a9608c077de24b8d3d4722d26a822
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98742718"
---
# <a name="traffic-manager-endpoint-monitoring"></a>Traffic Manager uç nokta izleme

Azure Traffic Manager, yerleşik uç nokta izlemeyi ve otomatik uç nokta yük devretmesini içerir. Bu özellik, Azure bölge hataları da dahil olmak üzere uç nokta hatasına dayanıklı olan yüksek kullanılabilirliğe sahip uygulamalar sunmanıza yardımcı olur.

## <a name="configure-endpoint-monitoring"></a>Uç nokta izlemeyi yapılandırma

Uç nokta izlemeyi yapılandırmak için Traffic Manager profilinizde aşağıdaki ayarları belirtmeniz gerekir:

* **Protokol**. Traffic Manager, sistem durumunu denetlemek için uç noktanıza göz atın, kullandığı protokol olarak HTTP, HTTPS veya TCP ' yi seçin. HTTPS izleme, TLS/SSL sertifikanızın geçerli olup olmadığını doğrulamaz; yalnızca sertifikanın mevcut olduğunu denetler.
* **Bağlantı noktası**. İstek için kullanılan bağlantı noktasını seçin.
* **Yol**. Bu yapılandırma ayarı yalnızca HTTP ve HTTPS protokolleri için geçerlidir; bu, yol ayarını belirtmektir. TCP izleme protokolü için bu ayarın sağlanması bir hatayla sonuçlanır. HTTP ve HTTPS protokolü için, göreli yolu ve Web sayfasının veya izlemenin eriştiği dosyanın adını verin. Eğik çizgi (/), göreli yol için geçerli bir giriştir. Bu değer, dosyanın kök dizinde (varsayılan) olduğunu gösterir.
* **Özel üstbilgi ayarları**. Bu yapılandırma ayarı, Traffic Manager bir profil altındaki uç noktalara gönderdiği durum denetimlerine belirli HTTP üstbilgileri eklemenize yardımcı olur. Özel üstbilgiler, bu profildeki tüm uç noktalar ve/veya yalnızca o uç nokta için geçerli olan bir uç nokta düzeyinde geçerli olacak şekilde bir profil düzeyinde belirtilebilir. Çok kiracılı bir ortamda uç noktaların sistem durumu denetimleri için özel üst bilgileri kullanabilirsiniz. Bu şekilde, ana bilgisayar üstbilgisi belirtilerek hedefine doğru şekilde yönlendirilebilmesi. Bu ayarı, Traffic Manager kaynaklı HTTP isteklerini tanımlamak ve bunları farklı şekilde kullanmak için kullanılabilecek benzersiz üstbilgiler ekleyerek de kullanabilirsiniz. En fazla sekiz üst bilgi belirtebilirsiniz: virgülle ayrılmış değer çiftleri. Örneğin, "Header1: değer1, header2: değer2". 
* **Beklenen durum kodu aralıkları**. Bu ayar 200-299, 301-301 biçiminde birden fazla başarı kodu aralığı belirtmenize olanak tanır. Bu durum kodları bir uç noktadan yanıt olarak alındığında, bir sistem durumu denetimi tamamlandığında, Traffic Manager bu uç noktaları sağlıklı olarak işaretler. En fazla sekiz durum kodu aralığı belirtebilirsiniz. Bu ayar yalnızca HTTP ve HTTPS protokolü ve tüm uç noktalar için geçerlidir. Bu ayar Traffic Manager profil düzeyidir ve varsayılan olarak 200 değeri başarı durum kodu olarak tanımlanmıştır.
* **Yoklama aralığı**. Bu değer, bir uç noktanın bir Traffic Manager araştırma aracısından sistem durumu için ne sıklıkta denetleneceğini belirtir. Burada iki değer belirtebilirsiniz: 30 saniye (normal yoklama) ve 10 saniye (hızlı yoklama). Değer sağlanmazsa, profil varsayılan değer olan 30 saniye olarak ayarlanır. Hızlı yoklama fiyatlandırması hakkında daha fazla bilgi edinmek için [Traffic Manager fiyatlandırma](https://azure.microsoft.com/pricing/details/traffic-manager) sayfasını ziyaret edin.
* **Toleranslı başarısızlık sayısı**. Bu değer, bu uç noktayı uygun olmayan şekilde işaretlemeden önce Traffic Manager yoklama aracısının kaç başarısızlık olduğunu belirtir. Değeri 0 ile 9 arasında değişebilir. 0 değeri, tek bir izleme hatasının, bu uç noktanın sağlıksız olarak işaretlenmesine neden olabileceği anlamına gelir. Hiçbir değer belirtilmemişse, varsayılan değer olan 3 ' ü kullanır.
* **Araştırma zaman aşımı**. Bu özellik Traffic Manager yoklama aracısının bir uç noktaya durum araştırma denetimini bir hataya düşünmeden önce bekleyeceği süreyi belirtir. Yoklama aralığı 30 saniyeye ayarlanırsa, zaman aşımı değerini 5 ile 10 saniye arasında ayarlayabilirsiniz. Hiçbir değer belirtilmemişse, varsayılan 10 saniyelik bir değer kullanır. Yoklama aralığı 10 saniye olarak ayarlandıysa, zaman aşımı değerini 5 ile 9 saniye arasında ayarlayabilirsiniz. Zaman aşımı değeri belirtilmemişse, varsayılan değer olan 9 saniyelik bir değeri kullanır.

    ![Traffic Manager uç nokta izleme](./media/traffic-manager-monitoring/endpoint-monitoring-settings.png)

    **Şekil: Traffic Manager uç nokta izleme**

## <a name="how-endpoint-monitoring-works"></a>Uç nokta izlemenin çalışması

İzleme Protokolü HTTP veya HTTPS olarak ayarlandığında, Traffic Manager araştırma Aracısı, verilen protokol, bağlantı noktası ve göreli yolu kullanarak uç noktaya bir GET isteği yapar. Yoklama Aracısı 200-Tamam yanıtı alırsa veya **beklenen durum kodu \* aralıklarında** yapılandırılan yanıtlardan herhangi biri alırsa, uç nokta sağlıklı olarak değerlendirilir. Yanıt, zaman aşımı süresi içinde farklı bir değer ise veya yanıt alma süresi içinde alınmadığında, Traffic Manager yoklama Aracısı, toleranslı başarısızlık sayısı ayarına göre reatlar. Bu ayar 0 ise hiçbir reattempi yapılmaz. Ardışık hataların sayısı, toleranslı başarısızlık sayısı ayarından fazlaysa, uç nokta sağlıksız olarak işaretlenir.

İzleme protokolü TCP olduğunda, Traffic Manager araştırma Aracısı belirtilen bağlantı noktasını kullanarak bir TCP bağlantı isteği oluşturur. Uç nokta, bağlantıyı kurmak için bir Yanıt ile isteğe yanıt verirse, bu sistem durumu denetimi başarılı olarak işaretlenir. Traffic Manager araştırma Aracısı TCP bağlantısını sıfırlar. Yanıtın farklı bir değer olduğu veya zaman aşımı süresi içinde alınan yanıt alma durumlarında, Traffic Manager yoklama Aracısı, toleranslı başarısızlık sayısı ayarına göre yeniden yapılır. Bu ayar 0 ise hiçbir PCNS yapılmaz. Ardışık hataların sayısı, toleranslı başarısızlık sayısı ayarından yüksekse, bu uç nokta sağlıksız olarak işaretlenir.

Her durumda, birden çok konumdan yoklamalar Traffic Manager. Ardışık hata her bölge içinde ne olduğunu belirler. Bu nedenle uç noktalar, yoklama aralığı için kullanılan ayardan daha yüksek bir sıklıkta Traffic Manager durum araştırmalarını alıyor.

>[!NOTE]
>HTTP veya HTTPS izleme protokolü için, uç nokta tarafında ortak bir uygulama, uygulamanız içinde özel bir sayfa uygulamaktır. Örneğin,/Health.exe. Bu yolu izlemek için kullanarak, performans sayaçlarını denetleme veya veritabanı kullanılabilirliğini doğrulama gibi uygulamaya özgü denetimler gerçekleştirebilirsiniz. Bu özel denetimleri temel alarak, sayfa uygun bir HTTP durum kodu döndürür.

Traffic Manager profilindeki tüm uç noktalar, izleme ayarlarını paylaşır. Farklı uç noktalar için farklı izleme ayarları kullanmanız gerekiyorsa, [iç içe geçmiş Traffic Manager profilleri](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings)oluşturabilirsiniz.

## <a name="endpoint-and-profile-status"></a>Uç nokta ve profil durumu

Traffic Manager profillerini ve uç noktalarını etkinleştirebilir ve devre dışı bırakabilirsiniz. Ancak, Traffic Manager otomatik ayarlar ve süreçler nedeniyle uç nokta durumundaki bir değişiklik de oluşabilir.

### <a name="endpoint-status"></a>Uç nokta durumu

Belirli bir uç noktayı etkinleştirebilir veya devre dışı bırakabilirsiniz. Hala sağlıklı olabilecek temel hizmet, etkilenmemiştir. Uç nokta durumunu değiştirmek, Traffic Manager profilindeki uç noktanın kullanılabilirliğini denetler. Bir uç nokta durumu devre dışı bırakıldığında, Traffic Manager sistem durumunu denetlemez ve uç nokta bir DNS yanıtına dahil değildir.

### <a name="profile-status"></a>Profil durumu

Profil durumu ayarını kullanarak belirli bir profili etkinleştirebilir veya devre dışı bırakabilirsiniz. Uç nokta durumu tek bir uç noktayı etkileirken, profil durumu tüm uç noktalar dahil olmak üzere tüm profili etkiler. Bir profili devre dışı bıraktığınızda, uç noktalar sistem durumu denetlenmez ve bir DNS yanıtına hiçbir uç nokta eklenmez. DNS sorgusu için bir [nxdomain](https://tools.ietf.org/html/rfc2308) yanıt kodu döndürülür.

### <a name="endpoint-monitor-status"></a>Uç nokta izleyici durumu

Uç nokta izleyici durumu, uç noktanın durumunu gösteren Traffic Manager tarafından oluşturulan bir değerdir. Bu ayarı el ile değiştiremezsiniz. Uç nokta izleyici durumu, uç nokta izleme sonuçlarının ve yapılandırılan uç nokta durumunun bir birleşimidir. Uç nokta İzleyici durumunun olası değerleri aşağıdaki tabloda gösterilmiştir:

| Profil durumu | Uç nokta durumu | Uç nokta izleyici durumu | Notlar |
| --- | --- | --- | --- |
| Devre dışı |Etkin |Etkin değil |Profil devre dışı bırakıldı. Uç nokta durumu etkin olsa da, profil durumu (devre dışı) öncelik kazanır. Devre dışı bırakılmış profillerdeki uç noktalar izlenmez. DNS sorgusu için bir NXDOMAIN yanıt kodu döndürülür. |
| &lt;tümü&gt; |Devre dışı |Devre dışı |Uç nokta devre dışı bırakıldı. Devre dışı uç noktalar izlenmiyor. Uç nokta, trafik almadığı için DNS yanıtlarına dahil değildir. |
| Etkin |Etkin |Çevrimiçi |Uç nokta izlenir ve iyi durumda. Bu, DNS yanıtlarına dahildir ve trafik alabilir. |
| Etkin |Etkin |Düzeyi düşürüldü |Uç nokta izleme sistem durumu denetimleri başarısız oluyor. Uç nokta DNS yanıtlarına dahil değildir ve trafik almaz. <br>Tüm uç noktaların düzeyi düşürülmüş bir özel durumdur. Bu durumda, tüm bunlar sorgu yanıtında döndürülmek üzere kabul edilir).</br>|
| Etkin |Etkin |CheckingEndpoint |Uç nokta izleniyor, ancak ilk araştırmanın sonuçları henüz alınmadı. CheckingEndpoint, genellikle profilde bir uç nokta eklendikten veya etkinleştirildikten sonra hemen meydana gelen geçici bir durumdur. Bu durumdaki bir uç nokta, DNS yanıtlarına dahildir ve trafik alabilir. |
| Etkin |Etkin |Durduruldu |Uç noktanın işaret ettiği Web uygulaması çalışmıyor. Web uygulaması ayarlarını kontrol edin. Uç nokta, iç içe geçmiş uç noktası türünde ise ve alt profil devre dışı bırakıldığında veya etkin olmadığında da bu durum oluşabilir. <br>Durdurulmuş durumdaki bir uç nokta izlenmez. DNS yanıtlarına dahil değildir ve trafik almaz. Tüm uç noktaların düzeyi düşürülmüş bir özel durumdur. Bu durumda, tüm bunlar sorgu yanıtında döndürülmek üzere değerlendirilir.</br>|

Uç nokta İzleyici durumunun iç içe geçmiş uç noktalar için nasıl hesaplandığı hakkında ayrıntılar için bkz. [iç içe Traffic Manager profilleri](traffic-manager-nested-profiles.md).

>[!NOTE]
> Web uygulamanız Standart katmanda veya yukarıda çalışmıyorsa, App Service durdurulmuş bir uç nokta izleyici durumu oluşabilir. Daha fazla bilgi için bkz. [App Service tümleştirmesi Traffic Manager](../app-service/web-sites-traffic-manager.md).

### <a name="profile-monitor-status"></a>Profil izleyici durumu

Profil izleyici durumu, yapılandırılmış profil durumunun ve tüm uç noktalar için uç nokta İzleyicisi durum değerlerinin bir birleşimidir. Olası değerler aşağıdaki tabloda açıklanmıştır:

| Profil durumu (yapılandırıldığı gibi) | Uç nokta izleyici durumu | Profil izleyici durumu | Notlar |
| --- | --- | --- | --- |
| Devre dışı |&lt;&gt;tanımlı uç noktaları olmayan herhangi bir veya bir profil. |Devre dışı |Profil devre dışı bırakıldı. |
| Etkin |En az bir uç noktanın durumu düşürülmüş olur. |Düzeyi düşürüldü |Hangi uç noktaların daha fazla dikkat gerektirdiğini öğrenmek için tek uç nokta durumu değerlerini gözden geçirin. |
| Etkin |En az bir uç noktanın durumu çevrimiçi. Hiçbir uç nokta düşürülmüş bir duruma sahip değil. |Çevrimiçi |Hizmet trafiği kabul ediyor. Başka bir eylem gerekli değildir. |
| Etkin |En az bir uç noktanın durumu CheckingEndpoint olur. Çevrimiçi veya düşürülmüş durumunda hiçbir uç nokta yok. |CheckingEndpoints |Bu geçiş durumu, oluşturulduğunda veya etkinleştirildiğinde bir profil oluşur. Uç nokta sistem durumu ilk kez denetlenir. |
| Etkin |Profildeki tüm uç noktaların durumları devre dışı veya durdurulmuş ya da profilde tanımlı uç noktalar yok. |Etkin değil |Etkin bir uç nokta yok, ancak profil hala etkin. |

## <a name="endpoint-failover-and-recovery"></a>Uç nokta yük devretme ve kurtarma

Traffic Manager, sağlıksız uç noktalar dahil olmak üzere her uç noktanın sistem durumunu düzenli olarak denetler. Traffic Manager, bir uç noktanın sağlıklı hale geldiğini algılar ve geri dönüşü geri getirir.

Aşağıdaki olaylardan herhangi biri gerçekleştiğinde bir uç nokta sağlıksız olur:

- İzleme Protokolü HTTP veya HTTPS ise:
    - 200 olmayan bir yanıt veya **beklenen durum kodu aralıkları** ayarında belirtilen durum aralığını içermeyen bir yanıt alındı. (Farklı bir 2xx kodu veya 301/302 yeniden yönlendirme dahil).
- İzleme protokolü TCP ise: 
    - Bağlantı kurmayı denemek için Traffic Manager tarafından gönderilen SYN isteğine yanıt olarak ACK veya SYN-ACK dışında bir yanıt alındı.
- Aş. 
- Diğer bir bağlantı sorunu, uç noktada ulaşılamaz durumda olur.

Başarısız denetimlerin sorunlarını giderme hakkında daha fazla bilgi için bkz. [Azure Traffic Manager 'de sorun giderme durumu](traffic-manager-troubleshooting-degraded.md). 

Aşağıdaki şekilde zaman çizelgesi, aşağıdaki ayarlara sahip Traffic Manager uç noktası izleme işleminin ayrıntılı bir açıklamasıdır:

* İzleme Protokolü HTTP 'dir.
* Yoklama aralığı 30 saniyedir.
* Toleranslı hataların sayısı 3 ' dir.
* Zaman aşımı değeri 10 saniyedir.
* DNS TTL değeri 30 saniyedir.

![Traffic Manager uç noktası yük devretme ve yeniden çalışma sırası](./media/traffic-manager-monitoring/timeline.png)

**Şekil: Traffic Manager uç noktası yük devretme ve kurtarma sırası**

1. **Al**. Her uç nokta için Traffic Manager izleme sistemi, izleme ayarlarında belirtilen yol üzerinde bir GET isteği yapar.
2. **200 Tamam veya özel kod aralığı profil izleme ayarları Traffic Manager belirtildi**. İzleme sistemi, 10 saniye içinde döndürülmek üzere izleme ayarlarında belirtilen aralıktaki bir HTTP 200 Tamam veya durum kodu bekliyor. Bu yanıtı aldığında hizmetin kullanılabildiğini algılar.
3. **denetimler arasında 30 saniye**. Uç nokta sistem durumu denetimi, her 30 saniyede yinelenir.
4. **Hizmet kullanılamıyor**. Hizmet kullanılamaz duruma gelir. Traffic Manager bir sonraki sistem durumu denetimine kadar bilmez.
5. **İzleme yoluna erişmeye çalışır**. İzleme sistemi bir GET isteği yapar, ancak 10 saniyelik zaman aşımı süresi içinde bir yanıt almaz. Daha sonra 30 saniyelik aralıklarla üç kez daha dener. Denemeden biri başarılı olursa, denemeler sayısı sıfırlanır.
6. **Durum düşürülmüş olarak ayarlandı**. Dördüncü ardışık bir hatadan sonra, izleme sistemi kullanılamayan uç nokta durumunu düşürülmüş olarak işaretler.
7. **Trafik diğer uç noktalara göre belirlenir**. Traffic Manager DNS ad sunucuları güncelleştirilir ve Traffic Manager DNS sorgularına yanıt olarak uç noktasını döndürmez. Yeni bağlantılar, diğer kullanılabilir uç noktalara yönlendirilir. Ancak, bu uç noktayı içeren önceki DNS yanıtları hala özyinelemeli DNS sunucuları ve DNS istemcileri tarafından önbelleğe alınmış olabilir. İstemciler, DNS önbelleğinin süresi dolana kadar uç noktasını kullanmaya devam eder. DNS önbelleğinin süresi dolduğu için istemciler yeni DNS sorguları yapar ve farklı uç noktalara yönlendirilir. Önbellek süresi, Traffic Manager profilindeki TTL ayarı (örneğin, 30 saniye) tarafından denetlenir.
8. **Sistem durumu denetimleri devam eder**. Traffic Manager, düzeyi düşürülmüş bir duruma sahip olduğu sırada uç noktanın durumunu denetlemeye devam eder. Traffic Manager, uç noktanın sistem durumuna döndüğünü algılar.
9. **Hizmet çevrimiçi olarak geri geliyor**. Hizmet kullanılabilir hale gelir. Uç nokta, izleme sistemi bir sonraki sistem durumu denetimini yapana kadar Traffic Manager düzeyi düşürülmüş durumunu korur.
10. **Hizmetin trafiği devam ettirir**. Traffic Manager bir GET isteği gönderir ve bir 200 Tamam durum yanıtı alır. Hizmet sağlıklı bir duruma geri döndü. Traffic Manager ad sunucuları güncelleştirilir ve DNS yanıtlarındaki hizmetin DNS adını kullanıma açmaya başlar. Trafik, diğer uç noktaları döndüren önbelleğe alınmış DNS yanıtları olarak bitiş noktasına geri döner ve diğer uç noktalara yönelik mevcut bağlantılar sona erer.

    > [!NOTE]
    > Traffic Manager DNS düzeyinde çalıştığından, herhangi bir uç noktaya yönelik mevcut bağlantıları etkilemez. Trafiği uç noktalar arasında (değiştirilen profil ayarları veya yük devretme ya da yeniden çalışma sırasında) yönlendirirse, Traffic Manager yeni bağlantıları kullanılabilir uç noktalara yönlendirir. Ancak, diğer uç noktalar, Bu oturumlar sonlandırılana kadar mevcut bağlantılar aracılığıyla trafik almaya devam edebilir. Trafiği, mevcut bağlantılardan boşaltacak şekilde etkinleştirmek için, her bir uç nokta ile kullanılan oturum süresini sınırlamanız gerekir.

## <a name="traffic-routing-methods"></a>Trafik-yönlendirme yöntemleri

Bir uç noktanın durumu düşürülmüş olduğunda, artık DNS sorgularına yanıt olarak döndürülmez. Bunun yerine, alternatif bir uç nokta seçilir ve döndürülür. Profilde yapılandırılan trafik yönlendirme yöntemi alternatif uç noktanın nasıl seçili olduğunu belirler.

* **Öncelik**. Uç noktalar öncelikli bir liste oluşturur. Listedeki ilk kullanılabilir uç nokta her zaman döndürülür. Bir uç nokta durumu düşerse, sonraki kullanılabilir uç nokta döndürülür.
* **Ağırlıklı**. Kullanılabilir tüm uç noktalar, kendilerine atanan ağırlıklarını ve diğer kullanılabilir uç noktaların ağırlıklarını temel alarak rastgele seçilir.
* **Performans**. Son kullanıcıya en yakın uç nokta döndürülür. Uç nokta kullanılamıyorsa Traffic Manager trafiği sonraki en yakın Azure bölgesindeki uç noktalara taşıdır. [İç içe Traffic Manager profillerini](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region)kullanarak performans trafiği yönlendirmesi için alternatif yük devretme planları yapılandırabilirsiniz.
* **Coğrafi**. Sorgu isteği IP 'si temelinde coğrafi konuma hizmeti sunacak şekilde eşlenen uç nokta döndürülür. Bu uç nokta kullanılamıyorsa, coğrafi bir konum yalnızca bir profildeki bir uç noktaya eşlenildiği için başka bir uç noktanın yük devri yapılacak şekilde seçilmeyeceği için bu bitiş noktası seçilemez. (Ayrıntılar [hakkında](traffic-manager-FAQs.md#traffic-manager-geographic-traffic-routing-method)daha fazla bilgi için bkz.). En iyi uygulama olarak, coğrafi yönlendirme kullanılırken, müşterilerin profil uç noktaları olarak birden fazla uç nokta ile iç içe Traffic Manager profillerini kullanmalarını öneririz.
* **Çoklu değer** IPv4/IPv6 adresleriyle eşlenmiş birden fazla uç nokta döndürülür. Bu profil için bir sorgu alındığında sağlıklı uç noktalar, belirttiğiniz **Yanıt değerindeki en fazla kayıt sayısına** göre döndürülür. Varsayılan yanıt sayısı iki uç nokta olur.
* **Alt ağ** Bir IP adresi aralığı kümesiyle eşlenen uç nokta döndürülür. Bu IP adresinden bir istek alındığında döndürülen uç nokta bu IP adresi için eşlenmiş bir noktadır. 

Daha fazla bilgi için bkz. [Traffic Manager trafik-yönlendirme yöntemleri](traffic-manager-routing-methods.md).

> [!NOTE]
> Normal trafik yönlendirme davranışına yönelik bir istisna, tüm uygun uç noktalar düşürülmüş bir duruma sahip olduğunda oluşur. Traffic Manager *, tüm düşürülmüş durum uç noktaları aslında çevrimiçi bir durumda olduğundan*, "en iyi çaba" girişimi yapar ve yanıt verir. Bu davranış, DNS yanıtında herhangi bir uç nokta döndürülmemelidir, alternatif olarak tercih edilir. Devre dışı bırakılan veya durdurulan uç noktalar izlenmiyor, bu nedenle trafik için uygun kabul edilmez.
>
> Bu durum genellikle hizmetin yanlış yapılandırılması nedeniyle oluşur; örneğin:
>
> * Traffic Manager sistem durumu denetimlerini engelleyen bir erişim denetim listesi [ACL].
> * Traffic Manager profilinde izleme bağlantı noktasının veya protokolünün yanlış yapılandırması.
>
> Bu davranışın sonucunda, Traffic Manager sistem durumu denetimleri doğru yapılandırılmamışsa *, Traffic Manager düzgün şekilde çalıştığı gibi* trafik yönlendirmesinde görünebilirler. Ancak, bu durumda, genel uygulama kullanılabilirliğini etkileyen uç nokta yük devretmesi gerçekleşmeyecek. Profilin düşürülmüş bir durum değil, çevrimiçi durumunu gösterdiğinden emin olmak önemlidir. Çevrimiçi bir durum Traffic Manager durum denetimlerinin beklendiği gibi çalıştığını gösterir.

Başarısız durum denetimlerinde sorun giderme hakkında daha fazla bilgi için bkz. [Azure Traffic Manager 'de sorun giderme durumu](traffic-manager-troubleshooting-degraded.md).

## <a name="faqs"></a>SSS

* [Azure bölge hatalarıyla Traffic Manager dayanıklı mi?](./traffic-manager-faqs.md#is-traffic-manager-resilient-to-azure-region-failures)

* [Kaynak grubu konumu seçimi Traffic Manager nasıl etkiler?](./traffic-manager-faqs.md#how-does-the-choice-of-resource-group-location-affect-traffic-manager)

* [Nasıl yaparım? her uç noktanın geçerli sistem durumunu mı belirlemelisiniz?](./traffic-manager-faqs.md#how-do-i-determine-the-current-health-of-each-endpoint)

* [HTTPS uç noktalarını izleyebilir miyim?](./traffic-manager-faqs.md#can-i-monitor-https-endpoints)

* [Uç nokta eklerken bir IP adresi veya DNS adı kullanıyorum mi?](./traffic-manager-faqs.md#do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint)

* [Uç nokta eklerken ne tür IP adresleri kullanabilirim?](./traffic-manager-faqs.md#what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint)

* [Tek bir profil içinde farklı uç nokta adresleme türlerini kullanabilir miyim?](./traffic-manager-faqs.md#can-i-use-different-endpoint-addressing-types-within-a-single-profile)

* [Gelen sorgunun kayıt türü, uç noktaların adresleme türüyle ilişkili kayıt türünden farklı olduğunda ne olur?](./traffic-manager-faqs.md#what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints)

* [Bir profili, iç içe geçmiş bir profilde IPv4/IPv6 ile ilgili belirtilen uç noktalarla kullanabilir miyim?](./traffic-manager-faqs.md#can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile)

* [Traffic Manager profilimde bir Web uygulaması uç noktasını durdurdum, ancak yeniden başlattıktan sonra bile hiç trafik almadım. Bunu nasıl giderebilirim?](./traffic-manager-faqs.md#i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this)

* [Uygulamamın HTTP veya HTTPS desteği olmasa bile Traffic Manager kullanabilir miyim?](./traffic-manager-faqs.md#can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https)

* [TCP izlemesi kullanılırken uç noktadan hangi belirli yanıtlar gerekir?](./traffic-manager-faqs.md#what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring)

* [Kullanıcılarınızın sağlıksız bir uç noktadan uzakta mi ne kadar hızlı hareket Traffic Manager?](./traffic-manager-faqs.md#how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint)

* [Bir profildeki farklı uç noktalar için farklı izleme ayarlarını nasıl belirtirim?](./traffic-manager-faqs.md#how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile)

* [Uç noktalarıma Traffic Manager sistem durumu denetimlerine HTTP üstbilgileri nasıl atayabilirim?](./traffic-manager-faqs.md#how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints)

* [Uç nokta sistem durumu denetimleri hangi konak üstbilgisini kullanır?](./traffic-manager-faqs.md#what-host-header-do-endpoint-health-checks-use)

* [Sistem durumu denetimlerinin kaynağı olan IP adresleri nelerdir?](./traffic-manager-faqs.md#what-are-the-ip-addresses-from-which-the-health-checks-originate)

* [Uç noktadan kaç tane sistem durumu denetlemesi Traffic Manager bekleyebilir?](./traffic-manager-faqs.md#how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager)

* [Uç noktalarımı bir veya daha fazla kaldığında nasıl bildirim alabilirim?](./traffic-manager-faqs.md#how-can-i-get-notified-if-one-of-my-endpoints-goes-down)

## <a name="next-steps"></a>Sonraki adımlar

[Traffic Manager nasıl çalıştığını](traffic-manager-how-it-works.md) öğrenin

Traffic Manager tarafından desteklenen [trafik yönlendirme yöntemleri](traffic-manager-routing-methods.md) hakkında daha fazla bilgi edinin

[Traffic Manager profili oluşturmayı](traffic-manager-manage-profiles.md) öğrenin

Traffic Manager uç noktasındaki [azaltılmış durum sorunlarını giderme](traffic-manager-troubleshooting-degraded.md)