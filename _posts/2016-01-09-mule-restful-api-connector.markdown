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

*This post is unfinished ... Check back often, as it's a work in progress.*