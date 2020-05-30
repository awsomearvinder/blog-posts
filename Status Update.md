# Status Update
## 04/14/2020
So, first day of working on the site for this blog, I got a navbar down. No pictures as of yet but it dosen't look *too* bad. This is the first time I'm actually using a backend and I gotta say having templates are really nice. Especially since I'm fairly sure that rocket uses macros to compile the templates into pages at compile time. At the very least it can throw errors in my face because the templates are incorrect.

All in all, aside from not being async, working with rocket is a dream. When rocket finally moves to async I doubt many changes are going to have to be done in my code since so much of what would be asynchronous code is handled by rocket behind the scenes already.

The one iffy thing is all the mounting of the route, it's a tad boiler-platey for me but like, since I'm using templates I'm planning on just mounting it to "/Blog/name" where name can change, and using a single template for all the pages. It'll all be served by one function that just returns a page with a md file with  the corresponding name. 

<small> *linux bias best bias* </small>
