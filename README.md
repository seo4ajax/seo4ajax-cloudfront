Example of SEO4Ajax integration using Lambda@Edge functions in AWS CloudFront
==

This is an example of integrating SEO4Ajax and a SPA in S3 served
over Cloudfront.

Instructions
--

1. Upload seo4ajax-cloudfront.yaml to Cloudformation as a new stack,
   that'll setup the example for you. Enter your SEO4Ajax site token
   when asked.
2. Upload code.js and index.html to the S3 bucket created by
   Cloudformation in step 1.
3. Change the read permissions for code.js and index.html to be publicly
   readable.

Testing
--

To see the page as rendered by seo4ajax.com run:

    curl -H 'User-Agent: Facebot' https://${CLOUDFRONT_DOMAIN}/over/here

The same page without pre-rendering:

    curl https://${CLOUDFRONT_DOMAIN}/over/here

Implementation
--

Two Lambda@Edge functions are used. The first detects bot requests on
requests entering the system, it sets a header which Cloudfront uses to
partition the cache. The second function, run after the cache, detects
the presence of the header and, if present, routes the request to
api.seo4ajax.com

Caching
--

By default, static resources from the bucket are cached for a long time
period. This improves performance but means that the deploy process of
any real app will need a Cloudfront purge step. The requests to 
api.seo4ajax.com are also cached.


Alternative
--

Amazon describes a more recent procedure for integrating a service like 
SEO4Ajax, you can find more info here: 
https://aws.amazon.com/blogs/networking-and-content-delivery/how-to-optimize-content-for-search-engines-with-aws-waf-bot-control-and-amazon-cloudfront/

Configuring CloudFront cache
--
Depending on your configuration, it may be useful to configure CloudFront 
to store captures served by SEO4Ajax and intended for bots separately 
from those intended for regular users.

In this case, you need to [create a "Cache policy"](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/cache-key-create-cache-policy.html) 
and a "Behavior" that uses a cache key based on the HTTP "from" header,
as illustrated in the screenshot below. This header is provided by the 
most popular bots (e.g. "googlebot(at)googlebot.com",
"bingbot(at)microsoft.com", "support@search.yandex.ru", 
"gptbot(at)openai.com", "oai-searchbot@openai.com") and is empty
otherwise.

![image](https://github.com/user-attachments/assets/0ad5fc1f-9cca-4309-9c68-eb37f67e30d3)


Notes
--
This project is a fork of https://github.com/jinty/prerender-cloudfront
