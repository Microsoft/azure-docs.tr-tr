---
title: Syslog verilerini Azure Sentinel 'e bağlama | Microsoft Docs
description: Bir Linux makinesinde gereç ve Azure Sentinel arasında bir aracı kullanarak Syslog 'yi destekleyen herhangi bir makine veya gereci Azure Sentinel 'e bağlayın.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: d35a97b0008a7ce3069185dd557a60221776b0ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100595464"
---
# <a name="collect-data-from-linux-based-sources-using-syslog"></a>Syslog kullanarak Linux tabanlı kaynaklardan veri toplama

Linux tabanlı, syslog destekleyen makinelerden veya gereçlerden, Linux için Log Analytics Aracısı (eski adıyla OMS Aracısı) kullanarak Azure Sentinel 'e olay akışını sağlayabilirsiniz. Bunu, Log Analytics aracısını doğrudan makineye yüklemenize izin veren herhangi bir makine için yapabilirsiniz. Makinenin yerel Syslog Daemon 'u belirtilen türlerin yerel olaylarını toplar ve onları Log Analytics çalışma alanınıza akışa alacak şekilde yerel olarak iletir.

> [!NOTE]
> - Gereciniz **Syslog üzerinden ortak olay biçimini (CEF)** destekliyorsa, daha kapsamlı bir veri kümesi toplanır ve veriler koleksiyonda ayrıştırılır. Bu seçeneği seçmeniz ve [dış çözümünüzü CEF kullanarak bağlama](connect-common-event-format.md)bölümündeki yönergeleri izlemeniz gerekir.
>
> - Log Analytics, **rsyslog** veya **Syslog-ng** Daemon 'ları tarafından gönderilen iletilerin toplanmasını destekler; burada rsyslog varsayılandır. Red Hat Enterprise Linux (RHEL), CentOS ve Oracle Linux Version (**sysklog**) sürüm 5 ' te bulunan varsayılan Syslog Daemon, Syslog olay koleksiyonu için desteklenmez. Bu dağıtımların bu sürümünden Syslog verileri toplamak için rsyslog arka plan programı yüklenmeli ve sysklog ' ı değiştirecek şekilde yapılandırılmalıdır.

## <a name="how-it-works"></a>Nasıl çalışır?

**Syslog** , Linux için ortak olan bir olay günlüğü protokolüdür. **Linux için Log Analytics ARACıSı** sanal makinenize veya gerecinize yüklendiğinde, yükleme yordamı, iletileri TCP bağlantı noktası 25224 ' deki aracıya iletmek Için yerel Syslog arka plan programını yapılandırır. Aracı daha sonra iletiyi HTTPS üzerinden Log Analytics çalışma alanınıza gönderir ve burada **Azure Sentinel > günlüklerinde** Syslog tablosunda bir olay günlüğü girişi olarak ayrıştırılır.

Daha fazla bilgi için bkz. [Azure izleyici 'de Syslog veri kaynakları](../azure-monitor/agents/data-sources-syslog.md).

## <a name="configure-syslog-collection"></a>Syslog koleksiyonunu yapılandırma

### <a name="configure-your-linux-machine-or-appliance"></a>Linux makinenizi veya gereci yapılandırma

1. Azure Sentinel 'de **veri bağlayıcıları** ' nı seçin ve **Syslog** bağlayıcısını seçin.

1. **Syslog** dikey penceresinde **bağlayıcı sayfasını aç**' ı seçin.

1. Linux aracısını yükler. **Aracının yükleneceği yeri seçin altında:**
    
    **Bir Azure Linux sanal makinesi için:**
      
    1. **Aracıyı Azure Linux sanal makinesine yüklemeyi** seçin.
    
    1. **Azure Linux sanal makineleri Için indirme & yükleme aracısı >** bağlantısına tıklayın. 
    
    1. **Sanal makineler** dikey penceresinde, aracıyı yüklemek için bir sanal makineye tıklayın ve ardından **Bağlan**' a tıklayın. Bağlanmak istediğiniz her sanal makine için bu adımı tekrarlayın.
    
    **Diğer herhangi bir Linux makinesi için:**

    1. **Azure olmayan bir Linux makinesine aracıyı yüklemeyi** seçin

    1. **Azure dışı Linux makineler Için indirme & yükleme aracısı >** bağlantısına tıklayın. 

    1. **Aracılar yönetimi** dikey penceresinde **Linux sunucuları** sekmesine tıklayın, ardından **Linux için indirme ve ekleme Aracısı** için komutu kopyalayın ve Linux makinenizde çalıştırın. 
    
   > [!NOTE]
   > Bu bilgisayarlar için güvenlik ayarlarını kuruluşunuzun güvenlik ilkesine göre yapılandırmadığınızdan emin olun. Örneğin, ağ ayarlarını kuruluşunuzun ağ güvenlik ilkesiyle hizalanacak şekilde yapılandırabilir ve güvenlik gereksinimlerine uyum sağlamak için Daemon içindeki bağlantı noktalarını ve protokolleri değiştirebilirsiniz.

### <a name="configure-the-log-analytics-agent"></a>Log Analytics aracısını yapılandırma

1. Syslog Bağlayıcısı dikey penceresinin alt kısmında, **çalışma alanı aracıları yapılandırma >bağlantısını açın** .

1. **Aracılar yapılandırma** dikey penceresinde **Syslog** sekmesini seçin. Ardından, bağlayıcının toplanacak tesisleri ekleyin. **Tesis Ekle** ' yi seçin ve tesis aşağı açılan listesinden seçin.
    
    - Syslog gerecinizin günlük üst bilgilerinde içerdiği tesisleri ekleyin. 
    
    - Topladığımız verilerle anormal SSH oturum açma algılaması kullanmak istiyorsanız, **AUTH** ve **authprıv** ekleyin. Daha fazla bilgi için [aşağıdaki bölüme](#configure-the-syslog-connector-for-anomalous-ssh-login-detection) bakın.

1. İzlemek istediğiniz tüm özellikleri eklediğinizde, istenen tüm önem dereceleri onay kutularının işaretlendiğinden emin olun.

1. **Uygula**’yı seçin. 

1. VM 'niz veya gerecinizde belirttiğiniz olanakları gönderdiğinizden emin olun.

1. **Günlüklerde** Syslog günlük verilerini sorgulamak için, `Syslog` sorgu penceresine yazın.

1. Syslog iletilerinizi ayrıştırmak için [Azure izleyici günlük sorgularındaki Işlevleri kullanma](../azure-monitor/logs/functions.md) bölümünde açıklanan sorgu parametrelerini kullanabilirsiniz. Sonra sorguyu yeni bir Log Analytics işlevi olarak kaydedebilir ve yeni bir veri türü olarak kullanabilirsiniz.

> [!NOTE]
> **Hem düz Syslog *hem* de CEF iletilerini iletmek için aynı makineyi kullanma**
>
> Var olan [CEF günlüğü iletici makinenizi](connect-cef-agent.md) kullanarak, günlükleri düz Syslog kaynaklarından toplayıp iletebilirsiniz. Ancak, olayların çoğaltılmasıyla sonuçlanacak şekilde Azure Sentinel 'e her iki biçimdeki olayları göndermeyi önlemek için aşağıdaki adımları gerçekleştirmeniz gerekir.
>
>    [CEF kaynaklarınızdan veri toplamayı](connect-common-event-format.md)zaten ayarlamış ve Log Analytics aracısını yukarıdaki gibi yapılandırmış olmanız gerekir:
>
> 1. CEF biçiminde günlük gönderen her makinede, CEF iletilerini göndermek için kullanılan tesisleri kaldırmak için Syslog yapılandırma dosyasını düzenlemeniz gerekir. Bu şekilde, CEF 'de gönderilen tesisler Syslog 'da da gönderilmeyecektir. Bunun nasıl yapılacağı hakkında ayrıntılı yönergeler için bkz. [Linux aracısında Syslog yapılandırma](../azure-monitor/agents/data-sources-syslog.md#configure-syslog-on-linux-agent) .
>
> 1. Aracının Azure Sentinel 'de Syslog yapılandırmasıyla eşitlenmesini devre dışı bırakmak için bu makinelerde aşağıdaki komutu çalıştırmanız gerekir. Bu, önceki adımda yaptığınız yapılandırma değişikliğinin üzerine yazılmamasını sağlar.<br>
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>Anormal SSH oturum açma algılaması için Syslog bağlayıcısını yapılandırma

> [!IMPORTANT]
> Anomalous SSH oturum açma algılaması Şu anda genel önizlemededir.
> Bu özellik, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel, anormal Secure Shell (SSH) oturum açma etkinliğini belirlemek için Syslog verilerine makine öğrenimi (ML) uygulayabilir. Senaryolar şunlardır:

- Mümkün olmayan seyahat: iki konumdan iki ayrı oturum açma olayı gerçekleştiğinde, iki oturum açma olayının zaman çerçevesi içinde ulaşılamayabilir.
- Beklenmeyen konum: başarılı bir oturum açma olayının gerçekleştiği konum şüpheli. Örneğin, konum yakın zamanda görülmedi.
 
Bu algılama, syslog veri bağlayıcısının belirli bir yapılandırmasını gerektirir: 

1. Yukarıdaki [Log Analytics aracısını yapılandırma](#configure-the-log-analytics-agent) bölümünde 2. adım için hem **AUTH** hem de **authprıv** 'in izlenecek tesis olarak seçildiğinden ve tüm önem dereceleri seçeneklerinin seçili olduğundan emin olun. 

2. Syslog bilgilerinin toplanması için yeterli zaman kullanılmasına izin verin. Ardından, **Azure Sentinel-logs**' a gidin ve aşağıdaki sorguyu kopyalayıp yapıştırın:
    
    ```kusto
    Syslog
    | where Facility in ("authpriv","auth")
    | extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)
    | where isnotempty(c)
    | count 
    ```
    
    Gerekirse **zaman aralığını** değiştirin ve **Çalıştır**' ı seçin.
    
    Elde edilen sayı sıfırsa, bağlayıcının yapılandırmasını onaylayın ve izlenen bilgisayarların sorgunuz için belirttiğiniz dönem için başarılı oturum açma etkinliğine sahip olduğunu doğrulayın.
    
    Elde edilen sayı sıfırdan büyükse, syslog verileri anormal SSH oturum açma algılaması için uygundur. Bu algılamayı **analiz**  >   **kuralı şablonları**  >  **(Önizleme) anormal SSH oturum açma algılaması**' ndan etkinleştirirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
Bu belgede, syslog şirket içi gereçlerini Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
- Verilerinizi izlemek için [çalışma kitaplarını kullanın](tutorial-monitor-your-data.md) .

