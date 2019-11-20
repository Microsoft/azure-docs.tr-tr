---
title: Grafik performans sorunlarını tanılama uzak masaüstü-Azure
description: Bu makalede, Windows sanal masaüstündeki grafiklerle ilgili performans sorunlarını tanılamak için Uzak Masaüstü Protokolü oturumlarında RemoteFX grafik sayaçlarının nasıl kullanılacağı açıklanır.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 05/23/2019
ms.author: helohr
ms.openlocfilehash: c41a433ee19969546e1db2aa583c72ed166b7ebf
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607473"
---
# <a name="diagnose-graphics-performance-issues-in-remote-desktop"></a>Uzak Masaüstü 'nde grafik performans sorunlarını tanılama

Uzak oturumlarınızda deneyim kalitesi sorunlarını tanılamak için performans Izleyicisi 'nin RemoteFX grafik bölümü altında sayaçlar verilmiştir. Bu makale, bu sayaçları kullanarak Uzak Masaüstü Protokolü (RDP) oturumları sırasında grafiklerle ilgili performans sorunlarını belirlemenize ve düzeltmenize yardımcı olur.

## <a name="find-your-remote-session-name"></a>Uzak oturum adınızı bulun

Grafik performans sayaçlarını belirlemek için uzak oturum adınızın olması gerekir. Her sayacın örneğinizi tanımlamak için bu bölümdeki yönergeleri izleyin.

1. Uzak oturumınızdan Windows komut istemi 'ni açın.
2. **Qwinsta** komutunu çalıştırın ve oturum adınızı bulun.
    - Oturumunuz çok oturumlu bir sanal makinede (VM) barındırılıyorsa: her bir sayacın örneğiniz, oturum adınızın son eki olan ("RDP-TCP 37" gibi) aynı sayı ile düzeltilir.
    - Oturumunuz sanal grafik Işlem birimlerini (vGPU) destekleyen bir VM 'de barındırılıyorsa: her bir sayacın örneği VM 'niz yerine sunucusunda depolanır. Sayaç örneklerinizin, "Win8 Enterprise VM" gibi oturum adındaki sayı yerine VM adı vardır.

>[!NOTE]
> Sayaçlar adlarında RemoteFX 'e sahip olsa da, vGPU senaryolarında uzak masaüstü grafikleri de içerirler.

## <a name="access-performance-counters"></a>Erişim performans sayaçları

Uzak oturum adınızı belirledikten sonra, uzak oturumunuz için RemoteFX grafik performans sayaçlarını toplamak üzere aşağıdaki yönergeleri izleyin.

1. **Yönetim araçları** > **Performans İzleyicisi** > **Başlat** ' ı seçin.
2. **Performans İzleyicisi** Iletişim kutusunda **izleme araçları**' nı genişletin, **Performans İzleyicisi**' ni seçin ve ardından **Ekle**' yi seçin.
3. **Sayaç Ekle** iletişim kutusunda, **kullanılabilir sayaçlar** listesinden, RemoteFX grafikleri bölümünü genişletin.
4. İzlenecek sayaçları seçin.
5. **Seçilen nesne örnekleri** listesinde, seçilen sayaçlar için izlenecek belirli örnekleri seçin ve ardından **Ekle**' yi seçin. Tüm kullanılabilir sayaç örneklerini seçmek için **tüm örnekler**' i seçin.
6. Sayaçları ekledikten sonra **Tamam**' ı seçin.

Seçilen performans sayaçları, performans Izleyicisi ekranında görünür.

>[!NOTE]
>Bir konaktaki her etkin oturum, her bir performans sayacının kendi örneğine sahiptir.

## <a name="diagnose-issues"></a>Sorunları tanılama

Grafiklerle ilgili performans sorunları genellikle dört kategoriye ayrılır:

- Düşük çerçeve hızı
- Rastgele takılması
- Yüksek giriş gecikmesi
- Kötü çerçeve kalitesi

### <a name="addressing-low-frame-rate-random-stalls-and-high-input-latency"></a>Düşük çerçeve hızı, rastgele takılması ve yüksek giriş gecikmesini adresleme

Önce çıkış çerçevelerini/Ikinci sayacı kontrol edin. İstemcinin kullanımına sunulan çerçevelerin sayısını ölçer. Bu değer, giriş çerçevelerinden/Ikinci sayaçtan azsa, çerçeveler atlanır. Performans sorunlarını belirlemek için Atlanan kare/saniye sayaçlarını kullanın.

Atlanan üç tür çerçeve atlandı/Ikinci sayaç:

- Atlanan çerçeve/saniye (yetersiz sunucu kaynağı)
- Atlanan çerçeve/saniye (yetersiz ağ kaynağı)
- Atlanan çerçeve/saniye (yetersiz Istemci kaynağı)

Atlanan herhangi bir çerçeve için yüksek bir değer, sorunun, sayacın izlediği kaynakla ilgili olduğunu gösterir. Örneğin, istemci aynı hızda çerçeveler kodunu çözmez ve sunmazsa, atlanan/saniye (Istemci kaynakları yetersiz) sayacı yüksek olur.

Çıkış çerçeveleri/Ikinci sayacı giriş çerçeveleri/Ikinci sayaç ile eşleşiyorsa, hala olağan dışı gecikme veya yanıt olduğunu fark ediyorsanız, ortalama kodlama süresi de olabilir. Kodlama, tek oturum (vGPU) senaryosunda sunucuda ve çoklu oturum senaryosunda sanal makinede gerçekleşen zaman uyumlu bir işlemdir. Ortalama kodlama süresi 33 MS altında olmalıdır. Ortalama kodlama süresi 33 MS altındaysa ancak hala performans sorunlarınız varsa, kullanmakta olduğunuz uygulama veya işletim sistemi ile ilgili bir sorun olabilir.

Uygulamayla ilgili sorunları tanılama hakkında daha fazla bilgi için bkz. [Kullanıcı girişi gecikmesi performans sayaçları](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters).

RDP, 33 MS ortalama bir kodlama süresini desteklediğinden, en fazla 30 kare/saniye bir giriş çerçevesi hızını destekler. 33 MS 'nin desteklenen en yüksek çerçeve hızının olduğunu unutmayın. Çoğu durumda, bir karenin kaynak tarafından RDP 'ye ne sıklıkta sağlandığını bağlı olarak, Kullanıcı tarafından karşılaşılan çerçeve ücreti daha düşük olacaktır. Örneğin, bir video izleme gibi görevler, tam bir giriş çerçevesi oranının 30 kare/saniye olmasını gerektirir, ancak bir belge sonucunu seyrek olarak düzenlemesi gibi daha az hesaplama gerektiren görevler, kullanıcının deneyim kalitesi.

### <a name="addressing-poor-frame-quality"></a>Kötü çerçeve kalitesini adresleme

Çerçeve kalitesi sorunlarını tanılamak için çerçeve kalitesi sayacını kullanın. Bu sayaç, çıkış çerçevesinin kalitesini kaynak çerçeve kalitesinin yüzdesi olarak ifade eder. Kalite kaybı RemoteFX veya grafik kaynağına bağlı olabilir. RemoteFX kalite kaybına neden olduysa, sorun daha yüksek kaliteli içerik göndermek için ağ veya sunucu kaynaklarının bulunmaması olabilir.

## <a name="mitigation"></a>Risk azaltma

Sunucu kaynakları performans sorunlarına neden oluyorsa, performansı artırmak için aşağıdaki yaklaşımlardan birini deneyin:

- Konak başına oturum sayısını azaltın.
- Sunucudaki belleği ve işlem kaynaklarını artırın.
- Bağlantının çözümlenme durumunu bırakın.

Ağ kaynakları performans sorunlarına neden oluyorsa, oturum başına ağ kullanılabilirliğini geliştirmek için aşağıdaki yaklaşımlardan birini deneyin:

- Konak başına oturum sayısını azaltın.
- Daha yüksek bir bant genişliği ağı kullanın.
- Bağlantının çözümlenme durumunu bırakın.

İstemci kaynakları soruna yol açıyorsa, performansı artırmak için aşağıdaki yaklaşımlardan birini deneyin:

- En son uzak masaüstü istemcisini yükler.
- İstemci makinesindeki belleği ve işlem kaynaklarını artırın.

> [!NOTE]
> Şu anda kaynak çerçevelerini/Ikinci sayacı desteklemiyoruz. Şimdilik, kaynak çerçeveler/Ikinci sayaç her zaman 0 ' ı görüntüler.

## <a name="next-steps"></a>Sonraki adımlar

- GPU ile iyileştirilmiş bir Azure sanal makinesi oluşturmak için bkz. [Windows sanal masaüstü ortamı için grafik işleme birimi (GPU) hızlandırmasını yapılandırma](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu).
- Sorun giderme ve yükseltme izlemelerine genel bakış için bkz. [sorun giderme genel bakış, geri bildirim ve destek](https://docs.microsoft.com/azure/virtual-desktop/troubleshoot-set-up-overview).
- Hizmet hakkında daha fazla bilgi edinmek için bkz. [Windows masaüstü ortamı](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
