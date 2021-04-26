---
title: Azure NetApp Files için Active Directory bağlantıları oluşturma ve yönetme | Microsoft Docs
description: Bu makalede Azure NetApp Files için Active Directory bağlantıları oluşturma ve yönetme işlemlerinin nasıl yapılacağı gösterilir.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/06/2021
ms.author: b-juche
ms.openlocfilehash: 27c2ab96106bbfcc05b8fa12daf9b6f7b816c5c7
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579980"
---
# <a name="create-and-manage-active-directory-connections-for-azure-netapp-files"></a>Azure NetApp Files için Active Directory bağlantıları oluşturma ve yönetme

Azure NetApp Files çeşitli özellikleri Active Directory bağlantınızın olmasını gerektirir.  Örneğin, bir [SMB birimi](azure-netapp-files-create-volumes-smb.md) veya bir [çift protokol birimi](create-volumes-dual-protocol.md)oluşturabilmeniz için önce bir Active Directory bağlantınız olması gerekir.  Bu makalede Azure NetApp Files için Active Directory bağlantıları oluşturma ve yönetme işlemlerinin nasıl yapılacağı gösterilir.

## <a name="before-you-begin"></a>Başlamadan önce  

Zaten bir kapasite havuzu ayarlamış olmalısınız.   
[Kapasite havuzu ayarlama](azure-netapp-files-set-up-capacity-pool.md)   
Azure NetApp Files için bir alt ağ atanmış olmalıdır.  
[Azure NetApp Files için bir alt ağı temsilci olarak belirleme](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Active Directory bağlantılarının gereksinimleri

 Active Directory bağlantıları için gereksinimler şunlardır: 

* Kullandığınız yönetici hesabının, belirttiğiniz kuruluş birimi (OU) yolunda makine hesapları oluşturma yeteneği olmalıdır.  

* Uygun bağlantı noktaları geçerli Windows Active Directory (AD) sunucusunda açık olmalıdır.  
    Gerekli bağlantı noktaları aşağıdaki gibidir: 

    |     Hizmet           |     Bağlantı noktası     |     Protokol     |
    |-----------------------|--------------|------------------|
    |    AD Web Hizmetleri    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    Icmpv4             |    Yok       |    Yankı yanıtı    |
    |    Kerberos           |    464       |    TCP           |
    |    Kerberos           |    464       |    UDP           |
    |    Kerberos           |    88        |    TCP           |
    |    Kerberos           |    88        |    UDP           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    UDP           |
    |    LDAP               |    3268      |    TCP           |
    |    NetBIOS adı       |    138       |    UDP           |
    |    SAM/LSA            |    445       |    TCP           |
    |    SAM/LSA            |    445       |    UDP           |
    |    W32Time            |    123       |    UDP           |

* Hedeflenen Active Directory Domain Services yönelik site topolojisi, özel olarak Azure NetApp Files dağıtıldığı Azure VNet 'te yönergelere uymalıdır.  

    Azure NetApp Files dağıtıldığı sanal ağın adres alanı yeni veya var olan bir Active Directory sitesine eklenmelidir (Azure NetApp Files tarafından erişilebilen bir etki alanı denetleyicisi). 

* Belirtilen DNS sunucularına, Azure NetApp Files için [temsilci alt ağından](./azure-netapp-files-delegate-subnet.md) ulaşılabilir olmalıdır.  

    Desteklenen ağ topolojileri için [Azure NetApp Files ağ planlama yönergelerine](./azure-netapp-files-network-topologies.md) bakın.

    Ağ güvenlik grupları (NSG 'ler) ve güvenlik duvarları, Active Directory ve DNS trafiği isteklerine izin vermek için uygun şekilde yapılandırılmış kurallara sahip olmalıdır. 

* Azure NetApp Files atanmış alt ağ, tüm yerel ve uzak etki alanı denetleyicileri dahil olmak üzere etki alanındaki tüm Active Directory Domain Services (ekleme) etki alanı denetleyicilerine erişebilmelidir. Aksi takdirde, hizmet kesintisi meydana gelebilir.  

    Azure NetApp Files atanmış alt ağ tarafından erişilemeyen etki alanı denetleyicileriniz varsa, Active Directory bağlantısını oluştururken bir Active Directory sitesi belirtebilirsiniz.  Azure NetApp Files, yalnızca Azure NetApp Files atanmış alt ağ adres alanının olduğu sitedeki etki alanı denetleyicileriyle iletişim kurması gerekir.

    Bkz. AD siteleri ve hizmetleriyle ilgili [site topolojisini tasarlama](/windows-server/identity/ad-ds/plan/designing-the-site-topology) . 
    
* [Active Directory Birleştir](#create-an-active-directory-connection) penceresinde **AES ŞIFRELEME** kutusunu işaretleyerek ad kimlik doğrulaması için AES şifrelemeyi etkinleştirebilirsiniz. Azure NetApp Files DES, Kerberos AES 128 ve Kerberos AES 256 şifreleme türlerini (en az güvenli olan en az güvenli) destekler. AES şifrelemesini etkinleştirirseniz Active Directory birleştirmek için kullanılan Kullanıcı kimlik bilgileri, Active Directory için etkinleştirilen yetenekler ile eşleşen en yüksek karşılık gelen hesap seçeneğine sahip olmalıdır.    

    Örneğin, Active Directory yalnızca AES-128 özelliğine sahipse, Kullanıcı kimlik bilgileri için AES-128 hesabı seçeneğini etkinleştirmeniz gerekir. Active Directory AES-256 özelliğine sahipse, AES-256 hesabı seçeneğini etkinleştirmeniz gerekir (Bu da AES-128 ' i destekler). Active Directory herhangi bir Kerberos şifreleme özelliği yoksa, Azure NetApp Files varsayılan olarak DES kullanır.  

    Active Directory Kullanıcıları ve Bilgisayarları Microsoft Yönetim Konsolu (MMC) ' nin özelliklerinde Hesap seçeneklerini etkinleştirebilirsiniz:   

    ![Active Directory Kullanıcıları ve bilgisayarları MMC](../media/azure-netapp-files/ad-users-computers-mmc.png)

* Azure NetApp Files, Azure NetApp Files hizmeti ve hedeflenen [Active Directory etki alanı denetleyicileri](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)arasında LDAP trafiğinin güvenli aktarımını sağlayan [LDAP imzalamayı](/troubleshoot/windows-server/identity/enable-ldap-signing-in-windows-server)destekler. LDAP imzalama için Microsoft Danışmanlık [ADV190023](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023) ' nin kılavuzunu takip ediyorsanız, [Birleştir Active Directory](#create-an-active-directory-connection) penceresindeki **LDAP imzalama** kutusunu işaretleyerek Azure NetApp Files ' de LDAP imzalama özelliğini etkinleştirmeniz gerekir. 

    [LDAP kanalı bağlama](https://support.microsoft.com/help/4034879/how-to-add-the-ldapenforcechannelbinding-registry-entry) yapılandırması tek başına Azure NetApp Files hizmeti üzerinde hiçbir etkiye sahip değildir. Ancak, hem LDAP kanalı bağlamayı hem de Güvenli LDAP 'yi (örneğin, LDAPS veya) kullanırsanız `start_tls` , SMB birimi oluşturma işlemi başarısız olur.

* AD dışı tümleşik DNS için, Azure NetApp Files bir "kolay ad" kullanarak çalışmasını sağlamak için bir DNS A/PTR kaydı eklemeniz gerekir. 

## <a name="decide-which-domain-services-to-use"></a>Hangi etki alanı hizmetlerinin kullanılacağını belirleyin 

Azure NetApp Files, AD bağlantıları için hem [Active Directory Domain Services](/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) (eklemeleri) hem Azure Active Directory Domain Services (aeklemeleri) destekler.  Bir AD bağlantısı oluşturmadan önce, ekleme veya AEKLEMESINE göre ' yi kullanıp kullanmayacağınızı belirlemeniz gerekir.  

Daha fazla bilgi için bkz. [otomatik olarak yönetilen Active Directory Domain Services, Azure Active Directory ve yönetilen Azure Active Directory Domain Services karşılaştırma](../active-directory-domain-services/compare-identity-solutions.md). 

### <a name="active-directory-domain-services"></a>Active Directory Domain Services

Azure NetApp Files için tercih edilen [Active Directory Siteleri ve Hizmetleri](/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) kapsamını kullanabilirsiniz. Bu seçenek, [Azure NetApp Files tarafından erişilebilen](azure-netapp-files-network-topologies.md)etki alanı denetleyicilerini ACTIVE DIRECTORY DOMAIN SERVICES (ekler) okuma ve yazma işlemleri sağlar. Ayrıca, hizmetin belirtilen Active Directory Siteleri ve Hizmetleri sitesinde olmayan etki alanı denetleyicileriyle iletişim kurmasını da engeller. 

EKLEME kullandığınızda sitenizin adını bulmak için, kuruluşunuzda Active Directory Domain Services sorumlu yönetim grubuyla iletişim sağlayabilirsiniz. Aşağıdaki örnekte, site adının görüntülendiği Active Directory Siteleri ve Hizmetleri eklentisi gösterilmektedir: 

![Active Directory Siteleri ve Hizmetleri](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-services.png)

Azure NetApp Files için bir AD bağlantısı yapılandırdığınızda, **ad site adı** alanı için kapsam içinde site adını belirtirsiniz.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 

Azure Active Directory Domain Services (AEKLEYEN) yapılandırma ve kılavuz için bkz. [Azure AD Domain Services belgeleri](../active-directory-domain-services/index.yml).

Azure NetApp Files için ek AEKLEYEN konular geçerlidir: 

* AEKLEMELERI 'nin dağıtıldığı VNet veya alt ağın Azure NetApp Files dağıtımıyla aynı Azure bölgesinde olduğundan emin olun.
* Azure NetApp Files dağıtıldığı bölgede başka bir VNet kullanırsanız, iki sanal ağ arasında bir eşleme oluşturmanız gerekir.
* Azure NetApp Files `user` ve `resource forest` türlerini destekler.
* Eşitleme türü için, `All` veya seçebilirsiniz `Scoped` .   
    Seçeneğini belirlerseniz `Scoped` , SMB paylaşımlarına erişmek için doğru Azure AD grubunun seçildiğinden emin olun.  Emin değilseniz, `All` eşitleme türünü kullanabilirsiniz.
* Enterprise veya Premium SKU kullanımı gereklidir. Standart SKU desteklenmiyor.

Bir Active Directory bağlantısı oluşturduğunuzda, AEKLEMELERI için aşağıdaki özellikleri aklınızda bulabilirsiniz:

* **BIRINCIL DNS**, **Ikincil DNS** ve **ad DNS etki alanı ADıNA** ilişkin bilgileri, aeklemeleri menüsünde bulabilirsiniz.  
DNS sunucuları için, Active Directory bağlantısını yapılandırmak için iki IP adresi kullanılacaktır. 
* **Kuruluş birimi yolu** `OU=AADDC Computers` .  
Bu ayar, **NetApp hesabı** altındaki **Active Directory bağlantılarında** yapılandırılır:

  ![Kuruluş birimi yolu](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* **Kullanıcı adı** kimlik bilgileri, Azure AD grubunun **Azure AD DC yöneticileri** üyesi olan herhangi bir kullanıcı olabilir.


## <a name="create-an-active-directory-connection"></a>Active Directory bağlantısı oluşturma

1. NetApp hesabınızdan **Active Directory bağlantılar**' a ve ardından **Birleştir**' e tıklayın.  

    ![Active Directory bağlantıları](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. Active Directory Birleştir penceresinde, kullanmak istediğiniz etki alanı hizmetlerine bağlı olarak aşağıdaki bilgileri sağlayın:  

    Kullandığınız etki alanı hizmetlerine özgü bilgiler için bkz. [hangi etki alanı hizmetlerinin kullanılacağını belirleyin](#decide-which-domain-services-to-use). 

    * **Birincil DNS**  
        Bu, Active Directory etki alanına ekleme ve SMB kimlik doğrulama işlemleri için gereken DNS 'dir. 
    * **İkincil DNS**   
        Bu, gereksiz ad hizmetleri sağlamak için ikincil DNS sunucusudur. 
    * **AD DNS etki alanı adı**  
        Bu, birleştirmek istediğiniz Active Directory Domain Services etki alanı adıdır.
    * **AD site adı**  
        Bu, etki alanı denetleyicisi bulmanın sınırlandırılacak site adıdır. Bu, Active Directory Siteleri ve Hizmetleri içindeki site adıyla eşleşmelidir.
    * **SMB sunucusu (bilgisayar hesabı) ön eki**  
        Bu, Azure NetApp Files yeni hesapların oluşturulması için kullanacağı Active Directory makine hesabının adlandırma ön ekidir.

        Örneğin, kuruluşunuzun dosya sunucuları için kullandığı adlandırma standardı, NAS-01, NAS-02..., NAS-045 ise, ön ek için "NAS" girersiniz. 

        Hizmet, gerektiğinde Active Directory daha fazla makine hesabı oluşturur.

        > [!IMPORTANT] 
        > Active Directory bağlantısını oluşturduktan sonra SMB sunucusu ön ekinin yeniden adlandırılması kesintiye uğramasıdır. SMB sunucu önekini yeniden adlandırdıktan sonra mevcut SMB paylaşımlarını yeniden bağlamanız gerekecektir.

    * **Kuruluş birimi yolu**  
        Bu, SMB sunucu makinesi hesaplarının oluşturulacağı kuruluş birimi (OU) için LDAP yoludur. Diğer bir deyişle, OU = ikinci düzey, OU = ilk düzey. 

        Azure Active Directory Domain Services ile Azure NetApp Files kullanıyorsanız, kuruluş birimi yolu, `OU=AADDC Computers` NetApp hesabınız için Active Directory yapılandırdığınızda olur.

        ![Active Directory Birleştir](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

    * **AES şifrelemesi**   
        Bir SMB birimi için AES şifrelemesini etkinleştirmek istiyorsanız bu onay kutusunu işaretleyin. Gereksinimler için [Active Directory bağlantılara yönelik gereksinimlere](#requirements-for-active-directory-connections) bakın. 

        ![Active Directory AES şifrelemesi](../media/azure-netapp-files/active-directory-aes-encryption.png)

        **AES şifreleme** özelliği şu anda önizlemededir. Bu özelliği ilk kez kullanıyorsanız, özelliği kullanmadan önce kaydedin: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```

        Özellik kaydının durumunu denetleyin: 

        > [!NOTE]
        > **Registrationstate** , ' a `Registering` değiştirilmeden önce 60 dakikaya kadar bir durumda olabilir `Registered` . Devam etmeden önce durum olana kadar bekleyin `Registered` .

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```
        
        Ayrıca, [Azure CLI komutlarını](/cli/azure/feature) kullanarak `az feature register` `az feature show` özelliği kaydedebilir ve kayıt durumunu görüntüleyebilirsiniz. 

    * **LDAP Imzalama**   
        LDAP imzalamayı etkinleştirmek için bu onay kutusunu seçin. Bu işlev, Azure NetApp Files hizmeti ve Kullanıcı tarafından belirtilen [Active Directory Domain Services etki alanı denetleyicileri](/windows/win32/ad/active-directory-domain-services)arasında güvenli LDAP aramaları sunar. Daha fazla bilgi için bkz. [ADV190023 | LDAP kanalı bağlamayı ve LDAP IMZALAMAYı etkinleştirmek için Microsoft Kılavuzu](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).  

        ![LDAP imzalamayı Active Directory](../media/azure-netapp-files/active-directory-ldap-signing.png) 

        **LDAP imzalama** özelliği şu anda önizlemededir. Bu özelliği ilk kez kullanıyorsanız, özelliği kullanmadan önce kaydedin: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```

        Özellik kaydının durumunu denetleyin: 

        > [!NOTE]
        > **Registrationstate** , ' a `Registering` değiştirilmeden önce 60 dakikaya kadar bir durumda olabilir `Registered` . Devam etmeden önce durum olana kadar bekleyin `Registered` .

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```
        
        Ayrıca, [Azure CLI komutlarını](/cli/azure/feature) kullanarak `az feature register` `az feature show` özelliği kaydedebilir ve kayıt durumunu görüntüleyebilirsiniz. 

     * **Güvenlik ayrıcalığı kullanıcıları**   <!-- SMB CA share feature -->   
        `SeSecurityPrivilege`Azure NetApp Files birimlerine erişmek için yükseltilmiş ayrıcalık gerektiren kullanıcılara güvenlik ayrıcalığı () verebilirsiniz. Belirtilen kullanıcı hesaplarının, varsayılan olarak etki alanı kullanıcılarına atanmamış güvenlik ayrıcalığı gerektiren Azure NetApp Files SMB paylaşımlarında belirli eylemler gerçekleştirmesine izin verilir.   

        Örneğin, belirli senaryolarda SQL Server yüklemek için kullanılan kullanıcı hesaplarına yükseltilmiş güvenlik ayrıcalığı verilmelidir. SQL Server yüklemek için yönetici olmayan (etki alanı) hesabı kullanıyorsanız ve hesaba atanmış güvenlik ayrıcalığı yoksa, hesaba güvenlik ayrıcalığı eklemeniz gerekir.  

        > [!IMPORTANT]
        > **Güvenlik ayrıcalıkları kullanıcıları** özelliğinin kullanılması için **[Azure NetApp Files SMB sürekli kullanılabilirlik paylaşımları genel önizleme eklenebileceğinizi gönderim sayfasından](https://aka.ms/anfsmbcasharespreviewsignup)** bir eklenebileceğinizi isteği göndermeniz gerekir. Bu özelliği kullanmadan önce Azure NetApp Files ekibinden bir resmi onay e-postası bekleyin.        
        > 
        > Bu özelliği kullanmak isteğe bağlıdır ve yalnızca SQL Server desteklenir. SQL Server yüklemek için kullanılan etki alanı hesabının, **güvenlik ayrıcalıkları kullanıcıları** alanına eklemeden önce mevcut olması gerekir. SQL Server yükleyicinin hesabını **güvenlik ayrıcalıkları kullanıcılarına** eklediğinizde, Azure NetApp Files hizmeti etki alanı denetleyicisiyle iletişim kurarak hesabı doğrulayabilir. Etki alanı denetleyicisiyle iletişim kurabilmesi durumunda komut başarısız olabilir.  

        Ve SQL Server hakkında daha fazla bilgi için `SeSecurityPrivilege` , [Kurulum hesabının belirli kullanıcı haklarına sahip olmaması durumunda SQL Server yükleme başarısız olur](/troubleshoot/sql/install/installation-fails-if-remove-user-right).

        ![Active Directory bağlantıları penceresinin güvenlik ayrıcalığı kullanıcıları kutusunu gösteren ekran görüntüsü.](../media/azure-netapp-files/security-privilege-users.png) 

     * **Yedekleme ilkesi kullanıcıları**  
        Azure NetApp Files ile kullanım için oluşturulan bilgisayar hesabına yükseltilmiş ayrıcalıklar gerektiren ek hesaplar ekleyebilirsiniz. Belirtilen hesapların dosya veya klasör düzeyinde NTFS izinlerini değiştirmesine izin verilir. Örneğin, Azure NetApp Files bir SMB dosya paylaşımında veri geçirmek için kullanılan ayrıcalıklı olmayan bir hizmet hesabı belirtebilirsiniz.  

        ![Active Directory yedekleme ilkesi kullanıcıları](../media/azure-netapp-files/active-directory-backup-policy-users.png)

        **Yedekleme ilkesi kullanıcıları** özelliği şu anda önizlemededir. Bu özelliği ilk kez kullanıyorsanız, özelliği kullanmadan önce kaydedin: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```

        Özellik kaydının durumunu denetleyin: 

        > [!NOTE]
        > **Registrationstate** , ' a `Registering` değiştirilmeden önce 60 dakikaya kadar bir durumda olabilir `Registered` . Devam etmeden önce durum olana kadar bekleyin `Registered` .

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```
        
        Ayrıca, [Azure CLI komutlarını](/cli/azure/feature) kullanarak `az feature register` `az feature show` özelliği kaydedebilir ve kayıt durumunu görüntüleyebilirsiniz. 

    * **Kullanıcı adınız** ve **Parolanız** dahil kimlik bilgileri

        ![Active Directory kimlik bilgileri](../media/azure-netapp-files/active-directory-credentials.png)

3. **Katıl**’a tıklayın.  

    Oluşturduğunuz Active Directory bağlantı görüntülenir.

    ![Active Directory bağlantıları oluşturuldu](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

## <a name="next-steps"></a>Sonraki adımlar  

* [SMB birimi oluşturma](azure-netapp-files-create-volumes-smb.md)
* [Çift protokollü birim oluşturma](create-volumes-dual-protocol.md)
* [NFSv4.1 Kerberos şifrelemesini yapılandırma](configure-kerberos-encryption.md)
* [Azure CLı kullanarak yeni bir Active Directory ormanı yüklemesi](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm) 
