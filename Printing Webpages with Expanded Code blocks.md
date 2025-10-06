https://addons.mozilla.org/en-US/firefox/addon/print-edit-we/
- Edit a webpage prior to printing
# Edit CSS before print
Find relevant elements in DOM and ensure their height and width properties are set to show everything
## tl;dr devtools scripts

Use appropriate selector and copy into devtools console
```js
selector = '.s-code-block'

Array.from(document.querySelectorAll(selector)).forEach( (el) => {
el.style.height = 'max-content';
el.style.width = 'max-content';
el.style.maxHeight = 'max-content';
el.style.maxHidth = 'max-content';
});
```

### Selectors

stackoverflow.com : "s-code-block"
medium.com: ".nl.nm.nn.no.np.nq.nr.ns.bq.nt.bc.bl"

1. Select a code block using picker/Right Click > inspect
2. Find selector for all code blocks
	- prepend with `.` if it's classname is a non-standard html element
	- Validate the sector selects all code blocks 
	```js
    var selector = "class1 class2 class3"
	selector = "." + selector.replaceAll(" ", ".")
	document.querySelectorAll(selector)	
	NodeList(7)
	```

3. Copy into selector variable above
4. Visually validate page before printing WITHOUT REFRESHING
## Explanation
Derived from : https://superuser.com/questions/1282281/how-can-i-print-this-webpage-with-the-whole-code-snippets-and-without-missing-im

1. Edit CSS using Dev Tools
2. Use Ctrl-P to Print current DOM WITHOUT refreshing the page

Relevant CSS Styles
- height and width
	- "max-content" should be sufficient
	- "fit-content" as a fall-back
	- "100%" for width if really needed
- max-height and max-width
	- Uncheck/Disable/Delete

Use Item selector or Right Click > Inspect on code blocks and disable/remove
- <font color="#00ff00">Code Block DOM/Source</font>
- <font color="#EE82EE">Filter on CSS element here</font>
- <font color="#d6672a">Max Height CSS</font> (Uncheck or delete)
![[firefox_print_remove_maxheight_css.png]]

## JS debugger snipper for Stack Overflow

```js
Array.from(document.querySelectorAll('.s-code-block')).forEach( (el) => {
el.style.height = 'max-content';
el.style.width = 'max-content';
el.style.maxHeight = 'max-content';
el.style.maxHidth = 'max-content';
});
```


Good example 
https://stackoverflow.com/questions/35821184/implement-an-interactive-shell-over-ssh-in-python-using-paramiko the OG question is an example of height cutoff
- and answer https://stackoverflow.com/a/75308211 example of width cutoff

```js
>> document.querySelectorAll('.s-code-block');
NodeList(4) [ pre.default.s-code-block, pre.default.s-code-block, pre.default.s-code-block, pre.default.s-code-block
 ]
​
0: <pre class="default s-code-block">​
1: <pre class="default s-code-block">​
2: <pre class="default s-code-block">​
3: <pre class="default s-code-block">
​
length: 4
​
<prototype>: NodeListPrototype { item: item(), keys: keys(), values: values(), … }
>> document.querySelectorAll('.s-code-block')[0].style.maxHeight='max-content';
"max-content"
>> document.querySelectorAll('.s-code-block')[3].style.width='max-content';
"max-content" ​
```



```html
<pre class="default s-code-block"><code data-highlighted="yes" class="hljs language-python"><span class="hljs-keyword">import</span> paramiko
<span class="hljs-keyword">import</span> re


<span class="hljs-keyword">class</span> <span class="hljs-title class_">ShellHandler</span>:

    <span class="hljs-keyword">def</span> <span class="hljs-title function_">__init__</span>(<span class="hljs-params">self, host, user, psw</span>):
        self.ssh = paramiko.SSHClient()
        self.ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
        self.ssh.connect(host, username=user, password=psw, port=<span class="hljs-number">22</span>)

        channel = self.ssh.invoke_shell()
        self.stdin = channel.makefile(<span class="hljs-string">'wb'</span>)
        self.stdout = channel.makefile(<span class="hljs-string">'r'</span>)

    <span class="hljs-keyword">def</span> <span class="hljs-title function_">__del__</span>(<span class="hljs-params">self</span>):
        self.ssh.close()

    <span class="hljs-keyword">def</span> <span class="hljs-title function_">execute</span>(<span class="hljs-params">self, cmd</span>):
        <span class="hljs-string">"""

        :param cmd: the command to be executed on the remote computer
        :examples:  execute('ls')
                    execute('finger')
                    execute('cd folder_name')
        """</span>
        cmd = cmd.strip(<span class="hljs-string">'\n'</span>)
        self.stdin.write(cmd + <span class="hljs-string">'\n'</span>)
        finish = <span class="hljs-string">'end of stdOUT buffer. finished with exit status'</span>
        echo_cmd = <span class="hljs-string">'echo {} $?'</span>.<span class="hljs-built_in">format</span>(finish)
        self.stdin.write(echo_cmd + <span class="hljs-string">'\n'</span>)
        shin = self.stdin
        self.stdin.flush()

        shout = []
        sherr = []
        exit_status = <span class="hljs-number">0</span>
        <span class="hljs-keyword">for</span> line <span class="hljs-keyword">in</span> self.stdout:
            <span class="hljs-keyword">if</span> <span class="hljs-built_in">str</span>(line).startswith(cmd) <span class="hljs-keyword">or</span> <span class="hljs-built_in">str</span>(line).startswith(echo_cmd):
                <span class="hljs-comment"># up for now filled with shell junk from stdin</span>
                shout = []
            <span class="hljs-keyword">elif</span> <span class="hljs-built_in">str</span>(line).startswith(finish):
                <span class="hljs-comment"># our finish command ends with the exit status</span>
                exit_status = <span class="hljs-built_in">int</span>(<span class="hljs-built_in">str</span>(line).rsplit(maxsplit=<span class="hljs-number">1</span>)[<span class="hljs-number">1</span>])
                <span class="hljs-keyword">if</span> exit_status:
                    <span class="hljs-comment"># stderr is combined with stdout.</span>
                    <span class="hljs-comment"># thus, swap sherr with shout in a case of failure.</span>
                    sherr = shout
                    shout = []
                <span class="hljs-keyword">break</span>
            <span class="hljs-keyword">else</span>:
                <span class="hljs-comment"># get rid of 'coloring and formatting' special characters</span>
                shout.append(re.<span class="hljs-built_in">compile</span>(<span class="hljs-string">r'(\x9B|\x1B\[)[0-?]*[ -/]*[@-~]'</span>).sub(<span class="hljs-string">''</span>, line).
                             replace(<span class="hljs-string">'\b'</span>, <span class="hljs-string">''</span>).replace(<span class="hljs-string">'\r'</span>, <span class="hljs-string">''</span>))

        <span class="hljs-comment"># first and last lines of shout/sherr contain a prompt</span>
        <span class="hljs-keyword">if</span> shout <span class="hljs-keyword">and</span> echo_cmd <span class="hljs-keyword">in</span> shout[-<span class="hljs-number">1</span>]:
            shout.pop()
        <span class="hljs-keyword">if</span> shout <span class="hljs-keyword">and</span> cmd <span class="hljs-keyword">in</span> shout[<span class="hljs-number">0</span>]:
            shout.pop(<span class="hljs-number">0</span>)
        <span class="hljs-keyword">if</span> sherr <span class="hljs-keyword">and</span> echo_cmd <span class="hljs-keyword">in</span> sherr[-<span class="hljs-number">1</span>]:
            sherr.pop()
        <span class="hljs-keyword">if</span> sherr <span class="hljs-keyword">and</span> cmd <span class="hljs-keyword">in</span> sherr[<span class="hljs-number">0</span>]:
            sherr.pop(<span class="hljs-number">0</span>)

        <span class="hljs-keyword">return</span> shin, shout, sherr
</code></pre>
```
