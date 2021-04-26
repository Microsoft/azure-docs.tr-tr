---
title: "Hızlı başlangıç: Azure 'da Linux SQL Server VM oluşturma"
description: Bu öğreticide Azure portalında Linux SQL Server 2017 sanal makinesi oluşturma adımları gösterilmiştir.
services: virtual-machines-sql
author: MashaMSFT
ms.date: 10/22/2019
tags: azure-service-management
ms.topic: quickstart
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 492d4a799f35d7fee4f500b48cbb31d2a9c5957f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450013"
---
# <a name="provision-a-linux-virtual-machine-running-sql-server-in-the-azure-portal"></a>Azure portal SQL Server çalıştıran bir Linux sanal makinesi sağlayın
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Linux](sql-vm-create-portal-quickstart.md)
> * [Windows](../windows/sql-vm-create-portal-quickstart.md)

Bu hızlı başlangıç öğreticisinde, SQL Server 2017 yüklü bir Linux sanal makinesi oluşturmak için Azure portal kullanırsınız. Şunları öğrenirsiniz: 


* [Galeriden SQL Server çalıştıran bir Linux VM oluşturma](#create)
* [ssh ile yeni VM'ye bağlanma](#connect)
* [SA parolasını değiştirme](#password)
* [Uzak bağlantılar için yapılandırma](#remote)

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free) oluşturun.

## <a name="create-a-linux-vm-with-sql-server-installed"></a><a id="create"></a> SQL Server yüklü bir Linux VM oluşturma

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. Soldaki bölmede **Kaynak oluştur**'u seçin.

1. **Kaynak oluştur** bölmesinde **işlem**' ı seçin.

1. **Öne çıkan** başlığın yanındaki **Tümünü göster** ' i seçin.

   ![Tüm VM görüntülerini inceleme](./media/sql-vm-create-portal-quickstart/azure-compute-blade.png)

1. Arama kutusuna **SQL Server 2019** yazın ve aramayı başlatmak için **ENTER** ' u seçin.

1. Arama sonuçlarını, **işletim sistemi**  >  **RedHat**' i seçerek sınırlayın.

    ![SQL Server 2019 VM görüntüleri için arama filtresi](./media/sql-vm-create-portal-quickstart/searchfilter.png)

1. Arama sonuçlarından bir SQL Server 2019 Linux görüntüsü seçin. Bu öğretici **RHEL74 üzerinde 2019 SQL Server** kullanır.

   > [!TIP]
   > Geliştirici sürümü, Enterprise Edition özellikleriyle test veya geliştirme yapmanızı sağlar, ancak SQL Server lisanslama maliyetlerine sahip olmaz. Yalnızca Linux VM çalıştırma maliyetleri için ödeme yaparsınız.

1. **Oluştur**’u seçin. 


### <a name="set-up-your-linux-vm"></a>Linux VM 'nizi ayarlama

1. **Temel bilgiler** sekmesinde, **aboneliğinizi** ve **kaynak grubunuzu** seçin. 

    ![Temel bilgiler penceresi](./media/sql-vm-create-portal-quickstart/basics.png)

1. **Sanal makine adı** alanına yenı Linux sanal makineniz için bir ad girin.
1. Ardından, aşağıdaki değerleri yazın veya seçin:
   * **Bölge**: sizin Için doğru Azure bölgesini seçin.
   * **Kullanılabilirlik seçenekleri**: Uygulamalarınız ve verileriniz için en iyi kullanılabilirlik ve artıklık seçeneğini belirleyin.
   * **Boyutu Değiştir**: bir makine boyutu seçmek için bu seçeneği belirleyin ve Işiniz bittiğinde **Seç**' i seçin. VM makine boyutları hakkında daha fazla bilgi için bkz. [VM boyutları](../../../virtual-machines/sizes.md).

     ![VM boyutu seçme](./media/sql-vm-create-portal-quickstart/vmsizes.png)

   > [!TIP]
   > Geliştirme ve işlevsel test için, **DS2** veya ÜZERI bir VM boyutu kullanın. Performans testi için **DS13** veya üzeri kullanın.

   * **Kimlik doğrulama türü**: **SSH ortak anahtarını** seçin.

     > [!Note]
     > Kimlik doğrulaması için SSH ortak anahtarı veya Parola kullanabilirsiniz. SSH daha güvenlidir. SSH anahtarı oluşturma talimatları için bkz. [Azure'daki Linux VM için Linux ve Mac üzerinde SSH anahtarı oluşturma](../../../virtual-machines/linux/mac-create-ssh-keys.md).

   * **Kullanıcı adı**: VM için yönetici adını girin.
   * **SSH ortak anahtarı**: RSA ortak anahtarınızı girin.
   * **Ortak gelen bağlantı noktaları**: **Seçili bağlantı noktalarına izin ver** ' i seçin ve **ortak gelen bağlantı noktalarını Seç** listesinde **SSH (22)** bağlantı noktasını seçin. Bu hızlı başlangıçta, SQL Server yapılandırmasını bağlamak ve gerçekleştirmek için bu adım gereklidir. SQL Server uzaktan bağlanmak istiyorsanız, sanal makine oluşturulduktan sonra Internet üzerinden bağlantılar için Microsoft SQL Server tarafından kullanılan varsayılan bağlantı noktasına (1433) giden trafiğe el ile izin vermeniz gerekir.

     ![Gelen bağlantı noktaları](./media/sql-vm-create-portal-quickstart/port-settings.png)

1. Aşağıdaki ek sekmelerdeki ayarlarda istediğiniz değişiklikleri yapın veya varsayılan ayarları koruyun.
    * **Diskler**
    * **Ağ**
    * **Yönetim**
    * **Konuk yapılandırması**
    * **Etiketler**

1. **Gözden geçir ve oluştur**’u seçin.
1. **Gözden geçir + oluştur** bölmesinde **Oluştur**' u seçin.

## <a name="connect-to-the-linux-vm"></a><a id="connect"></a> Linux VM'ye bağlanma

BASH kabuğu kullanıyorsanız **ssh** komutuyla Azure VM'ye bağlanabilirsiniz. Aşağıdaki komutta yer alan VM kullanıcı adı ve IP adresini Linux VM bilgileriyle değiştirin.

```bash
ssh azureadmin@40.55.55.555
```

VM'nizin IP adresini Azure portalında bulabilirsiniz.

![Azure portalında IP adresi](./media/sql-vm-create-portal-quickstart/vmproperties.png)

Windows üzerinde çalıştırıyorsanız ve BASH kabuğu yoksa, PuTTY gibi bir SSH istemcisi yükleyebilirsiniz.

1. [PuTTY'yi indirin ve yükleyin](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. PuTTY'yi çalıştırın.

1. PuTTY yapılandırma ekranına VM'nizin genel IP adresini girin.

1. **Aç** ' ı seçin ve istemlerin Kullanıcı adınızı ve parolanızı girin.

Linux VM'lerinize bağlanma hakkında daha fazla bilgi için bkz. [Portal kullanarak Azure’da bir Linux VM oluşturma](../../../virtual-machines/linux/quick-create-portal.md).

> [!NOTE]
> Sunucunun ana bilgisayar anahtarı hakkında kayıt defterinde önbelleğe alınmamış bir PuTTY güvenlik uyarısı görürseniz, aşağıdaki seçeneklerden birini belirleyin. Bu konağa güveniyorsanız, anahtarı PuTTy 'in önbelleğine eklemek ve bağlanmaya devam etmek için **Evet** ' i seçin. Yalnızca bir kez bağlanmak istiyorsanız, anahtarı önbelleğe eklemeden, **Hayır**' ı seçin. Bu konağa güvenmiyorsanız bağlantıyı bırakmak için **iptal** ' i seçin.

## <a name="change-the-sa-password"></a><a id="password"></a> SA parolasını değiştirme

Yeni sanal makine SQL Server'ı rastgele bir SA parolasıyla yükler. SA oturum açmayla SQL Server bağlanmadan önce bu parolayı sıfırlayın.

1. Linux VM'nize bağlandıktan sonra yeni bir komut terminali açın.

1. Aşağıdaki komutları kullanarak SA parolasını değiştirin:

   ```bash
   sudo systemctl stop mssql-server
   sudo /opt/mssql/bin/mssql-conf set-sa-password
   ```

   Sorulduğunda yeni bir SA parolası girin ve tekrar girerek onaylayın.

1. SQL Server hizmetini yeniden başlatın.

   ```bash
   sudo systemctl start mssql-server
   ```

## <a name="add-the-tools-to-your-path-optional"></a>Yolunuza araçları ekleme (isteğe bağlı)

Varsayılan olarak SQL Server komut satırı araçları paketi dahil olmak üzere birkaç SQL Server [paketi](sql-server-on-linux-vm-what-is-iaas-overview.md#packages) yüklenmiştir. Araçlar paketi **sqlcmd** ve **bcp** araçlarını içerir. Kolaylık sağlaması amacıyla araçlar yolu olan `/opt/mssql-tools/bin/` girişini **PATH** ortam değişkeninize ekleyebilirsiniz.

1. **PATH** ortam değişkenini hem oturum açma bilgileriyle başlatılan oturumları hem de etkileşimli/oturum açma bilgisi olmadan başlatılan oturumları için değiştirmek üzere aşağıdaki komutları çalıştırın:

   ```bash
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
   source ~/.bashrc
   ```

## <a name="configure-for-remote-connections"></a><a id="remote"></a> Uzak bağlantılar için yapılandırma gerçekleştirme

Azure VM üzerindeki SQL Server'a uzaktan bağlanmanız gerekirse ağ güvenlik grubu üzerinde bir gelen kuralı yapılandırmanız gerekir. Kural SQL Server'ın dinlediği bağlantı noktasından (varsayılan olarak 1433) gelen trafiğe izin verir. Aşağıdaki adımlar, bu işlemi Azure portalından nasıl yapacağınızı göstermektedir.

> [!TIP]
> Sağlama sırasında ayarlarda **MS SQL (1433)** gelen bağlantı noktasını seçtiyseniz, bu değişiklikler sizin için yapılmış olur. Güvenlik duvarını yapılandırmayla ilgili sonraki bölme geçebilirsiniz.

1. Portalda **Sanal makineler**'i ve ardından SQL Server VM'nizi seçin.
1. Sol gezinti bölmesindeki **Ayarlar** altında **ağ**' ı seçin.
1. Ağ penceresinde gelen **bağlantı noktası kuralları** altında **gelen bağlantı noktası Ekle** ' yi seçin.

   ![Gelen bağlantı noktası kuralları](./media/sql-vm-create-portal-quickstart/networking.png)

1. **Hizmet** listesinde **MS SQL** girişini seçin.

    ![MS SQL güvenlik grubu kuralı](./media/sql-vm-create-portal-quickstart/sqlnsgrule.png)

1. VM kuralını kaydetmek için **Tamam**'a tıklayın.

### <a name="open-the-firewall-on-rhel"></a>RHEL güvenlik duvarını açma

Bu öğreticide nasıl Red Hat Enterprise Linux (RHEL) VM oluşturacağınız gösterilmiştir. RHEL VM'lerine uzaktan bağlanmak isterseniz Linux güvenlik duvarı üzerindeki 1433 numaralı bağlantı noktasını da açmanız gerekir.

1. RHEL VM'nize [bağlanın](#connect).

1. BASH kabuğunda aşağıdaki komutları çalıştırın:

   ```bash
   sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
   sudo firewall-cmd --reload
   ```

## <a name="next-steps"></a>Sonraki adımlar

Azure'da bir SQL Server 2017 sanal makinesi oluşturdunuz. Artık **sqlcmd** ile yerel olarak bağlanıp Transact-SQL sorguları çalıştırabilirsiniz.

Azure VM 'yi uzak SQL Server bağlantıları için yapılandırdıysanız, uzaktan bağlanabilmelisiniz. Windows'dan Linux üzerindeki SQL Server'a uzaktan bağlantı kurma örneği için bkz. [Linux üzerindeki SQL Server'a bağlanmak için Windows üzerinde SSMS kullanma](/sql/linux/sql-server-linux-develop-use-ssms). Visual Studio Code bağlantısı kurmak için bkz. [SQL Server için Transact-SQL betikleri oluşturma ve çalıştırma amacıyla Visual Studio Code'u kullanma](/sql/linux/sql-server-linux-develop-use-vscode)

Linux üzerinde SQL Server hakkında daha fazla genel bilgi için bkz. [Linux üzerinde SQL Server 2017 'ye genel bakış](/sql/linux/sql-server-linux-overview). SQL Server 2017 Linux sanal makinelerini kullanma hakkında daha fazla bilgi için bkz. [Azure'daki SQL Server 2017 sanal makinelerine genel bakış](sql-server-on-linux-vm-what-is-iaas-overview.md).