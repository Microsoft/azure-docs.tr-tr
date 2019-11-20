---
title: Syslog verilerini Azure Sentinel 'e bağlama | Microsoft Docs
description: Syslog verilerini Azure Sentinel 'e bağlamayı öğrenin.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: rkarlin
ms.openlocfilehash: b2be563efa3c09cffaf14dec2b871f3881af1a7a
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240043"
---
# <a name="connect-your-external-solution-using-syslog"></a>Syslog kullanarak dış çözümünüzü bağlama

Syslog 'yi destekleyen şirket içi gereçlerden Azure Sentinel 'e bağlanabilirsiniz. Bu, Gereç ve Azure Sentinel arasında Linux makinesine dayalı bir aracı kullanılarak yapılır. Linux makineniz Azure 'da ise, cihazınızdan veya uygulamanızdan günlükleri Azure 'da oluşturduğunuz ayrılmış bir çalışma alanına akışa alabilir ve bağlayabilirsiniz. Linux makineniz Azure 'da değilse, Aracıınızdan günlükleri Linux için aracıyı yüklediğiniz bir adanmış şirket içi VM 'ye veya makineye akışını sağlayabilirsiniz. 

> [!NOTE]
> Gereciniz Syslog CEF 'yi destekliyorsa, bağlantı daha tamamlanmıştır ve bu seçeneği seçmeniz ve [CEF 'deki verileri bağlama](connect-common-event-format.md)bölümündeki yönergeleri izlemeniz gerekir.

## <a name="how-it-works"></a>Nasıl çalışır?

Syslog Linux için ortak olan olay günlüğü protokolüdür. Uygulamaları, yerel makinede depolanan veya bir Syslog Toplayıcıya teslim olabilir iletileri gönderir. Linux için Log Analytics Aracısı yüklendiğinde, iletileri aracıya iletmek için yerel Syslog Daemon programını yapılandırır. Aracı daha sonra iletiyi ilgili kaydın oluşturulduğu Azure Izleyici 'ye gönderir.

Daha fazla bilgi için bkz. [Azure izleyici 'de Syslog veri kaynakları](../azure-monitor/platform/data-sources-syslog.md).

> [!NOTE]
> Aracı, birden fazla kaynaktaki günlükleri toplayabilir, ancak adanmış ara sunucu makinesine yüklenmiş olmalıdır.

## <a name="connect-your-syslog-appliance"></a>Syslog gerecinizi bağlama

1. Azure Sentinel 'de **veri bağlayıcıları** ' nı seçin ve **Syslog** bağlayıcısını seçin.

2. **Syslog** dikey penceresinde **bağlayıcı sayfasını aç**' ı seçin.

3. Linux aracısını yükler:
    
    - Linux sanal makineniz Azure 'de ise **indir ve aracıyı Azure Linux sanal makinesine**Yükle ' yi seçin. **Sanal makineler** dikey penceresinde, aracının yükleneceği sanal makineleri seçin ve ardından **Bağlan**' a tıklayın.
    - Linux makineniz Azure 'da değilse, **Linux Azure olmayan makineye indir ve aracıyı**Yükle ' yi seçin. **Doğrudan aracı** DIKEY PENCERESINDE **LINUX için INDIRME ve ekleme Aracısı** için komutu kopyalayın ve bilgisayarınızda çalıştırın. 
    
   > [!NOTE]
   > Bu bilgisayarlar için güvenlik ayarlarını kuruluşunuzun güvenlik ilkesine göre yapılandırmadığınızdan emin olun. Örneğin, ağ ayarlarını kuruluşunuzun ağ güvenlik ilkesiyle hizalanacak şekilde yapılandırabilir ve güvenlik gereksinimlerine uyum sağlamak için Daemon içindeki bağlantı noktalarını ve protokolleri değiştirebilirsiniz.

4. **Çalışma alanı Gelişmiş ayarları yapılandırmanızı aç**' ı seçin.

5. **Gelişmiş ayarlar** dikey penceresinde **veri** > **Syslog**öğesini seçin. Ardından, bağlayıcının toplanacak tesisleri ekleyin.
    
    Syslog gerecinizin günlük üst bilgilerinde içerdiği tesisleri ekleyin. Bu yapılandırmayı, `/etc/rsyslog.d/security-config-omsagent.conf` klasörde **Syslog-d** içindeki Syslog gerecinizde ve **r-Syslog** `/etc/syslog-ng/security-config-omsagent.conf`içinde görebilirsiniz.
    
    Topladığımız verilerle anormal SSH oturum açma algılaması kullanmak istiyorsanız, **AUTH** ve **authprıv**ekleyin. Daha fazla bilgi için [aşağıdaki bölüme](#configure-the-syslog-connector-for-anomalous-ssh-login-detection) bakın.

6. İzlemek istediğiniz tüm özellikleri eklediğinizde ve her biri için herhangi bir önem derecesi belirlediyseniz, bu onay kutusunu **makinelerime aşağıdaki yapılandırmayı Uygula**seçeneğini belirleyin.

7. **Kaydet**’i seçin. 

8. Syslog gerecinizde belirttiğiniz olanakları gönderdiğinizden emin olun.

9. Syslog günlükleri için Azure Izleyici 'de ilgili şemayı kullanmak üzere **Syslog**için arama yapın.

10. Syslog iletilerinizi ayrıştırmak için [Azure izleyici günlük sorgularındaki Işlevleri kullanma](../azure-monitor/log-query/functions.md) bölümünde açıklanan kusto işlevini kullanabilirsiniz. Ardından, yeni bir veri türü olarak kullanmak üzere yeni bir Log Analytics işlevi olarak kaydedebilirsiniz.

### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>Anormal SSH oturum açma algılaması için Syslog bağlayıcısını yapılandırma

> [!IMPORTANT]
> Anomalous SSH oturum açma algılaması Şu anda genel önizlemededir.
> Bu özellik, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel, anormal Secure Shell (SSH) oturum açma etkinliğini belirlemek için Syslog verilerine makine öğrenimi (ML) uygulayabilir. Senaryolar şunlardır:

- Mümkün olmayan seyahat: iki konumdan iki ayrı oturum açma olayı gerçekleştiğinde, iki oturum açma olayının zaman çerçevesi içinde ulaşılamayabilir.
- Beklenmeyen konum: başarılı bir oturum açma olayının gerçekleştiği konum şüpheli. Örneğin, konum yakın zamanda görülmedi.
 
Bu algılama, syslog veri bağlayıcısının belirli bir yapılandırmasını gerektirir: 

1. Önceki yordamdaki 5. adım için hem **AUTH** hem de **authprıv** 'in izlenecek tesis olarak seçildiğinden emin olun. Tüm seçili olmaları için önem derecesi seçenekleri için varsayılan ayarları koruyun. Örneğin:
    
    > [!div class="mx-imgBorder"]
    > ![Anormal SSH oturum açma algılaması için gereken tesisler](./media/connect-syslog/facilities-ssh-detection.png)

2. Syslog bilgilerinin toplanması için yeterli zaman kullanılmasına izin verin. Ardından, **Azure Sentinel-logs**' a gidin ve aşağıdaki sorguyu kopyalayıp yapıştırın:
    
        Syslog |  where Facility in ("authpriv","auth")| extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)| where isnotempty(c) | count 
    
    Gerekirse **zaman aralığını** değiştirin ve **Çalıştır**' ı seçin.
    
    Elde edilen sayı sıfırsa, bağlayıcının yapılandırmasını onaylayın ve izlenen bilgisayarların sorgunuz için belirttiğiniz dönem için başarılı oturum açma etkinliğine sahip olduğunu doğrulayın.
    
    Elde edilen sayı sıfırdan büyükse, syslog verileri anormal SSH oturum açma algılaması için uygundur. Bu algılamayı **analiz** >  **kuralı şablonları** >  **(Önizleme) anormal SSH oturum açma algılaması**' ndan etkinleştirirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
Bu belgede, syslog şirket içi gereçlerini Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
