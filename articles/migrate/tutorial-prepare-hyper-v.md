---
title: Azure geçişi ile Azure 'a değerlendirme ve geçiş için Hyper-V VM 'lerini hazırlayın | Microsoft Docs
description: Azure geçişi kullanılarak Hyper-V VM 'lerinin Azure 'a değerlendirmesi ve geçirilmesi için nasıl hazırlanılacağı açıklanmaktadır.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 09/16/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 2a70472518b72052f8338d6f14ea64fed6c6d4f1
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158331"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Hyper-V VM 'lerinin Azure 'a değerlendirmesi ve geçirilmesi için hazırlanma

Bu makalede, [Azure geçişi](migrate-services-overview.md)ile şirket içi Hyper-V VM 'lerinin Azure 'a değerlendirmesi ve geçirilmesi için nasıl hazırlanılacağı açıklanır.

[Azure geçişi](migrate-overview.md) , Microsoft Azure için uygulamaları, altyapıyı ve iş yüklerini keşfetmenize, değerlendirmenize ve geçirmenize yardımcı olan araçların merkezini sağlar. Hub, Azure geçiş araçları ve üçüncü taraf bağımsız yazılım satıcısı (ISV) tekliflerini içerir.

Bu öğretici, Hyper-V sanal makinelerini Azure 'a nasıl değerlendirmenizi ve geçirekullanacağınızı gösteren bir serinin ilk ilkidir. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Azure 'u hazırlayın. Azure hesabınız ve kaynaklarınızın Azure geçişi ile çalışması için izinleri ayarlayın.
> * Sunucu değerlendirmesi için şirket içi Hyper-V konakları ve VM 'Leri hazırlayın.
> * Sunucu geçişi için şirket içi Hyper-V konakları ve VM 'Leri hazırlayın.


> [!NOTE]
> Öğreticiler, bir senaryo için en basit dağıtım yolunu gösterir, böylece bir kavram kanıtı hızlı bir şekilde ayarlayabilmenizi sağlayabilirsiniz. Öğreticiler mümkün olduğunca varsayılan seçenekleri kullanır ve tüm olası ayarları ve yolları göstermez. Ayrıntılı yönergeler için bkz. Hyper-V değerlendirmesi ve geçişi için nasıl yapılır.


Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturun.


## <a name="prepare-azure"></a>Azure’u hazırlama

### <a name="azure-permissions"></a>Azure izinleri

Azure geçişi dağıtımı için izinleri ayarlamanız gerekir.

- Azure geçişi projesi oluşturmak için Azure hesabınız için izinler.
- Azure geçişi gerecini kaydetmek için hesabınızın izinleri. Gereç, Hyper-V bulma ve geçiş için kullanılır. Gereç kaydı sırasında Azure geçişi, gereci benzersiz bir şekilde tanımlayan iki Azure Active Directory (Azure AD) uygulaması oluşturur:
    - İlk uygulama Azure geçişi hizmet uç noktaları ile iletişim kurar.
    - İkinci uygulama, Azure AD uygulama bilgileri ve gereç yapılandırma ayarlarını depolamak için kayıt sırasında oluşturulan Azure Key Vault erişir.



### <a name="assign-permissions-to-create-project"></a>Proje oluşturmak için izin atama

Azure geçişi projesi oluşturma izniniz olup olmadığını denetleyin.

1. Azure portal aboneliğini açın ve **erişim denetimi (IAM)** seçeneğini belirleyin.
2. **Erişimi denetle**' de ilgili hesabı bulun ve izinleri görüntülemek için tıklatın.
3. **Katkıda bulunan** veya **sahip** izinlerinizin olması gerekir.
    - Henüz ücretsiz bir Azure hesabı oluşturduysanız, aboneliğinizin sahibi olursunuz.
    - Abonelik sahibi değilseniz, rolü atamak için sahip ile çalışın.


### <a name="assign-permissions-to-register-the-appliance"></a>Gereci kaydetmek için izin atama

Aşağıdaki yöntemlerden birini kullanarak, Gereç kaydı sırasında oluşturma sırasında Azure AD uygulamalarını oluşturmak için Azure geçişi izinleri atayabilirsiniz:

- Kiracı/Genel yönetici, Kiracıdaki kullanıcılara Azure AD uygulamaları oluşturmak ve kaydolmak için izin verebilir.
- Kiracı/Genel yönetici, uygulama geliştirici rolünü (izinleri olan) hesaba atayabilir.

Buna dikkat edin:

- Uygulamalar, yukarıda açıklananlar dışında başka bir abonelik üzerinde başka erişim izinlerine sahip değildir.
- Yalnızca yeni bir gereç kaydettiğinizde bu izinlere ihtiyacınız vardır. Gereç kurulduktan sonra izinleri kaldırabilirsiniz.


#### <a name="grant-account-permissions"></a>Hesap izinleri verme

Kiracı/Genel yönetici, izinleri aşağıdaki gibi verebilir:

1. Azure AD 'de, kiracı/genel yönetici **Azure Active Directory** > **Kullanıcılar** > **Kullanıcı ayarları**' na gitmelidir.
2. Yönetici **uygulama kayıtları** **Evet**olarak ayarlanmalıdır.

    ![Azure AD izinleri](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Bu, hassas olmayan bir varsayılan ayardır. [Daha fazla bilgi edinin](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### <a name="assign-application-developer-role"></a>Uygulama geliştirici rolü atama

Kiracı/Genel yönetici, uygulama geliştirici rolünü bir hesaba atayabilir. [Daha fazla bilgi edinin](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-for-hyper-v-assessment"></a>Hyper-V değerlendirmesi için hazırlanma

Hyper-V değerlendirmesi için hazırlanmak üzere şunları yapın:

1. Hyper-V konak ayarlarını doğrulayın.
2. Her konakta PowerShell uzaktan iletişimini ayarlayın, böylece Azure geçişi gereci konakta PowerShell komutlarını bir WinRM bağlantısı üzerinden çalıştırabilir.
3. VM diskleri uzak SMB depolaması 'nda bulunuyorsa, kimlik bilgilerinin temsili gereklidir.
    - Azure geçişi gerecinin istemci olarak davranabilmesi ve bir konağa kimlik bilgileri temsilciliğini sağlamak için CredSSP temsilcisini etkinleştirin.
    - Aşağıda açıklandığı gibi, her bir konağın gereç için bir temsilci görevi görmesini sağlayabilirsiniz.
    - Daha sonra gereci ayarladığınızda, Gereç üzerinde temsilciyi etkinleştirecektir.
4. Gereç gereksinimlerini ve gereç için gereken URL/bağlantı noktası erişimini gözden geçirin.
5. Gerecin VM 'Leri bulması için kullanacağı bir hesap ayarlayın.
6. Bulma ve değerlendirme yapmak istediğiniz her VM 'de Hyper-V tümleştirme hizmetlerini ayarlayın.


Aşağıdaki yordamları kullanarak bu ayarları el ile yapılandırabilirsiniz. Alternatif olarak, Hyper-V önkoşulları yapılandırma betiğini çalıştırırsınız.

### <a name="hyper-v-prerequisites-configuration-script"></a>Hyper-V önkoşulları yapılandırma betiği

Betik, Hyper-v konaklarını doğrular ve Hyper-V VM 'lerini bulup değerlendirmek için ihtiyacınız olan ayarları yapılandırır. İşte şunları yapar:

- Betiği desteklenen bir PowerShell sürümünde çalıştırıp çalıştırdığınızı denetler.
- Sizin (betiği çalıştıran kullanıcı) Hyper-V konağında yönetim ayrıcalıklarına sahip olduğunu doğrular.
- , Hyper-V konağından iletişim kurmak üzere Azure geçişi hizmeti için kullanılan bir yerel kullanıcı hesabı (yönetici değil) oluşturmanıza olanak sağlar. Bu Kullanıcı hesabı konaktaki bu gruplara eklenir:
    - Uzaktan yönetim kullanıcıları
    - Hyper-V yöneticileri
    - Performans Izleyicisi kullanıcıları
- Konağın desteklenen bir Hyper-V sürümü ve Hyper-V rolü çalıştığını denetler.
- WinRM hizmetini etkinleştirilir ve konakta 5985 (HTTP) ve 5986 (HTTPS) bağlantı noktalarını (meta veri koleksiyonu için gereklidir) açar.
- Konakta PowerShell uzaktan iletişimini mümkün bir şekilde sunar.
- Hyper-V tümleştirme hizmetinin konak tarafından yönetilen tüm VM 'lerde etkin olduğunu denetler.
- Gerekirse, konakta CredSSP 'yi etkinleştirilir.

Betiği aşağıdaki gibi çalıştırın:

1. Hyper-V konağında PowerShell sürüm 4,0 veya daha yeni bir sürümün yüklü olduğundan emin olun.
2. Betiği [Microsoft Indirme merkezi](https://aka.ms/migrate/script/hyperv)' nden indirin. Betik, Microsoft tarafından şifreli olarak imzalanır.
3. MD5 veya SHA256 karma dosyalarını kullanarak betik bütünlüğünü doğrulayın. Diyez etiketi değerleri aşağıda verilmiştir. Komut dosyasının karmasını oluşturmak için şu komutu çalıştırın:
    ```
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Örnek kullanım:
    ```
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1
    SHA256
    ```

4.  Betik bütünlüğünü doğruladıktan sonra, bu PowerShell komutuyla her Hyper-V konağında betiği çalıştırın:
    ```
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```

#### <a name="hashtag-values"></a>Diyez etiketi değerleri

Karma değerleri şunlardır:

| **Yla** | **Değer** |
| --- | --- |
| **MD5** | 0ef418f31915d01f896ac42a80dc414e |
| **SHA256** | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2 |

### <a name="verify-hyper-v-host-settings"></a>Hyper-V konak ayarlarını doğrulama

1. Sunucu değerlendirmesi için [Hyper-V konağı gereksinimlerini](migrate-support-matrix-hyper-v.md#assessment-hyper-v-host-requirements) doğrulayın.
2. Hyper-V konaklarında [gerekli bağlantı noktalarının](migrate-support-matrix-hyper-v.md#assessment-port-requirements) açık olduğundan emin olun.

### <a name="enable-powershell-remoting-on-hosts"></a>Konaklarda PowerShell uzaktan iletişimini etkinleştir

Her konakta PowerShell uzaktan iletişimini aşağıdaki gibi ayarlayın:

1. Her konakta, yönetici olarak bir PowerShell konsolu açın.
2. Şu komutu çalıştırın:

    ```
    Enable-PSRemoting -force
    ```

### <a name="enable-credssp-on-hosts"></a>Konaklarda CredSSP 'yi etkinleştirme

Konağın diskler içeren VM 'Ler varsa, bu adımı konakta doldurun.

- Bu komutu, tüm Hyper-V konaklarında uzaktan çalıştırabilirsiniz.
- Kümeye yeni konak düğümleri eklerseniz, bulma için otomatik olarak eklenir, ancak gerekirse yeni düğümlerde CredSSP 'yi el ile etkinleştirmeniz gerekir.

Aşağıdaki gibi etkinleştirin:

1. SMB paylaşımlarında diskler içeren Hyper-V VM 'lerini çalıştıran Hyper-V konaklarını belirler.
2. Tanımlanan her Hyper-V konağında aşağıdaki komutu çalıştırın:

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

Gereci ayarlarken, [gereç üzerinde etkinleştirerek](tutorial-assess-hyper-v.md#delegate-credentials-for-smb-vhds)CredSSP ayarlamayı tamamlayın. Bu, bu serideki bir sonraki öğreticide açıklanmıştır.


### <a name="verify-appliance-settings"></a>Gereç ayarlarını doğrulama

Sonraki öğreticide Azure geçişi gerecini ve değerlendirmeyi ayarlamadan önce, Gereç dağıtımına hazırlanın.

1. Gereç gereksinimlerini [doğrulayın](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) .
2. Gerecin erişmesi gereken Azure URL 'Lerini [gözden geçirin](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access) .
3. Bulma ve değerlendirme sırasında gereç tarafından toplanacak verileri gözden geçirin.
4. Gereç [için bağlantı noktası](migrate-support-matrix-hyper-v.md#assessment-port-requirements) erişim gereksinimleri.


### <a name="set-up-an-account-for-vm-discovery"></a>VM bulma için bir hesap ayarlama

Azure geçişi 'nin şirket içi VM 'Leri bulması için izinleri olması gerekir.

- Hyper-V konakları/kümesi üzerinde yönetici izinlerine sahip bir etki alanı veya yerel kullanıcı hesabı ayarlayın.

    - Bulmaya dahil etmek istediğiniz tüm konaklar ve kümeler için tek bir hesaba ihtiyacınız vardır.
    - Hesap bir yerel veya etki alanı hesabı olabilir. Hyper-V konakları veya kümeleri üzerinde yönetici izinlerine sahip olması önerilir.
    - Alternatif olarak, yönetici izinleri atamak istemiyorsanız, aşağıdaki izinler gereklidir:
        - Uzaktan yönetim kullanıcıları
        - Hyper-V yöneticileri
        - Performans Izleyicisi kullanıcıları

### <a name="enable-integration-services-on-vms"></a>VM 'lerde tümleştirme hizmetlerini etkinleştirme

Azure geçişi 'nin VM 'deki işletim sistemi bilgilerini yakalaması için, her bir sanal makinede Tümleştirme Hizmetleri etkinleştirilmelidir.

Bulup değerlendirmek istediğiniz VM 'lerde, her VM 'de [Hyper-V tümleştirme hizmetlerini](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) etkinleştirin.

## <a name="prepare-for-hyper-v-migration"></a>Hyper-V geçişine hazırlanma

1. [İnceleme](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-requirements) Geçiş için Hyper-V ana bilgisayar gereksinimleri.
2. Azure 'a geçirmek istediğiniz Hyper-V VM 'lerinin gereksinimlerini [gözden geçirin](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements) .
3. Hyper-V konaklarının ve kümelerinin VM geçişi için erişmesi gereken Azure URL 'Lerine [göz önünde](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-url-access) kalmaz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Azure hesabı izinlerini ayarlayın.
> * Değerlendirme ve geçiş için Hyper-V konakları ve VM 'Ler hazırlandı.

Azure geçişi projesi oluşturmak ve Azure 'a geçiş için Hyper-V VM 'lerini değerlendirmek üzere bir sonraki öğreticiye devam edin

> [!div class="nextstepaction"]
> [Hyper-V VM 'lerini değerlendirme](./tutorial-assess-hyper-v.md)
