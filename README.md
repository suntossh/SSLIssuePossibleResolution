SSLIssuePossibleResolution
==========================

Wiki for SSL Issues

http://stackoverflow.com/questions/3566240/websphere-7-ssl-error-that-never-goes-away-no-matter-what-i-do

Ok I install Websphere 7.0 and RAD 7.5. Update WAS to fix pack 11 and update RAD. 7.5.5. lastest updates..etc...

I create a server profile.

I start the server.

I turn on global security and use LDAP. (something I have done a billion times)

I don't even attempt to publish an application.

The server constantly debugs out this message every two minutes.

How do you make it stop? I have tried making new keys doesn't work, I blow away the profile and make a new one. Nothing works. Nothing. The server is running at 400 mb without an application installed. Is this supposed to be normal? 400mb with no app published?

The server profile creation wizard forces this SSL nonsense into the config.

What's really going on here?

I would love to utilize the latest server technology IBM has to offer but it seems to be broken right out of the box, out of the gate. 5 fix packs later and its still broken.

[8/25/10 8:12:44:896 CDT] 0000000b SSLHandshakeE E   SSLC0008E: Unable to initialize SSL connection.  Unauthorized access was denied or security settings have expired.  Exception is javax.net.ssl.SSLException: Unrecognized SSL message, plaintext connection?
 at com.ibm.jsse2.b.a(b.java:34)
 at com.ibm.jsse2.pc.a(pc.java:155)
 at com.ibm.jsse2.pc.unwrap(pc.java:104)
 at javax.net.ssl.SSLEngine.unwrap(SSLEngine.java:17)
 at com.ibm.ws.ssl.channel.impl.SSLConnectionLink.readyInbound(SSLConnectionLink.java:531)
 at com.ibm.ws.ssl.channel.impl.SSLConnectionLink.ready(SSLConnectionLink.java:291)
 at com.ibm.ws.tcp.channel.impl.NewConnectionInitialReadCallback.sendToDiscriminators(NewConnectionInitialReadCallback.java:214)
 at com.ibm.ws.tcp.channel.impl.NewConnectionInitialReadCallback.complete(NewConnectionInitialReadCallback.java:113)
 at com.ibm.ws.tcp.channel.impl.AioReadCompletionListener.futureCompleted(AioReadCompletionListener.java:165)
 at com.ibm.io.async.AbstractAsyncFuture.invokeCallback(AbstractAsyncFuture.java:217)
 at com.ibm.io.async.AsyncChannelFuture.fireCompletionActions(AsyncChannelFuture.java:161)
 at com.ibm.io.async.AsyncFuture.completed(AsyncFuture.java:138)
 at com.ibm.io.async.ResultHandler.complete(ResultHandler.java:204)
 at com.ibm.io.async.ResultHandler.runEventProcessingLoop(ResultHandler.java:775)
 at com.ibm.io.async.ResultHandler$2.run(ResultHandler.java:905)
 at com.ibm.ws.util.ThreadPool$Worker.run(ThreadPool.java:1550)
Could someone please create a Websphere-7.0 tag

websphere
share|improve this question
edited Aug 25 '10 at 14:19

asked Aug 25 '10 at 13:23

branchgabriel
2,45121840
add a comment
7 Answers
activeoldestvotes
up vote
5
down vote
I solve this issue by enabling security in the server screen. Open the Servers view, double click on the server, expand security, enable "Security is enabled in this sever" and provide user id and password. After this the problem went away. For some reason it was disabled even htough I enabled it through the admin console.

share|improve this answer
answered Jan 20 '12 at 16:52

Programador
5112
add a comment

up vote
4
down vote
accepted
I was wrong. Creating it from either way causes the issue. (running the pmt.bat or through the rad tool).

The real issue was not copying the global security stuff as a security domain.

Basically you go to Security > Security Domains > then click the Copy from Global Security option.

This is just crazy. Why not simply have the goofy wizard ask if you would like this to happen also??? IBM infuriates me.

share|improve this answer
edited Aug 26 '10 at 18:17

answered Aug 25 '10 at 19:26

branchgabriel
2,45121840
  	 	
That is strange, I usually create my profiles from rad and do not have this problem. Clicking on "Run Profile Management Tool" in rad simply runs <rad sdp folder>/runtimes/base_v7/bin/ProfileManagement/bin/pmt.exe. like pmt.bat does. Perhaps you should think about opening a pmr. –  svachon Aug 25 '10 at 20:33
  	 	
Moving to a newer version of the container software or a container that isn't so memory heavy I cry on every build is the best solution. Opening PMRs are about as fun as driving razor blades under my fingernails. –  branchgabriel Feb 7 '12 at 21:46 
2	 	
+1 on the "IBM infuriates me" part. And thx for the answer, btw. –  jambriz Sep 28 '12 at 20:49
  	 	
This might be "fixed" in Fix Pack 25 - www-01.ibm.com/support/docview.wss?uid=swg1PM61843 –  NickTee Oct 19 '12 at 4:44
add a comment
up vote
0
down vote
Your app server is trying to establish a ssl connection on a port that is not ssl. An easy way to see it live is trying to access the admin console using http but using the ssl port. If you use the standard ports you can try this: http://localhost:9043/ibm/console/

share|improve this answer
answered Aug 25 '10 at 18:38

svachon
2,7081814
  	 	
Thanks for trying but it really is just an issue caused by poor programming on the part of IBM. Occam's Razor actually applies in this situation. IBM FAIL I am sure they will fix this in some fix pack that won't be released for the next 3 years. –  branchgabriel Aug 25 '10 at 19:31
add a comment
up vote
0
down vote
have you resently update your WAS this might be a cause. I resolved it by creating a new profile after updating WAS

share|improve this answer
answered Jan 30 '13 at 13:44

Thakhani Tharage
82111
add a comment
up vote
0
down vote
I found that this solution worked best for me.

http://wiing.fr/websphere-application-server-ssl-error/ "The way to fix it is to connect to the administration console, navigate to : Security >SSL certificate and key management > Key stores and certificates > NodeDefaultKeyStore > Personal certificates Select the default alias and click on renew. Restart WAS. I recently got that error because the certificate’s beginning date was set to a date in the future, could not understand what happened to my configuration…"

share|improve this answer
answered May 7 '13 at 16:14

Revoman
7613
add a comment
up vote
0
down vote
This error may be caused by your IDE (let it be Rational Application Developer RAD, Rational Software Architect RSA or plain Eclipse), which is trying to update the server status in the "Servers View".

As somebody here already said, the IDE's call to WebSphere Application Server's console fails, because it's malformed:

Unrecognized SSL message, plaintext connection?

Since your IDE tries to update the status regularly, the server prints this error message as often.

What worked in my case, was to remove the server from the "Servers View" (Right click - delete) and add a new one (Right click - new).

share|improve this answer
answered Aug 27 '13 at 12:43

Peter Wippermann
968522
add a comment
up vote
0
down vote
Its too late but may be it helps others like me :)

Agree with Peter above, its IDE which checks status from server..

You need to add the certificate 'X' i.e. exportedCertificate.cer to JRE keystore...run this command.. (Win cmd prompt)>keytool -import -exportedCertificate.cer -storepass changeit -keystore %JAVA_HOME%/jre/lib/security/cacerts -alias myAliase

Certificate 'X' is the default certificate in your Websphere server. You can find and export it through IBM console. Alternative is to hit HTTPS url at browser and export it from browser in DER format.

share|improve this answer
answered May 3 at 8:12

Mahesh Manuja
1
add a comment

