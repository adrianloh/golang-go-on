---


---

<h1 id="vex">VEX</h1>
<p><img src="https://i.imgur.com/H2zGC6K.jpg" alt="enter image description here"></p>
<p>To access attributes of inputs (zero-indexed):</p>
<pre class=" language-c"><code class="prism  language-c">  @Cd <span class="token operator">=</span> @opinput1_Cd<span class="token punctuation">;</span> <span class="token comment">// Set @Cd of box1 to @Cd of input 1</span>
</code></pre>
<p>In a parameter box, you would use (remember the backticks):</p>
<pre class=" language-c"><code class="prism  language-c">`<span class="token function">opinputpath</span><span class="token punctuation">(</span><span class="token string">".."</span><span class="token punctuation">,</span><span class="token number">1</span><span class="token punctuation">)</span>`
</code></pre>
<p>To get a channel/parameter from any node:</p>
<pre class=" language-c"><code class="prism  language-c">  <span class="token keyword">float</span> scale <span class="token operator">=</span> <span class="token function">chf</span><span class="token punctuation">(</span>“<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token operator">/</span>box1<span class="token operator">/</span>scale”<span class="token punctuation">)</span> <span class="token comment">// like using `ch` appended with a type</span>
</code></pre>
<p>To access other types, you have <code>chi</code> (int) <code>chs</code> (string) <code>cfv</code> (vector) etc.</p>
<p>Inside a VOP node, an <code>attribute</code> is dubiously called a <em>bound variable</em>. Here, we create an attribute <code>@fucker</code> using the <code>bind</code> operator.</p>
<p><img src="https://i.imgur.com/O2tWJ66.jpg" alt="enter image description here"></p>
<p>A few variables are available for referencing (as you can see inside the VOP):<br>
- <code>@ptnum</code>  – current point number up to <code>@numpt</code><br>
- <code>@elemnum</code> – current index of this element up to <code>@numelem</code></p>
<p>Their equivalents are <code>@primnum</code>, <code>@numprim</code> etc.</p>
<h2 id="types">Types</h2>

<table>
<thead>
<tr>
<th align="left">Type</th>
<th>Syntax</th>
</tr>
</thead>
<tbody>
<tr>
<td align="left">float</td>
<td><code>f@name</code></td>
</tr>
<tr>
<td align="left">vector2</td>
<td><code>u@name</code></td>
</tr>
<tr>
<td align="left">vector</td>
<td><code>v@name</code></td>
</tr>
<tr>
<td align="left">vector4</td>
<td><code>p@name</code></td>
</tr>
<tr>
<td align="left">int</td>
<td><code>i@name</code></td>
</tr>
</tbody>
</table><p>Declaring just a plain vector:</p>
<pre class=" language-c"><code class="prism  language-c">vector scale <span class="token operator">=</span> <span class="token punctuation">{</span><span class="token number">1</span><span class="token punctuation">,</span><span class="token number">2</span><span class="token punctuation">,</span><span class="token number">3</span><span class="token punctuation">}</span><span class="token punctuation">;</span>
</code></pre>
<p>Declaring an <code>attribute</code> vector:</p>
<pre class=" language-c"><code class="prism  language-c">v@scale <span class="token operator">=</span> <span class="token punctuation">{</span><span class="token number">1</span><span class="token punctuation">,</span><span class="token number">2</span><span class="token punctuation">,</span><span class="token number">3</span><span class="token punctuation">}</span><span class="token punctuation">;</span>
</code></pre>
<p>VEX implicitly casts to <code>float</code></p>
<pre class=" language-c"><code class="prism  language-c">@pscale <span class="token operator">=</span> <span class="token number">1</span><span class="token punctuation">;</span> <span class="token comment">// No type declaration, `1.0`</span>
@pscale <span class="token operator">=</span> <span class="token punctuation">{</span><span class="token number">1.0</span><span class="token punctuation">,</span> <span class="token number">1.0</span><span class="token punctuation">,</span> <span class="token number">1.0</span><span class="token punctuation">}</span><span class="token punctuation">;</span> <span class="token comment">// This is still just `1.0` since we didn't declare its type</span>
</code></pre>
<p>To access one element of a vector:</p>
<pre class=" language-c"><code class="prism  language-c">vector fuckyou <span class="token operator">=</span> <span class="token punctuation">{</span><span class="token number">1</span><span class="token punctuation">,</span> <span class="token number">2</span><span class="token punctuation">,</span> <span class="token number">3</span><span class="token punctuation">}</span><span class="token punctuation">;</span>
f@suckadick <span class="token operator">=</span> fuckyou<span class="token punctuation">.</span>y<span class="token punctuation">;</span>
</code></pre>
<p>You can use <code>x y z w</code> or <code>r g b a</code> or <code>x y</code> etc. depending on the vector’s dimensions. Houdini just assumes 3-element vectors are 	<code>xyz</code> or <code>rgb</code> – it’s all the same.</p>
<blockquote>
<p><strong>GOTCHA</strong><br>
You cannot use variables in literal declarations (due to casting), instead use <a href="https://www.sidefx.com/docs/houdini/vex/functions/set.html">set</a>:</p>
<pre class=" language-c"><code class="prism  language-c">v@someshit <span class="token operator">=</span> <span class="token punctuation">{</span>@P<span class="token punctuation">.</span>x<span class="token punctuation">,</span> @P<span class="token punctuation">.</span>y<span class="token punctuation">,</span> @P<span class="token punctuation">.</span>z<span class="token punctuation">}</span><span class="token punctuation">;</span> <span class="token comment">// this is invalid</span>
v@someshit <span class="token operator">=</span> <span class="token function">set</span><span class="token punctuation">(</span>@P<span class="token punctuation">.</span>x<span class="token punctuation">,</span> @P<span class="token punctuation">.</span>y<span class="token punctuation">,</span> @P<span class="token punctuation">.</span>z<span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token comment">// using `set`</span>
</code></pre>
</blockquote>

