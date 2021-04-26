---
title: Azure VMware çözümünüz özel bulutunuzda GitHub Enterprise Server 'ı ayarlama
description: Azure VMware Çözüm özel bulutunuzda GitHub Enterprise Server 'ı ayarlamayı öğrenin.
ms.topic: how-to
ms.date: 02/11/2021
ms.openlocfilehash: 0ff7ab87d7401cd3faaecf149fb1b07be3f8db42
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862948"
---
# <a name="set-up-github-enterprise-server-on-your-azure-vmware-solution-private-cloud"></a>Azure VMware çözümünüz özel bulutunuzda GitHub Enterprise Server 'ı ayarlama

Bu makalede, Azure VMware Çözüm özel bulutunuzda "Şirket içi" [sürümü olan GitHub](https://github.com/)Enterprise Server 'ı ayarlama adımlarını adım adım inceleyeceğiz. Ele alacağız senaryo, GitHub eylemleri üzerinde dakikada 25 ' e kadar iş yürüten 3.000 adede kadar geliştirici sunan bir GitHub kurumsal sunucu örneğidir. GitHub eylemleri gibi *Önizleme* özelliklerinin (yazma sırasında) kurulumunu içerir. Kurulumu belirli gereksinimlerinize göre özelleştirmek için, [VMware 'de GitHub Enterprise Server yükleme](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations)bölümünde listelenen gereksinimleri gözden geçirin.

## <a name="before-you-begin"></a>Başlamadan önce

GitHub Enterprise Server geçerli bir lisans anahtarı gerektirir. [Deneme lisansı](https://enterprise.github.com/trial)için kaydolabilirsiniz. GitHub Enterprise Server 'ın yeteneklerini bir tümleştirme aracılığıyla genişletmeyi düşünüyorsanız, ücretsiz beş özellikli bir geliştirici lisansı için uygun olabilir. Bu lisans için [GitHub Iş ortağı programı](https://partner.github.com/)aracılığıyla uygulayın.

## <a name="installing-github-enterprise-server-on-vmware"></a>VMware 'de GitHub Enterprise Server 'ı yükleme

VMware ESXi/vSphere (OVA) için [GitHub Enterprise Server 'ın geçerli sürümünü](https://enterprise.github.com/releases/2.19.0/download) indirin ve indirdiğiniz [ova şablonunu dağıtın](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html) .

:::image type="content" source="media/github-enterprise-server/github-options.png" alt-text="GitHub 'ı şirket içinde veya bulutta çalıştırmayı seçin.":::  

:::image type="content" source="media/github-enterprise-server/deploy-ova-template.png" alt-text="OVA şablonunu dağıtın.":::  

Yeni sanal makineniz için GitHubEnterpriseServer gibi tanınabilir bir ad sağlayın. Örnek yükseltildiğinde, bu ayrıntılar eski hale geldiğinde, sürüm ayrıntılarını VM adına eklemeniz gerekmez. Şimdilik tüm Varsayılanları seçin (bu ayrıntıları kısa süre içinde düzenleyeceğiz) ve OVA 'nın içeri aktarılmasını bekleyin.

İçeri aktarıldıktan sonra, gereksinimlerinize göre [donanım yapılandırmasını ayarlayın](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#creating-the-github-enterprise-server-instance) . Örnek Senaryomuzda aşağıdaki yapılandırmaya ihtiyacımız olacak.

| Kaynak | Standart Kurulum | Standart Kurulum + "Beta Özellikleri" (Eylemler) |
| --- | --- | --- |
| Sanal çekirdek | 4 | 8 |
| Bellek | 32 GB | 61 GB |
| Bağlı depolama alanı | 250 GB | 300 GB |
| Kök depolama | 200 GB | 200 GB |

Ancak gereksinimleriniz farklılık gösterebilir. [VMware 'de GitHub Enterprise Server yükleme](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations)ile ilgili donanım konuları hakkında rehberlik bölümüne bakın. Ayrıca, durumunuza göre donanım yapılandırmasını özelleştirmek için [VMware IÇIN CPU veya bellek kaynakları ekleme](https://docs.github.com/en/enterprise/admin/enterprise-management/increasing-cpu-or-memory-resources#adding-cpu-or-memory-resources-for-vmware) bölümüne bakın.

## <a name="configuring-the-github-enterprise-server-instance"></a>GitHub Enterprise Server örneğini yapılandırma

:::image type="content" source="media/github-enterprise-server/install-github-enterprise.png" alt-text="GitHub Enterprise 'ı yükler.":::  

Yeni sağlanan sanal makine (VM) açık olduktan sonra, [tarayıcınızı kullanarak yapılandırın](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#configuring-the-github-enterprise-server-instance). Lisans dosyanızı karşıya yüklemeniz ve bir yönetim konsolu parolası ayarlamanız gerekir. Bu parolayı güvenli bir yerde yazdığınızdan emin olun.

:::image type="content" source="media/github-enterprise-server/ssh-access.png" alt-text="Yönetim Kabuğu SSH aracılığıyla erişin.":::    

En azından aşağıdaki adımları gerçekleştirmeniz önerilir:

1. Yönetim [kabuğu 'NA SSH aracılığıyla erişebilmek](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh)için genel bir SSH anahtarını yönetim konsoluna yükleyin. 

2. Güvenilen bir sertifika yetkilisi tarafından imzalanan bir sertifika kullanabilmeniz için [örneğiniz ÜZERINDE TLS yapılandırın](https://docs.github.com/en/enterprise/admin/configuration/configuring-tls) .

:::image type="content" source="media/github-enterprise-server/configuring-your-instance.png" alt-text="Örneğinizi yapılandırma.":::

Ayarlarınızı uygulayın.  Örnek yeniden başlatıldıktan sonra, bir sonraki adımla devam ederek **GitHub işlemleri Için blob depolamayı** yapılandırabilirsiniz.

:::image type="content" source="media/github-enterprise-server/create-admin-account.png" alt-text="Yönetici hesabınızı oluşturun.":::

Örnek yeniden başlatıldıktan sonra örnekte yeni bir yönetici hesabı oluşturabilirsiniz. Bu kullanıcının parolasını da bir yere göz önünde olduğunuzdan emin olun.

### <a name="other-configuration-steps"></a>Diğer yapılandırma adımları

Örnek üretim kullanımı için, aşağıdaki isteğe bağlı kurulum adımları önerilir:

1. Koruma için [yüksek kullanılabilirliği](https://help.github.com/enterprise/admin/guides/installation/configuring-github-enterprise-for-high-availability/) şu şekilde yapılandırın:

    - Yazılım kilitlenmeleri (işletim sistemi veya uygulama düzeyi)
    - Donanım arızaları (depolama, CPU, RAM vb.)
    - Sanallaştırma Ana bilgisayar sistemi sorunları
    - Mantıksal veya fiziksel olarak ayrılmış ağ

2. Birincil örnekten ayrı kullanılabilirlik bölümünde barındırılan, olağanüstü durum kurtarma için sürümlü anlık görüntüler sağlayan [Backup-Utilities](https://github.com/github/backup-utils)öğesini [yapılandırın](https://docs.github.com/en/enterprise/admin/configuration/configuring-backups-on-your-appliance) .
3. Siteler arası komut dosyası ve diğer ilgili güvenlik açıklarını azaltmak için, geçerli bir TLS sertifikası kullanarak alt [etki alanı yalıtımı ayarlayın](https://docs.github.com/en/enterprise/admin/configuration/enabling-subdomain-isolation).

## <a name="configuring-blob-storage-for-github-actions"></a>GitHub eylemleri için blob depolamayı yapılandırma

> [!NOTE]
> GitHub eylemleri, [GitHub Enterprise Server sürüm 2,22 ' de şu anda sınırlı bir beta olarak sunulmaktadır](https://docs.github.com/en/enterprise/admin/github-actions).

GitHub Enterprise Server 'da GitHub eylemlerini etkinleştirmek için dış BLOB depolama alanı gereklidir (Şu anda bir "Beta" özelliği olarak kullanılabilir). Bu dış BLOB depolama, yapıtları ve günlükleri depolamak için eylemler tarafından kullanılır. GitHub Enterprise Server 'daki eylemler, [Azure Blob depolamayı bir depolama sağlayıcısı](https://docs.github.com/en/enterprise/admin/github-actions/enabling-github-actions-and-configuring-storage#about-external-storage-requirements) (ve diğerlerinin bazıları) olarak destekler. Bu nedenle, BlobStorage [depolama hesabı türüne](../storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-storage-accounts) sahip yeni bir Azure depolama hesabı sağlayacağız:

:::image type="content" source="media/github-enterprise-server/storage-account.png" alt-text="Azure Blob depolama hesabı sağlayın.":::

Yeni BlobStorage kaynağının dağıtımı tamamlandıktan sonra, bağlantı dizesini kopyalayın ve (erişim anahtarları altında kullanılabilir) bir yere göz önünde yapın. Kısa süre içinde bu dizeye ihtiyacımız olacak.

## <a name="setting-up-the-github-actions-runner"></a>GitHub eylemleri Çalıştırıcısı 'nı ayarlama

> [!NOTE]
> GitHub eylemleri, [GitHub Enterprise Server sürüm 2,22 ' de şu anda sınırlı bir beta olarak sunulmaktadır](https://docs.github.com/en/enterprise/admin/github-actions).

Bu noktada, bir yönetici hesabıyla oluşturulmuş bir GitHub Enterprise Server örneğine sahip olmanız gerekir. Ayrıca GitHub eylemlerinin Kalıcılık için kullanacağı dış BLOB depolama alanı da olmalıdır.

Şimdi GitHub eylemlerinin çalışması için bir yerde oluşturalım; Yine de Azure VMware çözümünü kullanacağız.

İlk olarak, kümede yeni bir VM sağlayalim. [En son Ubuntu Server sürümünde](http://releases.ubuntu.com/20.04.1/)VM 'imizi temel alacağız.

:::image type="content" source="media/github-enterprise-server/provision-new-vm.png" alt-text="Yeni bir sanal makine sağlayın.":::

:::image type="content" source="media/github-enterprise-server/provision-new-vm-2.png" alt-text="Yeni bir VM sağlama adımı 2.":::

VM oluşturulduktan sonra, BT 'yi kapatıp SSH aracılığıyla bağlayın.

Ardından, bir GitHub eylemleri iş akışından bir işi çalıştıran [Eylemler Çalıştırıcısı](https://github.com/actions/runner) uygulamasını yükler. [Yayınlar sayfasından](https://github.com/actions/runner/releases) ya da aşağıdaki hızlı betiği çalıştırarak, eylemler Çalıştırıcısı 'nın en güncel Linux x64 sürümünü belirleyip indirin. Bu betik, sanal makinenizde hem kıvrımlı hem de [JQ](https://stedolan.github.io/jq/) bulunmasını gerektirir.

`LATEST\_RELEASE\_ASSET\_URL=$( curl https://api.github.com/repos/actions/runner/releases/latest | \`

`  jq -r '.assets | .[] | select(.name | match("actions-runner-linux-arm64")) | .url' )`

`DOWNLOAD\_URL=$( curl $LATEST\_RELEASE\_ASSET\_URL | \`

`  jq -r '.browser\_download\_url' )`

`curl -OL $DOWNLOAD\_URL`

Artık VM 'niz üzerinde yerel olarak bir dosyanız olmalıdır; eylemler-Çalıştırıcısı-Linux-arm64- \* . tar. gz. Bu tarbol 'yi yerel olarak ayıklayın:

`tar xzf actions-runner-linux-arm64-\*.tar.gz`

Bu ayıklama, bir ve betiği de dahil olmak üzere birkaç dosyayı yerel olarak, kısa bir süre içinde geri döneceğiz `config.sh` `run.sh` .

## <a name="enabling-github-actions"></a>GitHub eylemlerini etkinleştirme

> [!NOTE]
> GitHub eylemleri, [GitHub Enterprise Server sürüm 2,22 ' de şu anda sınırlı bir beta olarak sunulmaktadır](https://docs.github.com/en/enterprise/admin/github-actions).

Neredeyse orada! GitHub Enterprise Server örneğinde GitHub eylemlerini yapılandırıp etkinleştirelim. [GitHub Enterprise Server örneğinin Yönetim Kabuğu SSH üzerinden erişmemiz](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh)ve ardından aşağıdaki komutları çalıştırmanız gerekir:

`# set an environment variable containing your Blob storage connection string`

`export CONNECTION\_STRING="<your connection string from the blob storage step>"`

`# configure actions storage`

`ghe-config secrets.actions.storage.blob-provider azure`

`ghe-config secrets.actions.storage.azure.connection-string "$CONNECTION\_STRING"`

`# apply these settings`

`ghe-config-apply`

`# execute a precheck, this install additional software required by Actions on GitHub Enterprise Server`

`ghe-actions-precheck -p azure -cs "$CONNECTION\_STRING"`

`# enable actions, and re-apply the config`

`ghe-config app.actions.enabled true`

`ghe-config-apply`

Sonraki çalıştırma:

`ghe-actions-check -s blob`

Çıktıyı görmeniz gerekir: "blob depolama sağlıklı".

GitHub eylemleri yapılandırıldığına göre, kullanıcılarınız için etkinleştirin. GitHub Enterprise Server Örneğiniz için yönetici olarak oturum açın ve ![ roket simgesini seçin.](media/github-enterprise-server/rocket-icon.png) herhangi bir sayfanın sağ üst köşesinde. Sol kenar çubuğunda **Kurumsal Genel Bakış**' ı, ardından **ilkeler**, **Eylemler**' i seçin ve **tüm kuruluşlar için eylemleri etkinleştirme** seçeneğini belirleyin.

Ardından, **kendi şirket içinde barındırılan** çalışma sekmesinden çalıştırıcı 'yı yapılandırın. Açılan listeden **Yeni Ekle** ' yi ve ardından **Yeni Çalıştırıcısı** ' nı seçin.

Bir sonraki sayfada, çalıştırılacak bir komut kümesi sunulur, örneğin, Çalıştırıcısı **yapılandırmak** için komutu kopyalamanız gerekir, örneğin:

`./config.sh --url https://10.1.1.26/enterprises/octo-org --token AAAAAA5RHF34QLYBDCHWLJC7L73MA`

Komutu kopyalayın `config.sh` ve eylemler Çalıştırıcısı (daha önce oluşturulmuş) üzerindeki bir oturuma yapıştırın.

:::image type="content" source="media/github-enterprise-server/actions-runner.png" alt-text="Eylem Çalıştırıcısı.":::

Çalıştırıcısı *çalıştırmak* için Run.sh komutunu kullanın:

:::image type="content" source="media/github-enterprise-server/run-runner.png" alt-text="Çalıştırıcısı 'nı çalıştırın.":::

Bu Çalıştırıcısı kuruluşunuzdaki kuruluşlar için kullanılabilir hale getirmek için kuruluş erişimini düzenleyin:

:::image type="content" source="media/github-enterprise-server/edit-runner-access.png" alt-text="Çalıştırıcısı erişimini düzenleyin.":::

Burada tüm kuruluşlar için kullanılabilir hale getiririz, ancak kuruluşların bir alt kümesiyle ve hatta belirli depolara erişmesini sınırlayabilirsiniz.

## <a name="optional-configuring-github-connect"></a>Seçim GitHub Connect yapılandırılıyor

Bu adım isteğe bağlı olsa da, GitHub.com 'de kullanılabilir açık kaynaklı eylemleri tüketmek için plan yapmanız önerilir. İş akışlarınızda bu yeniden kullanılabilir eylemlere başvurarak başkalarının çalışmasını derlemenize olanak tanır.

GitHub Connect 'i etkinleştirmek için [GitHub Connect kullanarak GitHub.com eylemlerine otomatik erişimi etkinleştirme](https://docs.github.com/en/enterprise/admin/github-actions/enabling-automatic-access-to-githubcom-actions-using-github-connect)bölümündeki adımları izleyin.

GitHub Connect etkinleştirildikten sonra, **iş akışı çalıştırmaları seçeneğinde GitHub.com öğesinden eylemleri kullanacak sunucuyu** seçin.

:::image type="content" source="media/github-enterprise-server/enable-using-actions.png" alt-text="İş akışı çalışmalarından GitHub.com 'dan eylemleri kullanmayı etkinleştirin.":::

## <a name="setting-up-and-running-your-first-workflow"></a>İlk iş akışınızı ayarlama ve çalıştırma

Artık eylemler ve GitHub Connect ayarlanmış olduğuna göre, tüm bu işleri iyi kullanıma koyalim. İşte, GitHub eylemleri tarafından desteklenen GitHub API 'sini kullanan etkileşimler aracılığıyla "komut dosyası" GitHub sayesinde, mükemmel [octokit/istek-eyleme](https://github.com/octokit/request-action)başvuruda bulunan örnek bir iş akışı.

Bu temel iş akışında, `octokit/request-action` API 'yi kullanarak yalnızca GitHub 'daki bir sorunu açmak için kullanacağız.

:::image type="content" source="media/github-enterprise-server/workflow-example.png" alt-text="Örnek iş akışı.":::

>[!NOTE]
>GitHub.com eylemi barındırır, ancak GitHub Enterprise Server üzerinde çalıştırıldığında *otomatik olarak* GitHub ENTERPRISE Server API 'sini kullanır.

GitHub bağlantısını etkinleştirememeyi seçerseniz, aşağıdaki alternatif iş akışını kullanabilirsiniz.

:::image type="content" source="media/github-enterprise-server/workflow-example-2.png" alt-text="Alternatif örnek iş akışı.":::

Örneğiniz üzerindeki bir depoya gidin ve yukarıdaki iş akışını şu şekilde ekleyin: `.github/workflows/hello-world.yml`

:::image type="content" source="media/github-enterprise-server/workflow-example-3.png" alt-text="Başka bir örnek iş akışı.":::

Deponuzdaki **Eylemler** sekmesinde iş akışının yürütülmesi için bekleyin.

:::image type="content" source="media/github-enterprise-server/executed-example-workflow.png" alt-text="Örnek iş akışı yürütüldü.":::

Ayrıca, Çalıştırıcısı tarafından işlenmekte olan izlemeyi da izleyebilirsiniz.

:::image type="content" source="media/github-enterprise-server/workflow-processed-by-runner.png" alt-text="Çalıştırıcısı tarafından işlenen iş akışı.":::

Her şey başarıyla çalıştırıldıysa, deponuzda "Merhaba Dünya" adlı yeni bir sorun görmeniz gerekir.

:::image type="content" source="media/github-enterprise-server/example-in-repo.png" alt-text="Depoda bir örnek.":::

Tebrikler! Azure VMware Çözüm özel bulutunuzda çalışan GitHub Enterprise Server 'daki ilk eylemler iş akışınızı az önce tamamladınız.

Bu makalede, Azure VMware çözümünüz özel bulutunuzun en üstünde GitHub.com 'in şirket içinde barındırılan eşdeğeri olan GitHub Enterprise Server 'ın yeni bir örneğini ayarladık. Bu örnek, GitHub eylemleri için destek içerir ve günlüklerin ve yapıtların sürekliliği için Azure Blob depolamayı kullanır. Ancak GitHub eylemleriyle yapabileceklerinizi biraz daha hazırlıyoruz. [GitHub Market](https://github.com/marketplace)'teki eylem listesine göz atın veya [kendinizinkini oluşturun](https://docs.github.com/en/actions/creating-actions).

## <a name="next-steps"></a>Sonraki adımlar

Azure VMware Çözüm özel bulutunuzda GitHub Enterprise Server ayarlamayı artık kapsadığınıza göre, şunları öğrenmek isteyebilirsiniz: 

- [GitHub eylemleri ile çalışmaya başlama](https://docs.github.com/en/actions)
- [Beta programına nasıl katılır](https://resources.github.com/beta-signup/)
- [GitHub Enterprise Server Yönetimi](https://githubtraining.github.io/admin-training/#/00_getting_started)
