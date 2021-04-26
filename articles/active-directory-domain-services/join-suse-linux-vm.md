---
title: Azure AD Domain Services için bir SLE VM 'ye katılarak | Microsoft Docs
description: Bir SUSE Linux Enterprise sanal makinesini Azure AD Domain Services yönetilen bir etki alanına nasıl yapılandıracağınızı ve katılacağınızı öğrenin.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 08/12/2020
ms.author: justinha
ms.openlocfilehash: f2f421d95dfc376aed373c718198db33a870d9dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619615"
---
# <a name="join-a-suse-linux-enterprise-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>SUSE Linux Enterprise sanal makinesini Azure Active Directory Domain Services yönetilen bir etki alanına katma

Kullanıcıların Azure 'da tek bir kimlik bilgileri kümesi kullanarak sanal makinelerde (VM) oturum açmalarına izin vermek için VM 'Leri Azure Active Directory Domain Services (Azure AD DS) yönetilen bir etki alanına katabilirsiniz. Bir VM 'yi Azure AD DS yönetilen bir etki alanına katdığınızda, etki alanındaki Kullanıcı hesapları ve kimlik bilgileri, sunucuları oturum açmak ve yönetmek için kullanılabilir. Yönetilen etki alanındaki grup üyelikleri, VM 'deki dosyalara veya hizmetlere erişimi denetlemenize olanak sağlamak için de uygulanır.

Bu makalede, bir SUSE Linux Enterprise (SLE) sanal makinesini yönetilen bir etki alanına nasıl katılabilmeniz gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için aşağıdaki kaynaklar ve ayrıcalıklar gereklidir:

* Etkin bir Azure aboneliği.
    * Azure aboneliğiniz yoksa [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Abonelikle ilişkili bir Azure Active Directory kiracısı, şirket içi bir dizinle veya yalnızca bulut diziniyle eşitlenir.
    * Gerekirse, [bir Azure Active Directory kiracı oluşturun][create-azure-ad-tenant] veya [bir Azure aboneliğini hesabınızla ilişkilendirin][associate-azure-ad-tenant].
* Azure AD kiracınızda etkinleştirilmiş ve yapılandırılmış Azure Active Directory Domain Services yönetilen bir etki alanı.
    * Gerekirse, ilk öğretici [Azure Active Directory Domain Services yönetilen bir etki alanı oluşturur ve yapılandırır][create-azure-ad-ds-instance].
* Yönetilen etki alanının bir parçası olan bir kullanıcı hesabı.

## <a name="create-and-connect-to-a-sle-linux-vm"></a>Bir SLE Linux VM 'si oluşturun ve bu VM 'ye bağlanın

Azure 'da var olan bir SLE Linux sanal makinesi varsa, SSH kullanarak buna bağlanın ve sonra [VM 'yi yapılandırmaya başlamak](#configure-the-hosts-file)için sonraki adıma geçin.

Bir SLE Linux sanal makinesi oluşturmanız veya bu makaleyle kullanmak üzere bir test sanal makinesi oluşturmak istiyorsanız aşağıdaki yöntemlerden birini kullanabilirsiniz:

* [Azure portalı](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

VM 'yi oluştururken sanal ağ ayarlarına dikkat ederek VM 'nin yönetilen etki alanıyla iletişim kurabildiğinden emin olun:

* Sanal makineyi aynı veya Azure AD Domain Services etkinleştirdiğiniz bir eşlenen sanal ağa dağıtın.
* VM 'yi Azure AD Domain Services yönetilen etki alanından farklı bir alt ağa dağıtın.

VM dağıtıldıktan sonra, SSH kullanarak VM 'ye bağlanma adımlarını izleyin.

## <a name="configure-the-hosts-file"></a>Hosts dosyasını yapılandırma

VM ana bilgisayar adının yönetilen etki alanı için doğru yapılandırıldığından emin olmak için, */etc/hosts* dosyasını düzenleyin ve ana bilgisayar adını ayarlayın:

```console
sudo vi /etc/hosts
```

*Konaklar* dosyasında, *localhost* adresini güncelleştirin. Aşağıdaki örnekte:

* *aaddscontoso.com* , yönetilen etkı alanının DNS etki alanı adıdır.
* *Linux-q2gr* , yönetilen etki alanına katıldığınız SLE sanal makinenizin ana bilgisayar adıdır.

Bu adları kendi değerlerinizle güncelleştirin:

```console
127.0.0.1 linux-q2gr linux-q2gr.aaddscontoso.com
```

İşiniz bittiğinde, düzenleyicinin komutunu kullanarak *konaklar* dosyasını kaydedin ve kapatın `:wq` .

## <a name="join-vm-to-the-managed-domain-using-sssd"></a>SSSD kullanarak VM 'yi yönetilen etki alanına katma

**Sssd** kullanarak yönetilen etki alanına ve yast *Kullanıcı oturum açma yönetim* modülünü birleştirmek için aşağıdaki adımları izleyin:

1. *Kullanıcı oturum açma yönetimi* yast modülünü yükler:

    ```bash
    sudo zypper install yast2-auth-client
    ```

1. YaST 'yi açın.

1. DNS otomatik bulma 'yı daha sonra başarıyla kullanmak için, yönetilen etki alanı IP adreslerini ( *Active Directory sunucusu*) istemciniz için ad sunucusu olarak yapılandırın.

    YaST 'de, **sistem > ağ ayarları**' nı seçin.

1. *Ana bilgisayar adı/DNS* sekmesini seçin ve ardından yönetilen etkı alanının IP adreslerini metin kutusu *adı sunucu 1*' e girin. Bu IP adresleri, *10.0.2.4* ve *10.0.2.5* gibi yönetilen etki alanınız için Azure Portal *Özellikler* penceresinde gösterilir.

    Kendi yönetilen etki alanı IP adreslerinizi ekleyin ve ardından **Tamam**' ı seçin.

1. Yast ana penceresinde *Ağ Hizmetleri*  >  *Kullanıcı oturum açma yönetimi*' ni seçin.

    Modül, aşağıdaki örnek ekran görüntüsünde gösterildiği gibi, bilgisayarınızın farklı ağ özelliklerini ve şu anda kullanılmakta olan kimlik doğrulama yöntemini gösteren bir genel bakış ile açılır:

    ![YaST 'de Kullanıcı oturum açma Yönetimi penceresinin örnek ekran görüntüsü](./media/join-suse-linux-vm/overview-window.png)

    Düzenle 'yi başlatmak için **Ayarları Değiştir**' i seçin.

VM 'yi yönetilen etki alanına katmak için aşağıdaki adımları izleyin:

1. İletişim kutusunda **etki alanı Ekle**' yi seçin.

1. *Aaddscontoso.com* gibi doğru *etki alanı adını* belirtin ve kimlik verileri ve kimlik doğrulaması için kullanılacak hizmetleri belirtin. Her ikisi için *Microsoft Active Directory* seçin.

    *Etki alanını etkinleştir* seçeneğinin seçili olduğundan emin olun.

1. Hazırlık sırasında **Tamam**' ı seçin.

1. Aşağıdaki iletişim kutusunda varsayılan ayarları kabul edin ve ardından **Tamam**' ı seçin.

1. VM gerektiğinde ek yazılım yüklüyor ve ardından yönetilen etki alanının kullanılabilir olup olmadığını denetler.

    Her şey doğruysa, VM 'nin yönetilen etki alanını bulduğunu ancak *Henüz kaydolmadıysanız* olduğunu göstermek için aşağıdaki örnek iletişim kutusu gösterilir.

    ![YaST 'de Active Directory kayıt penceresinin örnek ekran görüntüsü](./media/join-suse-linux-vm/enroll-window.png)

1. İletişim kutusunda, yönetilen etki alanının bir parçası olan bir *kullanıcının Kullanıcı adını ve* *parolasını* belirtin. Gerekirse, [Azure AD 'de bir gruba bir kullanıcı hesabı ekleyin](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Geçerli etki alanının Samba için etkinleştirildiğinden emin olmak için, *Bu ad ile çalışmak üzere Samba yapılandırmasının üzerine yazmayı* etkinleştirin.

1. Kaydolmak için **Tamam**' ı seçin.

1. Başarıyla kaydolduğunu doğrulamak için bir ileti gösterilir. Son olarak **Tamam**' ı seçin.

VM, yönetilen etki alanına kaydedildikten sonra aşağıdaki örnekte gösterildiği gibi, *etki alanı kullanıcı oturum açma bilgilerini yönetme* seçeneğini kullanarak istemciyi yapılandırın:

![YaST 'de etki alanı kullanıcı oturumu yönetme penceresinin örnek ekran görüntüsü](./media/join-suse-linux-vm/manage-domain-user-logon-window.png)

1. Yönetilen etki alanı tarafından belirtilen verileri kullanarak oturum açma işlemleri için, *etki alanına Izin ver Kullanıcı oturum açmaya izin* ver kutusunu işaretleyin.

1. İsteğe bağlı olarak, *etki alanı veri kaynağını etkinleştir* altında ortamınız için gereken ek veri kaynaklarını kontrol edin. Bu seçenekler, hangi kullanıcıların **sudo** kullanmasına izin verileceğini veya hangi ağ sürücülerinin kullanılabilir olduğunu içerir.

1. Yönetilen etki alanındaki kullanıcıların VM 'de giriş dizinlerine sahip olmasını sağlamak için *giriş dizinleri oluşturma* kutusunu işaretleyin.

1. Yan çubukta, **hizmet seçenekleri › ad anahtarı**' nı seçin ve *genişletilmiş seçenekler*' e tıklayın. Bu pencereden *fallback_homedir* veya *override_homedir* seçin, sonra **Ekle**' yi seçin.

1. Ana Dizin konumu için bir değer belirtin. Giriş dizinlerinin */Home/user_name* biçimini izlemesi için */Home/%u* kullanın. Olası değişkenler hakkında daha fazla bilgi için bkz. sssd. conf man sayfası ( `man 5 sssd.conf` ), bölüm *override_homedir*.

1. **Tamam**’ı seçin.

1. Değişiklikleri kaydetmek için **Tamam**’ı seçin. Ardından, şimdi görüntülenen değerlerin doğru olduğundan emin olun. İletişim kutusundan çıkmak için **iptal**' i seçin.

1. SSSD ve winbind 'yi aynı anda çalıştırmayı düşünüyorsanız (SSSD aracılığıyla birleştirme sırasında, ancak Samba dosya sunucusunu çalıştırırken),, SMB. conf dosyasındaki Samba seçeneği *Kerberos yöntemi* *gizli dizi ve keytab* olarak ayarlanmalıdır. Sssd seçeneği *ad_update_samba_machine_account_password* sssd. conf içinde de *true* olarak ayarlanmalıdır. Bu seçenekler, sistem keytab 'ın eşitlenmemiş olmasını engeller.

## <a name="join-vm-to-the-managed-domain-using-winbind"></a>Winbind kullanarak VM 'yi yönetilen etki alanına katma

**Winbind** ve *Windows etki alanı üyeliği* modülü yast kullanarak yönetilen etki alanına katmak için aşağıdaki adımları izleyin:

1. YaST 'de **Windows etki alanı üyeliği > ağ hizmetleri**' ni seçin.

1. *Windows etki alanı üyeliği* ekranındaki *etki alanı veya çalışma grubuna* katılacak etki alanını girin. *Aaddscontoso.com* gibi yönetilen etki alanı adını girin.

    ![YaST 'de Windows etki alanı üyeliği penceresinin örnek ekran görüntüsü](./media/join-suse-linux-vm/samba-client-window.png)

1. Linux kimlik doğrulaması için SMB kaynağını kullanmak üzere *Linux kimlik doğrulaması IÇIN SMB bilgilerini kullan* seçeneğini işaretleyin.

1. VM 'de yönetilen etki alanı kullanıcıları için otomatik olarak yerel bir ana dizin oluşturmak için *oturum açma sırasında giriş dizini oluşturma* seçeneğini işaretleyin.

1. Yönetilen etki alanı geçici olarak kullanılamıyor olsa da, etki alanı kullanıcılarınızın oturum açmasını sağlamak için *çevrimdışı kimlik doğrulama* seçeneğini işaretleyin.

1. Samba kullanıcıları ve grupları için UID ve GID aralıklarını değiştirmek istiyorsanız, *uzman ayarları*' nı seçin.

1. *NTP yapılandırması*' nı seçerek yönetilen etki alanınız Için ağ zaman Protokolü (NTP) zaman eşitlemesini yapılandırın. Yönetilen etki alanının IP adreslerini girin. Bu IP adresleri, *10.0.2.4* ve *10.0.2.5* gibi yönetilen etki alanınız için Azure Portal *Özellikler* penceresinde gösterilir.

1. **Tamam** ' ı seçin ve istendiğinde etki alanına katılmayı onaylayın.

1. Yönetilen etki alanındaki bir yöneticinin parolasını girip **Tamam**' ı seçin.

    ![Yönetilen etki alanına bir SLE VM 'ye katdığınızda kimlik doğrulama iletişim isteminin örnek ekran görüntüsü](./media/join-suse-linux-vm/domain-join-authentication-prompt.png)

Yönetilen etki alanına katıldıktan sonra masaüstü veya konsolunun görüntü yöneticisini kullanarak iş istasyonunuzdan oturum açabilirsiniz.

## <a name="join-vm-to-the-managed-domain-using-winbind-from-the-yast-command-line-interface"></a>YaST komut satırı arabiriminden winbind kullanarak VM 'yi yönetilen etki alanına katma

Yönetilen etki alanına **winbind** ve *yast komut satırı arabirimini* kullanarak katmak için:

* Etki alanına katılarak:

  ```console
  sudo yast samba-client joindomain domain=aaddscontoso.com user=<admin> password=<admin password> machine=<(optional) machine account>
  ```

## <a name="join-vm-to-the-managed-domain-using-winbind-from-the-terminal"></a>Terminalden winbind kullanarak VM 'yi yönetilen etki alanına katma

Yönetilen etki alanına **winbind** kullanarak katmak için ve *`samba net` komutunu* kullanın:

1. Kerberos istemcisi ve Samba-winbind 'yi yükler:

   ```console
   sudo zypper in krb5-client samba-winbind
   ```

2. Yapılandırma dosyalarını düzenleyin:

   * /etc/samba/SMB.exe
   
     ```ini
     [global]
         workgroup = AADDSCONTOSO
         usershare allow guests = NO #disallow guests from sharing
         idmap config * : backend = tdb
         idmap config * : range = 1000000-1999999
         idmap config AADDSCONTOSO : backend = rid
         idmap config AADDSCONTOSO : range = 5000000-5999999
         kerberos method = secrets and keytab
         realm = AADDSCONTOSO.COM
         security = ADS
         template homedir = /home/%D/%U
         template shell = /bin/bash
         winbind offline logon = yes
         winbind refresh tickets = yes
     ```

   * /etc/kronb5,conf
   
     ```ini
     [libdefaults]
         default_realm = AADDSCONTOSO.COM
         clockskew = 300
     [realms]
         AADDSCONTOSO.COM = {
             kdc = PDC.AADDSCONTOSO.COM
             default_domain = AADDSCONTOSO.COM
             admin_server = PDC.AADDSCONTOSO.COM
         }
     [domain_realm]
         .aaddscontoso.com = AADDSCONTOSO.COM
     [appdefaults]
         pam = {
             ticket_lifetime = 1d
             renew_lifetime = 1d
             forwardable = true
             proxiable = false
             minimum_uid = 1
         }
     ```

   * /etc/Security/pam_winbind. conf
   
     ```ini
     [global]
         cached_login = yes
         krb5_auth = yes
         krb5_ccache_type = FILE
         warn_pwd_expire = 14
     ```

   * /etc/nsswitch.conf
   
     ```ini
     passwd: compat winbind
     group: compat winbind
     ```

3. Azure AD ve Linux 'ta tarih ve saatin eşitlenmiş olduğunu denetleyin. Bunu, Azure AD sunucusunu NTP hizmetine ekleyerek yapabilirsiniz:
   
   1. Aşağıdaki satırı/etc/NTP.conf öğesine ekleyin:
     
      ```console
      server aaddscontoso.com
      ```

   1. NTP hizmetini yeniden başlatın:
     
      ```console
      sudo systemctl restart ntpd
      ```

4. Etki alanına katılarak:

   ```console
   sudo net ads join -U Administrator%Mypassword
   ```

5. Linux takılabilir kimlik doğrulama modüllerinde (PAM) oturum açma kaynağı olarak winbind 'yi etkinleştirin:

   ```console
   pam-config --add --winbind
   ```

6. Kullanıcıların oturum açabilmeleri için giriş dizinlerinin otomatik oluşturulmasını etkinleştirin:

   ```console
   pam-config -a --mkhomedir
   ```

7. Winbind hizmetini başlatın ve etkinleştirin:

   ```console
   sudo systemctl enable winbind
   sudo systemctl start winbind
   ```

## <a name="allow-password-authentication-for-ssh"></a>SSH için parola kimlik doğrulamasına izin ver

Varsayılan olarak, kullanıcılar yalnızca SSH ortak anahtar tabanlı kimlik doğrulaması kullanarak bir VM 'de oturum açabilirler. Parola tabanlı kimlik doğrulaması başarısız olur. VM 'yi yönetilen bir etki alanına katdığınızda, bu etki alanı hesaplarının parola tabanlı kimlik doğrulaması kullanması gerekir. Aşağıdaki gibi, parola tabanlı kimlik doğrulamasına izin vermek için SSH yapılandırmasını güncelleştirin.

1. *Sshd_conf* dosyasını bir düzenleyici ile açın:

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. *Passwordaduthentication* satırını *Evet* olarak güncelleştirin:

    ```console
    PasswordAuthentication yes
    ```

    İşiniz bittiğinde, düzenleyicinin komutunu kullanarak *sshd_conf* dosyasını kaydedin ve kapatın `:wq` .

1. Değişiklikleri uygulamak ve kullanıcıların bir parola kullanarak oturum açmasını sağlamak için SSH hizmetini yeniden başlatın:

    ```console
    sudo systemctl restart sshd
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>' AAD DC yöneticileri ' Grup sudo ayrıcalıklarına izin verme

SLE VM 'de *AAD DC yöneticileri* grubunun üyelerine yönetici ayrıcalıkları vermek için, */etc/suoners* öğesine bir giriş ekleyin. Eklendikten sonra *AAD DC yöneticileri* grubunun üyeleri, `sudo` SLE sanal makinesinde komutunu kullanabilir.

1. Şu şekilde düzenlenecek *sudoers* dosyasını açın:

    ```console
    sudo visudo
    ```

1. */Etc/sudoers* dosyasının sonuna aşağıdaki girişi ekleyin. *AAD DC Administrators* grubu adında boşluk içerir, bu nedenle Grup adına ters eğik çizgi kaçış karakteri ekleyin. *Aaddscontoso.com* gibi kendi etki alanı adınızı ekleyin:

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@aaddscontoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    İşiniz bittiğinde düzenleyicinin komutunu kullanarak düzenleyiciyi kaydedin ve kapatın `:wq` .

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Bir etki alanı hesabı kullanarak VM 'de oturum açma

VM 'nin yönetilen etki alanına başarıyla katıldığını doğrulamak için, bir etki alanı kullanıcı hesabı kullanarak yeni bir SSH bağlantısı başlatın. Bir giriş dizininin oluşturulduğunu ve etki alanındaki grup üyeliğinin uygulandığını doğrulayın.

1. Konsolınızdan yeni bir SSH bağlantısı oluşturun. Komutunu kullanarak yönetilen etki alanına ait bir etki alanı hesabı kullanın, örneğin, `ssh -l` `contosoadmin@aaddscontoso.com` *Linux-q2gr.aaddscontoso.com* gibi sanal makinenizin adresini girin. Azure Cloud Shell kullanıyorsanız, iç DNS adı yerine VM 'nin genel IP adresini kullanın.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com linux-q2gr.aaddscontoso.com
    ```

2. VM 'ye başarıyla bağlandığınızda, giriş dizininin doğru şekilde başlatıldığını doğrulayın:

    ```console
    pwd
    ```

    Kullanıcı hesabıyla eşleşen kendi dizininizin bulunduğu */Home* dizininde olmanız gerekir.

3. Şimdi grup üyeliklerinin doğru çözümlendiğini kontrol edin:

    ```console
    id
    ```

    Grup üyeliklerinizi yönetilen etki alanından görmeniz gerekir.

4. VM 'de *AAD DC Administrators* grubunun bir üyesi olarak oturum açtıysanız, komutunu doğru şekilde kullanıp kullandığınıza bakın `sudo` :

    ```console
    sudo zypper update
    ```

## <a name="next-steps"></a>Sonraki adımlar

VM 'yi yönetilen etki alanına bağlama veya bir etki alanı hesabıyla oturum açma sorunları yaşıyorsanız, bkz. [etki alanına ekleme sorunlarını giderme](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
