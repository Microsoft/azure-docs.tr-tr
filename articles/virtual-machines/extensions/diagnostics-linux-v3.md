---
title: Azure Işlem-Linux Tanılama Uzantısı 3,0
description: Azure 'da çalışan Linux sanal makinelerinden ölçümleri ve günlük olaylarını toplamak için Azure Linux Tanılama uzantısı 'nı (LAD 3,0) yapılandırma.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 12/13/2018
ms.openlocfilehash: fe03bbfb33f3637eecc4e68f24846c929dad5fa4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479262"
---
# <a name="use-linux-diagnostic-extension-30-to-monitor-metrics-and-logs"></a>Ölçümleri ve günlükleri izlemek için Linux Tanılama Uzantısı 3,0 kullanın

Bu belgede, sürüm 3,0 ve Linux Tanılama uzantısının (LAD) daha yeni bir sürümü açıklanmaktadır.

> [!IMPORTANT]
> Sürüm 2,3 ve önceki sürümler hakkında daha fazla bilgi için bkz. [bir LINUX VM 'nin performans ve tanılama verilerini izleme](https://docs.microsoft.com/previous-versions/azure/virtual-machines/linux/classic/diagnostic-extension-v2).

## <a name="introduction"></a>Giriş

Linux Tanılama uzantısı, bir kullanıcının Microsoft Azure üzerinde çalışan bir Linux sanal makinesinin sistem durumunu izlemesine yardımcı olur. Aşağıdaki özellikleri içerir:

* VM 'den sistem performans ölçümlerini toplar ve bunları belirlenen depolama hesabındaki belirli bir tabloda depolar.
* Syslog 'dan günlük olaylarını alır ve bunları belirlenen depolama hesabındaki belirli bir tabloya depolar.
* Kullanıcıların toplanan ve yüklenen veri ölçümlerini özelleştirmesini sağlar.
* Kullanıcıların, toplanan ve yüklenen olayların Syslog özelliklerini ve önem düzeyleri özelleştirmesini sağlar.
* Kullanıcıların belirtilen günlük dosyalarını belirlenmiş bir depolama tablosuna karşıya yüklemesine olanak sağlar.
* , Belirlenen depolama hesabındaki rastgele Azure Event Hubs uç noktalarına ve JSON biçimli bloblara ölçüm ve günlük olayları göndermeyi destekler.

Bu uzantı, hem Azure dağıtım modelleriyle birlikte çalışmaktadır.

## <a name="install-the-extension-on-a-vm"></a>Uzantıyı bir VM 'ye yükler

Uzantıyı Azure PowerShell cmdlet 'leri, Azure CLı betikleri, Azure Kaynak İzleyicisi şablonları (ARM şablonları) veya Azure portal kullanarak etkinleştirebilirsiniz. Daha fazla bilgi için bkz. [uzantı özellikleri](features-linux.md).

>[!NOTE]
>LAD VM uzantısının bazı bileşenleri [log ANALYTICS VM uzantısına](./oms-linux.md)da gönderilir. Bu mimaride, aynı ARM şablonunda her iki uzantı de örneği oluşturulduğunda çakışmalar ortaya çıkabilir. 
>
>Yükleme zamanı çakışmalarını önlemek için, uzantıların sırayla yüklü olduğundan emin olmak için [ `dependsOn` yönergesini](../../azure-resource-manager/templates/define-resource-dependency.md#dependson) kullanın. Uzantılar her iki sırayla yüklenebilir.

Bu yükleme yönergeleri ve LAD 3,0 ' i yapılandırmak için [indirilebilir bir örnek yapılandırma](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) :

* Aynı ölçümleri, LAD 2,3 ile aynı şekilde yakalayın ve saklayın.
* Kullanışlı bir dosya sistemi ölçümleri kümesi yakalayın. Bu işlevsellik, LAD 3,0 ' de yenidir.
* LAD 2,3 'nin etkinleştirildiği varsayılan Syslog koleksiyonunu yakalayın.
* VM ölçümlerinde grafik oluşturma ve uyarı verme için Azure portal deneyimini etkinleştirin.

İndirilebilir yapılandırma yalnızca bir örnektir. Gereksinimlerinize uyacak şekilde değiştirin.

### <a name="supported-linux-distributions"></a>Desteklenen Linux dağıtımları

LAD, aşağıdaki dağıtımları ve sürümleri destekler. Dağıtımların ve sürümlerin listesi yalnızca Azure tarafından onaylanan Linux satıcı görüntüleri için geçerlidir. Uzantı genellikle gereçler gibi üçüncü taraf KLG ve KCG görüntülerini desteklemez.

Yalnızca deler 7 gibi ana sürümleri listeleyen bir dağıtım, tüm küçük sürümler için de desteklenir. Küçük bir sürüm belirtilmişse, yalnızca bu sürüm desteklenir. Artı işareti (+) eklendiğinde, belirtilen sürümden veya daha sonraki bir sürüme eşit olan ikincil sürümler desteklenir.

Desteklenen dağıtımlar ve sürümler:

- Ubuntu 18,04, 16,04, 14,04
- CentOS 7, 6.5 +
- Oracle Linux 7, 6.4 +
- OpenSUSE 13.1 +
- SUSE Linux Enterprise Server 12
- 9, 8, 7
- Red Hat Enterprise Linux (RHEL) 7, 6.7 +

### <a name="prerequisites"></a>Önkoşullar

* **Azure Linux Aracısı sürüm 2.2.0 veya üzeri**. Azure VM Linux Galeri görüntülerinin çoğu, sürüm 2.2.7 veya üstünü içerir. `/usr/sbin/waagent -version`VM 'de yüklü sürümü onaylamak için ' i çalıştırın. VM daha eski bir sürüm çalıştırıyorsa, [Konuk aracısını güncelleştirin](./update-linux-agent.md).
* **Azure CLI**. Gerekirse, makinenizde [Azure CLI](/cli/azure/install-azure-cli) ortamı ' nı ayarlayın.
* **Wget komutu**. Henüz yoksa, öğesini çalıştırın `sudo apt-get install wget` .
* Mevcut bir **Azure aboneliği**. 
* Verileri depolamak için mevcut bir **genel amaçlı depolama hesabı** . Genel amaçlı depolama hesaplarının tablo depolamayı desteklemesi gerekir. BLOB depolama hesabı çalışmaz.
* **Python 2**.

### <a name="python-requirement"></a>Python gereksinimi

Linux Tanılama uzantısı Python 2 gerektirir. Sanal makineniz, varsayılan olarak Python 2 içermeyen bir dağıtım kullanıyorsa, onu yüklemelisiniz. Aşağıdaki örnek komutlar çeşitli dağıtımlarla Python 2 ' ye yüklenir:   

- Red hat, CentOS, Oracle: `yum install -y python2`
- Ubuntu, debir: `apt-get install -y python2`
- SUSE `zypper install -y python2`

`python2`Yürütülebilir dosya *Python*'un diğer adı olmalıdır. Bu diğer adı ayarlamak için bir yöntem aşağıda verilmiştir:

1. Mevcut diğer adları kaldırmak için aşağıdaki komutu çalıştırın.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. Diğer adı oluşturmak için aşağıdaki komutu çalıştırın.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

### <a name="sample-installation"></a>Örnek yükleme

Aşağıdaki örneklerde indirilen örnek yapılandırma bir dizi standart veri toplar ve onu tablo depolama alanına gönderir. Örnek yapılandırmanın URL 'SI ve içeriği değişebilir. 

Çoğu durumda, portal ayarları JSON dosyasının bir kopyasını indirmeniz ve gereksinimlerinize göre özelleştirmeniz gerekir. Ardından, her seferinde URL 'den indirmek yerine yapılandırma dosyasının özelleştirilmiş bir sürümünü kullanmak için şablonları veya kendi otomasyonunu kullanın.

> [!NOTE]
> Aşağıdaki örnekler için kodu çalıştırmadan önce ilk bölümdeki değişkenler için doğru değerleri girin. 
#### <a name="azure-cli-sample"></a>Azure CLı örneği

```azurecli
# Set your Azure VM diagnostic variables.
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Login to Azure before you do anything else.
az login

# Select the subscription that contains the storage account.
az account set --subscription <your_azure_subscription_id>

# Download the sample public settings. (You could also use curl or any web browser.)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VM resource ID. Replace the storage account name and resource ID in the public settings.
my_vm_resource_id=$(az vm show -g $my_resource_group -n $my_linux_vm --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vm_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token).
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally, tell Azure to install and enable the extension.
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```
#### <a name="azure-cli-sample-to-install-lad-30-on-the-virtual-machine-scale-set-instance"></a>Sanal makine ölçek kümesi örneğine LAD 3,0 yüklemek için Azure CLı örneği

```azurecli
#Set your Azure Virtual Machine Scale Sets diagnostic variables.
$my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
$my_linux_vmss=<your_azure_linux_vmss_name>
$my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Login to Azure before you do anything else.
az login

# Select the subscription that contains the storage account.
az account set --subscription <your_azure_subscription_id>

# Download the sample public settings. (You could also use curl or any web browser.)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the virtual machine scale set resource ID. Replace the storage account name and resource ID in the public settings.
$my_vmss_resource_id=$(az vmss show -g $my_resource_group -n $my_linux_vmss --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vmss_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token).
$my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
$my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally, tell Azure to install and enable the extension.
az vmss extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vmss-name $my_linux_vmss --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

#### <a name="powershell-sample"></a>PowerShell örneği

```powershell
$storageAccountName = "yourStorageAccountName"
$storageAccountResourceGroup = "yourStorageAccountResourceGroupName"
$vmName = "yourVMName"
$VMresourceGroup = "yourVMResourceGroupName"

# Get the VM object
$vm = Get-AzVM -Name $vmName -ResourceGroupName $VMresourceGroup

# Get the public settings template from GitHub and update the templated values for storage account and resource ID
$publicSettings = (Invoke-WebRequest -Uri https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json).Content
$publicSettings = $publicSettings.Replace('__DIAGNOSTIC_STORAGE_ACCOUNT__', $storageAccountName)
$publicSettings = $publicSettings.Replace('__VM_RESOURCE_ID__', $vm.Id)

# If you have customized public settings, you can inline those rather than using the preceding template: $publicSettings = '{"ladCfg":  { ... },}'

# Generate a SAS token for the agent to use to authenticate with the storage account
$sasToken = New-AzStorageAccountSASToken -Service Blob,Table -ResourceType Service,Container,Object -Permission "racwdlup" -Context (Get-AzStorageAccount -ResourceGroupName $storageAccountResourceGroup -AccountName $storageAccountName).Context -ExpiryTime $([System.DateTime]::Now.AddYears(10))

# Build the protected settings (storage account SAS token)
$protectedSettings="{'storageAccountName': '$storageAccountName', 'storageAccountSasToken': '$sasToken'}"

# Finally, install the extension with the settings you built
Set-AzVMExtension -ResourceGroupName $VMresourceGroup -VMName $vmName -Location $vm.Location -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0 
```

### <a name="update-the-extension-settings"></a>Uzantı ayarlarını Güncelleştir

Korumalı veya ortak ayarlarınızı değiştirdikten sonra aynı komutu çalıştırarak bunları sanal makineye dağıtın. Ayarlar değiştiyse güncelleştirmeler uzantıya gönderilir. LAD yapılandırmayı yeniden yükler ve kendisini yeniden başlatır.

### <a name="migrate-from-previous-versions-of-the-extension"></a>Uzantının önceki sürümlerinden geçirin

Uzantının en son sürümü *4,0*' dir. 

> [!IMPORTANT]
> Bu uzantı, yapılandırmada önemli değişiklikler sunar. Bu tür bir değişiklik uzantının güvenliğini iyileştirdiğinden 2. x ile geriye dönük uyumluluk korunamadı. Ayrıca, bu uzantının uzantı yayımcısı 2. x sürümleri için yayımcıdan farklıdır.
>
> 2. x sürümünden yeni sürüme geçiş yapmak için önce eski uzantıyı kaldırın (eski yayımcı adı altında). Sonra sürüm 3 ' ü yükler.

Öneriler:

* Uzantıyı otomatik ikincil sürüm yükseltmesi etkin olarak yükler.
  * Klasik dağıtım modeli VM 'lerinde, `3.*` uzantıyı Azure XPLAT CLI veya PowerShell aracılığıyla yüklüyorsanız sürümü belirtin.
  * Azure Resource Manager dağıtım modeli VM 'lerinde `"autoUpgradeMinorVersion": true` VM Dağıtım şablonuna dahil edin.
* LAD 3,0 için yeni veya farklı bir depolama hesabı kullanın. LAD 2,3 ve LAD 3,0, sorunlu hesabını paylaşmayı sağlayan birkaç küçük uyumsuzluklara sahiptir:
  * LAD 3,0, syslog olaylarını farklı bir ada sahip bir tabloda depolar.
  * `counterSpecifier`Ölçümler için dizeler, `builtin` lad 3,0 ' de farklılık gösterir.

## <a name="protected-settings"></a>Korumalı ayarlar

Bu yapılandırma bilgileri kümesi, genel görünümden korunması gereken hassas bilgiler içerir. Örneğin, depolama kimlik bilgilerini içerir. Bu ayarlar, uzantısı tarafından şifrelenmiş biçimde iletilir ve saklanır.

```json
{
    "storageAccountName" : "the storage account to receive data",
    "storageAccountEndPoint": "the hostname suffix for the cloud for this account",
    "storageAccountSasToken": "SAS access token",
    "mdsdHttpProxy": "HTTP proxy settings",
    "sinksConfig": { ... }
}
```

Name | Değer
---- | -----
storageAccountName | Verilerin uzantı tarafından yazıldığı depolama hesabının adı.
storageAccountEndPoint | Seçim Depolama hesabının bulunduğu bulutu tanımlayan uç nokta. Bu ayar yoksa, LAD varsayılan Azure genel bulutu olur `https://core.windows.net` . Azure Almanya, Azure Kamu veya Azure Çin 21Vianet 'de bir depolama hesabı kullanmak için bu değeri gereken şekilde ayarlayın.
storageAccountSasToken | Blob ve tablo Hizmetleri () için bir [Hesap SAS belirteci](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) `ss='bt'` . Kapsayıcılar ve nesneler için geçerlidir ( `srt='co'` ). Ekleme, oluşturma, listeleme, güncelleştirme ve yazma izinleri ( `sp='acluw'` ) verir. Önde gelen soru işareti (? *) eklemeyin.*
mdsdHttpProxy | Seçim Uzantının belirtilen depolama hesabına ve uç noktaya bağlanması için gereken HTTP proxy bilgileri.
sinksConfig | Seçim Ölçüm ve olayların sunulabilecek alternatif hedeflerin ayrıntıları. Aşağıdaki bölümlerde, uzantısı tarafından desteklenen her bir veri havuzunun ayrıntıları ele verilmiştir.

Bir ARM şablonu içinde SAS belirteci almak için `listAccountSas` işlevini kullanın. Örnek bir şablon için bkz. [list işlev örneği](../../azure-resource-manager/templates/template-functions-resource.md#list-example).

Gerekli SAS belirtecini Azure portal aracılığıyla oluşturabilirsiniz:

1. Uzantının yazmasını istediğiniz genel amaçlı depolama hesabını seçin.
1. Soldaki menüde, **Ayarlar**' ın altında, **paylaşılan erişim imzası**' nı seçin.
1. Seçimleri daha önce açıklanan şekilde yapın.
1. **SAS oluştur**' u seçin.

:::image type="content" source="./media/diagnostics-linux/make_sas.png" alt-text="Ekran görüntüsü, bir S oluştur düğmesi ile paylaşılan erişim imzası sayfasını gösterir.":::

Oluşturulan SAS 'yi `storageAccountSasToken` alana kopyalayın. Önde gelen soru işaretini (?) kaldırın.

### <a name="sinksconfig"></a>sinksConfig

```json
"sinksConfig": {
    "sink": [
        {
            "name": "sinkname",
            "type": "sinktype",
            ...
        },
        ...
    ]
},
```

`sinksConfig`İsteğe bağlı bölüm, uzantının topladığı bilgileri gönderdiği daha fazla hedef tanımlar. `sink`Dizi, her ek veri havuzu için bir nesne içerir. `type`Özniteliği nesnesindeki diğer öznitelikleri belirler.

Öğe | Değer
------- | -----
name | Uzantı yapılandırmasının başka bir yerinde bu havuza başvuran bir dize.
tür | Tanımlanmakta olan havuz türü. Bu türün örneklerinde diğer değerleri (varsa) belirler.

LAD sürüm 3,0 iki havuz türünü destekler: `EventHub` ve `JsonBlob` .

#### <a name="eventhub-sink"></a>EventHub havuzu

```json
"sink": [
    {
        "name": "sinkname",
        "type": "EventHub",
        "sasURL": "https SAS URL"
    },
    ...
]
```

`"sasURL"`Giriş, verilerin yayımlanması gereken olay hub 'ı IÇIN SAS belirteci dahil olmak üzere tam URL 'yi içerir. LAD, gönder talebini sağlayan bir ilkeyi adlandırmak için SAS gerektirir. 

Örnek:

* Adlı bir Azure Event Hubs ad alanı oluşturun `contosohub` .
* Adlı ad alanında bir olay hub 'ı oluşturun `syslogmsgs` .
* Olay Hub 'ında, gönderme talebini sağlayan bir paylaşılan erişim ilkesi oluşturun. İlkeyi adlandırın `writer` .

SAS 'niz 1 Ocak 2018 ' de gece yarısına kadar ise, sasURL değeri şu örnekteki gibi olabilir:

```https
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Event Hubs için SAS belirteçleriyle ilgili bilgi oluşturma ve alma hakkında daha fazla bilgi için bkz. [SAS belirteci oluşturma](/rest/api/eventhub/generate-sas-token#powershell).

#### <a name="jsonblob-sink"></a>JsonBlob havuzu

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

Bir havuza yöneltilen veriler `JsonBlob` , Azure depolama 'daki bloblarda depolanır. Her bir LAD örneği her bir havuz adı için her saat bir blob oluşturur. Her blob her zaman sözdizimsel olarak geçerli bir JSON dizisi dizisi içerir. Yeni girdiler diziye göre otomatik olarak eklenir. 

Blob 'lar, havuzla aynı ada sahip bir kapsayıcıda depolanır. Blob kapsayıcı adları için Azure depolama kuralları, havuz adları için geçerlidir `JsonBlob` . Ad 3 ila 63 küçük harf alfasayısal karakter veya tire içermelidir.

## <a name="public-settings"></a>Ortak ayarlar

Genel ayarlar yapısı, uzantının topladığı bilgileri denetleyen çeşitli ayarlar bloklarını içerir. Her ayar isteğe bağlıdır. Belirtirseniz `ladCfg` , öğesini de belirtmeniz gerekir `StorageAccount` .

```json
{
    "ladCfg":  { ... },
    "perfCfg": { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "mdsdHttpProxy" : ""
}
```

Öğe | Değer
------- | -----
StorageAccount | Verilerin uzantı tarafından yazıldığı depolama hesabının adı. [Korunan ayarlarda](#protected-settings)belirtilen ad olmalıdır.
mdsdHttpProxy | Seçim [Korumalı ayarlarınızdaki](#protected-settings)gibi. Ortak değer, ayarlandıysa özel değer tarafından geçersiz kılınır. [Korumalı ayarlar](#protected-settings)' da, parola gibi gizli dizi içeren ara sunucu ayarlarını yerleştirin.

Aşağıdaki bölümlerde kalan öğeler için ayrıntılar sağlanmaktadır.

### <a name="ladcfg"></a>El cfg

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {
        "eventVolume": "Medium",
        "metrics": { ... },
        "performanceCounters": { ... },
        "syslogEvents": { ... }
    },
    "sampleRateInSeconds": 15
}
```

`ladCfg`Yapı isteğe bağlıdır. Azure Izleyici ölçümleri hizmetine ve diğer veri havuzları 'na teslim edilen ölçüm ve günlüklerin toplanmasıyla ilgili bir denetim sağlar. Şunu belirtmeniz gerekir:

* Ya da ya da `performanceCounters` `syslogEvents` her ikisi. 
* `metrics`Yapı.

Öğe | Değer
------- | -----
eventVolume | Seçim Depolama tablosu içinde oluşturulan bölüm sayısını denetler. `"Large"`, `"Medium"` Veya olmalıdır `"Small"` . Bir değer belirtilmezse, varsayılan olarak olur `"Medium"` .
Samplerateınseconds | Seçim Ham (toplanmayan) ölçümler koleksiyonu arasındaki varsayılan Aralık. Desteklenen en küçük örnek oranı 15 saniyedir. Değer belirtilmezse, varsayılan olur `15` .

#### <a name="metrics"></a>metrics

```json
"metrics": {
    "resourceId": "/subscriptions/...",
    "metricAggregation" : [
        { "scheduledTransferPeriod" : "PT1H" },
        { "scheduledTransferPeriod" : "PT5M" }
    ]
}
```

Öğe | Değer
------- | -----
resourceId | VM 'nin veya VM 'nin ait olduğu ölçek kümesinin Azure Resource Manager kaynak KIMLIĞI. Bu ayar `JsonBlob` , yapılandırmada herhangi bir havuz kullanılıyorsa da belirtilmelidir.
scheduledTransferPeriod | Toplam ölçümlerin hesaplanacağı ve Azure Izleyici ölçümlerine aktarıldığı sıklık. Sıklık, bir 8601 zaman aralığı olarak ifade edilir. En küçük aktarım süresi 60 saniyedir, yani PT1M. En az bir belirtin `scheduledTransferPeriod` .

Bölümünde belirtilen ölçümlerin örnekleri, `performanceCounters` her 15 saniyede bir veya sayaç için açıkça tanımlanmış örnek hızda toplanır. `scheduledTransferPeriod`Örnekte olduğu gibi birden çok sıklık görünürse, her toplama bağımsız olarak hesaplanır.

#### <a name="performancecounters"></a>performanceCounters

```json
"performanceCounters": {
    "sinks": "",
    "performanceCounterConfiguration": [
        {
            "type": "builtin",
            "class": "Processor",
            "counter": "PercentIdleTime",
            "counterSpecifier": "/builtin/Processor/PercentIdleTime",
            "condition": "IsAggregate=TRUE",
            "sampleRate": "PT15S",
            "unit": "Percent",
            "annotation": [
                {
                    "displayName" : "Aggregate CPU %idle time",
                    "locale" : "en-us"
                }
            ]
        }
    ]
}
```

`performanceCounters`İsteğe bağlı bölüm, ölçüm koleksiyonunu denetler. Ham örnekler her biri için toplanır ve [`scheduledTransferPeriod`](#metrics) Bu değerleri üretir:

* Ortalama
* Minimum
* Maksimum
* Son toplanan değer
* Toplamı hesaplamak için kullanılan ham örnek sayısı

Öğe | Değer
------- | -----
yapma | Seçim LAD 'nin toplanmış ölçüm sonuçları gönderdiği havuz adlarının virgülle ayrılmış bir listesi. Tüm toplanan ölçümler listelenen her havuza yayımlanır. Örnek: `"EHsink1, myjsonsink"`. Daha fazla bilgi için bkz. [`sinksConfig`](#sinksconfig).
tür | Ölçümün gerçek sağlayıcısını tanımlar.
sınıf | , İle birlikte `"counter"` sağlayıcının ad alanındaki belirli ölçüyü tanımlar.
counter | , İle birlikte `"class"` sağlayıcının ad alanındaki belirli ölçüyü tanımlar.
Onay Belirleyicisi | Azure Izleyici ölçümleri ad alanındaki belirli ölçüyü tanımlar.
koşul | Seçim Ölçümün uygulandığı nesnenin belirli bir örneğini seçer. Ya da bu nesnenin tüm örneklerinde toplamayı seçer. 
Örnekleray | Bu ölçüm için ham örneklerin toplandığı oranı ayarlayan 8601 aralığıdır. Değer ayarlanmamışsa, koleksiyon aralığı değerine göre ayarlanır [`sampleRateInSeconds`](#ladcfg) . Desteklenen en kısa örnek hızı 15 saniyedir (PT15S).
unit | Ölçüm için birimi tanımlar. Şu dizelerden biri olmalıdır: `"Count"` ,,, `"Bytes"` `"Seconds"` `"Percent"` , `"CountPerSecond"` , `"BytesPerSecond"` , `"Millisecond"` . Toplanan verilerin tüketicileri, toplanan veri değerlerinin bu birimle eşleşmesini bekler. LAD bu alanı yoksayar.
displayName | Azure Izleyici ölçümlerinde verilere eklenecek etiket. Bu etiket, ilişkili yerel ayar ayarıyla belirtilen dilde. LAD bu alanı yoksayar.

, `counterSpecifier` Rastgele bir tanımlayıcıdır. Azure portal grafik oluşturma ve uyarı özelliği gibi ölçüm tüketicileri, ölçüm `counterSpecifier` veya ölçüm örneğini tanımlayan "anahtar" olarak kullanılır. 

`builtin`Ölçümler için `counterSpecifier` ile başlayan değerleri öneririz `/builtin/` . Bir ölçümün belirli bir örneğini topluyorsanız, örneğin tanımlayıcısını değere iliştirmenizi öneririz `counterSpecifier` . 

İşte bazı örnekler:

* `/builtin/Processor/PercentIdleTime` -Tüm vCPU 'larda ortalama boşta geçen süre
* `/builtin/Disk/FreeSpace(/mnt)`-Dosya sistemi için boş alan `/mnt`
* `/builtin/Disk/FreeSpace` -Tüm bağlı dosya sistemleri genelinde ortalama boş alan

LAD ve Azure portal, `counterSpecifier` değeri herhangi bir kalıpla eşleşecek şekilde beklemez. Değerleri oluşturma konusunda tutarlı olun `counterSpecifier` .

Belirttiğinizde `performanceCounters` , Lad verileri her zaman Azure Storage 'daki bir tabloya yazar. Aynı veriler JSON bloblarına veya Event Hubs veya her ikisine de yazılabilir. Ancak verileri bir tabloya depolamayı devre dışı bırakamıyorum. 

Aynı depolama hesabı adını ve uç noktasını kullanan tüm LAD örnekleri, ölçümlerini ve günlüklerini aynı tabloya ekler. Aynı tablo bölümüne çok fazla sayıda VM yazarsanız, Azure bu bölüme yazma işlemlerini kısıtlayabilir. 

`eventVolume`Ayar girişlerin 1 (küçük), 10 (orta) veya 100 (büyük) bölümler arasında yayılmasına neden olur. Genellikle, orta ölçekli bölümler trafik azaltmasını önlemek için yeterlidir. 

Azure portal Azure Izleyici ölçümleri özelliği, grafikler oluşturmak veya uyarıları tetiklemek için bu tablodaki verileri kullanır. Tablo adı, Bu dizelerin bitiştirilmesi:

* `WADMetrics`
* `"scheduledTransferPeriod"`Tabloda depolanan toplanmış değerler için
* `P10DV2S`
* "YYYYMMDD" biçiminde, her 10 günde bir tarih değiştiren bir tarih

Örnekler `WADMetricsPT1HP10DV2S20170410` ve içerir `WADMetricsPT1MP10DV2S20170609` .

#### <a name="syslogevents"></a>syslogEvents

```json
"syslogEvents": {
    "sinks": "",
    "syslogEventConfiguration": {
        "facilityName1": "minSeverity",
        "facilityName2": "minSeverity",
        ...
    }
}
```

`syslogEvents`İsteğe bağlı bölüm, syslog 'dan gelen günlük olayları koleksiyonunu denetler. Bölüm atlanırsa, syslog olayları hiç yakalanmaz.

`syslogEventConfiguration`Koleksiyonda, ilgilendiğiniz her Syslog özelliği için bir giriş vardır. `minSeverity` `"NONE"` Belirli bir tesis için ise veya bu tesis öğesinde hiç görünmezse, bu olanınızdan hiçbir olay yakalanmaz.

Öğe | Değer
------- | -----
yapma | Ayrı günlük olaylarının yayımlandığı havuz adlarının virgülle ayrılmış listesi. İçindeki kısıtlamalarla eşleşen tüm günlük olayları `syslogEventConfiguration` listelenen her havuza yayımlanır. Örnek: `"EHforsyslog"`
facilityName | Veya gibi bir Syslog tesis adı `"LOG_USER"` `"LOG\LOCAL0"` . Daha fazla bilgi için [Syslog Man sayfasının](http://man7.org/linux/man-pages/man3/syslog.3.html)"tesis" bölümüne bakın.
Minönem derecesi | Veya gibi bir Syslog önem derecesi düzeyi `"LOG_ERR"` `"LOG_INFO"` . Daha fazla bilgi için [Syslog Man sayfasının](http://man7.org/linux/man-pages/man3/syslog.3.html)"düzey" bölümüne bakın. Uzantı, belirtilen düzeyin üzerinde veya üzerinde tesise gönderilen olayları yakalar.

Belirttiğinizde `syslogEvents` , Lad verileri her zaman Azure Storage 'daki bir tabloya yazar. Aynı veriler JSON bloblarına veya Event Hubs veya her ikisine de yazılabilir. Ancak verileri bir tabloya depolamayı devre dışı bırakamıyorum. 

Tablo için bölümleme davranışı, konusunda açıklandığı gibi aynıdır `performanceCounters` . Tablo adı, Bu dizelerin bitiştirilmesi:

* `LinuxSyslog`
* "YYYYMMDD" biçiminde, her 10 günde bir tarih değiştiren bir tarih

Örnekler `LinuxSyslog20170410` ve içerir `LinuxSyslog20170609` .

### <a name="perfcfg"></a>perfCfg

Bu `perfCfg` bölüm isteğe bağlıdır. Rastgele [Açık yönetim altyapısı (OMı)](https://github.com/Microsoft/omi) sorgularının çalıştırılmasını denetler.

```json
"perfCfg": [
    {
        "namespace": "root/scx",
        "query": "SELECT PercentAvailableMemory, PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
        "table": "LinuxOldMemory",
        "frequency": 300,
        "sinks": ""
    }
]
```

Öğe | Değer
------- | -----
ad alanı | Seçim Sorgunun çalıştırılacağı OMı ad alanı. Belirtilmemişse, varsayılan değer `"root/scx"` . [System Center platformlar arası sağlayıcılar](https://github.com/Microsoft/SCXcore)tarafından uygulanır.
sorgu | Çalıştırılacak OMı sorgusu.
tablo | Seçim Belirtilen depolama hesabındaki Azure Storage tablosu. Daha fazla bilgi için bkz. [korumalı ayarlar](#protected-settings).
frequency | Seçim Sorgu çalıştırmaları arasındaki saniye sayısı. Varsayılan değer 300 ' dir (5 dakikadır). En küçük değer 15 saniyedir.
yapma | Seçim Ham örnek ölçüm sonuçlarının yayımlanması için daha fazla havuz adlarının virgülle ayrılmış bir listesi. Bu ham örneklerin toplaması, uzantı veya Azure Izleyici ölçümleri tarafından hesaplanmadı.

Ya da ya da `"table"` `"sinks"` her ikisi de belirtilmelidir.

### <a name="filelogs"></a>Dosya günlükleri

`fileLogs`Bölüm, günlük dosyalarının yakalanmasını denetler. LAD, dosyaya yazıldığı gibi yeni metin satırları yakalar. Bunları tablo satırlarına ve/veya belirtilen herhangi bir havuza ( `JsonBlob` veya) yazar `EventHub` .

> [!NOTE]
> , `fileLogs` LAD 'nin çağrılan bir alt bileşeni tarafından yakalanır `omsagent` . Toplanacak `fileLogs` , `omsagent` kullanıcının belirttiğiniz dosyalar üzerinde okuma izinlerine sahip olduğundan emin olun. Kullanıcının ayrıca bu dosyanın yolundaki tüm dizinlerde yürütme izinlerine sahip olması gerekir. LAD yüklendikten sonra, öğesini çalıştırarak izinleri denetleyebilirsiniz `sudo su omsagent -c 'cat /path/to/file'` .

```json
"fileLogs": [
    {
        "file": "/var/log/mydaemonlog",
        "table": "MyDaemonEvents",
        "sinks": ""
    }
]
```

Öğe | Değer
------- | -----
 dosyası | İzlenen ve yakalanan günlük dosyasının tam yol adı. Yol adının tek bir dosya adı olmalıdır. Bir dizin adını veya joker karakter içeremez. `omsagent`Kullanıcı hesabının dosya yoluna okuma erişiminin olması gerekir.
tablo | Seçim Dosyanın "Tail" içindeki yeni satırların yazıldığı Azure Storage tablosu. Tablo, korunan yapılandırmada belirtildiği gibi, belirtilen depolama hesabında olmalıdır. 
yapma | Seçim Günlük satırlarının gönderildiği daha fazla havuza ait adların virgülle ayrılmış bir listesi.

Ya da ya da `"table"` `"sinks"` her ikisi de belirtilmelidir.

## <a name="metrics-supported-by-the-builtin-provider"></a>Yerleşik sağlayıcı tarafından desteklenen ölçümler

`builtin`Ölçüm sağlayıcısı, geniş bir Kullanıcı kümesiyle en ilginç olan ölçüm kaynağıdır. Bu ölçümler beş geniş sınıfa ayrılır:

* İşlemci
* Bellek
* Ağ
* Dosya sistemi
* Disk

### <a name="builtin-metrics-for-the-processor-class"></a>Işlemci sınıfı için yerleşik ölçümler

Ölçümlerin Işlemci sınıfı, sanal makinede işlemci kullanımı hakkında bilgi sağlar. Yüzdeler toplandıktan sonra, sonuç tüm CPU 'larda ortalama olur. 

İki vCPU VM 'de, bir vCPU yüzde 100 meşgulse ve diğeri yüzde 100 boşta ise, bildirilen `PercentIdleTime` 50. Her vCPU aynı dönem için yüzde 50 meşgulse, bildirilen sonuç de 50 ' dir. Dört vCPU VM 'de, bir vCPU yüzde 100 meşgul olduğunda ve diğerleri boşta kaldığında, bildirilen `PercentIdleTime` 75 ' dir.

Sayaç | Anlamı
------- | -------
PercentIdleTime | Toplama penceresinde işlemcilerin çekirdek boşta döngüsünü çalıştırdığı sürenin yüzdesi
PercentProcessorTime | Boşta olmayan iş parçacığı çalıştırma süresi yüzdesi
PercentIOWaitTime | GÇ işlemlerinin bitmesi için bekleyen sürenin yüzdesi
PercentInterruptTime | Donanım veya yazılım kesintileri ve DPC 'leri çalıştırma süresinin yüzdesi (ertelenmiş yordam çağrıları)
PercentUserTime | Toplama penceresi sırasında boşta olmayan süre, kullanıcı modunda harcanan sürenin normal önceliğinde yüzdesi
PercentNiceTime | Boşta olmayan süre, düşürülen (iyi) öncelikte harcanan yüzde
PercentPrivilegedTime | Boşta olmayan süre, ayrıcalıklı (çekirdek) modda harcanan yüzde

İlk dört sayaç yüzde 100 ' e kadar olmalıdır. Son üç sayaç Ayrıca yüzde 100 ' i de toplayın. Bu üç sayaç,, ve toplamlarını alt bölümlere bölüdir `PercentProcessorTime` `PercentIOWaitTime` `PercentInterruptTime` .

Tüm işlemciler genelinde tek bir ölçümü toplamak için, ayarlayın `"condition": "IsAggregate=TRUE"` . Dört vCPU VM 'nin ikinci mantıksal işlemcisi gibi belirli bir işlemcinin ölçüsünü almak için, ayarlayın `"condition": "Name=\\"1\\""` . Mantıksal işlemci numaraları, aralığındadır `[0..n-1]` .

### <a name="builtin-metrics-for-the-memory-class"></a>Bellek sınıfı için yerleşik ölçümler

Ölçüm bellek sınıfı bellek kullanımı, sayfalama ve değiştirme hakkında bilgi sağlar.

Sayaç | Anlamı
------- | -------
Kullanılabilirlik Blememory | MIB 'de kullanılabilir fiziksel bellek
Yüztavailablememory | Toplam belleğin yüzdesi olarak kullanılabilir fiziksel bellek
UsedMemory | Kullanımda olan fiziksel bellek (MIB)
Yüztusedmemory | Toplam belleğin yüzdesi olarak kullanılan fiziksel bellek
PagesPerSec | Toplam sayfalama (okuma/yazma)
PagesReadPerSec | Takas dosyası, program dosyası ve eşlenmiş dosya gibi yedekleme deposundan okunan sayfalar
PagesWrittenPerSec | Yedekleme deposuna yazılan, takas dosyası ve eşlenmiş dosya gibi sayfalar
AvailableSwap | Kullanılmayan takas alanı (MIB)
Yüztavailableswap | Toplam değiştirmenin yüzdesi olarak kullanılmayan takas alanı
UsedSwap | Kullanımda değiştirme alanı (MIB)
Yüztusedswap | İçindeki değiştirme alanını toplam değiştirmenin yüzdesi olarak kullanın

Bu ölçüm sınıfında yalnızca bir örnek vardır. `"condition"`Özniteliğin yararlı bir ayarı yoktur ve atlanmalıdır.

### <a name="builtin-metrics-for-the-network-class"></a>Ağ sınıfı için yerleşik ölçümler

Ölçüm ağ sınıfı, başlangıçtan bu yana tek bir ağ arabirimindeki ağ etkinliği hakkında bilgi sağlar. 

LAD, bant genişliği ölçümlerini sunmaz. Bu ölçümleri konak ölçümlerinden alabilirsiniz.

Sayaç | Anlamı
------- | -------
Bytestransmderlenen | Başlangıçtan bu yana gönderilen toplam bayt sayısı
BytesReceived | Başlangıçtan bu yana alınan toplam bayt sayısı
BytesTotal | Başlangıçtan bu yana gönderilen veya alınan toplam bayt sayısı
Packetstransmderlenen | Başlangıçtan bu yana gönderilen toplam paket sayısı
Paket alındı | Başlangıçtan bu yana alınan toplam paket sayısı
Toplam Rxerrors | Başlangıçtan bu yana alma hatalarının sayısı
TotalTxErrors | Başlangıçtan bu yana geçen iletme hatası sayısı
Toplam çarpışmalar | Başlangıçtan bu yana ağ bağlantı noktaları tarafından raporlanan çakışmaların sayısı

Ağ sınıfı ınstanmiş olsa da, LAD tüm ağ cihazlarında toplanan ağ ölçümlerinin yakalanmayı desteklemez. Eth0 gibi belirli bir arabirim için ölçümleri elde etmek üzere `"condition": "InstanceID=\\"eth0\\""` .

### <a name="builtin-metrics-for-the-file-system-class"></a>Dosya sistemi sınıfı için yerleşik ölçümler

Ölçüm dosya sistemi sınıfı, dosya sistemi kullanımı hakkında bilgi sağlar. Mutlak ve yüzde değerleri sıradan bir kullanıcıya (kök değil) görüntülenebilecekleri şekilde raporlanır.

Sayaç | Anlamı
------- | -------
FreeSpace | Bayt cinsinden kullanılabilir disk alanı
UsedSpace | Bayt cinsinden kullanılan disk alanı
Yüztfreesi | Boş alan yüzdesi
Yüztusedspace | Kullanılan yüzde alanı
Yüztfreeinodes | Kullanılmayan dizin düğümlerinin yüzdesi (ınomdes)
Yüztusedinodes | Tüm dosya sistemleri genelinde toplanan ayrılmış (kullanımda) ınomof yüzdesi
BytesReadPerSecond | Saniye başına okunan bayt
BytesWrittenPerSecond | Saniye başına yazılan bayt sayısı
BytesPerSecond | Saniye başına okunan veya yazılan bayt sayısı
ReadsPerSecond | Saniye başına okuma işlemi
WritesPerSecond | Saniye başına yazma işlemi
TransfersPerSecond | Saniye başına okuma veya yazma işlemi

' İ ayarlayarak, tüm dosya sistemlerinde toplanan değerleri alabilirsiniz `"condition": "IsAggregate=True"` . Gibi belirli bir bağlı dosya sisteminin değerlerini alın `"/mnt"` `"condition": 'Name="/mnt"'` . 

> [!NOTE]
> JSON yerine Azure portal çalışıyorsanız, koşul alanı formu olur `Name='/mnt'` .

### <a name="builtin-metrics-for-the-disk-class"></a>Disk sınıfı için yerleşik ölçümler

Ölçümlerin disk sınıfı disk cihazı kullanımı hakkında bilgi sağlar. Bu istatistikler tüm sürücü için geçerlidir. 

Bir cihazda birden çok dosya sistemi olduğunda, bu cihaz için sayaçlar tüm dosya sistemlerinde toplanır.

Sayaç | Anlamı
------- | -------
ReadsPerSecond | Saniye başına okuma işlemi
WritesPerSecond | Saniye başına yazma işlemi
TransfersPerSecond | Saniyede toplam işlem sayısı
AverageReadTime | Okuma işlemi başına ortalama saniye
AverageWriteTime | Yazma işlemi başına ortalama saniye
AverageTransferTime | İşlem başına ortalama saniye
AverageDiskQueueLength | Sıraya alınan disk işlemlerinin ortalama sayısı
ReadBytesPerSecond | Saniye başına okunan bayt sayısı
WriteBytesPerSecond | Saniye başına yazılan bayt sayısı
BytesPerSecond | Saniye başına okunan veya yazılan bayt sayısı

' İ ayarlayarak, tüm disklerde toplanmış değerler alabilirsiniz `"condition": "IsAggregate=True"` . Belirli bir cihazla ilgili bilgi almak için (örneğin, `/dev/sdf1` ), ayarlayın `"condition": "Name=\\"/dev/sdf1\\""` .

## <a name="install-and-configure-lad-30"></a>LAD 3,0 'yi yükleme ve yapılandırma

### <a name="azure-cli"></a>Azure CLI

Korumalı ayarlarınız dosyada *ProtectedSettings.js* ve genel yapılandırma bilgileriniz *üzerindePublicSettings.js*, aşağıdaki komutu çalıştırın.

```azurecli
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group <resource_group_name> --vm-name <vm_name> --protected-settings ProtectedSettings.json --settings PublicSettings.json
```

Bu komut, Azure CLı 'nın Azure Resource Manager modunu kullandığınızı varsayar. Klasik dağıtım modeli VM 'Leri için LAD 'yi yapılandırmak için, "asm" moduna geçin ( `azure config mode asm` ) ve komutta kaynak grubu adını atlayın. 

Daha fazla bilgi için [platformlar arası CLI belgelerine](/cli/azure/authenticate-azure-cli)bakın.

### <a name="powershell"></a>PowerShell

Korumalı ayarlarınız `$protectedSettings` değişkense ve ortak yapılandırma bilgileriniz `$publicSettings` değişkense, şu komutu çalıştırın:

```powershell
Set-AzVMExtension -ResourceGroupName <resource_group_name> -VMName <vm_name> -Location <vm_location> -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0
```

## <a name="example-lad-30-configuration"></a>Örnek LAD 3,0 yapılandırması

Bu bölümde, önceki tanımlara göre örnek bir LAD 3,0 uzantı yapılandırması ve bazı açıklamalar sağlanmaktadır. Bu örneği çalışmanıza uygulamak için kendi depolama hesabı adınızı, hesap SAS belirtecinizi ve Event Hubs SAS belirteçlerinizi kullanın.

> [!NOTE]
> LAD 'yi yüklemek için Azure CLı veya PowerShell 'i kullanıp kullanmayacağınızı bağlı olarak, ortak ve korumalı ayarların sağlanması yöntemi farklılık gösterir: 
>
> * Azure CLı kullanıyorsanız, yukarıdaki örnek komutu kullanmak için *ProtectedSettings.jsve üzerinde* *PublicSettings.js* için aşağıdaki ayarları kaydedin. 
> * PowerShell kullanıyorsanız, aşağıdaki ayarları `$protectedSettings` çalıştırarak ve çalıştırarak öğesine kaydedin `$publicSettings` `$protectedSettings = '{ ... }'` .

### <a name="protected-settings"></a>Korumalı ayarlar

Korumalı ayarlar yapılandırılır:

* Depolama hesabı.
* Eşleşen bir hesap SAS belirteci.
* Çeşitli havuzlar ( `JsonBlob` veya `EventHub` SAS belirteçleriyle).

```json
{
  "storageAccountName": "yourdiagstgacct",
  "storageAccountSasToken": "sv=xxxx-xx-xx&ss=bt&srt=co&sp=wlacu&st=yyyy-yy-yyT21%3A22%3A00Z&se=zzzz-zz-zzT21%3A22%3A00Z&sig=fake_signature",
  "sinksConfig": {
    "sink": [
      {
        "name": "SyslogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "FilelogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "MyJsonMetricsBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=fake_signature&se=1808096361&skn=yourehpolicy"
      },
      {
        "name": "MyMetricEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&skn=yourehpolicy"
      },
      {
        "name": "LoggingEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&se=1808096361&skn=yourehpolicy"
      }
    ]
  }
}
```

### <a name="public-settings"></a>Ortak ayarlar

Genel ayarlar şu şekilde olabilir:

* Yüzde işlemci-zaman ölçümlerini ve kullanılan disk alanı ölçümlerini `WADMetrics*` tabloya yükleyin.
* Syslog tesis `"user"` ve önem derecesindeki iletileri `"info"` tabloya yükleyin `LinuxSyslog*` .
* Ham OMı sorgu sonuçlarını ( `PercentProcessorTime` ve `PercentIdleTime` ) adlandırılmış `LinuxCPU` tabloya yükleyin.
* Dosyadaki eklenmiş satırları tabloya yükleyin `/var/log/myladtestlog` `MyLadTestLog` .

Her durumda, veriler öğesine de yüklenir:

* Azure Blob depolama. Kapsayıcı adı, `JsonBlob` havuzda tanımlanmıştır.
* Event Hubs uç noktası, havuzda belirtilen şekilde `EventHub` .

```json
{
  "StorageAccount": "yourdiagstgacct",
  "ladCfg": {
    "sampleRateInSeconds": 15,
    "diagnosticMonitorConfiguration": {
      "performanceCounters": {
        "sinks": "MyMetricEventHub,MyJsonMetricsBlob",
        "performanceCounterConfiguration": [
          {
            "unit": "Percent",
            "type": "builtin",
            "counter": "PercentProcessorTime",
            "counterSpecifier": "/builtin/Processor/PercentProcessorTime",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Aggregate CPU %utilization"
              }
            ],
            "condition": "IsAggregate=TRUE",
            "class": "Processor"
          },
          {
            "unit": "Bytes",
            "type": "builtin",
            "counter": "UsedSpace",
            "counterSpecifier": "/builtin/FileSystem/UsedSpace",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Used disk space on /"
              }
            ],
            "condition": "Name=\"/\"",
            "class": "Filesystem"
          }
        ]
      },
      "metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1H"
          },
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "/subscriptions/your_azure_subscription_id/resourceGroups/your_resource_group_name/providers/Microsoft.Compute/virtualMachines/your_vm_name"
      },
      "eventVolume": "Large",
      "syslogEvents": {
        "sinks": "SyslogJsonBlob,LoggingEventHub",
        "syslogEventConfiguration": {
          "LOG_USER": "LOG_INFO"
        }
      }
    }
  },
  "perfCfg": [
    {
      "query": "SELECT PercentProcessorTime, PercentIdleTime FROM SCX_ProcessorStatisticalInformation WHERE Name='_TOTAL'",
      "table": "LinuxCpu",
      "frequency": 60,
      "sinks": "LinuxCpuJsonBlob,LinuxCpuEventHub"
    }
  ],
  "fileLogs": [
    {
      "file": "/var/log/myladtestlog",
      "table": "MyLadTestLog",
      "sinks": "FilelogJsonBlob,LoggingEventHub"
    }
  ]
}
```

`resourceId`Yapılandırmadaki ÖĞESININ VM veya sanal makine ölçek kümesi ile aynı olması gerekir.

* Azure platformu ölçümleri grafik oluşturma ve uyarı, `resourceId` üzerinde çalıştığınız sanal makineyi bilir. Arama anahtarını kullanarak VM 'niz için verileri bulmayı bekler `resourceId` .
* Azure otomatik ölçeklendirme kullanırsanız, `resourceId` Otomatik ölçeklendirme yapılandırmasında, `resourceId` Bu lad kullanımları ile aynı olmalıdır.
* , `resourceId` LAD tarafından YAZıLAN JSON bloblarının adlarına yerleşiktir.

## <a name="view-your-data"></a>Verilerinizi görüntüleme

Performans verilerini görüntülemek veya uyarıları ayarlamak için Azure portal kullanın:

:::image type="content" source="./media/diagnostics-linux/graph_metrics.png" alt-text="Ekran görüntüsü Azure portal gösterir. Ölçüm üzerinde kullanılan disk alanı seçilidir. Elde edilen grafik görüntülenir.":::

`performanceCounters`Veriler her zaman bir Azure depolama tablosunda depolanır. Azure depolama API 'Leri birçok dil ve platformda kullanılabilir.

Havuza gönderilen veriler, `JsonBlob` [korunan ayarlarda](#protected-settings)adlı depolama hesabındaki bloblarda depolanır. Blob verilerini Azure Blob depolama API 'Leri kullanarak kullanabilirsiniz.

Azure depolama 'daki verilere erişmek için bu kullanıcı arabirimi araçlarını da kullanabilirsiniz:

* Visual Studio Sunucu Gezgini
* [Azure Depolama Gezgini](https://azurestorageexplorer.codeplex.com/)

Bir Azure Depolama Gezgini oturumunun aşağıdaki ekran görüntüsü, test sanal makinesinde doğru yapılandırılmış bir LAD 3,0 uzantısının oluşturulan Azure depolama tablolarını ve kapsayıcılarını gösterir. Görüntü, [örnek LAD 3,0 yapılandırmasıyla](#example-lad-30-configuration)tam olarak eşleşmez.

:::image type="content" source="./media/diagnostics-linux/stg_explorer.png" alt-text="Ekran görüntüsünde Azure Depolama Gezgini gösterilmektedir.":::


Event Hubs uç noktasına yayınlanan iletileri kullanma hakkında daha fazla bilgi için ilgili [Event Hubs belgelerine](../../event-hubs/event-hubs-about.md)bakın.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure izleyici](../../azure-monitor/alerts/alerts-classic-portal.md)'de, topladığınız ölçümler için uyarılar oluşturun.
* Ölçümleriniz için [izleme grafikleri](../../azure-monitor/data-platform.md) oluşturun.
* Otomatik ölçeklendirmeyi denetlemek için ölçümlerinizi kullanarak [bir sanal makine ölçek kümesi oluşturun](../linux/tutorial-create-vmss.md) .
