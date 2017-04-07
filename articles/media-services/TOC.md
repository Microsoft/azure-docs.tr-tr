# [Genel Bakış](media-services-overview.md)
## [Kavramlar ](media-services-concepts.md)

# Başlarken
## [Hesap oluşturma ve yönetme](media-services-portal-create-account.md)
## [Geliştirme ortamınızı kurma](media-services-set-up-computer.md)
###[.NET](media-services-dotnet-how-to-use.md)
###[REST](media-services-rest-how-to-use.md)  
## Program aracılığıyla bağlanma
### [.NET](media-services-dotnet-connect-programmatically.md)
### [REST](media-services-rest-connect-programmatically.md)

## İsteğe bağlı video gönderme
### [Portal](media-services-portal-vod-get-started.md)
### [.NET SDK](media-services-dotnet-get-started.md)
### [Java](media-services-java-how-to-use.md)
### [REST](media-services-rest-get-started.md)
## Canlı akış gerçekleştirme
### [Portal](media-services-portal-live-passthrough-get-started.md)
### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)

# Nasıl yapılır?
## Yönet
### Hesaplar
#### [PowerShell](media-services-manage-with-powershell.md)
#### [REST](/rest/api/media/mediaservice)
### Varlıklar
#### [.NET](media-services-dotnet-manage-entities.md)
#### [REST](media-services-rest-manage-entities.md)
### [Akış uç noktaları](media-services-streaming-endpoints-overview.md)
#### [Portal](media-services-portal-manage-streaming-endpoints.md)
#### [.NET](media-services-dotnet-manage-streaming-endpoints.md)
### Depolama
#### [Depolama erişim anahtarlarını değiştirdikten Sonra Media Services’ı güncelleştirme](media-services-roll-storage-access-keys.md)
#### [Birden çok depolama hesabında yer alan varlıkları yönetme](meda-services-managing-multiple-storage-accounts.md)
### [Kotalar ve sınırlamalar](media-services-quotas-and-limitations.md)

## İçerik yükleme
### Bir hesaba dosya yükleme
#### [Portal](media-services-portal-upload-files.md)
#### [.NET](media-services-dotnet-upload-files.md)
#### [REST](media-services-rest-upload-files.md)
### [Aspera ile büyük dosyaları karşıya yükleme](media-services-upload-files-with-aspera.md)
### [StorSimple ile dosyaları karşıya yükleme](media-services-upload-files-from-storsimple.md)
### [Mevcut blobları kopyalama](media-services-copying-existing-blob.md)

## [İçerik kodlama](media-services-encode-asset.md)
### [Kodlayıcıları karşılaştır](media-services-compare-encoders.md)
### [Kodlamanızın hızını ve eşzamanlılığını yönetme](media-services-manage-encoding-speed.md)
### Media Encoder Standard (MES)
#### [Media Encoder Standard Biçimleri ve Kod Çözücüleri](media-services-media-encoder-standard-formats.md)
#### [Otomatik olarak bit hızı merdiveni oluşturmak için MES kullanma](media-services-autogen-bitrate-ladder-with-mes.md)
#### Media Encoder Standard ile kodlama
##### [Portal](media-services-portal-encode.md)
##### [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
##### [REST](media-services-rest-encode-asset.md)
#### [MES ile gelişmiş kodlama](media-services-advanced-encoding-with-mes.md)
##### [Media Encoder Standard hazır ayarlarını özelleştirme](media-services-custom-mes-presets-with-dotnet.md)
##### [.NET ile Media Encoder Standard kullanarak küçük resim oluşturma](media-services-dotnet-generate-thumbnail-with-mes.md)
##### [Videoları Media Encoder Standard ile kırpma](media-services-crop-video.md)
#### MES Şemaları
##### [Media Encoder Standard şeması](media-services-mes-schema.md)
##### [Giriş meta verileri](media-services-input-metadata-schema.md)
##### [Çıkış meta verileri](media-services-output-metadata-schema.md)
#### [MES Ön Ayarları](media-services-mes-presets-overview.md) 
##### [H264 Çoklu Bit Hızı 1080p Ses 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md)
##### [H264 Çoklu Bit Hızı 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md)
##### [H264 Çoklu Bit Hızı 16x9 SD Ses 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md)
##### [H264 Çoklu Bit Hızı 16x9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md)
##### [iOS için H264 Çoklu Bit Hızı 16x9](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md)
##### [H264 Çoklu Bit Hızı 4K Ses 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md)
##### [H264 Çoklu Bit Hızı 4K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md)
##### [H264 Çoklu Bit Hızı 4x3 SD Ses 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md)
##### [H264 Çoklu Bit Hızı 4x3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md)
##### [iOS için H264 Çoklu Bit Hızı 4x3](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md)
##### [H264 Çoklu Bit Hızı 720p Ses 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md)
##### [H264 Çoklu Bit Hızı 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md)
##### [H264 Tekli Bit Hızı 1080p Ses 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md)
##### [H264 Tekli Bit Hızı 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md)
##### [H264 Tekli Bit Hızı 16x9 SD Ses 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md)
##### [H264 Tekli Bit Hızı 16x9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md)
##### [H264 Tekli Bit Hızı 4K Ses 5.1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md)
##### [H264 Tekli Bit Hızı 4K](media-services-mes-preset-H264-Single-Bitrate-4K.md)
##### [H264 Tekli Bit Hızı 4x3 SD Ses 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md)
##### [H264 Tekli Bit Hızı 4x3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md)
##### [H264 Tekli Bit Hızı 720p Ses 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md)
##### [H264 Tekli Bit Hızı 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md)
##### [Android için H264 Tekli Bit Hızı 720p](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md)
##### [Android için H264 Tekli Bit Hızı Yüksek Kaliteli SD](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md)
##### [Android için H264 Tekli Bit Hızı Düşük Kaliteli SD](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md)
### Media Encoder Premium İş Akışı
#### [Media Encoder Premium İş Akışı Biçimleri ve Kod Çözücüleri](media-services-premium-workflow-encoder-formats.md)
#### Media Encoder Premium İş Akışı ile kodlama
##### [Media Encoder Premium İş Akışı](media-services-encode-with-premium-workflow.md)
##### [Media Encoder Premium İş Akışı öğreticileri](media-services-media-encoder-premium-workflow-tutorials.md)
##### [İş Akışı Tasarımcısı ile Gelişmiş Kodlama İş Akışları Oluşturma](media-services-workflow-designer.md)
##### [Birden fazla giriş ile premium iş akışı](media-services-media-encoder-premium-workflow-multiplefilesinput.md)
### [fMP4 öbekleri oluşturan bir görev oluşturma](media-services-generate-fmp4-chunks.md)
### Medya işlemcileri
#### [.NET](media-services-get-media-processor.md)
#### [REST](media-services-rest-get-media-processor.md)
### [Hata kodları](media-services-encoding-error-codes.md)
### Kullanım Dışı
#### [Statik paketleme ve şifreleme](media-services-static-packaging.md)

## [Canlı akış](media-services-manage-channels-overview.md)
### [Şirket içi kodlayıcılar](media-services-live-streaming-with-onprem-encoders.md)
#### [Portal](media-services-portal-live-passthrough-get-started.md)
#### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)
#### [REST](https://docs.microsoft.com/rest/api/media/operations/channel)
### [Bulut kodlayıcıyla canlı akış](media-services-manage-live-encoder-enabled-channels.md)
#### [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
#### [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
### [Şirket içi kodlayıcıları bulut kodlayıcıyla kullanılmak üzere yapılandırma](media-services-live-encoders-overview.md)
#### [Elemental Live kodlayıcı](media-services-configure-elemental-live-encoder.md)
#### [FMLE kodlayıcı](media-services-configure-fmle-live-encoder.md)
#### [NewTek TriCaster kodlayıcı](media-services-configure-tricaster-live-encoder.md)
#### [Wirecast kodlayıcı](media-services-configure-wirecast-live-encoder.md)
### [Uzun süren işlemleri çözme](media-services-dotnet-long-operations.md)
### [Bölünmüş MP4 canlı alma belirtimi](media-services-fmp4-live-ingest-overview.md)

## [Koruma](media-services-content-protection-overview.md)
### [Portalda içerik korumayı yapılandırma](media-services-portal-protect-content.md)
### [Akışınız için AES-128 şifresiz anahtarını yapılandırma](media-services-protect-with-aes128.md)
### [İçeriklerinizi şifrelemek için depolama şifreleme ile REST’i kullanma](media-services-rest-storage-encryption.md)
### [Media Services PlayReady lisans şablonuna genel bakış](media-services-playready-license-template-overview.md)
### [Widevine lisans şablonuna genel bakış](media-services-widevine-license-template-overview.md)
### [DRM lisansı teslimi](media-services-deliver-keys-and-licenses.md)
### [Media Services’da Widevine lisanslarını teslim etmek için iş ortaklarıyla çalışma](media-services-licenses-partner-integration.md)
### [PlayReady ve/ya Widevine dinamik ortak şifreleme işlemini kullanma](media-services-protect-with-drm.md)
### [Apple FairPlay ile korunan HLS içeriğinizin akışını yapma ](media-services-protect-hls-with-fairplay.md)
### [Çoklu DRM ve Access Control ile CENC](media-services-cenc-with-multidrm-access-control.md)
### [Media Services’da Widevine lisanslarını teslim etmek için Axinom kullanma  ](media-services-axinom-integration.md)
### [Media Services’da Widevine lisanslarını teslim etmek için castLabs kullanma](media-services-castlabs-integration.md)

### Varlık teslimi
#### Varlık teslim ilkelerini yapılandırma
##### [.NET](media-services-dotnet-configure-asset-delivery-policy.md)
##### [REST](media-services-rest-configure-asset-delivery-policy.md)
### ContentKeys oluşturma
#### [.NET](media-services-dotnet-create-contentkey.md)
#### [REST](media-services-rest-create-contentkey.md)
### İçerik anahtarı yetkilendirme ilkesini yapılandırma
#### [Portal](media-services-portal-configure-content-key-auth-policy.md)
#### [.NET](media-services-dotnet-configure-content-key-auth-policy.md)
#### [REST](media-services-rest-configure-content-key-auth-policy.md)

## [Çözümleme](media-services-analytics-overview.md)
### [Portalı kullanarak medya çözümleme](media-services-portal-analyze.md)
### [Indexer 2 ile işleme](media-services-process-content-with-indexer2.md)
### [Indexer ile işleme](media-services-index-content.md)
### [Hyperlapse ile işleme](media-services-hyperlapse-content.md)
### [Face Detector ile işleme](media-services-face-and-emotion-detection.md)
### [Motion Detector ile işleme](media-services-motion-detection.md)
### [Face Redactor ile İşleme](media-services-face-redaction.md)
#### [Face Redactor kılavuzu](media-services-redactor-walkthrough.md)
### [Video Thumbnails ile işleme](media-services-video-summarization.md)
### [OCR ile işleme](media-services-video-optical-character-recognition.md)

## [Telemetri yapılandırma](media-services-telemetry-overview.md)
###[.NET](media-services-dotnet-telemetry.md)
###[REST](media-services-rest-telemetry.md)

## Ölçek
### [Medya İşleme](media-services-scale-media-processing-overview.md)
#### [Portal](media-services-portal-scale-media-processing.md)
#### [.NET](media-services-dotnet-encoding-units.md)
#### [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
### Akış Uç Noktaları
#### [Portal](media-services-portal-scale-streaming-endpoints.md)

## [İçerik teslim etme](media-services-deliver-content-overview.md)
### [Dinamik paketleme](media-services-dynamic-packaging-overview.md)
### [Filtrelere ve dinamik bildirimlere genel bakış](media-services-dynamic-manifest-overview.md)
#### [.NET ile filtre oluşturma](media-services-dotnet-dynamic-manifest.md)
#### [REST ile filtre oluşturma](media-services-rest-dynamic-manifest.md)
### [Media Services Uzantısında CDN Önbelleğe Alma İlkesi](../cdn/cdn-caching-policy.md?toc=%2fazure%2fmedia-services%2ftoc.json)
### İçerik yayımlama
#### [Portal](media-services-portal-publish.md)
#### [.NET](media-services-deliver-streaming-content.md)
#### [REST](media-services-rest-deliver-streaming-content.md)
### [İndirme ile teslim etme](media-services-deliver-asset-download.md)
### [Akış yük devretme senaryosu](media-services-implement-failover.md)

## Tüketme
### [Mevcut yürütücüler ile medya kayıttan yürütme](media-services-playback-content-with-existing-players.md)
### [Media Player ile medya kayıttan yürütme](media-services-develop-video-players.md)
### Diğer kayıttan yürütme seçenekleri
#### [Kesintisiz akış Windows Mağazası uygulaması](media-services-build-smooth-streaming-apps.md)
#### [DASH.js ile HTML5 Uygulaması](media-services-embed-mpeg-dash-in-html5.md)
#### [Adobe Open Source Media Framework yürütücüleri](media-services-use-osmf-smooth-streaming-client-plugin.md)
### [İstemci tarafına reklam ekleme](media-services-inserting-ads-on-client-side.md)
### [Microsoft† Kesintisiz Akış İstemci Taşıma Kitini Lisanslama](media-services-sspk.md)

## Tümleştirme
### [Azure İşlevleri’ni Media Services ile kullanma](media-services-dotnet-how-to-use-azure-functions.md)
### [Azure İşlevleri ile Media Services örnekleri](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)

## İzleme
### İşin ilerleme durumunu denetleme
#### [REST](media-services-rest-check-job-progress.md)
#### [Portal](media-services-portal-check-job-progress.md)
#### [.NET](media-services-check-job-progress.md)
### [Kuyruk depolama ile iş bildirimlerini izleme](media-services-dotnet-check-job-progress-with-queues.md)
### [Web kancaları ile iş bildirimlerini izleme](media-services-dotnet-check-job-progress-with-webhooks.md)

## Sorun giderme
### [Sık sorulan sorular](media-services-frequently-asked-questions.md)
### [Canlı akış sorun giderme kılavuzu](media-services-troubleshooting-live-streaming.md)
### [Hata kodları](media-services-error-codes.md)
### [Yeniden deneme mantığı](media-services-retry-logic-in-dotnet-sdk.md)

# Başvuru
## [PowerShell (Resource Manager)](/powershell/resourcemanager/azurerm.media/v0.3.1/azurerm.media)
## [PowerShell (Hizmet Yönetimi)](/powershell/servicemanagement/azure.compute/v3.1.0/azure.compute)
## [.NET](/dotnet/api/microsoft.windowsazure.mediaservices.client)
## [REST](/rest/api/media)

# Kaynaklar
## [Sürüm notları](media-services-release-notes.md)
## [Fiyatlandırma](https://azure.microsoft.com/pricing/details/media-services/)
## [Azure Media Services Topluluğu](media-services-community.md)
## [Videolar](https://azure.microsoft.com/resources/videos/index/?services=media-services)
