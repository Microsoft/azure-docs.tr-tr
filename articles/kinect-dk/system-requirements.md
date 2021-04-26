---
title: Azure Kinect algılayıcı SDK sistem gereksinimleri
description: Windows ve Linux 'ta Azure Kinect algılayıcısı SDK 'Sı için sistem gereksinimlerini anlayın.
author: qm13
ms.author: quentinm
ms.custom:
- CI 115266
- CSSTroubleshooting
manager: dcscontentpm
ms.prod: kinect-dk
ms.date: 03/05/2021
ms.topic: article
keywords: Azure, Kinect, sistem gereksinimleri, CPU, GPU, USB, ayarlama, kurulum, minimum, gereksinimler
ms.openlocfilehash: 558c1b9ca264874fa808aeba5f1e8a809a8f722d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104656983"
---
# <a name="azure-kinect-sensor-sdk-system-requirements"></a>Azure Kinect algılayıcı SDK sistem gereksinimleri

Bu belge, algılayıcı SDK 'sını yüklemek için gereken sistem gereksinimleriyle ilgili ayrıntıları ve Azure Kinect DK 'yi başarıyla dağıtmanızı sağlar.

## <a name="supported-operating-systems-and-architectures"></a>Desteklenen işletim sistemleri ve mimariler

- Windows 10 Nisan 2018 (sürüm 1803, OS Build 17134) sürüm (x64) veya sonraki bir sürüm
- Linux Ubuntu 18,04 (x64), OpenGLv 4.4 veya sonraki bir sürümü kullanan bir GPU sürücüsüyle

Algılayıcı SDK, yerel C/C++ Windows uygulamaları için Windows API (Win32) için kullanılabilir. SDK Şu anda UWP uygulamaları için kullanılamaz. Azure ınect DK, Windows 10 ' da S modunda desteklenmez.

## <a name="development-environment-requirements"></a>Geliştirme ortamı gereksinimleri

Algılayıcı SDK geliştirmeye katkıda bulunmak için [GitHub](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK)' ı ziyaret edin.

## <a name="minimum-host-pc-hardware-requirements"></a>En düşük Konak bilgisayar donanım gereksinimleri

PC konak donanım gereksinimi, ana bılgısayarda yürütülen uygulama/algoritma/algılayıcı çerçeve hızına/çözünürlüğe bağımlıdır. Windows için önerilen en düşük algılayıcı SDK yapılandırması şunlardır:

- Yedinci Gen Intel &reg; CoreTM I3 işlemcisi (HD620 GPU veya daha hızlı Ile çift çekirdekli 2,4 GHz)
- 4 GB bellek
- Adanmış USB3 bağlantı noktası
- OpenGL 4,4 veya DirectX 11,0 için grafik sürücüsü desteği

Kullanım durumunuza bağlı olarak, alt uç veya daha eski CPU 'Lar de çalışabilir.

Performans ayrıca Windows/Linux işletim sistemleri ve kullanılan grafik sürücüleri arasında farklılık gösterir.

## <a name="body-tracking-host-pc-hardware-requirements"></a>Gövde izleme Konak bilgisayar donanım gereksinimleri

Gövde izleme BILGISAYAR ana bilgisayar gereksinimi, genel BILGISAYAR ana bilgisayar gereksiniminden daha sıkı. Windows için önerilen en düşük gövde Izlemesi SDK yapılandırması şunlardır:

- Yedinci Gen Intel &reg; CoreTM I5 işlemcisi (dört çekirdekli 2,4 GHz veya daha hızlı)
- 4 GB bellek
- NVıDıA GEFORCE GTX 1050 veya eşdeğeri
- Adanmış USB3 bağlantı noktası

Önerilen en düşük yapılandırma, K4A_DEPTH_MODE_NFOV_UNBINNED derinlik modunun, 30fps izleme 5 kişi üzerinde olduğunu varsayar. Küçük veya daha eski CPU 'Lar ve NVıDıA GPU 'lar, kullanım durumunuza göre de çalışabilir.

## <a name="usb3"></a>USB3

USB ana bilgisayar denetleyicileri ile ilgili bilinen uyumluluk sorunları vardır. [Sorun giderme sayfasında](troubleshooting.md#usb3-host-controller-compatibility) daha fazla bilgi edinebilirsiniz

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Kinect DK genel bakış](about-azure-kinect-dk.md)

- [Azure Kinect DK’yi kurma](set-up-azure-kinect-dk.md)

- [Azure Kinect gövdesi izlemeyi ayarlama](body-sdk-setup.md)
