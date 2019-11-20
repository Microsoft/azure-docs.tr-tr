---
title: Kurumsal Güvenlik Paketi kümelerini oluşturma, Yapılandırma-Azure
description: Azure HDInsight 'ta Kurumsal Güvenlik Paketi kümelerini oluşturma ve yapılandırma hakkında bilgi edinin
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 05/09/2019
ms.openlocfilehash: 6247a6b2eeeb421773400cc60d05696f973a1dff
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044681"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Azure HDInsight 'ta Kurumsal Güvenlik Paketi kümeleri oluşturma ve yapılandırma

Azure HDInsight için Kurumsal Güvenlik Paketi (ESP), Azure 'daki Apache Hadoop kümeleriniz için Active Directory tabanlı kimlik doğrulamasına, çok kullanıcılı desteğe ve rol tabanlı erişim denetimine erişmenizi sağlar. HDInsight ESP kümeleri, sıkı şirket güvenlik ilkelerine bağlı olan kuruluşların hassas verileri güvenli bir şekilde işlemesini sağlar.

Bu kılavuzda, ESP etkin bir Azure HDInsight kümesinin nasıl oluşturulacağı gösterilmektedir. Ayrıca, Active Directory ve etki alanı adı sistemi 'nin (DNS) etkinleştirildiği bir Windows IaaS VM 'sinin nasıl oluşturulacağını gösterir. Şirket içi kullanıcıların bir ESP etkin HDInsight kümesinde oturum açmasını sağlamak için gerekli kaynakları yapılandırmak üzere bu kılavuzu kullanın.

Oluşturduğunuz sunucu, *gerçek* şirket içi ortamınızın yerini alacak şekilde davranır. Bu ayarı, kurulum ve yapılandırma adımları için kullanacaksınız. Daha sonra, adımları kendi ortamınızda tekrarlamalısınız. 

Bu kılavuz Ayrıca, Azure Active Directory (Azure AD) ile parola karması eşitlemesini kullanarak bir karma kimlik ortamı oluşturmanıza yardımcı olur. Kılavuz, [HDInsight 'TA ESP kullanır](apache-domain-joined-architecture.md).

Bu işlemi kendi ortamınızda kullanmadan önce:
* Active Directory ve DNS 'yi ayarlayın.
* Azure AD 'yi etkinleştirin.
* Şirket içi kullanıcı hesaplarını Azure AD 'ye eşitleyin.

![Azure AD mimari diyagramı](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0002.png)

## <a name="create-an-on-premises-environment"></a>Şirket içi ortam oluşturma

Bu bölümde, yeni VM 'Ler oluşturmak, DNS yapılandırmak ve yeni bir Active Directory ormanı eklemek için bir Azure hızlı başlangıç dağıtım şablonu kullanacaksınız.

1. [Yeni bir Active Directory ormanı Ile Azure VM oluşturmak](https://azure.microsoft.com/resources/templates/active-directory-new-domain/)Için hızlı başlangıç Dağıtım şablonuna gidin.

1. **Azure 'A dağıt**' ı seçin.
1. Azure aboneliğinizde oturum açın.
1. Yeni bir **ad ormanı Ile Azure VM oluşturma** sayfasında:
    1. **Abonelik** açılan listesinden, kaynakları dağıtmak istediğiniz aboneliği seçin.
    1. **Kaynak grubu**' nun yanındaki **Yeni oluştur**' u seçin ve *onpremadvrg*adını girin.
    1. Şablon alanlarının geri kalanı için aşağıdaki ayrıntıları girin:

        * **Konum**: Orta ABD
        * **Yönetici Kullanıcı adı**: HDIFabrikamAdmin
        * **Yönetici parolası**: \<YOUR_PASSWORD >
        * **Etki alanı adı**: HDIFabrikam.com
        * **DNS ön eki**: hdıfabrikam

        ![Yeni Azure AD Ormanı ile Azure VM oluşturma şablonu](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

    1. **Satın al**'ı seçin.
    1. Dağıtımı izleyin ve bitmesini bekleyin.
    1. Kaynakların, **Onpremadvrg**doğru kaynak grubu altında oluşturulduğundan emin olun.

## <a name="configure-users-and-groups-for-cluster-access"></a>Küme erişimi için kullanıcıları ve grupları yapılandırma

Bu bölümde, bu kılavuzun sonuna kadar HDInsight kümesine erişimi olacak kullanıcıları oluşturacaksınız.

1. Uzak Masaüstü kullanarak etki alanı denetleyicisine bağlanın.
    1. Başında bahsedilen şablonu kullandıysanız, etki alanı denetleyicisi **Onpremadvrg** kaynak grubunda **advm** adlı bir sanal makine olur.
    1. Azure portal **kaynak grupları** > **Onpremadvrg** > **Advm** > **Connect**' i seçin.
    1. RDP **dosyasını indirmek**> **RDP** sekmesini seçin.
    1. Dosyayı bilgisayarınıza kaydedin ve açın.
    1. Kimlik bilgileri istendiğinde, Kullanıcı adı olarak *HDIFabrikam\HDIFabrikamAdmin* kullanın. Ardından, yönetici hesabı için seçtiğiniz parolayı girin.

1. Uzak Masaüstü oturumunuz etki alanı denetleyicisi VM 'sinde açıldığında, **Sunucu Yöneticisi** panosunda, **Active Directory Kullanıcıları ve bilgisayarları**' nı açın. Sağ üst köşedeki **araçlar** > **Active Directory Kullanıcılar ve bilgisayarlar**' ı seçin.

    ![Sunucu Yöneticisi panosunda Active Directory Yönetimi 'ni açın](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. İki yeni kullanıcı oluşturun: **Hdiadmin** ve **hdiuser**. Bu iki Kullanıcı HDInsight kümelerinde oturum açacaktır.

    1. **Active Directory Kullanıcıları ve bilgisayarları** sayfasında, **eylem** > **Yeni** > **Kullanıcı**' yı seçin.

        ![Yeni bir Active Directory kullanıcı oluşturun](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png)

    1. **Yeni nesne-Kullanıcı** sayfasında, **Kullanıcı oturum açma adı**için *hdiuser*girin. Sonra **İleri**’yi seçin.

        ![İlk yönetici kullanıcı nesnesini oluşturma](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png)

    1. Görüntülenen açılır pencerede, yeni hesap için bir parola girin. **Parola seçme hiçbir zaman dolmaz** > **Tamam**.
    1. Yeni hesabı oluşturmak için **son** ' u seçin.
    1. *Hdiadmin*adlı başka bir kullanıcı oluşturun.

        ![İkinci Yönetici Kullanıcı nesnesi oluşturma](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png)

1. **Active Directory Kullanıcıları ve bilgisayarları** sayfasında, **eylem** > **Yeni** > **grubu**' nu seçin. *HDIUserGroup*adlı bir grup ekleyin.

    ![Yeni bir Active Directory grubu oluştur](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png)

    ![Yeni bir nesne oluşturun](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png)

1. Grup üyesi olarak **HDIUserGroup** 'e **hdiuser** ekleyin.

    1. **HDIUserGroup** öğesine sağ tıklayın ve **Özellikler**' i seçin.
    1. **Üyeler** sekmesinde **Ekle**' yi seçin.
    1. **Seçilecek nesne adlarını girin** kutusuna *hdiuser*girin. Sonra **Tamam**’ı seçin.
    1. **Hdıadmin** hesabı için önceki adımları tekrarlayın.

        ![Üye HDIUser HDIUserGroup grubuna ekleme](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

Artık Active Directory ortamınızı oluşturdunuz. HDInsight kümesine erişebilen iki Kullanıcı ve bir Kullanıcı grubu eklediniz.

Kullanıcılar Azure AD ile eşitlenir.

### <a name="create-an-azure-ad-directory"></a>Azure AD dizini oluşturma

1. Azure Portal’da oturum açın.
1. **Kaynak oluştur** ve *Dizin*yaz ' ı seçin. **Azure Active Directory** > **Oluştur**' u seçin.
1. **Kuruluş adı**altında *hdıfabrikam*yazın.
1. **İlk etki alanı adı**altında *HDIFabrikamoutlook*girin.
1. **Oluştur**'u seçin.

    ![Azure AD dizini oluşturma](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

1. Azure portal sol tarafında **Azure Active Directory**' i seçin.
1. Gerekirse, yeni **HDIFabrikamoutlook** dizinine geçmek için **dizini** Değiştir ' i seçin.
1. **Yönet**altında **özel etki alanı adları**' nı seçin  > **özel etki alanı ekleyin**.
1. **Özel etki alanı adı**altında *HDIFabrikam.com* girin ve **etki alanı Ekle**' yi seçin.

![Özel etki alanı oluşturma](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

## <a name="configure-your-azure-ad-tenant"></a>Azure AD kiracınızı yapılandırma

Artık Azure AD kiracınızı, şirket içi Active Directory örneğindeki kullanıcıları ve grupları buluta eşitleyebilmeniz için yapılandıracaksınız.

1. Active Directory kiracı yöneticisi oluşturun.
    1. Azure portal oturum açın ve Azure AD kiracınızı, **Hdifabrikam**' ı seçin.
    1. **Yönet**altında **Kullanıcılar** > **Yeni Kullanıcı**' yı seçin.
    1. Yeni Kullanıcı için aşağıdaki ayrıntıları girin:
        * **Ad**: fabrikamazureadmin
        * **Kullanıcı adı**: fabrikamazureadmin@hdifabrikam.com
        * **Parola**: tercih ettiğiniz güvenli bir parola

    1. **Gruplar** bölümünde **AAD DC yöneticileri** ' ni arayın ve **Seç**' e tıklayın.

        ![Azure AD grupları iletişim kutusu](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0038.png)

    1. **Dizin rolü** bölümünü açın ve sağ tarafta, **genel yönetici** > **Tamam**' ı seçin.

        ![Azure AD rolü iletişim kutusu](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0040.png)

    1. Kullanıcı için bir parola girin. Ardından **Oluştur**’u seçin.

1. Yeni oluşturulan kullanıcı \<fabrikamazureadmin@hdifabrikam.com> parolasını değiştirmek istiyorsanız, kimliği kullanarak Azure portal oturum açın. Parolayı değiştirmeniz istenir.

## <a name="sync-on-premises-users-to-azure-ad"></a>Şirket içi kullanıcıları Azure AD ile eşitleme

### <a name="download-and-install-azure-ad-connect"></a>Azure AD Connect indir ve yükle

1. [Azure AD Connect indirin](https://www.microsoft.com/download/details.aspx?id=47594).

1. Azure AD Connect etki alanı denetleyicisine yükler.

    1. İndirdiğiniz yürütülebilir dosyayı açın ve lisans koşullarını kabul edin. **Devam**'ı seçin.

        !["Azure AD Connect 'e hoş geldiniz" sayfası](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0052.png)

    1. **Hızlı ayarları kullan** ' ı seçin ve yüklemeyi doldurun.

        ![Azure AD Connect Express ayarları](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0054.png)

### <a name="configure-a-sync-with-the-on-premises-domain-controller"></a>Şirket içi etki alanı denetleyicisiyle eşitleme yapılandırma

1. **Azure AD 'ye Bağlan** sayfasında, Azure AD için genel yöneticinin kullanıcı adını ve parolasını girin. Active Directory kiracınızı yapılandırdığınızda oluşturduğunuz `fabrikamazureadmin@hdifabrikam.com` Kullanıcı adını kullanın. Sonra **İleri**’yi seçin. 

    !["Azure AD 'ye bağlanma" sayfası](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png)

1. **Active Directory Domain Services Bağlan** sayfasında, bir kurumsal yönetici hesabının kullanıcı adını ve parolasını girin. Daha önce oluşturduğunuz Kullanıcı adını `HDIFabrikam\HDIFabrikamAdmin` ve parolasını kullanın. Sonra **İleri**’yi seçin. 

   !["Azure AD 'ye bağlanma" sayfası](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png)
1. **Azure AD oturum açma yapılandırması** sayfasında **İleri**' yi seçin.
   "Azure AD oturum açma yapılandırması" sayfasını ![](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png)

1. **Yapılandırmaya hazırlanma** sayfasında, **yüklensin**' i seçin.

   !["Yapılandırmaya hazırlanma" sayfası](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png)

1. **Yapılandırma Tamam** sayfasında **Çıkış**' ı seçin.
   "Yapılandırma Tamam" sayfasını ![](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png)

1. Eşitleme tamamlandıktan sonra, IaaS dizininde oluşturduğunuz kullanıcıların Azure AD ile eşitlendiğinden emin olun.
   1. Azure Portal’da oturum açın.
   1. **Azure Active Directory** > **hdifabrikam** > **Kullanıcı**seçin.

### <a name="create-a-user-assigned-managed-identity"></a>Kullanıcı tarafından atanan yönetilen kimlik oluşturma

Azure AD Domain Services yapılandırmak için kullanabileceğiniz, Kullanıcı tarafından atanan bir yönetilen kimlik oluşturun (Azure AD DS). Daha fazla bilgi için, bkz. [Azure Portal kullanarak Kullanıcı tarafından atanan yönetilen kimliğe rol oluşturma, listeleme, silme veya atama](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Azure Portal’da oturum açın.
1. **Kaynak oluştur** ' u seçin ve *yönetilen kimlik*yazın. **Kullanıcı tarafından atanan yönetilen kimlik** > **Oluştur**' u seçin.
1. **Kaynak adı**için *HDIFabrikamManagedIdentity*girin.
1. Aboneliğinizi seçin.
1. **Kaynak grubu**altında, **Yeni oluştur** ' u seçin ve *Hdifabrikam-merkezileştirmişus*girin.
1. **Konum**altında **Orta ABD**' yi seçin.
1. **Oluştur**'u seçin.

![Kullanıcı tarafından atanan yeni bir yönetilen kimlik oluşturma](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png)

### <a name="enable-azure-ad-ds"></a>Azure AD DS etkinleştirme

Azure AD DS 'yi etkinleştirmek için bu adımları izleyin. Daha fazla bilgi için bkz. [Azure Portal kullanarak Azure AD DS etkinleştirme](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

1. Azure AD DS barındırmak için bir sanal ağ oluşturun. Aşağıdaki PowerShell kodunu çalıştırın.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Set-AzContext -Subscription 'SUBSCRIPTION_ID'
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Azure Portal’da oturum açın.
1. **Kaynak oluştur**' u seçin, *etki alanı hizmetleri*girin ve **Azure AD Domain Services**' ı seçin.
1. **Temel bilgiler** sayfasında:
    1. **Dizin adı**altında, oluşturduğunuz Azure AD dizinini seçin: **hdıfabrikam**.
    1. **DNS etki alanı adı**için *HDIFabrikam.com*girin.
    1. Aboneliğinizi seçin.
    1. **Hdifabrikam-merkezde ABD**kaynak grubunu belirtin. **Konum**için **Orta ABD**' yi seçin.

        ![Azure AD DS temel ayrıntıları](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0084.png)

1. **Ağ** sayfasında, PowerShell betiği kullanılarak oluşturduğunuz ağı (**HDıFABRIKAM-VNET**) ve alt ağı (**aeklemesine-subnet**) seçin. Veya Şimdi bir sanal ağ oluşturmak için **Yeni oluştur** ' a tıklayın.

    !["Sanal ağ oluştur" adımı](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png)

1. **Yönetici grubu** sayfasında, bu grubu yönetmek IÇIN **AAD DC yöneticileri** adlı bir grubun zaten oluşturulduğunu belirten bir bildirim görmeniz gerekir. İsterseniz bu grubun üyeliğini değiştirebilirsiniz, ancak bu durumda bunu değiştirmeniz gerekmez. **Tamam**’ı seçin.

    ![Azure AD yönetici grubunu görüntüleme](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png)

1. **Eşitleme** sayfasında, **Tüm** > **Tamam**' ı seçerek tam eşitlemeyi etkinleştirin.

    ![Azure AD DS eşitlemesini etkinleştirme](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png)

1. **Özet** sayfasında, Azure AD DS ayrıntılarını doğrulayın ve **Tamam**' ı seçin.

    !["Etkinleştir Azure AD Domain Services" Özeti](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png)

Azure AD DS etkinleştirdikten sonra, Azure AD VM 'lerinde yerel bir DNS sunucusu çalışır.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Azure AD DS Sanal ağınızı yapılandırma

Azure AD DS Sanal ağınızı (**Hdıfabrikam-AADDSVNET**) özel DNS sunucularınızı kullanacak şekilde yapılandırmak için aşağıdaki adımları kullanın.

1. Özel DNS sunucularınızın IP adreslerini bulun. 
    1. **HDIFabrikam.com** Azure AD DS kaynağını seçin. 
    1. **Yönet**altında **Özellikler**' i seçin. 
    1. **Sanal ağdaki IP adresi**altındaki IP adreslerini bulun.

    ![Azure AD DS için özel DNS IP adreslerini bulma](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0096.png)

1. **Hdifabrikam-AADDSVNET** öğesini 10.0.0.4 ve 10.0.0.5 özel IP adreslerini kullanacak şekilde yapılandırın.

    1. **Ayarlar**altında, **DNS sunucuları**' nı seçin. 
    1. **Özel**' i seçin.
    1. Metin kutusuna, ilk IP adresini (*10.0.0.4*) girin.
    1. **Kaydet**’i seçin.
    1. Diğer IP adresini (*10.0.0.5*) eklemek için adımları yineleyin.

Senaryolarımızda Azure AD DS 10.0.0.4 ve 10.0.0.5 IP adreslerini kullanacak şekilde yapılandırdık ve Azure AD DS sanal ağında aynı IP adresini ayarlıyoruz:

![Özel DNS sunucuları sayfası](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0098.png)

## <a name="securing-ldap-traffic"></a>LDAP trafiğinin güvenliğini sağlama

Azure Active Directory okumak ve yazmak için hafif Dizin Erişim Protokolü (LDAP) kullanılır. LDAP trafiğini Güvenli Yuva Katmanı (SSL) veya Aktarım Katmanı Güvenliği (TLS) teknolojisini kullanarak gizli ve güvenli hale getirebilirsiniz. Düzgün şekilde biçimlendirilen bir sertifika yükleyerek SSL üzerinden LDAP 'yi (LDAPS) etkinleştirebilirsiniz.

Güvenli LDAP hakkında daha fazla bilgi için bkz. [Azure AD DS yönetilen etki alanı IÇIN LDAPS yapılandırma](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap).

Bu bölümde, kendinden imzalı bir sertifika oluşturur, sertifikayı indirebilir ve LDAPS 'yi **Hdifabrikam** Azure AD DS yönetilen etki alanı için yapılandırırsınız.

Aşağıdaki betik, **Hdıfabrikam**için bir sertifika oluşturur. Sertifika, *LocalMachine* yolunda kaydedilir.

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

> [!NOTE] 
> SSL sertifika isteğini oluşturmak için geçerli bir ortak anahtar şifreleme standartları (PKCS) \#10 isteği oluşturan herhangi bir yardımcı program veya uygulama kullanılabilir.

Sertifikanın bilgisayarın **Kişisel** deposunda yüklü olduğunu doğrulayın:

1. Microsoft Yönetim Konsolu 'Nu (MMC) başlatın.
1. Yerel bilgisayardaki sertifikaları yöneten **Sertifikalar** ek bileşenini ekleyin.
1. **Sertifikalar (yerel bilgisayar)**  > **Kişisel** > **sertifikaları**' nı genişletin. **Kişisel** depoda yeni bir sertifika bulunmalıdır. Bu sertifika, tam ana bilgisayar adına verilir.

    ![Yerel sertifika oluşturmayı doğrulama](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png)

1. Sağ taraftaki bölmede, oluşturduğunuz sertifikaya sağ tıklayın. **Tüm görevler**' in üzerine gelin ve ardından **dışarı aktar**' ı seçin.

1. **Özel anahtarı dışarı aktar** sayfasında **Evet, özel anahtarı dışarı aktar**' ı seçin. Anahtarın içeri aktarılacağı bilgisayarın şifrelenmiş iletileri okuması için özel anahtara ihtiyacı vardır.

    ![Sertifika Dışarı Aktarma Sihirbazı 'nın özel anahtarı dışarı aktarma sayfası](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png)

1. **Dışarı aktarma dosyası biçimi** sayfasında, varsayılan ayarları bırakın ve ardından **İleri**' yi seçin. 
1. **Parola** sayfasında, özel anahtar için bir parola yazın. **Şifreleme**Için **TripleDES-SHA1**' ı seçin. Sonra **İleri**’yi seçin.
1. **Dışarı aktarılacak dosya** sayfasında, dışarı aktarılmış sertifika dosyasının yolunu ve adını yazın ve ardından **İleri**' yi seçin. Dosya adının. pfx uzantısı olmalıdır. Bu dosya, güvenli bir bağlantı kurmak için Azure portal yapılandırılır.
1. Azure AD DS yönetilen bir etki alanı için LDAPS 'yi etkinleştirin.
    1. Azure portal, **HDIFabrikam.com**etki alanını seçin.
    1. **Yönet**altında **Güvenli LDAP**' yi seçin.
    1. **Güvenli LDAP** sayfasında, **Güvenli LDAP**altında **Etkinleştir**' i seçin.
    1. Bilgisayarınıza verdiğiniz. pfx sertifika dosyasına gözatamazsınız.
    1. Sertifika parolasını girin.

    ![Güvenli LDAP 'yi etkinleştirme](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png)

1. LDAPS 'yi etkinleştirmiş olduğunuza göre, bağlantı noktası 636 ' i etkinleştirerek ulaşılabilir olduğundan emin olun.
    1. **Hdifabrikam-merkezde ABD** kaynak grubunda ağ güvenlik grubu ' nu seçin **aeklemeleri-HDIFabrikam.com-NSG**.
    1. **Ayarlar**altında, **gelen güvenlik kuralları** > **Ekle**' yi seçin.
    1. **Gelen güvenlik kuralı ekle** sayfasında, aşağıdaki özellikleri girin ve **Ekle**' yi seçin:

        | Özellik | Değer |
        |---|---|
        | Kaynak | Herhangi biri |
        | Kaynak bağlantı noktası aralıkları | * |
        | Hedef | Herhangi biri |
        | Hedef bağlantı noktası aralığı | 636 |
        | Protokol | Herhangi biri |
        | Eylem | Allow |
        | Öncelik | \< Istenen sayı > |
        | Adı | Port_LDAP_636 |

    !["Gelen güvenlik kuralı ekle" iletişim kutusu](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

**HDIFabrikamManagedIdentity** , Kullanıcı tarafından atanan yönetilen kimliktir. HDInsight etki alanı Hizmetleri katılımcısı rolü, bu kimliğin etki alanı Hizmetleri işlemlerini okumasına, oluşturmasına, değiştirmesine ve silmesine imkan tanıyan yönetilen kimlik için etkinleştirilmiştir.

![Kullanıcı tarafından atanan yönetilen kimlik oluşturma](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png)

## <a name="create-an-esp-enabled-hdinsight-cluster"></a>ESP etkin HDInsight kümesi oluşturma

Bu adım aşağıdaki önkoşulları gerektirir:

1. **Batı ABD**konum Içinde *hdifabrikam-WestUS* adlı yeni bir kaynak grubu oluşturun.
1. ESP etkin HDInsight kümesini barındıracak bir sanal ağ oluşturun.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Azure AD DS (`HDIFabrikam-AADDSVNET`) ve ESP özellikli HDInsight kümesini barındıracak sanal ağı (`HDIFabrikam-HDIVNet`) barındıran sanal ağ arasında bir eşdüzey ilişki oluşturun. İki sanal ağı eşler için aşağıdaki PowerShell kodunu kullanın.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. **Hdigen2store**adlı yeni bir Azure Data Lake Storage 2. hesabı oluşturun. Hesabı kullanıcı tarafından yönetilen **HDIFabrikamManagedIdentity**kimliğiyle yapılandırın. Daha fazla bilgi için bkz. [Azure HDInsight kümeleriyle Azure Data Lake Storage 2. kullanma](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

1. **Hdifabrikam-AADDSVNET** sanal AĞıNDA özel DNS ayarlayın.
    1. Azure portal > **kaynak gruplarına**gidin  > **Onpremadvrg** > **HDIFABRIKAM-aaddsvnet** > **DNS sunucuları**.
    1. **Özel** ' i seçin ve *10.0.0.4* ve *10.0.0.5*girin.
    1. **Kaydet**’i seçin.

        ![Bir sanal ağ için özel DNS ayarlarını kaydet](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png)

1. Yeni bir ESP etkin HDInsight Spark kümesi oluşturun.
    1. **Özel (boyut, ayarlar, uygulamalar) öğesini**seçin.
    2. **Temel bilgiler** için ayrıntıları girin (Bölüm 1). **Küme türünün** **Spark 2,3 (HDI 3,6)** olduğundan emin olun. **Kaynak grubunun** **hdifabrikam-merkezde US**olduğundan emin olun.

    1. **Güvenlik + ağ** (Bölüm 2) için aşağıdaki ayrıntıları girin:
        * **Kurumsal güvenlik paketi**altında **etkin**' i seçin.
        * **Küme Yönetici kullanıcısı** ' nı seçin ve şirket içi yönetici kullanıcı olarak oluşturduğunuz **hdıadmin** hesabını seçin. **Seç**'e tıklayın.
        * **HDIUserGroup** > **küme erişim grubunu** seçin. Gelecekte bu gruba eklediğiniz tüm kullanıcılar HDInsight kümelerine erişebilecektir.

            ![Küme erişim grubunu seçin HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg)

    1. Küme yapılandırmasının diğer adımlarını tamamlayıp **küme özetinin**ayrıntılarını doğrulayın. **Oluştur**'u seçin.

1. Yeni oluşturulan küme için `https://CLUSTERNAME.azurehdinsight.net`konumundaki ambarı Kullanıcı arabiriminde oturum açın. Yönetici Kullanıcı adınızı `hdiadmin@hdifabrikam.com` ve parolasını kullanın.

    ![Apache ambarı Kullanıcı arabirimi oturum açma penceresi](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg)

1. Küme panosunda **Roller**' i seçin.
1. **Roller** sayfasında, **bunlara rol ata**altında, **Küme Yöneticisi** rolünün yanındaki *hdiusergroup*grubunu girin. 

    ![Hdiusergroup 'e Küme Yöneticisi rolünü atama](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg)

1. Secure Shell (SSH) istemcinizi açın ve kümede oturum açın. Şirket içi Active Directory örneğinde oluşturduğunuz **hdiuser** 'ı kullanın.

    ![SSH istemcisini kullanarak kümede oturum açma](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg)

Bu hesapla oturum açarsanız, şirketinizin şirket içi Active Directory örneğiyle eşitlenmesi için ESP kümenizi doğru şekilde yapılandırdınız.

## <a name="next-steps"></a>Sonraki adımlar

[ESP ile güvenliği Apache Hadoop Için bir giriş](hdinsight-security-overview.md)okuyun.
