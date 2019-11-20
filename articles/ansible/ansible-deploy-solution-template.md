---
title: Hızlı başlangıç-Azure için anormal çözüm şablonunu CentOS 'a dağıtma
description: Bu hızlı başlangıçta, Azure 'da barındırılan bir CentOS sanal makinesine ve Azure ile çalışmak üzere yapılandırılmış araçlarla birlikte, anormal çözüm şablonunu dağıtmayı öğrenin.
keywords: anerişilebilir, Azure, DevOps, çözüm şablonu, sanal makine, Azure kaynakları için Yönetilen kimlikler, CentOS, Red Hat
ms.topic: quickstart
ms.date: 04/30/2019
ms.openlocfilehash: ebd01411ff27b0e95be21ef147098a90b6f62742
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156319"
---
# <a name="quickstart-deploy-the-ansible-solution-template-for-azure-to-centos"></a>Hızlı başlangıç: Azure 'da CentOS 'a yönelik anormal çözüm şablonu dağıtma

Azure için anormal çözüm şablonu, bir CentOS sanal makinesi üzerinde anormal bir örnek yapılandırmak üzere tasarlanmıştır ve Azure ile birlikte çalışmak üzere yapılandırılmış bir araç paketidir. Araçlar şunları içerir:

- **Azure Için anormal modüller** -Azure 'A [yönelik anormal modüller](./ansible-matrix.md) , altyapınızı Azure 'da oluşturmanıza ve yönetmenize olanak tanıyan bir modül paketidir. Bu modüllerin en son sürümü varsayılan olarak dağıtılır. Ancak, çözüm-şablon dağıtım işlemi sırasında, ortamınız için uygun bir sürüm numarası belirtebilirsiniz.
- **Azure komut satırı arabirimi (CLI) 2,0** -azure [CLI 2,0](/cli/azure/?view=azure-cli-latest) , Azure kaynaklarını yönetmeye yönelik platformlar arası bir komut satırı deneyimidir. 
- **Azure kaynakları için Yönetilen kimlikler** - [Azure kaynakları için Yönetilen kimlikler](/azure/active-directory/managed-identities-azure-resources/overview) özelliği, bulut uygulaması kimlik bilgilerinin güvenliğini sağlama sorununu ele alınmaktadır.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="deploy-the-ansible-solution-template"></a>Anerişilebilir çözüm şablonunu dağıtma

1. [Azure Marketi 'Nde Anerişilebilir çözüm şablonuna](https://azuremarketplace.microsoft.com/en-%20%20us/marketplace/apps/azure-oss.ansible?tab=Overview)gidin.

1. **Şimdi al**' ı seçin.

1. Kullanım koşullarını, gizlilik Ilkesini ve Azure Marketi koşullarının kullanımını ayrıntılarıyla gösteren bir pencere görüntülenir. **Devam**'ı seçin.

1. Azure portal görünür ve çözüm şablonunu açıklayan erişilebilir sayfayı görüntüler. **Oluştur**'u seçin.

1. Mümkün olan **Oluştur** sayfasında, birkaç sekme görürsünüz. **Temel bilgiler** sekmesinde, gerekli bilgileri girin:

   - **Ad** -daha erişilebilir örneğinizin adını belirtin. Tanıtım amacıyla, `ansiblehost` adı kullanılır.
   - **Kullanıcı adı:** -erişilebilir örneğe erişimi olacak Kullanıcı adını belirtin. Tanıtım amacıyla, `ansibleuser` adı kullanılır.
   - **Kimlik doğrulama türü:** - **parola** ya da **SSH ortak anahtarı**seçin. Tanıtım amacıyla, **SSH ortak anahtarı** seçilidir.
   - **Parola** ve **Parolayı Onayla** - **kimlik doğrulama türü**için **parola** ' yı seçerseniz, bu değerler için parolanızı girin.
   - **SSH ortak anahtarı** - **kimlik doğrulama türü**için **SSH ortak anahtarı** ' nı seçerseniz, `ssh-rsa`başlayarak tek satır biçiminde RSA ortak anahtarınızı girin.
   - **Abonelik** -açılan listeden Azure aboneliğinizi seçin.
   - **Kaynak grubu** -açılan listeden var olan bir kaynak grubunu seçin veya yeni **Oluştur** ' u seçip yeni kaynak grubu için bir ad belirtin. Tanıtım amacıyla, `ansiblerg` adlı yeni bir kaynak grubu kullanılır.
   - **Konum** -açılır listeden senaryonuz için uygun konumu seçin.

     ![Daha erişilebilir temel ayarlar için Azure portal sekmesi](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-1.png)

1. **Tamam**’ı seçin.

1. **Ek ayarlar** sekmesinde, gerekli bilgileri girin:

   - **Boyut** -Azure Portal varsayılan olarak standart bir boyut olur. Belirli senaryonuza uyum sağlayan farklı bir boyut belirtmek için, farklı boyutlardaki bir liste göstermek için oku seçin.
   - **VM disk türü** - **SSD** (Premium Solid-State Drive) veya **HDD** (sabit disk sürücüsü) seçeneğini belirleyin. Demo amaçlarıyla, **SSD** 'nin performans avantajları için seçilir. Bu tür disk depolama alanı hakkında daha fazla bilgi için aşağıdaki makalelere bakın:
       - [VM’ler için yüksek performanslı Premium Depolama ve yönetilen diskler](/azure/virtual-machines/windows/premium-storage)
       - [Azure sanal makine iş yükleri için yönetilen diskleri Standart SSD](/azure/virtual-machines/windows/disks-standard-ssd)
   - **Genel IP adresi** -sanal makine dışından sanal makine ile iletişim kurmak istiyorsanız bu ayarı belirtin. Varsayılan değer `ansible-pip`adı olan yeni bir genel IP adresidir. Farklı bir IP adresi belirtmek için, söz konusu IP adresinin adı, SKU 'SU ve atama gibi öznitelikleri belirten oku seçin. 
   - **Etki alanı adı etiketi** -sanal makinenin herkese açık etki alanı adını girin. Ad benzersiz olmalıdır ve adlandırma gereksinimlerine uymalıdır. Sanal makine için bir ad belirtme hakkında daha fazla bilgi için bkz. [Azure kaynakları Için adlandırma kuralları](/azure/architecture/best-practices/resource-naming).
   - **Anormal sürüm** -en son sürümü dağıtmak için bir sürüm numarası ya da değer belirtin `latest`. Kullanılabilir sürümler hakkında daha fazla bilgi için, erişilebilir **Sürüm** ' ün yanındaki bilgi simgesini seçin.

     ![Daha erişilebilir ek ayarlar için Azure portal sekmesi](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-2.png)

1. **Tamam**’ı seçin.

1. Mümkün olan **tümleştirme ayarları** sekmesinde, kimlik doğrulama türünü belirtin. Azure kaynaklarının güvenliğini sağlama hakkında daha fazla bilgi için bkz. [Azure kaynakları için Yönetilen kimlikler nelerdir?](/azure/active-directory/managed-identities-azure-resources/overview).

    ![Anormal tümleştirme ayarları için Azure portal sekmesi](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-3.png)

1. **Tamam**’ı seçin.

1. **Özet** sayfası, doğrulama işlemini gösteren ve anormal dağıtım için belirtilen ölçütleri listelerken görüntülenir. Sekmenin alt kısmındaki bir bağlantı, desteklenen Azure dilleri ve platformları ile kullanım için **şablon ve parametreleri indirmelerini** sağlar. 

     ![Anormal Özet sekmesi için Azure portal sekmesi](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-4.png)

1. **Tamam**’ı seçin.

1. **Oluştur** sekmesi göründüğünde, bir daha erişilebilir dağıtmak için **Tamam** ' ı seçin.

1. Anormal dağıtımı izlemek için portal sayfasının üst kısmındaki **Bildirimler** simgesini seçin. Dağıtım tamamlandıktan sonra **kaynak grubuna git**' i seçin. 

     ![Anormal Özet sekmesi için Azure portal sekmesi](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-complete.png)

1. Kaynak grubu sayfasında, Anerişilebilir ana bilgisayarın IP adresini alın ve Azure kaynaklarınızı yönetmek için oturum açın.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"] 
> [Hızlı başlangıç: Azure 'da bir Linux sanal makinesini kullanarak yapılandırma](/azure/virtual-machines/linux/ansible-create-vm)