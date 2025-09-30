### HTTP
* stateless protocol - neither party is required to remember any previous interactions
* cookies - key/value pairs that are stored in the browser at the request of the server. COOKIES SOLVE THE 'STATELESS' ISSUE
* since cookes are stored in the browser, the client has full access. This means that we need to be careful about what information we store in cookies
* cookies are present in EVERY http request
* cookies are domain specific, so only the domain that issues it can access it
* cookies are also port specfic


POSTS should alwasy end as a redirection. DO NOT render anything in a POST. Rendering is for GET paths/requests

