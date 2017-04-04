---
title: "Azure Farklı Çalıştır Hesabını Yapılandırma | Microsoft Docs"
description: "Bu öğretici, Azure Otomasyonu’nda güvenlik temel elemanı kimlik doğrulaması oluşturulması, test edilmesi ve örneklerinde size yol göstermektedir."
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
keywords: "hizmet asıl adı, setspn, azure kimlik doğrulaması"
ms.assetid: 2f783441-15c7-4ea0-ba27-d7daa39b1dd3
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/27/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: fbca3d195290551d37606e231b997a40a602351f
ms.lasthandoff: 03/28/2017


---

# <a name="authenticate-runbooks-with-an-azure-run-as-account"></a>Azure Farklı Çalıştır hesabıyla kimlik doğrulaması runbook’ları
Bu makalede, Azure portalında bir Azure Otomasyonu hesabını yapılandırma işlemi gösterilmektedir. Bunu yapmak için, Farklı Çalıştır hesabı özelliğini kullanarak Azure Resource Manager veya Azure Service Management’ta kaynakları yöneten runbook’ların kimliğini doğrulayın.

Azure portalında bir Otomasyon hesabı oluşturduğunuzda otomatik olarak iki hesap oluşturursunuz:

* Bir Farklı Çalıştır hesabı. Bu hesap, Azure Active Directory'de (Azure AD) bir hizmet sorumlusu ve bir sertifika oluşturur. Ayrıca, runbook kullanarak Resource Manager kaynaklarını yöneten Katkıda Bulunan rol tabanlı erişim denetimi (RBAC) iznini atar.
* Klasik Farklı Çalıştır hesabı. Bu hesap, runbook kullanarak Service Management’ı ya da klasik kaynakları yönetmek için kullanılan bir yönetim sertifikasını karşıya yükler.

Bir Otomasyon hesabının oluşturulması, işlemi sizin için basitleştirir ve otomasyon gerekliliklerini desteklemek için runbook’ları oluşturmaya ve dağıtmaya hemen başlamanıza yardımcı olur.

Farklı Çalıştır ve Klasik Farklı Çalıştır hesapları ile şunları yapabilirsiniz:

* Azure portalındaki runbook’lardan Resource Manager veya Service Management kaynakları yönetilirken Azure ile kimlik doğrulaması için standartlaştırılmış bir yol sağlama.
* Azure Uyarıları’nda yapılandırabileceğiniz genel runbook'ların kullanımını otomatikleştirme.

> [!NOTE]
> Otomasyon genel runbook’larına sahip Azure [Uyarı tümleştirme özelliği](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) için Farklı Çalıştır hesabı ve Klasik Farklı Çalıştır hesabıyla yapılandırılmış bir Otomasyon hesabı gerekir. Farklı Çalıştır ve Klasik Farklı Çalıştır hesabı zaten tanımlanmış bir Otomasyon hesabı seçebilir ya da yeni bir Otomasyon hesabı oluşturmayı seçebilirsiniz.
>  

Bu makalede, Otomasyon hesabının Azure portalından oluşturulması, bir Otomasyon hesabının Azure PowerShell kullanılarak güncelleştirilmesi, hesap yapılandırmasının yönetilmesi ve runbook’larınızda kimlik doğrulamasının nasıl yapılacağı gösterilir.

Bir Otomasyon hesabı oluşturmaya başlamadan önce aşağıdakileri anlayın ve göz önünde bulundurun:

* Bir Otomasyon hesabının oluşturulması, klasik veya Resource Manager dağıtım modelinde daha önce oluşturmuş olabileceğiniz Otomasyon hesaplarını etkilemez.
* İşlem yalnızca Azure portalında oluşturduğunuz Otomasyon hesapları için geçerli olur. Klasik Azure portalından bir hesap oluşturulmaya çalışıldığında Farklı Çalıştır hesabının yapılandırması çoğaltılmaz.
* Klasik kaynakları yönetmek için kullandığınız runbook’lar ve varlıklar (zamanlamalar veya değişkenler) zaten varsa ve yeni Klasik Farklı Çalıştır hesabında runbook’ların kimliğini doğrulamak istiyorsanız, aşağıdakilerden birini yapın:

  * Klasik Farklı Çalıştır hesabı oluşturmak için "Farklı Çalıştır hesabınızı yönetme" bölümünde yer alan yönergeleri izleyin. 
  * Var olan hesabınızı güncelleştirmek için "PowerShell kullanarak Otomasyon hesabınızı güncelleştirme" bölümündeki PowerShell betiğini kullanın.
* Yeni Farklı Çalıştır hesabını ve Klasik Farklı Çalıştır Otomasyon hesabını kullanarak kimlik doğrulamak için mevcut runbook’larınızı [Kimlik doğrulaması kod örnekleri](#authentication-code-examples) bölümünde sağlanan örnek kod ile değiştirmeniz gerekir.

    >[!NOTE]
    >Farklı Çalıştır hesabı, sertifika tabanlı hizmet sorumlusu kullanarak Resource Manager kaynaklarına göre kimlik doğrulaması içindir. Klasik Farklı Çalıştır hesabı ise bir yönetim sertifikası ile Service Management kaynaklarına göre kimlik doğrulaması içindir.

## <a name="create-an-automation-account-from-the-azure-portal"></a>Azure portalından Otomasyon hesabı oluşturma
Bu bölümde, Azure portalından hem bir Farklı Çalıştır hesabı hem de bir Klasik Farklı Çalıştır hesabı oluşturan Azure Otomasyonu hesabı oluşturacaksınız.

>[!NOTE]
>Bir Otomasyon hesabı oluşturmak için, Hizmet Yöneticileri rolünün bir üyesi veya aboneliğe erişim veren aboneliğin ortak yöneticisi olmanız gerekir. Ayrıca, bu aboneliğin varsayılan Active Directory örneğine kullanıcı olarak eklenmeniz gerekir. Hesabın ayrıcalıklı bir role atanması gerekmez.
>
>Aboneliğin ortak yönetici rolüne eklenmeden önce aboneliğin Active Directory örneğine üye değilseniz, Active Directory’ye konuk olarak eklenirsiniz. Bu örnekte, “Oluşturma izniniz yok…” uyarısını **Otomasyon Hesabı Ekle** dikey penceresinde görürsünüz.
>
>İlk olarak ortak yönetici rolüne eklenen kullanıcılar aboneliğin Active Directory örneğinden kaldırılabilir ve tekrar eklenerek Active Directory’de tam bir Kullanıcı haline getirilebilir. Bu durumu Azure portalındaki **Azure Active Directory** bölmesinde **Kullanıcılar ve gruplar**’ı ve **Tüm kullanıcılar**’ı seçtikten sonra gerekli kullanıcıyı belirleyip **Profil**’i seçerek doğrulayabilirsiniz. Kullanıcı profili altındaki **Kullanıcı türü** özniteliğinin **Konuk** olmaması gerekir.
>

1. Azure portalında abonelik yöneticileri rolünün üyesi ve aboneliğin ortak yöneticisi olan bir hesapla oturum açın.

2. **Automation Hesapları**’nı seçin.

3. **Otomasyon Hesapları** dikey penceresinde **Ekle**’ye tıklayın.
**Otomasyon Hesabı Ekle** dikey penceresi açılır.

 ![“Otomasyon Hesabı Ekle” dikey penceresi](media/automation-sec-configure-azure-runas-account/create-automation-account-properties-b.png)

   > [!NOTE]
   > Hesabınız aboneliğin yönetici rolüne üye değilse veya aboneliğin ortak yöneticisi değilse, **Otomasyon Hesabı Ekle** dikey penceresinde şu uyarı gösterilir:
   >
   >![Automation Hesabı Uyarısı ekleme](media/automation-sec-configure-azure-runas-account/create-account-without-perms.png)
   >
   >

4. **Otomasyon Hesabı Ekle** dikey penceresindeki **Ad** kutusuna yeni Otomasyon hesabınız için bir ad yazın.

5. Birden fazla aboneliğiniz varsa aşağıdaki işlemleri yapın:

    a. **Abonelik** altında yeni hesap için bir abonelik belirtin.

    b. **Kaynak Grubu** altında, **Yeni oluştur** veya **Var olanı kullan**’a tıklayın.

    c. **Konum** altında bir Azure veri merkezi belirtin.

6. **Azure Farklı Çalıştır hesabı oluştur** altında **Evet**’i seçin ve ardından **Oluştur**’a tıklayın.

   > [!NOTE]
   > **Hayır**’ı seçerek Farklı Çalıştır hesabı oluşturmamayı tercih ederseniz, **Otomasyon Hesabı Ekle** dikey penceresinde bir uyarı iletisi gösterilir. Hesap Azure portalında oluşturulsa da, klasik veya Resource Manager aboneliğinin dizin hizmetinde ona karşılık gelen bir kimlik doğrulama kimliği yoktur. Sonuç olarak hesap, aboneliğinizdeki kaynaklara erişemez. Bu senaryo, ilgili dağıtım modellerindeki kaynaklara göre kimlik doğrulama ve görev gerçekleştirme işlemlerinden bu hesaba başvuran runbook’ları engeller.
   >
   > !["Otomasyon Hesabı Ekle" dikey penceresindeki uyarı iletisi](media/automation-sec-configure-azure-runas-account/create-account-decline-create-runas-msg.png)
   >
   > Ayrıca, hizmet sorumlusu oluşturulmadığı için Katkıda Bulunan rolü atanmaz.
   >

7. Azure Automation hesabını oluşturduğu sırada menünün **Bildirimler** öğesi altında ilerleme durumunu izleyebilirsiniz.

### <a name="resources"></a>Kaynaklar
Otomasyon hesabı başarıyla oluşturulduğunda bazı kaynaklar sizin için otomatik olarak oluşturulur. Kaynaklar aşağıdaki iki tabloda özetlenmiştir:

#### <a name="run-as-account-resources"></a>Farklı Çalıştır hesabının kaynakları

| Kaynak | Açıklama |
| --- | --- |
| AzureAutomationTutorial Runbook | Farklı Çalıştır hesabını kullanarak kimlik doğrulaması yapmayı gösteren ve tüm Resource Manager kaynaklarını alan örnek bir grafik runbook. |
| AzureAutomationTutorialScript Runbook | Farklı Çalıştır hesabını kullanarak kimlik doğrulaması yapmayı gösteren ve tüm Resource Manager kaynaklarını alan örnek bir PowerShell runbook. |
| AzureRunAsCertificate | Bir Otomasyon hesabı oluşturduğunuzda veya var olan bir hesap için aşağıdaki PowerShell betiğini kullandığınızda otomatik olarak oluşturulan sertifika varlığı. Sertifika, Azure Resource Manager kaynaklarını runbook’lardan yönetebilmeniz için Azure kimlik doğrulaması yapmanıza imkan tanır. Bu sertifikanın bir yıllık kullanım ömrü vardır. |
| AzureRunAsConnection | Bir Otomasyon hesabı oluşturduğunuzda veya var olan bir hesap için PowerShell betiğini kullandığınızda otomatik olarak oluşturulan bağlantı varlığı. |

#### <a name="classic-run-as-account-resources"></a>Klasik Farklı Çalıştır hesabının kaynakları

| Kaynak | Açıklama |
| --- | --- |
| AzureClassicAutomationTutorial Runbook | Klasik Farklı Çalıştır hesabı (sertifika) kullanarak, bir abonelikte klasik dağıtım modeli ile oluşturulan tüm VM’leri alan ve sonra VM adı ile durumunu yazan, örnek grafik runbook. |
| AzureClassicAutomationTutorial Script Runbook | Klasik Farklı Çalıştır hesabı (sertifika) kullanarak bir abonelikteki tüm klasik VM'leri alan ve sonra VM adını ve durumunu yazan, örnek PowerShell runbook. |
| AzureClassicRunAsCertificate | Otomatik olarak oluşturulan ve Azure klasik kaynaklarını runbook’lardan yönetebilmeniz için Azure kimlik doğrulaması yapmak üzere kullandığınız sertifika varlığı. Bu sertifikanın bir yıllık kullanım ömrü vardır. |
| AzureClassicRunAsConnection | Otomatik olarak oluşturulan ve Azure klasik kaynaklarını runbook’lardan yönetebilmeniz için Azure kimlik doğrulaması yapmak üzere kullandığınız bağlantı varlığı. |

## <a name="verify-run-as-authentication"></a>Farklı Çalıştır kimlik doğrulamasını onaylama
Yeni Farklı Çalıştır hesabını kullanarak kimlik doğrulamasını sorunsuz yapabildiğinizi doğrulamak için küçük bir test gerçekleştirin.

1. Azure portalında, daha önce oluşturduğunuz Otomasyon hesabını açın.

2. Runbook'ların listesini açmak için **Runbook'lar** kutucuğuna tıklayın.

3. **AzureAutomationTutorialScript** runbook’unu seçin ve ardından **Başlat**’a tıklayarak runbook’u başlatın. Aşağıdaki olaylar gerçekleşir:
 * Bir [runbook işi](automation-runbook-execution.md) oluşturulur, **İş** dikey penceresi gösterilir ve iş durumu **İş Özeti** kutucuğunda gösterilir.
 * İş durumu, bulutta bir runbook çalışanının kullanılabilir hale gelmesinin beklendiğini gösteren şekilde **Sırada** olarak başlar.
 * Bir çalışan işi talep ettiğinde durum **Başlıyor** olur.
 * Runbook çalışmaya başladığında durum **Çalışıyor** olur.
 * Runbook işi tamamlandığında **Tamamlandı** durumunu görmeniz gerekir.

       ![Security Principal Runbook Test](media/automation-sec-configure-azure-runas-account/job-summary-automationtutorialscript.png)
4. Runbook’un ayrıntılı sonuçlarını görmek için **Çıktı** kutucuğuna tıklayın.  
Runbook kimlik doğrulamasının başarıyla yapıldığını ve kaynak grubunda mevcut olan tüm kaynakların bir listesini döndürdüğünü gösteren **Çıktı** dikey penceresi görüntülenir.

5. **Çıktı** dikey penceresini kapatarak **İş Özeti** dikey penceresine geri dönün.

6. **İş Özeti** dikey penceresini ve karşılık gelen **AzureAutomationTutorialScript** runbook dikey penceresini kapatın.

## <a name="verify-classic-run-as-authentication"></a>Klasik Farklı Çalıştır kimlik doğrulamasını onaylama
Yeni Klasik Farklı Çalıştır hesabını kullanarak kimlik doğrulamasını sorunsuz yapabildiğinizi doğrulamak için benzer bir küçük test gerçekleştirin.

1. Azure portalında, daha önce oluşturduğunuz Otomasyon hesabını açın.

2. Runbook'ların listesini açmak için **Runbook'lar** kutucuğuna tıklayın.

3. **AzureClassicAutomationTutorialScript** runbook’unu seçin ve ardından **Başlat**’a tıklayarak runbook’u başlatın. Aşağıdaki olaylar gerçekleşir:

 * Bir [runbook işi](automation-runbook-execution.md) oluşturulur, **İş** dikey penceresi gösterilir ve iş durumu **İş Özeti** kutucuğunda gösterilir.
 * İş durumu, bulutta bir runbook çalışanının kullanılabilir hale gelmesinin beklendiğini gösteren şekilde **Sırada** olarak başlar.
 * Bir çalışan işi talep ettiğinde durum **Başlıyor** olur.
 * Runbook çalışmaya başladığında durum **Çalışıyor** olur.
 * Runbook işi tamamlandığında **Tamamlandı** durumunu görmeniz gerekir.

    ![Güvenlik Sorumlusu Runbook Testi](media/automation-sec-configure-azure-runas-account/job-summary-automationclassictutorialscript.png)<br>
4. Runbook’un ayrıntılı sonuçlarını görmek için **Çıktı** kutucuğuna tıklayın.  
Runbook kimlik doğrulamasının başarıyla yapıldığını ve abonelikteki tüm klasik VM’lerin bir listesini döndürdüğünü gösteren **Çıktı** dikey penceresi görüntülenir.

5. **Çıktı** dikey penceresini kapatarak **İş Özeti** dikey penceresine geri dönün.

6. **İş Özeti** dikey penceresini ve karşılık gelen **AzureAutomationTutorialScript** runbook dikey penceresini kapatın.

## <a name="managing-your-run-as-account"></a>Farklı Çalıştır hesabınızı yönetme
Otomasyon hesabınızın süresi dolmadan önce sertifikayı yenilemeniz gerekir. Farklı Çalıştır hesabının tehlikede olduğunu düşünüyorsanız, hesabı silip yeniden oluşturabilirsiniz. Bu bölümde bu işlemlerin nasıl gerçekleştirileceği ele alınmaktadır.

### <a name="self-signed-certificate-renewal"></a>Otomatik olarak imzalanan sertifika yenileme
Farklı Çalıştır hesabı için oluşturduğunuz otomatik olarak imzalanan sertifikanın süresi, oluşturulduktan bir yıl sonra dolar. Sertifikayı süresi dolmadan önce herhangi bir zamanda yenileyebilirsiniz. Sertifikayı yenilediğinizde, sıraya alınmış ya da o anda çalışan ve Farklı Çalıştır hesabı ile kimliği doğrulanmış runbook’ların olumsuz yönde etkilenmemesi için geçerli sertifika saklanır. Sertifika, sona erme tarihine kadar geçerliliğini sürdürür.

> [!NOTE]
> Otomasyon Farklı Çalıştır hesabınızı, kuruluş sertifika yetkiliniz tarafından yayınlanan bir sertifika kullanmak üzere yapılandırdıysanız ve bu seçeneği kullanırsanız, kurumsal sertifika otomatik olarak imzalanan bir sertifikayla değiştirilir.

Sertifikayı yenilemek için aşağıdakileri yapın:

1. Azure portalında Otomasyon hesabınızı açın.

2. **Otomasyon Hesabı** dikey penceresindeki **Hesap özellikleri** bölmesinin **Hesap Ayarları** altında **Farklı Çalıştır Hesapları**’nı seçin.

    ![Otomasyon hesabı özellikleri bölmesi](media/automation-sec-configure-azure-runas-account/automation-account-properties-pane.png)
3. **Farklı Çalıştır Hesapları** özellikleri dikey penceresinde sertifikasını yenilemek istediğiniz Farklı Çalıştır Hesabını veya Klasik Farklı Çalıştır Hesabını seçin.

4. Seçili hesabın **Özellikler** dikey penceresinde **Sertifikayı yenile**’ye tıklayın.

    ![Farklı Çalıştır hesabının sertifikasını yenileme](media/automation-sec-configure-azure-runas-account/automation-account-renew-runas-certificate.png)

5. Sertifika yenilenirken menünün **Bildirimler** öğesi altında ilerleme durumunu izleyebilirsiniz.

### <a name="delete-a-run-as-or-classic-run-as-account"></a>Farklı Çalıştır veya Klasik Farklı Çalıştır hesabını silme
Bu bölümde bir Farklı Çalıştır veya Klasik Farklı Çalıştır hesabını silip yeniden oluşturma işlemi açıklamaktadır. Bu eylemi gerçekleştirdiğinizde Otomasyon hesabı korunur. Farklı Çalıştır veya Klasik Farklı Çalıştır hesabını sildikten sonra Azure portalında yeniden oluşturabilirsiniz.

1. Azure portalında Otomasyon hesabınızı açın.

2. **Otomasyon Hesabı** dikey penceresindeki hesap özellikleri bölmesinde **Farklı Çalıştır Hesapları**’nı seçin.

3. **Farklı Çalıştır Hesapları** özellikleri dikey penceresinde silmek istediğiniz Farklı Çalıştır Hesabını veya Klasik Farklı Çalıştır Hesabını seçin. Ardından, seçili hesabın **Özellikler** dikey penceresinde **Sil**’e tıklayın.

 ![Farklı Çalıştır hesabını silme](media/automation-sec-configure-azure-runas-account/automation-account-delete-runas.png)

4. Hesap silinirken menünün **Bildirimler** öğesi altında ilerleme durumunu izleyebilirsiniz.

5. Hesap silindikten sonra **Farklı Çalıştır Hesapları** özellikler dikey penceresinde **Azure Farklı Çalıştır Hesabı** seçeneğini belirleyerek hesabı yeniden oluşturabilirsiniz.

 ![Otomasyon Farklı Çalıştır hesabını yeniden oluşturma](media/automation-sec-configure-azure-runas-account/automation-account-create-runas.png)

### <a name="misconfiguration"></a>Yanlış yapılandırma
İlk kurulum sırasında, Farklı Çalıştır veya Klasik Farklı Çalıştır hesabının düzgün çalışması için gerekli olan bazıları silinmiş veya düzgün oluşturulmamış olabilir. Bu öğeler şunlardır:

* Sertifika varlığı
* Bağlantı varlığı
* Farklı Çalıştır hesabının katkıda bulunan rolünden kaldırılması
* Azure AD'de hizmet sorumlusu veya uygulama

Yanlış yapılandırmanın önceki ve diğer örneklerinde, Otomasyon hesabı değişiklikleri algılar ve hesabın **Farklı Çalıştır Hesapları** özellikleri dikey penceresinde *Tamamlanmadı* durumunu gösterir.

![Tamamlanmamış Farklı Çalıştır hesabı yapılandırma durumu](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config.png)

Farklı Çalıştır hesabını seçtiğinizde hesabın **Özellikler** bölmesinde aşağıdaki hata iletisi görüntülenir:

![Tamamlanmamış Farklı Çalıştır yapılandırma uyarısı iletisi](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config-msg.png).

Hesabı silip yeniden oluşturarak Farklı Çalıştır hesabıyla ilgili bu sorunları hızlı bir şekilde çözebilirsiniz.

## <a name="update-your-automation-account-by-using-powershell"></a>PowerShell kullanarak Otomasyon hesabınızı güncelleştirme
Aşağıdaki durumlarda var olan Otomasyon hesabınızı güncelleştirmek için PowerShell kullanabilirsiniz:

* Bir Otomasyon hesabı oluşturdunuz, ancak Farklı Çalıştır hesabı oluşturmayı reddettiniz.
* Resource Manager kaynaklarını yönetmek için bir Otomasyon hesabı zaten kullanıyorsunuz ve runbook kimlik doğrulaması için Farklı Çalıştır hesabını içerecek şekilde güncelleştirmek istiyorsunuz.
* Klasik kaynakları yönetmek için bir Otomasyon hesabı zaten kullanıyorsunuz ve yeni bir hesap oluşturup runbook’larınızı ve varlıklarınızı ona geçirmek yerine Klasik Farklı Çalıştır hesabını kullanacak şekilde güncelleştirmek istiyorsunuz.   
* Kuruluş sertifika yetkiliniz (CA) tarafından verilen bir sertifika kullanarak bir Farklı Çalıştır ve Klasik Farklı Çalıştır hesabı oluşturmak istiyorsunuz.

Betiğin aşağıdaki önkoşulları vardır:

* Betik yalnızca Azure Resource Manager 2.01 veya sonrasındaki modülleri içeren Windows 10 ve Windows Server 2016 işletim sistemlerinde çalıştırılabilir. Windows'un önceki sürümleri desteklenmez.
* Azure PowerShell 1.0 ve üzeri. PowerShell 1.0 sürümü hakkında bilgi için bkz. [Azure PowerShell’i yükleme ve yapılandırma](/powershell/azureps-cmdlets-docs).
* Aşağıdaki PowerShell betiğinde *–AutomationAccountName* ve *-ApplicationDisplayName* parametrelerinin değeri olarak başvurulan bir Otomasyon hesabı.

Betiklerin parametreleri için gerekli olan *SubscriptionID*, *ResourceGroup* ve *AutomationAccountName* değerlerini almak için aşağıdakileri yapın:
1. Azure portalında, **Otomasyon hesabı** dikey penceresinden Otomasyon hesabınızı ve ardından **Tüm ayarlar** öğesini seçin. 
2. **Tüm ayarlar** dikey penceresindeki **Hesap Ayarları** altında **Özellikler**’i seçin. 
3. **Özellikler** dikey penceresindeki değerleri not alın.

 ![Otomasyon hesabı "Özellikler" dikey penceresi](media/automation-sec-configure-azure-runas-account/automation-account-properties.png)  

### <a name="create-a-run-as-account-powershell-script"></a>Farklı Çalıştır hesabı PowerShell betiği oluşturma
Bu PowerShell betiği aşağıdaki yapılandırmalar için destek içerir:

* Otomatik olarak imzalanan sertifika kullanarak Farklı Çalıştır hesabı oluşturun.
* Otomatik olarak imzalanan sertifika kullanarak Farklı Çalıştır hesabı ve Klasik Farklı Çalıştır hesabı oluşturun.
* Kurumsal sertifika kullanarak Farklı Çalıştır hesabı ve Klasik Farklı Çalıştır hesabı oluşturun.
* Azure Kamu bulutunda otomatik olarak imzalanan sertifika kullanarak Farklı Çalıştır hesabı ve Klasik Farklı Çalıştır hesabı oluşturun.

Seçtiğiniz yapılandırma seçeneğine bağlı olarak, betik aşağıdaki öğeleri oluşturur.

**Farklı Çalıştır hesapları için:**

* Otomatik olarak imzalanan sertifika veya kurumsal sertifika ortak anahtarıyla dışarı aktarılacak bir Azure AD uygulaması oluşturur, Azure AD’de bu uygulama için bir hizmet sorumlusu hesabı oluşturur ve geçerli aboneliğinizde hesap için Katkıda Bulunan rolünü atar. Bu ayarı Sahip veya başka bir rolle değiştirebilirsiniz. Daha fazla bilgi için bkz. [Azure Otomasyonu’nda rol tabanlı erişim denetimi](automation-role-based-access-control.md).
* Belirtilen Otomasyon hesabında *AzureRunAsCertificate* adlı bir Otomasyon sertifikası varlığı oluşturur. Sertifika varlıkları, Azure AD uygulaması tarafından kullanılan sertifika özel anahtarını içerir.
* Belirtilen Otomasyon hesabında *AzureRunAsConnection* adlı bir Otomasyon bağlantı varlığı oluşturur. Bağlantı varlığı applicationId, tenantId, subscriptionId ve sertifika parmak izini içerir.

**Klasik Farklı Çalıştır hesapları için:**

* Belirtilen Otomasyon hesabında *AzureClassicRunAsCertificate* adlı bir Otomasyon sertifikası varlığı oluşturur. Sertifika varlığı, yönetim sertifikası tarafından kullanılan sertifika özel anahtarını içerir.
* Belirtilen Otomasyon hesabında *AzureClassicRunAsConnection* adlı bir Otomasyon bağlantı varlığı oluşturur. Bağlantı varlığı; abonelik adı, subscriptionId ve sertifika varlık adını içerir.

>[!NOTE]
> Klasik Farklı Çalıştır hesabı oluşturma seçeneğini belirlerseniz, betik yürütüldükten sonra Otomasyon hesabının oluşturulduğu abonelik için yönetim deposuna ortak sertifikayı (.cer dosya adı uzantısı) yükleyin.
> 

Betiği yürütüp sertifikayı karşıya yüklemek için aşağıdakileri yapın:

1. Aşağıdaki betiği bilgisayarınıza kaydedin. Bu örnekte *New-RunAsAccount.ps1* dosya adıyla kaydedin.

        #Requires -RunAsAdministrator
         Param (
        [Parameter(Mandatory=$true)]
        [String] $ResourceGroup,

        [Parameter(Mandatory=$true)]
        [String] $AutomationAccountName,

        [Parameter(Mandatory=$true)]
        [String] $ApplicationDisplayName,

        [Parameter(Mandatory=$true)]
        [String] $SubscriptionId,

        [Parameter(Mandatory=$true)]
        [Boolean] $CreateClassicRunAsAccount,

        [Parameter(Mandatory=$true)]
        [String] $SelfSignedCertPlainPassword,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPathForRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPlainPasswordForRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPathForClassicRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,

        [Parameter(Mandatory=$false)]
        [ValidateSet("AzureCloud","AzureUSGovernment")]
        [string]$EnvironmentName="AzureCloud",

        [Parameter(Mandatory=$false)]
        [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
        )

        function CreateSelfSignedCertificate([string] $keyVaultName, [string] $certificateName, [string] $selfSignedCertPlainPassword,
                                      [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
        $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
           -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
           -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired)

        $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose
        }

        function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {  
        $CurrentDate = Get-Date
        $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
        $KeyId = (New-Guid).Guid

        $KeyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
        $KeyCredential.StartDate = $CurrentDate
        $KeyCredential.EndDate= [DateTime]$PfxCert.GetExpirationDateString()
        $KeyCredential.KeyId = $KeyId
        $KeyCredential.CertValue  = $keyValue

        # Use key credentials and create an Azure AD application
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $KeyCredential
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id

        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
        $NewRole = New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6)
        {
           Sleep -s 10
           New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
           $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
           $Retries++;
        }
           return $Application.ApplicationId.ToString();
        }

        function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName,[string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force   
        Remove-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
        }

        function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzureRmAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
        }

        Import-Module AzureRM.Profile
        Import-Module AzureRM.Resources

        $AzureRMProfileVersion= (Get-Module AzureRM.Profile).Version
        if (!(($AzureRMProfileVersion.Major -ge 2 -and $AzureRMProfileVersion.Minor -ge 1) -or ($AzureRMProfileVersion.Major -gt 2)))
        {
           Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
           return
        }

        Login-AzureRmAccount -EnvironmentName $EnvironmentName
        $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId

        # Create a Run As account by using a service principal
        $CertifcateAssetName = "AzureRunAsCertificate"
        $ConnectionAssetName = "AzureRunAsConnection"
        $ConnectionTypeName = "AzureServicePrincipal"

        if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
        $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
        $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
        } else {
          $CertificateName = $AutomationAccountName+$CertifcateAssetName
          $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
          $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
          $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
          CreateSelfSignedCertificate $KeyVaultName $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create a service principal
        $PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
        $ApplicationId=CreateServicePrincipal $PfxCert $ApplicationDisplayName

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

        # Populate the ConnectionFieldValues
        $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
        $TenantID = $SubscriptionInfo | Select TenantId -First 1
        $Thumbprint = $PfxCert.Thumbprint
        $ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId}


        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

        if ($CreateClassicRunAsAccount) {
            # Create a Run As account by using a service principal
            $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
            $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
            $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
            $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
                    "Log in to the Microsoft Azure Management portal (https://manage.windowsazure.com) and select Settings -> Management Certificates." + [Environment]::NewLine +
                    "Then click Upload and upload the .cer format of #CERT#"

             if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
             $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
             $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
             $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
        } else {
             $ClassicRunAsAccountCertificateName = $AutomationAccountName+$ClassicRunAsAccountCertifcateAssetName
             $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
             $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
             $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
             $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
             CreateSelfSignedCertificate $KeyVaultName $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

        # Populate the ConnectionFieldValues
        $SubscriptionName = $subscription.Subscription.SubscriptionName
        $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName $ClassicRunAsAccountConnectionFieldValues

        Write-Host -ForegroundColor red $UploadMessage
        }

2. Bilgisayarınızda **Başlat**’a tıklayın ve yükseltilmiş kullanıcı haklarıyla **Windows PowerShell**’i başlatın.

3. Yükseltilmiş PowerShell komut satırı kabuğundan, 1. adımda oluşturduğunuz komut dosyasını içeren klasöre gidin.

4. İstediğiniz yapılandırmanın parametre değerlerini kullanarak betiği yürütün.

    **Otomatik olarak imzalanan sertifika kullanarak Farklı Çalıştır hesabı oluşturma**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false`

    **Otomatik olarak imzalanan sertifika kullanarak Farklı Çalıştır hesabı ve Klasik Farklı Çalıştır hesabı oluşturma**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true`

    **Kurumsal sertifika kullanarak Farklı Çalıştır hesabı ve Klasik Farklı Çalıştır hesabı oluşturma**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>`

    **Azure Kamu bulutunda otomatik olarak imzalanan sertifika kullanarak Farklı Çalıştır hesabı ve Klasik Farklı Çalıştır hesabı oluşturma**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment`

    > [!NOTE]
    > Betik yürütüldükten sonra Azure kimlik doğrulamasını yapmanız istenecektir. Aboneliğin yöneticiler rolünün üyesi ve aboneliğin ortak yöneticisi olan bir hesapla oturum açın.
    >
    >

Betik başarıyla yürütüldükten sonra aşağıdakilere dikkat edin:
* Otomatik olarak imzalanan bir ortak sertifika (.cer dosyası) ile Klasik Farklı Çalıştır hesabı oluşturduysanız, betik bu hesabı oluşturup bilgisayarınızdaki geçici dosya klasörüne, PowerShell oturumunu yürütmek için kullandığınız *%USERPROFILE%\AppData\Local\Temp* kullanıcı profili altında kaydeder.
* Kurumsal ortak sertifika (.cer file) ile bir Klasik Farklı Çalıştır hesabı oluşturduysanız bu sertifikayı kullanın. [Klasik Azure portalında yönetim API sertifikayı yükleme](../azure-api-management-certs.md) yönergelerini izleyin ve ardından [Service Management Kaynakları ile kimlik doğrulamaya yönelik örnek kodu](#sample-code-to-authenticate-with-service-management-resources) kullanarak Service Management kaynakları ile kimlik bilgisi yapılandırmasını doğrulayın. 
* Klasik Farklı Çalıştır hesabı *oluşturmadıysanız*, Resource Manager kaynakları kimlik doğrulaması yapmak ve kimlik bilgisi yapılandırmasını doğrulamak için [Service Management kaynakları ile kimlik doğrulamaya yönelik örnek kodu](#sample-code-to-authenticate-with-resource-manager-resources) kullanın.

## <a name="sample-code-to-authenticate-with-resource-manager-resources"></a>Resource Manager kaynaklarıyla kimlik doğrulaması için örnek kod
Runbook’larınızla Resource Manager kaynaklarını yönetecek Farklı Çalıştır hesabını kullanarak kimlik doğrulaması yapmak için *AzureAutomationTutorialScript* örnek runbook’undan alınan aşağıdaki güncelleştirilmiş örnek kodu kullanabilirsiniz.

    $connectionName = "AzureRunAsConnection"
    $SubId = Get-AutomationVariable -Name 'SubscriptionId'
    try
    {
       # Get the connection "AzureRunAsConnection "
       $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

       "Signing in to Azure..."
       Add-AzureRmAccount `
         -ServicePrincipal `
         -TenantId $servicePrincipalConnection.TenantId `
         -ApplicationId $servicePrincipalConnection.ApplicationId `
         -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
       "Setting context to a specific subscription"     
       Set-AzureRmContext -SubscriptionId $SubId              
    }
    catch {
        if (!$servicePrincipalConnection)
        {
           $ErrorMessage = "Connection $connectionName not found."
           throw $ErrorMessage
         } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
         }
    }

Bu betikte, birden fazla abonelik arasında kolayca çalışmanıza yardımcı olmak üzere abonelik bağlamına başvuruyu destekleyen iki ek kod satırı bulunur. *SubscriptionId* adlı değişken varlık, aboneliğin kimliğini içerir. `Add-AzureRmAccount` cmdlet’i belirtildikten sonra *-SubscriptionId* parametre kümesi ile [`Set-AzureRmContext`](https://msdn.microsoft.com/library/mt619263.aspx) cmdlet’i belirtilir. Değişken adı çok genelse, tanımlanmasını kolaylaştırmak amacıyla bir ön ek veya başka diğer adlandırma kuralı kullanmak için değişken adını gözden geçirebilirsiniz. Alternatif olarak, ilgili değişken varlığıyla *-SubscriptionId* yerine *-SubscriptionName* parametre kümesini kullanabilirsiniz.

Runbook’ta kimlik doğrulaması için kullandığınız cmdlet `Add-AzureRmAccount`, *ServicePrincipalCertificate* parametre kümesini kullanır. Kullanıcı kimlik bilgilerini değil, hizmet sorumlusu sertifikasını kullanarak kimlik doğrulaması yapar.

## <a name="sample-code-to-authenticate-with-service-management-resources"></a>Service Management kaynaklarıyla kimlik doğrulaması için örnek kod
Runbook’larınızla klasik kaynakları yönetecek Klasik Farklı Çalıştır hesabını kullanarak kimlik doğrulaması yapmak için *AzureClassicAutomationTutorialScript* örnek runbook’undan alınan aşağıdaki güncelleştirilmiş örnek kodu kullanabilirsiniz.

    $ConnectionAssetName = "AzureClassicRunAsConnection"
    # Get the connection
    $connection = Get-AutomationConnection -Name $connectionAssetName        

    # Authenticate to Azure with certificate
    Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
    if ($Conn -eq $null)
    {
       throw "Could not retrieve connection asset: $ConnectionAssetName. Assure that this asset exists in the Automation account."
    }

    $CertificateAssetName = $Conn.CertificateAssetName
    Write-Verbose "Getting the certificate: $CertificateAssetName" -Verbose
    $AzureCert = Get-AutomationCertificate -Name $CertificateAssetName
    if ($AzureCert -eq $null)
    {
       throw "Could not retrieve certificate asset: $CertificateAssetName. Assure that this asset exists in the Automation account."
    }

    Write-Verbose "Authenticating to Azure with certificate." -Verbose
    Set-AzureSubscription -SubscriptionName $Conn.SubscriptionName -SubscriptionId $Conn.SubscriptionID -Certificate $AzureCert
    Select-AzureSubscription -SubscriptionId $Conn.SubscriptionID

## <a name="next-steps"></a>Sonraki adımlar
* [Azure AD’de uygulama ve hizmet sorumlusu nesneleri](../active-directory/active-directory-application-objects.md)
* [Azure Otomasyonu’nda rol tabanlı erişim denetimi](automation-role-based-access-control.md)
* [Azure Cloud Services’da sertifikalara genel bakış](../cloud-services/cloud-services-certs-create.md)

