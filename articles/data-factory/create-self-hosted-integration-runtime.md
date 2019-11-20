---
title: Azure Data Factory içinde şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma
description: Azure Data Factory ' de şirket içinde barındırılan bir tümleştirme çalışma zamanı oluşturmayı öğrenin. Bu, veri fabrikalarının özel bir ağdaki veri depolarına erişmesine olanak sağlar.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/18/2019
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 594d4b941f3ed67daa4e1cfd57c2f5539e0cb9ee
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73677181"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>Şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma ve yapılandırma
Integration Runtime (IR), farklı ağ ortamlarında veri tümleştirme özellikleri sağlamak için Azure Data Factory tarafından kullanılan işlem altyapısıdır. IR ile ilgili ayrıntılar için bkz. [tümleştirme çalışma zamanına genel bakış](concepts-integration-runtime.md).

Şirket içinde barındırılan tümleştirme çalışma zamanı, bir bulut veri deposu ve bir veri deposu arasındaki kopyalama etkinliklerini özel bir ağda çalıştırabilir ve şirket içi bir ağda veya Azure sanal ağında işlem kaynaklarına karşı dönüştürme etkinliklerini gönderebilir. Şirket içinde barındırılan tümleştirme çalışma zamanının yüklemesi, bir şirket içi makinede veya bir sanal makinede (VM) özel ağ içinde olmalıdır.  

Bu belge, kendinden konak IR oluşturma ve yapılandırma işlemini açıklar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="setting-up-a-self-hosted-integration-runtime"></a>Şirket içinde barındırılan Integration Runtime ayarlama

Şirket içinde barındırılan bir tümleştirme çalışma zamanı oluşturmak ve ayarlamak için aşağıdaki yöntemleri kullanabilirsiniz:

### <a name="create-a-self-hosted-ir-via-azure-powershell"></a>Azure PowerShell aracılığıyla kendinden konak IR oluşturma 

1. Bu görev için Azure PowerShell kullanabilirsiniz. Örnek aşağıda verilmiştir:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. Şirket içinde barındırılan tümleştirme çalışma zamanını yerel bir makineye [indirip](https://www.microsoft.com/download/details.aspx?id=39717) yükleyin.

3. Kimlik doğrulama anahtarını alın ve şirket içinde barındırılan tümleştirme çalışma zamanını anahtarla kaydettirin. Bir PowerShell örneği aşağıda verilmiştir:

    ```powershell

    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName  

    ```

### <a name="create-a-self-hosted-ir-via-azure-data-factory-ui"></a>Azure Data Factory Kullanıcı arabirimi aracılığıyla kendinden konak IR oluşturma

Azure Data Factory Kullanıcı arabirimini kullanarak kendinden konak IR oluşturmak için aşağıdaki adımları izleyebilirsiniz. 

1. Azure Data Factory Kullanıcı arabiriminin **Başlarken sayfasında** sol bölmedeki **Yazar** sekmesini seçin.

   ![Giriş sayfası yazar düğmesi](media/doc-common-process/get-started-page-author-button.png)
2. Sol bölmenin altındaki **Bağlantılar** ' a tıklayın ve bağlantılar penceresinde **tümleştirme çalışma zamanları** ' na geçin. **+ Yeni**seçeneğini belirleyin.

   ![Tümleştirme çalışma zamanı](media/create-self-hosted-integration-runtime/new-integration-runtime.png)
3. **Tümleştirme çalışma zamanı kurulumu** penceresinde, *veri taşıma ve dağıtım etkinliklerini dış hesaplamalar*' ı seçin ve **devam**' a tıklayın.
4. IR 'niz için bir ad girin ve **Oluştur**' u seçin.
5. Bilgisayarınızda hızlı kurulumu başlatmak için **1 seçeneğini** kullanabilirsiniz. Alternatif olarak, el ile ayarlamak için **2 seçeneğini** kullanın. Aşağıdaki yönergeler **el ile kuruluma**dayalıdır:

   ![Tümleştirme çalışma zamanı kurulumu](media/create-self-hosted-integration-runtime/integration-runtime-setting-up.png)

    1. Kimlik doğrulama anahtarını kopyalayıp yapıştırın. **Tümleştirme çalışma zamanını indir ve Yükle ' yi**seçin.
        
    1. Şirket içinde barındırılan tümleştirme çalışma zamanını yerel bir Windows makinesine indirin. Yüklemeyi çalıştırın.
        
    1. **Integration Runtime’ı (Şirket içinde barındırılan) Kaydet** sayfasında, önceki bölümde kaydettiğiniz anahtarı yapıştırın ve **Kaydol**'u seçin.
    
       ![Tümleştirme çalışma zamanını kaydetme](media/create-self-hosted-integration-runtime/register-integration-runtime.png)

    1. **Yeni Integration Runtime (Şirket içinde barındırılan) düğümü** sayfasında **son**' u seçin. 

6. Şirket içinde barındırılan tümleştirme çalışma zamanı başarıyla kaydedildiğinde aşağıdaki iletiyi görürsünüz:

    ![Başarıyla kaydedildi](media/create-self-hosted-integration-runtime/registered-successfully.png)

### <a name="set-up-a-self-hosted-ir-on-azure-vm-via-azure-resource-manager-template"></a>Azure VM 'de Azure Resource Manager şablonu aracılığıyla şirket içinde barındırılan bir IR ayarlama 
[Bu Azure Resource Manager şablonunu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime)kullanarak bir Azure sanal makinesinde kendınden konak IR kurulumunu otomatik hale getirebilirsiniz. Bu şablon, yüksek kullanılabilirlik ve ölçeklenebilirlik özelliklerine sahip bir Azure sanal ağı içinde şirket içinde barındırılan bir IR 'nin tam olarak işlevsel olması için kolay bir yol sağlar (düğüm sayısını 2 veya daha yüksek olarak ayarladığınız sürece).

### <a name="set-up-an-existing-self-hosted-ir-via-local-powershell"></a>Yerel PowerShell aracılığıyla mevcut bir şirket içinde barındırılan IR ayarlama

Mevcut bir şirket içinde barındırılan IR 'yi ayarlamak veya yönetmek için komut satırını kullanabilirsiniz. Bu, özellikle yüklemeyi otomatikleştirmek için, şirket içinde barındırılan IR düğümlerinin kaydını yapmak için kullanılabilir. 

**Dmgcmd. exe** , genellikle: C:\Program Files\Microsoft Integration Runtime\3,\shared\ klasöründe bulunan şirket içinde barındırılan yüklemeye dahildir. Bu, çeşitli parametreleri destekler ve otomasyon için Batch betikleri kullanılarak komut istemi aracılığıyla çağrılabilir. 

*Kullanımıyla* 

```powershell
dmgcmd [ -RegisterNewNode "<AuthenticationKey>" -EnableRemoteAccess "<port>" ["<thumbprint>"] -EnableRemoteAccessInContainer "<port>" ["<thumbprint>"] -DisableRemoteAccess -Key "<AuthenticationKey>" -GenerateBackupFile "<filePath>" "<password>" -ImportBackupFile "<filePath>" "<password>" -Restart -Start -Stop -StartUpgradeService -StopUpgradeService -TurnOnAutoUpdate -TurnOffAutoUpdate -SwitchServiceAccount "<domain\user>" ["password"] -Loglevel <logLevel> ] 
```

 *Ayrıntılar (parametreler/özellik):* 

| Özellik                                                    | Açıklama                                                  | Gerekli |
| ----------------------------------------------------------- | ------------------------------------------------------------ | -------- |
| RegisterNewNode "`<AuthenticationKey>`"                     | Integration Runtime (şirket içinde barındırılan) düğümünü belirtilen kimlik doğrulama anahtarıyla Kaydet | Hayır       |
| EnableRemoteAccess "`<port>`" ["`<thumbprint>`"]            | Yüksek kullanılabilirlik kümesi ayarlamak ve/veya kimlik bilgilerinin otomatik olarak barındırılan IR (ADF hizmetinden geçmeden) **ile doğrudan barındırılan IR 'ye karşı ayarlanmasını sağlamak için geçerli düğümde uzaktan erişimi etkinleştirin.** Aynı ağdaki bir uzak makineden New-AzDataFactoryV2LinkedServiceEncryptedCredential cmdlet 'i. | Hayır       |
| Enableremoteaccessıncontainer "`<port>`" ["`<thumbprint>`"] | Düğüm kapsayıcıda çalışırken geçerli düğüme uzaktan erişimi etkinleştir | Hayır       |
| DisableRemoteAccess                                         | Geçerli düğüme uzaktan erişimi devre dışı bırakın. Çok düğümlü kurulum için uzaktan erişim gerekir. Yeni-**AzDataFactoryV2LinkedServiceEncryptedCredential** PowerShell cmdlet 'i, uzaktan erişim, şirket IÇINDE barındırılan IR düğümüyle aynı makinede yürütüldüğü sürece hala devre dışı bırakıldığında bile çalışmaya devam eder. | Hayır       |
| "`<AuthenticationKey>`" anahtarı                                 | Önceki kimlik doğrulama anahtarının üzerine yaz/güncelleştir. Bu, anahtar yeni bir tümleştirme çalışma zamanı ise, bu, önceki şirket içinde barındırılan IR düğümünüz çevrimdışı duruma neden olduğundan emin olun. | Hayır       |
| GenerateBackupFile "`<filePath>`" "`<password>`"            | Geçerli düğüm için yedek dosya oluştur, yedekleme dosyası düğüm anahtarını ve veri deposu kimlik bilgilerini içerir | Hayır       |
| Importbackupfile "`<filePath>`" "`<password>`"              | Düğümü bir yedekleme dosyasından geri yükleme                          | Hayır       |
| Yeniden Başlatma                                                     | Integration Runtime (şirket içinde barındırılan) ana bilgisayar hizmetini yeniden başlatın   | Hayır       |
| Başlatma                                                       | Integration Runtime (şirket içinde barındırılan) ana bilgisayar hizmetini başlatın     | Hayır       |
| Durdur                                                        | Güncelleştirme hizmetini Integration Runtime (şirket içinde barındırılan) Durdur        | Hayır       |
| StartUpgradeService                                         | Güncelleştirme hizmeti Integration Runtime (şirket içinde barındırılan) Başlat       | Hayır       |
| StopUpgradeService                                          | Güncelleştirme hizmetini Integration Runtime (şirket içinde barındırılan) Durdur        | Hayır       |
| Turnonotomatik güncelleştirme                                            | Otomatik güncelleştirme Integration Runtime (şirket içinde barındırılan) aç        | Hayır       |
| TurnOffAutoUpdate                                           | Otomatik güncelleştirme Integration Runtime (şirket içinde barındırılan) Kapat       | Hayır       |
| SwitchServiceAccount "< etkialanı \ Kullanıcı >" ["parola"]           | DIAHostService ' i yeni bir hesap olarak çalışacak şekilde ayarlayın. Sistem hesabı veya sanal hesap için boş parola ("") kullan | Hayır       |
| LogLevel `<logLevel>`                                       | ETW günlük düzeyini ayarlayın (kapalı, hata, ayrıntılı veya tümü). Genellikle hata ayıklanırken Microsoft desteği tarafından kullanılır. | Hayır       |


## <a name="command-flow-and-data-flow"></a>Komut akışı ve veri akışı
Şirket içi ve bulut arasında veri taşıdığınızda, etkinlik şirket içi veri kaynağından buluta veri aktarmak için şirket içinde barındırılan bir tümleştirme çalışma zamanı kullanır ve tam tersi de geçerlidir.

Şirket içinde barındırılan bir IR ile kopyalama adımlarının Özeti için üst düzey bir veri akışı aşağıda verilmiştir:

![Yüksek düzey genel bakış](media/create-self-hosted-integration-runtime/high-level-overview.png)

1. Veri geliştiricisi, bir PowerShell cmdlet 'i kullanarak bir Azure Data Factory içinde şirket içinde barındırılan bir tümleştirme çalışma zamanı oluşturur. Şu anda Azure portal bu özelliği desteklemez.
2. Veri geliştiricisi, veri depolarına bağlanmak için kullanması gereken şirket içinde barındırılan tümleştirme çalışma zamanı örneğini belirterek şirket içi veri deposu için bağlı bir hizmet oluşturur.
3. Şirket içinde barındırılan tümleştirme çalışma zamanı düğümü, Windows Data Protection uygulama programlama arabirimini (DPAPI) kullanarak kimlik bilgilerini şifreler ve kimlik bilgilerini yerel olarak kaydeder. Yüksek kullanılabilirlik için birden çok düğüm ayarlandıysa, kimlik bilgileri diğer düğümlerde daha fazla eşitlenir. Her düğüm, DPAPI kullanarak kimlik bilgilerini şifreler ve yerel olarak depolar. Kimlik bilgisi eşitleme, veri geliştiricisi tarafından saydamdır ve şirket içinde barındırılan IR tarafından işlenir.    
4. Data Factory hizmeti, paylaşılan bir [Azure Service Bus geçişi](https://docs.microsoft.com/azure/service-bus-relay/relay-what-is-it#wcf-relay)kullanan bir *denetim kanalı* aracılığıyla işlerin zamanlanması ve yönetilmesi için şirket içinde barındırılan tümleştirme çalışma zamanı ile iletişim kurar. Bir etkinlik işinin çalıştırılması gerektiğinde Data Factory, isteği herhangi bir kimlik bilgisi bilgisiyle birlikte sıraya alır (Bu kimlik bilgileri şirket içinde barındırılan tümleştirme çalışma zamanı 'nda zaten depolanmaz). Şirket içinde barındırılan tümleştirme çalışma zamanı, kuyruğu yokladıktan sonra işi devre dışı bırakır.
5. Şirket içinde barındırılan tümleştirme çalışma zamanı, verileri şirket içi bir mağazadan bulut depolama alanına kopyalar veya kopyalama etkinliğinin veri ardışık düzeninde nasıl yapılandırıldığına bağlı olarak tam tersi olur. Bu adım için, şirket içinde barındırılan tümleştirme çalışma zamanı, güvenli (HTTPS) kanal üzerinden Azure Blob depolama gibi bulut tabanlı depolama hizmetleriyle doğrudan iletişim kurar.

## <a name="considerations-for-using-a-self-hosted-ir"></a>Şirket içinde barındırılan IR kullanma konuları

- Tek bir şirket içinde barındırılan tümleştirme çalışma zamanı, birden çok şirket içi veri kaynağı için kullanılabilir. Tek bir şirket içinde barındırılan tümleştirme çalışma zamanı, aynı Azure Active Directory kiracısında başka bir veri fabrikasıyla paylaşılabilir. Daha fazla bilgi için bkz. [Şirket içinde barındırılan tümleştirme çalışma zamanını paylaşma](#create-a-shared-self-hosted-integration-runtime-in-azure-data-factory).
- Tek bir makinede şirket içinde barındırılan tümleştirme çalışma zamanının yalnızca bir örneğine sahip olabilirsiniz. Şirket içi veri kaynaklarına erişmesi gereken iki veri fabrikası varsa, şirket içinde barındırılan tümleştirme çalışma zamanını paylaşmak veya şirket içinde barındırılan tümleştirme çalışma zamanını iki şirket içi bilgisayara [yüklemek için bir](#create-a-shared-self-hosted-integration-runtime-in-azure-data-factory) Her veri fabrikası.  
- Şirket içinde barındırılan tümleştirme çalışma zamanının veri kaynağıyla aynı makinede olması gerekmez. Ancak, şirket içinde barındırılan tümleştirme çalışma zamanının veri kaynağına yakın olması, şirket içinde barındırılan tümleştirme çalışma zamanının veri kaynağına bağlanması için geçen süreyi azaltır. Şirket içinde barındırılan tümleştirme çalışma zamanını şirket içi veri kaynağını barındıran bilgisayardan farklı bir makineye yüklemenizi öneririz. Şirket içinde barındırılan tümleştirme çalışma zamanı ve veri kaynağı farklı makinelerinizde, şirket içinde barındırılan tümleştirme çalışma zamanı veri kaynağı olan kaynaklar için rekabet etmez.
- Aynı şirket içi veri kaynağına bağlanan farklı makinelerde şirket içinde barındırılan birden çok tümleştirme çalışma zamanı olabilir. Örneğin, iki veri fabrikası sunan iki şirket içinde barındırılan tümleştirme çalışma zamanı olabilir, ancak aynı şirket içi veri kaynağı her iki veri fabrikasıyla de kaydedilir.
- Bilgisayarınızda Power BI senaryoya yönelik olarak yüklü bir ağ geçidiniz varsa, başka bir makineye Azure Data Factory için otomatik olarak barındırılan ayrı bir tümleştirme çalışma zamanı yükleyebilirsiniz.
- Şirket içinde barındırılan tümleştirme çalışma zamanı, bir Azure sanal ağı içindeki veri tümleştirmesini desteklemek için kullanılmalıdır.
- Azure ExpressRoute kullandığınızda bile, veri kaynağınızı bir güvenlik duvarının arkasındaki şirket içi veri kaynağı olarak değerlendirin. Hizmet ve veri kaynağı arasında bağlantı kurmak için şirket içinde barındırılan tümleştirme çalışma zamanını kullanın.
- Veri deposu bir Azure IaaS sanal makinesinde bulutta olsa bile, şirket içinde barındırılan tümleştirme çalışma zamanını kullanmanız gerekir.
- Görevler, FIPS uyumlu şifrelemenin etkinleştirildiği bir Windows Server 'da yüklü olan şirket içinde barındırılan bir tümleştirme çalışma zamanında başarısız olabilir. Bu sorunu geçici olarak çözmek için sunucuda FIPS uyumlu şifrelemeyi devre dışı bırakın. FIPS uyumlu şifrelemeyi devre dışı bırakmak için, aşağıdaki kayıt defteri değerini 1 (etkin) iken 0 (devre dışı) olarak değiştirin: `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`.

## <a name="prerequisites"></a>Ön koşullar

- Desteklenen işletim sistemi sürümleri, Windows 7 Service Pack 1, Windows 8.1, Windows 10, Windows Server 2008 R2 SP1, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016 ve Windows Server 2019. Şirket içinde barındırılan tümleştirme çalışma zamanının bir etki alanı denetleyicisine yüklenmesi desteklenmez.
- .NET Framework 4.6.1 veya üzeri gereklidir. Şirket içinde barındırılan tümleştirme çalışma zamanını bir Windows 7 makinesine yüklüyorsanız, .NET Framework 4.6.1 veya üstünü yükleyebilirsiniz. Ayrıntılar için [.NET Framework sistem gereksinimleri](/dotnet/framework/get-started/system-requirements) ' ne bakın.
- Şirket içinde barındırılan tümleştirme çalışma zamanı makinesi için önerilen yapılandırma en az 2 GHz, dört çekirdek, 8 GB RAM ve 80 GB bir disk.
- Ana makine hazırda beklemesi durumunda, şirket içinde barındırılan tümleştirme çalışma zamanı veri isteklerine yanıt vermez. Şirket içinde barındırılan tümleştirme çalışma zamanını yüklemeden önce bilgisayarda uygun bir güç planı yapılandırın. Makine hazırda beklemeye yapılandırıldıysa, şirket içinde barındırılan tümleştirme çalışma zamanı yüklemesi bir ileti ister.
- Şirket içinde barındırılan tümleştirme çalışma zamanını başarılı bir şekilde yüklemek ve yapılandırmak için makinede yönetici olmanız gerekir.
- Kopyalama etkinliği çalıştırmaları belirli bir sıklıkta gerçekleşir. Makinedeki kaynak kullanımı (CPU, bellek), yoğun ve boşta zamanlarla aynı düzene uyar. Kaynak kullanımı Ayrıca, taşınmakta olan veri miktarına göre büyük ölçüde farklılık gösterir. Birden çok kopyalama işi devam ederken, yoğun saatlerde kaynak kullanımını görürsünüz.
- Parquet, ORC veya avro biçimlerinde veri ayıklandıysanız görevler başarısız olabilir. Dosya oluşturma, şirket içinde barındırılan tümleştirme makinesinde çalışır ve aşağıdaki önkoşulların beklendiği gibi çalışmasını gerektirir ( [Azure Data Factory, bkz. Parquet biçimi](https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime)).
    - [Visual C++ 2010 yeniden dağıtılabilir](https://download.microsoft.com/download/3/2/2/3224B87F-CFA0-4E70-BDA3-3DE650EFEBA5/vcredist_x64.exe) paketi (x64)
    - Java Runtime (JRE) sürüm 8, bir JRE sağlayıcısından, `JAVA_HOME` ortam değişkeninin ayarlanmış olduğunu doğrulayarak [OpenJDK benimseyin](https://adoptopenjdk.net/).

## <a name="installation-best-practices"></a>En iyi yükleme uygulamaları
Şirket içinde barındırılan tümleştirme çalışma zamanını, [Microsoft Indirme merkezi](https://www.microsoft.com/download/details.aspx?id=39717)' nden bir MSI kurulum paketi indirerek yükleyebilirsiniz. Adım adım yönergeler için bkz. Şirket [içi ve bulut makalesi arasında veri taşıma](tutorial-hybrid-copy-powershell.md) .

- Makinenin hazırda bekletmeden olmaması için, şirket içinde barındırılan tümleştirme çalışma zamanı için konak makinede bir güç planı yapılandırın. Ana makine hazırda bekletmeye başlarsa, şirket içinde barındırılan tümleştirme çalışma zamanı çevrimdışı olarak geçer.
- Şirket içinde barındırılan tümleştirme çalışma zamanı ile ilişkili kimlik bilgilerini düzenli olarak yedekleyin.
- Şirket içinde barındırılan IR kurulum işlemlerini otomatik hale getirmek için lütfen [Bu bölümde](#setting-up-a-self-hosted-integration-runtime) **PowerShell aracılığıyla mevcut bir ŞIRKET içinde barındırılan IR 'yi ayarlama** bölümüne bakın.  

## <a name="install-and-register-self-hosted-ir-from-the-download-center"></a>Indirme merkezinden şirket içinde barındırılan IR yükleme ve kaydetme

1. [Microsoft tümleştirme çalışma zamanı indirme sayfasına](https://www.microsoft.com/download/details.aspx?id=39717)gidin.
2. **İndir**' i seçin, 64 bit sürümünü (32 bit desteklenmez) seçin ve **İleri**' yi seçin.
3. MSI dosyasını doğrudan çalıştırın veya sabit diskinize kaydedin ve çalıştırın.
4. **Hoş geldiniz** sayfasında bir dil seçin ve **İleri**' yi seçin.
5. Microsoft yazılımı lisans koşulları 'nı kabul edin ve **İleri ' yi**seçin.
6. Şirket içinde barındırılan tümleştirme çalışma zamanını yüklemek için **klasör** ' ü seçin ve **İleri**' yi seçin.
7. **Yüklemeye hazırlanma** sayfasında, **yükler**' i seçin.
8. Yüklemeyi tamamlamaya **son** ' a tıklayın.
9. Azure PowerShell kullanarak kimlik doğrulama anahtarını alın. Kimlik doğrulama anahtarını almaya yönelik bir PowerShell örneği aşağıda verilmiştir:

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```
11. Makinenizde çalışan Microsoft Integration Runtime Configuration Manager **Integration Runtime (Şirket içinde barındırılan) Kaydet** sayfasında, aşağıdaki adımları uygulayın:

    a. Kimlik doğrulama anahtarını metin alanına yapıştırın.

    b. İsteğe bağlı olarak, anahtar metnini görmek için **kimlik doğrulama anahtarını göster** ' i seçin.

    c. **Kaydol**’u seçin.


## <a name="high-availability-and-scalability"></a>Yüksek kullanılabilirlik ve ölçeklenebilirlik
Şirket içinde barındırılan bir tümleştirme çalışma zamanı, Azure 'daki birden çok şirket içi makine veya sanal makine ile ilişkilendirilebilir. Bu makinelere düğüm denir. Şirket içinde barındırılan bir tümleştirme çalışma zamanı ile ilişkili en fazla dört düğümünüz olabilir. Mantıksal bir ağ geçidi için birden çok düğüm (örneğin, ağ geçidine yüklenmiş şirket içi makineler) olmanın avantajları şunlardır:
* Şirket içinde barındırılan tümleştirme çalışma zamanının, büyük veri çözümünüzde veya Azure Data Factory ile bulut veri tümleştirmesinde, en fazla dört düğüm ile devamlılığını sağlamak için yüksek bir başarısızlık noktası olmaması için daha yüksek kullanılabilirlik.
* Şirket içi ve bulut veri depoları arasında veri taşıma sırasında geliştirilmiş performans ve verimlilik. [Performans karşılaştırmaları](copy-activity-performance.md)hakkında daha fazla bilgi alın.

Şirket içinde barındırılan tümleştirme çalışma zamanı yazılımını [Indirme merkezinden](https://www.microsoft.com/download/details.aspx?id=39717)yükleyerek birden çok düğümü ilişkilendirebilirsiniz. Ardından, [öğreticide](tutorial-hybrid-copy-powershell.md)açıklandığı gibi **New-AzDataFactoryV2IntegrationRuntimeKey** cmdlet 'inden alınan kimlik doğrulama anahtarlarından birini kullanarak kaydedin.

> [!NOTE]
> Her bir düğümün ilişkilendirilmesi için şirket içinde barındırılan yeni tümleştirme çalışma zamanı oluşturmanız gerekmez. Şirket içinde barındırılan tümleştirme çalışma zamanını başka bir makineye yükleyebilir ve aynı kimlik doğrulama anahtarını kullanarak kaydedebilirsiniz. 

> [!NOTE]
> Yüksek kullanılabilirlik ve ölçeklenebilirlik için başka bir düğüm eklemeden önce, ilk düğümde **intranet 'e uzaktan erişim** seçeneğinin (**Microsoft Integration Runtime Configuration Manager** > **ayarları** ) etkinleştirildiğinden emin olun > **Intranete uzaktan erişim**). 

### <a name="scale-considerations"></a>Ölçek konuları

#### <a name="scale-out"></a>Ölçeği genişletme

Şirket içinde barındırılan IR üzerindeki kullanılabilir bellek düşükse ve CPU kullanımı yüksekse, yeni bir düğüm eklemek, makinelerin tamamında yükün ölçeğini belirlemenize yardımcı olur. Etkinlik zaman aşımına uğradığından veya şirket içinde barındırılan IR düğümü çevrimdışı olduğundan, ağ geçidine bir düğüm eklemenize yardımcı olur.

#### <a name="scale-up"></a>Ölçeği artırma

Kullanılabilir bellek ve CPU iyi şekilde kullanılmaz, ancak eşzamanlı işlerin yürütülmesi sınıra ulaşarak, bir düğümde çalışabilecek eşzamanlı işlerin sayısını artırarak ölçeklendirmelisiniz. Ayrıca, şirket içinde barındırılan IR aşırı yüklendiği için etkinlikler zaman aşımına uğradığından ölçeğini ölçeklendirmek isteyebilirsiniz. Aşağıdaki görüntüde gösterildiği gibi, bir düğüm için maksimum kapasiteyi artırabilirsiniz:  

![Bir düğümde çalışabilecek eşzamanlı işleri artırma](media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>TLS/SSL sertifikası gereksinimleri

Tümleştirme çalışma zamanı düğümleri arasındaki iletişimin güvenliğini sağlamak için kullanılan TLS/SSL sertifikasına yönelik gereksinimler şunlardır:

- Sertifika, genel olarak güvenilen bir x509 v3 sertifikası olmalıdır. Ortak (ortak) sertifika yetkilisi (CA) tarafından verilen sertifikaları kullanmanızı öneririz.
- Her Integration Runtime düğümü bu sertifikaya güvenmelidir.
- Konu alternatif adı (SAN) sertifikaları önermediğimiz için, yalnızca son SAN öğesi kullanılacak ve geçerli sınırlamalar nedeniyle diğerleri yok sayılacak. Örneğin, San 'Lar **node1.domain.contoso.com** ve **node2.DOMAIN.contoso.com**olan bir San sertifikanız varsa, bu sertifikayı yalnızca FQDN 'si **node2.domain.contoso.com**olan bir makinede kullanabilirsiniz.
- Sertifika, SSL sertifikaları için Windows Server 2012 R2 tarafından desteklenen anahtar boyutunu destekler.
- CNG anahtarları kullanan sertifikalar desteklenmez.  

> [!NOTE]
> Bu sertifika, **düğümden düğüme iletişim** için kullanılan (bağlı hizmetlerin düğümler arasında kimlik bilgileri eşitlemesini içeren durum eşitlemesi için) ve **için PowerShell cmdlet 'ini kullanırken, ŞIRKET içinde barındırılan IR düğümündeki bağlantı noktalarını şifrelemek için kullanılır. Yerel ağ içinden bağlı hizmet kimlik bilgileri ayarı** . Özel ağ ortamınız güvenli değilse veya özel ağınız içindeki düğümler arasındaki iletişimin güvenliğini sağlamak istiyorsanız bu sertifikayı kullanmanızı öneririz. Şirket içinde barındırılan IR 'den diğer veri depolarına geçişte veri taşıma, bu sertifika kümesinden bağımsız olarak her zaman şifreli kanal kullanılarak gerçekleşir. 

## <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Azure Data Factory içinde, şirket içinde barındırılan, paylaşılan bir tümleştirme çalışma zamanı oluşturma

Zaten bir veri fabrikasında ayarlamış olduğunuz mevcut, şirket içinde barındırılan bir tümleştirme çalışma zamanı altyapısını yeniden kullanabilirsiniz. Bu, mevcut bir şirket içinde barındırılan IR 'ye (paylaşılan) başvurarak farklı bir veri fabrikasında *bağlı, şirket içinde barındırılan bir tümleştirme çalışma zamanı* oluşturmanızı sağlar.

Bu özelliğin on iki dakikalık bir giriş ve gösterimi için aşağıdaki videoyu izleyin:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>Terminoloji

- **PAYLAŞıLAN IR**: fiziksel bir altyapı üzerinde çalışan, otomatik olarak BARıNDıRıLAN ilk IR.  
- **Bağlı IR**: başka BIR paylaşılan IR 'ye başvuran IR. Bu bir mantıksal IR ve diğer şirket içinde barındırılan IR (paylaşılan) altyapısını kullanır.

### <a name="methods-to-share-a-self-hosted-integration-runtime"></a>Şirket içinde barındırılan tümleştirme çalışma zamanını paylaşma yöntemleri

Şirket içinde barındırılan tümleştirme çalışma zamanını birden çok veri fabrikası ile paylaşmak için, Ayrıntılar için [Bu yönergeye](create-shared-self-hosted-integration-runtime-powershell.md) bakın.

### <a name="monitoring"></a>İzleme

- **Paylaşılan IR**

  ![Paylaşılan tümleştirme çalışma zamanı bulma seçimleri](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

  ![İzleme sekmesi](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

- **Bağlı IR**

  ![Bağlantılı tümleştirme çalışma zamanı bulma seçimleri](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

  ![İzleme sekmesi](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)

### <a name="known-limitations-of-self-hosted-ir-sharing"></a>Şirket içinde barındırılan IR paylaşımının bilinen sınırlamaları

* Bağlı bir IR 'nin oluşturulacağı Veri Fabrikası bir [MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)içermelidir. Varsayılan olarak, Azure portal veya PowerShell cmdlet 'lerinde oluşturulan veri fabrikaları, örtük olarak oluşturulmuş bir MSI 'e sahiptir. Ancak, bir veri fabrikası Azure Resource Manager şablonu veya SDK aracılığıyla oluşturulduğunda, Azure Resource Manager, MSI içeren bir veri fabrikası oluşturduğundan emin olmak için **kimlik** özelliği açıkça ayarlanmalıdır. 

* Bu özelliği destekleyen Azure Data Factory .NET SDK sürümü 1.1.0 veya üzeri bir sürüm.

* İzin vermek için, kullanıcının sahip rolüne veya paylaşılan IR 'nin bulunduğu veri fabrikasında devralınan sahip rolüne ihtiyacı vardır.

* Paylaşma özelliği yalnızca aynı Azure Active Directory kiracısındaki veri fabrikaları için geçerlidir.

* Active Directory [Konuk kullanıcılar](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews)için, Kullanıcı arabirimindeki arama işlevleri (bir arama anahtar sözcüğü kullanılarak tüm veri fabrikalarını listeleme) [çalışmaz](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#SearchLimits). Ancak Konuk Kullanıcı veri fabrikasının sahibi olduğu sürece, IR 'nin **Izin ata** metin kutusunda paylaşılması gereken veri FABRIKASıNıN MSI 'sini doğrudan yazarak ve **Ekle** ' yi seçerek IR 'yi, arama işlevi olmadan paylaşabilir. Azure Data Factory Kullanıcı arabiriminde. 

  > [!NOTE]
  > Bu özellik yalnızca Azure Data Factory v2 sürümünde kullanılabilir. 

## <a name="notification-area-icons-and-notifications"></a>Bildirim alanı simgeleri ve bildirimleri

İmlecinizi bildirim alanındaki simgenin veya iletinin üzerine taşırsanız, şirket içinde barındırılan tümleştirme çalışma zamanının durumuyla ilgili ayrıntıları bulabilirsiniz.

![Bildirim alanındaki bildirimler](media/create-self-hosted-integration-runtime/system-tray-notifications.png)

## <a name="ports-and-firewall"></a>Bağlantı noktaları ve güvenlik duvarı

Dikkate alınması gereken iki güvenlik duvarı vardır: kuruluşun merkezi yönlendiricisinde çalışan *Kurumsal güvenlik duvarı ve şirket* içinde barındırılan tümleştirme çalışma zamanının yüklendiği yerel makinede bir daemon olarak yapılandırılmış *Windows Güvenlik Duvarı* .

![Güvenlik duvarı](media/create-self-hosted-integration-runtime/firewall.png)

*Şirket güvenlik duvarı* düzeyinde, aşağıdaki etki alanlarını ve giden bağlantı noktalarını yapılandırmanız gerekir:

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]


*Windows Güvenlik Duvarı* düzeyinde (makine düzeyinde), bu giden bağlantı noktaları normalde etkindir. Aksi takdirde, etki alanlarını ve bağlantı noktalarını şirket içinde barındırılan bir tümleştirme çalışma zamanı makinesine göre yapılandırabilirsiniz.

> [!NOTE]
> Kaynak ve havuzları temel alarak, kurumsal güvenlik duvarınızdaki veya Windows güvenlik duvarında ek etki alanlarına ve giden bağlantı noktalarına izin vermeniz gerekebilir.
>
> Bazı bulut veritabanları (örneğin, Azure SQL veritabanı ve Azure Data Lake) için, kendi güvenlik duvarı yapılandırmasındaki şirket içinde barındırılan tümleştirme çalışma zamanı makinelerinin IP adreslerine izin vermeniz gerekebilir.

### <a name="copy-data-from-a-source-to-a-sink"></a>Verileri bir kaynaktan havuza kopyalama

Güvenlik Duvarı kurallarının kurumsal güvenlik duvarında, şirket içinde barındırılan tümleştirme çalışma zamanı makinesindeki Windows güvenlik duvarında ve veri deposunun kendisi üzerinde düzgün şekilde etkinleştirildiğinden emin olun. Bu kuralların etkinleştirilmesi, şirket içinde barındırılan tümleştirme çalışma zamanının hem kaynağa hem de havuza başarıyla bağlanmasını sağlar. Kopyalama işleminde yer alan her bir veri deposu için kuralları etkinleştirin.

Örneğin, şirket içi bir veri deposundan bir Azure SQL veritabanı havuzuna veya bir Azure SQL veri ambarı havuzuna kopyalamak için aşağıdaki adımları uygulayın:

1. Windows Güvenlik Duvarı ve kurumsal güvenlik duvarı için 1433 numaralı bağlantı noktasında giden TCP iletişimine izin verin.
2. Şirket içinde barındırılan tümleştirme çalışma zamanı makinesinin IP adresini izin verilen IP adresleri listesine eklemek için Azure SQL veritabanı 'nın güvenlik duvarı ayarlarını yapılandırın.

> [!NOTE]
> Güvenlik duvarınız giden bağlantı noktası 1433 ' ye izin vermediğinden, şirket içinde barındırılan tümleştirme çalışma zamanı Azure SQL veritabanına doğrudan erişemez. Bu durumda, [hazırlanan bir kopyayı](copy-activity-performance.md) Azure SQL veritabanı ve Azure SQL veri ambarı 'na kullanabilirsiniz. Bu senaryoda, veri taşıma için yalnızca HTTPS (bağlantı noktası 443) gerekir.

## <a name="proxy-server-considerations"></a>Proxy sunucusu konuları

Şirket ağı ortamınız Internet 'e erişmek için bir proxy sunucusu kullanıyorsa, kendi kendine barındırılan tümleştirme çalışma zamanını uygun proxy ayarlarını kullanacak şekilde yapılandırın. İlk kayıt aşamasında ara sunucu ayarlayabilirsiniz.

![Proxy belirtin](media/create-self-hosted-integration-runtime/specify-proxy.png)

Yapılandırıldığında, şirket içinde barındırılan tümleştirme çalışma zamanı, bulut hizmetine, kaynak/hedefe (HTTP/HTTPS protokolü kullanılarak) bağlanmak için proxy sunucusunu kullanır. Bu, ilk kurulum sırasında **Bağlantıyı Değiştir** ' i seçin. Proxy ayarı iletişim kutusunu görürsünüz.

![Ara sunucuyu ayarla](media/create-self-hosted-integration-runtime/set-http-proxy.png)

Üç yapılandırma seçeneği vardır:

- **Proxy kullanma**: şirket içinde barındırılan tümleştirme çalışma zamanı, bulut hizmetlerine bağlanmak için hiçbir proxy 'yi açıkça kullanmaz.
- **Sistem proxy 'Si kullan**: şirket içinde barındırılan tümleştirme çalışma zamanı diahost. exe. config ve diawp. exe. config içinde yapılandırılan proxy ayarını kullanır. Diahost. exe. config ve diawp. exe. config içinde yapılandırılmış bir proxy yoksa, şirket içinde barındırılan tümleştirme çalışma zamanı bir proxy üzerinden doğrudan bulut hizmetine bağlanır.
- **Özel proxy kullan**: diahost. exe. config ve diawp. exe. config içinde yapılandırma kullanmak yerine, şirket içinde barındırılan tümleştirme çalışma zamanı IÇIN kullanılacak http proxy ayarını yapılandırın. **Adres** ve **bağlantı noktası** gereklidir. **Kullanıcı adı** ve **parola** , proxy 'nizin kimlik doğrulama ayarına bağlı olarak isteğe bağlıdır. Tüm ayarlar, şirket içinde barındırılan tümleştirme çalışma zamanı üzerinde Windows DPAPI ile şifrelenir ve makinede yerel olarak depolanır.

Tümleştirme çalışma zamanı konak hizmeti, güncelleştirilmiş proxy ayarlarını kaydettikten sonra otomatik olarak yeniden başlatılır.

Şirket içinde barındırılan tümleştirme çalışma zamanı başarıyla kaydedildikten sonra, proxy ayarlarını görüntülemek veya güncelleştirmek istiyorsanız Integration Runtime Configuration Manager kullanın.

1. **Microsoft Integration Runtime Configuration Manager**açın.
2. **Ayarlar** sekmesine geçin.
3. Http proxy 'yi **Ayarla** iletişim kutusunu açmak Için **http proxy** bölümündeki **değişiklik** bağlantısını seçin.
4. **İleri**’yi seçin. Ardından, proxy ayarını kaydetme izninizin olduğunu soran bir uyarı görürsünüz ve Integration Runtime ana bilgisayar hizmetini yeniden başlatın.

HTTP proxy 'sini Configuration Manager aracını kullanarak görüntüleyebilir ve güncelleştirebilirsiniz.

![Ara sunucuyu görüntüle](media/create-self-hosted-integration-runtime/view-proxy.png)

> [!NOTE]
> NTLM kimlik doğrulaması ile bir ara sunucu ayarlarsanız, tümleştirme çalışma zamanı konak hizmeti etki alanı hesabı altında çalışır. Etki alanı hesabının parolasını daha sonra değiştirirseniz, hizmetin yapılandırma ayarlarını güncelleştirmeyi ve uygun şekilde yeniden başlatmayı unutmayın. Bu gereksinim nedeniyle, parolayı sık sık güncelleştirmenizi gerektirmeyen ara sunucuya erişmek için adanmış bir etki alanı hesabı kullanmanızı öneririz.

### <a name="configure-proxy-server-settings"></a>Proxy sunucusu ayarlarını yapılandırma

HTTP proxy için **sistem proxy 'Si kullan** ayarını seçerseniz, şirket içinde barındırılan tümleştirme çalışma zamanı diahost. exe. config ve diawp. exe. config dosyasındaki proxy ayarını kullanır. Diahost. exe. config ve diawp. exe. config dosyasında hiçbir proxy belirtilmemişse, şirket içinde barındırılan tümleştirme çalışma zamanı, doğrudan proxy 'ye geçmeden bulut hizmetine bağlanır. Aşağıdaki yordam diahost. exe. config dosyasını güncelleştirmek için yönergeler sağlar:

1. Dosya Gezgini 'nde, özgün dosyayı yedeklemek için C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config ' ın güvenli bir kopyasını oluşturun.
2. Yönetici olarak çalışan Notepad. exe ' yi açın ve C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config. metin dosyasını açın. Aşağıdaki kodda gösterildiği gibi, system.net için varsayılan etiketi bulun:

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    Daha sonra aşağıdaki örnekte gösterildiği gibi proxy sunucu ayrıntılarını ekleyebilirsiniz:

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Proxy etiketi içinde, `scriptLocation`gibi gerekli ayarları belirtmek için ek özelliklere izin verilir. Sözdizimi için bkz. [proxy öğesi (ağ ayarları)](https://msdn.microsoft.com/library/sa91de1e.aspx) .

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Yapılandırma dosyasını özgün konumuna kaydedin. Ardından, değişiklikleri sağlayan şirket içinde barındırılan tümleştirme çalışma zamanı ana bilgisayarı hizmetini yeniden başlatın. 

   Hizmeti yeniden başlatmak için Denetim Masası 'ndaki hizmetler uygulamasını kullanın. Ya da Integration Runtime Configuration Manager, **hizmeti Durdur** düğmesini seçin ve ardından **Hizmeti Başlat**' ı seçin. 
   
   Hizmet başlamazsa, düzenlenmiş uygulama yapılandırma dosyasında yanlış bir XML etiketi söz dizimi eklenmiş olabilir.

> [!IMPORTANT]
> Hem diahost. exe. config hem de diawp. exe. config ' i güncelleştirmeyi unutmayın.

Ayrıca, Microsoft Azure şirketinizin izin verilenler listesinde olduğundan emin olmanız gerekir. Geçerli Microsoft Azure IP adreslerinin listesini [Microsoft Indirme merkezi](https://www.microsoft.com/download/details.aspx?id=41653)' nden indirebilirsiniz.

### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Güvenlik Duvarı ve ara sunucu ile ilgili sorunlar için olası belirtiler

Aşağıdakine benzer hatalarla karşılaşırsanız, büyük olasılıkla güvenlik duvarının veya ara sunucunun yanlış yapılandırılması nedeniyle, şirket içinde barındırılan tümleştirme çalışma zamanının kendi kimliğini doğrulamak için Data Factory 'e bağlanmasını engeller. Güvenlik duvarınızın ve ara sunucunuzun düzgün yapılandırıldığından emin olmak için önceki bölüme bakın.

* Şirket içinde barındırılan tümleştirme çalışma zamanını kaydettirmeye çalıştığınızda şu hatayı alırsınız: "Bu Integration Runtime düğümü kaydedilemedi! Kimlik doğrulama anahtarının geçerli olduğunu ve tümleştirme hizmeti ana bilgisayar hizmetinin bu makinede çalıştığını doğrulayın. "
* Integration Runtime Configuration Manager açtığınızda, **bağlantısı kesilen** veya **bağlanan**bir durum görürsünüz. Windows olay günlüklerini görüntülerken, **uygulama ve hizmet günlükleri** > **Microsoft Integration Runtime** > **Olay Görüntüleyicisi** altında, aşağıdakine benzer bir hata iletisi görürsünüz:

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enabling-remote-access-from-an-intranet"></a>İntranetten uzaktan erişimi etkinleştirme 

PowerShell kullanarak, şirket içinde barındırılan tümleştirme çalışma zamanının yüklendiği dışında başka bir makineden (ağdaki) kimlik bilgilerini şifreleyebilirsiniz, **Intranet seçeneğinden uzaktan erişimi** etkinleştirebilirsiniz. Şirket içinde barındırılan tümleştirme çalışma zamanının yüklendiği makinede kimlik bilgilerini şifrelemek için PowerShell 'i çalıştırırsanız, **Intranetten uzaktan erişimi**etkinleştiremezsiniz.

Yüksek kullanılabilirlik ve ölçeklenebilirlik için başka bir düğüm eklemeden önce **Intranetten uzaktan erişimi** etkinleştirmeniz gerekir.  

Şirket içinde barındırılan tümleştirme çalışma zamanı kurulumu (sürüm 3.3. xxxx. x sonrası) sırasında, şirket içinde barındırılan tümleştirme çalışma zamanı yüklemesi, şirket içinde barındırılan tümleştirme çalışma zamanı makinesindeki **Intranetten uzaktan erişimi** devre dışı bırakır.

Üçüncü taraf bir güvenlik duvarı kullanıyorsanız, 8060 numaralı bağlantı noktasını (veya Kullanıcı tarafından yapılandırılmış bağlantı noktasını) el ile açabilirsiniz. Şirket içinde barındırılan tümleştirme çalışma zamanını ayarlarken bir güvenlik duvarı sorununuz varsa, güvenlik duvarını yapılandırmadan şirket içinde barındırılan tümleştirme çalışma zamanını yüklemek için aşağıdaki komutu kullanmayı deneyin:

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```

Şirket içinde barındırılan tümleştirme çalışma zamanı makinesinde 8060 numaralı bağlantı noktasını açmayı seçerseniz, veri deposu kimlik bilgilerini yapılandırmak için kimlik bilgileri uygulaması ayarı dışındaki mekanizmaları kullanın. Örneğin, **New-AzDataFactoryV2LinkedServiceEncryptCredential** PowerShell cmdlet 'ini kullanabilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar
Adım adım yönergeler için aşağıdaki öğreticiye bakın: [öğretici: şirket içi verileri buluta kopyalama](tutorial-hybrid-copy-powershell.md).
