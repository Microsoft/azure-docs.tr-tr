---
title: Azure Automation 'da ilk Python runbook 'im
description: Basit bir Python runbook 'u oluşturma, test etme ve yayımlamayı adım adım gösteren öğretici.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 03/19/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 626f446c18acf1f07f458fb1b4238f182546e479
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596940"
---
# <a name="my-first-python-runbook"></a>İlk Python runbook 'um

> [!div class="op_single_selector"]
> - [Grafik](automation-first-runbook-graphical.md)
> - [PowerShell](automation-first-runbook-textual-powershell.md)
> - [PowerShell İş Akışı](automation-first-runbook-textual.md)
> - [Python](automation-first-runbook-textual-python2.md)

Bu öğreticide, Azure Otomasyonu 'nda bir [Python runbook](automation-runbook-types.md#python-runbooks) oluşturma işlemi adım adım açıklanmaktadır. Test ve yayımladığınız basit bir runbook 'u kullanmaya başlayabilirsiniz. Ardından, bir Azure sanal makinesini başlatmayı içeren bir örnekle, bu runbook’u gerçekten Azure kaynaklarını yönetmek üzere değiştirin. Son olarak, runbook parametreleri ekleyerek runbook 'u daha sağlam hale getirebilirsiniz.

> [!NOTE]
> Python runbook 'u başlatmak için Web kancası kullanılması desteklenmez.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakiler gerekir:

- Azure aboneliği. Henüz bir aboneliğiniz yoksa [MSDN abone avantajlarınızı etkinleştirebilir](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ya da [ücretsiz hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) için kaydolabilirsiniz.
- Runbook’u tutacak ve Azure kaynaklarında kimlik doğrulamasını yapacak bir [Automation hesabı](automation-offering-get-started.md). Bu hesabın sanal makineyi başlatma ve durdurma izni olmalıdır.
- Azure sanal makinesi. Bu makineyi durdurup başlatacağınız için makinenin üretime yönelik bir VM olmaması gerekir.

## <a name="create-a-new-runbook"></a>Yeni runbook oluştur

*Merhaba Dünya*metin çıkışı veren basit bir runbook oluşturarak başlayın.

1. Azure portalında, Otomasyon hesabınızı açın.

    Automation hesabı sayfası size bu hesaptaki kaynakların hızlı bir görünümünü sağlar. Birkaç varlığınız zaten olmalıdır. Bu varlıkların çoğu, yeni bir Otomasyon hesabına otomatik olarak dahil edilen modüllerdir. Burada ayrıca [önkoşullarda](#prerequisites) belirtilen Kimlik Bilgileri varlığınız da bulunmalıdır.<br>

1. Runbook’ların listesini açmak için **İŞLEM YÖNETİMİ** altında **Runbooklar**’ı seçin.
1. Yeni bir runbook oluşturmak için **+ runbook Ekle** ' yi seçin.
1. Runbook *'A Myfirstrunbook-Python*adını verin.
1. Bu durumda, bir [Python runbook](automation-runbook-types.md#python-runbooks) oluşturacaksınız, bu nedenle **runbook türü**için **Python 2** ' yi seçin.
1. Runbook’u oluşturmak için **Oluştur**’a tıklayın ve metin düzenleyicisini açın.

## <a name="add-code-to-the-runbook"></a>Runbook 'a kod ekleme

Şimdi "Merhaba Dünya" metnini yazdırmak için basit bir komut eklersiniz:

```python
print("Hello World!")
```

Runbook 'u kaydetmek için **Kaydet** ' e tıklayın.

## <a name="test-the-runbook"></a>Runbook'u test etme

Runbook’u üretimde kullanılabilir hale getirmek üzere yayımlamadan önce düzgün çalıştığından emin olmak için test etmeniz gerekir. Bir runbook'u test ettiğinizde, bunun **Taslak** sürümünü çalıştırır ve çıktısını etkileşimli olarak görüntülersiniz.

1. Test bölmesini açmak için **Test bölmesi**’ne tıklayın.
1. Testi başlatmak için **Başlat**’a tıklayın. Etkinleştirilen tek seçenek bu olmalıdır.
1. Bir [runbook işi](automation-runbook-execution.md) oluşturulur ve durumu görüntülenir.
   İş durumu, bulutta bir runbook çalışanının kullanılabilir hale gelmesinin beklendiğini gösteren şekilde *Sırada* olarak başlar. Bir çalışan işi talep ettiği zaman *başlayacak* ve ardından runbook gerçekten çalışmaya başladığında *çalışır* .
1. Runbook işi tamamlandığında çıktısı görüntülenir. Bu durumda *Merhaba Dünya*görmeniz gerekir.
1. Tuvale geri dönmek için Test bölmesini kapatın.

## <a name="publish-and-start-the-runbook"></a>Runbook 'u yayımlama ve başlatma

Oluşturduğunuz runbook hala taslak modunda. Bunu üretimde çalıştırmadan önce yayımlamanız gerekir.
Bir runbook yayımladığınızda, var olan yayımlanmış sürümün üzerine taslak sürümünü yazın.
Bu durumda, henüz runbook 'u oluşturduğunuz için yayınlanmış bir sürümdür.

1. Runbook’u yayımlamak için **Yayımla**’ya tıklayın ve sorulduğunda **Evet**’e tıklayın.
1. Şimdi runbook 'u runbook **'lar** bölmesinde görüntülemek için sola kaydırırsanız, **yayımlanmış**bir **yazma durumu** gösterir.
1. **Myfirstrunbook-Python**bölmesini görüntülemek için sağa kaydırın.
   Üstteki seçenekler runbook 'u başlatmamıza, runbook 'u görüntülemenize veya gelecekte bir süre sonra başlayacak şekilde zamanlamaya izin verir.
2. Runbook 'u başlatmak istiyorsunuz, bu nedenle **Başlat** ' a tıklayın ve Runbook 'u Başlat dikey penceresi açıldığında **Tamam** ' a tıklayın.
3. Oluşturduğunuz runbook işi için bir iş bölmesi açılır. Bu bölmeyi kapatabilirsiniz, ancak bu durumda, işin ilerlemesini izlemek için açık bırakmalısınız.
1. İş durumu **Iş özetinde** gösterilir ve Runbook 'u test ettiğinizde gördüğünüz durumları eşleştirir.
2. Runbook durumu olarak *Tamamlandı* gösterilince **Çıktı**’ya tıklayın. Çıkış bölmesi açılır ve *Merhaba Dünya*bakabilirsiniz.
3. Çıktı bölmesini kapatın.
4. Runbook işine ait Akışlar bölmesini açmak için **Tüm Günlükler**’e tıklayın. Çıktı akışında yalnızca *Merhaba Dünya* metnini görmelisiniz, ancak bu bölmede, runbook bunlara yazıyorsa Ayrıntılı ve Hata gibi runbook işine yönelik diğer akışlar da gösterilebilir.
5. MyFirstRunbook-Python bölmesine dönmek için akışlar bölmesini ve Iş bölmesini kapatın.
6. Bu runbook’a ait İşler bölmesini açmak için **İşler**’e tıklayın. Bu bölmede, bu runbook tarafından oluşturulan tüm işler listelenir. İşi yalnızca bir kez çalıştırdığınız için sadece bir işin listelendiğini görmeniz gerekir.
7. Runbook’u başlattığınızda, görüntülediğiniz iş bölmesini açmak için bu işe tıklayabilirsiniz. Böylece zaman içinde geri dönerek, belirli bir runbook için oluşturulan herhangi bir işin ayrıntılarını görüntüleyebilirsiniz.

## <a name="add-authentication-to-manage-azure-resources"></a>Azure kaynaklarını yönetmek için kimlik doğrulaması ekleme

Runbook uygulamanızı test ettiniz ve yayımladınız, ancak şu ana kadar faydalı bir şey yapmadı. Bu runbook’un Azure kaynaklarını yönetmesini istiyorsunuz.
Azure kaynaklarını yönetmek için betiğin Otomasyon hesabınızdaki kimlik bilgilerini kullanarak kimlik doğrulaması yapması gerekir. Size yardımcı olmak için [Azure Otomasyonu yardımcı programını](https://github.com/azureautomation/azure_automation_utility) kullanarak Azure kaynaklarıyla kimlik doğrulaması ve etkileşim kurmayı kolaylaştırın.

> [!NOTE]
> Otomasyon hesabı, bir farklı çalıştır sertifikası olmak için hizmet sorumlusu özelliği ile oluşturulmuş olmalıdır.
> Otomasyon hesabınız hizmet sorumlusu ile oluşturulmadıysa, [Python Için Azure Yönetim kitaplıkları Ile kimlik doğrulama](/azure/python/python-sdk-azure-authenticate)konusunda açıklanan yöntemi kullanarak kimlik doğrulaması yapabilirsiniz.

1. MyFirstRunbook-Python bölmesinde **Düzenle** ' ye tıklayarak metinsel düzenleyiciyi açın.

2. Azure 'da kimlik doğrulaması yapmak için aşağıdaki kodu ekleyin:

   ```python
   import os
   from azure.mgmt.compute import ComputeManagementClient
   import azure.mgmt.resource
   import automationassets

   def get_automation_runas_credential(runas_connection):
       from OpenSSL import crypto
       import binascii
       from msrestazure import azure_active_directory
       import adal

       # Get the Azure Automation RunAs service principal certificate
       cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
       pks12_cert = crypto.load_pkcs12(cert)
       pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())

       # Get run as connection information for the Azure Automation service principal
       application_id = runas_connection["ApplicationId"]
       thumbprint = runas_connection["CertificateThumbprint"]
       tenant_id = runas_connection["TenantId"]

       # Authenticate with service principal certificate
       resource ="https://management.core.windows.net/"
       authority_url = ("https://login.microsoftonline.com/"+tenant_id)
       context = adal.AuthenticationContext(authority_url)
       return azure_active_directory.AdalAuthentication(
       lambda: context.acquire_token_with_client_certificate(
               resource,
               application_id,
               pem_pkey,
               thumbprint)
       )

   # Authenticate to Azure using the Azure Automation RunAs service principal
   runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
   azure_credential = get_automation_runas_credential(runas_connection)
   ```

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Python Işlem istemcisi oluşturmak ve VM 'yi başlatmak için kod ekleme

Azure VM 'leriyle çalışmak için [Python Için Azure işlem istemcisinin](https://docs.microsoft.com/python/api/azure-mgmt-compute/azure.mgmt.compute.computemanagementclient)bir örneğini oluşturun.

VM 'yi başlatmak için Işlem istemcisini kullanın. Aşağıdaki kodu runbook 'a ekleyin:

```python
# Initialize the compute management client with the RunAs credential and specify the subscription to work against.
compute_client = ComputeManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"])
)


print('\nStart VM')
async_vm_start = compute_client.virtual_machines.start(
    "MyResourceGroup", "TestVM")
async_vm_start.wait()
```

Burada _Myresourcegroup_ , VM 'yi içeren kaynak grubunun adıdır ve _Testvm_ , başlatmak istediğiniz VM 'nin adıdır.

VM 'nin başlatıldığını görmek için Runbook 'u yeniden test edin ve çalıştırın.

## <a name="use-input-parameters"></a>Giriş parametrelerini kullanma

Runbook Şu anda kaynak grubunun ve VM 'nin adları için sabit kodlanmış değerler kullanıyor.
Şimdi Giriş parametrelerinden bu değerleri alan kodu ekleyelim.

Parametre değerlerini almak için `sys.argv` değişkenini kullanırsınız.
Aşağıdaki kodu diğer `import` deyimlerden hemen sonra runbook 'a ekleyin:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Bu, `sys` modülünü içeri aktarır ve kaynak grubunu ve VM adlarını tutmak için iki değişken oluşturur.
Bağımsız değişken listesinin öğesi `sys.argv[0]`, betiğin adı olduğunu ve Kullanıcı tarafından giriş olmadığına dikkat edin.

Artık runbook 'un son iki satırını, sabit kodlanmış değerler kullanmak yerine giriş parametresi değerlerini kullanacak şekilde değiştirebilirsiniz:

```python
async_vm_start = compute_client.virtual_machines.start(
    resource_group_name, vm_name)
async_vm_start.wait()
```

Bir Python runbook 'u başlattığınızda ( **Test** sayfasında veya yayımlanan runbook olarak **) parametreler ' ın altındaki** **runbook 'u Başlat** sayfasında parametrelerin değerlerini girebilirsiniz.

İlk kutuya bir değer girmeyi başlattıktan sonra, gereken sayıda parametre değeri girebileceğiniz şekilde ikinci olarak bir görünür ve bu şekilde devam eder.

Bu değerler, az önce eklediğiniz koddaki gibi `sys.argv` dizisi olarak betikte kullanılabilir.

İlk parametrenin değeri olarak kaynak grubunuzun adını ve ikinci parametrenin değeri olarak başlatılacak VM 'nin adını girin.

![Parametre değerlerini girin](media/automation-first-runbook-textual-python/runbook-python-params.png)

Runbook 'u başlatmak için **Tamam** ' ı tıklatın. Runbook çalışır ve belirttiğiniz VM 'yi başlatır.

## <a name="error-handling-in-python"></a>Python 'da hata Işleme

Ayrıca, Python runbook 'larınızdan **Uyarı**, **hata**ve **hata ayıklama** akışları gibi çeşitli akışlar almak için aşağıdaki kuralları da kullanabilirsiniz.

```python
print("Hello World output") 
print("ERROR: - Hello world error")
print("WARNING: - Hello world warning")
print("DEBUG: - Hello world debug")
print("VERBOSE: - Hello world verbose")
```

Aşağıdaki örnek, `try...except` bloğunda kullanılan bu kuralı gösterir.

```python
try:
    raise Exception('one', 'two')
except Exception as detail:
    print 'ERROR: Handling run-time error:', detail
```

> [!NOTE]
> **sys. stderr** , Azure Otomasyonu 'nda desteklenmez.

## <a name="next-steps"></a>Sonraki adımlar

- PowerShell runbook'ları kullanmaya başlamak için bkz. [İlk PowerShell runbook uygulamam](automation-first-runbook-textual-powershell.md)
- Grafik runbook'ları kullanmaya başlamak için bkz. [İlk grafik runbook uygulamam](automation-first-runbook-graphical.md)
- PowerShell iş akışı runbook'larını kullanmaya başlamak için bkz. [İlk PowerShell iş akışı runbook uygulamam](automation-first-runbook-textual.md)
- Runbook türleri, avantajları ve sınırlamaları hakkında daha fazla bilgi için bkz. [Azure Automation runbook türleri](automation-runbook-types.md)
- Python ile Azure için geliştirme hakkında bilgi edinmek için bkz. [Python geliştiricileri Için Azure](/azure/python/)
- Örnek Python 2 runbook 'larını görüntülemek için bkz. [Azure Otomasyonu GitHub](https://github.com/azureautomation/runbooks/tree/master/Utility/Python)
