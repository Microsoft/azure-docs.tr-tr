---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 35f506235f698fbcf42308e6f0b0f400e925df29
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361550"
---
### <a name="bastion-tier"></a>Savunma katmanı

Savunma ana bilgisayarı, uygulama ve veritabanı örneklerine erişmek için bir sıçrama sunucusu olarak kullanabileceğiniz isteğe bağlı bir bileşendir. Savunma ana bilgisayar VM 'sine atanmış bir genel IP adresi olabilir, ancak bu öneri, güvenli erişim için şirket içi ağınızla bir ExpressRoute bağlantısı veya siteden siteye VPN ayarlamak olur. Ayrıca, gelen trafik için yalnızca SSH (bağlantı noktası 22, Linux) veya RDP (bağlantı noktası 3389, Windows Server) açılmalıdır. Yüksek kullanılabilirlik için, bir savunma konağını iki kullanılabilirlik bölgesinde veya tek bir kullanılabilirlik kümesinde dağıtın.

Ayrıca sanal makinelerinizdeki SSH Aracısı iletmeyi etkinleştirerek kimlik bilgilerini savunma konağından ileterek sanal ağdaki diğer VM 'Lere erişebilirsiniz. Ya da diğer örneklere erişmek için SSH tüneli kullanın.

Aracı iletme örneği aşağıda verilmiştir:

```
ssh -A -t user@BASTION_SERVER_IP ssh -A root@TARGET_SERVER_IP`
```

Bu komut, savunma 'a bağlanır ve sonra yeniden çalışır `ssh` , bu nedenle hedef örnekte bir Terminal alırsınız. Kümeniz farklı yapılandırılmışsa, hedef örnekte kök dışında bir kullanıcı belirtmeniz gerekebilir. `-A` Bağımsız değişkeni, aracı bağlantısını iletir, böylece yerel makinenizde özel anahtarınız otomatik olarak kullanılır. Aracı iletme zincirinin bir zincirdir, bu nedenle ikinci `ssh` komut, hedef örnekten başlatılan sonraki SSH bağlantılarının de yerel özel anahtarınızı kullanmasını sağlamak için de içerir. `-A`