---
title: F5 verilerini Azure Sentinel 'e bağlama | Microsoft Docs
description: F5 verilerini Azure Sentinel 'e bağlamayı öğrenin.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: rkarlin
ms.openlocfilehash: 9e7ceee07cfc81953709e3077a6af14388e40e99
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73475827"
---
# <a name="connect-f5-to-azure-sentinel"></a>F5 'i Azure Sentinel 'e bağlama

Bu makalede F5 gerecinizi Azure Sentinel 'e nasıl bağlayabileceğiniz açıklanır. F5 Data Connector, panoları görüntülemek, özel uyarılar oluşturmak ve araştırmayı geliştirmek için F5 günlüklerinizi Azure Sentinel ile kolayca bağlamanıza olanak tanır. Azure Sentinel 'de F5 'in kullanılması, kuruluşunuzun Internet kullanımı hakkında daha fazla öngörü sağlar ve güvenlik işlemi yeteneklerini geliştirir. 


## <a name="how-it-works"></a>Nasıl çalışır

F5 ve Azure Sentinel arasındaki iletişimi desteklemek için adanmış bir Linux makinesine (VM veya şirket içi) bir aracı dağıtmanız gerekir. Aşağıdaki diyagramda, Azure 'daki bir Linux sanal makinesi olayında kurulum açıklanmaktadır.

 ![Azure 'da CEF](./media/connect-cef/cef-syslog-azure.png)

Alternatif olarak, başka bir bulutta veya şirket içi bir makinede bir VM kullanıyorsanız bu kurulum mevcut olacaktır. 

 ![Şirket içi CEF](./media/connect-cef/cef-syslog-onprem.png)


## <a name="security-considerations"></a>Güvenlikle ilgili dikkat edilmesi gerekenler

Makinenin güvenliğini kuruluşunuzun güvenlik ilkesine göre yapılandırdığınızdan emin olun. Örneğin, ağınızı kurumsal ağ güvenlik ilkenize göre olacak şekilde yapılandırabilir ve gereksinimlerinize göre uyum sağlamak için arka plan programındaki bağlantı noktalarını ve protokolleri değiştirmelisiniz. Aşağıdaki yönergeleri kullanarak makinenizin güvenlik yapılandırmasını geliştirebilirsiniz:  [Azure 'Da GÜVENLI VM](../virtual-machines/linux/security-policy.md), [ağ güvenliği için en iyi uygulamalar](../security/fundamentals/network-best-practices.md).

Güvenlik çözümü ve Syslog makinesi arasındaki TLS iletişimini kullanmak için Syslog Daemon (rsyslog veya Syslog-ng) ' i TLS ile iletişim kurmak üzere yapılandırmanız gerekir: [Syslog TRAFIĞINI TLS-rsyslog Ile şifreleme](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), [günlük iletilerini TLS ile şifreleme – Syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).

 
## <a name="prerequisites"></a>Önkoşullar
Proxy olarak kullandığınız Linux makinenin aşağıdaki işletim sistemlerinden birini çalıştırdığından emin olun:

- 64 bit
  - CentOS 6 ve 7
  - Amazon Linux 2017,09
  - Oracle Linux 6 ve 7
  - Red Hat Enterprise Linux Server 6 ve 7
  - Borçlu GNU/Linux 8 ve 9
  - Ubuntu Linux 14,04 LTS, 16,04 LTS ve 18,04 LTS
  - SUSE Linux Enterprise Server 12
- 32 bit
   - CentOS 6
   - Oracle Linux 6
   - Red Hat Enterprise Linux Server 6
   - Borçlu GNU/Linux 8 ve 9
   - Ubuntu Linux 14,04 LTS ve 16,04 LTS
 
 - Daemon sürümleri
   - Syslog-NG: 2,1-3.22.1
   - Rsyslog: V8
  
 - Syslog RFC 'Leri destekleniyor
   - Syslog RFC 3164
   - Syslog RFC 5424
 
Makinenizin aynı zamanda aşağıdaki gereksinimleri karşıladığından emin olun: 
- İzinler
    - Makinenizde yükseltilmiş izinleriniz (sudo) olmalıdır. 
- Yazılım gereksinimleri
    - Makinenizde Python 'un çalıştığından emin olun
## <a name="step-1-deploy-the-agent"></a>1\. Adım: aracıyı dağıtma

Bu adımda, Azure Sentinel ve güvenlik çözümünüz arasında proxy görevi görecek Linux makinesini seçmeniz gerekir. Proxy makinede şu komutu çalıştırmanız gerekir:
- Log Analytics aracısını yükleyip TCP üzerinden 514 numaralı bağlantı noktasında syslog iletilerini dinlemek ve CEF iletilerini Azure Sentinel çalışma alanınıza göndermek için gereken şekilde yapılandırır.
- , 25226 bağlantı noktasını kullanarak CEF iletilerini Log Analytics aracısına iletmek için Syslog Daemon programını yapılandırır.
- Verileri toplamak ve Log Analytics ayrıştırıldığında ve zenginleştirılabildiği yere güvenli bir şekilde göndermek için Syslog aracısını ayarlar.
 
 
1. Azure Sentinel portalında, **veri bağlayıcıları** ' na tıklayın ve **F5** ' i seçip **bağlayıcı sayfası**' nı açın. 

1. **Syslog aracısını yükleyip yapılandırma**altında, Azure, diğer bulut ya da şirket içi makine türünü seçin. 
   > [!NOTE]
   > Sonraki adımdaki betik Log Analytics aracısını yükleyip makineyi Azure Sentinel çalışma alanınıza bağladığından, bu makinenin başka bir çalışma alanına bağlı olmadığından emin olun.
1. Makinenizde yükseltilmiş izinleriniz (sudo) olmalıdır. Aşağıdaki komutu kullanarak makinenizde Python olduğundan emin olun: `python –version`

1. Proxy makinenizde aşağıdaki betiği çalıştırın.
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. Betik çalışırken, herhangi bir hata veya uyarı iletisi aldığınızdan emin olmak için kontrol edin.


## <a name="step-2-configure-your-f5-to-send-cef-messages"></a>2\. Adım: F5 'nizi CEF iletileri gönderecek şekilde yapılandırma

1. F5 ' e giderek [uygulama güvenliği olay günlüğünü yapılandırma](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html)' ya gidin ve aşağıdaki yönergeleri kullanarak uzaktan günlüğe kaydetmeyi ayarlamak için yönergeleri izleyin:
   - **Uzak depolama türünü** **CEF**olarak ayarlayın.
   - **Protokolü** **TCP**olarak ayarlayın.
   - **IP adresini** Syslog sunucusu IP adresi olarak ayarlayın.
   - **Bağlantı noktası numarasını** **514**olarak veya aracıyı kullanmak için ayarladığınız bağlantı noktasını ayarlayın.
   - **En büyük sorgu dizesi boyutunu** , aracısında ayarladığınız boyuta ayarlayabilirsiniz.

1. CEF olayları için Log Analytics ilgili şemayı kullanmak için, `CommonSecurityLog`aratın.

## <a name="step-3-validate-connectivity"></a>3\. Adım: bağlantıyı doğrulama

1. Günlüklerin CommonSecurityLog şeması kullanılarak alındığından emin olmak için Log Analytics açın.<br> Günlüklerinizin Log Analytics görünene kadar 20 dakikadan bu kadar bir zaman çıkabilir. 

1. Betiği çalıştırmadan önce, yapılandırdığınız Syslog proxy makinesine iletilmesini sağlamak için güvenlik çözümünüzden iletiler göndermenizi öneririz. 
1. Makinenizde yükseltilmiş izinleriniz (sudo) olmalıdır. Aşağıdaki komutu kullanarak makinenizde Python olduğundan emin olun: `python –version`
1. Aracı, Azure Sentinel ve güvenlik çözümünüz arasındaki bağlantıyı denetlemek için aşağıdaki betiği çalıştırın. Daemon iletme işleminin düzgün şekilde yapılandırıldığını, doğru bağlantı noktalarını dinlediğini ve hiçbir şeyin daemon ile Log Analytics Aracısı arasındaki iletişimi engellemediğini denetler. Betik Ayrıca, uçtan uca bağlantıyı denetlemek için ' TestCommonEventFormat ' adlı sahte iletiler gönderir. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`


## <a name="next-steps"></a>Sonraki adımlar
Bu belgede F5 'i Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats.md)başlayın.
