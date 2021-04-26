---
title: Azure portal bir Log Analytics çalışma alanı oluşturun | Microsoft Docs
description: Azure portal bulut ve şirket içi ortamlarınızdaki yönetim çözümlerini ve veri toplamayı etkinleştirmek için Log Analytics çalışma alanı oluşturmayı öğrenin.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/18/2021
ms.openlocfilehash: 7d3ffd754959c59fb34f326473247d836871ea75
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106828"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Azure portalında Log Analytics çalışma alanı oluşturma
Azure portal kullanarak Log Analytics çalışma alanı oluşturmak için **Log Analytics çalışma alanları** menüsünü kullanın. Log Analytics çalışma alanı, Azure Izleyici günlük verileri için benzersiz bir ortamdır. Her çalışma alanının kendi veri deposu ve yapılandırması vardır ve veri kaynakları ve çözümleri verilerini belirli bir çalışma alanında depolamak üzere yapılandırılır. Aşağıdaki kaynaklardan veri toplamayı planlıyorsanız bir Log Analytics çalışma alanı gerekir:

* Aboneliğinizdeki Azure kaynakları
* System Center Operations Manager tarafından izlenen şirket içi bilgisayarlar
* Configuration Manager cihaz koleksiyonları 
* Azure depolama 'dan tanılama veya günlük verileri

Ortamınızdaki Azure VM 'Leri ve Windows veya Linux VM 'Leri gibi diğer kaynaklar için aşağıdaki konulara bakın:

*  [Azure sanal makinelerinden veri toplama](../vm/quick-collect-azurevm.md) 
*  [Karma Linux bilgisayarından veri topla](../vm/quick-collect-linux-computer.md)
*  [Karma Windows bilgisayarından veri topla](../vm/quick-collect-windows-computer.md)

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="sign-in-to-azure-portal"></a>Azure portalda oturum açın
[https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın. 

## <a name="create-a-workspace"></a>Çalışma alanı oluşturma
Azure portalında **Tüm hizmetler**’e tıklayın. Kaynak listesinde **Log Analytics** yazın. Yazmaya başladığınızda liste, girişinize göre filtrelenir. **Log Analytics çalışma alanlarını** seçin.

![Azure portalı](media/quick-create-workspace/azure-portal-01.png)
  
**Ekle**' ye tıklayın ve ardından aşağıdaki seçenekler için değerler girin:

   * Varsayılan seçili abonelik uygun değilse açılan listeden bağlanacak bir **Abonelik** seçin.
   * **Kaynak grubu** için, zaten kurulumu veya yeni bir kaynak grubu oluşturmayı seçin.  
   * Yeni **Log Analytics çalışma alanı** Için *defaultlaworkspace* gibi bir ad sağlayın. Bu ad her kaynak grubu için benzersiz olmalıdır.
   * Kullanılabilir bir **bölge** seçin.  Daha fazla bilgi için, [' de hangi bölgelerin kullanılabildiği Log Analytics](https://azure.microsoft.com/regions/services/) ve **bir ürün Için aramadan** Azure izleyici araması ' nı inceleyin.  


        ![Log Analytics kaynak dikey penceresi oluşturma](media/quick-create-workspace/create-workspace.png)  


Ayarları gözden geçirmek için **gözden geçir + oluştur** ' a tıklayın ve ardından çalışma alanını oluşturmak için **oluşturun** . Bu, yeterli miktarda veri toplamaya başlamadan önce bir değişiklik yapmayacak şekilde Kullandıkça Öde varsayılan fiyatlandırma katmanını seçer. Diğer fiyatlandırma katmanları hakkında daha fazla bilgi için bkz. [Log Analytics fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/log-analytics/).



## <a name="troubleshooting"></a>Sorun giderme
Son 14 gün içinde silinen bir çalışma alanı oluşturduğunuzda ve [geçici silme durumunda](../logs/delete-workspace.md#soft-delete-behavior)işlem, çalışma alanı yapılandırmanıza bağlı olarak farklı bir sonuca sahip olabilir:
1. Silinen çalışma alanında aynı çalışma alanı adı, kaynak grubu, abonelik ve bölge sağlarsanız, çalışma alanınız veri, yapılandırma ve bağlı aracılar dahil kurtarılacak.
2. Aynı çalışma alanı adını, ancak farklı kaynak grubunu, aboneliği veya bölgeyi kullanırsanız, *Bu çalışma alanı adının zaten kullanımda olduğunu bir hata alırsınız. Lütfen başka bir tane deneyin*. Geçici silme işlemini geçersiz kılmak ve çalışma alanınızı kalıcı olarak silmek ve aynı ada sahip yeni bir çalışma alanı oluşturmak için, önce çalışma alanını kurtarmak ve kalıcı silme gerçekleştirmek için şu adımları izleyin:
   - Çalışma alanınızı [kurtarma](../logs/delete-workspace.md#recover-workspace)
   - Çalışma alanınızı [kalıcı olarak silme](../logs/delete-workspace.md#permanent-workspace-delete)
   - Aynı çalışma alanı adını kullanarak yeni bir çalışma alanı oluştur

## <a name="next-steps"></a>Sonraki adımlar
Artık kullanılabilir bir çalışma alanınız olduğuna göre, izleme telemetri koleksiyonunu yapılandırabilir, bu verileri çözümlemek için günlük aramalarını çalıştırabilir ve ek veri ve analitik Öngörüler sağlamak için bir yönetim çözümü ekleyebilirsiniz. 

* Çalışma alanınızın sistem durumunu izlemek için bkz. [Azure izleyici 'de Log Analytics çalışma alanının sistem durumunu izleme](../logs/monitor-workspace.md) uyarı kuralları oluşturma. 
* Azure Tanılama veya Azure depolama ile Azure kaynaklarından veri toplamayı etkinleştirmek için, bkz. [Log Analytics Için Azure hizmet günlüklerini ve ölçümlerini toplayın](../essentials/resource-logs.md#send-to-log-analytics-workspace) .
