---
title: Azure erişim paneli uzantısını dağıtmak için bir GPO kullanarak IE | Microsoft Docs
description: Internet Explorer eklenti için uygulamalarım portalında dağıtmak için Grup İlkesi kullanma
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/08/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71c342ede77349b3f6c22093e5877ad5f5ce6549
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807677"
---
# <a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>Nasıl yapılır: Grup İlkesi'ni kullanarak Internet Explorer için erişim paneli uzantısını dağıtma

Bu öğreticide, uzaktan erişim paneli uzantısını Internet Explorer için kullanıcılarınızın makinelerde yüklemek için Grup İlkesi kullanımı gösterilmektedir. Bu uzantıyı kullanarak yapılandırılan uygulamalarında oturum açmak için gereken Internet Explorer kullanıcılar için gerekli olan [parola tabanlı çoklu oturum açma](what-is-single-sign-on.md#password-based-sso).

Yöneticiler'in bu uzantının dağıtımı otomatik hale getirmek önerilir. Aksi takdirde, kullanıcılar indirin ve uzantı kendilerinin yüklemesi kullanıcı hataya ve yönetici izinleri gerektiren gerekir. Bu öğretici, bir Grup İlkesi'ni kullanarak yazılım dağıtımlarını otomatikleştirme yöntemi içerir. [Grup İlkesi hakkında daha fazla bilgi edinin.](https://technet.microsoft.com/windowsserver/bb310732.aspx)

Erişim paneli uzantısı için de kullanılabilir olan [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) ve [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998), hangi hiçbiri yüklemek için yönetici izinleri gerektirir.

## <a name="prerequisites"></a>Önkoşullar

* Ayarlamış olduğunuz [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx), ve kullanıcılarınızın makineleri etki alanınıza katılmış.
* Grup İlkesi nesnesi (GPO) düzenlemek için "Ayarları düzenleme" izniniz olmalıdır. Varsayılan olarak, şu güvenlik gruplarının üyeleri bu izne sahiptir: Etki alanı yöneticileri, kuruluş yöneticileri ve Grup İlkesi Oluşturucu Sahipleri. [Daha fazla bilgi edinin.](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

## <a name="step-1-create-the-distribution-point"></a>1\. adım: Dağıtım noktası oluştur

İlk olarak, uzaktan üzerinde uzantıyı yüklemek istediğiniz makineleri tarafından erişilebilen bir ağ konumu üzerinde Yükleyici paketini yerleştirmeniz gerekir. Bunu yapmak için şu adımları uygulayın:

1. Sunucuda bir yönetici olarak oturum açın.
1. İçinde **Sunucu Yöneticisi'ni** penceresinde, Git **dosya ve depolama hizmetleri**.

    ![Açık dosyalar ve depolama hizmetleri](./media/deploy-access-panel-browser-extension/files-services.png)

1. Git **paylaşımları** sekmesi. Ardından **görevleri** > **yeni paylaşım...**

    ![Ekran görüntüsü, görevleri ekrandan yeni paylaşım yeri gösterir.](./media/deploy-access-panel-browser-extension/shares.png)

1. Tamamlamak **yeni paylaşım Sihirbazı** ve kullanıcılarınızın makinelerden erişilebildiğinden emin olmak için izinleri ayarlayın. [Paylaşımları hakkında daha fazla bilgi edinin.](https://technet.microsoft.com/library/cc753175.aspx)
1. Aşağıdaki Microsoft Windows Installer paketi (.msi dosyası) indirin: [Erişim paneli Extension.msi](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)
1. Yükleyici paket paylaşımında istenen bir konuma kopyalayın.

    ![.Msi dosyasını paylaşıma kopyalayın.](./media/deploy-access-panel-browser-extension/copy-package.png)

1. İstemci makineleriniz Yükleyici paketini paylaşımından erişmek mümkün olduğunu doğrulayın.

## <a name="step-2-create-the-group-policy-object"></a>2\. adım: Grup İlkesi nesnesi oluşturma

1. Active Directory etki alanı Hizmetleri (AD DS) yüklemenizi barındıran sunucuda oturum açın.
1. Sunucu Yöneticisi'nde Git **Araçları** > **Grup İlkesi Yönetimi**.

    ![Araçlar'a gidin > Grup İlkesi Yönetimi](./media/deploy-access-panel-browser-extension/tools-gpm.png)

1. Sol bölmesinde **Grup İlkesi Yönetimi** penceresinde, kuruluş birimi (OU) hiyerarşisini görüntülemek ve hangi kapsamda grup ilkesini uygulamak istediğiniz belirleyin. Örneğin, test etmek için birkaç kullanıcılara dağıtmak için küçük bir OU çekme karar verebilir ya da kuruluşunuzun tamamıyla dağıtmak için bir en üst düzey OU seçin.

   > [!NOTE]
   > Oluşturma veya, kuruluş birimine (OU) Düzenle, Sunucu Yöneticisi'ne geri geçmek ve gitmek istiyorsanız, **Araçları** > **Active Directory Kullanıcıları ve Bilgisayarları**.

1. Bir OU seçtikten sonra sağ tıklatın ve seçin **bu etki alanında GPO oluştur ve buraya bağla...**

    ![Ekran görüntüsü oluştur yeni bir GPO seçenek gösterir.](./media/deploy-access-panel-browser-extension/create-gpo.png)

1. İçinde **yeni GPO** istemi, yeni Grup İlkesi nesnesi için bir ad yazın.
1. Grup İlkesi nesnesini, oluşturduğunuz ve seçin sağ **Düzenle**.

## <a name="step-3-assign-the-installation-package"></a>3\. adım: Yükleme paketini atayın

1. Temel uzantısını dağıtmak isteyip istemediğinizi belirleyin **Bilgisayar Yapılandırması** veya **Kullanıcı Yapılandırması**. Kullanırken [Bilgisayar Yapılandırması](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx), uzantıyı hangi kullanıcıların oturum açın, bilgisayara yüklenir. İle [Kullanıcı Yapılandırması](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx), kullanıcılar, bunları bağımsız olarak hangi bilgisayarların oturum açmak için yüklü uzantılıdır.
1. Sol bölmesinde **Grup İlkesi Yönetimi Düzenleyicisi** penceresi, seçtiğiniz yapılandırma türüne aşağıdaki klasör yolları birini gidin:

   * `Computer Configuration/Policies/Software Settings/`
   * `User Configuration/Policies/Software Settings/`

1. Sağ **yazılım yüklemesi**, ardından **yeni** > **paket...**
1. Yükleyici paketi içeren paylaşılan klasöre gidin [1. adım: Dağıtım noktası oluştur](#step-1-create-the-distribution-point).msi dosyasını seçin ve tıklayın **açık**.

   > [!IMPORTANT]
   > Paylaşım bu aynı sunucuda bulunuyorsa, yerel dosya yolu yerine bir ağ dosya yolu .msi eriştiğiniz doğrulayın.

    ![Paylaşılan klasörden yükleme paketini seçin](./media/deploy-access-panel-browser-extension/select-package.png)

1. İçinde **yazılım dağıtma** anında, select **atanan** dağıtım yönteminize için. Daha sonra, **Tamam**'a tıklayın.

Uzantı artık, seçtiğiniz kuruluş dağıtılır. [Grup İlkesi Yazılım yükleme hakkında daha fazla bilgi edinin.](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

## <a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>4\. Adım: Uzantı için Internet Explorer otomatik etkinleştir

Kullanmadan önce yükleyici çalıştırmanın yanı sıra, her uzantısını Internet Explorer için açıkça etkinleştirilmesi gerekir. Grup İlkesi kullanarak erişim panelinde uzantıyı etkinleştirmek için aşağıdaki adımları izleyin:

1. İçinde **Grup İlkesi Yönetimi Düzenleyicisi** penceresinde, yapılandırma türüne seçerseniz, aşağıdaki yollardan birini gidin [3. adım: Yükleme paketini Ata](#step-3-assign-the-installation-package):

   * `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`

1. Sağ **eklenti listesi**seçip **Düzenle**.

    !["Eklenti Listesi" sağ tıklayın ve "Düzenle" yi seçin](./media/deploy-access-panel-browser-extension/edit-add-on-list.png)

1. İçinde **eklenti listesi** penceresinde **etkin**. Ardından, altında **seçenekleri** bölümünde **göster...** .

    ![Etkinleştir'i tıklatın ve ardından göster...](./media/deploy-access-panel-browser-extension/edit-add-on-list-window.png)

1. İçinde **içeriğini göster** penceresinde aşağıdaki adımları gerçekleştirin:

   1. İlk sütun için ( **değer adı** alan), kopyalama ve aşağıdaki sınıf kimliği: `{030E9A3F-7B18-4122-9A60-B87235E4F59E}`
   1. İkinci sütun için ( **değer** alan), aşağıdaki değer: `1`
   1. Tıklayın **Tamam** kapatmak için **içeriğini göster** penceresi.

      ![Önceki adımda belirtilen değerleri doldurun](./media/deploy-access-panel-browser-extension/show-contents.png)

1. Tıklayın **Tamam** yaptığınız değişiklikleri uygulamak ve kapatmak için **eklenti listesi** penceresi.

Seçili OU'ya makineler için şimdi Uzantının etkinleştirilmesi gerekir. [Etkinleştirmek veya Internet Explorer eklentileri devre dışı bırakmak için Grup İlkesi'ni kullanma hakkında daha fazla bilgi edinin.](https://technet.microsoft.com/library/dn454941.aspx)

## <a name="step-5-optional-disable-remember-password-prompt"></a>5\. adım (isteğe bağlı): "Parolayı anımsa" istemini devre dışı bırakın

Kullanıcılar Web sitelerine erişim paneli uzantısını kullanarak oturum açtığınızda, Internet Explorer "parolanızı depolamak istiyorsunuz?" isteyen aşağıdaki istemi gösterebilir

![Gösterilir "parolanızı... depolamak istiyorsanız" istemi](./media/deploy-access-panel-browser-extension/remember-password-prompt.png)

Ardından kullanıcılarınız bu istemi görüntülemesini engellemek istiyorsanız, otomatik tamamlama parolaları anımsamaya çalışmaktan gelen önlemek için aşağıdaki adımları izleyin:

1. İçinde **Grup İlkesi Yönetimi Düzenleyicisi** penceresi, aşağıda listelenen yoluna gidin. Bu yapılandırma ayarı yalnızca altında kullanılabilir **Kullanıcı Yapılandırması**.

   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`
1. Adlı ayarı bulmak **kullanıcı adları ve parolalar formlarında Otomatik Tamamlama özelliği açmak**.

   > [!NOTE]
   > Active Directory önceki sürümlerinde bu ayar adıyla listesinde **parolaları kaydetmek için otomatik tamamlama izin verme**. Bu öğreticide açıklanan ayarı için bu ayarı yapılandırmanın farklıdır.

    ![Kullanıcı Ayarları altında şunun unutmayın](./media/deploy-access-panel-browser-extension/disable-auto-complete.png)

1. Yukarıdaki ayar sağ tıklatın ve seçin **Düzenle**.
1. Başlıklı penceresinde **kullanıcı adları ve parolalar formlarında Otomatik Tamamlama özelliği açmak**seçin **devre dışı bırakılmış**.

    ![Otomatik Tamamlama özelliğini Aç "Devre dışı" seçeneğini belirleyin](./media/deploy-access-panel-browser-extension/disable-passwords.png)

1. Tıklayın **Tamam** bu değişiklikleri uygulamak ve penceresini kapatın.

Kullanıcılar artık kendi kimlik bilgilerini veya kullanım otomatik tamamlama daha önce depolanan kimlik bilgilerine depolamak mümkün olacaktır. Ancak, bu ilke otomatik tamamlama, arama alanlar gibi form alanlarını diğer türleri için kullanmaya devam etmek kullanıcılara izin vermez.

> [!WARNING]
> Bazı kimlik bilgileri, bu ilke depolamak kullanıcıları seçtikten sonra bu ilke etkinleştirilirse *değil* zaten depolanmış kimlik bilgilerini temizleyin.

## <a name="step-6-testing-the-deployment"></a>6\. Adım: Test etme ve dağıtım

Uzantı dağıtımı başarılı olup olmadığını doğrulamak için aşağıdaki adımları izleyin:

1. Kullanarak dağıttıysanız **Bilgisayar Yapılandırması**, seçtiğiniz OU'ya ait bir istemci makinesi oturum [2. adım: Grup İlkesi nesnesi oluşturmak](#step-2-create-the-group-policy-object). Kullanarak dağıttıysanız **Kullanıcı Yapılandırması**, ilgili OU'ya ait bir kullanıcı olarak oturum açtığınızdan emin olun.
1. Bu, birkaç oturum açma işlemleri için tam olarak bu makineyle güncelleştirmek Grup İlkesi değişikliklerinin sürebilir. Güncelleştirmeyi zorlamak için açık bir **komut istemi** penceresi ve şu komutu çalıştırın: `gpupdate /force`
1. Yüklemenin gerçekleşmesi makineyi yeniden başlatmanız gerekir. Önyükleme sırasında uzantı normal yükler önemli ölçüde daha uzun sürebilir.
1. Yeniden başlattıktan sonra açın **Internet Explorer**. Pencerenin sağ üst köşesindeki üzerinde tıklayın **Araçları** (dişli simgesi) seçip **eklentileri yönetme**.
1. İçinde **Eklentileri Yönet** penceresinde doğrulayın **erişim paneli uzantısını** yüklü olduğundan ve kendi **durumu** ayarlanmış **etkin**.

   ![Erişim paneli uzantısı yüklü ve etkin olduğunu doğrulayın](./media/deploy-access-panel-browser-extension/verify-install.png)

## <a name="learn-more"></a>Daha fazla bilgi edinin

* [Uygulama erişimi ve Azure Active Directory ile çoklu oturum açma](what-is-single-sign-on.md)
* [Erişim paneli uzantısını Internet Explorer için sorun giderme](manage-access-panel-browser-extension.md)
