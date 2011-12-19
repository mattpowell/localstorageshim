Readme
=========

localStorage shim for IE 6/7. Tries to provide a mapping between the HTML5 localStorage api and IE's proprietary UserData format.

What is UserData?
-----------------
UserData is a behavior that provides an xml based persistence layer in Internet Explorer starting at version 5.5. See: http://msdn.microsoft.com/en-us/library/ms531424(v=vs.85).aspx

What is localStorage?
---------------------
DOM Storage is the name given to the set of storage-related features first introduced in the Web Applications 1.0 specification, and now split off into its own W3C Web Storage specification. DOM Storage is designed to provide a larger, securer, and easier-to-use alternative to storing information in cookies. Data placed in localStorage is per domain (it's available to all scripts from the domain that originally stored the data) and persists after the browser is closed.
 
MDN: https://developer.mozilla.org/en/DOM/Storage

Spec: http://dev.w3.org/html5/webstorage/

UserData Limitations
--------------------
64kb - 128kb storage limit per ***path*** (max 640kb - 1024kb per domain).

Synchronous access to data (meaning data isn't available immediately on page load).

Note regarding the path limitation
------------------------
Data saved via UserData is only available on the url path it was originally saved from. So, data stored at example.com/path1/ is unavailable from example.com/path2/. That sucks and would make UserData useless as shim for localStorage. BUT, there's a way around this. If we setup the script to dynamically insert an invisible iframe with a static path then we have a consistent point from which to store and access data. Whoo! But what do we use as that static path? We have to keep in mind that the iframe will load the static url so it'd be bad to load an entirely new html page (keep in mind IE6/7 is limited to 2 parallel connections per host). Also, we should try to load something that's fast and already cached. Bam, favicon.ico fits the bill. And IE6/7 is one of those browsers that automatically requests /favicon.ico even if you didn't specify it in a `rel='icon'` link. But, what happens if it's not cached? Or even worse, if it's 404 page?! This is where it gets next level. Are you ready? We're going to reference the script that originally included the shim (thus the id in the example below). Dang, but, when you point an iframe to a .js file IE tries to download it. Say hello to my little friend: MHTML. I'm tired of writing, so, I'm going to cut to the chase. We're referencing base64 encoded html that's already been cached inside localstorageshim.js. It's actually really ~~clever~~ tricky... if you want to know more, let me know :)

Size
----
  - 4.18 kbytes raw
  - 2.08 kbytes minified (Closure Compiler `ADVANCED_OPTIMIZATIONS`)
  - 1.23 kbytes minified+gzip

TODO
----
* Try to make access to data more asynchronous and minimize race conditions.
* Reduce the amount of try/catches
* Reduce usage of `syncStoredKeys`. Or at least try to make it more performant.
* Provide persistence for this type of usage: `localStorage.myProp = 'val';` (which is the same as: `localStorage.setItem('myProp','val');`)

Example
-------
    <!--[if lte IE 7]>
        <script src="localstorageshim.min.js" type="text/javascript" id="ie-localstorage-shim"></script>
    <![endif]-->
    <script type="text/javascript">
      window.onload = function() {
        alert(localStorage.getItem('test'));//null on first page load. 'hello' on second
        localStorage.setItem('test', 'hello');
      }
    </script>

p.s.
----
I'm open to critiques/ideas so hit me up if you have them: twitter.com/mattpowell (or in the issues tab ;)