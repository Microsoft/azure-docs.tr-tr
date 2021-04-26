---
title: Şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma
description: Azure Data Factory ' de şirket içinde barındırılan bir tümleştirme çalışma zamanı oluşturmayı öğrenin. Bu, veri fabrikalarının özel bir ağdaki veri depolarına erişmesini sağlar.
ms.service: data-factory
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
ms.date: 02/10/2021
ms.openlocfilehash: 3e61b6a0f17d2d21aaaebc5ff42b0221cf851a4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100389527"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>Şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma ve yapılandırma

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Integration Runtime (IR), farklı ağ ortamlarında veri tümleştirme özellikleri sağlamak için Azure Data Factory tarafından kullanılan işlem altyapısıdır. IR ile ilgili ayrıntılar için bkz. [tümleştirme çalışma zamanına genel bakış](concepts-integration-runtime.md).

Şirket içinde barındırılan tümleştirme çalışma zamanı, bir bulut veri deposu ile özel ağda bulunan bir veri deposu arasında kopyalama etkinlikleri çalıştırabilir. Şirket içi ağına veya Azure sanal ağındaki işlem kaynaklarına dönüştürme etkinlikleri de gönderebilir. Şirket içinde barındırılan tümleştirme çalışma zamanının yüklenmesi için şirket içindeki bir makine veya özel ağ içindeki bir sanal makine gerekir.  

Bu makalede, kendinden konak IR oluşturma ve yapılandırma açıklanmaktadır.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="considerations-for-using-a-self-hosted-ir"></a>Şirket içinde barındırılan IR kullanma konuları

- Birden çok şirket içi veri kaynağı için şirket içinde barındırılan tek bir tümleştirme çalışma zamanı kullanabilirsiniz. Aynı zamanda aynı Azure Active Directory (Azure AD) kiracısında bulunan başka bir veri fabrikasıyla de paylaşabilirsiniz. Daha fazla bilgi için bkz. [Şirket içinde barındırılan tümleştirme çalışma zamanını paylaşma](./create-shared-self-hosted-integration-runtime-powershell.md).
- Şirket içinde barındırılan tümleştirme çalışma zamanının yalnızca bir örneğini tek bir makineye yükleyebilirsiniz. Şirket içi veri kaynaklarına erişmesi gereken iki veri fabrikası varsa, şirket içinde barındırılan IR paylaşmak için şirket içinde barındırılan IR [paylaşma özelliğini](./create-shared-self-hosted-integration-runtime-powershell.md) kullanın veya her bir veri fabrikası için bir tane olmak üzere iki şirket içi bilgisayara şirket IÇINDE barındırılan IR 'yi yükleyebilirsiniz.  
- Şirket içinde barındırılan tümleştirme çalışma zamanının veri kaynağıyla aynı makinede olması gerekmez. Ancak, şirket içinde barındırılan tümleştirme çalışma zamanının veri kaynağına yakın olması, şirket içinde barındırılan tümleştirme çalışma zamanının veri kaynağına bağlanması için geçen süreyi azaltır. Şirket içinde barındırılan tümleştirme çalışma zamanını, şirket içi veri kaynağını barındıran bilgisayardan farklı bir makineye yüklemenizi öneririz. Şirket içinde barındırılan tümleştirme çalışma zamanı ve veri kaynağı farklı makinelerinizde, şirket içinde barındırılan tümleştirme çalışma zamanı, kaynaklar için veri kaynağıyla rekabet etmez.
- Aynı şirket içi veri kaynağına bağlanan farklı makinelerde şirket içinde barındırılan birden çok tümleştirme çalışma zamanı olabilir. Örneğin, iki veri fabrikası sunan iki şirket içinde barındırılan tümleştirme çalışma zamanı varsa, aynı şirket içi veri kaynağı her iki veri fabrikasıyla de kaydedilebilir.
- Bir Azure sanal ağı içindeki veri tümleştirmesini desteklemek için şirket içinde barındırılan bir tümleştirme çalışma zamanı kullanın.
- Azure ExpressRoute kullandığınızda bile, veri kaynağınızı bir güvenlik duvarının arkasındaki şirket içi veri kaynağı olarak değerlendirin. Hizmeti veri kaynağına bağlamak için şirket içinde barındırılan tümleştirme çalışma zamanını kullanın.
- Veri deposu bir Azure hizmet olarak altyapı (IaaS) sanal makinesi üzerinde bulutta olsa bile, şirket içinde barındırılan tümleştirme çalışma zamanını kullanın.
- Görevler, FIPS uyumlu şifreleme etkin olan bir Windows Server 'a yüklediğiniz şirket içinde barındırılan tümleştirme çalışma zamanında başarısız olabilir. Bu sorunu geçici olarak çözmek için iki seçeneğiniz vardır: kimlik bilgilerini/gizli değerleri bir Azure Key Vault depolayın veya sunucuda FIPS uyumlu şifrelemeyi devre dışı bırakın. FIPS uyumlu şifrelemeyi devre dışı bırakmak için, aşağıdaki kayıt defteri alt anahtarının değerini 1 (etkin) iken 0 (devre dışı) olarak değiştirin: `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled` . Kendi şirket içinde [barındırılan tümleştirme çalışma ZAMANıNı SSIS tümleştirme çalışma zamanı için bir proxy olarak](./self-hosted-integration-runtime-proxy-ssis.md)kullanıyorsanız, FIPS uyumlu şifreleme etkinleştirilebilir ve verileri şirket Içinden Azure Blob depolamaya hazırlama alanı olarak taşırken kullanılacaktır.

## <a name="command-flow-and-data-flow"></a>Komut akışı ve veri akışı

Şirket içi ve bulut arasında veri taşıdığınızda, etkinlik şirket içi veri kaynağı ve bulut arasında veri aktarmak için şirket içinde barındırılan bir tümleştirme çalışma zamanı kullanır.

Şirket içinde barındırılan bir IR ile kopyalamaya yönelik veri akışı adımlarının üst düzey bir özeti aşağıda verilmiştir:

![Veri akışına üst düzey genel bakış](media/create-self-hosted-integration-runtime/high-level-overview.png)

1. Veri geliştiricisi, Azure portal veya PowerShell cmdlet 'ini kullanarak bir Azure Data Factory içinde şirket içinde barındırılan bir tümleştirme çalışma zamanı oluşturur.

2. Veri geliştiricisi, şirket içi veri deposu için bağlı bir hizmet oluşturur. Geliştirici bunu, hizmetin veri depolarına bağlanmak için kullanması gereken şirket içinde barındırılan tümleştirme çalışma zamanı örneğini belirterek yapar.

3. Şirket içinde barındırılan tümleştirme çalışma zamanı düğümü, Windows Data Protection uygulama programlama arabirimini (DPAPI) kullanarak kimlik bilgilerini şifreler ve kimlik bilgilerini yerel olarak kaydeder. Yüksek kullanılabilirlik için birden çok düğüm ayarlandıysa, kimlik bilgileri diğer düğümlerde daha fazla eşitlenir. Her düğüm, DPAPI kullanarak kimlik bilgilerini şifreler ve yerel olarak depolar. Kimlik bilgisi eşitleme, veri geliştiricisi tarafından saydamdır ve şirket içinde barındırılan IR tarafından işlenir.

4. Azure Data Factory işleri zamanlamak ve yönetmek için şirket içinde barındırılan tümleştirme çalışma zamanı ile iletişim kurar. İletişim, paylaşılan bir [Azure Relay](../azure-relay/relay-what-is-it.md#wcf-relay) bağlantısı kullanan bir denetim kanalı üzerinden yapılır. Bir etkinlik işinin çalıştırılması gerektiğinde, isteği herhangi bir kimlik bilgisi bilgisiyle birlikte Data Factory kuyruğa alır. Bu durum, kimlik bilgilerinin şirket içinde barındırılan tümleştirme çalışma zamanı 'nda zaten depolanmaması durumunda olur. Şirket içinde barındırılan tümleştirme çalışma zamanı, kuyruğu yokladıktan sonra işi başlatır.

5. Şirket içinde barındırılan tümleştirme çalışma zamanı, verileri şirket içi bir mağaza ve bulut depolama arasında kopyalar. Kopyanın yönü, kopyalama etkinliğinin veri ardışık düzeninde nasıl yapılandırıldığına bağlıdır. Bu adım için, şirket içinde barındırılan tümleştirme çalışma zamanı, güvenli bir HTTPS kanalı üzerinden Azure Blob depolama gibi bulut tabanlı depolama hizmetleriyle doğrudan iletişim kurar.

## <a name="prerequisites"></a>Önkoşullar

- Desteklenen Windows sürümleri şunlardır:
  - Windows 8.1
  - Windows 10
  - Windows Server 2012
  - Windows Server 2012 R2
  - Windows Server 2016
  - Windows Server 2019

Şirket içinde barındırılan tümleştirme çalışma zamanının bir etki alanı denetleyicisine yüklenmesi desteklenmez.

- Şirket içinde barındırılan tümleştirme çalışma zamanı, .NET Framework 4.7.2 veya üzeri bir 64 bitlik bir Işletim sistemi gerektirir. Ayrıntılar için [.NET Framework sistem gereksinimleri](/dotnet/framework/get-started/system-requirements) ' ne bakın.
- Şirket içinde barındırılan tümleştirme çalışma zamanı makinesi için önerilen en düşük yapılandırma, 4 çekirdek, 8 GB RAM ve 80 GB kullanılabilir sabit disk alanı olan 2 GHz bir işlemcisidir. Sistem gereksinimlerinin ayrıntıları için bkz. [indirme](https://www.microsoft.com/download/details.aspx?id=39717).
- Ana makine hazırda beklemesi durumunda, şirket içinde barındırılan tümleştirme çalışma zamanı veri isteklerine yanıt vermez. Şirket içinde barındırılan tümleştirme çalışma zamanını yüklemeden önce bilgisayarda uygun bir güç planı yapılandırın. Makine hazırda beklemeye yapılandırıldıysa, şirket içinde barındırılan tümleştirme çalışma zamanı yükleyicisi bir iletiyle uyarır.
- Şirket içinde barındırılan tümleştirme çalışma zamanını başarılı bir şekilde yüklemek ve yapılandırmak için makinede yönetici olmanız gerekir.
- Kopyalama-etkinlik çalıştırmaları belirli bir sıklıkta gerçekleşir. Makinedeki işlemci ve RAM kullanımı, yoğun ve boşta zamanlarla aynı düzene uyar. Kaynak kullanımı Ayrıca, taşınan veri miktarına büyük ölçüde bağlıdır. Birden çok kopyalama işi devam ederken, yoğun saatlerde kaynak kullanımını görürsünüz.
- , Parquet, ORC veya avro biçimlerinde veri ayıklama sırasında görevler başarısız olabilir. Parquet hakkında daha fazla bilgi için [Azure Data Factory Içindeki Parquet biçimine](./format-parquet.md#using-self-hosted-integration-runtime)bakın. Dosya oluşturma, şirket içinde barındırılan tümleştirme makinesinde çalışır. Dosya oluşturma, beklendiği gibi çalışması için aşağıdaki önkoşulları gerektirir:
  - [Visual C++ 2010 yeniden dağıtılabilir](https://download.microsoft.com/download/3/2/2/3224B87F-CFA0-4E70-BDA3-3DE650EFEBA5/vcredist_x64.exe) Paket (x64)
  - Java Runtime (JRE) sürüm 8, bir JRE sağlayıcısından, [OpenJDK 'Yi benimseyin](https://adoptopenjdk.net/). `JAVA_HOME`Ortam değişkeninin, JRE klasörüne (yalnızca JDK klasörü değil) ayarlandığından emin olun.

## <a name="setting-up-a-self-hosted-integration-runtime"></a>Şirket içinde barındırılan tümleştirme çalışma zamanı ayarlama

Şirket içinde barındırılan bir tümleştirme çalışma zamanı oluşturmak ve ayarlamak için aşağıdaki yordamları kullanın.

### <a name="create-a-self-hosted-ir-via-azure-powershell"></a>Azure PowerShell aracılığıyla kendinden konak IR oluşturma

1. Bu görev için Azure PowerShell kullanabilirsiniz. Aşağıda bir örnek verilmiştir:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. Şirket içinde barındırılan tümleştirme çalışma zamanını yerel bir makineye [indirip](https://www.microsoft.com/download/details.aspx?id=39717) yükleyin.

3. Kimlik doğrulama anahtarını alın ve şirket içinde barındırılan tümleştirme çalışma zamanını anahtarla kaydettirin. Bir PowerShell örneği aşağıda verilmiştir:

    ```powershell

    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName  

    ```

### <a name="create-a-self-hosted-ir-via-azure-data-factory-ui"></a>Azure Data Factory Kullanıcı arabirimi aracılığıyla kendinden konak IR oluşturma

Azure Data Factory Kullanıcı arabirimini kullanarak şirket içinde barındırılan bir IR oluşturmak için aşağıdaki adımları kullanın.

1. Azure Data Factory Kullanıcı arabiriminin **Başlarken** sayfasında, en soldaki bölmeden [Yönet sekmesini](./author-management-hub.md) seçin.

   ![Giriş sayfası Yönet düğmesi](media/doc-common-process/get-started-page-manage-button.png)

1. Sol bölmedeki **tümleştirme çalışma zamanları** ' nı seçin ve ardından **+ Yeni**' yi seçin.

   ![Tümleştirme çalışma zamanı oluşturma](media/doc-common-process/manage-new-integration-runtime.png)

1. **Tümleştirme çalışma zamanı kurulumu** sayfasında, **Azure, şirket içinde barındırılan**' ı seçin ve ardından **devam**' ı seçin.

1. Aşağıdaki sayfada, bir Self-Hosted IR oluşturmak için şirket içinde **barındırılan** ' i seçin ve ardından **devam**' ı seçin.
   ![Kendini barındıran IR oluşturma](media/create-self-hosted-integration-runtime/new-selfhosted-integration-runtime.png)

1. IR 'niz için bir ad girin ve **Oluştur**' u seçin.

1. **Tümleştirme çalışma zamanı kurulumu** sayfasında, **1. seçenek** ' in altındaki bağlantıyı seçerek hızlı kurulumu bilgisayarınızda açın. Veya el ile ayarlamak için **2. seçenek** bölümündeki adımları izleyin. Aşağıdaki yönergeler el ile kuruluma dayalıdır:

   ![Tümleştirme çalışma zamanı kurulumu](media/create-self-hosted-integration-runtime/integration-runtime-setting-up.png)

    1. Kimlik doğrulama anahtarını kopyalayıp yapıştırın. **Tümleştirme çalışma zamanını indir ve Yükle ' yi** seçin.

    1. Şirket içinde barındırılan tümleştirme çalışma zamanını yerel Windows makinesine indirin. Yükleyiciyi çalıştırın.

    1. **Kayıt Integration Runtime (Şirket içinde barındırılan)** sayfasında, daha önce kaydettiğiniz anahtarı yapıştırın ve **Kaydet**' i seçin.

       ![Tümleştirme çalışma zamanını kaydetme](media/create-self-hosted-integration-runtime/register-integration-runtime.png)

    1. **Yeni Integration Runtime (Şirket içinde barındırılan) düğümü** sayfasında **son**' u seçin.

1. Şirket içinde barındırılan tümleştirme çalışma zamanı başarıyla kaydedildikten sonra aşağıdaki pencereyi görürsünüz:

    ![Başarılı kayıt](media/create-self-hosted-integration-runtime/registered-successfully.png)

### <a name="set-up-a-self-hosted-ir-on-an-azure-vm-via-an-azure-resource-manager-template"></a>Azure Resource Manager şablonu aracılığıyla bir Azure VM üzerinde şirket içinde barındırılan bir IR ayarlama

Şirket içinde barındırılan IR kurulumunu, bir Azure sanal makinesinde [kendi kendine konak IR şablonu oluştur şablonunu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime)kullanarak otomatik hale getirebilirsiniz. Şablon, bir Azure sanal ağı içinde tamamen işlevsel şirket içinde barındırılan bir IR 'nin kolayca bir yolunu sağlar. IR, düğüm sayısını 2 veya daha yüksek olarak ayarladığınız sürece yüksek kullanılabilirlik ve ölçeklenebilirlik özelliklerine sahiptir.

### <a name="set-up-an-existing-self-hosted-ir-via-local-powershell"></a>Yerel PowerShell aracılığıyla mevcut bir şirket içinde barındırılan IR ayarlama

Şirket içinde barındırılan mevcut bir IR kurmak veya yönetmek için komut satırını kullanabilirsiniz. Bu kullanım özellikle, şirket içinde barındırılan IR düğümlerinin yüklenmesinin ve kaydının otomatikleştirilmesine yardımcı olabilir.

Dmgcmd.exe, şirket içinde barındırılan yükleyicide bulunur. Genellikle C:\Program Files\Microsoft Integration Runtime\4.0\Shared\ klasöründe bulunur. Bu uygulama, çeşitli parametreleri destekler ve otomasyon için Batch betikleri kullanılarak bir komut satırı aracılığıyla çağrılabilir.

Uygulamayı aşağıdaki gibi kullanın:

```powershell
dmgcmd ACTION args...
```

Uygulamanın eylemlerinin ve bağımsız değişkenlerinin ayrıntıları şunlardır: 

|ÖN|args|Description|
|------|----|-----------|
|`-rn`,<br/>`-RegisterNewNode`|"`<AuthenticationKey>`" ["`<NodeName>`"]|Şirket içinde barındırılan tümleştirme çalışma zamanı düğümünü belirtilen kimlik doğrulama anahtarı ve düğüm adıyla kaydedin.|
|`-era`,<br/>`-EnableRemoteAccess`|"`<port>`" ["`<thumbprint>`"]|Yüksek kullanılabilirlik kümesi ayarlamak için geçerli düğümde uzaktan erişimi etkinleştirin. Ya da Azure Data Factory aracılığıyla doğrudan şirket içinde barındırılan IR 'ye karşı kimlik bilgileri ayarlamayı etkinleştirin. İkinci olarak, aynı ağdaki bir uzak makineden **New-AzDataFactoryV2LinkedServiceEncryptedCredential** cmdlet 'ini kullanarak bunu yapabilirsiniz.|
|`-erac`,<br/>`-EnableRemoteAccessInContainer`|"`<port>`" ["`<thumbprint>`"]|Düğüm bir kapsayıcıda çalıştırıldığında geçerli düğüme uzaktan erişimi etkinleştirin.|
|`-dra`,<br/>`-DisableRemoteAccess`||Geçerli düğüme uzaktan erişimi devre dışı bırakın. Çok düğümlü kurulum için uzaktan erişim gerekir. **Yeni-AzDataFactoryV2LinkedServiceEncryptedCredential** PowerShell cmdlet 'i, uzaktan erişim devre dışı bırakıldığında bile hala çalışıyor. Bu davranış, cmdlet 'in şirket içinde barındırılan IR düğümüyle aynı makinede yürütüldüğü sürece doğrudur.|
|`-k`,<br/>`-Key`|"`<AuthenticationKey>`"|Önceki kimlik doğrulama anahtarının üzerine yazın veya güncelleştirin. Bu eyleme dikkat edin. Anahtar yeni bir tümleştirme çalışma zamanı ise, önceki şirket içinde barındırılan IR düğümünüz çevrimdışı duruma geçebilir.|
|`-gbf`,<br/>`-GenerateBackupFile`|"`<filePath>`" "`<password>`"|Geçerli düğüm için bir yedek dosya oluşturun. Yedekleme dosyası, düğüm anahtarını ve veri deposu kimlik bilgilerini içerir.|
|`-ibf`,<br/>`-ImportBackupFile`|"`<filePath>`" "`<password>`"|Düğümü bir yedekleme dosyasından geri yükleyin.|
|`-r`,<br/>`-Restart`||Şirket içinde barındırılan tümleştirme çalışma zamanı ana bilgisayarı hizmetini yeniden başlatın.|
|`-s`,<br/>`-Start`||Şirket içinde barındırılan tümleştirme çalışma zamanı ana bilgisayarı hizmetini başlatın.|
|`-t`,<br/>`-Stop`||Şirket içinde barındırılan tümleştirme çalışma zamanı ana bilgisayarı hizmetini durdurun.|
|`-sus`,<br/>`-StartUpgradeService`||Şirket içinde barındırılan tümleştirme çalışma zamanı yükseltme hizmetini başlatın.|
|`-tus`,<br/>`-StopUpgradeService`||Şirket içinde barındırılan tümleştirme çalışma zamanı yükseltme hizmetini durdurun.|
|`-tonau`,<br/>`-TurnOnAutoUpdate`||Şirket içinde barındırılan tümleştirme çalışma zamanı otomatik güncelleştirmesini açın.|
|`-toffau`,<br/>`-TurnOffAutoUpdate`||Şirket içinde barındırılan tümleştirme çalışma zamanı otomatik güncelleştirmesini devre dışı bırakın.|
|`-ssa`,<br/>`-SwitchServiceAccount`|"`<domain\user>`" ["`<password>`"]|DIAHostService ' i yeni bir hesap olarak çalışacak şekilde ayarlayın. Sistem hesapları ve sanal hesaplar için "" boş parolasını kullanın.|

## <a name="install-and-register-a-self-hosted-ir-from-microsoft-download-center"></a>Microsoft Indirme merkezi 'nden şirket içinde barındırılan bir IR yükleme ve kaydetme

1. [Microsoft tümleştirme çalışma zamanı indirme sayfasına](https://www.microsoft.com/download/details.aspx?id=39717)gidin.
2. **İndir**' i seçin, 64 bit sürümünü seçin ve **İleri**' yi seçin. 32 bit sürümü desteklenmiyor.
3. Yönetilen kimlik dosyasını doğrudan çalıştırın veya sabit sürücünüze kaydedin ve çalıştırın.
4. **Hoş geldiniz** penceresinde bir dil seçin ve **İleri**' yi seçin.
5. Microsoft yazılımı lisans koşulları 'nı kabul edin ve **İleri ' yi** seçin.
6. Şirket içinde barındırılan tümleştirme çalışma zamanını yüklemek için **klasör** ' ü seçin ve **İleri**' yi seçin.
7. **Yüklemeye hazırlanma** sayfasında, **yükler**' i seçin.
8. Yüklemeyi tamamlamaya **son** ' u seçin.
9. PowerShell kullanarak kimlik doğrulama anahtarını alın. Kimlik doğrulama anahtarını almaya yönelik bir PowerShell örneği aşağıda verilmiştir:

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```

10. Makinenizde çalışan Microsoft Integration Runtime Configuration Manager **Integration Runtime (Şirket içinde barındırılan) Kaydet** penceresinde aşağıdaki adımları uygulayın:

    1. Kimlik doğrulama anahtarını metin alanına yapıştırın.

    2. İsteğe bağlı olarak, anahtar metnini görmek için **kimlik doğrulama anahtarını göster** ' i seçin.

    3. **Kaydet**’i seçin.

## <a name="service-account-for-self-hosted-integration-runtime"></a>Şirket içinde barındırılan tümleştirme çalışma zamanı için hizmet hesabı

Şirket içinde barındırılan tümleştirme çalışma zamanının varsayılan oturum açma hizmeti hesabı **NT SERVICE\DIAHostService** şeklindedir. Bunu, **Hizmetler-> Integration Runtime hizmeti-> Özellikler ' de > oturum aç**' da görebilirsiniz.

![Şirket içinde barındırılan tümleştirme çalışma zamanı için hizmet hesabı](media/create-self-hosted-integration-runtime/shir-service-account.png)

Hesabın hizmet olarak oturum açma izni olduğundan emin olun. Aksi halde şirket içinde barındırılan tümleştirme çalışma zamanı başarıyla başlayamaz. **Yerel güvenlik ilkesi-> güvenlik ayarları-> yerel ilkeler-> Kullanıcı hakları ataması-> hizmet olarak oturum aç** ' daki izinleri kontrol edebilirsiniz

![Yerel Güvenlik Ilkesi-Kullanıcı hakları atamasının ekran görüntüsü](media/create-self-hosted-integration-runtime/shir-service-account-permission.png)

![Hizmet olarak oturum açma kullanıcı hakları ataması ekran görüntüsü](media/create-self-hosted-integration-runtime/shir-service-account-permission-2.png)

## <a name="notification-area-icons-and-notifications"></a>Bildirim alanı simgeleri ve bildirimleri

İmlecinizi bildirim alanındaki simgenin veya iletinin üzerine taşırsanız, şirket içinde barındırılan tümleştirme çalışma zamanının durumuyla ilgili ayrıntıları görebilirsiniz.

![Bildirim alanındaki bildirimler](media/create-self-hosted-integration-runtime/system-tray-notifications.png)

## <a name="high-availability-and-scalability"></a>Yüksek kullanılabilirlik ve ölçeklenebilirlik

Şirket içinde barındırılan bir tümleştirme çalışma zamanını Azure 'da birden çok şirket içi makine veya sanal makine ile ilişkilendirebilirsiniz. Bu makinelere düğüm denir. Şirket içinde barındırılan bir tümleştirme çalışma zamanı ile ilişkili en fazla dört düğümünüz olabilir. Mantıksal bir ağ geçidi için ağ geçidi yüklü olan şirket içi makinelerde birden çok düğüme sahip olmanın avantajları şunlardır:

- Şirket içinde barındırılan tümleştirme çalışma zamanının, büyük veri çözümünüzde veya Data Factory bulut veri tümleştirmesinde artık tek hata noktası olmaması için daha yüksek kullanılabilirlik. Bu kullanılabilirlik, en fazla dört düğüm kullandığınızda devamlılığını sağlamaya yardımcı olur.
- Şirket içi ve bulut veri depoları arasında veri taşıma sırasında geliştirilmiş performans ve verimlilik. [Performans karşılaştırmaları](copy-activity-performance.md)hakkında daha fazla bilgi alın.

Şirket içinde barındırılan tümleştirme çalışma zamanı yazılımını [Indirme merkezi](https://www.microsoft.com/download/details.aspx?id=39717)' nden yükleyerek birden çok düğümü ilişkilendirebilirsiniz. Ardından, [öğreticide](tutorial-hybrid-copy-powershell.md)açıklandığı gibi **New-AzDataFactoryV2IntegrationRuntimeKey** cmdlet 'inden alınan kimlik doğrulama anahtarlarından birini kullanarak kaydedin.

> [!NOTE]
> Her bir düğümü ilişkilendirmek için şirket içinde barındırılan yeni bir tümleştirme çalışma zamanı oluşturmanız gerekmez. Şirket içinde barındırılan tümleştirme çalışma zamanını başka bir makineye yükleyebilir ve aynı kimlik doğrulama anahtarını kullanarak kaydedebilirsiniz.

> [!NOTE]
> Yüksek kullanılabilirlik ve ölçeklenebilirlik için başka bir düğüm eklemeden önce, ilk düğümde **Intranete uzaktan erişim** seçeneğinin etkinleştirildiğinden emin olun. Bunu yapmak için **Microsoft Integration Runtime Configuration Manager**  >  **ayarları**  >  **intranete uzaktan erişim**' i seçin.

### <a name="scale-considerations"></a>Ölçek konuları

#### <a name="scale-out"></a>Ölçeği genişletme

İşlemci kullanımı yüksek olduğunda ve kullanılabilir bellek, şirket içinde barındırılan IR üzerinde düşükse, yük makineler arasında ölçeklendirmeye yardımcı olmak için yeni bir düğüm ekleyin. Etkinlikler, zaman aşımına uğradığından veya şirket içinde barındırılan IR düğümü çevrimdışı olduğu için başarısız olursa, ağ geçidine bir düğüm eklemenize yardımcı olur.

#### <a name="scale-up"></a>Ölçeği artırma

İşlemci ve kullanılabilir RAM iyi bir şekilde kullanıldığında, ancak eşzamanlı işlerin yürütülmesi düğümün sınırlarına ulaştığında, bir düğümün çalıştırabileceği eşzamanlı iş sayısını artırarak ölçeği artırır. Ayrıca, şirket içinde barındırılan IR aşırı yüklendiği için etkinliklerin zaman aşımına uğradığından ölçeğini ölçeklendirmek isteyebilirsiniz. Aşağıdaki görüntüde gösterildiği gibi, bir düğüm için maksimum kapasiteyi artırabilirsiniz:  

![Bir düğümde çalışabilecek eşzamanlı işlerin sayısını artırın](media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>TLS/SSL sertifikası gereksinimleri

Tümleştirme çalışma zamanı düğümleri arasındaki iletişimin güvenliğini sağlamak için kullandığınız TLS/SSL sertifikası için gereksinimler şunlardır:

- Sertifika, genel olarak güvenilen bir x509 v3 sertifikası olmalıdır. Ortak iş ortağı sertifika yetkilisi (CA) tarafından verilen sertifikaları kullanmanızı öneririz.
- Her Integration Runtime düğümü bu sertifikaya güvenmelidir.
- Yalnızca son SAN öğesi kullanıldığından, konu diğer adı (SAN) sertifikalarını önermiyoruz. Diğer tüm SAN öğeleri yok sayılır. Örneğin, San 'Lar **node1.domain.contoso.com** ve **node2.DOMAIN.contoso.com** olan bir San sertifikanız varsa, bu sertifikayı yalnızca tam etkı alanı adı (FQDN) **node2.domain.contoso.com** olan bir makinede kullanabilirsiniz.
- Sertifika, TLS/SSL sertifikaları için Windows Server 2012 R2 tarafından desteklenen herhangi bir anahtar boyutunu kullanabilir.
- CNG anahtarları kullanan sertifikalar desteklenmez.  

> [!NOTE]
> Bu sertifika kullanılır:
>
> - Şirket içinde barındırılan IR düğümündeki bağlantı noktalarını şifrelemek için.
> - Düğümler arasında bağlı hizmetlerin kimlik bilgileri eşitlemesini içeren durum eşitlemeye yönelik düğümden düğüme iletişim için.
> - Bir PowerShell cmdlet 'i, bir yerel ağ içinden bağlı hizmet kimlik bilgisi ayarları için kullanıldığında.
>
> Özel ağ ortamınız güvenli değilse veya özel ağınız içindeki düğümler arasındaki iletişimin güvenliğini sağlamak istiyorsanız bu sertifikayı kullanmanızı öneririz.
>
> Şirket içinde barındırılan bir IR 'den diğer veri depolarına geçişte veri taşıma işlemi, bu sertifikanın ayarlanmış olup olmamasına bakılmaksızın her zaman şifrelenmiş bir kanalda gerçekleşir.

## <a name="proxy-server-considerations"></a>Proxy sunucusu konuları

Şirket ağı ortamınız Internet 'e erişmek için bir proxy sunucusu kullanıyorsa, kendi kendine barındırılan tümleştirme çalışma zamanını uygun proxy ayarlarını kullanacak şekilde yapılandırın. İlk kayıt aşamasında ara sunucu ayarlayabilirsiniz.

![Proxy 'yi belirtin](media/create-self-hosted-integration-runtime/specify-proxy.png)

Yapılandırıldığında, şirket içinde barındırılan tümleştirme çalışma zamanı, bulut hizmetinin kaynağına ve hedefine (HTTP veya HTTPS protokolünü kullanan) bağlanmak için proxy sunucusunu kullanır. İlk kurulum sırasında **Bağlantıyı Değiştir** ' i seçmenizin nedeni budur.

![Proxy 'yi ayarlama](media/create-self-hosted-integration-runtime/set-http-proxy.png)

Üç yapılandırma seçeneği vardır:

- **Proxy kullanma**: şirket içinde barındırılan tümleştirme çalışma zamanı, bulut hizmetlerine bağlanmak için hiçbir proxy 'yi açık olarak kullanmaz.
- **Sistem proxy 'Si kullan**: şirket içinde barındırılan tümleştirme çalışma zamanı, diahost.exe.config ve diawp.exe.config yapılandırılan proxy ayarını kullanır. Bu dosyalar hiçbir proxy yapılandırması belirtmeyecekse, şirket içinde barındırılan tümleştirme çalışma zamanı, bir ara sunucuya geçmeden doğrudan bulut hizmetine bağlanır.
- **Özel proxy kullan**: diahost.exe.config ve diawp.exe.config yapılandırmaların kullanılması yerine, şirket içinde barındırılan tümleştirme çalışma zamanı IÇIN kullanılacak http proxy ayarını yapılandırın. **Adres** ve **bağlantı noktası** değerleri gereklidir. Proxy 'nin kimlik doğrulama ayarına bağlı olarak, **Kullanıcı adı** ve **parola** değerleri isteğe bağlıdır. Tüm ayarlar, şirket içinde barındırılan tümleştirme çalışma zamanı üzerinde Windows DPAPI ile şifrelenir ve makinede yerel olarak depolanır.

Tümleştirme çalışma zamanı konak hizmeti, güncelleştirilmiş proxy ayarlarını kaydettikten sonra otomatik olarak yeniden başlatılır.

Şirket içinde barındırılan tümleştirme çalışma zamanını kaydettikten sonra, proxy ayarlarını görüntülemek veya güncelleştirmek istiyorsanız Microsoft Integration Runtime Configuration Manager kullanın.

1. **Microsoft Integration Runtime Configuration Manager** açın.
1. **Ayarlar** sekmesini seçin.
1. **Http proxy** altında bağlantıyı **Değiştir** ' i seçerek **http proxy 'yi ayarla** iletişim kutusunu açın.
1. **İleri**’yi seçin. Ardından, proxy ayarını kaydetme izninizin olduğunu soran bir uyarı görürsünüz ve Integration Runtime ana bilgisayar hizmetini yeniden başlatın.

HTTP proxy 'sini görüntülemek ve güncelleştirmek için Configuration Manager aracını kullanabilirsiniz.

![Ara sunucuyu görüntüleme ve güncelleştirme](media/create-self-hosted-integration-runtime/view-proxy.png)

> [!NOTE]
> NTLM kimlik doğrulaması ile bir ara sunucu ayarlarsanız, tümleştirme çalışma zamanı konak hizmeti etki alanı hesabı altında çalışır. Daha sonra etki alanı hesabının parolasını değiştirirseniz, hizmetin yapılandırma ayarlarını güncelleştirmeyi ve hizmeti yeniden başlatmayı unutmayın. Bu gereksinim nedeniyle, parolayı sık sık güncelleştirmenizi gerektirmeyen ayrılmış bir etki alanı hesabı kullanarak ara sunucuya erişmenizi öneririz.

### <a name="configure-proxy-server-settings"></a>Proxy sunucusu ayarlarını yapılandırma

HTTP proxy için **sistem proxy 'Si kullan** seçeneğini belirlerseniz, şirket içinde barındırılan tümleştirme çalışma zamanı diahost.exe.config ve diawp.exe.config içindeki proxy ayarlarını kullanır. Bu dosyalar proxy belirtmeksizin, şirket içinde barındırılan tümleştirme çalışma zamanı, bir ara sunucuya geçmeden doğrudan bulut hizmetine bağlanır. Aşağıdaki yordam diahost.exe.config dosyasını güncelleştirmek için yönergeler sağlar:

1. Dosya Gezgini 'nde, özgün dosyanın bir yedeklemesi olarak C:\Program Files\Microsoft Integration Runtime\4.0\Shared\diahost.exe.config 'ın güvenli bir kopyasını oluşturun.
1. Yönetici olarak çalışan Not defteri 'Ni açın.
1. Not defteri 'nde C:\Program Files\Microsoft Integration Runtime\4.0\Shared\diahost.exe.config metin dosyasını açın.
1. Varsayılan **System.net** etiketini aşağıdaki kodda gösterildiği gibi bulun:

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

    Proxy etiketi, ek özelliklerin gibi gerekli ayarları belirlemesine izin verir `scriptLocation` . Sözdizimi için bkz. [ \<proxy\> öğesi (ağ ayarları)](/dotnet/framework/configure-apps/file-schema/network/proxy-element-network-settings) .

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```

1. Yapılandırma dosyasını özgün konumuna kaydedin. Ardından, değişiklikleri sağlayan şirket içinde barındırılan tümleştirme çalışma zamanı ana bilgisayarı hizmetini yeniden başlatın.

   Hizmeti yeniden başlatmak için Denetim Masası 'ndan hizmetler uygulamasını kullanın. Ya da Integration Runtime Configuration Manager, **hizmeti Durdur** düğmesini seçin ve ardından **Hizmeti Başlat**' ı seçin.

   Hizmet başlatılmazsa, büyük olasılıkla düzenlediğiniz uygulama yapılandırma dosyasında yanlış XML etiketi söz dizimi eklemiş olursunuz.

> [!IMPORTANT]
> Hem diahost.exe.config hem de diawp.exe.config güncelleştirmeyi unutmayın.

Ayrıca, Microsoft Azure şirketinizin izin verilenler listesinde olduğundan emin olmanız gerekir. Geçerli Azure IP adreslerinin listesini [Microsoft Indirme merkezi](https://www.microsoft.com/download/details.aspx?id=41653)' nden indirebilirsiniz.

### <a name="possible-symptoms-for-issues-related-to-the-firewall-and-proxy-server"></a>Güvenlik Duvarı ve ara sunucu ile ilgili sorunlar için olası belirtiler

Aşağıdakiler gibi hata iletileri görürseniz, olası neden güvenlik duvarının veya ara sunucunun yapılandırması hatalı olur. Bu tür yapılandırma, şirket içinde barındırılan tümleştirme çalışma zamanının kendi kimliğini doğrulamak için Data Factory bağlanmasına engel olur. Güvenlik duvarınızın ve ara sunucunuzun düzgün yapılandırıldığından emin olmak için önceki bölüme bakın.

- Şirket içinde barındırılan tümleştirme çalışma zamanını kaydettirmeye çalıştığınızda şu hata iletisini alırsınız: "Bu Integration Runtime düğümü kaydedilemedi! Kimlik doğrulama anahtarının geçerli olduğunu ve tümleştirme hizmeti ana bilgisayar hizmetinin bu makinede çalıştığını doğrulayın. "
- Integration Runtime Configuration Manager açtığınızda, **bağlantısı kesilen** veya **bağlanan** bir durum görürsünüz. Windows olay günlüklerini görüntülediğinizde,   >  **uygulama ve hizmet günlükleri**  >  **Microsoft Integration Runtime** Olay Görüntüleyicisi altında, şöyle bir hata mesajı görürsünüz:

  ```output
  Unable to connect to the remote server
  A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
  ```

### <a name="enable-remote-access-from-an-intranet"></a>İntranetten uzaktan erişimi etkinleştir

Şirket içinde barındırılan tümleştirme çalışma zamanını yüklediğiniz yerden başka bir ağa bağlı makineden kimlik bilgilerini şifrelemek için PowerShell kullanıyorsanız, **Intranet seçeneğinden uzaktan erişimi** etkinleştirebilirsiniz. Şirket içinde barındırılan tümleştirme çalışma zamanını yüklediğiniz makinede kimlik bilgilerini şifrelemek için PowerShell 'i çalıştırırsanız, **Intranetten uzaktan erişimi** etkinleştiremezsiniz.

Yüksek kullanılabilirlik ve ölçeklenebilirlik için başka bir düğüm eklemeden önce **Intranetten uzaktan erişimi** etkinleştirin.  

Şirket içinde barındırılan tümleştirme çalışma zamanı kurulumu 3,3 veya üstünü çalıştırdığınızda, varsayılan olarak şirket içinde barındırılan tümleştirme çalışma zamanı yükleyicisi, şirket içinde barındırılan tümleştirme çalışma zamanı makinesindeki **Intranetten uzaktan erişimi** devre dışı bırakır.

Bir iş ortağından veya diğer bilgisayarlardan bir güvenlik duvarı kullandığınızda, 8060 numaralı bağlantı noktasını veya Kullanıcı tarafından yapılandırılmış bağlantı noktasını el ile açabilirsiniz. Şirket içinde barındırılan tümleştirme çalışma zamanını ayarlarken bir güvenlik duvarı sorununuz varsa, güvenlik duvarını yapılandırmadan şirket içinde barındırılan tümleştirme çalışma zamanını yüklemek için aşağıdaki komutu kullanın:

```cmd
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```

Şirket içinde barındırılan tümleştirme çalışma zamanı makinesinde 8060 numaralı bağlantı noktasını açmayı seçerseniz, veri deposu kimlik bilgilerini yapılandırmak için kimlik bilgileri uygulaması ayarı dışındaki mekanizmaları kullanın. Örneğin, **New-AzDataFactoryV2LinkedServiceEncryptCredential** PowerShell cmdlet 'ini kullanabilirsiniz.

## <a name="ports-and-firewalls"></a>Bağlantı noktaları ve güvenlik duvarları

Dikkate alınması gereken iki güvenlik duvarı vardır:

- Kuruluşun merkezi yönlendiricisinde çalışan *Kurumsal güvenlik duvarı*
- Şirket içinde barındırılan tümleştirme çalışma zamanının yüklü olduğu yerel makinede bir daemon olarak yapılandırılan *Windows Güvenlik Duvarı*

![Güvenlik duvarları](media/create-self-hosted-integration-runtime/firewall.png)

Şirket güvenlik duvarı düzeyinde, aşağıdaki etki alanlarını ve giden bağlantı noktalarını yapılandırmanız gerekir:

[!INCLUDE [domain-and-outbound-port-requirements](./includes/domain-and-outbound-port-requirements-internal.md)]

Windows güvenlik duvarı düzeyinde veya makine düzeyinde, bu giden bağlantı noktaları normalde etkindir. Bu değillerse, şirket içinde barındırılan tümleştirme çalışma zamanı makinesindeki etki alanlarını ve bağlantı noktalarını yapılandırabilirsiniz.

> [!NOTE]
> Şu anda Azure Relay hizmet etiketini desteklemediği için, Azure Relay iletişim için NSG kurallarında **Azurecyüksek** veya **Internet** hizmet etiketi kullanmanız gerekir.
> Azure Data Factory iletişim için NSG kural kurulumunda, **Datafactorymanagement** hizmet etiketini kullanabilirsiniz.

Kaynak ve havuzları temel alarak, kurumsal güvenlik duvarınızdaki veya Windows güvenlik duvarında ek etki alanlarına ve giden bağlantı noktalarına izin vermeniz gerekebilir.

[!INCLUDE [domain-and-outbound-port-requirements](./includes/domain-and-outbound-port-requirements-external.md)]

Azure SQL veritabanı ve Azure Data Lake gibi bazı bulut veritabanlarında, kendi güvenlik duvarı yapılandırmasındaki şirket içinde barındırılan tümleştirme çalışma zamanı makinelerinin IP adreslerine izin vermeniz gerekebilir.

### <a name="get-url-of-azure-relay"></a>Azure Relay URL 'sini al

Güvenlik duvarınızın izin verilenler listesine eklenmesi gereken bir gerekli etki alanı ve bağlantı noktası, Azure Relay iletişim içindir. Şirket içinde barındırılan tümleştirme çalışma zamanı, test bağlantısı, klasör listesi ve tablo listesi, şema al ve Önizleme verileri gibi etkileşimli yazma için bunu kullanır. **. ServiceBus.Windows.net** için izin vermek ve daha özel URL 'ler eklemek ISTIYORSANıZ, ADF portalından şirket içinde barındırılan tümleştirme çalışma zamanının gerektirdiği tüm FQDN 'leri görebilirsiniz. Şu adımları izleyin:

1. ADF Portalı ' na gidin ve şirket içinde barındırılan tümleştirme çalışma zamanı ' nı seçin.
2. Düzenle sayfasında **düğümler**' i seçin.
3. Tüm FQDN 'Leri almak için **hizmet URL 'Lerini görüntüle** ' yi seçin.

   ![Azure Relay URL 'Leri](media/create-self-hosted-integration-runtime/Azure-relay-url.png)

4. Bu FQDN 'leri, güvenlik duvarı kuralları izin verilenler listesine ekleyebilirsiniz.

### <a name="copy-data-from-a-source-to-a-sink"></a>Verileri bir kaynaktan havuza kopyalama

Şirket güvenlik duvarında güvenlik duvarı kurallarını, şirket içinde barındırılan tümleştirme çalışma zamanı makinesinin Windows güvenlik duvarını ve veri deposunun kendisini düzgün şekilde etkinleştirdiğinizden emin olun. Bu kuralların etkinleştirilmesi, şirket içinde barındırılan tümleştirme çalışma zamanının hem kaynak hem de havuza başarıyla bağlanmasını sağlar. Kopyalama işleminde yer alan her bir veri deposu için kuralları etkinleştirin.

Örneğin, şirket içi bir veri deposundan bir SQL veritabanı havuzuna veya bir Azure SYNAPSE Analytics havuzuna kopyalamak için aşağıdaki adımları uygulayın:

1. Hem Windows Güvenlik Duvarı hem de kurumsal güvenlik duvarı için bağlantı noktası 1433 üzerinde giden TCP iletişimine izin verin.
2. Şirket içinde barındırılan tümleştirme çalışma zamanı makinesinin IP adresini izin verilen IP adresleri listesine eklemek için SQL veritabanının güvenlik duvarı ayarlarını yapılandırın.

> [!NOTE]
> Güvenlik duvarınız giden bağlantı noktası 1433 ' a izin vermezse, şirket içinde barındırılan tümleştirme çalışma zamanı SQL veritabanına doğrudan erişemez. Bu durumda, [hazırlanan bir KOPYAYı](copy-activity-performance.md) SQL Database ve Azure SYNAPSE Analytics 'e kullanabilirsiniz. Bu senaryoda, veri taşıma için yalnızca HTTPS (bağlantı noktası 443) gerekir.

## <a name="installation-best-practices"></a>Yükleme için en iyi yöntemler

Şirket içinde barındırılan tümleştirme çalışma zamanını, [Microsoft Indirme merkezi](https://www.microsoft.com/download/details.aspx?id=39717)' nden yönetilen bir kimlik kurulum paketini indirerek yükleyebilirsiniz. Adım adım yönergeler için [verileri şirket içi ve bulut arasında taşıma](tutorial-hybrid-copy-powershell.md) makalesine bakın.

- Makinenin hazırda bekletmeden olmaması için, şirket içinde barındırılan tümleştirme çalışma zamanı için konak makinede bir güç planı yapılandırın. Ana makine hazırda bekletmeye başlarsa, şirket içinde barındırılan tümleştirme çalışma zamanı çevrimdışı olarak geçer.
- Şirket içinde barındırılan tümleştirme çalışma zamanı ile ilişkili kimlik bilgilerini düzenli olarak yedekleyin.
- Şirket içinde barındırılan IR kurulum işlemlerini otomatik hale getirmek için, [PowerShell aracılığıyla mevcut bir şirket içinde BARıNDıRıLAN IR ayarlama](#setting-up-a-self-hosted-integration-runtime)bölümüne bakın.

## <a name="next-steps"></a>Sonraki adımlar

Adım adım yönergeler için bkz. [öğretici: şirket içi verileri buluta kopyalama](tutorial-hybrid-copy-powershell.md).
