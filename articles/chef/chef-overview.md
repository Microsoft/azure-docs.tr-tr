---
title: Azure ile Chef kullanma
description: Azure altyapınızı yapılandırmak ve test etmek için Chef kullanmaya giriş
keywords: Azure, Chef, DevOps, sanal makineler, genel bakış, Otomatikleştir
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 4ad8b79b42c9d8d7942f391223c052f63579b11b
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158012"
---
# <a name="using-chef-with-azure"></a>Azure ile Chef kullanma
[Chef](https://www.chef.io) , Azure 'daki sanal makine altyapısını koda dönüştüren güçlü bir Otomasyon platformudur. Chef, altyapının, boyutu ne kadar bir şekilde yapılandırıldığını, dağıtıldığını ve yönetildiğini otomatik hale getirir.

Bu makalede, Azure altyapısını yönetmek için Chef kullanmanın avantajları açıklanmaktadır.

## <a name="chef-extension-on-azure"></a>Azure 'da Chef uzantısı
Azure portalında [Chef uzantılı](https://docs.microsoft.com/azure/chef/chef-extension-portal) bir arka plan hizmeti olarak çalışan Chef istemcisiyle bir sanal makine sağlayın. Bu sanal makineler, sağlandıktan sonra bir Chef sunucusu tarafından yönetilmeye hazır hale gelir.

## <a name="chef-cloud-shell"></a>Chef Cloud Shell
Chef Workstation 'ı doğrudan Azure Cloud Shell ' de kullanın! Cloud Shell tüm Chef yardımcı programlarını ve InSpec hakkını çalıştırın. Chef komutlarının arasından yararlanabilirsiniz:

* [Chef](https://docs.chef.io/ctl_chef.html)
* [ğa](https://docs.chef.io/ctl_kitchen.html)
* [inspec](https://www.inspec.io/docs/reference/cli/)
* [bı](https://docs.chef.io/knife.html)
* [Tanıtım stili](https://docs.chef.io/cookstyle.html)
* [kandcritik](https://docs.chef.io/foodcritic.html)
* [Chef-Çalıştır](https://www.chef.sh/docs/chef-workstation/getting-started/)

`git`, `az-cli`ve `terraform`gibi Cloud Shell bulunan diğer araçlarla komut yardımcı programlarını birleştirin ve altyapı ve uyumluluk Otomasyonunuzu tarayıcıdan yazın.

## <a name="automate-infrastructure-apps-and-compliance-with-one-platform"></a>Altyapıyı, uygulamaları ve bir platformla uyumluluğu otomatikleştirin
Şirketler, dijital market 'te yarışmaya yönelik hız, hız ve güvenlik gerektirir. Chef ve Microsoft Yardım bireyleri, takımları ve kuruluşları bu işlemleri gerçekleştirebilir. Tek platformlu Chef sayesinde, artık altyapınızı, uygulamalarınızı ve uyumluluğunu Microsoft 'a göre otomatik hale getirebilir ve sürekli teslim edebilirsiniz.

## <a name="test-drive-chef-automate-on-azure"></a>Azure üzerinde sürücü Chef Otomasyonu test etme
Chef tarafından desteklenen [Chef, Azure Marketi çözümünü otomatikleştirin](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/chef-software.chef-automate) , altyapı ve uygulamalarınızı işbirliği yaparak oluşturmanıza, dağıtmanıza ve yönetmenize olanak sağlar. Tek tıklamada Chef otomatikleştirmesine dahil olan tüm ticari özelliklere anında erişebilirsiniz; Tüm Fleet genelinde uçtan uca görünürlük elde edin, sürekli uyumluluğu etkinleştirin ve Birleşik bir iş akışıyla tüm değişikliği yönetin.

## <a name="next-steps"></a>Sonraki adımlar

* [Chef kullanarak Azure 'da Windows sanal makinesi oluşturma](/azure/virtual-machines/windows/chef-automation)
