---
title: Azure CLı ile Azure ölçek kümeleri için diskleri şifreleme
description: Bir Windows sanal makine ölçek kümesindeki sanal makine örneklerini ve bağlı diskleri şifrelemek için Azure CLı 'yi nasıl kullanacağınızı öğrenin
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/15/2019
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: af870b3be9c2ab7022a05c9cf9e3a662c5850214
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875080"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-the-azure-cli"></a>Azure CLı ile bir sanal makine ölçek kümesindeki işletim sistemini ve bağlı veri disklerini şifreleme

Azure CLI, komut satırından veya betik içindeki Azure kaynaklarını oluşturmak ve yönetmek için kullanılır. Bu hızlı başlangıçta, bir sanal makine ölçek kümesi oluşturmak ve şifrelemek için Azure CLı 'nın nasıl kullanılacağı gösterilmektedir. Azure disk şifrelemesini bir sanal makine ölçek kümesine uygulama hakkında daha fazla bilgi için bkz. [Sanal Makine Ölçek Kümeleri Için Azure disk şifrelemesi](disk-encryption-overview.md).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin sürüm 2.0.31 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="create-a-scale-set"></a>Ölçek kümesi oluşturma

Ölçek kümesi oluşturabilmek için [az group create](/cli/azure/group) ile bir kaynak grubu oluşturun. Aşağıdaki örnek *eastus* konumunda *myresourcegroup* adlı bir kaynak grubu oluşturur:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Bu adımda [az vmss create](/cli/azure/vmss) ile bir sanal makine ölçek kümesi oluşturun. Aşağıdaki örnek, değişiklikler uygulandıkça otomatik güncelleştirilecek şekilde ayarlanan *myScaleSet* adlı bir ölçek kümesi oluşturur ve *~/.ssh/id_rsa* içinde yoksa SSH anahtarları oluşturur. Her bir sanal makine örneğine bir 32Gb veri diski iliştirilir ve [az VMSS uzantı kümesiyle](/cli/azure/vmss/extension)veri disklerini hazırlamak Için Azure [Özel Betik uzantısı](../virtual-machines/extensions/custom-script-linux.md) kullanılır:

```azurecli-interactive
# Create a scale set with attached data disk
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 32

# Prepare the data disk for use with the Custom Script Extension
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

Tüm ölçek kümesi kaynaklarının ve VM'lerin oluşturulup yapılandırılması birkaç dakika sürer.

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Disk şifrelemesi için etkin bir Azure Anahtar Kasası oluşturma

Azure Key Vault, uygulamaları ve hizmetlerinize güvenli bir şekilde uygulamanızı sağlayan anahtarları, parolaları veya parolaları depolayabilirler. Şifreleme anahtarları yazılım korumasını kullanarak Azure Key Vault depolanır veya anahtarlarınızı FIPS 140-2 düzey 2 standartlarına sertifikalı donanım güvenlik modüllerinde (HSM 'ler) içeri aktarabilir veya oluşturabilirsiniz. Bu şifreleme anahtarları, VM 'nize bağlı sanal diskleri şifrelemek ve şifrelerini çözmek için kullanılır. Bu şifreleme anahtarlarının denetimini koruyabilir ve kullanımlarını denetleyebilir.

Kendi benzersiz *keyvault_name* tanımlayın. Daha sonra, ölçek kümesiyle aynı abonelikte ve bölgede [az keykasatıon Create](/cli/azure/keyvault#az_keyvault_create) Ile bir keykasa oluşturun ve *--Enabled-for-disk şifrelemesi* erişim ilkesini ayarlayın.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### <a name="use-an-existing-key-vault"></a>Mevcut bir Key Vault kullan

Bu adım yalnızca disk şifrelemesi ile kullanmak istediğiniz mevcut bir Key Vault varsa gereklidir. Önceki bölümde bir Key Vault oluşturduysanız bu adımı atlayın.

Kendi benzersiz *keyvault_name* tanımlayın. Ardından, [az keykasatıon Update](/cli/azure/keyvault#az_keyvault_update) Ile keykasalarınızı güncelledi ve *--Enabled-for-disk şifrelemesi* erişim ilkesini ayarlayın.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault update --name $keyvault_name --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>Şifrelemeyi etkinleştir

Bir ölçek kümesindeki sanal makine örneklerini şifrelemek için, öncelikle Key Vault kaynak KIMLIĞIYLE ilgili bazı bilgileri [az keykasashow](/cli/azure/keyvault#az_keyvault_show)komutuyla alın. Bu değişkenler daha sonra [az VMSS ENCRYPTION Enable](/cli/azure/vmss/encryption#az_vmss_encryption_enable)ile şifreleme işlemini başlatmak için kullanılır:

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

Şifreleme işleminin başlaması bir veya iki dakika sürebilir.

Ölçek kümesi, önceki bir adımda oluşturulan ölçek kümesindeki yükseltme ilkesi *Otomatik* olarak ayarlandığından, sanal makine örnekleri otomatik olarak şifreleme işlemini başlatır. Yükseltme ilkesinin el ile olduğu ölçek kümelerinde, [az VMSS Update-Instances](/cli/azure/vmss#az_vmss_update_instances)ile sanal makine örneklerinde şifreleme ilkesini başlatın.

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Anahtarı kaydırmak için KEK kullanarak şifrelemeyi etkinleştirme

Ayrıca, sanal makine ölçek kümesini şifrelerken ek güvenlik için anahtar şifreleme anahtarını da kullanabilirsiniz.

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --key-encryption-key myKEK \
    --key-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

> [!NOTE]
>  Disk-Encryption-keykasası parametresinin değeri için sözdizimi tam tanımlayıcı dizesidir:</br>
/Subscriptions/[abonelik-kimliği-GUID]/resourceGroups/[resource-Group-Name]/providers/Microsoft.KeyVault/vaults/[keykasaadı]</br></br>
> Anahtar şifreleme-anahtar parametresinin değeri için sözdizimi, KEK içinde olduğu gibi tam URI 'dir:</br>
https://[keykasaadı]. kasa. Azure. net/Keys/[kekname]/[kek-Unique-ID]

## <a name="check-encryption-progress"></a>Şifreleme ilerlemesini denetleme

Disk şifrelemenin durumunu denetlemek için [az VMSS ENCRYPTION Show](/cli/azure/vmss/encryption#az_vmss_encryption_show)komutunu kullanın:

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

VM örnekleri şifrelendiğinde, aşağıdaki örnek çıktıda gösterildiği gibi durum kodu *EncryptionState/* encrypı bildirir:

```console
[
  {
    "disks": [
      {
        "encryptionSettings": null,
        "name": "myScaleSet_myScaleSet_0_disk2_3f39c2019b174218b98b3dfae3424e69",
        "statuses": [
          {
            "additionalProperties": {},
            "code": "EncryptionState/encrypted",
            "displayStatus": "Encryption is enabled on disk",
            "level": "Info",
            "message": null,
            "time": null
          }
        ]
      }
    ],
    "id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualMachines/0",
    "resourceGroup": "MYRESOURCEGROUP"
  }
]
```

## <a name="disable-encryption"></a>Şifrelemeyi devre dışı bırak

Artık şifrelenmiş VM örnekleri disklerini kullanmak istemiyorsanız, [az VMSS ENCRYPTION Disable](/cli/azure/vmss/encryption#az_vmss_encryption_disable) ile şifrelemeyi şu şekilde devre dışı bırakabilirsiniz:

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## <a name="next-steps"></a>Sonraki adımlar

- Bu makalede, Azure CLı 'yi bir sanal makine ölçek kümesini şifrelemek için kullandınız. [Azure PowerShell](disk-encryption-powershell.md) veya [Azure Resource Manager şablonlarını](disk-encryption-azure-resource-manager.md)da kullanabilirsiniz.
- Başka bir uzantı sağlandıktan sonra Azure disk şifrelemesi 'nin uygulanmasını istiyorsanız [uzantı sıralamasını](virtual-machine-scale-sets-extension-sequencing.md)kullanabilirsiniz. 
- Linux ölçek kümesi veri diski şifrelemesi için uçtan uca toplu iş dosyası örneği [burada](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat)bulunabilir. Bu örnek, bir kaynak grubu, Linux ölçek kümesi oluşturur, 5 GB 'lık bir veri diski bağlar ve sanal makine ölçek kümesini şifreler.
