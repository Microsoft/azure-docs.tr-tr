---
title: Azure Izleyici ile arşiv & raporu-Azure AD Yetkilendirme Yönetimi
description: Azure Active Directory yetkilendirme yönetimi 'nde Azure Izleyici ile günlükleri arşivlemeyi ve rapor oluşturmayı öğrenin.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 354805b3e2b538c92ba2345df2bcd93968640068
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764396"
---
# <a name="archive-logs-and-reporting-on-azure-ad-entitlement-management-in-azure-monitor"></a>Azure Izleyici 'de Azure AD yetkilendirme yönetiminde arşiv günlükleri ve raporları

Azure AD denetim günlüğünde en fazla 30 gün boyunca denetim olaylarını depolar. Bununla birlikte, denetim verilerini varsayılan saklama süresinden daha uzun bir süre boyunca tutabilirsiniz. Azure [ad depolama, verileri](../reports-monitoring/reference-reports-data-retention.md)bir Azure depolama hesabına yönlendirerek veya Azure izleyici 'yi kullanarak verileri raporlama. Daha sonra bu veriler üzerinde çalışma kitaplarını ve özel sorguları ve raporları kullanabilirsiniz.


## <a name="configure-azure-ad-to-use-azure-monitor"></a>Azure AD 'yi Azure Izleyici kullanacak şekilde yapılandırma
Azure Izleyici çalışma kitaplarını kullanmadan önce Azure AD 'yi, denetim günlüklerinin bir kopyasını Azure Izleyici 'ye gönderecek şekilde yapılandırmanız gerekir.

Azure AD denetim günlüklerini arşivleme, bir Azure aboneliğinde Azure Izleyici olmasını gerektirir. Azure izleyici ['de](../reports-monitoring/concept-activity-logs-azure-monitor.md)Azure izleyici 'de Azure izleyici 'yi kullanmaya ilişkin ön koşullar ve tahmini maliyetler hakkında daha fazla bilgi edinebilirsiniz.

**Önkoşul rolü**: genel yönetici

1. Azure portal, genel yönetici olan bir kullanıcı olarak oturum açın. Azure Izleyici çalışma alanını içeren kaynak grubuna erişiminizin olduğundan emin olun.
 
1. **Azure Active Directory** öğesini seçin ve ardından sol gezinti menüsünde Izleme altında **Tanılama ayarları** ' na tıklayın. Denetim günlüklerini bu çalışma alanına göndermek için zaten bir ayar olup olmadığını denetleyin.

1. Zaten bir ayar yoksa, **Tanılama ayarı Ekle**' ye tıklayın. Azure AD denetim günlüğünü Azure Izleyici çalışma alanına göndermek için Azure [ad günlüklerini Azure izleyici günlükleriyle tümleştirme](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md#send-logs-to-azure-monitor) makalesindeki yönergeleri kullanın.

    ![Tanılama ayarları bölmesi](./media/entitlement-management-logs-and-reporting/audit-log-diagnostics-settings.png)


1. Günlük Azure Izleyici 'ye gönderildikten sonra **Log Analytics çalışma alanları**' nı seçin ve Azure AD denetim günlüklerini içeren çalışma alanını seçin.

1. **Kullanım ve tahmini maliyetler** ' i seçin ve **veri saklama**' ye tıklayın. Kaydırıcıyı, verileri denetim gereksinimlerinize uyacak şekilde tutmak istediğiniz gün sayısıyla değiştirin.

    ![Log Analytics çalışma alanları bölmesi](./media/entitlement-management-logs-and-reporting/log-analytics-workspaces.png)

1. Daha sonra, çalışma alanınızda tutulan tarih aralığını görmek için *arşivlenmiş günlük tarih aralığı* çalışma kitabını kullanabilirsiniz:  
    
    1. **Azure Active Directory** sonra **çalışma kitapları**' nı seçin. 
    
    1. **Sorun giderme Azure Active Directory** bölümünü genişletin ve **arşivlenmiş günlük tarih aralığı**' na tıklayın. 


## <a name="view-events-for-an-access-package"></a>Erişim paketi için olayları görüntüleme  

Bir erişim paketinin olaylarını görüntülemek için, temel alınan Azure izleyici çalışma alanına erişiminizin olması gerekir (bilgi için bkz. [Azure izleyici 'de günlük verilerine ve çalışma alanlarına erişimi yönetme](../../azure-monitor/logs/manage-access.md#manage-access-using-azure-permissions) ) ve aşağıdaki rollerden biri: 

- Genel yönetici  
- Güvenlik yöneticisi  
- Güvenlik okuyucusu  
- Rapor okuyucu  
- Uygulama Yöneticisi  

Olayları görüntülemek için aşağıdaki yordamı kullanın: 

1. Azure portal, **Azure Active Directory** ve ardından **çalışma kitapları**' nı seçin. Yalnızca bir aboneliğiniz varsa adım 3 ' e geçin. 

1. Birden çok aboneliğiniz varsa, çalışma alanını içeren aboneliği seçin.  

1. *Erişim paketi etkinliği* adlı çalışma kitabını seçin. 

1. Bu çalışma kitabında, bir zaman aralığı seçin (emin değil **tümüne** değiştirin) ve bu zaman aralığı boyunca etkinliği olan tüm erişim paketlerinin açılan listesinden bir erişim paketi kimliği seçin. Seçilen zaman aralığı boyunca oluşan erişim paketiyle ilgili olaylar görüntülenir.  

    ![Erişim paketi olaylarını görüntüle](./media/entitlement-management-logs-and-reporting/view-events-access-package.png) 

    Her satır saat, erişim paketi kimliği, işlemin adı, nesne kimliği, UPN ve işlemi başlatan kullanıcının görünen adını içerir.  Ek ayrıntılar JSON 'a dahildir.   


## <a name="create-custom-azure-monitor-queries-using-the-azure-portal"></a>Azure portal kullanarak özel Azure Izleyici sorguları oluşturma
Yetkilendirme Yönetimi olayları dahil olmak üzere Azure AD denetim olayları üzerinde kendi sorgularınızı oluşturabilirsiniz.  

1. Azure portal Azure Active Directory, yeni bir sorgu sayfası oluşturmak için sol gezinti menüsündeki Izleme bölümünde bulunan **Günlükler** ' e tıklayın.

1. Çalışma alanınızın, sorgu sayfasının sol üst kısmında gösterilmesi gerekir. Birden çok Azure Izleyici çalışma alanınız varsa ve Azure AD denetim olaylarını depolamak için kullandığınız çalışma alanı gösterilmemişse, **kapsam Seç**' e tıklayın. Ardından, doğru aboneliği ve çalışma alanını seçin.

1. Sonra sorgu metin alanında "Search *" dizesini silin ve aşağıdaki sorguyla değiştirin:

    ```
    AuditLogs | where Category == "EntitlementManagement"
    ```

1. Ardından **Çalıştır**' a tıklayın. 

    ![Sorguyu başlatmak için Çalıştır 'a tıklayın](./media/entitlement-management-logs-and-reporting/run-query.png)

Tablo, varsayılan olarak son saatten yetkilendirme yönetimi için denetim günlüğü olaylarını gösterir. Eski olayları görüntülemek için "zaman aralığı" ayarını değiştirebilirsiniz. Ancak, bu ayarın değiştirilmesi yalnızca Azure AD, olayları Azure Izleyici 'ye göndermek üzere yapılandırıldıktan sonra gerçekleşen olayları gösterir.

Azure Izleyici 'de tutulan en eski ve en yeni denetim olaylarını bildirmek istiyorsanız aşağıdaki sorguyu kullanın:

```
AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type
```

Azure Izleyici 'de denetim olayları için depolanan sütunlar hakkında daha fazla bilgi için bkz. [Azure izleyici 'de Azure AD denetim günlükleri şemasını yorumlama](../reports-monitoring/reference-azure-monitor-audit-log-schema.md).

## <a name="create-custom-azure-monitor-queries-using-azure-powershell"></a>Azure PowerShell kullanarak özel Azure Izleyici sorguları oluşturma

Azure AD 'yi Azure Izleyici 'ye Günlükler gönderecek şekilde yapılandırdıktan sonra, PowerShell aracılığıyla günlüklere erişebilirsiniz. Ardından, kiracınızda genel yönetici olması gerekmeden, komut dosyalarından veya PowerShell komut satırından sorgular gönderin. 

### <a name="ensure-the-user-or-service-principal-has-the-correct-role-assignment"></a>Kullanıcı veya hizmet sorumlusunun doğru rol atamasına sahip olduğundan emin olun

Azure AD 'de kimlik doğrulaması yapılacak Kullanıcı veya hizmet sorumlusu Log Analytics çalışma alanında uygun Azure rolünde olduğundan emin olun. Rol seçenekleri Log Analytics okuyucu veya Log Analytics katılımcısı olabilir. Bu rollerden birinde zaten varsa, [bir Azure aboneliğiyle log ANALYTICS kimliği almak](#retrieve-log-analytics-id-with-one-azure-subscription)için atlayın.

Rol atamasını ayarlamak ve bir sorgu oluşturmak için aşağıdaki adımları uygulayın:

1. Azure portal, [Log Analytics çalışma alanını](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces
)bulun.

1. **Access Control (IAM)** seçeneğini belirleyin.

1. Rol ataması eklemek için **Ekle** ' ye tıklayın.

    ![Rol ataması ekleyin](./media/entitlement-management-logs-and-reporting/workspace-set-role-assignment.png)

### <a name="install-azure-powershell-module"></a>Azure PowerShell modülünü Install

Uygun rol atamasını aldıktan sonra PowerShell 'i başlatın ve [Azure PowerShell modülünü](/powershell/azure/install-az-ps) (henüz yapmadıysanız), şunu yazarak yükleyebilirsiniz:

```azurepowershell
install-module -Name az -allowClobber -Scope CurrentUser
```
    
Artık Azure AD 'ye kimlik doğrulaması yapmaya hazırsınız ve sorguladığınız Log Analytics çalışma alanının KIMLIĞINI alma.

### <a name="retrieve-log-analytics-id-with-one-azure-subscription"></a>Tek bir Azure aboneliğiyle Log Analytics KIMLIĞINI alma
Yalnızca tek bir Azure aboneliğiniz ve tek bir Log Analytics çalışma alanınız varsa, Azure AD 'de kimlik doğrulaması yapmak, bu aboneliğe bağlanmak ve bu çalışma alanını almak için aşağıdakileri yazın:
 
```azurepowershell
Connect-AzAccount
$wks = Get-AzOperationalInsightsWorkspace
```
 
### <a name="retrieve-log-analytics-id-with-multiple-azure-subscriptions"></a>Birden çok Azure aboneliğine sahip Log Analytics KIMLIĞINI alma

 [Get-Azoperationalınsightsworkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) her seferinde bir abonelikte çalışır. Bu nedenle, birden çok Azure aboneliğiniz varsa, Azure AD günlükleriyle Log Analytics çalışma alanına sahip olan birine bağlandığınızdan emin olmak isteyeceksiniz. 
 
 Aşağıdaki cmdlet 'ler, aboneliklerin bir listesini görüntüler ve Log Analytics çalışma alanına sahip olan aboneliğin KIMLIĞINI bulur:
 
```azurepowershell
Connect-AzAccount
$subs = Get-AzSubscription
$subs | ft
```
 
Gibi bir komut kullanarak PowerShell oturumunuzu yeniden kimlik doğrulaması yapabilir ve bu abonelikle ilişkilendirebilirsiniz `Connect-AzAccount –Subscription $subs[0].id` . Etkileşimli olmayanlar dahil olmak üzere PowerShell 'den Azure 'da kimlik doğrulaması yapma hakkında daha fazla bilgi edinmek için bkz. [Azure PowerShell oturum açma](/powershell/azure/authenticate-azureps).

Bu abonelikte birden çok Log Analytics çalışma alanınız varsa, [Get-Azoperationalınsightsworkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) cmdlet 'i, çalışma alanlarının listesini döndürür. Daha sonra Azure AD günlüklerine sahip olan bir tane bulabilirsiniz. `CustomerId`Bu cmdlet tarafından döndürülen alan, Log Analytics çalışma alanına genel bakış alanındaki Azure Portal gösterildiği "çalışma alanı kimliği" değeri ile aynıdır.
 
```powershell
$wks = Get-AzOperationalInsightsWorkspace
$wks | ft CustomerId, Name
```

### <a name="send-the-query-to-the-log-analytics-workspace"></a>Sorguyu Log Analytics çalışma alanına gönder
Son olarak, bir çalışma alanı tanımlandıktan sonra, bu çalışma alanına bir kusto sorgusu göndermek için [Invoke-Azoperationalınsightsquery](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery) komutunu kullanabilirsiniz. Bu sorgular [kusto sorgu dilinde](/azure/kusto/query/)yazılır.
 
Örneğin, Log Analytics çalışma alanından denetim olay kayıtlarının tarih aralığını, PowerShell cmdlet 'leri ile birlikte kullanarak, şunun gibi bir sorgu gönderebilmeniz için alabilirsiniz:
 
```powershell
$aQuery = "AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type"
$aResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $aQuery
$aResponse.Results |ft
```

Yetkilendirme yönetimi olaylarını, şunun gibi bir sorgu kullanarak da alabilirsiniz:

```azurepowershell
$bQuery = 'AuditLogs | where Category == "EntitlementManagement"'
$bResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $Query
$bResponse.Results |ft 
```

## <a name="next-steps"></a>Sonraki adımlar:
- [Azure Izleyici çalışma kitapları ile etkileşimli raporlar oluşturma](../../azure-monitor/visualize/workbooks-overview.md)
