---
title: Azure sanal makinelerinde TmaxSoft OpenFrame ile çalışmaya başlama
description: Azure sanal makinelerinde (VM) TmaxSoft OpenFrame ortamını kullanarak IBM z/OS ana bilgisayar iş yüklerinizi yeniden barındırın.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 346c331596f984cbb068c03110997c2478499488
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950680"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>Azure 'da TmaxSoft OpenFrame ile çalışmaya başlama

Mevcut ana bilgisayar varlıklarınızı alıp TmaxSoft OpenFrame kullanarak Microsoft Azure taşıyın. Bu popüler yeniden barındırma çözümü, Azure 'da bir öykünme ortamı oluşturarak uygulamaları hızlıca geçirmenize olanak sağlar. Yeniden biçimlendirme gerekli değildir.

## <a name="openframe-rehosting-environment"></a>OpenFrame yeniden barındırma ortamı

Geliştirme, tanıtımlar, test veya üretim iş yükleri için Azure 'da bir OpenFrame ortamı ayarlayın. Aşağıdaki şekilde gösterildiği gibi, OpenFrame, Azure 'da ana bilgisayar öykünme ortamını oluşturan birden çok bileşeni içerir. Örneğin, OpenFrame çevrimiçi hizmetler IBM müşteri bilgileri denetim sistemi (CICS) gibi ana bilgisayar ara yazılımını değiştirir. OpenFrame Batch, TJES bileşeniyle birlikte IBM ana bilgisayarın Iş girişi alt sistemini (JES) değiştirir. 

![OpenFrame yeniden barındırma işlemi](media/openframe-01.png)

> [!NOTE]
> OpenFrame ortamını Azure 'da çalıştırmak için, TmaxSoft için geçerli bir ürün lisansınızın veya deneme lisansınızın olması gerekir.

## <a name="openframe-components"></a>OpenFrame bileşenleri

Aşağıdaki bileşenler, Azure 'daki OpenFrame ortamının bir parçasıdır:

- Ana bilgisayarlar varlıklarını çözümleyen ve sonra bunları Azure 'a geçirirken bir çözüm olan OFMiner dahil **geçiş araçları** .
- Ana bilgisayarın COBOL programlarını yorumlayan bir derleyici olan OFCOBOL dahil olmak üzere **derleyiciler**; Ana bilgisayarın PL/ı programlarını yorumlayan OFPLI; ve ana bilgisayarın assembler programlarını yorumlayan bir derleyici.
- Java Enterprise User Solution (JEUS) dahil **ön uç** bileşenleri, Java Enterprise Edition 6. ofgw için sertifikalı bir Web uygulama sunucusu ve 3270 dinleyicisi sağlayan OpenFrame Gateway bileşeni.
- **Uygulama** ortamı. OpenFrame tabanı, sistemin tamamını yöneten ara yazılımlar. OpenFrame sunucu türü C (OSC), ana bilgisayarın ara yazılım ve IBM CICS 'ın yerini alır.
- Tibero (gösterilen), Oracle Database, Microsoft SQL Server, IBM DB2 veya MySQL gibi **ilişkisel veritabanı**. OpenFrame uygulamaları veritabanıyla iletişim kurmak için açık veritabanı bağlantısı (ODBC) protokolünü kullanır.
- Sistem ve kaynaklara Kullanıcı erişimini denetleyen bir hizmet modülü olan TACF aracılığıyla **güvenlik** . 
- **Ofmanager** , Web ortamında OpenFrame 'in işlem ve yönetim işlevlerini sağlayan bir çözümdür.

![OpenFrame mimarisi](media/openframe-02.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure 'da TmaxSoft OpenFrame 'i yükle](./install-openframe-azure.md)
