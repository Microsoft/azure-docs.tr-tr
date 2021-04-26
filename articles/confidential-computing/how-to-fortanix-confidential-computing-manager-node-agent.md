---
title: Fortanix gizli bilgi Işlem yöneticisi ile uygulama çalıştırma
description: Kapsayıcılı görüntülerinizi dönüştürmek için Fortanx gizli bilgi Işlem yöneticisi 'ni nasıl kullanacağınızı öğrenin
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: how-to
ms.date: 03/24/2021
ms.author: JenCook
ms.openlocfilehash: 91a3f0a38d1182e445eba39bf31092be17f9a1fd
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111998"
---
# <a name="how-to-run-an-application-with-fortanix-confidential-computing-manager"></a>Nasıl yapılır: Fortanix gizli bilgi Işlem yöneticisi ile uygulama çalıştırma

Fortanx [Gizli Bilgi Işlem yöneticisi](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.enclave_manager?tab=Overview) ve Fortanx [düğüm Aracısı](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.rte_node_agent) 'nı kullanarak uygulamanızı Azure gizli bilgi işlem ortamında çalıştırmaya [başlayın.](https://www.fortanix.com/)


Fortanx, Azure altyapısının üzerine kurulmuş ürün ve hizmetlere sahip bir üçüncü taraf yazılım satıcıdır. Azure 'da benzer gizli bilgi işlem hizmetleri sunan başka üçüncü taraf sağlayıcılar vardır.

> [!Note]
> Bu belgede başvurulan Ürünler Microsoft denetimi altında değil. Microsoft bu bilgileri size kolaylık sağlaması için sağlıyor ve bu Microsoft dışı ürünlerin başvurusu Microsoft tarafından onay göstermez.

Bu öğreticide, uygulama görüntünüzü gizli işlem korumalı bir görüntüye nasıl dönüştürebileceğiniz gösterilmektedir. Bu ortam, Azure 'un DCsv2-Series Intel SGX özellikli sanal makineler tarafından desteklenen [Fortanx](https://www.fortanix.com/) yazılımını kullanır. Bu çözüm, kimlik doğrulama ve veri erişim denetimi gibi kritik güvenlik ilkelerini düzenler.

Fortanix 'e özgü destek için [Fortanix bolluk topluluğuna](https://fortanix.com/community/) katılarak kanalı kullanın `#enclavemanager` .

## <a name="prerequisites"></a>Önkoşullar

1. Bir Fortanx gizli bilgi Işlem yöneticisi hesabınız yoksa, başlamadan önce [kaydolun](https://ccm.fortanix.com/auth/sign-up) .
1. Dönüştürülmüş uygulama görüntülerini göndermek için özel bir [Docker](https://docs.docker.com/) kayıt defteri.
1. Azure aboneliğiniz yoksa başlamadan önce [bir hesap oluşturun](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) .

> [!NOTE]
> Ücretsiz deneme hesaplarının bu öğreticide kullanılan sanal makinelere erişimi yoktur. Lütfen Kullandıkça Öde aboneliğine yükseltin.

## <a name="add-an-application-to-fortanix-confidential-computing-manager"></a>Fortanix gizli bilgi Işlem yöneticisi 'ne uygulama ekleme

1. [Fortanix gizli bilgi Işlem yöneticisi 'nde (FORTANX CCM)](https://ccm.fortanix.com)oturum açın.
1. **Hesaplar** sayfasına gıdın ve **Hesap Ekle** ' yi seçerek yeni bir hesap oluşturun.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/create-account-new.png" alt-text="Hesap oluşturmayı gösteren ekran görüntüsü.":::

1. Hesabınız oluşturulduktan sonra, yeni oluşturulan hesabı seçmek için **Seç** ' e tıklayın. Şimdi işlem düğümlerini kaydetmeye ve uygulamalar oluşturmaya başlayabiliriz.
1. Uygulama eklemek için **+ uygulama** düğmesini seçin. Bu örnekte, bir Flask sunucusu şifreleme SISTEMI uygulaması ekliyoruz.

1. Şifreleme işletim sistemi uygulaması için **Ekle** düğmesini seçin.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/add-enclave-application.png" alt-text="Uygulamanın nasıl ekleneceğini gösteren ekran görüntüsü.":::

   > [!NOTE]
   > Bu öğretici yalnızca şifreleme işletim sistemi uygulamaları eklemeyi içerir. EDP Rust uygulamalarını Fortanx gizli bilgi Işlem yöneticisi 'ne getirme hakkında [daha fazla bilgi edinin](https://support.fortanix.com/hc/en-us/articles/360044746932-Bringing-EDP-Rust-Apps-to-Confidential-Computing-Manager) .

1. Bu öğreticide, örnek uygulama için Fortanx ' in Docker kayıt defterini kullanacağız. Aşağıdaki bilgilerden ayrıntıları girin. Çıkış görüntüsünü tutmak için özel Docker kayıt defterinizi kullanın.

    - **Uygulama adı**: Python uygulama sunucusu
    - **Açıklama**: Python Flask sunucusu
    - **Giriş resmi adı**: fortanx/Python-Flask
    - **Çıkış görüntüsü adı**: fortanx-Private/Python-Flask-SGX
    - **ISVPRODID**: 1
    - **Ivsvm**: 1
    - **Bellek boyutu**: 1 GB
    - **Iş parçacığı sayısı**: 128

    *Isteğe bağlı*: uygulamayı çalıştırın.
    - **Docker Hub**: [https://hub.docker.com/u/fortanix](https://hub.docker.com/u/fortanix)
    - **Uygulama**: fortanix/Python-Flask

      Şu komutu çalıştırın:

      ```bash
         sudo docker run fortanix/python-flask
      ```

1. Sertifika ekleyin. Aşağıdaki ayrıntıları kullanarak bilgileri girin ve ardından **İleri**' yi seçin:
    - **Etki alanı**: MyApp. domain. Dom
    - **Tür**: gizli bilgi işlem yöneticisi tarafından verilen sertifika
    - **Anahtar yolu**:/appkey.exe
    - **Anahtar türü**: RSA
    - **Sertifika yolu**:/appcert.exe
    - **RSA anahtar boyutu**: 2048 bit

## <a name="create-an-image"></a>Görüntü oluşturma

Bir Fortanx CCM görüntüsü, bir uygulamanın yazılım sürümüdür veya sürümüdür. Her görüntü, bir şifreleme karması (MRENCLAVE) ile ilişkilendirilir.

1. **Görüntü ekle** sayfasında, **Çıkış görüntüsü adı** IÇIN **kayıt defteri kimlik bilgilerini** girin. Bu kimlik bilgileri, görüntünün gönderildiği özel Docker kayıt defterine erişmek için kullanılır.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/create-image.png" alt-text="Görüntü oluşturmayı gösteren ekran görüntüsü.":::

1. Resim etiketini girip **Oluştur**' u seçin.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/add-tag.png" alt-text="Etiketin nasıl ekleneceğini gösteren ekran görüntüsü.":::

## <a name="domain-and-image-allowlist"></a>Etki alanı ve görüntü izin

Etki alanı allowlist öğesine eklenen bir uygulama, Fortanix gizli bilgi Işlem yöneticisi 'nden bir TLS sertifikası alır. Benzer şekilde, bir uygulama dönüştürülen görüntüden çalıştığında Fortanix gizli bilgi Işlem yöneticisi ile iletişim kurmaya çalışır. Uygulama daha sonra bir TLS sertifikası ister.

Soldaki **Görevler** sekmesine geçin ve bekleyen istekleri etki alanı ve görüntüye izin verecek şekilde onaylayın.

## <a name="enroll-compute-node-agent-in-azure"></a>Azure 'da işlem düğümü aracısını kaydetme

### <a name="generate-and-copy-join-token"></a>JOIN belirteci oluştur ve Kopyala

Fortanix gizli bilgi Işlem yöneticisinde bir belirteç oluşturacaksınız. Bu belirteç, Azure 'daki bir işlem düğümünün kendi kimliğini doğrulamasına izin verir. Bu belirteci Azure sanal makinenize sağlamanız gerekir.

1. Yönetim konsolunda **+ düğüm kaydet** düğmesini seçin.
1. JOIN belirtecini oluşturmak için **belirteç oluştur** ' u seçin. Belirteci kopyalayın.

### <a name="enroll-nodes-into-fortanix-node-agent-in-azure-marketplace"></a>Azure Marketi 'nde düğümleri Fortanx düğüm aracısına kaydetme

Bir Fortanx düğüm Aracısı oluşturmak, Azure Kaynak grubunuza bir sanal makine, ağ arabirimi, sanal ağ, ağ güvenlik grubu ve genel IP adresi dağıtır. Azure aboneliğiniz, sanal makine için saatlik olarak faturalandırılır. Bir Fortanx düğüm Aracısı oluşturmadan önce DCsv2 serisi için Azure [sanal makine fiyatlandırma sayfasını](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) gözden geçirin. Kullanımda olmadığında Azure kaynaklarını silin.

1. [Azure Marketi](https://azuremarketplace.microsoft.com/marketplace/) ' ne gidin ve Azure kimlik bilgilerinizle oturum açın.
1. Arama çubuğuna **Fortanix gizli bilgi Işlem düğüm Aracısı** yazın. Teklifin giriş sayfasına gitmek için **Fortanix gizli bilgi Işlem düğüm Aracısı** adlı arama kutusunda görünen uygulamayı seçin.

   ![Market 'te ara](media/how-to-fortanix-confidential-computing-manager-node-agent/search-fortanix-marketplace.png)
1. **Şimdi al**' ı seçin, gerekirse bilgilerinizi girin ve **devam**' ı seçin. Azure portal yönlendirilirsiniz.
1. Fortanix gizli bilgi Işlem düğüm Aracısı dağıtım sayfasını girmek için **Oluştur** ' u seçin.
1. Bu sayfada, bir sanal makineyi dağıtmak için bilgi girersiniz. Özellikle, bu VM, Azure 'dan Fortanx düğüm Aracısı yazılımının yüklü olduğu DCsv2-Series Intel SGX özellikli bir sanal makinedir. Düğüm Aracısı, dönüştürülmüş resminizin Azure 'da Intel SGX düğümlerinde güvenli şekilde çalışmasına izin verir.  Sanal makineyi ve ilişkili kaynakları dağıtmak istediğiniz **aboneliği** ve **kaynak grubunu** seçin.

   > [!NOTE]
   > Azure 'da DCsv2-Series sanal makineler dağıtımı sırasında kısıtlamalar vardır. Ek çekirdek için kota istemeniz gerekebilir. Daha fazla bilgi için [Azure VM 'lerinde gizli bilgi işlem çözümleri](./virtual-machine-solutions.md) hakkında bilgi edinin.

1. Kullanılabilir bir bölge seçin.
1. **Düğüm adı** alanına sanal makineniz için bir ad girin.
1. Sanal makinede kimlik doğrulaması için bir Kullanıcı adı ve parola (veya SSH anahtarı) girin.
1. Varsayılan işletim sistemi disk boyutunu 200 olarak bırakın ve bir VM boyutu seçin (Standard_DC4s_v2 Bu öğretici için yeterli olacaktır).
1. Daha önce, **JOIN belirtecinde** oluşturulan belirteci yapıştırın.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/deploy-fortanix-node-agent-protocol.png" alt-text="Bir kaynağın nasıl dağıtılacağını gösteren ekran görüntüsü.":::

1. **Gözden geçir + Oluştur**’u seçin. Doğrulamanın başarılı olduğundan emin olun ve ardından **Oluştur**' u seçin. Tüm kaynaklar dağıtılırken, işlem düğümü artık Fortanix gizli bilgi Işlem yöneticisi 'ne kaydedilir.

## <a name="run-the-application-image-on-the-compute-node"></a>Uygulama görüntüsünü işlem düğümünde Çalıştır

Aşağıdaki komutu yürüterek uygulamayı çalıştırın. Düğüm IP 'sini, bağlantı noktasını ve dönüştürülmüş görüntü adını, belirli uygulamanıza giriş olarak değiştirdiğinizden emin olun.

Bu öğreticide, yürütülecek komut şu şekilde olur:

```bash
    sudo docker run `
        --device /dev/isgx:/dev/isgx `
        --device /dev/gsgx:/dev/gsgx `
        -v /var/run/aesmd/aesm.socket:/var/run/aesmd/aesm.socket `
        -e NODE_AGENT_BASE_URL=http://52.152.206.164:9092/v1/ fortanix-private/python-flask-sgx
```

Konum:

- *52.152.206.164* , düğüm Aracısı ana bilgisayar IP 'si
- *9092* , düğüm aracısının dinlediği bağlantı noktasıdır
- *fortanx-Private/Python-Flask-SGX* , Fortanix gizli bilgi Işlem Yöneticisi Web portalındaki **görüntüler** tablosunda **görüntü adı** sütununun altındaki görüntüler sekmesinde bulunan dönüştürülmüş uygulamadır.

## <a name="verify-and-monitor-the-running-application"></a>Çalışan uygulamayı doğrulama ve izleme

1. [Fortanix gizli bilgi Işlem yöneticisine](https://ccm.fortanix.com/console)dönün.
1. Düğümü kaydettiğiniz **hesabın** içinde çalıştığınızdan emin olun.
1. Sol gezinti bölmesindeki en üstteki simgeyi seçerek **Yönetim konsoluna** gidin.
1. **Uygulama** sekmesini seçin.
1. İlişkili bir işlem düğümü olan çalışan bir uygulama olduğunu doğrulayın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu, sanal makineyi ve ilişkili kaynakları silebilirsiniz. Kaynak grubunun silinmesi, dönüştürülmüş görüntünüz ile ilişkili düğümlerin kaydını kaldırır.

Sanal makine için kaynak grubunu seçin ve **Sil**' i seçin. Kaynakları silmeyi tamamlayacak kaynak grubunun adını onaylayın.

Oluşturduğunuz Fortanix gizli bilgi Işlem yöneticisi hesabını silmek için Fortanix gizli bilgi Işlem Yöneticisi ' nde [hesaplar sayfasına](https://ccm.fortanix.com/accounts) gidin. Silmek istediğiniz hesabın üzerine gelin. Sağ üst köşedeki dikey siyah noktaları seçin ve **Hesabı Sil**' i seçin.

:::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/delete-account.png" alt-text="Hesabın nasıl silineceğini gösteren ekran görüntüsü.":::

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, uygulama görüntünüzü gizli bir bilgi işlem sanal makinesinin en üstünde çalışacak şekilde dönüştürmek için fortanx Araçları öğesini kullandınız. Azure 'daki sanal makinelerin gizli bilgi işlem hakkında daha fazla bilgi için bkz. [sanal makinelerdeki çözümler](virtual-machine-solutions.md).

Azure 'un gizli bilgi işlem teklifleri hakkında daha fazla bilgi edinmek için bkz. [Azure gizli bilgi işlem Özeti](overview.md).

Azure 'da [Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) ve [skoni](https://sconedocs.github.io)gibi diğer üçüncü taraf teklifleri kullanarak benzer görevleri tamamlamayı öğrenin.