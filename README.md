#PubNub 3.5 client for Go 1.0.3, 1.1

###Important changes in this version:
The package name has been modified to "messaging" from "pubnubMessaging". 

###Features

* Supports multiplexing, UUID, SSL, Encryption, Proxy, and godoc
* This version is not backward compatible. The major change is in the func calls. A new parameter "error callback" is added to the major functions of the pubnub class.
* The client now supports:
* Error Callback: All the error messages are routed to this callback channel
* Resume on reconnect
* You can now "Subscribe with timetoken"
* An example of Disconnect/Retry has been added in the example 
* Multiple messages received in a single response from the server will now be split into individual messages
* Non 200 response will now be bubbled to the client
* PAM: To use the PAM features in the example please enable PAM from the Pubnub admin console (https://admin.pubnub.com) and replace the publish, subscribe and secret keys in example/pubnubExample.go (line 124).

###Quick Start Video

We've put together a quick HOWTO video here http://vimeo.com/93523019

###Get Package

* Use the command `go get github.com/pubnub/go/messaging` to download and install the package

###Run the example
* Built using Eclipse IDE (juno) 
* Install golang plugin for Eclipse
* Using Eclipse Project Explorer browse to the directory `$GOPATH/src/github.com/pubnub/go/messaging/example`, where `$GOPATH` is the workspaces directory of go.
* Run `pubnubExample.go` as a "go application"
* Look for the application in the "Console" of the Eclipse IDE

###Running Unit tests (instructions for Mac/Linux, for other dev environments the instructions are similar)

* Open Terminal.
* Change the directory to 
`<eclipse-workspace>/src/github.com/pubnub/go/messaging/tests.`
* Run the command `go test -i` to install the packages. 
* And then run the command `go test` to run test cases.

###Use pubnub in your project

* Install golang plugin for Eclipse.
* Use the command go get github.com/pubnub/go/messaging to download and install the package.
* Open terminal/command prompt. Browse to the directory ` $GOPATH/src/github.com/pubnub/go/messaging/ `
* Run the command `go install`.
* Go to eclipse and create a new "go project". Enter the project name.
* Create a new "go file" in the "src" directory of the new project. For this example choose the "Command Source File" under the "Source File Type" with "Empty Main Function".
* Click Finish
* On this file in eclipse.
* Under import add the 2 lines

```go
import (
    // Other imports...
    "fmt"
    "github.com/pubnub/go/messaging"
)
```

* And under main add the following line

```go
fmt.Println("PubNub Api for go;", messaging.VersionInfo())
```

* Run the example as a "go application"
* This application will print the version info of the PubNub Api.
* For the detailed usage of the PunNub API, please refer to the rest of the ReadMe or the pubnubExample.go file under ` $GOPATH/src/github.com/pubnub/go/messaging/example `


In addition to Eclipse, this has also been tested with Go 1.0.3 on Linux using IntelliJ IDEA 12.

###Demo Console App
We've included a demo console app which documents all the functionality of the client, for example:

* Subscribe
* Subscribe with timetoken
* Publish
* Presence
* Detailed History
* Here_Now
* Unsubscribe
* Presence-Unsubscribe
* Time
* Disconnect/Retry
* GrantSubscribe
* RevokeSubscribe
* AuditSubscribe
* GrantPresence
* RevokePresence
* AuditPresence
* SetAuthKey
* GetAuthKey
* Exit

###Quick Implementation Examples

#### ParseResponse

This function is a utility function used in the examples below to parse
a message received from a Pubnub chanel. You will want to adapt it to
your own needs.

```go
func ParseResponse(channel chan []byte){
    for {
        value, ok := <-channel
        if !ok {
            break
        }
        if string(value) != "[]"{
            fmt.Println(fmt.Sprintf("Response: %s ", value))
            fmt.Println("");
        }
    }
}
```

#### ParseErrorResponse

This is a utility function to parse an error response and display it.
You will want to adapt it to your own needs.

```go
func ParseErrorResponse(channel chan []byte){
    for {
        value, ok := <-channel
        if !ok {  
            fmt.Println("")            
            break
        }
        if string(value) != "[]"{
            if(_displayError){
                fmt.Println(fmt.Sprintf("Error Callback: %s", value))
                fmt.Println("")
            }
        }
    }
}
```

#### Init

Initialize a new Pubnub instance.

```go
        pubInstance := messaging.NewPubnub(<YOUR PUBLISH KEY>, <YOUR SUBSCRIBE KEY>, <SECRET KEY>, <CIPHER>, <SSL ON/OFF>, <UUID>)
```

#### Publish

```go
        //Init pubnub instance

        var errorChannel = make(chan []byte)
        var callbackChannel = make(chan []byte)
        go pubInstance.Publish(<pubnub channel>, <message to publish>, callbackChannel, errorChannel)
        go ParseResponse(callbackChannel)
        go ParseErrorResponse(errorChannel) 
        // please goto the end of this file see the implementations of ParseResponse and ParseErrorResponse
```

#### Subscribe

```go
        //Init pubnub instance

        var errorChannel = make(chan []byte)
        var subscribeChannel = make(chan []byte)
        go pubInstance.Subscribe(<pubnub channels, multiple channels can be separated by comma>, <timetoken, should be an empty string in this case>, subscribeChannel, <this field is FALSE for subscribe requests>, errorChannel)
        go ParseResponse(subscribeChannel)  
        go ParseErrorResponse(errorChannel)  
        // please goto the end of this file see the implementations of ParseResponse and ParseErrorResponse
```

#### Subscribe with timetoken

```go
        //Init pubnub instance

        var errorChannel = make(chan []byte)
        var subscribeChannel = make(chan []byte)
        go pubInstance.Subscribe(<pubnub channel, multiple channels can be separated by comma>, <timetoken to init the request with>, subscribeChannel, <this field is FALSE for subscribe requests>, errorChannel)
        go ParseResponse(subscribeChannel)  
        go ParseErrorResponse(errorChannel)  
        // please goto the end of this file see the implementations of ParseResponse and ParseErrorResponse
```

#### Presence

```go
        //Init pubnub instance

        var errorChannel = make(chan []byte)
        var presenceChannel = make(chan []byte)
        go pubInstance.Subscribe(<pubnub channel, multiple channels can be separated by comma>, <timetoken, should be an empty string in this case>, presenceChannel, <this field is TRUE for subscribe requests>, errorChannel)
        go ParseResponse(subscribeChannel)  
        go ParseErrorResponse(errorChannel)  
        // please goto the end of this file see the implementations of ParseResponse and ParseErrorResponse
```

#### Detailed History

```go
        //Init pubnub instance

        var errorChannel = make(chan []byte)
        var channelCallback = make(chan []byte)
        go pubInstance.History(<pubnub channel>, <no of items to fetch>, <start time>, <end time>, false, channelCallback, errorChannel)
        //example: go _pub.History(<pubnub channel>, 100, 0, 0, false, channelCallback, errorChannel)
        go ParseResponse(channel)
        go ParseErrorResponse(errorChannel)  
        // please goto the end of this file see the implementations of ParseResponse and ParseErrorResponse
```

#### Here_Now

```go
        //Init pubnub instance

        var errorChannel = make(chan []byte)
        var channelCallback = make(chan []byte)
        go pubInstance.HereNow(<pubnub channel>, channelCallback, errorChannel)
        go ParseResponse(channelCallback)
        go ParseErrorResponse(errorChannel) 
        // please goto the end of this file see the implementations of ParseResponse and ParseErrorResponse
```

####  Unsubscribe

```go
        //Init pubnub instance

        var errorChannel = make(chan []byte)
        var channelCallback = make(chan []byte)
        go pubInstance.Unsubscribe(<pubnub channels, multiple channels can be separated by comma>, channelCallback, errorChannel)
        go ParseUnsubResponse(channelCallback)
        go ParseErrorResponse(errorChannel) 
        // please goto the end of this file see the implementations of ParseResponse and ParseErrorResponse
```

#### Presence-Unsubscribe

```go
        //Init pubnub instance

        var errorChannel = make(chan []byte)
        var channelCallback = make(chan []byte)
        go pubInstance.PresenceUnsubscribe(<pubnub channels, multiple channels can be separated by comma>, channelCallback, errorChannel)
        go ParseUnsubResponse(channelCallback)
        go ParseErrorResponse(errorChannel) 
        // please goto the end of this file see the implementations of ParseResponse and ParseErrorResponse
```

#### Time

```go
        //Init pubnub instance

        var errorChannel = make(chan []byte)
        var channelCallback = make(chan []byte)
        go pubInstance.GetTime(channelCallback, errorChannel)
        go ParseResponse(channelCallback)
        go ParseErrorResponse(errorChannel) 
        // please goto the end of this file see the implementations of ParseResponse and ParseErrorResponse
```

#### Disconnect/Retry
```go
        //Init pubnub instance

        pubInstance.CloseExistingConnection() 
```

#### GrantSubscribe
```go
        //Init pubnub instance

        var errorChannel = make(chan []byte)
        var pamChannel = make(chan []byte)
        go pub.GrantSubscribe(channels, true, true, 60, pamChannel, errorChannel)
        go parseResponsePam(pamChannel)
        go parseErrorResponse(errorChannel)
```

#### RevokeSubscribe
```go
        //Init pubnub instance

        var errorChannel = make(chan []byte)
        var pamChannel = make(chan []byte)
        go pub.GrantSubscribe(channels, false, false, -1, pamChannel, errorChannel)
        go parseResponsePam(pamChannel)
        go parseErrorResponse(errorChannel)
```

#### AuditSubscribe
```go
        //Init pubnub instance

        var errorChannel = make(chan []byte)
        var pamChannel = make(chan []byte)
        go pub.AuditSubscribe(channels, pamChannel, errorChannel)
        go parseResponsePam(pamChannel)
        go parseErrorResponse(errorChannel)
```

#### GrantPresence
```go
        //Init pubnub instance

        var errorChannel = make(chan []byte)
        var pamChannel = make(chan []byte)
        go pub.GrantPresence(channels, true, true, 60, pamChannel, errorChannel)
        go parseResponsePam(pamChannel)
        go parseErrorResponse(errorChannel)
```

#### RevokePresence
```go
        //Init pubnub instance

        var errorChannel = make(chan []byte)
        var pamChannel = make(chan []byte)
        go pub.GrantPresence(channels, false, false, -1, pamChannel, errorChannel)
        go parseResponsePam(pamChannel)
        go parseErrorResponse(errorChannel)
```

#### AuditPresence
```go
        //Init pubnub instance

        var errorChannel = make(chan []byte)
        var pamChannel = make(chan []byte)
        go pub.AuditPresence(channels, pamChannel, errorChannel)
        go parseResponsePam(pamChannel)
        go parseErrorResponse(errorChannel)
```

#### SetAuthKey
```go
        //Init pubnub instance

        pub.SetAuthenticationKey("authkey")
```

#### GetAuthKey
```go
        //Init pubnub instance

        fmt.Println(pub.GetAuthenticationKey())
```

#### Exit

```go
        //Init pubnub instance

        pubInstance.Abort()  
```

