---
title: Azure Lab Services Mühendislik için bir SOLIDÇALıŞMALAR Laboratuvarı ayarlama | Microsoft Docs
description: SOLIDÇALıŞMALAR kullanarak mühendislik kursları için laboratuvar ayarlamayı öğrenin.
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 3a7e8c47977f0518a3a3e9f8a6fd2e57454e1c42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99626113"
---
# <a name="set-up-a-lab-for-engineering-classes-using-solidworks"></a>SOLIDÇALıŞMALAR kullanarak mühendislik sınıfları için laboratuvar ayarlama

[Solidçalışmalar](https://www.solidworks.com/) , düz nesnelerin modellenmesi Için bir 3B bilgisayar destekli TASARıM (CAD) ortamı sağlar ve farklı türlerde mühendislik alanlarında kullanılır.  SOLIDÇALıŞMALARLA mühendisler, tasarımlarını kolayca oluşturabilir, görselleştirin, benzetimini yapabilir ve belgeleştirebilir.

Üniversiteler tarafından yaygın olarak kullanılan bir lisanslama seçeneği, "ağ lisanslaması" olur.   Bu seçenekle, kullanıcılar bir lisanslama sunucusu tarafından yönetilen bir lisans havuzunu paylaşır.  Bu tür bir lisans bazen "kayan" lisans olarak adlandırılır çünkü yalnızca eşzamanlı kullanıcı sayısı için yeterli lisansa sahip olmanız gerekir.  Bir Kullanıcı KESINTISDLEDı kullanılarak yapıldığında, lisansları merkezi olarak yönetilen lisans havuzuna geri dönerek başka bir kullanıcı tarafından yeniden kullanılabilir.

Bu makalede, KESINTISDÇALıŞMALARı 2019 ve ağ lisansı kullanan bir sınıfı nasıl ayarlayabileceğinizi göstereceğiz.

## <a name="license-server"></a>Lisans sunucusu

SOLIDÇALıŞMALAR ağ lisansı, lisans sunucunuzda SolidNetWork Lisans Yöneticisi 'nin yüklü ve etkinleştirilmiş olmasını gerektirir.  Bu lisans sunucusu, genellikle şirket içi ağınızda veya Azure 'daki özel bir ağda bulunur.  Sunucuda SolidNetWork Lisans yöneticisini ayarlama hakkında daha fazla bilgi için, bkz. SOLIDÇALıŞMALAR yükleme kılavuzunda [lisans yöneticisini yükleme ve etkinleştirme](https://help.solidworks.com/2019/English/Installation/install_guide/t_installing_snl_lic_mgr.htm) .  Bu ayarı yaparken, sonraki adımlarda gereksinim duyadıklarından bu yana kullanılan **bağlantı noktası numarasını** ve [**seri numarasını**](https://help.solidworks.com/2019/english/installation/install_guide/r_hid_state_serial_number.htm) unutmayın.

Lisans sunucunuz kurulduktan sonra, [sanal ağı (VNet)](./how-to-connect-peer-virtual-network.md) [Laboratuvar hesabınıza](./tutorial-setup-lab-account.md)eşetmeniz gerekir.  Laboratuvar sanal makinelerinin lisans sunucusuna ve diğer bir yönteme erişebilmeleri için laboratuvar oluşturmadan önce ağ eşlemesinin tamamlanması gerekir.

> [!NOTE]
> Laboratuvar sanal makineleri ve lisans sunucusu arasında iletişime izin vermek için güvenlik duvarınızda uygun bağlantı noktalarının açıldığını doğrulamanız gerekir.  Örneğin, lisans sunucusunun güvenlik duvarına gelen ve giden kuralların nasıl ekleneceğini gösteren [Windows Güvenlik Duvarı Için Lisans Yöneticisi bilgisayar bağlantı noktalarını değiştirme](http://help.solidworks.com/2019/english/installation/install_guide/t_mod_ports_on_lic_mgr_for_firewall.htm) yönergelerine bakın.  Ayrıca, laboratuvar sanal makinelerinin bağlantı noktalarını açmanız gerekebilir.  Laboratuvarın genel IP adresini alma da dahil olmak üzere, bu konuda daha fazla bilgi edinmek için [Labs için güvenlik duvarı ayarları](./how-to-configure-firewall-settings.md) makalesindeki adımları izleyin.

## <a name="lab-configuration"></a>Laboratuvar yapılandırması

Bu Laboratuvarı ayarlamak için, başlamak üzere bir Azure aboneliğine ve laboratuvar hesabına sahip olmanız gerekir. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun. Bir Azure aboneliği aldıktan sonra, Azure Lab Services yeni bir laboratuvar hesabı oluşturabilirsiniz. Yeni bir laboratuar hesabı oluşturma hakkında daha fazla bilgi için [Laboratuvar hesabı ayarlama](./tutorial-setup-lab-account.md)hakkında öğreticiye bakın. Ayrıca var olan bir laboratuvar hesabı da kullanabilirsiniz.

### <a name="lab-account-settings"></a>Laboratuvar hesabı ayarları

Laboratuvar hesabı için aşağıdaki tabloda açıklanan ayarları etkinleştirin. Market görüntülerinin nasıl etkinleştirileceği hakkında daha fazla bilgi için, [Laboratuvar oluşturucuları için kullanılabilen Market görüntülerinin nasıl belirtilme](./specify-marketplace-images.md)hakkındaki makaleye bakın.

| Laboratuvar hesabı ayarı | Yönergeler |
| ------------------- | ------------ |
|Market görüntüsü| Laboratuvar hesabınızda kullanılmak üzere Windows 10 Pro görüntüsünü etkinleştirin.|

> [!NOTE]
> Windows 10 ' a ek olarak, SOLIDÇALıŞMALAR Windows 'un diğer sürümlerini destekler.  Ayrıntılar için bkz. [Solidçalışmalar sistem gereksinimleri](https://www.solidworks.com/sw/support/SystemRequirements.html) .

### <a name="lab-settings"></a>Laboratuvar ayarları

Bir sınıf Laboratuvarı ayarlarken aşağıdaki tablodaki ayarları kullanın. Sınıf Laboratuvarı oluşturma hakkında daha fazla bilgi için bkz. sınıf Laboratuvarı ayarlama öğreticisi.

| Laboratuvar ayarları | Değer/yönergeler |
| ------------ | ------------------ |
|Sanal makine boyutu| **Küçük GPU (görselleştirme)**.  Bu VM, OpenGL ve DirectX gibi çerçeveleri kullanarak uzaktan görselleştirme, akış, oyun ve kodlama için idealdir.|  
|Sanal makine görüntüsü| Windows 10 Pro|

> [!NOTE]
> **Küçük GPU (görselleştirme)** sanal makine boyutu, yüksek performanslı bir grafik deneyimini etkinleştirecek şekilde yapılandırılmıştır.  Bu sanal makine boyutu hakkında daha fazla bilgi için [GPU 'lar ile laboratuvar ayarlama konusundaki](./how-to-setup-lab-gpu.md)makaleye bakın.

> [!WARNING]
> Laboratuvar oluşturmadan **önce** laboratuvar hesabının sanal ağını, lisans sunucusu için sanal ağa [eşdüzey](./how-to-connect-peer-virtual-network.md) olarak kaçırmayın.

## <a name="template-virtual-machine-configuration"></a>Şablon sanal makine yapılandırması

Bu bölümdeki adımlarda, SOLIDÇALıŞMALAR yükleme dosyalarını indirerek ve istemci yazılımını yükleyerek şablon sanal makinenizin nasıl ayarlanacağı gösterilmektedir:

1. Şablon sanal makinesini başlatın ve RDP kullanarak makineye bağlanın.

1. SOLIDÇALıŞMALAR istemci yazılımı için yükleme dosyalarını indirin. İndirmek için iki seçeneğiniz vardır:
   - [Solidçalışmalar müşteri portalından](https://login.solidworks.com/nidp/idff/sso?id=cpenglish&sid=1&option=credential&sid=1&target=https%3A%2F%2Fcustomerportal.solidworks.com%2F)indirin.
   - Sunucudaki bir dizinden indirin.  Bu seçeneği kullandıysanız, sunucuya şablon sanal makineden erişilebildiğinden emin olmanız gerekir.  Örneğin, bu sunucu, laboratuvar hesabınızla eşlenen sanal ağda bulunabilir.
  
    Ayrıntılar için bkz. solidçalışmalarda [tek tek bilgisayarlara yükleme](http://help.solidworks.com/2019/english/Installation/install_guide/c_installing_on_individual_computers.htm?id=fc149e8a968a422a89e2a943265758d3#Pg0) , kesintisdçalışmalar Yükleme Kılavuzu.

1. Yükleme dosyaları indirildikten sonra, KESINTISDÇALıŞMALAR Yükleme Yöneticisi 'Ni kullanarak istemci yazılımını yükleme. Bkz. [bir lisans istemcisini](http://help.solidworks.com/2019/english/installation/install_guide/t_installing_snl_license_client.htm) , solidçalışmalar yükleme kılavuzuna yükleme.

    > [!NOTE]
    > **Sunucu Ekle** iletişim kutusunda, lisans sunucunuz için kullanılan **bağlantı noktası numarası** ve LISANS sunucusunun adı veya IP adresi sorulur.

## <a name="cost"></a>Maliyet

Bu sınıf için olası bir maliyet tahminini ele alalım. Bu tahmin, lisans sunucusunu çalıştırmanın maliyetini içermez. 25 öğrencilerden oluşan bir sınıf kullanacağız. 20 saatlik zamanlanan sınıf zamanı vardır. Ayrıca, her öğrenci, zamanlanan sınıf zamanı dışında ev ödevleri veya atamalar için 10 saatlik kota alır. Seçtiğimiz sanal makine boyutu, 160 laboratuvar birimi olan **küçük GPU (görselleştirme)** idi.

25 öğrenci \* (20 zamanlanan saat + 10 kota saati) \* 160 laboratuvar birimi * 0,01 saat başına usd = 1200,00 ABD Doları

>[!IMPORTANT]
> Maliyet tahmini yalnızca örnek amaçlıdır.  Fiyatlandırma hakkındaki güncel Ayrıntılar için bkz. [Azure Lab Services fiyatlandırması](https://azure.microsoft.com/pricing/details/lab-services/).  

## <a name="next-steps"></a>Sonraki adımlar

Sonraki adımlar, herhangi bir laboratuvarı ayarlamak için ortaktır.

- [Şablon oluşturma ve yönetme](how-to-create-manage-template.md)
- [Kullanıcı ekle](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kota ayarlama](how-to-configure-student-usage.md#set-quotas-for-users)
- [Zamanlama ayarlama](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Öğrenciler için e-posta kaydı bağlantıları](how-to-configure-student-usage.md#send-invitations-to-users)