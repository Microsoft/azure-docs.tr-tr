---
title: Azure Lab Services (UI) içindeki bir şablon sanal makinesinde iç içe sanallaştırmayı etkinleştirme | Microsoft Docs
description: İçinde birden çok VM içeren bir şablon VM 'si oluşturmayı öğrenin.  Diğer bir deyişle, Azure Lab Services bir şablon sanal makinesinde iç içe sanallaştırmayı etkinleştirin.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: f8135e11fb7b7ddb588ab3a8ed01227712072fd2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94647928"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-manually"></a>Azure Lab Services bir şablon sanal makinesinde el ile iç içe sanallaştırmayı etkinleştirin

İç içe sanallaştırma, bir laboratuvarın şablon sanal makinesi içinde çoklu VM ortamı oluşturmanızı sağlar. Şablonu yayımlamak, her kullanıcıya, içindeki birden çok VM ile bir sanal makine oluşturacak şekilde laboratuvar sağlar.  İç içe sanallaştırma ve Azure Lab Services hakkında daha fazla bilgi için bkz. [Azure Lab Services bir şablon sanal makinesinde iç içe sanallaştırmayı etkinleştirme](how-to-enable-nested-virtualization-template-vm.md).

Bu makalede, doğrudan Windows rolleri ve araçları kullanarak Azure Lab Services bir şablon makinesinde iç içe sanallaştırmanın nasıl ayarlanacağı ele alınmaktadır.  Bir sınıfın iç içe sanallaştırmayı kullanmasını sağlamak için gereken birkaç nokta vardır.  Aşağıdaki adımlarda, Hyper-V ile bir laboratuvar hizmetleri makine şablonunun el ile nasıl ayarlanacağı açıklanır.  Adımlar Windows Server 2016 veya Windows Server 2019 için tasarlanmıştır.  

>[!IMPORTANT]
>Laboratuvar oluştururken sanal makine boyutu için **büyük (iç içe sanallaştırma)** veya **Orta (iç içe sanallaştırma)** seçeneğini belirleyin.  İç içe sanallaştırma, aksi takdirde çalışmaz.  

## <a name="enable-hyper-v-role"></a>Hyper-V rolünü etkinleştir

Aşağıdaki adımlarda, Sunucu Yöneticisi kullanarak Windows Server 'da Hyper-V ' i etkinleştirmek için gereken eylemler açıklanır.  Yükleme başarılı olduktan sonra, Hyper-V Yöneticisi istemci sanal makinelerini eklemek, değiştirmek ve silmek için kullanılabilir olur.

1. **Sunucu Yöneticisi**, Pano sayfasında, **rol ve Özellik Ekle**' ye tıklayın.
2. **Başlamadan önce** sayfasında **İleri**'ye tıklayın.
3. **Yükleme türünü seçin** sayfasında, rol tabanlı veya özellik tabanlı yükleme için varsayılan seçimi koruyun ve ardından **İleri**' ye tıklayın.
4. **Hedef sunucuyu seçin** sayfasında, Sunucu havuzundan bir sunucu seçin ' i seçin.   Geçerli sunucu zaten seçili olacak.  İleri'ye tıklayın.
5. **Sunucu rollerini seçin** sayfasında **Hyper-V**' yi seçin.  
6. **Rol ve Özellik Ekleme Sihirbazı** açılır penceresi görüntülenir.  **Yönetim araçlarını dahil et (varsa)** seçeneğini belirleyin.  **Özellik Ekle** düğmesine tıklayın.
7. **Sunucu rolleri seç** sayfasında, **İleri** öğesine tıklayın.
8. **Özellikleri Seç sayfasında** **İleri**' ye tıklayın.
9. **Hyper-V** sayfasında **İleri**' ye tıklayın.
10. **Sanal anahtar oluştur** sayfasında, varsayılan değerleri kabul edin ve **İleri**' ye tıklayın.
11. **Sanal makine geçişi** sayfasında, Varsayılanları kabul edin ve **İleri**' ye tıklayın.
12. **Varsayılan mağazalar** sayfasında, Varsayılanları kabul edin ve **İleri**' ye tıklayın.
13. **Yükleme seçimlerini Onayla** sayfasında, **gerekirse hedef sunucuyu otomatik olarak yeniden Başlat**' ı seçin.
14. **Rol ve Özellik Ekleme Sihirbazı** açılır penceresi göründüğünde **Evet**' e tıklayın.
15. **Yükle**'ye tıklayın.
16. **Yükleme ilerleme durumu** sayfasını, Hyper-V rolünün tamamlandığını belirtecek şekilde bekleyin.  Makine, yükleme ortasında yeniden başlatılabilir.
17. **Kapat**’a tıklayın.

## <a name="enable-dhcp-role"></a>DHCP rolünü etkinleştir

Oluşturulan Hyper-V istemci sanal makinelerinin, NAT ağında bir IP adresi olması gerekir.  NAT ağını daha sonra oluşturacağız.  IP adreslerini atayabilmeniz için bir yol, bu durumda laboratuvar sanal makine şablonu olarak bir DHCP sunucusu olarak ayarlanır.  DHCP rolünü etkinleştirmek için gereken adımlar aşağıda verilmiştir.

1. **Sunucu Yöneticisi**, **Pano** sayfasında, **rol ve Özellik Ekle**' ye tıklayın.
2. **Başlamadan önce** sayfasında **İleri**'ye tıklayın.
3. **Yükleme türünü seçin** sayfasında, **rol tabanlı veya özellik tabanlı yükleme** ' yi seçin ve ardından **İleri**' ye tıklayın.
4. **Hedef sunucuyu seçin** sayfasında, Sunucu havuzundan geçerli sunucuyu seçin ve ardından **İleri**' ye tıklayın.
5. **Sunucu rollerini seçin** sayfasında **DHCP sunucusu**' nu seçin.  
6. **Rol ve Özellik Ekleme Sihirbazı** açılır penceresi görüntülenir.  **Yönetim araçlarını dahil et (varsa)** seçeneğini belirleyin.  **Özellik Ekle**' ye tıklayın.

    >[!NOTE]
    >Statik IP adresinin bulunamadığını belirten bir doğrulama hatası görebilirsiniz.  Bu uyarı, senaryolarımız için yoksayılabilir.

7. **Sunucu rolleri seç** sayfasında, **İleri** öğesine tıklayın.
8. **Özellikleri Seç** sayfasında **İleri**' ye tıklayın.
9. **DHCP sunucusu** sayfasında, **İleri**' ye tıklayın.
10. **Yükleme seçimlerini onaylayın** sayfasında, **Yükle**'ye tıklayın.
11. **Yükleme ilerleme durumu sayfasını** , DHCP rolünün tamamlandığını belirtecek şekilde bekleyin.
12. Kapat’a tıklayın.

## <a name="enable-routing-and-remote-access-role"></a>Yönlendirme ve uzaktan erişim rolünü etkinleştir

1. **Sunucu Yöneticisi**, **Pano** sayfasında, **rol ve Özellik Ekle**' ye tıklayın.
2. **Başlamadan önce** sayfasında **İleri**'ye tıklayın.
3. **Yükleme türünü seçin** sayfasında, **rol tabanlı veya özellik tabanlı yükleme** ' yi seçin ve ardından **İleri**' ye tıklayın.
4. **Hedef sunucuyu seçin** sayfasında, Sunucu havuzundan geçerli sunucuyu seçin ve ardından **İleri**' ye tıklayın.
5. **Sunucu rollerini Seç** sayfasında, **Uzaktan erişim**' i seçin. **Tamam**'a tıklayın.
6. **Özellikleri Seç** sayfasında **İleri**' ye tıklayın.
7. **Uzaktan erişim** sayfasında, **İleri**' ye tıklayın.
8. **Rol hizmetleri** sayfasında, **yönlendirme**' yi seçin.
9. **Rol ve Özellik Ekleme Sihirbazı** açılır penceresi görüntülenir.  **Yönetim araçlarını dahil et (varsa)** seçeneğini belirleyin.  **Özellik Ekle**' ye tıklayın.
10. **İleri**’ye tıklayın.
11. **Web Sunucusu Rolü (IIS)** sayfasında **İleri**’ye tıklayın.
12. **Rol hizmetlerini seçin** sayfasında, **İleri**’ye tıklayın.
13. **Yükleme seçimlerini onaylayın** sayfasında, **Yükle**'ye tıklayın.
14. **Yükleme ilerleme durumu** sayfasını, uzaktan erişim rolünün tamamlandığını belirtecek şekilde bekleyin.  
15. **Kapat**’a tıklayın.

## <a name="create-virtual-nat-network"></a>Sanal NAT ağı oluştur

Tüm gerekli rollerin yüklenmiş olduğuna göre, artık NAT ağını oluşturmanız zaman atalım.  Oluşturma işlemi, bir anahtar ve NAT ağının kendisini oluşturmayı kapsar.  NAT (ağ adresi çevirisi) ağı, internet bağlantısına izin vermek için özel bir ağdaki bir VM grubuna genel bir IP adresi atar.  Bu durumda, özel VM 'Ler grubu iç içe geçmiş sanal makineler olacaktır.  NAT ağı, iç içe geçmiş VM 'Lerin birbirleriyle iletişim kurmasına izin verir. Anahtar, trafiği bir ağda almayı ve yönlendirmeyi işleyen bir ağ aygıtıdır.

### <a name="create-a-new-virtual-switch"></a>Yeni bir sanal anahtar oluştur

1. Windows Yönetim Araçları 'ndan **Hyper-V Yöneticisi 'ni** açın.
2. Sol taraftaki gezinti menüsünde geçerli sunucuyu seçin.
3. **Sanal anahtar Yöneticisi ' ne tıklayın...** **Hyper-V yöneticisinin** sağ tarafındaki **Eylemler** menüsünde.
4. **Sanal anahtar Yöneticisi** açılır penceresinde, oluşturulacak anahtar türü için **dahili** ' ı seçin.  **Sanal anahtar oluştur**' a tıklayın.
5. Yeni oluşturulan sanal anahtar için, adı daha kolay bir şekilde ayarlayın.  Bu örnekte, ' LabServicesSwitch ' kullanacağız.  **Tamam**'a tıklayın.
6. Yeni bir ağ bağdaştırıcısı oluşturulacak.  Ad ' vEthernet (LabServicesSwitch) ' ile benzerdir.  **Denetim Masası**'nı açın, **Ağ ve Internet**' e tıklayın, **ağ durumunu ve görevlerini görüntüle**' ye tıklayın.  Sol tarafta **Bağdaştırıcı ayarlarını değiştir**' e tıklayın.

### <a name="create-a-nat-network"></a>NAT ağı oluşturma

1. Windows Yönetim Araçları 'ndan **Yönlendirme ve uzaktan erişim** aracını açın.
2. Sol Gezinti sayfasında yerel sunucuyu seçin.
3. **İşlem**  ->  **yapılandırma ve Yönlendirme ve uzaktan erişim 'i etkinleştir ' i** seçin.
4. **Yönlendirme ve uzaktan erişim sunucusu Kurulum Sihirbazı** göründüğünde, **İleri**' ye tıklayın.
5. **Yapılandırma** sayfasında **ağ adresı çevirisi (NAT)** yapılandırması ' nı seçin.  **İleri**’ye tıklayın.

    >[!WARNING]
    >' Sanal özel ağ (VPN) erişimi ve NAT ' seçeneğini belirtmeyin.

6. **NAT Internet bağlantısı** sayfasında, ' Ethernet ' seçeneğini belirleyin.  Hyper-V Yöneticisi 'nde oluşturduğumuz ' vEthernet (LabServicesSwitch) ' bağlantısını seçmeyin. **İleri**’ye tıklayın.
7. Sihirbazın son sayfasında **son** ' a tıklayın.
8. **Hizmeti Başlat** iletişim kutusu göründüğünde **Hizmeti Başlat**' a tıklayın.
9. Hizmet başlatılana kadar bekleyin.

## <a name="update-network-adapter-settings"></a>Ağ bağdaştırıcısı ayarlarını Güncelleştir

Ağ bağdaştırıcısı, daha önce oluşturulan NAT ağı için varsayılan ağ geçidi IP 'si için kullanılan IP ile ilişkilendirilir.  Bu örnekte, 255.255.255.0 alt ağ maskesi ile 192.168.0.1 IP adresini oluşturacağız.  Daha önce oluşturulan sanal anahtarı kullanacağız.

1. **Denetim Masası**'nı açın, **Ağ ve Internet**' e tıklayın, **ağ durumunu ve görevlerini görüntüle**' ye tıklayın.
2. Sol tarafta **Bağdaştırıcı ayarlarını değiştir**' e tıklayın.  
3. **Ağ bağlantıları** penceresinde, ' vEthernet (labservicesswitch) ' öğesine çift tıklayarak **vEthernet (Labservicesswitch) durum** ayrıntıları iletişim kutusunu görüntüleyin.
4. **Özellikler** düğmesine tıklayın.
5. **Internet Protokolü sürüm 4 (TCP/IPv4)** öğesini seçin ve **Özellikler** düğmesine tıklayın.
6. **Internet Protokolü sürüm 4 (TCP/IPv4) özellikleri** iletişim kutusunda **Aşağıdaki IP adresini kullan**' ı seçin.  IP adresi için 192.168.0.1 girin. Alt ağ maskesi için 255.255.255.0 girin.  Varsayılan ağ geçidini boş bırakın.  DNS sunucularını da boş bırakın.

    >[!NOTE]
    > NAT ağımız için olan aralığınız, CıDR gösteriminde, 192.168.0.0/24 ' te olacaktır.  Bu, 192.168.0.1 ile 192.168.0.254 arasında kullanılabilir bir IP adresi aralığı oluşturur.  Kurala göre, ağ geçitleri bir alt ağ aralığındaki ilk IP adresine sahiptir.

7. Tamam'a tıklayın.

## <a name="create-dhcp-scope"></a>DHCP kapsamı oluştur

Aşağıdaki adımlar DHCP kapsamı ekleme yönergesidir.  Bu makalede, NAT ağımız CıDR gösteriminde 192.168.0.0/24 ' dir.  Bu, 192.168.0.1 ile 192.168.0.254 arasında kullanılabilir bir IP adresi aralığı oluşturur.  Oluşturulan kapsam, zaten daha önce oluşturulmuş olan IP adresini dışlayarak kullanılabilir adresler aralığında olmalıdır.

1. **Yönetim Araçları** ' nı açın ve **DHCP** yönetim aracını açın.
2. **DHCP** aracında, geçerli sunucunun düğümünü genişletin ve **IPv4**' ü seçin.
3. Eylem menüsünden Yeni Kapsam ' ı seçin **...**
4. **Yeni Kapsam Sihirbazı** göründüğünde, **hoş geldiniz** sayfasında **İleri** ' ye tıklayın.
5. **Kapsam adı** sayfasında, ' LabServicesDhcpScope ' veya ad için başka bir şey girin.  **İleri**’ye tıklayın.
6. **IP adres aralığı** sayfasında, aşağıdaki değerleri girin.

    - Başlangıç IP adresi için 192.168.0.100
    - Bitiş IP adresi için 192.168.0.200
    - 24 uzunluk için
    - Alt ağ maskesi için 255.255.255.0

7. **İleri**’ye tıklayın.
8. **Dışlamalar ve gecikme Ekle** sayfasında, **İleri**' ye tıklayın.
9. **Kira süresi** sayfasında, **İleri**' ye tıklayın.
10. **DHCP seçeneklerini yapılandır** sayfasında, **Evet, bu seçenekleri şimdi yapılandırmak istiyorum**' u seçin. **İleri**’ye tıklayın.
11. **Yönlendiricide (varsayılan ağ geçidi)**
12. Henüz yapmadıysanız, 192.168.0.1 ekleyin. **İleri**’ye tıklayın.
13. **Etki alanı adı ve DNS sunucuları** sayfasında, zaten YAPMADıYSANıZ, DNS sunucusu IP adresi olarak 168.63.129.16 ekleyin.  168.63.129.16, Azure statik DNS sunucusunun IP adresidir. **İleri**’ye tıklayın.
14. **WINS sunucuları** sayfasında, **İleri**' ye tıklayın.
15. **Kapsamı etkinleştir** sayfasında **Evet, bu kapsamı şimdi etkinleştirmek istiyorum**' u seçin.  **İleri**’ye tıklayın.
16. **Yeni Kapsam Sihirbazı Tamamlanıyor** sayfasında **son**' a tıklayın.

## <a name="conclusion"></a>Sonuç

Şablon makineniz artık Hyper-V sanal makineleri oluşturmak için hazırdır.   Hyper-V sanal makinelerinin nasıl oluşturulacağı hakkında yönergeler için bkz. [Hyper-v ' d e sanal makine oluşturma](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) .  Ayrıca, kullanılabilir işletim sistemlerini ve yazılımlarını kullanıma almak için [Microsoft değerlendirme merkezi](https://www.microsoft.com/evalcenter/) ' ne bakın.

## <a name="next-steps"></a>Sonraki adımlar

Sonraki adımlar, herhangi bir laboratuvarı ayarlamak için ortaktır.

- [Kullanıcı ekle](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kota ayarlama](how-to-configure-student-usage.md#set-quotas-for-users)
- [Zamanlama ayarlama](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Öğrenciler için e-posta kaydı bağlantıları](how-to-configure-student-usage.md#send-invitations-to-users)