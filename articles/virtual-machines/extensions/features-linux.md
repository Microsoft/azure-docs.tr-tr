---
title: Linux için Azure VM uzantıları ve özellikleri
description: Azure sanal makineleri için neler sundukları veya iyileştirebilecekleri ile gruplanan uzantıları öğrenin.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 52f5d0ec-8f75-49e7-9e15-88d46b420e63
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: akjosh
ms.openlocfilehash: 67df46742be52b03bd91af19654fbfac5df29646
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073758"
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Linux için sanal makine uzantıları ve özellikleri

Azure sanal makinesi (VM) uzantıları, Azure VM 'lerinde dağıtım sonrası yapılandırma ve otomasyon görevleri sağlayan küçük uygulamalardır. Örneğin, bir sanal makine yazılım yüklemesi, virüsten koruma koruması veya bunun içinde bir komut dosyası çalıştırmak istiyorsa, bir VM Uzantısı kullanılabilir. Azure VM uzantıları, Azure CLı, PowerShell, Azure Resource Manager şablonları ve Azure portal birlikte çalıştırılabilir. Uzantılar, yeni bir VM dağıtımıyla birlikte paketlenmiştir veya var olan herhangi bir sisteme karşı çalıştırılabilir.

Bu makalede VM uzantılarına genel bakış, Azure VM uzantıları kullanma önkoşulları ve VM uzantılarının algılanması, yönetilmesi ve kaldırılması ile ilgili yönergeler sunulmaktadır. Bu makalede birçok VM Uzantısı kullanılabildiği için, her biri potansiyel olarak benzersiz bir yapılandırma olan Genelleştirilmiş bilgiler sağlanmaktadır. Uzantıya özgü ayrıntılar, tek tek uzantıya özgü her belge içinde bulunabilir.

## <a name="use-cases-and-samples"></a>Kullanım örnekleri ve örnekler

Birçok farklı Azure VM uzantısı, her biri belirli bir kullanım durumu ile kullanılabilir. Bazı örnekler:

- Linux için DSC uzantısına sahip bir VM 'ye PowerShell Istenen durum yapılandırmasını uygulayın. Daha fazla bilgi için bkz. [Azure Istenen durum yapılandırması uzantısı](https://github.com/Azure/azure-linux-extensions/tree/master/DSC).
- VM 'yi Microsoft Monitoring Agent VM uzantısıyla izlemeyi yapılandırın. Daha fazla bilgi için bkz. [LINUX VM izleme](../linux/tutorial-monitoring.md).
- Chef veya Dataköpek uzantısıyla Azure altyapınızı izlemeyi yapılandırın. Daha fazla bilgi için [Chef docs](https://docs.chef.io/azure_portal.html) veya [dataköpek bloguna](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/)bakın.

İşleme özgü uzantılara ek olarak, hem Windows hem de Linux sanal makineleri için özel bir betik uzantısı vardır. Linux için özel Betik uzantısı, bir sanal makine üzerinde herhangi bir bash komut dosyasının çalıştırılmasını sağlar. Özel betikler, yerel Azure araçlarının sağlayabildiklerinin ötesinde yapılandırılması gereken Azure dağıtımlarını tasarlamak için yararlıdır. Daha fazla bilgi için bkz. [LINUX VM özel Betik uzantısı](custom-script-linux.md).

## <a name="prerequisites"></a>Önkoşullar

SANAL makinede uzantıyı işlemek için Azure Linux aracısının yüklü olması gerekir. Bazı ayrı uzantılar, kaynaklara veya bağımlılıklara erişim gibi önkoşullara sahiptir.

### <a name="azure-vm-agent"></a>Azure VM aracısı

Azure VM Aracısı, bir Azure VM ile Azure yapı denetleyicisi arasındaki etkileşimleri yönetir. VM Aracısı, çalışan VM uzantıları dahil olmak üzere Azure VM 'Leri dağıtmanın ve yönetmenin pek çok işlevsel yönünden sorumludur. Azure VM Aracısı, Azure Market görüntülerine önceden yüklenir ve desteklenen işletim sistemlerine el ile yüklenebilir. Linux için Azure VM Aracısı, Linux Aracısı olarak bilinir.

Desteklenen işletim sistemleri ve yükleme yönergeleri hakkında daha fazla bilgi için bkz. [Azure sanal makine Aracısı](agent-linux.md).

#### <a name="supported-agent-versions"></a>Desteklenen Aracı sürümleri

Mümkün olan en iyi deneyimi sağlamak için aracının en düşük sürümü vardır. Daha fazla bilgi için [bu makaleye](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) bakın.

#### <a name="supported-oses"></a>Desteklenen Işletim sistemleri

Linux Aracısı birden çok OSE üzerinde çalışır, ancak uzantı çerçevesinin bu uzantılara yönelik bir sınırı vardır. Daha fazla bilgi için [bu makaleye](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems
) bakın.

Bazı uzantılar tüm Işletim sistemlerinde desteklenmez ve *51 hata kodu, ' desteklenmeyen işletim sistemi '* verilebilir. Desteklenebilirliği için bağımsız uzantı belgelerini denetleyin.

#### <a name="network-access"></a>Ağ erişimi

Uzantı paketleri Azure Storage uzantı deposundan indirilir ve uzantı durumu karşıya yüklemeleri Azure depolama 'ya gönderilir. Aracıların [desteklenen](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) sürümünü kullanıyorsanız, aracıyı aracı Iletişimleri için Azure Fabric denetleyicisine yönlendirmek üzere aracı kullanabilmesi gibi, sanal makine bölgesindeki Azure depolama 'ya erişime izin vermeniz gerekmez. Aracının desteklenmeyen bir sürümü kullanıyorsanız, VM 'den o bölgedeki Azure depolama 'ya giden erişime izin vermeniz gerekir.

> [!IMPORTANT]
> Konuk güvenlik duvarını kullanarak *168.63.129.16* 'e erişimi engellediyse, uzantılar yukarıdaki bağımsız olarak başarısız olur.

Aracılar yalnızca uzantı paketleri ve raporlama durumunu indirmek için kullanılabilir. Örneğin, bir uzantı yüklemesinin GitHub 'dan (özel betik) bir betiği indirmesi veya Azure depolama 'ya (Azure Backup) erişmesi gerekiyorsa, ek güvenlik duvarı/ağ güvenlik grubu bağlantı noktalarının açılması gerekir. Farklı uzantılar, kendi sağında uygulamalar olduklarından farklı gereksinimlere sahiptir. Azure depolama 'ya erişim gerektiren uzantılar için, [depolama](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)için Azure NSG hizmet etiketlerini kullanarak erişime izin verebilirsiniz.

Aracı trafik isteklerini yeniden yönlendirmek için, Linux aracısının proxy sunucu desteği vardır. Ancak, bu proxy sunucu desteği uzantıları uygulamaz. Her bir uzantıyı bir ara sunucu ile çalışacak şekilde yapılandırmanız gerekir.

## <a name="discover-vm-extensions"></a>VM uzantılarını bulma

Azure VM'leri ile kullanabileceğiniz birçok farklı VM uzantısı vardır. Listenin tamamını görmek için [az VM Extension image List](/cli/azure/vm/extension/image#az-vm-extension-image-list)kullanın. Aşağıdaki örnek, *westus* konumundaki tüm kullanılabilir uzantıları listeler:

```azurecli
az vm extension image list --location westus --output table
```

## <a name="run-vm-extensions"></a>VM uzantılarını Çalıştır

Azure VM uzantıları, mevcut VM 'lerde çalışır, bu, önceden dağıtılmış bir VM 'de yapılandırma değişiklikleri yapmanız veya bağlantıyı kurtarmanız gerektiğinde yararlı olur. VM uzantıları, Azure Resource Manager şablon dağıtımları ile de paketlenmiştir. Azure VM 'Leri, Kaynak Yöneticisi şablonlarıyla birlikte uzantıları kullanarak dağıtım sonrası müdahalesi olmadan dağıtılabilir ve yapılandırılabilir.

Aşağıdaki yöntemler mevcut bir VM 'ye karşı bir uzantı çalıştırmak için kullanılabilir.

### <a name="azure-cli"></a>Azure CLI

Azure VM uzantıları, [az VM Extension set](/cli/azure/vm/extension#az-vm-extension-set) komutuyla mevcut bir VM 'ye karşı çalıştırılabilir. Aşağıdaki örnek, *Myresourcegroup*adlı kaynak grubunda *MYVM* adlı bir VM 'ye karşı özel Betik uzantısı 'nı çalıştırır. Çalıştırılacak örnek kaynak grubu adı, VM adı ve betiği (https:\//raw.githubusercontent.com/me/project/hello.sh) kendi bilgileriniz ile değiştirin. 

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/me/project/hello.sh"],"commandToExecute": "./hello.sh"}'
```

Uzantı doğru şekilde çalıştığında, çıktı aşağıdaki örneğe benzer:

```bash
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Azure portalında

VM uzantıları, mevcut bir VM 'ye Azure portal aracılığıyla uygulanabilir. Portalda VM 'yi seçin, **Uzantılar**' ı seçin ve **Ekle**' yi seçin. Kullanılabilir uzantılar listesinden istediğiniz uzantıyı seçin ve sihirbazdaki yönergeleri izleyin.

Aşağıdaki görüntüde, Azure portal Linux özel betik uzantısının yüklenmesi gösterilmektedir:

![Özel Betik uzantısı 'nı yükler](./media/features-linux/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager şablonları

VM uzantıları, bir Azure Resource Manager şablonuna eklenebilir ve şablonun dağıtımıyla birlikte yürütülür. Bir uzantıyı bir şablon ile dağıttığınızda, tam olarak yapılandırılmış Azure dağıtımları oluşturabilirsiniz. Örneğin, aşağıdaki JSON, yük dengeli VM 'Ler ve Azure SQL veritabanı 'nı dağıtan bir Kaynak Yöneticisi şablonundan alınır ve ardından her VM 'ye bir .NET Core uygulaması yükler. VM uzantısı, yazılım yüklemesini üstlenir.

Daha fazla bilgi için bkz. tam [Kaynak Yöneticisi şablonu](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
    }
}
```

Kaynak Yöneticisi şablonları oluşturma hakkında daha fazla bilgi için bkz. [yazma Azure Resource Manager şablonları](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>VM Uzantısı verilerini güvenli hale getirme

Bir VM Uzantısı çalıştırdığınızda kimlik bilgileri, depolama hesabı adları ve depolama hesabı erişim anahtarları gibi hassas bilgileri eklemek gerekebilir. Birçok VM uzantısı, verileri şifreleyen ve yalnızca hedef VM 'nin şifresini çözdüğü korumalı bir yapılandırma içerir. Her uzantının belirli bir korumalı yapılandırma şeması vardır ve her biri uzantıya özgü belgelerde ayrıntılıdır.

Aşağıdaki örnekte, Linux için özel betik uzantısının bir örneği gösterilmektedir. Yürütülecek komut bir kimlik bilgileri kümesi içerir. Bu örnekte, yürütülecek komut şifrelenmemiştir:

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ],
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

Aşağıdaki örnekte gösterildiği gibi, komutu, **korumalı** yapılandırmaya özelliği **yürütmek üzere** taşımak, yürütme dizesinin güvenliğini sağlar:

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

### <a name="how-do-agents-and-extensions-get-updated"></a>Aracılar ve uzantılar nasıl güncelleştirilir?

Aracılar ve uzantılar aynı güncelleştirme mekanizmasını paylaşır. Bazı güncelleştirmeler ek güvenlik duvarı kuralları gerektirmez.

Bir güncelleştirme kullanılabilir olduğunda, yalnızca Uzantılardaki bir değişiklik olduğunda VM 'de ve diğer VM modeli değiştikçe şu şekilde değişir:

- Veri diskleri
- Uzantılar
- Önyükleme tanılaması kapsayıcısı
- Konuk işletim sistemi gizli dizileri
- VM boyutu
- Ağ profili

Yayımcılar, güncelleştirmeleri farklı zamanlarda bölgelere uygun hale getirir, böylece farklı sürümlerde farklı bölgelerde sanal makinelere sahip olabilirsiniz.

#### <a name="agent-updates"></a>Aracı güncelleştirmeleri

Linux VM Aracısı, bir pakette *sağlama Aracısı Kodu* ve *uzantı işleme kodu* içerir ve bu ayrılamaz. Cloud-init kullanarak Azure 'da sağlamak istediğinizde *sağlama aracısını* devre dışı bırakabilirsiniz. Bunu yapmak için bkz. [Cloud-init kullanma](../linux/using-cloud-init.md).

Aracıların desteklenen sürümleri otomatik güncelleştirmeleri kullanabilir. Güncelleştirilebileceğiniz tek kod, sağlama kodu değil, *uzantı işleme kodudur*. *Sağlama Aracısı Kodu* , tek bir kez çalıştırılır.

*Uzantı işleme kodu* , Azure dokusuna iletişim kurmaktan ve Yüklemeler, raporlama durumu, tek tek uzantıları güncelleştirme ve bunları KALDıRMA gibi VM uzantıları işlemlerinin işlenmesine yöneliktir. Güncelleştirmeler, güvenlik düzeltmelerini, hata düzeltmelerini ve *uzantı işleme koduna*yönelik geliştirmeleri içerir.

Aracı yüklendiğinde, bir üst Daemon oluşturulur. Bu üst daha sonra, uzantıları işlemek için kullanılan bir alt işlem çoğaltılır. Aracı için bir güncelleştirme varsa, bu karşıdan yüklenir, üst işlem alt işlemi sonlandırır, yükseltir ve sonra yeniden başlatılır. Güncelleştirmede bir sorun olması gerekir, üst işlem önceki alt sürüme geri döner.

Üst işlem otomatik olarak güncelleştirilemez. Üst öğe yalnızca bir paket güncelleştirmesi tarafından güncelleştirilemez.

Çalıştırdığınız sürümü denetlemek için `waagent` aşağıdaki gibi denetleyin:

```bash
waagent --version
```

Çıktı aşağıdaki örneğe benzer:

```bash
WALinuxAgent-2.2.17 running on ubuntu 16.04
Python: 3.5.2
Goal state agent: 2.2.18
```

Yukarıdaki örnek çıktıda, üst veya ' paket dağıtılan sürüm ', *Walınuxagent-2.2.17*

' Hedef durum Aracısı ' otomatik güncelleştirme sürümüdür.

Her zaman aracı için otomatik güncelleştirme yapmanız önerilir, Otomatik Güncelleştir [. etkin = y](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent). Bu özelliği etkinleştirmediyseniz, aracıyı el ile güncelleştirmeniz gerekir ve hata ve güvenlik düzeltmelerini almaz.

#### <a name="extension-updates"></a>Uzantı güncelleştirmeleri

Bir uzantı güncelleştirmesi kullanılabilir olduğunda, Linux Aracısı uzantıyı indirir ve yükseltir. Otomatik uzantı güncelleştirmeleri, *küçük* veya *Düzeltme*olabilir. Uzantıyı sağladığınızda uzantıların *küçük* güncelleştirmelerini kabul edebilir veya devre dışı bırakabilirsiniz. Aşağıdaki örnek, bir Kaynak Yöneticisi şablonundaki alt sürümlerin otomatik olarak *': true, '* ile nasıl yükseltilleceğini gösterir:

```json
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
```

En son küçük sürüm hata düzeltmelerini almak için, uzantı dağıtımlarınızda her zaman otomatik güncelleştirme ' yi seçmeniz önemle tavsiye edilir. Güvenlik veya anahtar hata düzeltmelerini taşıyan düzeltme güncelleştirmeleri devre dışı bırakılamaz.

### <a name="how-to-identify-extension-updates"></a>Uzantı güncelleştirmelerini belirleme

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Uzantının, bir VM 'de oto Upgrademinorversion ile ayarlanmış olup olmadığını belirleme

Uzantının ' oto Upgrademinorversion ' ile sağlanması durumunda VM modelinden bakabilirsiniz. Denetlemek için [az VM Show](/cli/azure/vm#az-vm-show) kullanın ve kaynak grubunu ve VM adını aşağıdaki gibi sağlayın:

```azurecli
az vm show --resource-group myResourceGroup --name myVM
```

Aşağıdaki örnek çıktıda, ' *true*' olarak ayarlanmış *olan bir çıktı* gösterilmektedir:

```json
  "resources": [
    {
      "autoUpgradeMinorVersion": true,
      "forceUpdateTag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/CustomScriptExtension",
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Bir bir bir bir bir bir bir bir oto Upgrademinorversion

Uzantıya yönelik bir güncelleştirmenin ne zaman oluştuğunu görmek için, */var/log/waagent.log*konumundaki VM 'deki aracı günlüklerini gözden geçirin.

Aşağıdaki örnekte, VM 'nin *Microsoft. OSTCExtensions. LinuxDiagnostic-2.3.9025* yüklendi. *Microsoft. OSTCExtensions. LinuxDiagnostic-2.3.9027*için bir düzeltme sunuldu:

```bash
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Expected handler state: enabled
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Decide which version to use
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Use version: 2.3.9027
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Current handler state is: NotInstalled
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Download extension package
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Unpack extension package
INFO Event: name=Microsoft.OSTCExtensions.LinuxDiagnostic, op=Download, message=Download succeeded
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Initialize extension directory
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Update settings file: 0.settings
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025] Disable extension.
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025] Launch command:diagnostic.py -disable
...
INFO Event: name=Microsoft.OSTCExtensions.LinuxDiagnostic, op=Disable, message=Launch command succeeded: diagnostic.py -disable
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Update extension.
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Launch command:diagnostic.py -update
2017/08/14 20:21:57 LinuxAzureDiagnostic started to handle.
```

## <a name="agent-permissions"></a>Aracı izinleri

Görevlerini gerçekleştirmek için aracının *kök*olarak çalıştırılması gerekir.

## <a name="troubleshoot-vm-extensions"></a>VM uzantılarının sorunlarını giderme

Her VM uzantısının uzantıya özgü sorun giderme adımları olabilir. Örneğin, Özel Betik uzantısını kullandığınızda, komut dosyası yürütme ayrıntıları, uzantının çalıştırıldığı VM 'de yerel olarak bulunabilir. Uzantıya özgü tüm sorun giderme adımları uzantıya özgü belgelerde ayrıntılıdır.

Aşağıdaki sorun giderme adımları tüm VM uzantıları için geçerlidir.

1. Linux Aracısı günlüğünü denetlemek için, uzantınızın */var/log/waagent.log* adresinde sağlanması durumunda etkinliğe bakın.

2. */Var/log/azure/\<extensionName >* hakkında daha fazla ayrıntı için gerçek uzantı günlüklerine bakın

3. Hata kodları, bilinen sorunlar vb. için uzantıya özgü belge sorunlarını giderme bölümüne bakın.

3. Sistem günlüklerine bakın. Uzantıya sahip olabilecek, Özel Paket Yöneticisi erişimi gerektiren başka bir uygulamanın uzun süre çalışan yüklemesi gibi diğer işlemleri denetleyin.

### <a name="common-reasons-for-extension-failures"></a>Uzantı hatalarının yaygın nedenleri

1. Uzantıların çalışması için 20 dakika vardır (özel durumlar CustomScript Extensions, Chef ve 90 dakikalık bir DSC 'Dir). Dağıtımınız bu saati aşarsa, zaman aşımı olarak işaretlenir. Bunun nedeni, düşük kaynak VM 'lerinin, diğer VM yapılandırmalarının/başlangıç görevlerinin, uzantının sağlamaya çalıştığı yüksek miktarda kaynak tüketiyor olmasından kaynaklanabilir.

2. En düşük önkoşul karşılanmadı. Bazı uzantıların, HPC görüntüleri gibi VM SKU 'Larında bağımlılıkları vardır. Uzantılar, Azure depolama veya ortak hizmetlere iletişim gibi belirli ağ erişim gereksinimleri gerektirebilir. Diğer örnekler paket depolarına, yetersiz disk alanı veya güvenlik kısıtlamalarına erişebilir.

3. Özel Paket Yöneticisi erişimi. Bazı durumlarda, büyük bir çalışan VM yapılandırması ve dahili uzantı yüklemesi ile karşılaşmanız, burada her ikisinin de paket yöneticisine özel erişime ihtiyacı olabilir.

### <a name="view-extension-status"></a>Uzantı durumunu görüntüle

VM uzantısı bir VM 'ye karşı çalıştırıldıktan sonra, aşağıdaki gibi uzantı durumunu döndürmek için [az VM Get-instance-View](/cli/azure/vm#az-vm-get-instance-view) kullanın:

```azurecli
az vm get-instance-view \
    --resource-group rgName \
    --name myVM \
    --query "instanceView.extensions"
```

Çıktı aşağıdaki örnek çıkışına benzer:

```bash
  {
    "name": "customScript",
    "statuses": [
      {
        "code": "ProvisioningState/failed/0",
        "displayStatus": "Provisioning failed",
        "level": "Error",
        "message": "Enable failed: failed to execute command: command terminated with exit status=127\n[stdout]\n\n[stderr]\n/bin/sh: 1: ech: not found\n",
        "time": null
      }
    ],
    "substatuses": null,
    "type": "Microsoft.Azure.Extensions.customScript",
    "typeHandlerVersion": "2.0.6"
  }
```

Uzantı yürütme durumu Azure portal da bulunabilir. Bir uzantının durumunu görüntülemek için VM 'yi seçin, **Uzantılar**' ı seçin ve ardından istediğiniz uzantıyı seçin.

### <a name="rerun-a-vm-extension"></a>VM uzantısını yeniden çalıştırma

Bir VM uzantısının yeniden çalıştırılması gereken durumlar olabilir. Bir uzantıyı kaldırarak yeniden çalıştırabilir ve sonra uzantıyı seçtiğiniz bir yürütme yöntemiyle yeniden çalıştırabilirsiniz. Bir uzantıyı kaldırmak için [az VM Extension Delete](/cli/azure/vm/extension#az-vm-extension-delete) ' i aşağıda gösterildiği gibi kullanın:

```azurecli
az vm extension delete \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name customScript
```

Azure portal bir uzantıyı aşağıdaki gibi da kaldırabilirsiniz:

1. Bir VM seçin.
2. **Uzantılar**' ı seçin.
3. İstediğiniz uzantıyı seçin.
4. **Kaldır**' ı seçin.

## <a name="common-vm-extension-reference"></a>Ortak VM Uzantısı başvurusu

| Uzantı adı | Açıklama | Daha fazla bilgi |
| --- | --- | --- |
| Linux için özel Betik uzantısı |Azure sanal makinesinde betikleri çalıştırma |[Linux için özel Betik uzantısı](custom-script-linux.md) |
| VM Erişimi uzantısı |Bir Azure sanal makinesine yeniden erişim elde edin |[VM Erişimi uzantısı](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Azure Tanılama uzantısı |Azure Tanılama Yönet |[Azure Tanılama uzantısı](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Azure VM erişimi uzantısı |Kullanıcıları ve kimlik bilgilerini yönetme |[Linux için VM erişimi uzantısı](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Sonraki adımlar

VM uzantıları hakkında daha fazla bilgi için bkz. [Azure sanal makine uzantılarına ve özelliklerine genel bakış](overview.md).
