---
title: Hızlı başlangıç-Azure portal bir Windows sanal makinesi oluşturma
description: Bu hızlı başlangıçta Azure portalı kullanarak Windows sanal makinesi oluşturmayı öğrenirsiniz
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 03/15/2021
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 0ba28d003f359af12de6242c6d2444fb8adab0d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103562764"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-the-azure-portal"></a>Hızlı başlangıç: Azure portalda Windows sanal makinesi oluşturma

Azure sanal makineleri (VM’ler), Azure portalı üzerinden oluşturulabilir. Bu yöntem, sanal makineleri ve tüm ilgili kaynaklarını oluşturmaya yönelik tarayıcı tabanlı bir kullanıcı arabirimi sağlar. Bu hızlı başlangıçta, Azure 'da Windows Server 2019 çalıştıran bir sanal makineyi (VM) dağıtmak için Azure portal nasıl kullanılacağı gösterilmektedir. VM’ye RDP oluşturup IIS web sunucusunu yükleyerek VM’nizin çalıştığını görebilirsiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

https://portal.azure.com adresinden Azure portalında oturum açın.

## <a name="create-virtual-machine"></a>Sanal makine oluşturma

1. Aramada **sanal makineler** yazın.
1. **Hizmetler** altında **sanal makineler**' i seçin.
1. **Sanal makineler** sayfasında, **Ekle** ' yi ve ardından **sanal makine**' yi seçin. 
1. **Temel Bilgiler** sekmesinde, **Proje ayrıntıları** altında, doğru aboneliğin seçildiğinden emin olun, ardından **Yeni oluştur** kaynak grubunu seçin. Ad için *Myresourcegroup* yazın. 

    ![Azure aboneliğini ve sanal makinenin kaynak grubunu nerede seçdiğinin gösterildiği proje ayrıntıları bölümünün ekran görüntüsü](./media/quick-create-portal/project-details.png)

1. **Örnek ayrıntıları** altında, **Sanal makine adı** için *myVM* yazın ve **Bölge** için *Doğu ABD*'yi seçin. **Görüntü** Için *Windows Server 2019 Datacenter* ve **Boyut** için *Standard_DS1_v2* seçin. Diğer varsayılan değerleri bırakın.

    ![Sanal makine için bir ad verdiğiniz örnek ayrıntıları bölümünün ekran görüntüsü ve kendi bölgesini, görüntüsünü ve boyutunu seçebilirsiniz](./media/quick-create-portal/instance-details.png)

1. **Yönetici hesabı** altında, *azureuser* gibi bir kullanıcı adı ve bir parola girin. Parola en az 12 karakter uzunluğunda olmalı ve [tanımlanmış karmaşıklık gereksinimlerini](faq.md#what-are-the-password-requirements-when-creating-a-vm)karşılamalıdır.

    ![Yönetici Kullanıcı adını ve parolasını sağladığınız yönetici hesabı bölümünün ekran görüntüsü](./media/quick-create-portal/administrator-account.png)

1. **Gelen bağlantı noktası kuralları** altında **Seçili bağlantı noktalarına izin ver** ' i seçin ve ardından açılır listeden **RDP (3389)** ve **http (80)** seçeneğini belirleyin.

    ![Gelen bağlantılara izin verilen bağlantı noktalarını seçtiğiniz gelen bağlantı noktası kuralları bölümünün ekran görüntüsü](./media/quick-create-portal/inbound-port-rules.png)

1. Kalan varsayılan ayarları bırakın, ardından sayfanın alt kısmındaki **Gözden geçir + oluştur** düğmesini seçin.

    ![Sayfanın alt kısmındaki Inceleme ve oluşturma düğmesini gösteren ekran görüntüsü](./media/quick-create-portal/review-create.png)

1. Doğrulama çalıştıktan sonra sayfanın alt kısmındaki **Oluştur** düğmesini seçin.

1. Dağıtım tamamlandıktan sonra **Kaynağa Git**' i seçin.

    ![Kaynağa giden sonraki adımı gösteren ekran görüntüsü](./media/quick-create-portal/next-steps.png)

## <a name="connect-to-virtual-machine"></a>Sanal makineye bağlanma

Sanal makine ile bir uzak masaüstü bağlantısı oluşturun. Bu yönergeler VM’nize bir Windows bilgisayarından nasıl bağlanacağınızı gösterir. Mac bilgisayarlarda, Mac App Store’dan bu [Uzak Masaüstü İstemcisi](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) gibi bir RDP istemcisi indirmeniz gerekir.

1. Sanal makinenizin Genel Bakış sayfasında **Bağlan** düğmesine ve ardından **RDP**' yi seçin. 

    ![Bağlan düğmesinin konumunu gösteren sanal makineye Genel Bakış sayfasının ekran görüntüsü](./media/quick-create-portal/portal-quick-start-9.png)
    
2. **RDP Ile Bağlan** sayfasında, bağlantı noktası 3389 ' den IP adresine göre bağlanmak için varsayılan seçenekleri tutun ve **RDP dosyasını indir**' e tıklayın.

2. İndirilen RDP dosyasını açın ve istendiğinde **Bağlan**’a tıklayın. 

3. **Windows Güvenliği** penceresinde **Diğer seçenekler**'i ve ardından **Başka bir hesap kullanın**'ı seçin. Kullanıcı adını **localhost** \\ *Kullanıcı adı* olarak yazın, sanal makine için oluşturduğunuz parolayı girin ve ardından **Tamam**' a tıklayın.

4. Oturum açma işlemi sırasında bir sertifika uyarısı alabilirsiniz. Bağlantıyı oluşturmak için **Evet** veya **Devam**’a tıklayın.

## <a name="install-web-server"></a>Web sunucusunu yükleme

Sanal makinenizin çalıştığını görmek için IIS web sunucusunu yükleyin. VM’de bir PowerShell istemi açın ve şu komutu çalıştırın:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

İşiniz bittiğinde, RDP’nin sanal makine bağlantısını kapatın.


## <a name="view-the-iis-welcome-page"></a>IIS karşılama sayfasını görüntüleme

Portalda, sanal makineyi seçin ve VM 'ye genel bakış bölümünde, **Panoya Kopyala**' yı göstermek için IP adresinin üzerine gelin. IP adresini kopyalayın ve bir tarayıcı sekmesine yapıştırın. Varsayılan IIS karşılama sayfası açılır ve şöyle görünür:

![Tarayıcıda IIS varsayılan sitesinin ekran görüntüsü](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu, sanal makineyi ve tüm ilişkili kaynakları silebilirsiniz. 

Sanal makine için kaynak grubuna gidin ve **kaynak grubunu sil**' i seçin. Kaynakları silmeyi tamamlayacak kaynak grubunun adını onaylayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta basit bir sanal makine dağıttığınız, Web trafiği için bir ağ bağlantı noktası açtık ve temel bir Web sunucusu yüklediniz. Azure sanal makineleri hakkında daha fazla bilgi için Windows VM’lerine yönelik öğreticiye geçin.

> [!div class="nextstepaction"]
> [Azure Windows sanal makine öğreticileri](./tutorial-manage-vm.md)
