# Notes from High Performance Browser Networking
http://chimera.labs.oreilly.com/books/1230000000545/ch10.html#_hypertext_web_pages_and_web_applications

"Nothing beats *application-specific knowledge and measurements*, especially when linked to bottom-line goals and metrics of your business."

An HTTP 0.9 session consisted of a single document request, which was perfectly sufficient for delivery of hypertext: single document, one TCP connection, followed by connection close.

Page Load Time (PLT) - time to onload event in the browser, which is an event fired by the browser once the document and all of its dependent resources (JavaScript, images, etc.) have finished loading.

> The load event fires at the end of the document loading process. At this point, all of the objects in the document are in the DOM, and all the images, scripts, links and sub-frames have finished loading.
https://developer.mozilla.org/en-US/docs/Web/API/GlobalEventHandlers/onload

Not to be confused with `DOMContentLoaded`:
>The DOMContentLoaded event is fired when the initial HTML document has been completely loaded and parsed, without waiting for stylesheets, images, and subframes to finish loading. A very different event - load - should be used only to detect a fully-loaded page. It is an incredibly popular mistake to use load where DOMContentLoaded would be much more appropriate, so be cautious.
https://developer.mozilla.org/en-US/docs/Web/Events/DOMContentLoaded

Script execution can issue a synchronous doc.write and block DOM parsing and construction. Similarly, scripts can query for a computed style of any object, which means that JavaScript can also block on CSS. Consequently, the construction of DOM and CSSOM objects is frequently intertwined: DOM construction cannot proceed until JavaScript is executed, and JavaScript execution cannot proceed until CSSOM is available.
Rendering and script execution are blocked on stylesheets; get the CSS down to the user as quickly as you can.

## Delay	vs User perception
0–100 ms = Instant
100–300 ms = Small perceptible delay
300–1000 ms = Machine is working
1,000+ ms = Likely mental context switch
10,000+ ms = Task is abandoned

Experiences can be engineered to improve perceived performance. (Read Jakob Nielsen’s Usability Engineering and Steven Seow’s Designing and Engineering Time)

## Resource Waterfall
Use dev tools or http://www.webpagetest.org/.

The stages:
* DNS resolution
* TCP connection handshake
* TLS negotiation (if required)
* HTTP request
* content download

**Incremental Discovery**
As the browser is downloading markup it is checking for external resource links, and fetching them in parallel. Use this to load vital content first, leave perifical content to last

**Start Render**
The browser will also start rendering before all the resouces are available, and fill in the gaps.

* Different browsers implement different logic for when, and in which order, the individual resource requests are dispatched. As a result, the performance of the application will vary from browser to browser.

Turns out, bandwidth is not the limiting performance factor for most web applications. Instead, the bottleneck is the network roundtrip latency between the client and the server.

## Networking, rendering, computing
The execution of a web program primarily involves three tasks: fetching network resources, page layout + rendering, and JavaScript execution.

Bandwidth doesn’t matter, much... increasing bandwidth yeilds less and less returns with latency constant. We need to speed up the network (cut latency) or reduce the number of requests and round trips (handshakes).

TCP is optimized for long-lived connections and bulk data transfers. **Wireless latencies are significantly higher**, making networking optimization a critical priority for the mobile web

TCP slow start?

## Automated vs Human Testing

When configured well, automated testing provides a controlled and reproducible performance testing environment, which makes it a great fit for identifying and fixing performance regressions before they reach the user. Tip: identify your key performance metrics and set a "budget" for each one as part of your synthetic testing. If the budget is exceeded, raise an alarm!

**Navigation Timing API** is now supported across many of the modern desktop and mobile browsers.

The real benefit of Navigation Timing is that it exposes a lot of previously inaccessible data, such as DNS and TCP connect times, with high precision (microsecond timestamps), via a standardized `performance.timing` object in each browser. Hence, the data gathering process is very simple: load the page, grab the timing object from the user’s browser, and beacon it back to your analytics servers! By capturing this data, we can observe real-world performance of our applications as seen by real users, on real hardware, and across a wide variety of different networks. 

**Resource Timing** provides similar performance data for each resource on the page, allowing us to gather the full performance profile of the page

**User Timing** provides a simple JavaScript API to mark and measure application-specific performance metrics 

## Browser Optimisation

Browsers employ techniques to speed up page load

2 classes of approaches:
**Document aware network optimisation**
Spotting and prioritising critical network calls during css, JS, and document loading pipelines.
**User aware optimisation**
Learning + predicting future user actions.

4 techniques:
* Prefesting + priorotising
* resolving DNS early - in response to hovering on a link, and other user patterns
* premtive TCP connection - openning a TCP connection in anticipation of an HTTP request
* preloading a page - loading the whole page in a hidden tab so it can be instantly swapped in

How Chrome does it: https://www.igvita.com/posa/high-performance-networking-in-google-chrome/

