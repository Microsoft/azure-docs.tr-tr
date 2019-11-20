---
title: Azure 'da özel uç noktaları yönetme
description: Azure portal kullanarak özel uç nokta oluşturmayı öğrenin
services: private-link
author: KumudD
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: d8d7c0232110d3d5b040debc3a24941988d4ee29
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72372238"
---
# <a name="create-a-private-endpoint-using-azure-portal"></a>Azure portal kullanarak özel uç nokta oluşturma

Özel uç nokta, Azure 'da özel bağlantı için temel yapı taşdır. Sanal makineler (VM) gibi Azure kaynaklarının özel bağlantı kaynaklarıyla özel olarak iletişim kurmasına olanak sağlar. Bu hızlı başlangıçta, Azure portalını kullanarak Azure özel uç noktası olan bir SQL veritabanı sunucusu olan bir Azure sanal ağında bir VM oluşturmayı öğreneceksiniz. Ardından, VM 'den SQL veritabanı sunucusuna güvenle erişebilirsiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.


> [!NOTE]
> Aynı alt ağdaki hizmet uç noktaları ile birlikte özel uç noktalara izin verilmez!

## <a name="sign-in-to-azure"></a>Azure'da oturum açın

https://portal.azure.com adresinden Azure portalında oturum açın.

## <a name="create-a-vm"></a>VM oluşturma
Bu bölümde, özel bağlantı kaynağına erişmek için kullanılan VM 'yi barındırmak için sanal ağ ve alt ağ oluşturacaksınız (Bu örnekteki Azure 'da bir SQL Server).

### <a name="create-the-virtual-network"></a>Sanal ağı oluşturma


Bu bölümde, özel bağlantı kaynağına erişmek için kullanılan VM 'yi barındırmak için bir sanal ağ ve alt ağ oluşturacaksınız.

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


### <a name="create-virtual-machine"></a>Sanal Makine oluşturun

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
    |||


1. **İncele ve oluştur**’u seçin. Azure 'un yapılandırmanızı doğruladığı, **gözden geçir + oluştur** sayfasına götürülürsünüz.

1. **Doğrulama başarılı** Iletisini gördüğünüzde **Oluştur**' u seçin.

## <a name="create-a-sql-database-server"></a>SQL veritabanı sunucusu oluşturma
Bu bölümde, Azure 'da bir SQL veritabanı sunucusu oluşturacaksınız. 

1. Azure portal ekranın sol üst kısmında, **kaynak oluştur** > **veritabanları** > **SQL veritabanı**' nı seçin.

1. **SQL veritabanı oluşturma-temel**bilgiler bölümünde, bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | **Veritabanı ayrıntıları** | |
    | Abonelik | Aboneliğinizi seçin. |
    | Kaynak grubu | **Myresourcegroup**öğesini seçin. Bu, önceki bölümde oluşturdunuz.|
    | **ÖRNEK AYRıNTıLARı** |  |
    | Veritabanı adı  | *MyDatabase*yazın. Bu ad alındıysanız, benzersiz bir ad oluşturun. |
    |||
5. **Sunucu**' da, **Yeni oluştur**' u seçin. 
6. **Yeni sunucu**' da bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    |Sunucu adı  | *Sunucum*girin. Bu ad alındıysanız, benzersiz bir ad oluşturun.|
    | Sunucu yöneticisi oturum açma| Tercih etmek için bir yönetici adı girin. |
    | Parola | Seçtiğiniz bir parolayı girin. Parola en az 8 karakter uzunluğunda olmalı ve tanımlanan gereksinimleri karşılamalıdır. |
    | Konum | SQL Server bulunmasını istediğiniz bir Azure bölgesi seçin. |
    
7. **Tamam**’ı seçin. 
8. **İncele ve oluştur**’u seçin. Azure 'un yapılandırmanızı doğruladığı, **gözden geçir + oluştur** sayfasına götürülürsünüz. 
9. Doğrulama başarılı iletisini gördüğünüzde **Oluştur**' u seçin. 
10. Doğrulama başarılı iletisini gördüğünüzde oluştur ' u seçin. 

## <a name="create-a-private-endpoint"></a>Özel uç nokta oluşturma

Bu bölümde, bir SQL Server oluşturacaksınız ve kendisine özel bir uç nokta ekleyeceksiniz. 

1. Azure portal ekranın sol üst kısmında, **kaynak oluştur** > **ağ** > **özel bağlantı merkezi (Önizleme)** seçeneğini belirleyin.
2. **Özel bağlantı merkezi 'Ne genel bakış**' da, **bir hizmete özel bağlantı oluşturma**seçeneğinde, **Başlat**' ı seçin.
1. **Özel uç nokta oluştur (Önizleme)-temel**bilgiler için, bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | **Proje ayrıntıları** | |
    | Abonelik | Aboneliğinizi seçin. |
    | Kaynak grubu | **Myresourcegroup**öğesini seçin. Bu, önceki bölümde oluşturdunuz.|
    | **ÖRNEK AYRıNTıLARı** |  |
    | Adı | *@No__t-1myPrivateEndpoint*girin. Bu ad alındıysanız, benzersiz bir ad oluşturun. |
    |Bölge|**WestCentralUS**öğesini seçin.|
    |||
5. **Sonraki: kaynak**' ı seçin.
6. **Özel uç nokta oluştur-kaynak**bölümünde bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    |Bağlantı yöntemi  | Dizinimde bir Azure kaynağına bağlan ' ı seçin.|
    | Abonelik| Aboneliğinizi seçin. |
    | Kaynak türü | **Microsoft. SQL/Servers**' ı seçin. |
    | Kaynak |*Sunucum* seçin|
    |Hedef alt kaynak |*SqlServer* seçin|
    |||
7. Ileri 'yi seçin **: yapılandırma**.
8. **Özel uç nokta (Önizleme) oluştur-yapılandırma**' da bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    |**IŞLEMLERI**| |
    | Sanal ağ| *MyVirtualNetwork*öğesini seçin. |
    | Alt ağ |  *Mysubnet*öğesini seçin. |
    |**ÖZEL DNS TÜMLEŞTIRMESI**||
    |Özel DNS bölgesiyle tümleştirin |**Evet**' i seçin. |
    |Özel DNS bölgesi |Seçin *(yeni) Privatelink. Database. Windows. net* |
    |||

1. **İncele ve oluştur**’u seçin. Azure 'un yapılandırmanızı doğruladığı, **gözden geçir + oluştur** sayfasına götürülürsünüz. 
2. **Doğrulama başarılı** Iletisini gördüğünüzde **Oluştur**' u seçin. 
 
## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Uzak Masaüstü (RDP) kullanarak bir VM 'ye bağlanma


**Myvm*oluşturduktan sonra, internet 'ten aşağıdaki gibi bağlanın: 

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

## <a name="access-the-sql-database-server-privately-from-the-vm"></a>SQL veritabanı sunucusuna VM 'den özel olarak erişme

1.  *Myvm*uzak masaüstünde PowerShell ' i açın.

2. @ No__t-0 girin. 

    Şuna benzer bir ileti alacaksınız:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
3. [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)'i yükler.

4. **Sunucuya Bağlan**' da bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Sunucu türü| **Veritabanı Altyapısı**’nı seçin.|
    | Sunucu adı| *Myserver.Database.Windows.net* seçin |
    | Kullanıcı adı | SQL Server oluşturma sırasında belirtilen username@servername olarak Kullanıcı adı girin. |
    |Parola |SQL Server oluşturma sırasında girilen bir parolayı girin. |
    |Parolayı anımsa|**Evet**' i seçin.|
    |||
1. **Bağlan**’ı seçin.
2. Sol menüden veritabanlarına gözatamazsınız.
3. I MyDatabase 'ten bilgi oluşturun veya sorgulayın.
4.  *Myvm*ile uzak masaüstü bağlantısını kapatın. 

## <a name="clean-up-resources"></a>Kaynakları temizleme 
Özel uç nokta, SQL Server ve VM 'yi kullanarak işiniz bittiğinde, kaynak grubunu ve içerdiği tüm kaynakları silin: 
1. Portalın üst kısmındaki **arama** kutusuna *myresourcegroup* girin ve arama sonuçlarından *myresourcegroup* ' i seçin. 
2. **Kaynak grubunu sil**'i seçin. 
3. **Kaynak grubu adını yazın** ve **Sil**' i seçmek için myresourcegroup girin.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir sanal ağ, bir SQL veritabanı sunucusu ve özel erişim için özel bir uç nokta üzerinde bir VM oluşturdunuz. İnternet 'ten bir VM 'ye bağlanırsınız ve özel bağlantı kullanarak SQL veritabanı sunucusuna güvenli bir şekilde iletilecaksınız. Özel uç noktalar hakkında daha fazla bilgi edinmek için bkz. [Azure özel uç noktası nedir?](private-endpoint-overview.md).

