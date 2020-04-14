---


---

<h1 id="low-level-javascript-the-magic-of-asm.js">Low Level JavaScript: The Magic of asm.js</h1>
<h3 id="introduction">Introduction</h3>
<p>Low level means fast, and its hard to get faster than pure assembly. Asm.js, or Assembly JavaScript is a subset of normal JavaScript built to take advantage of certain aspects of JavaScript and low level techniques to squeeze every ounce of speed on the web.</p>
<h3 id="opening-sequence">Opening Sequence</h3>
<p>Asm.js is a strict subset of normal JavaScript with some interesting aspects. Unlike most JavaScript dialects, asm.js is a compilation target, not something meant to be coded by hand. Instead, programmers write C or C++ and then translate the semi-compiled source code into asm.js. In this article, we will explore two of the tricks asm employs, and see some of the true power of the web.</p>
<h3 id="the-trick">The Trick</h3>
<p>Asm takes advantage of many low level aspects of JavaScript to leverage speed.</p>
<h4 id="types">Types</h4>
<p>Before asm.js became JavaScript, it was a language like C or C++.<br>
If you have ever taken an intro to JavaScript course, you will know that all numbers are of type <code>number</code>.</p>
<pre class=" language-javascript"><code class="prism  language-javascript"><span class="token comment">//would be an int in C/C++</span>
console<span class="token punctuation">.</span><span class="token function">log</span><span class="token punctuation">(</span><span class="token keyword">typeof</span><span class="token punctuation">(</span><span class="token number">3</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
<span class="token comment">//would be a float in C/C++</span>
console<span class="token punctuation">.</span><span class="token function">log</span><span class="token punctuation">(</span><span class="token keyword">typeof</span><span class="token punctuation">(</span><span class="token number">3.14</span><span class="token punctuation">)</span><span class="token punctuation">)</span>
</code></pre>
<p>The <code>number</code> type in JavaScript is an 8 byte IEEE-754 double. Does this mean that our C/C++ code that uses ints get translated into an 8 byte double? Not exactly. JavaScript can actually handle numbers of different sizes and types.</p>
<pre class=" language-javascript"><code class="prism  language-javascript"><span class="token comment">//here is what the number type normally is</span>
console<span class="token punctuation">.</span><span class="token function">log</span><span class="token punctuation">(</span>Float64Array<span class="token punctuation">.</span>BYTES_PER_ELEMENT<span class="token punctuation">)</span>
</code></pre>
<pre class=" language-javascript"><code class="prism  language-javascript"><span class="token comment">//a traditional C/C++ int</span>
console<span class="token punctuation">.</span><span class="token function">log</span><span class="token punctuation">(</span>Int8Array<span class="token punctuation">.</span>BYTES_PER_ELEMENT<span class="token punctuation">)</span>
</code></pre>
<p>Running both of these in the browser’s console gives us 8 byes for the first, and 1 byte for the second. Armed with this knowledge, we can see how asm.js can translate from the strongly typed variables in C/C++ to weakly typed JavaScript.</p>
<p>Finally, we have type annotations.</p>
<p>If you look at compiled asm.js code, you should expect to see the bit-wise or 0 a lot. For example,</p>
<pre class=" language-javascript"><code class="prism  language-javascript"><span class="token keyword">var</span> x <span class="token operator">=</span> <span class="token number">3</span> <span class="token operator">|</span> <span class="token number">0</span>
</code></pre>
<p>That bit-wise or does something interesting to our value. In this case, it acts as an implicit cast from the floating point <code>number</code> to an int <code>number</code>. Try this in your console:</p>
<pre class=" language-javascript"><code class="prism  language-javascript"><span class="token keyword">var</span> x <span class="token operator">=</span> <span class="token number">3.5</span> <span class="token operator">|</span> <span class="token number">0</span>
console<span class="token punctuation">.</span><span class="token function">log</span><span class="token punctuation">(</span>x<span class="token punctuation">)</span> <span class="token comment">// 3</span>
</code></pre>
<p>This has a special consequence when doing math with numbers.</p>
<h4 id="math">Math</h4>
<p>Many things change when we have stronger typing. For the sake of time, we will only talk about division.<br>
Try this code in a console or this <a href="https://repl.it/@NadivGold/asm1">replit</a>:</p>
<pre class=" language-javascript"><code class="prism  language-javascript"><span class="token comment">// x and x2 should be the same number</span>
<span class="token comment">// divided by the same floating point number</span>
<span class="token keyword">var</span> x <span class="token operator">=</span>  <span class="token operator">-</span><span class="token number">39591737333333</span>  <span class="token operator">|</span>  <span class="token number">0</span> 
<span class="token keyword">var</span> d <span class="token operator">=</span> x <span class="token operator">/</span>  <span class="token number">3.34541</span>
console<span class="token punctuation">.</span><span class="token function">log</span><span class="token punctuation">(</span>d<span class="token punctuation">)</span>
<span class="token keyword">var</span> x2 <span class="token operator">=</span>  <span class="token operator">-</span><span class="token number">39591737333333</span>
<span class="token keyword">var</span> d2 <span class="token operator">=</span> x2 <span class="token operator">/</span>  <span class="token number">3.34541</span>
console<span class="token punctuation">.</span><span class="token function">log</span><span class="token punctuation">(</span>d2<span class="token punctuation">)</span>
</code></pre>
<p>For reasons we won’t get into, floating point division is different from integer division. It is also important to note that float division is significantly faster than integer division.</p>
<h3 id="lightning-round">Lightning Round</h3>
<p>Asm.js does a lot more under the hood than we’ll go in depth here, but it’s still worth noting.</p>
<ul>
<li>Asm.js forgoes high level abstractions like JavaScript Objects</li>
<li>Asm.js does not create garbage, thus no need to spend time garbage collecting</li>
<li>Asm.js takes advantage of worker threads, opening up faster async code</li>
<li>Asm.js allows for use of some C/C++ libraries, such as OpenCV and Qhull</li>
</ul>
<h3 id="final-curtain">Final Curtain</h3>
<p>If you want to see asm.js in action, I recommend the <a href="https://kripken.github.io/misc-js-benchmarks/banana/index.html">bananabread demo</a> by Mozilla.</p>
<p>Asm.js is an important tool to unlocking the power of the web. It’s uses are endless, from media processing to gaming, to AI, to responsive web apps. Even if you don’t use it in your projects, the tricks that asm employs are something that every web dev should know.</p>

