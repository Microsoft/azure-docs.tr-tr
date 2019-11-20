---
title: Uzak oturumlar için bant genişliği önerileri-Azure
description: Uzak oturumlar için kullanılabilir bant genişliği önerileri.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: helohr
ms.openlocfilehash: 8352858b9fd6059257b5ba7637822f85c56eb070
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802623"
---
# <a name="bandwidth-recommendations-for-remote-sessions"></a>Uzak oturumlar için bant genişliği önerileri

Uzak bir Windows oturumu kullanırken, ağınızın kullanılabilir bant genişliği, deneyiminizin kalitesini büyük ölçüde etkiler. Farklı uygulamalar ve görüntü çözünürlükleri farklı ağ yapılandırması gerektirir, bu nedenle ağınızın gereksinimlerinizi karşılayacak şekilde yapılandırıldığından emin olmak önemlidir.

>[!NOTE]
>Aşağıdaki öneriler% 0,1 ' dan az kayıp olan ağlarda geçerlidir.

## <a name="applications"></a>Uygulamalar

Aşağıdaki tabloda, sorunsuz bir kullanıcı deneyimi için önerilen en düşük bant genişlikleri listelenmektedir. 

|İş yükü        |Örnek uygulamalar                                                                                           |Önerilen bant genişliği|
|----------------|--------------------------------------------------------------------------------------------------------------|---------------------|
|Görev çalışanı     |Microsoft Word, Outlook, Excel, Adobe Reader                                                                  |1,5 @ no__t-0Mbps        |
|Office çalışanı   |Microsoft Word, Outlook, Excel, Adobe Reader, PowerPoint, Fotoğraf Görüntüleyicisi                                        |3 @ no__t-0Mbps          |
|Bilgi çalışanı|Microsoft Word, Outlook, Excel, Adobe Reader, PowerPoint, Fotoğraf Görüntüleyicisi, Java                                  |5 @ no__t-0Mbps          |
|Güç çalışanı    |Microsoft Word, Outlook, Excel, Adobe Reader, PowerPoint, Fotoğraf Görüntüleyicisi, Java, CAD/CAM, çizim/yayımlama|15 @ no__t-0Mbps         |

>[!NOTE]
>Bu öneriler, oturumda kaç kullanıcının olduğunu dikkate almaksızın geçerlidir.

Ağınıza yerleştirdiğiniz stres, hem uygulama iş yükünüzün çıkış çerçevesinin hızına hem de ekran çözünürlüğünüze bağlı olduğunu aklınızda bulundurun. Çerçeve hızının veya ekran çözünürlüğünden biri arttıkça bant genişliği gereksinimi de artar. Örneğin, yüksek çözünürlüklü ekran içeren hafif bir iş yükü, düzenli veya düşük çözünürlüklü hafif bir iş yüküne göre daha fazla kullanılabilir bant genişliği gerektirir.

Diğer senaryolar, bunları nasıl kullandığınıza bağlı olarak, bant genişliği gereksinimlerinin değiştirilmesini sağlayabilir, örneğin:

- Ses veya görüntülü konferans
- Gerçek zamanlı iletişim
- Akış 4K videosu

Bu senaryoları, oturum açma VSI gibi simülasyon araçlarını kullanarak dağıtımınıza test ettiğinizden emin olun. Ağınızın gereksinimlerini daha iyi anlamak için yük boyutunu değişir, stres testlerini çalıştırın ve uzak oturumlarda ortak Kullanıcı senaryolarını test edin. 

## <a name="display-resolutions"></a>Görüntü çözünürlükleri

Farklı görüntü çözünürlükleri kullanılabilir bant genişlikleri gerektirir. Aşağıdaki tabloda, bir saniyede 30 kareden (fps) kare hızına sahip tipik görüntü çözünürlüklerinde sorunsuz bir kullanıcı deneyimi için önerdiğimiz bant genişlikleri listelenmektedir. Bu öneriler, tek ve birden çok Kullanıcı senaryosu için geçerlidir. Statik metin okuma, daha az kullanılabilir bant genişliği gerektirme gibi 30 fps altında kare hızına sahip senaryoların olduğunu aklınızda bulundurun. 

|30 fps 'de tipik görüntü çözünürlükleri    |Önerilen bant genişliği|
|-----------------------------------------|---------------------|
|Yaklaşık 1024 × 768 piksel                      |1,5 Mbps             |
|Yaklaşık 1280 × 720 piksel                      |3 Mbps               |
|Yaklaşık 1920 × 1080 piksel                     |5 Mbps               |
|Yaklaşık 3840 × 2160 piksel (4K)                |15 Mbps              |

>[!NOTE]
>Bu öneriler, oturumda kaç kullanıcının olduğunu dikkate almaksızın geçerlidir.

## <a name="additional-resources"></a>Ek kaynaklar

İçinde olduğunuz Azure bölgesi, ağ koşullarından çok kullanıcı deneyimini etkileyebilir. Daha fazla bilgi edinmek için [Windows sanal masaüstü deneyimi tahmin aracı](https://azure.microsoft.com/services/virtual-desktop/assessment/) 'a göz atın.
