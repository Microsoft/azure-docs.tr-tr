---
title: Windows sanal masaüstü için hizmet kalitesi (QoS) uygulama (Önizleme)
titleSuffix: Azure
description: Windows sanal masaüstü için QoS 'yi ayarlama (Önizleme).
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: b61faf74d96e2571e91f7bf9d10eac88cdbf8345
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94639426"
---
# <a name="implement-quality-of-service-qos-for-windows-virtual-desktop-preview"></a>Windows sanal masaüstü için hizmet kalitesi (QoS) uygulama (Önizleme)

> [!IMPORTANT]
> Windows sanal masaüstü için hizmet kalitesi (QoS) Ilkesi desteği şu anda genel önizlemededir.
> Bu önizleme bir hizmet düzeyi sözleşmesi olmadan sağlanır ve bunu üretim iş yükleri için kullanmanızı önermiyoruz. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[RDP ShortPath](./shortpath.md) , uzak masaüstü Istemcisi ile oturum ana bilgisayarı arasında doğrudan UDP tabanlı bir aktarım sağlar. RDP Shortfılepath, RDP verileri için hizmet kalitesi (QoS) ilkelerinin yapılandırılmasına izin vermez.
Windows sanal masaüstündeki QoS, ağ gecikmeleriyle duyarlı olan gerçek zamanlı RDP trafiğinin, daha az hassas olan trafiğin önünde "satır içinde kesilme" olmasına olanak sağlar. Bu tür daha az hassas trafiğe örnek olarak yeni bir uygulama indirileceği için, çok fazla indirme işlemi büyük bir anlaşma değildir. QoS, gerçek zamanlı akışlarda tüm paketleri tanımlamak ve işaretlemek ve ağınızın bant genişliğinin adanmış bir bölümünü belirtmek için ağınıza yardımcı olmak üzere Windows grup ilkesi nesnelerini kullanır.

Bu makalede açıklanan sorunlardan biriyle karşılaşan büyük bir kullanıcı grubunu destekleyebiliyorsanız, büyük olasılıkla QoS uygulamanız gerekir. Az sayıda kullanıcısı olan küçük bir işletme, QoS 'ye ihtiyaç duymayabilir, ancak bunun da yararlı olması gerekir.

Bir QoS formu olmadan, aşağıdaki sorunları görebilirsiniz:

* Değişim – RDP paketleri farklı ücretlerden geliyor ve bu da görsel ve ses Gösterbir sonucu verebilir
* Paket kaybı: bırakılan paketler, daha fazla zaman gerektiren yeniden aktarım ile sonuçlanır
* Gecikmeli gidiş dönüş süresi (RTT) – RDP paketlerinin hedeflerine ulaşması uzun zaman alan ve bu, uzak uygulamadan gelen giriş ve yeniden eylem arasında dikkat çekici gecikmeler oluşmasına neden olur.

Bu sorunları gidermek için en az karmaşık yol, veri bağlantılarının boyutunu hem iç hem de internet üzerinden artırmanız olur. Genellikle düşük maliyetli olduğundan, QoS bant genişliğini daha verimli bir şekilde eklemek yerine sahip olduğunuz kaynakları yönetmenin bir yolunu sunar. Kalite sorunlarını gidermek için, ilk olarak QoS kullanmanızı ve ardından yalnızca gerekli yerlerde bant genişliği eklemenizi öneririz.

QoS 'nin etkili olabilmesi için kuruluşunuzun tamamında tutarlı QoS ayarları uygulamanız gerekir. Yolun, QoS önceliklerinizi desteklemeye başarısız olan herhangi bir bölümü, kalite RDP oturumunun düşmesine neden olabilir.

## <a name="introduction-to-qos-queues"></a>QoS sıralarına giriş

QoS sağlamak için, ağ cihazlarının trafiği sınıflandırıp bir yolu olmalıdır ve RDP 'yi diğer ağ trafiğinden ayırabilmelidir.

Ağ trafiği bir yönlendirici girdiğinde trafik bir kuyruğa konur. Bir QoS ilkesi yapılandırılmamışsa, tek bir kuyruk vardır ve tüm veriler ilk kez, ilk kez aynı önceliğe sahip olarak değerlendirilir. Bu, birkaç ekstra milisaniye gecikme süresinin bir sorun olması durumunda RDP trafiğinin trafiğin arkasında takılmasına yol açabilir.

QoS 'yi uyguladığınızda, Cisco 'nun öncelik sırası ve [sınıf tabanlı ağırlıklı sıra (CBWFQ)](https://www.cisco.com/en/US/docs/ios/12_0t/12_0t5/feature/guide/cbwfq.html#wp17641) ve [ağırlıklı rastgele erken algılama (WRED)](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/qos_conavd/configuration/15-mt/qos-conavd-15-mt-book/qos-conavd-cfg-wred.html)gibi sıkışıklık engelleme özellikleri gibi çeşitli sıkışıklık yönetimi özelliklerinden birini kullanarak birden çok kuyruk tanımlarsınız.

Basit bir benzerleme vurguladı, QoS 'nin veri ağınızda sanal "Carpool yolları" oluşturmasıdır. Bu nedenle, bazı veri türleri hiçbir şekilde gecikme veya nadiren bir gecikmeyle karşılaşıyor. Bu kulvarları oluşturduktan sonra, kuruluşunuzun kullanıcıları için iş kolu deneyimlerini sunmaya devam ederken, sahip olduğunuz bağlantı bant genişliğini ayarlayabilir ve daha verimli bir şekilde yönetebilirsiniz.

## <a name="qos-implementation-checklist"></a>QoS uygulama denetim listesi

Yüksek düzeyde, QoS uygulamak için aşağıdakileri yapın:

1. [Ağınızın hazırsanız emin olun](#make-sure-your-network-is-ready)
2. [RDP ShortPath 'in etkinleştirildiğinden emin olun](./shortpath.md) -QoS ilkeleri, geriye doğru bağlantı taşıması için desteklenmez
3. Oturum konaklarında [DSCP işaretçilerinin eklenmesini Uygula](#insert-dscp-markers)

QoS uygulamaya hazırlanırken aşağıdaki yönergeleri aklınızda bulundurun:

* Oturum ana bilgisayarının en kısa yolu en iyisidir
* Proxy 'ler veya paket denetleme cihazları gibi her türlü engellerin kullanılması önerilmez

## <a name="make-sure-your-network-is-ready"></a>Ağınızın hazırsanız emin olun

Bir QoS uygulamasını düşünüyorsanız, bant genişliği gereksinimlerinizi ve diğer [ağ gereksinimlerini](/windows-server/remote/remote-desktop-services/network-guidance?context=/azure/virtual-desktop/context/context)zaten belirlemelisiniz.
  
Ağ genelindeki trafik tıkanıklığı, medya kalitesini önemli ölçüde etkiler. Bant genişliğinin bulunmaması, performans düşüşüne ve zayıf bir kullanıcı deneyimine yol açar. Windows sanal masaüstü benimseme ve kullanım büyüdükçe, sorunları belirlemek için [Log Analytics](./diagnostics-log-analytics.md) kullanın ve ardından QoS ve seçmeli bant genişliği eklemeleri kullanarak ayarlamalar yapın.

### <a name="vpn-considerations"></a>VPN konuları

QoS yalnızca istemciler ve oturum konakları arasındaki tüm bağlantılarda uygulandığında beklendiği gibi çalışmaktadır. QoS 'yi bir iç ağda kullanırsanız ve Kullanıcı uzak bir konumdan oturum açarsa, yalnızca dahili, yönetilen ağınız dahilinde öncelik verebilirsiniz. Uzak konumlar bir sanal özel ağ (VPN) uygulayarak yönetilen bir bağlantı alabilse de, VPN doğal olarak paket ek yükü ekler ve gerçek zamanlı trafikle gecikme oluşturur.

Kıtaların yayıldığı yönetilen bağlantıları olan küresel bir kuruluşta, bu bağlantılara ait bant genişliği LAN ile karşılaştırıldığında sınırlı olduğundan QoS 'yi kesinlikle öneririz.

## <a name="insert-dscp-markers"></a>DSCP işaretçileri Ekle

Oturum ana bilgisayarlarını, belirli bir trafik türü olarak tanımlayan IP paket üstbilgilerinde bir DSCP işaretleyicisi eklemek için bir grup ilkesi nesnesi (GPO) kullanarak QoS uygulayabilirsiniz. Yönlendiriciler ve diğer ağ cihazları bu işaretlerini tanımak ve trafiği ayrı ve yüksek öncelikli bir sıraya koymak üzere yapılandırılabilir.

E-posta çalışanlarına, teslimin ne kadar iyi olduğunu ve daha hızlı teslim için ne kadar en iyi şekilde sıralanacağını belirten, DSCP işaretlerini bir posta pulu ile karşılaştırabilirsiniz. RDP akışlarına öncelik vermek üzere ağınızı yapılandırdıktan sonra, kayıp paketleri ve geç paketleri önemli ölçüde azalmalıdır.

Tüm ağ aygıtları aynı sınıflandırmalar, işaretler ve öncelikleri kullanıyorsa, gecikmeleri, bırakılan paketleri ve değişimi azaltmak veya ortadan kaldırmak mümkündür. RDP perspektifinden, gereken yapılandırma adımı paketlerin sınıflandırmasıdır ve işaretlenedir. Bununla birlikte, uçtan uca QoS 'nin başarılı olması için, RDP yapılandırmasını temeldeki ağ yapılandırmasıyla dikkatle hizalamanız gerekir.
DSCP değeri, karşılık olarak yapılandırılmış bir ağa, bir paket veya akışa verilecek önceliği belirtir.

**Çabuk iletme (EF)** DSCP sınıfıyla eşleşen 46 DSCP değerini kullanmanızı öneririz.

### <a name="implement-qos-on-session-host-using-group-policy"></a>grup ilkesi kullanarak oturum ana bilgisayarında QoS uygulama

Önceden tanımlanmış DSCP değerini ayarlamak için grup ilkesi içinde ilke tabanlı hizmet kalitesi (QoS) kullanabilirsiniz.

Etki alanına katılmış oturum konakları için bir QoS ilkesi oluşturmak üzere öncelikle grup ilkesi yönetiminin yüklendiği bir bilgisayarda oturum açın. Grup ilkesi yönetimi 'ni açın (Başlat ' ı seçin, Yönetimsel Araçlar ' ın üzerine gelin ve grup ilkesi yönetim ' i seçin) ve ardından aşağıdaki adımları uygulayın:

1. Grup ilkesi Yönetimi ' nde, yeni ilkenin oluşturulması gereken kapsayıcıyı bulun. Örneğin, tüm oturumunuz **"oturum Konakları"** ADLı bir OU 'da bulunuyorsa, oturum ana bilgisayarları OU 'sunda yeni ilke oluşturulmalıdır.

2. Uygun kapsayıcıya sağ tıklayın ve sonra **Bu etki alanında GPO oluştur ve buraya bağla**' yı seçin.

3. **Yenı GPO** iletişim kutusunda, **ad** kutusuna yeni Grup İlkesi nesnesi için bir ad yazın ve ardından **Tamam**' ı seçin.

4. Yeni oluşturulan ilkeye sağ tıkladıktan sonra **Düzenle**' yi seçin.

5. Grup İlkesi Yönetimi Düzenleyicisi, **bilgisayar yapılandırması**' nı genişletin, **Windows ayarları**' nı genişletin, **ilke tabanlı QoS**' ye sağ tıklayın ve ardından **Yeni ilke oluştur**' u seçin.

6. **İlke tabanlı QoS** iletişim kutusunda, açma sayfasında, **ad** kutusuna yeni ilke için bir ad yazın. **DSCP değerini belirt** ' i seçin ve değeri **46** olarak ayarlayın. **Giden kısıtlama oranının** seçimini işaretsiz bırakın ve sonra **İleri**' yi seçin.

7. Sonraki sayfada, **yalnızca bu yürütülebilir dosya adına sahip olan uygulamalar** ' ı seçin ve **svchost.exe** adı girin ve ardından **İleri**' yi seçin. Bu ayar, ilkeye yalnızca Uzak Masaüstü hizmetindeki eşleşen trafiğe öncelik verir.

8. Üçüncü sayfada, her iki **kaynak IP adresinin** ve **HERHANGI bir hedef IP adresinin** seçildiğinden emin olun ve ardından **İleri**' yi seçin. Bu iki ayar, paketlerin hangi bilgisayar (IP adresi) tarafından gönderildiğini ve hangi bilgisayarın (IP adresi) paketleri alacağını her ne olursa olsun yönetileceğini de garanti eder.

9. 4. sayfada, **Bu QoS ilkesi için geçerli olan protokolü seçin iletişim** listesinde **UDP** ' yi seçin.

10. **Kaynak bağlantı noktası numarasını belirtin** başlığı altında **Bu kaynak bağlantı noktasından veya aralıktan** seçim yapın. Eşlik eden metin kutusunda **3390** yazın. **Son**'u seçin.

Oluşturduğunuz yeni ilkeler, grup ilkesi oturum ana bilgisayar bilgisayarlarınızda yenilenene kadar etkili olmayacaktır. Grup ilkesi düzenli olarak kendi kendine yenilese de, aşağıdaki adımları izleyerek anında yenilemeyi zorlayabilirsiniz:

1. Grup ilkesi yenilemek istediğiniz her oturum konağında, yönetici olarak bir komut Istemi açın (*yönetici olarak çalıştır*).

1. Komut istemine şunu girin:

   ```console
   gpupdate /force
   ```

### <a name="implement-qos-on-session-host-using-powershell"></a>PowerShell kullanarak oturum konağında QoS uygulama

Aşağıdaki PowerShell cmdlet 'ini kullanarak RDP Shortfılepath için QoS ayarlayabilirsiniz:

```powershell
New-NetQosPolicy -Name "RDP Shortpath" -AppPathNameMatchCondition "svchost.exe" -IPProtocolMatchCondition UDP -IPSrcPortStartMatchCondition 3390 -IPSrcPortEndMatchCondition 3390 -DSCPAction 46 -NetworkProfile All
```

## <a name="related-articles"></a>İlgili makaleler:

* [Hizmet kalitesi (QoS) Ilkesi](/windows-server/networking/technologies/qos/qos-policy-top)

## <a name="next-steps"></a>Sonraki adımlar

* Windows sanal masaüstü için bant genişliği gereksinimleri hakkında bilgi edinmek için bkz. [Windows sanal masaüstü için Uzak Masaüstü Protokolü (RDP) bant genişliği gereksinimlerini anlama](rdp-bandwidth.md).
* Windows Sanal Masaüstü Ağ bağlantısı hakkında bilgi edinmek için bkz. [Windows Sanal Masaüstü Ağ bağlantısını anlama](network-connectivity.md).
