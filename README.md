# PDF Ref Preview

A bookmarklet is a link that contains Javascript. To install a bookmarklet, the most common way is to drag and drop a bookmarklet link into your browser's bookmark bar.

This bookmarklet only works if the viewer uses [pdf.js](https://github.com/mozilla/pdf.js/), which is the default in Firefox.

<pre>
<a href="javascript:(async()=>{const e=window.PDFViewerApplication;if('_previewHandler'in e)return document.removeEventListener('mouseover',e._previewHandler),void delete e._previewHandler;const t=await e.pdfDocument.getDestinations();async function n(n){if('internalLink'!=n.target.className)return;const i=n.target.hash,o=n.target.parentElement,r=document.createElement('canvas');r.style.border='1px solid black',r.style.direction='ltr';const a=i.substring(1),s=a in t?t[a]:JSON.parse(decodeURIComponent(a)),d=e.pdfLinkService._cachedPageNumber(s[0]);e.pdfDocument.getPage(d).then(function(e){const t=e.getViewport({scale:1});r.style.height=`${t.height}px`,r.style.width=`${t.width}px`;const n=e.getViewport({scale:4,offsetX:4*-s[2],offsetY:4*(s[3]-t.height)});r.height=n.height,r.width=n.width;const i={canvasContext:r.getContext('2d'),viewport:n};e.render(i)}),o.appendChild(r),o.addEventListener('mouseleave',function(e){r.remove()})}document.addEventListener('mouseover',n),e._previewHandler=n})();">PDFRefPreview</a>
<pre>