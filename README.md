#ReflectShield

ReflectShield migrates a part of Chrome XSS Protection on the server side. ReflectShield checks whether a script that’s about to run on a web page is also present in the request that fetched that web page. If the script is present in the request, that’s a strong indication that the web server might have been tricked into reflecting the script. Therefore, it blocks the reflected XSS attacks by translating all characters of this request parameter which have HTML character entity equivalents into these entities.

##The Chrome XSS Protection:

XSS Auditor is a built-in function of Chrome and Safari designed to mitigate Cross-site Scripting (XSS) attacks. It aims to identify if query parameters contain malicious JavaScript and block the response if it believes the payloads were injected into the server response. XSS Auditor takes a black list approach to identify dangerous characters and tags supplied in request parameters. It also attempts to match query parameters with content to identify injection points.

##The ReflectShield protects from:

* Reflected Cross Site Scripting Attacks: Non-persistent XSS issues, which occur when the web application blindly echos parts of the HTTP request in the respective HTTP response’s HTML. In order to successfully exploit a reflected XSS vulnerability, the adversary has to trick the victim into sending a fabricated HTTP request. This can be done by, for instance, sending the victim a malicious link, or including a hidden Iframe into an attacker controlled page.

___

##Installation:

* Paste the following into your .htaccess file and it will enable ReflectShield across all pages in the directory where the .htaccess file resides:

	```
	php_value auto_prepend_file /var/www/html/reflectshield/examples/usinghtaccess/initReflectShield.php
	```

	The content of initReflectShield.php file should be:

	```php
	use ReflectShield\ReflectShield;
	require_once  '/var/www/html/reflectshield/vendor/autoload.php'; 
	$shield = new ReflectShield;
	```

	If .htaccess files are not enabled, check the following site https://help.ubuntu.com/community/EnablingUseOfApacheHtaccessFiles

* If none of the above options is available, you can use directly ReflectShield in your PHP scripts, by requesting a new object at the beggining of the script:

	```php
	use ReflectShield\ReflectShield;
	$shield = new ReflectShield;
	```
___

##Limitations of ReflectShield:

* Input Mutations: ReflectShield attempts to match query parameters with content to identify injection points. If the query parameter can’t be matched to content in the response, the ReflectShield will not be triggered. As a result, if the web application performs any kind of modification on the input parameters, will render the ReflectShield useless in preventing attacks.

*  Reflected JavaScript Injections: If a query parameter is matched to content in the response, the ReflectShield will translate all characters of this content which have HTML character entity equivalents into these entities. However, reflected JavaScript injections can occur even without characters that have entity equivalents.

* DOM Based XSS: XSS vulnerabilities which are caused by insecure client-side code which handles inappropriate the data from its associated DOM. DOM consists of objects representing the document properties from the point of view of the browser. These issues come to light when untrusted DOM data is used in a security-critical context, such as a call to eval. ReflectShield does not protect from DOM based XSS, since the vulnerability resides in the script code from the website and the injection payloads are not reflected directly in the response or always transmitted to the server.
 
* Persistent/Stored XSS: All XSS vulnerabilities, where the adversary is able to permanently inject the malicious script in the vulnerable application’s storage. This way every user that accesses the poisoned web page receives the injected script without further actions by the adversary. ReflectShield can only handle reflected injections.

* Multiple Step XSS: These vulnerabilities require the user to perform several actions on the applications to execute the attack vector/injected malicious JavaScript code. The main characteristics of multiple step XSS vulnerabilities are that the attack vector is injected in one page and then echoed in another page or application later. ReflectShield can only handle reflected injections on the same request.


