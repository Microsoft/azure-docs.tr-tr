---
title: Web kancasından Azure Otomasyonu runbook 'u başlatma
description: Bu makalede, Azure Otomasyonu 'nda bir runbook 'u HTTP çağrısından başlatmak için Web kancasının nasıl kullanılacağı açıklanır.
services: automation
ms.subservice: process-automation
ms.date: 03/18/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 338fb56c4af5c24b7b746ffd6508c2fe7d52b131
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830205"
---
# <a name="start-a-runbook-from-a-webhook"></a>Web kancasından runbook başlatma

Web kancası, bir dış hizmetin Azure Otomasyonu 'nda belirli bir runbook 'u tek bir HTTP isteği aracılığıyla başlatmasını sağlar. Dış hizmetler Azure DevOps Services, GitHub, Azure Izleyici günlükleri ve özel uygulamalar içerir. Bu tür bir hizmet, tam Azure Otomasyonu API 'sini uygulamadan bir runbook 'u başlatmak için Web kancasını kullanabilir. Web kancalarını, [Azure Otomasyonu 'nda bir runbook başlatma](./start-runbooks.md)bölümünde bulunan bir runbook 'u başlatma yöntemleriyle karşılaştırabilirsiniz.

> [!NOTE]
> Python runbook 'u başlatmak için Web kancası kullanılması desteklenmez.

![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

Web kancalarıyla TLS 1,2 için istemci gereksinimlerini anlamak için bkz. [Azure Otomasyonu Için tls 1,2 zorlaması](automation-managing-data.md#tls-12-enforcement-for-azure-automation).

## <a name="webhook-properties"></a>Web kancası özellikleri

Aşağıdaki tabloda bir Web kancası için yapılandırmanız gereken özellikler açıklanmaktadır.

| Özellik | Açıklama |
|:--- |:--- |
| Ad |Web kancasının adı. İstemciye açık olmadığından istediğiniz adı verebilirsiniz. Yalnızca Azure Otomasyonu 'nda runbook 'u tanımlamak için kullanılır. En iyi uygulama olarak, Web kancasına onu kullanan istemciyle ilgili bir ad vermeniz gerekir. |
| URL |Web kancasının URL 'SI. Bu, bir istemcinin Web kancasına bağlı olan runbook 'u başlatmak için bir HTTP POST ile çağırdığı benzersiz adrestir. Web kancasını oluşturduğunuzda otomatik olarak oluşturulur. Özel bir URL belirtemezsiniz. <br> <br> URL, üçüncü taraf bir sistemin runbook 'u başka bir kimlik doğrulaması olmadan çağırmasına izin veren bir güvenlik belirteci içeriyor. Bu nedenle, URL 'YI bir parola gibi kabul etmelisiniz. Güvenlik nedenleriyle, URL 'YI yalnızca Web kancasını oluştururken Azure portal görüntüleyebilirsiniz. Daha sonra kullanılmak üzere güvenli bir konumda URL 'YI aklınızda bulundurma. |
| Son kullanma tarihi | Web kancasının, daha sonra kullanılamayacak son kullanma tarihi. Web kancasının süresi dolduğu sürece, Web kancası oluşturulduktan sonra sona erme tarihini değiştirebilirsiniz. |
| Etkin | Web kancasının oluşturulduğu zaman varsayılan olarak etkinleştirilip etkinleştirilmediğini belirten ayar. Bu özelliği devre dışı olarak ayarlarsanız, hiçbir istemci Web kancasını kullanamaz. Bu özelliği, Web kancasını veya oluşturulduktan sonra herhangi bir zamanı oluşturduğunuzda ayarlayabilirsiniz. |

## <a name="parameters-used-when-the-webhook-starts-a-runbook"></a>Web kancası runbook 'u başlattığında kullanılan parametreler

Web kancası runbook 'un başlatıldığı zaman kullanılan runbook parametreleri için değerler tanımlayabilir. Web kancası zorunlu runbook parametrelerinin değerlerini içermeli ve isteğe bağlı parametrelerin değerlerini içerebilir. Web kancasına yapılandırılmış bir parametre değeri, Web kancası oluşturulduktan sonra bile değiştirilebilir. Tek bir runbook 'a bağlı birden çok Web kancası, her biri farklı runbook parametre değerleri kullanabilir. İstemci bir Web kancası kullanarak runbook 'u başlattığında, Web kancasında tanımlanan parametre değerlerini geçersiz kılamazsınız.

İstemciden veri almak için Runbook adlı tek bir parametreyi destekler `WebhookData` . Bu parametre, bir POST isteğinde istemcinin içerdiği verileri içeren bir nesneyi tanımlar.

![Web kancası veri özellikleri](media/automation-webhooks/webhook-data-properties.png)

`WebhookData`Parametresi aşağıdaki özelliklere sahiptir:

| Özellik | Açıklama |
|:--- |:--- |
| `WebhookName` | Web kancasının adı. |
| `RequestHeader` | Gelen POST isteğinin üst bilgilerini içeren Hashtable. |
| `RequestBody` | Gelen POST isteğinin gövdesi. Bu gövde dize, JSON, XML veya form kodlu gibi tüm veri biçimlendirmelerini korur. Runbook 'un beklenen veri biçimiyle çalışacak şekilde yazılması gerekir. |

Parametresi desteklemek için gereken Web kancası yapılandırması yoktur `WebhookData` ve Runbook 'un kabul etmesi gerekmez. Runbook parametreyi tanımlamaz, istemciden gönderilen isteğin ayrıntıları yoksayılır.

> [!NOTE]
> Web kancasını çağırırken, çağrının başarısız olması durumunda istemcinin her zaman parametre değerlerini depolaması gerekir. Ağ kesintisi veya bağlantı sorunu varsa, uygulama başarısız Web kancası çağrılarını alamıyor.

`WebhookData`Web kancası oluşturma sırasında için bir değer belirtirseniz, Web kancası runbook 'u ISTEMCI POST isteğinden alınan verilerle başlattığında bu geçersiz kılınır. Bu, uygulama, istek gövdesine herhangi bir veri içermediği halde olur. 

Web kancası dışında bir mekanizma kullanarak tanımlayan bir runbook 'u başlatırsanız `WebhookData` runbook 'un tanıdığı bir değer sağlayabilirsiniz `WebhookData` . Bu değer, runbook ile aynı [özelliklere](#webhook-properties) sahip bir nesne olmalıdır `WebhookData` , böylece `WebhookData` bir Web kancası tarafından geçirilen gerçek nesnelerle birlikte çalışır.

Örneğin, Azure portal aşağıdaki runbook 'u başlatıyorsanız ve test için bazı örnek Web kancası verileri geçirmek istiyorsanız, verileri JSON içinde Kullanıcı arabirimine geçirmeniz gerekir.

![Kullanıcı arabiriminden Web kancası verileri parametresi](media/automation-webhooks/WebhookData-parameter-from-UI.png)

Sonraki runbook örneğinde, için aşağıdaki özellikleri tanımlayalim `WebhookData` :

* **Web kancası adı**: MyWeb kancası
* **Istek gövdesi**: `*[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*`

Şimdi parametresi için Kullanıcı arabiriminde aşağıdaki JSON nesnesini geçiyoruz `WebhookData` . Bu örnek, satır başı ve yeni satır karakterleri ile bir Web kancasından geçirilen biçimle eşleşir.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Kullanıcı arabiriminden Webkancaverisi parametresini Başlat](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Azure Otomasyonu, tüm giriş parametrelerinin değerlerini runbook işiyle günlüğe kaydeder. Bu nedenle, Web kancası isteğinde istemci tarafından sağlanan herhangi bir giriş günlüğe kaydedilir ve otomasyon işine erişimi olan herkes tarafından kullanılabilir. Bu nedenle, Web kancası çağrılarında hassas bilgileri dahil etmek için dikkatli olmanız gerekir.

## <a name="webhook-security"></a>Web kancası güvenliği

Web kancasının güvenliği, Web kancasının çağrılmasına izin veren bir güvenlik belirteci içeren URL 'sinin gizliliğini kullanır. Azure Otomasyonu, doğru URL 'ye yapıldığı sürece bir istek üzerinde herhangi bir kimlik doğrulaması gerçekleştirmez. Bu nedenle istemcileriniz, isteği doğrulamak için alternatif bir yöntem kullanmadan son derece hassas işlemler gerçekleştiren runbook 'lar için Web kancalarını kullanmamalıdır.

Aşağıdaki stratejileri göz önünde bulundurun:

* Bir Web kancası tarafından çağrılıp çağrılmadığını öğrenmek için bir runbook 'a mantığı dahil edebilirsiniz. Runbook `WebhookName` 'un parametresinin özelliğini denetlemesini sağlayabilirsiniz `WebhookData` . Runbook, ve özelliklerindeki belirli bilgileri arayarak daha fazla doğrulama gerçekleştirebilir `RequestHeader` `RequestBody` .

* Runbook 'un bir Web kancası isteği aldığında dış koşul doğrulaması gerçekleştirmesini sağlama. Örneğin bir GitHub deposuna yeni bir kayıt olduğunda GitHub tarafından çağrılan bir runbook 'u göz önünde bulundurun. Runbook, devam etmeden önce yeni bir işleme gerçekleştiğini doğrulamak için GitHub 'a bağlanabilir.

* Azure Otomasyonu, Azure sanal ağ hizmeti etiketlerini, özellikle de [Gustandhybridmanagement](../virtual-network/service-tags-overview.md)'ı destekler. [Ağ güvenlik grupları](../virtual-network/network-security-groups-overview.md#security-rules) veya [Azure Güvenlik Duvarı](../firewall/service-tags.md) üzerindeki ağ erişim denetimlerini tanımlamak ve sanal ağınızın içinden Web kancalarını tetikleyebilmeniz için hizmet etiketlerini kullanabilirsiniz. Hizmet etiketleri, güvenlik kuralları oluştururken belirli IP adreslerinin yerine kullanılabilir. Bir kuralın uygun kaynak veya hedef alanında hizmet etiketi adı **Guestandhybridmanagement**  ' i belirterek, Otomasyon Hizmeti için trafiğe izin verebilir veya bu trafiği reddedebilirsiniz. Bu hizmet etiketi, IP aralıklarını belirli bir bölgeyle kısıtlayarak daha ayrıntılı denetim yapılmasına izin vermeyi desteklemez.

## <a name="create-a-webhook"></a>Web kancası oluşturma

Azure portal bir runbook 'a bağlı yeni bir Web kancası oluşturmak için aşağıdaki yordamı kullanın.

1. Azure portal runbook 'Lar sayfasında, runbook ayrıntılarını görüntülemek için Web kancasının başladığı runbook 'a tıklayın. Runbook **durum** alanının **yayımlanmış** olarak ayarlandığından emin olun.
2. Web kancası Ekle sayfasını açmak için sayfanın üst kısmındaki **Web kancası** ' ne tıklayın.
3. **Yeni Web kancası** oluştur ' a tıklayarak Web kancası oluştur sayfasını açın.
4. Web kancası için **ad** ve **sona erme tarihi** alanlarını doldurup etkinleştirilmesi gerekip gerekmediğini belirtin. Bu özellikler hakkında daha fazla bilgi için bkz. [Web kancası özellikleri](#webhook-properties) .
5. Web kancasının URL 'sini kopyalamak için Kopyala simgesine tıklayın ve CTRL + C tuşlarına basın. Daha sonra güvenli bir yere kaydedin. 

    > [!IMPORTANT]
    > Web kancasını oluşturduktan sonra URL 'YI tekrar alamazsınız. ' İ kopyalayıp, yukarıdaki gibi kaydettiğinizden emin olun.

   ![Web kancası URL 'SI](media/automation-webhooks/copy-webhook-url.png)

1. Runbook parametreleri için değerler sağlamak üzere **Parametreler** ' e tıklayın. Runbook 'un zorunlu parametreleri varsa, değer sağlamadıkça Web kancasını oluşturamazsınız.

2. Web kancasını oluşturmak için **Oluştur**’a tıklayın.

## <a name="use-a-webhook"></a>Web kancası kullanma

Bir Web kancasını oluşturulduktan sonra kullanmak için, istemciniz `POST` Web KANCASı URL 'siyle BIR http isteği vermelidir. Söz dizimi aşağıdaki gibidir:

```http
http://<Webhook Server>/token?=<Token Value>
```

İstemci, istekten aşağıdaki dönüş kodlarından birini alır `POST` .

| Kod | Metin | Description |
|:--- |:--- |:--- |
| 202 |Kabul edildi |İstek kabul edildi ve Runbook başarıyla kuyruğa alındı. |
| 400 |Hatalı İstek |İstek aşağıdaki nedenlerden biri için kabul edilmedi: <ul> <li>Web kancası süresi doldu.</li> <li>Web kancası devre dışı bırakıldı.</li> <li>URL 'deki belirteç geçersiz.</li>  </ul> |
| 404 |Bulunamadı |İstek aşağıdaki nedenlerden biri için kabul edilmedi: <ul> <li>Web kancası bulunamadı.</li> <li>Runbook bulunamadı.</li> <li>Hesap bulunamadı.</li>  </ul> |
| 500 |İç sunucu hatası |URL geçerli, ancak bir hata oluştu. Lütfen isteği yeniden gönderin. |

İsteğin başarılı olduğu varsayıldığında, Web kancası yanıtı aşağıda gösterildiği gibi JSON biçimindeki iş KIMLIĞINI içerir. Tek bir iş KIMLIĞI içerir, ancak JSON biçimi gelecekteki iyileştirmeler için izin verir.

```json
{"JobIds":["<JobId>"]}
```

İstemci, runbook işinin ne zaman tamamlandığını veya Web kancasından tamamlanma durumunu belirleyemez. Bu bilgileri, [Windows PowerShell](/powershell/module/servicemanagement/azure.service/get-azureautomationjob) veya [Azure Otomasyonu API](/rest/api/automation/job)gibi başka BIR mekanizmasıyla iş kimliğini kullanarak bulabilir.

### <a name="use-a-webhook-from-an-arm-template"></a>ARM şablonundan Web kancası kullanma

Otomasyon Web kancaları, [Azure Resource Manager (ARM) şablonları](/azure/azure-resource-manager/templates/overview)tarafından da çağrılabilir. ARM şablonu bir istek verir `POST` ve aynı diğer istemci gibi bir dönüş kodu alır. Bkz. [Web kancası kullanma](#use-a-webhook).

   > [!NOTE]
   > Güvenlik nedenleriyle, URI yalnızca bir şablon dağıtıldığında ilk kez döndürülür.

Bu örnek şablon bir test ortamı oluşturur ve oluşturduğu Web kancası için URI döndürür.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "automationAccountName": {
            "type": "String",
            "metadata": {
                "description": "Automation account name"
            }
        },
        "webhookName": {
            "type": "String",
            "metadata": {
                "description": "Webhook Name"
            }
        },
        "runbookName": {
            "type": "String",
            "metadata": {
                "description": "Runbook Name for which webhook will be created"
            }
        },
        "WebhookExpiryTime": {
            "type": "String",
            "metadata": {
                "description": "Webhook Expiry time"
            }
        },
        "_artifactsLocation": {
            "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-automation/",
            "type": "String",
            "metadata": {
                "description": "URI to artifacts location"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2020-01-13-preview",
            "name": "[parameters('automationAccountName')]",
            "location": "[resourceGroup().location]",
            "properties": {
                "sku": {
                    "name": "Free"
                }
            },
            "resources": [
                {
                    "type": "runbooks",
                    "apiVersion": "2018-06-30",
                    "name": "[parameters('runbookName')]",
                    "location": "[resourceGroup().location]",
                    "dependsOn": [
                        "[parameters('automationAccountName')]"
                    ],
                    "properties": {
                        "runbookType": "Python2",
                        "logProgress": "false",
                        "logVerbose": "false",
                        "description": "Sample Runbook",
                        "publishContentLink": {
                            "uri": "[uri(parameters('_artifactsLocation'), 'scripts/AzureAutomationTutorialPython2.py')]",
                            "version": "1.0.0.0"
                        }
                    }
                },
                {
                    "type": "webhooks",
                    "apiVersion": "2018-06-30",
                    "name": "[parameters('webhookName')]",
                    "dependsOn": [
                        "[parameters('automationAccountName')]",
                        "[parameters('runbookName')]"
                    ],
                    "properties": {
                        "isEnabled": true,
                        "expiryTime": "[parameters('WebhookExpiryTime')]",
                        "runbook": {
                            "name": "[parameters('runbookName')]"
                        }
                    }
                }
            ]
        }
    ],
    "outputs": {
        "webhookUri": {
            "type": "String",
            "value": "[reference(parameters('webhookName')).uri]"
        }
    }
}
```

## <a name="renew-a-webhook"></a>Web kancasını yenileme

Bir Web kancası oluşturulduğunda, geçerlilik süresi on yıl, sonrasında otomatik olarak sona erer. Bir Web kancası süresi dolduktan sonra yeniden etkinleştiremezsiniz. Yalnızca kaldırıp yeniden oluşturabilirsiniz. 

Sona erme süresine ulaşmayan bir Web kancasını genişletebilirsiniz. Web kancasını genişletmek için:

1. Web kancasını içeren runbook 'a gidin. 
2. **Kaynaklar** altında **Web kancaları** ' nı seçin. 
3. Genişletmek istediğiniz Web kancasına tıklayın. 
4. Web kancası sayfasında, yeni bir sona erme tarihi ve saati seçip **Kaydet**' e tıklayın.

## <a name="sample-runbook"></a>Örnek runbook

Aşağıdaki örnek runbook, Web kancası verilerini kabul eder ve istek gövdesinde belirtilen sanal makineleri başlatır. Bu runbook 'u test etmek için Runbook **'ların** altındaki Otomasyon hesabınızda **runbook oluştur ' a** tıklayın. Runbook oluşturmayı bilmiyorsanız, bkz. [runbook oluşturma](automation-quickstart-create-runbook.md).

> [!NOTE]
> Grafik olmayan PowerShell runbook 'ları için `Add-AzAccount` ve `Add-AzureRMAccount` [Connect-azaccount](/powershell/module/az.accounts/connect-azaccount)için diğer adlar. Bu cmdlet 'leri kullanabilir veya Otomasyon hesabınızdaki [modüllerinizi](automation-update-azure-modules.md) en son sürümlere güncelleştirebilirsiniz. Yeni bir Otomasyon hesabı oluşturmuş olsanız bile modüllerinizi güncelleştirmeniz gerekebilir.

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData
)

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

    # Check header for message to validate request
    if ($WebhookData.RequestHeader.message -eq 'StartedbyContoso')
    {
        Write-Output "Header has required information"}
    else
    {
        Write-Output "Header missing required information";
        exit;
    }

    # Retrieve VMs from Webhook request body
    $vms = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Authenticate to Azure by using the service principal and certificate. Then, set the subscription.

    Write-Output "Authenticating to Azure with service principal and certificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    Write-Output "Get connection asset: $ConnectionAssetName"

    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null)
            {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Write-Output "Authenticating to Azure with service principal."
            Add-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Output

        # Start each virtual machine
        foreach ($vm in $vms)
        {
            $vmName = $vm.Name
            Write-Output "Starting $vmName"
            Start-AzVM -Name $vm.Name -ResourceGroup $vm.ResourceGroup
        }
}
else {
    # Error
    write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="test-the-sample"></a>Örneği test etme

Aşağıdaki örnek Windows PowerShell kullanarak bir runbook 'u bir Web kancası ile başlatır. HTTP isteğinde bulunan herhangi bir dil, Web kancası kullanabilir. Windows PowerShell bir örnek olarak burada kullanılır.

Runbook, istek gövdesinde JSON 'da biçimlendirilen sanal makinelerin bir listesini bekliyor. Runbook, Web kancası çağıranın geçerli olduğunu doğrulamak için üst bilgilerin tanımlı bir ileti içermesini sağlar.

```azurepowershell-interactive
$uri = "<webHook Uri>"

$vms  = @(
            @{ Name="vm01";ResourceGroup="vm01"},
            @{ Name="vm02";ResourceGroup="vm02"}
        )
$body = ConvertTo-Json -InputObject $vms
$header = @{ message="StartedbyContoso"}
$response = Invoke-WebRequest -Method Post -Uri $uri -Body $body -Headers $header
$jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
```

Aşağıdaki örnek, öğesinin özelliğindeki runbook için kullanılabilir olan isteğin gövdesini gösterir `RequestBody` `WebhookData` . Bu değer, isteğin gövdesinde bulunan biçimle uyumlu olacak şekilde JSON 'da biçimlendirilir.

```json
[
    {
        "Name":  "vm01",
        "ResourceGroup":  "myResourceGroup"
    },
    {
        "Name":  "vm02",
        "ResourceGroup":  "myResourceGroup"
    }
]
```

Aşağıdaki görüntüde Windows PowerShell 'den gönderilen istek ve elde edilen yanıt gösterilmektedir. İş KIMLIĞI yanıttan çıkarılır ve bir dizeye dönüştürülür.

![Web kancaları düğmesi](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>Sonraki adımlar

* Bir runbook 'u bir uyarıdan tetiklemek için bkz. [Azure Otomasyonu runbook 'u tetiklemek için uyarı kullanma](automation-create-alert-triggered-runbook.md).
