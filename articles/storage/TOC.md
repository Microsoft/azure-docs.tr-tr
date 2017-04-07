# Genel Bakış

## [Giriş](storage-introduction.md)

# Kullanmaya Başlama

## [Hızlı başlangıç kılavuzu](storage-getting-started-guide.md)
## [Depolama hesabı oluşturma](storage-create-storage-account.md)

## Blob Depolama
### [.NET](storage-dotnet-how-to-use-blobs.md)
### [Java](storage-java-how-to-use-blob-storage.md)
### [Node.js](storage-nodejs-how-to-use-blob-storage.md)
### [C++](storage-c-plus-plus-how-to-use-blobs.md)
### [Python](storage-python-how-to-use-blob-storage.md)
### [PHP](storage-php-how-to-use-blobs.md)
### [Ruby](storage-ruby-how-to-use-blob-storage.md)
### [iOS](storage-ios-how-to-use-blob-storage.md)
### [Xamarin](storage-xamarin-blob-storage.md)

## Kuyruk Depolama
### [.NET](storage-dotnet-how-to-use-queues.md)
### [Java](storage-java-how-to-use-queue-storage.md)
### [Node.js](storage-nodejs-how-to-use-queues.md)
### [C++](storage-c-plus-plus-how-to-use-queues.md)
### [Python](storage-python-how-to-use-queue-storage.md)
### [PHP](storage-php-how-to-use-queues.md)
### [Ruby](storage-ruby-how-to-use-queue-storage.md)

## Tablo Depolama
### [.NET](storage-dotnet-how-to-use-tables.md)
### [Java](storage-java-how-to-use-table-storage.md)
### [Node.js](storage-nodejs-how-to-use-table-storage.md)
### [C++](storage-c-plus-plus-how-to-use-tables.md)
### [Python](storage-python-how-to-use-table-storage.md)
### [PHP](storage-php-how-to-use-table-storage.md)
### [Ruby](storage-ruby-how-to-use-table-storage.md)

## Dosya Depolama
### [Windows, .NET, PowerShell](storage-dotnet-how-to-use-files.md)
### [Linux](storage-how-to-use-files-linux.md)
### [Java](storage-java-how-to-use-file-storage.md)
### [C++](storage-c-plus-plus-how-to-use-files.md)
### [Python](storage-python-how-to-use-file-storage.md)

## Disk Depolama 
### [Resource Manager ve PowerShell kullanarak VM oluşturma](../virtual-machines/virtual-machines-windows-ps-create.md)
### [Azure CLI 2.0 kullanarak bir Linux VM oluşturma](../virtual-machines/linux/quick-create-cli.md)
### [PowerShell kullanarak bir Windows VM’ye yönetilen disk ekleme](../virtual-machines/windows/attach-disk-ps.md)
### [Linux VM’ye yönetilen disk ekleme](../virtual-machines/linux/add-disk.md)
### [Windows’da Anlık Görüntüler kullanılarak Yönetilen Disk olarak depolanmış VHD kopyası oluşturma](../virtual-machines/windows/snapshot-copy-managed-disk.md)
### [Linux’ta Anlık Görüntüler kullanılarak Yönetilen Disk olarak depolanmış VHD kopyası oluşturma](../virtual-machines/linux/snapshot-copy-managed-disk.md)

# Nasıl yapılır?
## [Depolama hesabı oluşturma](storage-create-storage-account.md)
## Blob'ları kullanma
### [Hizmete genel bakış](https://msdn.microsoft.com/library/dd179376.aspx)
### [Sık erişimli ve seyrek erişimli katmanlar](storage-blob-storage-tiers.md)
### [Özel etki alanları](storage-custom-domain-name.md)
### [Bloblara anonim erişim](storage-manage-access-to-resources.md)
### [Örnekler](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob)
## Kuyrukları kullanma
### [Kavramlar](https://msdn.microsoft.com/library/dd179353.aspx)
### [Örnekler](https://azure.microsoft.com/documentation/samples/?service=storage&term=queue)
## Tabloları kullanma
### [Genel Bakış](https://msdn.microsoft.com/library/dd179463.aspx)
### [Tablo Tasarımı Kılavuzu](storage-table-design-guide.md)
### [Örnekler](https://azure.microsoft.com/documentation/samples/?service=storage&term=table)
## Dosyaları kullanma
### [Genel Bakış](/rest/api/storageservices/fileservices/File-Service-Concepts)
### [Azure Dosyalarının Sorunlarını Giderme](storage-troubleshoot-file-connection-problems.md)
### [Örnekler](https://azure.microsoft.com/documentation/samples/?service=storage&term=file)
## Disk kullanma
### [Windows VM’ler için diskler ve VHD’ler](storage-about-disks-and-vhds-windows.md)
### [Linux VM’ler için diskler ve VHD’ler](storage-about-disks-and-vhds-linux.md)
### [Azure Yönetilen Disklere Genel Bakış](storage-managed-disks-overview.md)
### [Azure VM’lerini Azure Yönetilen Disklere geçirme](../virtual-machines/windows/migrate-to-managed-disks.md)
### [AWS ve diğer platformlardan Yönetilen Disklere geçirme](../virtual-machines/windows/on-prem-to-azure.md)
### [Azure IaaS VM Diskleri hakkında Sık Sorulan Sorular](storage-faq-for-disks.md)
### Premium Depolama
#### [VM Diskleri için yüksek performanslı Premium Depolama](storage-premium-storage.md)
#### [Yüksek Performans Tasarımı](storage-premium-storage-performance.md)
### Standart Depolama
#### [Uygun maliyetli Standart Depolama ile yönetilmeyen ve yönetilen VM Diskleri](storage-standard-storage.md)
### Yönetilmeyen diskleri kullanma
#### [Premium Depolama’ya geçiş](storage-migration-to-premium-storage.md)
#### [Artımlı anlık görüntülerle yönetilmeyen VM disklerini yedekleme](storage-incremental-snapshots.md)
## Planlama ve tasarım
### [Çoğaltma](storage-redundancy.md)
### [Ölçeklenebilirlik ve Performans Hedefleri](storage-scalability-targets.md)
### [Performans ve Ölçeklenebilirlik Denetim Listesi](storage-performance-checklist.md)
### [Eşzamanlılık](storage-concurrency.md)
## Geliştirme
### Örnekler
#### [.NET](storage-samples-dotnet.md)
#### [Java](storage-samples-java.md)
### [RA-GRS kullanarak HA Uygulamaları tasarlama](storage-designing-ha-apps-with-ragrs.md)
### [Bağlantı Dizelerini yapılandırma](storage-configure-connection-string.md)
### [Depolama öykünücüsü kullanma](storage-use-emulator.md)
### [Özellikler ile meta verileri ayarlama ve alma](storage-properties-metadata.md)
## Yönet
### [PowerShell](storage-powershell-guide-full.md)
### [Azure CLI 2.0](storage-azure-cli.md)
### [Azure CLI 1.0](storage-azure-cli-nodejs.md)
### [Azure Otomasyonu](automation-manage-storage.md)
## Güvenlik
### [Güvenlik kılavuzu](storage-security-guide.md)
### [Bekleyen verilerin şifrelenmesi](storage-service-encryption.md)
### [Paylaşılan anahtar kimlik doğrulaması](https://msdn.microsoft.com/library/dd179428.aspx)
### [Paylaşılan erişim imzaları (SAS)](storage-dotnet-shared-access-signature-part-1.md)
### [Öğretici: Azure Anahtar Kasasını kullanarak blob’ları şifreleme ve şifre çözme](storage-encrypt-decrypt-blobs-key-vault.md)
### İstemci Tarafında Şifreleme
#### [.NET](storage-client-side-encryption.md)
#### [Java](storage-client-side-encryption-java.md)
#### [Python](storage-client-side-encryption-python.md)
## İzleme ve sorun giderme
### Ölçümler ve günlüğe kaydetme
#### [Depolama Analizi](storage-analytics.md)
#### [Ölçümleri etkinleştirme ve görüntüleme](storage-enable-and-view-metrics.md)
#### [İzleme, tanılama ve sorun giderme](storage-monitoring-diagnosing-troubleshooting.md)
#### [Sorun giderme öğreticisi](storage-e2e-troubleshooting.md)
### Disk silme hatalarıyla ilgili sorunları giderme
#### [Resource Manager Dağıtımında](storage-resource-manager-cannot-delete-storage-account-container-vhd.md)
#### [Klasik dağıtımda](storage-cannot-delete-storage-account-container-vhd.md)
### [Dosya depolama sorunlarını giderme](storage-troubleshoot-file-connection-problems.md)
### [Olağanüstü durum kurtarma kılavuzu](storage-disaster-recovery-guidance.md)
## Veri Aktarma
### [Verileri depolamaya ve depolamadan aktarma](storage-moving-data.md)
### [AzCopy komut satırı yardımcı programı](storage-use-azcopy.md)
### [İçeri/Dışarı Aktarma hizmetini kullanma](storage-import-export-service.md)
### [İçeri/Dışarı Aktarma Aracı’nı kullanma](storage-import-export-tool-how-to.md)
#### [İçeri/Dışarı Aktarma Aracı’nı ayarlama](storage-import-export-tool-setup.md)
#### [Sabit sürücüleri içeri aktarma işine hazırlama](storage-import-export-tool-preparing-hard-drives-import.md)
##### [İçeri aktarma işlemi sırasında özellikleri ve meta verileri ayarlama](storage-import-export-tool-setting-properties-metadata-import.md)
##### [Sabit sürücüleri içeri aktarma işine hazırlamak için örnek iş akışı](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)
##### [İçeri aktarma işlerinde sık kullanılan komutlar için hızlı başvuru](storage-import-export-tool-quick-reference.md)
#### [Dışarı aktarma işi için sürücü kullanımının önizlemesini yapma](storage-import-export-tool-previewing-drive-usage-export-v1.md)
#### [Kopyalama günlük dosyalarıyla iş durumunu gözden geçirme](storage-import-export-tool-reviewing-job-status-v1.md)
#### [Bir içeri aktarma işini onarma](storage-import-export-tool-repairing-an-import-job-v1.md)
#### [Bir dışarı aktarma işini onarma](storage-import-export-tool-repairing-an-export-job-v1.md)
#### [İçeri/Dışarı Aktarma Aracı ile ilgili sorunları giderme](storage-import-export-tool-troubleshooting-v1.md)
#### [İçeri/Dışarı Aktarma hizmet bildirimi dosyası biçimi](storage-import-export-file-format-manifest.md)
#### [İçeri/Dışarı Aktarma hizmeti meta veriler ve özellikler dosyası biçimi](storage-import-export-file-format-metadata-and-properties.md)
#### [İçeri/Dışarı Aktarma hizmeti günlük dosyası biçimi](storage-import-export-file-format-log.md)
### [İçeri/Dışarı Aktarma Aracı’nı (v1) kullanma](storage-import-export-tool-how-to-v1.md)
#### [İçeri/Dışarı Aktarma Aracı’nı ayarlama](storage-import-export-tool-setup-v1.md)
#### [Sabit sürücüleri içeri aktarma işine hazırlama](storage-import-export-tool-preparing-hard-drives-import-v1.md)
##### [İçeri aktarma işlemi sırasında özellikleri ve meta verileri ayarlama](storage-import-export-tool-setting-properties-metadata-import-v1.md)
##### [Sabit sürücüleri içeri aktarma işine hazırlamak için örnek iş akışı](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)
##### [İçeri aktarma işlerinde sık kullanılan komutlar için hızlı başvuru](storage-import-export-tool-quick-reference-v1.md)
#### [Dışarı aktarma işi için sürücü kullanımının önizlemesini yapma](storage-import-export-tool-previewing-drive-usage-export-v1.md)
#### [Kopyalama günlük dosyalarıyla iş durumunu gözden geçirme](storage-import-export-tool-reviewing-job-status-v1.md)
#### [Bir içeri aktarma işini onarma](storage-import-export-tool-repairing-an-import-job-v1.md)
#### [Bir dışarı aktarma işini onarma](storage-import-export-tool-repairing-an-export-job-v1.md)
#### [İçeri/Dışarı Aktarma Aracı ile ilgili sorunları giderme](storage-import-export-tool-troubleshooting-v1.md)
#### [İçeri/Dışarı Aktarma hizmet bildirimi dosyası biçimi](storage-import-export-file-format-manifest.md)
#### [İçeri/Dışarı Aktarma hizmeti meta veriler ve özellikler dosyası biçimi](storage-import-export-file-format-metadata-and-properties.md)
#### [İçeri/Dışarı Aktarma hizmeti günlük dosyası biçimi](storage-import-export-file-format-log.md)
### [Azure İçeri/Dışarı Aktarma hizmeti REST API’sini kullanma](storage-import-export-using-the-rest-api.md)
#### [İçeri aktarma işi oluşturma](storage-import-export-creating-an-import-job.md)
#### [Dışarı aktarma işi oluşturma](storage-import-export-creating-an-export-job.md)
#### [Bir iş için durum bilgilerini alma](storage-import-export-retrieving-state-info-for-a-job.md)
#### [İşleri numaralandırma](storage-import-export-enumerating-jobs.md)
#### [İşleri iptal etme ve silme](storage-import-export-cancelling-and-deleting-jobs.md)
#### [Sürücü bildirimlerini yedekleme](storage-import-export-backing-up-drive-manifests.md)
#### [İçeri/Dışarı Aktarma işleri için tanılama ve hata kurtarma](storage-import-export-diagnostics-and-error-recovery.md)
# Başvuru
## [PowerShell](/powershell/storage)
## [Azure CLI](/cli/azure/storage)
## .NET
### [Resource Manager](/dotnet/api/microsoft.azure.management.storage)
### [Veri taşıma](/dotnet/api/microsoft.windowsazure.storage.datamovement)
### [Bloblar, Kuyruklar, Tablolar ve Dosyalar](https://msdn.microsoft.com/library/azure/mt347887.aspx)
## [Java](http://azure.github.io/azure-storage-java/)
## [Node.js](http://azure.github.io/azure-storage-node)
## [Ruby](http://azure.github.io/azure-storage-ruby)
## [Python](https://azure-storage.readthedocs.io/en/latest/index.html)
## [C++](http://azure.github.io/azure-storage-cpp)
## [iOS](http://azure.github.io/azure-storage-ios/)
## [Android](http://azure.github.io/azure-storage-android)
## REST
### [Bloblar, Kuyruklar, Tablolar ve Dosyalar](/rest/api/storageservices/fileservices/azure-storage-services-rest-api-reference)
### [Kaynak sağlayıcısı](/rest/api/storagerp)
### [İçeri/Dışarı Aktarma](/rest/api/storageimportexport)

# İlgili
## Klasik Portal
### [Depolama hesabı oluşturma](storage-create-storage-account-classic-portal.md)
### [Ölçümleri etkinleştirme ve görüntüleme](storage-enable-and-view-metrics-classic-portal.md)
### [İzleme, tanılama ve sorun giderme](storage-monitoring-diagnosing-troubleshooting-classic-portal.md)
### [Sorun giderme öğreticisi](storage-e2e-troubleshooting-classic-portal.md)

# Kaynaklar
## [Fiyatlandırma](https://azure.microsoft.com/pricing/details/storage/blobs/)
## [Azure Depolama İstemci Araçları](storage-explorers.md)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/windows-azure-storage)
## [Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)
## [Hizmet güncelleştirmeleri](https://azure.microsoft.com/updates/?product=storage)
## [Videolar](https://azure.microsoft.com/documentation/videos/index/?services=storage)

## Azure Depolama Gezgini
### [Depolama Gezgini (Önizleme)](../vs-azure-tools-storage-manage-with-storage-explorer.md)
### [Depolama Gezgini (Önizleme) ile blob’ları yönetme](../vs-azure-tools-storage-explorer-blobs.md)
### [Depolama Gezgini’ni (Önizleme) Azure dosya depolama ile kullanma](../vs-azure-tools-storage-explorer-files.md)

## NuGet paketleri
### [.NET için Azure Depolama İstemcisi](https://www.nuget.org/packages/WindowsAzure.Storage/)
### [Azure Depolama Veri Taşıma Kitaplığı](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)
### [Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)

## Kaynak kod
### .NET
#### [Blob, kuyruk, tablo ve dosya](https://github.com/Azure/azure-storage-net)
#### [Veri taşıma](https://github.com/Azure/azure-storage-net-data-movement)
#### [Kaynak sağlayıcısı](https://github.com/Azure/azure-sdk-for-net)
### [Node.js](http://azure.github.io/azure-storage-node/)
### [Java](https://github.com/Azure/azure-storage-java)
### [C++](https://github.com/Azure/azure-storage-cpp)
### [PHP](https://github.com/Azure/azure-storage-php)
### [Ruby](https://github.com/Azure/azure-storage-ruby)
### [Python](https://github.com/Azure/azure-storage-python)
### [iOS](https://github.com/Azure/azure-storage-ios)
