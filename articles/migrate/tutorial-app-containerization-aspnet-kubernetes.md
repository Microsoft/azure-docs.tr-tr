---
title: Azure Uygulama Kapsayıcılama ASP.NET; ASP.NET uygulamalarının Azure Kubernetes 'e kapsayıcılama ve geçişi.
description: 'Öğretici: ASP.NET uygulamalarını Azure Kubernetes hizmetine geçirme &.'
services: ''
author: rahugup
manager: bsiva
ms.topic: tutorial
ms.date: 3/2/2021
ms.author: rahugup
ms.openlocfilehash: 464e2450b4d4dea9fc650ad8869af4215d3db1a7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561806"
---
# <a name="aspnet-app-containerization-and-migration-to-azure-kubernetes-service"></a>Azure Kubernetes hizmetine ASP.NET uygulama kapsayıcılama ve geçiş

Bu makalede, Azure geçişi: uygulama Kapsayıcılama aracı 'nı kullanarak ASP.NET uygulamalarını kapsayılaştırma ve [Azure Kubernetes Service 'e (AKS)](https://azure.microsoft.com/services/kubernetes-service/) geçirme hakkında bilgi edineceksiniz. Kapsayıcı oluşturma işlemi, kod tabanınıza erişim gerektirmez ve mevcut uygulamaları kapsayıtabilecek kolay bir yol sağlar. Araç, uygulama bileşenlerini belirleyebilmek ve bunları bir kapsayıcı görüntüsünde paketlemenize yardımcı olmak için bir sunucudaki uygulamaların çalışma durumunu kullanarak çalışır. Kapsayıcılı uygulama daha sonra Azure Kubernetes Service (AKS) üzerinde dağıtılabilir.

Azure geçişi: uygulama Kapsayıcılama aracı şu anda şunları destekler:

- ASP.NET uygulamalarını kapsayıyor ve AKS üzerinde Windows kapsayıcılarına dağıtma.
- Apache Tomcat 'te Java Web Apps kapsayıyor (Linux sunucularında) ve bunları AKS üzerinde Linux kapsayıcılarına dağıtma. [Daha fazla bilgi edinin](./tutorial-containerize-java-kubernetes.md)


Azure geçişi: uygulama Kapsayıcılama Aracı, şunları yapmanıza yardımcı olur:

- **Uygulamanızı bulun**: araç, ASP.NET uygulamanızı çalıştıran uygulama sunucularına uzaktan bağlanır ve uygulama bileşenlerini bulur. Araç, uygulama için bir kapsayıcı görüntüsü oluşturmak üzere kullanılabilecek bir Dockerfile oluşturur.
- **Kapsayıcı görüntüsünü oluşturma**: Dockerfile 'ı uygulama gereksinimlerinize göre inceleyebilir ve daha fazla özelleştirebilir ve bunu uygulama kapsayıcısı görüntünüzü oluşturmak için kullanabilirsiniz. Uygulama kapsayıcı görüntüsü, belirttiğiniz bir Azure Container Registry gönderilir.
- **Azure Kubernetes hizmetine dağıtma**: araç daha sonra kapsayıcılı uygulamayı Azure Kubernetes hizmet kümenize dağıtmak Için gereken Kubernetes kaynak tanımı YAML dosyalarını oluşturur. YAML dosyalarını özelleştirebilir ve uygulamayı AKS 'te dağıtmak için kullanabilirsiniz.

> [!NOTE]
> Azure geçişi: uygulama Kapsayıcılama Aracı, belirli uygulama türlerini (Apache Tomcat 'te ASP.NET ve Java Web Apps) ve bunların bileşenlerini bir uygulama sunucusuna keşfetmenize yardımcı olur. Sunucuları ve şirket içi makinelerde çalışan uygulamaların, rollerin ve özelliklerin envanterini bulmak için Azure geçişi: bulma ve değerlendirme özelliği ' ni kullanın. [Daha fazla bilgi edinin](./tutorial-discover-vmware.md)

Tüm uygulamalar, önemli ölçüde yeniden mimari olmadan tek bir kaymadan kapsayıcılara avantajlarına sahip olmasa da, var olan uygulamaları yeniden yazmadan kapsayıcılara taşımaya faydaların bazıları şunlardır:

- **Geliştirilmiş altyapı kullanımı:** Kapsayıcılarla, birden çok uygulama kaynakları paylaşabilir ve aynı altyapıda barındırılabilir. Bu, altyapıyı birleştirip kullanımı iyileştirmenize yardımcı olabilir.
- **Basitleştirilmiş Yönetim:** Uygulamalarınızı AKS gibi modern bir yönetilen altyapı platformunda barındırarak, altyapınız üzerinde denetim sahibi olmaya devam ederken yönetim uygulamalarınızı basitleştirebilirsiniz. Bunu, sahip olduğu altyapı bakımı ve yönetim süreçlerini kullanımdan kaldırarak veya azaltarak elde edersiniz.
- **Uygulama taşınabilirliği:** Kapsayıcı belirtim biçimlerinin ve düzenleme platformlarının arttığı ve standartlaştırmasıyla, uygulama taşınabilirliği artık sorun değildir.
- **DevOps ile modern yönetimi benimseyin:** Kod olarak altyapı ve DevOps 'a geçiş ile yönetim ve güvenlik için modern uygulamaları benimsemenize ve standartlaştırmanıza yardımcı olur.


Bu öğreticide aşağıdakilerin nasıl yapılacağını öğreneceksiniz:

> [!div class="checklist"]
> * Bir Azure hesabı ayarlayın.
> * Azure geçişi: uygulama Kapsayıcılama aracı 'nı yükler.
> * ASP.NET uygulamanızı bulun.
> * Kapsayıcı görüntüsünü oluşturun.
> * Kapsayıcılı uygulamayı AKS üzerinde dağıtın.

> [!NOTE]
> Öğreticiler, bir senaryo için en basit dağıtım yolunu gösterir, böylece bir kavram kanıtı hızlı bir şekilde ayarlayabilmenizi sağlayabilirsiniz. Öğreticiler mümkün olduğunca varsayılan seçenekleri kullanır ve tüm olası ayarları ve yolları göstermez.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce karşılamanız gereken ön koşullar şunlardır:

**Gereksinim** | **Ayrıntılar**
--- | ---
**Aracı yüklemek için bir makine tanımla** | Azure geçişi: uygulama Kapsayıcılama aracı 'nı yüklemek ve çalıştırmak için bir Windows makinesi. Windows makinesi bir sunucu (Windows Server 2016 veya üzeri) veya istemci (Windows 10) işletim sistemi olabilir. Bu, aracın masaüstünüzde da çalıştırılabileceği anlamına gelir. <br/><br/> Aracı çalıştıran Windows makinesinin, kapsayıcıya alınacak ASP.NET uygulamalarını barındıran sunuculara/sanal makinelere ağ bağlantısı olmalıdır.<br/><br/> Uygulama yapıtlarını depolamak için Azure geçişi: uygulama Kapsayıcılama aracı 'nı çalıştıran Windows makinesinde 6 GB 'lık alanın kullanılabilir olduğundan emin olun. <br/><br/> Windows makinesinin doğrudan veya ara sunucu üzerinden İnternet erişimi olmalıdır. <br/> <br/>Uygulama Kapsayıcılama Yardımcısı aracı 'nı çalıştıran makineye Microsoft Web Dağıtımı aracını ve henüz yüklenmemişse uygulama sunucusunu yükleme. Aracı [buradan](https://aka.ms/webdeploy3.6) indirebilirsiniz
**Uygulama sunucuları** | Uygulama sunucularında PowerShell uzaktan iletişimini etkinleştir: uygulama sunucusunda oturum açın ve PowerShell uzaktan iletişimini açmak için [Bu](/powershell/module/microsoft.powershell.core/enable-psremoting) yönergeleri izleyin. <br/><br/> Uygulama sunucusu Windows Server 2008 R2 çalıştırıyorsa, PowerShell 5,1 ' nin uygulama sunucusunda yüklü olduğundan emin olun. Uygulama sunucusuna PowerShell 5,1 indirmek ve yüklemek için [buradaki](/powershell/scripting/windows-powershell/wmf/setup/install-configure) yönergeleri izleyin. <br/><br/> Uygulama Kapsayıcılama Yardımcısı aracı 'nı çalıştıran makineye Microsoft Web Dağıtımı aracını ve henüz yüklenmemişse uygulama sunucusunu yükleme. Aracı [buradan](https://aka.ms/webdeploy3.6) indirebilirsiniz
**ASP.NET uygulaması** | Araç şu anda destekliyor <br/><br/> -Microsoft .NET Framework 3,5 veya üstünü kullanan uygulamalar ASP.NET.<br/> -Windows Server 2008 R2 veya üstünü çalıştıran uygulama sunucuları (uygulama sunucuları PowerShell sürüm 5,1 çalıştırmalıdır). <br/> -Internet Information Services (IIS) 7,5 veya üzeri sürümlerde çalışan uygulamalar. <br/><br/> Araç şu anda desteklenmiyor <br/><br/> -Windows kimlik doğrulaması gerektiren uygulamalar (AKS Şu anda gMSA 'Yı desteklemez). <br/> -IIS dışında barındırılan diğer Windows hizmetlerine bağımlı uygulamalar.


## <a name="prepare-an-azure-user-account"></a>Azure Kullanıcı hesabı hazırlama

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturun.

Aboneliğiniz kurulduktan sonra, aşağıdakileri içeren bir Azure Kullanıcı hesabına sahip olmanız gerekir:
- Azure aboneliğinde sahip izinleri
- Azure Active Directory uygulamaları kaydetme izinleri

Ücretsiz Azure hesabı oluşturduysanız aboneliğinizin sahibi siz olursunuz. Abonelik sahibi değilseniz, izinleri aşağıdaki şekilde atamak için sahibiyle birlikte çalışın:

1. Azure portal, "abonelikler" araması yapın ve **Hizmetler** altında **abonelikler**' i seçin.

    ![Azure aboneliğini aramak için arama kutusu.](./media/tutorial-discover-vmware/search-subscription.png)

2. **Abonelikler** sayfasında, Azure geçişi projesi oluşturmak istediğiniz aboneliği seçin.
3. Abonelikte **erişim denetimi (IAM)**  >  **erişimi denetle**' yi seçin.
4. **Erişimi denetle**' de ilgili Kullanıcı hesabını arayın.
5. **Rol ataması Ekle**' de, **Ekle**' ye tıklayın.

    ![Erişimi denetlemek ve rol atamak için bir kullanıcı hesabı arayın.](./media/tutorial-discover-vmware/azure-account-access.png)

6. **Rol ataması Ekle**' de, sahip rolünü seçin ve hesabı (örneğimizde azmigrateuser) seçin. Daha sonra **Kaydet**'e tıklayın.

    ![Hesaba rol atamak için rol ataması Ekle sayfasını açar.](./media/tutorial-discover-vmware/assign-role.png)

7. Azure hesabınızın **Azure Active Directory uygulamaları kaydetme izinleri** de vardır.
8. Azure Portal ' de, **Azure Active Directory**  >  **kullanıcıları**  >  **Kullanıcı ayarları**' na gidin.
9. **Kullanıcı ayarları**' nda, Azure AD kullanıcılarının uygulamaları kaydedebildiğini doğrulayın (varsayılan olarak **Evet** ' e ayarlanır).

      ![Kullanıcıların Active Directory uygulamalar kaydedebildiğini Kullanıcı ayarlarında doğrulayın.](./media/tutorial-discover-vmware/register-apps.png)

10. ' Uygulama kayıtları ' ayarlarının ' No ' olarak ayarlanması durumunda, gerekli izni atamak için kiracı/genel yönetici isteyin. Alternatif olarak, kiracı/genel yönetici, Azure Active Directory uygulamasının kaydedilmesine izin vermek için **uygulama geliştirici** rolünü bir hesaba atayabilir. [Daha fazla bilgi edinin](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).


## <a name="download-and-install-azure-migrate-app-containerization-tool"></a>Azure geçişi 'ni indirme ve yükleme: uygulama Kapsayıcılama aracı

1. Azure geçişi: uygulama Kapsayıcılama yükleyicisini bir Windows makinesine [indirin](https://go.microsoft.com/fwlink/?linkid=2134571) .
2. PowerShell 'i yönetici modunda başlatın ve PowerShell dizinini yükleyiciyi içeren klasör olarak değiştirin.
3. Komutunu kullanarak yükleme betiğini çalıştırın

   ```powershell
   .\AppContainerizationInstaller.ps1
   ```

## <a name="launch-the-app-containerization-tool"></a>Uygulama Kapsayıcılama aracını Başlat

1. Uygulama Kapsayıcılama aracı 'nı çalıştıran Windows makinesine bağlanabilecek herhangi bir makinede bir tarayıcı açın ve araç URL 'sini açın: **https://*makine adı veya IP adresi*: 44368**.

   Alternatif olarak, uygulama kısayolunu seçerek uygulamayı masaüstünden açabilirsiniz.

2. Bağlantınızın özel olmadığını bildiren bir uyarı görürseniz, Gelişmiş ' e tıklayın ve Web sitesine devam etmek için seçin. Web arabirimi otomatik olarak imzalanan bir TLS/SSL sertifikası kullanıyorsa bu uyarı görüntülenir.
3. Oturum açma ekranında, oturum açmak için makinede yerel yönetici hesabını kullanın.
4. Uygulama türünü belirtin için, Kapsayıcılı hale getirmek istediğiniz uygulama türü olarak **ASP.NET Web Apps** ' i seçin.

    ![Uygulama Kapsayıcılama aracı için varsayılan yükleme.](./media/tutorial-containerize-apps-aks/tool-home.png)


### <a name="complete-tool-pre-requisites"></a>Tam araç önkoşulları
1. **Lisans koşullarını** kabul edin ve üçüncü taraf bilgilerini okuyun.
6. **Önkoşulları ayarlamak**> araç Web uygulamasında, aşağıdaki adımları uygulayın:
   - **Bağlantı**: araç, Windows makinenin internet erişimi olup olmadığını denetler. Makine bir proxy kullanıyorsa:
     - Proxy adresini (IP adresi veya FQDN biçiminde) ve dinleme bağlantı noktasını belirtmek için **proxy ayarla** ' ya tıklayın.
     - Proxy için kimlik doğrulaması gerekiyorsa kimlik bilgilerini gerekin.
     - Yalnızca HTTP proxy’si desteklenir.
     - Proxy ayrıntılarını eklediyseniz veya proxy ve/veya kimlik doğrulamasını devre dışı bırakırsanız, bağlantıyı tetiklemek için **Kaydet** 'e tıklayarak bağlantı denetimini yeniden başlatın.
   - **Güncelleştirmeleri yükler**: araç, en son güncelleştirmeleri otomatik olarak denetler ve yükler. Aracın en son sürümünü [buradan](https://go.microsoft.com/fwlink/?linkid=2134571)el ile de yükleyebilirsiniz.
   - **Microsoft Web dağıtımı aracını yükleme**: araç, Microsoft Web dağıtımı aracının Azure geçişi: uygulama kapsayıcılama aracı 'Nı çalıştıran Windows makinesinde yüklü olduğunu denetler.
   - **PowerShell uzaktan Iletişimini etkinleştir**: araç, Kapsayıcılı hale getirmek için ASP.NET uygulamalarını çalıştıran uygulama sunucularında PowerShell uzaktan Iletişimini etkin hale getirmek için size bildirir.


## <a name="log-in-to-azure"></a>Azure'da oturum açma

Azure hesabınızda oturum açmak için **oturum aç** ' a tıklayın.

1. Azure ile kimlik doğrulaması yapmak için bir cihaz kodu gerekir. Oturum açma seçeneğine tıkladığınızda cihaz kodu ile kalıcı olarak açılır.
2. Cihaz kodunu kopyalamak ve yeni bir tarayıcı sekmesinde bir Azure oturum açma istemi açmak için **kodu kopyala & oturum** aç ' a tıklayın. Görünmüyorsa, tarayıcıda açılır pencere engelleyicisini devre dışı bırakmış olduğunuzdan emin olun.

    ![Cihaz kodu kalıcı gösteriliyor.](./media/tutorial-containerize-apps-aks/login-modal.png)

3. Yeni sekmede, cihaz kodunu yapıştırın ve Azure hesabı kimlik bilgilerinizi kullanarak oturum açın. Oturum açma tamamlandıktan sonra tarayıcı sekmesini kapatabilir ve uygulama Kapsayıcılama aracının Web arabirimine geri dönün.
4. Kullanmak istediğiniz **Azure kiracısını** seçin.
5. Kullanmak istediğiniz **Azure aboneliğini** belirtin.

## <a name="discover-aspnet-applications"></a>ASP.NET uygulamalarını bulma

Uygulama Kapsayıcılama Yardımcısı Aracı, belirtilen kimlik bilgilerini kullanarak uygulama sunucularına uzaktan bağlanır ve uygulama sunucularında barındırılan ASP.NET uygulamalarını bulmaya çalışır.

1. Uygulama bulma için sunucuya uzaktan bağlanmak üzere kullanılması gereken ASP.NET uygulamasını çalıştıran sunucunun **IP adresini/FQDN 'sini ve kimlik bilgilerini** belirtin.
    - Sağlanan kimlik bilgileri uygulama sunucusundaki bir yerel yöneticiye (Windows) ait olmalıdır.
    - Etki alanı hesapları için (kullanıcının uygulama sunucusunda bir yönetici olması gerekir), Kullanıcı adını *<etkialanı \ kullanıcıadı>* biçiminde etki alanı adıyla önek yapın.
    - Uygulama bulmayı aynı anda en fazla beş sunucu için çalıştırabilirsiniz.

2. Uygulama sunucusuna aracı çalıştıran makineden erişilebildiğini ve kimlik bilgilerinin geçerli olduğunu doğrulamak için **Doğrula** ' ya tıklayın. Doğrulama başarıyla tamamlandığında durum sütunu, durumu **eşlendi** olarak gösterir.  

    ![Sunucu IP 'si ve kimlik bilgileri için ekran görüntüsü.](./media/tutorial-containerize-apps-aks/discovery-credentials.png)

3. Seçili uygulama sunucularında uygulama bulmayı başlatmak için **devam** ' a tıklayın.

4. Uygulama bulmayı başarıyla tamamladıktan sonra, Kapsayıcılı uygulamalar listesini seçebilirsiniz.

    ![Keşfedilen ASP.NET uygulamasının ekran görüntüsü.](./media/tutorial-containerize-apps-aks/discovered-app.png)


4. Konteynize yapılacak uygulamaları seçmek için onay kutusunu kullanın.
5. **Kapsayıcı adı belirtin**: seçili her uygulama için hedef kapsayıcı için bir ad belirtin. Kapsayıcı adı, etiketin kapsayıcı görüntüsü için kullanıldığı <*ad: tag*> olarak belirtilmelidir. Örneğin, hedef kapsayıcı adını *appname: v1* olarak belirtebilirsiniz.   

### <a name="parameterize-application-configurations"></a>Uygulama yapılandırmasını Parametreleştirme
Yapılandırma parametreleştirilmesi, bir dağıtım süresi parametresi olarak kullanılabilir hale gelir. Bu, kapsayıcıyı kapsayıcı görüntüsündeki belirli bir değere sabit olarak kodlanması aksine, uygulamayı dağıttığınızda yapılandırmanıza olanak tanır. Örneğin, bu seçenek veritabanı bağlantı dizeleri gibi parametreler için yararlıdır.
1. Algılanan konfigürasyonları gözden geçirmek için **uygulama yapılandırması** ' na tıklayın.
2. Algılanan uygulama yapılandırmasını parametreleştirmek için onay kutusunu seçin.
3. Parametreleştirme yapılandırması ' nı seçtikten sonra **Uygula** ' ya tıklayın.

   ![Uygulama yapılandırma parameterleştirme ASP.NET uygulamasının ekran görüntüsü.](./media/tutorial-containerize-apps-aks/discovered-app-configs.png)

### <a name="externalize-file-system-dependencies"></a>Externalize dosya sistemi bağımlılıkları

 Uygulamanızın kullandığı diğer klasörleri ekleyebilirsiniz. Azure dosya paylaşımındaki kalıcı birimler aracılığıyla kapsayıcı görüntüsünün parçası mı yoksa externalized mi olduğunu belirtin. Kalıcı birimleri kullanmak, durumu kapsayıcının dışında depolayan veya dosya sisteminde başka statik içerik bulunan durum bilgisi olan uygulamalar için harika bir şekilde çalışabilir. [Daha fazla bilgi edinin](../aks/concepts-storage.md)

1. Algılanan uygulama klasörlerini gözden geçirmek için uygulama klasörleri bölümünde **Düzenle** ' ye tıklayın. Algılanan uygulama klasörleri, uygulamanın gerektirdiği zorunlu yapıtlar olarak tanımlandı ve kapsayıcı görüntüsüne kopyalanacaktır.

2. **Klasör Ekle** ' ye tıklayın ve eklenecek klasör yollarını belirtin.
3. Aynı birime birden çok klasör eklemek için, virgül ( `,` ) ayrılmış değerler sağlayın.
4. Klasörlerin kalıcı bir birimde kapsayıcının dışında depolanmasını istiyorsanız depolama seçeneği olarak **kalıcı birim** ' i seçin.
5. Uygulama klasörlerini gözden geçirdikten sonra **Kaydet** ' e tıklayın.
   ![Uygulama birimleri depolama seçimi için ekran görüntüsü.](./media/tutorial-containerize-apps-aks/discovered-app-volumes.png)

6. Kapsayıcı görüntüsü oluşturma aşamasına geçmek için **devam** ' a tıklayın.

## <a name="build-container-image"></a>Kapsayıcı görüntüsü oluşturma


1. **Azure Container Registry seçin**: açılan listeyi kullanarak uygulamalar için kapsayıcı görüntülerini derlemek ve depolamak için kullanılacak bir [Azure Container Registry](../container-registry/index.yml) seçin. Mevcut bir Azure Container Registry kullanabilir veya yeni kayıt defteri oluştur seçeneğini kullanarak yeni bir tane oluşturmayı seçebilirsiniz.

    ![Uygulama ACR seçimi için ekran görüntüsü.](./media/tutorial-containerize-apps-aks/build-aspnet-app.png)


2. **Dockerfile dosyasını gözden geçirin**: seçili her uygulama için kapsayıcı görüntülerini oluşturmak Için gereken dockerfile, derleme adımının başlangıcında oluşturulur. Dockerfile dosyasını gözden geçirmek için **gözden geçir** ' e tıklayın. Ayrıca, gözden geçirme adımındaki Dockerfile dosyasına gerekli özelleştirmeleri ekleyebilir ve derleme işlemini başlatmadan önce değişiklikleri kaydedebilirsiniz.

3. **Derleme Işlemini Tetikle**: görüntüleri derlemek için uygulamaları seçin ve **Oluştur**' a tıklayın. Derleme ' ye tıkladığınızda her uygulama için kapsayıcı görüntüsü oluşturma başlatılır. Araç derleme durumunu sürekli olarak izlemeye devam eder ve derlemeyi başarılı bir şekilde tamamladıktan sonra bir sonraki adıma devam etmenize olanak tanır.

4. **Derleme durumunu izleyin**: Durum sütununun altında, **devam eden yapı** bağlantısına tıklayarak derleme adımının ilerlemesini de izleyebilirsiniz. Yapı işlemini tetikledikten sonra bağlantının etkin olması birkaç dakika sürer.  

5. Oluşturma işlemi tamamlandıktan sonra, dağıtım ayarlarını belirtmek için **devam** ' a tıklayın.

    ![Uygulama kapsayıcısı resim oluşturma işleminin tamamlanması için ekran görüntüsü.](./media/tutorial-containerize-apps-aks/build-aspnet-app-completed.png)

## <a name="deploy-the-containerized-app-on-aks"></a>Kapsayıcılı uygulamayı AKS üzerinde dağıtma

Kapsayıcı görüntüsü oluşturulduktan sonra, bir sonraki adım, uygulamayı [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/)' de bir kapsayıcı olarak dağıtmaktır.

1. **Azure Kubernetes hizmet kümesini seçin**: uygulamanın dağıtılması gereken aks kümesini belirtin.

     - Seçili AKS kümesinin bir Windows düğüm havuzu olmalıdır.
     - Kümenin, görüntüleri depolamak için seçilen Azure Container Registry görüntülerin çekmesini sağlamak üzere yapılandırılması gerekir.
         - AKS kümesini ACR 'ye eklemek için Azure CLı 'de aşağıdaki komutu çalıştırın.
           ``` Azure CLI
           az aks update -n <cluster-name> -g <cluster-resource-group> --attach-acr <acr-name>
           ```  
     - Bir AKS kümeniz yoksa veya uygulamayı uygulamasına dağıtmak için yeni bir AKS kümesi oluşturmak isterseniz, **Yeni AKS kümesi oluştur**' a tıklayarak aracından üzerinde oluşturmayı seçebilirsiniz.      
          - Aracı kullanılarak oluşturulan AKS kümesi bir Windows düğüm havuzuyla oluşturulur. Küme, daha önce oluşturulan Azure Container Registry resim çekmesine izin verecek şekilde yapılandırılacak (yeni kayıt defteri oluştur seçeneği belirlenmişse).
     - AKS kümesini seçtikten sonra **devam** ' a tıklayın.

2. **Azure dosya paylaşımının belirtin**: daha fazla klasör eklediyseniz ve kalıcı Birim seçeneğini belirlediyseniz, Azure geçişi tarafından kullanılması gereken Azure dosya paylaşımından, dağıtım Işlemi sırasında uygulama kapsayıcılama Aracı ' nı belirtin. Araç, bu Azure dosya paylaşımında kalıcı birim depolaması için yapılandırılmış uygulama klasörlerinin üzerine kopyalamak için yeni dizinler oluşturacaktır. Uygulama dağıtımı tamamlandıktan sonra araç, oluşturduğu dizinleri silerek Azure dosya paylaşımından temizler.

     - Azure dosya paylaşımınız yoksa veya yeni bir Azure dosya paylaşımının oluşturulmasını istiyorsanız **Yeni depolama hesabı ve dosya paylaşma oluştur**' a tıklayarak aracından üzerinde oluşturmayı seçebilirsiniz.  

3. **Uygulama dağıtımı yapılandırması**: Yukarıdaki adımları tamamladıktan sonra uygulamanın dağıtım yapılandırmasını belirtmeniz gerekir. Uygulamanın dağıtımını özelleştirmek için **Yapılandır** ' a tıklayın. Yapılandırma adımında aşağıdaki özelleştirmeleri sağlayabilirsiniz:
     - **Ön ek dizesi**: aks kümesindeki Kapsayıcılı uygulama için oluşturulan tüm kaynakların adında kullanılacak bir ön ek dizesi belirtin.
     - **SSL sertifikası**: uygulamanız bir https site bağlaması gerektiriyorsa, bağlama için kullanılacak SERTIFIKAYı içeren pfx dosyasını belirtin. PFX dosyası parola korumalı olmamalıdır ve özgün sitede birden çok bağlama bulunmamalıdır.
     - **Çoğaltma kümeleri**: kapsayıcılar içinde çalışması gereken uygulama örneklerinin (pods) sayısını belirtin.
     - **Yük dengeleyici türü**: Kapsayıcılı uygulamaya ortak ağlardan ulaşılabiliyorsa, *dış* ' i seçin.
     - **Uygulama yapılandırması**: parametreli olan herhangi bir uygulama yapılandırması için, geçerli dağıtım için kullanılacak değerleri sağlayın.
     - **Depolama**: kalıcı birim depolaması için yapılandırılmış tüm uygulama klasörleri için, birimin uygulama örnekleri arasında paylaşılıp paylaşılmayacağını veya kapsayıcıdaki her örnekle ayrı ayrı başlatılıp başlatılmayacağını belirtin. Varsayılan olarak, kalıcı birimlerdeki tüm uygulama klasörleri paylaşılan olarak yapılandırılır.  
     - Dağıtım yapılandırmasını kaydetmek için **Uygula** ' ya tıklayın.
     - Uygulamayı dağıtmak için **devam** ' a tıklayın.

    ![Dağıtım uygulaması yapılandırması ekran görüntüsü.](./media/tutorial-containerize-apps-aks/deploy-aspnet-app-config.png)

4. **Uygulamayı dağıtma**: uygulamanın dağıtım yapılandırması kaydedildikten sonra, araç uygulama Için Kubernetes DAĞıTıM YAML 'sini oluşturur.
     - Uygulamalar için Kubernetes dağıtımını gözden geçirmek ve özelleştirmek üzere **Düzenle** ' ye tıklayın.
     - Dağıtılacak uygulamayı seçin.
     - Seçili uygulamalar için dağıtımları başlatmak üzere **Dağıt** ' a tıklayın

         ![Uygulama dağıtımı yapılandırması ekran görüntüsü.](./media/tutorial-containerize-apps-aks/deploy-aspnet-app-deploy.png)

     - Uygulama dağıtıldıktan sonra, uygulama için dağıtılan kaynakları izlemek için *dağıtım durumu* sütununa tıklayabilirsiniz.

## <a name="download-generated-artifacts"></a>Oluşturulan yapıtları indir

Dockerfile ve Kubernetes YAML belirtim dosyaları da dahil olmak üzere uygulamayı derlemek ve dağıtmak için kullanılan tüm yapıtlar, aracı çalıştıran makinede saklanır. Yapıtlar *C:\ProgramData\Microsoft Azure geçişi uygulama Kapsayıcılama*' adresinde bulunur.

Her uygulama sunucusu için tek bir klasör oluşturulur. Kapsayıcılama sürecinde kullanılan tüm ara yapıtları bu klasöre giderek görüntüleyebilir ve indirebilirsiniz. Uygulama sunucusuna karşılık gelen klasör belirli bir sunucu için her bir aracın çalışmasının başlangıcında temizlenir.

## <a name="troubleshoot-issues"></a>Sorunları giderme

Araçla ilgili sorunları gidermek için, uygulama Kapsayıcılama aracı 'nı çalıştıran Windows makinesindeki günlük dosyalarına bakabilirsiniz. Araç günlük dosyaları *C:\ProgramData\Microsoft Azure Migrate App Containerization\Logs* klasöründe bulunur.

## <a name="next-steps"></a>Sonraki adımlar

- Apache Tomcat 'te Java Web Apps kapsayıyor (Linux sunucularında) ve bunları AKS üzerinde Linux kapsayıcılarına dağıtma. [Daha fazla bilgi](./tutorial-containerize-java-kubernetes.md)