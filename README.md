# PDF Ref Preview

Preview internal links in PDFs on mouse hover.

<p align="center">
  <img title="Demo" src="https://raw.githubusercontent.com/belinghy/PDFRefPreview/assets/assets/demo.gif">
</p>

The script works by following internal links to the target page and location, and render the view in a separate canvas. Some PDFs may not work because the internal links are broken.

This script only works if the viewer is [PDF.js](https://github.com/mozilla/pdf.js/).

## Firefox

PDF.js is the default viewer in Firefox. To install the script, create a bookmark and add the code below to the `URL` field. Use the bookmark to toggle enable/disable preview after a PDF has been loaded.

```js
javascript:(
    async () => { 
        const e = window.PDFViewerApplication, 
        t = document.getElementById("viewer");
        if ("_previewHandler" in e) 
            return t.removeEventListener("mouseover", e._previewHandler), 
        delete e._previewHandler, void delete e._previewing;
        const n = t.getBoundingClientRect(), 
        i = (n.left + n.right) / 2,
        r = await e.pdfDocument.getDestinations();
        async function o(n) {
            if ("internalLink" != n.target.className || e._previewing) return;
            const o = n.target.hash,
            a = n.target.parentElement,
            c = document.createElement("canvas"),
            s = c.style;
            s.border = "1px solid black",
            s.direction = "ltr", 
            s.position = "fixed", 
            s.zIndex = "2", 
            s.top = `${n.clientY + 10}px`, /* here offset for entire canvas from coursor */
            s.boxShadow = "1px 1px 1px black, -1px 1px 1px black";
            const p = decodeURIComponent(o.substring(1));
            if (p.startsWith("figure"))
                internalOffset = .75;
            else
                internalOffset = 1;
            d = p in r ? r[p] : JSON.parse(p);
            l = e.pdfLinkService._cachedPageNumber(d[0]);
            e.pdfDocument.getPage(l).then(
                function (t) { 
                    const r = t.getViewport({ scale: 1 }), 
                    o = 1.2 * r.height * e.pdfViewer.currentScale, 
                    a = 1.2 * r.width * e.pdfViewer.currentScale, 
                    p = n.clientX > i ? 2 * a / 3 : a / 3;
                    let l;
                    switch (
                        s.height = `${o}px`, 
                        s.width = `${a}px`, 
                        s.left = `${n.clientX - p - 4}px`, 
                        d[1].name
                        ) { 
                            case "XYZ": l = d[3];
                            break;
                            case "FitH": case "FitBH": case "FitV": case "FitBV": l = d[2];
                            break;
                            default: console.log(`Oops, link ${d[1].name} is not supported.`) 
                        }
                        const g = t.getViewport({ scale: 4, offsetY: 4 * (l - internalOffset * r.height) });
                        c.height = g.height, 
                        c.width = g.width;
                        const w = { canvasContext: c.getContext("2d"), viewport: g };
                        t.render(w) 
                }
            ),
            t.prepend(c),
            e._previewing = !0, 
            a.addEventListener("mouseleave", 
                function (t) {
                    c.remove(), 
                    e._previewing = !1 
                })
        }
     e._previewing = !1, t.addEventListener("mouseover", o), e._previewHandler = o })();
```