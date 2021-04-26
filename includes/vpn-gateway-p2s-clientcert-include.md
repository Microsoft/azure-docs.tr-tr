---
title: include dosyası
description: include dosyası
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/28/2020
ms.author: cherylmc
ms.openlocfilehash: 34986ac80a309bcfd495e5782496ba560f84c5f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93041536"
---
Noktadan siteye bağlantısı olan bir VNet 'e bağlandığınız her istemci bilgisayarda bir istemci sertifikası yüklü olmalıdır. Bunu kök sertifikadan oluşturup her bir istemci bilgisayara yüklersiniz. Geçerli bir istemci sertifikası yüklemezseniz, istemci VNet 'e bağlanmayı denediğinde kimlik doğrulaması başarısız olur.

Her istemci için benzersiz bir sertifika oluşturabileceğiniz gibi, birden çok istemci için aynı sertifikayı da kullanabilirsiniz. Benzersiz istemci sertifikaları oluşturmanın avantajı, tek bir sertifikayı iptal edebiliyor olmanızdır. Aksi halde, birden fazla istemci kimlik doğrulaması için aynı istemci sertifikasını kullanıyorsa ve iptal ederseniz, bu sertifikayı kullanan tüm istemciler için yeni sertifikalar oluşturmanız ve yüklemeniz gerekir.

Aşağıdaki yöntemleri kullanarak istemci sertifikaları oluşturabilirsiniz:

* **Kurumsal sertifika:**

  * Kurumsal bir sertifika çözümü kullanıyorsanız, ortak ad değeri biçimi *adı \@ yourdomain.com* olan bir istemci sertifikası oluşturun. *Etkialanı \ Kullanıcı adı* biçimi yerine bu biçimi kullanın.

  * İstemci sertifikasının, Kullanıcı listesindeki ilk öğe olarak listelenmiş *Istemci kimlik doğrulaması* olan bir Kullanıcı sertifika şablonunu temel alan bir kullanıcı sertifikası şablonuna dayandığınızdan emin olun. Sertifikayı çift tıklayarak ve **Ayrıntılar** sekmesinde **Gelişmiş anahtar kullanımı** ' nı görüntüleyerek denetleyin.

* **Otomatik olarak imzalanan kök sertifika:** Oluşturduğunuz istemci sertifikalarının P2S bağlantılarınız ile uyumlu olması için aşağıdaki P2S sertifika makalelerinden birindeki adımları izleyin.

  Otomatik olarak imzalanan kök sertifikadan bir istemci sertifikası oluşturduğunuzda, bu, onu oluşturmak için kullandığınız bilgisayara otomatik olarak yüklenir. İstemci sertifikasını başka bir istemci bilgisayara yüklemek istiyorsanız, tüm sertifika zinciriyle birlikte bir. pfx dosyası olarak dışarı aktarın. Bunun yapılması, istemcinin kimliğini doğrulamak için gereken kök sertifika bilgilerini içeren bir. pfx dosyası oluşturur.

  Bu makalelerdeki adımlar, daha sonra dışa ve dağıtım için uyumlu bir istemci sertifikası oluşturur.

  * [Windows 10 PowerShell yönergeleri](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert): Bu yönergeler, sertifika oluşturmak için Windows 10 ve PowerShell gerektirir. Oluşturulan sertifikalar, desteklenen herhangi bir P2S istemcisine yüklenebilir.

  * [MakeCert yönergeleri](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): sertifikaları oluşturmak Için bir Windows 10 bilgisayarına erişiminiz yoksa MakeCert kullanın. MakeCert kullanım dışı olsa da, sertifika oluşturmak için kullanmaya devam edebilirsiniz. Oluşturulan sertifikaları desteklenen herhangi bir P2S istemcisine yükleyebilirsiniz.

  * [Linux yönergeleri](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md).