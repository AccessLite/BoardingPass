# Bite Your Thumb: Week 1 MVP
---

### [FoaasAPI](http://www.foaas.com/)

### Models

1. `Foaas`
2. `FoaasOperation`
3. `FoaasField`

#### Model Details

#### `Foaas.swift` 
Corresponds to an endpoints return `JSON`: 

```
// Enpoint: /awesome/:from
// GET http://www.foaas.com/awesome/louis
{
  "message": "This is Fucking Awesome.",
  "subtitle": "- louis"
}
```

#### `FoaasOperation.swift`
Corresponds to an "operation" json when making a request to the `/operations` endpoint

```
Endpoint: /operations
GET http://www.foaas.com/operations
[
  {
      "name": "Awesome",
      "url": "/awesome/:from",
      "fields": [
       {
          "name": "From",
          "field": "from"
        }
      ]
  },
  { ... another operation ... },
  { ... another operation ... }
]
```

`FoaasField.swift` - 
Corresponds to the "field" dictionary in a `FoaasOperation` (see above)

```
    "fields": [
       {
          "name": "From",
          "field": "from"
        }
      ]
```    

---
### Protocols 

To be added in a file called `FoaasProtocols.swift`

```swift
protocol JSONConvertible {
  init?(json: [String : AnyObject])
  func toJson() -> [String : AnyObject]
}

// This protocol's intention is to allow you to convert a model into Data to be stored in UserDefaults
// This protocol relies on JSONConvertible working to convert models into Swift-types (Array, Dict, String, etc.). 
protocol DataConvertible {
  init?(data: Data)
  func toData() throws -> Data
}
```

__Required Conformance__

`Foaas: JSONConvertible, CustomStringConvertible`

`FoaasOperation: JSONConvertible, DataConvertible`

`FoaasField: JSONConvertible, CustomStringConvertible`

---

### API Calls

#### `FoaasAPIManager`
The `FoaasAPIManager` will be used to make calls to our endpoints. For now (at least), we're really only going to need a small set of functions
One will just make a `GET` request to any endpoint we pass to it, and the other will retrieve our list of `FoaasOperation` for storage and later use. 

__Guidelines__
- Not a singleton, all class functions
- Suggest creating a `private static let defaultSession = URLSession(configuration: .default)` for API Calls
- For now, you may have some hard-coded URL's to ensure functionality
- You only need 2 functions at this moment:
  - `internal class func getFoaas(url: URL, completion: @escaping (Foaas?)->Void)`
  - `internal class func getOperations(completion: @escaping ([FoaasOperation]?)->Void )`
  
---

### Data Management

#### `FoaasDataManager`
The goal of the data manager is to save `[FoaasOperation]` (making sure that your `DataConvertible` conformance is working) to user defaults,
load the `[FoaasOperation]` and return a `Bool` to indicate success, and to delete all stored `[FoaasOperation]`. 

__Requirements__
1. Keep your calls to the `AppDelegate`
2. Check to see if you can load stored `[FoaasOperation]` before you attempt to make an API call to store them. 
3. Take special care to adhere to [DRY](https://en.wikipedia.org/wiki/Don't_repeat_yourself) principles. 

__Guidelines__

- Singleton instance named `shared`
- All instance functions, only three should be done for now: 
  - `save(operations: [FoaasOperation])`
  - `load() -> Bool`
  - `deleteStoredOperations()`
- Minimize `for-in` loops in favor of `flatMap`
- Include a few `static let`, for example I'd recommend: 
  - `private static let operationsKey: String = "FoaasOperationsKey"`
  - `private static let defaults = UserDefaults.standard`
  - `internal private(set) var operations: [FoaasOperation]?`
  
---
### View Controllers
1. `FoaasViewController` will act as our main screen, see PM's notes on design
  - Adding something like a `tapGesture` may help you debug text layout if each tap makes a single API request to a random endpoint
2. `FoaasOperationsTableViewController` will display our list of `FoaasOperation`
 
