---
title: Uzak Masaüstü Protokolü bant genişliği gereksinimleri Windows sanal masaüstü-Azure
titleSuffix: Azure
description: Windows sanal masaüstü için RDP bant genişliği gereksinimlerini anlama.
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: 6fe12c985f5d9a519380d1d9b5a7d6c2820630c6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932344"
---
# <a name="remote-desktop-protocol-rdp-bandwidth-requirements"></a>Uzak Masaüstü Protokolü (RDP) bant genişliği gereksinimleri

Uzak Masaüstü Protokolü (RDP), sunucunun uzak grafik teslimini istemci cihazına kusursuz bir şekilde kullanmak için çeşitli teknikler kullanan gelişmiş bir teknolojidir. Kullanım örneğine, bilgi işlem kaynaklarının kullanılabilirliğine ve ağ bant genişliğine bağlı olarak, RDP en iyi kullanıcı deneyimini sunmak için çeşitli parametreleri dinamik olarak ayarlar.

Birden çok dinamik sanal kanalın (DVCs) farklı ağ aktarımları üzerinden gönderilen tek bir veri kanalına çoğullanıyor Uzak Masaüstü Protokolü. Uzak grafikler, giriş, cihaz yeniden yönlendirme, yazdırma ve daha fazlası için ayrı bir DVCs vardır. Windows sanal masaüstü ortakları, DVC arabirimlerini kullanan uzantılarını da kullanabilir.

RDP üzerinden gönderilen verilerin miktarı Kullanıcı etkinliğine göre değişir. Örneğin, bir Kullanıcı, oturumun büyük bir yanındaki temel metin içeriğiyle çalışabilir ve en az bant genişliği tüketebilir, sonra da yerel yazıcıda 200 sayfalı bir belgenin çıktısını oluşturabilir. Bu yazdırma işi, önemli miktarda ağ bant genişliği kullanır.

Bir uzak oturum kullanırken, ağınızın kullanılabilir bant genişliği, deneyiminizin kalitesini önemli ölçüde etkiler. Farklı uygulamalar ve görüntü çözünürlükleri farklı ağ yapılandırmaları gerektirir, bu nedenle ağ yapılandırmanızın gereksinimlerinizi karşıladığından emin olmak önemlidir.

## <a name="estimating-bandwidth-utilization"></a>Bant genişliği kullanımı tahmin ediliyor

RDP, farklı veri türleri için çeşitli sıkıştırma algoritmaları kullanır. Aşağıdaki tabloda, veri aktarımlarının tahmini olarak kılavuz verilmiştir:

| Veri türü | Yön | Tahmin etme |
|---|---|---|
| Uzak grafikler | Oturum Ana bilgisayarı istemciye | [Ayrıntılı yönergelere bakın](#estimating-bandwidth-used-by-remote-graphics) |
| Sinyal | Her iki yön | her 5 saniyede 20 bayt  |
| Giriş | İstemciden oturum ana bilgisayar | Veri miktarı, çoğu işlem için 100 bayttan daha az Kullanıcı etkinliğine dayalıdır  |
| Dosya aktarımları | Her iki yön | Dosya aktarımları toplu sıkıştırma kullanıyor. Yaklaşık olarak. zip sıkıştırması kullan |
| Yazdırma | Oturum Ana bilgisayarı istemciye | Yazdırma işi aktarımı, sürücüye bağlıdır ve toplu sıkıştırma kullanarak, yaklaşık olarak. zip sıkıştırması kullanın |

Diğer senaryolar, bunları nasıl kullandığınıza bağlı olarak, bant genişliği gereksinimlerinin değiştirilmesini sağlayabilir, örneğin:

* Ses veya görüntülü konferans
* Gerçek zamanlı iletişim
* Akış 4K videosu

## <a name="estimating-bandwidth-used-by-remote-graphics"></a>Uzak grafikler tarafından kullanılan bant genişliğini tahmin etme

Bant genişliği kullanımını uzak masaüstü tarafından tahmin etmek zor olabilir. Kullanıcı Etkinlikleri, uzak masaüstü trafiğinin çoğunu oluşturur. Her Kullanıcı benzersizdir ve çalışma desenlerindeki farklılıklar ağ kullanımını önemli ölçüde değiştirebilir.

Bant genişliği gereksinimlerini anlamanın en iyi yolu, gerçek Kullanıcı bağlantılarını izlamaktır. İzleme, yerleşik performans sayaçları veya ağ donatımı tarafından gerçekleştirilebilir.

Ancak çoğu durumda, Uzak Masaüstü Protokolü nasıl çalıştığını ve kullanıcılarınızın iş desenlerini analiz ederek ağ kullanımını tahmin edebilirsiniz.

Uzak protokol, uzak sunucu tarafından oluşturulan grafikleri yerel bir monitörde görüntülemek için teslim eder. Özellikle, masaüstü bit eşlemini tamamen sunucuda oluşturulmuş olarak sunar.
Bir masaüstü bit eşlemini ilk yaklaşımda basit bir görev gibi görünse de, önemli miktarda kaynak gerekir. Örneğin, bir 1080p masaüstü görüntüsü, sıkıştırılmamış biçimdeki daha fazla 8Mb ile ilgilidir. Bu görüntü, yerel olarak bağlı monitörde, bir modust ekran yenileme hızı olan 30 Hz ile görüntülenirken, yaklaşık 237 MB/sn bant genişliği gerekir.

Ağ üzerinden aktarılan veri miktarını azaltmak için, RDP, bununla sınırlı olmamak üzere birden çok teknik birleşimini kullanır.

* Kare hızı iyileştirmeleri
* Ekran içerik sınıflandırması
* İçeriğe özgü codec bileşenleri
* Aşamalı görüntü kodlaması
* İstemci tarafı önbelleğe alma

Uzak grafikleri daha iyi anlamak için aşağıdakileri göz önünde bulundurun:

* Grafiklerin daha zengin olması, daha fazla bant genişliğine sahip olur
  * Metin, pencere Kullanıcı arabirimi öğeleri ve düz renk alanlarının başka herhangi bir şeyle daha az bant genişliği tüketiyor.
  * Doğal görüntüler, bant genişliği kullanımı için en önemli katkı sağlar. Ancak, istemci tarafı önbelleğe alma, azaltmaya yardımcı olur.
* Yalnızca ekranın değiştirilen parçaları iletilir. Ekranda görünür bir güncelleştirme yoksa, hiçbir güncelleştirme gönderilmez.
* Video kayıttan yürütme ve diğer yüksek çerçeve hızı içerikleri aslında bir görüntü slayt görüntüsüdür. RDP, orijinal kare hızına yakın bir şekilde teslim etmek için uygun video codec bileşenleri kullanır. Bununla birlikte, hala grafik olur ve bant genişliği kullanımının en önemli katkıdır.
* Uzak masaüstünde boş kalma süresi veya en az ekran güncelleştirmesi anlamına gelir; Bu nedenle, ağ kullanımı boş zamanlarda en az düzeydedir.
* Uzak Masaüstü istemci penceresi küçültüldüğünde, oturum ana bilgisayardan bir grafik güncelleştirme gönderilmez.

Ağınıza yerleştirdiğiniz stres, hem uygulama iş yükünüzün çıkış çerçevesinin hızına hem de ekran çözünürlüğünüze bağlı olduğunu aklınızda bulundurun. Çerçeve hızının veya ekran çözünürlüğünden biri arttıkça bant genişliği gereksinimi de artar. Örneğin, yüksek çözünürlüklü ekran içeren hafif bir iş yükü, düzenli veya düşük çözünürlüklü hafif bir iş yüküne göre daha fazla kullanılabilir bant genişliği gerektirir. Farklı görüntü çözünürlükleri kullanılabilir bant genişlikleri gerektirir.

Aşağıdaki tabloda, farklı grafik senaryolarında kullanılan verilerin tahmini olarak yer almaktadır. Bu numaralar, 1920x1080 çözünürlüklü tek bir izleyici yapılandırması için ve hem varsayılan grafik modu hem de H. bir/AVC/AVC 444 grafik modunda geçerlidir.

| Senaryo | Varsayılan mod | H. bir/AVC 444 modu | Resimler | Senaryonun açıklaması |
|:---|---:|---:|---|---|
| Boş | 0,3 kbps | 0,3 kbps |:::image type="content" source="media/idle.png" alt-text="Boşta bağlantısının ekran görüntüsü":::| Kullanıcı işlerini duraklattı ve etkin ekran güncelleştirme yok |
| Microsoft Word | 100-150 kbps | 200-300 Kbps |:::image type="content" source="media/word.gif" alt-text="Microsoft Word canlandırması":::| Kullanıcı Microsoft Word ile etkin bir şekilde çalışıyor, yazma, grafik yapıştırma ve belgeler arasında geçiş yapma |
| Microsoft Excel | 150-200 kbps | 400-500 kbps |:::image type="content" source="media/excel.gif" alt-text="Microsoft Excel animasyonu":::| Kullanıcı Microsoft Excel ile etkin bir şekilde çalışıyor, formüllerin ve grafiklerin birden çok hücresi eşzamanlı olarak güncelleştiriliyor |
| Microsoft PowerPoint | 4-4.5 Mbps | 1.6-1.8 Mbps |:::image type="content" source="media/powerpoint.gif" alt-text="Microsoft PowerPoint Animasyonu":::| Kullanıcı Microsoft PowerPoint, yazma, yapıştırma ile etkin bir şekilde çalışır. Kullanıcı aynı zamanda zengin grafikleri değiştirme ve slayt geçişi efektlerini kullanma |
| Web 'e gözatma | 6-6.5 Mbps | 0.9-1 MB/sn |:::image type="content" source="media/web.gif" alt-text="Web 'e göz atma animasyonu":::| Kullanıcı, birden çok statik ve animasyonlu görüntü içeren grafiksel bir zengin Web sitesiyle etkin bir şekilde çalışır. Kullanıcı sayfaları yatay ve dikey olarak kaydırır |
| Görüntü Galerisi | 3.3-3.6 Mbps | 0.7-0.8 Mbps |:::image type="content" source="media/image-gallery.gif" alt-text="Görüntü Galerisi animasyonu":::| Kullanıcı, görüntü Galerisi uygulamasıyla etkin bir şekilde çalışır. görüntüleri tarama, yakınlaştırma, yeniden boyutlandırma ve döndürme |
| Video kayıttan yürütme | 8.5-9,5 Mbps | 2.5-2.8 Mbps |:::image type="content" source="media/video.gif" alt-text="Video kayıttan yürütme animasyonu":::| Kullanıcı, ekranın 1/2 tüketen bir 30 FPS videosu izliyor |
| Tam ekran görüntüsü yürütme | 7,5-8.5 Mbps | 2.5-3.1 Mbps |:::image type="content" source="media/fullscreen-video.gif" alt-text="Tam ekran video kayıttan yürütme animasyonu":::| Kullanıcı, tam ekran olarak ekranı kaplayan 30 FPS 'e sahip bir video izliyor |

## <a name="dynamic-bandwidth-allocation"></a>Dinamik bant genişliği ayırma

Uzak Masaüstü Protokolü, değişen ağ koşullarına dinamik olarak ayarlanmaları için tasarlanan modern bir protokoldür.
RDP, bant genişliği kullanımının sabit sınırlarını kullanmak yerine, kullanılabilir ağ bant genişliğini ve paket gidiş dönüş süresini etkin bir şekilde izleyen sürekli ağ algılama kullanır. Bulguları temel alarak, RDP, grafik kodlama seçeneklerini dinamik olarak seçer ve cihaz yeniden yönlendirmesi ve diğer sanal kanallar için bant genişliğini ayırır.  
Bu teknoloji, RDP 'nin kullanılabilir olduğunda tam ağ kanalını kullanmasına ve ağın başka bir şey için gerektiğinde hızla devre dışı bırakmanıza olanak tanır.
RDP, diğer uygulamaların ağı istemesi durumunda görüntü kalitesini, çerçeve hızını veya sıkıştırma algoritmalarının olduğunu algılar ve ayarlar.

## <a name="limit-network-bandwidth-use-with-throttle-rate"></a>Ağ bant genişliği kullanımını azaltma oranı ile sınırla

Çoğu senaryoda, sınırlandırma Kullanıcı deneyimini etkileyebileceğinden bant genişliği kullanımını sınırlandırmanıza gerek yoktur. Ancak kısıtlı ağlarda ağ kullanımını sınırlamak isteyebilirsiniz. Diğer bir örnek, kullanılan trafik miktarı için ücretlendirilen kiralanmış ağlardır.

Bu gibi durumlarda, QoS Ilkesinde bir kısıtlama oranı belirterek RDP giden ağ trafiğini sınırlayabilirsiniz.

  >[!NOTE]
  > [RDP ShortPath özelliğinin etkin](./shortpath.md) olduğundan ve ters bağlantı taşıması için azaltma hızı sınırlandırmasının desteklenmediğinden emin olun.

### <a name="implement-throttle-rate-limiting-on-session-host-using-group-policy"></a>grup ilkesi kullanarak oturum ana bilgisayarında kısıtlama oranı sınırlaması uygulama

Önceden tanımlı kısıtlama oranını ayarlamak için grup ilkesi içinde ilke tabanlı hizmet kalitesi (QoS) kullanabilirsiniz.

Etki alanına katılmış oturum konakları için bir QoS ilkesi oluşturmak üzere öncelikle grup ilkesi yönetiminin yüklendiği bir bilgisayarda oturum açın. Grup ilkesi yönetimi 'ni açın (Başlat ' ı seçin, Yönetimsel Araçlar ' ın üzerine gelin ve grup ilkesi yönetim ' i seçin) ve ardından aşağıdaki adımları uygulayın:

1. Grup ilkesi Yönetimi ' nde, yeni ilkenin oluşturulması gereken kapsayıcıyı bulun. Örneğin, tüm oturumunuz, **oturum** ana bilgisayarları ADLı bir OU 'da bulunuyorsa, oturum ana bilgisayarları OU 'da yeni ilke oluşturulmalıdır.

2. Uygun kapsayıcıya sağ tıklayın ve sonra **Bu etki alanında GPO oluştur ve buraya bağla**' yı seçin.

3. **Yenı GPO** iletişim kutusunda, **ad** kutusuna yeni Grup İlkesi nesnesi için bir ad yazın ve ardından **Tamam**' ı seçin.

4. Yeni oluşturulan ilkeye sağ tıkladıktan sonra **Düzenle**' yi seçin.

5. Grup İlkesi Yönetimi Düzenleyicisi, **bilgisayar yapılandırması**' nı genişletin, **Windows ayarları**' nı genişletin, **ilke tabanlı QoS**' ye sağ tıklayın ve ardından **Yeni ilke oluştur**' u seçin.

6. **İlke tabanlı QoS** iletişim kutusunda, açma sayfasında, **ad** kutusuna yeni ilke için bir ad yazın. **Giden kısıtlama oranını belirtin** ' i seçin ve gerekli değeri ayarlayın ve ardından **İleri**' yi seçin.

7. Sonraki sayfada, **yalnızca bu yürütülebilir dosya adına sahip olan uygulamalar** ' ı seçin ve **svchost.exe** adı girin ve ardından **İleri**' yi seçin. Bu ayar, ilkeye yalnızca Uzak Masaüstü hizmetindeki eşleşen trafiğe öncelik verir.

8. Üçüncü sayfada, her iki **kaynak IP adresinin** ve **HERHANGI bir hedef IP adresinin** seçildiğinden emin olun. **İleri**’yi seçin. Bu iki ayar, paketlerin hangi bilgisayar (IP adresi) tarafından gönderildiğini ve hangi bilgisayarın (IP adresi) paketleri alacağını her ne olursa olsun yönetileceğini de garanti eder.

9. 4. sayfada, **Bu QoS ilkesi için geçerli olan protokolü seçin iletişim** listesinde **UDP** ' yi seçin.

10. **Kaynak bağlantı noktası numarasını belirtin** başlığı altında **Bu kaynak bağlantı noktasından veya aralıktan** seçim yapın. Eşlik eden metin kutusunda **3390** yazın. **Son**'u seçin.

Oluşturduğunuz yeni ilkeler, grup ilkesi oturum ana bilgisayar bilgisayarlarınızda yenilenene kadar etkili olmayacaktır. Grup ilkesi düzenli olarak kendi kendine yenilese de, aşağıdaki adımları izleyerek anında yenilemeyi zorlayabilirsiniz:

1. Grup ilkesi yenilemek istediğiniz her oturum konağında, yönetici olarak bir komut Istemi açın (*yönetici olarak çalıştır*).

2. Komut istemine şunu girin:

   ```console
   gpupdate /force
   ```

### <a name="implement-throttle-rate-limiting-on-session-host-using-powershell"></a>PowerShell kullanarak oturum konağında kısıtlama oranı sınırlaması uygulama

RDP Shortfılepath için azaltma oranını aşağıdaki PowerShell cmdlet 'ini kullanarak ayarlayabilirsiniz:

```powershell
New-NetQosPolicy -Name "RDP Shortpath" -AppPathNameMatchCondition "svchost.exe" -IPProtocolMatchCondition UDP -IPSrcPortStartMatchCondition 3390 -IPSrcPortEndMatchCondition 3390 -DSCPAction 46 -NetworkProfile All
```

## <a name="next-steps"></a>Sonraki adımlar

* Windows sanal masaüstü için bant genişliği gereksinimleri hakkında bilgi edinmek için bkz. [Windows sanal masaüstü için Uzak Masaüstü Protokolü (RDP) bant genişliği gereksinimlerini anlama](rdp-bandwidth.md).
* Windows Sanal Masaüstü Ağ bağlantısı hakkında bilgi edinmek için bkz. [Windows Sanal Masaüstü Ağ bağlantısını anlama](network-connectivity.md).
* Windows sanal masaüstü için hizmet kalitesi 'ni (QoS) kullanmaya başlamak için bkz. [Windows sanal masaüstü Için hizmet kalitesi (QoS) uygulama](rdp-quality-of-service-qos.md).
