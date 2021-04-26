---
title: Windows Server VM 'sini Azure AD Domain Services yönetilen bir etki alanına katma | Microsoft Docs
description: Bu öğreticide, Windows Server sanal makinesini Azure Active Directory Domain Services yönetilen bir etki alanına nasıl katılacağınızı öğrenin.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 869c827485d9b7a6baf68d2619af98d4c2ee82b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619581"
---
# <a name="tutorial-join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Öğretici: Windows Server sanal makinesini Azure Active Directory Domain Services yönetilen bir etki alanına katma

Azure Active Directory Domain Services (Azure AD DS), Windows Server Active Directory ile tamamen uyumlu etki alanına katılması, Grup ilkesi, LDAP, Kerberos/NTLM kimlik doğrulaması gibi yönetilen etki alanı Hizmetleri sağlar. Azure AD DS yönetilen bir etki alanıyla, Azure 'daki sanal makinelere (VM 'Ler) etki alanına ekleme özellikleri ve yönetimi sağlayabilirsiniz. Bu öğreticide, bir Windows Server sanal makinesi oluşturma ve ardından bunu yönetilen bir etki alanına katma gösterilmektedir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Windows Server VM oluşturma
> * Windows Server VM 'sini bir Azure sanal ağına bağlama
> * VM 'yi yönetilen etki alanına katma

Azure aboneliğiniz yoksa başlamadan önce [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için aşağıdaki kaynaklara ihtiyacınız vardır:

* Etkin bir Azure aboneliği.
    * Azure aboneliğiniz yoksa [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Abonelikle ilişkili bir Azure Active Directory kiracısı, şirket içi bir dizinle veya yalnızca bulut diziniyle eşitlenir.
    * Gerekirse, [bir Azure Active Directory kiracı oluşturun][create-azure-ad-tenant] veya [bir Azure aboneliğini hesabınızla ilişkilendirin][associate-azure-ad-tenant].
* Azure AD kiracınızda etkinleştirilmiş ve yapılandırılmış Azure Active Directory Domain Services yönetilen bir etki alanı.
    * Gerekirse, [Azure Active Directory Domain Services yönetilen bir etki alanı oluşturun ve yapılandırın][create-azure-ad-ds-instance].
* Yönetilen etki alanının bir parçası olan bir kullanıcı hesabı.
    * Hesabın yönetilen etki alanında oturum açabilmeleri için Azure AD Connect Parola karması eşitlemesi veya self servis parola sıfırlamasının gerçekleştirildiğinden emin olun.
* Azure AD DS sanal ağınıza dağıtılan bir Azure savunma ana bilgisayarı.
    * Gerekirse, [bir Azure savunma ana bilgisayarı oluşturun][azure-bastion].

Etki alanına katmak istediğiniz bir VM zaten varsa, [VM 'yi yönetilen etki alanına katmak](#join-the-vm-to-the-managed-domain)için bölümüne atlayın.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

Bu öğreticide, Azure portal kullanarak yönetilen etki alanınıza katmak üzere bir Windows Server sanal makinesi oluşturacaksınız. Başlamak için öncelikle [Azure Portal](https://portal.azure.com)oturum açın.

## <a name="create-a-windows-server-virtual-machine"></a>Windows Server sanal makinesi oluşturma

Bir bilgisayarı yönetilen bir etki alanına nasıl katılabilmek için bir Windows Server VM 'si oluşturalım. Bu VM, yönetilen etki alanına bağlantı sağlayan bir Azure sanal ağına bağlı. Yönetilen bir etki alanına katılma işlemi, düzenli bir şirket içi Active Directory Domain Services etki alanına katılmaktır.

Etki alanına katmak istediğiniz bir VM zaten varsa, [VM 'yi yönetilen etki alanına katmak](#join-the-vm-to-the-managed-domain)için bölümüne atlayın.

1. Azure portal menüsünden veya **giriş** sayfasından **kaynak oluştur**' u seçin.

1. **Kullanmaya** başlayın ' dan **Windows Server 2016 Datacenter**' u seçin.

    ![Azure portal Windows Server 2016 Datacenter VM oluşturmayı seçin](./media/join-windows-vm/select-vm-image.png)

1. **Temel bilgiler** penceresinde, sanal makine için çekirdek ayarları yapılandırın. *Kullanılabilirlik seçenekleri*, *resim* ve *Boyut* için varsayılan değerleri bırakın.

    | Parametre            | Önerilen değer   |
    |----------------------|-------------------|
    | Kaynak grubu       | *Myresourcegroup* gibi bir kaynak grubu seçin veya oluşturun |
    | Sanal makine adı | VM için *Myvm* gibi bir ad girin |
    | Region               | VM 'nizi oluşturmak için bir bölge seçin; örneğin, *Doğu ABD* |
    | Kullanıcı adı             | VM üzerinde oluşturulacak yerel yönetici hesabı için bir Kullanıcı adı girin; Örneğin, *azureuser* . |
    | Parola             | Yerel yönetici için VM 'de oluşturulacak güvenli bir parola girin ve ardından onaylayın. Bir etki alanı kullanıcı hesabının kimlik bilgilerini belirtmeyin. |

1. Varsayılan olarak, Azure 'da oluşturulan VM 'Ler, RDP kullanarak Internet 'ten erişilebilir. RDP etkinleştirildiğinde otomatik oturum açma saldırıları meydana gelir. Bu, birden çok başarısız oturum açma girişimi nedeniyle *yönetici* veya *yönetici* gibi ortak adlara sahip hesapları devre dışı bırakabilen bir durum olabilir.

    RDP yalnızca gerektiğinde etkinleştirilmelidir ve bir yetkili IP aralığı kümesiyle sınırlıdır. Bu yapılandırma, sanal makinenin güvenliğini artırmaya yardımcı olur ve olası saldırı için alanı azaltır. Ya da yalnızca TLS üzerinden Azure portal erişim sağlayan bir Azure savunma ana bilgisayarı oluşturup kullanın. Bu öğreticinin bir sonraki adımında, sanal makineye güvenli bir şekilde bağlanmak için bir Azure savunma ana bilgisayarı kullanırsınız.

    **Ortak gelen bağlantı noktaları** altında *hiçbiri*' ni seçin.

1. İşiniz bittiğinde **İleri: diskler**' i seçin.
1. **Işletim sistemi diski türünün** açılan menüsünde *Standart SSD*' yi seçin ve ardından İleri ' yi seçin **: ağ**.
1. SANAL makinenizin, yönetilen etki alanının dağıtıldığı alt ağ ile iletişim kurabilen bir Azure sanal ağ alt ağına bağlanması gerekir. Yönetilen bir etki alanının kendi adanmış alt ağına dağıtılmasını öneririz. VM 'nizi, yönetilen etki alanınız ile aynı alt ağda dağıtmayın.

    VM 'nizi dağıtmanın ve uygun bir sanal ağ alt ağına bağlanmanın iki ana yolu vardır:
    
    * Yönetilen etki alanınız dağıtıldıktan sonra, sanal ağ ile aynı bir alt ağ oluşturun veya var olan bir alt ağı seçin.
    * Azure sanal ağ [eşlemesi][vnet-peering]kullanarak bu ağa bağlı bir Azure sanal ağında bir alt ağ seçin.
    
    Yönetilen etki alanınız için alt ağa bağlı olmayan bir sanal ağ alt ağı seçerseniz, VM 'yi yönetilen etki alanına ekleyemezsiniz. Bu öğreticide, Azure sanal ağında yeni bir alt ağ oluşturalım.

    **Ağ** bölmesinde, yönetilen etki alanınızı dağıttığınız sanal ağı seçin, örneğin *aaads-VNET*
1. Bu örnekte, var olan *aaads-subnet* , yönetilen etki alanının bağlı olduğu gösterilmiştir. VM 'nizi bu alt ağa bağlama. VM için bir alt ağ oluşturmak üzere **alt ağ yapılandırmasını Yönet**' i seçin.

    ![Azure portal alt ağ yapılandırmasını yönetmeyi seçin](./media/join-windows-vm/manage-subnet.png)

1. Sanal ağ penceresinin sol menüsünde **Adres alanı**' nı seçin. Sanal ağ, varsayılan alt ağ tarafından kullanılan *10.0.2.0/24* tek bir adres alanı ile oluşturulur. *İş yükleri* veya Azure savunma gibi diğer alt ağlar da zaten var olabilir.

    Sanal ağa ek bir IP adresi aralığı ekleyin. Bu adres aralığının ve kullanılacak gerçek IP adresi aralığının boyutu, zaten dağıtılmış olan diğer ağ kaynaklarına bağlıdır. IP adresi aralığı, Azure veya şirket içi ortamınızda var olan tüm adres aralıklarıyla çakışmamalıdır. Alt ağa dağıtmayı düşündüğünüz VM sayısı için yeterince büyük olan IP adresi aralığını boyutlandırdığınızdan emin olun.

    Aşağıdaki örnekte, ek bir IP adresi aralığı *10.0.5.0/24* eklenmiştir. Hazırlanıyor, **Kaydet**' i seçin.

    ![Azure portal ek bir sanal ağ IP adresi aralığı ekleyin](./media/join-windows-vm/add-vnet-address-range.png)

1. Sonra, sanal ağ penceresinin sol menüsünde **alt ağlar**' ı seçin, sonra da **+ alt** ağ ' i seçerek alt ağ ekleyin.

1. **+ Alt ağ**' ı seçin ve ardından alt ağ için *Yönetim* gibi bir ad girin. *10.0.5.0/24* gibi bir **adres aralığı (CIDR bloğu)** sağlayın. Bu IP adresi aralığının diğer mevcut Azure veya şirket içi adres aralıklarıyla çakışmadığından emin olun. Diğer seçenekleri varsayılan değerler olarak bırakın ve **Tamam**' ı seçin.

    ![Azure portal alt ağ yapılandırması oluşturma](./media/join-windows-vm/create-subnet.png)

1. Alt ağın oluşturulması birkaç saniye sürer. Oluşturulduktan sonra alt ağ penceresini kapatmak için *X* ' i seçin.
1. **Ağ** bölmesine geri döndüğünüzde, bir VM oluşturmak için, *Yönetim* gibi açılan menüden oluşturduğunuz alt ağı seçin. Yine, doğru alt ağı seçtiğinizden ve VM 'nizi yönetilen etki alanı ile aynı alt ağda dağıttığınızdan emin olun.
1. **Genel IP** için, açılan menüden *hiçbiri* ' ni seçin. Bu öğreticide Azure savunma 'yı kullanarak yönetime bağlandığınızda, VM 'ye atanmış bir genel IP adresine ihtiyacınız yoktur.
1. Diğer seçenekleri varsayılan değerler olarak bırakın ve sonra **Yönetim**' i seçin.
1. **Önyükleme tanılamayı** *kapalı* olarak ayarlayın. Diğer seçenekleri varsayılan değerler olarak bırakın ve ardından **gözden geçir + oluştur**' u seçin.
1. VM ayarlarını gözden geçirin ve ardından **Oluştur**' u seçin.

VM 'nin oluşturulması birkaç dakika sürer. Azure portal dağıtımın durumunu gösterir. VM çalışmaya başladıktan sonra **Kaynağa Git**' i seçin.

![Başarılı bir şekilde oluşturulduktan sonra Azure portal VM kaynağına git](./media/join-windows-vm/vm-created.png)

## <a name="connect-to-the-windows-server-vm"></a>Windows Server VM 'ye bağlanma

Sanal makinelerinize güvenli bir şekilde bağlanmak için bir Azure savunma Konağı kullanın. Azure savunma sayesinde, yönetilen bir konak sanal ağınıza dağıtılır ve VM 'lere Web tabanlı RDP veya SSH bağlantıları sağlar. VM 'Ler için genel IP adresi gerekmez ve dış uzak trafik için ağ güvenlik grubu kuralları açmanız gerekmez. Web tarayıcınızdan Azure portal kullanarak VM 'lere bağlanırsınız. Gerekirse, [bir Azure savunma ana bilgisayarı oluşturun][azure-bastion].

Sanal makinenize bağlanmak için bir savunma ana bilgisayarı kullanmak üzere aşağıdaki adımları izleyin:

1. VM 'nizin **genel bakış** bölmesinde **Bağlan** **' ı ve sonra da**' yi seçin.

    ![Azure portal kullanarak Windows sanal makinesine bağlanma](./media/join-windows-vm/connect-to-vm.png)

1. VM 'niz için önceki bölümde belirttiğiniz kimlik bilgilerini girin ve ardından **Bağlan**' ı seçin.

   ![Azure portal savunma ana bilgisayarıyla bağlantı](./media/join-windows-vm/connect-to-bastion.png)

Gerekirse, Web tarayıcınızın görüntülenecek savunma bağlantısı için açılır pencereleri açmasına izin verin. VM 'nize bağlantı kurmak birkaç saniye sürer.

## <a name="join-the-vm-to-the-managed-domain"></a>VM 'yi yönetilen etki alanına katma

Oluşturulan VM ve Azure savunma kullanılarak oluşturulan Web tabanlı bir RDP bağlantısı ile, artık Windows Server sanal makinesini yönetilen etki alanına katalım. Bu işlem, düzenli bir şirket içi Active Directory Domain Services etki alanına bağlanan bir bilgisayarla aynıdır.

1. **Sunucu Yöneticisi** sanal makinede oturum açtığınızda varsayılan olarak açılmazsa **Başlat** menüsünü ve ardından **Sunucu Yöneticisi** öğesini seçin.
1. **Sunucu Yöneticisi** penceresinin sol bölmesinde **yerel sunucu**' yı seçin. Sağ bölmedeki **Özellikler** altında **çalışma grubu**' nu seçin.

    ![VM üzerinde Sunucu Yöneticisi açın ve çalışma grubu özelliğini düzenleyin](./media/join-windows-vm/server-manager.png)

1. **Sistem Özellikleri** penceresinde, yönetilen etki alanına katılması için **Değiştir** ' i seçin.

    ![Çalışma grubu veya etki alanı özelliklerini değiştirmeyi seçin](./media/join-windows-vm/change-domain.png)

1. **Etki alanı** kutusunda, yönetilen etki alanının adını (örneğin, *aaddscontoso.com*) belirtin ve ardından **Tamam**' ı seçin.

    ![Katılacak yönetilen etki alanını belirtin](./media/join-windows-vm/join-domain.png)

1. Etki alanına katılacak etki alanı kimlik bilgilerini girin. Yönetilen etki alanının bir parçası olan bir kullanıcının kimlik bilgilerini sağlayın. Hesap, yönetilen etki alanının veya Azure AD kiracısı 'nin bir parçası olmalıdır. Azure AD kiracınızla ilişkili dış dizinlerden gelen hesaplar, etki alanına ekleme işlemi sırasında doğru şekilde kimlik doğrulaması yapamaz.

    Hesap kimlik bilgileri, aşağıdaki yollarla belirtilebilir:

    * **UPN biçimi** (önerilir)-Kullanıcı hesabı IÇIN Azure AD 'de yapılandırıldığı şekilde Kullanıcı asıl adı (UPN) sonekini girin. Örneğin, *contosoadmin* kullanıcısının UPN son eki olacaktır `contosoadmin@aaddscontoso.onmicrosoft.com` . UPN biçiminin *sAMAccountName* biçimi yerine etki alanında oturum açmak için güvenilir bir şekilde kullanılabilecek, yaygın olarak kullanılan birkaç kullanım durumu vardır:
        * Bir kullanıcının UPN öneki uzunsa ( *deehasareallylongname* gibi), *sAMAccountName* otomatik olarak oluşturulabilir.
        * Birden çok Kullanıcı Azure AD kiracınızda, *Dee* gıbı aynı UPN ön ekine sahip Ise, *sAMAccountName* biçimleri otomatik olarak oluşturulabilir.
    * **SAMAccountName biçimi** -hesap adını *sAMAccountName* biçiminde girin. Örneğin, *contosoadmin* kullanıcısının *sAMAccountName* 'ı olacaktır `AADDSCONTOSO\contosoadmin` .

1. Yönetilen etki alanına katılması birkaç saniye sürer. Bu tamamlandığında, şu ileti size etki alanına gönderilir:

    ![Etki alanına hoş geldiniz](./media/join-windows-vm/join-domain-successful.png)

    Devam etmek için **Tamam**'ı seçin.

1. Yönetilen etki alanına katılması işlemi gerçekleştirmek için VM 'yi yeniden başlatın.

> [!TIP]
> PowerShell kullanarak bir VM 'yi [Add-Computer][add-computer] cmdlet 'i ile etki alanına katabilirsiniz. Aşağıdaki örnek *Aaddscontoso* etki alanını birleştirir ve ardından VM 'yi yeniden başlatır. İstendiğinde, yönetilen etki alanının bir parçası olan bir kullanıcının kimlik bilgilerini girin:
>
> `Add-Computer -DomainName AADDSCONTOSO -Restart`
>
> Bir VM 'ye bağlanmadan ve bağlantıyı el ile yapılandırarak sanal makineye katılarak, [set-AzVmAdDomainExtension][set-azvmaddomainextension] Azure PowerShell cmdlet 'ini kullanabilirsiniz.

Windows Server VM yeniden başlatıldıktan sonra, yönetilen etki alanında uygulanan tüm ilkeler sanal makineye gönderilir. Ayrıca, uygun etki alanı kimlik bilgilerini kullanarak Windows Server VM 'de da oturum açabilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Sonraki öğreticide, yönetilen etki alanını yönetmenize olanak sağlayan yönetim araçlarını yüklemek için bu Windows Server VM 'sini kullanırsınız. Bu öğretici serisinde devam etmek istemiyorsanız, [VM 'yi silmek](#delete-the-vm)için aşağıdaki Temizleme adımlarını gözden geçirin. Aksi takdirde, [sonraki öğreticiye geçin](#next-steps).

### <a name="unjoin-the-vm-from-the-managed-domain"></a>VM 'nin yönetilen etki alanından ayrılma

VM 'yi yönetilen etki alanından kaldırmak için adımları tekrar izleyerek [VM 'yi bir etki alanına katın](#join-the-vm-to-the-managed-domain). Yönetilen etki alanına katılmak yerine, varsayılan *çalışma grubu* gibi bir çalışma grubuna katılmayı seçin. VM yeniden başlatıldıktan sonra, bilgisayar nesnesi yönetilen etki alanından kaldırılır.

VM 'yi etki alanından çıkmadan [silerseniz](#delete-the-vm) , yalnız bırakılmış bir bilgisayar nesnesi Azure AD DS ' de bırakılır.

### <a name="delete-the-vm"></a>VM’yi silin

Bu Windows Server VM 'yi kullanmıyorsanız, aşağıdaki adımları kullanarak VM 'yi silin:

1. Sol taraftaki menüden **kaynak grupları** ' nı seçin.
1. Kaynak grubunuzu ( *Myresourcegroup* gibi) seçin.
1. VM 'nizi ( *Myvm* gibi) seçip **Sil**' i seçin. Kaynak silme işlemini onaylamak için **Evet** ' i seçin. VM 'nin silinmesi birkaç dakika sürer.
1. VM silindiğinde, işletim sistemi diski, ağ arabirimi kartı ve *Myvm-* prefix ile diğer tüm kaynakları seçin ve silin.

## <a name="troubleshoot-domain-join-issues"></a>Etki alanına ekleme sorunlarını giderme

Windows Server VM 'nin yönetilen etki alanına başarıyla katılması gerekir, bu, normal bir şirket içi bilgisayarla aynı şekilde bir Active Directory Domain Services etki alanına katılabilir. Windows Server VM, yönetilen etki alanına katılaamazsa bağlantı veya kimlik bilgileriyle ilgili bir sorun olduğunu gösterir. Yönetilen etki alanına başarıyla katılması için aşağıdaki sorun giderme bölümlerini gözden geçirin.

### <a name="connectivity-issues"></a>Bağlantı sorunları

Etki alanına katılması için kimlik bilgileri isteyen bir istem almazsanız bir bağlantı sorunu vardır. VM, sanal ağdaki yönetilen etki alanına erişemiyor.

Bu sorun giderme adımlarını her bir kez denemeden sonra, Windows Server VM 'yi yönetilen etki alanına yeniden birleştirmeyi deneyin.

* VM 'nin, Azure AD DS 'de etkinleştirilmiş olduğu sanal ağa bağlı olduğunu veya eşlenmiş bir ağ bağlantısına sahip olduğunu doğrulayın.
* Yönetilen etki alanının DNS etki alanı adını (gibi) ping yapmayı deneyin `ping aaddscontoso.com` .
    * Ping isteği başarısız olursa, yönetilen etki alanının IP adreslerini (gibi) ping yapmayı deneyin `ping 10.0.0.4` . Azure kaynakları listenizden yönetilen etki alanını seçtiğinizde, ortamınız için IP adresi *Özellikler* sayfasında görüntülenir.
    * Etki alanına değil, IP adresine ping işlemi yapabiliyorsanız, DNS yanlış yapılandırılmış olabilir. Yönetilen etki alanının IP adreslerinin sanal ağ için DNS sunucuları olarak yapılandırıldığını doğrulayın.
* Komutunu kullanarak sanal makinedeki DNS çözümleyici önbelleğini temizlemeyi deneyin `ipconfig /flushdns` .

### <a name="credentials-related-issues"></a>Kimlik bilgileri ile ilgili sorunlar

Etki alanına katılması için kimlik bilgileri isteyen bir istem alırsanız, bu kimlik bilgilerini girdikten sonra bir hata söz konusu olduğunda, VM yönetilen etki alanına bağlanabilir. Verdiğiniz kimlik bilgileri bundan sonra VM 'nin yönetilen etki alanına katılmasına izin vermez.

Bu sorun giderme adımlarını her bir kez denemeden sonra, Windows Server VM 'yi yönetilen etki alanına yeniden birleştirmeyi deneyin.

* Belirttiğiniz kullanıcı hesabının yönetilen etki alanına ait olduğundan emin olun.
* Hesabın yönetilen etki alanının veya Azure AD kiracısı 'nin bir parçası olduğundan emin olun. Azure AD kiracınızla ilişkilendirilen dış dizinlerden hesaplar, etki alanına ekleme işlemi sırasında doğru şekilde kimlik doğrulaması yapamaz.
* Gibi kimlik bilgilerini belirtmek için UPN biçimini kullanmayı deneyin `contosoadmin@aaddscontoso.onmicrosoft.com` . Kiracınızda aynı UPN ön ekine sahip çok sayıda kullanıcı varsa veya UPN ön eki aşırı uzunsa, hesabınız için *sAMAccountName* otomatik olarak oluşturulabilir. Bu durumlarda, hesabınız için *sAMAccountName* biçimi, şirket içi etki alanında beklediğiniz veya kullandığınız verilerden farklı olabilir.
* Yönetilen etki alanınız için [parola eşitlemesini etkinleştirmiş][password-sync] olup olmadığınızı denetleyin. Bu yapılandırma adımı olmadan, oturum açma girişiminizi doğru bir şekilde doğrulamak için, yönetilen etki alanında gerekli parola karmaları yok.
* Parola eşitlemesinin tamamlanmasını bekleyin. Bir kullanıcı hesabının parolası değiştirildiğinde Azure AD 'den otomatik bir arka plan eşitlemesi Azure AD DS parolasını güncelleştirir. Parolanın, etki alanına katılması için kullanılabilir olması biraz zaman alır.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Windows Server VM oluşturma
> * Windows Server VM 'ye bir Azure sanal ağına bağlanma
> * VM 'yi yönetilen etki alanına katma

Yönetilen etki alanınızı yönetmek için Active Directory Yönetim Merkezi (ADAC) kullanarak bir yönetim sanal makinesi yapılandırın.

> [!div class="nextstepaction"]
> [Yönetim sanal makinesine yönetim araçları 'nı yükler](tutorial-create-management-vm.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[password-sync]: ./tutorial-create-instance.md
[add-computer]: /powershell/module/microsoft.powershell.management/add-computer
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[set-azvmaddomainextension]: /powershell/module/az.compute/set-azvmaddomainextension