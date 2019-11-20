---
title: Azure Log Analytics çalışma alanını silme ve kurtarma | Microsoft Docs
description: Kişisel abonelikte bir tane oluşturduysanız veya çalışma alanı modelinizi yeniden yapılandırmak için Log Analytics çalışma alanınızı silmeyi öğrenin.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 10/28/2019
ms.openlocfilehash: 709d63b2c764049a698bc538d9ec451b4e75feaa
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044236"
---
# <a name="delete-and-restore-azure-log-analytics-workspace"></a>Azure Log Analytics çalışma alanını silme ve geri yükleme

Bu makalede, Azure Log Analytics çalışma alanı geçici silme kavramı ve silinen çalışma alanının nasıl kurtarılacağı açıklanmaktadır. 

## <a name="considerations-when-deleting-a-workspace"></a>Çalışma alanı silinirken dikkat edilecek noktalar

Bir Log Analytics çalışma alanını sildiğinizde, silme işleminin yanlışlıkla mi yoksa bilerek mi olduğunu, veri ve bağlı aracılarını 14 gün içinde içeren çalışma alanının kurtarılmasına izin veren bir geçici silme işlemi yapılır. Geçici silme süresinden sonra, çalışma alanı ve verileri kurtarılamaz ve 30 gün içinde kalıcı olarak silinmek üzere sıraya alınır.

Hizmet işleminizi olumsuz yönde etkileyebilecek önemli veriler ve yapılandırma olabileceğinden, bir çalışma alanını sildiğinizde dikkatli olmak istersiniz. Şu gibi Log Analytics verilerini depolayan aracıları, çözümleri ve diğer Azure hizmetlerini ve kaynakları inceleyin:

* Yönetim çözümleri
* Azure Otomasyonu
* Windows ve Linux sanal makinelerinde çalışan aracılar
* Ortamınızdaki Windows ve Linux bilgisayarları üzerinde çalışan aracılar
* System Center Operations Manager

Geçici silme işlemi çalışma alanı kaynağını siler ve ilişkili kullanıcıların izni bozulur. Kullanıcılar başka çalışma alanlarıyla ilişkiliyse, bu diğer çalışma alanlarıyla Log Analytics kullanmaya devam edebilirler.

## <a name="soft-delete-behavior"></a>Geçici silme davranışı

Çalışma alanı silme işlemi, çalışma alanı Kaynak Yöneticisi kaynağını kaldırır, ancak yapılandırma ve veriler 14 gün boyunca tutulur ve çalışma alanının silindiği görünümü verir. Çalışma alanına rapor verecek şekilde yapılandırılan tüm aracılar ve System Center Operations Manager yönetim grupları, geçici silme dönemi boyunca yalnız bırakılmış durumda kalır. Bu hizmet, silinen çalışma alanının verileri ve bağlı kaynakları dahil olmak üzere, silme işlemini geri alarak kurtarmak için bir mekanizma sağlar.

> [!NOTE] 
> Azure Otomasyonu hesabınız gibi yüklü çözümler ve bağlı hizmetler, silme sırasında çalışma alanından kalıcı olarak kaldırılır ve kurtarılamaz. Çalışma alanını önceden yapılandırılmış durumuna getirmek için kurtarma işleminden sonra bunların yeniden yapılandırılması gerekir.

Bir çalışma alanını [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0), [REST API](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete)veya [Azure Portal](https://portal.azure.com)kullanarak silebilirsiniz.

### <a name="delete-workspace-in-azure-portal"></a>Azure portal çalışma alanını sil

1. Oturum açmak için [Azure Portal](https://portal.azure.com)gidin. 
2. Azure portalda **Tüm hizmetler**’i seçin. Kaynak listesinde **Log Analytics** yazın. Yazmaya başladığınızda liste, girişinize göre filtrelenir. **Log Analytics çalışma alanlarını**seçin.
3. Log Analytics çalışma alanları listesinde bir çalışma alanı seçin ve orta bölmenin en üstündeki **Sil** ' e tıklayın.
   ![seçim alanı Özellikler bölmesinden](media/delete-workspace/log-analytics-delete-workspace.png)
4. Çalışma alanını silme işlemini onaylamanızı isteyen onay iletisi penceresi göründüğünde **Evet**' e tıklayın.
   ![çalışma alanının silinmesini onaylayın](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

## <a name="recover-workspace"></a>Çalışma alanını kurtar

Çalışma alanının geçici silme işleminden önce ilişkilendirildiği abonelik ve kaynak grubu için katkıda bulunan izinleriniz varsa, verileri, yapılandırması ve bağlı aracıları da dahil olmak üzere geçici silme döneminde kurtarabilirsiniz. Geçici silme süresinden sonra, çalışma alanı kurtarılamaz ve kalıcı silme için atanmaz. Silinen çalışma alanlarının adları, geçici silme dönemi sırasında korunur ve yeni bir çalışma alanı oluşturulmaya çalışılırken kullanılamaz.  

Aşağıdaki özellikler silinen çalışma alanı ayrıntılarıyla doldurulduğu sürece, bir çalışma alanını yeniden oluşturarak kurtarabilirsiniz: [PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/New-AzOperationalInsightsWorkspace) veya [REST API]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate) .

* Abonelik Kimliği
* Kaynak grubu adı
* Çalışma alanı adı
* Bölge

Çalışma alanı ve tüm verileri kurtarma işleminden sonra geri getirilir. Çözüm ve bağlı hizmetler silindiği zaman çalışma alanından kalıcı olarak kaldırılmıştır ve çalışma alanını daha önce yapılandırılmış durumuna getirmek için yeniden yapılandırılması gerekir. İlişkili çözümler yeniden yüklenene ve şemaları çalışma alanına eklenene kadar, bazı veriler, çalışma alanı kurtarmasından sonra sorgu için kullanılamayabilir.

> [!NOTE]
> * [Azure Portal](https://portal.azure.com)çalışma alanı kurtarma desteklenmiyor. 
> * Geçici silme dönemi sırasında çalışma alanının yeniden oluşturulması, bu çalışma alanı adının zaten kullanımda olduğunu belirten bir bildirim sağlar. 
> 
