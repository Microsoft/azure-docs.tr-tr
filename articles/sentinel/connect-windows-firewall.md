---
title: Windows Defender güvenlik duvarı verilerini Azure Sentinel 'e bağlama | Microsoft Docs
description: Azure Sentinel 'de Windows Güvenlik Duvarı bağlayıcısını, Log Analytics aracıları yüklü Windows makinelerden kolayca Güvenlik Duvarı olaylarını akışa almak üzere etkinleştirin.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/05/2020
ms.author: yelevin
ms.openlocfilehash: cf7e389fc4a8a8dfa88691dc034611cae3471731
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94655349"
---
# <a name="connect-windows-defender-firewall-with-advanced-security-to-azure-sentinel"></a>Gelişmiş Güvenlik Özellikli Windows Defender güvenlik duvarı 'Nı Azure Sentinel 'e bağlama

[Gelişmiş Güvenlik Özellikli Windows Defender güvenlik duvarı](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) Bağlayıcısı, Azure Sentinel 'in çalışma alanınızdaki herhangi bir Windows makineden gelişmiş güvenlik günlükleri Ile Windows Defender güvenlik duvarı 'nı kolayca almasına olanak tanır. Bu bağlantı, çalışma kitaplarınızda Windows Güvenlik Duvarı olaylarını görüntülemenize ve çözümlemenize, bunları özel uyarılar oluşturmakta ve güvenlik araştırmalarınıza ekleyerek kuruluşunuzun ağına daha fazla bilgi vererek ve güvenlik işlemleri yeteneklerini iyileştirmenize olanak sağlar. 

Çözüm, Log Analytics aracısının yüklendiği Windows makinelerden Windows Güvenlik Duvarı olaylarını toplar. 

> [!NOTE]
> - Veriler, Azure Sentinel çalıştırdığınız çalışma alanının coğrafi konumunda depolanır.
>
> - Azure Güvenlik Merkezi 'nden Azure Defender uyarıları zaten Azure Sentinel çalışma alanına toplanmışsa, bu bağlayıcı aracılığıyla Windows Güvenlik Duvarı çözümünü etkinleştirmeniz gerekmez. Ancak, bunu etkinleştirirseniz yinelenen verilere neden olmaz. 

## <a name="prerequisites"></a>Önkoşullar

- İzlemek istediğiniz makinelerin bağlı olduğu çalışma alanı üzerinde okuma ve yazma izinlerinizin olması gerekir.

- Tüm **Azure Sentinel** rollerine ek olarak, bu çalışma alanındaki securityınsights çözümünde **Log Analytics katkıda** bulunan rolüne atanmalısınız. [Daha fazla bilgi edinin](../role-based-access-control/built-in-roles.md#log-analytics-contributor)

## <a name="enable-the-connector"></a>Bağlayıcıyı etkinleştirme 

1. Azure Sentinel portalında, gezinti menüsünden **veri bağlayıcıları** ' nı seçin.

1. Bağlayıcılar galerisinden **Windows Güvenlik Duvarı** ' nı seçin ve **bağlayıcı sayfasını aç**' a tıklayın.

### <a name="instructions-tab"></a>Yönergeler sekmesi

- **Windows makineleriniz Azure 'da ise:**

    1. **Azure Windows sanal makinesine aracıyı yüklemeyi** seçin.

    1. Görüntülenen **Azure Windows sanal makineler Için yükleme & yükleme aracısı >** bağlantısını tıklatın.

    1. **Sanal makineler** listesinde, Azure Sentinel 'de akışı yapmak istediğiniz Windows makinesini seçin. (Yalnızca Windows VM 'lerinin görüntülendiğinden emin olmak için işletim sistemi sütun filtresi ' nde **Windows** ' u seçebilirsiniz).

    1. Bu VM için açılan pencerede, **Bağlan**' a tıklayın.

    1. **Sanal makineler** bölmesine dönün ve bağlanmak istediğiniz diğer tüm VM 'ler için önceki iki adımı tekrarlayın. İşiniz bittiğinde **Windows Güvenlik Duvarı** bölmesine dönün.

- **Windows makineniz bir Azure VM değilse:**

    1. **Azure olmayan Windows makinesine aracıyı yüklemeyi** seçin.

    1. Görüntülenen **Azure olmayan Windows makineleri Için indirme & yükleme aracısı >** bağlantısını tıklatın.

    1. **Aracılar yönetimi** bölmesinde, gerektiğinde **Windows agent 'ı (64 bit) Indir** veya **Windows Agent 'ı (32 bit) İndir** seçeneğini belirleyin.

    1. **Çalışma alanı kimliği**, **birincil anahtar** ve **İkincil anahtar** dizelerini bir metin dosyasına kopyalayın. Bu dosyayı ve indirilen yükleme dosyasını Windows makinenize kopyalayın. Yükleme dosyasını çalıştırın ve istendiğinde, yükleme sırasında metin dosyasına KIMLIĞI ve anahtar dizelerini girin.

    1. **Windows Güvenlik Duvarı** bölmesine geri dönün.

1. **Çözümü yüklensin**' e tıklayın.

### <a name="next-steps-tab"></a>Sonraki adımlar sekmesi

- Windows Güvenlik Duvarı günlük verilerinize ilişkin Öngörüler almak için **Windows Güvenlik Duvarı** veri Bağlayıcısı ile birlikte sunulan önerilen çalışma kitaplarına ve sorgu örneklerine bakın.

- **Günlüklerde** Windows Güvenlik Duvarı verilerini sorgulamak için, sorgu penceresine **WindowsFirewall** yazın.

## <a name="validate-connectivity"></a>Bağlantıyı doğrula
 
Windows Güvenlik Duvarı günlükleri yalnızca yerel günlük dosyası kapasiteye ulaştığında Azure Sentinel 'e gönderildiğinden, günlüğü 4096 KB 'ın varsayılan boyutunda bırakmak büyük olasılıkla yüksek toplama gecikmesi oluşmasına neden olur. Günlük dosyası boyutunu azaltarak gecikme süresini düşürebilirsiniz. [Windows Güvenlik Duvarı günlüğünü yapılandırma](/windows/security/threat-protection/windows-firewall/configure-the-windows-firewall-log)yönergelerine bakın. Mümkün olan en düşük günlük boyutunu (1 KB) tanımlarken, koleksiyon gecikmesini neredeyse ortadan kaldıracak, bu durum da yerel makinenin performansını olumsuz yönde etkileyebilir. 

## <a name="next-steps"></a>Sonraki adımlar
Bu belgede, Windows Güvenlik Duvarı 'nı Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.