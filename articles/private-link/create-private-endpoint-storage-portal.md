---
title: Azure özel uç noktasını kullanarak bir depolama hesabına özel olarak bağlanma
description: Özel bir uç nokta kullanarak Azure 'daki bir depolama hesabına özel olarak nasıl bağlanacağınızı öğrenin.
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 8a72f70fbc1ab6052587beb1d949dd73b1ad3559
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376143"
---
# <a name="connect-privately-to-a-storage-account-using-azure-private-endpoint"></a>Azure özel uç noktasını kullanarak bir depolama hesabına özel olarak bağlanma
Azure özel uç noktası, Azure 'da özel bağlantı için temel yapı taşdır. Sanal makineler (VM) gibi Azure kaynaklarının özel bağlantı kaynaklarıyla özel olarak iletişim kurmasına olanak sağlar.

Bu hızlı başlangıçta, bir Azure sanal ağında, Azure portal kullanarak özel uç nokta olan bir depolama hesabı olan bir VM oluşturmayı öğreneceksiniz. Daha sonra, depolama hesabına VM 'den güvenli bir şekilde erişebilirsiniz.


## <a name="sign-in-to-azure"></a>Azure'da oturum açın

https://portal.azure.com adresinden Azure portalında oturum açın.

## <a name="create-a-vm"></a>VM oluşturma
Bu bölümde, özel bağlantı kaynağına (Bu örnekteki bir depolama hesabı) erişmek için kullanılan VM 'yi barındırmak için sanal ağ ve alt ağ oluşturacaksınız.

### <a name="create-the-virtual-network"></a>Sanal ağı oluşturma

Bu bölümde, özel bağlantı kaynağına erişmek için kullanılan VM 'yi barındırmak için sanal ağ ve alt ağ oluşturacaksınız.

1. Ekranın sol üst kısmında, **kaynak oluştur** > **ağ** > **sanal ağ**' ı seçin.
1. **Sanal ağ oluştur**' da bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Adı | *MyVirtualNetwork*girin. |
    | Adres alanı | *10.1.0.0/16*girin. |
    | Abonelik | Aboneliğinizi seçin.|
    | Kaynak grubu | **Yeni oluştur**' u seçin, *myresourcegroup*yazın ve ardından **Tamam**' ı seçin. |
    | Konum | **WestCentralUS**öğesini seçin.|
    | Alt ağ adı | *Mysubnet*yazın. |
    | Alt Ağ - Adres aralığı | *10.1.0.0/24*girin. |
    |||
1. Rest 'i varsayılan olarak bırakın ve **Oluştur**' u seçin.


### <a name="create-virtual-machine"></a>Sanal makine oluşturma

1. Azure portal ekranın sol üst tarafında, **kaynak oluştur** > **işlem** > **sanal makine**' yi seçin.

1. **Sanal makine oluşturma-temel bilgiler**bölümünde, bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | **PROJE AYRıNTıLARı** | |
    | Abonelik | Aboneliğinizi seçin. |
    | Kaynak grubu | **Myresourcegroup**öğesini seçin. Bu, önceki bölümde oluşturdunuz.  |
    | **ÖRNEK AYRıNTıLARı** |  |
    | Sanal makine adı | *Myvm*' i girin. |
    | Bölge | **WestCentralUS**öğesini seçin. |
    | Kullanılabilirlik seçenekleri | Varsayılan **altyapı yedekliliği gerekli değildir**. |
    | Görüntü | **Windows Server 2019 Datacenter**öğesini seçin. |
    | Boyut | Varsayılan **Standart DS1 v2**' i bırakın. |
    | **YÖNETICI HESABı** |  |
    | Kullanıcı adı | Seçmekten bir Kullanıcı adı girin. |
    | Parola | Seçtiğiniz bir parolayı girin. Parola en az 12 karakter uzunluğunda olmalı ve [tanımlanmış karmaşıklık gereksinimlerini](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm) karşılamalıdır.|
    | Parolayı Onayla | Parolayı yeniden girin. |
    | **GELEN BAĞLANTı NOKTASı KURALLARı** |  |
    | Ortak gelen bağlantı noktaları | Varsayılanı **yok**olarak bırakın. |
    | **TASARRUF EDIN** |  |
    | Zaten bir Windows lisansınız var mı? | Varsayılan **Hayır**olarak bırakın. |
    |||

1. **İleri ' yi seçin: diskler**.

1. **Sanal makine oluşturma-diskler**' de, varsayılan değerleri bırakın ve **İleri ' yi seçin: ağ**.

1. **Sanal makine oluşturma-ağ oluşturma**bölümünde şu bilgileri seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Sanal ağ | Varsayılan **MyVirtualNetwork**bırakın.  |
    | Adres alanı | Varsayılan **10.1.0.0/24**' i bırakın.|
    | Alt ağ | Varsayılan **Mysubnet (10.1.0.0/24)** olarak bırakın.|
    | Genel IP | Varsayılan **(yeni) myVm-ip**' i bırakın. |
    | Ortak gelen bağlantı noktaları | **Seçili bağlantı noktalarına Izin ver**' i seçin. |
    | Gelen bağlantı noktalarını seçin | **Http** ve **RDP**' yi seçin.|
    ||

1. **İncele ve oluştur**’u seçin. Azure 'un yapılandırmanızı doğruladığı, **gözden geçir + oluştur** sayfasına götürülürsünüz.

1. **Doğrulama başarılı** Iletisini gördüğünüzde **Oluştur**' u seçin.

## <a name="create-your-private-endpoint"></a>Özel uç noktanızı oluşturma
Bu bölümde, için özel bir uç nokta kullanarak özel bir depolama hesabı oluşturacaksınız. 

1. Azure portal, ekranın sol üst tarafında, **kaynak oluştur** > **depolama** > **depolama hesabı**' nı seçin.

1. **Depolama hesabı oluşturma-temel**bilgiler bölümünde, bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | **PROJE AYRıNTıLARı** | |
    | Abonelik | Aboneliğinizi seçin. |
    | Kaynak grubu | **Myresourcegroup**öğesini seçin. Bu, önceki bölümde oluşturdunuz.|
    | **ÖRNEK AYRıNTıLARı** |  |
    | Depolama hesabı adı  | *Mystorageaccount*değerini girin. Bu ad alındıysanız, benzersiz bir ad oluşturun. |
    | Bölge | **WestCentralUS**öğesini seçin. |
    | Performans| Varsayılan **Standart**bırakın. |
    | Hesap türü | Varsayılan **depolama alanını (genel amaçlı v2)** bırakın. |
    | Çoğaltma | **Okuma Erişimli Coğrafi olarak yedekli depolama (RA-GRS)** seçeneğini belirleyin. |
    |||
  
3. **İleri ' yi seçin: ağ**.
4. **Depolama hesabı oluşturma-ağ**, bağlantı yöntemi ' nde, **Özel uç nokta**' ı seçin.
5. **Depolama hesabı oluştur-ağ**' da, **Özel uç nokta Ekle**' yi seçin. 
6. **Özel uç nokta oluştur**' da bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | **PROJE AYRıNTıLARı** | |
    | Abonelik | Aboneliğinizi seçin. |
    | Kaynak grubu | **Myresourcegroup**öğesini seçin. Bu, önceki bölümde oluşturdunuz.|
    |Konum|**WestCentralUS**öğesini seçin.|
    |Adı| *Myprivateendpoint*girin.  |
    |Depolama alt kaynağı|Varsayılan **blobu**bırakın. |
    | **IŞLEMLERI** |  |
    | Sanal ağ  | *Myresourcegroup*kaynak grubundan *MyVirtualNetwork* öğesini seçin. |
    | Alt ağ |  *Mysubnet*öğesini seçin. |
    | **ÖZEL DNS TÜMLEŞTIRMESI**|  |
    | Özel DNS bölgesiyle tümleştirin  | Varsayılan **Evet**' i bırakın. |
    | Özel DNS bölgesi  | Varsayılan * * (yeni) privatelink.blob.core.windows.net * * ' i bırakın. |
    |||
7. **Tamam**’ı seçin. 
8. **İncele ve oluştur**’u seçin. Azure 'un yapılandırmanızı doğruladığı, **gözden geçir + oluştur** sayfasına götürülürsünüz. 
9. **Doğrulama başarılı** Iletisini gördüğünüzde **Oluştur**' u seçin. 
10. Yeni oluşturduğunuz depolama hesabı kaynağına gidin.
11. Sol içerik menüsünden **erişim tuşları** ' nı seçin.
12. KEY1 için bağlantı dizesinde **Kopyala** ' yı seçin.
 
## <a name="connect-to-a-vm-from-the-internet"></a>İnternet'ten bir sanal makineye bağlanma

Aşağıdaki gibi, internet *'ten gelen VM VM* 'sine bağlanın:

1. Portalın arama çubuğunda *Myvm*' i girin.

1. **Bağlan** düğmesini seçin. **Bağlan** düğmesini seçtikten sonra **sanal makineye bağlan** açılır.

1. **RDP dosyasını indir**' i seçin. Azure bir Uzak Masaüstü Protokolü ( *. rdp*) dosyası oluşturur ve bilgisayarınıza indirir.

1. İndirilen. rdp * dosyasını açın.

    1. İstendiğinde **Bağlan**’ı seçin.

    1. VM oluştururken belirttiğiniz kullanıcı adını ve parolayı girin.

        > [!NOTE]
        > Sanal makineyi oluştururken girdiğiniz kimlik bilgilerini belirtmek için,**farklı bir hesap kullanmak**@no__t **daha fazla seçenek**belirlemeniz gerekebilir.

1. **Tamam**’ı seçin.

1. Oturum açma işlemi sırasında bir sertifika uyarısı alabilirsiniz. Bir sertifika uyarısı alırsanız **Evet** ' i veya **devam et**' i seçin.

1. VM masaüstü seçildikten sonra, bunu yerel masaüstünüze geri dönmek için simge durumuna küçültün.  

## <a name="access-storage-account-privately-from-the-vm"></a>Depolama hesabına özel olarak VM 'den erişin

Bu bölümde, Özel uç nokta kullanarak, depolama hesabına özel olarak bağlanacaksınız.

> [!IMPORTANT]
> Depolama için DNS yapılandırması, belirli bir hesabın FQDN 'sini içermesi için konaklar dosyasında el ile bir değişikliğe ihtiyaç duyuyor. lütfen Windows üzerinde yönetici izinlerini kullanarak şu dosyayı değiştirin: c:\Windows\System32\Drivers\etc\hosts veya Linux/etc/hosts Önceki adımdan hesabın DNS bilgilerini şu biçimde ekleyin [özel IP adresi] myaccount.blob.core.windows.net

1.  *Myvm*uzak masaüstünde PowerShell ' i açın.
2. @ No__t-0 girerek şuna benzer bir ileti alacaksınız:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
    ```
3. [Microsoft Azure Depolama Gezgini](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows)'ni yükleyin.
4. Sağ tıklama ile **depolama hesapları** ' nı seçin.
5. **Azure depolama 'Ya Bağlan**' ı seçin.
6. **Bağlantı dizesi kullan**' ı seçin.
7. **İleri**’yi seçin.
8. Daha önce kopyalanmış bilgileri yapıştırarak bağlantı dizesini girin.
9. **İleri**’yi seçin.
10. **Bağlan**’ı seçin.
11. Mystorageaccount öğesinden blob kapsayıcılarına gözatam 
12. I Klasörler oluşturun ve/veya dosyaları *mystorageaccount*konumuna yükleyin. 
13.  *Myvm*ile uzak masaüstü bağlantısını kapatın. 

Depolama hesabına erişmek için ek seçenekler:
- Microsoft Azure Depolama Gezgini, Microsoft 'un Windows, macOS ve Linux üzerinde Azure Depolama verileriyle görsel olarak çalışmanıza olanak sağlayan tek başına ücretsiz bir uygulamadır. Uygulamayı, özel olarak depolama hesabı içeriğine gözatabilmeniz için yükleyebilirsiniz. 
 
- AzCopy yardımcı programı, Azure depolama için yüksek performanslı komut dosyalı veri aktarımına yönelik başka bir seçenektir. Blob, Dosya ve Tablo depolamaları arasında veri aktarmak için AzCopy kullanın. 


## <a name="clean-up-resources"></a>Kaynakları temizleme 
Özel uç nokta, depolama hesabı ve VM 'yi kullanarak işiniz bittiğinde, kaynak grubunu ve içerdiği tüm kaynakları silin: 
1. Portalın üst kısmındaki **arama** kutusuna *myresourcegroup* girin ve arama sonuçlarından *myresourcegroup* ' i seçin. 
2. **Kaynak grubunu sil**'i seçin. 
3. **Kaynak grubu adını yazmak** Için *myresourcegroup* girin ve **Sil**' i seçin. 

## <a name="next-steps"></a>Sonraki adımlar
Bu hızlı başlangıçta, bir sanal ağ ve depolama hesabı ve özel uç nokta üzerinde bir VM oluşturdunuz. İnternet 'ten bir VM 'ye bağlanırsınız ve özel bağlantı kullanarak depolama hesabına güvenli bir şekilde iletilecaksınız. Özel uç nokta hakkında daha fazla bilgi edinmek için bkz. [Azure özel uç noktası nedir?](private-endpoint-overview.md).
