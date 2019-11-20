---
title: include dosyası
description: include dosyası
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/11/2018
ms.author: cherylmc
ms.openlocfilehash: 31ccf14c82f6248c74d6af932fe9e338d26d2747
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "67188233"
---
Noktadan siteye bağlantısı olan bir VNet 'e bağlandığınız her istemci bilgisayarda bir istemci sertifikası yüklü olmalıdır. Bunu kök sertifikadan oluşturup her bir istemci bilgisayara yüklersiniz. Geçerli bir istemci sertifikası yüklemezseniz, istemci VNet 'e bağlanmayı denediğinde kimlik doğrulaması başarısız olur.

Her istemci için benzersiz bir sertifika oluşturabileceğiniz gibi, birden çok istemci için aynı sertifikayı da kullanabilirsiniz. Benzersiz istemci sertifikaları oluşturmanın avantajı, tek bir sertifikayı iptal edebiliyor olmanızdır. Aksi halde, birden fazla istemci kimlik doğrulaması için aynı istemci sertifikasını kullanıyorsa ve iptal ederseniz, bu sertifikayı kullanan tüm istemciler için yeni sertifikalar oluşturmanız ve yüklemeniz gerekir.

Aşağıdaki yöntemleri kullanarak istemci sertifikaları oluşturabilirsiniz:

- **Kurumsal sertifika:**

  - Kurumsal bir sertifika çözümü kullanıyorsanız, ortak ad değer biçimi *adı \@yourdomain. com*olan bir istemci sertifikası oluşturun. *Etkialanı \ Kullanıcı adı* biçimi yerine bu biçimi kullanın.
  - İstemci sertifikasının, Kullanıcı listesindeki ilk öğe olarak listelenmiş *Istemci kimlik doğrulaması* olan bir Kullanıcı sertifika şablonunu temel alan bir kullanıcı sertifikası şablonuna dayandığınızdan emin olun. Sertifikayı çift tıklayarak ve **Ayrıntılar** sekmesinde **Gelişmiş anahtar kullanımı** ' nı görüntüleyerek denetleyin.

- **Otomatik olarak imzalanan kök sertifika:** Oluşturduğunuz istemci sertifikalarının P2S bağlantılarınız ile uyumlu olması için aşağıdaki P2S sertifika makalelerinden birindeki adımları izleyin. Bu makalelerdeki adımlar uyumlu bir istemci sertifikası oluşturur: 

  * [Windows 10 PowerShell yönergeleri](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert): Bu yönergeler, sertifika oluşturmak için Windows 10 ve PowerShell gerektirir. Oluşturulan sertifikalar, desteklenen herhangi bir P2S istemcisine yüklenebilir.
  * [MakeCert yönergeleri](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): sertifikaları oluşturmak Için bir Windows 10 bilgisayarına erişiminiz yoksa MakeCert kullanın. MakeCert kullanım dışı olsa da, sertifika oluşturmak için kullanmaya devam edebilirsiniz. Oluşturulan sertifikaları desteklenen herhangi bir P2S istemcisine yükleyebilirsiniz.
  * [Linux yönergeleri](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)

  Otomatik olarak imzalanan kök sertifikadan bir istemci sertifikası oluşturduğunuzda, bu, onu oluşturmak için kullandığınız bilgisayara otomatik olarak yüklenir. İstemci sertifikasını başka bir istemci bilgisayara yüklemek istiyorsanız, tüm sertifika zinciriyle birlikte bir. pfx dosyası olarak dışarı aktarın. Bunun yapılması, istemcinin kimliğini doğrulamak için gereken kök sertifika bilgilerini içeren bir. pfx dosyası oluşturur. 

**Sertifikayı dışarı aktarmak için**

Sertifikayı dışarı aktarma adımları için bkz. [PowerShell kullanarak noktadan siteye sertifika oluşturma ve dışarı aktarma](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport).
