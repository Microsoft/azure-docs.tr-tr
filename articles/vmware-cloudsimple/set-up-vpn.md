---
title: CloudSimple tarafından Azure VMware çözümü-şirket içi ve özel bulut arasında VPN yapılandırma
description: Şirket içi ağınız ile CloudSimple özel bulutunuz arasında siteden siteye veya Noktadan siteye VPN bağlantısının nasıl yapılandırılacağını açıklar.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d000d8390375466232c7daac2a4a056ef424be79
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972389"
---
# <a name="configure-a-vpn-connection-to-your-cloudsimple-private-cloud"></a>CloudSimple özel bulutunuz için bir VPN bağlantısı yapılandırma

VPN ağ geçitleri, CloudSimple ağına şirket içi ağınızdan ve bir istemci bilgisayardan uzaktan bağlanmanızı sağlar.  Bu makalede, CloudSimple portalından VPN ağ geçitlerini ayarlama hakkında bilgi edinebilirsiniz.  Şirket içi ağınız ile CloudSimple ağınız arasında bir VPN bağlantısı, özel bulutunuzda vCenter ve iş yüklerine erişim sağlar. CloudSimple, hem Noktadan siteye VPN 'i hem de siteden siteye VPN ağ geçitlerini destekler.

## <a name="vpn-gateway-types"></a>VPN ağ geçidi türleri

* **Noktadan sıteye VPN** bağlantısı, bilgisayarınızdan özel bulutunuzu bağlamak için en kolay yoldur. Özel buluta uzaktan bağlanmak için Noktadan siteye VPN bağlantısı kullanın.
* **Siteden sıteye VPN** bağlantısı, şirket içi hizmetlere erişmek Için özel bulut iş yüklerinizi ayarlamanıza olanak sağlar. Ayrıca, özel bulut vCenter 'unuzda kimlik doğrulaması için bir kimlik kaynağı olarak şirket içi Active Directory de kullanabilirsiniz.  Şu anda, **Ilke tabanlı VPN** türü desteklenir.

Bir bölgede, bir siteden siteye VPN ağ geçidi ve bir noktadan siteye VPN ağ geçidi oluşturabilirsiniz.

## <a name="point-to-site-vpn"></a>Noktadan siteye VPN

Noktadan siteye VPN ağ geçidi oluşturmak için bkz. [noktadan sıteye VPN ağ geçidi oluşturma](vpn-gateway.md#create-point-to-site-vpn-gateway).

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Noktadan siteye VPN kullanarak CloudSimple 'a bağlanma

VPN istemcisi, bilgisayarınızdan CloudSimple 'a bağlanmak için gereklidir.  MacOS ve OS X için Windows veya [viscosity](https://www.sparklabs.com/viscosity/download/) Için [OpenVPN istemcisini](https://openvpn.net/community-downloads/) indirin.

1. CloudSimple portalını başlatın ve **ağ**' ı seçin.
2. **VPN Gateway**seçin.
3. VPN ağ geçitleri listesinden Noktadan siteye VPN Gateway ' e tıklayın.
4. **Kullanıcıları**seçin.
5. **VPN yapılandırması 'Nı indir** 'e tıklayın

    ![VPN yapılandırmasını indir](media/download-p2s-vpn-configuration.png)

6. VPN istemcinizdeki yapılandırmayı içeri aktarın

    * [Windows istemcisinde yapılandırmayı içeri aktarma](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program) yönergeleri
    * [MacOS veya OS X üzerinde yapılandırmayı içeri aktarma](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection) yönergeleri

7. CloudSimple VPN Gateway 'e bağlanın.

Aşağıdaki örnekte, **viscosity istemcisi**kullanılarak bağlantı içeri aktarma gösterilmektedir.

#### <a name="import-connection-on-viscosity-client"></a>Viscosity istemcisinde bağlantı içeri aktar

1. İndirilen. zip dosyasından VPN yapılandırmasının içeriğini ayıklayın.

2. Bilgisayarınızda viscosity 'yi açın.

3. Simgeye tıklayın ve **bağlantıyı** > dosyadan içeri aktar ' ı seçin. **+**

    ![VPN yapılandırmasını dosyadan içeri aktar](media/import-p2s-vpn-config.png)

4. Kullanmak istediğiniz protokol için OpenVPN yapılandırma dosyasını (. ovpn) seçin ve **Aç**' a tıklayın.

    ![VPN](media/import-p2s-vpn-config-choose-ovpn.png)

Bağlantı artık viscosity menüsünde görüntülenir.

#### <a name="connect-to-the-vpn"></a>VPN'e bağlanın

Viscosity OpenVPN istemcisini kullanarak VPN 'ye bağlanmak için, menüden bağlantıyı seçin. Menü simgesi, bağlantının oluşturulduğuna işaret etmek için güncellenir.

![VPN](media/vis03.png)

### <a name="connecting-to-multiple-private-clouds"></a>Birden çok özel bulutlara bağlanma

Noktadan siteye VPN bağlantısı, oluşturduğunuz ilk özel bulutun DNS adlarını çözer. Diğer özel bulutlara erişmek istediğinizde, VPN istemcinizdeki DNS sunucusunu güncelleştirmeniz gerekir.

1. [Cloudsimple portalını](access-cloudsimple-portal.md)başlatın.

2. **Kaynaklar** > **özel bulutları** ' na gidin ve bağlanmak istediğiniz özel bulutu seçin.

3. Özel bulutun **Özet** sayfasında, **temel bilgi**altında özel bulut DNS sunucusu IP adresini kopyalayın.

    ![Özel bulut DNS sunucuları](media/private-cloud-dns-server.png)

4. Bilgisayarınızın sistem tepsisindeki viscosity simgesine sağ tıklayın ve **Tercihler**' i seçin.

    ![VPN](media/vis00.png)

5. CloudSimple VPN bağlantısını seçin.

    ![VPN bağlantısı](media/viscosity-client.png)

6. Bağlantı özelliklerini değiştirmek için **Düzenle** ' ye tıklayın.

    ![VPN bağlantısını Düzenle](media/viscosity-edit-connection.png)

7. **Ağ** sekmesine tıklayın ve virgülle veya alana ve etki alanına ```cloudsimple.io```göre ayrılmış özel bulut DNS sunucusu IP adreslerini girin.  **VPN sunucusu tarafından GÖNDERILEN DNS ayarlarını yoksay**' ı seçin.

    ![VPN ağı](media/viscosity-edit-connection-networking.png)

> [!IMPORTANT]
> İlk özel bulutunuzu bağlamak için bu ayarları kaldırın ve VPN sunucusuna bağlanın.

## <a name="site-to-site-vpn"></a>Siteden siteye VPN

Siteden siteye VPN ağ geçidi oluşturmak için bkz. [siteden sıteye VPN ağ geçidi oluşturma](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway).  Şirket içi ağınızdan özel bulutunuz için siteden siteye VPN bağlantısı bu avantajları sağlar.  

* Şirket içi ağınızdaki herhangi bir iş istasyonundan özel bulut vCenter ' nin erişilebilirliği
* Bir vCenter Identity kaynağı olarak şirket içi Active Directory kullanımı
* VM şablonlarının, IOS 'nin ve diğer dosyaların şirket içi kaynaklarınızın özel bulut vCenter 'larınıza uygun şekilde aktarılması
* Şirket içi ağınızdan özel bulutunuzda çalışan iş yüklerinin erişilebilirliği

Şirket içi VPN ağ geçidinizi yüksek kullanılabilirlik modunda ayarlamak için bkz. [yüksek kullanılabilirliğe sahıp VPN bağlantısını yapılandırma](high-availability-vpn-connection.md).

> [!IMPORTANT]
>    1. VPN cihazınızda 1200 ' de TCP ve üst sınırı ayarlayın. Ya da VPN cihazlarınız, sahip olma özelliğini desteklemiyorsa, bunun yerine tünel arabirimindeki MTU değerini 1240 bayta ayarlayabilirsiniz.
> 2. Siteden siteye VPN kurulduktan sonra, *. cloudsimple.io için DNS isteklerini özel bulut DNS sunucularına iletin.  Şirket [ıçı DNS kurulumu](on-premises-dns-setup.md)'ndaki yönergeleri izleyin.
