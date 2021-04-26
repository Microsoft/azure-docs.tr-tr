---
title: Azure Otomasyonu 'nda runbook giriş parametrelerini yapılandırma
description: Bu makalede, runbook giriş parametrelerinin, başlatıldığında verilerin bir runbook 'a geçirilmesine izin veren nasıl yapılandırılacağı açıklanır.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 95a6cc87471fcf2209d452f90e1e5f52cae122c5
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831303"
---
# <a name="configure-runbook-input-parameters"></a>Runbook girdi parametrelerini yapılandırma

Runbook giriş parametreleri, başlatıldığında verilerin kendisine geçirilmesine izin vererek bir runbook 'un esnekliğini artırır. Bu parametreler, runbook eylemlerinin belirli senaryolar ve ortamlar için hedeflenmiş olmasını sağlar. Bu makalede, runbook 'larınızda giriş parametrelerinin yapılandırması ve kullanımı açıklanmaktadır.

PowerShell, PowerShell Iş akışı, grafik ve Python runbook 'lar için giriş parametrelerini yapılandırabilirsiniz. Bir runbook, farklı veri türlerine sahip birden fazla parametreye sahip olabilir veya hiç parametre içermez. Giriş parametreleri zorunlu veya isteğe bağlı olabilir ve isteğe bağlı parametreler için varsayılan değerleri kullanabilirsiniz.

Uygulamayı başlattığınızda bir runbook için giriş parametrelerine değerler atarsınız. Azure portal, bir Web hizmeti veya PowerShell 'den bir runbook 'u başlatabilirsiniz. Ayrıca, başka bir runbook 'ta satır içi olarak adlandırılan bir alt runbook olarak da başlatabilirsiniz.

### <a name="configure-input-parameters-in-powershell-runbooks"></a>PowerShell runbook 'larında giriş parametrelerini yapılandırma

Azure Otomasyonu 'ndaki PowerShell ve PowerShell Iş akışı runbook 'ları, aşağıdaki özellikler aracılığıyla tanımlanan giriş parametrelerini destekler. 

| **Özellik** | **Açıklama** |
|:--- |:--- |
| Tür |Gereklidir. Parametre değeri için beklenen veri türü. Herhangi bir .NET türü geçerlidir. |
| Name |Gereklidir. Parametrenin adı. Bu ad runbook içinde benzersiz olmalıdır, bir harfle başlamalıdır ve yalnızca harf, sayı veya alt çizgi karakterlerini içerebilir. |
| Zorunlu |İsteğe bağlı. Parametrenin bir değer gerektirip gerektirmediğini belirten Boole değeri. Bunu true olarak ayarlarsanız, runbook başlatıldığında bir değer sağlanmalıdır. Bunu yanlış olarak ayarlarsanız bir değer isteğe bağlıdır. Özelliği için bir değer belirtmezseniz `Mandatory` , PowerShell giriş parametresini varsayılan olarak isteğe bağlı olarak değerlendirir. |
| Varsayılan değer |İsteğe bağlı. Runbook başladığında hiçbir giriş değeri geçirilmemişse, parametresi için kullanılan bir değer. Runbook herhangi bir parametre için varsayılan bir değer ayarlayabilir. |

Windows PowerShell, giriş parametrelerinin, doğrulama, diğer adlar ve parametre kümeleri gibi yukarıda listelenenlerden daha fazla özniteliğini destekler. Ancak, Azure Otomasyonu Şu anda yalnızca listelenen giriş parametresi özelliklerini desteklemektedir.

Örnek olarak, PowerShell Iş akışı runbook 'unda bir parametre tanımına bakalım. Bu tanım, birden fazla parametrenin virgülle ayrıldığı aşağıdaki genel formu içerir.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

Şimdi, tek bir VM veya bir kaynak grubundaki tüm VM 'Ler hakkında sanal makinelerle ilgili ayrıntıları çıkaran bir PowerShell Iş akışı runbook 'u için giriş parametrelerini yapılandıralim. Bu runbook 'un aşağıdaki ekran görüntüsünde gösterildiği gibi iki parametresi vardır: sanal makinenin adı ( `VMName` ) ve kaynak grubunun adı ( `resourceGroupName` ).

![Otomasyon PowerShell Iş akışı](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

Bu parametre tanımında, giriş parametreleri dize türünde basit parametrelerdir.

PowerShell ve PowerShell Iş akışı runbook 'larının, veya gibi, `Object` giriş parametreleri için tüm basit türleri ve karmaşık türleri desteklediğini unutmayın `PSCredential` . Runbook 'da bir nesne giriş parametresi varsa, bir değeri geçirmek için ad-değer çiftleri içeren bir PowerShell Hashtable 'ı kullanmanız gerekir. Örneğin, bir runbook 'ta aşağıdaki parametreye sahipsiniz.

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

Bu durumda, parametresine aşağıdaki değeri geçirebilirsiniz.

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> Null varsayılan değere sahip isteğe bağlı bir dize parametresine bir değer geçirmezseniz, parametrenin değeri null yerine boş bir dizedir.

### <a name="configure-input-parameters-in-graphical-runbooks"></a>Grafik runbook 'larda giriş parametrelerini yapılandırma

Grafik runbook 'un giriş parametrelerinin yapılandırılmasını göstermek için, sanal makineler hakkındaki ayrıntıları, tek bir VM veya bir kaynak grubu içindeki tüm VM 'Ler hakkında bilgi veren bir runbook oluşturalım. Ayrıntılar için bkz. [ilk grafik runbook 'Um](./learn/automation-tutorial-runbook-graphical.md).

Grafik runbook şu ana runbook etkinliklerini kullanır:

* Azure ile kimlik doğrulamak için Azure farklı çalıştır hesabının yapılandırılması. 
* VM özelliklerini almak için [Get-AzVM](/powershell/module/az.compute/get-azvm) cmdlet 'inin tanımı.
* VM adlarını çıkarmak için [yazma çıkışı](/powershell/module/microsoft.powershell.utility/write-output) etkinliğinin kullanımı. 

`Get-AzVM`Etkinlik iki giriş, VM adı ve kaynak grubu adını tanımlar. Bu adlar runbook 'un her başlatıldığında farklı olduğundan, bu girdileri kabul etmek için Runbook 'a giriş parametreleri eklemeniz gerekir. [Azure Otomasyonu 'Nda grafik yazma](automation-graphical-authoring-intro.md)konusuna bakın.

Giriş parametrelerini yapılandırmak için bu adımları izleyin.

1. Runbook 'Lar sayfasından grafik runbook 'unu seçin ve ardından **Düzenle**' ye tıklayın.
2. Grafik düzenleyicide **giriş ve çıkış** düğmesine tıklayın ve sonra da runbook giriş parametresi bölmesini açmak Için **girdi ekleyin** .

   ![Otomasyon grafik runbook 'u](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. Giriş ve çıkış denetimi, runbook için tanımlanan giriş parametrelerinin bir listesini görüntüler. Burada, yeni bir giriş parametresi ekleyebilir veya var olan bir giriş parametresinin yapılandırmasını düzenleyebilirsiniz. Runbook için yeni bir parametre eklemek üzere **giriş Ekle** ' ye tıklayarak **runbook giriş parametresi** dikey penceresini açın ve [Azure Otomasyonu 'nda grafik yazma](automation-graphical-authoring-intro.md)bölümünde tanımlanan özellikleri kullanarak parametreleri yapılandırabilirsiniz.

    ![Yeni giriş Ekle](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Etkinlik tarafından kullanılmak üzere aşağıdaki özelliklerle iki parametre oluşturun `Get-AzVM` ve ardından **Tamam**' a tıklayın.

   * Parametre 1:
        * **Ad**  --  **VMName**
        * **Tür** --dize
        * **Zorunlu**  --  **Hayır**

   * Parametre 2:
        * **Ad**  --  **Resourcegroupname**
        * **Tür** --dize
        * **Zorunlu**  --  **Hayır**
        * **Varsayılan değer**  --  **Özel**
        * Özel varsayılan değer-VM 'Leri içeren kaynak grubunun adı

5. Giriş ve çıkış denetimindeki parametreleri görüntüleyin. 
6. Yeniden **Tamam** ' a ve ardından **Kaydet**' e tıklayın.
7. Runbook 'unuzu yayımlamak için **Yayımla** ' ya tıklayın.

### <a name="configure-input-parameters-in-python-runbooks"></a>Python runbook 'larda giriş parametrelerini yapılandırma

PowerShell, PowerShell Iş akışı ve grafik runbook 'larının aksine, Python runbook 'ları adlandırılmış parametreler almaz. Runbook Düzenleyicisi, tüm giriş parametrelerini bağımsız değişken değerleri dizisi olarak ayrıştırır. Diziyi `sys` Python betiğe içeri aktararak ve sonra diziyi kullanarak erişebilirsiniz `sys.argv` . Dizinin ilk öğesinin, `sys.argv[0]` komut dosyasının adı olduğunu unutmamak önemlidir. Bu nedenle, ilk gerçek giriş parametresi `sys.argv[1]` .

Bir Python runbook 'unda giriş parametrelerinin nasıl kullanılacağına ilişkin bir örnek için bkz. [Azure Automation 'da Ilk Python runbook 'Um](./learn/automation-tutorial-runbook-textual-python2.md).

## <a name="assign-values-to-input-parameters-in-runbooks"></a>Runbook 'larda giriş parametrelerine değerler atama

Bu bölümde, runbook 'larda değerleri giriş parametrelerine geçirmek için çeşitli yollar açıklanmaktadır. Şunları yaptığınızda parametre değerleri atayabilirsiniz:

* [Runbook başlatma](#start-a-runbook-and-assign-parameters)
* [Bir runbook'u test etme](#test-a-runbook-and-assign-parameters)
* [Runbook için zamanlama bağlama](#link-a-schedule-to-a-runbook-and-assign-parameters)
* [Runbook için web kancası oluşturma](#create-a-webhook-for-a-runbook-and-assign-parameters)

### <a name="start-a-runbook-and-assign-parameters"></a>Bir runbook 'u başlatma ve parametreleri atama

Bir runbook pek çok yolla başlatılabilir: Azure portal, bir Web kancası ile PowerShell cmdlet 'leri, REST API veya SDK ile birlikte kullanılabilir. 

#### <a name="start-a-published-runbook-using-the-azure-portal-and-assign-parameters"></a>Azure portal kullanarak yayımlanmış runbook 'u başlatma ve parametreleri atama

Azure portal [runbook 'u](start-runbooks.md#start-a-runbook-with-the-azure-portal) başlattığınızda **runbook 'u Başlat** dikey penceresi açılır ve oluşturduğunuz parametrelere ilişkin değerleri girebilirsiniz.

![Portalı kullanmaya başlayın](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

Giriş kutusunun altındaki etikette, parametre özniteliklerini tanımlamak üzere ayarlanmış özellikleri görebilirsiniz; Örneğin, zorunlu veya isteğe bağlı, tür, varsayılan değer. Parametre adının yanındaki yardım balonu, parametre giriş değerleriyle ilgili kararlar almak için gereken anahtar bilgileri de tanımlar. 

> [!NOTE]
> Dize parametreleri dize türünde boş değerleri destekler. `[EmptyString]`Giriş parametresi kutusuna girildiğinde, parametreye boş bir dize geçirilir. Ayrıca dize parametreleri null değerini desteklemez. Bir dize parametresine bir değer geçirmezseniz, PowerShell onu null olarak yorumlar.

#### <a name="start-a-published-runbook-using-powershell-cmdlets-and-assign-parameters"></a>PowerShell cmdlet 'lerini kullanarak yayımlanmış runbook 'u başlatma ve parametreleri atama

* **Azure Resource Manager cmdlet 'leri:** [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook)kullanarak bir kaynak grubunda oluşturulan bir Otomasyon Runbook 'unu başlatabilirsiniz.

   ```powershell
     $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
     Start-AzAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
   ```

* **Azure klasik dağıtım modeli cmdlet 'leri:** [Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure.service/start-azureautomationrunbook)kullanarak, varsayılan bir kaynak grubunda oluşturulmuş bir Otomasyon Runbook 'unu başlatabilirsiniz.
  
   ```powershell
     $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
     Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
   ```

> [!NOTE]
> PowerShell cmdlet 'lerini kullanarak bir runbook 'u başlattığınızda, varsayılan parametresi `MicrosoftApplicationManagementStartedBy` değeri ile oluşturulur `PowerShell` . Bu parametreyi Iş ayrıntıları bölmesinde görüntüleyebilirsiniz.  

#### <a name="start-a-runbook-using-an-sdk-and-assign-parameters"></a>SDK kullanarak runbook başlatma ve parametreleri atama

* **Azure Resource Manager yöntemi:** Bir programlama dilinin SDK 'sını kullanarak bir runbook 'u başlatabilirsiniz. Otomasyon hesabınızda runbook başlatmaya yönelik bir C# kod parçacığı aşağıda verilmiştir. [GitHub deponuzdaki](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs)tüm kodu görüntüleyebilirsiniz.

   ```csharp
   public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
      {
        var response = AutomationClient.Jobs.Create(resourceGroupName, automationAccount, new JobCreateParameters
         {
            Properties = new JobCreateProperties
             {
                Runbook = new RunbookAssociationProperty
                 {
                   Name = runbookName
                 },
                   Parameters = parameters
             }
         });
      return response.Job;
      }
   ```

* **Azure klasik dağıtım modeli yöntemi:** Bir programlama dilinin SDK 'sını kullanarak bir runbook 'u başlatabilirsiniz. Otomasyon hesabınızda runbook başlatmaya yönelik bir C# kod parçacığı aşağıda verilmiştir. [GitHub deponuzdaki](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs)tüm kodu görüntüleyebilirsiniz.

   ```csharp
  public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
    {
      var response = AutomationClient.Jobs.Create(automationAccount, new JobCreateParameters
    {
      Properties = new JobCreateProperties
         {
           Runbook = new RunbookAssociationProperty
         {
           Name = runbookName
              },
                Parameters = parameters
              }
       });
      return response.Job;
    }
   ```

   Bu yöntemi başlatmak için, runbook parametrelerini ve değerlerini depolamak için bir sözlük `VMName` oluşturun  `resourceGroupName` . Ardından runbook 'u başlatın. Yukarıda tanımlanan yöntemi çağırmak için C# kod parçacığı aşağıda verilmiştir.

   ```csharp
   IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
   // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
   RunbookParameters.Add("resourceGroupName", "WSSC1");
  
   //Call the StartRunbook method with parameters
   StartRunbook("Get-AzureVMGraphical", RunbookParameters);
   ```

#### <a name="start-a-runbook-using-the-rest-api-and-assign-parameters"></a>REST API kullanarak runbook 'u başlatma ve parametreleri atama

`PUT`Aşağıdaki istek URI 'si ile yöntemini kullanarak bir runbook Işini Azure otomasyonu REST API oluşturup başlatabilirsiniz:`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`

İstek URI 'sinde, aşağıdaki parametreleri değiştirin:

* `subscriptionId`: Azure abonelik KIMLIĞINIZ.  
* `resourceGroupName`: Otomasyon hesabı için kaynak grubunun adı.
* `automationAccountName`: Belirtilen bulut hizmeti içinde barındırılan Otomasyon hesabının adı.  
* `jobName`: İş için GUID. PowerShell 'deki GUID 'Ler kullanılarak oluşturulabilir `[GUID]::NewGuid().ToString()*` .

Parametreleri runbook işine geçirmek için istek gövdesini kullanın. JSON biçiminde belirtilen aşağıdaki bilgileri alır:

* Runbook adı: gerekli. Başlatılacak iş için Runbook 'un adı.  
* Runbook parametreleri: Isteğe bağlı. Ad dize ve değer türünde olan parametre listesinin (ad, değer) biçiminde bir sözlüğü geçerli bir JSON değeri olabilir.

Daha önce ve parametresiyle oluşturulan **Get-AzureVMTextual** runbook 'unu başlatmak istiyorsanız `VMName` `resourceGroupName` , Istek gövdesi için aşağıdaki JSON biçimini kullanın.

```json
    {
      "properties":{
        "runbook":{
        "name":"Get-AzureVMTextual"},
      "parameters":{
         "VMName":"WindowsVM",
         "resourceGroupName":"ContosoSales"}
        }
    }
```

İş başarıyla oluşturulduysa 201 HTTP durum kodu döndürülür. Yanıt üstbilgileri ve yanıt gövdesi hakkında daha fazla bilgi için, bkz. [REST API kullanarak bir runbook işi oluşturma](/rest/api/automation/job/create).

### <a name="test-a-runbook-and-assign-parameters"></a>Runbook 'u test etme ve parametreleri atama

Test seçeneğini kullanarak [runbook 'un taslak sürümünü test](./manage-runbooks.md) ettiğinizde, test sayfası açılır. Oluşturduğunuz parametrelerin değerlerini yapılandırmak için bu sayfayı kullanın.

![Test ve atama parametreleri](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Bir runbook 'a zamanlama bağlama ve parametreleri atama

Runbook 'un belirli bir zamanda başlaması için [bir zamanlamayı](./shared-resources/schedules.md) runbook 'una bağlayabilirsiniz. Zamanlamayı oluştururken giriş parametrelerini atarsınız ve runbook, zamanlama tarafından başlatıldığında bu değerleri kullanır. Tüm zorunlu parametre değerleri sağlanana kadar zamanlamayı kaydedemezsiniz.

![Parametreleri zamanlayın ve atayın](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Runbook için Web kancası oluşturma ve parametreleri atama

Runbook 'larınız için bir [Web kancası](automation-webhooks.md) oluşturabilir ve Runbook giriş parametrelerini yapılandırabilirsiniz. Tüm zorunlu parametre değerleri sağlanana kadar Web kancasını kaydedemezsiniz.

![Web kancası oluşturma ve parametreleri atama](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Bir Web kancasını kullanarak bir runbook yürüttüğünüzde, önceden tanımlanmış giriş parametresi `[WebhookData](automation-webhooks.md)` , tanımladığınız giriş parametreleriyle birlikte gönderilir. 

![Web kancası verileri parametresi](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="pass-a-json-object-to-a-runbook"></a>JSON nesnesini runbook 'a geçirme

Bir JSON dosyasındaki runbook 'a geçirmek istediğiniz verileri depolamak yararlı olabilir. Örneğin, bir runbook 'a geçirmek istediğiniz tüm parametreleri içeren bir JSON dosyası oluşturabilirsiniz. Bunu yapmak için JSON kodunu bir dizeye dönüştürmeniz ve sonra dizgiyi runbook 'a geçirmeden önce bir PowerShell nesnesine dönüştürmeniz gerekir.

Bu bölümde, bir PowerShell betiğinin, bir PowerShell runbook 'unu başlatmak için [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) 'u ÇAĞıRDıĞı ve json dosyasının içeriğini runbook 'a geçirerek bir örnek kullanılmıştır. PowerShell runbook 'u JSON nesnesinden VM için parametreleri alarak bir Azure VM başlatır.

### <a name="create-the-json-file"></a>JSON dosyası oluşturma

Aşağıdaki kodu bir metin dosyasına yazın ve **test.js** olarak yerel bilgisayarınızda bir yere kaydedin.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>Runbook 'u oluşturma

Azure Otomasyonu 'nda **Test-JSON** adlı yeni bir PowerShell runbook 'u oluşturun. Bkz. [Ilk PowerShell runbook 'Um](./learn/automation-tutorial-runbook-textual-powershell.md).

JSON verilerini kabul etmek için, runbook bir nesneyi giriş parametresi olarak almalıdır. Runbook daha sonra JSON dosyasında tanımlanan özellikleri kullanabilir.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
```

Bu runbook 'u Otomasyon hesabınızda kaydedin ve yayımlayın.

### <a name="call-the-runbook-from-powershell"></a>Runbook 'u PowerShell 'den çağırma

Artık Azure PowerShell kullanarak runbook 'u yerel makinenizden çağırabilirsiniz. 

1. Gösterildiği gibi Azure 'da oturum açın. Daha sonra, Azure kimlik bilgilerinizi girmeniz istenir.

   ```powershell
   Connect-AzAccount
   ```

    >[!NOTE]
    >PowerShell runbook 'ları için `Add-AzAccount` ve `Add-AzureRMAccount` diğer adlardır `Connect-AzAccount` . Bu diğer adların grafik runbook 'lar için kullanılamaz olduğunu unutmayın. Grafik runbook 'u yalnızca kendisi kullanabilir `Connect-AzAccount` .

1. Kaydedilen JSON dosyasının içeriğini alın ve bir dizeye dönüştürün. `JsonPath` JSON dosyasını kaydettiğiniz yolu gösterir.

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

1. Dize içeriğini `$json` bir PowerShell nesnesine dönüştürün.

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. İçin parametreler için bir Hashtable oluşturun `Start-AzAutomationRunbook` . 

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   Değerini `Parameters` JSON dosyasındaki değerleri Içeren PowerShell nesnesine ayarlamadığınızı unutmayın.
1. Runbook 'u başlatın.

   ```powershell
   $job = Start-AzAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>Sonraki adımlar

* Metinsel runbook hazırlamak için bkz. [Azure Automation 'da metin runbook 'Larını düzenleme](automation-edit-textual-runbook.md).
* Grafik runbook 'u hazırlamak için bkz. [Azure Otomasyonu 'nda grafik runbook 'Ları yazma](automation-graphical-authoring-intro.md).
