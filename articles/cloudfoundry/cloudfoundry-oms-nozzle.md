---
title: Cloud Foundry izleme için Azure Log Analytics başlık dağıtma
description: Azure Log Analytics için Cloud Foundry loggregator kafa kılavuzunu dağıtmaya yönelik adım adım yönergeler. Cloud Foundry sistem durumu ve performans ölçümlerini izlemek için kafa başlığı 'nı kullanın.
services: virtual-machines
author: ningk
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: ningk
ms.openlocfilehash: 54001c47d03b686a8e7c1f59f1e53d405e3bc506
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557395"
---
# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Cloud Foundry sistem izleme için Azure Log Analytics başlık dağıtma

[Azure izleyici](https://azure.microsoft.com/services/log-analytics/) , Azure 'daki bir hizmettir. Bulut ve şirket içi ortamlarınızla oluşturulan verileri toplayıp analiz etmenize yardımcı olur.

Log Analytics başlık (başlık), [Cloud Foundry loggregator](https://docs.cloudfoundry.org/loggregator/architecture.html) Fire'tan Azure izleyici günlüklerine olan ölçümleri ileten bir Cloud Foundry (CF) bileşenidir. Kafa başlığı ile, çok sayıda dağıtımda CF sistem durumu ve performans ölçümlerini toplayabilir, görüntüleyebilir ve analiz edebilirsiniz.

Bu belgede, başlık ' ı CF ortamınıza dağıtmayı öğrenirsiniz ve sonra Azure Izleyici günlükleri konsolundan verilere erişebilirsiniz.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Önkoşullar

Aşağıdaki adımlar, başlık dağıtmak için önkoşullardır.

### <a name="1-deploy-a-cf-or-pivotal-cloud-foundry-environment-in-azure"></a>1. Azure 'da bir CF veya özetleme Cloud Foundry ortamı dağıtın

Kafa kaynağını açık kaynaklı bir CF dağıtımı veya bir özetleme Cloud Foundry (PCF) dağıtımıyla birlikte kullanabilirsiniz.

* [Azure’da Cloud Foundry dağıtma](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [Azure 'da Özettal Cloud Foundry dağıtma](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2. başlık dağıtmak için CF komut satırı araçlarını yükler

Kafa başlığı, CF ortamında bir uygulama olarak çalışır. Uygulamayı dağıtmak için CF CLı gerekir.

Başlık Ayrıca, loggregator firehortum ve bulut denetleyicisi için erişim izni gerektirir. Kullanıcı oluşturmak ve yapılandırmak için Kullanıcı hesabı ve kimlik doğrulama (UAA) hizmetine ihtiyacınız vardır.

* [Cloud Foundry CLı 'yi yükler](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [Cloud Foundry UAA komut satırı istemcisini yükler](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

UAA komut satırı istemcisini ayarlamadan önce, RubyGems 'nin yüklü olduğundan emin olun.

### <a name="3-create-a-log-analytics-workspace-in-azure"></a>3. Azure 'da Log Analytics çalışma alanı oluşturma

Log Analytics çalışma alanını el ile veya bir şablon kullanarak oluşturabilirsiniz. Şablon, Azure Izleyici günlükleri konsolu için önceden yapılandırılmış KPI görünümleri ve uyarıları kurulumunu dağıtır. 

#### <a name="to-create-the-workspace-manually"></a>Çalışma alanını el ile oluşturmak için:

1. Azure portal Azure Marketi ' nde Hizmetler listesinde arama yapın ve sonra Log Analytics çalışma alanları ' nı seçin.
2. **Oluştur**' u seçin ve sonra aşağıdaki öğeler için Seçenekler ' i seçin:

   * **Log Analytics çalışma alanı**: çalışma alanınız için bir ad yazın.
   * **Abonelik**: birden çok aboneliğiniz varsa, CF dağıtımınızla aynı olanı seçin.
   * **Kaynak grubu**: yeni bir kaynak grubu oluşturabilir veya bunu CF dağıtımınızla aynı şekilde kullanabilirsiniz.
   * **Konum**: konumu girin.
   * **Fiyatlandırma katmanı**: tamamlanacak **Tamam ' ı** seçin.

Daha fazla bilgi için bkz. [Azure izleyici günlüklerini kullanmaya başlama](../azure-monitor/overview.md).

#### <a name="to-create-the-log-analytics-workspace-through-the-monitoring-template-from-azure-market-place"></a>Azure pazar yeri ' nden izleme şablonu aracılığıyla Log Analytics çalışma alanı oluşturmak için:

1. Azure portalını açın.
1. Sol üst köşedeki "+" işaretine veya "kaynak oluştur" düğmesine tıklayın.
1. Arama penceresinde "Cloud Foundry" yazın, "çözümü Cloud Foundry Izleme" öğesini seçin.
1. Cloud Foundry izleme çözümü şablonu ön sayfası yüklendi, şablon dikey penceresini başlatmak için "Oluştur" düğmesine tıklayın.
1. Gerekli parametreleri girin:
    * **Abonelik**: Log Analytics çalışma alanı için genellikle Cloud Foundry dağıtımıyla aynı bir Azure aboneliği seçin.
    * **Kaynak grubu**: Log Analytics çalışma alanı için var olan bir kaynak grubunu seçin veya yeni bir tane oluşturun.
    * **Kaynak grubu konumu**: kaynak grubunun konumunu seçin.
    * **OMS_Workspace_Name**: bir çalışma alanı adı girin, çalışma alanı yoksa şablon yeni bir tane oluşturur.
    * **OMS_Workspace_Region**: çalışma alanının konumunu seçin.
    * **OMS_Workspace_Pricing_Tier**: Log Analytics çalışma alanı SKU 'su seçin. Başvuru için [fiyatlandırma Kılavuzu](https://azure.microsoft.com/pricing/details/log-analytics/) ' na bakın.
    * **Yasal koşullar**: yasal koşullar ' a tıklayın ve sonra yasal terimi kabul etmek Için "Oluştur" a tıklayın.
1. Tüm parametreler belirtildikten sonra, şablonu dağıtmak için "Oluştur" düğmesine tıklayın. Dağıtım tamamlandığında, durum bildirim sekmesinde görünür.


## <a name="deploy-the-nozzle"></a>Başlık dağıtma

Başlık: PCF kutucuğu olarak veya bir CF uygulaması olarak, kafa 'yi dağıtmanın birkaç farklı yolu vardır.

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>Kafa başlığı 'nı PCF Ops Yöneticisi kutucuğu olarak dağıtma

[PCF Için Azure Log Analytics kafa başlığı 'nı yüklemek ve yapılandırmak](https://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html)için adımları izleyin. Bu basitleştirilmiş yaklaşımda, PCF Ops Manager kutucuğu otomatik olarak yapılandırılacak ve kafa gönderecektir. 

### <a name="deploy-the-nozzle-manually-as-a-cf-application"></a>Kafa başlığı 'nı bir CF uygulaması olarak el ile dağıtın

PCF Ops Manager kullanmıyorsanız, kafa başlığı 'nı bir uygulama olarak dağıtın. Aşağıdaki bölümlerde bu işlem açıklanır.

#### <a name="sign-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>CF kullanarak CF dağıtımınızda bir yönetici olarak oturum açın

Şu komutu çalıştırın:
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

"SYSTEM_DOMAIN", CF etki alanınız adıdır. Bunu, CF dağıtım bildirimi dosyanızda "SYSTEM_DOMAIN" arayarak alabilirsiniz. 

"CF_User", CF yöneticisi adıdır. "Scim" bölümünde adı ve parolayı arayarak, CF dağıtım bildirim dosyanızdaki adı ve "cf_admin_password" öğesini arayın.

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>Bir CF kullanıcısı oluşturma ve gerekli ayrıcalıkları verme

Aşağıdaki komutları çalıştırın:
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

"SYSTEM_DOMAIN", CF etki alanınız adıdır. Bunu, CF dağıtım bildirimi dosyanızda "SYSTEM_DOMAIN" arayarak alabilirsiniz.

#### <a name="download-the-latest-log-analytics-nozzle-release"></a>En son Log Analytics başlık yayınını indirin

Şu komutu çalıştırın:
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables"></a>Ortam değişkenlerini belirleme

Artık geçerli dizininizdeki manifest. yıml dosyasında ortam değişkenlerini ayarlayabilirsiniz. Aşağıda, başlık için uygulama bildirimi gösterilmektedir. Değerleri, belirli Log Analytics çalışma alanı bilgileriniz ile değiştirin.

```
OMS_WORKSPACE             : Log Analytics workspace ID: Open your Log Analytics workspace in the Azure portal, select **Advanced settings**, select **Connected Sources**, and select **Windows Servers**.
OMS_KEY                   : OMS key: Open your Log Analytics workspace in the Azure portal, select **Advanced settings**, select **Connected Sources**, and select **Windows Servers**.
OMS_POST_TIMEOUT          : HTTP post timeout for sending events to Azure Monitor logs. The default is 10 seconds.
OMS_BATCH_TIME            : Interval for posting a batch to Azure Monitor logs. The default is 10 seconds.
OMS_MAX_MSG_NUM_PER_BATCH : The maximum number of messages in a batch to Azure Monitor logs. The default is 1000.
API_ADDR                  : The API URL of the CF environment. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
DOPPLER_ADDR              : Loggregator's traffic controller URL. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
FIREHOSE_USER             : CF user you created in the preceding section, "Create a CF user and grant required privileges." This user has firehose and Cloud Controller admin access.
FIREHOSE_USER_PASSWORD    : Password of the CF user above.
EVENT_FILTER              : Event types to be filtered out. The format is a comma-separated list. Valid event types are METRIC, LOG, and HTTP.
SKIP_SSL_VALIDATION       : If true, allows insecure connections to the UAA and the traffic controller.
CF_ENVIRONMENT            : Enter any string value for identifying logs and metrics from different CF environments.
IDLE_TIMEOUT              : The Keep Alive duration for the firehose consumer. The default is 60 seconds.
LOG_LEVEL                 : The logging level of the Nozzle. Valid levels are DEBUG, INFO, and ERROR.
LOG_EVENT_COUNT           : If true, the total count of events that the Nozzle has received and sent are logged to Azure Monitor logs as CounterEvents.
LOG_EVENT_COUNT_INTERVAL  : The time interval of the logging event count to Azure Monitor logs. The default is 60 seconds.
```

### <a name="push-the-application-from-your-development-computer"></a>Geliştirme bilgisayarınızdan uygulamayı gönderin

OMS-Log-Analytics-firehortm-başlık klasörü altında olduğunuzdan emin olun. Şu komutu çalıştırın:
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>Başlık yüklemesini doğrulama

### <a name="from-apps-manager-for-pcf"></a>Apps Manager 'dan (PCF için)

1. Ops Manager 'da oturum açın ve kutucuğun yükleme panosunda görüntülendiğinden emin olun.
2. Apps Manager 'da oturum açın, başlık için oluşturduğunuz alanın kullanım raporunda listelendiğinden emin olun ve durumun normal olduğunu onaylayın.

### <a name="from-your-development-computer"></a>Geliştirme bilgisayarınızdan

CF CLı penceresinde, şunu yazın:
```
cf apps
```
OMS kafa başlığı uygulamasının çalıştığından emin olun.

## <a name="view-the-data-in-the-azure-portal"></a>Azure portal verileri görüntüleyin

İzleme çözümünü pazar yeri şablonuyla dağıttıysanız, Azure portal gidin ve çözümü bulun. Çözümü şablonda belirttiğiniz kaynak grubunda bulabilirsiniz. Çözüme tıklayın, "Log Analytics konsoluna" gidin, önceden yapılandırılmış görünümler, en üst Cloud Foundry sistem KPI 'Leri, uygulama verileri, uyarılar ve VM sistem durumu ölçümleri ile listelenir. 

Log Analytics çalışma alanını el ile oluşturduysanız, görünümleri ve uyarıları oluşturmak için aşağıdaki adımları izleyin:

### <a name="1-import-the-oms-view"></a>1. OMS görünümünü içeri aktarma

OMS portalından **Görünüm tasarımcı**  >  **içeri aktarma**  >  **gözatmaya** gidin ve omsview dosyalarından birini seçin. Örneğin *Cloud Foundry. omsview*' i seçin ve görünümü kaydedin. Artık **genel bakış** sayfasında bir kutucuk görüntülenir. Görselleştirilen ölçümleri görmek için seçin.

Bu görünümleri özelleştirebilir veya **Görünüm Tasarımcısı** aracılığıyla yeni görünümler oluşturabilirsiniz.

*"Cloud Foundry. omsview"* , Cloud Foundry OMS görünüm şablonunun önizleme sürümüdür. Bu, tam olarak yapılandırılmış, varsayılan bir şablondur. Şablonla ilgili önerileriniz veya geri bildiriminiz varsa, bunları [sorun bölümüne](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues)gönderin.

### <a name="2-create-alert-rules"></a>2. uyarı kuralları oluşturma

[Uyarıları oluşturabilir](../azure-monitor/alerts/alerts-overview.md)ve gerektiğinde sorgular ve eşik değerlerini özelleştirebilirsiniz. Önerilen uyarılar aşağıda verilmiştir:

| Arama sorgusu                                                                  | Temelinde uyarı oluştur | Description                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Tür = CF_ValueMetric_CL Origin_s = BBS Name_s = "Domain. CF-Apps"                   | Sonuç sayısı < 1   | **BBS. Domain.cf-uygulamalar** , CF-uygulamalar etki alanının güncel olup olmadığını gösterir. Bu, bulut denetleyicideki CF uygulaması isteklerinin BBS ile eşitlendiği anlamına gelir. Yürütme için LRPsDesired (Diego-Desired ASIS). Veri alınmadı; belirtilen zaman penceresinde CF-uygulamalar etki alanı güncel değil. |
| Type = CF_ValueMetric_CL Origin_s = rep Name_s = Unhealthyıcell Value_d>1            | Sonuç sayısı > 0   | Diego hücreleri için 0 iyi anlamına gelir ve 1 iyi durumda değildir. Belirtilen zaman penceresinde birden çok sağlıksız Diego hücresi algılanırsa uyarıyı ayarlayın. |
| Type = CF_ValueMetric_CL Origin_s = "Bosh-HM-iletici" Name_s = "System. sağlıklı" Value_d = 0 | Sonuç sayısı > 0 | 1 sistem sağlıklı olduğu ve 0 ' ın sistem sağlıklı olmadığı anlamına gelir. |
| Type = CF_ValueMetric_CL Origin_s = route_emitter Name_s = Danışmandownmode Value_d>0 | Sonuç sayısı > 0   | Tüketil, sistem durumunu düzenli olarak yayar. 0 sistem sağlıklı olduğu anlamına gelir ve 1, rota vericinin Tüketil 'nin bir şekilde algıladığı anlamına gelir. |
| Tür = CF_CounterEvent_CL Origin_s = DopplerServer (Name_s = "TruncatingBuffer. DroppedMessages" veya Name_s = "Doppler. shedEnvelopes") Delta_d>0 | Sonuç sayısı > 0 | Arka basınç nedeniyle kasıtlı olarak Doppler tarafından bırakılan ileti Delta sayısı. |
| Tür = CF_LogMessage_CL SourceType_s = LGR MessageType_s = hata                      | Sonuç sayısı > 0   | Loggregator, günlük işlemle ilgili sorunları göstermek için **LGR** 'yi yayar. Bu tür bir sorun, günlük iletisi çıkışının çok yüksek olduğu bir örnektir. |
| Tür = CF_ValueMetric_CL Name_s = Yavaşconsumeralert                               | Sonuç sayısı > 0   | Kafa başlığı, loggregator 'dan yavaş bir tüketici uyarısı aldığında, bu, Azure Izleyici günlüklerine **Yavaşconsumeralert** valuemetric gönderir. |
| Tür = CF_CounterEvent_CL Job_s = başlık Name_s = olayları kaybetme Delta_d>0              | Sonuç sayısı > 0   | Kaybolan olayların Delta sayısı bir eşiğe ulaşırsa, kafa başlığı çalışırken bir sorun olabileceği anlamına gelir. |

## <a name="scale"></a>Ölçek

Başlık ve loggregator 'ı ölçeklendirebilirsiniz.

### <a name="scale-the-nozzle"></a>Kafa ölçeğini ölçeklendirme

En az iki başlık örneğiyle başlamanız gerekir. Firehortum, iş yükünü tüm başlık örneklerine dağıtır.
Kafa akışının veri trafiğinden gelen veri trafiğiyle devam etmesini sağlamak için, **Yavaşconsumeruyarı** uyarısını ayarlayın (önceki bölümde "uyarı kuralları oluşturma" bölümünde listelenmiştir). Uyarı aldıktan sonra, ölçeklendirmenin gerekip gerekmediğini öğrenmek için [yavaş kafa başlığı kılavuzunu](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz) izleyin.
Kafa ölçeğini genişletmek için, uygulama Yöneticisi veya CF CLı kullanarak, başlık için örnek numaralarını veya bellek ya da disk kaynaklarını artırın.

### <a name="scale-the-loggregator"></a>Loggregator 'ı ölçeklendirin

Loggregator, günlüğe kaydetme işlemiyle ilgili sorunları göstermek için bir **LGR** günlüğü iletisi gönderir. Günlüğe loggregator 'in ölçeğini ayarlamak için uyarıyı izleyebilirsiniz.
Loggregator 'ın ölçeğini artırmak için, Doppler arabellek boyutunu artırın ya da CF bildiriminde ek Doppler sunucu örnekleri ekleyin. Daha fazla bilgi için bkz. [loggregator ölçeklendirme Kılavuzu](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling).

## <a name="update"></a>Güncelleştir

Kafa başlığı 'nı daha yeni bir sürümle güncelleştirmek için yeni kafa başlığı sürümünü indirin, yukarıdaki "başlık dağıtma" bölümündeki adımları izleyin ve uygulamayı yeniden gönderin.

### <a name="remove-the-nozzle-from-ops-manager"></a>Başlık Ops Manager 'dan kaldırma

1. Ops Manager 'da oturum açın.
2. **PCF kutucuğu için Microsoft Azure Log Analytics başlık** ' nu bulun.
3. Çöp simgesini seçin ve silmeyi onaylayın.

### <a name="remove-the-nozzle-from-your-development-computer"></a>Geliştirme bilgisayarınızdan başlık kaldırma

CF CLı pencerenizde, şunu yazın:
```
cf delete <App Name> -r
```

Kafa başlığı ' nı kaldırırsanız, OMS portalındaki veriler otomatik olarak kaldırılmaz. Bu, Azure Izleyici günlükleri saklama ayarınız temelinde sona erer.

## <a name="support-and-feedback"></a>Destek ve geri bildirim

Azure Log Analytics başlık açık kaynaklıdır. Sorularınızı ve geri bildiriminizi [GitHub bölümüne](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues)gönderin. Bir Azure destek talebi açmak için hizmet kategorisi olarak "Cloud Foundry çalıştıran sanal makine" seçeneğini belirleyin. 

## <a name="next-step"></a>Sonraki adım

PCF 2.0 'dan VM performans ölçümleri, sistem ölçümleri Ileticisine göre Azure Log Analytics başlık dosyasına aktarılır ve Log Analytics çalışma alanıyla tümleşiktir. Artık VM performans ölçümleri için Log Analytics aracısına ihtiyacınız yoktur. Ancak, syslog bilgilerini toplamak için Log Analytics aracısını kullanmaya devam edebilirsiniz. Log Analytics Aracısı, CF sanal makinelerinize bir Bosh eklentisi olarak yüklenir. 

Ayrıntılar için bkz. [Cloud Foundry dağıtımınıza Log Analytics aracısı dağıtma](https://github.com/Azure/oms-agent-for-linux-boshrelease).