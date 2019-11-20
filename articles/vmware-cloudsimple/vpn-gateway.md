---
title: CloudSimple tarafından Azure VMware çözümü-VPN Gateway ayarlama
description: Noktadan siteye VPN ağ geçidini ve siteden siteye VPN ağ geçidini ayarlamayı ve şirket içi ağınız ile CloudSimple özel bulutunuz arasında bağlantı oluşturmayı açıklar
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a8b7e238333196381524d189904871fe5933c906
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845369"
---
# <a name="set-up-vpn-gateways-on-cloudsimple-network"></a>CloudSimple ağında VPN ağ geçitlerini ayarlama

VPN ağ geçitleri, CloudSimple ağına şirket içi ağınızdan ve bir istemci bilgisayardan uzaktan bağlanmanızı sağlar. Şirket içi ağınız ile CloudSimple ağınız arasında bir VPN bağlantısı, özel bulutunuzda vCenter ve iş yüklerine erişim sağlar. CloudSimple, siteden siteye VPN ve Noktadan siteye VPN ağ geçitlerini destekler.

## <a name="vpn-gateway-types"></a>VPN ağ geçidi türleri

* **Siteden sıteye VPN** bağlantısı, şirket içi hizmetlere erişmek Için özel bulut iş yüklerinizi ayarlamanıza olanak sağlar. Ayrıca, özel bulut vCenter 'unuzda kimlik doğrulaması için bir kimlik kaynağı olarak şirket içi Active Directory de kullanabilirsiniz.  Şu anda yalnızca **Ilke tabanlı VPN** türü destekleniyor.
* **Noktadan sıteye VPN** bağlantısı, bilgisayarınızdan özel bulutunuzu bağlamak için en kolay yoldur. Özel buluta uzaktan bağlanmak için Noktadan siteye VPN bağlantısı kullanın. Noktadan siteye VPN bağlantısı için istemci yükleme hakkında bilgi için bkz. [özel bulutla VPN bağlantısı yapılandırma](set-up-vpn.md).

Bir bölgede, bir noktadan siteye VPN ağ geçidi ve bir siteden siteye VPN ağ geçidi oluşturabilirsiniz.

## <a name="automatic-addition-of-vlansubnets"></a>VLAN/alt ağların otomatik olarak eklenmesi

CloudSimple VPN Gateway 'ler, VPN ağ geçitlerine VLAN/alt ağları ekleme ilkeleri sağlar.  İlkeler, yönetim VLAN/alt ağları ve Kullanıcı tanımlı VLAN/alt ağlar için farklı kurallar belirtmenize olanak tanır.  Yönetim VLAN/alt ağları için kurallar, oluşturduğunuz tüm yeni özel bulutlar için geçerlidir.  Kullanıcı tanımlı VLAN/alt ağlar için kurallar, bir siteden siteye VPN ağ geçidi Için mevcut veya yeni özel bulutlara otomatik olarak yeni VLAN/alt ağlar eklemenize olanak tanır, her bağlantı için ilkeyi tanımlarsınız.

VPN ağ geçitlerine VLAN/alt ağları ekleme ilkeleri, siteden siteye VPN ve Noktadan siteye VPN ağ geçitleri için de geçerlidir.

## <a name="automatic-addition-of-users"></a>Kullanıcıların otomatik eklenmesi

Noktadan siteye VPN ağ geçidi, yeni kullanıcılar için Otomatik Ekleme ilkesi tanımlamanızı sağlar. Varsayılan olarak, aboneliğin tüm sahipleri ve katkıda bulunanları CloudSimple portalına erişebilir.  Kullanıcılar yalnızca CloudSimple portalı ilk kez başlatıldığında oluşturulur.  **Otomatik olarak kural ekle** seçeneğinin belirlenmesi, yeni bir kullanıcının Noktadan siteye VPN bağlantısı kullanarak cloudsimple ağına erişmesine olanak sağlar.

## <a name="set-up-a-site-to-site-vpn-gateway"></a>Siteden siteye VPN ağ geçidi ayarlama

1. [CloudSimple portalına erişin](access-cloudsimple-portal.md) ve **ağ**' ı seçin.
2. **VPN Gateway**seçin.
3. **Yeni VPN Gateway**' ye tıklayın.

    ![VPN ağ geçidi oluşturma](media/create-vpn-gateway.png)

4. **Ağ geçidi yapılandırması**için aşağıdaki ayarları belirtin ve **İleri**' ye tıklayın.

    * Ağ Geçidi türü olarak **siteden sıteye VPN** ' yi seçin.
    * Ağ geçidini tanımlamak için bir ad girin.
    * CloudSimple hizmetinizin dağıtıldığı Azure konumunu seçin.
    * İsteğe bağlı olarak, yüksek kullanılabilirliği etkinleştirin.

    ![Siteden siteye VPN ağ geçidi oluşturma](media/create-vpn-gateway-s2s.png)

    > [!WARNING]
    > Yüksek kullanılabilirliği etkinleştirmek, şirket içi VPN cihazınızın iki IP adresine bağlanmayı desteklemesini gerektirir. VPN ağ geçidi dağıtıldıktan sonra bu seçenek devre dışı bırakılamaz.

5. Şirket içi ağınızdan ilk bağlantıyı oluşturun ve **İleri**' ye tıklayın.

    * Bağlantıyı tanımlamak için bir ad girin.
    * Eş IP 'si için, şirket içi VPN ağ geçidinizin genel IP adresini girin.
    * Şirket içi VPN ağ geçidinizin eş tanımlayıcısını girin.  Eş tanımlayıcısı genellikle şirket içi VPN ağ geçidinizin genel IP adresidir.  Ağ geçidinizdeki belirli bir tanımlayıcıyı yapılandırdıysanız tanımlayıcıyı girin.
    * Şirket içi VPN ağ Geçidinizden bağlantı için kullanılacak paylaşılan anahtarı kopyalayın.  Varsayılan paylaşılan anahtarı değiştirmek ve yeni bir anahtar belirtmek için Düzenle simgesine tıklayın.
    * Şirket **Içi ön ekler**Için, cloudsimple ağına erişecek ŞIRKET içi CIDR öneklerini girin.  Bağlantıyı oluştururken birden fazla CıDR ön eki ekleyebilirsiniz.

    ![Siteden siteye VPN Ağ Geçidi bağlantısı oluşturma](media/create-vpn-gateway-s2s-connection.png)

6. Özel bulut ağınızda, şirket içi ağdan erişilecek VLAN/alt ağları etkinleştirin ve **İleri**' ye tıklayın.

    * Bir yönetim VLAN/subnet eklemek için **özel bulutların yönetim VLAN 'ları/alt ağları Ekle**' yi etkinleştirin.  VMotion ve vSAN alt ağları için yönetim alt ağı gereklidir.
    * VMotion alt ağları eklemek için, **özel bulutlar Için vMotion ağı ekleme**' yi etkinleştirin.
    * VSAN alt ağları eklemek için **özel bulutların vSAN alt ağını eklemeyi**etkinleştirin.
    * Belirli VLAN 'Ları seçin veya seçimini kaldırın.

    ![Bağlantı oluştur](media/create-vpn-gateway-s2s-connection-vlans.png)

7. Ayarları gözden geçirin ve **Gönder**' e tıklayın.

    ![Siteden siteye VPN Gateway İnceleme ve oluşturma](media/create-vpn-gateway-s2s-review.png)

## <a name="create-point-to-site-vpn-gateway"></a>Noktadan siteye VPN ağ geçidi oluşturma

1. [CloudSimple portalına erişin](access-cloudsimple-portal.md) ve **ağ**' ı seçin.
2. **VPN Gateway**seçin.
3. **Yeni VPN Gateway**' ye tıklayın.

    ![VPN ağ geçidi oluşturma](media/create-vpn-gateway.png)

4. **Ağ geçidi yapılandırması**için aşağıdaki ayarları belirtin ve **İleri**' ye tıklayın.

    * Ağ Geçidi türü olarak **noktadan sıteye VPN ' yi** seçin.
    * Ağ geçidini tanımlamak için bir ad girin.
    * CloudSimple hizmetinizin dağıtıldığı Azure konumunu seçin.
    * Noktadan siteye ağ geçidi için istemci alt ağını belirtin.  Bağlandığınızda, istemci alt ağından DHCP adresleri verilecek.

5. **Bağlantı/Kullanıcı**için aşağıdaki ayarları belirtin ve **İleri**' ye tıklayın.

    * Tüm geçerli ve gelecekteki kullanıcıların, Noktadan siteye ağ geçidi aracılığıyla özel buluta erişmesine otomatik olarak izin vermek için **tüm kullanıcıları otomatik olarak ekle**' yi seçin. Seçeneğini belirlediğinizde, Kullanıcı listesindeki tüm kullanıcılar otomatik olarak seçilir. Listedeki bireysel kullanıcıların seçimini kaldırarak otomatik seçeneği geçersiz kılabilirsiniz.
    * Bireysel kullanıcıları seçmek için Kullanıcı listesindeki onay kutularına tıklayın.

6. VLAN/alt ağlar bölümü, ağ geçidi ve bağlantılar için yönetim ve Kullanıcı VLAN 'Ları/alt ağları belirtmenize olanak tanır.

    * **Otomatik olarak ekle** seçeneği, ağ geçidinin genel ilkesini ayarlar. Ayarlar geçerli ağ geçidi için geçerlidir. Ayarlar **seçim** alanında geçersiz kılınabilir.
    * **Özel bulutların yönetim VLAN 'ları/alt ağlarını Ekle**' yi seçin. 
    * Kullanıcı tanımlı tüm VLAN 'Ları/alt ağları eklemek için **Kullanıcı tanımlı VLAN 'lar/alt ağlar Ekle**' ye tıklayın.
    * **Seçim** ayarları **otomatik olarak ekle**altındaki genel ayarları geçersiz kılar.

7. Ayarları gözden geçirmek için **İleri** 'ye tıklayın. Herhangi bir değişiklik yapmak için düzenleme simgeleri ' ne tıklayın.
8. VPN ağ geçidini oluşturmak için **Oluştur** ' a tıklayın.

### <a name="client-subnet-and-protocols-for-point-to-site-vpn-gateway"></a>Noktadan siteye VPN ağ geçidi için istemci alt ağı ve protokolleri

Noktadan siteye VPN ağ geçidi TCP ve UDP bağlantılarına izin verir.  TCP veya UDP yapılandırmasını seçerek bilgisayarınızdan bağlandığınızda kullanılacak protokolü seçin.

Yapılandırılmış istemci alt ağı, TCP ve UDP istemcileri için kullanılır.  CıDR ön eki, biri TCP ve diğeri UDP istemcileri için olmak üzere iki alt ağa bölünmüştür. Eş zamanlı olarak bağlanacak VPN kullanıcılarının sayısına göre önek maskesini seçin.  

Aşağıdaki tabloda, önek maskesi için eşzamanlı istemci bağlantısı sayısı listelenmektedir.

| Ön ek maskesi | /24 | /25 | /26 | /27 | /28 |
|-------------|-----|-----|-----|-----|-----|
| Eşzamanlı TCP bağlantısı sayısı | 124 | 60 | 28 | 12 | 4 |
| Eşzamanlı UDP bağlantısı sayısı | 124 | 60 | 28 | 12 | 4 |

Noktadan siteye VPN kullanarak bağlanmak için bkz. [noktadan sıteye VPN kullanarak CloudSimple 'A bağlanma](set-up-vpn.md#connect-to-cloudsimple-using-point-to-site-vpn).
