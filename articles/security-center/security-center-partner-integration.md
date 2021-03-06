---
title: Azure Güvenlik Merkezi'ndeki tümleşik güvenlik çözümleri | Microsoft Docs
description: Azure Güvenlik Merkezi'nin, Azure kaynaklarınızın genel güvenliğini geliştirmek amacıyla iş ortaklarıyla nasıl tümleştirildiğini öğrenin.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: memildin
ms.openlocfilehash: ff23a1fa4b631fc10163f22d94ccdbd8cbe657c2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102099259"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Azure Güvenlik Merkezi'ndeki tümleşik güvenlik çözümleri
Bu belge Azure Güvenlik Merkezi'ne bağlanmış olan güvenlik çözümlerini yönetmenize ve yenilerini eklemenize yardımcı olur.

## <a name="integrated-azure-security-solutions"></a>Tümleşik Azure güvenlik çözümleri
Güvenlik Merkezi, Azure'daki tümleşik güvenlik çözümlerini etkinleştirmeyi kolaylaştırır. Faydaları şunlardır:

- **Basitleştirilmiş dağıtım**: Güvenlik Merkezi, tümleşik iş ortağı çözümlerinin kolay bir şekilde sağlanmasını mümkün kılar. Güvenlik Merkezi, kötü amaçlı yazılımdan koruma ve güvenlik açığı değerlendirmesi gibi çözümler için sanal makinelerinizde aracıyı temin edebilir. Güvenlik Duvarı gereçlerinde, Güvenlik Merkezi için gereken ağ yapılandırmasının büyük bir bölümü olabilir.
- **Tümleşik algılamalar**: İş ortağı çözümlerinden gelen güvenlik olayları otomatik olarak toplanır, birleştirilir ve Güvenlik Merkezi uyarıları ve olaylarının bir parçası olarak görüntülenir. Gelişmiş tehdit algılama özellikleri sağlanması amacıyla, bu olaylar diğer kaynaklardan alınan algılamalarla da birleştirilir.
- **Birleşik sistem durumu izleme ve yönetim**: Müşteriler tüm iş ortağı çözümlerini bir bakışta izlemek için tümleşik sistem durumu olaylarını kullanabilir. İş ortağı çözümü kullanarak gelişmiş kuruluma kolayca erişim olanağıyla temel yapılandırma seçeneği sunulur.

Şu anda tümleşik güvenlik çözümleri [Qualys](https://www.qualys.com/public-cloud/#azure) ve [Rapid7](https://www.rapid7.com/products/insightvm/) tarafından güvenlik açığı değerlendirmesi ve [Azure Application Gateway üzerinde Web uygulaması güvenlik duvarı Microsoft Azure](../web-application-firewall/ag/ag-overview.md)vardır.

> [!NOTE]
> Güvenlik Merkezi, çoğu güvenlik satıcısı, gereçlerinde çalışan dış aracıları yasakladığından, Log Analytics aracısını iş ortağı sanal gereçlerine yüklemez.

Azure Defender müşterilerinin kullanabildiği yerleşik bir tarayıcı dahil olmak üzere Qualys 'ten güvenlik açığı tarama araçlarının tümleştirilmesi hakkında daha fazla bilgi edinmek için bkz. [Azure sanal makineleriniz için güvenlik açığı değerlendirmeleri](deploy-vulnerability-assessment-vm.md).

Güvenlik Merkezi, için güvenlik açığı analizini de sunmaktadır:

* SQL veritabanları-bkz [. güvenlik açığı değerlendirme panosundaki güvenlik açığı değerlendirmesi raporlarını inceleyin](defender-for-sql-on-machines-vulnerability-assessment.md#explore-vulnerability-assessment-reports)
* Görüntüleri Azure Container Registry-bkz. [Azure Defender 'ı kapsayıcı kayıt defterleri Için kullanma güvenlik açıkları için görüntülerinizi tarayın](defender-for-container-registries-usage.md)

## <a name="how-security-solutions-are-integrated"></a>Güvenlik çözümlerinin tümleştirilme şekli
Güvenlik Merkezinden dağıtılan Azure güvenlik çözümleri otomatik olarak bağlanır. Ayrıca, şirket içinde veya diğer bulutlarda çalışan bilgisayarlar da dahil olmak üzere diğer güvenlik veri kaynaklarını da bağlayabilirsiniz.

[![İş ortağı çözümlerini tümleştirme](./media/security-center-partner-integration/security-solutions-page.png)](./media/security-center-partner-integration/security-solutions-page.png#lightbox)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Tümleşik Azure güvenlik çözümlerini ve diğer veri kaynaklarını yönetme

1. [Azure Portal](https://azure.microsoft.com/features/azure-portal/), **Güvenlik Merkezi**'ni açın.

1. Güvenlik Merkezi menüsünden **güvenlik çözümleri**' ni seçin.

**Güvenlik çözümleri** sayfasında, tümleşik Azure güvenlik çözümlerinin durumunu görebilir ve temel yönetim görevlerini çalıştırabilirsiniz.

### <a name="connected-solutions"></a>Bağlantılı çözümler

**Bağlı çözümler** bölümü, güvenlik merkezi 'ne bağlı olan güvenlik çözümlerini içerir. Ayrıca, her çözümün sistem durumunu gösterir.  

![Bağlantılı çözümler](./media/security-center-partner-integration/connected-solutions.png)

İş ortağı çözümünün durumu şu olabilir:

* **Sağlıklı** (yeşil)-sistem durumu sorunu yok.
* **Sağlıksız** (kırmızı)-hemen ilgilenilmesi gereken bir sistem durumu sorunu var.
* **Raporlama durduruldu** (turuncu)-çözüm, sistem durumunu raporlamayı durdurdu.
* Bildirilmedi (gri)-çözüm henüz bir şey **bildirmedi** ve sistem durumu verisi yok. Bir çözümün durumu, yakın zamanda bağlandıysa ve hala dağıtılarak bildirilmeyebilir.

> [!NOTE]
> Sağlık durumu verileri yoksa, Güvenlik Merkezi, çözümün raporlama yapılıp yapılmayacağını göstermek için alınan son olayın tarih ve saatini gösterir. Bir sistem durumu verisi yoksa ve son 14 gün içinde hiçbir uyarı alınmıyorsa, Güvenlik Merkezi, çözümün sağlıksız olduğunu veya bildirilmediğini belirtir.
>
>

Ek bilgi ve seçenekler için **Görünüm** ' ü seçin:

   - **Çözüm konsolu** -bu çözüm için yönetim deneyimini açar.
   - **VM bağla** -uygulamaları bağla sayfasını açar. Burada, kaynakları iş ortağı çözümüne bağlayabilirsiniz.
   - **Çözümü sil**
   - **Yapılandır**

   ![İş ortağı çözümü ayrıntısı](./media/security-center-partner-integration/partner-solutions-detail.png)


### <a name="discovered-solutions"></a>Bulunan çözümler

Güvenlik Merkezi, Azure 'da çalışan ancak güvenlik merkezi 'ne bağlı olmayan güvenlik çözümlerini otomatik olarak bulur ve **bulunan çözümler** bölümünde çözümleri görüntüler. Bu çözümler [Azure AD kimlik koruması](../active-directory/identity-protection/overview-identity-protection.md)ve iş ortağı çözümleri gibi Azure çözümlerini içerir.

> [!NOTE]
> Bulunan çözümler özelliği için abonelik düzeyinde **Azure Defender 'ı** etkinleştirin. Hızlı Başlangıç bölümünde daha fazla bilgi edinin [: Azure Defender 'ı etkinleştirin](enable-azure-defender.md).

Güvenlik Merkezi ile tümleştirilecek ve güvenlik uyarıları hakkında bildirim almak için bir çözüm altında **Bağlan** ' ı seçin.

### <a name="add-data-sources"></a>Veri kaynağı ekleme

**Veri kaynakları ekleyin** bölümünde bağlanabilecek diğer veri kaynakları bulunur. Bu kaynaklardan veri ekleme talimatları için **EKLE**'ye tıklayın.

![Veri kaynakları](./media/security-center-partner-integration/add-data-sources.png)



## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Güvenlik Merkezi'nde iş ortağı çözümlerinin nasıl tümleştirileceğini öğrendiniz. Azure Sentinel veya başka bir SıEM ile tümleştirme ayarlamayı öğrenmek için bkz. [Güvenlik Merkezi verilerini sürekli dışa aktarma](continuous-export.md).