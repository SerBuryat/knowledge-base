> **Virtual hosting** is a method for hosting multiple [domain names](https://en.wikipedia.org/wiki/Domain_name "Domain name") (with separate handling of each name) on a single [server](https://en.wikipedia.org/wiki/Server_(computing) "Server (computing)") (or pool of servers). This allows one server to share its resources, such as memory and processor cycles, without requiring all services provided to use the same host name. - [wiki](https://en.wikipedia.org/wiki/Virtual_hosting)

Hosting provider gives you an *userspace* in server where you have access to your folder where is `/userspace-name/www` in.

> Unlike *[[VPS-VDS]]* which hosting provider gives you a whole *virtual server* instead of just *userspace* 

You can create `html.index` file as entry point for your site.
When client requests `www.your-domain-name.com` `html.index` returns as a response for him.