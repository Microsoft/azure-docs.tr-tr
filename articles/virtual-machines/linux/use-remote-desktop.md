---
title: Linux ile xrdp kullanma
description: Grafik araçlarını kullanarak Azure 'da bir Linux VM 'sine bağlanmak için Uzak Masaüstü 'Nü (xrdp) yüklemeyi ve yapılandırmayı öğrenin
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/03/2021
ms.author: cynthn
ms.openlocfilehash: 309b106d2141c8257c5163efe7ff45a7bae5d5c3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759660"
---
# <a name="install-and-configure-xrdp-to-use-remote-desktop-with-ubuntu"></a>Ubuntu ile uzak masaüstü kullanmak için xrdp 'yi yükleyip yapılandırın

Azure 'daki Linux sanal makineleri (VM 'Ler), genellikle güvenli bir kabuk (SSH) bağlantısı kullanılarak komut satırından yönetilir. Linux 'ta yeni veya hızlı sorun giderme senaryolarında, uzak masaüstü kullanımı daha kolay olabilir. Bu makalede, Ubuntu çalıştıran Linux sanal makinenizin masaüstü ortamının ([Xfce](https://www.xfce.org)) ve uzak masaüstü 'nün ([xrdp](http://xrdp.org)) nasıl yükleneceği ve yapılandırılacağı açıklanır.

Makale, Ubuntu 18,04 VM kullanılarak yazıldı ve test edilmiştir. 

## <a name="prerequisites"></a>Önkoşullar

Bu makalede, Azure 'da mevcut bir Ubuntu 18,04 LTS sanal makinesi gereklidir. Bir VM oluşturmanız gerekiyorsa aşağıdaki yöntemlerden birini kullanın:

- [Azure CLI](quick-create-cli.md)
- [Azure Portal](quick-create-portal.md)


## <a name="install-a-desktop-environment-on-your-linux-vm"></a>Linux sanal makinenize masaüstü ortamı yükler

Azure 'daki Linux VM 'lerinin çoğunda varsayılan olarak bir masaüstü ortamı yüklü değildir. Linux VM 'Ler, masaüstü ortamı yerine genellikle SSH bağlantıları kullanılarak yönetilir. Linux 'ta seçebileceğiniz çeşitli masaüstü ortamları vardır. Masaüstü ortamınız seçiminize bağlı olarak, bir ila 2 GB disk alanı tüketebilir ve gerekli tüm paketleri yüklemek ve yapılandırmak için 5 ila 10 dakika sürebilir.

Aşağıdaki örnek, bir Ubuntu 18,04 LTS sanal makinesine basit [Xfce4](https://www.xfce.org/) masaüstü ortamı 'nı yükleme. Diğer dağıtımlara yönelik komutlar biraz farklılık gösterir ( `yum` Örneğin, Red Hat Enterprise Linux yüklemek ve uygun `selinux` kuralları yapılandırmak ya da `zypper` SUSE 'e yüklemek için kullanmak için kullanın).

İlk olarak, sanal makinenize SSH. Aşağıdaki örnek, *azureuser* Kullanıcı adı ile *myvm.westus.cloudapp.Azure.com* adlı sanal makineye bağlanır. Kendi değerlerinizi kullanın:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Windows kullanıyorsanız ve SSH kullanma hakkında daha fazla bilgiye ihtiyacınız varsa bkz. [Windows Ile SSH anahtarlarını kullanma](ssh-from-windows.md).

Ardından, aşağıdaki gibi kullanarak Xfce 'yi yazın `apt` :

```bash
sudo apt-get update
sudo apt-get -y install xfce4
```

## <a name="install-and-configure-a-remote-desktop-server"></a>Uzak masaüstü sunucusunu yükleyip yapılandırma
Artık yüklü bir masaüstü ortamınız olduğuna göre, gelen bağlantıları dinlemek için bir uzak masaüstü hizmeti yapılandırın. [xrdp](http://xrdp.org) , çoğu Linux dağılımından kullanılabilen açık kaynaklı Uzak Masaüstü Protokolü (RDP) sunucusudur ve Xfce ile iyi çalışmaktadır. Aşağıdaki şekilde Ubuntu sanal makinenize xrdp 'yi yüklersiniz:

```bash
sudo apt-get -y install xrdp
sudo systemctl enable xrdp
```

Oturumunuzu başlattığınızda xrdp 'ye hangi masaüstü ortamının kullanılacağını söyleyin. Xrdp 'yi masaüstü ortamınız olarak şu şekilde kullanacak şekilde yapılandırın:

```bash
echo xfce4-session >~/.xsession
```

Değişikliklerin etkili olması için xrdp hizmetini yeniden başlatın:

```bash
sudo service xrdp restart
```


## <a name="set-a-local-user-account-password"></a>Yerel Kullanıcı hesabı parolası ayarlama
VM 'nizi oluştururken Kullanıcı hesabınız için bir parola oluşturduysanız, bu adımı atlayın. Yalnızca SSH anahtarı kimlik doğrulaması kullanıyorsanız ve yerel hesap parolası ayarlanmamışsa, sanal makinenizde oturum açmak için xrdp kullanmadan önce bir parola belirtin. xrdp, kimlik doğrulaması için SSH anahtarlarını kabul edemez. Aşağıdaki örnek, *azureuser* Kullanıcı hesabı için bir parola belirtir:

```bash
sudo passwd azureuser
```

> [!NOTE]
> Bir parola belirtilmesi, SSHD yapılandırmanızı, şu anda yoksa parola oturumlarını açmaya izin verecek şekilde güncelleştirmez. Bir güvenlik perspektifinden, anahtar tabanlı kimlik doğrulaması kullanarak bir SSH tüneli ile sanal makinenize bağlanmak ve ardından xrdp 'ye bağlanmak isteyebilirsiniz. Bu durumda, uzak masaüstü trafiğine izin vermek için bir ağ güvenlik grubu kuralı oluşturma konusunda aşağıdaki adımı atlayın.


## <a name="create-a-network-security-group-rule-for-remote-desktop-traffic"></a>Uzak Masaüstü trafiği için bir ağ güvenlik grubu kuralı oluşturma
Uzak Masaüstü trafiğinin Linux VM 'nize erişmesine izin vermek için, 3389 numaralı bağlantı noktasında sanal makinenize ulaşmasını sağlayan bir ağ güvenlik grubu kuralı oluşturulması gerekir. Ağ güvenlik grubu kuralları hakkında daha fazla bilgi için bkz. [ağ güvenlik grubu nedir?](../../virtual-network/network-security-groups-overview.md) Ayrıca [, bir ağ güvenlik grubu kuralı oluşturmak için Azure Portal de kullanabilirsiniz](../windows/nsg-quickstart-portal.md).

Aşağıdaki örnek, *3389* numaralı bağlantı noktasında [az VM Open-Port](/cli/azure/vm#az_vm_open_port) ile bir ağ güvenlik grubu kuralı oluşturur. Sanal makinenize SSH oturumundan değil, Azure CLı 'dan aşağıdaki ağ güvenlik grubu kuralını açın:

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 3389
```


## <a name="connect-your-linux-vm-with-a-remote-desktop-client"></a>Linux VM 'nizi uzak masaüstü istemcisiyle bağlama

Yerel Uzak Masaüstü istemcinizi açın ve Linux sanal makinenizin IP adresine veya DNS adına bağlanın. 

:::image type="content" source="media/use-remote-desktop/remote-desktop.png" alt-text="Uzak Masaüstü istemcisinin ekran görüntüsü.":::

VM 'nizin Kullanıcı hesabı için Kullanıcı adını ve parolayı şu şekilde girin:

:::image type="content" source="media/use-remote-desktop/xrdp-login.png" alt-text="Xrdp oturum açma ekranının ekran görüntüsü.":::

Kimlik doğrulamasından sonra, Xfce masaüstü ortamı yüklenir ve aşağıdaki örneğe benzer şekilde görünür:

![xrdp aracılığıyla Xfce masaüstü ortamı](./media/use-remote-desktop/xfce-desktop-environment.png)

Yerel RDP istemciniz ağ düzeyinde kimlik doğrulaması (NLA) kullanıyorsa, bu bağlantı ayarını devre dışı bırakmanız gerekebilir. XRDP Şu anda NLA 'yı desteklemiyor. Ayrıca, [FreeRDP](https://www.freerdp.com)gibi nla 'yı destekleyen alternatif RDP çözümlerine bakabilirsiniz.


## <a name="troubleshoot"></a>Sorun giderme
Linux VM 'nize bir uzak masaüstü istemcisi kullanarak bağlanamıyorsanız, `netstat` sanal MAKINENIZIN RDP bağlantılarını dinlediğini doğrulamak Için LINUX sanal makinenizde aşağıdaki gibi kullanın:

```bash
sudo netstat -plnt | grep rdp
```

Aşağıdaki örnek, TCP bağlantı noktası 3389 ' de beklendiği gibi dinleme yapan VM 'yi gösterir:

```bash
tcp     0     0      127.0.0.1:3350     0.0.0.0:*     LISTEN     53192/xrdp-sesman
tcp     0     0      0.0.0.0:3389       0.0.0.0:*     LISTEN     53188/xrdp
```

*Xrdp-sesman* hizmeti dinlemediğinde, Ubuntu VM 'de hizmeti aşağıdaki şekilde yeniden başlatın:

```bash
sudo service xrdp restart
```

Hizmetin yanıt vermemesine neden olabilecek göstergeler için Ubuntu VM 'nizde */var/log* içindeki günlükleri gözden geçirin. Ayrıca, bir Uzak Masaüstü bağlantısı sırasında hataları görüntüleme denemesi sırasında Syslog 'yi izleyebilirsiniz:

```bash
tail -f /var/log/syslog
```

Red Hat Enterprise Linux ve SUSE gibi diğer Linux dağıtımlarındaki hizmetleri yeniden başlatmak için farklı yollar ve gözden geçirmek için alternatif günlük dosyası konumları olabilir.

Uzak Masaüstü istemcinizdeki herhangi bir yanıt almazsanız ve sistem günlüğünde herhangi bir olay görmüyorsanız, bu davranış uzak masaüstü trafiğinin sanal makineye ulaşamayacağını gösterir. 3389 numaralı bağlantı noktasında TCP 'ye izin veren bir kuralınız olduğundan emin olmak için ağ güvenlik grubu kurallarınızı gözden geçirin. Daha fazla bilgi için bkz. [uygulama bağlantı sorunlarını giderme](/troubleshoot/azure/virtual-machines/troubleshoot-app-connection).


## <a name="next-steps"></a>Sonraki adımlar
Linux VM 'Leri ile SSH anahtarları oluşturma ve kullanma hakkında daha fazla bilgi için bkz. [Azure 'Da Linux VM 'ler IÇIN SSH anahtarları oluşturma](mac-create-ssh-keys.md).

Windows 'dan SSH kullanma hakkında bilgi için bkz. [Windows Ile SSH anahtarlarını kullanma](ssh-from-windows.md).