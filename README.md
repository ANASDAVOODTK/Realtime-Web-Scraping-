# Realtime-Web-Scraping-
Scrape Webpages using JS and Autocode


<!DOCTYPE html>
<html>
<h1 id="crawler-query-scraping-example">Crawler Query (Scraping) Example</h1>
<p>A quick example of the <code>crawler.query</code> API is using it to scrape the top 30 stories on Hacker News right now. To do that, use this link;</p>
<p><a href="https://autocode.standardlibrary.com/new/?workflow=crawler%2Fquery%2Fselectors">autocode.standardlibrary.com/new/?workflow=crawler%2Fquery%2Fselectors</a></p>
<p>This will open up an interface that looks something like this;</p>
<p><img src="https://standardlibrary-dotcom.s3.amazonaws.com/static/blog/introducing-the-crawler-query-api-scrape-webpages-via-css/maker-mode-autocode-example.png" alt="Maker Mode Example"></p>
<p>This is a part of <a href="https://autocode.com/">Autocode</a> called <strong>Maker Mode</strong> and can be used to pre-generate API logic. You can edit the code at any time by clicking on the code example to the right. You can see I&#39;ve already pre-filled the following settings;</p>
<ul>
<li><strong>url</strong> is <code>https://news.ycombinator.com/</code></li>
<li><strong>userAgent</strong> is <code>standardlibrary/crawler/query</code> (this is the default)</li>
<li><strong>includeMetadata</strong> is <code>False</code> (if <code>True</code>, will return additional metadata in a <code>meta</code> field in the response)</li>
<li><strong>selectorQueries</strong> is an array with one object, the values being <code>{&quot;selector&quot;:&quot;a.storylink&quot;,&quot;resolver&quot;:&quot;text&quot;}</code></li>
</ul>
<p>These settings generate the code;</p>
<pre><code class="language-javascript">// Store API Responses
const result = {crawler: {}};

console.log(`Running [Crawler → Query (scrape) a provided URL based on CSS selectors]...`);
result.crawler.pageData = await lib.crawler.query[&#39;@0.0.1&#39;].selectors({
  url: `https://news.ycombinator.com/`,
  userAgent: `standardlibrary/crawler/query`,
  includeMetadata: false,
  selectorQueries: [
    {
      &#39;selector&#39;: `a.storylink`,
      &#39;resolver&#39;: `text`
    }
  ]
});</code></pre>
<p>This can now be run by hitting <strong>Run Code</strong> in the bottom right. As of writing this, the Hacker News Front Page looks like this:</p>
<p><img src="https://standardlibrary-dotcom.s3.amazonaws.com/static/blog/introducing-the-crawler-query-api-scrape-webpages-via-css/hn-top-5.png" alt="HN Top 5"></p>
<p>So my returned response was (I&#39;ve truncated to only five results):</p>
<pre><code class="language-json">{
  &quot;url&quot;: &quot;https://news.ycombinator.com/&quot;,
  &quot;userAgent&quot;: &quot;standardlibrary/crawler/query&quot;,
  &quot;queryResults&quot;: [
    [
      {
        &quot;text&quot;: &quot;Zig cc: A drop-in replacement for GCC/Clang&quot;
      },
      {
        &quot;text&quot;: &quot;I got my file from Clearview AI&quot;
      },
      {
        &quot;text&quot;: &quot;A Novel Mechanical Ventilator Designed for Mass Scale Production&quot;
      },
      {
        &quot;text&quot;: &quot;Little Snitch and the deprecation of kernel extensions&quot;
      },
      {
        &quot;text&quot;: &quot;Doctors turn to social media to develop Covid-19 treatments in real time&quot;
      }
    ]
  ]
}</code></pre>
<p>And that&#39;s it! That&#39;s how easy the <code>crawler.query</code> API is to use.</p>
<h1 id="web-scraping-next-steps">Web Scraping, Next Steps</h1>
<p>You might be wondering how to customize this further. First, the <code>resolver</code> object attribute can take one of <strong>four</strong> values: <code>text</code>, <code>html</code>, <code>attr</code> and <code>map</code>.</p>
<ul>
<li><code>text</code> returns the element text</li>
<li><code>html</code> returns the element HTML</li>
<li><code>attr</code> returns an HTML attribute of the element, you must add an additional <code>attr</code> key with a value like <code>&quot;attr&quot;: &quot;href&quot;</code></li>
<li><code>map</code> returns a nested CSS selector query, this requires an additional <code>mapQueries</code> attribute expecting another array of <code>selectorQueries</code></li>
</ul>
<h1 id="using-resolver-attr">Using <strong><code>&quot;resolver&quot;: &quot;attr&quot;</code></strong></h1>
<p>For this query:</p>
<pre><code class="language-javascript">result.crawler.pageData = await lib.crawler.query[&#39;@0.0.1&#39;].selectors({
  url: `https://news.ycombinator.com/`,
  userAgent: `standardlibrary/crawler/query`,
  includeMetadata: false,
  selectorQueries: [
    {
      &#39;selector&#39;: `a.storylink`,
      &#39;resolver&#39;: `attr`,
      &#39;attr&#39;: `href`
    }
  ]
});</code></pre>
<p>We would expect a response that looks like this:</p>
<pre><code>{
  &quot;url&quot;: &quot;https://news.ycombinator.com/&quot;,
  &quot;userAgent&quot;: &quot;standardlibrary/crawler/query&quot;,
  &quot;queryResults&quot;: [
    [
      {
        &quot;attr&quot;: &quot;https://andrewkelley.me/post/zig-cc-powerful-drop-in-replacement-gcc-clang.html&quot;
      },
      {
        &quot;attr&quot;: &quot;https://onezero.medium.com/i-got-my-file-from-clearview-ai-and-it-freaked-me-out-33ca28b5d6d4&quot;
      },
      {
        &quot;attr&quot;: &quot;https://arxiv.org/abs/2003.10405&quot;
      },
      {
        &quot;attr&quot;: &quot;https://blog.obdev.at/little-snitch-and-the-deprecation-of-kernel-extensions/&quot;
      },
      {
        &quot;attr&quot;: &quot;https://www.bloomberg.com/news/articles/2020-03-24/covid-19-mysteries-yield-to-doctors-new-weapon-crowd-sourcing&quot;
      }
    ]
  ]
}</code></pre><h1 id="using-resolver-map">Using <strong><code>&quot;resolver&quot;: &quot;map&quot;</code></strong></h1>
<p>We can use <code>map</code> to make subqueries (called <code>mapQueries</code>) against a selector to parse data in parallel. For example, if we want to combine the above two queries (get both title and URL simultaneously)...</p>
<pre><code class="language-javascript">result.crawler.pageData = await lib.crawler.query[&#39;@0.0.1&#39;].selectors({
  url: `https://news.ycombinator.com/`,
  userAgent: `standardlibrary/crawler/query`,
  includeMetadata: false,
  selectorQueries: [
    {
      &#39;selector&#39;: `tr[id]:not([id=&quot;pagespace&quot;])`,
      &#39;resolver&#39;: `map`,
      &#39;mapQueries&#39;: [
        {
          &#39;selector&#39;: &#39;a.storylink&#39;,
          &#39;resolver&#39;: &#39;text&#39;
        },
        {
          &#39;selector&#39;: &#39;a.storylink&#39;,
          &#39;resolver&#39;: &#39;attr&#39;,
          &#39;attr&#39;: &#39;href&#39;
        }
      ]
    }
  ]
});</code></pre>
<p>And we&#39;ll get a result like this...</p>
<pre><code class="language-json">{
  &quot;url&quot;: &quot;https://news.ycombinator.com/&quot;,
  &quot;userAgent&quot;: &quot;standardlibrary/crawler/query&quot;,
  &quot;queryResults&quot;: [
    [
      {
        &quot;mapResults&quot;: [
          [
            {
              &quot;text&quot;: &quot;Zig cc: A drop-in replacement for GCC/Clang&quot;
            }
          ],
          [
            {
              &quot;attr&quot;: &quot;https://andrewkelley.me/post/zig-cc-powerful-drop-in-replacement-gcc-clang.html&quot;
            }
          ]
        ]
      },
      {
        &quot;mapResults&quot;: [
          [
            {
              &quot;text&quot;: &quot;I got my file from Clearview AI&quot;
            }
          ],
          [
            {
              &quot;attr&quot;: &quot;https://onezero.medium.com/i-got-my-file-from-clearview-ai-and-it-freaked-me-out-33ca28b5d6d4&quot;
            }
          ]
        ]
      },
      {
        &quot;mapResults&quot;: [
          [
            {
              &quot;text&quot;: &quot;A Novel Mechanical Ventilator Designed for Mass Scale Production&quot;
            }
          ],
          [
            {
              &quot;attr&quot;: &quot;https://arxiv.org/abs/2003.10405&quot;
            }
          ]
        ]
      },
      {
        &quot;mapResults&quot;: [
          [
            {
              &quot;text&quot;: &quot;Little Snitch and the deprecation of kernel extensions&quot;
            }
          ],
          [
            {
              &quot;attr&quot;: &quot;https://blog.obdev.at/little-snitch-and-the-deprecation-of-kernel-extensions/&quot;
            }
          ]
        ]
      },
      {
        &quot;mapResults&quot;: [
          [
            {
              &quot;text&quot;: &quot;Doctors turn to social media to develop Covid-19 treatments in real time&quot;
            }
          ],
          [
            {
              &quot;attr&quot;: &quot;https://www.bloomberg.com/news/articles/2020-03-24/covid-19-mysteries-yield-to-doctors-new-weapon-crowd-sourcing&quot;
            }
          ]
        ]
      }
    ]
  ]
}</code></pre>
<p><strong>Please Note:</strong> With the current version of the API, you can only use <code>map</code> at the top-level of <code>selectorQueries</code>.</p>
<h1 id="thats-it">That&#39;s it!</h1>
<p>We hope you enjoy playing around with scraping web page data. We&#39;ll have more tutorials incoming on how you can leverage this API more successfully. In the meantime, we encourage you to keep using <a href="https://autocode.com">Autocode</a> or jump into <a data-slack-button href="#" onclick="return false;">our Slack Community</a> to say hello. Looking forward to more!</p>
<div class="signature">
  <div class="signature-name">Keith Horwood</div>
  <div class="signature-title">Founder and CEO, Standard Library</div>
</div>
</article>
  </noscript>
  
  <script type="application/ld+json">
    {
  "@context": "https://schema.org/",
  "@type": "Article",
  "url": "https://autocode.com/community/announcements/introducing-the-crawler-query-api-scrape-webpages-via-css",
  "headline": "Introducing the Crawler Query API: Scrape Webpages via CSS",
  "name": "Introducing the Crawler Query API: Scrape Webpages via CSS",
  "articleBody": "Hey everyone, Another week, another update. We&#39;ve published a few minor changes to Standard Library and Autocode in the past couple of weeks, namely; Better Autocomplete support in Maker Mode Customize / Configure environment variables from GitHub-based template projects, where applicable This week we&#39;re launching a new Standard Library API on the crawler namespace, namely the crawler.query API. It&#39;s available at stdlib.com/@crawler/lib/query/. Right now, this API lets you seamlessly query data from webpages based on CSS selectors via the .selectors endpoint. This is useful for running scheduled tasks to scrape new website data. Crawler Query (Scraping) Example A quick example of the crawler.query API is using it to scrape the top 30 stories on Hacker News right now. To do that, use this link; autocode.stdlib.com/new/?workflow=crawler%2Fquery%2Fselectors This will open up an interface that looks something like this; This is a part of Autocode called Maker Mode and can be used to pre-generate API logic. You can edit the code at any time by clicking on the code example to the right. You can see I&#39;ve already pre-filled the following settings; url is https://news.ycombinator.com/ userAgent is stdlib/crawler/query (this is the default) includeMetadata is False (if True, will return additional metadata in a meta field in the response) selectorQueries is an array with one object, the values being {&quot;selector&quot;:&quot;a.storylink&quot;,&quot;resolver&quot;:&quot;text&quot;} These settings generate the code; // Store API Responses const result = {crawler: {}}; console.log(`Running [Crawler → Query (scrape) a provided URL based on CSS selectors]...`); result.crawler.pageData = await lib.crawler.query[&#39;@0.0.1&#39;].selectors({   url: `https://news.ycombinator.com/`,   userAgent: `stdlib/crawler/query`,   includeMetadata: false,   selectorQueries: [     {       &#39;selector&#39;: `a.storylink`,       &#39;resolver&#39;: `text`     }   ] }); This can now be run by hitting Run Code in the bottom right. As of writing this, the Hacker News Front Page looks like this: So my returned response was (I&#39;ve truncated to only five results): {   &quot;url&quot;: &quot;https://news.ycombinator.com/&quot;,   &quot;userAgent&quot;: &quot;stdlib/crawler/query&quot;,   &quot;queryResults&quot;: [     [       {         &quot;text&quot;: &quot;Zig cc: A drop-in replacement for GCC/Clang&quot;       },       {         &quot;text&quot;: &quot;I got my file from Clearview AI&quot;       },       {         &quot;text&quot;: &quot;A Novel Mechanical Ventilator Designed for Mass Scale Production&quot;       },       {         &quot;text&quot;: &quot;Little Snitch and the deprecation of kernel extensions&quot;       },       {         &quot;text&quot;: &quot;Doctors turn to social media to develop Covid-19 treatments in real time&quot;       }     ]   ] } And that&#39;s it! That&#39;s how easy the crawler.query API is to use. Web Scraping, Next Steps You might be wondering how to customize this further. First, the resolver object attribute can take one of four values: text, html, attr and map. text returns the element text html returns the element HTML attr returns an HTML attribute of the element, you must add an additional attr key with a value like &quot;attr&quot;: &quot;href&quot; map returns a nested CSS selector query, this requires an additional mapQueries attribute expecting another array of selectorQueries Using &quot;resolver&quot;: &quot;attr&quot; For this query: result.crawler.pageData = await lib.crawler.query[&#39;@0.0.1&#39;].selectors({   url: `https://news.ycombinator.com/`,   userAgent: `stdlib/crawler/query`,   includeMetadata: false,   selectorQueries: [     {       &#39;selector&#39;: `a.storylink`,       &#39;resolver&#39;: `attr`,       &#39;attr&#39;: `href`     }   ] }); We would expect a response that looks like this: {   &quot;url&quot;: &quot;https://news.ycombinator.com/&quot;,   &quot;userAgent&quot;: &quot;stdlib/crawler/query&quot;,   &quot;queryResults&quot;: [     [       {         &quot;attr&quot;: &quot;https://andrewkelley.me/post/zig-cc-powerful-drop-in-replacement-gcc-clang.html&quot;       },       {         &quot;attr&quot;: &quot;https://onezero.medium.com/i-got-my-file-from-clearview-ai-and-it-freaked-me-out-33ca28b5d6d4&quot;       },       {         &quot;attr&quot;: &quot;https://arxiv.org/abs/2003.10405&quot;       },       {         &quot;attr&quot;: &quot;https://blog.obdev.at/little-snitch-and-the-deprecation-of-kernel-extensions/&quot;       },       {         &quot;attr&quot;: &quot;https://www.bloomberg.com/news/articles/2020-03-24/covid-19-mysteries-yield-to-doctors-new-weapon-crowd-sourcing&quot;       }     ]   ] }Using &quot;resolver&quot;: &quot;map&quot; We can use map to make subqueries (called mapQueries) against a selector to parse data in parallel. For example, if we want to combine the above two queries (get both title and URL simultaneously)... result.crawler.pageData = await lib.crawler.query[&#39;@0.0.1&#39;].selectors({   url: `https://news.ycombinator.com/`,   userAgent: `stdlib/crawler/query`,   includeMetadata: false,   selectorQueries: [     {       &#39;selector&#39;: `tr[id]:not([id=&quot;pagespace&quot;])`,       &#39;resolver&#39;: `map`,       &#39;mapQueries&#39;: [         {           &#39;selector&#39;: &#39;a.storylink&#39;,           &#39;resolver&#39;: &#39;text&#39;         },         {           &#39;selector&#39;: &#39;a.storylink&#39;,           &#39;resolver&#39;: &#39;attr&#39;,           &#39;attr&#39;: &#39;href&#39;         }       ]     }   ] }); And we&#39;ll get a result like this... {   &quot;url&quot;: &quot;https://news.ycombinator.com/&quot;,   &quot;userAgent&quot;: &quot;stdlib/crawler/query&quot;,   &quot;queryResults&quot;: [     [       {         &quot;mapResults&quot;: [           [             {               &quot;text&quot;: &quot;Zig cc: A drop-in replacement for GCC/Clang&quot;             }           ],           [             {               &quot;attr&quot;: &quot;https://andrewkelley.me/post/zig-cc-powerful-drop-in-replacement-gcc-clang.html&quot;             }           ]         ]       },       {         &quot;mapResults&quot;: [           [             {               &quot;text&quot;: &quot;I got my file from Clearview AI&quot;             }           ],           [             {               &quot;attr&quot;: &quot;https://onezero.medium.com/i-got-my-file-from-clearview-ai-and-it-freaked-me-out-33ca28b5d6d4&quot;             }           ]         ]       },       {         &quot;mapResults&quot;: [           [             {               &quot;text&quot;: &quot;A Novel Mechanical Ventilator Designed for Mass Scale Production&quot;             }           ],           [             {               &quot;attr&quot;: &quot;https://arxiv.org/abs/2003.10405&quot;             }           ]         ]       },       {         &quot;mapResults&quot;: [           [             {               &quot;text&quot;: &quot;Little Snitch and the deprecation of kernel extensions&quot;             }           ],           [             {               &quot;attr&quot;: &quot;https://blog.obdev.at/little-snitch-and-the-deprecation-of-kernel-extensions/&quot;             }           ]         ]       },       {         &quot;mapResults&quot;: [           [             {               &quot;text&quot;: &quot;Doctors turn to social media to develop Covid-19 treatments in real time&quot;             }           ],           [             {               &quot;attr&quot;: &quot;https://www.bloomberg.com/news/articles/2020-03-24/covid-19-mysteries-yield-to-doctors-new-weapon-crowd-sourcing&quot;             }           ]         ]       }     ]   ] } Please Note: With the current version of the API, you can only use map at the top-level of selectorQueries. That&#39;s it! We hope you enjoy playing around with scraping web page data. We&#39;ll have more tutorials incoming on how you can leverage this API more successfully. In the meantime, we encourage you to keep using Autocode or jump into our Slack Community to say hello. Looking forward to more!   Keith Horwood   Founder and CEO, Standard Library",
  "wordCount": 1801,
  "author": {
    "@type": "Person",
    "name": "Keith Horwood"
  },
  "contributor": {
    "@type": "Person",
    "name": "Keith Horwood"
  },
  "publisher:": {
    "@type": "Person",
    "name": "Keith Horwood"
  },
  "datePublished": "2020-03-25T03:21:13.250Z",
  "thumbnailUrl": "https://polybit-apps.s3.amazonaws.com/stdlib/users/keith/profile/image.png?1594331471434",
  "image": "https://autocode.com/static/social/autocode-512.png",
  "timeRequired": "PT10M"
}
  </script>
</body>

</html>
