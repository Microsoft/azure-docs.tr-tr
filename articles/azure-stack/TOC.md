# Genel Bakış
## [Azure Stack nedir?](azure-stack-poc.md)
## [Yenilikler](azure-stack-whats-new.md)
## [Önemli özellikler ve kavramlar](azure-stack-key-features.md)
## [POC mimarisi](azure-stack-architecture.md)

# Değerlendirme ve dağıtma
## başlarken
### [Dağıtım önkoşulları](azure-stack-deploy.md)
### [Dağıtma](azure-stack-run-powershell-script.md)
### [Kaydolma](azure-stack-register.md)
## Nasıl yapılır?
### [Azure Stack POC'ye Bağlanma](azure-stack-connect-azure-stack.md)
### [Varsayılan görüntü ekleme](azure-stack-add-default-image.md)
### [Sanal makine sağlama](azure-stack-provision-vm.md)
### [Depolama hesabı oluşturma](azure-stack-provision-storage-account.md)
### [Azure Stack kiracısı ekleme](azure-stack-add-new-user-aad.md)
### [Azure Stack POC’yi yeniden dağıtma](azure-stack-redeploy.md)

# Yönet
## Genel Bakış
### [Bölge yönetimi](azure-stack-region-management.md)
### [Portalları kullanma](azure-stack-manage-portals.md)
## başlarken
### [Azure Stack’e bağlanma](azure-stack-connect-azure-stack.md)
### Yönetim ortamını ayarlama
#### [PowerShell yükleme](azure-stack-powershell-install.md)
#### [Araçları indirme](azure-stack-powershell-download.md)
#### [PowerShell yapılandırma](azure-stack-powershell-configure.md)
## Nasıl yapılır?
### [Güncelleştirmeleri yönetme](azure-stack-updates.md)
### [Sistem durumu ve uyarıları izleme](azure-stack-monitor-health.md)
### [Ağ kaynaklarını yönetme](azure-stack-viewing-public-ip-address-consumption-in-tp2.md)
### [Depolama kaynaklarını yönetme](azure-stack-manage-storage-accounts.md)
### [Windows Azure Paketi VM’lerini yönetme](azure-stack-manage-windows-azure-pack.md)

# Güvenlik ve uyumluluk
## Nasıl yapılır?
### [RBAC’yi yönetme](azure-stack-manage-permissions.md)
### [Kullanıcıları AD FS’ye ekleme](azure-stack-add-users-adfs.md)
### [Hizmet sorumlusu oluşturma](Azure-stack-create-service-principals.md)

# Hizmet sunma
## başlarken
### Plan, teklif ve kota oluşturma
#### [Kota ayarlama](azure-stack-setting-quotas.md)
#### [Plan oluşturma](azure-stack-create-plan.md)
#### [Teklif oluşturma](azure-stack-create-offer.md)
#### [Bir teklife abone olma](azure-stack-subscribe-plan-provision-vm.md)
#### [Azure Stack’te teklifleri yetkilendirme](azure-stack-delegated-provider.md)
## Nasıl yapılır?
### PaaS olarak SQL veya MySQL teklifi
#### [MySQL kaynak sağlayıcısı dağıtma](azure-stack-mysql-resource-provider-deploy.md)
#### [SQL kaynak sağlayıcısı dağıtma](azure-stack-sql-resource-provider-deploy.md)
### PaaS olarak App Service teklifi
#### [Azure Stack’te App Service’a genel bakış](azure-stack-app-service-overview.md)
#### [Başlamadan önce](azure-stack-app-service-before-you-get-started.md)
#### [App Service kaynak sağlayıcısı dağıtma](azure-stack-app-service-deploy.md)
#### [Daha fazla Web çalışanı rolü ekleme](azure-stack-app-service-add-worker-roles.md)
#### [Dağıtım kaynaklarını yapılandırma](azure-stack-app-service-configure-deployment-sources.md)
#### [Azure Stack üzerinde App Service’te FTP’yi etkinleştirme ](azure-stack-app-service-enable-ftp.md)
### Market’i doldurma
#### [Market’e genel bakış](azure-stack-marketplace.md)
#### [Market öğelerini indirme](azure-stack-download-azure-marketplace-item.md)
#### [Market öğesi oluşturma ve yayımlama](azure-stack-create-and-publish-marketplace-item.md)
#### [Özel bir sanal makine görüntüsü ekleme](azure-stack-add-vm-image.md)
#### [Linux sanal makineleri dağıtma](azure-stack-linux.md)
### Kullanım ve Faturalandırma
#### [Genel Bakış](azure-stack-billing-and-chargeback.md)
#### [Kullanım verilerini raporlama](azure-stack-usage-reporting.md)
#### [Sağlayıcı kullanım API’si](azure-stack-provider-resource-api.md)
#### [Kiracı kullanım API’si](azure-stack-tenant-resource-usage-api.md)
#### [Kullanım Hakkında SSS](azure-stack-usage-related-faq.md)

# Hizmetleri kullanma
## İşlem
### [VM görüntüsü ekleme](azure-stack-add-vm-image.md)
### [Linux konukları kullanma](azure-stack-linux.md)
## Depolama
### [Genel Bakış](azure-stack-storage-overview.md)
### [Farklılıklar ve dikkat edilmesi gerekenler](azure-stack-acs-differences-tp2.md)
## Ağ
### [Azure Stack için iDNS](azure-stack-understanding-dns-in-tp2.md)
### [Azure Stack’te DNS](azure-stack-dns.md)
### [Siteden siteye VPN bağlantılarını anlama](azure-stack-create-vpn-connection-one-node-tp2.md)
## Anahtar Kasası
### [Genel Bakış](azure-stack-kv-intro.md)
### Kullanmaya Başlama
#### [Portal ile yönetme](azure-stack-kv-manage-portal.md)
#### [PowerShell ile Yönetme](azure-stack-kv-manage-powershell.md)
### Nasıl yapılır?
#### [Sertifika ile VM oluşturma](azure-stack-kv-push-secret-into-vm.md)
#### [Anahtar Kasası örnek uygulaması](azure-stack-kv-sample-app.md)

# Uygulama oluşturma
## Genel Bakış
### [Azure Stack için geliştirme](azure-stack-developer.md)
## başlarken
### [Azure Stack’e bağlanma](azure-stack-connect-azure-stack.md)
### Geliştirme ortamı ayarlama
#### [PowerShell yükleme](azure-stack-powershell-install.md)
#### [Araçları indirme](azure-stack-powershell-download.md)
#### [PowerShell yapılandırma](azure-stack-powershell-configure.md)
#### [Visual Studio yükleme](azure-stack-install-visual-studio.md)
## Nasıl yapılır?
### [İlke modülü kullanma](azure-stack-policy-module.md)
### Şablon kullanma
#### [Şablona genel bakış](azure-stack-arm-templates.md)
#### [Şablon geliştirme](azure-stack-develop-templates.md)
#### [Şablonları denetleme](azure-stack-validate-templates.md)
#### [Portal ile dağıtma](azure-stack-deploy-template-portal.md)
#### [PowerShell ile dağıtma](azure-stack-deploy-template-powershell.md)
#### [Visual Studio ile dağıtma](azure-stack-deploy-template-visual-studio.md)

# Sorun giderme
## [Bilinen sorunlar](azure-stack-troubleshooting.md)
## [Azure Stack’te tanılama](azure-stack-diagnostics.md)

# Başvuru
## [API sürümü profillerini yönetme](azure-stack-version-profiles.md)

# Kaynaklar
## [MSDN forumu](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStack)  
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-stack)

