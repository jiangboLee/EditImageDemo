# EditImageDemo
iOS 图片放大缩小，选择裁剪区域

![1.gif](https://github.com/jiangboLee/EditImageDemo/blob/master/1.gif)

#### 核心代码
###### 就一个scrollView文件，可以直接拖到项目中使用~
```swift
//
//  EditImageCropView.swift
//  EditImageDemo
//
//  Created by Lee on 2018/9/5.
//  Copyright © 2018年 Lee. All rights reserved.
//

import UIKit

class EditImageCropView: UIScrollView {

    var imageView = UIImageView()
    var imageSize: CGSize?
    var image: UIImage! = nil {
        didSet {
            if image != nil {
                if !imageView.isDescendant(of: self) {
                    imageView.alpha = 1.0
                    addSubview(imageView)
                }
            } else {
                imageView.image = nil
                return
            }
            let imageSize = self.imageSize ?? image.size
            /// 宽比例
            let ratioW = frame.width / imageSize.width
            /// 高比例
            let ratioH = frame.height / imageSize.height
            if ratioH > ratioW {
                imageView.frame = CGRect(origin: .zero, size: CGSize(width: imageSize.width * ratioH, height: frame.height))
            } else {
                imageView.frame = CGRect(origin: .zero, size: CGSize(width: frame.width, height: imageSize.height * ratioW))
            }
            
            contentOffset = CGPoint(x: imageView.center.x - center.x, y: imageView.center.y - center.y)
            imageView.image = image
            zoomScale = 1.0
            
        }
    }
    
    override init(frame: CGRect) {
        super.init(frame: frame)
        setupUI()
    }
    
    required init?(coder aDecoder: NSCoder) {
        super.init(coder: aDecoder)
        setupUI()
    }
    func setupUI() {
        backgroundColor = UIColor.black
        frame.size = .zero
        clipsToBounds = true
        imageView.alpha = 0
        imageView.frame = .zero
        maximumZoomScale = 2.0
        minimumZoomScale = 0.8
        showsVerticalScrollIndicator = false
        showsHorizontalScrollIndicator = false
        bounces = true
        bouncesZoom = true
        scrollsToTop = false
        delegate = self
        setNeedsLayout()
    }
}

extension EditImageCropView: UIScrollViewDelegate {
    func viewForZooming(in scrollView: UIScrollView) -> UIView? {
        return imageView
    }
    
    func scrollViewDidZoom(_ scrollView: UIScrollView) {
        let boundsSize = scrollView.bounds.size
        var contentsFrame = imageView.frame
        if contentsFrame.size.width < boundsSize.width {
            contentsFrame.origin.x = (boundsSize.width - contentsFrame.size.width) / 2.0
        } else {
            contentsFrame.origin.x = 0.0
        }
        if contentsFrame.size.height < boundsSize.height {
            contentsFrame.origin.y = (boundsSize.height - contentsFrame.size.height) / 2
        } else {
            contentsFrame.origin.y = 0.0
        }
        imageView.frame = contentsFrame
    }
    
    func scrollViewDidEndZooming(_ scrollView: UIScrollView, with view: UIView?, atScale scale: CGFloat) {
        contentSize = CGSize(width: imageView.frame.width > frame.width ? imageView.frame.width + 1.0 : frame.width + 1.0, height: imageView.frame.height > frame.height ? imageView.frame.height + 1.0 : frame.height + 1.0)
    }
}

```
