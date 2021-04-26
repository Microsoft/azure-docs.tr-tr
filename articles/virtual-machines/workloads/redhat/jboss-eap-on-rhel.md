---
title: Hızlı başlangıç-Red Hat Enterprise Linux (RHEL) üzerinde Jpatron kurumsal uygulama platformunu (EAP) Azure VM 'lerine ve sanal makine ölçek kümelerine dağıtma
description: Azure RHEL VM 'lerinde ve sanal makine ölçek kümelerinde Red Hat Jpatron EAP kullanarak kurumsal Java uygulamaları dağıtma.
author: theresa-nguyen
ms.author: bicnguy
ms.date: 10/30/2020
ms.assetid: 8a4df7bf-be49-4198-800e-db381cda98f5
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: redhat
ms.custom:
- mode-api
ms.collection: linux
ms.openlocfilehash: 18cf093adad858f50b2b1fa1c97e38821bd1c949
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538677"
---
# <a name="deploy-enterprise-java-applications-to-azure-with-jboss-eap-on-red-hat-enterprise-linux"></a>Red Hat Enterprise Linux’da JBoss EAP ile Azure’a kurumsal Java uygulamalarını dağıtma

Bu makaledeki Azure hızlı başlangıç şablonları [Red Hat Enterprise Linux (RHEL)](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux) Ile [Jpatron kurumsal uygulama platformunu (EAP)](https://www.redhat.com/en/technologies/jboss-middleware/application-platform) Azure sanal makinelerine (VM 'ler) ve sanal makine ölçek kümelerine nasıl dağıtacağınızı gösterir. Dağıtımı doğrulamak için örnek bir Java uygulaması kullanacaksınız. 

Jpatron EAP açık kaynaklı bir uygulama sunucusu platformudur. Java uygulamalarınız için kurumsal düzeyde güvenlik, ölçeklenebilirlik ve performans sunar. RHEL, açık kaynaklı bir işletim sistemi (OS) platformudur. Mevcut uygulamaların ölçeklendirilmesine ve tüm ortamlarda gelişen teknolojilerde kullanıma sunulmaya olanak tanır. 

Jpatron EAP ve RHEL her türlü ortamda kurumsal Java uygulamaları oluşturmak, çalıştırmak, dağıtmak ve yönetmek için ihtiyacınız olan her şeyi içerir. Birleşim, şirket içi, sanal ortamlar ve özel, genel ya da karma bulutlarda açık kaynaklı bir çözümdür.

## <a name="prerequisites"></a>Önkoşullar 

* Etkin aboneliği olan bir Azure hesabı. Bir Azure aboneliği almak için, [Visual Studio aboneleri Için Azure kredilerinizi](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) etkinleştirin veya [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/pricing/free-trial).

* Jpatron EAP yüklemesi. Jpatron EAP için Red Hat abonelik yönetimi (RHSM) yetkilendirimiyle Red Hat hesabına sahip olmanız gerekir. Bu yetkilendirme, Red Hat test ve sertifikalı Jpatron EAP sürümünü indirmenizi sağlayacak.  

  EAP yetkilendirmeleri yoksa, başlamadan önce bir [Jpatron EAP değerlendirme aboneliği](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) alın. Yeni bir Red Hat aboneliği oluşturmak için [Red Hat müşteri portalı](https://access.redhat.com/) ' na gidin ve bir hesap ayarlayın.

* [Azure CLI](/cli/azure/overview).

* RHEL seçenekleri. Kullandıkça Öde (PAYG) veya kendi aboneliğinizi getir (KCG) seçeneğini belirleyin. KCG sayesinde, hızlı başlangıç şablonunu dağıtmadan önce [Red Hat bulut erişimi](https://access.redhat.com/) RHEL Gold görüntünüzü etkinleştirmeniz gerekir.

## <a name="java-ee-and-jakarta-ee-application-migration"></a>Java EE ve Jakarta EE uygulama geçişi

### <a name="migrate-to-jboss-eap"></a>Jpatron EAP 'ye geçiş
Jpatron EAP 7,2 ve 7,3, Java Enterprise Edition (Java EE) 8 ve Jakarta EE 8 belirtimlerinin sertifikalı uygulamalarıdır. Jpatron EAP, yüksek kullanılabilirliğe sahip (HA) kümeleme, mesajlaşma ve dağıtılmış önbelleğe alma gibi özellikler için önceden yapılandırılmış seçenekler sağlar. Ayrıca, kullanıcıların Jpatron EAP 'nin sağladığı çeşitli API 'Leri ve Hizmetleri kullanarak uygulamaları yazmasına, dağıtmasına ve çalıştırmasına olanak tanır.  

Jpatron EAP hakkında daha fazla bilgi için bkz. [Jpatron eap 7,2 'ye giriş](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html-single/introduction_to_jboss_eap/index) veya [jpatron EAP 7,3 ' e giriş](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/introduction_to_jboss_eap/index).

 #### <a name="applications-on-jboss-eap"></a>Jpatron EAP üzerinde uygulamalar

* **Web Hizmetleri uygulamaları**. Web Hizmetleri, yazılım uygulamaları arasında birlikte çalışabilmenin standart bir yolunu sunar. Her uygulama, farklı platformlar ve çerçeveler üzerinde çalışabilir. Bu Web Hizmetleri, iç ve heterojen alt sistem iletişimini kolaylaştırır. 

  Daha fazla bilgi edinmek için bkz. EAP [7,2 üzerinde Web Hizmetleri uygulamaları geliştirme](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_web_services_applications/index) veya [EAP 7,3 üzerinde Web Hizmetleri uygulamaları geliştirme](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_web_services_applications/index).

* **Enterprise Java Beans (EJB) uygulamaları**. EJB 3,2, dağıtılmış, işlemsel, güvenli ve taşınabilir Java EE ve Jakarta EE uygulamaları geliştirmeye yönelik bir API 'dir. EJB, bir uygulamanın iş mantığını yeniden kullanımını teşvik eden ayrılmış bir şekilde uygulamak için kurumsal Beans adlı sunucu tarafı bileşenlerini kullanır. 

  Daha fazla bilgi edinmek için bkz. EAP [7,2 ÜZERINDE EJB uygulamaları geliştirme](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_ejb_applications/index) veya [EAP 7,3 üzerinde EJB uygulamaları geliştirme](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_ejb_applications/index).

* **Uygulamaları hazırda beklet**. Geliştiriciler ve Yöneticiler, Jpatron EAP ile Java Kalıcılık API 'SI (JPA) ve hazırda bekletme uygulamaları geliştirebilir ve dağıtabilir. Hazırda bekletme çekirdeği, Java dili için nesne ilişkisel bir eşleme çerçevesidir. Nesne yönelimli bir etki alanı modelini ilişkisel bir veritabanına eşlemek için bir çerçeve sağlar, böylece uygulamalar veritabanıyla doğrudan etkileşime engel olabilir. 

  Hazırda Beklet varlık Yöneticisi, [JPA 2,1 belirtimi](https://www.jcp.org/en/jsr/overview)tarafından tanımlanan programlama arabirimlerini ve yaşam döngüsü kurallarını uygular. Bu sarmalayıcı, hazırda bekleme ek açıklamalarıyla birlikte, çok sayıda hazırda bekleme moduna sahip bir tamamen (ve tek başına) JPA çözümü uygular. 
  
  Hazırda bekletme hakkında daha fazla bilgi için bkz. EAP [7,2 üzerinde JPA](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/development_guide/java_persistence_api) veya  [EAP 7,3 üzerinde Cakarta kalıcılığı](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/development_guide/java_persistence_api).

#### <a name="red-hat-migration-toolkit-for-applications"></a>Uygulamalar için Red Hat geçiş araç seti
[Uygulamalar Için Red Hat geçiş araç seti (MTA)](https://developers.redhat.com/products/mta/overview) , Java uygulama sunucuları için bir geçiş aracıdır. Başka bir uygulama sunucusundan Jpatron EAP 'ye geçiş yapmak için bu aracı kullanın. [Çakışan Küreler IDE](https://www.eclipse.org/ide/), [Red Hat CodeReady çalışma alanları](https://developers.redhat.com/products/codeready-workspaces/overview)ve Java için [Visual Studio Code](https://code.visualstudio.com/docs/languages/java) IDE eklentileriyle birlikte çalışmaktadır. 

MTA, ücretsiz ve açık kaynaklı bir araçtır:
* Uygulama analizini otomatikleştirir.
* Efor tahmini destekler.
* Kod geçişini hızlandırır.
* Kapsayıcılama desteği.
* Azure Iş yükü Oluşturucu ile tümleşir.

### <a name="migrate-jboss-eap-from-on-premises-to-azure"></a>Jpatron EAP 'yi Şirket içinden Azure 'a geçirme
RHEL 'de Jpatron EAP 'nin Azure Marketi teklifi, Azure VM 'lerinde 20 dakikadan kısa bir süre içinde yüklenir ve temin edilir. Bu tekliflere [Azure Marketi](https://azuremarketplace.microsoft.com/)'nden erişebilirsiniz.

Bu Azure Marketi teklifi, gereksinimlerinizi desteklemek için çeşitli EAP ve RHEL sürümlerinin birleşimlerini içerir. Jpatron EAP her zaman BYOS 'dir, ancak RHEL işletim sistemi için KCG veya PAYG arasında seçim yapabilirsiniz. Azure Marketi teklifi, RHEL 'de tek başına veya kümelenmiş VM 'Ler olarak Jpatron EAP için plan seçeneklerini içerir:

* Jpatron EAP 7,2 RHEL 7,7 VM (PAYG)
* Jpatron EAP 7,2 RHEL 8,0 VM (PAYG)
* Jpatron EAP 7,3 RHEL 8,0 VM (PAYG)
* Jpatron EAP 7,2 RHEL 7,7 VM (BYOS)
* Jpatron EAP 7,2 RHEL 8,0 VM (BYOS)
* Jpatron EAP 7,3 RHEL 8,0 VM (BYOS)

Azure Market teklifleriyle birlikte, Azure geçiş yolculuğuna başlamak için hızlı başlangıç şablonlarını kullanabilirsiniz. Bu hızlı başlangıçlar, farklı yapılandırmalarda ve sürüm birleşimlerinde Jpatron EAP 'yi RHEL 'de dağıtmaya yönelik önceden oluşturulmuş Azure Resource Manager (ARM) şablonları ve betikleri içerir. Şunları yapmanız gerekir:

* Yük dengeleyici.
* Yük Dengeleme ve VM 'Ler için özel bir IP.
* Tek bir alt ağa sahip bir sanal ağ.
* VM yapılandırması (küme veya tek başına).
* Örnek bir Java uygulaması.

Bu şablonlar için çözüm mimarisi şunları içerir:

* Tek başına RHEL VM üzerinde Jpatron EAP.
* Birden çok RHEL VM arasında kümelenmiş bir Jpatron EAP.
* Azure sanal makine ölçek kümeleri aracılığıyla kümelenmiş, Jpatron EAP.

#### <a name="linux-workload-migration-for-jboss-eap"></a>Jpatron EAP için Linux Iş yükü geçişi
Azure Iş yükü Oluşturucu, şirket içi Java uygulamalarına yönelik kavram kanıtı, değerlendirme ve geçiş sürecini Azure 'a basitleştirir. İş yükü Oluşturucu, Jpatron EAP sunucularını belirlemek için Azure geçişi bulma aracı ile tümleşir. Daha sonra, Jpatron EAP sunucusu dağıtımı için dinamik olarak anormal bir PlayBook oluşturur. Diğer uygulama sunucularından Jpatron EAP 'ye sunucu geçirmek için Red Hat MTA aracı 'nı kullanır. 

Geçişi basitleştirecek adımlar şunlardır:
1. **Değerlendirme**. Jpatron EAP kümelerini bir Azure VM veya bir sanal makine ölçek kümesi kullanarak değerlendirin.
1. **Değerlendirme**. Uygulamaları ve altyapıyı tarayın.
1. **Altyapı yapılandırması**. Bir iş yükü profili oluşturun.
1. **Dağıtım ve test**. İş yükünü dağıtın, geçirin ve test edin.
1. **Dağıtım sonrası yapılandırma**. Verilerle tümleştirin, izleme, güvenlik, yedekleme ve daha fazlasını yapın.

## <a name="server-configuration-choice"></a>Sunucu yapılandırma seçimi

RHEL VM 'nin dağıtımı için, PAYG veya KCG 'LERI seçebilirsiniz. [Azure Marketi](https://azuremarketplace.microsoft.com) varsayılan görüntüleri PAYG olarak. Kendi RHEL işletim sistemi görüntünüz varsa, bir KCG-Type RHEL VM dağıtın. VM veya sanal makine ölçek kümesini dağıtmadan önce RHSM hesabınızın bulut erişimi aracılığıyla KCG yetkilendirmelerine sahip olduğundan emin olun.

Jpatron EAP, uygulamalarına işlevsellik ve API 'Ler sağlamaya yönelik güçlü yönetim özellikleri içerir. Bu yönetim özellikleri, Jpatron EAP 'yi başlatmak için kullandığınız işletim moduna bağlı olarak farklılık gösterir. Bu, RHEL ve Windows Server 'da desteklenir. Jpatron EAP, ayrık örnekleri yönetmek için tek başına sunucu işletim modu sağlar. Ayrıca, tek bir denetim noktasından örnek gruplarını yönetmek için yönetilen bir etki alanı işletim modu sağlar. 

> [!NOTE]
> Azure altyapı hizmetleri HA özelliğini yönettiğinden, Jpatron EAP tarafından yönetilen etki alanları Microsoft Azure desteklenmez. 

Ortam değişkeni, `EAP_HOME` Jpatron EAP yüklemesinin yolunu gösterir. Jpatron EAP hizmetini tek başına modunda başlatmak için aşağıdaki komutu kullanın:

```
$EAP_HOME/bin/standalone.sh
```
    
Bu başlangıç betiği, JVM seçenekleri gibi bazı varsayılan tercihleri ayarlamak için EAP_HOME/bin/standalone.conf dosyasını kullanır. Bu dosyadaki ayarları özelleştirebilirsiniz. Jpatron EAP, varsayılan olarak tek başına modunda başlamak için standalone.xml yapılandırma dosyasını kullanır, ancak başlatmak için farklı bir mod kullanabilirsiniz. 

Kullanılabilir tek başına yapılandırma dosyaları ve bunların nasıl kullanılacağı hakkında ayrıntılı bilgi için bkz. EAP [7,2 Için tek başına sunucu yapılandırma dosyaları](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files) veya [EAP 7,3 Için tek başına sunucu yapılandırma dosyaları](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files). 

Jpatron EAP 'yi farklı bir yapılandırmayla başlatmak için `--server-config` bağımsız değişkenini kullanın. Örnek:
    
 ```
 $EAP_HOME/bin/standalone.sh --server-config=standalone-full.xml
 ```
    
Tüm kullanılabilir başlangıç betiği bağımsız değişkenlerinin ve amaçlarını tümüyle listelemek için `--help` bağımsız değişkenini kullanın. Daha fazla bilgi için bkz. EAP [7,2 üzerinde sunucu çalışma zamanı değişkenleri](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime) veya [EAP 7,3 üzerinde sunucu çalışma zamanı bağımsız](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime)değişkenleri.
    
Jpatron EAP, küme modunda da çalışabilir. Jpatron EAP küme mesajlaşma işlemi, Jpatron EAP ileti sunucularının gruplanarak ileti işleme yükünü paylaşmasına izin verir. Kümedeki her etkin düğüm, kendi iletilerini yöneten ve kendi bağlantılarını işleyen etkin bir Jpatron EAP mesajlaşma sunucusudur. Daha fazla bilgi edinmek için bkz. EAP [7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuring_messaging/clusters_overview) veya [ kümelerdeki kümelere genel bakış 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuring_messaging/clusters_overview). 

## <a name="support-and-subscription-notes"></a>Destek ve abonelik notları
Bu hızlı başlangıç şablonları şu şekilde sunulmaktadır: 

- RHEL OS, Red Hat Gold görüntü modeli aracılığıyla PAYG veya BYOS olarak sunulur.
- Jpatron EAP yalnızca BYOS olarak sunulur.

#### <a name="using-rhel-os-with-the-payg-model"></a>RHEL işletim sistemini PAYG modeliyle kullanma

Varsayılan olarak, bu hızlı başlangıç şablonları Azure Market 'ten Isteğe bağlı RHEL 7,7 veya 8,0 PAYG görüntüsünü kullanır. PAYG görüntülerinin normal işlem, ağ ve depolama maliyetlerinin en üstünde ek saatlik RHEL abonelik ücreti vardır. Aynı zamanda, örnek Red Hat aboneliğinize kaydedilir. Bu, yetkilendirmelerinizin birini kullandığınız anlamına gelir. 

Bu PAYG görüntüsü "çift Faturalandırma" ile sonuçlanacaktır. Kendi RHEL görüntünüzü oluşturarak bu sorundan kaçınabilirsiniz. Daha fazla bilgi edinmek için, [Microsoft Azure IÇIN RHEL VM sağlama](https://access.redhat.com/articles/uploading-rhel-image-to-azure)başlıklı Red Hat Bilgi Bankası makalesini okuyun. Veya [Red Hat bulut erişimi](https://access.redhat.com/) RHEL Gold görüntünüzü etkinleştirin.

PAYG VM fiyatlandırmasıyla ilgili ayrıntılar için bkz. [Red Hat Enterprise Linux fiyatlandırması](https://azure.microsoft.com/pricing/details/virtual-machines/red-hat/). PAYG modelinde RHEL 'yi kullanmak için Azure Market 'te RHEL [7,7](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux77-ARM) için belirtilen ödeme yöntemiyle veya [Azure Marketi 'Nde rhel 8,0](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux80-ARM)' ye sahip bir Azure aboneliğine sahip olmanız gerekir. Bu teklifler, Azure aboneliğinde bir ödeme yönteminin belirtilmesini gerektirir.

#### <a name="using-rhel-os-with-the-byos-model"></a>KCG modeliyle RHEL OS kullanma

RHEL işletim sistemi için KCG 'yi kullanmak için Azure 'da RHEL OS kullanma yetkilendirmelerine sahip geçerli bir Red Hat aboneliğine sahip olmanız gerekir. KCG modeliyle RHEL işletim sistemini dağıtmadan önce aşağıdaki önkoşulları doldurun:

1. Red Hat aboneliğinize bağlı RHEL OS ve Jpatron EAP yetkilendirmelerine sahip olduğunuzdan emin olun.
2. RHEL BYOS görüntülerini kullanmak için Azure abonelik KIMLIĞINIZI yetkilendirin. Aşağıdaki adımları içeren işlemi gerçekleştirmek için [Red Hat abonelik yönetimi belgelerini](https://access.redhat.com/documentation/red_hat_subscription_management/1/) izleyin:

   1. Red Hat bulut erişim panonuzda bir sağlayıcı olarak Microsoft Azure etkinleştirin.

   1. Azure abonelik kimliklerinizi ekleyin.

   1. Microsoft Azure bulut erişimi için yeni ürünleri etkinleştirin.
    
   1. Azure aboneliğiniz için Red Hat Gold görüntülerini etkinleştirin. Daha fazla bilgi için bkz. [Microsoft Azure Red Hat Gold görüntüleri](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/cloud-access-gold-images_cloud-access#proc_using-gold-images-azure_cloud-access).

   1. Azure aboneliğinizde Red Hat Gold görüntülerinin kullanılabilir olmasını bekleyin. Bu görüntüler genellikle üç saatlik gönderim dahilinde kullanılabilir.
    
3. RHEL BYOS görüntüleri için Azure Marketi hüküm ve koşullarını kabul edin. Aşağıdaki Azure CLı komutlarını çalıştırarak bu işlemi tamamlayabilirsiniz. Daha fazla bilgi için bkz. [Azure 'Da RHEL BYOS Gold görüntüleri](./byos.md) . En son Azure CLı sürümünü çalıştırıyor olmanız önemlidir.

   1. Azure CLı oturumu açın ve Azure hesabınızla kimlik doğrulaması yapın. Yardım için bkz. [Azure CLI Ile oturum açma](/cli/azure/authenticate-azure-cli).

   1. Aşağıdaki CLı komutunu çalıştırarak RHEL BYOS görüntülerinin aboneliğinizde kullanılabilir olduğunu doğrulayın. Burada herhangi bir sonuç alamazsanız, RHEL BYOS görüntüleri için Azure aboneliğinizin etkinleştirildiğinden emin olun.
   
      ```
      az vm image list --offer rhel-byos --all
      ```

   1. Sırasıyla RHEL 7,7 BYOS ve RHEL 8,0 BYOS için Azure Marketi koşullarını kabul etmek üzere aşağıdaki komutu çalıştırın:
      ```
      az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm77
      ``` 

      ```
      az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm8
      ``` 
   
Aboneliğiniz artık Azure sanal makinelerinde RHEL 7,7 veya 8,0 KCG 'yi dağıtmaya hazırdır.

#### <a name="using-jboss-eap-with-the-byos-model"></a>Kyos modeliyle Jpatron EAP kullanma

Jpatron EAP yalnızca KCG modeli aracılığıyla Azure 'da kullanılabilir. Bu şablonu dağıttığınızda, geçerli EAP yetkilendirmelerine sahip RHSM havuz KIMLIĞIYLE birlikte RHSM kimlik bilgilerinizi sağlamanız gerekir. EAP Yetkilendirmeleriniz yoksa, başlamadan önce bir [Jpatron EAP değerlendirme aboneliği](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) alın.

## <a name="deployment-options"></a>Dağıtım seçenekleri

Şablonu aşağıdaki yollarla dağıtabilirsiniz:

- **PowerShell**. Aşağıdaki komutları çalıştırarak şablonu dağıtın: 

  ```
  New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri <raw link to the template which can be obtained from github>
  ```
 
  Azure PowerShell yükleme ve yapılandırma hakkında bilgi için bkz. [PowerShell belgeleri](/powershell/azure/).  

- **Azure CLI**. Aşağıdaki komutları çalıştırarak şablonu dağıtın:

  ```
  az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  az deployment group create --resource-group <my-resource-group> --template-uri <raw link to the template which can be obtained from github>
  ```

  Azure CLı 'yi yükleme ve yapılandırma hakkında ayrıntılı bilgi için bkz. [CLI 'Yı yükleme](/cli/azure/install-azure-cli).

- **Azure portalı**. Sonraki bölümde belirtildiği gibi Azure hızlı başlangıç şablonlarına giderek Azure portal dağıtabilirsiniz. Hızlı başlangıç adımlarını tamamladıktan sonra **Azure 'A dağıt** veya **GitHub 'da araştır** düğmesini seçin.

## <a name="azure-quickstart-templates"></a>Azure Hızlı Başlangıç şablonları

Dağıtım hedefini karşılayan RHEL üzerinde Jpatron EAP için aşağıdaki hızlı başlangıç şablonlarından birini kullanarak başlayabilirsiniz:

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-standalone-rhel/"> RHEL 'de Jpatron EAP (tek BAŞıNA VM)</a>. Bu işlem, Azure 'da Jpatron-EAP adlı bir Web uygulamasını RHEL 7,7 veya 8,0 VM üzerinde çalışan Jpatron EAP 7,2 veya 7,3 olarak dağıtır.

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-clustered-multivm-rhel/"> RHEL 'de Jpatron EAP (kümelenmiş, birden çok VM)</a>. Bu işlem, *n* sayıda rhel 7,7 veya 8,0 VM üzerinde çalışan bir jpatron EAP 7,2 veya 7,3 kümesinde EAP-Session-Replication adlı bir Web uygulaması dağıtır. Kullanıcı *n* değerine karar veriyor. Tüm VM 'Ler bir yük dengeleyicinin arka uç havuzuna eklenir.

* <a href="https://azure.microsoft.com/en-us/resources/templates/jboss-eap-clustered-vmss-rhel/"> RHEL 'de Jpatron EAP (kümelenmiş, sanal makine ölçek kümesi)</a>. Bu, RHEL 7,7 veya 8,0 sanal makine ölçek kümeleri üzerinde çalışan Jpatron EAP 7,2 veya 7,3 kümesinde EAP-Session-Replication adlı bir Web uygulaması dağıtır.

## <a name="resource-links"></a>Kaynak bağlantıları

* [Azure Hibrit Avantajı](../../windows/hybrid-use-benefit-licensing.md)
* [Azure App Service için bir Java uygulaması yapılandırma](../../../app-service/configure-language-java.md)
* [Azure Red Hat OpenShift üzerinde Jpatron EAP](https://azure.microsoft.com/services/openshift/)
* [Azure App Service Linux üzerinde Jpatron EAP](../../../app-service/quickstart-java.md)
* [Azure App Service Jpatron EAP dağıtma](https://github.com/JasonFreeberg/jboss-on-app-service)

## <a name="next-steps"></a>Sonraki adımlar

* [Jpatron EAP 7,2](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.2/)hakkında daha fazla bilgi edinin.
* [Jpatron EAP 7,3](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/)hakkında daha fazla bilgi edinin.
* [Red Hat abonelik yönetimi](https://access.redhat.com/products/red-hat-subscription-management)hakkında daha fazla bilgi edinin.
* [Azure 'Da Red hat iş yükleri](./overview.md)hakkında bilgi edinin.
* [Jpatron EAP 'yi BIR RHEL VM 'ye veya Azure Marketi 'ndeki sanal makine ölçek kümesine](https://aka.ms/AMP-JBoss-EAP)dağıtın.
* [Jpatron EAP 'yi BIR RHEL VM 'ye veya Azure hızlı başlangıç şablonlarından sanal makine ölçek kümesine](https://aka.ms/Quickstart-JBoss-EAP)dağıtın.
