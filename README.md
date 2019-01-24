<h2>Getting started with Selenium + NodeJS</h2>
Selenium is a great tool to automate our functional tests on websites and web applications in our favorite language. With <a href="https://crossbrowsertesting.com">CrossBrowserTesting</a>, you can use Selenium and JavaScript to run automated browser tests on thousands of real mobile and desktop browsers in the cloud.
<div class="blue-alert">

You'll need a Username and Authkey to run your tests on CrossBrowserTesting. To get yours, sign up for a <a href="https://crossbrowsertesting.com/freetrial"><b>free trial</b></a> or purchase a <a href="https://crossbrowsertesting.com/pricing"><b>plan</b></a>.

</div>
<h3>Installing Selenium</h3>
To get started, make sure you have Selenium’s language bindings for Javascript installed and ready. You can find the official resource for this here:

<a href="http://www.seleniumhq.org/download/">http://www.seleniumhq.org/download/</a>

Or if you have NPM loaded on your machine (comes with NodeJS distribution), you can type the following into the terminal:
<pre><code>npm install selenium-webdriver</code></pre>
<h3>Running Your First Test</h3>
Once you have Selenium-WebDriver in place, you should be ready to start testing. Copy the following script into your text-editor of choice:
<pre><code>
"use strict"; 
var webdriver = require("selenium-webdriver"),
SeleniumServer = require("selenium-webdriver/remote").SeleniumServer;
 
var cbtHub = "http://hub.crossbrowsertesting.com:80/wd/hub";

var username ='YOUR_USERNAME'; //replace with your email address 
var authkey = 'YOUR_AUTHKEY'; //replace with your authkey  

var caps = {
    name : 'Basic Test Example',
    build :  '1.0',
    version : '70', 
    platform : 'Windows 10', 
    screen_resolution : '1366x768',
    record_video : 'true',
    record_network : 'false',
    browserName : 'Chrome',
    username : username,
    password : authkey
};


async function basicExample(){
    try{
        var driver = new webdriver.Builder()
            .usingServer(cbtHub)
            .withCapabilities(caps)
            .build(); 


        await driver.get('http://crossbrowsertesting.github.io/selenium_example_page.html');

        await driver.getTitle().then(function(title) {
                    console.log("The title is: " + title)
            });

        driver.quit();
    }

    catch(err){
        handleFailure(err, driver)
    }

}

basicExample();

function handleFailure(err, driver) {
     console.error('Something went wrong!\n', err.stack, '\n');
     driver.quit();
} 
</code></pre>
<h3>Choosing Browsers</h3>
To choose your environment, you can select from Browsers, Operating System, Resolutions, and Devices. You can use our easy <a href="https://app.crossbrowsertesting.com/selenium/run">Selenium Wizard</a> to select your configuration or hit our <a href="http://crossbrowsertesting.com/api/v3/selenium/browsers">browser list endpoint</a>.
<h3>Recording Videos</h3>
For superior debugging capabilities, CrossBrowserTesting offers the ability to record a video of your Selenium test session. You can find a copy of your <a href="https://app.crossbrowsertesting.com/selenium">test results here</a>.
<pre><code>  var caps = {
    'record_video': 'true'
}</code></pre>
&nbsp;
<h3>Recording Network</h3>
To record the network packets during your test for performance debugging, set the following to "true".
<pre><code>  var caps = {
    'record_network': 'true'
}</code></pre>
&nbsp;
<h2>Running A Local Test</h2>
With our Local Tunnel, you can run a test on a local URL or behind your company's firewall. There are two different ways to connect the CrossBrowserTesting local tunnel: our <a href="https://www.npmjs.com/package/cbt_tunnels">NodeJS client</a> and our <a href="https://chrome.google.com/webstore/detail/crossbrowsertesting-local/ldabplgpogjknofonmccpbgeoolbcbfm">Chrome Extension</a>.

You can read more about setting up, starting, and using your tunnel connection <a href="https://help.crossbrowsertesting.com/local-connection/general/local-tunnel-overview/#enabling-local-connection">here.</a>

When a tunnel is open, any automated test can access websites locally, behind a firewall, or across a proxy. There is no need to set a capability during your test.
<h2>Running Tests In Parallel</h2>
To speed your automated testing, you can run your tests in parallel, making use of several browsers or devices at one time. To run parallel tests using Node we'll use the Flow function.
<pre><code>

var username ='YOUR_USERNAME'; //replace with your email address 
var authkey = 'YOUR_AUTHKEY'; //replace with your authkey  

var webdriver = require('selenium-webdriver'),
    SeleniumServer = require('selenium-webdriver/remote').SeleniumServer,
    request = require('request');

var remoteHub = "http://" + username + ":" + authkey + "@hub.crossbrowsertesting.com:80/wd/hub";

var browsers = [
   { browserName: 'Chrome', platform: 'Windows 10', version: '64', screen_resolution: '1366x768' },
   { browserName: 'Chrome', platform: 'Mac OSX 10.14', version: '71x64', screen_resolution: '1366x768' },
   { browserName: 'Internet Explorer', platform: 'Windows 8.1', version: '11', screen_resolution: '1366x768' }
];

var flows = browsers.map(function(browser) {

    var caps = {
        name : 'Node Parallel Example',
        browserName : browser.browserName,
        version : browser.version, 
        platform : browser.platform,     
        screen_resolution : browser.screen_resolution,       
        username : username,
        password : authkey
    };
    async function parallelExample(){
        try{
            var driver = new webdriver.Builder()
                 .usingServer(remoteHub)
                 .withCapabilities(caps)
                 .build();

            await driver.getSession().then(function(session){
                var sessionId = session.id_; //need for API calls
                console.log('Session ID: ', sessionId); 
                console.log('See your test run at: https://app.crossbrowsertesting.com/selenium/' + sessionId)
            });

            await driver.get('http://www.google.com');
            var element = await driver.findElement(webdriver.By.name('q'));
            await element.sendKeys('cross browser testing');
            await element.submit();
            await driver.getTitle().then(function(title) {
                console.log("The title is: " + title);
                if (title !== ('cross browser testing - Google Search')) {
                    throw Error('Unexpected title: ' + title);
                }
            });
            driver.quit();

           
        }
        catch(err){
            console.error('Exception!\n', err.stack, '\n');
            driver.quit();
        }
    }
    parallelExample();
});


</code></pre>
<h2>Taking Snapshots</h2>
You can take snapshots of errors during your test run for easy debugging and documentation. You can share these snapshots through Jira, Slack, or Email. To take a snapshot during your test run, you just have to invoke our API.
<pre><code>driver.takeSnapshot();</code></pre>
To see how to call the API, read below.
<h2>Full Example</h2>
<pre><code>


var username ='YOUR_USERNAME'; //replace with your email address 
var authkey = 'YOUR_AUTHKEY'; //replace with your authkey  

var webdriver = require('selenium-webdriver');
var SeleniumServer = require('selenium-webdriver/remote').SeleniumServer;
var request = require('request');
var remoteHub = 'http://hub.crossbrowsertesting.com:80/wd/hub';

var caps = {
    name : 'Login Form Example',
    build :  '1.0',
    version : '70', 
    platform : 'Windows 10', 
    screen_resolution : '1366x768',
    record_video : 'true',
    record_network : 'false',
    browserName : 'Chrome',
    username : username,
    password : authkey
};

var sessionId = null;


console.log('Connection to the CrossBrowserTesting remote server');
async function fullExample(){
    try{
    var driver = new webdriver.Builder()
                .usingServer(remoteHub)
                .withCapabilities(caps)
                .build();


    console.log('Waiting on the browser to be launched and the session to start');

    await driver.getSession().then(function(session){
        sessionId = session.id_; //need for API calls
        console.log('Session ID: ', sessionId); 
        console.log('See your test run at: https://app.crossbrowsertesting.com/selenium/' + sessionId)
    });

    //load your URL
    await driver.get('http://crossbrowsertesting.github.io/login-form.html');

    //take snapshot via cbt api
    await driver.takeSnapshot();
 
     //find checkout and click it 
    await driver.findElement(webdriver.By.id("username")).sendKeys("tester@crossbrowsertesting.com");

    //send keys to element to enter text
    await driver.findElement(webdriver.By.xpath("//*[@type=\"password\"]")).sendKeys("test123");

    //take snapshot via cbt api
    driver.takeSnapshot();

    //click the archive button
    await driver.findElement(webdriver.By.css("button[type=submit]")).click();

    //wait on logged in message
    await driver.wait(webdriver.until.elementLocated(webdriver.By.id("logged-in-message")), 10000);


    //take snapshot via cbt api
    await driver.takeSnapshot();

    //quit the driver
    await driver.quit()

    //set the score as passing
    setScore('pass').then(function(result){
        console.log('SUCCESS! set score to pass')
    });
    }
    catch(e){
        webdriverErrorHandler(e, driver)
    }
   
}

fullExample();


//Call API to set the score
function setScore(score){
    return new Promise((resolve, fulfill)=> {
    var result = { error: false, message: null }

    if (sessionId){
        
        request({
            method: 'PUT',
            uri: 'https://crossbrowsertesting.com/api/v3/selenium/' + sessionId,
            body: {'action': 'set_score', 'score': score },
            json: true
        },
        function(error, response, body) {
            if (error) {
                result.error = true;
                result.message = error;
            }
            else if (response.statusCode !== 200){
                result.error = true;
                result.message = body;
            }
            else{
                result.error = false;
                result.message = 'success';
            }
        })
        .auth(username, authkey);
    }
    else{
        result.error = true;
        result.message = 'Session Id was not defined';
        deferred.fulfill(result);
    }

    
        result.error ? fulfill('Fail') : resolve('Pass');
    });
}

//Call API to get a snapshot 
webdriver.WebDriver.prototype.takeSnapshot = function() {

    return new Promise((resolve, fulfill)=> { 
        var result = { error: false, message: null }
        
        if (sessionId){
            request.post(
                'https://crossbrowsertesting.com/api/v3/selenium/' + sessionId + '/snapshots', 
                function(error, response, body) {
                    if (error) {
                        result.error = true;
                        result.message = error;
                    }
                    else if (response.statusCode !== 200){
                        result.error = true;
                        result.message = body;
                    }
                    else{
                        result.error = false;
                        result.message = 'success';
                    }
                }
            )
            .auth(username,authkey);
            
        }
        else{
            result.error = true;
            result.message = 'Session Id was not defined';
           
        }

            result.error ? fulfill('Fail') : resolve('Pass'); //never call reject as we don't need this to actually stop the test
    });
}

//general error catching function
function webdriverErrorHandler(err, driver){

    console.error('There was an unhandled exception! ' + err.message);

    //if we had a session, end it and mark failed
    if (driver && sessionId){
        driver.quit();
        setScore('fail').then(function(result){
            console.log('FAILURE! set score to fail')
        })
    }
}


</code></pre>
<h2>Troubleshooting</h2>
<h3>Javascript / Selenium Bindings and Promises</h3>
The major difference between Selenium bindings for Javascript and any other language is that almost every method call in JS is asynchronous. The following code does not work:
<pre><code>var title = driver.getTitle();</code> <code>
console.log(title);</code></pre>
Because, of course, both statements are executed at the same time. WebDriver for javascript luckily allows us to use promises so that the steps of our test are executed in the proper sequence. The following code snippet does the same thing the previous version should, but it actually executes correctly:
<pre><code>
driver.getTitle().then(function(title) {
console.log(title);
});
</code></pre>
Luckily for us, most WebDriver methods in JavaScript will return a then method which takes a callback function for the first argument. In the example here, our first function gets a title, and the callback function receives and makes use of that title.
