---
title: Azure Izleyici 'de izleme çözümlerini hedefleme | Microsoft Docs
description: İzleme çözümlerini hedefleme, izleme çözümlerini belirli bir aracı kümesiyle sınırlamanıza olanak tanır.  Bu makalede, bir kapsam yapılandırmasının nasıl oluşturulacağı ve bir çözüme nasıl uygulanacağı açıklanır.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/27/2017
ms.openlocfilehash: 04b47cb6079d9213c1a20425f62286f1b2aa778b
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555318"
---
# <a name="targeting-monitoring-solutions-in-azure-monitor-preview"></a>Azure Izleyici 'de izleme çözümlerini hedefleme (Önizleme)
Aboneliğinize bir izleme çözümü eklediğinizde, varsayılan olarak, Log Analytics çalışma alanınıza bağlı tüm Windows ve Linux aracılarına otomatik olarak dağıtılır.  Maliyetlerinizi yönetmek ve bir çözüm için toplanan veri miktarını belirli bir aracı kümesiyle sınırlayarak sınırlamak isteyebilirsiniz.  Bu makalede, çözümlerinize bir kapsam uygulamanıza olanak tanıyan bir özellik olan **çözüm hedeflemesi** 'nin nasıl kullanılacağı açıklanır.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="how-to-target-a-solution"></a>Bir çözümü hedefleme
Aşağıdaki bölümlerde açıklandığı gibi bir çözümü hedeflemek için üç adım vardır. 


### <a name="1-create-a-computer-group"></a>1. bir bilgisayar grubu oluşturun
Azure Izleyici 'de bir [bilgisayar grubu](../platform/computer-groups.md) oluşturarak kapsama eklemek istediğiniz bilgisayarları belirlersiniz.  Bilgisayar grubu, bir günlük sorgusuna dayalı olabilir veya Active Directory veya WSUS grupları gibi diğer kaynaklardan içeri aktarılabilir. [Aşağıda açıklandığı](#solutions-and-agents-that-cant-be-targeted)gibi, yalnızca Azure izleyici 'ye doğrudan bağlı olan bilgisayarlar kapsama dahil edilir.

Çalışma alanınızda bilgisayar grubu oluşturulduktan sonra, bir veya daha fazla çözümü uygulanabilecek bir kapsam yapılandırmasına dahil edersiniz.
 
 
### <a name="2-create-a-scope-configuration"></a>2. kapsam yapılandırması oluşturma
 **Kapsam yapılandırmasında** bir veya daha fazla bilgisayar grubu bulunur ve bir veya daha fazla çözüm için uygulanabilir. 
 
 Aşağıdaki işlemi kullanarak bir kapsam yapılandırması oluşturun.  

 1. Azure portal, **Log Analytics çalışma alanları** ' na gidin ve çalışma alanınızı seçin.
 2. Çalışma alanı **veri kaynakları** altındaki çalışma alanının özelliklerinde **kapsam yapılandırması**' nı seçin.
 3. Yeni bir kapsam yapılandırması oluşturmak için **Ekle** ' ye tıklayın.
 4. Kapsam yapılandırması için bir **ad** yazın.
 5. **Bilgisayar gruplarını Seç**' e tıklayın.
 6. Oluşturduğunuz bilgisayar grubunu ve isteğe bağlı olarak yapılandırmaya eklenecek diğer grupları seçin.  **Seç**'e tıklayın.  
 6. Kapsam yapılandırmasını oluşturmak için **Tamam** ' ı tıklatın. 


### <a name="3-apply-the-scope-configuration-to-a-solution"></a>3. kapsam yapılandırmasını bir çözüme uygulayın.
Kapsam yapılandırmasına sahip olduktan sonra, bir veya daha fazla çözüm için uygulayabilirsiniz.  Tek bir kapsam yapılandırması birden çok çözümle birlikte kullanılabileceği sürece, her çözümün yalnızca bir kapsam yapılandırması kullanabileceğini unutmayın.

Aşağıdaki işlemi kullanarak bir kapsam yapılandırması uygulayın.  

 1. Azure portal, **Log Analytics çalışma alanları** ' na gidin ve çalışma alanınızı seçin.
 2. Çalışma alanının özelliklerinde **çözümler**' i seçin.
 3. Kapsam yapmak istediğiniz çözüme tıklayın.
 4. **Çalışma alanı veri kaynakları** altındaki çözüm özellikleri ' nde **çözüm hedefleme**' yi seçin.  Seçenek kullanılamıyorsa, [Bu çözüm hedeflenemez](#solutions-and-agents-that-cant-be-targeted).
 5. **Kapsam yapılandırması Ekle**' ye tıklayın.  Bu çözüme uygulanan bir yapılandırma zaten varsa, bu seçenek kullanılamaz.  Başka bir yapılandırma eklemeden önce varolan yapılandırmayı kaldırmanız gerekir.
 6. Oluşturduğunuz kapsam yapılandırmasına tıklayın.
 7. **Başarılı**olduğunu gösterdiğinizden emin olmak Için yapılandırmanın **durumunu** izleyin.  Durum bir hata gösteriyorsa, yapılandırmanın sağındaki elipsi tıklatın ve değişiklik yapmak için **kapsam yapılandırmasını düzenle** ' yi seçin.

## <a name="solutions-and-agents-that-cant-be-targeted"></a>Hedeflenemez çözümler ve aracılar
Çözüm hedefleme ile kullanılamayan aracıların ve çözümlerin ölçütleri aşağıda verilmiştir.

- Çözüm hedefleme yalnızca aracılara dağıtan çözümler için geçerlidir.
- Çözüm hedefleme yalnızca Microsoft tarafından sunulan çözümler için geçerlidir.  [Kendi veya iş ortakları tarafından oluşturulan](solutions-creating.md)çözümlere uygulanmaz.
- Yalnızca Azure Izleyici 'ye doğrudan bağlanan aracıları filtreleyebilirsiniz.  Çözümler, bir kapsam yapılandırmasına dahil edilip edilmeksizin bağlı Operations Manager yönetim grubunun parçası olan aracılara otomatik olarak dağıtılır.

### <a name="exceptions"></a>Özel Durumlar
Çözüm hedefleme, belirtilen ölçütlere uygun olsa da aşağıdaki çözümlerle kullanılamaz.

- Aracı Durumu değerlendirmesi

## <a name="next-steps"></a>Sonraki adımlar
- [Çalışma alanınıza Azure Log Analytics izleme çözümleri ekleme](solutions.md)bölümünde ortamınızda yüklenebilecek çözümler dahil olmak üzere çözümleri izleme hakkında daha fazla bilgi edinin.
- [Azure izleyici günlük sorgularının bilgisayar gruplarında](../platform/computer-groups.md)bilgisayar grupları oluşturma hakkında daha fazla bilgi edinin.
