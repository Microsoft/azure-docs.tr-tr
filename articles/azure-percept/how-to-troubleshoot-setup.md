---
title: Azure Percept DK kurulum deneyimi sırasında sorun giderme
description: Kurulum deneyimi sırasında bulunan daha yaygın sorunlardan bazıları için sorun giderme ipuçları alın
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: 7ce13cedff9afc25900c0bf75359ae49cc29fe19
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608502"
---
# <a name="azure-percept-dk-setup-experience-troubleshooting-guide"></a>Azure Percept DK kurulum deneyimi sorun giderme kılavuzu

[Azure PERCEPT dk kurulum deneyimi](./quickstart-percept-dk-set-up.md)sırasında sık karşılaşılan sorunların çözümleri için aşağıdaki tabloya bakın. Sorununuz devam ederse Azure müşteri desteği 'ne başvurun.

|Sorun|Nedeni|Geçici çözüm|
|:-----|:------|:----------|
|Azure hesabı kaydolma sayfalarına veya Azure portal bağlanırken, önbelleğe alınmış hesapla otomatik olarak oturum açabilir. Bu, kullanmayı amaçladığı hesap değilse, belge ile tutarsız bir deneyime neden olabilir.|Bu genellikle tarayıcıda daha önce kullandığınız bir hesabı "hatırlama" gibi bir ayar nedeniyle oluşur.|Azure sayfasında, sağ üst köşedeki hesap adına tıklayın ve **Oturumu Kapat**' ı seçin. Bundan sonra doğru hesapla oturum açabileceksiniz.|
|Azure Percept DK Wi-Fi erişim noktası (SCZ-xxxx veya APD-xxxx), kullanılabilir Wi-Fi ağları listesinde görünmez.|Bu genellikle 15 dakika içinde çözümlenen geçici bir sorundur.|Ağın görünmesini bekleyin. 15 dakikadan daha uzun bir süre içinde görünmezse, cihazı yeniden başlatın.|
|Azure Percept DK Wi-Fi erişim noktası bağlantısı genellikle bağlantıyı keser.|Bunun nedeni, cihazla ana bilgisayar arasında zayıf bir bağlantı olabilir. Ayrıca, ana bilgisayardaki diğer Wi-Fi bağlantılarından kaynaklanan girişim de oluşabilir.|Antenin Dev Kit 'e doğru bir şekilde bağlı olduğundan emin olun. Geliştirme seti ana bilgisayardan uzakta değilse, daha yakınına geçmeyi deneyin. Ana bilgisayarda çalışıyorsa LTE/5G gibi diğer internet bağlantılarını devre dışı bırakın.|
|Ana bilgisayar, Azure Percept DK erişim noktasıyla bağlantı hakkında bir güvenlik uyarısı gösterir.|Bu, sonraki bir güncelleştirmede düzeltilecektir bilinen bir sorundur.|Kurulum deneyiminde ilerlemek güvenlidir.|
|Azure Percept DK Wi-Fi erişim noktası (SCZ-xxxx veya APD-xxxx) ağ listesinde görüntülenir, ancak bağlantı başarısız olur.|Bunun nedeni, Dev Kit 'in Wi-Fi erişim noktasının geçici bozulması olabilir.|Geliştirme setini yeniden başlatın ve yeniden deneyin.|
|Kurulum deneyimi sırasında Wi-Fi ağa bağlanılamıyor.|Wi-Fi ağın Şu anda Azure ile iletişim kurmak için internet bağlantısı olması gerekir. EAP [PEAP/MSCHAP], captive portalları ve kurumsal EAP-TLS bağlantısı şu anda desteklenmiyor.|Wi-Fi ağ türünün desteklendiğinden ve internet bağlantısına sahip olduğundan emin olun.|