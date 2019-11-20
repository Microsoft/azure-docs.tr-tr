---
title: Öğretici Azure CLı kullanarak mevcut bir sanal ağa dağıtma-Azure ayrılmış HSM | Microsoft Docs
description: CLı kullanarak mevcut bir sanal ağa özel bir HSM 'nin nasıl dağıtılacağını gösteren öğretici
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
ms.openlocfilehash: 51e3bddef75bcf41b8c7a4d9693b622429130217
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73930465"
---
# <a name="tutorial-deploying-hsms-into-an-existing-virtual-network-using-cli"></a>Öğretici: CLı kullanarak mevcut bir sanal ağa HSMs dağıtma

Azure adanmış HSM, tam yönetim denetimi ve tam yönetim sorumluluğuna sahip tek müşteri kullanımı için fiziksel bir cihaz sağlar. Fiziksel cihazların kullanımı, kapasitenin etkin bir şekilde yönetilmesini sağlamak üzere cihaz ayırmayı denetmek için Microsoft 'a yönelik ihtiyacı oluşturur. Sonuç olarak, bir Azure aboneliği içinde, ayrılmış HSM hizmeti normalde kaynak sağlama için görünür olmayacaktır. Adanmış HSM hizmetine erişmesi gereken tüm Azure müşterileri, öncelikle adanmış HSM hizmeti için kayıt istemek üzere Microsoft hesabı Executive ile iletişim kurmanız gerekir. Bu işlem başarıyla tamamlandığında, sağlama mümkün olacaktır. 

Bu öğreticide, şu durumlarda tipik bir sağlama işlemi gösterilmektedir:

- Müşterinin zaten bir sanal ağı var
- Bir sanal makinesi vardır
- Bu mevcut ortama HSM kaynakları eklemesi gerekir.

Tipik, yüksek kullanılabilirlik, çok bölgeli bir dağıtım mimarisi aşağıdaki gibi görünebilir:

![çok bölgeli dağıtım](media/tutorial-deploy-hsm-cli/high-availability-architecture.png)

Bu öğretici, var olan bir sanal ağla tümleştirildiği bir dizi HSM 'ye ve gerekli ExpressRoute Gateway 'e odaklanır (yukarıdaki VM 1 ' e bakın).  Diğer tüm kaynaklar standart Azure kaynaklarıdır. Aynı tümleştirme işlemi, yukarıdaki VNET 3 ' te alt ağ 4 ' te HSM 'ler için kullanılabilir.

## <a name="prerequisites"></a>Önkoşullar

Azure ayrılmış HSM Şu anda Azure portal kullanılamıyor. Hizmetle tüm etkileşim, komut satırı aracılığıyla veya PowerShell kullanılarak yapılır. Bu öğretici Azure Cloud Shell komut satırı (CLı) arabirimini kullanacaktır. Azure CLı 'yi yeni kullanmaya başladıysanız, başlangıç yönergelerini buradan izleyin: [Azure clı 2,0 kullanmaya](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)başlayın.

Çoğu

- Azure ayrılmış HSM kayıt işlemini tamamladınız
- Hizmetin kullanımı onaylandı. Aksi takdirde Ayrıntılar için Microsoft hesabı temsilcinizle iletişime geçin.
- Bu kaynaklar için bir kaynak grubu oluşturdunuz ve bu öğreticide dağıtılan yeni olanlar bu gruba katılacak.
- Yukarıdaki diyagram uyarınca gerekli sanal ağ, alt ağ ve sanal makineleri zaten oluşturdunuz ve şimdi 2 HSM 'leri Bu dağıtıma tümleştirmek istiyorsunuz.

Aşağıdaki tüm yönergeler Azure portal zaten gezindiği ve Cloud Shell açtığınız varsayılır (portalın sağ üst köşesinde yer alan "\>\_" öğesini seçin).

## <a name="provisioning-a-dedicated-hsm"></a>Adanmış bir HSM sağlama

HSMs sağlama ve ExpressRoute ağ geçidi aracılığıyla mevcut bir sanal ağ ile tümleştirme, SSH kullanılarak doğrulanacak. Bu doğrulama, daha fazla yapılandırma etkinliği için HSM cihazının ulaşılabilirliğini ve temel kullanılabilirliğini sağlamaya yardımcı olur. Aşağıdaki komutlar, HSM kaynaklarını ve ilişkili ağ kaynaklarını oluşturmak için bir Azure Resource Manager şablonu kullanacaktır.

### <a name="validating-feature-registration"></a>Özellik kaydı doğrulanıyor

Yukarıda belirtildiği gibi, tüm sağlama etkinlikleri aboneliğiniz için ayrılmış HSM hizmetinin kayıtlı olmasını gerektirir. Bunu doğrulamak için Azure portal Cloud Shell 'de aşağıdaki komutları çalıştırın.

```azurecli
az feature show \
   --namespace Microsoft.HardwareSecurityModules \
   --name AzureDedicatedHSM
```

Aşağıdaki komut, adanmış HSM hizmeti için gereken ağ özelliklerini doğrular.

```azurecli
az feature show \
   --namespace Microsoft.Network \
   --name AllowBaremetalServers
```

Her iki komut de "kayıtlı" (aşağıda gösterildiği gibi) durumunu döndürmelidir. Komutlar bu hizmete kaydolmanız gereken "kayıtlı" döndürmezse, Microsoft hesabı temsilcinizle iletişime geçin.

![Abonelik durumu](media/tutorial-deploy-hsm-cli/subscription-status.png)

### <a name="creating-hsm-resources"></a>HSM kaynakları oluşturma

Bir HSM 'nin bir müşterinin sanal ağına sağlanması için sanal ağ ve alt ağ gerekir. Sanal ağ ile fiziksel cihaz arasında iletişimi etkinleştirmek için HSM 'nin bağımlılığı bir ExpressRoute ağ geçididir ve son olarak, HSM cihazına Gemalto istemci yazılımını kullanarak erişmek için bir sanal makine gerekir. Bu kaynaklar, kullanım kolaylığı için karşılık gelen parametre dosyası ile birlikte bir şablon dosyasına toplanır. Dosyalar Microsoft 'a doğrudan HSMrequest@Microsoft.comolarak iletişim kurarak kullanılabilir.

Dosyalar alındıktan sonra, kaynaklar için tercih ettiğiniz adları eklemek üzere parametre dosyasını düzenlemeniz gerekir. "Değer": "" ile satırları düzenleyin.

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

İlişkili Azure Resource Manager şablon dosyası, bu bilgilerle 6 kaynak oluşturacak:

- Belirtilen VNET 'te HSM 'ler için bir alt ağ
- Sanal ağ geçidi için bir alt ağ
- VNET 'i HSM cihazlarına bağlayan bir sanal ağ geçidi
- Ağ Geçidi için genel bir IP adresi
- Damga 1 ' de HSM
- Damga 2 ' de HSM

Parametre değerleri ayarlandıktan sonra, dosyaların kullanım için Azure portal Cloud Shell dosya paylaşımında karşıya yüklenmesi gerekir. Azure portal, "\>\_" Cloud Shell symbol sağ üst simgesine tıklayın ve bu, ekranın alt kısmını bir komut ortamı haline getirir. Bu seçenekler BASH ve PowerShell ' dir ve henüz ayarlanmamışsa BASH ' i seçmeniz gerekir.

Komut kabuğu, araç çubuğunda karşıya yükle/İndir seçeneğine sahiptir ve şablon ve parametre dosyalarını dosya paylaşımınıza yüklemek için bunu seçmeniz gerekir:

![dosya paylaşma](media/tutorial-deploy-hsm-cli/file-share.png)

Dosyalar karşıya yüklendikten sonra kaynak oluşturmaya hazırlanın. Yeni HSM kaynakları oluşturmadan önce bazı ön koşul kaynakları bulunduğundan emin olmanız gerekir. İşlem, HSMs ve ağ geçidi için alt ağ aralıklarına sahip bir sanal ağınız olmalıdır. Aşağıdaki komutlar, böyle bir sanal ağın nasıl oluşturacağına ilişkin bir örnek olarak görev yapar.

```azurecli
az network vnet create \
  --name myHSM-vnet \
  --resource-group myRG \
  --address-prefix 10.2.0.0/16
  --subnet-name compute
  --subnet-prefix 10.2.0.0/24
```

```azurecli
--vnet-name myHSM-vnet \
  --resource-group myRG \
  --name hsmsubnet \
  --address-prefixes 10.2.1.0/24 \
  --delegations Microsoft.HardwareSecurityModules/dedicatedHSMs
```

```azurecli
az network vnet subnet create \
  --vnet-name myHSM-vnet \
  --resource-group myRG \
  --name GatewaySubnet \
  --address-prefixes 10.2.255.0/26
```

>[!NOTE]
>Sanal ağ için dikkat edilmesi gereken en önemli yapılandırma, HSM cihazının alt ağının "Microsoft. HardwareSecurityModules/ayrılmış Atedhsms" olarak ayarlanmış temsilciler içermelidir.  Bu seçenek ayarlanmaksızın HSM sağlama çalışmayacaktır.

Tüm ön koşullar olduktan sonra, benzersiz adlarınızla (en azından kaynak grubu adı) değerleri güncelleştirdiğinizden emin olmak için Azure Resource Manager şablonunu kullanmak üzere aşağıdaki komutu çalıştırın:

```azurecli
az group deployment create \
   --resource-group myRG  \
   --template-file ./Deploy-2HSM-toVNET-Template.json \
   --parameters ./Deploy-2HSM-toVNET-Params.json \
   --name HSMdeploy \
   --verbose
```

Bu dağıtım, HSM cihazları olan bu sürenin toplu olarak tamamlanması yaklaşık 25 ila 30 dakika sürer

![Sağlama durumu](media/tutorial-deploy-hsm-cli/progress-status.png)

Dağıtım başarıyla tamamlandığında "provisioningState": "başarılı" görüntülenir. Mevcut sanal makinenize bağlanabilir ve HSM cihazının kullanılabilirliğini sağlamak için SSH kullanabilirsiniz.

## <a name="verifying-the-deployment"></a>Dağıtım doğrulanıyor

Cihazların sağlandığını doğrulamak ve cihaz özniteliklerini görmek için aşağıdaki komut kümesini çalıştırın. Kaynak grubunun uygun şekilde ayarlandığından ve kaynak adının tam olarak parametre dosyasında bulunduğundan emin olun.

```azurecli
subid=$(az account show --query id --output tsv)
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM1
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM2
```

![sağlama çıkışı](media/tutorial-deploy-hsm-cli/progress-status2.png)

Ayrıca, [Azure Kaynak Gezgini](https://resources.azure.com/)'ni kullanarak kaynakları görebileceksiniz.   Gezgin 'de, soldaki "abonelikler" i genişletin, adanmış HSM için özel aboneliğinizi genişletin, "kaynak grupları" nı genişletin, kullandığınız kaynak grubunu genişletin ve son olarak "kaynaklar" öğesini seçin.

## <a name="testing-the-deployment"></a>Dağıtımı test etme

Dağıtımı test etmek, HSM 'ye erişebilen ve ardından doğrudan HSM cihazına bağlanan bir sanal makineye bağlantı kurma ihtimaline sahip olabilir. Bu eylemler HSM 'nin erişilebilir olduğunu doğrulayacaktır.
SSH aracı, sanal makineye bağlanmak için kullanılır. Bu komut, parametresinde belirttiğiniz yönetici adı ve DNS adı ile benzerdir.

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

VM 'nin IP adresi, Yukarıdaki komutta DNS adı yerine de kullanılabilir. Komut başarılı olursa, parola ister ve bunu girmeniz gerekir. Sanal makinede oturum açtıktan sonra, HSM ile ilişkili ağ arabirimi kaynağı için portalda bulunan özel IP adresini kullanarak HSM 'de oturum açabilirsiniz.

![bileşenler listesi](media/tutorial-deploy-hsm-cli/resources.png)

>[!NOTE]
>Seçildiğinde, HSM kaynaklarını görüntüleyen "gizli türleri göster" onay kutusunu görürsünüz.

Yukarıdaki ekran görüntüsünde, "HSM1_HSMnic" veya "HSM2_HSMnic" tıklanması uygun özel IP adresini gösterir. Aksi takdirde, yukarıda kullanılan `az resource show` komutu doğru IP adresini tanımlamak için bir yoldur. 

Doğru IP adresine sahip olduğunuzda, bu adresi değiştirerek aşağıdaki komutu çalıştırın:

`ssh tenantadmin@10.0.2.4`

Bu işlem başarılı olursa parola girmeniz istenir. Varsayılan parola PAROLADıR ve HSM öncelikle parolanızı değiştirmenizi isteyecektir, böylece güçlü bir parola ayarlayın ve kuruluşunuzun parolayı depolamak ve kaybı engellemek için tercih ettiği mekanizmaları kullanın.

>[!IMPORTANT]
>Bu parolayı kaybederseniz, HSM 'nin sıfırlanması ve anahtarlarınızın kaybedilmesi anlamına gelir.

SSH kullanarak HSM 'ye bağlandığınızda, HSM 'nin çalışır durumda olduğundan emin olmak için aşağıdaki komutu çalıştırın.

`hsm show`

Çıktı aşağıdaki görüntüde gösterildiği gibi görünmelidir:

![bileşenler listesi](media/tutorial-deploy-hsm-cli/hsm-show-output.png)

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

```azurecli
az group deployment delete \
   --resource-group myRG \
   --name HSMdeploy \
   --verbose

```

## <a name="next-steps"></a>Sonraki adımlar

Öğreticideki adımları tamamladıktan sonra, özel HSM kaynakları sağlanır ve HSM ile iletişimi etkinleştirmek için gerekli HSM 'ler ve daha fazla ağ bileşeni içeren bir sanal ağınız vardır.  Artık bu dağıtımı tercih ettiğiniz dağıtım mimariniz için gereken diğer kaynaklarla karmaşıklama eden bir pozisyonda olursunuz. Dağıtımınızı planlamaya yardımcı olma hakkında daha fazla bilgi için bkz. kavramlar belgeleri.
Birincil bölgedeki iki HSM 'yi raf düzeyinde adresleyen ve bir ikincil bölgedeki iki HSM 'nin bölgesel kullanılabilirliği ele aldığı bir tasarımın olması önerilir. Bu öğreticide kullanılan şablon dosyası, iki HSM dağıtımı için temel olarak kolayca kullanılabilir, ancak parametrelerinizi karşılamak için parametrelerinin değiştirilmesini gerektirir.

* [Yüksek kullanılabilirlik](high-availability.md)
* [Fiziksel güvenlik](physical-security.md)
* [Ağ](networking.md)
* [Desteklenebilirlik](supportability.md)
* [İzleme](monitoring.md)
