---
title: Jenkins derleme aracısı olarak Azure Container Instances kullanma
description: Bir Jenkins sunucusunu istendiğinde derleme işleri çalıştırmak için nasıl yapılandıracağınızı öğrenin Azure Container Instances
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 08/31/2018
ms.author: danlep
ms.openlocfilehash: 7e93457a182598a2e8d739f4d626b49ff57b30fb
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150225"
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>Jenkins derleme aracısı olarak Azure Container Instances kullanma

Azure Container Instances (acı) Kapsayıcılı iş yüklerini çalıştırmak için isteğe bağlı, Burstable ve yalıtılmış bir ortam sağlar. Bu öznitelikler nedeniyle, Cankins derleme işlerini büyük ölçekte çalıştırmaya yönelik harika bir platform oluşturur. Bu makalede, bir derleme hedefi olarak ACI ile önceden yapılandırılmış bir Jenkins sunucusunun dağıtımı ve kullanılması anlatılmaktadır.

Azure Container Instances hakkında daha fazla bilgi için bkz. [Azure Container Instances hakkında][about-aci].

## <a name="deploy-a-jenkins-server"></a>Jenkins sunucusu dağıtma

1. Azure portal, **kaynak oluştur** ' u seçin ve **Jenkins**için arama yapın. **Microsoft**'un bir yayımcısı Ile Jenkins teklifini seçin ve ardından **Oluştur**' u seçin.

2. **Temel** bilgiler formuna aşağıdaki bilgileri girin ve ardından **Tamam**' ı seçin.

   - **Ad**: Jenkins dağıtımı için bir ad girin.
   - **Kullanıcı adı**: Jenkins sanal makinesinin Yönetici kullanıcısı için bir ad girin.
   - **Kimlik doğrulama türü**: kimlik doğrulaması için SSH ortak anahtarı önerilir. Bu seçeneği belirlerseniz, Jenkins sanal makinesinde oturum açmak için kullanılacak SSH ortak anahtarını yapıştırın.
   - **Abonelik**: Bir Azure aboneliği seçin.
   - **Kaynak grubu**: Bir kaynak grubu oluşturun veya mevcut bir kaynak grubunu seçin.
   - **Konum**: Jenkins sunucusu için bir konum seçin.

   ![Jenkins Portal dağıtımı için temel ayarlar](./media/container-instances-jenkins/jenkins-portal-01.png)

3. **Ek ayarlar** formunda, aşağıdaki öğeleri doldurun:

   - **Boyut**: Jenkins sanal makineniz için uygun boyutlandırma seçeneğini belirleyin.
   - **VM disk türü**: Jenkins sunucusu için **HDD** (sabit disk sürücüsü) ya da **SSD** (katı hal sürücüsü) belirtin.
   - **Sanal ağ**: varsayılan ayarları değiştirmek istiyorsanız oku seçin.
   - **Alt ağlar**: oku seçin, bilgileri doğrulayın ve **Tamam**' ı seçin.
   - **Genel IP adresi**: genel IP adresine özel bir ad vermek, SKU 'yu yapılandırmak ve atama yöntemini ayarlamak için oku seçin.
   - **Etki alanı adı etiketi**: Jenkins sanal makinesine tam bir URL oluşturmak için bir değer belirtin.
   - **Jenkins sürüm türü**: **LTS**, **Haftalık derleme**veya **Azure tarafından doğrulanan**seçeneklerden istenen yayın türünü seçin.

   ![Jenkins Portal dağıtımı için ek ayarlar](./media/container-instances-jenkins/jenkins-portal-02.png)

4. Hizmet sorumlusu tümleştirmesi için **Otomatik (MSI)** ' i seçerek [Azure kaynakları için yönetilen kimliklerin][managed-identities-azure-resources] otomatik olarak Jenkins örneği için bir kimlik doğrulama kimliği oluşturmasını sağlayabilirsiniz. Kendi hizmet sorumlusu kimlik bilgilerinizi sağlamak için **el ile** ' yi seçin.

5. Bulut aracıları, Jenkins derleme işleri için bulut tabanlı bir platform yapılandırır. Bu makalenin sake 'ı için **aci**'yi seçin. ACI bulut aracısıyla, her Jenkins derleme işi bir kapsayıcı örneğinde çalıştırılır.

   ![Jenkins Portal dağıtımı için bulut tümleştirme ayarları](./media/container-instances-jenkins/jenkins-portal-03.png)

6. Tümleştirme ayarları ile işiniz bittiğinde **Tamam**' ı seçin ve ardından doğrulama özetinde tekrar **Tamam** ' ı seçin. **Kullanım koşulları** özetinde **Oluştur** ' u seçin. Jenkins sunucusunun dağıtılması birkaç dakika sürer.

## <a name="configure-jenkins"></a>Jenkins’i yapılandırma

1. Azure portal Jenkins kaynak grubuna göz atın, Jenkins sanal makinesini seçin ve DNS adını göz önünde atın.

   ![Jenkins sanal makinesiyle ilgili ayrıntıların bulunduğu DNS adı](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

2. Jenkins sanal makinesinin DNS adına gidin ve döndürülen SSH dizesini kopyalayın.

   ![SSH dizesiyle Jenkins oturum açma yönergeleri](./media/container-instances-jenkins/jenkins-portal-04.png)

3. Geliştirme sisteminizde bir terminal oturumu açın ve son adımdan SSH dizesine yapıştırın. Jenkins sunucusunu dağıtırken belirttiğiniz kullanıcı adına `username` güncelleştirin.

4. Oturum bağlandıktan sonra, ilk yönetici parolasını almak için aşağıdaki komutu çalıştırın:

   ```
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

5. SSH oturumunu ve tüneli çalışır durumda bırakın ve bir tarayıcıda `http://localhost:8080` gidin. İlk yönetici parolasını kutuya yapıştırın ve sonra **devam**' ı seçin.

   !["Jenkins" ekranının yönetici parolası kutusuyla birlikte açılması](./media/container-instances-jenkins/jenkins-portal-05.png)

6. Önerilen tüm Jenkins eklentilerini yüklemek için **Önerilen eklentileri yüklensin** ' i seçin.

   !["Önerilen eklentileri yükler" seçiliyken "Jenkins" ekranını özelleştirme](./media/container-instances-jenkins/jenkins-portal-06.png)

7. Yönetici Kullanıcı hesabı oluşturun. Bu hesap, ' de oturum açmak ve Jenkins örneğiniz ile çalışmak için kullanılır.

   !["Birinci yönetici kullanıcı oluştur" ekranı, kimlik bilgileriyle doldurulmuştur](./media/container-instances-jenkins/jenkins-portal-07.png)

8. **Kaydet ve son**' u seçin ve ardından yapılandırmayı tamamladıktan sonra **Jenkins kullanmaya başla** ' yı seçin.

Jenkins artık yapılandırılır ve kodu derlemek ve dağıtmak için hazırdır. Bu örnekte, Azure Container Instances bir Jenkins derlemesini göstermek için basit bir Java uygulaması kullanılır.

## <a name="create-a-build-job"></a>Yapı işi oluşturma

Şimdi, Azure Container Instance üzerinde Jenkins derlemelerini göstermek için Jenkins derleme işi oluşturulur.

1. **Yeni öğe**' yi seçin, derleme projesine **aci-demo**gibi bir ad verin, **Freestyle Project**' i seçin ve **Tamam**' ı seçin.

   ![Yapı işinin adı ve proje türlerinin listesi için kutu](./media/container-instances-jenkins/jenkins-new-job.png)

2. **Genel**altında, **Bu projenin nerede çalıştırılabileceğini kısıtla** seçeneğinin işaretli olduğundan emin olun. Etiket ifadesi için **Linux** girin. Bu yapılandırma, bu derleme işinin ACI bulutu üzerinde çalışmasını sağlar.

   ![Yapılandırma ayrıntıları içeren "genel" sekmesi](./media/container-instances-jenkins/jenkins-job-01.png)

3. **Yapı**' ın altında **derleme Ekle adımı** ' nı seçin ve **kabuğu Yürüt**' ü seçin Komut olarak `echo "aci-demo"` girin.

   ![Derleme adımının seçimleriyle birlikte "derleme" sekmesi](./media/container-instances-jenkins/jenkins-job-02.png)

5. **Kaydet**’i seçin.

## <a name="run-the-build-job"></a>Derleme işini çalıştırma

Yapı işini test etmek ve derleme platformu olarak Azure Container Instances gözlemlemek için bir derlemeyi el ile başlatın.

1. Derleme işini başlatmak için **Şimdi Oluştur** ' u seçin. İşin başlatılması birkaç dakika sürer. Aşağıdaki görüntüye benzer bir durum görmeniz gerekir:

   ![İş durumu ile "derleme geçmişi" bilgileri](./media/container-instances-jenkins/jenkins-job-status.png)

2. İş çalışırken Azure portal açın ve Jenkins kaynak grubuna bakın. Bir kapsayıcı örneğinin oluşturulduğunu görmeniz gerekir. Jenkins işi bu örnek içinde çalışıyor.

   ![Kaynak grubundaki kapsayıcı örneği](./media/container-instances-jenkins/jenkins-aci.png)

3. Jenkins, yapılandırılmış Jenkins yürüticilerinden (varsayılan 2) daha fazla iş çalıştırıyorsa, birden çok kapsayıcı örneği oluşturulur.

   ![Yeni oluşturulan kapsayıcı örnekleri](./media/container-instances-jenkins/jenkins-aci-multi.png)

4. Tüm derleme işleri tamamlandıktan sonra kapsayıcı örnekleri kaldırılır.

   ![Kapsayıcı örnekleri kaldırılmış kaynak grubu](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>Jenkins eklentisiyle ilgili sorunlarını giderme

Jenkins eklentileriyle ilgili hatalarla karşılaşırsanız [Jenkins JIRA](https://issues.jenkins-ci.org/) sayfasında söz konusu bileşenle ilgili sorun bildirebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Azure 'da Jenkins hakkında daha fazla bilgi edinmek için bkz. [Azure ve Jenkins][jenkins-azure].

<!-- LINKS - internal -->
[about-aci]: ./container-instances-overview.md
[jenkins-azure]: ../jenkins/overview.md
[managed-identities-azure-resources]: ../active-directory/managed-identities-azure-resources/overview.md