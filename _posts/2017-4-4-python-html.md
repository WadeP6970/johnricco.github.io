--- 
layout: post 
title: Using Python to scrape HTML tables with merged cells
---

The python library `pandas` makes it easy to scrape HTML tables from the web. The `read_html()` method is very straightfoward and, for most tables, does a great job. It reads a page's tables directly into memory as a list of pandas dataframes. 

For example, say we wanted to scrape the Flow of Funds table found on [this page](https://www.federalreserve.gov/apps/fof/DisplayTable.aspx?t=f.105). We could do the following: 

```python
import pandas as pd 
fof_url = "https://www.federalreserve.gov/apps/fof/DisplayTable.aspx?t=f.105"
fof = pd.read_html(fof_url)[0]
fof.to_csv("fof.csv", header = False, index = False)
```

...which scrapes the table then saves it as a CSV which ends up looking like this:

(fof_csv.png)

However, not all tables are this simple. What if the table has cells that are merged, as is common in multi-level column titles? For example, the first table on this Social Security Supplement 

<div class="table" id="table3.e1">
  <table>
    <caption><span class="tableNumber">Table&nbsp;3.E1 </span>Weighted average poverty thresholds for nonfarm families, by size, <span class="nobr">1959&ndash;2014</span> (in&nbsp;dollars)</caption>
    <colgroup span="1" style="width:4em"></colgroup>
    <colgroup span="14" style="width:5em"></colgroup>
    <thead>
      <tr>
        <th rowspan="3" class="stubHeading" id="c1">Year</th>
        <th colspan="3" rowspan="2" class="spanner" id="c2">Unrelated individuals</th>
        <th colspan="10" class="spanner" id="c3">Families</th>
        <th rowspan="3" id="c4">Annual average <abbr class="spell">CPI</abbr>&nbsp;<sup>a</sup></th>
      </tr>
      <tr>
        <th colspan="3" class="spanner" id="c5" headers="c3">2&nbsp;persons</th>
        <th rowspan="2" id="c6" headers="c3">3&nbsp;persons</th>
        <th rowspan="2" id="c7" headers="c3">4&nbsp;persons</th>
        <th rowspan="2" id="c8" headers="c3">5&nbsp;persons</th>
        <th rowspan="2" id="c9" headers="c3">6&nbsp;persons</th>
        <th rowspan="2" id="c10" headers="c3">7&nbsp;persons</th>
        <th rowspan="2" id="c11" headers="c3">8&nbsp;persons</th>
        <th rowspan="2" id="c12" headers="c3">9&nbsp;persons or more</th>
      </tr>
      <tr>
        <th id="c13" headers="c2">All ages</th>
        <th id="c14" headers="c2">Under age&nbsp;65</th>
        <th id="c15" headers="c2">Aged&nbsp;65 or older</th>
        <th id="c16" headers="c3 c5">All ages</th>
        <th id="c17" headers="c3 c5">Householder under age&nbsp;65</th>
        <th id="c18" headers="c3 c5">Householder aged&nbsp;65 or older</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <th class="stub0" id="r1" headers="c1">1959</th>
        <td headers="r1 c2 c13">1,467</td>
        <td headers="r1 c2 c14">1,503</td>
        <td headers="r1 c2 c15">1,397</td>
        <td headers="r1 c3 c5 c16">1,894</td>
        <td headers="r1 c3 c5 c17">1,952</td>
        <td headers="r1 c3 c5 c18">1,761</td>
        <td headers="r1 c3 c6">2,324</td>
        <td headers="r1 c3 c7">2,973</td>
        <td headers="r1 c3 c8">3,506</td>
        <td headers="r1 c3 c9">3,944</td>
        <td headers="r1 c3 c10">.&nbsp;.&nbsp;.</td>
        <td headers="r1 c3 c11">.&nbsp;.&nbsp;.</td>
        <td headers="r1 c3 c12">.&nbsp;.&nbsp;.</td>
        <td headers="r1 c4">29.2</td>
      </tr>
      <tr>
        <th class="stub0" id="r2" headers="c1">1960</th>
        <td headers="r2 c2 c13">1,490</td>
        <td headers="r2 c2 c14">1,526</td>
        <td headers="r2 c2 c15">1,418</td>
        <td headers="r2 c3 c5 c16">1,924</td>
        <td headers="r2 c3 c5 c17">1,982</td>
        <td headers="r2 c3 c5 c18">1,788</td>
        <td headers="r2 c3 c6">2,359</td>
        <td headers="r2 c3 c7">3,022</td>
        <td headers="r2 c3 c8">3,560</td>
        <td headers="r2 c3 c9">4,002</td>
        <td headers="r2 c3 c10">.&nbsp;.&nbsp;.</td>
        <td headers="r2 c3 c11">.&nbsp;.&nbsp;.</td>
        <td headers="r2 c3 c12">.&nbsp;.&nbsp;.</td>
        <td headers="r2 c4">29.6</td>
      </tr>
      <tr>
        <th class="stub0" id="r3" headers="c1">1961</th>
        <td headers="r3 c2 c13">1,506</td>
        <td headers="r3 c2 c14">1,545</td>
        <td headers="r3 c2 c15">1,433</td>
        <td headers="r3 c3 c5 c16">1,942</td>
        <td headers="r3 c3 c5 c17">2,005</td>
        <td headers="r3 c3 c5 c18">1,808</td>
        <td headers="r3 c3 c6">2,383</td>
        <td headers="r3 c3 c7">3,054</td>
        <td headers="r3 c3 c8">3,597</td>
        <td headers="r3 c3 c9">4,041</td>
        <td headers="r3 c3 c10">.&nbsp;.&nbsp;.</td>
        <td headers="r3 c3 c11">.&nbsp;.&nbsp;.</td>
        <td headers="r3 c3 c12">.&nbsp;.&nbsp;.</td>
        <td headers="r3 c4">29.9</td>
      </tr>
      <tr>
        <th class="stub0" id="r4" headers="c1">1962</th>
        <td headers="r4 c2 c13">1,519</td>
        <td headers="r4 c2 c14">1,562</td>
        <td headers="r4 c2 c15">1,451</td>
        <td headers="r4 c3 c5 c16">1,962</td>
        <td headers="r4 c3 c5 c17">2,027</td>
        <td headers="r4 c3 c5 c18">1,828</td>
        <td headers="r4 c3 c6">2,412</td>
        <td headers="r4 c3 c7">3,089</td>
        <td headers="r4 c3 c8">3,639</td>
        <td headers="r4 c3 c9">4,088</td>
        <td headers="r4 c3 c10">.&nbsp;.&nbsp;.</td>
        <td headers="r4 c3 c11">.&nbsp;.&nbsp;.</td>
        <td headers="r4 c3 c12">.&nbsp;.&nbsp;.</td>
        <td headers="r4 c4">30.3</td>
      </tr>
      <tr>
        <th class="stub0" id="r5" headers="c1">1963</th>
        <td headers="r5 c2 c13">1,539</td>
        <td headers="r5 c2 c14">1,581</td>
        <td headers="r5 c2 c15">1,470</td>
        <td headers="r5 c3 c5 c16">1,988</td>
        <td headers="r5 c3 c5 c17">2,052</td>
        <td headers="r5 c3 c5 c18">1,850</td>
        <td headers="r5 c3 c6">2,442</td>
        <td headers="r5 c3 c7">3,128</td>
        <td headers="r5 c3 c8">3,685</td>
        <td headers="r5 c3 c9">4,135</td>
        <td headers="r5 c3 c10">.&nbsp;.&nbsp;.</td>
        <td headers="r5 c3 c11">.&nbsp;.&nbsp;.</td>
        <td headers="r5 c3 c12">.&nbsp;.&nbsp;.</td>
        <td headers="r5 c4">30.6</td>
      </tr>
      <tr>
        <th class="stub0" id="r6" headers="c1">1964</th>
        <td headers="r6 c2 c13">1,558</td>
        <td headers="r6 c2 c14">1,601</td>
        <td headers="r6 c2 c15">1,488</td>
        <td headers="r6 c3 c5 c16">2,015</td>
        <td headers="r6 c3 c5 c17">2,079</td>
        <td headers="r6 c3 c5 c18">1,875</td>
        <td headers="r6 c3 c6">2,473</td>
        <td headers="r6 c3 c7">3,169</td>
        <td headers="r6 c3 c8">3,732</td>
        <td headers="r6 c3 c9">4,193</td>
        <td headers="r6 c3 c10">.&nbsp;.&nbsp;.</td>
        <td headers="r6 c3 c11">.&nbsp;.&nbsp;.</td>
        <td headers="r6 c3 c12">.&nbsp;.&nbsp;.</td>
        <td headers="r6 c4">31.0</td>
      </tr>
      <tr class="topPad1">
        <th class="stub0" id="r7" headers="c1">1965</th>
        <td headers="r7 c2 c13">1,582</td>
        <td headers="r7 c2 c14">1,626</td>
        <td headers="r7 c2 c15">1,512</td>
        <td headers="r7 c3 c5 c16">2,048</td>
        <td headers="r7 c3 c5 c17">2,114</td>
        <td headers="r7 c3 c5 c18">1,906</td>
        <td headers="r7 c3 c6">2,514</td>
        <td headers="r7 c3 c7">3,223</td>
        <td headers="r7 c3 c8">3,797</td>
        <td headers="r7 c3 c9">4,264</td>
        <td headers="r7 c3 c10">.&nbsp;.&nbsp;.</td>
        <td headers="r7 c3 c11">.&nbsp;.&nbsp;.</td>
        <td headers="r7 c3 c12">.&nbsp;.&nbsp;.</td>
        <td headers="r7 c4">31.5</td>
      </tr>
      <tr>
        <th class="stub0" id="r8" headers="c1">1966</th>
        <td headers="r8 c2 c13">1,628</td>
        <td headers="r8 c2 c14">1,674</td>
        <td headers="r8 c2 c15">1,556</td>
        <td headers="r8 c3 c5 c16">2,107</td>
        <td headers="r8 c3 c5 c17">2,175</td>
        <td headers="r8 c3 c5 c18">1,961</td>
        <td headers="r8 c3 c6">2,588</td>
        <td headers="r8 c3 c7">3,317</td>
        <td headers="r8 c3 c8">3,908</td>
        <td headers="r8 c3 c9">4,388</td>
        <td headers="r8 c3 c10">.&nbsp;.&nbsp;.</td>
        <td headers="r8 c3 c11">.&nbsp;.&nbsp;.</td>
        <td headers="r8 c3 c12">.&nbsp;.&nbsp;.</td>
        <td headers="r8 c4">32.5</td>
      </tr>
      <tr>
        <th class="stub0" id="r9" headers="c1">1967</th>
        <td headers="r9 c2 c13">1,675</td>
        <td headers="r9 c2 c14">1,722</td>
        <td headers="r9 c2 c15">1,600</td>
        <td headers="r9 c3 c5 c16">2,168</td>
        <td headers="r9 c3 c5 c17">2,238</td>
        <td headers="r9 c3 c5 c18">2,017</td>
        <td headers="r9 c3 c6">2,661</td>
        <td headers="r9 c3 c7">3,410</td>
        <td headers="r9 c3 c8">4,019</td>
        <td headers="r9 c3 c9">4,516</td>
        <td headers="r9 c3 c10">.&nbsp;.&nbsp;.</td>
        <td headers="r9 c3 c11">.&nbsp;.&nbsp;.</td>
        <td headers="r9 c3 c12">.&nbsp;.&nbsp;.</td>
        <td headers="r9 c4">33.4</td>
      </tr>
      <tr>
        <th class="stub0" id="r10" headers="c1">1968</th>
        <td headers="r10 c2 c13">1,748</td>
        <td headers="r10 c2 c14">1,797</td>
        <td headers="r10 c2 c15">1,667</td>
        <td headers="r10 c3 c5 c16">2,262</td>
        <td headers="r10 c3 c5 c17">2,333</td>
        <td headers="r10 c3 c5 c18">2,102</td>
        <td headers="r10 c3 c6">2,774</td>
        <td headers="r10 c3 c7">3,553</td>
        <td headers="r10 c3 c8">4,188</td>
        <td headers="r10 c3 c9">4,706</td>
        <td headers="r10 c3 c10">.&nbsp;.&nbsp;.</td>
        <td headers="r10 c3 c11">.&nbsp;.&nbsp;.</td>
        <td headers="r10 c3 c12">.&nbsp;.&nbsp;.</td>
        <td headers="r10 c4">34.8</td>
      </tr>
      <tr>
        <th class="stub0" id="r11" headers="c1">1969</th>
        <td headers="r11 c2 c13">1,840</td>
        <td headers="r11 c2 c14">1,893</td>
        <td headers="r11 c2 c15">1,757</td>
        <td headers="r11 c3 c5 c16">2,383</td>
        <td headers="r11 c3 c5 c17">2,458</td>
        <td headers="r11 c3 c5 c18">2,215</td>
        <td headers="r11 c3 c6">2,924</td>
        <td headers="r11 c3 c7">3,743</td>
        <td headers="r11 c3 c8">4,415</td>
        <td headers="r11 c3 c9">4,958</td>
        <td headers="r11 c3 c10">.&nbsp;.&nbsp;.</td>
        <td headers="r11 c3 c11">.&nbsp;.&nbsp;.</td>
        <td headers="r11 c3 c12">.&nbsp;.&nbsp;.</td>
        <td headers="r11 c4">36.7</td>
      </tr>
      <tr class="topPad1">
        <th class="stub0" id="r12" headers="c1">1970</th>
        <td headers="r12 c2 c13">1,954</td>
        <td headers="r12 c2 c14">2,010</td>
        <td headers="r12 c2 c15">1,861</td>
        <td headers="r12 c3 c5 c16">2,525</td>
        <td headers="r12 c3 c5 c17">2,604</td>
        <td headers="r12 c3 c5 c18">2,348</td>
        <td headers="r12 c3 c6">3,099</td>
        <td headers="r12 c3 c7">3,968</td>
        <td headers="r12 c3 c8">4,680</td>
        <td headers="r12 c3 c9">5,260</td>
        <td headers="r12 c3 c10">.&nbsp;.&nbsp;.</td>
        <td headers="r12 c3 c11">.&nbsp;.&nbsp;.</td>
        <td headers="r12 c3 c12">.&nbsp;.&nbsp;.</td>
        <td headers="r12 c4">38.8</td>
      </tr>
      <tr>
        <th class="stub0" id="r13" headers="c1">1971</th>
        <td headers="r13 c2 c13">2,040</td>
        <td headers="r13 c2 c14">2,098</td>
        <td headers="r13 c2 c15">1,940</td>
        <td headers="r13 c3 c5 c16">2,633</td>
        <td headers="r13 c3 c5 c17">2,716</td>
        <td headers="r13 c3 c5 c18">2,448</td>
        <td headers="r13 c3 c6">3,229</td>
        <td headers="r13 c3 c7">4,137</td>
        <td headers="r13 c3 c8">4,880</td>
        <td headers="r13 c3 c9">5,489</td>
        <td headers="r13 c3 c10">.&nbsp;.&nbsp;.</td>
        <td headers="r13 c3 c11">.&nbsp;.&nbsp;.</td>
        <td headers="r13 c3 c12">.&nbsp;.&nbsp;.</td>
        <td headers="r13 c4">40.5</td>
      </tr>
      <tr>
        <th class="stub0" id="r14" headers="c1">1972</th>
        <td headers="r14 c2 c13">2,109</td>
        <td headers="r14 c2 c14">2,168</td>
        <td headers="r14 c2 c15">2,005</td>
        <td headers="r14 c3 c5 c16">2,724</td>
        <td headers="r14 c3 c5 c17">2,808</td>
        <td headers="r14 c3 c5 c18">2,530</td>
        <td headers="r14 c3 c6">3,339</td>
        <td headers="r14 c3 c7">4,275</td>
        <td headers="r14 c3 c8">5,044</td>
        <td headers="r14 c3 c9">5,673</td>
        <td headers="r14 c3 c10">.&nbsp;.&nbsp;.</td>
        <td headers="r14 c3 c11">.&nbsp;.&nbsp;.</td>
        <td headers="r14 c3 c12">.&nbsp;.&nbsp;.</td>
        <td headers="r14 c4">41.8</td>
      </tr>
      <tr>
        <th class="stub0" id="r15" headers="c1">1973</th>
        <td headers="r15 c2 c13">2,247</td>
        <td headers="r15 c2 c14">2,307</td>
        <td headers="r15 c2 c15">2,130</td>
        <td headers="r15 c3 c5 c16">2,895</td>
        <td headers="r15 c3 c5 c17">2,984</td>
        <td headers="r15 c3 c5 c18">2,688</td>
        <td headers="r15 c3 c6">3,548</td>
        <td headers="r15 c3 c7">4,540</td>
        <td headers="r15 c3 c8">5,358</td>
        <td headers="r15 c3 c9">6,028</td>
        <td headers="r15 c3 c10">.&nbsp;.&nbsp;.</td>
        <td headers="r15 c3 c11">.&nbsp;.&nbsp;.</td>
        <td headers="r15 c3 c12">.&nbsp;.&nbsp;.</td>
        <td headers="r15 c4">44.4</td>
      </tr>
      <tr>
        <th class="stub0" id="r16" headers="c1">1974</th>
        <td headers="r16 c2 c13">2,495</td>
        <td headers="r16 c2 c14">2,562</td>
        <td headers="r16 c2 c15">2,364</td>
        <td headers="r16 c3 c5 c16">3,211</td>
        <td headers="r16 c3 c5 c17">3,312</td>
        <td headers="r16 c3 c5 c18">2,982</td>
        <td headers="r16 c3 c6">3,936</td>
        <td headers="r16 c3 c7">5,038</td>
        <td headers="r16 c3 c8">5,950</td>
        <td headers="r16 c3 c9">6,699</td>
        <td headers="r16 c3 c10">.&nbsp;.&nbsp;.</td>
        <td headers="r16 c3 c11">.&nbsp;.&nbsp;.</td>
        <td headers="r16 c3 c12">.&nbsp;.&nbsp;.</td>
        <td headers="r16 c4">49.3</td>
      </tr>
      <tr class="topPad1">
        <th class="stub0" id="r17" headers="c1">1975</th>
        <td headers="r17 c2 c13">2,724</td>
        <td headers="r17 c2 c14">2,797</td>
        <td headers="r17 c2 c15">2,581</td>
        <td headers="r17 c3 c5 c16">3,506</td>
        <td headers="r17 c3 c5 c17">3,617</td>
        <td headers="r17 c3 c5 c18">3,257</td>
        <td headers="r17 c3 c6">4,293</td>
        <td headers="r17 c3 c7">5,500</td>
        <td headers="r17 c3 c8">6,499</td>
        <td headers="r17 c3 c9">7,316</td>
        <td headers="r17 c3 c10">.&nbsp;.&nbsp;.</td>
        <td headers="r17 c3 c11">.&nbsp;.&nbsp;.</td>
        <td headers="r17 c3 c12">.&nbsp;.&nbsp;.</td>
        <td headers="r17 c4">53.8</td>
      </tr>
      <tr>
        <th class="stub0" id="r18" headers="c1">1976</th>
        <td headers="r18 c2 c13">2,884</td>
        <td headers="r18 c2 c14">2,959</td>
        <td headers="r18 c2 c15">2,730</td>
        <td headers="r18 c3 c5 c16">3,711</td>
        <td headers="r18 c3 c5 c17">3,826</td>
        <td headers="r18 c3 c5 c18">3,445</td>
        <td headers="r18 c3 c6">4,540</td>
        <td headers="r18 c3 c7">5,815</td>
        <td headers="r18 c3 c8">6,876</td>
        <td headers="r18 c3 c9">7,760</td>
        <td headers="r18 c3 c10">.&nbsp;.&nbsp;.</td>
        <td headers="r18 c3 c11">.&nbsp;.&nbsp;.</td>
        <td headers="r18 c3 c12">.&nbsp;.&nbsp;.</td>
        <td headers="r18 c4">56.9</td>
      </tr>
      <tr>
        <th class="stub0" id="r19" headers="c1">1977</th>
        <td headers="r19 c2 c13">3,075</td>
        <td headers="r19 c2 c14">3,152</td>
        <td headers="r19 c2 c15">2,906</td>
        <td headers="r19 c3 c5 c16">3,951</td>
        <td headers="r19 c3 c5 c17">4,072</td>
        <td headers="r19 c3 c5 c18">3,666</td>
        <td headers="r19 c3 c6">4,833</td>
        <td headers="r19 c3 c7">6,191</td>
        <td headers="r19 c3 c8">7,320</td>
        <td headers="r19 c3 c9">8,261</td>
        <td headers="r19 c3 c10">.&nbsp;.&nbsp;.</td>
        <td headers="r19 c3 c11">.&nbsp;.&nbsp;.</td>
        <td headers="r19 c3 c12">.&nbsp;.&nbsp;.</td>
        <td headers="r19 c4">60.6</td>
      </tr>
      <tr>
        <th class="stub0" id="r20" headers="c1">1978</th>
        <td headers="r20 c2 c13">3,311</td>
        <td headers="r20 c2 c14">3,392</td>
        <td headers="r20 c2 c15">3,127</td>
        <td headers="r20 c3 c5 c16">4,249</td>
        <td headers="r20 c3 c5 c17">4,383</td>
        <td headers="r20 c3 c5 c18">3,944</td>
        <td headers="r20 c3 c6">5,201</td>
        <td headers="r20 c3 c7">6,662</td>
        <td headers="r20 c3 c8">7,880</td>
        <td headers="r20 c3 c9">8,891</td>
        <td headers="r20 c3 c10">.&nbsp;.&nbsp;.</td>
        <td headers="r20 c3 c11">.&nbsp;.&nbsp;.</td>
        <td headers="r20 c3 c12">.&nbsp;.&nbsp;.</td>
        <td headers="r20 c4">65.2</td>
      </tr>
      <tr>
        <th class="stub0" id="r21" headers="c1">1979</th>
        <td headers="r21 c2 c13">3,689</td>
        <td headers="r21 c2 c14">3,778</td>
        <td headers="r21 c2 c15">3,479</td>
        <td headers="r21 c3 c5 c16">4,725</td>
        <td headers="r21 c3 c5 c17">4,878</td>
        <td headers="r21 c3 c5 c18">4,390</td>
        <td headers="r21 c3 c6">5,784</td>
        <td headers="r21 c3 c7">7,412</td>
        <td headers="r21 c3 c8">8,775</td>
        <td headers="r21 c3 c9">9,914</td>
        <td headers="r21 c3 c10">.&nbsp;.&nbsp;.</td>
        <td headers="r21 c3 c11">.&nbsp;.&nbsp;.</td>
        <td headers="r21 c3 c12">.&nbsp;.&nbsp;.</td>
        <td headers="r21 c4">72.6</td>
      </tr>
      <tr class="topPad1">
        <th class="stub0" id="r22" headers="c1">1980</th>
        <td headers="r22 c2 c13">4,190</td>
        <td headers="r22 c2 c14">4,290</td>
        <td headers="r22 c2 c15">3,949</td>
        <td headers="r22 c3 c5 c16">5,363</td>
        <td headers="r22 c3 c5 c17">5,537</td>
        <td headers="r22 c3 c5 c18">4,983</td>
        <td headers="r22 c3 c6">6,565</td>
        <td headers="r22 c3 c7">8,414</td>
        <td headers="r22 c3 c8">9,966</td>
        <td headers="r22 c3 c9">11,269</td>
        <td headers="r22 c3 c10">12,761</td>
        <td headers="r22 c3 c11">14,199</td>
        <td headers="r22 c3 c12">16,896</td>
        <td headers="r22 c4">82.4</td>
      </tr>
      <tr>
        <th class="stub0" id="r23" headers="c1">1981</th>
        <td headers="r23 c2 c13">4,620</td>
        <td headers="r23 c2 c14">4,729</td>
        <td headers="r23 c2 c15">4,359</td>
        <td headers="r23 c3 c5 c16">5,917</td>
        <td headers="r23 c3 c5 c17">6,111</td>
        <td headers="r23 c3 c5 c18">5,498</td>
        <td headers="r23 c3 c6">7,250</td>
        <td headers="r23 c3 c7">9,287</td>
        <td headers="r23 c3 c8">11,007</td>
        <td headers="r23 c3 c9">12,449</td>
        <td headers="r23 c3 c10">14,110</td>
        <td headers="r23 c3 c11">15,655</td>
        <td headers="r23 c3 c12">18,572</td>
        <td headers="r23 c4">90.9</td>
      </tr>
      <tr>
        <th class="stub0" id="r24" headers="c1">1982</th>
        <td headers="r24 c2 c13">4,901</td>
        <td headers="r24 c2 c14">5,019</td>
        <td headers="r24 c2 c15">4,626</td>
        <td headers="r24 c3 c5 c16">6,281</td>
        <td headers="r24 c3 c5 c17">6,487</td>
        <td headers="r24 c3 c5 c18">5,836</td>
        <td headers="r24 c3 c6">7,693</td>
        <td headers="r24 c3 c7">9,862</td>
        <td headers="r24 c3 c8">11,684</td>
        <td headers="r24 c3 c9">13,207</td>
        <td headers="r24 c3 c10">15,036</td>
        <td headers="r24 c3 c11">16,719</td>
        <td headers="r24 c3 c12">19,698</td>
        <td headers="r24 c4">96.5</td>
      </tr>
      <tr>
        <th class="stub0" id="r25" headers="c1">1983</th>
        <td headers="r25 c2 c13">5,061</td>
        <td headers="r25 c2 c14">5,180</td>
        <td headers="r25 c2 c15">4,775</td>
        <td headers="r25 c3 c5 c16">6,483</td>
        <td headers="r25 c3 c5 c17">6,697</td>
        <td headers="r25 c3 c5 c18">6,023</td>
        <td headers="r25 c3 c6">7,938</td>
        <td headers="r25 c3 c7">10,178</td>
        <td headers="r25 c3 c8">12,049</td>
        <td headers="r25 c3 c9">13,630</td>
        <td headers="r25 c3 c10">15,500</td>
        <td headers="r25 c3 c11">17,170</td>
        <td headers="r25 c3 c12">20,310</td>
        <td headers="r25 c4">99.6</td>
      </tr>
      <tr>
        <th class="stub0" id="r26" headers="c1">1984</th>
        <td headers="r26 c2 c13">5,278</td>
        <td headers="r26 c2 c14">5,400</td>
        <td headers="r26 c2 c15">4,979</td>
        <td headers="r26 c3 c5 c16">6,762</td>
        <td headers="r26 c3 c5 c17">6,983</td>
        <td headers="r26 c3 c5 c18">6,282</td>
        <td headers="r26 c3 c6">8,277</td>
        <td headers="r26 c3 c7">10,609</td>
        <td headers="r26 c3 c8">12,566</td>
        <td headers="r26 c3 c9">14,207</td>
        <td headers="r26 c3 c10">16,096</td>
        <td headers="r26 c3 c11">17,961</td>
        <td headers="r26 c3 c12">21,247</td>
        <td headers="r26 c4">103.9</td>
      </tr>
      <tr class="topPad1">
        <th class="stub0" id="r27" headers="c1">1985</th>
        <td headers="r27 c2 c13">5,469</td>
        <td headers="r27 c2 c14">5,593</td>
        <td headers="r27 c2 c15">5,156</td>
        <td headers="r27 c3 c5 c16">6,998</td>
        <td headers="r27 c3 c5 c17">7,231</td>
        <td headers="r27 c3 c5 c18">6,503</td>
        <td headers="r27 c3 c6">8,573</td>
        <td headers="r27 c3 c7">10,989</td>
        <td headers="r27 c3 c8">13,007</td>
        <td headers="r27 c3 c9">14,696</td>
        <td headers="r27 c3 c10">16,656</td>
        <td headers="r27 c3 c11">18,512</td>
        <td headers="r27 c3 c12">22,083</td>
        <td headers="r27 c4">107.6</td>
      </tr>
      <tr>
        <th class="stub0" id="r28" headers="c1">1986</th>
        <td headers="r28 c2 c13">5,572</td>
        <td headers="r28 c2 c14">5,701</td>
        <td headers="r28 c2 c15">5,255</td>
        <td headers="r28 c3 c5 c16">7,138</td>
        <td headers="r28 c3 c5 c17">7,372</td>
        <td headers="r28 c3 c5 c18">6,630</td>
        <td headers="r28 c3 c6">8,737</td>
        <td headers="r28 c3 c7">11,203</td>
        <td headers="r28 c3 c8">13,259</td>
        <td headers="r28 c3 c9">14,986</td>
        <td headers="r28 c3 c10">17,049</td>
        <td headers="r28 c3 c11">18,791</td>
        <td headers="r28 c3 c12">22,497</td>
        <td headers="r28 c4">109.6</td>
      </tr>
      <tr>
        <th class="stub0" id="r29" headers="c1">1987</th>
        <td headers="r29 c2 c13">5,778</td>
        <td headers="r29 c2 c14">5,909</td>
        <td headers="r29 c2 c15">5,447</td>
        <td headers="r29 c3 c5 c16">7,397</td>
        <td headers="r29 c3 c5 c17">7,641</td>
        <td headers="r29 c3 c5 c18">6,872</td>
        <td headers="r29 c3 c6">9,056</td>
        <td headers="r29 c3 c7">11,611</td>
        <td headers="r29 c3 c8">13,737</td>
        <td headers="r29 c3 c9">15,509</td>
        <td headers="r29 c3 c10">17,649</td>
        <td headers="r29 c3 c11">19,515</td>
        <td headers="r29 c3 c12">23,105</td>
        <td headers="r29 c4">113.6</td>
      </tr>
      <tr>
        <th class="stub0" id="r30" headers="c1">1988</th>
        <td headers="r30 c2 c13">6,022</td>
        <td headers="r30 c2 c14">6,155</td>
        <td headers="r30 c2 c15">5,674</td>
        <td headers="r30 c3 c5 c16">7,704</td>
        <td headers="r30 c3 c5 c17">7,958</td>
        <td headers="r30 c3 c5 c18">7,157</td>
        <td headers="r30 c3 c6">9,435</td>
        <td headers="r30 c3 c7">12,092</td>
        <td headers="r30 c3 c8">14,304</td>
        <td headers="r30 c3 c9">16,146</td>
        <td headers="r30 c3 c10">18,232</td>
        <td headers="r30 c3 c11">20,253</td>
        <td headers="r30 c3 c12">24,129</td>
        <td headers="r30 c4">118.3</td>
      </tr>
      <tr>
        <th class="stub0" id="r31" headers="c1">1989</th>
        <td headers="r31 c2 c13">6,310</td>
        <td headers="r31 c2 c14">6,451</td>
        <td headers="r31 c2 c15">5,947</td>
        <td headers="r31 c3 c5 c16">8,076</td>
        <td headers="r31 c3 c5 c17">8,343</td>
        <td headers="r31 c3 c5 c18">7,501</td>
        <td headers="r31 c3 c6">9,885</td>
        <td headers="r31 c3 c7">12,674</td>
        <td headers="r31 c3 c8">14,990</td>
        <td headers="r31 c3 c9">16,921</td>
        <td headers="r31 c3 c10">19,162</td>
        <td headers="r31 c3 c11">21,328</td>
        <td headers="r31 c3 c12">25,480</td>
        <td headers="r31 c4">124.0</td>
      </tr>
      <tr class="topPad1">
        <th class="stub0" id="r32" headers="c1">1990</th>
        <td headers="r32 c2 c13">6,652</td>
        <td headers="r32 c2 c14">6,800</td>
        <td headers="r32 c2 c15">6,268</td>
        <td headers="r32 c3 c5 c16">8,509</td>
        <td headers="r32 c3 c5 c17">8,794</td>
        <td headers="r32 c3 c5 c18">7,905</td>
        <td headers="r32 c3 c6">10,419</td>
        <td headers="r32 c3 c7">13,359</td>
        <td headers="r32 c3 c8">15,792</td>
        <td headers="r32 c3 c9">17,839</td>
        <td headers="r32 c3 c10">20,241</td>
        <td headers="r32 c3 c11">22,582</td>
        <td headers="r32 c3 c12">26,848</td>
        <td headers="r32 c4">130.7</td>
      </tr>
      <tr>
        <th class="stub0" id="r33" headers="c1">1991</th>
        <td headers="r33 c2 c13">6,932</td>
        <td headers="r33 c2 c14">7,086</td>
        <td headers="r33 c2 c15">6,532</td>
        <td headers="r33 c3 c5 c16">8,865</td>
        <td headers="r33 c3 c5 c17">9,165</td>
        <td headers="r33 c3 c5 c18">8,241</td>
        <td headers="r33 c3 c6">10,860</td>
        <td headers="r33 c3 c7">13,924</td>
        <td headers="r33 c3 c8">16,456</td>
        <td headers="r33 c3 c9">18,587</td>
        <td headers="r33 c3 c10">21,058</td>
        <td headers="r33 c3 c11">23,605</td>
        <td headers="r33 c3 c12">27,942</td>
        <td headers="r33 c4">136.2</td>
      </tr>
      <tr>
        <th class="stub0" id="r34" headers="c1">1992</th>
        <td headers="r34 c2 c13">7,143</td>
        <td headers="r34 c2 c14">7,299</td>
        <td headers="r34 c2 c15">6,729</td>
        <td headers="r34 c3 c5 c16">9,137</td>
        <td headers="r34 c3 c5 c17">9,443</td>
        <td headers="r34 c3 c5 c18">8,487</td>
        <td headers="r34 c3 c6">11,186</td>
        <td headers="r34 c3 c7">14,335</td>
        <td headers="r34 c3 c8">16,952</td>
        <td headers="r34 c3 c9">19,137</td>
        <td headers="r34 c3 c10">21,594</td>
        <td headers="r34 c3 c11">24,053</td>
        <td headers="r34 c3 c12">28,745</td>
        <td headers="r34 c4">140.3</td>
      </tr>
      <tr>
        <th class="stub0" id="r35" headers="c1">1993</th>
        <td headers="r35 c2 c13">7,363</td>
        <td headers="r35 c2 c14">7,518</td>
        <td headers="r35 c2 c15">6,930</td>
        <td headers="r35 c3 c5 c16">9,414</td>
        <td headers="r35 c3 c5 c17">9,728</td>
        <td headers="r35 c3 c5 c18">8,740</td>
        <td headers="r35 c3 c6">11,522</td>
        <td headers="r35 c3 c7">14,763</td>
        <td headers="r35 c3 c8">17,449</td>
        <td headers="r35 c3 c9">19,718</td>
        <td headers="r35 c3 c10">22,383</td>
        <td headers="r35 c3 c11">24,838</td>
        <td headers="r35 c3 c12">29,529</td>
        <td headers="r35 c4">144.5</td>
      </tr>
      <tr>
        <th class="stub0" id="r36" headers="c1">1994</th>
        <td headers="r36 c2 c13">7,547</td>
        <td headers="r36 c2 c14">7,710</td>
        <td headers="r36 c2 c15">7,108</td>
        <td headers="r36 c3 c5 c16">9,661</td>
        <td headers="r36 c3 c5 c17">9,976</td>
        <td headers="r36 c3 c5 c18">8,967</td>
        <td headers="r36 c3 c6">11,821</td>
        <td headers="r36 c3 c7">15,141</td>
        <td headers="r36 c3 c8">17,900</td>
        <td headers="r36 c3 c9">20,235</td>
        <td headers="r36 c3 c10">22,923</td>
        <td headers="r36 c3 c11">25,427</td>
        <td headers="r36 c3 c12">30,300</td>
        <td headers="r36 c4">148.2</td>
      </tr>
      <tr class="topPad1">
        <th class="stub0" id="r37" headers="c1">1995</th>
        <td headers="r37 c2 c13">7,763</td>
        <td headers="r37 c2 c14">7,929</td>
        <td headers="r37 c2 c15">7,309</td>
        <td headers="r37 c3 c5 c16">9,933</td>
        <td headers="r37 c3 c5 c17">10,259</td>
        <td headers="r37 c3 c5 c18">9,219</td>
        <td headers="r37 c3 c6">12,158</td>
        <td headers="r37 c3 c7">15,569</td>
        <td headers="r37 c3 c8">18,408</td>
        <td headers="r37 c3 c9">20,804</td>
        <td headers="r37 c3 c10">23,552</td>
        <td headers="r37 c3 c11">26,237</td>
        <td headers="r37 c3 c12">31,280</td>
        <td headers="r37 c4">152.4</td>
      </tr>
      <tr>
        <th class="stub0" id="r38" headers="c1">1996</th>
        <td headers="r38 c2 c13">7,995</td>
        <td headers="r38 c2 c14">8,163</td>
        <td headers="r38 c2 c15">7,525</td>
        <td headers="r38 c3 c5 c16">10,233</td>
        <td headers="r38 c3 c5 c17">10,564</td>
        <td headers="r38 c3 c5 c18">9,491</td>
        <td headers="r38 c3 c6">12,516</td>
        <td headers="r38 c3 c7">16,036</td>
        <td headers="r38 c3 c8">18,952</td>
        <td headers="r38 c3 c9">21,389</td>
        <td headers="r38 c3 c10">24,268</td>
        <td headers="r38 c3 c11">27,091</td>
        <td headers="r38 c3 c12">31,971</td>
        <td headers="r38 c4">156.9</td>
      </tr>
      <tr>
        <th class="stub0" id="r39" headers="c1">1997</th>
        <td headers="r39 c2 c13">8,183</td>
        <td headers="r39 c2 c14">8,350</td>
        <td headers="r39 c2 c15">7,698</td>
        <td headers="r39 c3 c5 c16">10,473</td>
        <td headers="r39 c3 c5 c17">10,805</td>
        <td headers="r39 c3 c5 c18">9,712</td>
        <td headers="r39 c3 c6">12,802</td>
        <td headers="r39 c3 c7">16,400</td>
        <td headers="r39 c3 c8">19,380</td>
        <td headers="r39 c3 c9">21,886</td>
        <td headers="r39 c3 c10">24,802</td>
        <td headers="r39 c3 c11">27,593</td>
        <td headers="r39 c3 c12">32,566</td>
        <td headers="r39 c4">160.5</td>
      </tr>
      <tr>
        <th class="stub0" id="r40" headers="c1">1998</th>
        <td headers="r40 c2 c13">8,316</td>
        <td headers="r40 c2 c14">8,480</td>
        <td headers="r40 c2 c15">7,818</td>
        <td headers="r40 c3 c5 c16">10,634</td>
        <td headers="r40 c3 c5 c17">10,972</td>
        <td headers="r40 c3 c5 c18">9,862</td>
        <td headers="r40 c3 c6">13,003</td>
        <td headers="r40 c3 c7">16,660</td>
        <td headers="r40 c3 c8">19,680</td>
        <td headers="r40 c3 c9">22,228</td>
        <td headers="r40 c3 c10">25,257</td>
        <td headers="r40 c3 c11">28,166</td>
        <td headers="r40 c3 c12">33,339</td>
        <td headers="r40 c4">163.0</td>
      </tr>
      <tr>
        <th class="stub0" id="r41" headers="c1">1999</th>
        <td headers="r41 c2 c13">8,501</td>
        <td headers="r41 c2 c14">8,667</td>
        <td headers="r41 c2 c15">7,990</td>
        <td headers="r41 c3 c5 c16">10,869</td>
        <td headers="r41 c3 c5 c17">11,214</td>
        <td headers="r41 c3 c5 c18">10,075</td>
        <td headers="r41 c3 c6">13,290</td>
        <td headers="r41 c3 c7">17,029</td>
        <td headers="r41 c3 c8">20,127</td>
        <td headers="r41 c3 c9">22,727</td>
        <td headers="r41 c3 c10">25,912</td>
        <td headers="r41 c3 c11">28,967</td>
        <td headers="r41 c3 c12">34,417</td>
        <td headers="r41 c4">166.6</td>
      </tr>
      <tr class="topPad1">
        <th class="stub0" id="r42" headers="c1">2000</th>
        <td headers="r42 c2 c13">8,794</td>
        <td headers="r42 c2 c14">8,959</td>
        <td headers="r42 c2 c15">8,259</td>
        <td headers="r42 c3 c5 c16">11,239</td>
        <td headers="r42 c3 c5 c17">11,590</td>
        <td headers="r42 c3 c5 c18">10,419</td>
        <td headers="r42 c3 c6">13,738</td>
        <td headers="r42 c3 c7">17,603</td>
        <td headers="r42 c3 c8">20,819</td>
        <td headers="r42 c3 c9">23,528</td>
        <td headers="r42 c3 c10">26,754</td>
        <td headers="r42 c3 c11">29,701</td>
        <td headers="r42 c3 c12">35,060</td>
        <td headers="r42 c4">172.2</td>
      </tr>
      <tr>
        <th class="stub0" id="r43" headers="c1">2001</th>
        <td headers="r43 c2 c13">9,039</td>
        <td headers="r43 c2 c14">9,214</td>
        <td headers="r43 c2 c15">8,494</td>
        <td headers="r43 c3 c5 c16">11,569</td>
        <td headers="r43 c3 c5 c17">11,920</td>
        <td headers="r43 c3 c5 c18">10,715</td>
        <td headers="r43 c3 c6">14,128</td>
        <td headers="r43 c3 c7">18,104</td>
        <td headers="r43 c3 c8">21,405</td>
        <td headers="r43 c3 c9">24,195</td>
        <td headers="r43 c3 c10">27,517</td>
        <td headers="r43 c3 c11">30,627</td>
        <td headers="r43 c3 c12">36,286</td>
        <td headers="r43 c4">177.1</td>
      </tr>
      <tr>
        <th class="stub0" id="r44" headers="c1">2002</th>
        <td headers="r44 c2 c13">9,183</td>
        <td headers="r44 c2 c14">9,359</td>
        <td headers="r44 c2 c15">8,628</td>
        <td headers="r44 c3 c5 c16">11,756</td>
        <td headers="r44 c3 c5 c17">12,110</td>
        <td headers="r44 c3 c5 c18">10,885</td>
        <td headers="r44 c3 c6">14,348</td>
        <td headers="r44 c3 c7">18,392</td>
        <td headers="r44 c3 c8">21,744</td>
        <td headers="r44 c3 c9">24,576</td>
        <td headers="r44 c3 c10">28,001</td>
        <td headers="r44 c3 c11">30,907</td>
        <td headers="r44 c3 c12">37,062</td>
        <td headers="r44 c4">179.9</td>
      </tr>
      <tr>
        <th class="stub0" id="r45" headers="c1">2003</th>
        <td headers="r45 c2 c13">9,393</td>
        <td headers="r45 c2 c14">9,573</td>
        <td headers="r45 c2 c15">8,825</td>
        <td headers="r45 c3 c5 c16">12,015</td>
        <td headers="r45 c3 c5 c17">12,384</td>
        <td headers="r45 c3 c5 c18">11,133</td>
        <td headers="r45 c3 c6">14,680</td>
        <td headers="r45 c3 c7">18,810</td>
        <td headers="r45 c3 c8">22,245</td>
        <td headers="r45 c3 c9">25,122</td>
        <td headers="r45 c3 c10">28,544</td>
        <td headers="r45 c3 c11">31,589</td>
        <td headers="r45 c3 c12">37,656</td>
        <td headers="r45 c4">184.0</td>
      </tr>
      <tr>
        <th class="stub0" id="r46" headers="c1">2004</th>
        <td headers="r46 c2 c13">9,645</td>
        <td headers="r46 c2 c14">9,827</td>
        <td headers="r46 c2 c15">9,060</td>
        <td headers="r46 c3 c5 c16">12,334</td>
        <td headers="r46 c3 c5 c17">12,714</td>
        <td headers="r46 c3 c5 c18">11,430</td>
        <td headers="r46 c3 c6">15,067</td>
        <td headers="r46 c3 c7">19,307</td>
        <td headers="r46 c3 c8">22,831</td>
        <td headers="r46 c3 c9">25,788</td>
        <td headers="r46 c3 c10">29,236</td>
        <td headers="r46 c3 c11">32,641</td>
        <td headers="r46 c3 c12">39,048</td>
        <td headers="r46 c4">188.9</td>
      </tr>
      <tr class="topPad1">
        <th class="stub0" id="r47" headers="c1">2005</th>
        <td headers="r47 c2 c13">9,973</td>
        <td headers="r47 c2 c14">10,160</td>
        <td headers="r47 c2 c15">9,367</td>
        <td headers="r47 c3 c5 c16">12,755</td>
        <td headers="r47 c3 c5 c17">13,145</td>
        <td headers="r47 c3 c5 c18">11,815</td>
        <td headers="r47 c3 c6">15,577</td>
        <td headers="r47 c3 c7">19,971</td>
        <td headers="r47 c3 c8">23,613</td>
        <td headers="r47 c3 c9">26,683</td>
        <td headers="r47 c3 c10">30,249</td>
        <td headers="r47 c3 c11">33,610</td>
        <td headers="r47 c3 c12">40,288</td>
        <td headers="r47 c4">195.3</td>
      </tr>
      <tr>
        <th class="stub0" id="r48" headers="c1">2006</th>
        <td headers="r48 c2 c13">10,294</td>
        <td headers="r48 c2 c14">10,488</td>
        <td headers="r48 c2 c15">9,669</td>
        <td headers="r48 c3 c5 c16">13,167</td>
        <td headers="r48 c3 c5 c17">13,569</td>
        <td headers="r48 c3 c5 c18">12,201</td>
        <td headers="r48 c3 c6">16,079</td>
        <td headers="r48 c3 c7">20,614</td>
        <td headers="r48 c3 c8">24,382</td>
        <td headers="r48 c3 c9">27,560</td>
        <td headers="r48 c3 c10">31,205</td>
        <td headers="r48 c3 c11">34,774</td>
        <td headers="r48 c3 c12">41,499</td>
        <td headers="r48 c4">201.6</td>
      </tr>
      <tr>
        <th class="stub0" id="r49" headers="c1">2007</th>
        <td headers="r49 c2 c13">10,590</td>
        <td headers="r49 c2 c14">10,787</td>
        <td headers="r49 c2 c15">9,944</td>
        <td headers="r49 c3 c5 c16">13,540</td>
        <td headers="r49 c3 c5 c17">13,954</td>
        <td headers="r49 c3 c5 c18">12,550</td>
        <td headers="r49 c3 c6">16,530</td>
        <td headers="r49 c3 c7">21,203</td>
        <td headers="r49 c3 c8">25,080</td>
        <td headers="r49 c3 c9">28,323</td>
        <td headers="r49 c3 c10">32,233</td>
        <td headers="r49 c3 c11">35,816</td>
        <td headers="r49 c3 c12">42,739</td>
        <td headers="r49 c4">207.3</td>
      </tr>
      <tr>
        <th class="stub0" id="r50" headers="c1">2008</th>
        <td headers="r50 c2 c13">10,991</td>
        <td headers="r50 c2 c14">11,201</td>
        <td headers="r50 c2 c15">10,326</td>
        <td headers="r50 c3 c5 c16">14,051</td>
        <td headers="r50 c3 c5 c17">14,489</td>
        <td headers="r50 c3 c5 c18">13,030</td>
        <td headers="r50 c3 c6">17,163</td>
        <td headers="r50 c3 c7">22,025</td>
        <td headers="r50 c3 c8">26,049</td>
        <td headers="r50 c3 c9">29,456</td>
        <td headers="r50 c3 c10">33,529</td>
        <td headers="r50 c3 c11">37,220</td>
        <td headers="r50 c3 c12">44,346</td>
        <td headers="r50 c4">215.3</td>
      </tr>
      <tr>
        <th class="stub0" id="r51" headers="c1">2009</th>
        <td headers="r51 c2 c13">10,956</td>
        <td headers="r51 c2 c14">11,161</td>
        <td headers="r51 c2 c15">10,289</td>
        <td headers="r51 c3 c5 c16">13,991</td>
        <td headers="r51 c3 c5 c17">14,439</td>
        <td headers="r51 c3 c5 c18">12,982</td>
        <td headers="r51 c3 c6">17,098</td>
        <td headers="r51 c3 c7">21,954</td>
        <td headers="r51 c3 c8">25,991</td>
        <td headers="r51 c3 c9">29,405</td>
        <td headers="r51 c3 c10">33,372</td>
        <td headers="r51 c3 c11">37,252</td>
        <td headers="r51 c3 c12">44,366</td>
        <td headers="r51 c4">214.5</td>
      </tr>
      <tr class="topPad1">
        <th class="stub0" id="r52" headers="c1">2010</th>
        <td headers="r52 c2 c13">11,139</td>
        <td headers="r52 c2 c14">11,344</td>
        <td headers="r52 c2 c15">10,458</td>
        <td headers="r52 c3 c5 c16">14,218</td>
        <td headers="r52 c3 c5 c17">14,676</td>
        <td headers="r52 c3 c5 c18">13,194</td>
        <td headers="r52 c3 c6">17,374</td>
        <td headers="r52 c3 c7">22,314</td>
        <td headers="r52 c3 c8">26,439</td>
        <td headers="r52 c3 c9">29,897</td>
        <td headers="r52 c3 c10">34,009</td>
        <td headers="r52 c3 c11">37,934</td>
        <td headers="r52 c3 c12">45,220</td>
        <td headers="r52 c4">218.1</td>
      </tr>
      <tr>
        <th class="stub0" id="r53" headers="c1">2011</th>
        <td headers="r53 c2 c13">11,484</td>
        <td headers="r53 c2 c14">11,702</td>
        <td headers="r53 c2 c15">10,788</td>
        <td headers="r53 c3 c5 c16">14,657</td>
        <td headers="r53 c3 c5 c17">15,139</td>
        <td headers="r53 c3 c5 c18">13,609</td>
        <td headers="r53 c3 c6">17,916</td>
        <td headers="r53 c3 c7">23,021</td>
        <td headers="r53 c3 c8">27,251</td>
        <td headers="r53 c3 c9">30,847</td>
        <td headers="r53 c3 c10">35,085</td>
        <td headers="r53 c3 c11">39,064</td>
        <td headers="r53 c3 c12">46,572</td>
        <td headers="r53 c4">224.9</td>
      </tr>
      <tr>
        <th class="stub0" id="r54" headers="c1">2012</th>
        <td headers="r54 c2 c13">11,720</td>
        <td headers="r54 c2 c14">11,945</td>
        <td headers="r54 c2 c15">11,011</td>
        <td headers="r54 c3 c5 c16">14,937</td>
        <td headers="r54 c3 c5 c17">15,450</td>
        <td headers="r54 c3 c5 c18">13,892</td>
        <td headers="r54 c3 c6">18,284</td>
        <td headers="r54 c3 c7">23,492</td>
        <td headers="r54 c3 c8">27,827</td>
        <td headers="r54 c3 c9">31,471</td>
        <td headers="r54 c3 c10">35,743</td>
        <td headers="r54 c3 c11">39,688</td>
        <td headers="r54 c3 c12">47,297</td>
        <td headers="r54 c4">229.6</td>
      </tr>
      <tr>
        <th class="stub0" id="r55" headers="c1">2013</th>
        <td headers="r55 c2 c13">11,892</td>
        <td headers="r55 c2 c14">12,119</td>
        <td headers="r55 c2 c15">11,173</td>
        <td headers="r55 c3 c5 c16">15,156</td>
        <td headers="r55 c3 c5 c17">15,676</td>
        <td headers="r55 c3 c5 c18">14,095</td>
        <td headers="r55 c3 c6">18,552</td>
        <td headers="r55 c3 c7">23,836</td>
        <td headers="r55 c3 c8">28,235</td>
        <td headers="r55 c3 c9">31,932</td>
        <td headers="r55 c3 c10">36,267</td>
        <td headers="r55 c3 c11">40,269</td>
        <td headers="r55 c3 c12">47,990</td>
        <td headers="r55 c4">233.0</td>
      </tr>
      <tr>
        <th class="stub0" id="r56" headers="c1">2014</th>
        <td headers="r56 c2 c13">12,071</td>
        <td headers="r56 c2 c14">12,316</td>
        <td headers="r56 c2 c15">11,354</td>
        <td headers="r56 c3 c5 c16">15,379</td>
        <td headers="r56 c3 c5 c17">15,934</td>
        <td headers="r56 c3 c5 c18">14,326</td>
        <td headers="r56 c3 c6">18,850</td>
        <td headers="r56 c3 c7">24,230</td>
        <td headers="r56 c3 c8">28,695</td>
        <td headers="r56 c3 c9">32,473</td>
        <td headers="r56 c3 c10">36,927</td>
        <td headers="r56 c3 c11">40,968</td>
        <td headers="r56 c3 c12">49,021</td>
        <td headers="r56 c4">236.7</td>
      </tr>
    </tbody>
    <tfoot>
      <tr>
        <td class="firstNote" colspan="15">SOURCE: Census Bureau, Current Population Survey.</td>
      </tr>
      <tr>
        <td class="note" colspan="15">NOTES: <abbr class="spell">CPI</abbr>&nbsp;= Consumer Price Index; .&nbsp;.&nbsp;.&nbsp;= not applicable.
          <div class="newNote">Poverty thresholds in&nbsp;dollars for larger families before 1981 are:
            <table class="hundredpercent">
              <colgroup span="1" style="width:8em"></colgroup>
              <colgroup span="1" style="width:8em"></colgroup>
              <thead>
                <tr>
                  <th class="stubHeading" scope="col">Year</th>
                  <th scope="col">7&nbsp;persons<br>or more</th>
                </tr>
              </thead>
              <tbody>
                <tr>
                  <th class="stub0" scope="row">1959</th>
                  <td>4,849</td>
                </tr>
                <tr>
                  <th class="stub0" scope="row">1960</th>
                  <td>4,921</td>
                </tr>
                <tr>
                  <th class="stub0" scope="row">1961</th>
                  <td>4,967</td>
                </tr>
                <tr>
                  <th class="stub0" scope="row">1962</th>
                  <td>5,032</td>
                </tr>
                <tr>
                  <th class="stub0" scope="row">1963</th>
                  <td>5,092</td>
                </tr>
                <tr>
                  <th class="stub0" scope="row">1964</th>
                  <td>5,156</td>
                </tr>
                <tr class="topPad1">
                  <th class="stub0" scope="row">1965</th>
                  <td>5,248</td>
                </tr>
                <tr>
                  <th class="stub0" scope="row">1966</th>
                  <td>5,395</td>
                </tr>
                <tr>
                  <th class="stub0" scope="row">1967</th>
                  <td>5,550</td>
                </tr>
                <tr>
                  <th class="stub0" scope="row">1968</th>
                  <td>5,789</td>
                </tr>
                <tr>
                  <th class="stub0" scope="row">1969</th>
                  <td>6,101</td>
                </tr>
                <tr class="topPad1">
                  <th class="stub0" scope="row">1970</th>
                  <td>6,468</td>
                </tr>
                <tr>
                  <th class="stub0" scope="row">1971</th>
                  <td>6,751</td>
                </tr>
                <tr>
                  <th class="stub0" scope="row">1972</th>
                  <td>6,983</td>
                </tr>
                <tr>
                  <th class="stub0" scope="row">1973</th>
                  <td>7,435</td>
                </tr>
                <tr>
                  <th class="stub0" scope="row">1974</th>
                  <td>8,253</td>
                </tr>
                <tr class="topPad1">
                  <th class="stub0" scope="row">1975</th>
                  <td>9,022</td>
                </tr>
                <tr>
                  <th class="stub0" scope="row">1976</th>
                  <td>9,588</td>
                </tr>
                <tr>
                  <th class="stub0" scope="row">1977</th>
                  <td>10,216</td>
                </tr>
                <tr>
                  <th class="stub0" scope="row">1978</th>
                  <td>11,002</td>
                </tr>
                <tr>
                  <th class="stub0" scope="row">1979</th>
                  <td>12,280</td>
                </tr>
                <tr>
                  <th class="stub0" scope="row">1980</th>
                  <td>13,955</td>
                </tr>
              </tbody>
              <tfoot>
                <tr>
                  <td class="noNotes" colspan="2">&nbsp;</td>
                </tr>
              </tfoot>
            </table>
          </div>
          <div class="newNote">Beginning with poverty reports for 1981, the Census Bureau discontinued making distinctions based on householder's sex or between farm and nonfarm families of the same size and composition. Additionally, larger Current Population Survey samples permitted separate poverty income criteria for families of 7, 8, and 9 or more persons. The Social Security Administration developed separate criteria for these larger families by number of children for the base year 1978 on the same basis as the original poverty matrix for smaller families, and has since updated the criteria by means of the <span class="nobr">all-items</span> <abbr class="spell">CPI</abbr>. For additional details, see Census Bureau's <i>Current Population Reports</i> Series&nbsp;<span class="nobr">P-60,</span> <abbr title="Number">No.</abbr>&nbsp;133, &quot;Characteristics of the Population Below the Poverty Level, 1980.&quot;</div>
        </td>
      </tr>
      <tr>
        <td class="note" colspan="15">a. Beginning in January&nbsp;1978, the Bureau of Labor Statistics introduced a new price index for all urban consumers <span class="nobr">(<abbr class="spell">CPI</abbr>-U)</span> for all items <span class="nobr">(1982&ndash;1984</span>&nbsp;= 100) that forms a continuous series with the earlier index for urban wage earners and for clerical workers as of December&nbsp;1977.</td>
      </tr>
      <tr>
        <td class="lastNote" colspan="15">CONTACT: Bernadette&nbsp;D. Proctor <span class="nobr">(301) 763-3213</span> or <a href="mailto:statistics@ssa.gov">statistics@ssa.gov</a>.</td>
      </tr>
    </tfoot>
  </table>
</div>


Pandas does not handle this well



