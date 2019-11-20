---
title: Hızlı başlangıç-Azure portal bir Windows sanal makinesi oluşturma
description: Bu hızlı başlangıçta Azure portalı kullanarak Windows sanal makinesi oluşturmayı öğrenirsiniz
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/05/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 002d374f5be606688121ef4a3952383567c43e85
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685207"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-the-azure-portal"></a>Hızlı başlangıç: Azure portalda Windows sanal makinesi oluşturma

Azure sanal makineleri (VM’ler), Azure portalı üzerinden oluşturulabilir. Bu yöntem, sanal makineleri ve tüm ilgili kaynaklarını oluşturmaya yönelik tarayıcı tabanlı bir kullanıcı arabirimi sağlar. Bu hızlı başlangıçta, Azure 'da Windows Server 2019 çalıştıran bir sanal makineyi (VM) dağıtmak için Azure portal nasıl kullanılacağı gösterilmektedir. VM’ye RDP oluşturup IIS web sunucusunu yükleyerek VM’nizin çalıştığını görebilirsiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

https://portal.azure.com adresinden Azure portalında oturum açın.

## <a name="create-virtual-machine"></a>Sanal makine oluşturma

1. Aramada **sanal makineler** yazın.
1. **Hizmetler**altında **sanal makineler**' i seçin.
1. **Sanal makineler** sayfasında **Ekle**' yi seçin. 
1. **Temel Bilgiler** sekmesinde, **Proje ayrıntıları** altında, doğru aboneliğin seçildiğinden emin olun, ardından **Yeni oluştur** kaynak grubunu seçin. Ad için *Myresourcegroup* yazın. 

    ![VM'niz için yeni bir kaynak grubu oluşturma](./media/quick-create-portal/project-details.png)

1. **Örnek ayrıntıları**' nın altında, **sanal makine adı** için *myvm* yazın ve **bölgeniz**Için *Doğu ABD* seçin ve ardından **görüntü**için *Windows Server 2019 Datacenter* ' u seçin. Diğer varsayılan değerleri bırakın.

    ![Örnek ayrıntıları bölümü](./media/quick-create-portal/instance-details.png)

1. **Yönetici hesabı** altında, *azureuser* gibi bir kullanıcı adı ve bir parola girin. Parola en az 12 karakter uzunluğunda olmalı ve [tanımlanmış karmaşıklık gereksinimlerini](faq.md#what-are-the-password-requirements-when-creating-a-vm) karşılamalıdır.

    ![Kullanıcı adınızı ve parolanızı girin](./media/quick-create-portal/administrator-account.png)

1. **Gelen bağlantı noktası kuralları**altında **Seçili bağlantı noktalarına izin ver** ' i seçin ve ardından açılır listeden **RDP (3389)** ve **http (80)** seçeneğini belirleyin.

    ![RDP ve HTTP için bağlantı noktaları açma](./media/quick-create-portal/inbound-port-rules.png)

1. Kalan varsayılan ayarları bırakın, ardından sayfanın alt kısmındaki **Gözden geçir + oluştur** düğmesini seçin.

    ![Gözden geçirme ve oluşturma](./media/quick-create-portal/review-create.png)


## <a name="connect-to-virtual-machine"></a>Sanal makineye bağlanma

Sanal makine ile bir uzak masaüstü bağlantısı oluşturun. Bu yönergeler VM’nize bir Windows bilgisayarından nasıl bağlanacağınızı gösterir. Mac bilgisayarlarda, Mac App Store’dan bu [Uzak Masaüstü İstemcisi](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) gibi bir RDP istemcisi indirmeniz gerekir.

1. Sanal makinenizin Genel Bakış sayfasında **Bağlan** düğmesine tıklayın. 

    ![Portaldan bir Azure sanal makinesine bağlanma](./media/quick-create-portal/portal-quick-start-9.png)
    
2. **Sanal makineye bağlan** sayfasında, bağlantı noktası 3389 ' den IP adresine göre bağlanmak için varsayılan seçenekleri tutun ve **RDP dosyasını indir**' e tıklayın.

2. İndirilen RDP dosyasını açın ve istendiğinde **Bağlan**’a tıklayın. 

3. **Windows Güvenliği** penceresinde **Diğer seçenekler**'i ve ardından **Başka bir hesap kullanın**'ı seçin. Kullanıcı adını **localhost**\\*username* olarak yazın, sanal makine için oluşturduğunuz parolayı girin ve ardından **Tamam**’a tıklayın.

4. Oturum açma işlemi sırasında bir sertifika uyarısı alabilirsiniz. Bağlantıyı oluşturmak için **Evet** veya **Devam**’a tıklayın.

## <a name="install-web-server"></a>Web sunucusunu yükleme

Sanal makinenizin çalıştığını görmek için IIS web sunucusunu yükleyin. VM’de bir PowerShell istemi açın ve şu komutu çalıştırın:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

İşiniz bittiğinde, RDP’nin sanal makine bağlantısını kapatın.


## <a name="view-the-iis-welcome-page"></a>IIS karşılama sayfasını görüntüleme

Portalda VM 'yi seçin ve VM 'ye Genel Bakış ' da, kopyalamak için **tıklayın** ve sonra da bir tarayıcı sekmesine yapıştırın. Varsayılan IIS karşılama sayfası açılır ve şöyle görünür:

![Varsayılan IIS sitesi](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu, sanal makineyi ve tüm ilişkili kaynakları silebilirsiniz. 

Sanal makine için kaynak grubunu seçin ve **Sil**' i seçin. Kaynakları silmeyi tamamlayacak kaynak grubunun adını onaylayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, basit bir sanal makine dağıttınız, web trafiği için bir ağ bağlantı noktası açtınız ve temel bir web sunucusu yüklediniz. Azure sanal makineleri hakkında daha fazla bilgi için Windows VM’lerine yönelik öğreticiye geçin.

> [!div class="nextstepaction"]
> [Azure Windows sanal makine öğreticileri](./tutorial-manage-vm.md)