# Thumbnail-From-Video-Swift-iOS
Generate video thumbnail with cache for local videos in swift iOS.

# Code: 

```swift

    func getVideoThumbnail(url: URL) -> UIImage? {
        //let url = url as URL
        let request = URLRequest(url: url)
        let cache = URLCache.shared
        
        if let cachedResponse = cache.cachedResponse(for: request), let image = UIImage(data: cachedResponse.data) {
            return image
        }
        
        let asset = AVAsset(url: url)
        let imageGenerator = AVAssetImageGenerator(asset: asset)
        imageGenerator.appliesPreferredTrackTransform = true
        imageGenerator.maximumSize = CGSize(width: 250, height: 120)
        
        var time = asset.duration
        time.value = min(time.value, 2)
        
        var image: UIImage?
        
        do {
            let cgImage = try imageGenerator.copyCGImage(at: time, actualTime: nil)
            image = UIImage(cgImage: cgImage)
        } catch { }
        
        if let image = image, let data = image.pngData(), let response = HTTPURLResponse(url: url, statusCode: 200, httpVersion: nil, headerFields: nil) {
            let cachedResponse = CachedURLResponse(response: response, data: data)
            cache.storeCachedResponse(cachedResponse, for: request)
        }
        
        return image
    }

```

# Usage:
```swift

yourImageView.image = self.getVideoThumbnail(url: mediaURL)

```
