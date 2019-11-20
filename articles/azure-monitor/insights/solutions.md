---
title: Azure Izleyici 'de çözümleri izleme | Microsoft Docs
description: Azure Izleyici 'de izleme çözümleri, belirli bir sorun alanı etrafında ölçüm sağlayan bir mantık, görselleştirme ve veri alma kuralları koleksiyonudur.  Bu makale, izleme çözümlerini yükleme ve kullanma hakkında bilgi sağlar.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: b5304f2f9af3f3dac5a1259669cdd78548c2e832
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933735"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Azure Izleyici 'de çözümleri izleme
İzleme çözümleri, belirli bir uygulama veya hizmetin işlemine ek Öngörüler sağlamak için Azure 'daki hizmetlerden yararlanır. Bu makalede, Azure 'daki çözümleri izleme hakkında kısa bir genel bakış ve bunları kullanma ve yükleme hakkındaki ayrıntılar sunulmaktadır.

> [!NOTE]
> İzleme çözümleri daha önce yönetim çözümleri olarak adlandırılmıştı.

İzleme çözümleri genellikle günlük verilerini toplar ve toplanan verileri çözümlemek için sorgular ve görünümler sağlar. Uygulama veya hizmetle ilgili eylemler gerçekleştirmek için Azure Otomasyonu gibi diğer hizmetlerden de faydalanabilir.

Azure Izleyici 'ye, kullandığınız tüm uygulama ve hizmetler için izleme çözümleri ekleyebilirsiniz. Bunlar genellikle ücretsiz olarak kullanılabilir ancak kullanım ücretlerini çağırabilecek veri toplar. Microsoft tarafından sağlanan çözümlere ek olarak, iş ortakları ve müşteriler kendi ortamlarında kullanılmak üzere [yönetim çözümleri oluşturabilir](solutions-creating.md) veya topluluk aracılığıyla müşteriler tarafından kullanılabilir hale getirilebilir.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="use-monitoring-solutions"></a>İzleme çözümlerini kullanma
Çalışma alanında yüklü her bir çözümün kutucuğunu görüntülemek için Azure Izleyici 'de **genel bakış** sayfasını açın. 

1. Azure portalında oturum açın.
1. **Tüm hizmetleri** açın ve **izleyiciyi**bulun.
1. **Öngörüler** menüsünde, **daha fazla**' yı seçin.
1. Çalışma alanını veya kutucuklar için kullanılan zaman aralığını değiştirmek için ekranın üst kısmındaki açılan kutuları kullanın.
1. Bir çözüm için kutucuğa tıklayarak, toplanan verilerin daha ayrıntılı analizini içeren görünümünü açın.

![Genel Bakış](media/solutions/overview.png)

İzleme çözümleri birden çok Azure kaynağı türü içerebilir ve bir çözüme dahil olan tüm kaynakları diğer kaynaklar gibi görüntüleyebilirsiniz. Örneğin, çözüme dahil edilen herhangi bir günlük sorgusu, [sorgu Gezgini](../log-query/get-started-portal.md#load-queries) 'Ndeki **çözüm sorguları** altında listelenmiştir ve [günlük sorgularıyla](../log-query/log-query-overview.md)geçici analiz gerçekleştirirken bu sorguları kullanabilirsiniz.

## <a name="list-installed-monitoring-solutions"></a>Yüklü izleme çözümlerini Listele 
Aboneliğinizde yüklü izleme çözümlerini listelemek için aşağıdaki yordamı kullanın.

1. Azure portalında oturum açın.
1. **Tüm hizmetler** ' i açın ve **çözümleri**bulun.
4. Çalışma alanlarınızın tümünde yüklü olan çözümler listelenir. Çözümün adının ardından yüklendiği çalışma alanının adı gelir.
1. Abonelik veya kaynak grubuna göre filtrelemek için ekranın üst kısmındaki açılan kutuları kullanın.


![Tüm çözümleri Listele](media/solutions/list-solutions-all.png)

Bir çözümün adına tıklayarak Özet sayfasını açın. Bu sayfada çözüme dahil edilen görünümler görüntülenir ve çözümün kendisi ve çalışma alanı için farklı seçenekler sunulur. Çözümleri listelemek için Yukarıdaki yordamlardan birini kullanarak bir çözümün Özet sayfasını görüntüleyin ve sonra çözümün adına tıklayın.

![Çözüm özellikleri](media/solutions/solution-properties.png)



## <a name="install-a-monitoring-solution"></a>İzleme çözümü yüklemesi
Microsoft ve iş ortaklarından izleme çözümlerini [Azure Marketi](https://azuremarketplace.microsoft.com)' nden edinebilirsiniz. Aşağıdaki yordamı kullanarak, kullanılabilir çözümleri arayabilir ve bunları yükleyebilirsiniz. Bir çözüm yüklediğinizde, çözümün yükleneceği ve verilerinin toplanacağı bir [Log Analytics çalışma alanı](../platform/manage-access.md) seçmeniz gerekir.

1. [Aboneliğiniz için çözümler listesinden](#list-installed-monitoring-solutions) **Ekle**' ye tıklayın.
1. Bir çözüme gözatıp arayın. Ayrıca, [Bu arama bağlantısından](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions)çözümlere da gidebilirsiniz.
1. İstediğiniz izleme çözümünü bulun ve açıklamasını okuyun.
1. Yükleme işlemini başlatmak için **Oluştur** ' a tıklayın.
1. Yükleme işlemi başladığında Log Analytics çalışma alanını belirtmeniz ve çözüm için gerekli tüm yapılandırmaları sağlamanız istenir.

![Çözüm yüklemesi](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Topluluktan bir çözüm yükler
Topluluk üyeleri, yönetim çözümlerini Azure hızlı başlangıç şablonlarına gönderebilir. Bu çözümleri doğrudan yükleyebilir veya daha sonra yüklenmek üzere şablonları indirebilirsiniz.

1. Çalışma alanını ve hesabı bağlamak için [Log Analytics çalışma alanı ve Otomasyon hesabı](#log-analytics-workspace-and-automation-account) ' nda açıklanan süreci izleyin.
2. [Azure hızlı başlangıç şablonlarına](https://azure.microsoft.com/documentation/templates/)gidin. 
3. İlgilendiğiniz bir çözüm arayın.
4. Ayrıntılarını görüntülemek için sonuçlardan çözümü seçin.
5. **Azure 'A dağıt** düğmesine tıklayın.
6. Çözümdeki parametrelerin değerlerinin yanı sıra kaynak grubu ve konum gibi bilgileri sağlamanız istenir.
7. Çözümü yüklemek için **satın al** ' a tıklayın.


## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics çalışma alanı ve Otomasyon hesabı
Tüm izleme çözümleri, çözüm tarafından toplanan verileri depolamak ve günlük aramalarını ve görünümlerini barındırmak için bir [Log Analytics çalışma alanı](../platform/manage-access.md) gerektirir. Bazı çözümler ayrıca runbook 'ları ve ilgili kaynakları içeren bir [Otomasyon hesabı](../../automation/automation-security-overview.md#automation-account-overview) gerektirir. Çalışma alanı ve hesap aşağıdaki gereksinimlere uymalıdır.

* Her bir çözümün yüklemesi yalnızca bir Log Analytics çalışma alanı ve bir Otomasyon hesabı kullanabilir. Çözümü birden çok çalışma alanına ayrı olarak yükleyebilirsiniz.
* Bir çözüm bir Otomasyon hesabı gerektiriyorsa, Log Analytics çalışma alanı ve Otomasyon hesabı bir diğeri ile bağlantılı olmalıdır. Log Analytics çalışma alanı yalnızca bir Otomasyon hesabına bağlanabilir ve bir Otomasyon hesabı yalnızca bir Log Analytics çalışma alanına bağlanabilir.
* Bağlantı sağlamak için, Log Analytics çalışma alanı ve Otomasyon hesabı aynı kaynak grubunda ve bölgede olmalıdır. Özel durum, Doğu ABD 2 Doğu ABD Region ve Automation hesabındaki bir çalışma alanıdır.

### <a name="create-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Log Analytics çalışma alanı ve Otomasyon hesabı arasında bağlantı oluşturma
Log Analytics çalışma alanını ve otomasyon hesabını nasıl belirtirsiniz çözümünüz için yükleme yöntemine bağlıdır.

* Azure Marketi aracılığıyla bir çözüm yüklediğinizde, sizden bir çalışma alanı ve Otomasyon hesabı istenir. Bunlar arasındaki bağlantı, henüz bağlanmadıysa oluşturulur.
* Azure Marketi dışındaki çözümler için, çözümü yüklemeden önce Log Analytics çalışma alanını ve otomasyon hesabını bağlamanız gerekir. Bunu, Azure Marketi 'nde herhangi bir çözümü seçerek ve Log Analytics çalışma alanı ve Otomasyon hesabı ' nı seçerek yapabilirsiniz. Log Analytics çalışma alanı ve Otomasyon hesabı seçilir oluşturmaz bağlantı oluşturulduğundan, çözümü gerçekten yüklemek zorunda değilsiniz. Bağlantı oluşturulduktan sonra, bu Log Analytics çalışma alanını ve otomasyon hesabını herhangi bir çözüm için kullanabilirsiniz.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Log Analytics çalışma alanı ve Otomasyon hesabı arasındaki bağlantıyı doğrulama
Aşağıdaki yordamı kullanarak bir Log Analytics çalışma alanı ve Otomasyon hesabı arasındaki bağlantıyı doğrulayabilirsiniz.

1. Azure portal Otomasyon hesabını seçin.
1. Menünün **Ilgili kaynaklar** bölümüne gidin.
1. **Çalışma alanı** ayarı etkinse, bu hesap bir Log Analytics çalışma alanına bağlanır. Çalışma alanının ayrıntılarını görüntülemek için **çalışma alanı** ' na tıklayabilirsiniz.

## <a name="remove-a-monitoring-solution"></a>İzleme çözümünü kaldırma
Yüklü bir çözümü kaldırmak için, [yüklü çözümler listesinde](#list-installed-monitoring-solutions)bulun. Çözümün adına tıklayarak Özet sayfasını açın ve ardından **Sil**' e tıklayın.


## <a name="next-steps"></a>Sonraki adımlar
* [Microsoft 'un izleme çözümlerinin bir listesini](solutions-inventory.md)alın.
* İzleme çözümleri tarafından toplanan verileri analiz etmek için [sorgular oluşturmayı](../log-query/log-query-overview.md) öğrenin.

