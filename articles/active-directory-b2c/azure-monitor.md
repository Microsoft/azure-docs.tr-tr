---
title: Azure Izleyici ile Azure AD B2C izleme
titleSuffix: Azure AD B2C
description: Yetkilendirilmiş kaynak yönetimi 'ni kullanarak Azure Izleyici ile Azure AD B2C olaylarını günlüğe kaydetme hakkında bilgi edinin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.author: mimart
ms.subservice: B2C
ms.date: 01/29/2021
ms.openlocfilehash: 0231f7f3882218ef88d6151488da6aa23e4cb8e4
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106106696"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>Azure Izleyici ile Azure AD B2C izleme

Farklı izleme çözümlerine Azure Active Directory B2C (Azure AD B2C) oturum açma ve [Denetim](view-audit-logs.md) günlüklerini yönlendirmek Için Azure izleyici 'yi kullanın. Ardından bu günlükleri uzun vadeli kullanım için saklayabilir veya ortamınızla ilgili içgörülere ulaşmak için üçüncü taraf güvenlik bilgileri ve olay yönetimi (SIEM) araçlarıyla tümleştirebilirsiniz.

Günlük olaylarını şu şekilde yönlendirebilirsiniz:

* Bir Azure [depolama hesabı](../storage/blobs/storage-blobs-introduction.md).
* [Log Analytics çalışma alanı](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) (verileri analiz etmek, panolar oluşturmak ve belirli olaylara uyarı vermek için).
* Azure [Olay Hub 'ı](../event-hubs/event-hubs-about.md) (ve splunk ve sumo Logic Instances ile tümleştirin).

![Azure İzleyici](./media/azure-monitor/azure-monitor-flow.png)

Bu makalede, günlüklerin bir Azure Log Analytics çalışma alanına nasıl aktarılacağı öğrenirsiniz. Daha sonra bir pano oluşturabilir veya Azure AD B2C kullanıcıların etkinliklerini temel alan uyarılar oluşturabilirsiniz.

> [!IMPORTANT]
> Azure AD B2C günlüklerini farklı izleme çözümlerine veya depoya aktarmayı planlarken, aşağıdakileri göz önünde bulundurun. Azure AD B2C Günlükler kişisel verileri içerir. Bu tür veriler, uygun teknik veya kuruluş ölçüleri kullanılarak yetkisiz veya ayrıcalıklı işleme karşı koruma dahil olmak üzere kişisel verilerin uygun güvenliğine izin veren bir şekilde işlenmelidir.


## <a name="deployment-overview"></a>Dağıtıma genel bakış

Azure AD B2C [Azure Active Directory izlemeyi](../active-directory/reports-monitoring/overview-monitoring.md)kullanır. Azure AD B2C kiracınızda Azure Active Directory *tanılama ayarlarını* etkinleştirmek Için, [Azure açık thouse](../lighthouse/concepts/azure-delegated-resource-management.md) ' ı kullanarak [bir kaynağı devredebilir](../lighthouse/concepts/azure-delegated-resource-management.md)ve bu da Azure AD B2C ( **hizmet sağlayıcısı**) bir Azure AD ( **Müşteri**) kaynağını yönetmesine olanak tanır. Bu makaledeki adımları tamamladıktan sonra, **Azure AD B2C** portalınızdaki [Log Analytics çalışma alanını](../azure-monitor/logs/quick-create-workspace.md) içeren *Azure-AD-B2C-Monitor* kaynak grubuna erişebilirsiniz. Ayrıca, Azure AD B2C günlükleri Log Analytics çalışma alanınıza aktarabilirsiniz.

Bu dağıtım sırasında, Azure aboneliğinizi içeren Kiracıdaki Log Analytics çalışma alanı örneğini yapılandırmak için Azure AD B2C dizininizde bir kullanıcı veya grup yetkilendirirsiniz. Yetkilendirmeyi oluşturmak için aboneliği içeren Azure AD kiracınıza bir [Azure Resource Manager](../azure-resource-manager/index.yml) şablonu dağıtırsınız.

Aşağıdaki diyagramda Azure AD ve Azure AD B2C kiracılarınız için yapılandıracağınız bileşenler gösterilmektedir.

![Kaynak grubu projeksiyonu](./media/azure-monitor/resource-group-projection.png)

Bu dağıtım sırasında, hem Azure AD B2C kiracınızı hem de Azure AD kiracınızı Log Analytics çalışma alanının barındırılacak şekilde yapılandıracaksınız. Azure AD B2C hesaba Azure AD B2C kiracısında [genel yönetici](../active-directory/roles/permissions-reference.md#global-administrator) rolü atanmalıdır. Dağıtımı çalıştırmak için kullanılan Azure AD hesabına Azure AD aboneliğinde [sahip](../role-based-access-control/built-in-roles.md#owner) rolü atanmalıdır. Ayrıca, açıklanan her adımı tamamlayarak doğru dizinde oturum açtığınızdan emin olmak da önemlidir.

## <a name="1-create-or-choose-resource-group"></a>1. kaynak grubu oluşturun veya seçin

İlk olarak, Azure AD B2C verileri alacak hedef Log Analytics çalışma alanını içeren bir kaynak grubu oluşturun veya seçin. Azure Resource Manager şablonunu dağıtırken kaynak grubu adını belirtirsiniz.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Portal araç çubuğunda **Dizin + abonelik** simgesini seçin ve ardından **Azure AD kiracınızı** içeren dizini seçin.
1. [Bir kaynak grubu oluşturun](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) veya var olan bir grubu seçin. Bu örnek, *Azure-AD-B2C-Monitor* adlı bir kaynak grubu kullanır.

## <a name="2-create-a-log-analytics-workspace"></a>2. Log Analytics çalışma alanı oluşturma

**Log Analytics çalışma alanı** , Azure izleyici günlük verileri için benzersiz bir ortamdır. Bu Log Analytics çalışma alanını Azure AD B2C [Denetim günlüklerinden](view-audit-logs.md)veri toplamak ve sonra sorgular ve çalışma kitapları ile görselleştirmek ya da uyarı oluşturmak için kullanacaksınız.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Portal araç çubuğunda **Dizin + abonelik** simgesini seçin ve ardından **Azure AD kiracınızı** içeren dizini seçin.
1. [Log Analytics çalışma alanı oluşturun](../azure-monitor/logs/quick-create-workspace.md). Bu örnekte, *Azure-AD-B2C-Monitor* adlı bir kaynak grubunda *AzureAdB2C* adlı bir Log Analytics çalışma alanı kullanılmaktadır.

## <a name="3-delegate-resource-management"></a>3. temsilci kaynak yönetimi

Bu adımda, **hizmet sağlayıcısı** olarak Azure AD B2C kiracınızı seçersiniz. Ayrıca, Azure AD kiracınızdaki gruplara uygun Azure yerleşik rollerini atamanız için gereken yetkilendirmeleri de tanımlarsınız.

### <a name="31-get-your-azure-ad-b2c-tenant-id"></a>3,1 Azure AD B2C kiracı KIMLIĞINIZI alın

İlk olarak, Azure AD B2C dizininizin **KIRACı kimliğini** (dizin kimliği olarak da bilinir) alın.

1. [Azure portalında](https://portal.azure.com/) oturum açın.
1. Portal araç çubuğunda **Dizin + abonelik** simgesini seçin ve ardından **Azure AD B2C** kiracınızı içeren dizini seçin.
1. **Azure Active Directory** seçin, **genel bakış**' ı seçin.
1. **KIRACı kimliğini** kaydedin.

### <a name="32-select-a-security-group"></a>3,2 bir güvenlik grubu seçin

Şimdi, aboneliğinizi içeren dizinde daha önce oluşturduğunuz kaynak grubu için izin vermek istediğiniz bir Azure AD B2C grubunu veya kullanıcıyı seçin.  

Yönetimi kolaylaştırmak için, her rol için Azure AD Kullanıcı *grupları* kullanmanızı öneririz. böylece, izinleri doğrudan bu kullanıcıya atamak yerine gruba bireysel kullanıcı ekleyebilir veya kaldırabilirsiniz. Bu kılavuzda bir güvenlik grubu ekleyeceğiz.

> [!IMPORTANT]
> Bir Azure AD grubu için izinler eklemek üzere, **Grup türü** **güvenlik** olarak ayarlanmalıdır. Grup oluşturulduğunda bu seçenek seçilidir. Daha fazla bilgi için bkz. [temel Grup oluşturma ve Azure Active Directory kullanarak üye ekleme](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

1. **Azure Active Directory** **Azure AD B2C** dizininizde seçili durumdayken **gruplar**' ı seçin ve ardından bir grup seçin. Mevcut bir grubunuz yoksa, bir **güvenlik** grubu oluşturun ve ardından Üyeler ekleyin. Daha fazla bilgi için [temel Grup oluşturma ve Azure Active Directory kullanarak üye ekleme](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)yordamını izleyin. 
1. **Genel bakış**' ı seçin ve grubun **nesne kimliğini** kaydedin.

### <a name="33-create-an-azure-resource-manager-template"></a>3,3 Azure Resource Manager şablonu oluşturma

Daha sonra, daha önce oluşturduğunuz Azure AD kaynak grubuna Azure AD B2C erişimi veren bir Azure Resource Manager şablonu oluşturacaksınız (örneğin, *Azure-AD-B2C-Monitor*). Azure portal açan ve şablonu doğrudan portalda yapılandırmanıza ve dağıtmanıza olanak tanıyan **Azure 'A dağıt** düğmesini kullanarak şablonu GitHub örneğinden dağıtın. Bu adımlar için, Azure AD kiracınızda (Azure AD B2C kiracısında değil) oturum açtığınızdan emin olun.

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Portal araç çubuğunda **Dizin + abonelik** simgesini seçin ve ardından **Azure AD** kiracınızı içeren dizini seçin.
3. Azure portal açmak ve şablonu doğrudan portalda dağıtmak için **Azure 'A dağıt** düğmesini kullanın. Daha fazla bilgi için bkz. [Azure Resource Manager şablonu oluşturma](../lighthouse/how-to/onboard-customer.md#create-an-azure-resource-manager-template).

   [![Azure’a dağıtın](https://aka.ms/deploytoazurebutton)](   https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure-ad-b2c%2Fsiem%2Fmaster%2Ftemplates%2FrgDelegatedResourceManagement.json)

5. **Özel dağıtım** sayfasında, aşağıdaki bilgileri girin:

   | Alan   | Tanım |
   |---------|------------|
   | Abonelik |  *Azure-AD-B2C-Monitor* kaynak grubunun oluşturulduğu Azure aboneliğini içeren dizini seçin. |
   | Region| Kaynağın dağıtılacağı bölgeyi seçin.  | 
   | Msp teklif adı| Bu tanımı açıklayan bir ad. Örneğin, *izleme Azure AD B2C*.  |
   | Msp teklif açıklaması| Teklifinizin kısa bir açıklaması. Örneğin, *Azure AD B2C 'de Azure Izleyicisini etkinleştirilir*.|
   | Kiracı kimliğiyle yönetiliyor| Azure AD B2C kiracınızın **KIRACı kimliği** (dizin kimliği olarak da bilinir). |
   |Yetkilendirmeler|Azure AD `principalId` , `principalIdDisplayName` , ve Azure 'u içeren nesnelerden oluşan bir JSON dizisi belirtin `roleDefinitionId` . , `principalId` B2C grubunun veya bu Azure aboneliğindeki kaynaklara erişimi olacak kullanıcının **nesne kimliğidir** . Bu izlenecek yol için, daha önce kaydettiğiniz grubun nesne KIMLIĞINI belirtin. İçin, `roleDefinitionId` *katkıda bulunan rolü* için [yerleşik rol](../role-based-access-control/built-in-roles.md) değerini kullanın `b24988ac-6180-42a0-ab88-20f7382dd24c` .|
   | RG adı | Azure AD kiracınızda daha önce oluşturduğunuz kaynak grubunun adı. Örneğin, *Azure-AD-B2C-Monitor*. |

   Aşağıdaki örnek, bir güvenlik grubu içeren bir yetkilendirmeler dizisini gösterir.

   ```json
   [
       {
           "principalId": "<Replace with group's OBJECT ID>",
           "principalIdDisplayName": "Azure AD B2C tenant administrators",
           "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
       }
   ]
   ```

Şablonu dağıttıktan sonra kaynak projeksiyonun tamamlanması birkaç dakika sürebilir (genellikle beşten fazla olmayan). Azure AD kiracınızda dağıtımı doğrulayabilirsiniz ve kaynak projeksiyonunun ayrıntılarını alabilirsiniz. Daha fazla bilgi için bkz. [hizmet sağlayıcılarını görüntüleme ve yönetme](../lighthouse/how-to/view-manage-service-providers.md).

## <a name="4-select-your-subscription"></a>4. aboneliğinizi seçin

Şablonu dağıttıktan ve kaynak projeksiyonunun tamamlanmasını birkaç dakika bekledikten sonra, aboneliğinizi Azure AD B2C dizininizle ilişkilendirmek için aşağıdaki adımları izleyin.

1. Şu anda oturum açtıysanız Azure portal oturumunuzu kapatın (Bu, oturum kimlik bilgilerinizin bir sonraki adımda yenilenmesini sağlar).
2. [Azure portal](https://portal.azure.com) **Azure AD B2C** Yönetici hesabınızla oturum açın. Bu hesap, [temsilci kaynak yönetimi](#3-delegate-resource-management) adımında belirttiğiniz güvenlik grubunun bir üyesi olmalıdır.
3. Portal araç çubuğunda **Dizin + abonelik** simgesini seçin.
4. Oluşturduğunuz Azure aboneliğini ve *Azure-AD-B2C-Monitor* kaynak grubunu IÇEREN Azure ad dizini ' ni seçin.

    ![Dizini değiştirin](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)

1. Doğru dizin ve aboneliği seçtiğinizden emin olun. Bu örnekte, tüm dizinler ve tüm abonelikler seçilidir.

    ![Dizin & abonelik filtresi 'nde seçilen tüm dizinler](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="5-configure-diagnostic-settings"></a>5. tanılama ayarlarını yapılandırma

Tanılama ayarları, bir kaynağın hangi günlüklerde ve ölçümlerinin gönderileceğini tanımlar. Olası hedefler şunlardır:

- [Azure depolama hesabı](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)
- [Olay Hub 'ları](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs) çözümleri
- [Log Analytics çalışma alanı](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)

Bu örnekte, bir pano oluşturmak için Log Analytics çalışma alanını kullanırız.

### <a name="51-create-diagnostic-settings"></a>5,1 Tanılama ayarları oluşturma

Azure portal [Tanılama ayarları oluşturmaya](../active-directory/reports-monitoring/overview-monitoring.md) hazırsınız.

Azure AD B2C etkinlik günlüklerinin izleme ayarlarını yapılandırmak için:

1. [Azure portal](https://portal.azure.com/) Azure AD B2C Yönetici hesabınızla oturum açın. Bu hesap, [bir güvenlik grubu seçin](#32-select-a-security-group) adımında belirttiğiniz güvenlik grubunun bir üyesi olmalıdır.
1. Portal araç çubuğunda **Dizin + abonelik** simgesini seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. **Azure Active Directory** seçin
1. **İzleme** bölümünde **Tanılama ayarları**'nı seçin.
1. Kaynak için mevcut ayarlar varsa, önceden yapılandırılmış ayarların bir listesini görürsünüz. Yeni bir ayar eklemek için **Tanılama ayarı Ekle** ' yi seçin veya var olan bir ayarı düzenlemek için **Düzenle** ' yi seçin. Her bir ayarın hedef türlerin her biri birden fazla olamaz.

    ![Azure portal Tanılama ayarları bölmesi](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

1. Ayarınız yoksa, bir ad verin.
1. Günlükleri göndermek için her bir hedefin kutusunu işaretleyin. Ayarlarını **Aşağıdaki tabloda açıklandığı gibi** belirtmek için **Yapılandır** ' ı seçin.
1. **Log Analytics gönder**' i seçin ve daha önce oluşturduğunuz **çalışma alanının adını** ( `AzureAdB2C` ) seçin.
1. **Auditlogs** ve **signınlogs**' u seçin.
1. **Kaydet**’i seçin.

> [!NOTE]
> Bir olay, [bir Log Analytics çalışma alanında görünmesi](../azure-monitor/logs/data-ingestion-time.md)için bir olayın yayıldıktan sonra 15 dakika kadar sürebilir. Ayrıca, verilerin etkisini etkileyebilecek ve raporlama 'da önemli bir rol oynatabilen [Active Directory raporlama gecikme](../active-directory/reports-monitoring/reference-reports-latencies.md)süreleri hakkında daha fazla bilgi edinin.

"Azure AD B2C dizininiz için Azure Izleyicisini kullanmak üzere tanılama ayarlarını kurmak için" hata iletisini görürseniz, yetkilendirilmiş kaynak yönetimini ayarlamanız gerekir, " [güvenlik grubunun](#32-select-a-security-group) üyesi olan bir kullanıcıyla oturum açıp [aboneliğinizi](#4-select-your-subscription)seçtiğinizden emin olun.

## <a name="6-visualize-your-data"></a>6. verilerinizi görselleştirin

Artık Log Analytics çalışma alanınızı, verilerinizi görselleştirmek ve uyarıları yapılandırmak için yapılandırabilirsiniz. Bu yapılandırma, hem Azure AD kiracınızda hem de Azure AD B2C kiracınızda yapılabilir.

### <a name="61-create-a-query"></a>6,1 sorgu oluşturma

Günlük sorguları, Azure Izleyici günlüklerinde toplanan verilerin değerini tamamen kullanmanıza yardımcı olur. Güçlü bir sorgu dili, birden çok tablodan veri birleştirme, büyük veri kümelerini toplama ve en az kodla karmaşık işlemler gerçekleştirmenize olanak tanır. Neredeyse her soru yanıtlanarak, destekleyici veriler toplandıkça ve analiz, doğru sorgunun nasıl oluşturulacağını anlamış olabilir. Daha fazla bilgi için bkz. [Azure izleyici 'de günlük sorgularını kullanmaya başlama](../azure-monitor/logs/get-started-queries.md).

1. **Log Analytics çalışma** alanından **Günlükler** ' i seçin
1. Sorgu Düzenleyicisi 'nde, aşağıdaki [kusto sorgu dili](/azure/data-explorer/kusto/query/) sorgusunu yapıştırın. Bu sorgu, son x gün içinde işleme göre ilke kullanımını gösterir. Varsayılan süre 90 gün (90D) olarak ayarlanır. Sorgunun yalnızca bir belirteç/kodun ilke tarafından verildiği işleme odaklandığına dikkat edin.

    ```kusto
    AuditLogs
    | where TimeGenerated  > ago(90d)
    | where OperationName contains "issue"
    | extend  UserId=extractjson("$.[0].id",tostring(TargetResources))
    | extend Policy=extractjson("$.[1].value",tostring(AdditionalDetails))
    | summarize SignInCount = count() by Policy, OperationName
    | order by SignInCount desc  nulls last
    ```

1. **Çalıştır**'ı seçin. Sorgu sonuçları, ekranın alt kısmında görüntülenir.
1. Sorgunuzu daha sonra kullanmak üzere kaydetmek için **Kaydet**' i seçin.

   ![Log Analytics günlük Düzenleyicisi](./media/azure-monitor/query-policy-usage.png)

1. Aşağıdaki ayrıntıları girin:

    - **Ad** -sorgunuzun adını girin.
    - Seçim **olarak kaydet** `query` .
    - **Kategori** -seçin `Log` .

1. **Kaydet**’i seçin.

Ayrıca, [render](/azure/data-explorer/kusto/query/renderoperator?pivots=azuremonitor) işlecini kullanarak verileri görselleştirmek için sorgunuzu değiştirebilirsiniz.

```kusto
AuditLogs
| where TimeGenerated  > ago(90d)
| where OperationName contains "issue"
| extend  UserId=extractjson("$.[0].id",tostring(TargetResources))
| extend Policy=extractjson("$.[1].value",tostring(AdditionalDetails))
| summarize SignInCount = count() by Policy
| order by SignInCount desc  nulls last
| render  piechart
```

![Log Analytics günlük Düzenleyicisi pasta](./media/azure-monitor/query-policy-usage-pie.png)

Daha fazla örnek için Azure AD B2C [SIEM GitHub deposu](https://aka.ms/b2csiem)' na bakın.

### <a name="62-create-a-workbook"></a>6,2 çalışma kitabı oluşturma

Çalışma kitapları, Azure portalda zengin görsel raporlarının oluşturulması ve veri analizi için esnek bir tuval sağlar. Azure 'da birden çok veri kaynağına dokunmanıza ve bunları Birleşik etkileşimli deneyimler halinde birleştirmeye olanak tanır. Daha fazla bilgi için bkz. [Azure Izleyici çalışma kitapları](../azure-monitor/visualize/workbooks-overview.md).

JSON Galerisi şablonu kullanarak yeni bir çalışma kitabı oluşturmak için aşağıdaki yönergeleri izleyin. Bu çalışma kitabı, Azure AD B2C kiracı için bir **Kullanıcı öngörüleri** ve **kimlik doğrulama** panosu sağlar.

1. **Log Analytics çalışma** alanından **çalışma kitapları**' nı seçin.
1. Araç çubuğundan, yeni bir çalışma kitabı oluşturmak için **+ Yeni** seçeneğini belirleyin.
1. **Yeni çalışma kitabı** sayfasında, araç çubuğundaki seçeneğini kullanarak **Gelişmiş Düzenleyici** seçin **</>** .

     ![Galeri şablonu](./media/azure-monitor/wrkb-adv-editor.png)

1. **Galeri şablonu**' nu seçin.
1. **Galeri ŞABLONUNDAKI JSON öğesini** [Azure AD B2C temel çalışma kitabındaki](https://raw.githubusercontent.com/azure-ad-b2c/siem/master/workbooks/dashboard.json)içerikle değiştirin:
1. **Uygula** düğmesini kullanarak şablonu uygulayın.
1. Çalışma kitabını düzenlemenin tamamlanması için araç çubuğundan **Düzenle** düğmesini seçin.
1. Son olarak, çalışma kitabını araç çubuğundan **Kaydet** düğmesini kullanarak kaydedin.
1. *Azure AD B2C panosu* gibi bir **başlık** sağlayın.
1. **Kaydet**’i seçin.

    ![Çalışma kitabını Kaydet](./media/azure-monitor/wrkb-title.png)

Çalışma kitabı, raporları bir pano biçiminde görüntüler.

![Çalışma kitabı ilk Pano](./media/azure-monitor/wkrb-dashboard-1.png)

![Çalışma kitabı ikinci Pano](./media/azure-monitor/wrkb-dashboard-2.png)

![Çalışma kitabı Üçüncü Pano](./media/azure-monitor/wrkb-dashboard-3.png)


## <a name="create-alerts"></a>Uyarı oluşturma

Uyarılar, Azure İzleyici'deki uyarı kuralları tarafından oluşturulur ve kaydedilmiş sorguları veya özel günlük aramalarını düzenli aralıklarla otomatik olarak çalıştırabilir. Belirli performans ölçümleri temelinde veya belirli bir zaman aralığında bir olay sayısı oluşturulduğunda, bir olay olmadığında ya da belirli olaylar oluşturulduğunda uyarılar oluşturabilirsiniz. Örneğin, uyarılar, ortalama oturum açma sayısı belirli bir eşiği aştığında size bildirimde bulunabilir. Daha fazla bilgi için bkz. [uyarı oluşturma](../azure-monitor/alerts/tutorial-response.md).


**Toplam isteklerindeki** %25 ' in önceki döneme karşılaştırıldığı her durumda bir [e-posta bildirimi](../azure-monitor/alerts/action-groups.md#configure-notifications) gönderecek yeni bir Azure uyarısı oluşturmak için aşağıdaki yönergeleri kullanın. Uyarı her 5 dakikada bir çalışır ve son 24 saat içinde bırakma için arama yapılır. Uyarılar kusto sorgu dili kullanılarak oluşturulur.


1. **Log Analytics çalışma** alanından **Günlükler**' i seçin. 
1. Aşağıdaki sorguyu kullanarak yeni bir **kusto sorgusu** oluşturun.

    ```kusto
    let start = ago(24h);
    let end = now();
    let threshold = -25; //25% decrease in total requests.
    AuditLogs
    | serialize TimeGenerated, CorrelationId, Result
    | make-series TotalRequests=dcount(CorrelationId) on TimeGenerated in range(start, end, 1h)
    | mvexpand TimeGenerated, TotalRequests
    | where TotalRequests > 0
    | serialize TotalRequests, TimeGenerated, TimeGeneratedFormatted=format_datetime(todatetime(TimeGenerated), 'yyyy-M-dd [hh:mm:ss tt]')
    | project   TimeGeneratedFormatted, TotalRequests, PercentageChange= ((toreal(TotalRequests) - toreal(prev(TotalRequests,1)))/toreal(prev(TotalRequests,1)))*100
    | order by TimeGeneratedFormatted
    | where PercentageChange <= threshold   //Trigger's alert rule if matched.
    ```

1. Sorguyu test etmek için **Çalıştır**' ı seçin. Son 24 saat içindeki toplam isteklerde %25 veya daha fazla bir bırakma varsa sonuçları görmeniz gerekir.
1. Yukarıdaki sorguyu temel alan bir uyarı kuralı oluşturmak için, araç çubuğunda bulunan **+ Yeni uyarı kuralı** seçeneğini kullanın.
1. **Uyarı kuralı oluştur** sayfasında **koşul adı** ' nı seçin. 
1. **Sinyal mantığını Yapılandır** sayfasında, aşağıdaki değerleri ayarlayın ve ardından değişiklikleri kaydetmek için **bitti** düğmesini kullanın.
    * Uyarı mantığı: **0**' **dan büyük** **sonuçların sayısını** ayarlayın.
    * Değerlendirme temeli: dönem için **1440** (dakika) ve sıklık için **5** (dakika cinsinden) seçin 

    ![Uyarı kuralı koşulu oluşturma](./media/azure-monitor/alert-create-rule-condition.png)

Uyarı oluşturulduktan sonra, **Log Analytics çalışma alanına** gidin ve **Uyarılar**' ı seçin. Bu sayfada, **zaman aralığına** göre ayarlanan süre seçeneğinde tetiklenen tüm uyarılar görüntülenir.  

### <a name="configure-action-groups"></a>Eylem gruplarını Yapılandır

Azure Izleyici ve hizmet durumu uyarıları, kullanıcılara bir uyarının tetiklendiğini bildirmek için eylem gruplarını kullanır. Sesli çağrı gönderme, SMS, e-posta; veya çeşitli otomatikleştirilmiş eylem türlerini tetikleyerek. [Azure Portal eylem gruplarını oluşturma ve yönetme](../azure-monitor/alerts/action-groups.md) kılavuzunu izleyin

Uyarı bildirimi e-postasına bir örnek aşağıda verilmiştir. 

   ![E-posta ile bildirim](./media/azure-monitor/alert-email-notification.png)

## <a name="multiple-tenants"></a>Birden çok kiracı

Birden çok Azure AD B2C kiracı günlüğünü aynı Log Analytics çalışma alanına (veya Azure Storage hesabı ya da Olay Hub 'ı) eklemek için farklı **MSP teklif adı** değerleriyle ayrı dağıtımlar yapmanız gerekir. Log Analytics çalışma alanınızın, [oluşturma veya kaynak grubu seçme](#1-create-or-choose-resource-group)bölümünde yapılandırdığınız kaynak grubunda olduğundan emin olun.

Birden çok Log Analytics çalışma alanıyla çalışırken, birden çok çalışma alanında çalışan sorgular oluşturmak için [çapraz çalışma alanı sorgusunu](../azure-monitor/logs/cross-workspace-query.md) kullanın. Örneğin, aşağıdaki sorgu, farklı kiracılardan iki denetim günlüğünün aynı kategoriye göre (örneğin, kimlik doğrulaması) bir birleştirmesini gerçekleştirir:

```kusto
workspace("AD-B2C-TENANT1").AuditLogs
| join  workspace("AD-B2C-TENANT2").AuditLogs
  on $left.Category== $right.Category
```

## <a name="change-the-data-retention-period"></a>Veri saklama süresini değiştirme

Azure Izleyici günlükleri, kuruluşunuzda bulunan veya Azure 'da dağıtılan herhangi bir kaynaktan günlük büyük miktarlarda veri toplamayı, dizinlemesini ve depolamayı desteklemek için tasarlanmıştır. Varsayılan olarak, Günlükler 30 gün boyunca korunur, ancak saklama süresi iki yıla kadar artırılabilir. [Azure Izleyici günlükleri ile kullanımı ve maliyetleri yönetmeyi](../azure-monitor/logs/manage-cost-storage.md)öğrenin. Fiyatlandırma katmanını seçtikten sonra, [veri saklama süresini değiştirebilirsiniz](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period).

## <a name="next-steps"></a>Sonraki adımlar

* Azure AD B2C [SIEM galerisinde](https://aka.ms/b2csiem)daha fazla örnek bulun. 

* Azure Izleyici 'de tanılama ayarlarını ekleme ve yapılandırma hakkında daha fazla bilgi için bkz. [öğretici: kaynak günlüklerini bir Azure kaynağından toplayın ve çözümleyin](../azure-monitor/essentials/monitor-azure-resource.md).

* Azure AD günlüklerini bir olay hub 'ına akışı hakkında daha fazla bilgi için bkz. [öğretici: Azure Olay Hub 'ına akış Azure Active Directory günlükleri](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
