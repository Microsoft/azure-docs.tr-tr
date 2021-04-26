---
title: Önizleme-kendi anahtarlarınız ile şifrelenmiş bir görüntü sürümü oluşturma
description: Paylaşılan görüntü galerisinde, müşteri tarafından yönetilen şifreleme anahtarlarını kullanarak bir görüntü sürümü oluşturun.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/3/2020
ms.author: cynthn
ms.openlocfilehash: 601b8236ca413dd510585bdfffddc3e892caa73b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759678"
---
# <a name="preview-use-customer-managed-keys-for-encrypting-images"></a>Önizleme: görüntüleri şifrelemek için müşteri tarafından yönetilen anahtarları kullanın

Paylaşılan görüntü galerisindeki görüntüler anlık görüntü olarak depolanır, bu nedenle otomatik olarak sunucu tarafı şifrelemesi aracılığıyla şifrelenir. Sunucu tarafı şifrelemesi, en güçlü blok şifrelemeleri bulunan 256 bit [AES şifrelemesi](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)kullanır. Sunucu tarafı şifreleme da FIPS 140-2 ile uyumludur. Azure yönetilen diskleri temel alan şifreleme modülleri hakkında daha fazla bilgi için bkz. [şifreleme API 'si: yeni nesil](/windows/desktop/seccng/cng-portal).

Görüntülerinizin şifrelenmesi için platform tarafından yönetilen anahtarları kullanabilir veya kendi anahtarlarınızı kullanabilirsiniz. Çift şifreleme için her ikisini birlikte de kullanabilirsiniz. Şifrelemeyi kendi anahtarlarınız ile yönetmeyi seçerseniz, görüntülerinizdeki tüm diskleri şifrelemek ve şifrelerini çözmek için kullanılacak *müşteri tarafından yönetilen bir anahtar* belirtebilirsiniz. 

Müşteri tarafından yönetilen anahtarlar aracılığıyla sunucu tarafı şifreleme Azure Key Vault kullanır. [RSA anahtarlarınızı](../key-vault/keys/hsm-protected-keys.md) anahtar kasanıza aktarabilir ya da Azure Key Vault yeni RSA anahtarları oluşturabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu makale, görüntünüzü çoğaltmak istediğiniz her bölgede zaten bir disk şifrelemesi ayarlamış olmanızı gerektirir:

- Yalnızca müşteri tarafından yönetilen bir anahtar kullanmak için [Azure Portal](./disks-enable-customer-managed-keys-portal.md) veya [PowerShell](./windows/disks-enable-customer-managed-keys-powershell.md#set-up-an-azure-key-vault-and-diskencryptionset-without-automatic-key-rotation)'i kullanarak, müşteri tarafından yönetilen anahtarları sunucu tarafı şifrelemesiyle Etkinleştirme makalesine bakın.

- Hem platform tarafından yönetilen hem de müşteri tarafından yönetilen anahtarları kullanmak için (Çift şifreleme için), [Azure Portal](./disks-enable-double-encryption-at-rest-portal.md) veya [PowerShell](./windows/disks-enable-double-encryption-at-rest-powershell.md)kullanarak Rest 'te çift şifrelemeyi Etkinleştirme makalesine bakın.

   > [!IMPORTANT]
   > [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates)Azure Portal erişmek için bağlantıyı kullanmanız gerekir. Bu bağlantıyı kullanmadığınız takdirde, Rest 'te Çift şifreleme Şu anda genel Azure portal görünür değildir.

## <a name="limitations"></a>Sınırlamalar

Paylaşılan bir görüntü galerisinde resimleri şifrelemek için müşteri tarafından yönetilen anahtarlar kullanırken, bu sınırlamalar geçerlidir:   

- Şifreleme anahtarı kümeleri görüntresimle aynı abonelikte olmalıdır.

- Şifreleme anahtarı kümeleri bölgesel kaynaklardır, bu nedenle her bölge farklı bir şifreleme anahtarı kümesi gerektirir.

- Müşteri tarafından yönetilen anahtarlar kullanan görüntüleri kopyalayamaz veya paylaşamazsınız. 

- Bir diski veya görüntüyü şifrelemek için kendi anahtarlarınızı kullandıktan sonra, bu diskleri veya görüntüleri şifrelemek için platform tarafından yönetilen anahtarları kullanmaya geri dönemezsiniz.


> [!IMPORTANT]
> Müşteri tarafından yönetilen anahtarlar aracılığıyla şifreleme Şu anda genel önizlemededir.
> Bu önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve bunu üretim iş yükleri için önermiyoruz. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="powershell"></a>PowerShell

Genel önizleme için önce özelliği kaydetmeniz gerekir:

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Kaydın tamamlanabilmesi birkaç dakika sürer. `Get-AzProviderFeature`Özellik kaydının durumunu denetlemek için kullanın:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

`RegistrationState`Döndüğünde `Registered` , bir sonraki adıma geçebilirsiniz.

Sağlayıcı kaydınızı denetleyin. Döndürdüğünden emin olun `Registered` .

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
```

Döndürmezse `Registered` , sağlayıcıları kaydetmek için aşağıdaki kodu kullanın:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

Bir görüntü sürümüne bir disk şifreleme kümesi belirtmek için,  [New-Azgallerımagedefinition](/powershell/module/az.compute/new-azgalleryimageversion) parametresini parametresiyle birlikte kullanın `-TargetRegion` : 

```azurepowershell-interactive

$sourceId = <ID of the image version source>

$osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet'}

$dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet1';Lun=1}

$dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet2';Lun=2}

$dataDiskImageEncryptions = @($dataDiskImageEncryption1,$dataDiskImageEncryption2)

$encryption1 = @{OSDiskImage=$osDiskImageEncryption;DataDiskImages=$dataDiskImageEncryptions}

$region1 = @{Name='West US';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption1}

$eastUS2osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet'}

$eastUS2dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet1';Lun=1}

$eastUS2dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet2';Lun=2}

$eastUS2DataDiskImageEncryptions = @($eastUS2dataDiskImageEncryption1,$eastUS2dataDiskImageEncryption2)

$encryption2 = @{OSDiskImage=$eastUS2osDiskImageEncryption;DataDiskImages=$eastUS2DataDiskImageEncryptions}

$region2 = @{Name='East US 2';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption2}

$targetRegion = @($region1, $region2)


# Create the image
New-AzGalleryImageVersion `
   -ResourceGroupName $rgname `
   -GalleryName $galleryName `
   -GalleryImageDefinitionName $imageDefinitionName `
   -Name $versionName -Location $location `
   -SourceImageId $sourceId `
   -ReplicaCount 2 `
   -StorageAccountType Standard_LRS `
   -PublishingProfileEndOfLifeDate '2020-12-01' `
   -TargetRegion $targetRegion
```

### <a name="create-a-vm"></a>VM oluşturma

Paylaşılan görüntü galerisinden bir sanal makine (VM) oluşturabilir ve diskleri şifrelemek için müşteri tarafından yönetilen anahtarları kullanabilirsiniz. Söz dizimi, bir görüntüden [Genelleştirilmiş](vm-generalized-image-version-powershell.md) veya [özel](vm-specialized-image-version-powershell.md) bir VM oluşturma ile aynıdır. Genişletilmiş parametre kümesini kullanın ve `Set-AzVMOSDisk -Name $($vmName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage` VM yapılandırmasına ekleyin.

Veri diskleri için `-DiskEncryptionSetId $setID` [Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk)kullandığınızda parametresini ekleyin.


## <a name="cli"></a>CLI 

Genel önizleme için öncelikle bu özelliğe kaydolmanız gerekir. Kayıt yaklaşık 30 dakika sürer.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SIGEncryption
```

Özellik kaydının durumunu denetleyin:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SIGEncryption | grep state
```

Bu kod döndüğünde bir `"state": "Registered"` sonraki adıma geçebilirsiniz.

Kaydınızı denetleyin:

```azurecli-interactive
az provider show -n Microsoft.Compute | grep registrationState
```

Kayıtlı değilse, aşağıdaki komutu çalıştırın:

```azurecli-interactive
az provider register -n Microsoft.Compute
```


Bir görüntü sürümüne yönelik bir disk şifreleme kümesi belirtmek için, parametresiyle [az Image Gallery Create-Image-Version](/cli/azure/sig/image-version#az_sig_image_version_create) kullanın `--target-region-encryption` . Biçimi, `--target-region-encryption` işletim sistemi ve veri disklerini şifrelemek için bir virgülle ayrılmış anahtar listesidir. Şöyle görünmelidir: `<encryption set for the OS disk>,<Lun number of the data disk>,<encryption set for the data disk>,<Lun number for the second data disk>,<encryption set for the second data disk>` . 

İşletim sistemi diskinin kaynağı yönetilen bir disk veya VM ise, `--managed-image` görüntü sürümü kaynağını belirtmek için öğesini kullanın. Bu örnekte kaynak, LUN 0 ' da işletim sistemi diskine ve veri diskine sahip olan yönetilen bir görüntüdür. İşletim sistemi diski DiskEncryptionSet1 ile şifrelenir ve veri diski DiskEncryptionSet2 ile şifrelenir.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus \
   --target-regions westus=2=standard_lrs eastus2 \
   --target-region-encryption WestUSDiskEncryptionSet1,0,WestUSDiskEncryptionSet2 EastUS2DiskEncryptionSet1,0,EastUS2DiskEncryptionSet2 \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

İşletim sistemi diskinin kaynağı bir anlık görüntüdür, `--os-snapshot` işletim sistemi diskini belirtmek için kullanın. Aynı zamanda görüntü sürümünün parçası olması gereken veri diski anlık görüntüleri varsa, bunları ekleyin. `--data-snapshot-luns`LUN 'u belirtmek için öğesini kullanın ve `--data-snapshots` anlık görüntüleri belirtmek için öğesini kullanın.

Bu örnekte, kaynaklar disk anlık görüntüleridir. LUN 0 ' da bir işletim sistemi diski ve bir veri diski vardır. İşletim sistemi diski DiskEncryptionSet1 ile şifrelenir ve veri diski DiskEncryptionSet2 ile şifrelenir.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus\
   --target-regions westus=2=standard_lrs eastus\
   --target-region-encryption WestUSDiskEncryptionSet1,0,WestUSDiskEncryptionSet2 EastUS2DiskEncryptionSet1,0,EastUS2DiskEncryptionSet2 \
   --os-snapshot "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myOSSnapshot" \
   --data-snapshot-luns 0 \
   --data-snapshots "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myDDSnapshot" \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage 
   
```

### <a name="create-the-vm"></a>Sanal makineyi oluşturma

Paylaşılan görüntü galerisinden bir VM oluşturabilir ve diskleri şifrelemek için müşterinin yönettiği anahtarları kullanabilirsiniz. Söz dizimi, bir görüntüden [Genelleştirilmiş](vm-generalized-image-version-cli.md) veya [özel](vm-specialized-image-version-cli.md) bir VM oluşturma ile aynıdır. Yalnızca, `--os-disk-encryption-set` şifreleme KÜMESININ kimliğiyle parametresini eklemeniz yeterlidir. Veri diskleri için, `--data-disk-encryption-sets` veri diskleri için disk şifreleme kümelerinin boşlukla ayrılmış bir listesiyle ekleyin.


## <a name="portal"></a>Portal

Portalda görüntü sürümünüzü oluşturduğunuzda, depolama şifreleme kümelerinizi uygulamak için **şifreleme** sekmesini kullanabilirsiniz.

> [!IMPORTANT]
> Çift şifrelemeyi kullanmak için, [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) Azure Portal erişmek için bağlantıyı kullanmanız gerekir. Bu bağlantıyı kullanmadığınız takdirde, Rest 'te Çift şifreleme Şu anda genel Azure portal görünür değildir.


1. **Görüntü sürümü oluştur** sayfasında **şifreleme** sekmesini seçin.
2. **Şifreleme türü**' nde, **müşteri tarafından yönetilen bir anahtarla** veya **platform tarafından yönetilen ve müşteri tarafından yönetilen anahtarlarla çift Şifrelemeli** şifreleme ' yi seçin. 
3. Görüntüdeki her disk için, **disk şifreleme kümesi** açılan listesinden bir şifreleme kümesi seçin. 

### <a name="create-the-vm"></a>Sanal makineyi oluşturma

Bir görüntü sürümünden bir VM oluşturabilir ve diskleri şifrelemek için müşteri tarafından yönetilen anahtarları kullanabilirsiniz. Portalda VM oluştururken, **diskler** sekmesinde, **müşteri tarafından yönetilen anahtarlarla bekleyen şifreleme** veya **şifreleme türü** Için müşteri tarafından yönetilen **anahtarlar ile çift şifreleme** seçeneğini belirleyin. Daha sonra, açılan listeden şifreleme kümesini seçebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

[Sunucu tarafı disk şifrelemesi](./disk-encryption.md)hakkında daha fazla bilgi edinin.

Satın alma planı bilgilerini sağlama hakkında daha fazla bilgi için bkz. [görüntü oluştururken Azure Marketi satın alma planı bilgilerini sağlama](marketplace-images.md).