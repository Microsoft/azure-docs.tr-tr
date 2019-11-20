---
title: Azure Log Analytics BT Hizmet Yönetimi Bağlayıcısı desteklenen bağlantılar | Microsoft Docs
description: Bu makalede, ITSM iş öğelerini merkezi olarak izlemek ve yönetmek için ITSM ürünlerinizi/hizmetlerinizi Azure Izleyici 'de BT Hizmet Yönetimi Bağlayıcısı (ITSMC) ile bağlama hakkında bilgi sağlanır.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: JYOTHIRMAISURI
ms.author: v-jysur
ms.date: 05/24/2018
ms.openlocfilehash: c7e102b6b8976f8caf7676fd456e6c23488c935a
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932285"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>ISM ürünlerini/hizmetlerini BT Hizmet Yönetimi Bağlayıcısı bağlama
Bu makalede, çalışma öğelerinizi merkezi olarak yönetmek için ıTSM ürününüz/hizmetiniz ile Log Analytics BT Hizmet Yönetimi Bağlayıcısı (ITSMC) arasındaki bağlantının nasıl yapılandırılacağı hakkında bilgi sağlanır. ISMC hakkında daha fazla bilgi için bkz. [genel bakış](../../azure-monitor/platform/itsmc-overview.md).

Aşağıdaki ıTSM ürünleri/hizmetleri desteklenir. Ürünün ıSMC 'a nasıl bağlanacağı hakkında ayrıntılı bilgileri görüntülemek için ürünü seçin.

- [System Center Service Manager](#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](#connect-provance-to-it-service-management-connector-in-azure)
- [Cherwell](#connect-cherwell-to-it-service-management-connector-in-azure)

> [!NOTE]
> 
> ITSM Bağlayıcısı, yalnızca bulut tabanlı ServiceNow örneklerine bağlanabilir. Şirket içi ServiceNow örnekleri şu anda desteklenmiyor.

## <a name="connect-system-center-service-manager-to-it-service-management-connector-in-azure"></a>Azure 'da BT Hizmet Yönetimi Bağlayıcısı System Center Service Manager bağlama

Aşağıdaki bölümlerde, System Center Service Manager ürününüzü Azure 'daki ıSMC 'a bağlama hakkında ayrıntılı bilgi sağlanmaktadır.

### <a name="prerequisites"></a>Önkoşullar

Aşağıdaki önkoşulların karşılandığından emin olun:

- ISMC yüklendi. Daha fazla bilgi: [BT hizmet yönetimi Bağlayıcısı çözümü ekleme](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Service Manager Web uygulaması (Web uygulaması) dağıtıldı ve yapılandırılır. Web uygulaması hakkında bilgi [burada](#create-and-deploy-service-manager-web-app-service)yer alır.
- Karma bağlantı oluşturuldu ve yapılandırıldı. Daha fazla bilgi: [karma bağlantıyı yapılandırın](#configure-the-hybrid-connection).
- Desteklenen Service Manager sürümleri: 2012 R2 veya 2016.
- Kullanıcı rolü: [Gelişmiş işleç](https://technet.microsoft.com/library/ff461054.aspx).

### <a name="connection-procedure"></a>Bağlantı yordamı

System Center Service Manager örneğinizi ıSMC 'a bağlamak için aşağıdaki yordamı kullanın:

1. Azure portal ' de **tüm kaynaklar** ' a gidin ve **ServiceDesk (yourçalışmaalanıadı)** araması yapın

2.  **Çalışma alanı VERI kaynakları** altında **ITSM bağlantıları**' na tıklayın.

    ![Yeni bağlantı](media/itsmc-connections/add-new-itsm-connection.png)

3. Sağ bölmenin üst kısmındaki **Ekle**' ye tıklayın.

4. Aşağıdaki tabloda açıklandığı gibi bilgileri sağlayın ve bağlantıyı oluşturmak için **Tamam** ' ı tıklatın.

> [!NOTE]
> 
> Tüm bu parametreler zorunludur.

| **Alan** | **Açıklama** |
| --- | --- |
| **Bağlantı Adı**   | ISMC ile bağlanmak istediğiniz System Center Service Manager örneği için bir ad yazın.  Bu adı daha sonra bu örnekteki iş öğelerini yapılandırırken veya ayrıntılı Log Analytics 'i görüntülerken kullanırsınız. |
| **İş ortağı türü**   | **System Center Service Manager**seçin. |
| **Sunucu URL 'SI**   | Service Manager Web uygulamasının URL 'sini yazın. Service Manager Web uygulaması hakkında daha fazla bilgi [burada](#create-and-deploy-service-manager-web-app-service)verilmiştir.
| **İstemci KIMLIĞI**   | Web uygulamasının kimliğini doğrulamak için oluşturduğunuz istemci KIMLIĞINI (otomatik betiği kullanarak) yazın. Otomatik betik hakkında daha fazla bilgi [burada verilmiştir.](../../azure-monitor/platform/itsmc-service-manager-script.md)|
| **İstemci parolası**   | Bu KIMLIK için oluşturulan istemci parolasını yazın.   |
| **Verileri eşitleme**   | ITSMC aracılığıyla eşitlemek istediğiniz Service Manager iş öğelerini seçin.  Bu iş öğeleri Log Analytics içine aktarılır. **Seçenekler:**  Olaylar, değişiklik Istekleri.|
| **Veri eşitleme kapsamı** | Verilerin içinden istediğiniz geçmiş gün sayısını yazın. **Maksimum sınır**: 120 gün. |
| **ITSM çözümünde yeni yapılandırma öğesi oluştur** | ITSM ürününde yapılandırma öğelerini oluşturmak istiyorsanız bu seçeneği belirleyin. Seçildiğinde Log Analytics, desteklenen ıTSM sisteminde, etkilenen CIS 'yi yapılandırma öğeleri (mevcut olmayan CIS olması durumunda) olarak oluşturur. **Varsayılan**: devre dışı. |

![Service Manager bağlantısı](media/itsmc-connections/service-manager-connection.png)

**Başarıyla bağlandığında ve eşitlendiğinde**:

- Service Manager seçilen iş öğeleri Azure Log Analytics içeri aktarılmalıdır **.** BT Hizmet Yönetimi Bağlayıcısı kutucuğunda bu iş öğelerinin özetini görüntüleyebilirsiniz.

- Log Analytics uyarılarından veya günlük kayıtlarından veya bu Service Manager örneğindeki Azure uyarılarından olaylar oluşturabilirsiniz.


Daha fazla bilgi: [Azure uyarılarından ITSM iş öğeleri oluşturun](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-and-deploy-service-manager-web-app-service"></a>Service Manager Web App Service oluşturun ve dağıtın

Şirket içi Service Manager Azure 'daki ıSMC ile bağlamak için, Microsoft GitHub 'da bir Service Manager Web uygulaması oluşturmuştur.

Service Manager için ıTSM Web uygulamasını ayarlamak için aşağıdakileri yapın:

- **Web uygulamasını dağıtın** – Web uygulamasını dağıtın, özellikleri ayarlayın ve Azure AD ile kimlik doğrulaması yapın. Web uygulamasını, Microsoft 'un size verdiği [Otomatikleştirilmiş betiği](../../azure-monitor/platform/itsmc-service-manager-script.md) kullanarak dağıtabilirsiniz.
- **Karma bağlantıyı yapılandırın** - [Bu bağlantıyı](#configure-the-hybrid-connection)el ile yapılandırın.

#### <a name="deploy-the-web-app"></a>Web uygulamasını dağıtma
Web uygulamasını dağıtmak, özellikleri ayarlamak ve Azure AD ile kimlik doğrulaması yapmak için otomatikleştirilmiş [betiği](../../azure-monitor/platform/itsmc-service-manager-script.md) kullanın.

Aşağıdaki gerekli ayrıntıları sağlayarak betiği çalıştırın:

- Azure abonelik ayrıntıları
- Kaynak grubu adı
- Konum
- Sunucu ayrıntılarını Service Manager (sunucu adı, etki alanı, Kullanıcı adı ve parola)
- Web uygulamanız için site adı ön eki
- ServiceBus ad alanı.

Betik, belirttiğiniz adı kullanarak Web uygulamasını oluşturur (benzersiz hale getirmek için birkaç ek dize ile birlikte). **Web uygulaması URL 'sini**, **istemci kimliğini** ve **istemci gizli**anahtarını oluşturur.

Değerleri kaydedin, ıSMC ile bir bağlantı oluşturduğunuzda bunları kullanırsınız.

**Web uygulaması yüklemesini denetleyin**

1. **Azure portal** > **kaynaklara**gidin.
2. Web uygulamasını seçin, **ayarlar** > **uygulama ayarları**' na tıklayın.
3. Uygulamayı betik aracılığıyla dağıtma sırasında verdiğiniz Service Manager örneğiyle ilgili bilgileri onaylayın.

### <a name="configure-the-hybrid-connection"></a>Karma bağlantıyı yapılandırma

Service Manager örneğini Azure 'daki ıSMC ile bağlayan karma bağlantıyı yapılandırmak için aşağıdaki yordamı kullanın.

1. **Azure kaynakları**altında Service Manager Web uygulamasını bulun.
2. **Ağ** > **Ayarlar** ' a tıklayın.
3. **Karma bağlantılar**altında **karma bağlantı uç noktalarınızı yapılandırın**' ı tıklatın.

    ![Karma bağlantı ağı](media/itsmc-connections/itsmc-hybrid-connection-networking-and-end-points.png)
4. **Karma bağlantılar** dikey penceresinde **karma bağlantı ekle**' ye tıklayın.

    ![Karma bağlantı ekle](media/itsmc-connections/itsmc-new-hybrid-connection-add.png)

5. **Karma bağlantılar Ekle** dikey penceresinde **yeni karma bağlantı oluştur**' a tıklayın.

    ![Yeni karma bağlantı](media/itsmc-connections/itsmc-create-new-hybrid-connection.png)

6. Aşağıdaki değerleri yazın:

   - **Uç nokta adı**: yeni karma bağlantı için bir ad belirtin.
   - **Uç nokta ana bilgisayarı**: Service Manager yönetim sunucusunun FQDN 'si.
   - **Uç nokta bağlantı noktası**: tür 5724
   - **ServiceBus ad alanı**: mevcut bir ServiceBus ad alanını kullanın veya yeni bir tane oluşturun.
   - **Konum**: konumu seçin.
   - **Ad**: Eğer oluşturuyorsanız ServiceBus için bir ad belirtin.

     ![Karma bağlantı değerleri](media/itsmc-connections/itsmc-new-hybrid-connection-values.png)
6. **Karma bağlantı oluştur** dikey penceresini kapatmak ve karma bağlantıyı oluşturmaya başlamak için **Tamam** ' ı tıklatın.

    Karma bağlantı oluşturulduktan sonra dikey pencerenin altında görüntülenir.

7. Karma bağlantı oluşturulduktan sonra bağlantıyı seçin ve **Seçili karma bağlantı ekle**' ye tıklayın.

    ![Yeni karma bağlantı](media/itsmc-connections/itsmc-new-hybrid-connection-added.png)

#### <a name="configure-the-listener-setup"></a>Dinleyici kurulumunu yapılandırma

Karma bağlantı için dinleyici kurulumunu yapılandırmak üzere aşağıdaki yordamı kullanın.

1. **Karma bağlantılar** dikey penceresinde **bağlantı yöneticisini indir** ' e tıklayın ve System Center Service Manager örneğinin çalıştığı makineye yükleyin.

    Yükleme tamamlandıktan sonra, **karma bağlantı YÖNETICISI UI** seçeneği **Başlat** menüsünde kullanılabilir.

2. **Karma bağlantı Yöneticisi Kullanıcı arabirimi** ' ne tıklayın, sizden Azure kimlik bilgileriniz istenir.

3. Azure kimlik bilgilerinizle oturum açın ve karma bağlantının oluşturulduğu aboneliğinizi seçin.

4. **Kaydet** düğmesine tıklayın.

Karma bağlantınız başarıyla bağlandı.

![karma bağlantı başarıyla oluşturuldu](media/itsmc-connections/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]
> 
> Karma bağlantı oluşturulduktan sonra, dağıtılan Service Manager Web uygulamasını ziyaret ederek bağlantıyı doğrulayıp test edin. Azure 'da ıSMC 'a bağlanmayı denemeden önce bağlantının başarılı olduğundan emin olun.

Aşağıdaki örnek görüntüde başarılı bir bağlantının ayrıntıları gösterilmektedir:

![Karma bağlantı sınaması](media/itsmc-connections/itsmc-hybrid-connection-test.png)

## <a name="connect-servicenow-to-it-service-management-connector-in-azure"></a>ServiceNow 'ı Azure 'da BT Hizmet Yönetimi Bağlayıcısı bağlama

Aşağıdaki bölümlerde, ServiceNow ürününüzü Azure 'daki ıSMC 'a bağlama hakkında ayrıntılı bilgi sağlanmaktadır.

### <a name="prerequisites"></a>Önkoşullar
Aşağıdaki önkoşulların karşılandığından emin olun:
- ISMC yüklendi. Daha fazla bilgi: [BT hizmet yönetimi Bağlayıcısı çözümü ekleme](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- ServiceNow desteklenen sürümler: Madrid, Londra, Kingston, Jakarta, Istanbul, Helsinki, Genfiliz.

**ServiceNow yöneticilerinin ServiceNow örneğinde aşağıdakileri yapması gerekir**:
- ServiceNow ürünü için istemci KIMLIĞI ve istemci gizli dizisi oluşturun. İstemci KIMLIĞI ve gizli dizi oluşturma hakkında bilgi için, aşağıdaki bilgileri gerekli şekilde inceleyin:

    - [Madrid için OAuth ayarlama](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Londra için OAuth ayarlama](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Kingston için OAuth ayarla](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Cakarta için OAuth ayarlama](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Istanbul için OAuth ayarlama](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Helsinki için OAuth ayarlama](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Genfiliz için OAuth ayarlama](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)


- Microsoft Log Analytics Integration (ServiceNow uygulaması) için Kullanıcı uygulamasını yükler. [Daha fazla bilgi edinin](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 ).
- Yüklü kullanıcı uygulaması için tümleştirme Kullanıcı rolü oluşturun. Tümleştirme Kullanıcı rolünü oluşturma hakkında bilgi [burada](#create-integration-user-role-in-servicenow-app)verilmiştir.

### <a name="connection-procedure"></a>**Bağlantı yordamı**
Bir ServiceNow bağlantısı oluşturmak için aşağıdaki yordamı kullanın:


1. Azure portal ' de **tüm kaynaklar** ' a gidin ve **ServiceDesk (yourçalışmaalanıadı)** araması yapın

2.  **Çalışma alanı VERI kaynakları** altında **ITSM bağlantıları**' na tıklayın.
    Yeni bağlantı ![](media/itsmc-connections/add-new-itsm-connection.png)

3. Sağ bölmenin üst kısmındaki **Ekle**' ye tıklayın.

4. Aşağıdaki tabloda açıklandığı gibi bilgileri sağlayın ve bağlantıyı oluşturmak için **Tamam** ' ı tıklatın.


> [!NOTE]
> Tüm bu parametreler zorunludur.

| **Alan** | **Açıklama** |
| --- | --- |
| **Bağlantı Adı**   | ISMC ile bağlanmak istediğiniz ServiceNow örneği için bir ad yazın.  Bu ıTSM 'de iş öğelerini yapılandırdığınızda ve ayrıntılı Log Analytics 'i görüntülemek için bu adı daha sonra Log Analytics ' de kullanırsınız. |
| **İş ortağı türü**   | **ServiceNow**' ı seçin. |
| **Kullanıcı Adı**   | ISMC bağlantısını desteklemek için ServiceNow uygulamasında oluşturduğunuz tümleştirme Kullanıcı adını yazın. Daha fazla bilgi: [ServiceNow uygulaması kullanıcı rolü oluşturun](#create-integration-user-role-in-servicenow-app).|
| **Parola**   | Bu kullanıcı adıyla ilişkili parolayı yazın. **Not**: Kullanıcı adı ve parola yalnızca kimlik doğrulama belirteçleri oluşturmak için kullanılır ve ısmc hizmetinin içinde herhangi bir yerde depolanmaz.  |
| **Sunucu URL 'SI**   | ISMC 'a bağlamak istediğiniz ServiceNow örneğinin URL 'sini yazın. |
| **İstemci KIMLIĞI**   | Daha önce oluşturduğunuz OAuth2 kimlik doğrulaması için kullanmak istediğiniz istemci KIMLIĞINI yazın.  İstemci KIMLIĞI ve gizli anahtar oluşturma hakkında daha fazla bilgi: [OAuth kurulumu](https://wiki.servicenow.com/index.php?title=OAuth_Setup). |
| **İstemci parolası**   | Bu KIMLIK için oluşturulan istemci parolasını yazın.   |
| **Veri eşitleme kapsamı**   | ISMC aracılığıyla Azure Log Analytics eşitlemek istediğiniz ServiceNow iş öğelerini seçin.  Seçilen değerler Log Analytics 'e aktarılır.   **Seçenekler:**  Olaylar ve değişiklik Istekleri.|
| **Verileri eşitleme** | Verilerin içinden istediğiniz geçmiş gün sayısını yazın. **Maksimum sınır**: 120 gün. |
| **ITSM çözümünde yeni yapılandırma öğesi oluştur** | ITSM ürününde yapılandırma öğelerini oluşturmak istiyorsanız bu seçeneği belirleyin. Seçildiğinde, ıSMC, desteklenen ıTSM sisteminde, etkilenen CIS 'yi yapılandırma öğeleri (mevcut olmayan CIS olması durumunda) olarak oluşturur. **Varsayılan**: devre dışı. |

![ServiceNow bağlantısı](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

**Başarıyla bağlandığında ve eşitlendiğinde**:

- ServiceNow örneğinden seçilen iş öğeleri Azure Log Analytics içeri aktarılmalıdır **.** BT Hizmet Yönetimi Bağlayıcısı kutucuğunda bu iş öğelerinin özetini görüntüleyebilirsiniz.

- Log Analytics uyarılarından veya günlük kayıtlarından ya da bu ServiceNow örneğindeki Azure uyarılarından olaylar oluşturabilirsiniz.

Daha fazla bilgi: [Azure uyarılarından ITSM iş öğeleri oluşturun](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-integration-user-role-in-servicenow-app"></a>ServiceNow uygulaması 'nda tümleştirme Kullanıcı rolü oluşturma

Aşağıdaki yordamı Kullanıcı:

1. ServiceNow [mağazasını](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) ziyaret edin ve ServiceNow örneğiniz **Için Kullanıcı uygulamasını ServiceNow ve Microsoft OMS tümleştirmesi için** yüklemeyi yapın.
   
   >[!NOTE]
   >Microsoft Operations Management Suite (OMS) ile Azure Izleyici arasında devam eden geçişin bir parçası olarak, OMS artık Log Analytics olarak adlandırılır.     
2. Yüklemeden sonra ServiceNow örneğinin sol gezinti çubuğunu ziyaret edin, arama yapın ve Microsoft OMS tümleştirici ' i seçin.  
3. **Yükleme denetim listesi**' ne tıklayın.

   Kullanıcı rolü henüz oluşturulmadıysa durum **tamamlanmamış** olarak görüntülenir.

4. Metin kutularında, **tümleştirme kullanıcısı oluştur**' un yanındaki Azure 'DA ısmc 'a bağlanabilecek kullanıcı için Kullanıcı adını girin.
5. Bu kullanıcının parolasını girin ve **Tamam**' a tıklayın.  

> [!NOTE]
> 
> Azure 'da ServiceNow bağlantısı kurmak için bu kimlik bilgilerini kullanırsınız.

Yeni oluşturulan kullanıcı varsayılan rollerle birlikte görüntülenir.

**Varsayılan roller**:
- personalize_choices
- import_transformer
-   x_mioms_microsoft. User
-   ITIL
-   template_editor
-   view_changer

Kullanıcı başarılı bir şekilde oluşturulduktan sonra, **yükleme denetim listesinin** durumu tamamlandı olarak getirilir ve uygulama için oluşturulan kullanıcı rolünün ayrıntıları denetlenir.

> [!NOTE]
> 
> ITSM Bağlayıcısı, ServiceNow Örneğinizde yüklü başka modüller olmadan ServiceNow 'a olay gönderebilir. ServiceNow Örneğinizde EventManagement modülünü kullanıyorsanız ve bağlayıcıyı kullanarak ServiceNow 'da olay veya uyarı oluşturmak istiyorsanız, tümleştirme kullanıcısına aşağıdaki rolleri ekleyin:
> 
>    - evt_mgmt_integration
>    - evt_mgmt_operator  


## <a name="connect-provance-to-it-service-management-connector-in-azure"></a>Azure 'da BT Hizmet Yönetimi Bağlayıcısı Provance 'e bağlanma

Aşağıdaki bölümlerde, Provance ürününüzü Azure 'daki ıSMC 'a bağlama hakkında ayrıntılı bilgi sağlanmaktadır.


### <a name="prerequisites"></a>Önkoşullar

Aşağıdaki önkoşulların karşılandığından emin olun:


- ISMC yüklendi. Daha fazla bilgi: [BT hizmet yönetimi Bağlayıcısı çözümü ekleme](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Provance uygulamasının Azure AD 'ye kayıtlı olması gerekir ve istemci KIMLIĞI kullanılabilir hale getirilir. Ayrıntılı bilgi için bkz. [Active Directory kimlik doğrulamasını yapılandırma](../../app-service/configure-authentication-provider-aad.md).

- Kullanıcı rolü: yönetici.

### <a name="connection-procedure"></a>Bağlantı yordamı

Bir Provance bağlantısı oluşturmak için aşağıdaki yordamı kullanın:

1. Azure portal ' de **tüm kaynaklar** ' a gidin ve **ServiceDesk (yourçalışmaalanıadı)** araması yapın

2.  **Çalışma alanı VERI kaynakları** altında **ITSM bağlantıları**' na tıklayın.
    Yeni bağlantı ![](media/itsmc-connections/add-new-itsm-connection.png)

3. Sağ bölmenin üst kısmındaki **Ekle**' ye tıklayın.

4. Aşağıdaki tabloda açıklandığı gibi bilgileri sağlayın ve bağlantıyı oluşturmak için **Tamam** ' ı tıklatın.

> [!NOTE]
> 
> Tüm bu parametreler zorunludur.

| **Alan** | **Açıklama** |
| --- | --- |
| **Bağlantı Adı**   | ISMC ile bağlanmak istediğiniz Provance örneği için bir ad yazın.  Bu adı daha sonra bu ıTSM 'de iş öğelerini yapılandırdığınızda ve ayrıntılı Log Analytics 'i görüntülemek için kullanabilirsiniz. |
| **İş ortağı türü**   | **Provance**' ı seçin. |
| **Kullanıcı Adı**   | ISMC 'a bağlanabilecek kullanıcı adını yazın.    |
| **Parola**   | Bu kullanıcı adıyla ilişkili parolayı yazın. **Note:** Kullanıcı adı ve parola yalnızca kimlik doğrulama belirteçleri oluşturmak için kullanılır ve ıSMC hizmetinin içinde herhangi bir yerde depolanmaz. _|
| **Sunucu URL 'SI**   | ISMC 'a bağlamak istediğiniz Provance örneğinizin URL 'sini yazın. |
| **İstemci KIMLIĞI**   | Bu bağlantının kimliğini doğrulamak için gereken istemci KIMLIĞINI yazın ve bu, Provance Örneğinizde oluşturduğunuz.  İstemci KIMLIĞI hakkında daha fazla bilgi için bkz. [Active Directory kimlik doğrulamasını yapılandırma](../../app-service/configure-authentication-provider-aad.md). |
| **Veri eşitleme kapsamı**   | ITSMC aracılığıyla Azure Log Analytics eşitlemek istediğiniz Provance iş öğelerini seçin.  Bu iş öğeleri Log Analytics 'e aktarılır.   **Seçenekler:**   Olaylar, değişiklik Istekleri.|
| **Verileri eşitleme** | Verilerin içinden istediğiniz geçmiş gün sayısını yazın. **Maksimum sınır**: 120 gün. |
| **ITSM çözümünde yeni yapılandırma öğesi oluştur** | ITSM ürününde yapılandırma öğelerini oluşturmak istiyorsanız bu seçeneği belirleyin. Seçildiğinde, ıSMC, desteklenen ıTSM sisteminde, etkilenen CIS 'yi yapılandırma öğeleri (mevcut olmayan CIS olması durumunda) olarak oluşturur. **Varsayılan**: devre dışı.|

![Provance bağlantısı](media/itsmc-connections/itsm-connections-provance-latest.png)

**Başarıyla bağlandığında ve eşitlendiğinde**:

- Bu Provance örneğinden seçilen iş öğeleri Azure Log Analytics içeri aktarılmalıdır **.** BT Hizmet Yönetimi Bağlayıcısı kutucuğunda bu iş öğelerinin özetini görüntüleyebilirsiniz.

- Log Analytics uyarılarından veya günlük kayıtlarından ya da bu Provance örneğindeki Azure uyarılarından olaylar oluşturabilirsiniz.

Daha fazla bilgi: [Azure uyarılarından ITSM iş öğeleri oluşturun](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

## <a name="connect-cherwell-to-it-service-management-connector-in-azure"></a>Azure 'daki BT Hizmet Yönetimi Bağlayıcısı Cherwell 'e bağlama

Aşağıdaki bölümlerde, Cherwell ürününüzü Azure 'daki ıSMC 'a bağlama hakkında ayrıntılı bilgi sağlanmaktadır.

### <a name="prerequisites"></a>Önkoşullar

Aşağıdaki önkoşulların karşılandığından emin olun:

- ISMC yüklendi. Daha fazla bilgi: [BT hizmet yönetimi Bağlayıcısı çözümü ekleme](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- İstemci KIMLIĞI oluşturuldu. Daha fazla bilgi: [Cherwell için ISTEMCI kimliği oluşturma](#generate-client-id-for-cherwell).
- Kullanıcı rolü: yönetici.

### <a name="connection-procedure"></a>Bağlantı yordamı

Bir Provance bağlantısı oluşturmak için aşağıdaki yordamı kullanın:

1. Azure portal ' de **tüm kaynaklar** ' a gidin ve **ServiceDesk (yourçalışmaalanıadı)** araması yapın

2.  **Çalışma alanı VERI kaynakları** altında **ITSM bağlantıları**' na tıklayın.
    Yeni bağlantı ![](media/itsmc-connections/add-new-itsm-connection.png)

3. Sağ bölmenin üst kısmındaki **Ekle**' ye tıklayın.

4. Aşağıdaki tabloda açıklandığı gibi bilgileri sağlayın ve bağlantıyı oluşturmak için **Tamam** ' ı tıklatın.

> [!NOTE]
> 
> Tüm bu parametreler zorunludur.

| **Alan** | **Açıklama** |
| --- | --- |
| **Bağlantı Adı**   | ISMC 'a bağlamak istediğiniz Cherwell örneği için bir ad yazın.  Bu adı daha sonra bu ıTSM 'de iş öğelerini yapılandırdığınızda ve ayrıntılı Log Analytics 'i görüntülemek için kullanabilirsiniz. |
| **İş ortağı türü**   | **Cherwell** öğesini seçin. |
| **Kullanıcı Adı**   | ISMC 'a bağlanabilecek Cherwell Kullanıcı adını yazın. |
| **Parola**   | Bu kullanıcı adıyla ilişkili parolayı yazın. **Note:** Kullanıcı adı ve parola yalnızca kimlik doğrulama belirteçleri oluşturmak için kullanılır ve ıSMC hizmetinin içinde herhangi bir yerde depolanmaz.|
| **Sunucu URL 'SI**   | ISMC 'a bağlamak istediğiniz Cherwell örneğinizin URL 'sini yazın. |
| **İstemci KIMLIĞI**   | Bu bağlantının kimliğini doğrulamak için, Cherwell örneğiniz içinde oluşturduğunuz istemci KIMLIĞINI yazın.   |
| **Veri eşitleme kapsamı**   | ITSMC aracılığıyla eşitlemek istediğiniz Cherwell iş öğelerini seçin.  Bu iş öğeleri Log Analytics 'e aktarılır.   **Seçenekler:**  Olaylar, değişiklik Istekleri. |
| **Verileri eşitleme** | Verilerin içinden istediğiniz geçmiş gün sayısını yazın. **Maksimum sınır**: 120 gün. |
| **ITSM çözümünde yeni yapılandırma öğesi oluştur** | ITSM ürününde yapılandırma öğelerini oluşturmak istiyorsanız bu seçeneği belirleyin. Seçildiğinde, ıSMC, desteklenen ıTSM sisteminde, etkilenen CIS 'yi yapılandırma öğeleri (mevcut olmayan CIS olması durumunda) olarak oluşturur. **Varsayılan**: devre dışı. |


![Provance bağlantısı](media/itsmc-connections/itsm-connections-cherwell-latest.png)

**Başarıyla bağlandığında ve eşitlendiğinde**:

- Bu Cherwell örneğinden seçilen iş öğeleri Azure Log Analytics içeri aktarılmaktadır **.** BT Hizmet Yönetimi Bağlayıcısı kutucuğunda bu iş öğelerinin özetini görüntüleyebilirsiniz.

- Log Analytics uyarılarından veya günlük kayıtlarından veya bu Cherwell örneğindeki Azure uyarılarından olaylar oluşturabilirsiniz.

Daha fazla bilgi: [Azure uyarılarından ITSM iş öğeleri oluşturun](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="generate-client-id-for-cherwell"></a>Cherwell için istemci KIMLIĞI oluşturma

Cherwell için istemci KIMLIĞINI/anahtarını oluşturmak için aşağıdaki yordamı kullanın:

1. Yönetici olarak Cherwell örneğiniz üzerinde oturum açın.
2. **İstemci ayarlarını REST API** **güvenlik** > Düzenle ' ye tıklayın.
3. **Yeni istemci** > **istemci parolası**oluştur ' u seçin.

    ![Cherwell Kullanıcı kimliği](media/itsmc-connections/itsmc-cherwell-client-id.png)


## <a name="next-steps"></a>Sonraki adımlar
 - [Azure uyarılarından ıTSM iş öğeleri oluşturma](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts)
