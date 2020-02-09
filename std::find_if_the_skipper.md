<!DOCTYPE html>
<html>

<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>std::find_if_the_skipper</title>
  <link rel="stylesheet" href="https://stackedit.io/style.css" />
</head>

<body class="stackedit">
  <div class="stackedit__html"><h1 id="stdfind_if-as-a-skipper">std::find_if as a skipper</h1>
<p>One of the algorithms/functions that I find myself using a lot is std::find_if.  It is just a linear search in a range. It can be used however in other applications. One possible implementation of this function is as follows:</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">template</span> <span class="token operator">&lt;</span><span class="token keyword">typename</span> Iterator<span class="token punctuation">,</span> <span class="token keyword">typename</span> Op<span class="token operator">&gt;</span> 
Iterator <span class="token function">find_if</span><span class="token punctuation">(</span>Iterator first<span class="token punctuation">,</span> Iterator last<span class="token punctuation">,</span> Op<span class="token operator">&amp;&amp;</span> op<span class="token punctuation">)</span> 
<span class="token punctuation">{</span>
	<span class="token keyword">for</span> <span class="token punctuation">(</span><span class="token punctuation">;</span> <span class="token punctuation">(</span>first <span class="token operator">!=</span> last<span class="token punctuation">)</span> <span class="token operator">&amp;&amp;</span> <span class="token operator">!</span><span class="token function">op</span><span class="token punctuation">(</span><span class="token operator">*</span>first<span class="token punctuation">)</span><span class="token punctuation">;</span> first<span class="token operator">++</span><span class="token punctuation">)</span><span class="token punctuation">;</span> 
	<span class="token keyword">return</span> first<span class="token punctuation">;</span>
<span class="token punctuation">}</span> 
</code></pre>
<p>The magic is in what this function returns; an iterator. Not a boolean, not a number, an iterator!  This function is just a loop that returns the iterator of the first element v in the range such that op(v) to true. If nothing is found (and in this case, <em>first</em> will equal to <em>last</em>), then last is returned.</p>
<p>The user will need to test the returned iterator against <em>last</em> to check if it exists in our range.  Returning a boolean, instead of an iterator, is just an extra work that this function designer decided to leave to the caller.</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token comment">// the classical application of std::find_if is something like this. </span>
<span class="token keyword">auto</span> it <span class="token operator">=</span> std<span class="token operator">::</span><span class="token function">find_if</span><span class="token punctuation">(</span>std<span class="token operator">::</span><span class="token function">cbegin</span><span class="token punctuation">(</span>v<span class="token punctuation">)</span><span class="token punctuation">,</span> std<span class="token operator">::</span><span class="token function">cend</span><span class="token punctuation">(</span>v<span class="token punctuation">)</span><span class="token punctuation">,</span> 
			<span class="token punctuation">[</span><span class="token punctuation">]</span> <span class="token punctuation">(</span><span class="token keyword">auto</span> e<span class="token punctuation">)</span> <span class="token punctuation">{</span> <span class="token keyword">return</span> e <span class="token operator">%</span> <span class="token number">2</span><span class="token punctuation">;</span> <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token comment">// This extra check may sound boring, but it just add some power to this std::find_if function (as we will see next..). </span>
<span class="token keyword">if</span> <span class="token punctuation">(</span>it <span class="token operator">!=</span> std<span class="token operator">::</span><span class="token function">cend</span><span class="token punctuation">(</span>v<span class="token punctuation">)</span><span class="token punctuation">)</span> 
<span class="token punctuation">{</span> 
	std<span class="token operator">::</span>cout <span class="token operator">&lt;&lt;</span> <span class="token string">"There is an odd number there\n"</span><span class="token punctuation">;</span> 
<span class="token punctuation">}</span>  
</code></pre>
<p>The std::find_if is just one simple for-loop that we often see when we just want to skip some elements in a given range.  For example, if we want to find the sum of all positive integers in a sorted std::vector, we can do something like this.</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">int</span> sum <span class="token operator">=</span> <span class="token number">0</span><span class="token punctuation">;</span> 
<span class="token keyword">for</span> <span class="token punctuation">(</span><span class="token keyword">auto</span> f <span class="token operator">=</span> std<span class="token operator">::</span><span class="token function">cbegin</span><span class="token punctuation">(</span>d<span class="token punctuation">)</span><span class="token punctuation">;</span> f <span class="token operator">!=</span> std<span class="token operator">::</span><span class="token function">cend</span><span class="token punctuation">(</span>d<span class="token punctuation">)</span><span class="token punctuation">;</span> f<span class="token operator">++</span><span class="token punctuation">)</span> 
<span class="token punctuation">{</span>
    <span class="token keyword">if</span> <span class="token punctuation">(</span><span class="token operator">*</span>f <span class="token operator">&lt;</span> <span class="token number">0</span><span class="token punctuation">)</span> <span class="token punctuation">{</span> <span class="token keyword">continue</span><span class="token punctuation">;</span> <span class="token punctuation">}</span> 
    sum <span class="token operator">+</span><span class="token operator">=</span> <span class="token operator">*</span>f<span class="token punctuation">;</span>
<span class="token punctuation">}</span> 
</code></pre>
<p>… or something that looks like this.</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token comment">// All this means is: skip all negative numbers.</span>
<span class="token keyword">auto</span> f <span class="token operator">=</span> std<span class="token operator">::</span><span class="token function">find_if</span><span class="token punctuation">(</span>std<span class="token operator">::</span><span class="token function">cbegin</span><span class="token punctuation">(</span>v<span class="token punctuation">)</span><span class="token punctuation">,</span> std<span class="token operator">::</span><span class="token function">cend</span><span class="token punctuation">(</span>v<span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token punctuation">[</span><span class="token punctuation">]</span><span class="token punctuation">(</span><span class="token keyword">const</span> <span class="token keyword">auto</span><span class="token operator">&amp;</span> v<span class="token punctuation">)</span> <span class="token punctuation">{</span> <span class="token keyword">return</span> <span class="token operator">!</span><span class="token punctuation">(</span>v <span class="token operator">&lt;=</span> <span class="token number">0</span><span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span> 
<span class="token keyword">auto</span> sum <span class="token operator">=</span> std<span class="token operator">::</span><span class="token function">accumulate</span><span class="token punctuation">(</span>f<span class="token punctuation">,</span> std<span class="token operator">::</span><span class="token function">cend</span><span class="token punctuation">(</span>v<span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token number">0</span><span class="token punctuation">)</span><span class="token punctuation">;</span> 
</code></pre>
<p>We will work on some more fun problems next.</p>
<p><strong>The first problem:</strong> we have a vector of numbers.  Let’s assume it is sorted.  We want to find the minimum positive number that does not exist in this vector.</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">int</span> <span class="token function">min_positive_not_found_number</span><span class="token punctuation">(</span>std<span class="token operator">::</span>vector<span class="token operator">&lt;</span><span class="token keyword">int</span><span class="token operator">&gt;</span> <span class="token keyword">const</span><span class="token operator">&amp;</span> vec<span class="token punctuation">)</span>
<span class="token punctuation">{</span>
	<span class="token comment">// The first step is to skip any negative number. </span>
	<span class="token keyword">auto</span> f <span class="token operator">=</span> std<span class="token operator">::</span><span class="token function">find_if</span><span class="token punctuation">(</span>std<span class="token operator">::</span><span class="token function">cbegin</span><span class="token punctuation">(</span>vec<span class="token punctuation">)</span><span class="token punctuation">,</span> std<span class="token operator">::</span><span class="token function">cend</span><span class="token punctuation">(</span>vec<span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token punctuation">[</span><span class="token punctuation">]</span><span class="token punctuation">(</span><span class="token keyword">const</span> <span class="token keyword">auto</span><span class="token operator">&amp;</span> v<span class="token punctuation">)</span> <span class="token punctuation">{</span> <span class="token keyword">return</span> v <span class="token operator">&gt;</span> <span class="token number">0</span><span class="token punctuation">;</span> <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span> 
	<span class="token keyword">int</span> sol<span class="token punctuation">{</span><span class="token number">1</span><span class="token punctuation">}</span><span class="token punctuation">;</span> 
	<span class="token keyword">for</span> <span class="token punctuation">(</span> <span class="token punctuation">;</span> fw <span class="token operator">!=</span> std<span class="token operator">::</span><span class="token function">cend</span><span class="token punctuation">(</span>vec<span class="token punctuation">)</span><span class="token punctuation">;</span> f<span class="token operator">++</span><span class="token punctuation">)</span>
	<span class="token punctuation">{</span>
		<span class="token keyword">if</span> <span class="token punctuation">(</span><span class="token operator">*</span>f <span class="token operator">&lt;=</span> sol<span class="token punctuation">)</span> <span class="token punctuation">{</span> sol <span class="token operator">=</span> <span class="token operator">*</span>f <span class="token operator">+</span> <span class="token number">1</span><span class="token punctuation">;</span> <span class="token punctuation">}</span>
		<span class="token comment">// Note 1: Because the vector is sorted, we don't want to get beyond this point. 	 </span>
	<span class="token punctuation">}</span> 
	<span class="token keyword">return</span> sol<span class="token punctuation">;</span> 
<span class="token punctuation">}</span> 
</code></pre>
<p>Because of Note 1, we can rewrite this code as follow:</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">int</span> <span class="token function">min_positive_non_found_number</span><span class="token punctuation">(</span>std<span class="token operator">::</span>vector<span class="token operator">&lt;</span><span class="token keyword">int</span><span class="token operator">&gt;</span> <span class="token keyword">const</span><span class="token operator">&amp;</span> vec<span class="token punctuation">)</span>
<span class="token punctuation">{</span>
	<span class="token comment">// the first step is to skip any negative number. </span>
	<span class="token keyword">auto</span> f <span class="token operator">=</span> std<span class="token operator">::</span><span class="token function">find_if</span><span class="token punctuation">(</span>std<span class="token operator">::</span><span class="token function">cbegin</span><span class="token punctuation">(</span>vec<span class="token punctuation">)</span><span class="token punctuation">,</span> std<span class="token operator">::</span><span class="token function">cend</span><span class="token punctuation">(</span>vec<span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token punctuation">[</span><span class="token punctuation">]</span><span class="token punctuation">(</span><span class="token keyword">const</span> <span class="token keyword">auto</span><span class="token operator">&amp;</span> v<span class="token punctuation">)</span> <span class="token punctuation">{</span> <span class="token keyword">return</span> v <span class="token operator">&gt;</span> <span class="token number">0</span><span class="token punctuation">;</span> <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span> 
	<span class="token keyword">int</span> sol<span class="token punctuation">{</span><span class="token number">1</span><span class="token punctuation">}</span><span class="token punctuation">;</span> 
	<span class="token keyword">for</span> <span class="token punctuation">(</span> <span class="token punctuation">;</span> f <span class="token operator">!=</span> std<span class="token operator">::</span><span class="token function">cend</span><span class="token punctuation">(</span>vec<span class="token punctuation">)</span> <span class="token operator">&amp;&amp;</span> <span class="token punctuation">(</span><span class="token operator">*</span>f <span class="token operator">&lt;=</span> sol<span class="token punctuation">)</span><span class="token punctuation">;</span> f<span class="token operator">++</span><span class="token punctuation">)</span>
	<span class="token punctuation">{</span>
		sol <span class="token operator">=</span> <span class="token operator">*</span>f <span class="token operator">+</span> <span class="token number">1</span><span class="token punctuation">;</span> 
		<span class="token comment">// You can also put this statement in the loop, and turn it into a one-liner.	 </span>
		<span class="token comment">// Not a big deal, and totally unrelated to the topic of this blog :). </span>
	<span class="token punctuation">}</span> 
	<span class="token keyword">return</span> sol<span class="token punctuation">;</span> 
<span class="token punctuation">}</span> 
</code></pre>
<p><strong>Problem 2:</strong> I found std::find_if useful when dealing with strings. Let’s assume we want to write a simple csv parser. Let’s assume for the sake of clarity that each line in your file has only numbers that are separated by a comma. We want to convert each token in the CSV line to a number. In other words, we want to write a string <em>tokenizer</em>.</p>
<pre class=" language-cpp"><code class="prism  language-cpp"><span class="token keyword">template</span> <span class="token operator">&lt;</span><span class="token keyword">typename</span> Iter<span class="token punctuation">,</span> <span class="token keyword">typename</span> D<span class="token punctuation">,</span> <span class="token keyword">typename</span> P<span class="token operator">&gt;</span> 
<span class="token keyword">void</span> <span class="token function">tokenize</span><span class="token punctuation">(</span>Iter first<span class="token punctuation">,</span> Iter last<span class="token punctuation">,</span> D d<span class="token punctuation">,</span> P process<span class="token punctuation">)</span> 
<span class="token punctuation">{</span> 
  <span class="token keyword">for</span> <span class="token punctuation">(</span> <span class="token punctuation">;</span> <span class="token punctuation">;</span> first<span class="token operator">++</span><span class="token punctuation">)</span>  
  <span class="token punctuation">{</span>
    <span class="token comment">// std::find is just a special case of std::find_if. This is equivalent to.</span>
    <span class="token comment">// std::find_if(first, last, [d](const auto v) { return v == d; }); </span>
    <span class="token comment">// std::find here means iterate until d is found.  </span>
    <span class="token keyword">auto</span> nf <span class="token operator">=</span> std<span class="token operator">::</span><span class="token function">find</span><span class="token punctuation">(</span>first<span class="token punctuation">,</span> last<span class="token punctuation">,</span> d<span class="token punctuation">)</span><span class="token punctuation">;</span> 
    <span class="token function">process</span><span class="token punctuation">(</span>first<span class="token punctuation">,</span> nf<span class="token punctuation">)</span><span class="token punctuation">;</span> 
    <span class="token comment">// This is the "skip" statement (i.e., let first jumps directly to nf). </span>
    first <span class="token operator">=</span> nf<span class="token punctuation">;</span>  
    <span class="token keyword">if</span> <span class="token punctuation">(</span>first <span class="token operator">==</span> last<span class="token punctuation">)</span> <span class="token punctuation">{</span> <span class="token keyword">return</span><span class="token punctuation">;</span> <span class="token punctuation">}</span> 
  <span class="token punctuation">}</span>
<span class="token punctuation">}</span> 
</code></pre>
<p>We call the function <em>tokenize</em> with something similar to this:</p>
<pre class=" language-cpp"><code class="prism  language-cpp">  std<span class="token operator">::</span>string s<span class="token punctuation">{</span><span class="token string">"12,234,234"</span><span class="token punctuation">}</span><span class="token punctuation">;</span> 
  <span class="token keyword">auto</span> range_printer <span class="token operator">=</span> <span class="token punctuation">[</span><span class="token punctuation">]</span><span class="token punctuation">(</span><span class="token keyword">auto</span> f<span class="token punctuation">,</span> <span class="token keyword">auto</span> l<span class="token punctuation">)</span> 
  <span class="token punctuation">{</span>
     <span class="token keyword">for</span> <span class="token punctuation">(</span> <span class="token punctuation">;</span> f <span class="token operator">!=</span> l<span class="token punctuation">;</span> f<span class="token operator">++</span><span class="token punctuation">)</span> 
     <span class="token punctuation">{</span> std<span class="token operator">::</span>cout <span class="token operator">&lt;&lt;</span> <span class="token operator">*</span>f<span class="token punctuation">;</span> <span class="token punctuation">}</span>
     std<span class="token operator">::</span>cout <span class="token operator">&lt;&lt;</span> <span class="token string">'\n'</span><span class="token punctuation">;</span> 
  <span class="token punctuation">}</span><span class="token punctuation">;</span> 
  <span class="token function">tokenize</span><span class="token punctuation">(</span>std<span class="token operator">::</span><span class="token function">begin</span><span class="token punctuation">(</span>s<span class="token punctuation">)</span><span class="token punctuation">,</span> std<span class="token operator">::</span><span class="token function">end</span><span class="token punctuation">(</span>s<span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token string">','</span><span class="token punctuation">,</span> range_printer<span class="token punctuation">)</span><span class="token punctuation">;</span>  
</code></pre>
<p>and the output will be:</p>
<pre><code>12
234
234
</code></pre>
<p>Of course, we can generalize the <em>process</em> function to any other function we want (e.g., convert the string to integers).</p>
<p><strong>Problem 3:</strong>. Let’s assume we have a pattern matcher. If the pattern is “abc”, we expect to match the exact pattern (i.e., anything other than “abc” is false).  A pattern can have the symbol ‘+’, which indicates any character can be matched.  We can make it a little bit more fun and we say that abc*z will match any string that replaces the * char with any other string.  Here is the code of <em>match</em>.</p>
<pre class=" language-cpp"><code class="prism  language-cpp">
<span class="token keyword">bool</span> <span class="token function">match</span><span class="token punctuation">(</span>std<span class="token operator">::</span>string <span class="token keyword">const</span><span class="token operator">&amp;</span> pattern<span class="token punctuation">,</span> std<span class="token operator">::</span>string <span class="token keyword">const</span><span class="token operator">&amp;</span> word<span class="token punctuation">)</span> 
<span class="token punctuation">{</span>
  <span class="token keyword">auto</span> pf <span class="token operator">=</span> std<span class="token operator">::</span><span class="token function">cbegin</span><span class="token punctuation">(</span>pattern<span class="token punctuation">)</span><span class="token punctuation">;</span> 
  <span class="token keyword">auto</span> pe <span class="token operator">=</span> std<span class="token operator">::</span><span class="token function">cend</span><span class="token punctuation">(</span>pattern<span class="token punctuation">)</span><span class="token punctuation">;</span> 
  
  <span class="token keyword">auto</span> wf <span class="token operator">=</span> std<span class="token operator">::</span><span class="token function">cbegin</span><span class="token punctuation">(</span>word<span class="token punctuation">)</span><span class="token punctuation">;</span> 
  <span class="token keyword">auto</span> we <span class="token operator">=</span> std<span class="token operator">::</span><span class="token function">cend</span><span class="token punctuation">(</span>word<span class="token punctuation">)</span><span class="token punctuation">;</span> 
  
  <span class="token keyword">for</span> <span class="token punctuation">(</span> <span class="token punctuation">;</span> <span class="token punctuation">(</span>pf <span class="token operator">!=</span> pe<span class="token punctuation">)</span> <span class="token operator">&amp;&amp;</span> <span class="token punctuation">(</span>wf <span class="token operator">!=</span> we<span class="token punctuation">)</span> <span class="token punctuation">;</span>  <span class="token punctuation">)</span> 
  <span class="token punctuation">{</span>
    <span class="token keyword">if</span> <span class="token punctuation">(</span><span class="token operator">*</span>pf <span class="token operator">==</span> <span class="token operator">*</span>wf <span class="token operator">||</span> <span class="token operator">*</span>pf <span class="token operator">==</span> <span class="token string">'+'</span><span class="token punctuation">)</span> <span class="token punctuation">{</span> pf <span class="token operator">++</span><span class="token punctuation">;</span> wf <span class="token operator">++</span><span class="token punctuation">;</span> <span class="token punctuation">}</span> 
    <span class="token keyword">else</span> <span class="token keyword">if</span> <span class="token punctuation">(</span><span class="token operator">*</span>pf <span class="token operator">==</span> <span class="token string">'*'</span><span class="token punctuation">)</span>
    <span class="token punctuation">{</span>
        <span class="token comment">// We should look for what comes after '*' in the pattern. </span>
        <span class="token comment">// special case: that can be pe (i.e., the end). We cant de-reference </span>
        <span class="token comment">// that. In this case, anything in the word match, so just quit by setting wf equal to we. </span>
        wf <span class="token operator">=</span>  <span class="token punctuation">(</span><span class="token operator">++</span>pf <span class="token operator">==</span> pe<span class="token punctuation">)</span> <span class="token operator">?</span> we <span class="token operator">:</span> std<span class="token operator">::</span><span class="token function">find</span><span class="token punctuation">(</span>wf<span class="token punctuation">,</span> we<span class="token punctuation">,</span> <span class="token operator">*</span>pf<span class="token punctuation">)</span><span class="token punctuation">;</span> 
    <span class="token punctuation">}</span>
    <span class="token keyword">else</span> <span class="token punctuation">{</span> <span class="token keyword">return</span> <span class="token boolean">false</span><span class="token punctuation">;</span> <span class="token punctuation">}</span> 
  <span class="token punctuation">}</span> 
  
  <span class="token comment">// special case. "ab*" against "ab". </span>
  <span class="token comment">// Here, we will quit the loop because wf == we.</span>
  <span class="token keyword">if</span> <span class="token punctuation">(</span> pf <span class="token operator">!=</span> pe <span class="token operator">&amp;&amp;</span> <span class="token operator">*</span>pf <span class="token operator">==</span> <span class="token string">'*'</span><span class="token punctuation">)</span> <span class="token punctuation">{</span> <span class="token operator">++</span> pf<span class="token punctuation">;</span> <span class="token punctuation">}</span>
  
  <span class="token keyword">return</span> <span class="token punctuation">(</span>pf <span class="token operator">==</span> pe<span class="token punctuation">)</span> <span class="token operator">&amp;&amp;</span> <span class="token punctuation">(</span>wf <span class="token operator">==</span> we<span class="token punctuation">)</span><span class="token punctuation">;</span> 
<span class="token punctuation">}</span> 
</code></pre>
<p>This std::find_if (and its sister std::find) are just one of the coolest algorithms there.  Most of this power comes from returning an iterator. Although it seems like it is giving the caller more checks to worry about, it gave the caller more power. It is a genius idea from a genius algorithm designer. Alex Stepanov mastered this way of thinking when designed the STL library. His books, articles, and lectures have many similar and more powerful examples. This falls under what he termed as <em>Generic Programming</em>. I put him at the same level as Knuth. I advise every developer to read more about Alex’s programming approach.  It is just genius!</p>
</div>
</body>

</html>
