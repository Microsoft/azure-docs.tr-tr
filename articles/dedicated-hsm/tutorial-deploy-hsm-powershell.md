---
title: Öğretici PowerShell kullanarak mevcut bir sanal ağa dağıtma-Azure ayrılmış HSM | Microsoft Docs
description: PowerShell kullanarak var olan bir sanal ağa özel bir HSM 'nin nasıl dağıtılacağını gösteren öğretici
services: dedicated-hsm
documentationcenter: na
author: msmbaldwin
manager: rkarlin
editor: ''
ms.service: key-vault
ms.topic: tutorial
ms.custom: mvc, seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 2f605d5adda913fa465b43a85bd027458959c122
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73928103"
---
# <a name="tutorial--deploying-hsms-into-an-existing-virtual-network-using-powershell"></a>Öğretici: PowerShell kullanarak var olan bir sanal ağa HSM 'leri dağıtma

Azure ayrılmış HSM hizmeti, tam yönetim denetimi ve tam yönetim sorumluluğuna sahip tek müşteri kullanımı için fiziksel bir cihaz sağlar. Fiziksel donanım sağlanmasından dolayı Microsoft 'un bu cihazların nasıl ayrıldığını kontrol etmelidir ve bu da kapasitenin etkin bir şekilde yönetilmesini sağlar. Sonuç olarak, bir Azure aboneliği içinde, ayrılmış HSM hizmeti normalde kaynak sağlama için görünür olmayacaktır. Adanmış HSM hizmetine erişmesi gereken tüm Azure müşterileri, öncelikle adanmış HSM hizmeti için kayıt istemek üzere Microsoft hesabı Executive ile iletişim kurmanız gerekir. Bu işlem başarıyla tamamlandığında, sağlama mümkün olacaktır.
Bu öğreticide, şu durumlarda tipik bir sağlama işlemi gösterilmektedir:

- Müşterinin zaten bir sanal ağı var
- Bir sanal makinesi vardır
- Bu mevcut ortama HSM kaynakları eklemesi gerekir.

Tipik, yüksek kullanılabilirlik, çok bölgeli bir dağıtım mimarisi aşağıdaki gibi görünebilir:

![çok bölgeli dağıtım](media/tutorial-deploy-hsm-powershell/high-availability.png)

Bu öğretici, mevcut bir sanal ağla tümleştirildiği bir dizi HSM 'ye ve gerekli ExpressRoute Gateway 'e (Yukarıdaki alt ağ 1 ' e bakın) odaklanmaktadır (bkz. VNET 1).  Diğer tüm kaynaklar standart Azure kaynaklarıdır. Aynı tümleştirme işlemi, yukarıdaki VNET 3 ' te alt ağ 4 ' te HSM 'ler için kullanılabilir.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar

Azure ayrılmış HSM Azure portal Şu anda kullanılamıyor, bu nedenle hizmetle tüm etkileşim komut satırı veya PowerShell kullanılarak yapılır. Bu öğretici Azure Cloud Shell PowerShell 'i kullanacaktır. PowerShell 'i kullanmaya yeni başladıysanız başlangıç yönergelerini buradan izleyin: [Azure PowerShell kullanmaya](https://docs.microsoft.com/powershell/azure/get-started-azureps)başlayın.

Çoğu

- Azure ayrılmış HSM kayıt işlemini tamamladınız ve hizmetin kullanımı onaylandı. Aksi takdirde, Ayrıntılar için Microsoft hesabı temsilcinizle iletişime geçin. 
- Bu kaynaklar için bir kaynak grubu oluşturdunuz ve bu öğreticide dağıtılan yeni olanlar bu gruba katılacak.
- Yukarıdaki diyagram uyarınca gerekli sanal ağ, alt ağ ve sanal makineleri zaten oluşturdunuz ve şimdi 2 HSM 'leri Bu dağıtıma tümleştirmek istiyorsunuz.

Aşağıdaki tüm yönergeler Azure portal zaten gezindiği ve Cloud Shell açtığınız varsayılır (portalın sağ üst köşesinde yer alan "\>\_" öğesini seçin).

## <a name="provisioning-a-dedicated-hsm"></a>Adanmış bir HSM sağlama

Daha fazla yapılandırma etkinliği için HSM cihazının erişilebilir ve temel kullanılabilirliğini sağlamak amacıyla, HSMs sağlama ve ExpressRoute ağ geçidi aracılığıyla mevcut bir sanal ağla tümleştirme işlemi, SSH komut satırı aracı kullanılarak onaylanır. Aşağıdaki komutlar, HSM kaynaklarını ve ilişkili ağ kaynaklarını oluşturmak için bir Kaynak Yöneticisi şablonu kullanacaktır.

### <a name="validating-feature-registration"></a>Özellik kaydı doğrulanıyor

Yukarıda belirtildiği gibi, tüm sağlama etkinlikleri aboneliğiniz için ayrılmış HSM hizmetinin kayıtlı olmasını gerektirir. Bunu doğrulamak için, Azure portal Cloud Shell 'de aşağıdaki PowerShell komutunu çalıştırın. 

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.HardwareSecurityModules -FeatureName AzureDedicatedHsm
```

Aşağıdaki komut, adanmış HSM hizmeti için gereken ağ özelliklerini doğrular.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowBaremetalServers
```

Her iki komut de devam etmeden önce "kayıtlı" (aşağıda gösterildiği gibi) durumunu döndürmelidir.  Bu hizmete kaydolmanız gerekiyorsa Microsoft hesabı temsilcinizle iletişime geçin.

![Abonelik durumu](media/tutorial-deploy-hsm-powershell/subscription-status.png)

### <a name="creating-hsm-resources"></a>HSM kaynakları oluşturma

Bir HSM cihazı, müşterilerin sanal ağına temin edilir. Bu, bir alt ağ gereksinimini gösterir. Sanal ağ ile fiziksel cihaz arasında iletişimi etkinleştirmek için HSM 'nin bağımlılığı bir ExpressRoute ağ geçididir ve son olarak, HSM cihazına Gemalto istemci yazılımını kullanarak erişmek için bir sanal makine gerekir. Bu kaynaklar, kullanım kolaylığı için karşılık gelen parametre dosyası ile birlikte bir şablon dosyasına toplanır. Dosyalar, Microsoft 'a doğrudan HSMrequest@Microsoft.comadresinden iletişim kurarak kullanılabilir.

Dosyalar alındıktan sonra, kaynaklar için tercih ettiğiniz adları eklemek üzere parametre dosyasını düzenlemeniz gerekir. Bu, satırları "değer": "" ile düzenlemeniz anlamına gelir.

- HSM kaynaklarının adları için `namingInfix` ön eki
- HSM 'ler için kullanılan sanal ağın `ExistingVirtualNetworkName` adı
- veri merkezi damgası 1 ' deki HSM kaynağının `DedicatedHsmResourceName1` adı
- veri merkezi damgası 2 ' deki HSM kaynağının `DedicatedHsmResourceName2` adı
- HSMs için `hsmSubnetRange` alt ağ IP adresi aralığı
- VNET ağ geçidi için `ERSubnetRange` alt ağ IP adresi aralığı

Bu değişikliklere bir örnek aşağıdaki gibidir:

```json
{
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "namingInfix": {
      "value": "MyHSM"
    },
    "ExistingVirtualNetworkName": {
      "value": "MyHSM-vnet"
    },
    "DedicatedHsmResourceName1": {
      "value": "HSM1"
    },
    "DedicatedHsmResourceName2": {
      "value": "HSM2"
    },
    "hsmSubnetRange": {
      "value": "10.0.2.0/24"
    },
    "ERSubnetRange": {
      "value": "10.0.255.0/26"
    },
  }
}
```

İlişkili Kaynak Yöneticisi Şablon dosyası, bu bilgilerle 6 kaynak oluşturacak:

- Belirtilen VNET 'te HSM 'ler için bir alt ağ
- Sanal ağ geçidi için bir alt ağ 
- VNET 'i HSM cihazlarına bağlayan bir sanal ağ geçidi
- Ağ Geçidi için genel bir IP adresi
- Damga 1 ' de HSM
- Damga 2 ' de HSM

Parametre değerleri ayarlandıktan sonra, dosyaların kullanım için Azure portal Cloud Shell dosya paylaşımında karşıya yüklenmesi gerekir. Azure portal, "\>\_" Cloud Shell symbol sağ üst simgesine tıklayın ve bu, ekranın alt kısmını bir komut ortamı haline getirir. Bu seçenekler BASH ve PowerShell ' dir ve henüz ayarlanmamışsa BASH ' i seçmeniz gerekir.

Komut kabuğu, araç çubuğunda karşıya yükle/İndir seçeneğine sahiptir ve şablon ve parametre dosyalarını dosya paylaşımınıza yüklemek için bunu seçmeniz gerekir:

![dosya paylaşma](media/tutorial-deploy-hsm-powershell/file-share.png)

Dosyalar karşıya yüklendikten sonra kaynak oluşturmaya hazırlanın.
Yeni HSM kaynakları oluşturmadan önce bazı ön koşul kaynakları bulunduğundan emin olmanız gerekir. İşlem, HSMs ve ağ geçidi için alt ağ aralıklarına sahip bir sanal ağınız olmalıdır. Aşağıdaki komutlar, böyle bir sanal ağın nasıl oluşturacağına ilişkin bir örnek olarak görev yapar.

```powershell
$compute = New-AzVirtualNetworkSubnetConfig `
  -Name compute `
  -AddressPrefix 10.2.0.0/24
```

```powershell
$delegation = New-AzDelegation `
  -Name "myDelegation" `
  -ServiceName "Microsoft.HardwareSecurityModules/dedicatedHSMs"

```

```powershell
$hsmsubnet = New-AzVirtualNetworkSubnetConfig ` 
  -Name hsmsubnet ` 
  -AddressPrefix 10.2.1.0/24 ` 
  -Delegation $delegation 

```

```powershell

$gwsubnet= New-AzVirtualNetworkSubnetConfig `
  -Name GatewaySubnet `
  -AddressPrefix 10.2.255.0/26

```

```powershell

New-AzVirtualNetwork `
  -Name myHSM-vnet `
  -ResourceGroupName myRG `
  -Location westus `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $compute, $hsmsubnet, $gwsubnet

```

>[!NOTE]
>Sanal ağ için dikkat edilmesi gereken en önemli yapılandırma, HSM cihazının alt ağının "Microsoft. HardwareSecurityModules/ayrılmış Atedhsms" olarak ayarlanmış temsilciler içermelidir.  HSM sağlama bu olmadan çalışmaz.

Tüm ön koşullar olduktan sonra, benzersiz adlarınızla (en azından kaynak grubu adı) değerleri güncelleştirdiğinizden emin olmak için Kaynak Yöneticisi şablonunu kullanmak üzere aşağıdaki komutu çalıştırın:

```powershell

New-AzResourceGroupDeployment -ResourceGroupName myRG `
     -TemplateFile .\Deploy-2HSM-toVNET-Template.json `
     -TemplateParameterFile .\Deploy-2HSM-toVNET-Params.json `
     -Name HSMdeploy -Verbose

```

Bu komutun tamamlanması yaklaşık 20 dakika sürer. Kullanılan "-verbose" seçeneği, durumun sürekli görüntülendiğini güvence altına alacak.

![Sağlama durumu](media/tutorial-deploy-hsm-powershell/progress-status.png)

Başarıyla tamamlandığında, "provisioningState": "başarılı" olarak gösterildiği gibi, mevcut sanal makinenizde oturum açabilir ve HSM cihazının kullanılabilirliğini sağlamak için SSH kullanabilirsiniz.

## <a name="verifying-the-deployment"></a>Dağıtım doğrulanıyor

Cihazların sağlandığını doğrulamak ve cihaz özniteliklerini görmek için aşağıdaki komut kümesini çalıştırın. Kaynak grubunun uygun şekilde ayarlandığından ve kaynak adının tam olarak parametre dosyasında bulunduğundan emin olun.

```powershell

$subid = (Get-AzContext).Subscription.Id
$resourceGroupName = "myRG"
$resourceName = "HSM1"  
Get-AzResource -Resourceid /subscriptions/$subId/resourceGroups/$resourceGroupName/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/$resourceName

```

![sağlama durumu](media/tutorial-deploy-hsm-powershell/progress-status2.png)

Ayrıca, [Azure Kaynak Gezgini](https://resources.azure.com/)'ni kullanarak kaynakları görebileceksiniz.   Gezgin 'de, soldaki "abonelikler" i genişletin, adanmış HSM için özel aboneliğinizi genişletin, "kaynak grupları" nı genişletin, kullandığınız kaynak grubunu genişletin ve son olarak "kaynaklar" öğesini seçin.

## <a name="testing-the-deployment"></a>Dağıtımı test etme

Dağıtımı test etmek, HSM 'ye erişebilen ve ardından doğrudan HSM cihazına bağlanan bir sanal makineye bağlantı kurma ihtimaline sahip olabilir. Bu eylemler HSM 'nin erişilebilir olduğunu doğrulayacaktır.
SSH aracı, sanal makineye bağlanmak için kullanılır. Bu komut, parametresinde belirttiğiniz yönetici adı ve DNS adı ile benzerdir.

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

Kullanılacak parola parametre dosyasından biridir.
Linux VM 'de oturum açtıktan sonra, Portal 'da bulunan özel IP adresini kullanarak HSM 'de, kaynak \<önek > hsm_vnic oturum açabilirsiniz.

```powershell

(Get-AzResource -ResourceGroupName myRG -Name HSMdeploy -ExpandProperties).Properties.networkProfile.networkInterfaces.privateIpAddress

```
IP adresine sahip olduğunuzda, aşağıdaki komutu çalıştırın:

`ssh tenantadmin@<ip address of HSM>`

Bu işlem başarılı olursa parola girmeniz istenir. Varsayılan parola PAROLADıR. HSM, parolanızı değiştirmenizi isteyecektir, böylece güçlü bir parola ayarlayın ve kuruluşunuzun parolayı depolamak ve kaybı engellemek için tercih ettiği mekanizmayı kullanın.  

>[!IMPORTANT]
>Bu parolayı kaybederseniz, HSM 'nin sıfırlanması ve anahtarlarınızın kaybedilmesi anlamına gelir.

SSH kullanarak HSM cihazına bağlandığınızda, HSM 'nin çalışır durumda olduğundan emin olmak için aşağıdaki komutu çalıştırın.

`hsm show`

Çıktı aşağıda gösterilen görüntü gibi görünmelidir:

![sağlama durumu](media/tutorial-deploy-hsm-powershell/output.png)

Bu noktada, yüksek oranda kullanılabilir, iki HSM dağıtımı ve doğrulanan erişim ve işlemsel durum için tüm kaynakları ayırmış olursunuz. Daha fazla yapılandırma veya test, HSM cihazının kendisi ile daha fazla iş içerir. Bu şekilde, HSM 'yi başlatmak ve bölüm oluşturmak için Gemalto Luna ağ HSM 7 Yönetim Kılavuzu Bölüm 7 ' deki yönergeleri izlemelisiniz. Tüm belgeler ve yazılımlar, Gemalto müşteri destek portalına kaydolduktan ve müşterinin KIMLIĞI varsa, doğrudan Gemalto yüklenmek üzere kullanılabilir. Gerekli tüm bileşenleri almak için Istemci yazılımı 7,2 sürümünü indirin.

## <a name="delete-or-clean-up-resources"></a>Kaynakları silme veya temizleme

Yalnızca HSM cihazını tamamladıysanız, kaynak olarak silinebilir ve ücretsiz havuza geri döndürülebilecek. Bu, cihazdaki önemli müşteri verileri olduğunda bu sorunu giderin. Hassas müşteri verilerini kaldırmak için cihazın, Gemalto istemcisi kullanılarak fabrika sıfırlaması gerekir. SafeNet Network Luna 7 cihazı için Gemalto yöneticileri kılavuzuna başvurun ve aşağıdaki komutları sırasıyla göz önünde bulundurun.

1. `hsm factoryReset -f`
2. `sysconf config factoryReset -f -service all`
3. `my file clear -f`
4. `my public-key clear -f`
5. `syslog rotate`


> [!NOTE]
> herhangi bir Gemalto cihaz yapılandırmasıyla ilgili sorun yaşıyorsanız, [Gemalto müşteri desteği](https://safenet.gemalto.com/technical-support/)'ne başvurmalısınız.

Bu kaynak grubundaki kaynaklarla işiniz bittiğinde, bunları aşağıdaki komutla kaldırabilirsiniz:

```powershell

$subid = (Get-AzContext).Subscription.Id
$resourceGroupName = "myRG" 
$resourceName = "HSMdeploy"  
Remove-AzResource -Resourceid /subscriptions/$subId/resourceGroups/$resourceGroupName/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/$resourceName 

```

## <a name="next-steps"></a>Sonraki adımlar

Öğreticideki adımları tamamladıktan sonra, adanmış HSM kaynakları sanal ağınızda sağlanır ve kullanılabilir. Artık bu dağıtımı tercih ettiğiniz dağıtım mimariniz için gereken diğer kaynaklarla karmaşıklama eden bir pozisyonda olursunuz. Dağıtımınızı planlamaya yardımcı olma hakkında daha fazla bilgi için bkz. kavramlar belgeleri. Birincil bölgedeki iki HSM 'yi raf düzeyinde adresleyen ve bir ikincil bölgedeki iki HSM 'nin bölgesel kullanılabilirliği ele aldığı bir tasarımın olması önerilir. Bu öğreticide kullanılan şablon dosyası, iki HSM dağıtımı için temel olarak kolayca kullanılabilir, ancak parametrelerinizi karşılamak için parametrelerinin değiştirilmesini gerektirir.

* [Yüksek kullanılabilirlik](high-availability.md)
* [Fiziksel güvenlik](physical-security.md)
* [Ağ](networking.md)
* [İzleme](monitoring.md)
* [Desteklenebilirlik](supportability.md)
