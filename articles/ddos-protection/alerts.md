---
title: Azure DDoS koruma standardı için DDoS koruma uyarılarını görüntüleyin ve yapılandırın
description: Azure DDoS koruma standardı için DDoS koruma uyarılarını görüntülemeyi ve yapılandırmayı öğrenin.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: a5639d583d9b98f6527e47bf5db213cb191ebeb7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100575292"
---
# <a name="view-and-configure-ddos-protection-alerts"></a>DDoS konuma uyarılarını görüntüleme ve yapılandırma

Azure DDoS koruması standardı, DDoS saldırı analiziyle ayrıntılı saldırı öngörüleri ve görselleştirmeleri sağlar. Sanal ağlarını DDoS saldırılarına karşı koruyan müşteriler, saldırı saldırılarına karşı saldırı ve risk azaltma & raporları aracılığıyla saldırının etkilerini azaltmak için gerçekleştirilen saldırı ve eylemler hakkında ayrıntılı görünürlük sağlar. Zengin telemetri, DDoS saldırısının süresi boyunca ayrıntılı ölçümler dahil olmak üzere Azure Izleyici aracılığıyla sunulur. DDos Koruması tarafından sunulan tüm Azure İzleyici ölçümleri için uyarı yapılandırılabilir. Günlüğe kaydetme, Azure Izleme tanılama arabirimi aracılığıyla [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md), splunk (Azure Event Hubs), OMS Log Analytics ve gelişmiş analiz Için Azure depolama ile daha da tümleştirilebilir.

Bu öğreticide aşağıdakilerin nasıl yapılacağını öğreneceksiniz:

> [!div class="checklist"]
> * Azure İzleyici aracılığıyla yapılandırma
> * Portal aracılığıyla uyarıları yapılandırma
> * Azure Güvenlik Merkezi'nde uyarıları görüntüleme
> * Uyarılarınızı doğrulama ve test etme

## <a name="prerequisites"></a>Ön koşullar

- Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.
- Bu öğreticideki adımları tamamlayabilmeniz için önce bir [Azure DDoS standart koruma planı](manage-ddos-protection.md) oluşturmanız ve DDoS koruma standardının bir sanal ağ üzerinde etkinleştirilmesi gerekir.
- DDoS, bir sanal ağ içindeki kaynaklara atanan genel IP adreslerini izler. Sanal ağda genel IP adresi olan hiç kaynağınız yoksa önce genel IP adresiyle bir kaynak oluşturmanız gerekir. Azure App Service ortamları dışında, [Azure hizmetleri Için sanal ağda](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (arka uç sanal makinelerin sanal ağda bulunduğu Azure yük dengeleyiciler dahil) (klasik değil), Kaynak Yöneticisi aracılığıyla dağıtılan tüm KAYNAKLARıN genel IP adresini izleyebilirsiniz. Bu öğreticiye devam etmek için hızlı bir şekilde [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) veya [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) sanal makinesi oluşturabilirsiniz.     

## <a name="configure-alerts-through-azure-monitor"></a>Azure İzleyici aracılığıyla yapılandırma

Bu şablonlar sayesinde, tanılama oturum açmayı etkinleştirdiğiniz tüm genel IP adresleri için uyarıları yapılandıracaksınız. Bu nedenle, bu uyarı şablonlarını kullanabilmeniz için önce Tanılama ayarları etkin olan bir Log Analytics çalışma alanına ihtiyacınız olacaktır. Bkz. [DDoS tanılama günlüğünü görüntüleme ve yapılandırma](diagnostic-logging.md).

### <a name="azure-monitor-alert-rule"></a>Azure Izleyici uyarı kuralı

Bu [Azure izleyici uyarı kuralı](https://aka.ms/DDOSmitigationstatus) , etkin bir DDoS risk azaltma ne zaman meydana geldiğini algılamak için basit bir sorgu çalıştırır. Bu, olası bir saldırı olduğunu gösterir. Eylem grupları, uyarının sonucu olarak eylemleri çağırmak için kullanılabilir.

[![Azure’a dağıtın](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FAzure%2520Monitor%2520Alert%2520-%2520DDoS%2520Mitigation%2520Started%2FDDoSMitigationStarted.json)

### <a name="azure-monitor-alert-rule-with-logic-app"></a>Mantıksal uygulama ile Azure Izleyici uyarı kuralı

Bu [şablon](https://aka.ms/ddosalert) , zenginleştirilmiş bir DDoS azaltma uyarısının gerekli bileşenlerini dağıtır: Azure izleyici uyarı kuralı, eylem grubu ve mantıksal uygulama. İşlemin sonucu, IP adresi hakkında, IP ile ilişkili kaynakla ilgili bilgiler dahil olmak üzere, saldırı kapsamındaki IP adresiyle ilgili ayrıntıların bulunduğu bir e-posta uyarısıdır. Kaynağın sahibi, güvenlik ekibi ile birlikte e-postanın bir alıcısı olarak eklenir. Temel bir uygulama kullanılabilirlik testi de gerçekleştirilir ve sonuçlar e-posta uyarısına eklenir.

[![Azure’a dağıtın](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FDDoS%2520Mitigation%2520Alert%2520Enrichment%2FEnrich-DDoSAlert.json)

## <a name="configure-alerts-through-portal"></a>Portal aracılığıyla uyarıları yapılandırma

Azure Izleyici uyarı yapılandırması kullanılarak saldırı sırasında etkin bir risk azaltma olduğunda sizi uyarmak için kullanılabilir DDoS koruma ölçümlerinden herhangi birini seçebilirsiniz. 

1. [Azure Portal](https://portal.azure.com/) oturum açın ve DDoS koruma planınıza gidin.
2. **İzleme** seçeneğinin altından **Ölçümler**’i seçin.
3. Gri gezinti çubuğunda **Yeni uyarı kuralı**' nı seçin. 
4. Kendi değerlerinizi girin veya seçin veya aşağıdaki örnek değerleri girin, kalan Varsayılanları kabul edin ve ardından **Uyarı kuralı oluştur**' u seçin:

    |Ayar                  |Değer                                                                                               |
    |---------                |---------                                                                                           |
    | Kapsam                   | **Kaynak seç** seçeneğini belirleyin. </br> Günlüğe kaydetmek istediğiniz genel IP adresini içeren **aboneliği** seçin, **kaynak türü** için **genel IP adresi** ' ni seçin ve ardından ÖLÇÜMLERINI günlüğe kaydetmek istediğiniz belirli genel IP adresini seçin. </br> **Bitti** seçeneğini belirleyin. | 
    | Koşul | **Koşul Seç**' i seçin. </br> Sinyal adı altında, **DDoS saldırısı altında** öğesini seçin. </br> **İşleç** altında, **büyüktür veya eşittir**' i seçin. </br> **Toplama türü** altında **en fazla**' yı seçin. </br> **Eşik değeri** altında *1* girin. For **DDoS saldırısı veya ölçüm değil** için **0** , saldırı altında değil, saldırı altında **olduğunuz anlamına gelir** . </br> **Bitti** seçeneğini belirleyin. | 
    | Eylemler | **Eylem grubu Ekle**' yi seçin. </br> **Eylem grubu oluştur**'u seçin. </br> **Bildirimler**' in altında, **bildirim türü** altında **e-posta/SMS ileti/gönderim/ses**' i seçin </br> **Ad**' ın altında, _Myundersaldırıda Kemailalert_' i girin. </br> Düzenle düğmesine tıklayın, ardından **e-posta** ' i seçin ve aşağıdaki seçeneklerden birçoğu gerekir ve ardından **Tamam**' ı seçin. </br> **Gözden geçir ve oluştur**’u seçin. | 
    | Uyarı kuralı ayrıntıları | **Uyarı kuralı adı** altında, _Myddosalert_ yazın. |

Birkaç dakikalık saldırı algılamasında, Azure Izleyici ölçümlerinden aşağıdaki resme benzer bir e-posta almalısınız:

![Saldırı Uyarısı](./media/manage-ddos-protection/ddos-alert.png)

Ayrıca, uyarı oluşturmak için [Web kancaları](../azure-monitor/alerts/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ve [Logic Apps](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) yapılandırma hakkında daha fazla bilgi edinebilirsiniz.

## <a name="view-alerts-in-azure-security-center"></a>Azure Güvenlik Merkezi'nde uyarıları görüntüleme

Azure Güvenlik Merkezi, sorunları araştırmaya ve düzeltmeye yardımcı olacak bilgiler içeren [güvenlik uyarılarının](../security-center/security-center-managing-and-responding-alerts.md)bir listesini sağlar. Bu özellikle, DDoS saldırılarıyla ilgili uyarılar ve saldırıyı neredeyse güncel olarak azaltmak için gerçekleştirilecek eylemler dahil olmak üzere uyarıların birleştirilmiş bir görünümünü alırsınız.
Tüm DDoS saldırıları algılama ve risk azaltma için göreceğiniz iki özel uyarı vardır:

- **Genel IP Için DDoS saldırısı algılandı**: Bu uyarı, DDoS Koruması hizmeti, genel IP adreslerinizin bir DDoS saldırısının hedefi olduğunu algıladığında üretilir.
- **Genel IP Için DDoS saldırısı azaltılmıştır**: Bu uyarı, genel IP adresindeki bir saldırı azaltıldığında oluşturulur.
Uyarıları görüntülemek için Azure portal **Güvenlik Merkezi** 'ni açın. **Tehdit koruması** altında **güvenlik uyarıları**' nı seçin. Aşağıdaki ekran görüntüsünde, DDoS saldırı uyarılarının bir örneği gösterilmektedir.

![Azure Güvenlik Merkezi 'nde DDoS uyarısı](./media/manage-ddos-protection/ddos-alert-asc.png)

Uyarılar, saldırı altında genel IP adresi, coğrafi ve tehdit bilgileri ve düzeltmeler adımları hakkında genel bilgiler içerir.

## <a name="validate-and-test"></a>Doğrula ve test et

Uyarıları doğrulamak için DDoS saldırısının benzetimini yapmak için bkz. [DDoS algılamayı doğrulama](test-through-simulations.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

- Azure İzleyici aracılığıyla yapılandırma
- Portal aracılığıyla uyarıları yapılandırma
- Azure Güvenlik Merkezi'nde uyarıları görüntüleme
- Uyarılarınızı doğrulama ve test etme

DDoS saldırısını test etme ve benzetimini yapma hakkında bilgi edinmek için bkz. simülasyon test Kılavuzu:

> [!div class="nextstepaction"]
> [Simülasyonlar aracılığıyla test etme](test-through-simulations.md)
