---
title: Windows Server VM 'sini yönetilen bir etki alanına katma | Microsoft Docs
description: Bu öğreticide, Windows Server sanal makinesini Azure Active Directory Domain Services yönetilen bir etki alanına nasıl katılacağınızı öğrenin.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: iainfou
ms.openlocfilehash: 4753cc9a98cd59c0c5d446b3d92280aabfb72c12
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474702"
---
# <a name="tutorial-join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Öğretici: Windows Server sanal makinesini yönetilen bir etki alanına ekleme

Azure Active Directory Domain Services (Azure AD DS), Windows Server Active Directory ile tamamen uyumlu etki alanına katılması, Grup ilkesi, LDAP, Kerberos/NTLM kimlik doğrulaması gibi yönetilen etki alanı Hizmetleri sağlar. Azure AD DS yönetilen bir etki alanıyla, Azure 'daki sanal makinelere (VM 'Ler) etki alanına ekleme özellikleri ve yönetimi sağlayabilirsiniz. Bu öğreticide, bir Windows Server sanal makinesi oluşturma ve Azure AD DS yönetilen bir etki alanına ekleme işlemlerinin nasıl yapılacağı gösterilmektedir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Windows Server VM oluşturma
> * Windows Server VM 'ye bir Azure sanal ağına bağlanma
> * VM 'yi Azure AD DS yönetilen etki alanına katma

Azure aboneliğiniz yoksa başlamadan önce [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için aşağıdaki kaynaklara ihtiyacınız vardır:

* Etkin bir Azure aboneliği.
    * Azure aboneliğiniz yoksa [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Abonelikle ilişkili bir Azure Active Directory kiracısı, şirket içi bir dizinle veya yalnızca bulut diziniyle eşitlenir.
    * Gerekirse, [bir Azure Active Directory kiracı oluşturun][create-azure-ad-tenant] veya [bir Azure aboneliğini hesabınızla ilişkilendirin][associate-azure-ad-tenant].
* Azure AD kiracınızda etkinleştirilmiş ve yapılandırılmış Azure Active Directory Domain Services yönetilen bir etki alanı.
    * Gerekirse, [bir Azure Active Directory Domain Services örneği oluşturun ve yapılandırın][create-azure-ad-ds-instance].
* Azure AD kiracınızda *Azure AD DC Administrators* grubunun üyesi olan bir kullanıcı hesabı.
    * Hesabın Azure AD DS yönetilen etki alanında oturum açabilmeleri için Azure AD Connect Parola karması eşitlemesi veya self servis parola sıfırlamasının gerçekleştirildiğinden emin olun.

Etki alanına katmak istediğiniz bir VM zaten varsa, [VM 'Yi Azure AD DS yönetilen etki alanına katmak](#join-the-vm-to-the-azure-ad-ds-managed-domain)için bölümüne atlayın.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

Bu öğreticide, Azure portal kullanarak Azure AD DS yönetilen etki alanınıza katmak üzere bir Windows Server sanal makinesi oluşturacaksınız. Başlamak için öncelikle [Azure Portal](https://portal.azure.com)oturum açın.

## <a name="create-a-windows-server-virtual-machine"></a>Windows Server sanal makinesi oluşturma

Bir bilgisayarı Azure AD DS yönetilen bir etki alanına nasıl katılabilmek için bir Windows Server VM 'si oluşturalım. Bu VM, Azure AD DS yönetilen etki alanına bağlantı sağlayan bir Azure sanal ağına bağlı. Azure AD DS yönetilen bir etki alanına katılma işlemi, düzenli bir şirket içi Active Directory Domain Services etki alanına katılma ile aynıdır.

Etki alanına katmak istediğiniz bir VM zaten varsa, [VM 'Yi Azure AD DS yönetilen etki alanına katmak](#join-the-vm-to-the-azure-ad-ds-managed-domain)için bölümüne atlayın.

1. Azure portal menüsünden veya **giriş** sayfasından **kaynak oluştur**' u seçin.

1. **Kullanmaya**başlayın ' dan **Windows Server 2016 Datacenter**' u seçin.

    ![Azure portal Windows Server 2016 Datacenter VM oluşturmayı seçin](./media/join-windows-vm/select-vm-image.png)

1. **Temel bilgiler** penceresinde, sanal makine için çekirdek ayarları yapılandırın. *Kullanılabilirlik seçenekleri*, *resim*ve *Boyut*için varsayılan değerleri bırakın.

    | Parametre            | Önerilen değer   |
    |----------------------|-------------------|
    | Kaynak grubu       | *Myresourcegroup* gibi bir kaynak grubu seçin veya oluşturun |
    | Sanal makine adı | VM için *Myvm* gibi bir ad girin |
    | Bölge               | VM 'nizi oluşturmak için bir bölge seçin; örneğin, *Doğu ABD* |
    | Kullanıcı adı             | VM üzerinde oluşturulacak yerel yönetici hesabı için bir Kullanıcı adı girin; Örneğin, *azureuser* . |
    | Parola             | Yerel yönetici için VM 'de oluşturulacak güvenli bir parola girin ve ardından onaylayın. Bir etki alanı kullanıcı hesabının kimlik bilgilerini belirtmeyin. |

1. Varsayılan olarak, Azure 'da oluşturulan sanal makinelere Internet 'ten erişilemez. Bu yapılandırma, sanal makinenin güvenliğini artırmaya yardımcı olur ve olası saldırı için alanı azaltır. Bu öğreticinin bir sonraki adımında, Uzak Masaüstü Protokolü (RDP) kullanarak VM 'ye bağlanmanız ve sonra Windows Server 'ı Azure AD DS yönetilen etki alanına eklemeniz gerekir.

    RDP etkinleştirildiğinde otomatik oturum açma saldırılarına neden olmuş olabilir. Bu, birden çok başarısız oturum açma denemesi nedeniyle *yönetici* veya *yönetici* gibi ortak adlara sahip hesapları devre dışı bırakabilen bir durum olabilir. RDP yalnızca gerektiğinde etkinleştirilmelidir ve bir yetkili IP aralığı kümesiyle sınırlıdır. Azure Güvenlik Merkezi 'nin bir parçası olarak [Azure tam ZAMANıNDA VM erişimi][jit-access] , bu kısa süreli, kısıtlı RDP oturumlarını etkinleştirebilir. Yalnızca SSL üzerinden Azure portal erişim sağlamak için [bir Azure savunma ana bilgisayarı oluşturup kullanabilirsiniz (Şu anda önizleme aşamasında)][azure-bastion] .

    Bu öğreticide, VM 'ye yönelik RDP bağlantılarını el ile etkinleştirin.

    **Ortak gelen bağlantı noktaları**altında, **Seçili bağlantı noktalarına izin verme**seçeneğini belirleyin. **Gelen bağlantı noktaları Seç**açılan menüsünde, *RDP (3389)* öğesini seçin.

1. İşiniz bittiğinde **İleri: diskler**' i seçin.
1. **Işletim sistemi diski türünün**açılan menüsünde *Standart SSD*' yi seçin ve ardından İleri ' yi seçin **: ağ**.
1. SANAL makinenizin, Azure AD DS yönetilen etki alanının dağıtıldığı alt ağ ile iletişim kurabilen bir Azure sanal ağ alt ağına bağlanması gerekir. Azure AD DS yönetilen bir etki alanının kendi adanmış alt ağına dağıtılmasını öneririz. SANAL makinenizin Azure AD DS yönetilen etki alanı ile aynı alt ağda dağıtılmayın.

    VM 'nizi dağıtmanın ve uygun bir sanal ağ alt ağına bağlanmanın iki ana yolu vardır:
    
    * Azure AD DS yönetilen etki alanınız dağıtıldıktan sonra, sanal ağ ile aynı sanal ağda var olan bir alt ağ oluşturun veya seçin.
    * Azure sanal ağ [eşlemesi][vnet-peering]kullanarak bu ağa bağlı bir Azure sanal ağında bir alt ağ seçin.
    
    Azure AD DS örneğiniz için alt ağa bağlı olmayan bir sanal ağ alt ağı seçerseniz, VM 'yi yönetilen etki alanına birleştiremezsiniz. Bu öğreticide, Azure sanal ağında yeni bir alt ağ oluşturalım.

    **Ağ** bölmesinde, Azure AD DS tarafından yönetilen etki alanının dağıtıldığı sanal ağı seçin, örneğin *aaads-VNET*
1. Bu örnekte, mevcut *aaads-subnet* , Azure AD DS yönetilen etki alanının bağlı olduğu gösterilmiştir. VM 'nizi bu alt ağa bağlama. VM için bir alt ağ oluşturmak üzere **alt ağ yapılandırmasını Yönet**' i seçin.

    ![Azure portal alt ağ yapılandırmasını yönetmeyi seçin](./media/join-windows-vm/manage-subnet.png)

1. Sanal ağ penceresinin sol menüsünde **Adres alanı**' nı seçin. Sanal ağ, varsayılan alt ağ tarafından kullanılan *10.0.1.0/24*tek bir adres alanı ile oluşturulur.

    Sanal ağa ek bir IP adresi aralığı ekleyin. Bu adres aralığının ve kullanılacak gerçek IP adresi aralığının boyutu, zaten dağıtılmış olan diğer ağ kaynaklarına bağlıdır. IP adresi aralığı, Azure veya şirket içi ortamınızda var olan tüm adres aralıklarıyla çakışmamalıdır. Alt ağa dağıtmayı düşündüğünüz VM sayısı için yeterince büyük olan IP adresi aralığını boyutlandırdığınızdan emin olun.

    Aşağıdaki örnekte, ek bir IP adresi aralığı *10.0.2.0/24* eklenmiştir. Hazırlanıyor, **Kaydet**' i seçin.

    ![Azure portal ek bir sanal ağ IP adresi aralığı ekleyin](./media/tutorial-configure-networking/add-vnet-address-range.png)

1. Sonra, sanal ağ penceresinin sol menüsünde **alt ağlar**' ı seçin, sonra da **+ alt** ağ ' i seçerek alt ağ ekleyin.

1. **+ Alt ağ**' ı seçin ve ardından alt ağ için *Yönetim*gibi bir ad girin. *10.0.2.0/24*gibi bir **adres aralığı (CIDR bloğu)** sağlayın. Bu IP adresi aralığının diğer mevcut Azure veya şirket içi adres aralıklarıyla çakışmadığından emin olun. Diğer seçenekleri varsayılan değerler olarak bırakın ve **Tamam**' ı seçin.

    ![Azure portal alt ağ yapılandırması oluşturma](./media/join-windows-vm/create-subnet.png)

1. Alt ağın oluşturulması birkaç saniye sürer. Oluşturulduktan sonra alt ağ penceresini kapatmak için *X* ' i seçin.
1. **Ağ** bölmesine geri döndüğünüzde, bir VM oluşturmak için, *Yönetim*gibi açılan menüden oluşturduğunuz alt ağı seçin. Yine, doğru alt ağı seçtiğinizden ve VM 'nizi Azure AD DS yönetilen etki alanı ile aynı alt ağda dağıttığınızdan emin olun.
1. Diğer seçenekleri varsayılan değerler olarak bırakın ve sonra **Yönetim**' i seçin.
1. **Önyükleme tanılamayı** *kapalı*olarak ayarlayın. Diğer seçenekleri varsayılan değerler olarak bırakın ve ardından **gözden geçir + oluştur**' u seçin.
1. VM ayarlarını gözden geçirin ve ardından **Oluştur**' u seçin.

VM 'nin oluşturulması birkaç dakika sürer. Azure portal dağıtımın durumunu gösterir. VM çalışmaya başladıktan sonra **Kaynağa Git**' i seçin.

![Başarılı bir şekilde oluşturulduktan sonra Azure portal VM kaynağına git](./media/join-windows-vm/vm-created.png)

## <a name="connect-to-the-windows-server-vm"></a>Windows Server VM 'ye bağlanma

Şimdi, RDP kullanarak yeni oluşturulan Windows Server VM 'sine bağlanalım ve Azure AD DS yönetilen etki alanına katılamadı. VM, önceki adımda oluşturulduğunda belirttiğiniz yerel yönetici kimlik bilgilerini, mevcut etki alanı kimlik bilgilerinin hiçbirini değil kullanın.

1. **Genel bakış** bölmesinde **Bağlan**' ı seçin.

    ![Azure portal Windows sanal makinesine bağlanma](./media/join-windows-vm/connect-to-vm.png)

1. *RDP dosyasını indirme*seçeneğini belirleyin. Bu RDP dosyasını Web tarayıcınıza kaydedin.
1. VM'nize bağlanmak için indirilen RDP dosyasını açın. İstendiğinde **Bağlan**’ı seçin.
1. VM oluşturmak için önceki adımda girdiğiniz yerel yönetici kimlik bilgilerini girin, örneğin *localhost\azureuser* .
1. Oturum açma işlemi sırasında bir sertifika uyarısı görürseniz, **Evet** ' i seçin veya bağlanmaya **devam edin** .

## <a name="join-the-vm-to-the-azure-ad-ds-managed-domain"></a>VM 'yi Azure AD DS yönetilen etki alanına katma

Oluşturulan VM ve bir RDP bağlantısı kurmak için Windows Server sanal makinesini Azure AD DS yönetilen etki alanına katalım. Bu işlem, düzenli bir şirket içi Active Directory Domain Services etki alanına bağlanan bir bilgisayarla aynıdır.

1. **Sunucu Yöneticisi** sanal makinede oturum açtığınızda varsayılan olarak açılmazsa **Başlat** menüsünü ve ardından **Sunucu Yöneticisi**öğesini seçin.
1. **Sunucu Yöneticisi** penceresinin sol bölmesinde **yerel sunucu**' yı seçin. Sağ bölmedeki **Özellikler** altında **çalışma grubu**' nu seçin.

    ![VM üzerinde Sunucu Yöneticisi açın ve çalışma grubu özelliğini düzenleyin](./media/join-windows-vm/server-manager.png)

1. Azure AD DS yönetilen etki alanına katmak için **Sistem Özellikleri** penceresinde **Değiştir** ' i seçin.

    ![Çalışma grubu veya etki alanı özelliklerini değiştirmeyi seçin](./media/join-windows-vm/change-domain.png)

1. **Etki alanı** kutusunda, *contoso.com*gibi Azure AD DS yönetilen etki alanının adını belirtin ve ardından **Tamam**' ı seçin.

    ![Katılacak Azure AD DS yönetilen etki alanını belirtin](./media/join-windows-vm/join-domain.png)

1. Etki alanına katılacak etki alanı kimlik bilgilerini girin. *Azure AD DC Administrators* grubuna ait olan bir kullanıcının kimlik bilgilerini kullanın. Yalnızca bu grubun üyeleri, makineleri Azure AD DS tarafından yönetilen etki alanına katma ayrıcalıklarına sahiptir. Hesap, Azure AD DS yönetilen etki alanının veya Azure AD kiracısının bir parçası olmalıdır-Azure AD kiracınızla ilişkili dış dizinlerden gelen hesaplar, etki alanına ekleme işlemi sırasında doğru şekilde kimlik doğrulaması yapamaz. Hesap kimlik bilgileri, aşağıdaki yollarla belirtilebilir:

    * **UPN biçimi** (önerilir)-Kullanıcı hesabı IÇIN Azure AD 'de yapılandırıldığı şekilde Kullanıcı asıl adı (UPN) sonekini girin. Örneğin, *contosoadmin* kullanıcısının UPN son eki `contosoadmin@contoso.onmicrosoft.com`olacaktır. UPN biçiminin *sAMAccountName* biçimi yerine etki alanında oturum açmak için güvenilir bir şekilde kullanılabilecek, yaygın olarak kullanılan birkaç kullanım durumu vardır:
        * Bir kullanıcının UPN öneki uzunsa ( *deehasareallylongname*gibi), *sAMAccountName* otomatik olarak oluşturulabilir.
        * Birden çok Kullanıcı Azure AD kiracınızda, *Dee*gıbı aynı UPN ön ekine sahip Ise, *sAMAccountName* biçimleri otomatik olarak oluşturulabilir.
    * **SAMAccountName biçimi** -hesap adını *sAMAccountName* biçiminde girin. Örneğin, *contosoadmin* kullanıcısının *sAMAccountName* 'ı `CONTOSO\contosoadmin`.

1. Azure AD DS yönetilen etki alanına katılması birkaç saniye sürer. Bu tamamlandığında, şu ileti size etki alanına gönderilir:

    ![Etki alanına hoş geldiniz](./media/join-windows-vm/join-domain-successful.png)

    Devam etmek için **Tamam**'ı seçin.

1. Azure AD DS yönetilen etki alanına katma işlemini gerçekleştirmek için VM 'yi yeniden başlatın.

> [!TIP]
> PowerShell kullanarak bir VM 'yi [Add-Computer][add-computer] cmdlet 'i ile etki alanına katabilirsiniz. Aşağıdaki örnek *contoso* etki alanına katılır ve sonra VM 'yi yeniden başlatır. İstendiğinde, *Azure AD DC Administrators* grubuna ait bir kullanıcının kimlik bilgilerini girin:
>
> `Add-Computer -DomainName CONTOSO -Restart`
>
> Bir VM 'ye bağlanmadan ve bağlantıyı el ile yapılandırarak sanal makineye katılarak, [set-AzVmAdDomainExtension][set-azvmaddomainextension] Azure PowerShell cmdlet 'ini kullanabilirsiniz.

Windows Server VM yeniden başlatıldıktan sonra, Azure AD DS yönetilen etki alanında uygulanan tüm ilkeler sanal makineye gönderilir. Ayrıca, uygun etki alanı kimlik bilgilerini kullanarak Windows Server VM 'de da oturum açabilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Sonraki öğreticide, Azure AD DS yönetilen etki alanını yönetmenize olanak sağlayan yönetim araçlarını yüklemek için bu Windows Server VM 'sini kullanırsınız. Bu öğretici serisinde devam etmek istemiyorsanız, [RDP 'yi devre dışı bırakmak](#disable-rdp) veya [VM 'yi silmek](#delete-the-vm)için aşağıdaki Temizleme adımlarını gözden geçirin. Aksi takdirde, [sonraki öğreticiye geçin](#next-steps).

### <a name="un-join-the-vm-from-azure-ad-ds-managed-domain"></a>Azure AD DS yönetilen etki alanından VM 'yi kaldırın

VM 'yi Azure AD DS yönetilen etki alanından kaldırmak için, [sanal makineyi bir etki alanına katmak](#join-the-vm-to-the-azure-ad-ds-managed-domain)üzere adımları yeniden izleyin. Azure AD DS yönetilen etki alanına katılmak yerine, varsayılan *çalışma grubu*gibi bir çalışma grubuna katılmayı seçin. VM yeniden başlatıldıktan sonra, bilgisayar nesnesi Azure AD DS yönetilen etki alanından kaldırılır.

VM 'yi etki alanından çıkmadan [silerseniz](#delete-the-vm) , yalnız bırakılmış bir bilgisayar nesnesi Azure AD DS ' de bırakılır.

### <a name="disable-rdp"></a>RDP 'yi devre dışı bırak

Kendi uygulamalarınızı veya iş yüklerinizi çalıştırmak için bu öğreticide oluşturulan Windows Server VM 'sini kullanmaya devam ederseniz, RDP 'nin Internet üzerinden açık olduğunu hatırlayın. Güvenliği geliştirmek ve saldırı riskini azaltmak için, RDP 'nin Internet üzerinden devre dışı bırakılması gerekir. Internet üzerinden Windows Server VM 'sine RDP 'yi devre dışı bırakmak için aşağıdaki adımları izleyin:

1. Sol taraftaki menüden **kaynak grupları** ' nı seçin.
1. Kaynak grubunuzu ( *Myresourcegroup*gibi) seçin.
1. VM 'nizi ( *Myvm*gibi) seçip *ağ*' ı seçin.
1. Ağ güvenlik grubu için **gelen ağ güvenlik kuralları** altında, RDP 'ye izin veren kuralı seçin ve **Sil**' i seçin. Gelen güvenlik kuralının kaldırılması birkaç saniye sürer.

### <a name="delete-the-vm"></a>VM’yi silin

Bu Windows Server VM 'yi kullanmıyorsanız, aşağıdaki adımları kullanarak VM 'yi silin:

1. Sol taraftaki menüden **kaynak grupları** ' nı seçin.
1. Kaynak grubunuzu ( *Myresourcegroup*gibi) seçin.
1. VM 'nizi ( *Myvm*gibi) seçip **Sil**' i seçin. Kaynak silme işlemini onaylamak için **Evet** ' i seçin. VM 'nin silinmesi birkaç dakika sürer.
1. VM silindiğinde, işletim sistemi diski, ağ arabirimi kartı ve *Myvm-* prefix ile diğer tüm kaynakları seçin ve silin.

## <a name="troubleshoot-domain-join-issues"></a>Etki alanına ekleme sorunlarını giderme

Windows Server sanal makinesi, Azure AD DS tarafından yönetilen etki alanına başarıyla katılması gerekir. Bu, normal bir şirket içi bilgisayarla aynı şekilde Active Directory Domain Services bir etki alanına katılabilmelidir. Windows Server VM, Azure AD DS yönetilen etki alanına katılaamazsa bağlantı veya kimlik bilgileriyle ilgili bir sorun olduğunu gösterir. Yönetilen etki alanına başarıyla katılması için aşağıdaki sorun giderme bölümlerini gözden geçirin.

### <a name="connectivity-issues"></a>Bağlantı sorunları

Etki alanına katılması için kimlik bilgileri isteyen bir istem almazsanız bir bağlantı sorunu vardır. VM, sanal ağda Azure AD DS tarafından yönetilen etki alanına erişemiyor.

Bu sorun giderme adımlarını her bir kez denemeden sonra, Windows Server VM 'yi yönetilen etki alanına yeniden birleştirmeyi deneyin.

* VM 'nin, Azure AD DS 'de etkinleştirilmiş olduğu sanal ağa bağlı olduğunu veya eşlenmiş bir ağ bağlantısına sahip olduğunu doğrulayın.
* `ping contoso.com`gibi, yönetilen etki alanının DNS etki alanı adına ping işlemi yapmayı deneyin.
    * Ping isteği başarısız olursa, yönetilen etki alanının IP adreslerini `ping 10.0.0.4`gibi ping yapmayı deneyin. Azure kaynakları listenizden Azure AD DS yönetilen etki alanını seçtiğinizde, ortamınız için IP adresi *Özellikler* sayfasında görüntülenir.
    * Etki alanına değil, IP adresine ping işlemi yapabiliyorsanız, DNS yanlış yapılandırılmış olabilir. Yönetilen etki alanının IP adreslerinin sanal ağ için DNS sunucuları olarak yapılandırıldığını doğrulayın.
* `ipconfig /flushdns` komutunu kullanarak sanal makinedeki DNS çözümleyici önbelleğini temizlemeyi deneyin.

### <a name="credentials-related-issues"></a>Kimlik bilgileri ile ilgili sorunlar

Etki alanına katılması için kimlik bilgileri isteyen bir istem alırsanız, bu kimlik bilgilerini girdikten sonra bir hata söz konusu olduğunda, VM Azure AD DS yönetilen etki alanına bağlanabilir. Verdiğiniz kimlik bilgileri bundan sonra sanal makinenin Azure AD DS yönetilen etki alanına katılmasına izin vermez.

Bu sorun giderme adımlarını her bir kez denemeden sonra, Windows Server VM 'yi yönetilen etki alanına yeniden birleştirmeyi deneyin.

* Belirttiğiniz kullanıcı hesabının *AAD DC Administrators* grubuna ait olduğundan emin olun.
* Hesabın Azure AD DS yönetilen etki alanının veya Azure AD kiracısı 'nin bir parçası olduğundan emin olun. Azure AD kiracınızla ilişkilendirilen dış dizinlerden hesaplar, etki alanına ekleme işlemi sırasında doğru şekilde kimlik doğrulaması yapamaz.
* `contosoadmin@contoso.onmicrosoft.com`gibi kimlik bilgilerini belirtmek için UPN biçimini kullanmayı deneyin. Kiracınızda aynı UPN ön ekine sahip çok sayıda kullanıcı varsa veya UPN ön eki aşırı uzunsa, hesabınız için *sAMAccountName* otomatik olarak oluşturulabilir. Bu durumlarda, hesabınız için *sAMAccountName* biçimi, şirket içi etki alanında beklediğiniz veya kullandığınız verilerden farklı olabilir.
* Yönetilen etki alanınız için [parola eşitlemesini etkinleştirmiş][password-sync] olup olmadığınızı denetleyin. Bu yapılandırma adımı olmadan, oturum açma girişiminizi doğru bir şekilde doğrulamak için gerekli parola karmaları Azure AD DS yönetilen etki alanında yok.
* Parola eşitlemesinin tamamlanmasını bekleyin. Bir kullanıcı hesabının parolası değiştirildiğinde Azure AD 'den otomatik bir arka plan eşitlemesi Azure AD DS parolasını güncelleştirir. Parolanın, etki alanına katılması için kullanılabilir olması biraz zaman alır.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Windows Server VM oluşturma
> * Windows Server VM 'ye bir Azure sanal ağına bağlanma
> * VM 'yi Azure AD DS yönetilen etki alanına katma

Azure AD DS yönetilen etki alanınızı yönetmek için Active Directory Yönetim Merkezi (ADAC) kullanarak bir yönetim sanal makinesi yapılandırın.

> [!div class="nextstepaction"]
> [Yönetim sanal makinesine yönetim araçları 'nı yükler](tutorial-create-management-vm.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[password-sync]: active-directory-ds-getting-started-password-sync.md
[add-computer]: /powershell/module/microsoft.powershell.management/add-computer
[jit-access]: ../security-center/security-center-just-in-time.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[set-azvmaddomainextension]: /powershell/module/az.compute/set-azvmaddomainextension
