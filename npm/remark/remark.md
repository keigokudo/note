# remark

you can parse mark down into html.

```
import remark from "remark";
import html from "remark-html";

// use remark to convert markdown into HTML string
  const processdContent = await remark()
    .use(html)
    .process(matterResult.content);
  const contentHtml = processdContent.toString();

```
