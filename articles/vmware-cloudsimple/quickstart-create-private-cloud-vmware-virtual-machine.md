---
title: Hızlı başlangıç-özel bulutta bir VMware VM oluşturma
description: CloudSimple özel bulutu 'nda ve VMware VM 'nin nasıl oluşturulacağını açıklar
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8d4712ca57801c15510ffcaf54852ce9287d343b
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972393"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>Özel bulutunuzda VMware sanal makineleri oluşturma

Özel bulutunuzda sanal makineler oluşturmak için, Azure portal CloudSimple portalına erişerek başlayın.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.

## <a name="access-the-cloudsimple-portal"></a>CloudSimple portalına erişim

1. **Tüm Hizmetler**’i seçin.
2. **Cloudsimple Hizmetleri**için arama yapın.
3. Özel bulutunuzu oluşturmak istediğiniz CloudSimple hizmetini seçin.
4. **Genel bakış** sayfasından Cloudsimple portalına **Git** ' e tıklayarak cloudsimple Portal için yeni bir tarayıcı sekmesi açın.  İstenirse, Azure oturum açma kimlik bilgilerinizle oturum açın.  

    ![CloudSimple portalını Başlat](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>VCenter Web arabirimi 'ni başlatma

Artık sanal makineleri ve ilkeleri ayarlamak için vCenter 'ı başlatabilirsiniz.

VCenter 'a erişmek için CloudSimple portalından başlatın. Giriş sayfasında, **ortak görevler**altında, **vSphere istemcisini Başlat**' a tıklayın.  Özel bulutu seçin ve ardından özel bulutta **vSphere Istemcisini Başlat** ' a tıklayın.

   ![VSphere Istemcisini Başlat](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>ISO veya vSphere şablonu yükleme

  > [!WARNING]
  > ISO yükleme için vSphere HTML5 istemcisini kullanın.  Flash Client kullanımı bir hatayla sonuçlanabilir.

1. Bir VM oluşturmak ve yerel sisteminizde kullanılabilir olmasını sağlamak için vCenter 'a yüklemek istediğiniz ISO veya vSphere şablonunu alın.
2. VCenter 'da **disk** simgesine tıklayın ve **vsandatastore**' i seçin. **Dosyalar** ' a ve ardından **Yeni klasör**' e tıklayın.
    ![vCenter ISO](media/vciso00.png)

3. ' IOS ve Şablonlar ' adlı bir klasör oluşturun.

4. ISOs ve şablonlar 'daki ISOs klasörüne gidin ve **dosyaları karşıya yükle**' ye tıklayın. ISO dosyasını karşıya yüklemek için ekrandaki yönergeleri izleyin.

## <a name="create-a-virtual-machine-in-vcenter"></a>VCenter 'da sanal makine oluşturma

1. VCenter 'da **konaklar ve kümeler** simgesine tıklayın.

2. **Iş yükü** ' na sağ tıklayın ve **Yeni sanal makine**' yi seçin.
    ![Yeni VM](media/vcvm01.png)

3. **Yeni sanal makine oluştur** ' u seçin ve **İleri**' ye tıklayın.
    ![Yeni VM](media/vcvm02.png)

4. Makineyi adlandırın, **Iş yükü VM** 'sinin konumunu seçin ve **İleri**' ye tıklayın.
    ![Yeni VM](media/vcvm03.png)

5. **Iş yükü** işlem kaynağını seçin ve **İleri**' ye tıklayın.
    ![Yeni VM](media/vcvm04.png)

6. **Vsandatastore** ' i seçin ve **İleri**' ye tıklayın.
    ![Yeni VM](media/vcvm05.png)

7. Varsayılan ESXi 6,5 uyumluluğu seçimini koruyun ve **İleri**' ye tıklayın.
    ![Yeni VM](media/vcvm06.png)

8. Oluşturmakta olduğunuz VM için ISO Konuk işletim sistemini seçin ve **İleri**' ye tıklayın.
    ![Yeni VM](media/vcvm07.png)

9. Sabit disk ve ağ seçeneklerini belirleyin. Yeni CD/DVD sürücüsü için, **veri deposu ISO dosyası**' nı seçin.  Genel IP adresinden bu VM 'ye giden trafiğe izin vermek istiyorsanız, ağı **VM-1**olarak seçin.
    ![Yeni VM](media/vcvm08.png)

10. Bir seçim penceresi açılır. Daha önce ISOs ve Şablonlar klasörüne yüklediğiniz dosyayı seçin ve **Tamam**' a tıklayın.
    ![Yeni VM](media/vcvm10.png)

11. Ayarları gözden geçirin ve VM 'yi oluşturmak için **Tamam** ' ı tıklatın.
    ![Yeni VM](media/vcvm11.png)

VM artık Iş yükü işlem kaynaklarına eklenir ve kullanıma hazırdır. 
![Yeni VM](media/vcvm12.png)

Temel kurulum artık tamamlanmıştır. Şirket içi VM altyapınızı kullanmaya benzer şekilde, özel bulutunuzu kullanmaya başlayabilirsiniz.

Aşağıdaki bölümler, özel bulut iş yükleri için DNS ve DHCP sunucularını ayarlama ve varsayılan ağ yapılandırmasını değiştirme hakkında isteğe bağlı bilgiler içerir.

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>VCenter 'a Kullanıcı ve kimlik kaynakları ekleme (Isteğe bağlı)

CloudSimple, Kullanıcı adı `cloudowner@cloudsimple.local`Ile varsayılan bir vCenter Kullanıcı hesabı atar. Kullanmaya başlamak için başka bir hesap kurulumu gerekli değildir.  CloudSimple normalde yöneticilere normal işlemler yapmak için ihtiyaç duydukları ayrıcalıkları atar.  Şirket içi Active Directory 'nizi veya Azure AD 'yi özel bulutunuzda [ek bir kimlik kaynağı](set-vcenter-identity.md) olarak ayarlayın.

## <a name="create-a-dns-and-dhcp-server-optional"></a>DNS ve DHCP sunucusu oluşturma (Isteğe bağlı)

Özel bir bulut ortamında çalışan uygulamalar ve iş yükleri, arama ve IP adresi ataması için ad çözümlemesi ve DHCP hizmetleri gerektirir. Bu hizmetleri sağlamak için uygun bir DHCP ve DNS altyapısı gereklidir. Özel bulut ortamınızda bu hizmetleri sağlamak için vCenter 'da bir sanal makine yapılandırabilirsiniz.

Önkoşullar

* VLAN yapılandırılmış bir dağıtılmış bağlantı noktası grubu

* Kurulumu şirket içi veya Internet tabanlı DNS sunucularına yönlendirin

* Bir sanal makine oluşturmak için sanal makine şablonu veya ISO

Aşağıdaki bağlantılar, Linux ve Windows üzerinde DHCP ve DNS sunucularını ayarlamaya yönelik rehberlik sağlar.

#### <a name="linux-based-dns-server-setup"></a>Linux tabanlı DNS sunucusu kurulumu

Linux, DNS sunucularını ayarlamak için çeşitli paketler sunar.  Bir açık kaynak bağlama DNS sunucusu ayarlamaya yönelik yönergelerin bağlantısı aşağıda verilmiştir.

[Örnek kurulum](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

#### <a name="windows-based-setup"></a>Windows tabanlı kurulum

Bu Microsoft konuları, bir Windows Server 'ı bir DNS sunucusu olarak ve bir DHCP sunucusu olarak ayarlamayı açıklamaktadır.

[DNS sunucusu olarak Windows Server](https://docs.microsoft.com/windows-server/networking/dns/dns-top)

[DHCP sunucusu olarak Windows Server](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>Ağ yapılandırmasını özelleştirme (Isteğe bağlı)

CloudSimple portalındaki ağ sayfaları, güvenlik duvarı tabloları ve VM 'Ler için genel IP adresleri için yapılandırmayı belirtmenize olanak tanır.

### <a name="allocate-public-ips"></a>Genel IP 'Leri ayır

1. CloudSimple portalındaki **Network > genel IP** sayfasına gidin.
2. **Genel IP ayır**' a tıklayın.
3. IP adresi girişini tanımlamak için bir ad girin.
4. Varsayılan konumu koruyun.
5. İstenirse, boşta kalma zaman aşımını değiştirmek için kaydırıcıyı kullanın.
6. Genel IP adresi atamak istediğiniz yerel IP adresini girin.
7. İsterseniz ilişkili bir DNS adı girin.
8. **Bitti**’ye tıklayın.

    ![Genel IP](media/quick-create-pc-public-ip.png)

Genel IP adresini ayırma görevi başlar. Görevin durumunu **etkinlik > görevler** sayfasında kontrol edebilirsiniz. Ayırma tamamlandığında, yeni giriş genel IP 'Ler sayfasında gösterilir.

Bu IP adresinin eşlenmesi gereken VM 'nin, yukarıda belirtilen yerel adresle yapılandırılması gerekir. IP adresini yapılandırma yordamı VM işletim sistemine özgüdür. Doğru yordam için VM işletim sisteminizin belgelerine başvurun.

#### <a name="example"></a>Örnek

Örneğin, Ubuntu 16,04 için Ayrıntılar aşağıda verilmiştir.

/Etc/Network/ınterfacesfile dosyasındaki Inet adres ailesi yapılandırmasına statik yöntemi ekleyin. Adres, ağ maskesi ve ağ geçidi değerlerini değiştirin. Bu örnekte eth0 arabirimini, iç IP adresi 192.168.24.10, ağ geçidi adresi 192.168.24.1 ve ağ maskesi 255.255.255.0 ' ı kullanıyoruz. Ortamınız için, kullanılabilir alt ağ bilgileri hoş geldiniz e-postasında sağlanır.

```
sudo vi /etc/network/interfaces
```

```
auto eth0
Iface eth0 inet static
iface eth0 inet static
address 192.168.24.10
netmask 255.255.255.0
gateway 192.168.24.1
dns-nameservers 8.8.8.8
dns-domain acme.com
dns-search acme.com
```
Arabirimi el ile devre dışı bırakın.

```
sudo ifdown eth0
```
Arabirimi el ile yeniden etkinleştirin.

```
sudo ifup eth0
```

Varsayılan olarak, Internet 'ten gelen tüm trafik **reddedilir**. Başka herhangi bir bağlantı noktasını açmak isterseniz, bir [güvenlik duvarı tablosu](firewall.md)oluşturun.

Bir iç IP adresini statik IP adresi olarak yapılandırdıktan sonra, sanal makinenin içinden Internet 'e ulaşabildiğinizi doğrulayın.

```
ping 8.8.8.8
```
Ayrıca, genel IP adresini kullanarak VM 'ye Internet 'ten ulaşabildiğinizi doğrulayın.

VM üzerindeki tüm IPTable kurallarının 80 gelen bağlantı noktasını engellemediğinden emin olun.
        
```
netstat -an | grep 80
```

80 numaralı bağlantı noktasında dinleme yapan bir http sunucusu başlatın.
       
```
python2.7 -m SimpleHTTPServer 80
```

veya

```
python3 -m http.server 80
```
Masaüstünüzde bir tarayıcı başlatın ve VM 'nizin dosyalarına gözatmasını sağlamak için genel IP adresi bağlantı noktası 80 ' ye işaret edin.

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>Genel IP için varsayılan CloudSimple güvenlik duvarı kuralları

* VPN trafiği: VPN ve tüm iş yükü ağları ve yönetim ağı arasındaki (from/ile) tüm trafiğe izin verilir.
* Özel bulut iç trafiği: (From/to) iş yükü ağları ve yönetim ağı (yukarıda gösterilen) arasındaki tüm Doğu Batı trafiğine izin verilir.
* Internet trafiği:
  * Internet 'ten gelen tüm trafik iş yükü ağları ve yönetim ağı için reddedilir.
  * İş yükü ağlarından veya yönetim ağından Internet 'e giden tüm trafiğe izin verilir.

Ayrıca, güvenlik duvarı kuralları özelliğini kullanarak, trafiğinizi güvenli hale getirmenin şeklini de değiştirebilirsiniz. Daha fazla bilgi için bkz. [güvenlik duvarı tablolarını ve kurallarını ayarlama](firewall.md).

## <a name="install-solutions-optional"></a>Çözümleri (Isteğe bağlı) yükler

Özel bulut vCenter ortamınızdan tam olarak yararlanabilmek için, CloudSimple özel bulutunuzda çözümler yükleyebilirsiniz. Sanal makinelerinizi korumak için yedekleme, olağanüstü durum kurtarma, çoğaltma ve diğer işlevleri ayarlayabilirsiniz. Örnek olarak VMware Site Recovery Manager (VMware SRM) ve Veead yedekleme & çoğaltması bulunur.

Bir çözümü yüklemek için, sınırlı bir süre için ek ayrıcalıklar istemeniz gerekir. Bkz. [hakları Yükselt](escalate-private-cloud-privileges.md).

## <a name="next-steps"></a>Sonraki adımlar

* [Azure 'da VMware VM 'lerini kullanma](quickstart-create-vmware-virtual-machine.md)
* [Azure ExpressRoute kullanarak şirket içi ağa bağlanma](on-premises-connection.md)
* [CloudSimple ağında VPN ağ geçitlerini ayarlama](vpn-gateway.md)
