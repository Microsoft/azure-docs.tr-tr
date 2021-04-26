---
title: Azure Pipelines ile özel ilkeler dağıtma
titleSuffix: Azure AD B2C
description: Azure DevOps Services Azure Pipelines kullanarak bir CI/CD işlem hattındaki Azure AD B2C özel ilkeleri dağıtmayı öğrenin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 913f21b90043209cae1ec9963619389bcb452781
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529437"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>Azure Pipelines ile özel ilkeler dağıtma

[Azure Pipelines][devops-pipelines]' de ayarladığınız bir sürekli tümleştirme ve teslım (CI/CD) işlem hattı kullanarak, yazılım teslimine ve kod denetim otomasyonuna Azure AD B2C özel ilkelerinizi dahil edebilirsiniz. Geliştirme, test ve üretim gibi farklı Azure AD B2C ortamlara dağıtırken, el ile işlemleri kaldırmanızı ve Azure Pipelines kullanarak otomatikleştirilmiş test gerçekleştirmenizi öneririz.

Azure AD B2C içindeki özel ilkeleri yönetmek için Azure Pipelines etkinleştirilmesi gereken üç temel adım vardır:

1. Azure AD B2C kiracınızda bir Web uygulaması kaydı oluşturma
1. Azure deposu yapılandırma
1. Azure işlem hattı yapılandırma

> [!IMPORTANT]
> Azure işlem hattı ile Azure AD B2C özel ilkeleri yönetmek Şu anda Microsoft Graph API uç noktasında kullanılabilen **Önizleme** işlemlerini kullanıyor `/beta` . Üretim uygulamalarında bu API 'lerin kullanılması desteklenmez. Daha fazla bilgi için [Microsoft Graph REST API Beta uç nokta başvurusuna](/graph/api/overview?toc=.%2fref%2ftoc.json&view=graph-rest-beta&preserve-view=true)bakın.

## <a name="prerequisites"></a>Önkoşullar

* [B2C ıEF Ilke Yöneticisi](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator) rolüyle dizindeki bir kullanıcı için [Azure AD B2C kiracı](tutorial-create-tenant.md)ve kimlik bilgileri
* Kiracınıza yüklenen [özel ilkeler](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
* [Yönetim uygulaması](microsoft-graph-get-started.md) kiracınızda Microsoft Graph API izin *ilkesi. ReadWrite. TrustFramework* ile kaydedildi
* [Azure Işlem hattı](https://azure.microsoft.com/services/devops/pipelines/)ve bir [Azure DevOps Services projesine][devops-create-project] erişim

## <a name="client-credentials-grant-flow"></a>İstemci kimlik bilgileri verme akışı

Burada açıklanan senaryo, OAuth 2,0 [istemci kimlik bilgileri verme akışını](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)kullanarak Azure Pipelines ve Azure AD B2C arasındaki hizmetten hizmete yapılan çağrıların kullanımını sağlar. Bu verme akışı, Azure Pipelines (gizli istemci) gibi bir Web hizmetinin başka bir Web hizmetini çağırırken kimlik doğrulaması yapmak yerine kendi kimlik bilgilerini kullanmasına izin verir (Bu durumda, Microsoft Graph API 'SI). Azure Pipelines, etkileşimli olmayan bir belirteci edinir ve sonra Microsoft Graph API 'sine istek yapar.

## <a name="register-an-application-for-management-tasks"></a>Yönetim görevleri için bir uygulamayı kaydetme

[Önkoşullardan](#prerequisites)bahsedildiği gibi, PowerShell betiklerinizin (Azure Pipelines tarafından yürütülen), kiracınızdaki kaynaklara erişmek için kullanabileceği bir uygulama kaydına ihtiyacınız vardır.

Otomasyon görevleri için kullandığınız bir uygulama kaydınız zaten varsa,   >    >  uygulama kaydının **API izinleri** içinde Microsoft Graph Policy **Policy. ReadWrite. TrustFramework** izninin verildiğinden emin olun.

Bir yönetim uygulamasını kaydetme hakkında yönergeler için bkz. [Microsoft Graph Azure AD B2C yönetme](microsoft-graph-get-started.md).

## <a name="configure-an-azure-repo"></a>Azure deposu yapılandırma

Kayıtlı bir yönetim uygulaması ile, ilke dosyalarınız için bir depo yapılandırmaya hazırsınız demektir.

1. Azure DevOps Services kuruluşunuzda oturum açın.
1. [Yeni bir proje oluşturun][devops-create-project] veya var olan bir projeyi seçin.
1. Projenizde, **Repos** ' a gidin ve **dosyalar** sayfasını seçin. Mevcut bir depoyu seçin veya bu alıştırma için bir tane oluşturun.
1. *B2CAssets* adlı bir klasör oluşturun. Gerekli yer tutucu dosyasını *README.MD* olarak adlandırın ve dosyayı **işleyin** . İsterseniz bu dosyayı daha sonra kaldırabilirsiniz.
1. Azure AD B2C ilkesi dosyalarınızı *B2CAssets* klasörüne ekleyin. Bu, *TrustFrameworkBase.xml*, *TrustFrameWorkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml* ve oluşturduğunuz diğer ilkeleri içerir. Daha sonraki bir adımda kullanmak üzere her bir Azure AD B2C ilkesi dosyasının dosya adını kaydedin (PowerShell betiği bağımsız değişkenleri olarak kullanılırlar).
1. Deponun kök dizininde *betikler* adlı bir klasör oluşturun, yer tutucu dosyasını *DeployToB2c.ps1* olarak adlandırın. Bu noktada dosyayı kaydetme, daha sonraki bir adımda yapacaksınız.
1. Aşağıdaki PowerShell betiğini *DeployToB2c.ps1* yapıştırın, sonra dosyayı **yürütün** . Betik, Azure AD 'den bir belirteç alır ve *B2CAssets* klasörünün içindeki ilkeleri Azure AD B2C kiracınıza yüklemek IÇIN Microsoft Graph API 'sini çağırır.

    ```PowerShell
    [Cmdletbinding()]
    Param(
        [Parameter(Mandatory = $true)][string]$ClientID,
        [Parameter(Mandatory = $true)][string]$ClientSecret,
        [Parameter(Mandatory = $true)][string]$TenantId,
        [Parameter(Mandatory = $true)][string]$PolicyId,
        [Parameter(Mandatory = $true)][string]$PathToFile
    )

    try {
        $body = @{grant_type = "client_credentials"; scope = "https://graph.microsoft.com/.default"; client_id = $ClientID; client_secret = $ClientSecret }

        $response = Invoke-RestMethod -Uri https://login.microsoftonline.com/$TenantId/oauth2/v2.0/token -Method Post -Body $body
        $token = $response.access_token

        $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
        $headers.Add("Content-Type", 'application/xml')
        $headers.Add("Authorization", 'Bearer ' + $token)

        $graphuri = 'https://graph.microsoft.com/beta/trustframework/policies/' + $PolicyId + '/$value'
        $policycontent = Get-Content $PathToFile
        $response = Invoke-RestMethod -Uri $graphuri -Method Put -Body $policycontent -Headers $headers

        Write-Host "Policy" $PolicyId "uploaded successfully."
    }
    catch {
        Write-Host "StatusCode:" $_.Exception.Response.StatusCode.value__

        $_

        $streamReader = [System.IO.StreamReader]::new($_.Exception.Response.GetResponseStream())
        $streamReader.BaseStream.Position = 0
        $streamReader.DiscardBufferedData()
        $errResp = $streamReader.ReadToEnd()
        $streamReader.Close()

        $ErrResp

        exit 1
    }

    exit 0
    ```

## <a name="configure-your-azure-pipeline"></a>Azure işlem hattınızı yapılandırma

Deponuz başlatılmış ve özel ilke dosyalarınıza doldurulduktan sonra yayın işlem hattını ayarlamaya hazırsınız demektir.

### <a name="create-pipeline"></a>İşlem hattı oluşturma

1. Azure DevOps Services kuruluşunuzda oturum açın ve projenize gidin.
1. Projenizde işlem **hatları**  >    >  **Yeni işlem hattı**' nı seçin.
1. **Şablon seç** altında **boş iş**' ı seçin.
1. Bir **aşama adı** girin, örneğin *DeployCustomPolicies*, sonra bölmeyi kapatın.
1. **Yapıt Ekle**' yi seçin ve **kaynak türü** altında **Azure deposu**' nu seçin.
    1. PowerShell betiği ile doldurulmuş *betikler* klasörünü içeren kaynak depoyu seçin.
    1. Varsayılan bir **dal** seçin. Önceki bölümde yeni bir depo oluşturduysanız, varsayılan dal *ana öğe* olur.
    1. Varsayılan daldan varsayılan **Sürüm** ayarını *en son* bırakın.
    1. Depo için bir **kaynak diğer adı** girin. Örneğin, *policydeposu*. Diğer ada boşluk eklemeyin.
1. **Ekle**’yi seçin
1. İşlem hattını, amacını yansıtacak şekilde yeniden adlandırın. Örneğin, *özel ilke Işlem hattı dağıtın*.
1. İşlem hattı yapılandırmasını kaydetmek için **Kaydet** ' i seçin.

### <a name="configure-pipeline-variables"></a>İşlem hattı değişkenlerini yapılandırma

1. **Değişkenler** sekmesini seçin.
1. Aşağıdaki değişkenleri **ardışık düzen değişkenleri** altına ekleyin ve değerlerini belirtilen şekilde ayarlayın:

    | Name | Değer |
    | ---- | ----- |
    | `clientId` | Daha önce kaydettiğiniz uygulamanın **uygulama (istemci) kimliği** . |
    | `clientSecret` | Daha önce oluşturduğunuz **istemci parolasının** değeri. <br /> Değişken türünü **gizli** olarak değiştirin (kilit simgesini seçin). |
    | `tenantId` | `your-b2c-tenant.onmicrosoft.com`, *-B2C-kiracınız* Azure AD B2C kiracınızın adıdır. |

1. Değişkenleri kaydetmek için **Kaydet** ' i seçin.

### <a name="add-pipeline-tasks"></a>İşlem hattı görevleri ekleme

Sonra, bir ilke dosyası dağıtmak için bir görev ekleyin.

1. **Görevler** sekmesini seçin.
1. **Aracı** işini seçin ve ardından **+** Aracı işine bir görev eklemek için artı işaretini () seçin.
1. **PowerShell**'i arayın ve seçin. "Azure PowerShell," "hedef makinelerde PowerShell" veya başka bir PowerShell girişi seçmeyin.
1. Yeni eklenen **PowerShell betiği** görevi ' ni seçin.
1. PowerShell betiği görevi için aşağıdaki değerleri girin:
    * **Görev sürümü**: 2. *
    * **Görünen ad**: Bu görevin karşıya yüklenmesi gereken ilkenin adı. Örneğin, *B2C_1A_TrustFrameworkBase*.
    * **Tür**: dosya yolu
    * **Betik yolu**: üç noktayı seçin (**_..._* _) _Scripts * klasörüne gidin ve *DeployToB2C.ps1* dosyasını seçin.
    * **Değişkenlerinden**

        **Bağımsız değişkenler** için aşağıdaki değerleri girin. `{alias-name}`Önceki bölümde belirttiğiniz diğer adla değiştirin.

        ```PowerShell
        # Before
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/{alias-name}/B2CAssets/TrustFrameworkBase.xml
        ```

        Örneğin, belirttiğiniz diğer ad *Policydeppo* ise, bağımsız değişken satırı şu şekilde olmalıdır:

        ```PowerShell
        # After
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/policyRepo/B2CAssets/TrustFrameworkBase.xml
        ```

1. Aracı işini kaydetmek için **Kaydet** ' i seçin.

Yeni eklediğiniz görev Azure AD B2C *bir* ilke dosyası yükler. Devam etmeden önce, ek görevler oluşturmadan önce başarıyla tamamlandığından emin olmak için işi el ile tetikleyin (**yayın oluşturun**).

Görev başarıyla tamamlanırsa, özel ilke dosyalarının her biri için önceki adımları gerçekleştirerek dağıtım görevleri ekleyin. `-PolicyId` `-PathToFile` Her ilke için ve bağımsız değişken değerlerini değiştirin.

, `PolicyId` TrustFrameworkPolicy düğümündeki BIR XML ilke dosyasının başlangıcında bulunan bir değerdir. Örneğin, `PolicyId` aşağıdaki POLICY XML dosyasında *B2C_1A_TrustFrameworkBase*:

```xml
<TrustFrameworkPolicy
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:xsd="http://www.w3.org/2001/XMLSchema"
xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
PolicySchemaVersion="0.3.0.0"
TenantId="contoso.onmicrosoft.com"
PolicyId= "B2C_1A_TrustFrameworkBase"
PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

Aracıları çalıştırırken ve ilke dosyalarını karşıya yüklerken, bu sırada karşıya yüklendiklerinden emin olun:

1. *TrustFrameworkBase.xml*
1. *TrustFrameworkExtensions.xml*
1. *SignUpOrSignin.xml*
1. *ProfileEdit.xml*
1. *PasswordReset.xml*

Dosya yapısı hiyerarşik bir zincirde oluşturulduğu için kimlik deneyimi çerçevesi bu sırayı zorlar.

## <a name="test-your-pipeline"></a>İşlem hattınızı test etme

Yayın işlem hattınızı test etmek için:

1. İşlem **hatları** ve sonra **yayınlar**' ı seçin.
1. Daha önce oluşturduğunuz işlem hattını seçin, örneğin *DeployCustomPolicies*.
1. **Yayın oluştur**' u seçin ve sonra yayını kuyruğa almak için **Oluştur** ' u seçin.

Bir yayının sıraya alınmış olduğunu belirten bir bildirim başlığı görmeniz gerekir. Durumunu görüntülemek için bildirim başlığından bağlantıyı seçin ya da **yayınlar** sekmesindeki listeden seçin.

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdakiler hakkında daha fazla bilgi edinin:

* [İstemci kimlik bilgilerini kullanan hizmetten hizmete çağrılar](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)
* [Azure DevOps Services](/azure/devops/user-guide/)

<!-- LINKS - External -->
[devops]: /azure/devops/
[devops-create-project]:  /azure/devops/organizations/projects/create-project
[devops-pipelines]: /azure/devops/pipelines
