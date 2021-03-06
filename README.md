# Monocle

[![MIT licensed](https://img.shields.io/badge/license-MIT-blue.svg)](https://raw.githubusercontent.com/Badgerati/Monocle/master/LICENSE.txt)
[![PowerShell](https://img.shields.io/powershellgallery/dt/monocle.svg?label=PowerShell&colorB=085298)](https://www.powershellgallery.com/packages/Monocle)
[![Docker](https://img.shields.io/docker/pulls/badgerati/monocle.svg?label=Docker)](https://hub.docker.com/r/badgerati/monocle/)

Monocle is a Cross-Platform PowerShell Web Automation module, made to make automating and testing websites easier. It's a PowerShell wrapper around Selenium, with the aim of abstracting Selenium away from the user.

* [Install](#install)
* [Example](#example)
* [Documentation](#documentation)
  * [Functions](#functions)
  * [Screenshots](#screenshots)
  * [2FA Codes](#2fa-codes)
  * [Docker](#docker)

Monocle currently supports the following browsers:

* IE
* Google Chrome
* Firefox

## Install

```powershell
Install-Module -Name Monocle
```

## Example

```powershell
Import-Module Monocle

# create a browser
$browser = New-MonocleBrowser -Type Chrome

# Monocle runs commands in web flows, for easy disposal and test tracking
Start-MonocleFlow -Name 'Load YouTube' -Browser $browser -ScriptBlock {

    # tell the browser which URL to navigate to, will wait for the page to load
    Set-MonocleUrl -Url 'https://www.youtube.com'

    # sets the element's value, selecting the element by ID/Name
    Get-MonocleElement -Id 'search_query' | Set-MonocleElementValue -Value 'Beerus Madness (Extended)'

    # click the search button
    Get-MonocleElement -Id 'search-icon-legacy' | Invoke-MonocleElementClick

    # wait for the URL to change to start with the following value
    Wait-MonocleUrl -Url 'https://www.youtube.com/results?search_query=' -StartsWith

    # downloads an image from the page, selcted by using an XPath to an element
    Get-MonocleElement -XPath "//div[@data-context-item-id='SI6Yyr-iI6M']/img[1]" | Save-MonocleImage -FilePath '.\beerus.jpg'

    # tells the browser to click the video in the results
    Get-MonocleElement -XPath "//a[@title='Dragon Ball Super Soundtrack - Beerus Madness (Extended)']" | Invoke-MonocleElementClick

    # wait for the URL to be loaded
    Wait-MonocleUrl -Url 'https://www.youtube.com/watch?v=SI6Yyr-iI6M'

}

# dispose the browser
Close-MonocleBrowser -Browser $browser
```

## Documentation

### Functions

The following is a list of available functions in Monocle:

* Assert-MonocleBodyValue
* Assert-MonocleElementValue
* Clear-MonocleElementValue
* Close-MonocleBrowser
* Edit-MonocleUrl
* Get-Monocle2FACode
* Get-MonocleElement
* Get-MonocleElementAttribute
* Get-MonocleElementValue
* Get-MonocleHtml
* Get-MonocleTimeout
* Get-MonocleUrl
* Invoke-MonocleElementCheck
* Invoke-MonocleElementClick
* Invoke-MonocleJavaScript
* Invoke-MonocleRetryScript
* Invoke-MonocleScreenshot
* New-MonocleBrowser
* Restart-MonocleBrowser
* Save-MonocleImage
* Set-MonocleElementAttribute
* Set-MonocleElementValue
* Set-MonocleTimeout
* Set-MonocleUrl
* Start-MonocleFlow
* Start-MonocleSleep
* Submit-MonocleForm
* Test-MonocleElement
* Test-MonocleElementAttribute
* Wait-MonocleUrl
* Wait-MonocleUrlDifferent
* Wait-MonocleValue

### Screenshots

There are two main ways to take a screenshot of the browser. The first it to tell Monocle to automatically take a screenshot whenever a flow fails. You can do this by using the `-ScreenshotPath` and `-ScreenshotOnFail` parameters on the `Start-MonocleFlow` function:

```powershell
Start-MonocleFlow -Name '<name>' -Browser $browser -ScriptBlock {
    # failing logic
} -ScreenshotPath './path' -ScreenshotOnFail
```

Or, you can take a screenshot directly:

```powershell
Invoke-MonocoleScreenshot -Name 'screenshot.png' -Path './path'
```

> Not supplying `-ScreenshotPath` or `-Path` will default to the current path.

### 2FA Codes

Monocle has inbuilt support for generating 2FA codes. To do this you need the Secret Code that is normally presented with the QR code, and you pass this to the `Get-Monocle2FACode` function with a date - which is defaulted to now:

```powershell
$code = Get-Monocle2FACode -Secret 'FAKENDMYJWLLB'
Get-MonocleElement -Id '2fa-code' | Set-MonocleElementValue -Value $code -Mask
```

### Docker

Monocle has an official Docker image, which comes preloaded with:

* Monocle (obviously!)
* Firefox
* Google Chrome

You can use this image to run your Monocle flows - and they will also automatically run headless.

An example `Dockerfile` could be:

```dockerfile
FROM badgerati/monocle:latest
COPY . /usr/src/scripts
CMD [ "pwsh", "-c", "cd /usr/src/scripts; ./flow.ps1" ]
```
