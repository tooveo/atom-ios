# ironSource.atom SDK for IOS

[![License][license-image]][license-url]
[![Docs][docs-image]][docs-url]
[![Build status][travis-image]][travis-url]
[![Coverage Status][coverage-image]][coverage-url]

atom-ios is the official [ironSource.atom](http://www.ironsrc.com/data-flow-management) SDK for the IOS platform.

- [Signup](https://atom.ironsrc.com/#/signup)
- [Documentation](https://ironsource.github.io/atom-ios/)
- [Sending an event](#Using-the-IronSource-API-to-send-events)

#### Using the IronSource API to send events 
Example of sending an event in Swift:
```swift
class ViewController: UIViewController {
    var api_: IronSourceAtom?
    var apiTracker_: IronSourceAtomTracker?

    override func viewDidLoad() {
        super.viewDidLoad()

        // initialize atom-sdk api object
        self.api_ = IronSourceAtom()
        // print debug info in console
        self.api_!.enableDebug(true)
        self.api_!.setAuth("<YOUR_AUTH_KEY>")

        self.apiTracker_ = IronSourceAtomTracker()
        self.apiTracker_!.enableDebug(true)
        self.apiTracker_!.setAuth("<YOUR_AUTH_KEY>")
        self.apiTracker_!.setBulkSize(<BULK_COUNT>)
        self.apiTracker_!.setBulkBytesSize(<MAX_BULK_SIZE_IN_BYTES>)
        self.apiTracker_!.setEndpoint("https://track.atom-data.io/")
    }

    @IBOutlet var textArea_: UITextView!
    
    func postCallback(response: Response) {
        print("from class method \(self.test)")
        let errorStr = (response.error == "") ? "nil" : "\"\(response.error)\""
        let dataStr = (response.data == "") ? "nil" : "\"\(response.data)\""
        let statusStr = "\(response.status)"
        
        let responseStr = "{\n\t\"error\": \(errorStr), " +
            "\n\t\"data\": \(dataStr), " +
            "\n\t\"status\": \(statusStr)\n}"
        
        dispatch_sync(dispatch_get_main_queue()) {
            self.textArea_.text = responseStr
        }

    }
   	
   	// send single POST request
    @IBAction func buttonPostPressed(sender: UIButton) {
        self.api_!.putEvent("<YOUR_STREAM_NAME>",
                            data: "{\"test\":\"test\"}",
                            method: HttpMethod.POST, callback: postCallback)
    }

    // senf Bulk request
    @IBAction func buttonBulkPressed(sender: UIButton) {
        func callback(response: Response) {
            let errorStr = (response.error == "") ? "nil" : "\"\(response.error)\""
            let dataStr = (response.data == "") ? "nil" : "\"\(response.data)\""
            let statusStr = "\(response.status)"
            
            let responseStr = "{\n\t\"error\": \(errorStr), " +
                    "\n\t\"data\": \(dataStr), " +
                    "\n\t\"status\": \(statusStr)\n}"
            
            dispatch_sync(dispatch_get_main_queue()) {
                self.textArea_.text = responseStr //Yay!
            }
        }
        
        // check health of server
        self.api_!.health(nil)

        self.api_!.putEvents("<YOUR_STREAM_NAME>",
                            data: ["{\"test\":\"test\"}", "{\"test\":\"test\"}"],
                            callback: callback)
    }

    // track event
    @IBAction func buttonTackPressed(sender: UIButton) {
        self.apiTracker_!.track("<YOUR_STREAM_NAME>",
                                data: "{\"test\":\"test\"}")
    }
    
    // flush all data in tracker
    @IBAction func buttonFlushPressed(sender: UIButton) {
        self.apiTracker_!.flush()
    }

```

### Example

![alt text][example]

### License
MIT

[docs-image]: https://img.shields.io/badge/docs-latest-blue.svg
[docs-url]: https://ironsource.github.io/atom-ios/
[travis-image]: https://travis-ci.org/ironSource/atom-ios.svg?branch=master
[travis-url]: https://travis-ci.org/ironSource/atom-ios
[coverage-image]: https://coveralls.io/repos/github/ironSource/atom-ios/badge.svg?branch=master
[coverage-url]: https://coveralls.io/github/ironSource/atom-ios?branch=master
[license-image]: https://img.shields.io/badge/license-MIT-blue.svg?style=flat-square
[license-url]: LICENSE
[example]: https://cloud.githubusercontent.com/assets/1713228/15971662/08129c62-2f43-11e6-980d-66d36a41f961.png "example"