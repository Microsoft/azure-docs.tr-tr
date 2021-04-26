---
title: Micro Focus Enterprise Server 5,0 'i AKS 'e dağıtma | Microsoft Docs
description: Azure sanal makinelerinde (VM) mikro odak geliştirme ve test ortamını kullanarak IBM z/OS ana bilgisayar iş yüklerinizi yeniden barındırın.
services: virtual-machines
documentationcenter: ''
author: maggsl
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 06/29/2020
tags: ''
keywords: ''
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 58ba530e434a9dc141ba8cb98120f6325eb06f7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950482"
---
# <a name="deploy-micro-focus-enterprise-server-50-to-aks"></a>Micro Focus Enterprise Server 5,0 'i AKS 'e dağıtma

Başka bir [makalede](./run-enterprise-server-container.md), Micro Focus Enterprise Server 5,0 'ı bir Docker kapsayıcısında çalıştırmaya yönelik adımları anahatlıyorum. Bunu bir sonraki adımda, bir adım daha nasıl ele alıp Azure Kubernetes Service (AKS) için oluşturduğunuz Docker görüntüsünü nasıl dağıtacağınızı göstermek istiyorum.

Azure Kubernetes hizmeti, Kubernetes tabanlı yönetilen bir Orchestration hizmetidir. Bir kapsayıcı ana bilgisayar kümesi genelinde Docker kapsayıcılarını (ve diğer kapsayıcı tabanlı uygulamaları) dağıtmanıza, ölçeklendirmenize ve yönetmenize olanak sağlar.

Bu, üç adımlı bir işlemdir. Şunları yapmanız gerekir:

1.  Docker görüntünüzü depolamak için bir Azure Container Registry oluşturun.

2.  Docker görüntüsünü çalıştırmak için bir Azure Kubernetes kümesi oluşturun.

3.  Uygulamayı çalıştırın.

Bu, bulut platformunun gerçek avantajlarından yararlanarak Azure 'daki ana bilgisayar modernleştirme iş yüklerinizi genişleme (ve Ölçeklendirebilmenizi) sağlar.

Geldiğinizde? Başlayalım!

## <a name="create-the-azure-container-registry"></a>Azure Container Registry oluşturun

Azure portal, sol üst köşedeki **kaynak oluştur** ' u seçin. Market panosundan **kapsayıcılar '** ı seçin ve **Container Registry**. Bu sizi, **kayıt defteri adı**, **Azure aboneliği**, **kaynak grubu** ve **konum**' u doldurmanız gereken **kapsayıcı kayıt defteri oluştur** bölmesine götürür. **Kayıt defteri adının** çözümlenmesi gerekir, bu nedenle benzersiz olmalıdır. Önceki blog gönderisinin ve ilgili **konumun** aynısını kullandığınız **kaynak grubunu** seçin. **SKU** için **Yönetici Kullanıcı** ve **temel** için **Etkinleştir** ' i seçin. Her şeyi doldurduktan sonra **Oluştur**' u seçin.

![Kapsayıcı kayıt defteri arabirimi oluştur](media/deploy-image-1.png)

Kayıt defteri dağıtıldıktan sonra **Kaynağa Git**' i seçin. Bu, sizi Azure Container Registry ana dikey penceresine götürür. Burada iyi bir özellik **hızlı başlangıç** menü seçeneğidir. Bunu seçtiğinizde, kayıt defterine ve kayıt defterinden görüntü göndermek ve çekmek için yapılması gerekenler hakkında yönergeler görürsünüz. Şimdi şunları yapalım:

1.  **Docker 'ı yükler** : zaten yapıldığından, bu konuda endişelenmeniz gerekmez.

2.  **"Hello – World" temel görüntüsünü çalıştırın** , bu gerekli değildir, ancak deneyin.

3.  **Kapsayıcı kayıt defterinizde oturum açın** – bunu sanal MAKINEDEN (VM) yapmanız gerekir. Komutu Pano veya Not defteri 'ne kopyalayın.

    Oluşturduğum kayıt defteri için komut şu olur: **Docker login acrmf50.azurecr.io**

4.  **Kayıt defterinize gönderim** – bunu mikro odak görüntüsü IÇIN, VM 'de oturum açtıktan sonra yapmanız gerekir.

5.  **Kayıt defterinizden çekme** – Bu izlenecek yol için uygun değildir, ancak başka bir Docker görüntüsü çalıştırmanız gerektiğinde bilmeniz iyi olur.

Portalından çıkmadan önce, oturum açmak için kayıt defteri kimlik bilgilerini almanız gerekir. **Hızlı başlangıç** dikey penceresinden çıkın ve kayıt defteri menüsünden **erişim tuşları** ' nı seçin. **Kullanıcı adını** ve **parolalardan** birini (iki tane) Pano veya Not defteri 'ne kopyalayın. Oturum açmak için daha sonra ihtiyacınız olacak.

Ne yapılması gerektiğini bildiğinize göre, VM 'de oturum açın.

## <a name="rdp-to-the-virtual-machine-you-used-to-create-the-docker-image"></a>Docker görüntüsünü oluşturmak için kullandığınız sanal makineye RDP

Docker görüntüsünü zaten bir Windows 2016 sunucusunda oluşturmuş olduğundan, bu VM 'de oturum açmanız gerekir. Bu VM 'den, görüntüyü yeni oluşturduğunuz Azure Container Registry gönderebilirsiniz. Azure portal VM 'ye gidin ve ardından **genel bakış** ' ı ve sonra **Bağlan**' ı seçin. Bu, sizi VM 'ye Uzak Masaüstü Protokolü (RDP) aracılığıyla bağlar. VM 'yi oluştururken kimlik bilgilerini kullanmanız gerekir.

## <a name="log-in-and-push-the-image-to-the-registry"></a>Oturum açın ve görüntüyü kayıt defterine gönderin

Oturum açtıktan sonra, bir komut istemi açın ve aşağıdaki Docker komutlarını başlatın:

-   **Docker görüntüleri** : Bu, sanal makinede yüklü olan tüm görüntülerin bir listesini gösterir. Üzerinde çalıştığınız bir hizmet olduğundan, **mikro odak/es-acctdemo** ' i de göz önünde bulabilirsiniz.

-   **Docker login acrmf50.azurecr.io** – burada doğru biçim *Docker oturum açma \<registry name\>* biçimidir. Kayıt defterini oluştururken kullandığınız adı değiştirin.

    -   Azure portal kopyaladığınız **Kullanıcı adı** ve **parolaya** ihtiyacınız olacak. Aşağıdaki görüntüye benzer bir sonuçla karşılaşmanız gerekir.

    ![Yönetici komut Isteminin ekran görüntüsü](media/deploy-image-2.png)

-   **Docker Tag mikro Focus/es-acctdemo acrmf50.azurecr.io/es-acctdemo** – bu, deponun adı ile ilgili görüntüyü Etiketler. **Not**: ad \<microfocus/es-acctdemo\> çalışmazsa, tam görüntü kimliğini kullanmayı deneyin. Komutu çalıştırdıktan sonra **Docker görüntüleri – No-TRUNC** yazın. Sonraki görüntüde olduğu gibi bir şey görmeniz gerekir. Görüntünün düzgün etiketlendiğine dikkat edin.

    ![Yönetici komut Istemi ekranını seçin](media/deploy-image-3.png)

-   **Docker push acrmf50.azurecr.io/es-acctdemo** – bu, deponuzdaki gerçek gönderimi kapatır. Boyut 15 GB olduğundan, çalışması birkaç dakika sürer. Her şey doğru olursa, aşağıdaki görüntüye benzer bir şey görmeniz gerekir.

    ![Yönetici komut Istemi ekranı](media/deploy-image-4.png)

Artık, özel olarak **depoya** Azure Portal geri dönün. **Deponun** menüsünde **depolar**' ı seçin ve ardından **es-acctdemo** ' i görmeniz gerekir. Şimdi AKS kümesini oluşturun.

## <a name="create-the-azure-kubernetes-aks-cluster"></a>Azure Kubernetes (AKS) kümesi oluşturma

Azure portal, **Market** menüsünden **kaynak oluştur** ve ardından **kapsayıcılar/Kubernetes hizmeti** ' ni seçin. Sonra, **Kubernetes kümesi oluştur** dikey penceresini doldurmanız gerekir. Kümeyi, kullanmakta olduğunuz bölgede ve kaynak grubunda kaydettiğinizden emin olun. Yalnızca 1 olması gereken **düğüm sayısı** dışında varsayılan geri kalanı kabul edebilirsiniz. İşiniz bittiğinde, **gözden geçir + oluştur**' u seçin.

![Kubernetes kümesi ekranı oluşturma](media/deploy-image-5.png)

İşlem tamamlandığında dağıtım, **Kubernetes hizmet** yapılarını dikey pencerede seçtiğiniz **kaynak grubuna** yerleştirir. Ancak, gerçek kümenin dağıtım sırasında kendi kaynak grubu oluşturulur. Sol taraftaki menüden **kaynak grupları** ' nı seçerseniz, adlandırma kuralına göre bulabilirsiniz. İşte bir mayın görüntüsü: listede en son bir yer.

![Kaynak grupları ekran görüntüsü](media/deploy-image-6.png)

**Görüntüyü çalıştırma**

Şimdi görüntüyü çekmeniz ve AKS 'de çalıştırmanız zaman atalım. Bunu Azure portal yapmanın en kolay yolu Cloud Shell kullanmaktır. Azure portal simgenin sağ üst köşesinde bulabilirsiniz. Bu anlatım için bash kabuğunu kullandığımı unutmayın.

![Cloud Shell simgesinin ekran görüntüsü](media/deploy-image-7.png)

Kabuk yüklendikten sonra aşağıdaki komutu yazın:

**kubectl Run es-acctdemo--Image acrmf50.azurecr.io/es-acctdemo--Port = 9040**

Bu, görüntüyü **Acrmf50.azurecr.io** deposundan çeker ve aks 'e yükler. Daha sonra görüntüyü 9040 açık bağlantı noktası ile çalıştırır. Bu işlem, Docker görüntünüz için açtığınız bağlantı noktasıdır. Enterprise Server 'a erişmeniz gerekir.

Kubernetes, dağıtımın oluşturulduğu bir iletiyle yanıt vermelidir.

![Dağıtım iletisinin ekran görüntüsü](media/deploy-image-8.jpg)

Kapsayıcının gerçekten çalışıp çalışmadığını görmek için şunu yazın: **kubectl Get Pod**.

Aşağıdaki görüntüde olduğu gibi es-acctdemo çalıştıran bir pod olarak görmeniz gerekir.

![Çalışan Pod olarak ekran görüntüsü es-acctdemo](media/deploy-image-9.png)

Tebrikler! Artık Azure Kubernetes hizmetinde kurumsal sunucu çalıştırıyorsunuz. Bir sonraki makalede, kurumsal sunucu yönetim konsoluna nasıl erişeceğim ve dağıtımınızı ölçeklendirmek için Kubernetes 'ten nasıl yararlanacağınızı göstereceğiz.
