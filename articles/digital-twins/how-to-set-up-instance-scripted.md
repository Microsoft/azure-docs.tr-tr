---
title: Örnek ayarlama ve kimlik doğrulaması (betik ile)
titleSuffix: Azure Digital Twins
description: Otomatikleştirilmiş bir dağıtım betiği çalıştırarak Azure dijital TWINS hizmeti örneğini ayarlama bölümüne bakın
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q3
ms.openlocfilehash: 4ef4e1f11018c82d96907518b79cc5737c49b946
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107104057"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-scripted"></a>Azure dijital TWINS örneği ve kimlik doğrulaması (komut dosyası) ayarlama

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Bu makalede, örneği oluşturma ve kimlik doğrulamasını ayarlama dahil olmak üzere **Yeni bir Azure dijital TWINS örneği ayarlama** adımları ele alınmaktadır. Bu makaleyi tamamladıktan sonra, programlamaya başlamak için kullanabileceğiniz bir Azure dijital TWINS örneğine sahip olursunuz.

Bu makalenin bu sürümü, işlemi kolaylaştıran [ **otomatikleştirilmiş bir dağıtım betiği** örneği](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) çalıştırarak bu adımları tamamlar. 
* Betiğin arka planda çalıştığı el ile CLı adımlarını görüntülemek için, bu makalenin CLı sürümüne bakın: [*nasıl yapılır: bir örnek ve kimlik doğrulaması (CLI) ayarlama*](how-to-set-up-instance-cli.md).
* Azure portal göre el ile adımları görüntülemek için, bu makalenin Portal sürümüne bakın: [*nasıl yapılır: bir örnek ve kimlik doğrulaması (portal) ayarlama*](how-to-set-up-instance-portal.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="prerequisites-download-the-script"></a>Önkoşullar: betiği Indir

Örnek betik, PowerShell 'de yazılmıştır. Bu örnek bağlantıya giderek ve başlık altındaki *koda Gözat* düğmesine tıklayarak makinenize Indirebileceğiniz [**Azure dijital TWINS uçtan uca örneklerinin**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)bir parçasıdır. Bu işlem sizi, örnek olarak indirebileceğiniz GitHub deposuna götürür *.* *Kod* düğmesini seçip zip ' i *indirin*.

:::image type="content" source="media/includes/download-repo-zip.png" alt-text="GitHub 'da dijital-TWINS-örnek deposunun görünümü. Kod düğmesi seçilidir ve posta Indirme düğmesinin vurgulandığı küçük bir iletişim kutusu oluşturulur." lightbox="media/includes/download-repo-zip.png":::

Bu, bir yükler *. ZIP* klasörünü makinenize **digital-twins-samples-master.zip**. Makinenizde bulunan klasöre gidin ve dosyaları ayıklamak için dosyayı ayıklayın.

Sıkıştırılmış klasörde, dağıtım betiği, _**deploy.ps1**> Digital-TWINS-samples-Master > betiklerinde_ bulunur.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="run-the-deployment-script"></a>Dağıtım betiğini çalıştırma

Bu makalede bir Azure dijital TWINS örneği ve gerekli kimlik doğrulama yarı otomatik olarak dağıtmak için bir Azure dijital TWINS kod örneği kullanılmaktadır. Ayrıca, kendi komut dosyası etkileşimlerinizi yazmak için bir başlangıç noktası olarak da kullanılabilir.

Dağıtım betiğini Cloud Shell ' de çalıştırma adımları aşağıda verilmiştir.
1. Tarayıcınızda bir [Azure Cloud Shell](https://shell.azure.com/) penceresine gidin. Şu komutu kullanarak oturum açın:
    ```azurecli-interactive
    az login
    ```
    CLı varsayılan tarayıcınızı açabildiğinden, bunu yapılır ve bir Azure oturum açma sayfası yükler. Aksi takdirde, konumunda bir tarayıcı sayfası açın *https://aka.ms/devicelogin* ve terminalinizde görünen yetkilendirme kodunu girin.
 
2. Cloud Shell simge çubuğunda, Cloud Shell PowerShell sürümünü çalıştıracak şekilde ayarlandığından emin olun.

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="PowerShell sürümünün seçimini gösteren pencere Cloud Shell":::

1. "Dosyaları karşıya yükle/Indir" simgesini seçin ve "karşıya yükle" yi seçin.

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Karşıya yükleme simgesinin seçimini gösteren Cloud Shell pencere":::

    Makinenizde _**deploy.ps1**_ dosyasına gidin ( _dijital-TWINS-örnekler-ana > betikler > **deploy.ps1**_) ve "Aç" düğmesine basın. Bu işlem, dosyayı Cloud Shell penceresinde çalıştırabilmeniz için Cloud Shell dosyasına yükler.

4. Komutu Cloud Shell penceresine göndererek betiği çalıştırın `./deploy.ps1` . Aşağıdaki komutu kopyalayabilir (Cloud Shell yapıştırmak için çağırır, Windows ve Linux 'ta **CTRL + SHIFT + v** ' i veya MacOS 'ta **cmd + SHIFT + v** ' yi kullanabilirsiniz. Sağ tıklama menüsünü de kullanabilirsiniz.

    ```azurecli-interactive
    ./deploy.ps1
    ```

    Betik, bir Azure dijital TWINS örneği oluşturur ve Azure User 'a Azure *Digital TWINS veri sahibi* rolünü örnek olarak atar.

    Komut dosyası otomatik kurulum adımları üzerinden çalışırken, aşağıdaki değerleri geçirmeniz istenir:
    * Örnek için: kullanılacak Azure aboneliğinizin *ABONELIK kimliği*
    * Örnek için: örneği dağıtmak istediğiniz *konum* . Azure dijital TWINS 'i destekleyen bölgeleri görmek için [*bölgeye göre kullanılabilen Azure ürünlerini*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)ziyaret edin.
    * Örnek için: bir *kaynak grubu* adı. Var olan bir kaynak grubunu kullanabilir veya oluşturmak için yeni bir ad girebilirsiniz.
    * Örnek için: Azure dijital TWINS örneğiniz için bir *ad* . Aboneliğiniz zaten belirtilen adı kullanan bölgede başka bir Azure dijital TWINS örneğine sahipse, farklı bir ad seçmeniz istenir.

Betikten çıktı günlüğü alıntısı aşağıda verilmiştir:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png" alt-text="Dağıtım betiği çalıştırıldığında giriş ve çıkış günlüğünü gösteren Cloud Shell pencere" lightbox="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png":::

Betik başarılı bir şekilde tamamlanırsa, nihai çıktıyı görürsünüz `Deployment completed successfully` . Aksi takdirde, hata iletisini adreslayın ve betiği yeniden çalıştırın. Bu işlem, önceden tamamladığınız adımları atlar ve kaldığınız yerden yeniden giriş istemeye başlar.

> [!NOTE]
> Betik şu anda Azure dijital TWINS (*Azure dijital TWINS veri sahibi*) içindeki gerekli yönetim rolünü, Cloud Shell betiği çalıştıran kullanıcıya atar. Bu rolü örneği yöneten başka birine atamanız gerekiyorsa, bunu şimdi Azure portal ([yönergeler](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) veya CLI ([yönergeler](how-to-set-up-instance-cli.md#set-up-user-access-permissions)) yoluyla yapabilirsiniz.

>[!NOTE]
>Şu anda, bazı kullanıcıların (özellikle kişisel [Microsoft hesaplarındaki kullanıcılar (MSAs)](https://account.microsoft.com/account)) **_Azure dijital TWINS_ 'in rol atamasını** bulabileceği, betikleştirilmiş kurulum ile ilgili **bilinen bir sorun** vardır.
>
>Rol atamasını, bu makalenin ilerleyen kısımlarında bulunan [*Kullanıcı rolü atamasını doğrula*](#verify-user-role-assignment) bölümü ile doğrulayabilirsiniz ve gerekirse, [Azure Portal](how-to-set-up-instance-portal.md#set-up-user-access-permissions) veya [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions)kullanarak rol atamasını el ile ayarlayabilirsiniz.
>
>Bu sorun hakkında daha fazla bilgi için bkz. [*sorun giderme: Azure dijital TWINS 'de bilinen sorunlar*](troubleshoot-known-issues.md#missing-role-assignment-after-scripted-setup).

## <a name="verify-success-and-collect-important-values"></a>Başarıyı doğrulama ve önemli değerleri toplama

Komut dosyası tarafından ayarlanan kaynaklarınızın ve izinlerinizin oluşturulmasını doğrulamak için aşağıdaki yönergelerden [Azure Portal](https://portal.azure.com) bakabilirsiniz. Herhangi bir adımın başarısını doğrulayadıysanız adımı yeniden deneyin. [Azure Portal](how-to-set-up-instance-portal.md) veya [CLI](how-to-set-up-instance-cli.md) yönergelerini kullanarak adımları tek tek gerçekleştirebilirsiniz.

Bu bölümde ayrıca, Azure dijital TWINS örneğiniz ile çalışmaya devam ederken ihtiyacınız olabilecek komut dosyası tarafından ayarlanan kaynaklardan önemli değerlerin nasıl bulunacağı gösterilmektedir. Bu değerleri güvenli bir yere kaydetmelisiniz veya daha sonra ihtiyacınız olduğunda bunları bulmak için bu bölüme geri dönün. Diğer kullanıcılar örnekle programlama yapacaktır, bu değerleri de onlarla paylaşabilirsiniz.

### <a name="verify-instance"></a>Örneği doğrula

Örneğinizin oluşturulduğunu doğrulamak için Azure portal [Azure dijital TWINS sayfasına](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) gidin. Bu sayfaya, Portal arama çubuğunda *Azure dijital TWINS* araması yaparak kendiniz ulaşabilirsiniz.

Bu sayfada tüm Azure dijital TWINS örnekleri listelenir. Listede yeni oluşturduğunuz örneğin adını arayın.

Doğrulama başarısız olduysa, [Portal](how-to-set-up-instance-portal.md#create-the-azure-digital-twins-instance) veya [CLI](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)kullanarak bir örnek oluşturmayı yeniden deneyebilirsiniz.

### <a name="collect-instance-values"></a>Örnek değerlerini toplama

[Azure dijital TWINS sayfasında](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) örneğinizin adını seçerek örneğin *genel bakış* sayfasını açın. *Adına*, *kaynak grubuna* ve *ana bilgisayar adına göz önünde barının*. Daha sonra örneğinizi belirleyip daha sonra bu örneğe bağlanmanız gerekebilir.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Örneğin genel bakış sayfasından önemli değerleri vurgulama":::

### <a name="verify-user-role-assignment"></a>Kullanıcı rolü atamasını doğrula

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

> [!NOTE]
> Betiğin Şu anda bu gerekli rolü Cloud Shell betiği çalıştıran aynı kullanıcıya atadığını hatırlayın. Bu rolü örneği yöneten başka birine atamanız gerekiyorsa, bunu şimdi Azure portal ([yönergeler](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) veya CLI ([yönergeler](how-to-set-up-instance-cli.md#set-up-user-access-permissions)) yoluyla yapabilirsiniz.

Doğrulama başarısız olduysa, [Portal](how-to-set-up-instance-portal.md#set-up-user-access-permissions) veya [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions)kullanarak kendi rol atamasını de yineleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Azure Digital TWINS CLı komutlarını kullanarak örneğiniz için tek REST API çağrılarını test edin: 
* [az DT Reference](/cli/azure/dt)
* [*Nasıl yapılır: Azure dijital TWINS CLı 'sını kullanma*](how-to-use-cli.md)

Ya da bkz. kimlik doğrulama kodu ile bir istemci uygulamasını örneğinize bağlama:
* [*Nasıl yapılır: uygulama kimlik doğrulama kodunu yazma*](how-to-authenticate-client.md)