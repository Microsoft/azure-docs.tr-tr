---
title: Azure portalında eylem gruplarını oluşturma ve yönetme
description: Azure portal eylem grupları oluşturmayı ve yönetmeyi öğrenin.
author: dkamstra
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: dukek
ms.openlocfilehash: 1486415c5d225163dd2b2c7e79cd008ad0a76588
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514878"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Azure portalında eylem gruplarını oluşturma ve yönetme
Bir eylem grubu, bir Azure aboneliğinin sahibi tarafından tanımlanan bildirim tercihleri koleksiyonudur. Azure Izleyici ve hizmet durumu uyarıları, kullanıcılara bir uyarının tetiklendiğini bildirmek için eylem gruplarını kullanır. Çeşitli uyarılar, kullanıcının gereksinimlerine bağlı olarak aynı eylem grubunu veya farklı eylem gruplarını kullanabilir. 

Bu makalede Azure portal eylem gruplarının nasıl oluşturulacağı ve yönetileceği gösterilmektedir.

Her eylem aşağıdaki özelliklerden oluşur:

* **Yazın**: bildirim veya eylem gerçekleştirildi. Bir sesli çağrı, SMS, e-posta gönderme örnekleri aşağıda verilmiştir. veya çeşitli otomatikleştirilmiş eylem türlerini tetikleyerek. Bu makalenin ilerleyen kısımlarında bulunan türlere bakın.
* **Ad**: eylem grubu içindeki benzersiz bir tanımlayıcı.
* **Ayrıntılar**: *türe* göre farklılık gösteren ilgili ayrıntılar.

Eylem gruplarını yapılandırmak için Azure Resource Manager şablonlarını kullanma hakkında daha fazla bilgi için bkz. [eylem grubu Kaynak Yöneticisi şablonları](./action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Azure portal kullanarak bir eylem grubu oluşturun

1. [Azure Portal](https://portal.azure.com), **izleme**' yi arayıp seçin. **İzleyici** bölmesi tüm izleme ayarlarınızı ve verilerinizi tek bir görünümde birleştirir.

1. **Uyarılar**' ı seçin ve ardından **eylemleri Yönet**' i seçin.

    ![Eylemleri Yönet düğmesi](./media/action-groups/manage-action-groups.png)
    
1. **Eylem grubu Ekle**' yi seçin ve sihirbaz deneyiminde ilgili alanları girin.

    !["Eylem grubu Ekle" komutu](./media/action-groups/add-action-group.PNG)

### <a name="configure-basic-action-group-settings"></a>Temel eylem grubu ayarlarını yapılandırma

**Proje ayrıntıları**:

Eylem grubunun kaydedildiği **aboneliği** ve **kaynak grubunu** seçin.

**Örnek ayrıntıları**:

1. Bir **eylem grubu adı** girin.

1. Bir **görünen ad** girin. Görünen ad, bildirimler bu grup kullanılarak gönderildiğinde tam bir eylem grubu adı yerine kullanılır.

      ![Eylem grubu Ekle "iletişim kutusu](./media/action-groups/action-group-1-basics.png)


### <a name="configure-notifications"></a>Bildirimleri yapılandırma

1. **Bildirimler** sekmesine gitmek için **ileri: bildirimler >** düğmesine tıklayın veya ekranın üst kısmındaki **Bildirimler** sekmesini seçin.

1. Bir uyarı tetiklendiğinde gönderilecek bildirimlerin listesini tanımlayın. Her bildirim için aşağıdakileri sağlayın:

    a. **Bildirim türü**: göndermek istediğiniz bildirim türünü seçin. Şu seçenekler sağlanır:
      * E-posta Azure Resource Manager rolü-belirli bir abonelik düzeyi ARM rolüne atanan kullanıcılara bir e-posta gönderin.
      * E-posta/SMS/Push/Voice-bu bildirim türlerini belirli alıcılara gönderir.
    
    b. **Ad**: bildirim için benzersiz bir ad girin.

    c. **Ayrıntılar**: seçili bildirim türüne göre bir e-posta adresi, telefon numarası vb. girin.
    
    d. **Ortak uyarı şeması**: Azure izleyici 'deki tüm uyarı hizmetlerinde tek bir Genişletilebilir ve birleştirilmiş uyarı yüküne sahip olmanın avantajını sağlayan [ortak uyarı şemasını](./alerts-common-schema.md)etkinleştirmeyi seçebilirsiniz.

    ![Bildirimler sekmesi](./media/action-groups/action-group-2-notifications.png)
    
### <a name="configure-actions"></a>Eylemleri yapılandırma

1. **Eylemler** sekmesine gitmek için **ileri: eylemler >** düğmesine tıklayın veya ekranın üst kısmındaki **Eylemler** sekmesini seçin.

1. Bir uyarı tetiklendiğinde tetiklenecek eylemlerin listesini tanımlayın. Her eylem için aşağıdakileri sağlayın:

    a. **Eylem türü**: Otomasyon Runbook 'U, Azure IşLEVI, ITSM, mantıksal uygulama, güvenli Web kancası, Web kancası 'yi seçin.
    
    b. **Ad**: eylem için benzersiz bir ad girin.

    c. **Ayrıntılar**: eylem türüne bağlı olarak, bir Web kancası URI 'Si, Azure UYGULAMASı, ITSM bağlantısı veya Otomasyon Runbook 'u girin. ITSM eylemi için, ayrıca ıTSM araclarınızın gerektirdiği **Iş öğesini** ve diğer alanları belirtin.
    
    d. **Ortak uyarı şeması**: Azure izleyici 'deki tüm uyarı hizmetlerinde tek bir Genişletilebilir ve birleştirilmiş uyarı yüküne sahip olmanın avantajını sağlayan [ortak uyarı şemasını](./alerts-common-schema.md)etkinleştirmeyi seçebilirsiniz.
    
    ![Eylemler sekmesi](./media/action-groups/action-group-3-actions.png)

### <a name="create-the-action-group"></a>Eylem grubunu oluşturma

1. Dilerseniz **Etiketler** ayarlarını da inceleyebilirsiniz. Bu, anahtar/değer çiftlerini kategorilere ayırma için eylem grubuyla ilişkilendirmenize ve tüm Azure kaynakları için kullanılabilen bir özelliktir.

    ![Etiketler sekmesi](./media/action-groups/action-group-4-tags.png)
    
1. Ayarları gözden geçirmek için **Gözden geçir + oluştur**'a tıklayın. Bu, tüm gerekli alanların seçili olduğundan emin olmak için girişlerinizin hızlı bir şekilde doğrulanmasını sağlar. Sorun varsa burada bildirilir. Ayarları inceledikten sonra, eylem grubunu sağlamak için **Oluştur** ' a tıklayın.
    
    ![Gözden geçir + Oluştur sekmesi](./media/action-groups/action-group-5-review.png)

> [!NOTE]
> E-posta veya SMS ile bir kişiye bildirimde bulunan bir eylem yapılandırdığınızda, bunlar eylem grubuna eklendiğini belirten bir onay alırlar.

## <a name="manage-your-action-groups"></a>Eylem gruplarınızı yönetin

Bir eylem grubu oluşturduktan sonra, **izleme** bölmesinde **Uyarılar** giriş sayfasından **eylemleri Yönet** ' i seçerek **eylem gruplarını** görüntüleyebilirsiniz. Yönetmek istediğiniz eylem grubunu seçin:

* Eylemleri ekleyin, düzenleyin veya kaldırın.
* Eylem grubunu silin.

## <a name="action-specific-information"></a>Eyleme özgü bilgiler

> [!NOTE]
> Aşağıdaki öğelerin her birinde sayısal limitleri [izlemek Için abonelik hizmeti sınırlarına](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-monitor-limits) bakın.  

### <a name="automation-runbook"></a>Otomasyon Runbook'u
Runbook yüklerinin sınırları için [Azure abonelik hizmeti sınırlarına](../../azure-resource-manager/management/azure-subscription-service-limits.md) bakın.

Bir eylem grubunda sınırlı sayıda runbook eylemi olabilir. 

### <a name="azure-app-push-notifications"></a>Azure Uygulama anında Iletme bildirimleri
Azure mobil uygulaması yapılandırırken hesap KIMLIĞINIZ olarak kullandığınız e-posta adresini girerek [Azure mobil uygulaması](https://azure.microsoft.com/features/azure-portal/mobile-app/) anında iletme bildirimleri etkinleştirin.

Bir eylem grubunda sınırlı sayıda Azure uygulama eylemi olabilir.

### <a name="email"></a>E-posta
E-postalar aşağıdaki e-posta adreslerinden gönderilir. E-posta filtrelemesinin uygun şekilde yapılandırıldığından emin olun
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Bir eylem grubunda sınırlı sayıda e-posta eylemi olabilir. Bkz. [hız sınırlandırma bilgileri](./alerts-rate-limiting.md) makalesi.

### <a name="email-azure-resource-manager-role"></a>Azure Resource Manager Rolüne E-posta Gönder
Abonelik rolü üyelerine e-posta gönderin. E-posta yalnızca rolün **Azure AD Kullanıcı** üyelerine gönderilir. Azure AD gruplarına veya hizmet sorumlularına e-posta gönderilmez.

Yalnızca *birincil e-posta* adresine bir bildirim e-postası gönderilir.

*Birincil e-postanıza* bildirim aldıysanız aşağıdaki adımları deneyebilirsiniz:

1. Azure portal *Active Directory* gidin.
2. Tüm kullanıcılar ' a (sol bölmede) tıklayın, Kullanıcı listesini (sağ bölmede) görürsünüz.
3. *Birincil e-posta* bilgilerini gözden geçirmek istediğiniz kullanıcıyı seçin.

  :::image type="content" source="media/action-groups/active-directory-user-profile.png" alt-text="Kullanıcı profilini İnceleme hakkında örnek." border="true":::

4. "E-posta" sekmesi boş ise, Iletişim bilgileri altındaki Kullanıcı profili ' nde, üstteki *Düzenle* düğmesine tıklayın ve *birincil e-postanızı* ekleyin ve en üstteki *Kaydet* düğmesine basın.

  :::image type="content" source="media/action-groups/active-directory-add-primary-email.png" alt-text="Birincil e-postanın nasıl ekleneceği hakkında örnek." border="true":::

Bir eylem grubunda sınırlı sayıda e-posta eylemi olabilir. Bkz. [hız sınırlandırma bilgileri](./alerts-rate-limiting.md) makalesi.

*E-posta ARM rolünü* ayarlarken 3 koşulun karşılandığından emin olmanız gerekir:

1. Role atanan varlığın türünün **"user"** olması gerekir.
2. Atamanın **abonelik** düzeyinde yapılması gerekir.
3. Kullanıcının **AAD profilinde** yapılandırılmış bir e-posta olması gerekir. 


### <a name="function"></a>İşlev
[Azure işlevlerinde](../../azure-functions/functions-get-started.md)var olan bir http tetikleyici uç noktasını çağırır. Bir isteği işlemek için uç noktanızın HTTP POST fiilini işlemesi gerekir.

Bir eylem grubunda sınırlı sayıda Işlev eylemine sahip olabilirsiniz.

### <a name="itsm"></a>ITSM
ITSM eylemi bir ıTSM bağlantısı gerektiriyor. [ITSM bağlantısı](./itsmc-overview.md)oluşturmayı öğrenin.

Bir eylem grubunda sınırlı sayıda ıSM eylemi olabilir. 

### <a name="logic-app"></a>Logic App
Bir eylem grubunda sınırlı sayıda mantıksal uygulama eylemi olabilir.

### <a name="secure-webhook"></a>Güvenli Web Kancası
Eylem grupları güvenli Web kancası eylemi, eylem grubunuz ve korumalı Web API 'niz (Web kancası uç noktası) arasındaki bağlantıyı güvenli hale getirmek için Azure Active Directory avantajlarından yararlanmanızı sağlar. Bu işlevden yararlanmak için genel iş akışı aşağıda açıklanmıştır. Azure AD uygulamalarına ve hizmet sorumlularına genel bakış için bkz. [Microsoft Identity platform (v 2.0) genel bakış](../../active-directory/develop/v2-overview.md).

> [!NOTE]
> Web kancası eyleminin kullanılması için hedef Web kancası uç noktasının, uyarının ayrıntılarının başarıyla çalışması gerekmez veya POST işleminin bir parçası olarak sağlanmış olan uyarı bağlamı bilgilerini ayrıştırmaktan emin olmanız gerekir. Web kancası uç noktası, uyarı bağlamı bilgilerini kendi kendine işleyemez, Web kancasının beklenen veri biçimiyle eşleşecek şekilde uyarı bağlamı bilgilerini özel bir şekilde işlemek için bir [mantıksal uygulama eylemi](./action-groups-logic-app.md) gibi bir çözüm kullanabilirsiniz.

1. Korumalı Web API 'niz için bir Azure AD uygulaması oluşturun. Bkz. [korumalı Web API 'si: uygulama kaydı](../../active-directory/develop/scenario-protected-web-api-app-registration.md).
    - Korumalı API 'nizi [bir Daemon uygulaması tarafından çağrılacak](../../active-directory/develop/scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app)şekilde yapılandırın.
    
2. Azure AD uygulamanızı kullanmak için eylem gruplarını etkinleştirin.

    > [!NOTE]
    > Bu betiği yürütmek için [Azure AD uygulama Yöneticisi rolünün](../../active-directory/roles/permissions-reference.md#all-roles) bir üyesi olmanız gerekir.
    
    - PowerShell betiğinin Connect-AzureAD çağrısını Azure AD kiracı KIMLIĞINIZI kullanacak şekilde değiştirin.
    - PowerShell betiğinin değişken $myAzureADApplicationObjectId Azure AD uygulamanızın nesne KIMLIĞINI kullanacak şekilde değiştirin.
    - Değiştirilen betiği çalıştırın.
    
3. Eylem grubu güvenli Web kancası eylemini yapılandırın.
    - Betikten $myApp. ObjectID değerini kopyalayın ve Web kancası eylem tanımındaki uygulama nesne KIMLIĞI alanına girin.
    
    ![Güvenli Web kancası eylemi](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>Güvenli Web kancası PowerShell betiği

```PowerShell
Connect-AzureAD -TenantId "<provide your Azure AD tenant ID here>"
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object ID of your Azure AD Application>"
    
# This is the Action Groups Azure AD AppId
$actionGroupsAppId = "461e8683-5575-4561-ac7f-899cc907d62a"
    
# This is the name of the new role we will add to your Azure AD Application
$actionGroupRoleName = "ActionGroupsSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
    
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles
$actionGroupsSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $actionGroupsAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match "ActionGroupsSecureWebhook")
{
    Write-Host "The Action Groups role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $actionGroupRoleName -Description "This is a role for Action Groups to join"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($actionGroupsSP -match "AzNS AAD Webhook")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the Action Groups Azure AD Application and add it to the role
    $actionGroupsSP = New-AzureADServicePrincipal -AppId $actionGroupsAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $actionGroupsSP.ObjectId -PrincipalId $actionGroupsSP.ObjectId
    
Write-Host "My Azure AD Application (ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```

### <a name="sms"></a>SMS
Ek önemli bilgiler için bkz. bilgi ve [SMS uyarı davranışını](./alerts-sms-behavior.md) [sınırlandırma](./alerts-rate-limiting.md) . 

Bir eylem grubunda sınırlı sayıda SMS eylemi olabilir.

> [!NOTE]
> Azure portal eylem grubu Kullanıcı arabirimi ülke/bölge kodunuzu seçmenizi vermezse, ülkeniz/bölgeniz için SMS desteklenmez.  Ülke/Bölge kodunuz yoksa, ülkeniz/bölgenizin [Kullanıcı sesine](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice)eklenmesini sağlayabilirsiniz. Bu sırada, geçici bir çözüm, eylem grubunuzun bir üçüncü taraf SMS sağlayıcısına ait bir Web kancasını ülkeniz/bölgeniz desteğiyle çağırmasını sağlar.  

Desteklenen ülkeler/bölgeler için fiyatlandırma, [Azure izleyici fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/monitor/)listelenmiştir.

**SMS bildiriminin desteklendiği ülkelerin listesi**

| Ülke Kodu | Ülke Adı |
|:---|:---|
| 61 | Avustralya |
| 43 | Avusturya |
| 32 | Belçika |
| 55 | Brezilya |
| 1 |Kanada |
| 56 | Şili |
| 86 | Çin |
| 420 | Çek Cumhuriyeti |
| 45 | Danimarka |
| 372 | Estonya |
| 358 | Finlandiya |
| 33 | Fransa |
| 49 | Almanya |
| 852 | Hong Kong |
| 91 | Hindistan |
| 353 | İrlanda |
| 972 | İsrail |
| 39 | İtalya |
| 81 | Japonya |
| 352 | Lüksemburg |
| 60 | Malezya |
| 52 | Meksika |
| 31 | Hollanda |
| 64 | Yeni Zelanda |
| 47 | Norveç |
| 351 | Portekiz |
| 1 | Porto Riko |
| 40 | Romanya |
| 65 | Singapur |
| 27 | Güney Afrika |
| 82 | Güney Kore |
| 34 | İspanya |
| 41 | İsviçre |
| 886 | Tayvan |
| 44 | Birleşik Krallık |
| 1 | Birleşik Devletler |

### <a name="voice"></a>Ses
Daha önemli davranış için bkz. [hız sınırlandırma bilgileri](./alerts-rate-limiting.md) makalesi.

Bir eylem grubunda sınırlı sayıda ses eylemi olabilir.

> [!NOTE]
> Azure portal eylem grubu Kullanıcı arabirimi ülke/bölge kodunuzu seçmenizi desteklemiyorsa, ülkeniz/bölgeniz için sesli çağrılar desteklenmez. Ülke/Bölge kodunuz yoksa, ülkeniz/bölgenizin [Kullanıcı sesine](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice)eklenmesini sağlayabilirsiniz.  Bu sırada, geçici bir çözüm, eylem grubunuzun bir üçüncü taraf sesli çağrı sağlayıcısına ait bir Web kancasını ülke/bölgenizde destekleyecek şekilde çağırmasını sağlar.  
> Yalnızca ses bildirimi için Azure portal eylem grubunda bugün desteklenen ülke kodu + 1 ' dir (Birleşik Devletler). 

Desteklenen ülkeler/bölgeler için fiyatlandırma, [Azure izleyici fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/monitor/)listelenmiştir.

### <a name="webhook"></a>Web Kancası

> [!NOTE]
> Web kancası eyleminin kullanılması için hedef Web kancası uç noktasının, uyarının ayrıntılarının başarıyla çalışması gerekmez veya POST işleminin bir parçası olarak sağlanmış olan uyarı bağlamı bilgilerini ayrıştırmaktan emin olmanız gerekir. Web kancası uç noktası, uyarı bağlamı bilgilerini kendi kendine işleyemez, Web kancasının beklenen veri biçimiyle eşleşecek şekilde uyarı bağlamı bilgilerini özel bir şekilde işlemek için bir [mantıksal uygulama eylemi](./action-groups-logic-app.md) gibi bir çözüm kullanabilirsiniz.

Web kancaları aşağıdaki kurallar kullanılarak işlenir
- Bir Web kancası çağrısı en fazla 3 kez denendi.
- Zaman aşımı süresi içinde bir yanıt alınmadığında veya aşağıdaki HTTP durum kodlarından biri döndürülürse, çağrı yeniden denenir: 408, 429, 503 veya 504.
- İlk çağrı bir yanıt için 10 saniye bekler.
- İkinci ve üçüncü denemeler, yanıt için 30 saniye bekler.
- Web kancasını çağırma 3 girişimi başarısız olduysa, hiçbir eylem grubu 15 dakika boyunca bitiş noktasını çağırmaz.

Lütfen kaynak IP adresi aralıkları için [eylem grubu IP adresleri](../app/ip-addresses.md) bölümüne bakın.


## <a name="next-steps"></a>Sonraki adımlar
* [SMS uyarı davranışı](./alerts-sms-behavior.md)hakkında daha fazla bilgi edinin.  
* [Etkinlik günlüğü uyarısı Web kancası şemasının anlaşılmasını](./activity-log-alerts-webhook.md)elde edin.  
* [ITSM Bağlayıcısı](./itsmc-overview.md)hakkında daha fazla bilgi edinin.
* Uyarıların [hız sınırlaması](./alerts-rate-limiting.md) hakkında daha fazla bilgi edinin.
* [Etkinlik günlüğü uyarılarına genel bir bakış](./alerts-overview.md)elde edin ve uyarıları alma hakkında bilgi edinin.  
* [Bir hizmet durumu bildirimi gönderildiğinde uyarıların nasıl yapılandırılacağını](../../service-health/alerts-activity-log-service-notifications-portal.md)öğrenin.
