---
title: Azure Farmtts dağıtma
description: Farmtts 'nin nasıl dağıtılacağını açıklar
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: acc64486ac8bd15a9abab1f2010ea56b752a1e86
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73927678"
---
# <a name="deploy-farmbeats"></a>FarmBeats'i dağıtma

Bu makalede, Azure Farmtts 'nin nasıl ayarlanacağı açıklanır.

Azure Farm, Azure bulutuna, telemetri veri toplamaya ve toplamaya yönelik sorunsuz sağlama ve cihaz bağlantısı sağlayan, verilere dayalı farkliğe yönelik sektöre özgü, genişletilebilir bir çözümdür. Azure Farmırts; kameralar, Drones, SOIL sensörleri ve buluttan cihazların yönetimi gibi çeşitli sensörler içerir. bu sayede, IoT-Ready (Nesnelerin İnterneti) cihazlar için Azure 'daki altyapı ve hizmetler, bir genişletilmiş Web ve mobil uygulamaya sağlanacak görselleştirme, uyarılar ve Öngörüler.

> [!NOTE]
> Azure Farmtts yalnızca genel bulut ortamlarında desteklenir. Bulut ortamı hakkında daha fazla bilgi için bkz. [Azure](https://azure.microsoft.com/overview/what-is-a-public-cloud/).

Azure Farmtts aşağıdaki iki bileşene sahiptir:

- **Veri merkezi** -veri hub 'ı, mevcut veya yeni veri işlem hatlarından veri oluşturmanıza, depolamanıza, işlemenize ve Öngörüler sağlamanıza olanak tanıyan Azure Farmtts 'nin platform katmanıdır. Bu platform katmanı, tarım veri işlem hatlarınızı ve modellerini çalıştırmak ve oluşturmak için kullanışlıdır.

- **Hızlandırıcı** -Hızlandırıcı, önceden oluşturulmuş agricdefinitions modellerini kullanarak Azure farmtilerinin yeteneklerini göstermek üzere yerleşik bir uygulamaya sahip Azure farmtts 'nin çözüm katmanıdır. Bu çözüm katmanı, Grup sınırları oluşturmanıza ve grup sınırının bağlamı içindeki agricbir veri kaynağından Öngörüler çizmenize imkan tanır.

Azure Farmtts 'nin hızlı dağıtımı bir saatten kısa sürer. Veri merkezi ve hızlandırıcının maliyetleri, kullanıma göre farklılık gösterir.

## <a name="deployed-resources"></a>Dağıtılan kaynaklar

Azure Farmtts dağıtımı, aboneliğiniz dahilinde aşağıdaki listelenen kaynakları oluşturur:

|S.No  |Kaynak Adı  |Azure Farmtempts bileşeni  |
|---------|---------|---------|
|1  |       Azure Cosmos DB   |  Veri Merkezi       |
|2  |    Application Insights      |     Veri merkezi/Hızlandırıcı     |
|3  |Redis için Azure Önbelleği   |Veri Merkezi   |
|4  |       Azure Keykasası    |  Veri merkezi/Hızlandırıcı        |
|5  |    Zaman Serisi Öngörüleri       |     Veri Merkezi      |
|6 |      EventHub ad alanı    |  Veri Merkezi       |
|7  |    Azure Data Factory V2       |     Veri merkezi/Hızlandırıcı      |
|8  |Batch hesabı    |Veri Merkezi   |
|9  |       Depolama hesabı     |  Veri merkezi/Hızlandırıcı        |
|10  |    Mantıksal uygulama        |     Veri Merkezi      |
|11  |    API bağlantısı        |     Veri Merkezi      |
|12|      App Service      |  Veri merkezi/Hızlandırıcı       |
|13 |    App Service planı        |     Veri merkezi/Hızlandırıcı      |
|14 |Azure haritalar hesabı     |Hızlandır    |
|15 |       Zaman Serisi Öngörüleri      |  Veri Merkezi     |

Azure Farmırts, Azure Marketi 'nden indirebilirsiniz. Doğrudan Azure portal erişebilirsiniz.  

## <a name="create-azure-farmbeats-offer-on-marketplace"></a>Market 'te Azure Farmırts teklifi oluşturma

Market 'te bir Azure Farmırts teklifi oluşturmak için aşağıdaki adımları kullanın:

1. Azure portal oturum açın ve sağ üst köşedeki hesabınızı seçin ve Microsoft Azure Farmtts 'yi dağıtmak istediğiniz Azure AD kiracısına geçiş yapın.
2. Azure Farmırts, Azure Marketi 'nde kullanılabilir. Market sayfasında "Şimdi al" seçeneğini belirleyin.
3. Oluştur ' u seçin ve aşağıdaki bilgileri girin:
    - Abonelik adı.
    - Mevcut bir kaynak grubu adı (yalnızca boş kaynak grubu) veya Azure Farmtempts dağıtmak için yeni bir kaynak grubu oluşturun. Sonraki bölümlerde bu kaynak grubunu bir yere iade edin.
4. Azure Farmtts 'yi yüklemek istediğiniz bölge. Şu anda şu bölgeleri aşağıda verilmiştir: Orta ABD, Batı Avrupa, Doğu ABD 2, Kuzey Avrupa, Batı ABD, Güneydoğu Asya, Doğu ABD, Avustralya Doğu, Batı ABD 2.
5. **Tamam**’ı seçin.
Kullanım koşulları sayfası görüntülenir. Standart Market şartlarını gözden geçirin veya kullanım koşullarını gözden geçirmek için köprüyü seçin.
6. **Kapat**' ı, sonra "kabul ediyorum" onay kutusunu seçin ve ardından **Oluştur**' u seçin.
7. Azure Farmtts 'nin Son Kullanıcı Lisans Sözleşmesi 'ni (EULA) Market 'te başarıyla kapattınız.  
7. Dağıtıma devam etmek için bu kılavuzdaki sonraki adımları izleyin.

## <a name="prepare"></a>Hazırlama

Azure Farmduytaları dağıtmak için aşağıdaki izinlere ihtiyacınız vardır:

- Kiracı: okuma erişimi
- Abonelik: katkıda bulunan veya sahip
- Kaynak grubu: sahip

## <a name="before-you-begin"></a>Başlamadan önce

Dağıtımı başlatmadan önce aşağıdakilerden emin olun:

- Sentinel hesabı
- Azure Active Directory (AD) uygulama kaydı

## <a name="create-a-sentinel-account"></a>Sentinel hesabı oluşturma    

Sentinel 'e sahip bir hesap, resmi web sitesinden cihazınızdan, Sentinel uydu canlandırın 'yi yüklemenize yardımcı olur. Ücretsiz bir hesap oluşturmak için aşağıdaki adımları izleyin:

https://scihub.copernicus.eu/dhus/#/self-registration kısmına gidin. Kayıt sayfasında, bir ad, soyadı, Kullanıcı adı, parola ve e-posta sağlayın.
Onay için kayıtlı e-posta adresine bir doğrulama e-postası gönderilir. Bağlantıyı seçin ve onaylayın. Kayıt işleminiz tamamlanmıştır.

## <a name="create-azure-ad-app-registration"></a>Azure AD uygulama kaydı oluşturma

Azure Farm, kimlik doğrulaması ve yetkilendirme için, aşağıdakileri içeren bir Azure Active Directory uygulaması kaydınız olmalıdır:

- Durum 1: yükleyici otomatik olarak oluşturulabilir (gerekli kiracı, abonelik ve kaynak grubu erişim izinlerine sahip olmanız şartıyla).
- 2\. durum: Azure Farmtts 'yi dağıtmaya başlamadan önce oluşturabilir ve yapılandırabilirsiniz (el ile adımlar gerekir).

**Durum 1**:: AAD uygulama kaydı oluşturma erişiminiz varsa, bu adımı atlayabilir ve yükleyicinin uygulama kaydını oluşturmasını sağlayabilirsiniz. Lütfen sonraki bölüme devam edin: [input. json dosyasını hazırlama](#prepare-input-json-file)

Zaten bir aboneliğiniz varsa, doğrudan bir sonraki yordama taşıyabilirsiniz.

**Durum 2**: aboneliğiniz dahilinde BIR Azure AD uygulama kaydı oluşturmak ve yapılandırmak için yeterli haklara sahip olmadığınız durumlarda bu yöntem tercih edilen adımdır. Yöneticinizin, BT yöneticisinin Azure portal Azure AD uygulama kaydını otomatik olarak oluşturup yapılandırmasına yardımcı olacak [özel betiği](https://aka.ms/FarmBeatsAADScript)kullanmasını isteyin. PowerShell ortamını kullanarak bu özel betiği çalıştırmanın bir çıkışı olarak BT yöneticisinin sizinle bir Azure Active Directory Uygulama Istemci KIMLIĞI ve parola gizli anahtarı paylaşması gerekir. Bu değerleri bir yere unutmayın.

Azure AD uygulaması kayıt betiğini çalıştırmak için aşağıdaki adımları kullanın:

1. [Betiği](https://aka.ms/FarmBeatsAADScript)indirin.
2. Azure portal oturum açın ve aboneliğinizi ve AD kiracınızı seçin.
3. Cloud Shell’i Azure portalında en üst gezinti bölmesinden başlatın.

    ![Proje grubu ları](./media/prepare-for-deployment/navigation-bar-1.png)


4. İlk kullanıcıdan, bir depolama hesabı ve Microsoft Azure dosya paylaşımının oluşturulması için bir abonelik seçmesi istenir. **Depolama oluştur**'u seçin.
5. Kullanıcılardan ilk kez tercih edilen kabuk deneyimi (bash veya PowerShell seçimi) istenir. PowerShell ' i seçin.
6. Betiği (adım 1 ' den Cloud Shell) karşıya yükleyin ve karşıya yüklenen dosyanın konumunu aklınızda edin.

    > [!NOTE]
    > Varsayılan olarak, Giriş dizininize yüklenir.

    Aşağıdaki betiği kullanın:

    ```azurepowershell-interactive
    ./create_aad_script.ps1
    ```
7. Azure Farmıd 'leri dağıtan kişilerle paylaşmak için Azure AD uygulama KIMLIĞI ve istemci gizli anahtarı ' nı bir yere göz önünde yapın.

### <a name="prepare-input-json-file"></a>Giriş json dosyasını hazırla

Yükleme kapsamında, aşağıdaki gibi bir input. JSON dosyası oluşturun:

```json
    {  
       "sku":"both",
       "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx",
       "datahubResourceGroup":"dummy-test-dh1",
       "location":"westus2",
       "datahubWebsiteName":"dummy-test-dh1",
       "acceleratorResourceGroup":" dummy-test-acc1",
       "acceleratorWebsiteName":" dummy-test-acc1",
       "sentinelUsername":"dummy-dev",
       "notificationEmailAddress":"dummy@yourorg.com",
       "updateIfExists":true
    }
```

Bu dosya, Azure Cloud Shell ve yükleme sırasında değerleri kullanılan parametrelere sahip olan giriş dosyasıdır. JSON 'daki tüm parametrelerin uygun değerlerle değiştirilmeleri veya kaldırılması gerekir; kaldırılırsa, yükleyici yükleme sırasında sizden istemde yer alacak


Dosyayı hazırlamadan önce parametreleri gözden geçirin.

|Komut | Açıklama|
|--- | ---|
|isteyin  | Azure Farmtts bileşenlerinin birini ya da her ikisini de indirmek için bir seçenek sağlar. Hangi bileşenlerin indirileceği belirtir. Yalnızca veri merkezini yüklemek için "onlydatabhub" kullanın. Veri merkezini ve hızlandırıcıyı yüklemek için "her ikisi de" kullanın|
|subscriptionId | Farmtts yükleme aboneliğini belirtir|
|datahubResourceGroup| Veri merkezi kaynakları için kaynak grubu adı|
|location |Kaynakları oluşturmak istediğiniz konum|
|Ivatorwebsitename |Veri merkezinizi adlandırmak için benzersiz URL öneki|
|Ivatorresourcegroup  | Hızlandırıcı Web sitenizi adlandırmak için benzersiz URL ön eki.|
|datahubWebsiteName  | Veri Merkezi Web sitenizi adlandırmak için Uıunique URL öneki. |
|sentinelUsername | oturum açmak için Kullanıcı adı: https://scihub.copernicus.eu/dhus/#/self-registration.|
|Notificationemapostaadresi  | Veri merkezi içinde yapılandırdığınız tüm uyarılar için bildirimleri alacak e-posta adresi.|
|updateIfExists|Seçim Yalnızca var olan bir Farmtts örneğini yükseltmek istiyorsanız Input. JSON içine dahil edilecek parametre. Yükseltme için diğer ayrıntılar örn. kaynak grubu adları, konumlar vb. aynı olmalıdır.|
|Aadappclientıd | [**Isteğe bağlı**] Yalnızca Azure AD uygulaması zaten varsa Input. JSON içine dahil edilecek parametre.  |
|aadAppClientSecret  | [**Isteğe bağlı**] Yalnızca Azure AD uygulaması zaten varsa Input. JSON içine dahil edilecek parametre.|

## <a name="deploy-within-cloud-shell-browser-based-command-line"></a>Cloud Shell tarayıcı tabanlı komut satırı içinde dağıtın

Yukarıdaki Market iş akışının bir parçası olarak, bir kaynak grubu oluşturmuş ve Son Kullanıcı Lisans sözleşmesini imzaladığınızdan, bu, gerçek dağıtımın bir parçası olarak bir kez daha gözden geçirilebilmeniz gerekir. Dağıtım, Bash ortamı kullanılarak Azure Cloud Shell (tarayıcı tabanlı komut satırı) aracılığıyla yapılabilir. Lütfen Cloud Shell aracılığıyla dağıtılacak sonraki bölümlere devam edin.

> [!NOTE]
> Etkin olmayan Cloud Shell oturumlarının süresi 20 dakika sonra dolacak. Bu süre içinde dağıtımı tamamlamayı deneyin.

1. Azure portal oturum açın ve istediğiniz aboneliği ve AD kiracıyı seçin.
2. Cloud Shell’i Azure portalında en üst gezinti bölmesinden başlatın.
3. Cloud Shell ilk kez kullanıyorsanız, bir depolama hesabı ve Microsoft Azure dosya paylaşımının oluşturulması için bir abonelik seçmeniz istenir.
4. **Depolama oluştur**' u seçin.  

Ortamı Bash (PowerShell değil) olarak seçin.

## <a name="deployment-scenario-1"></a>Dağıtım senaryosu 1

Yükleyici Azure AD Uygulaması kaydını (Yukarıdaki durum 1) oluşturur

1. Aşağıdaki şablonu kopyalayın ve input. JSON olarak adlandırın.  
Örnek JSON girişi:

    ```json
    {  
       "sku":"both", 
       "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx", 
       "datahubResourceGroup":"dummy-test-dh1", 
       "location":"eastus2", 
       "datahubWebsiteName":"dummy-test-dh1", 
       "acceleratorResourceGroup":" dummy-test-acc1",   
       "acceleratorWebsiteName":" dummy-test-acc1", 
       "sentinelUsername":"dummy-dev", 
       "notificationEmailAddress":" dummy@microsoft.com", 
       "updateIfExists":true 
    }
    ```

2. Dosyayı kaydedin ve yolu (yerel bilgisayarınızda) bir yere göz önünde yapın.
3. Azure Cloud Shell gidin ve başarılı kimlik doğrulamasından sonra karşıya yükle ' yi seçin (aşağıdaki görüntüde vurgulanan simgeye bakın) ve input. json dosyasını Cloud Shell depolama alanına yükleyin.  

    ![Proje grubu ları](./media/prepare-for-deployment/bash-2-1.png)

4. Cloud Shell 'de Giriş dizininize gidin. Varsayılan olarak,/Home/<username>
5. Aşağıdaki komutu Cloud Shell yazın veya yapıştırın. Girişin yolunu değiştirdiğinizden emin olun. JSON dosyası ve ENTER tuşuna basın.

   ```bash
      wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
     Yükleyici tüm bağımlılıkları otomatik olarak indirir ve dağıtıcıyı oluşturur. Azure Farmınts Son Kullanıcı Lisans Sözleşmesi 'ni (EULA) kabul etmeniz istenir.

     - Kabul ediyorsanız ' Y ' yazın ve sonraki adıma ilerlemeniz gerekir.
     - Koşulları kabul etmiyorsanız ve dağıtım sonlanacak olursa ' N ' girin.

6. Ardından dağıtım için bir erişim belirteci girmeniz istenir. Oluşturulan kodu kopyalayın ve Azure kimlik bilgilerinizle https://microsoft.com/devicelogin oturum açın.

    > [!NOTE]
    > Belirtecin süresi 60 dakika sonra dolar. Süresi sona erdiğinde, dağıtım komutunu yeniden yazarak yeniden başlatabilirsiniz.

7. İstendiğinde, Sentinel hesap parolanızı girin.
8. Yükleyici şimdi doğrular ve dağıtımı başlatır. Bu işlem 20 dakika sürebilir.
9. Dağıtım başarılı olduktan sonra aşağıdaki çıkış bağlantılarını alacaksınız:

 - **Veri merkezi URL 'si**: Azure Farmtts API 'lerini denemek için Swagger bağlantısı.
 - **HıZLANDıRıCı URL**: Azure Farmtts akıllı grup hızlandırıcıyı keşfetmeye yönelik kullanıcı arabirimi.
 - **Deployer günlük dosyası**-dağıtım sırasında oluşturulan günlük dosyası. Gerekirse, sorun giderme için kullanılabilir.

## <a name="deployment-scenario-2"></a>Dağıtım senaryosu 2

Dağıtım için mevcut Azure Active Directory uygulama kaydı kullanılıyor (Yukarıdaki durum 2)

1. Azure Uygulama Istemci KIMLIĞINI ve parolayı input. JSON içinde içeren aşağıdaki JSON dosyasını kopyalayın ve kaydedin.

    ```json
   {

   "sku":"both",
   "subscriptionId":"daxx9xxx-d18f-4xxc-9c21-5xx3exxxxx45",
   "datahubResourceGroup":"dummy-test-dh1",   
   "location":"westus2",   
   "datahubWebsiteName":"dummy-test-dh1",   
   "acceleratorResourceGroup":"dummy-test-acc1",   
   "acceleratorWebsiteName":"dummy-test-acc1",   
   "sentinelUsername":"dummy-dev",
   "notificationEmailAddress":"dummyuser@org1.com",
   "updateIfExists": true,
   "aadAppClientId": "lmtlemlemylmylkmerkywmkm823",
   "aadAppClientSecret": "Kxxxqxxxxu*kxcxxx3Yxxu5xx/db[xxx"

   }
   ```

Kalan adımları izleyin:

2. İnput. JSON dosyanızın yolunu (yerel bilgisayarınızda) bir yere unutmayın.
3. Azure Cloud Shell yeniden bir kez daha gidin ve başarıyla kimliğiniz doğrulandıktan sonra karşıya yükle düğmesini (aşağıdaki görüntüde vurgulanan simgeye bakın) seçin ve input. json dosyasını Cloud Shell depolama alanına yükleyin.

    ![Proje grubu ları](./media/prepare-for-deployment/bash-2-1.png)

4. Cloud Shell 'de Giriş dizininize gidin. Varsayılan olarak,/Home/<username>
5. Aşağıdaki komutu Cloud Shell yazın veya yapıştırın. Girişin yolunu değiştirdiğinizden emin olun. JSON dosyası ve ENTER tuşuna basın.

    ```bash
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```

Ekrandaki yönergeleri izleyin.

6. Komut dosyası tüm bağımlılıkları otomatik olarak indirir ve dağıtıcı oluşturur.
7. Azure Farmınts Son Kullanıcı Lisans Sözleşmesi 'ni (EULA) okuyup kabul etmeniz istenir.

    - Kabul ediyorsanız ' Y ' girin ve sonraki adımla devam edersiniz.
    - Koşulları kabul etmiyorsanız ve dağıtım sonlanacak olursa ' N ' girin.

8. Dağıtım için bir erişim belirteci girmeniz istenir. Oluşturulan kodu kopyalayın ve Azure kimlik bilgilerinizle https://microsoft.com/devicelogin oturum açın.
9. Yükleyici artık 20 dakika sürebilecek kaynakları doğrular ve oluşturmaya başlar. Bu süre boyunca Cloud Shell oturumun etkin kalmasını sağlayın.
10. Dağıtım başarıyla tamamlandıktan sonra aşağıdaki çıkış bağlantılarını alacaksınız:

 - **Veri merkezi URL 'si**: Farmtts API 'lerini denemek için Swagger bağlantısı.
 - **HıZLANDıRıCı URL**: Farmtts akıllı grup hızlandırıcıyı keşfetmeye yönelik kullanıcı arabirimi.
 - **Deployer günlüğü dosyası**: dağıtım sırasında günlük dosyası oluşturuldu. Gerekirse, sorun giderme için kullanılabilir.

Herhangi bir sorunla karşılaşırsanız [sorun giderme](troubleshoot-project-farmbeats.md)' yi gözden geçirin.


## <a name="validate-deployment"></a>Dağıtımı doğrula

### <a name="data-hub"></a>Veri Merkezi

Veri merkezi yüklemesi tamamlandıktan sonra, Şu biçimdeki Swagger arabirimi aracılığıyla Azure Farmınts API 'Lerine erişmek için URL 'YI alacaksınız: https://\<yourdatahub-website-name >. azurewebsites. net

1. Swagger aracılığıyla oturum açmak için URL 'YI kopyalayıp tarayıcıya yapıştırın.
2. Azure portal kimlik bilgileriyle oturum açın.
3. Sanity testi (Isteğe bağlı)

     - Başarılı bir dağıtıma çıkış olarak aldığınız veri merkezi bağlantısını kullanarak Swagger portalında başarıyla oturum açabiliyor.
     - Genişletilmiş türler API al-"deneyin/kapat" seçeneğini belirleyin
     - Sunucu yanıt kodu 200 ' i almalısınız ve 403 "yetkisiz Kullanıcı" gibi bir özel durum değil.

### <a name="accelerator"></a>Hızlandır

Hızlandırıcı yüklemesi tamamlandıktan sonra, Şu biçimdeki Farmtts Kullanıcı arabirimine erişmek için URL 'YI alacaksınız: https://\<Hızlandırıcısı-Web sitesi-adı >. azurewebsites. net

1. Hızlandırıcının oturumunu açmak için URL 'YI kopyalayıp tarayıcıya yapıştırın.
2. Azure portal kimlik bilgileriyle oturum açın.

## <a name="upgrade"></a>Yükseltme

Yükseltme adımları, ilk kez yüklemeye benzerdir. Şu adımları uygulayın:

1. Azure portal için oturum açın ve istediğiniz abonelik ve AD kiracınızı seçin.
2. Cloud Shell’i Azure portalında en üst gezinti bölmesinden başlatın.

   ![Proje grubu ları](./media/prepare-for-deployment/navigation-bar-1.png)

3. Kabuğun solundaki açılan listeden "Bash" olarak ortamı seçin.
4. Yalnızca gerektiğinde input. JSON dosyanızda değişiklik yapın ve Azure Cloud Shell yükleyin. Örneğin, e-posta adresinizi almak istediğiniz bildirim için güncelleştirebilirsiniz.
5. Input. json dosyasını Azure Cloud Shell karşıya yükleyin.
6. Aşağıdaki iki komutu Cloud Shell yazın veya yapıştırın. İnput. json dosyasının yolunu değiştirdiğinizden emin olun ve ENTER tuşuna basın.

    ```bash
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
Ekrandaki yönergeleri izleyin:

7. Yükleyici, çalışma zamanında gerekli girdileri otomatik olarak sorar:
8. Dağıtım için bir erişim belirteci girin. Oluşturulan kodu kopyalayın ve Azure kimlik bilgilerinizle https://microsoft.com/devicelogin oturum açın.
9. Sentinel parolası
10. Yükleyici artık 20 dakikadan uzun sürebilen ve kaynakları oluşturmayı doğrular ve başlatır.
11. Dağıtım başarılı olduktan sonra aşağıdaki çıkış bağlantılarını alacaksınız:
 - **Veri merkezi URL 'si**: Farmtts API 'lerini denemek için Swagger bağlantısı.
 - **HıZLANDıRıCı URL**: Farmtts akıllı grup hızlandırıcıyı keşfetmeye yönelik kullanıcı arabirimi.
 - **Deployer günlüğü dosyası**: dağıtım sırasında günlükleri kaydeder. Sorun giderme için kullanılabilir.

> [!NOTE]
> Daha sonra kullanmak için yukarıdaki değerleri unutmayın.


## <a name="uninstall"></a>Kaldırma

Şu anda, yükleyiciyi kullanarak Farmof 'ları otomatik olarak kaldırmayı desteklemiyoruz. Veri hub 'ını veya hızlandırıcıyı kaldırmak için Azure portal, bu bileşenlerin yüklendiği kaynak grubunu silin veya kaynakları el ile silin.

Örneğin, veri hub 'ı ve hızlandırıcıyı iki farklı kaynak grubunda dağıttıysanız, bu kaynak gruplarını aşağıdaki şekilde silersiniz:

1. Azure portal oturum açın.
2. Sağ üst köşede hesabınızı seçin ve Microsoft Farmtts 'yi dağıtmak istediğiniz Azure AD kiracısına geçiş yapın.

   > [!NOTE]
   > Hızlandırıcının düzgün çalışması için veri hub 'ı gerekir. Hızlandırıcı 'Yı kaldırmadan veri merkezini kaldırmayı önermiyoruz.

3. Kaynak grupları ' nı seçin ve silmek istediğiniz veri hub 'ının veya hızlandırıcı kaynak grubunun adını yazın.
4. Kaynak grubu adını seçin. Yeniden denetlemek için adı tekrar yazın ve kaynak grubunu ve tüm temel kaynaklarını kaldırmak için Sil ' i seçin.
5. Alternatif olarak, her kaynağı el ile silebilirsiniz, bu önerilmez.
7. Veri merkezini silmek/kaldırmak için doğrudan Azure 'da kaynak grubuna gidin ve kaynak grubunu buradan silin.

## <a name="next-steps"></a>Sonraki adımlar

Azure Farmtts 'yi dağıttıysanız. Şimdi [gruplar oluşturmayı](manage-farms.md#create-farms)öğrenin.
