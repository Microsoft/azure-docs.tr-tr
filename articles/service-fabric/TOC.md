
# Genel Bakış
## [Service Fabric nedir?](service-fabric-overview.md)
## [Mikro hizmetleri anlama](service-fabric-overview-microservices.md)
## [Uygulama senaryoları](service-fabric-application-scenarios.md)
## [Mimari](service-fabric-architecture.md)
## [Terminoloji](service-fabric-technical-overview.md)
## [İçerik yol haritası](service-fabric-content-roadmap.md)

# Kullanmaya Başlama
## Geliştirme ortamınızı kurma
### [Windows](service-fabric-get-started.md)
### [Linux](service-fabric-get-started-linux.md)
### [Mac OS](service-fabric-get-started-mac.md)
## İlk uygulamanızı oluşturun
### [Windows üzerinde C#](service-fabric-create-your-first-application-in-visual-studio.md)
### [Linux üzerinde Java](service-fabric-create-your-first-linux-application-with-java.md)
### [Linux üzerinde C#](service-fabric-create-your-first-linux-application-with-csharp.md)
## [Yerel bir kümede uygulamaları dağıtma](service-fabric-get-started-with-a-local-cluster.md)

# Nasıl yapılır?
## Uygulama oluşturma
### [Modeller ve senaryolar](service-fabric-patterns-and-scenarios.md)
### Temel Bilgiler
#### [Uygulama modeli](service-fabric-application-model.md)
#### [Uygulamaları paketleme](service-fabric-package-apps.md)
#### [Desteklenen programlama modeli](service-fabric-choose-framework.md)
#### [Hizmet durumu](service-fabric-concepts-state.md)
#### [Hizmet iletişimleri](service-fabric-connect-and-communicate-with-services.md)
#### [Web ön ucu ekleme](service-fabric-add-a-web-frontend.md)
#### [Hizmet bildirimi kaynakları](service-fabric-service-manifest-resources.md)
#### [Java ile geliştirme için Eclipse eklentisini kullanmaya başlama](service-fabric-get-started-eclipse.md)
#### [Visual Studio’da uygulama yönetme](service-fabric-manage-application-in-visual-studio.md)
#### [Visual Studio'da güvenli bağlantı yapılandırma](service-fabric-visualstudio-configure-secure-connections.md)
#### Hata ayıklama
##### [VS’de C# hizmeti hata ayıklaması](service-fabric-debugging-your-application.md)
##### [Eclipse’te Java hizmeti hata ayıklaması](service-fabric-debugging-your-application-java.md)
#### İzleme ve tanılama
##### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
##### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
#### [Uygulama parolalarını yönetme](service-fabric-application-secret-management.md)  
#### [Uygulamanıza yönelik güvenlik ilkeleri yapılandırma](service-fabric-application-runas-security.md)  
#### [Uygulamanızı birden çok ortam için yapılandırma](service-fabric-manage-multiple-environment-app-configuration.md)  
#### [Genel hatalar ve özel durumlar](service-fabric-errors-and-exceptions.md) 

### Konuk tarafından yürütülebilir uygulama
#### [Konuk tarafından yürütülebilir bir uygulama dağıtma](service-fabric-deploy-existing-app.md)
#### [Konuk tarafından yürütülebilir birden çok uygulama dağıtma](service-fabric-deploy-multiple-apps.md)

### Kapsayıcı uygulaması
#### [Genel Bakış](service-fabric-containers-overview.md)
#### [Windows kapsayıcısı dağıtma](service-fabric-deploy-container.md)
#### [Docker kapsayıcısı dağıtma](service-fabric-deploy-container-linux.md)

### Reliable Service uygulaması
#### [Genel Bakış](service-fabric-reliable-services-introduction.md)
#### Başlarken
##### [Windows üzerinde C#](service-fabric-reliable-services-quick-start.md)
##### [Linux üzerinde Java](service-fabric-reliable-services-quick-start-java.md)
#### [Reliable Services yaşam döngüsü - C#](service-fabric-reliable-services-lifecycle.md)
#### [Reliable Services yaşam döngüsü - Java](service-fabric-reliable-services-lifecycle-java.md)
#### [Güvenilir Koleksiyonlar](service-fabric-reliable-services-reliable-collections.md)
#### [Reliable Collections kullanımı](service-fabric-work-with-reliable-collections.md)
#### [Yapılandırma](service-fabric-reliable-services-configuration.md)
#### [Bildirimler](service-fabric-reliable-services-notifications.md)
#### [Yedekleme ve geri yükleme](service-fabric-reliable-services-backup-restore.md)
#### [Reliable Services özelliğiyle iletişim kurma](service-fabric-reliable-services-communication.md)
#### [Reliable Services - C# ile güvenli iletişim](service-fabric-reliable-services-secure-communication.md)
#### [Reliable Services - Java ile güvenli iletişim](service-fabric-reliable-services-secure-communication-java.md)
##### [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)
##### [Uzaktan Hizmet Verme - C#](service-fabric-reliable-services-communication-remoting.md)
##### [Uzaktan Hizmet Verme - Java](service-fabric-reliable-services-communication-remoting-java.md)
##### [WCF](service-fabric-reliable-services-communication-wcf.md)
##### [Ters Proxy](service-fabric-reverseproxy.md)
#### [Gelişmiş kullanım](service-fabric-reliable-services-advanced-usage.md)

### Reliable Actor uygulaması
#### [Genel Bakış](service-fabric-reliable-actors-introduction.md)
#### Kullanmaya Başlama
##### [Windows üzerinde C#](service-fabric-reliable-actors-get-started.md)
##### [Linux üzerinde Java](service-fabric-reliable-actors-get-started-java.md)
#### [Mimari](service-fabric-reliable-actors-platform.md)
#### [Yaşam döngüsü ve atık toplama](service-fabric-reliable-actors-lifecycle.md)
#### [Çok biçimlilik](service-fabric-reliable-actors-polymorphism.md)
#### [Yeniden giriş](service-fabric-reliable-actors-reentrancy.md)
#### [Olaylar](service-fabric-reliable-actors-events.md) 
#### [Zamanlayıcılar ve anımsatıcılar](service-fabric-reliable-actors-timers-reminders.md)
#### [Durum yönetimi](service-fabric-reliable-actors-state-management.md)
#### [KvsActorStateProvider’ı yapılandırma](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)
#### [Türü seri hale getirme](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)
#### [İletişim ayarlarını yapılandırma](service-fabric-reliable-actors-fabrictransportsettings.md) 
#### [ReliableDictionaryActorStateProvider’ı yapılandırma](service-fabric-reliable-actors-reliabledictionarystateprovider-configuration.md)

## Cloud Services’tan geçiş
### [Cloud Services ile Service Fabric karşılaştırması](service-fabric-cloud-services-migration-differences.md)
### [Service Fabric’e geçme](service-fabric-cloud-services-migration-worker-role-stateless-service.md)
### [Önerilen uygulamalar](/azure/architecture/service-fabric/migrate-from-cloud-services)

## Küme oluşturma ve yönetme

### Temel Bilgiler
#### [Genel Bakış](service-fabric-deploy-anywhere.md)
#### [Bir kümeyi tanımlama](service-fabric-cluster-resource-manager-cluster-description.md)
#### [Kapasite planlaması](service-fabric-cluster-capacity.md)
#### [Küme görselleştirme](service-fabric-visualizing-your-cluster.md)
#### [Güvenli bir kümeye bağlanma](service-fabric-connect-to-secure-cluster.md)
#### [Azure CLI kullanarak bir kümeyi yönetme](service-fabric-azure-cli.md) 
#### [Küme güvenliğini sağlama](service-fabric-cluster-security.md)
#### [Olağanüstü durum kurtarma](service-fabric-disaster-recovery.md)
#### [Linux ve Windows arasındaki özellik farkları](service-fabric-linux-windows-differences.md)

### Azure’da kümeler
#### Azure’da küme oluşturma
##### [Azure Portal](service-fabric-cluster-creation-via-portal.md)
##### [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
##### [Visual Studio ve Azure Resource Manager](service-fabric-cluster-creation-via-visual-studio.md)
#### [Küme ağ desenleri](service-fabric-patterns-networking.md)
#### [Düğüm türleri ve VM Ölçek Kümeleri](service-fabric-cluster-nodetypes.md)
#### [Küme ölçeklendirme](service-fabric-cluster-scale-up-down.md)
#### [Bir kümeyi programlama yoluyla ölçeklendirme](service-fabric-cluster-programmatic-scaling.md)
#### [Kümeyi yükseltme](service-fabric-cluster-upgrade.md)
#### [Küme silme](service-fabric-cluster-delete.md)
#### [Erişim denetimi](service-fabric-cluster-security-roles.md)
#### [Küme yapılandırma](service-fabric-cluster-fabric-settings.md)
#### [Küme sertifikası ekleme veya geçirme](service-fabric-cluster-security-update-certs-azure.md) 
#### [Bir Parti Kümesi’ni ücretsiz olarak deneme](http://aka.ms/tryservicefabric)

### Tek başına kümeler
#### [Dağıtımınızı planlama ve dağıtım için hazırlanma](service-fabric-cluster-standalone-deployment-preparation.md)
#### [Service Fabric tek başına paketinin içeriği](service-fabric-cluster-standalone-package-contents.md)
#### [Tek başına küme oluşturma](service-fabric-cluster-creation-for-windows-server.md)
#### [Azure Sanal Makinelerde tek başına küme oluşturma](service-fabric-cluster-creation-with-windows-azure-vms.md)
#### [Küme ölçeklendirme](service-fabric-cluster-windows-server-add-remove-nodes.md)
#### [Kümeyi yükseltme](service-fabric-cluster-upgrade-windows-server.md)
#### [Erişim denetimi](service-fabric-cluster-security-roles.md)
#### [Küme yapılandırma](service-fabric-cluster-manifest.md)
#### [Sertifika kullanarak küme güvenliğini sağlama](service-fabric-windows-cluster-x509-security.md)  
#### [Windows güvenliğini kullanarak küme güvenliğini sağlama](service-fabric-windows-cluster-windows-security.md) 

## Uygulama yaşam döngüsünü yönetme
### [Genel Bakış](service-fabric-application-lifecycle.md)
### [VSTS ile sürekli tümleştirmeyi ayarlama](service-fabric-set-up-continuous-integration.md)
### [Jenkins kullanarak Linux Java uygulamanızı dağıtma](service-fabric-cicd-your-linux-java-application-with-jenkins.md)
### [ImageStoreConnectionString ayarını anlama](service-fabric-image-store-connection-string.md)
### Uygulama dağıtma veya kaldırma
#### [PowerShell](service-fabric-deploy-remove-applications.md)
#### [Visual Studio](service-fabric-publish-app-remote-cluster.md)
### [Uygulama yükseltmeye genel bakış](service-fabric-application-upgrade.md)
### [Uygulama yükseltmeyi yapılandırma](service-fabric-visualstudio-configure-upgrade.md)
### [Uygulama yükseltme parametreleri](service-fabric-application-upgrade-parameters.md)
### Uygulama yükseltme
#### [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
#### [Visual Studio](service-fabric-application-upgrade-tutorial.md)
### [Uygulama yükseltme ile ilgili sorunları giderme](service-fabric-application-upgrade-troubleshooting.md)
### [Uygulama yükseltmede verileri serileştirme](service-fabric-application-upgrade-data-serialization.md)
### [Uygulama yükseltme ile ilgili gelişmiş konular](service-fabric-application-upgrade-advanced.md)

## Uygulama ve küme sistem durumunu denetleme
### [Service Fabric sistem durumunu izleme](service-fabric-health-introduction.md)
### [Hizmet durumunu raporlama ve denetleme](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
### [Özel durum raporları ekleme](service-fabric-report-health.md)
### [Sistem durum raporlarıyla ilgili sorunları giderme](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
### [Sistem durumu raporlarını görüntüleme](service-fabric-view-entities-aggregated-health.md)

## İzleme ve tanılama
### [İzleme ve tanılama uygulamaları](service-fabric-diagnostics-overview.md)
### Hizmetleri yerel olarak izleme ve tanılama
#### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
#### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
### Azure Tanılama günlükleri
#### [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
#### [Linux](service-fabric-diagnostics-how-to-setup-lad.md)
### [Bir hizmet işleminden günlük toplama](service-fabric-diagnostic-collect-logs-without-an-agent.md)
### [Durum bilgisi olan Reliable Services özelliğinde tanılama](service-fabric-reliable-services-diagnostics.md)
### [Reliable Actors hizmetinde tanılama](service-fabric-reliable-actors-diagnostics.md)
### [Yerel kümenizde sorun giderme](service-fabric-troubleshoot-local-cluster-setup.md)

## Uygulamaları ölçeklendirme
### [Reliable Services özelliğini bölümleme](service-fabric-concepts-partitioning.md)
### [Hizmetlerin kullanılabilirliği](service-fabric-availability-services.md)
### [Uygulamaları ölçeklendirme](service-fabric-concepts-scalability.md)
### [Uygulamalara yönelik kapasite planlama](service-fabric-capacity-planning.md)

## Uygulama ve hizmetleri test etme
### [Fault Analysis’e genel bakış](service-fabric-testability-overview.md)
### [Hizmetten hizmete iletişimi test etme](service-fabric-testability-scenarios-service-communication.md)
### Hata benzetimi yapma
#### [Denetimli Chaos kullanma](service-fabric-controlled-chaos.md)
#### [Test eylemlerini kullanma](service-fabric-testability-actions.md)
#### [İş yükleri sırasında](service-fabric-testability-workload-tests.md)
#### [Test senaryolarını kullanma](service-fabric-testability-scenarios.md)
#### [Düğüm geçişi API’lerini kullanma](service-fabric-node-transition-apis.md)
### [Uygulamanıza yük testi yapma](service-fabric-vso-load-test.md)

## Küme kaynaklarını yönetme ve düzenleme
### [Cluster Resource Manager’a genel bakış](service-fabric-cluster-resource-manager-introduction.md)
### [Cluster Resource Manager mimarisi](service-fabric-cluster-resource-manager-architecture.md)
### [Bir kümeyi tanımlama](service-fabric-cluster-resource-manager-cluster-description.md)
### [Uygulama gruplarına genel bakış](service-fabric-cluster-resource-manager-application-groups.md)
### [Cluster Resource Manager ayarlarını yapılandırma](service-fabric-cluster-resource-manager-configure-services.md)
### [Kaynak tüketimi ölçümleri](service-fabric-cluster-resource-manager-metrics.md)
### [Hizmet benzeşimi kullanımı](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
### [Hizmet yerleştirme ilkeleri](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
### [Küme yönetme](service-fabric-cluster-resource-manager-management-integration.md)
### [Küme birleştirme](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
### [Küme Dengeleme](service-fabric-cluster-resource-manager-balancing.md)
### [Azaltma](service-fabric-cluster-resource-manager-advanced-throttling.md)
### [Hizmet taşıma](service-fabric-cluster-resource-manager-movement-cost.md)

# Başvuru
## [PowerShell](//powershell/servicefabric/vlatest/servicefabric)
## [Java API’si](/java/api/microsoft.servicefabric.services)
## [.NET](/dotnet/api/microsoft.servicefabric.services)
## [REST](/rest/api/servicefabric)

# Kaynaklar
## [Service Fabric hakkında sık sorulan sorular](service-fabric-common-questions.md)
## [Service Fabric destek seçenekleri](service-fabric-support.md)
## [Örnek kod](http://aka.ms/servicefabricsamples)
## [Öğrenme yolu](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
## [Fiyatlandırma](https://azure.microsoft.com/pricing/details/service-fabric/)
## [Hizmet Güncelleştirmeleri](https://azure.microsoft.com/updates/?product=service-fabric)
## [MSDN Forumu](https://social.msdn.microsoft.com/Forums/home?forum=AzureServiceFabric)
## [Videolar](https://azure.microsoft.com/documentation/videos/index/?services=service-fabric)
