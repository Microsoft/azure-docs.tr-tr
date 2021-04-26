---
title: Portal şablonunu şablon belirtimine Dönüştür
description: Azure portal galerisinde varolan bir şablonun şablon özelliklerine nasıl dönüştürüleceğini açıklar.
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: c59275fca1eb3037b48b7293fc9e507df46b7fcb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99555944"
---
# <a name="convert-template-gallery-in-portal-to-template-specs"></a>Portaldaki şablon galerisini şablon özelliklerine Dönüştür

Azure portal, hesabınızda Azure Resource Manager şablonları (ARM şablonları) depolamak için bir yol sağlar. Ancak, [Şablon Özellikleri](template-specs.md) , şablonlarınızın kuruluşunuzdaki kullanıcılarla paylaşılmasını ve diğer şablonlarla bağlantı için daha kolay bir yol sunar. Bu makalede, şablon galerisindeki var olan şablonların şablon özelliklerine nasıl dönüştürüleceği gösterilmektedir.

Dönüştürülecek şablonunuz olup olmadığını görmek için [portalda şablon galerisini](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Gallery%2Fmyareas%2Fgalleryitems)görüntüleyin. Bu şablonlar kaynak türüne sahiptir `Microsoft.Gallery/myareas/galleryitems` .

## <a name="convert-with-powershell-script"></a>PowerShell betiği ile Dönüştür

Şablon galerisinde şablonları dönüştürmeyi basitleştirmek için, Azure hızlı başlangıç şablonları deposundan bir PowerShell betiği kullanın. Betiği çalıştırdığınızda her şablon için yeni bir şablon belirtimi oluşturabilir veya şablon belirtimini oluşturan bir şablonu indirebilirsiniz. Betik şablonu şablon galerisinden silmez.

1. [Geçiş betiğini](https://github.com/Azure/azure-quickstart-templates/blob/master/201-templatespec-migrate-create/Migrate-GalleryItems.ps1)kopyalayın. *Migrate-GalleryItems.ps1* adlı yerel bir kopyasını kaydedin.
1. Yeni şablon özellikleri oluşturmak için, ve parametreleri için değerler `-ResourceGroupName` sağlayın `-Location` . 

   `ItemsToExport` `MyGalleryItems` Şablonlarınızı dışarı aktarmak için olarak ayarlayın. `AllGalleryItems`Erişiminiz olan tüm şablonları dışarı aktarmak için olarak ayarlayın.

   Aşağıdaki örnek, **migratedRG** adlı bir kaynak grubundaki her bir şablon için yeni şablon özellikleri oluşturur. Bu komut dosyası, mevcut değilse kaynak grubunu oluşturur.

   ```azurepowershell
   .\Migrate-GalleryItems.ps1 -ResourceGroupName migratedRG -Location westus2 -ItemsToExport MyGalleryItems
   ```

1. Şablon özelliklerini oluşturmak için kullanabileceğiniz şablonları indirmek için kaynak grubunun veya konumun değerlerini sağlayın. Bunun yerine, belirtin `-ExportToFile` . Şablon, galerisindeki şablonunuz ile aynı değildir. Bunun yerine, şablonunuz için şablon belirtimini oluşturan bir şablon belirtimi kaynağı içerir.

   Aşağıdaki örnek, şablon özellikleri oluşturmadan şablonları indirir.

   ```azurepowershell
   .\Migrate-GalleryItems.ps1 -ItemsToExport MyGalleryItems -ExportToFile
   ```

   Şablon belirtimini oluşturan şablonu dağıtmayı öğrenmek için bkz. [hızlı başlangıç: Create and Deploy Template spec (Önizleme)](quickstart-create-template-specs.md).

Betiği ve parametreleri hakkında daha fazla bilgi için bkz. [Şablon Galerisi şablonlarından Templatespec oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/201-templatespec-migrate-create).

## <a name="manually-convert-through-portal"></a>Portala el ile Dönüştür

Şablonları Galeriden el ile yeni bir şablon özelliklerine kopyalayabilirsiniz.

1. Portalda [Şablonlar (Önizleme)](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Gallery%2Fmyareas%2Fgalleryitems) öğesini açın.
1. Geçirilecek şablonu seçin.
1. **Şablonu görüntüle**' yi seçin.
1. Şablon içeriğini kopyalayın.
1. Portal arama çubuğunda, **Şablon Özellikleri**' ni arayın. Bu seçeneği belirleyin.
1. **Şablon Oluştur belirtimini** seçin.
1. Ad, abonelik, kaynak grubu, konum ve sürüm için değerler sağlayın.
1. **İleri ' yi seçin: şablonu Düzenle**.
1. Şablonun içeriği için, şablon galerisinden kopyaladığınız şablonu yapıştırın.
1. **Gözden geçir + Oluştur**’u seçin.
1. Doğrulama başarıyla tamamlandıktan sonra **Oluştur**' u seçin.

Şablon belirtimini kuruluşunuzdaki diğer kullanıcılarla paylaşmanız gerekiyorsa, [rol tabanlı erişim denetimini](../../role-based-access-control/tutorial-role-assignments-group-powershell.md) erişim gerektiren Grup veya kullanıcılara ayarlayın.

## <a name="next-steps"></a>Sonraki adımlar

Şablon özellikleri hakkında daha fazla bilgi edinmek için bkz. [şablon özellikleri oluşturma ve dağıtma](template-specs.md).
