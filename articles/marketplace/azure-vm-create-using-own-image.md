---
title: Azure Market 'te kendi görüntünüzü kullanarak Azure sanal makine teklifi oluşturma
description: Kendi görüntünüzü kullanarak bir sanal makine teklifini Azure Market 'e yayımlamayı öğrenin.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: krsh
ms.author: krsh
ms.date: 04/16/2021
ms.openlocfilehash: 47fe7b42b68ae42f74a74e5fc69c8d1041d3bf8d
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727133"
---
# <a name="how-to-create-a-virtual-machine-using-your-own-image"></a>Kendi görüntünüzü kullanarak bir sanal makine oluşturma

Bu makalede, Kullanıcı tarafından belirtilen bir sanal makine (VM) görüntüsünün nasıl oluşturulacağı ve dağıtılacağı açıklanır.

> [!NOTE]
> Bu yordama başlamadan önce, sanal sabit disk (VHD) gereksinimleri de dahil olmak üzere Azure VM teklifleri için [teknik gereksinimleri](marketplace-virtual-machines.md#technical-requirements) gözden geçirin.

Bunun yerine onaylanan bir temel görüntü kullanmak için, [onaylanan bir tabandan VM görüntüsü oluşturma](azure-vm-create-using-approved-base.md)' daki yönergeleri izleyin.

## <a name="configure-the-vm"></a>VM'yi yapılandırma

Bu bölümde bir Azure VM 'nin nasıl boyutlandırılacağını, güncelleştirileceğini ve genelleştirirsiniz. Bu adımlar, VM 'nizi Azure Marketi 'nde dağıtılacak şekilde hazırlamak için gereklidir.

### <a name="size-the-vhds"></a>VHD 'leri boyutlandırma

[!INCLUDE [Discussion of VHD sizing](includes/vhd-size.md)]

### <a name="install-the-most-current-updates"></a>En güncel güncelleştirmeleri yükler

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-more-security-checks"></a>Daha fazla güvenlik denetimi gerçekleştirin

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Özel yapılandırma ve zamanlanmış görevler gerçekleştirme

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

### <a name="generalize-the-image"></a>Görüntüyü genelleştirin

Azure Marketi 'ndeki tüm görüntülerin genel bir biçimde yeniden kullanılabilir olması gerekir. Bunu başarmak için, işletim sistemi VHD 'SI, bir VM 'den örneğe özgü tüm tanımlayıcıları ve yazılım sürücülerini kaldıran bir işlem olan genelleştirilmelidir.

## <a name="bring-your-image-into-azure"></a>Görüntünüzü Azure 'a taşıyın

> [!NOTE]
> Imza içeren Azure aboneliğinin, yayımlamak için yayımcı hesabıyla aynı kiracı altında olması gerekir. Ayrıca, yayımcı hesabının SıG içeren aboneliğe en az katkıda bulunan erişimi olmalıdır.

Görüntünüzü Azure 'a getirmenin üç yolu vardır:

1. VHD 'yi paylaşılan bir görüntü galerisine (SıG) yükleyin.
1. VHD 'yi bir Azure depolama hesabına yükleyin.
1. Yönetilen görüntüden (görüntü oluşturma hizmetleri kullanılıyorsa) VHD 'yi ayıklayın.

Aşağıdaki üç bölümde bu seçenekler açıklanır.

### <a name="option-1-upload-the-vhd-as-shared-image-gallery"></a>Seçenek 1: VHD 'YI paylaşılan görüntü Galerisi olarak yükleme

1. VHD 'leri depolama hesabına yükleyin.
2. Azure portal, **özel bir şablon dağıt**' ı arayın.
3. **Düzenleyicide kendi şablonunuzu oluşturun öğesini** seçin.
4. Aşağıdaki Azure Resource Manager (ARM) şablonunu kopyalayın.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "sourceStorageAccountResourceId": {
          "type": "string",
          "metadata": {
            "description": "Resource ID of the source storage account that the blob vhd resides in."
          }
        },
        "sourceBlobUri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "sourceBlobDataDisk0Uri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "sourceBlobDataDisk1Uri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "galleryName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Shared Image Gallery."
          }
        },
        "galleryImageDefinitionName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Image Definition."
          }
        },
        "galleryImageVersionName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Image Version - should follow <MajorVersion>.<MinorVersion>.<Patch>."
          }
        }
      },
      "resources": [
        {
          "type": "Microsoft.Compute/galleries/images/versions",
          "name": "[concat(parameters('galleryName'), '/', parameters('galleryImageDefinitionName'), '/', parameters('galleryImageVersionName'))]",
          "apiVersion": "2020-09-30",
          "location": "[resourceGroup().location]",
          "properties": {
            "storageProfile": {
              "osDiskImage": {
                "source": {
                  "id": "[parameters('sourceStorageAccountResourceId')]",
                  "uri": "[parameters('sourceBlobUri')]"
                }
              },
    
              "dataDiskImages": [
                {
                  "lun": 0,
                  "source": {
                    "id": "[parameters('sourceStorageAccountResourceId')]",
                    "uri": "[parameters('sourceBlobDataDisk0Uri')]"
                  }
                },
                {
                  "lun": 1,
                  "source": {
                    "id": "[parameters('sourceStorageAccountResourceId')]",
                    "uri": "[parameters('sourceBlobDataDisk1Uri')]"
                  }
                }
              ]
            }
          }
        }
      ]
    }
    
    ```

5. Şablonu düzenleyiciye yapıştırın.

    :::image type="content" source="media/create-vm/vm-sample-code-screen.png" alt-text="VM için örnek kod ekranı.":::

1. **Kaydet**’i seçin.
1. Aşağıdaki ekrandaki alanları doldurmak için bu tablodaki parametreleri kullanın.

| Parametreler | Description |
| --- | --- |
| Sourcestorampaaccountresourceıd | Blob VHD 'nin bulunduğu kaynak depolama hesabının kaynak KIMLIĞI.<br><br>Kaynak KIMLIĞINI almak için **Azure Portal** **depolama hesabınıza** gidin, **Özellikler**' e gidin ve **RESOURCEID** değerini kopyalayın. |
| sourceBlobUri | İşletim sistemi diski VHD blobunun blob URI 'Si (belirtilen depolama hesabında olmalıdır).<br><br>Blob URL 'sini almak için **Azure Portal** **depolama hesabınıza** gidin, **bloba** gidin ve **URL** değerini kopyalayın. |
| sourceBlobDataDisk0Uri | Veri diski VHD blobunun blob URI 'Si (belirtilen depolama hesabında olmalıdır). Bir veri diskiniz yoksa, bu parametreyi şablondan kaldırın.<br><br>Blob URL 'sini almak için **Azure Portal** **depolama hesabınıza** gidin, **bloba** gidin ve **URL** değerini kopyalayın. |
| sourceBlobDataDisk1Uri | Ek veri diski VHD blobunun blob URI 'Si (belirtilen depolama hesabında olmalıdır). Ek veri diskiniz yoksa, bu parametreyi şablondan kaldırın.<br><br>Blob URL 'sini almak için **Azure Portal** **depolama hesabınıza** gidin, **bloba** gidin ve **URL** değerini kopyalayın. |
| Gallername | Paylaşılan görüntü galerisinin adı |
| Gallerımagedefinitionname | Görüntü tanımının adı |
| Gallerımageversionname | Oluşturulacak görüntü sürümünün adı (Bu biçimde): `<MajorVersion>.<MinorVersion>.<Patch>` |
|

:::image type="content" source="media/create-vm/custom-deployment-window.png" alt-text="Özel dağıtım penceresini gösterir.":::

8. **Gözden geçir ve oluştur**’u seçin. Doğrulama tamamlandıktan sonra **Oluştur**' u seçin.

> [!TIP]
> Imza görüntüsünü yayımlamak için yayımcı hesabının "Owner" erişimi olmalıdır. Gerekirse, erişim izni vermek için aşağıdaki adımları izleyin:
>
> 1. Paylaşılan görüntü galerisine (SıG) gidin.
> 2. Sol panelde **erişim denetimi** (IAM) seçeneğini belirleyin.
> 3. **Ekle**' yi ve ardından **rol ataması Ekle**' yi seçin.
> 4. **Rol** için **sahip**' i seçin.
> 5. **Erişim atama** için **Kullanıcı, Grup veya hizmet sorumlusu**' nı seçin.
> 6. Görüntüyü yayımlayabilecek kişinin Azure e-postasını girin.
> 7. **Kaydet**’i seçin.<br><br>
> :::image type="content" source="media/create-vm/add-role-assignment.png" alt-text="Rol ataması Ekle penceresi gösterilir.":::

### <a name="option-2-upload-the-vhd-to-a-storage-account"></a>Seçenek 2: VHD 'YI bir depolama hesabına yükleyin

[Azure 'a yüklemek için bir WINDOWS VHD veya vhdx hazırlama](../virtual-machines/windows/prepare-for-upload-vhd-image.md) veya [bir Linux VHD oluşturma ve karşıya yükleme](../virtual-machines/linux/create-upload-generic.md)konusunda açıklandığı gibi, VM 'yi karşıya yüklenecek şekilde yapılandırın ve hazırlayın.

### <a name="option-3-extract-the-vhd-from-managed-image-if-using-image-building-services"></a>Seçenek 3: yönetilen görüntüden VHD 'YI ayıklama (görüntü oluşturma Hizmetleri kullanıyorsanız)

[Packer](https://www.packer.io/)gibi bir görüntü oluşturma hizmeti KULLANıYORSANıZ, VHD 'yi görüntüden ayıklamanız gerekebilir. Bunu yapmanın doğrudan bir yolu yoktur. VM 'yi oluşturmanız ve VHD 'yi VM diskinden ayıklamanız gerekir.

## <a name="create-the-vm-on-the-azure-portal"></a>Azure portal VM 'yi oluşturma

[Azure Portal](https://ms.portal.azure.com/)temel VM görüntüsünü oluşturmak için aşağıdaki adımları izleyin.

1. [Azure portalında](https://ms.portal.azure.com/) oturum açın.
2. **Sanal makineler**'i seçin.
3. **+ Ekle** ' yi seçerek **sanal makine oluştur** ekranını açın.
4. Açılan listeden görüntüyü seçin veya tüm kullanılabilir sanal makine görüntülerini aramak veya taramak için **tüm ortak ve özel görüntülere gözatıp '** yi seçin.
5. **Gen 2** VM oluşturmak için **Gelişmiş** sekmesine gidin ve **Gen 2** seçeneğini belirleyin.

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Gen 1 veya Gen 2 ' yi seçin.":::

6. Dağıtılacak VM 'nin boyutunu seçin.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Seçilen görüntü için önerilen bir VM boyutu seçin.":::

7. VM oluşturmak için gereken diğer ayrıntıları sağlayın.
8. Seçimlerinizi gözden geçirmek için **gözden geçir + oluştur** ' u seçin. **Doğrulama başarılı** Iletisi göründüğünde **Oluştur**' u seçin.

Azure, belirttiğiniz sanal makineyi sağlamaya başlar. Sol menüdeki **sanal makineler** sekmesini seçerek ilerleme durumunu izleyin. Oluşturulduktan sonra sanal makinenin **çalışması** için değişiklikler durumu.

## <a name="connect-to-your-vm"></a>Sanal makinenize bağlanma

[Windows](../virtual-machines/windows/connect-logon.md) veya [Linux](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm) VM 'nize bağlanmak için aşağıdaki belgelere bakın.

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>Sonraki adımlar

- Azure Marketi yayımlama gereksinimlerini karşıladığından emin olmak için [VM görüntünüzü test edin](azure-vm-image-test.md) . Bu isteğe bağlıdır.
- VM görüntünüzü test etmek istemiyorsanız, [Iş Ortağı Merkezi](https://partner.microsoft.com/) ' nde oturum açın ve SIG görüntüsünü yayımlayın (seçenek #1).
- #2 veya #3 seçeneğini izlediyseniz [SAS URI 'Sini oluşturun](azure-vm-get-sas-uri.md).
- Yeni Azure tabanlı VHD 'nizi oluştururken zorluk yaşıyorsanız bkz. [Azure Market Için VM SSS](azure-vm-create-faq.md).
