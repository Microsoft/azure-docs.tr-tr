---
title: Terraform'u Azure ile birlikte kullanma
description: Azure altyapısını sürümüne ve dağıtmaya yönelik Terrayform kullanmaya giriş.
ms.topic: overview
ms.date: 10/26/2019
ms.openlocfilehash: d1754594c651206a0d4d15e659e2926557f9e912
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158995"
---
# <a name="terraform-with-azure"></a>Terraform ve Azure

[Hashicorp Terraform](https://www.terraform.io/), bulut altyapısı sağlamak ve yönetmek için kullanılan bir açık kaynak araçtır. Bulut kaynaklarının topolojisini tanımlayan yapılandırma dosyalarındaki altyapıyı ortak hale büyütür. Bu kaynaklar sanal makineler, depolama hesapları ve ağ arabirimlerini içerir. Terrayform CLı, yapılandırma dosyalarını dağıtmak ve Azure 'a dağıtmak için basit bir mekanizma sağlar.

Bu makalede Azure altyapısını yönetmek için Terraform'u kullanmanın avantajları anlatılmaktadır.

## <a name="automate-infrastructure-management"></a>Altyapı yönetimini otomatikleştirin.

Terraform'un şablon tabanlı yapılandırma dosyaları Azure kaynaklarını yinelenebilir ve tahmin edilebilir bir şekilde tanımlamanızı, sağlamanızı ve yapılandırmanızı sağlar. Altyapıyı otomatikleştirmek birçok avantaja sahiptir:

- Altyapıyı dağıtma ve yönetme aşamalarındaki insan hatası ihtimalini azaltır.
- Birbirinin aynı geliştirme, test ve üretim ortamları oluşturmak için aynı şablonu birçok kez dağıtabilir.
- Geliştirme ve test ortamlarını istek üzerine oluşturarak geliştirme maliyetini azaltır.

## <a name="understand-infrastructure-changes-before-being-applied"></a>Altyapı değişikliklerini uygulanmadan önce anlayın

Kaynak topolojisi araçları daha karmaşık hale geldikçe altyapıda gerçekleştirilen değişikliklerin anlamının ve etkisinin anlaşılması zor olabilir.

Terrayform CLı, kullanıcıların uygulama öncesinde altyapı değişikliklerini doğrulamasını ve önizlemesini yapmasını sağlar. Altyapı değişikliklerinin güvenli bir şekilde önizlenmesi birçok avantaj sağlar:
- Ekip üyeleri önerilen değişiklikleri ve etkisini daha hızlı anlayarak daha verimli bir şekilde çalışabilir.
- İstenmeden yapılan değişiklikler geliştirme sürecinin erken dönemlerinde fark edilebilir

## <a name="deploy-infrastructure-to-multiple-clouds"></a>Altyapı birden fazla buluta dağıtılabilir

Teresform, birden çok bulut sağlayıcısı genelinde bir altyapıyı dağıtmakta bir bölüm. Geliştiricilerin her bir altyapı tanımını yönetmek için tutarlı araçlar kullanmasına olanak sağlar.

## <a name="next-steps"></a>Sonraki adımlar

Terraform ve avantajlarına genel bir bakış elde ettiğinize göre, aşağıdaki önerilen adımlara geçebilirsiniz:

- [Terraform'u yükleyip Azure'u kullanacak şekilde yapılandırarak](/azure/virtual-machines/linux/terraform-install-configure) başlayın.
- [Terraform'u kullanarak bir Azure sanal makinesi oluşturma](/azure/virtual-machines/linux/terraform-create-complete-vm)
- [Terraform için Azure Resource Manager modülünü](https://www.terraform.io/docs/providers/azurerm/) keşfedin 