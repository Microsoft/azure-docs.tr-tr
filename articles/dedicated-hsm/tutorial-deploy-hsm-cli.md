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
ms.custom: mvc, seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: b845ecabe74040e154886476a8ba28efecc99325
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868870"
---
# <a name="tutorial-deploying-hsms-into-an-existing-virtual-network-using-the-azure-cli"></a>Öğretici: Azure CLı kullanarak mevcut bir sanal ağa HSMs dağıtma

Azure adanmış HSM, tam yönetim denetimi ve tam yönetim sorumluluğuna sahip tek müşteri kullanımı için fiziksel bir cihaz sağlar. Fiziksel cihazların kullanımı, kapasitenin etkin bir şekilde yönetilmesini sağlamak üzere cihaz ayırmayı denetmek için Microsoft 'a yönelik ihtiyacı oluşturur. Sonuç olarak, bir Azure aboneliği içinde, ayrılmış HSM hizmeti normalde kaynak sağlama için görünür olmayacaktır. Adanmış HSM hizmetine erişmesi gereken tüm Azure müşterileri, öncelikle adanmış HSM hizmeti için kayıt istemek üzere Microsoft hesabı Executive ile iletişim kurmanız gerekir. Bu işlem başarıyla tamamlandığında, sağlama mümkün olacaktır. 

Bu öğreticide, şu durumlarda tipik bir sağlama işlemi gösterilmektedir:

- Müşterinin zaten bir sanal ağı var
- Bir sanal makinesi vardır
- Bu mevcut ortama HSM kaynakları eklemesi gerekir.

Tipik, yüksek kullanılabilirlik, çok bölgeli bir dağıtım mimarisi aşağıdaki gibi görünebilir:

![çok bölgeli dağıtım](media/tutorial-deploy-hsm-cli/high-availability-architecture.png)

Bu öğretici, var olan bir sanal ağla tümleştirildiği bir dizi HSM 'ye ve gerekli ExpressRoute Gateway 'e odaklanır (yukarıdaki VM 1 ' e bakın).  Diğer tüm kaynaklar standart Azure kaynaklarıdır. Aynı tümleştirme işlemi, yukarıdaki VNET 3 ' te alt ağ 4 ' te HSM 'ler için kullanılabilir.

## <a name="prerequisites"></a>Önkoşullar

Azure ayrılmış HSM Şu anda Azure portal kullanılamıyor. Hizmetle tüm etkileşim, komut satırı aracılığıyla veya PowerShell kullanılarak yapılır. Bu öğretici Azure Cloud Shell komut satırı (CLı) arabirimini kullanacaktır. Azure CLı 'yi yeni kullanmaya başladıysanız, başlangıç yönergelerini buradan izleyin: [Azure clı 2,0 kullanmaya](/cli/azure/get-started-with-azure-cli)başlayın.

Varsayımlar:

- Azure ayrılmış HSM kayıt işlemini tamamladınız
- Hizmetin kullanımı onaylandı. Aksi takdirde Ayrıntılar için Microsoft hesabı temsilcinizle iletişime geçin.
- Bu kaynaklar için bir kaynak grubu oluşturdunuz ve bu öğreticide dağıtılan yeni olanlar bu gruba katılacak.
- Yukarıdaki diyagram uyarınca gerekli sanal ağ, alt ağ ve sanal makineleri zaten oluşturdunuz ve şimdi 2 HSM 'leri Bu dağıtıma tümleştirmek istiyorsunuz.

Aşağıdaki tüm yönergeler Azure portal zaten gezindiyseniz ve Cloud Shell açtınız ( \> \_ portalın sağ üst köşesinde yer alan "" seçeneğini belirleyin).

## <a name="provisioning-a-dedicated-hsm"></a>Adanmış bir HSM sağlama

HSMs sağlama ve ExpressRoute ağ geçidi aracılığıyla mevcut bir sanal ağ ile tümleştirme, SSH kullanılarak doğrulanacak. Bu doğrulama, daha fazla yapılandırma etkinliği için HSM cihazının ulaşılabilirliğini ve temel kullanılabilirliğini sağlamaya yardımcı olur.

### <a name="validating-feature-registration"></a>Özellik kaydı doğrulanıyor

Yukarıda belirtildiği gibi, tüm sağlama etkinlikleri aboneliğiniz için ayrılmış HSM hizmetinin kayıtlı olmasını gerektirir. Bunu doğrulamak için, Azure portal Cloud Shell aşağıdaki komutları çalıştırın.

```azurecli
az feature show \
   --namespace Microsoft.HardwareSecurityModules \
   --name AzureDedicatedHSM
```

Komutlar "kayıtlı" (aşağıda gösterildiği gibi) durumunu döndürmelidir. Komutlar "kayıtlı" döndürmezse, Microsoft hesabı temsilcinizle iletişime geçerek bu hizmete kaydolmanız gerekir.

![Abonelik durumu](media/tutorial-deploy-hsm-cli/subscription-status.png)

### <a name="creating-hsm-resources"></a>HSM kaynakları oluşturma

HSM kaynaklarını oluşturmadan önce, ihtiyacınız olan bazı önkoşul kaynakları vardır. İşlem, HSMs ve ağ geçidi için alt ağ aralıklarına sahip bir sanal ağınız olmalıdır. Aşağıdaki komutlar, böyle bir sanal ağın nasıl oluşturacağına ilişkin bir örnek olarak görev yapar.

```azurecli
az network vnet create \
  --name myHSM-vnet \
  --resource-group myRG \
  --address-prefix 10.2.0.0/16 \
  --subnet-name compute \
  --subnet-prefix 10.2.0.0/24
```

```azurecli
az network vnet subnet create \
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

Ağınızı yapılandırdıktan sonra, HSMs 'nizi sağlamak için bu Azure CLı komutlarını kullanın.

1. İlk HSM sağlamak için [az adanmış-HSM Create](/cli/azure/dedicated-hsm#az_dedicated_hsm_create) komutunu kullanın. HSM 'nin adı hsm1. Aboneliğinizi yerine koyun:

   ```azurecli
   az dedicated-hsm create --location westus --name hsm1 --resource-group myRG --network-profile-network-interfaces \
        /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Network/virtualNetworks/MyHSM-vnet/subnets/MyHSM-vnet
   ```

   Bu dağıtım, HSM cihazları olan bu sürenin toplu olarak tamamlanması yaklaşık 25 ila 30 dakika sürer.

1. Geçerli bir HSM 'yi görmek için [az adanmış-HSM Show](/cli/azure/dedicated-hsm#az_dedicated_hsm_show) komutunu çalıştırın:

   ```azurecli
   az dedicated-hsm show --resource group myRG --name hsm1
   ```

1. Şu komutu kullanarak ikinci HSM 'yi sağlayın:

   ```azurecli
   az dedicated-hsm create --location westus --name hsm2 --resource-group myRG --network-profile-network-interfaces \
        /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Network/virtualNetworks/MyHSM-vnet/subnets/MyHSM-vnet
   ```

1. Geçerli HSM 'larınız hakkındaki ayrıntıları görüntülemek için [az adanmış-HSM List](/cli/azure/dedicated-hsm#az_dedicated_hsm_list) komutunu çalıştırın:

   ```azurecli
   az dedicated-hsm list --resource-group myRG
   ```

Yararlı olabilecek bazı komutlar vardır. Bir HSM 'yi güncelleştirmek için [az adanmış-HSM Update](/cli/azure/dedicated-hsm#az_dedicated_hsm_update) komutunu kullanın:

```azurecli
az dedicated-hsm update --resource-group myRG –name hsm1
```

Bir HSM 'yi silmek için [az adanmış-HSM Delete](/cli/azure/dedicated-hsm#az_dedicated_hsm_delete) komutunu kullanın:

```azurecli
az dedicated-hsm delete --resource-group myRG –name hsm1
```

## <a name="verifying-the-deployment"></a>Dağıtım doğrulanıyor

Cihazların sağlandığını doğrulamak ve cihaz özniteliklerini görmek için aşağıdaki komut kümesini çalıştırın. Kaynak grubunun uygun şekilde ayarlandığından ve kaynak adının tam olarak parametre dosyasında bulunduğundan emin olun.

```azurecli
subid=$(az account show --query id --output tsv)
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM1
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM2
```

Çıktı aşağıdaki çıktıya benzer bir şekilde görünür:

```json
{
    "id": n/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/HSM-RG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSMl",
    "identity": null,
    "kind": null,
    "location": "westus",
    "managedBy": null,
    "name": "HSM1",
    "plan": null,
    "properties": {
        "networkProfile": {
            "networkInterfaces": [
            {
            "id": n/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/HSM-RG/providers/Microsoft.Network/networkInterfaces/HSMl_HSMnic", "privatelpAddress": "10.0.2.5",
            "resourceGroup": "HSM-RG"
            }
            L
            "subnet": {
                "id": n/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/HSM-RG/providers/Microsoft.Network/virtualNetworks/demo-vnet/subnets/hsmsubnet", "resourceGroup": "HSM-RG"
            }
        },
        "provisioningState": "Succeeded",
        "stampld": "stampl",
        "statusMessage": "The Dedicated HSM device is provisioned successfully and ready to use."
    },
    "resourceGroup": "HSM-RG",
    "sku": {
        "capacity": null,
        "family": null,
        "model": null,
        "name": "SafeNet Luna Network HSM A790",
        "size": null,
        "tier": null
    },
    "tags": {
        "Environment": "prod",
        "resourceType": "Hsm"
    },
    "type": "Microsoft.HardwareSecurityModules/dedicatedHSMs"
}
```

Ayrıca, [Azure Kaynak Gezgini](https://resources.azure.com/)'ni kullanarak kaynakları görebileceksiniz.   Gezgin 'de, soldaki "abonelikler" i genişletin, adanmış HSM için özel aboneliğinizi genişletin, "kaynak grupları" nı genişletin, kullandığınız kaynak grubunu genişletin ve son olarak "kaynaklar" öğesini seçin.

## <a name="testing-the-deployment"></a>Dağıtımı test etme

Dağıtımı test etmek, HSM 'ye erişebilen ve ardından doğrudan HSM cihazına bağlanan bir sanal makineye bağlantı kurma ihtimaline sahip olabilir. Bu eylemler HSM 'nin erişilebilir olduğunu doğrulayacaktır.
SSH aracı, sanal makineye bağlanmak için kullanılır. Bu komut, parametresinde belirttiğiniz yönetici adı ve DNS adı ile benzerdir.

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

VM 'nin IP adresi, Yukarıdaki komutta DNS adı yerine de kullanılabilir. Komut başarılı olursa, parola ister ve bunu girmeniz gerekir. Sanal makinede oturum açtıktan sonra, HSM ile ilişkili ağ arabirimi kaynağı için portalda bulunan özel IP adresini kullanarak HSM 'de oturum açabilirsiniz.

![bileşenler listesi](media/tutorial-deploy-hsm-cli/resources.png)

>[!NOTE]
>Seçildiğinde, HSM kaynaklarını görüntüleyen "gizli türleri göster" onay kutusunu görürsünüz.

Yukarıdaki ekran görüntüsünde, "HSM1_HSMnic" veya "HSM2_HSMnic" tıklanması uygun özel IP adresini gösterir. Aksi takdirde, `az resource show` yukarıda kullanılan komut doğru IP adresini tanımlamak için bir yoldur. 

Doğru IP adresine sahip olduğunuzda, bu adresi değiştirerek aşağıdaki komutu çalıştırın:

`ssh tenantadmin@10.0.2.4`

Bu işlem başarılı olursa parola girmeniz istenir. Varsayılan parola PAROLADıR ve HSM öncelikle parolanızı değiştirmenizi isteyecektir, böylece güçlü bir parola ayarlayın ve kuruluşunuzun parolayı depolamak ve kaybı engellemek için tercih ettiği mekanizmaları kullanın.

>[!IMPORTANT]
>Bu parolayı kaybederseniz, HSM 'nin sıfırlanması ve anahtarlarınızın kaybedilmesi anlamına gelir.

SSH kullanarak HSM 'ye bağlandığınızda, HSM 'nin çalışır durumda olduğundan emin olmak için aşağıdaki komutu çalıştırın.

`hsm show`

Çıktı aşağıdaki görüntüde gösterildiği gibi görünmelidir:

![Ekran görüntüsü, PowerShell penceresinde çıktıyı gösterir.](media/tutorial-deploy-hsm-cli/hsm-show-output.png)

Bu noktada, yüksek oranda kullanılabilir, iki HSM dağıtımı ve doğrulanan erişim ve işlemsel durum için tüm kaynakları ayırmış olursunuz. Daha fazla yapılandırma veya test, HSM cihazının kendisi ile daha fazla iş içerir. Bunun için, HSM 'yi başlatmak ve bölüm oluşturmak üzere Thales Luna 7 HSM Yönetim Kılavuzu Bölüm 7 ' deki yönergeleri izlemelisiniz. [Thales müşteri destek portalına](https://supportportal.thalesgroup.com/csm) kaydolduktan ve müşterinin kimliği varsa, tüm belgeler ve yazılımlar doğrudan karşıdan yüklenmek üzere kullanılabilir. Gerekli tüm bileşenleri almak için Istemci yazılımı 7,2 sürümünü indirin.

## <a name="delete-or-clean-up-resources"></a>Kaynakları silme veya temizleme

Yalnızca HSM cihazını tamamladıysanız, kaynak olarak silinebilir ve ücretsiz havuza geri döndürülebilecek. Bu, cihazdaki önemli müşteri verileri olduğunda bu sorunu giderin. Bir cihazı "sıfırlama" yapmanın en iyi yolu, HSM Yönetici parolasının yanlış 3 kez alınacağını (Not: Bu gereç Yöneticisi değil, gerçek HSM Yöneticisi). Anahtar malzemesini korumanın bir güvenlik önlemi olarak, cihaz, sıfırlama durumunda olana kadar bir Azure kaynağı olarak silinemez.

> [!NOTE]
> herhangi bir Thales cihaz yapılandırmasıyla ilgili sorun yaşıyorsanız [Thales müşteri desteği](https://supportportal.thalesgroup.com/csm)'ne başvurmalısınız.

Bu kaynak grubundaki tüm kaynakları tamamladıysanız, bunları aşağıdaki komutla kaldırabilirsiniz:

```azurecli
az group delete \
   --resource-group myRG \
   --name HSMdeploy \
   --verbose
```

## <a name="next-steps"></a>Sonraki adımlar

Öğreticideki adımları tamamladıktan sonra, özel HSM kaynakları sağlanır ve HSM ile iletişimi etkinleştirmek için gerekli HSM 'ler ve daha fazla ağ bileşeni içeren bir sanal ağınız vardır.  Artık bu dağıtımı tercih ettiğiniz dağıtım mimariniz için gereken diğer kaynaklarla karmaşıklama eden bir pozisyonda olursunuz. Dağıtımınızı planlamaya yardımcı olma hakkında daha fazla bilgi için bkz. kavramlar belgeleri.
Birincil bölgedeki iki HSM 'yi raf düzeyinde adresleyen ve bir ikincil bölgedeki iki HSM 'nin bölgesel kullanılabilirliği ele aldığı bir tasarımın olması önerilir. 

* [Yüksek Kullanılabilirlik](high-availability.md)
* [Fiziksel Güvenlik](physical-security.md)
* [Ağ](networking.md)
* [Desteklenebilirlik](supportability.md)
* [İzleme](monitoring.md)
