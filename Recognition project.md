##Fruit Identification


**Description：**

Different countries have their own specialty fruits. With various technology development, there are more and more novel fruits in our world. We design a project which can recognize almost all kinds of fruits from all over the world based on deep learning method. If one user taste uncommon fruits which he doesn’t know the name when he is traveling, he can use this app to know which kind of fruit they are by just uploading the photo of this kind of fruits to the app.

####Project process:

***Getting Started:***

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes.

***Create The coreML Model:***
######1.preparation:
a.Install Turi Create in our computer;

b.Create our own dataset;
######2. Collect the dataset:

	We gain our fruits dataset from the websites, here is the link:
	(http://yangxian10.github.io/2015/11/05/dataset-cv/)

	There are 30 categories total, around 1500 images in our dataset.



######3.  Train our coreML dataset;

![the model we have finished training](/Users/mac/Desktop/Screen Shot 2019-05-03 at 10.24.17.png)



```

data["fruitType"] = data["path"].apply(
    lambda path:
    "Acerola" if "acerolas" in path
    else "Apple" if "apples" in path
    else "Apricot" if "apricots" in path
     else "Avocado" if "avocados" in path
      else "Banana" if "bananas" in path
      else "Blackberry" if "blackberries" in path
      else "Blueberry" if "blueburries" in path
       else "Cantaloupe" if "cantaloupes" in path
      else "Cherry" if "cherries" in path
      else "Coconut" if "coconuts" in path
      else "Fig" if "figs" in path
      else "Grapefruit" if "grapefruits" in path
      else "Grape" if "grapes" in path
      else "Guava" if "guava" in path
       else "Kiwifruit" if "kiwifruit" in path
        else "Lemon" if "lemons" in path
        else "Lime" if "limes" in path
        else "Mango" if "mangos" in path
        else "Olive" if "olives" in path
        else "Orange" if "oranges" in path
        else "Passionfruit" if "passionfruit" in path
        else "Peach" if "peaches" in path
        else "Pear" if "pears" in path
        else "Pineapple" if "pineapples" in path
        else "Plum" if "plums" in path
        else "Pomegranate" if "pomegranates" in path
        else "Raspberry" if "raspberries" in path
        else "Strawberry" if "strawberries" in path
        else "Tomato" if "tomatoes" in path
        else "Watermelon" )

data.save("fruit.sframe")



```
























######4.Test our model and export to CoreML model;
![Test Model](https://raw.githubusercontent.com/LuckyStarRain/OwnDataset_TuriCreate/master/Screen%20Shot%202019-05-03%20at%2010.24.17.png)
######5.The following are some part of our view controller source code:
```


    func imagePickerController(_ picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [String : Any]) {
        picker.dismiss(animated: true)
        classifier.text = "Analyzing Image..."
        guard let image = info["UIImagePickerControllerOriginalImage"] as? UIImage else {
            return
        }

        UIGraphicsBeginImageContextWithOptions(CGSize(width: 224, height: 224), true, 2.0)
        image.draw(in: CGRect(x: 0, y: 0, width: 224, height: 224))
        let newImage = UIGraphicsGetImageFromCurrentImageContext()!
        UIGraphicsEndImageContext()

        let attrs = [kCVPixelBufferCGImageCompatibilityKey: kCFBooleanTrue, kCVPixelBufferCGBitmapContextCompatibilityKey: kCFBooleanTrue] as CFDictionary
        var pixelBuffer : CVPixelBuffer?
        let status = CVPixelBufferCreate(kCFAllocatorDefault, Int(newImage.size.width), Int(newImage.size.height), kCVPixelFormatType_32ARGB, attrs, &pixelBuffer)
        guard (status == kCVReturnSuccess) else {
            return
        }

        CVPixelBufferLockBaseAddress(pixelBuffer!, CVPixelBufferLockFlags(rawValue: 0))
        let pixelData = CVPixelBufferGetBaseAddress(pixelBuffer!)

        let rgbColorSpace = CGColorSpaceCreateDeviceRGB()
        let context = CGContext(data: pixelData, width: Int(newImage.size.width), height: Int(newImage.size.height), bitsPerComponent: 8, bytesPerRow: CVPixelBufferGetBytesPerRow(pixelBuffer!), space: rgbColorSpace, bitmapInfo: CGImageAlphaInfo.noneSkipFirst.rawValue) //3

        context?.translateBy(x: 0, y: newImage.size.height)
        context?.scaleBy(x: 1.0, y: -1.0)

        UIGraphicsPushContext(context!)
        newImage.draw(in: CGRect(x: 0, y: 0, width: newImage.size.width, height: newImage.size.height))
        UIGraphicsPopContext()
        CVPixelBufferUnlockBaseAddress(pixelBuffer!, CVPixelBufferLockFlags(rawValue: 0))
        imageView.image = newImage
        guard let prediction = try? model.prediction(image: pixelBuffer!) else {
            return
        }

        classifier.text = "I think this is a \(prediction.fruitType)."

    }
}


```
***Result:***

Our app can recognize diverse kinds of fruits close to 100% correctly.

***Problems***

The problem we met was the format of images can’t match the requirement of application.

***The solution to the problem:***
Change some formats of pictures to jpeg or PNG.
