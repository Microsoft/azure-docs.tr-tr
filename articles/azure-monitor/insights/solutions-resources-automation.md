---
title: Yönetim çözümlerinde Azure Otomasyonu kaynakları | Microsoft Docs
description: Yönetim çözümleri genellikle, izleme verilerini toplama ve işleme gibi işlemleri otomatikleştirmek için Azure Otomasyonu 'ndaki runbook 'ları içerir.  Bu makalede runbook 'ların ve ilgili kaynaklarının bir çözüme eklenmesi açıklanmaktadır.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/24/2017
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 63e09bacd1ce70f05f04798f092d3eb4b3e36ab5
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555249"
---
# <a name="adding-azure-automation-resources-to-a-management-solution-preview"></a>Azure Otomasyonu kaynaklarını bir yönetim çözümüne ekleme (Önizleme)
> [!NOTE]
> Bu, şu anda önizleme aşamasında olan yönetim çözümlerini oluşturmaya yönelik bir belgedir. Aşağıda açıklanan tüm şemalarla değişiklik yapılır.   


[Yönetim çözümleri]( solutions.md) genellikle, izleme verilerini toplama ve işleme gibi işlemleri otomatikleştirmek Için Azure Otomasyonu 'ndaki runbook 'ları içerir.  Runbook 'lara ek olarak, Otomasyon hesapları, çözümde kullanılan runbook 'ları destekleyen değişkenler ve zamanlamalar gibi varlıkları içerir.  Bu makalede runbook 'ların ve ilgili kaynaklarının bir çözüme eklenmesi açıklanmaktadır.

> [!NOTE]
> Bu makaledeki örneklerde, yönetim çözümlerinde gerekli veya ortak olan ve [Azure 'da bir yönetim çözümü tasarlama ve derleme]( solutions-creating.md) konularında açıklanan parametreleri ve değişkenleri kullanır 


## <a name="prerequisites"></a>Önkoşullar
Bu makalede, aşağıdaki bilgiler hakkında bilgi sahibi olduğunuz varsayılır.

- [Yönetim çözümü oluşturma]( solutions-creating.md).
- Bir [çözüm dosyasının]( solutions-solution-file.md)yapısı.
- [Kaynak Yöneticisi şablonlarını yazma](../../azure-resource-manager/resource-group-authoring-templates.md)

## <a name="automation-account"></a>Otomasyon hesabı
Azure Otomasyonu 'ndaki tüm kaynaklar bir [Otomasyon hesabında](../../automation/automation-security-overview.md#automation-account-overview)yer alır.  [Log Analytics çalışma alanı ve Otomasyon hesabı]( solutions.md#log-analytics-workspace-and-automation-account) ' nda açıklandığı gibi, Otomasyon hesabı yönetim çözümüne dahil değildir ancak çözüm yüklenmeden önce var olmalıdır.  Kullanılabilir değilse, çözüm yüklemesi başarısız olur.

Her Otomasyon kaynağının adı, Otomasyon hesabının adını içerir.  Bu, runbook kaynağı aşağıdaki örneğinde olduğu gibi **AccountName** parametresi ile çözümde yapılır.

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbook'lar
Çözüm yüklenirken çözüm tarafından kullanılan tüm runbook 'ları, çözüm yüklendiğinde oluşturulacak şekilde dahil etmelisiniz.  Runbook 'un gövdesini de ekleyemezsiniz, bu nedenle runbook 'u çözümünüzü yükleyen herhangi bir kullanıcı tarafından erişilebilen genel bir konuma yayımlamanız gerekir.

[Azure Otomasyonu runbook](../../automation/automation-runbook-types.md) kaynakları, **Microsoft. Automation/automationaccounts/runbook** türlerine sahiptir ve aşağıdaki yapıya sahiptir. Bu kod parçacığını kopyalayıp çözüm dosyanıza yapıştırarak parametre adlarını değiştirebilmeniz için ortak değişkenleri ve parametreleri içerir. 

    {
        "name": "[concat(parameters('accountName'), '/', variables('Runbook').Name)]",
        "type": "Microsoft.Automation/automationAccounts/runbooks",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "dependsOn": [
        ],
        "location": "[parameters('regionId')]",
        "tags": { },
        "properties": {
            "runbookType": "[variables('Runbook').Type]",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('Runbook').Description]",
            "publishContentLink": {
                "uri": "[variables('Runbook').Uri]",
                "version": [variables('Runbook').Version]"
            }
        }
    }


Runbook 'lar için özellikler aşağıdaki tabloda açıklanmıştır.

| Özellik | Açıklama |
|:--- |:--- |
| runbookType |Runbook 'un türlerini belirtir. <br><br> Betik-PowerShell betiği <br>PowerShell-PowerShell iş akışı <br> GraphPowerShell-grafik PowerShell betiği runbook 'u <br> GraphPowerShellWorkflow-grafik PowerShell iş akışı runbook 'u |
| logProgress |Runbook için [ilerleme kayıtlarının](../../automation/automation-runbook-output-and-messages.md) oluşturulup oluşturulmayacağını belirtir. |
| logVerbose |Runbook için [ayrıntılı kayıtların](../../automation/automation-runbook-output-and-messages.md) oluşturulup oluşturulmayacağını belirtir. |
| açıklama |Runbook için isteğe bağlı açıklama. |
| publishContentLink |Runbook 'un içeriğini belirtir. <br><br>Runbook 'un içeriğine URI URI 'si.  Bu, PowerShell ve betik runbook 'ları için bir. ps1 dosyası ve bir grafik runbook 'u için aktarılmış bir grafik runbook dosyası olacaktır.  <br> kendi izleme için Runbook 'un sürüm sürümü. |


## <a name="automation-jobs"></a>Otomasyon işleri
Azure Otomasyonu 'nda bir runbook başlattığınızda bir Otomasyon işi oluşturulur.  Yönetim çözümü yüklenirken bir runbook 'u otomatik olarak başlatmak için çözümünüze bir Otomasyon işi kaynağı ekleyebilirsiniz.  Bu yöntem genellikle çözümün ilk yapılandırması için kullanılan runbook 'ları başlatmak için kullanılır.  Düzenli aralıklarla bir runbook başlatmak için bir [zamanlama](#schedules) ve [iş zamanlaması](#job-schedules) oluşturun

İş kaynakları bir **Microsoft. Automation/automationAccounts/Jobs** türüne sahiptir ve aşağıdaki yapıya sahiptir.  Bu kod parçacığını kopyalayıp çözüm dosyanıza yapıştırarak parametre adlarını değiştirebilmeniz için ortak değişkenleri ve parametreleri içerir. 

    {
      "name": "[concat(parameters('accountName'), '/', parameters('Runbook').JobGuid)]",
      "type": "Microsoft.Automation/automationAccounts/jobs",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('Runbook').Name)]"
      ],
      "tags": { },
      "properties": {
        "runbook": {
          "name": "[variables('Runbook').Name]"
        },
        "parameters": {
          "Parameter1": "[[variables('Runbook').Parameter1]",
          "Parameter2": "[[variables('Runbook').Parameter2]"
        }
      }
    }

Otomasyon işlerinin özellikleri aşağıdaki tabloda açıklanmıştır.

| Özellik | Açıklama |
|:--- |:--- |
| 'unu |Başlatılacak runbook 'un adına sahip tek bir ad varlığı. |
| parametreler |Runbook 'un gerektirdiği her parametre değeri için varlık. |

İş Runbook adı ve Runbook 'a gönderilecek parametre değerlerini içerir.  İş, runbook 'un işten önce oluşturulması gerektiğinden runbook 'un başladığı runbook ['a bağlı]( solutions-solution-file.md#resources) olmalıdır.  Başlatılması gereken birden çok runbook 'unuz varsa, bir işi, önce çalıştırılması gereken diğer işlere bağımlı olacak şekilde tanımlayabilirsiniz.

Bir iş kaynağının adı, genellikle bir parametre tarafından atanan GUID içermelidir.  [Azure 'da bir yönetim çözümü dosyası oluşturma]( solutions-solution-file.md#parameters)bölümünde GUID parametreleri hakkında daha fazla bilgi edinebilirsiniz.  


## <a name="certificates"></a>Sertifikalar
[Azure Otomasyonu sertifikalarında](../../automation/automation-certificates.md) bir **Microsoft. Automation/automationaccounts/Certificates** türü vardır ve aşağıdaki yapıya sahiptir. Bu kod parçacığını kopyalayıp çözüm dosyanıza yapıştırarak parametre adlarını değiştirebilmeniz için ortak değişkenleri ve parametreleri içerir. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Certificate').Name)]",
      "type": "Microsoft.Automation/automationAccounts/certificates",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "base64Value": "[variables('Certificate').Base64Value]",
        "thumbprint": "[variables('Certificate').Thumbprint]"
      }
    }



Sertifika kaynaklarının özellikleri aşağıdaki tabloda açıklanmıştır.

| Özellik | Açıklama |
|:--- |:--- |
| Base64value değeri |Sertifika için temel 64 değeri. |
| #c0 |Sertifika için parmak izi. |



## <a name="credentials"></a>Kimlik bilgileri
[Azure Otomasyonu kimlik bilgileri](../../automation/automation-credentials.md) bir tür **Microsoft. Automation/automationaccounts/Credentials** ve aşağıdaki yapıya sahiptir.  Bu kod parçacığını kopyalayıp çözüm dosyanıza yapıştırarak parametre adlarını değiştirebilmeniz için ortak değişkenleri ve parametreleri içerir. 


    {
      "name": "[concat(parameters('accountName'), '/', variables('Credential').Name)]",
      "type": "Microsoft.Automation/automationAccounts/credentials",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "userName": "[parameters('credentialUsername')]",
        "password": "[parameters('credentialPassword')]"
      }
    }

Kimlik bilgileri kaynaklarının özellikleri aşağıdaki tabloda açıklanmıştır.

| Özellik | Açıklama |
|:--- |:--- |
| Nitelen |Kimlik bilgisinin Kullanıcı adı. |
| password |Kimlik bilgileri için parola. |


## <a name="schedules"></a>Zamanlamalar
[Azure Otomasyonu zamanlamalarının](../../automation/automation-schedules.md) türü **Microsoft. Automation/automationaccounts/zamanlamaları** vardır ve aşağıdaki yapıya sahiptir. Bu kod parçacığını kopyalayıp çözüm dosyanıza yapıştırarak parametre adlarını değiştirebilmeniz için ortak değişkenleri ve parametreleri içerir. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Schedule').Name)]",
      "type": "microsoft.automation/automationAccounts/schedules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "description": "[variables('Schedule').Description]",
        "startTime": "[parameters('scheduleStartTime')]",
        "timeZone": "[parameters('scheduleTimeZone')]",
        "isEnabled": "[variables('Schedule').IsEnabled]",
        "interval": "[variables('Schedule').Interval]",
        "frequency": "[variables('Schedule').Frequency]"
      }
    }

Zamanlama kaynaklarının özellikleri aşağıdaki tabloda açıklanmıştır.

| Özellik | Açıklama |
|:--- |:--- |
| açıklama |Zamanlama için isteğe bağlı açıklama. |
| startTime |Bir zamanlamanın başlangıç saatini DateTime nesnesi olarak belirtir. Geçerli bir tarih saat değerine dönüştürülebiliyorsanız bir dize belirtilebilir. |
| IsEnabled |Zamanlamanın etkinleştirilip etkinleştirilmeyeceğini belirtir. |
| interval |Zamanlama için Aralık türü.<br><br>günündeki<br>saat |
| frequency |Zamanlamanın gün veya saat sayısında tetiklenmesi gereken sıklık. |

Zamanlamalar, geçerli zamandan daha büyük bir değere sahip bir başlangıç saatine sahip olmalıdır.  Ne zaman yükleneceğini bilmenin bir yolu olmadığından, bu değeri bir değişkenle birlikte belirtemezsiniz.

Bir çözümde kaynakları zamanlamayı kullanırken aşağıdaki iki stratejiden birini kullanın.

- Zamanlamanın başlangıç saati için bir parametre kullanın.  Bu işlem, kullanıcıdan çözümü yüklediklerinde bir değer sağlamasını ister.  Birden çok zamanlamalarınız varsa, birden fazla tane için tek bir parametre değeri kullanabilirsiniz.
- Çözüm yüklendiğinde başlayan bir runbook 'u kullanarak zamanlamalar oluşturun.  Bu, kullanıcının gereksinimini bir süre belirtmektir, ancak çözümünüzde zamanlamayı, çözüm kaldırıldığında kaldırılacak şekilde bulunduramıyoruz.


### <a name="job-schedules"></a>İş zamanlamaları
İş zamanlaması kaynakları bir runbook 'u zamanlamaya göre bağlar.  Bunlar **Microsoft. Automation/automationAccounts/Jobzamanlamalar** türüne sahiptir ve aşağıdaki yapıya sahiptir.  Bu kod parçacığını kopyalayıp çözüm dosyanıza yapıştırarak parametre adlarını değiştirebilmeniz için ortak değişkenleri ve parametreleri içerir. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Schedule').LinkGuid)]",
      "type": "microsoft.automation/automationAccounts/jobSchedules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "dependsOn": [
        "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
        "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]"
      ],
      "tags": {
      },
      "properties": {
        "schedule": {
          "name": "[variables('Schedule').Name]"
        },
        "runbook": {
          "name": "[variables('Runbook').Name]"
        }
      }
    }


İş zamanlamalarının özellikleri aşağıdaki tabloda açıklanmıştır.

| Özellik | Açıklama |
|:--- |:--- |
| Zamanlama adı |Zamanlamanın adına sahip tek bir **ad** varlığı. |
| Runbook adı  |Runbook 'un adına sahip tek bir **ad** varlığı.  |



## <a name="variables"></a>Değişkenler
[Azure Otomasyonu değişkenlerinde](../../automation/automation-variables.md) bir tür **Microsoft. Automation/automationaccounts/Variable** vardır ve aşağıdaki yapıya sahiptir.  Bu kod parçacığını kopyalayıp çözüm dosyanıza yapıştırarak parametre adlarını değiştirebilmeniz için ortak değişkenleri ve parametreleri içerir.

    {
      "name": "[concat(parameters('accountName'), '/', variables('Variable').Name)]",
      "type": "microsoft.automation/automationAccounts/variables",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "description": "[variables('Variable').Description]",
        "isEncrypted": "[variables('Variable').Encrypted]",
        "type": "[variables('Variable').Type]",
        "value": "[variables('Variable').Value]"
      }
    }

Değişken kaynakların özellikleri aşağıdaki tabloda açıklanmıştır.

| Özellik | Açıklama |
|:--- |:--- |
| açıklama | Değişken için isteğe bağlı açıklama. |
| IsEncrypted | Değişkenin şifrelenmesi gerekip gerekmediğini belirtir. |
| type | Bu özelliğin şu anda hiçbir etkisi yoktur.  Değişkenin veri türü başlangıçtaki değere göre belirlenir. |
| değer | Değişkenin değeri. |

> [!NOTE]
> **Type** özelliğinin Şu anda oluşturulmakta olan değişken üzerinde hiçbir etkisi yoktur.  Değişken için veri türü değere göre belirlenir.  

Değişken için ilk değeri ayarlarsanız, doğru veri türü olarak yapılandırılmalıdır.  Aşağıdaki tabloda, izin verilen farklı veri türleri ve bunların sözdizimi verilmiştir.  JSON 'daki değerlerin tekliflerin içindeki özel karakterlerle her zaman tırnak içine alınması beklendiğini unutmayın.  Örneğin, bir dize değeri, dize etrafında tırnak işareti (kaçış karakteri (\\) kullanılarak), bir sayısal değer tek tırnak kümesiyle belirtiken belirtilir.

| Veri türü | Açıklama | Örnek | Çözümler |
|:--|:--|:--|:--|
| string   | Değeri çift tırnak içine alın.  | "\"Hello dünya \"" | "Merhaba Dünya" |
| rakamlardan  | Tek tırnak ile sayısal değer.| "64" | 64 |
| boole  | tırnak içinde **true** veya **false** .  Bu değerin küçük harf olması gerektiğini unutmayın. | değeri | doğru |
| datetime | Serileştirilmiş tarih değeri.<br>Bu değeri belirli bir tarih için oluşturmak üzere PowerShell 'de ConvertTo-JSON cmdlet 'ini kullanabilirsiniz.<br>Örnek: Get-Date "5/24/2017 13:14:57" \| ConvertTo-JSON | "\\/Date (1495656897378) \\/" | 2017-05-24 13:14:57 |

## <a name="modules"></a>Modüller
Yönetim çözümünüzün, her zaman Otomasyon hesabınızda kullanılabilir olacağı için Runbook 'larınız tarafından kullanılan [genel modülleri](../../automation/automation-integration-modules.md) tanımlamasına gerek yoktur.  Runbook 'larınız tarafından kullanılan başka bir modül için bir kaynak eklemeniz gerekir.

[Tümleştirme modüllerinin](../../automation/automation-integration-modules.md) bir tür **Microsoft. Automation/automationaccounts/modülleri** vardır ve aşağıdaki yapıya sahiptir.  Bu kod parçacığını kopyalayıp çözüm dosyanıza yapıştırarak parametre adlarını değiştirebilmeniz için ortak değişkenleri ve parametreleri içerir.

    {
      "name": "[concat(parameters('accountName'), '/', variables('Module').Name)]",
      "type": "Microsoft.Automation/automationAccounts/modules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "dependsOn": [
      ],
      "properties": {
        "contentLink": {
          "uri": "[variables('Module').Uri]"
        }
      }
    }


Modül kaynaklarının özellikleri aşağıdaki tabloda açıklanmıştır.

| Özellik | Açıklama |
|:--- |:--- |
| contentLink |Modülün içeriğini belirtir. <br><br>modülün içeriğine URI URI 'si.  Bu, PowerShell ve betik runbook 'ları için bir. ps1 dosyası ve bir grafik runbook 'u için aktarılmış bir grafik runbook dosyası olacaktır.  <br> kendi izleme için modülün sürüm sürümü. |

Runbook 'un, runbook 'tan önce oluşturulduğundan emin olmak için modül kaynağına bağlı olması gerekir.

### <a name="updating-modules"></a>Modüller güncelleştiriliyor
Bir zamanlama kullanan bir runbook içeren bir yönetim çözümünü güncelleştirirseniz ve çözümünüzün yeni sürümü bu runbook tarafından kullanılan yeni bir modüle sahipse, runbook modülün eski sürümünü kullanabilir.  Çözümünüze aşağıdaki runbook 'ları eklemeniz ve diğer runbook 'lardan önce çalıştırmak için bir iş oluşturmanız gerekir.  Bu, runbook 'ları yüklemeden önce tüm modüllerin gerekli olarak güncelleştirilmesini sağlayacaktır.

* [Update-ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/) , çözümünüzde runbook 'lar tarafından kullanılan tüm modüllerin en son sürümü olmasını sağlayacaktır.  
* [ReRegisterAutomationSchedule-MS-MGMT](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/) , bunlarla bağlantılı runbook 'ların en son modülleri kullandığından emin olmak için tüm zamanlama kaynaklarını yeniden kaydettirirsiniz.




## <a name="sample"></a>Örnek
Aşağıdaki kaynakları içeren bir çözümün örneği aşağıda verilmiştir:

- 'Unu.  Bu, genel bir GitHub deposunda depolanan örnek bir runbook örneğidir.
- Çözüm yüklendiğinde runbook 'u Başlatan Otomasyon işi.
- Düzenli aralıklarla runbook 'u başlatmak için zamanlama ve iş zamanlaması.
- Sertifika.
- Credential.
- Değişken.
- Birimi.  Bu, Log Analytics veri yazmak için [Omsingestionapı modülüdür](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) . 

Örnek, kaynak tanımlarındaki değerlerin kodlanması aksine yaygın olarak bir çözümde kullanılan [standart çözüm parametreleri]( solutions-solution-file.md#parameters) değişkenlerini kullanır.


    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceName": {
          "type": "string",
          "metadata": {
            "Description": "Name of Log Analytics workspace."
          }
        },
        "accountName": {
          "type": "string",
          "metadata": {
            "Description": "Name of Automation account."
          }
        },
        "workspaceregionId": {
          "type": "string",
          "metadata": {
            "Description": "Region of Log Analytics workspace."
          }
        },
        "regionId": {
          "type": "string",
          "metadata": {
            "Description": "Region of Automation account."
          }
        },
        "pricingTier": {
          "type": "string",
          "metadata": {
            "Description": "Pricing tier of both Log Analytics workspace and Azure Automation account."
          }
        },
        "certificateBase64Value": {
          "type": "string",
          "metadata": {
            "Description": "Base 64 value for certificate."
          }
        },
        "certificateThumbprint": {
          "type": "securestring",
          "metadata": {
            "Description": "Thumbprint for certificate."
          }
        },
        "credentialUsername": {
          "type": "string",
          "metadata": {
            "Description": "Username for credential."
          }
        },
        "credentialPassword": {
          "type": "securestring",
          "metadata": {
            "Description": "Password for credential."
          }
        },
        "scheduleStartTime": {
          "type": "string",
          "metadata": {
            "Description": "Start time for schedule."
          }
        },
        "scheduleTimeZone": {
          "type": "string",
          "metadata": {
            "Description": "Time zone for schedule."
          }
        },
        "scheduleLinkGuid": {
          "type": "string",
          "metadata": {
            "description": "GUID for the schedule link to runbook.",
            "control": "guid"
          }
        },
        "runbookJobGuid": {
          "type": "string",
          "metadata": {
            "description": "GUID for the runbook job.",
            "control": "guid"
          }
        }
      },
      "variables": {
        "SolutionName": "MySolution",
        "SolutionVersion": "1.0",
        "SolutionPublisher": "Contoso",
        "ProductName": "SampleSolution",
    
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31",
    
        "Runbook": {
          "Name": "MyRunbook",
          "Description": "Sample runbook",
          "Type": "PowerShell",
          "Uri": "https://raw.githubusercontent.com/user/myrepo/master/samples/MyRunbook.ps1",
          "JobGuid": "[parameters('runbookJobGuid')]"
        },
    
        "Certificate": {
          "Name": "MyCertificate",
          "Base64Value": "[parameters('certificateBase64Value')]",
          "Thumbprint": "[parameters('certificateThumbprint')]"
        },
    
        "Credential": {
          "Name": "MyCredential",
          "UserName": "[parameters('credentialUsername')]",
          "Password": "[parameters('credentialPassword')]"
        },
    
        "Schedule": {
          "Name": "MySchedule",
          "Description": "Sample schedule",
          "IsEnabled": "true",
          "Interval": "1",
          "Frequency": "hour",
          "StartTime": "[parameters('scheduleStartTime')]",
          "TimeZone": "[parameters('scheduleTimeZone')]",
          "LinkGuid": "[parameters('scheduleLinkGuid')]"
        },
    
        "Variable": {
          "Name": "MyVariable",
          "Description": "Sample variable",
          "Encrypted": 0,
          "Type": "string",
          "Value": "'This is my string value.'"
        },
    
        "Module": {
          "Name": "OMSIngestionAPI",
          "Uri": "https://devopsgallerystorage.blob.core.windows.net/packages/omsingestionapi.1.3.0.nupkg"
        }
      },
      "resources": [
        {
          "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
          "location": "[parameters('workspaceRegionId')]",
          "tags": { },
          "type": "Microsoft.OperationsManagement/solutions",
          "apiVersion": "[variables('LogAnalyticsApiVersion')]",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/jobs/', parameters('accountName'), variables('Runbook').JobGuid)]",
            "[resourceId('Microsoft.Automation/automationAccounts/certificates/', parameters('accountName'), variables('Certificate').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/credentials/', parameters('accountName'), variables('Credential').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/jobSchedules/', parameters('accountName'), variables('Schedule').LinkGuid)]",
            "[resourceId('Microsoft.Automation/automationAccounts/variables/', parameters('accountName'), variables('Variable').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/modules/', parameters('accountName'), variables('Module').Name)]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
            "referencedResources": [
              "[resourceId('Microsoft.Automation/automationAccounts/modules/', parameters('accountName'), variables('Module').Name)]"
            ],
            "containedResources": [
              "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/jobs/', parameters('accountName'), variables('Runbook').JobGuid)]",
              "[resourceId('Microsoft.Automation/automationAccounts/certificates/', parameters('accountName'), variables('Certificate').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/credentials/', parameters('accountName'), variables('Credential').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/jobSchedules/', parameters('accountName'), variables('Schedule').LinkGuid)]",
              "[resourceId('Microsoft.Automation/automationAccounts/variables/', parameters('accountName'), variables('Variable').Name)]"
            ]
          },
          "plan": {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspaceName'), ']')]",
            "Version": "[variables('SolutionVersion')]",
            "product": "[variables('ProductName')]",
            "publisher": "[variables('SolutionPublisher')]",
            "promotionCode": ""
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Runbook').Name)]",
          "type": "Microsoft.Automation/automationAccounts/runbooks",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "location": "[parameters('regionId')]",
          "tags": { },
          "properties": {
            "runbookType": "[variables('Runbook').Type]",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('Runbook').Description]",
            "publishContentLink": {
              "uri": "[variables('Runbook').Uri]",
              "version": "1.0.0.0"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Runbook').JobGuid)]",
          "type": "Microsoft.Automation/automationAccounts/jobs",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('Runbook').Name)]"
          ],
          "tags": { },
          "properties": {
            "runbook": {
              "name": "[variables('Runbook').Name]"
            },
            "parameters": {
              "targetSubscriptionId": "[subscription().subscriptionId]",
              "resourcegroup": "[resourceGroup().name]",
              "automationaccount": "[parameters('accountName')]"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Certificate').Name)]",
          "type": "Microsoft.Automation/automationAccounts/certificates",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "Base64Value": "[variables('Certificate').Base64Value]",
            "Thumbprint": "[variables('Certificate').Thumbprint]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Credential').Name)]",
          "type": "Microsoft.Automation/automationAccounts/credentials",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "userName": "[variables('Credential').UserName]",
            "password": "[variables('Credential').Password]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Schedule').Name)]",
          "type": "microsoft.automation/automationAccounts/schedules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "description": "[variables('Schedule').Description]",
            "startTime": "[variables('Schedule').StartTime]",
            "timeZone": "[variables('Schedule').TimeZone]",
            "isEnabled": "[variables('Schedule').IsEnabled]",
            "interval": "[variables('Schedule').Interval]",
            "frequency": "[variables('Schedule').Frequency]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Schedule').LinkGuid)]",
          "type": "microsoft.automation/automationAccounts/jobSchedules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]"
          ],
          "tags": {
          },
          "properties": {
            "schedule": {
              "name": "[variables('Schedule').Name]"
            },
            "runbook": {
              "name": "[variables('Runbook').Name]"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Variable').Name)]",
          "type": "microsoft.automation/automationAccounts/variables",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "description": "[variables('Variable').Description]",
            "isEncrypted": "[variables('Variable').Encrypted]",
            "type": "[variables('Variable').Type]",
            "value": "[variables('Variable').Value]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Module').Name)]",
          "type": "Microsoft.Automation/automationAccounts/modules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "properties": {
            "contentLink": {
              "uri": "[variables('Module').Uri]"
            }
          }
        }
    
      ],
      "outputs": { }
    }




## <a name="next-steps"></a>Sonraki adımlar
* Toplanan verileri görselleştirmek için [çözümünüze bir görünüm ekleyin]( solutions-resources-views.md) .
