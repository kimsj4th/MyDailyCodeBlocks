# total_2019






# 05152019 

### Dictionary
```swift
// MARK: Reference []
    private var eyeBrowTilts = [
        FacialExpression.EyeBrows.relaxed: 0.5,
        .furrowed: -0.5,
        .normal: 0.0
    ]
    
    private func updateUI() {
        switch expression.eyes {
        case .open:
            myFaceView.eyeOpen = true
        case .closed:
            myFaceView.eyeOpen = false
        case .squinting:
            myFaceView.eyeOpen = false
        }
        myFaceView.mouthCurvature = mouthCurvatures[expression.mouth] ?? 0.0
        myFaceView.eyeBrowTilt = eyeBrowTilts[expression.eyeBrows] ?? 0.0
    }
```

### UIPinchGestureRecognizer
> UIKit > Touches, Presses, and Gestures > [UIPinchGestureRecognizer](https://developer.apple.com/documentation/uikit/uipinchgesturerecognizer)

```swift
// MARK: Reference []
    @objc func changeScale(_ recognizer: UIPinchGestureRecognizer) {
        switch recognizer.state {
        case .changed:
            fallthrough
        case .ended:
            scale *= recognizer.scale
            recognizer.scale = 1.0
        default:
            break
        }
    }
```

```swift
myFaceView.addGestureRecognizer(UIPinchGestureRecognizer(target: myFaceView, action: #selector(FaceView.changeScale(_:))))
```


### Update UI
```swift
// MARK: Reference []
@IBOutlet weak var myFaceView: FaceView! {
        didSet {
            updateUI()
        }
    }
    var expression = FacialExpression(eyes: .closed, eyeBrows: .relaxed, mouth: .smirk) {
        didSet {
            updateUI()
        }
    }
```


### NSObject

```swift
class UserModel: NSObject {
    @objc var profileImageURL: String?
    @objc var userName: String?
}
```
```swift
@objcMembers
class UserModel: NSObject {
    var profileImageURL: String?
    var userName: String?
}
```

# 05142019 

### DataSnapshot
>Firebase Documentation > Guides > RealTime Database > iOS > [Read and Write Data](https://firebase.google.com/docs/database/ios/read-and-write)

```swift
Database.database().reference().child("users").observe(DataEventType.value, with: { (snapshot) in
            self.array.removeAll()
            for child in snapshot.children {
                let fchild = child as! DataSnapshot
                let userModel = UserModel()
                userModel.setValuesForKeys(fchild.value as! [String: Any])

                self.array.append(userModel)
            }
            DispatchQueue.main.async {
                self.tableView.reloadData()
            }
        })
```

### URLSession
> Foundation > URL Loading System > `URLSession` > [dataTask(with:completionHandler:)](https://developer.apple.com/documentation/foundation/urlsession/1410330-datatask)

```swift
        URLSession.shared.dataTask(with: URL(string: array[indexPath.row].profileImageURL!)!) { (data, response, error) in
            DispatchQueue.main.async {
                imageView.image = UIImage(data: data!)
                imageView.layer.cornerRadius = imageView.frame.size.width / 2
                imageView.clipsToBounds = true
            }
        }.resume()
```


### Multiple Returns 
```swift
func sortEvenOdd(numbers: [Int]) -> (evens: [Int], odds: [Int]) {
    var evens = [Int]()
    var odds = [Int]()
    for number in numbers {
        if number % 2 == 0 {
            evens.append(number)
        } else {
            odds.append(number)
        }
    }
    return(evens, odds)
}

let aBunchOfNumbers = [10,1,4,3,57,43,84,27,156,111]
let theSortedNumbers = sortEvenOdd(numbers: aBunchOfNumbers)
print("The even numbers are: \(theSortedNumbers.evens) \nthe odd numbers are: \(theSortedNumbers.odds)")
```

### NumberFormatter
> Foundation > Numbers, Data, and Basic Values > [NumberFormatter](https://developer.apple.com/documentation/foundation/numberformatter)

```swift
let numberFormatter: NumberFormatter = {
    let formatter = NumberFormatter()
    formatter.numberStyle = .decimal
    formatter.minimumFractionDigits = 0
    formatter.maximumFractionDigits = 1
    return formatter
}()
```
```swift
guard let fahrenheit = numberFormatter.string(from: NSNumber(value: originFahrenheit)) else {
    return UITableViewCell()
}
```


# 05132019 


### Bezier Curve
>  UIKit > Drawing > `UIBezierPath` > `addCurve(to:controlPoint1:controlPoint2:)`

```swift
    private func pathForMouth() -> UIBezierPath {
        let mouthWidth = skullRadius / Rations.skullRadiusToMouthWidth
        let mouthHeight = skullRadius / Rations.skullRadiusToMouthHeight
        let mouthOffset = skullRadius / Rations.skullRadiusToMouthOffset
        
        let mouthRect = CGRect(x: skullCenter.x - mouthWidth/2,
                               y: skullCenter.y + mouthOffset,
                               width: mouthWidth,
                               height: mouthHeight)
        let mouthCurvature: Double = 1.0 // 1 full smile, -1 full frown
        let smileOffset = CGFloat(max(-1, min(mouthCurvature, 1))) * mouthRect.height
        let start = CGPoint(x: mouthRect.minX, y: mouthRect.minY)
        let end = CGPoint(x: mouthRect.maxX, y: mouthRect.minY)
        let cp1 = CGPoint(x: mouthRect.minX + mouthRect.width / 3, y: mouthRect.minY + smileOffset)
        let cp2 = CGPoint(x: mouthRect.maxX - mouthRect.width / 3, y: mouthRect.minY + smileOffset)
        
        let path = UIBezierPath()
        path.move(to: start)
        path.addCurve(to: end, controlPoint1: cp1, controlPoint2: cp2)
        path.lineWidth = 5.0
        
        return path
    }
```

### NSAttributedString
> UIKit > Text Storage > `NSAttributedString` > `NSAttributedString.Key` > `underlineStyle`  
> Foundation > Strings and Text > NSMutableAttributedString > addAttribute(_:value:range:)  
> Foundation > Strings and Text > NSAttribtuedString  



```swift
        let label = UILabel.init(frame: CGRect(x: 24, y:400, width: 100, height: 40))
        label.backgroundColor = .lightGray
        label.center.y = view.bounds.height / 2
        label.isHighlighted = true
        label.highlightedTextColor = UIColor.red
        let attributedString = NSMutableAttributedString.init(string: "Apply UnderLining")
        attributedString.addAttribute(NSAttributedString.Key.underlineStyle,
                                      value: 1,
                                      range: NSRange.init(location: 0,
                                                          length: attributedString.length))
        label.attributedText = attributedString
        label.sizeToFit()
```

### NotificationCenter, keyboardFrameBeginUserInfoKey
> Foundation > Notifications > `NotificationCenter`   
> UIKit > Touches, Presses, and Gestures > `UIResponder` > `keyboardFrameBeginUserInfoKey`


```swift
NotificationCenter.default.addObserver(self, selector: #selector(keyboardWasShown(_:)), name: UIResponder.keyboardDidShowNotification, object: nil)
```

```swift
  guard let info = notification.userInfo,
        let keyboardFrameValue = info[UIResponder.keyboardFrameBeginUserInfoKey] as? NSValue else
        { return }
        let keyboardFrame = keyboardFrameValue.cgRectValue
        let keyboardSize = keyboardFrame.size
        
        let contentInsets = UIEdgeInsets(top: 0.0, left: 0.0, bottom: keyboardSize.height + 50, right: 0.0)
        myScrollView.contentInset = contentInsets
        myScrollView.scrollIndicatorInsets = contentInsets
```

# 05122019 

### Error Handling 
```swift 
enum VendingMachineError: Error {
    case invalidSelection
    case insufficientFunds(coinsNeeded: Int)
    case outOfStock
}

class VendingMachine {
    var inventory = [
        "Candy Bar": Item(price: 12, count: 7),
        "Chips": Item(price: 10, count: 4),
        "Pretzels": Item(price: 7, count: 11)
    ]
    var coinsDeposited = 0
    
    func vend(itemNamed name: String) throws {
        guard let item = inventory[name] else {
            throw VendingMachineError.invalidSelection
        }
        
        guard item.count > 0 else {
            throw VendingMachineError.outOfStock
        }
        
        guard item.price <= coinsDeposited else {
            throw VendingMachineError.insufficientFunds(coinsNeeded: item.price - coinsDeposited)
        }
        
        coinsDeposited -= item.price
        
        var newItem = item
        newItem.count -= 1
        inventory[name] = newItem
        
        print("Dispensing \(name)")
    }
}
```

### Codable, Codingkey 
> Foundation > Archives and Serialization > Encoding and Decoding Custom Types

```swift
struct Coordinate: Codable {
    var latitude: Double
    var longitude: Double
}

struct Landmark: Codable {
    var name: String
    var foundingYear: Int
    var location: Coordinate
    var vantagePoints: [Coordinate]
    var metaData: [String: String]
    var website: URL?
    
    enum CodingKeys: String, CodingKey {
        case name = "title"
        case foundingYear = "founding_date"
        
        case location, vantagePoints, metaData, website
    }
}
```

### Alternating Row Color 

```swift
if indexPath.row % 2 == 0 {
            cell.backgroundColor = UIColor.lightGray.withAlphaComponent(0.1)
        } else {
            cell.backgroundColor = UIColor.white
        }
```
```swift
        weatherInfo.sort() { $0.celsius > $1.celsius }
        self.tableView.reloadData()
```

# 05112019 

### UIBezierPath 
> UIKit > Drawing > `UIBezierPath` 

```swift
 let skullRadius = min(bounds.size.width, bounds.size.height) / 2
        let skullCenter = CGPoint(x: bounds.midX, y: bounds.midY)
        let skull = UIBezierPath(arcCenter: skullCenter,
                                 radius: skullRadius,
                                 startAngle: 0.0,
                                 endAngle: CGFloat(2*Double.pi),
                                 clockwise: false)
        skull.lineWidth = 5.0
        UIColor.red.set()
        skull.stroke()
        
```


### Tab Bar 
```swift 
        if let tabBarController = self.window?.rootViewController as? UITabBarController {
            if let tabBarItems = tabBarController.tabBar.items {
                tabBarItems[0].image = UIImage(named: "calendar")?.withRenderingMode(UIImage.RenderingMode.alwaysOriginal)
                tabBarItems[0].title = "Calendar" 
                }
```
```swift
override func touchesEnded(_ touches: Set<UITouch>, with event: UIEvent?) {
        let tabBar = self.tabBarController?.tabBar
        UIView.animate(withDuration: TimeInterval(0.15)) {
            // tabBar?.isHidden = (tabBar?.isHidden == true) ? false : true
            tabBar?.alpha = (tabBar?.alpha == 1 ? 0 : 1)
        }
    }
```


### UIRefreshControl
> UIKit > Views and Controls > Table Views > `UIRefreshControl`

```swift
        refreshIndicator.addTarget(self, action: #selector(pullDownRefresh), for: .valueChanged)
        self.tableView.addSubview(refreshIndicator)
        refreshIndicator.attributedTitle = NSAttributedString(string: "Refreshing Data ...")
```
```swift
 @objc func pullDownRefresh() {
        self.tableView.reloadData()
        refreshIndicator.endRefreshing()
    }
```

### WebView
```java
        WebSettings webSettings = mWebView.getSettings();
        webSettings.setJavaScriptEnabled(true);
        mWebView.setWebViewClient(new WebViewClient(){
            @Override
            public void onPageFinished(WebView view, String url) {
                Toast.makeText(MainActivity.this, "로딩끝", Toast.LENGTH_SHORT).show();
            }
        });
```
```java
public boolean onOptionsItemSelected(MenuItem item) {
        switch (item.getItemId()) {
            case R.id.action_back:
                if (mWebView.canGoBack()) {
                    mWebView.goBack();
                }
                return  true;
            case R.id.action_forward:
                if (mWebView.canGoForward()){
                    mWebView.goForward();
                }
                return true;
            case R.id.acrion_refresh:
                mWebView.reload();
                return true;
        }
        return super.onOptionsItemSelected(item);
    }
```

# 05102019 

### URLComponents, URLQueryItem
```swift
private static func flickrURL(method: Method, parameters: [String: String]?) -> URL {
        var componets = URLComponents(string: baseURLString)!
        var queryItems = [URLQueryItem]()
        let baseParams = [
            "method": method.rawValue,
            "format": "json",
            "nojsoncallback": "1",
            "api_key": APIKey
        ]        
        for (key, value) in baseParams {
            let item = URLQueryItem(name: key, value: value)
            queryItems.append(item)
        } 
        if let additionalParams = parameters {
            for (key, value) in additionalParams {
                let item = URLQueryItem(name: key, value: value)
                queryItems.append(item)
            }
        }
        componets.queryItems = queryItems
        return componets.url!
    }
```

### CABasicAnimation
```swift
 func flash() {
        let flash = CABasicAnimation(keyPath: "opacity")
        flash.duration = 0.5
        flash.fromValue = 1
        flash.toValue = 0.1
        flash.timingFunction = CAMediaTimingFunction(name: CAMediaTimingFunctionName.easeInEaseOut)
        flash.autoreverses = true
        flash.repeatCount = 3
        layer.add(flash, forKey: nil)
    }
```

### func prepare(for: UIStoryboardSegue, sender: Any?) 
>UIKit > UIViewController > prepare(for:sender:)

```swift
override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
    guard let nextViewController = segue.destination as? ThirdViewController else { return }
    guard let cell = sender as? CustomTableViewCell else { return }
    nextViewController.cityName = cell.cityNameLabel?.text
    guard let guardImage = cell.weatherImage?.image else { return }
    nextViewController.cityWeatherImageString = getCellImageString(guardImage)
}
```

### getView(int, View, ViewGrop)
```java 
@Override
    public View getView(int position, View convertView, ViewGroup parent) {

       convertView = LayoutInflater.from(parent.getContext())
               .inflate(R.layout.item_weather, parent, false);

        ImageView weatherImage = convertView.findViewById(R.id.weather_image);
        TextView citytext = convertView.findViewById(R.id.city_text);
        TextView temptext = convertView.findViewById(R.id.temp_text);

        Weather weather = mData.get(position);
        citytext.setText(weather.getCity());
        temptext.setText(weather.getTemp());
        weatherImage.setImageResource(mWeatherImageMap.get(weather.getWeather()));

        return convertView;
    }
```



# 05092019 


### Label Shadow
```swift 
        label01.layer.shadowOffset = CGSize(width: 3, height: 3)
        label01.layer.shadowOpacity = 0.7
        label01.layer.shadowRadius = 2
```

### Property Observer 

```swift
class StepCounter {
    var totalSteps: Int = 0 {
        willSet(newTotalSteps) {
            print("About to set totalSteps to \(newTotalSteps)")
        }
        didSet {
            if totalSteps > oldValue  {
                print("Added \(totalSteps - oldValue) steps")
            }
        }
    }
}
let stepCounter = StepCounter()
stepCounter.totalSteps = 200
// About to set totalSteps to 200
// Added 200 steps
stepCounter.totalSteps = 360
// About to set totalSteps to 360
// Added 160 steps
stepCounter.totalSteps = 896
// About to set totalSteps to 896
// Added 536 steps
```
```swift
 	var value: Int = 0 {
        didSet {
            self.centerLabel.text = String(value)
        }
    }
    public var leftTitle: String = "⬇︎" {
        didSet {
            self.leftButton.setTitle(leftTitle, for: .normal)
        }
    }
```

### Equatable, Comparable

```swift
struct Employee: Equatable, Comparable {
    var firstName: String
    var lastName: String
    var jobTitle: String
    var phoneNumber: String
    static func ==(lhs: Employee, rhs: Employee) -> Bool {
        return lhs.firstName == rhs.firstName &&
        lhs.lastName == rhs.lastName &&
        lhs.jobTitle == rhs.jobTitle &&
        lhs.phoneNumber == rhs.phoneNumber
    }
    static func < (lhs: Employee, rhs: Employee) -> Bool {
        return lhs.lastName < rhs.lastName
    }
}
``` 


# 05082019 
-


```swift 
enum TriStateSwitch {
    case off, low, high
    mutating func next() {
        switch self {
        case .off:
            self = .low
        case .low:
            self = .high
        case .high:
            self = .off
        }
    }
}
var ovenLight = TriStateSwitch.low
ovenLight.next()
// ovenLight is now equal to .high
ovenLight.next()
// ovenLight is now equal to .off
```
---

```swift 
enum LineBreakMode {
        case wordWrapping, charWrapping, clipping, truncatingHead, truncatingTail, trucatingMiddle
        mutating func next() {
            switch self {
            case .wordWrapping:
                self = .charWrapping
            case .charWrapping:
                self = .clipping
            case .clipping:
                self = .truncatingHead
            case .truncatingHead:
                self = .truncatingTail
            case .truncatingTail:
                self = .trucatingMiddle
            case .trucatingMiddle:
                self = .wordWrapping
            }
        }
    }
```
---

```swift

@objc func actionButtonEvent() {
        labelStatus.next()
        button.setTitle("Case: \(labelStatus)", for: .normal)
        switch labelStatus {
        case .wordWrapping:
        label.lineBreakMode = .byWordWrapping
        case .charWrapping:
            label.lineBreakMode = .byCharWrapping
        case .clipping:
            label.lineBreakMode = .byClipping
        case .truncatingHead:
            label.lineBreakMode = .byTruncatingHead
        case .trucatingMiddle:
            label.lineBreakMode = .byTruncatingMiddle
        case .truncatingTail:
            label.lineBreakMode = .byTruncatingTail
        }
    }
```
---

```swift
 typealias PropertyList = AnyObject
 var program: PropertyList {
        get {
            return internalProgram as CalculatorBrain.PropertyList
        }
        set {
            accumulator = 0.0
            pending = nil
            internalProgram.removeAll()
            if let arrayOfOps = newValue as? [AnyObject] {
                for op in arrayOfOps {
                    if let operand = op as? Double {
                        setOperand(operand: operand)
                    } else if let operation = op as? String {
                        performOperation(symbol: operation)
                    }
                }
            }
        }
    }

```