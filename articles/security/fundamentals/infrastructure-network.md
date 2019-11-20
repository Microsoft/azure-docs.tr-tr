---
title: Azure ağ mimarisi
description: Bu makale, Microsoft Azure altyapı ağının genel bir açıklamasını sağlar.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2019
ms.author: terrylan
ms.openlocfilehash: c4756c36c2243840df69f3696e7ddac3628f3a00
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727168"
---
# <a name="azure-network-architecture"></a>Azure ağ mimarisi
Azure ağ mimarisi, farklı donanım katmanları ile endüstri standardı çekirdek/dağıtım/erişim modelinin değiştirilmiş bir sürümünü izler. Katmanlar şunları içerir:

- Çekirdek (veri merkezi yönlendiricileri)
- Dağıtım (erişim yönlendiricileri ve L2 toplama). Dağıtım katmanı, L3 yönlendirmeyi L2 geçiş işleminden ayırır.
- Erişim (L2 ana bilgisayar anahtarları)

Ağ mimarisinin iki katman 2 anahtarı düzeyi vardır. Bir katman, diğer katmandaki trafiği toplar. Artıklığı birleştirmek için ikinci katman döngüsü. Mimari daha esnek bir VLAN parmak izi sağlar ve bağlantı noktası ölçeklendirmeyi geliştirir. Mimari ve L3 birbirinden farklı olur, bu, ağdaki ayrı katmanların her birinde donanım kullanımına olanak tanır ve bir katmandaki hatayı diğer katmanları etkileyerek etkilemeden en aza indirir. Trunks kullanımı, L3 altyapısına bağlantı gibi kaynak paylaşımına izin verir.

## <a name="network-configuration"></a>Ağ yapılandırması
Bir veri merkezi içindeki bir Azure kümesinin ağ mimarisi aşağıdaki cihazlardan oluşur:

- Yönlendiriciler (veri merkezi, erişim yönlendiricisi ve sınır yaprak yönlendiricileri)
- Anahtarlar (toplama ve raf üstü anahtarlar)
- Digi CMs
- Güç dağıtımı birimleri

Azure 'un iki ayrı mimarisi vardır. Bazı mevcut Azure müşterileri ve paylaşılan hizmetleri varsayılan LAN mimarisinde (DLA) bulunur, yeni bölgeler ve sanal müşteriler ise hisse 10 (Q10) mimaride bulunur. DLA mimarisi, erişim yönlendiricilerine uygulanan etkin/Pasif erişim yönlendiricileri ve güvenlik erişimi denetim listeleri (ACL 'Ler) içeren geleneksel bir ağaç tasarımıdır. Hisse 10 mimarisi, yönlendiricilerin, yönlendiricilerin yönlendiricilerde uygulanmadığı bir kapatma/kafes tasarımdır. Bunun yerine, ACL 'Ler, yazılım yük dengelemesi (SLB) veya yazılım tanımlı VLAN 'Lar aracılığıyla yönlendirmenin altına uygulanır.

Aşağıdaki diyagramda bir Azure kümesi içindeki ağ mimarisine yüksek düzeyde bir genel bakış sunulmaktadır:

![Azure ağ diyagramı](./media/infrastructure-network/network-arch.png)

### <a name="quantum-10-devices"></a>Hisse 10 cihaz
Hisse ve katman 3 ' ü, bir giyin/kafes tasarımında birden çok cihaza yayılan hisse. 10. Q10 tasarımının avantajları, var olan ağ altyapısını ölçeklendirmenin daha büyük bir özelliğini ve daha fazlasını içerir. Tasarım, kenarlık yaprak yönlendiricileri, sırt geçişleri ve raf üstü yönlendiricileri kullanır ve bu da, hataya dayanıklılık sağlamak için trafiği birden çok yol genelinde kümelere geçirir. Donanım aygıtları yerine yazılım yük dengelemesi, ağ adresi çevirisi gibi güvenlik hizmetlerini işler.

### <a name="access-routers"></a>Erişim yönlendiricileri
Dağıtım/erişim L3 yönlendiricileri (ARs) dağıtım ve erişim katmanları için birincil yönlendirme işlevini gerçekleştirir. Bu cihazlar bir çift olarak dağıtılır ve alt ağlar için varsayılan ağ geçididir. Her bir AR çifti, kapasiteye bağlı olarak birden çok L2 toplama anahtarı çiftini destekleyebilir. Maksimum sayı, cihazın kapasitesine ve hata etki alanlarına bağlıdır. Tipik bir sayı, AR çifti başına üç L2 toplama anahtar çiftliğinde oluşur.

### <a name="l2-aggregation-switches"></a>L2 toplama anahtarları  
Bu cihazlar L2 trafiği için bir toplama noktası olarak görev yapar. L2 dokusunun dağıtım katmanıdır ve büyük miktarlarda trafiği işleyebilir. Bu cihazlar trafiği topladığından, 802.1 q işlevselliğine ve bağlantı noktası toplama ve 10GE gibi yüksek bant genişliği teknolojilerine gerek duyar.

### <a name="l2-host-switches"></a>L2 ana bilgisayar anahtarları
Konaklar doğrudan bu anahtarlara bağlanır. Bunlar rafa bağlı anahtarlar veya kasa dağıtımları olabilir. 802.1 q standardı, bir VLAN 'ın yerel VLAN olarak oluşturulmasına izin verir ve bu VLAN 'ın normal (etiketlenmemiş) Ethernet çerçevelemesi olarak kabul etmesine olanak tanır. Normal koşullarda, yerel VLAN 'daki çerçeveler iletilir ve bir 802.1 q santral bağlantı noktasında etiketsiz alınır. Bu özellik, 802.1 q 'ye geçiş için tasarlandı ve 802.1 q özellikli olmayan cihazlarla uyumludur. Bu mimaride, yalnızca ağ altyapısı yerel VLAN 'ı kullanır.

Bu mimari yerel VLAN seçimi için bir standart belirler. Standart, AR cihazlarının her santral için benzersiz, yerel VLAN ve L2Aggregation to L2Aggregation Trunks olmasını sağlar. L2Aggregation to L2Host Switch Trunks, varsayılan olmayan bir yerel VLAN 'a sahiptir.

### <a name="link-aggregation-8023ad"></a>Bağlantı toplama (802.3 ad)
Bağlantı toplama, birden çok bireysel bağlantının birlikte paketlenmesini ve tek bir mantıksal bağlantı olarak ele getirilmesini sağlar. İşlemsel hata ayıklamayı kolaylaştırmak için, bağlantı noktası kanal arabirimlerini belirlemek için kullanılan sayı standartlaştırılmış olmalıdır. Ağın geri kalanı bir bağlantı noktası kanalının her iki ucunda da aynı numarayı kullanır.

L2Agg to L2Host anahtarı için belirtilen sayılar, L2Agg tarafında kullanılan bağlantı noktası kanal numaralarıdır. Sayı aralığı L2Host tarafında daha sınırlı olduğundan, standart L2Host tarafında 1 ve 2 sayılarını kullanmaktır. Bunlar, sırasıyla "a" ve "b" tarafına giden bağlantı noktası kanalına başvurur.

### <a name="vlans"></a>VLAN
Ağ mimarisi, sunucuları birlikte tek bir yayın etki alanında gruplamak için VLAN 'Lar kullanır. VLAN numaraları, 1 – 4094 numaralı VLAN 'Ları destekleyen 802.1 q standardına uygundur.

### <a name="customer-vlans"></a>Müşteri VLAN 'Ları
Çözümünüzün ayrım ve mimari ihtiyaçlarını karşılamak için Azure portal aracılığıyla dağıtabileceğiniz çeşitli VLAN uygulama seçenekleriniz vardır. Bu çözümleri sanal makineler aracılığıyla dağıtırsınız. Müşteri başvuru mimarisi örnekleri için bkz. [Azure başvuru mimarileri](https://docs.microsoft.com/azure/architecture/reference-architectures/).

### <a name="edge-architecture"></a>Edge mimarisi
Azure veri merkezleri, yüksek düzeyde yedekli ve iyi sağlanmış Ağ altyapılarına göre oluşturulmuştur. Microsoft, Azure veri merkezlerindeki ağları "gereksinim Plus bir" (N + 1) artıklık mimarisi veya daha iyi bir şekilde uygular. Veri merkezleri içindeki ve içindeki tam yük devretme özellikleri, ağ ve hizmet kullanılabilirliğinin sağlanmasına yardımcı olur. Dışarıdan, veri merkezleri adanmış, yüksek bant genişliğine sahip ağ devreleri tarafından sunulur. Bu devreler, 1200 ' den fazla internet hizmet sağlayıcısından oluşan özellikleri birden çok eşleme noktasında küresel olarak Redundantly. Bu, ağ genelinde 2.000 GB/sn 'lik potansiyel sınır kapasitesi sağlar.

Azure ağının Edge ve erişim katmanındaki yönlendiricilerin filtrelenmesi, paket düzeyinde iyi bir güvenlik sağlar ve Azure 'a bağlanma girişimlerini önlemeye yardımcı olur. Yönlendiriciler, paketlerin gerçek içeriğinin beklenen biçimde veri içermesine ve beklenen istemci/sunucu iletişim düzenine uyduğundan emin olmaya yardımcı olur. Azure, aşağıdaki ağ ayrımı ve erişim denetimi bileşenlerinden oluşan katmanlı bir mimari uygular:

- **Edge yönlendiricileri.** Bunlar, uygulama ortamını internet 'ten ayırt etmek için. Uç yönlendiriciler, ACL 'Ler kullanılarak sızlığa karşı koruma sağlamak ve erişimi kısıtlamak için tasarlanmıştır.
- **Dağıtım (erişim) yönlendiricileri.** Bunlar yalnızca Microsoft tarafından onaylanan IP adreslerine izin verir, kimlik sahtekarlığına karşı koruma sağlar ve ACL 'Leri kullanarak bağlantı kurabilir.

### <a name="ddos-mitigation"></a>DDOS azaltma
Dağıtılmış hizmet reddi (DDoS) saldırıları, çevrimiçi hizmetler güvenilirliğine gerçek bir tehdit sunma işlemine devam eder. Saldırılar daha hedeflenmiş ve gelişmiş hale geldiği için, Microsoft 'un sağladığı hizmetler daha coğrafi olarak daha farklı hale gelmiştir, bu saldırıların etkisini belirlemek ve en aza indirmek yüksek önceliktir.

[Azure DDoS koruması standardı](../../virtual-network/ddos-protection-overview.md) , DDoS saldırılarına karşı savunma sağlar. Bkz [. Azure DDoS koruması: Daha fazla bilgi için en](ddos-best-practices.md) iyi uygulamalar ve başvuru mimarileri.

> [!NOTE]
> Microsoft, tüm Azure müşterileri için varsayılan olarak DDoS koruması sağlar.
>
>

## <a name="network-connection-rules"></a>Ağ bağlantısı kuralları
Azure, ağ üzerinden Azure 'a bağlanma girişimlerini engellemek için paket düzeyinde güvenlik sağlayan uç yönlendiriciler dağıtır. Edge yönlendiricileri, paketlerin gerçek içeriğinin beklenen biçimde veri içermesini ve beklenen istemci/sunucu iletişim düzenine uygun olmasını sağlar.

Uç yönlendiriciler, uygulama ortamının internet 'ten ayırt olduğunu. Bu yönlendiriciler, sahteciliği önleme koruması sağlamak ve ACL 'Leri kullanarak erişimi kısıtlamak için tasarlanmıştır. Microsoft Edge yönlendiricilerini, sınır yönlendiricileri ve erişim yönlendiricilerine aktarım yapmasına izin verilen ağ protokollerini sınırlandırmak için katmanlı bir ACL yaklaşımı kullanarak yapılandırır.

Microsoft, erişim ve kenar konumlarında bulunan ağ cihazlarını, giriş veya Çıkış filtrelerinin uygulandığı sınır noktaları olarak davranacak şekilde konumlandırır.

## <a name="next-steps"></a>Sonraki adımlar
Microsoft 'un Azure altyapısını güvenli hale getirmeye yardımcı olma hakkında daha fazla bilgi edinmek için bkz.:

- [Azure tesisler, şirket içi ve fiziksel güvenlik](physical-security.md)
- [Azure altyapı kullanılabilirliği](infrastructure-availability.md)
- [Azure Information System bileşenleri ve sınırları](infrastructure-components.md)
- [Azure üretim ağı](production-network.md)
- [Azure SQL veritabanı güvenlik özellikleri](infrastructure-sql.md)
- [Azure üretim işlemleri ve yönetimi](infrastructure-operations.md)
- [Azure altyapı izleme](infrastructure-monitoring.md)
- [Azure altyapı bütünlüğü](infrastructure-integrity.md)
- [Azure müşteri verileri koruması](protection-customer-data.md)


