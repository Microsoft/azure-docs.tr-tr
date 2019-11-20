---
title: Öğretici-Terrayform kullanarak Azure Marketi görüntüsünden yönetilen kimliğe sahip bir Linux VM oluşturma
description: Azure Marketi görüntüsünü kullanarak yönetilen kimlik ve uzak durum yönetimi ile Teraform Linux VM oluşturma
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 6bc6bec2a13dcd5747823de739d1dd11c6027091
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158981"
---
# <a name="tutorial-create-a-linux-vm-with-a-managed-identity-from-the-azure-marketplace-image-using-terraform"></a>Öğretici: Terrayform kullanarak Azure Marketi görüntüsünden yönetilen kimliğe sahip bir Linux VM oluşturma

Bu makalede bir [Terraform Market görüntüsünü](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview) kullanarak [Azure kaynakları için yönetilen kimlikler](https://www.terraform.io/intro/index.html) ile yüklenmiş ve yapılandırılmış en son [Terraform](/azure/active-directory/managed-service-identity/overview) sürümüne sahip bir Ubuntu Linux VM (16.04 LTS) oluşturmayı öğreneceksiniz. Bu görüntü ayrıca Terraform ile [uzak durum](https://www.terraform.io/docs/state/remote.html) yönetimi sağlamak için bir uzak arka uç da yapılandıracaktır. 

Terraform Market görüntüsü, Terraform'u el ile yükleme ve yapılandırmanıza gerek kalmadan Terraform'u Azure'da kullanmaya başlamanızı kolaylaştırır. 

Bu Terraform VM görüntüsü için yazılım ücreti alınmaz. Sağlanan VM 'nin boyutuna bağlı olarak yalnızca Azure donanım kullanımı ücretleri ödersiniz. 

İşlem ücretleri hakkında daha fazla bilgi için bkz. [LINUX VM fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="prerequisites"></a>Önkoşullar
Bir Linux Terrayform VM oluşturabilmeniz için önce bir Azure aboneliğinizin olması gerekir. Henüz yoksa [ücretsiz Azure hesabınızı hemen oluşturun](https://azure.microsoft.com/free/).  

## <a name="create-your-terraform-vm"></a>Terrayform VM 'nizi oluşturma 

Linux Terrayform VM 'sinin bir örneğini oluşturma adımları aşağıda verilmiştir: 

1. Azure portalda [Kaynak oluştur](https://ms.portal.azure.com/#create/hub) bölümüne gidin.

1. **Market içinde ara** arama çubuğundan **Terraform** araması yapın. 

1. **Oluştur**' u seçin. 

1. Aşağıdaki bölümlerde, Praüform Linux VM 'sini oluşturmak için sihirbazdaki adımların her biri için girişler sağlanmaktadır. Aşağıdaki bölümde bu adımları yapılandırmak için gerekli olan değerler belirtilmiştir.

## <a name="details-on-the-create-terraform-tab"></a>Terraform Oluştur sekmesindeki ayrıntılar

**Terraform Oluştur** sekmesine aşağıdaki bilgileri girin:

1. **Temel Bilgiler**
    
   * **Ad**: Terrayform sanal makinenizin adı.
   * **Kullanıcı Adı**: İlk hesabın oturum açma kimliği.
   * **Parola**: İlk hesabın parolası. (Parola yerine SSH ortak anahtarı kullanabilirsiniz.)
   * **Abonelik**: Makinenin oluşturulacağı ve fatura için kullanacağı abonelik. Bu abonelikte kaynak oluşturma ayrıcalıklarına sahip olmanız gerekir.
   * **Kaynak grubu**: Yeni veya var olan bir kaynak grubu.
   * **Konum**: Size en uygun veri merkezi. Bu genellikle verilerinizin çoğunun bulunduğu veya en hızlı ağ erişimi için fiziksel konumunuza en yakın olan veri merkezidir.

2. **Ek ayarlar**

   * **Boyut**: VM 'nin boyutu. 
   * **VM disk türü**: SSD veya HDD.

3. **Terraform Özeti**

   * Girdiğiniz tüm bilgilerin doğru olduğunu onaylayın. 

4. **Satın Al**

   * Sağlama işlemini başlatmak için **Satın Al**'ı seçin. İşlemin koşullarının bağlantısı sunulur. Sanal makinede, boyut adımında seçtiğiniz sunucu boyutu için işlem dışında ek ücret yoktur.

Terrayform VM görüntüsü aşağıdaki adımları yapar:

* Ubuntu 16.04 LTS görüntüsünü temel alan ve sistem tarafından atanmış olan kimliğe sahip olan bir VM oluşturur.
* Azure kaynakları için OAuth belirteçlerinin kullanılmasına izin vermek üzere sanal makinede Azure kaynakları uzantısı için Yönetilen kimlikler ' i yükleme.
* Yönetilen kimliğe RBAC izinleri atayarak kaynak grubunda sahip hakları verir.
* Bir Terraform şablon klasörü (tfTemplate) oluşturur.
* Bir Terraform uzak durumu için Azure arka ucuyla ön yapılandırma gerçekleştirir.

## <a name="access-and-configure-a-linux-terraform-vm"></a>Linux Terrayform VM 'sine erişme ve yapılandırma

VM 'yi oluşturduktan sonra aşağıdaki adımları uygulayın:

1. SSH kullanarak VM 'de oturum açın. Önceki bölümde oluşturduğunuz hesap kimlik bilgilerini kullanın. Windows'da [Putty](https://www.putty.org/) gibi bir SSH istemcisi aracı indirebilirsiniz.

1. VM 'deki Azure kaynakları için yönetilen kimliklerin tüm aboneliğine katkıda bulunan izinleri verin. 

    Katkıda bulunan izni, VM 'deki Azure kaynaklarına yönelik yönetilen kimliklerin VM kaynak grubu dışında kaynak oluşturmak için Terrayform kullanmasına yardımcı olur. Aşağıdaki betiği çalıştırarak bu eylemi yapın: 
    
    ```bash
    . ~/tfEnv.sh
    ```

    Bu betik, Azure 'da kimlik doğrulaması yapmak için [Azure CLI etkileşimli oturum açma](/cli/azure/authenticate-azure-cli?view=azure-cli-latest#sign-in-interactively) mekanizmasını kullanır. Bu işlem, tüm abonelik için [yönetilen kimlik katılımcısı iznini](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) atar. 

1. VM, Terraform uzak durum arka ucuna sahiptir. Bu uygulamayı teraform dağıtımınızda etkinleştirmek için, `remoteState.tf` dosyasını terrampascripts 'in köküne kopyalamanız gerekir.

    ```bash
    cp  ~/tfTemplate/remoteState.tf .
    ```

    Uzak durum yönetimi hakkında daha fazla bilgi için bkz. [Terrayform uzak durumu](https://www.terraform.io/docs/state/remote.html). Depolama erişim anahtarı bu dosyada kullanıma sunulur. Kaynak denetimine Terlarform yapılandırma dosyalarını uygulamadan önce bu uygulamayı dışlayın.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"] 
> [Azure 'da Terrayform kullanma hakkında daha fazla bilgi edinin](/azure/terraform)