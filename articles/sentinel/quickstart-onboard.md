---
title: "Hızlı başlangıç: Azure Sentinel 'e ekleme"
description: Bu Hızlı Başlangıç belgesini izleyerek Azure Sentinel 'de veri toplamayı öğrenin.
services: sentinel
author: rkarlin
ms.author: rkarlin
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.date: 09/23/2019
ms.openlocfilehash: 182dec35d515966ae883aca405cf3a95b6f1f72c
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74133090"
---
# <a name="quickstart-on-board-azure-sentinel"></a>Hızlı başlangıç: yerleşik Azure Sentinel

Bu hızlı başlangıçta, yerleşik Azure Sentinel 'i nasıl yapacağınızı öğrenin. 

Azure Sentinel 'de, ilk olarak Azure Sentinel 'i etkinleştirmeniz ve sonra veri kaynaklarınızı bağlamanız gerekir. Azure Sentinel, Microsoft çözümleri için çok sayıda bağlayıcı sunar ve Microsoft tehdit koruması çözümleri Microsoft 365, Office 365, Azure AD, Azure ATP ve dahil gerçek zamanlı tümleştirme sağlar. Microsoft Cloud App Security ve daha fazlası. Ayrıca, Microsoft dışı çözümler için daha geniş güvenlik ekosistemine yerleşik bağlayıcılar vardır. Veri kaynaklarınızı Azure Sentinel 'e bağlamak için ortak olay biçimi, syslog veya REST API 'sini de kullanabilirsiniz.  

Veri kaynaklarınızı bağlandıktan sonra, verilerinizi temel alan bir expertly oluşturulan çalışma kitapları galerisinden seçim yapın. Bu çalışma kitapları, gereksinimlerinize kolayca özelleştirilebilir.

>[!NOTE] 
> [Azure Sentinel fiyatlandırması](https://azure.microsoft.com/pricing/details/azure-sentinel/) , Ilişkili Azure izleyici Log Analytics çalışma alanındaki veri miktarına göre belirlenir. Aşağıdaki veri bağlayıcılarından bazıları alma işlemi için ücretlendirilmez, ancak Log Analytics çalışma alanında depolanan giriş verilerini kullanarak her türlü uyarı oluşturduğunuzda, [Azure Izleyici Günlük Analizi Fiyatlandırması](https://azure.microsoft.com/pricing/details/monitor/)başına ücretlendirilecektir. 
  > - Azure etkinliği (Azure Işlemleri için etkinlik günlükleri)
  > - Azure Active Directory Kimlik Koruması (AAD P2 lisanslarına sahip kiracılar Için)
  > - Azure Information Protection
  > - Azure Gelişmiş tehdit koruması (Uyarılar)
  > - Azure Güvenlik Merkezi (Uyarılar)
  > - Microsoft Cloud App Security (yalnızca uyarılar)
  > - Microsoft Defender Gelişmiş tehdit koruması (Izleme Aracısı uyarıları)
  > - Office 365 (Exchange ve SharePoint günlükleri)
>
> Azure AD/Office365 denetim günlükleri ücretsiz değildir ve Premium bir katman olarak sunulmadığından bu verilerin alımı GB başına fiyatlandırılır. 
  

## <a name="global-prerequisites"></a>Genel Önkoşullar

- Etkin Azure aboneliği, yoksa, başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

- Log Analytics çalışma alanı. [Log Analytics çalışma alanı oluşturmayı](../log-analytics/log-analytics-quick-create-workspace.md)öğrenin. Log Analytics çalışma alanları hakkında daha fazla bilgi için bkz. [Azure Izleyici günlükleri dağıtımınızı tasarlama](../azure-monitor/platform/design-logs-deployment.md).

-  Azure Sentinel 'i etkinleştirmek için, Azure Sentinel çalışma alanının bulunduğu abonelik için katkıda bulunan izinlerinizin olması gerekir. 
- Azure Sentinel 'i kullanmak için, çalışma alanının ait olduğu kaynak grubunda katkıda bulunan ya da okuyucu izinlerinizin olması gerekir.
- Belirli veri kaynaklarına bağlanmak için ek izinler gerekebilir.
- Azure Sentinel, ücretli bir hizmettir. Fiyatlandırma bilgileri için bkz. [Azure Sentinel hakkında](https://go.microsoft.com/fwlink/?linkid=2104058).
 
## Azure Sentinel 'i etkinleştirme<a name="enable"></a>

1. Azure portal gidin.
2. Azure Sentinel 'in oluşturulduğu aboneliğin seçili olduğundan emin olun. 
3. Azure Sentinel 'i arayın. 
   ![arama](./media/quickstart-onboard/search-product.png)

1. **+ Ekle**' ye tıklayın.
1. Kullanmak istediğiniz çalışma alanını seçin veya yeni bir tane oluşturun. Azure Sentinel 'i birden fazla çalışma alanında çalıştırabilirsiniz, ancak veriler tek bir çalışma alanına yalıtılmış olur.

   ![search](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Azure Güvenlik Merkezi tarafından oluşturulan varsayılan çalışma alanları listede görünmez; Azure Sentinel 'i bunlara yükleyemezsiniz.
   > - Azure Sentinel, Çin, Almanya ve Azure Kamu bölgeleri hariç Log Analytics her bir [ga bölgesindeki](https://azure.microsoft.com/global-infrastructure/services/?products=monitor) çalışma alanlarında çalıştırılabilir. Azure Sentinel tarafından oluşturulan (olaylar, yer işaretleri ve uyarı kuralları gibi) veriler, bu çalışma alanlarından alınan bazı müşteri verilerini içerebilir) Batı Avrupa (Avrupa 'da bulunan çalışma alanları için) veya Doğu ABD (tüm ABD tabanlı çalışma alanları için de Avrupa dışındaki diğer bölgeler).

6. **Azure Sentinel Ekle**' ye tıklayın.
  

## <a name="connect-data-sources"></a>Veri kaynaklarını bağlama

Azure Sentinel, hizmete bağlanarak ve olayları ve günlükleri Azure Sentinel 'e ileterek hizmet ve uygulamalarla bağlantı kurar. Makineler ve sanal makineler için, günlükleri toplayan ve bunları Azure Sentinel 'e ileten Azure Sentinel Aracısı 'nı yükleyebilirsiniz. Güvenlik duvarları ve proxy 'ler için Azure Sentinel bir Linux Syslog sunucusu kullanır. Aracı üzerine yüklenir ve aracı günlük dosyalarını toplayıp Azure Sentinel 'e iletir. 
 
1. **Veri koleksiyonu**' na tıklayın.
2. Bağlandığınız her veri kaynağı için bir kutucuk vardır.<br>
Örneğin **Azure Active Directory**' ye tıklayın. Bu veri kaynağını bağladığınızda, Azure AD 'deki tüm günlükleri Azure Sentinel 'e akışla alırsınız. Ne tür Günlükler alınacağını seçin-oturum açma günlükleri ve/veya denetim günlükleri. <br>
Azure Sentinel, en altta, her bağlayıcı için hangi çalışma kitaplarını yükleyeceksiniz? böylece verileriniz genelinde ilginç Öngörüler elde edebilirsiniz. <br> Daha fazla bilgi için yükleme yönergelerini izleyin veya [ilgili bağlantı kılavuzuna bakın](connect-data-sources.md) . Veri bağlayıcıları hakkında daha fazla bilgi için bkz. [Microsoft hizmetlerini bağlama](connect-data-sources.md).

Veri kaynaklarınızı bağladıktan sonra Verileriniz Azure Sentinel 'e akışa başlar ve ile çalışmaya başlayabilirsiniz. Günlükleri [yerleşik panolarda](quickstart-get-visibility.md) görüntüleyebilir ve [verileri araştırmak](tutorial-investigate-cases.md)için Log Analytics içinde sorgular oluşturmaya başlayabilirsiniz.



## <a name="next-steps"></a>Sonraki adımlar
Bu belgede, veri kaynaklarını Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
- [Yaygın hata biçimi gereçlerden](connect-common-event-format.md) Azure Sentinel 'e veri akışı.
