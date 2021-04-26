---
title: "Hızlı başlangıç: Azure Sentinel 'e ekleme"
description: Bu hızlı başlangıçta, önce onu etkinleştirerek ve sonra veri kaynaklarını bağlayarak Pano üzerinde Azure Sentinel 'i nasıl yapacağınızı öğrenin.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.date: 10/14/2020
ms.custom: references_regions
ms.openlocfilehash: a5a7af768875efd4733070b85bacd1916f897f27
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063789"
---
# <a name="quickstart-on-board-azure-sentinel"></a>Hızlı başlangıç: yerleşik Azure Sentinel

Bu hızlı başlangıçta, yerleşik Azure Sentinel 'i nasıl yapacağınızı öğrenin. 

Azure Sentinel 'de, ilk olarak Azure Sentinel 'i etkinleştirmeniz ve sonra veri kaynaklarınızı bağlamanız gerekir. Azure Sentinel, Microsoft çözümleri için çok sayıda bağlayıcı sunar ve Microsoft 365 Defender (eski adıyla Microsoft tehdit koruması) çözümleri, Microsoft 365 kaynakları (Office 365 dahil), Azure AD, Microsoft Cloud App Security kimlik için Microsoft Defender, Azure Güvenlik Merkezi 'ndeki Azure Defender uyarıları ve daha fazlasını içeren gerçek zamanlı tümleştirme sağlar. Ayrıca, Microsoft dışı çözümler için daha geniş güvenlik ekosistemine yerleşik bağlayıcılar vardır. Veri kaynaklarınızı Azure Sentinel 'e bağlamak için ortak olay biçimi (CEF), syslog veya REST API 'yi de kullanabilirsiniz. 

Veri kaynaklarınızı bağlandıktan sonra, verilerinizi temel alan bir expertly oluşturulan çalışma kitapları galerisinden seçim yapın. Bu çalışma kitapları, gereksinimlerinize kolayca özelleştirilebilir.

>[!IMPORTANT] 
> Azure Sentinel 'i kullanırken oluşan ücretler hakkında bilgi için bkz. [Azure Sentinel fiyatlandırması](https://azure.microsoft.com/pricing/details/azure-sentinel/).

## <a name="global-prerequisites"></a>Genel Önkoşullar

- Etkin Azure aboneliği, yoksa, başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

- Log Analytics çalışma alanı. [Log Analytics çalışma alanı oluşturmayı](../azure-monitor/logs/quick-create-workspace.md)öğrenin. Log Analytics çalışma alanları hakkında daha fazla bilgi için bkz. [Azure Izleyici günlükleri dağıtımınızı tasarlama](../azure-monitor/logs/design-logs-deployment.md).

- Azure Sentinel 'i etkinleştirmek için, Azure Sentinel çalışma alanının bulunduğu abonelik için katkıda bulunan izinlerinizin olması gerekir. 
- Azure Sentinel 'i kullanmak için, çalışma alanının ait olduğu kaynak grubunda katkıda bulunan ya da okuyucu izinlerinizin olması gerekir.
- Belirli veri kaynaklarına bağlanmak için ek izinler gerekebilir.
- Azure Sentinel ücretli bir hizmettir. Fiyatlandırma bilgileri için bkz. [Azure Sentinel hakkında](https://go.microsoft.com/fwlink/?linkid=2104058).

### <a name="geographical-availability-and-data-residency"></a>Coğrafi kullanılabilirlik ve veri yerleşimi

- Azure Sentinel, Çin ve Almanya (Sovereign) bölgeleri hariç [Log Analytics çoğu GA](https://azure.microsoft.com/global-infrastructure/services/?products=monitor) bölgesindeki çalışma alanlarında çalıştırılabilir. Bazen yeni Log Analytics bölgelerinin Azure Sentinel hizmetini eklemek biraz zaman alabilir. 

- Olaylar, yer işaretleri ve analiz kuralları gibi Azure Sentinel tarafından oluşturulan veriler, müşterinin Log Analytics çalışma alanlarından kaynaklanan bazı müşteri verilerini içerebilir. Bu Azure Sentinel tarafından oluşturulan veriler, çalışma alanının bulunduğu coğrafi tabloya göre aşağıdaki tabloda listelenen Coğrafya içine kaydedilir:

    | Çalışma alanı Coğrafya | Azure Sentinel-üretilen veri Coğrafya |
    | --- | --- |
    | Birleşik Devletler<br>Hindistan<br>Brezilya<br>Afrika<br>Güney Kore<br>Birleşik Arap Emirlikleri | Birleşik Devletler |
    | Avrupa<br>Fransa<br>İsviçre | Avrupa |
    | Avustralya | Avustralya |
    | Birleşik Krallık | Birleşik Krallık |
    | Kanada | Kanada |
    | Japonya | Japonya |
    |

## <a name="enable-azure-sentinel"></a>Azure Sentinel 'i etkinleştirme <a name="enable"></a>

1. Azure portalında oturum açın. Azure Sentinel 'in oluşturulduğu aboneliğin seçildiğinden emin olun.

1. **Azure Sentinel**' i arayıp seçin.

   ![Hizmet arama](./media/quickstart-onboard/search-product.png)

1. **Add (Ekle)** seçeneğini belirleyin.

1. Kullanmak istediğiniz çalışma alanını seçin veya yeni bir tane oluşturun. Azure Sentinel 'i birden fazla çalışma alanında çalıştırabilirsiniz, ancak veriler tek bir çalışma alanına yalıtılmış olur.

   ![Çalışma alanı seçin](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Azure Güvenlik Merkezi tarafından oluşturulan varsayılan çalışma alanları listede görünmez; Azure Sentinel 'i bunlara yükleyemezsiniz.
   >

   >[!IMPORTANT]
   >
   > - Bir çalışma alanına dağıtıldıktan sonra, Azure Sentinel bu çalışma alanının diğer kaynak gruplarına veya aboneliklerine taşınmasını **desteklememektedir** . 
   >
   >   Çalışma alanını zaten taşıdıysanız, **analiz** altındaki tüm etkin kuralları devre dışı bırakın ve beş dakika sonra yeniden etkinleştirin. Bu durum çoğu durumda etkili olmalıdır, ancak yeniden yinelemek için, bu, sizin sorumluluğunuzdadır ve riski size aittir.

1. **Azure Sentinel Ekle**' yi seçin.

## <a name="connect-data-sources"></a>Veri kaynaklarını bağlama

Azure, hizmete bağlanarak ve olayları ve günlükleri Azure Sentinel 'e ileterek hizmetlerden ve uygulamalardan verileri alır. Fiziksel ve sanal makineler için, günlükleri toplayan ve bunları Azure Sentinel 'e ileten Log Analytics aracısını yükleyebilirsiniz. Güvenlik duvarları ve proxy 'ler için Azure Sentinel, Log Analytics aracısını aracının günlük dosyalarını topladığı ve bunları Azure Sentinel 'e ileten bir Linux Syslog sunucusuna yüklerse. 
 
1. Ana menüden **veri bağlayıcıları**' nı seçin. Bu, veri bağlayıcıları galerisini açar.

1. Galeri, bağlayabilmeniz için kullanabileceğiniz tüm veri kaynaklarının bir listesidir. Bir veri kaynağı ve ardından **bağlayıcı sayfası aç** düğmesini seçin.

1. Bağlayıcı sayfasında, bağlayıcıyı yapılandırmaya yönelik yönergeler ve gerekli olabilecek ek yönergeler gösterilir.<br>
Örneğin, Azure AD 'den Azure Sentinel 'e günlükleri akışla aktarmanıza olanak sağlayan **Azure Active Directory** veri kaynağını seçerseniz, ne tür Günlükler alınacağını ve/veya denetim günlüklerini seçebilirsiniz. <br> Daha fazla bilgi için yükleme yönergelerini izleyin veya [ilgili bağlantı kılavuzuna bakın](connect-data-sources.md) . Veri bağlayıcıları hakkında daha fazla bilgi için bkz. [Microsoft hizmetlerini bağlama](connect-data-sources.md).

1. Bağlayıcı sayfasındaki **sonraki adımlar** sekmesinde, veri bağlayıcısına eşlik eden ilgili yerleşik çalışma kitapları, örnek sorgular ve analiz kuralı şablonları gösterilmektedir. Bunları olduğu gibi kullanabilir ya da değiştirebilirsiniz. bu şekilde, verileriniz genelinde ilgi çekici Öngörüler elde edebilirsiniz. <br>

Veri kaynaklarınızı bağladıktan sonra Verileriniz Azure Sentinel 'e akışa başlar ve ile çalışmaya başlayabilirsiniz. Günlükleri [yerleşik çalışma kitaplarında](quickstart-get-visibility.md) görüntüleyebilir ve [verileri araştırmak](tutorial-investigate-cases.md)için Log Analytics sorguları oluşturmaya başlayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
Bu belgede veri kaynaklarını ekleme ve Azure Sentinel 'e bağlama hakkında bilgi edindiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
- [Ortak olay biçimi gereçlerden](connect-common-event-format.md) Azure Sentinel 'e veri akışı yapın.
