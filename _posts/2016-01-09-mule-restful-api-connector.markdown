---
layout: post
title:  "A Mule RESTful API Client Connector"
date:   2016-01-09 21:06:19 -0500
categories: jekyll update
---
Introduction
============
I've recently been learning about JHipster and now I'm learning about the Mule integration platform as well. My JHipster application
is called "goof" and the reason I'm using it is because it's not an application that I will use for anything other than learning.
The beauty of JHipster is the ability to generate a Spring-boot/AngularJS application quickly and also to generate everything you
would need to perform CRUD operations on a persisted entity. I choose have a table of developer names. The connector would call the
JHipster API e.g. /api/developers and the returned listing of developers (JSON array) would be listed in the browser window using a
Mule HTTP connector in the flow design.

 ![mules]({{ site.baseurl }}/images/mule-3.jpg)The connector project was created in AnyPoint Studio (Mule's Eclipse Environment) using 
the AnyPoint Connector Devkit that had to be downloaded and installed in AnyPoint. Devkit can be downloaded here 
https://docs.mulesoft.com/anypoint-connector-devkit/v/3.8/ . I used version 3.8. This may have changed since this writing. 
I obtained this software via the Eclipse update process using the IDE. Once I agreed to
all the EULA's that MuleSoft provided, the software installed and I was ready to begin my new connector.

Since, I've already created a working connector, I'm going to create a new connector project and document everything as I implement the
connector and test it. This way I'll be sure to note anything difficult or confusing as we go. Let's open AnyPoint Studio and create a
new project. Select File/New/AnyPoint Connector Project or if the choice doesn't exist, select File/New/Other... and choose the Mule
wizard and then select the AnyPoint Connector Project. Press the Next> button to proceed. 

The dialog that appears provides two choices for the type of connector project. You will want to select SDK Based and then the Next> button 
to proceed. 

The dialog that is now presented collects metadata for the project. This is where you will name the connector. My connector name is CIWiseREST. 
You do not have to add the text "connector". The project generator will do this for you. Please notice that the project name and namespace 
text boxes will fill automatically as you name the connector itself. Be sure to select the "Generate sample operations and configurable fields" 
checkbox. We will use a very simple operation to prove that the connector will install and can be used in a Mule flow on a running server instance.
The API Type is Java SDK and the Authentication drop down should be set to "None". We will use authentication but we'll implement that
code ourselves. Nothing else should be checked. Select Next>.

The dialog presented contains Maven and Github configuration information. We will unselect the Maven default checkbox and add information
for the group-id as "org.ciwise.modules". Select Next>.

This last dialog provides for customization of the connector icon and also a text layer over the icon. I changed the label to read "REST".
That's it. We're done and can now select Finish. The code for the project will now be generated.

Two Java classes are generated:
- org.ciwise.modules.ciwiserest.CIWiseRESTConnector.java
- org.ciwise.modules.ciwiserest.config.ConnectorConfig.java

The connector class is called the @Connector class my the MuleSoft folks because this class must be annotated with @Connector. This is not
optional. Mule has a code you don't see that allows the connector to be used in a drag-and-drop window and integrate with other connectors
that make up a Mule flow. Here's a code listing of this class:

**Listing 1 - CIWiseRESTConnector.java**

{% highlight java %}
package org.ciwise.modules.ciwiserest;

import org.mule.api.annotations.Config;
import org.mule.api.annotations.Connector;
import org.mule.api.annotations.Processor;
import org.ciwise.modules.ciwiserest.config.ConnectorConfig;

@Connector(name="ci-wise-rest", friendlyName="CIWiseREST")
public class CIWiseRESTConnector {

    @Config
    ConnectorConfig config;

    /**
     * Custom processor
     *
     * @param friend Name to be used to generate a greeting message.
     * @return A greeting message
     */
    @Processor
    public String greet(String friend) {
        /*
         * MESSAGE PROCESSOR CODE GOES HERE
         */
        return config.getGreeting() + " " + friend + ". " + config.getReply();
    }

    public ConnectorConfig getConfig() {
        return config;
    }

    public void setConfig(ConnectorConfig config) {
        this.config = config;
    }

}
{% endhighlight %}

Let's build the connector and install it into AnyPoint Studio so we can test it with Mule. The connector project when created added the greet() method to the @Connector
class because we checked the box to add an example operation. This operation is shown by the greet() method and annotated with the @Processor notation. When the Mule
connector is selected in a Mule Project flow, the operations that have been annotated and defined, are selectable in the IDE to be processed during communications across
the Mule flow in production. Select the project ci-wise-rest-connector in the Package explorer and right-mouse. Next select AnyPoint Connector at the bottom and then 
Build Connector. An Apache Maven build will commence. The project should build successfully.

To install the connector for testing, follow the same steps and when the final pop-up menu is shown, select Install or Update. The IDE will install and accept the connector
for use when designing a Mule flow. A dialog should be shown when the installation is complete providing confirmation that the install was successful.

To test the connector using Mule, I created a Mule project called muletest. Open the Mule test project and drag an HTTP connector into the Mule Flow view. Set the properties
of the connector to use localhost and port 8081. Next, find CIWiseREST in the Mule palette and drag your new connector into the flow beside te HTTP connector. Select your
connector in the flow view and double-click. The connector properties window will be shown. Under Basic Settings/Connector Configuration, select the add or plus icon. 
Select "CIWiseREST__Configuration". Now select the Operation drop-down and choose "Greet". The greet method required an input string called friend and under General/Friend:
you need to give the operation this String. Enter "John". When we run the Mule application and give the browser the URL http://localhost:8081 we should expect to see 
"Hello John. How are you?". That's it. It doesn't do much, however it demonstrates the @Connector and @Process annotations and how they are used with a Mule Flow. 

Click in the Mule flow view and the little red X in the upper right of your connector should go away as there are no errors now. Press the Save All icon in the IDE. We are
now ready to test our connector. Click on the Mule flow XML file in the Package Explorer and right-mouse. Choose Run-As/Mule Application. When Mule says it's deployed and
successful, open a browser and enter http://localhost:8081. I see "How are you? John. Hello". I set my configuration backwards. The return statement in the connector
operation method looks like this.

**Listing 2 - Snippet from CIWiseRESTConnector.java**
{% highlight java %}
    @Processor
    public String greet(String friend) {
        /*
         * MESSAGE PROCESSOR CODE GOES HERE
         */
        return config.getGreeting() + " " + friend + ". " + config.getReply();
    }

{% endhighlight %}

And, here's the source for the Configuration.

**Listing 3 - ConnectorConfig.java**
{% highlight java %}
package org.ciwise.modules.ciwiserest.config;

import org.mule.api.annotations.components.Configuration;

@Configuration(friendlyName = "Configuration")
public class ConnectorConfig {

	private final String reply = "Hello";
	private final String greeting = "How are you?";

	public String getReply() {
		return reply;
	}
	public String getGreeting() {
		return greeting;
	}
	
}
{% endhighlight %}

The greeting should be "Hello" and the reply should be "How are you?". Delete the connector from the Mule flow view. Swap the Strings, rebuild the connector, 
and then try to update the installation. I found that for some reason the Maven clean operation would not work. I'm still trying to figure this out, but you 
can uninstall the connector, delete the target directory in a terminal or file explorer and then build the connector and install it successfully. Do this and 
re-test the connector using Mule.





