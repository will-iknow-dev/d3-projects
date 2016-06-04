##Lets Make a Bar Chart II

#Introducing SVG

Whereas HTML is largely limited to rectangular shapes, SVG supports powerful drawing primitives like Bezier curves, gradients, clipping and masks. We won't needall of SVG's extensive features set for a lowly bar chart, but learning SVG is a worthwhile addition to your visual lexicon when it comes to designing visualizations.

Like anything, this richness necessarily comes at a cost. The large SVG specification may be intimidating, but remember that you don't need to master every feature to get started. Browsing examples is a an enjoyable way to pick up new techniques.

And despite obvious differences, SVG and HTML share many similarities. You can write SVG markup and embed it directly in a web page (provided you use <!DOCTYPE html>. You can inspect SVG elements in your browser's developer tools. And SVG elements can be styled with CSS, albeit using different property names like fill instead of background-color. However, unlike HTML, SVG elements must be positioned relative to the top-left corner of the container; SVG does not support flow layout or even text wrapping.

#Coding a chart, Manually

Before we construct the new chart using JavaScript, let's revisit the static specification in SVG.

    <!DOCTYPE html>
    <style>
        .chart rect {
            fill: steelblue;
        }

        .chart text {
            fill: white;
            font: 10px sans-serif;
            text-anchor: end;
        }
    </style>
    <svg class="chart" width="420" height="120">
        <g transform="translate(0,0)">
            <rect width="40" height="19"></rect>
            <text x="37" y="9.5" dy=".35em">4</text>
        </g>
        <g transform="translate(0,20)">
            <rect width="80" height="19"></rect>
            <text x="77" y="9.5" dy=".35em">8</text>
        <g transform="trans;ate(0,40)">
            <rect width="150" height="19"></rect>
            <text x="147" y="9.5" dy=".35em">15</text>
        <g transform="translate(0,60)">
            <rect width="160" height="19"></rect>
            <text x="157" y="9.5" dy=".35em">16</text>
      </g>
        <g transform="translate(0,80)">
            <rect width="230" height="19"></rect>
            <text x="227" y="9.5" dy=".35em">23</text>
      </g>
        <g transform="translate(0,100)">
            <rect width="420" height="19"></rect>
            <text x="417" y="9.5" dy=".35em">42</text>
        </g>
    </svg>

As before, a stylesheet applies colors and other aesthetic properties to the SVG elements. But unlike the div elements that were implicitly positioned using flow layout, the SVG elements must be absolutely positioned with hard-coded translations relative to the origin.

A common point of confusion in SVG is distinguishing between properties that must be specified as attributes and properties that can be set as styles. The full list of styling properties is documented in the specification, but a quick rule of thumb is that geometry(such as the width of a rect element) must be specified as attributes, which aesthetics (such as a fill color) can be specified as styles. While you can use attributes for anything, I recommend you prefer styles for aesthetics; this ensures any inline styles play nicely with cascading stylesheets.

SVG requires text to be placed explicity in text elements. Since text elements do not support padding or margins, the text position must be offset by three pixels from the end of the bar, while the dy offset is used to center the text vertically.

##Coding a Chart, Automatically

Next let's construct the chart using D3. By now, parts of this code should look familiar:

        <!DOCTYPE html>
        <meta charset="utf-8">
        <style>
                .chart rect {
                    fill: white;
                    font: 10px sans-serif;
                    text-anchor: end;
                }
         </style>
         <svg class="chart"></svg>
         <script src="https://d3js.org/d3.v3.min.js" charset="utf-8"></script>
         <script>
                var data = [4, 8, 15, 16, 23, 42];

