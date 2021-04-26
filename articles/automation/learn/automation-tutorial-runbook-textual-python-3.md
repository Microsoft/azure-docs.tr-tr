---
title: Azure Otomasyonu 'nda Python 3 runbook (Önizleme) oluşturma
description: Bu makalede basit bir Python 3 runbook (Önizleme) oluşturma, test etme ve yayımlama öğretilir.
services: automation
ms.subservice: process-automation
ms.date: 02/16/2021
ms.topic: tutorial
ms.openlocfilehash: c19f7e177d51a3de75e7d7ae2b83442e23efd243
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100546151"
---
# <a name="tutorial-create-a-python-3-runbook-preview"></a>Öğretici: Python 3 runbook 'u oluşturma (Önizleme)

Bu öğreticide, Azure Otomasyonu 'nda [Python 3 runbook](../automation-runbook-types.md#python-runbooks) (Önizleme) oluşturma işlemi adım adım açıklanmaktadır. Python runbook 'ları Python 2 ve 3 altında derlenir. Azure portal metin düzenleyicisini kullanarak runbook 'un kodunu doğrudan düzenleyebilirsiniz.

> [!div class="checklist"]
> * Basit bir Python runbook 'u oluşturma
> * Runbook 'u test etme ve yayımlama
> * Runbook işinin durumunu çalıştırma ve izleme
> * Runbook parametreleri ile bir Azure sanal makinesini başlatmak için Runbook 'u güncelleştirme

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakiler gerekir:

- Azure aboneliği. Henüz bir hesabınız yoksa [MSDN abone avantajlarınızı etkinleştirebilir](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) veya [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)için kaydolabilirsiniz.

- Runbook’u tutacak ve Azure kaynaklarında kimlik doğrulamasını yapacak bir [Automation hesabı](../automation-security-overview.md). Bu hesabın sanal makineyi başlatma ve durdurma izni olmalıdır.

- Azure sanal makinesi. Bu öğreticide, bu makineyi başlatıp durdurarak bir üretim sanal makinesi olmaması gerekir.

- Python 3 runbook 'ları çalıştırmak için bir Linux veya Windows karma runbook çalışanı kullanabilirsiniz. [Linux](../automation-linux-hrw-install.md)   veya [Windows](../automation-windows-hrw-install.md)   karma Runbook Worker yükleme yönergelerini izleyin.Linux karma çalışanı için belirli bir önkoşul yoktur. Ancak, Python 3 runbook 'lar için bir Windows hibrit çalışanı kullanmak için, yalnızca Python 3 ' ü destekliyorsanız veya Python 2 ve 3 ' ün bir arada olması gerekiyorsa, runbook worker 'ı barındıran makineli yapılandırın.

   * *Yalnızca* Python 2 veya Python 3 yüklüyse, python.exe **Path** ortam değişkeninde bulunan klasörün yolunu eklemeniz gerekir. Örneğin, python.exe yükleme yolunuzda `C:\Python` , bu yolun **Path** ortam değişkenine eklenmesi gerekir.

   * Hem Python 2 hem de Python 3 ' ü yüklediyseniz ve her iki türdeki runbook 'u çalıştırmak istiyorsanız, aşağıdaki ortam değişkenlerini yapılandırmanız gerekir:

     * Python 2-adlı yeni bir ortam değişkeni oluşturun `PYTHON_2_PATH` ve yükleme klasörünü belirtin. Örneğin, yükleme klasörü ise `C:\Python27` , bu yolun değişkenine eklenmesi gerekir.

     * Python 3-adlı yeni bir ortam değişkeni oluşturun `PYTHON_3_PATH` ve yükleme klasörünü belirtin. Örneğin, yükleme klasörü ise `C:\Python3` , bu yolun değişkenine eklenmesi gerekir.

## <a name="create-a-new-runbook"></a>Yeni runbook oluştur

*Merhaba Dünya* metin çıkışı veren basit bir runbook oluşturarak başlayın.

1. Azure portalında, Otomasyon hesabınızı açın.

    Automation hesabı sayfası size bu hesaptaki kaynakların hızlı bir görünümünü sağlar. Birkaç varlığınız zaten olmalıdır. Bu varlıkların çoğu, yeni bir Otomasyon hesabına otomatik olarak dahil edilen modüllerdir. Burada ayrıca [önkoşullarda](#prerequisites) belirtilen Kimlik Bilgileri varlığınız da bulunmalıdır.

2. Runbook 'ların listesini açmak için **Işlem Otomasyonu** altında **runbook 'lar** ' ı seçin.

3. Yeni bir runbook oluşturmak için **runbook Ekle** ' yi seçin.

4. Runbook **'A Myfirstrunbook-Python** adını verin.

5. **Runbook türü** için **Python 3 ' ü** seçin.

6. Runbook 'u oluşturmak için **Oluştur** ' u seçin ve metin düzenleyicisini açın.

## <a name="add-code-to-the-runbook"></a>Runbook 'a kod ekleme

Artık metni yazdırmak için basit bir komut eklersiniz `Hello World` .

```python
print("Hello World!")
```

Runbook 'u kaydetmek için **Kaydet** ' i seçin.

## <a name="test-the-runbook"></a>Runbook'u test etme

Runbook’u üretimde kullanılabilir hale getirmek üzere yayımlamadan önce düzgün çalıştığından emin olmak için test etmeniz gerekir. Bir runbook 'u test ettiğinizde, taslak sürümünü çalıştırır ve çıktısını etkileşimli olarak görüntüleyebilirsiniz.

1. Test **bölmesini açmak** için **Test bölmesi** ' ni seçin.

2. Testi başlatmak için **Başlat** ' ı seçin. Etkinleştirilen tek seçenek bu olmalıdır.

3. Bir [runbook işi](../automation-runbook-execution.md) oluşturulur ve durumu görüntülenir.
   İş durumu, bulutta bir runbook worker 'ın kullanılabilir hale gelmesini beklediğini belirten **sıraya alındı** olarak başlar. Bir çalışan işi talep ettiği zaman **başlayacak** şekilde değişir ve ardından runbook aslında çalışmaya başladığında **çalışır** .

4. Runbook işi tamamlandığında çıktısı görüntülenir. Bu durumda, görmeniz gerekir `Hello World` .

5. Tuvale geri dönmek için **Test** bölmesini kapatın.

## <a name="publish-and-start-the-runbook"></a>Runbook 'u yayımlama ve başlatma

Oluşturduğunuz runbook hala taslak modunda. Bunu üretimde çalıştırmadan önce yayımlamanız gerekir. Bir runbook yayımladığınızda, var olan yayımlanmış sürümün üzerine taslak sürümünü yazın. Bu durumda, henüz runbook 'u oluşturduğunuz için yayınlanmış bir sürümdür.

1. Runbook 'u yayımlamak için **Yayımla** ' yı seçin ve istendiğinde **Evet** ' e tıklayın.

2. Runbook 'u runbook **'ları** sayfasında görüntülemek için sola kaydırırsanız **yayımlanmış** bir **yazma durumu** görmeniz gerekir.

3. **Myfirstrunbook-Python3** bölmesini görüntülemek için sağ tarafa geri kaydırın.

   Üstteki seçenekler, runbook 'u başlatabilir, runbook 'u görüntüleyebilir veya gelecekte bir süre sonra başlayacak şekilde zamanlayabilirsiniz.

4. **Başlat** ' ı seçin ve **runbook 'u Başlat** bölmesi açıldığında **Tamam** ' ı seçin.

5. Oluşturduğunuz runbook işi için bir **iş** bölmesi açılır. Bu bölmeyi kapatabilir, ancak işin ilerlemesini izleyebilmeniz için açık bırakalım.

6. İş durumu **Iş özetinde** gösterilir ve Runbook 'u test ettiğinizde gördüğünüz durumları eşleştirir.

7. Runbook durumu **tamamlandı** olarak gösterildikten sonra **Çıkış**' ı seçin. Burada görebileceğiniz **Çıkış** bölmesi açılır `Hello World` .

8. **Çıkış** bölmesini kapatın.

9. Runbook işinin **akışlar** bölmesini açmak Için **Tüm Günlükler** ' i seçin. Yalnızca `Hello World` Çıkış akışında görmeniz gerekir. Ancak bu bölme, runbook 'a yazıyorsa **ayrıntılı** ve **hata** gibi bir runbook işi için diğer akışları gösterebilir.

10. **Myfirstrunbook-Python3** bölmesine dönmek için **akışlar** bölmesini ve **iş** bölmesini kapatın.

11. Bu runbook 'un **işler** sayfasını açmak için **işler** ' i seçin. Bu sayfada bu runbook tarafından oluşturulan tüm işler listelenir. İşi yalnızca bir kez çalıştırdığınız için sadece bir işin listelendiğini görmeniz gerekir.

12. Bu işi, runbook 'u başlattığınızda görüntülediğiniz aynı **iş** bölmesini açmak için seçebilirsiniz. Bu bölme, zaman içinde geri dönmenizi ve belirli bir runbook için oluşturulan herhangi bir işin ayrıntılarını görüntülemenizi sağlar.

## <a name="add-authentication-to-manage-azure-resources"></a>Azure kaynaklarını yönetmek için kimlik doğrulaması ekleme

Runbook uygulamanızı test ettiniz ve yayımladınız, ancak şu ana kadar faydalı bir şey yapmadı. Bu runbook’un Azure kaynaklarını yönetmesini istiyorsunuz.
Bunu yapmak için, komut dosyasının Otomasyon hesabınızdaki kimlik bilgilerini kullanarak kimlik doğrulaması yapması gerekir.

> [!NOTE]
> Otomasyon hesabı, bir farklı çalıştır sertifikası olmak için hizmet sorumlusu özelliği ile oluşturulmuş olmalıdır.
> Otomasyon hesabınız hizmet sorumlusu ile oluşturulmadıysa, [Python Için Azure Yönetim kitaplıklarıyla kimlik doğrulama](/azure/python/python-sdk-azure-authenticate)konusunda açıklandığı gibi kimlik doğrulaması yapabilirsiniz.

1. **Myfirstrunbook-Python3** bölmesinde **Düzenle** seçeneğini belirleyerek metinsel düzenleyiciyi açın.

2. Azure 'da kimlik doğrulaması yapmak için aşağıdaki kodu ekleyin:

    ```python
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
    ```

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Python Işlem istemcisi oluşturmak ve VM 'yi başlatmak için kod ekleme

Azure VM 'leriyle çalışmak için [Python Için Azure işlem istemcisinin](/python/api/azure-mgmt-compute/azure.mgmt.compute.computemanagementclient)bir örneğini oluşturun.

VM 'yi başlatmak için işlem istemcisini kullanın. Aşağıdaki kodu runbook 'a ekleyin:

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

, `MyResourceGroup` Sanal makineyi içeren kaynak grubunun adıdır ve `TestVM` başlatmak istediğiniz VM 'nin adıdır.

VM 'nin başlatıldığını görmek için Runbook 'u yeniden test edin ve çalıştırın.

## <a name="use-input-parameters"></a>Giriş parametrelerini kullanma

Runbook Şu anda kaynak grubunun ve VM 'nin adları için sabit kodlanmış değerler kullanıyor. Şimdi Giriş parametrelerinden bu değerleri alan kodu ekleyelim.

`sys.argv`Parametre değerlerini almak için değişkenini kullanırsınız. Aşağıdaki kodu diğer deyimlerden hemen sonra runbook 'a ekleyin `import` :

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Bu, modülünü içeri aktarır `sys` ve kaynak grubunu ve VM adlarını tutmak için iki değişken oluşturur. Bağımsız değişken listesinin öğesi, `sys.argv[0]` , betiğin adı olduğunu ve Kullanıcı tarafından giriş olmadığına dikkat edin.

Artık runbook 'un son iki satırını, sabit kodlanmış değerler kullanmak yerine giriş parametresi değerlerini kullanacak şekilde değiştirebilirsiniz:

```python
async_vm_start = compute_client.virtual_machines.start(
    resource_group_name, vm_name)
async_vm_start.wait()
```

**Test** bölmesinden ya da yayımlanmış bir runbook olarak bir Python runbook 'u başlattığınızda, Parametreler altındaki **runbook 'u Başlat** **sayfasında parametrelerin değerlerini** girebilirsiniz.

İlk kutuya bir değer girmeyi başlattıktan sonra, ikinci olarak bir belirir ve bu şekilde gerektiği kadar parametre değeri girebilirsiniz.

Değerler dizideki komut dosyasında, `sys.argv` yeni eklediğiniz kodda olduğu gibi kullanılabilir.

İlk parametrenin değeri olarak kaynak grubunuzun adını ve ikinci parametrenin değeri olarak başlatılacak VM 'nin adını girin.

![Parametre değerlerini girin](../media/automation-tutorial-runbook-textual-python/runbook-python-params.png)

Runbook 'u başlatmak için **Tamam ' ı** seçin. Runbook çalışır ve belirttiğiniz VM 'yi başlatır.

## <a name="error-handling-in-python"></a>Python 'da hata işleme

Ayrıca, Python runbook 'larınızdan uyarı, hata ve hata ayıklama akışları gibi çeşitli akışlar almak için aşağıdaki kuralları da kullanabilirsiniz.

```python
print("Hello World output")
print("ERROR: - Hello world error")
print("WARNING: - Hello world warning")
print("DEBUG: - Hello world debug")
print("VERBOSE: - Hello world verbose")
```

Aşağıdaki örnekte, bir bloğunda kullanılan bu kural gösterilmektedir `try...except` .

```python
try:
    raise Exception('one', 'two')
except Exception as detail:
    print ('ERROR: Handling run-time error:', detail)
```

## <a name="next-steps"></a>Sonraki adımlar

- Runbook türleri, avantajları ve sınırlamaları hakkında daha fazla bilgi için bkz. [Azure Otomasyonu runbook türleri](../automation-runbook-types.md).

- Python ile Azure için geliştirme hakkında bilgi edinmek için bkz. [Python geliştiricileri Için Azure](/azure/python/).

- Örnek Python 3 runbook 'larını görüntülemek için bkz. [Azure Otomasyonu GitHub](https://github.com/azureautomation/runbooks/tree/master/Utility/Python).
