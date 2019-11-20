---
title: Azure Izleyici 'ye Configuration Manager bağlanma | Microsoft Docs
description: Bu makalede, Azure Izleyici 'de çalışma alanına Configuration Manager bağlama ve verileri çözümlemeye başlama adımları gösterilmektedir.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 08/28/2019
ms.openlocfilehash: fee6f09ba8e290ae6599f07d4ed831fb89427f76
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932648"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>Configuration Manager Azure Izleyici 'ye bağlama
Cihaz koleksiyonu verilerini eşitlemek ve Azure Izleyici ve Azure Otomasyonu 'nda bu koleksiyonlara başvurmak için System Center Configuration Manager ortamınızı Azure Izleyici 'ye bağlayabilirsiniz.  

## <a name="prerequisites"></a>Önkoşullar

Azure Izleyici System Center Configuration Manager geçerli dalı, sürüm 1606 ve üstünü destekler.

>[!NOTE]
>Log Analytics bir çalışma alanıyla Configuration Manager bağlama özelliği isteğe bağlıdır ve varsayılan olarak etkinleştirilmez. Bu özelliği kullanmadan önce etkinleştirmeniz gerekir. Daha fazla bilgi için bkz. [güncelleştirmelerden isteğe bağlı özellikleri etkinleştirme](https://docs.microsoft.com/sccm/core/servers/manage/install-in-console-updates#bkmk_options).

## <a name="configuration-overview"></a>Yapılandırmaya genel bakış

Aşağıdaki adımlarda, Azure Izleyici ile Configuration Manager Tümleştirmesini Yapılandırma adımları özetlenmektedir.  

1. Azure Active Directory ' de bir Web uygulaması ve/veya Web API uygulaması olarak Configuration Manager kaydedin ve Azure Active Directory kaydından istemci KIMLIĞI ve istemci gizli anahtarı anahtarına sahip olduğunuzdan emin olun. Bu adımın nasıl yapılacağı hakkında ayrıntılı bilgi için [kaynaklara erişebilen Active Directory uygulama ve hizmet sorumlusu oluşturmak için portalı kullanma](../../active-directory/develop/howto-create-service-principal-portal.md) konusuna bakın.

2. Azure Active Directory ' de, [Azure izleyici 'ye erişim izni olan Configuration Manager (kayıtlı Web uygulaması) verin](#grant-configuration-manager-with-permissions-to-log-analytics).

3. Configuration Manager, **Azure hizmetleri** Sihirbazı 'nı kullanarak bir bağlantı ekleyin.

4. Configuration Manager hizmet bağlantı noktası site sistemi rolünü çalıştıran bilgisayarda [Windows için Log Analytics Aracısı 'Nı indirin ve yükleyin](#download-and-install-the-agent) . Aracı Configuration Manager verilerini Azure Izleyici 'de Log Analytics çalışma alanına gönderir.

5. Azure Izleyici 'de, [koleksiyonları Configuration Manager](#import-collections) bilgisayar grupları olarak içeri aktarın.

6. Azure Izleyici 'de Configuration Manager verileri [bilgisayar grupları](computer-groups.md)olarak görüntüleyin.

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Log Analytics izinlere sahip Configuration Manager verme

Aşağıdaki yordamda, Log Analytics çalışma alanınızdaki *katkıda* bulunan rolünü Configuration Manager daha önce oluşturduğunuz ad uygulaması ve hizmet sorumlusuna verirsiniz. Henüz bir çalışma alanınız yoksa devam etmeden önce [Azure izleyici 'de çalışma alanı oluşturma](../../azure-monitor/learn/quick-create-workspace.md) bölümüne bakın. Bu, Configuration Manager kimliğinin doğrulanmasını ve Log Analytics çalışma alanınıza bağlanmasını sağlar.  

> [!NOTE]
> Configuration Manager için Log Analytics çalışma alanında izinleri belirtmeniz gerekir. Aksi takdirde, Configuration Manager 'de Yapılandırma Sihirbazı 'nı kullandığınızda bir hata iletisi alırsınız.
>

1. Azure portalının sol alt köşesinde bulunan **Tüm hizmetler**’e tıklayın. Kaynak listesinde **Log Analytics** yazın. Yazmaya başladığınızda liste, girişinize göre filtrelenir. **Log Analytics**’i seçin.

2. Log Analytics çalışma alanları listenizde, değiştirilecek çalışma alanını seçin.

3. Sol bölmeden **erişim denetimi (IAM)** seçeneğini belirleyin.

4. Erişim denetimi (ıAM) sayfasında, **rol ataması Ekle** ' ye tıklayın ve **rol ataması Ekle** bölmesi görüntülenir.

5. **Rol ataması Ekle** bölmesinde, **rol** açılan listesi altında **katkıda** bulunan rolünü seçin.  

6. **Erişim Izni ata** listesinde, daha önce ad ' de oluşturulan Configuration Manager uygulamasını seçin ve ardından **Tamam**' a tıklayın.  

## <a name="download-and-install-the-agent"></a>Aracıyı indirme ve yükleme

Configuration Manager hizmet bağlantı noktası site sistemi rolünü barındıran bilgisayarda Windows Log Analytics Aracısı 'nı yüklemeye yönelik yöntemleri anlamak için [Windows bilgisayarları Azure 'da Azure izleyici 'ye bağlama](agent-windows.md) makalesini gözden geçirin.  

## <a name="connect-configuration-manager-to-log-analytics-workspace"></a>Configuration Manager Log Analytics çalışma alanına bağlama

>[!NOTE]
> Log Analytics bağlantısı eklemek için, Configuration Manager ortamınızın çevrimiçi mod için yapılandırılmış bir [hizmet bağlantı noktası](https://docs.microsoft.com/sccm/core/servers/deploy/configure/about-the-service-connection-point) olması gerekir.

> [!NOTE]
> Hiyerarşinizdeki üst katman siteyi Azure Izleyici 'ye bağlamanız gerekir. Tek başına bir birincil siteyi Azure Izleyici 'ye bağlayıp ortamınıza bir merkezi yönetim sitesi eklerseniz, bağlantıyı yeni hiyerarşi içinde silip yeniden oluşturmanız gerekir.

1. Configuration Manager **Yönetim** çalışma alanında, **bulutlar Hizmetleri** ' ni seçin ve ardından **Azure hizmetleri**' ni seçin. 

2. **Azure hizmetleri** ' ne sağ tıklayın ve ardından **Azure hizmetlerini yapılandır**' ı seçin. **Azure hizmetlerini yapılandır** sayfası görünür. 
   
3. **Genel** ekranda, aşağıdaki eylemleri yaptık ve her öğe için ayrıntılarınızı doğrulayın, ardından **İleri**' yi seçin.

4. Azure Hizmetleri Sihirbazı 'nın Azure Hizmetleri sayfasında:

    1. Configuration Manager nesne için bir **ad** belirtin.
    2. Hizmeti tanımlamanızı sağlayacak isteğe bağlı bir **Açıklama** belirtin.
    3. Azure hizmeti **OMS bağlayıcısını**seçin.

    >[!NOTE]
    >OMS artık Azure Izleyici 'nin bir özelliği olan Log Analytics olarak adlandırılır.

5. Azure hizmetleri sihirbazının Azure uygulama özellikleri sayfasına devam etmek için **İleri ' yi** seçin.

6. Azure Hizmetleri Sihirbazı 'nın **uygulama** sayfasında, önce listeden Azure ortamı ' nı seçin ve ardından **içeri aktar**' a tıklayın.

7. **Uygulamaları Içeri aktar** sayfasında, aşağıdaki bilgileri belirtin:

    1. Uygulama için **Azure AD kiracı adını** belirtin.

    2. Azure AD kiracısı için Azure AD kiracı **kimliği** belirtin. Bu bilgileri Azure Active Directory **Özellikler** sayfasında bulabilirsiniz. 

    3. **Uygulama adının** uygulama adını belirtin.

    4. Daha önce oluşturulan oluşturulan Azure AD uygulamasının uygulama KIMLIĞI olan **ISTEMCI kimliği**için belirtin.

    5. Oluşturulan Azure AD uygulamasının Istemci gizli anahtarını **gizli anahtar**için belirtin.

    6. **Gizli anahtar süre sonu**için anahtarın süre sonu tarihini belirtin.

    7. Daha önce oluşturulan oluşturulan Azure AD uygulamasının uygulama KIMLIĞI URI 'si olan **uygulama kimliği URI 'si**için belirtin.

    8. **Doğrula** ' yı seçin ve sağ tarafta, sonuçların **başarıyla doğrulanması**gerektiğini gösterir!.

8. **Yapılandırma** sayfasında Azure **aboneliklerini**, **Azure Kaynak grubu**ve **Operations Management Suite çalışma alanı** ALANLARıNı doğrulamak için bilgileri gözden geçirin. Azure AD uygulaması, kaynak grubunda yeterli izin yok. Alanlar boşsa, uygulamanızın gerekli haklara sahip olmadığını gösterir. Toplanacak ve çalışma alanına iletecek cihaz koleksiyonlarını seçin ve ardından **Ekle**' yi seçin.

9. **Ayarları onaylayın** sayfasında seçenekleri gözden geçirin ve bağlantıyı oluşturmaya ve yapılandırmaya başlamak için **İleri** ' yi seçin.

10. Yapılandırma tamamlandığında **tamamlama** sayfası görüntülenir. **Kapat**' ı seçin. 

Configuration Manager Azure Izleyici 'ye bağladıktan sonra, koleksiyonları ekleyebilir veya kaldırabilir ve bağlantının özelliklerini görüntüleyebilirsiniz.

## <a name="update-log-analytics-workspace-connection-properties"></a>Log Analytics çalışma alanı bağlantı özelliklerini güncelleştir

Parola veya gizli anahtar süresinin dolması veya kaybolması durumunda Log Analytics bağlantısı özelliklerini el ile güncelleştirmeniz gerekir.

1. Configuration Manager **Yönetim** çalışma alanında **Cloud Services** ' i seçin ve OMS **bağlantısı Özellikler** sayfasını açmak için **OMS Bağlayıcısı** ' nı seçin.
2. Bu sayfada, **kiracınızı**, **istemci kimliğinizi**, **istemci gizli anahtarı süre sonunu**görüntülemek için **Azure Active Directory** sekmesine tıklayın. Süresi dolmuşsa, **istemci gizli anahtarınızı** **doğrulayın** .

## <a name="import-collections"></a>Koleksiyonları içeri aktar

Configuration Manager bir Log Analytics bağlantısı ekledikten ve aracıyı Configuration Manager hizmet bağlantı noktası site sistemi rolünü çalıştıran bilgisayara yükledikten sonra, bir sonraki adım, koleksiyonları Azure 'daki Configuration Manager içeri aktarmasıdır. Bilgisayar grupları olarak izleyin.

Hiyerarşinizden cihaz koleksiyonlarını içeri aktarmaya yönelik ilk yapılandırmayı tamamladıktan sonra, Üyelik güncel tutulması için koleksiyon bilgileri her 3 saatte bir alınır. Bu seçeneği dilediğiniz zaman devre dışı bırakabilirsiniz.

1. Azure portalının sol alt köşesinde bulunan **Tüm hizmetler**’e tıklayın. Kaynak listesinde **Log Analytics** yazın. Yazmaya başladığınızda liste, girişinize göre filtrelenir. **Log Analytics çalışma alanlarını**seçin.
2. Log Analytics çalışma alanları listenizde, Configuration Manager kayıtlı çalışma alanını seçin.  
3. **Gelişmiş ayarlar**’ı seçin.
4. **Bilgisayar grupları** ' nı seçin ve ardından **SCCM**' yi seçin.  
5. **Configuration Manager koleksiyonu üyeliklerini Içeri aktar** ' ı seçin ve **Kaydet**' e tıklayın.  
   
    ![Bilgisayar grupları-SCCM sekmesi](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Configuration Manager verileri görüntüleme

Configuration Manager bir Log Analytics bağlantısı ekledikten ve aracıyı Configuration Manager hizmet bağlantı noktası site sistemi rolünü çalıştıran bilgisayara yükledikten sonra, aracıdaki veriler Azure Izleyici 'de Log Analytics çalışma alanına gönderilir. Azure Izleyici 'de, Configuration Manager koleksiyonlarınız [bilgisayar grupları](../../azure-monitor/platform/computer-groups.md)olarak görünür. Grupları, **Ayarları \ bilgisayar grupları**' nın altında **Configuration Manager** sayfasından görüntüleyebilirsiniz.

Koleksiyonlar alındıktan sonra, koleksiyon üyeliğine sahip kaç bilgisayar algılandığını görebilirsiniz. Ayrıca, içeri aktarılmış koleksiyonların sayısını da görebilirsiniz.

![Bilgisayar grupları-SCCM sekmesi](./media/collect-sccm/sccm-computer-groups02.png)

Birine tıkladığınızda, herhangi bir gruba ait tüm içeri aktarılan grupları veya tüm bilgisayarları görüntüleyen günlük sorgu Düzenleyicisi açılır. [Günlük aramasını](../../azure-monitor/log-query/log-query-overview.md)kullanarak, koleksiyon üyeliği verilerini ayrıntılı olarak analiz gerçekleştirebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Configuration Manager verileriniz hakkında ayrıntılı bilgileri görüntülemek için [günlük aramasını](../../azure-monitor/log-query/log-query-overview.md) kullanın.
