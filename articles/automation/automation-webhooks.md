---
title: Web kancası ile Azure Otomasyonu runbook 'u başlatma
description: Bir istemcinin Azure Automation 'da bir runbook 'u HTTP çağrısından başlatmasını sağlayan bir Web kancası.  Bu makalede bir Web kancası oluşturma ve bir runbook 'u başlatmak için bir nasıl çağrılacağını açıklanmaktadır.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 03/19/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 153e910ea85ae843c6d4db51e709b58e441f6761
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70061442"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Web kancası ile Azure Otomasyonu runbook 'u başlatma

*Web kancası* , tek bir http Isteği aracılığıyla Azure Otomasyonu 'nda belirli bir runbook 'u başlatabilmeniz için izin verir. Bu, Azure DevOps Services, GitHub, Azure Izleyici günlükleri veya özel uygulamalar gibi dış hizmetlerin Azure Otomasyonu API 'sini kullanarak tam bir çözüm uygulamadan runbook 'ları başlatmasını sağlar.
![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

Web kancalarını, [Azure Otomasyonu 'nda runbook başlatma](automation-starting-a-runbook.md) bölümünde bulunan bir runbook 'u başlatma yöntemleriyle karşılaştırabilirsiniz

> [!NOTE]
> Python runbook 'u başlatmak için Web kancası kullanılması desteklenmez.

## <a name="details-of-a-webhook"></a>Web kancası ayrıntıları

Aşağıdaki tabloda bir Web kancası için yapılandırmanız gereken özellikler açıklanmaktadır.

| Özellik | Description |
|:--- |:--- |
| Name |Bu istemciye açık olmadığından, Web kancası için istediğiniz herhangi bir ad sağlayabilirsiniz. Yalnızca Azure Otomasyonu 'nda runbook 'u tanımlamak için kullanılır. <br> En iyi uygulama olarak, Web kancasına onu kullanan istemciyle ilgili bir ad vermeniz gerekir. |
| URL |Web kancası URL 'SI, bir istemcinin Web kancasına bağlı olan runbook 'u başlatmak için bir HTTP POST ile çağırdığı benzersiz adrestir. Web kancasını oluşturduğunuzda otomatik olarak oluşturulur. Özel bir URL belirtemezsiniz. <br> <br> URL, runbook 'un başka kimlik doğrulaması olmadan bir üçüncü taraf sistem tarafından çağrılmasına izin veren bir güvenlik belirteci içeriyor. Bu nedenle, bir parola gibi değerlendirilmelidir. Güvenlik nedenleriyle, URL 'YI yalnızca, Web kancasının oluşturulduğu sırada Azure portal görüntüleyebilirsiniz. Daha sonra kullanılmak üzere güvenli bir konumda URL 'YI aklınızda bulundurma. |
| Son kullanma tarihi |Bir sertifika gibi, her Web kancasının bir sona erme tarihi vardır ve bu süre artık kullanılamaz. Bu sona erme tarihi, Web kancasının süresi dolduğu sürece Web kancası oluşturulduktan sonra değiştirilebilir. |
| Enabled |Bir Web kancası oluşturulduğunda varsayılan olarak etkinleştirilir. Devre dışı olarak ayarlarsanız, hiçbir istemci bunu kullanamaz. **Etkin** özelliği, Web kancasını oluştururken veya her zaman oluşturulduğunda ayarlayabilirsiniz. |

### <a name="parameters"></a>Parametreler

Web kancası, runbook 'un bu Web kancası tarafından başlatıldığı zaman kullanılan runbook parametreleri için değerler tanımlayabilir. Web kancası, runbook 'un zorunlu parametreleri için değerler içermeli ve isteğe bağlı parametrelerin değerlerini içerebilir. Web kancası oluşturulduktan sonra bile bir Web kancasına yapılandırılmış bir parametre değeri değiştirilebilir. Tek bir runbook 'a bağlı birden çok Web kancası, her biri farklı parametre değerleri kullanır.

İstemci bir Web kancası kullanarak runbook 'u başlattığında, Web kancasında tanımlanan parametre değerlerini geçersiz kılamazsınız. İstemciden veri almak için, runbook **$WebhookData**adlı tek bir parametreyi kabul edebilir. Bu parametre, POST isteğinde istemcinin içerdiği verileri içeren bir [Object] türüdür.

![Web kancası veri özellikleri](media/automation-webhooks/webhook-data-properties.png)

**$WebhookData** nesnesi aşağıdaki özelliklere sahiptir:

| Özellik | Açıklama |
|:--- |:--- |
| WebhookName |Web kancasının adı. |
| RequestHeader |Gelen POST isteğinin üst bilgilerini içeren karma tablo. |
| Istek gövdesi |Gelen POST isteğinin gövdesi. Bu, String, JSON, XML veya form kodlu veriler gibi herhangi bir biçimlendirmeyi korur. Runbook 'un beklenen veri biçimiyle çalışacak şekilde yazılması gerekir. |

**$WebhookData** parametresini desteklemek için gereken Web kancası yapılandırması yoktur ve Runbook 'un kabul etmek için gerekli değildir. Runbook parametreyi tanımlamaz, istemciden gönderilen isteğin ayrıntıları yoksayılır.

> [!NOTE]
> Web kancasını çağırırken, çağrının başarısız olması durumunda her zaman parametre değerlerini depolamanız gerekir. Ağ kesintisi veya bağlantı sorunu varsa, başarısız olan Web kancası çağrılarını alamazsınız.

Web kancasını oluştururken $WebhookData için bir değer belirtirseniz, Web kancası, istemci, istek gövdesine hiçbir veri içermediği halde, runbook 'u istemci POST isteğindeki verilerle başlattığında bu değer geçersiz kılınır. Web kancası dışında bir yöntem kullanan $WebhookData bir runbook 'u başlatırsanız, runbook tarafından tanınan $Webhookdata için bir değer sağlayabilirsiniz. Bu değer, runbook 'un bir Web kancası tarafından geçirilen gerçek Web kancasıyla çalıştığı gibi düzgün şekilde çalışabilmesi için $Webhookdata aynı [özelliklere](#details-of-a-webhook) sahip bir nesne olmalıdır.

Örneğin, aşağıdaki runbook 'u Azure portal başlatıyorsanız ve test için bazı örnek Web kancası verileri geçirmek istiyorsanız, Web kancası verileri bir nesne olduğundan, Kullanıcı arabiriminde JSON olarak geçirilmelidir.

![Kullanıcı arabiriminden Web kancası verileri parametresi](media/automation-webhooks/WebhookData-parameter-from-UI.png)

Aşağıdaki runbook için, Web kancası verileri parametresi için aşağıdaki özelliklere sahipseniz:

* WebhookName *MyWeb kancası*
* RequestBody: *[{' resourcegroup ': ' myResourceGroup ', ' name ': ' vm01 '}, {' ResourceGroup ': ' myResourceGroup ', ' name ': ' vm02 '}]*

Daha sonra Web Kancaverisi parametresi için Kullanıcı arabiriminde aşağıdaki JSON değerini geçitirsiniz. Aşağıdaki örnek, satır başı ve yeni satır karakterleri ile bir Web kancasından geçirilen biçimle eşleşir.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Kullanıcı arabiriminden Webkancaverisi parametresini Başlat](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Tüm giriş parametrelerinin değerleri runbook işiyle günlüğe kaydedilir. Bu, Web kancası isteğinde istemci tarafından sağlanan tüm girişlerin günlüğe kaydedilecek ve otomasyon işine erişimi olan herkes tarafından kullanılabilir olacağı anlamına gelir.  Bu nedenle, Web kancası çağrılarında hassas bilgileri dahil etmek için dikkatli olmanız gerekir.

## <a name="security"></a>Güvenlik

Web kancasının güvenliği, onun çağrılmasını sağlayan bir güvenlik belirteci içeren URL 'sinin gizliliğini kullanır. Azure Otomasyonu, doğru URL 'ye yapıldığı sürece istek üzerinde herhangi bir kimlik doğrulaması gerçekleştirmez. Bu nedenle, Web kancaları, isteği doğrulamak için alternatif bir yöntem kullanmadan son derece duyarlı işlevler gerçekleştiren runbook 'lar için kullanılmamalıdır.

$WebhookData parametresinin **Webkancaname** özelliğini denetleyerek bir Web kancası tarafından çağrıldığından emin olmak için Runbook içine Logic ekleyebilirsiniz. Runbook, **RequestHeader** veya **requestbody** özelliklerindeki belirli bilgileri arayarak daha fazla doğrulama gerçekleştirebilir.

Başka bir strateji, runbook 'un bir Web kancası isteği aldığında dış koşul doğrulaması gerçekleştirmesini sağlar. Örneğin, GitHub deposuna yeni bir kayıt olduğunda GitHub tarafından çağrılan bir runbook 'u düşünün. Runbook, devam etmeden önce yeni bir işleme gerçekleştiğini doğrulamak için GitHub 'a bağlanabilir.

## <a name="creating-a-webhook"></a>Web kancası oluşturma

Azure portal bir runbook 'a bağlı yeni bir Web kancası oluşturmak için aşağıdaki yordamı kullanın.

1. Azure portal **runbook 'ları sayfasında** , Web kancasının Ayrıntılar sayfasını görüntülemek için başladığı runbook 'a tıklayın. Runbook **durumunun** **yayımlandığından**emin olun.
2. **Web kancası Ekle** sayfasını açmak için sayfanın üst kısmındaki **Web kancası** ' ne tıklayın.
3. **Yeni Web kancası** oluştur ' a tıklayarak **Web kancası oluştur sayfasını**açın.
4. Web kancası için bir **ad**, **sona erme tarihi** ve etkinleştirilmesi gerekip gerekmediğini belirtin. Bu özellikler hakkında daha fazla bilgi için [Web kancasının ayrıntılarına](#details-of-a-webhook) bakın.
5. Web kancasının URL 'sini kopyalamak için Kopyala simgesine tıklayın ve CTRL + C tuşlarına basın. Daha sonra güvenli bir yere kaydedin. **Web kancasını oluşturduktan sonra URL 'YI tekrar alamazsınız.**

   ![Web kancası URL'si](media/automation-webhooks/copy-webhook-url.png)

1. Runbook parametreleri için değerler sağlamak üzere **Parametreler** ' e tıklayın. Runbook 'un zorunlu parametreleri varsa, hiçbir değer sağlanmadığı müddetçe Web kancasını oluşturabileceksiniz.
1. Web kancasını oluşturmak için **Oluştur** ' a tıklayın.

## <a name="using-a-webhook"></a>Web kancası kullanma

Bir Web kancasını oluşturulduktan sonra kullanmak için, istemci uygulamanızın Web kancası URL 'SI ile bir HTTP GÖNDERISI vermesi gerekir. Web kancasının sözdizimi aşağıdaki biçimdedir:

```http
http://<Webhook Server>/token?=<Token Value>
```

İstemci, POST isteğinden aşağıdaki dönüş kodlarından birini alır.

| Kod | Text | Açıklama |
|:--- |:--- |:--- |
| 202 |Kabul Edildi |İstek kabul edildi ve Runbook başarıyla kuyruğa alındı. |
| 400 |Bozuk İstek |İstek aşağıdaki nedenlerden biri için kabul edilmedi: <ul> <li>Web kancası süresi doldu.</li> <li>Web kancası devre dışı bırakıldı.</li> <li>URL 'deki belirteç geçersiz.</li>  </ul> |
| 404 |Bulunamadı |İstek aşağıdaki nedenlerden biri için kabul edilmedi: <ul> <li>Web kancası bulunamadı.</li> <li>Runbook bulunamadı.</li> <li>Hesap bulunamadı.</li>  </ul> |
| 500 |İç Sunucu Hatası |URL geçerli, ancak bir hata oluştu. Lütfen isteği yeniden gönderin. |

İsteğin başarılı olduğu varsayıldığında, Web kancası yanıtı JSON biçimindeki iş KIMLIĞINI aşağıdaki şekilde içerir. Tek bir iş KIMLIĞI içerir, ancak JSON biçimi gelecekteki iyileştirmeler için izin verir.

```json
{"JobIds":["<JobId>"]}
```

İstemci, runbook işinin ne zaman tamamlandığını veya Web kancasından tamamlanma durumunu belirleyemez. Bu bilgileri, iş KIMLIĞINI [Windows PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjob) veya [Azure Otomasyonu API](/rest/api/automation/job)gibi başka bir yöntemle kullanarak belirleyebilir.

## <a name="renew-webhook"></a>Web kancasını yenileme

Bir Web kancası oluşturulduğunda, bir yıllık geçerlilik süresi vardır. Bu yıldan sonra, Web kancası otomatik olarak sona erer. Bir Web kancası süre dolduktan sonra yeniden etkinleştirilemez, kaldırılması ve yeniden oluşturulması gerekir. Bir Web kancası süre sonu zamanına ulaşmazsa genişletilebilir.

Web kancasını genişletmek için Web kancasını içeren runbook 'a gidin. **Kaynaklar**altında **Web kancaları** ' nı seçin. Genişletmek istediğiniz Web kancasına tıklayın, bu eylem **Web kancası** sayfasını açar.  Yeni bir sona erme tarihi ve saati seçip **Kaydet**' e tıklayın.

## <a name="sample-runbook"></a>Örnek runbook

Aşağıdaki örnek runbook, Web kancası verilerini kabul eder ve istek gövdesinde belirtilen sanal makineleri başlatır. Bu runbook 'u test etmek için Runbook **'ların**altındaki Otomasyon hesabınızda **+ runbook Ekle**' ye tıklayın. Runbook oluşturmayı bilmiyorsanız, bkz. [runbook oluşturma](automation-quickstart-create-runbook.md).

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

    # Retrieve VM's from Webhook request body
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
            Add-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Output

        # Start each virtual machine
        foreach ($vm in $vms)
        {
            $vmName = $vm.Name
            Write-Output "Starting $vmName"
            Start-AzureRMVM -Name $vm.Name -ResourceGroup $vm.ResourceGroup
        }
}
else {
    # Error
    write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="test-the-example"></a>Örneği test etme

Aşağıdaki örnek Windows PowerShell kullanarak bir runbook 'u bir Web kancası ile başlatır. HTTP isteğinde bulunan herhangi bir dil, Web kancası kullanabilir; Windows PowerShell bir örnek olarak burada kullanılır.

Runbook, istek gövdesinde JSON 'da biçimlendirilen sanal makinelerin bir listesini bekliyor. Runbook, Web kancası çağıranın geçerli olduğunu doğrulamak için üst bilgilerin tanımlı bir ileti içerek olarak doğrular.

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

Aşağıdaki örnekte, **Web kancadaki** **requestbody** özelliğinde runbook için kullanılabilen isteğin gövdesi gösterilmektedir. Bu değer, isteğin gövdesine eklenen biçim olduğundan JSON olarak biçimlendirilir.

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

* Azure Otomasyonu 'nu kullanarak Azure uyarıları üzerinde işlem yapma hakkında bilgi edinmek için bkz. [Azure Otomasyonu runbook 'u tetiklemek için uyarı kullanma](automation-create-alert-triggered-runbook.md).

