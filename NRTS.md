# QXONI Network Request Timeout Safeguard (NRTS)

This document explains how QXONI handles network delays, CDN routing issues, and temporary connectivity disruptions that may prevent website resources from loading correctly.

---

## What is NRTS?

**NRTS** stands for **Network Request Timeout Safeguard**.

QXONI delivers static resources such as images, fonts, and other assets through a globally distributed Content Delivery Network (CDN). In rare situations, internet routing problems, provider outages, fiber disruptions, or regional connectivity issues may significantly delay requests.

To prevent pages from remaining in an indefinite loading state, QXONI uses NRTS to detect requests that exceed the expected response time.

---

## How It Works

1. The browser requests a resource from a QXONI content endpoint.
2. If the request cannot be completed within the configured timeout period, NRTS terminates the waiting process.
3. The timeout event is intercepted by the application.
4. The user is redirected to a controlled error page explaining that the request could not be completed.

This mechanism ensures that users receive clear feedback instead of experiencing endless loading indicators.

---

## Common Error Message

### "Unable to load the requested resource"

If this message appears after an extended loading period, it does not necessarily mean that the resource is missing.

Possible causes include:

* Temporary CDN routing issues
* Regional network disruptions
* ISP connectivity problems
* Upstream provider outages
* Excessive network latency

In many cases, the resource becomes available again shortly afterward.

---

## Troubleshooting

### 1. Perform a Hard Reload

**Windows / Linux**

`Ctrl + F5`

**macOS**

`Cmd + Shift + R`

This forces the browser to request a fresh copy of all resources and bypasses locally cached files.

---

### 2. Retry After a Few Minutes

Large-scale network incidents are often resolved automatically as internet traffic is rerouted through alternative paths.

---

### 3. Check Your Internet Connection

Verify that:

* Your connection is stable
* VPN services are not interfering with traffic
* Firewall or security software is not blocking requests

---

## Technical Overview

The following example demonstrates the timeout protection mechanism used by NRTS.

```js
const REQUEST_TIMEOUT = 10000;

const fetchWithNRTS = async (url) => {
    try {
        const response = await Promise.race([
            fetch(url),
            new Promise((_, reject) =>
                setTimeout(
                    () => reject(new Error('REQUEST_TIMEOUT')),
                    REQUEST_TIMEOUT
                )
            )
        ]);

        return response;
    } catch (error) {
        if (error.message === 'REQUEST_TIMEOUT') {
            showNetworkErrorPage();
        }

        throw error;
    }
};
```

---

## Notes

NRTS is designed to improve user experience during temporary connectivity issues. It does not indicate that a resource has been deleted or permanently removed. In most cases, reloading the page or retrying the request resolves the problem once network conditions return to normal.
