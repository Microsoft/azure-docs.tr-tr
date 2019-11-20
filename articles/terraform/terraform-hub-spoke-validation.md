---
title: Öğretici-Terrayform kullanarak Azure 'da bir hub ve bağlı ağ ağını doğrulama
description: Hub ve bağlı bileşen ağ topolojisini birbirine bağlı tüm sanal ağlarla doğrulama öğreticisi.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 9ba4780c59e5e9da4999573abbc08ecd2738a2cd
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159196"
---
# <a name="tutorial-validate-a-hub-and-spoke-network-in-azure-using-terraform"></a>Öğretici: Terrayform kullanarak Azure 'da bir hub ve bağlı ağ ağını doğrulama

Bu makalede, bu serinin önceki makalesinde oluşturulan teraform dosyalarını yürütüteolursunuz. Sonuç, tanıtım sanal ağları arasındaki bağlantının doğrulanmasından kaynaklanır.

Bu öğretici aşağıdaki görevleri kapsar:

> [!div class="checklist"]
> * Hub-ışınsal-uç topolojisinde hub VNet 'i uygulamak için HCL (HashiCorp Language) kullanın
> * Dağıtılacak kaynakları doğrulamak için Terrayform planını kullanma
> * Azure 'da kaynak oluşturmak için Terrayform Uygula ' yı kullanın
> * Farklı ağlar arasındaki bağlantıyı doğrulama
> * Tüm kaynakları yok etmek için Terrayform kullanın

## <a name="prerequisites"></a>Önkoşullar

1. [Azure 'Da Terrayform ile bir hub ve bağlı bileşen karma ağ topolojisi oluşturun](./terraform-hub-spoke-introduction.md).
1. [Azure 'Da Terrayform ile şirket içi sanal ağ oluşturun](./terraform-hub-spoke-on-prem.md).
1. [Azure 'Da Terrayform ile bir hub sanal ağı oluşturun](./terraform-hub-spoke-hub-network.md).
1. [Azure 'Da Terrayform ile bir hub sanal ağ gereci oluşturun](./terraform-hub-spoke-hub-nva.md).
1. [Azure 'Da Terrayform ile bir bağlı bileşen sanal ağı oluşturun](./terraform-hub-spoke-spoke-network.md).

## <a name="verify-your-configuration"></a>Yapılandırmanızı doğrulayın

[Önkoşulları](#prerequisites)tamamladıktan sonra uygun yapılandırma dosyalarının mevcut olduğunu doğrulayın.

1. [Azure portala](https://portal.azure.com) gidin.

1. [Azure Cloud Shell](/azure/cloud-shell/overview)'i açın. Önceden bir ortam seçmediyseniz **Bash** ortamını seçin.

    ![Cloud Shell istemi](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. `clouddrive` dizinine geçin.

    ```bash
    cd clouddrive
    ```

1. Dizinleri yeni dizinle değiştirin:

    ```bash
    cd hub-spoke
    ```

1. Önceki öğreticilerde oluşturulan `.tf` config dosyalarının listelendiğini doğrulamak için `ls` komutunu çalıştırın:

    ![Terrayform demo yapılandırma dosyaları](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-config-files.png)

## <a name="deploy-the-resources"></a>Kaynakları dağıtma

1. Terrayform sağlayıcısını başlatın:
    
    ```bash
    terraform init
    ```
    
    !["Terrayform init" komutunun örnek sonuçları](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-init.png)
    
1. Yürütmeden önce dağıtımın etkisini görmek için `terraform plan` komutunu çalıştırın:

    ```bash
    terraform plan
    ```
    
    !["Terrayform planı" komutunun örnek sonuçları](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-plan.png)

1. Çözümü dağıtın:

    ```bash
    terraform apply
    ```
    
    Dağıtımı onaylamanız istendiğinde `yes` girin.

    !["Terrayform Apply" komutunun örnek sonuçları](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-apply.png)
    
## <a name="test-the-hub-vnet-and-spoke-vnets"></a>Hub VNet ve bağlı bileşen sanal ağlarını test etme

Bu bölümde, sanal şirket içi ortamdan hub VNet 'e bağlantının nasıl test yapılacağı gösterilmektedir.

1. Azure portal **onprez-VNET-RG** kaynak grubuna gidin.

1. **Onpred-VNET-RG** sekmesinde **onpred-VM**adlı VM 'yi seçin.

1. **Bağlan**’ı seçin.

1. **VM yerel hesabını kullanarak metin oturum açma**seçeneğinin yanında, **SSH** komutunu panoya kopyalayın.

1. Bir Linux istemiyle çalıştırılabilen `ssh` simülasyonu yapılmış şirket içi ortama bağlanmak için. `on-prem.tf` parametre dosyasında belirtilen parolayı kullanın.

1. Hub VNet 'teki sıçrama kutusu VM 'sine bağlantıyı sınamak için `ping` komutunu çalıştırın:

   ```bash
   ping 10.0.0.68
   ```

1. Her bir bağlı bileşen içindeki sıçrama kutusu VM 'lerine bağlantıyı sınamak için `ping` komutunu çalıştırın:

   ```bash
   ping 10.1.0.68
   ping 10.2.0.68
   ```

1. **Onpree-VM** sanal makinesindeki SSH oturumundan çıkmak için `exit` girin ve > Enter &lt;tuşuna basın.

## <a name="troubleshoot-vpn-issues"></a>VPN sorunlarını giderme

VPN hatalarını çözme hakkında daha fazla bilgi için bkz. [karma VPN bağlantısıyla Ilgili sorun giderme](/azure/architecture/reference-architectures/hybrid-networking/troubleshoot-vpn).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, öğretici serisinde oluşturulan kaynakları silin.

1. Planda belirtilen kaynakları kaldırın:

    ```bash
    terraform destroy
    ```

    Kaynakların kaldırılmasını onaylamanız istendiğinde `yes` girin.

1. Dizinleri üst dizinle değiştirin:

    ```bash
    cd ..
    ```

1. `hub-scope` dizinini silin (tüm dosyaları dahil):

    ```bash
    rm -r hub-spoke
    ```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"] 
> [Azure 'da Terrayform kullanma hakkında daha fazla bilgi edinin](/azure/terraform)