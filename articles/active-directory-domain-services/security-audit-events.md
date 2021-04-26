---
title: Azure AD Domain Services için güvenlik denetimlerini etkinleştir | Microsoft Docs
description: Azure AD Domain Services ' de analiz ve uyarılar için olayların günlüğe kaydedilmesini merkezileştirmek üzere güvenlik denetimlerini nasıl etkinleştireceğinizi öğrenin
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: caf46850b3d8d6946225575b8a9a732a90847482
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100574141"
---
# <a name="enable-security-audits-for-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services için güvenlik denetimlerini etkinleştir

Azure Active Directory Domain Services (Azure AD DS) güvenlik denetimleri, Azure 'un hedeflenen kaynaklara yönelik güvenlik olaylarının akışını sağlar. Bu kaynaklar Azure Storage, Azure Log Analytics çalışma alanları veya Azure Olay Hub 'ı içerir. Güvenlik denetim olaylarını etkinleştirdikten sonra Azure AD DS, seçilen kategorinin tüm denetlenen olaylarını hedeflenen kaynağa gönderir.

Olayları Azure depolama 'ya arşivleyebilir ve olayları Azure Event Hubs kullanarak güvenlik bilgileri ve olay yönetimi (ya da eşdeğeri) yazılımlarına kaydedebilir ya da kendi analizinizi yapabilir ve Azure portal Azure Log Analytics çalışma alanlarını kullanabilirsiniz.

> [!IMPORTANT]
> Azure AD DS Güvenlik denetimleri yalnızca Azure Resource Manager tabanlı yönetilen etki alanları için kullanılabilir. Geçirme hakkında daha fazla bilgi için bkz. [Azure AD DS 'Yi klasik sanal ağ modelinden Kaynak Yöneticisi 'ye geçirme][migrate-azure-adds].

## <a name="security-audit-destinations"></a>Güvenlik denetimi hedefleri

Azure depolama, Azure Event Hubs veya Azure Log Analytics çalışma alanlarını Azure AD DS Güvenlik denetimleri için hedef kaynak olarak kullanabilirsiniz. Bu hedefler birleştirilebilir. Örneğin, Azure depolama 'yı güvenlik denetim olaylarını arşivlemek için kullanabilirsiniz, ancak kısa vadede bilgileri analiz etmek ve raporlamak için bir Azure Log Analytics çalışma alanı.

Aşağıdaki tabloda, her bir hedef kaynak türü için senaryolar özetlenmektedir.

> [!IMPORTANT]
> Azure AD DS güvenlik denetimlerini etkinleştirmeden önce hedef kaynağı oluşturmanız gerekir. Azure portal, Azure PowerShell veya Azure CLı kullanarak bu kaynakları oluşturabilirsiniz.

| Hedef kaynak | Senaryo |
|:---|:---|
|Azure Depolama| Bu hedef, birincil ihtiyacınız olduğunda, arşiv amacıyla güvenlik denetim olaylarını depolamak gerektiğinde kullanılmalıdır. Diğer hedefler arşiv amaçlarıyla kullanılabilir, ancak bu hedefler, arşivleme gereksinimlerinden daha fazla yetenek sağlar. <br /><br />Azure AD DS güvenlik denetim olaylarını etkinleştirmeden önce, önce [bir Azure depolama hesabı oluşturun](../storage/common/storage-account-create.md).|
|Azure Event Hubs| Bu hedef, birincil ihtiyacınız olduğunda, veri analizi yazılımı veya güvenlik bilgileri & olay yönetimi (SıEM) yazılımları gibi ek yazılımlarla güvenlik denetim olaylarını paylaşmak gerektiğinde kullanılmalıdır.<br /><br />Azure AD DS güvenlik denetim olaylarını etkinleştirmeden önce [Azure Portal kullanarak bir olay hub 'ı oluşturun](../event-hubs/event-hubs-create.md)|
|Azure Log Analytics çalışma alanı| Bu hedef, birincil ihtiyacınız olduğunda, Azure portal doğrudan güvenli denetimleri analiz etmek ve gözden geçirmek gerektiğinde kullanılmalıdır.<br /><br />Azure AD DS güvenlik denetim olaylarını etkinleştirmeden önce, [Azure Portal bir Log Analytics çalışma alanı oluşturun.](../azure-monitor/logs/quick-create-workspace.md)|

## <a name="enable-security-audit-events-using-the-azure-portal"></a>Azure portal kullanarak güvenlik denetim olaylarını etkinleştirin

Azure portal kullanarak Azure AD DS güvenlik denetim olaylarını etkinleştirmek için aşağıdaki adımları uygulayın.

> [!IMPORTANT]
> Azure AD DS Güvenlik denetimleri geriye dönük olarak etkin değildir. Olayları eski bir kaynaktan alamaz veya yeniden çalıştıramazsınız. Azure AD DS, yalnızca güvenlik denetimleri etkinleştirildikten sonra oluşan olayları gönderebilir.

1. https://portal.azure.com adresinden Azure portalında oturum açın.
1. Azure portal en üstünde **Azure AD Domain Services**' i arayıp seçin. *Aaddscontoso.com* gibi yönetilen etki alanınızı seçin.
1. Azure AD DS penceresinde, sol taraftaki **Tanılama ayarları** ' nı seçin.
1. Hiçbir Tanılama varsayılan olarak yapılandırılmaz. Başlamak için **Tanılama ayarı Ekle**' yi seçin.

    ![Azure AD Domain Services için bir tanılama ayarı ekleyin](./media/security-audit-events/add-diagnostic-settings.png)

1. Tanılama yapılandırması için *aeklemeleri-denetim* gibi bir ad girin.

    İstediğiniz güvenlik denetimi hedefinin kutusunu işaretleyin. Bir Azure depolama hesabı, Azure Olay Hub 'ı veya Log Analytics çalışma alanı arasından seçim yapabilirsiniz. Bu hedef kaynakların Azure aboneliğinizde zaten mevcut olması gerekir. Bu sihirbazda hedef kaynakları oluşturamazsınız.

    ![Yakalanacak gereken hedef ve denetim olaylarının türünü etkinleştir](./media/security-audit-events/diagnostic-settings-page.png)

    * **Azure depolama**
        * **Bir depolama hesabına arşiv**' i seçin ve ardından **Yapılandır**' ı seçin.
        * Güvenlik denetim olaylarını arşivlemek için kullanmak istediğiniz **aboneliği** ve **Depolama hesabını** seçin.
        * Hazırlık sırasında **Tamam**' ı seçin.
    * **Azure Olay Hub 'ları**
        * **Bir olay hub 'ına akış**' ı seçin ve ardından **Yapılandır**' ı seçin.
        * **Aboneliği** ve **Olay Hub 'ı ad alanını** seçin. Gerekirse, bir **Olay Hub 'ı adı** ve ardından **Olay Hub 'ı ilke adı**' nı da seçin.
        * Hazırlık sırasında **Tamam**' ı seçin.
    * **Azure log analitik çalışma alanları**
        * **Log Analytics gönder**' i seçin, ardından güvenlik denetim olaylarını depolamak için kullanmak istediğiniz **aboneliği** ve **Log Analytics çalışma alanını** seçin.

1. Belirli hedef kaynağa dahil etmek istediğiniz günlük kategorilerini seçin. Denetim olaylarını bir Azure depolama hesabına gönderirseniz, verilerin saklanacağı gün sayısını tanımlayan bir bekletme ilkesi de yapılandırabilirsiniz. Varsayılan ayarı *0* ' ın tüm verileri korur ve bir süre sonra olayları döndürmez.

    Tek bir yapılandırma içindeki hedeflenen her kaynak için farklı günlük kategorileri seçebilirsiniz. Bu özellik, Log Analytics için tutmak istediğiniz günlük kategorilerini seçmenizi sağlar ve örneğin arşivlemek istediğiniz kategorileri kaydeder.

1. İşiniz bittiğinde, değişikliklerinizi uygulamak için **Kaydet** ' i seçin. Hedef kaynaklar, yapılandırma kaydedildikten hemen sonra Azure AD DS Security denetim olaylarını almaya başlar.

## <a name="enable-security-audit-events-using-azure-powershell"></a>Azure PowerShell kullanarak güvenlik denetim olaylarını etkinleştirme

Azure PowerShell kullanarak Azure AD DS güvenlik denetim olaylarını etkinleştirmek için aşağıdaki adımları uygulayın. Gerekirse, önce [Azure PowerShell modülünü yükledikten sonra Azure aboneliğinize bağlanın](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Azure AD DS Güvenlik denetimleri geriye dönük olarak etkin değildir. Olayları eski bir kaynaktan alamaz veya yeniden çalıştıramazsınız. Azure AD DS, yalnızca güvenlik denetimleri etkinleştirildikten sonra oluşan olayları gönderebilir.

1. [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) cmdlet 'Ini kullanarak Azure aboneliğinizde kimlik doğrulaması yapın. İstendiğinde, hesap kimlik bilgilerinizi girin.

    ```azurepowershell
    Connect-AzAccount
    ```

1. Güvenlik denetim olayları için hedef kaynağı oluşturun.

    * **Azure depolama**  -  [Azure PowerShell kullanarak bir depolama hesabı oluşturma](../storage/common/storage-account-create.md?tabs=azure-powershell)
    * **Azure Olay Hub 'ları**  -  [Azure PowerShell kullanarak bir olay hub 'ı oluşturun](../event-hubs/event-hubs-quickstart-powershell.md). Ayrıca, Olay Hub 'ı *ad alanına* Azure AD DS izinleri veren bir yetkilendirme kuralı oluşturmak için [New-AzEventHubAuthorizationRule](/powershell/module/az.eventhub/new-azeventhubauthorizationrule) cmdlet 'ini de kullanmanız gerekebilir. Yetkilendirme kuralı **Yönet**, **dinle** ve **Gönder** haklarını içermelidir.

        > [!IMPORTANT]
        > Olay Hub 'ında değil, Olay Hub 'ı ad alanında yetkilendirme kuralını ayarlamış olduğunuzdan emin olun.

    * **Azure log analitik çalışma alanları**  -  [Azure PowerShell bir Log Analytics çalışma alanı oluşturun](../azure-monitor/logs/powershell-workspace-configuration.md).

1. [Get-AzResource](/powershell/module/Az.Resources/Get-AzResource) cmdlet 'Ini kullanarak Azure AD DS yönetilen etki alanınız IÇIN kaynak kimliği alın. $Aadds adlı bir değişken oluşturun *.* Değerin tutulacağı RESOURCEID:

    ```azurepowershell
    $aadds = Get-AzResource -name aaddsDomainName
    ```

1. [Set-AzDiagnosticSetting](/powershell/module/Az.Monitor/Set-AzDiagnosticSetting) cmdlet 'Ini kullanarak Azure tanılama ayarlarını, Azure AD Domain Services güvenlik denetim olayları için hedef kaynağı kullanacak şekilde yapılandırın. Aşağıdaki örneklerde, değişkeni *$aadds. RESOURCEID* , önceki adımdan kullanılır.

    * **Azure depolama** - *storageaccountıd* değerini depolama hesabınızın adıyla değiştirin:

        ```powershell
        Set-AzDiagnosticSetting `
            -ResourceId $aadds.ResourceId `
            -StorageAccountId storageAccountId `
            -Enabled $true
        ```

    * **Azure Olay Hub 'ları** - *Eventhubname* değerini, Olay Hub 'ınızın adı ve *eventhubruleıd* değerini yetkilendirme kuralı Kimliğinizle değiştirin:

        ```powershell
        Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId `
            -EventHubName eventHubName `
            -EventHubAuthorizationRuleId eventHubRuleId `
            -Enabled $true
        ```

    * **Azure log analitik çalışma alanları** -çalışma alanı *kimliğini* Log Analytics çalışma alanının kimliğiyle değiştirin:

        ```powershell
        Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId `
            -WorkspaceID workspaceId `
            -Enabled $true
        ```

## <a name="query-and-view-security-audit-events-using-azure-monitor"></a>Azure Izleyici kullanarak güvenlik denetim olaylarını sorgulama ve görüntüleme

Log analitik çalışma alanları, Azure Izleyici ve kusto sorgu dilini kullanarak güvenlik denetim olaylarını görüntülemenize ve çözümlemenize olanak sağlar. Bu sorgu dili, kolay okunabilir bir sözdizimi ile Power analitik özelliklerine sahip olan salt okunurdur kullanım için tasarlanmıştır. Kusto sorgu dillerini kullanmaya başlama hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

* [Azure İzleyici belgeleri](../azure-monitor/index.yml)
* [Azure Izleyici 'de Log Analytics kullanmaya başlama](../azure-monitor/logs/log-analytics-tutorial.md)
* [Azure İzleyici’de günlük sorgularını kullanmaya başlama](../azure-monitor/logs/get-started-queries.md)
* [Log Analytics verilerinden pano oluşturma ve paylaşma](../azure-monitor/visualize/tutorial-logs-dashboards.md)

Aşağıdaki örnek sorgular, Azure AD DS güvenlik denetim olaylarını çözümlemeye başlamak için kullanılabilir.

### <a name="sample-query-1"></a>Örnek sorgu 1

Son yedi güne ait tüm hesap kilitleme olaylarını görüntüleyin:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>Örnek sorgu 2

Hesap kilitleme olaylarını (*4740*) 1 Haziran 2020 ile 9 saat arasında görüntüle ve 10 Haziran 2020 gece yarısı, tarih ve saate göre artan düzende sıralanır:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2020-06-03 09:00) and TimeGenerated <= datetime(2020-06-10)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>Örnek sorgu 3

Hesap oturum açma olaylarını, Kullanıcı adlı hesap için yedi gün önce (bundan sonra) görüntüleyin:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>Örnek sorgu 4

Hatalı parola kullanarak oturum açmayı deneyen kullanıcı adlı hesap için artık hesap oturum açma olaylarını yedi gün önce görüntüleyin (*0Xc0000006a*):

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>Örnek sorgu 5

Hesap kilitlenirken oturum açmayı deneyen kullanıcı adlı hesap için artık hesap oturum açma olaylarını yedi gün önce görüntüleyin (*0Xc0000234*):

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>Örnek sorgu 6

Tüm kilitli kullanıcılar için gerçekleşen tüm oturum açma girişimleri için hemen yedi gün önce hesap oturum açma olaylarının sayısını görüntüleyin:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="audit-event-categories"></a>Denetim olayı kategorileri

Azure AD DS Güvenlik denetimleri geleneksel AD DS etki alanı denetleyicileri için geleneksel denetimle hizalanır. Karma ortamlarda, olayları analiz edilirken aynı mantığın kullanılabilmesi için var olan denetim düzenlerini yeniden kullanabilirsiniz. Sorun gidermeniz veya çözümlemeniz gereken senaryoya bağlı olarak, farklı denetim olayı kategorilerinin hedeflenmiş olması gerekir.

Aşağıdaki denetim olayı kategorileri kullanılabilir:

| Denetim kategorisi adı | Description |
|:---|:---|
| Hesap oturum açma|Denetimler, bir etki alanı denetleyicisindeki veya yerel bir güvenlik Hesapları Yöneticisi (SAM) hesap verilerinin kimliğini doğrulamaya çalışır.</p>Oturum açma ve oturum kapatma ilkesi ayarları ve olayları izleme belirli bir bilgisayara erişmeye çalışır. Bu kategorideki ayarlar ve olaylar, kullanılan hesap veritabanına odaklanmaktadır. Bu kategori aşağıdaki alt kategorileri içerir:<ul><li>[Kimlik Bilgisi Doğrulamayı Denetleme](/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[Kerberos Kimlik Doğrulaması Hizmetini Denetleme](/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[Kerberos Hizmet Bileti İşlemlerini Denetleme](/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[Diğer oturum açma/kapatma olaylarını denetleme](/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| Hesap Yönetimi|Kullanıcı ve bilgisayar hesapları ve gruplarındaki değişiklikleri denetler. Bu kategori aşağıdaki alt kategorileri içerir:<ul><li>[Uygulama Grubu Yönetimini Denetleme](/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[Bilgisayar Hesabı Yönetimini Denetleme](/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[Dağıtım Grubu Yönetimini Denetleme](/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[Diğer hesap yönetimini denetleme](/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[Güvenlik Grubu Yönetimini Denetleme](/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[Kullanıcı Hesabı Yönetimini Denetleme](/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| Ayrıntı Izleme|, Bu bilgisayardaki bireysel uygulamaların ve kullanıcıların etkinliklerini denetler ve bir bilgisayarın nasıl kullanıldığını anlayın. Bu kategori aşağıdaki alt kategorileri içerir:<ul><li>[DPAPI Etkinliğini Denetleme](/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[PNP etkinliğini denetle](/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[İşlem Oluşturmayı Denetleme](/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[İşlem Sonlandırmayı Denetleme](/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[RPC Olaylarını Denetleme](/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| Dizin Hizmetleri erişimi|Denetimler Active Directory Domain Services (AD DS) içindeki nesnelere erişme ve bunları değiştirmeye çalışır. Bu denetim olayları yalnızca etki alanı denetleyicilerinde günlüğe kaydedilir. Bu kategori aşağıdaki alt kategorileri içerir:<ul><li>[Ayrıntılı Dizin Hizmeti Çoğaltmayı Denetleme](/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[Dizin Hizmeti Erişimini Denetleme](/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[Dizin Hizmeti Değişikliklerini Denetleme](/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[Dizin Hizmeti Çoğaltmasını Denetleme](/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| Logon-Logoff|Denetimler bir bilgisayarda etkileşimli olarak veya ağ üzerinden oturum açmaya çalışır. Bu olaylar, Kullanıcı etkinliğini izlemek ve ağ kaynaklarında olası saldırıları belirlemek için faydalıdır. Bu kategori aşağıdaki alt kategorileri içerir:<ul><li>[Hesap Kilitlemeyi Denetleme](/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[Kullanıcı/cihaz taleplerini denetleme](/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[IPsec Genişletilmiş Modunu Denetleme](/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[Denetim grubu üyeliği](/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[IPsec Ana Modunu Denetleme](/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[IPsec Hızlı Modunu Denetleme](/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[Oturum Kapatmayı Denetleme](/windows/security/threat-protection/auditing/audit-logoff)</li><li>[Oturum Açmayı Denetleme](/windows/security/threat-protection/auditing/audit-logon)</li><li>[Ağ İlkesi Sunucusunu Denetleme](/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[Diğer oturum açma/kapatma olaylarını denetleme](/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[Özel Oturum Açmayı Denetleme](/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|Nesne erişimi| Denetimler, bir ağ veya bilgisayardaki belirli nesnelere veya nesne türlerine erişmeyi dener. Bu kategori aşağıdaki alt kategorileri içerir:<ul><li>[Oluşturulan Uygulamayı Denetleme](/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[Sertifika Hizmetlerini Denetleme](/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[Ayrıntılı Dosya Paylaşımını Denetleme](/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[Dosya Paylaşımını Denetleme](/windows/security/threat-protection/auditing/audit-file-share)</li><li>[Dosya sistemini denetleme](/windows/security/threat-protection/auditing/audit-file-system)</li><li>[Platform Bağlantısı Filtrelemeyi Denetleme](/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[Platform Paketi Bırakma Filtrelemesini Denetleme](/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[Denetim tanıtıcısı Işleme](/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[Çekirdek nesnesini denetle](/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[Diğer Nesne Erişim Olaylarını Denetleme](/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[Denetim kayıt defteri](/windows/security/threat-protection/auditing/audit-registry)</li><li>[Çıkarılabilir depolamayı denetle](/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[SAM Denetleme](/windows/security/threat-protection/auditing/audit-sam)</li><li>[Merkezi erişim Ilkesi hazırlamayı denetle](/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|İlke değişikliği|Yerel bir sistem veya ağ üzerindeki önemli güvenlik ilkelerine yapılan değişiklikleri denetler. İlkeler, genellikle ağ kaynaklarının güvenli hale getirilmesine yardımcı olmak için yöneticiler tarafından oluşturulur. Değişiklikleri izlemek veya bu ilkeleri değiştirme girişimleri, bir ağ için güvenlik yönetiminin önemli bir yönüdür. Bu kategori aşağıdaki alt kategorileri içerir:<ul><li>[İlke Değişimini Denetleme](/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[Kimlik Doğrulama İlkesi Değişikliğini Denetleme](/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[Yetkilendirme İlkesi Değişikliğini Denetleme](/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[Filtre Platformu İlke Değişikliğini Denetleme](/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[MPSSVC Kural Düzeyi İlke Değişikliğini Denetleme](/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[Diğer Ilke değişikliğini denetleme](/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|Ayrıcalık kullanımı| Bir veya daha fazla sistemde belirli izinlerin kullanımını denetler. Bu kategori aşağıdaki alt kategorileri içerir:<ul><li>[Hassas Olmayan Ayrıcalık Kullanımını Denetleme](/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[Hassas Ayrıcalık Kullanımını Denetleme](/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[Diğer Ayrıcalık Kullanım Olaylarını Denetleme](/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|Sistem| Diğer kategorilere dahil olmayan ve olası güvenlik etkilerine sahip olmayan bir bilgisayarda sistem düzeyindeki değişiklikleri denetler. Bu kategori aşağıdaki alt kategorileri içerir:<ul><li>[IPsec Sürücüsünü Denetleme](/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[Diğer Sistem Olaylarını Denetleme](/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[Güvenlik Durumu Değişikliğini Denetleme](/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[Güvenlik Sistemi Uzantısını Denetleme](/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[Sistem Bütünlüğünü Denetleme](/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>Kategori başına olay kimliği

 Azure AD DS Güvenlik denetimleri, belirli bir eylem denetlenebilir bir olay harekete geçirdiğinde aşağıdaki olay kimliklerini kaydeder:

| Olay kategorisi adı | Olay kimlikleri |
|:---|:---|
|Hesap oturum açma güvenliği|4767, 4774, 4775, 4776, 4777|
|Hesap yönetimi güvenliği|4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4730, 4731, 4732, 4733, 4734, 4735, 4737, 4738, 4740, 4741, 4742, 4743, 4754, 4755, 4756, 4757, 4758, 4764, 4765, 4766, 4780, 4781, 4782, 4793, 4798, 4799, 5376, 5377|
|Ayrıntı Izleme güvenliği|Yok|
|DS erişim güvenliği|5136, 5137, 5138, 5139, 5141|
|Logon-Logoff güvenliği|4624, 4625, 4634, 4647, 4648, 4672, 4675, 4964|
|Nesne erişim güvenliği|Yok|
|İlke değişikliği güvenliği|4670, 4703, 4704, 4705, 4706, 4707, 4713, 4715, 4716, 4717, 4718, 4719, 4739, 4864, 4865, 4866, 4867, 4904, 4906, 4911, 4912|
|Ayrıcalık kullanım güvenliği|4985|
|Sistem güvenliği|4612, 4621|

## <a name="next-steps"></a>Sonraki adımlar

Kusto hakkında belirli bilgiler için aşağıdaki makalelere bakın:

* Kusto sorgu diline [genel bakış](/azure/kusto/query/) .
* Sorgu temelleri hakkında bilgi edinmek için [kusto öğreticisi](/azure/kusto/query/tutorial) .
* Verilerinizi görmeniz için yeni yollar öğrenmenize yardımcı olan [örnek sorgular](/azure/kusto/query/samples) .
* Kusto başarılı olup olmadığını iyileştirmek için [en iyi uygulamalar](/azure/kusto/query/best-practices) .

<!-- LINKS - Internal -->
[migrate-azure-adds]: migrate-from-classic-vnet.md