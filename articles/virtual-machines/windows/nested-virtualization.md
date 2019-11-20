---
title: Azure sanal makinelerinde iç içe sanallaştırmayı etkinleştirme
description: Azure sanal makinelerinde iç içe sanallaştırmayı etkinleştirme
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
ms.author: cynthn
ms.date: 10/09/2017
ms.topic: conceptual
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.openlocfilehash: 16f5bed5a2342bb1d120d0d3dc853e0bc44376dc
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033133"
---
# <a name="how-to-enable-nested-virtualization-in-an-azure-vm"></a>Azure VM 'de iç içe sanallaştırmayı etkinleştirme

İç içe sanallaştırma birkaç Azure sanal makine ailelerinde desteklenir. Bu özellik geliştirme, test, eğitim ve tanıtım ortamları gibi destekleyici senaryolarda harika esneklik sağlar.   

Bu makalede, bir Azure VM üzerinde Hyper-V ' i etkinleştirme ve bu konuk sanal makine ile Internet bağlantısını yapılandırma adımları sağlanır.

## <a name="create-a-nesting-capable-azure-vm"></a>İç içe özellikli bir Azure VM oluşturma

Yeni bir Windows Server 2016 Azure sanal makinesi oluşturun.  Hızlı başvuru için, tüm v3 sanal makineleri iç içe sanallaştırmayı destekler. İç içe geçme desteği olan sanal makine boyutlarının tüm listesi için [Azure Işlem birimi makalesine](acu.md)göz atın.

Konuk sanal makine taleplerini desteklemeye yetecek büyüklükte bir VM boyutu seçip seçeceğini unutmayın. Bu örnekte, bir Azure VM D3_v3 boyutu kullanıyoruz. 

Dv3 veya Ev3 serisi sanal makinelerin bölgesel kullanılabilirliğini [burada](https://azure.microsoft.com/regions/services/)görebilirsiniz.

>[!NOTE]
>
>Yeni bir sanal makine oluşturmayla ilgili ayrıntılı yönergeler için, bkz [. Azure PowerShell modülü Ile Windows VM 'Leri oluşturma ve yönetme](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
    
## <a name="connect-to-your-azure-vm"></a>Azure VM 'nize bağlanma

Sanal makine ile bir uzak masaüstü bağlantısı oluşturun.

1. Sanal makine özelliklerinde **Bağlan** düğmesine tıklayın. Uzak Masaüstü Protokolü dosyasını (.rdp dosyası) oluşturulup indirilir.

2. VM'nize bağlanmak için indirilen RDP dosyasını açın. İstenirse, **Bağlan**’a tıklayın. Mac bilgisayarlarda, Mac App Store’dan bu [Uzak Masaüstü İstemcisi](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) gibi bir RDP istemcisi indirmeniz gerekir.

3. Sanal makine oluştururken belirttiğiniz kullanıcı adı ile parolayı girin ve **Tamam**’a tıklayın.

4. Oturum açma işlemi sırasında bir sertifika uyarısı alabilirsiniz. Bağlantıya devam etmek için **Evet** veya **Devam**’a tıklayın.

## <a name="enable-the-hyper-v-feature-on-the-azure-vm"></a>Azure VM 'de Hyper-V özelliğini etkinleştirme
Bu ayarları el ile yapılandırabilir veya yapılandırmayı otomatikleştirmek için bir PowerShell betiği sağladık.

### <a name="option-1-use-a-powershell-script-to-configure-nested-virtualization"></a>Seçenek 1: iç içe sanallaştırmayı yapılandırmak için bir PowerShell betiği kullanın
Windows Server 2016 konağında iç içe sanallaştırmayı etkinleştirmek için bir PowerShell betiği [GitHub](https://github.com/charlieding/Virtualization-Documentation/tree/live/hyperv-tools/Nested)' da kullanılabilir. Komut dosyası önkoşulları denetler ve Azure VM 'de iç içe sanallaştırmayı yapılandırır. Yapılandırmayı gerçekleştirmek için Azure VM 'nin yeniden başlatılması gerekiyor. Bu betik başka ortamlarda çalışabilir, ancak garanti edilmez. Azure 'da çalışan iç içe sanallaştırmaya yönelik canlı bir video tanıtımı ile Azure blog gönderisine göz atın! https://aka.ms/AzureNVblog.

### <a name="option-2-configure-nested-virtualization-manually"></a>Seçenek 2: iç içe sanallaştırmayı el ile yapılandırma

1. Azure VM 'de PowerShell 'i yönetici olarak açın. 

2. Hyper-V özelliğini ve yönetim araçlarını etkinleştirin.

    ```powershell
    Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
    ```

    >[!WARNING] 
    >
    >Bu komut Azure sanal makinesini yeniden başlatır. Yeniden başlatma işlemi sırasında RDP bağlantınızı kaybedersiniz.
    
3. Azure VM yeniden başlatıldıktan sonra, RDP kullanarak sanal makinenize yeniden bağlanın.

## <a name="set-up-internet-connectivity-for-the-guest-virtual-machine"></a>Konuk sanal makine için internet bağlantısı kurma
Konuk sanal makine için yeni bir sanal ağ bağdaştırıcısı oluşturun ve Internet bağlantısını etkinleştirmek için bir NAT ağ geçidi yapılandırın.

### <a name="create-a-nat-virtual-network-switch"></a>NAT sanal ağ anahtarı oluşturma

1. Azure VM 'de PowerShell 'i yönetici olarak açın.
   
2. İç anahtar oluşturun.

    ```powershell
    New-VMSwitch -Name "InternalNAT" -SwitchType Internal
    ```

3. Anahtarın özelliklerini görüntüleyin ve yeni bağdaştırıcı için Ifındex ' i aklınızda yapın.

    ```powershell
    Get-NetAdapter
    ```

    ![NetAdapter](./media/virtual-machines-nested-virtualization/get-netadapter.png)

    >[!NOTE] 
    >
    >Az önce oluşturduğunuz sanal anahtar için "Ifındex" i bir yere göz atın.
    
4. NAT ağ geçidi için bir IP adresi oluşturun.
    
Ağ geçidini yapılandırmak için ağınız hakkında bazı bilgilere ihtiyacınız vardır:    
  * IPAddress-NAT ağ geçidi IP 'si, sanal ağ alt ağı için varsayılan ağ geçidi adresi olarak kullanılacak IPv4 veya IPv6 adresini belirtir. Genel form a. b. c. 1 ' dir (örneğin, "192.168.0.1"). Son konumun 1 olması gerekmez, genellikle (ön ek uzunluğuna dayalıdır) olur. Genellikle, bir RFC 1918 özel ağ adresi alanı kullanmanız gerekir. 
  * PrefixLength-alt ağ ön ek uzunluğu yerel alt ağ boyutunu (alt ağ maskesi) tanımlar. Alt ağ ön eki uzunluğu 0 ile 32 arasında bir tamsayı değeri olacaktır. 0 tüm internet 'i eşleyebilir, 32 yalnızca bir eşlenmiş IP 'ye izin verir. Ortak değerler, NAT 'a kaç tane IP iliştirildiğine bağlı olarak 24 ile 12 arasında değişir. Ortak bir Önekuzunluğu 24 ' dir; bu, 255.255.255.0 alt ağ maskesidir.
  * InterfaceIndex- **Ifındex** , önceki adımda oluşturulan sanal anahtarın arabirim dizinidir. 

    ```powershell
    New-NetIPAddress -IPAddress 192.168.0.1 -PrefixLength 24 -InterfaceIndex 13
    ```

### <a name="create-the-nat-network"></a>NAT ağını oluşturma

Ağ geçidini yapılandırmak için ağ ve NAT ağ geçidi hakkında bilgi sağlamanız gerekir:
  * Ad-NAT ağının adıdır. 
  * Internalıpınterfaceaddressprefıx-NAT alt ağ ön eki, hem NAT ağ geçidi IP önekini hem de yukarıda bulunan NAT alt ağ önek uzunluğunu tanımlar. Genel form bir. b. c. 0/NAT alt ağ önek uzunluğu olacak. 

PowerShell 'de yeni bir NAT ağı oluşturun.
```powershell
New-NetNat -Name "InternalNat" -InternalIPInterfaceAddressPrefix 192.168.0.0/24
```


## <a name="create-the-guest-virtual-machine"></a>Konuk sanal makinesini oluşturma

>[!IMPORTANT] 
>
>Azure Konuk Aracısı, iç içe geçmiş VM 'lerde desteklenmez ve hem konak hem de iç içe geçmiş VM 'lerde sorunlara yol açabilir. Azure aracısını iç içe geçmiş VM 'Lere yüklemeyin ve Azure Konuk aracısının zaten yüklü olduğu iç içe geçmiş VM 'Leri oluşturmak için bir görüntü kullanmayın.

1. Hyper-V Yöneticisi 'Ni açın ve yeni bir sanal makine oluşturun. Sanal makineyi, oluşturduğunuz yeni Iç ağı kullanacak şekilde yapılandırın.
    
    ![NetworkConfig](./media/virtual-machines-nested-virtualization/configure-networking.png)
    
2. Konuk sanal makinesine bir işletim sistemi yükler.
    
    >[!NOTE] 
    >
    >İşletim sisteminin VM 'ye yüklenmesi için yükleme medyası gerekir. Bu durumda, Windows 10 Enterprise kullandık.

## <a name="assign-an-ip-address-to-the-guest-virtual-machine"></a>Konuk sanal makinesine bir IP adresi atayın

Konuk sanal makine üzerinde el ile statik bir IP adresi ayarlayarak veya IP adresini dinamik olarak atamak için Azure VM 'de DHCP 'yi yapılandırarak Konuk sanal makinesine bir IP adresi atayabilirsiniz.

###  <a name="option-1-configure-dhcp-to-dynamically-assign-an-ip-address-to-the-guest-virtual-machine"></a>Seçenek 1: DHCP 'yi konuk sanal makineye dinamik olarak bir IP adresi atamak için yapılandırma
Dinamik adres ataması için konak sanal makinesinde DHCP 'yi yapılandırmak için aşağıdaki adımları izleyin.

#### <a name="install-dchp-server-on-the-azure-vm"></a>Azure VM 'ye DCHP sunucusu 'nı yükler

1. Sunucu Yöneticisi açın. Panoda, **rol ve Özellik Ekle**' ye tıklayın. Rol ve Özellik Ekleme Sihirbazı görünür.
  
2. Sihirbazda, sunucu rolleri sayfasına kadar **İleri** ' ye tıklayın.
  
3. **DHCP sunucusu** onay kutusunu seçin, **Özellik Ekle**' ye tıklayın ve ardından Sihirbazı tamamlamadan **İleri** ' ye tıklayın.
  
4. **Yükle**'ye tıklatın.

#### <a name="configure-a-new-dhcp-scope"></a>Yeni bir DHCP kapsamı yapılandırma

1. DHCP Yöneticisi 'Ni açın.
  
2. Gezinti bölmesinde, sunucu adını genişletin, **IPv4**' e sağ tıklayın ve **yeni kapsam**' a tıklayın. Yeni Kapsam Sihirbazı görüntülenir, **İleri**' ye tıklayın.
  
3. Kapsam için bir ad ve açıklama girin ve **İleri**' ye tıklayın.
  
4. DCHP sunucunuz için bir IP aralığı tanımlayın (örneğin, 192.168.0.100 to 192.168.0.200).
  
5. Varsayılan ağ geçidi sayfasına kadar **İleri** ' ye tıklayın. Daha önce oluşturduğunuz IP adresini (örneğin, 192.168.0.1) varsayılan ağ geçidi olarak girin ve **Ekle**' ye tıklayın.
  
6. Sihirbaz tamamlanana kadar **İleri** ' ye, tüm varsayılan değerleri bırakarak **son**' a tıklayın.
    
### <a name="option-2-manually-set-a-static-ip-address-on-the-guest-virtual-machine"></a>Seçenek 2: Konuk sanal makinesinde El Ile statik bir IP adresi ayarlama
DHCP 'yi konuk sanal makineye dinamik olarak bir IP adresi atamak üzere yapılandırmadıysanız, statik bir IP adresi ayarlamak için aşağıdaki adımları izleyin.

1. Azure VM 'de PowerShell 'i yönetici olarak açın.

2. Konuk sanal makineye sağ tıklayın ve Bağlan ' a tıklayın.

3. Konuk sanal makinesinde oturum açın.

4. Konuk sanal makinede, ağ ve Paylaşım Merkezi ' ni açın.

5. Önceki bölümde oluşturduğunuz NAT ağı aralığı içindeki bir adres için ağ bağdaştırıcısını yapılandırın.

Bu örnekte, 192.168.0.0/24 aralığında bir adres kullanacaksınız.

## <a name="test-connectivity-in-guest-virtual-machine"></a>Konuk sanal makinede bağlantıyı test etme

Konuk sanal makinede, tarayıcınızı açın ve bir Web sayfasına gidin.
    ![GuestVM](./media/virtual-machines-nested-virtualization/guest-virtual-machine.png)

## <a name="set-up-intranet-connectivity-for-the-guest-virtual-machine"></a>Konuk sanal makine için intranet bağlantısı ayarla

Konuk VM 'Ler ve Azure VM 'Ler arasında şeffaf bağlantıyı etkinleştirme yönergeleri için lütfen [Bu belgeye](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization-azure-virtual-network)başvurun.
