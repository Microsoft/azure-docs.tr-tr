---
title: Karma ortam için Azure Izleyicisini (Önizleme) etkinleştirme | Microsoft Docs
description: Bu makalede, bir veya daha fazla sanal makine içeren karma bulut ortamı için VM'ler için Azure İzleyici nasıl etkinleştirileceği açıklanır.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: e9e5504125920cedaf383f8fa4299a4b1b1d60ed
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553859"
---
# <a name="enable-azure-monitor-for-vms-preview-for-a-hybrid-environment"></a>Karma ortam için VM'ler için Azure İzleyici (Önizleme) etkinleştirme

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Bu makalede, veri merkezinizde veya diğer bulut ortamınızda barındırılan sanal makineler veya fiziksel bilgisayarlar için VM'ler için Azure İzleyici (Önizleme) nasıl etkinleştirileceği açıklanır. Bu işlemin sonunda, ortamınızdaki sanal makinelerinizi izlemeye başarıyla başlacaksınız ve performans veya kullanılabilirlik sorunlarıyla karşılaşmadığını öğrenirsiniz. 

Başlamadan önce, [önkoşulları](vminsights-enable-overview.md) gözden geçirdiğinizden ve aboneliğinizin ve kaynaklarınızın gereksinimleri karşıladığından emin olun. [Linux ve Windows aracısı Log Analytics](../../log-analytics/log-analytics-agent-overview.md)için gereksinimleri ve dağıtım yöntemlerini gözden geçirin.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>VM'ler için Azure İzleyici Map bağımlılık Aracısı herhangi bir veri iletmez ve güvenlik duvarları veya bağlantı noktalarında değişiklik gerektirmez. Harita verileri her zaman doğrudan veya [Operations Management Suite ağ geçidi](../../azure-monitor/platform/gateway.md) aracılığıyla Log Analytics ARACıSıDıR ve BT güvenlik ilkeleriniz ağdaki bilgisayarların internet 'e bağlanmasına izin vermez.

Bu görevi tamamlamaya yönelik adımlar şu şekilde özetlenmiştir:

1. Windows veya Linux için Log Analytics Aracısı 'nı yükler. Aracıyı yüklemeden önce, sistem önkoşullarını ve dağıtım yöntemlerini anlamak için [Log Analytics aracısına genel bakış](../platform/log-analytics-agent.md) makalesini gözden geçirin.

2. [Windows](https://aka.ms/dependencyagentwindows) veya [Linux](https://aka.ms/dependencyagentlinux)için VM'ler için Azure izleyici Map bağımlılık aracısını indirip yükleyin.

3. Performans sayaçları koleksiyonunu etkinleştirin.

4. VM'ler için Azure İzleyici dağıtın.

>[!NOTE]
>Bağımlılık aracısını dağıtmaya yönelik bu makalede açıklanan bilgiler [hizmet eşlemesi çözümü](service-map.md)için de geçerlidir.  

## <a name="install-the-dependency-agent-on-windows"></a>Windows 'a bağımlılık Aracısı 'nı yükler

@No__t_0 çalıştırarak, Windows bilgisayarlarına bağımlılık aracısını el ile yükleyebilirsiniz. Bu yürütülebilir dosyayı herhangi bir seçenek olmadan çalıştırırsanız, aracı etkileşimli olarak yüklemek için izleyebileceğiniz bir Kurulum Sihirbazı başlatılır.

>[!NOTE]
>Aracıyı yüklemek veya kaldırmak için *yönetici* ayrıcalıkları gerekir.

Aşağıdaki tabloda, komut satırından aracının kurulumu tarafından desteklenen parametreler vurgulanmıştır.

| Parametre | Açıklama |
|:--|:--|
| /? | Komut satırı seçeneklerinin bir listesini döndürür. |
| Sn | Kullanıcı etkileşimi olmadan sessiz yükleme gerçekleştirir. |

Örneğin, yükleme programını `/?` parametresiyle çalıştırmak için **InstallDependencyAgent-Windows. exe/?** girin.

Windows bağımlılık aracısının dosyaları varsayılan olarak *C:\Program Files\Microsoft Dependency Agent* 'e yüklenir. Kurulum tamamlandıktan sonra bağımlılık Aracısı başlatılamazsa, ayrıntılı hata bilgileri için günlüklere bakın. Günlük dizini *%ProgramFiles%\Microsoft Dependency Fıles\logs*dizinidir.

## <a name="install-the-dependency-agent-on-linux"></a>Linux 'a bağımlılık Aracısı 'nı yükler

Bağımlılık Aracısı, kendi kendine ayıklanan ikiliye sahip bir kabuk betiği olan *InstallDependencyAgent-linux64. bin*' dan Linux sunucularına yüklenir. Dosyayı `sh` kullanarak çalıştırabilir veya dosya üzerinde yürütme izinleri ekleyebilirsiniz.

>[!NOTE]
> Aracıyı yüklemek veya yapılandırmak için kök erişimi gerekir.
>

| Parametre | Açıklama |
|:--|:--|
| -yardım | Komut satırı seçeneklerinin listesini alır. |
| -s | Kullanıcıdan bilgi istenmeden sessiz yükleme gerçekleştirir. |
| --denetle | İzinleri ve işletim sistemini denetleyin, ancak aracıyı yüklemeyin. |

Örneğin, yükleme programını `-help` parametresiyle çalıştırmak için, **InstallDependencyAgent-linux64. bin-Help**girin.

@No__t_0 komutunu çalıştırarak Linux bağımlılık aracısını kök olarak yükler.

Bağımlılık Aracısı başlatılamazsa, ayrıntılı hata bilgileri için günlüklere bakın. Linux aracılarında günlük dizini */var/seçenek/Microsoft/Dependency-Agent/log*olur.

Bağımlılık aracısına ait dosyalar aşağıdaki dizinlere yerleştirilir:

| Dosyalar | Konum |
|:--|:--|
| Çekirdek dosyaları | /opt/microsoft/dependency-agent |
| Günlük dosyaları | /var/opt/microsoft/dependency-agent/log |
| Yapılandırma dosyaları | /etc/opt/microsoft/dependency-agent/config |
| Hizmet yürütülebilir dosyaları | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| İkili depolama dosyaları | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>Yükleme betiği örnekleri

Bağımlılık aracısını birçok sunucuya aynı anda kolayca dağıtmak için, Windows veya Linux 'ta bağımlılık aracısını indirmek ve yüklemek üzere aşağıdaki betik örneği verilmiştir.

### <a name="powershell-script-for-windows"></a>Windows için PowerShell betiği

```powershell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```

### <a name="shell-script-for-linux"></a>Linux için kabuk betiği

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="desired-state-configuration"></a>İstenen Durum Yapılandırması

Istenen durum yapılandırması (DSC) kullanarak bağımlılık aracısını dağıtmak için, xPSDesiredStateConfiguration modülünü aşağıdaki örnek kodla kullanabilirsiniz:

```powershell
configuration ServiceMap {

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    $DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

    Node localhost
    {
        # Download and install the Dependency agent
        xRemoteFile DAPackage 
        {
            Uri = "https://aka.ms/dependencyagentwindows"
            DestinationPath = $DAPackageLocalPath
        }

        xPackage DA
        {
            Ensure="Present"
            Name = "Dependency Agent"
            Path = $DAPackageLocalPath
            Arguments = '/S'
            ProductId = ""
            InstalledCheckRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"
            InstalledCheckRegValueName = "DisplayName"
            InstalledCheckRegValueData = "Dependency Agent"
            DependsOn = "[xRemoteFile]DAPackage"
        }
    }
}
```

## <a name="enable-performance-counters"></a>Performans sayaçlarını etkinleştir

Çözüm tarafından başvurulan Log Analytics çalışma alanı, çözüm için gereken performans sayaçlarını toplamak üzere zaten yapılandırılmamışsa, bunları etkinleştirmeniz gerekir. Bunu iki şekilde yapabilirsiniz:
* [Windows ve Linux performans verileri kaynaklarında](../../azure-monitor/platform/data-sources-performance-counters.md) açıklandığı gibi el ile Log Analytics
* [Azure PowerShell galerisinde](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1) bulunan bir PowerShell betiğini indirip çalıştırarak

## <a name="deploy-azure-monitor-for-vms"></a>VM'ler için Azure İzleyici dağıt

Bu yöntem, Log Analytics çalışma alanınızda çözüm bileşenlerini etkinleştirmeye yönelik yapılandırmayı belirten bir JSON şablonu içerir.

Bir şablon kullanarak kaynakların nasıl dağıtılacağını bilmiyorsanız, bkz:
* [Kaynakları Resource Manager şablonları ve Azure PowerShell ile dağıtma](../../azure-resource-manager/resource-group-template-deploy.md)
* [Kaynak Yöneticisi şablonları ve Azure CLı ile kaynak dağıtma](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Azure CLı 'yi kullanmak için, önce CLı 'yi yerel olarak yüklemeniz ve kullanmanız gerekir. Azure CLı sürüm 2.0.27 veya üstünü çalıştırıyor olmanız gerekir. Sürümünüzü belirlemek için `az --version` ' ı çalıştırın. Azure CLı 'yı yüklemek veya yükseltmek için bkz. [Azure CLI 'Yi yüklemek](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="create-and-execute-a-template"></a>Şablon oluşturma ve yürütme

1. Aşağıdaki JSON söz dizimini kopyalayıp dosyanıza yapıştırın:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Bu dosyayı *ınstallsolutionsforvminsıghts. JSON* olarak yerel bir klasöre kaydedin.

1. *Çalışmaadı*, *Resourcegroupname*ve *WorkspaceLocation*değerlerini yakalayın. *Çalışmaadı* değeri, Log Analytics çalışma alanınızın adıdır. *WorkspaceLocation* değeri, çalışma alanının tanımlandığı bölgedir.

1. Aşağıdaki PowerShell komutunu kullanarak bu şablonu dağıtmaya hazırsınız:

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    Yapılandırma değişikliğinin tamamlanması birkaç dakika sürebilir. İşiniz bittiğinde, aşağıdakine benzer ve sonucu içeren bir ileti görüntülenir:

    ```powershell
    provisioningState       : Succeeded
    ```
   İzlemeyi etkinleştirdikten sonra, karma bilgisayar için sistem durumu ve ölçümlerini görüntüleyebilmeniz yaklaşık 10 dakika sürebilir.

## <a name="troubleshooting"></a>Sorun giderme

### <a name="vm-doesnt-appear-on-the-map"></a>VM haritada görünmüyor

Bağımlılık aracısı yüklemeniz başarılı oldu, ancak bilgisayarınızı haritada görmüyorsanız, bu adımları izleyerek sorunu tanılayın.

1. Bağımlılık Aracısı başarıyla yüklendi mi? Hizmetin yüklü ve çalışır olup olmadığını denetleyerek bunu doğrulayabilirsiniz.

    **Windows**: "Microsoft bağımlılık Aracısı" adlı hizmeti arayın. 

    **Linux**: "Microsoft-Dependency-Agent" çalıştırma işlemini arayın.

2. [Log Analytics ücretsiz fiyatlandırma katmanında](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)misiniz? Ücretsiz plan, beş adede kadar benzersiz bilgisayara izin verir. Önceki beş, artık veri göndermese bile, sonraki tüm bilgisayarlar haritada gösterilmez.

3. Bilgisayar Azure Izleyici günlüklerine günlük ve performans verileri gönderiyor mu? Bilgisayarınız için aşağıdaki sorguyu gerçekleştirin: 

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    Bir veya daha fazla sonuç döndürüyor mu? Veriler son zamanlarda mı? Bu durumda, Log Analytics aracınız doğru şekilde çalışıyor ve hizmetle iletişim kuruyor. Aksi takdirde, sunucunuzdaki aracıyı denetleyin: [Windows sorun giderme için Log Analytics Aracısı](../platform/agent-windows-troubleshoot.md) veya [Linux sorun giderme için Log Analytics Aracısı](../platform/agent-linux-troubleshoot.md).

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>Bilgisayar haritada görünüyor ancak işleme sahip değil

Sunucunuzu haritada görürseniz, ancak bir işlem veya bağlantı verisi yoksa, bağımlılık aracısının yüklü olduğunu ve çalıştığını, ancak çekirdek sürücüsünün yüklenmediğini belirtir. 

C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log dosyasını (Windows) veya/var/seçenek/Microsoft/Dependency-Agent/log/Service.log dosyasını (Linux) denetleyin. Dosyanın son satırları, çekirdeğin neden yüklenmediğini göstermelidir. Örneğin, çekirdeğini güncelleştirdiyseniz, çekirdek Linux üzerinde desteklenmeyebilir.


## <a name="next-steps"></a>Sonraki adımlar

Artık sanal makineleriniz için izleme etkin olduğuna göre, bu bilgiler VM'ler için Azure İzleyici analiz için kullanılabilir.
 
- Bulunan uygulama bağımlılıklarını görüntülemek için bkz. [VM'ler için Azure izleyici haritasını görüntüleme](vminsights-maps.md).

- VM performanlarınızın performans sorunlarını ve genel kullanımını belirlemek için bkz. [Azure VM performansını görüntüleme](vminsights-performance.md).
