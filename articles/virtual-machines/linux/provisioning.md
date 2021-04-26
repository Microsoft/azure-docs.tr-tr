---
title: Linux sağlamaya genel bakış
description: Linux VM görüntülerinizi getirme veya Azure 'da kullanmak üzere yeni görüntüler oluşturma konusuna genel bakış.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: linux
ms.topic: overview
ms.workload: infrastructure
ms.date: 06/22/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 6dafa400f2ce2421db6775084befc0abeab70a04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102563238"
---
# <a name="azure-linux-vm-provisioning"></a>Azure Linux VM sağlama
Genelleştirilmiş görüntüden (paylaşılan görüntü Galerisi veya yönetilen görüntü) bir VM oluşturduğunuzda, denetim düzlemi VM oluşturmanıza ve parametreleri ve ayarları VM 'ye geçirmenize olanak tanır. Bu, VM *sağlama* olarak adlandırılır. Sağlama sırasında, platform VM 'nin önyüklendiği sırada gereken sanal makine oluşturma parametre değerlerini (konak adı, Kullanıcı adı, parola, SSH anahtarları, customData) sağlar. 

Görüntünün içindeki bir sağlama Aracısı, platform ile arabirim oluşturacak, birden çok bağımsız sağlama arabirimine bağlanan), özellikleri ve sinyali tamamladığı platforma ayarlar. 

Sağlama aracıları [Azure Linux Aracısı](../extensions/agent-linux.md)veya [Cloud-init](./using-cloud-init.md)olabilir. Bunlar Genelleştirilmiş görüntüler oluşturma [önkoşullarıdır](create-upload-generic.md) .

Sağlama aracıları, tüm onaylı [Azure Linux dağıtımları](./endorsed-distros.md)için destek sağlar ve birçok durumda, hem Cloud-init hem de Linux aracısıyla birlikte gelen onaylanan görüntüleri de bulacaksınız. Bu, sağlamayı işlemek için Cloud-init ' i sağlamak için, Linux aracısının [Azure uzantılarını](../extensions/features-windows.md)işlemeye yönelik destek sağlamasına olanak sağlar. Uzantılar için destek sağlanması, sanal makinenin VM parola sıfırlama, Azure Izleme, Azure Backup, Azure disk şifrelemesi vb. gibi ek Azure hizmetlerini desteklemeye uygun olacağı anlamına gelir.

Sağlama tamamlandıktan sonra, Cloud-init her önyüklemede çalıştırılır. Cloud-init, ağ değişiklikleri, takma geçici diski bağlama ve biçimlendirme gibi sanal makinede yapılan değişiklikleri izler ve Linux Aracısı başlatılır. Linux Aracısı sürekli olarak sunucuda çalışır ve Azure platformundan bir ' hedef durumu ' (yeni yapılandırma) arayan, uzantıları her yüklediğinizde aracı bunları işleyebilir.

Şu anda iki sağlama Aracısı varsa, Cloud-init seçtiğiniz sağlama Aracısı olmalıdır ve Linux aracısının uzantı desteği için yüklenmesi gerekir. Bu, platform iyileştirmelerinden yararlanmanızı sağlar ve Linux aracısını devre dışı bırakmanıza/kaldırmanıza olanak tanır. aracı olmadan görüntü oluşturma ve kaldırma hakkında daha fazla ayrıntı için lütfen bu [belgeleri](disable-provisioning.md)gözden geçirin.

İki aracıyı de çalıştırmayı desteklemeyen bir Linux çekirdeğe sahipseniz, ancak konak adı, customData, userName, Password ve SSH anahtarları gibi bazı sanal makine oluşturma özelliklerini ayarlamak istiyorsanız, bu belgede [aracı olmadan Genelleştirilmiş görüntüler oluşturma](no-agent.md)ve platform gereksinimlerini karşılayabilirsiniz.


## <a name="provisioning-agent-responsibilities"></a>Aracı sorumlulukları sağlama

**Görüntü sağlama**
  
- Kullanıcı hesabı oluşturma
- SSH kimlik doğrulama türlerini yapılandırma
- SSH ortak anahtarlarının ve anahtar çiftlerinin dağıtımı
- Ana bilgisayar adı ayarlanıyor
- Ana bilgisayar adı Platform DNS 'ye yayımlanıyor
- Platforma SSH ana bilgisayar anahtarı parmak izini bildirme
- Kaynak disk yönetimi
- Kaynak diski biçimlendirme ve bağlama
- Tüketme ve işleme `customData`
 
**Ağ**
  
- Platform DHCP sunucularıyla uyumluluğu geliştirmek için yolları yönetir
- Ağ arabirimi adının kararlılığını sağlar

**Çekirdek**
  
- Sanal NUMA 'yı yapılandırır (çekirdek <için devre dışı bırak `2.6.37` )
- İçin Hyper-V entropi kullanır `/dev/random`
- Kök cihaz (uzak olabilir) için SCSI zaman aşımlarını yapılandırır

**Tanılama**
  
- Konsol yeniden yönlendirme seri bağlantı noktası

## <a name="communication"></a>İletişim
Platformdan aracıya bilgi akışı iki kanal aracılığıyla gerçekleşir:

- IaaS dağıtımları için önyükleme zamanına bağlı bir DVD. DVD, gerçek SSH anahtar çiftleri dışında tüm sağlama bilgilerini içeren bir OVF uyumlu yapılandırma dosyası içerir.
- Dağıtımı ve topoloji yapılandırmasını elde etmek için kullanılan bir REST API ortaya çıkaran bir TCP uç noktası.


## <a name="azure-provisioning-agent-requirements"></a>Azure sağlama Aracısı gereksinimleri
Linux Aracısı ve Cloud-init, düzgün çalışması için bazı sistem paketlerine bağımlıdır:
- Python 2.6+
- OpenSSL 1.0+
- OpenSSH 5.3+
- Dosya sistemi yardımcı programları: `sfdisk`, `fdisk`, `mkfs`, `parted`
- Parola araçları: chpasswd, sudo
- Metin işleme araçları: SED, grep
- Ağ araçları: IP-yol
- UDF FILESYSTEMS 'ı bağlamak için çekirdek desteği.

## <a name="next-steps"></a>Sonraki adımlar

Gerekirse, [sağlamayı devre dışı bırakabilir ve Linux aracısını kaldırabilirsiniz](disable-provisioning.md).
