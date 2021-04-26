---
title: Azure Advisor’ı kullanmaya başlama
description: Azure Advisor 'ı kullanmaya başlayın.
ms.topic: article
ms.date: 02/01/2019
ms.openlocfilehash: e91049077502a6c0eedf0a4f979c073690c214da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85117871"
---
# <a name="get-started-with-azure-advisor"></a>Azure Advisor’ı kullanmaya başlama

Azure portal aracılığıyla Advisor 'a erişmeyi öğrenin, öneriler alın ve öneriler uygulayın.

> [!NOTE]
> Yeni oluşturulan kaynakları bulmak için Azure Advisor arka planda otomatik olarak çalışır. Bu kaynaklarla ilgili öneriler sağlamak 24 saate kadar sürebilir.

## <a name="get-recommendations"></a>Önerileri al

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Sol bölmede **danışman**' ye tıklayın.  Sol bölmede Advisor ' ı görmüyorsanız, **tüm hizmetler**' e tıklayın.  Hizmet menü bölmesinde, **izleme ve yönetim** altında **danışman**' ye tıklayın. Danışman panosu görüntülenir.

   ![Azure portal kullanarak Azure Advisor 'a erişme](./media/advisor-get-started/advisor-portal-menu.png) 

1. Danışman panosu, seçilen tüm abonelikler için önerilerinizin özetini görüntüler.  Abonelik filtresi açılan listesini kullanarak önerilerin görüntülenmesini istediğiniz abonelikleri seçebilirsiniz.

1. Belirli bir kategoriye ilişkin öneriler almak için, sekmelerden birine tıklayın: **güvenilirlik**, **güvenlik**, **performans** veya **Maliyet**. 

   ![Azure Advisor panosu](./media/advisor-overview/advisor-dashboard.png)

## <a name="get-recommendation-details-and-implement-a-solution"></a>Öneri ayrıntılarını alın ve bir çözüm uygulayın

Danışman 'de öneri eylemleri ve etkilenen kaynaklar gibi ek ayrıntıları görüntülemek ve çözümü önerisine uygulamak için bir öneri seçebilirsiniz.  

1. [Azure Portal](https://portal.azure.com)oturum açın ve [danışman](https://aka.ms/azureadvisordashboard)' yi açın.

1. Bu kategori içindeki önerilerin listesini görüntülemek için bir öneri kategorisi seçin veya tüm önerilerinizi görüntülemek için **Tümü** sekmesini seçin.

1. Ayrıntılı olarak gözden geçirmek istediğiniz öneriye tıklayın.

1. Öneri ve önerinin uygulandığı kaynaklar hakkındaki bilgileri gözden geçirin.

1. Öneriyi uygulamak için **Önerilen eyleme** tıklayın.

## <a name="filter-recommendations"></a>Filtre önerileri

Sizin için en önemli olan ayrıntıya gitmek üzere önerilere filtre uygulayabilirsiniz.  Abonelik, kaynak türü veya öneri durumuna göre filtreleyebilirsiniz.  

1. [Azure Portal](https://portal.azure.com)oturum açın ve [danışman](https://aka.ms/azureadvisordashboard)' yi açın.

1. Danışman panosundaki açılan listeleri aboneliğe, kaynak türüne veya öneri durumuna göre filtrelemek için kullanın.

    ![Danışman arama-filtre ölçütü](./media/advisor-get-started/advisor-filters.png)

## <a name="postpone-or-dismiss-recommendations"></a>Önerileri erteleyin veya kapatın

1. [Azure Portal](https://portal.azure.com)oturum açın ve [danışman](https://aka.ms/azureadvisordashboard)' yi açın.

1. Ertelemek veya kapatmak istediğiniz öneriye gidin.

1. Öneriye tıklayın.

1. **Ertele**' ye tıklayın. 

1. Bir erteleme dönemi belirtin veya öneriyi yok **saymayacak** şekilde seçin.

## <a name="exclude-subscriptions-or-resource-groups"></a>Abonelikleri veya kaynak gruplarını hariç tut

' Test ' kaynakları gibi danışman önerilerini almak istemediğiniz kaynak gruplarınız veya abonelikleriniz olabilir.  Advisor 'ı yalnızca belirli abonelikler ve kaynak grupları için öneriler oluşturacak şekilde yapılandırabilirsiniz.

> [!NOTE]
> Danışman 'den bir abonelik veya kaynak grubu dahil etmek veya dışlamak için bir abonelik sahibi olmanız gerekir.  Bir abonelik veya kaynak grubu için gerekli izinlere sahip değilseniz, Kullanıcı arabiriminde dahil etme veya dışlama seçeneği devre dışı bırakılır.

1. [Azure Portal](https://portal.azure.com)oturum açın ve [danışman](https://aka.ms/azureadvisordashboard)' yi açın.

1. Eylem çubuğunda **Yapılandır** ' a tıklayın.

1. Danışman önerilerini almak istemediğiniz aboneliklerin veya kaynak gruplarının işaretini kaldırın.

    ![Advisor kaynakları yapılandırma örneği](./media/advisor-get-started/advisor-configure-resources.png)

1. **Uygula** düğmesine tıklayın.

## <a name="configure-low-usage-vm-recommendation"></a>Düşük kullanım VM önerisi yapılandırma

Bu yordam, düşük kullanım sanal makinesi önerisi için Ortalama CPU kullanım kuralını yapılandırır.

Danışman, sanal makine kullanımınızı 7 gün boyunca izler ve düşük kullanım sanal makinelerini belirler. CPU kullanımı %5 veya daha az olursa ve ağ kullanımı %2 ' den küçük veya geçerli iş yükünün daha küçük bir sanal makine boyutuyla ele alınabiliyorsa, sanal makineler düşük kullanım olarak değerlendirilir.

Düşük kullanım sanal makinelerini tanımlamaya daha Agresif olmak istiyorsanız, ortalama CPU kullanımı kuralını abonelik başına temelinde ayarlayabilirsiniz.  CPU kullanım kuralı %5, %10, %15 veya %20 olarak ayarlanabilir.

> [!NOTE]
> Düşük kullanım sanal makinelerini tanımlamaya yönelik ortalama CPU kullanım kuralını ayarlamak için bir abonelik *sahibi* olmanız gerekir.  Bir abonelik veya kaynak grubu için gerekli izinlere sahip değilseniz, Kullanıcı arabiriminde dahil etme veya dışlama seçeneği devre dışı bırakılır. 

1. [Azure Portal](https://portal.azure.com)oturum açın ve [danışman](https://aka.ms/azureadvisordashboard)' yi açın.

1. Eylem çubuğunda **Yapılandır** ' a tıklayın.

1. **Kurallar** sekmesine tıklayın.

1. Ortalama CPU kullanım kuralını ayarlamak istediğiniz abonelikleri seçin ve ardından **Düzenle**' ye tıklayın.

1. İstenen ortalama CPU kullanımı değerini seçin ve **Uygula**' ya tıklayın.

1. Mevcut önerilerinizi yeni ortalama CPU kullanımı kuralını kullanacak şekilde güncelleştirmek için **öneriyi Yenile** ' ye tıklayın. 

   ![Advisor öneri kurallarını yapılandırma örneği](./media/advisor-get-started/advisor-configure-rules.png)

## <a name="download-recommendations"></a>Yükleme önerileri

Danışman, önerilerinizi bir Özet indirmenizi sağlar.  Önerilerinizi bir PDF dosyası veya CSV dosyası olarak indirebilirsiniz.  Önerilerinizi indirmek, iş arkadaşlarınızla kolayca paylaşmanıza veya öneri verilerinin üzerine kendi analizinizi gerçekleştirmenize olanak sağlar.

1. [Azure Portal](https://portal.azure.com)oturum açın ve [danışman](https://aka.ms/azureadvisordashboard)' yi açın.

1. Eylem çubuğunda **CSV olarak indir** veya **PDF olarak indir** ' e tıklayın.

İndir seçeneği, danışman panosuna uyguladığınız tüm filtreleri uyar.  Belirli bir öneri kategorisi veya önerisi görüntülenirken indir seçeneğini belirlerseniz, indirilen Özet yalnızca ilgili kategori veya öneriyle ilgili bilgileri içerir. 

## <a name="next-steps"></a>Sonraki adımlar

Advisor hakkında daha fazla bilgi edinmek için bkz.:

- [Azure Danışmanı’na Giriş](advisor-overview.md)
- [Advisor güvenilirlik önerileri](advisor-high-availability-recommendations.md)
- [Danışman güvenlik önerileri](advisor-security-recommendations.md)
- [Advisor performans önerileri](advisor-performance-recommendations.md)
- [Danışman maliyet önerileri](advisor-cost-recommendations.md)
- [Danışman Işletimsel üstün öneriler](advisor-operational-excellence-recommendations.md)
