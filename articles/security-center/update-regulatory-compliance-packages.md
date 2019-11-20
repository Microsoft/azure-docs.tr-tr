---
title: Azure Güvenlik Merkezi mevzuat uyumluluk panonuzda dinamik mevzuata uyumluluğu izlemeye güncelleştirme | Microsoft Docs
description: Yasal uyumluluk paketlerinizi güncelleştirme (Önizleme)
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: f76b5443d6c1e3fd2cebf87cba39ba0a6bbdeaee
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521793"
---
# <a name="update-to-dynamic-compliance-packages-in-your-regulatory-compliance-dashboard-preview"></a>Yasal uyumluluk panonuzda dinamik uyumluluk paketlerine güncelleştirme (Önizleme)

Azure Güvenlik Merkezi, kaynaklarınızın yapılandırmalarını endüstri standartları, yönetmelikler ve kıyaslamalar ile sürekli olarak karşılaştırır. **Mevzuat uyumluluk panosu** , uyumluluk denetimi ve gereksinimleri nasıl karşıladığınızı temel alarak uyumlulukla ilgili öngörüler sağlar.

Uyumluluk duruşunuzu izleyebilmeniz için bir standart [Azure CIS 1.1.0](https://www.cisecurity.org/benchmark/azure/) (daha resmi olarak, "CIS Microsoft Azure Founi kıyaslama sürümü 1.1.0"). 

Uyumluluk panonuzda başlangıçta görünen Azure CIS temsili, güvenlik merkezi 'nde bulunan statik bir kural kümesine bağlıdır.

**Dinamik uyumluluk paketleri (Önizleme)** özelliğiyle, güvenlik merkezi zaman içinde sektör standartlarının kapsamını otomatik olarak geliştirir. Uyumluluk paketleri temelde Azure Ilkesinde tanımlanan girişimlerdir. Bunlar seçili kapsamınızda (abonelik, yönetim grubu vb.) atanabilir. Panonuzda değerlendirmelere eşlenen uyumluluk verilerini görmek için, Güvenlik Ilkesi içinden yönetim grubunuza veya aboneliğinize bir uyumluluk paketi ekleyin. Uyumluluk paketi eklendiğinde, yasal uyumluluk girişimi seçili kapsamınızda etkin bir şekilde atanır. Bu şekilde, yeni yayınlanan mevzuata girişimlerini panonuzda uyumluluk standartları olarak izleyebilirsiniz. Microsoft, girişim için yeni içerik yayımlarsa (Standart içindeki daha fazla denetim ile eşlenen yeni ilkeler), panonuzda ek içerik otomatik olarak görünür.

Azure CIS kıyaslaması için dinamik uyumluluk paketi olan **Azure CIS 1.1.0 (New)** , özgün *statik* sürümünde şu şekilde geliştirilir:

* Daha fazla ilke dahil
* Eklendikçe yeni kapsama göre otomatik olarak güncelleştiriliyor 

Aşağıda açıklandığı gibi yeni dinamik pakete güncelleştirin.

## <a name="adding-a-dynamic-compliance-package"></a>Dinamik uyumluluk paketi ekleme

Aşağıdaki adımlarda, Azure CIS kıyaslama v 1.1.0 ile uyumluluğunuzu izlemeye yönelik dinamik paketin nasıl ekleneceği açıklanmaktadır.   

### <a name="update-to-the-azure-cis-110-new-dynamic-compliance-package"></a>Azure CIS 1.1.0 (yeni) dinamik uyumluluk paketine güncelleştirme 

1. **Güvenlik ilkesi** sayfasını açın. Bu sayfa yönetim gruplarının, aboneliklerin, çalışma alanlarının ve yönetim grubu yapınızın sayısını gösterir.

1. Mevzuat uyumluluk duruşunu yönetmek istediğiniz aboneliği veya yönetim grubunu seçin. Tüm iç içe geçmiş kaynaklar için uyumluluk verilerinin toplanması ve izlenmesi için standart için geçerli olan en yüksek kapsamı seçmenizi öneririz. 

1. Sektör & mevzuat standartları (Önizleme) bölümünde, Azure CIS 1.1.0 'in yeni içerik için güncelleştirilemeyebilir. **Şimdi Güncelleştir**'e tıklayın. 

1. İsteğe bağlı olarak, **mevzuat uyumluluk standartları Ekle** sayfasını açmak için **başka standartlar Ekle** ' ye tıklayın. Buradan, **NıST SP 800-53 R4**, **SWIFT CSP cscf-V2020**, **UKO ve UK NHS**ve **Kanada Pbmm**gibi diğer uyumluluk standartları için **Azure CIS 1.1.0 (yeni)** ve dinamik paketleri için el ile arama yapabilirsiniz.
    
    ![Azure Güvenlik Merkezi 'nin mevzuat uyumluluk panosuna mevzuat paketleri ekleme](./media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-additional-standards.png)


1. Güvenlik Merkezi 'nin kenar çubuğundan, mevzuat uyumluluk panosunu açmak için **mevzuata uyumluluğu** ' nu seçin. 
    * Azure CIS 1.1.0 (yeni) artık sektör & mevzuat standartları listenizde görünür. 
    * Azure CIS 1.1.0 uyumluluğun orijinal *statik* görünümü de bunun yanında kalır. Gelecekte otomatik olarak kaldırılabilir.

    > [!NOTE]
    > Yeni eklenen bir standart, uyumluluk panosu 'nda görünmesi birkaç saat sürebilir.


    [Eski ve yeni Azure CIS 'yi gösteren yasal uyumluluk panosu ![](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png)](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png#lightbox)


## <a name="next-steps"></a>Sonraki adımlar

Bu makalede şunları öğrendiniz:

* Yasal uyumluluk panonuzda gösterilen standartları yeni *dinamik* paketlere **yükseltme**
* Ek standartlarla uyumluluğunuzu izlemek için **uyumluluk paketleri ekleme** . 

Diğer ilgili malzemeler için aşağıdaki makalelere bakın: 

- [Güvenlik Merkezi mevzuata uyumluluğu panosu](security-center-compliance-dashboard.md)
- [Güvenlik ilkeleriyle çalışma](tutorial-security-policy.md)
- [Azure Güvenlik Merkezi 'nde güvenlik önerilerini yönetme](security-center-recommendations.md) -Azure kaynaklarınızın korunmasına yardımcı olmak Için Azure Güvenlik Merkezi 'nde önerilerin nasıl kullanılacağını öğrenin.
- [Azure Güvenlik Merkezi SSS](security-center-faq.md) -Güvenlik Merkezi 'ni kullanma hakkında sık sorulan soruların yanıtlarını alın.