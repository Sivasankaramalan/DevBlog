## Parallel Execution of Appium Tests using Selenium Grid

### Selenium Grid

> Selenium-Grid allows running your tests on different machines against different (browsers/devices) in parallel. That is, running multiple tests at the same time against different machines running different browsers and operating systems.

**When to use selenium grid:**

Generally speaking, there are two reasons why you might want to use Selenium-Grid.

- To run your tests on multiple devices having different configurations or running different mobile operating systems like **ANDROID/IOS**.
- To reduce the time it takes for the test suite to complete a test pass.

**Prerequisites for Selenium Grid Configuration**
- Ensure that Java is already installed in your machine and configured
- Ensure selenium/Appium is configured properly
- Know the path of Selenium-Standalone Jar 

**Start Selenium Grid Server**
- Open terminal
- Navigate to the location/folder in terminal using the command  
`cd /Users/Desktop/SeleniumGrid`
- Then start the grid using the command 

```
 Java –jar selenium-standalone-server-3.7.0.jar –role hub
``` 
**JSON Files**

JSON files are required to read data from a configuration file related to different devices.

Below mentioned are the sample JSON files.

*To create JSON file --->open any text editor --->add below mentioned data—and save ---> rename file to .json as extension*

Sample JSON file For Device 1 : (File name:- nodeConfigDevice1.json)
```
{
  "capabilities": [
    {
      "browserName": "Samsung S9",
      "version": "8.0.1",
      "maxInstances": 1,
      "platform": "ANDROID",
      "deviceName": "b112212"
    }
  ],
  "configuration": {
    "cleanUpCycle": 2000,
    "timeout": 30000,
    "proxy": "org.openqa.grid.selenium.proxy.DefaultRemoteProxy",
    "host": "127.0.0.1",
    "port": 4723,
    "maxSession": 1,
    "register": true,
    "registerCycle": 10000,
    "hubPort": 4444,
    "hubHost": "127.0.0.1"
  }
}
``` 
Sample For Device 2:- (File Name:-NodeConfigDevice2.json)

```
{
  "capabilities": [
    {
      "browserName": "Pixel 2XL",
      "version": "9.0",
      "maxInstances": 1,
      "platform": "ANDROID",
      "deviceName": "192.168.0.23:5555"
    }
  ],
  "configuration": {
    "cleanUpCycle": 2000,
    "timeout": 30000,
    "proxy": "org.openqa.grid.selenium.proxy.DefaultRemoteProxy",
    "host": "127.0.0.1",
    "port": 4723,
    "maxSession": 1,
    "register": true,
    "registerCycle": 10000,
    "hubPort": 4444,
    "hubHost": "127.0.0.1"
  }
}
``` 

** Parallel device execution**

Create a sample.xml file for parallel device execution

```
<suite name="SmokeSuite" verbose="1" parallel="tests" thread-count="5">
    <!-- <listeners> <listener class-name="org.uncommons.reportng.HTMLReporter" 

  /> <listener class-name="org.uncommons.reportng.JUnitXMLReporter" /> </listeners> -->
    <tests>
        <test name="run on device 1">
            <classes>
                <parameter name="port" value="4723" />
                <parameter name="device" value="8775bef9"></parameter>
                <class name="BDD_OwnersAPP.desiredCapabilities"></class>
            </classes>
        </test>
        <test name="run on device 2">
            <classes>
                <parameter name="port" value="4725"></parameter>
                <parameter name="device" value="192.168.56.101:5555 "></parameter>
                <parameter name="bp" value="4728"></parameter>
                <class name="BDD_OwnersAPP.desiredCapabilities"></class>
            </classes>
        </test>
    </tests>
</suite>

``` 
**Sample Java code to read .xml file:**

```
@Parameters({
    "port",
    "device"})

public void forDeviceOne() throws MalformedURLException, InterruptedException {

    String port = "";

    String device = "";

    final File appDir = new File("/Users/path/to/the/app");

    final File app = new File(appDir, "Test_App.apk");

    final DesiredCapabilities capabilities = new DesiredCapabilities();

    capabilities.setCapability("deviceName", "Samsung S9+");

    capabilities.setCapability("udid", "b112121");

    capabilities.setCapability("udid", device);

    capabilities.setCapability("platformVersion", "8.0.1");

    capabilities.setCapability("platformName", "Android");

    capabilities.setCapability("app", app.getAbsolutePath());

    capabilities.setCapability("appPackage", "com.test.app");

    capabilities.setCapability("appActivity", "com.test.app.Activity");

    driver = new AndroidDriver < MobileElement > (new URL("http://127.0.0.1:" + port + "/wd/hub"), capabilities);

    wait = new WebDriverWait(driver, 20);

    System.out.println("Initialize: " + driver);

}
``` 
**Procedure to Start Grid and Appium Server for parallel execution:**
 
- To start Selenium Grid please follow above-mentioned steps & ensure corresponding devices on which you are trying to start the scripts are connected
Note:- Here  the scripts are running simultaneously at 2 places i.e. (Real device) & (Emulator)

- Now start the Appium server for device 1 
- Open a new terminal and type in below mentioned commands to start the server

```
appium -a 127.0.0.1 -p 4726 --bootstrap-port 4729 -U b112212 --nodeconfig /Users/Desktop/nodeConfigDevice1.json
                                                              OR
appium -p 4726 --nodeconfig /Users//Desktop/nodeConfigDevice1.json

``` 
- Now start the Appium server for device 2

Open a new terminal and type in below mentioned commands to start the server

```
appium -a 127.0.0.1 -p 4723 --bootstrap-port 4729 -U 192.168.0.23:5555 --nodeconfig /Users/Desktop/nodeConfigDevice2.json
                                                              OR
appium -p 4723 --nodeconfig /Users//Desktop/nodeConfigDevice2.json

``` 
- Run the test Script

*Below mentioned is the command in the selenium grid to ensure that data is cleared before the start of every scenario:*

```
appium -a 127.0.0.1 -p 4723 --full-reset --bootstrap-port 4728 -U b112212 --nodeconfig /Users/Desktop/nodeConfigDevice1.json

``` 
**Full Reset Capability:**

It stops the app and clears app data and uninstalls the app before the start of each scenario.

**No Reset Capability**
- It does not stops the app and does not clear the app data and doesn't even uninstall the app.
- Refer to the [link](https://appium.io/docs/en/writing-running-appium/other/reset-strategies/index.html) for Reset capability reference.




