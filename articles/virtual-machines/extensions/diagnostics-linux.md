---
title: Azure Işlem-Linux Tanılama uzantısı
description: Azure 'da çalışan Linux sanal makinelerinden ölçümleri ve günlük olaylarını toplamak için Azure Linux Tanılama uzantısı 'nı (LAD) yapılandırma.
services: virtual-machines-linux
author: abhijeetgaiha
manager: gwallace
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 12/13/2018
ms.author: gwallace
ms.openlocfilehash: b8a5a344f2f1d8280ca60169786e72a0e1dd291e
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073160"
---
# <a name="use-linux-diagnostic-extension-to-monitor-metrics-and-logs"></a>Ölçümleri ve günlükleri izlemek için Linux Tanılama uzantısı 'nı kullanın

Bu belgede Linux Tanılama uzantısının sürüm 3,0 ve daha yeni bir sürümü açıklanmaktadır.

> [!IMPORTANT]
> Sürüm 2,3 ve üzeri hakkında daha fazla bilgi için [Bu belgeye](../linux/classic/diagnostic-extension-v2.md)bakın.

## <a name="introduction"></a>Giriş

Linux Tanılama uzantısı, bir kullanıcının Microsoft Azure üzerinde çalışan bir Linux sanal makinesinin sistem durumunu izlemesine yardımcı olur. Aşağıdaki özellikleri içerir:

* VM 'den sistem performans ölçümlerini toplar ve bunları belirlenen depolama hesabındaki belirli bir tabloda depolar.
* Syslog 'dan günlük olaylarını alır ve bunları belirlenen depolama hesabındaki belirli bir tabloya depolar.
* Kullanıcıların toplanan ve yüklenen veri ölçümlerini özelleştirmesini sağlar.
* Kullanıcıların, toplanan ve yüklenen olayların Syslog özelliklerini ve önem düzeyleri özelleştirmesini sağlar.
* Kullanıcıların belirtilen günlük dosyalarını belirlenmiş bir depolama tablosuna karşıya yüklemesine olanak sağlar.
* , Belirlenen depolama hesabındaki rastgele EventHub uç noktalarına ve JSON biçimli bloblara ölçüm ve günlük olayları göndermeyi destekler.

Bu uzantı, hem Azure dağıtım modelleriyle birlikte çalışmaktadır.

## <a name="installing-the-extension-in-your-vm"></a>Sanal makinenize Uzantı yükleme

Bu uzantıyı Azure PowerShell cmdlet 'lerini, Azure CLı betikleri, ARM şablonlarını veya Azure portal kullanarak etkinleştirebilirsiniz. Daha fazla bilgi için bkz. [uzantı özellikleri](features-linux.md).

Bu yükleme yönergeleri ve [indirilebilir bir örnek yapılandırma](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) lad 3,0 'yi şu şekilde yapılandırın:

* aynı ölçümleri, LAD 2,3 ile belirtilen şekilde yakalayın ve saklayın.
* Yeni bir dosya sistemi ölçümleri kümesi yakalayın, LAD 3,0;
* LAD 2,3 ile etkinleştirilen varsayılan Syslog koleksiyonunu yakala;
* VM ölçümlerinde grafik oluşturma ve uyarı verme için Azure portal deneyimini etkinleştirin.

İndirilebilir yapılandırma yalnızca bir örnektir; kendi gereksinimlerinize uyacak şekilde değiştirin.

### <a name="prerequisites"></a>Önkoşullar

* **Azure Linux Aracısı sürüm 2.2.0 veya üzeri**. Azure VM Linux Galeri görüntülerinin çoğu, sürüm 2.2.7 veya üstünü içerir. VM 'de yüklü sürümü onaylamak için `/usr/sbin/waagent -version` çalıştırın. VM, Konuk aracısının eski bir sürümünü çalıştırıyorsa, güncelleştirmek için [Bu yönergeleri](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) izleyin.
* **Azure CLI**. Makinenizde [Azure CLI ortamını ayarlayın](https://docs.microsoft.com/cli/azure/install-azure-cli) .
* Henüz yoksa wget komutu: `sudo apt-get install wget`çalıştırın.
* Mevcut bir Azure aboneliği ve içindeki mevcut bir depolama hesabı, verileri depolamak için.
* Desteklenen Linux dağıtımları listesi https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic#supported-linux-distributions

### <a name="sample-installation"></a>Örnek yükleme

Çalışmadan önce ilk bölümdeki değişkenler için doğru değerleri girin:

```bash
# Set your Azure VM diagnostic variables correctly below
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Should login to Azure first before anything else
az login

# Select the subscription containing the storage account
az account set --subscription <your_azure_subscription_id>

# Download the sample Public settings. (You could also use curl or any web browser)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VM resource ID. Replace storage account name and resource ID in the public settings.
my_vm_resource_id=$(az vm show -g $my_resource_group -n $my_linux_vm --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vm_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token)
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finallly tell Azure to install and enable the extension
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

Örnek yapılandırmanın URL 'SI ve içeriği değiştirilebilir. Portal Ayarları JSON dosyasının bir kopyasını indirin ve gereksinimlerinize göre özelleştirin. Oluşturduğunuz tüm şablonlar veya Otomasyon, bu URL 'YI her seferinde indirmek yerine kendi kopyanızı kullanmalıdır.

### <a name="updating-the-extension-settings"></a>Uzantı ayarları güncelleştiriliyor

Korumalı veya ortak ayarlarınızı değiştirdikten sonra aynı komutu çalıştırarak bunları sanal makineye dağıtın. Ayarlarda her şey değiştirilmişse, güncelleştirilmiş ayarlar uzantıya gönderilir. LAD yapılandırmayı yeniden yükler ve kendisini yeniden başlatır.

### <a name="migration-from-previous-versions-of-the-extension"></a>Uzantının önceki sürümlerinden geçiş

Uzantının en son sürümü **3,0**' dir. **Tüm eski sürümler (2. x) kullanım dışıdır ve 31 temmuz 2018 tarihinde veya sonrasında yayımdan kaldırılmış olabilir**.

> [!IMPORTANT]
> Bu uzantı, uzantının yapılandırmasındaki son değişiklikleri tanıtır. Uzantının güvenliğini artırmak için bu tür bir değişiklik yapılmıştır; Sonuç olarak, 2. x ile geriye dönük uyumluluk korunamadı. Ayrıca, bu uzantının uzantı yayımcısı 2. x sürümleri için yayımcıdan farklıdır.
>
> 2\. x sürümünden uzantının bu yeni sürümüne geçiş yapmak için eski uzantıyı (eski yayımcı adı altında) kaldırmanız ve ardından uzantının 3. sürümünü kurmanız gerekir.

Öneri

* Uzantıyı otomatik ikincil sürüm yükseltmesi etkin olarak yükler.
  * Klasik dağıtım modeli VM 'lerinde, uzantıyı Azure XPLAT CLı veya PowerShell aracılığıyla yüklüyorsanız sürüm olarak ' 3. * ' belirtin.
  * Azure Resource Manager dağıtım modeli VM 'lerinde, VM Dağıtım şablonuna ' "Oto Upgrademinorversion": true ' ekleyin.
* LAD 3,0 için yeni/farklı bir depolama hesabı kullanın. Sorunlu hesabını paylaşmayı sağlayan LAD 2,3 ile LAD 3,0 arasında birkaç küçük uyumsuzluk vardır:
  * LAD 3,0, syslog olaylarını farklı bir ada sahip bir tabloda depolar.
  * `builtin` ölçümler için onay tanımlayıcısı dizeleri, LAD 3,0 ' de farklılık gösterir.

## <a name="protected-settings"></a>Korumalı ayarlar

Bu yapılandırma bilgileri kümesi, genel görünümden korunması gereken hassas bilgiler içerir (örneğin, depolama kimlik bilgileri). Bu ayarlar, uzantısı tarafından şifrelenmiş biçimde iletilir ve saklanır.

```json
{
    "storageAccountName" : "the storage account to receive data",
    "storageAccountEndPoint": "the hostname suffix for the cloud for this account",
    "storageAccountSasToken": "SAS access token",
    "mdsdHttpProxy": "HTTP proxy settings",
    "sinksConfig": { ... }
}
```

Ad | Değer
---- | -----
storageAccountName | Verilerin uzantı tarafından yazıldığı depolama hesabının adı.
storageAccountEndPoint | seçim Depolama hesabının bulunduğu bulutu tanımlayan uç nokta. Bu ayar yoksa, LAD varsayılan olarak Azure genel bulutuna `https://core.windows.net`. Azure Almanya, Azure Kamu veya Azure Çin 'de bir depolama hesabı kullanmak için bu değeri uygun şekilde ayarlayın.
storageAccountSasToken | Ekleme, oluşturma, listeleme, güncelleştirme ve yazma izinleri (`sp='acluw'`) veren kapsayıcılar ve nesneler (`srt='co'`) için geçerli olan blob ve tablo Hizmetleri (`ss='bt'`) için bir [Hesap SAS belirteci](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) . Önde gelen soru işareti (? *) eklemeyin.*
mdsdHttpProxy | seçim Uzantının belirtilen depolama hesabına ve uç noktaya bağlanmasını sağlamak için HTTP proxy bilgileri gerekir.
sinksConfig | seçim Ölçüm ve olayların sunulabilecek alternatif hedeflerin ayrıntıları. Uzantı tarafından desteklenen her bir veri havuzunun belirli ayrıntıları, izleyen bölümlerde ele alınmıştır.

Bir Kaynak Yöneticisi şablonu içinde SAS belirteci almak için **Listaccountsas** işlevini kullanın. Örnek bir şablon için bkz. [list işlev örneği](../../azure-resource-manager/resource-group-template-functions-resource.md#list-example).

Gerekli SAS belirtecini Azure portal aracılığıyla kolayca oluşturabilirsiniz.

1. Uzantının yazmasını istediğiniz genel amaçlı depolama hesabını seçin
1. Sol menünün ayarlar bölümünden "paylaşılan erişim imzası" seçeneğini belirleyin
1. Daha önce açıklanan uygun bölümleri yapın
1. "SAS oluştur" düğmesine tıklayın.

![image](./media/diagnostics-linux/make_sas.png)

Oluşturulan SAS 'yi storageAccountSasToken alanına kopyalayın; Baştaki soru işaretini ("?") kaldırın.

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

Bu isteğe bağlı bölüm, uzantının topladığı bilgileri gönderdiği ek hedefleri tanımlar. "Sink" dizisi her ek veri havuzu için bir nesne içerir. "Type" özniteliği nesnedeki diğer öznitelikleri belirler.

Öğe | Değer
------- | -----
ad | Bu havuza uzantı yapılandırmasında başka bir yerde başvurmak için kullanılan bir dize.
type | Tanımlanmakta olan havuz türü. Bu türün örneklerinde diğer değerleri (varsa) belirler.

Linux Tanılama uzantısının 3,0 sürümü iki havuz türünü destekliyor: EventHub ve JsonBlob.

#### <a name="the-eventhub-sink"></a>EventHub havuzu

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

"SasURL" girdisi, verilerin yayımlanması gereken olay hub 'ı için SAS belirteci dahil olmak üzere tam URL 'yi içerir. LAD, gönderme talebini sağlayan bir ilkeyi adlandırma için bir SAS gerektirir. Örnek:

* `contosohub` adlı bir Event Hubs ad alanı oluşturun
* `syslogmsgs` adlı ad alanında bir olay hub 'ı oluşturma
* `writer` adlı olay hub 'ında, isteği gönder talebini sağlayan bir paylaşılan erişim ilkesi oluşturun

1 Ocak 2018 ' de gece yarısı UTC 'ye kadar bir SAS oluşturduysanız, sasURL değeri şu olabilir:

```url
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Event Hubs için SAS belirteçleri oluşturma hakkında daha fazla bilgi için [Bu Web sayfasına](../../event-hubs/event-hubs-authentication-and-security-model-overview.md)bakın.

#### <a name="the-jsonblob-sink"></a>JsonBlob havuzu

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

JsonBlob havuzuna yöneltilen veriler, Azure depolama 'daki bloblarda depolanır. Her bir LAD örneği her bir havuz adı için her saat bir blob oluşturur. Her blob her zaman sözdizimi geçerli bir JSON dizisi içerir. Yeni girdiler diziye göre otomatik olarak eklenir. Blob 'lar, havuzla aynı ada sahip bir kapsayıcıda depolanır. Blob kapsayıcı adları için Azure depolama kuralları, JsonBlob havuzları adları için geçerlidir: 3 ila 63 küçük harfli alfasayısal karakterler veya tireler.

## <a name="public-settings"></a>Ortak ayarlar

Bu yapı, uzantı tarafından toplanan bilgileri denetleyen çeşitli ayarlar bloklarını içerir. Her ayar isteğe bağlıdır. `ladCfg`belirtirseniz, `StorageAccount`de belirtmeniz gerekir.

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
StorageAccount | Verilerin uzantı tarafından yazıldığı depolama hesabının adı. [Korunan ayarlarda](#protected-settings)belirtilen adla aynı olmalıdır.
mdsdHttpProxy | seçim [Korumalı ayarlarınızdaki](#protected-settings)gibi. Ayarlanırsa, ortak değer özel değer tarafından geçersiz kılınır. [Korumalı ayarlar](#protected-settings)' da, parola gibi gizli dizi içeren ara sunucu ayarlarını yerleştirin.

Kalan öğeler aşağıdaki bölümlerde ayrıntılı olarak açıklanmıştır.

### <a name="ladcfg"></a>ladCfg

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

Bu isteğe bağlı yapı, Azure ölçümleri hizmetine ve diğer veri havuzları 'na teslime ilişkin ölçüm ve günlüklerin toplanması için denetim sağlar. `performanceCounters` veya `syslogEvents` ya da her ikisini de belirtmeniz gerekir. `metrics` yapısını belirtmeniz gerekir.

Öğe | Değer
------- | -----
eventVolume | seçim Depolama tablosu içinde oluşturulan bölüm sayısını denetler. `"Large"`, `"Medium"`veya `"Small"`biri olmalıdır. Belirtilmemişse, varsayılan değer `"Medium"`.
Samplerateınseconds | seçim Ham (toplanmayan) ölçümler koleksiyonu arasındaki varsayılan Aralık. Desteklenen en küçük örnek oranı 15 saniyedir. Belirtilmemişse, varsayılan değer `15`.

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
resourceId | VM 'nin veya VM 'nin ait olduğu sanal makine ölçek kümesinin Azure Resource Manager kaynak KIMLIĞI. Bu ayar, yapılandırmada bir JsonBlob havuzu kullanılıyorsa da belirtilmelidir.
scheduledTransferPeriod | Toplam ölçümlerin hesaplanacağı ve Azure ölçümlerine aktarılacağı sıklık, bir 8601 zaman aralığı olarak ifade edilir. En küçük aktarım süresi 60 saniyedir, yani PT1M. En az bir scheduledTransferPeriod belirtmeniz gerekir.

PerformanceCounters bölümünde belirtilen ölçümlerin örnekleri, her 15 saniyede bir veya sayaç için açıkça tanımlanmış örnek hızda toplanır. Birden çok scheduledTransferPeriod frekansları görünürse (örnekte olduğu gibi), her toplama bağımsız olarak hesaplanır.

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

Bu isteğe bağlı bölüm, ölçüm koleksiyonunu denetler. Ham örnekler her bir [Scheduledtransferperiod](#metrics) için toplanır ve bu değerleri üretir:

* Ortalama
* en az
* en fazla
* Son toplanan değer
* toplamı hesaplamak için kullanılan ham örnek sayısı

Öğe | Değer
------- | -----
Yapma | seçim LAD 'nin toplanmış ölçüm sonuçları gönderdiği havuz adlarının virgülle ayrılmış bir listesi. Tüm toplanan ölçümler listelenen her havuza yayımlanır. Bkz. [Sinksconfig](#sinksconfig). Örnek: `"EHsink1, myjsonsink"`.
type | Ölçümün gerçek sağlayıcısını tanımlar.
sınıf | "Counter" ile birlikte, sağlayıcının ad alanı içinde belirli ölçümü tanımlar.
counter | "Class" ile birlikte, sağlayıcının ad alanı içinde belirli bir ölçümü tanımlar.
Onay Belirleyicisi | Azure ölçümleri ad alanı içindeki belirli ölçüyü tanımlar.
condition | seçim Bir nesnenin geçerli bir örneğini seçer veya bu nesnenin tüm örneklerinde toplamayı seçer. Daha fazla bilgi için `builtin` ölçüm tanımlarına bakın.
Örnekleray | Bu ölçüm için ham örneklerin toplandığı oranı ayarlayan 8601 aralığıdır. Ayarlanmamışsa, koleksiyon aralığı [Samplerateınseconds](#ladcfg)değerine göre ayarlanır. Desteklenen en kısa örnek hızı 15 saniyedir (PT15S).
birim | Şu dizelerden biri olmalıdır: "Count", "bytes", "saniye", "percent", "CountPerSecond", "BytesPerSecond", "milisaniyelik". Ölçüm için birimi tanımlar. Toplanan verilerin tüketicileri, toplanan veri değerlerinin bu birimle eşleşmesini bekler. LAD bu alanı yoksayar.
displayName | Azure ölçümlerinde bu verilere eklenecek olan etiket (ilişkili yerel ayar tarafından belirtilen dilde). LAD bu alanı yoksayar.

Counterbelirleyicisi, rastgele bir tanımlayıcıdır. Ölçüm tüketicileri, Azure portal grafik oluşturma ve uyarı özelliği gibi, ölçüm veya ölçüm örneğini tanımlayan "anahtar" olarak Counterbelirleyicisi kullanın. `builtin` ölçümler için, `/builtin/`ile başlayan onay belirticisi değerlerini kullanmanızı öneririz. Bir ölçümün belirli bir örneğini topluyorsanız, örneğin tanımlayıcısını Counterıdentifier değerine iliştirmenizi öneririz. Bazı örnekler:

* Tüm vCPU 'larda ortalama `/builtin/Processor/PercentIdleTime` boşta geçen süre
* /MNT dosya sistemi için `/builtin/Disk/FreeSpace(/mnt)` boş alan
* Tüm bağlı dosya sistemleri genelinde ortalama `/builtin/Disk/FreeSpace` boş alan

Ne de ne de Azure portal, Counterbelirtici değerinin herhangi bir kalıpla eşleşmesini bekler. Onay tanımlayıcısı değerlerini nasıl oluşturabileceğinize göre tutarlı olun.

`performanceCounters`belirttiğinizde, LAD verileri her zaman Azure Storage 'daki bir tabloya yazar. JSON bloblarına ve/veya Event Hubs yazılmış verilere sahip olabilirsiniz, ancak verileri bir tabloya depolamayı devre dışı bırakabilirsiniz. Aynı depolama hesabı adını ve uç noktasını kullanmak üzere yapılandırılan tanılama uzantısının tüm örnekleri, ölçümlerini ve günlüklerini aynı tabloya ekler. Aynı tablo bölümüne çok fazla sayıda VM yazıyorsanız Azure bu bölüme yazma işlemlerini kısıtlayabilir. EventVolume ayarı, girdilerin 1 (küçük), 10 (orta) veya 100 (büyük) farklı bölümler arasında yayılmasına neden olur. Genellikle trafiğin kısıtlanmadığından emin olmak için "Orta" yeterlidir. Azure portal Azure ölçümleri özelliği, grafik oluşturmak veya uyarıları tetiklemek için bu tablodaki verileri kullanır. Tablo adı, Bu dizelerin bitiştirilmesi:

* `WADMetrics`
* Tabloda depolanan toplanmış değerler için "scheduledTransferPeriod"
* `P10DV2S`
* "YYYYMMDD" biçiminde, her 10 günde bir tarih değiştiren bir tarih

Örnekler `WADMetricsPT1HP10DV2S20170410` ve `WADMetricsPT1MP10DV2S20170609`içerir.

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

Bu isteğe bağlı bölüm, syslog 'dan gelen günlük olayları koleksiyonunu denetler. Bölüm atlanırsa, syslog olayları hiç yakalanmaz.

SyslogEventConfiguration koleksiyonunda, ilgilendiğiniz her Syslog özelliği için bir giriş vardır. Bu özellik belirli bir tesis için Minönem derecesi "NONE" ise veya bu tesis öğede görünmüyorsa, bu olandakilerden hiçbir olay yakalanmaz.

Öğe | Değer
------- | -----
Yapma | Ayrı günlük olaylarının yayımlandığı havuz adlarının virgülle ayrılmış listesi. SyslogEventConfiguration ' deki kısıtlamalarla eşleşen tüm günlük olayları listelenen her havuza yayımlanır. Örnek: "EHforsyslog"
facilityName | Syslog tesis adı (örneğin, "LOG\_USER" veya "LOG\_LOCAL0"). Tam liste için [Syslog Man sayfasının](http://man7.org/linux/man-pages/man3/syslog.3.html) "tesis" bölümüne bakın.
Minönem derecesi | Syslog önem derecesi düzeyi ("LOG\_ERR" veya "LOG\_ıNFO" gibi). Tam liste için [Syslog Man sayfasının](http://man7.org/linux/man-pages/man3/syslog.3.html) "düzey" bölümüne bakın. Uzantı, belirtilen düzeyin üzerinde veya üzerinde tesise gönderilen olayları yakalar.

`syslogEvents`belirttiğinizde, LAD verileri her zaman Azure Storage 'daki bir tabloya yazar. JSON bloblarına ve/veya Event Hubs yazılmış verilere sahip olabilirsiniz, ancak verileri bir tabloya depolamayı devre dışı bırakabilirsiniz. Bu tablo için bölümleme davranışı `performanceCounters`açıklananla aynıdır. Tablo adı, Bu dizelerin bitiştirilmesi:

* `LinuxSyslog`
* "YYYYMMDD" biçiminde, her 10 günde bir tarih değiştiren bir tarih

Örnekler `LinuxSyslog20170410` ve `LinuxSyslog20170609`içerir.

### <a name="perfcfg"></a>perfCfg

Bu isteğe bağlı bölüm, rastgele [OMI](https://github.com/Microsoft/omi) sorgularının yürütülmesini denetler.

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
ad alanı | seçim Sorgunun yürütülmesi gereken OMı ad alanı. Belirtilmemişse, varsayılan değer, [System Center platformlar arası sağlayıcılar](https://github.com/Microsoft/SCXcore)tarafından uygulanan "root/SCX" dır.
sorgu | Yürütülecek OMı sorgusu.
table | seçim Azure Storage tablosu, belirtilen depolama hesabında (bkz. [korumalı ayarlar](#protected-settings)).
frequency | seçim Sorgunun yürütülmesi arasındaki saniye sayısı. Varsayılan değer 300 ' dir (5 dakika); minimum değer 15 saniyedir.
Yapma | seçim Ham örnek ölçüm sonuçlarının yayımlanması gereken ek havuz adlarının virgülle ayrılmış bir listesi. Bu ham örneklerin toplaması, uzantı veya Azure ölçümleri tarafından hesaplanmadı.

"Table" veya "Havuzlar" ya da her ikisi de belirtilmelidir.

### <a name="filelogs"></a>Dosya günlükleri

Günlük dosyalarının yakalanmasını denetler. LAD, dosyaya yazıldığı ve bunları tablo satırlarına ve/veya belirtilen herhangi bir havuza (JsonBlob veya EventHub) yazan yeni metin satırlarını yakalar.

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
file | İzlenen ve yakalanan günlük dosyasının tam yol adı. Yol adının tek bir dosya adı olmalıdır; bir dizini veya joker karakter içeremez.
table | seçim Belirtilen depolama hesabında (korumalı yapılandırmada belirtildiği gibi), dosyanın "Tail" içindeki yeni satırların yazıldığı Azure Storage tablosu.
Yapma | seçim Günlük satırlarının gönderildiği ek havuz adlarının virgülle ayrılmış bir listesi.

"Table" veya "Havuzlar" ya da her ikisi de belirtilmelidir.

## <a name="metrics-supported-by-the-builtin-provider"></a>Yerleşik sağlayıcı tarafından desteklenen ölçümler

Yerleşik ölçüm sağlayıcısı, geniş bir Kullanıcı kümesiyle en ilginç ölçüm kaynağıdır. Bu ölçümler beş geniş sınıfa ayrılır:

* İşlemci
* Bellek
* Ağ
* dosya sistemi
* Disk

### <a name="builtin-metrics-for-the-processor-class"></a>Işlemci sınıfı için yerleşik ölçümler

Ölçümlerin Işlemci sınıfı, sanal makinede işlemci kullanımı hakkında bilgi sağlar. Yüzdeleri toplayarak, sonuç tüm CPU 'larda ortalama olur. İki vCPU sanal makinesinde, bir vCPU %100 meşgulse ve diğeri 100% boşta ise, bildirilen PercentIdleTime 50. Her vCPU aynı dönemde %50 meşgulse, bildirilen sonuç de 50 olacaktır. Dört sanal CPU sanal makinesinde, tek bir vCPU 100% meşgul ve diğerleri boşta, bildirilen PercentIdleTime 75 olacaktır.

counter | Anlamı
------- | -------
PercentIdleTime | Toplama penceresinde işlemcilerin çekirdek boşta döngüsünü yürüttüğünden geçen sürenin yüzdesi
percentProcessorTime | Boşta olmayan iş parçacığı yürütme zaman yüzdesi
PercentIOWaitTime | GÇ işlemlerinin tamamlanması için bekleyen sürenin yüzdesi
PercentInterruptTime | Donanım/yazılım kesintileri ve DPC 'leri yürütme zaman yüzdesi (ertelenmiş yordam çağrıları)
PercentUserTime | Toplama penceresi sırasında boşta olmayan süre, Kullanıcı için normal öncelikte harcanan sürenin yüzdesi
PercentNiceTime | Boşta olmayan süre, düşürülen (iyi) öncelikte harcanan yüzde
PercentPrivilegedTime | Boşta olmayan süre, ayrıcalıklı (çekirdek) modda harcanan yüzde

İlk dört sayaç %100 olmalıdır. Son üç sayaç ayrıca %100 ' a kadar toplam PercentProcessorTime, PercentIOWaitTime ve PercentInterruptTime toplamını alt bölümlere bölüler.

Tüm işlemciler genelinde toplanmış tek bir ölçüm elde etmek için `"condition": "IsAggregate=TRUE"`ayarlayın. Dört vCPU sanal makinesinin ikinci mantıksal işlemcisi gibi belirli bir işlemcinin ölçüsünü almak için `"condition": "Name=\\"1\\""`ayarlayın. Mantıksal işlemci numaraları `[0..n-1]`aralığındadır.

### <a name="builtin-metrics-for-the-memory-class"></a>Bellek sınıfı için yerleşik ölçümler

Ölçüm bellek sınıfı bellek kullanımı, sayfalama ve değiştirme hakkında bilgi sağlar.

counter | Anlamı
------- | -------
Kullanılabilirlik Blememory | MIB 'de kullanılabilir fiziksel bellek
Yüztavailablememory | Toplam belleğin yüzdesi olarak kullanılabilir fiziksel bellek
UsedMemory | Kullanımda olan fiziksel bellek (MIB)
Yüztusedmemory | Toplam belleğin yüzdesi olarak kullanılan fiziksel bellek
PagesPerSec | Toplam sayfalama (okuma/yazma)
PagesReadPerSec | Yedekleme deposundan okunan sayfalar (takas dosyası, program dosyası, eşlenmiş dosya vb.)
PagesWrittenPerSec | Yedekleme deposuna yazılan sayfalar (takas dosyası, eşlenmiş dosya vb.)
AvailableSwap | Kullanılmayan takas alanı (MIB)
Yüztavailableswap | Toplam değiştirmenin yüzdesi olarak kullanılmayan takas alanı
UsedSwap | Kullanımda değiştirme alanı (MIB)
Yüztusedswap | İçindeki değiştirme alanını toplam değiştirmenin yüzdesi olarak kullanın

Bu ölçüm sınıfının yalnızca tek bir örneği vardır. "Condition" özniteliğinin hiçbir kullanışlı ayarı yoktur ve atlanmalıdır.

### <a name="builtin-metrics-for-the-network-class"></a>Ağ sınıfı için yerleşik ölçümler

Ölçüm ağ sınıfı, önyükleme sonrasında tek bir ağ arabirimlerinde ağ etkinliği hakkında bilgi sağlar. LAD, ana bilgisayar ölçümlerinden alınabilecek bant genişliği ölçümlerini sunmaz.

counter | Anlamı
------- | -------
Bytestransmderlenen | Önyüklemeden bu yana gönderilen toplam bayt sayısı
BytesReceived | Önyüklemeden bu yana alınan toplam bayt sayısı
BytesTotal | Önyüklemeden bu yana gönderilen veya alınan toplam bayt sayısı
Packetstransmderlenen | Önyüklemeden bu yana gönderilen toplam paket sayısı
Paket alındı | Önyüklemeden bu yana alınan toplam paket sayısı
Toplam Rxerrors | Önyüklemeden bu yana alma hatalarının sayısı
TotalTxErrors | Önyüklemeden bu yana aktarılan hataların sayısı
Toplam çarpışmalar | Önyüklemeden bu yana ağ bağlantı noktaları tarafından raporlanan çakışmaların sayısı

 Bu sınıf ınstanmiş olsa da, LAD tüm ağ cihazlarında toplanan ağ ölçümlerinin yakalanmayı desteklemez. Eth0 gibi belirli bir arabirime yönelik ölçümleri almak için `"condition": "InstanceID=\\"eth0\\""`ayarlayın.

### <a name="builtin-metrics-for-the-filesystem-class"></a>Dosya sistemi sınıfı için yerleşik ölçümler

Ölçümlerin dosya sistemi sınıfı, dosya sistemi kullanımı hakkında bilgi sağlar. Mutlak ve yüzde değerleri sıradan bir kullanıcıya (kök değil) gösterildiklerinde raporlanır.

counter | Anlamı
------- | -------
FreeSpace | Bayt cinsinden kullanılabilir disk alanı
UsedSpace | Bayt cinsinden kullanılan disk alanı
Yüztfreesi | Boş alan yüzdesi
Yüztusedspace | Kullanılan yüzde alanı
Yüztfreeinodes | Kullanılmayan ınomdes yüzdesi
Yüztusedinodes | Tüm dosya sistemleri genelinde toplanan ayrılmış (kullanımda) ınomof 'ların yüzdesi
BytesReadPerSecond | Saniye başına okunan bayt
BytesWrittenPerSecond | Saniye başına yazılan bayt sayısı
BytesPerSecond | Saniye başına okunan veya yazılan bayt sayısı
ReadsPerSecond | Saniye başına okuma işlemi
WritesPerSecond | Saniye başına yazma işlemi
TransfersPerSecond | Saniye başına okuma veya yazma işlemi

Tüm dosya sistemleri genelinde toplanmış değerler `"condition": "IsAggregate=True"`ayarlanarak elde edilebilir. "/Mnt" gibi belirli bir bağlı dosya sisteminin değerleri, `"condition": 'Name="/mnt"'`ayarlanarak elde edilebilir. 

**Note**: JSON yerine Azure portalını kullanıyorsanız, doğru koşul alanı formu adı = '/mnt ' olur

### <a name="builtin-metrics-for-the-disk-class"></a>Disk sınıfı için yerleşik ölçümler

Ölçümlerin disk sınıfı disk cihazı kullanımı hakkında bilgi sağlar. Bu istatistikler tüm sürücü için geçerlidir. Bir cihazda birden çok dosya sistemi varsa, bu cihaz için sayaçlar tüm bunlar arasında toplanır.

counter | Anlamı
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

Tüm diskler genelinde toplanmış değerler `"condition": "IsAggregate=True"`ayarlanarak elde edilebilir. Belirli bir cihazla ilgili bilgi almak için (örneğin,/dev/sdf1), `"condition": "Name=\\"/dev/sdf1\\""`ayarlayın.

## <a name="installing-and-configuring-lad-30-via-cli"></a>CLı aracılığıyla LAD 3,0 yükleme ve yapılandırma

Korunan ayarlarınızın PrivateConfig. json dosyasında olduğunu ve ortak yapılandırma bilgilerinizin PublicConfig. json dosyasında olduğunu varsayarsak, şu komutu çalıştırın:

```azurecli
az vm extension set *resource_group_name* *vm_name* LinuxDiagnostic Microsoft.Azure.Diagnostics '3.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json
```

Bu komut, Azure CLı 'nın Azure Kaynak yönetimi modunu (ARM) kullandığınızı varsayar. Klasik dağıtım modeli (ASM) VM 'Leri için LAD 'yi yapılandırmak üzere, "asm" moduna geçin (`azure config mode asm`) ve komutta kaynak grubu adını atlayın. Daha fazla bilgi için [platformlar arası CLI belgelerine](https://docs.microsoft.com/azure/xplat-cli-connect)bakın.

## <a name="an-example-lad-30-configuration"></a>Örnek LAD 3,0 yapılandırması

Önceki tanımları temel alarak, bazı açıklamayla örnek bir LAD 3,0 uzantı yapılandırması aşağıda verilmiştir. Bu örneği çalışmanıza uygulamak için kendi depolama hesabı adınızı, hesap SAS belirtecinizi ve EventHubs SAS belirteçlerini kullanmanız gerekir.

### <a name="privateconfigjson"></a>PrivateConfig.json

Bu özel ayarlar yapılandırılır:

* depolama hesabı
* eşleşen bir hesap SAS belirteci
* birkaç havuz (SAS belirteçlerine sahip JsonBlob veya EventHubs)

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

### <a name="publicconfigjson"></a>PublicConfig.json

Bu genel ayarlar, LAD 'ye neden olur:

* Yüzde-işlemci-zamanı ve kullanılan disk alanı ölçümlerini `WADMetrics*` tablosuna yükleme
* Syslog tesis "Kullanıcı" ve önem derecesi "bilgi" den `LinuxSyslog*` tabloya ileti yükleyin
* Ham OMı sorgu sonuçlarını (PercentProcessorTime ve PercentIdleTime) adlandırılmış `LinuxCPU` tablosuna yükleyin
* Dosya `/var/log/myladtestlog` eklenen satırları `MyLadTestLog` tabloya yükle

Her durumda, veriler öğesine de yüklenir:

* Azure Blob depolama (kapsayıcı adı, JsonBlob havuzunda tanımlanmıştır)
* EventHubs uç noktası (EventHubs havuzunda belirtilen şekilde)

```json
{
  "StorageAccount": "yourdiagstgacct",
  "sampleRateInSeconds": 15,
  "ladCfg": {
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

Yapılandırmadaki `resourceId` VM veya sanal makine ölçek kümesi ile aynı olmalıdır.

* Azure platformu ölçümleri grafik oluşturma ve uyarı, üzerinde çalıştığınız VM 'nin RESOURCEID olduğunu bilir. RESOURCEID arama anahtarını kullanarak VM 'niz için verileri bulmayı bekler.
* Azure otomatik ölçeklendirme kullanırsanız, otomatik ölçeklendirme yapılandırmasındaki RESOURCEID, LAD tarafından kullanılan RESOURCEID ile aynı olmalıdır.
* RESOURCEID, LAD tarafından yazılan Jsonblob 'ların adlarına yerleşiktir.

## <a name="view-your-data"></a>Verilerinizi görüntüleyin

Performans verilerini görüntülemek veya uyarıları ayarlamak için Azure portal kullanın:

![image](./media/diagnostics-linux/graph_metrics.png)

`performanceCounters` verileri her zaman bir Azure depolama tablosunda depolanır. Azure depolama API 'Leri birçok dil ve platformda kullanılabilir.

JsonBlob havuzları 'na gönderilen veriler, [korunan ayarlarda](#protected-settings)adlı depolama hesabındaki bloblara depolanır. Blob verilerini Azure Blob depolama API 'Leri kullanarak kullanabilirsiniz.

Ayrıca, bu kullanıcı arabirimi araçlarını kullanarak Azure Storage 'daki verilere erişebilirsiniz:

* Visual Studio Sunucu Gezgini.
* [Microsoft Azure Depolama Gezgini](https://azurestorageexplorer.codeplex.com/ "Azure Storage Gezgini").

Microsoft Azure Depolama Gezgini oturumunun bu anlık görüntüsü, test sanal makinesinde doğru yapılandırılmış bir LAD 3,0 uzantısının oluşturulan Azure depolama tablolarını ve kapsayıcılarını gösterir. Görüntü, [örnek LAD 3,0 yapılandırmasıyla](#an-example-lad-30-configuration)tam olarak eşleşmez.

![image](./media/diagnostics-linux/stg_explorer.png)

Bir EventHubs uç noktasına yayınlanan iletileri kullanmayı öğrenmek için ilgili [eventhubs belgelerine](../../event-hubs/event-hubs-what-is-event-hubs.md) bakın.

## <a name="next-steps"></a>Sonraki adımlar

* Topladığınız ölçümler için [Azure izleyici](../../monitoring-and-diagnostics/insights-alerts-portal.md) 'de ölçüm uyarıları oluşturun.
* Ölçümleriniz için [izleme grafikleri](../../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) oluşturun.
* Otomatik ölçeklendirmeyi denetlemek için ölçümlerinizi kullanarak [bir sanal makine ölçek kümesi oluşturmayı](../linux/tutorial-create-vmss.md) öğrenin.
