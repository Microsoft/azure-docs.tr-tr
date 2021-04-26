---
title: Azure dosyaları için DNS iletmeyi yapılandırma | Microsoft Docs
description: Azure dosyaları için DNS iletmeyi yapılandırmayı öğrenin.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 3/19/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9abe306668a4b20e42e45c498bf85b540dfaaee5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94630201"
---
# <a name="configuring-dns-forwarding-for-azure-files"></a>Azure Dosyalar için DNS iletmeyi yapılandırma
Azure dosyaları, dosya paylaşımlarınızı içeren depolama hesapları için özel uç noktalar oluşturmanıza olanak sağlar. Birçok farklı uygulama için yararlı olsa da, Özel uç noktalar özellikle özel eşleme kullanarak bir VPN veya ExpressRoute bağlantısı kullanarak şirket içi ağınızdan Azure dosya paylaşımlarınıza bağlanmak için yararlıdır. 

Depolama Hesabınıza yönelik bağlantıların ağ Tüneliniz üzerinden gitmesi için, depolama hesabınızın tam etki alanı adı (FQDN) özel uç noktanızın özel IP adresine çözümlenmelidir. Bunu başarmak için, depolama uç noktası sonekini ( `core.windows.net` genel bulut bölgeleri için) sanal ağınızdan erişilebilen Azure özel DNS hizmetine iletmeniz gerekir. Bu kılavuzda, depolama hesabınızın özel uç nokta IP adresine doğru bir şekilde çözülmesi için DNS iletmeyi ayarlama ve yapılandırma işlemlerinin nasıl yapılacağı gösterilir.

Bu makalede açıklanan adımları tamamlamadan önce [bir Azure dosyaları dağıtımı Için planlamayı](storage-files-planning.md) ve [Azure dosyaları ağ oluşturma konularını](storage-files-networking-overview.md) okumanız önemle tavsiye ederiz.

## <a name="overview"></a>Genel Bakış
Azure dosyaları, Azure dosya paylaşımlarına erişmek için iki ana uç nokta türü sağlar: 
- Genel bir IP adresi olan ve dünyanın herhangi bir yerinden erişilebilen genel uç noktalar.
- Bir sanal ağ içinde bulunan ve bu sanal ağın adres alanından özel bir IP adresine sahip olan özel uç noktalar.

Azure depolama hesabında genel ve özel uç noktalar mevcuttur. Depolama hesabı, birden çok dosya paylaşımını ve BLOB kapsayıcıları ya da kuyrukları gibi diğer depolama kaynaklarını dağıtabileceğiniz, paylaşılan bir depolama havuzunu temsil eden bir yönetim yapısıdır.

Her depolama hesabının tam etki alanı adı (FQDN) vardır. Genel bulut bölgeleri için, bu FQDN, `storageaccount.file.core.windows.net` `storageaccount` depolama hesabının adı olan kalıbı izler. SMB kullanarak iş istasyonunuza bağlama gibi bu ada karşı istek yaptığınızda işletim sisteminiz, tam etki alanı adını SMB isteklerini göndermek için kullanabileceği bir IP adresine çözümlemek için bir DNS araması gerçekleştirir.

Varsayılan olarak, `storageaccount.file.core.windows.net` genel bitiş NOKTASıNıN IP adresini çözümler. Bir depolama hesabının genel uç noktası, diğer birçok depolama hesabının genel uç noktalarını barındıran bir Azure Depolama kümesinde barındırılır. Özel bir uç nokta oluşturduğunuzda, özel bir DNS bölgesi öğesine eklendiği sanal ağa bağlanır ve bu, `storageaccount.file.core.windows.net` depolama hesabınızın özel uç noktasının özel IP adresi Için bir kayıt girişine BIR CNAME kaydı eşlemesi olur. Bu `storageaccount.file.core.windows.net` , sanal ağ içinde FQDN kullanmanıza ve özel uç NOKTANıN IP adresine çözümlenmenize olanak sağlar.

En son hedefimiz, bir VPN veya ExpressRoute bağlantısı gibi bir ağ tüneli kullanarak, depolama hesabı dahilinde barındırılan Azure dosya paylaşımlarına erişmek için, şirket içi DNS sunucularınızı Azure dosyaları hizmetine yapılan istekleri Azure özel DNS hizmetine iletecek şekilde yapılandırmanız gerekir. Bunu gerçekleştirmek için,  `*.core.windows.net` Azure sanal AĞıNıZDA barındırılan bir DNS sunucusuna (veya ABD Kamu, Almanya veya Çin Ulusal bulutları için uygun depolama uç noktası sonekini) koşullu iletmeyi ayarlamanız gerekir. Daha sonra bu DNS sunucusu, depolama hesabının tam etki alanı adını uygun özel IP adresine çözümleyecek şekilde Azure 'un özel DNS hizmetine yapılan isteği yinelemeli olarak iletir.

Azure dosyaları için DNS iletmeyi yapılandırmak, istekleri iletmek üzere bir DNS sunucusu barındırmak için bir sanal makinenin çalıştırılmasını gerektirir, ancak bu, sanal ağınızda barındırılan tüm Azure dosya paylaşımları için tek seferlik bir adımdır. Buna ek olarak, Azure dosyaları için özel bir gereksinim değildir; Şirket içinden erişmek istediğiniz özel uç noktaları destekleyen herhangi bir Azure hizmeti, bu kılavuzda yapılandıracaksınız DNS iletmeyi kullanabilir: Azure Blob depolama, SQL Azure, Cosmos DB, vb. 

Bu kılavuzda, Azure depolama uç noktası için DNS iletmeyi yapılandırma adımları gösterilmektedir. bu nedenle Azure dosyalarına ek olarak, diğer tüm Azure depolama hizmetleri (Azure Blob depolama, Azure Tablo depolama, Azure kuyruk depolama, vb.) için DNS ad çözümlemesi istekleri Azure 'un özel DNS hizmetine iletilir. İsterseniz diğer Azure hizmetleri için ek uç noktalar da eklenebilir. Şirket içi DNS sunucularınızda DNS iletimi, şirket içi makine adlarını çözümlemek için sanal ağınızdaki (DFS-N sunucusu gibi) bulut kaynaklarını etkinleştirmek üzere de yapılandırılır. 

## <a name="prerequisites"></a>Önkoşullar
Azure dosyalarına DNS iletmeyi ayarlayabilmeniz için önce aşağıdaki adımları tamamlamış olmanız gerekir:

- Bağlamak istediğiniz bir Azure dosya paylaşımının bulunduğu depolama hesabı. Depolama hesabı ve Azure dosya paylaşımının nasıl oluşturulacağını öğrenmek için bkz. [Azure dosya paylaşma oluşturma](storage-how-to-create-file-share.md).
- Depolama hesabı için özel bir uç nokta. Azure dosyaları için özel bir uç nokta oluşturmayı öğrenmek için bkz. [Özel uç nokta oluşturma](storage-files-networking-endpoints.md#create-a-private-endpoint).
- Azure PowerShell modülünün [en son sürümü](/powershell/azure/install-az-ps) .

> [!Important]  
> Bu kılavuzda, şirket içi ortamınızda Windows Server 'da DNS sunucusunu kullandığınız varsayılır. Bu kılavuzda açıklanan adımların tümü yalnızca Windows DNS sunucusu değil, herhangi bir DNS sunucusu ile yapılabilir.

## <a name="manually-configuring-dns-forwarding"></a>DNS iletmeyi el ile yapılandırma
Azure sanal ağınızda zaten DNS sunucularınız varsa veya kuruluşunuzun kullandığı metodolojiye göre kendi sanal makinelerinizi DNS sunucuları olacak şekilde dağıtmayı tercih ediyorsanız, yerleşik DNS sunucusu PowerShell cmdlet 'leriyle DNS 'yi el ile yapılandırabilirsiniz.

Şirket içi DNS sunucularınızda, kullanarak koşullu bir iletici oluşturun `Add-DnsServerConditionalForwarderZone` . Trafiği Azure 'a doğru şekilde iletmek için bu koşullu ileticinin tüm şirket içi DNS sunucularınızda dağıtılması gerekir. `<azure-dns-server-ip>`Ortamınız için uygun IP adresleriyle değiştirmeyi unutmayın.

```powershell
$vnetDnsServers = "<azure-dns-server-ip>", "<azure-dns-server-ip>"

$storageAccountEndpoint = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty StorageEndpointSuffix

Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers $vnetDnsServers
```

Azure sanal ağınızdaki DNS sunucularında, depolama hesabı DNS bölgesi isteklerinin, ayrılmış IP adresi tarafından ele alındığı Azure özel DNS hizmetine yönlendirilmesi gibi bir iletici de yerleştirmeniz gerekir `168.63.129.16` . ( `$storageAccountEndpoint` Komutları farklı bir PowerShell oturumunda çalıştırıyorsanız, bu komutları doldurmanız gerektiğini unutmayın.)

```powershell
Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers "168.63.129.16"
```

## <a name="using-the-azure-files-hybrid-module-to-configure-dns-forwarding"></a>DNS iletmeyi yapılandırmak için Azure dosyaları karma modülünü kullanma
DNS iletmeyi mümkün olduğunca kolay bir şekilde yapılandırmak için, Azure dosyaları karma modülünde Otomasyon sağladık. Bu modüldeki DNS 'yi işlemek için sağlanan cmdlet 'ler, Azure sanal ağınıza DNS sunucuları dağıtmanıza ve şirket içi DNS sunucularınızı bunlara iletmek üzere güncelleştirmenize yardımcı olur. 

Azure dosyaları karma modülünü hiç kullanmadıysanız, önce iş istasyonunuza yüklemeniz gerekir. Azure dosyaları karma PowerShell modülünün [en son sürümünü](https://github.com/Azure-Samples/azure-files-samples/releases) indirin:

```PowerShell
# Unzip the downloaded file
Expand-Archive -Path AzFilesHybrid.zip

# Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\AzFilesHybrid\CopyToPSPath.ps1 

# Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid
```

DNS iletme çözümünün dağıtılması iki adımdan oluşur. istekleri iletmek istediğiniz Azure hizmetlerini ve DNS ileticilerinin gerçek dağıtımını tanımlayan bir DNS iletme kuralı kümesi oluşturur. 

Aşağıdaki örnek, istekleri depolama hesabına, Azure dosyaları, Azure Blob depolama, Azure Tablo depolama ve Azure kuyruk depolama 'ya dahil edilen istekleri iletir. İsterseniz, cmdlet 'in parametresi aracılığıyla kurala ek Azure hizmeti için iletme ekleyebilirsiniz `-AzureEndpoints` `New-AzDnsForwardingRuleSet` . `<virtual-network-resource-group>`, `<virtual-network-name>` Ve `<subnet-name>` değerlerini, ortamınız için uygun değerlerle değiştirmeyi unutmayın.

```PowerShell
# Create a rule set, which defines the forwarding rules
$ruleSet = New-AzDnsForwardingRuleSet -AzureEndpoints StorageAccountEndpoint

# Deploy and configure DNS forwarders
New-AzDnsForwarder `
        -DnsForwardingRuleSet $ruleSet `
        -VirtualNetworkResourceGroupName "<virtual-network-resource-group>" `
        -VirtualNetworkName "<virtual-network-name>" `
        -VirtualNetworkSubnetName "<subnet-name>"
```

Ayrıca, birkaç ek parametre sağlamak için yararlı/gerekli bulabilirsiniz:

| Parametre adı | Tür | Description |
|----------------|------|-------------|
| `DnsServerResourceGroupName` | `string` | Varsayılan olarak, DNS sunucuları sanal ağla aynı kaynak grubuna dağıtılır. Bu istenmiyorsa, bu parametre, içine dağıtılabilmesi için alternatif bir kaynak grubu seçmenizi sağlar. |
| `DnsForwarderRootName` | `string` | Varsayılan olarak, Azure 'da dağıtılan DNS sunucuları, `DnsFwder-*` yıldız işareti bir yineleyici tarafından doldurulduğu adlara sahiptir. Bu parametre, bu adın kökünü değiştirir (ör. `DnsFwder` ). |
| `VmTemporaryPassword` | `SecureString` | Varsayılan olarak, bir VM 'nin etki alanına katılmadan önce sahip olduğu geçici varsayılan hesap için rastgele bir parola seçilir. Etki alanına katılmış olduktan sonra, varsayılan hesap devre dışıdır. |
| `DomainToJoin` | `string` | Katılacak DNS VM 'leri içine katılacak etki alanı. Varsayılan olarak, bu etki alanı cmdlet 'lerini çalıştırdığınız bilgisayarın etki alanına göre seçilir. |
| `DnsForwarderRedundancyCount` | `int` | Sanal ağınız için dağıtılacak DNS sanal makine sayısı. Varsayılan olarak, `New-AzDnsForwarder` Artıklık sağlamak Için Azure sanal ağınızda ıkı DNS sunucusunu bir kullanılabilirlik kümesinde dağıtır. Bu sayı istenen şekilde değiştirilebilir. |
| `OnPremDnsHostNames` | `HashSet<string>` | Üzerinde ileticiler oluşturmak için şirket içi DNS ana bilgisayar adlarının el ile belirtilen bir listesi. Bu parametre, el ile belirtilen DNS adlarına sahip bir istemci aralığına sahip olduğunuzda olduğu gibi, tüm şirket içi DNS sunucularına ileticiler uygulamak istemediğiniz durumlarda faydalıdır. |
| `Credential` | `PSCredential` | DNS sunucuları güncelleştirilirken kullanılacak kimlik bilgileri. Bu, oturum açtığınız kullanıcı hesabının DNS ayarlarını değiştirme izinleri olmadığında yararlıdır. |
| `SkipParentDomain` | `SwitchParameter` | Varsayılan olarak, DNS ileticileri ortamınızda bulunan en üst düzey etki alanına uygulanır. Örneğin, `northamerica.corp.contoso.com` bir alt etki alanı ise `corp.contoso.com` , iletici Ile ilişkili DNS sunucuları için oluşturulur `corp.contoso.com` . Bu parametre, içinde ileticilerin oluşturulmasına neden olur `northamerica.corp.contoso.com` . |

## <a name="confirm-dns-forwarders"></a>DNS ileticilerini Onayla
DNS ileticilerinin başarıyla uygulanmış olup olmadığını görmek için test etmeden önce, kullanarak yerel iş istasyonunuzda DNS önbelleğini temizlemeniz önerilir `Clear-DnsClientCache` . Depolama hesabınızın tam etki alanı adını başarıyla çözümleyip çözebildiğini sınamak için, `Resolve-DnsName` veya kullanın `nslookup` .

```powershell
# Replace storageaccount.file.core.windows.net with the appropriate FQDN for your storage account.
# Note the proper suffix (core.windows.net) depends on the cloud your deployed in.
Resolve-DnsName -Name storageaccount.file.core.windows.net
```

Ad çözümlemesi başarılı olursa, çözümlenmiş IP adresinin depolama hesabınızın IP adresiyle eşleştiğini görmeniz gerekir.

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  29    Answer     csostoracct.privatelink.file.core.windows.net
net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 1769
Section    : Answer
IP4Address : 192.168.0.4
```

Zaten bir VPN veya ExpressRoute bağlantısı ayarladıysanız, `Test-NetConnection` depolama hesabınıza BIR TCP bağlantısının başarıyla yapıldığını görmek için ' yi de kullanabilirsiniz.

```PowerShell
Test-NetConnection -ComputerName storageaccount.file.core.windows.net -CommonTCPPort SMB
```

## <a name="see-also"></a>Ayrıca bkz.
- [Azure Dosyalar dağıtımını planlama](storage-files-planning.md)
- [Azure dosyaları ağ iletişimi konuları](storage-files-networking-overview.md)
- [Azure dosyaları ağ uç noktalarını yapılandırma](storage-files-networking-endpoints.md)