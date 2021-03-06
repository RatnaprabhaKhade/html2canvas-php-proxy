## html2canvas-php-proxy 1.0.0

## PHP Proxy html2canvas

This script allows you to use **html2canvas.js** with different servers, ports and protocols (http, https),
preventing to occur "tainted" when exporting the `<canvas>` for image.

## Others scripting language

You do not use PHP, but need html2canvas working with proxy, see other proxies:

* [html2canvas proxy in asp.net (csharp)](https://github.com/brcontainer/html2canvas-csharp-proxy)
* [html2canvas proxy in asp classic (vbscript)](https://github.com/brcontainer/html2canvas-asp-vbscript-proxy)
* [html2canvas proxy in python (work any framework)](https://github.com/brcontainer/html2canvas-proxy-python)

## Problems and solutions

When adding an image that belongs to another domain in `<canvas>` and after that try to export the canvas
for a new image, a security error occurs (actually occurs is a security lock), which can return the error:

> SecurityError: DOM Exception 18
>
> Error: An attempt was made to break through the security policy of the user agent.

If using Google Maps (or google maps static) you can get this error in console:

> Google Maps API error: MissingKeyMapError

You need get a API Key in: https://developers.google.com/maps/documentation/javascript/get-api-key

If get this error:

> Access to Image at 'file:///...' from origin 'null' has been blocked by CORS policy: Invalid response. Origin 'null' is therefore not allowed access.

Means that you are not using an HTTP server, html2canvas does not work over the "file:///" protocol, use Apache, Nginx or IIS with PHP for work.

## Follow

I ask you to follow me or "star" my repository to track updates

## Run script in Cross-domain (data URI scheme)

(See details: https://github.com/brcontainer/html2canvas-php-proxy/issues/9)

> Note: Enable cross-domain in proxy server can consume more memory, but can be faster in execution it performs only one request at the proxy server.

> Note: If the file html2canvasproxy.php is in the same domain that your project, you do not need to enable this option.

> Note: Disable the "cross-domain" does not mean you will not be able to capture images from different servers, in other words, the "cross-domain" here refers to "html2canvas.js" (not necessarily the javascript file, but the place where runs) and the "html2canvas.php" are in different domains, the "cross-domain" here refers domain.

In some cases you may want to use this [html2canvasproxy.php](https://github.com/brcontainer/html2canvas-php-proxy/blob/master/html2canvasproxy.php) on a specific server, but the "html2canvas.js" and another server, this would cause problems in your project with the security causing failures in execution. In order to use security just set in the [html2canvasproxy.php](https://github.com/brcontainer/html2canvas-php-proxy/blob/master/html2canvasproxy.php):

Enable data uri scheme for use proxy for all servers:

`define('H2CP_DATAURI', true);`

Disable data uri scheme:

`define('H2CP_DATAURI', false);`

## Setup

Definition | Description
--- | ---
`define('H2CP_PATH', 'cache');`                   | Folder where the images are saved
`define('H2CP_PERMISSION', 0666);`                | Set forlder permission (use 644 or 666 for remove execution for prevent sploits)
`define('H2CP_CACHE', 60 * 5 * 1000);`            | Limit access-control and cache in seconds, define 0/false/null/-1 to not use "http header cache"
`define('H2CP_TIMEOUT', 20);`                     | Timeout from load Socket
`define('H2CP_MAX_LOOP', 10);`                    | Configure loop limit for redirects (location header)
`define('H2CP_DATAURI', false);`                  | Enable use of "data URI scheme"
`define('H2CP_PREFER_CURL', true);`               | Prefer curl if avaliable or disable
`define('H2CP_SSL_VERIFY_PEER', false);`          | Set false for disable SSL checking or true for enable (require config PHP.INI with `curl.cainfo=/path/to/cacert.pem`). You can set path manualy like this: `define('H2CP_SSL_VERIFY_PEER', '/path/to/cacert.pem');`
`define('H2CP_ALLOWED_DOMAINS', array( '*' ));`   | `*` allow all domains, for subdomains use like this `*.site.com`, for fixed domains use `array( 'site.com', 'www.site.com' )`
`define('H2CP_ALLOWED_PORTS', array( 80, 443 ));` | Config allowed ports

## Usage

> Note: Requires PHP5+

* [Google maps](https://github.com/brcontainer/html2canvas-php-proxy/blob/master/examples/google-maps.html)
* [Test case](https://github.com/brcontainer/html2canvas-php-proxy/blob/master/examples/usable-example.html)

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title>html2canvas php proxy</title>
        <script src="html2canvas.js"></script>
        <script>
        //<![CDATA[
        (function() {
            window.onload = function(){
                html2canvas(document.body, {
                    "logging": true, //Enable log (use Web Console for get Errors and Warnings)
                    "proxy": "html2canvasproxy.php",
                    "onrendered": function (canvas) {
                        var img = new Image();
                        img.onload = function() {
                            img.onload = null;
                            document.body.appendChild(img);
                        };
                        img.onerror = function() {
                            img.onerror = null;
                            if(window.console.log) {
                                window.console.log("Not loaded image from canvas.toDataURL");
                            } else {
                                alert("Not loaded image from canvas.toDataURL");
                            }
                        };
                        img.src = canvas.toDataURL("image/png");
                    }
                });
            };
        })();
        //]]>
        </script>
    </head>
    <body>
        <p>
            <img alt="google maps static" src="http://maps.googleapis.com/maps/api/staticmap?center=40.714728,-73.998672&amp;zoom=12&amp;size=800x600&amp;maptype=roadmap&amp;sensor=false">
        </p>
    </body>
</html>
```

## Using Web Console

If you have any problems with the script recommend to analyze the log use the Web Console from your browser:
* Firefox: https://developer.mozilla.org/en-US/docs/Tools/Browser_Console
* Chrome: https://developers.google.com/chrome-developer-tools/docs/console
* InternetExplorer: http://msdn.microsoft.com/en-us/library/gg589530%28v=vs.85%29.aspx

Get NetWork results:
* Firefox: https://hacks.mozilla.org/2013/05/firefox-developer-tool-features-for-firefox-23/
* Chrome: https://developers.google.com/chrome-developer-tools/docs/network
* InternetExplorer: http://msdn.microsoft.com/en-us/library/gg130952%28v=vs.85%29.aspx

An alternative is to diagnose problems accessing the link directly:

`http://[DOMAIN]/[PATH]/html2canvasproxy.php?url=http%3A%2F%2Fmaps.googleapis.com%2Fmaps%2Fapi%2Fstaticmap%3Fcenter%3D40.714728%2C-73.998672%26zoom%3D12%26size%3D800x600%26maptype%3Droadmap%26sensor%3Dfalse%261&callback=html2canvas_0`

Replace `[DOMAIN]` by your domain (eg. 127.0.0.1) and replace `[PATH]` by your project folder (eg. project-1/test), something like:

`http://localhost/project-1/test/html2canvasproxy.php?url=http%3A%2F%2Fmaps.googleapis.com%2Fmaps%2Fapi%2Fstaticmap%3Fcenter%3D40.714728%2C-73.998672%26zoom%3D12%26size%3D800x600%26maptype%3Droadmap%26sensor%3Dfalse%261&callback=html2canvas_0`

## Changelog

Changelog moved to https://github.com/brcontainer/html2canvas-php-proxy/blob/master/CHANGELOG.md

## Next versions

Details of future versions are being studied, in other words, can happen as can be forsaken ideas.
The ideas here are not ready or are not public in the main script, are only suggestions. You can offer suggestions on [issues](https://github.com/brcontainer/html2canvas-php-proxy/issues/new).

* Etag cache browser for use HTTP 304 (resources are reusable, avoiding unnecessary downloads)
* Cache from SOCKET, if not specified header cache in SOCKET, then uses settings by `DEFINE();`
* Rewrinte script using [PSR-4](http://www.php-fig.org/psr/psr-4/)
* Support to `composer`
