# Repo to reproduce a bug in discovering tlds inside jars using liberty and eclipse

## Tools versions

The features versions are:

Web Development Tools 21.0.900.v20210916_2134 com.ibm.wdt.webtools.top.feature.feature.group
IBMWebSphere® Application Server Liberty Tools 21.0.900.v20210916_2134 com.ibm.websphere.wdt.server.tools.main.feature.group
IBMWebSphere® Application Server V8.5 Tools 21.0.900.v20210916_2134 com.ibm.websphere.wdt.st.v85.feature.group
IBMWebSphere® Application Server V9.0 Tools 21.0.900.v20210916_2134 com.ibm.websphere.wdt.st.v9.feature.group IBM

This is using the server version (as taken from the server log):
Launching defaultServer (WebSphere Application Server 22.0.0.1/wlp-1.0.60.cl220120220103-1900) on IBM J9 VM, version 8.0.7.0 - pwa6480sr7-20211025_01(SR7) (en_ES)

It was installed directly from eclipse using the ibm developer tools

And the eclipse version

Version: 2021-09 (4.21.0)Build id: 20210910-1417

## Symptomps

Adding the war app to the liberty server with the option `Run applications directly from the workspace`
enabled in the eclipse server config, generates this xml in `X:\path\to\liberty\servers\defaultServer\apps\test-tld-0.0.1-SNAPSHOT.war.xml:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<archive>
    <archive targetInArchive="/WEB-INF/lib/test-tld-lib-0.0.1.jar">
        <dir sourceOnDisk="X:\path\to\test-tlds\test-tld-lib\target\classes" targetInArchive="/"/>
        <dir sourceOnDisk="X:\path\to\test-tlds\test-tld-lib\target\test-classes" targetInArchive="/"/>
    </archive>
    <dir sourceOnDisk="X:\path\to\test-tlds\test-tld-war\src\main\webapp" targetInArchive="/"/>
    <dir sourceOnDisk="X:\path\to\test-tlds\test-tld-war\target\classes" targetInArchive="/WEB-INF/classes"/>
</archive>
```

with such configuration i've got this error when trying to open `http://localhost:9080/test-tld/test`:

```log
com.ibm.ws.jsp.translator.JspTranslationException: /jsp/test.jsp(0,1) --> JSPG0005E: tld file could not be found for uri[http://test-tlds.org/util] prefix [testUtil]
 at com.ibm.ws.jsp.translator.visitor.validator.ValidateVisitor.visitJspRootStart(ValidateVisitor.java:497)
 at com.ibm.ws.jsp.translator.visitor.JspVisitor.processJspElement(JspVisitor.java:237)
 at com.ibm.ws.jsp.translator.visitor.JspVisitor.visit(JspVisitor.java:220)
 at com.ibm.ws.jsp.translator.JspTranslator.processVisitors(JspTranslator.java:130)
 at com.ibm.ws.jsp.translator.utils.JspTranslatorUtil.translateJsp(JspTranslatorUtil.java:266)
 at com.ibm.ws.jsp.translator.utils.JspTranslatorUtil.translateJspAndCompile(JspTranslatorUtil.java:113)
 at com.ibm.ws.jsp.webcontainerext.AbstractJSPExtensionServletWrapper.translateJsp(AbstractJSPExtensionServletWrapper.java:546)
 at com.ibm.ws.jsp.webcontainerext.AbstractJSPExtensionServletWrapper._checkForTranslation(AbstractJSPExtensionServletWrapper.java:463)
 at com.ibm.ws.jsp.webcontainerext.AbstractJSPExtensionServletWrapper.checkForTranslation(AbstractJSPExtensionServletWrapper.java:253)
 at com.ibm.ws.jsp.webcontainerext.AbstractJSPExtensionServletWrapper.handleRequest(AbstractJSPExtensionServletWrapper.java:163)
 at com.ibm.ws.webcontainer.filter.WebAppFilterChain.invokeTarget(WebAppFilterChain.java:193)
 at com.ibm.ws.webcontainer.filter.WebAppFilterChain.doFilter(WebAppFilterChain.java:98)
 at com.ibm.ws.security.jaspi.JaspiServletFilter.doFilter(JaspiServletFilter.java:56)
 at com.ibm.ws.webcontainer.filter.FilterInstanceWrapper.doFilter(FilterInstanceWrapper.java:201)
 at com.ibm.ws.webcontainer.filter.WebAppFilterChain.doFilter(WebAppFilterChain.java:91)
 at com.ibm.ws.webcontainer.filter.WebAppFilterManager.doFilter(WebAppFilterManager.java:1002)
 at com.ibm.ws.webcontainer.filter.WebAppFilterManager.invokeFilters(WebAppFilterManager.java:1140)
 at com.ibm.ws.webcontainer.webapp.WebApp.handleRequest(WebApp.java:5049)
 at com.ibm.ws.webcontainer.osgi.DynamicVirtualHost$2.handleRequest(DynamicVirtualHost.java:316)
 at com.ibm.ws.webcontainer.WebContainer.handleRequest(WebContainer.java:1007)
 at com.ibm.ws.webcontainer.osgi.DynamicVirtualHost$2.run(DynamicVirtualHost.java:281)
 at com.ibm.ws.http.dispatcher.internal.channel.HttpDispatcherLink$TaskWrapper.run(HttpDispatcherLink.java:1184)
 at com.ibm.ws.http.dispatcher.internal.channel.HttpDispatcherLink.wrapHandlerAndExecute(HttpDispatcherLink.java:453)
 at com.ibm.ws.http.dispatcher.internal.channel.HttpDispatcherLink.ready(HttpDispatcherLink.java:412)
 at com.ibm.ws.http.channel.internal.inbound.HttpInboundLink.handleDiscrimination(HttpInboundLink.java:566)
 at com.ibm.ws.http.channel.internal.inbound.HttpInboundLink.handleNewRequest(HttpInboundLink.java:500)
 at com.ibm.ws.http.channel.internal.inbound.HttpInboundLink.processRequest(HttpInboundLink.java:360)
 at com.ibm.ws.http.channel.internal.inbound.HttpInboundLink.ready(HttpInboundLink.java:327)
 at com.ibm.ws.tcpchannel.internal.NewConnectionInitialReadCallback.sendToDiscriminators(NewConnectionInitialReadCallback.java:167)
 at com.ibm.ws.tcpchannel.internal.NewConnectionInitialReadCallback.complete(NewConnectionInitialReadCallback.java:75)
 at com.ibm.ws.tcpchannel.internal.WorkQueueManager.requestComplete(WorkQueueManager.java:504)
 at com.ibm.ws.tcpchannel.internal.WorkQueueManager.attemptIO(WorkQueueManager.java:574)
 at com.ibm.ws.tcpchannel.internal.WorkQueueManager.workerRun(WorkQueueManager.java:958)
 at com.ibm.ws.tcpchannel.internal.WorkQueueManager$Worker.run(WorkQueueManager.java:1047)
 at com.ibm.ws.threading.internal.ExecutorServiceImpl$RunnableWrapper.run(ExecutorServiceImpl.java:238)
 at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1160)
 at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:635)
 at java.lang.Thread.run(Thread.java:825)
```

I double checked the tld config is present in

However if i change the xml loose application xml config to:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<archive>
    <dir sourceOnDisk="X:\path\to\test-tlds\test-tld-war\src\main\webapp" targetInArchive="/"/>
    <dir sourceOnDisk="X:\path\to\test-tlds\test-tld-war\target\classes" targetInArchive="/WEB-INF/classes"/>
    <file sourceOnDisk="X:\path\to\test-tlds\test-tld-lib\target\test-tld-lib-0.0.1.jar" targetInArchive="/WEB-INF/lib/test-tld-lib-0.0.1.jar"/>
</archive>
```

the error is gone

## Diagnostic

It seems the first loose application configuration makes the server ignore the tld definitions present in the jar
